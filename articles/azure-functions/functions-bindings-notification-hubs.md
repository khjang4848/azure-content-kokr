<properties
	pageTitle="Azure Functions 알림 허브 바인딩 | Microsoft Azure"
	description="Azure Functions에서 Azure 알림 허브 바인딩을 사용하는 방법을 파악합니다."
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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Azure Functions 알림 허브 출력 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure 알림 허브 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

함수는 몇 줄의 코드로 구성된 Azure 알림 허브를 사용하여 푸시 알림을 보낼 수 있습니다. 그러나 알림 허브는 사용하려는 PNS(플랫폼 알림 서비스)에 대해 구성되어야 합니다. Azure 알림 허브를 구성하고 알림 수신을 위해 등록하는 클라이언트 응용 프로그램을 개발하는 데 대한 자세한 내용은 [알림 허브 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)을 참조하고 맨 위에 있는 대상 클라이언트 플랫폼을 클릭합니다.

## Azure 알림 허브 출력 바인딩에 대한 function.json

function.json 파일은 다음 속성을 제공합니다.

- `name`: 알림 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: *"notificationHub"*로 설정해야 합니다.
- `tagExpression`: 태그 식을 사용하면 알림을 태그 식과 일치하는 알림을 수신하도록 등록된 일련의 장치에 배달하도록 지정할 수 있습니다. 자세한 내용은 [라우팅 및 태그 식](../notification-hubs/notification-hubs-tags-segment-push-message.md)을 참조하세요.
- `hubName`: Azure 포털에서 알림 허브 리소스의 이름입니다.
- `connection`: 이 연결 문자열은 알림 허브에 대한 *DefaultFullSharedAccessSignature*값으로 설정된 **응용 프로그램 설정** 연결 문자열이어야 합니다.
- `direction`: *"out"*으로 설정해야 합니다.
 
예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Azure 알림 허브 연결 문자열 설정

알림 허브 출력 바인딩을 사용하려면 허브에 대한 연결 문자열을 구성해야 합니다. *통합* 탭에서 알림 허브를 선택하거나 새로 만들어서 이를 수행할 수 있습니다.

알림 허브에 *DefaultFullSharedAccessSignature*에 대한 연결 문자열을 추가하여 기존 허브에 대한 연결 문자열을 수동으로 추가할 수도 있습니다. 이 연결 문자열에서는 알림 메시지를 보내는 함수 액세스 권한을 제공합니다. *DefaultFullSharedAccessSignature* 연결 문자열 값은 Azure 포털의 알림 허브 리소스의 주 블레이드에 있는 **키** 단추에서 액세스할 수 있습니다. 허브에 대한 연결 문자열을 수동으로 추가하려면 다음 단계를 사용합니다.

1. Azure 포털의 **함수 앱** 블레이드에서 **함수 앱 설정 > 앱 서비스 설정으로 이동**을 클릭합니다.

2. **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

3. **연결 문자열** 섹션을 아래로 스크롤하여 알림 허브에 *DefaultFullSharedAccessSignature* 값에 대한 명명된 항목을 추가합니다. 형식을 **사용자 지정**으로 변경합니다.
4. 출력 바인딩에서 연결 문자열 이름을 참조합니다. 위의 예제에서 사용된 **MyHubConnectionString**과 유사합니다.

## Node.js 타이머 트리거에 대한 Azure 알림 허브 코드 예제 

이 예제는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

## F# 타이머 트리거에 대한 Azure Notification Hub 코드 예제

이 예제는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

	let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
	    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]

## C# 큐 트리거에 대한 Azure 알림 허브 코드 예제

이 예제는 `message`를 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

이 예제는 유효한 JSON 문자열을 사용하여 `message`를 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## 알림 유형을 사용하는 Azure 알림 허브 큐 트리거 C# 코드 예제

이 예제는 [Microsoft Azure 알림 허브 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 `Notification` 형식을 사용하는 방법을 보여줍니다. 이 형식 및 라이브러리를 사용하기 위해 함수 앱에 *project.json* 파일을 업로드해야 합니다. project.json 파일은 다음과 유사한 JSON 텍스트 파일입니다.

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

project.json 파일을 업로드하는 자세한 내용은 [project.json 파일 업로드](functions-reference.md#fileupdate)를 참조하세요.

예제 코드:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->