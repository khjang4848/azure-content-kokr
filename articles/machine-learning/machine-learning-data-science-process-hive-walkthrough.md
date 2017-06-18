<properties
	pageTitle="실행 중인 팀 데이터 과학 프로세스: Hadoop 클러스터 사용 | Microsoft Azure"
	description="HDInsight Hadoop 클러스터를 사용하는 종단 간 시나리오에 팀 데이터 과학 프로세스를 사용하여 공개적으로 사용 가능한 데이터 집합으로 모델을 빌드 및 배포합니다."
	services="machine-learning,hdinsight"
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
	ms.date="09/19/2016"
	ms.author="hangzh;bradsev" />


# 실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용

이 연습에서는 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)를 사용하는 종단 간 시나리오에서 [TDSP(팀 데이터 과학 프로세스)](data-science-process-overview.md)를 사용하여 공개적으로 사용 가능한 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합에서 데이터를 저장, 탐색, 기능 설계, 다운 샘플링합니다. 데이터의 모델은 이진/다중 클래스 분류 및 회귀 예측 작업을 처리하기 위해 Azure 기계 학습으로 빌드됩니다.

데이터 처리에 HDInsight Hadoop 클러스터를 사용하는 유사한 시나리오에서 더 큰 데이터 집합(1TB)을 처리하는 방법을 보여 주는 연습은 [팀 데이터 과학 프로세스 - 1TB 데이터 집합에서 Azure HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-criteo-walkthrough.md)을 참조하세요.

IPython 노트북에서 1TB 데이터 집합을 사용하는 연습의 작업을 수행할 수도 있습니다. 이 방법을 사용하려면 [Hive ODBC 연결을 사용하여 Criteo 연습](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 토픽을 참조해야 합니다.


## <a name="dataset"></a>NYC Taxi Trips 데이터 집합 설명

NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 구성된 약 20GB의 압축된 CSV(쉼표로 구분된 값) 파일(압축되지 않은 경우 약 48GB)입니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 집합으로 제공됩니다.

1. 'trip\_data' CSV 파일은 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip\_fare' CSV 파일은 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime 필드로 구성됩니다.

특정 여정과 관련된 모든 세부 정보를 가져오려면 세 개의 키인 "medallion", "hack\_license" 및 "pickup\_datetime"을 사용하여 조인하면 됩니다.

데이터에 대한 보다 자세한 정보는 잠시 후 Hive 테이블을 저장할 때 살펴봅니다.

## <a name="mltasks"></a>예측 작업의 예제
데이터에 접근할 때 분석을 기반으로 수행할 예측의 종류를 결정하면 프로세스에 포함해야 하는 작업을 명확하게 확인할 수 있습니다. 다음은 이 연습에서 우리가 해결할 예측 문제에 대한 3가지 예제입니다. 예제의 공식은 *tip\_amount*를 기반으로 합니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 즉, *tip\_amount*가 $0보다 크면 지불된 것이며 *tip\_amount*가 $0이면 지불되지 않은 것입니다.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0

2. **다중 클래스 분류**: 여정에 대해 지불된 팁 금액의 범위를 예측합니다. *tip\_amount*를 5개의 bin 또는 클래스로 나눕니다.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.


## <a name="setup"></a>고급 분석용 HDInsight Hadoop 클러스터 설정

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

다음 세 단계를 통해 HDInsight 클러스터를 사용하는 고급 분석용 Azure 환경을 설정할 수 있습니다.

1. [저장소 계정 만들기](../storage/storage-create-storage-account.md):이 저장소 계정은 Azure Blob 저장소에 데이터를 저장하는 데 사용됩니다. HDInsight 클러스터에 사용되는 데이터도 여기에 상주합니다.

2. [고급 분석 프로세스 및 기술을 위한 Azure HDInsight Hadoop 클러스터 사용자 지정](machine-learning-data-science-customize-hadoop-cluster.md). 이 단계에서는 모든 노드에 64비트 Anaconda Python 2.7이 설치된 Azure HDInsight Hadoop 클러스터를 만듭니다. HDInsight 클러스터 사용자 지정하는 동안 기억해야 할 중요한 두 단계가 있습니다.

	* HDInsight 클러스터를 만들 때 1단계에서 만든 저장소 계정을 연결해야 합니다. 이 저장소 계정은 클러스터 내에서 처리되는 데이터에 액세스하는 데 사용됩니다.

	* 클러스터를 만든 후에는 클러스터의 헤드 노드에 대한 원격 액세스를 활성화합니다. **구성** 탭으로 이동하여 **원격 사용**을 클릭합니다. 이 단계에서는 원격 로그인에 사용되는 사용자 자격 증명을 지정합니다.

3. [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md): 이 Azure 기계 학습 작업 영역은 기계 학습 모델을 빌드하는 데 사용됩니다. 이 작업은 초기 데이터 탐색을 완료하고 HDInsight 클러스터를 사용하여 다운 샘플링한 후 처리됩니다.

## <a name="getdata"></a>공용 원본에서 데이터 가져오기

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

해당 공용 위치에서 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합을 가져오려면 [Azure Blob 저장소에서 데이터 이동](machine-learning-data-science-move-azure-blob.md)에 설명된 방법 중 하나를 사용하여 데이터를 컴퓨터에 복사하면 됩니다.

여기에서는 AzCopy를 사용하여 데이터가 포함된 파일을 전송하는 방법을 설명합니다. AzCopy를 다운로드하여 설치하려면 [AzCopy 명령줄 유틸리티 시작](../storage/storage-use-azcopy.md) 지침을 따릅니다.

1. 명령 프롬프트 창에서 *<path_to_data_folder>*를 원하는 대상으로 바꿔 다음 AzCopy 명령을 실행합니다.


		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. 복사가 완료되면 총 24개의 압축된 파일이 선택한 데이터 폴더에 생성됩니다. 로컬 컴퓨터에서 동일한 디렉터리에 다운로드한 파일의 압축을 풉니다. 압축을 푼 파일이 있는 폴더를 적어 둡니다. 이 폴더를 *<path\_to\_unzipped\_data\_files>*라고 합니다.


## <a name="upload"></a>Azure HDInsight Hadoop 클러스터의 기본 컨테이너에 데이터 업로드

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

다음 AzCopy 명령에서 다음 매개 변수를 Hadoop 클러스터를 만들고 데이터 파일의 압축을 풀 때 지정한 실제 값으로 바꿉니다.

* ***&#60;path\_to\_data\_folder>*** - 압축을 푼 데이터 파일이 들어 있는 컴퓨터의 디렉터리(경로와 함께)
* ***&#60;storage account name of Hadoop cluster>*** - HDInsight 클러스터와 연결된 저장소 계정
* ***&#60;default container of Hadoop cluster>*** - 클러스터에서 사용하는 기본 컨테이너. 기본 컨테이너의 이름은 일반적으로 클러스터 자체의 이름과 같습니다. 예를 들어 클러스터가 "abc123.azurehdinsight.net"인 경우 기본 컨테이너는 abc123입니다.
* ***&#60;storage account key>*** - 클러스터에서 사용하는 저장소 계정의 키

명령 프롬프트 또는 컴퓨터의 Windows PowerShell 창에서 다음 두 AzCopy 명령을 실행합니다.

이 명령은 여정 데이터를 Hadoop 클러스터의 기본 컨테이너에 있는 ***nyctaxitripraw*** 디렉터리에 업로드합니다.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

이 명령은 요금 데이터를 Hadoop 클러스터의 기본 컨테이너에 있는 ***nyctaxifareraw*** 디렉터리에 업로드합니다.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

이제 데이터가 Azure Blob 저장소에 있고 HDInsight 클러스터 내에서 사용할 수 있도록 준비됩니다.

## <a name="#download-hql-files"></a>Hadoop 클러스터의 헤드 노드에 로그인하여 예비 데이터 분석 준비

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

예비 데이터 분석 및 데이터 다운 샘플링을 위해 클러스터의 헤드 노드에 액세스하려면 [Hadoop 클러스터의 헤드 노드 액세스](machine-learning-data-science-customize-hadoop-cluster.md#headnode)에 설명된 절차를 따릅니다.

이 연습에서는 주로 SQL과 유사한 쿼리 언어인 [Hive](https://hive.apache.org/)로 작성된 쿼리를 사용하여 예비 데이터 탐색을 수행합니다. Hive 쿼리는 .hql 파일에 저장됩니다. 그런 다음 모델 빌드를 위해 Azure 기계 학습 내에서 사용하도록 이 데이터를 다운 샘플링합니다.

예비 데이터 분석을 위해 클러스터를 준비하려면 [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)에서 관련 Hive 스크립트가 포함된 .hql 파일을 헤드 노드의 로컬 디렉터리(C:\\temp)에 다운로드합니다. 이렇게 하려면 클러스터의 헤드 노드 내에서 **명령 프롬프트**를 열고 다음 두 명령을 실행합니다.

	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

이 두 명령은 이 연습에 필요한 모든 .hql 파일을 헤드 노드의 로컬 디렉터리 ***C:\temp&#92;***에 다운로드합니다.

## <a name="#hive-db-tables"></a>월별로 분할된 Hive 데이터베이스 및 테이블 만들기

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

이제 NYC taxi 데이터 집합에 대한 Hive 테이블을 만들 준비가 완료되었습니다. Hadoop 클러스터 헤드 노드의 바탕 화면에서 ***Hadoop Command Line***을 열고 명령을 입력하여 Hive 디렉터리를 입력합니다.

    cd %hive_home%\bin

>[AZURE.NOTE] **이 연습의 모든 Hive 명령은 위의 Hive bin/ 디렉터리 프롬프트에서 실행합니다. 경로 문제가 자동으로 해결됩니다. "Hive 디렉터리 프롬프트", "Hive bin/ 디렉터리 프롬프트" 및 "Hadoop Command Line"은 이 연습에서 상호 교환적으로 사용되는 용어입니다.**

Hive 디렉터리 프롬프트에서 헤드 노드의 Hadoop 명령줄에 다음 명령을 입력하여 Hive 데이터베이스 및 테이블을 만드는 Hive 쿼리를 제출합니다.

	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

다음은 Hive 데이터베이스 ***nyctaxidb***와 테이블 ***trip*** 및 ***fare***를 만드는 ***C:\\temp\\sample\_hive\_create\_db\_and\_tables.hql*** 파일의 콘텐츠입니다.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double)  
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    pickup_datetime string,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

이 Hive 스크립트는 두 개의 테이블을 만듭니다.

* "trip" 테이블은 각 승객의 여정 세부 정보(운전 기사 정보, 승차 시간, 여정 거리 및 시간)를 포함합니다.
* "fare" 테이블은 요금 세부 정보(요금 금액, 팁 금액, 통행료 및 추가 요금)를 포함합니다.

이러한 절차에 대한 도움이 필요하거나 다른 방법을 조사하려는 경우 [Hadoop 명령줄에서 직접 Hive 쿼리 제출](machine-learning-data-science-process-hive-tables.md#submit) 섹션을 참조하세요.

## <a name="#load-data"></a>분할된 Hive 테이블에 데이터 로드

>[AZURE.NOTE] 이는 일반적으로 **관리자** 작업입니다.

NYC taxi 데이터 집합에는 처리 및 쿼리 시간을 단축하기 위해 사용하는 월별 자연 분할 기능이 있습니다. 아래의 PowerShell 명령(**Hadoop 명령줄**을 사용하여 Hive 디렉터리에서 실행)은 월별로 분할된 "trip" 및 "fare" Hive 테이블에 데이터를 로드합니다.

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

*sample\_hive\_load\_data\_by\_partitions.hql* 파일에는 다음 **LOAD** 명령이 포함되어 있습니다.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

탐색 프로세스에서 사용한 Hive 쿼리 수에는 단일 파티션 또는 두 파티션에서 탐색한 횟수가 포함됩니다. 그러나 이러한 쿼리는 전체 데이터에서 실행될 수 있습니다.

### <a name="#show-db"></a>HDInsight Hadoop 클러스터에서 데이터베이스 표시

HDInsight Hadoop 클러스터에서 만든 데이터베이스를 Hadoop 명령줄 창 내에 표시하려면 Hadoop 명령줄에서 다음 명령을 실행합니다.

	hive -e "show databases;"

### <a name="#show-tables"></a>nyctaxidb 데이터베이스에서 Hive 테이블 표시

nyctaxidb 데이터베이스에서 테이블을 표시하려면 Hadoop 명령줄에서 다음 명령을 실행합니다.

	hive -e "show tables in nyctaxidb;"

아래 명령을 실행하여 테이블이 분할되었는지 확인할 수 있습니다.

	hive -e "show partitions nyctaxidb.trip;"

예상 출력은 다음과 같습니다.

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 2.075 seconds, Fetched: 12 row(s)

마찬가지로 아래 명령을 실행하여 fare 테이블이 분할되었는지 확인할 수 있습니다.

	hive -e "show partitions nyctaxidb.fare;"

예상 출력은 다음과 같습니다.

	month=1
	month=10
	month=11
	month=12
	month=2
	month=3
	month=4
	month=5
	month=6
	month=7
	month=8
	month=9
	Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Hive에서 데이터 탐색 및 기능 엔지니어링

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

Hive 테이블에 로드된 데이터에 대한 데이터 탐색 및 기능 엔지니어링 작업은 Hive 쿼리를 사용하여 수행할 수 있습니다. 다음은 이 섹션에서 설명할 이러한 작업의 예입니다.

- 두 테이블의 상위 10개 레코드를 봅니다.
- 다양한 기간에 걸쳐 몇몇 필드의 데이터 분포를 탐색합니다.
- 경도 및 위도 필드의 데이터 품질을 조사합니다.
- **tip\_amount**에 따라 이진 및 다중 클래스 분류 레이블을 생성합니다.
- 직접 여정 거리를 계산하여 기능을 생성합니다.

### 탐색: trip 테이블의 상위 10개 레코드 보기

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

데이터 모양을 보려면 각 테이블에서 10개의 레코드를 살펴봅니다. Hadoop 명령줄 콘솔의 Hive 디렉터리 프롬프트에서 다음 두 쿼리를 따로 실행하여 레코드를 검사합니다.

첫째 달의 "trip" 테이블에서 상위 10개의 레코드를 가져오려면

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

첫째 달의 "fare" 테이블에서 상위 10개의 레코드를 가져오려면

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

보기 편하도록 레코드를 파일로 저장하는 것이 유용한 경우가 많습니다. 위 쿼리를 약간 변경하면 다음 작업이 수행됩니다.

	hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### 탐색: 각 12개 파티션의 각 레코드 수 보기

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

연간 여정 수가 어떻게 다른지 확인하려는 것입니다. 월별로 그룹화를 통해 이 여정 분포를 볼 수 있습니다.

	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

출력은 다음과 같습니다.

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 283.406 seconds, Fetched: 12 row(s)

여기서 첫 번째 열은 월이고, 두 번째 열은 해당 월의 여정 수입니다.

Hive 디렉터리 프롬프트에서 다음 명령을 실행하여 여정 데이터 집합의 총 레코드 수를 계산할 수도 있습니다.

	hive -e "select count(*) from nyctaxidb.trip;"

다음과 같은 결과가 산출됩니다.

	173179759
	Time taken: 284.017 seconds, Fetched: 1 row(s)

trip 데이터 집합에 표시된 것과 유사한 명령을 사용하여 Hive 디렉터리 프롬프트에서 fare 데이터 집합에 대해 레코드 수를 확인하는 Hive 쿼리를 실행할 수 있습니다.

	hive -e "select month, count(*) from nyctaxidb.fare group by month;"

출력은 다음과 같습니다.

	1       14776615
	2       13990176
	3       15749228
	4       15100468
	5       15285049
	6       14385456
	7       13823840
	8       12597109
	9       14107693
	10      15004556
	11      14388451
	12      13971118
	Time taken: 253.955 seconds, Fetched: 12 row(s)

두 데이터 집합 모두에 대해 정확히 동일한 월별 여정 수가 반환됩니다. 이는 데이터가 올바르게 로드되었는지 확인하는 첫 번째 유효성 검사를 제공합니다.

Hive 디렉터리 프롬프트에서 아래 명령을 사용하여 fare 데이터 집합의 총 레코드 수를 계산할 수 있습니다.

	hive -e "select count(*) from nyctaxidb.fare;"

다음과 같은 결과가 산출됩니다.

	173179759
	Time taken: 186.683 seconds, Fetched: 1 row(s)

두 테이블 모두의 총 레코드 수가 동일합니다. 이는 데이터가 올바르게 로드되었는지 확인하는 두 번째 유효성 검사를 제공합니다.

### 탐색: medallion별 여정 분포

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

이 예제에서는 지정된 기간 내의 여정이 100개가 넘는 medallion(택시 번호)을 식별합니다. 쿼리는 파티션 변수 **month**의 영향을 받기 때문에 테이블을 분할하면 쿼리 성능이 개선됩니다. 쿼리 결과는 `C:\temp` 헤드 노드의 로컬 파일 queryoutput.tsv에 작성됩니다.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

다음은 검사할 *sample\_hive\_trip\_count\_by\_medallion.hql* 파일의 내용입니다.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

NYC taxi 데이터 집합의 medallion은 고유한 택시를 식별합니다. 특정 기간에 특정 여정 수를 초과하는 택시를 조회하여 "운행량이 많은" 택시를 식별할 수 있습니다. 다음 예제에서는 첫 3개월 동안 여정 수가 100건이 넘는 택시를 식별하여 쿼리 결과를 로컬 파일 C:\\temp\\queryoutput.tsv에 저장합니다.

다음은 검사할 *sample\_hive\_trip\_count\_by\_medallion.hql* 파일의 내용입니다.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100
	ORDER BY med_count desc;

Hive 디렉터리 프롬프트에서 아래 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### 탐색: medallion 및 hack\_license별 여정 분포

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

데이터 집합을 탐색할 때 값 그룹의 동시 발생 횟수를 조사하는 경우가 많습니다. 이 섹션에서는 택시와 운전 기사에 대해 이 작업을 수행하는 방법에 대한 예제를 제공합니다.

*sample\_hive\_trip\_count\_by\_medallion\_license.hql* 파일은 "medallion" 및 "hack\_license"에서 fare 데이터 집합을 그룹화하고 각 조합의 개수를 반환합니다. 내용은 다음과 같습니다.

    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

이 쿼리는 택시와 특정 운전 기사의 조합을 여정 수 내림차순으로 반환합니다.

Hive 디렉터리 프롬프트에서 다음을 실행합니다.

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

쿼리 결과는 로컬 파일 C:\\temp\\queryoutput.tsv에 작성됩니다.

### 탐색: 잘못된 경도/위도 레코드를 확인하여 데이터 품질 평가

>[AZURE.NOTE] 이는 일반적으로 **데이터 과학자** 작업입니다.

예비 데이터 분석의 일반적인 목적은 유효하지 않거나 잘못된 레코드를 걸러내는 것입니다. 이 섹션의 예제에서는 위도 또는 경도 필드에 NYC 영역 밖의 값이 포함되어 있는지 여부를 확인합니다. 이러한 레코드에는 잘못된 경도-위도 값이 있을 수 있기 때문에 모델링에 사용할 데이터에서 이를 제거하려고 합니다.

다음은 검사할 *sample\_hive\_quality\_assessment.hql* 파일의 내용입니다.

	    SELECT COUNT(*) FROM nyctaxidb.trip
	    WHERE month=1
	    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Hive 디렉터리 프롬프트에서 다음을 실행합니다.

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

이 명령에 포함된 *-S* 인수는 Hive 맵/감소 작업의 상태 화면 인쇄를 표시하지 않습니다. 이렇게 하면 Hive 쿼리 출력의 화면 인쇄를 좀 더 쉽게 읽을 수 있으므로 유용합니다.

### 탐색: 여정 팁의 이진 클래스 분포

**참고:** 이는 일반적으로 **데이터 과학자** 작업입니다.

[예측 작업의 예제](machine-learning-data-science-process-hive-walkthrough.md#mltasks) 섹션에 설명된 이진 분류 문제의 경우 팁 제공 여부를 아는 것이 유용합니다. 이 팁 분포는 이진입니다.

* tip given(Class 1, tip\_amount > $0)
* no tip(Class 0, tip\_amount = $0).

아래에 표시된 *sample\_hive\_tipped\_frequencies.hql* 파일에서 이 작업을 수행합니다.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Hive 디렉터리 프롬프트에서 다음을 실행합니다.

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### 탐색: 다중 클래스 설정의 클래스 분포

**참고:** 이는 일반적으로 **데이터 과학자** 작업입니다.

[예측 작업의 예제](machine-learning-data-science-process-hive-walkthrough.md#mltasks) 섹션에 설명된 다중 클래스 분류 문제의 경우 이 데이터 집합은 제공된 팁 금액을 예측할 수 있는 자연 분류 기능을 제공합니다. bin을 사용하여 쿼리에서 팁 범위를 정의할 수 있습니다. 여러 팁 범위에 대한 클래스 분포를 가져오려면 *sample\_hive\_tip\_range\_frequencies.hql* 파일을 사용합니다. 내용은 다음과 같습니다.

	SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Hadoop 명령줄 콘솔에서 다음 명령을 실행합니다.

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### 탐색: 두 경도-위도 위치 간의 직접 거리 계산

**참고:** 이는 일반적으로 **데이터 과학자** 작업입니다.

직접 거리를 측정하면 직접 거리와 실제 여정 거리 간의 불일치를 찾을 수 있습니다. 이 기능이 필요한 이유는 승객이 운전 기사가 의도적으로 훨씬 더 긴 경로를 이용했는지 알면 팁을 제공하지 않을 것이기 때문입니다.

실제 여정 거리와 두 경도-위도 지점 간의 [Haversine 거리](http://en.wikipedia.org/wiki/Haversine_formula)("대권" 거리)를 비교하려면 Hive 내에서 제공되는 삼각 함수를 사용합니다.

    set R=3959;
    set pi=radians(180);

	insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

위 쿼리에서 R은 지구의 반경(마일)이고, pi는 라디안으로 변환됩니다. 경도-위도 지점은 NYC 영역에서 멀리 떨어진 값을 제거하기 위해 "필터링"됩니다.

이 예제에서는 결과를 "queryoutputdir"이라는 디렉터리에 기록합니다. 아래 표시된 명령 시퀀스는 먼저 이 출력 디렉터리를 만든 다음 Hive 명령을 실행합니다.

Hive 디렉터리 프롬프트에서 다음을 실행합니다.

	hdfs dfs -mkdir wasb:///queryoutputdir

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


쿼리 결과는 Hadoop 클러스터 기본 컨테이너 아래 9개의 Azure blob(***queryoutputdir/000000\_0***~***queryoutputdir/000008\_0***)에 기록됩니다.

개별 blob의 크기를 보려면 Hive 디렉터리 프롬프트에서 다음 명령을 실행합니다.

	hdfs dfs -ls wasb:///queryoutputdir

지정된 파일, 즉 000000\_0의 내용을 보려면 Hadoop의 `copyToLocal` 명령을 사용합니다.

	hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

**경고:** `copyToLocal`은 파일이 큰 경우 매우 느려질 수 있으므로 큰 파일에 사용하지 않는 것이 좋습니다.

이 데이터를 Azure blob에 두면 [데이터 가져오기][import-data] 모듈을 사용하여 Azure 기계 학습 내에서 데이터를 탐색할 수 있는 이점이 있습니다.


## <a name="#downsample"></a>Azure 기계 학습에서 데이터 다운 샘플링 및 모델 빌드

**참고:** 이는 일반적으로 **데이터 과학자** 작업입니다.

예비 데이터 분석 단계를 마쳤으므로 이제 Azure 기계 학습에서 모델을 빌드하기 위한 데이터를 다운 샘플링할 수 있습니다. 이 섹션에서는 Hive 쿼리를 사용하여 Azure 기계 학습의 [데이터 가져오기][import-data] 모듈에서 액세스할 데이터를 다운 샘플링하는 방법을 보여 줍니다.

### 데이터 다운 샘플링

이 절차에는 두 단계가 있습니다. 먼저 모든 레코드에 있는 세 개의 키("medallion", "hack\_license" 및 "pickup\_datetime")에서 **nyctaxidb.trip** 및 **nyctaxidb.fare** 테이블을 조인합니다. 그런 다음 이진 분류 레이블 **tipped**와 다중 클래스 분류 레이블 **tip\_class**를 생성합니다.

다운 샘플링한 데이터를 Azure 기계 학습의 [데이터 가져오기][import-data] 모듈에서 직접 사용하려면 위 쿼리 결과를 내부 Hive 테이블에 저장해야 합니다. 내부 Hive 테이블을 만들고 조인 및 다운 샘플링된 데이터로 채웁니다.

이 쿼리는 표준 Hive 함수를 직접 적용하여 "pickup\_datetime" 필드에서 시간, 주, 요일(월요일은 1, 토요일은 7)을 생성하고 승차 위치와 하차 위치 간의 직접 거리를 생성합니다. 이러한 항목의 전체 목록은 [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)에서 확인할 수 있습니다.

그런 다음 이 쿼리는 쿼리 결과가 Azure 기계 학습 스튜디오에 적합하도록 데이터를 다운 샘플링합니다. 원래 데이터 집합의 약 1%만 스튜디오로 가져옵니다.

다음은 Azure 기계 학습에서 모델 빌드를 위한 데이터를 준비하는 *sample\_hive\_prepare\_for\_aml\_full.hql* 파일의 내용입니다.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
		)
		row format delimited fields terminated by ','
		lines terminated by '\n'
		stored as textfile;

		--- now insert contents of the join into the above internal table

    	insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
    	select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

    	from
    	(
        select
		medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
		${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

이 쿼리를 실행하려면 Hive 디렉터리 프롬프트에서 다음을 실행합니다.

	hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

이제 Azure 기계 학습의 [데이터 가져오기][import-data] 모듈을 사용하여 액세스할 수 있는 내부 테이블 "nyctaxidb.nyctaxi\_downsampled\_dataset"가 생성되었습니다. 또한 이 데이터 집합을 사용하여 기계 학습 모델을 빌드할 수 있습니다.

### Azure 기계 학습의 데이터 가져오기 모듈을 사용하여 다운 샘플링된 데이터 액세스

Azure 기계 학습의 [데이터 가져오기][import-data] 모듈에서 Hive 쿼리를 실행하려면 Azure Machine Learning 작업 영역과 클러스터 및 연결된 해당 저장소 계정의 자격 증명에 액세스해야 합니다.

[데이터 가져오기][import-data] 모듈과 입력할 매개 변수에 대한 일부 정보는 다음과 같습니다.

**HCatalog server URI**: 클러스터 이름이 abc123인 경우 단순히 https://abc123.azurehdinsight.net입니다.

**Hadoop user account name**: 클러스터에 대해 선택한 사용자 이름(원격 액세스 사용자 이름이 **아님**)입니다.

**Hadoop user account password**: 클러스터에 대해 선택한 암호(원격 액세스 암호가 **아님**)입니다.

**Location of output data**: Azure로 선택됩니다.

**Azure storage account name**: 클러스터와 연결된 기본 저장소 계정의 이름입니다.

**Azure container name**: 클러스터의 기본 컨테이너 이름이며, 일반적으로 클러스터 이름과 같습니다. "abc123"이라는 클러스터의 경우 단순히 abc123입니다.

**중요:** **Azure 기계 학습의 [데이터 가져오기][import-data] 모듈을 사용하여 쿼리할 모든 테이블은 내부 테이블이어야 합니다.** 데이터베이스 D.db의 테이블 T가 내부 테이블인지 확인하기 위한 팁은 다음과 같습니다.

Hive 디렉터리 프롬프트에서 다음 명령을 실행합니다.

	hdfs dfs -ls wasb:///D.db/T

테이블이 내부 테이블이고 채워진 경우 해당 내용이 여기에 표시되어야 합니다. 테이블이 내부 테이블인지 확인하는 또 다른 방법은 Azure 저장소 탐색기를 사용하는 것입니다. Azure 저장소 탐색기를 사용하여 클러스터의 기본 컨테이너 이름으로 이동한 다음 테이블 이름으로 필터링합니다. 테이블과 해당 내용이 표시되면 내부 테이블인 것입니다.

다음은 Hive 쿼리 및 [데이터 가져오기][import-data] 모듈의 스냅숏입니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

다운 샘플링된 데이터는 기본 컨테이너에 있으므로 Azure 기계 학습의 결과 Hive 쿼리는 매우 단순하며 "SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data"입니다.

이제 이 데이터 집합을 기계 학습 모델 빌드를 위한 시작 지점으로 사용할 수 있습니다.

### <a name="mlmodel"></a>Azure 기계 학습에서 모델 빌드

이제 [Azure 기계 학습](https://studio.azureml.net)에서 모델 빌드 및 모델 배포를 진행할 수 있습니다. 위에서 파악된 다음과 같은 예측 문제를 해결하는 데 데이터를 사용할 수 있습니다.

**1. 이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다.

**사용된 학습자:** 2클래스 로지스틱 회귀

a. 이 문제의 경우 대상(또는 클래스) 레이블은 "tipped"입니다. 원래 다운 샘플링된 데이터 집합에는 이 분류 실험에 대한 대상 누수인 몇 가지 열이 있습니다. 특히 tip\_class, tip\_amount 및 total\_amount는 테스트 시 사용할 수 없는 대상 레이블에 대한 정보를 표시합니다. [데이터 집합의 열 선택][select-columns] 모듈을 사용하여 이러한 열을 고려 대상에서 제거합니다.

아래 스냅숏은 주어진 여정에 대해 팁이 지불되었는지 여부를 예측하는 실험을 보여 줍니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. 이 실험의 경우 대상 레이블 분포는 약 1:1입니다.

아래 스냅숏은 이진 분류 문제에 대한 팁 클래스 레이블의 분포를 보여 줍니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

결과적으로, 아래 그림에 표시된 것처럼 AUC는 0.987입니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. 다중 클래스 분류**: 이전에 정의된 클래스를 사용하여 여정에 대해 지불된 팁 금액 범위를 예측합니다.

**사용된 학습자:** 다중 클래스 로지스틱 회귀

a. 이 문제의 경우 대상(또는 클래스) 레이블은 5개 값(0,1,2,3,4) 중 하나일 수 있는 "tip\_class"입니다. 이진 분류와 마찬가지로 이 실험에 대한 대상 누수인 몇 개 열이 있습니다. 특히 tipped, tip\_amount 및 total\_amount는 테스트 시 사용할 수 없는 대상 레이블에 대한 정보를 표시합니다. [데이터 집합의 열 선택][select-columns] 모듈을 사용하여 이러한 열을 제거합니다.

아래 스냅숏은 팁이 속할 수 있는 bin을 예측하는 실험을 보여 줍니다(Class 0: tip = $0, class 1 : tip > $0 and tip <= $5, Class 2 : tip > $5 and tip <= $10, Class 3 : tip > $10 and tip <= $20, Class 4 : tip > $20).

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

실제 테스트 클래스 분포는 다음과 같습니다. Class 0과 Class 1은 우세한 반면, 다른 클래스는 희박합니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. 이 실험에서는 혼동 행렬을 사용하여 예측 정확도를 확인합니다. 다음과 같습니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

우세한 클래스의 클래스 정확도는 매우 좋지만 희박한 클래스에서 "학습"하는 것은 좋지 않습니다.


**3. 회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.

**사용된 학습자:** 향상된 의사 결정 트리

a. 이 문제의 경우 대상(또는 클래스) 레이블은 "tip\_amount"입니다. 이 경우 대상 누수는 tipped, tip\_class, total\_amount입니다. 이러한 변수는 모두 일반적으로 테스트 시 사용할 수 없는 팁 금액에 대한 정보를 표시합니다. [데이터 집합의 열 선택][select-columns] 모듈을 사용하여 이러한 열을 제거합니다.

아래 스냅숏에서는 주어진 팁 금액을 예측하는 실험을 보여 줍니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. 회귀 문제의 경우 예측의 제곱된 오류, 결정 계수 등을 확인하여 예측 정확도를 측정합니다. 아래와 같이 표시됩니다.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

결정 계수가 0.709인데, 이는 분산의 약 71%가 모델 계수로 설명됨을 의미합니다.

**중요:** Azure 기계 학습 및 이를 사용하고 액세스하는 방법에 대한 자세한 내용은 [기계 학습이란?](machine-learning-what-is-machine-learning.md)을 참조하세요. Azure 기계 학습의 다양한 기계 학습 실험을 활용하는 데 매우 유용한 리소스는 [Cortana Intelligence 갤러리](https://gallery.cortanaintelligence.com/)입니다. 갤러리에는 다양한 실험이 있으며, Azure 기계 학습의 광범위한 기능을 소개합니다.

## 라이선스 정보

이 샘플 연습 및 함께 제공되는 스크립트는 Microsoft에서 MIT 라이선스에 따라 공유하고 있습니다. 자세한 내용은 GitHub의 샘플 코드 디렉터리에 있는 LICENSE.txt 파일을 참조하세요.

## 참조

•	[Andrés Monroy NYC 택시 왕복 다운로드 페이지](http://www.andresmh.com/nyctaxitrips/)  
•	[Chris Whong FOILing NYC 택시 여정 데이터](http://chriswhong.com/open-data/foil_nyc_taxi/)   
•	[NYC 택시 및 리무진 수수료 연구 및 통계](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->