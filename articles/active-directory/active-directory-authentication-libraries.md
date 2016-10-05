<properties
   pageTitle="Azure Active Directory 인증 라이브러리 | Microsoft Azure"
   description="Azure AD 인증 라이브러리(ADAL)는 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 Active Directory(AD)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# Azure Active Directory 인증 라이브러리

ADAL(Azure AD 인증 라이브러리)은 클라이언트 응용 프로그램 개발자들이 클라우드 또는 온-프레미스 AD(Active Directory)에 쉽게 사용자를 인증하고 API 호출 보안을 위한 액세스 토큰을 가져올 수 있게 합니다. ADAL에는 비동기 지원, 액세스 토큰 및 새로고침 토큰을 저장하는 구성 가능한 토큰 캐시, 액세스 토큰이 만료되고 새로고침 토큰을 사용할 수 있을 때 자동 토큰 새로고침 등, 개발자들의 인증을 쉽게 지원하는 많은 기능이 있습니다. 대부분의 복잡성을 처리함으로써 ADAL은 개발자가 응용 프로그램의 비즈니스 논리에 초점을 맞추고 보안 전문가가 아니더라도 리소스를 쉽게 보호할 수 있게 도와줍니다.

ADAL은 다양한 플랫폼에서 사용할 수 있습니다.

|플랫폼|패키지 이름|클라이언트/서버|다운로드|소스 코드|설명서 및 샘플|
|---|---|---|---|---|---|
|.NET 클라이언트, Windows 스토어, Windows Phone(8.1)|.NET용 Active Directory 인증 라이브러리(ADAL)|클라이언트|[Microsoft.IdentityModel.Clients.ActiveDirectory(NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[.NET용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[설명서](https://msdn.microsoft.com/library/azure/mt417579.aspx)|
|JavaScript|JavaScript용 ADAL(Azure AD 인증 라이브러리)|클라이언트|[JavaScript용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[JavaScript용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|샘플: [SinglePageApp-DotNet(Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Objective-C용 ADAL(Azure AD 인증 라이브러리)|클라이언트|[Objective-C용 ADAL(CocoaPods)](https://cocoapods.org/?q=adal%20io)|[Objective-C용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|샘플: [NativeClient-iOS(Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Android용 ADAL(Azure AD 인증 라이브러리)|클라이언트|[Android용 ADAL(중앙 리포지토리)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[Android용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|샘플: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Node.js용 ADAL(Azure AD 인증 라이브러리)|클라이언트|[Node.js용 ADAL(npm)](https://www.npmjs.com/package/adal-node)|[Node.js용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|샘플: [WebAPI-Nodejs(Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|노드에 대한 Microsoft Azure Active Directory Passport 인증 미들웨어|클라이언트|[Node.js용 Azure Active Directory Passport(npm)](https://www.npmjs.com/package/passport-azure-ad)|[Node.js용 Azure Active Directory(Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Java용 ADAL(Azure AD 인증 라이브러리)|클라이언트|[Java용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[Java용 ADAL(Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Microsoft.NET Framework 4.5용 ID 프로토콜 확장|서버|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[.NET용 Azure AD ID 모델 확장(Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Microsoft .Net Framework 4.5용 JSON 웹 토큰 처리기|서버|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[.NET용 Azure AD ID 모델 확장(Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|응용 프로그램이 Microsoft의 인증 기술을 사용할 수 있게 해주는 OWIN 미들웨어입니다.|서버|[Microsoft.Owin.Security.ActiveDirectory(NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN(CodePlex)](http://katanaproject.codeplex.com)||
|.NET|응용 프로그램이 인증에 OpenIDConnect를 사용할 수 있게 해주는 OWIN 미들웨어입니다.|서버|[Microsoft.Owin.Security.OpenIdConnect(NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN(CodePlex)](http://katanaproject.codeplex.com)|샘플: [WebApp-OpenIDConnecty-DotNet(Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|응용 프로그램이 인증에 WS-Federation을 사용할 수 있게 해주는 OWIN 미들웨어입니다.|서버|[Microsoft.Owin.Security.WsFederation(NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN(CodePlex)](http://katanaproject.codeplex.com)|샘플: [WebApp-WSFederation-DotNet(Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## 시나리오

ADAL을 인증에 사용할 수 있는 3가지 공통 시나리오는 다음과 같습니다.

### 원격 리소스에 클라이언트 응용 프로그램의 사용자를 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 WPF 응용 프로그램과 같은 클라이언트를 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, ADAL을 사용하여 인증 경험을 ADAL에 위임하거나 사용자 자격 증명을 명시적으로 처리함으로써 Azure AD와의 인증을 수월하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 사용자를 인증하고 Azure AD에서 액세스 토큰 및 새로고침 토큰을 얻은 다음 액세스 토큰을 사용하여 웹 API에 요청할 수 있습니다.

Azure AD에 대한 인증을 사용하여 이 시나리오를 설명하는 코드 샘플의 경우 [웹 API에 네이티브 클라이언트 WPF 응용 프로그램](https://github.com/azureadsamples/nativeclient-dotnet)을 참조하세요.

### 원격 리소스에 서버 응용 프로그램 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 서비스 호출 방법을 알고 있으며 웹 API가 사용하는 Azure AD 테넌트를 알고 있습니다. 결과적으로, 개발자는 ADAL을 사용하여 응용 프로그램의 자격 증명을 명시적으로 처리함으로써 Azure AD로 인증을 용이하게 지원할 수 있습니다. ADAL을 사용하면 쉽게 응용 프로그램의 클라이언트 자격 증명을 사용하여 Azure AD에서 토큰을 점검한 다음 해당 토큰을 사용하여 웹 API에 요청할 수 있습니다. ADAL은 또한 액세스 토큰을 캐시화하고 필요한 대로 갱신하여 액세스 토큰의 수명 관리도 처리합니다. 이 시나리오를 보여 주는 코드 샘플에 대해서는 [웹 API에 콘솔 응용 프로그램](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

### 사용자를 대신해서 서버 응용 프로그램이 원격 리소스에 액세스할 수 있도록 인증

이 시나리오에서는 개발자가 웹 API와 같이 Azure AD에서 보호하는 원격 리소스에 액세스해야 하는 서버에서 실행 중인 응용 프로그램을 가지고 있습니다. 이것은 또한 Azure AD에서 사용자 대신에 요청되어야 합니다. 개발자는 Azure 구독을 가지고 있고 다운스트림 웹 API 호출 방법을 알고 있으며 서비스가 사용하는 Azure AD 테넌트를 알고 있습니다. 사용자가 웹 응용 프로그램에 인증되면 응용 프로그램이 Azure AD로부터 사용자에 대한 인증 코드를 가져올 수 있습니다. 그러면 웹 응용 프로그램에서 ADAL을 사용하여 Azure AD의 응용 프로그램과 연결된 클라이언트 자격 증명과 인증 코드를 사용하는 사용자 대신 액세스 토큰과 새로고침 토큰을 얻을 수 있습니다. 웹 응용 프로그램을 액세스 토큰에서 소유하게 되면 토큰이 만료될 때까지 웹 API를 호출할 수 있습니다. 토큰이 만료되면 웹 응용 프로그램이 ADAL을 사용하여 이전에 받은 새로고침 토큰을 사통해 새 액세스 토큰을 가져올 수 있습니다.


## 참고 항목

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

[Azure Active directory 인증 시나리오](active-directory-authentication-scenarios.md)

[Azure Active Directory 코드 샘플](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->