<properties
   pageTitle="Express 경로 FAQ"
   description="Express 경로 FAQ는 Azure 서비스, 비용, 데이터 및 연결, SLA, 공급자 및 위치, 대역폭 및 추가 기술 세부 정보에 대한 정보를 포함합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/19/2016"
   ms.author="cherylmc"/>

# Express 경로 FAQ


## Express 경로란?
Express 경로는 온-프레미스 또는 공동 장소 환경의 Microsoft 데이터 센터와 인프라 사이에 개인 연결을 만들 수 있게 해 주는 서비스입니다. Express 경로 연결은 공용 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

### Express 경로 및 개인 네트워크 연결을 사용할 경우 이점은 무엇인가요?
Express 경로 연결은 공용 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 안정적이고 대기 시간이 짧고 일관적인 일관된 속도를 제공하며 보안성이 높습니다. 경우에 따라 온-프레미스 장치와 Azure 간 데이터 전송에 Express 경로 연결을 사용하면 상당한 비용 혜택을 얻을 수 있습니다.

### Express 경로를 통해 지원되는 Microsoft 클라우드 서비스는 무엇인가요?
Express 경로는 Office 365를 포함한 현재 대부분의 Microsoft Azure 서비스를 지원합니다. 일반 가용성에 관한 업데이트를 찾아보세요.

### 서비스를 사용할 수 있는 곳은 어디인가요?
서비스 위치 및 가용성은 [Express 경로 파트너 및 위치](expressroute-locations.md) 페이지를 참조하세요.

### Express 경로 통신 업체 중 하나와 파트너의 관계가 아닌 경우, Express 경로를 사용하여 Microsoft에 연결할 수 있는 방법이 있나요?
지역 통신 업체를 선택하고 지원되는 exchange 공급자 위치 중 하나에 이더넷 연결을 제공할 수 있습니다. 그러면 공급자 위치에서 Microsoft와 피어링할 수 있습니다. [Express 경로 파트너 및 위치](expressroute-locations.md)의 마지막 섹션을 검사하여 서비스 공급자가 Exchange 위치 중 하나에 있는지 확인합니다. 그런 다음 Azure에 연결하려면 서비스 공급자를 통해 Express 경로 회로를 주문할 수 있습니다.

### Express 경로 비용
가격 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/)를 참조하세요.

### 지정된 대역폭의 Express 경로 회로에 대한 비용을 지불하는 경우, 네트워크 서비스 공급자로부터 구입한 VPN 연결은 동일한 속도여야 하나요?
아니요. 서비스 공급자로부터 모든 속도의 VPN 연결을 구입할 수 있습니다. 그러나 Azure에 대한 연결은 구입할 Express 경로 회로 대역폭으로 제한됩니다.

### 지정된 대역폭의 Express 경로 회로에 대 한 비용을 지불하는 경우, 필요하다면 더 높은 속도까지 버스트할 수 있나요?
예. Express 경로 회로는 추가 비용 없이 확보된 대역폭 제한의 최대 2배까지 버스트할 수 있는 경우를 지원하도록 구성됩니다. 이 기능을 지원하는 경우 해당 서비스 공급자를 확인하세요.

### 동시에 가상 네트워크 및 다른 Azure 서비스와 동일한 개인 네트워크 연결을 사용할 수 있나요?
예. Express 경로 회로를 일단 설정하면 동시에 가상 네트워크 내 서비스 및 기타 Azure 서비스에 액세스할 수 있습니다. 공용 피어링 경로를 통해 가상 네트워크에 연결하고 공용 피어링 경로를 통해 기타 서비스에 연결합니다.

### Express 경로는 SLA(서비스 수준 계약)을 제공하나요?
자세한 내용은 [Express 경로 SLA 페이지](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

## 지원되는 서비스
대부분의 Azure 서비스는 Express 경로를 통해 지원됩니다.

- 가상 컴퓨터 및 가상 네트워크에 배포된 클라우드 서비스에 대한 연결은 개인 피어링 경로를 통해 지원됩니다.
- Azure 웹 사이트는 공용 피어링 경로를 통해 지원됩니다.
- IoT Hub는 공용 피어링 경로를 통해 지원됩니다.
- Office 365는 Microsoft 피어링 경로를 통해 지원됩니다.
- 다른 모든 서비스는 공용 피어링 경로를 통해 액세스할 수 있습니다. 예외는 다음과 같습니다.

	**다음 서비스는 지원되지 않습니다.**

	- CDN
	- Visual Studio Team Services 부하 테스트
	- Multi-Factor 인증
	- 트래픽 관리자

## 데이터 및 연결

### Express 경로를 사용하여 전송할 수 있는 데이터의 양에 제한이 있나요?
데이터 전송 크기에 대한 제한을 설정하지 않습니다. 대역폭 요금에 대한 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/)을 참조하세요.

### Express 경로에서 지원되는 연결 속도는 무엇인가요?
지원되는 대역폭은 다음을 제공합니다.

|50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 2Gbps, 5Gbps, 10Gbps|

### 어떤 서비스 공급자를 사용할 수 있나요?
서비스 공급자 및 위치 목록은 [Express 경로 파트너 및 위치](expressroute-locations.md)를 참조하세요.

## 기술 세부 정보

### 내 온-프레미스 위치를 Azure에 연결하기 위한 기술 요구 사항은 무엇인가요?
요구 사항은 [Express 경로 필수 구성 요소 페이지](expressroute-prerequisites.md)를 참조하세요.

### Express 경로에 대한 연결이 중복되나요?
예. 각 Express 경로 회로에는 고가용성을 제공하도록 구성된 중복 쌍의 교차연결이 있습니다.

### 내 Express 경로 링크 중 하나가 실패하면 연결이 끊어지나요?
교차 연결 중 하나가 실패할 경우 연결이 손실되지 않습니다. 중복 연결은 네트워크의 로드를 지원하기 위해 사용할 수 있습니다. 오류 복구를 수행할 다른 피어링 위치에 여러 회로를 추가로 만들 수 있습니다.

### <a name="onep2plink"></a>클라우드 교환에 공동 배치되지 않았으며 서비스 공급자가 점 대 점 연결을 공급하는 경우 온-프레미스 네트워크와 Microsoft 간에 두 개의 실제 연결을 주문해야 하나요? 
아니요, 서비스 공급자가 실제 연결을 통해 두 개의 이더넷 가상 회로를 설정할 수 있는 경우 하나의 실제 연결만 필요합니다. 실제 연결(예: 광 파이버)은 레이어 1(L1) 장치에서 종료됩니다(아래 이미지 참조). 두 개의 이더넷 가상 회로는 서로 다른 VLAN ID로 태그 처리되는데 하나는 기본 회로용이고 하나는 보조 회로용입니다. 해당 VLAN ID는 외부 802.1Q 이더넷 헤더에 있습니다. 내부 802.1Q 이더넷 헤더(표시되지 않음)는 특정 [Express 경로 라우팅 도메인](expressroute-circuit-peerings.md)에 매핑됩니다.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)


### Express 경로를 사용하여 Azure에 대한 내 VLAN 중 하나를 확장할 수 있나요?
아니요. Azure까지의 계층 2 연결 확장을 지원하지 않습니다.

### 내 구독에 둘 이상의 Express 경로 회로가 있을 수 있나요?
예. 사용자 구독에 둘 이상의 Express 경로 회로가 있을 수 있습니다. 전용 회로의 수에 대한 기본 제한은 10으로 설정됩니다 필요한 경우 제한을 늘리려면 Microsoft 지원에 문의할 수 있습니다.

### 다른 서비스 공급자의 Express 경로 회로를 사용할 수 있나요?
예. 여러 서비스 공급자의 Express 경로 회로가 있을 수 있습니다. 각 Express 경로 회로는 하나의 서비스 공급자화만 연결됩니다.

### 가상 네트워크를 Express 경로 회로에 연결하려면 어떻게 해야 하나요?
기본 단계는 다음과 같습니다.

- Express 경로 회로를 설정하고 서비스 공급자를 사용하도록 설정해야 합니다.
- 사용자 또는 공급자는 BGP 피어링을 구성해야 합니다.
- 가상 네트워크를 Express 경로 회로에 연결해야 합니다.

자세한 내용은 [회로에 대한 Express 경로 워크플로 프로비전 및 회로 상태](expressroute-workflows.md)를 참조하세요.

### 내 Express 경로 회로에 대한 연결 경계가 있나요?
예. [Express 경로 파트너 및 위치](expressroute-locations.md) 페이지에서 Express 경로 회로에 대한 연결 경계를 간략하게 설명합니다. ExpressRoute 회로에 대한 연결은 단일 지역으로 제한됩니다. 연결은 Express 경로 프리미엄 기능을 사용하여 지역을 교차하도록 확장할 수 있습니다.

### 둘 이상의 가상 네트워크를 Express 경로 회로에 연결할 수 있나요?
예. 최대 10개의 가상 네트워크를 Express 경로 회로에 연결할 수 있습니다.

### 가상 네트워크를 포함하는 여러 Azure 구독을 가지고 있습니다. 개별 구독에 속한 가상 네트워크를 단일 Express 경로 회로에 연결할 수 있나요?
예. 단일 Express 경로 회로를 사용하여 다른 Azure 구독을 최대 10개까지 권한을 부여할 수 있습니다. Express 경로 프리미엄 기능을 사용하여 이 한도를 늘릴 수 있습니다.

자세한 내용은 [여러 구독에서 Express 경로 회로 공유](expressroute-howto-linkvnet-arm.md)를 참조하세요.

### 가상 네트워크가 서로 격리된 동일한 회로에 연결되어 있나요?
아니요. 동일한 Express 경로 회로에 연결된 모든 가상 네트워크는 동일한 라우팅 도메인의 일부이며 라우팅 관점에서 서로 분리되지 않습니다. 경로 격리가 필요한 경우, 별도 Express 경로 회로를 만들어야 합니다.

### 하나의 가상 네트워크를 둘 이상의 Express 경로 회로에 연결할 수 있나요?
예. 최대 4까지의 Express 경로 회로와 단일 가상 네트워크를 연결할 수 있습니다. 4개의 [Express 경로 위치](expressroute-locations.md)를 통과하도록 순서를 지정해야 합니다.

### Express 경로 회로에 연결된 내 가상 네트워크에서 인터넷에 액세스할 수 있나요?
예. 기본 경로(0.0.0.0/0)나 BGP 세션을 통한 인터넷 경로 접두사를 보급하지 않았다면 Express 경로 회로에 연결된 가상 네트워크에서 인터넷에 연결할 수 있습니다.

### Express 경로 회로에 연결된 가상 네트워크에 인터넷 연결을 차단할 수 있나요?
예. 가상 네트워크 내에 배포된 가상 컴퓨터에 모든 인터넷 연결을 차단하도록 기본 경로(0.0.0.0/0)을 보급할 수 있으며 Express 경로 회로를 통해 모든 트래픽을 라우팅할 수 있습니다. 기본 경로를 보급하는 경우, 공용 피어링(예: Azure 저장소 및 SQL DB)을 통해 사용자 프레미스에 다시 제공된 서비스로 강제로 트래픽을 적용합니다. 공용 피어링 경로 또는 인터넷을 통해 트래픽을 Azure에 반환하도록 라우터를 구성해야 합니다.

### 동일한 Express 경로 회로에 연결된 가상 네트워크가 서로 통신할 수 있나요?
예. 동일한 Express 경로 회로에 연결된 가상 네트워크에 배포된 가상 컴퓨터는 서로 통신할 수 있습니다.

### Express 경로와 함께 가상 네트워크에 대한 사이트 간 연결을 사용할 수 있나요?
예. Express 경로는 사이트 간 VPN과 공존할 수 있습니다.

### Express 경로를 사용하여 사이트 간 / 지점과 사이트 간 구성에서 가상 네트워크를 이동할 수 있나요?
예. 가상 네트워크 내에 Express 경로 게이트웨이를 만들어야 합니다. 프로세스와 연관되어 잠시 가동 중지됩니다.

### Express 경로를 통해 Azure 저장소에 연결해야 하나요?
Express 경로 회로를 설정하고 공용 피어링에 대한 경로를 구성해야 합니다.

### 보급할 수 있는 경로의 수에 제한이 있나요?
예. 개인 피어링에 대해 최대 4000개의 경로 접두사를 허용하고, 공용 피어링과 Microsoft 피어링에 대해 각각 200개의 경로 접두사를 허용합니다. Express 경로 프리미엄 기능을 사용하도록 설정하면 개인 피어링에 대해 10,000개의 경로까지 늘릴 수 있습니다.

### BGP 세션을 통해 보급할 수 있는 IP 범위에 제한 사항이 있나요?
공용 및 Microsoft 피어링 BGP 세션에서 개인 접두사(RFC1918)를 허용하지 않습니다.

### BGP 제한을 초과하면 어떻게 되나요?
BGP 세션이 삭제됩니다. 접두사 개수가 제한보다 적으면 재설정됩니다.

### Express 경로 BGP 확보 시간이란 무엇인가요? 조정할 수 있나요?
확보 시간은 180입니다. 연결 유지 메시지는 60초마다 전송됩니다. 이 설정은 Microsoft 쪽에서 고정된 설정으로, 변경할 수 없습니다.

### 가상 네트워크에 기본 경로(0.0.0.0/0)를 보급한 후 Azure VM에서 실행되는 Windows를 활성화할 수 없습니다. 이 문제를 해결하려면 어떻게 해야 하나요?
다음 단계는 Azure가 정품 인증 요청을 인식하는 데 도움이 됩니다.

1. Express 경로 회로에 공용 피어링을 설정합니다.
2. DNS 조회를 수행하고 **kms.core.windows.net**의 IP 주소를 찾습니다.
3. 다음 두 항목 중 하나를 수행하면 키 관리 서비스가 Azure에서 해당 정품 인증 요청을 인식하고 요청을 인증합니다.
	- 온-프레미스 네트워크에서 공용 피어링을 통해 Azure에 다시 IP 주소(2단계에서 얻은)를 대상으로 하는 트래픽을 라우팅합니다.
	- NSP 공급자는 공용 피어링을 통해 Azure에 다시 트래픽을 hair-pin합니다.

### Express 경로 회로의 대역폭을 변경할 수 있나요?
예. 종료하지 않고 Express 경로 회로의 대역폭을 늘릴 수 있습니다. 연결 공급자는 네트워크 내 스로틀을 업데이트하여 대역폭 증가를 지원하도록 후속 작업해야 합니다. 그러나 Express 경로 회로의 대역폭을 줄일 수 없습니다. 대역폭을 낮춰야 한다는 것은 Express 경로 회로의 삭제 및 재생성을 의미합니다.

### Express 경로 회로의 대역폭을 변경하려면 어떻게 해야 하나요?
업데이트 전용 회로 API 및 PowerShell cmdlet을 사용하여 Express 경로 회로의 대역폭을 업데이트할 수 있습니다.

## Express 경로 프리미엄:

### Express 경로 프리미엄이란?
Express 경로 프리미엄은 아래 표시된 기능의 컬렉션입니다.

 - 증가된 라우팅 테이블은 개인 피어링에 대해 4000개의 경로에서 경로 10, 000개의 경로로 제한합니다.
 - Express 경로 회로에 연결할 수 있는 VNets 수가 증가합니다(기본값은 10). 자세한 내용은 다음 표를 참조하세요.
 - Microsoft 핵심 네트워크를 통해 전역 연결합니다. 이제 다른 지역의 Express 경로 회로를 지역의 VNet에 연결할 수 있습니다. **예:** 실리콘밸리에서 만든 Express 경로 회로에 서부 유럽에서 만든 VNet을 연결할 수 있습니다.
 - Office 365 서비스 및 CRM Online에 대한 연결

### Express 경로 프리미엄을 사용하는 경우, 얼마나 많은 VNet를 Express 경로 회로에 연결할 수 있나요?
아래 표는 Express 경로 한도 및 Express 경로 회로당 VNet의 수를 보여 줍니다.


[AZURE.INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]


### Express 경로 프리미엄을 사용하려면 어떻게 하나요?
기능이 활성화되어 있고 회로 상태를 업데이트하여 종료될 수 있는 경우, Express 경로 프리미엄 기능을 사용할 수 있습니다. 경로 작성 시 Express 경로 프리미엄을 사용하거나 업데이트 전용 회로 API / PowerShell cmdlet을 호출하여 Express 경로 프리미엄을 사용할 수 있습니다.

### Express 경로 프리미엄을 사용하지 않도록 하려면 어떻게 하나요?
업데이트 전용 회로 API / PowerShell cmdlet을 호출하여 Express 경로 프리미엄을 사용하지 않도록 설정할 수 있습니다. Express 경로 프리미엄을 사용하지 않도록 설정하기 전에 기본 제한을 충족하도록 연결 크기를 조정했는지 확인해야 합니다. 사용률이 확장 기본 제한을 초과 하는 경우, Express 경로 프리미엄을 사용하지 않도록 하는 요청이 실패합니다.

### 프리미엄 기능 집합에서 원하는 기능을 선택할 수 있나요?
아니요. 필요한 기능을 선택할 수 없습니다. Express 경로 프리미엄을 켜면 모든 기능을 사용합니다.

### Express 경로 프리미엄 비용
비용은 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/)을 참조하세요.

### 표준 Express 경로 요금 외에도 Express 경로 프리미엄에 대한 납부 여부
예. Express 경로 프리미엄 요금은 Express 경로 회로 요금 및 연결 공급자에서 필요한 요금 위에 적용됩니다.

## Express 경로, Office 365 서비스 및 CRM Online

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### Office 365 서비스 및 CRM Online에 연결하려면 어떻게 Express 경로 회로를 만드나요?

1. [Express 경로 필수 구성 요소 페이지](expressroute-prerequisites.md)를 검토하여 요구 사항을 충족하는지 확인합니다.
2. 서비스 공급자 및 위치 목록을 [Express 경로 파트너 및 위치](expressroute-locations.md)에서 검토하여 사용자 연결 요구 사항이 충족하는지 확인합니다.
3. [Office 365에 대한 네트워크 계획 및 성능 튜닝](http://aka.ms/tune/)을 검토하여 용량 요구 사항을 계획합니다.
4. [회로 프로비전 및 회로 상태에 대한 Express 경로 워크플로](expressroute-workflows.md) 연결 설정 아래의 워크플로에 나열된 단계를 따르세요.

>[AZURE.IMPORTANT] Office 365 서비스 및 CRM Online에 대한 연결을 구성하는 경우 Express 경로 프리미엄 추가 기능을 사용하도록 설정했는지 확인합니다.

### Office 365 서비스 및 CRM Online에 연결하려면 Azure 공용 피어링을 사용하도록 설정해야 하나요?
아니요, Microsoft 피어링을 사용하도록 설정하기만 하면 됩니다. Azure AD에 대한 인증 트래픽은 Microsoft 피어링을 통해 전송됩니다.

### 기존 내 Express 경로 회로가 Office 365 서비스 및 CRM Online에 대한 연결을 지원할 수 있나요?
예. 기존 Express 경로 회로가 Office 365 서비스에 대한 연결을 지원하도록 구성할 수 있나요? Office 365 서비스에 연결하는 데 용량이 충분한지 확인하고 프리미엄 추가 기능을 사용하도록 설정했는지 확인합니다. [Office 365에 대한 네트워크 계획 및 성능 튜닝](http://aka.ms/tune/)은 연결 요구 사항을 계획하는 데 도움이 됩니다. [Express 경로 회로 만들기 및 수정](expressroute-howto-circuit-classic.md)도 참조하세요.

### Express 경로 연결을 통해 어느 Office 365 서비스에 액세스할 수 있나요?

Express 경로에서 지원되는 최신 서비스 목록은 [Office 365 URL 및 IP 주소 범위](http://aka.ms/o365endpoints) 페이지를 참조하세요.

### Office 365 서비스 및 CRM Online용 Express 경로 비용은 얼마인가요?
Office 365 서비스 및 CRM Online에서는 프리미엄 추가 기능을 사용하도록 설정해야 합니다. [가격 세부 정보 페이지](https://azure.microsoft.com/pricing/details/expressroute/)는 Express 경로에 대한 비용의 세부 정보를 제공합니다.

### Office 365용 Express 경로가 지원하는 영역
Express 경로가 지원되는 파트너 및 위치에 대한 자세한 내용은 [Express 경로 파트너 및 위치](expressroute-locations.md)를 참조하세요.

### Express 경로가 내 조직에 대해 구성된 경우라도 인터넷을 통해 Office 365에 액세스할 수 있나요?
예. Express 경로가 네트워크에 대해 구성된 경우라도 인터넷을 통해 Office 365 서비스 끝점에 연결할 수 있습니다. Express 경로를 통해 Office 365 서비스에 연결하도록 구성된 위치에 있는 경우 Express 경로를 통해 연결됩니다.

### Express 경로 연결을 통해 Dynamics AX Online에 액세스할 수 있나요?
아니요, 지원되지 않습니다.

<!---HONumber=AcomDC_0921_2016-->