<properties
   pageTitle="Azure Data Factory를 사용하여 HDInsight에서 주문형 Linux 기반 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure Data Factory를 사용하여 주문형 HDInsight 클러스터를 만드는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# Azure Data Factory를 사용하여 HDInsight에서 주문형 Linux 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md)는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 이 문서에서는 Azure Data Factory를 사용하여 [Azure HDInsight 주문형 연결된 서비스](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 만들고 클러스터를 사용하여 Hive 작업을 실행하는 방법에 대해 알아봅니다. 다음은 대략적인 흐름입니다.

1. 요청 시 HDInsight 클러스터를 만듭니다.
2. Hive 작업을 실행하여 원본 Blob 저장소 계정에서 원시 웹 로그 데이터를 읽고, 데이터를 변환하며 출력을 대상 Blob 저장소 계정에 씁니다.
3. TTL(Time-to-Live) 설정에 따라 클러스터를 삭제합니다.

데이터 팩터리 파이프라인에 정의된 Hive 작업은 미리 정의된 HiveQL 스크립트를 호출합니다. 스크립트는 Azure Blob 저장소에 저장된 원시 웹 로그 데이터를 참조하는 외부 테이블을 만든 다음 원시 데이터를 연도별 및 월별로 분할합니다.

다음은 입력 파일의 각 월에 대한 샘플 행입니다.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

스크립트는 이전 입력을 기반으로 세 개의 출력 폴더를 만듭니다. 각 폴더에는 각 월의 항목과 함께 파일이 포함되어 있습니다.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Hive 작업 외에도 데이터 팩터리의 데이터 변환 활동 목록은 [Azure Data Factory를 사용한 분석 및 변환](../data-factory/data-factory-data-transformation-activities.md)을 참조하세요.

데이터 팩터리와 함께 HDInsight를 사용할 때 많은 이점이 있습니다.

- HDInsight 클러스터 결제는 사용 여부에 관계없이 분당으로 비례 배분됩니다. 데이터 팩터리를 사용하여 클러스터가 요청 시 만들어집니다. 그리고 작업이 완료되면 클러스터가 자동으로 삭제됩니다. 따라서 작업 실행 시간 및 잠깐의 유휴 시간(TTL)에 대해서만 지불합니다.
- 데이터 팩터리 파이프라인을 사용하여 워크플로를 만들 수 있습니다.
- 재귀 작업을 예약할 수 있습니다.

##필수 조건:

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- [Azure 구독](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure CLI 또는 Azure PowerShell.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##저장소 계정 준비

이 시나리오에서는 최대 3개의 저장소 계정을 사용할 수 있습니다.

- HDInsight 클러스터에 대한 기본 저장소 계정
- 입력 데이터에 대한 저장소 계정
- 출력 데이터에 대한 저장소 계정

자습서를 간단하게 만들기 위해 1개의 저장소 계정을 3가지 용도로 사용합니다. 이 섹션에 있는 Azure CLI 및 Azure PowerShell 샘플 스크립트는 다음을 수행합니다.

1. Azure에 로그인
2. Azure 리소스 그룹 만들기
3. Azure 저장소 계정 만들기
4. 저장소 계정에 Blob 컨테이너 만들기
5. Blob 컨테이너에 다음 두 파일 복사

    - 입력 데이터 파일: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - HiveQL 스크립트: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    두 파일은 공용 Blob 컨테이너에 저장되어 있습니다.

>[AZURE.IMPORTANT] 스크립트에 사용된 리소스 그룹 이름, 저장소 계정 이름 및 저장소 계정 키를 적어 둡니다. 다음 섹션에 필요합니다.

**저장소를 준비하고 Azure CLI를 사용하여 파일을 복사하려면**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

컨테이너 이름은 *adfgetstarted*입니다. 그대로 유지합니다. 그렇지 않으면 Resource Manager 템플릿을 업데이트해야 합니다.

이 CLI 스크립트에 대해 도움이 필요한 경우 [Azure 저장소에서 Azure CLI 사용](../storage/storage-azure-cli.md)을 참조하세요.

**저장소를 준비하고 Azure PowerShell을 사용하여 파일을 복사하려면**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = (Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName)[0].Value

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

이 PowerShell 스크립트에 대해 도움이 필요한 경우 [Azure 저장소에서 Azure PowerShell 사용](../storage/storage-powershell-guide-full.md)을 참조하세요.

**저장소 계정 및 그 내용을 검사하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹**을 클릭합니다.
3. CLI 또는 PowerShell 스크립트에서 만든 리소스 그룹 이름을 두 번 클릭합니다. 나열된 리소스 그룹이 너무 많은 경우 필터를 사용합니다.
4. **리소스** 타일에서 리소스 그룹을 다른 프로젝트와 공유하지 않는 한 하나의 리소스가 나열됩니다. 이전에 지정한 이름의 저장소 계정입니다. 저장소 계정 이름을 클릭합니다.
5. **Blob** 타일을 클릭합니다.
6. **adfgetstarted** 컨테이너를 클릭합니다. **입력 데이터** 및 **스크립트**라는 두 폴더가 표시됩니다.
7. 폴더를 열고 폴더에서 파일을 확인합니다.
 
## 데이터 팩터리 만들기

저장소 계정, 입력 데이터 및 HiveQL 스크립트가 준비되었으면 Azure Data Factory를 만들 준비가 된 것입니다. 데이터 팩터리는 여러 가지 방법으로 만들 수 있습니다. 이 자습서에서는 Azure 포털을 사용하여 사용자 지정 Resource Manager 템플릿을 호출합니다. 또한 [Azure CLI](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) 및 [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell)에서 Resource Manager 템플릿을 호출할 수도 있습니다. 기타 데이터 팩터리 만들기 방법은 [자습서: 첫 번째 데이터 팩터리 빌드](../data-factory/data-factory-build-your-first-pipeline.md)를 참조하세요.

최상위 수준 Resource Manager 템플릿은 다음을 포함합니다.

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

*hdinsight-hive-on-demand*라는 하나의 데이터 팩터리 리소스를 포함합니다(이름은 스크린샷에 표시되지 않음). 데이터 팩터리는 현재 미국 서부 하위 지역 및 북유럽 하위 지역에서만 지원됩니다.

*hdinsight-hive-on-demand* 리소스는 4개의 리소스를 포함합니다.

- 기본 HDInsight 저장소 계정, 입력 데이터 저장소 및 출력 데이터 저장소로 사용할 저장소 계정에 대한 linkedservice.
- 만들 HDInsight 클러스터에 대한 linkedservice

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    지정되지 않은 경우에도 클러스터가 저장소 계정과 동일한 하위 지역에 만들어집니다.
    
    *timeToLive* 설정을 확인합니다. 데이터 팩터리는 클러스터가 30분 동안 유휴 상태가 지속되면 클러스터를 자동으로 삭제합니다.
- 입력 데이터에 대한 데이터 집합. 파일 이름과 폴더 이름을 여기에서 정의합니다.

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- 출력 데이터에 대한 데이터 집합과 데이터 처리를 위한 파이프라인. 출력 경로를 여기에서 정의합니다.
        
        "folderPath": "adfgetstarted/partitioneddata",

    [데이터 집합 가용성](../data-factory/data-factory-create-datasets.md#Availability) 설정은 다음과 같습니다.
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    Azure Data Factory에서 출력 데이터 집합 가용성이 파이프라인을 유도합니다. 즉, 월의 마지막 날에 조각이 매달 생성됩니다. 자세한 내용은 [데이터 팩터리 예약 및 실행](../data-factory/data-factory-scheduling-and-execution.md)을 참조하세요.

    파이프라인 정의는 다음과 같습니다.
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    하나의 작업이 포함됩니다. 작업의 *start* 및 *end*는 모두 과거 날짜이며 이것은 조각이 하나뿐이라는 것을 의미합니다. end가 미래 날짜이면 데이터 팩터리는 시간이 되면 다른 조각을 만듭니다. 자세한 내용은 [데이터 팩터리 예약 및 실행](../data-factory/data-factory-scheduling-and-execution.md)을 참조하세요.

    다음은 작업 정의입니다.
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    입력, 출력 및 스크립트 경로가 정의됩니다.
    
**데이터 팩터리를 만들려면**

1. Azure에 로그인하여 Azure 포털에서 Azure Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다. 템플릿은 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json에 있습니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 마지막 섹션에서 만든 계정에 대해 **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** 및 **STORAGEACCOUNTKEY**를 입력한 후 **확인**을 클릭합니다. 데이터 팩터리 이름은 전역적으로 고유해야 합니다.
3. **리소스 그룹**에서, 마지막 섹션에서 사용한 동일한 리소스 그룹을 선택합니다.
4. **약관**을 클릭한 후에 **만들기**를 클릭합니다.
5. **만들기**를 클릭합니다. 대시보드에 **템플릿 배포 배포 중**이라는 타일이 표시됩니다. 타일 텍스트가 리소스 그룹 이름으로 변경될 때까지 기다립니다. HDInsight 클러스터를 만들려면 보통 약 20분 정도 걸립니다.
6. 타일을 클릭하여 리소스 그룹을 엽니다. 이제 저장소 계정 리소스 외에 하나 이상의 데이터 팩터리 리소스가 나열됩니다.
7. **hdinsight-hive-on-demand**를 클릭합니다.
8. **다이어그램** 타일을 클릭합니다. 다이어그램은 입력 데이터 집합 및 출력 데이터 집합이 있는 하나의 작업을 보여 줍니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 다이어그램](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    이름은 Resource Manager 템플릿에 정의됩니다.
9. **AzureBlobOutput**을 두 번 클릭합니다.
10. **최근 업데이트된 조각**에 하나의 조각이 표시됩니다. 상태가 **진행 중**이면 **준비**로 변경될 때까지 기다립니다.

**데이터 팩터리 출력을 확인하려면**

1. 마지막 세션에서와 동일한 절차를 사용하여 Adfgetstarted 컨테이너의 포함을 확인합니다. **adfgetsarted** 외에도 두 개의 새 컨테이너가 있습니다.

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx: HDInsight 클러스터에 대한 기본 컨테이너입니다. 기본 컨테이너 이름은 "adf>yourdatafactoryname>-linkedservicename-datetimestamp" 패턴을 따릅니다.
    - adfjobs: ADF 작업 로그에 대한 컨테이너입니다.
    
    데이터 팩터리 출력은 Resource Manager 템플릿에 구성된 대로 afgetstarted에 저장됩니다.
2. **adfgetstarted**를 클릭합니다.
3. **partitioneddata**를 두 번 클릭합니다. 모든 웹 로그가 2014년에 지정되었으므로 **year=2014** 폴더가 표시됩니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 출력](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    목록을 드릴다운하면 1월, 2월 및 3월에 대한 3개 폴더가 표시됩니다. 각 월에 대한 로그가 있습니다.

    ![Azure Data Factory HDInsight 주문형 hive 작업 파이프라인 출력](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##자습서 정리

주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(timeToLive)가 없는 한 조각을 처리해야 할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 클러스터가 삭제됩니다. 각 클러스터에 대해 Azure Data Factory는 클러스터에 대한 기본 파일 시스템으로 사용되는 Azure Blob 저장소를 만듭니다. HDInsight 클러스터가 삭제되어도 기본 Blob 저장소 컨테이너와 연결된 저장소 계정은 삭제되지 않습니다. 의도적인 작동입니다. 점점 더 많은 조각이 처리될수록 Azure Blob 저장소에 컨테이너가 많아집니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 저장소 비용을 줄이기 위해 삭제할 수 있습니다. 이 컨테이너의 이름은 "adfyourdatafactoryname-linkedservicename-datetimestamp" 패턴을 따릅니다.

[Azure Resource Manager](../resource-group-overview.md)는 솔루션을 그룹으로 배포, 관리 및 모니터링하는 데 사용됩니다. 리소스 그룹을 삭제하면 그룹 내의 모든 구성 요소가 삭제됩니다.

**리소스 그룹을 삭제하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹**을 클릭합니다.
3. CLI 또는 PowerShell 스크립트에서 만든 리소스 그룹 이름을 두 번 클릭합니다. 나열된 리소스 그룹이 너무 많은 경우 필터를 사용합니다. 새 블레이드에서 리소스 그룹을 엽니다.
4. **리소스** 타일에서 리소스 그룹을 다른 프로젝트와 공유하지 않는 한 기본 저장소 계정과 데이터 팩터리가 나열됩니다.
5. 블레이드 맨 위에서 **삭제**를 클릭합니다. 이렇게 하면 저장소 계정 및 저장소 계정에 저장된 데이터도 삭제됩니다.
6. 리소스 그룹 이름을 입력하고 **삭제**를 클릭합니다.

리소스 그룹을 삭제할 때 저장소 계정을 삭제하지 않으려는 경우 기본 저장소 계정에서 비즈니스 데이터를 구분하여 다음 아키텍처 디자인을 고려할 수 있습니다. 이 경우 비즈니스 데이터가 있는 저장소 계정에 대해 하나의 리소스 그룹을 포함하고 기본 저장소 계정 및 데이터 팩터리에 대해 다른 리소스 그룹을 포함합니다. 두 번째 리소스 그룹을 삭제할 경우 비즈니스 데이터 저장소 계정에 영향을 주지 않습니다. 이렇게 하려면 다음을 수행합니다.

- 다음을 Resource Manager 템플릿에서 Microsoft.DataFactory/datafactories 리소스와 함께 최상위 수준 리소스 그룹에 추가합니다. 그러면 새 저장소 계정이 만들어집니다.

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- 새 저장소 계정에 새 연결된 서비스 지점을 추가합니다.

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- 추가 dependsOn 및 additionalLinkedServiceNames로 HDInsight 주문형 연결된 서비스를 구성합니다.

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##다음 단계
이 문서에서는 Azure Data Factory를 사용하여 주문형 HDInsight 클러스터를 만들고 Hive 작업을 처리하는 방법을 알아보았습니다. 자세히 알아보려면 다음을 참조하세요.

- [Hadoop 자습서: HDInsight에서 Linux 기반 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
- [HDInsight 설명서](https://azure.microsoft.com/documentation/services/hdinsight/)
- [데이터 팩터리 설명서](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0914_2016-->