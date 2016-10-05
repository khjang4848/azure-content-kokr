<properties 
	pageTitle="Azure SQL 데이터 웨어하우스 간 데이터 이동 | Microsoft Azure" 
	description="Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터 이동

이 문서에서는 복사 작업을 사용하여 Azure SQL Data Warehouse를 이동하는 방법에 대해 간략하게 설명합니다.

Azure SQL 데이터 웨어하우스로 데이터를 로드하는 동안 PolyBase를 사용할지 여부를 지정할 수 있습니다. Azure SQL 데이터 웨어하우스로 데이터를 로드할 때 최상의 성능을 얻으려면 PolyBase를 사용하는 것이 좋습니다. 자세한 내용은 [PolyBase를 사용하여 Azure SQL 데이터 웨어하우스에 데이터 로드](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요.


## 데이터 복사 마법사
Azure SQL 데이터 웨어하우스 간에 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure SQL 데이터 웨어하우스 및 Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.


> [AZURE.NOTE] 
Azure 데이터 팩터리 서비스에 대한 개요는 [Azure 데이터 팩터리 소개](data-factory-introduction.md)를 참조하세요.
> 
> 이 문서에서는 JSON 예제를 제공하지만 데이터 팩터리를 만들기 위한 단계별 지침을 제공하지 않습니다. Azure 데이터 팩터리에서 복사 작업을 사용하는 단계별 지침이 있는 빠른 연습은 [자습서: Azure Blob에서 Azure SQL 데이터베이스에 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.


## 샘플: Azure SQL 데이터 웨어하우스에서 Azure Blob에 데이터 복사

샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1. [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3. [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4. [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure SQL Data Warehouse의 테이블에서 Blob으로 (매시간, 매일 등) 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 데이터 웨어하우스 연결된 서비스:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL 데이터 웨어하우스 입력 데이터 집합:**

샘플은 Azure SQL 데이터 웨어하우스에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn" 라는 열이 포함되었다고 가정합니다.
 
"external": "true"로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **SqlDWSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

> [AZURE.NOTE] 예에서 **sqlReaderQuery**는 SqlDWSource에 지정됩니다. 복사 작업은 데이터를 가져오는 Azure SQL 데이터 웨어하우스 원본에 대해 이 쿼리를 실행합니다.
>  
> 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
>  
> sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열은 쿼리를 작성하는 데 사용되어 Azure SQL 데이터 웨어하우스에 대해 실행합니다.(mytable에서 column1, column2 선택) 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

## 샘플: Azure Blob에서 Azure SQL 데이터 웨어하우스에 데이터 복사

샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1.	[AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 및 [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.


샘플은 Azure blob에서 Azure SQL Data Warehouse의 테이블로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 데이터 웨어하우스 연결된 서비스:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure SQL 데이터 웨어하우스 출력 데이터 집합:**

샘플은 Azure SQL 데이터 웨어하우스의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure SQL Data Warehouse의 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**복사 작업을 포함하는 파이프라인**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **SqlDWSink**로 설정됩니다.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

자세한 설명은 Azure SQL Data Warehouse 설명서의 [Azure Data Factory를 사용하여 데이터 로드](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) 문서를 참조하세요.

## Azure SQL 데이터 웨어하우스 연결된 서비스 속성

다음 테이블은 Azure SQL 데이터 웨어하우스 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **AzureSqlDW**로 설정되어야 합니다. | 예
**connectionString** | connectionString 속성에 대한 Azure SQL 데이터 웨어하우스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예

> [AZURE.IMPORTANT] [Azure SQL Database 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)과 데이터베이스 서버를 구성하여 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 해야 합니다. 또한 데이터 팩터리 게이트웨이를 사용하여 온-프레미스 데이터 원본을 포함한 Azure 외부에서 Azure SQL 데이터 웨어하우스로 데이터를 복사하는 경우 데이터를 Azure SQL Data Warehouse로 보내는 컴퓨터에 대한 적절한 IP 주소 범위를 구성합니다.

## Azure SQL 데이터 웨어하우스 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AzureSqlDWTable** 형식의 데이터 집합에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | 연결된 서비스가 참조하는 Azure SQL 데이터 웨어하우스 데이터베이스에 있는 테이블의 이름입니다. | 예 |

## Azure SQL 데이터 웨어하우스 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

> [AZURE.NOTE]
복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

### SqlDWSource

원본이 **SqlDWSource** 형식인 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: select * from MyTable. | 아니요 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. | 저장 프로시저의 이름입니다. | 아니요 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다. | 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아니요 |

**sqlReaderQuery**가 SqlDWSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL 데이터 웨어하우스 원본에 대해 이 쿼리를 실행합니다.

또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열은 쿼리를 작성하는 데 사용되어 Azure SQL Data Warehouse에 대해 실행합니다. 예: `select column1, column2 from mytable`. 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

#### SqlDWSource 예제

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**저장 프로시저 정의:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink**는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 | 정수(행 수) | 아니요(기본값: 10000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. | timespan<br/><br/> 예: “00:30:00”(30분). | 아니요 | 
| sqlWriterCleanupScript | 특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. 자세한 내용은 [반복성 섹션](#repeatability-during-copy)을 참조하세요. | 쿼리 문입니다. | 아니요 |
| allowPolyBase | BULKINSERT 메커니즘 대신 (해당하는 경우)PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 로드할지 여부를 나타냅니다. <br/><br/>현재, **형식**을 가진 **Azure blob** 데이터 집합 만이 **TextFormat**에 원본 데이터 집합으로 설정됩니다. <br/><br/>제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요. | True <br/>False(기본값) | 아니요 |  
| polyBaseSettings | **allowPolybase** 속성이 **true**로 설정된 경우 지정될 수 있는 속성의 그룹입니다. | &nbsp; | 아니요 |  
| rejectValue | 쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다. <br/><br/>[외부 테이블 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) 토픽의 **인수** 섹션에 있는 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. | 0(기본값), 1, 2, … | 아니요 |  
| rejectType | rejectValue 옵션을 리터럴 값 또는 백분율로 지정할지 여부를 지정합니다. | 값(기본값), 백분율 | 아니요 |   
| rejectSampleValue | PolyBase가 거부된 행의 비율을 다시 계산하기 전에 검색할 행 수를 결정합니다. | 1, 2, … | 예. **rejectType**가 **백분율**인 경우 |  
| useTypeDefault | PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)의 인수 섹션에서 이 속성에 대해 자세히 알아봅니다. | True, False(기본값) | 아니요 | 


#### SqlDWSink 예제


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## PolyBase를 사용하여 Azure SQL 데이터 웨어하우스에 데이터 로드
**PolyBase**를 사용하는 것은 처리량이 높은 많은 양의 데이터를 Azure SQL 데이터 웨어하우스에 로드하는 효율적인 방법입니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하여 처리량의 증가를 확인할 수 있습니다.

Azure 데이터 팩터리에 대한 다음 예와 같이 **allowPolyBase** 속성을 **true**로 설정하여 Azure SQL 데이터 웨어하우스로 데이터를 복사하기 위해 PolyBase를 사용합니다. allowPolyBase를 true로 설정하면 **polyBaseSettings** 속성 그룹을 사용하여 PolyBase 특정 속성을 지정할 수 있습니다. polyBaseSettings에 사용할 수 있는 속성에 대한 세부 정보는 [SqlDWSink](#SqlDWSink) 섹션을 참조하세요.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10.0,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

### PolyBase를 사용하여 직접 복사
원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우 PolyBase를 사용하여 원본 데이터 저장소에서 Azure SQL Data Warehouse로 직접 복사할 수 있습니다. 그렇지 않을 경우, [PolyBase를 사용하여 준비한 복사본](#staged-copy-using-polybase)을 사용할 수 있습니다.

조건을 충족하지 않는 경우 Azure 데이터 팩터리는 설정을 확인한 후 데이터 이동을 위한 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1.	**원본에 연결된 서비스**는 **Azure Storage** 형식이며 SAS(공유 액세스 서명) 인증을 사용하도록 구성되지 않습니다. 자세한 내용은 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 참조하세요.
2. **입력 데이터 집합**은 **Azure Blob** 형식이고 형식 속성의 서식 형식은 다음 구성이 포함된 **OrcFormat** 또는 **TextFormat**입니다.
	1. **rowDelimiter**는 **\\n**이어야 합니다.
	2. **nullValue**는 **빈 문자열**("")로 설정됩니다.
	3. **encodingName**은 **utf-8**로 설정되며 이는 **기본** 값이므로 다른 값으로 설정하지 마세요.
	4. **escapeChar** 및 **quoteChar**는 지정되지 않습니다.
	5. **Compression**은 **BZIP2**가 아닙니다.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	**skipHeaderLineCount**는 파이프라인에서 복사 작업에 대한 **BlobSource**에 설정되지 않습니다.
4.	**sliceIdentifierColumnName**은 파이프라인에서 복사 작업에 대한 **SqlDWSink**에 설정되지 않습니다. (PolyBase는 한 번의 실행으로 모든 데이터를 업데이트하거나 아무 것도 업데이트하지 않도록 보장합니다. **반복성**을 달성하려면 **sqlWriterCleanupScript**를 사용할 수 있습니다.
5.	**columnMapping**은 연결된 복사 작업에서 사용되지 않습니다.

### PolyBase를 사용한 준비된 복사
원본 데이터가 이전 섹션에서 도입된 조건을 충족하지 않는 경우 일시적으로 스테이징한 Azure Blob Storage를 통해 데이터를 복사하도록 설정할 수 있습니다. 이 경우 Azure 데이터 팩터리는 PolyBase의 데이터 형식 요구 사항을 충족시키기 위해 데이터에 변환을 수행한 다음 데이터를 SQL Data Warehouse에 로드하기 위해 PolyBase를 사용합니다. 스테이징 Azure Blob을 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](data-factory-copy-activity-performance.md#staged-copy)를 참조하세요.

> [AZURE.IMPORTANT] PolyBase 및 스테이징을 사용하여 Azure SQL Data Warehouse로 온-프레미스 데이터 저장소의 데이터를 복사하는 경우 원본 데이터를 적절한 형식으로 변환하는 데 사용되는 게이트웨이 컴퓨터에 JRE 8(Java 런타임 환경)을 설치합니다. 64비트 게이트웨이에는 64비트 JRE가 필요하고 32비트 게이트웨이에는 32비트 JRE가 필요합니다. [Java 다운로드 위치](http://go.microsoft.com/fwlink/?LinkId=808605)에서 적절한 버전을 다운로드합니다.

이 기능을 사용하려면 중간 Blob Storage가 있는 Azure 저장소 계정을 나타내는 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 만든 후 다음 코드에 표시된 복사 작업에 대해 **enableStaging** 및 **stagingSettings** 속성을 지정합니다.

	"activities":[  
	{
		"name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDWOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlDwSink",
				"allowPolyBase": true
			},
    		"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob"
			}
		}
	}
	]


### PolyBase를 사용하는 경우 모범 사례

#### 행 크기 제한
Polybase는 32KB보다 큰 크기의 행을 지원하지 않습니다. 32KB보다 큰 행이 있는 테이블을 로드하려고 시도하면 다음과 같은 오류가 발생합니다.

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

32KB보다 큰 크기의 행을 포함한 원본 데이터가 있는 경우 각 항목의 최대 행 크기가 제한을 초과하지 않는 정도의 작은 테이블로 원본 테이블을 수직으로 분할하는 것이 좋습니다. 작은 테이블은 PolyBase를 사용하여 로드될 수 있고 Azure SQL 데이터 웨어하우스에서 병합될 수 있습니다.

#### Azure SQL 데이터 웨어하우스의 tableName
다음 테이블은 스키마와 테이블 이름의 다양한 조합에 대한 JSON 데이터 집합에서 **tableName** 속성을 지정하는 방법에 대한 예제를 제공합니다.

| DB 스키마 | 테이블 이름 | tableName JSON 속성 |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable 또는 [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] 또는 [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

다음 오류가 표시하는 경우 tableName 속성에 지정한 값에 관한 문제일 수 있습니다. tableName JSON 속성에 대한 값을 지정하는 올바른 방법은 테이블을 참조하세요.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### 기본값이 있는 열
현재 데이터 팩터리의 PolyBase 기능은 대상 테이블과 동일한 열 수를 허용합니다. 가령 네 개의 열이 있고 그 중 한 열이 기본 값으로 정의된 테이블이 있다고 합시다. 입력 데이터는 여전히 네 개의 열을 포함해야 합니다. 3열 입력 데이터 집합을 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

	All columns of the table must be specified in the INSERT BULK statement.

NULL 값은 특별한 형태의 기본값입니다. 열이 null을 허용하면 해당 열에 대한 입력 데이터(Blob)는 비어 있을 수 있습니다(입력 데이터 집합에서 누락될 수 없음). PolyBase는 Azure SQL Data Warehouse의 해당 항목에 NULL을 삽입합니다.


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure SQL 데이터 웨어하우스에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 대로 복사 작업은 다음 2단계 접근 방식을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Azure SQL, SQL Server 및 Sybase 간에 데이터를 이동할 때는SQL 형식과 .NET 형식 간의 다음과 같은 매핑이 사용됩니다.

매핑은 [ADO.NET에 대한 SQL Server 데이터 형식 매핑](https://msdn.microsoft.com/library/cc716729.aspx)과 같습니다.

| SQL Server 데이터베이스 엔진 형식 | .NET Framework 형식 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 10진수 | 10진수 |
| FILESTREAM 특성(varbinary(max)) | Byte |
| Float | Double |
| 이미지 | Byte | 
| int | Int32 | 
| money | 10진수 |
| nchar | String, Char |
| ntext | String, Char |
| numeric | 10진수 |
| nvarchar | String, Char |
| real | 단일 |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 10진수 | 
| sql\_variant | 개체 * |
| 텍스트 | String, Char |
| 실시간 | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->