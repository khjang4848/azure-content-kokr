<properties
	pageTitle="Azure Functions 서비스 버스 트리거 및 바인딩 | Microsoft Azure"
	description="Azure Functions에서 Azure 서비스 버스 트리거 및 바인딩을 사용하는 방법을 파악합니다."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Azure Functions의 큐 및 항목에 대한 서비스 버스 트리거 및 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure 서비스 버스 트리거 및 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> 서비스 버스 큐 또는 항목 트리거

#### function.json

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: 큐 또는 항목 메시지, 또는 큐 또는 항목에 대한 함수 코드에 사용되는 변수 이름입니다.
- `queueName`: 큐 트리거에만 해당하는, 폴링할 큐의 이름입니다.
- `topicName`: 항목 트리거에만 해당하는, 폴링할 항목의 이름입니다.
- `subscriptionName`: 항목 트리거에만 해당하는, 구독 이름입니다.
- `connection`: 서비스 버스 연결 문자열을 포함하는 앱 설정의 이름입니다. 연결 문자열은 서비스 버스 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다. 연결 문자열에 관리 권한이 없으면 `accessRights` 속성을 설정합니다. `connection`을 비워두면, 트리거 또는 바인딩은 AzureWebJobsServiceBus 앱 설정에 의해 지정되는 함수 앱에 대한 기본 서비스 버스 연결 문자열로 작동합니다.
- `accessRights`: 연결 문자열에 사용할 수 있는 액세스 권한을 지정합니다. 기본값은 `manage`입니다. 관리 권한을 제공하지 않는 연결 문자열을 사용하는 경우 `listen`으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업을 시도하고 실패할 수 있습니다.
- `type`: *serviceBusTrigger*로 설정해야 합니다.
- `direction`: *in*으로 설정해야 합니다.

서비스 버스 큐 트리거에 대한 *function.json* 예제

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### 서비스 버스 큐 메시지를 처리하는 C# 코드 예제

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Service Bus 큐 메시지를 처리하는 F# 코드 예제

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### 서비스 버스 큐 메시지를 처리하는 Node.js 코드 예제

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### 지원되는 형식

서비스 버스 큐 메시지를 다음 중 원하는 형식으로 역직렬화할 수 있습니다.

* 개체(JSON의)
* string
* 바이트 배열
* `BrokeredMessage`(C#)

#### <a id="sbpeeklock"></a> PeekLock 동작

함수 런타임은 `PeekLock` 모드로 메시지를 받아 함수가 정상적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 잠금이 자동으로 갱신됩니다.

#### <a id="sbpoison"></a> 포이즌 메시지 처리

서비스 버스는 Azure Functions 구성 또는 코드로 제어되거나 구성될 수 있는 자체 포이즌 메시지 처리를 수행합니다.

#### <a id="sbsinglethread"></a> 단일 스레드

기본적으로 함수 런타임은 여러 개의 큐 메시지를 동시에 처리합니다. 런타임이 큐 또는 항목 메시지를 한 번에 하나만 처리하도록 하려면, *host.json* 파일에서 `serviceBus.maxConcurrrentCalls`을 1로 설정합니다. *host.json* 파일에 대한 자세한 내용은 개발자 참조 문서의 [폴더 구조](functions-reference.md#folder-structure) 또는 WebJobs.Script 리포지토리 wiki에서 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 참조하세요.

## <a id="sboutput"></a> 서비스 버스 큐 또는 항목 출력 바인딩

#### function.json

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: 큐 또는 큐 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `queueName`: 큐 트리거에만 해당하는, 폴링할 큐의 이름입니다.
- `topicName`: 항목 트리거에만 해당하는, 폴링할 항목의 이름입니다.
- `subscriptionName`: 항목 트리거에만 해당하는, 구독 이름입니다.
- `connection`: 서비스 버스 트리거에 대한 것과 같습니다.
- `accessRights`: 연결 문자열에 사용할 수 있는 액세스 권한을 지정합니다. 기본값은 `manage`입니다. 관리 권한을 제공하지 않는 연결 문자열을 사용하는 경우 `send`으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업(예: 큐 만들기)을 시도하고 실패할 수 있습니다.
- `type`: *serviceBus*로 설정해야 합니다.
- `direction`: *out*으로 설정해야 합니다.

서비스 버스 큐 메시지를 작성하기 위해 타이머 트리거를 사용하는 *function.json* 예제

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### 지원되는 형식

Azure Functions은 다음 형식으로부터 서비스 버스 큐 메시지를 만들 수 있습니다.

* 개체(항상 JSON 메시지를 만들고, 함수가 종료될 때 값이 null이면 null 개체를 포함하는 메시지 생성)
* 문자열(함수가 종료될 때 값이 null이 아니면, 메시지 생성)
* 바이트 배열(문자열처럼 작동)
* `BrokeredMessage`(C#, 문자열처럼 작동)

C# 함수에서 여러 메시지를 만들려면, `ICollector<T>` 또는 `IAsyncCollector<T>`를 사용할 수 있습니다. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

#### 서비스 버스 큐 메시지를 작성하는 C# 코드 예제

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Service Bus 큐 메시지를 작성하는 F# 코드 예제

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### 서비스 버스 큐 메시지를 작성하는 Node.js 코드 예제

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->