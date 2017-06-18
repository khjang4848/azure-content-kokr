<properties
   pageTitle="Resource Manager에서 Azure 포털을 사용하여 내부 부하 분산 장치 만들기 시작 | Microsoft Azure"
   description="Azure 포털을 사용하여 Resource Manager에서 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Azure 포털에서 내부 부하 분산 장치 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Azure 포털을 사용하여 내부 부하 분산 장치 만들기 시작

Azure 포털에서 내부 부하 분산 장치를 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure 포털](http://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 화면 왼쪽 상단에서 **새로 만들기** > **네트워킹** > **부하 분산 장치**를 클릭합니다.
3. **부하 분산 장치 만들기** 블레이드에서 부하 분산 장치의 **이름**을 입력합니다.
4. **구성표** 아래에서 **내부**를 클릭합니다.
5. **가상 네트워크**를 클릭한 다음 부하 분산 장치를 만들 가상 네트워크를 선택합니다.

    >[AZURE.NOTE] 사용하려는 가상 네트워크가 보이지 않으면 부하 분산 장치에 대해 사용 중인 **위치**를 확인하고 적절하게 변경합니다.

6. **서브넷**을 클릭한 다음 부하 분산 장치를 만들 서브넷을 선택합니다.
7. **IP 주소 할당** 아래에서 부하 분산 장치의 IP 주소를 고정으로 할 것인지 유동으로 할 것인지에 따라 **동적** 또는 **정적**을 클릭합니다.

    >[AZURE.NOTE] 고정 IP 주소를 사용하도록 선택하는 경우 부하 분산 장치에 대한 주소를 제공해야 합니다.

8. **리소스 그룹** 아래에서 부하 분산 장치에 대한 새 리소스 그룹의 이름을 지정하거나 **기존 선택**을 클릭하여 기존 리소스 그룹을 선택합니다.
9. **만들기**를 클릭합니다.

## 부하 분산 규칙 구성

부하 분산 장치를 만든 후 구성을 위해 부하 분산 장치 리소스로 이동합니다. 백 엔드 주소 풀 및 프로브를 먼저 구성한 후 부하 분산 규칙을 구성합니다.

### 1단계

백 엔드 풀 구성:

1. Azure 포털에서 **찾아보기** > **부하 분산 장치**를 클릭한 다음 위에서 만든 부하 분산 장치를 클릭합니다.
2. **설정** 블레이드에서 **백 엔드 풀**을 클릭합니다.
3. **백 엔드 주소 풀** 블레이드에서 **추가**를 클릭합니다.
4. **백 엔드 풀 추가** 블레이드에서 백 엔드 풀에 대한 **이름**을 입력한 후 **확인**을 클릭합니다.

### 2단계

프로브 구성:

1. Azure 포털에서 **찾아보기** > **부하 분산 장치**를 클릭한 다음 위에서 만든 부하 분산 장치를 클릭합니다.
2. **설정** 블레이드에서 **프로브**를 클릭합니다.
3. **프로브** 블레이드에서 **추가**를 클릭합니다.
4. **프로브 추가** 블레이드에서 프로브에 대한 **이름**을 입력합니다.
5. **프로토콜** 아래에서 **HTTP**(웹 사이트용) 또는 **TCP**(기타 TCP 기반 응용 프로그램용)를 선택합니다.
6. **포트** 아래에서 프로브에 액세스할 때 사용할 포트를 지정합니다.
7. **경로**(HTTP 프로브 전용) 아래에서 프로브로 사용할 경로를 지정합니다.
8. **간격** 아래에서 응용 프로그램 검색 빈도를 지정합니다.
9. **비정상 임계값** 아래에서 백 엔드 VM을 비정상으로 표시하기 전에 실패하는 시도 횟수를 지정합니다.
10. **확인**을 클릭하여 프로브를 만듭니다.

### 3단계

부하 분산 규칙 구성:

1. Azure 포털에서 **찾아보기** > **부하 분산 장치**를 클릭한 다음 위에서 만든 부하 분산 장치를 클릭합니다.
2. **설정** 블레이드에서 **부하 분산 규칙**을 클릭합니다.
3. **부하 분산 규칙** 블레이드에서 **추가**를 클릭합니다.
4. **부하 분산 규칙 추가** 블레이드에서 규칙에 대한 **이름**을 입력합니다.
5. **프로토콜** 아래에서 **HTTP**(웹 사이트용) 또는 **TCP**(기타 TCP 기반 응용 프로그램용)를 선택합니다.
6. **포트** 아래에서 클라이언트가 부하 분산 장치에 연결할 때 사용할 포트를 지정합니다.
7. **백 엔드 포트** 아래에서 백 엔드 풀에 사용할 포트를 지정합니다. 일반적으로 부하 분산 장치 포트 및 백 엔드 포트는 동일합니다.
8. **백 엔드 풀** 아래에서 위에서 만든 백 엔드 풀을 선택합니다.
9. **세션 지속성** 아래에서 세션을 얼마나 지속할 것인지 선택합니다.
10. **유휴 시간 제한(분)** 아래에서 유휴 시간 제한을 지정합니다.
11. **부동 IP(Direct Server Return(DSR))** 아래에서 **사용 안 함** 또는 **사용**을 클릭합니다.
12. **확인**을 클릭합니다.

## 다음 단계

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->