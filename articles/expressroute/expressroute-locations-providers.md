<properties
   pageTitle="Express 경로 위치 | Microsoft Azure"
   description="이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/20/2016"
   ms.author="cherylmc" />

# Express 경로 파트너 및 피어링 위치

이 문서의 표에서는 Express 경로 연결 공급자, Express 경로 지역 범위, Express 경로를 통해 지원되는 Microsoft 클라우드 서비스 및 Express 경로 SI(시스템 통합업체)에 대한 정보를 제공합니다.

## <a name="partners"></a>Express 경로 연결 공급자

Express 경로는 모든 Azure 지역 및 위치에서 지원됩니다. 다음 맵에 Azure 지역 및 Express 경로 위치 목록을 제공합니다. Express 경로 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.

![위치 맵][0]

지역 내 하나 이상의 Express 경로 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 다음 표에서 지역 내 Express 경로 위치에 대한 Azure 지역의 맵을 제공합니다.

|**지역**|**Azure 지역**|**Express 경로 위치**|
|---|---|---|
|**북아메리카**|미국 동부, 미국 서부, 미국 동부 2, 중앙 미국, 미국 중남부, 미국 중북부, 캐나다 중부, 캐나다 동부|애틀랜타, 시카고, 댈러스, 라스베이거스, 로스앤젤레스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC, 몬트리올+, 퀘벡 시티+, 토론토|
|**남미**|브라질 남부|상파울루|
|**유럽**|북유럽, 서유럽, 영국 서부, 영국 남부|암스테르담, 더블린, 런던, 뉴포트(웨일스)+, 파리|
|**아시아**|동아시아, 동남 아시아|홍콩 특별행정구, 싱가포르|
|**일본**|일본 서부, 일본 동부|오사카, 도쿄|
|**오스트레일리아**|오스트레일리아 남동부, 오스트레일리아 동부|멜버른, 시드니|
|**인도**|인도 서부, 인도 중부, 인도 남부|첸나이, 뭄바이|



아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

|**지역**|**Azure 지역**|**Express 경로 위치**|
|---|---|---|---|
|**미국 정부 클라우드**|미국 정부 아이오와, 미국 정부 버지니아|시카고, 댈러스, 뉴욕, 워싱턴 DC|
|**중국**|중국 북부, 중국 동부|베이징, 상하이|
|**독일**|독일 중부, 독일 동부|베를린+, 프랑크푸르트|


지정학적 지역에 걸친 연결은 표준 Express 경로 SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 Express 경로 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.


## 연결 공급자 위치

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### 프로덕션 Azure
| **위치** | **서비스 공급자** |
|---------------|-----------------------|
| **암스테르담** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT+, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **애틀랜타** | Equinix |
| **첸나이** | Tata Communications |
| **시카코** | AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **댈러스** | AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport |
| **더블린** | Colt, Telecity Group |
| **홍콩** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **런던** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **라스베이거스** | Level 3 Communications+, Megaport
| **로스앤젤레스** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **멜버른** | Equinix, Megaport, NEXTDC, Telstra Corporation |
| **뉴욕** | Equinix, Megaport, Zayo Group |
| **Montreal** | Cologix + |
| **뭄바이** | Tata Communications |
| **오사카** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **파리** | Interxion |
| **상파울루** | Equinix, Telefonica |
| **시애틀** | Equinix, Level 3 Communications, Megaport |
| **실리콘밸리** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **싱가포르** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **시드니** | AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **도쿄** | Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **토론토** | Cologix, Equinix, Zayo Group |
| **워싱턴 DC** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |

 **+**는 서비스 예정을 나타냄

### 국가별 클라우드 환경

#### 미국 정부 클라우드

| **위치** |**서비스 공급자** |
|---------------|--------------------|
| **시카코** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **댈러스** | Equinix, Verizon+ |
| **뉴욕** | Equinix, Level 3 Communications+, Verizon |
| **워싱턴 DC** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |

#### 중국

| **위치** | **서비스 공급자** |
|---------------|-----------------------|
| **베이징** | China Telecom |
| **상하이** | China Telecom |
자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)를 참조하세요.

#### 독일

| **위치** | **서비스 공급자** |
|---------------|-----------------------|
| **베를린** | Colt+, e-shelter+ |
| **프랑크푸르트** | Colt, Equinix+, Interxion |

## <a name="nonpartners"></a>나열되지 않은 서비스 공급자를 통한 연결

연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

- 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.

	- [Equinix 클라우드 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
	- 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
- Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 Express 경로 회로를 정렬합니다.
	- [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md)의 단계에 따라 연결을 설정합니다.

|**위치**|**Exchange**|**연결 공급자**|
|-------------|------------|-------------------------|
| **뉴욕** | Equinix | Lightower |
| **시애틀** | Equinix | Alaska Communications |
| **실리콘밸리** | Equinix | XO Communications |
| **싱가포르** | Equinix | 1CLOUDSTAR |
| **워싱턴 DC** | Equinix | Lightower |

## Express 경로 시스템 통합업체

사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 Express 경로에 등록할 수 있습니다.

|**대륙**|**시스템 통합 업체**|
|-------------|---------------------|
| **아시아** | Avanade Inc., OneAs1a|
| **유럽** | Avanade Inc., Dotnet Solutions|
| **US** | Avanade Inc., Equinix Professional Services, Perficient, Project Leadership|

## 다음 단계

- Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.
- 모든 필수 조건이 충족되었는지 확인합니다. [Express 경로 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"

<!---HONumber=AcomDC_0921_2016-->