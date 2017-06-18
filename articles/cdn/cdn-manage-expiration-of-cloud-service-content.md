<properties
 pageTitle="Azure CDN에서 Azure Web Apps/Cloud Services, ASP.NET 및 IIS 콘텐츠의 만료를 관리하는 방법 | Microsoft Azure"
 description="Azure CDN에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법에 대해 설명합니다."
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# Azure CDN에서 Azure Web Apps/Cloud Services, ASP.NET 또는 IIS 콘텐츠의 만료를 관리하는 방법

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET 또는 IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure Storage Blob service](cdn-manage-expiration-of-blob-content.md)

TTL (time-to-live)이 경과할 때까지 원본 웹 서버에서 공개적으로 액세스할 수 있는 파일은 Azure CDN에 캐시될 수 있습니다. TTL은 원본 서버의 HTTP 응답에 있는 [*Cache-Control* 헤더](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)에 기반하여 결정됩니다. 이 문서에서는 Azure Web Apps, Azure Cloud Services, ASP.NET 응용 프로그램 및 IIS (Internet Information Services) 사이트에 대한 `Cache-Control` 헤더를 설정하는 방법을 설명하며, 이 헤더들은 모두 비슷하게 구성됩니다.

>[AZURE.TIP] 파일에 TTL을 설정하지 않을 수도 있습니다. 이 경우에 Azure CDN은 기본 TTL인 7일을 자동으로 적용합니다.
>
>파일과 다른 리소스에 대한 액세스 속도를 높이기 위해 Azure CDN이 작동하는 방법에 대한 자세한 내용은 [Azure CDN 개요](./cdn-overview.md)를 참조하세요.

## 구성 파일에 Cache-Control 헤더 설정

이미지, 스타일 시트 등 정적 콘텐츠에 대해서는 웹 응용 프로그램의 **applicationHost.config** 또는 **web.config** 파일을 수정함으로써 업데이트 빈도를 제어할 수 있습니다. 구성 파일의 **system.webServer\\staticContent\\clientCache** 요소에는 콘텐츠에 대한 `Cache-Control` 헤더가 설정됩니다. **web.config**의 구성 설정은 하위 폴더 수준에서 재정의된 경우를 제외하고는 폴더 및 그 하위 폴더의 모든 항목에 영향을 줍니다. 예를 들어 루트에서 모든 정적 콘텐츠가 3일간 캐시되지만 캐시 설정이 6시간인 추가 변수 콘텐츠가 포함된 하위 폴더가 있도록 기본 TTL(Time-To-Live)을 설정할 수 있습니다. **applicationHost.config** 파일은 사이트의 모든 응용 프로그램에 영향을 주지만, 응용 프로그램의 **web.config** 파일에서 재정의할 수 있습니다.

다음 XML은 최대 보존 기간을 3일로 지정하는 **clientCache** 설정 예제를 보여 줍니다.

```xml
<configuration>
	<system.webServer>
		<staticContent>
			<clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
		</staticContent>
	</system.webServer>
</configuration>
```

**UseMaxAge**를 지정하면 **CacheControlMaxAge** 특성에 지정된 값에 따라 `Cache-Control: max-age=<nnn>` 헤더를 응답에 추가합니다. **cacheControlMaxAge** 특성에 대한 시간 범위의 형식은 <days>.<hours>:<min>:<sec>입니다. **clientCache** 노드에 대한 자세한 내용은 [클라이언트 캐시 <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)를 참조하세요.

## 코드에 Cache-Control 헤더 설정

ASP.NET 응용 프로그램의 경우 **HttpResponse.Cache** 속성을 설정하면 CDN 캐싱 동작을 프로그래밍 방식으로 설정할 수 있습니다. **HttpResponse.Cache** 속성에 대한 자세한 내용은 [HttpResponse.Cache 속성](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 및 [HttpCachePolicy 클래스](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)를 참조하세요.

ASP.NET의 응용 프로그램 콘텐츠를 프로그래밍 방식으로 캐시하려면 HttpCacheability를 *Public*으로 설정하여 캐시 가능한 콘텐츠로 표시되도록 합니다. 또한 캐시 유효성 검사기가 설정되었는지 확인합니다. 캐시 유효성 검사기는 SetLastModified를 호출하여 설정된 마지막으로 수정한 날짜 타임스탬프 또는 SetETag를 호출하여 설정된 etag 값일 수 있습니다. 필요에 따라, SetExpires를 호출하여 캐시 만료 시간을 지정하거나 이 문서의 앞부분에서 설명한 기본 캐시 추론을 사용할 수도 있습니다.

예를 들어 1시간 동안의 콘텐츠를 캐시하려면 다음을 추가합니다.

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## 다음 단계

- [**clientCache** 요소에 대한 자세한 내용](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [**HttpResponse.Cache** 속성에 대한 자세한 내용](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)
- [**HttpCachePolicy 클래스**에 대한 자세한 내용](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)

<!---HONumber=AcomDC_0921_2016-->