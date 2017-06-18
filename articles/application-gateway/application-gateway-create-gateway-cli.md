<properties
   pageTitle="Resource Manager에서 Azure CLI를 사용하여 Application Gateway 만들기 | Microsoft Azure"
   description="Resource Manager에서 Azure CLI를 사용하여 Application Gateway를 만드는 방법을 알아봅니다."
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace" />

# Azure CLI를 사용하여 Application Gateway 만들기

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램 게이트웨이의 응용 프로그램 전달 기능에는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 등이 있습니다.

> [AZURE.SELECTOR]
- [Azure 포털](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

## 필수 조건: Azure CLI 설치

이 문서의 단계를 수행하려면 [Mac, Linux 및 Windows에 대한 Azure 명령줄 인터페이스(Azure CLI)를 설치](../xplat-cli-install.md)하고 [Azure에 로그온](../xplat-cli-connect.md)해야 합니다.

> [AZURE.NOTE] Azure 계정이 없는 경우 계정이 필요합니다. [여기서 무료 평가판](../active-directory/sign-up-organization.md)에 등록합니다.

## 시나리오

이 시나리오에서는 Azure 포털을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.

이 시나리오에서는 다음을 수행합니다.

- 두 인스턴스를 사용하여 중간 응용 프로그램 게이트웨이를 만듭니다.
- 예약된 CIDR 블록이 10.0.0.0/16이고 이름이 AdatumAppGatewayVNET인 가상 네트워크를 만듭니다.
- CIDR 블록으로 10.0.0.0/28을 사용하는 Appgatewaysubnet이라고 하는 서브넷을 만듭니다.
- SSL 오프로드용 인증서를 구성합니다.

![예제 시나리오][scenario]

>[AZURE.NOTE] 초기 배포 중이 아닌 경우 응용 프로그램 게이트웨이를 구성하면 사용자 지정 상태 프로브, 백 엔드 풀 주소, 추가 규칙 등 응용 프로그램 게이트웨이에 대한 추가 구성이 구성됩니다.

## 시작하기 전에

Azure 응용 프로그램 게이트웨이에는 자체 서브넷이 필요합니다. 가상 네트워크를 만들 때 여러 서브넷을 둘 수 있는 충분한 주소 공간이 있는지 확인합니다. Application Gateway를 서브넷에 배포한 경우 추가 Application Gateway를 서브넷에 추가할 수 있습니다.

## Azure에 로그인

**Microsoft Azure 명령 프롬프트**를 열고 로그인합니다.

    azure login

앞의 예제를 입력하면 코드가 제공됩니다. 브라우저에서 https://aka.ms/devicelogin(으)로 이동하여 로그인 프로세스를 계속합니다.

![장치 로그인을 보여 주는 cmd][1]

브라우저에서 받은 코드를 입력합니다. 로그인 페이지로 리디렉션됩니다.

![코드를 입력하는 브라우저][2]

로그인한 코드가 입력된 후 브라우저를 닫아 시나리오를 계속합니다.

![성공적으로 로그인][3]

## Resource Manager 모드로 전환합니다.

    azure config mode arm

## 리소스 그룹 만들기

Application Gateway를 만들기 전에 리소스 그룹이 Application Gateway를 포함하도록 만들어집니다. 다음은 명령을 표시합니다.

    azure group create -n AdatumAppGatewayRG -l eastus

## 가상 네트워크 만들기

리소스 그룹을 만든 후에 Application Gateway에 가상 네트워크가 만들어집니다. 다음 예제에서 주소 공간은 이전 시나리오 노트에 정의된 대로 10.0.0.0/16이었습니다.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## 서브넷 만들기

가상 네트워크를 만든 후에 Application Gateway에 서브넷이 추가됩니다. Application Gateway와 동일한 가상 네트워크에서 호스팅된 웹앱을 포함한 Application Gateway를 사용하려는 경우 다른 서브넷을 위해 충분한 공간을 남겨야 합니다.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## Application Gateway 만들기

가상 네트워크와 서브넷을 만들면 Application Gateway에 대한 필수 구성 요소가 완료됩니다. 또한 이전에 내보낸 .pfx 인증서 및 인증서의 암호는 다음 단계에 필요합니다. 백 엔드에 사용되는 IP 주소는 백 엔드 서버에 대한 IP 주소입니다. 이 값은 가상 네트워크의 개인 IP, 공용 IP 또는 백 엔드 서버의 정규화된 도메인 이름일 수 있습니다.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard



이 예제에서는 수신기, 백 엔드 풀, 백 엔드 http 설정 및 규칙에 대한 기본 설정으로 기본 Application Gateway를 만듭니다. 또한 SSL 오프로드를 구성합니다. 프로비전에 성공하면 배포에 맞게 이러한 설정을 수정할 수 있습니다. 이전 단계에서 백 엔드 풀로 정의된 웹 응용 프로그램이 이미 있는 경우 만들어지면 부하 분산이 시작됩니다.

## 다음 단계

[사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)를 방문하여 사용자 지정 상태 프로브를 만드는 방법을 알아봅니다.

[SSL 오프로드 구성](application-gateway-ssl-arm.md)을 방문하여 SSL 오프로드를 구성하고 웹 서버에서 비용이 드는 SSL 암호 해독을 제거하는 방법을 알아봅니다.

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

<!---HONumber=AcomDC_0921_2016-->