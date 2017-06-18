<properties 
	pageTitle="자습서: REST API를 사용하여 복사 작업이 있는 파이프라인 만들기 | Microsoft Azure" 
	description="이 자습서에서는 REST API를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# 자습서: REST API를 사용하여 복사 작업이 있는 파이프라인 만들기
> [AZURE.SELECTOR]
- [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [복사 마법사](data-factory-copy-data-wizard-tutorial.md)

이 자습서에서는 REST API를 사용하여 Azure Data Factory를 만들고 모니터링하는 방법을 보여 줍니다. 데이터 팩터리의 파이프라인은 복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사합니다.

> [AZURE.NOTE] 
이 문서는 모든 데이터 팩터리 REST API를 다루지 않습니다. 데이터 팩터리 REST API에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조](https://msdn.microsoft.com/library/azure/dn906738.aspx)(영문)를 참조하세요.
  

## 필수 조건

- [자습서 개요](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
- 컴퓨터에 [Curl](https://curl.haxx.se/dlwiz/)을 설치합니다. REST 명령과 함께 Curl 도구를 사용하여 데이터 팩터리를 만듭니다.
- [이 문서](../resource-group-create-service-principal-portal.md)의 지침에 따라 다음 작업을 수행합니다.
	1. Azure Active Directory에서 **ADFCopyTutorialApp**이라는 웹 응용 프로그램을 만듭니다.
	2. **클라이언트 ID** 및 **암호 키**를 가져옵니다.
	3. **테넌트 ID**를 가져옵니다.
	4. **ADFCopyTutorialApp** 응용 프로그램을 **데이터 팩터리 참가자** 역할에 할당합니다.
- [Azure PowerShell](../powershell-install-configure.md)을 설치합니다.
- **PowerShell**을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.
	1. 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
	
			Login-AzureRmAccount   
	2. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

			Get-AzureRmSubscription 
	3. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;를 Azure 구독의 이름으로 바꿉니다.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
	1. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup**이라는 Azure 리소스 그룹을 만듭니다.

			New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

		리소스 그룹이 이미 있다면 업데이트(Y)할지 또는 유지(N)할지를 지정합니다.

		이 자습서의 일부 단계에서는 ADFTutorialResourceGroup이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.
  
## JSON 정의 만들기
curl.exe가 있는 폴더에서 다음 JSON 파일을 만듭니다.

### datafactory.json 
> [AZURE.IMPORTANT] 이름은 전역적으로 고유해야 하므로 고유한 이름을 지정하기 위해 ADFCopyTutorialDF를 접두사/접미사로 지정할 수 있습니다.

	{  
	    "name": "ADFCopyTutorialDF",  
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

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] **servername**, **databasename**, **username** 및 **password**를 Azure SQL Server의 이름, SQL 데이터베이스의 이름, 사용자 계정 및 계정의 암호로 바꿉니다.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}


### inputdataset.json

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "structure": [
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "AzureStorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "fileName": "emp.txt",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

JSON 정의는 **AzureBlobInput**이라는 데이터 집합을 정의하며 이는 파이프라인의 작업에 대한 입력 데이터를 나타냅니다. 또한 입력 데이터가 Blob 컨테이너 **adftutorial**에 있는 **emp.txt** 파일에 위치한다고 지정합니다.

 다음 사항에 유의하세요.

- 데이터 집합 **형식**을 **AzureBlob**로 설정합니다.
- **linkedServiceName**을 **AzureStorageLinkedService**로 설정합니다.
- **folderPath**는 **adftutorial** 컨테이너로 설정되고 **fileName**은 **emp.txt**로 설정됩니다.
- format **type**을 **TextFormat**으로 설정합니다.
- 텍스트 파일에는 **FirstName**과 **LastName**의 두 필드가 쉼표(**columnDelimiter**)로 구분되어 있습니다.
- **가용성**은 **매시간**으로 설정됩니다(빈도는 시간으로, 간격은 1로 설정됨). 따라서 데이터 팩터리는 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 한 시간마다 입력 데이터를 찾습니다.

입력 데이터 집합의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob는 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다.

**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.&lt;Guid&gt;.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

**SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 설정하려면, **partitionedBy** 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 연도, 월 및 일을 사용하고 fileName은 SliceStart의 시간을 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### outputdataset.json
	
	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "structure": [
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "emp"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


JSON 정의는 **AzureSqlOutput**이라는 데이터 집합을 정의하며 이는 파이프라인의 작업에 대한 출력 데이터를 나타냅니다. 또한 결과가 AzureSqlLinkedService에 의해 나타나는 데이터베이스의 **emp** 테이블에 저장된다고 지정합니다. **가용성** 섹션은 출력 데이터 집합이 월(빈도: 시간 및 간격: 1) 단위로 생성되도록 지정합니다.

다음 사항에 유의하세요.

- dataset **type**을 **AzureSQLTable**로 설정합니다.
- **linkedServiceName**을 **AzureSqlLinkedService**로 설정합니다.
- **tablename**을 **emp**로 설정합니다.
- 데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName**이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName**만 지정해야 합니다.
- **availability**는 **hourly**(**frequency**는 **hour**로, **interval**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.

### pipeline.json

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from a blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-08-12T00:00:00Z",
	    "end": "2016-08-13T00:00:00Z"
	  }
	}


다음 사항에 유의하세요.

- activities 섹션에는 **type**이 **CopyActivity**로 설정된 작업 하나밖에 없습니다.
- 작업에 대한 입력을 **AzureBlobInput**으로 설정하고 작업에 대한 출력을 **AzureSqlOutput**로 설정합니다.
- **transformation** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다.

**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2015-02-03"은 "2015-02-03T00:00:00Z"과 동일합니다.

start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.

**end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다.

예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
> [AZURE.NOTE] 위의 예에서 사용된 JSON 속성에 대한 내용은 [파이프라인의 분석](data-factory-create-pipelines.md#anatomy-of-a-pipeline)을 참조하세요.

## 전역 변수 설정

Azure PowerShell에서 값을 고유한 값으로 대체한 후에 다음 명령을 실행합니다.

> [AZURE.IMPORTANT] 클라이언트 ID, 클라이언트 암호, 테넌트 ID 및 구독 ID를 가져오는 지침은 [필수 구성 요소](#prerequisites) 섹션을 참조하세요.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "ADFCopyTutorialDF"

## AAD로 인증 
다음 명령을 실행하여 AAD(Azure Active Directory)로 인증합니다.

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 

## 데이터 팩터리 만들기

이 단계에서는 **ADFCopyTutorialDF**라는 Azure Data Factory를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 복사 작업은 원본에서 대상 데이터 저장소로 데이터를 복사합니다. HDInsight Hive 작업은 Hive 스크립트를 실행하여 입력 데이터를 제품 출력 데이터로 변환합니다. 다음 명령을 실행하여 데이터 팩터리를 만듭니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

	여기(ADFCopyTutorialDF)에서 지정한 데이터 팩터리의 이름이 **datafactory.json**에서 지정한 이름과 일치하는지 확인합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 팩터리가 성공적으로 생성된 경우 데이터 팩터리에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

		Write-Host $results

다음 사항에 유의하세요.
 
- Azure Data Factory 이름은 전역적으로 고유해야 합니다. 결과에 **데이터 팩터리 이름 "ADFCopyTutorialDF"를 사용할 수 없습니다**라는 오류가 발생한 경우 다음 단계를 수행합니다.
	1. **datafactory.json** 파일에서 이름(예: yournameADFCopyTutorialDF)을 변경합니다.
	2. **$cmd** 변수가 값을 할당한 첫 번째 명령에서 ADFCopyTutorialDF를 새 이름으로 바꾸고 명령을 실행합니다.
	3. REST API를 호출하는 다음 두 명령을 실행하여 데이터 팩터리를 만들고 작업의 결과를 인쇄합니다.
	
	데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
- 데이터 팩터리 인스턴스를 만들려면 Azure 구독의 참가자/관리자여야 합니다.
- 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
- "**구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.**" 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요.

	- Azure PowerShell에서 다음 명령을 실행하여 Data Factory 공급자를 등록합니다.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다.
	
			Get-AzureRmResourceProvider
	- Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com)에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

파이프라인을 만들기 전에 먼저 몇 가지 데이터 팩터리 엔터티를 만들어야 합니다. 먼저 연결된 서비스를 만들어서 원본 및 대상 데이터 저장소를 데이터 저장소에 연결합니다. 그런 입력 및 출력 데이터 집합을 정의하여 다음 연결된 데이터 저장소에 데이터를 나타냅니다. 마지막으로 이러한 데이터 집합을 사용하는 작업이 있는 파이프라인을 만듭니다.

## 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 데이터 저장소는 데이터 팩터리 파이프라인에 대한 입력 데이터를 포함하거나 출력 데이터를 저장하는 Azure Storage, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스일 수 있습니다. 계산 서비스는 입력 데이터를 처리하고 출력 데이터를 생성하는 서비스입니다.

이 단계에서는 **AzureStorageLinkedService** 및 **AzureSqlLinkedService** 등 두 개의 연결된 서비스를 만듭니다. AzureStorageLinkedService 연결된 서비스는 Azure Storage 계정을 연결하고, AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리 **ADFCopyTutorialDF**에 연결합니다. 이 자습서 뒷부분에서는 AzuretStorageLinkedService의 Blob 컨테이너에서 AzureSqlLinkedService의 SQL 테이블로 데이터를 복사하는 파이프라인을 만듭니다.

### Azure 저장소 연결된 서비스 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure Storage 계정을 사용하여 입력 데이터를 저장합니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.
 
		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results

### Azure SQL 연결된 서비스 만들기
이 단계에서는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. 이 자습서에서는 동일한 Azure SQL 데이터베이스를 사용하여 출력 데이터를 저장합니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.
 
		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results

## 데이터 집합 만들기

이전 단계에서는 연결된 서비스 **AzureStorageLinkedService** 및 **AzureSqlLinkedService**를 만들어 Azure Storage 계정과 Azure SQL 데이터베이스를 데이터 팩터리 **ADFCopyTutorialDF**에 연결했습니다. 이 단계에서는 다음 단계에서 만들 파이프라인에 있는 복사 작업의 입력 및 출력 데이터를 나타내는 데이터 집합을 만듭니다.

이 자습서의 입력 데이터 집합은 AzureStorageLinkedService가 가리키는 Azure Storage에서 Blob 컨테이너를 참조합니다. 출력 데이터 집합은 AzureSqlLinkedService가 가리키는 Azure SQL 데이터베이스에서 SQL 테이블을 참조합니다.

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
다음 단계를 수행하여 이 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스를 준비합니다.
 
* **AzureStorageLinkedService**가 가리키는 Azure blob 저장소에서 **adftutorial**라는 Blob 컨테이너를 만듭니다.
* **adftutorial** 컨테이너에 대한 Blob으로 텍스트 파일 **emp.txt**를 만들어 업로드합니다.
* **AzureSqlLinkedService**가 가리키는 Azure SQL 데이터베이스에 **emp**라는 테이블을 만듭니다.


1. 메모장을 시작하고 다음 텍스트를 붙여넣은 다음 **emp.txt**로 하드 드라이브의 **C:\\ADFGetStartedPSH** 폴더에 저장합니다.

        John, Doe
		Jane, Doe
				
2. [Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너에 업로드합니다.

    ![Azure 저장소 탐색기](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. 다음 SQL 스크립트를 사용하여 **emp** 테이블을 Azure SQL 데이터베이스에 만듭니다.


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	SQL Server 2014가 컴퓨터에 설치된 경우: [2단계: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리의 SQL 데이터베이스에 연결][sql-management-studio] 문서의 지침에 따라 Azure SQL Server에 연결하고 SQL 스크립트를 실행합니다.

	클라이언트가 Azure SQL Server에 액세스할 수 없는 경우 컴퓨터(IP 주소)의 액세스를 허용하도록 Azure SQL Server의 방화벽을 구성해야 합니다. Azure SQL Server의 방화벽을 구성하는 단계는 [이 문서](../sql-database/sql-database-configure-firewall-settings.md)를 참조하세요.
		
### 입력 데이터 집합 만들기 
이 단계에서는 **AzureStorageLinkedService** 연결된 서비스가 나타내는 Azure Storage의 Blob 컨테이너를 가리키는 **AzureBlobInput**이라는 데이터 집합을 만듭니다. 이 Blob 컨테이너(**adftutorial**)는 **emp.txt** 파일에 입력 데이터를 포함합니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results

### 출력 데이터 집합 만들기
이 단계에서는 **AzureSqlOutput**이라는 출력 테이블을 만듭니다. 이 데이터 집합은 **AzureSqlLinkedService**가 나타내는 Azure SQL 데이터베이스에서 SQL 테이블(**emp**)을 가리킵니다. 파이프라인은 입력 Blob에서 **emp** 테이블로 데이터를 복사합니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
  
		Write-Host $results 

## 파이프라인 만들기
이 단계에서는 **AzureBlobInput**을 입력으로 사용하고 **AzureSqlOutput**을 출력으로 사용하는 **복사 작업**을 포함하는 파이프라인을 만듭니다.

1. 이 명령을 **cmd**라는 변수에 할당합니다.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. **Invoke-Command**를 사용하여 명령을 실행합니다.

		$results = Invoke-Command -scriptblock $cmd;
3. 결과를 확인합니다. 데이터 집합이 성공적으로 생성된 경우 데이터 집합에 대한 JSON이 **결과**에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

		Write-Host $results

**축하합니다.** Azure Blob 저장소에서 Azure SQL 데이터베이스에 데이터를 복사하는 파이프라인으로 Azure Data Factory를 성공적으로 만들었습니다.

## 파이프라인 모니터링
이 단계에서는 데이터 팩터리 REST API를 사용하여 파이프라인에 의해 생성되는 조각을 모니터링합니다.

	$ds ="AzureSqlOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;


	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

조각이 **준비** 상태 또는 **실패** 상태로 표시될 때까지 이러한 명령을 실행합니다. 조각이 준비 상태일 때 출력 데이터에 대한 Azure SQL 데이터베이스에서 **emp** 테이블을 확인합니다.

각 조각에 대해 원본 파일의 데이터 두 개의 행을 Azure SQL 데이터베이스의 emp 테이블에 복사합니다. 따라서 모든 조각이 준비 상태로 성공적으로 처리되면 emp 테이블에 24개의 새 레코드가 표시됩니다.


## 요약
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure Data Factory를 만드는 데 REST API를 사용했습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.

1.	Azure **Data Factory**를 만들었습니다.
2.	**연결된 서비스**를 만들었습니다.
	1. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 Azure Storage 연결된 서비스입니다.
	2. 출력 데이터를 보유하는 Azure SQL 데이터베이스를 연결하는 Azure SQL 연결된 서비스입니다.
3.	파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 집합**을 만들었습니다.
4.	원본인 BlobSource와 싱크인 SqlSink를 사용하여 복사 작업으로 **파이프라인**을 만들었습니다.

## 참고 항목
| 항목 | 설명 |
| :---- | :---- |
| [데이터 이동 활동](data-factory-data-movement-activities.md) | 이 문서에서는 이 자습서에서 사용한 복사 작업에 대한 자세한 정보를 제공합니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) | 이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [파이프라인](data-factory-create-pipelines.md) | 이 문서는 Azure Data Factory의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 종단 간 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 집합](data-factory-create-datasets.md) | 이 문서는 Azure Data Factory의 데이터 집합을 이해하는 데 도움이 됩니다.
| [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) | 이 문서는 모니터링 및 관리 앱을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0921_2016-->