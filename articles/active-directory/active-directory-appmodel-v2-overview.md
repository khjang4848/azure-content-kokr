<properties
	pageTitle="앱 모델 v2.0 개요 | Microsoft Azure"
	description="Microsoft 계정 및 Azure Active Directory 로그인을 사용하는 앱 구축을 소개합니다."
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

# 단일 앱에서 Microsoft 계정 및 Azure AD 사용자 로그인

과거에 Microsoft 계정과 Azure Active Directory를 지원하려는 앱 개발자는 별도의 두 시스템을 통합해야 했습니다. 지금까지 Azure AD 시스템을 사용하여 두 가지 유형의 계정을 모두 사용하여 사용자가 로그인할 수 있게 해주는 새 인증 API 버전을 살펴보았습니다. 이 수렴된 인증 시스템을 **v2.0 끝점**이라고 합니다. v2.0 끝점을 사용하면 간단한 통합을 사용하여 개인 및 작업/학교 계정으로 범위가 수 백만 명의 사용자인 대상에 도달할 수 있습니다.

또한 v2.0 끝점을 사용하는 앱은 두 가지 계정 유형 중 하나 이상을 사용하여 [Microsoft Graph](https://graph.microsoft.io) 및 [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)에서 REST API를 사용할 수 있습니다.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## 시작하기
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

아래에서 원하는 플랫폼을 선택하여 당사의 오픈 소스 라이브러리 및 프레임워크를 사용하여 앱을 만듭니다. 또는 인증 라이브러리를 사용하지 않고 당사의 OAuth 2.0 및 OpenID Connect 프로토콜 설명서를 사용하여 프로토콜 메시지를 직접 보내고 받을 수 있습니다.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 새로운 기능
여기서 설명하는 개념 정보는 v2.0 끝점을 사용하여 할 수 있는 일과 할 수 없는 일을 이해하는 데 유용합니다.

- v2.0 끝점 2015년 미리 보기 기간 중에 앱을 만드는 경우 당사가 최근 실행한 [최신 프로토콜 변경에 대해 읽어야](active-directory-v2-preview-oidc-changes.md) 합니다.
- [v2.0 끝점을 사용하여 만들 수 있는 앱의 종류](active-directory-v2-flows.md)에 대해 알아봅니다.
- Azure Active Directory에 익숙한 개발자의 경우 [v2.0 끝점의 프로토콜 및 차이점에 대한 업데이트](active-directory-v2-compare.md)를 확인합니다.
- v2.0 끝점에 대한 [제한, 제한 사항 및 제약 조건](active-directory-v2-limitations.md)을 이해합니다.

## 참조
이러한 링크는 플랫폼을 자세히 탐색하는 데 유용합니다.

- 빌드 2016: [Microsoft ID 시작하기: 앱에 대한 엔터프라이즈 수준 로그인](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 또는 [adal](http://stackoverflow.com/questions/tagged/adal) 태그를 사용하여 스택 오버플로에 대한 도움말을 봅니다.
- [v2.0 프로토콜 참조](active-directory-v2-protocols.md)
- [v2.0 토큰 참조](active-directory-v2-tokens.md)
- [v2.0 끝점의 범위 및 동의](active-directory-v2-scopes.md)
- [Microsoft 앱 등록 포털](https://apps.dev.microsoft.com)
- [Office 365 REST API 참조](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- 다음은 v2.0 끝점을 사용하여 시험한 오픈 소스 클라이언트 라이브러리 및 샘플입니다.

  - [Java WSO2 ID 서버](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu 페더레이션](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect 기본 클라이언트](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [iOS OAuth2 클라이언트](https://github.com/nxtbgthng/OAuth2Client)
  - [Android OAuth2 클라이언트](https://github.com/wuman/android-oauth-client)
  - [Android OpenID Connect 클라이언트](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->