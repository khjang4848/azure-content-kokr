<properties
	pageTitle="Azure AD v2.0 끝점으로 바꾸기 | Microsoft Azure"
	description="앱 모델 v2.0 공개 미리보기 프로토콜에 적용될 변경 사항에 대한 설명"
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

# v2.0 인증 프로토콜에 대한 중요 업데이트
개발자들은 주목하세요! 앞으로 2주 동안 v2.0 인증 프로토콜에 대해 몇 가지 업데이트를 수행할 예정이며, 이는 미리보기 기간 동안 작성한 앱에 대해 상당한 변화를 가져올 수 있습니다.

## 어떤 것이 영향을 받습니까?
v2.0 수렴형 인증 끝점을 사용하여 작성된 모든 앱.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

v2.0 끝점에 대한 자세한 내용은 [여기](active-directory-appmodel-v2-overview.md)에서 확인할 수 있습니다.

v2.0 프로토콜에 대해 직접 코딩하여 v2.0 끝점을 사용하거나 OpenID Connect 또는 OAuth 웹 미들웨어를 사용하거나 인증을 수행하기 위해 다른 타사 라이브러리를 사용하여 앱을 작성한 경우, 프로젝트를 테스트하고 필요하면 변경할 준비를 해야 합니다.

## 어떤 것이 영향을 받지 않습니까?
프로덕션 Azure AD 인증 끝점에 대해 작성된 모든 앱.

```
https://login.microsoftonline.com/common/oauth2/authorize
```

이 프로토콜은 확정되었으며 어떠한 변경도 발생하지 않습니다.

또한 앱이 ADAL 라이브러리**만**를 사용하여 인증을 수행하는 경우 어떤 것도 변경할 필요가 없습니다. ADAL은 앱을 변경하지 못하도록 차단합니다.

## 변경 사항은 무엇입니까?
### JWT 헤더에서 x5t 값 제거
v2.0 끝점은 JWT 토큰을 광범위하게 사용하며, 토큰과 관련된 메타데이터와 헤더 매개 변수 섹션을 포함합니다. 현재 JWT 중 하나의 헤더를 디코딩하는 경우, 다음을 확인할 수 있습니다.

```
{ 
	"type": "JWT",
	"alg": "RS256",
	"x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
	"kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

OpenID Connect 메타데이터 끝점에서 검색된 토큰의 서명 유효성을 검사하는 데 사용해야 하는 공개 키를 "x5t" 및 "kid"의 두 속성이 식별하는 위치.

여기서 변경하려는 내용은 "x5t" 속성을 제거하는 것입니다. 토큰 유효성을 검사하기 위해 동일한 메커니즘을 계속 사용할 수 있지만, OpenID Connect 프로토콜에 지정된 대로 올바른 공개 키를 검색하기 위해 "kid" 속성에만 의존해야 합니다.

> [AZURE.IMPORTANT] **작업: 앱이 x5t 값의 존재에 종속되지 않도록 하세요.**

### profile\_info 제거
이전에는 v2.0 끝점이 `profile_info`라고 하는 토큰 응답에 있는 base64 인코딩 JSON 개체를 반환해왔습니다. 요청을 다음에 전송하여 v2.0 끝점으로부터 액세스 토큰을 요청하는 경우

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

응답은 다음과 같은 JSON 개체처럼 보입니다.
```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`profile_info` 값에는 표시 이름, 이름, 성, 전자 메일 주소, 식별자 및 등 앱에 서명한 사용자에 관한 정보가 있습니다. `profile_info`은(는) 주로 토큰 캐싱과 표시 용도로 사용되었습니다.

이제 `profile_info` 값이 제거되지만, 개발자에게 이 정보를 다소 다른 위치에서 제공합니다. `profile_info` 대신, v2.0 끝점이 각 토큰 응답에서 `id_token`을(를) 반환하게 됩니다.

```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

profile\_info에서 수신한 동일한 정보를 검색하는 id\_token을 디코딩하고 구문 분석할 수 있습니다. id\_token는 OpenID Connect에 지정된 대로 콘텐츠가 포함된 JSON 웹 토큰(JWT)입니다. 이를 수행하기 위한 코드는 매우 유사해야 합니다. 즉, 범위 내에서 JSON 개체를 액세스하기 위해 id\_token 및 base64 디코딩의 중간 세그먼트(본문)를 추출하기만 하면 됩니다.

앞으로 2주 동안 `id_token` 또는 `profile_info`에 존재하는 사용자 정보를 검색하기 위해 앱을 코딩해야 합니다. 이러한 방식으로 변경할 때 앱은 중단 없이 `profile_info`에서 `id_token`(으)로의 전환을 원활하게 처리할 수 있습니다

> [AZURE.IMPORTANT] **작업: 앱이 `profile_info` 값의 존재에 종속되지 않도록 하세요.**

### Id\_token\_expires\_in 제거
`profile_info`와(과) 마찬가지로 응답에서 `id_token_expires_in` 매개 변수도 제거할 것입니다. 이전에는 권한 부여 응답 또는 토큰 응답의 인스턴스에 대해 v2.0 끝점이 각 id\_token 응답과 함께 `id_token_expires_in`에 대한 값을

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

반환합니다.

```
{ 
	"token_type": "Bearer",
	"id_token_expires_in": 3599,
	"scope": "openid",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`id_token_expires_in` 값은 id\_token을 유효한 상태로 유지하는 시간(초)의 수를 나타냅니다. 이제 `id_token_expires_in` 값을 완전히 제거하겠습니다. 대신에 OpenID Connect 표준 `nbf` 및 `exp` 클레임을 사용하여 id\_token의 유효성을 검사할 수도 있습니다. 이러한 클레임에 대한 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

> [AZURE.IMPORTANT] **작업: 앱이 `id_token_expires_in` 값의 존재에 종속되지 않도록 하세요.**


### scope=openid에 의해 반환된 클레임 변경
이 변경은 영향력이 가장 큽니다. 실제로 v2.0 끝점을 사용하는 거의 모든 앱에 영향을 줍니다. 많은 응용 프로그램은 다음과 같은 `openid` 범위를 사용하여 v2.0 끝점에 요청을 전송합니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

현재 `openid` 범위에 대해 사용자가 동의한 경우, 앱은 결과 id\_token의 사용자에 대해 다양한 정보를 수신합니다. 이러한 클레임에는 이름, 기본 설정된 사용자 이름, 전자 메일 주소, 개체 ID 등이 있습니다.

이 업데이트에는 OpenID Connect 사양으로 보다 잘 확인하기 위해 `openid` 범위가 허용하는 앱의 액세스 정보를 변경할 것입니다. `openid` 범위는 앱이 id\_token의 `sub` 클레임에 있는 사용자를 로그인시키고 해당 사용자에 대한 앱별 식별자만 수신할 수 있게 합니다. 부여된 `openid` 범위만 있는 id\_token의 클레임에는 개인 식별 정보가 없습니다. 예시 id\_token 클레임은 다음과 같습니다.

```
{ 
	"aud": "580e250c-8f26-49d0-bee8-1c078add1609",
	"iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
	"iat": 1449520283,
	"nbf": 1449520283,
	"exp": 1449524183,
	"nonce": "12345",
	"sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
	"tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
	"ver": "2.0",
}
```

앱에 있는 사용자의 개인 식별 정보(PII)를 가져오려면 앱은 사용자로부터 추가 권한을 요청해야 합니다. 이를 수행할 수 있는 OpenID Connect 사양의 두 가지의 새로운 범위 즉, `email` 및 `profile` 범위에 대한 지원을 도입할 것입니다.

- `email` 범위는 매우 간단합니다. 앱이 id\_token의 `email` 클레임을 통해 사용자의 기본 전자 메일 주소에 액세스할 수 있도록 해줍니다. `email` 클레임이 id\_tokens에 항상 존재하지는 않을 것입니다. 사용자의 프로필에 사용할 수 있는 경우에만 포함됩니다.
- `profile` 범위는 앱이 사용자 이름, 기본 설정된 사용자 이름, 개체 ID 등 사용자에 관한 모든 기타 기본 정보에 액세스할 수 있도록 해줍니다.

이렇게 하면 최소한의 공개로 앱을 코딩할 수 있으며, 작업 수행에 필요한 정보를 사용자에게 요청할 수 있습니다. 현재 앱이 수신하고 있는 전체 사용자 정보 전체를 계속 가져오려는 경우, 권한 부여 요청에 세 가지 범위 모두를 포함시켜야 합니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

앱은 즉시 `email` 및 `profile` 범위 전송을 시작할 수 있으며, v2.0 끝점은 이러한 두 범위를 수락하고 필요에 따라 사용자로부터 권한 요청을 시작합니다. 그러나 `openid` 범위에 대한 해석 변경은 몇 주 동안 적용되지 않을 것입니다.

> [AZURE.IMPORTANT] **작업: 앱이 사용자에 대한 정보를 요구하는 경우 `profile` 및 `email` 범위를 추가하세요.** ADAL은 기본적으로 요청에 이러한 권한을 모두 포함하게 됩니다.

### 발급자 후행 슬래시 제거
이전에는 v2.0 끝점의 토큰에 표시되는 발급자 값이 양식을 사용했습니다.

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

이 양식에서 GUID는 토큰을 발급한 Azure AD 테넌트의 tenantId입니다. 이러한 변경 사항과 함께 발급자 값은

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

두 토큰과 OpenID Connect Discovery 문서에 나와 있습니다.

> [AZURE.IMPORTANT] **작업: 앱이 발급자 유효성 검사 중 후행 슬래시가 있는 발급자 값과 없는 발급자 값 모두 수락하는지 확인하세요.**

## 변경한 이유는 무엇입니까?
이러한 변경 사항을 도입하는 주된 목적은 OpenID Connect 표준 사양을 준수하기 위한 것입니다. OpenID Connect를 준수함으로써 Microsoft ID 서비스와 업계에서 다른 ID 서비스를 통합하는 과정에서 차이가 최소화되기 바랍니다. 개발자가 Microsoft의 차이점을 수용하기 위해 라이브러리를 변경하지 않고도 자신의 좋아하는 오픈 소스 인증 라이브러리를 사용할 수 있도록 하려 합니다.

## 어떻게 해야 합니까?
현재 위에서 설명한 모든 변경을 시작할 수 있습니다. 다음은 즉시 수행해야 합니다.

1.	**`x5t` 헤더 매개 변수의 모든 종속성을 제거합니다.**
2.	**토큰 응답에서 `profile_info`에서 `id_token`(으)로의 전환을 적절하게 처리합니다.**
3.  **`id_token_expires_in` 응답 매개 변수의 모든 종속성을 제거합니다.**
3.	**앱에 기본 사용자 정보가 필요한 경우, `profile` 및 `email` 범위를 앱에 추가합니다.**
4.	**토큰에 후행 슬래시가 있는 발급자 값과 없는 발급자 값을 모두 수락합니다.**

[v2.0 프로토콜 설명서](active-directory-v2-protocols.md)에는 이미 이러한 변경 내용을 반영 되어 있기 때문에, 코드를 업데이트하는 데 참조로 사용할 수 있습니다.

변경의 범위에 대해 추가 질문이 있으면 Twitter @AzureAD로 문의하세요.

## 프로토콜은 얼마나 자주 변경합니까?
이후 인증 프로토콜의 주요 변경을 예상하지 않습니다. 이러한 유형의 업데이트 과정을 너무 자주 거치지 않도록 의도적으로 이러한 변경 사항들을 하나의 릴리스로 묶습니다. 물론, 수렴형 v2.0 인증 서비스를 활용하도록 기능을 계속해서 추가할 것이지만, 이러한 변경 사항을 추가할 뿐 기존 코드를 변경하지 않습니다.

마지막으로, 이번 미리보기 기간 동안 작업을 시도해주신데 대해 감사 말씀을 드립니다. 지금까지 최초 도입자의 통찰력과 경험은 매우 소중했으며, 계속 의견 및 아이디어를 공유해주시기를 바랍니다.

즐거운 코딩 작업이 되길 바랍니다!

Microsoft ID 부서

<!---HONumber=AcomDC_0921_2016-->