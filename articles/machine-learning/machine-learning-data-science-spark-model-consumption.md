<properties
	pageTitle="Spark에서 만든 기계 학습 모델 점수 매기기 | Microsoft Azure"
	description="Azure Blob 저장소(WASB)에 저장된 학습 모델의 점수를 매기는 방법."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="deguhath;bradsev;gokuma" />

# Spark에서 만든 기계 학습 모델 점수 매기기 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

이 토픽에서는 Spark MLlib를 사용하여 만들어진 Azure Blob Storage(WASB)에 저장된 기계 학습(ML) 모델을 로드하는 방법 및 WASB에도 저장된 데이터 집합을 사용하여 해당 모델의 점수를 매기는 방법을 설명합니다. 입력 데이터를 전처리하고 MLlib 도구 키트의 인덱싱 및 인코딩 기능을 사용하여 기능을 변환하는 방법, 그리고 ML 모델에서 점수를 매기기 위한 입력으로 사용할 수 있는 레이블이 지정된 점수 데이터 개체를 만드는 방법을 보여 줍니다. 점수 매기기에 사용되는 모델은 선형 회귀, 로지스틱 회귀, 임의 포리스트 모델 및 점진적 향상 트리 모델을 포함합니다.


## 필수 조건

1. Azure 계정과 HDInsight Spark가 필요합니다. 이 연습을 완료하려면 HDInsight 3.4 Spark 1.6 클러스터가 필요합니다. 이러한 요구 사항, 여기에 사용한 NYC 2013 Taxi 데이터에 대한 설명 및 Spark 클러스터의 Jupyter Notebook에서 코드를 실행하는 방법에 관한 지침은 [Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요](machine-learning-data-science-spark-overview.md)를 참조하세요. 이 토픽에서 코드 샘플을 포함하는 **machine-learning-data-science-spark-data-exploration-modeling.ipynb** Notebook은 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)에서 사용할 수 있습니다.

2. 또한 [Spark로 데이터 탐색 및 모델링](machine-learning-data-science-spark-data-exploration-modeling.md) 항목을 통해 작업하여 여기서 점수를 매길 기계 학습 모델을 만들어야 합니다.


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## 설정: 저장소 위치, 라이브러리 및 사전 설정 Spark 컨텍스트

Spark는 Azure 저장소 Blob(WASB)를 읽고 쓸 수 있습니다. 따라서 Spark 및 WASB에 다시 저장된 결과를 사용하여 해당 저장소에 저장된 기존 데이터를 처리할 수 있습니다.

모델 또는 파일을 WASB에저장하려면 경로를 올바르게 지정해야 합니다. *"wasb//"*로 시작하는 경로를 사용하여 Spark 클러스터에 연결된 기본 컨테이너를 참조할 수 있습니다. 다음 코드 샘플은 읽을 데이터의 위치 및 모델 출력이 저장될 모델 저장소 디렉터리에 대한 경로를 지정합니다.


### WASB의 저장소 위치에 대 한 디렉터리 경로를 설정합니다.

모델 저장 위치: "wasb:///user/remoteuser/NYCTaxi/Models". 이 경로를 올바르게 설정하지 않으면 점수 매기기를 위한 모델이 로드되지 않습니다.

점수를 매긴 결과가 저장된 위치: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". 폴더에 대한 경로가 올바르지 않으면 결과가 해당 폴더에 저장되지 않습니다.


>[AZURE.NOTE] **machine-learning-data-science-spark-data-exploration-modeling.ipynb** Notebook의 마지막 셀의 출력에서 파일 경로 위치를 복사하여 이 코드의 자리 표시자에 붙여넣을 수 있습니다.


디렉터리 경로를 설정하는 코드는 다음과 같습니다.

	# LOCATION OF DATA TO BE SCORED (TEST DATA)
	taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
	
	# SET SCORDED RESULT DIRECTORY PATH
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
	
	# FILE LOCATIONS FOR THE MODELS TO BE SCORED
	logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
	linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
	randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
	randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
	BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
	BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

	# RECORD START TIME
	import datetime
	datetime.datetime.now()

**출력:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)


### 라이브러리 가져오기

Spark 컨텍스트를 설정하고 다음 코드를 사용하여 필요한 라이브러리 가져오기

	#IMPORT LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	import matplotlib
	import matplotlib.pyplot as plt
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime


### 미리 설정된 Spark 컨텍스트 및 PySpark 매직

Jupyter Notebook에 제공되는 PySpark 커널에는 미리 설정된 컨텍스트가 있으므로 개발 중인 응용 프로그램으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요가 없습니다. 즉, Spark 또는 Hive 컨텍스트를 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.

- sc - Spark용
- sqlContext - Hive용

PySpark 커널은 특수 명령인 일부 미리 정의된 "매직"을 제공하며 이러한 매직은 %%를 사용하여 호출할 수 있습니다. 이러한 코드 샘플에 사용되는 다음과 같은 두 가지 명령이 있습니다.

- **%%local** 다음 줄의 코드는 로컬로 실행됩니다. 코드는 유효한 Python 코드여야 합니다.
- **%%sql -o <variable name>** sqlContext에 대해 Hive 쿼리를 실행합니다. -o 매개 변수가 전달된 경우 쿼리 결과가 %%local Python 컨텍스트에서 Pandas 데이터 프레임으로 유지됩니다.
 

Jupyter Notebook의 커널 및 제공되는 %%(예: %%local)로 호출할 수 있는 미리 정의된 "매직"에 대한 자세한 내용은 [HDInsight의 HDInsight Spark Linux 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요.


## 데이터 수집 및 정리된 데이터 프레임 만들기

이 섹션에는 점수를 매길 데이터를 수집하는 데 필요한 일련의 작업에 대한 코드가 포함되어 있습니다. 택시 여정 및 요금 파일(.tsv 파일로 저장됨)의 연결된 0.1% 샘플을 읽고 데이터를 서식 지정한 다음 정리된 데이터 프레임을 만듭니다.

택시 여정 및 요금 파일은 [실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-walkthrough.md) 항목에 제공된 절차를 기반으로 연결됩니다.

	# INGEST DATA AND CREATE A CLEANED DATA FRAME

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_test_file = sc.textFile(taxi_test_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	    
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_test_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# CREATE DATA FRAME
	taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_cleaned.cache()
	taxi_df_test_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_test_cleaned.registerTempTable("taxi_test")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 46.37초


## Spark에서 점수를 매기도록 데이터 준비 

이 섹션에서는 범주 기능을 MLlib가 감독하는 학습 알고리즘에서 분류 및 회귀에 사용하도록 준비하기 위해 인덱싱, 인코딩 및 규모 조정하는 방법을 보여 줍니다.

### 기능 변환: 범주 기능을 점수 매기기 모델에 입력하기 위해 인덱싱 및 인코딩 

이 섹션에서는 `StringIndexer`를 사용하여 범주 데이터를 인덱싱하고 `OneHotEncoder` 입력을 가진 기능을 모델로 인코딩하는 방법을 보여 줍니다.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer)는 레이블의 문자열 열을 레이블 인덱스의 열로 인코딩합니다. 인덱스는 레이블 빈도 순으로 정렬됩니다.

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder)는 레이블 인덱스의 열을 단 하나의 값을 가진 이진 벡터의 열에 매핑합니다. 이 인코딩을 사용하여 로지스틱 회귀 등과 같은 연속 값을 가진 기능을 예상하는 알고리즘을 범주 기능에 적용할 수 있습니다.
	
	#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_test 
	"""
	taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_with_newFeatures.cache()
	taxi_df_test_with_newFeatures.count()
	
	# INDEX AND ONE-HOT ENCODING
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_test_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND ENCODE TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 5.37초


### 모델에 입력하기 위해 기능 배열을 사용하여 RDD 개체 만들기

이 섹션은 범주 텍스트 데이터를 RDD 개체로 인덱싱하고 이 개체를 사용하여 MLlib 로지스틱 회귀 및 트리 기반 모델을 학습하고 시험할 수 있도록 원 핫 인코딩하는 방법을 보여 주는 코드를 포함하고 있습니다. 인덱싱된 데이터는 [RDD(Resilient Distributed Dataset)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) 개체에 저장됩니다. 이들은 Spark의 기본 추상화입니다. RDD 개체는 Spark와 함께 병렬로 작업을 수행할 수 있으며 변경할 수 없고 분할된 요소 컬렉션입니다.

또한 광범위한 기계 학습 모델을 학습하기 위한 인기 있는 알고리즘인 SGD(Stochastic Gradient Descent)와 함께 선형 회귀에 사용하기 위해 MLlib에서 제공하는 `StandardScalar`를 사용하여 데이터를 규모 조정하는 방법을 보여 주는 코드를 포함하고 있습니다. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler)는 기능을 단위 분산으로 규모 조정하기 위해 사용됩니다. 데이터 정규화라고도 하는 기능 규모 조정은 목적 함수에서 폭 넓게 분배된 값을 가진 기능에 과도한 가중치를 부여하지 않도록 합니다.


	# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# IMPORT LIBRARIES
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	from numpy import array
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features

	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
	oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
	
	# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
	indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
	oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
	
	# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
	scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
	oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
	
	# CACHE RDDS IN MEMORY
	indexedTESTbinary.cache();
	oneHotTESTbinary.cache();
	indexedTESTreg.cache();
	oneHotTESTreg.cache();
	oneHotTESTregScaled.cache();
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**출력:**

위의 셀을 실행하는 데 걸린 시간: 11.72초


## 로지스틱 회귀 모델을 사용하여 점수를 매기고 출력을 blob에 저장

이 섹션의 코드는 Azure blob 저장소에 저장된 로지스틱 회귀 모델을 로드하고 이를 사용하여 택시 여정에 대한 팁을 지불했는지 여부를 예측하고 표준 분류 메트릭을 사용하여 점수를 매긴 다음 결과를 blob 저장소에 저장 및 표시하는 방법을 보여 줍니다. 점수가 매겨진 결과는 RDD 개체에 저장됩니다.


	# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	## LOAD SAVED MODEL
	savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
	predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
	
	## SAVE SCORED RESULTS (RDD) TO BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
	dirfilename = scoredResultDir + logisticregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**출력:**

위의 셀을 실행하는 데 걸린 시간: 19.22초


## 선형 회귀 모델 점수 매기기

우리는 [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD)를 사용하여 최적화를 위해 SGD(Stochastic Gradient Descent)를 통해 지불한 팁 금액을 예측하는 선형 회귀 모델을 학습했습니다.

이 섹션의 코드는 Azure blob 저장소에서 선형 회귀 모델을 로드하고 규모 조정된 변수를 사용하여 점수를 매긴 다음 결과를 blob에 다시 저장하는 방법을 보여 줍니다.

	#SCORE LINEAR REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	#LOAD LIBRARIES​
	from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
	
	# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
	savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
	predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = scoredResultDir + linearregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL​
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**출력:**

위의 셀을 실행하는 데 걸린 시간: 16.63초


## 분류 및 회귀 임의 포리스트 모델 점수 매기기

이 섹션의 코드는 Azure blob 저장소에 저장된 분류 및 회귀 임의 포리스트 모델을 로드하고 표준 분류자 및 회귀 측정값을 사용하여 해당 성능의 점수를 매긴 다음 결과를 blob 저장소에 다시 저장하는 방법을 보여 줍니다.

[임의 포리스트](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests)는 결정 트리의 결합체입니다. 즉, 과잉 맞춤을 줄이기 위해 많은 결정 트리를 결합합니다. 임의 포리스트는 범주 기능을 처리하고 다중 클래스 분류 설정으로 확장하고 기능 규모 조정을 요구하지 않으며 비선형 및 기능 상호 작용을 캡처할 수 있습니다. 임의 포리스트는 분류 및 회귀를 위해 매우 성공적인 기계 학습 모델 중 하나입니다.

[spark.mllib](http://spark.apache.org/mllib/)는 연속 기능과 범주 기능을 둘 다 사용하여 이진 및 다중 클래스 분류와 회귀에 대해 임의 포리스트를 지원합니다.

	# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES	
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfregressionfilename;
	predictions.saveAsTextFile(dirfilename)

	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**출력:**

위의 셀을 실행하는 데 걸린 시간: 31.07초


## 분류 및 회귀 점진적 향상 트리 모델 점수 매기기

이 섹션의 코드는 Azure blob 저장소에서 점진적 향상 트리 모델을 로드하고 표준 분류자 및 회귀 측정값을 사용하여 해당 성능의 점수를 매긴 다음 결과를 blob 저장소에 다시 저장하는 방법을 보여 줍니다.

**spark.mllib**는 연속 기능과 범주 기능을 둘 다 사용하여 이진 분류 및 회귀에 대해 GBT를 지원합니다.

[그라데이션 향상 트리](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)(GBT)는 결정 트리의 결합체입니다. GBT는 기능 손실을 최소화하기 위해 결정 트리를 반복적으로 학습합니다. GBT는 범주 기능을 처리하고 기능 규모 결정을 요구하지 않으며 비선형 기능 상호 작용을 캡처할 수 있습니다. 또한 다중 클래스 분류 설정에도 사용할 수 있습니다.


	# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	#LOAD AND SCORE THE MODEL
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	# LOAD AND SCORE MODEL 
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btregressionfilename;
	predictions.saveAsTextFile(dirfilename)


	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
	
**출력:**

위의 셀을 실행하는 데 걸린 시간: 14.6초


## 메모리에서 개체 정리 및 점수가 매겨진 파일 위치 인쇄

	# UNPERSIST OBJECTS CACHED IN MEMORY
	taxi_df_test_cleaned.unpersist()
	indexedTESTbinary.unpersist();
	oneHotTESTbinary.unpersist();
	indexedTESTreg.unpersist();
	oneHotTESTreg.unpersist();
	oneHotTESTregScaled.unpersist();


	# PRINT OUT PATH TO SCORED OUTPUT FILES
	print "logisticRegFileLoc: " + logisticregressionfilename;
	print "linearRegFileLoc: " + linearregressionfilename;
	print "randomForestClassificationFileLoc: " + rfclassificationfilename;
	print "randomForestRegFileLoc: " + rfregressionfilename;
	print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
	print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**출력:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22\_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22\_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23\_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23\_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23\_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23\_56.860740.txt



## 웹 인터페이스를 통해 Spark 모델 사용

Spark는 Livy라는 구성 요소와의 REST 인터페이스를 통해 배치 작업 또는 대화형 쿼리를 원격으로 제출하는 메커니즘을 제공합니다. Livy는 HDInsight Spark 클러스터에서 기본적으로 사용하도록 설정되어 있습니다. Livy에 대한 자세한 내용은 [Livy를 사용하여 원격으로 Spark 작업 제출](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md)을 참조하세요.

Livy를 사용하면 Azure blob에 저장된 파일의 점수를 일괄적으로 매긴 다음 결과를 다른 blob에 쓰는 작업을 원격으로 제출할 수 있습니다. 이 작업을 수행하려면[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py)에서 Python 스크립트를 Spark 클러스터의 BLOB에 업로드합니다. **Microsoft Azure Storage Explorer** 또는 **AzCopy** 등과 같은 도구를 사용하여 스크립트를 클러스터 BLOB에 복사합니다. 이 경우 우리는 스크립트를 ***wasb:///example/python/ConsumeGBNYCReg.py***에 업로드했습니다.


>[AZURE.NOTE] 필요한 액세스 키는 Spark 클러스터와 연결된 저장소 계정용 포털에서 찾을 수 있습니다.


이 스크립트는 이 위치에 업로드된 후 분산 컨텍스트의 Spark 클러스터 내에서 실행됩니다. 모델을 로드하고 모델을 기반으로 하는 입력 파일에 대해 예측을 실행합니다.

Livy에서 간단한 HTTPS/REST 요청을 만들어 이 스크립트를 원격으로 호출할 수 있습니다. 다음은 Python 스크립트를 원격으로 호출하는 HTTP 요청을 생성하는 curl 명령입니다. CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME을 Spark 클러스터에 적절한 값으로 바꾸어야 합니다.


	# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

원격 시스템에서 임의의 언어를 사용하여 기본 인증을 가진 간단한 HTTPS 호출을 만들어 Livy를 통해 Spark 작업을 호출할 수 있습니다.


>[AZURE.NOTE] 이 HTTP 호출을 만들 때 Python 요청을 사용하면 편리할 수 있지만 이 기능은 현재 Azure Functions에 기본적으로 설치되어 있지 않습니다. 따라서 이전 HTTP 라이브러리가 대신 사용됩니다.


HTTP 호출을 위한 Python 코드는 다음과 같습니다.

	#MAKE AN HTTPS CALL ON LIVY. 

	import os

	# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
	import httplib, urllib, base64
	
	# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
	host = '<spark cluster name>.azurehdinsight.net:443'
	username='<username>'
	password='<password>'
	
	#AUTHORIZATION
	conn = httplib.HTTPSConnection(host)
	auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
	headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
	
	# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
	# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
	r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
	response = conn.getresponse().read()
	print(response)
	conn.close()


또한 이 Python 코드를 [Azure Functions](https://azure.microsoft.com/documentation/services/functions/)에 추가하여 BLOB의 타이머, 생성 또는 업데이트 등과 같은 여러 이벤트를 기반으로 BLOB의 점수를 매기는 Spark 작업 제출을 트리거할 수 있습니다.

코드 없는 클라이언트 환경을 선호하는 경우 [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/)를 사용하여 **Logic Apps Designer**에서 HTTP 작업을 정의하고 해당 매개변수를 설정하여 Spark 배치 점수 매기기를 호출합니다.

- Azure Portal에서 **+새로 만들기** -> **웹 + 모바일** -> **Logic App**을 선택하여 새 Logic App을 만듭니다.
- Logic App 및 앱 서비스 계획의 이름을 입력하여 **Logic Apps Designer**를 표시합니다.
- HTTP 작업을 선택하고 다음 그림과 같은 매개 변수를 입력합니다.

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## 다음 작업 

**교차 유효성 검사 및 하이퍼 매개 변수 비우기**: 교차 유효성 검사 및 하이퍼 매개 변수 비우기를 사용하여 모델을 학습하는 방법은 [Spark를 사용한 고급 데이터 탐색 및 모델링](machine-learning-data-science-spark-advanced-data-exploration-modeling.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->