<properties
	pageTitle="서비스 버스 프리미엄 및 표준 메시징 가격 책정 계층 개요 | Microsoft Azure"
	description="서비스 버스 프리미엄 및 표준 메시징"
	services="service-bus-messaging"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-messaging"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/02/2016"
	ms.author="darosa;sethm"/>

# 서비스 버스 프리미엄 및 표준 메시징 계층 

큐 및 항목과 같은 메시징 엔터티를 포함하는 Service Bus 메시징은 클라우드 범위에서 엔터프라이즈 메시징 기능을 풍부한 게시-구독 의미 체계에 결합합니다. 서비스 버스 메시징은 정교한 여러 클라우드 솔루션의 통신 백본으로 사용됩니다.

서비스 버스 메시징의 *프리미엄* 계층은 중요 업무용 응용 프로그램에 대한 확장성, 성능 및 가용성에 관한 일반적인 고객 요구를 해결합니다. 기능 집합은 거의 동일하지만 이러한 서비스 버스 메시징의 두 계층은 다른 용도로 사용하도록 고안되었습니다.

아래 표에는 차이가 자세히 설명되어 있습니다.

| Premium | Standard |
|---------------------------------------|--------------------------------|
| 높은 처리량 | 가변 처리량 |
| 예측 가능한 성능 | 가변 대기 시간 |
| 예측 가능한 가격 | 종량제 가변 가격 |
| 작업을 확장 및 축소하는 기능 | 해당 없음 |
| 메시지 크기 > 256KB | 메시지 크기 = 256KB |

**서비스 버스 프리미엄 메시징**은 각 고객의 작업이 따로 실행되도록 CPU 및 메모리 계층에서 리소스 격리를 제공합니다. 이 리소스 컨테이너를 *메시징 단위*라고 합니다. 각 프리미엄 네임스페이스에는 하나 이상의 메시징 단위가 할당됩니다. 각 서비스 버스 프리미엄 네임스페이스에 대해 1, 2 또는 4 메시징 단위를 구입할 수 있습니다. 요금은 24시간 단위 또는 일별 요금으로 부과되더라도 단일 워크로드 또는 엔터티가 여러 메시징 단위에 걸쳐 있을 수 있고 메시징 단위 수를 변경할 수도 있습니다. 그 결과, 서비스 버스 기반 솔루션에 대해 예측 가능하고 반복 가능한 성능이 구현됩니다.

이로 인해 예측 가능성 및 가용성도 높아질 뿐 아니라 속도도 더 빨라집니다. 서비스 버스 프리미엄 메시징은 [Azure 이벤트 허브](https://azure.microsoft.com/services/event-hubs/)에 도입된 저장소 엔진에 구축됩니다. 프리미엄 메시징을 사용할 경우 표준 계층을 사용하는 것보다 최고 성능이 훨씬 더 빠릅니다.

## 프리미엄 메시징 기술 차이

다음은 프리미엄 및 표준 메시징 계층 간의 몇 가지 차이점입니다.

### 분할 큐 및 항목

분할 큐 및 항목은 프리미엄 메시징에서 지원되지만 Service Bus 메시징의 표준 및 기본 계층의 경우와 동일하게 작동하지 않습니다. 프리미엄 메시징은 SQL을 데이터 저장소로 사용하지 않으며 공유 플랫폼과 관련된 리소스 경합이 더 이상 가능하지 않습니다. 따라서 분할이 필요하지 않습니다. 또한 파티션 수가 표준 메시징의 16개 파티션에서 프리미엄 메시징의 2개 파티션으로 변경되었습니다. 2개의 파티션은 가용성을 보장하며, 프리미엄 런타임 환경에 좀 더 적합합니다. 분할에 대한 자세한 내용은 [분할 큐 및 항목](service-bus-partitioning.md)을 참조하세요.

### Express 엔터티

프리미엄 메시징이 완전히 격리된 런타임 환경에서 실행되므로 프리미엄 메시징에는 Express 엔터티가 지원되지 않습니다. Express 기능에 대한 자세한 내용은 [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) 속성을 참조하세요.

## 다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Azure 서비스 버스 프리미엄 메시징 소개(블로그 게시물)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure 서비스 버스 프리미엄 메시징 소개(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [서비스 버스 메시징 개요](service-bus-messaging-overview.md)
- [Azure 서비스 버스 아키텍처 개요](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
- [서비스 버스 큐를 사용하는 방법](service-bus-dotnet-get-started-with-queues.md)

<!---HONumber=AcomDC_0928_2016-->