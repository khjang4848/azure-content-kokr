<properties
   pageTitle="URL 기반 콘텐츠 라우팅 개요 | Microsoft Azure"
   description="이 페이지에서는 응용 프로그램 게이트웨이 URL 기반 콘텐츠 라우팅, UrlPathMap 구성 및 PathBasedRouting 규칙에 대한 개요를 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="gwallace"/>

# URL 경로 기반 라우팅 개요

URL 경로 기반 라우팅을 사용하여 요청의 URL 경로에 따라 트래픽을 백 엔드 서버 풀로 라우팅할 수 있습니다. 시나리오 중 하나는 여러 콘텐츠 형식에 대한 요청을 서로 다른 백 엔드 서버 풀로 라우팅하는 것입니다. 다음 예제에서는 응용 프로그램 게이트웨이가 세 개의 백 엔드 서버 풀(예: VideoServerPool, ImageServerPool 및 DefaultServerPool)에서 contoso.com에 대한 트래픽을 제공합니다.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

http://contoso.com/video*에 대한 요청은 VideoServerPool로 라우팅되고, http://contoso.com/images*에 대한 요청은 ImageServerPool로 라우팅됩니다. 경로 패턴과 일치하는 항목이 없는 경우 DefaultServerPool이 선택됩니다.

## UrlPathMap 구성 요소

UrlPathMap 요소는 백 엔드 서버 풀 매핑에 대한 경로 패턴을 지정하는 데 사용됩니다. 다음 코드 예제는 템플릿 파일의 urlPathMap 요소 조각입니다.

	"urlPathMaps": [
	{
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
	}
	

>[AZURE.NOTE] PathPattern: 이 설정은 일치하는 경로 패턴의 목록입니다. 각각은 /로 시작해야 하고 "*"는 / 다음의 끝에 올 수 있습니다. 경로 검사기에 제공하는 문자열은 ? 또는 #으로 시작하는 텍스트는 포함하지 않습니다. 이러한 문자는 허용되지 않습니다.

자세한 내용은 [URL 기반 라우팅을 사용하는 Resource Manager 템플릿](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing)을 참조하세요.

## PathBasedRouting 규칙

PathBasedRouting 형식의 RequestRoutingRule은 수신기를 urlPathMap에 바인딩하는 데 사용됩니다. 이 수신기에 대해 수신되는 모든 요청은 urlPathMap에 지정된 정책에 따라 라우팅됩니다. PathBasedRouting 규칙 조각:

	"requestRoutingRules": [
  	{

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
	
## 다음 단계

URL 기반 콘텐츠 라우팅을 알아본 후에는 [URL 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-url-route-portal.md)로 이동하여 URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이를 만듭니다.

<!---HONumber=AcomDC_0921_2016-->