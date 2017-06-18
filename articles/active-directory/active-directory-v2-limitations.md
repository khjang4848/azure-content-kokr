<properties
	pageTitle="v2.0 끝점 제한 사항 | Microsoft Azure"
	description="Azure AD v2.0 끝점의 제한 사항 목록입니다."
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

# v2.0 끝점을 사용해야 하나요?

Azure Active Directory와 통합되는 응용 프로그램을 빌드할 때 v2.0 끝점 및 인증 프로토콜이 사용자 요구를 충족할지 결정해야 합니다. 원래 Azure AD 앱 모델은 완벽하게 지원되며 v2.0보다 더 풍부한 기능을 갖추고 있습니다. 그러나 v2.0 끝점은 사용자가 새로운 프로그래밍 모델을 사용하도록 유도할 수 있는 개발자에게 [상당한 혜택을 소개](active-directory-v2-compare.md)합니다. 시간이 지남에 따라 v2.0은 v2.0 끝점을 사용하는 것만으로 충분하도록 Azure AD의 모든 기능을 포함하도록 확장됩니다.

현 시점에는 v2.0 끝점으로 달성할 수 있는 두 가지 핵심 기능이 있습니다.

- 개인 및 회사 계정으로 사용자 로그인
- [수렴형 Outlook API](https://dev.outlook.com) 호출

이러한 기능은 모두 웹, 모바일 및 PC 응용 프로그램 등에서 구현할 수 있습니다. 응용 프로그램에 이러한 비교적 제한된 기능이 적합한 경우 v2.0 끝점을 사용하는 것이 좋습니다. 응용 프로그램에 Microsoft 서비스의 추가 기능이 필요한 경우 Azure AD 및 Microsoft 계정의 시도된 실제 끝점을 계속 사용하는 것이 좋습니다. 때가 되면 v2.0 끝점은 Azure AD 및 Microsoft 계정 이상을 포함하도록 설정되고 모든 개발자가 v2.0 끝점으로 전환할 수 있게 됩니다.

그 동안 이 문서는 v2.0 끝점이 사용자에게 적합한지 결정하는 데 도움이 되도록 작성되었습니다. v2.0 끝점의 현재 상태를 반영하도록 시간에 따라 이 문서를 계속 업데이트할 예정이므로 다시 확인하여 v2.0 기능에 대해 요구 사항을 다시 평가하세요.

v2.0 끝점을 사용하지 않는 Azure AD 사용 기존 앱이 있는 경우 처음부터 시작할 필요가 없습니다. 나중에 v2.0 끝점에 사용할 기존 Azure AD 응용 프로그램을 설정하는 방법을 제공할 예정입니다.

## 앱에 대한 제한 사항
다음 유형의 앱은 v2.0 끝점에서 현재 지원되지 않습니다. 지원되는 앱 형식에 대한 설명은 [이 문서](active-directory-v2-flows.md)를 참조하세요.

##### 독립 실행형 Web API
v2.0 끝점으로는 [OAuth 2.0을 사용하여 보안된 Web API를 빌드](active-directory-v2-flows.md#web-apis)할 수 있습니다. 그러나 해당 Web API는 동일한 응용 프로그램 ID를 공유하는 응용 프로그램에서만 토큰을 받을 수 있습니다. 여러 다른 응용 프로그램 ID로 클라이언트에서 액세스되는 Web API 빌드는 지원되지 않습니다. 해당 클라이언트는 Web API에 대한 권한을 요청하거나 얻을 수 없습니다.

동일한 앱 ID를 가진 클라이언트의 토큰을 수락하는 Web API를 빌드하는 방법을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에서 v2.0 끝점 Web API 샘플을 참조하세요.

##### 디먼/서버 쪽 앱
장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 앱은 Web API아 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다.

이 흐름은 현재 v2.0 끝점에서 지원되지 않으므로 대화형 사용자 로그인 흐름이 발생한 후에만 앱이 토큰을 가져올 수 있습니다. 조만간 클라이언트 자격 증명 흐름이 추가될 예정입니다. 원래 Azure AD 끝점을 사용하여 클라이언트 자격 증명 흐름을 확인하려면 [GitHub의 디먼 샘플](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

##### Web API On-Behalf-Of 흐름
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 v2.0 끝점에 의해 보안이 유지됩니다. 이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Microsoft 온라인 서비스 또는 Azure AD를 지원하는 사용자 지정 작성 Web API를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 v2.0 끝점에 대해 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [GitHub의 On-Behalf-Of 코드 샘플](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)을 참조하세요.

## 앱 등록에 대한 제한 사항
이 시점에서 v2.0 끝점과 통합하려는 모든 앱은 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새로운 앱 등록을 만들어야 합니다. 기존 Azure AD 또는 Microsoft 계정 앱은 v2.0 끝점과 호환되지 않으며 새로운 앱 등록 포털 외에는 어떤 포털에서도 앱이 등록되지 않습니다. v2.0 앱으로 기존 응용 프로그램을 사용하도록 설정하는 방법을 제공할 계획이지만 현재는 앱을 v2.0 끝점으로 마이그레이션하는 경로가 없습니다.

마찬가지로, 새 앱 등록 포털에 등록된 앱은 원래 Azure AD 인증 끝점에 대해 작동하지 않습니다. 그러나 Microsoft 계정 인증 끝점 `https://login.live.com`과 성공적으로 통합하기 위해 앱 등록 포털에서 만든 앱을 사용할 수 있습니다.

새로운 응용 프로그램 등록 포털에서 등록된 앱은 현재 제한된 redirect\_uri 값 집합으로 한정됩니다. 웹앱 및 서비스에 대한 redirect\_uri는 체계 또는 `https`로 시작해야 하는 반면, 다른 모든 플랫폼에 대한 redirect\_uri는 `urn:ietf:oauth:2.0:oob`라는 하드 코드된 값을 사용해야 합니다.

## 리디렉션 URI에 대한 제한
웹앱의 경우 redirect\_uri 값은 모두 단일 DNS 도메인을 공유해야 합니다. 예를 들어 redirect\_uris가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com` `https://login-west.contoso.com`

등록 시스템은 기존 redirect\_uri의 전체 DNS 이름을 편집하려는 redirect\_uri의 DNS 이름과 비교합니다. 새 redirect\_uri의 전체 DNS 이름이 기존 redirect\_uri의 DNS 이름과 정확히 일치하지 않거나 새 redirect\_uri의 전체 DNS 이름이 기존 redirect\_uri의 하위 도메인이 아닌 경우 추가하려는 요청이 실패합니다. 예를 들어 앱이 현재 redirect\_uri를 갖는 경우

`https://login.contoso.com`

DNS 이름과 정확히 일치하는 다음을 추가합니다.

`https://login.contoso.com/new`

또는 다음을 추가할 수 있습니다.

`https://new.login.contoso.com`

(login.contoso.com의 DNS 하위 도메인에 해당) redirect\_uris로 login-east.contoso.com 및 login-west.contoso.com을 갖는 앱을 원할 경우 다음 redirect\_uris를 순서대로 추가해야 합니다.

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

뒤의 두 개는 첫 번째 redirect\_uri의 하위 도메인이므로 추가할 수 있습니다. 이 제한은 향후 릴리스에서 제거될 예정입니다.

새로운 응용 프로그램 등록 포털에서 앱을 등록하는 방법을 알아보려면 [이 문서](active-directory-v2-app-registration.md)를 참조하세요.

## 서비스 및 API에 대한 제한 사항
v2.0 끝점은 [지원되는 인증 흐름](active-directory-v2-flows.md) 목록에 해당하는 경우 현재 새로운 응용 프로그램 등록 포털에서 등록된 모든 앱에 대해 로그인을 지원합니다. 그러나 이러한 앱은 매우 제한된 리소스 집합에 대한 OAuth 2.0 액세스 토큰만 획득할 수 있습니다. v2.0 끝점은 다음에 대한 access\_token만 발급합니다.

- 토큰을 요청한 앱. 논리적 앱이 여러 구성 요소나 계층으로 이루어진 경우 앱이 자신에 대한 access\_token을 획득할 수 있습니다. 이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 자습서를 확인하세요.
- 모두 https://outlook.office.com에 있는 Outlook 메일, 일정 및 연락처 REST API. 이러한 API에 액세스하는 앱을 작성하는 방법을 알아보려면 [Office 시작](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 자습서를 참조하세요.
- Microsoft Graph API. Microsoft Graph 및 사용할 수 있는 모든 데이터에 대해 알아보려면 [https://graph.microsoft.io](https://graph.microsoft.io)를 방문하세요.

현재 다른 서비스는 지원되지 않습니다. 조만간 사용자 고유의 사용자 지정 작성 Web API 및 서비스 지원뿐 아니라 더 많은 Microsoft 온라인 서비스가 추가될 예정입니다.

## 라이브러리 및 SDK에 대한 제한 사항
사용해 볼 수 있도록 v2.0 끝점과 호환되는 실험적 버전의 Active Directory Authentcation Library를 제공합니다. 그러나 이 버전의 ADAL은 미리 보기 상태로 지원되지 않으며 향후 몇 개월 동안 크게 변경됩니다. v2.0 끝점에서 실행되는 앱을 신속하게 가져오려는 경우 .NET, iOS, Android 및 Javascript에 대해 ADAL을 사용하는 코드 샘플은 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에 제공됩니다.

프로덕션 응용 프로그램에서 v2.0 끝점을 사용하려는 경우 다음 옵션이 있습니다.

- 웹 응용 프로그램을 작성하는 경우 일반적으로 사용 가능한 서버 쪽 미들웨어를 안전하게 사용하여 로그인 및 토큰 유효성 검사를 수행할 수 있습니다. 여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 NodeJS Passport 플러그 인이 포함됩니다. 이러한 미들웨어를 사용하는 코드 샘플도 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에 제공됩니다.
- 다른 플랫폼과 네이티브 및 모바일 응용 프로그램의 경우 응용 프로그램 코드에서 프로토콜 메시지를 직접 전송 및 수신하여 v2.0 끝점과 통합할 수도 있습니다. v2.0 OpenID Connect 및 OAuth 프로토콜은 [명시적으로 문서화](active-directory-v2-protocols.md)되어 있어 이러한 통합을 수행하는 데 도움이 됩니다.
- 마지막으로, v2.0 끝점과 통합하는 데 오픈 소스 Open ID Connect 및 OAuth 라이브러리를 사용할 수 있습니다. v2.0 프로토콜은 크게 변경되지 않고 다양한 오픈 소스 프로토콜 라이브러리와 호환되어야 합니다. 이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [Open ID Connect](http://openid.net/connect/) 및 [OAuth 2.0](http://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. 다음은 v2.0 끝점을 사용하여 시험한 오픈 소스 클라이언트 라이브러리 및 샘플입니다.

  - [Java WSO2 ID 서버](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu 페더레이션](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect 기본 클라이언트](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [iOS OAuth2 클라이언트](https://github.com/nxtbgthng/OAuth2Client)
  - [Android OAuth2 클라이언트](https://github.com/wuman/android-oauth-client)
  - [Android OpenID Connect 클라이언트](https://github.com/kalemontes/OIDCAndroidLib)

## 프로토콜에 대한 제한 사항
v2.0 끝점만 Open ID Connect 및 OAuth 2.0을 지원합니다. 그러나 각 프로토콜의 일부 특징과 기능은 v2.0 끝점에 통합되어 있지 않습니다. 일부 사례:

- OpenID Connect `end_sesssion_endpoint`
- OAuth 2.0 클라이언트 자격 증명 부여

v2.0 끝점에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

## 고급 Azure AD 개발자 기능
Azure Active Directory 서비스에서 사용할 수 있는 개발자 기능 집합 중 v2.0 끝점에 대해서는 아직 지원되지 않는 다음과 같은 기능이 있습니다.

- Azure AD 사용자에 대한 그룹 클레임
- 응용 프로그램 역할 및 역할 클레임

<!---HONumber=AcomDC_0921_2016-->