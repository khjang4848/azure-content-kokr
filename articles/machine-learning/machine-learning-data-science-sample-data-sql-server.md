<properties 
	pageTitle="Azure의 SQL Server에서 데이터 샘플링 | Microsoft Azure" 
	description="Azure의 SQL Server에서 데이터 샘플링" 
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
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Azure의 SQL Server에서 데이터 샘플링


이 문서에서는 SQL 또는 Python 프로그래밍 언어를 사용하여 Azure의 SQL Server에 저장된 데이터를 샘플링하는 방법을 보여 줍니다. 또한 샘플링된 데이터를 파일에 저장하고, Azure blob에 업로드한 다음, Azure 기계 학습 스튜디오로 읽어 들여 Azure 기계 학습으로 이동하는 방법을 보여 줍니다.

Python 샘플링은 Azure의 SQL Sever와 [Pandas](http://pandas.pydata.org/) 라이브러리에 연결하기 위해 [pyodbc](https://code.google.com/p/pyodbc/) ODBC 라이브러리를 사용하여 샘플링을 수행합니다.

>[AZURE.NOTE] 이 문서의 샘플 SQL 코드에서는 데이터가 Azure의 SQL Server에 있는 것으로 가정합니다. 그렇지 않은 경우 Azure의 SQL Server로 데이터를 이동하는 방법에 대한 지침은 [Azure의 SQL Server로 데이터 이동](machine-learning-data-science-move-sql-server-virtual-machine.md) 토픽을 참조하세요.

**데이터를 샘플링하는 이유** 분석할 데이터 집합이 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 그러면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.

아래의 **메뉴**는 다양한 저장소 환경에서 데이터를 샘플링하는 방법을 설명하는 토픽에 연결되는 링크입니다.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)의 단계입니다.

##<a name="SQL"></a>SQL 사용

이 섹션에는 SQL을 사용하여 데이터베이스의 데이터에 대해 간단한 무작위 샘플링을 수행하는 몇 가지 방법을 설명합니다. 데이터 크기 및 해당 분포에 따라 방법을 선택하세요.

아래 두 항목은 SQL Server에서 newid를 사용하여 샘플링을 수행하는 방법을 보여 줍니다. 선택하는 방법은 샘플링할 무작위 수준에 따라 달라집니다. 아래 샘플 코드의 pk\_id는 자동으로 생성된 기본 키로 간주됩니다.

1. 낮은 수준 무작위 샘플

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. 높은 수준 무작위 샘플

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

아래 표시된 대로 Tablesample을 샘플링에 사용할 수 있습니다. 이는 데이터 크기가 큰 경우(여러 페이지의 데이터가 상관 관계가 없는 것으로 가정) 및 적당한 시간에 쿼리를 완료하는 데 보다 적합한 접근법일 수 있습니다.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] 이 샘플링된 데이터를 새 테이블에 저장하여 기능을 탐색하고 생성할 수 있습니다.


###<a name="sql-aml"></a>Azure 기계 학습에 연결

Azure 기계 학습 [데이터 가져오기][import-data] 모듈에서 위의 샘플 쿼리를 직접 사용하여 데이터를 즉시 다운 샘플링한 후 Azure 기계 학습 실험으로 가져올 수 있습니다. 판독기 모듈을 사용하여 샘플링된 데이터를 읽는 스크린 샷이 아래에 표시되어 있습니다.
   
![판독기 sql][1]

##<a name="python"></a>Python 프로그래밍 언어 사용 

이 섹션에서는 [pyodbc 라이브러리](https://code.google.com/p/pyodbc/)를 사용하여 Python에서 SQL server 데이터베이스에 ODBC 연결을 설정하는 방법을 보여줍니다. 데이터베이스 연결 문자열은 다음과 같습니다. 여기서 servername, dbname, username 및 password를 고유한 구성으로 바꿉니다.

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python의 [Pandas](http://pandas.pydata.org/) 라이브러리에서는 Python 프로그래밍용 데이터 조작을 위한 다양한 데이터 구조 및 데이터 분석 도구 집합을 제공합니다. 아래 코드는 Azure SQL 데이터베이스의 테이블에서 0.1% 샘플 데이터를 Pandas 데이터로 읽습니다.

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

이제 Pandas 데이터 프레임에서 샘플링된 데이터로 작업할 수 있습니다.

###<a name="python-aml"></a>Azure 기계 학습에 연결

다음 샘플 코드를 사용하여 다운 샘플링한 데이터를 파일에 저장한 후 Azure Blob에 업로드할 수 있습니다. [데이터 가져오기 모듈][import-data]을 사용하여 Blob의 데이터를 Azure 기계 학습 실험으로 직접 읽을 수 있습니다. 단계는 다음과 같습니다.

1. pandas 데이터 프레임을 로컬 파일에 기록합니다.

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 로컬 파일을 Azure Blob에 업로드합니다.

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 아래 화면에 표시된 대로 Azure 기계 학습 [데이터 가져오기 모듈][import-data]을 사용하여 Azure Blob에서 데이터를 읽습니다.
 
![판독기 blob][2]

## 실행 중인 팀 데이터 과학 프로세스 예제

공용 데이터 집합을 사용한 팀 데이터 과학 프로세스의 종단 간 연습 예제는 [실행 중인 팀 데이터 과학 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md)을 참조하세요.

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->