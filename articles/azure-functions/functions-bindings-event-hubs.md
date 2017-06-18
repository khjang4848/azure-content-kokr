<properties
	pageTitle="Azure Functions 이벤트 허브 바인딩 | Microsoft Azure"
	description="Azure Functions에서 Azure 이벤트 허브 바인딩을 사용하는 방법을 파악합니다."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
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
	ms.author="wesmc"/>

# Azure Functions 이벤트 허브 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에 대한 [Azure 이벤트 허브](../event-hubs/event-hubs-overview.md) 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Azure 이벤트 허브에 대한 트리거 및 출력 바인딩을 지원합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## Azure 이벤트 허브 트리거 바인딩

Azure 이벤트 허브 트리거를 사용하여 이벤트 허브 이벤트 스트림으로 전송된 이벤트에 응답할 수 있습니다. 트리거 바인딩을 설정하려면 이벤트 허브에 대한 읽기 권한이 있어야 합니다.

#### 이벤트 허브 트리거 바인딩을 위한 function.json

Azure 이벤트 허브 트리거에 대한 *function.json* 파일은 다음 속성을 지정합니다.

- `type`: *eventHubTrigger*로 설정되어야 합니다.
- `name`: 이벤트 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `direction`: *in*으로 설정되어야 합니다.
- `path`: 이벤트 허브의 이름입니다.
- `connection`: 이벤트 허브가 있는 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 이벤트 허브 자체가 아닌 네임스페이스에 대한 **연결 정보** 단추를 클릭하여 이 연결 문자열을 복사합니다. 트리거를 활성화하려면 이 연결 문자열은 적어도 읽기 권한이 있어야 합니다.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Azure 이벤트 허브 트리거 C# 예제
 
위의 예제 function.json을 사용하면 아래의 C# 함수 코드를 사용하여 이벤트 메시지의 본문이 로깅됩니다.
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Azure Event Hub 트리거 F# 예제

위의 예제 function.json을 사용하면 아래의 F# 함수 코드를 사용하여 이벤트 메시지의 본문이 로깅됩니다.

	let Run(myEventHubMessage: string, log: TraceWriter) =
	    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### Azure 이벤트 허브 트리거 Node.js 예제
 
위의 예제 function.json을 사용하면 아래의 Node.js 함수 코드를 사용하여 이벤트 메시지의 본문이 로깅됩니다.
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Azure 이벤트 허브 출력 바인딩

Azure 이벤트 허브 출력 바인딩은 이벤트 허브 이벤트 스트림에 이벤트를 쓰는 데 사용됩니다. 이벤트를 쓰려면 이벤트 허브에 대한 보내기 사용 권한이 있어야 합니다.

#### 이벤트 허브 출력 바인딩에 대한 function.json

Azure 이벤트 허브 출력 바인딩에 대한 *function.json* 파일은 다음 속성을 지정합니다.

- `type`: *eventHub*으로 설정해야 합니다.
- `name`: 이벤트 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `path`: 이벤트 허브의 이름입니다.
- `connection`: 이벤트 허브가 있는 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 이벤트 허브 자체가 아닌 네임스페이스에 대한 **연결 정보** 단추를 클릭하여 이 연결 문자열을 복사합니다. 이 연결 문자열에는 이벤트 허브 스트림으로 메시지를 보내기 위해 보내기 사용 권한이 있어야 합니다.
- `direction`: *out*으로 설정해야 합니다.

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### 출력 바인딩에 대한 Azure 이벤트 허브 C# 코드 예제
 
다음 C# 예제 함수 코드에서는 Event Hub 이벤트 스트림에 이벤트를 쓰는 방법을 보여 줍니다. 이 예제에서는 C# 타이머 트리거에 적용된 위의 이벤트 허브 출력 바인딩을 나타냅니다.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### 출력 바인딩에 대한 Azure Event Hub F# 코드 예제

다음 F# 예제 함수 코드에서는 Event Hub 이벤트 스트림에 이벤트를 쓰는 방법을 보여 줍니다. 이 예제에서는 C# 타이머 트리거에 적용된 위의 이벤트 허브 출력 바인딩을 나타냅니다.

	let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
	    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
	    log.Verbose(msg);
	    outputEventHubMessage <- msg;

#### 출력 바인딩에 대한 Azure 이벤트 허브 Node.js 코드 예제
 
다음 Node.js 예제 함수 코드에서는 이벤트 허브 이벤트 스트림에 이벤트를 쓰는 방법을 보여 줍니다. 이 예제에서는 Node.js 타이머 트리거에 적용된 위의 이벤트 허브 출력 바인딩을 나타냅니다.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->