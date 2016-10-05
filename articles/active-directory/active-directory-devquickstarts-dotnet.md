<properties
	pageTitle="Azure AD .NET 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 .NET Windows Desktop 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Windows Desktop WPF 앱에 Azure AD 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

데스크톱 응용 프로그램을 개발하는 경우 Azure AD를 사용하면 간단하고 편리하게 Active Directory 계정에 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 .NET 네이티브 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 .NET WPF To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	지정된 별칭을 가진 사용자를 디렉터리에서 검색합니다.
-	사용자를 로그아웃합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

2. Azure AD에 응용 프로그램을 등록합니다.
3. ADAL을 설치 및 구성합니다.
5. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)하세요. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## *1. DirectorySearcher 응용 프로그램 등록*
앱에서 토큰을 가져올 수 있게 하려면 먼저 Azure AD 테넌트에 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다.

-	Azure 관리 포털에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 Uri**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `http://DirectorySearcher`).
-	등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 **구성** 탭에서 복사해둡니다.
- 또한 **구성** 탭에서 “다른 응용 프로그램에 대한 권한” 섹션을 찾습니다. "Azure Active Directory" 응용 프로그램의 경우 **위임된 권한**에서 **조직 디렉터리 액세스** 권한을 추가합니다. 이렇게 하면 응용 프로그램은 Graph API에서 사용자를 쿼리할 수 있습니다.

## *2. ADAL 설치 및 구성*
Azure AD에서 응용 프로그램이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.
-	먼저 패키지 관리자 콘솔을 사용하여 ADAL을 DirectorySearcher 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	DirectorySearcher 프로젝트에서 `app.config`를 엽니다. Azure 포털에 입력한 값을 반영하도록 `<appSettings>` 섹션의 요소 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
    -	`ida:Tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
    -	`ida:ClientId`는 포털에서 복사한 응용 프로그램의 clientId여야 합니다.
    -	`ida:RedirectUri`는 포털에 등록한 리디렉션 url입니다.

## *3. ADAL을 사용하여 AAD에서 토큰을 가져오기*
ADAL에서 확인되는 기본 원칙은 액세스 토큰이 필요할 때마다 앱이 `authContext.AcquireTokenAsync(...)`을 호출하고 나머지 작업은 ADAL이 수행한다는 것입니다.

-	`DirectorySearcher` 프로젝트에서 `MainWindow.xaml.cs`를 열고 `MainWindow()` 메서드를 찾습니다. 첫 번째 단계는 앱의 `AuthenticationContext`, 즉 ADAL의 기본 클래스를 초기화하는 것입니다. 이 클래스에서 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달하고 토큰 캐시 방법을 알려줍니다.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- 이제 사용자가 앱의 UI에서 "검색" 단추를 클릭할 때 호출되는 `Search(...)` 메서드를 찾습니다. 이 메서드는 Azure AD Graph API에 해당 UPN이 지정된 검색어로 시작하는 사용자를 쿼리하라는 GET 요청을 만듭니다. 그렇지만 Graph API를 쿼리하려면 ADAL이 연결되는 요청의 `Authorization` 헤더에 access\_token을 포함해야 합니다.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- 앱이 `AcquireTokenAsync(...)`을 호출하여 토큰을 요청하면 ADAL은 사용자에게 자격 증명을 요구하지 않고 토큰을 반환하려고 합니다. ADAL은 사용자가 토큰을 가져오기 위해 로그인해야 한다고 판단할 경우 로그인 대화 상자를 표시하고, 사용자의 자격 증명을 수집하고, 인증 성공 시 토큰을 반환합니다. 어떤 이유로든 ADAL이 토큰을 반환할 수 없는 경우 `AdalException`을 발생합니다.
- `AuthenticationResult` 개체에는 사용자 앱에 필요할 수 있는 정보를 수집하는 데 사용할 수 있는 `UserInfo` 개체가 포함되어 있습니다. DirectorySearcher에서 `UserInfo`는 사용자 ID로 앱 UI를 사용자 지정하는 데 사용됩니다.

- 여기서는 사용자가 "로그아웃" 단추를 클릭한 경우 다음 `AcquireTokenAsync(...)` 호출 시 사용자에게 로그인하라는 메시지가 표시되도록 하려고 합니다. ADAL을 사용하면 이 작업은 토큰 캐시를 지우는 것 만큼 쉽습니다.

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- 그러나 사용자가 "로그아웃" 단추를 클릭하지 않으면 다음에 DirectorySearcher를 실행할 때 사용자 세션이 유지되도록 하려고 합니다. 앱이 시작되면 ADAL의 토큰 캐시에서 기존 토큰이 있는지 확인하고 그에 따라 UI를 업데이트할 수 있습니다. `CheckForCachedToken()` 메서드에서 `AcquireTokenAsync(...)`를 한 번 더 호출하면 이번에는 `PromptBehavior.Never` 매개 변수를 제공합니다. `PromptBehavior.Never`는 사용자가 로그인하라는 메시지를 표시하지 않도록 ADAL에 지시하고, 대신 토큰을 반환할 수 없는 경우 ADAL에서는 예외를 발생해야 합니다.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

축하합니다. 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 .NET WPF 응용 프로그램이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. DirectorySearcher 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다. 앱을 닫았다가 다시 실행합니다. 사용자의 세션을 그대로 유지하는 방법을 알아두세요. 로그아웃했다가 다른 사용자로 다시 로그인합니다.

ADAL은 응용 프로그램에 이러한 모든 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `authContext.AcquireTokenAsync(...)`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)에 제공됩니다. 이제 추가 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 .NET Web API 보안 유지 >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->