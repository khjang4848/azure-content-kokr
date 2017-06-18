<properties 
   pageTitle="두 가상 네트워크 간의 VPN 연결 구성 | Microsoft Azure" 
   description="두 Azure 가상 네트워크 간의 VPN 연결을 구성하는 방법, 두 Azure 가상 네트워크 간의 도메인 이름 확인을 구성하는 방법 및 HBase 지역에서 복제를 구성하는 방법에 대해 알아봅니다." 
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
   ms.date="06/28/2016"
   ms.author="jgao"/>

# 두 Azure 가상 네트워크 간의 VPN 연결 구성  

> [AZURE.SELECTOR]
- [VPN 연결 구성](hdinsight-hbase-geo-replication-configure-VNETs.md)
- [DNS 구성](hdinsight-hbase-geo-replication-configure-DNS.md)
- [HBase 복제 구성](hdinsight-hbase-geo-replication.md)

Azure 가상 네트워크 사이트 간 연결에서는 VPN 게이트웨이를 사용하여 Ipsec/IKE를 통한 보안 터널을 제공합니다. VNet은 서로 다른 구독과 지역에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. Vnet 간 연결을 사용하는 이유는 다음과 같습니다.

- 지역 간 지리적 중복 및 지리적 상태
- 분리 경계가 뚜렷한 지역별 다중 계층 응용 프로그램
- Azure의 구독 간/조직 간 통신

자세한 내용은 [VNet 간 연결 구성](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요.

비디오를 보려면

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

이 자습서는 HBase 지역에서 복제 만들기에 대한 [시리즈][hdinsight-hbase-replication]의 한 부분입니다.

- 두 가상 네트워크 간의 VPN 연결 구성(이 자습서)
- [가상 네트워크에 대한 DNS 구성][hdinsight-hbase-geo-replication-dns]
- [HBase 지역에서 복제 구성][hdinsight-hbase-geo-replication]

다음 다이어그램에서는 이 자습서에서 만들 두 가상 네트워크를 보여 줍니다.

![HDInsight HBase 복제 가상 네트워크 다이어그램][img-vnet-diagram]
 

##필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**.

	PowerShell 스크립트를 실행하기 전에 다음의 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>
		
	[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Azure 서비스 이름과 가상 컴퓨터 이름은 고유해야 합니다. 이 자습서에서 사용되는 이름은 Contoso-[Azure Service/VM name]-[EU/US]입니다. 예를 들어 Contoso-VNet-EU는 북유럽 데이터 센터에 있는 Azure 가상 네트워크이고, Contoso-DNS-US는 미국 동부 데이터 센터에 있는 DNS 서버 VM입니다. 사용자 고유의 이름을 사용해야 합니다.
 

##두 Azure Vnet 만들기



**북유럽에서 Contoso-VNet-EU라는 가상 네트워크를 만들려면**

1.	[Azure 클래식 포털][azure-portal]에 로그인합니다.
2.	**새로 만들기**, **네트워크 서비스**, **가상 네트워크**, **사용자 지정 만들기**를 클릭합니다.
3.	다음을 입력합니다.

	- **이름**: Contoso-VNet-EU
	- **위치**: North Europe

		이 자습서에서는 북유럽 및 미국 동부 데이터 센터를 사용합니다. 사용자 고유의 데이터 센터를 선택할 수 있습니다.
4.	다음을 입력합니다.

	- **DNS 서버**:(빈 상태로 둠)
	
		가상 네트워크 내에서 이름 확인을 위해 자체 DNS 서버가 필요합니다. Azure가 제공한 이름 확인을 사용하는 경우 및 사용자 고유의 DNS 서버를 사용하는 경우에 대한 자세한 내용은 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요. VNet 간의 이름 확인을 구성하는 지침은 [두 Azure 가상 네트워크 간 DNS 구성][hdinsight-hbase-dns]을 참조하세요.
  
	- **지점 및 사이트 간 VPN 구성**:(선택 안 함)

		지점 대 사이트는 이 시나리오에 적용되지 않습니다.

 	- **사이트 간 VPN 구성**: (선택 안 함)
 	
		미국 동부 데이터 센터의 Azure 가상 네트워크에 대한 사이트 간 VPN 연결을 구성합니다.
5.	다음을 입력합니다.

	- 	**주소 공간 시작 IP**: 10.1.0.0
	- 	**주소 공간 CIDR**: /16
	- 	**서브넷-1 시작 IP**: 10.1.0.0
	- 	**서브넷-1 CIDR**: /24

	주소 공간은 미국 가상 네트워크와 겹칠 수 없습니다.

**서유럽에서 Contoso-VNet-EU라는 가상 네트워크를 만들려면**

- 다음 값으로 마지막 절차를 반복합니다.

	- **이름**: Contoso-VNet-US
	- **위치**: East US
	 
	- **DNS 서버**: (빈 상태로 둠)
	- **지점 및 사이트 간 VPN 구성**:(선택 안 함)
	- **사이트 간 VPN 구성**: (선택 안 함)
	 
	- **주소 공간 시작 IP**: 10.2.0.0
	- **주소 공간 CIDR**: /16
	- **서브넷-1 시작 IP**: 10.2.0.0
	- **서브넷-1 CIDR**: /24

















##두 VNet 간의 VPN 연결 구성

###로컬 네트워크 만들기

VNet 간 구성을 만드는 경우 서로를 로컬 네트워크 사이트로 식별하도록 각 VNet을 구성해야 합니다. 이 섹션에서는 각 VNet을 로컬 네트워크로 구성합니다. 로컬 네트워크는 해당 VNet과 동일한 IP 주소 공간을 공유합니다.

![Azure VPN 사이트 간 구성 - Azure 로컬 네트워크][img-vnet-lnet-diagram]


**Contoso-VNet-EU 네트워크 주소 공간과 일치하는 Contoso-LNet-EU라는 로컬 네트워크를 만들려면**

1. Azure 클래식 포털에서 **새로 만들기**, **네트워크 서비스**, **가상 네트워크**, **로컬 네트워크 추가**를 클릭합니다.
3. 다음을 입력합니다.

	- **이름**: Contoso-LNet-EU
	- **VPN 장치 IP 주소**: 192.168.0.1(이 주소는 나중에 업데이트됨)

		일반적으로는 VPN 장치의 실제 외부 IP 주소를 사용합니다. VNet 간 구성의 경우 VPN 게이트웨이 IP 주소를 사용합니다. 두 VNet에 대해 VPN 게이트웨이를 아직 만들지 못한 점을 고려하여 임의적인 IP 주소를 입력하고 다시 와서 수정합니다.
4.	다음을 입력합니다.

	- **주소 공간 시작 IP**: 10.1.0.0
	- **주소 공간 CIDR**: /16
	
	이전에 Contoso-VNet-EU에 대해 지정한 범위와 정확히 일치해야 합니다.

**Contoso-VNet-US 네트워크 주소 공간과 일치하는 Contoso-LNet-US라는 로컬 네트워크를 만들려면**

- 다음 매개 변수를 사용하여 마지막 절차를 반복합니다.

	- **이름**: Contoso-LNet-US
	- **VPN 장치 IP 주소**: 192.168.0.1(이 주소는 나중에 업데이트됨)
	 
	- **주소 공간 시작 IP**: 10.2.0.0
	- **주소 공간 CIDR**: /16


###VPN 게이트웨이 만들기

이 구성에는 두 부분이 있습니다. 먼저 로컬 네트워크에 대한 VNet 사이트 간 연결을 구성한 다음 동적 라우팅 VPN을 만듭니다. VNet 간 연결에는 동적 라우팅 VPN이 있는 Azure VPN 게이트웨이가 필요합니다. Azure 정적 라우팅 VPN은 지원되지 않습니다.

**Contoso-LNet-US에 대한 Contoso-VNet-EU 사이트 간 연결을 구성하려면**

1.	Azure 클래식 포털의 왼쪽 창에서 **네트워크**를 클릭합니다.
2.	**Contoso-VNet-EU**를 클릭합니다.
3.	**구성** 탭을 클릭합니다.
4.	**로컬 네트워크에 연결**을 선택합니다.
5.	**로컬 네트워크**에서 **Contoso-LNet-US**를 선택합니다.
6.	가상 네트워크 주소 공간 섹션에서 **게이트웨이 서브넷 추가**를 클릭합니다.
7.	**저장**을 클릭합니다.
8.	**확인**을 클릭하여 확인합니다.


**Contoso-VNet-EU에 대한 VPN 게이트웨이를 만들려면**

1.	Azure 클래식 포털에서 **대시보드** 탭을 클릭합니다.
4.	페이지 아래쪽에서 **게이트웨이 만들기**를 클릭한 다음 **동적 라우팅**을 클릭합니다.
5.	**예**를 클릭하여 확인합니다. 페이지의 게이트웨이 그래픽이 노란색으로 변경되고 게이트웨이를 만드는 중이 표시됩니다. 일반적으로 게이트웨이를 만드는 데 약 15분이 소요됩니다.

	게이트웨이 상태가 연결 중으로 변경되면 각 게이트웨이의 IP 주소가 대시보드에 표시됩니다. 혼합되지 않도록 주의하여 각 VNet에 해당하는 IP 주소를 적어 둡니다. 이는 로컬 네트워크의 VPN 장치에 대한 자리 표시자 IP 주소를 편집할 때 사용되는 IP 주소입니다.

6.	**게이트웨이 IP 주소** 복사본을 만듭니다. 다음 섹션에서 Contoso-VNet-EU에 대한 VPN 게이트웨이 IP 주소를 구성하는 데 사용됩니다.

**Contoso-VNet-EU에 대한 VPN 게이트웨이를 만들려면**

- 마지막 두 절차를 반복하여 Contoso-LNet-EU에 대한 Contoso-VNet-US 사이트 간 연결을 구성하고 Contoso-Vnet-US에 대한 VPN 게이트웨이를 만듭니다. 작업을 마치면 Contoso-VNet-US에 대한 VPN 게이트웨이 IP 주소가 생성됩니다.


### 로컬 네트워크에 대한 VPN 장치 IP 주소를 설정합니다.
마지막 섹션에서는 각 VNet에 대한 VPN 게이트웨이를 만듭니다. VPN 게이트웨이의 IP 주소를 가져왔습니다. 이제 돌아가서 로컬 네트워크 VPN 장치 IP 주소를 구성할 수 있습니다.

**Contoso-LNet-EU에 대한 VPN 장치 IP 주소를 구성하려면**

1.	Azure 클래식 포털의 왼쪽 창에서 **네트워크**를 클릭합니다.
2.	위쪽에서 **로컬 네트워크**를 클릭합니다.
3.	**Contoso-LNet-EU**를 클릭한 다음 아래쪽에서 **편집**을 클릭합니다.
4.	**VPN 장치 IP 주소**를 업데이트합니다. 이는 Contoso-VNET-EU의 대시보드 탭에서 가져온 주소입니다.
5.	오른쪽 단추를 클릭합니다.
6.	선택 단추를 클릭합니다.

**Contoso-LNet-US에 대한 VPN 장치 IP 주소를 구성하려면**

- 마지막 절차를 반복하여 Contoso-LNet-US에 대한 VPN 장치 IP 주소를 구성합니다.

###VNet 게이트웨이 키 설정

VNet 게이트웨이는 공유 키를 사용하여 가상 네트워크 간의 연결을 인증합니다. Azure 클래식 포털에서는 키를 구성할 수 없습니다. PowerShell 또는.NET SDK를 사용해야 합니다.

**키를 설정하려면**

1. 워크스테이션에서 **Windows PowerShell ISE** 또는 Windows PowerShell 콘솔을 엽니다.
2. 다음 스크립트의 매개 변수를 업데이트하고 실행합니다.

		Add-AuzreAccount
		Select-AzureSubscription -[AzureSubscriptionName]
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
		Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##VPN 연결 확인 

VNet에 VM을 배포하지 않은 경우 가상 네트워크의 시각적 다이어그램인 Azure 클래식 포털의 VNet 대시보드 페이지를 사용하여 연결 상태를 확인할 수 있습니다.

![HDInsight HBase 복제 가상 네트워크 VPN 연결 상태][img-vpn-status]
  



##다음 단계

이 자습서에서는 두 Azure 가상 네트워크 간의 VPN 연결을 구성하는 방법에 대해 알아보았습니다. 시리즈의 다른 두 문서에서는 다음 내용을 다룹니다.

- [두 Azure 가상 네트워크 간의 DNS 구성][hdinsight-hbase-geo-replication-dns]
- [HBase 지역에서 복제 구성][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-DNS.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.LNet.diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-VNets/HDInsight.HBase.VPN.status.png

<!---HONumber=AcomDC_0914_2016-->