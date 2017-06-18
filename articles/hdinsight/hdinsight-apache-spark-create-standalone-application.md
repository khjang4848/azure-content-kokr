<properties
	pageTitle="독립 실행형 Scala 응용 프로그램을 만들어 HDInsight Spark 클러스터에서 실행하기 | Microsoft Azure"
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
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# HDInsight Linux의 Apache Spark에서 실행할 독립 실행형 Scala 응용 프로그램 만들기

이 문서에서는 IntelliJ IDEA와 함께 Maven을 사용하여 Scala로 작성된 독립 실행형 Spark 응용 프로그램 개발에 대한 단계별 지침을 제공합니다. 문서는 빌드 시스템으로 Apache Maven을 사용하고 IntelliJ IDEA에서 제공하는 Scala에 대한 기존 Maven 원형으로 시작합니다. IntelliJ IDEA에서 전반적인 Scala 응용 프로그램 만들기는 다음 단계를 포함합니다.


* 빌드 시스템으로 Maven을 사용합니다.
* POM(프로젝트 개체 모델) 파일을 업데이트하여 Spark 모듈 종속성을 해결합니다.
* Scala에서 응용 프로그램을 작성합니다.
* HDInsight Spark 클러스터에 제출할 수 있는 jar 파일을 생성합니다.
* Livy를 사용하여 Spark 클러스터에서 응용 프로그램을 실행합니다.

>[AZURE.NOTE] 또한 HDInsight는 Linux의 HDInsight Spark 클러스터에 대한 응용 프로그램을 만들고 제출하는 과정을 용이하게 하는 IntelliJ IDEA 플러그 인 도구를 제공합니다. 자세한 내용은 [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)을 참조하세요.


**필수 구성 요소**

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
* Java IDE. 이 문서에서는 IntelliJ IDEA 15.0.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.


## IntelliJ IDEA용 Scala 플러그 인 설치

IntelliJ IDEA 설치가 Scala 플러그 인 활성화를 묻지 않은 경우 IntelliJ IDEA를 시작하고 다음 단계를 진행하여 플러그 인을 설치합니다.

1. IntelliJ IDEA를 시작하고 시작 화면에서 **구성**을 클릭한 다음 **플러그 인**을 클릭합니다.

	![scala 플러그 인 활성화](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)

2. 다음 화면에서 왼쪽 아래 모서리에서 **JetBrains 플러그 인 설치**를 클릭합니다. 열리는 **JetBrains 플러그 인 찾아보기** 대화 상자에서Scala를 검색한 다음 **설치**를 클릭합니다.

	![scala 플러그 인 설치](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)

3. 플러그 인이 성공적으로 설치되면 **IntelliJ IDEA 다시 시작 단추**를 클릭하여 IDE를 다시 시작합니다.

## 독립 실행형 Scala 프로젝트 만들기

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Maven 프로젝트 만들기](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)

	* 프로젝트 유형으로 **Maven**을 선택합니다.
	* **프로젝트 SDK**를 지정합니다. 새로 만들기를 클릭하여 Java 설치 디렉터리, 일반적으로 `C:\Program Files\Java\jdk1.8.0_66`(으)로 이동합니다.
	* **원형으로부터 만들기** 옵션을 선택합니다.
	* 원형 목록에서 **org.scala-tools.archetypes:scala-archetype-simple**을 선택합니다. 이는 올바른 디렉터리 구조를 만들고 Scala 프로그램 작성에 필요한 기본 종속성을 다운로드합니다.

2. **GroupId**, **ArtifactId** 및 **버전**에 관련 값을 제공합니다. **다음**을 클릭합니다.

3. Maven 홈 디렉터리 및 기타 사용자 설정을 지정하는 다음 대화 상자에서 기본값을 적용하고 **다음**을 클릭합니다.

4. 마지막 대화 상자에서 프로젝트 이름 및 위치를 지정한 다음 **마침**을 클릭합니다.

5. **src\\test\\scala\\com\\microsoft\\spark\\example**에서 **MySpec.Scala** 파일을 삭제합니다. 응용 프로그램에 필요하지 않습니다.

6. 필요한 경우 기본 원본 및 테스트 파일의 이름을 바꿉니다. IntelliJ IDEA의 왼쪽 창에서 **src\\main\\scala\\com.microsoft.spark.example**로 이동합니다. **App.scala**를 마우스 오른쪽 단추로 클릭하고 **Refactor**를 클릭하고 파일 이름 바꾸기를 클릭하고 대화 상자에서 응용 프로그램에 대한 새 이름을 제공한 다음 **Refactor**를 클릭합니다.

	![파일 이름 바꾸기](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)

7. 이후 단계에서는 pom.xml을 업데이트하여 Spark Scala 응용 프로그램에 대한 종속성을 정의합니다. 다운로드되고 자동으로 해결되는 이러한 종속성의 경우 Maven을 적절하게 구성해야 합니다.

	![자동 다운로드를 위해 Maven 구성](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)

	1. **파일** 메뉴에서 **설정**을 클릭합니다.
	2. **설정** 대화 상자에서 **빌드, 실행, 배포** > **빌드 도구** > **Maven** > **가져오기**로 이동합니다.
	3. **Maven 프로젝트 자동으로 가져오기**에 옵션을 선택합니다.
	4. **적용**을 클릭한 다음 **확인**을 클릭합니다.


8. Scala 소스 파일을 업데이트하여 응용 프로그램 코드를 포함합니다. 기존 샘플 코드를 열고 다음 코드로 바꾸고 변경 내용을 저장합니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, 여섯 번째 열에 한 자리만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut**에 씁니다.

		package com.microsoft.spark.example

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext

		/**
		  * Test IO to wasb
		  */
		object WasbIOTest {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("WASBIOTest")
		    val sc = new SparkContext(conf)

		    val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

		    rdd1.saveAsTextFile("wasbs:///HVACout")
		  }
		}


9. pom.xml를 업데이트합니다.

	1.  `<project><properties>`에 다음을 추가합니다.

			<scala.version>2.10.4</scala.version>
    		<scala.compat.version>2.10.4</scala.compat.version>
			<scala.binary.version>2.10</scala.binary.version>

	2. `<project><dependencies>`에 다음을 추가합니다.

			<dependency>
		      <groupId>org.apache.spark</groupId>
		      <artifactId>spark-core_${scala.binary.version}</artifactId>
		      <version>1.4.1</version>
		    </dependency>

	pom.xml에 변경 내용을 저장합니다.

10. .jar 파일을 만듭니다. IntelliJ IDEA는 프로젝트의 아티팩트로 JAR을 작성할 수 있습니다. 다음 단계를 수행합니다.

	1. **파일** 메뉴에서 **프로젝트 구조**를 클릭합니다.
	2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭한 다음 더하기 기호를 클릭합니다. 팝업 대화 상자에서 **JAR**을 클릭한 다음 **종속성이 있는 모듈에서**를 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)

	3. **모듈에서 JAR 만들기** 대화 상자에서 **기본 클래스**에 대해 줄임표(![줄임표](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png))를 클릭합니다.

	4. **기본 클래스 선택** 대화 상자에서 기본적으로 표시되는 클래스를 선택한 다음 **확인**을 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)

	5. **모듈에서 JAR 만들기** 대화 상자에서 **대상 JAR에 추출**에 대한 옵션이 선택되었는지 확인한 다음 **확인**을 클릭합니다. 이렇게 하면 모든 종속성이 있는 단일 JAR이 만들어집니다.

		![JAR 만들기](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)

	6. 출력 레이아웃 탭에는 Maven 프로젝트의 일부분으로 포함된 jar이 모두 나열됩니다. 직접 종속성이 없는 Scala 응용 프로그램을 선택하고 삭제할 수 있습니다. 여기에서 만드는 응용 프로그램의 경우 마지막 것(**SparkSimpleApp 컴파일 출력**)을 제외한 모두를 제거할 수 있습니다. jar을 선택하여 삭제한 다음 **삭제** 아이콘을 클릭합니다.

		![JAR 만들기](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)

		프로젝트를 작성하거나 업데이트할 때마다 jar이 생성되는지 확인하는 **Build on make** 확인란이 선택되었는지 확인합니다. **적용**을 클릭한 다음 **확인**을 클릭합니다.

	7. 메뉴 모음에서 **빌드**를 클릭한 다음 **프로젝트 만들기**를 클릭합니다. **빌드 아티팩트**를 클릭하여 jar을 만들 수도 있습니다. **\\out\\artifacts** 아래에 출력 jar이 만들어집니다.

		![JAR 만들기](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## Spark 클러스터에서 응용 프로그램 실행

클러스터에서 응용 프로그램을 실행하려면 다음을 수행해야 합니다.

* 클러스터와 연결된 **Azure 저장소 Blob에 응용 프로그램 jar을 복사**합니다. 이렇게 하기 위해 명령줄 유틸리티 [**AzCopy**](../storage/storage-use-azcopy.md)를 사용할 수 있습니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.

* **Livy를 사용하여 응용 프로그램 작업을 원격으로** Spark 클러스터에 제출합니다. HDInsight의 Spark 클러스터에는 Spark 작업을 원격으로 제출하는 REST 끝점을 노출하는 Livy가 포함됩니다. 자세한 내용은 [HDInsight의 Spark 클러스터와 함께 Livy를 사용하여 원격으로 Spark 작업 제출](hdinsight-apache-spark-livy-rest-interface.md)을 참조하세요.


## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

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

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->