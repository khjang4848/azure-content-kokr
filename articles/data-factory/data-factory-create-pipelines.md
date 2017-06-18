<properties 
	pageTitle="데이터 팩터리에서 파이프라인, 체인 작업 만들기/예약 | Microsoft Azure" 
	description="Azure Data Factory에서 데이터 파이프라인을 만들어 데이터를 이동하고 변환하는 방법을 배웁니다. 데이터 기반 워크플로를 만들어 정보를 사용하도록 준비합니다." 
    keywords="데이터 파이프라인, 데이터 기반 워크플로"
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

# Azure Data Factory의 파이프라인 및 작업: 파이프라인 및 체인 작업 만들기/예약
이 문서는 Azure Data Factory의 파이프라인 및 활동을 이해하고 시나리오를 위한 종단 간 데이터 기반 워크플로 사용하는 데 도움이 됩니다.

> [AZURE.NOTE] 이 문서는 [Azure Data Factory 소개](data-factory-introduction.md) 및 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 마쳤다고 간주합니다. 데이터 팩터리를 만드는 실습 경험이 없는 경우 [첫 번째 데이터 팩터리 빌드](data-factory-build-your-first-pipeline.md) 자습서를 진행하면 이 문서를 이해하는 데 도움이 됩니다.

## 데이터 파이프라인이란?
**파이프라인은 활동의 논리적 그룹화입니다**. 파이프라인은 여러 활동 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다. 파이프라인을 더 이해하려면 먼저 활동을 이해해야 합니다.

## 활동 정의
활동은 데이터에 수행할 작업을 정의합니다. 각 활동은 0개 이상의 [데이터 집합](data-factory-create-datasets.md)을 입력 항목으로 취하며 하나 이상의 데이터 집합을 출력물로 생성합니다. **활동은 Azure 데이터 팩터리의 오케스트레이션 단위입니다.**

예를 들어 하나의 데이터 집합에서 다른 데이터 집합으로의 데이터 복사를 오케스트레이션하기 위해 복사 활동을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 HDInsight Hive 활동을 사용할 수 있습니다. Azure 데이터 팩터리는 다양한 [데이터 변환, 분석](data-factory-data-transformation-activities.md) 및 [데이터 이동 활동](data-factory-data-movement-activities.md)을 제공합니다. 또한 사용자 고유의 코드를 실행하려면 사용자 지정 .NET 활동을 만들 수 있습니다.

다음 두 가지 데이터 집합을 고려합니다.

**Azure SQL 데이터 집합**

'MyTable' 테이블은 데이터가 데이터베이스에 삽입된 때의 datetime을 지정하는 데 도움이 되는 'timestampcolumn' 열을 포함합니다.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

**Azure Blob 데이터 집합**

데이터가 매시간 새 Blob으로 복사됩니다. Blob에 대한 경로에 시간 세분성으로 특정 날짜와 시간이 반영됩니다.

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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
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


다음 파이프라인의 복사 작업은 Azure SQL에서 Azure Blob Storage로 데이터를 복사합니다. Azure SQL 테이블을 시간별 빈도와 함께 입력 데이터 집합으로 인식하고 'AzureBlobOutput' 데이터 집합에서 표시되는 Azure Blob 저장소에 데이터를 작성합니다. 또한 출력 데이터 집합에는 시간별 빈도가 있습니다. [예약 및 실행 섹션](#scheduling-and-execution)을 참조하여 시간 단위를 통해 데이터를 복사하는 방법을 이해합니다. 이 파이프라인에는 “2015-01-01T08:00:00”에서 “2015-01-01T11:00:00”까지 3시간의 활성 기간이 있습니다.

**파이프라인:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

이제 활동 정의를 간략히 이해했으므로 파이프라인을 다시 살펴보겠습니다.
 
파이프라인은 활동의 논리적 그룹화입니다. 파이프라인은 여러 활동 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다. **또한 파이프라인은 작업에 대한 배포 및 관리 단위입니다.** 예를 들어 논리적으로 관련된 활동을 활성 또는 일시 중지된 상태에 있을 수 있는 하나의 파이프라인으로 함께 배치하려 할 수 있습니다.

파이프라인의 활동에서 출력 데이터 집합은 작업 간의 종속성을 정의하여 동일한/다른 파이프라인에서 다른 활동에 입력 데이터 집합일 수 있습니다. 자세한 내용은 [예약 및 실행](#chaining-activities)을 참조하세요.

Azure 데이터 팩터리에서 파이프라인을 만드는 경우 일반적인 단계입니다.

1.	(생성되지 않은 경우) 데이터 팩터리를 만듭니다.
2.	각 데이터 저장소 또는 계산에 대한 연결된 서비스를 만듭니다.
3.	입력 및 출력 데이터 집합을 만듭니다.
4.	데이터 집합에서 작동하는 활동으로 파이프라인을 만듭니다.

![데이터 팩터리 엔터티](./media/data-factory-create-pipelines/entities.png)

파이프라인을 정의하는 방법에 대해 자세히 살펴보겠습니다.

## 파이프라인의 분석  

파이프라인에 대한 일반 구조는 다음과 같이 보입니다.

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

**활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. 각 활동에는 다음과 같은 최상위 수준 구조가 있습니다.

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

다음 테이블은 활동 및 파이프라인 JSON 정의 내에서 속성을 설명합니다.

태그 | 설명 | 필수
--- | ----------- | --------
name | 작업 또는 파이프라인의 이름입니다. 활동 또는 파이프라인을 수행하도록 구성된 작업을 나타내는 이름을 지정합니다.<br/><ul><li>최대 문자 수: 260개</li><li>문자 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> | 예
description | 작업 또는 파이프라인이 무엇에 사용되는지 설명하는 텍스트입니다. | 예
type | 작업의 유형을 지정합니다. 다른 종류의 작업은 [데이터 이동 작업](data-factory-data-movement-activities.md) 및 [데이터 변환 작업](data-factory-data-transformation-activities.md) 문서를 참조하세요. | 예
inputs | 작업에서 사용하는 입력 테이블<br/><br/>// 하나의 입력 테이블<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>// 두 개의 입력 테이블 <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | 예
outputs | 작업에서 사용하는 출력 테이블.// 하나의 출력 테이블<br/>"outputs": [ { "name": “outputtable1” } ],<br/><br/>//두 개의 출력 테이블<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ], | 예
linkedServiceName | 작업에서 사용하는 연결된 서비스의 이름입니다. <br/><br/>작업은 필요한 계산 환경에 연결하는 연결된 서비스를 지정해야 할 수 있습니다. | HDInsight 작업 및 Azure 기계 학습 배치 평가 작업의 경우 예 <br/><br/>다른 모든 사용자의 경우 아니요
typeProperties | typeProperties 섹션의 속성은 작업의 종류에 따라 달라 집니다. | 아니요
policy | 작업의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본 정책이 사용됩니다. | 아니요
start | 파이프라인에 대한 시작 날짜-시간입니다. [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)에 있어야 합니다. 예: 2014-10-14T16:32:41Z. <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예: "2016-02-27T06:00:00**-05:00**"는 EST 시간 오전 6시입니다<br/><br/>start 및 end 속성은 함께 파이프라인의 활성 기간을 지정합니다. 출력 조각은 이 활성 기간에만 생성됩니다. | 아니요<br/><br/>end 속성에 대한 값을 지정하면 start 속성에 대한 값을 지정해야 합니다.<br/><br/>파이프라인을 만들 때에는 시작 및 종료 시간을 비워 둘 수 있습니다. 파이프라인을 실행할 활성 기간을 설정하려면 둘 다 값을 지정해야 합니다. 파이프라인을 만들 때 시작 시간과 종료 시간을 지정하지 않으면 나중에 Set-AzureRmDataFactoryPipelineActivePeriod cmdlet를 사용하여 설정할 수 있습니다.
end | 파이프라인에 대한 종료 날짜-시간입니다. 지정된 경우 ISO 형식에 있어야 합니다. 예: 2014-10-14T17:32:41Z <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예: "2016-02-27T06:00:00**-05:00**"는 EST 시간 오전 6시입니다<br/><br/>파이프라인을 무기한 실행하려면 end 속성 값으로 9999-09-09를 지정합니다. | 아니요 <br/><br/>start 속성에 대한 값을 지정하는 경우 반드시 end 속성에 대한 값도 지정해야 합니다.<br/><br/>**start** 속성에 대한 참조를 확인합니다.
isPaused | 파이프라인을 true로 설정하면 실행되지 않습니다. 기본값 = false입니다. 이 속성을 사용하여 활성화 또는 비활성화할 수 있습니다. | 아니요 
scheduler | "scheduler" 속성은 작업에 원하는 일정을 정의하는 데 사용됩니다. 하위 속성은 [데이터 집합에서 가용성 속성](data-factory-create-datasets.md#Availability)에 있는 속성과 같습니다. | 아니요 |   
| pipelineMode | 파이프라인에 대한 실행을 예약하는 메서드입니다. 허용되는 값은 scheduled(기본), onetime입니다.<br/><br/>‘Scheduled’는 파이프라인이 활성 기간(시작 및 종료 시간)에 따라 지정된 시간 간격으로 실행된다는 것을 나타냅니다. ‘Onetime’은 파이프라인이 한 번만 실행된다는 것을 나타냅니다. 현재는, Onetime 파이프라인이 생성된 후에 수정/업데이트가 불가능합니다. 일회성 설정에 대한 세부 정보는 [일회성 파이프라인](data-factory-scheduling-and-execution.md#onetime-pipeline)을 참조하세요. | 아니요 | 
| expirationTime | 생성 후에 파이프라인이 유효하고 프로비전된 상태로 유지해야 하는 시간입니다. 활성 작업, 실패한 작업 또는 보류 중인 작업이 없는 경우 만료 시간이 되면 파이프라인은 자동으로 삭제됩니다. | 아니요 | 
| datasets | 파이프라인에 정의된 작업에서 사용할 데이터 집합 목록입니다. 이 속성은 데이터 팩터리 내에 정의되지 않은 파이프라인에만 해당되는 데이터 집합을 정의하는 데 사용될 수 있습니다. 이 파이프라인 내에 정의된 데이터 집합은 이 파이프라인에서만 사용될 수 있고 공유될 수 없습니다. 자세한 내용은 [범위가 지정된 데이터 집합](data-factory-create-datasets.md#scoped-datasets)을 참조하세요.| 아니요 |  
 

## 데이터 이동 및 데이터 변환에 대한 작업 유형
Azure 데이터 팩터리는 다양한 [데이터 이동](data-factory-data-movement-activities.md) 및 [데이터 변환](data-factory-data-transformation-activities.md) 활동을 제공합니다.

### 정책
정책은 특히 테이블의 조각을 처리할 때 활동의 런타임 동작에 영향을 줍니다. 다음 테이블에서는 자세한 내용을 제공합니다.

속성 | 허용된 값 | 기본값 | 설명
-------- | ----------- | -------------- | ---------------
동시성 | 정수 <br/><br/>최대값: 10 | 1 | 작업의 동시 실행 수입니다.<br/><br/>다른 조각에 발생할 수 있는 병렬 작업 실행 횟수를 결정합니다. 예를 들어 활동이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 동시성 값을 높이면 데이터 처리가 빨라집니다. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | 처리 중인 데이터 조각의 순서를 결정합니다.<br/><br/>예를 들어 2개의 조각이 있으며(각각 오후 4시 및 오후 5시에 발생) 둘 다 실행 보류 상태입니다. executionPriorityOrder를 설정하여 NewestFirst가 되도록 하면 오후 5시에 조각이 먼저 처리됩니다. 마찬가지로 executionPriorityORder를 OldestFIrst로 설정하면 오후 4시의 조각이 처리됩니다. 
retry | 정수<br/><br/>최대값이 10이 될 수 있음 | 3 | 조각에 대한 데이터 처리 전에 다시 시도 횟수가 실패로 표시됩니다. 데이터 조각에 대한 활동 실행은 지정된 재시도 횟수까지 다시 시도됩니다. 재시도는 실패 후 가능한 한 빨리 수행합니다.
시간 제한 | TimeSpan | 00:00:00 | 활동에 대한 시간 제한입니다. 예: 00:10:00(시간 제한 10분을 의미함)<br/><br/>값을 지정하지 않거나 0으로 지정하는 경우 시간 제한이 없습니다.<br/><br/>조각의 데이터 처리 시간이 시간 제한 값을 초과하면 취소되고 시스템이 처리를 다시 시도합니다. 다시 시도 횟수는 다시 시도 속성에 따라 달라집니다. 시간 제한이 발생할 때 상태는 TimedOut으로 설정됩니다.
delay | TimeSpan | 00:00:00 | 조각의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다.<br/><br/>예상된 실행 시간 이후까지 지연된 후에 데이터 조각에 대한 작업이 실행되기 시작됩니다.<br/><br/>예: 00:10:00(10분의 지연을 의미함)
longRetry | 정수<br/><br/>최대값: 10 | 1 | 조각 실행이 실패하기 전까지의 긴 재시도 횟수입니다.<br/><br/>longRetry 시도는 longRetryInterval에 따라 간격이 조정됩니다. 따라서 재시도 간의 시간을 지정해야 하는 경우 longRetry를 사용합니다. Retry 및 longRetry를 둘 다 지정하면 각 longRetry 시도에는 Retry 시도가 포함되고 최대 시도 횟수는 Retry * longRetry가 됩니다.<br/><br/>예를 들어 작업 정책에 다음 항목이 있는 경우:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>실행할 조각이 하나뿐이며(Waiting 상태) 작업 실행이 매번 실패한다고 가정합니다. 우선 3번 연속 실행 시도를 합니다. 시도한 후 각 조각 상태는 다시 시도입니다. 처음 3번 시도 후에 조각 상태는 LongRetry입니다.<br/><br/>한 시간(즉, longRetryInteval의 값) 후에 3번 연속 실행이 다시 시도됩니다. 그 후에 조각 상태가 실패이면 다시 시도는 더 이상 시도하지 않습니다. 즉, 전체적으로 6번의 시도가 일어납니다.<br/><br/>모든 실행이 성공하면 조각 상태가 Ready가 되고 더 이상 다시 시도되지 않습니다.<br/><br/>longRetry는 종속 데이터가 명확하지 않은 시간에 도착하거나 데이터 처리가 발생하는 전체적인 환경을 신뢰할 수 없는 상황에서 사용될 수 있습니다. 이러한 경우 하나씩 다시 시도를 수행해도 도움이 되지 않을 수 있으며 특정 시간 간격 후에 시도할 경우 출력이 나타납니다.<br/><br/>주의: longRetry 또는 longRetryInterval에 높은 값을 설정하지 마세요. 일반적으로 더 높은 값을 지정하면 다른 시스템 문제가 발생합니다. 
longRetryInterval | TimeSpan | 00:00:00 | 긴 다시 시도 간의 지연 

## 활동 연결
파이프라인에 여러 활동이 있고 활동의 출력이 다른 활동의 입력이 아닌 경우 해당 활동에 대한 입력 데이터 조각이 준비되면 활동을 병렬로 실행할 수 있습니다.

한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 지정하여 두 활동을 연결할 수 있습니다. 활동은 동일한 파이프라인 또는 다른 파이프라인에 있을 수 있습니다. 두 번째 활동은 첫 번째 활동이 완료된 경우에만 실행됩니다.

예를 들어 다음과 같은 경우를 고려해 보겠습니다.
 
1.	파이프라인 P1에는 외부 입력 데이터 집합 D1이 필요하고 **출력** 데이터 집합 **D2**를 생성하는 작업 A1이 있습니다.
2.	파이프라인 P2에는 데이터 집합 **D2**의 **입력**이 필요하고 출력 데이터 집합 D3을 생성하는 작업 A2가 있습니다.
 
이 시나리오에서 활동 A1은 외부 데이터를 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 활동 A2는 D2에서 예약된 조각을 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 데이터 집합 D2의 조각 중 하나에 오류가 있으면 해당 조각을 사용할 수 있을 때까지 A2가 실행되지 않습니다.

다이어그램 뷰:

![두 개의 파이프라인에서 활동 연결](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

동일한 파이프라인에서 두 활동이 있는 다이어그램 뷰:

![동일한 파이프라인에서 활동 연결](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## 예약 및 실행
지금까지 파이프라인 및 활동 정의를 이해했습니다. 또한 Azure Data Factory에서 정의된 방식 및 활동의 상위 수준을 살펴봅니다. 이제 어떻게 실행되는지를 대해 살펴보겠습니다.

파이프라인은 시작 시간과 종료 시간 사이에서만 활성화됩니다. 시작 시간 이전 또는 종료 시간 이후에 실행되지 않습니다. 파이프라인이 일시 중지된 경우 시작 및 종료 시간에 관계없이 실행되지 않습니다. 실행될 파이프라인의 경우 일시 중지되지 않아야 합니다. 실제로 실행되는 파이프라인이 아닙니다. 실행되는 파이프라인의 활동입니다. 그러나 파이프라인의 전체적인 맥락에서 이를 수행합니다.

Azure 데이터 팩터리에서 예약 및 실행의 작동 방식을 이해하려면 [예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.

### 조각의 병렬 처리
런타임 시 작업의 여러 인스턴스에서 여러 조각을 병렬로 처리하도록 작업 JSON 정의에서 **concurrency**를 1보다 큰 값으로 설정합니다. 이 기능은 과거부터 채워진 조각을 처리할 때 유용합니다.


## 파이프라인 제작 및 관리
Azure 데이터 팩터리는 파이프라인을 작성하고 배포하는 다양한 메커니즘을 제공합니다.(차례로 하나 이상의 활동을 포함)

### Azure 포털 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 파이프라인을 만들려는 Azure 데이터 팩터리 인스턴스로 이동합니다.
3. **요약** 렌즈의 **작성자 및 배포** 타일을 클릭합니다.
 
	![작성 및 배포 타일](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. 명령 모음에서 **새 파이프라인**을 클릭합니다.

	![새 파이프라인 단추](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. 파이프라인 JSON 템플릿을 사용하여 편집기 창을 표시합니다.

	![파이프라인 편집기](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. 파이프라인을 작성을 마친 후에 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.

	> [AZURE.NOTE] 배포하는 동안 Azure Data Factory 서비스는 유효성 검사를 수행하여 몇 가지 일반적인 문제를 해결하는 데 도움을 줍니다. 오류가 있는 경우 해당 정보가 표시됩니다. 정정 작업을 수행하고 작성된 파이프라인을 다시 배포합니다. 파이프라인을 업데이트 및 삭제하려면 편집기를 사용할 수 있습니다.

파이프라인으로 데이터 팩터리 만들기에 대한 전체 연습은 [Azure Data Factory 시작(데이터 팩터리 편집기)](data-factory-build-your-first-pipeline-using-editor.md)을 참조하세요.

### Visual Studio 플러그인 사용
Azure 데이터 팩터리에 파이프라인을 작성하고 배포하려면 Visual Studio를 사용할 수 있습니다. 파이프라인으로 데이터 팩터리 만들기에 대한 전체 연습은 [Azure Data Factory 시작(Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md)을 참조하세요.


### Azure PowerShell 사용
Azure 데이터 공장에서 파이프라인을 만들려면 Azure PowerShell을 사용할 수 있습니다. 다시 말하면 c:\\DPWikisample.json에 있는 파일에 파이프라인 JSON를 정의했습니다. 다음 예제와 같이 Azure 데이터 팩터리 인스턴스로 업로드할 수 있습니다.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

파이프라인으로 데이터 팩터리 만들기에 대한 전체 연습은 [Azure Data Factory 시작(Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md)을 참조하세요.

### .NET SDK 사용
.NET SDK를 통해 파이프라인을 만들고 배포할 수 있습니다. 프로그래밍 방식으로 파이프라인을 만들려면 이 메커니즘을 사용할 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 데이터 팩터리 만들기, 관리 및 모니터링](data-factory-create-data-factories-programmatically.md)을 참조하세요.


### Azure Resource Manager 템플릿 사용
Azure Resource Manager 템플릿을 사용하여 파이프라인을 만들고 배포할 수 있습니다. 이에 대한 자세한 내용은 [Azure Data Factory 시작(Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md)을 참조하세요.

### REST API 사용
REST API를 사용하여 파이프라인을 만들고 배포할 수 있습니다. 프로그래밍 방식으로 파이프라인을 만들려면 이 메커니즘을 사용할 수 있습니다. [파이프라인 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/dn906741.aspx)를 참조하세요.


## 관리 및 모니터링  
파이프라인을 배포한 후에 파이프라인, 조각 및 실행 프로그램을 관리하고 모니터링할 수 있습니다. 이에 대한 자세한 내용은 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.

## 다음 단계

- [Azure 데이터 팩터리에서 예약 및 실행](data-factory-scheduling-and-execution.md)을 이해합니다.
- Azure 데이터 팩터리에서 [데이터 이동](data-factory-data-movement-activities.md) 및 [데이터 변환 기능](data-factory-data-transformation-activities.md)에 대해 읽어봅니다.
- [Azure 데이터 팩터리에서 관리 및 모니터링](data-factory-monitor-manage-pipelines.md)을 이해합니다.
- [첫 번째 파이프라인을 빌드 및 배포](data-factory-build-your-first-pipeline.md)합니다.

<!---HONumber=AcomDC_0914_2016-->