<properties 
	pageTitle="ODBC 데이터 저장소에서 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터를 이동하는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터 이동
이 문서에서는 Azure 데이터 팩터리에서 복사 작업을 사용하여 온-프레미스 ODBC 데이터 저장소에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 데이터 팩터리는 온-프레미스 ODBC 데이터 저장소에서 다른 데이터 저장소로 데이터 이동만을 지원합니다. 다른 데이터 저장소에서 온-프레미스 ODBC 데이터 저장소로 데이터 이동은 지원하지 않습니다.


## 연결 사용
데이터 팩터리 서비스는 데이터 관리 게이트웨이를 사용하여 온-프레미스 ODBC 원본에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. Azure IaaS VM에서 호스팅되는 경우 ODBC 데이터 저장소에 연결하려면 게이트웨이를 사용합니다.

게이트웨이를 동일한 온-프레미스 컴퓨터 또는 ODBC 데이터 저장소로 Azure VM에 설치할 수 있습니다. 그러나 리소스 경합을 방지하고 성능 향상을 꾀하려면 게이트웨이를 별도 컴퓨터/Azure IaaS VM에 설치하는 것이 좋습니다. 별도 컴퓨터에 게이트웨이를 설치하는 경우 컴퓨터는 ODBC 데이터 저장소를 사용하여 컴퓨터에 액세스할 수 있어야 합니다.

데이터 관리 게이트웨이 외에도 게이트웨이 컴퓨터에서 데이터 저장소에 대한 ODBC 드라이버를 설치해야 합니다.

> [AZURE.NOTE] 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.

## 데이터 복사 마법사
ODBC 원본에서 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 ODBC 원본에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores) 에 설명한 싱크로 데이터를 복사할 수 있습니다.


## 샘플: ODBC 데이터 저장소에서 Azure Blob로 데이터 복사

이 샘플은 ODBC에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.
 
이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1.	[OnPremisesOdbc](#odbc-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[RelationalTable](#odbc-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.	[RelationalSource](#odbc-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 ODBC 데이터 저장소의 쿼리 결과에서 Blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**ODBC 연결된 서비스** 이 예제에서는 기본 인증을 사용합니다. 사용할 수 있는 다른 유형의 인증은 [ODBC 연결된 서비스](#odbc-linked-service-properties) 섹션을 참조하세요.

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Azure 저장소 연결된 서비스**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**ODBC 입력 데이터 집합**

샘플은 ODBC 데이터베이스에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn"이라는 열이 포함되었다고 가정합니다.

"external": "true"로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}



**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "MM"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "dd"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "HH"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**복사 작업을 포함하는 파이프라인**

파이프라인은 이러한 입력 및 출력 데이터 집합을 사용하도록 구성되며 매시간 실행되도록 예약되는 복사 활동을 포함합니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.
	
	{
	    "name": "CopyODBCToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## ODBC 연결된 서비스 속성

다음 테이블은 ODBC 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| type | 형식 속성은 다음으로 설정해야 함: **OnPremisesOdbc** | 예 |
| connectionString | 선택적 암호화된 자격 증명 및 연결 문자열의 비 액세스 자격 증명 부분입니다. 다음 섹션의 예제를 참조하십시오. | 예
| 자격 증명 | 드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 예: “Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”. | 아니요
| authenticationType | ODBC 데이터 저장소에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명 및 기본입니다. | 예 | 
| username | 기본 인증을 사용하는 경우 사용자 이름을 지정합니다. | 아니요 | 
| password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. | 아니요 | 
| gatewayName | 데이터 팩터리 서비스가 ODBC 데이터 저장소에 연결하는 데 사용해야 하는 게이트웨이의 이름. | 예 |


온-프레미스 ODBC 데이터 저장소의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)을 참조하세요.

### 기본 인증 사용

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### 암호화된 자격 증명으로 기본 인증 사용
[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx)(Azure PowerShell의 1.0 버전 ) cmdlet 또는 [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx)(Azure PowerShell의 0.9 이전 버전)를 사용하여 자격 증명을 암호화할 수 있습니다.

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### 익명 인증 사용

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## ODBC 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식(ODBC 데이터 집합을 포함)의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | ODBC 데이터 저장소에 있는 테이블의 이름입니다. | 예 | 

## ODBC 복사 활동 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **RelationalSource** 형식인 복사 작업(ODBC 포함)에서 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| 쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: select * from MyTable. | 예 | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### ODBC에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

ODBC 데이터 저장소에서 데이터를 이동할 때 [ODBC 데이터 형식 매핑](https://msdn.microsoft.com/library/cc668763.aspx) 토픽에서 설명된 대로 ODBC 데이터 형식은 .NET 형식에 매핑됩니다.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## GE Historian 저장소
다음 예제와 같이 [GE Proficy Historian(현재 GE Historian)](http://www.geautomation.com/products/proficy-historian) 데이터 저장소를 Azure Data Factory에 연결하는 ODBC 연결된 서비스를 만듭니다.

	{
	    "name": "HistorianLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
			    "connectionString": "DSN=<name of the GE Historian store>",
			    "gatewayName": "<gateway name>",
			    "authenticationType": "Basic",
			    "userName": "<user name>",
			    "password": "<password>"
	        }
	    }
	}

온-프레미스 컴퓨터에서 데이터 관리 게이트웨이를 설치하고 포털에 게이트웨이를 등록합니다. 온-프레미스 컴퓨터에 설치된 게이트웨이는 GE Historian용 ODBC 드라이버를 사용하여 GE Historian 데이터 저장소에 연결합니다. 따라서 게이트웨이 컴퓨터에 아직 설치되지 않은 경우 해당 드라이버를 설치합니다. 자세한 내용은 [연결 사용](#enabling-connectivity) 섹션을 참조하세요.

Data Factory 솔루션에서 GE Historian 저장소를 사용하기 전에 게이트웨이에서 다음 섹션의 지침을 사용하여 데이터 저장소에 연결할 수 있는지 여부를 확인합니다.

복사 작업에서 ODBC 데이터 저장소를 원본 데이터 저장소로 사용하는 데 대한 자세한 개요를 보려면 문서를 처음부터 읽어보세요.

## 연결 문제 해결
연결 문제를 해결하려면 **데이터 관리 게이트웨이 구성 관리자**의 **진단** 탭을 사용합니다.

1. **데이터 관리 게이트웨이 구성 관리자**를 시작합니다. "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe"를 직접 실행하거나 다음 이미지에서처럼 **게이트웨이**를 검색하여 **Microsoft 데이터 관리 게이트웨이** 응용 프로그램에 대한 링크를 찾을 수 있습니다.

	![게이트웨이 검색](./media/data-factory-odbc-connector/search-gateway.png)
2. **진단** 탭으로 전환합니다.

	![게이트웨이 진단](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. 데이터 저장소(연결된 서비스)의 **형식**을 선택합니다.
4. **인증**을 지정하고 데이터 저장소에 연결된 **자격 증명**을 입력하거나 **연결 문자열**을 입력합니다.
5. **연결 테스트**를 클릭하여 데이터 저장소에 대한 연결을 테스트합니다.

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->