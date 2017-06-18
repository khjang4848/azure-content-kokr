<properties
	pageTitle="Azure AD v2.0 .NET 웹앱 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다로 사용자를 로그인하는 .NET MVC Web 웹앱을 빌드하는 방법입니다."
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

# .NET MVC 웹앱에 로그인 추가

v2.0 끝점을 사용하면 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 지원하는 인증을 웹앱에 빠르게 추가할 수 있습니다. Asp.NET 웹앱에서는 .NET Framework 4.5에 포함된 Microsoft OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

 여기서는 OWIN을 사용하여 사용자를 로그인하고, 사용자에 관한 일부 정보를 표시하고, 사용자를 앱에서 로그아웃하는 웹앱을 만듭니다.
 
 ## 다운로드 이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

전체 앱은 이 자습서 마지막 부분에서도 제공됩니다.

## 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.
- 앱에 대한 **웹** 플랫폼을 추가합니다.
- 올바른 **리디렉션 URI**를 입력합니다. 리디렉션 URI는 인증 응답을 보내야 하는 Azure AD를 나타냅니다. 이 자습서에 대한 기본값은 `https://localhost:44326/`입니다.

## OWIN 인증 설치 및 구성
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 OWIN 미들웨어를 구성합니다. OWIN은 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	먼저 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 앱의 구성 값을 입력합니다.
    -	`ida:ClientId`은(는) 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.
    -	`ida:RedirectUri`은(는) 포털에서 입력한 **리디렉션 URI**입니다.

-	그 다음, 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	`Startup.cs` 프로젝트에 OWIN Startup 클래스를 추가하고, 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(...)` 메서드를 호출합니다.
-	클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(...)` 메서드에서 ConfigureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
	public partial class Startup
	{
		public void Configuration(IAppBuilder app)
		{
			ConfigureAuth(app);
		}
	}
}
```

-	`App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(...)` 메서드를 구현합니다. `OpenIdConnectAuthenticationOptions`에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다. 보이지 않지만 OpenID Connect 미들웨어는 쿠키를 사용하므로 쿠키 인증도 설정해야 합니다.

```C#
public void ConfigureAuth(IAppBuilder app)
			 {
					 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

					 app.UseCookieAuthentication(new CookieAuthenticationOptions());

					 app.UseOpenIdConnectAuthentication(
							 new OpenIdConnectAuthenticationOptions
							 {
									 // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
									 // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
									 // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

									 ClientId = clientId,
									 Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
									 RedirectUri = redirectUri,
									 Scope = "openid email profile",
									 ResponseType = "id_token",
									 PostLogoutRedirectUri = redirectUri,
									 TokenValidationParameters = new TokenValidationParameters
									 {
											 ValidateIssuer = false,
									 },
									 Notifications = new OpenIdConnectAuthenticationNotifications
									 {
											 AuthenticationFailed = OnAuthenticationFailed,
									 }
							 });
			 }
```

## 인증 요청 보내기
이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 v2.0 끝점 끝점과 통신하도록 올바르게 구성되었습니다. OWIN이 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 까다로운 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인하고 로그아웃하는 방법을 알려주기만 하면 됩니다.

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

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-	이제 `Views\Shared\_LoginPartial.cshtml`을 엽니다. 여기서 사용자에게 앱 로그인 및 로그아웃 링크를 표시하고 보기에 사용자 이름을 출력하게 됩니다.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## 사용자 정보 표시
OpenID Connect로 사용자를 인증할 때 v2.0 끝점은 [클레임](active-directory-v2-tokens.md#id_tokens) 또는 사용자에 대한 어설션을 포함하는 id\_token을 앱에 반환합니다. 이러한 클레임을 사용하여 앱 개인 설정을 수행할 수 있습니다.

- `Controllers\HomeController.cs` 파일을 엽니다. `ClaimsPrincipal.Current` 보안 주체 개체를 통해 컨트롤러의 사용자 클레임에 액세스할 수 있습니다.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## 실행

마지막으로 앱을 빌드하고 실행합니다. 개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인하고 위쪽 탐색 모음에 사용자 ID가 반영되는 방식을 확인합니다. 이제 개인 및 회사/학교 계정으로 사용자를 인증할 수 있는 업계 표준 프로토콜을 사용하여 웹앱이 보안되었습니다.

참조를 위해 완료된 샘플(사용자 구성 값 제외)이 [여기에 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## 다음 단계

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[v2.0 끝점을 사용하여 웹 API 보안 유지>>](active-directory-devquickstarts-webapi-dotnet.md)

추가 리소스는 다음을 확인해보세요.
- [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->