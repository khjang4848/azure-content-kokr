 <properties
	pageTitle="Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 | Microsoft Azure"
	description="독립 실행형 Spark 응용 프로그램을 만들어 HDInsight Spark 클러스터에서 실행하는 방법에 대해 알아봅니다."
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
	ms.date="08/30/2016"
	ms.author="nitinme"/>


# Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기

이 문서에서는 Scala로 작성된 Spark 응용 프로그램을 개발한 다음 Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터에 제출하는 과정의 단계별 지침을 제공합니다. 이 도구는 여러 가지 방식으로 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 응용 프로그램을 개발 및 제출하려면
* Azure HDInsight Spark 클러스터 리소스에 액세스하려면
* Scala Spark 응용 프로그램을 로컬로 개발 및 실행하려면

>[AZURE.IMPORTANT] 이 도구를 사용하면 Linux의 HDInsight Spark 클러스터용 응용 프로그램만 만들고 제출할 수 있습니다.


##필수 조건

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

* Oracle Java Development Kit 버전 7 및 버전 8.
	* HDInsight 클러스터가 Java 버전 7을 지원하는 것처럼 **Java SDK 7**은 Spark 프로젝트를 컴파일하는 데 사용됩니다. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)에서 Java SDK 7을 다운로드할 수 있습니다.
	* **Java SDK 8**은 Eclipse IDE 런타임에 사용됩니다. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 다운로드할 수 있습니다.

* Eclipse IDE. 이 문서에서는 Eclipse Neon을 사용합니다. [여기](https://www.eclipse.org/downloads/)에서 설치할 수 있습니다.

* Eclipse용 Scala IDE.
	* **Eclipse IDE가 설치되어 있는 경우** **도움말** -> **새 소프트웨어 설치**로 이동하여 Scala IDE 플러그 인을 추가할 수 있고 [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site)를 원본으로 추가하여 Eclipse용 Scala 플러그 인을 다운로드할 수 있습니다.
	* **Eclipse IDE가 설치되지 않은 경우** [여기](http://scala-ide.org/download/sdk.html)에서 Scala IDE를 직접 설치할 수 있습니다. 이 링크에서 .zip 파일을 다운로드하고 추출하고 **/eclipse** 폴더로 이동한 다음 거기서 **eclipse.exe** 파일을 실행합니다.
	
	>[AZURE.NOTE] 이 문서의 단계는 Scala 플러그 인이 설치된 Eclipse IDE를 사용하는 것을 기반으로 합니다.

* Spark SDK. [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 다운로드할 수 있습니다.

* [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html)에서 e(fx)clipse를 설치합니다.


## Eclipse용 Azure 도구 키트의 HDInsight 도구 설치

Eclipse용 HDInsight 도구는 Eclipse용 Azure 도구 키트의 일부분으로 제공됩니다. Azure 도구 키트를 설치하는 방법에 대한 지침은 [Eclipse용 Azure 도구 키트 설치](../azure-toolkit-for-eclipse-installation.md)를 참조하세요.

## Azure 구독에 로그인

1. Eclipse IDE를 시작하고 Azure 탐색기를 엽니다. IDE의 **창** 메뉴에서 **보기 표시**를 클릭한 다음 **기타**를 클릭합니다. 열린 대화 상자에서 **Azure**를 확장하고 **Azure 탐색기**를 클릭한 다음 **확인**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. **Azure 탐색기**에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 클릭합니다.

3. **구독 관리** 대화 상자에서 **로그인**을 클릭하고 Azure 자격 증명을 입력합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. 로그인하고 나면 **구독 관리** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. 대화 상자에서 **닫기**를 클릭합니다.

5. Azure 탐색기 탭에서 **HDInsight**를 확장하여 구독에서 HDInsight Spark 클러스터를 표시합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. 클러스터 이름 노드를 더 확장하여 클러스터와 연결된 리소스(예: 저장소 계정)를 표시할 수 있습니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## HDInsight Spark 클러스터에 Spark Scala 프로젝트 설정

1. Eclipse IDE 작업 영역에서 **파일**, **새로 만들기** 및 **프로젝트**를 차례로 클릭합니다.

2. **새 프로젝트** 마법사에서 **HDInsight**를 확장하고 **HDInsight의 Spark(Scala)**를 클릭한 후에 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. **새 HDInsight Scala 프로젝트** 대화 상자에서 아래 이미지와 같이 값을 입력/선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* 프로젝트의 이름을 입력합니다.
	* **JRE** 상자에서 **실행 환경 JRE 사용**을 **JavaSE-1.7**로 설정해야 합니다.
	* Spark SDK가 SDK를 다운로드한 위치로 설정되어 있는지 확인합니다. 다운로드 위치에 대한 링크는 이 항목 앞부분의 [필수 구성 요소](#prerequisites)에 나와 있습니다. 위의 이미지와 같이 이 대화 상자에 포함된 링크에서 SDK를 다운로드할 수도 있습니다.

4. 다음 대화 상자에서 **라이브러리** 탭을 클릭한 다음 **JRE 시스템 라이브러리[JavaSE 1.7]**을 두 번 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. **라이브러리 편집** 대화 상자에서 **실행 환경**을 **JavaSE-1.7(jdk1.7.0\_79)**로 설정해야 합니다. 옵션으로 사용할 수 없는 경우 다음 단계를 수행합니다.

	1. **대체 JRE** 옵션을 선택하고 **JavaSE-1.7(jdk1.7.0\_79)**를 사용할 수 있는지 확인합니다.
	2. 그렇지 않은 경우 **설치된 JRE** 단추를 클릭합니다.

		  ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. **설치된 JRE** 대화 상자에서 **추가**를 클릭합니다.

		  ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. **JRE 형식** 대화 상자에서 **표준 VM**을 선택한 후 **다음**을 클릭합니다.

		  ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. **JRE 정의** 대화 상자에서 디렉터리를 클릭한 다음 JDK 7을 설치할 위치로 이동하여 **jdk1.7.0\_79**의 루트 폴더를 선택합니다.

		  ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. **Finish**를 클릭합니다. **설치된 JRE** 대화 상자에서 새로 추가된 JRE를 선택한 다음 **확인**을 클릭합니다.

		   ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. 새로 추가한 JRE이 **실행 환경**에 나열됩니다. **Finish**를 클릭합니다.

	  	   ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. **라이브러리** 탭으로 돌아가서 **Scala 라이브러리 컨테이너[2.11.8]**을 두 번 클릭합니다. **라이브러리 편집** 대화 상자에서 **고정된 Scala 라이브러리 컨테이너:2.10.6**을 선택합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	**마침**을 클릭하여 프로젝트 설정 대화 상자를 끝냅니다.

## HDInsight Spark 클러스터에 Spark Scala 응용 프로그램 만들기

1. 이미 열려 있는 Eclipse IDE의 **패키지 탐색기**에서 앞서 만든 프로젝트를 확장하고 **src**를 마우스 오른쪽 단추로 클릭한 후에 **새로 만들기**를 가리키고 **기타**를 클릭합니다.

2. **마법사 선택** 대화 상자에서 **Scala 마법사**를 확장하고 **Scala 개체**, **다음**을 차례로 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. **새 파일 만들기** 대화 상자에서 개체의 이름을 입력한 다음 **마침**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. 텍스트 편집기에 다음 코드를 붙여 넣습니다.

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasbs:///HVACOut")
		  }		
		}


5. HDInsight Spark 클러스터에서 응용 프로그램 실행

	1. **패키지 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight에 Spark 응용 프로그램 제출**을 선택합니다.

	2. **Spark 제출** 대화 상자에서 다음 값을 입력합니다.

		* **클러스터 이름**의 경우 응용 프로그램을 실행하려는 HDInsight Spark 클러스터를 선택합니다.

		* Eclipse 프로젝트에서 아티팩트를 선택하거나 하드 디스크에서 아티팩트를 선택해야 합니다.

		* **주 클래스 이름** 텍스트 상자에는 코드에 지정된 개체의 이름을 입력합니다(아래 이미지 참조).

			![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* 이 예제의 응용 프로그램 코드에는 명령줄 인수가 필요하지 않고 JAR 또는 파일을 참조하지 않으므로 나머지 텍스트 상자를 비워둘 수 있습니다.

		* **Submit**를 클릭합니다.

	3. **Spark 제출** 탭에 진행 상태가 표시되기 시작합니다. "Spark 제출" 창에서 빨간색 단추를 클릭하여 응용 프로그램을 중지할 수 있습니다. 구형 아이콘(이미지에 파란색 상자로 표시됨)을 클릭하여 특정 응용 프로그램을 실행하는 로그를 볼 수도 있습니다.

        ![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    다음 섹션에서는 Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 작업 출력에 액세스하는 방법을 배웁니다.


## Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터 액세스 및 관리

HDInsight 도구를 사용하여 다양한 작업을 수행할 수 있습니다.

### 클러스터에 대한 저장소 컨테이너 액세스

1. Azure 탐색기에서 **HDInsight** 루트 노드를 확장하여 사용할 수 있는 HDInsight Spark 클러스터의 목록을 표시합니다.

3. 클러스터 이름을 확장하여 클러스터에 대한 저장소 계정 및 기본 저장소 컨테이너를 표시합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. 클러스터와 연결된 저장소 컨테이너 이름을 클릭합니다. 오른쪽 창에 **HVACOut** 폴더가 표시됩니다. 폴더를 두 번 클릭하여 열면 **part-*** 파일이 나타납니다. 이러한 파일 중 하나를 열어 응용 프로그램의 출력을 표시합니다.

### Spark 기록 서버 액세스

1. **Azure 탐색기**에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **Spark 기록 UI 열기**를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

2. Spark 기록 서버 대시보드에서 응용 프로그램 이름을 사용하여 방금 실행을 마친 응용 프로그램을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("MyClusterApp")`을 사용하여 응용 프로그램 이름을 설정했습니다. 그러므로 Spark 응용 프로그램의 이름은 **MyClusterApp**입니다.

### Ambari 포털 시작

**Azure 탐색기**에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **클러스터 관리 포털 열기(Ambari)**를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

### Azure 구독 관리

기본적으로 Eclipse용 Azure 도구 키트의 HDInsight 도구에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 클러스터에 액세스하려는 구독을 지정할 수 있습니다. **Azure 탐색기**에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 클릭합니다. 대화 상자에서 액세스하지 않으려는 구독의 확인란 선택을 취소하고 **닫기**를 클릭합니다. Azure 구독에서 로그오프하려는 경우 **로그아웃**을 클릭할 수도 있습니다.


## 로컬로 Spark Scala 응용 프로그램 실행

Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 워크스테이션에서 Spark Scala 응용 프로그램을 로컬로 실행할 수 있습니다. 일반적으로 이러한 응용 프로그램은 저장소 컨테이너와 같은 클러스터 리소스에 액세스할 필요가 없으므로 로컬로 실행하고 테스트할 수 있습니다.

### 필수 요소

Windows 컴퓨터에서 로컬 Spark Scala 응용 프로그램을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에 설명된 예외가 발생할 수 있습니다. 이 예외는 Windows OS에 **WinUtils.exe**가 없는 경우 발생합니다. 이 오류를 해결하려면 [여기서 실행 파일을 다운로드](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\\WinUtils\\bin**과 같은 위치에 저장해야 합니다. 그런 다음 환경 변수 **HADOOP\_HOME**을 추가하고 변수 값을 **C\\WinUtils**로 설정합니다.

### 로컬 Spark Scala 응용 프로그램 실행	 

1. Eclipse를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **HDInsight의 Spark 로컬 실행 샘플(Scala)**을 선택합니다.
	* **Next**를 클릭합니다.

2. 프로젝트 세부 정보를 제공하려면 이전의 [HDInsight Spark 클러스터에 Spark Scala 응용 프로그램 프로젝트 설정] 섹션에 표시된 대로 3-6단계를 따릅니다(#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. 템플릿은 컴퓨터에서 로컬로 실행할 수 있는 샘플 코드(**LogQuery**)를 **src** 폴더 아래에 추가합니다.

	![Spark 응용 프로그램 로컬 실행 결과](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  **LogQuery** 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **다음으로 실행**을 가리킨 다음 **1 Scala 응용 프로그램**을 클릭합니다. **콘솔** 탭 아래쪽에 다음과 같은 출력이 표시됩니다.

	![Spark 응용 프로그램 로컬 실행 결과](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## 사용자 의견 및 알려진 문제

현재 직접 Spark 출력 보기는 지원되지 않으며 준비 중입니다.

제안 또는 피드백이 있거나 이 도구를 사용할 때 문제가 발생하는 경우 microsoft.com의 hdivstool로 메일을 보내 주시기 바랍니다.

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

* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->