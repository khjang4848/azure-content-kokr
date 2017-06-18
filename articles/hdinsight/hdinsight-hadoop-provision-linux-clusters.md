<properties
   	pageTitle="Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터 만들기 | Microsoft Azure"
   	description="브라우저, Azure CLI, Azure PowerShell, REST를 사용하거나 SDK를 통해 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터를 만드는 방법에 대해 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/06/2016"
   	ms.author="jgao"/>


# HDInsight에서 Linux 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Hadoop 클러스터는 클러스터에 있는 작업의 분산 처리에 사용되는 여러 가상 컴퓨터(노드)로 구성됩니다. Azure는 개별 노드의 설치 및 구현에 대한 세부 구현을 추상화하므로 일반적인 구성 정보만 제공해야 합니다. 이 문서에서는 이러한 구성 설정에 대해 알아봅니다.

## 클러스터 유형

현재 Azure HDInsight는 각각이 특정 기능을 제공하는 구성 요소 모음을 포함하는 5가지 유형의 클러스터를 제공합니다.

| 클러스터 유형 | 기능 |
| ------------ | ----------------------------- |
| Hadoop은 | 쿼리 및 분석(배치 작업) |
| HBase | NoSQL 데이터 저장소 |
| Storm | 실시간 이벤트 처리 |
| Spark(미리 보기) | 메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |
| Spark에서 R 서버 | 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능 |

각 클러스터 유형에는 클러스터 내 자체 노드 수, 클러스터 내 노드 용어 및 각 노드 유형에 대한 기본 VM 크기가 포함됩니다. 다음 표에서는 각 노드 유형에 대한 노드 수는 괄호로 묶어서 표시됩니다.

| 형식| 노드 | 다이어그램|
|-----|------|--------|
|Hadoop은| 헤드 노드(2), 데이터 노드(1+)|![HDInsight Hadoop 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|헤드 서버(2), 지역 서버(1+), 마스터/Zookeeper 노드(3)|![HDInsight HBase 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nimbus 노드(2), 감독자 서버(1+), Zookeeper 노드(3)|![HDInsight Storm 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|헤드 노드(2), 작업자 노드(1+), Zookeeper 노드(3)(A1 Zookeeper VM 크기의 경우 무료)|![HDInsight Spark 클러스터 노드](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|


다음 테이블에서는 HDInsight에 대한 기본 VM 크기를 나열합니다.

|클러스터 유형|	Hadoop은|	HBase|	Storm|	Spark|
|------------|--------|------|-------|-------|
|헤드 – 기본 VM 크기|	D3 |A3|	A3|	D12|
|헤드 – 권장되는 VM 크기|	D3, D4, D12 |A3, A4, A5 |A3, A4, A5|	D12, D13, D14|
|작업자 – 기본 VM 크기|	D3|	D3|	D3|	D12|
|작업자 – 권장되는 VM 크기 |	D3, D4, D12|	D3, D4, D12 |D3, D4, D12|	D12, D13, D14|
|Zookeeper – 기본 VM 크기| |	A2|	A2 | |
|Zookeeper – 권장되는 VM 크기 | |A2, A3, A4 |A2, A3, A4 | |

헤드는 Storm 클러스터 유형에 대한 *Nimbus*로 알려져 있습니다. 작업자는 HBase 클러스터 유형에 대한 *영역* 및 Storm 클러스터 유형에 대한 *감독자*로 알려져 있습니다.



> [AZURE.IMPORTANT] 클러스터를 생성할 때 또는 클러스터를 만든 후 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 코어와 14GB RAM으로 헤드 노드의 크기를 선택해야 합니다.

[스크립트 동작](#customize-clusters-using-script-action)을 사용하여 이러한 기본 유형에 Hue 또는 R과 같은 다른 구성 요소를 추가할 수 있습니다.

## 클러스터 계층

Azure HDInsight는 빅 데이터 클라우드 제품을 Standard 및 [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)의 두 범주로 제공합니다. HDInsight 프리미엄은 R 및 기타 추가 구성 요소를 포함합니다. HDInsight 프리미엄은 HDInsight 버전 3.4에서만 지원됩니다.

다음 표에는 HDInsight 클러스터 유형 및 HDInsight Premium 지원 행렬이 나와 있습니다.

| 클러스터 유형 | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop은 | 예 | 예 |
| Spark | 예 | 예 |
| HBase | 예 | 아니요 |
| Storm | 예 | 아니요 |
| Spark에서 R 서버 | 아니요 | 예 |

이 표는 HDInsight Premium에 추가 클러스터 유형이 포함되면 업데이트될 예정입니다. 다음 스크린 샷에서 클러스터 유형을 선택하기 위한 Azure 포털 정보를 보여줍니다.

![HDInsight 프리미엄 구성](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


## 기본 구성 옵션

다음은 HDInsight 클러스터를 만드는 데 사용되는 기본 구성 옵션입니다.

### 클러스터 이름 ###

클러스터 이름은 클러스터를 식별하는 데 사용됩니다. 클러스터 이름은 전역적으로 고유해야 하고 다음 명명 지침을 준수해야 합니다.

- 이 필드는 3~63자 사이의 문자열이어야 합니다.
- 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

### 클러스터 유형###

[클러스터 유형](#cluster-types) 및 [클러스터 계층](#cluster-tiers)을 참조하세요.

### 운영 체제 ###

다음 두 운영 체제 중 하나에서 HDInsight 클러스터를 만들 수 있습니다.

- Linux용 HDInsight. HDInsight는 Azure에서 Linux 클러스터를 구성하는 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 구성합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.
- Windows에서의 HDInsight(Windows Server 2012 R2 데이터 센터).

### HDInsight 버전###

이는 이 클러스터에 필요한 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [HDInsight의 Hadoop 클러스터 버전 및 구성 요소](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)를 참조하세요.

### 구독 이름###

각 HDInsight 클러스터는 하나의 Azure 구독에 연결됩니다.

### 리소스 그룹 이름 ###

[Azure Resource Manager](../resource-group-overview.md)를 사용하면 Azure 리소스 그룹이라고 하는 그룹인 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

### 자격 증명###

HDInsight 클러스터를 사용하면 클러스터 생성 중에 다음과 같은 두 개의 사용자 계정을 구성할 수 있습니다.

- HTTP 사용자. 기본 사용자 이름은 Azure Portal에 대한 기본 구성을 사용하는 *admin*입니다. 경우에 따라 "클러스터 사용자"라고도 합니다.
- SSH 사용자(Linux 클러스터). SSH를 사용하여 클러스터에 연결하는 데 사용됩니다. [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md) 또는 [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)의 단계에 따라 클러스터를 만든 후 추가 SSH 사용자 계정을 만들 수 있습니다.

    >[AZURE.NOTE] Windows 기반 클러스터의 경우 RDP를 사용하여 클러스터에 연결하는 RDP 사용자를 만들 수 있습니다.

### 데이터 원본###

기존의 Hadoop 분산 파일 시스템(HDFS)은 클러스터에 있는 많은 로컬 디스크를 사용합니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. Azure Blob 저장소는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDFS 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob 저장소에서 구조적 또는 비구조적 데이터에 대해 직접 작동할 수 있습니다. Blob 저장소에 데이터를 저장하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

구성 중에 Azure 저장소 계정과 해당 Azure 저장소 계정의 Azure Blob 저장소 컨테이너를 지정해야 합니다. 일부 생성 프로세스는 Azure Storage 계정 및 Blob 저장소 컨테이너를 미리 만들어 두어야 합니다. Blob 저장소 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 Azure 저장소 계정(연결된 저장소)을 지정할 수 있습니다. 클러스터는 전체 공용 읽기 액세스 또는 Blob 전용 공용 읽기 액세스로 구성된 모든 Blob 저장소 컨테이너에 액세스할 수도 있습니다. 자세한 내용은 [Azure 저장소 리소스에 대한 액세스 관리](../storage/storage-manage-access-to-resources.md)를 참조하세요.

![HDInsight 저장소](./media/hdinsight-provision-clusters/HDInsight.storage.png)

>[AZURE.NOTE] Blob 저장소 컨테이너는 다음 이미지에 나온 것처럼 Blob 집합 그룹화를 제공합니다.

![Azure Blob 저장소](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

기본 Blob 저장소 컨테이너를 비즈니스 데이터 저장에 사용하지 않는 것이 좋습니다. 저장소 비용을 줄이기 위해 사용한 후에는 매번 기본 Blob 저장소 컨테이너를 삭제하는 것이 좋습니다. 기본 컨테이너에는 응용 프로그램 및 시스템 로그가 포함되어 있습니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

>[AZURE.WARNING] 여러 클러스터에 대해 단일 Blob 저장소 컨테이너를 공유하는 것은 지원되지 않습니다.

보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

Azure Blob 저장소 외에, [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)를 HDInsight의 HBase 클러스터에 대한 기본 저장소 계정 및 네 개의 전체 HDInsight 클러스터 형식에 대한 연결된 저장소로 사용할 수도 있습니다. 자세한 내용은 [Azure 포털을 사용하여 Data Lake Store로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

### 위치(영역) ###

HDInsight 클러스터와 해당 기본 저장소 계정은 같은 Azure 위치에 있어야 합니다.

![Azure 지역](./media/hdinsight-provision-clusters/Azure.regions.png)

지원되는 지역 목록은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 클릭하세요.

### 노드 가격 책정 계층###

고객은 클러스터의 수명 기간 동안 해당 노드의 사용량에 대한 대금이 청구됩니다. 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 클러스터의 경우 할당을 취소하거나 보류할 수 없습니다.

클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다. 예를 들어, Hadoop 클러스터 유형은 _헤드 노드_ 2개, 기본 _데이터 노드_ 4개를 포함하는 반면, Storm 클러스터 유형은 _nimbus 노드_ 2개, _zookeeper 노드_ 3개 및 기본 _감독자 노드_ 4개를 포함합니다. HDInsight 클러스터의 비용은 노드 수와 노드에 대한 가상 컴퓨터 크기에 따라 결정됩니다. 예를 들어 많은 메모리가 필요한 작업을 수행할 것으로 예상되는 경우 더 많은 메모리를 포함하는 계산 리소스를 선택할 수 있습니다. 학습 목적인 경우는 데이터 노드를 하나 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.

>[AZURE.NOTE] 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의하세요.

>노드에 대해 사용되는 가상 컴퓨터 이미지는 HDInsight 서비스의 구현 세부 정보이므로 클러스터에서 사용되는 노드는 가상 컴퓨터로 계산되지 않습니다. 노드에서 사용되는 계산 코어는 구독에 사용할 수 있는 계산 코어의 총 수에 계산되지 않습니다. HDInsight 클러스터를 만들 때 노드 가격 책정 계층 블레이드의 요약 섹션에서 클러스터에서 사용할 코어 수와 사용 가능한 코어 수를 확인할 수 있습니다.

클러스터를 구성하기 위해 Azure 포털을 사용하는 경우 노드 크기는 __노드 가격 책정 계층__ 블레이드를 통해 사용할 수 있습니다. 또한 다양한 노드 크기와 관련된 비용도 볼 수 있습니다. 다음 스크린 샷에서는 Linux 기반 Hadoop 클러스터에 대한 선택 항목을 보여 줍니다.

![HDInsight VM 노드 크기](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

다음 표에는 HDInsight 클러스터에서 지원하는 크기와 제공하는 용량이 나와 있습니다.

#### 표준 계층: A 시리즈####

클래식 배포 모델에서는 PowerShell과 CLI 간에 일부 VM 크기가 약간 다릅니다.
* Standard\_A3은 Large
* Standard\_A4는 ExtraLarge

|크기 |CPU 코어|메모리|NIC(최대)|최대 디스크 크기|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_A3\\Large|4|7 GB|2|임시 = 285GB |8|8x500|
|Standard\_A4\\ExtraLarge|8|14 GB|4|임시 = 605GB |16|16x500|
|Standard\_A6|4|28GB|2|임시 = 285GB |8|8x500|
|Standard\_A7|8|56GB|4|임시 = 605GB |16|16x500|


#### 표준 계층: D 시리즈####

|크기 |CPU 코어|메모리|NIC(최대)|최대 디스크 크기|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_D3 |4|14 GB|4|임시(SSD) = 200GB |8|8x500|
|Standard\_D4 |8|28GB|8|임시(SSD) = 400GB |16|16x500|
|Standard\_D12 |4|28GB|4|임시(SSD) = 200GB |8|8x500|
|Standard\_D13 |8|56GB|8|임시(SSD) = 400GB |16|16x500|
|Standard\_D14 |16|112GB|8|임시(SSD) = 800GB |32|32x500|

#### 표준 계층: Dv2 시리즈####

|크기 |CPU 코어|메모리|NIC(최대)|최대 디스크 크기|최대 데이터 디스크(각 1023GB)|최대 IOPS(디스크당 500)|
|---|---|---|---|---|---|---|
|Standard\_D3\_v2 |4|14 GB|4|임시(SSD) = 200GB |8|8x500|
|Standard\_D4\_v2 |8|28GB|8|임시(SSD) = 400GB |16|16x500|
|Standard\_D12\_v2 |4|28GB|4|임시(SSD) = 200GB |8|8x500|
|Standard\_D13\_v2 |8|56GB|8|임시(SSD) = 400GB |16|16x500|
|Standard\_D14\_v2 |16|112GB|8|임시(SSD) = 800GB |32|32x500|    

이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항은 [가상 컴퓨터 크기](../virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요. 다양한 크기의 가격 책정에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight)을 참조하세요.

> [AZURE.IMPORTANT] 클러스터를 생성할 때 또는 클러스터를 만든 후 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 코어와 14GB RAM으로 헤드 노드의 크기를 선택해야 합니다.

클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 가격 책정에 대한 자세한 내용은 [HDInsight 가격 정보](https://azure.microsoft.com/pricing/details/hdinsight/)를 참조하세요.


## 추가 저장소 사용

일부 경우에는 클러스터에 추가 저장소를 추가할 수도 있습니다. 예를 들어 서로 다른 지리적 지역 또는 서비스에 대해 여러 Azure Storage 계정을 가지고 있지만 HDInsight로 모두 분석하려는 경우가 있습니다.

HDInsight 클러스터를 만들 때 또는 클러스터를 만든 후에 저장소 계정을 추가할 수 있습니다. [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

보조 Blob 저장소에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 보조 Data Lake Storage에 대한 자세한 내용은 [Azure Portal을 사용하는 Data Lake Store로 HDInsight 클러스터 만들기](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.


## Hive/Oozie Metastore 사용

HDInsight 클러스터를 삭제한 후 Hive 테이블을 유지하려는 경우 사용자 지정 metastore를 사용하는 것이 좋습니다. 해당 metastore를 다른 HDInsight 클러스터에 연결할 수 있습니다.

> [AZURE.IMPORTANT] HDInsight Metastore는 이전 버전과 호환되지 않습니다. 예를 들어 HDInsight 3.4 클러스터의 Metastore를 사용하여 HDInsight 3.3 클러스터를 만들 수 없습니다.

Metastore는 Hive 테이블, 파티션, 스키마, 열 등 Hive 및 Oozie 메타데이터에 대한 정보를 포함합니다. metastore를 통해 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 만들 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다. 기본적으로 Hive는 포함된 Azure SQL 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다. Hive metastore를 구성하여 HDInsight 클러스터에서 Hive 테이블을 만든 경우 동일한 Hive metastore를 사용하여 클러스터를 다시 만들면 해당 테이블이 유지됩니다.

HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

> [AZURE.IMPORTANT] 사용자 지정 metastore를 만들 때 대시 또는 하이픈을 포함하는 데이터베이스 이름을 사용하지 않습니다. 이렇게 하면 클러스터 만들기 프로세스가 실패할 수 있습니다.

## Azure 가상 네트워크 사용

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)를 사용하여, 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결(사이트 간 또는 지점 및 사이트 간)

| 사이트 간 구성 | 지점 및 사이트 간 구성 |
| -------------------------- | --------------------------- |
| 사이트 간 구성을 통해 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.<br />![사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | 지점 및 사이트 간 구성을 통해 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.<br />![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Windows 기반 클러스터에는 v1(클래식) 가상 네트워크가 필요하고, Linux 기반 클러스터에는 v2(Azure Resource Manager) 가상 네트워크가 필요합니다. 올바른 유형의 네트워크가 없으면, 클러스터를 만들어도 사용할 수 없습니다.

가상 네트워크에 대한 특정 구성 요구 사항을 비롯한 가상 네트워크로 HDInsight를 사용에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

## HDInsight 클러스터 사용자 지정을 사용하여 클러스터 사용자 지정(bootstrap)

경우에 따라 다음과 같은 구성 파일을 구성해야 할 수 있습니다.

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

클러스터의 수명 동안 변경 내용을 유지하려면 생성 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용하거나 Linux 기반 클러스터에서 Ambari를 사용할 수 있습니다. 자세한 내용은 [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.

>[AZURE.NOTE] Windows 기반 클러스터는 이미지로 다시 설치하므로 변경 내용을 유지할 수 없습니다. 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하십시오. 클러스터의 수명 동안 변경 내용을 유지하려면 생성 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용해야 합니다.

## 스크립트 작업을 사용하여 클러스터 사용자 지정

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 작업**을 통해 호출됩니다. 스크립트 작업은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR(Java Archive) 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Blob 저장소에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)을 참조하세요.

>[AZURE.NOTE] HDInsight 클러스터에 Jar 파일을 배포하거나 HDInsight 클러스터에서 Jar 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원 센터](https://azure.microsoft.com/support/options/)로 문의하세요.

> Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)을 참조하세요.

## 에지 노드 사용

 빈 에지 노드는 헤드 노드에 설치되고 구성된 것과 동일한 클라이언트 도구를 사용하는 Linux 가상 컴퓨터입니다. 에지 노드를 사용하면 클러스터에 액세스하고, 클라이언트 응용 프로그램을 테스트하며, 클라이언트 응용 프로그램을 호스트할 수 있습니다. 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.
 
## 클러스터 생성 방법

이 문서에서는 Linux 기반 HDInsight 클러스터 만들기에 대한 기본 정보에 대해 알아봤습니다. 다음 표를 사용하여 사용자 요구에 적합한 방법으로 클러스터를 만드는 방법에 대한 구체적인 정보를 확인합니다.

| 다음을 사용하여 만든 클러스터 | 웹 브라우저 사용 | 명령줄 | REST API | SDK) | Linux, Mac OS X 또는 Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure 포털](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure 데이터 팩터리](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔ | ✔ | ✔ |✔ | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Azure 리소스 관리자 템플릿](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0914_2016-->