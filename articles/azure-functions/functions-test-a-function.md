<properties
   pageTitle="Azure Functions 테스트 | Microsoft Azure"
   description="Postman, cURL 및 Node.js를 사용하여 Azure Functions 테스트"
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처, 테스트"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="wesmc"/>

# Azure Functions 테스트

## 개요

이 자습서에서는 함수 테스트에 대한 다른 접근 방법을 살펴보겠습니다. 쿼리 문자열 매개 변수 또는 요청 본문을 통해 입력을 허용하는 http 트리거 함수를 정의합니다. 기본 **HttpTrigger Node.js 함수** 템플릿 코드는 `name` 쿼리 문자열 매개 변수를 지원합니다. 또한 요청 본문의 사용자에 대한 `address` 정보와 함께 해당 매개 변수를 지원하는 코드를 추가합니다.

## 테스트용 함수 만들기

이 자습서의 대부분에서는 새 함수를 만들 때 사용할 수 있는 **HttpTrigger Nodejs 함수** 템플릿의 약간 수정된 버전을 사용합니다. 새 함수를 만드는 데 도움이 필요한 경우 [첫 번째 Azure 함수 만들기 자습서](functions-create-first-azure-function.md)를 검토할 수 있습니다. [Azure 포털]에서 테스트 함수를 만들 때에는 **HttpTrigger Nodejs 함수** 템플릿만 선택하면 됩니다.

기본 함수 템플릿은 기본적으로 요청 본문 또는 쿼리 문자열 매개 변수의 이름(`name=<your name>`)을 되돌려 주는 hello world 함수입니다. 또한 요청 본문의 JSON 콘텐츠로 이름 및 주소를 제공할 수 있도록 하는 코드를 업데이트합니다. 그러면 함수는 사용 가능한 경우 클라이언트에 다시 표시합니다.

함수를 테스트에 사용할 다음 코드로 업데이트합니다.

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## 도구를 사용하여 함수 테스트

### cURL을 사용하여 테스트

종종 소프트웨어를 테스트할 때 명령줄이 응용 프로그램을 디버그하는 데 도움이 되며 함수와 차이가 없습니다.

위의 함수를 테스트하려면 포털에서 **함수 Url**을 복사합니다. 다음과 같은 형식이 됩니다.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
이는 함수를 트리거하는 Url이며, 함수에 대한 Get(`-G` 또는 `--get`) 요청을 수행하는 명령줄에서 cURL 명령을 사용하여 테스트할 수 있습니다.

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
이 특정 예제에서는 cURL 명령에서 데이터(`-d`)로 전달될 수 있는 쿼리 문자열 매개 변수가 필요합니다.

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Enter 키를 누르면 명령줄에 함수의 출력이 표시됩니다.

![](./media/functions-test-a-function/curl-test.png)

포털 **로그** 창에 다음과 유사한 출력이 함수를 실행하는 동안 기록됩니다.

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### 브라우저를 사용하여 테스트

매개 변수가 필요하지 않거나 쿼리 문자열 매개 변수만 필요한 함수는 브라우저를 사용하여 테스트할 수 있습니다.

위에서 정의한 함수를 테스트하려면 포털에서 **함수 Url**을 복사합니다. 다음과 같은 형식이 됩니다.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

`<Enter a name here>` 자리 표시자에 대한 실제 이름을 사용하여 다음과 같이 `name` 쿼리 문자열 매개 변수를 추가합니다.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

브라우저에 URL을 붙여넣으면 다음과 유사한 응답을 얻게 됩니다.

![](./media/functions-test-a-function/browser-test.png)

포털 **로그** 창에 다음과 유사한 출력이 함수를 실행하는 동안 기록됩니다.

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"ko-KR"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Postman을 사용하여 테스트

Postman은 대부분의 함수 테스트에 권장되는 도구입니다. Postman을 설치하려면 [Postman 가져오기](https://www.getpostman.com/)를 참조하세요. Postman은 다양한 특성의 HTTP 요청에 대한 제어를 제공합니다.

> [AZURE.TIP] 익숙한 REST 클라이언트를 사용합니다. 다음은 Postman의 몇 가지 대안입니다.
> 
> * [Fiddler](http://www.telerik.com/fiddler)
> * [Paw](https://luckymarmot.com/paw)

Postman에서 요청 본문을 사용하여 함수를 테스트하려면

1. Chrome 브라우저 창의 왼쪽 위에 있는 **앱** 단추를 눌러 Postman을 시작합니다.
2. **함수 Url**을 복사하여 Postman에 붙여넣습니다. 액세스 코드 쿼리 문자열 매개 변수를 포함합니다.
3. HTTP 메서드를 **POST**로 변경합니다.
4. **본문** > **원시**를 클릭하여 다음과 유사한 JSON 요청 본문을 추가합니다.

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. **보내기**를 클릭합니다.

다음 이미지는 이 자습서의 간단한 에코 함수 예제 테스트를 보여 줍니다.

![](./media/functions-test-a-function/postman-test.png)

포털 **로그** 창에 다음과 유사한 출력이 함수를 실행하는 동안 기록됩니다.

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ko-KR"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### 저장소 탐색기를 사용하여 Blob 트리거 테스트

[Microsoft Azure 저장소 탐색기](http://storageexplorer.com/)를 사용하여 Blob 트리거 함수를 테스트할 수 있습니다.

1. 함수 앱에 대한 [Azure Portal]에서 새 C#, F# 또는 노드 Blob 트리거 함수를 만듭니다. 모니터링할 경로를 Blob 컨테이너의 이름으로 설정합니다. 예:

		files

2. 사용하려는 저장소 계정을 선택하거나 만들려면 **+** 단추를 클릭합니다. 그런 다음 **Create**를 클릭합니다.

3. 다음 텍스트로 텍스트 파일을 만들고 저장합니다.

		A text file for blob trigger function testing.

4. [Microsoft Azure 저장소 탐색기](http://storageexplorer.com/)를 실행하고 모니터링되고 있는 저장소 계정의 Blob 컨테이너에 연결합니다.

5. **업로드** 단추를 클릭하고 텍스트 파일을 업로드합니다.

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	기본 Blob 트리거 함수 코드는 로그에서 Blob 처리를 보고합니다.

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## 함수 내에서 함수 테스트

### 함수 포털 실행 단추를 사용하여 테스트

포털에서는 몇 가지 제한된 테스트를 수행할 수 있는 **실행** 단추를 제공합니다. 실행 단추를 사용하여 요청 본문을 제공할 수 있지만 쿼리 문자열 매개 변수를 제공하거나 요청 헤더를 업데이트할 수 없습니다.

**요청 본문** 필드에서 다음과 유사한 JSON 문자열을 추가하여 앞에서 만든 HTTP 트리거 함수를 테스트한 다음 **실행** 단추를 클릭합니다.

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

포털 **로그** 창에 다음과 유사한 출력이 함수를 실행하는 동안 기록됩니다.

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ko-KR"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### 타이머 트리거를 사용하여 테스트

일부 함수는 앞에서 언급한 도구를 사용하여 제대로 테스트할 수 없습니다. 예를 들어 [Azure 큐 저장소](../storage/storage-dotnet-how-to-use-queues.md)에 메시지를 놓을 때 실행되는 큐 트리거 함수입니다. 언제나 큐에 메시지를 놓는 코드를 작성할 수 있으며 콘솔 프로젝트의 이러한 예는 아래 제공됩니다. 그러나 함수를 직접 테스트하는 데 사용할 수 있는 또 다른 방법이 있습니다.

큐 출력 바인딩으로 구성된 타이머 트리거를 사용할 수 있습니다. 타이머 트리거 코드가 해당 큐에 테스트 메시지를 작성할 수 있습니다. 이 섹션에서는 예제를 통해 살펴봅니다.

Azure Functions 바인딩 사용에 대한 자세한 내용은 [Azure Functions 개발자 참조](functions-reference.md)를 참조하세요.


#### 테스트용 큐 트리거 만들기

이 방법을 시연하려면 먼저 `queue-newusers`라는 큐를 테스트하려는 큐 트리거 함수를 만듭니다. 이 함수는 Azure 큐 저장소에 놓은 새 사용자에 대한 이름 및 주소 정보를 처리합니다.

> [AZURE.NOTE] 다른 큐 이름을 사용하는 경우 해당 이름이 [큐 및 메타데이터 명명](https://msdn.microsoft.com/library/dd179349.aspx) 규칙을 준수해야 합니다. 그러지 않으면 HTTP 상태 코드 400(“잘못된 요청”)이 발생합니다.

1. 함수 앱에 대한 [Azure 포털]에서 **새 함수** > **QueueTrigger - C#**을 클릭합니다.
2. 큐 함수가 모니터링할 큐 이름을 입력합니다.

		queue-newusers 

3. **+**(추가) 단추를 클릭하여 사용하려는 저장소 계정을 선택하거나 만듭니다. 그런 다음 **Create**를 클릭합니다.
4. 기본 큐 함수 템플릿 코드에 대한 로그 항목을 모니터링할 수 있도록 포털 브라우저 창을 그대로 열어 둡니다.


#### 큐에 메시지를 놓는 타이머 트리거 만들기

1. 새 브라우저 창에서 [Azure 포털]을 열고 함수 앱으로 이동합니다.
2. **새 함수** > **TimerTrigger - C#**을 클릭합니다. 타이머 코드가 큐 함수 테스트를 실행하는 빈도를 설정하는 cron 식을 입력합니다. 그런 다음 **Create**를 클릭합니다. 테스트를 30초마다 실행하려는 경우 다음 [CRON 식](https://wikipedia.org/wiki/Cron#CRON_expression)을 사용할 수 있습니다.

		*/30 * * * * *


2. 새 타이머 트리거에 대한 **통합** 탭을 클릭합니다.
3. **출력**에서 **+ 새 출력** 단추를 클릭합니다. 그런 다음 **큐** 및 **선택** 단추를 클릭합니다.
4. 타이머 함수 코드에서 사용할 **큐 메시지 개체**에 사용하는 이름을 확인합니다.

		myQueue

4. 메시지가 전송될 큐 이름을 입력합니다.

		queue-newusers 

3. **+**(추가) 단추를 클릭하여 큐 트리거와 함께 이전에 사용한 저장소 계정을 선택합니다. 그런 다음 **Save**를 클릭합니다.
4. 타이머 트리거에 대한 **개발** 탭을 클릭합니다.
5. 위에 표시된 동일한 큐 메시지 개체 이름을 사용하는 한 C# 타이머 함수에 대해 다음 코드를 사용할 수 있습니다. 그런 다음 **저장**을 클릭합니다.

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

이때 예제 cron 식을 사용하는 경우 C# 타이머 함수가 30초마다 실행됩니다. 타이머 함수에 대한 로그에서 각 실행을 보고합니다.

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

큐 함수에 대한 브라우저 창에 처리 중인 각 메시지가 나타납니다.

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## 코드를 사용하여 함수 테스트

### 코드를 사용하여 HTTP 트리거 함수 테스트: Node.js

http 요청을 실행하는 Node.js 코드를 사용하여 Azure 함수를 테스트할 수 있습니다.

다음을 수행해야 합니다.

- 요청 옵션에서 `host`를 함수 앱 호스트로 설정
- `path`에서 함수 이름 설정
- `path`에서 액세스 코드(`<your code>`) 설정

코드 예제:

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



출력:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
포털 **로그** 창에 다음과 유사한 출력이 함수를 실행하는 동안 기록됩니다.

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### 코드를 사용하여 큐 트리거 함수 테스트: C# #

앞에서 큐에 메시지를 놓는 코드를 사용하여 큐 트리거를 테스트하는 방법에 대해 설명했습니다. 다음 예제 코드는 [Azure 큐 저장소 시작](../storage/storage-dotnet-how-to-use-queues.md) 자습서에 제공된 C# 코드를 기반으로 합니다. 해당 링크에서 다른 언어에 대한 코드를 사용할 수도 있습니다.

콘솔 앱에서 이 코드를 테스트하려면 다음을 수행해야 합니다.

- [app.config 파일에서 저장소 연결 문자열을 구성합니다](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string).
- 이 코드는 런타임 동안 새 사용자에 대한 이름 및 주소를 명령줄 인수로 허용합니다. 앱에 대한 매개 변수로 `name` 및 `address`를 전달합니다. 예를 들어 `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`과 같습니다.


C# 코드 예제:

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

큐 함수에 대한 브라우저 창에 처리 중인 각 메시지가 나타납니다.

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure Portal]: https://portal.azure.com
[Azure 포털]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->