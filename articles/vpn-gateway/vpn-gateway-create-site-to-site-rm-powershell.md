<properties
   pageTitle="Azure Resource Manager 및 PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기 | Microsoft Azure"
   description="이 문서에서는 Resource Manager 배포 모델을 사용하여 VNet을 만들고 S2S VPN 게이트웨이 연결을 사용하여 로컬 온-프레미스 네트워크에 연결하는 과정을 안내합니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# PowerShell을 사용하여 사이트 간 연결로 VNet 만들기

> [AZURE.SELECTOR]
- [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)

이 문서에서는 **Azure Resource Manager 배포 모델**을 사용하여 온-프레미스 네트워크에 대한 가상 네트워크 및 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 사이트간 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

![사이트 간 다이어그램](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "사이트 간")


### 사이트 간 연결에 대한 배포 모델 및 도구

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

VNet을 서로 연결하되 온-프레미스 위치에는 연결하지 않으려는 경우 [VNet간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.


## 시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

- 호환되는 VPN 장치 및 구성할 수 있는 사람. [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.

- VPN 장치에 대한 외부 연결 공용 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
	
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	
- 최신 버전의 Azure Resource Manager PowerShell cmdlet. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.


## 1\. 구독에 연결 

리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

	Login-AzureRmAccount

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription 

사용할 구독을 지정합니다.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. 가상 네트워크 및 게이트웨이 서브넷 만들기

예제에서는 게이트웨이 서브넷 /28을 사용합니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 권장하지 않습니다. 추가 기능 요구 사항을 수용하기 위해 게이트웨이 서브넷 /27 이상(/26, /25 등)을 만드는 것이 좋습니다.

/29 이상인 게이트웨이 서브넷이 포함된 가상 네트워크가 이미 있는 경우 [로컬 네트워크 게이트웨이 추가](#localnet)로 바로 이동할 수 있습니다.


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### 가상 네트워크 및 게이트웨이 서브넷을 만들려면

다음 샘플을 사용하여 가상 네트워크 및 게이트웨이 서브넷을 만듭니다. 사용자 고유의 값으로 대체합니다.

먼저 리소스 그룹을 만듭니다.
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

그런 다음 가상 네트워크를 만듭니다. 지정한 주소 공간이 온-프레미스 네트워크에 가지고 있는 주소 공간과 겹치지 않는지 확인하세요.

다음 샘플은 *testvnet*이라는 가상 네트워크와 *GatewaySubnet* 및 *Subnet1*이라는 두 서브넷을 만듭니다. 특히 *GatewaySubnet*이라는 서브넷을 만드는 것이 중요합니다. 다른 이름을 지정하는 경우 연결 구성이 실패합니다.

변수를 설정합니다.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

VNet을 만듭니다.

	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
	-Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>이미 만든 가상 네트워크에 게이트웨이 서브넷을 추가하려면

이 단계는 이전에 만든 VNet에 게이트웨이 서브넷을 추가해야 하는 경우에만 필요합니다.

다음 샘플을 사용하여 게이트웨이 서브넷을 만들 수 있습니다. 게이트웨이 서브넷의 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하는 경우 서브넷을 만들지만 Azure에서는 게이트웨이 서브넷으로 취급하지 않습니다.

변수를 설정합니다.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

게이트웨이 서브넷을 만듭니다.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

구성을 설정합니다.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>로컬 네트워크 게이트웨이 추가

가상 네트워크에서 로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. Azure에서 참조할 수 있는 이름을 해당 사이트에 지정하고 로컬 네트워크 게이트웨이에 대한 주소 공간 접두사를 지정합니다.

Azure는 지정된 IP 주소 접두사를 사용하여 온-프레미스 위치로 보낼 트래픽을 식별합니다. 즉, 로컬 네트워크 게이트웨이에 연결하려는 각 주소 접두사를 지정해야 합니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다.

PowerShell 예제를 사용할 때는 다음 사항에 유의하세요.
	
- *GatewayIPAddress*는 온-프레미스 VPN 장치의 IP 주소입니다. VPN 장치는 NAT 뒤에 배치할 수 없습니다.
- *AddressPrefix*는 온-프레미스 주소 공간입니다.

로컬 네트워크 게이트웨이에 단일 주소 접두사를 추가하려면:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

로컬 네트워크 게이트웨이에 중복 주소 접두사를 추가하려면:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### 로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 수정하려면

경우에 따라 로컬 네트워크 게이트웨이 접두사를 변경합니다. IP 주소 접두사를 수정하기 위해 수행하는 단계는 VPN 게이트웨이 연결을 만들었는지에 따라 달라집니다. 이 문서의 [로컬 네트워크 게이트웨이에 대한 IP 주소 접두사 수정](#modify) 섹션을 참조하세요.


## 4\. VPN 게이트웨이에 대한 공용 IP 주소 요청

다음에는 Azure VNet VPN 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 이 주소는 VPN 장치에 할당되는 것과 동일한 IP 주소가 아니라 Azure VPN 게이트웨이 자체에 할당됩니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다. 게이트웨이에 연결할 온-프레미스 VPN 장치를 구성할 때 이 IP 주소를 사용합니다.

리소스 관리자 배포 모델에 대한 Azure VPN 게이트웨이는 현재 동적 할당 방법을 사용하여 공용 IP 주소를 지원합니다. 그러나 이로 인해 IP 주소가 변경되지는 않습니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 Azure VPN 게이트웨이 IP 주소가 변경됩니다. 게이트웨이 공용 IP 주소는 Azure VPN 게이트웨이의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드를 변경하지 않습니다.

다음 PowerShell 샘플을 사용합니다.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. 게이트웨이 IP 주소 지정 구성 만들기

게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 다음 샘플을 사용하여 게이트웨이 구성을 만듭니다.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. 가상 네트워크 게이트웨이 만들기

이 단계에서는 가상 네트워크 게이트웨이를 만듭니다. 게이트웨이를 만드는 작업은 완료하는 데 다소 시간이 소요됩니다. 45분 이상 걸리기도 합니다.

다음 값을 사용합니다.

- 사이트 간 구성에 대한 *-GatewayType*은 *Vpn*입니다. 게이트웨이 유형은 항상 구현하는 구성에 따라 다릅니다. 예를 들어 다른 게이트웨이 구성인 GatewayType Express 경로가 필요할 수 있습니다.

- *-VpnType*은 *RouteBased*(일부 설명서에서는 동적 게이트웨이라고도 함) 또는 *PolicyBased*(일부 설명서에서는 정적 게이트웨이라고도 함)일 수 있습니다. VPN 게이트웨이 형식에 대한 자세한 내용은 [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md#vpntype)를 참조하세요.
- *-GatewaySku*는 *기본*, *표준* 또는 *HighPerformance*일 수 있습니다.

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
		-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
		-VpnType RouteBased -GatewaySku Standard

## 7\. VPN 장치 구성

이제 온-프레미스 VPN 장치를 구성하기 위한 가상 네트워크 게이트웨이의 공용 IP 주소가 필요합니다. 특정 구성 정보는 장치 제조업체에 문의하세요. 자세한 내용은 [VPN 장치](vpn-gateway-about-vpn-devices.md)를 참조하세요.

가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 다음 샘플을 사용합니다.

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. VPN 연결 만들기

다음으로 가상 네트워크 게이트웨이와 VPN 장치 사이에 사이트 간 VPN 연결을 만듭니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 장치 구성에 사용한 값과 일치해야 합니다. 사이트 간에 대한 `-ConnectionType`은 *IPsec*입니다.

변수를 설정합니다.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

연결을 만듭니다.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

잠시 후, 연결이 설정됩니다.

## <a name="toverify"></a>VPN 연결을 확인하려면

VPN 연결을 확인하는 몇 가지 방법이 있습니다.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 수정하려면

로컬 네트워크 게이트웨이에 대한 접두사를 변경해야 하는 경우 다음 지침을 사용합니다. 두 가지 지침이 제공됩니다. 게이트웨이 연결을 이미 만들었는지 여부에 따라 선택하는 지침이 다릅니다.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>로컬 네트워크 게이트웨이에 대한 IP 주소를 수정하려면

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## 다음 단계

- 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)를 참조하세요.

- BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->