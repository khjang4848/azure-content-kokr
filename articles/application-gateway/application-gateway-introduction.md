<properties
   pageTitle="응용 프로그램 게이트웨이 소개 | Microsoft Azure"
   description="이 페이지는 게이트웨이 크기, HTTP 부하 분산, 쿠키 기반의 세션 선호도 및 SSL 오프로드를 비롯하여 응용 프로그램 게이트웨이 서비스 계층 7 부하 분산의 개요를 제공합니다."
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
   ms.date="09/06/2016"
   ms.author="gwallace"/>

# 응용 프로그램 게이트웨이 개요

Microsoft Azure Application Gateway는 계층 7 부하 분산을 기반으로 한 Azure 관리 HTTP 부하 분산 서비스를 제공합니다. 즉, 트래픽을 허용하여 작동하며 정의된 규칙에 따라 트래픽을 적절한 백 엔드 인스턴스에 라우팅합니다.

응용 프로그램 부하 분산을 통해 IT 관리자 및 개발자가 HTTP 프로토콜에 따라 네트워크 트래픽에 대한 라우팅 규칙을 만들 수 있습니다. 응용 프로그램 게이트웨이 서비스는 가용성이 높으며 데이터 통신을 사용합니다. SLA 및 가격 책정은 [SLA](https://azure.microsoft.com/support/legal/sla/) 및 [가격 책정](https://azure.microsoft.com/pricing/details/application-gateway/) 페이지를 참조하세요.

현재 Application Gateway는 다음 기능을 사용하여 계층 7 응용 프로그램 배달을 지원합니다.

- **HTTP 부하 분산** - 응Application Gateway의 기본 기능은 부하 분산을 제공하는 것입니다. 부하 분산은 계층 7에서 수행되고 HTTP 트래픽에 대해서만 사용됩니다.
- **쿠키 기반 세션 선호도** - 이 기능은 동일한 백 엔드에서 사용자 세션을 유지하려는 경우에 유용합니다. Application Gateway는 쿠키를 사용하여 사용자 세션에서 동일한 백 엔드에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이 기능은 항목이 사용자 세션에 대한 백 엔드 서버에 로컬로 저장된 경우에 특히 중요합니다.
- **[SSL(Secure Sockets Layer) 오프로드](application-gateway-ssl-arm.md)** - 이 기능은 웹 서버에서 HTTPS 트래픽을 해독하는 것과 같은 비용이 많이 드는 태스크를 담당합니다. 웹 서버는 Application Gateway에서 SSL 연결을 종료하고 암호화되지 않은 서버에 대한 요청을 전달하여 암호 해독에서 벗어납니다. Application Gateway는 응답을 클라이언트에 다시 보내기 전에 다시 암호화합니다. 이 기능은 백 엔드가 Azure에서 Application Gateway와 동일한 보안 가상 네트워크에 있는 경우에 유용합니다.
- **[URL 기반 콘텐츠 라우팅](application-gateway-url-route-overview.md)** - 이 기능은 다른 트래픽에 다른 백 엔드 서버를 사용하는 기능을 제공합니다. 웹 서버 또는 CDN에 있는 폴더에 대한 트래픽은 다른 백 엔드로 라우팅될 수 있으며 서버 특정 콘텐츠가 아닌 백 엔드에 대한 불필요한 로드를 감소시킵니다.
- **[다중 사이트 라우팅](application-gateway-multi-site-overview.md)** - Application Gateway를 사용하면 단일 Application Gateway에서 최대 20개의 웹 사이트를 통합할 수 있습니다.
- **[Websocket 지원](application-gateway-websocket.md)** - Application Gateway의 또 다른 장점은 Websocket에 대한 네이티브 지원이라는 점입니다.


## HTTP 계층 7 부하 분산

응용 프로그램 게이트웨이는 라우팅 규칙을 HTTP 트래픽에 적용하며 계층 7(HTTP) 부하 분산을 제공합니다. 응용 프로그램 게이트웨이를 만들 때 끝점(VIP)이 연결되며 수신 네트워크 트래픽에 대한 공용 IP로 사용됩니다. Azure는 전송 수준(TCP/UDP)에서 작동하며 응용 프로그램 게이트웨이 서비스로 모든 수신 네트워크 트래픽이 부하 분산되는 Azure Load Balancer를 통해 계층 4 부하 분산을 제공합니다. 응용 프로그램 게이트웨이는 가상 컴퓨터, 클라우드 서비스, 웹앱 또는 외부 IP 주소인 해당 구성에 따라 HTTP 트래픽을 라우팅합니다.

HTTP 계층 7 부하 분산은 다음에 유용합니다.

- 동일한 사용자/클라이언트 세션의 요청이 동일한 백 엔드 가상 컴퓨터에 도달해야 하는 응용 프로그램. 이러한 응용 프로그램의 예로는 쇼핑 카트 앱 및 웹 메일 서버가 있습니다.
- 응용 프로그램은 SSL 종료 오버헤드에서 웹 서버 팜을 무료로 원합니다.
- 콘텐츠 배달 네트워크와 같은 응용 프로그램은 다른 서버로 라우팅 또는 부하 분산을 위해 여러 HTTP 요청을 동일한 장기 실행 TCP 연결에서 요구합니다.
- websocket 트래픽을 지원하지만 모니터링에 대한 http 끝점을 포함하는 응용 프로그램입니다.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## 게이트웨이 크기 및 인스턴스

응용 프로그램 게이트웨이는 현재 소형, 중형 및 대형의 3가지 크기를 제공합니다. 소규모 인스턴스 크기는 개발 및 테스트 시나리오를 위해 사용 됩니다.

구독당 최대 50개의 응용 프로그램 게이트웨이를 만들 수 있으며 각 응용 프로그램 게이트웨이에는 최대 10개의 인스턴스가 있을 수 있습니다. Azure 관리 서비스와 비슷한 부하 분산 응용 프로그램 게이트웨이는 Azure 소프트웨어 부하 분산 뒤에서 계층 7 부하 분산 장치를 프로비전하는 것을 허용합니다.

다음 표에서는 각 응용 프로그램 게이트웨이 인스턴스의 평균 성능 처리량을 보여 줍니다.

| 백 엔드 페이지 응답 | 작음 | 중간 | 큼|
|---|---|---|---|
| 6K | 7\.5Mbps | 13Mbps | 50Mbps |
|100K | 35Mbps | 100Mbps| 200Mbps |

>[AZURE.NOTE] 이러한 값은 응용 프로그램 게이트웨이 처리량에 대한 대략적인 값입니다. 실제 처리량은 평균 페이지 크기, 백 엔드 인스턴스의 위치 및 페이지 처리 시간 등 다양한 환경 세부 사항에 따라 달라집니다.

## 상태 모니터링

Azure Application Gateway는 기본 및 사용자 지정 상태 프로브를 통해 백 엔드 인스턴스의 상태를 자동으로 모니터링합니다. 자세한 내용은 [응용 프로그램 게이트웨이 상태 모니터링 개요](application-gateway-probe-overview.md)를 참조하세요.

## 구성 및 관리

Azure Application Gateway는 구성된 경우 끝점으로 공용 IP, 개인 IP 또는 둘 다를 가질 수 있습니다. Application Gateway는 고유한 서브넷의 가상 네트워크 내에서 구성됩니다. Application Gateway에 대해 만들거나 사용된 서브넷은 다른 유형의 리소스를 포함할 수 없습니다. 그리고 서브넷에 허용되는 유일한 리소스는 다른 Application Gateway입니다.

REST API, PowerShell cmdlets, Azure CLI 또는 [Azure Portal](https://portal.azure.com/)을 사용하여 Application Gateway를 만들고 관리할 수 있습니다.

## 다음 단계

응용 프로그램 게이트웨이에 대해 학습한 후에 [응용 프로그램 게이트웨이를 만들](application-gateway-create-gateway-portal.md)거나 [응용 프로그램 게이트웨이 SSL 오프로드를 만들](application-gateway-ssl-arm.md)어서 HTTPS 연결의 부하를 분산할 수 있습니다.

URL 기반 콘텐츠 라우팅을 사용하여 응용 프로그램 게이트웨이를 만드는 방법에 대한 자세한 내용은 [URL 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-url-route-arm-ps.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->