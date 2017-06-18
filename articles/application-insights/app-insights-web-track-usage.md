<properties 
	pageTitle="Application Insights를 사용한 웹 응용 프로그램의 사용 현황 분석" 
	description="Application Insights을 사용하여 웹앱에 대한 사용량 분석 개요" 
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
	ms.date="06/12/2016" 
	ms.author="awills"/>
 
# Application Insights를 사용한 웹 응용 프로그램의 사용 현황 분석

사람들이 사용자의 응용 프로그램을 어떻게 사용하는지 알면 사람들에게 가장 중요한 시나리오로 개발 작업을 하는 데 초점을 맞출 수 있으며, 사람들이 달성하기 더 쉽거나 어려워하는 목표에 대한 통찰력을 얻을 수 있습니다.

Visual Studio Application Insights는 2단계 사용 현황 추적을 제공합니다.

* **사용자, 세션 및 페이지 보기 데이터** - 독점적으로 제공합니다.
* **사용자 지정 원격 분석** - 앱의 사용자 경험을 통해 사용자를 추적하는 [코드를 작성][api]합니다.

## 설치

[Azure 포털](https://portal.azure.com)에서 Application Insights 리소스를 열고, 빈 브라우저 페이지 로드 차트를 클릭하고 설치 지침을 따릅니다.

[자세히 알아보기](app-insights-javascript.md)


## 내 웹 응용 프로그램의 인기는 어느 정도인가요?

[Azure 포털][portal]에 로그인하고 응용 프로그램 리소스를 찾은 다음 사용을 클릭합니다.

![](./media/app-insights-web-track-usage/14-usage.png)

* **사용자:** 차트의 시간 범위에 따른 고유한 활성 사용자 수입니다.
* **세션:** 활성 세션 수입니다.
* **페이지 보기** trackPageView() 호출 수를 계산합니다. 일반적으로 각 웹 페이지에서 한 번 호출됩니다.

보다 자세한 정보를 확인하려면 차트를 클릭합니다. 차트의 시간 범위를 변경할 수 있습니다.

### 내 사용자는 어디에 살고 있나요?

사용 블레이드에서 사용자 차트를 클릭하여 자세한 정보를 확인합니다.

![사용 현황 블레이드에서 사용자 차트를 클릭 합니다.](./media/app-insights-web-track-usage/02-sessions.png)
 
### 어떤 브라우저 또는 운영 체제를 사용하나요?

브라우저, 운영체제 또는 도시와 같은 속성으로 데이터를 그룹화(구분)합니다.

![단일 메트릭을 보여주는 차트를 선택하고 그룹화로 전환한 다음 속성 선택](./media/app-insights-web-track-usage/03-browsers.png)


## 세션

세션은 Application Insights의 기본 개념이며 요청, 페이지 보기, 예외 또는 사용자가 특정 사용자 세션을 직접 코딩하는 사용자 지정 이벤트 등 모든 원격 분석 이벤트를 연결하려고 합니다.

장치 특성, 지리적 위치, 운영 체제 등의 각 세션에 대한 풍부한 컨텍스트 정보가 수집됩니다.

클라이언트와 서버를 모두 계측하는 경우([ASP.NET][greenbrown] 또는 [J2EE][java]), 양쪽 모두에 대한 이벤트를 상호 관련시킬 수 있도록 클라이언트와 서버 간에 세션 id를 전파합니다.

[문제를 진단][diagnostic]할 때 모든 요청, 기록된 이벤트, 예외 또는 추적을 포함하여 문제가 발생한 세션과 관련된 모든 원격 분석을 찾을 수 있습니다.

세션은 장치, 운영 체제 또는 위치 같은 컨텍스트의 인기에 대한 유용한 척도를 제공합니다. 예를 들어 장치별로 그룹화된 세션의 개수를 표시하면 해당 장치가 사용자의 앱에서 사용되는 빈도를 페이지 보기를 세는 것보다 더 정확히 파악할 있습니다. 이 정보는 장치별 문제의 심사에 유용한 입력이 될 수 있습니다.


#### 세션이란?

세션은 사용자와 응용 프로그램 간의 단일 발생을 나타냅니다. 가장 간단한 형태의 세션은 앱을 시작하는 사용자로 시작하고 사용자가 앱을 떠날 때 완료됩니다. 웹 응용 프로그램의 경우, 기본적으로 세션은 비활성 30 분 후 또는 24 시간 활동 후에 종료합니다.

코드 조각을 편집하여 이러한 기본값을 변경할 수 있습니다.

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs`: 사용자의 비활성으로 인해 세션을 만료하는 시간(밀리초 단위). 기본값: 30분.
* `sessionExpirationMs`: 최대 세션 길이(밀리초 단위). 이 시간 후 사용자가 활성 상태로 남아 있으면 다른 세션이 계산됩니다. 기본값: 24시간.

**세션 기간**은 세션의 첫 번째와 마지막 원격 분석 항목 사이의 시간 범위를 기록하는 [메트릭][metrics]입니다. (시간 제한 기간은 포함하지 않습니다.)

특정 간격의 **세션 수**는 이 간격 중에 일부 활동이 있는 고유한 세션 수라고 정의됩니다. 지난 주에 대한 일별 세션 수와 같이 오랜 시간 범위를 보는 경우, 일반적으로 이 세션 수는 총 세션 수와 같습니다.

그러나 시간별 단위 같은 더 짧은 시간 범위를 탐색하는 경우, 여러 시간에 걸친 긴 세션은 세션이 활성화된 각 시간에 대해 계산됩니다.

## 사용자 및 사용자 수


각 사용자 세션은 고유한 사용자 id와 연결됩니다.

기본적으로 사용자는 쿠키를 배치하여 식별됩니다. 여러 브라우저 또는 장치를 사용하는 사용자는 두 번 이상 계산됩니다. ([인증된 사용자](#authenticated-users)를 참조하세요.)


특정 간격의 **사용자 수** 메트릭은 이 간격 중에 기록된 활동이 있는 고유한 사용자 수라고 정의됩니다. 따라서 단위가 한 시간보다 더 짧도록 시간 범위를 설정한 경우, 긴 세션을 가진 사용자는 여러 번 계산됩니다.

**새 사용자**는 이 간격 중에 앱에 대한 첫 번째 세션이 발생한 사용자 수를 계산합니다. 쿠키에 의해 사용자별로 계산하는 기본 방법을 사용하는 경우, 자신의 쿠키를 삭제했거나 새 장치 또는 브라우저를 사용하여 앱에 처음 액세스하는 사용자 수도 이 사용자 수에 포함됩니다. ![사용 현황 블레이드에서 사용자 차트를 클릭하여 새 사용자를 검사합니다.](./media/app-insights-web-track-usage/031-dual.png)

### 인증된 사용자

웹앱에서 사용자 로그인을 허용하는 경우 Application Insights에 고유한 사용자 식별자를 제공하여 보다 정확한 개수를 얻을 수 있습니다. 앱에서 사용한 것과 동일한 ID 또는 해당 이름이 아니어도 됩니다. 앱이 사용자를 식별하는 즉시 다음 코드를 사용합니다.


*클라이언트의 JavaScript*

      appInsights.setAuthenticatedUserContext(userId);

앱이 사용자를 계정으로 그룹화하는 경우 계정 식별자를 전달할 수도 있습니다.

      appInsights.setAuthenticatedUserContext(userId, accountId);

사용자 및 계정 ID에 공백이나 `,;=|` 문자가 포함되면 안 됩니다.


[메트릭 탐색기](app-insights-metrics-explorer.md)에서 **인증된 사용자** 및 **계정** 차트를 만들 수 있습니다.

## 종합 트래픽

종합 트래픽은 가용성 및 부하 테스트, 검색 엔진 크롤러 및 다른 에이전트의 요청을 포함합니다.

Application Insights는 종합 트래픽을 자동으로 결정하고 분류한 다음 적절히 표시하려고 합니다. 대부분의 경우 종합 트래픽은 JavaScript SDK를 호출하지 않으므로, 이 활동은 사용자 및 세션 계산에서 제외됩니다.

그러나 Application Insights [웹 테스트][availability]의 경우, 사용자 ID는 POP 위치에 따라 자동으로 설정되며, 세션 ID는 테스트 실행 ID에 따라 설정됩니다. 기본 보고서의 경우, 종합 트래픽은 이러한 사용자 및 세션을 제외하도록 기본적으로 필터링됩니다. 그러나 종합 트래픽을 포함하면 전체 사용자 및 세션 수가 약간 증가할 수 있습니다.
 
## 페이지 사용

가장 인기 있는 페이지의 분석과 함께 보다 확대된 버전을 가져오려면 페이지 보기 차트를 클릭합니다.


![개요 블레이드에서 페이지 보기 차트 클릭](./media/app-insights-web-track-usage/05-games.png)
 
위의 예시는 게임 웹 사이트에서 나온 것입니다. 여기에서 바로 볼 수 있습니다.

* 사용량은 지난 주보다 개선되지 않았습니다. 검색 엔진 최적화에 대해 생각해볼 수 있을까요?
* 홈 페이지보다 게임 페이지를 보는 사람들이 줄어들었습니다. 왜 홈 페이지가 사람들이 게임을 플레이하도록 끌어당기지 못할까요?
* '낱말 맞추기'는 가장 인기 있는 게임입니다. 새로운 아이디어 및 개선 사항에 우선 순위를 두어야 합니다.

## 사용자 지정 추적

별도의 웹 페이지에서 각각의 게임을 구현하는 대신, 웹 페이지에서 대부분 Javascript로 코딩된 기능을 사용하여 단일 페이지 앱으로 리펙터링하기로 결정한다고 가정해 봅니다. 이를 통해 사용자는 한 게임에서 다른 게임으로 빠르게 전환할 수 있으며 한 페이지에서 여러 게임도 할 수 있습니다.

하지만 별도의 웹 페이지에서 게임할 때와 완전히 동일한 방법으로 각각의 게임이 열리는 횟수를 로그하기 위해 여전히 Application Insights를 사용하고자 합니다. 간단합니다. 열리는 새 '페이지'를 기록하려는 JavaScript에 원격 분석 모듈에 대한 호출을 삽입하면 됩니다.

	appInsights.trackPageView(game.Name);

## 사용자 지정 이벤트

사용자 지정 원격 분석을 작성하여 구체적 이벤트를 기록합니다. 특히 단일 페이지 앱에서는 사용자가 얼마나 자주 특정 작업을 수행하는지, 특정 목표를 달성하는지 알고 싶을 것입니다.

    appInsights.trackEvent("GameEnd");

예를 들어, 링크 클릭을 로그하려면:

    <a href="target.htm" onclick="appInsights.trackEvent('linkClick');return true;">my link</a>


## 사용자 지정 이벤트 조회수 개수

메트릭 탐색기를 열고 차트를 추가하여 이벤트를 표시합니다. 이름별 세그먼트:

![하나의 메트릭만 보여주는 차트를 선택합니다. 그룹화로 전환합니다. 속성을 선택합니다. 일부 속성은 사용할 수 없습니다.](./media/app-insights-web-track-usage/06-eventsSegment.png)



## 특정 이벤트 자세히 알아보기

일반적인 세션이 진행되는 방식에 대해 더 잘 이해하기 위해 특정 이벤트 종류를 포함하는 특정 사용자 세션에 초점을 맞추고자할 수 있습니다.

이 예제에서는 사용자가 실제로 게임을 시작하지 않고 로그아웃하는 경우 호출되는 "NoGame" 사용자 지정 이벤트를 코딩했습니다. 사용자는 왜 그렇게 할까요? 특정 항목을 자세히 다루면 실마리를 얻게 될 것입니다.

앱에서 수신한 사용자 지정 이벤트는 개요 블레이드에 이름별로 나열됩니다.


![개요 블레이드에서 사용자 지정 이벤트 종류 중 하나를 클릭합니다.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
관심 있는 이벤트를 클릭하고 최근에 발생한 특정 항목을 선택합니다.


![요약 차트 아래의 목록에서 이벤트를 클릭합니다.](./media/app-insights-web-track-usage/08-searchEvents.png)
 
해당하는 특정 NoGame 이벤트가 발생한 세션에 대한 모든 원격 분석을 살펴봅시다.


!['세션에 대한 모든 원격 분석'을 클릭합니다.](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
예외 사항이 없었으므로 사용자는 일부 오류로 인해 플레이하지 못한 것입니다.
 
이 세션에 대한 페이지 보기를 제외한 모든 종류의 원격 분석을 필터링할 수 있습니다.


![](./media/app-insights-web-track-usage/10-filter.png)
 
그리고 이제 단순히 최신 점수를 확인하기 위해 이 사용자가 로그인한 것을 확인할 수 있습니다. 이를 더 쉽게 하기 위해 사용자 스토리 개발을 고려해야 할 수도 있습니다. (그리고 이 특정 스토리가 발생할 때 보고할 사용자 지정 이벤트를 구현해야 합니다.)

## 속성을 사용하여 데이터를 필터링, 검색 및 분할
이벤트에 임의의 태그 및 숫자 값을 연결할 수 있습니다.
 

*클라이언트의 JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*서버의 C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*서버의 VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

동일한 방식으로 페이지 뷰에 속성을 연결합니다.

*클라이언트의 JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

진단 검색에서 이벤트의 개별 항목을 클릭하여 속성을 봅니다.


![이벤트 목록에서 이벤트를 열고 '...'를 클릭하여 더 많은 속성 보기](./media/app-insights-web-track-usage/11-details.png)
 
검색 필드를 사용하여 특정 속성 값이 포함된 이벤트 항목을 볼 수 있습니다.


![검색 필드에 값 입력](./media/app-insights-web-track-usage/12-searchEvents.png)


## A | B 테스트

기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 태그를 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 응용 프로그램이 전송하는 모든 원격 분석 메시지에 추가됩니다.

그런 다음 Application Insights 포털에서 태그로 데이터를 필터링 및 그룹화(구분)할 수 있게 되므로 다른 버전과 비교할 수 있습니다.

*서버의 C#*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*서버의 VB*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

개별 원격 분석에서 기본값을 재정의할 수 있습니다.

유니버설 아니셜라이저를 설정하여 새로운 모든 TelemetryClients는 사용자 컨텍스트를 자동으로 사용할 수 있습니다.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Global.asax.cs 같은 앱 이니셜라이저에서:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## 빌드 - 측정 - 학습

분석을 사용하면 문제를 해결하기 위해 생각한 무언가가 아닌 개발 주기의 통합 부분이 됩니다. 다음은 몇 가지 팁입니다.

* 응용 프로그램의 주요 메트릭을 결정합니다. 가능한 많은 사용자를 원하시나요? 아니면 매우 만족하는 소규모의 사용자를 원하시나요? 방문수 또는 매출을 최대화하겠습니까?
* 각 스토리를 측정하도록 계획합니다. 새 사용자 스토리 또는 기능을 스케치하거나 기존 내용을 업데이트할 계획을 세우면 항상 변화의 성공을 어떻게 측정할지 생각해 봅니다. 코딩을 시작하기 전에 "작동한다면 우리 메트릭에 어떠한 영향을 미칠까? 새 이벤트를 추적해야 하는가?"라고 물어봅니다. 물론, 기능이 실시간이면 분석 내용을 자세히 보고 결과에 대한 조치를 취해야 합니다.
* 기타 메트릭을 주요 메트릭과 연결합니다. 예를 들어 "즐겨찾기" 기능을 추가하는 경우 사용자가 즐겨찾기를 추가하는 빈도를 알고 싶습니다. 하지만 사용자가 즐겨찾기를 다시 방문하는 빈도를 아는 데에 더 많은 관심이 있을 수 있습니다. 또한 가장 중요한 것은 궁극적으로 즐겨찾기를 사용하는 고객이 사용자의 제품을 더 구입하느냐입니다.
* 카나리아 테스트입니다. 새로운 기능을 일부 사용자에게만 표시할 수 있는 기능 스위치를 설정합니다. Application Insights를 사용하여 새로운 기능이 예상하는 방식으로 사용되고 있는지 확인합니다. 조정한 다음 더 광범위한 대상에게 이를 릴리스합니다.
* 사용자에게 이야기하세요! 분석은 자체적으로 충분하지 않지만 좋은 고객 관계 유지를 보완합니다.


## 참조

* [API 사용 - 개요][api]
* [JavaScript API 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## 비디오

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0914_2016-->