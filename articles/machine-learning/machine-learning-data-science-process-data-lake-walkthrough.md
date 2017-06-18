<properties
	pageTitle="Azure Data Lake에서 확장성 있는 데이터 과학: 종단 간 연습 | Microsoft Azure"
	description="Azure Data Lake를 사용하여 데이터 집합에 대해 데이터 탐색 및 이진 분류 작업을 수행하는 방법입니다."  
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
	ms.date="09/19/2016"
	ms.author="bradsev;weig"/>


# Azure Data Lake에서 확장성 있는 데이터 과학: 종단 간 연습

이 연습에서는 팁을 요금으로 지급할지 여부를 예측하기 위해 NYC Taxi Trip 및 요금 데이터 집합 샘플에서 데이터 탐색 및 이진 분류 작업을 수행하는 데 Azure Data Lake를 사용하는 방법을 보여 줍니다. [팀 데이터 과학 프로세스](http://aka.ms/datascienceprocess), 종단 간, 데이터 획득에서 모델 학습 후 모델을 게시하는 웹 서비스 배포 단계까지 안내합니다.


### Azure 데이터 레이크 분석

[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)는 데이터 과학자가 임의 크기, 모양 및 속도로 데이터를 저장하고 데이터 처리, 고급 분석 및 기계 학습 모델링을 높은 확장성과 함께 비용 효율적으로 수행하는 데 필요한 모든 기능을 포함합니다. 데이터가 실제로 처리되는 경우에만 작업 단위로 비용을 지불합니다. Azure Data Lake 분석에는 SQL의 선언적 특성을 C#의 표현 능력으로 혼합하여 확장성 있는 분산 쿼리 기능을 제공하는 언어인 U-SQL을 포함합니다. 이를 통해, 읽기에 스키마를 적용하여 구조화되지 않은 데이터를 처리하고, 사용자 지정 논리 및 UDF(사용자 정의 함수)를 삽입하고, 대규모 실행 방법을 정교하게 세분화하여 제어할 수 있도록 확장성을 포함할 수 있습니다. U-SQL의 디자인 원리에 대해 자세히 알아보려면 [Visual Studio 블로그 게시물](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)을 참조하세요.

Data Lake 분석은 또한 Cortana Analytics Suite에서 핵심적인 부분으로, Azure SQL 데이터 웨어하우스, Power BI 및 Data Factory와 함께 사용할 수 있습니다. 이렇게 전체 클라우드 빅 데이터 및 고급 분석 플랫폼을 제공합니다.

이 연습에서는 데이터 과학 프로세스를 구성하는 Data Lake 분석에서 작업을 완료하는 데 필요한 필수 조건 및 리소스와 이를 설치하는 방법을 설명하는 것으로 시작합니다. 그런 후 U-SQL을 사용하여 데이터 처리 단계를 요약하고, 마지막으로 Azure 기계 학습 스튜디오에서 Python 및 Hive를 사용하여 예측 모델을 빌드하고 배포하는 방법을 보여 줍니다.


### U-SQL 및 Visual Studio

이 연습에서는 Visual Studio를 사용하여 데이터 집합을 처리하도록 U-SQL 스크립트를 편집할 것을 권장합니다. U-SQL 스크립트는 여기서 설명하고 별도의 파일로 제공됩니다. 이 프로세스에는 데이터 수집, 탐색 및 샘플링이 포함됩니다. 또한 Azure 포털에서 U-SQL 스크립트 작업을 실행하는 방법을 보여 줍니다. Azure 기계 학습 스튜디오에서 이진 분류 모델의 빌드 및 배포를 용이하게 하기 위해 연결된 HDInsight 클러스터에 데이터에 대한 Hive 테이블이 생성됩니다.


### Python

이 연습에는 Azure 기계 학습 스튜디오에서 Python을 사용하여 예측 모델을 빌드 및 배포하는 방법을 보여 주는 섹션도 포함되어 있습니다. 이 프로세스의 이러한 단계에 대해 Python 스크립트와 함께 Jupyter Notebook을 제공합니다. Notebook에는 여기에 설명된 이진 분류 모델 외에도 다중 클래스 분류 및 회귀 모델링과 같은 추가 기능 엔지니어링 단계 및 모델 생성을 위한 코드가 포함됩니다. 회귀 작업은 다른 팁 기능을 기반으로 하는 팁의 금액을 예측합니다.


### Azure 기계 학습
Azure 기계 학습 스튜디오는 예측 모델을 빌드 및 배포하는 데 사용됩니다. 이러한 작업은 먼저 Python 스크립트를 사용한 다음 HDInsight(Hadoop) 클러스터의 Hive 테이블을 사용하여 수행됩니다.


### 스크립트

이 연습에는 주요 단계만 나와 있습니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)에서 전체 **U-SQL 스크립트** 및 **Jupyter Notebook**을 다운로드할 수 있습니다.


## 필수 조건

이 토픽을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. 아직 가지고 있지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- [권장] Visual Studio 2013 또는 2015. 아직 이러한 버전이 설치되지 않은 경우 [여기](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)에서 무료 Community Edition을 다운로드할 수 있습니다. Visual Studio 섹션 아래에서 **Community 2015 다운로드** 단추를 클릭합니다.

>[AZURE.NOTE] Visual Studio 대신 Azure 포털을 사용하여 Azure Data Lake 쿼리를 제출할 수도 있습니다. Visual Studio 및 포털에서 이 작업을 수행하는 방법에 대한 지침은 **U-SQL로 데이터 처리** 섹션에서 설명합니다.

- Azure Data Lake 미리 보기 등록

>[AZURE.NOTE] 이러한 서비스는 미리 보기 상태이므로 Azure Data Lake 저장소(ADLS) 및 Azure Data Lake 분석(ADLA) 사용에 대한 승인을 받아야 합니다. 첫 번째 ADLS 또는 ADLA를 만들 때 등록하라는 메시지가 표시됩니다. 등록하려면 **미리 보기 등록**을 클릭하고 규약 내용을 읽은 후 **확인**을 클릭합니다. 예를 들어 다음은 ADLS 등록 페이지입니다.

 ![2](./media/machine-learning-data-science-process-data-lake-walkthrough/2-ADLA-preview-signup.PNG)


## Azure Data Lake에 대한 데이터 과학 환경 준비
이 연습에 대한 데이터 과학 환경을 준비하려면 다음 리소스를 만듭니다.

- Azure Data Lake 저장소(ADLS)
- Azure Data Lake 분석(ADLA)
- Azure Blob 저장소 계정
- Azure 기계 학습 스튜디오 계정
- Visual Studio용 Azure Data Lake 도구(권장)

이 섹션에서는 이러한 각 리소스를 만드는 방법에 대한 지침을 제공합니다. Azure 기계 학습에서 Python 대신 Hive 테이블을 사용하여 모델을 작성하려는 경우 HDInsight(Hadoop) 클러스터를 프로비전해야 합니다. 이러한 대체 절차는 아래의 해당 섹션에 설명되어 있습니다. <br/>
>AZURE.NOTE **Azure Data Lake 저장소**는 별도로 만들거나 **Azure Data Lake 분석**을 만들 때 기본 저장소로 만들 수 있습니다. 아래에서는 이러한 각 리소스를 만들기 위한 지침이 별도로 참조되지만 Data Lake 저장소 계정을 별도의 단계로 만들지 않아도 됩니다. <br/>
### Azure 데이터 레이크 저장소 만들기

[Azure 포털](http://portal.azure.com)에서 ADLS를 만듭니다. 자세한 내용은 [Azure 포털을 사용하여 Data Lake 저장소로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 여기 설명된 **옵션 구성** 블레이드의 **DataSource** 블레이드에서 클러스터 AAD ID를 설정해야 합니다.

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)


### Azure Data Lake 분석 계정 만들기
[Azure 포털](http://portal.azure.com)에서 ADLA 계정을 만듭니다. 자세한 내용은 [자습서: Azure 포털을 사용하여 Azure Data Lake 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)


### Azure Blob 저장소 계정 만들기
[Azure 포털](http://portal.azure.com)에서 Azure Blob 저장소 계정을 만듭니다. 자세한 내용은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)의 저장소 계정 만들기 섹션을 참조하세요.
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)


### Azure 기계 학습 스튜디오 계정 설정
[Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 페이지에서 Azure 기계 학습 스튜디오를 등록/로그인합니다. **지금 시작** 단추를 클릭한 후 "무료 작업 영역" 또는 "표준 작업 영역"을 선택합니다. 그런 다음 Azure 기계 학습 스튜디오에서 실험을 만들 수 있습니다.

### Azure Data Lake 도구 설치[권장]
[Visual Studio용 Azure Data Lake 도구](https://www.microsoft.com/download/details.aspx?id=49504)에서 사용 중인 Visual Studio 버전에 대한 Azure Data Lake 도구를 설치합니다.

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

설치가 성공적으로 완료되었으면 Visual Studio를 엽니다. 위쪽 메뉴에 Data Lake 탭이 표시됩니다. Azure 리소스는 Azure 계정에 로그인하면 왼쪽 패널에 표시됩니다.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)


## NYC Taxi Trips 데이터 집합
여기에 사용된 데이터 집합은 공개적으로 제공되는 데이터 집합인 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합입니다. NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 기록된 약 20GB의 압축된 CSV 파일(압축되지 않은 경우 약 48GB)로 구성됩니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 집합으로 제공됩니다.

 - 'trip\_data' CSV는 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - 'trip\_fare' CSV는 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_license 및 pickup\_datetime 필드로 구성됩니다. 공용 Azure 저장소 Blob에서 원시 CSV 파일을 액세스할 수 있습니다. 이 조인에 대한 U-SQL 스크립트는 [trip 및 fare 테이블 조인](#join) 섹션에 나와 있습니다.

## U-SQL로 데이터 처리

이 섹션에서 설명하는 데이터 처리 작업에는 데이터 수집, 품질 검사, 탐색 및 샘플링이 포함됩니다. 또한 trip 및 fare 테이블을 조인하는 방법도 보여 줍니다. 마지막 섹션에서는 Azure 포털에서 U-SQL 스크립트 작업을 실행하는 방법을 보여 줍니다. 각 하위 섹션에 대한 링크는 다음과 같습니다.

- [데이터 수집: 공용 Blob에서 데이터 읽기](#ingest)
- [데이터 품질 검사](#quality)
- [데이터 탐색](#explore)
- [trip 및 fare 테이블 조인](#join)
- [데이터 샘플링](#sample)
- [U-SQL 작업 실행](#run)

U-SQL 스크립트는 여기서 설명하고 별도의 파일로 제공됩니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)에서 전체 **U-SQL 스크립트**를 다운로드할 수 있습니다.

U-SQL을 실행하려면 Visual Studio를 열고 **파일 --> 새로 만들기 --> 프로젝트**를 클릭한 후 **U-SQL 프로젝트**를 선택하고 이름을 지정하고 폴더에 저장합니다.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

>[AZURE.NOTE] Visual Studio 대신 Azure 포털을 사용하여 U-SQL을 실행할 수 있습니다. 포털에서 Azure Data Lake 분석 리소스로 이동한 후 다음 그림과 같이 쿼리를 직접 제출할 수 있습니다.

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>데이터 수집: 공용 Blob에서 데이터 읽기

Azure Blob에서 데이터 위치는 **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**으로 참조되고 **Extractors.Csv()**를 사용하여 추출할 수 있습니다. 다음 스크립트에서 wasb 주소의 container_name@blob\_storage\_account\_name에 대한 사용자 고유 컨테이너 이름과 저장소 계정 이름을 대체합니다. 파일 이름은 같은 형식이므로 12개 trip 파일을 모두 읽는 데 **trip\_data\_{*}.csv**를 사용할 수 있습니다.

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

첫 번째 행에 헤더가 있으므로 헤더를 제거하고 열 형식을 적절하게 변경해야 합니다. **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_을 사용하여 처리된 데이터를 Azure Data Lake 저장소에 저장하거나 **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**을 사용하여 Azure Blob 저장소 계정에 저장할 수 있습니다.

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

마찬가지로 fare 데이터 집합에서 읽을 수 있습니다. Azure Data Lake 저장소를 마우스 오른쪽 단추로 클릭하고 **Azure Portal --> 데이터 탐색기** 또는 Visual Studio 내 **파일 탐색기**에서 데이터를 살펴보도록 선택할 수 있습니다.

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)


### <a name="quality"></a>데이터 품질 검사

Trip 및 fare 테이블을 읽은 후 다음과 같은 방식으로 데이터 품질 검사를 수행할 수 있습니다. 결과 CSV 파일은 Azure Blob 저장소 또는 Azure Data Lake 저장소에 출력할 수 있습니다.

medallion 수와 medallion의 고유 번호를 찾습니다.

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

여정(trip)이 100개가 넘는 medallion을 찾습니다.

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

pickup\_longitude 조건으로 유효하지 않은 레코드를 찾습니다.

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

일부 변수에 대해 누락된 값을 찾습니다.

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>데이터 탐색

데이터를 잘 이해하기 위해 몇 가지 데이터 탐색을 수행할 수 있습니다.

왕복 및 비왕복 여정의 분포를 찾습니다.

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

0, 5, 10, 20달러의 제한 값을 가진 팁 금액의 분포를 찾습니다.

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

여정 거리의 기본 통계를 찾습니다.

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

여정 거리의 백분위수를 찾습니다.

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>trip 및 fare 테이블 조인

medallion, hack\_license 및 pickup\_time으로 trip 및 fare 테이블을 조인할 수 있습니다.

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


각 승객 수 수준에 대해 레코드 수, 평균 팁 금액, 팁 금액의 분산, 왕복 여정의 백분율을 계산합니다.

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>데이터 샘플링

먼저 조인된 테이블에서 0.1%의 데이터를 임의로 선택합니다.

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

다음으로 이진 변수 tip\_class로 계층화된 샘플링을 수행합니다.

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>U-SQL 작업 실행

U-SQL 스크립트 편집을 마치면 Azure Data Lake 분석 계정을 사용하여 서버에 제출할 수 있습니다. **Data Lake**, **작업 제출**을 클릭하고 **분석 계정**, **병렬 처리**를 선택하고 **제출** 단추를 클릭합니다.

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

작업이 성공적으로 컴파일되면 모니터링을 위해 작업 상태가 Visual Studio에 표시됩니다. 작업 실행을 완료한 후에는 작업 실행 프로세스를 재생하여 병목 단계를 파악하고 작업 효율성을 향상시킬 수 있습니다. 또한 Azure 포털로 이동하여 U-SQL 작업의 상태를 확인할 수도 있습니다.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)


 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)


이제 Azure Blob 저장소 또는 Azure 포털에서 출력 파일을 확인할 수 있습니다. 다음 단계에서 모델링을 위한 계층화된 샘플 데이터를 사용합니다.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)


## Azure 기계 학습에서 모델 빌드 및 배포

Azure 기계 학습으로 데이터를 끌어와 빌드 및 배포하기 위한 두 가지 옵션에 대해 설명합니다.

- 첫 번째 옵션에서는 Azure Blob에 기록된 샘플링된 데이터를 사용하고(위의 **데이터 샘플링** 단계에서) 모델을 빌드하고 Azure 기계 학습에 배포하는 데 Python을 사용합니다.
- 두 번째 옵션에서는 Hive 쿼리를 사용하여 Azure Data Lake의 데이터를 직접 쿼리할 수 있습니다. 이 옵션에서는 새 HDInsight 클러스터를 만들거나 Hive 테이블이 Azure Data Lake 저장소의 NY 택시 데이터를 가리키는 기존 HDInsight 클러스터를 사용해야 합니다. 아래의 두 옵션을 모두 설명합니다.

## 옵션 1: Python을 사용하여 기계 학습 모델 빌드 및 배포

Python을 사용하여 기계 학습 모델을 빌드 및 배포하려면 로컬 컴퓨터에서 또는 Azure 기계 학습 스튜디오에서 Jupyter Notebook을 만듭니다. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)에 제공된 Jupyter Notebook에는 데이터 탐색 및 시각화, 기능 엔지니어링, 모델링 및 배포를 위한 전체 코드가 포함되어 있습니다. 이 문서에서는 모델링 및 배포 단계만 보여 줍니다.

### Python 라이브러리 가져오기

샘플 Jupyter Notebook 또는 Python 스크립트 파일을 실행하기 위해 다음 Python 패키지가 필요합니다. AzureML Notebook 서비스를 사용하는 경우 이러한 패키지는 미리 설치되었습니다.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


### Blob에서 데이터 읽기

- 연결 문자열

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- 텍스트로 읽기

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)
 
- 열 이름 추가 및 열 구분

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- 일부 열을 숫자로 변경

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

### 기계 학습 모델 빌드

여기서는 여정에서 팁을 받는지 여부를 예측하는 이진 분류 모델을 빌드합니다. Jupyter Notebook에서 다중 클래스 분류 및 회귀 모델이라는 다른 두 모델을 확인할 수 있습니다.

- 먼저 scikit-learn 모델에 사용할 수 있는 더미 변수를 만들어야 합니다.

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- 모델링을 위한 데이터 프레임 만들기

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- 학습 및 테스트 60-40분할

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- 학습 집합에서 로지스틱 회귀

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)

- 테스트 데이터 집합 점수 매기기

		Y_test_pred = logit_fit.predict(X_test)

- 평가 메트릭 계산

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)


 
### 웹 서비스 API 구축 및 Python에서 사용

기계 학습 모델을 빌드한 후 운영하려고 합니다. 여기서는 예로 이진 로지스틱 모델을 사용합니다. 로컬 컴퓨터에서 scikit-learn 버전이 0.15.1인지 확인합니다. Azure 기계 학습 스튜디오 서비스를 사용하는 경우에는 걱정할 필요가 없습니다.

- Azure 기계 학습 스튜디오 설정에서 작업 영역 자격 증명을 찾습니다. Azure 기계 학습 스튜디오에서 **설정** --> **이름** --> **권한 부여 토큰**을 클릭합니다.

	![c3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- 웹 서비스 만들기

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- 웹 서비스 자격 증명 가져오기

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- 웹 서비스 API를 호출합니다. 이전 단계를 수행한 후 5~10초 정도 기다려야 합니다.

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)


## 옵션 2: Azure 기계 학습에서 모델을 만들고 직접 배포

Azure 기계 학습 스튜디오에서는 Azure Data Lake 저장소에서 직접 데이터를 읽고 모델을 만들며 배포할 수 있습니다. 이 방식에서는 Azure Data Lake 저장소를 가리키는 Hive 테이블을 사용합니다. 이를 위해 Hive 테이블이 만들어지는 별도의 Azure HDInsight 클러스터를 프로비전해야 합니다. 다음 섹션에서는 이 작업을 수행하는 방법을 보여 줍니다.

### HDInsight Linux 클러스터 만들기

[Azure 포털](http://portal.azure.com)에서 HDInsight 클러스터(Linux)를 만듭니다. 자세한 내용은 [Azure 포털을 사용하여 Data Lake 저장소로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)의 **Azure Data Lake 저장소에 액세스할 수 있는 HDInsight 클러스터 만들기** 섹션을 참조하세요.

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### HDInsight에서 Hive 테이블 만들기

이제 이전 단계에서 Azure Data Lake 저장소에 저장한 데이터를 사용하여 Azure 기계 학습 스튜디오에서 사용할 Hive 테이블을 HDInsight 클러스터에서 만듭니다. 방금 만든 HDInsight 클러스터로 이동합니다. **설정** --> **속성** --> **클러스터 AAD ID** --> **ADLS 액세스**를 클릭하고 Azure Data Lake 저장소 계정이 읽기, 쓰기 및 실행 권한이 있는 목록에 추가되어 있는지 확인합니다.

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)


그런 다음 **설정** 단추 옆의 **대시보드**를 클릭하면 팝업 창이 나타납니다. 페이지의 오른쪽 위 모서리에서 **Hive 보기**를 클릭하면 **쿼리 편집기**가 표시됩니다.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)


다음 Hive 스크립트를 붙여 넣어 테이블을 만듭니다. 데이터 원본의 위치는 다음과 같은 방식으로 Azure Data Lake 저장소 참조에 있습니다. **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


쿼리 실행이 끝나면 다음과 같은 결과가 표시됩니다.

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)



### Azure 기계 학습 스튜디오에서 모델 빌드 및 배포

이제 팁이 Azure 기계 학습에서 유료인지 여부를 예측하는 모델을 빌드 및 배포할 준비가 되었습니다. 계층화된 샘플 데이터는 이 이진 분류(팁인지 아닌지) 문제에서 사용할 준비가 되었습니다. 다중 클래스 분류(tip\_class) 및 회귀(tip\_amount)를 사용하는 예측 모델은 Azure 기계 학습 스튜디오를 사용하여 빌드 및 배포할 수도 있지만 여기에서는 이진 분류 모델을 사용하여 사례를 처리하는 방법만 살펴보겠습니다.

1. **데이터 입력 및 출력** 섹션에서 제공되는 **데이터 가져오기** 모듈을 사용하여 Azure ML로 데이터를 가져옵니다. 자세한 내용은 [데이터 가져오기](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) 참조 페이지를 참조하세요.
2. **속성** 패널에서 **데이터 원본**으로 **Hive 쿼리**를 선택합니다.
3. 다음 Hive 스크립트를 **Hive 데이터베이스 쿼리** 편집기에 붙여 넣습니다.

    	select * from nyc_stratified_sample;

4. HDInsight 클러스터의 URI(Azure 포털에서 찾을 수 있음), Hadoop 자격 증명, 출력 데이터의 위치, Azure 저장소 계정 이름/키/컨테이너 이름을 입력합니다.

 ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)

아래 그림은 Hive 테이블에서 데이터를 읽는 이진 분류 실험 예제입니다.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

실험을 만든 후 **웹 서비스 설정** --> **예측 웹 서비스**를 클릭합니다.

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

자동으로 생성된 점수 매기기 실험을 실행하고 완료되었으면 **웹 서비스 배포**를 클릭합니다.

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

웹 서비스 대시보드가 바로 표시됩니다.

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)


## 요약

이 연습을 완료하면서 Azure Data Lake에서 확장성 있는 종단 간 솔루션을 구축하기 위한 데이터 과학 환경을 만들었습니다. 이 환경은 모델 학습을 통한 데이터 획득부터 웹 서비스로 모델 배포에 이르는 데이터 과학 프로세스의 정식 단계를 통해 가져온 대형 공용 데이터 집합을 분석하는 데 사용되었습니다. U-SQL은 이러한 데이터를 처리하고 탐색하며 샘플링하는 데 사용되었습니다. Python 및 Hive는 Azure 기계 학습 스튜디오에서 예측 모델을 빌드하고 배포하는 데 사용되었습니다.

## 다음 작업

[TDSP(팀 데이터 과학 프로세스)](http://aka.ms/datascienceprocess)에 대한 학습 경로는 고급 분석 프로세스의 각 단계를 설명하는 토픽에 대한 링크를 제공합니다. 다양한 예측 분석 시나리오에서 리소스 및 서비스를 사용하는 방법을 소개하는 [팀 데이터 과학 프로세스 연습](data-science-process-walkthroughs.md) 페이지에는 일련의 연습 과정이 항목별로 정리되어 있습니다.

- [실행 중인 팀 데이터 과학 프로세스: SQL 데이터 웨어하우스 사용](machine-learning-data-science-process-sqldw-walkthrough.md)
- [실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-walkthrough.md)
- [팀 데이터 과학 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md)
- [Azure HDInsight에서 Spark를 사용하는 데이터 과학 프로세스 개요](machine-learning-data-science-spark-overview.md)

<!---HONumber=AcomDC_0921_2016-->