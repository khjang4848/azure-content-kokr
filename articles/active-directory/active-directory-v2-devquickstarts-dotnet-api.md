<properties
	pageTitle="Azure AD v2.0 .NET 웹 API| Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다의 토큰을 허용하는 .NET MVC Web API를 빌드하는 방법입니다."
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

# MVC 웹 API 보안 유지

Azure Active Directory v2.0 끝점을 사용하면 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 액세스 토큰을 사용하여 Web API를 보호함으로써 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 가진 사용자가 Web API에 안전하게 액세스할 수 있도록 합니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

ASP.NET Web API에서는 .NET Framework 4.5에 포함된 Microsoft OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다. 여기서는 클라이언트가 사용자의 할 일 목록을 만들고 거기서 작업을 읽을 수 있는 "할 일 목록" MVC 웹 API를 만듭니다. 웹 API는 들어오는 요청이 유효한 액세스 토큰을 포함하고 있는지 확인하고 보호된 경로에서 유효성 검사에 합격하지 않은 요청을 거부합니다.

## 다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

골격 앱은 간단한 API에 대한 모든 상용구 코드를 포함하지만 ID 관련 부분은 전혀 포함하지 않습니다. 따라서 진행하지 않으려면 전체 샘플을 대신 복제하거나 [다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)합니다.

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.

또한 이 visual studio 솔루션에는 간단한 WPF 앱인 "TodoListClient"가 포함되어 있습니다. TodoListClient는 사용자가 로그인하는 방법 및 클라이언트가 웹 API에 요청을 발급할 수 있는 방법을 설명하기 위해 사용됩니다. 이 경우 TodoListClient와 TodoListService는 모두 동일한 앱으로 표시됩니다. TodoListClient를 구성하려면 다음을 수행해야 합니다.

- 앱용 **Mobile** 플랫폼을 추가합니다.
- 포털에서 **Redirect URI**를 복사합니다. `urn:ietf:wg:oauth:2.0:oob`의 기본값을 사용해야 합니다.


## OWIN 설치

앱을 등록했으므로 들어오는 요청 및 토큰의 유효성을 검사하기 위해 v2.0 끝점과 통신할 수 있게 앱을 설정해야 합니다.

- 시작하려면 솔루션을 열고 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 TodoListService 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## OAuth 인증 구성

- OWIN Startup 클래스를 `Startup.cs`라는 TodoListService 프로젝트에 추가합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** --> **새 항목** --> "OWIN" 검색을 클릭합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(…)` 메서드를 호출합니다.
- 클래스 선언을 이미 다른 파일에서 이 클래스의 일부를 구현했던 `public partial class Startup`으로 변경합니다. `Configuration(…)` 메서드에서 ConfgureAuth(...)를 호출하여 웹앱에 대한 인증을 설정합니다.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- `App_Start\Startup.Auth.cs` 파일을 열고 v2.0 끝점의 토큰을 허용하도록 Web API를 설정하는 `ConfigureAuth(…)` 메서드를 구현합니다.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

- 이제 `[Authorize]` 특성을 사용하여 OAuth 2.0 전달자 인증으로 컨트롤러 및 작업을 보호할 수 있습니다. authorize 태그를 사용하여 `Controllers\TodoListController.cs` 클래스를 데코레이팅합니다. 이렇게 하면 사용자는 해당 페이지에 액세스하기 전에 강제로 로그인됩니다.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 권한 있는 호출자가 `TodoListController` API 중 하나를 호출하면 작업은 호출자에 대한 정보에 액세스해야 합니다. OWIN은 `ClaimsPrincpal` 개체를 통해 전달자 토큰 내의 클레임에 액세스할 수 있도록 합니다.

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	마지막으로 TodoListService 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	`ida:Audience`는 포털에 입력한 앱의 **응용 프로그램 ID**입니다.

## 클라이언트 앱 구성
Todo List Service가 작동하는 것을 보려면 먼저 v2.0 끝점에서 토큰을 가져오고 서비스를 호출할 수 있도록 Todo List Client를 구성해야 합니다.

- TodoListClient 프로젝트에서 `App.config`를 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
  -	포털에서 복사한 `ida:ClientId` 응용 프로그램 Id입니다.
	- `ida:RedirectUri`는 포털의 **리디렉션 URI**입니다.

마지막으로, 각 프로젝트를 정리하고 빌드한 후 실행합니다. 이제 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다의 토큰을 허용하는 .NET MVC Web API가 있습니다. TodoListClient에 로그인하고 Web API를 호출하여 사용자의 할 일 모음에 작업을 추가합니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## 다음 단계
이제 추가 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[웹앱에서 웹 API 호출 >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

추가 리소스는 다음을 확인해보세요.
- [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->