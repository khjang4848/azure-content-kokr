<properties
	pageTitle="데이터 팩터리 자습서: 첫 번째 데이터 파이프라인 | Microsoft Azure"
	description="이 Azure 데이터 팩터리 자습서에서는 Hadoop 클러스터에서 Hive 스크립트를 사용하여 데이터를 처리하는 데이터 팩터리를 만들고 예약하는 방법을 보여 줍니다."
	services="data-factory"
	keywords="Azure 데이터 팩터리 자습서, Hadoop 클러스터, Hadoop Hive"
	documentationCenter=""
	authors="spelluru"
	manager=""
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/06/2016"
	ms.author="spelluru"/>

# 자습서: Hadoop 클러스터를 사용하여 데이터를 처리하는 첫 번째 파이프라인 빌드 
> [AZURE.SELECTOR]
- [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
- [Azure 포털](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

이 자습서에서는 Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 데이터를 처리하는 데이터 파이프라인으로 첫 번째 Azure 데이터 팩터리를 구축합니다.

> [AZURE.NOTE] 이 문서는 Azure Data Factory에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 개념적 개요는 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요. 데이터 팩터리에 관해 알아보기 위해 콘텐츠를 탐색할 권장 방법은 [데이터 팩터리 학습 경로](https://azure.microsoft.com/documentation/learning-paths/data-factory/)를 참조하세요.

## 이 자습서에서 다루는 내용	
**Azure Data Factory**를 사용하면 데이터 **이동** 및 데이터 **처리** 작업을 데이터 기반 워크플로(데이터 파이프라인이라고도 함)로 작성할 수 있습니다. 데이터 처리(또는 데이터 변환) 작업으로 첫 번째 데이터 파이프라인을 빌드하는 방법에 대해 배웁니다. 이 작업은 HDInsight Hadoop 클러스터를 사용하여 샘플 웹 로그를 변환 및 분석합니다.

이 자습서에서는 다음 단계를 수행합니다.

1.	**데이터 팩터리**를 만듭니다. 데이터 팩터리는 데이터를 이동 및 처리하는 하나 이상의 데이터 파이프라인을 포함할 수 있습니다.
2.	**연결된 서비스** 만들기. 연결된 서비스를 만들어서 데이터 저장소 또는 계산 서비스를 데이터 팩터리에 연결합니다. Azure 저장소와 같은 데이터 저장소는 파이프라인에서 활동의 입/출력 데이터를 저장합니다. HDInsight Hadoop cluster 클러스터와 같은 계산 서비스는 데이터를 처리/변환합니다.
3.	입력 및 출력 **데이터 집합**을 만듭니다. 입력 데이터 집합은 파이프라인의 작업에 대한 입력을 나타내고 출력 데이터 집합은 작업에 대한 출력을 나타냅니다.
3.	**파이프라인** 만들기. 파이프라인에는 하나 이상의 활동(예: 복사 활동, HDInsight Hive 활동)이 포함될 수 있습니다. 이 샘플에서는 HDInsight Hadoop 클러스터에서 Hive 스크립트를 실행하는 HDInsight Hive 작업을 사용합니다. 스크립트는 먼저 Azure Blob Storage에 저장된 원시 웹 로그 데이터를 참조하는 테이블을 만든 다음 원시 데이터를 연도별 및 월별로 분할합니다.

	Azure 데이터 팩터리에서 지원하는 작업 환경은 두 가지 유형이 있습니다. 그 두 가지 유형은 [데이터 이동 작업](data-factory-data-movement-activities.md) 및 [데이터 변환 작업](data-factory-data-transformation-activities.md)입니다. 데이터 이동 작업은 복사 작업 하나 밖에 없습니다. 이 자습서에서는 복사 작업을 사용하지 않습니다. 복사 작업 사용에 대한 자습서는 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 이 자습서에서 사용하는 HDInsight Hive 작업은 데이터 팩터리에서 지원하는 데이터 변환 작업 중 하나입니다.
 
다음은 이 자습서에서 빌드한 샘플 데이터 팩터리의 **다이어그램 뷰**입니다.

![데이터 팩터리 자습서에서 다이어그램 보기](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

이 자습서에서 **adfgetstarted** Azure blob 컨테이너의 **inputdata** 폴더에는 input.log라는 하나의 파일이 들어 있습니다. 이 로그 파일은 2016년 1월, 2월 및 3월 등 세 달에 항목이 있습니다. 다음은 입력 파일의 각 월에 대한 샘플 행입니다.

	2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
	2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
	2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

HDInsight Hive 작업을 사용하여 파이프라인에서 파일이 처리될 때 작업은 입력 데이터를 연도별 및 월별로 분할하는 HDInsight 클러스터에서 Hive 스크립트를 실행합니다. 스크립트는 각 월의 항목을 가진 파일을 포함하는 세 개의 출력 폴더를 만듭니다.

	adfgetstarted/partitioneddata/year=2016/month=1/000000_0
	adfgetstarted/partitioneddata/year=2016/month=2/000000_0
	adfgetstarted/partitioneddata/year=2016/month=3/000000_0

위에 표시된 샘플 줄에서 첫 번째 줄(2016-01-01)은 월=1 폴더의 000000\_0 파일에 기록됩니다. 마찬가지로 두 번째 줄은 월=2 폴더의 파일에 기록되고 세 번째 줄은 월=3 폴더의 파일에 기록됩니다.


## 필수 구성 요소
이 자습서를 시작하기 전에 다음 필수 조건이 있어야 합니다.

1.	**Azure 구독** - Azure 구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 무료 평가판 계정을 확보하는 방법은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 문서를 참조하세요.

2.	**Azure 저장소** – 이 자습서에서는 데이터 저장을 위해 Azure Storage 계정을 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요. 저장소 계정을 만든 후 **계정 이름**과 **선택키**를 적어둡니다. [저장소 액세스 키 보기, 복사 및 다시 생성](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)을 참조하세요.

### 자습서에 대한 Azure 저장소에 파일 업로드
자습서를 시작하기 전에 자습서를 위한 샘플 파일과 함께 Azure 저장소 계정을 준비해 두어야 합니다.

1. **adfgetstarted** blob 컨테이너의 **스크립트** 폴더에 Hive 쿼리 파일(HQL)을 업로드합니다.
2. **adfgetstarted** blob 컨테이너의 **inputdata** 폴더에 입력 파일을 업로드합니다.

#### HQL 스크립트 파일 만들기 

1. **메모장**을 시작하고 다음의 HQL 스크립트를 붙여넣습니다. 이 Hive 스크립트는 **WebLogsRaw** 및 **WebLogsPartitioned**라는 두 개의 테이블을 만듭니다. 메뉴에서 **파일**을 클릭하고 **다른이름으로**를 선택합니다. 하드 드라이브의 **C:\\adfgetstarted** 폴더로 전환합니다. **형식으로 저장** 필드에서 **모든 파일 (*.*)**을 선택합니다. **파일 이름**에 **partitionweblogs.hql**을 입력합니다. 대화 상자의 아래쪽에서 **인코딩** 필드가 **ANSI**로 설정된 것을 확인합니다. 그렇지 않은 경우 **ANSI**로 설정합니다.

		set hive.exec.dynamic.partition.mode=nonstrict;
		
		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		tblproperties ("skip.header.line.count"="2");
		
		LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';
		
		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

런타임에 Data Factory 파이프라인의 Hive 활동은 다음 코드 조각에 나와 있는 것처럼 **inputtable** 및 **partitionedtable** 매개 변수의 값을 전달합니다.

		"inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		"partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

**storageaccountname**은 Azure 저장소 계정 이름입니다.
 
#### 샘플 입력 파일 만들기
메모장을 사용하여 **c:\\adfgetstarted**에 다음과 같은 내용으로 **input.log**라는 파일을 만듭니다.

	#Software: Microsoft Internet Information Services 8.0
	#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
	2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### Azure Blob 저장소에 입력 파일 및 HQL 파일 업로드

이 섹션에서는 **AzCopy** 도구를 사용하여 Azure Blob Storage에 input.log 및 partitionweblogs.hql 파일을 복사하기 위한 지침을 제공합니다. 사용자가 원하는 아무 도구나 사용하여 이 작업을 수행할 수 있습니다(예: [Microsoft Azure Storage 탐색기](http://storageexplorer.com/), [ClumsyLeaf Software CloudXPlorer](http://clumsyleaf.com/products/cloudxplorer)).
	 
1. [최신 버전의 **AzCopy**](http://aka.ms/downloadazcopy) 또는 [최신 미리 보기 버전](http://aka.ms/downloadazcopypr)을 다운로드합니다. 유틸리티 사용 지침은 [AzCopy 사용 방법](../storage/storage-use-azcopy.md)을 참조하세요.
2. c:\\adfgetstarted 폴더로 이동하여 다음 명령을 실행합니다.

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

	**input.log** 파일을 저장소 계정(**adfgetstarted** 컨테이너 및 **inputdata** 폴더)에 업로드합니다. **storageAccountName**을 해당 저장소 계정의 이름으로 바꾸고 **storageaccesskey**를 저장소 선택키로 바꿉니다.

	> [AZURE.NOTE] 이 명령은 Azure Blob 저장소에 이름이 **adfgetstarted**인 컨테이너를 만들고 로컬 드라이브에서 컨테이너의 **inputdata** 폴더에 **input.log** 파일을 복사합니다.
	
3. 파일이 성공적으로 업로드되면 AzCopy에서 다음과 비슷한 출력이 표시됩니다.
	
		Finished 1 of total 1 file(s).
		[2015/12/16 23:07:33] Transfer summary:
		-----------------
		Total files transferred: 1
		Transfer successfully:   1
		Transfer skipped:        0
		Transfer failed:         0
		Elapsed time:            00.00:00:01
5. 다음 명령을 실행하여 **adfgetstarted** 컨테이너의 **script** 폴더에 **partitionweblogs.hql** 파일을 업로드합니다. 명령은 다음과 같습니다.
	
		AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

<!---HONumber=AcomDC_0921_2016-->