<properties
	pageTitle=".NET 다중 계층 응용 프로그램 | Microsoft Azure"
	description="Azure에서 서비스 버스 큐를 사용하여 계층 간에 통신하는 다중 계층 응용 프로그램을 개발하는 데 도움이 되는 .NET 자습서입니다."
	services="service-bus-messaging"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-messaging"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="09/01/2016"
	ms.author="sethm"/>

# Azure 서비스 버스 큐를 사용하는 .NET 다중 계층 응용 프로그램

## 소개

Visual Studio 및 무료로 제공되는 Azure SDK for .NET을 사용하면 Microsoft Azure용 개발이 간단합니다. 이 자습서에서는 로컬 환경에서 실행되는 여러 Azure 리소스를 사용하는 응용 프로그램을 만드는 단계를 안내합니다. 이전에 Azure를 사용한 경험이 없다고 가정합니다.

다음을 학습합니다.

-   한 번 다운로드하고 설치하여 Azure 개발용 컴퓨터를 사용하도록 설정하는 방법
-   Visual Studio를 사용하여 Azure용으로 개발하는 방법
-   웹 및 작업자 역할을 사용하여 Azure에서 다중 계층 응용 프로그램을 만드는 방법
-   서비스 버스 큐를 사용하여 계층 간에 통신하는 방법입니다.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

이 자습서에서는 Azure 클라우드 서비스에서 다중 계층 응용 프로그램을 빌드하고 실행합니다. 프런트 엔드는 ASP.NET MVC 웹 역할이고 백 엔드는 서비스 버스 큐를 사용하는 작업자 역할입니다. 클라우드 서비스가 아닌 Azure 웹 사이트에 배포되는 웹 프로젝트와 동일한 다중 계층 응용 프로그램(프런트 엔드 포함)을 만들 수 있습니다. Azure 웹 사이트 프런트 엔드를 다르게 수행하는 방법에 대한 지침은 [다음 단계](#nextsteps) 섹션을 참조하세요. [.NET 온-프레미스/클라우드 하이브리드 응용 프로그램](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) 자습서를 시도해 볼 수 있습니다.

다음 스크린샷에서는 완성된 응용 프로그램을 보여 줍니다.

![][0]

## 시나리오 개요: 역할 간 통신

처리할 주문을 제출하려면 웹 역할에서 실행되는 프런트 엔드 UI 구성 요소가 작업자 역할에서 실행되는 중간 계층 논리와 상호 작용해야 합니다. 이 예제에서는 계층 간 통신에 서비스 버스 조정된 메시징을 사용합니다.

웹과 중간 계층 간에 조정된 메시징을 사용하면 두 구성 요소가 분리됩니다. 직접 메시징(즉, TCP 또는 HTTP)과 달리 웹 계층은 중간 계층에 직접 연결되지 않고 작업 단위를 메시지로 서비스 버스에 푸시하여 중간 계층에서 사용하고 처리할 준비가 될 때까지 안정적으로 유지합니다.

서비스 버스는 조정된 메시징을 지원하는 두 개의 엔터티인 큐와 토픽을 제공합니다. 큐를 사용하면 큐로 전송된 각 메시지가 단일 수신기에서 사용됩니다. 토픽은 게시된 각 메시지를 토픽에 등록된 구독에서 사용할 수 있게 하는 게시/구독 패턴을 지원합니다. 각 구독은 메시지의 고유한 큐를 논리적으로 유지 관리합니다. 또한 구독은 구독 큐에 전달되는 메시지 집합을 필터와 일치하는 메시지로 제한하는 필터 규칙을 사용하여 구성할 수 있습니다. 다음 예에서는 서비스 버스 큐를 사용합니다.

![][1]

이 통신 메커니즘은 직접 메시징에 비해 몇 가지 장점이 있습니다.

-   **임시 분리.** 비동기 메시징 패턴을 사용하면 생산자와 소비자가 동시에 온라인 상태일 필요가 없습니다. 서비스 버스는 소비하는 쪽에서 메시지를 수신할 준비가 될 때까지 메시지를 안정적으로 저장합니다. 따라서 전체 시스템에는 영향을 주지 않고 자발적으로(예: 유지 관리의 경우) 또는 구성 요소 크래시로 인해 분산 응용 프로그램 구성 요소의 연결을 끊을 수 있습니다. 또한 소비 응용 프로그램은 하루 중 특정 기간 동안만 온라인 상태로 전환해야 할 수 있습니다.

-   **부하 평준화.** 많은 응용 프로그램에서 시스템 부하는 시간에 따라 다르지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 최대 부하가 아닌 평균 부하를 수용하려면 소비 응용 프로그램(작업자)만 프로비전해야 함을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 응용 프로그램 부하를 제공하는 데 필요한 인프라의 크기와 관련하여 비용이 직접적으로 절약됩니다.

-   **부하 분산.** 부하가 증가하면 뷰에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 속도로 메시지를 가져올 때 처리 능력이 다른 경우에도 작업자 컴퓨터의 최적 사용률을 허용합니다. 이 패턴을 종종 *경쟁 소비자* 패턴이라고 합니다.

    ![][2]

다음 섹션에서는 이 아키텍처를 구현하는 코드에 대해 설명합니다.

## 개발 환경 설정

Azure 응용 프로그램 개발을 시작하려면 먼저 도구를 얻고 개발 환경을 설정해야 합니다.

1.  [도구 및 SDK 가져오기][]에서 .NET 용 Azure SDK를 설치합니다.

2. 	사용하고 있는 Visual Studio 버전에 대한 **SDK 설치**를 클릭합니다. 이 자습서의 단계에서는 Visual Studio 2015를 사용합니다.

4.  설치 관리자를 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.

5.  **웹 플랫폼 설치 관리자**에서 **설치**를 클릭하여 설치를 계속합니다.

6.  설치가 완료되면 앱을 개발하기 시작하는 데 필요한 내용이 모두 준비된 것입니다. SDK에는 Visual Studio에서 Azure 응용 프로그램을 쉽게 개발할 수 있는 도구가 포함되어 있습니다. Visual Studio가 설치되어 있지 않으면 SDK에서 무료로 제공되는 Visual Studio Express도 설치합니다.

## 네임스페이스 만들기

다음 단계에서는 서비스 네임스페이스를 만들고 SAS(공유 액세스 서명) 키를 얻습니다. 네임스페이스는 서비스 버스를 통해 노출되는 각 응용 프로그램에 대한 응용 프로그램 경계를 제공합니다. SAS 키는 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 네임스페이스 및 SAS 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 웹 역할 만들기

이 섹션에서는 응용 프로그램의 프런트 엔드를 빌드합니다. 먼저 응용 프로그램에서 표시하는 페이지를 만듭니다. 그런 다음 서비스 버스 큐에 항목을 제출하고 큐에 대한 상태 정보를 표시하는 코드를 추가합니다.

### 프로젝트 만들기

1.  관리자 권한을 사용하여 Microsoft Visual Studio를 시작합니다. 관리자 권한으로 Visual Studio를 시작하려면 **Visual Studio** 프로그램 아이콘을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭합니다. 이 문서의 뒷부분에서 설명하는 Azure 계산 에뮬레이터를 사용하려면 Visual Studio를 관리자 권한으로 실행해야 합니다.

    Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

2.  **설치된 템플릿**의 **Visual C#** 아래에서 **클라우드**를 클릭한 다음 **Azure 클라우드 서비스**를 클릭합니다. 프로젝트의 이름을 **MultiTierApp**으로 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![][9]

3.  **.NET Framework 4.5** 역할에서 **ASP.NET 웹 역할**을 두 번 클릭합니다.

    ![][10]

4.  **Azure 클라우드 서비스 솔루션** 아래의 **WebRole1**을 가리키고 연필 아이콘을 클릭한 다음 웹 역할의 이름을 **FrontendWebRole**로 변경합니다. 그런 후 **OK**를 클릭합니다. "FrontEnd"가 아니라 소문자 "e"를 사용하여 "Frontend"를 입력해야 합니다.

    ![][11]

5.  **새 ASP.NET 프로젝트** 대화 상자의 **템플릿 선택** 목록에서 **MVC**를 클릭합니다.

    ![][12]

6. **새 ASP.NET 프로젝트** 대화 상자에서 **인증 변경** 단추를 클릭합니다. **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다. 이 자습서의 경우 사용자 로그인이 필요하지 않은 앱을 배포하고 있습니다.

	![][16]

7. **새 ASP.NET 프로젝트** 대화 상자로 돌아와서 **확인**을 클릭하여 프로젝트를 만듭니다.

6.  **FrontendWebRole** 프로젝트의 **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

7.  **찾아보기** 탭을 클릭한 다음 `Microsoft Azure Service Bus`를 검색합니다. **설치**를 클릭하고 사용 약관에 동의합니다.

    ![][13]

	이제 필요한 클라이언트 어셈블리가 참조되고 몇 가지 새 코드 파일이 추가되었습니다.

9.  **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다. **이름** 상자에 **OnlineOrder.cs** 이름을 입력합니다. 그런 다음 **추가**를 클릭합니다.

### 웹 역할에 대한 코드 작성

이 섹션에서는 응용 프로그램에서 표시하는 다양한 페이지를 만듭니다.

1.  Visual Studio의 OnlineOrder.cs 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

	```
	namespace FrontendWebRole.Models
	{
	    public class OnlineOrder
	    {
	        public string Customer { get; set; }
	        public string Product { get; set; }
	    }
	}
	```

2.  **솔루션 탐색기**에서 **Controllers\\HomeController.cs**를 두 번 클릭합니다. 파일 맨 위에 다음 **using** 문을 추가하여 서비스 버스뿐만 아니라 방금 만든 모델에 대한 네임스페이스를 포함합니다.

	```
	using FrontendWebRole.Models;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	```

3.  또한 Visual Studio의 HomeController.cs 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드에는 큐에 대한 항목 제출을 처리하는 메서드가 포함되어 있습니다.

	```
	namespace FrontendWebRole.Controllers
	{
	    public class HomeController : Controller
	    {
	        public ActionResult Index()
	        {
	            // Simply redirect to Submit, since Submit will serve as the
	            // front page of this application.
	            return RedirectToAction("Submit");
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        // GET: /Home/Submit.
	        // Controller method for a view you will create for the submission
	        // form.
	        public ActionResult Submit()
	        {
	            // Will put code for displaying queue message count here.
	
	            return View();
	        }
	
	        // POST: /Home/Submit.
	        // Controller method for handling submissions from the submission
	        // form.
	        [HttpPost]
			// Attribute to help prevent cross-site scripting attacks and
			// cross-site request forgery.  
			[ValidateAntiForgeryToken]
	        public ActionResult Submit(OnlineOrder order)
	        {
	            if (ModelState.IsValid)
	            {
	                // Will put code for submitting to queue here.
	
	                return RedirectToAction("Submit");
	            }
	            else
	            {
	                return View(order);
	            }
	        }
	    }
	}
	```

4.  **빌드** 메뉴에서 **솔루션 빌드**를 클릭하여 지금까지의 작업에 대한 정확성을 테스트합니다.

5.  이제 위에서 만든 `Submit()` 메서드에 대한 보기를 만듭니다. `Submit()` 메서드(매개 변수를 사용하지 않는 `Submit()`의 오버로드) 내에서 마우스 오른쪽 단추로 클릭한 다음 **보기 추가**를 선택합니다.

    ![][14]

6.  보기를 만들 수 있는 대화 상자가 나타납니다. **템플릿** 목록에서 **만들기**를 선택합니다. **모델 클래스** 목록에서 **OnlineOrder** 클래스를 클릭합니다.

    ![][15]

7.  **추가**를 클릭합니다.

8.  이제 응용 프로그램의 표시 이름을 변경합니다. **솔루션 탐색기**에서 **Views\\Shared\\_Layout.cshtml** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

9.  **내 ASP.NET 응용 프로그램**과 일치하는 모든 항목을 **LITWARE 제품**으로 바꿉니다.

10. **홈**, **정보** 및 **연락처** 링크를 제거합니다. 강조 표시된 코드를 삭제합니다.

	![][28]

11. 마지막으로 큐에 대한 일부 정보를 포함하도록 제출 페이지를 수정합니다. **솔루션 탐색기**에서 **Views\\Home\\Submit.cshtml** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. `<h2>Submit</h2>` 뒤에 다음 줄을 추가합니다. 이제 `ViewBag.MessageCount`은(는) 비어 있습니다. 나중에 채웁니다.

	```
	<p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
	```

12. 이제 UI를 구현했습니다. **F5** 키를 눌러 응용 프로그램을 실행하고 예상대로 나타나는지 확인할 수 있습니다.

    ![][17]

### 서비스 버스 큐에 항목을 제출하는 코드 작성

이제 큐에 항목을 제출하는 코드를 추가합니다. 먼저 서비스 버스 큐 연결 정보를 포함하는 클래스를 만듭니다. 그런 다음 Global.aspx.cs에서 연결을 초기화합니다. 끝으로, 앞에서 만든 제출 코드를 HomeController.cs에서 업데이트하여 항목을 서비스 버스 큐에 실제로 제출합니다.

1.  **솔루션 탐색기**에서 **FrontendWebRole**을 마우스 오른쪽 단추로 클릭합니다(역할이 아닌 프로젝트를 마우스 오른쪽 단추로 클릭). **추가**를 클릭한 후 **클래스**를 클릭합니다.

2.  클래스 이름을 **QueueConnector.cs**로 지정합니다. **추가**를 클릭하여 클래스를 만듭니다.

3.  이제 연결 정보를 캡슐화하고 서비스 버스 큐에 대한 연결을 초기화하는 코드를 추가합니다. QueueConnector.cs의 전체 내용을 다음 코드로 바꾸고 `your Service Bus namespace`(네임스페이스 이름) 및 `yourKey`의 값을 입력합니다. 이는 Azure 포털에서 이전에 가져온 **기본 키**입니다.

	```
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.ServiceBus;
	
	namespace FrontendWebRole
	{
	    public static class QueueConnector
	    {
	        // Thread-safe. Recommended that you cache rather than recreating it
	        // on every request.
	        public static QueueClient OrdersQueueClient;
	
	        // Obtain these values from the portal.
	        public const string Namespace = "your Service Bus namespace";
	
	        // The name of your queue.
	        public const string QueueName = "OrdersQueue";
	
	        public static NamespaceManager CreateNamespaceManager()
	        {
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var uri = ServiceBusEnvironment.CreateServiceUri(
	                "sb", Namespace, String.Empty);
	            var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                "RootManageSharedAccessKey", "yourKey");
	            return new NamespaceManager(uri, tP);
	        }
	
	        public static void Initialize()
	        {
	            // Using Http to be friendly with outbound firewalls.
	            ServiceBusEnvironment.SystemConnectivity.Mode =
	                ConnectivityMode.Http;
	
	            // Create the namespace manager which gives you access to
	            // management operations.
	            var namespaceManager = CreateNamespaceManager();
	
	            // Create the queue if it does not exist already.
	            if (!namespaceManager.QueueExists(QueueName))
	            {
	                namespaceManager.CreateQueue(QueueName);
	            }
	
	            // Get a client to the queue.
	            var messagingFactory = MessagingFactory.Create(
	                namespaceManager.Address,
	                namespaceManager.Settings.TokenProvider);
	            OrdersQueueClient = messagingFactory.CreateQueueClient(
	                "OrdersQueue");
	        }
	    }
	}
	```

4.  이제 **Initialize** 메서드가 호출되는지 확인합니다. **솔루션 탐색기**에서 **Global.asax\\Global.asax.cs**를 두 번 클릭합니다.

5.  다음 코드 줄을 **Application\_Start** 메서드의 끝에 추가합니다.

	```
	FrontendWebRole.QueueConnector.Initialize();
	```

6.  끝으로, 앞에서 만든 웹 코드를 업데이트하여 항목을 큐에 제출합니다. **솔루션 탐색기**에서 **Controllers\\HomeController.cs**를 두 번 클릭합니다.

7.  다음과 같이 `Submit()` 메서드(매개 변수를 사용하지 않는 오버로드)를 업데이트하여 큐에 대한 메시지 수를 가져옵니다.

	```
	public ActionResult Submit()
	{
	    // Get a NamespaceManager which allows you to perform management and
	    // diagnostic operations on your Service Bus queues.
	    var namespaceManager = QueueConnector.CreateNamespaceManager();
	
	    // Get the queue, and obtain the message count.
	    var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
	    ViewBag.MessageCount = queue.MessageCount;
	
	    return View();
	}
	```

8.  다음과 같이 `Submit(OnlineOrder order)` 메서드(하나의 매개 변수를 사용하는 오버로드)를 업데이트하여 큐에 주문 정보를 제출합니다.

	```
	public ActionResult Submit(OnlineOrder order)
	{
	    if (ModelState.IsValid)
	    {
	        // Create a message from the order.
	        var message = new BrokeredMessage(order);
	
	        // Submit the order.
	        QueueConnector.OrdersQueueClient.Send(message);
	        return RedirectToAction("Submit");
	    }
	    else
	    {
	        return View(order);
	    }
	}
	```

9.  이제 응용 프로그램을 다시 실행할 수 있습니다. 주문을 제출할 때마다 메시지 수가 증가합니다.

    ![][18]

## 작업자 역할 만들기

이제 주문 제출을 처리하는 작업자 역할을 만듭니다. 이 예제에서는 **Worker Role with Service Bus Queue** Visual Studio 프로젝트 템플릿을 사용합니다. 포털에서 필요한 자격 증명을 이미 가져왔습니다.

1. Visual Studio를 Azure 계정에 연결해야 합니다.

2.  Visual Studio의 **솔루션 탐색기**에서 **MultiTierApp** 프로젝트 아래의 **역할** 폴더를 마우스 오른쪽 단추로 클릭합니다.

3.  **추가**를 클릭한 후 **새 작업자 역할 프로젝트**를 클릭합니다. **새 역할 프로젝트 추가** 대화 상자가 나타납니다.

	![][26]

4.  **새 역할 프로젝트 추가** 대화 상자에서 **서비스 버스 큐가 있는 작업자 역할**을 클릭합니다.

	![][23]

5.  **이름** 상자에서 프로젝트 이름을 **OrderProcessingRole**로 지정합니다. 그런 다음 **추가**를 클릭합니다.

6.  "서비스 버스 네임스페이스 만들기" 섹션의 9단계에서 얻은 연결 문자열을 클립보드에 복사합니다.

7.  **솔루션 탐색기**에서, 5단계에서 만든 **OrderProcessingRole**을 마우스 오른쪽 단추로 클릭합니다. 이때 클래스가 아니라 **역할** 아래의 **OrderProcessingRole**을 마우스 오른쪽 단추로 클릭해야 합니다. 그런 다음 **속성**을 클릭합니다.

8.  **속성** 대화 상자의 **설정** 탭에서 **Microsoft.ServiceBus.ConnectionString**의 **값** 상자 내부를 클릭한 다음 6단계에서 복사한 끝점 값을 붙여 넣습니다.

	![][25]

9.  **OnlineOrder** 클래스를 만들어 큐에서 처리할 때 주문을 나타냅니다. 이미 만든 클래스를 다시 사용할 수 있습니다. **솔루션 탐색기**에서 **OrderProcessingRole** 클래스를 마우스 오른쪽 단추로 클릭합니다(역할이 아닌 클래스 아이콘을 마우스 오른쪽 단추로 클릭). **추가**를 클릭한 후 **기존 항목**을 클릭합니다.

10. **FrontendWebRole\\Models**의 하위 폴더로 이동하고 **OnlineOrder.cs**를 두 번 클릭하여 이 프로젝트에 추가합니다.

11. **WorkerRole.cs**에서 다음 코드와 같이 **QueueName** 변수 값을 `"ProcessingQueue"`에서 `"OrdersQueue"`(으)로 변경합니다.

	```
	// The name of your queue.
	const string QueueName = "OrdersQueue";
	```

12. WorkerRole.cs 파일 맨 위에 다음 using 문을 추가합니다.

	```
	using FrontendWebRole.Models;
	```

13. `Run()` 함수의 `OnMessage()` 호출 내에서 `try` 절의 내용을 다음 코드로 바꿉니다.

	```
	Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
	// View the message as an OnlineOrder.
	OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
	Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
	receivedMessage.Complete();
	```

14. 응용 프로그램을 완성했습니다. 솔루션 탐색기에서 MultiTierApp 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택한 후 F5 키를 눌러 전체 응용 프로그램을 테스트할 수 있습니다. 작업자 역할에서 큐의 항목을 처리하고 완료로 표시하므로 메시지 수가 증가하지 않습니다. Azure 계산 에뮬레이터 UI를 표시하여 작업자 역할의 추적 출력을 확인할 수 있습니다. 이 작업은 작업 표시줄의 알림 영역에 있는 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭하고 **Show Compute Emulator UI**를 선택하여 수행할 수 있습니다.

    ![][19]

    ![][20]

## 다음 단계  

서비스 버스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [Azure 서비스 버스][sbmsdn]
* [서비스 버스 서비스 페이지][sbwacom]
* [서비스 버스 큐를 사용하는 방법][sbwacomqhowto]

다중 계층 시나리오에 대한 자세한 내용은 다음을 참조하세요.

* [저장소 테이블, 큐 및 Blob을 사용하는 .NET 다중 계층 응용 프로그램][mutitierstorage]

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [도구 및 SDK 가져오기]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  

<!---HONumber=AcomDC_0928_2016-->