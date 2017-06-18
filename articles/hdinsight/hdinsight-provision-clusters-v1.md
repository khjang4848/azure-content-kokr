<properties 
   pageTitle="HDInsight에서 Hadoop 클러스터 사용자 지정 프로비전 | Microsoft Azure" 
   description="Azure 클래식 포털, Azure PowerShell, 명령줄 또는 .NET SDK를 사용하여 Azure HDInsight용 클러스터를 사용자 지정 프로비전하는 방법에 대해 알아봅니다." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

#HDInsight에서 Hadoop 클러스터 프로비전

HDInsight 클러스터 프로비전에 대한 계획을 세우는 방법에 대해 알아봅니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 Azure 클래식 포털을 사용합니다. 새 서비스를 만들 때 클래식 포털을 사용하지 않는 것이 좋습니다. Azure 포털의 장점에 대한 자세한 내용은 [Microsoft Azure 포털](https://azure.microsoft.com/features/azure-portal/)을 참조하세요.
>
> 또한 이 문서는 Azure PowerShell, Azure CLI 및 HDInsight용 .NET SDK를 사용하는 방법에 관한 정보도 포함하고 있습니다. 제공된 코드 조각은 ASM(Azure 서비스 관리)을 사용하여 HDInsight와 작동하고 현재 __사용되지 않는__ 명령을 기반으로 작성되었습니다. 이러한 명령은 2017년 1월 1일에 제거됩니다.
>
>ARM(Azure Resource Manager)을 사용하는 PowerShell, Azure CLI 및 HDInsight용 .NET SDK 코드 조각과 함께 Azure 포털을 사용하는 이 문서의 버전에 대해서는 [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

**필수 조건:**

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.


## 기본 구성 옵션


- **클러스터 이름**

	클러스터 이름은 클러스터를 식별하는 데 사용됩니다. 클러스터 이름은 다음 지침을 따라야 합니다.
	
	- 이 필드는 3~63자 사이의 문자열이어야 합니다.
	- 이 필드에는 문자, 숫자 및 하이픈만 포함할 수 있습니다.

- **구독 이름**

	하나의 HDInsight 클러스터는 하나의 Azure 구독에 연결됩니다.
 
- **운영 체제**

	다음 두 운영 체제 중 하나에서 HDInsight 클러스터를 프로비전할 수 있습니다.
	- **Windows에서의 HDInsight(Windows Server 2012 R2 데이터 센터)**:
	- **Linux에서의 HDInsight**: HDInsight는 Azure에서 Linux 클러스터를 구성하는 옵션을 제공합니다. Linux 기반으로 작성된 Hadoop 에코 시스템 구성 요소와 쉽게 통합 또는 Linux 또는 Unix를 사용하고 기존 Hadoop Linux 기반 솔루션에서 마이그레이션에 익숙한 경우에 Linux 클러스터를 구성합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.


- **HDInsight 버전**

	이 클러스터에서 사용할 HDInsight 버전을 결정하는 데 사용됩니다. 자세한 내용은 [HDInsight의 Hadoop 클러스터 버전 및 구성 요소](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)를 참조하세요.

- **클러스터 형식** 및 **클러스터 크기(데이터 노드라고도 함)**

	HDInsight를 사용하면 고객이 서로 다른 데이터 분석 작업을 위한 다양한 클러스터 형식을 배포할 수 있습니다. 현재 제공되는 클러스터 형식은 다음과 같습니다.
	
	- Hadoop 클러스터: 쿼리 및 분석 작업용
	- HBase 클러스터: NoSQL 작업용
	- Storm 클러스터: 실시간 이벤트 처리 작업용
	- Spark 클러스터: 메모리 내 처리, 대화형 쿼리, 스트림 및 기계 학습 워크로드용

	![HDInsight 클러스터](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE] *Azure HDInsight 클러스터*는 *HDInsight의 Hadoop 클러스터* 또는 *HDInsight 클러스터*라고도 부릅니다. 경우에 따라 *Hadoop 클러스터*로도 사용됩니다. 이들은 모두 Microsoft Azure 환경에서 호스트되는 Hadoop 클러스터를 말합니다.

	지정된 클러스터 형식 내의 다양한 노드에 대해 다양한 역할이 있으므로 고객이 지정된 역할에서 작업 세부 정보에 적절하게 노드 크기를 조정할 수 있습니다. 예를 들어, Hadoop 클러스터에는 수행되는 분석 유형이 메모리 집약적인 경우 대량 메모리로 프로비전된 작업자 노드가 있을 수 있습니다.

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	HDInsight용 Hadoop 클러스터는 두 가지 역할로 배포됩니다.

	- 헤드 노드(노드 2개)
	- 데이터 노드(노드 1개 이상)

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	HDInsight용 HBase 클러스터는 세 가지 역할로 배포됩니다.
	- 헤드 서버(노드 2개)
	- 지역 서버(노드 1개 이상)
	- 마스터/Zookeeper 노드(노드 3개)
	
	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	HDInsight용 Storm 클러스터는 세 역할과 함께 배포됩니다.
	- Nimbus 노드(노드 2개)
	- 감독자 서버(노드 1개 이상)
	- Zookeeper 노드(노드 3개)
	

	![HDInsight Hadoop 클러스터 역할](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	HDInsight용 Spark 클러스터는 다음 세 가지 역할로 배포됩니다.
	- 헤드 노드(노드 2개)
	- 작업자 노드(노드 1개 이상)
	- Zookeeper 모드(노드 3개)(A1 Zookeeper: 무료)

	고객은 클러스터의 수명 기간 동안 해당 노드의 사용량에 대한 대금이 청구됩니다. 대금 청구는 클러스터를 만들면 시작되고 클러스터가 삭제되면 중지됩니다(클러스터를 할당 해제하거나 보류할 수 없음). 클러스터 크기는 클러스터 가격에 영향을 줍니다. 학습 목적인 경우는 데이터 노드를 하나 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.


	>[AZURE.NOTE] 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의하세요.
	
- **지역/가상 네트워크(위치라고도 함)**

	![Azure 지역](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	지원되는 지역 목록은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 클릭하세요.

- **노드 크기**

	![HDInsight VM 노드 크기](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	노드의 VM 크기를 선택합니다. 자세한 내용은 [클라우드 서비스에 적합한 크기](cloud-services-sizes-specs.md)를 참조하세요.

	선택한 VM에 따라 비용이 달라질 수 있습니다. HDInsight에서는 클러스터 노드에 모든 표준 계층 VM을 사용합니다. VM 크기가 가격에 미치는 영향에 대한 자세한 내용은 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 가격</a>을 참조하세요.


- **HDInsight 사용자**

	HDInsight 클러스터를 사용하면 프로비전 중에 두 개의 사용자 계정을 구성할 수 있습니다.

	- HTTP 사용자. 기본 사용자 이름은 Azure 클래식 포털에서 기본 구성을 사용하는 admin입니다.
	- RDP 사용자(Windows 클러스터): RDP를 사용하여 클러스터에 연결하는 데 사용됩니다. 계정을 만들 때 만료 날짜는 오늘부터 90일 이내로 설정해야 합니다.
	- SSH 사용자(Linux 클러스터): SSH를 사용하여 클러스터에 연결하는 데 사용됩니다. [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)의 단계에 따라 클러스터를 만든 후 추가 SSH 사용자 계정을 만들 수 있습니다.
  
 

- **Azure 저장소 계정**


	원래 HDFS는 클러스터에 있는 많은 로컬 디스크를 사용합니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 대신 사용합니다. Azure Blob 저장소는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob 저장소에서 구조적 또는 비구조적 데이터에 대해 직접 작동할 수 있습니다. Blob 저장소에 데이터를 저장하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.
	
	구성 중에 Azure 저장소 계정과 해당 Azure 저장소 계정의 Azure Blob 저장소 컨테이너를 지정해야 합니다. 일부 프로비전 프로세스는 Azure 저장소 계정 및 Blob 저장소 컨테이너를 미리 만들어 두어야 합니다. Blob 저장소 컨테이너는 클러스터에서 기본 저장소 위치로 사용됩니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 Azure 저장소 계정(연결된 저장소)을 지정할 수 있습니다. 또한 클러스터는 전체 공용 읽기 액세스 또는 Blob 전용 공용 읽기 액세스로 구성된 모든 Blob 컨테이너에 액세스할 수도 있습니다. 액세스 제한 자세한 내용은 [Azure 저장소 리소스에 대한 액세스 관리](storage-manage-access-to-resources.md)를 참조하세요.

	![HDInsight 저장소](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE] Blob 저장소 컨테이너는 이미지에 나온 것처럼 Blob 집합 그룹화를 제공합니다.
	
	![Azure Blob 저장소](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING] 여러 클러스터에서 하나의 Blob 저장소 컨테이너를 공유하지 마세요. 이 기능은 지원되지 않습니다.
	
	보조 Blob 저장소 사용에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

- **Hive/Oozie Metastore**

	Metastore는 Hive 테이블, 파티션, 스키마, 열 등 Hive 및 Oozie 메타데이터에 대한 정보를 포함합니다. Metastore를 사용하면 Hive 및 Oozie 메타데이터를 보존할 수 있으므로 새 클러스터를 프로비전할 때 Hive 테이블 또는 Oozie 작업을 다시 만들 필요가 없습니다. 기본적으로 Hive는 포함된 Azure SQL 데이터베이스를 사용하여 이 정보를 저장합니다. 포함된 데이터베이스에서는 클러스터가 삭제된 경우 메타데이터를 유지할 수 없습니다. 예를 들어, Hive Metastore로 프로비전된 클러스터가 있고 몇 개의 Hive 테이블을 만들었습니다. 클러스터를 삭제하고 동일한 Hive Metastore를 사용하여 클러스터를 다시 만든 후, 원래 클러스터에서 만든 Hive 테이블을 볼 수 있습니다.

## 고급 구성 옵션

>[AZURE.NOTE] 이 섹션은 현재 Windows 기본 HDInsight 클러스터에만 적용됩니다.

### HDInsight 클러스터 사용자 지정을 사용하여 클러스터 사용자 지정

경우에 따라 구성 파일을 구성해야 할 수 있습니다. 다음은 그 중 몇 가지 파일입니다.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

클러스터는 이미지로 다시 설치하므로 변경 내용을 유지할 수 없습니다. 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하십시오. 클러스터의 수명 동안 변경 내용을 유지하려면 프로비전 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용할 수 있습니다.

다음은 Hive 구성을 사용자 지정하는 Azure PowerShell 스크립트 예제입니다.

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

다른 구성 파일을 사용자 지정하는 추가 샘플:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
자세한 내용은 Azim Uddin의 [HDInsight 클러스터 프로비전 사용자 지정](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) 블로그를 참조하세요.




### 스크립트 작업을 사용하여 클러스터 사용자 지정

프로비전하는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 동작**을 통해 호출됩니다. 스크립트 동작은 Azure 클래식 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.


### Azure 가상 네트워크 사용

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결합니다(사이트 간 또는 지점 및 사이트 간).

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

## 프로비전 도구

- Azure 클래식 포털
- Azure PowerShell
- .NET SDK
- CLI

### Azure 클래식 포털 사용

[기본 구성 옵션] 및 필드 설명을 위한 [고급 구성 옵션]을 참조할 수 있습니다.

**사용자 지정 만들기 옵션을 사용하여 HDInsight 클러스터를 만들려면**

1. [Azure 클래식 포털][azure-management-portal]에 로그인합니다.
2. 페이지 아래에서 **+ 새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**, **사용자 지정 만들기**를 차례로 클릭합니다.
3. **클러스터 세부 정보** 페이지에서 다음 값을 입력하거나 선택합니다.

	- 클러스터 이름
	- 구독 이름
	- 클러스터 유형
	- 운영 체제
	- HDInsight 버전

4. **클러스터 구성** 페이지에서 다음 값을 입력하거나 선택합니다.

	- 데이터 노드
	- 지역/가상 네트워크
	- 헤드 노드 크기
	- 데이터 노드 크기

5. **클러스터 사용자 구성** 페이지에서 다음 값을 제공합니다.

	- HTTP 사용자 이름
	- HTTP 암호/암호 확인
	- 클러스터에 대한 원격 데스크톱 활성화
	- Hive/Oozie Metastore 입력

6. 이전 화면에서 Hive/Oozie Metastore를 입력하도록 선택한 경우 **Hive/Oozie Metastore 구성** 페이지에서 다음 값을 제공합니다.

	- Hive Metastore 데이터베이스
	- 데이터베이스 사용자
	- 데이터베이스 사용자 암호
	- Oozie Metastore 데이터베이스
	- 데이터베이스 사용자
	- 데이터베이스 사용자 암호

7. **저장소 계정** 페이지에서 다음 값을 제공합니다.

	- 저장소 계정
	- 계정 이름
	- 기본 컨테이너
	- 추가 저장소 계정

8. **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 클러스터가 생성될 때 클러스터를 사용자 지정하기 위해 실행할 사용자 지정 스크립트에 대한 세부 정보를 제공합니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

	- 이름: 스크립트 작업의 이름을 지정합니다.
	- 스크립트 URI: 클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다.
	- 노드 유형: 사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>데이터 노드만</b>을 선택할 수 있습니다.
	- 매개 변수: 스크립트에 필요한 경우 매개 변수를 지정합니다.</td></tr>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

### Azure PowerShell 사용
Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 섹션에서는 Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. HDInsight Windows PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요. HDInsight에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)를 참조하세요. HDInsight Windows PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조](https://msdn.microsoft.com/library/azure/dn858087.aspx)를 참조하세요.

> [AZURE.NOTE] 이 섹션의 스크립트는 Azure 가상 네트워크에서 HDInsight 클러스터를 구성하는 데 사용할 수는 있지만 Azure 가상 네트워크를 만들지는 않습니다. Azure 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 구성 작업](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)을 참조하세요.

Azure PowerShell을 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

- Azure 저장소 계정 만들기
- Azure Blob 컨테이너 만들기
- HDInsight 클러스터 만들기

Windows PowerShell 콘솔 또는 Windows PowerShell ISE(통합 스크립팅 환경)를 사용하여 스크립트를 실행할 수 있습니다.

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정 및 저장소 컨테이너가 필요합니다. 저장소 계정은 HDInsight 클러스터와 동일한 데이터 센터에 있어야 합니다.

**Azure 계정에 연결하려면**

	Add-AzureAccount

Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

**Azure 저장소 계정을 만들려면**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 Windows PowerShell 명령을 사용하여 정보를 검색할 수 있습니다.

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Azure Blob 저장소 컨테이너를 만들려면**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

**HDInsight 클러스터를 프로비전하려면**

> [AZURE.NOTE] HDInsight 클러스터에서 구성 변수를 변경할 때는 Azure PowerShell cmdlet만 사용하는 것이 좋습니다. 원격 데스크톱을 통해 클러스터에 연결된 상태로 Hadoop 구성 파일을 변경하면 클러스터 패치 시 해당 변경 내용을 덮어쓸 수 있습니다. 반면 Azure PowerShell을 통해 설정된 구성 값은 클러스터를 패치해도 보존됩니다.

- Azure PowerShell 콘솔 창에서 다음 명령을 실행합니다.

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE] $hadoopUserName 및 $hadoopUserPassword 명령은 클러스터에 대한 Hadoop 사용자 계정을 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에 연결하고 작업을 실행합니다. Azure 클래식 포털에서 빠른 생성 옵션을 사용하여 클러스터를 프로비전하는 경우 기본 Hadoop 사용자 이름은 "admin"입니다. 이 계정을 RDP(원격 데스크톱 프로토콜) 사용자 계정과 혼동하지 마세요. RDP 사용자 계정은 Hadoop 사용자 계정과 달라야 합니다. 자세한 내용은 [Azure 클래식 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리][hdinsight-admin-portal]를 참조하세요.

	클러스터 프로비전을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**사용자 지정 구성 옵션을 사용하여 HDInsight 클러스터를 프로비전하려면**

클러스터를 프로비전할 때 둘 이상의 Azure Blob 저장소 컨테이너에 연결하거나 가상 네트워크를 사용하거나 Hive 및 Oozie 메타 저장소에 대해 Azure SQL 데이터베이스를 사용하는 것과 같은 기타 구성 옵션을 사용할 수 있습니다. 이 기능을 사용하면 데이터 및 메타데이터의 수명을 클러스터의 수명과 분리할 수 있습니다.

> [AZURE.NOTE] HDInsight 클러스터에서 구성 변수를 변경할 때는 Windows PowerShell cmdlet만 사용하는 것이 좋습니다. 원격 데스크톱을 통해 클러스터에 연결된 상태로 Hadoop 구성 파일을 변경하면 클러스터 패치 시 해당 변경 내용을 덮어쓸 수 있습니다. 반면 Azure PowerShell을 통해 설정된 구성 값은 클러스터를 패치해도 보존됩니다.

- Windows PowerShell 창에서 다음 명령을 실행합니다.

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

**HDInsight 클러스터를 나열하려면**

- Azure PowerShell 콘솔 창에서 다음 명령을 실행하여 HDInsight 클러스터를 나열한 후 해당 클러스터가 성공적으로 프로비전되었는지 확인합니다.

		Get-AzureHDInsightCluster -Name <ClusterName>


### Azure CLI 사용

> [AZURE.NOTE] 2014년 8월 29일부터 Azure CLI를 사용하여 클러스터를 Azure 가상 네트워크와 연결할 수 없습니다.

HDInsight 클러스터를 프로비전하는 다른 옵션은 Azure CLI입니다. Azure CLI는 Node.js로 구현됩니다. Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다.

Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI](../xplat-cli-install.md)를 참조하세요.

아래 지침에서는 Linux 및 Windows에서 Azure CLI를 설치하는 방법 및 명령줄을 사용하여 클러스터를 프로비전하는 방법을 안내합니다.

- [Linux용 Azure CLI 설치](#clilin)
- [Windows용 Azure CLI 설치](#cliwin)
- [Azure CLI를 사용하여 HDInsight 클러스터 프로비전](#cliprovision)

#### <a id="clilin"></a>Linux용 Azure CLI 설치

Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Linux 컴퓨터를 설정하려면 다음 절차를 수행합니다.

- NPM(Node.js 패키지 관리자)을 사용하여 Azure CLI 설치
- Azure 구독에 연결

**NPM을 사용하여 Azure CLI를 설치하려면**

1.	Linux 컴퓨터에서 터미널 창을 열고 다음 명령을 실행합니다.

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	다음 명령을 실행하여 설치를 확인합니다.

		azure hdinsight -h

	여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다. 예:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Azure 구독에 연결하려면**

Azure CLI를 사용하기 전에, 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 Azure CLI에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 게시 설정 파일에서 구할 수 있습니다. 그런 다음 게시 설정 파일은 Azure CLI가 후속 작업에 사용할 수 있는 영구적 로컬 구성 설정으로 가져올 수 있습니다. 게시 설정을 한 번만 가져오면 됩니다.

> [AZURE.NOTE] 게시 설정 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker 드라이브 암호화를 사용합니다.


1.	터미널 창을 엽니다.
2.	다음 명령을 실행하여 Azure 구독에 로그인합니다.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	이 명령은 게시 설정 파일을 다운로드할 웹 페이지를 시작합니다. 웹 페이지가 열리지 않으면 브라우저 페이지를 열고 터미널 창에서 해당 링크를 클릭한 다음 포털에 로그인합니다.

3.	컴퓨터에 게시 설정 파일을 다운로드합니다.
5.	명령 프롬프트 창에서 다음 명령을 실행하여 게시 설정 파일을 가져옵니다.

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Windows용 Azure CLI 설치

Azure CLI를 사용하도록 Windows 기반 컴퓨터를 설정하려면 다음 절차를 수행합니다.

- NPM 또는 Windows Installer를 사용하여 Azure CLI 설치
- Azure 계정 게시 설정 다운로드 및 가져오기


NPM 또는 Windows Installer를 사용하여 Azure CLI를 설치할 수 있습니다. 두 옵션 중 하나만 사용하여 설치하는 것이 좋습니다.

**NPM을 사용하여 Azure CLI를 설치하려면**

1.	**www.nodejs.org**로 이동합니다.
2.	**INSTALL**을 클릭하고 기본 설정을 사용하여 지침을 따릅니다.
3.	워크스테이션에서 **명령 프롬프트**(또는 *Azure 명령 프롬프트* 또는 *VS2012용 개발자 명령 프롬프트*)를 엽니다.
4.	명령 프롬프트 창에서 다음 명령을 실행합니다.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE] NPM 명령을 찾을 수 없다는 오류가 표시되는 경우 다음 경로가 PATH 환경 변수에 있는지 확인합니다. <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> 또는 <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>

5.	다음 명령을 실행하여 설치를 확인합니다.

		azure hdinsight -h

	여러 수준에서 *-h* 스위치를 사용하여 도움말 정보를 표시할 수 있습니다. 예:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows Installer를 사용하여 Azure CLI를 설치하려면**

1.	**http://azure.microsoft.com/downloads/**로 이동합니다.
2.	**명령줄 도구** 섹션으로 스크롤하여 **Azure 명령줄 인터페이스**를 클릭하고 웹 플랫폼 설치 관리자 마법사를 따릅니다.

**게시 설정을 다운로드하거나 가져오려면**

Azure CLI를 사용하기 전에, 워크스테이션과 Azure 사이의 연결을 구성해야 합니다. Azure 구독 정보는 Azure CLI에서 계정에 연결하는 데 사용됩니다. 이 정보는 Azure의 게시 설정 파일에서 구할 수 있습니다. 그런 다음 게시 설정 파일은 Azure CLI가 후속 작업에 사용할 수 있는 영구적 로컬 구성 설정으로 가져올 수 있습니다. 게시 설정을 한 번만 가져오면 됩니다.

> [AZURE.NOTE] 게시 설정 파일에는 중요 정보가 포함되어 있습니다. 파일을 삭제하거나 파일이 포함된 사용자 폴더를 암호화하는 추가 단계를 수행하는 것이 좋습니다. Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다.


1.	**명령 프롬프트**를 엽니다.
2.	다음 명령을 실행하여 게시 설정 파일을 다운로드합니다.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	이 명령은 게시 설정 파일을 다운로드할 웹 페이지를 시작합니다.

3.	파일을 저장하라는 메시지가 표시되면 **저장**을 클릭하고 파일을 저장해야 하는 위치를 제공합니다.
5.	명령 프롬프트 창에서 다음 명령을 실행하여 게시 설정 파일을 가져옵니다.

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Azure CLI를 사용하여 HDInsight 클러스터 프로비전

Azure CLI를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차가 필요합니다.

- Azure 저장소 계정 만들기
- 클러스터 프로비전

**Azure 저장소 계정을 만들려면**

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정이 필요합니다. 저장소 계정은 동일한 데이터 센터에 위치해야 합니다.

- 명령 프롬프트 창에서 다음 명령을 실행하여 Azure 저장소 계정을 만듭니다.

		azure storage account create [options] <StorageAccountName>

	위치를 묻는 메시지가 표시되면 HDINsight 클러스터를 프로비전할 수 있는 위치를 선택합니다. 저장소는 HDInsight 클러스터와 동일한 위치에 있어야 합니다. 현재 **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 북중부**, **미국 남중부** 지역에서만 HDInsight 클러스터를 호스트할 수 있습니다.

Azure 클래식 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](../storage/storage-create-storage-account.md)를 참조하세요.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Azure 클래식 포털을 사용하여 정보를 얻는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](../storage/storage-create-storage-account.md)의 *방법: 저장소 액세스 키 보기, 복사 및 다시 생성* 섹션을 참조하세요.

또한 HDInsight 클러스터를 사용하려면 저장소 계정 내에 컨테이너가 있어야 합니다. 제공한 저장소 계정에 아직 컨테이너가 없을 때 *azure hdinsight cluster create* 명령을 실행하면 컨테이너 이름을 묻는 메시지가 표시되고 해당 이름으로 컨테이너가 만들어집니다. 그렇지만 미리 컨테이너를 만들려면 다음 명령을 사용할 수 있습니다.

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

**HDInsight 클러스터를 프로비전하려면**

- 명령 프롬프트 창에서 다음 명령을 실행합니다.

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**구성 파일을 사용하여 HDInsight 클러스터를 프로비전하려면**

일반적으로 HDInsight 클러스터를 프로비전하고, 해당 작업을 실행한 후에 비용을 줄이기 위해 클러스터를 삭제합니다. Azure CLI에서는 클러스터를 프로비전할 때마다 다시 사용할 수 있도록 구성을 파일에 저장하는 옵션이 있습니다.

- 명령 프롬프트 창에서 다음 명령을 실행합니다.


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**클러스터 세부 정보를 나열하고 표시하려면**

- 클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**클러스터를 삭제하려면**

- 클러스터를 삭제하려면 다음 명령을 사용합니다.

		azure hdinsight cluster delete <ClusterName>



### HDInsight .NET SDK 사용
HDInsight .NET SDK는 .NET Framework 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.

SDK를 사용하여 HDInsight 클러스터를 프로비전하려면 다음 절차를 수행해야 합니다.

- HDInsight .NET SDK 설치
- 자체 서명된 인증서 만들기
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK를 설치하려면**

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서를 만들려면**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


**Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio 2013 또는 2015 열기

2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

	|속성|값|
	|--------|-----|
	|Template|템플릿/시각화 개체 C#/Windows/콘솔 응용 프로그램|
	|이름|CreateHDICluster|

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.Resources -Pre

	.NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.
8. 코드를 다음으로 바꿉니다.

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
                private const string NewClusterType = "Hadoop";
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		            
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. 클래스 멤버 값을 대체합니다.

**응용 프로그램을 실행하려면**

Visual Studio에 응용 프로그램이 열려 있을 때 **F5**를 눌러 응용 프로그램을 실행합니다. 콘솔 창이 열리고 응용 프로그램의 상태가 표시되며 HDInsight 클러스터를 만들려면 몇 분 정도 걸릴 수 있습니다.


##<a id="nextsteps"></a> 다음 단계
이 문서에서는 HDInsight 클러스터를 프로비전하는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight 클러스터를 시작하는 방법을 알아봅니다.
* [HDInsight에서 Sqoop 사용](hdinsight-use-sqoop.md) - HDInsight와 SQL 데이터베이스 또는 SQL Server 간에 데이터를 복사하는 방법을 알아봅니다.
* [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md) - Azure PowerShell을 사용하여 HDInsight 작업을 하는 방법을 알아봅니다.
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md) - 프로그래밍 방식으로 작업을 HDInsight에 제출하는 방법을 알아봅니다.
* [Azure HDInsight SDK 문서][hdinsight-sdk-documentation] - HDInsight SDK를 살펴봅니다.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0914_2016-->