<properties 
   pageTitle="Data Lake 저장소와 관련된 데이터 시나리오 | Microsoft Azure" 
   description="데이터를 Data Lake 저장소에 수집, 처리, 다운로드 및 시각화할 수 있는 다양한 시나리오 및 도구 이해" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# 빅 데이터 요구 사항에 Azure Data Lake 저장소 사용

빅 데이터 처리에는 네 가지 주요 단계가 있습니다.

* 실시간으로 또는 배치로 대량의 데이터를 데이터 저장소에 수집
* 데이터 처리
* 데이터 다운로드
* 데이터 시각화

이 문서에서는 Azure Data Lake 저장소와 관련된 네 단계를 살펴보고 빅 데이터 요구 사항을 충족하는 옵션 및 도구에 대해 알아보겠습니다.

## Data Lake 저장소에 데이터 수집

이 섹션에서는 다양한 데이터 원본 그리고 그 데이터를 Data Lake 저장소 계정에 수집하는 여러 가지 방법을 살펴봅니다.

![Data Lake 저장소에 데이터 수집](./media/data-lake-store-data-scenarios/ingest-data.png "Data Lake 저장소에 데이터 수집")

### 임시 데이터

빅 데이터 응용 프로그램의 프로토타입 제작에 사용되는 작은 데이터 집합을 나타냅니다. 데이터 원본에 따라 임시 데이터를 수집하는 여러 가지 방법이 있습니다.

| 데이터 원본 | 로컬 컴퓨터를 사용하여 |
|--------------------|----------------------------------------------------------------------------------------|
| 수집 | <ul> <li>[Azure 포털](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure 플랫폼 간 CLI](data-lake-store-get-started-cli.md)</li> <li>[Data Lake Tools for Visual Studio 사용](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure 저장소 Blob | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 클러스터에서 실행되는 DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### 스트리밍된 데이터

응용 프로그램, 장치, 센서 등 다양한 원본을 통해 생성할 수 있는 데이터를 나타냅니다. 이 데이터는 다양한 도구를 사용하여 Data Lake 저장소에 수집할 수 있습니다. 이러한 도구는 일반적으로 이벤트 단위로 데이터를 실시간으로 캡처 및 처리한 다음, 추가로 처리할 수 있도록 이벤트를 배치로 Data Lake 저장소에 씁니다.

다음은 사용할 수 있는 도구입니다.
 
* [Azure 스트림 분석](../stream-analytics-data-lake-output) - Azure Data Lake Store 출력을 사용하면 이벤트 허브에 수집된 이벤트를 Azure Data Lake에 쓸 수 있습니다.
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md) - Storm 클러스터에서 Data Lake Store에 직접 데이터를 쓸 수 있습니다.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – 이벤트 허브에서 이벤트를 받은 다음 [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)를 사용하여 Data Lake Store에 데이터를 쓸 수 있습니다.

### 관계형 데이터

관계형 데이터베이스의 데이터를 원본으로 사용할 수도 있습니다. 관계형 데이터베이스는 일정 기간 동안 엄청난 양의 데이터를 수집합니다. 이 데이터를 빅 데이터 파이프라인을 통해 처리하면 중요한 정보를 얻을 수 있습니다. 다음 도구를 사용하여 이러한 데이터를 Data Lake 저장소로 이동할 수 있습니다.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 데이터 팩터리](../data-factory/data-factory-data-movement-activities.md)

### 웹 서버 로그 데이터(사용자 지정 응용 프로그램을 사용하여 업로드)

이 유형의 데이터 집합은 한정적으로 호출됩니다. 빅 데이터 응용 프로그램이 사용되는 일반적인 사용 사례가 웹 서버 로그 데이터 분석이며 웹 서버 로그 데이터를 분석하려면 대용량의 로그 파일을 Data Lake 저장소에 업로드해야 하기 때문입니다. 다음 중 아무 도구를 사용하여 이러한 데이터를 업로드하는 고유의 스크립트 또는 응용 프로그램을 작성할 수 있습니다.

* [Azure 플랫폼 간 CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake 저장소 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure 데이터 팩터리](../data-factory/data-factory-data-movement-activities.md)

웹 서버 로그 데이터를 업로드하고 다른 종류의 데이터 (예: 소셜 정서 데이터)를 업로드하려는 경우 고유의 사용자 지정 스크립트/응용 프로그램을 작성하는 것이 좋습니다. 데이터 업로드 구성 요소를 더 큰 빅 데이터 응용 프로그램의 일부로 유연하게 포함할 수 있기 때문입니다. 어떤 경우에는 이 코드가 스크립트 형태 또는 간단한 명령줄 유틸리티의 형태를 취할 수 있습니다. 또 어떤 경우에는 빅 데이터 처리를 비즈니스 응용 프로그램 또는 솔루션에 통합하는 데 이 코드가 사용될 수 있습니다.


### Azure HDInsight 클러스터와 연결된 데이터

대부분의 HDInsight 클러스터 유형(Hadoop, HBase, Storm)은 데이터 저장소 리포지토리로 Data Lake 저장소를 지원합니다. HDInsight 클러스터는 Azure 저장소 Blob(WASB)에서 데이터에 액세스합니다. WASB에서 클러스터와 연결된 Data Lake 저장소 계정으로 데이터를 복사하면 성능을 향상할 수 있습니다. 다음 도구를 사용하여 데이터를 복사할 수 있습니다.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure 데이터 팩터리](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### 온-프레미스 또는 IaaS Hadoop 클러스터에 저장된 데이터

HDFS를 사용하여 로컬 컴퓨터의 기존 Hadoop 클러스터에 대량의 데이터를 저장할 수 있습니다. Hadoop 클러스터는 온-프레미스 배포 또는 Azure의 IaaS 클러스터에 있을 수 있습니다. 이러한 데이터를 Azure Data Lake 저장소에 일회성 또는 반복적으로 복사하는 데는 요구 사항이 있을 수 있습니다. 이 작업을 수행하는 방법은 다양한 옵션이 있습니다. 다음은 이를 대체할 수 있는 방법 목록과 관련 절충 사항입니다.

| 접근 방식 | 세부 정보 | 장점 | 고려 사항 |
|-----------|---------|--------------|-----------------|
| Azure Data Factory(ADF)를 사용하여 Hadoop 클러스터에서 Azure Data Lake 저장소로 직접 데이터를 복사합니다. | [ADF는 데이터 원본으로 HDFS 지원](../data-factory/data-factory-hdfs-connector.md) | ADF는 HDFS에 대한 기본 지원과 일등급 종단 간 관리 및 모니터링을 제공합니다. | 온-프레미스 또는 IaaS 클러스터에 배포하려면 데이터 관리 게이트웨이가 필요합니다. |
| Hadoop에서 데이터를 파일로 내보냅니다. 그런 다음 적절한 메커니즘을 사용하여 파일을 Azure Data Lake 저장소에 복사합니다. | Azure Data Lake Store에 파일을 복사하는 방법: <ul><li>[Windows OS용 Azure PowerShell](data-lake-store-get-started-powershell.md)</li><li>[Windows 이외 OS용 Azure 플랫폼 간 CLI](data-lake-store-get-started-cli.md)</li><li>Data Lake Store SDK를 사용하는 사용자 지정 앱</li></ul> | 빠르게 시작할 수 있습니다. 맞춤 업로드를 수행할 수 있습니다. | 여러 기술을 사용하는 다단계 절차입니다. 도구를 맞춤화할 수 있으므로 시간이 지날수록 관리와 모니터링이 어려워집니다. |
| Distcp를 사용하여 Hadoop에서 Azure 저장소로 데이터를 복사합니다. 그런 다음 적절한 메커니즘을 사용하여 Azure 저장소에서 Data Lake 저장소로 데이터를 복사합니다. | Azure Storage에서 Data Lake Store로 데이터를 복사하는 방법: <ul><li>[Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)</li><li>[AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 클러스터에서 실행되는 Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| 오픈 소스 도구를 사용할 수 있습니다. | 여러 기술을 사용하는 다단계 절차입니다. |

### 매우 큰 데이터 집합

용량이 수 테라바이트에 달하는 데이터 집합을 업로드하는 경우 위에서 설명한 방법을 사용하면 속도가 느리고 비용이 많이 들 수 있습니다. 이러한 경우 아래의 옵션을 사용할 수 있습니다.

* **Azure Express 경로 사용**. Azure Express 경로를 사용하면 온-프레미스의 인프라와 Azure 데이터 센터 사이에 개인 연결을 만들 수 있습니다. 이렇게 하면 대용량 데이터를 안전하게 전송할 수 있습니다. 자세한 내용은 [Azure Express 경로 설명서](../expressroute/expressroute-introduction.md)를 참조하세요.


* **데이터를 "오프라인"으로 업로드**. Azure Express 경로를 사용할 수 없는 경우 [Azure 가져오기/내보내기 서비스](../storage/storage-import-export-service.md)를 사용하여 데이터가 들어 있는 하드 디스크를 Azure 데이터 센터로 보낼 수 있습니다. 데이터는 먼저 Azure Storage Blob에 업로드됩니다. 그러면 [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) 또는 [AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)를 사용하여 Azure 저장소 Blob에서 Data Lake Store로 데이터를 복사할 수 있습니다.

	>[AZURE.NOTE] 가져오기/내보내기 서비스를 사용할 때 Azure 데이터 센터로 보내는 디스크의 파일 크기가 200GB를 초과하면 안 됩니다.


## Data Lake 저장소에 저장된 데이터 처리

Data Lake 저장소의 데이터를 사용할 수 있게 되면 지원되는 빅 데이터 응용 프로그램을 사용하여 해당 데이터에 대해 분석을 실행할 수 있습니다. 현재, Azure HDInsight 및 Azure Data Lake 분석을 사용하여 Azure Data Lake 저장소에 저장된 데이터에 대한 데이터 분석 작업을 실행할 수 있습니다.

![Data Lake 저장소의 데이터 분석](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake 저장소의 데이터 분석")

다음 예를 살펴보세요.

* [Data Lake 저장소를 저장소로 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## Data Lake 저장소에서 데이터 다운로드

다음과 같은 시나리오를 처리하기 위해 Azure Data Lake 저장소에서 데이터를 다운로드하거나 데이터를 이동하려는 경우가 있습니다.

* 기존 데이터 처리 파이프라인과 상호 작용하기 위해 다른 리포지토리로 데이터를 이동합니다. 예를 들어 Data Lake 저장소에서 Azure SQL 데이터베이스 또는 온-프레미스 SQL Server로 데이터를 이동하려는 경우가 있습니다.
* 응용 프로그램 프로토타입을 빌드하는 동안 IDE 환경에서 데이터를 처리하기 위해 로컬 컴퓨터에 데이터를 다운로드 합니다.

![Data Lake 저장소에서 데이터 송신](./media/data-lake-store-data-scenarios/egress-data.png "Data Lake 저장소에서 데이터 송신")

이러한 경우 다음 옵션을 사용할 수 있습니다.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 데이터 팩터리](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

또한 다음 방법을 사용하여 Data Lake 저장소에서 데이터를 다운로드하는 고유의 스크립트/응용 프로그램을 작성할 수 있습니다.

* [Azure 플랫폼 간 CLI](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake 저장소 .NET SDK](data-lake-store-get-started-net-sdk.md)

## Data Lake 저장소에서 데이터 시각화

여러 서비스를 사용하여 Data Lake 저장소에 저장된 데이터를 시각적으로 표현할 수 있습니다.

![Data Lake 저장소에서 데이터 시각화](./media/data-lake-store-data-scenarios/visualize-data.png "Data Lake 저장소에서 데이터 시각화")

* 먼저 [Azure Data Factory를 사용하여 Data Lake Store에서 Azure SQL Data Warehouse로 데이터를 이동](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)할 수 있습니다.
* 그 후에는 [Power BI를 Azure SQL Data Warehouse와 통합](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md)하여 데이터를 시각적으로 표현할 수 있습니다.

<!---HONumber=AcomDC_0914_2016-->