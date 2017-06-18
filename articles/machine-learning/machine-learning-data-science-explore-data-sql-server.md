<properties 
	pageTitle="Azure의 SQL Server 가상 컴퓨터에서 데이터 탐색 | Microsoft Azure" 
	description="Azure에서 SQL Server VM에 저장된 데이터를 탐색하는 방법입니다." 
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
	ms.date="09/13/2016" 
	ms.author="bradsev" />

#Azure의 SQL Server 가상 컴퓨터에서 데이터 탐색


이 문서에서는 Azure에서 SQL Server VM에 저장된 데이터를 탐색하는 방법을 다룹니다. 이렇게 하려면 SQL을 사용하여 데이터 랭글링을 수행하거나 Python과 같은 프로그래밍 언어를 사용합니다.

다음 **메뉴**는 다양한 저장소 환경에서 데이터를 탐색하기 위해 도구를 사용하는 방법을 설명하는 토픽에 연결되는 링크입니다. 이 작업은 Cortana 분석 프로세스(CAP)의 한 단계입니다.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] 이 문서의 샘플 SQL 문에서는 데이터가 SQL Server에 있는 것으로 가정합니다. 그렇지 않은 경우 데이터를 SQL Server로 이동하는 방법은 클라우드 데이터 과학 프로세스 맵을 참조하세요.



## <a name="sql-dataexploration"></a>SQL 스크립트로 SQL 데이터 탐색

SQL Server에서 데이터 저장소를 탐색하는 데 사용할 수 있는 몇 가지 샘플 SQL 스크립트는 다음과 같습니다.

1. 일별 관찰 수 가져오기

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. 범주 열의 수준 가져오기

	`select  distinct <column_name> from <databasename>`

3. 두 범주 열 조합의 수준 수 가져오기

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. 숫자 열의 분포 가져오기

	`select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] 실용적인 예제에는 [NYC Taxi 데이터 집합](http://www.andresmh.com/nyctaxitrips/)을 사용할 수 있으며, 종단 간 연습에 [IPython Notebook 및 SQL Server를 사용한 NYC 데이터 랭글링](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)이라는 IPNB를 참조할 수 있습니다.

##<a name="python"></a>Python으로 SQL 데이터 탐색

데이터가 SQL Server에 있는 경우 Python을 사용하여 데이터를 탐색하고 기능을 생성하는 작업은 [데이터 과학 환경에서 Azure Blob 데이터 처리](machine-learning-data-science-process-data-blob.md)에 설명된 대로 Python을 사용하여 Azure Blob의 데이터를 처리하는 것과 유사합니다. 데이터베이스에서 Pandas DataFrame으로 데이터를 로드해야 하며, 그런 다음 데이터를 추가로 처리할 수 있습니다. 데이터베이스에 연결하여 데이터 프레임으로 데이터를 로드하는 프로세스는 이 섹션에 설명되어 있습니다.

다음 연결 문자열 형식은 pyodbc를 사용(servername, dbname, username 및 password를 특정 값으로 대체)하여 Python에서 SQL Server 데이터베이스 연결하는 데 사용될 수 있습니다.

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python의 [Pandas 라이브러리](http://pandas.pydata.org/)에서는 Python 프로그래밍용 데이터 조작을 위한 다양한 데이터 구조 및 데이터 분석 도구 집합을 제공합니다. 다음 코드는 SQL Server 데이터베이스에서 Pandas 데이터 프레임으로 반환되는 결과를 읽습니다.

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

이제 [데이터 과학 환경에서 Azure Blob 데이터 처리](machine-learning-data-science-process-data-blob.md) 토픽에 설명된 대로 Pandas DataFrame으로 작업할 수 있습니다.

## 실행 중인 Cortana 분석 프로세스 예

공용 데이터 집합을 사용하여 Cortana 분석 프로세스의 종단 간 연습 예제는 [실행 중인 팀 데이터 과학 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md)을 참조하세요.

 

<!---HONumber=AcomDC_0914_2016-->