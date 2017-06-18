<properties
	pageTitle="Azure AD .NET 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되는 .NET MVC 웹앱을 빌드하는 방법"
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

# Azure AD를 사용하는 웹앱 로그인 및 로그아웃

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD를 사용하면 단순하고 간편하게 웹앱의 ID 관리를 아웃소싱하고 몇 개의 코드 줄만으로 단일 로그인 및 로그아웃을 제공할 수 있습니다. Asp.NET 웹앱에서는 .NET Framework 4.5에 포함된 Microsoft에서 구현한 커뮤니티 기반 OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다. OWIN을 사용하는 경우는 다음과 같습니다.
-	ID 공급자로 Azure AD를 사용하여 사용자를 앱에 로그인합니다.
-	사용자에 대한 일부 정보를 표시합니다.
-	앱에서 사용자를 로그아웃합니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
3. OWIN을 사용하여 Azure AD에 로그인 및 로그아웃 요청을 실행합니다.
4. 사용자에 대한 데이터를 출력합니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)하세요. 응용 프로그램을 등록할 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## *1. Azure AD에 응용 프로그램 등록*
앱에서 사용자를 인증할 수 있게 하려면 먼저 새 응용 프로그램을 테넌트에 등록해야 합니다.

- Azure 관리 포털에 로그인합니다.
- 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
- 응용 프로그램을 등록할 테넌트를 선택합니다.
- **응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 추가를 클릭합니다.
- 프롬프트에 따라 새 **웹 응용 프로그램 및/또는 WebAPI**를 만듭니다.
    - 응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**로그온 URL**은 앱의 기본 URL입니다. 기본값은 `https://localhost:44320/`입니다.
    - **앱 ID URI**는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>`(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)을 사용하는 것입니다.
- 등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 구성 탭에서 복사해둡니다.

## *2. OWIN 인증 파이프라인을 사용하도록 앱 설정*
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 OWIN 미들웨어를 구성합니다. OWIN은 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	시작하려면 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	`Startup.cs` 프로젝트에 OWIN Startup 클래스를 추가하고, 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(...)` 메서드를 호출합니다.
-	클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(...)` 메서드에서 ConfgureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(...)` 메서드를 구현합니다. `OpenIDConnectAuthenticationOptions`에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다. 보이지 않지만 OpenID Connect 미들웨어는 쿠키를 사용하므로 쿠키 인증도 설정해야 합니다.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	마지막으로 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
    -	앱의 `ida:ClientId`는 1단계의 Azure 포털에서 복사한 Guid입니다.
    -	`ida:Tenant`는 Azure AD 테넌트의 이름(예: “contoso.onmicrosoft.com”)입니다.
    -	`ida:PostLogoutRedirectUri`는 로그아웃 요청이 성공적으로 완료된 후 사용자가 리디렉션되는 Azure AD를 나타냅니다.

## *3. OWIN을 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행*
이제 앱은 OpenID Connect 인증 프로토콜을 사용하여 Azure AD와 통신하도록 올바르게 구성되었습니다. OWIN이 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 까다로운 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인하고 로그아웃하는 방법을 알려주기만 하면 됩니다.

- 컨트롤러에서 권한 부여 태그를 사용하여 사용자가 특정 페이지에 액세스하기 전에 로그인하도록 요구할 수 있습니다. `Controllers\HomeController.cs`를 열고 About 컨트롤러에 `[Authorize]` 태그를 추가합니다.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	또한 OWIN을 사용하여 코드 내에서 직접 인증 요청을 실행할 수도 있습니다. `Controllers\AccountController.cs`을(를) 엽니다. SignIn() 및 SignOut() 작업에서 각각 OpenID Connect 챌린지 및 로그아웃 요청을 실행합니다.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	이제 `Views\Shared\_LoginPartial.cshtml`을 엽니다. 여기서 사용자에게 앱 로그인 및 로그아웃 링크를 표시하고 보기에 사용자 이름을 출력하게 됩니다.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4. 사용자 정보 표시*
OpenID Connect로 사용자를 인증할 때 Azure AD는 "클레임" 또는 사용자에 대한 어설션을 포함하는 id\_token을 응용 프로그램에 반환합니다. 이러한 클레임을 사용하여 앱 개인 설정을 수행할 수 있습니다.

- `Controllers\HomeController.cs` 파일을 엽니다. `ClaimsPrincipal.Current` 보안 주체 개체를 통해 컨트롤러의 사용자 클레임에 액세스할 수 있습니다.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

마지막으로 앱을 빌드하고 실행합니다. 새 사용자를 아직 만들지 않았으면 .onmicrosoft.com 도메인을 사용하여 테넌트에 새 사용자를 만들어야 합니다. 해당 사용자로 로그인하고 위쪽 탐색 모음에 사용자 ID가 반영되는 방식을 확인합니다. 로그아웃하고 테넌트의 다른 사용자로 다시 로그인합니다. 관심이 있으면 이 응용 프로그램의 다른 인스턴스를 등록 및 실행하고(자체 clientId 사용) SSO(Single Sign-On) 작동을 살펴봅니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)에 제공됩니다.

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 Web API 보안 유지 >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->