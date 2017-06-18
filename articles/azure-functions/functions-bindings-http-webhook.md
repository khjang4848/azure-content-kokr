<properties
	pageTitle="Azure Functions HTTP 및 WebHook 바인딩 | Microsoft Azure"
	description="Azure Functions에서 HTTP와 WebHook 트리거 및 바인딩을 사용하는 방법을 파악합니다."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Azure Functions HTTP 및 WebHook 바인딩

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 HTTP와 WebHook 트리거 및 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## HTTP 및 WebHook 바인딩에 대한 function.json

*function.json* 파일은 요청 및 응답 둘 다에 속하는 속성을 제공합니다.

HTTP 요청에 대한 속성:

- `name`: 요청 개체(또는 Node.js 함수의 경우 요청 본문)에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: *httpTrigger*로 설정되어 있어야 합니다.
- `direction`: *in*으로 설정해야 합니다.
- `webHookType`: WebHook 트리거의 경우 유효한 값은 *github*, *slack* 및 *genericJson*입니다. WebHook이 아닌 HTTP 트리거의 경우 이 속성을 빈 문자열로 설정합니다. WebHook에 대한 자세한 내용은 다음 [WebHook 트리거](#webhook-triggers) 섹션을 참조하세요.
- `authLevel`: WebHook 트리거에 적용되지 않습니다. "함수"가 API 키를 요구하도록 설정하거나 "익명"이 API 키 요구 사항을 삭제하도록 설정하거나 "관리자"가 마스터 API 키를 요구하도록 설정합니다. 자세한 내용은 아래 [API 키](#apikeys)를 참조하세요.

HTTP 응답에 대한 속성:

- `name`: 응답 개체에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: *http*로 설정해야 합니다.
- `direction`: *out*으로 설정해야 합니다.
 
예제 *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## WebHook 트리거

WebHook 트리거는 WebHook을 위해 디자인된 다음과 같은 기능을 포함하는 HTTP 트리거입니다.

* 특정 WebHook 공급자(현재 GitHub와 Slack 지원)의 경우 함수 런타임은 공급자 서명의 유효성을 검사합니다.
* Node.js 함수의 경우 함수 런타임은 요청 개체 대신 요청 본문을 제공합니다. 매개 변수 형식을 지정하여 제공되는 항목을 제어하기 때문에 C# 함수에 대한 특별한 처리가 없습니다. `HttpRequestMessage`를 지정하는 경우 요청 개체를 가져옵니다. POCO 형식을 지정하는 경우 함수 런타임은 요청 본문에서 JSON 개체를 구문 분석하려고 시도하여 개체 속성을 채웁니다.
* WebHook 함수를 트리거하려면 HTTP 요청이 API 키를 포함해야 합니다. WebHook HTTP가 아닌 트리거의 경우 이 요구 사항은 선택 사항입니다.

GitHub WebHook을 설정하는 방법에 대한 내용은 [GitHub 개발자 - WebHook 만들기](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)를 참조하세요.

## 함수를 트리거하는 URL

함수를 트리거하려면 함수 앱 URL 및 함수 이름을 조합한 URL에 HTTP 요청을 보냅니다.

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## API 키

기본적으로 API 키는 HTTP 또는 WebHook 함수를 트리거하는 HTTP 요청에 포함되어야 합니다. 키는 `code`이라는 쿼리 문자열 변수에 포함되거나 `x-functions-key` HTTP 헤더에 포함될 수 있습니다. WebHook이 아닌 함수의 경우 `authLevel` 속성을 *function.json* 파일의 "익명"으로 설정하여 API 키가 필요하지 않음을 알릴 수 있습니다.

API 키 값을 함수 앱에 있는 파일 시스템의 *D:\\home\\data\\Functions\\secrets* 폴더에서 찾을 수 있습니다. 마스터 키 및 함수 키는 이 예제에 표시된 대로 *host.json* 파일에서 설정됩니다.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

*host.json*에서 기능 키는 모든 함수를 트리거하는 데 사용할 수 있지만 사용하지 않는 함수를 트리거하지 않습니다. 마스터 키는 모든 함수를 트리거하는 데 사용할 수 있고 사용하지 않는 경우에도 함수를 트리거합니다. `authLevel` 속성을 "관리자"로 설정하여 마스터 키를 요청하는 함수를 구성할 수 있습니다.

*비밀* 폴더가 함수와 동일한 이름을 가진 JSON 파일을 포함하는 경우 해당 파일의 `key` 속성은 함수를 트리거하는 데에도 사용할 수 있고 이 키는 참조하는 함수에서만 작동합니다. 예를 들어 `HttpTrigger`이라는 함수에 대한 API 키는 *비밀* 폴더의 *HttpTrigger.json*에 지정됩니다. 다음은 예제입니다.

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] WebHook 트리거를 설정할 경우 마스터 키를 WebHook 공급자와 공유하지 마세요. WebHook을 처리하는 함수로만 작동하는 키를 사용합니다. 마스터 키는 사용하지 않는 함수를 포함한 모든 함수를 트리거하는 데 사용할 수 있습니다.

## HTTP 트리거 함수에 대한 예제 C# 코드 

예제 코드는 쿼리 문자열 또는 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## HTTP 트리거 함수에 대한 예제 F# 코드

예제 코드는 쿼리 문자열 또는 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

다음과 같이 NuGet을 사용하여 `FSharp.Interop.Dynamic` 및 `Dynamitey` 어셈블리를 참조하는 `project.json` 파일이 필요합니다.

```json
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
```

그러면 NuGet을 사용하여 종속성을 가져오고 스크립트에서 해당 항목을 참조하게 됩니다.

## HTTP 트리거 함수에 대한 예제 Node.js 코드 

이 예제 코드는 쿼리 문자열 또는 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## GitHub WebHook 함수에 대한 예제 C# 코드 

이 예제 코드는 GitHub 문제 설명을 기록합니다.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## GitHub WebHook 함수에 대한 예제 F# 코드

이 예제 코드는 GitHub 문제 설명을 기록합니다.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## GitHub WebHook 함수에 대한 예제 Node.js 코드 

이 예제 코드는 GitHub 문제 설명을 기록합니다.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## 다음 단계

[AZURE.INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->