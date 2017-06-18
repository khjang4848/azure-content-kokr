<properties
	pageTitle="Azure AD v2.0 토큰 참조 | Microsoft Azure"
	description="v2.0 끝점에서 내보내는 토큰 및 클레임 형식"
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

# V2.0 토큰 참조

v2.0 끝점은 각 [인증 흐름](active-directory-v2-flows.md)의 처리 과정에서 여러 유형의 보안 토큰을 내보냅니다. 이 문서에서는 각 토큰 유형의 형식, 보안 특성 및 내용을 설명합니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## 토큰 형식

v2.0 끝점은 access\_token 및 refresh\_token 둘 다를 활용하는 [OAuth 2.0 인증 프로토콜](active-directory-v2-protocols.md)을 지원합니다. 또한 세 번째 토큰 유형인 id\_token을 도입하는 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 통해 인증 및 로그인을 지원합니다. 이러한 토큰은 각각 "전달자 토큰"으로 표시됩니다.

전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하려면 먼저 Azure AD를 사용하여 인증해야 합니다. 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 악의적인 사용자가 메시지 가로채기(man-in-the-middle) 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

v2.0 끝점에서 발급된 토큰은 대부분 Json 웹 토큰, 즉 JWT로 구현됩니다. JWT는 두 요소 간에 정보를 전송하는 URL로부터 안전한 간단한 수단입니다. JWT에 포함된 정보를 "클레임" 또는 토큰의 전달자 및 주체에 대한 정보 어설션이라고 합니다. JWT의 클레임은 전송을 위해 인코드 및 직렬화된 JSON 개체입니다. V2.0 끝점에 의해 발급된 JWT가 서명되었지만 암호화되지 않았으므로 디버깅을 위해 JWT의 내용을 쉽게 검사할 수 있습니다. JWT에 대한 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요.

## Id\_tokens

id\_token은 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 사용하여 인증을 수행할 때 앱이 받는 로그인 보안 토큰의 한 형태입니다. [JWT](#types-of-tokens)로 표시되며 사용자를 앱에 로그인하는 데 사용할 수 있는 클레임을 포함합니다. id\_token의 클레임을 적절하게 사용할 수 있습니다. 일반적으로 계정 정보를 표시하거나 앱에서 액세스 제어 결정을 내리는 데 사용됩니다. v2.0 끝점은 로그인한 사용자 유형에 관계없이 일관된 클레임 집합을 포함하는 하나의 id\_token 유형만 발급합니다. 즉, id\_token의 형식 및 콘텐츠는 개인 Microsoft 계정 사용자와 회사 또는 학교 계정에 대해 동일합니다.

지금은 Id\_token이 서명되었지만 암호화되지 않았습니다. 앱이 id\_token을 받으면 [서명의 유효성을 검사](#validating-tokens)하여 토큰의 신뢰성을 입증하고 토큰에 있는 몇 개 클레임의 유효성을 검사하여 유효성을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다르지만 앱이 모든 시나리오에서 수행해야 하는 몇 가지 [일반적인 클레임 유효성 검사](#validating-tokens)가 있습니다.

id\_token의 클레임에 대한 자세한 내용 및 샘플 id\_token은 아래에 나와 있습니다. id\_token의 클레임은 특정 순서로 반환되지 않습니다. 또한 언제든지 id\_token에 새 클레임이 도입될 수 있으므로 새 클레임이 도입될 때 앱이 손상되지 않아야 합니다. 아래 목록에는 이 문서가 작성될 때 앱이 안정적으로 해석할 수 있는 클레임이 포함되어 있습니다. 필요한 경우 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)에서 자세한 내용을 확인할 수 있습니다.

#### 샘플 id\_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] 연습을 위해 샘플 id\_token에 있는 클레임을 [calebb.net](https://calebb.net)에 붙여 넣어 검사하세요.

#### id\_token의 클레임
| 이름 | 클레임 | 예제 값 | 설명 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 대상 | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | 토큰의 의도한 수신자를 식별합니다. Id\_token에서 대상은 앱 등록 포털에서 앱에 할당된 앱의 응용 프로그램 ID입니다. 앱은 이 값의 유효성을 검사하고 일치하지 않을 경우 토큰을 거부해야 합니다. |
| 발급자 | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | 사용자가 인증된 Azure AD 테넌트뿐 아니라 토큰을 생성하고 반환하는 STS(보안 토큰 서비스)를 식별합니다. 앱은 발급자 클레임의 유효성을 검사하여 토큰이 v2.0 끝점에서 제공된 것인지 확인해야 합니다. 또한 클레임의 guid 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한해야 합니다. 사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 guid는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. |
| 발급 시간 | `iat` | `1452285331` | epoch 시간으로 표시된, 토큰이 발급된 시간입니다. |
| 만료 시간 | `exp` | `1452289231` | epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 이전이 아님 | `nbf` | `1452285331` | epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 발급 시간과 일반적으로 동일합니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 버전 | `ver` | `2.0` | Azure AD에서 정의된 id\_token 버전입니다. V2.0 끝점에 대한 값은 `2.0`입니다. |
| 테넌트 ID | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | 사용자가 속해 있는 Azure AD 테넌트를 나타내는 guid입니다. 회사 및 학교 계정의 경우 guid는 사용자가 속해 있는 조직의 변경 불가능 테넌트 ID입니다. 개인 계정의 경우 이 값은 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| 코드 해시 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 코드 해시는 OAuth 2.0 인증 코드와 함께 id\_token이 발급된 경우에만 id\_token에 포함됩니다. 인증 코드의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| 액세스 토큰 해시 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 액세스 토큰 해시는 OAuth 2.0 액세스 토큰과 함께 id\_token이 발급된 경우에만 id\_token에 포함됩니다. 액세스 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| nonce | `nonce` | `12345` | nonce는 토큰 재생 공격을 완화하기 위한 전략입니다. 앱은 `nonce` 쿼리 매개 변수를 사용하여 권한 부여 요청에 nonce를 지정할 수 있습니다. 요청에 제공한 값은 수정되지 않고 id\_token의 `nonce` 클레임에 내보내집니다. 따라서 앱이 지정된 id\_token과 앱 세션을 연결하는 요청에 지정된 값과 비교하여 값을 확인할 수 있습니다. 앱은 id\_token 유효성 검사 프로세스 중에 이 유효성 검사를 수행해야 합니다. |
| 이름 | `name` | `Babe Ruth` | 이름 클레임은 토큰의 주체를 식별하는, 사람이 읽을 수 있는 값을 제공합니다. 이 값은 반드시 고유한 것은 아니며 변경 가능하고 표시 용도로만 사용하도록 디자인되었습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| Email | `email` | `thegreatbambino@nyy.onmicrosoft.com` | 만약 있다면 사용자 계정과 연결된 기본 전자 메일 주소입니다. 해당 값은 변경 가능하며 지정된 사용자에 대해 점차 변경될 수 있습니다. `email` 범위는 이 클레임을 받기 위해 필요합니다. |
| 기본 설정된 사용자 이름 | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | v2.0 끝점에서 사용자를 나타내는 데 사용되는 기본 사용자 이름입니다. 메일 주소, 전화 번호 또는 지정된 형식이 없는 일반 사용자 이름일 수 있습니다. 해당 값은 변경 가능하며 지정된 사용자에 대해 점차 변경될 수 있습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| 제목  
 | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 따라서 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Azure AD 시스템에서 회사 또는 학교 계정의 개체 ID입니다. 이 클레임은 개인 Microsoft 계정에 대해 발급되지 않습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |


## 액세스 토큰

v2.0 끝점이 발급하는 액세스 토큰은 현재 Microsoft 서비스에서만 사용할 수 있습니다. 현재 지원되는 시나리오에 대해서는 앱이 액세스 토큰의 유효성 검사 또는 검사를 수행할 필요가 없습니다. 액세스 토큰을 완전 불투명으로 처리할 수 있습니다. 앱이 HTTP 요청을 통해 Microsoft에 전달할 수 있는 문자열일 뿐입니다.

조만간 v2.0 끝점에 다른 클라이언트의 액세스 토큰을 받을 수 있는 기능이 도입될 예정입니다. 그때 이 정보는 앱이 액세스 토큰 유효성 검사 및 기타 유사한 작업을 수행하는 데 필요한 정보로 업데이트될 것입니다.

v2.0 끝점의 액세스 토큰을 요청하는 경우 앱이 사용할 수 있도록 액세스 토큰에 대한 일부 메타데이터도 v2.0 끝점에서 반환됩니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효한 범위가 포함됩니다. 따라서 앱이 액세스 토큰 자체를 구문 분석하지 않아도 액세스 토큰의 지능형 캐싱을 수행할 수 있습니다.

## 새로 고침 토큰

새로 고침 토큰은 앱이 OAuth 2.0 흐름에서 새 액세스 토큰을 획득하는 데 사용할 수 있는 보안 토큰입니다. 새로 고침 토큰을 통해 앱은 사용자 조작을 요구하지 않고 사용자 대신 리소스에 장기적으로 액세스할 수 있습니다.

새로 고침 토큰은 다중 리소스입니다. 즉, 한 리소스에 대한 토큰 요청 중에 받은 새로 고침 토큰을 완전히 다른 리소스에 대한 액세스 토큰으로 교환할 수 있습니다.

토큰 응답에서 새로 고침을 받으려면 앱이 `offline_acesss` 범위를 요청하고 부여 받아야 합니다. `offline_access` 범위에 대한 자세한 내용은 [여기서 동의 및 범위 문서](active-directory-v2-scopes.md)를 참조하세요.

새로 고침 토큰은 앱에 완전히 불투명하며 항상 그럴 것입니다. 새로 고침 토큰은 Azure AD v2.0 끝점에서 발급되며 Azure AD v2.0 끝점에서만 검사되고 해석됩니다. 또한 새로 고침 토큰은 수명이 길지만 일정 기간 지속될 것으로 예상하도록 앱을 작성해서는 안 됩니다. 다양한 이유로 언제든지 새로 고침 토큰이 무효화될 수 있기 때문입니다. 앱에서 새로 고침 토큰이 유효한지 확인하는 유일한 방법은 v2.0 끝점에 대한 토큰 요청을 수행하여 교환하는 것입니다.

새로 고침 토큰을 새 액세스 토큰으로 교환할 때 앱에 `offline_access` 범위가 부여된 경우 토큰 응답에 새로운 새로 고침 토큰을 받게 됩니다. 새로 발급된 새로 고침 토큰을 저장하고 요청에 사용한 토큰을 대체해야 합니다. 이렇게 하면 새로 고침 토큰이 최대한 오랫동안 유효한 상태로 유지됩니다.

## 토큰 유효성 검사

이 시점에서 앱이 수행해야 하는 토큰 유효성 검사는 id\_token 유효성 검사뿐입니다. id\_token의 유효성을 검사하려면 앱이 id\_token의 서명과 id\_token에 있는 클레임의 유효성을 모두 검사해야 합니다.

<!-- TODO: Link -->
토큰 유효성 검사를 쉽게 처리하는 방법을 보여 주는 라이브러리 및 코드 샘플이 제공됩니다. 아래 정보는 단순히 기본 프로세스를 이해하려는 사용자를 위한 것입니다. JWT 유효성 검사에 사용할 수 있는 여러 타사 오픈 소스 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대해 하나 이상의 옵션이 있습니다.

#### 서명 유효성 검사
JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 **헤더**, 두 번째 세그먼트는 **본문**, 세 번째 세그먼트는 **서명**이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 id\_token의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

id\_token은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. id\_token의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` 클레임은 토큰 서명에 사용된 알고리즘을 나타내고 `kid` 클레임은 토큰 서명에 사용된 특정 공개 키를 나타냅니다.

특정 시점에 v2.0 끝점은 공개-개인 키 쌍의 특정 집합 중 하나를 사용하여 id\_token에 서명할 수 있습니다. v2.0 끝점은 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. v2.0 끝점에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 약 24시간입니다.

다음 위치에 있는 OpenID Connect 메타데이터 문서를 사용하여 서명 유효성 검사에 필요한 서명 키 데이터를 얻을 수 있습니다.

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] 브라우저에서 이 URL을 사용해 보세요!

이 메타데이터 문서는 OpenID Connect 인증을 수행하는 데 필요한 다양한 끝점의 위치 등 여러 유용한 정보를 포함하는 JSON 개체입니다.

토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 `jwks_uri`도 포함합니다. `jwks_uri`에 있는 JSON 문서는 해당 특정 시점에 사용 중인 공개 키 정보를 모두 포함합니다. 앱은 JWT 헤더에 `kid` 클레임을 사용하여 토큰 서명에 사용된 공개 키를 이 문서에서 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행할 수 있습니다.

서명 유효성 검사는 이 문서의 범위를 벗어납니다. 필요한 경우 이 작업에 도움이 되는 다양한 오픈 소스 라이브러리가 있습니다.

#### 클레임 유효성 검사
사용자 로그인 시 앱이 id\_token을 받은 경우 id\_token의 클레임에 대해서도 몇 가지 검사를 수행해야 합니다. 포함하지만 다음과 같이 제한되지 않습니다.

- **대상** 클레임 - id\_token이 앱에 제공된 것이 맞는지 확인합니다.
- **이전이 아님** 및 **만료 시간** 클레임 - id\_token이 만료되지 않았는지 확인합니다.
- **발급자** 클레임 - v2.0 끝점이 실제로 앱에 토큰을 발급했는지 확인합니다.
- **nonce** - 토큰 재생 공격 완화로 사용됩니다.
- 공유할 수 있습니다.

앱이 수행해야 하는 클레임 유효성 검사의 전체 목록은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)을 참조하세요.

이러한 클레임의 예상 값에 대한 자세한 내용은 위의 [id\_token 섹션](#id_tokens)에 포함되어 있습니다.


## 토큰 수명

다음 토큰 수명은 앱 개발 및 디버그에 유용할 수 있으므로 순수하게 이해를 돕기 위해 제공되었습니다. 이러한 수명이 일정하게 유지된다고 예상하도록 앱을 작성해서는 안 됩니다. 언제든지 변경될 수 있고 변경됩니다.

| 위임 | 수명 | 설명 |
| ----------------------- | ------------------------------- | ------------ |
| id\_token(회사 또는 학교 계정) | 1시간 | id\_token은 일반적으로 1시간 동안 유효합니다. 웹앱은 이 동일한 수명을 사용하여 사용자와의 자체 세션을 유지 관리하거나 완전히 다른 세션 수명을 선택할 수 있습니다. 앱이 새 id\_token을 가져와야 하는 경우 v2.0 권한 부여 끝점에 대한 새 로그인 요청을 만들면 됩니다. 사용자에게 v2.0 끝점과의 유효한 브라우저 세션이 있는 경우 자격 증명을 다시 입력할 필요가 없을 수도 있습니다. |
| id\_token(개인 계정) | 24시간 | 개인 계정에 대한 id\_token은 일반적으로 24시간 동안 유효합니다. 웹앱은 이 동일한 수명을 사용하여 사용자와의 자체 세션을 유지 관리하거나 완전히 다른 세션 수명을 선택할 수 있습니다. 앱이 새 id\_token을 가져와야 하는 경우 v2.0 권한 부여 끝점에 대한 새 로그인 요청을 만들면 됩니다. 사용자에게 v2.0 끝점과의 유효한 브라우저 세션이 있는 경우 자격 증명을 다시 입력할 필요가 없을 수도 있습니다. |
| 액세스 토큰(회사 또는 학교 계정) | 1시간 | 토큰 메타데이터의 일부로 토큰 응답에 표시됩니다. |
| 액세스 토큰(개인 계정) | 1시간 | 토큰 메타데이터의 일부로 토큰 응답에 표시됩니다. 개인 계정 대신 발급된 access\_token은 다른 수명으로 구성할 수도 있지만 1시간이 일반적입니다. |
| 새로 고침 토큰(회사 또는 학교 계정) | 최대 14일 | 단일 새로 고침 토큰이 최대 14일 동안 유효합니다. 그러나 새로 고침 토큰은 다양한 이유로 언제든지 무효화될 수 있으므로 실패하거나 앱이 새로운 새로 고침 토큰으로 대체할 때까지 앱에서 계속 새로 고침 토큰을 시도하고 사용해야 합니다. 사용자가 자격 증명을 입력한 후 90일이 경과한 경우에도 새로 고침 토큰이 무효화됩니다. |
| 새로 고침 토큰(개인 계정) | 최대 1년 | 단일 새로 고침 토큰이 최대 1년 동안 유효합니다. 그러나 새로 고침 토큰은 다양한 이유로 언제든지 무효화될 수 있으므로 실패할 때까지 앱에서 계속 새로 고침 토큰을 시도하고 사용해야 합니다. |
| 인증 코드(회사 또는 학교 계정) | 10분 | 인증 코드는 의도적으로 수명이 짧으므로 받는 즉시 access\_token 및 refresh\_token으로 교환해야 합니다. |
| 인증 코드(개인 계정) | 5분 | 인증 코드는 의도적으로 수명이 짧으므로 받는 즉시 access\_token 및 refresh\_token으로 교환해야 합니다. 또한 개인 계정 대신 발급된 인증 코드는 일회용으로 사용됩니다. |

<!---HONumber=AcomDC_0921_2016-->