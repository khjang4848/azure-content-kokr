<properties
	pageTitle="로컬로 Azure Functions 개발 및 실행 | Microsoft Azure"
	description="Azure 앱 서비스에서 실행하기 전에 Visual Studio에서 Azure Functions을 코딩 및 테스트하는 방법을 알아봅니다."
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="erikre"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="glenga"/>

# Visual Studio에서 Azure Functions 코딩 및 테스트하는 방법

## 개요

이 문서에서는 [WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) GitHub 리포지토리를 다운로드하고 여기에 포함된 Visual Studio 솔루션을 실행하여 [Azure Functions](functions-overview.md)을 로컬로 실행하는 방법을 설명합니다.

Azure Functions에 대한 런타임은 WebJobs.Script 오픈 소스 프로젝트의 구현입니다. 이 프로젝트는 [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)에 기반하고 프레임워크는 모두 로컬로 실행할 수 있습니다. 그러나 WebJobs SDK가 저장소 에뮬레이터에서 지원하지 않는 저장소 계정 기능을 사용하기 때문에 Azure 저장소 계정에 연결해야 합니다.

함수는 Azure 포털에서 쉽게 코딩 및 테스트할 수 있지만 경우에 따라 Azure에서 실행하기 전에 로컬로 작업하는 데에도 유용합니다. 예를 들어 Azure Functions가 지원하는 언어 중 일부는 [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx)를 제공하기 때문에 Visual Studio에서 코드를 쉽게 작성할 수 있습니다. 그리고 함수를 원격으로 디버깅할 수 있는 동안 로컬로 더 빠르고 쉽게 디버깅할 수 있습니다. 로컬로 실행하는 경우 WebJobs 스크립트 호스트 코드 뿐만 아니라 함수 코드에서 중단점을 디버깅하고 설정할 수 있습니다.

>[AZURE.NOTE] Azure Functions은 현재 미리 보기 상태이며 도구를 비롯한 전반적인 환경을 신속하게 개발하는 중입니다. 이 문서에 설명된 절차는 최종 로컬 개발 환경을 반영하지 않으며 [사용자 의견을 제공](https://feedback.azure.com/forums/355860-azure-functions)해 주시길 부탁드립니다.

## 필수 조건

### 함수 앱이 있는 Azure 계정

이 문서에서는 포털에서 [Azure Functions](functions-overview.md)으로 작업하고 [트리거, 바인딩 및 JobHost](functions-reference.md)와 같은 Azure Functions 개념을 잘 알고 있다고 가정합니다.

함수를 로컬로 실행하는 경우 콘솔 창에서 일부 출력을 가져올 수 있지만 함수 호출 및 로그를 확인하기 위해 라이브 함수 앱에서 호스트하는 대시보드를 사용하려 합니다.

### 최신 Azure SDK for .NET을 설치한 Visual Studio 2015

Visual Studio 2015가 설치되지 않거나 현재 Azure SDK가 설치되지 않은 경우 [Visual Studio 2015용 Azure SDK를 다운로드](http://go.microsoft.com/fwlink/?linkid=518003)합니다. SDK가 없는 경우 Visual Studio 2015는 SDK와 함께 자동으로 설치됩니다.

### 조건부 필수 조건

예를 들어 일부 Azure 리소스 및 소프트웨어는 해당 프로그램을 사용하는 함수를 실행하려는 경우에만 설치해야 합니다.

* Azure 리소스
	* 서비스 버스
	* 쉬운 테이블
	* DocumentDB
	* 이벤트 허브
	* 알림 허브

* 컴파일러 및 스크립트 엔진
	* F#
	* BASH
	* Python
	* PHP

고객을 위해 설정해야 하는 환경 변수를 포함하는 이러한 요구 사항에 대한 자세한 내용은 [WebJobs.Script 리포지토리에 대한 wiki 페이지](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)를 참조하세요.

목적이 WebJobs.SDK 프로젝트에 적용되는 경우 전체 테스트를 실행하기 위해 조건부 필수 구성 요소가 모두 필요합니다.

## 로컬로 실행하려면

1. Webjobs.Script 리포지토리를 [복제](https://github.com/Azure/azure-webjobs-sdk-script/) 또는 [다운로드](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip)합니다.

2. 저장소 연결 문자열에 대한 환경 변수를 설정합니다.

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	함수 앱에 대한 앱 서비스 **응용 프로그램 설정** 포털 블레이드에서 이러한 값을 가져올 수 있습니다.

	a. **함수 앱** 블레이드에서 **함수 앱 설정**을 클릭합니다.

	![함수 앱 설정 클릭](./media/functions-run-local/clickfuncappsettings.png)
 
	b. **함수 앱 설정** 블레이드에서 **앱 서비스 설정으로 이동**을 클릭합니다.

	![앱 서비스 설정 클릭](./media/functions-run-local/clickappsvcsettings.png)
 
	c. **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

	![응용 프로그램 설정 클릭](./media/functions-run-local/clickappsettings.png)
 
	d. **응용 프로그램 설정** 블레이드에서 **앱 설정** 섹션까지 아래로 스크롤하고 WebJobs SDK 설정을 찾습니다.

	![WebJobs 설정](./media/functions-run-local/wjsettings.png)

	e. `AzureWebJobsStorage` 앱 설정과 동일한 이름과 값을 가진 환경 변수를 설정합니다.

	f. `AzureWebJobsDashboard` 앱 설정에 대해서도 동일하게 수행합니다.

2. AzureWebJobsServiceBus라는 환경 변수를 만들고 서비스 버스 연결 문자열에 대해 설정합니다.

	이 환경 변수는 서비스 버스 바인딩에 필요하고 서비스 버스 바인딩을 사용하지 않는 경우에도 설정하는 것이 좋습니다. 일부 시나리오에서는 사용 중인 바인딩에 관계 없이 서비스 버스 연결 문자열을 설정하지 않는 경우 예외가 나타날 수 있습니다.

3. 필요한 다른 환경 변수가 설정되어 있는지 확인합니다. (앞서 [조건부 필수 조건](#conditional-prerequisites) 섹션을 참조하세요).

4. Visual Studio를 시작한 다음 WebJobs.Script 솔루션을 엽니다.

6. 시작 프로젝트를 설정합니다. HTTP 또는 WebHook 트리거를 사용하는 함수를 실행하려는 경우 **WebJobs.Script.WebHost**를 선택하고 그렇지 않으면 **WebJobs.Script.Host**를 선택합니다.

4. 시작 프로젝트가 WebJobs.Script.Host인 경우:

	a. **솔루션 탐색기**에서 WebJobs.Script.Host 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

	b. **프로젝트 속성** 창의 **디버그** 탭에서 **명령줄 인수**를 `..\..\..\..\sample`로 설정합니다.

	![명령줄 인수](./media/functions-run-local/cmdlineargs.png)

	리포지토리에 있는 *샘플* 폴더에 대한 상대 경로입니다. *샘플* 폴더는 전역 설정을 포함하는 *host.json* 파일 및 각 샘플 함수에 대한 폴더를 포함합니다.

	시작하려면 제공되는 *샘플* 폴더를 사용하는 것이 쉽습니다. 나중에 *샘플* 폴더에 사용자 고유의 함수를 추가하거나 *host.json* 및 함수 폴더를 포함하는 폴더를 사용할 수 있습니다.

5. 시작 프로젝트가 WebJobs.Script.WebHost인 경우:

	a. `sample` 폴더에 대한 전체 경로에 AzureWebJobsScriptRoot 환경 변수를 설정합니다.

	b. Visual Studio를 다시 시작하여 새 환경 변수 값을 선택합니다.

	HTTP 트리거 함수를 실행하는 방법에 대한 자세한 내용은 [API 키](#api-keys) 섹션을 참조하세요.

5. *sample\\host.json* 파일을 열고 `functions` 속성을 추가하여 실행하려는 함수를 지정합니다.

	예를 들어 다음 JSON은 WebJobs SDK JobHost를 만들고 두 개의 함수를 찾습니다.

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	*샘플* 폴더 대신 고유한 폴더를 사용하는 경우 실행하려는 함수만을 포함합니다. 그런 다음 *host.json*에서 `functions` 속성을 생략할 수 있습니다.
 
6. 솔루션을 빌드하고 실행합니다.

	콘솔 창에서는 JobHost가 `host.json` 파일에서 지정한 함수를 찾는다는 것을 보여줍니다.

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

	WebHost 프로젝트를 시작할 경우 프로젝트의 기본 URL에 제공되는 콘텐츠가 없으므로 빈 브라우저 페이지가 표시됩니다. HTTP 트리거 함수에 사용할 URL에 대한 자세한 내용은 [API 키](#apikeys) 섹션을 참조하세요.

## 함수 출력 보기

함수 앱에 대한 대시보드로 이동하여 함수 호출 및 출력 로그를 확인합니다.

대시보드는 다음 URL에 위치합니다.

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

**함수** 페이지에는 실행된 함수 목록 및 함수 호출의 목록이 표시됩니다.

![호출 세부 정보](./media/functions-run-local/invocationdetail.png)

호출을 클릭하여 **호출 세부 정보** 페이지를 참조하며 여기서는 함수가 트리거되는 경우, 대략적인 실행 시간 및 정상적인 완료를 나타냅니다. **토글 출력** 단추를 클릭하여 함수 코드에 의해 작성되는 로그를 확인합니다.

![호출 세부 정보](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> HTTP 트리거에 대한 API 키

HTTP 또는 WebHook 함수를 실행하려면 *function.json* 파일에 `"authLevel": "anonymous"`을 포함하지 않는 한 API 키가 필요합니다.

예를 들어 API 키가 `12345`인 경우 WebJobs.Script.WebHost 프로젝트를 실행하면 다음 URL로 *HttpTrigger* 함수를 트리거할 수 있습니다.

	http://localhost:28549/api/httptrigger?code=12345

또는 `x-functions-key` HTTP 헤더에 API 키를 저장할 수 있습니다.

API 키가 WebJobs.Script.WebHost 프로젝트의 [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) 폴더에 있는 `.json` 파일에 저장됩니다.

### 모든 HTTP 및 WebHook 함수에 적용되는 API 키

*App\_Data/secrets* 폴더의 *host.json* 파일에는 다음과 같은 두 개의 키가 있습니다.

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

`functionKey` 속성은 특정 기능을 재정의하도록 정의하지 않은 경우 HTTP 또는 WebHook 함수에 사용할 수 있는 키를 저장합니다. 이 기능으로 인해 만든 모든 함수에 대해 새 API 키를 항상 정의할 필요가 없습니다.

`masterKey` 속성은 몇 가지 테스트 시나리오에서 유용한 키를 저장합니다.

* 마스터 키로 WebHook 함수를 호출하면 WebJobs SDK는 WebHook 공급자의 서명에 대한 유효성 검사를 무시합니다.

* 마스터 키로 HTTP 또는 WebHook 함수를 호출하는 경우 함수를 *function.json* 파일에서 사용하지 않더라도 트리거됩니다. 사용할 수 없는 기능에 대해서도 **실행** 단추가 작동하도록 Azure 포털에서 사용합니다.
 
### 개별 함수에 적용되는 API 키

*{function name}.json*이라는 파일은 특정 기능에 대한 API 키를 포함합니다. 예를 들어 *App\_Data/secrets/HttpTrigger.json*의 다음 예제 JSON 콘텐츠는 `HttpTrigger` 함수에 대한 API 키를 설정합니다.

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## 함수에서 NuGet 패키지 참조 사용  

NuGet 참조가 현재 처리되는 방식 때문에 호스트가 실행되는 동안 *project.json* 파일을 "터치"해야 합니다. 호스트는 파일 수정 내용을 감시하고 변경 내용을 감지하면 복원을 시작합니다. 또한 *NuGet.exe*(3.3.0 권장)가 경로에 있거나 *NuGet.exe*의 경로를 사용하여 AzureWebJobs\_NuGetPath라는 환경 변수를 설정해야 합니다.

## 문제 해결

Visual Studio가 실행되는 동안 수행된 환경 변수 변경은 자동으로 선택되지 않습니다. Visual Studio를 시작한 후에 환경 변수를 추가하거나 변경하는 경우 Visual Studio를 종료하고 다시 시작하여 현재 값을 선택하도록 합니다.

디버깅하는 경우 **예외 설정** 창에서 **공용 언어 런타임 예외**를 선택하여 예외에 대한 자세한 내용을 알 수 있습니다(창을 열려면 Ctrl-Alt-E).

디버깅하는 동안 자세한 예외 정보를 얻을 수 있는 다른 방법은 스크립트 호스트에 대한 기본 루프의 `catch` 블록에 중단점을 설정하는 것입니다. `RunAndBlock` 메서드의 *Host/ScriptHostManager.cs*에 있는 WebJobs.Script 프로젝트에서 찾을 수 있습니다.

## 다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->