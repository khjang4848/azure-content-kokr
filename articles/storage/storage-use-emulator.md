<properties 
	pageTitle="| Microsoft Azure 개발 및 테스트에 대해 Azure 저장소 에뮬레이터를 사용하세요." 
	description="Azure 저장소 에뮬레이터는 Azure 저장소에 대한 개발 및 테스트에 대해 무료 로컬 개발 환경을 제공합니다. 요청을 인증하는 방법, 응용 프로그램에서 에뮬레이터에 연결하는 방법 및 명령줄 도구를 사용하는 방법을 포함하여 저장소 에뮬레이터에 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="tamram"/>

# 개발 및 테스트에 Azure 저장소 에뮬레이터 사용

## 개요

Microsoft Azure 저장소 에뮬레이터는 개발 목적으로 Azure Blob, 큐 및 테이블 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 저장소 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 저장소 서비스에 대해 응용 프로그램을 테스트할 수 있습니다. 에뮬레이터에서 응용 프로그램이 작동하는 방식에 만족하는 경우 Azure 저장소 계정을 클라우드에서 사용 하도록 전환할 수 있습니다.

> [AZURE.NOTE] 저장소 에뮬레이터는 [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)의 일부로 제공됩니다. [독립 실행형 설치 관리자](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)를 사용하여 저장소 에뮬레이터를 설치할 수도 있습니다. 저장소 에뮬레이터를 구성하려면 컴퓨터에 대한 관리 권한이 있어야 합니다.
> 
> 저장소 에뮬레이터는 현재 Windows에서만 실행됩니다.
>  
> 저장소 에뮬레이터의 어느 한 버전으로 작성한 데이터에 다른 버전으로 반드시 액세스할 수 있는 것은 아닙니다. 장기간 보존하려는 데이터는 저장소 에뮬레이터가 아닌 Azure 저장소 계정에 저장하는 것이 좋습니다.

## 저장소 에뮬레이터의 작동 원리
 
저장소 에뮬레이터는 로컬 Microsoft® SQL Server 인스턴스와 로컬 파일 시스템을 사용하여 Azure 저장소 서비스를 에뮬레이트합니다. 기본적으로 저장소 에뮬레이터는 Microsoft SQL Server 2012 Express LocalDB의 데이터베이스를 사용합니다. 저장소 에뮬레이터가 LocalDB가 아닌 SQL Server의 로컬 인스턴스에 액세스하도록 구성할 수 있습니다. 자세한 내용은.아래 [저장소 에뮬레이터 시작 및 초기화](#start-and-initialize-the-storage-emulator)를 참조하세요.

SQL Server Management Studio Express를 설치하여 LocalDB 설치를 관리할 수 있습니다. 저장소 에뮬레이터는 Windows 인증을 사용하여 SQL Server 또는 LocalDB에 연결합니다.

저장소 에뮬레이터와 Azure 저장소 서비스 간에는 기능에 몇 가지 차이점이 있습니다. 이러한 차이점에 대한 자세한 내용은 [저장소 에뮬레이터와 Azure 저장소 서비스의 차이점](#differences-between-the-storage-emulator-and-azure-storage)을 참조하세요.

## 저장소 에뮬레이터에 대한 인증 요청

익명 요청이 아니면 클라우드의 Azure 저장소의 경우와 마찬가지로 Azure 저장소 에뮬레이터에 대한 모든 요청을 인증해야 합니다. 공유 키 인증 또는 공유 액세스 서명 (SAS)을 사용하여 저장소 에뮬레이터에 대한 요청을 인증할 수 있습니다.

### 공유 키 자격 증명 인증

[AZURE.INCLUDE [저장소-에뮬레이터-연결-문자열-포함](../../includes/storage-emulator-connection-string-include.md)]

연결 문자열에 대한 자세한 내용은 [Azure 저장소 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

### 공유 액세스 서명 인증 

Xamarin 라이브러리와 같은 일부 Azure 저장소 클라이언트 라이브러리는 공유 액세스 서명 (SAS) 토큰 인증만 지원합니다. 공유 키 인증을 지원하는 도구 또는 응용 프로그램을 사용 하여 이 SAS 토큰을 만들어야 합니다. SAS 토큰을 만드는 쉬운 방법은 Azure PowerShell을 통하는 것입니다.

1. 아직 없는 경우 Azure PowerShell을 설치합니다. Azure PowerShell cmdlet의 최신 버전을 사용하는 것이 좋습니다. 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md#Install)을 참조하세요.

2. Azure PowerShell을 열고 다음 명령을 실행합니다. 사용자 고유 자격 증명을 사용하여 *ACCOUNT\_NAME* 및 *ACCOUNT\_KEY = =*을 바꿔야 합니다. Replace *CONTAINER\_NAME* 을 선택한 이름으로 바꿉니다.

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

새 컨테이너에 대해 결과적으로 생성된 공유 액세스 서명 URI는 다음과 유사해야 합니다.

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

이 예제를 사용하여 만든 공유 액세스 서명은 하루 동안 유효 합니다. 서명은 컨테이너 내에서 blob에 대해 전체 권한을 부여합니다(예: 읽기, 쓰기, 삭제 및 나열).

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.


## 저장소 에뮬레이터 시작 및 초기화

Azure 저장소 에뮬레이터를 시작하려면 시작 단추를 선택하거나 Windows 키를 누릅니다. **Azure 저장소 에뮬레이터** 입력을 시작하고 응용 프로그램 목록에서 Azure 저장소 에뮬레이터를 선택합니다.

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다.

저장소 에뮬레이터가 시작되면 명령줄 창이 표시됩니다. 이 명령줄 창을 사용하여 저장소 에뮬레이터를 시작/중지하고, 데이터를 지우고, 최신 상태를 가져오고, 에뮬레이터를 초기화할 수 있습니다. 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference)를 참조하세요.

명령줄 창을 닫으면 저장소 에뮬레이터가 계속 실행됩니다. 명령줄을 다시 불러오려면 저장소 에뮬레이터를 시작하는 것처럼 위의 단계를 수행합니다.

저장소 에뮬레이터를 처음 실행하면 로컬 저장소 환경이 자동으로 초기화됩니다. 초기화 프로세스에서는 LocalDB에 데이터베이스가 작성되며 각 로컬 저장소 서비스용으로 HTTP 포트가 예약됩니다.

저장소 에뮬레이터는 기본적으로 C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\Storage Emulator 디렉터리에 설치됩니다.

### 다른 SQL 데이터베이스를 사용하여 저장소 에뮬레이터를 초기화 합니다.

저장소 에뮬레이터 명령줄 도구를 사용하여 기본 LocalDB 인스턴스가 아닌 SQL 데이터베이스 인스턴스를 가리키도록 저장소 에뮬레이터를 초기화할 수 있습니다. 저장소 에뮬레이터에 대한 백엔드 데이터베이스를 초기화 하려면 관리자 권한으로 명령줄 도구를 실행해야 합니다.

1. **시작** 단추를 클릭하거나 **Windows** 키를 누릅니다. `Azure Storage Emulator`을 입력하기 시작하고 저장소 에뮬레이터 명령줄 도구를 불러올 때 표시되면 선택합니다.
2. 명령 프롬프트 창에 다음 명령을 입력합니다. 여기서 `<SQLServerInstance>`은 SQL Server 인스턴스의 이름입니다. LocalDb를 사용하려면 SQL Server 인스턴스로 `(localdb)\v11.0`을 지정합니다.

		AzureStorageEmulator init /server <SQLServerInstance> 
    
	기본 SQL Server 인스턴스를 사용하도록 에뮬레이터에 지시하는 다음 명령을 사용할 수도 있습니다.

    	AzureStorageEmulator init /server .\\ 

	또는 LocalDB 데이터베이스를 다시 초기화하는 다음 명령을 사용할 수도 있습니다.

    	AzureStorageEmulator init /forceCreate 

이 명령에 대한 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference)를 참조하세요.

## 저장소 에뮬레이터에서 리소스의 주소 지정

저장소 에뮬레이터에 대한 서비스 끝점은 Azure 저장소 계정의 끝점과 다릅니다. 이 차이는 로컬 컴퓨터가 도메인 이름 확인을 수행하지 않기 때문이며, 따라서 저장소 에뮬레이터 끝점에는 도메인 이름이 아닌 로컬 주소가 필요합니다.

Azure 저장소 계정에 리소스 주소를 지정하는 경우 다음의 체계를 사용하며, 계정 이름이 URI 호스트 이름의 일부인 경우에는 주소 지정되는 리소스는 URI 경로의 일부입니다.

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

예를 들어, 다음 URI는 Azure 저장소 계정의 blob에 대한 올바른 주소입니다.

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

저장소 에뮬레이터에서는 로컬 컴퓨터가 도메인 이름 확인을 수행하지 않기 때문에 계정 이름은 호스트 이름이 아닌 URI의 일부입니다. 저장소 에뮬레이터에서 실행되는 리소스에 대해다음 체계를 사용합니다.

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

예를 들어 저장소 에뮬레이터의 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

저장소 에뮬레이터에 대한 서비스 끝점은 다음과 같습니다.

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### RA-GRS를 사용하여 보조 계정 주소 지정

버전 3.1부터는 저장소 에뮬레이터 계정에 대해 RA-GRS(읽기 권한 지역 중복 복제)를 사용할 수 있습니다. 클라우드와 로컬 에뮬레이터의 저장소 리소스에 대해 계정 이름에 -secondary를 추가하면 보조 위치에 액세스할 수 있습니다. 예를 들어 저장소 에뮬레이터에서 읽기 전용 보조 계정을 사용하여 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] 저장소 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 저장소 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 [.NET용 Microsoft Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하세요.

## 저장소 에뮬레이터 명령줄 도구 참조

버전 3.0부터 저장소 에뮬레이터를 시작하면 명령줄 창 팝업이 표시됩니다. 명령줄 창을 사용하여 에뮬레이터를 시작/중지할 수 있으며 상태를 쿼리하고 기타 작업을 수행할 수도 있습니다.

> [AZURE.NOTE] Microsoft Azure 계산 에뮬레이터가 설치되어 있으면 저장소 에뮬레이터를 시작할 때 시스템 트레이 아이콘이 표시됩니다. 해당 아이콘을 마우스 오른쪽 단추로 클릭하면 표시되는 메뉴에서 그래픽 방식으로 저장소 에뮬레이터를 시작하고 중지할 수 있습니다.

### 명령줄 구문

	AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### 옵션

옵션 목록을 보려면 명령 프롬프트에 `/help`을(를) 입력합니다.

| 옵션 | 설명 | 명령 | 인수 |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **시작** | 저장소 에뮬레이터를 시작합니다. | `AzureStorageEmulator start [-inprocess]` | *-inprocess*: 새 프로세스를 만드는 대신 현재 프로세스에서 에뮬레이터를 시작합니다. |
| **중지** | 저장소 에뮬레이터를 중지합니다. | `AzureStorageEmulator stop` | |
| **상태** | 저장소 에뮬레이터의 상태를 인쇄합니다. | `AzureStorageEmulator status` | |
| **지우기** | 명령줄에 지정된 모든 서비스의 데이터를 지웁니다. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: blob 데이터를 지웁니다. <br/>*queue*: 큐 데이터를 지웁니다. <br/>*table*: 테이블 데이터를 지웁니다. <br/>*all*: 모든 서비스의 모든 데이터를 지웁니다. |
| **Init** | 에뮬레이터를 설정하기 위해 하는 일회 초기화를 수행 합니다. | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server serverName\\instanceName*: SQL 인스턴스를 호스팅하는 서버를 지정합니다. <br/>*-sqlinstance instanceName*: 기본 서버 인스턴스에서 사용할 SQL 인스턴스의 이름을 지정합니다.<br/>*-forcecreate*: 이미 존재하는 경우에도 SQL 데이터베이스를 강제로 만듭니다. <br/>*-inprocess*: 새 프로세스를 생성하는 대신 현재 프로세스의 초기화를 수행합니다. 초기화를 수행하려면 관리자 권한으로 현재 프로세스를 시작해야 합니다. |
                                                                                                                  
## 저장소 에뮬레이터와 Azure 저장소의 차이점

저장소 에뮬레이터는 로컬 SQL 인스턴스를 실행하는 에뮬레이트된 환경이기 때문에 클라우드에서 에뮬레이터와 Azure 저장소 계정 간에 기능에 일부 차이가 있습니다.

- 저장소 에뮬레이터는 단일 고정 계정과 알려진 인증 키만 지원합니다.

- 저장소 에뮬레이터는 확장 가능한 저장소 서비스가 아니며 많은 수의 동시 클라이언트를 지원하지 않습니다.

- [저장소 에뮬레이터에서 리소스의 주소 지정](#addressing-resources-in-the-storage-emulator)에 설명한 대로, 저장소 에뮬레이터 및 Azure 저장소 계정에서 리소스의 주소가 서로 다르게 지정됩니다. 이러한 차이는 도메인 이름 확인이 클라우드에서는 가능하지만 로컬 컴퓨터에서는 불가능하기 때문에 발생합니다.

- 버전 3.1부터는 저장소 에뮬레이터 계정에 대해 RA-GRS(읽기 권한 지역 중복 복제)를 사용할 수 있습니다. 에뮬레이터에서 모든 계정은 RA-GRS 활성화 되어 있으며 기본 및 보조 복제본 간에 지연되지 않습니다. Blob 서비스 통계 가져오기, 큐 서비스 통계 가져오기 및 테이블 서비스 통계 가져오기 작업은 보조 계정에서 지원되며 기본 SQL 데이터베이스에 따라 현재 시간으로 `LastSyncTime` 응답 요소의 값을 항상 반환합니다.

	저장소 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 저장소 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 [.NET용 Microsoft Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하세요.

- 파일 서비스 및 SMB 프로토콜 서비스 끝점은 저장소 에뮬레이터에서 현재 지원되지 않습니다.

- 사용 중인 에뮬레이터 버전에서 지원되지 않는 저장소 서비스 버전을 사용하는 경우 저장소 에뮬레이트에서 VersionNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 반환됩니다.

### Blob 저장소의 차이점 

다음과 같은 차이점이 에뮬레이터의 Blob 저장소에 적용됩니다.

- 저장소 에뮬레이터는 blob 크기를 최대 2GB까지만 지원합니다.

- 임대 ID가 요청의 일부로 지정되어 있지 않은 경우에도 Put Blob 작업은 저장소 에뮬레이터에 있으며 활성 임대가 있는 blob에 대해 성공할 수 있습니다.

- 추가 Blob 작업은 에뮬레이터에서 지원되지 않습니다. 추가 Blob에 대한 작업을 시도하면 FeatureNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 반환됩니다.

### 테이블 저장소의 차이점 

다음과 같은 차이점이 에뮬레이터의 테이블 저장소에 적용됩니다.

- 저장소 에뮬레이터에서 테이블 서비스의 날짜 속성은 SQL Server 2005에서 지원되는 범위만 지원합니다. *즉*, 날짜 속성은 1753년 1월 1일 이후여야 합니다. 1753년 1월 1일 이전의 모든 날짜는 이 값으로 변경됩니다. 날짜의 정밀도는 SQL Server 2005의 정밀도로 제한되며, 즉 날짜의 정밀도는 1/300 초입니다.

- 저장소 에뮬레이터는 각각 512 바이트 미만의 파티션 키 및 행 키 속성 값을 지원합니다. 또한 계정 이름, 테이블 이름 및 키 속성 이름의 총 크기는 900 바이트를 초과할 수 없습니다.

- 저장소 에뮬레이터의 테이블에 있는 행의 총 크기는 1MB 미만으로 제한 합니다.

- 저장소 에뮬레이터에서 데이터 형식 `Edm.Guid` 또는 `Edm.Binary`의 속성은 쿼리 필터 문자열의 `Equal (eq)` 및 `NotEqual (ne)` 비교 연산자만 지원합니다.

### 큐 저장소의 차이점

에뮬레이터에서 큐 저장소에 특정 차이점이 있습니다.

## 저장소 에뮬레이터 릴리스 정보

### 버전 4.5

- 백업 데이터베이스의 이름이 변경되었을 때 저장소 에뮬레이터의 초기화 및 설치가 실패하는 버그가 수정되었습니다.

### 버전 4.4

- 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2015-12-11 버전의 저장소 서비스를 지원합니다.

- 저장소 에뮬레이터의 blob 데이터 가비지 수집은 이제 많은 수의 blob 처리할 때 더 효율적입니다.

- 컨테이너 ACL XML이 저장소 서비스의 방법과는 약간 다르게 유효성 검사가 이뤄지게 하던 버그를 수정했습니다.

- 때로 최대 및 최소 DateTime 값이 잘못된 표준 시간대에 보고되도록 하던 버그를 수정했습니다.

### 버전 4.3

- 저장소 에뮬레이터는 이제 Blob, 큐 및 테이블 서비스 끝점에서 2015-07-08 버전의 저장소 서비스를 지원합니다.

### 버전 4.2

- 저장소 에뮬레이터는 이제 Blob, 큐 및 테이블 서비스 끝점에서 2015-04-05 버전의 저장소 서비스를 지원합니다.

### 버전 4.1

- 저장소 에뮬레이터는 이제 새로운 추가 Blob 기능을 제외하고 Blob, 큐 및 테이블 서비스 끝점에서 2015-02-21 버전의 저장소 서비스를 지원합니다.

- 에뮬레이터 버전에서 지원되지 않는 저장소 서비스 버전을 사용하는 경우 이제 저장소 에뮬레이트에서 의미 있는 오류 메시지가 반환됩니다. 최신 버전의 에뮬레이터를 사용하는 것이 좋습니다. VersionNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 발생하는 경우 최신 버전의 저장소 에뮬레이터를 다운로드하세요.

- 경합 조건으로 인해 동시 병합 작업 중 잘못된 테이블 엔터티 데이터가 발생하는 버그가 수정되었습니다.

### 버전 4.0

- 저장소 에뮬레이터 실행 파일의 이름이 *AzureStorageEmulator.exe*로 바뀌었습니다.

### 버전 3.2

- 저장소 에뮬레이터는 이제 Blob, 큐 및 테이블 서비스 끝점에서 2014-02-14 버전의 저장소 서비스를 지원합니다. 파일 서비스 끝점은 저장소 에뮬레이터에서 현재 지원되지 않습니다. 2014-02-14 버전에 대한 자세한 내용은 [Azure 저장소 서비스에 대한 버전 관리](https://msdn.microsoft.com/library/azure/dd894041.aspx)를 참조하세요.

### 버전 3.1

- 읽기 액세스 지역 중복 저장소 (RA-GRS)는 이제 저장소 에뮬레이터에서 지원됩니다. Blob 서비스 통계 가져오기, 큐 서비스 통계 가져오기 및 테이블 서비스 통계 가져오기 API는 보조 계정에서 지원되며 기본 SQL 데이터베이스에 따라 현재 시간으로 LastSync 시간 응답 요소의 값을 항상 반환합니다. 저장소 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 저장소 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 .NET용 Microsoft Azure 저장소 클라이언트 라이브러리 참조를 참조하세요.

### 버전 3.0

- Azure 저장소 에뮬레이터는 계산 에뮬레이터와 같은 패키지에 더 이상 제공되지 않습니다.

- 저장소 에뮬레이터 그래픽 사용자 인터페이스는 더 이상 사용되지 않으며, 대신 스크립트 가능한 명령줄 인터페이스가 사용됩니다. 명령줄 인터페이스에 대한 자세한 내용은 저장소 에뮬레이터 명령줄 도구 참조를 참조하세요. 그래픽 인터페이스는 버전 3.0에 계속 있지만 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 저장소 에뮬레이터 UI 표시 보기를 선택하여 계산 에뮬레이터를 설치할 때에만 액세스 할 수 있습니다.

- 이제 Azure 저장소 서비스의 2013-08-15 버전이 완벽 하게 지원됩니다. (이전에 이 버전은 저장소 에뮬레이터 버전 2.2.1 미리 보기에서만 지원되었습니다.)

<!---HONumber=AcomDC_0914_2016-->