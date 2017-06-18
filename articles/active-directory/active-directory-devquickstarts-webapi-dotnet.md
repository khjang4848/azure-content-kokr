<properties
	pageTitle="Azure AD .NET 시작 | Microsoft Azure"
	description="인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET MVC Web API를 빌드하는 방법"
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


# Azure AD에서 전달자 토큰을 사용하여 Web API 보호

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

보호된 리소스에 액세스할 수 있는 응용 프로그램을 빌드하는 경우 허가되지 않은 액세스로부터 해당 리소스를 보호하는 방법을 알고 있어야 합니다. Azure AD를 사용하면 몇 개의 코드 줄만으로 단순하고 간편하게 OAuth Bearer 2.0 액세스 토큰을 사용하여 Web API를 보호할 수 있습니다.

Asp.NET 웹앱에서는 .NET Framework 4.5에 포함된 Microsoft에서 구현한 커뮤니티 기반 OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다. 여기서는 "할 일 목록" 웹 API를 작성하는 데 OWIN을 사용합니다.
-	보호되는 API를 지정합니다.
-	웹 API 호출에 유효한 액세스 토큰이 포함되어 있는지 확인합니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
3. To Do List Web API를 호출하도록 클라이언트 응용 프로그램 구성

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)하세요. 각 샘플은 Visual Studio 2013 솔루션입니다. 응용 프로그램을 등록할 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).


## *1. Azure AD에 응용 프로그램 등록*
응용 프로그램 보안을 유지하려면 먼저 테넌트에서 응용 프로그램을 만들고 몇 가지 중요 정보로 Azure AD를 제공해야 합니다.

-	[Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다. “To Do List Service"를 입력합니다.
    -	**리디렉션 Uri**는 Azure AD가 앱이 요청한 토큰을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 이 값으로 `https://localhost:44321/`을 입력합니다.
-	등록을 완료한 후 **구성** 탭으로 이동한 후 **앱 ID URI** 필드를 찾습니다. 이 값으로 테넌트별 식별자를 입력합니다(예: `https://contoso.onmicrosoft.com/TodoListService`).
- 구성을 저장합니다. 잠시 후에 클라이언트 응용 프로그램을 등록해야 하므로 포털을 열어둡니다.

## *2. OWIN 인증 파이프라인을 사용하도록 앱 설정*

Azure AD에 응용 프로그램을 등록했으므로 들어오는 요청 및 토큰의 유효성을 검사하기 위해 Azure AD와 통신할 수 있게 응용 프로그램을 설정해야 합니다.

-	시작하려면 솔루션을 열고 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 TodoListService 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-	OWIN Startup 클래스를 `Startup.cs`라는 TodoListService 프로젝트에 추가합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(…)` 메서드를 호출합니다.
-	클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(…)` 메서드에서 ConfgureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(…)` 메서드를 구현합니다. `WindowsAzureActiveDirectoryBearerAuthenticationOptions`에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-	이제 `[Authorize]` 특성을 사용하여 JWT 전달자 인증으로 컨트롤러 및 작업을 보호할 수 있습니다. authorize 태그를 사용하여 `Controllers\TodoListController.cs` 클래스를 데코레이팅합니다. 이렇게 하면 사용자는 해당 페이지에 액세스하기 전에 강제로 로그인됩니다.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 권한 있는 호출자가 `TodoListController` API 중 하나를 호출하면 작업은 호출자에 대한 정보에 액세스해야 합니다. OWIN은 `ClaimsPrincpal` 개체를 통해 전달자 토큰 내의 클레임에 액세스할 수 있도록 합니다.
- Web API에 대한 일반적인 요구 사항은 토큰에 있는 "범위"의 유효성을 검사하는 것입니다. 이렇게 하면 최종 사용자가 Todo List Service에 액세스하는 데 필요한 권한에 동의했는지 확인할 수 있습니다.

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-	마지막으로 TodoListService 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	`ida:Tenant`는 Azure AD 테넌트의 이름(예: “contoso.onmicrosoft.com”)입니다.
  -	`ida:Audience`는 Azure 포털에 입력한 응용 프로그램의 앱 ID URI입니다.

## *3. 클라이언트 응용 프로그램 구성 및 서비스 실행*
Todo List Service가 작동하는 것을 보려면 먼저 AAD에서 토큰을 가져오고 서비스를 호출할 수 있게 Todo List Client를 구성해야 합니다.

- [Azure 관리 포털](https://manage.windowsazure.com)로 다시 이동합니다.
- Azure AD 테넌트에 새 응용 프로그램을 만들고 결과 프롬프트에서 **네이티브 클라이언트 응용 프로그램**을 선택합니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 URI** 값으로 `http://TodoListClient/`를 입력합니다.
- 등록이 끝나면 AAD는 앱에 고유한 **클라이언트 ID**를 할당합니다. 이 값은 다음 단계에서 필요하므로 구성 탭에서 복사해둡니다.
- 또한 **구성** 탭에서 “다른 응용 프로그램에 대한 권한” 섹션을 찾습니다. “응용 프로그램 추가”를 클릭합니다. “표시” 드롭다운에서 “모든 앱”을 선택하고 위쪽 확인 표시를 클릭합니다. To Do List Service를 찾아 클릭하고 아래쪽 확인 표시를 클릭하여 응용 프로그램을 추가합니다. “위임된 권한” 드롭다운에서 "Access To Do List Service"를 선택하고 구성을 저장합니다.


- Visual Studio의 TodoListClient 프로젝트에서 `App.config`를 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	`ida:Tenant`는 Azure AD 테넌트의 이름(예: “contoso.onmicrosoft.com”)입니다.
  -	Azure 포털에서 복사한 `ida:ClientId` 앱 ID입니다.
  -	`todo:TodoListResourceId`는 Azure 포털에 입력한 To Do List Service 응용 프로그램의 앱 ID URI입니다.

마지막으로, 각 프로젝트를 정리하고 빌드한 후 실행합니다. 새 사용자를 아직 만들지 않았으면 *.onmicrosoft.com 도메인을 사용하여 테넌트에 새 사용자를 만들어야 합니다. 해당 사용자로 To Do List 클라이언트에 로그인하고 사용자의 To Do List에 일부 작업을 추가합니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)에 제공됩니다. 이제 추가 ID 시나리오로 이동할 수 있습니다.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->