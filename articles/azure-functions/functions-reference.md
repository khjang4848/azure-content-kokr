<properties
	pageTitle="Azure Functions 개발자 참조 | Microsoft Azure"
	description="모든 언어와 바인딩에 공통적인 Azure Functions 개념 및 구성 요소를 이해합니다."
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
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Azure Functions 개발자 참조

Azure Functions는 사용하는 언어나 바인딩에 관계없이, 몇 가지 핵심적인 기술 개념과 구성 요소를 공유합니다. 특정 언어나 바인딩에 해당하는 세부 정보를 학습하기 전에, 모든 항목에 해당하는 이 개요를 꼼꼼히 읽어 보시기 바랍니다.

이 문서에서는 [Azure Functions 개요](functions-overview.md)를 이미 읽었고 [트리거, 바인딩, JobHost 런타임 같은 WebJobs SDK 개념](../app-service-web/websites-dotnet-webjobs-sdk.md)에 익숙하다고 가정합니다. Azure Functions는 WebJobs SDK를 기반으로 합니다.


## 함수 코드

*함수*는 Azure Functions의 기본 개념입니다. 사용자는 원하는 언어로 함수 코드를 작성하고 코드 파일과 구성 파일을 같은 폴더에 저장합니다. 구성은 JSON 형식이고 파일 이름은 `function.json`입니다. 다양한 언어가 지원되며, 각각의 언어는 각 언어에 적합하게 최적화된 약간 다른 환경을 갖습니다.

`function.json` 파일은 바인딩을 포함한 함수에 해당하는 구성을 포함합니다. 런타임은 이 파일을 읽어서 트리거를 해제할 이벤트, 함수를 호출할 때 포함할 데이터, 함수 자체로부터 전달된 데이터를 보낼 곳을 결정합니다.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

`disabled` 속성을 `true`로 설정하여 런타임이 함수를 실행하지 않도록 막을 수 있습니다.

`bindings` 속성은 트리거와 바인딩을 모두 구성하는 곳에 위치합니다. 각 바인딩은 몇 가지 공통적인 설정과 특정한 바인딩 형식에 해당하는 일부 설정을 공유합니다. 모든 바인딩에는 다음 설정이 필요합니다.

|속성|값/형식|설명|
|---|-----|------|
|`type`|string|바인딩 형식 예: `queueTrigger`.
|`direction`|'in', 'out'| 함수 안으로 데이터를 수신할 바인딩인지 또는 함수의 데이터를 전송할 바인딩인지를 나타냅니다.
| `name` | string | 함수 내의 바인딩 데이터에 사용될 이름입니다. C#의 경우에는 인수 이름이며 JavaScript의 경우 키/값 목록의 키입니다.

## 함수 앱

함수 앱은 Azure 앱 서비스에서 함께 관리되는 하나 이상의 개별 함수 함수로 구성됩니다. 함수 앱의 모든 함수는 동일한 가격 책정 계획, 연속 배포 및 런타임 버전을 공유합니다. 여러 언어로 작성된 함수는 동일한 함수 앱을 공유할 수 있습니다. 함수 앱을 함수를 구성하고 전체적으로 관리하는 방법으로 생각합니다.

## 런타임(스크립트 호스트 및 웹 호스트)

런타임 또는 스크립트 호스트는 이벤트 수신을 대기하고, 데이터를 모아서 보내고, 최종적으로 코드를 실행하는 기초를 이루는 WebJobs SDK 호스트입니다.

HTTP 트리거를 지원하기 위해 프로덕션 시나리오에서 스크립트 호스트 앞쪽에 위치하도록 설계된 웹 호스트도 있습니다. 이것은 스크립트 호스트를 웹 호스트에서 관리하는 프런트 엔드 트래픽과 격리시키는 데 도움이 됩니다.

## 폴더 구조

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure 앱 서비스에서 함수를 함수 앱에 배포하기 위한 프로젝트를 설정하는 경우에는, 이 폴더 구조를 사용자의 사이트 코드로 처리할 수 있습니다. 배포 시 패키지 설치 또는 코드 transpilation 수행을 위하여 지속적인 통합 및 배포 같은 기존 툴 또는 사용자 지정 배포 스크립트를 사용할 수 있습니다.

## <a id="fileupdate"></a> 함수 앱 파일을 업데이트하는 방법

Azure 포털에 기본 제공되는 함수 편집기를 사용하면 함수에 대한 코드 파일 및 *function.json* 파일을 업데이트할 수 있습니다. *package.json* 또는 *project.json* 또는 종속성 등의 다른 파일을 업로드하거나 업데이트하려면 다른 배포 방법을 사용해야 합니다.

함수 앱은 앱 서비스를 기반으로 하므로 [표준 웹앱에 사용할 수 있는 배포 옵션](../app-service-web/web-sites-deploy.md)을 모두 함수 앱에도 사용할 수 있습니다. 함수 앱 파일을 업로드하거나 업데이트하는 데 사용할 수 있는 방법이 몇 가지 입니다.

#### 앱 서비스 편집기 사용하기

1. Azure Functions 포털에서 **함수 앱 설정**을 클릭합니다.

2. **고급 설정** 섹션에서 **앱 서비스 설정으로 이동**을 클릭합니다.

3. **개발 도구** 아래 앱 메뉴 탐색 창에서 **앱 서비스 편집기**를 클릭합니다.

4.  **이동**을 클릭합니다.

	앱 서비스 편집기가 로드된 후에 *host.json* 파일과 *wwwroot* 하위의 함수 폴더를 볼 수 있습니다.

5. 파일을 열어서 편집하거나, 배포 컴퓨터에서 끌어서 놓기로 파일을 업로드합니다.

#### 함수 앱의 SCM(Kudu) 끝점을 사용하려면

1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔 > CMD**를 클릭합니다.

3. `D:\home\site\wwwroot`으로 이동하여 *host.json*을 업데이트하거나 `D:\home\site\wwwroot<function_name>`로 이동하여 함수 파일을 업데이트합니다.

4. 파일 그리드에서 적절한 폴더로 업로드할 파일을 끌어서 놓습니다. 파일을 삭제할 수 있는 파일 표에는 두 가지 영역이 있습니다. *.zip* 파일의 경우 "여기에 끌어 와서 업로드하고 압축을 풉니다" 레이블이 있는 상자가 나타납니다. 다른 파일 형식의 경우 "압축" 상자 외부에 파일 표를 살펴봅니다.

#### FTP를 사용하려면

1. [여기](../app-service-web/web-sites-deploy.md#ftp)에 있는 지침에 따라 FTP를 구성합니다.

2. 함수 앱 사이트에 연결되면, 업데이트된 *host.json* 파일을 `/site/wwwroot`에 복사하거나 함수 파일을 `/site/wwwroot/<function_name>`에 복사합니다.

#### 연속 배포를 사용하려면

[Azure Functions에 대한 연속 배포](functions-continuous-deployment.md) 항목의 지침을 따릅니다.

## 병렬 실행

복수의 트리거 이벤트가 단일 스레드 함수 런타임이 해당 이벤트를 처리할 수 있는 속도보다 빨리 발생하면 런타임은 병렬 모드로 함수를 여러 번 호출할 수 있습니다. 함수 앱이 [동적 서비스 계획](functions-scale.md#dynamic-service-plan)을 사용하는 경우에, 함수 앱은 자동으로 확장할 수 있습니다. 앱이 동적 서비스 계획으로 실행되건 일반 [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)으로 실행되건, 함수 앱의 각 인스턴스는 여러 스레드를 사용하여 병렬로 동시 함수 호출을 처리할 수 있습니다. 각 함수 앱 인스턴스에서 동시 함수 호출의 최대 수는 함수 앱의 메모리 크기에 따라 달라집니다.

## Azure Functions 펄스  

펄스는 함수의 실행 빈도는 물론 성공과 실패를 보여주는 라이브 이벤트 스트림입니다. 평균 실행 시간을 모니터링할 수도 있습니다. 앞으로 더 많은 기능과 사용자 지정을 추가할 예정입니다. **펄스** 페이지는 **모니터링** 탭을 통해 액세스할 수 있습니다.

## 리포지토리

Azure Functions에 대한 코드는 공개 소스이며 GitHub 리포지토리에 저장됩니다.

* [Azure Functions 런타임](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions 포털](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions 템플릿](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## 바인딩

지원되는 모든 바인딩을 포함하는 테이블입니다.

[AZURE.INCLUDE [동적 계산](../../includes/functions-bindings.md)]

## 문제 보고

[AZURE.INCLUDE [문제 보고](../../includes/functions-reporting-issues.md)]

## 다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Azure Functions: Azure 앱 서비스](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) 팀 블로그 과정. Azure Functions 개발에 대한 기록

<!---HONumber=AcomDC_0921_2016-->