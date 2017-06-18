<properties 
   pageTitle="Sqoop를 사용하여 Data Lake 저장소와 Azure SQL 데이터베이스 간에 데이터 복사 | Microsoft Azure"
   description="Sqoop를 사용하여 Azure SQL 데이터베이스와 Data Lake 저장소 간에 데이터 복사" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Sqoop를 사용하여 Data Lake 저장소와 Azure SQL 데이터베이스 간에 데이터 복사

Apache Sqoop를 사용하여 Azure SQL 데이터베이스와 Data Lake 저장소 간에 데이터를 가져오고 내보내는 방법을 알아봅니다.
 

## Sqoop 정의

빅 데이터 응용 프로그램은 로그 및 파일과 같은 비구조적 및 반구조적 데이터를 처리하기 위한 자연스러운 선택입니다. 그러나 관계형 데이터베이스에 저장된 구조적 데이터를 처리해야 할 수도 있습니다.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html)는 Data Lake 저장소와 같은 빅 데이터 리포지토리와 관계형 데이터베이스 간에 데이터를 전송하도록 디자인된 도구입니다. 이 도구를 사용하여 Azure SQL 데이터베이스와 같은 RDBMS(관계형 데이터베이스 관리 시스템)에서 Data Lake 저장소로 데이터를 가져올 수 있습니다. 그런 다음 빅 데이터 워크로드를 사용하여 데이터를 변환 및 분석한 후 RDBMS로 다시 내보낼 수 있습니다. 이 자습서에서는 Azure SQL 데이터베이스를 관계형 데이터베이스로 사용하여 데이터를 가져오고 내보낼 수 있습니다.
 

## 필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- Data Lake 저장소 계정에 액세스할 수 있는 **Azure HDInsight 클러스터**입니다. [Data Lake 저장소가 있는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 이 문서에서는 Data Lake 저장소가 있는 HDInsight Linux 클러스터 액세스 권한이 있는 것으로 가정합니다.
- **Azure SQL 데이터베이스**. 데이터베이스를 만드는 방법에 대한 지침은 [Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started.md)를 참조하세요.

## 비디오로 빠르게 배우시겠습니까?

DistCp를 사용하여 Azure 저장소 Blob과 Data Lake 저장소 간에 데이터 복사하는 방법에 대한 [비디오를 보세요](https://mix.office.com/watch/1butcdjxmu114).

## Azure SQL 데이터베이스에서 샘플 테이블 만들기

1. 시작하려면 Azure SQL 데이터베이스에서 두 개의 샘플 테이블을 만듭니다. [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) 또는 Visual Studio를 사용하여 Azure SQL 데이터베이스에 연결한 후 다음 쿼리를 실행합니다.

	**Table1 만들기**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Table2 만들기**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. **Table1**에서 몇 가지 샘플 데이터를 추가합니다. **Table2**를 빈 상태로 둡니다. **Table1**에서 Data Lake 저장소로 데이터를 가져옵니다. 그런 다음 Data Lake 저장소에서 **Table2**로 데이터를 내보냅니다. 다음 조각을 실행합니다.

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Data Lake 저장소 계정에 액세스할 수 있는 HDInsight 클러스터에서 Sqoop를 사용합니다.

HDInsight 클러스터에는 사용 가능한 Sqoop 패키지가 이미 있습니다. Data Lake 저장소를 추가 저장소로 사용하도록 HDInsight 클러스터를 구성한 경우 Sqoop(구성 변경 없이)를 사용하여 관계형 데이터베이스(이 예제의 경우 Azure SQL 데이터베이스)와 Data Lake 저장소 계정 간에 데이터를 가져오고 내보낼 수 있습니다.

1. 이 자습서에서는 Linux 클러스터를 만든 것으로 가정하므로 SSH를 사용하여 클러스터에 연결해야 합니다. [Linux 기반 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)을 참조하세요.

2. 클러스터에서 Data Lake 저장소 계정에 액세스할 수 있는지 확인합니다. SSH 프롬프트에서 다음 명령을 실행합니다.

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	데이터 레이크 저장소 계정의 파일/폴더 목록이 제공되어야 합니다.

### Azure SQL 데이터베이스에서 Data Lake 저장소로 데이터 가져오기

3. Sqoop 패키지를 사용할 수 있는 디렉터리로 이동합니다. 일반적으로 `/usr/hdp/<version>/sqoop/bin`에 있습니다.

4. **Table1**에서 Data Lake 저장소 계정으로 데이터를 가져옵니다. 다음 구문을 사용합니다.

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	**sql-database-server-name** 자리 표시자는 Azure SQL 데이터베이스가 실행 중인 서버의 이름을 나타내고, **sql-database-name** 자리 표시자는 실제 데이터베이스 이름을 나타냅니다.

	예를 들면 다음과 같습니다.

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. 데이터가 Data Lake 저장소 계정으로 전송되었는지 확인합니다. 다음 명령을 실행합니다.

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	다음 출력이 표시되어야 합니다.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	각 **part-m-*** 파일은 원본 테이블 **Table1**의 행에 해당합니다. 확인할 part-m-* 파일의 내용을 볼 수 있습니다.


### Data Lake 저장소에서 Azure SQL 데이터베이스로 데이터 내보내기

6. Data Lake Store 계정에서 Azure SQL 데이터베이스의 빈 테이블 **Table2**로 데이터를 내보냅니다. 다음 구문을 사용합니다.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	예를 들면 다음과 같습니다.

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. 데이터가 SQL 데이터베이스 테이블에 업로드되었는지 확인합니다. [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) 또는 Visual Studio를 사용하여 Azure SQL 데이터베이스에 연결한 후 다음 쿼리를 실행합니다.

		
		SELECT * FROM TABLE2

	다음 출력이 표시됩니다.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## 참고 항목

- [Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->