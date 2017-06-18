<properties 
	pageTitle="Azure Redis Cache 프리미엄 계층 소개 | Microsoft Azure" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대해 Redis 지속성, Redis 클러스터링 및 VNET 지원을 만들고 관리하는 방법에 대해 알아봅니다." 
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

# Azure Redis Cache 프리미엄 계층 소개
Azure Redis Cache는 분산되고 관리된 캐시로, 데이터에 매우 빠르게 액세스하여 확장성과 응답성이 뛰어난 응용 프로그램을 빌드하는 데 사용할 수 있습니다.

새로운 프리미엄 계층은 모든 표준 계층 기능과 추가 기능(예: 성능 향상, 더 큰 작업, 재해 복구, 가져오기/내보내기 및 강화된 보안)이 포함된 엔터프라이즈급 계층입니다. 프리미엄 캐시 계층의 추가 기능에 대해 자세히 알아보려면 계속 읽습니다.

## 표준 또는 기본 계층에 비해 향상된 성능
**표준 또는 기본 계층에 대해 향상된 성능.** 프리미엄 계층의 캐시는 더 빠른 프로세서가 포함되고 기본 또는 표준 계층에 비해 더 나은 성능을 제공하는 하드웨어에 배포됩니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다.

**동일한 크기의 캐시에 대한 처리량이 표준 계층에 비해 프리미엄에서 더 높습니다.** 예를 들어 53GB P4(프리미엄) 캐시의 처리량은 초당 250K의 요청인 반면 C6(표준)은 150K입니다.

프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

## Redis 데이터 지속성
프리미엄 계층을 사용하면 Azure 저장소 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라의 경우 문제는 잠재적인 데이터 손실이 있을 수 있다는 점입니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Redis Cache는 [Redis 지속성](http://redis.io/topics/persistence)에서 RDB 및 AOF(출시 예정) 옵션을 제공합니다.

지속성 구성에 대한 지침은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.

##Redis 클러스터
53GB보다 큰 캐시를 만들거나 여러 Redis 노드에서 데이터를 분할하려는 경우 프리미엄 계층에서 사용 가능한 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 Azure에 의해 관리되는 주/복제본 캐시 쌍으로 구성됩니다.

**Redis 클러스터링은 최대 배율 및 처리량을 제공합니다.** 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 10개의 분할된 데이터베이스에 대해 P4 클러스터를 만드는 경우 가능한 처리량은 250K * 10 = 초당 250만 요청 수입니다. 프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

클러스터링을 시작하려면 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

##강화된 보안 및 격리

기본 또는 표준 계층에서 만든 캐시는 공용 인터넷에서 액세스할 수 있습니다. 캐시에 대한 액세스는 선택키에 따라 제한됩니다. 프리미엄 계층을 사용하면 지정된 네트워크 내의 클라이언트만 캐시에 액세스할 수 있는지 추가로 확인할 수 있습니다. [Azure VNet(가상 네트워크)](https://azure.microsoft.com/services/virtual-network/)에서 Redis Cache를 배포할 수 있습니다. 서브넷, 액세스 제어 정책과 같은 VNet의 모든 기능 및 다른 기능을 사용하여 추가로 Redis에 대한 액세스를 제한할 수 있습니다.

자세한 내용은 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.

## 가져오기/내보내기

가져오기/내보내기는 프리미엄 캐시에서 Azure 저장소 계정의 페이지 Blob으로 Redis Cache 데이터베이스(RDB) 스냅숏을 가져오고 내보내는 방식으로, Azure Redis Cache로 데이터를 가져오고 Azure Redis Cache의 데이터를 내보낼 수 있는 Azure Redis Cache 데이터 관리 작업입니다. 이를 통해, 다양한 Azure Redis Cache 인스턴스 간에 마이그레이션이 가능하고, 데이터를 사용하기 전에 캐시에 채울 수 있습니다.

가져오기는 Linux, Windows 또는 Amazon Web Services 및 기타 클라우드 공급자에서 실행되는 Redis를 비롯한 환경이나 클라우드에서 실행되는 Redis 서버로부터 Redis 호환 RDB 파일을 가져오는 데 사용됩니다. 데이터 가져오기는 미리 채워진 데이터로 캐시를 만드는 손쉬운 방법입니다. 가져오기 프로세스를 진행하는 동안, Azure Redis Cache는 Azure 저장소에서 메모리로 RDB 파일을 로드한 다음 키를 캐시에 삽입합니다.

내보내기를 통해 Azure Redis Cache에 저장된 데이터를 Redis 호환 RDB 파일로 내보낼 수 있습니다. 이 기능을 사용하면 Azure Redis Cache 인스턴스에서 다른 인스턴스나 다른 Redis 서버로 데이터를 이동할 수 있습니다. 내보내기 프로세스를 진행하는 동안, Azure Redis Cache 서버 인스턴스를 호스트하는 VM에 임시 파일이 생성되고, 지정된 저장소 계정에 그 파일이 업로드됩니다. 성공 또는 실패 상태로 내보내기 작업이 완료되면, 임시 파일은 삭제됩니다.

자세한 내용은 [Azure Redis Cache로 데이터를 가져오고 Azure Redis Cache에서 데이터를 내보내는 방법](cache-how-to-import-export-data.md)을 참조하세요.

## Reboot

이 프리미엄 계층에서는 요청이 있을 때 하나 이상의 캐시 노드를 다시 부팅할 수 있습니다. 따라서 오류 발생 시 응용 프로그램의 복원력을 테스트할 수 있습니다. 다음 노드를 다시 부팅할 수 있습니다.

-	캐시의 마스터 노드
-	캐시의 슬레이브 노드
-	캐시의 마스터 및 슬레이브 노드 둘 다
-	프리미엄 캐시에 클러스터링을 사용하는 경우 캐시의 개별 분할 영역에 대한 마스터, 슬레이브 또는 두 노드를 모두 다시 부팅할 수 있습니다.

자세한 내용은 [다시 부팅](cache-administration.md#reboot) 및 [다시 부팅 FAQ](cache-administration.md#reboot-faq)를 참조하세요.

## 업데이트 예약

예약된 업데이트 기능을 사용하여 캐시 유지 관리 기간을 지정할 수 있습니다. 유지 관리 기간이 지정되면 이 기간 동안 Redis 서버 업데이트가 진행됩니다. 유지 관리 기간을 지정하려면 원하는 요일을 선택하고 각 요일의 유지 관리 기간 시작 시간을 선택합니다. 유지 관리 기간 시간은 UTC로 나타냅니다.

자세한 내용은 [업데이트 예약](cache-administration.md#schedule-updates) 및 [업데이트 예약 FAQ](cache-administration.md#schedule-updates-faq)를 참조하세요.

>[AZURE.NOTE] 예약된 유지 관리 기간 동안에는 Redis 서버 업데이트만 수행됩니다. 유지 관리 기간이 Azure 업데이트 또는 VM 운영 체제에 대한 업데이트에는 적용되지 않습니다.

## 프리미엄 계층으로 크기를 조정하려면

프리미엄 계층으로 크기를 조정하려면 **가격 책정 계층 변경** 블레이드에서 프리미엄 계층 중 하나를 선택하기만 하면 됩니다. 또한 PowerShell 및 CLI를 사용하여 프리미엄 계층으로 캐시를 확장할 수 있습니다. 단계별 지침에 대해서는 [Azure Redis Cache 크기를 조정하는 방법](cache-how-to-scale.md) 및 [크기 조정 작업을 자동화하는 방법](cache-how-to-scale.md#how-to-automate-a-scaling-operation)을 참조하세요.

## 다음 단계

캐시를 만들고 새로운 프리미엄 계층 기능을 탐색합니다.

-	[프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)
-	[프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)
-	[프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)
-	[Azure Redis Cache로 데이터를 가져오고 Azure Redis Cache에서 데이터를 내보내는 방법](cache-how-to-import-export-data.md)
-	[Azure Redis Cache를 관리하는 방법](cache-administration.md)
  

<!---HONumber=AcomDC_0921_2016-->