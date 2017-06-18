<properties 
	pageTitle="HDInsight에서 Apache Spark를 사용하여 기계 학습 응용 프로그램 빌드 | Microsoft Azure" 
	description="기계 학습 응용 프로그램을 빌드하기 위해 Apache Spark와 함께 노트북을 사용하는 방법에 대한 단계별 지침" 
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
	ms.date="09/14/2016" 
	ms.author="nitinme"/>


# HDInsight Linux에서 Apache Spark 클러스터를 실행하는 기계 학습 응용 프로그램 빌드

Apache Spark 클러스터를 사용하여 HDInsight에서 기계 학습 응용 프로그램을 빌드하는 방법을 알아봅니다. 이 문서에서는 응용 프로그램을 빌드하고 테스트할 클러스터와 함께 사용할 수 있는 Jupyter Python 노트북을 사용하는 방법을 보여 줍니다. 응용 프로그램은 기본적으로 모든 클러스터에서 사용할 수 있는 샘플 HVAC.csv 데이터를 사용합니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

##<a name="data"></a>데이터 표시

응용 프로그램 빌드를 시작하기 전에 데이터 구조 및 데이터에 대해 수행할 분석 종류를 알려주세요.

이 문서에서는 HDInsight 클러스터와 연결되는 Azure Storage 계정에서 사용할 수 있는 **HVAC.csv** 데이터 파일을 샘플로 사용합니다. 이 파일은 저장소 계정에서 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**에 있습니다. CSV 파일을 다운로드하고 열어서 데이터의 스냅숏을 가져옵니다.

![HVAC 데이터 스냅숏](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "HVAC 데이터의 스냅숏")

데이터는 HVAC 시스템이 설치된 건물의 대상 온도 및 실제 온도를 보여 줍니다. **System** 열은 시스템 ID를 나타내고 **SystemAge** 열은 건물에서 HVAC 시스템이 몇 년 전에 설치되었는지를 나타낸다고 가정해 보겠습니다.

시스템 ID 및 시스템 연수가 지정된 상태에서 대상 온도를 기반으로 건물이 더 덥거나 추운지를 예측하기 위해 이 데이터를 사용합니다.

##<a name="app"></a>Spark MLlib를 사용하여 기계 학습 응용 프로그램 작성

이 응용 프로그램에서 문서 분류를 수행하는 데 Spark ML 파이프라인을 사용합니다. 파이프라인에서 기능 벡터 및 레이블을 사용하여 문서를 단어로 분할하고 단어를 숫자 기능 벡터로 변환하며 마지막으로 예측 모델을 빌드합니다. 다음 단계에 따라 응용 프로그램을 만듭니다.

1. [Azure 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

	> [AZURE.NOTE] 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

	![새 Jupyter 노트북 만들기](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "새 Jupyter 노트북 만들기")

3. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

	![노트북에 대한 이름 제공](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "노트북에 대한 이름 제공")

3. PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 이 시나리오에 필요한 형식을 가져와 시작할 수 있습니다. 빈 셀에 다음 코드 조각을 붙여넣은 다음 **Shift + Enter**를 누릅니다.

		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		
		import os
		import sys
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		
	 
4. 이제 데이터(hvac.csv)를 로드하고 분석하고 모델 학습에 사용해야 합니다. 이를 위해 건물의 실제 온도가 대상 온도보다 큰지를 확인하는 함수를 정의합니다. 실제 온도가 큰 경우 건물이 더운 것이고 값이 **1.0**으로 표시됩니다. 실제 온도가 작은 경우 건물이 추운 것이고 값이 **0.0**으로 표시됩니다.

	빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		
		# List the structure of data for better understanding. Becuase the data will be
		# loaded as an array, this structure makes it easy to understand what each element
		# in the array corresponds to

		# 0 Date
		# 1 Time
		# 2 TargetTemp
		# 3 ActualTemp
		# 4 System
		# 5 SystemAge
		# 6 BuildingID

		LabeledDocument = Row("BuildingID", "SystemInfo", "label")

		# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
		
		def parseDocument(line):
    		values = [str(x) for x in line.split(',')]
    		if (values[3] > values[2]):
        		hot = 1.0
    		else:
        		hot = 0.0        
    
    		textValue = str(values[4]) + " " + str(values[5])
    
    		return LabeledDocument((values[6]), textValue, hot)

		# Load the raw HVAC.csv file, parse it using the function
		data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. tokenizer, hashingTF 및 lr의 세 단계로 구성된 Spark 기계 학습 파이프라인을 구성합니다. 파이프라인의 정의 및 작동 방법에 대한 자세한 내용은 <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 기계 학습 파이프라인</a>을 참조하세요.

	빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. 학습 문서에 파이프라인을 맞춥니다. 빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		model = pipeline.fit(training)

7. 학습 문서를 확인하여 응용 프로그램 진행 상태의 검사점을 설정합니다. 빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		training.show()

	다음과 유사한 결과가 출력됩니다.

		+----------+----------+-----+
		|BuildingID|SystemInfo|label|
		+----------+----------+-----+
		|         4|     13 20|  0.0|
		|        17|      3 20|  0.0|
		|        18|     17 20|  1.0|
		|        15|      2 23|  0.0|
		|         3|      16 9|  1.0|
		|         4|     13 28|  0.0|
		|         2|     12 24|  0.0|
		|        16|     20 26|  1.0|
		|         9|      16 9|  1.0|
		|        12|       6 5|  0.0|
		|        15|     10 17|  1.0|
		|         7|      2 11|  0.0|
		|        15|      14 2|  1.0|
		|         6|       3 2|  0.0|
		|        20|     19 22|  0.0|
		|         8|     19 11|  0.0|
		|         6|      15 7|  0.0|
		|        13|      12 5|  0.0|
		|         4|      8 22|  0.0|
		|         7|      17 5|  0.0|
		+----------+----------+-----+


	돌아가서 원시 CSV 파일에 대한 출력을 확인합니다. 예를 들어 CSV 파일의 첫 번째 행에는 다음 데이터가 있습니다.

	![HVAC 데이터 스냅숏](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "HVAC 데이터의 스냅숏")

	실제 온도가 건물이 춥다는 것을 의미하는 대상 온도보다 얼마나 작은지 확인합니다. 학습 결과에 따르면 첫 행의 **label** 값이 **0.0**이고, 이는 건물이 덥지 않다는 것을 의미합니다.

8.  학습된 모델에 대해 실행할 데이터 집합을 준비합니다. 이렇게 하려면 시스템 ID 및 시스템 연수에서 전달하고(학습 결과에 **SystemInfo**로 표시) 모델이 해당 시스템 ID 및 시스템 연수를 가진 건물이 더울지(1.0으로 표시) 또는 추울지(0.0으로 표시) 예측합니다.

	빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. 마지막으로 테스트 데이터에서 예측을 수행합니다. 빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. 다음과 유사한 결과가 표시됩니다.

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	예측의 첫 번째 행에서 ID는 20이고 25년이라는 시스템 연수를 가진 HVAC 시스템의 경우 건물이 덥습니다(**prediction=1.0**). DenseVector(0.49999)에 대한 첫 번째 값은 예측 0.0에 해당하고 두 번째 값(0.5001)은 예측 1.0에 해당합니다. 출력에서 두 번째 값이 약간만 높더라도 모델은 **prediction=1.0**을 보여 줍니다.

11. 응용 프로그램 실행을 완료한 후 리소스를 해제하도록 노트북을 종료해야 합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **Close and Halt**를 클릭합니다. 그러면 노트북이 종료되고 닫힙니다.
	  	   

##<a name="anaconda"></a>기계 학습에 대한 Anaconda scikit-learn 라이브러리 사용

HDInsight에서 Apache Spark 클러스터에는 Anaconda 라이브러리가 포함되어 있습니다. 기계 학습에 대한 **scikit-learn** 라이브러리도 있습니다. 또한 라이브러리에는 Jupyter 노트북에서 직접 샘플 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 데이터 집합이 포함되어 있습니다. scikit-learn 라이브러리 사용에 대한 예는 [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)를 참조하세요.

##<a name="seealso"></a>참고 항목

* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

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

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0914_2016-->