<properties
   pageTitle=" Azure Resource Manager 및 Azure 포털을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기 | Microsoft Azure"
   description="Resource Manager 배포 모델을 사용하여 VNet을 만들고 S2S VPN 게이트웨이 연결을 사용하여 로컬 온-프레미스 네트워크에 연결하는 방법입니다."
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

# Azure 포털을 사용하여 사이트 간 연결로 VNet 만들기

> [AZURE.SELECTOR]
- [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [클래식 - 클래식 포털](vpn-gateway-site-to-site-create.md)


이 문서에서는 **Azure Resource Manager 배포 모델** 및 Azure 포털을 사용하여 온-프레미스 네트워크에 대한 가상 네트워크와 사이트 간 VPN 연결을 만드는 과정을 안내합니다. 사이트간 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

![다이어그램](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)



### 사이트 간 연결에 대한 배포 모델 및 도구

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

VNet을 서로 연결하되 온-프레미스 위치에는 연결하지 않으려는 경우 [VNet간 연결 구성](vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

## 시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

- 호환되는 VPN 장치 및 구성할 수 있는 사람. [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.

- VPN 장치에 대한 외부 연결 공용 IP 주소. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
	
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

### <a name="values"></a>이 연습에 대한 샘플 구성 값


연습으로 이러한 단계를 사용하는 경우 샘플 구성 값을 사용할 수 있습니다.

- VNet 이름: TestVNet1
- 주소 공간: 10.11.0.0/16 및 10.12.0.0/16
- 서브넷:
	- 프런트 엔드: 10.11.0.0/24
	- 백 엔드: 10.12.0.0/24
	- 게이트웨이 서브넷 = 10.12.255.0/27
- 리소스 그룹: TestRG1
- 위치: 미국 동부
- DNS 서버: 8.8.8.8
- 게이트웨이 이름: VNet1GW
- 공용 IP: VNet1GWIP
- VPN 유형: 경로 기반
- 연결 형식: 사이트 간(IPsec)
- 게이트웨이 유형: VPN
- 로컬 네트워크 게이트웨이 이름: Site2
- 연결 이름: VNet1toSite2



## 1\. 가상 네트워크 만들기 

VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다. VNet이 올바른 설정으로 구성되었다면 [DNS 서버 지정](#dns) 섹션의 단계를 시작할 수 있습니다.

### 가상 네트워크를 만들려면

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2\. 다른 주소 공간 및 서브넷 추가

VNet이 만들어지면 여기에 다른 주소 공간 및 서브넷을 추가할 수 있습니다.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. DNS 서버 지정

연습으로 이 구성을 만드는 경우 DNS 서버를 지정할 때 이러한 [값](#values)을 참조합니다.

### DNS 서버를 지정하려면

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\. 게이트웨이 서브넷 만들기

가상 네트워크를 게이트웨이에 연결하기 전에 먼저 연결하려는 가상 네트워크에 대한 게이트웨이 서브넷을 만들어야 합니다. 만들 게이트웨이 서브넷의 이름을 *GatewaySubnet*으로 지정하지 않으면 제대로 작동하지 않습니다.

일부 구성에 대한 게이트웨이 서브넷 접두사는 풀에 필요한 IP 주소 수에 맞게 /28 이상의 서브넷이 필요합니다. 즉, 게이트웨이 서브넷 접두사는 /28, /27, /26 등이어야 합니다. 앞으로 추가 가능한 구성에 맞게 여기에 더 큰 서브넷을 만들려고 할 수 있습니다.

연습으로 이 구성을 만드는 경우 게이트웨이 서브넷을 만들 때 이러한 [값](#values)을 참조합니다.

### 게이트웨이 서브넷을 만들려면

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\. 가상 네트워크 게이트웨이 만들기

연습으로 이 구성을 만드는 경우 게이트웨이를 만들 때 이러한 [값](#values)을 참조합니다.

### 가상 네트워크 게이트웨이를 만들려면

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\. 로컬 네트워크 게이트웨이 만들기

*로컬 네트워크 게이트웨이*는 온-프레미스 위치를 가리킵니다. Azure에서 참조할 수 있는 이름을 로컬 네트워크 게이트웨이에 지정합니다.

연습으로 이 구성을 만드는 경우 로컬 사이트를 추가할 때 이 [값](#values)을 참조합니다.

### 로컬 네트워크 게이트웨이를 만들려면

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\. VPN 장치 구성

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\. 사이트 간 VPN 연결 만들기

가상 네트워크 게이트웨이와 VPN 장치 사이에 사이트 간 VPN 연결을 만듭니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 장치 구성에 사용한 값과 일치해야 합니다.

이 섹션을 시작하기 전에 가상 네트워크 게이트웨이 및 로컬 네트워크 게이트웨이를 다 만들었는지 확인합니다. 연습으로 이 구성을 만드는 경우 연결을 만들 때 이러한 [값](#values)을 참조합니다.

### VPN 연결을 만들려면


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\. VPN 연결 확인

포털에서 또는 PowerShell을 사용하여 VPN 연결을 확인할 수 있습니다.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## 다음 단계

- 연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 가상 컴퓨터 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/virtual-machines)를 참조하세요.

- BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->