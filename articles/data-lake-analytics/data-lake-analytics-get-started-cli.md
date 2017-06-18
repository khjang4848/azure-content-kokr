<properties 
   pageTitle="Azure 명령줄 인터페이스를 사용하여 Azure 데이터 레이크 분석 시작 | Microsoft Azure" 
   description="Azure 명령줄 인터페이스를 사용하여 데이터 레이크 저장소 계정을 만들고, U-SQL을 사용하여 데이터 레이크 분석 작업을 만들고, 작업을 제출하는 방법에 대해 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# 자습서: Azure CLI(명령줄 인터페이스)를 사용하여 Azure 데이터 레이크 분석 시작

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Azure CLI를 사용하여 Azure 데이터 레이크 분석 계정을 만들고, [U-SQL](data-lake-analytics-u-sql-get-started.md)로 데이터 레이크 분석 작업을 정의하고, 작업을 데이터 레이크 분석 계정에 제출하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

이 자습서에서는 TSV(탭 분리 값) 파일을 읽고 CSV(쉼표로 구분된 값) 파일로 변환하는 작업을 개발합니다. 지원되는 다른 도구를 사용하여 같은 자습서를 진행하려면 이 섹션의 맨 위에 있는 탭을 클릭하세요.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure CLI**. [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
	- 이 데모를 완료하려면 **시험판** [Azure CLI 도구](https://github.com/MicrosoftBigData/AzureDataLake/releases)를 다운로드하여 설치합니다.
- **인증**. 다음 명령을 사용합니다.

		azure login
	회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.
- **Azure 리소스 관리자 모드로 전환**. 다음 명령을 사용합니다.

		azure config mode arm
		
## 데이터 레이크 분석 계정 만들기

모든 작업을 실행하기 전에 Data Lake Analytics 계정이 있어야 합니다. 데이터 레이크 분석 계정을 만들려면 다음을 지정해야 합니다.

- **Azure 리소스 그룹**: Azure 리소스 그룹 내에서 데이터 레이크 분석 계정을 만들어야 합니다. [Azure 리소스 관리자](../resource-group-overview.md)를 사용하면 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

	구독에서 리소스 그룹을 나열하려면:
    
    	azure group list 
    
	새 리소스 그룹을 만들려면 다음을 수행합니다.

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **데이터 레이크 분석 계정 이름**
- **위치**: 데이터 레이크 분석을 지원하는 Azure 데이터 센터 중 하나입니다.
- **기본 데이터 레이크 계정**: 각 데이터 레이크 분석 계정에는 기본 데이터 레이크 계정이 있습니다.

	기존 데이터 레이크 계정을 나열하려면
	
		azure datalake store account list

	새 데이터 레이크 계정을 만들려면:

		azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

	> [AZURE.NOTE] 데이터 레이크 계정 이름은 소문자와 숫자만 포함해야 합니다.



**데이터 레이크 분석 계정을 만들려면**

		azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

		azure datalake analytics account list
		azure datalake analytics account show "<Data Lake Analytics Account Name>"			

![데이터 레이크 분석 표시 계정](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] 데이터 레이크 분석 계정 이름은 소문자와 숫자만 포함해야 합니다.


## 데이터 레이크 저장소에 데이터 업로드

이 자습서에서는 몇 가지 검색 로그를 처리합니다. 검색 로그는 데이터 레이크 저장소 또는 Azure Blob 저장소에 저장할 수 있습니다.

Azure 포털은 검색 로그 파일을 포함하는 기본 데이터 레이크 저장소 계정에 샘플 데이터 파일을 복사하는 사용자 인터페이스를 제공합니다. 기본 데이터 레이크 저장소 계정에 데이터를 업로드하려면 [원본 데이터 준비](data-lake-analytics-get-started-portal.md#prepare-source-data)를 참조하세요.

CLI를 사용하여 파일을 업로드하려면 다음 명령을 사용합니다.

  	azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
  	azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

데이터 레이크 분석은 Azure Blob 저장소에 액세스할 수도 있습니다. Azure Blob 저장소에 데이터를 업로드하려면 [Azure 저장소와 Azure CLI 사용](../storage/storage-azure-cli.md)을 참조하세요.

## 데이터 레이크 분석 작업 제출

데이터 레이크 분석 작업은 U-SQL 언어로 작성됩니다. U-SQL에 대한 자세한 내용은 [U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md) 및 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/?LinkId=691348)를 참조하세요.

**데이터 레이크 분석 작업 스크립트를 만들려면**

- 다음 U-SQL 스크립트를 사용하여 텍스트 파일을 만들고 사용자의 워크스테이션에 텍스트 파일을 저장합니다.

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	이 U-SQL 스크립트는 **Extractors.Tsv()**를 사용하여 원본 데이터 파일을 읽은 다음 **Outputters.Csv()**를 사용하여 csv 파일을 만듭니다.
    
    원본 파일을 다른 위치에 복사하지 않는 한 두 경로를 수정하지 마세요. 출력 폴더가 없는 경우 데이터 레이크 분석에서 해당 폴더를 만듭니다.
	
	기본 데이터 레이크 계정에 저장된 파일의 상대 경로를 사용하는 것이 더 쉽습니다. 절대 경로를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    연결된 저장소 계정의 파일에 액세스하려면 절대 경로를 사용해야 합니다. 연결된 Azure 저장소 계정에 저장된 파일에 대한 구문은 다음과 같습니다.
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] 공용 Blob 또는 공용 컨테이너 액세스 권한이 있는 Azure Blob 컨테이너는 현재 지원되지 않습니다.

	
**작업을 제출하려면**


	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
다음 명령을 사용하여 작업을 나열하고, 작업 정보를 가져오고, 작업을 취소할 수 있습니다.

  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
  	azure datalake analytics job list "<Data Lake Analytics Account Name>"
	azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

작업이 완료된 후 다음 cmdlet을 사용하여 파일을 나열하고 다운로드할 수 있습니다.
	
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
	azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
	azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## 참고 항목

- 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
- 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
- U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
- 관리 작업을 보려면 [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
- 데이터 레이크 분석에 대한 개요를 보려면 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->