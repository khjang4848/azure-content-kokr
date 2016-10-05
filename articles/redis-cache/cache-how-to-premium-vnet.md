<properties 
	pageTitle="프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법 | Microsoft Azure" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대한 가상 네트워크 지원을 만들고 관리하는 방법에 대해 알아봅니다." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>

# 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법
Azure Redis Cache에는 새 프리미엄 계층을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다.

Azure Redis Cache 프리미엄 계층 기능에는 클러스터링, 지속성 및 VNet(가상 네트워크) 지원이 포함됩니다. VNet은 클라우드의 개인 네트워크입니다. Azure Redis Cache 인스턴스를 VNet으로 구성한 경우 클라이언트에서만 공개적으로 주소를 지정할 수 없으며, VNet 내의 가상 컴퓨터 및 응용 프로그램에서만 액세스할 수 있습니다. 이 문서에서는 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법을 설명합니다.

>[AZURE.NOTE] Azure Redis Cache는 클래식 및 ARM VNet을 둘 다 지원합니다.

다른 프리미엄 캐시 기능에 대한 자세한 내용은 [Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)를 참조하세요.

## VNet을 사용하는 이유
[Azure VNet(가상 네트워크)](https://azure.microsoft.com/services/virtual-network/) 배포는 Azure Redis Cache의 보안과 격리를 강화하며 Azure Redis Cache에 대한 액세스를 추가적으로 제한하기 위한 서브넷, 액세스 제어 정책 및 기타 기능을 제공합니다.

## 가상 네트워크 지원
VNet(가상 네트워크) 지원은 캐시를 만드는 중에 **새 Redis 캐시** 블레이드에 구성됩니다.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

프리미엄 가격 책정 계층을 선택한 경우 캐시와 동일한 구독 및 위치에 있는 VNet을 선택하여 Azure Redis Cache VNet 통합을 구성할 수 있습니다. 새 VNet을 사용하려면 먼저 [Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 또는 [Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-portal.md)의 단계에 따라 VNet을 만든 다음 **새 Redis Cache** 블레이드로 돌아가 프리미엄 캐시를 만들고 구성합니다.

새 캐시에 대해 VNet을 구성하려면 **새 Redis Cache** 블레이드에서 **가상 네트워크**를 클릭하고 드롭다운 목록에서 원하는 VNet을 선택합니다.

![가상 네트워크][redis-cache-vnet]

**서브넷** 드롭다운 목록에서 원하는 서브넷을 선택한 후 원하는 **고정 IP 주소**를 지정합니다. 클래식 VNet을 사용하는 경우 **고정 IP 주소** 필드는 선택적이며, 아무 항목도 지정하지 않으면 선택한 서브넷에서 하나가 자동으로 선택됩니다.

>[AZURE.IMPORTANT] ARM VNet에 Azure Redis Cache를 배포하는 경우 캐시가 Azure Redis Cache 인스턴스를 제외하고 다른 리소스를 포함하지 않는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 서브넷의 ARM VNet에 Azure Redis Cache를 배포하려고 하면 배포에 실패합니다.

![가상 네트워크][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] 서브넷의 처음 4개 주소는 예약되어 있으므로 사용할 수 없습니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)를 참조하세요.

캐시가 만들어진 경우 **설정** 블레이드에서 **가상 네트워크**를 클릭하여 VNet에 대한 구성을 볼 수 있습니다.

![가상 네트워크][redis-cache-vnet-info]


VNet을 사용하는 경우 Azure Redis Cache 인스턴스에 연결하려면 다음 예제에 표시된 대로 연결 문자열에 캐시의 호스트 이름을 지정합니다.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Azure Redis Cache VNet FAQ

다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

-	[Azure Redis Cache 및 VNet의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-	[표준 또는 기본 캐시에 VNet을 사용할 수 있나요?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-	[일부 서브넷에서만 Redis Cache 생성이 실패하는 이유는 무엇인가요?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-	[VNET에서 캐시를 호스팅하는 경우 모든 캐시 기능이 작동하나요?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## Azure Redis Cache 및 VNet의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?

Azure Redis Cache가 VNet에 호스트된 경우 다음 표의 포트가 사용됩니다. 이러한 포트가 차단되면 캐시가 제대로 작동하지 않을 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNet에서 Azure Redis Cache를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 원격 IP |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | 아웃바운드 | TCP | Azure 저장소/PKI(인터넷)에 대한 Redis 종속성 | * |
| 53 | 아웃바운드 | TCP/UDP | DNS(인터넷/VNet)에 대한 Redis 종속성 | * |
| 6379, 6380 | 인바운드 | TCP | Redis, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | 인바운드/아웃바운드 | TCP | Redis에 대한 구현 세부 정보 | VIRTUAL\_NETWORK |
| 8500 | 인바운드 | TCP/UDP | Azure 부하 분산 | AZURE\_LOADBALANCER |
| 10221-10231 | 인바운드/아웃바운드 | TCP | Redis에 대한 구현 세부 정보(VIRTUAL\_NETWORK에 원격 끝점을 제한할 수 있음) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | 인바운드 | TCP | Redis 클러스터, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | 인바운드 | TCP | Redis 클러스터, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | 인바운드 | TCP/UDP | Azure 부하 분산 | AZURE\_LOADBALANCER |
| 20226 | 인바운드+아웃바운드 | TCP | Redis 클러스터에 대한 구현 세부 정보 | VIRTUAL\_NETWORK |


가상 네트워크에서 처음에 충족되지 않는 Azure Redis Cache에 대한 네트워크 연결 요구 사항이 있습니다. Azure Redis Cache를 가상 네트워크 내에서 사용하는 경우 제대로 작동되려면 다음 항목이 모두 필요합니다.

-  전세계 Azure 저장소 끝점에 아웃바운드 네트워크 연결. Azure Redis Cache 인스턴스와 동일한 지역에 있는 끝점 뿐만 아니라 **다른** Azure 지역에 있는 저장소 끝점을 포함합니다. 다음 DNS 도메인에서 Azure 저장소 끝점은 다음을 확인합니다. *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* 및 *file.core.windows.net*.
-  *ocsp.msocsp.com*, *mscrl.microsoft.com* 및 *crl.microsoft.com*에 대한 아웃바운드 네트워크 연결입니다. 이 연결은 SSL 기능을 지원하는 데 필요합니다.
-  가상 네트워크에 대한 DNS 구성은 이전 시점에 언급된 끝점 및 도메인을 모두 해결할 수 있어야 합니다. 유효한 DNS 인프라를 구성하고 가상 네트워크에 유지 관리하여 DNS 요구를 충족할 수 있습니다.



### 표준 또는 기본 캐시에 VNet을 사용할 수 있나요?

VNet은 프리미엄 캐시에만 사용할 수 있습니다.

### 일부 서브넷에서만 Redis Cache 생성이 실패하는 이유는 무엇인가요?

ARM VNet에 Azure Redis Cache를 배포하는 경우 캐시가 다른 리소스를 포함하지 않는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 서브넷의 ARM VNet에 Azure Redis Cache를 배포하려고 하면 배포가 실패합니다. 새 Redis Cache를 만들려면 서브넷 내의 기존 리소스를 삭제해야 합니다.

사용할 수 있는 충분한 IP 주소가 있으면, 클래식 VNet에 여러 유형의 리소스를 배포할 수 있습니다.

### VNET에서 캐시를 호스팅하는 경우 모든 캐시 기능이 작동하나요?

캐시가 VNET의 일부인 경우 VNET의 클라이언트만 캐시에 액세스할 수 있습니다. 결과적으로 이번에는 다음 캐시 관리 기능이 작동하지 않습니다.

-	Redis 콘솔 - Redis 콘솔은 VNET의 일부가 아닌 VM에서 호스팅되는 redis-cli.exe 클라이언트를 사용하므로 캐시에 연결할 수 없습니다.


## Azure Redis Cache에서 Express 경로 사용

고객은 [Azure Express 경로](https://azure.microsoft.com/services/expressroute/) 회로를 가상 네트워크 인프라로 연결 할 수 있습니다. 따라서 Azure로 온-프레미스 네트워크를 확장합니다.

기본적으로, 새로 생성된 Express Route 회로는 아웃바운드 인터넷 연결을 허용하는 기본 경로를 알립니다. 이 구성을 사용할 경우 클라이언트 응용 프로그램은 Azure Redis Cache를 포함하는 다른 Azure 끝점에 연결할 수 있습니다.

그러나 고객의 일반적인 구성은 온-프레미스 흐름 대신 아웃바운드 인터넷 트래픽을 강제하는 기본 경로(0.0.0.0/0)로 정의되어 있습니다. 이 트래픽 흐름은 변함없이 Azure Redis Cache와의 연결을 끊습니다. 그 이유는 아웃바운드 트래픽이 온-프레미스에서 막히거나 다양한 Azure 끝점에서 더 이상 작동하지 않는 인식 불가능한 주소 집합으로 NAT되기 때문입니다.

해결책은 하나의(또는 그 이상) UDR(사용자 정의 경로)을 Azure Redis Cache를 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 특정 서브넷 경로는 기본 경로대신 적용됩니다.

가능하면 다음 구성을 사용하는 것이 좋습니다.

- Express 경로 구성은 0.0.0.0/0을 보급하고 기본적으로 모든 아웃바운드 트래픽 온-프레미스를 강제로 터널링합니다.
- Azure Redis Cache를 포함하는 서브넷에 적용된 UDR은 인터넷의 다음 홉 형식을 사용하여 0.0.0.0/0를 정의합니다(이 예제는 이 문서에서 아래쪽에 있음).

이러한 단계의 결합된 효과는 서브넷 수준 UDR이 강제된 터널링에 Express 경로를 담당하고 Azure Redis Cache에서 아웃바운드 인터넷 액세스를 보장합니다.

Express 경로를 사용하여 온-프레미스 응용 프로그램에서 Azure Redis Cache 인스턴스에 연결하는 것은 성능상의 이유로 일반적인 사용 시나리오에 해당하지 않지만(최상의 성능을 위해 Azure Redis Cache 클라이언트는 Azure Redis Cache와 동일한 지역에 있어야 함) 이 시나리오에서 아웃바운드 네트워크 경로는 내부 회사 프록시를 통과할 수 없고 온-프레미스에 강제로 터널링될 수도 없습니다. 이렇게 하면 Azure Redis Cache에서 아웃바운드 네트워크 트래픽의 효율적인 NAT 주소를 변경합니다. Azure Redis Cache 인스턴스의 아웃바운드 네트워크 트래픽의 NAT 주소 변경은 위에 나열된 끝점 대부분에 연결 실패를 초래합니다. 이로 인해 Azure Redis Cache를 만들려고 하면 실패합니다.

**중요:** UDR에 정의된 경로는 Express 경로 구성을 통해 보급된 경로보다 우선하도록 충분히 **구체적이어야** 합니다. 다음 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용하고 따라서 잠재적으로 보다 구체적인 주소 범위를 사용하는 경로 알림에서 실수로 재정의될 수 있습니다.

**매우 중요:** **공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 잘못**한 Express 경로 구성에서는 Azure Redis Cache가 지원되지 않습니다. 구성된 공용 피어링이 있는 Express 경로 구성은 다양한 Microsoft Azure IP 주소 범위 집합에 대해 Microsoft에서 경로 보급을 받습니다. 이러한 주소 범위의 교차 보급을 개인 피어링 경로에 잘못한 경우 Azure Redis Cache 인스턴스의 서브넷에서 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라에 강제 터널링되는 잘못된 최종 결과를 발생시킵니다. 이 네트워크 흐름은 Azure Redis Cache를 중단합니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 이어진 교차 보급 경로를 중지합니다.

사용자 정의 경로에 대한 배경 정보는 [개요](../virtual-network/virtual-networks-udr-overview.md)를 참조하세요.

Express 경로에 대한 자세한 내용은 [Express 경로 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.

## 다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

-	[Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0921_2016-->