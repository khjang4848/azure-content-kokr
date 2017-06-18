<properties 
	pageTitle="데이터 팩터리 개요, 데이터 통합 서비스 | Microsoft Azure" 
	description="Azure Data Factory가 무엇인지 알아봅니다. 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 데이터 통합 서비스입니다." 
	keywords="데이터 통합, 클라우드 데이터 통합, Azure 데이터 팩터리란"
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
	ms.date="09/22/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 서비스인 클라우드의 데이터 통합 서비스 소개

## Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 **이동**과 **변환**을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 다양한 데이터 저장소에서 데이터를 수집하고 변환/처리하며 데이터 저장소에 결과 데이터를 게시할 수 있는 Data Factory 서비스를 사용하여 데이터 통합 솔루션을 만들 수 있습니다.

Data Factory 서비스를 통해 데이터를 이동하고 변환하는 파이프라인을 실행한 다음 데이터 파이프라인을 지정된 일정(매시간, 매일, 매주 등)으로 만들 수 있습니다. 또한 데이터 파이프라인 간의 종속성과 계보를 표시하는 다양한 시각화를 제공하며 문제를 쉽고 정확하게 파악하고 모니터링 경고를 설정하는 통합된 단일 보기에서 모든 데이터 파이프라인을 모니터링합니다.

![다이어그램: 데이터 팩터리 개요, 데이터 통합 서비스](./media/data-factory-introduction/what-is-azure-data-factory.png) **그림 1.** 다양한 데이터 원본으로부터 데이터를 수집하고 해당 데이터를 준비하고 변환하고 분석한 다음 소비를 위해 바로 사용할 수 있는 데이터를 게시합니다.

## 파이프라인 및 활동
Data Factory 솔루션에서 하나 이상의 데이터 **파이프라인**를 만듭니다. 파이프라인은 활동의 논리적 그룹화입니다. 파이프라인은 여러 활동을 함께 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다.

**활동**은 데이터에 수행할 작업을 정의합니다. 예를 들어 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 복사 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 활동 및 데이터 변환 활동이라는 두 종류의 활동을 지원합니다.
  
## 데이터 이동 활동 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 참조하세요.

## 데이터 변환 활동
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

자세한 내용은 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 참조하세요.

복사 작업에서 지원하지 않는 데이터 저장소에서/로 데이터를 이동해야 하거나 사용자 고유한 논리를 사용하여 데이터를 변환해야 하는 경우 **사용자 지정 .NET 활동**을 만듭니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

## 연결된 서비스
연결된 서비스는 데이터 팩터리가 외부 리소스(예: Azure Storage, 온-프레미스 SQL Server, Azure HDInsight)에 연결하기 위해 필요한 정보를 정의합니다. 연결된 서비스는 데이터 팩터리 내에서 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server, Oracle 데이터베이스, 파일 공유 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 **데이터 저장소**를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](data-factory-data-movement-activities.md) 섹션을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 **계산 리소스**를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 지원되는 계산 환경 목록에 대해서는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 섹션을 참조하세요.

## 데이터 집합 
연결된 서비스는 데이터 저장소를 Azure Data Factory에 연결합니다. 데이터 집합은 데이터 저장소에 있는 데이터 구조를 나타냅니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 Data Factory에 대한 연결 정보를 제공합니다. Azure Blob 데이터 집합은 파이프라인이 데이터를 읽어야 하는 Azure Blob Storage에서 Blob 컨테이너 및 폴더를 지정합니다. 마찬가지로 Azure SQL 연결된 서비스는 Azure SQL Database에 대한 연결 정보를 제공하고 Azure SQL 데이터 집합은 데이터를 포함하는 테이블을 지정합니다.

## Data Factory 엔터티 간의 관계
데이터 팩터리에는 입력 및 출력 데이터, 처리 이벤트, 필요한 데이터 흐름을 실행하는데 필요한 일정과 리소스를 정의하기 위해 함께 작용하는 몇 가지 주요 엔터티가 있습니다.

![다이어그램: 데이터 팩터리, 클라우드 데이터 통합 서비스 - 주요 개념](./media/data-factory-introduction/data-integration-service-key-concepts.png) **그림 2.** 데이터 집합, 활동, 파이프라인 및 연결된 서비스 간의 관계

연결된 서비스, 데이터 집합, 활동, 파이프라인이라는 네 가지의 단순한 개념만으로 시작할 준비가 되었습니다. [첫 번째 파이프라인을 빌드](data-factory-build-your-first-pipeline.md)할 수 있습니다.

## 지원되는 지역
현재 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서 데이터 팩터리를 만들 수 있습니다. 그러나 데이터 팩터리는 계산 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스할 수 있습니다.

Azure 데이터 팩터리 자체는 데이터를 저장하지 않습니다. 데이터 기반 흐름을 만들어서 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores) 간의 데이터 이동을 조정하고 다른 지역 또는 온-프레미스 환경에서 [계산 서비스](data-factory-compute-linked-services.md)를 사용하여 데이터의 처리를 조정할 수 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md)할 수 있습니다.

Azure Data Factory가 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서만 사용할 수 있지만, 여러 지역에서 데이터 팩터리의 데이터 이동을 지원하는 서비스를 [전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. 데이터 저장소가 방화벽 뒤에 있는 경우 온-프레미스 환경에 설치된 [데이터 관리 게이트웨이](data-factory-move-data-between-onprem-and-cloud.md)가 대신 데이터를 이동시킵니다.

예를 들어 Azure HDInsight 클러스터 및 Azure Machine Learning과 같은 계산 환경이 서유럽 지역 외부에서 실행되고 있다고 가정해보겠습니다. 북유럽에서 Azure Data Factory 인스턴스를 만들고 사용할 수 있으며 이를 사용하여 서유럽의 계산 환경에 작업을 예약할 수 있습니다. 데이터 팩터리가 계산 환경에 작업을 트리거하는 데는 몇 밀리초가 걸리지만 사용자의 계산 환경에서 작업을 실행하는 데 걸리는 시간은 변경되지 않습니다.

나중에 Azure에서 지원하는 모든 지리적 위치에서 Azure 데이터 팩터리를 사용하게 될 예정입니다.
  
## 다음 단계
데이터 파이프라인을 사용하여 데이터 팩터리를 빌드하는 방법을 알아보려면 다음 자습서의 단계별 지침을 따르세요.

자습서 | 설명
-------- | -----------
[Hadoop 클러스터를 사용하여 데이터를 처리하는 데이터 파이프라인 구축](data-factory-build-your-first-pipeline.md) | 이 자습서에서는 Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 **데이터를 처리**하는 데이터 파이프라인으로 첫 번째 Azure 데이터 팩터리를 구축합니다. |
[데이터 파이프라인을 빌드하여 두 클라우드 데이터 저장소 간에 데이터 이동](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 이 자습서에서는 파이프라인을 포함한 데이터 팩터리를 만들어서 Blob Storage에서 SQL Database로 **데이터를 이동**합니다.
[데이터 파이프라인을 빌드하여 데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) | 이 자습서에서는 **온-프레미스** SQL Server 데이터베이스에서 Azure Blob으로 **데이터를 이동**하는 파이프라인이 포함된 데이터 팩터리를 구축합니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다. 

<!---HONumber=AcomDC_0928_2016-->