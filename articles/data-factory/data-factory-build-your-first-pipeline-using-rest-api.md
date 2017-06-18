<properties
	pageTitle="첫 번째 데이터 팩터리 빌드(REST) | Microsoft Azure"
	description="이 자습서에서는 데이터 팩터리 REST API를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/16/2016"
	ms.author="spelluru"/>

# 자습서: 데이터 팩터리 REST API를 사용하여 첫 번째 Azure Data Factory 빌드
> [AZURE.SELECTOR]
- [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
- [Azure 포털](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

이 문서에서는 데이터 팩터리 REST API를 사용하여 첫 번째 Azure Data Factory를 만듭니다.

## 필수 조건
- [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
- 컴퓨터에 [Curl](https://curl.haxx.se/dlwiz/)을 설치합니다. REST 명령과 함께 CURL 도구를 사용하여 데이터 팩터리를 만듭니다.
- [이 문서](../resource-group-create-service-principal-portal.md)의 지침에 따라 다음 작업을 수행합니다.
	1. Azure Active Directory에서 **ADFGetStartedApp**이라는 웹 응용 프로그램을 만듭니다.
	2. **클라이언트 ID** 및 **암호 키**를 가져옵니다.
	3. **테넌트 ID**를 가져옵니다.
	4. **ADFGetStartedApp** 응용 프로그램을 **데이터 팩터리 참가자** 역할에 할당합니다.
- [Azure PowerShell](../powershell-install-configure.md)을 설치합니다.
- **PowerShell**을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.
	1. **Login-AzureRmAccount**를 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
	2. **Get-AzureRmSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	3. **Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription 실행| Set-AzureRmContext** to select the subscription that you want to work with. Replace **NameOfAzureSubscription** with the name of your Azure subscription. 
3. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup**이라는 Azure 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	이 자습서의 일부 단계에서는 ADFTutorialResourceGroup이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.

## JSON 정의 만들기
curl.exe가 있는 폴더에서 다음 JSON 파일을 만듭니다.

### datafactory.json 
> [AZURE.IMPORTANT] 이름은 전역적으로 고유해야 하므로 고유한 이름을 지정하기 위해 ADFCopyTutorialDF를 접두사/접미사로 지정할 수 있습니다.

	{  
	    "name": "FirstDataFactoryREST",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] **accountname** 및 **accountkey**를 Azure Storage 계정 이름 및 키로 바꿉니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 액세스 키 보기, 복사 및 다시 생성](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}


### hdinsightondemandlinkedservice.json

	{
		"name": "HDInsightOnDemandLinkedService",
		"properties": {
			"type": "HDInsightOnDemand",
			"typeProperties": {
				"version": "3.2",
				"clusterSize": 1,
				"timeToLive": "00:30:00",
				"linkedServiceName": "AzureStorageLinkedService"
			}
		}
	}

다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

| 속성 | 설명 |
| :------- | :---------- |
| 버전 | 생성되는 HDInsight 버전을 3.2로 지정합니다. | 
| ClusterSize | HDInsight 클러스터의 크기입니다. | 
| TimeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다. |
| linkedServiceName | HDInsight에 의해 생성되는 로그를 저장하는데 사용될 저장소 계정을 지정합니다. |

다음 사항에 유의하세요.

- 데이터 팩터리는 위의 JSON으로 사용자에게 **Windows 기반** HDInsight 클러스터를 만들어 줍니다. **Linux 기반** HDInsight 클러스터를 만들도록 지정할 수도 있습니다. 자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요.
- 주문형 HDInsight 클러스터를 사용하는 대신 **고유의 HDInsight 클러스터**를 사용할 수 있습니다. 자세한 내용은 [HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 참조하세요.
- HDInsight 클러스터는 JSON(**linkedServiceName**)에서 지정한 Blob 저장소에 **기본 컨테이너**를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 한 슬라이스를 처리할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 삭제됩니다.

	많은 조각이 처리될수록 Azure Blob 저장소에 컨테이너가 많아집니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 저장소 비용을 줄이기 위해 삭제할 수 있습니다. 이 컨테이너의 이름은 "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp" 패턴을 따릅니다. [Microsoft 저장소 탐색기](http://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob 저장소에서 컨테이너를 삭제합니다.

자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요.

### inputdataset.json

	{
		"name": "AzureBlobInput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"fileName": "input.log",
				"folderPath": "adfgetstarted/inputdata",
				"format": {
					"type": "TextFormat",
					"columnDelimiter": ","
				}
			},
			"availability": {
				"frequency": "Month",
				"interval": 1
			},
			"external": true,
			"policy": {}
		}
	}


JSON은 **AzureBlobInput**이라는 데이터 집합을 정의하며 이는 파이프라인의 작업에 대한 입력 데이터를 나타냅니다. 또한 결과가 **adfgetstarted**라는 Blob 컨테이너 및 **inputdata**라는 폴더에 저장되도록 지정합니다.

다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

| 속성 | 설명 |
| :------- | :---------- |
| type | Azure blob 저장소에 데이터가 있기 때문에 형식 속성은 AzureBlob로 설정됩니다. |  
| linkedServiceName | 이전에 만든 StorageLinkedService를 참조합니다. |
| fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일을 선택합니다. 이 경우에 input.log만 처리됩니다. |
| type | 로그 파일이 텍스트 형식이므로 TextFormat을 사용합니다. | 
| columnDelimiter | 로그 파일의 열은 ,(쉼표)로 구분됩니다. |
| frequency/interval | 월 및 간격을 설정한 빈도가 1인 경우 입력 조각은 매월 제공됩니다. | 
| external | 입력 데이터가 데이터 팩터리 서비스에서 생성되지 않는 경우 이 속성은 true로 설정됩니다. | 

### outputdataset.json

	{
		"name": "AzureBlobOutput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"folderPath": "adfgetstarted/partitioneddata",
				"format": {
					"type": "TextFormat",
					"columnDelimiter": ","
				}
			},
			"availability": {
				"frequency": "Month",
				"interval": 1
			}
		}
	}

JSON은 **AzureBlobOutput**이라는 데이터 집합을 정의하고 이는 파이프라인의 작업에 대한 출력 데이터를 나타냅니다. 또한 결과가 **adfgetstarted**라는 Blob 컨테이너와 **partitioneddata**라는 폴더에 저장되도록 지정합니다. **가용성** 섹션은 출력 데이터 집합이 월 단위로 생성되도록 지정합니다.

### pipeline.json
> [AZURE.IMPORTANT] **storageaccountname**을 Azure Storage 계정 이름으로 바꿉니다.


	{
		"name": "MyFirstPipeline",
		"properties": {
			"description": "My first Azure Data Factory pipeline",
			"activities": [{
				"type": "HDInsightHive",
				"typeProperties": {
					"scriptPath": "adfgetstarted/script/partitionweblogs.hql",
					"scriptLinkedService": "AzureStorageLinkedService",
					"defines": {
						"inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
						"partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
					}
				},
				"inputs": [{
					"name": "AzureBlobInput"
				}],
				"outputs": [{
					"name": "AzureBlobOutput"
				}],
				"policy": {
					"concurrency": 1,
					"retry": 3
				},
				"scheduler": {
					"frequency": "Month",
					"interval": 1
				},
				"name": "RunSampleHiveActivity",
				"linkedServiceName": "HDInsightOnDemandLinkedService"
			}],
			"start": "2016-07-10T00:00:00Z",
			"end": "2016-07-11T00:00:00Z",
			"isPaused": false
		}
	}

JSON 코드 조각에서 Hive를 사용하여 HDInsight 클러스터에서 데이터를 처리하는 단일 작업으로 구성되는 파이프라인을 만듭니다.

Hive 스크립트 파일 **partitionweblogs.hql**은 Azure 저장소 계정(**StorageLinkedService**라고 하는 scriptLinkedService에 의해 지정됨)과 **adfgetstarted** 컨테이너에 있는 **스크립트** 폴더에 저장됩니다.

**defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: ${hiveconf:inputtable}, ${hiveconf:partitionedtable})으로 전달되는 런타임 설정을 지정합니다.

파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다.

작업 JSON에서 Hive 스크립트가 **linkedServiceName** – **HDInsightOnDemandLinkedService**에서 지정된 계산에서 실행되도록 지정합니다.

> [AZURE.NOTE] 위의 예에서 사용된 JSON 속성에 대한 내용은 [파이프라인의 분석](data-factory-create-pipelines.md#anatomy-of-a-pipeline)을 참조하세요.

## 전역 변수 설정

Azure PowerShell에서 값을 고유한 값으로 대체한 후에 다음 명령을 실행합니다.

> [AZURE.IMPORTANT] 클라이언트 ID, 클라이언트 암호, 테넌트 ID 및 구독 ID를 가져오는 지침은 [필수 구성 요소](#prerequisites) 섹션을 참조하세요.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "FirstDataFactoryREST"



## AAD로 인증

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 



## 데이터 팩터리 만들기

이 단계에서는 **FirstDataFactoryREST**라는 Azure Data Factory를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 데이터를 변환할 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 다음 명령을 실행하여 데이터 팩터리를 만듭니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

	여기(ADFCopyTutorialDF)에서 지정한 데이터 팩터리의 이름이 **datafactory.json**에서 지정한 이름과 일치하는지 확인합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 팩터리가 성공적으로 생성된 경우 데이터 팩터리에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

		Write-Host $results

다음 사항에 유의하세요.
 
- Azure Data Factory 이름은 전역적으로 고유해야 합니다. 결과에 **데이터 팩터리 이름 "FirstDataFactoryREST"를 사용할 수 없습니다**라는 오류가 발생한 경우 다음 단계를 수행합니다.
	1. **datafactory.json** 파일에서 이름(예: yournameFirstDataFactoryREST)을 변경합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	2. **$cmd** 변수가 값을 할당한 첫 번째 명령에서 FirstDataFactoryREST를 새 이름으로 바꾸고 명령을 실행합니다.
	3. REST API를 호출하는 다음 두 명령을 실행하여 데이터 팩터리를 만들고 작업의 결과를 인쇄합니다.
- 데이터 팩터리 인스턴스를 만들려면 Azure 구독의 참가자/관리자여야 합니다.
- 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
- "**구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.**" 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요.

	- Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 공급자를 등록합니다.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다.
	
			Get-AzureRmResourceProvider
	- Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com)에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

파이프라인을 만들기 전에 먼저 몇 가지 데이터 팩터리 엔터티를 만들어야 합니다. 먼저 데이터 저장소에 데이터 저장소/계산을 연결하는 연결된 서비스를 만들고 연결된 데이터 저장소에서 데이터를 나타내는 입력 및 출력 데이터 집합을 정의합니다.

## 연결된 서비스 만들기 
이 단계에서는 Azure Storage 계정 및 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결합니다. Azure Storage 계정은 이 샘플의 파이프라인에 대한 입력 및 출력 데이터를 가집니다. HDInsight 연결된 서비스는 이 샘플에서 파이프라인의 활동에 지정된 Hive 스크립트를 실행하는 데 사용됩니다.

### Azure 저장소 연결된 서비스 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다. 이 자습서에서는 동일한 Azure Storage 계정을 사용하여 입력/출력 데이터 및 HQL 스크립트 파일을 저장합니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.
 
		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results

### Azure HDInsight 연결된 서비스 만들기
이 단계에서는 데이터 팩터리에 주문형 HDInsight 클러스터를 연결합니다. HDInsight 클러스터는 런타임 시 자동으로 만들어지며 처리가 완료되고 지정된 시간 동안 유휴 상태를 유지한 후에 삭제됩니다. 주문형 HDInsight 클러스터를 사용하는 대신 고유의 HDInsight 클러스터를 사용할 수 있습니다. 자세한 내용은 [연결된 서비스 계산](data-factory-compute-linked-services.md)을 참조하세요.

1. 이 명령을 **cmd**라는 변수에 할당합니다.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

		Write-Host $results

## 데이터 집합 만들기
이 단계에서는 Hive 처리에 대한 입력 및 출력 데이터를 나타내는 데이터 집합을 만듭니다. 이러한 데이터 집합은 이 자습서의 앞부분에서 만든 **StorageLinkedService**를 참조합니다. 연결된 서비스는 Azure 저장소 계정을 가리키고 데이터 집합은 입력 및 출력 데이터를 가진 저장소의 컨테이너, 폴더, 파일 이름을 지정합니다.

### 입력 데이터 집합 만들기
이 단계에서는 Azure Blob 저장소에 저장된 출력 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results
### 출력 데이터 집합 만들기
이 단계에서는 Azure Blob 저장소에 저장된 출력 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results 

## 파이프라인 만들기
이 단계에서는 **HDInsightHive** 작업을 사용하여 첫 번째 파이프라인을 만듭니다. 입력 조각이 매월(빈도: 월, 간격: 1)이고 출력 조각이 매월 생성되며 작업에 대한 스케줄러 속성도 매월로 설정됩니다. 출력 데이터 집합 및 작업 스케줄러에 대한 설정이 일치해야 합니다. 현재 출력 데이터 집합이 일정을 결정하므로 작업이 출력을 생성하지 않는 경우 출력 데이터 집합을 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 집합 만들기를 건너뛸 수 있습니다.

Azure Blob 저장소의 **adfgetstarted/inputdata** 폴더에서 **input.log** 파일이 표시되는지 확인하고 다음 명령을 실행하여 파이프라인을 배포합니다. **시작** 및 **끝** 시간이 과거에 설정되고 **isPaused**가 false로 설정되었기 때문에 파이프라인(파이프라인의 활동)은 실행을 배포한 후에 즉시 실행됩니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

		Write-Host $results
5. 축하합니다. Azure PowerShell을 사용하여 첫 번째 파이프라인 만들기를 완료하였습니다.

## 파이프라인 모니터링
이 단계에서는 데이터 팩터리 REST API를 사용하여 파이프라인에 의해 생성되는 조각을 모니터링합니다.

	$ds ="AzureBlobOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;

	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}


> [AZURE.IMPORTANT] 
주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.(대략 20분) 따라서 파이프라인이 조각을 처리하는 데 **약 30분**이 걸릴 수 있습니다.

조각이 **준비** 상태 또는 **실패** 상태로 표시될 때까지 Invoke-Command 및 다음 명령을 실행합니다. 조각이 준비 상태에 있으면 출력 데이터에 대한 Blob 저장소의 **adfgetstarted** 컨테이너에 있는 **partitioneddata** 폴더를 확인합니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.

![출력 데이터](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] 조각이 성공적으로 처리될 때 입력된 파일이 삭제됩니다. 따라서 조각을 다시 실행하거나 자습서를 다시 수행하려는 경우 adfgetstarted 컨테이너의 inputdata 폴더에 입력 파일(input.log)을 업로드합니다.

또한 Azure 포털을 사용하여 조각을 모니터링하고 문제를 해결할 수 있습니다. [Azure 포털을 사용하여 파이프라인 모니터링](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) 세부 정보를 참조하세요.

## 요약 
이 자습서에서는 HDInsight hadoop 클러스터에서 Hive 스크립트를 실행하여 데이터를 처리하는 데 Azure 데이터 팩터리를 만들었습니다. Azure 포털에서 다음 단계를 수행하기 위해 데이터 팩터리 편집기를 사용했습니다.

1.	Azure **데이터 팩터리**를 만들었습니다.
2.	두 개의 **연결된 서비스**를 만들었습니다.
	1.	데이터 팩터리에 대한 입력/출력 파일을 보유하는 Azure Blob 저장소를 연결하는 **Azure 저장소** 연결된 서비스입니다.
	2.	주문형 HDInsight Hadoop 클러스터를 데이터 팩터리에 연결하는 **Azure HDInsight** 주문형 연결된 서비스입니다. Azure 데이터 팩터리는 입력 데이터를 처리하고 출력 데이터를 생성하기 위해 적시에 HDInsight Hadoop 클러스터를 만듭니다.
3.	파이프라인에서 HDInsight Hive 작업에 대한 입력 및 출력 데이터를 설명하는 두 개의 **데이터 집합**을 만들었습니다.
4.	**HDInsight Hive** 작업으로 **파이프라인**을 만들었습니다.

## 다음 단계
이 문서에서 파이프라인과 주문형 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## 참고 항목
| 항목 | 설명 |
| :---- | :---- |
| [데이터 팩터리 REST API 참조](https://msdn.microsoft.com/library/azure/dn906738.aspx) | 데이터 팩터리 cmdlet에서 포괄적인 설명서를 참조하세요. |
| [데이터 변환 활동](data-factory-data-transformation-activities.md) | 이 문서에서는 Azure 데이터 팩터리에서 지원되는 데이터 변환 활동(예: 자습서에 사용된 HDInsight Hive 변환)의 목록을 제공합니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) | 이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [파이프라인](data-factory-create-pipelines.md) | 이 문서는 Azure Data Factory의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 종단 간 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 집합](data-factory-create-datasets.md) | 이 문서는 Azure Data Factory의 데이터 집합을 이해하는 데 도움이 됩니다.
| [Azure 포털 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) | 이 문서는 Azure 포털 블레이드를 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. |
| [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) | 이 문서는 모니터링 및 관리 앱을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 

<!---HONumber=AcomDC_0921_2016-->