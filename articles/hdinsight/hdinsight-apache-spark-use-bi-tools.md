<properties 
	pageTitle="HDInsight에서 Apache Spark와 함께 BI 도구 사용 | Microsoft Azure" 
	description="Apache Spark에서 노트북을 사용하여 원시 데이터에 대한 스키마를 만들어 하이브 테이블로 저장한 후 하이브 테이블에서 데이터 분석을 위해 BI 도구를 사용하는 방법에 대한 단계별 지침입니다." 
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


# HDInsight Linux에서 Apache Spark 클러스터와 함께 BI 도구 사용

Azure HDInsight에서 Apache Spark를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

* 원시 샘플 데이터를 가져와서 하이브 테이블로 저장
* Power BI 및 Tableau와 같은 BI 도구를 사용하여 데이터를 분석하고 시각화합니다.

> [AZURE.NOTE] 이 자습서는 Azure HDInsight에서 만든 Spark 1.5.2 클러스터에만 적용할 수 있습니다.

이 자습서는 HDInsight에서 만드는 Spark(Linux) 클러스터에서 Jupyter 노트북으로 사용할 수도 있습니다. Notebook 환경을 통해 Notebook 자체에서 Python 코드 조각을 실행할 수 있습니다. Notebook 내에서 자습서를 수행하려면 Spark 클러스터를 만들고 Jupyter Notebook(`https://CLUSTERNAME.azurehdinsight.net/jupyter`)을 시작한 다음 **Python** 폴더 아래의 Notebook **HDInsight.ipynb에서 Apache Spark와 함께 BI 도구 사용**을 실행합니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
- Microsoft Spark ODBC 드라이버가 설치된 컴퓨터(Tableau 작업할 HDInsight에서 Spark 필요). [여기](http://go.microsoft.com/fwlink/?LinkId=616229)에서 드라이버를 설치할 수 있습니다.
- [Power BI](http://www.powerbi.com/) 또는 [Tableau Desktop](http://www.tableau.com/products/desktop)과 같은 BI 도구. [http://www.powerbi.com/](http://www.powerbi.com/)에서 Power BI의 미리 보기 구독을 무료로 받을 수 있습니다.

##<a name="hivetable"></a>하이브 테이블로 원시 데이터 저장

이 섹션에서는 HDInsight에서 Apache Spark 클러스터와 연결된 [Jupyter](https://jupyter.org) Notebook을 사용하여 원시 샘플 데이터를 처리하고 하이브 테이블로 저장하는 작업을 실행합니다. 샘플 데이터는 기본적으로 모든 클러스터에서 사용 가능한 .csv 파일(hvac.csv)입니다.

데이터를 하이브 테이블로 저장한 후에는 다음 섹션에서 Power BI 및 Tableau와 같은 BI 도구를 사용하여 하이브 테이블에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

	> [AZURE.NOTE] 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

	![새 Jupyter 노트북 만들기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "새 Jupyter 노트북 만들기")

3. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

	![노트북에 대한 이름 제공](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "노트북에 대한 이름 제공")

4. PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 이 시나리오에 필요한 형식을 가져와 시작할 수 있습니다. 그렇게 하려면 셀에 커서를 놓고 **Shift + Enter**를 누릅니다.

		from pyspark.sql import *
		
	
5. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들면 샘플 데이터 파일인 **hvac.csv**가 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** 아래 연결된 저장소 계정에 복사됩니다.

	빈 셀에서 다음 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 이 코드 조각은 **hvac**라는 하이브 테이블에 데이터로 등록됩니다.


		# Create an RDD from sample data
		hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = sqlContext.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. 테이블이 성공적으로 만들어졌는지 확인합니다. `%%sql` 매직을 사용하여 Hive 쿼리를 직접 실행할 수 있습니다. `%%sql` 매직 및 기타 PySpark 커널에서 사용 가능한 매직에 대한 자세한 내용은 [Spark HDInsight 클러스터와 함께 Jupyter Notebook에서 사용 가능한 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)을 참조하세요.

		%%sql
		SHOW TABLES

	다음과 유사한 결과가 표시됩니다.

		+-----------+---------------+
		|isTemporary|tableName		| 
		+-----------+---------------+
		|       true|hvactemptable  |
		|      false|hivesampletable|
		|      false|hvac			|
		+-----------+---------------+


	**isTemporary** 열 아래 false를 포함하는 테이블만 Metastore에 저장되는 하이브 테이블이며, BI 도구에서 액세스 가능합니다. 이 자습서에서는 방금 만든 **hvac** 테이블에 연결합니다.

6. 테이블에 원하는 데이터가 들어 있는지 확인합니다. Notebook의 빈 셀에서 다음 코드 조각을 복사하고 **Shift + Enter**를 누릅니다.

		%%sql
		SELECT * FROM hvac LIMIT 10
	
7. 이제 Notebook을 종료하여 리소스를 해제할 수 있습니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **Close and Halt**를 클릭합니다. 그러면 Notebook이 종료되고 닫힙니다.

##<a name="powerbi"></a>Power BI를 사용하여 하이브 테이블의 데이터 분석

데이터를 하이브 테이블로 저장한 후에는 Power BI를 사용하여 데이터를 연결하고 시각화하여 보고서, 대시보드 등을 만들 수 있습니다.

1. [Power BI](http://www.powerbi.com/)에 로그인합니다.

2. 시작 화면에서 **데이터베이스 및 기타**를 클릭합니다.

	![Power BI로 데이터 가져오기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Power BI로 데이터 가져오기")

3. 그 다음 화면에서 **Azure HDInsight의 Spark**를 클릭한 다음 **연결**을 클릭합니다. 메시지가 표시되면 클러스터 URL(`mysparkcluster.azurehdinsight.net`) 및 자격 증명을 입력하여 클러스터에 연결합니다.

	연결이 설정되면 Power BI는 HDInsight의 Spark 클러스터에서 데이터 가져오기를 시작합니다.

5. Power BI가 데이터를 가져와서 **데이터 집합** 제목 아래에 새 Spark 데이터 집합을 추가합니다. 데이터 집합을 클릭하여 새 워크시트를 열고 데이터를 시각화합니다. 워크시트를 보고서로 저장할 수도 있습니다. 워크시트를 저장하려면 **파일** 메뉴에서 **저장**을 클릭합니다.

	![Power BI 대시보드에서 Spark 타일](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Power BI 대시보드에서 Spark 타일")

6. 오른쪽 **필드** 목록에는 이전에 만든 **hvac** 테이블이 나열됩니다. 이전에 노트북에서 정의한 대로 테이블의 필드를 보려면 테이블을 확장합니다.

	  ![하이브 테이블 나열](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "하이브 테이블 나열")

7. 각 건물에 대한 대상 온도와 실제 온도 간의 차이를 보여주는 시각화를 빌드합니다. **영역 차트**(빨간색 상자)를 선택하여 데이터를 시각화합니다. 축을 정의하려면 **BuildingID** 필드를 **축** 아래에, **ActualTemp**/**TargetTemp** 필드를 **값** 아래에 끌어서 놓습니다.

	![시각화 만들기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "시각화 만들기")


8. 기본적으로 시각화에서는 **ActualTemp**및**TargetTemp**에 대한 합계를 보여 줍니다. 두 필드 모두에 대해 드롭다운 메뉴에서 **평균**을 선택하여 두 건물에 대한 실제 및 대상 온도의 평균을 얻습니다.

	![시각화 만들기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. 사용자 데이터 시각화는 다음과 유사해야 합니다. 커서를 시각화 위로 이동하면 관련 데이터와 함께 도구 설명이 나타납니다.

	![시각화 만들기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. 최상위 메뉴에서 **저장**을 클릭하고 보고서 이름을 제공합니다. 시각화를 고정할 수도 있습니다. 시각화를 고정하면 시각화가 대시보드에 저장되어 최신 값을 한 눈에 추적할 수 있습니다.

	동일한 데이터 집합에 대해 시각화를 원하는 만큼 추가하고 대시보드에 고정하여 데이터에 대한 스냅숏을 얻을 수 있습니다. 또한 직접 연결을 통해 HDInsight의 Spark 클러스터는 Power BI에 연결됩니다. 따라서 Power BI는 클러스터로부터 항상 최신 상태를 유지하므로 데이터 집합에 대한 새로 고침을 예약하지 않아도 됩니다.

##<a name="tableau"></a>Tableau Desktop을 사용하여 하이브 테이블에서 데이터 분석
	
1. Tableau Desktop을 실행합니다. 왼쪽 창의 연결할 서버 목록에서 **Spark SQL**을 클릭합니다. 왼쪽 창에 Spark SQL이 기본적으로 표시되지 않으면 **추가 서버**를 클릭하여 찾을 수 있습니다.

2. Spark SQL 연결 대화 상자에서 아래와 같이 값을 제공하고 **확인**을 클릭합니다.

	![Spark 클러스터에 연결](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Spark 클러스터에 연결")

	컴퓨터에 **Microsoft Spark ODBC 드라이버**를 설치한 경우에만 인증 드롭다운 목록에 [Microsoft Azure HDInsight Service](http://go.microsoft.com/fwlink/?LinkId=616229)가 옵션으로 표시됩니다.

3. 다음 화면의 **스키마** 드롭다운에서 **찾기** 아이콘을 클릭하고 **기본값**을 클릭합니다.

	![스키마 찾기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "스키마 찾기")

4. **테이블** 필드에서 **찾기** 아이콘을 다시 클릭하면 클러스터에 사용 가능한 모든 하이브 테이블이 나열됩니다. 이전에 Notebook을 사용하여 만든 **hvac** 테이블이 표시됩니다.

	![테이블 찾기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "테이블 찾기")

5. 테이블을 오른쪽 상단에 있는 상자에 끌어다 놓습니다. Tableau에서 데이터를 가져오고 빨간색 상자로 강조하여 스키마를 표시합니다.

	![Tableau에 테이블 추가](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Tableau에 테이블 추가")

6. 왼쪽 아래에서 **Sheet1** 탭을 클릭합니다. 날짜별로 모든 건물에 대한 대상 및 실제 온도 평균을 보여 주는 시각화를 만듭니다. **날짜** 및 **건물 ID**를 **열**로, **실제 온도**/**대상 온도**를 **행**으로 끌어옵니다. **표시** 아래에서 **영역**을 선택하여 영역 맵 시각화를 사용합니다.

	 ![시각화를 위한 필드 추가](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "시각화를 위한 필드 추가")

7. 기본적으로 온도 필드가 집계로 표시됩니다. 대신, 평균 온도를 표시하려면 아래 표시된 것과 같이 드롭다운에서 이 작업을 하면 됩니다.

	![온도 평균 구하기](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "온도 평균 구하기")

8. 또한 한 온도 맵을 다른 온도 맵 위에 겹쳐 놓아 대상 및 실제 온도 간의 차이를 보다 잘 파악할 수도 있습니다. 마우스를 하단 영역 맵의 모서리로 이동하면 빨간색 원으로 강조 표시된 핸들 모양이 나타납니다. 맵을 위쪽의 다른 맵으로 끌어온 후 빨간색 사각형으로 강조 표시된 모양이 표시되면 마우스를 놓습니다.

	![맵 병합](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "맵 병합")

	 데이터 시각화가 다음과 같이 변경됩니다.

	![시각화](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "시각화")
	 
9. **저장**을 클릭하여 워크시트를 저장합니다. 대시보드를 만들고 시트를 하나 이상 추가할 수도 있습니다.

##<a name="seealso"></a>참고 항목

* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

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