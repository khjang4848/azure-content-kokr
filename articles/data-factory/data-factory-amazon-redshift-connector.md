<properties 
	pageTitle="Data Factory를 사용하여 Amazon Redshift에서 데이터 이동 | Microsoft Azure" 
	description="Azure 데이터 팩터리를 사용하여 Amazon Redshift에서 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="08/23/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 Amazon Redshift에서 데이터 이동

이 문서는 Azure 데이터 팩토리에서 복사 활동을 사용하여 Amazon Redshift에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 설명합니다. 이 문서는 복사 활동 및 원본/싱크 데이터 저장소 목록을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 기초로 작성됩니다.

현재 데이터 팩터리는 Amazon Redshift에서 다른 데이터 저장소로의 데이터 이동 만을 지원하지만, 다른 데이터 저장소에서 Amazon Redshift로 데이터 이동은 지원하지 않습니다.

## 필수 조건

- 온-프레미스 데이터 저장소로 데이터를 이동하는 경우 데이터 관리 게이트웨이(컴퓨터의 IP 주소 사용)에 Amazon Redshift 클러스터에 대한 액세스를 부여합니다. 자세한 내용은 [클러스터에 대한 액세스 권한 부여](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)를 참조하세요.
- Azure 데이터 저장소에 데이터를 이동하는 경우, Microsoft Azure 데이터 센터에서 사용하는 IP 주소 범위(SQL 범위 포함)를 계산하기 위해 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.

## 데이터 복사 마법사
Amazon Redshift에서 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Amazon Redshift에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 [여기에](data-factory-data-movement-activities.md#supported-data-stores) 명시된 싱크는 어느 것에나 데이터를 복사할 수 있습니다.

## 샘플: Amazon Redshift에서 Azure Blob으로 데이터 복사
이 샘플은 Amazon Redshift 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.
 
이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

- [AmazonRedshift](#linked-service-properties) 형식의 연결된 서비스입니다.
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
- [RelationalTable](#dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
- [RelationalSource](#copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 Amazon Redshift의 쿼리 결과에서 blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Amazon Redshift 연결된 서비스**

	{
	    "name": "AmazonRedshiftLinkedService",
	    "properties":
	    {
	        "type": "AmazonRedshift",
	        "typeProperties":
	        {
	            "server": "< The IP address or host name of the Amazon Redshift server >",
	            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
	            "database": "<The database name of the Amazon Redshift database>",
	            "username": "<username>",
	            "password": "<password>"
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

**Amazon Redshift 입력 데이터 집합**

**"external": true**로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다. 파이프라인에서의 작업에 의해 생성되지 않은 입력 데이터 집합에서 이 속성을 true로 설정합니다.

	{
	    "name": "AmazonRedshiftInputDataset",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "AmazonRedshiftLinkedService",
	        "typeProperties": {
	            "tableName": "<Table name>"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    }
	}


**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.
	
	{
	    "name": "CopyAmazonRedshiftToBlob",
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
	                        "name": "AmazonRedshiftInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
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
	                "name": "AmazonRedshiftToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## 연결된 서비스 속성

다음 테이블은 Amazon Redshift 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| type | 형식 속성은 **AmazonRedshift**로 설정되어야 합니다. | 예 |
| server | Amazon Redshift 서버의 IP 주소 또는 호스트 이름입니다. | 예 |
| 포트 | Amazon Redshift 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트 수입니다. | 기본값이 없음: 5439 |
| database | Amazon Redshift 데이터베이스의 이름입니다. | 예 |
| username | 데이터베이스에 대한 액세스 권한이 있는 사용자의 이름입니다.| 예 |
| password | 사용자 계정의 password입니다.| 예 |


## 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식(Amazon Redshift 데이터 집합을 포함)의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | 연결된 서비스가 참조하는 Amazon Redshift 데이터베이스에서 테이블의 이름입니다. | 아니요(**RelationalSource**의 **쿼리**가 지정된 경우) | 

## 복사 활동 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

복사 작업의 원본이 **RelationalSource** 형식인 경우 (Amazon Redshift 포함) typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| 쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: select * from MyTable. | 아니요(**데이터 집합**의 **tableName**이 지정된 경우) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Amazon Redshift에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Amazon Redshift에 데이터를 이동하는 경우 Amazon Redshift 형식에서 .NET 형식으로 이동에 다음 매핑이 사용됩니다.

Amazon Redshift 형식 | .NET 기반 형식
-------------------- | ---------------
SmallInt | Int16
INTEGER | Int32
BIGINT | Int64
DECIMAL | 10진수
Real | 단일
double precision | Double
BOOLEAN | 문자열
CHAR | 문자열
VARCHAR | 문자열
DATE | DateTime
TIMESTAMP | DateTime
TEXT | 문자열



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

## 다음 단계
다음 문서를 참조하세요.
- 복사 작업을 포함하는 파이프라인 만들기 위한 단계별 지침이 들어 있는 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

<!---HONumber=AcomDC_0914_2016-->