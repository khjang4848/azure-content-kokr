<properties
	pageTitle="실행 중인 팀 데이터 과학 프로세스: SQL 데이터 웨어하우스 사용 | Microsoft Azure"
	description="활성 중인 고급 분석 프로세스 및 기술"  
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
	ms.date="06/24/2016"
	ms.author="bradsev;hangzh;weig"/>


# 실행 중인 팀 데이터 과학 프로세스: SQL 데이터 웨어하우스 사용

이 자습서에서는 공개적으로 사용 가능한 데이터 집합인 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합에 SQL 데이터 웨어하우스(SQL DW)를 사용하여 기계 학습 모델을 구축 및 배포하는 방법을 안내합니다. 생성된 이진 분류 모델을 통해 여정에 대해 팁이 지불되었는지 여부를 예측하며 지불된 팁 금액의 분배를 예측하는 다중 클래스 분류 및 회귀에 대한 모델도 설명됩니다.

이 절차에서는 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 워크플로를 따릅니다. 데이터 과학 환경을 설정하는 방법, SQL DW에 데이터를 로드하는 방법 및 SQL DW 또는 IPython Notebook을 사용하여 모델링할 데이터와 엔지니어링 기능을 탐색하는 방법을 보여 줍니다. 그런 다음 Azure 기계 학습으로 모델을 빌드하고 배포하는 방법을 보여 줍니다.


## <a name="dataset"></a>NYC Taxi Trips 데이터 집합

NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 기록된 약 20GB의 압축된 CSV 파일(압축되지 않은 경우 약 48GB)로 구성됩니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 집합으로 제공됩니다.

1. **trip\_data.csv** 파일에는 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보가 포함됩니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. **trip\_fare.csv** 파일에는 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보가 포함됩니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

trip\_data 및 trip\_fare를 조인하는 데 사용된 **고유 키**는 다음 세 필드로 구성됩니다.

- medallion,
- hack\_license,
- pickup\_datetime

## <a name="mltasks"></a>세 가지 유형의 예측 작업 처리

*tip\_amount*를 기반으로 예측 문제를 작성하여 세 종류의 모델링 작업을 보여 줍니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다. 즉, *tip\_amount*가 $0보다 크면 지불된 것이며 *tip\_amount*가 $0이면 지불되지 않은 것입니다.

2. **다중 클래스 분류**: 여정에 대해 지불된 팁의 범위를 예측합니다. *tip\_amount*를 5개의 bin 또는 클래스로 나눕니다.

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.


## <a name="setup"></a>고급 분석을 위한 Azure 데이터 과학 환경 설정

Azure 데이터 과학 환경을 설정하려면 다음 단계를 수행합니다.

**고유한 Azure Blob 저장소 계정 만들기**

- 고유한 Azure Blob 저장소를 프로비전할 때 Azure Blob 저장소에 대한 지역 위치를 NYC 택시 데이터가 저장된 **미국 중남부**에 가능한 한 가깝게 선택합니다. 데이터는 공용 Blob 저장소 컨테이너에서 AzCopy를 사용하여 자체 저장소 계정의 컨테이너로 복사됩니다. Azure Blob 저장소가 미국 중남부에 가까울수록 이 작업(4단계)이 완료가 더 빨라집니다.
- 고유의 Azure 저장소 계정을 만들려면 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)에 요약된 단계를 수행합니다. 이 연습의 뒷부분에서 필요하므로 다음 저장소 계정 자격 증명에 대한 값을 적어두어야 합니다.

  - **저장소 계정 이름**
  - **저장소 계정 키**
  - **컨테이너 이름**(데이터를 저장하려는 Azure Blob 저장소)

**Azure SQL DW 인스턴스를 프로비전합니다.** [SQL 데이터 웨어하우스 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)의 설명서에 따라 SQL 데이터 웨어하우스 인스턴스를 프로비전합니다. 이후 단계에서 사용되는 다음 SQL 데이터 웨어하우스 자격 증명에 표기하도록 합니다.

  - **Server Name**: <server Name>.database.windows.net
  - **SQLDW(데이터베이스) 이름**
  - **사용자 이름**
  - **암호**

**Visual Studio 2015 및 SQL Server 데이터 도구 설치** 자세한 지침은 [SQL 데이터 웨어하우스에 Visual Studio 2015 및/또는 SSDT(SQL Server Data Tools) 설치](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)를 참조하세요.

**Visual Studio로 Azure SQL DW에 연결합니다.** 자세한 지침은 [Visual Studio로 Azure SQL 데이터 웨어하우스에 연결](../sql-data-warehouse/sql-data-warehouse-connect-overview.md)의 1단계 및 2단계를 참조하세요.

>[AZURE.NOTE] SQL 데이터 웨어하우스에 만든 데이터베이스에 다음 SQL 쿼리(연결 토픽의 3단계에서 제공된 쿼리 대신)를 실행하여 **마스터 키를 만듭니다**.

	BEGIN TRY
	       --Try to create the master key
	    CREATE MASTER KEY
	END TRY
	BEGIN CATCH
	       --If the master key exists, do nothing
	END CATCH;

**Azure 구독에서 Azure Machine Learning 작업 영역을 만듭니다.** 자세한 지침은 [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md)를 참조하세요.

## <a name="getdata"></a>SQL 데이터 웨어하우스에 데이터 로드

Windows PowerShell 명령 콘솔을 엽니다. 다음 PowerShell 명령을 실행하여 Github에서 *-DestDir* 매개 변수를 사용하여 지정한 로컬 디렉터리에 공유하는 예제 SQL 스크립트 파일을 다운로드합니다. 매개 변수 *-DestDir*의 값을 로컬 디렉터리로 변경할 수 있습니다. *-DestDir*이 존재하지 않는 경우 PowerShell 스크립트를 통해 생성됩니다.

>[AZURE.NOTE] 다음 PowerShell 스크립트를 실행할 때 *DestDir* 디렉터리에 만들거나 작성하는 데 관리자 권한이 필요한 경우 **관리자 권한으로 실행**해야 할 수 있습니다.

	$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
	$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
	$wc = New-Object System.Net.WebClient
	$wc.DownloadFile($source, $ps1_dest)
	.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

성공적으로 실행된 후에 현재 작업 디렉터리를 *-DestDir*로 변경합니다. 아래와 같은 화면을 볼 수 있어야 합니다.

![][19]

*-DestDir*의 관리자 모드에서 다음 PowerShell 스크립트를 실행합니다.

	./SQLDW_Data_Import.ps1

PowerShell 스크립트가 처음으로 실행되면 Azure SQL DW 및 Azure Blob 저장소 계정에서 정보를 입력하라고 요청합니다. 이 PowerShell 스크립트가 처음 실행을 완료하는 경우 입력한 자격 증명은 현재 작업 디렉터리의 SQLDW.conf 구성 파일에 작성됩니다. 이 PowerShell 스크립트 파일을 나중에 실행하는 경우 이 구성 파일에서 필요한 매개 변수를 모두 읽는 옵션이 있습니다. 일부 매개 변수를 변경해야 할 경우 표시되는 메시지에 따라 이 구성 파일을 삭제하고 매개 변수 값을 입력하여 화면에 매개 변수를 입력하거나 *-DestDir* 디렉터리의 SQLDW.conf 파일을 편집하여 매개 변수 값을 변경하도록 선택할 수 있습니다.

>[AZURE.NOTE] Azure SQL DW에 이미 있는 이름과 스키마 이름 충돌을 방지하기 위해 SQLDW.conf 파일에서 직접 매개 변수를 읽을 때 SQLDW.conf 파일의 스키마 이름에 각 실행에 대한 기본 스키마 이름으로 임의의 3자리 수가 추가됩니다. PowerShell 스크립트에서 스키마 이름을 지정하라는 메시지가 표시될 수 있습니다. 이 이름은 사용자가 임의로 지정할 수 있습니다.

이 **PowerShell 스크립트** 파일은 다음 작업을 완료합니다.

- AzCopy가 설치되지 않은 경우 **AzCopy 다운로드 및 설치**

		$AzCopy_path = SearchAzCopy
    	if ($AzCopy_path -eq $null){
       		Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
        	InstallAzCopy
        	$AzCopy_path = SearchAzCopy
    	}
			$env_path = $env:Path
			for ($i=0; $i -lt $AzCopy_path.count; $i++){
				if ($AzCopy_path.count -eq 1){
					$AzCopy_path_i = $AzCopy_path
				} else {
					$AzCopy_path_i = $AzCopy_path[$i]
				}
				if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
					Write-Host $AzCopy_path_i 'not in system path, add it...'
					[Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
					$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
					$env_path = $env:Path
				}

- AzCopy로 공용 Blob에서 **개인 Blob 저장소 계정에 데이터 복사**

		Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
		$start_time = Get-Date
		AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
		$end_time = Get-Date
    	$time_span = $end_time - $start_time
    	$total_seconds = [math]::Round($time_span.TotalSeconds,2)
    	Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
    	Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- 다음 명령을 통해 개인 Blob 저장소 계정에서**Azure SQL DW에 Polybase를 사용(LoadDataToSQLDW.sql 실행)하여 데이터를 로드**합니다.

	- 스키마 만들기

			EXEC (''CREATE SCHEMA {schemaname};'');

	- 데이터베이스 범위 자격 증명 만들기

			CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
			WITH IDENTITY = ''asbkey'' ,
			Secret = ''{StorageAccountKey}''

	- Azure 저장소 Blob에 대한 외부 데이터 원본 만들기

			CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

			CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

	- csv 파일에 대한 외부 파일 형식을 만듭니다. 데이터가 압축되지 않으며 필드는 파이프 문자로 분리됩니다.

			CREATE EXTERNAL FILE FORMAT {csv_file_format}
			WITH
			(   
    			FORMAT_TYPE = DELIMITEDTEXT,
    			FORMAT_OPTIONS  
    			(
        			FIELD_TERMINATOR ='','',
        			USE_TYPE_DEFAULT = TRUE
    			)
			)
			;

	- Azure Blob 저장소에 NYC Taxi 데이터 집합에 대한 외부 요금 및 여정 테이블을 만듭니다.

			CREATE EXTERNAL TABLE {external_nyctaxi_fare}
			(
				medallion varchar(50) not null,
				hack_license varchar(50) not null,
				vendor_id char(3),
				pickup_datetime datetime not null,
				payment_type char(3),
				fare_amount float,
				surcharge float,
				mta_tax float,
				tip_amount float,
				tolls_amount float,
				total_amount float
			)
			with (
    			LOCATION    = ''/nyctaxifare/'',
    			DATA_SOURCE = {nyctaxi_fare_storage},
    			FILE_FORMAT = {csv_file_format},
				REJECT_TYPE = VALUE,
				REJECT_VALUE = 12     
			)  


			CREATE EXTERNAL TABLE {external_nyctaxi_trip}
			(
       			medallion varchar(50) not null,
       			hack_license varchar(50)  not null,
       			vendor_id char(3),
       			rate_code char(3),
       			store_and_fwd_flag char(3),
       			pickup_datetime datetime  not null,
       			dropoff_datetime datetime,
       			passenger_count int,
       			trip_time_in_secs bigint,
       			trip_distance float,
       			pickup_longitude varchar(30),
       			pickup_latitude varchar(30),
       			dropoff_longitude varchar(30),
       			dropoff_latitude varchar(30)
			)
			with (
    			LOCATION    = ''/nyctaxitrip/'',
    			DATA_SOURCE = {nyctaxi_trip_storage},
    			FILE_FORMAT = {csv_file_format},
    			REJECT_TYPE = VALUE,
				REJECT_VALUE = 12         
			)

	- Azure Blob 저장소의 외부 테이블에서 SQL 데이터 웨어하우스에 데이터 로드

			CREATE TABLE {schemaname}.{nyctaxi_fare}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			SELECT *
			FROM   {external_nyctaxi_fare}
			;

			CREATE TABLE {schemaname}.{nyctaxi_trip}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			SELECT *
			FROM   {external_nyctaxi_trip}
			;

	- 샘플 데이터 테이블(NYCTaxi\_Sample)을 만들고 여정 및 요금 테이블에 SQL 쿼리를 선택하여 데이터를 삽입합니다. (이 연습의 일부 단계는 이 샘플 테이블을 사용해야 합니다.)

			CREATE TABLE {schemaname}.{nyctaxi_sample}
			WITH
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS
			(
	    		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
				tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
				tip_class = CASE
						WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
	    		FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
    			WHERE datepart("mi",t.pickup_datetime) = 1
				AND t.medallion = f.medallion
    			AND   t.hack_license = f.hack_license
    			AND   t.pickup_datetime = f.pickup_datetime
    			AND   pickup_longitude <> ''0''
        		AND   dropoff_longitude <> ''0''
			)
			;

저장소 계정의 지리적 위치는 로드 시간을 영향을 줍니다.

>[AZURE.NOTE] 개인 Blob 저장소 계정의 지리적 위치에 따라 공용 Blob에서 개인 저장소 계정에 데이터를 복사하는 프로세스는 15분 이상이 걸릴 수 있습니다. 저장소 계정에서 Azure SQL DW로 데이터를 로드하는 프로세스는 20분 이상이 걸릴 수 있습니다.

중복된 원본 및 대상 파일이 있는 경우 수행할 작업을 결정해야 합니다.

>[AZURE.NOTE] 공용 Blob 저장소에서 개인 Blob 저장소 계정으로 복사할 .csv 파일이 개인 Blob 저장소 계정에 이미 있으면 AzCopy는 덮어쓸 것인지를 묻습니다. 덮어쓰지 않으려는 경우 메시지가 표시되면 **n**을 입력합니다. **모두** 덮어쓰려는 경우 메시지가 표시되면 **a**를 입력합니다. 또한 **y**를 입력하여 개별적으로 .csv 파일을 덮어쓸 수 있습니다.

![그림 #21][21]

사용자 고유의 데이터를 사용할 수 있습니다. 데이터가 실제 응용 프로그램의 온-프레미스 컴퓨터에 있으면 AzCopy을 사용하여 개인 Azure Blob 저장소에 온-프레미스 데이터를 업로드할 수 있습니다. PowerShell 스크립트 파일의 AzCopy 명령에서 **원본** 위치인 `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`를 데이터가 있는 로컬 디렉터리로 변경해야만 합니다.


>[AZURE.TIP] 데이터가 실제 응용 프로그램의 개인 Azure Blob 저장소에 이미 있으면 PowerShell 스크립트에서 AzCopy 단계를 건너뛰고 직접 Azure SQL DW에 데이터를 업로드할 수 있습니다. 데이터 형식에 맞추려면 스크립트를 추가로 편집해야 합니다.


또한 이 Powershell 스크립트는 Azure SQL DW 정보에서 데이터 탐색 예제 파일 SQLDW\_Explorations.sql, SQLDW\_Explorations.ipynb 및 SQLDW\_Explorations\_Scripts.py에 플러그 인하므로 이러한 세 가지 파일은 PowerShell 스크립트가 완료된 후에 즉시 시도될 준비가 됩니다.

성공적으로 실행한 후에 다음과 같이 화면에 표시됩니다.

![][20]

## <a name="dbexplore"></a>Azure SQL 데이터 웨어하우스에서 데이터 탐색 및 기능 엔지니어링

이 섹션에서는 **Visual Studio Data Tools**에서 바로 Azure SQL DW에 대해 SQL 쿼리를 실행하여 데이터를 탐색하고 기능을 생성합니다. 이 섹션에서 사용되는 모든 SQL 쿼리는 *SQLDW\_Explorations.sql*이라는 샘플 스크립트에서 찾을 수 있습니다. 이 파일은 이미 PowerShell 스크립트에 의해 로컬 디렉터리에 다운로드되었습니다. 또한 [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)에서 검색할 수 있습니다. 하지만 Github의 파일은 플러그 인된 Azure SQL DW 정보를 갖지 않습니다.

Visual Studio에서 SQL DW 로그인 이름 및 암호를 사용하여 Azure SQL DW에 연결하고 **SQL 개체 탐색기**를 열어 데이터베이스와 테이블을 가져왔는지 확인합니다. *SQLDW\_Explorations.sql* 파일을 검색합니다.

>[AZURE.NOTE] PDW(병렬 데이터 웨어하우스) 쿼리 편집기를 열려면 **SQL 개체 탐색기**에서 PDW를 선택하여 **새 쿼리** 명령을 사용합니다. 표준 SQL 쿼리 편집기는 PDW에서 지원되지 않습니다.

다음은 이 섹션에서 수행된 데이터의 탐색 및 기능 생성 작업의 형식입니다.

- 다양한 기간에 걸쳐 몇몇 필드의 데이터 분포를 탐색합니다.
- 경도 및 위도 필드의 데이터 품질을 조사합니다.
- **tip\_amount**에 따라 이진 및 다중 클래스 분류 레이블을 생성합니다.
- 기능을 생성하고 여정 거리를 계산/비교합니다.
- 두 테이블을 조인하고 모델을 빌드하는 데 사용할 무작위 샘플을 추출합니다.

### 데이터 가져오기 확인

다음 쿼리를 실행하면 이전에 Polybase 병렬 대량 가져오기를 사용하여 채운 테이블에서 행 및 열 수를 신속하게 확인할 수 있습니다.

	-- Report number of rows in table <nyctaxi_trip> without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

	-- Report number of columns in table <nyctaxi_trip>
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**출력:** 173,179,759행과 14개의 열이 표시됩니다.

### 탐색: medallion별 여정 분포

이 예제 쿼리에서는 지정된 기간 내의 100개가 넘는 여정을 완료한 medallion(택시 번호)을 식별합니다. 쿼리는 **pickup\_datetime** 파티션 구성표를 조건으로 하므로 분할된 테이블 액세스를 활용합니다. 전체 데이터 집합을 쿼리할 때도 분할된 테이블 및/또는 인덱스 검색을 사용합니다.

	SELECT medallion, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

**출력:** 쿼리는 13,369medallion(택시)을 지정하는 행과 2013년에 이를 통해 완료된 여정 수가 있는 테이블을 반환합니다. 마지막 열에는 완료된 여정 수가 포함됩니다.

### 탐색: medallion 및 hack\_license별 여정 분포

이 예제에서는 지정된 기간 내의 100개가 넘는 여정을 완료한 medallion(택시 번호) 및 hack\_license 번호(드라이버)를 식별합니다.

	SELECT medallion, hack_license, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

**출력:** 쿼리는 2013년에 100개가 넘는 여정을 완료한 13,369개의 차/드라이버 ID가 지정된 13,369개 행의 테이블을 반환합니다. 마지막 열에는 완료된 여정 수가 포함됩니다.

### 데이터 품질 평가: 경도 및/또는 위도가 잘못된 레코드 확인

이 예제에서는 경도 및/또는 위도 필드에 유효하지 않은 값(라디안이 -90도~90도에 속해야 함)이 포함되거나 (0, 0) 좌표가 있는지 조사합니다.

	SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**출력:** 쿼리는 잘못된 경도 및/또는 위도 필드가 있는 837,467개의 여정을 반환합니다.

### 탐색: 왕복 여정과 비왕복 여정 분포

이 예제에서는 지정된 기간 동안(또는 여기 설정된 대로 전체 연도를 포함하는 경우 전체 데이터 집합에서) 왕복 여정 수와 비왕복 여정 수를 확인합니다. 이 분포는 나중에 이진 분류 모델링에 사용할 이진 레이블 분포를 반영합니다.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM <schemaname>.<nyctaxi_fare>
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

**출력:** 쿼리는 2013년 팁 빈도(왕복 여정 90,447,622 및 비왕복 여정 82,264,709)를 반환합니다.

### 탐색: 팁 클래스/범위 분포

이 예제에서는 지정된 기간 동안(또는 전체 연도를 포괄하는 경우 전체 데이터 집합에서) 팁 범위 분포를 계산합니다. 이는 나중에 다중 클래스 분류 모델링에 사용할 레이블 클래스의 분포입니다.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4
		END AS tip_class
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

**출력:**

|tip\_class | tip\_freq |
| --------- | -------|
|1 | 82230915 |
|2 | 6198803 |
|3 | 1932223 |
|0 | 82264625 |
|4 | 85765 |

### 탐색: 여정 거리 계산 및 비교

이 예제에서는 승차 및 하차 경도/위도를 SQL 지리 지점으로 변환하고, SQL 지리 지점 차이를 사용하여 여정 거리를 계산한 다음, 비교를 위해 무작위 결과 샘플을 반환합니다. 앞서 설명한 데이터 품질 평가 쿼리를 사용하여 유효한 좌표로만 결과가 제한됩니다.

	/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

### SQL 함수를 사용하는 기능 엔지니어링

때때로 SQL 함수는 기능 엔지니어링에 대한 효율적인 옵션일 수 있습니다. 이 연습에서는 승차와 하차 위치 간의 직접 거리를 계산하는 SQL 함수를 정의했습니다. **Visual Studio Data Tools**에서 다음 SQL 스크립트를 실행할 수 있습니다.

거리 함수를 정의하는 SQL 스크립트는 다음과 같습니다.

	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function calculate the direct distance between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

SQL 쿼리에서 기능을 생성하는 이 함수를 호출하는 예는 다음과 같습니다.

	-- Sample query to call the function to create features
	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

**출력:** 이 쿼리는 승차 및 하차 위도 및 경도와 해당 직선 거리(마일)가 포함된 테이블(2,803,538개 행)을 생성합니다. 처음 3개 행에 대한 결과는 다음과 같습니다.

||pickup\_latitude | pickup\_longitude | dropoff\_latitude |dropoff\_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1 | 40\.731804 | -74.001083 | 40\.736622 | -73.988953 | .7169601222 |
|2 | 40\.715794 | -74,010635 | 40\.725338 | -74.00399 | .7448343721 |
|3 | 40\.761456 | -73.999886 | 40\.766544 | -73.988228 | 0\.7037227967 |



### 모델 구축에 사용할 데이터를 준비합니다.

다음 쿼리는 **nyctaxi\_trip** 및 **nyctaxi\_fare** 테이블을 조인하고, 이진 분류 레이블 **tipped**와 다중 클래스 분류 레이블 **tip\_class**를 생성하며, 조인된 전체 데이터 집합에서 샘플을 추출합니다. 샘플링은 승차 시간에 따라 여정의 하위 집합을 검색하여 수행됩니다. Azure의 SQL 데이터베이스 인스턴스에서 데이터를 직접 수집하기 위해 이 쿼리를 복사한 다음 [Azure 기계 학습 스튜디오](https://studio.azureml.net) [데이터 가져오기][import-data] 모듈에 직접 붙여넣을 수 있습니다. 잘못된 (0, 0) 좌표가 있는 레코드는 쿼리에서 제외됩니다.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
	WHERE datepart("mi",t.pickup_datetime) = 1
	AND   t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Azure 기계 학습을 진행할 준비가 되었으면 다음을 수행할 수 있습니다.

1. 데이터를 추출 및 샘플링할 최종 SQL 쿼리를 저장하고 Azure 기계 학습의 [데이터 가져오기][import-data] 모듈에 쿼리를 직접 복사하여 붙여 넣습니다. 또는
2. 모델을 빌드하는 데 사용할 샘플링 및 엔지니어링된 데이터를 새 SQL DW 테이블에 유지하고 Azure 기계 학습의 [데이터 가져오기][import-data] 모듈에서 새 테이블을 사용합니다. 이전 단계에서 PowerShell 스크립트가 이를 수행했습니다. 데이터 가져오기 모듈의 이 테이블에서 직접 읽을 수 있습니다.


## <a name="ipnb"></a>IPython Notebook에서 데이터 탐색 및 기능 엔지니어링

이 섹션에서는 Python과 SQL 쿼리를 모두 사용하여 이전에 만든 SQL DW에 대해 데이터 탐색 및 기능 생성을 수행합니다. **SQLDW\_Explorations.ipynb**라는 샘플 IPython Notebook 및 Python 스크립트 파일 **SQLDW\_Explorations\_Scripts.py**는 로컬 디렉터리에 다운로드되었습니다. 또한 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)에서 사용할 수 있습니다. 이 두 파일은 Python 스크립트에서 동일합니다. IPython Notebook 서버가 없는 경우 Python 스크립트 파일이 제공됩니다. 이 두 샘플 Python 파일은 **Python 2.7**에서 디자인됩니다.

로컬 컴퓨터에 다운로드한 샘플 IPython Notebook 및 Python 스크립트 파일에서 필요한 Azure SQL DW 정보는 이전에 PowerShell 스크립트에 의해 플러그 인되었습니다. 수정 없이 실행할 수 있습니다.

AzureML 작업 영역을 이미 설정한 경우 샘플 IPython Notebook을 AzureML IPython Notebook 서비스에 직접 업로드하고 실행을 시작할 수 있습니다. AzureML IPython Notebook 서비스에 업로드하는 단계는 다음과 같습니다.

1. AzureML 작업 영역에 로그인하고 맨 위에 있는 "Studio"를 클릭한 다음 웹 페이지의 왼쪽에서 "NOTEBOOKS"를 클릭합니다.

	![그림 #22][22]

2. 웹 페이지의 왼쪽 아래 모서리에서 "새로 만들기"를 클릭하고 "Python 2"를 선택합니다. 그런 다음 노트북에 이름을 제공하고 확인 표시를 클릭하여 새 비어 있는 IPython Notebook을 만듭니다.

	![그림 #23][23]

3. 새 IPython Notebook의 왼쪽 위 모서리에서 "Jupyter" 기호를 클릭합니다.

	![그림 #24][24]

4. 샘플 IPython Notebook을 AzureML IPython Notebook 서비스의 **트리** 페이지로 끌어서 놓고 **업로드**를 클릭합니다. 그런 다음 샘플 IPython Notebook은 AzureML IPython Notebook 서비스에 업로드됩니다.

	![그림 #25][25]

이 샘플 IPython Notebook 또는 Python 스크립트 파일을 실행하기 위해 다음 Python 패키지가 필요합니다. AzureML IPython Notebook 서비스를 사용하는 경우 이러한 패키지는 미리 설치되었습니다.

	- pandas
	- numpy
	- matplotlib
	- pyodbc
	- PyTables

큰 데이터로 AzureML에 고급 분석 솔루션을 구축할 때 권장된 시퀀스는 다음과 같습니다.

- 소량의 데이터 샘플을 메모리 내 데이터 프레임으로 읽습니다.
- 샘플링된 데이터를 사용하여 일부 시각화 및 탐색을 수행합니다.
- 샘플링된 데이터를 사용하여 기능 엔지니어링을 실험합니다.
- 더 큰 데이터 탐색, 데이터 조작 및 기능 엔지니어링의 경우 Python을 사용하여 SQL DW에 대해 SQL 쿼리를 직접 실행합니다.
- Azure 기계 학습 모델 빌드에 적합한 샘플 크기를 결정합니다.

다음은 데이터 탐색, 데이터 시각화 및 기능 엔지니어링에 대한 몇 가지 예제입니다. 더 많은 데이터 탐색은 샘플 IPython Notebook 및 샘플 Python 스크립트 파일에 있을 수 있습니다.

### 데이터베이스 자격 증명 초기화

다음 변수에서 데이터베이스 연결 설정을 초기화합니다.

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### 데이터베이스 연결 만들기

데이터베이스에 연결을 만드는 연결 문자열은 다음과 같습니다.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <nyctaxi\_trip> 테이블의 행 및 열 수 보고

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- 총 행 수 = 173179759
- 총 열 수 = 14

### <nyctaxi\_fare> 테이블의 행 및 열 수 보고

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- 총 행 수 = 173179759
- 총 열 수 = 11

### SQL 데이터 웨어하우스에서 소량의 데이터 샘플 읽기

    t0 = time.time()

	query = '''
		SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
			f.tolls_amount, f.total_amount, f.tip_amount
		FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
		WHERE datepart("mi",t.pickup_datetime) = 1
		AND   t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

샘플 테이블을 읽은 시간 = 14.096495초 검색된 행 및 열 수 = (1000, 21)

### 기술 통계

이제 샘플링된 데이터를 탐색할 준비가 되었습니다. **trip\_distance**(또는 지정하도록 선택한 다른 모든 필드)에 대한 기술 통계부터 살펴봅니다.

    df1['trip_distance'].describe()

### 시각화: 상자 그림 예제

다음으로 여정 거리에 대한 상자 그림을 확인하여 사분위수를 시각화합니다.

    df1.boxplot(column='trip_distance',return_type='dict')

![그릴 #1][1]

### 시각화: 분포 그림 예제

샘플링된 여정 거리에 대한 분포 및 히스토그램을 시각화하는 그림입니다.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![그릴 #2][2]

### 시각화: 가로 막대형 차트 및 꺾은선형 그림

이 예에서는 여정 거리를 5개의 bin으로 범주화하고 범주화 결과를 시각화합니다.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

위의 bin 분포를 다음과 같은 가로 막대형 차트 또는 꺾은선형 그림으로 그릴 수 있습니다.

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![그릴 #3][3]

and

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![그릴 #4][4]

### 시각화: 산점도 예제

**trip\_time\_in\_secs**와 **trip\_distance** 사이의 산점도를 표시하여 상관관계가 있는지 확인합니다.

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![그릴 #6][6]

마찬가지로 **rate\_code**와 **trip\_distance** 간의 관계를 확인할 수 있습니다.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![그릴 #8][8]


### IPython Notebook에서 SQL 쿼리를 사용하여 샘플링된 데이터에서 데이터 탐색

이 섹션에서는 위에서 만든 새 테이블에 유지되는 샘플링된 데이터를 사용하여 데이터 분포를 탐색합니다. 원래 테이블을 사용하여 유사한 탐색을 수행할 수 있습니다.

#### 탐색: 샘플링된 테이블의 행과 열 개수를 보고합니다.

	nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
	print 'Number of rows in sample = %d' % nrows.iloc[0,0]

	ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
	print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### 탐색: 왕복/비왕복 분포

	query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

	pd.read_sql(query, conn)

#### 탐색: 팁 클래스 분포

	query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
	'''

	tip_class_dist = pd.read_sql(query, conn)

#### 탐색: 클래스에 의해 팁 분포 그리기

	tip_class_dist['tip_freq'].plot(kind='bar')

![그림 #26][26]


#### 탐색: 일일 여정 분포

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### 탐색: medallion당 여정 분포

    query = '''
		SELECT medallion,count(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY medallion
	'''

	pd.read_sql(query,conn)

#### 탐색: medallion 및 hack license별 여정 분포

	query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
	pd.read_sql(query,conn)


#### 탐색: 여정 시간 분포

	query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
	pd.read_sql(query,conn)

#### 탐색: 여정 거리 분포

	query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
	pd.read_sql(query,conn)

#### 탐색: 지불 형식 분포

	query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
	pd.read_sql(query,conn)

#### 기능화한 테이블의 최종 양식 확인

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Azure 기계 학습에서 모델 빌드

이제 [Azure 기계 학습](https://studio.azureml.net)에서 모델 빌드 및 모델 배포를 진행할 준비가 완료되었습니다. 이전에 식별된 다음과 같은 예측 문제에 데이터를 사용할 준비가 되었습니다.

1. **이진 분류**: 여정에 대해 팁이 지불되었는지 여부를 예측합니다.

2. **다중 클래스 분류**: 이전에 정의한 클래스에 따라 지불된 팁의 범위를 예측합니다.

3. **회귀 작업**: 여정에 대해 지불된 팁의 금액을 예측합니다.



모델링 연습을 시작하려면 **Azure 기계 학습** 작업 영역에 로그인합니다. 기계 학습 작업 영역을 아직 만들지 않은 경우 [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md)를 참조하세요.

1. Azure 기계 학습을 시작하려면 [Azure 기계 학습 스튜디오란?](machine-learning-what-is-ml-studio.md)을 참조하세요.

2. [Azure 기계 학습 스튜디오](https://studio.azureml.net)에 로그인합니다.

3. 스튜디오 홈 페이지에서는 다양한 정보, 비디오, 자습서, 모듈 참조 링크 및 기타 리소스를 제공합니다. Azure 기계 학습에 대한 자세한 내용은 [Azure 기계 학습 설명서 센터](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

일반적인 학습 실험은 다음 단계로 구성됩니다.

1. **+새** 실험 만들기
2. Azure ML로 데이터를 가져옵니다.
3. 필요에 따라 데이터를 전처리, 변환 및 조작합니다.
4. 필요에 따라 기능을 생성합니다.
5. 데이터를 학습/유효성 검사/테스트 데이터 집합으로 분할하거나, 각각에 대한 별도의 데이터 집합을 만듭니다.
6. 해결할 학습 문제에 따라 하나 이상의 기계 학습 알고리즘(예: 이진 분류, 다중 클래스 분류, 회귀)을 선택합니다.
7. 학습 데이터 집합을 사용하여 하나 이상의 모델을 학습합니다.
8. 학습된 모델을 사용하여 유효성 검사 데이터 집합의 점수를 매깁니다.
9. 모델을 평가하여 학습 문제에 대한 관련 메트릭을 계산합니다.
10. 모델을 미세 조정하고 배포할 가장 적합한 모델을 선택합니다.

이 연습에서는 이미 SQL 데이터 웨어하우스에서 데이터를 탐색 및 엔지니어링하고 Azure ML에서 수집할 샘플 크기를 결정했습니다. 예측 모델 중 하나 이상을 빌드하는 절차는 다음과 같습니다.

1. **데이터 입력 및 출력** 섹션에서 제공되는 [데이터 가져오기][import-data] 모듈을 사용하여 Azure ML로 데이터를 가져옵니다. 자세한 내용은 [데이터 가져오기][import-data] 참조 페이지를 참조하세요.

	![Azure ML 데이터 가져오기][17]

2. **속성** 패널에서 **Azure SQL 데이터베이스**를 **데이터 원본**으로 선택합니다.

3. **데이터베이스 서버 이름** 필드에 데이터베이스 DNS 이름을 입력합니다. 형식: `tcp:<your_virtual_machine_DNS_name>,1433`

4. **데이터베이스 이름**을 해당 필드에 입력합니다.

5. **서버 사용자 계정 이름**에 *SQL 사용자 이름*을 입력하고, **서버 사용자 계정 암호**에 *암호*를 입력합니다.

6. **모든 서버 인증서 허용** 옵션을 선택합니다.

7. **데이터베이스 쿼리** 편집 텍스트 영역에서 필요한 데이터베이스 필드를 추출하는 쿼리(레이블과 같은 모든 계산된 필드 포함)를 붙여 넣고 데이터를 원하는 샘플 크기로 다운 샘플링합니다.

SQL 데이터 웨어하우스 데이터베이스에서 직접 데이터를 읽는 이진 분류 실험의 예는 아래 그림에 있습니다.(연습에서 사용한 스키마 이름 및 테이블 이름으로 테이블 이름 nyctaxi\_trip 및 nyctaxi\_fare를 교체해야 함) 다중 클래스 분류 및 회귀 문제에 대한 유사한 실험을 생성할 수 있습니다.

![Azure 기계 학습][10]

> [AZURE.IMPORTANT] 이전 섹션에 제공된 모델링 데이터 추출 및 샘플링 쿼리 예제에서는 **세 가지 모델링 연습에 대한 모든 레이블이 쿼리에 포함되어 있습니다**. 각 모델링 연습의 중요한(필수) 단계는 다른 두 문제에 대한 필요 없는 레이블 및 다른 모든 **목표 누설**을 **제외**하는 것입니다. 예를 들어 이진 분류를 사용할 때는 레이블 **tipped**를 사용하고, **tip\_class**, **tip\_amount** 및 **total\_amount** 필드를 제외합니다. 이러한 필드는 지불된 팁을 의미하므로 목표 누설입니다.
>
> 필요 없는 열 또는 목표 누설을 제외하려면 [데이터 집합의 열 선택][select-columns] 모듈 또는 [메타데이터 편집][edit-metadata]을 사용하면 됩니다. 자세한 내용은 [데이터 집합의 열 선택][select-columns] 및 [메타 데이터 편집][edit-metadata] 참조 페이지를 참조하세요.

## <a name="mldeploy"></a>Azure 기계 학습에서 모델 배포

모델이 준비된 경우 실험에서 직접 웹 서비스로 쉽게 배포할 수 있습니다. Azure 기계 학습 웹 서비스 배포에 대한 자세한 내용은 [Azure 기계 학습 웹 서비스 배포](machine-learning-publish-a-machine-learning-web-service.md)를 참조하세요.

새 웹 서비스를 배포하려면 다음을 수행해야 합니다.

1. 점수 매기기 실험을 만듭니다.
2. 웹 서비스를 배포합니다.

**완료된** 학습 실험에서 점수 매기기 실험을 만들려면 아래쪽 작업 모음에서 **점수 매기기 실험 만들기**를 클릭합니다.

![Azure 점수 매기기][18]

Azure 기계 학습에서는 학습 실험의 구성 요소를 기반으로 점수 매기기 실험을 만듭니다. 특히 다음 작업을 수행합니다.

1. 학습된 모델을 저장하고 모델 학습 모듈을 제거합니다.
2. 필요한 입력 데이터 스키마를 나타내는 논리적 **입력 포트**를 식별합니다.
3. 필요한 웹 서비스 출력 스키마를 나타내는 논리적 **출력 포트**를 식별합니다.

점수 매기기 실험을 만들 때 필요에 따라 검토하고 조정합니다. 일반적인 조정은 입력 데이터 집합 및/또는 쿼리를 레이블 필드를 제외한 것으로 바꾸는 것입니다. 레이블 필드는 서비스를 호출할 때 사용할 수 없기 때문입니다. 또한 입력 데이터 집합 및/또는 쿼리 크기를 입력 스키마를 나타내는 데 충분한 정도의 몇몇 레코드로 줄이는 것이 좋습니다. 출력 포트의 경우 일반적으로 [데이터 집합의 열 선택][select-columns] 모듈을 사용하여 모든 입력 필드를 제외하고 **점수가 매겨진 레이블** 및 **점수가 매겨진 확률**만 출력에 포함합니다.

샘플 점수 매기기 실험은 아래 그림에서 제공됩니다. 배포할 준비가 되면 아래쪽 작업 모음에서 **웹 서비스 게시** 단추를 클릭합니다.

![Azure ML 게시][11]


## 요약
이 연습 자습서에서 수행한 작업을 설명하면 Azure 데이터 과학 환경을 만들고 대용량 공용 데이터 집합으로 작업하며, 팀 데이터 과학 프로세스를 통해 가져오고, 데이터 취득에서 모델 학습, Azure 기계 학습 웹 서비스의 배포에 이르는 모든 과정을 수행했습니다.

### 라이선스 정보

이 샘플 연습 및 이와 함께 제공되는 스크립트와 IPython Notebook은 MIT 라이선스에 따라 Microsoft에서 공유한 것입니다. 자세한 내용은 GitHub의 샘플 코드 디렉터리에 있는 LICENSE.txt 파일을 참조하세요.

## 참조

• [Andrés Monroy NYC 택시 왕복 다운로드 페이지](http://www.andresmh.com/nyctaxitrips/) • [Chris Whong FOILing NYC 택시 여정 데이터](http://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC 택시 및 리무진 수수료 연구 및 통계](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->