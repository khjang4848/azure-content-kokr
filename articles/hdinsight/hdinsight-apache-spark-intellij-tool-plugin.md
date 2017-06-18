 <properties
	pageTitle="IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 | Microsoft Azure"
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
	ms.date="09/09/2016"
	ms.author="nitinme"/>


# IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Spark 응용 프로그램 만들기

이 문서에서는 Scala로 작성된 Spark 응용 프로그램을 개발한 다음 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터에 제출하는 과정에 대한 단계별 지침을 제공합니다. 이 도구는 여러 가지 방식으로 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 응용 프로그램을 개발 및 제출하려면
* Azure HDInsight Spark 클러스터 리소스에 액세스하려면
* Scala Spark 응용 프로그램을 로컬로 개발 및 실행하려면

또한 [여기](https://mix.office.com/watch/1nqkqjt5xonza)에 있는 비디오를 따라 시작할 수도 있습니다.

>[AZURE.IMPORTANT] 이 도구를 사용하면 Linux의 HDInsight Spark 클러스터용 응용 프로그램만 만들고 제출할 수 있습니다.


##필수 조건

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.

* IntelliJ IDEA. 이 문서에서는 버전 15.0.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.

## IntelliJ용 Azure 도구 키트에서 HDInsight 도구 설치

IntelliJ용 HDInsight 도구는 IntelliJ용 Azure 도구 키트의 일부로 사용할 수 있습니다. Azure 도구 키트를 설치하는 방법에 대한 지침은 [IntelliJ용 Azure 도구 키트 설치](../azure-toolkit-for-intellij-installation.md)를 참조하세요.

## Azure 구독에 로그인

1. IntelliJ IDE를 시작하고 Azure 탐색기를 엽니다. IDE의 **보기** 메뉴에서 **도구 창**, **Azure 탐색기**를 차례로 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. **Azure 탐색기**에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 클릭합니다.

3. **구독 관리** 대화 상자에서 **로그인**을 클릭하고 Azure 자격 증명을 입력합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 로그인하고 나면 **구독 관리** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. 대화 상자에서 **닫기**를 클릭합니다.

5. **Azure 탐색기** 탭에서 **HDInsight**를 확장하여 구독 중인 HDInsight Spark 클러스터를 표시합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 클러스터 이름 노드를 더 확장하여 클러스터와 연결된 리소스(예: 저장소 계정)를 표시할 수 있습니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## HDInsight Spark 클러스터에서 Spark Scala 응용 프로그램 실행

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **HDInsight의 Spark(Scala)**를 선택합니다.
	* **다음**을 클릭합니다.

2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

	* 프로젝트 이름과 프로젝트 위치를 제공합니다.
	* **프로젝트 SDK**의 경우 7보다 높은 Java 버전을 제공해야 합니다.
	* **Scala SDK**의 경우 **만들기**를 클릭하고 **다운로드**를 클릭한 다음 사용할 Scala의 버전을 선택합니다. **2.11.x 버전은 사용하지 마세요**. 이 샘플에서는 버전 **2.10.6**을 사용합니다.

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* **Spark SDK**의 경우 [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 SDK를 다운로드하여 사용합니다. 이 메시지를 무시하고 [Spark Maven 리포지토리](http://mvnrepository.com/search?q=spark)를 대신 사용할 수도 있으나 Spark 응용 프로그램을 개발하기 위한 올바른 Maven 리포지토리가 설치되어 있어야 합니다. (예를 들어 Spark 스트리밍을 사용하는 경우 Spark 스트리밍 부분이 설치되어 있는지 확인해야 합니다. 또한 Scala 2.10으로 표시된 리포지토리를 사용하고 있는지 확인해야 합니다. Scala 2.11로 표시된 리포지토리는 사용하지 마세요.)

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* **마침**을 클릭합니다.

3. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 아티팩트를 확인하려면 이 단계를 따릅니다.

	1. **파일** 메뉴에서 **프로젝트 구조**를 클릭합니다.
	2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭하여 만들어지는 기본 아티팩트를 봅니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

	위의 그림에서 강조 표시된 **+** 아이콘을 클릭하여 사용자 고유의 아티팩트를 만들 수도 있습니다.

4. **프로젝트 구조** 대화 상자에서 **프로젝트**를 클릭합니다. **프로젝트 SDK**가 1.8로 설정되어 있다면 **프로젝트 언어 수준**이 **7 - 다이아몬드, ARM, 다중 캐치 등**으로 설정되어 있는지 확인합니다.

	![프로젝트 언어 수준 설정](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. 응용 프로그램 소스 코드 적용

	1. **프로젝트 탐색기**에서 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala class(Scala 클래스)**를 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. **Create New Scala Class(새 Scala 클래스 만들기)** 대화 상자에서 이름을 제공하고 **종류**에 대해 **개체**를 선택하고 **확인**을 클릭합니다.

		![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. **MyClusterApp.scala** 파일에 다음 코드를 붙여넣습니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut**에 씁니다.


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

	1. **프로젝트 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight에 Spark 응용 프로그램 제출**을 선택합니다.

		![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Azure 구독 자격 증명을 입력하라는 메시지가 표시됩니다. **Spark 제출** 대화 상자에 다음 값을 제공합니다.

		* **Spark 클러스터(Linux만 해당)**의 경우 응용 프로그램을 실행하려는 HDInsight Spark 클러스터를 선택합니다.

		* IntelliJ 프로젝트에서 아티팩트를 선택하거나 하드 디스크에서 아티팩트를 선택해야 합니다.

		* **기본 클래스 이름** 텍스트 상자에서 줄임표(![줄임표](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))를 클릭하고 응용 프로그램 소스 코드에서 기본 클래스를 선택한 다음 **확인**을 클릭합니다.

			![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* 이 예제의 응용 프로그램 코드에는 명령줄 인수가 필요하지 않고 JAR 또는 파일을 참조하지 않으므로 나머지 텍스트 상자를 비워둘 수 있습니다.

		* 모든 입력을 제공하면 대화 상자는 다음과 같아야 합니다.

			![Spark 응용 프로그램 제출](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* **Submit**를 클릭합니다.

	3. 창 아래쪽의 **Spark 제출** 탭에 진행 상태가 표시되기 시작합니다. 또한 "Spark Submission(Spark 제출)" 창에서 빨간색 단추를 클릭하여 응용 프로그램을 중지할 수 있습니다.

        ![Spark 응용 프로그램 결과](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    다음 섹션에서는 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 작업 출력에 액세스하는 방법에 대해 알아봅니다.


## IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터 액세스 및 관리

IntelliJ용 Azure 도구 키트의 일부분인 HDInsight 도구를 사용하면 다양한 작업을 수행할 수 있습니다.

### HDInsight 도구에서 직접 작업 보기에 액세스

1. **Azure 탐색기**에서 **HDInsight**, Spark 클러스터 이름을 차례로 확장한 다음 **작업**을 클릭합니다.

2. 오른쪽 창의 **Spark 작업 보기** 탭에는 클러스터에서 실행된 모든 응용 프로그램이 표시됩니다. 자세한 내용을 보려면 원하는 응용 프로그램 이름을 클릭합니다.

	![작업 보기 액세스](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. **오류 메시지**, **작업 출력**, **Livy 작업 로그** 및 **Spark 드라이버 로그** 상자가 선택한 응용 프로그램을 기준으로 채워집니다.

4. 화면 맨 아래에 있는 각 단추를 클릭하여 **Spark 기록 UI** 및 **YARN UI**(응용 프로그램 수준)를 열 수도 있습니다.

### Spark 기록 서버 액세스

1. **Azure 탐색기**에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음 **Spark 기록 UI 열기**를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

2. Spark 기록 서버 대시보드에서 응용 프로그램 이름을 사용하여 방금 실행을 마친 응용 프로그램을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("MyClusterApp")`을 사용하여 응용 프로그램 이름을 설정했습니다. 그러므로 Spark 응용 프로그램의 이름은 **MyClusterApp**입니다.

### Ambari 포털 시작

**Azure 탐색기**에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음 **클러스터 관리 포털 열기(Ambari)**를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정해야 합니다.

### Azure 구독 관리

기본적으로 HDInsight 도구에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 클러스터에 액세스하려는 구독을 지정할 수 있습니다. **Azure 탐색기**에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 클릭합니다. 대화 상자에서 액세스하지 않으려는 구독의 확인란 선택을 취소하고 **닫기**를 클릭합니다. Azure 구독에서 로그오프하려는 경우 **로그아웃**을 클릭할 수도 있습니다.


## 로컬로 Spark Scala 응용 프로그램 실행

IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 워크스테이션에서 Spark Scala 응용 프로그램을 로컬로 실행할 수 있습니다. 일반적으로 이러한 응용 프로그램은 저장소 컨테이너와 같은 클러스터 리소스에 액세스할 필요가 없으므로 로컬로 실행하고 테스트할 수 있습니다.

### 필수 요소

Windows 컴퓨터에서 로컬 Spark Scala 응용 프로그램을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에 설명된 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없는 경우 발생합니다. 이 오류를 해결하려면 [여기에서 실행 파일을 다운로드](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\\WinUtils\\bin** 등의 위치에 저장해야 합니다. 그런 다음 환경 변수 **HADOOP\_HOME**을 추가하고 변수 값을 **C\\WinUtils**로 설정합니다.

### 로컬 Spark Scala 응용 프로그램 실행	 

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **HDInsight의 Spark 로컬 실행 샘플(Scala)**을 선택합니다.
	* **다음**을 클릭합니다.

2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

	* 프로젝트 이름과 프로젝트 위치를 제공합니다.
	* **프로젝트 SDK**의 경우 7보다 높은 Java 버전을 제공해야 합니다.
	* **Scala SDK**의 경우 **만들기**를 클릭하고 **다운로드**를 클릭한 다음 사용할 Scala의 버전을 선택합니다. **2.11.x 버전은 사용하지 마세요**. 이 샘플에서는 버전 **2.10.6**을 사용합니다.

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* **Spark SDK**의 경우 [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 SDK를 다운로드하여 사용합니다. 이 메시지를 무시하고 [Spark Maven 리포지토리](http://mvnrepository.com/search?q=spark)를 대신 사용할 수도 있으나 Spark 응용 프로그램을 개발하기 위한 올바른 Maven 리포지토리가 설치되어 있어야 합니다. (예를 들어 Spark 스트리밍을 사용하는 경우 Spark 스트리밍 부분이 설치되어 있는지 확인해야 합니다. 또한 Scala 2.10으로 표시된 리포지토리를 사용하고 있는지 확인해야 합니다. Scala 2.11로 표시된 리포지토리는 사용하지 마세요.)

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

	* **마침**을 클릭합니다.

3. 템플릿은 컴퓨터에서 로컬로 실행할 수 있는 샘플 코드(**LogQuery**)를 **src** 폴더 아래에 추가합니다.

	![로컬 Scala 응용 프로그램](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  아래에서 **LogQuery** 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **"'LogQuery' 실행"**을 클릭합니다. 아래쪽의 **실행** 탭에 다음과 같은 출력이 표시됩니다.

	![Spark 응용 프로그램 로컬 실행 결과](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하도록 기존 IntelliJ IDEA 응용 프로그램 변환

IntelliJ IDEA에서 만든 기존 Spark Scala 응용 프로그램을 IntelliJ용 Azure 도구 키트의 HDInsight 도구와 호환되도록 변환할 수도 있습니다. 이렇게 하면 도구를 사용하여 HDInsight Spark 클러스터에 응용 프로그램을 제출할 수 있습니다. 다음 단계를 사용하여 이 작업을 수행할 수 있습니다.

1. IntelliJ IDEA를 사용하여 만들어진 기존 Spark Scala 응용 프로그램의 경우 관련된 .iml 파일을 엽니다.
2. 루트 수준에 다음과 같은 **module** 요소가 표시됩니다.

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. **module** 요소가 다음과 같이 표시되도록 요소를 편집하여 `UniqueKey="HDInsightTool"`을 추가합니다.

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. 변경 내용을 저장합니다. 이제 응용 프로그램이 IntelliJ용 Azure 도구 키트의 HDInsight 도구와 호환됩니다. 프로젝트 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 이 테스트를 수행할 수 있습니다. 팝업 메뉴에 **Submit Spark Application to HDInsight(HDInsight에 Spark 응용 프로그램 제출)** 옵션이 표시됩니다.


## 문제 해결

### 로컬 실행에서 "더 큰 힙 크기를 사용하세요." 오류 발생

Spark 1.6에서 로컬 실행 동안 32비트 Java SDK를 사용하는 경우 다음과 같은 오류가 발생할 수 있습니다.

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
    	at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
    	at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
    	at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
    	at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
    	at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
    	at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
    	at LogQuery$.main(LogQuery.scala:53)
    	at LogQuery.main(LogQuery.scala)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:606)
    	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Spark에는 471MB 이상이 필요하므로 힙 크기가 충분히 크지 않아 Spark에 실행할 수 없을 때 이 문제가 발생합니다(원하는 경우 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)에서 추가 세부 정보를 가져올 수 있음). 한 가지 간단한 해결 방법은 64비트 Java SDK를 사용하는 것입니다. 또한 다음 옵션을 추가하여 IntelliJ의 JVM 설정을 변경할 수도 있습니다.

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Spark 응용 프로그램 로컬 실행 결과](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

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

* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->