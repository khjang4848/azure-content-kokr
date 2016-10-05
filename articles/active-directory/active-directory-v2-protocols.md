<properties
	pageTitle="Azure AD v2.0 프로토콜 | Microsoft Azure"
	description="Azure AD v2.0 끝점에서 지원하는 프로토콜에 대한 가이드입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# v2.0 프로토콜 - OAuth 2.0 및 OpenID Connect

v2.0 끝점은 산업 표준 프로토콜 OpenID Connect 및 OAuth 2.0을 이용한 ID 서비스를 위해 Azure AD를 사용할 수 있습니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현 간에는 약간의 차이가 있을 수 있습니다. 오픈 소스 라이브러리 중 하나를 사용하는 대신 HTTP 요청을 직접 전송 및 처리하여 코드를 작성하거나 타사 오픈 소스 라이브러리를 사용하도록 선택한 경우 여기에 포함된 정보가 유용합니다.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## 기본 사항
거의 모든 OAuth 및 OpenID Connect 흐름에서 교환에 참여하는 다음 네 가지 요소가 있습니다.

![OAuth 2.0 역할](../media/active-directory-v2-flows/protocols_roles.png)

- **권한 부여 서버**는 v2.0 끝점입니다. 사용자 ID를 확인하고 리소스에 대한 액세스 권한을 부여 및 해지하고, 토큰을 발급합니다. ID 공급자라고도 하며 사용자 정보, 해당 액세스 및 흐름의 요소 간 트러스트 관계와 관련된 모든 사항을 안전하게 처리합니다.
- **리소스 소유자**는 일반적으로 최종 사용자입니다. 데이터를 소유하는 당사자이며 제3자가 해당 데이터 또는 리소스에 액세스하도록 허용할 권한이 있습니다.
- **OAuth 클라이언트**는 해당 응용 프로그램 ID로 식별되는 앱입니다. 일반적으로 최종 사용자가 상호 작용하는 요소이며 권한 부여 서버의 토큰을 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 권한을 클라이언트에 부여해야 합니다.
- **리소스 서버**는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여할 수 있도록 권한 부여 서버를 신뢰하고 전달자 access\_token을 사용하여 리소스에 대한 액세스 권한을 부여할 수 있도록 합니다.


## 앱 등록
v2.0 끝점을 사용하는 각 앱을 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에 등록해야 OAuth 또는 OpenID Connect를 사용하여 상호 작용할 수 있습니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

- 앱을 고유하게 식별하는 **응용 프로그램 ID**
- 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI** 또는 **패키지 식별자**
- 다른 몇 가지 시나리오 관련 값.

자세한 내용은 [앱 등록](active-directory-v2-app-registration.md) 방법을 참조하세요.

## 끝점
등록되면 앱이 v2.0 끝점에 요청을 보내기 위해 Azure AD와 통신합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

여기서 `{tenant}`은 네 개의 서로 다른 값 중 하나를 가질 수 있습니다.

| 값 | 설명 |
| ----------------------- | ------------------------------- |
| `common` | 개인 Microsoft 계정과 Azure Active Directory의 회사/학교 계정이 모두 있는 사용자가 응용 프로그램에 로그인할 수 있습니다. |
| `organizations` | Azure Active Directory의 회사/학교 계정이 있는 사용자만 응용 프로그램에 로그인할 수 있습니다. |
| `consumers` | 개인 Microsoft 계정(MSA)이 있는 사용자만이 용응용 프로그램에 로그인 할 수 있습니다. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 또는 `contoso.onmicrosoft.com` | 특정 Azure Active Directory 테넌트의 회사/학교 계정이 있는 사용자만 응용 프로그램에 로그인할 수 있습니다. Azure AD 테넌트의 친숙한 도메인 이름 또는 테넌트의 guid ID를 사용할 수 있습니다. |

이러한 끝점과 상호 작용하는 방법에 대한 자세한 내용을 보려면 아래의 특정 앱 종류를 선택합니다.

## 토큰
OAuth 2.0 및 OpenID Connect의 v2.0구현은 JWT로 표현되는 전달자 토큰을 포함하여 전달자 토큰을 광범위하게 활용합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하려면 먼저 Azure AD를 사용하여 인증해야 합니다. 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 악의적인 사용자가 메시지 가로채기(man-in-the-middle) 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

v2.0 끝점에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

## 프로토콜

일부 예제 요청을 확인할 준비가 되었다면 아래 자습서 중 하나를 시작합니다. 각각 특정 인증 시나리오에 해당합니다. 사용자에게 맞는 흐름을 결정하는 데 도움이 필요하면 [v2.0으로 빌드할 수 있는 앱 형식](active-directory-v2-flows.md)을 확인합니다.

- [OAuth 2.0를 사용하여 모바일 및 네이티브 응용 프로그램 빌드](active-directory-v2-protocols-oauth-code.md)
- [Open ID Connect를 사용하는 웹앱 빌드](active-directory-v2-protocols-oidc.md)
- [OAuth 2.0 암시적 흐름으로 단일 페이지 앱 구축](active-directory-v2-protocols-implicit.md)
- OAuth 2.0 클라이언트 자격 증명 흐름으로 데몬 또는 서버 쪽 프로세스 빌드(출시 예정)
- 흐름 대신 OAuth 2.0으로 웹 API에서 토큰 가져오기(출시 예정)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 

<!---HONumber=AcomDC_0921_2016-->