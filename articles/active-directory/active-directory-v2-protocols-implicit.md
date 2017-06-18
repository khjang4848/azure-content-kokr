<properties
	pageTitle="Azure AD v2.0 암시적 흐름 | Microsoft Azure"
	description="단일 페이지 앱에 대해 Azure AD의 v2.0 암시적 흐름 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# v2.0 프로토콜 - 암시적 흐름을 사용하는 SPA
v2.0 끝점을 사용하면, Microsoft 개인 계정 및 회사/학교 계정을 사용하여 단일 페이지 앱에 사용자를 로그인할 수 있습니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증과 관련된 흥미로운 난제가 몇 가지 있습니다.

- 이러한 앱의 보안적인 특성은 기존의 서버 기반 웹 응용 프로그램과 확실한 차이가 있습니다.
- 다수의 인증 서버 및 ID 공급자는 CORS 요청을 지원하지 않습니다.
- 앱에서 멀어지는 전체 페이지 브라우저 리디렉션은 사용자 환경에 특히 방해가 됩니다.

이러한 응용 프로그램(예: AngularJS, Ember.js, React.js 등)에 대해 Azure AD는 OAuth 2.0 암시적 허용 흐름을 지원합니다. 암시적 흐름은 [OAuth 2.0 사양(영문)](http://tools.ietf.org/html/rfc6749#section-4.2)에 설명되어 있습니다. 이것의 주요 이점은 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 Azure AD에서 토큰을 가져오도록 허용한다는 것입니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다. 암시적인 흐름을 사용하는 경우(구체적으로 [클라이언트](http://tools.ietf.org/html/rfc6749#section-10.3) 및 [사용자 가장](http://tools.ietf.org/html/rfc6749#section-10.3)과 관련하여) 참작해야 하는 몇 가지 중요한 보안 고려 사항이 있습니다.

암시적 흐름과 Azure AD를 사용하여 JavaScript 앱에 인증을 추가하려면 오픈 소스 JavaScript 라이브러리인 [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js)를 사용하는 것이 좋습니다. 시작에 도움이 될만한 AngularJS 자습서는 [여기](active-directory-appmodel-v2-overview.md#getting-started)에 있습니다.

단일 페이지 앱에 라이브러리를 사용하지 않고 직접 프로토콜 메시지를 보내려면 아래의 일반적인 단계에 따릅니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
    
## 프로토콜 다이어그램
전체 암시적 로그인 흐름은 다음과 같습니다. - 각 단계를 아래에서 자세히 설명합니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## 로그인 요청 보내기

사용자를 앱에 처음으로 로그인하려면 [OpenID Connect](active-directory-v2-protocols-oidc.md) 권한 부여 요청을 보내고 v2.0 끝점으로부터 `id_token`을 받습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `id_token`과 함께 `https://localhost/myapp/`으로 리디렉션됩니다. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | OpenID Connect 로그인을 위한 `id_token`이 포함되어야 합니다. response\_type `token`을 포함할 수도 있습니다. `token`을 여기서 사용하면 앱은 권한 부여 끝점에 두 번째 요청을 수행하지 않고 권한 부여 끝점에서 즉시 액세스 토큰을 받을 수 있습니다. `token` response\_type을 사용하는 경우 `scope` 매개 변수는 토큰을 발행할 리소스를 나타내는 범위를 포함해야 합니다. |
| redirect\_uri | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인" 권한으로 해석되는 `openid` 범위가 포함되어야 합니다. 필요에 따라 추가 사용자 데이터에 액세스하기 위해 `email` 또는 `profile` [범위](active-directory-v2-scopes.md)를 포함할 수도 있습니다. 다양한 리소스에 대한 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| response\_mode | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 암시적 흐름의 경우 `fragment`이어야 합니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 'login', 'none', 'consent'뿐입니다. `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 v2.0 끝점은 오류를 반환합니다. `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| login\_hint | 선택 사항 | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |
| domain\_hint | 선택 사항 | `consumers` 또는 `organizations` 중 하나일 수 있습니다. 포함된 경우, v2.0 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 전자 메일 기반 검색 프로세스를 건너뜁니다. 앱이 id\_token에서 `tid` 클레임을 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

#### 성공적인 응답

읽기 쉽도록 줄 바꿈을 포함하여 `response_mode=fragment` 및 `response_type=id_token+token`을 사용하는 성공적인 응답은 다음과 같이 표시됩니다.

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | `response_type`이 `token`을 포함하는 경우 포함됩니다. Microsoft Graph의 경우 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코딩하지 않거나 그렇지 않은 경우 검사하지 않아야 불투명 문자열로 처리할 수 있습니다. |
| token\_type | `response_type`이 `token`을 포함하는 경우 포함됩니다. 항상 `Bearer`입니다. |
| expires\_in | `response_type`이 `token`을 포함하는 경우 포함됩니다. 캐싱 목적으로 토큰이 유효한 시간(초)을 나타냅니다. |
| scope | `response_type`이 `token`을 포함하는 경우 포함됩니다. access\_token이 유효한 범위를 나타냅니다. |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)에 포함되어 있습니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |


#### 오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## id\_token 유효성 검사
id\_token을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. id\_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. v2.0 끝점은 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

클라이언트 코드에서 `id_token`의 유효성을 검사하도록 선택할 수 있지만, 일반적으로 `id_token`을 백 엔드 서버에 보내서 그 곳에서 유효성 검사를 수행합니다. id\_token의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다. [토큰 유효성 검사](active-directory-v2-tokens.md#validating-tokens) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-v2-tokens.md#validating-tokens)를 포함하여 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. 대부분의 언어 및 플랫폼에서 사용할 수 있는 하나 이상의 토큰의 구문 분석 및 유효성 검사에 대한 라이브러리를 사용하는 것이 좋습니다.
<!--TODO: Improve the information on this-->

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 앱에 등록했는지 확인
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

id\_token의 클레임에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

id\_token의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 id\_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## 액세스 토큰 가져오기

사용자를 단일 페이지 앱에 로그인했으니, [Microsoft Graph](https://graph.microsoft.io)와 같이 Azure AD로 보안이 유지되는 웹 API를 호출하는 액세스 토큰을 가져올 수 있습니다. `token` response\_type을 사용하여 토큰을 이미 받았더라도 이 방법을 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 OpenID Connect/OAuth 흐름에서는 v2.0 `/token` 끝점에 요청을 보내어 이 작업을 수행합니다. 하지만 v2.0 끝점은 CORS 요청을 지원하지 않기 때문에 AJAX 호출이 토큰을 가져오고 새로 고치도록 하는 것은 불가능합니다. 그 대신, 숨겨진 iFrame에 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] 아래 요청을 브라우저 탭에 복사하여 붙여 넣으세요! (`domain_hint` 및 `login_hint` 값을 사용자에 대한 올바른 값으로 바꾸는 것을 잊지 마세요)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | OpenID Connect 로그인을 위한 `id_token`이 포함되어야 합니다. `code`와 같은 다른 response\_types을 포함할 수도 있습니다. |
| redirect\_uri | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 토큰을 가져오는 경우 관련 리소스에 필요한 모든 [범위](active-directory-v2-scopes.md)를 포함합니다. |
| response\_mode | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| prompt | 필수 | 숨겨진 iFrame에서 토큰을 새로 고치고 가져오는 경우 `prompt=none`을 사용하여 v2.0 로그인 페이지에 iFrame이 머무르지 않고 즉시 반환되는지 확인해야 합니다. |
| login\_hint | 필수 | 숨겨진 iFrame에서 토큰을 새로 고치고 가져오는 경우 사용자가 지정된 시간에 가질 수 있는 여러 세션을 구분하기 위해 이 힌트에 해당 사용자의 사용자 이름을 포함해야 합니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름을 추출할 수 있습니다. |
| domain\_hint | 필수 | `consumers` 또는 `organizations` 중 하나일 수 있습니다. 숨겨진 iFrame에서 토큰을 새로 고치고 가져오는 경우 요청에 domain\_hint를 포함해야 합니다. 사용할 값을 결정하기 위해 이전 로그인의 id\_token에서 `tid` 클레임을 추출해야 합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

`prompt=none` 매개 변수로 인해, 이 요청은 즉시 성공하거나 실패하고 응용 프로그램에 반환됩니다. 성공적인 응답은 `response_mode` 매개 변수에 지정된 메서드를 사용하여 지정된 `redirect_uri`의 앱으로 전송됩니다.

#### 성공적인 응답
`response_mode=fragment`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 앱이 요청한 토큰입니다. |
| token\_type | 항상 `Bearer`입니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| scope | access\_token이 유효한 범위입니다. |

#### 오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다. `prompt=none`의 경우, 예상되는 오류는 다음과 같습니다.

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다. 어떠한 방식이든 응용 프로그램에 적합한 방식으로 이러한 경우를 처리하도록 선택할 수 있습니다.

## 토큰 새로 고침

`id_token`과 `access_token`은 모두 짧은 시간 안에 만료되기 때문에 앱은 주기적으로 토큰을 새로 고치도록 준비가 되어야 합니다. 이러한 유형의 토큰을 새로 고치려면, Azure AD의 동작을 제어하도록 `prompt=none` 매개 변수를 사용하여 위와 동일한 숨겨진 iFrame 요청을 수행합니다. 새 `id_token`을 받으려면 `response_type=id_token` 및 `scope=openid`는 물론 `nonce` 매개 변수를 사용해야 합니다.


## 로그아웃 요청 보내기

OpenIdConnect `end_session_endpoint`는 현재 v2.0 끝점에서 지원되지 않습니다. 즉, 앱이 v2.0 끝점에 요청을 보내 사용자 세션을 종료하고 v2.0 끝점에서 설정한 쿠키를 지울 수 없습니다. 사용자를 로그아웃하기 위해 앱은 단순히 사용자와의 해당 세션을 종료하고 v2.0 끝점과의 사용자 세션을 그대로 둡니다. 다음에 사용자가 로그인하려고 하면 자주 로그인한 계정이 나열된 "계정 선택" 페이지가 표시됩니다. 해당 페이지에서 사용자는 계정을 로그아웃하고 v2.0 끝점과의 세션을 종료할 수 있습니다.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

<!---HONumber=AcomDC_0921_2016-->