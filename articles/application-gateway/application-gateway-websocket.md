<properties
   pageTitle="Application Gateway WebSocket 지원 | Microsoft Azure"
   description="이 페이지에서는 Application Gateway WebSocket 지원에 대한 개요를 제공합니다."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# Application Gateway WebSocket 지원

[RFC6455](https://tools.ietf.org/html/rfc6455)에서 표준화된 WebSocket 프로토콜을 사용하면 장기 실행 TCP 연결에 걸쳐 서버와 클라이언트 간의 완전한 이중 통신이 가능합니다. 이 기능을 통해 웹 서버 및 클라이언트 간에 대화형 통신이 가능해지며 HTTP 기반 구현에 필수인 폴링이 없이도 양방향이 가능해집니다. WebSocket은 HTTP와 달리 오버헤드가 낮고 여러 요청/응답에 동일한 TCP 연결을 다시 사용하므로 리소스를 보다 효율적으로 사용할 수 있습니다. WebSocket 프로토콜은 기존의 HTTP 포트 80 및 443을 통해 작동하도록 디자인되었습니다.

Application Gateway는 모든 게이트웨이 크기에 WebSocket에 대한 네이티브 지원을 제공합니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. 포트 80/443에 대해 표준 HTTPListener를 계속 사용하여 WebSocket 트래픽을 받을 수 있습니다. WebSocket 트래픽은 Application Gateway 규칙에 지정된 대로 적절한 백 엔드 풀을 사용하여 WebSocket 활성화된 백 엔드 서버로 지정됩니다.

백 엔드 서버는 Application Gateway 프로브에 응답해야 하며 이는 [상태 프로브 개요](application-gateway-probe-overview.md) 섹션에서 설명합니다. Application Gateway 상태 프로브는 HTTP/HTTPS입니다. 즉, 모든 백 엔드 서버는 Application Gateway에 대한 HTTP 프로브에 응답하여 서버에 WebSocket 트래픽을 라우팅해야 합니다.


## 수신기 구성 요소

기존 HTTPListener는 WebSocket을 지원하기 위해 사용될 수 있습니다. 다음은 샘플 템플릿 파일에서 HttpListeners 요소의 조각입니다. WebSocket을 지원하고 WebSocket 트래픽을 보호하기 위해 HTTP 및 HTTPS 수신기가 모두 필요합니다. 마찬가지로 [포털](application-gateway-create-gateway-portal.md) 또는 [PowerShell](application-gateway-create-gateway-arm.md)을 사용하여 WebSocket 트래픽을 지원하기 위해 포트 80/443에 대한 수신기를 포함한 Application Gateway를 만들 수 있습니다.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## BackendAddressPool, BackendHttpSetting 및 라우팅 규칙 구성

BackendAddressPool은 WebSocket 활성화 서버를 사용하여 백 엔드 풀을 정의하는 데 사용되어야 합니다. BackendHttpSetting은 백 엔드 포트 80/443와 함께 정의되어야 합니다. 쿠키 기반 선호도 및 requestTimeouts의 속성은 WebSocket 트래픽과 관련되지 않습니다. 라우팅 규칙에 필요한 변경 사항은 없습니다. 적절한 수신기를 해당 백 엔드 주소 풀에 연결하기 위해 계속 '기본' 라우팅 규칙을 사용해야 합니다.

	"requestRoutingRules": [{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}, {
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}

		}
	}]

## WebSocket 활성화 백 엔드

WebSocket이 작동되려면 사용자의 백 엔드가 구성된 포트(대개 80/443)에서 HTTP/HTTPS 웹 서버를 실행해야 합니다. 이 요구 사항은 WebSocket 프로토콜이 헤더 필드인 WebSocket 프로토콜에 업그레이드를 사용하여 초기 핸드셰이크를 HTTP가 되도록 해야 하기 때문입니다.

	GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

또 다른 이유는 해당 Application Gateway 백 엔드 상태 프로브가 HTTP/HTTPS 프로토콜만 지원하기 때문입니다. 백 엔드 서버가 HTTP/HTTPS 프로브에 응답하지 않으면 백 엔드 풀에서 떨어졌으므로 WebSocket 요청을 포함한 요청이 이 백 엔드에 연결되지 않습니다.

## 다음 단계

WebSocket 지원에 대해 알아본 후에 [Application Gateway 만들기](application-gateway-create-gateway.md)로 이동하여 WebSocket 활성화 웹 응용 프로그램을 시작합니다.

<!---HONumber=AcomDC_0921_2016-->