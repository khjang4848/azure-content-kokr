<properties
	pageTitle="Azure 서비스에 대한 경고 알림 받기 | Microsoft Azure"
	description="경고 규칙 조건이 충족되면 알려줍니다."
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

# 경고 알림 받기

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

메트릭 값에 대한 경고 규칙은 지정된 메트릭 값이 할당된 임계값을 벗어날 때 해당 경고 규칙이 활성화되고 알림을 보낼 수 있습니다. 이벤트에 대한 경고 규칙은 *모든* 이벤트 또는 특정 개수의 이벤트가 발생했을 때만 알림을 보낼 수 있습니다.

경고 규칙을 만들 때는 서비스 관리자 및 공동 관리자에게 전자 메일 알림을 보내거나 사용자가 지정 가능한 다른 관리자에게 전자 메일 알림을 보내는 옵션을 선택할 수 있습니다. 규칙이 활성화되고 경고 조건이 해결되면 알림 전자 메일이 전송됩니다.

[REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 경고 규칙에 대한 정보를 프로그래밍 방식으로 구성하고 가져올 수 있습니다.

## 경고 규칙 만들기

1. [포털](https://portal.azure.com/)에서 **찾아보기**를 클릭한 후 모니터링할 리소스를 클릭합니다.

2. **작업** 렌즈에서 **경고 규칙** 타일을 클릭합니다.

3. **경고 추가** 명령을 클릭합니다.

	![경고 추가](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. 경고 규칙의 이름을 지정하고 알림 전자 메일에 표시되는 설명을 선택할 수 있습니다.

5. **메트릭**을 선택하면 메트릭에 대한 조건 및 임계값을 선택할 수 있습니다. 이 값은 Azure에서 경고 활동을 모니터링하고 표시하는 데 사용하는 기간입니다.

	![조건 및 임계값](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. 또한 **이벤트**를 선택하고 특정 이벤트가 발생할 때 알림을 받을 수도 있습니다.

	![이벤트](./media/insights-receive-alert-notifications/Insights_Events.png)

7. 마지막으로, 담당 관리자에게 전자 메일 알림을 보낼 수도 있습니다.

**저장**을 클릭하면 몇 분 이내에 선택한 메트릭이 임계값을 초과할 때마다 알림이 표시됩니다.

## 경고 규칙 관리

경고 규칙을 만들고 나면 전날의 메트릭과 비교한 경고 임계값 미리 보기를 볼 수 있습니다.

![이벤트](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


또한 이 경고 규칙을 편집할 수 있으며, 알림 수신을 일시적으로 중지하려는 경우 경고 규칙을 **사용 안 함** 또는 **사용**으로 설정할 수 있습니다.

## 다음 단계

* 다양한 채널로 알림을 전달하도록 [알림에서 Webhook](insights-webhooks-alerts.md) 구성
* [서비스 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* [모니터링 및 진단을 사용](insights-how-to-use-diagnostics.md)하여 서비스의 자세한 고주파 메트릭을 수집합니다.
* Application Insights를 사용하여 [웹 페이지의 가용성 및 응답성을 모니터링](../application-insights/app-insights-monitor-web-app-availability.md)하여 페이지가 다운된 경우를 찾을 수 있습니다.
* 클라우드에서 코드가 어떻게 수행되고 있는지 정확하게 알고 싶은 경우[응용 프로그램 성능을 모니터링](../application-insights/app-insights-azure-web-apps.md)합니다.
* [이벤트 및 감사 로그를 보고](insights-debugging-with-events.md) 서비스에서 발생한 모든 사항을 알아봅니다.
* [서비스 상태를 추적](insights-service-health.md)하여 Azure에서 성능 저하 또는 서비스 중단이 발생한 경우를 알아봅니다.

<!---HONumber=AcomDC_0914_2016-->