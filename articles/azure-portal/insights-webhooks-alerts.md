<properties
	pageTitle="Azure 메트릭 경고에 대한 웹후크 구성 | Microsoft Azure"
	description="Azure 경고를 다른 비Azure 시스템으로 경로를 전환합니다."
	authors="kamathashwin"
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
	ms.date="09/15/2016"
	ms.author="ashwink"/>

# Azure 활동 메트릭 경고에 대한 웹후크 구성

웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. SMS 보내기, 버그 기록, 채팅/메시징 서비스를 통한 팀 알림 또는 원하는 수의 다른 작업 수행 등을 처리하는 서비스에 라우팅하도록 웹후크를 경고에 사용할 수 있습니다. 이 문서에서는 Azure 메트릭 경고에 웹후크를 설정하는 방법과 웹후크에 나타나는 HTTP POST의 페이로드에 대해 설명합니다. 한편 Azure 활동 로그 경고(이벤트에 대한 경고)에 대한 설정과 스키마에 대해서는 [이 페이지를 대신 참조하세요](./insights-auditlog-to-webhook-email.md).

Azure에서는 앞으로 정의될 JSON 형식과 스키마에 포함되는 경고인 HTTP POST를 이 경고를 만들 때 제공되는 웹후크 URI로 알립니다. 이 URI의 HTTP 또는 HTTPS 끝점은 유효해야 합니다. 경고가 활성화되면 Azure에서 요청당 항목 하나만 게시합니다.

## 포털을 통해 Webhooks 구성

[포털](https://portal.azure.com/)의 경고 만들기/업데이트 화면에서 웹후크 URI를 추가하거나 업데이트할 수 있습니다.

![경고 규칙 추가](./media/insights-webhooks-alerts/Alertwebhook.png)

[Azure PowerShell Cmdlet](./insights-powershell-samples.md#create-alert-rules), [플랫폼 간 CLI](./insights-cli-samples.md#work-with-alerts) 또는 [Insights REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 웹후크 URI에 게시하도록 경고를 구성할 수 있습니다.

## 웹후크 인증

웹후크는 다음 방법 중 하나를 사용하여 인증할 수 있습니다.

1. **토큰 기반 인증** - 토큰 ID를 사용하여 웹후크 URI를 저장합니다. 예를 들면 다음과 같습니다. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.	**기본 인증** - 사용자 이름과 암호를 사용하여 웹후크 URI를 저장합니다. 예를 들면 다음과 같습니다. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## 페이로드 스키마

POST 작업에는 모든 메트릭 기반 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| 필드 | 필수 | 고정된 값 집합 | 참고 사항 |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|“Activated”, “Resolved”|설정한 조건을 기반으로 하는 경고에 대한 상태입니다.|
|context| Y | | 경고 컨텍스트입니다.|
|timestamp| Y | | 경고가 트리거된 시점의 시간입니다.|
|id | Y | | 모든 경고 규칙에는 고유한 ID가 있습니다.|
|name |Y | | 경고 이름입니다.|
|description |Y | |경고에 대한 설명입니다.|
|conditionType |Y |“Metric”, “Event” |두 형식의 경고가 지원됩니다. 하나는 메트릭 조건에, 다른 하나는 활동 로그의 이벤트에 기반합니다. 이 값을 사용하여 메트릭 또는 이벤트에 기반하는 경고를 확인합니다.|
|condition |Y | | conditionType에 기반하여 확인하기 위한 특정 필드입니다.|
|metricName |메트릭 경고의 경우 | |규칙은 모니터링을 정의하는 메트릭의 이름입니다.|
|metricUnit |메트릭 경고의 경우 |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds"|	 메트릭에 사용되는 단위입니다. [허용되는 값은 여기에 나열되어 있습니다](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue |메트릭 경고의 경우 | |경고를 발생시킨 메트릭의 실제 값입니다.|
|threshold |메트릭 경고의 경우 | |경고가 활성화되는 임계값입니다.|
|windowSize |메트릭 경고의 경우 | |임계값에 기반하여 경보 활동을 모니터링하는 데 사용되는 기간입니다. 5분에서 하루 사이여야 합니다. ISO 8601 기간 형식입니다.|
|timeAggregation |메트릭 경고의 경우 |"Average", "Last", "Maximum", "Minimum", "None", "Total" |	데이터가 수집되는 방법은 시간이 지남에 따라 결합되어야 합니다. 기본값은 평균입니다. [허용되는 값은 여기에 나열되어 있습니다](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operator |메트릭 경고의 경우 | |현재 메트릭 데이터를 설정한 임계값과 비교 하는 데 사용되는 연산자입니다.|
|subscriptionId |Y | |Azure 구독 ID입니다.|
|resourceGroupName |Y | |영향을 받는 리소스의 리소스 그룹 이름입니다.|
|resourceName |Y | |영향을 받는 리소스의 리소스 이름입니다.|
|resourceType |Y | |영향을 받는 리소스의 리소스 형식입니다.|
|resourceId |Y | |영향을 받는 리소스의 리소스 ID입니다.|
|resourceRegion |Y | |영향을 받는 리소스의 지역 또는 위치입니다.|
|portalLink |Y | |포털 리소스 요약 페이지에 대한 직접 링크입니다.|
|properties |N |옵션 |이벤트에 대한 세부 정보를 포함하는 `<Key, Value>` 쌍의 집합(예: `Dictionary<String, String>`)입니다. 속성 필드는 선택 사항입니다. 사용자 지정 UI 또는 논리 앱 기반 워크플로에서 페이로드를 통해 전달될 수 있는 키/값을 입력할 수 있습니다. 사용자 지정 속성을 Webhook에 다시 전달할 대체 방법은 Webhook URI 자체를 통하는 것입니다.(쿼리 매개 변수로)|


>[AZURE.NOTE] properties 필드만 [Insights REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 설정할 수 있습니다.

## 다음 단계

- [Azure 경고와 PagerDuty의 통합](http://go.microsoft.com/fwlink/?LinkId=627080) 비디오에서의 Azure 경고 및 웹후크에 대한 자세한 내용
- [Azure 경고에 대한 Azure Automation 스크립트 실행 (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)
- [논리 앱을 사용하여 Azure 경고에서 Twilio 통해 SMS 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [논리 앱을 사용하여 Azure 경고에서 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [논리 앱을 사용하여 Azure 경고에서 Azure Queue에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0921_2016-->