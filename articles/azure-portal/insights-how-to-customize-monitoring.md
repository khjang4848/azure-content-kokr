<properties
	pageTitle="Microsoft Azure의 메트릭 개요 | Microsoft Azure"
	description="Azure에서 모니터링 차트를 사용자 지정하는 방법에 대해 알아봅니다."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="robb"/>

# Microsoft Azure의 메트릭 개요

모든 Azure 서비스는 서비스의 상태, 성능, 가용성 및 사용량을 모니터링할 수 있게 해주는 주요 메트릭을 추적합니다. Azure 포털에서 이러한 메트릭을 볼 수 있으며, [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 전체 메트릭 집합에 액세스할 수도 있습니다.

일부 서비스의 경우 메트릭을 확인하기 위해 진단을 켜야 할 수도 있습니다. 가상 컴퓨터와 같은 기타 서비스의 경우 기본 메트릭 집합이 표시되지만 전체 집합 고빈도 메트릭을 사용하도록 설정해야 합니다. 자세히 알아보려면 [모니터링 및 진단 사용](insights-how-to-use-diagnostics.md)을 참조하세요.

## 모니터링 차트 사용

선택한 기간에 대한 메트릭을 차트로 작성할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 **찾아보기**를 클릭한 후 모니터링할 리소스를 클릭합니다.

2. **모니터링** 섹션에는 각 Azure 리소스에 대한 가장 중요한 메트릭이 포함됩니다. 예를 들어 웹앱에는 **요청 및 오류**가 있고, 가상 컴퓨터에는 **CPU 비율** 및 **디스크 읽기 및 쓰기**가 있습니다. ![모니터링 렌즈](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. 차트를 클릭하면 **메트릭** 블레이드가 표시됩니다. 블레이드에는 그래프 외에도 선택한 시간 범위의 메트릭 집계(예: 평균, 최소값 및 최대값)를 보여 주는 테이블이 있습니다. 그 아래에는 리소스에 대한 경고 규칙이 있습니다. ![메트릭 블레이드](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. 표시되는 선을 사용자 지정하려면 차트의 **편집** 단추 또는 메트릭 블레이드의 **차트 편집** 명령을 클릭합니다.

5. 쿼리 편집 블레이드에서 다음 세 가지 작업을 수행할 수 있습니다.
    - 시간 범위 변경
    - 막대와 선 간에 모양 전환
    - 다른 메트릭 선택 ![쿼리 편집](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. 시간 범위를 변경하는 것은 다른 범위(예: **Past Hour**)를 선택하고 블레이드 맨 아래에 있는 **저장**을 클릭하는 것만큼 쉽습니다. **사용자 지정**을 통해 지난 2주간에 걸친 기간을 임의로 선택할 수도 있습니다. 예를 들어 2주 전체를 확인하거나 어제부터 1시간만 확인할 수도 있습니다. 다른 시간을 입력하려면 텍스트 상자에 입력합니다. ![사용자 지정 시간 범위](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. 시간 범위 아래에서 차트에 표시할 메트릭 수를 제한 없이 선택할 수 있습니다.

8. 저장을 클릭하면 해당 리소스에 대해 변경 내용이 저장됩니다. 예를 들어 두 개의 가상 컴퓨터가 있고 한 가상 컴퓨터에서 차트를 변경하는 경우 다른 가상 컴퓨터에는 영향을 주지 않습니다.

## 병렬 차트 만들기

포털의 강력한 사용자 지정 기능을 통해 차트를 원하는 개수만큼 추가할 수 있습니다.

1. 블레이드 맨 위에 있는 **...** 메뉴에서 **타일 추가**를 클릭합니다. ![메뉴 추가](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 그런 다음 화면 오른쪽에 있는 **갤러리**에서 차트를 선택할 수 있습니다. ![갤러리](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 원하는 메트릭이 표시되지 않는 경우 언제든지 미리 설정된 메트릭 중 하나를 추가하고 차트를 **편집**하여 필요한 메트릭을 표시할 수 있습니다.

## 사용 할당량 모니터링

대부분의 메트릭은 시간에 따른 추세를 보여 주지만 사용 할당량와 같은 특정 데이터는 임계값을 포함하는 지정 시간 정보입니다.

할당량이 있는 리소스에 대한 블레이드에서 사용 할당량을 확인할 수도 있습니다.

![사용](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

메트릭과 마찬가지로, [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 사용 할당량의 전체 집합에 액세스할 수 있습니다.

## 다음 단계

* 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [모니터링 및 진단을 사용](insights-how-to-use-diagnostics.md)하여 서비스의 자세한 고빈도 메트릭을 수집합니다.
* [인스턴스 개수를 자동으로 조정](insights-how-to-scale.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 클라우드에서 코드의 성능을 정확히 파악하려는 경우[응용 프로그램 성능을 모니터링](../application-insights/app-insights-azure-web-apps.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](../application-insights/app-insights-web-track-usage.md)를 사용하여 웹 페이지를 방문하는 브라우저에 대한 클라이언트 분석을 가져옵니다.
* 페이지가 다운된 경우 이를 찾을 수 있도록 Application Insights를 사용하여 [웹 페이지의 가용성 및 응답성을 모니터링](../application-insights/app-insights-monitor-web-app-availability.md)합니다.

<!---HONumber=AcomDC_0914_2016-->