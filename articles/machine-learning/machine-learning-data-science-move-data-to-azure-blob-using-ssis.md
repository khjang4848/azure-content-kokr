<properties
	pageTitle="SSIS 커넥터를 사용하여 Azure Blob 저장소의 데이터 이동 | Microsoft Azure"
	description="SSIS 커넥터를 사용하여 Azure Blob 저장소의 데이터 이동"
	services="machine-learning,storage"
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

# SSIS 커넥터를 사용하여 Azure Blob 저장소의 데이터 이동

[Azure용 SQL Server Integration Services 기능 팩](https://msdn.microsoft.com/library/mt146770.aspx)에서는 Azure에 연결하고, Azure와 온-프레미스 데이터 원본 간에 데이터를 전송하며, Azure에 저장된 데이터를 처리하는 구성 요소를 제공합니다.

Azure Blob 저장소로 및/또는 저장소에서 데이터를 이동하는 데 사용되는 기술 지침은 여기에 연결되어 있습니다.

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


고객은 온-프레미스 데이터를 클라우드로 이동한 후 Azure 서비스에서 데이터에 액세스하여 Azure 기술의 완전한 기능을 활용할 수 있습니다. 예를 들어 Azure 기계 학습 또는 HDInsight 클러스터에서 사용할 수 있습니다.

이는 일반적으로 [SQL](machine-learning-data-science-process-sql-walkthrough.md) 및 [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) 연습의 첫 번째 단계입니다.

SSIS를 사용하여 하이브리드 데이터 통합 시나리오에서 일반적인 비즈니스 요구 사항을 충족하는 정식 시나리오에 대한 자세한 내용은 [Azure용 SQL Server Integration Services 통합 팩으로 더 많은 작업 수행](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) 블로그를 참조하세요.

> [AZURE.NOTE] Azure Blob 저장소에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../storage/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.

## 필수 조건

이 문서에 설명된 작업을 수행하려면 Azure 구독이 있어야 하며 Azure 저장소 계정을 설정해야 합니다. 데이터를 업로드하거나 다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

- **Azure 구독**을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- **저장소 계정**을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.


**SSIS 커넥터**를 사용하려면 다음을 다운로드해야 합니다.

- **SQL Server 2014 또는 2016 Standard 이상**: 설치 파일에 SQL Server Integration Services가 포함되어 있습니다.
- **Azure용 Microsoft SQL Server 2014 또는 2016 Integration Services 기능 팩**: [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) 및 [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) 페이지에서 다운로드할 수 있습니다.

> [AZURE.NOTE] SSIS는 SQL Server와 함께 설치되지만 Express 버전에는 포함되어 있지 않습니다. 다양한 버전의 SQL Server에 포함된 응용 프로그램에 대한 자세한 내용은 [SQL Server 버전](http://www.microsoft.com/ko-KR/server-cloud/products/sql-server-editions/)을 참조하세요.

SSIS에 대한 교육 자료는 [SSIS에 대한 실습 교육](http://www.microsoft.com/download/details.aspx?id=20766)을 참조하세요.

SISS를 사용하여 간단한 ETL(추출, 변환 및 로드) 패키지를 빌드하는 방법에 대한 자세한 내용은 [SSIS 자습서: 간단한 ETL 패키지 만들기](https://msdn.microsoft.com/library/ms169917.aspx)를 참조하세요.

## NYC Taxi 데이터 집합 다운로드  
여기에 설명된 예제에서는 공개적으로 제공되는 데이터 집합인 [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) 데이터 집합을 사용합니다. 이 데이터 집합은 2013년 뉴욕 시의 1억 7,300만 건에 달하는 택시 승차 기록으로 구성됩니다. 데이터는 여정 정보 데이터와 요금 데이터의 두 종류가 있습니다. 월별로 하나의 파일씩 총 24개의 파일이 있으며, 각 파일은 압축되지 않은 크기가 약 2GB입니다.


## Azure Blob 저장소에 데이터 업로드
SSIS 기능 팩을 사용하여 온-프레미스에서 Azure blob 저장소로 데이터를 이동하려면 여기에 표시된 대로 [**Azure Blob 업로드 작업**](https://msdn.microsoft.com/library/mt146776.aspx)의 인스턴스를 사용합니다.

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


작업에 사용되는 매개 변수는 다음과 같습니다.


필드|설명|
----------------------|----------------|
**AzureStorageConnection**|기존 Azure 저장소 연결 관리자를 지정하거나, blob 파일이 호스트되는 위치를 가리키는 Azure 저장소 계정을 참조하는 새 Azure 저장소 연결 관리자를 만듭니다.|
**BlobContainer**|업로드된 파일을 blob로 유지할 blob 컨테이너의 이름을 지정합니다.|
**BlobDirectory**|업로드된 파일을 블록 blob로 저장할 blob 디렉터리를 지정합니다. blob 디렉터리는 가상 계층 구조입니다. blob가 이미 있는 경우 바뀝니다.|
**LocalDirectory**|업로드할 파일이 포함된 로컬 디렉터리를 지정합니다.|
**FileName**|지정된 이름 패턴의 파일을 선택할 이름 필터를 지정합니다. 예를 들어 MySheet*.xls*는 MySheet001.xls 및 MySheetABC.xlsx와 같은 파일을 포함합니다.|
**TimeRangeFrom/TimeRangeTo**|시간 범위 필터를 지정합니다. *TimeRangeFrom* 이후부터 *TimeRangeTo* 이전까지 수정된 파일이 포함됩니다.|


> [AZURE.NOTE] 전송을 시도하기 전에 **AzureStorageConnection** 자격 증명이 올바르고 **BlobContainer**가 있어야 합니다.

## Azure Blob 저장소에서 데이터 다운로드

SSIS를 사용하여 Azure Blob 저장소에서 온-프레미스 저장소로 데이터를 다운로드하려면 [Azure Blob 업로드 작업](https://msdn.microsoft.com/library/mt146779.aspx)의 인스턴스를 사용합니다.

##고급 SSIS-Azure 시나리오
SSIS 기능 팩을 사용하면 패키징 작업을 통해 보다 복잡한 흐름을 처리할 수 있습니다. 예를 들어 blob 데이터를 HDInsight 클러스터에 직접 공급하여 해당 출력을 blob로 다시 다운로드한 다음 온-프레미스 저장소에 다운로드할 수 있습니다. SSIS는 추가 SSIS 커넥터를 사용하여 HDInsight 클러스터에서 Hive 및 Pig 작업을 실행할 수 있습니다.

- SSIS를 사용하여 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하려면 [Azure HDInsight Hive 작업](https://msdn.microsoft.com/library/mt146771.aspx)을 사용합니다.
- SSIS를 사용하여 Azure HDInsight 클러스터에서 Pig 스크립트를 실행하려면 [Azure HDInsight Pig 작업](https://msdn.microsoft.com/library/mt146781.aspx)을 사용합니다.

<!---HONumber=AcomDC_0921_2016-->