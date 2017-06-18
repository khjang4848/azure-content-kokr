<properties 
   pageTitle="Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작 | Azure" 
   description="Azure PowerShell을 사용하여 데이터 레이크 저장소 계정을 만들고, U-SQL을 사용하여 데이터 레이크 분석 작업을 만들고, 작업을 제출하는 방법에 대해 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# 자습서: Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell을 사용하여 Azure Data Lake 분석 계정을 만들고, [U-SQL](data-lake-analytics-u-sql-get-started.md)로 Data Lake 분석 작업을 정의하고, 작업을 Data Lake 분석 계정에 제출하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

이 자습서에서는 TSV(탭 분리 값) 파일을 읽고 CSV(쉼표로 구분된 값) 파일로 변환하는 작업을 개발합니다. 지원되는 다른 도구를 사용하여 같은 자습서를 진행하려면 이 섹션의 맨 위에 있는 탭을 클릭하세요.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.
	
##데이터 레이크 분석 계정 만들기

모든 작업을 실행하기 전에 Data Lake Analytics 계정이 있어야 합니다. 데이터 레이크 분석 계정을 만들려면 다음을 지정해야 합니다.

- **Azure 리소스 그룹**: Azure 리소스 그룹 내에서 데이터 레이크 분석 계정을 만들어야 합니다. [Azure 리소스 관리자](../resource-group-overview.md)를 사용하면 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

	구독에서 리소스 그룹을 나열하려면:
    
    	Get-AzureRmResourceGroup
    
	새 리소스 그룹을 만들려면 다음을 수행합니다.

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **데이터 레이크 분석 계정 이름**
- **위치**: 데이터 레이크 분석을 지원하는 Azure 데이터 센터 중 하나입니다.
- **기본 데이터 레이크 계정**: 각 데이터 레이크 분석 계정에는 기본 데이터 레이크 계정이 있습니다.

	새 데이터 레이크 계정을 만들려면:

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE] 데이터 레이크 계정 이름은 소문자와 숫자만 포함해야 합니다.



**데이터 레이크 분석 계정을 만들려면**

1. Windows 워크스테이션에서 PowerShell ISE를 엽니다.
2. 다음 스크립트를 실행합니다.

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
		Write-Host "Create a resource group ..." -ForegroundColor Green
		New-AzureRmResourceGroup `
			-Name  $resourceGroupName `
			-Location $location
		
		Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
		New-AzureRmDataLakeStoreAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeStoreName `
			-Location $location 
		
		Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
		New-AzureRmDataLakeAnalyticsAccount `
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##데이터 레이크에 데이터 업로드

이 자습서에서는 몇 가지 검색 로그를 처리합니다. 검색 로그는 데이터 레이크 저장소 또는 Azure Blob 저장소에 저장할 수 있습니다.

샘플 검색 로그 파일이 공용 Azure Blob 컨테이너에 복사되었습니다. 다음 PowerShell 스크립트를 사용하여 파일을 사용자의 워크스테이션에 다운로드한 다음 데이터 레이크 분석 계정의 기본 데이터 레이크 저장소 계정에 업로드합니다.

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

다음 PowerShell 스크립트에서는 데이터 레이크 분석 계정에 대한 기본 데이터 레이크 저장소 이름을 가져오는 방법을 보여 줍니다.


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
	echo $dataLakeStoreName

>[AZURE.NOTE] Azure 포털은 기본 데이터 레이크 저장소 계정에 샘플 데이터 파일을 복사하는 사용자 인터페이스를 제공합니다. 자세한 내용은 [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account)을 참조하세요.

데이터 레이크 분석은 Azure Blob 저장소에 액세스할 수도 있습니다. Azure Blob 저장소에 데이터를 업로드하려면 [Azure 저장소와 Azure PowerShell 사용](../storage/storage-powershell-guide-full.md)을 참조하세요.

##데이터 레이크 분석 작업 제출

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

1. Windows 워크스테이션에서 PowerShell ISE를 엽니다.
2. 다음 스크립트를 실행합니다.

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		$job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

		Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	스크립트에서 U-SQL 스크립트 파일은 c:\\tutorials\\data-lake-analytics\\copyFile.usql에 저장됩니다. 이에 따라 파일 경로를 업데이트합니다.
 
작업이 완료된 후 다음 cmdlet을 사용하여 파일을 나열하고 다운로드할 수 있습니다.
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## 참고 항목

- 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
- 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
- U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
- 관리 작업을 보려면 [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
- 데이터 레이크 분석에 대한 개요를 보려면 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->