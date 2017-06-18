<properties
	pageTitle="미디어 서비스 확장의 CDN 캐싱 정책"
	description="이 항목에서는 Media Services Extension의 CDN 캐싱 정책에 대한 개요를 제공합니다."
	services="media-services,cdn"
	documentationCenter=".NET"
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="juliako"/>
 
#미디어 서비스 확장의 CDN 캐싱 정책

Azure 미디어 서비스는 HTTP 기반 적응 스트리밍 및 점진적 다운로드를 제공합니다. HTTP 기반 스트리밍은 클라이언트 쪽 캐싱뿐만 아니라 프록시 및 CDN 계층의 캐싱을 활용하므로 확장성이 뛰어납니다. 스트리밍 끝점은 일반적인 스트리밍 기능 및 HTTP 캐시 헤더에 대한 구성을 제공합니다. 스트리밍 끝점은 HTTP Cache-Control: max-age 및 Expires 헤더를 설정합니다. HTTP 캐시 헤더에 대한 자세한 내용은 [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)에서 확인할 수 있습니다.

##기본 캐싱 헤더

기본적으로 스트리밍 끝점은 주문형 스트리밍 데이터(실제 미디어 조각/청크) 및 매니페스트(재생 목록)에 대해 3일 캐시 헤더를 적용합니다. 라이브 스트리밍의 경우 스트리밍 끝점은 데이터(실제 미디어 조각/청크)에 대해 3일 캐시 헤드를 적용하고, 매니페스트(재생 목록) 요청에 대해 2초 캐시 헤더를 적용합니다. 라이브 프로그램이 주문형(라이브 보관 파일)으로 바뀐 경우에는 주문형 스트리밍 캐시 헤더가 적용됩니다.

##Azure CDN 통합

Azure 미디어 서비스는 스트리밍 끝점에 대한 [CDN 통합](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/)을 제공합니다. Cache-control 헤더는 CDN 사용 스트리밍 끝점에 스트리밍 끝점과 동일한 방식으로 적용됩니다. Azure CDN은 스트리밍 끝점에 구성된 캐시 값을 사용하여 내부적으로 캐시된 개체의 수명을 정의하며, 이 값을 사용하여 배달 캐시 헤더도 설정합니다. CDN 사용 스트리밍 끝점을 사용하는 경우에는 작은 캐시 값을 설정하지 않는 것이 좋습니다. 작은 값을 설정하면 성능이 저하되고 CDN의 이점이 줄어듭니다. CDN 사용 스트리밍 끝점의 경우 캐시 헤더를 600초보다 작게 설정할 수 없습니다.

>[AZURE.IMPORTANT] Azure 미디어 서비스와 Azure CDN의 통합은 **Verizon의 Azure CDN**에 구현됩니다. Azure Media Services에 **Akamai의 Azure CDN**을 사용하려면 [끝점을 수동으로 구성](cdn-create-new-endpoint.md)해야 합니다. Azure CDN 기능에 대한 자세한 내용은 [CDN 개요](cdn-overview.md)를 참조하세요.

##Azure 미디어 서비스를 사용하여 캐시 헤더 구성

Azure 관리 포털 또는 Azure 미디어 서비스 API를 사용하여 캐시 헤더 값을 구성할 수 있습니다.

1. 관리 포털을 사용하여 캐시 헤더를 구성하려면 스트리밍 끝점 구성에서 [스트리밍 끝점을 관리하는 방법](../media-services/media-services-portal-manage-streaming-endpoints.md) 섹션을 참조하세요.
2. Azure 미디어 서비스 REST API, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)
3. Azure 미디어 서비스 .NET SDK, [StreamingEndpointCacheControl 속성](http://go.microsoft.com/fwlink/?LinkId=615302)

##캐시 구성 우선 순위

1. Azure 미디어 서비스에서 구성된 캐시 값은 기본값을 재정의합니다.
2. 수동 구성이 없으면 기본값이 적용됩니다.
3. 라이브 스트리밍에는 Azure 미디어 또는 Azure 저장소 구성에 상관없이 기본적으로 2초 캐시 헤더가 적용되며, 이 값은 재정의할 수 없습니다.

<!---HONumber=AcomDC_0921_2016-->