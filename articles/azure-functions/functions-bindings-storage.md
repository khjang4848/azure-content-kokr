<properties
	pageTitle="Azure Storage에 대한 Azure Functions 트리거 및 바인딩 | Microsoft Azure"
	description="Azure Functions에서 Azure Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
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
	ms.author="chrande"/>

# Azure 저장소에 대한 Azure Functions 트리거 및 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure 저장소 트리거 및 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="storagequeuetrigger"></a> Azure 저장소 큐 트리거

#### 저장소 큐 트리거에 대한 function.json

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: 큐 또는 큐 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `queueName`: 폴링할 큐의 이름입니다. 큐 명명 규칙은 [큐 및 메타데이터 명명](https://msdn.microsoft.com/library/dd179349.aspx)을 참조하세요.
- `connection`: 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. `connection`을 비워두면, 트리거는 AzureWebJobsStorage 앱 설정에 의해 지정되는 함수 앱에 대한 기본 저장소 연결 문자열로 작동합니다.
- `type`: *queueTrigger*로 설정해야 합니다.
- `direction`: *in*으로 설정해야 합니다.

저장소 큐 트리거에 대한 *function.json* 예제:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### 큐 트리거를 지원하는 형식

큐 메시지를 다음 중 원하는 형식으로 역직렬화할 수 있습니다.

* 개체(JSON의)
* String
* 바이트 배열
* `CloudQueueMessage`(C#)

#### 큐 트리거 메타데이터

이러한 변수 이름을 사용하여 함수에서 큐 메타데이터를 얻을 수 있습니다.

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger(큐 메시지 텍스트를 문자열로 검색하는 다른 방법)

이 C# 코드 예제에서는 큐 메타데이터를 검색하고 기록합니다.

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### 포이즌 큐 메시지 처리

함수가 실패하게 만드는 내용이 포함된 메시지를 *포이즌 메시지*라고 합니다. 함수가 실패할 경우 큐 메시지가 삭제되지 않고 결국 다시 선택되어 주기가 반복됩니다. SDK에서 제한된 반복 횟수 후에 자동으로 주기를 중단하거나 수동으로 중단할 수 있습니다.

SDK는 최대 5회까지 함수를 호출하여 큐 메시지를 처리합니다. 다섯 번째 시도가 실패하면 메시지가 포이즌 큐로 이동됩니다.

포이즌 큐의 이름은 *{originalqueuename}*-poison으로 지정됩니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다.

포이즌 메시지를 수동으로 처리하려는 경우 `dequeueCount`을 확인하여 처리를 위해 메시지를 선택한 횟수를 가져올 수 있습니다.

## <a id="storagequeueoutput"></a> Azure 저장소 큐 출력 바인딩

#### 저장소 큐 출력 바인딩에 대한 function.json 예제

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: 큐 또는 큐 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `queueName`: 큐의 이름입니다. 큐 명명 규칙은 [큐 및 메타데이터 명명](https://msdn.microsoft.com/library/dd179349.aspx)을 참조하세요.
- `connection`: 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. `connection`을 비워두면, 트리거는 AzureWebJobsStorage 앱 설정에 의해 지정되는 함수 앱에 대한 기본 저장소 연결 문자열로 작동합니다.
- `type`: *queue*로 설정해야 합니다.
- `direction`: *out*으로 설정해야 합니다.

큐 트리거를 사용하고 큐 메시지를 쓰는 저장소 큐 출력 바인딩에 대한 *function.json* 예제:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### 큐 출력 바인딩을 지원하는 형식

`queue` 바인딩은 다음 형식을 큐 메시지로 직렬화할 수 있습니다.

* 개체(C#의 경우 `out T`, 함수가 종료될 때 매개 변수가 null인 경우 null 개체가 포함된 메시지 생성)
* 문자열(C#의 경우 `out string`, 함수가 종료될 때 매개 변수 값이 null이 아닌 경우 큐 메시지 생성)
* 바이트 배열(C#의 경우 `out byte[]`, 문자열처럼 작동)
* `out CloudQueueMessage`(C#, 문자열처럼 작동)

C#의 경우 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.

#### 큐 출력 바인딩 코드 예제

이 C# 코드 예제에서는 각 입력 큐 메시지에 단일 출력 큐 메시지를 작성합니다.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

이 C# 코드 예제는 `ICollector<T>`을 사용하여 여러 메시지를 작성합니다(비동기 함수에 `IAsyncCollector<T>`을 사용).

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a> Azure 저장소 Blob 트리거

#### 저장소 Blob 트리거에 대한 function.json

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: Blob에 대한 함수 코드에 사용되는 변수 이름입니다.
- `path`: 모니터링할 컨테이너를 지정하는 경로 및 선택적으로 Blob 이름 패턴입니다.
- `connection`: 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. `connection`을 비워두면, 트리거는 AzureWebJobsStorage 앱 설정에 의해 지정되는 함수 앱에 대한 기본 저장소 연결 문자열로 작동합니다.
- `type`: *blobTrigger*로 설정해야 합니다.
- `direction`: *in*으로 설정해야 합니다.

samples-workitems 컨테이너에 추가된 Blob을 감시하는 저장소 Blob 트리거에 대한 *function.json* 예제:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### Blob 트리거를 지원하는 형식

BLOB을 Node 또는 C# 함수에서 다음 중 원하는 형식으로 역직렬화할 수 있습니다.

* 개체(JSON의)
* String

C# 함수에서 다음 중 원하는 형식으로 바인딩할 수 있습니다.

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)에 의해 deserialize된 다른 유형

#### Blob 트리거 C# 코드 예제

이 C# 코드 예제에서는 모니터링되는 컨테이너에 추가된 각 Blob의 콘텐츠를 기록합니다.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### Blob 트리거 이름 패턴

`path` 속성에 Blob 이름 패턴을 지정할 수 있습니다. 예:

```json
"path": "input/original-{name}",
```

이 경로에는 *input* 컨테이너에 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

다른 예제:

```json
"path": "input/{blobname}.{blobextension}",
```

또한 이 경로에는 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

파일 확장명에 대한 고정된 값으로 패턴을 지정하여 함수를 트리거하는 Blob의 종류를 제한할 수 있습니다. `path`을 *samples/{name}.png*로 설정하는 경우 *samples* 컨테이너의 *.png* Blob만 함수를 트리거합니다.

이름에 중괄호가 있는 Blob 이름에 대한 이름 패턴을 지정해야 하는 경우 이중 중괄호를 사용합니다. 예를 들어 *images* 컨테이너에서 이름이 다음과 같은 Blob를 찾은 경우

		{20140101}-soundfile.mp3

`path` 속성에 사용합니다.

		images/{{20140101}}-{name}

예제에서 `name` 변수 값은 *soundfile.mp3*입니다.

#### Blob 수신 확인

Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 이를 위해 *blob 수신 확인*을 유지 관리하여 지정된 Blob 버전이 처리되었는지 확인합니다.

Blob 수신 확인은 AzureWebJobsStorage 연결 문자열에 지정된 Azure 저장소 계정의 *azure-webjobs-hosts*라는 컨테이너에 저장됩니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* Blob에 대해 호출된 함수("*{함수 앱 이름}*.Functions.*{함수 이름}*", 예: "functionsf74b96f7.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob를 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

#### 포이즌 Blob 처리

Blob 트리거 함수가 일시적인 오류로 인해 실패할 경우 SDK에서 이 함수를 다시 호출합니다. 그러나 Blob의 콘텐츠로 인해 실패한 경우에는 Blob을 처리하려고 할 때마다 함수가 실패합니다. 기본적으로 SDK는 지정된 Blob에 대해 함수를 최대 5번 호출합니다. 5번의 시도에 실패하면 *webjobs-blobtrigger-poison*이라는 큐에 메시지를 추가합니다.

포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(*{함수 앱 이름}*.함수.*{함수 이름}* 형식)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

#### 큰 컨테이너에 대한 Blob 폴링

트리거가 모니터링하는 Blob 컨테이너가 10,000개 이상의 Blob를 포함하는 경우 함수 런타임은 로그 파일을 스캔하여 새롭거나 변경된 Blob를 확인합니다. 이 프로세스는 실시간으로 처리되지 않으므로 Blob을 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 또한 [저장소 로그를 만들기 위해 “최선”](https://msdn.microsoft.com/library/azure/hh343262.aspx)을 다하고 있지만 모든 이벤트를 캡처하는 것을 보장하지는 않습니다. 경우에 따라 로그가 누락될 수 있습니다. 응용 프로그램에서 큰 컨테이너에 대한 Blob 트리거의 속도 및 안정성 제한 사항이 적합하지 않을 경우, Blob를 만들 때 큐 메시지를 만들고 Blob를 처리하는 Blob 트리거 대신 큐 트리거를 사용하는 것이 좋습니다.
 
## <a id="storageblobbindings"></a> Azure 저장소 Blob 입력 및 출력 바인딩

#### 저장소 Blob 입력 또는 출력 바인딩에 대한 function.json 예제

*function.json* 파일은 다음 속성을 지정합니다.

- `name`: Blob에 대한 함수 코드에 사용되는 변수 이름입니다.
- `path`: Blob을 읽어오거나 Blob을 기록할 컨테이너를 지정하는 경로이며, 선택적으로 Blob 이름 패턴입니다.
- `connection`: 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. `connection`을 비워두면, 바인딩은 AzureWebJobsStorage 앱 설정에 의해 지정되는 함수 앱에 대한 기본 저장소 연결 문자열로 작동합니다.
- `type`: *Blob*으로 설정해야 합니다.
- `direction`: *in* 또는 *out*으로 설정합니다.

저장소 Blob 입력 또는 출력 바인딩에 대한 *function.json* 예제로 큐 트리거를 사용하여 Blob을 복사합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Blob 입력 및 출력을 지원하는 형식

`blob` 바인딩은 Node.js 또는 C# 함수의 다음 형식을 직렬화 또는 역직렬화할 수 있습니다.

* 개체(출력 Blob에 대해 C#의 경우 `out T`, 함수가 종료될 때 매개 변수 값이 null이면 Blob을 null 개체로 생성)
* 문자열(출력 Blob에 대해 C#의 경우 `out string`, 함수가 반환될 때 문자열 매개 변수가 null이 아닌 경우에만 Blob 생성)

C# 함수에서 다음 형식으로 바인딩할 수 있습니다.

* `TextReader`(입력에만 해당)
* `TextWriter`(출력에만 해당)
* `Stream`
* `CloudBlobStream`(출력에만 해당)
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`

#### Blob 출력 C# 코드 예제

이 C# 코드 예제에서는 이름이 큐 메시지에 수신되는 Blob를 복사합니다.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a> Azure 저장소 테이블 입력 및 출력 바인딩

#### 저장소 테이블에 대한 function.json

*function.json*은 다음 속성을 지정합니다.

- `name`: 테이블 바인딩에 대한 함수 코드에 사용되는 변수 이름입니다.
- `tableName`: 테이블의 이름입니다.
- `partitionKey` 및 `rowKey`: 함께 사용되어 C# 또는 노드 함수에서 단일 엔터티를 읽거나 노드 함수에서 단일 엔터티를 작성합니다.
- `take`: 노드 함수에서 테이블 입력에 대해 읽는 행의 최대 수입니다.
- `filter`: 노드 함수에서 테이블 입력에 대한 OData 필터 식입니다.
- `connection`: 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. `connection`을 비워두면, 바인딩은 AzureWebJobsStorage 앱 설정에 의해 지정되는 함수 앱에 대한 기본 저장소 연결 문자열로 작동합니다.
- `type`: *table*로 설정해야 합니다.
- `direction`: *in* 또는 *out*으로 설정합니다.

다음 예제 *function.json*은 큐 트리거를 사용하여 단일 테이블 행을 읽습니다. JSON은 하드 코드된 파티션 키 값을 제공하고 행 키를 큐 메시지에서 가져온 것으로 지정합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### 저장소 테이블 입력 및 출력 지원 형식

`table` 바인딩은 Node.js 또는 C# 함수에서 개체를 직렬화 또는 역직렬화할 수 있습니다. 개체는 RowKey 및 PartitionKey 속성을 갖습니다.

C# 함수에서 다음 형식으로 바인딩할 수 있습니다.

* `T` 여기서 `T`는 `ITableEntity`를 구현
* `IQueryable<T>`(입력에만 해당)
* `ICollector<T>`(출력에만 해당)
* `IAsyncCollector<T>`(출력에만 해당)

#### 저장소 테이블 바인딩 시나리오

테이블 바인딩은 다음과 같은 시나리오를 지원합니다.

* C# 또는 노드 함수에서 단일 행을 읽습니다.

	`partitionKey` 및 `rowKey`를 설정합니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* C# 함수에서 여러 행을 읽습니다.

	함수 런타임은 테이블에 바인딩된 `IQueryable<T>` 개체를 제공합니다. `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`를 구현해야 합니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다. `IQueryable` 개체를 사용하여 필요한 모든 필터링 작업을 수행할 수 있습니다.

* 노드 함수에서 여러 행을 읽습니다.

	`filter` 및 `take` 속성을 설정합니다. `partitionKey` 또는 `rowKey`를 설정하지 않습니다.

* C# 함수에서 하나 이상의 행을 작성합니다.

	함수 런타임에서는 테이블에 바인딩된 `ICollector<T>` 또는 `IAsyncCollector<T>`를 제공하며 여기서 `T`은 추가하려는 엔터티의 스키마를 지정합니다. 일반적으로 `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`을 구현하지만 반드시 그런 것은 아닙니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

#### Storage 테이블 예제: C# 또는 노드에서 단일 테이블 엔터티 읽기

다음 C# 코드 예제에서는 앞에 표시된 *function.json* 파일로 작업하여 단일 테이블 엔터티를 읽습니다. 큐 메시지에는 행 키 값이 있고 *run.csx* 파일에 정의된 형식으로 테이블 엔터티를 읽습니다. 형식은 `PartitionKey` 및 `RowKey` 속성을 포함하며 `TableEntity`에서 파생되지 않습니다.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

또한 다음 F# 코드 예제에서는 앞의 *function.json* 파일로 작업하여 단일 테이블 엔터티를 읽습니다.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

또한 다음 노드 코드 예제에서는 앞의 *function.json* 파일로 작업하여 단일 테이블 엔터티를 읽습니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Storage 테이블 예제: C에서 여러 테이블 엔터티 읽기# 

다음 *function.json* 및 C# 코드 예제에서는 큐 메시지에 지정된 파티션 키에 대한 엔터티를 읽습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 코드는 엔터티 형식이 `TableEntity`에서 파생할 수 있도록 Azure 저장소 SDK에 대한 참조를 추가합니다.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Storage 테이블 예제: C로 테이블 엔터티 만들기# 

다음 *function.json* 및 *run.csx* 예제에서는 C#에서 테이블 엔터티를 작성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Storage 테이블 예제: F에서 테이블 엔터티 만들기#

다음 *function.json* 및 *run.fsx* 예제에서는 F#에서 테이블 엔터티를 작성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### Storage 테이블 예제: Node에서 테이블 엔터티 만들기

다음 *function.json* 및 *run.csx* 예제에서는 노드에서 테이블 엔터티를 작성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->