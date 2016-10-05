<properties 
    pageTitle="서비스 버스 조정된 메시징 .NET 자습서 | Microsoft Azure"
    description="조정된 메시징 .NET 자습서"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# 서비스 버스 조정된 메시징 .NET 자습서

Azure 서비스 버스는 두 가지 포괄적인 메시징 솔루션을 제공합니다. 그 중 하나는 다양한 전송 프로토콜과, SOAP, WS-* 및 REST 등을 망라하는 웹 서비스 표준을 지원하는 클라우드에서 실행되는 중앙집중식 "릴레이" 서비스를 통해서입니다. 클라이언트는 온-프레미스 서비스에 대한 직접 연결이 필요 없고 서비스가 상주하는 위치를 알 필요도 없으며, 온-프레미스 서비스는 방화벽에 인바운드 포트가 열려 있지 않아도 됩니다.

두 번째 메시징 솔루션은 "조정된" 메시징 기능을 구현합니다. 이 기능은 서비스 버스 메시징 인프라를 사용하여 게시-구독, 일시 분리, 부하 분산 시나리오를 지원하는 일종의 비동기 또는 분리형 메시징 기능이라고 할 수 있습니다. 분리된 통신에는 많은 장점이 있습니다. 예를 들어 필요하면 클라이언트와 서버가 연결되고 비동기 방식으로 작업을 수행할 수 있습니다.

이 자습서는 서비스 버스 조정된 메시징의 핵심 구성 요소 중 하나인 큐에 대한 실전 경험을 제공하기 위한 것입니다. 이 자습서의 항목을 순서대로 수행하고 나면 메시지 목록으로 채워진 응용 프로그램이 생기며, 큐를 만들고 큐에 메시지를 보내게 됩니다. 마지막으로, 응용 프로그램이 큐로부터 메시지를 받아 표시한 다음 그 리소스를 정리하고 종료합니다. Service Bus Relay를 사용하는 응용 프로그램 빌드 방법에 대해 설명하는 해당 자습서는 [Service Bus 릴레이된 메시징 자습서](../service-bus-relay/service-bus-relay-tutorial.md)를 참조하세요.

## 소개 및 필수 구성 요소

큐는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다. FIFO에서는 일반적으로 메시지가 큐에 추가된 임시 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다. 큐 사용의 주요 장점은 응용 프로그램의 *일시 분리*입니다. 즉 메시지가 큐에 지속적으로 저장되어 있으므로 생산자와 소비자가 동시에 메시지를 보내고 받을 필요가 없습니다. 관련된 이점은 *부하 평준화*로 생산자와 소비자가 서로 다른 속도로 메시지를 주고받을 수 있습니다.

이 자습서를 시작하기 전에 수행해야 하는 일부 관리 및 필수 구성 요소 단계는 다음과 같습니다. 첫 번째는 서비스 네임스페이스를 만들고 SAS(공유 액세스 서명) 키를 확보합니다. 네임스페이스는 서비스 버스를 통해 노출되는 각 응용 프로그램에 대한 응용 프로그램 경계를 제공합니다. SAS 키는 서비스 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 서비스 네임스페이스 및 SAS 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

### 서비스 네임스페이스 만들기 및 SAS 키 얻기

첫 단계는 서비스 네임스페이스를 만들고 [SAS(공유 액세스 서명)](../service-bus/service-bus-sas-overview.md) 키를 확보합니다. 네임스페이스는 서비스 버스를 통해 노출되는 각 응용 프로그램에 대한 응용 프로그램 경계를 제공합니다. SAS 키는 서비스 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 서비스 네임스페이스 및 SAS 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

다음 단계는 Visual Studio 프로젝트를 만들고, 메시지의 쉼표 분리 목록을 강력한 형식의[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) 개체로 로드하는 두 도우미 함수를 작성하는 것입니다.

### Visual Studio 프로젝트 만들기

1. 시작 메뉴에서 프로그램을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭하여 Visual Studio를 관리자 권한으로 엽니다.

1. 새 콘솔 응용 프로그램 프로젝트를 만듭니다. **파일** 메뉴를 클릭하고 **새로 만들기**, **프로젝트**를 차례로 클릭합니다. **새 프로젝트** 대화 상자에서 **Visual C#**을 클릭하고(**Visual C#**이 보이지 않으면 **다른 언어** 아래 확인) **콘솔 응용 프로그램** 템플릿을 클릭하고 이름을 **QueueSample**로 지정합니다. 기본 **위치**를 사용합니다. **확인**을 클릭하여 프로젝트를 만듭니다.

1. NuGet 패키지 관리자를 사용하여 서비스 버스 라이브러리를 프로젝트에 추가하려면:
	1. 솔루션 탐색기에서 **QueueSample**프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
	2. **Nuget 패키지 관리** 대화 상자에서 **찾아보기** 탭을 클릭하고 **Azure Service Bus**를 검색한 후 **설치**를 클릭합니다. <br />
1. 솔루션 탐색기에서 Program.cs 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. 네임스페이스 이름을 기본 이름인 `QueueSample`에서 `Microsoft.ServiceBus.Samples`로 변경합니다.

	```
	Microsoft.ServiceBus.Samples
	{
	    ...
	```

1. 다음 코드와 같이 `using` 문을 수정합니다.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	```

1. Data.csv라는 텍스트 파일을 만들고 다음 쉼표 구분 텍스트에 복사합니다.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Data.csv 파일을 저장하고 닫은 뒤 저장 위치를 기억해 둡니다.

1. 솔루션 탐색기에서 프로젝트 이름(이 예제에서는 **QueueSample**)을 마우스 오른쪽 단추로 클릭하고 **추가**, **기존 항목**을 클릭합니다.

1. 6단계에서 만든 Data.csv 파일을 찾습니다. 파일을 클릭한 다음 **추가**를 클릭합니다. 파일 형식 목록에서 **모든 파일(*.*)**을 선택했는지 확인합니다.

### 메시지 목록을 구문 분석하는 메서드 만들기

1. `Main()` 메서드 앞 `Program` 클래스에서 두 변수를 선언합니다. 하나는 **DataTable** 형식으로, Data.csv의 메시지 목록을 포함하게 됩니다. 또 다른 변수는 List 형식 개체로, 강력한 형식의 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)입니다. 후자는 이 자습서에서 나중에 사용하게 되는 조정된 메시지 목록입니다.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. `Main()`의 외부에서 다음과 같이 Data.csv의 메시지 목록을 구문 분석하고 [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) 테이블에 메시지를 로드하는 `ParseCSV()` 메서드를 정의합니다. 이 메서드는 **DataTable** 개체를 반환합니다.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. `Main()` 메서드에서 `ParseCSVFile()` 메서드를 호출하는 문을 추가합니다.

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### 메시지 목록을 로드하는 메서드 만들기

1. `Main()`의 외부에서 `ParseCSVFile()`이 반환한 **DataTable** 개체를 취하여 강력한 형식의 broker 저장 메시지 목록에 테이블을 로드하는 `GenerateMessages()` 메서드를 정의합니다. 그러면 이 메서드는 다음 예제처럼 **List** 개체를 반환합니다.

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. `Main()`에서 `ParseCSVFile()`에 대한 호출 바로 뒤에 인수 형태로 `ParseCSVFile()`에서의 반환 값을 통해 `GenerateMessages()` 메서드를 호출하는 문을 추가합니다.

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### 사용자 자격 증명 얻기

1. 먼저 이러한 값을 저장할 전역 문자열 변수를 세 개를 만듭니다. 이 변수는 이전 변수 선언 바로 뒤에 선언합니다. 예를 들면 다음과 같습니다.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList; 

	        // Add these variables
			private static string ServiceNamespace;
	        private static string sasKeyName = "RootManageSharedAccessKey";
	        private static string sasKeyValue;
	        …
	```

1. 다음으로 서비스 네임스페이스와 SAS 키를 받아 저장하는 함수를 만듭니다. 이 메서드를 `Main()` 외부에 추가합니다. 예:

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. `Main()`에서 `GenerateMessages()`에 대한 호출 바로 뒤에 `CollectUserInput()` 메서드를 호출하는 문을 추가합니다.

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### 솔루션을 빌드합니다.

Visual Studio의 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하거나 **Ctrl+Shift+B** 키를 눌러 지금까지 수행한 작업의 정확성을 확인합니다.

## 관리 자격 증명 만들기

이 단계에서는 응용 프로그램의 권한을 부여 받은 공유 액세스 서명 (SAS) 자격 증명을 만들기 위해 사용할 관리 작업을 정의합니다.

1. 명확성을 위해 이 자습서에서는 모든 큐 작업을 별도의 메서드에 배치합니다. `Program` 클래스에서 `Main()` 메서드 뒤에, 비동기 `Queue()` 메서드를 만듭니다. 예:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static async Task Queue()
	{
	}
	```

1. 다음 단계는 [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 개체를 사용하여 SAS 자격 증명을 만드는 것입니다. 만들기 메서드는 `CollectUserInput()` 메서드에서 얻은 SAS 키 이름과 값을 취합니다. `Queue()` 메서드에 다음 코드를 추가합니다.

	```
	static async Task Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```

2. 이전 단계에서 확보한 네임스페이스 이름과 관리 자격 증명을 인수 형태로 포함하는 URI로 새로운 네임스페이스 관리 개체를 만듭니다. 이전 단계에서 추가한 코드 바로 뒤에 이 코드를 추가합니다. `<yourNamespace>`를 서비스 네임스페이스의 이름으로 바꿔야 합니다.
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

### 예

이 시점에서 코드는 다음과 유사합니다.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## 큐에 메시지 보내기

이 단계에서는 큐를 만든 다음 조정된 메시지 목록에 포함된 메시지를 해당 큐로 보냅니다.

### 큐 만들기 및 큐에 메시지 보내기

1. 먼저 큐를 만듭니다. 예를 들어 이 큐를 `myQueue`라고 명명하고 마지막 단계에서 `Queue()` 메서드에 추가한 관리 작업 직후에 선언합니다.

	```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. `Queue()` 메서드에서 새로 만든 서비스 버스 URI를 통해 인수 형태로 메시징 팩터리 개체를 만듭니다. 앞 단계에서 추가한 관리 작업 바로 뒤에 다음 코드를 추가합니다. `<yourNamespace>`를 서비스 네임스페이스의 이름으로 바꿔야 합니다.

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
	```

1. 다음으로 [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 클래스를 사용하여 큐 개체를 만듭니다. 마지막 단계에서 추가한 코드 바로 뒤에 다음 코드를 추가합니다.

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. 다음으로, 앞에서 만든 조정된 메시지 목록을 통해 루프를 실행하여 각각을 큐에 보내는 코드를 추가합니다. 이전 단계에서 `CreateQueueClient()` 문 바로 뒤에 다음 코드를 추가합니다.
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    await myQueueClient.SendAsync(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## 큐에서 메시지 받기

이 단계에서는 이전 단계에서 만든 큐에서 메시지 목록을 가져옵니다.

### 수신기를 만들고 큐에서 메시지 수신

`Queue()` 메서드에서 [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) 메서드를 사용하여 큐를 반복하고 메시지를 수신하여 콘솔에 각각의 메시지를 출력합니다. 이전 단계에서 추가한 코드 바로 뒤에 다음 코드를 추가합니다.

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
	
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

메시지 처리를 시뮬레이션하는 데 `Thread.Sleep`만 사용되며 아마도 실제 메시징 응용 프로그램에는 필요하지 않습니다.

### 큐 메서드 종료 및 리소스 정리

앞의 코드 바로 아래에 다음 코드를 추가하여 메시지 팩터리와 큐 리소스를 정리합니다.

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### Queue 메서드 호출

마지막 단계는 `Main()`에서 `Queue()` 메서드를 호출하는 문을 추가하는 것입니다. 다음의 강조 표시된 코드 줄을 Main()의 끝에 추가합니다.
	
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
	
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
	
    // Add this call
    Queue();
}
```

### 예

다음 코드는 전체 **QueueSample** 응용 프로그램을 포함합니다.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## QueueSample 빌드 및 실행

이제 이전 단계를 모두 완료했으므로 **QueueSample** 응용 프로그램을 빌드하여 실행할 수 있습니다.

### QueueSample 응용 프로그램 빌드

Visual Studio의 **빌드** 메뉴에서 **솔루션 빌드**를 클릭하거나 **Ctrl+Shift+B** 키를 누릅니다. 오류를 발생할 경우 앞 단계의 마지막에 있는 전체 예제를 기준으로 코드가 정확한지 확인합니다.

## 다음 단계

이 자습서에서는 서비스 버스 "조정된" 메시징 기능을 사용하여 서비스 버스 클라이언트 응용 프로그램 및 서비스를 빌드하는 방법을 보여줍니다. Service Bus [Relay](service-bus-messaging-overview.md#Relayed-messaging)를 사용하는 유사한 자습서는 [Service Bus 릴레이된 메시징 자습서](../service-bus-relay/service-bus-relay-tutorial.md)를 참조하세요.

[서비스 버스](https://azure.microsoft.com/services/service-bus/)에 대한 자세한 내용은 다음 항목을 참조하세요.

- [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
- [서비스 버스 기본 사항](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [서비스 버스 아키텍처](../service-bus/service-bus-architecture.md)

<!---HONumber=AcomDC_0928_2016-->