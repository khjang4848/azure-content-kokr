<properties 
	pageTitle="Application Insights에서 종속성 문제 진단" 
	description="종속성으로 인한 오류 및 성능 저하 찾기" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="awills"/>
 
# Application Insights에서 종속성 문제 진단


*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. 또는 웹 페이지 스크립트에서 서버를 다시 호출하는 AJAX 호출일 수 있습니다. Visual Studio Application Insights에서 응용 프로그램이 종속성을 기다리는 시간과 종속성 호출에 실패하는 빈도를 쉽게 확인할 수 있습니다.

## 사용 가능한 대상

기본적으로 종속성 모니터링은 현재 다음에서 사용할 수 있습니다.

* IIS 서버 또는 Azure에서 실행 중인 ASP.NET 웹앱 및 서비스
* [Java 웹앱](app-insights-java-agent.md)
* [웹 페이지](https://azure.microsoft.com/blog/ajax-collection-in-application-insights/)

장치 앱과 같은 다른 유형의 경우 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)를 사용하여 고유한 모니터를 작성할 수 있습니다.

기본적으로 종속성 모니터는 현재 다음 유형의 종속성에 대한 호출을 보고합니다.

* ASP.NET
 * SQL 데이터베이스
 * ASP.NET 웹 및 HTTP 기반 바인딩을 사용하는 WCF 서비스
 * 로컬 또는 원격 HTTP 호출
 * Azure DocumentDb, 테이블, Blob 저장소 및 큐
* Java
 * MySQL, SQL Server, PostgreSQL 또는 SQLite 등의 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) 드라이버를 통해 데이터베이스를 호출합니다.
* 웹 페이지
 * [AJAX 호출](app-insights-javascript.md)

다시 말하지만, 자체 SDK 호출을 작성하여 다른 종속성을 모니터링할 수 있습니다.

## 종속성 모니터링을 설정하려면

호스트 서버에 대한 적절한 에이전트를 설치합니다.

플랫폼 | 설치
---|---
IIS 서버 | [상태 모니터를 서버에 설치](app-insights-monitor-performance-live-website-now.md) 또는 [응용 프로그램을 .NET Framework 4.6 이상으로 업그레이드](http://go.microsoft.com/fwlink/?LinkId=528259)하고 앱에 [Application Insights SDK](app-insights-asp-net.md)를 설치합니다.
Azure 웹앱 | [Application Insights 확장](app-insights-azure-web-apps.md)
Java 웹 서버 | [Java 웹앱](app-insights-java-agent.md)
웹 페이지 | [JavaScript 모니터](app-insights-javascript.md)(웹 페이지 모니터링 외 추가 설정 없음)
Azure 클라우드 서비스 | [시작 작업 사용](app-insights-cloudservices.md#dependencies) 또는 [.NET Framework 4.6+ 설치](../cloud-services/cloud-services-dotnet-install-dotnet.md)  

IIS 서버용 상태 모니터는 Application Insights SDK를 사용하여 소스 프로젝트를 다시 빌드할 필요가 없습니다.

## 응용 프로그램 맵

응용 프로그램 맵은 응용 프로그램의 구성 요소 간에 종속성을 검색하는 시각화 보조 도구의 역할을 합니다.

![설정, 응용 프로그램 맵을 클릭합니다.](./media/app-insights-dependencies/08.png)

상자에서 관련 종속성 및 기타 차트로 이동할 수 있습니다.

작은 [x]를 클릭하여 하위 트리를 축소합니다.

[대시보드](app-insights-dashboards.md)에 맵을 고정하면 완벽하게 작동될 수 있습니다.

[자세히 알아봅니다](app-insights-app-map.md).

## <a name="diagnosis"></a> 웹 서버에서 종속성 성능 문제 진단

서버에서 요청 성능을 평가하려면:

![Application Insights의 응용 프로그램 개요 페이지에서 성능 타일을 클릭합니다.](./media/app-insights-dependencies/01-performance.png)

아래로 스크롤하여 요청 표를 확인합니다.

![평균 및 개수가 포함된 요청 목록](./media/app-insights-dependencies/02-reqs.png)

맨 위의 요청은 시간이 아주 오래 걸립니다. 시간이 어디에 소비되는지 확인해 보겠습니다.

개별 요청 이벤트를 보려면 해당 행을 클릭합니다.


![요청 항목 목록](./media/app-insights-dependencies/03-instances.png)

장기 실행 인스턴스를 클릭하면 세부 사항을 조사할 수 있습니다.

> [AZURE.NOTE] 인스턴스를 선택하려면 약간 아래로 스크롤합니다. 파이프라인의 대기 시간은 최상위 인스턴스의 데이터가 완료되지 않았음을 의미할 수 있습니다.

이 요청과 관련된 원격 종속성 호출까지 스크롤합니다.

![원격 종속성에 대한 호출 찾기, 특별한 기간 식별](./media/app-insights-dependencies/04-dependencies.png)

이 요청을 서비스하는 데 걸린 시간은 대부분 로컬 서비스 호출에 소요된 시간인 것 같습니다.

해당 행을 선택하여 자세한 정보를 봅니다.


![해당 원격 종속성을 클릭하여 원인 식별](./media/app-insights-dependencies/05-detail.png)

세부 정보에는 문제를 진단하는 데 충분한 정보가 들어 있습니다.



## 오류

실패한 요청이 있으면 차트를 클릭합니다.

![실패한 요청 차트 클릭](./media/app-insights-dependencies/06-fail.png)

요청 유형 및 요청 인스턴스를 클릭하여 실패한 원격 종속성 호출을 찾습니다.


![요청 유형을 클릭하고, 인스턴스를 클릭하여 동일한 인스턴스의 다른 보기로 이동하고, 클릭하여 예외 세부 정보를 표시합니다.](./media/app-insights-dependencies/07-faildetail.png)


## 사용자 지정 종속성 추적

표준 종속성 추적 모듈은 데이터베이스 및 REST API와 같은 외부 종속성을 자동으로 검색합니다. 하지만 일부 추가 구성 요소를 동일한 방식으로 취급할 수도 있습니다.

표준 모듈에 의해 사용되는 동일한[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)를 사용하여 종속성 정보를 보내는 코드를 작성할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

표준 종속성 추적 모듈을 해제하려는 경우, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 DependencyTrackingTelemetryModule에 대한 참조를 삭제합니다.


## Ajax

[웹 페이지](app-insights-javascript.md)를 참조하세요.


 

<!---HONumber=AcomDC_0914_2016-->