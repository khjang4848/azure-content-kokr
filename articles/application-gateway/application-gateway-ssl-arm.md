<properties
   pageTitle="Azure 리소스 관리자를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성 | Microsoft Azure"
   description="이 페이지에서는 Azure Resource Manager를 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# Azure Resource Manager를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

 Azure 응용 프로그램 게이트웨이 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트 엔드 서버 설치 및 웹 응용 프로그램의 관리를 간소화합니다.


## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. [다운로드 페이지](https://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

## 응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?


- **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다.
- **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
- **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 백 엔드 서버 풀을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.

**추가 구성 정보**

SSL 인증서 구성에서 **HttpListener**의 프로토콜은 *Https*(대/소문자 구분)로 바꿔야 합니다. **SslCertificate** 요소는 SSL 인증서에 대해 구성된 변수 값으로 **HttpListener**에 추가됩니다. 프런트 엔드 포트는 443으로 업데이트되어야 합니다.

**쿠키 기반 선호도를 사용하도록 설정**: 응용 프로그램 게이트웨이는 클라이언트 세션의 요청이 항상 웹 팜에 있는 동일한 VM으로 전송되도록 구성될 수 있습니다. 게이트웨이에서 트래픽을 적절하게 지시할 수 있는 세션 쿠키를 삽입하면 이 시나리오가 완료됩니다. 쿠키를 기반 선호도를 사용하려면 **BackendHttpSettings** 요소에서 **CookieBasedAffinity**를 *Enabled*로 설정합니다.


## 응용 프로그램 게이트웨이 만들기

Azure 클래식 배포 모델 및 Azure Resource Manager 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

Resource Manager를 사용하면 응용 프로그램 게이트웨이의 모든 구성 요소가 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.


다음은 응용 프로그램 게이트웨이를 만드는 데 필요한 단계입니다.

1. 리소스 관리자에 대한 리소스 그룹 만들기
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP 만들기
3. 응용 프로그램 게이트웨이 구성 개체 만들기
4. 응용 프로그램 게이트웨이 리소스 만들기


## 리소스 관리자에 대한 리소스 그룹 만들기

Azure 리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell](../powershell-azure-resource-manager.md) 사용을 참조하세요.

### 1단계

	Login-AzureRmAccount

### 2단계

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 3단계

사용할 Azure 구독을 선택합니다. <BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 설정은 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 Resource Manager를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### 1단계

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

이 샘플은 주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

### 2단계

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

이 샘플은 접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

### 3단계

	$subnet = $vnet.Subnets[0]

이 샘플은 다음 단계를 위해 $subnet 변수에 서브넷 개체를 할당합니다.

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

이 샘플은 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 공용 IP 리소스 "publicIP01"을 만듭니다.


## 응용 프로그램 게이트웨이 구성 개체 만들기

### 1단계

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

이 샘플은 "gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

### 2단계

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

이 샘플은 IP 주소가 "134.170.185.46, 134.170.188.221,134.170.185.50"인 "pool01"이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 값은 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신하는 IP 주소입니다. 앞의 예제에서 IP 주소를 웹 응용 프로그램 끝점의 IP 주소로 대체합니다.

### 3단계

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

이 샘플은 백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01"을 구성합니다.

### 4단계

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

이 샘플은 공용 IP 끝점에 대해 "frontendport01"이라는 프런트 엔드 IP 포트를 구성합니다.

### 5단계

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

이 샘플은 SSL 연결에 사용된 인증서를 구성합니다. 인증서는 .pfx 형식으로 4~12자 사이의 암호이어야 합니다.

### 6단계

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

이 샘플은 "fipconfig01"이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.

### 7단계

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


이 샘플은 "listener01"이라는 수신기를 만들고 프런트 엔드 IP 구성 및 인증서에 프런트 엔드 포트를 연결합니다.

### 8단계

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

이 샘플은 "rule01"이라는 부하 분산 장치 라우팅 규칙을 만들고 부하 분산 장치 동작을 구성합니다.

### 9단계

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

이 샘플은 응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

>[AZURE.NOTE]  *InstanceCount*의 기본값은 2이고, 최대값은 10입니다. *GatewaySize*의 기본값은 보통입니다. Standard\_Small, Standard\_Medium 및 Standard\_Large 간에 선택할 수 있습니다.

## New-AzureApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

이 샘플은 이전 단계의 모든 구성 항목을 가진 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 "appgwtest"라고 합니다.

## 다음 단계

ILB(내부 부하 분산 장치)에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->