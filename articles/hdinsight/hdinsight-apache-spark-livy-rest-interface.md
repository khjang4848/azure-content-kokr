<properties
	pageTitle="Livy를 사용하여 원격으로 Spark 작업 제출 | Microsoft Azure"
	description="HDInsight 클러스터와 함께 Livy를 사용하여 Spark 작업을 원격으로 제출하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# Livy를 사용하여 HDInsight Linux의 Apache Spark 클러스터에 원격으로 Spark 작업 제출

Azure HDInsight의 Apache Spark 클러스터는 Spark 클러스터에 원격으로 작업을 제출하기 위한 Livy, REST 인터페이스를 포함합니다. 자세한 설명서는 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)를 참조하세요.

Livy를 사용하여 대화형 Spark 셸을 실행하거나 Spark에서 실행되도록 배치 작업을 제출할 수 있습니다. 이 문서는 Livy를 사용하여 배치 작업을 제출하는 방법에 대해 설명합니다. 아래 구문은 Curl을 사용하여 Livy 끝점에 대한 REST 호출을 만듭니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

## 배치 작업 제출

배치 작업을 제출하기 전에 클러스터와 연결된 클러스터 저장소에 응용 프로그램 jar을 업로드해야 합니다. 이렇게 하기 위해 명령줄 유틸리티 [**AzCopy**](../storage/storage-use-azcopy.md)를 사용할 수 있습니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.

	curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**예제**:

* jar 파일이 클러스터 저장소(WASB)에 있는 경우

		curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 입력 파일의 일부분으로 jar 파일 이름 및 클래스 이름을 전달하려는 경우(이 예제에서는 input.txt)

		curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## 클러스터에서 실행 중인 배치에 대한 정보 가져오기

	curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**예제**:

* 클러스터에서 실행 중인 모든 배치를 검색하려는 경우:

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 지정된 batchId로 특정 배치를 검색하려는 경우

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## 배치 작업 삭제

	curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**예제**:

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## Livy 및 고가용성

Livy는 클러스터에서 실행 중인 Spark 작업에 대해 고가용성을 제공합니다. 다음은 몇 가지 예입니다.

* Spark 클러스터에 원격으로 작업을 제출한 후에 Livy 서비스가 다운되면 작업은 백그라운드에서 계속 실행됩니다. Livy는 백업된 후 작업의 상태를 복원하고 다시 보고합니다.

* HDInsight용 Jupyter 노트북은 백 엔드에서 Livy를 통해 구동됩니다. 노트북에서 Spark 작업이 실행되고 있으며 Livy 서비스가 다시 시작되면 해당 노트북은 코드 셀을 계속 실행합니다.

## 예제 보기

이 섹션에서는 Livy를 사용하여 Spark 응용 프로그램을 제출하고 응용 프로그램의 진행 상황을 모니터링한 다음 작업을 삭제하는 방법에 대한 예제를 살펴봅니다. 이 예제에서 사용하는 응용 프로그램은 문서 [독립 실행형 Scala 응용 프로그램을 만들고 HDInsight Spark 클러스터에서 실행하기](hdinsight-apache-spark-create-standalone-application.md)에서 개발된 것입니다. 아래 단계는 다음을 가정합니다.

* 응용 프로그램 jar를 클러스터와 연결된 저장소 계정에 이미 복사했습니다.
* 이 단계를 시도하려는 컴퓨터에 CuRL을 설치했습니다.

다음 단계를 수행합니다.

1. 먼저 Livy가 클러스터에서 실행 중인지 확인하도록 합니다. 실행 중인 배치 목록을 가져와서 확인할 수 있습니다. Livy를 사용하여 처음으로 작업을 실행하는 경우 0을 반환해야 합니다.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

	다음과 유사한 결과가 표시됩니다.

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:47:53 GMT
		< Content-Length: 34
		<
		{"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	출력의 마지막 줄이 실행 중인 배치가 없는 것을 나타내는 **total:0**을 말하는 방법을 확인합니다.

2. 이제 배치 작업을 제출하도록 합니다. 아래 코드 조각은 입력 파일(input.txt)을 사용하여 매개 변수로 jar 이름 및 클래스 이름을 전달합니다. Windows 컴퓨터에서 이 단계를 실행하는 경우 권장되는 방법입니다.

		curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

	파일 **input.txt**에서 매개 변수는 다음과 같이 정의됩니다.

		{ "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

	다음과 유사한 결과가 표시됩니다.

		< HTTP/1.1 201 Created
		< Content-Type: application/json; charset=UTF-8
		< Location: /0
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:51:30 GMT
		< Content-Length: 36
		<
		{"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	출력의 마지막 줄이 **state:starting**을 말하는 방법을 확인합니다. 또한 **id:0**을 말합니다. 배치 ID입니다.

3. 이제 배치 ID를 사용하여 이 특정 배치의 상태를 검색할 수 있습니다.

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	다음과 유사한 결과가 표시됩니다.

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:54:42 GMT
		< Content-Length: 509
		<
		{"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	이제 출력은 작업이 성공적으로 완료됐음을 나타내는 **state:success**를 보여 줍니다.

4. 원하는 경우 이제 배치를 삭제할 수 있습니다.

		curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	다음과 유사한 결과가 표시됩니다.

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Sat, 21 Nov 2015 18:51:54 GMT
		< Content-Length: 17
		<
		{"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	출력의 마지막 줄은 배치가 성공적으로 삭제된 것을 보여 줍니다. 실행되는 동안 작업을 삭제하는 경우 기본적으로 작업이 중단됩니다. 성공적으로 완료됐거나 그렇지 않은 작업을 삭제하는 경우 작업 정보를 완전히 삭제합니다.

## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)

### 도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->