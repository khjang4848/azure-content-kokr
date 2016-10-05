<properties
   pageTitle="포털을 사용하여 응용 프로그램 게이트웨이 만들기 | Microsoft Azure"
   description="포털을 사용하여 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다."
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

# 포털을 사용하여 응용 프로그램 게이트웨이 만들기

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다. 응용 프로그램 게이트웨이는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC(Application Delivery Controller)를 제공합니다. 지원되는 기능의 전체 목록을 찾으려면 [Application Gateway 개요](application-gateway-introduction.md)를 방문하세요.

> [AZURE.SELECTOR]
- [Azure 포털](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

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

## 응용 프로그램 게이트웨이 만들기

### 1단계

Azure 포털로 이동하여 **새로 만들기** > **네트워킹** > **응용 프로그램 게이트웨이**를 클릭합니다.

![Application Gateway 만들기][1]

### 2단계

응용 프로그램 게이트웨이에 대한 기본 정보를 입력합니다. 완료되면 **확인**을 클릭합니다.

기본 설정에 필요한 정보는 다음과 같습니다.

- **이름** - 응용 프로그램 게이트웨이의 이름입니다.
- **SKU 크기** - 이 설정은 Application Gateway의 크기입니다. 사용 가능한 옵션은 소형, 중형 및 대형입니다.
- **인스턴스 수** - 인스턴스 수입니다. 이 값은 2에서 10 사이의 숫자여야 합니다.
- **리소스 그룹** - Application Gateway를 유지할 리소스 그룹입니다. 기존 리소스 그룹 또는 새 리소스 그룹일 수 있습니다.
- **위치** - Application Gateway의 지역입니다. 리소스 그룹에서의 위치와 같은 위치입니다. *위치는 가상 네트워크와 공용 IP가 게이트웨이와 동일한 위치에 있어야 하므로 중요합니다*.

![기본 설정이 표시된 블레이드][2]

>[AZURE.NOTE] 테스트 목적으로 인스턴스 수 1을 선택할 수 있습니다. 그러나 두 개 미만의 인스턴스 수는 SLA에서 다루지 않으므로 권장되지 않는다는 점을 알아야 합니다. 작은 게이트웨이는 개발 테스트용이며 프로덕션용으로는 사용되지 않습니다.

### 3단계

기본 설정을 정의한 후에는 사용할 가상 네트워크를 정의합니다. 가상 네트워크는 Application Gateway에서 부하를 분산하는 응용 프로그램을 수용합니다.

**가상 네트워크 선택**을 클릭하여 가상 네트워크를 구성합니다.

![응용 프로그램 게이트웨이에 대한 설정이 표시된 블레이드][3]

### 4단계

*가상 네트워크 선택* 블레이드에서 **새로 만들기**를 클릭합니다.

이 시나리오에는 설명되어 있지 않지만 여기서 기존 Virtual Network를 선택할 수도 있습니다. 기존 가상 네트워크를 사용하는 경우 해당 가상 네트워크에 사용할 빈 서브넷 또는 Application Gateway 리소스의 서브넷이 필요합니다.

![가상 네트워크 선택 블레이드][4]

### 5단계

위 [시나리오](#scenario) 설명에 따라 **가상 네트워크 만들기** 블레이드에서 네트워크 정보를 입력합니다.

![정보가 입력된 가상 네트워크 만들기 블레이드][5]

### 6단계

가상 네트워크를 만든 후에는 응용 프로그램 게이트웨이에 대한 프런트 엔드 IP를 정의합니다. 이 시점에서는 프런트 엔드에 대해 공용 또는 개인 IP 주소를 선택합니다. 응용 프로그램이 인터넷 연결용인지 내부용인지에 따라 선택이 달라집니다. 이 시나리오에서는 공용 IP 주소를 사용하는 것으로 가정합니다. 개인 IP 주소를 선택하려면 **개인** 단추를 클릭하면 됩니다. 자동으로 할당된 IP 주소가 선택되거나, **특정 개인 IP 주소 선택** 확인란을 클릭하여 수동으로 입력할 수 있습니다.

### 7단계

**공용 IP 주소 선택**을 클릭합니다. 기존 공용 IP 주소를 사용할 수 있는 경우 이 시점에서 선택될 수 있지만 이 시나리오에서는 새 공용 IP 주소를 만듭니다. **새로 만들기**를 클릭합니다.

![공용 IP 주소 선택 블레이드][6]

### 8단계

공용 IP 주소의 이름을 지정하고 **확인**을 클릭합니다.

![공용 IP 주소 만들기 블레이드][7]

### 9단계

응용 프로그램 게이트웨이를 만들 때 구성할 마지막 설정은 수신기 구성입니다. **http**가 사용되는 경우 구성할 항목이 더 이상 없으므로 **확인**을 클릭하면 됩니다. **https**를 사용하려면 추가 구성이 필요합니다.

**https**를 사용하려면 인증서가 필요합니다. 인증서의 개인 키가 필요하므로 인증서의 .pfx 내보내기 및 파일의 암호를 제공해야 합니다.


![설정 블레이드의 빈 수신기 구성 섹션][8]

### 10단계

**HTTPS**를 클릭한 다음 **PFX 인증서 업로드** 텍스트 상자 옆의 **폴더** 아이콘을 클릭합니다. 파일 시스템에서 .pfx 인증서 파일로 이동합니다. 선택한 후 인증서의 이름을 지정하고 .pfx 파일에 대한 암호를 입력합니다.

완료되면 **확인**을 클릭하여 응용 프로그램 게이트웨이에 대한 설정을 검토합니다.

![설정 블레이드의 수신기 구성 섹션][9]

### 11단계

요약 페이지를 검토하고 **확인**을 클릭합니다. 이제 응용 프로그램 게이트웨이가 만들어지고 대기 상태가 됩니다.

### 12단계

응용 프로그램 게이트웨이를 만든 후에는 포털에서 해당 응용 프로그램 게이트웨이로 이동하여 응용 프로그램 게이트웨이 구성을 계속합니다.

![응용 프로그램 게이트웨이 리소스 보기][10]

이러한 단계에서는 수신기, 백 엔드 풀, 백 엔드 http 설정 및 규칙에 대한 기본 설정으로 기본 Application Gateway를 만듭니다. 프로비전에 성공하면 배포에 맞게 이러한 설정을 수정할 수 있습니다.

## 다음 단계

[사용자 지정 상태 프로브 만들기](application-gateway-create-probe-portal.md)를 방문하여 사용자 지정 상태 프로브를 만드는 방법을 알아봅니다.

[SSL 오프로드 구성](application-gateway-ssl-portal.md)을 방문하여 SSL 오프로드를 구성하고 웹 서버에서 비용이 드는 SSL 암호 해독을 제거하는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

<!---HONumber=AcomDC_0921_2016-->