<properties
   pageTitle="포털을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성 | Microsoft Azure"
   description="이 페이지에서는 포털을 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# 포털을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Azure 응용 프로그램 게이트웨이 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트 엔드 서버 설치 및 웹 응용 프로그램의 관리를 간소화합니다.

## 시나리오

다음 시나리오에서는 기존 응용 프로그램 게이트웨이에서 SSL 오프로드를 구성하는 과정을 살펴봅니다. 이 시나리오에서는 [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md) 단계를 이미 수행한 것으로 가정합니다.

## 시작하기 전에

응용 프로그램 게이트웨이에서 SSL 오프로드를 구성하려면 인증서가 필요합니다. 이 인증서는 응용 프로그램 게이트웨이에 로드되며 SSL을 통해 전송된 트래픽을 암호화 및 해독하는 데 사용됩니다. 인증서는 개인 정보 교환(.pfx) 형식이어야 합니다. 이 파일 형식을 사용하면 응용 프로그램 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 개인 키를 내보낼 수 있습니다.

## HTTPS 수신기 추가

HTTPS 수신기는 구성에 따라 트래픽을 확인하며, 백 엔드 풀로 트래픽을 라우팅하도록 도와줍니다.

### 1단계

Azure 포털로 이동하여 기존 응용 프로그램 게이트웨이를 선택합니다.

![응용 프로그램 게이트웨이 개요 블레이드][1]

### 2단계

수신기를 클릭한 다음 추가 단추를 클릭하여 수신기를 추가합니다.

### 3단계

수신기에 대한 필수 정보를 입력하고 .pfx 인증서를 업로드합니다. 완료되면 확인을 클릭합니다.

**이름** - 이 값은 수신기의 이름입니다.

**프런트 엔드 IP 구성** - 이 값은 수신기에 사용되는 프런트 엔드 IP 구성입니다.

**프런트 엔드 포트(이름/포트)** - 응용 프로그램 게이트웨이의 프런트 엔드에서 사용되는 포트 및 실제로 사용되는 포트의 이름입니다.

**프로토콜** - 프런트 엔드에 https가 사용되는지 또는 http가 사용되는지를 결정하는 스위치입니다.

**인증서(이름/암호)** - SSL 오프로드를 사용하는 경우 이 설정에는 .pfx 인증서가 필요하며, 이름 및 암호가 필요합니다.

![수신기 추가 블레이드][2]

## 규칙을 만들고 수신기에 연결

이제 수신기가 만들어졌습니다. 수신기에서 트래픽을 처리할 규칙을 만들 차례입니다.

### 1단계

응용 프로그램 게이트웨이의 **규칙**을 클릭한 다음 추가를 클릭합니다.

![앱 게이트웨이 규칙 블레이드][3]

### 2단계

**기본 규칙 추가** 블레이드에서 규칙의 이름을 입력하고 이전 단계에서 만든 수신기를 선택합니다. 적절한 백 엔드 풀 및 http 설정을 선택하고 **확인**을 클릭합니다.

![https 설정 창][4]

이제 설정이 응용 프로그램 게이트웨이에 저장됩니다. 이러한 설정의 저장 프로세스에는 약간의 시간이 걸릴 수 있으며, 그런 다음 포털 또는 PowerShell을 통해 볼 수 있습니다. 저장이 완료되면 응용 프로그램 게이트웨이에서 트래픽의 암호화 및 암호 해독을 처리합니다. 응용 프로그램 게이트웨이와 백 엔드 웹 서버 간의 모든 트래픽은 http를 통해 처리됩니다. Https를 통해 시작된 경우에는 클라이언트와의 모든 통신이 암호화된 상태로 클라이언트에 반환됩니다.

## 다음 단계

Azure 응용 프로그램 게이트웨이를 사용하여 사용자 지정 상태 프로브를 구성하는 방법을 알아보려면 [사용자 지정 상태 프로브 만들기](application-gateway-create-gateway-portal.md)를 참조하세요.

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

<!---HONumber=AcomDC_0921_2016-->