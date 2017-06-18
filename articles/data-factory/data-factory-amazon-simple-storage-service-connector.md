<properties 
	pageTitle="Data Factory를 사용하여 Amazon 단순 저장소 서비스에서 데이터 이동 | Microsoft Azure" 
	description="Azure 데이터 팩터리를 사용하여 Amazon 단순 저장소 서비스 (S3)에서 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 Amazon 단순 저장소 서비스에서 데이터 이동

이 문서는 Azure 데이터 팩토리에서 복사 활동을 사용하여 Amazon 단순 저장소 서비스 (S3)에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 설명합니다. 이 문서는 복사 활동을 사용하여 데이터 이동 및 지원되는 원본/싱크 데이터 저장소 목록의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 기초로 작성됩니다.

현재 데이터 팩터리는 Amazon S3에서 다른 데이터 저장소로의 데이터 이동 만을 지원하지만, 다른 데이터 저장소에서 Amazon S3로 데이터 이동은 지원하지 않습니다.

## 데이터 복사 마법사
Amazon S3에서 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Amazon S3에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 [여기에](data-factory-data-movement-activities.md#supported-data-stores) 명시된 싱크는 어느 것에나 데이터를 복사할 수 있습니다.

## 샘플: Amazon S3에서 Azure Blob으로 데이터 복사
이 샘플은 Amazon S3 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.
 
이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

- [AwsAccessKey](#linked-service-properties) 형식의 연결된 서비스.
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
- [AmazonS3](#dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
- [FileSystemSource](#copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 웹 테이블의 데이터를 Amazon S3으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Amazon S3 연결된 서비스**

	{
	    "name": "AmazonS3LinkedService",
	    "properties": {
	        "type": "AwsAccessKey",
	        "typeProperties": {
	            "accessKeyId": "<access key id>",
	            "secretAccessKey": "<secret access key>"
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

**Amazon S3 입력 데이터 집합**

**"external": true**로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다. 파이프라인에서의 작업에 의해 생성되지 않은 입력 데이터 집합에서 이 속성을 true로 설정합니다.

	{
	    "name": "AmazonS3InputDataset",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "AmazonS3LinkedService",
	        "typeProperties": {
	            "key": "testFolder/test.orc",
	            "bucketName": "testbucket",
	            "format": {
	                "type": "OrcFormat"
	            }
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
	            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **소스** 형식은 **FileSystemSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다.
	
	{
	    "name": "CopyAmazonS3ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
                        	"type": "FileSystemSource",
                        	"recursive": true
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AmazonS3InputDataset"
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
	                "name": "AmazonS3ToBlob"
	            }
	        ],
	        "start": "2014-08-08T18:00:00Z",
	        "end": "2014-08-08T19:00:00Z"
	    }
	}



## 연결된 서비스 속성

다음 테이블은 Amazon S3 (**AwsAccessKey**) 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | 비밀 액세스 키의 ID입니다. | string | 예 |
| secretAccessKey | 비밀 액세스 키 자체입니다. | 암호화된 비밀 문자열 | 예 | 


## 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AmazonS3** 형식(AmazonS3 데이터 집합을 포함)의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------- | ------ | 
| bucketName | S3 버킷 이름입니다. | 문자열 | 예 |
| key | S3 개체 키입니다. | 문자열 | 아니요 | 
| 접두사 | S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. 키가 비어 있을 때에만 적용됩니다. | 문자열 | 아니요 | 
| 버전 | S3 버전 관리를 사용하도록 설정되면 S3 개체의 버전입니다. | 문자열 | 아니요 |  
| format | **TextFormat**, **AvroFormat**, **JsonFormat** 및 **OrcFormat**과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 세부 정보는 [TextFormat 지정](#specifying-textformat), [AvroFormat 지정](#specifying-avroformat), [JsonFormat 지정](#specifying-jsonformat) 및 [OrcFormat 지정](#specifying-orcformat) 섹션을 참조하세요. 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뛸 수 있습니다.| 아니요
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate** 및 **BZip2**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 현재 **AvroFormat** 또는 **OrcFormat** 형식인 데이터에 대한 압축 설정은 지원되지 않습니다. 자세한 내용은 [압축 지원](#compression-support) 섹션을 참조하세요. | 아니요 |

> [AZURE.NOTE] bucketName + 키는 S3 개체의 위치를 지정합니다. 여기서 버킷은 S3 개체에 대한 루트 컨테이너이며 키는 S3 개체의 전체 경로입니다.

### 접두사를 사용한 샘플 데이터 집합

	{
	    "name": "dataset-s3",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "link- testS3",
	        "typeProperties": {
	            "prefix": "testFolder/test",
	            "bucketName": "testbucket",
	            "format": {
	                "type": "OrcFormat"
	            }
	        },
			"availability": {
		    	"frequency": "Hour",
			    "interval": 1
		    },
			"external": true
	    }
	}

### 샘플 데이터 집합 (버전 포함)

	{
	    "name": "dataset-s3",
	    "properties": {
	        "type": "AmazonS3",
	        "linkedServiceName": "link- testS3",
	        "typeProperties": {
	            "key": "testFolder/test.orc",
	            "bucketName": "testbucket",
	            "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
	            "format": {
	                "type": "OrcFormat"
	            }
	        },
			"availability": {
		    	"frequency": "Hour",
			    "interval": 1
		    },
			"external": true
	    }
	}


### S3에 대한 동적 경로

이 샘플에서는 Amazon S3 데이터 집합의 키 및 bucketName 속성에 대해 고정 값을 사용합니다.

	"key": "testFolder/test.orc",
	"bucketName": "testbucket",

SliceStart와 같은 시스템 변수를 사용하여 데이터 팩터리가 런타임에 동적으로 키와 bucketName 계산하게 할 수 있습니다.

	"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
	"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Amazon S3 데이터 집합의 접두사 속성에 대해서도 동일하게 수행할 수 있습니다. 지원되는 함수 및 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 참조하세요.


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## 복사 활동 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

복사 작업의 원본이 **FileSystemSource** 형식인 경우(Amazon S3 포함) typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- | 
| recursive | S3 개체를 디렉터리 아래에 재귀적으로 나열할 것인지를 지정합니다. | True/False | 아니요 | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

## 다음 단계
다음 문서를 참조하세요.
- 복사 작업을 포함하는 파이프라인 만들기 위한 단계별 지침이 들어 있는 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

<!---HONumber=AcomDC_0914_2016-->