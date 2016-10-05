<properties 
	pageTitle="Application Insights 데이터 보존 및 저장소" 
	description="보존 및 개인 정보 취급 방침" 
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
	ms.date="05/17/2016" 
	ms.author="awills"/>

# Application Insights 데이터 수집, 보존 및 저장소 

*Application Insights는 미리 보기 상태입니다.*

앱에 [Visual Studio Application Insights][start] SDK를 설치하는 경우 앱에 대한 원격 분석을 클라우드로 보냅니다. 담당하는 개발자는 전송되는 데이터가 정확한 내용, 데이터에 발생한 내용, 데이터를 제어할 수 있는 방법을 알고자 합니다. 특히 중요한 데이터를 보낼 수 있는지, 저장되었는지 및 얼마나 안전한지를 파악합니다.


우선 짧은 응답은 다음과 같습니다.

* "초기"를 실행하는 표준 원격 분석 모듈은 서비스에 중요한 데이터를 전송하지 않을 가능성이 있습니다. 원격 분석은 로드, 성능 및 사용 현황 메트릭, 예외 보고서 및 기타 진단 데이터와 관련되어 있습니다. 진단 보고서에 표시되는 기본 사용자 데이터는 URL입니다. 하지만 앱은 어떤 경우에도 URL에 일반 텍스트로 중요한 데이터를 배치하지 않아야 합니다.
* 추가 사용자 지정 원격 분석을 보내는 코드를 작성하여 사용 현황의 진단 및 모니터링을 도울 수 있습니다. (이 확장성은 Application Insights의 매우 유용한 기능입니다.) 실수로 개인 및 기타 중요한 데이터가 포함되도록 이 코드를 작성할 수 있습니다. 응용 프로그램이 이러한 데이터를 사용하여 작동하는 경우 작성하는 모든 코드에 강력한 검토 프로세스를 적용해야 합니다.
* 앱을 개발하고 테스트하는 동안 SDK에서 보낼 항목을 검사하기 쉽습니다. IDE 및 브라우저의 디버깅 출력 창에 데이터가 나타납니다.
* 데이터는 미국의 [Microsoft Azure](http://azure.com) 서버에 저장됩니다. 그러나 앱은 어디서나 실행할 수 있습니다. Azure는 [강력한 보안 프로세스를 가지고 광범위한 규정 준수 표준을 충족](https://azure.microsoft.com/support/trust-center/)합니다. 사용자와 지정된 팀만 데이터에 액세스할 수 있습니다. Microsoft 직원은 지식으로 제한된 특정 상황에서만 해당 데이터에 제한된 액세스 권한을 갖을 수 있습니다. 서버에 없더라도 전송 중에 암호화됩니다.

이 문서의 나머지 부분에서는 이러한 대답에 대해 더 자세하게 설명합니다. 자체 포함되도록 설계되어 소속 팀에 속하지 않은 동료에게 표시할 수 있습니다.


## Application Insights란?

[Visual Studio Application Insights][start]는 라이브 응용 프로그램의 성능 및 가용성을 향상시키는 Microsoft에서 제공하는 서비스입니다. 테스트 중인 경우 및 게시하거나 배포한 후에 실행 중인 모든 시간 동안 응용 프로그램을 모니터링합니다. 예를 들어 Application Insights는 많은 사용자를 가져오는 시간, 앱이 얼마나 반응하는지, 종속된 외부 서비스에서 얼마나 잘 제공되는지를 보여주는 차트 및 테이블을 만듭니다. 충돌, 오류 또는 성능 문제가 있는 경우 세부 정보에서 원격 분석 데이터를 통해 검색하여 원인을 진단할 수 있습니다. 그리고 앱의 성능과 가용성에 변경 사항이 있는 경우 서비스는 사용자에게 전자 메일을 보냅니다.

이 기능을 가져오기 위해 응용 프로그램에서 해당 코드의 일부가 되는 Application Insights SDK를 설치합니다. 앱이 실행 중일 때 SDK는 작업을 모니터링하고 Application Insights 서비스에 원격 분석을 보냅니다. [Microsoft Azure](http://azure.com)에서 호스팅하는 클라우드 서비스입니다. (하지만 Application Insights는 Azure에서 호스팅되는 서비스가 아닌 모든 응용 프로그램에 대해 작동합니다.)

![앱의 SDK Application Insights은 서비스에 원격 분석을 보냅니다.](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights 서비스는 원격 분석 데이터를 저장하고 분석합니다. 저장된 원격 분석을 통해 분석 또는 검색을 보려면 Azure 계정에 로그인하고 응용 프로그램에 Application Insights 리소스를 엽니다. 또한 팀의 다른 구성원 또는 지정된 Azure 구독자와 데이터에 대한 액세스를 공유할 수 있습니다.

예를 들어 데이터베이스 또는 외부 도구에 Application Insights 서비스에서 내보낸 데이터가 있을 수 있습니다. 서비스에서 가져와야 하는 특수 키를 사용하여 각 도구를 제공합니다. 필요한 경우 키를 해지할 수 있습니다.

Application Insights SDK는 다음과 같은 응용 프로그램 형식에 사용할 수 있습니다. 고유한 J2EE 또는 ASP.NET 서버 또는 Azure에서 호스팅된 웹 서비스, 웹 클라이언트 즉, 웹 페이지에서 실행되는 코드, 데스크톱 앱 및 서비스, Windows Phone, iOS 및 Android 같은 장치 앱. 모두 동일한 서비스에 원격 분석을 보냅니다.

## 어떤 데이터를 수집하나요?

### 데이터는 어떻게 수집되나요?

세 가지 데이터 원본이 있습니다.

* [개발 시](app-insights-asp-net.md) 또는 [런타임 시](app-insights-monitor-performance-live-website-now.md) 앱과 통합하는 SDK가 있습니다. 다른 응용 프로그램 형식에 대한 여러 SDK가 있습니다. 또한 페이지와 함께 최종 사용자의 브라우저에 로드하는 [웹 페이지에 대한 SDK](app-insights-javascript.md)가 있습니다.

 * 각 SDK에는 다양한 [모듈](app-insights-configuration-with-applicationinsights-config.md)이 있으며 이는 서로 다른 기술을 사용하여 다른 형식의 원격 분석 데이터를 수집합니다.
 * 개발 시 SDK를 설치하면 표준 모듈 외에도 API를 사용하여 사용자 고유의 원격 분석을 보낼 수 있습니다. 이 사용자 지정 원격 분석은 전송하려는 데이터를 포함할 수 있습니다.
* 또한 일부 웹 서버에는 앱과 함께 실행하고 CPU, 메모리 및 네트워크 선점에 대한 원격 분석을 보내는 에이전트가 있습니다. 예를 들어 Azure VM, Docker 호스트 및 [J2EE 서버](app-insights-java-agent.md)에는 이러한 에이전트가 있을 수 있습니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md)는 정기적으로 웹앱에 요청을 전송하는 Microsoft에서 실행되는 프로세스입니다. 결과는 Application Insights 서비스에 전송됩니다.

### 어떤 종류의 데이터를 수집하나요?

주요 범주는 다음과 같습니다.

* [웹 서버 원격 분석](app-insights-asp-net.md) - HTTP가 요청합니다. URI, 요청, 응답 코드, 클라이언트 IP 주소를 처리하는 데 걸린 시간입니다. 세션 ID.
* [웹 페이지](app-insights-javascript.md) -페이지, 사용자 및 세션 수입니다. 페이지 로드 시간. 예외. Ajax 호출.
* 성능 카운터 - 메모리, CPU, IO, 네트워크 선점입니다.
* 클라이언트 및 서버 컨텍스트 - OS, 로캘, 장치 형식, 브라우저, 화면 해상도입니다.
* [예외](app-insights-asp-net-exceptions.md) 및 작동 중단 - **스택 덤프**, 작성 ID, CPU 형식입니다.
* [종속성](app-insights-asp-net-dependencies.md) - REST, SQL, AJAX와 같은 외부 서비스를 호출합니다. URI 또는 연결 문자열, 시간, 성공, 명령입니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md) - 테스트, 단계, 응답의 기간입니다.
* [추적 로그](app-insights-search-diagnostic-logs.md) 및 [사용자 지정 원격 분석](app-insights-api-custom-events-metrics.md) - **로그 또는 원격 분석에 코딩한 것**입니다.

[자세한 내용](#data-sent-by-application-insights).

## 수집되는 항목을 어떻게 확인할 수 있나요?

Visual Studio를 사용하여 앱을 개발하는 경우 디버그(F5) 모드에서 앱을 실행합니다. 원격 분석은 출력 창에 나타납니다. 여기에서 복사하고 쉬운 검사를 JSON으로 형식으로 지정할 수 있습니다.

![](./media/app-insights-data-retention-privacy/06-vs.png)

진단 창에 보다 읽기 쉬운 보기도 있습니다.

웹 페이지의 경우 브라우저의 디버깅 창을 엽니다.

![F12 키를 누르고 네트워크 탭을 엽니다.](./media/app-insights-data-retention-privacy/08-browser.png)

### 전송하기 전에 코드를 작성하여 원격 분석을 필터링할 수 있습니까?

[원격 분석 프로세서 플러그 인](app-insights-api-filtering-sampling.md)을 작성하여 수행할 수 있습니다.



## 데이터가 얼마 동안 보존되나요? 

원시 데이터 요소(즉, 진단 검색에서 검사할 수 있는 항목)는 7일 동안 보관됩니다. 이보다 더 오래 데이터를 보존해야 하는 경우 [연속 내보내기](app-insights-export-telemetry.md)를 사용하여 해당 데이터를 저장소 계정에 복사할 수 있습니다.

집계 데이터(즉, 메트릭 탐색기에 표시되는 개수, 평균 및 기타 통계 데이터)는 30일 동안 1분 단위로 보존되고, 적어도 90일 동안 형식에 따라 1시간 또는 1일 단위로 보존됩니다.


## 데이터에 액세스할 수 있는 사용자는 누구인가요?

데이터는 사용자 및 조직 계정이 있는 경우 팀 멤버에게 표시됩니다.

사용자와 팀 멤버는 데이터를 내보낼 수 있으며, 다른 위치로 복사하고 다른 사람에게 전달할 수 있습니다.

#### Microsoft는 내 앱이 Application Insights로 보내는 정보로 무엇을 하나요?

Microsoft는 서비스를 제공하기 위한 목적으로만 데이터를 사용합니다.


## 데이터가 저장되는 위치는 어디인가요? 

* 미국입니다.

#### 유럽 등의 다른 곳에 저장할 수 있나요? 

* 현재는 암호화되지 않습니다.

#### 내 앱을 미국에 호스트해야 한다는 뜻인가요?

* 아니요. 응용 프로그램은 자체 온-프레미스 호스트 또는 클라우드의 어디에서나 실행할 수 있습니다.

## 내 데이터는 어느 정도 안전한가요?  

Application Insights는 미리 보기 상태의 Azure 서비스입니다. 미리 보기 상태에서는 [Azure 보안, 개인 정보 및 규정 준수 백서](http://go.microsoft.com/fwlink/?linkid=392408)에 설명된 정책에 따라 데이터를 보호하기 위해 노력합니다.


데이터는 Microsoft Azure 서버에 저장됩니다. Azure 포털 계정의 경우 [Azure 보안, 개인 정보 및 규정 준수 문서](http://go.microsoft.com/fwlink/?linkid=392408)에 계정 제한 사항이 설명되어 있습니다. Visual Studio Team Services 포털의 경우 [Visual Studio Team Services 데이터 보호](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf) 문서가 적용됩니다.

Microsoft 직원의 사용자 데이터에 대한 액세스는 제한되어 있습니다. Microsoft에서는 사용자가 허락한 경우에만, 그리고 Application Insights 사용을 지원하는 데 필요한 경우에 사용자 데이터에 액세스합니다.

모든 고객의 응용 프로그램에 대한 집계 데이터(예: 데이터 속도 및 평균 추적 크기)는 Application Insights를 개선하는 데 사용됩니다.

#### 다른 사람의 원격 분석이 내 Application Insights 데이터에 방해가 될 수 있나요?

웹 페이지의 코드에서 찾을 수 있는 계측 키를 사용하여 계정에 추가 원격 분석을 보낼 수 있습니다. 추가 데이터가 충분하면 메트릭이 앱의 성능 및 사용을 올바르게 나타냅니다.

다른 프로젝트와 코드를 공유하는 경우 계측 키를 제거해야 합니다.

## 데이터는 암호화되나요? 

현재 서버 내에서 암호화되지 않습니다.

모든 데이터는 데이터 센터 간에 이동할 때 암호화됩니다.

#### 내 응용 프로그램에서 Application Insights 서버로 전송 중에 데이터가 암호화되나요?

예. 웹 서버, 장치 및 HTTPS 웹 페이지를 포함하여 거의 모든 SDK에서 https를 사용하여 포털로 데이터를 보냅니다. 유일한 예외는 일반 HTTP 웹 페이지에서 전송된 데이터입니다.

## 개인 식별이 가능한 정보

#### PII(개인 식별이 가능한 정보)가 Application Insights에 전송될 수 있나요? 

예, 전송할 수 있습니다.

일반 지침:

* 대부분의 표준 원격 분석(즉, 코드를 작성하지 않고 전송된 원격 분석)은 명시적 PII를 포함하지 않습니다. 그러나 이벤트 컬렉션에서 유추하여 개인을 식별할 수 있습니다.
* 예외 및 추적 메시지는 PII를 포함할 수 있습니다.
* 사용자 지정 원격 분석(즉, API 또는 로그 추적을 사용하여 코드로 작성하는 TrackEvent와 같은 호출)은 선택한 모든 데이터를 포함할 수 있습니다.


이 문서의 끝에 있는 표에 수집되는 데이터에 대한 자세한 설명이 포함되어 있습니다.



#### PII와 관련된 법률 및 규정을 준수해야 하나요?

예. 데이터의 수집 및 사용은 법률 및 규정과 Microsoft Online Services 사용 약관을 준수해야 합니다.

응용 프로그램이 수집하는 데이터 및 데이터 사용 방법에 대해 고객에게 적절하게 알려야 합니다.

#### 내 사용자가 Application Insights를 끌 수 있나요?

직접 끌 수는 없습니다. 사용자가 Application Insights를 끄기 위해 작동할 수 있는 스위치는 제공되지 않습니다.

그러나 응용 프로그램에서 이러한 기능을 구현할 수 있습니다. 모든 SDK에는 원격 분석 수집을 끄는 API 설정이 포함되어 있습니다.

#### 내 응용 프로그램이 의도하지 않게 중요한 정보를 수집하고 있습니다. Application Insights에서 이 데이터가 보존되지 않도록 삭제할 수 있나요?

Application Insights는 데이터를 필터링하거나 삭제하지 않습니다. 데이터를 적절하게 관리하여 이러한 데이터가 Application Insights로 전송되지 않도록 해야 합니다.



## Application Insights에서 보내는 데이터

SDK는 플랫폼마다 다르며, 설치할 수 있는 여러 구성 요소가 있습니다. [Application Insights - 시작][start]을 참조하세요. 각 구성 요소마다 다른 데이터를 보냅니다.

#### 다양한 시나리오에서 전송되는 데이터 클래스

사용자 작업 | 수집되는 데이터 클래스(다음 표 참조)
---|---
[.NET 웹 프로젝트에 Application Insights SDK 추가][greenbrown] | ServerContext<br/>유추<br/>성능 카운터<br/>요청<br/>**예외**<br/>세션<br/>사용자
[IIS에서 상태 모니터 설치][redfield]|종속성<br/>ServerContext<br/>유추<br/>성능 카운터
[Java 웹앱에 Application Insights SDK 추가][java]|ServerContext<br/>유추<br/>요청<br/>세션<br/>사용자
[웹 페이지에 JavaScript SDK 추가][client]|ClientContext <br/>유추<br/>페이지<br/>ClientPerf<br/>Ajax
[기본 속성 정의][apiproperties]|모든 표준 및 사용자 지정 이벤트의 **속성**
[호출 TrackMetric][api]|숫자 값<br/>**속성**
[호출 추적*][api]|이벤트 이름<br/>**속성**
[호출 TrackException][api]|**예외**<br/>스택 덤프<br/>**속성**
SDK는 데이터를 수집할 수 없습니다. 예: <br/> - 성능 카운터에 액세스할 수 없음<br/> - 원격 분석 이니셜라이저 예외 | SDK 진단
 

[다른 플랫폼에 대한 SDK][platforms]의 경우 해당 문서를 참조하세요.



#### 수집되는 데이터 클래스

수집되는 데이터 클래스 | 포함(전체 목록 아님) 
---|---
**속성**|**임의 데이터 - 코드에 의해 결정됨**
DeviceContext |ID, IP, 로캘, 장치 모델, 네트워크, 네트워크 종류, OEM 이름, 화면 해상도, 역할 인스턴스, 역할 이름, 장치 유형
ClientContext |OS, 로캘, 언어, 네트워크, 창 해상도
세션 | 세션 ID
ServerContext |컴퓨터 이름, 로캘, OS, 장치, 사용자 세션, 사용자 컨텍스트, 작업 
유추 |IP 주소, 타임스탬프, OS, 브라우저에서 지리적 위치 유추
메트릭 | 메트릭 이름 및 값
이벤트 | 이벤트 이름 및 값
PageViews | URL 및 페이지 이름이나 화면 이름
클라이언트 성능 | URL/페이지 이름, 브라우저 로드 시간
Ajax | 웹 페이지에서 서버로 HTTP 호출
요청 |URL, 기간, 응답 코드
종속성|유형(SQL, HTTP,...), 연결 문자열 또는 URI, 동기/비동기, 기간, 성공, SQL 문(상태 모니터 사용)
**예외** | 유형, **메시지**, 호출 스택, 원본 파일 및 줄 번호, 스레드 ID
충돌 | 프로세스 ID, 부모 프로세스 ID, 충돌 스레드 ID; 응용 프로그램 패치, ID, 빌드; 예외 유형, 주소, 이유; 난독 처리된 기호 및 레지스터, 이진 시작 및 끝 주소, 이진 이름 및 경로, CPU 종류
추적 | **메시지** 및 심각도 수준
성능 카운터 | 프로세서 시간, 사용 가능한 메모리, 요청 속도, 예외 속도, 프로세스 전용 바이트, IO 속도, 요청 기간, 요청 큐 길이
Availability | 웹 테스트 응답 코드, 각 테스트 단계, 테스트 이름, 타임 스탬프, 성공, 응답 시간, 테스트 위치의 기간
SDK 진단 | 추적 메시지 또는 예외 

[ApplicationInsights.config를 편집하여 일부 데이터를 해제][config]할 수 있습니다.


## 크레딧

이 제품에는 MaxMind에서 작성된 GeoLite2 데이터를 포함하며 [http://www.maxmind.com](http://www.maxmind.com)에 있습니다.

## <a name="video"></a>동영상

#### 소개

<div class="wa-video-container" data-control="video-container" data-slug="application-insights-introduction"> <a href="/en-us/documentation/videos/application-insights-introduction/" data-control="video" data-expanding="false" class="wa-video-thumbnail" title="Application Insights Introduction" data-ch9="//channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Application-Insights-Introduction/player/" data-caption="" data-event="videopage-clicked-videothumbnail" data-event-property="application-insights-introduction"> <img src="https://sec.ch9.ms/ch9/8b8e/e924e562-d966-4f38-8410-b19ed0a28b8e/AppInsightsIntro_960.jpg" alt="Application Insights Introduction" class="thumbnail"> <img class="play-icon" src="/cdn/cvt-a29a0e789afe7f47464f393c011b53f210c511ee5fed2c4c9c0633442f30d6ec/images/icon/VideoPlay.svg"> <span class="metadata"> <span class="date"> <span>08-05-2014</span> <span>03 min, 14 sec</span> </span> </span> </a> </div>

#### 시작

<div class="wa-video-container" data-control="video-container" data-slug="getting-started-with-application-insights"> <a href="/en-us/documentation/videos/getting-started-with-application-insights/" data-control="video" data-expanding="false" class="wa-video-thumbnail" title="Getting Started with Application Insights" data-ch9="//channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Getting-Started-with-Application-Insights/player/" data-caption="" data-event="videopage-clicked-videothumbnail" data-event-property="getting-started-with-application-insights"> <img src="https://sec.ch9.ms/ch9/3037/37da1ff1-7691-424e-8817-179e190c3037/AIGettingStarted_960.jpg" alt="Getting Started with Application Insights" class="thumbnail"> <img class="play-icon" src="/cdn/cvt-a29a0e789afe7f47464f393c011b53f210c511ee5fed2c4c9c0633442f30d6ec/images/icon/VideoPlay.svg"> <span class="metadata"> <span class="date"> <span>08-05-2014</span> <span>05 min, 28 sec</span> </span> </span> </a> </div>




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0907_2016-->