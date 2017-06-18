<properties
	pageTitle="Azure 데이터 팩터리를 사용하여 온-프레미스 SQL Server에서 SQL Azure로 데이터 이동 | Azure"
	description="온-프레미스와 클라우드의 데이터베이스 간에 데이터를 매일 이동하는 두 데이터 마이그레이션 활동으로 구성된 ADF 파이프라인을 설정합니다."
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
	ms.date="09/14/2016"
	ms.author="bradsev" />


# Azure 데이터 팩터리를 사용하여 온-프레미스 SQL server에서 SQL Azure로 데이터 이동

이 토픽에서는 Azure 데이터 팩터리(ADF)를 사용하여 Azure Blob Storage를 통해 온-프레미스 SQL Server 데이터베이스에서 SQL Azure 데이터베이스로 데이터를 이동하는 방법을 보여 줍니다.

이 **메뉴**는 팀 데이터 과학 프로세스 중 데이터를 저장하고 처리할 수 있는 대상 환경에 데이터를 수집하는 방법을 설명하는 항목에 연결됩니다.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>소개: ADF란 무엇이며 데이터를 마이그레이션하는 데 사용하려면 언제 사용해야 하나요?

Azure 데이터 팩터리는 데이터의 이동과 변환을 오케스트레이션하고 자동화하는 완전히 관리되는 클라우드 기반의 데이터 통합 서비스입니다. ADF 모델의 핵심 개념은 파이프라인입니다. 파이프라인은 각각 데이터 집합에 포함된 데이터에 수행할 작업을 정의하는 활동의 논리적 그룹화입니다. 연결된 서비스는 데이터 팩터리가 데이터 리소스에 연결하기 위해 필요한 정보를 정의하는 데 사용됩니다.

ADF와 함께 기존 데이터 처리 서비스는 가용성이 높고 클라우드에서 관리되는 데이터 파이프라인으로 구성될 수 있습니다. 이러한 데이터 파이프라인에서 데이터 수집, 준비, 변환, 분석 및 게시를 예약할 수 있으며 ADF가 복잡한 데이터 및 처리 종속성을 관리하고 오케스트레이션합니다. 클라우드에서 솔루션을 신속하게 구축 및 배포할 수 있으며 증가하는 온-프레미스 및 클라우드 데이터 원본을 연결합니다.

다음 경우에 ADF 사용을 고려합니다.

- 온-프레미스 및 클라우드 리소스 둘 다에 액세스하는 하이브리드 시나리오에서 데이터를 지속적으로 마이그레이션해야 하는 경우
- 데이터를 트랜잭션 처리하거나 수정해야 할 때 또는 마이그레이션 중에 비즈니스 논리를 추가할 때

ADF에서는 정기적으로 데이터 이동을 관리하는 간단한 JSON 스크립트를 사용하여 작업 예약 및 모니터링이 가능합니다. 또한 복잡한 작업을 지원하는 기타 기능도 포함하고 있습니다. ADF에 대한 자세한 내용은 [Azure 데이터 팩터리(ADF)](https://azure.microsoft.com/services/data-factory/)를 참조하세요.


## <a name="scenario"></a>시나리오

두 가지 데이터 마이그레이션 작업을 구성하는 ADF 파이프라인을 설정했습니다. 데이터는 매일 온-프레미스 SQL 데이터베이스와 클라우드의 Azure SQL 데이터베이스 간에 이동됩니다. 두 활동은 다음과 같습니다.

* 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소 계정으로 데이터 복사
* Azure Blob 저장소 계정에서 Azure SQL 데이터베이스로 데이터 복사

>[AZURE.NOTE] 여기에 나오는 단계는 ADF 팀이 제공한 더 자세한 자습서 [데이터 관리 게이트웨이 클라우드를 사용하여 온-프레미스 원본과 클라우드 간에 데이터 이동](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)을 인용하고 새롭게 구성하였으며 해당하는 경우 해당 항목의 관련 섹션에 대한 참조를 제공합니다.


## <a name="prereqs"></a>필수 조건
이 자습서에서는 사용자가 다음을 보유하고 있다고 가정합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure 저장소 계정**. 이 자습서에서는 데이터 저장을 위해 Azure 저장소 계정을 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요. 저장소 계정을 만든 후에는 저장소 액세스에 사용되는 계정 키를 확보해야 합니다. [저장소 액세스 키 보기, 복사 및 다시 생성](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.
* **Azure SQL 데이터베이스**에 대한 액세스. Azure SQL 데이터베이스를 설정해야 하는 경우, [Microsoft Azure SQL 데이터베이스 시작](../sql-database/sql-database-get-started.md) 항목에서 Azure SQL 데이터베이스의 새 인스턴스를 프로비전하는 방법에 대한 정보를 제공합니다.
* 로컬로 설치 및 구성된 **Azure PowerShell**. 자세한 내용은 [Azure PowerShell 설치 및 구성법](../powershell-install-configure.md)을 참조하세요.

> [AZURE.NOTE] 이 절차에서는 [Azure 포털](https://portal.azure.com/)을 사용합니다.


##<a name="upload-data"></a>온-프레미스 SQL Server에 데이터 업로드

[NYC Taxi 데이터 집합](http://chriswhong.com/open-data/foil_nyc_taxi/)을 사용하여 마이그레이션 프로세스를 시연합니다. 해당 게시물에서 설명한 것처럼 NYC Taxi 데이터 집합은 Azure blob 저장소 [NYC Taxi 데이터](http://www.andresmh.com/nyctaxitrips/)에서 제공됩니다. 데이터에는 두 개 파일이 있습니다. trip\_data.csv 파일에는 여정 세부 정보가 들어 있고 trip\_far.csv 파일에는 각 여정에 대한 요금 세부 정보가 들어 있습니다. 이러한 파일의 샘플 및 설명은 [NYC Taxi Trips 데이터 집합 설명](machine-learning-data-science-process-sql-walkthrough.md#dataset)에 제공됩니다.


자신의 데이터 집합에 여기에 제공된 절차를 도입하거나 NYC Taxi 데이터 집합을 사용하여 설명된 대로 단계를 따릅니다. NYC Taxi 데이터 집합을 온-프레미스 SQL Server 데이터베이스에 업로드하려면 [SQL Server 데이터베이스로 대량 데이터 가져오기](machine-learning-data-science-process-sql-walkthrough.md#dbload)에 설명된 절차를 따릅니다. 이러한 지침은 Azure 가상 컴퓨터의 SQL Server에 대한 내용이지만 온-프레미스 SQL Server로 업로드하는 절차는 동일합니다.


##<a name="create-adf"></a>Azure 데이터 팩터리 만들기

[Azure 포털](https://portal.azure.com/)에서 새 Azure Data Factory 및 리소스 그룹을 만들기 위한 지침은 [Azure Data Factory 만들기](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory)에서 제공됩니다. 새 ADF 인스턴스의 이름은 *adfdsp*이고 생성된 리소스 그룹은 *adfdsprg*입니다.


## 데이터 관리 게이트웨이 설치 및 구성

Azure 데이터 팩터리의 파이프라인에서 온-프레미스 SQL Server를 사용할 수 있게 하려면 데이터 팩터리에 연결된 서비스로 추가해야 합니다. 온-프레미스 SQL Server에 대한 연결된 서비스를 만들려면 다음을 수행해야 합니다.

- 온-프레미스 컴퓨터에 Microsoft 데이터 관리 게이트웨이를 다운로드한 후 설치합니다.
- 온-프레미스 데이터 원본에 대한 연결된 서비스를 게이트웨이를 사용하도록 구성합니다.

데이터 관리 게이트웨이는 호스팅되는 컴퓨터에서 원본 및 싱크 데이터를 직렬화 및 역직렬화합니다.

데이터 관리 게이트웨이에 대한 설치 지침 및 세부 정보에 대한 내용은 [온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.


## <a name="adflinkedservices"></a>데이터 리소스에 연결할 연결된 서비스 만들기

연결된 서비스는 Azure 데이터 팩터리가 데이터 리소스에 연결하기 위해 필요한 정보를 정의합니다. 연결된 서비스를 만들기 위한 단계별 절차가 [연결된 서비스 만들기](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services)에 제공됩니다.

이 시나리오에는 연결된 서비스가 필요한 3개의 리소스가 있습니다.

1. [온-프레미스 SQL Server에 대한 연결된 서비스](#adf-linked-service-onprem-sql)
2. [Azure Blob 저장소에 대한 연결된 서비스](#adf-linked-service-blob-store)
3. [Azure SQL 데이터베이스에 대한 연결된 서비스](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>온-프레미스 SQL Server 데이터베이스에 대한 연결된 서비스

온-프레미스 SQL Server에 대한 연결된 서비스를 만들려면

- Azure 클래식 포털의 ADF 방문 페이지에서 **데이터 저장소**를 클릭합니다.
- **SQL**을 선택하고 온-프레미스 SQL Server에 대한 *사용자 이름* 및 *암호* 자격 증명을 입력합니다. servername을 **정규화된 서버 이름 백슬래시 인스턴스 이름(servername\\instancename)**으로 입력해야 합니다. 연결된 서비스 이름을 *adfonpremsql*로 지정합니다.

###<a name="adf-linked-service-blob-store"></a>Blob에 대한 연결된 서비스

Azure Blob 저장소 계정에 대한 연결된 서비스를 만들려면

- Azure 클래식 포털의 ADF 방문 페이지에서 **데이터 저장소**를 클릭합니다.
- **Azure 저장소 계정**을 선택합니다.
- Azure Blob 저장소 계정 키 및 컨테이너 이름을 입력합니다. 연결된 서비스 이름을 *adfds*로 지정합니다.

###<a name="adf-linked-service-azure-sql"></a>Azure SQL 데이터베이스에 대한 연결된 서비스

Azure SQL 데이터베이스에 대한 연결된 서비스를 만들려면

- Azure 클래식 포털의 ADF 방문 페이지에서 **데이터 저장소**를 클릭합니다.
- **Azure SQL**을 선택하고 Azure SQL 데이터베이스에 대한 *사용자 이름* 및 *암호* 자격 증명을 입력합니다. *username*은 *user@servername*으로 지정해야 합니다.


##<a name="adf-tables"></a>데이터 집합에 액세스하는 방법을 지정하는 테이블 정의 및 만들기

다음 스크립트 기반 프로시저로 데이터 집합의 구조, 위치 및 가용성을 지정하는 테이블을 만듭니다. 테이블을 정의하는 데 JSON 파일이 사용됩니다. 이러한 파일의 구조에 대한 자세한 내용은 [데이터 집합](../data-factory/data-factory-create-datasets.md)을 참조하세요.

> [AZURE.NOTE]  [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet을 실행하기 전에 `Add-AzureAccount` cmdlet을 실행하여 명령 실행을 위해 Azure 구독을 선택했는지 확인해야 합니다. 이 cmdlet의 설명서는 [Add-azureaccount](https://msdn.microsoft.com/library/azure/dn790372.aspx)를 참조하세요.

테이블에서 JSON 기반 정의는 다음 이름을 사용합니다.

* 온 프레미스 SEL 서버의 **테이블 이름**은 *nyctaxi\_data*임
* Azure Blob 저장소 계정에서 **컨테이너 이름**은 *containername*입니다.

이 ADF 파이프라인에는 3개의 테이블 정의가 필요합니다.

1. [SQL 온-프레미스 테이블](#adf-table-onprem-sql)
2. [Blob 테이블](#adf-table-blob-store)
3. [SQL Azure 테이블](#adf-table-azure-sql)

> [AZURE.NOTE]  이러한 절차에서는 Azure PowerShell을 사용하여 ADF 활동을 정의하고 만듭니다. 그러나 이러한 작업은 Azure 포털을 사용해서도 수행할 수 있습니다. 자세한 내용은 [입력 및 출력 데이터 집합 만들기](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets)를 참조하세요.

###<a name="adf-table-onprem-sql"></a>SQL 온-프레미스 테이블

온-프레미스 SQL Server에 대한 테이블 정의는 다음 JSON 파일에 지정됩니다.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}

여기서는 열 이름이 포함되지 않았습니다. 여기에 열 이름을 포함하여 열 이름을 sub-select할 수 있습니다(자세한 내용은 [ADF 설명서](../data-factory/data-factory-data-movement-activities.md) 항목 참조).

테이블의 JSON 정의를 *onpremtabledef.json*이라는 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\\temp\\onpremtabledef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Blob 테이블
출력 blob 위치에 대한 테이블 정의는 다음과 같습니다(온-프레미스에서 수집된 데이터를 Azure blob에 매핑).

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

테이블의 JSON 정의를 *bloboutputtabledef.json*이라는 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\\temp\\bloboutputtabledef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure 테이블
SQL Azure 출력에 대한 테이블 정의가 다음과 같습니다(이 스키마는 blob에서 가져온 데이터를 매핑).

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

테이블의 JSON 정의를 *AzureSqlTable.json*이라는 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\\temp\\AzureSqlTable.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>파이프라인 정의 및 만들기

파이프라인에 포함될 활동을 지정하고 다음 스크립트 기반 프로시저로 파이프라인을 만듭니다. 파이프라인 속성을 정의하는 데 JSON 파일이 사용됩니다.

* 스크립트는 **파이프라인 이름**이 *AMLDSProcessPipeline*이라고 가정합니다.
* 또한 파이프라인이 매일 정기적으로 실행되도록 파이프라인의 주기성을 설정하고 작업에 대한 기본 실행 시간을 사용합니다(오전 12시 UTC).

> [AZURE.NOTE]  다음 절차에서는 Azure PowerShell을 사용하여 ADF 파이프라인을 정의하고 만듭니다. 그러나 이러한 작업은 Azure 포털을 사용해서도 수행할 수 있습니다. 자세한 내용은 [파이프라인 만들기 및 실행](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline)을 참조하세요.

이전에 제공된 테이블 정의를 사용하여 ADF에 대한 파이프라인 정의는 다음과 같이 지정됩니다.

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

파이프라인의 이 JSON 정의를 *pipelinedef.json*이라는 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\\temp\\pipelinedef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 파이프라인을 만듭니다.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Azure 클래식 포털의 ADF에서 다음과 같이 파이프라인이 표시되는지 확인합니다(다이어그램을 클릭할 때).

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>파이프라인 시작
이제 다음 명령을 사용하여 파이프라인을 실행할 수 있습니다.

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*startdate* 및 *enddate* 매개 변수 값을 파이프라인을 실행할 실제 날짜로 바꿔야 합니다.

파이프라인이 실행된 후에는 blob에 대해 선택한 컨테이너에 표시된 데이터를 하루에 한 개 파일로 볼 수 있습니다.

데이터를 증분 방식으로 파이프하는 ADF 제공 기능을 활용하지 않았습니다. 이 작업을 수행하는 방법 및 ADF에서 제공하는 기타 기능에 대한 자세한 내용은 [ADF 설명서](https://azure.microsoft.com/services/data-factory/)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->