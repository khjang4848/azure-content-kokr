<properties 
	pageTitle="SQL 파티션 테이블을 사용하여 대량의 데이터를 병렬로 가져오기 | Microsoft Azure" 
	description="SQL 파티션 테이블을 사용하여 대량의 데이터를 병렬로 가져오기" 
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
	ms.author="bradsev" />

# SQL 파티션 테이블을 사용하여 대량의 데이터를 병렬로 가져오기

이 문서에서는 분할된 테이블을 만들어서 SQL Server 데이터베이스로 대량의 데이터를 병렬로 더 빨리 가져오는 방법을 설명합니다. SQL 데이터베이스로 빅 데이터를 로드/전송할 때 _분할된 테이블 및 뷰_를 사용하여 SQL DB로 데이터를 가져오는 작업과 후속 쿼리의 성능을 개선할 수 있습니다.


## 새 데이터베이스 및 파일 그룹 만들기

- [새 데이터베이스 만들기](https://technet.microsoft.com/library/ms176061.aspx)(존재하지 않는 경우)
- 분할된 실제 파일을 보유할 데이터베이스에 데이터베이스 파일 그룹 추가

  참고: 새 데이터베이스를 만드는 경우 [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx)를 사용하여, 기존 데이터베이스가 있는 경우 [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx)를 사용하여 이 작업을 수행할 수 있음

- 각 데이터베이스 파일 그룹에 파일을 하나 이상(필요한 만큼) 추가

 > [AZURE.NOTE] 이 파티션의 데이터를 보유할 대상 파일 그룹과 파일 그룹 데이터가 저장될 실제 데이터베이스 파일 이름을 지정합니다.
 
다음은 기본 그룹 및 로그 그룹이 아닌 3개의 파일 그룹이 있고 각 파일 그룹에 물리적 파일 1개가 포함되는 새 데이터베이스를 만드는 예제입니다. SQL Server 인스턴스에 구성된 것처럼 데이터베이스 파일은 기본 SQL Server 데이터 폴더에 생성됩니다. 기본 파일 위치에 대한 자세한 내용은[ SQL Server의 기본 인스턴스 및 명명된 인스턴스의 파일 위치](https://msdn.microsoft.com/library/ms143547.aspx)를 참조하세요.

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
    	CREATE DATABASE <database_name>
     	ON  PRIMARY 
    	( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_1] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_2] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
     	FILEGROUP [filegroup_3] 
    	( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
     	LOG ON 
    	( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## 분할된 테이블 만들기

이전 단계에서 만든 데이터베이스 파일 그룹에 매핑된 데이터 스키마에 따라 분할된 테이블을 만듭니다. 분할된 테이블로 데이터를 대량으로 가져오는 경우 아래에 설명된 것처럼 파티션 스키마에 따라 레코드가 파일 그룹 사이에 분산됩니다.

**파티션 테이블을 만들려면 다음을 수행해야 합니다.**

- 각 파티션 테이블에 포함될 값/경계 범위를 정의하는 [파티션 함수를 만듭니다](https://msdn.microsoft.com/library/ms187802.aspx). 예를 들어 아래는 2013년 월별로(some\_datetime\_field) 파티션을 제한합니다.

	    CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
	    AS RANGE RIGHT FOR VALUES (
	    	'20130201', '20130301', '20130401',
	    	'20130501', '20130601', '20130701', '20130801',
	    	'20130901', '20131001', '20131101', '20131201' )

- 파티션 함수의 각 파티션 범위를 실제 파일 그룹에 매핑하는 [파티션 스키마를 만듭니다](https://msdn.microsoft.com/library/ms179854.aspx). 아래는 그 예입니다.

	    CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
	    PARTITION <DatetimeFieldPFN> TO (
	    <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
	    <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
	    <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  팁: 함수/스키마에 따라 각 파티션에 적용되는 범위를 확인하려면 다음 쿼리를 실행합니다.

	    SELECT psch.name as PartitionScheme,
	    	prng.value AS ParitionValue,
	    	prng.boundary_id AS BoundaryID
	    FROM sys.partition_functions AS pfun
	    INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
	    INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
	    WHERE pfun.name = <DatetimeFieldPFN>

- 데이터 스키마에 따라 [분할된 테이블을 만들고](https://msdn.microsoft.com/library/ms174979.aspx) 테이블 분할에 사용할 파티션 스키마 및 제약 조건 필드를 지정합니다. 아래는 그 예입니다.

	    CREATE TABLE <table_name> ( [include schema definition here] )
	    ON <TablePScheme>(<partition_field>)

자세한 내용은 [분할된 테이블 및 인덱스 만들기](https://msdn.microsoft.com/library/ms188730.aspx)를 참조하세요.


## 각 파티션 테이블의 데이터를 대량으로 가져오기

- BCP, BULK INSERT 또는 [SQL Server 마이그레이션 마법사](http://sqlazuremw.codeplex.com/) 같은 기타 방법을 사용할 수 있습니다. 제공된 예에서는 BCP를 사용합니다.

- 로깅 오버헤드를 최소화하기 위해 [데이터베이스를 변경](https://msdn.microsoft.com/library/bb522682.aspx)하여 트랜잭션 로깅 스키마를 BULK\_LOGGED로 변경합니다. 아래는 그 예입니다.

	    ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- 신속한 데이터 로드를 위해 대량 가져오기 작업을 병렬로 실행합니다. 빅 데이터를 SQL Server 데이터베이스로 신속하게 대량으로 가져오는 방법에 대한 팁은 [1시간 이내에 1TB 로드하기](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)를 참조하세요.

다음 PowerShell 스크립트는 BCP를 사용하여 병렬로 데이터를 로드하는 예입니다.

    # Set database name, input data directory, and output log directory
	# This example loads comma-separated input data files
	# The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
	# Assumes the input data files include a header line. Loading starts at line number 2.

	$dbname = "<database_name>"
	$indir  = "<path_to_data_files>"
	$logdir = "<path_to_log_directory>"

	# Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
	# Set table name to be loaded, basename of input data files, input format file, and number of partitions
	$tbname = "<table_name>"
	$basename = "<base_input_data_filename_no_extension>"
	$fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "" + $basename + "_" + $num + ".csv") -o ($logdir + "" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "" + $basename + "_" + $num + ".csv") -o ($logdir + "" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## 인덱스를 만들어서 조인 및 쿼리 성능 최적화

- 여러 테이블에서 모델링에 대한 데이터를 추출하려는 경우 조인 키에 인덱스를 만들어서 조인 성능을 개선할 수 있습니다.

- 각 파티션에 대해 동일한 파일 그룹을 대상으로 하는 클러스터형 또는 비클러스터형 [인덱스를 만듭니다](https://technet.microsoft.com/library/ms188783.aspx). 아래는 그 예입니다.

	    CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
	    ON <TablePScheme>(<partition)field>)
또는

	    CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
	    ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] 인덱스를 만든 후 데이터를 대량으로 가져올 수도 있습니다. 인덱스를 만든 후 대량 가져오기를 수행하면 데이터 로드 속도가 느려집니다.


## 고급 분석 프로세스 및 기술 작동 예제

공용 데이터 집합과 Cortana 분석 프로세스를 사용하여 종단간 연습 예제는 [실행 중인 Cortana 분석 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md)을 참조하세요.
 

<!---HONumber=AcomDC_0921_2016-->