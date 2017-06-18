<properties 
	pageTitle="리소스 관리자를 사용하여 HDInsight에서 Apache Spark 클러스터에 리소스 할당 | Microsoft Azure" 
	description="성능 향상을 위해 HDInsight에서 Spark 클러스터에 리소스 관리자를 사용하는 방법을 알아봅니다." 
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
	ms.date="08/25/2016" 
	ms.author="nitinme"/>


# HDInsight Linux에서 Apache Spark 클러스터에 대한 리소스 관리

이 문서에서 Spark 클러스터와 연결된 Ambari UI, YARN UI, Spark 기록 서버와 같은 인터페이스에 액세스하는 방법을 알아봅니다. 최적의 성능을 위해 클러스터 구성을 조정하는 방법에 대해 알아봅니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

## Ambari 웹 UI를 시작하는 방법

1. [Azure 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.
 
2. Spark 클러스터 블레이드에서 **대시보드**를 클릭합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

	![Ambari 시작](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "리소스 관리자 시작")

3. 이렇게 하면 아래와 같이 Ambari 웹 UI가 시작됩니다.

	![Ambari 웹 UI](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari 웹 UI")

## Spark 기록 서버를 시작하는 방법

1. [Azure 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우).

2. 클러스터 블레이드의 **빠른 링크**에서 **클러스터 대시보드**를 클릭합니다. **클러스터 대시보드** 블레이드에서 **Spark 기록 서버**를 클릭합니다.

	![Spark 기록 서버](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark 기록 서버")

	메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.


## Yarn UI를 시작하는 방법

YARN UI를 사용하여 현재 Spark 클러스터에서 실행 중인 응용 프로그램을 모니터링할 수 있습니다.

1. 클러스터 블레이드에서 **클러스터 대시보드**를 클릭하고 **YARN**을 클릭합니다.

	![YARN UI 시작](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png)

	>[AZURE.TIP] 또는 Ambari UI에서 YARN UI를 시작할 수도 있습니다. Ambari UI를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**를 클릭한 다음 **HDInsight 클러스터 대시보드**를 클릭합니다. Ambari UI에서 **YARN**, **빠른 링크**, 활성 리소스 관리자를 차례로 클릭한 다음 **ResourceManager UI**를 클릭합니다.

## Spark 응용 프로그램을 실행하는 데 최적화된 클러스터 구성이란?

응용 프로그램 요구 사항에 따라 Spark 구성에 사용할 수 있는 세 가지 주요 매개 변수는 `spark.executor.instances`, `spark.executor.cores` 및 `spark.executor.memory`입니다. 실행자는 Spark 응용 프로그램을 위해 시작된 프로세스입니다. 작업자 노드에서 실행되며 응용 프로그램에 대한 작업을 수행해야 합니다. 각 클러스터에 대한 실행자 및 실행자 크기의 기본 수는 작업자 노드 및 작업자 노드 크기의 수에 기반하여 계산됩니다. 클러스터 헤드 노드의 `spark-defaults.conf`에 저장됩니다.

세 가지 구성 매개 변수는 (클러스터에서 실행된는 모든 응용 프로그램의 경우) 클러스터 수준에서 구성될 수 있거나 각 개별 응용 프로그램에 대해 지정될 수 있습니다.

### Ambari UI를 사용하여 매개 변수 변경

1. Ambari UI에서 **Spark**, **Configs**를 차례로 클릭한 다음 **사용자 지정 spark 기본값**을 확장합니다.

	![Ambari를 사용하여 매개 변수 설정](./media/hdinsight-apache-spark-resource-manager/set-parameters-using-ambari.png)

2. 기본값으로 Spark 클러스터에서 4개의 응용 프로그램을 동시에 실행할 수 있습니다. 아래와 같이 사용자 인터페이스에서 이러한 값을 변경할 수 있습니다.

	![Ambari를 사용하여 매개 변수 설정](./media/hdinsight-apache-spark-resource-manager/set-executor-parameters.png)

3. **저장**을 클릭하여 구성 변경을 저장합니다. 페이지 맨 위에 모든 영향을 받는 서비스를 다시 시작하라는 메시지가 표시됩니다. **다시 시작**을 클릭합니다.

	![서비스 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-services.png)


### Jupyter notebook에서 실행 중인 응용 프로그램에 대한 매개 변수 변경

Jupyter notebook에서 실행 중인 응용 프로그램의 경우 `%%configure` magic을 사용하여 구성을 변경할 수 있습니다. 이상적으로 첫 번째 코드 셀을 실행하기 전에 응용 프로그램의 시작 부분에 이를 변경해야 합니다. 이렇게 하면 구성이 생성된 경우 Livy 세션에 적용됩니다. 응용 프로그램의 이후 단계에서 구성을 변경하려는 경우 `-f` 매개 변수를 사용해야 합니다. 그러나 이렇게 하면 응용 프로그램의 모든 진행 상태는 손실됩니다.

다음 코드 조각에서는 Jupyter에서 실행 중인 응용 프로그램에 대한 구성을 변경하는 방법을 보여 줍니다.

	%%configure 
	{"executorMemory": "3072M", "executorCores": 4, “numExecutors”:10}

구성 매개 변수는 JSON 문자열로 전달되어야 하며, 아래 예제 열과 같이 매직 뒤의 다음 줄에 있어야 합니다.

### spark-submit를 사용하여 제출된 응용 프로그램에 대한 매개 변수 변경

다음 명령은 `spark-submit`을 사용하여 제출된 배치 응용 프로그램에 대한 구성 매개 변수를 변경하는 방법의 예제입니다.

	spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### cURL을 사용하여 제출된 응용 프로그램에 대한 매개 변수 변경

다음 명령은 cURL을 사용하여 제출된 배치 응용 프로그램에 대한 구성 매개 변수를 변경하는 방법의 예제입니다.

	curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### Spark Thrift 서버에서 이러한 매개 변수를 변경하려면 어떻게 해야 하나요?

Spark Thrift 서버에서는 Spark 클러스터에 대한 JDBC/ODBC 액세스를 제공하고 Spark SQL 쿼리를 제공하는 데 사용됩니다. Power BI, Tableau 등과 같은 도구는 ODBC 프로토콜을 사용하여 Spark SQL 쿼리를 Spark 응용 프로그램으로 실행하기 위해 Spark Thrift 서버와 통신합니다. Spark 클러스터를 만들 경우 Spark Thrift 서버에서 각 헤드 노드에 하나씩 두 개의 인스턴스를 시작합니다. 각 Spark Thrift 서버는 YARN UI에서 Spark 응용 프로그램으로 표시됩니다.

Spark Thrift 서버는 Spark 동적 실행자 할당을 사용하며 따라서 `spark.executor.instances`을 사용하지 않습니다. 대신 Spark Thrift 서버는 `spark.dynamicAllocation.minExecutors` 및 `spark.dynamicAllocation.maxExecutors`을 사용하여 실행자 수를 지정합니다. 구성 매개 변수 `spark.executor.cores` 및 `spark.executor.memory`를 사용하여 실행자 크기를 수정합니다. 아래와 같이 이러한 매개 변수를 변경할 수 있습니다.

* **고급 spark-thrift-sparkconf** 범주를 확장하여 매개 변수 `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, 및 `spark.executor.memory`를 업데이트합니다.

	![Spark Thrift 서버 구성](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-1.png)

* **사용자 지정 spark-thrift-sparkconf** 범주를 확장하여 매개 변수 `spark.executor.cores`를 업데이트합니다.

	![Spark Thrift 서버 구성](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-2.png)

### Spark Thrift 서버의 드라이버 메모리를 변경하려면 어떻게 해야 하나요?

Spark Thrift 서버 드라이버 메모리는 헤드 노드 RAM 크기의 25%로 구성되어 제공된 헤드 노드의 총 RAM 크기는 14GB보다 큽니다. 아래와 같이 Ambari UI를 사용하여 드라이버 메모리 구성을 변경할 수 있습니다.

* Ambari UI에서 **Spark**, **Configs**를 클릭하고 **고급 spark-env**를 확장한 다음 **spark\_thrift\_cmd\_opts**에 대한 값을 제공합니다.

	![Spark Thrift 서버 RAM 구성](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-ram.png)

## Spark 클러스터와 함께 BI를 사용하지 않습니다. 리소스를 회수하려면 어떻게 해야 하나요?

Spark 동적 할당을 사용하기 때문에 Thrift 서버에서 사용되는 리소스만이 두 응용 프로그램 마스터에 대한 리소스입니다. 이러한 리소스를 회수하려면 클러스터에서 실행되는 Thrift 서버 서비스를 중지해야 합니다.

1. Ambari UI의 왼쪽된 창에서 **Spark**를 클릭합니다.

2. 다음 페이지에서 **Spark Thrift 서버**를 클릭합니다.

	![Thrift 서버 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-1.png)

3. Spark Thrift 서버가 실행되는 두 개의 헤드 노드가 표시되어야 됩니다. 헤드 노드 중 하나를 클릭합니다.

	![Thrift 서버 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-2.png)

4. 다음 페이지는 헤드 노드에서 실행 중인 모든 서비스를 나열합니다. 목록에서 Spark Thrift 서버 옆에 있는 드롭다운 단추를 클릭한 다음 **중지**를 클릭합니다.

	![Thrift 서버 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-3.png)

5. 다른 헤드 노드에서도 이 단계를 반복합니다.


## 내 Jupyter Notebook이 예상대로 실행되지 않습니다. 서비스를 다시 시작하려면 어떻게 해야 하나요?

1. 아래와 같이 Ambari 웹 UI를 시작합니다. 왼쪽의 탐색 창에서 **Jupyter**를 클릭하고, **서비스 작업**을 클릭한 다음 **모두 다시 시작**을 클릭합니다. 그러면 모든 헤드 노드에서 Jupyter 서비스가 시작됩니다.

	![Jupyter 다시 시작](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Jupyter 다시 시작")

	


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

* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### 도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0914_2016-->