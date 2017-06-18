<properties
   pageTitle="Azure 데이터 레이크 저장소 개요 | Azure"
   description="Azure 데이터 레이크 저장소 및 기타 데이터 저장소를 통해 제공하는 값 이해"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Azure 데이터 레이크 저장소 개요

Azure 데이터 레이크 저장소는 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure 데이터 레이크를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

> [AZURE.TIP] [데이터 레이크 저장소 학습 경로](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/)를 사용하여 Azure 데이터 레이크 저장소 서비스 탐색을 시작합니다.

Azure 데이터 레이크 저장소는 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스할 수 있습니다. 저장된 데이터에 대한 분석을 사용하도록 특별히 설계되었으며 데이터 분석 시나리오에 대한 성능을 위해 조정됩니다. 기본적으로 실제 엔터프라이즈 사용 사례에 필수적인 모든 엔터프라이즈급 기능-보안, 관리 효율성, 확장성, 안정성 및 가용성-을 포함합니다.


![Azure 데이터 레이크](./media/data-lake-store-overview/data-lake-store-concept.png)

Azure 데이터 레이크의 주요 기능 중 일부는 다음과 같습니다.

### Hadoop용으로 작성

Azure 데이터 레이크 저장소는 HDFS(Hadoop Distributed File System)와 호환되고 Hadoop 에코시스템과 함께 작동되는 Apache Hadoop 파일 시스템입니다. WebHDFS API를 사용하는 기존 HDInsight 응용 프로그램 또는 서비스는 데이터 레이크 저장소와 쉽게 통합할 수 있습니다. 데이터 레이크 저장소는 또한 응용 프로그램에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

데이터 레이크 저장소에 저장된 데이터는 MapReduce 또는 Hive와 같은 Hadoop 분석 프레임워크를 사용하여 쉽게 분석될 수 있습니다. Microsoft Azure HDInsight 클러스터는 데이터 레이크 저장소에 저장된 데이터에 직접 액세스하도록 프로비전되고 구성될 수 있습니다.

### 무제한 저장소, 페타바이트 파일

Azure 데이터 레이크 저장소는 무제한 저장소를 제공하며 분석에 대한 다양한 데이터를 저장하는데 적합합니다. 데이터 레이크에 저장될 수 있는 계정 크기, 파일 크기 또는 데이터 양에 어떠한 제한도 적용하지 않습니다. 개별 파일의 범위는 모든 종류의 데이터를 저장하는데 적합하도록 킬로바이트에서 페타바이트까지입니다. 데이터는 여러 복사본을 만들어 영구적으로 저장되며 데이터가 데이터 레이크에 저장될 수 있는 기간에 제한이 없습니다.

### 빅 데이터 분석에 대한 성능 조정

Azure 데이터 레이크 저장소는 많은 양의 데이터에 대한 쿼리 및 분석을 위해 대규모 처리 능력이 필요한 대규모 분석 시스템을 실행할 수 있도록 제작되었습니다. 데이터 레이크는 개별 저장소 서버의 수 이상으로 파일의 일부분을 배포합니다. 데이터 분석을 수행하기 위해 병렬로 파일을 읽을 때 읽기 처리량이 향상됩니다.


### 엔터프라이즈 지원: 고가용성 및 보안

Azure 데이터 레이크 저장소는 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다. 기업에서는 기존 데이터 플랫폼의 중요한 부분으로 솔루션에서 Azure 데이터 레이크를 사용할 수 있습니다.

또한 데이터 레이크 저장소는 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure 데이터 레이크 저장소의 데이터 보안](#DataLakeStoreSecurity)을 참조하세요.


### 모든 데이터

Azure 데이터 레이크 저장소는 사전 변환 없이 모든 데이터를 고유 형식으로 그대로 저장할 수 있습니다. 데이터 레이크 저장소는 데이터가 로드되기 전에 정의되어야 하는 스키마를 필요로 하지 않으므로 개별 분석 프레임워크가 데이터를 해석하고 분석 시 스키마를 정의할 때까지 그대로 둡니다. 임의 크기 및 형식의 파일을 저장 가능한 것은 데이터 레이크 저장소가 구조화, 반구조화 및 비구조화된 데이터를 처리할 수 있도록 합니다.

데이터에 대한 Azure 데이터 레이크 저장소 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure 포털 및 Azure Powershell을 사용하여 저장된 데이터에서 작동합니다. 이러한 인터페이스 및 적절한 컨테이너를 사용하여 저장소에 데이터를 저장하는 한 모든 종류의 데이터를 저장할 수 있습니다. 데이터 레이크 저장소는 저장하는 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.


## <a name="DataLakeStoreSecurity"></a>Azure 데이터 레이크 저장소의 데이터 보호

Azure 데이터 레이크 저장소는 인증 및 액세스 제어 목록(ACL)을 위해 Azure Active Directory를 사용하여 데이터에 대한 액세스를 관리합니다.

| 기능 | 설명 |
|-----------------------------------------|------------------------------------------|
| 인증 | Azure 데이터 레이크 저장소는 Azure 데이터 레이크 저장소에 저장된 모든 데이터에 대한 ID 및 액세스 관리를 위해 Azure Active Directory(AAD)와 통합합니다. 통합의 결과로 Azure 데이터 레이크는 다단계 인증, 조건부 액세스, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 보안 모니터링 및 경고 등을 포함한 모든 AAD 기능의 이점을 얻습니다. Azure 데이터 레이크 저장소는 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. |
| 액세스 제어 | Azure 데이터 레이크 저장소는 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. 현재 릴리스에서 ACL는 루트 폴더, 하위 폴더뿐만 아니라 개별 파일에서 사용할 수 있습니다. 루트 폴더에 적용할 ACL는 모든 자식 폴더/파일에도 적용할 수 있습니다.|

Data Lake 저장소의 데이터를 보호하는 방법에 대한 자세히 알아보려 합니다. 아래 링크를 따라갑니다.

* 데이터 레이크 저장소의 데이터 보안 방법에 대한 지침은 [Azure 데이터 레이크 저장소의 데이터 보안](data-lake-store-secure-data.md)을 참조하세요.
* 비디오를 선호하십니까? Data Lake 저장소에 저장된 데이터를 보호하는 방법은 [이 비디오를 참고하세요](https://mix.office.com/watch/1q2mgzh9nn5lx).

## Azure 데이터 레이크 저장소와 호환되는 응용 프로그램

Azure Data Lake 저장소는 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와 원활하게 통합됩니다. 따라서 Data Lake 저장소는 데이터 저장소 요구 사항에 맞는 완벽한 옵션입니다. 다른 Azure 서비스 뿐만 아니라 오픈 소스 구성 요소와 함께 Data Lake 저장소를 사용할 수 있는 방법을 자세히 알아보려면 아래 링크를 따라갑니다.

* Data Lake 저장소와 상호 운용 가능한 오픈 소스 응용 프로그램 목록은 [Azure Data Lake 저장소와 호환되는 응용 프로그램 및 서비스](data-lake-store-compatible-oss-other-applications.md)를 참조하세요.
* 데이터 레이크 저장소를 다른 Azure 서비스와 사용하여 광범위한 시나리오를 활성화할 수 있는 방법을 이해하려면 [다른 Azure 서비스와 통합](data-lake-store-integrate-with-other-services.md)을 참조하세요.
* [Data Lake 저장소를 사용하는 시나리오](data-lake-store-data-scenarios.md)를 참조하여 데이터 수집, 데이터 처리, 데이터 다운로드 및 데이터 시각화와 같은 시나리오에서 Data Lake 저장소를 사용하는 방법을 알아봅니다.

## Azure Data Lake Store 파일 시스템(adl://)이란 무엇입니까?

Hadoop 환경에서 새로운 파일 시스템인 AzureDataLakeFilesystem(adl://)을 통해 Data Lake Store에 액세스할 수 있습니다(HDInsight 클러스터에서 사용 가능). adl://를 사용하는 응용 프로그램 및 서비스는 현재 WebHDFS에서 사용할 수 없는 추가 성능 최적화의 장점을 활용할 수 있습니다. 그 결과 데이터 레이크 저장소는 권장되는 옵션의 adl://를 사용하여 최상의 성능을 이용하거나 WebHDFS API를 계속해서 직접 사용하여 기존 코드를 유지하도록 유연성을 제공합니다. Azure HDInsight는 데이터 레이크 저장소에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

`adl://<data_lake_store_name>.azuredatalakestore.net`을(를) 사용하여 데이터 레이크 저장소의 데이터에 액세스할 수 있습니다. 데이터 레이크 저장소의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터의 속성 보기](data-lake-store-get-started-portal.md#properties)를 참조하세요.

## Azure 데이터 레이크 저장소를 사용하여 어떻게 시작합니까?

Azure 포털을 사용하여 데이터 레이크 저장소를 프로비전하는 방법은 [Azure 포털을 사용하여 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)을 참조하세요. Azure 데이터 레이크를 프로비전하면 데이터 레이크 저장소를 통해 Azure 데이터 레이크 분석 또는 Azure HDInsight와 같은 빅 데이터 제공을 사용하는 방법을 배울 수 있습니다. 또한 .NET 응용 프로그램을 만들어 Azure 데이터 레이크 저장소 계정을 만들고 데이터 업로드, 데이터 다운로드 등과 같은 작업을 수행할 수 있습니다.

- [Azure 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [데이터 레이크 저장소와 함께 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
- [.NET SDK를 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-net-sdk.md)


## Data Lake 저장소 비디오

학습을 위해 비디오를 선호하는 경우 Data Lake 저장소는 다양한 기능에 대한 비디오를 제공합니다.

* [Azure Data Lake 저장소 계정 만들기](https://mix.office.com/watch/1k1cycy4l4gen)
* [데이터 탐색기를 사용하여 Azure Data Lake 저장소에서 데이터 관리](https://mix.office.com/watch/icletrxrh6pc)
* [Azure Data Lake 분석을 Azure Data Lake 저장소에 연결](https://mix.office.com/watch/qwji0dc9rx9k)
* [Data Lake 분석을 통해 Azure Data Lake 저장소에 액세스](https://mix.office.com/watch/1n0s45up381a8)
* [Azure HDInsight를 Azure Data Lake 저장소에 연결](https://mix.office.com/watch/l93xri2yhtp2)
* [Hive 및 Pig를 통해 Azure Data Lake 저장소에 액세스](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [DistCp(Hadoop 분산 복사)를 사용하여 Azure Data Lake 저장소 간에 데이터 복사](https://mix.office.com/watch/1liuojvdx6sie)
* [Apache Sqoop을 사용하여 관계형 원본 및 Azure Data Lake 저장소 간에 데이터 이동](https://mix.office.com/watch/1butcdjxmu114)
* [Azure 데이터 팩터리를 사용하여 Azure Data Lake 저장소에 대한 데이터 오케스트레이션](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Azure Data Lake 저장소에서 데이터 보호](https://mix.office.com/watch/1q2mgzh9nn5lx)

<!-----HONumber=AcomDC_0914_2016-->