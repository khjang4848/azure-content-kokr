<properties
	pageTitle="Azure AD Windows Phone 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 Windows Phone 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>



# Azure AD와 Windows Phone 앱 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Windows Phone 8.1 앱을 개발하는 경우 Azure AD를 사용하면 간단하고 편리하게 Active Directory 계정에 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

> [AZURE.NOTE] 이 코드 샘플에서는 ADAL v2.0을 사용합니다. 최신 기술의 경우 대신 [ADAL v3.0을 사용한 Windows 범용 자습서](active-directory-devquickstarts-windowsstore.md)를 시도하려고 할 수 있습니다. 실제로 Windows Phone 8.1용 앱을 빌드하는 경우에 적절합니다. ADAL v2.0을 완전히 지원하게 되면 Azure AD를 사용하여 Windows Phone 8.1에 대한 앱을 개발하는 경우에 권장합니다.

보호된 리소스에 액세스해야 하는 .NET 네이티브 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 이 작업이 얼마나 쉬운지 보여 주기 위해 다음 작업을 수행하는 "Directory Searcher" Windows Phone 8.1 앱을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	지정된 UPN을 가진 사용자를 디렉터리에서 검색합니다.
-	사용자를 로그아웃합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

2. Azure AD에 응용 프로그램을 등록합니다.
3. ADAL을 설치 및 구성합니다.
5. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [기본 프로젝트를 다운로드](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip)하세요. 각 샘플은 Visual Studio 2013 솔루션입니다. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## *1. Directory Searcher 응용 프로그램 등록*
앱에서 토큰을 가져올 수 있게 하려면 먼저 Azure AD 테넌트에 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다.

-	[Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 Uri**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 지금은 자리 표시자 값(예: `http://DirectorySearcher`)을 입력합니다. 이 값은 나중에 바꿀 것입니다.
-	등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 **구성** 탭에서 복사해둡니다.
- 또한 **구성** 탭에서 “다른 응용 프로그램에 대한 권한” 섹션을 찾습니다. "Azure Active Directory" 응용 프로그램의 경우 **위임된 권한**에서 **조직 디렉터리 액세스** 권한을 추가합니다. 이렇게 하면 응용 프로그램은 Graph API에서 사용자를 쿼리할 수 있습니다.

## *2. ADAL 설치 및 구성*
Azure AD에서 응용 프로그램이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.
-	먼저 패키지 관리자 콘솔을 사용하여 ADAL을 DirectorySearcher 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	DirectorySearcher 프로젝트에서 `MainPage.xaml.cs`를 엽니다. Azure 포털에 입력한 값을 반영하도록 `Config Values` 영역의 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
    -	`tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
    -	`clientId`는 포털에서 복사한 응용 프로그램의 clientId여야 합니다.
-	이제 Windows Phone 앱에 대한 콜백 uri를 검색해야 합니다. 이 줄의 `MainPage` 메서드에 중단점을 설정합니다.

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- 앱을 실행하고 중단점을 만나면 `redirectUri` 값을 따로 복사해 둡니다. 다음과 같이 표시됩니다.

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Azure 관리 포털에서 응용 프로그램의 **구성** 탭으로 다시 가서 **RedirectUri**의 값을 이 값으로 바꿉니다.

## *3. ADAL을 사용하여 AAD에서 토큰을 가져오기*
ADAL에서 확인되는 기본 원칙은 액세스 토큰이 필요할 때마다 앱이 `authContext.AcquireToken(…)`을 호출하고 나머지 작업은 ADAL이 수행한다는 것입니다.

-	첫 번째 단계는 앱의 `AuthenticationContext`, 즉 ADAL의 기본 클래스를 초기화하는 것입니다. 이 클래스에서 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달하고 토큰 캐시 방법을 알려줍니다.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- 이제 사용자가 앱의 UI에서 "검색" 단추를 클릭할 때 호출되는 `Search(...)` 메서드를 찾습니다. 이 메서드는 Azure AD Graph API에 해당 UPN이 지정된 검색어로 시작하는 사용자를 쿼리하라는 GET 요청을 만듭니다. 그렇지만 Graph API를 쿼리하려면 ADAL이 연결되는 요청의 `Authorization` 헤더에 access\_token을 포함해야 합니다.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- 대화형 인증이 필요한 경우 ADAL은 Windows Phone의 WAB(Web Authentication Broker) 및 [연속 모델](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/)을 사용하여 Azure AD 로그인 페이지를 표시합니다. 사용자가 로그인하는 경우 앱은 WAB 상호 작용의 결과를 ADAL에 전달해야 합니다. 이 작업은 `ContinueWebAuthentication` 인터페이스를 구현하는 것만큼 간단합니다.

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- 이제 ADAL이 앱에 반환한 `AuthenticationResult`를 사용할 것입니다. `QueryGraph(...)` 콜백에서 획득한 access\_token을 Authorization 헤더의 GET 요청에 연결합니다.

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- `AuthenticationResult` 개체를 사용하여 앱에 사용자에 대한 정보를 표시할 수도 있습니다. `QueryGraph(...)` 메서드에서 해당 결과를 사용하여 페이지에 사용자의 ID를 표시합니다.

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- 마지막으로 ADAL을 사용하여 응용 프로그램에서 사용자를 로그아웃할 수 있습니다. 여기서는 사용자가 "로그아웃" 단추를 클릭한 경우 다음 `AcquireTokenSilentAsync(...)` 호출이 실패하도록 하려고 합니다. ADAL을 사용하면 이 작업은 토큰 캐시를 지우는 것 만큼 쉽습니다.

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

축하합니다. 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 Windows Phone 앱이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. DirectorySearcher 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다. 앱을 닫았다가 다시 실행합니다. 사용자의 세션을 그대로 유지하는 방법을 알아두세요. 로그아웃했다가 다른 사용자로 다시 로그인합니다.

ADAL은 응용 프로그램에 이러한 모든 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `authContext.AcquireToken*(…)`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip)에 제공됩니다. 이제 추가 ID 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 .NET Web API 보안 유지 >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->