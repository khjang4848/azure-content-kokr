<properties 
	pageTitle="HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그 | Microsoft Azure" 
	description="YARN UI, Spark UI 및 Spark 기록 서버를 사용하여 Azure HDInsight의 Spark 클러스터에서 실행 중인 작업을 추적하고 디버깅합니다." 
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

# HDInsight Linux의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그

이 문서에서는 YARN UI, Spark UI 및 Spark 기록 서버를 사용하여 Spark 작업을 추적하고 디버깅하는 방법을 알아봅니다. 이 문서의 경우 Spark 클러스터와 함께 사용할 수 있는 Notebook을 사용하여 Spark 작업을 시작합니다. **기계 학습: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석** 예를 들어 **spark-submit** 등 다른 방법을 사용하여 제출한 응용 프로그램을 추적하기 위해 이 단계를 사용할 수 있습니다.

##필수 조건

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
- Notebook을 실행하기 시작했어야 합니다. **[기계 학습: MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석](hdinsight-apache-spark-machine-learning-mllib-ipython.md)** 이 Notebook을 실행하는 방법은 링크를 따라갑니다.

## YARN UI에서 응용 프로그램 추적

1. YARN UI를 시작합니다. 클러스터 블레이드에서 **클러스터 대시보드**를 클릭하고 **YARN**을 클릭합니다.

	![YARN UI 시작](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)

	>[AZURE.TIP] 또는 Ambari UI에서 YARN UI를 시작할 수도 있습니다. Ambari UI를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**를 클릭한 다음 **HDInsight 클러스터 대시보드**를 클릭합니다. Ambari UI에서 **YARN**, **빠른 링크**, 활성 리소스 관리자를 차례로 클릭한 다음 **ResourceManager UI**를 클릭합니다.

3. Jupyter Notebook을 사용하여 Spark 작업을 시작했기 때문에 응용 프로그램은 **remotesparkmagics**이라는 이름을 갖게 됩니다(Notebook에서 시작된 모든 응용 프로그램에 대한 이름임). 응용 프로그램 이름에 대한 응용 프로그램 ID를 클릭하여 작업에 대한 자세한 정보를 봅니다. 그러면 응용 프로그램 보기를 시작합니다.

	![Spark 응용 프로그램 ID 찾기](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)

	Jupyter Notebook에서 시작된 이러한 응용 프로그램의 경우 Notebook을 끝낼 때까지 상태는 항상 **실행 중**입니다.

4. 응용 프로그램 보기에서 응용 프로그램 및 로그 (stdout/stderr)와 연결된 컨테이너에 대해 알아보기 위해 더 자세히 살펴볼 수 있습니다. 아래와 같이 **추적 URL**에 해당하는 연결을 클릭하여 Spark UI를 시작할 수도 있습니다.

	![컨테이너 로그 다운로드](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## Spark UI에서 응용 프로그램 추적

Spark UI에서 이전에 시작한 응용 프로그램에 의해 생성된 Spark 작업을 더 자세히 살펴볼 수 있습니다.

1. Spark UI를 시작하려면 응용 프로그램 보기에서 위의 화면 캡처에 표시된 것처럼 **추적 URL**에 대한 링크를 클릭합니다. Jupyter Notebook에서 실행 중인 응용 프로그램에 의해 시작되는 모든 Spark 작업을 확인할 수 있습니다.

	![Spark 작업 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)

2. **실행자** 탬을 클릭하여 각 실행자에 대한 처리 및 저장소 정보를 봅니다. **Thread Dump** 링크를 클릭하여 호출 스택을 검색할 수도 있습니다.

	![Spark 실행자 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
 
3. **단계** 탭을 클릭하여 응용 프로그램과 관련된 단계를 봅니다.

	![Spark 단계 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)

	각 단계에서는 아래와 같이 실행 통계를 볼 수 있는 여러 작업이 있습니다.

	![Spark 단계 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png)

4. 단계 세부 정보 페이지에서 DAG 시각화를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **DAG 시각화** 링크를 확장합니다.

	![Spark 단계 DAG 시각화 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

	DAG 또는 Direct Aclyic Graph는 응용 프로그램에서 다양한 단계를 나타냅니다. 그래프의 파란색 상자는 각각 응용 프로그램에서 호출한 Spark 작업을 나타냅니다.

5. 단계 세부 정보 페이지에서 응용 프로그램 타임라인 보기를 시작할 수 있습니다. 아래와 같이 페이지의 위쪽에서 **이벤트 타임라인** 링크를 확장합니다.

	![Spark 단계 이벤트 타임라인 보기](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)

	Spark 이벤트를 타임라인의 형태로 표시합니다. 타임라인 보기는 작업, 작업 내, 단계 내 등 세 가지 수준에서 사용할 수 있습니다. 위의 이미지는 지정된 단계에 대한 타임라인 보기를 캡처합니다.

	>[AZURE.TIP] **확대/축소 사용** 확인란을 선택한 경우 타임라인 보기를 좌우로 스크롤할 수 있습니다.

6. Spark UI에서 다른 탭도 Spark 인스턴스에 대한 유용한 정보를 제공합니다.

	* 저장소 탭 - 응용 프로그램이 RDD를 만드는 경우 저장소 탭에 대한 정보를 찾을 수 있습니다.
	* 환경 탭 - 이 탭은 다음과 같이 Spark 인스턴스에 대한 유용한 정보를 많이 제공합니다.
		* Scala 버전
		* 클러스터와 연결된 이벤트 로그 디렉터리
		* 응용 프로그램에 대한 실행자 코어 수
		* 등

## Spark 기록 서버를 사용하여 완료된 작업에 대한 정보 찾기

작업이 완료되면 작업에 대한 정보는 Spark 기록 서버에 유지됩니다.

1. Spark 기록 서버를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**를 클릭하고 **Spark 기록 서버**를 클릭합니다.

	![Spark 기록 서버 시작](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)

	>[AZURE.TIP] 또는 Ambari UI에서 Spark 기록 서버를 시작할 수도 있습니다. Ambari UI를 시작하려면 클러스터 블레이드에서 **클러스터 대시보드**를 클릭한 다음 **HDInsight 클러스터 대시보드**를 클릭합니다. Ambari UI에서 **Spark**, **빠른 링크**, **Spark 기록 서버 UI**를 차례로 클릭합니다.

2. 완료된 응용 프로그램을 모두 나열하여 표시합니다. 자세한 내용은 응용 프로그램 ID를 클릭하여 응용 프로그램에 대해 더 자세히 살펴봅니다.

	![Spark 기록 서버 시작](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)
	

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

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0914_2016-->