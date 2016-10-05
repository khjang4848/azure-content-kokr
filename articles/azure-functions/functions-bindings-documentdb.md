<properties
	pageTitle="Azure Functions DocumentDB 바인딩 | Microsoft Azure"
	description="Azure Functions에서 Azure DocumentDB 바인딩을 사용하는 방법을 파악합니다."
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

# Azure Functions DocumentDB 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure DocumentDB 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Azure DocumentDB 입력 바인딩

입력 바인딩은 DocumentDB 컬렉션에서 문서를 로드하고 바인딩에 직접 전달할 수 있습니다. 함수를 호출한 트리거에 따라 문서 ID를 결정할 수 있습니다. C# 함수에서 함수가 성공적으로 종료될 경우 레코드에 변경한 내용을 자동으로 다시 컬렉션에 전송합니다.

#### DocumentDB 입력 바인딩에 대한 function.json

*function.json* 파일은 다음 속성을 제공합니다.

- `name`: 문서에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: "documentdb"로 설정해야 합니다.
- `databaseName`: 문서를 포함하는 데이터베이스입니다.
- `collectionName`: 문서를 포함하는 컬렉션입니다.
- `id`: 검색할 문서의 ID입니다. 이 속성은 "{queueTrigger}"와 유사한 바인딩을 지원하며 이는 큐 메시지의 문자열 값을 문서 ID로 사용합니다
- `connection`: 이 문자열은 DocumentDB 계정에 대한 끝점에 설정된 응용 프로그램 설정이어야 합니다. 통합 탭에서 계정을 선택하는 경우 yourAccount\_DOCUMENTDB 형식을 사용하는 이름으로 새 앱 설정이 만들어집니다. 앱 설정을 수동으로 만들어야 하는 경우 실제 연결 문자열은 AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>; 형식을 사용해야 합니다.
- `direction: *"in"*으로 설정해야 합니다.

예제 *function.json*:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 큐 트리거에 대한 Azure DocumentDB 입력 코드 예제
 
위의 function.json 예제를 사용하여 DocumentDB 입력 바인딩이 큐 메시지 문자열과 일치하는 ID로 문서를 검색하고 '문서' 매개 변수에 전달합니다. 해당 문서가 없는 경우 '문서' 매개 변수가 null이 됩니다. 문서는 함수가 종료되는 경우 새 텍스트 값으로 업데이트됩니다.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### F# 큐 트리거에 대한 Azure DocumentDB 입력 코드 예제

위의 function.json 예제를 사용하여 DocumentDB 입력 바인딩이 큐 메시지 문자열과 일치하는 ID로 문서를 검색하고 '문서' 매개 변수에 전달합니다. 해당 문서가 없는 경우 '문서' 매개 변수가 null이 됩니다. 문서는 함수가 종료되는 경우 새 텍스트 값으로 업데이트됩니다.

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- "This has changed."

다음과 같이 NuGet을 사용하여 `FSharp.Interop.Dynamic` 및 `Dynamitey` 패키지를 패키지 종속성으로 지정하는 `project.json` 파일이 필요합니다.

	{
	  "frameworks": {
	    "net46": {
	      "dependencies": {
	        "Dynamitey": "1.0.2",
	        "FSharp.Interop.Dynamic": "3.0.0"
	      }
	    }
	  }
	}

그러면 NuGet을 사용하여 종속성을 가져오고 스크립트에서 해당 항목을 참조하게 됩니다.

#### Node.js 큐 트리거에 대한 Azure DocumentDB 입력 코드 예제
 
위의 function.json 예제를 사용하여 DocumentDB 입력 바인딩이 큐 메시지 문자열과 일치하는 ID로 문서를 검색하고 `documentIn` 바인딩 속성에 전달합니다. Node.js 함수에서 업데이트된 문서를 컬렉션에 다시 전송하지 않습니다. 그러나 입력 바인딩을 직접 `documentOut`라는 DocumentDB 출력 바인딩에 전달하여 업데이트를 지원할 수 있습니다. 이 코드 예제에서는 입력 문서의 텍스트 속성을 업데이트하고 출력 문서로 설정합니다.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

## <a id="docdboutput"></a> Azure DocumentDB 출력 바인딩

함수는 **Azure DocumentDB 문서** 출력 바인딩을 사용하여 Azure DocumentDB 데이터베이스에 JSON 문서를 작성할 수 있습니다. Azure DocumentDB에 대한 자세한 내용은 [DocumentDB 소개](../documentdb/documentdb-introduction.md) 및 [시작 자습서](../documentdb/documentdb-get-started.md)를 참조하세요.

#### DocumentDB 출력 바인딩에 대한 function.json

function.json 파일은 다음 속성을 제공합니다.

- `name`: 새 문서에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: *"documentdb"*로 설정해야 합니다.
- `databaseName`: 새 문서를 만들 컬렉션을 포함하는 데이터베이스입니다.
- `collectionName`: 새 문서를 만들 컬렉션입니다.
- `createIfNotExists`: 컬렉션이 존재하지 않는 경우 만들 수 있는지 여부를 나타내는 부울 값입니다. 기본값은 *false*입니다. 새 컬렉션이 예약된 처리량을 사용하여 만들어지면 가격 책정 면에서 의미하는 바가 있기 때문입니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
- `connection`: 이 문자열은 DocumentDB 계정에 대한 끝점에 설정된 **응용 프로그램 설정**이어야 합니다. **통합** 탭에서 계정을 선택하는 경우 `yourAccount_DOCUMENTDB` 형식을 사용하는 이름으로 새 앱 설정이 만들어집니다. 앱 설정을 수동으로 만들어야 하는 경우 실제 연결 문자열은 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;` 형식을 사용해야 합니다.
- `direction`: *"out"*으로 설정해야 합니다.
 
예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Node.js 큐 트리거에 대한 Azure DocumentDB 출력 코드 예제

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

출력 문서:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### F# 큐 트리거에 대한 Azure DocumentDB 출력 코드 예제

	open FSharp.Interop.Dynamic
	let Run(myQueueItem: string, document: obj) =
	    document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### C# 큐 트리거에 대한 Azure DocumentDB 출력 코드 예제


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Azure DocumentDB 출력 코드 예제 설정 파일 이름

함수에 문서의 이름을 설정하려는 경우 `id` 값을 설정합니다. 예를 들어 직원에 대한 JSON 콘텐츠가 다음과 유사하게 큐로 삭제된 경우:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

큐 트리거 함수에서 다음 C# 코드를 사용할 수 있습니다.
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

또는 해당하는 F# 코드:

	open FSharp.Interop.Dynamic
	open Newtonsoft.Json

	type Employee = {
	    id: string
	    name: string
	    employeeId: string
	    address: string
	}

	let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
	    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
	    let employee = JObject.Parse(myQueueItem)
	    employeeDocument <-
	        { id = sprintf "%s-%s" employee?name employee?employeeId
	          name = employee?name
	          employeeId = employee?id
	          address = employee?address }

예제 출력:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->