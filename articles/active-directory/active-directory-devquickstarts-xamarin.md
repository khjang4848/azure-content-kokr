<properties
	pageTitle="Azure AD Xamarin 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 Xamarin 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Xamarin 앱에 Azure AD 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin을 사용하면 iOS, Android 및 Windows(모바일 장치 및 PC)에서 실행할 수 있는 모바일 앱을 C#으로 작성할 수 있습니다. Xamarin을 사용하여 앱을 빌드하는 경우 Azure AD를 사용하면 간단하고 편리하게 Active Directory 계정에 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 Xamarin 앱의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 이 작업이 얼마나 쉬운지 보여 주기 위해 다음 작업을 수행하는 "Directory Searcher" 앱을 빌드해 보겠습니다.

-	iOS, Android, Windows 데스크톱, Windows Phone 및 Windows 스토어에서 실행됩니다.
- 단일 PCL(이식 가능 클래스 라이브러리)을 사용하여 사용자를 인증하고 Azure AD Graph API에 대한 토큰을 가져옵니다.
-	지정된 UPN을 가진 사용자를 디렉터리에서 검색합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

2. Xamarin 개발 환경 설정
2. Azure AD에 응용 프로그램을 등록합니다.
3. ADAL을 설치 및 구성합니다.
5. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [기본 프로젝트를 다운로드](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)하세요. 각 샘플은 Visual Studio 2013 솔루션입니다. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## *0. Xamarin 개발 환경 설정*
이 자습서에는 iOS, Android 및 Windows용 프로젝트가 포함되어 있으므로 Visual Studio와 Xamarin이 둘 다 필요합니다. 필요한 환경을 만들려면 MSDN에서 [Visual Studio 및 Xamarin 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)의 전체 지침을 따르세요. 이러한 지침에는 설치 관리자가 완료되기를 기다리는 동안 Xamarin에 대해 자세히 알아보기 위해 검토할 수 있는 자료가 포함되어 있습니다.

필요한 설정이 끝나면 Visual Studio에서 솔루션을 열어 시작합니다. 그러면 5개의 플랫폼별 프로젝트와 모든 플랫폼에서 공유되는 1개의 이식 가능 클래스 라이브러리(`DirectorySearcher.cs`)의 6개 프로젝트를 찾을 수 있습니다.

## *1. Directory Searcher 응용 프로그램 등록*
앱에서 토큰을 가져올 수 있게 하려면 먼저 Azure AD 테넌트에 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다.

-	[Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 Uri**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 값(예: `http://DirectorySearcher`)을 입력합니다.
-	등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 **구성** 탭에서 복사해둡니다.
- 또한 **구성** 탭에서 “다른 응용 프로그램에 대한 권한” 섹션을 찾습니다. "Azure Active Directory" 응용 프로그램의 경우 **위임된 권한**에서 **조직 디렉터리 액세스** 권한을 추가합니다. 이렇게 하면 응용 프로그램은 Graph API에서 사용자를 쿼리할 수 있습니다.

## *2. ADAL 설치 및 구성*
Azure AD에서 응용 프로그램이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.
-	먼저 패키지 관리자 콘솔을 사용하여 솔루션의 각 프로젝트에 ADAL을 추가합니다.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- ADAL의 PCL 부분과 플랫폼별 부분의 두 라이브러리 참조가 각 프로젝트에 추가됩니다.

-	DirectorySearcherLib 프로젝트에서 `DirectorySearcher.cs`를 엽니다. Azure 포털에 입력한 값을 반영하도록 클래스 멤버 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
    -	`tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
    -	`clientId`는 포털에서 복사한 응용 프로그램의 clientId여야 합니다.
    - `returnUri`는 포털에 입력한 redirectUri(예: `http://DirectorySearcher`)입니다.

## *3. ADAL을 사용하여 AAD에서 토큰을 가져오기*
*거의* 모든 앱 인증 논리가 `DirectorySearcher.SearchByAlias(...)`에 있습니다. 플랫폼별 프로젝트에서는 `DirectorySearcher` PCL에 컨텍스트 매개 변수를 전달하기만 하면 됩니다.

- 먼저 `DirectorySearcher.cs`를 열고 새 매개 변수를 `SearchByAlias(...)` 메서드에 추가합니다. `IPlatformParameters`는 ADAL이 인증을 수행하는 데 필요한 플랫폼별 개체를 캡슐화하는 컨텍스트 매개 변수입니다.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	다음으로, ADAL의 기본 클래스인 `AuthenticationContext`를 초기화합니다. 이 클래스에서 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달합니다. 그런 다음 `AcquireTokenAsync(...)`를 호출합니다. 이 메서드는 `IPlatformParameters` 개체를 수락하고 앱에 토큰을 반환하는 데 필요한 인증 흐름을 호출합니다.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`는 먼저 사용자에게 자격 증명을 요구하지 않고(이전 토큰을 캐시하거나 새로 고침) 요청된 리소스(이 경우 Graph API)에 대한 토큰을 반환하려고 합니다. 필요한 경우에만 요청된 토큰을 획득하기 전에 사용자에게 Azure AD 로그인 페이지를 표시합니다.


- 그러면 Authorization 헤더의 Graph API GET 요청에 액세스 토큰을 연결할 수 있습니다.

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

`DirectorySearcher` PCL 및 앱의 ID 관련 코드에 대한 작업이 끝났습니다. 이제 각 플랫폼 보기에서 `SearchByAlias(...)` 메서드를 호출하고, 필요한 경우 UI 수명 주기를 올바르게 처리하기 위한 코드를 추가하면 됩니다.

####Android:
- `MainActivity.cs`의 단추 클릭 처리기에서 `SearchByAlias(...)` 호출을 추가합니다.

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- 또한 `OnActivityResult` 수명 주기 메서드를 재정의하여 인증 리디렉션을 해당 메서드로 다시 전달해야 합니다. ADAL은 Android에서 이 작업을 위한 도우미 메서드를 제공합니다.

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Windows 데스크톱:
- `MainWindow.xaml.cs`에서 데스크톱의 `PlatformParameters` 개체에 `WindowInteropHelper`를 전달하여 `SearchByAlias(...)`를 호출하면 됩니다.

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- `DirSearchClient_iOSViewController.cs`에서 iOS `PlatformParameters` 개체는 단순히 보기 컨트롤러를 참조합니다.

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows 유니버설:
- Windows 유니버설에서 `MainPage.xaml.cs`를 열고 `Search` 메서드를 구현합니다. 이 메서드는 필요에 따라 공유 프로젝트에서 도우미 메서드를 사용하여 UI를 업데이트합니다.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

축하합니다. 이제 5개의 서로 다른 플랫폼에서 OAuth 2.0을 사용하여 사용자를 인증하고 안전 하 게 Web API를 호출하는 기능이 있는 Xamarin 앱이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. DirectorySearcher 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다.

ADAL은 앱에 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `authContext.AcquireToken*(…)`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)에 제공됩니다. 이제 추가 ID 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 .NET Web API 보안 유지 >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->