<properties 
	pageTitle="자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기 | Microsoft Azure" 
	description="이 자습서에서는 Visual Studio를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다." 
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
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# 자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기
> [AZURE.SELECTOR]
- [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [복사 마법사](data-factory-copy-data-wizard-tutorial.md)

이 자습서에서는 Visual Studio를 사용하여 Azure Data Factory를 만들고 모니터링하는 방법을 보여 줍니다. 데이터 팩터리의 파이프라인은 복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사합니다.

이 자습서의 일부로 수행하는 단계는 다음과 같습니다.

1. 2개의 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlinkedService1**를 만듭니다.

	AzureStorageLinkedService1은 Azure 저장소를 연결하고 AzureSqlLinkedService1은 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactoryVS**에 연결합니다. 파이프라인에 대한 입력 데이터는 Azure Blob 저장소의 Blob 컨테이너에 있고, 출력 데이터는 Azure SQL 데이터베이스의 테이블에 저장됩니다. 따라서 이러한 두 데이터 저장소를 연결된 서비스로 데이터 팩터리에 추가합니다.
2. 데이터 저장소에 저장된 입출력 데이터를 나타내는 2개의 데이터 집합인 **InputDataset** 및 **OutputDataset**을 만듭니다.

	InputDataset의 경우 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정합니다. OutputDataset의 경우 출력 데이터를 저장하는 SQL 테이블을 지정합니다. 구조, 가용성 및 정책과 같은 기타 속성도 지정합니다.
3. ADFTutorialDataFactoryVS에 **ADFTutorialPipeline**이라는 파이프라인을 만듭니다.

	이 파이프라인에는 Azure Blob에서 출력 Azure SQL 테이블로 입력 데이터를 복사하는 **복사 작업**이 있습니다. 복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.
4. **VSTutorialFactory**라는 데이터 팩터리를 만듭니다. 데이터 팩터리 및 모든 Data Factory 엔터티(연결된 서비스, 테이블 및 파이프라인)를 배포합니다.

## 필수 조건

1. [자습서 개요](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 문서를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
2. **Azure 구독의 관리자**여야만 Azure Data Factory에 데이터 팩터리 엔터티를 게시할 수 있습니다.
3. 다음 항목이 컴퓨터에 설치되어 있어야 합니다.
	- Visual Studio 2013 또는 Visual Studio 2015
	- Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015**를 클릭합니다.
	- Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트**를 클릭하여 플러그 인을 업데이트할 수도 있습니다.

## Visual Studio 프로젝트 만들기 
1. **Visual Studio 2013**을 실행합니다. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.
2. **새 프로젝트** 대화 상자에서 **DataFactory** 템플릿을 선택하고 **빈 데이터 팩터리 프로젝트**를 클릭합니다. DataFactory 템플릿이 보이지 않으면 Visual Studio를 닫고 Visual Studio 2013용 Azure SDK를 설치한 다음 Visual Studio를 다시 엽니다.

	![새 프로젝트 대화 상자](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. 프로젝트의 **이름**, **위치**, **솔루션**의 이름을 입력한 다음 **확인**을 클릭합니다.

	![솔루션 탐색기](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)

## 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 복사 작업에서 지원하는 모든 원본 및 싱크는 [지원되는 데이터 저장소](data-factory-data-movement-activities.md##supported-data-stores-and-formats)를 참조하세요. 데이터 팩터리에서 지원하는 계산 서비스 목록은 [연결된 계산 서비스](data-factory-compute-linked-services.md)를 참조하세요. 이 자습서에서는 계산 서비스를 사용하지 않습니다.

이 단계에서는 2개의 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlLinkedService1**을 만듭니다. AzureStorageLinkedService1 연결된 서비스는 Azure 저장소 계정을 연결하고, AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결합니다.

### Azure 저장소 연결된 서비스 만들기

4. 솔루션 탐색기에서 **Linked Services**(연결된 서비스) 노드를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
5. **새 항목 추가** 대화 상자의 목록에서 **Azure 저장소 연결된 서비스**를 선택한 다음 **추가**를 클릭합니다.

	![새 연결된 서비스](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. `<accountname>` 및 `<accountkey>`*를 Azure Storage 계정 이름 및 해당 키로 바꿉니다.

	![Azure 저장소 연결된 서비스](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** 파일을 저장합니다.

> JSON 속성에 대한 자세한 내용은 [Azure Blob 간의 데이터 이동](data-factory-azure-blob-connector.md#azure-storage-linked-service)을 참조하세요.

### Azure SQL 연결된 서비스 만들기

5. **솔루션 탐색기**에서 다시 **연결된 서비스** 노드를 마우스 오른쪽 단추로 다시 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
6. 이번에는 **Azure SQL 연결된 서비스**를 선택하고 **추가**를 클릭합니다.
7. **AzureSqlLinkedService1.json 파일**에서 `<servername>`, `<databasename>`, `<username@servername>` 및 `<password>`를 Azure SQL Server의 이름, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.
8.  **AzureSqlLinkedService1.json** 파일을 저장합니다.

> [AZURE.NOTE]
JSON 속성에 대한 자세한 내용은 [Azure SQL Database 간의 데이터 이동](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)을 참조하세요.

## 데이터 집합 만들기
이전 단계에서는 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlLinkedService1**을 만들어 Azure 저장소 계정과 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결했습니다. 이 단계에서는 각각 AzureStorageLinkedService1 및 AzureSqlLinkedService1로 참조되는 데이터 저장소에 저장된 입출력 데이터를 나타내는 **InputDataset** 및 **OutputDataset** 등 2개의 데이터 집합을 정의합니다. InputDataset의 경우 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정합니다. OutputDataset의 경우 출력 데이터를 저장하는 SQL 테이블을 지정합니다.

### 입력 데이터 집합 만들기
이 단계에서는 **AzureStorageLinkedService1** 연결된 서비스가 나타내는 Azure Storage의 Blob 컨테이너를 가리키는 **InputDataset**이라는 데이터 집합을 만듭니다. 테이블은 사각형 데이터 집합이며 현재 지원되는 유일한 데이터 집합 유형입니다.

9. **솔루션 탐색기**에서 **테이블**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
10. **새 항목 추가** 대화 상자에서 **Azure Blob**을 선택하고 **추가**를 클릭합니다.
10. JSON 텍스트를 다음 텍스트로 바꾸고 **AzureBlobLocation1.json** 파일을 저장합니다.

		{
		  "name": "InputDataset",
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
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
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

     다음 사항에 유의하세요.
	
	- 데이터 집합 **형식**을 **AzureBlob**로 설정합니다.
	- **linkedServiceName**을 **AzureStorageLinkedService**로 설정합니다. 이 연결된 서비스는 2단계에서 만들었습니다.
	- **folderPath**를 **adftutorial** 컨테이너로 설정합니다. 또한 **fileName** 속성을 사용하여 폴더 내의 Blob 이름을 지정할 수도 있습니다. Blob 이름을 지정하지 않으므로 컨테이너에 있는 모든 Blob의 데이터가 입력 데이터로 간주됩니다.
	- format **type**을 **TextFormat**으로 설정합니다.
	- 텍스트 파일에는 **FirstName**과 **LastName**의 두 필드가 쉼표(**columnDelimiter**)로 구분되어 있습니다.
	- **가용성**은 **매시간**으로 설정됩니다(**빈도**는 **시간**으로, **간격**은 **1**로 설정됨). 따라서 데이터 팩터리는 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 한 시간마다 입력 데이터를 찾습니다.
	
	**입력** 데이터 집합의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob은 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다.
 
	**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.&lt;Guid&gt;.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	**SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 설정하려면, **partitionedBy** 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 연도, 월 및 일을 사용하고 fileName은 SliceStart의 시간을 사용합니다. 예를 들어 조각이 2016-09-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2016/09/20으로 설정되고 fileName은 08.csv로 설정됩니다.

			"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	        "fileName": "{Hour}.csv",
	        "partitionedBy": 
	        [
	        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 

> [AZURE.NOTE]
JSON 속성에 대한 자세한 내용은 [Azure Blob 간의 데이터 이동](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)을 참조하세요.

### 출력 데이터 집합 만들기
이 단계에서는 **OutputDataset**이라는 출력 데이터 집합을 만듭니다. 이 데이터 집합은 **AzureSqlLinkedService1**이 나타내는 Azure SQL Database에서 SQL 테이블을 가리킵니다.

11. **솔루션 탐색기**에서 **테이블**을 마우스 오른쪽 단추로 다시 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
12. **새 항목 추가** 대화 상자에서 **Azure SQL**을 선택하고 **추가**를 클릭합니다.
13. JSON 텍스트를 다음 JSON으로 바꾸고 **AzureSqlTableLocation1.json** 파일을 저장합니다.

		{
		  "name": "OutputDataset",
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
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

     다음 사항에 유의하세요.
	
	- dataset **type**을 **AzureSQLTable**로 설정합니다.
	- **linkedServiceName**을 **AzureSqlLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- **tablename**을 **emp**로 설정합니다.
	- 데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName**이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName**만 지정해야 합니다.
	- **availability**는 **hourly**(**frequency**는 **hour**로, **interval**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.

> [AZURE.NOTE]
JSON 속성에 대한 자세한 내용은 [Azure SQL Database 간의 데이터 이동](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)을 참조하세요.

## 파이프라인 만들기 
지금까지 입출력 연결된 서비스 및 테이블을 만들었습니다. 이제 **복사 작업**으로 파이프라인을 만들어 Azure Blob에서 데이터를 Azure SQL 데이터베이스로 복사합니다.


1. **솔루션 탐색기**에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
15. **새 항목 추가** 대화 상자에서 **데이터 파이프라인 복사**를 선택하고 **추가**를 클릭합니다.
16. JSON을 다음 JSON으로 바꾸고 **CopyActivity1.json** 파일을 저장합니다.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "InputDataset"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputDataset"
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
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

	다음 사항에 유의하세요.

	- activities 섹션에는 **type**이 **Copy**로 설정된 작업만 있습니다.
	- 작업에 대한 입력을 **InputDataset**으로 설정하고 작업에 대한 출력을 **OutputDataset**으로 설정합니다.
	- **typeProperties** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다.

	**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2016-02-03"은 "2016-02-03T00:00:00Z"과 동일합니다.
	
	start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들어 2016-10-14T16:32:41Z입니다. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.
	
	**end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다.
	
	앞의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

## 데이터 팩터리 엔터티 게시/배포
이 단계에서는 이전에 만든 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 게시합니다. 이러한 엔터티를 저장하기 위해 만들어진 새 데이터 팩터리의 이름을 지정할 수도 있습니다.

18. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.
19. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인**을 클릭합니다.
20. 다음 대화 상자가 표시됩니다.

	![게시 대화 상자](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
21. 데이터 팩터리 구성 페이지에서 다음 단계를 수행합니다.
	1. **새 데이터 팩터리 만들기** 옵션을 선택합니다.
	2. **이름**에 **VSTutorialFactory**를 입력합니다.
	
		> [AZURE.IMPORTANT]  
		Azure Data Factory 이름은 전역적으로 고유해야 합니다. 게시할 때 데이터 팩터리의 이름에 대한 오류를 받은 경우 데이터 팩터리의 이름(예: yournameVSTutorialFactory)을 변경하고 다시 게시하도록 시도합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	3. **구독** 필드의 Azure 구독을 선택합니다.
	 
		> [AZURE.IMPORTANT] 모든 구독이 표시되지 않으면 구독의 관리자 또는 공동 관리자인 계정을 사용하여 로그인했는지 확인합니다.
	4. 생성되는 데이터 팩터리의 **리소스 그룹**을 선택합니다.
	5. 데이터 팩터리의 **하위 지역**을 선택합니다. Data Factory 서비스에서 지원하는 지역만 드롭다운 목록에 표시됩니다.
	6. **다음**을 클릭하여 **항목 게시** 페이지로 전환합니다.
	
		![데이터 팩터리 페이지 구성](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)
23. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음**을 클릭하여 **요약** 페이지로 전환합니다.
	
	![항목 페이지 게시](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)
24. 요약을 검토한 후 **다음**을 클릭하여 배포 프로세스를 시작하고 **배포 상태**를 봅니다.

	![요약 페이지 게시](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
25. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다. ![배포 상태 페이지](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png) 다음 사항에 유의하세요.

- "**구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.**" 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요.

	- Azure PowerShell에서 다음 명령을 실행하여 Data Factory 공급자를 등록합니다.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다.
	
			Get-AzureRmResourceProvider
	- Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com)에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.
- 	데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

> [AZURE.IMPORTANT] Data Factory 인스턴스를 만들려면 Azure 구독의 관리자 또는 공동 관리자여야 합니다.

## 요약
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure Data Factory를 만들었습니다. Visual Studio를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.

1.	Azure **Data Factory**를 만들었습니다.
2.	**연결된 서비스**를 만들었습니다.
	1. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 **Azure Storage** 연결된 서비스입니다.
	2. 출력 데이터를 보유하는 Azure SQL 데이터베이스를 연결하는 **Azure SQL** 연결된 서비스입니다.
3.	파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 집합**을 만들었습니다.
4.	원본으로 **BlobSource**와 **복사 작업**을 사용하고 싱크로 **SqlSink**를 사용하여 **파이프라인**을 만들었습니다.


## 서버 탐색기를 사용하여 데이터 팩터리 검토

1. **Visual Studio**의 메뉴에서 **보기**를 클릭한 다음 **서버 탐색기**를 클릭합니다.
2. 서버 탐색기 창에서 **Azure**를 확장한 다음 **데이터 팩터리**를 확장합니다. **Visual Studio에 로그인**이 표시되면 Azure 구독과 연결된 **계정**을 입력하고 **계속**을 클릭합니다. **암호**를 입력하고 **로그인**을 클릭합니다. Visual Studio에서는 구독에 있는 모든 Azure Data Factory에 대한 정보를 가져오려고 시도합니다. **데이터 팩터리 작업 목록** 창에 이 작업의 상태가 표시됩니다. ![서버 탐색기](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. 데이터 팩터리를 마우스 오른쪽 단추로 클릭하고 새 프로젝트로 데이터 팩터리 내보내기를 선택하여 기존 데이터 팩터리에 따라 Visual Studio 프로젝트를 만들 수 있습니다. ![VS 프로젝트로 데이터 팩터리 내보내기](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## Visual Studio용 데이터 팩터리 도구 업데이트
Visual Studio용 Azure Data Factory 도구를 업데이트하려면 다음 단계를 수행합니다.

1. 메뉴에서 **도구**를 클릭하고 **확장 및 업데이트**를 선택합니다.
2. 왼쪽 창에서 **업데이트**를 선택한 다음 **Visual Studio 갤러리**를 선택합니다.
4. **Visual Studio용 Azure Data Factory 도구**를 선택하고 **업데이트**를 클릭합니다. 이 항목이 표시되지 않으면 이미 최신 버전의 도구가 있는 것입니다.

Azure 포털을 사용하여 이 자습서에서 만든 파이프라인 및 데이터 집합을 모니터링하는 방법에 대한 지침은 [데이터 집합 및 파이프라인 모니터링](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline)을 참조하세요.

## 참고 항목
| 항목 | 설명 |
| :---- | :---- |
| [데이터 이동 활동](data-factory-data-movement-activities.md) | 이 문서에서는 이 자습서에서 사용한 복사 작업에 대한 자세한 정보를 제공합니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) | 이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [파이프라인](data-factory-create-pipelines.md) | 이 문서는 Azure Data Factory에서 파이프라인 및 작업을 이해하는 데 도움이 됩니다. |
| [데이터 집합](data-factory-create-datasets.md) | 이 문서는 Azure Data Factory의 데이터 집합을 이해하는 데 도움이 됩니다.
| [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) | 이 문서는 모니터링 및 관리 앱을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 

<!---HONumber=AcomDC_0928_2016-->