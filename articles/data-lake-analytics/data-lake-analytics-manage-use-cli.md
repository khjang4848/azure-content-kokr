<properties 
   pageTitle="Azure 명령줄 인터페이스(CLI)를 사용하여 Azure 데이터 레이크 분석 관리 | Azure" 
   description="Azure CLI를 사용하여 데이터 레이크 분석 계정, 데이터 원본, 작업, 사용자를 관리하는 방법을 알아봅니다." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Azure 명령줄 인터페이스(CLI)를 사용하여 Azure 데이터 레이크 분석 관리

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure를 사용하여 Azure 데이터 레이크 분석 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure CLI**. [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
	- 이 데모를 완료하려면 **시험판** [Azure CLI 도구](https://github.com/MicrosoftBigData/AzureDataLake/releases)를 다운로드하여 설치합니다.
- **인증**. 다음 명령을 사용합니다.

		azure login
	회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.
- **Azure 리소스 관리자 모드로 전환**. 다음 명령을 사용합니다.

		azure config mode arm

**데이터 레이크 저장소 및 데이터 레이크 분석 명령을 나열하려면:**

	azure datalake store
	azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## 계정 관리

데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다. 작업이 실행되는 시간에 대해서만 비용을 지불합니다. 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

###계정 만들기

  	azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###계정 업데이트

다음 명령은 기존 데이터 레이크 분석 계정의 속성을 업데이트합니다.
  	
	azure datalake analytics account set "<Data Lake Analytics Account Name>"


###계정 나열

데이터 레이크 분석 계정을 나열합니다.

	azure datalake analytics account list

특정 리소스 그룹 내의 데이터 레이크 분석 계정 나열

	azure datalake analytics account list -g "<Azure Resource Group Name>"

특정 데이터 레이크 분석 계정의 세부 정보 가져오기

	azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###데이터 레이크 분석 계정 삭제

  	azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## 계정 데이터 원본 관리

데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

- [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
- [Azure 저장소](../storage/storage-introduction.md)

분석 계정을 만들 때 Azure 데이터 레이크 저장소 계정이 기본 저장소 계정이 되도록 지정해야 합니다. 기본 ADL 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 분석 계정을 만든 후 데이터 레이크 저장소 계정 및/또는 Azure 저장소 계정을 더 추가할 수 있습니다.

### 기본 ADL 저장소 계정 찾기

	azure datalake analytics account show "<Data Lake Analytics Account Name>"

properties:datalakeStoreAccount:name 아래에 값이 나열됩니다.

### Azure Blob 저장소 계정 추가

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE] Blob 저장소 짧은 이름만 지원됩니다. FQDN(예: “myblob.blob.core.windows.net”)을 사용하지 마십시오.

### 데이터 레이크 저장소 계정 추가

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d]는 추가되는 데이터 레이크가 기본 데이터 레이크 계정인지를 나타내는 선택적 스위치입니다.

### 기존 데이터 원본 업데이트

기존 데이터 레이크 저장소 계정을 기본으로 설정하려면:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
	  
기존 Blob 저장소 계정 키를 업데이트하려면:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### 데이터 원본 나열:

	azure datalake analytics account show "<Data Lake Analytics Account Name>"
	
![데이터 레이크 분석은 데이터 원본을 나열합니다.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### 데이터 원본 삭제:

데이터 레이크 저장소 계정을 삭제하려면:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Blob 저장소 계정을 삭제하려면:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## 작업 관리

작업을 만들려면 데이터 레이크 분석 계정이 있어야 합니다. 자세한 내용은 [데이터 레이크 분석 계정 관리](#manage-accounts)를 참조하세요.

### 작업 나열

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![데이터 레이크 분석은 데이터 원본을 나열합니다.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### 작업 세부 정보 가져오기

  	azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
	
### 작업 제출

> [AZURE.NOTE] 작업의 기본 우선 순위는 1000이고 작업에 대한 기본 병렬 처리 수준은 1입니다.

	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### 작업 취소

list 명령을 사용하여 Job ID를 찾은 후 cancel을 사용하여 작업을 취소합니다.

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## 카탈로그 관리

U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. 자세한 내용은 [U-SQL 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.
 
###카탈로그 항목 나열

	#List databases
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

	#List tables
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
	
데이터베이스, 스키마, 어셈블리, 외부 데이터 소스, 테이블, 테이블 값 함수 또는 테이블 통계가 형식에 포함됩니다.

###카탈로그 암호 만들기

	azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### 카탈로그 암호 수정

  	azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###카탈로그 암호 삭제

	azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## ARM 그룹 사용

응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. ARM(Azure 리소스 관리자)을 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

데이터 레이크 분석 서비스는 다음 구성 요소를 포함할 수 있습니다.

- Azure 데이터 레이크 분석 계정
- 필수 기본 Azure 데이터 레이크 저장소 계정
- 추가 Azure 데이터 레이크 저장소 계정
- 추가 Azure 저장소 계정

이러한 모든 구성을 쉽게 관리할 수 있도록 하나의 ARM 그룹 아래 만들 수 있습니다.

![Azure 데이터 레이크 분석 계정 및 저장소](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

데이터 레이크 분석 계정 및 종속 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다. 그러나 ARM 그룹은 다른 데이터 센터에 있을 수 있습니다.


##참고 항목 

- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->