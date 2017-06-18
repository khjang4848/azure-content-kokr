<properties 
	pageTitle="Azure 데이터 팩터리 - 질문과 대답" 
	description="Azure 데이터 팩터리에 대한 질문과 대답입니다." 
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
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 질문과 대답

## 일반적인 질문

### Azure 데이터 팩터리란 무엇인가요?

Data Factory는 **데이터의 이동과 변환을 자동화**하는 클라우드 기반 데이터 통합 서비스입니다. 원자재를 가져다가 완제품으로 만들기 위해 장비를 작동하는 공장처럼 데이터 팩터리는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다.
 
Data Factory를 사용하면 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있을 뿐 아니라, Azure HDInsight 및 Azure Data Lake 분석과 같은 계산 서비스를 사용하여 데이터를 처리/변환할 수 있습니다. 필요한 작업을 수행하는 파이프라인을 만든 후 정기적(매시간, 매일, 매주 등...)으로 실행되도록 예약할 수 있습니다.

자세한 내용은 [개요 및 주요 개념](data-factory-introduction.md)을 참조하세요.

### Azure 데이터 팩터리에 대한 가격 정보는 어디서 찾을 수 있나요?

Azure 데이터 팩터리에 대한 가격 정보는 [데이터 팩터리 가격 정보 페이지][adf-pricing-details]를 참조하세요.

### Azure 데이터 팩터리를 시작하려면 어떻게 해야 하나요?

- Azure 데이터 팩터리에 대한 개요는 [Azure 데이터 팩터리 소개](data-factory-introduction.md)를 참조하세요.
- 복사 작업을 사용하여 **데이터를 이동/복사**하는 방법에 대한 자습서는 [Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
- HDInsight Hive 작업을 사용하여 **데이터를 변환**하는 방법에 대한 자습서는 [Process data by running Hive script on Hadoop cluster](data-factory-build-your-first-pipeline.md)(Hadoop 클러스터에서 Hive 스크립트를 실행하여 데이터 처리)를 참조하세요.
  
### 데이터 팩터리의 지역 가용성은 얼마나 되나요?
Data Factory는 **미국 서부** 및 **북유럽**에서 사용할 수 있습니다. 데이터 팩터리에서 사용되는 계산 및 저장소 서비스는 다른 지역에 있을 수 있습니다. [지원되는 지역](data-factory-introduction.md#supported-regions)을 참조하세요.
 
### 데이터 팩터리/파이프라인/작업/데이터 집합의 수에 대한 제한은 어떻게 되나요?
 
[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#data-factory-limits) 문서의 **Azure Data Factory 제한** 섹션을 참조하세요.

### Azure Data Factory 서비스를 사용한 제작/개발자 환경이란 무엇인가요?

다음 중 하나를 사용하여 데이터 팩터리를 작성/생성할 수 있습니다.

- **Azure 포털** Azure 포털의 Data Factory 블레이드는 데이터 팩터리 AD 연결된 서비스를 만들기 위한 풍부한 사용자 인터페이스를 제공합니다. 포털에 포함된 **데이터 팩터리 편집기**에서 이러한 아티팩트에 대한 JSON 정의를 지정하여 연결된 서비스, 테이블, 데이터 집합 및 파이프라인을 쉽게 만들 수 있습니다. 포털/편집기를 사용하여 데이터 팩터리를 만들고 배포하는 예제는 [Build your first data pipeline using Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)(Azure 포털을 사용하여 첫 번째 데이터 파이프라인 빌드)를 참조하세요.

- **Visual Studio** Visual Studio를 사용하여 Azure Data Factory를 만들 수 있습니다. 자세한 내용은 [Build your first data pipeline using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)(Visual Studio를 사용하여 첫 번째 데이터 파이프라인 빌드)를 참조하세요.

- **Azure PowerShell** PowerShell을 사용하여 Data Factory를 만드는 자습서는 [Create and monitor Azure Data Factory using Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)(Azure PowerShell을 사용하여 Azure Data Factory 만들기 및 모니터링)을 참조하세요. 데이터 팩터리 cmdlet의 포괄적인 설명서는 MSDN 라이브러리의 [데이터 팩터리 Cmdlet 참조][adf-powershell-reference] 콘텐츠를 참조하세요.
   
- **.NET 클래스 라이브러리** Data Factory .NET SDK를 사용하여 프로그래밍 방식으로 데이터 팩터리를 만들 수 있습니다. .NET SDK를 사용하여 데이터 팩터리를 만드는 연습은 [.NET SDK를 사용하여 데이터 팩터리 만들기, 모니터링 및 관리](data-factory-create-data-factories-programmatically.md)를 참조하세요. 데이터 팩터리 .NET SDK의 포괄적인 설명서는 [데이터 팩터리 클래스 라이브러리 참조][msdn-class-library-reference]를 참조하세요.

- **REST API** Azure Data Factory 서비스에 의해 노출된 REST API를 사용하여 데이터 팩터리를 만들고 배포할 수도 있습니다. 데이터 팩터리 REST API의 포괄적인 설명서는 [데이터 팩터리 REST API 참조][msdn-rest-api-reference]를 참조하세요.
 
- **Azure Resource Manager 템플릿** 자세한 내용은 [자습서: Azure Resource Manager 템플릿을 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-arm.md)를 참조하세요.

### Data Factory의 이름을 바꿀 수 있나요?
아니요. 다른 Azure 리소스와 마찬가지로 Azure 데이터 팩터리의 이름을 변경할 수 없습니다.

### 데이터 팩터리를 Azure 구독 간에 이동할 수 있나요? 
예. 데이터 팩터리 블레이드의 **이동** 단추를 다음 다이어그램에 나오듯이 사용합니다.

![데이터 팩터리 이동](media/data-factory-faq/move-data-factory.png)

## 작업 - FAQ
### Data Factory 파이프라인에서 사용할 수 있는 다른 형식의 작업은 무엇인가요? 

- 데이터를 이동하는 [데이터 이동 작업](data-factory-data-movement-activities.md)
- 데이터를 처리/변환하는 [데이터 변환 작업](data-factory-data-transformation-activities.md)

### 작업은 언제 실행되나요?
출력 데이터 테이블의 **가용성** 구성 설정에 따라 작업 실행 시기가 결정됩니다. 입력 데이터 집합이 지정된 경우 작업은 실행을 시작하기 전에 모든 입력 데이터 종속성이 충족되었는지 (즉, **Ready** 상태) 검사합니다.

## 복사 작업 - FAQ
### 여러 작업이 포함된 파이프라인 1개보다 각 작업에 개별 파이프라인을 사용하는 것이 더 효율적인가요? 
파이프라인은 관련 작업의 번들로 간주됩니다. 작업을 연결하는 데이터 집합이 파이프라인 외의 다른 작업에서 사용되지 않는 경우 파이프라인 하나에 작업을 유지할 수 있습니다. 이 경우 서로 정렬되도록 파이프라인 활성 기간을 연결할 필요가 없습니다. 또한 파이프라인을 업데이트할 때 파이프라인 내부 테이블의 데이터 무결성이 보다 완벽하게 유지됩니다. 파이프라인 업데이트는 기본적으로 파이프라인 내의 모든 작업을 중지하고 제거한 후 다시 만듭니다. 제작 관점에서는 파이프라인에 대한 하나의 JSON 파일에서 관련 작업 내의 데이터 흐름을 확인하는 것이 더 쉬울 수도 있습니다.

### 복사 작업을 어디서 수행하나요? 

자세한 내용은 [전역적으로 사용 가능한 데이터 이동](data-factory-data-movement-activities.md#global) 섹션을 참조하세요. 즉, 온-프레미스 데이터 저장소가 관련된 경우 온-프레미스 환경의 데이터 관리 게이트웨이에서 복사 작업을 수행합니다. 그리고 두 클라우드 저장소 간에 데이터를 이동하는 경우 같은 지리의 싱크 위치에 가장 가까운 지역에서 복사 작업을 수행합니다.


## HDInsight 작업 - FAQ

### HDInsight에서 지원하는 지역은 어디인가요?

다음 문서의 지리적 가용성 섹션 또는 [HDInsight 가격 정보][hdinsight-supported-regions]를 참조하세요.

### 주문형 HDInsight 클러스터가 사용되는 지역은 어디인가요?

주문형 HDInsight 클러스터는 클러스터에서 사용하도록 지정한 저장소가 있는 지역과 동일한 지역에 생성됩니다.

### 추가 저장소 계정을 HDInsight 클러스터에 연결하려면 어떻게 해야 하나요?

사용자 고유의 HDInsight 클러스터를 사용하는 경우(BYOC - 자체 클러스터 가져오기) 다음 항목을 참조하세요.

- [대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용][hdinsight-alternate-storage]
- [HDInsight Hive와 추가 저장소 계정 사용][hdinsight-alternate-storage-2]

데이터 팩터리 서비스에서 만든 주문형 클러스터를 사용하는 경우 데이터 팩터리 서비스가 사용자를 대신해서 등록할 수 있도록 HDInsight 연결된 서비스에 대한 추가 저장소 계정을 지정합니다. 주문형 연결된 서비스에 대한 JSON 정의에서, 다음 JSON 조각과 같이 **additionalLinkedServiceNames** 속성을 사용하여 대체 저장소 계정을 지정합니다.
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
			"typeProperties": {
	        	"clusterSize": 1,
		        "timeToLive": "00:01:00",
		        "linkedServiceName": "LinkedService-SampleData",
		        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
			}
	    }
	} 

위의 예제에서 otherLinkedServiceName1 및 otherLinkedServiceName2는 HDInsight 클러스터가 대체 저장소 계정에 액세스하는 데 필요한 자격 증명이 해당 정의에 포함된 연결된 서비스를 나타냅니다.

## 조각 - FAQ

### 내 입력 조각이 준비 상태가 아닌 이유는 무엇인가요?  
일반적인 실수는 입력 데이터가 데이터 팩터리 외부의 데이터일 때(데이터 팩터리에 의해 생성되지 않음) 입력 데이터 집합에 대해 **external** 속성을 **true**로 설정하지 않는 것입니다.

다음 예제에서는 **dataset1**에 대해서만 **external**을 true로 설정해야 합니다.

**DataFactory1** 파이프라인 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 파이프라인 2: dataset3-> activity3 -> dataset4

dataset4(데이터 팩터리 1의 파이프라인 2에 의해 생성)를 사용하는 파이프라인을 포함하는 다른 데이터 팩터리가 있는 경우 데이터 집합이 다른 데이터 팩터리(DataFactory1, DataFactory2는 아님)에 의해 생성되므로 dataset4를 외부 데이터 집합으로 표시합니다.

**DataFactory2** 파이프라인 1: dataset4->activity4->dataset5

외부 속성이 제대로 설정된 경우 입력 데이터가 입력 데이터 집합 정의에 지정된 위치에 있는지 여부를 확인합니다.

### 조각이 매일 생성될 때 자정 이외의 다른 시간에 조각을 실행하는 방법은 무엇인가요?
**offset** 속성을 사용하여 조각을 생성할 시간을 지정합니다. 이 속성에 대한 자세한 내용은 [데이터 집합 가용성](data-factory-create-datasets.md#Availability) 섹션을 참조하세요. 간단한 예제는 다음과 같습니다.

	"availability":
	{
	    "frequency": "Day",
	    "interval": 1,
	    "offset": "06:00:00"
	}

기본값인 자정 대신 **오전 6시**에 시작하는 일별 조각입니다.

### 어떻게 조각을 다시 실행할 수 있나요?
다음 방법 중 하나로 조각을 다시 실행할 수 있습니다.

- 모니터링 및 관리 앱을 사용하여 작업 창 또는 조각을 다시 실행합니다. 지침에 대해서는 [선택한 작업 창 다시 실행](data-factory-monitor-manage-app.md#re-run-selected-activity-windows)을 참조하세요.
- Azure 포털에서 조각의 **데이터 조각** 블레이드에 대해 명령 모음의 **실행**을 클릭합니다.
- 조각의 상태를 **Waiting**으로 설정하여 **Set-AzureRmDataFactorySliceStatus** cmdlet을 실행합니다.
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

cmdlet에 대한 자세한 내용은 [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status]를 참조하세요.

### 조각을 처리하는 데 얼마나 오래 걸렸나요?
모니터링 및 관리 앱에서 작업 창 탐색기를 사용하여 데이터 조각을 처리하는 데 걸린 시간을 확인합니다. 자세한 내용은 [작업 창 탐색기](data-factory-monitor-manage-app.md#activity-window-explorer)를 참조하세요.

Azure 포털에서 다음을 수행할 수도 있습니다.

1. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **데이터 집합** 타일을 클릭합니다.
2. **데이터 집합** 블레이드에서 특정 데이터 집합을 클릭합니다.
3. **테이블** 블레이드의 **최근 조각** 목록에서 관심 있는 조각을 선택합니다.
4. **데이터 조각** 블레이드의 **작업 실행** 목록에서 작업 실행을 클릭합니다.
5. **작업 실행 세부 정보** 블레이드에서 **속성** 타일을 클릭합니다.
6. **기간** 필드와 값이 표시됩니다. 이 값은 조각을 처리하는 데 소요된 시간입니다.

### 실행 중인 조각을 중지하려면 어떻게 해야 하나요?
파이프라인 실행을 중지해야 하는 경우 [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) cmdlet를 사용할 수 있습니다. 현재, 파이프라인을 일시 중단해도 진행 중인 조각 실행은 중지되지 않습니다. 진행 중인 실행이 완료되면 추가 조각이 선택되지 않습니다.

모든 실행을 즉시 중지하려면 파이프라인을 삭제하고 다시 만들어야 합니다. 파이프라인을 삭제하도록 선택하는 경우 파이프라인에서 사용되는 테이블 및 연결된 서비스를 삭제할 필요는 없습니다.


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0914_2016-->