<properties
	pageTitle="Azure 활동 로그 경고에 대한 웹후크 구성 | Microsoft Azure"
	description="활동 로그 경고를 통한 웹후크 호출 방법을 참조하세요. "
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

# Azure 활동 로그 경고에 대한 웹후크 구성

웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. SMS 보내기, 버그 기록, 채팅/메시징 서비스를 통한 팀 알림 또는 원하는 수의 다른 작업 수행 등을 처리하는 서비스에 라우팅하도록 웹후크를 경고에 사용할 수 있습니다. 이 문서에서는 Azure 활동 로그 경고에 웹후크를 설정하는 방법과 웹후크에 나타나는 HTTP POST의 페이로드에 대해 설명합니다. 한편 Azure 메트릭 경고에 대한 설정과 스키마에 대해서는 [이 페이지를 대신 참조하세요](./insights-webhooks-alerts.md). 또한 활성화될 때 전자 메일을 보내도록 활동 로그 경고를 설정할 수도 있습니다.

>[AZURE.NOTE] 이 기능은 다양한 품질과 성능을 기대하면서 현재 공개되어 있습니다.

[Azure PowerShell Cmdlet](./insights-powershell-samples.md#create-alert-rules), [플랫폼 간 CLI](./insights-cli-samples.md#work-with-alerts) 또는 [Insights REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 활동 로그 경고를 설정할 수 있습니다.

## 웹후크 인증
웹후크는 다음 방법 중 하나를 사용하여 인증할 수 있습니다.

1. **토큰 기반 인증** - 토큰 ID를 사용하여 웹후크 URI를 저장합니다. 예를 들면 다음과 같습니다. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.	**기본 인증** - 사용자 이름과 암호를 사용하여 웹후크 URI를 저장합니다. 예를 들면 다음과 같습니다. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## 페이로드 스키마
POST 작업에는 모든 활동 로그 기반 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다. 이 스키마는 메트릭 기반 경고에서 사용하는 것과 비슷합니다.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|요소 이름|	설명|
|---|---|
|status |메트릭 경고에 사용됩니다. 활동 로그 경고에 대해서는 항상 "activated"로 설정합니다.|
|context|이벤트에 대한 컨텍스트입니다.|
|resourceProviderName|영향을 받는 리소스의 리소스 공급자입니다.|
|conditionType |항상 "Event"입니다.|
|name |경고 규칙의 이름입니다.|
|id |경고의 리소스 ID입니다.|
|description|	경고를 만드는 동안 설정되는 경고 설명 입니다.|
|subscriptionId |Azure 구독 ID입니다.|
|timestamp|	요청을 처리하는 Azure 서비스에서 이벤트를 생성한 시간입니다.|
|resourceId |영향을 받는 리소스의 리소스 ID입니다.|
|resourceGroupName|영향을 받는 리소스의 리소스 그룹 이름입니다.|
|properties |이벤트에 대한 세부 정보를 포함하는 `<Key, Value>` 쌍의 집합(예: `Dictionary<String, String>`)입니다.|
|event|이벤트에 대한 메타데이터가 포함된 요소입니다.|
|권한 부여|이벤트의 RBAC 속성입니다. 여기에는 일반적으로 "action", "role" 및 "scope"가 포함됩니다.|
|카테고리 | 이벤트의 범주. 지원되는 값으로 Administrative, Alert, Security, ServiceHealth, Recommendation이 있습니다.|
|caller|가용성에 기반한 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. 특정 시스템 호출의 경우 null일 수 있습니다.|
|CorrelationId|	일반적으로 문자열 형식의 GUID. correlationId가 있는 이벤트는 동일한 상위 작업에 속하며 일반적으로 correlationId를 공유합니다.|
|eventDescription |이벤트에 대한 정적 텍스트 설명입니다.|
|eventDataId|이벤트에 대한 고유 식별자입니다.|
|eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다.|
|httpRequest|	일반적으로 “clientRequestId”, “clientIpAddress” 및 “method”(PUT 등의 HTTP 메서드) 포함|
|level|“Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” 값 중 하나입니다.|
|operationId|일반적으로 이벤트 간에 공유되는 GUID로서 단일 작업에 해당합니다.|
|operationName|작업의 이름입니다.|
|properties |이벤트의 속성입니다.|
|status|작업 상태를 나타내는 문자열입니다. 일반적인 값으로 "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved"가 포함됩니다.|
|subStatus|	일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값으로 OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503), Gateway Timeout (HTTP Status Code: 504)이 있습니다.|

## 다음 단계
- [활동 로그에 대한 자세한 내용](./monitoring-overview-activity-logs.md)
- [Azure 경고에 대한 Azure Automation 스크립트 실행 (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)
- [논리 앱을 사용하여 Azure 경고에서 Twilio 통해 SMS 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
- [논리 앱을 사용하여 Azure 경고에서 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
- [논리 앱을 사용하여 Azure 경고에서 Azure Queue에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->