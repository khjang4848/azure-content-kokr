<properties
    pageTitle=".NET으로 Service Bus 토픽 사용 | Microsoft Azure"
    description="Azure에서 .NET으로 Service Bus 토픽 및 구독을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 .NET 응용 프로그램용으로 작성되었습니다."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# 서비스 버스 토픽 및 구독을 사용하는 방법

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며 .NET API를 사용합니다. 여기서 다루는 시나리오에는 토픽 및 구독 만들기, 구독 필터 만들기, 토픽에 메시지 보내기, 구독에서 메시지 받기, 토픽 및 구독 삭제 등이 포함됩니다. 토픽 및 구독에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## 서비스 버스를 사용하도록 응용 프로그램 구성

서비스 버스를 사용하는 응용 프로그램을 만드는 경우 서비스 버스 어셈블리에 대한 참조를 추가하고 해당 네임스페이스를 포함해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 적절한 [NuGet](https://www.nuget.org) 패키지를 다운로드하는 것입니다.

## 서비스 버스 NuGet 패키지 다운로드

[Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)는 Service Bus API를 가져오고 모든 필요한 Service Bus 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다. 프로젝트에서 Service Bus NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
2.  "서비스 버스"를 검색하고 **Microsoft Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한 후 다음의 대화 상자를 닫습니다.

    ![][7]

이제 서비스 버스에 대한 코드를 작성할 준비가 되었습니다.

## Service Bus 연결 문자열 만들기

서비스 버스는 연결 문자열을 사용하여 끝점과 자격 증명을 저장합니다. 하드 코딩하는 대신 구성 파일에 연결 문자열을 저장할 수 있습니다.

- Azure 서비스를 사용하는 경우 Azure 서비스 구성 시스템(.csdef 및 .cscfg 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
- Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: Web.config 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 문서의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 연결 문자열 구성

서비스 구성 메커니즘은 응용 프로그램을 다시 배포하지 않고도 [Azure Portal][]에서 구성 설정을 동적으로 변경할 수 있게 해줍니다. 예를 들어 다음 예에 표시된 대로 서비스 정의(**.csdef**) 파일에 `Setting` 레이블을 추가합니다.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

그런 다음 서비스 구성(.cscfg) 파일에서 값을 지정합니다.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

이전에 설명한 대로 포털에서 검색된 SAS(공유 액세스 서명) 키 이름 및 키 값을 사용합니다.

### Azure 웹 사이트 또는 Azure 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: Web.config)을 사용하는 것이 좋습니다. 다음과 같이 `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

이전에 설명한 대로 [Azure Portal][]에서 검색한 SAS 이름 및 키 값을 사용합니다.

## 토픽 만들기

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 클래스를 사용해서 Service Bus 토픽과 구독에 대한 관리 작업을 수행할 수 있습니다. 이 클래스는 항목을 만들고 열거하고 삭제하는 메서드를 제공합니다.

다음 예제에서는 Azure `CloudConfigurationManager` 클래스를 사용하여 `NamespaceManager` 개체를 생성합니다. 연결 문자열은 Service Bus 네임스페이스의 기준 주소와 관리 권한이 있는 적절한 SAS 자격 증명으로 구성됩니다. 이 연결 문자열은 다음 형식을 사용합니다.

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

이전 섹션의 구성 설정이 제공될 경우 다음 예를 사용합니다.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

토픽의 속성을 설정할 수 있게 해 주는 [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) 메서드 오버로드가 있습니다. 예를 들어 토픽에 전송되는 메시지에 적용할 기본 TTL(Time-To-Live) 값을 설정할 수 있습니다. 이러한 설정은 [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) 클래스를 사용하여 적용됩니다. 다음 예제에서는 최대 크기가 5GB이고 기본 메시지 TTL(Time-To-Live)이 1분인 **TestTopic**이라는 토픽을 만드는 방법을 보여 줍니다.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 개체의 [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) 메서드를 사용하여 네임스페이스 내에 지정된 이름의 토픽이 이미 있는지 확인할 수 있습니다.

## 구독 만들기

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 클래스를 사용하여 토픽 구독을 만들 수도 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

새 구독을 만들 때 필터를 지정하지 않은 경우 **MatchAll** 필터가 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "AllMessages"라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### 필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지를 지정하는 필터를 설정할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 [SqlFilter][] 클래스입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][] 구문을 참조하세요.

다음 예제에서는 사용자 지정 **MessageNumber** 속성이 3보다 큰 메시지만 선택하는 [SqlFilter][] 개체를 사용하여 **HighMessages**라는 구독을 만듭니다.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

마찬가지로, 다음 예제에서는 **MessageNumber** 속성이 3보다 작거나 같은 메시지만 선택하는 [SqlFilter][]를 사용하여 **LowMessages**라는 구독을 만듭니다.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

이제 `TestTopic`으로 메시지를 보내는 경우 **AllMessages** 토픽 구독을 구독하는 수신자에게는 항상 배달되고, **HighMessages** 및 **LowMessages** 토픽 구독을 구독하는 수신자에게는 메시지 콘텐츠에 따라 선택적으로 배달됩니다.

## 토픽에 메시지 보내기

Service Bus 토픽에 메시지를 보내려면 응용 프로그램은 연결 문자열을 사용하여 [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 개체를 만듭니다.

다음 코드는 [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) API 호출을 사용하여 위에서 생성한 **TestTopic** 항목에 대한 [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 개체를 만드는 방법을 보여 줍니다.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Service Bus 토픽으로 전송된 메시지는 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 클래스의 인스턴스입니다. [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 개체에는 표준 속성 집합(예: [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 및 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 개체 생성자에 직렬화된 개체를 전달하여 메시지 본문을 설정할 수 있으며, 적절한 **DataContractSerializer**를 사용하여 개체를 직렬화합니다. 또는 **System.IO.Stream**을 제공할 수 있습니다.

다음 예제는 5개의 테스트 메시지를 앞의 코드 예제에서 얻은 **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 개체로 보내는 방법을 보여 줍니다. 루프의 반복에 따라 각 메시지의 [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) 속성 값이 변경되며 이 값에 따라 해당 메시지를 받는 구독이 결정됩니다.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

서비스 버스 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다. 분할을 사용하는 경우 상한이 더 높습니다. 자세한 내용은 [분할된 메시징 엔티티](service-bus-partitioning.md)를 참조하세요.

## 구독에서 메시지를 받는 방법

구독에서 메시지를 받는 권장 방법은 [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) 개체를 사용하는 것입니다. [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) 개체는 [*ReceiveAndDelete* 및 *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)의 두 가지 모드에서 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, Service Bus가 구독에서 메시지에 대한 읽기 요청을 받으면 메시지를 사용하는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 이용되는 것으로 표시했기 때문에 응용 프로그램이 다시 시작되고 메시지를 다시 사용하기 시작할 때 크래시 전에 사용한 메시지는 누락됩니다.

**PeekLock** 모드(기본 모드)에서는 수신 프로세스가 2단계 작업이므로 누락된 메시지를 허용하지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus가 **Complete** 호출을 확인하면 메시지를 사용되는 것으로 표시하고 구독에서 제거합니다.

다음 예제에서는 기본 **PeekLock** 모드를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 다른 [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 값을 지정하기 위해 [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx)에 다른 오버로드를 사용할 수 있습니다. 다음 예제는 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) 콜백을 사용하여 **HighMessages** 구독에 도착한 메시지를 처리합니다.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

다음 예제에서는 [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 개체를 사용하여 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) 콜백을 구성합니다. [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx)는 받은 메시지에서 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)를 호출할 시점을 수동으로 제어할 수 있도록 **false**로 설정되어 있습니다. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx)이 1분으로 설정되며 자동 갱신 기능이 종료되기 전에 클라이언트가 최대 1분간 대기하고 메시지를 확인하는 새 호출을 만들게 됩니다. 이 속성 값은 클라이언트가 메시지를 검색하지 않는 유료 호출하는 횟수를 줄입니다.

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 메서드 대신 [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 구독 내 메시지의 잠금을 해제하고 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 [완료](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 요청이 실행되기 전에 충돌되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 *최소 한 번 이상 처리*라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지하는 메시지의 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 속성을 사용합니다.

## 토픽 및 구독 삭제

다음 예제에서는 **HowToSample** 서비스 네임스페이스에서 **TestTopic** 항목을 삭제하는 방법을 보여 줍니다.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드는 **TestTopic** 토픽에서 **HighMessages**라는 구독을 삭제하는 방법을 보여 줍니다.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## 다음 단계

이제 서비스 버스 토픽 및 구독의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보십시오.

-   [큐, 토픽 및 구독][]
-   [항목 필터 샘플][]
-   [SqlFilter][]에 대한 API 참조
-   Service Bus 큐로 메시지를 보내고 받는 작동하는 응용 프로그램 빌드: [Service Bus 조정된 메시징 .NET 자습서][].
-   Service Bus 샘플: [Azure 샘플][]에서 다운로드하거나 [개요](service-bus-samples.md)를 참조하세요.

  [Azure portal]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [큐, 토픽 및 구독]: service-bus-queues-topics-subscriptions.md
  [항목 필터 샘플]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Service Bus 조정된 메시징 .NET 자습서]: service-bus-brokered-tutorial-dotnet.md
  [Azure 샘플]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

<!---HONumber=AcomDC_0921_2016-->