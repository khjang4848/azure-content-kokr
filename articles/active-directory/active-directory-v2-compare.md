<properties
	pageTitle="Azure AD v2.0 끝점 | Microsoft Azure"
	description="원래의 Azure AD와 v2.0 끝점 간의 비교입니다."
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

# v2.0 끝점의 달라진 점

Azure Active Directory에 익숙하거나 이전에 Azure AD와 앱을 통합했다면 v2.0 끝점에서 예상하지 못한 몇 가지 차이를 느낄 수 있을 것입니다. 이 문서는 이러한 차이의 이해를 돕기 위해 작성되었습니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


## Microsoft 계정과 Azure AD 계정
v2.0 끝점은 개발자가 단일 인증 끝점을 사용하여 Microsoft 계정과 Azure AD 계정 모두로부터 로그인할 수 있는 앱을 작성할 수 있도록 합니다. 이 모델은 완전한 계정 중립적인 앱을 작성할 수 있도록 해주며, 사용자가 로그인할 때 사용하는 계정 형식을 무시할 수 있습니다. 물론, 특정 세션에서 사용 중인 계정 유형을 인식할 수 있도록 *만들 수 있지만*, 그럴 필요가 없습니다.

예를 들어 앱이 [Microsoft Graph](https://graph.microsoft.io)를 호출하는 경우 일부 추가 기능 및 데이터를 해당 SharePoint 사이트 또는 디렉터리 데이터와 같은 엔터프라이즈 사용자가 사용할 수 있습니다. 하지만 [사용자의 메일 읽기](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message)와 같은 작업 대부분에서 코드는 Microsoft 계정과 Azure AD 계정 두 곳에서 정확히 동일하게 쓰일 수 있습니다.

이제 간단한 하나의 프로세스를 통해 Microsoft 계정과 Azure AD 계정을 사용하여 앱을 통합할 수 있습니다. 끝점의 단일 집합, 단일 라이브러리 및 단일 앱 등록을 사용하여 소비자와 엔터프라이즈 세계에 액세스하는 권한을 얻을 수 있습니다. v2.0 끝점에 대해 알아보려면 [개요](active-directory-appmodel-v2-overview.md)를 확인하세요.


## 새 앱 등록 포털
v2.0 끝점은 새 위치 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 등록할 수 있습니다. 이 곳은 응용 프로그램 Id를 얻거나, 앱 로그인 페이지의 외형 사용자 할당 등을 할 수 있는 포털입니다. 포털에 액세스하려면 개인 계정이나 회사/학교 계정의 Microsoft 지원 계정만 있으면 됩니다.

이 앱 등록 포털에 점차 더 많은 기능이 추가될 것입니다. 이 포털의 목적은 무엇이든 관리할 수 있고 Microsoft 앱을 사용하여 모든 작업이 가능한 새로운 위치가 되는 것입니다.


## 모든 플랫폼에 대한 하나의 앱 ID
원래의 Azure Active Directory 서비스에서는 단일 프로젝트에 여러 가지 다른 앱을 등록했었습니다. 네이티브 클라이언트와 웹앱을 위해서 별도로 앱 등록을 해야만 했습니다.

![이전 응용 프로그램 등록 UI](../media/active-directory-v2-flows/old_app_registration.PNG)

예를 들어 웹 사이트와 iOS 앱 둘 다 구축하는 경우 서로 다른 응용 프로그램 ID를 사용하여 두 개를 별도로 등록해야 했습니다. 웹 사이트와 백 엔드 웹 API가 있는 경우, Azure AD의 개별 앱처럼 각각 따로 등록해야 했습니다. iOS 앱과 안드로이드 앱이 있는 경우도 마찬가지로, 다른 두 개의 앱에 등록해야 했습니다.

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

이제 모든 프로젝트에는 단일 앱 등록과 단일 응용 프로그램 ID만 필요합니다. 각 프로젝트에 여러 "플랫폼"이추가 가능하며 추가한 플랫폼에 적절한 데이터를 제공할 수 있습니다. 물론, 원하는 조건에 따라 많은 앱을 만들 수 있지만, 대부분의 경우 하나의 응용 프로그램 ID가 필요합니다.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

우리의 목적은 이 제품을 통해 앱 관리와 개발 환경을 더욱 간소화시키고, 작업 중인 단일 프로젝트의 보다 통합된 보기를 만드는 것입니다.


## 리소스가 아닌 범위
원래의 Azure AD 서비스에서 앱은 **리소스** 또는 토큰 수신자로 작동할 수 있습니다. 리소스는 많은 **범위** 또는 이해할 수 있는 **oAuth2Permissions**를 정의할 수 있으며, 클라이언트 앱이 범위의 특정 집합에 대한 리소스에 토큰을 요청하도록 허용합니다. Azure AD Graph API를 리소스의 예로 생각해 볼 경우:

- 리소스 식별자, 또는 `AppID URI`: `https://graph.windows.net/`
- 범위, 또는 `OAuth2Permissions`: `Directory.Read`, `Directory.Write` 등.

이 모든 것은 v2.0 끝점에서도 유효합니다. 앱은 여전히 리소스로 작동하고, 범위를 정의하고, URI로 식별될 수 있습니다. 클라이언트 앱은 여전히 해당 범위에 액세스 요청할 수 있습니다. 그러나 클라이언트가 권한을 요청하는 방식이 변경되었습니다. 이전에는, OAuth 2.0 인증을 Azure AD로 보냈습니다.

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

클라이언트 앱이 권한 부여를 요청할 때 어떤 리소스가 필요한지 **리소스** 매개 변수가 표시해 주는 곳입니다. Azure AD는 Azure 포털의 정적 구성을 기반 앱에 필요한 권한을 계산하고, 이에 따라 토큰을 발급합니다. 이제, 동일한 OAuth 2.0 인증 요청은 다음과 같습니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

앱이 권한 부여를 요청할 때 어떤 리소스와 권한이 필요한지 **범위** 매개 변수가 표시해주는 곳입니다. 원하는 리소스는 여전히 요청에서 나타납니다. - 범위 매개 변수의 각 값에 단순 포함되어 있습니다. 이러한 방식의 범위 매개 변수 사용은 v2.0 끝점이 OAuth 2.0 사양과 더욱 잘 호환되도록 해주고 일반적인 업계 실무와 더욱 긴밀하게 연합될 수 있도록 합니다. 이 기능은 앱이 다음 섹션에서 설명하는 [증분 동의](#incremental-and-dynamic-consent)를 수행할 수 있도록 해줍니다.

## 증분 및 동적 동의
일반 공급 Azure AD 서비스에 등록된 앱은 앱 생성 시 필요한 Azure 포털의 OAuth 2.0 권한을 지정하게 되어 있었습니다.

![권한 등록 UI](../media/active-directory-v2-flows/app_reg_permissions.PNG)

앱에 필요한 권한은 **정적으로** 구성되어 있습니다. 이 기능이 Azure 포털 내에 있도록 앱 구성을 허용하고 코드를 보기 좋고 간편하게 유지하는 동안, 개발자에게 몇 가지 문제가 발생합니다.

- 앱은 생성 시 항상 필요한 모든 사용권한에 대해 알아야 했습니다. 시간의 경과에 따라 권한을 추가하는 것은 어려운 작업이었습니다.
- 앱은 항상 미리 액세스하는 모든 리소스에 대해 알아야 했습니다. 리소스의 임의 개수에 액세스할 수 있는 앱을 만들기 어려웠습니다.
- 앱은 사용자의 첫 로그인 시 항상 필요한 모든 권한을 요청해야 했습니다. 일부 경우에는 권한 목록이 매우 길어서 최종 사용자가 앱에 처음 로그인 시 액세스 승인을 하지 않는 경우가 많았습니다.

v2.0 끝점을 사용하여 앱이 원하는 권한을 런타임, 즉 앱을 일반적으로 사용하는 동안 **동적으로** 지정할 수 있습니다. 이렇게 하려면, `scope` 매개 변수의 범위를 포함하는 권한을 요청하는 동안 앱이 요구하는 범위를 제 시간에 주어진 지점에 지정해야 합니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

위에서 요청하는 권한은 Azure AD 사용자의 디렉터리 데이터를 읽고, 해당 디렉터리에 기록하는 앱에 대한 권한입니다. 사용자가 이전에 이 특정 앱의 권한에 동의한 경우, 간단히 자격 증명만 입력하면 앱에 로그인됩니다. 사용자가 이 권한 중에 하나라도 동의하지 않으면, v2.0 끝점은 사용자에게 권한에 대한 동의를 요청할 것입니다. 자세한 내용은 [권한, 동의, 및 범위](active-directory-v2-scopes.md)를 참조하세요.

`scope` 매개 변수를 통해 동적으로 권한을 요청하도록 앱을 허용하는 것은 사용자의 환경의 완전한 통제를 제공합니다. 원한다면, 초기 단계에 동의 환경을 선택할 수 있고, 초기 권한 요청의 모든 권한을 요청할 수 있습니다. 또는 앱이 많은 권한을 요청하는 경우, 시간의 경과에 따라 앱의 특정 기능을 사용하도록 시도하는 것처럼 점진적으로 사용자로부터 권한을 수집하도록 선택할 수 있습니다.

## 잘 알려진 범위

#### 오프라인 액세스
v2.0 끝점에는 앱에 대해 잘 알려진 새 권한(`offline_access` 범위)을 사용해야 할 수 있습니다. 모든 앱이 연장된 기간 동안 사용자를 대신하여 리소스에 액세스해야 할 경우, 사용자가 앱을 자주 쓰지 않는 경우에도 이 권한을 요청해야 합니다. `offline_access` 범위는 “오프라인으로 데이터에 엑세스"라는 동의 대화 상자로 사용자에게 표시되며 반드시 동의해야 합니다. `offline_access` 권한 요청은 웹앱이 OAuth 2.0 refresh\_token을 v2.0 끝점에서 받을 수 있도록 해줍니다. Refresh\_token은 수명이 길며, 액세스의 기간이 확장된 새로운 OAuth 2.0 access\_token으로 바꿀 수 있습니다.

앱이 `offline_access` 범위를 요청하지 않으면 refresh\_token을 받을 수 없습니다. 즉, [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols.md#oauth2-authorization-code-flow)에서 authorization\_code를 교환하는 경우 `/token` 끝점에서 access\_token만 받게 됩니다. 해당 access\_token은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 끝점으로 다시 리디렉션하여 새 authorization\_code를 검색해야 합니다. 리디렉션 중에 앱 형식에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

OAuth 2.0, refresh\_token, 및 access\_token에 대한 자세한 내용은[v2.0 프로토콜 참조](active-directory-v2-protocols.md)를 확인하세요.

#### OpenID, 프로필 및 전자 메일

원래의 Azure Active Directory 서비스에서 가장 기본적인 OpenID Connect 로그인 흐름은 결과 id\_token의 사용자에 대해 다양한 정보를 제공합니다. id\_token의 클레임에는 사용자의 이름, 기본 설정된 사용자 이름, 전자 메일 주소, 개체 ID 등이 있습니다.

이제 `openid` 범위가 허용하는 앱의 액세스 정보를 제한할 것입니다. ‘openid’ 범위는 앱이 사용자를 로그인시키고 해당 사용자에 대한 앱별 식별자만 수신할 수 있게 합니다. 앱에 있는 사용자의 개인 식별 정보(PII)를 가져오려면 앱은 사용자로부터 추가 권한을 요청해야 합니다. 이를 수행할 수 있는 두 가지의 새로운 범위, 즉 `email` 및 `profile` 범위를 도입할 것입니다.

`email` 범위는 매우 간단합니다. 앱이 id\_token의 `email` 클레임을 통해 사용자의 기본 메일 주소에 액세스할 수 있도록 해줍니다. `profile` 범위는 앱이 사용자 이름, 기본 설정된 사용자 이름, 개체 ID 등 사용자에 관한 모든 기타 기본 정보에 액세스할 수 있도록 해줍니다.

이렇게 하면 최소한의 공개로 앱을 코딩할 수 있으며, 작업 수행에 필요한 정보만 사용자에게 요청할 수 있습니다. 이러한 범위에 대한 자세한 내용은 [v2.0 범위 참조](active-directory-v2-scopes.md)를 참조하세요.

## 토큰 클레임

V2.0 끝점에 의해 발급된 토큰의 클레임은 일반 공급 Azure AD 끝점에서 발급된 토큰과 동일하지 않습니다. 새로운 서비스로 마이그레이션하는 앱은 id\_token 또는 access\_token에 존재할 특정 클레임을 가정하면 안 됩니다. V2.0 끝점에서 발급한 토큰은 OAuth 2.0 및 OpenID Connect 사양을 준수하지만 일반 공급 Azure AD 서비스보다는 서로 다른 의미 체계를 따를 수 있습니다.

v2.0 토큰에서 내보내는 특정 클레임에 관한 더 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 확인하세요.

## 제한 사항
v2.0 끝점을 사용할 때 고려해야 할 몇 가지 제한 사항이 있습니다. 이 제한 사항 중 하나라도 특정 시나리오에 적용하는 경우 [v2.0 제한 사항 문서](active-directory-v2-limitations.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->