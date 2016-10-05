<properties
   pageTitle="여러 사이트를 호스트하는 응용 프로그램 게이트웨이 만들기 | Microsoft Azure"
   description="이 페이지에서는 동일한 게이트웨이에서 여러 웹 응용 프로그램을 호스트하는 Azure 응용 프로그램 게이트웨이를 만들고 구성하는 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>


# 여러 웹 응용 프로그램을 호스트하는 응용 프로그램 게이트웨이 만들기

다중 사이트 호스팅을 통해 둘 이상의 웹 응용 프로그램을 동일한 응용 프로그램 게이트웨이에 배포할 수 있습니다. 들어오는 HTTP 요청의 호스트 헤더 존재 여부에 따라 트래픽을 수신할 수신기가 결정됩니다. 그런 다음 수신기는 게이트웨이 규칙 정의에 구성된 대로 적절한 백 엔드 풀로 트래픽을 전달합니다. SSL 사용 웹 응용 프로그램에서 응용 프로그램 게이트웨이는 SNI(서버 이름 표시) 확장에 의존하여 웹 트래픽에 대한 올바른 수신기를 선택합니다.

다중 사이트 호스팅의 일반적인 용도는 여러 웹 도메인에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다. 마찬가지로 같은 루트 도메인의 여러 하위 도메인을 동일한 응용 프로그램 게이트웨이에서 호스트할 수도 있습니다.

## 시나리오
다음 예제에서 응용 프로그램 게이트웨이는 두 개의 백 엔드 서버 풀(contoso 서버 풀 및 fabrikam 서버 풀)을 통해 contoso.com 및 fabrikam.com에 대한 트래픽을 처리합니다. 유사한 설정을 사용하여 app.contoso.com 및 blog.contoso.com과 같은 하위 도메인을 호스트할 수 있습니다.


## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. [다운로드 페이지](https://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 백 엔드 풀에 추가된 서버가 존재하거나 가상 네트워크 또는 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.



## 응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?


- **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다. FQDN을 사용할 수도 있습니다.
- **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다. 다중 사이트 지원 응용 프로그램 게이트웨이의 경우 호스트 이름 및 SNI 표시도 추가됩니다.
- **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 전달되어야 하는 백 엔드 서버 풀을 정의합니다.

## 응용 프로그램 게이트웨이 만들기

다음은 응용 프로그램 게이트웨이를 만드는 데 필요한 단계입니다.

1. 리소스 관리자에 대한 리소스 그룹을 만듭니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP를 만듭니다.
3. 응용 프로그램 게이트웨이 구성 개체를 만듭니다.
4. 응용 프로그램 게이트웨이 리소스를 만듭니다.

## 리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### 1단계

Azure에 로그인

	Login-AzureRmAccount

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### 2단계

계정에 대한 구독을 확인합니다.

	Get-AzureRmSubscription

### 3단계

사용할 Azure 구독을 선택합니다.

	Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### 4단계

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    New-AzureRmResourceGroup -Name appgw-RG -location "East Asia"

또는 응용 프로그램 게이트웨이의 리소스 그룹에 대한 태그를 만들 수도 있습니다.
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "East Asia" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"} 

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "appgw-RG"라는 리소스 그룹과 "East Asia"라는 위치를 만들었습니다.

>[AZURE.NOTE] 응용 프로그램 게이트웨이에 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md)을 확인합니다.

## 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

### 1단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 2단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 "appgw-rg"에서 "appgwvnet"이라는 가상 네트워크를 만듭니다.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "East Asia" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 3단계

응용 프로그램 게이트웨이를 만드는 다음 단계에 서브넷 변수를 할당합니다.

	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -VirtualNetwork $vnet

## 프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 "appgw-rg"에서 공용 IP 리소스 "publicIP01"을 만듭니다.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "East Asia" -AllocationMethod Dynamic

서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## 응용 프로그램 게이트웨이 구성 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### 1단계

"gatewayIP01"이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 2단계

IP 주소가 "10.0.0.100, 10.0.0.101,10.0.0.102"인 "pool1"과 IP 주소가 "10.0.0.103, 10.0.0.104, 10.0.0.105"인 "pool2"라는 백 엔드 IP 주소 풀을 구성합니다.


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.100, 10.0.0.101,10.0.0.102 
	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.0.103, 10.0.0.104, 10.0.0.105

이 예제에서는 요청된 사이트를 기반으로 네트워크 트래픽을 라우팅하는 두 개의 백 엔드 풀이 있습니다. 하나의 풀은 사이트 "contoso.com"에서 트래픽을 수신하고 또 다른 풀은 사이트 "fabrikam.com"에서 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 이전 IP 주소를 교체해야 합니다. 내부 IP 주소 대신 공용 IP 주소, FQDN 또는 VM의 NIC를 백 엔드 인스턴스에 사용할 수도 있습니다. IP 대신 FQDN을 지정하려면 PowerShell에서 "-BackendFQDNs" 매개 변수를 사용합니다.

### 3단계

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 "poolsetting01" 및 "poolsetting02"를 구성합니다. 이 예제에서는 백 엔드 풀에 대한 다른 백 엔드 풀 설정을 구성합니다. 각 백 엔드 풀에는 고유한 백 엔드 풀 설정이 있을 수 있습니다.


	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### 4단계

공용 IP 끝점으로 프런트 엔드 IP를 구성합니다.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### 5단계

응용 프로그램 게이트웨이에 대한 프런트 엔드 포트를 구성합니다.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### 6단계

이 예제에서 지원하려는 두 웹 사이트에 대해 두 개의 SSL 인증서를 구성합니다. 인증서 하나는 contoso.com 트래픽용이고, 나머지는 fabrikam.com 트래픽용입니다. 이러한 인증서는 사용자 웹 사이트용으로 인증 기관에서 발급한 인증서여야 합니다. 자체 서명된 인증서는 지원되지만 프로덕션 트래픽에 권장되지 않습니다.

	$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name  contosocert -CertificateFile <file path> -Password <password>
	$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### 7단계

이 예제의 두 웹 사이트에 대한 두 개의 수신기를 구성합니다. 이 단계에서는 들어오는 트래픽을 수신하는 데 사용되는 공용 IP 주소, 포트 및 호스트에 대한 수신기를 구성합니다. HostName 매개 변수는 다중 사이트 지원에 필요하며 트래픽이 수신되는 적절한 웹 사이트로 설정되어야 합니다. RequireServerNameIndication 매개 변수는 다중 호스트 시나리오에서 SSL 지원이 필요한 웹 사이트에 대해 true로 설정되어야 합니다. SSL 지원이 필요한 경우 해당 웹 응용 프로그램에 대한 트래픽의 보안을 유지하는 데 사용되는 SSL 인증서도 지정해야 합니다. FrontendIPConfiguration, FrontendPort 및 HostName의 조합은 수신기에 고유해야 합니다. 각 수신기는 하나의 인증서를 지원할 수 있습니다.

	$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
	$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### 8단계

이 예제의 두 웹 응용 프로그램에 대한 두 개의 규칙 설정을 만듭니다. 규칙은 수신기, 백 엔드 풀 및 http 설정을 연결합니다. 이 단계에서는 각 웹 사이트에 기본 라우팅 규칙을 사용하도록 응용 프로그램 게이트웨이를 구성합니다. 각 웹 사이트의 트래픽은 구성된 해당 수신기에서 수신된 후 BackendHttpSettings에 지정된 속성을 사용하여 구성된 해당 백 엔드 풀로 전송됩니다.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
	$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### 9단계

응용 프로그램 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## 응용 프로그램 게이트웨이 만들기

이전 단계의 모든 구성 개체로 응용 프로그램 게이트웨이를 만듭니다.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "East Asia" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02 	


>[AZURE.IMPORTANT] 응용 프로그램 게이트웨이 프로비전은 장기 실행 작업이며 완료하는 데 다소 시간이 걸릴 수 있습니다.

## 응용 프로그램 게이트웨이 DNS 이름 가져오기

응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름은 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용됩니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.
	
	Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01
		
	Name                     : publicIP01
	ResourceGroupName        : appgw-RG
	Location                 : eastasia
	Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
	ResourceGuid             : 00000000-0000-0000-0000-000000000000
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : xx.xx.xxx.xx
	PublicIpAddressVersion   : IPv4
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
	                           Configurations/frontend1"
	                           }
	DnsSettings              : {
	                             "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
	                           }

<!---HONumber=AcomDC_0921_2016-->