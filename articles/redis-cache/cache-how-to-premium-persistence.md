<properties 
	pageTitle="프리미엄 Azure Redis Cache에 데이터 지속성을 구성하는 방법" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대해 데이터 지속성을 구성하고 관리하는 방법에 대해 알아봅니다." 
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

# 프리미엄 Azure Redis Cache에 데이터 지속성을 구성하는 방법

Azure Redis Cache에는 새 프리미엄 계층을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다.

Azure Redis Cache 프리미엄 계층에는 클러스터링, 지속성 및 가상 네트워크 지원 등의 기능이 포함됩니다. 이 문서에서는 프리미엄 Azure Redis Cache에서 지속성을 구성하는 방법을 설명합니다.

다른 프리미엄 캐시 기능에 대한 자세한 내용은 [Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)를 참조하세요.

## 데이터 지속성이란?
Redis 지속성을 사용하면 Redis에 저장된 데이터를 유지할 수 있습니다. 또한 스냅숏을 만들고, 하드웨어 오류 시 로드할 수 있게 데이터를 백업할 수 있습니다. 기본 또는 표준 계층보다 훨씬 큰 이러한 혜택은 모든 데이터가 메모리에 저장되기 때문에 가능하며 캐시 노드 다운 시 데이터 손실 가능성이 있습니다.

Azure Redis Cache는 [RDB 모델](http://redis.io/topics/persistence)을 사용하여 Redis 지속성을 제공하며 이는 Azure 저장소 계정에 데이터를 저장합니다. 지속성이 구성된 경우 Azure Redis Cache는 구성 가능한 백업 주기에 따라 Redis 이진 형식으로 Redis 캐시의 스냅숏을 디스크에 지속적으로 유지합니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 가장 최근의 스냅숏을 사용하여 캐시를 재구성합니다.

기존 프리미엄 캐시의 경우 **설정** 블레이드에서 지속성을 구성하고 캐시를 만드는 경우에는 **새 Redis Cache** 블레이드에서 지속성을 구성할 수 있습니다.

## 프리미엄 캐시 만들기

캐시를 만들고 지속성을 구성하려면 [Azure 포털](https://portal.azure.com)에 로그인하고 **새로 만들기**->**데이터 + 저장소**>**Redis Cache**를 클릭합니다.

![Redis Cache 만들기][redis-cache-new-cache-menu]

지속성을 구성하려면 먼저 **가격 책정 계층 선택** 블레이드에서 **프리미엄** 캐시 중 하나를 선택합니다.

![가격 계층 선택][redis-cache-premium-pricing-tier]

프리미엄 가격 책정 계층을 선택한 다음 **Redis 지속성**을 클릭합니다.

![Redis 지속성][redis-cache-persistence]

다음 섹션의 단계에서는 새 프리미엄 캐시에서 Redis 지속성을 구성하는 방법에 대해 설명합니다. Redis 지속성이 구성되어 있는 경우 **만들기**를 클릭하여 Redis 지속성을 사용하는 새 프리미엄 캐시를 만듭니다.

## Redis 지속성 구성

Redis 지속성은 **Redis 데이터 지속성** 블레이드에서 구성됩니다. 새 캐시의 경우 위의 섹션에서 설명한 대로 캐시 만들기 프로세스 중 이 블레이드에 액세스합니다. 기존 캐시의 경우 캐시의 **설정** 블레이드에서 **Redis 데이터 지속성** 블레이드에 액세스합니다.

![Redis 설정][redis-cache-settings]

Redis 지속성을 사용하려면 **사용**을 클릭하여 RDB(Redis 데이터베이스) 백업을 사용하도록 설정합니다. 이전에 사용하도록 설정한 프리미엄 캐시에서 Redis 지속성을 사용하지 않도록 설정하려면 **사용 안 함**을 클릭합니다.

백업 간격을 구성하려면 드롭다운 목록에서 **백업 빈도**를 선택합니다. **15분**, **30분**, **60분**, **6시간**, **12시간**, **24시간**에서 선택할 수 있습니다. 이 간격은 이전 백업 작업이 성공적으로 완료되어 새 백업이 시작된 시점부터 계산됩니다.

**저장소 계정**을 클릭하여 사용할 저장소 계정을 선택하고 **저장소 키** 드롭다운에서 사용할 **기본 키** 또는 **보조 키**를 선택합니다. 캐시와 동일한 영역에 있는 저장소 계정을 선택해야 하며 높은 처리량을 가진 **프리미엄 저장소** 계정을 사용하는 것이 좋습니다.

>[AZURE.IMPORTANT] 지속성 계정에 대한 저장소 키가 다시 생성된 경우에는 **저장소 키** 드롭다운에서 원하는 키를 다시 선택해야 합니다.

![Redis 지속성][redis-cache-persistence-selected]

**확인**을 클릭하여 지속성 구성을 저장합니다.

백업 빈도 간격이 경과되면 다음 백업(새 캐시의 경우 첫 번째 백업)이 시작됩니다.



## 지속성 FAQ

다음 목록에는 Azure Redis Cache 지속성에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

-	[이전에 만든 캐시에서 지속성을 사용할 수 있나요?](#can-i-enable-persistence-on-a-previously-created-cache)
-	[캐시를 만든 후 백업 주기를 변경할 수 있나요?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-	[60분 백업 주기인데 왜 백업 사이 간격이 60분 이상이 되나요?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-	[새 백업을 만들면 이전 백업은 어떻게 되나요?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-	[다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### 이전에 만든 캐시에서 지속성을 사용할 수 있나요?

예, Redis 지속성은 캐시를 만들 때와 기존 프리미엄 캐시에서 모두 구성할 수 있습니다.

### 캐시를 만든 후 백업 주기를 변경할 수 있나요?

예, **Redis 데이터 지속성** 블레이드에서 백업 빈도를 변경할 수 있습니다. 자세한 내용은 [Redis 지속성 구성](#configure-redis-persistence)을 참조하세요.

### 60분 백업 주기인데 왜 백업 사이 간격이 60분 이상이 되나요?

백업 간격의 주기는 이전 백업 프로세스가 성공적으로 완료되어야 시작됩니다. 백업 간격이 60분이고 백업 프로세스를 성공적으로 완료하는 데 15분이 걸린다면 다음 백업은 이전 백업 시작 시점에서 75분까지 시작되지 않습니다.

### 새 백업을 만들면 이전 백업은 어떻게 되나요?

가장 최근 백업을 제외한 모든 백업은 자동으로 삭제됩니다. 즉시 삭제되지 않을 수 있으나 오래된 백업을 무한정 유지하지는 않습니다.

### 다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?

-	더 큰 크기로 조정했다면 영향은 없습니다.
-	더 작은 크기를 조정했고 새 크기에 대한 [데이터베이스 제한](cache-configure.md#databases)보다 사용자 지정 [데이터베이스](cache-configure.md#databases) 설정이 더 크다면, 그러한 데이터베이스에 저장된 데이터는 복원되지 않습니다. 자세한 내용은 [사용자 지정 데이터베이스 설정이 크기 조정 동안 영향을 받나요?](#is-my-custom-databases-setting-affected-during-scaling)를 참조하세요.
-	더 작은 크기로 조정 했고 마지막 백업의 모든 데이터를 저장하기에 충분한 공간이 더 작은 크기에 없는 경우, 일반적으로 [allkeys-lru](http://redis.io/topics/lru-cache) 제거 정책을 사용하여 복원 프로세스 중에 키가 제거됩니다.

## 다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

-	[Azure Redis Cache 프리미엄 계층 소개](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_0921_2016-->