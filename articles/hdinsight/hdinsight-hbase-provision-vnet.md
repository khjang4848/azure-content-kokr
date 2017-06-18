<properties
	pageTitle="가상 네트워크에 HBase 클러스터 프로비전 | Microsoft Azure"
	description="Azure HDInsight에서 HBase 사용 시작 Azure 가상 네트워크에 HDInsight HBase 클러스터를 만드는 방법을 알아봅니다."
	keywords=""
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
   ms.date="06/27/2016"
   ms.author="jgao"/>

# Azure 가상 네트워크에 HBase 클러스터 만들기 

[Azure 가상 네트워크][1]에서 Azure HDInsight HBase 클러스터를 만드는 방법에 대해 알아봅니다.

가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다. 이점은 다음과 같습니다.

- 웹 응용 프로그램을 HBase 클러스터의 노드에 직접 연결하므로 HBase Java RPC(원격 프로시저 호출) API를 통해 통신할 수 있습니다.
- 트래픽이 여러 게이트웨이 및 부하 분산 장치를 통과하지 않도록 하여 성능을 향상시킵니다.
- 공용 끝점을 노출하지 않고 중요한 정보를 보다 안전한 방식으로 처리할 수 있습니다.

###필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.

## 가상 네트워크에 HBase 클러스터 만들기

이 섹션에서는 [Azure ARM 템플릿](../resource-group-template-deploy.md)을 사용하여 HDInsight에서 Linux 기반 HBase 클러스터를 만듭니다. 이 자습서를 따라하는 데 Azure ARM 템플릿에 대한 경험이 필요하지는 않습니다. 기타 클러스터 생성 방법 및 설정에 대한 이해는 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. ARM 템플릿을 사용하여 HDInsight에서 Hadoop 클러스터를 만드는 방법에 대한 자세한 내용은 [ARM 템플릿을 사용하여 HDInsight에서 Hadoop 클러스터 만들기](hdinsight-hadoop-create-windows-clusters-arm-templates.md)를 참조하세요.

1. Azure 포털에서 ARM 템플릿을 열려면 다음 이미지를 클릭합니다. ARM 템플릿은 공용 BLOB 컨테이너에 있습니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **매개 변수** 블레이드에서 다음을 입력합니다.
    - **ClusterName**: 만들려는 Hadoop 클러스터의 이름을 입력합니다.
    - **Cluster login name and password**(클러스터 로그인 이름 및 암호): 기본 로그인 이름은 **admin**입니다.
    - **SSH username and password**(SSH 사용자 이름 및 암호): 기본 사용자 이름은 **sshuser**입니다. 이름은 변경할 수 있습니다.
	
	일부 속성이 템플릿에 하드 코딩되었습니다. 예를 들면 다음과 같습니다.<br/>

    - 위치: 미국 동부
	- 클러스터 작업자 노드 수: 4
    - 기본 저장소 계정: &lt; 클러스터 이름>저장소
    - 가상 네트워크 이름: &lt;클러스터 이름>-vnet
    - 가상 네트워크 주소 공간: 10.0.0.0/16
    - 서브넷 이름: 기본값
    - 서브넷 주소 범위: 10.0.0.0/24

3. **확인**을 클릭하여 매개 변수를 저장합니다.
4. **사용자 지정 배포** 블레이드에서 **리소스 그룹** 드롭다운 상자를 클릭한 다음 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 클러스터, 종속 저장소 계정 및 기타 연결된 리소스를 그룹화하는 컨테이너입니다.
5. **약관**을 클릭한 다음 **만들기**를 클릭합니다.
6. **만들기**를 클릭합니다. **템플릿 배포에 배포 제출 중**이라는 제목의 새 타일이 표시됩니다. 클러스터를 만들려면 20분 정도가 걸립니다. 클러스터가 만들어졌으면 포털에서 클러스터 블레이드를 클릭하면 열립니다.

이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure 저장소에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 클러스터 삭제에 대한 내용은 [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md#delete-clusters)를 참조하세요.

새 HBase 클러스터 사용을 시작하려는 경우 [HDInsight에서 Hadoop을 통해 HBase 사용 시작](hdinsight-hbase-tutorial-get-started.md)의 절차를 사용할 수 있습니다.

##HBase Java RPC API를 사용하여 HBase 클러스터에 연결

1.	동일한 Azure 가상 네트워크와 동일한 서브넷에 IaaS(infrastructure as a service) 가상 컴퓨터를 만듭니다. 따라서 가상 컴퓨터와 HBase 클러스터가 둘 다 동일한 내부 DNS 서버를 사용하여 호스트 이름을 확인합니다. 이렇게 하려면 **갤러리에서** 옵션을 선택한 다음 데이터 센터 대신 가상 네트워크를 선택해야 합니다. 지침에 대해서는 [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)를 참조하세요. 작은 VM 크기의 표준 Windows Server 2012 이미지면 충분합니다.

2.	Java 응용 프로그램을 사용하여 HBase에 원격으로 연결할 때는 FQDN(정규화된 도메인 이름)을 사용해야 합니다. FQDN을 확인하려면 HBase 클러스터의 연결별 DNS 접미사를 가져와야 합니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

	* 웹 브라우저를 사용하여 Ambari를 호출합니다.
	
		https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal\_response=true로 이동합니다. JSON 파일의 DNS 접미사를 설정합니다.

	* Ambari 웹 사이트를 사용합니다.

		1. https://&lt;ClusterName>.azurehdinsight.net으로 이동합니다.
		2. 상위 메뉴에서 **호스트**를 클릭합니다.

	* Curl을 사용하여 REST를 호출합니다.

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		반환되는 JSON(JavaScript Object Notation) 데이터에서 "host\_name" 항목을 찾습니다. 이 항목에는 클러스터의 노드에 대한 FQDN이 포함되어 있습니다. 예:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다. 예를 들면 mycluster.b1.cloudapp.net과 같습니다.

	* Azure PowerShell 사용
	
		다음 Azure PowerShell 스크립트를 사용하여 DNS 접미사를 반환하는 데 사용할 수 있는 **Get-ClusterDetail** 함수를 등록합니다.

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Azure PowerShell 스크립트를 실행한 후 다음 명령을 사용하여 **Get-ClusterDetail** 함수를 통해 DNS 접미사를 반환합니다. 이 명령을 사용할 때는 HDInsight HBase 클러스터 이름, 관리자 이름 및 관리자 암호를 지정합니다.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		그러면 DNS 접미사가 반환됩니다. 예를 들면 **yourclustername.b4.internal.cloudapp.net**과 같습니다.

	* RDP 사용
	
		원격 데스크톱을 사용하여 HBase 클러스터에 연결하고(헤드 노드에 연결됨) 명령 프롬프트에서 **ipconfig**를 실행하여 DNS 접미사를 가져올 수도 있습니다. RDP를 사용하도록 설정하고 RDP(원격 데스크톱 프로토콜)를 통해 클러스터에 연결하는 방법에 대한 지침은 [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리][hdinsight-admin-portal]를 참조하세요.
		
		![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

가상 컴퓨터가 HBase 클러스터와 통신할 수 있는지 확인하려면 가상 컴퓨터에서 `ping headnode0.<dns suffix>` 명령을 사용합니다. 예를 들면 ping headnode0.mycluster.b1.cloudapp.net을 사용합니다.

Java 응용 프로그램에서 이 정보를 사용하려는 경우 [Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성](hdinsight-hbase-build-java-maven.md)의 단계에 따라 응용 프로그램을 만들 수 있습니다. 응용 프로그램이 원격 HBase 서버에 연결하도록 하려면 이 예제의 **hbase-site.xml** 파일이 ZooKeeper의 FQDN을 사용하도록 수정합니다. 예:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 자체 DNS 서버를 사용하는 방법을 포함한 Azure 가상 네트워크의 이름 확인에 대한 자세한 내용은 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

##다음 단계

이 자습서에서는 HBase 클러스터를 만드는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md)
- [HDInsight의 Hadoop에서 HBase 사용 시작](hdinsight-hbase-tutorial-get-started.md)
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석](hdinsight-hbase-analyze-twitter-sentiment.md)
- [가상 네트워크 개요][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "새 HBase 클러스터에 대한 프로비전 정보"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "스크립트 작업을 사용하여 HBase 클러스터 사용자 지정"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->