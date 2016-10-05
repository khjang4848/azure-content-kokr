<properties
   pageTitle="클래식 배포를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성 | Microsoft Azure"
   description="이 문서에서는 Azure 클래식 배포 모델을 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# 클래식 배포 모델을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Azure 응용 프로그램 게이트웨이 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트 엔드 서버 설치 및 웹 응용 프로그램의 관리를 간소화합니다.


## 시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. [다운로드 페이지](https://azure.microsoft.com/downloads/)의 **Windows PowerShell** 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 유효한 서브넷과 작업 가상 네트워크가 있는지 확인합니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

응용 프로그램 게이트웨이에서 SSL 오프로드를 구성하려면 다음 단계를 나열된 순서대로 수행합니다.

1. [응용 프로그램 게이트웨이 만들기](#create-an-application-gateway)
2. [SSL 인증서를 업로드 합니다.](#upload-ssl-certificates)
3. [게이트웨이 구성](#configure-the-gateway)
4. [게이트웨이 구성 설정](#set-the-gateway-configuration)
5. [게이트웨이 시작](#start-the-gateway)
6. [게이트웨이 상태를 확인합니다.](#verify-the-gateway-status)


## 응용 프로그램 게이트웨이 만들기

게이트웨이를 만들려면 **New-AzureApplicationGateway** cmdlet을 사용하여 해당 값을 원하는 값으로 바꿉니다. 게이트웨이에 대한 청구는 이 시점에서 시작되지 않습니다. 게이트웨이가 성공적으로 작동되면, 요금청구가 시작됩니다.

	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

생성된 게이트웨이의 유효성을 검사하려면 **Get-AzureApplicationGateway** cmdlet을 사용합니다.

이 샘플에서 *Description*, *InstanceCount* 및 *GatewaySize*는 선택적 매개 변수입니다. *InstanceCount*에 대한 기본값은 2이고, 최대값은 10입니다. *GatewaySize*에 대한 기본값은 보통입니다. 크고 작은 다른 사용 가능한 값이 됩니다. 게이트웨이가 아직 시작되지 않았으므로 *VirtualIPs* 및 *DnsName*이 빈 값으로 표시됩니다. 이 값들은 게이트웨이가 실행 상태가 되면 생성됩니다.

	Get-AzureApplicationGateway AppGwTest

## SSL 인증서를 업로드 합니다.

**Add-AzureApplicationGatewaySslCertificate**를 사용하여 응용 프로그램 게이트웨이에 *pfx* 형식의 서버 인증서를 업로드합니다. 인증서 이름은 사용자가 선택해야 하고 응용 프로그램 게이트웨이 내에서 고유해야 합니다. 이 인증서는 응용 프로그램 게이트웨이에 대한 모든 인증서 관리작업에 이름이 참조됩니다.

이 다음 예제에서는 cmdlet을 보여 주고 사용자 고유의 샘플 값으로 대체 합니다.

	Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

그런 다음 인증서 업로드 유효성을 검사 합니다. **Get-AzureApplicationGatewayCertificate** cmdlet을 사용합니다.

이 출력 다음 샘플에서는 첫 줄에 cmdlet을 보여줍니다.

	Get-AzureApplicationGatewaySslCertificate AppGwTest

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert
	SubjectName    : CN=gwcert.app.test.contoso.com
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned

>[AZURE.NOTE] 인증서 암호는 4~12자의 문자 또는 숫자여야 합니다. 특수 문자는 허용되지 않습니다.

## 게이트웨이 구성

응용 프로그램 게이트웨이는 여러 값으로 구성됩니다. 값은 연결되어 구성을 만듭니다.

값은 다음과 같습니다.

- **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다.
- **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
- **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
- **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
- **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 백 엔드 서버 풀을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.

**추가 구성 정보**

SSL 인증서 구성에서 **HttpListener**의 프로토콜은 *Https*(대/소문자 구분)로 바꿔야 합니다. **SslCert** 요소는 이전 SSL 인증서 섹션의 업로드에 사용된 것과 동일한 이름으로 값을 설정하여 **HttpListener**에 추가됩니다. 프런트 엔드 포트는 443으로 업데이트되어야 합니다.

**쿠키 기반 선호도를 사용하도록 설정**: 응용 프로그램 게이트웨이는 클라이언트 세션의 요청이 항상 웹 팜에 있는 동일한 VM으로 전송되도록 구성될 수 있습니다. 게이트웨이에서 트래픽을 적절하게 지시할 수 있는 세션 쿠키를 삽입하면 이 시나리오가 완료됩니다. 쿠키를 기반 선호도를 사용하려면 **BackendHttpSettings** 요소에서 **CookieBasedAffinity**를 *Enabled*로 설정합니다.



구성 개체를 만들거나, 구성 XML 파일을 사용하여 구성을 생성할 수 있습니다. 구성 XML 파일을 사용하여 구성을 생성하려면 다음 샘플을 사용합니다.

**구성 XML 샘플**

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


## 게이트웨이 구성 설정

다음으로, 응용 프로그램 게이트웨이를 설정합니다. **Set-AzureApplicationGatewayConfig** cmdlet을 구성 XML 파일 또는 구성 개체와 함께 사용할 수 있습니다.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## 게이트웨이 시작

게이트웨이가 구성되면 **Start-AzureApplicationGateway** cmdlet을 사용하여 게이트웨이를 시작합니다. 응용 프로그램 게이트웨이에 대한 청구는 게이트웨이가 성공적으로 작동된 후 시작합니다.


**알림:** **Start-AzureApplicationGateway** cmdlet은 완료하는 데 최대 15-20분까지 걸릴 수 있습니다.

	Start-AzureApplicationGateway AppGwTest

## 게이트웨이 상태를 확인합니다.

**Get-AzureApplicationGateway** cmdlet을 사용하여 게이트웨이의 상태를 확인합니다. **Start-AzureApplicationGateway**가 이전 단계에서 성공한 경우 *상태*가 실행 중이어야 하고, *VirtualIPs*와 *DnsName*에 유효한 항목이 있어야 합니다.

이 샘플에서는 응용 프로그램 게이트웨이가 시작, 실행 그리고 트래픽을 받을 준비가 된 것을 보여 줍니다.

	Get-AzureApplicationGateway AppGwTest

	Name          : AppGwTest2
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## 다음 단계


보다 자세한 내용을 원한다면 일반적 부하 분산 옵션을 참조:

- [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0921_2016-->