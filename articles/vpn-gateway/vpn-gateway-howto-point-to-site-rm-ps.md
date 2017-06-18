<properties 
   pageTitle="Resource Manager 배포 모델을 사용하여 가상 네트워크에 지점 및 사이트 간 VPN Gateway 연결 구성 | Microsoft Azure"
   description="지점 및 사이트 간 VPN Gateway 연결을 만들어 Azure Virtual Network에 안전하게 연결합니다."
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
   ms.author="cherylmc" />

# PowerShell을 사용하여 VNet에 지점 및 사이트 간 연결 구성

> [AZURE.SELECTOR]
- [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [클래식 - 클래식 포털](vpn-gateway-point-to-site-create.md)

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S 연결은 집 또는 회의와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다.

이 문서에서는 **Resource Manager 배포 모델**에서 지점 및 사이트 간 연결로 VNet을 만드는 방법을 안내합니다. 이 단계에서는 PowerShell이 필요합니다. 지금은 Azure 포털에서 종단 간 솔루션을 만들 수 없습니다.

지점 및 사이트 간 연결을 작동하는 데는 VPN 장치 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 연결을 시작하여 VPN 연결을 설정합니다. 지점 및 사이트 간 연결에 대한 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) 및 [계획 및 설계](vpn-gateway-plan-design.md)를 참조하세요.



**Azure 배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**지점 및 사이트 간 연결에 대한 배포 모델 및 도구**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![지점 및 사이트 간 다이어그램](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "지점 및 사이트 간")


## 이 구성에 대한 정보

이 시나리오에서는 지점 및 사이트 간 연결로 가상 네트워크를 만듭니다. 지침은 이 구성에 필요한 인증서를 생성하는 데 도움이 됩니다. P2S 연결은 VPN 게이트웨이가 포함된 VNet, 루트 인증서 .cer 파일(공용 키), 클라이언트 인증서 및 클라이언트 쪽 VPN 구성으로 이루어집니다.

이 구성에 대해 다음 값을 사용합니다. 문서의 섹션 [1](#declare)에서 변수를 설정합니다. 단계를 연습으로 사용하고 값을 변경 없이 사용하거나 변경하여 사용자 환경을 반영합니다.

- 이름: **VNet1**, 주소 공간 **192.168.0.0/16** 및 **10.254.0.0/16** 사용. VNet에는 하나 이상의 주소 공간을 사용할 수 있습니다.
- 서브넷 이름: **FrontEnd**, **192.168.1.0/24** 사용
- 서브넷 이름: **BackEnd**, **10.254.1.0/24** 사용
- 서브넷 이름: **GatewaySubnet**, **192.168.200.0/24** 사용. 서브넷 이름 *GatewaySubnet*은 게이트웨이가 작동하기 위한 필수 항목입니다.
- VPN 클라이언트 주소 풀: **172.16.201.0/24**. 이 지점 및 사이트 간 연결을 사용 하여 VNet에 연결되는 VPN 클라이언트는 이 풀에서 IP 주소를 받습니다.
- 구독: 하나 이상 있는 경우 올바른 구독인지 확인합니다.
- 리소스 그룹: **TestRG**
- 위치: **미국 동부**
- DNS 서버: 이름 확인에 사용할 DNS 서버의 **IP 주소**.
- GW 이름: **GW**
- 공용 IP 이름: **GWIP**
- VpnType: **RouteBased**


## 시작하기 전에

- Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
	
- Azure Resource Manager PowerShell cmdlet(1.0.2 이상)을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요. 이 구성에 PowerShell을 사용할 때 관리자 권한으로 실행되고 있는지 확인합니다.

## <a name="declare"></a>1부 - 로그인 및 변수 설정

이 섹션에서는 로그인하고 이 구성에 사용되는 값을 선언합니다. 선언된 값은 예제 스크립트에 사용됩니다. 값을 변경하여 고유한 환경을 반영합니다. 또는 선언된 값을 사용하고 단계를 연습 삼아 살펴볼 수 있습니다.

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 이 cmdlet은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

		Login-AzureRmAccount 

2. Azure 구독 목록을 가져옵니다.

		Get-AzureRmSubscription

3. 사용할 구독을 지정합니다.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 사용할 변수를 선언합니다. 다음 샘플을 사용하여 필요할 때 고유한 값으로 대체합니다.

		$VNetName  = "VNet1"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"


## 2부 - VNet 구성 


1. 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name $RG -Location $Location

2. 가상 네트워크에 대한 서브넷 구성을 만들고 *FrontEnd*, *BackEnd* 및 *GatewaySubnet*으로 이름을 지정합니다. 이러한 접두사는 선언된 VNet 주소 공간의 일부여야 합니다.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. 가상 네트워크 만들기 지정된 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버여야 합니다. 이 예제에서는 공용 IP 주소를 사용했습니다. 고유한 값을 사용해야 합니다.
	
		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. 만든 가상 네트워크에 대한 변수를 지정합니다.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. 동적으로 할당된 공용 IP 주소를 요청합니다. 이 IP 주소는 게이트웨이가 제대로 작동하는 데 필요합니다. 나중에 게이트웨이를 게이트웨이 IP 구성에 연결합니다.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## 3부 - 신뢰할 수 있는 인증서 추가

Azure는 인증서를 사용하여 P2S를 통해 연결하려는 클라이언트를 인증합니다. 루트 인증서에 대한 .cer 파일(공개 키)을 Azure에 가져옵니다. Azure에 Base64 인코딩 X.509(.cer) 파일을 추가할 때 파일이 나타내는 루트 인증서를 신뢰하도록 Azure에 지시합니다.

엔터프라이즈 솔루션을 사용하는 경우 기존 인증서 체인을 사용할 수 있습니다. 엔터프라이즈 CA 솔루션을 사용하지 않는 경우 자체 서명된 루트 인증서를 만들 수 있습니다. 자체 서명된 인증서를 만드는 한 가지 메서드는 makecert입니다. *makecert*를 사용하여 자체 서명된 루트 인증서를 만드는 방법은 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md)을 참조하세요.

인증서를 가져온 방법과 관계 없이 Azure에 인증서의 .cer 파일을 업로드하고 클라이언트 인증서를 생성하여 연결하려는 클라이언트에 설치합니다. 자체 서명된 인증서를 클라이언트에 직접 설치하지 않습니다. 이 문서의 [클라이언트 구성](#cc) 섹션에서 나중에 클라이언트 인증서를 생성할 수 있습니다.
		
Azure에 최대 20개의 신뢰할 수 있는 인증서를 추가할 수 있습니다. 공개 키를 가져오려면 인증서를 Base64 인코딩 X.509(.cer) 파일로 내보냅니다. .cer를 내보내는 방법은 **certmger.msc**를 열고 개인/인증서에서 인증서를 찾는 것입니다. "Base-64로 인코딩된 X.509(. CER)"인 개인 키 없이 마우스 오른쪽 단추로 클릭하고 내보냅니다. .cer 파일로 내보낸 파일 경로를 기록해 둡니다. 이것은 인증서의 Base64 문자열 표현을 가져오는 샘플입니다.

Azure에 신뢰할 수 있는 인증서를 추가합니다. 이 단계에서 고유한 .cer 파일 경로를 사용해야 합니다. 이 문서의 1부에서 설정한 $P2SRootCertName = "ARMP2SRootCert.cer" 변수에 주의하세요. 인증서의 이름이 다른 경우 변수를 적절하게 조정합니다.
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## 4부 - VPN 게이트웨이 만들기

VNet용 가상 네트워크 게이트웨이를 구성하고 만듭니다. *-GatewayType*은 **Vpn**이어야 하고 *-VpnType*은 **RouteBased**여야 합니다. 완료하려면 최대 45분이 걸릴 수 있습니다.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## 5부 - VPN 클라이언트 구성 패키지 다운로드

P2S를 사용하여 Azure에 연결된 클라이언트에는 클라이언트 인증서와 VPN 클라이언트 구성 패키지가 설치되어야 합니다. VPN 클라이언트 구성 패키지는 Windows 클라이언트에 사용할 수 있습니다. VPN 클라이언트 패키지는 Windows에 기본 제공되고 연결하려는 VPN에 관련된 VPN 클라이언트 소프트웨어를 구성하는 정보를 포함합니다. 패키지는 추가 소프트웨어를 설치하지 않습니다. 자세한 내용은 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections)를 참조하세요.

1. 게이트웨이를 만든 후에 다음 예제를 사용하여 클라이언트 구성 패키지를 다운로드합니다.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. PowerShell cmdlet에서 URL 링크를 반환합니다. 다음은 반환된 URL 형태의 예입니다.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. 반환된 링크를 웹 브라우저에 복사 및 붙여넣기 하여 패키지를 다운로드합니다. 그런 다음 클라이언트 컴퓨터에 패키지를 설치합니다.

4. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 클릭합니다. 나열된 연결이 표시됩니다. 연결된 가상 네트워크의 이름이 다음 예제와 비슷하게 표시됩니다.

	![VPN 클라이언트](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "VPN 클라이언트")

## <a name="cc"></a>6부 - 클라이언트 인증서 생성

그 다음 클라이언트 인증서를 생성합니다. 연결할 각 클라이언트의 고유한 인증서를 생성할 수도 있고 여러 클라이언트에서 동일한 인증서를 사용할 수도 있습니다. 고유한 클라이언트 인증서를 생성하면 필요할 경우 단일 인증서를 해지할 수 있는 장점이 있습니다. 그렇지 않은 경우 즉, 모든 구성원이 동일한 클라이언트 인증서를 사용하고 한 클라이언트에 대한 인증서를 해지해야 하는 경우, 인증서를 사용하는 모든 클라이언트에 대해 새 인증서를 생성하고 설치해야 합니다.

- 엔터프라이즈 인증서 솔루션을 사용하는 경우, NetBIOS '도메인\\사용자 이름' 형식이 아닌 일반 이름 값 형식 'name@yourdomain.com'으로 클라이언트 인증서를 생성합니다.

- 자체 서명된 인증서를 사용하는 경우 [지점 및 사이트 간 구성에 대한 자체 서명된 루트 인증서로 작업](vpn-gateway-certificates-point-to-site.md)을 참조하여 클라이언트 인증서를 생성합니다.

## 7부 - 클라이언트 인증서 설치

가상 네트워크에 연결하려는 각 컴퓨터에 클라이언트 인증서를 설치합니다. 인증하려면 클라이언트 인증서가 필요합니다. 클라이언트 인증서를 자동으로 설치하거나 수동으로 설치할 수 있습니다. 다음 단계에서는 클라이언트 인증서를 내보내어 수동으로 설치하는 방법을 안내합니다.

1. 클라이언트 인증서를 내보내려면 *certmgr.msc*를 사용할 수 있습니다. 내보낼 클라이언트 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 클릭 한 다음 **내보내기**를 클릭합니다.
2. 개인 키로 클라이언트 인증서를 내보냅니다. 이는 *.pfx* 파일입니다. 이 인증서에 대해 설정한 암호(키)를 기억 하거나 기록해야 합니다.
3. *.pfx* 파일을 클라이언트 컴퓨터에 복사합니다. 클라이언트 컴퓨터에서 *.pfx* 파일을 두 번 클릭하여 설치합니다. 요청하는 경우 암호를 입력합니다. 설치 위치를 수정하지 마세요.


## 8부 - Azure에 연결

1. VNet에 연결하려면 클라이언트 컴퓨터에서 VPN 연결로 이동하고 만든 VPN 연결을 찾습니다. 가상 네트워크와 같은 이름이 지정됩니다. **Connect**를 클릭합니다. 인증서 사용을 안내하는 팝업 메시지가 나타날 수 있습니다. 이 경우 **계속**을 클릭하여 상승된 권한을 사용합니다.

2. **연결** 상태 페이지에서 **연결**을 클릭하여 연결을 시작합니다. **인증서 선택** 화면에서 표시되는 클라이언트 인증서가 연결하는 데 사용할 인증서인지 확인합니다. 그렇지 않은 경우 드롭다운 화살표를 사용하여 올바른 인증서를 선택한 다음 **확인**을 클릭합니다.

	![VPN 클라이언트 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "VPN 클라이언트 연결")

3. 이제 연결이 설정됩니다.

	![VPN 클라이언트 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "VPN 클라이언트 연결 2")

## 9부 - 연결 확인

1. VPN 연결이 활성인지를 확인하려면, 관리자 권한 명령 프롬프트를 열고 *ipconfig/all*을 실행합니다.

2. 결과를 확인합니다. 받은 IP 주소가 구성에 지정한 지점 및 사이트 VPN 클라이언트 주소 풀 내의 주소 중 하나인지 확인합니다. 결과는 다음과 같아야 합니다.
    
		PPP adapter VNet1:
			Connection-specific DNS Suffix .:
			Description.....................: VNet1
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## 신뢰할 수 있는 루트 인증서를 추가 또는 제거하려면

인증서는 지점 및 사이트 간 VPN에 대한 VPN 클라이언트를 인증하는 데 사용됩니다. 다음 단계는 루트 인증서를 추가하거나 제거하는 과정을 안내합니다. Azure에 Base64 인코딩 X.509(.cer) 파일을 추가할 때 파일이 나타내는 루트 인증서를 신뢰하도록 Azure에 지시합니다.

PowerShell을 사용하거나 Azure 포털에서 신뢰할 수 있는 루트 인증서를 추가하거나 제거할 수 있습니다. Azure 포털을 사용하여 수행하려면 **가상 네트워크 게이트웨이 > 설정 > 지점 및 사이트 구성 > 루트 인증서**로 이동합니다. 다음 단계에서는 PowerShell을 사용하여 이러한 작업을 살펴봅니다.

### 신뢰할 수 있는 루트 인증서 추가

Azure에 최대 20개의 신뢰할 수 있는 루트 인증서 .cer 파일을 추가할 수 있습니다. 아래 단계를 따라 루트 인증서를 추가합니다.

1. Azure에 추가할 새 루트 인증서를 만들고 준비합니다. Base-64로 인코딩된 X.509(.CER)로 공개 키를 내보내고 텍스트 편집기로 엽니다. 아래에 표시된 섹션만 복사합니다.
 
	다음 예제와 같이 값을 복사합니다.

	![인증서](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "인증서")
	
2. 인증서 이름 및 키 정보를 변수로 지정합니다. 다음 예제에 나와 있는 것처럼 정보를 고유한 정보로 바꿉니다.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. 새 루트 인증서를 추가합니다. 한번에 하나의 인증서만 추가할 수 있습니다.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. 다음 cmdlet을 사용하여 새 인증서가 올바르게 추가되었는지 확인할 수 있습니다.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

### 신뢰할 수 있는 루트 인증서를 제거하려면

Azure에서 신뢰할 수 있는 루트 인증서를 제거할 수 있습니다. 신뢰할 수 있는 인증서를 제거하면 루트 인증서에서 생성된 클라이언트 인증서는 더 이상 지점 및 사이트 간을 통해 Azure에 연결할 수 없습니다. 클라이언트를 연결하려는 경우 Azure에서 신뢰할 수 있는 인증서에서 생성된 새 클라이언트 인증서를 설치해야 합니다.

1. 신뢰할 수 있는 루트 인증서를 제거하려면 다음 샘플을 수정합니다.

	변수를 선언합니다.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. 인증서 제거

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. 다음 cmdlet을 사용하여 인증서가 성공적으로 제거되었는지 확인합니다.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

## 해지된 클라이언트 인증서 목록을 관리하려면

클라이언트 인증서를 해지할 수 있습니다. 인증서 해지 목록에서 개별 클라이언트 인증서를 기반으로 하는 지점 및 사이트 간 연결을 선택적으로 거부할 수 있습니다. Azure에서 루트 인증서 .cer를 제거하면, 해지된 루트 인증서에 의해 생성/서명된 모든 클라이언트 인증서에 대한 액세스 권한도 해지됩니다. 루트가 아닌 특정 클라이언트 인증서를 해지해도 이를 수행할 수 있습니다. 이런 방식은 루트 인증서에서 생성된 다른 인증서에도 여전히 유효합니다. 해지된 클라이언트 인증서를 사용하는 동안 개별 사용자의 세분화된 액세스 제어를 위해 일반적으로 루트 인증서를 사용하여 팀 또는 조직 수준에서 액세스를 관리합니다.

### 클라이언트 인증서 해지

1. 해지할 클라이언트 인증서의 지문을 가져옵니다.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. 해지된 지문 목록에 지문을 추가합니다.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. 지문이 인증서 해지 목록에 추가되었는지 확인합니다. 한번에 하나의 지문을 추가해야 합니다.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### 클라이언트 인증서 복구

해지된 클라이언트 인증서 목록에서 지문을 제거하여 클라이언트 인증서를 복구할 수 있습니다.

1.  해지된 클라이언트 인증서 지문 목록에서 지문을 제거합니다.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. 해지된 목록에서 지문이 제거되었는지 확인합니다.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## 다음 단계

가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 단계는 [가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->