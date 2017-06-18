<properties 
	pageTitle="Azure Active Directory 인증을 사용하여 LOB(기간 업무) Azure 앱 만들기 | Microsoft Azure" 
	description="Azure Active Directory로 인증하는 Azure App Service에서 ASP.NET MVC LOB(기간 업무) 앱을 만드는 방법에 대해 알아봅니다." 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="09/01/2016" 
	ms.author="cephalin"/>

# Azure Active Directory 인증을 사용하여 LOB(기간 업무) Azure 앱 만들기 #

이 문서에서는 [인증/권한 부여](../app-service/app-service-authentication-overview.md) 기능을 사용하여 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)에서 .NET LOB(기간 업무) 앱을 만드는 방법을 보여 줍니다. 또한 [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 사용하여 응용 프로그램에서 디렉터리 데이터를 쿼리하는 방법을 보여 줍니다.

사용한 Azure Active Directory 테넌트는 Azure 전용 디렉터리일 수 있습니다. 또는 [온-프레미스 Active Directory와 동기화](../active-directory/active-directory-aadconnect.md)되어 온-프레미스이고 원격인 작업자에 대한 Single Sign-On 환경을 만들 수 있습니다. 이 문서에서는 Azure 계정에 기본 디렉터리를 사용합니다.

<a name="bkmk_build"></a>
## 빌드할 내용 ##

앱 서비스 웹앱에서 다음 기능을 통해 작업 항목을 추적하는 간단한 기간 업무 CRUD(만들기-읽기-업데이트-삭제) 응용 프로그램을 빌드합니다.

- Azure Active Directory에 대해 사용자 인증
- [Azure Active Directory Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx)를 사용하여 디렉터리 사용자 및 그룹 쿼리
- ASP.NET MVC *인증 없음* 템플릿 사용

Azure에서 LOB(기간 업무) 앱에 대한 RBAC(역할 기반 액세스 제어)가 필요한 경우 [다음 단계](#next)를 참조하세요.

<a name="bkmk_need"></a>
## 필요한 항목 ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

이 자습서를 완료하려면 다음 항목이 필요합니다.

- 다양한 그룹의 사용자가 포함된 Azure Active Directory 테넌트
- Azure Active Directory 테넌트에서 응용 프로그램을 만들 수 있는 권한
- Visual Studio 2013 업데이트 4 이상
- [Azure SDK 2.8.1 이상](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>
## Azure에 웹앱 만들기 및 배포 ##

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **ASP.NET 웹 응용 프로그램**을 선택하고 프로젝트의 이름을 지정하고 **확인**을 클릭합니다.

3. **MVC** 템플릿을 선택한 다음 인증을 **인증 없음**으로 변경합니다. **클라우드에서 호스트**를 선택하고 **확인**을 클릭해야 합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)

4. **App Service 만들기** 대화 상자에서 **계정 추가**(그런 다음 드롭다운에서 **계정 추가**)를 클릭하여 Azure 계정에 로그인합니다.

5. 로그인하면 웹앱을 구성합니다. **새로 만들기** 단추를 각각 클릭하여 리소스 그룹 및 새 App Service 계획을 만듭니다. **추가 Azure 서비스 탐색**을 클릭하여 계속합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)

6. **서비스** 탭에서 **+**를 클릭하여 앱에 SQL Database를 추가합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)

7. **SQL Database 구성**에서 **새로 만들기**를 클릭하여 SQL Server 인스턴스를 만듭니다.

8. **SQL Server 구성**에서 SQL Server 인스턴스를 구성합니다. 그런 다음 **확인**, **확인** 및 **만들기**를 차례로 클릭하여 Azure에서 앱을 만들기 시작합니다.

9. **Azure App Service 활동**에서 앱 만들기가 완료되면 확인할 수 있습니다. **이 웹앱에 &lt;*appname*> 지금 게시**를 클릭한 다음 **게시**를 클릭합니다.

	Visual Studio가 완료되면 브라우저에서 게시 앱을 엽니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>
## 인증 및 디렉터리 액세스 구성

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **App Services** > **&lt;*appname*>** > **인증/권한 부여**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)

3. **켜기** > **Azure Active Directory** > **Express** > **확인**을 클릭하여 Azure Active Directory 인증을 사용합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)

4. 명령 모음에서 **저장**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)

    인증 설정이 성공적으로 저장되면 브라우저에서 다시 앱으로 이동합니다. 기본 설정은 전체 앱에 대한 인증을 적용합니다. 아직 로그인하지 않은 경우 로그인 화면으로 리디렉션됩니다. 로그인하면 HTTPS에서 보호하는 앱을 확인합니다. 다음으로, 디렉터리 데이터에 대한 액세스를 사용하도록 설정해야 합니다.

5. [클래식 포털](https://manage.windowsazure.com)로 이동합니다.

6. 왼쪽 메뉴에서 **Active Directory** > **기본 디렉터리** > **응용 프로그램** > **&lt;*appname*>**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)

	App Service에서 만든 Azure Active Directory 응용 프로그램이 인증/권한 부여 기능을 사용하도록 설정합니다.

7. **사용자** 및 **그룹**을 클릭하여 디렉터리에 일부 사용자 및 그룹이 있는지 확인합니다. 그렇지 않은 경우 몇 가지 테스트 사용자 및 그룹을 만듭니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)

7. **구성**을 클릭하여 이 응용 프로그램을 구성합니다.

8. **키** 섹션까지 아래로 스크롤하고 기간을 선택하여 키를 추가합니다. 그런 다음 **위임된 사용 권한**을 클릭하고 **디렉터리 데이터 읽기**를 선택합니다. **Save**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)

8. 설정이 저장되면 **키** 섹션까지 다시 스크롤하고 **복사** 단추를 클릭하여 클라이언트 키를 복사합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)

	>[AZURE.IMPORTANT] 이 페이지에서 지금 이동하는 경우 이 클라이언트 키에 다시 액세스할 수 없습니다.

9. 다음으로, 이 키를 사용하여 웹앱을 구성해야 합니다. Azure 계정을 사용하여 [Azure 리소스 탐색기](https://resources.azure.com)에 로그인합니다.

10. 페이지의 위쪽에서 **읽기/쓰기**를 클릭하여 Azure 리소스 탐색기를 변경합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)

11. subscriptions > **&lt;*subscriptionname*>** > **resourceGroups** > **&lt;*resourcegroupname*>** > **providers** > **Microsoft.Web** > **사이트** > **&lt;*appname*>** > **config** > **authsettings**에 있는 앱의 인증 설정을 찾습니다.

12. **편집**을 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)

13. 편집 창에서 `clientSecret` 및 `additionalLoginParams` 속성을 다음과 같이 설정합니다.

		...
		"clientSecret": "<client key from the Azure Active Directory application>",
		...
		"additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
		...

14. 위쪽에서 **배치**를 클릭하여 변경 내용을 제출합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)

14. 이제 Azure Active Directory Graph API에 액세스하는 권한 부여 토큰을 사용하는지를 테스트하려면 브라우저에서 **https://&lt;*appname*>.azurewebsites.net/.auth/me**로 이동합니다. 올바르게 모든 작업을 구성하는 경우 JSON 응답에서 `access_token` 속성이 표시되어야 합니다.

	`~/.auth/me` URL 경로는 App Service 인증/권한 부여에 의해 관리되어 인증된 세션에 관련된 모든 정보를 제공합니다. 자세한 내용은 [Azure App Service에서 인증 및 권한 부여](../app-service/app-service-authentication-overview.md)를 참조하세요.

	>[AZURE.NOTE] `access_token`의 기간이 만료되었습니다. 그러나 App Service 인증/권한 부여는 `~/.auth/refresh`를 사용하여 토큰 새로 고침 기능을 제공합니다. 사용 방법에 대한 자세한 내용은 [App Service 토큰 저장소](https://cgillum.tech/2016/03/07/app-service-token-store/)를 참조하세요.

다음으로, 디렉터리 데이터에 유용한 작업을 수행합니다.

<a name="bkmk_crud"></a>
## 앱에 LOB(기간 업무) 기능 추가

이제 간단한 CRUD 작업 항목 추적기를 만듭니다.

5.	~\\Models 폴더에서 WorkItem.cs라는 클래스 파일을 만들고 `public class WorkItem {...}`를 다음 코드로 바꿉니다.

		using System.ComponentModel.DataAnnotations;

		public class WorkItem
		{
			[Key]
			public int ItemID { get; set; }
			public string AssignedToID { get; set; }
			public string AssignedToName { get; set; }
			public string Description { get; set; }
			public WorkItemStatus Status { get; set; }
		}

		public enum WorkItemStatus
		{
			Open,
			Investigating,
			Resolved,
			Closed
		}

7.	프로젝트를 빌드하여 새 모델이 Visual Studio에서 스캐폴딩 논리에 액세스할 수 있도록 합니다.

8.	새 스캐폴드된 항목 `WorkItemsController`을 ~\\Controllers 폴더에 추가합니다(마우스 오른쪽 단추로 **컨트롤러**를 클릭하고 **추가**를 가리키고 **새 스캐폴드된 항목**을 선택함).

9.	**뷰가 포함된 MVC 5 컨트롤러, Entity Framework 사용**을 선택하고 **추가**를 클릭합니다.

10.	만든 모델을 선택한 다음 **+** 및 **추가**를 차례로 클릭하여 데이터 내용을 추가한 다음 **추가**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)

14.	~\\Views\\WorkItems\\Create.cshtml(자동으로 스캐폴드된 항목)에서 `Html.BeginForm` 도우미 메서드를 찾아 다음과 같이 강조 표시된 사항을 변경합니다.
	<pre class="prettyprint">
	@model WebApplication1.Models.WorkItem
	
	@{
		ViewBag.Title = "Create";
	}
	
	&lt;h2>만들기&lt;/h2>
	
	@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>)) 
	{
		@Html.AntiForgeryToken()
	
		&lt;div class="form-horizontal">
			&lt;h4>WorkItem&lt;/h4>
			&lt;hr />
			@Html.ValidationSummary(true, "", new { @class = "text-danger" })
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToID, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type = "hidden"</mark> } })
					@Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
					@Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				@Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
				&lt;div class="col-md-10">
					@Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
					@Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
				&lt;/div>
			&lt;/div>
	
			&lt;div class="form-group">
				&lt;div class="col-md-offset-2 col-md-10">
					&lt;input type="submit" value="Create" class="btn btn-default"<mark> id="submit-button"</mark> />
				&lt;/div>
			&lt;/div>
		&lt;/div>
	}
	
	&lt;div>
		@Html.ActionLink("Back to List", "Index")
	&lt;/div>
	
	@section Scripts {
		@Scripts.Render("~/bundles/jqueryval")
		<mark>&lt;script>
			// People/Group Picker Code
			var maxResultsPerPage = 14;
			var input = document.getElementById("AssignedToName");
	
			// 요청 헤더의 액세스 토큰 및 클레임 ID의 tenantID
			var token = "@Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]";
			var tenant ="@(System.Security.Claims.ClaimsPrincipal.Current.Claims
							.Where(c => c.Type == "http://schemas.microsoft.com/identity/claims/tenantid")
							.Select(c => c.Value).SingleOrDefault())";
	
			var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
	
			// Submit the selected user/group to be asssigned.
			$("#submit-button").click({ picker: picker }, function () {
				if (!picker.Selected())
					return;
				$("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
			});
		&lt;/script></mark>
	}
	</pre>
	
	`token` 및 `tenant`는 `AadPicker` 개체에서 사용하여 Azure Active Directory Graph API를 호출합니다. 나중에 `AadPicker`를 추가합니다.
	
	>[AZURE.NOTE] 마찬가지로 `~/.auth/me`을 사용하여 클라이언트 쪽에서 `token` 및 `tenant`을 가져올 수 있지만 추가 서버 호출입니다. 예:
	>  
    >     $.ajax({
    >         dataType: "json",
    >         url: "/.auth/me",
    >         success: function (data) {
    >             var token = data[0].access_token;
    >             var tenant = data[0].user_claims
    >                             .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid')
    >                             .val;
    >         }
    >     });
	
15. ~\\Views\\WorkItems\\Edit.cshtml을 사용하여 동일하게 변경합니다.

15. `AadPicker` 개체는 프로젝트에 추가해야 하는 스크립트에 정의됩니다. ~\\Scripts 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리키고 **JavaScript 파일**을 클릭합니다. 파일 이름에 `AadPickerLibrary`를 입력하고 **확인**을 클릭합니다.

16. [여기](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js)에서 ~\\Scripts\\AadPickerLibrary.js로 콘텐츠를 복사합니다.

	스크립트에서 `AadPicker` 개체는 [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 호출하여 입력과 일치하는 사용자 및 그룹을 검색합니다.

17. 또한 ~\\Scripts\\AadPickerLibrary.js는 [jQuery UI 자동 완성 위젯](https://jqueryui.com/autocomplete/)을 사용합니다. 따라서 jQuery UI를 프로젝트에 추가해야 합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

18. NuGet 패키지 관리자에서 찾아보기를 클릭하고 검색 표시줄에 **jquery ui**를 입력하고 **jQuery.UI.Combined**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)

19. 오른쪽 창에서 **설치**를 클릭한 다음 **확인**을 클릭하여 진행합니다.

19. ~\\App\_Start\\BundleConfig.cs를 열고 다음 강조 표시된 사항을 변경합니다.
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
		bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
					"~/Scripts/jquery-{version}.js"<mark>,
					"~/Scripts/jquery-ui-{version}.js",
					"~/Scripts/AadPickerLibrary.js"</mark>));
	
		bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
					"~/Scripts/jquery.validate*"));
	
		// Modernizr의 개발 버전을 사용하여 개발하고 학습합니다. 그런 다음 프로덕션 준비가 되면,
		// http://modernizr.com에서 빌드 도구를 사용하여 필요한 테스트만 선택합니다.
		bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
					"~/Scripts/modernizr-*"));
	
		bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
					"~/Scripts/bootstrap.js",
					"~/Scripts/respond.js"));
	
		bundles.Add(new StyleBundle("~/Content/css").Include(
					"~/Content/bootstrap.css",
					"~/Content/site.css"<mark>,
					"~/Content/themes/base/jquery-ui.css"</mark>));
	}
	</pre>

	앱에서 JavaScript 및 CSS 파일을 관리하는 다양하고 효율적인 방법이 있습니다. 그러나 간단히 하기 위해 모든 보기로 로드되는 번들에 대해 도움을 받습니다.

12. 마지막으로 ~\\Global.asax의 `Application_Start()` 메서드에 코드의 다음 줄을 추가합니다. 이를 수정하기 위해 각 명명 확인 오류에 대한 `Ctrl`+`.`.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	> [AZURE.NOTE] 기본 MVC 템플릿이 작업 일부에 대해 <code>[ValidateAntiForgeryToken]</code> 장식을 사용하기 때문에 이 코드 줄이 필요합니다. [MVC 4, AntiForgeryToken 및 클레임](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/)에서 [Brock Allen](https://twitter.com/BrockLAllen)이 설명한 동작으로 인해 다음과 같은 사유로 HTTP POST에서 위조 방지 토큰 유효성 검사에 실패할 수 있습니다.

	> - Azure Active Directory에서는 기본적으로 위조 방지 토큰에서 필요로 하는 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider를 보내지 않습니다.
	> - Azure Active Directory와 AD FS의 디렉터리가 동기화된 경우 AD FS 트러스트에서도 기본적으로 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider 클레임을 보내지 않습니다. 이 클레임을 보내도록 AD FS를 수동으로 구성할 수는 있습니다.

	> `ClaimTypes.NameIdentifies`는 Azure Active Directory에서 제공하는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 클레임을 지정합니다.

20. 이제 변경 내용을 게시합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

21. **설정**을 클릭하고 SQL Database에 연결 문자열이 있는지 확인하고 **데이터베이스 업데이트**를 선택하여 모델의 스키마를 변경하고 **게시**를 클릭합니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)

22. 브라우저에서 https://&lt;*appname*>.azurewebsites.net/workitems로 이동하고 **새로 만들기**를 클릭합니다.

23. **AssignedToName** 상자를 클릭합니다. 드롭다운에서 Azure Active Directory 테넌트의 사용자 및 그룹이 이제 표시되어야 합니다. 필터링하도록 입력하거나 `Up` 또는 `Down` 키를 사용하거나 사용자 또는 그룹을 선택하도록 클릭할 수 있습니다.

	![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)

24. **만들기**를 클릭하여 변경 내용을 저장합니다. 그런 다음 만든 작업 항목에서 **편집**을 클릭하여 동일한 동작을 관찰합니다.

축하합니다. 이제 디렉터리 액세스 권한으로 Azure에서 LOB(기간 업무) 앱을 실행합니다. Graph API를 사용하여 수행할 수 있는 작업은 많습니다. [Azure AD Graph API 참조](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog)를 참조하세요.

<a name="next"></a>
## 다음 단계

Azure에서 LOB(기간 업무) 앱에 대한 RBAC(역할 기반 액세스 제어)가 필요한 경우 Azure Active Directory 팀의 샘플에 대한 [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)을 참조하세요. Azure Active Directory 응용 프로그램에 대한 역할을 사용하도록 설정하고 `[Authorize]` 장식을 사용하여 사용자에게 권한을 부여하는 방법을 보여 줍니다.

LOB(기간 업무)가 온-프레미스 데이터에 대한 액세스 권한이 필요한 경우 [Azure App Service에서 하이브리드 연결을 사용하여 온-프레미스 리소스에 액세스](web-sites-hybrid-connection-get-started.md)를 참조하세요.

<a name="bkmk_resources"></a>
## 추가 리소스

- [Azure 앱 서비스의 인증 및 권한 부여](../app-service/app-service-authentication-overview.md)
- [Azure 앱에서 온-프레미스 Active Directory를 사용하여 인증](web-sites-authentication-authorization.md)
- [AD FS 인증을 사용하여 Azure에서 LOB(기간 업무) 앱 만들기](web-sites-dotnet-lob-application-adfs.md)
- [App Services 인증 및 Azure AD Graph API](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
- [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/AzureADSamples)
- [Azure Active Directory 지원 토큰 및 클레임 유형](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

<!---HONumber=AcomDC_0914_2016-->