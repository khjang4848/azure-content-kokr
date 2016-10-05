<properties
   pageTitle="Azure Active Directory 코드 샘플 | Microsoft Azure"
   description="시나리오별로 구성된 Azure Active Directory 코드 샘플의 인덱스입니다."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# Azure Active Directory 코드 샘플

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Microsoft Azure AD(Azure Active Directory)를 사용하여 웹 응용 프로그램 및 Web API에 인증 및 권한 부여를 추가할 수 있습니다. 이 섹션에는 이러한 작업 수행 방법을 보여 주는 코드 샘플과 응용 프로그램에 사용할 수 있는 코드 조각의 링크가 나와 있습니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 또한 코드는 중요한 섹션을 이해하는 데 도움이 되도록 주석으로 처리되어 있습니다.

각 샘플 유형식에 대한 기본 시나리오를 이해하려면 Azure AD의 인증 시나리오를 참조하세요.

GitHub에서 샘플에 참가: [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory).

## 웹 브라우저-웹 응용 프로그램

다음 샘플에서는 사용자의 브라우저가 사용자를 Azure AD에 로그인하도록 지시하는 웹 응용 프로그램을 작성하는 방법을 보여 줍니다.



| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | OpenID Connect(ASP.Net OpenID Connect OWIN 미들웨어)를 사용하여 Azure AD 테넌트의 사용자를 인증합니다.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | OpenID Connect(ASP.Net OpenID Connect OWIN 미들웨어)를 사용하여 여러 Azure AD 테넌트의 사용자를 인증하는 다중 테넌트 .NET MVC 웹 응용 프로그램입니다.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | WS-Federation(ASP.Net WS-Federation OWIN 미들웨어)을 사용하여 Azure AD 테넌트의 사용자를 인증합니다.






## SPA(단일 페이지 응용 프로그램)

이 샘플에서는 Azure AD로 보안이 설정된 단일 페이지 응용 프로그램을 작성하는 방법을 보여 줍니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | JavaScript용 ADAL 및 Azure AD를 사용하여 ASP.NET 웹 API 백 엔드로 구현된 AngularJS 기반 단일 페이지 앱의 보안을 유지합니다.


## 네이티브 응용 프로그램-Web API

다음 코드 샘플에서는 Azure AD로 보안이 설정된 Web API를 호출하는 네이티브 클라이언트 응용 프로그램을 빌드하는 방법을 보여 줍니다. [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md) 및 [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)이 사용됩니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | 웹 API를 호출하고 인증을 위해 Azure AD를 사용하는 Apache Cordova 앱을 빌드하는 데 Apache Cordova용 ADAL 플러그인을 사용합니다.
| C#/.NET | [NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Azure AD를 사용하여 보안되는 웹 API를 호출하는 .NET WPF 응용 프로그램입니다.
| C#/.NET | [NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Azure AD를 사용하여 보안되는 웹 API를 호출하는 Windows 스토어 응용 프로그램입니다.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Azure AD를 사용하여 보안되는 다중 테넌트 웹 API를 호출하는 Windows 스토어 응용 프로그램입니다.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | 웹 API를 호출하는 네이티브 클라이언트 응용 프로그램으로, 원래 사용자를 대신하여 토큰을 가져온 다음 이 토큰을 사용하여 다른 웹 API를 호출합니다.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Azure AD로 보안되는 웹 API를 호출하는 Windows Phone 8.1용 Windows 스토어 응용 프로그램입니다.
| ObjC | [NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) | 인증을 위해 Azure AD를 필요로 하는 웹 API를 호출하는 iOS 응용 프로그램입니다.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | OWIN 미들웨어를 사용하는 대신 웹 API에 JWT 토큰 처리를 위한 논리가 포함된 네이티브 클라이언트 응용 프로그램입니다.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Android 라이브러리에 대해 네이티브 Azure ADAL(AD 인증 라이브러리)에 바인딩하는 Xamarin입니다.
| C#/Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | iOS에 대해 네이티브 Azure ADAL(AD 인증 라이브러리)에 바인딩하는 Xamarin입니다.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | 5개의 플랫폼을 대상으로 하며 Azure AD로 보안되는 웹 API를 호출하는 Xamarin 프로젝트입니다.
| C#/.NET | [NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | 비대화형 인증을 수행하고 Azure AD로 보안되는 웹 API를 호출하는 네이티브 응용 프로그램입니다.



## 웹 응용 프로그램-Web API

다음 코드 샘플에서는 [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD로 보안되는 웹 API를 호출하는 웹 응용 프로그램을 빌드하는 방법을 보여 줍니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | 로그인한 사용자의 권한으로 웹 API를 호출합니다.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | 응용 프로그램의 권한으로 웹 API를 호출합니다.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)를 통해 기존 웹 응용 프로그램에 권한 부여를 추가하여 이 웹 응용 프로그램이 웹 API를 호출할 수 있도록 합니다.
| JavaScript | [WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | API 보호를 위해 Azure AD와 통합된 REST API 서비스를 설정합니다. Web API를 사용하여 Node.js 서버를 포함합니다.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) | Azure AD 테넌트의 사용자를 인증하는 데 OpenID Connect(ASP.Net OpenID Connect OWIN 미들웨어)를 사용하는 다중 테넌트 MVC 웹 응용 프로그램입니다. 인증 코드를 사용하여 Graph API를 호출합니다.

## 서버 또는 디먼 응용 프로그램-Web API

다음 코드 샘플에서는 [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md) 및 [Azure AD의 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 웹 API에서 리소스를 가져오는 디먼 또는 서버 응용 프로그램을 빌드하는 방법을 보여 줍니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | 웹 API를 호출하는 콘솔 응용 프로그램입니다. 클라이언트 자격 증명은 암호입니다.
| C#/.NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | 웹 API를 호출하는 콘솔 응용 프로그램입니다. 클라이언트 자격 증명은 인증서입니다.


## Azure AD Graph API 호출

다음 코드 샘플에서는 디렉터리 데이터 읽고 쓰기 위해 Azure AD Graph API를 호출하는 응용 프로그램을 빌드하는 방법을 보여 줍니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Graph API를 사용하여 Azure AD 디렉터리 데이터에 액세스하는 웹 응용 프로그램입니다.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Graph API를 사용하여 Azure AD 디렉터리 데이터에 액세스하는 웹 응용 프로그램입니다.
| C#/.NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Graph API를 사용하여 Azure AD 디렉터리 데이터에 액세스하는 웹 응용 프로그램입니다.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | 이 콘솔 앱은 Graph API에 대한 일반적인 읽기 및 쓰기 호출을 시연하며 사용자 라이선스 할당을 실행하고 사용자의 축소판 사진 및 링크를 업데이트하는 방법을 보여 줍니다.
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Graph API에서 차등 쿼리를 사용하여 Azure AD 테넌트의 사용자 개체에 대한 주기적인 변경 사항을 가져오는 콘솔 응용 프로그램입니다.
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Graph API 쿼리를 사용하여 간단한 회사 조직도를 생성하는 MVC 응용 프로그램입니다.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Graph API를 호출하여 확장을 등록한 다음 이 확장 특성의 값을 읽고 업데이트하고 삭제하는 PHP 응용 프로그램입니다.


## 권한 부여

다음 코드 샘플에서는 권한 부여에 Azure AD를 사용하는 방법을 보여 줍니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Azure AD와 통합된 응용 프로그램에서 Azure Active Directory 그룹 클레임을 사용하여 RBAC(역할 기반 액세스 제어)를 수행합니다.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD와 통합된 응용 프로그램에서 Azure Active Directory 응용 프로그램 역할을 사용하여 RBAC(역할 기반 액세스 제어)를 수행합니다.


## 레거시 연습

다음 연습에서는 다소 오래된 기술을 사용하지만 여전히 유용할 수 있습니다.

| 언어/플랫폼 | 샘플 | 설명
| ----------------- | ------ | -----------
| C#/.NET | [Microsoft Azure AD 응용 프로그램의 역할 기반 및 ACL 기반 권한 부여](http://go.microsoft.com/fwlink/?LinkId=331694) | Azure AD와 통합된 응용 프로그램에서 RBAC(역할 기반 액세스 제어) 및 ACL 기반 권한 부여를 수행합니다.
| C#/.NET | [AAL - Windows 스토어 앱-REST 서비스 - 인증](http://go.microsoft.com/fwlink/?LinkId=330605) | Windows Store 베타용 [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md)(이전의 AAL)을 사용하여 Windows 스토어 앱에 사용자 인증 기능을 추가합니다.
| C#/.NET | [ADAL - 네이티브 앱-REST 서비스 - 브라우저 대화 상자를 통해 AAD를 사용한 인증](http://go.microsoft.com/fwlink/?LinkId=259814) | [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md)을 사용하여 WPF 클라이언트에 사용자 인증 기능을 추가합니다.
| C#/.NET | [ADAL - 네이티브 앱-REST 서비스 - 브라우저 대화 상자를 통해 ACS를 사용한 인증](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md) 및 [ACS(액세스 제어 서비스 2.0)](http://msdn.microsoft.com/library/azure/hh147631.aspx)를 사용하여 WPF 클라이언트에 사용자 인증 기능을 추가합니다.
| C#/.NET | [ADAL - 서버 간 인증](http://go.microsoft.com/fwlink/?LinkId=259816) | [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md)을 사용하여 서버 쪽 프로세스에서 MVC4 웹 API REST 서비스로 이루어지는 서비스 호출의 보안을 유지합니다.
| C#/.NET | [Azure AD를 사용하여 웹 응용 프로그램에 로그온 추가](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Azure AD 엔터프라이즈 디렉터리에 대해 웹 Single Sign On을 수행하도록 .NET 응용 프로그램을 구성합니다.
| C#/.NET | [Azure AD를 사용하여 다중 테넌트 웹 응용 프로그램 개발](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Azure AD를 사용하여 Single Sign On 및 여러 조직에서 작동하는 단일 .NET 응용 프로그램의 디렉터리 액세스 기능을 추가합니다.
JAVA | [Azure AD Graph API용 Java 샘플 앱](http://go.microsoft.com/fwlink/?LinkId=263969) | Graph API를 사용하여 Azure AD에서 디렉터리에 데이터에 액세스합니다.
PHP | [Azure AD Graph API용 PHP 샘플 앱](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Graph API를 사용하여 Azure AD에서 디렉터리에 데이터에 액세스합니다.
| C#/.NET | [Azure AD Graph API용 샘플 앱](http://go.microsoft.com/fwlink/?LinkID=262648) | Graph API를 사용하여 Azure AD에서 디렉터리에 데이터에 액세스합니다.
| C#/.NET | [Azure AD Graph 차등 쿼리용 샘플 앱](http://go.microsoft.com/fwlink/?LinkId=275398) | Graph API에서 차등 쿼리를 사용하여 Azure AD 테넌트의 사용자 개체에 대한 주기적인 변경 사항을 가져옵니다.
| C#/.NET | [Azure AD에 대해 다중 테넌트 클라우드 응용 프로그램을 통합하는 샘플 앱](http://go.microsoft.com/fwlink/?LinkId=275397) | 다중 테넌트 응용 프로그램을 Azure AD에 통합합니다.
| C#/.NET | [Azure AD를 사용하여 Windows 스토어 응용 프로그램 및 REST 웹 서비스 보안](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Azure AD 및 [Azure ADAL(AD 인증 라이브러리)](active-directory-authentication-libraries.md)을 사용하여 간단한 웹 API 리소스 및 Windows 스토어 클라이언트 응용 프로그램을 만듭니다.
| C#/.NET| [Graph API를 사용하여 Azure AD 쿼리](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Azure AD Graph API를 사용하여 Azure AD 테넌트 디렉터리의 데이터에 액세스하도록 Microsoft .NET 응용 프로그램을 구성합니다.

## 참고 항목

##### 기타 리소스

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

[Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

<!---HONumber=AcomDC_0921_2016-->