<properties
	pageTitle="Azure Functions NodeJS 개발자 참조 | Microsoft Azure"
	description="NodeJS를 사용하여 Azure Functions를 개발하는 방법을 알아봅니다."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Azure Functions NodeJS 개발자 참조

> [AZURE.SELECTOR]
- [C# 스크립트](../articles/azure-functions/functions-reference-csharp.md)
- [F# 스크립트](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.JS](../articles/azure-functions/functions-reference-node.md)

Azure Functions에 대한 노드/JavaScript 환경은 런타임과 통신하고 바인딩을 통해 데이터를 수신 및 전송하는 `context` 개체가 전달되는 함수를 쉽게 내보낼 수 있도록 합니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다.

## 함수 내보내기

모든 JavaScript 함수는 런타임에 대한 `module.exports`를 통해 단일 `function`을 내보내 함수를 찾고 실행해야 합니다. 이 함수에는 `context` 개체가 항상 포함되어야 합니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

`direction === "in"`의 바인딩은 함수 인수로 전달됩니다. 즉, [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx)를 사용하여 동적으로 새 입력을 처리할 수 있습니다(예: 모든 입력에 대해 반복되는 `arguments.length` 사용). 이 기능은 `context` 개체를 참조하지 않고 트리거 데이터에 예측 가능하게 액세스할 수 있으므로 추가 입력을 사용하지 않는 트리거만 있는 경우 아주 편리합니다.

내보내기 문에 인수를 지정하지 않은 경우에도 *function.json*에서 발생하는 순서에 따라 인수가 항상 함수에 전달됩니다. 예를 들어 `function(context, a, b)`을 `function(context, a)`으로 변경하는 경우 `arguments[3]`를 참조하여 여전히 함수 코드의 `b` 값을 가져올 수 있습니다.

또한 방향에 관계없이 모든 바인딩은 `context` 개체로 전달됩니다(아래 참조).

## context 개체

런타임은 함수로 데이터를 전달하거나 전달받으며 사용자가 런타임과 통신할 수 있도록 하는 `context` 개체를 사용합니다.

context 개체는 항상 함수에 대한 첫 번째 매개 변수이며 런타임을 올바르게 사용하는 데 필요한 `context.done` 및 `context.log`와 같은 메서드를 가지고 있기 때문에 항상 포함되어야 합니다. 원하는 개체 이름(즉, `ctx` 또는 `c`)을 지정할 수 있습니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## context.bindings

`context.bindings` 개체는 모든 입력 및 출력 데이터를 수집합니다. 데이터는 바인딩의 `name` 속성을 통해 `context.bindings` 개체에 추가됩니다. 예를 들어 다음 바인딩 정의를 *function.json*에 지정하면 `context.bindings.myInput`을 통해 큐의 콘텐츠에 액세스할 수 있습니다.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

`context.done` 함수는 실행이 완료된 런타임을 알려줍니다. 함수가 완료되었을 때 호출해야 합니다. 그러지 않으면 런타임은 함수가 완료되었는지 알지 못합니다.

`context.done` 함수를 사용하면 `context.bindings` 개체에 속성을 덮어쓰는 속성의 속성 모음뿐만 아니라 사용자 정의 오류를 런타임에 다시 전달할 수 있습니다.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(message)

`context.log` 메서드를 사용하면 로깅을 위해 함께 상호 연결되는 로그 문을 출력할 수 있습니다. `console.log`를 사용하는 경우 메시지는 유용하지 않은 프로세스 수준 로깅에 대해서만 표시합니다.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

`context.log` 메서드는 노드 [util.format method](https://nodejs.org/api/util.html#util_util_format_format)가 지원하는 동일한 매개 변수 형식을 지원합니다. 따라서 예를 들어 코드는 다음과 같습니다.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

다음과 같이 작성할 수 있습니다.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## HTTP 트리거: context.req 및 context.res

HTTP 트리거의 경우 HTTP 요청 및 응답 개체에 대해 `req` 및 `res`를 사용하는 일종의 일반적인 패턴이므로 전체 `context.bindings.name` 패턴을 사용하지 않고도 컨텍스트 개체에서 이에 대해 쉽게 액세스할 수 있도록 하기로 결정했습니다.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## 노드 버전 및 패키지 관리

노드 버전이 현재 `5.9.1`에서 잠겨 있습니다. 더 많은 버전에 대한 지원을 추가하고 구성할 수 있도록 연구 중입니다.

함수 앱의 파일 시스템에 있는 함수 폴더에 *package.json* 파일을 업로드하여 함수에 패키지를 포함시킬 수 있습니다. 파일 업로드 지침은 [Azure Functions 개발자 참조 토픽](functions-reference.md#fileupdate)의 **함수 앱 파일을 업데이트하는 방법** 섹션을 참조하세요.

함수 앱의 SCM(Kudu) 명령줄 인터페이스에서 `npm install`을 사용할 수도 있습니다.

1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔 > CMD**를 클릭합니다.

3. `D:\home\site\wwwroot<function_name>`로 이동합니다.

4. `npm install`을 실행합니다.

필요한 패키지가 설치되면 일반적인 방법(예: `require('packagename')`)으로 함수에 가져옵니다.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## 환경 변수

환경 변수 또는 앱 설정 값을 가져오려면 다음 코드 예제와 같이 `process.env`를 사용합니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## TypeScript/CoffeeScript 지원

아직 런타임을 통해 TypeScript/CoffeeScript 자동 컴파일에 대한 직접 지원이 없으므로 배포 시 런타임 외부에서 모든 필요한 사항이 처리됩니다.

## 다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->