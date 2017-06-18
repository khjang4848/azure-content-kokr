<properties
	pageTitle="HDInsight의 Hadoop 클러스터 가용성 | Microsoft Azure"
	description="HDInsight는 추가 헤드 노드와 함께 항상 사용 가능하고 안정적인 클러스터를 배포합니다."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


#HDInsight에서 Windows 기반 Hadoop 클러스터의 가용성 및 안정성


>[AZURE.NOTE] 이 문서에서 사용한 단계는 Windows 기반 HDInsight 클러스터를 대상으로 합니다. Linux 기반 클러스터를 사용하는 경우 Linux 관련 정보는 [HDInsight에서 Linux 기반 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)을 참조하세요.

HDInsight를 사용하면 고객이 서로 다른 데이터 분석 작업을 위한 다양한 클러스터 형식을 배포할 수 있습니다. 현재 제공되는 클러스터 유형은 쿼리 및 분석 작업을 위한 Hadoop 클러스터, NoSQL 작업을 위한 HBase 클러스터 및 실시간 이벤트 처리 작업을 위한 Storm 클러스터입니다. 지정된 클러스터 유형 내에서는 다양한 노드에 대해 여러 역할이 존재합니다. 예:



- HDInsight용 Hadoop 클러스터는 두 가지 역할로 배포됩니다.
	- 헤드 노드(노드 2개)
	- 데이터 노드(노드 1개 이상)

- HDInsight용 HBase 클러스터는 세 가지 역할로 배포됩니다.
	- 헤드 서버(노드 2개)
	- 지역 서버(노드 1개 이상)
	- 마스터/Zookeeper 노드(노드 3개)

- HDInsight용 Storm 클러스터는 세 역할과 함께 배포됩니다.
	- Nimbus 노드(노드 2개)
	- 감독자 서버(노드 1개 이상)
	- Zookeeper 노드(노드 3개)

일반적으로 표준 방식으로 구현된 Hadoop 클러스터에는 헤드 노드가 1개뿐입니다. HDInsight는 보조 헤드 노드/헤드 서버/Nimbus 노드를 추가하여 단일 실패 지점을 제거하며 작업 관리에 필요한 서비스의 가용성 및 안정성을 향상합니다. 이러한 헤드 노드/헤드 서버/Nimbus 노드는 작업자 노드의 실패를 원활하게 관리하도록 디자인되었지만 헤드 노드에서 실행되는 마스터 서비스가 작동 중단되면 클러스터 작동이 중단될 수 있습니다.


ZK([ZooKeeper](http://zookeeper.apache.org/)) 노드가 추가되었으며 헤드 노드의 리더 선택을 위해, 그리고 활성 헤드 노드(헤드 노드 0)가 비활성화될 때 보조 헤드 노드(헤드 노드 1)로 장애 조치(failover)될 때 작업자 노드 및 GW(게이트웨이)에서 알 수 있도록 하는 데 사용됩니다.

![HDInsight Hadoop 구현의 매우 안정적인 헤드 노드의 다이어그램](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## 활성 헤드 노드의 서비스 상태 확인
활성 상태인 헤드 노드를 확인하고 해당 헤드 노드에서 실행되는 서비스의 상태를 확인하기 위해서는 RDP(원격 데스크톱 프로토콜)를 사용하여 Hadoop 클러스터에 연결해야 합니다. RDP 지침의 경우 [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)를 참조하세요. 클러스터에 원격으로 연결한 경우 바탕 화면에 있는 **Hadoop 서비스 사용 가능** 아이콘을 두 번 클릭하여 Namenode, Jobtracker, Templeton, Oozieservice, Metastore 및 Hiveserver2 서비스에서 실행 중인 헤드 노드에 대한 상태를 표시하고, HDI 3.0의 경우에는 Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore 및 Hiveserver2 서비스에서 실행 중인 헤드 노드에 대한 상태를 표시합니다.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

스크린샷에서는 활성 헤드 노드가 *headnode0*입니다.

## 보조 헤드 노드의 로그 파일 액세스

보조 헤드 노드가 활성 헤드 노드가 될 경우 이 노드의 작업 로그에 액세스하려는 경우 기본 활성 노드에서와 같이 JobTracker UI를 사용하면 됩니다. JobTracker에 액세스하려면 이전 섹션에 설명된 것처럼 RDP를 사용하여 Hadoop 클러스터에 연결해야 합니다. 클러스터에 원격으로 연결한 경우 바탕 화면에 있는 **Hadoop 이름 노드 상태** 아이콘을 두 번 클릭한 후 **NameNode 로그**를 클릭하여 보조 헤드 노드의 로그 디렉터리로 이동합니다.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## 헤드 노드 크기 구성
헤드 노드는 기본적으로 대형 VM(가상 컴퓨터)으로 할당됩니다. 이 크기는 클러스터에서 실행되는 대부분의 Hadoop 작업의 관리에 적합합니다. 그렇지만 초대형 VM이 헤드 노드에 필요할 수 있는 시나리오가 있습니다. 한 가지 예로 클러스터가 많은 수의 소형 Oozie 작업을 관리해야 하는 경우를 들 수 있습니다.

초대형 VM은 Azure PowerShell cmdlet 또는 HDInsight SDK를 사용하여 구성할 수 있습니다.

Azure PowerShell을 사용하는 클러스터의 생성 및 프로비전은 [PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)에 설명되어 있습니다. 초대형 헤드 노드를 구성하려면 `-HeadNodeVMSize ExtraLarge` 매개 변수를 이 코드에 사용되는 `New-AzureRmHDInsightcluster` cmdlet에 추가해야 합니다.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
				-ResourceGroupName $resourceGroupName `
				-ClusterName $clusterName ` 
				-Location $location `
				-HeadNodeVMSize ExtraLarge `
				-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				-DefaultStorageAccountKey $storageAccountKey `
				-DefaultStorageContainerName $containerName  `
				-ClusterSizeInNodes $clusterNodes

SDK의 경우에도 상황은 비슷합니다. SDK를 사용하는 클러스터의 생성 및 프로비전은 [HDInsight .NET SD 사용](hdinsight-provision-clusters.md#sdk)에 설명되어 있습니다. 초대형 헤드 노드를 구성하려면 `HeadNodeSize = NodeVMSize.ExtraLarge` 매개 변수를 이 코드에 사용되는 `ClusterCreateParameters()` 메서드에 추가해야 합니다.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
		Name = clustername,
		Location = location,
		HeadNodeSize = NodeVMSize.ExtraLarge,
		DefaultStorageAccountName = storageaccountname,
		DefaultStorageAccountKey = storageaccountkey,
		DefaultStorageContainer = containername,
		UserName = username,
		Password = password,
		ClusterSizeInNodes = clustersize
    };


## 다음 단계

- [Apache ZooKeeper](http://zookeeper.apache.org/)
- [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)
- [HDInsight .NET SDK 사용](hdinsight-provision-clusters.md#sdk)

<!---HONumber=AcomDC_0914_2016-->