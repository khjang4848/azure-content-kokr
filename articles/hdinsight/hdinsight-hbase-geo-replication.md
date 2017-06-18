<properties 
   pageTitle="두 데이터 센터 간에 HBase 복제 구성 | Microsoft Azure" 
   description="두 데이터 센터 HBase 복제를 구성하는 방법 및 클러스터 복제에 대한 사용 사례에 대해 알아봅니다." 
   services="hdinsight,virtual-network" 
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

# HDInsight에서 HBase 지역에서 복제 구성

> [AZURE.SELECTOR]
- [VPN 연결 구성](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [DNS 구성](hdinsight-hbase-geo-replication-configure-DNS.md)
- [HBase 복제 구성](hdinsight-hbase-geo-replication.md)
 
두 데이터 센터에서 HBase 복제를 구성하는 방법에 대해 알아봅니다. 클러스터 복제의 몇 가지 사용 사례는 다음과 같습니다.

- 백업 및 재해 복구
- 데이터 집계
- 지리적 데이터 배포
- 오프라인 데이터 분석과 결합된 온라인 데이터 수집

클러스터 복제에서는 원본-푸시 방법을 사용합니다. HBase 클러스터는 원본 또는 대상이거나 두 역할을 동시에 수행할 수 있습니다. 복제는 비동기이며 복제의 목적은 일관성을 유지하는 것입니다. 원본에서 복제를 사용할 수 있는 열 패밀리에 대한 편집 내용을 받은 경우 해당 편집 내용은 모든 대상 클러스터로 전파됩니다. 클러스터 간에 데이터가 복제되면 복제 루프를 방지하기 위해 원본 클러스터 및 데이터를 이미 사용한 모든 클러스터가 추적됩니다. 이 자습서에서는 원본-대상 복제를 구성합니다. 다른 클러스터 토폴로지는 [Apache HBase 참조 가이드](http://hbase.apache.org/book.html#_cluster_replication)를 참조하세요.

이 자습서는 시리즈의 세 번째 부분입니다.

- [두 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-replication-vnet]
- [가상 네트워크에 대한 DNS 구성][hdinsight-hbase-replication-dns]
- HBase 지역에서 복제 구성(이 자습서)

다음 다이어그램에서는 [두 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-geo-replication-vnet] 및 [가상 네트워크에 대한 DNS 구성][hdinsight-hbase-replication-dns]에서 만든 두 가상 네트워크와 네트워크 연결을 보여 줍니다.

![HDInsight HBase 복제 가상 네트워크 다이어그램][img-vnet-diagram]

## <a id="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**.

    PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. Set-executionpolicy cmdlet 사용을 참조하세요.
	
	[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **VPN 연결 및 DNS가 구성된 두 개의 Azure 가상 네트워크**. 자세한 내용은 [두 Azure 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-replication-vnet] 및 [두 Azure 가상 네트워크 간의 DNS 구성][hdinsight-hbase-replication-dns]을 참조하세요.


	PowerShell 스크립트를 실행하기 전에 다음의 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>



## HDInsight에서 HBase 클러스터 프로비전

[두 Azure 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-replication-vnet]에서 유럽 데이터 센터와 미국 데이터 센터에 각각 하나의 가상 네트워크를 만들었습니다. 두 가상 네트워크는 VPN을 통해 연결됩니다. 이 세션에서는 각 가상 네트워크에서 HBase 클러스터를 프로비전합니다. 이 자습서의 뒷부분에서는 HBase 클러스터 중 하나에서 다른 HBase 클러스터를 복제합니다.

Azure 클래식 포털은 사용자 지정 구성 옵션이 있는 HDInsight 클러스터 프로비전을 지원하지 않습니다. 예를 들어 *hbase.replication*을 *true*로 설정합니다. 클러스터를 프로비전한 후 구성 파일에서 값을 설정한 경우 클러스터를 이미지로 다시 설치한 후 설정이 손실됩니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 프로비전][hdinsight-provision]을 참조하세요. 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 프로비전하는 옵션 중 하나는 Azure PowerShell을 사용하는 것입니다.


**Contoso-VNet-EU에서 HBase 클러스터를 프로비전하려면**

1. 워크스테이션에서 Windows PowerShell ISE를 엽니다.
2. 스크립트의 시작 부분에 변수를 설정하고 스크립트를 실행합니다.

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Contoso-VNet-US에서 HBase 클러스터를 프로비전하려면**

- 다음 값과 함께 동일한 스크립트를 사용합니다.

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Azure 계정에 이미 연결했기 때문에 다음 cmdlet은 더 이상 실행할 필요가 없습니다.

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## DNS 조건부 전달자 구성

[가상 네트워크에 대한 DNS 구성][hdinsight-hbase-replication-dns]에서 두 네트워크에 대한 DNS 서버를 구성했습니다. HBase 클러스터는 도메인 접미사가 서로 다릅니다. 따라서 추가 DNS 조건부 전달자를 구성해야 합니다.

조건부 전달자를 구성하려면 두 HBase 클러스터의 도메인 접미사를 알아야 합니다.

**두 HBase 클러스터의 도메인 접미사를 찾으려면**

1. **Contoso-HBase-EU**로 RDP를 연결합니다. 자세한 내용은 [Azure 클래식 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리][hdinsight-manage-portal]를 참조하세요. 이는 실제로 클러스터의 headnode0입니다.
2. Windows PowerShell 콘솔 또는 명령 프롬프트를 엽니다.
3. **ipconfig**를 실행하고 **연결별 DNS 접미사**를 적어 둡니다.
4. RDP 세션을 닫지 마세요. 나중에 도메인 이름 확인을 테스트할 때 필요합니다.
5. 같은 단계를 반복하여 **Contoso-HBase-US**의 **연결별 DNS 접미사**를 확인합니다.


**DNS 전달자를 구성하려면**
 
1.	**Contoso-DNS-EU**로 RDP를 연결합니다.
2.	왼쪽 아래에서 Windows 키를 클릭합니다.
2.	**관리 도구**를 클릭합니다.
3.	**DNS**를 클릭합니다.
4.	왼쪽 창에서 **DSN**, **Contoso-DNS-EU**를 확장합니다.
5.	**조건부 전달자**를 마우스 오른쪽 단추로 클릭하고 **새 조건부 전달자**를 클릭합니다.
5.	다음 정보를 입력합니다.
	- **DNS 도메인**: Contoso-HBase-US의 DNS 접미사를 입력합니다. 예를 들면 Contoso-HBase-US.f5.internal.cloudapp.net과 같습니다.
	- **마스터 서버의 IP 주소**: Contoso-DNS-US의 IP 주소인 10.2.0.4를 입력합니다. IP를 확인하세요. DNS 서버의 IP 주소가 다를 수 있습니다.
6.	**Enter** 키를 누르고 **확인**을 클릭합니다. 이제 Contoso-DNS-EU에서 Contoso-DNS-US의 IP 주소를 확인할 수 있습니다.
7.	다음 값으로 단계를 반복하여 Contoso-DNS-US 가상 컴퓨터의 DNS 서비스에 DNS 조건부 전달자를 추가합니다.
	- **DNS 도메인**: Contoso-HBase-EU의 DNS 접미사를 입력합니다.
	- **마스터 서버의 IP 주소**: Contoso-DNS-EU의 IP 주소인 10.2.0.4를 입력합니다.

**도메인 이름 확인을 테스트하려면**

1. Contoso-HBase-EU RDP 창으로 전환합니다.
2. 명령 프롬프트를 엽니다.
3. Ping 명령을 실행합니다.

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	HBase 클러스터의 작업자 노드에서 ICM 프로토콜이 켜집니다.

4. RDP 세션을 닫지 마세요. 이 자습서의 뒷부분에서 필요합니다.
5. 같은 단계를 반복하여 Contoso-HBase-US에서 Contoso-HBase-EU의 headnode0을 ping합니다.

>[AZURE.IMPORTANT] 다음 단계를 진행하려면 먼저 DNS를 작동해야 합니다.

## HBase 테이블 간에 복제 사용

이제 샘플 HBase 테이블을 만들고 복제를 사용하도록 설정한 다음 일부 데이터로 테스트할 수 있습니다. 사용할 샘플 테이블에는 두 개의 열 패밀리 Personal 및 Office가 있습니다.

이 자습서에서는 유럽 HBase 클러스터를 원본 클러스터로 사용하고, 미국 HBase 클러스터를 대상 클러스터로 사용합니다.

대상 클러스터에서 받은 데이터를 저장할 위치를 알 수 있도록 원본 클러스터와 대상 클러스터 모두에서 동일한 이름 및 열 패밀리로 HBase 테이블을 만듭니다. HBase 셸 사용에 대한 자세한 내용은 [HDInsight에서 Apache HBase 시작][hdinsight-hbase-get-started]을 참조하세요.

**Contoso-HBase-EU에서 HBase 테이블을 만들려면**

1. **Contoso-HBase-EU** RDP 창으로 전환합니다.
2. 데스크톱에서 **Hadoop 명령줄**을 클릭합니다.
2. 폴더를 HBase 홈 디렉터리로 변경합니다.

		cd %HBASE_HOME%\bin
3. HBase 셸을 엽니다.

		hbase shell
4. HBase 테이블을 만듭니다.

		create 'Contacts', 'Personal', 'Office'
5. RDP 세션이나 Hadoop 명령줄 창을 닫지 마세요. 이 자습서의 뒷부분에서 필요합니다.
	
**Contoso-HBase-US에서 HBase 테이블을 만들려면**

- 같은 단계를 반복하여 Contoso-HBase-US에서 동일한 테이블을 만듭니다.


**Contoso-HBase-US를 복제 피어로 추가하려면**

1. **Contso-HBase\_EU** RDP 창으로 전환합니다.
2. HBase 셸 창에서 대상 클러스터(Contoso-HBase-US)를 피어로 추가합니다. 예를 들면 다음과 같습니다.

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	샘플에서는 도메인 접미사가 *contoso-hbase-us.d4.internal.cloudapp.net*입니다. 미국 HBase 클러스터의 도메인 접미사와 일치하도록 이를 업데이트해야 합니다. 호스트 이름 사이에는 공백이 없어야 합니다.

**원본 클러스터에서 복제할 각 열 패밀리를 구성하려면**

1. **Contso-HBase-EU** RDP 세션의 HBase 셸 창에서 복제할 각 열 패밀리를 구성합니다.

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**HBase 테이블에 데이터를 대량으로 업로드하려면**

샘플 데이터 파일이 다음 URL의 공용 Azure Blob 컨테이너로 업로드되었습니다.

		wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

파일 내용은 다음과 같습니다.

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

동일한 데이터 파일을 HBase 클러스터에 업로드하고 해당 클러스터에서 데이터를 가져올 수 있습니다.

1. **Contoso-HBase-EU** RDP 창으로 전환합니다.
2. 데스크톱에서 **Hadoop 명령줄**을 클릭합니다.
3. 폴더를 HBase 홈 디렉터리로 변경합니다.

		cd %HBASE_HOME%\bin

4. 데이터를 업로드합니다.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## 데이터 복제가 수행되는지 확인합니다.

다음 HBase 셸 명령을 사용하여 두 클러스터 모두에서 테이블을 검사하는 방식으로 복제를 확인할 수 있습니다.

		Scan 'Contacts'


## 다음 단계

이 자습서에서는 두 데이터 센터에서 HBase 복제를 구성하는 방법을 살펴보았습니다. HDInsight 및 HBase에 대한 자세한 내용은 다음을 참조하세요.

- [HDInsight 서비스 페이지](https://azure.microsoft.com/services/hdinsight/)
- [HDInsight 설명서](https://azure.microsoft.com/documentation/services/hdinsight/)
- [HDInsight에서 Apache HBase 시작][hdinsight-hbase-get-started]
- [HDInsight HBase 개요][hdinsight-hbase-overview]
- [Azure 가상 네트워크에 HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet]
- [HBase를 사용하여 Twitter 데이터 실시간 분석][hdinsight-hbase-twitter-sentiment]
- [Storm 및 HDInsight(Hadoop)의 HBase를 사용하여 센서 데이터 분석][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

<!---HONumber=AcomDC_0914_2016-->