<properties
	pageTitle="항공 우주 및 다른 비즈니스에서 예측 유지 관리를 위한 Cortana Intelligence 솔루션 템플릿에 대한 기술 가이드 | Microsoft Azure"
	description="항공, 유틸리티 및 운송에서 예측 유지 관리를 위한 Microsoft Cortana Intelligence를 사용한 솔루션 템플릿에 대한 기술 지침"
	services="cortana-analytics"
	documentationCenter=""
	authors="fboylu"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="fboylu" />

# 항공 우주 및 다른 비즈니스에서 예측 유지 관리를 위한 Cortana Intelligence 솔루션 템플릿에 대한 기술 가이드

## **승인**
이 문서는 데이터 과학자 Yan Zhang, Gauher Shaheen, Fidan Boylu Uz 및 Microsoft의 소프트웨어 엔지니어 Dan Grecoe가 작성하였습니다.

## **개요**

솔루션 템플릿은 Cortana Intelligence Suite를 기반으로 E2E 데모 빌드 프로세스를 가속화하도록 디자인되었습니다. 배포된 템플릿은 필요한 Cortana Intelligence 구성 요소로 구독을 프로비전하고 이들 간의 관계를 빌드합니다. 또한 솔루션 템플릿을 배포한 후 다운로드하여 로컬 컴퓨터에 설치하는 데이터 생성기 응용 프로그램에서 생성된 샘플 데이터로 데이터 파이프라인을 시드합니다. 생성기에서 생성된 데이터는 데이터 파이프라인을 하이드레이션하며 나중에 Power BI 대시보드에서 시각화할 수 있는 기계 학습 예측을 생성하기 시작합니다. 배포 프로세스는 솔루션 자격 증명을 설정하는 몇 가지 단계를 안내합니다. 솔루션 이름, 사용자 이름 및 배포하는 동안 제공하는 암호와 같은 이러한 자격 증명을 기록합니다.

이 문서의 목적은 참조 아키텍처 및 이 솔루션 템플릿의 일부로 구독에 프로비전된 다른 구성 요소를 설명하는 것입니다. 문서는 사용자 고유 데이터에서 통찰력과 예측을 볼 수 있도록 샘플 데이터를 사용자 고유의 실제 데이터로 대체하는 방법에 대해서도 설명합니다. 또한 문서는 사용자 고유 데이터로 솔루션을 사용자 지정하려는 경우 수정되어야 하는 솔루션 템플릿의 부분을 설명합니다. 이 솔루션 템플릿에 대한 Power BI 대시보드를 빌드하는 방법에 대한 지침은 끝에 제공됩니다.

>[AZURE.TIP] [이 문서의 PDF 버전](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)을 다운로드 및 인쇄할 수 있습니다.

## **큰 그림**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

솔루션이 배포되면 Cortana Analytics Suite 내의 여러 Azure 서비스가 활성화됩니다(*즉,* 이벤트 허브, 스트림 분석, HDInsight, 데이터 팩터리, 기계 학습 *등*). 위의 아키텍처 다이어그램은 높은 수준에서 항공 솔루션 템플릿에 대한 예측 유지 관리가 종단 간에서 생성되는 방법을 보여 줍니다. 이러한 서비스는 관련 파이프라인 작업을 실행하고 나중에 삭제해야 할 때 요청 시 프로비전되므로 HDInsight를 제외하고 솔루션을 배포할 때 생성된 솔루션 템플릿 다이어그램에서 해당 서비스를 클릭하여 Azure 포털에서 조사할 수 있습니다. [다이어그램의 전체 크기 버전](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)을 다운로드할 수 있습니다.

다음 섹션은 각 부분을 설명합니다.

## **데이터 원본 및 수집**

### 가상 데이터 원본

이 서식 파일의 경우 사용되는 데이터 원본은 성공적인 배포 후 다운로드하고 로컬로 실행하는 데스크톱 응용 프로그램에서 생성됩니다. 솔루션 템플릿 다이어그램에서 예측 유지 관리 데이터 생성기라는 첫 번째 노드를 선택하면 속성 모음에서 이 응용 프로그램 다운로드 및 설치에 대한 지침을 찾을 수 있습니다. 이 응용 프로그램은 솔루션 흐름의 나머지 부분에서 사용될 데이터 요소 또는 이벤트로 [Azure 이벤트 허브](#azure-event-hub)를 피드합니다. 이 데이터 원본은 [Turbofan 엔진 성능 저하 시뮬레이션 데이터 집합](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan)을 사용한 [NASA 데이터 저장소](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/)의 공개적으로 사용할 수 있는 데이터로 구성되었거나 파생되었습니다.

이벤트 생성 응용 프로그램은 컴퓨터에서 실행되는 동안 Azure 이벤트 허브를 채웁니다.

### Azure 이벤트 허브

[Azure 이벤트 허브](https://azure.microsoft.com/services/event-hubs/) 서비스는 위에서 설명한 가상 데이터 원본에서 제공된 입력의 받는 사람입니다.

## **데이터 준비 및 분석**


### Azure 스트림 분석

[Azure 스트림 분석](https://azure.microsoft.com/services/stream-analytics/) 서비스는 [Azure 이벤트 허브](#azure-event-hub)의 입력 스트림에서 거의 실시간으로 분석을 제공하는 데 사용되며 [Azure 데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/) 서비스에서 나중에 처리할 들어오는 모든 원시 이벤트를 [Azure 저장소](https://azure.microsoft.com/services/storage/)에 보관할 뿐만 아니라 [Power BI](https://powerbi.microsoft.com) 대시보드에 결과를 게시합니다.

### HD Insights 사용자 지정 집계

Azure HD Insight 서비스는 Azure 스트림 분석 서비스를 사용하여 보관된 원시 이벤트에 집계를 제공하도록 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트(Azure 데이터 팩터리에서 오케스트레이션된)를 실행하는 데 사용됩니다

### Azure 기계 학습

[Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 서비스는 수신된 입력이 제공된 특정 항공기 엔진의 잔여 수명(RUL)을 예측하는 데 사용됩니다(Azure 데이터 팩터리에서 오케스트레이션된).

## **데이터 게시**


### Azure SQL 데이터베이스 서비스

[Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/) 서비스는 [Power BI](https://powerbi.microsoft.com) 대시보드에서 사용되는 Azure 기계 학습 서비스에서 수신된 예측을 저장하는 데 사용됩니다(Azure 데이터 팩터리에서 관리된).

## **데이터 사용량**

### Power BI

[Power BI](https://powerbi.microsoft.com) 서비스는 [Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 서비스를 사용하여 생성된 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)에 저장된 RUL 예측 뿐만 아니라 [Azure 스트림 분석](https://azure.microsoft.com/services/stream-analytics/) 서비스에서 제공된 집계 및 경고를 포함하는 대시보드를 표시하는 데 사용됩니다. 이 솔루션 템플릿에 대한 Power BI 대시보드를 빌드하는 방법에 대한 지침은 아래 섹션을 참조하세요.

## **사용자 고유 데이터를 가져오는 방법**

이 섹션은 Azure에 사용자 고유 데이터를 가져오는 방법 및 이 아키텍처에 가져오는 데이터에 대한 변경 내용이 필요한 영역을 설명합니다.

가져오는 데이터 집합은 이 솔루션 템플릿에 사용되는 [Turbofan 엔진 성능 저하 시뮬레이션 데이터 집합](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan)에서 사용하는 데이터 집합과 일치하지 않을 수 있습니다. 데이터 및 요구 사항을 이해하는 것은 사용자 고유 데이터로 작업하기 위해 이 템플릿을 수정하는 방법에서 중요합니다. Azure 기계 학습 서비스를 처음 접하는 경우 [첫 번째 실험을 만드는 방법](machine-learning-create-experiment.md)의 예제를 사용하여 소개를 확인할 수 있습니다.

다음 섹션에서는 새 데이터 집합을 도입할 때 수정해야 하는 템플릿의 섹션을 설명합니다.

### Azure 이벤트 허브

Azure 이벤트 허브 서비스는 CSV 또는 JSON 형식으로 허브에 게시할 수 있는 데이터에 매우 일반적입니다. Azure 이벤트 허브에서 특별한 처리가 발생하지 않지만 공급되는 데이터를 이해하는 것이 중요합니다.

이 문서는 데이터를 수집하는 방법을 설명하지 않지만 이벤트 허브 API를 사용하여 Azure 이벤트 허브에 이벤트 또는 데이터를 손쉽게 보낼 수 있습니다.

### Azure 스트림 분석

Azure 스트림 분석 서비스는 데이터 스트림에서 읽고 원하는 수의 원본으로 데이터를 출력하여 거의 실시간 분석을 제공하는 데 사용됩니다.

항공 솔루션 템플릿에 대한 예측 유지 관리의 경우 Azure 스트림 분석 쿼리는 각각 Azure 이벤트 허브 서비스에서 이벤트를 소비하고 4개의 고유 위치에 출력을 갖는 4개의 하위 쿼리로 구성됩니다. 이러한 출력은 세 개의 Power BI 데이터 집합 및 하나의 Azure 저장소 위치로 구성됩니다.

Azure 스트림 분석 쿼리는 다음으로 찾을 수 있습니다.

-   Azure 포털에 로그인

-   솔루션이 배포될 때 생성된 스트림 분석 작업 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) 배치(*예:* 예측 유지 관리 솔루션에 대한**maintenancesa02asapbi** 및 **maintenancesa02asablob**)

-   선택

    -   쿼리 입력을 보려면 ***입력***

    -   쿼리 자체를 보려면 ***쿼리***

    -   다른 출력을 보려면 ***출력***

Azure 스트림 분석 쿼리 생성에 대한 정보는 MSDN의 [스트림 분석 쿼리 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 찾을 수 있습니다.

이 솔루션에서 쿼리는 이 솔루션 템플릿의 일부로 제공되는 Power BI 대시보드에 들어오는 데이터 스트림에 대한 거의 실시간 분석 정보로 세 개의 데이터 집합을 출력합니다. 들어오는 데이터 형식에 대한 암시적 지식이 있기 때문에 이러한 쿼리는 데이터 형식에 기반하여 변경되어야 합니다.

두 번째 스트림 분석 작업 **maintenancesa02asablob**의 쿼리는 모든 [이벤트 허브](https://azure.microsoft.com/services/event-hubs/) 이벤트를 [Azure 저장소](https://azure.microsoft.com/services/storage/)에 출력하므로 전체 이벤트 정보는 저장소로 스트리밍되므로 데이터 형식에 관계 없이 변경이 필요하지 않습니다.

### Azure 데이터 팩터리

[Azure 데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/) 서비스는 데이터의 이동 및 처리를 오케스트레이션합니다. 항공 솔루션 템플릿에 대한 예측 유지 관리에서 데이터 팩터리는 다양한 기술을 사용하여 데이터를 이동 및 처리하는 세 개의 [파이프라인](../data-factory/data-factory-create-pipelines.md)으로 구성됩니다. 솔루션의 배포로 만든 솔루션 템플릿 다이어그램의 맨 아래의 데이터 팩터리 노드를 열어 데이터 팩터리에 액세스할 수 있습니다. Azure 포털의 데이터 팩터리로 이동됩니다. 데이터 집합에서 오류가 표시되는 경우 데이터 생성기가 시작되기 전에 배포된 데이터로 인한 것이므로 이러한 오류를 무시할 수 있습니다. 이러한 오류가 발생해도 데이터 팩터리가 제대로 작동합니다.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

이 섹션에서는 [Azure 데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/)에 포함된 필요한 [파이프라인](../data-factory/data-factory-create-pipelines.md) 및 [작업](../data-factory/data-factory-create-pipelines.md)을 설명합니다. 솔루션의 다이어그램 보기는 다음과 같습니다.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

이 팩터리의 두 파이프라인은 데이터를 분할하고 집계하는 데 사용되는 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트를 포함합니다. 언급했듯이 스크립트는 설치하는 동안 만든 [Azure 저장소](https://azure.microsoft.com/services/storage/) 계정에 있습니다. 해당 위치는 maintenancesascript\\\script\\\hive\\\(또는 https://[Your 솔루션 이름].blob.core.windows.net/maintenancesascript)입니다.

[Azure 스트림 분석](#azure-stream-analytics-1) 쿼리와 유사하게 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트는 들어오는 데이터 형식에 대한 암시적 지식을 가지며 이러한 쿼리는 데이터 형식 및 [기능 엔지니어링](machine-learning-feature-selection-and-engineering.md) 요구 사항에 따라 변경해야 합니다.

#### *AggregateFlightInfoPipeline*

이 [파이프라인](../data-factory/data-factory-create-pipelines.md)은 [Azure 스트림 분석](https://azure.microsoft.com/services/stream-analytics/) 작업 동안 [Azure 저장소](https://azure.microsoft.com/services/storage/)에 넣은 데이터를 분할하도록 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트를 실행하는 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx)를 사용한 단일 작업([HDInsightHive](../data-factory/data-factory-hive-activity.md) 작업)을 포함합니다.

이 분할 작업에 대한 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트는 ***AggregateFlightInfo.hql***입니다.

#### *MLScoringPipeline*

이 [파이프라인](../data-factory/data-factory-create-pipelines.md)은 여러 작업을 포함하고 해당 최종 결과는 이 솔루션 템플릿과 연결된 [Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 실험의 점수가 매겨진 예측입니다.

이에 포함된 작업은 다음과 같습니다.

-   [Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 실험에 필요한 집계 및 기능 엔지니어링을 수행하도록 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트를 실행하는 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx)를 사용한 [HDInsightHive](../data-factory/data-factory-hive-activity.md) 작업. 이 분할 작업에 대한 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 스크립트는 ***PrepareMLInput.hql***입니다.

-   [HDInsightHive](../data-factory/data-factory-hive-activity.md) 작업의 결과를 [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 작업으로 액세스할 수 있는 단일 [Azure 저장소](https://azure.microsoft.com/services/storage/) Blob으로 이동하는 [복사](https://msdn.microsoft.com/library/azure/dn835035.aspx) 작업.

-   단일 [Azure 저장소](https://azure.microsoft.com/services/storage/) Blob에 배치되는 결과를 유발하는 [Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 실험을 호출하는 [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 작업.

#### *CopyScoredResultPipeline*

이 [파이프라인](../data-factory/data-factory-create-pipelines.md)은 ***MLScoringPipeline***에서 [Azure 기계 학습](#azure-machine-learning) 실험의 결과를 솔루션 템플릿 설치의 일부로 프로비전되는 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)에 이동하는 단일 작업([복사](https://msdn.microsoft.com/library/azure/dn835035.aspx) 작업)을 포함합니다.

### Azure 기계 학습

이 솔루션 템플릿에 사용된 [Azure 기계 학습](https://azure.microsoft.com/services/machine-learning/) 실험은 항공기 엔진의 잔여 수명(RUL)을 제공합니다. 실험은 사용된 데이터 집합에 특정되므로 가져온 데이터에 특정된 수정 또는 대체가 필요합니다.

Azure 기계 학습 실험 생성 방법에 대한 정보는 [예측 유지 관리: 1/3단계, 데이터 준비 및 기능 엔지니어링](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)을 참조하세요.

## **진행률 모니터링**
 데이터 생성기가 시작되면 파이프라인이 하이드레이션하기 시작하고 솔루션의 다양한 구성 요소가 데이터 팩터리에서 발급한 명령을 실행하는 작업을 시작합니다. 두 가지 방법으로 파이프라인을 모니터링할 수 있습니다.

1. 스트림 분석 작업 중 하나는 Blob 저장소에 들어오는 원시 데이터를 씁니다. 솔루션을 성공적으로 배포한 화면에서 솔루션의 Blob 저장소 구성 요소를 클릭하고 오른쪽 창에서 열기를 클릭하면 [관리 포털](https://portal.azure.com/)로 이동합니다. Blob을 클릭합니다. 다음 패널에서 컨테이너 목록이 표시됩니다. **maintenancesadata**를 클릭합니다. 다음 패널에서 **rawdata** 폴더가 표시됩니다. rawdata 폴더 안에 hour=17, hour=18 등과 같은 이름을 가진 폴더가 표시됩니다. 이러한 폴더가 표시되는 경우 원시 데이터가 성공적으로 컴퓨터에 생성되고 Blob 저장소에 저장되고 있음을 나타냅니다. 해당 폴더에 MB의 한정된 크기로 있어야 하는 csv 파일이 표시됩니다.

2. 파이프라인의 마지막 단계는 SQL 데이터베이스에 데이터(예: 기계 학습에서 예측)를 작성하는 것입니다. 데이터를 SQL 데이터베이스에 표시하려면 최대 3시간을 기다려야 할 수도 있습니다. 얼마나 많은 데이터를 SQL 데이터베이스에서 사용할 수 있는지를 모니터링하는 한 가지 방법은 [azure 포털](https://manage.windowsazure.com/)을 통한 것입니다. 왼쪽 패널에서 SQL 데이터베이스 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png)를 찾고 클릭합니다. 그런 다음 데이터베이스 **pmaintenancedb**를 찾고 클릭합니다. 맨 아래의 다음 페이지에서 관리를 클릭합니다.

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

	여기에서는 새 쿼리 및 행(예: PMResult의 select count(*))의 수에 대한 쿼리를 클릭할 수 있습니다. 데이터베이스 증가에 따라 테이블의 행 수도 증가해야 합니다.


## **Power BI 대시보드**

### 개요

이 섹션은 Azure 기계 학습(콜드 경로)의 배치 예측 결과 뿐 아니라 Azure 스트림 분석(실행 부하 과다 경로)에서 실시간으로 데이터를 시각화하도록 Power BI 대시보드를 설정하는 방법을 설명합니다.

### 콜드 경로 대시보드 설정

콜드 경로 데이터 파이프라인에서 필수적인 목표는 각 항공기 엔진이 비행(주기)을 완료하면 예측 RUL(잔여 수명)을 가져오는 것입니다. 예측 결과는 지난 3시간 동안 비행을 완료한 항공기 엔진을 예측하기 위해 3시간마다 업데이트됩니다.

Power BI는 예측 결과가 저장되는 해당 데이터 원본으로 Azure SQL 데이터베이스에 연결합니다. 참고: 1) 솔루션 배포 시 실제 예측은 3시간 이내로 데이터베이스에 표시됩니다. 생성기 다운로드와 함께 제공되는 pbix 파일은 지금 바로 Power BI 대시보드를 만들 수 있도록 일부 시드 데이터를 포함합니다. 2) 이 단계에서 필수 구성 요소는 무료 소프트웨어 [Power BI 데스크톱](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)을 다운로드 및 설치하는 것입니다.

다음 단계는 pbix 파일을 시각화에 대한 데이터(*예:* 예측 결과)를 포함하는 솔루션 배포 시 스핀업된 SQL 데이터베이스에 연결하는 방법을 안내합니다.

1.  데이터베이스 자격 증명을 가져옵니다.

    다음 단계로 이동하기 전에 **데이터베이스 서버 이름, 데이터베이스 이름, 사용자 이름 및 암호**가 필요합니다. 찾는 방법을 안내하는 단계는 다음과 같습니다.

    -   솔루션 템플릿 다이어그램의 **'Azure SQL 데이터베이스'**가 녹색으로 바뀌면 클릭한 다음 **'열기'**를 클릭합니다.

    -   Azure 포털 페이지를 표시하는 새로운 브라우저 탭/창이 표시됩니다. 왼쪽 패널에서 **'리소스 그룹'**을 클릭합니다.

    -   솔루션 배포에 사용 중인 구독을 선택한 다음 **'YourSolutionName\_ResourceGroup'**을 선택합니다.

    -   새 팝 아웃 패널에서 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) 아이콘을 클릭하여 데이터베이스에 액세스합니다. 데이터베이스 이름은 이 아이콘 옆에 있으며(*예:* **'pmaintenancedb'**) **데이터베이스 서버 이름**은 서버 이름 속성 아래에 나열되고 **YourSoutionName.database.windows.net**과 유사하게 보입니다.

	-   데이터베이스 **사용자 이름** 및 **암호**는 솔루션 배포 중 이전에 기록된 사용자 이름 및 암호와 동일합니다.

2.  Power BI 데스크톱을 사용하여 콜드 경로 보고서 파일의 데이터 원본을 업데이트합니다.

    -   생성기 파일을 다운로드하고 압축을 푼 PC의 폴더에서 **PowerBI\\PredictiveMaintenanceAerospace.pbix** 파일을 두 번 클릭합니다. 파일을 열 때 경고 메시지가 표시되는 경우 무시합니다. 파일의 맨 위에서 **'쿼리 편집'**을 클릭합니다.

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	두 개의 테이블, **RemainingUsefulLife** 및 **PMResult**가 표시됩니다. 첫 번째 테이블을 선택하고 오른쪽 **'쿼리 설정'** 패널의 **'적용된 단계'** 아래에서 **'원본'** 옆의 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png)을(를) 클릭합니다. 표시되는 경고 메시지를 무시합니다.

    -   팝 아웃 창에서 **'서버'** 및 **'데이터베이스'**를 사용자 고유 서버 및 데이터베이스 이름으로 바꾼 다음 **'확인'**을 클릭합니다. 서버 이름의 경우 포트 1433을 지정했는지 확인합니다(**YourSoutionName.database.windows.net, 1433**). 데이터베이스 필드를 **pmaintenancedb**로 남겨 둡니다. 화면에 나타나는 경고 메시지를 무시합니다.

    -   다음 팝 아웃 창에서 왼쪽 창에 두 가지 옵션이 표시됩니다(**Windows** 및 **데이터베이스**). **'데이터베이스'**를 클릭하고 **'사용자 이름'** 및 **'암호'**를 입력합니다(처음으로 솔루션을 배포하고 Azure SQL 데이터베이스를 만들 때 입력한 사용자 이름 및 암호). ***이러한 설정을 적용할 수준 선택***에서 데이터베이스 수준 옵션을 선택합니다. 그런 다음 **'연결'**을 클릭합니다.

    -   두 번째 테이블 **PMResult**를 클릭한 다음 오른쪽 **'쿼리 설정'** 패널의 **'적용된 단계'** 아래에서 **'원본'** 옆의 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png)을(를) 클릭하고 위의 단계에서와 같이 서버 및 데이터베이스 이름을 업데이트하고 확인을 클릭합니다.

    -   이전 페이지로 안내된 후 창을 닫습니다. 메시지가 나타납니다. **적용**을 클릭합니다. 마지막으로 **저장** 단추를 클릭하여 변경 내용을 저장합니다. Power BI 파일은 이제 서버에 대한 연결을 설정합니다. 시각화가 비어 있는 경우 범례의 오른쪽 위 모퉁이의 지우개 아이콘을 클릭하여 모든 데이터를 시각화하도록 시각화의 선택 항목을 해제해야 합니다. 새로 고침 단추를 사용하여 시각화에 새 데이터를 반영합니다. 처음에 데이터 팩터리는 3시간마다 새로 고치도록 예약되므로 시각화에 시드 데이터만 나타납니다. 3시간 후 데이터를 새로 고치면 시각화에 적용된 새 예측이 표시됩니다.

3.  (선택 사항) 콜드 경로 대시보드를 [Power BI 온라인](http://www.powerbi.com/)에 게시합니다. 이 단계는 Power BI 계정(또는 Office 365 계정)이 필요합니다.

    -   **'게시'**를 클릭하고 몇 초 후 녹색 확인 표시로 "Power BI에 게시 성공!"을 표시하는 창이 나타납니다. 아래 링크 "Power BI에서 PredictiveMaintenanceAerospace.pbix 열기"를 클릭합니다. 자세한 지침을 찾으려면 [Power BI 데스크톱에서 게시](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)를 참조하세요.

    -   새 대시보드를 만들려면 왼쪽 창의 **대시보드** 섹션 옆의 **+** 기호를 클릭합니다. 이 새 대시보드에 대해 이름 "예측 유지 관리 데모"를 입력합니다.

    -   보고서를 열면 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png)을(를) 클릭하여 모든 시각화를 대시보드에 고정합니다. 자세한 지침을 찾으려면 [보고서에서 Power BI 대시보드에 타일 고정](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)을 참조하세요. 대시보드 페이지로 이동하고 시각화의 크기 및 위치를 조정하고 제목을 편집합니다. 타일을 편집하는 방법에 자세한 지침을 찾으려면 [타일 편집 - 하이퍼링크 크기 조정, 이동, 이름 바꾸기, 고정, 삭제, 추가](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)를 참조하세요. 다음은 고정된 몇 가지 콜드 경로 시각화를 사용한 예제 대시보드입니다. 데이터 생성기의 실행 시간에 따라 시각화에 대한 숫자가 달라질 수 있습니다. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png) <br/>
    -   데이터 새로 고침을 예약하려면 **PredictiveMaintenanceAerospace** 데이터 집합 위로 마우스를 가져가고 ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png)을(를)클릭한 다음 **새로 고침 예약**을 선택합니다. <br/> **참고:** 경고 메시지가 표시되는 경우 **자격 증명 편집**을 클릭하고 데이터베이스 자격 증명이 1단계에서 설명된 것과 동일한지 확인합니다. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   **새로 고침 예약** 섹션을 확장합니다. "데이터를 최신 상태로 유지"를 켭니다. <br/>
    -   필요에 따라 새로 고침을 예약합니다. 자세한 정보를 찾으려면 [Power BI에서 데이터 새로 고침](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)을 참조하세요.

### 실행 부하 과다 경로 대시보드 설정

다음 단계에서는 솔루션 배포 시 생성된 스트림 분석 작업에서 실시간으로 데이터 출력을 시각화하는 방법을 안내합니다. 다음 단계를 수행하려면 [Power BI 온라인](http://www.powerbi.com/) 계정이 필요합니다. 계정이 없는 경우 [새로 만들](https://powerbi.microsoft.com/pricing) 수 있습니다.

1.  Azure 스트림 분석(ASA)에 Power BI 출력을 추가합니다.

    -  [Azure 스트림 분석 및 Power BI: 스트리밍 데이터의 실시간 가시성에 대한 실시간 분석 대시보드](stream-analytics-power-bi-dashboard.md)의 침에 따라 Power BI 대시보드로 Azure 스트림 분석 작업의 출력을 설정해야 합니다.
	- ASA 쿼리 출력은 **aircraftmonitor**, **aircraftalert** 및 **flightsbyhour** 세 개입니다. 쿼리 탭을 클릭하여 쿼리를 볼 수 있습니다. 이 테이블 각각에 해당하는 출력을 ASA에 추가해야 합니다. 첫 번째 출력(*예:* **aircraftmonitor**)을 추가하는 경우 **출력 별칭**, **데이터 집합 이름** 및 **테이블 이름**은 같습니다(**aircraftmonitor**). **aircraftalert** 및 **flightsbyhour**에 대한 출력을 추가하려면 단계를 반복합니다. 세 개의 출력 테이블을 추가하고 ASA 작업을 시작한 후 확인 메시지를 받아야 합니다(*예:* "스트림 분석 작업 maintenancesa02asapbi 시작 성공").

2. [Power BI 온라인](http://www.powerbi.com)에 로그인합니다

    -   내 작업 영역의 왼쪽 패널 데이터 집합 섹션에 ***데이터 집합*** 이름**aircraftmonitor**, **aircraftalert** 및 **flightsbyhour**가 나타납니다. 이는 이전 단계에 Azure 스트림 분석에서 푸시한 스트리밍 데이터입니다. 데이터 집합 **flightsbyhour**는 기반 SQL 쿼리의 특성 때문에 달느 두 데이터 집합과 동시에 표시되지 않을 수 있습니다. 그러나 한 시간 후에 표시됩니다.
    -   ***시각화*** 창이 열려 있고 화면 오른쪽에 표시되는지 확인합니다.

3. 데이터가 Power BI로 흐르게 한 후 스트리밍 데이터의 시각화를 시작할 수 있습니다. 다음은 고정된 몇 가지 실행 부하 과다 경로 시각화를 사용한 예제 대시보드입니다. 적절한 데이터 집합에 따라 다른 대시보드 타일을 만들 수 있습니다. 데이터 생성기의 실행 시간에 따라 시각화에 대한 숫자가 달라질 수 있습니다.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. 다음은 위의 타일 중 하나를 만드는 몇몇 단계입니다. “센서 11의 Fleet 보기 vs. 임계값 48.26” 타일:

    -   왼쪽 패널 데이터 집합 섹션에서 데이터 집합 **aircraftmonitor**를 클릭합니다.

    -   **꺾은선형 차트** 아이콘을 클릭합니다.

    -   **시각화** 창의 "축" 아래에 보이도록 **필드** 창에서 **처리**를 클릭합니다.

    -   "값" 아래에 모두 표시되도록 "s11" 및 "s11\_alert"을 클릭합니다. **s11** 및 **s11\_alert** 옆에 있는 작은 화살표를 클릭하고 "합계"를 "평균"으로 변경합니다.

    -   위의 **저장**을 클릭하고 "aircraftmonitor"로 보고서 이름을 지정합니다. "aircraftmonitor"라는 보고서가 왼쪽의 **탐색기** 창에서 **보고서** 섹션에 표시됩니다.

    -   이 꺾은선형 차트의 오른쪽 위 모퉁이에서 **시각화 고정** 아이콘을 클릭합니다. "대시보드에 고정" 창이 대시보드를 선택하도록 표시될 수 있습니다. "예측 유지 관리 데모"를 선택한 다음 "고정"을 클릭합니다.

    -   대시보드의 이 타일 위로 마우스를 이동하고 오른쪽 위 모서리에서 "편집" 아이콘을 클릭하여 제목을 "센서 11의 Fleet 보기 vs. 임계값 48.26"으로 부제목을 "시간이 지남에 Fleet의 평균"으로 변경합니다.

## **솔루션을 삭제하는 방법**
데이터 생성기를 실행하면 비용이 더 높아지므로 솔루션을 활발히 사용하지 않을 때 데이터 생성기를 중지하십시오. 솔루션을 사용하지 않는 경우 삭제하십시오. 솔루션을 삭제하면 솔루션을 배포할 때 구독에 프로 비전된 모든 구성 요소가 삭제됩니다. 솔루션을 삭제하려면 솔루션 템플릿 왼쪽 패널의 솔루션 이름을 클릭하고 삭제를 클릭합니다.

## **비용 예측 도구**

다음 두 가지 도구는 구독에서 항공 솔루션 템플릿에 대한 예측 유지 관리를 실행하는 데 필요한 총 비용을 더욱 잘 이해할 수 있도록 사용할 수 있습니다.

-   [Microsoft Azure 비용 추정 도구(온라인)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure 비용 추정 도구(데스크톱)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0914_2016-->