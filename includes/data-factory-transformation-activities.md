Azure Data Factory는 개별적 또는 다른 작업과 연계하여 파이프라인에 추가할 수 있는 다음과 같은 변환 작업을 지원합니다.

데이터 변환 작업 | 컴퓨팅 환경 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] [Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop] [MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop] [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] [Machine Learning 작업: Batch 실행 및 업데이트 리소스](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[저장 프로시저](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL 데이터 웨어하우스 또는 SQL Server |
[데이터 레이크 분석 U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Azure 데이터 레이크 분석 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] 또는 Azure Batch
   
> [AZURE.NOTE] 
MapReduce 작업을 사용하여 HDInsight Spark 클러스터에서 Spark 프로그램을 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](../articles/data-factory/data-factory-spark.md)을 참조하세요. R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory를 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->