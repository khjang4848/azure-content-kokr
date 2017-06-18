<properties
	pageTitle="가상 네트워크로 HDInsight 확장 | Microsoft Azure"  
	description="Azure 가상 네트워크를 사용하여 HDInsight 다른 클라우드 리소스 또는 데이터 센터에서 리소스에 연결하는 방법에 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>


#Azure 가상 네트워크를 사용하여 HDInsight 기능 확장

Azure 가상 네트워크를 사용하면 SQL Server와 같은 내부 리소스에 통합하기 위해 또는 클라우드의 리소스 간의 안전한 개인 네트워크를 만드는 Hadoop 솔루션을 확장할 수 있습니다.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>Azure 가상 네트워크란?

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.

	![클라우드 전용 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	가상 네트워크를 사용하여 Azure HDInsight와 함께 Azure 서비스를 연결하는 다음 시나리오를 사용합니다.

	* Azure 웹사이트 또는 Azure 가상 컴퓨터에서 실행되는 서비스에서 **HDInsight 서비스 또는 작업을 호출**합니다.

	* HDInsight와 Azure SQL 데이터베이스, SQL Server 또는 가상 컴퓨터에서 실행 중인 다른 데이터 저장소 솔루션 간에 **직접 데이터를 전송**합니다.

	* 단일 솔루션으로 **여러 HDInsight 서버를 결합**합니다. 예를 들어 들어오는 데이터를 사용하도록 HDInsight Storm 서버를 사용하고 HDInsight HBase 서버에 처리된 데이터를 저장합니다. 원시 데이터는 MapReduce를 사용하여 나중에 분석하기 위해 HDInsight Hadoop 서버에 저장될 수도 있습니다.

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결(사이트 간 또는 지점 및 사이트 간)

	사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.

	![사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

	![지점 및 사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	가상 네트워크를 사용하여 클라우드와 데이터 센터에 링크하면 클라우드 전용 구성에 비슷한 시나리오를 사용할 수 있습니다. 클라우드의 리소스에 대한 작업으로 한정되는 것이 아니라 데이터 센터의 리소스에 대해 작업할 수도 있습니다.

	* HDInsight와 데이터 센터 간에 **직접 데이터를 전송**합니다. 예를 들어 Sqoop을 사용하여 SQL Server에/에서 전송하거나 LOB(기간 업무) 응용 프로그램에서 생성된 데이터를 읽습니다.

	* LOB 응용 프로그램에서 **HDInsight 서비스 또는 작업을 호출**합니다. 예를 들어 HBase Java API를 사용하여 HDInsight HBase 클러스터에서 데이터를 저장 및 검색합니다.

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

> [AZURE.NOTE] HDInsight 클러스터를 프로비전하기 전에 Azure 가상 네트워크를 만들어야 합니다. 자세한 내용은 [가상 네트워크 구성 작업](https://azure.microsoft.com/documentation/services/virtual-network/)을 참조하세요.

## 가상 네트워크 요구 사항

> [AZURE.IMPORTANT] 가상 네트워크에 HDInsight 클러스터를 만들려면 이 섹션에 설명된 특정 가상 네트워크 구성이 필요합니다.

###위치 기반 가상 네트워크

Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹을 기반으로 하는 가상 네트워크와는 연동되지 않습니다.

###클래식 또는 v2 가상 네트워크

Windows 기반 클러스터에는 Classic Virtual Network가 필요하고, Linux 기반 클러스터에는 Azure Resource Manager Virtual Network가 필요합니다. 올바른 유형의 네트워크가 없으면, 클러스터를 만들어도 사용할 수 없습니다.

만들려는 클러스터에서 사용할 수 없는 가상 네트워크에 리소스가 있는 경우에는, 클러스터에서 사용할 수 있는 새로운 가상 네트워크를 만들어서 호환되지 않는 가상 네트워크에 연결할 수 있습니다. 그 후 필요한 네트워크 버전에서 클러스터를 만들면 두 네트워크가 연결되어 있기 때문에 다른 네트워크에 있는 리소스에 액세스할 수 있습니다. 클래식 가상 네트워크와 새 가상 네트워크 연결에 대한 내용은 [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

###사용자 지정 DNS

가상 네트워크를 만들 때 Azure는 네트워크에 설치된 HDInsight 같은 Azure 서비스에 대해 기본 이름 확인 기능을 제공합니다. 그러나 네트워크 간 도메인 이름 확인과 같은 상황의 경우 자체 DNS(Domain Name System)를 사용해야 할 수 있습니다. 예를 들어 연결된 두 가상 네트워크에 있는 서비스 간에 통신하는 경우가 여기에 해당합니다. HDInsight는 Azure 가상 네트워크에서 사용될 경우 사용자 지정 DNS와 기본 Azure 이름 확인을 모두 지원합니다.

Azure 가상 네트워크에서 자체 DNS 서버를 사용하는 방법에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 문서의 __자체 DNS 서버를 사용한 이름 확인__ 섹션을 참조하세요.

###보안 가상 네트워크

HDInsight 서비스는 관리되는 서비스이며 프로비전하고 실행하는 동안 인터넷 액세스가 필요합니다. Azure에서 클러스터의 상태를 모니터링하고 클러스터 리소스의 장애 조치를 시작하며 기타 관리 작업 및 크기 조정 작업을 통해 클러스터의 노드 수를 변경할 수 있도록 합니다.

보안 가상 네트워크에 HDInsight를 설치해야 하는 경우 다음 IP 주소에 포트 443 통해 인바운드 액세스를 허용해야 하며 이를 통해 Azure에서 HDInsight 클러스터를 관리할 수 있게 됩니다.

* 168\.61.49.99
* 23\.99.5.239
* 168\.61.48.131
* 138\.91.141.162

이 주소에 포트 443에서 인바운드 액세스를 허용하면 보안 가상 네트워크에 HDInsight를 정상적으로 설치할 수 있습니다.

> [AZURE.IMPORTANT] HDInsight는 아웃바운드 트래픽 제한은 지원하지 않으며 인바운드 트래픽만 제한합니다. HDInsight를 포함하는 서브넷에 대해 네트워크 보안 그룹 규칙을 정의할 때는 인바운드 규칙만 사용합니다.

다음 예제는 필요한 주소를 허용하고 가상 네트워크 내의 서브넷에 보안 그룹을 적용하는 새 네트워크 보안 그룹을 만드는 방법을 보여 줍니다. 이러한 단계에서는 HDInsight에 설치하려는 가상 네트워크 및 서브넷을 이미 만들었다고 가정합니다.

__Azure PowerShell 사용__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Azure CLI 사용__

1. 다음 명령을 사용하여 `hdisecure`이라는 새 네트워크 보안 그룹을 만듭니다. __RESOURCEGROUPNAME__ 및 __위치__를 그룹을 만든 Azure 가상 네트워크 및 위치(지역)를 포함하는 리소스 그룹으로 바꿉니다.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    그룹을 만들면 새 그룹에 대한 정보를 받습니다. 다음과 유사한 줄을 찾고 `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure` 정보를 저장합니다. 이는 이후 단계에서 사용됩니다.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. 다음을 사용하여 Azure HDInsight 상태 및 관리 서비스에서 포트 443에 대한 인바운드 통신을 허용하는 새 네트워크 보안 그룹에 규칙을 추가합니다. __RESOURCEGROUPNAME__을 Azure 가상 네트워크를 포함하는 리소스 그룹 이름으로 바꿉니다.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. 규칙을 만들면 다음을 사용하여 서브넷에 대한 새 네트워크 보안 그룹에 적용합니다. __RESOURCEGROUPNAME__을 Azure 가상 네트워크를 포함하는 리소스 그룹 이름으로 바꿉니다. __VNETNAME__ 및 __SUBNETNAME__을 Azure 가상 네트워크의 이름 및 HDInsight를 설치할 때 사용할 서브넷의 이름으로 바꿉니다.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    이 명령이 완료되면 이 단계에 사용되는 서브넷에 대한 보안 가상 네트워크에 HDInsight를 성공적으로 설치할 수 있습니다.

> [AZURE.IMPORTANT] 위 단계를 사용하면 Azure 클라우드의 HDInsight 상태 및 관리 서비스에 대한 액세스만 열립니다. 이를 통해 서브넷에 HDInsight 클러스터를 설치할 수 있지만 가상 네트워크 외부에서의 HDInsight 클러스터 액세스는 기본적으로 차단됩니다. 가상 네트워크 외부에서 액세스할 수 있도록 하려는 경우 네트워크 보안 그룹 규칙을 더 추가해야 합니다.
>
> 예를 들어 인터넷에서 SSH 액세스를 허용하려면 다음과 비슷한 규칙을 추가해야 합니다.
>
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI - ```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/virtual-networks-nsg.md)를 참조하세요. Azure 가상 네트워크에서 라우팅 제어에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

##<a id="tasks"></a>작업 및 정보

이 섹션에서는 일반적인 작업 정보 및 가상 네트워크에서 HDInsight를 사용할 때 필요한 정보를 알려줍니다.

###FQDN를 결정합니다.

HDInsight 클러스터는 가상 네트워크 인터페이스에 대한 특정 FQDN(정규화된 도메인 이름)이 할당됩니다. 가상 네트워크에 있는 다른 리소스에서 클러스터에 연결할 때 사용해야 하는 주소입니다. FQDN을 확인하려면 다음 URL을 사용하여 Ambari 관리 서비스를 쿼리합니다.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링](hdinsight-monitor-use-ambari-api.md)을 참조하세요.

클러스터 이름, 서비스 및 YARN 리소스 관리자와 같은 클러스터에서 실행 중인 구성 요소를 지정해야 합니다.

> [AZURE.NOTE] 반환되는 데이터는 많은 구성 요소에 대한 정보가 포함된 JSON(JavaScript Object Notation) 문서입니다. FQDN만 추출하려면 JSON 파서를 사용하여 `host_components[0].HostRoles.host_name` 값을 검색해야 합니다.

예를 들어, HDInsight Hadoop 클러스터에서 FQDN을 반환하려면 다음 방법 중 하나를 사용하여 YARN 리소스 관리자에 대한 데이터를 검색할 수 있습니다.

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [cURL](http://curl.haxx.se/) 및 [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###HBase에 연결

Java API를 사용하여 원격으로 HBase에 연결하려면 HBase 클러스터에 대한 Zookeeper 쿼럼 주소를 결정하고 응용 프로그램에서 이 값을 지정해야 합니다.

Zookeeper 쿼럼 주소를 얻으려면 다음 방법 중 하나를 사용하여 Ambari 관리 서비스를 쿼리합니다.

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) 및 [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링](hdinsight-monitor-use-ambari-api.md)을 참조하세요.

쿼럼 정보를 만든 후 클라이언트 응용 프로그램에서 사용합니다.

예를 들어, HBase API를 사용하는 Java 응용 프로그램의 경우 **hbase-site.xml** 파일을 프로젝트에 추가하고 다음과 같이 파일의 쿼럼 정보를 지정합니다.

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###네트워크 연결 확인

SQL Server와 같은 일부 서비스는 들어오는 네트워크 연결을 제한할 수 있습니다. 이렇게 하면 HDInsight에서 이러한 서비스를 성공적으로 사용할 수 없습니다.

HDInsight에서 서비스에 액세스하는 문제가 발생하는 경우 네트워크 액세스를 사용할 수 있는지 확인하려면 서비스에 대한 설명서를 참조하세요. 동일한 가상 네트워크에 Azure 가상 컴퓨터를 만들어 네트워크 액세스를 확인하고 클라이언트 유틸리티를 사용하여 가상 네트워크를 통해 가상 컴퓨터에서 서비스에 연결할 수 있는지 확인할 수도 있습니다.

##<a id="nextsteps"></a>다음 단계

다음 예에서는 Azure 가상 네트워크에서 HDInsight를 사용하는 방법을 보여줍니다.

* [HDInsight에서 Storm 및 HBase를 사용하여 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md) - 가상 네트워크에서 Storm 및 HBase 클러스터를 구성하는 방법과 Storm에서 HBase로 데이터를 원격으로 작성하는 방법을 보여 줍니다.

* [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md) - Azure 가상 네트워크를 사용하는 정보를 포함하여 Hadoop 클러스터를 프로비전하는 방법에 대한 정보를 제공합니다.

* [HDInsight에서 Hadoop과 함께 Sqoop 사용](hdinsight-use-sqoop-mac-linux.md) - Sqoop을 사용하여 가상 네트워크를 통해 SQL Server로 데이터를 전송하는 방법에 대한 정보를 제공합니다.

Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)(영문)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->