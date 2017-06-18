<properties 
	pageTitle="Hadoop 스트리밍 작업" 
	description="Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행하는 방법에 대해 알아봅니다." 
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

# Hadoop 스트리밍 작업
HDInsightStreamingActivity 작업을 사용하여 Azure Data Factory 파이프라인에서 Hadoop 스트리밍 작업을 호출할 수 있습니다. 다음 JSON 조각은 파이프라인 JSON 파일에서 HDInsightStreamingActivity를 사용하기 위한 구문을 보여 줍니다.

Data Factory [파이프라인](data-factory-create-pipelines.md)의 HDInsight 스트리밍 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

## JSON 샘플
HDInsight 클러스터는 예제 프로그램(wc.exe 및 cat.exe) 및 데이터(davinci.txt)와 함께 자동으로 채워집니다. 기본적으로 HDInsight 클러스터에 사용되는 컨테이너의 이름은 클러스터 자체의 이름입니다. 예를 들어, 클러스터 이름이 myhdicluster이면 연결된 BLOB 컨테이너의 이름은 myhdicluster가 됩니다.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

다음 사항에 유의하세요.

1. **linkedServiceName**을 스트리밍 mapreduce 작업을 실행할 수 있는 HDInsight 클러스터를 가리키는 연결된 서비스의 이름으로 설정합니다.
2. activity의 type을 **HDInsightStreaming**으로 설정합니다.
3. **mapper** 속성에는 매퍼 실행 파일의 이름을 지정합니다. 예제에서는 cat.exe가 매퍼 실행 파일입니다.
4. **reducer** 속성에는 리듀서 실행 파일의 이름을 지정합니다. 예제에서는 wc.exe가 리듀서 실행 파일입니다.
5. **input** 유형 속성에는 매퍼의 입력 파일(위치 포함)을 지정합니다. 예제인 "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"에서 adfsample은 blob 컨테이너이고 example/data/Gutenberg는 폴더이며 davinci.txt는 blob입니다.
6. **output** 유형 속성에는 리듀서의 출력 파일(위치 포함)을 지정합니다. Hadoop 스트리밍 작업의 출력이 이 속성에 지정된 위치에 기록됩니다.
7. **filePaths** 섹션에서 매퍼 및 리듀서 실행 파일의 경로를 지정합니다. "adfsample/example/apps/wc.exe" 예에서 adfsample은 Blob 컨테이너, example/apps는 폴더, wc.exe는 실행 파일입니다.
8. **fileLinkedService** 속성에는 filePaths 섹션에 지정된 파일이 포함된 Azure 저장소를 나타내는 Azure 저장소 연결된 서비스를 지정합니다.
9. **arguments** 속성에는 스트리밍 작업의 인수를 지정합니다.
10. **getDebugInfo** 속성은 선택적 요소입니다. Failure로 설정되면 실패한 경우에만 로그가 다운로드됩니다. All로 설정되면 실행 상태에 관계 없이 로그가 항상 다운로드됩니다.

> [AZURE.NOTE] 예제에서 볼 수 있듯이 **output** 속성에 대해 Hadoop 스트리밍 작업에 대한 출력 데이터 집합을 지정합니다. 이 데이터 집합은 파이프라인 일정을 진행하는데 필요한 더미 데이터 집합입니다. **input** 속성에 대한 작업에 입력 데이터 집합을 지정할 필요가 없습니다.

	
## 예
이번 연습의 파이프라인에서는 Azure HDInsight 클러스터에서 Word Count 스트리밍 Map/Reduce 프로그램을 실행합니다.

### 연결된 서비스

#### Azure 저장소 연결된 서비스
우선 Azure HDInsight 클러스터에서 사용되는 Azure 저장소를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 생성합니다. 다음 코드를 복사하여 붙여넣는 경우, 잊지 말고 계정 이름과 계정 키를 사용자의 Azure 저장소의 이름과 키로 바꿉니다.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Azure HDInsight 연결된 서비스
다음으로, Azure HDInsight 클러스터를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 만듭니다. 다음 코드를 복사하여 붙여넣는 경우, HDInsight 클러스터 이름을 사용자의 HDInsight 클러스터 이름으로 바꾸고 사용자 이름과 암호 값을 변경합니다.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### 데이터 집합

#### 출력 데이터 집합
이 예제의 파이프라인은 input을 포함하지 않습니다. HDInsight 스트리밍 작업에 대한 출력 데이터 집합을 지정합니다. 이 데이터 집합은 파이프라인 일정을 진행하는데 필요한 더미 데이터 집합입니다.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### 파이프라인

이 예제의 파이프라인은 **HDInsightStreaming** 형식의 작업을 하나만 포함합니다.

HDInsight 클러스터는 예제 프로그램(wc.exe 및 cat.exe) 및 데이터(davinci.txt)와 함께 자동으로 채워집니다. 기본적으로 HDInsight 클러스터에 사용되는 컨테이너의 이름은 클러스터 자체의 이름입니다. 예를 들어, 클러스터 이름이 myhdicluster이면 연결된 BLOB 컨테이너의 이름은 myhdicluster가 됩니다.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

## 참고 항목
- [Hive 작업](data-factory-hive-activity.md)
- [Pig 작업](data-factory-pig-activity.md)
- [MapReduce 작업](data-factory-map-reduce.md)
- [Spark 프로그램 호출](data-factory-spark.md)
- [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0921_2016-->