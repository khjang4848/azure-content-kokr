<properties 
	pageTitle="PostgreSQL에서 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure 데이터 팩터리를 사용하여 PostgreSQL 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다." 
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

# Azure 데이터 팩터리를 사용하여 PostgreSQL에서 데이터 이동

이 문서에서는 Azure 데이터 팩토리에서 복사 작업을 사용하여 PostgreSQL에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

데이터 팩터리 서비스는 데이터 관리 게이트웨이를 사용하여 온-프레미스 PostgreSQL 원본에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

> [AZURE.NOTE]
게이트웨이는 PostgreSQL 데이터베이스가 Azure IaaS VM에 호스팅되더라도 필요합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한 데이터 저장소와 동일한 IaaS VM 또는 다른 VM에 게이트웨이를 설치할 수 있습니다.

데이터 팩터리는 다른 데이터 저장소에서 PostgreSQL로가 아닌 PostgreSQL에서 다른 데이터 저장소로 데이터 이동만을 지원합니다.

## 설치 

PostgreSQL 데이터베이스에 연결할 데이터 관리 게이트웨이의 경우 데이터 관리 게이트웨이와 동일한 시스템에 [Ngpsql data provider for PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716)을 설치해야 합니다.

> [AZURE.NOTE] 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.

## 데이터 복사 마법사
PostgreSQL 데이터베이스의 데이터를 지원되는 싱크 데이터 저장소 중 하나에 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 PostgreSQL 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores) 에 설명한 싱크로 데이터를 복사할 수 있습니다.

## 샘플: PostgreSQL에서 Azure Blob로 데이터 복사

이 샘플은 PostgreSQL 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.
 
이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1.	[OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.	[RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 PostgreSQL 데이터베이스의 쿼리 결과에서 blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**PostgreSQL 연결된 서비스:**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Azure Blob 저장소 연결된 서비스:**
		
	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**PostgreSQL 입력 데이터 집합:**

샘플은 PostgreSQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestamp"라는 열이 포함되어 있다고 가정합니다.

"external": "true"로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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


**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**복사 작업:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 PostgreSQL 데이터베이스의 public.usstates 테이블에서 데이터를 선택합니다.
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "public"."usstates""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## PostgreSQL 연결된 서비스 속성

다음 표에서는 PostgreSQL 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **OnPremisesPostgreSql**로 설정되어야 합니다. | 예
server | PostgreSQL 서버의 이름입니다. | 예 
database | PostgreSQL 데이터베이스의 이름입니다. | 예 
schema | 데이터베이스에서 스키마의 이름입니다. schema 이름은 대/소문자를 구분합니다. | 아니요 
authenticationType | PostgreSQL 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. | 예 
username | 기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. | 아니요 
password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. | 아니요 
gatewayName | 데이터 팩터리 서비스가 온-프레미스 PostgreSQL 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. | 예 

온-프레미스 PostgreSQL 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)을 참조하세요.

## PostgreSQL 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식의 데이터 집합(PostgreSQL 데이터 집합을 포함)에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
tableName | 연결된 서비스가 참조하는 PostgreSQL 데이터베이스 인스턴스에서 테이블의 이름입니다. tableName은 대/소문자를 구분합니다. | 아니요(**RelationalSource**의 **쿼리**가 지정된 경우) 

## PostgreSQL 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **RelationalSource**(PostgreSQL 포함) 형식인 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

속성 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: "query": "select * from "MySchema"."MyTable"" | 아니요(**데이터 집합**의 **tableName**이 지정된 경우)

> [AZURE.NOTE] 스키마와 테이블 이름은 대/소문자를 구분하며 쿼리에서 ""(큰따옴표)로 묶어야 합니다.

**예제:**

 "query": "select * from "MySchema"."MyTable""

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## PostgreSQL에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
1. .NET 형식에서 네이티브 싱크 형식으로 변환

PostgreSQL로 데이터를 이동하는 경우 PostgreSQL 형식에서 .NET 형식으로 이동에 다음 매핑이 사용됩니다.

PostgreSQL 데이터베이스 형식 |	PostgresSQL 별칭 | .NET Framework 형식
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit [ (n) ] | | Byte, String
bit varying [ (n) ] | varbit | Byte, String
부울 | bool | Boolean
box | | Byte, String
bytea | | Byte, String
character [ (n) ] | char [ (n) ] | 문자열
character varying [ (n) ] | varchar [ (n) ] | 문자열
cid | | 문자열
cidr | | String
circle | | Byte, String
date | | Datetime
daterange | | 문자열
double precision| float8 | Double
inet | | Byte, String
intarry | | 문자열
int4range | | 문자열
int8range | | 문자열
정수 | int, int4 | Int32
interval [ fields ] [ (p) ] | | Timespan
json : | | String
jsonb | | Byte
line | | Byte, String
lseg | | Byte, String
macaddr | | Byte, String
money | | 10진수
numeric [ (p, s) ] | decimal [ (p, s) ] | 10진수
numrange | | 문자열
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte, String
polygon | | Byte, String
real | float4 | Single
smallint | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
텍스트 | | 문자열


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->