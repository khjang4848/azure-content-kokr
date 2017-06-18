<properties
	pageTitle="Azure 앱 서비스에서 웹앱에 대한 진단 로깅 설정"
	description="진단 로그를 사용하도록 설정하는 방법, 응용 프로그램에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에서 웹앱에 대한 진단 로깅 설정

## 개요

Azure는 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법, 응용 프로그램에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법을 설명합니다.

이 문서에서는 진단 로그와 같이 작업하기 위해 [Azure 포털](https://portal.azure.com), Azure PowerShell, 그리고 Azure CLI(Azure Command-Line Interface, Azure 명령줄 인터페이스)가 사용됩니다. Visual Studio를 사용하여 진단 로그로 작업하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>웹 서버 진단 및 응용 프로그램 진단

앱 서비스 웹앱은 웹 서버와 웹 응용 프로그램 모두의 정보를 로깅할 수 있도록 진단 기능을 제공합니다. 이는 논리적으로 **웹 서버 진단** 및 **응용 프로그램 진단**으로 구분됩니다.

### 웹 서버 진단

다음과 같은 종류의 로그를 사용하거나 사용하지 않도록 설정할 수 있습니다.

- **자세한 오류 로깅** - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)의 자세한 오류 정보입니다. 여기에는 서버에서 오류 코드를 반환한 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다.
- **실패한 요청 추적** - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 이는 사이트 성능을 개선하거나 반환된 특정 HTTP 오류를 유발한 항목을 격리하려는 경우에 유용합니다.
- **웹 서버 로깅** - [W3C 확장 로그 파일 형식](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 이는 처리된 요청 수, 특정 IP 주소에서 들어온 요청 수 등의 전체 사이트 메트릭을 확인하는 경우에 유용합니다.

### 응용 프로그램 진단

응용 프로그램 진단을 통해 웹 응용 프로그램에서 생성된 정보를 캡처할 수 있습니다. ASP.NET 응용 프로그램은 [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) 클래스를 사용하여 응용 프로그램 진단 로그에 정보를 로깅할 수 있습니다. 예:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

런타임에 문제 해결을 위해 이러한 로그를 검색할 수 있습니다. 자세한 내용은 [Visual Studio에서 Azure 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)을 참조하세요.

웹앱에 콘텐츠를 게시하는 경우 앱 서비스 웹앱은 배포 정보도 로깅합니다. 이는 자동으로 수행되며 배포 로깅에 대한 구성 설정은 없습니다. 배포 로깅을 사용하면 배포가 실패한 이유를 확인할 수 있습니다. 예를 들어 사용자 지정 배포 스크립트를 사용하는 경우 스크립트가 실패한 이유를 확인하는 데 배포 로깅을 사용할 수 있습니다.

## <a name="enablediag"></a>진단을 사용하도록 설정하는 방법

[Azure 포털](https://portal.azure.com)에서 진단을 사용하려면, 웹 앱의 블레이드에서 **설정>진단 로그**를 차례로 클릭합니다.

<!-- todo:cleanup dogfood addresses in screenshot -->
![로그 부분](./media/web-sites-enable-diagnostic-log/logspart.png)

**응용 프로그램 진단**을 사용하도록 설정하는 경우 **수준**도 선택합니다. 이 설정을 사용하면 캡처된 정보를 **정보**, **경고** 또는 **오류** 정보로 필터링할 수 있습니다. 이를 **세부 정보 표시**로 설정하면 응용 프로그램에서 생성된 모든 정보가 로깅됩니다.

> [AZURE.NOTE] web.config 파일을 변경하는 것과 달리, 응용 프로그램 진단을 사용하도록 설정하거나 진단 로그 수준을 변경하면 응용 프로그램이 실행되는 앱 도메인이 재순환되지 않습니다.

[클래식 포털](https://manage.windowsazure.com) 웹앱 **구성** 탭에서 **웹 서버 로깅**에 대한 **저장소** 또는 **파일 시스템**을 선택할 수 있습니다. **저장소**를 선택하면 저장소 계정을 선택하고 로그를 기록할 Blob 컨테이너를 선택할 수 있습니다. **사이트 진단**에 대한 기타 모든 로그는 파일 시스템에만 기록됩니다.

[클래식 포털](https://manage.windowsazure.com) 웹앱 구성 탭은 또한 응용 프로그램 진단에 대한 추가 **구성**이 포함됩니다.

* **파일 시스템** - 웹 앱 파일 시스템에 응용 프로그램 진단 정보를 저장합니다. 이러한 파일은 FTP로 액세스하거나, Azure PowerShell 또는 Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Zip 보관 파일로 다운로드할 수 있습니다.
* **테이블 저장소** - 지정된 Azure 저장소 계정 및 테이블 이름에 응용 프로그램 진단 정보를 저장합니다.
* **Blob 저장소** - 지정된 Azure 저장소 계정 및 Blob 컨테이너에 응용 프로그램 진단 정보를 저장합니다.
* **보존 기간** - 기본적으로 로그는 **Blob 저장소**에서 자동으로 삭제되지 않습니다. 자동으로 로그를 삭제하려면 **set retention**을 선택하고 로그를 보관할 기간(일)을 입력합니다.

>[AZURE.NOTE] [저장소 계정의 선택키를 다시 생성](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)하는 경우 해당 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 다음을 수행합니다.
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 저장소 계정 Blob 또는 테이블에 로깅을 다시 사용합니다. 설정을 저장합니다.

파일 시스템, 테이블 저장소 또는 Blob 저장소를 원하는 방식으로 결합하여 동시에 사용하도록 설정할 수 있으며 로그 수준은 개별적으로 구성할 수 있습니다. 예를 들어 장기적인 로깅 솔루션으로 Blob 저장소에 오류 및 경고를 기록하면서 파일 시스템 로깅은 세부 정보 표시 수준으로 사용할 수 있습니다.

세 저장소 위치 모두는 로깅된 이벤트에 대해 동일한 기본 정보를 제공하는 반면 **테이블 저장소** 및 **Blob 저장소**는 **파일 시스템**에 기록하는 것보다 인스턴스 ID, 스레드 ID 및 좀 더 세부적인 타임스탬프(눈금 형식) 등 추가 정보를 로깅합니다.

> [AZURE.NOTE] **테이블 저장소** 또는 **Blob 저장소**에 저장된 정보는 이러한 저장소 시스템에서 바로 작업할 수 있는 저장소 클라이언트 또는 응용 프로그램을 사용해서만 액세스할 수 있습니다. 예를 들어 Visual Studio 2013에는 테이블 또는 Blob 저장소를 탐색하는 데 사용할 수 있는 저장소 탐색기를 포함하고 있으며, HDInsight는 Blob 저장소에 저장된 데이터에 액세스하는 데 사용될 수 있습니다. 또한 [Azure SDK](/downloads/#) 중 하나를 사용하여 Azure 저장소에 액세스하는 응용 프로그램을 작성할 수도 있습니다.

> [AZURE.NOTE] Azure PowerShell에서 **Set-AzureWebsite** cmdlet을 사용해서도 진단을 사용하도록 설정할 수 있습니다. Azure PowerShell을 설치하지 않았거나 Azure 구독을 사용하도록 이를 구성하지 않은 경우 [Azure PowerShell을 사용하는 방법](/develop/nodejs/how-to-guides/powershell-cmdlets/)을 참조하세요.

##<a name="download"></a> 방법: 로그 다운로드

웹 앱 파일 시스템에 저장된 진단 정보는 FTP를 사용하여 직접 액세스할 수 있습니다. 또한 Azure PowerShell 또는 Azure 명령줄 인터페이스를 사용하여 Zip 보관 파일로 다운로드할 수도 있습니다.

로그가 저장되는 디렉터리 구조는 다음과 같습니다.

* **응용 프로그램 로그** - /LogFiles/Application/입니다. 이 폴더에는 응용 프로그램 로깅으로 생성된 정보가 포함된 하나 이상의 텍스트 파일이 포함됩니다.

* **실패한 요청 추적** - /LogFiles/W3SVC#########/입니다. 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. XSL 파일은 XML 파일을 Internet Explorer에서 볼 때 XML 파일의 내용에 서식을 지정하고 필터링하는 기능을 제공하므로 XSL 파일은 XML 파일과 동일한 디렉터리에 다운로드해야 합니다.

* **Detailed Error Logs** - /LogFiles/DetailedErrors/입니다. 이 폴더에는 발생한 HTTP 오류에 대해 방대한 정보를 제공하는 하나 이상의 .htm 파일이 포함되어 있습니다.

* **웹 서버 로그** - /LogFiles/http/RawLogs입니다. 이 폴더에는 [W3C 확장 로그 파일 형식](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)을 사용하여 서식이 지정된 하나 이상의 텍스트 파일이 포함되어 있습니다.

* **Deployment logs** - /LogFiles/Git입니다. 이 폴더에는 Azure 웹 앱에서 사용된 내부 배포 프로세스에서 생성된 로그와 Git 배포용 로그가 포함되어 있습니다.

### FTP

FTP를 사용하여 진단 정보에 액세스하려면, [클래식 포털](https://manage.windowsazure.com)에서 웹앱의 **대시보드**를 참조합니다. **간략 상태** 섹션에서 **FTP Diagnostic Logs** 링크를 사용하여 FTP를 통해 로그 파일에 액세스할 수 있습니다. **Deployment/FTP User** 항목은 FTP 사이트에 액세스하는 데 사용해야 하는 사용자 이름을 나열합니다.

> [AZURE.NOTE] **배포/FTP 사용자** 항목이 설정되지 않은 경우 또는 이 사용자의 암호를 잊은 경우 **대시보드**의 **간략 상태** 섹션에서 **Reset deployment credentials** 링크를 사용하여 새 사용자 및 암호를 만들 수 있습니다.

### Azure PowerShell로 다운로드

로그 파일을 다운로드하려면 새 인스턴스의 Azure PowerShell을 시작하고 다음 명령을 사용합니다.

	Save-AzureWebSiteLog -Name webappname

이 명령을 실행하면 **-Name** 매개 변수로 지정된 웹 앱이 로그가 현재 디렉터리의 **logs.zip**이라는 파일에 저장됩니다.

> [AZURE.NOTE] Azure PowerShell을 설치하지 않았거나 Azure 구독을 사용하도록 이를 구성하지 않은 경우 [Azure PowerShell을 사용하는 방법](/develop/nodejs/how-to-guides/powershell-cmdlets/)을 참조하세요.

### Azure 명령줄 인터페이스로 다운로드

Azure 명령줄 인터페이스를 사용하여 로그 파일을 다운로드하려면 새 명령 프롬프트, PowerShell, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

	azure site log download webappname

이 명령을 실행하면 'webappname'이라는 웹 앱의 로그가 현재 디렉터리의 **diagnostics.zip**이라는 파일에 저장됩니다.

> [AZURE.NOTE] Azure CLI(Azure Command-Line Interface)를 설치하지 않았거나 Azure 구독을 사용하도록 Azure CLI를 구성하지 않은 경우 [Azure CLI 사용 방법](../xplat-cli-install.md)을 참조하세요.

## 방법: Application Insights에서 로그 보기

Visual Studio Application Insights는 로그 필터링과 검색을 위한 도구 및 요청과 다른 이벤트와 로그를 연결하기 위한 도구를 제공합니다.

1. Visual Studio의 프로젝트에 Application Insights SDK 추가
 * 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다. Application Insights 리소스 만들기를 포함 하는 단계를 안내합니다. [자세히 알아보기](../application-insights/app-insights-asp-net.md)
2. 추적 수신기 패키지를 프로젝트에 추가 합니다.
 * 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. `Microsoft.ApplicationInsights.TraceListener` [자세히 알아보기](../application-insights/app-insights-asp-net-trace-logs.md) 선택
3. 프로젝트를 업로드하고 실행하여 로그 데이터를 생성합니다.
4. [Azure 포털](https://portal.azure.com/)에서 새 Application Insights 리소스를 찾아서 **검색**을 엽니다. 요청, 사용법 및 다른 원격 분석와 함께 로그 데이터가 표시됩니다. 일부 원격 분석에 몇 분 정도 걸릴 수 있습니다. 새로 고침을 클릭합니다. [자세히 알아보기](../application-insights/app-insights-diagnostic-search.md)

[Application Insights로 추적되는 성능에 대해 알아보기](../application-insights/app-insights-azure-web-apps.md)

##<a name="streamlogs"></a> 방법: 스트림 로그

응용 프로그램을 개발하는 동안 거의 실시간의 로깅 정보를 보는 것이 종종 유용합니다. 이는 Azure PowerShell 또는 Azure 명령줄 인터페이스 중 하나를 사용하여 개발 환경에 로깅 정보를 스트리밍하도록 하면 가능합니다.

> [AZURE.NOTE] 일부 유형의 로깅 버퍼는 로그 파일에 기록하고 이로 인해 스크림에서 이벤트가 작동하지 않을 수 있습니다. 예를 들어 사용자가 페이지를 방문할 때 발생한 응용 프로그램 로그 항목이 페이지 요청에 대한 해당 HTTP 로그 항목보다 먼저 스트림에 표시될 수 있습니다.

> [AZURE.NOTE] 로그 스트리밍은 **D:\\home\\LogFiles\** 폴더에 저장된 모든 텍스트 파일에 기록된 정보를 스트리밍할 수도 있습니다.

### Azure PowerShell로 스트리밍

로깅 정보를 스트리밍하려면 Azure PowerShell의 새 인스턴스를 시작하고 다음 명령을 사용합니다.

	Get-AzureWebSiteLog -Name webappname -Tail

이 명령을 실행하면 **-Name** 매개 변수로 지정된 웹 앱에 연결되고 로그 이벤트가 웹 앱에 발생하면 PowerShell 창으로 정보가 스트리밍되기 시작합니다. /LogFiles 디렉터리(d:/home/logfiles)에 저장된 .txt, .log 또는 .htm으로 끝나는 파일에 기록된 정보는 로컬 콘솔로 스트리밍됩니다.

오류와 같은 특정 이벤트를 필터링하려면 **-Message** 매개 변수를 사용합니다. 예:

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

HTTP와 같은 특정 로그 유형을 필터링하려면 **-Path** 매개 변수를 사용합니다. 예:

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

사용 가능한 경로 목록을 보려면 -ListPath 매개 변수를 사용합니다.

> [AZURE.NOTE] Azure PowerShell을 설치하지 않았거나 Azure 구독을 사용하도록 이를 구성하지 않은 경우 [Azure PowerShell을 사용하는 방법](/develop/nodejs/how-to-guides/powershell-cmdlets/)을 참조하세요.

### Azure 명령줄 인터페이스로 스트리밍

로깅 정보를 스트리밍하려면 새 명령 프롬프트, PowerShell, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

	azure site log tail webappname

이 명령을 실행하면 'webappname'이라는 웹 앱에 연결되고 로그 이벤트가 웹 앱에서 발생하면 창으로 정보가 스트리밍되기 시작합니다. /LogFiles 디렉터리(d:/home/logfiles)에 저장된 .txt, .log 또는 .htm으로 끝나는 파일에 기록된 정보는 로컬 콘솔로 스트리밍됩니다.

오류와 같은 특정 이벤트를 필터링하려면 **--Filter** 매개 변수를 사용합니다. 예:

	azure site log tail webappname --filter Error

HTTP와 같은 특정 로그 유형을 필터링하려면 **-Path** 매개 변수를 사용합니다. 예:

	azure site log tail webappname --path http

> [AZURE.NOTE] Azure Command-Line Interface를 설치하지 않았거나 Azure 구독을 사용하도록 Azure CLI를 구성하지 않은 경우 [Azure Command-Line Interface 사용 방법](../xplat-cli-install.md)을 참조하세요.

##<a name="understandlogs"></a> 방법: 진단 로그 이해

### 응용 프로그램 진단 로그

응용 프로그램 진단은 로그가 파일 시스템, 테이블 저장소 또는 Blob 저장소 중 어디에 저장되는지에 따라 .NET 응용 프로그램 관련 형식으로 정보를 저장합니다. 이벤트가 발생한 날짜 및 시간, 이벤트가 생성된 프로세스 ID, 이벤트 유형(정보, 경고, 오류), 이벤트 메시지 등 저장된 데이터의 기본 집합은 모든 세 저장소 유형 전체에서 동일합니다.

__파일 시스템__

파일 시스템에 로깅되거나 스트리밍을 통해 수신된 각 줄은 다음 형식과 같습니다.

	{Date}  PID[{process id}] {event type/level} {message}

예를 들어 오류 이벤트는 다음과 비슷하게 나타납니다.

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

파일 시스템에 로깅하면 사용 가능한 세 가지 방법의 가장 기본적인 정보, 즉 시간, 프로세스 ID, 이벤트 수준 및 메시지만 제공됩니다.

__테이블 저장소__

테이블 저장소에 로깅하면 추가 속성을 사용하여 테이블에 저장된 데이터와 이벤트에 대한 좀 더 세부적인 정보를 쉽게 검색할 수 있습니다. 다음 속성(열)이 테이블에 저장된 각 엔터티(행)에 사용됩니다.

속성 이름|값/형식
---|---
PartitionKey|yyyyMMddHH 형식의 이벤트 날짜/시간
RowKey|이 엔터티를 고유하게 식별하는 GUID 값
Timestamp|이벤트가 발생한 날짜 및 시간
EventTickCount|이벤트가 발생한 날짜 및 시간(눈금 형식, 더 높은 정밀도)
ApplicationName|웹 앱 이름
수준|이벤트 수준(예: 오류, 경고, 정보)
EventId|이 이벤트의 이벤트 ID<p><p>지정하지 않을 경우 0으로 기본 설정됨
InstanceId|이벤트가 발생한 웹앱의 인스턴스
Pid|프로세스 ID
Tid|이벤트가 생성된 스레드의 스레드 ID
Message|이벤트 세부 정보 메시지

__Blob 저장소__

Blob 저장소에 로깅하는 경우 데이터는 쉼표로 구분된 값(CSV) 형식으로 저장됩니다. 테이블 저장소와 마찬가지로 이벤트에 대해 좀 더 세부적인 정보를 제공하기 위해 추가 필드가 로깅됩니다. CSV에서 다음 속성이 각 행에 사용됩니다.

속성 이름|값/형식
---|---
Date|이벤트가 발생한 날짜 및 시간
수준|이벤트 수준(예: 오류, 경고, 정보)
ApplicationName|웹앱 이름
InstanceId|이벤트가 발생한 웹앱의 인스턴스
EventTickCount|이벤트가 발생한 날짜 및 시간(눈금 형식, 더 높은 정밀도)
EventId|이 이벤트의 이벤트 ID<p><p>지정하지 않을 경우 0으로 기본 설정됨
Pid|프로세스 ID
Tid|이벤트가 생성된 스레드의 스레드 ID
Message|이벤트 세부 정보 메시지

Blob에 저장된 데이터는 다음과 비슷합니다.

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] 로그의 첫 번째 줄에는 이 예에 나타난 대로 열 헤더가 포함됩니다.

### 실패한 요청 추적

실패한 요청 추적은 __fr######.xml__이라는 XML 파일에 저장됩니다. 로깅된 정보를 더 쉽게 볼 수 있도록 __freb.xsl__라는 XSL 스타일시트가 XML 파일과 동일한 디렉터리에 제공됩니다. Internet Explorer에서 XML 파일 중 하나를 열면 XSL 스타일시트를 사용하여 추적 정보에 서식이 지정되어 표시됩니다. 이는 다음과 비슷하게 나타납니다.

![브라우저에 표시된 실패한 요청](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### 자세한 오류 로그

자세한 오류 로그는 발생한 HTTP 오류에 대해 좀 더 자세한 정보를 제공하는 HTML 문서입니다. 이들은 단순한 HTML 문서이므로 웹 브라우저를 사용하여 볼 수 있습니다.

### 웹 서버 로그

웹 서버 로그는 [W3C 확장 로그 파일 형식](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)을 사용하여 서식이 지정됩니다. 이 정보는 텍스트 편집기를 사용하여 읽거나 [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619)와 같은 유틸리티를 사용하여 구문 분석할 수 있습니다.

> [AZURE.NOTE] Azure 웹 앱에서 생성된 로그는 __s-computername__, __s-ip__ 또는 __cs-버전__ 필드를 지원하지 않습니다.

##<a name="nextsteps"></a> 다음 단계

- [웹앱을 모니터링하는 방법](/manage/services/web-sites/how-to-monitor-websites/)
- [Visual Studio에서 Azure 웹앱 문제 해결](web-sites-dotnet-troubleshoot-visual-studio.md)
- [HDInsight에서 웹앱 로그 분석](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [Azure 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.
 

<!---HONumber=AcomDC_0914_2016-->