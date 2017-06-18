<properties 
   pageTitle="Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리 | Azure" 
   description="데이터 레이크 분석 작업, 데이터 원본, 사용자를 관리하는 방법에 대해 알아봅니다. " 
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

# Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.


<!-- ################################ -->
<!-- ################################ -->


##Azure PowerShell 1.0 이상 설치

[Azure 리소스 관리자로 Azure PowerShell 사용](powershell-azure-resource-manager.md#prerequisites)의 필수 조건 섹션을 참조하세요.
	
## 계정 관리

데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다. 작업이 실행되는 시간에 대해서만 비용을 지불합니다. 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

###계정 만들기

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
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
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

Azure 리소스 그룹 템플릿을 사용할 수도 있습니다. Data Lake 분석 계정 및 종속 Data Lake 저장소 계정에 대한 템플릿은 [부록 A](#appendix-a)에 있습니다. .json 템플릿을 사용하여 템플릿을 파일에 저장한 후 다음 PowerShell 스크립트를 사용하여 호출합니다.


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###계정 나열

현재 구독 내의 데이터 레이크 분석 계정 나열

	Get-AzureRmDataLakeAnalyticsAccount
	
출력은 다음과 같습니다.

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

특정 리소스 그룹 내의 데이터 레이크 분석 계정 나열

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

특정 데이터 레이크 분석 계정의 세부 정보 가져오기

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

특정 데이터 레이크 분석 계정의 존재 여부 테스트

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

cmdlet이 **True** 또는 **False**를 반환합니다.

###데이터 레이크 분석 계정 삭제

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Data Lake 분석 계정을 삭제해도 종속 Data Lake 저장소 계정은 삭제되지 않습니다. 다음 예제에서는 데이터 레이크 분석 계정 및 기본 데이터 레이크 저장소 계정을 삭제합니다.

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## 계정 데이터 원본 관리

데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

- [Azure 데이터 레이크 저장소](data-lake-storage-overview.md)
- [Azure 저장소](storage-introduction.md)

분석 계정을 만들 때 Azure 데이터 레이크 저장소 계정이 기본 저장소 계정이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 분석 계정을 만든 후 데이터 레이크 저장소 계정 및/또는 Azure 저장소 계정을 더 추가할 수 있습니다.

### 기본 데이터 레이크 저장소 계정 찾기

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### Azure Blob 저장소 계정 추가

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### 데이터 레이크 저장소 계정 추가

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### 데이터 원본 나열:

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## 작업 관리

작업을 만들려면 데이터 레이크 분석 계정이 있어야 합니다. 자세한 내용은 [데이터 레이크 분석 계정 관리](#manage-data-lake-analytics-accounts)를 참조하세요.

### 작업 나열

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### 작업 세부 정보 가져오기

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
	
### 작업 제출

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE] 작업의 기본 우선 순위는 1000이고 작업에 대한 기본 병렬 처리 수준은 1입니다.


### 작업 취소

	Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
		-JobID $jobID


## 카탈로그 항목 관리

U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. 자세한 내용은 [U-SQL 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.

###카탈로그 항목 나열

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###카탈로그 항목 세부 정보 가져오기 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-Account $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###카탈로그 항목의 존재 여부 테스트

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-Account $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###카탈로그 암호 만들기
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### 카탈로그 암호 수정
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###카탈로그 암호 삭제
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-Account $adlAnalyticsAccountName `
			-DatabaseName "master"


## Azure 리소스 관리자 그룹 사용

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

##부록 A - Data Lake 분석 ARM 템플릿

다음 ARM 템플릿은 데이터 레이크 분석 계정 및 해당 종속 데이터 레이크 저장소 계정을 배포하는 데 사용할 수 있습니다. Json 파일로 저장한 다음 PowerShell 스크립트를 사용하여 해당 템플릿을 호출합니다. 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](../resource-group-template-deploy.md#deploy-with-powershell) 및 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.

	{
	  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "adlAnalyticsName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Analytics account to create."
	      }
	    },
	    "adlStoreName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the Data Lake Store account to create."
	      }
	    }
	  },
	  "resources": [
	    {
	      "name": "[parameters('adlStoreName')]",
	      "type": "Microsoft.DataLakeStore/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ ],
	      "tags": { }
	    },
	    {
	      "name": "[parameters('adlAnalyticsName')]",
	      "type": "Microsoft.DataLakeAnalytics/accounts",
	      "location": "East US 2",
	      "apiVersion": "2015-10-01-preview",
	      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
	      "tags": { },
	      "properties": {
	        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
	        "dataLakeStoreAccounts": [
	          { "name": "[parameters('adlStoreName')]" }
	        ]
	      }
	    }
	  ],
	  "outputs": {
	    "adlAnalyticsAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
	    },
	    "adlStoreAccount": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
	    }
	  }
	}

<!---HONumber=AcomDC_0914_2016-->