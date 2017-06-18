<properties
	pageTitle="스크립트 작업을 사용하여 Hadoop 클러스터에 Spark 설치 | Microsoft Azure"
	description="스크립트 작업을 사용하여 Spark로 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# 스크립트 작업을 사용하여 HDInsight Hadoop 클러스터에 Spark 설치 및 사용

> [AZURE.IMPORTANT] 이 문서는 이제 사용되지 않습니다. 이제 HDInsight는 Windows 기반 클러스터를 위한 최고급 클러스터 형식으로 Spark를 제공하므로 스크립트 작업을 사용하여 Hadoop 클러스터를 수정하지 않고 Spark 클러스터를 직접 만들 수 있습니다. Spark 클러스터 유형을 사용하여 Spark 버전 1.3.1과 함께 HDInsight 버전 3.2 클러스터를 얻습니다. 다른 버전의 Spark를 설치하려면 스크립트 작업을 사용할 수 있습니다. HDInsight는 샘플 스크립트 작업 스크립트를 제공합니다.

스크립트 작업을 사용하여 Windows 기반 HDInsight에서 Spark를 설치하는 방법 및 HDInsight 클러스터에서 Spark 쿼리를 실행하는 방법을 알아봅니다.


**관련된 문서**

- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.

- [HDInsight에서 Apache Spark 시작](hdinsight-apache-spark-jupyter-spark-sql.md): HDInsight Spark 클러스터를 만듭니다.

- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.

- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)

## Spark란?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. Spark는 메모리 내 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다.

Spark를 사용하여 기존 디스크 기반 데이터 처리를 수행할 수도 있습니다. Spark는 중간 단계에서 디스크에 쓰기를 방지하여 기존 MapReduce 프레임워크를 향상시킵니다. 또한 Spark는 HDFS(Hadoop Distributed File System) 및 Azure Blob 저장소와 호환되므로 Spark를 통해 기존 데이터를 쉽게 처리할 수 있습니다.

이 항목에서는 HDInsight 클러스터를 사용자 지정하여 Spark를 설치하는 방법에 대한 지침을 제공합니다.

## Azure 포털을 사용하여 Spark 설치

HDInsight 클러스터에 Spark를 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1))에서 다운로드할 수 있습니다. 이 스크립트는 만든 HDInsight 클러스터의 버전에 따라 Spark 1.2.0 또는 Spark 1.0.2를 설치할 수 있습니다.

- **HDInsight 3.2** 클러스터를 만드는 동안 스크립트를 사용하면 **Spark 1.2.0**가 설치됩니다.
- **HDInsight 3.1** 클러스터를 만드는 동안 스크립트를 사용하면 **Spark 1.0.2**가 설치됩니다.

이 스크립트를 수정하거나 사용자 고유의 스크립트를 만들어 다른 버전의 Spark를 설치할 수 있습니다.

> [AZURE.NOTE] 샘플 스크립트는 HDInsight 3.1 및 3.2 클러스터에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터를 만들기 시작합니다. 다음에 따라 클러스터 버전을 선택합니다.

	- **Spark 1.2.0**를 설치하려면 HDInsight 3.2 클러스터를 만듭니다.
	- **Spark 1.0.2**를 설치하려면 HDInsight 3.1 클러스터를 만듭니다.


2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다. 예를 들면 <b>Install Spark</b>와 같습니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예를 들면 <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i>과 같습니다.</td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Spark를 설치하는 스크립트에는 매개 변수가 필요하지 않으므로 비워 둘 수 있습니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 만들기를 시작합니다.

스크립트를 사용하여 Azure PowerShell 또는 HDInsight.NET SDK로 HDInsight에 Spark를 설치할 수도 있습니다. 이 절차에 대한 자세한 내용은 이 항목의 뒷부분에 제공됩니다.

## HDInsight에서 Spark 사용
Spark는 Scala, Python 및 Java의 API를 제공합니다. 대화형 Spark 셸을 사용하여 Spark 쿼리를 실행할 수도 있습니다. 이 섹션에서는 다음 두 가지 방식으로 Spark를 사용하는 방법에 대한 지침을 제공합니다.

- [Spark 셸을 사용하여 대화형 쿼리 실행](#sparkshell)
- [Spark 셸을 사용하여 Spark SQL 쿼리 실행](#sparksql)
- [독립 실행형 Scala 프로그램 사용](#standalone)

###<a name="sparkshell"></a>Spark 셸을 사용하여 대화형 쿼리 실행
대화형 Spark 셸에서 Spark 쿼리를 실행하려면 다음 단계를 수행합니다. 이 섹션에서는 기본적으로 HDInsight 클러스터에서 사용할 수 있는 샘플 데이터 파일(/example/data/gutenberg/davinci.txt)에 대한 Spark 쿼리를 실행합니다.

1. Azure 포털에서 Spark를 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 원격으로 연결합니다. 지침은 [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)을 참조하세요.

2. RDP(원격 데스크톱 프로토콜) 세션의 데스크톱에서 Hadoop 명령줄을 열고(바탕 화면 바로 가기에서) Spark가 설치된 위치(예: C:**C:\\apps\\dist\\spark-1.2.0**)로 이동합니다.


3. 다음 명령을 실행하여 Spark 셸을 시작합니다.

		 .\bin\spark-shell --master yarn

	명령 실행이 완료되면 Scala 프롬프트가 표시됩니다.

		 scala>

5. Scala 프롬프트에 아래에 표시된 Spark 쿼리를 입력합니다. 이 쿼리는 클러스터와 연결된 Azure Blob 저장소의 /example/data/gutenberg/ 위치에서 사용할 수 있는 davinci.txt 파일에 나오는 각 단어의 수를 계산합니다.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 출력은 다음과 유사합니다.

	![HDInsight 클러스터에서 Scala 대화형 셸을 실행한 출력](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. :q 입력하여 Scala 프롬프트를 종료합니다.

		:q

###<a name="sparksql"></a>Spark 셸을 사용하여 Spark SQL 쿼리 실행

Spark SQL을 사용하면 Spark를 사용하여 SQL(구조적 쿼리 언어), HiveQL 또는 Scala로 표현된 관계형 쿼리를 실행할 수 있습니다. 이 섹션에서는 Spark를 사용하여 샘플 Hive 테이블에서 Hive 쿼리를 실행하는 방법을 알아봅니다. 이 섹션에서 사용하는 Hive 테이블(**hivesampletable**이라고 함)은 클러스터를 만들 때 기본적으로 사용할 수 있습니다.

>[AZURE.NOTE] 아래 샘플은 HDInsight 3.2 클러스터를 만드는 동안 스크립트 작업을 실행하는 경우에 설치되는 **Spark 1.2.0**에 맞게 만들어졌습니다.

1. Azure 포털에서 Spark를 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 원격으로 연결합니다. 지침은 [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)을 참조하세요.

2. RDP 세션의 데스크톱에서 Hadoop 명령줄을 열고(바탕 화면 바로 가기에서) Spark가 설치된 위치(예: C:**C:\\apps\\dist\\spark-1.2.0**)로 이동합니다.


3. 다음 명령을 실행하여 Spark 셸을 시작합니다.

		 .\bin\spark-shell --master yarn

	명령 실행이 완료되면 Scala 프롬프트가 표시됩니다.

		 scala>

4. Scala 프롬프트에서 Hive 컨텍스트를 설정합니다. 이는 Spark를 사용하여 Hive 쿼리 작업을 수행하는 데 필요합니다.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	**sc**는 Spark 셸을 시작할 때 설정되는 기본 Spark 컨텍스트입니다.

5. Hive 컨텍스트를 사용하여 Hive 쿼리를 실행하고 출력을 콘솔에 인쇄합니다. 이 쿼리는 특정 제조업체의 장치에 대한 데이터를 검색하며, 검색되는 레코드 수를 20으로 제한합니다.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 다음과 유사한 출력이 표시됩니다.

	![HDInsight 클러스터에서 Spark SQL을 실행한 출력](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. :q 입력하여 Scala 프롬프트를 종료합니다.

		:q

### <a name="standalone"></a>독립 실행형 Scala 프로그램 사용

이 섹션에서는 기본적으로 HDInsight 클러스터에서 사용할 수 있는 샘플 데이터 파일(/example/data/gutenberg/davinci.txt)에서 'a' 및 'b' 문자가 포함된 줄 수를 계산하는 Scala 응용 프로그램을 작성합니다. Spark 설치를 통해 사용자 지정된 클러스터에서 독립 실행형 Scala 프로그램을 작성하고 사용하려면 다음 단계를 수행해야 합니다.

- Scala 프로그램 작성
- Scala 프로그램을 빌드하여 .jar 파일 가져오기
- 클러스터에서 작업 실행

#### Scala 프로그램 작성
이 섹션에서는 샘플 데이터 파일에서 'a' 및 'b'가 포함된 줄 수를 계산하는 Scala 프로그램을 작성합니다.

1. 텍스트 편집기를 열고 다음 코드를 붙여 넣습니다.


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf

		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. 파일을 **SimpleApp.scala**라는 이름으로 저장합니다.

#### Scala 프로그램 빌드
이 섹션에서는 <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a>(또는 sbt)을 사용하여 scala 프로그램을 빌드합니다. sbt를 사용하려면 Java 1.6 이상이 필요하므로 이 섹션을 계속 진행하기 전에 올바른 버전의 Java가 설치되어 있는지 확인합니다.

1. http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html에서 sbt를 설치합니다.
2. **SimpleScalaApp**이라는 폴더를 만들고 이 폴더 내에 **simple.sbt**라는 파일을 만듭니다. 이 구성 파일에는 Scala 버전, 라이브러리 종속성 등에 대한 정보가 포함됩니다. simple.sbt 파일에 다음을 붙여 넣고 파일을 저장합니다.


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE] 파일의 빈 줄을 그대로 유지해야 합니다.


3. **SimpleScalaApp** 폴더 아래에 디렉터리 구조 **\\src\\main\\scala**를 만들고 앞에서 \\src\\main\\scala 폴더에 만든 Scala 프로그램(**SimpleApp.scala**)을 붙여 넣습니다.
4. 명령 프롬프트를 열고 SimpleScalaApp 디렉터리로 이동한 후 다음 명령을 입력합니다.


		sbt package


	응용 프로그램이 컴파일되면 루트 SimpleScalaApp 폴더 내의 **\\target\\scala-2.10** 디렉터리 아래에 **simpleapp\_2.10-1.0.jar** 파일이 만들어집니다.


#### 클러스터에서 작업 실행
이 섹션에서는 Spark가 설치된 클러스터에 원격으로 연결한 다음 SimpleScalaApp 프로젝트의 대상 폴더를 복사합니다. 그런 다음 **spark-submit** 명령을 사용하여 클러스터에서 작업을 제출합니다.

1. Spark가 설치된 클러스터에 원격으로 연결합니다. SimpleApp.scala 프로그램을 작성 및 빌드한 컴퓨터에서 **SimpleScalaApp\\target** 폴더를 복사하여 클러스터의 위치에 붙여 넣습니다.
2. RDP 세션의 데스크톱에서 Hadoop 명령줄을 열고 **target** 폴더를 붙여 넣은 위치로 이동합니다.
3. 다음 명령을 입력하여 SimpleApp.scala 프로그램을 실행합니다.


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 프로그램 실행이 완료되면 콘솔에 출력이 표시됩니다.


		Lines with a: 21374, Lines with b: 11430

## Azure PowerShell을 사용하여 Spark 설치

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight용 Azure PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 창을 열고 다음 변수를 선언합니다.

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. 사용할 기본 저장소 및 클러스터의 노드와 같은 구성 값을 지정합니다.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. **Add-AzureHDInsightScriptAction** cmdlet을 사용하여 클러스터 구성에 스크립트 작업을 추가합니다. 나중에 클러스터가 생성되는 중에 스크립트 작업이 실행됩니다.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	**Add-AzureHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">매개 변수</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">정의</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">구성</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">스크립트 작업 정보가 추가되는 구성 개체입니다</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트 작업의 이름입니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 HeadNode(헤드 노드에 설치) 또는 DataNode(데이터 노드에 설치)입니다. 두 값 중 하나 또는 모두 사용할 수 있습니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">실행되는 스크립트의 URI를 지정합니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">매개 변수</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트에 필요한 매개 변수입니다. 이 항목에서 사용된 샘플 스크립트에는 매개 변수가 필요하지 않으므로 위의 코드 조각에는 이 매개 변수가 없습니다.
	</td></tr>
	</table>

4. 마지막으로 Spark가 설치된 사용자 지정된 클러스터를 만들기 시작합니다.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

## PowerShell을 사용하여 Spark 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)을 참조하세요.

## .NET SDK을 사용하여 Spark 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)을 참조하세요.


## 참고 항목

- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만듭니다.
- [HDInsight에서 Apache Spark 시작](hdinsight-apache-spark-jupyter-spark-sql.md): HDInsight에서 Spark를 시작합니다.
- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정합니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md): 스크립트 작업 스크립트를 개발합니다.
- [HDInsight 클러스터에 R 설치][hdinsight-install-r] - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 R을 설치하고 사용하는 방법에 대한 지침을 제공합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.
- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.
- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md

<!---HONumber=AcomDC_0914_2016-->