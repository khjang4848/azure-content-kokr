<properties 
   pageTitle="Azure Data Lake Store에 대한 진단 로그 보기 | Microsoft Azure" 
   description="Azure Data Lake Store에 대한 진단 로그를 설정하고 액세스하는 방법을 이해합니다 " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Azure Data Lake Store에 대한 진단 로그에 액세스

Data Lake Store 계정에 대한 진단 로깅을 사용하는 방법 및 계정에 대해 수집된 로그를 보는 방법에 대해 알아봅니다.

조직에서는 Azure Data Lake Store 계정에 대한 진단 로깅을 사용하도록 설정하여 데이터에 액세스하는 사용자의 목록, 데이터가 액세스되는 빈도, 계정에 저장된 데이터 양과 같은 정보를 제공하는 데이터 액세스 감사 추적을 수집할 수 있습니다.

## 필수 조건

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- **Azure Data Lake 저장소 계정** [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다.

## Data Lake Store 계정에 대한 진단 로깅 사용

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. Data Lake Store 계정을 열고 Data Lake Store 계정 블레이드에서 **설정**을 클릭한 다음 **진단 설정**을 클릭합니다.

3. **진단** 블레이드에서 진단 로깅을 구성하려면 다음과 같이 변경합니다.

	![진단 로깅 사용](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "진단 로그 활성화")

	* 진단 로깅을 사용하려면 **상태**를 **켜기**로 설정합니다.
	* 두 가지 방법으로 데이터를 저장/처리하도록 선택할 수 있습니다.
		* Azure 이벤트 허브에 로그 데이터를 스트리밍하려면 **이벤트 허브로 내보내기** 옵션을 선택합니다. 들어오는 로그를 실시간으로 분석하는 다운스트림 처리 파이프라인을 사용하는 경우 대개 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 사용하려는 Azure 이벤트 허브에 대한 세부 정보를 제공해야 합니다.
		* **저장소 계정에 내보내기** 옵션을 선택하여 Azure 저장소 계정에 로그를 저장합니다. 나중에 배치로 처리할 데이터를 보관하려는 경우 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 Azure 저장소 계정을 제공하여 로그를 저장해야 합니다.
	* 감사 로그 또는 요청 로그를 가져올지, 혹은 둘 모두를 가져올지를 지정합니다.
	* 데이터를 유지해야 하는 일 수를 지정합니다.
	* **Save**를 클릭합니다.

진단 설정을 사용하도록 설정했으면 **진단 로그** 탭에서 로그를 볼 수 있습니다.

## Data Lake Store 계정에 대한 진단 로그 보기

두 가지 방법으로 Data Lake Store 계정에 대한 로그 데이터를 볼 수 있습니다.

* Data Lake Store 계정 설정 보기에서
* 데이터가 저장된 Azure 저장소 계정에서

### Data Lake Store 설정 보기 사용

1. Data Lake Store 계정 **설정** 블레이드에서 **진단 로그**를 클릭합니다.

	![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "진단 로그 보기")

2. **진단 로그** 블레이드에서 **감사 로그** 및 **요청 로그**로 분류된 로그가 표시됩니다.
	* 요청 로그는 Data Lake Store 계정에 대한 모든 API 요청을 캡처합니다.
	* 감사 로그는 요청 로그와 비슷하지만 Data Lake Store 계정에 수행된 작업의 훨씬 더 세부적인 분석 결과를 제공합니다. 예를 들어, 요청 로그에서 단일 업로드 API 호출은 감사 로그에서 여러 "추가" 작업을 발생시킬 수 있습니다.

3. 각 로그 항목에 대한 **다운로드** 링크를 클릭하여 로그를 다운로드합니다.

### 로그 데이터를 포함하는 Azure 저장소 계정에서

1. 로깅을 위한 Data Lake Store와 연결된 Azure 저장소 계정 블레이드를 열고 Blob을 클릭합니다. **Blob 서비스** 블레이드는 두 개의 컨테이너를 나열합니다.

	![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "진단 로그 보기")

	* **insights-logs-audit** 컨테이너는 감사 로그를 포함합니다.
	* **insights-logs-requests** 컨테이너는 요청 로그를 포함합니다.

2. 이러한 컨테이너 내에서 로그는 다음 구조로 저장됩니다.

	![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "진단 로그 보기")

	예를 들어, 감사 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`일 수 있습니다.

	마찬가지로 요청 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`일 수 있습니다.

## 로그 데이터의 구조 이해

감사 로그 및 요청 로그는 JSON 형식입니다. 이 섹션에서는 요청 로그 및 감사 로그에 대한 JSON의 구조를 살펴봅니다.

### 요청 로그

다음은 JSON 형식인 요청 로그의 샘플 항목입니다. 각 Blob는 **레코드**라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-07T21:02:53.456Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Requests",
			 "operationName": "GETCustomerIngressEgress",
			 "resultType": "200",
			 "callerIpAddress": "::ffff:1.1.1.1",
			 "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
			 "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
			 "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
		}
		,
		. . . .
	  ]
	}

#### 요청 로그 스키마

| Name | 형식 | 설명 |
|-----------------|--------|--------------------------------------------------------------------------------|
| 실시간 | 문자열 | 로그의 타임스탬프(UTC) |
| resourceId | String | 작업이 수행되는 리소스의 ID |
| 카테고리 | 문자열 | 로그 범주 예를 들어 **요청** |
| operationName | String | 기록된 작업의 이름 예를 들어 getfilestatus |
| resultType | 문자열 | 작업의 상태, 예를 들어 200 |
| callerIpAddress | 문자열 | 요청한 클라이언트의 IP 주소 |
| CorrelationId | 문자열 | 관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| ID | Object | 로그를 생성하는 ID |
| properties | JSON | 자세한 내용은 다음을 참조하세요. |

#### 요청 로그 속성 스키마

| Name | 형식 | 설명 |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod | String | 작업에 사용된 HTTP 메서드 예를 들어 GET |
| Path | 문자열 | 작업이 수행된 경로 |
| RequestContentLength | int | HTTP 요청의 콘텐츠 길이 |
| ClientRequestId | String | 이 요청을 고유하게 식별하는 ID |
| StartTime | 문자열 | 서버가 요청을 받은 시간 |
| EndTime | 문자열 | 서버가 응답을 전송한 시간 |

### 감사 로그

다음은 JSON 형식인 감사 로그의 샘플 항목입니다. 각 Blob는 **레코드**라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

	{
	"records": 
	  [		
		. . . .
		,
		{
			 "time": "2016-07-08T19:08:59.359Z",
			 "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
			 "category": "Audit",
			 "operationName": "SeOpenStream",
			 "resultType": "0",
			 "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
			 "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
			 "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
		}
		,
		. . . .
	  ]
	}

#### 감사 로그 스키마

| Name | 형식 | 설명 |
|-----------------|--------|--------------------------------------------------------------------------------|
| 실시간 | 문자열 | 로그의 타임스탬프(UTC) |
| resourceId | String | 작업이 수행되는 리소스의 ID |
| 카테고리 | 문자열 | 로그 범주 예를 들어 **감사** |
| operationName | String | 기록된 작업의 이름 예를 들어 getfilestatus |
| resultType | 문자열 | 작업의 상태, 예를 들어 200 |
| CorrelationId | 문자열 | 관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| ID | Object | 로그를 생성하는 ID |
| properties | JSON | 자세한 내용은 다음을 참조하세요. |

#### 감사 로그 속성 스키마

| Name | 형식 | 설명 |
|------------|--------|------------------------------------------|
| StreamName | 문자열 | 작업이 수행된 경로 |


## 로그 데이터를 처리하는 샘플

Azure Data Lake Store에서는 로그 데이터를 처리하고 분석하는 방법에 대한 샘플을 제공합니다. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)에서 샘플을 찾을 수 있습니다.


## 참고 항목

- [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->