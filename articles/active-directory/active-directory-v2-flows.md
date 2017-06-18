<properties
	pageTitle="v2.0 끝점의 형식 | Microsoft Azure"
	description="Azure AD v2.0 끝점에서 지원되는 앱 형식 및 시나리오입니다."
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

# V 2.0 끝점의 형식
V2.0 끝점은 모두 업계 표준 프로토콜 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 및/또는 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다. 이 문서에서는 선호하는 언어 또는 플랫폼에 독립적으로 빌드할 수 있는 앱 형식에 대해 간략하게 설명합니다. [코드를 바로 시작](active-directory-appmodel-v2-overview.md#getting-started)하기 전에 높은 수준의 시나리오를 이해하는 데 도움이 됩니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## 기본 사항
v2.0 끝점을 사용하는 각 앱을 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

- 앱을 고유하게 식별하는 **응용 프로그램 ID**
- 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
- 다른 몇 가지 시나리오 관련 값. 자세한 내용은 [앱 등록](active-directory-v2-app-registration.md) 방법을 참조하세요.

등록되면 앱이 Azure Active Directory v2.0 끝점에 요청을 보내기 위해 Azure AD와 통신합니다. 오픈 소스 프레임워크 및 이러한 요청에 대한 세부 정보를 관리하는 라이브러리를 제공하거나 이러한 끝점에 대 한 요청을 선별하여 직접 인증 논리를 구현할 수 있습니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## 웹 앱
브라우저를 통해 액세스되는 웹앱(.NET, PHP, Java, Ruby, Python, Node 등)의 경우 [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)를 사용하여 사용자 로그인을 지원할 수 있습니다. OpenID Connect에서는 웹앱이 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰인 `id_token`을 받습니다.

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

[v2.0 토큰 참조](active-directory-v2-tokens.md)에서 앱이 사용할 수 있는 모든 토큰 및 클레임 형식에 대해 알아볼 수 있습니다.

웹 서버 앱에서 로그인 인증 흐름은 다음과 같은 높은 수준의 단계를 수행합니다.

![웹앱 스윔 레인 이미지](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

v2.0 끝점에서 받은 공개 서명 키를 사용하여 id\_token의 유효성을 검사하기만 하면 사용자 ID를 확인하고 후속 페이지 요청 시 사용자를 식별하는 데 사용할 수 있는 세션 쿠키를 설정할 수 있습니다.

이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션의 웹앱 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 REST API와 같은 다른 일부 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹 서버 앱은 [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols.md#oauth2-authorization-code-flow)을 사용하여 결합된 OpenID Connect 및 OAuth 2.0 흐름에 참여할 수 있습니다. 이 시나리오는 아래의 [웹앱-웹 API 시작 항목](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)에서 설명합니다.

## Web API
v2.0 끝점을 사용하여 앱의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. Id\_token 및 세션 쿠키 대신 Web API는 OAuth 2.0 access\_token을 사용하여 데이터 보안을 유지하고 들어오는 요청을 인증합니다. Web API 호출자는 HTTP 요청의 인증 헤더에 access\_token을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API는 access\_token을 사용하여 API 호출자의 ID를 확인하고 access\_token에 인코드된 클레임에서 호출자에 대한 정보를 추출할 수 있습니다. [v2.0 토큰 참조](active-directory-v2-tokens.md)에서 앱이 사용할 수 있는 모든 토큰 및 클레임 형식에 대해 알아볼 수 있습니다.

Web API를 통해 사용자는 [범위](active-directory-v2-scopes.md)라고 하는 사용 권한을 노출하여 특정 기능이나 데이터를 옵트인(opt-in)하거나 옵트아웃(opt-out)할 수 있습니다. 호출 앱이 범위에 대한 사용 권한을 얻으려면 사용자가 흐름 중 범위에 동의해야 합니다. v2.0 끝점이 사용자에게 사용 권한을 요청하고 Web API가 받는 모든 access\_token에 이러한 사용 권한을 기록합니다. Web API는 각 호출에서 받은 access\_token의 유효성을 검사하고 적절한 권한 부여 검사를 수행하기만 하면 됩니다.

Web API는 웹 서버 앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 Web API까지 포함하여 모든 유형의 앱에서 access\_token을 받을 수 있습니다. 웹 API 인증에 대한 높은 수준의 흐름은 다음과 같습니다.

![웹 API 스윔 레인 이미지](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

authorization\_code, refresh\_token 및 access\_token을 가져오는 세부 단계에 대한 자세한 내용은 [OAuth 2.0 프로토콜](active-directory-v2-protocols-oauth-code.md)을 참조하세요.

OAuth 2.0 access\_token을 사용하여 Web API 보안을 유지하는 방법을 알아보려면 [시작 섹션](active-directory-appmodel-v2-overview.md#getting-started)에서 Web API 코드 샘플을 확인하세요.


## 모바일 및 네이티브 앱
모바일 및 데스크톱 앱과 같은 장치에 설치된 앱은 데이터를 저장하고 사용자 대신 다양한 기능을 수행하는 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 이러한 앱은 [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols-oauth-code.md)을 사용하여 백 엔드 서비스에 로그인 및 권한 부여를 추가할 수 있습니다.

이 흐름에서 앱은 사용자 로그인 시 v2.0 끝점에서 authorization\_code를 받습니다. 이 코드는 현재 로그인한 사용자 대신 백 엔드 서비스를 호출할 수 있는 앱의 권한을 나타냅니다. 앱은 백그라운드에서 authoriztion\_code를 OAuth 2.0 access\_token 및 refresh\_token으로 교환할 수 있습니다. 앱은 access\_token을 사용하여 HTTP 요청 시 Web API에 인증할 수 있고 refresh\_token을 사용하여 이전 토큰 만료 시 새 access\_token을 가져올 수 있습니다.

![네이티브 앱 스윔 레인 이미지](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## 단일 페이지 앱(Javascript)
대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 프레임워크로도 작성되는 단일 페이지 앱(SPA) 프런트 엔드가 있습니다. Azure AD v 2.0 끝점은 [OAuth 2.0 암시적 흐름](active-directory-v2-protocols-implicit.md)을 사용하여 이러한 앱을 지원합니다.

이 흐름에서 앱은 v 2.0 인증 끝점에서 바로 토큰을 수신하며, 백 엔드 서버 대 서버 교환을 수행하지 않습니다. 이렇게 하면 모든 인증 논리 및 세션 처리가 추가 페이지 리디렉션을 수행하지 않고 전적으로 javascript 클라이언트에서 발생합니다.

![암시적 흐름 스윔 레인 이미지](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션의 단일 페이지 앱 코드 샘플 중 하나를 시도해 보세요.

## 현재 제한 사항
이러한 종류의 앱은 v2.0 끝점에서 현재 지원되지 않지만 로드맵에 있습니다. v2.0 끝점에 대한 추가 제한 사항 및 제약은 [v2.0 제한 사항 문서](active-directory-v2-limitations.md)에서 설명합니다.

### 디먼/서버 쪽 앱
장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 앱은 Web API아 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다.

클라이언트 자격 증명 흐름은 v2.0 끝점에서 현재 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [GitHub의 디먼 코드 샘플](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

### 연결된 Web API(On-Behalf-Of)
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 v2.0 끝점에 의해 보안이 유지됩니다. 이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Office 365 또는 Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

[On-Behalf-Of 흐름](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 v2.0 끝점에 구현되어 있지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [GitHub의 On-Behalf-Of 코드 샘플](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->