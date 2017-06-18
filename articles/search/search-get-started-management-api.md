<properties 
	pageTitle="Azure 검색 관리 REST API 시작 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
	description="관리 REST API를 사용하여 호스팅되는 클라우드 Azure 검색 서비스 관리" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/08/2016" 
	ms.author="heidist"/>

# Azure 검색 관리 REST API 시작
> [AZURE.SELECTOR]
- [포털](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

Azure 검색 REST 관리 API는 포털에서 프로그래밍 방식으로 관리 작업을 수행하는 대체 방법입니다. 서비스 관리 작업에는 서비스 만들기 또는 삭제, 서비스 확장, 키 관리가 포함됩니다. 이 자습서는 서비스 관리 API를 보여 주는 샘플 클라이언트 응용 프로그램과 함께 제공됩니다. 로컬 개발 환경에서 샘플을 실행하는 데 필요한 구성 단계도 포함됩니다.

이 자습서를 완료하려면 다음이 필요합니다.

- Visual Studio 2012 또는 2013
- 샘플 클라이언트 응용 프로그램 다운로드

자습서를 완료하는 과정에서 두 가지 서비스: Azure 검색 및 AD(Azure Active Directory)를 프로비전합니다. 또한 Azure에서 클라이언트 응용 프로그램과 리소스 관리자 끝점 간에 트러스트를 설정하는 AD 응용 프로그램을 만듭니다.

이 자습서를 완료하려면 Azure 계정이 있어야 합니다.


##샘플 응용 프로그램 다운로드

이 자습서는 Visual Studio 2012 또는 2013에서 편집 및 실행할 수 있는 C#으로 작성된 Windows 콘솔 응용 프로그램을 기반으로 합니다.

[Azure 검색 .NET 관리 API 데모](https://github.com/Azure-Samples/search-dotnet-management-api/)에서 Github에 대한 클라이언트 응용 프로그램을 찾을 수 있습니다.


##응용 프로그램 구성

샘플 응용 프로그램을 실행하려면 먼저 클라이언트 응용 프로그램에서 리소스 관리자 끝점으로 보낸 요청을 수락할 수 있도록 인증을 사용하도록 설정해야 합니다. 인증 요구 사항은 검색 서비스 관리와 관련된 작업을 포함하여 API를 통해 요청된 모든 포털 관련 작업에 대한 기반이 되는 [Azure 리소스 관리자](https://msdn.microsoft.com/library/azure/dn790568.aspx)에서 시작됩니다. Azure 검색의 서비스 관리 API는 단순히 Azure 리소스 관리자의 확장이므로 해당 종속성을 상속합니다.

Azure 리소스 관리자에는 ID 공급자로서 Azure Active Directory 서비스가 필요합니다.

요청이 리소스 관리자에 연결되도록 허용하는 액세스 토큰을 가져오기 위해 클라이언트 응용 프로그램에는 Active Directory를 호출하는 코드 세그먼트가 포함됩니다. 코드 세그먼트와 코드 세그먼트 사용의 필수 조건 단계는 [Azure 리소스 관리자 요청 인증](http://msdn.microsoft.com/library/azure/dn790557.aspx) 문서에서 인용하였습니다.

위 링크의 지침에 따르거나, 자습서를 단계별로 진행하려고 이 문서의 단계를 사용할 수 있습니다.

이 섹션에서는 다음 작업을 수행합니다.

1. AD 서비스 만들기
1. AD 응용 프로그램 만들기
1. 다운로드한 샘플 클라이언트 응용 프로그램에 대한 세부 정보를 등록하여 AD 응용 프로그램 구성
1. 요청에 대한 권한을 얻는 데 사용할 값과 함께 샘플 클라이언트 응용 프로그램 로드

> [AZURE.NOTE] 이러한 링크는 Azure Active Directory를 사용하여 리소스 관리자: [Azure 리소스 관리자](http://msdn.microsoft.com/library/azure/dn790568.aspx), [Azure 리소스 관리자 요청 인증](http://msdn.microsoft.com/library/azure/dn790557.aspx) 및 [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)에 대한 클라이언트 요청을 인증하는 작업에 관한 배경 지식을 제공합니다.

###Active Directory 서비스 만들기

1. [Azure 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 왼쪽 탐색 창에서 아래로 스크롤하여 **Active Directory**를 클릭합니다.

4. **새로 만들기**를 클릭하여 **앱 서비스** | **Active Directory**를 엽니다. 이 단계에서는 새 Active Directory 서비스를 만듭니다. 이 서비스는 지금부터 몇 단계를 정의할 AD 응용 프로그램을 호스트합니다. 새 서비스를 만들면 이미 Azure에서 호스트 중일 수 있는 다른 응용 프로그램과 자습서를 분리할 수 있습니다.

5. **디렉터리** | **사용자 지정 만들기**를 클릭합니다.

6. 서비스 이름, 도메인 및 지리적 위치를 입력합니다. 도메인은 고유해야 합니다. 확인 표시를 클릭하여 서비스를 만듭니다.

     ![][5]

###이 서비스에 대한 새 AD 응용 프로그램 만들기

1. 방금 만든 "SearchTutorial" Active Directory 서비스를 선택합니다.

2. 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
 
3. **응용 프로그램 추가**를 클릭합니다. AD 응용 프로그램은 ID 공급자가 사용하게 될 클라이언트 응용 프로그램에 대한 정보를 저장합니다.
 
4. **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다. 이 옵션은 응용 프로그램 갤러리에 게시되지 않은 응용 프로그램에 대한 등록 설정을 제공합니다. 클라이언트 응용 프로그램은 응용 프로그램 갤러리에 포함되지 않으므로 이 자습서에 적합합니다.

     ![][6]
 
5. "Azure-Search-Manager"와 같은 이름을 입력합니다.

6. 응용 프로그램 유형으로 **Native client 응용 프로그램**을 선택합니다. 이는 샘플 응용 프로그램에 적절한 유형이고, 웹 응용 프로그램이 아니라 Windows 클라이언트(콘솔) 응용 프로그램일 수 있습니다.

     ![][7]
 
7. 리디렉션 URI에 "http://localhost/Azure-Search-Manager-App"을 입력합니다. 이 URI는 Azure Active Directory가 OAuth 2.0 권한 부여 요청에 대한 응답으로 사용자 에이전트를 리디렉션할 URI입니다. 값은 물리적 끝점일 필요가 없지만 유효한 URI여야 합니다.

    이 자습서에서는 아무 값이나 사용할 수 있지만 입력하는 값은 샘플 응용 프로그램에서 관리 연결에 대한 필수 입력이 됩니다.
 
7. 확인 표시를 클릭하여 Active Directory 응용 프로그램을 만듭니다. 왼쪽 탐색 창에 "Azure-Search-Manager-App"이 표시되어야 합니다.

###AD 응용 프로그램 구성
 
9. 방금 만든 AD 응용 프로그램, "Azure-Search-Manager-App"을 클릭합니다. 왼쪽 탐색 창에 해당 응용 프로그램이 표시되어야 합니다.

10. 최상위 메뉴에서 **구성**을 클릭합니다.
 
11. 사용 권한으로 아래로 스크롤하고 **Azure 관리 API**를 선택합니다. 이 단계에서 필요한 액세스 수준과 함께 클라이언트 응용 프로그램이 액세스해야 하는 API(이 경우 Azure 리소스 관리자 API)를 지정합니다.

12. 위임된 권한에서 드롭다운 목록을 클릭하고 **Azure 서비스 관리(미리 보기) 액세스**를 선택합니다.
 
     ![][8]
 
13. 변경 내용을 저장합니다.

응용 프로그램 구성 페이지를 열어 두세요. 다음 단계에서, 이 페이지에서 값을 복사하고 샘플 응용 프로그램에 입력합니다.

###등록 및 구독 값과 함께 샘플 응용 프로그램 로드

이 섹션에서는 Visual Studio에서 솔루션을 편집하여 포털에서 가져온 유효한 값을 대체합니다. 추가하는 값이 Program.cs의 위쪽에 나타납니다.

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

아직 [Github에서 응용 프로그램 예제를 다운로드](https://github.com/Azure-Samples/search-dotnet-management-api/)하지 않았으면 이 단계를 위해 다운로드해야 합니다.

1. Visual Studio에서 **ManagementAPI.sln**을 엽니다.

2. Program.cs를 엽니다.

3. `ClientId`을 제공합니다. 이전 단계에서 열어 둔 포털의 AD 응용 프로그램 구성 페이지에서 클라이언트 ID를 복사하고 Program.cs에 붙여넣습니다.

4. `RedirectUrl`을 제공합니다. 같은 포털 페이지의 리디렉션 URI를 복사하고 Program.cs에 붙여넣습니다.

	![][9]

5. `TenantID.` 제공
	- Active Directory | SearchTutorial(서비스)로 돌아갑니다.
	- 위쪽 막대에서 **응용 프로그램**을 클릭합니다.
	- 페이지 아래쪽에서 **끝점 보기** 를 클릭합니다.
	- 목록 아래쪽에서 OAUTH 2.0 권한 부여 끝점을 복사합니다.
	- 끝점을 TenantID에 붙여넣어 테넌트 ID를 제외한 모든 URI 매개 변수 값을 자릅니다.

    "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0"인 경우 "55e324c7-1656-4afe-8dc3-43efcd4ffa50"을 제외한 모든 내용을 삭제합니다.

	![][10]

6. `SubscriptionID`을 제공합니다.
	- 기본 포털 페이지로 이동합니다.
	- 왼쪽 탐색 창의 아래쪽에서 **설정**을 클릭합니다.
	- 구독 탭에서 구독 ID를 복사하고 Program.cs에 붙여 넣습니다.

7. 저장하고 솔루션을 빌드합니다.


##응용 프로그램 살펴보기

프로그램을 단계별로 실행할 수 있도록 첫 번째 메서드 호출에 중단점을 추가합니다. **F5** 키를 눌러 응용 프로그램을 실행하고 **F11** 키를 눌러 코드를 단계별로 실행합니다.

샘플 응용 프로그램에서는 기존 Azure 구독에 대한 무료 Azure 검색 서비스를 만듭니다. 구독에 대한 무료 서비스가 이미 있으면 샘플 응용 프로그램이 실패합니다. 무료 검색 서비스는 구독당 하나만 허용됩니다.

1. 솔루션 탐색기에서 Program.cs를 열고 Main(string void) 함수로 이동합니다.
 
3. **ExecuteArmRequest**는 지정된 `subscriptionID`의 Azure 리소스 관리자 끝점 `https://management.azure.com/subscriptions`에 대해 요청을 실행하는 데 사용됩니다. 이 메서드는 프로그램 전체에서 Azure 리소스 관리자 API 또는 검색 관리 API를 사용하여 작업을 수행하는 데 사용됩니다.

3. Azure 리소스 관리자에 대한 요청은 인증하고 권한을 부여해야 합니다. 이 작업은 [Azure 리소스 관리자 요청 인증](http://msdn.microsoft.com/library/azure/dn790557.aspx)에서 가져온 **ExecuteArmRequest** 메서드를 통해 호출되는 **GetAuthorizationHeader** 메서드를 사용하여 수행합니다. **GetAuthorizationHeader**는 `https://management.core.windows.net`를 호출하여 액세스 토큰을 가져옵니다.

4. 구독에 대해 유효한 사용자 이름 및 암호를 사용하여 로그인하라는 메시지가 표시됩니다.

5. 다음으로 새 Azure 검색 서비스가 Azure 리소스 관리자 공급자에 등록됩니다. 또한 이때 **ExecuteArmRequest** 메서드는 `providers/Microsoft.Search/register`를 통해 구독을 위한 Azure 검색 서비스를 만드는 데 사용됩니다.

6. 프로그램의 나머지 부분에서는 [Azure 검색 관리 REST API](http://msdn.microsoft.com/library/dn832684.aspx)를 사용합니다. 이 API의 `api-version`은 Azure 리소스 관리자 api-version과 다릅니다. 예를 들어 `/listAdminKeys?api-version=2014-07-31-Preview`은 Azure 검색 관리 REST API의 `api-version`를 표시합니다.

	다음 일련의 작업에서는 방금 만든 서비스 정의, admin api-key를 검색하고, 키를 재생성 및 검색하고, 복제본과 파티션을 변경하고, 마지막으로 서비스를 삭제합니다.

	서비스 복제본 또는 파티션 수를 변경할 때 무료 버전을 사용하면 이 작업이 실패합니다. Standard Edition에서만 파티션과 복제본을 추가로 사용할 수 있습니다.

	서비스 삭제가 마지막 작업입니다.

##다음 단계

이 자습서를 완료한 후 Active Directory 서비스를 사용한 서비스 관리 또는 인증에 대해 자세히 알아볼 수 있습니다.

- 클라이언트 응용 프로그램을 Active Directory와 통합하는 방법을 알아봅니다. [Azure Active Directory에서 응용 프로그램 통합](http://msdn.microsoft.com/library/azure/dn151122.aspx)을 참조하세요.
- Azure의 다른 서비스 관리 작업에 대해 알아봅니다. [서비스 관리](http://msdn.microsoft.com/library/azure/dn578292.aspx)를 참조하세요.

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 

<!---HONumber=AcomDC_0914_2016-->