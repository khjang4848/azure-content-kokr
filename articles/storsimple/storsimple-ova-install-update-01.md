<properties 
   pageTitle="StorSimple 가상 배열에 업데이트 설치 | Microsoft Azure"
   description="StorSimple 가상 배열 웹 UI를 사용하여 포털 및 핫픽스 방법을 사용하는 업데이트를 적용하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/07/2016"
   ms.author="alkohli" />

# StorSimple 가상 배열에 업데이트 설치

## 개요

이 문서에서는 로컬 웹 UI 및 Azure 클래식 포털을 사용하여 StorSimple 가상 배열에 업데이트를 설치하는 데 필요한 단계를 설명합니다. StorSimple 가상 배열을 최신 상태로 유지하려면 소프트웨어 업데이트 또는 핫픽스를 적용해야 합니다.

업데이트 또는 핫픽스를 설치하면 장치가 다시 시작됩니다. StorSimple 가상 배열이 단일 노드 장치인 경우 진행 중인 모든 IO가 중단되고 장치에 가동 중지 시간이 발생합니다.

업데이트를 적용하기 전에 호스트에서 볼륨 또는 공유를 오프라인으로 전환한 후 장치를 오프라인으로 전환하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다.

> [AZURE.IMPORTANT] 업데이트 0.1 또는 GA 소프트웨어 버전을 실행 중인 경우 로컬 웹 UI를 통해 핫픽스 메서드를 사용하여 업데이트 0.3을 설치해야 합니다. 업데이트 0.2를 실행 중인 경우 Azure 클래식 포털을 통해 업데이트를 설치하는 것이 좋습니다.

## 로컬 웹 UI 사용 
 
로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

- 업데이트 또는 핫픽스 다운로드
- 업데이트 또는 핫픽스 설치

### 업데이트 또는 핫픽스 다운로드

Microsoft 업데이트 카탈로그에서 소프트웨어 업데이트를 다운로드하려면 다음 단계를 수행합니다.

#### 업데이트 또는 핫픽스를 다운로드하려면

1. Internet Explorer를 시작하고 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)으로 이동합니다.

2. 이 컴퓨터에서 Microsoft 업데이트 카탈로그를 처음 사용하는 경우 Microsoft 업데이트 카탈로그 추가 기능을 설치하라는 메시지가 나타나면 **설치**를 클릭합니다.
  
3. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드하려는 핫픽스의 KB(기술 자료) 번호를 입력합니다. 업데이트 0.3의 경우 **3182061**을 입력하고 **검색**을 클릭합니다.

    핫픽스 목록(예: **StorSimple 가상 배열 업데이트 0.3**)이 나타납니다.

    ![카탈로그 검색](./media/storsimple-ova-install-update-01/download1.png)

4. **추가**를 클릭합니다. 업데이트는 장바구니에 추가됩니다.

5. **바구니 보기**를 클릭합니다.

6. **다운로드**를 클릭합니다. 다운로드를 표시할 로컬 위치를 지정하거나 **검색**합니다. 업데이트를 지정된 위치에 다운로드하고 업데이트와 같은 이름의 하위 폴더에 배치합니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

7. 복사된 폴더를 열면 Microsoft 업데이트 독립 실행형 패키지 파일 `WindowsTH-KB3011067-x64`가 표시됩니다. 이 파일은 업데이트 또는 핫픽스를 설치하는 데 사용됩니다.


### 업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에, 업데이트 또는 핫픽스를 호스트의 로컬에 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.

GA 또는 업데이트 0.1 소프트웨어 버전을 실행하는 장치에 업데이트를 설치하려면 이 방법을 사용합니다. 이 절차를 완료하는 데 2분 미만이 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.


#### 업데이트 또는 핫픽스를 설치하려면

1. 로컬 웹 UI에서 **유지 관리** > **소프트웨어 업데이트**로 이동합니다.

    ![장치 업데이트](./media/storsimple-ova-install-update-01/update1m.png)

2. **업데이트 파일 경로**에 업데이트 또는 핫픽스의 파일 이름을 입력합니다. 네트워크 공유에 있는 경우 업데이트 또는 핫픽스 설치 파일로 이동할 수 있습니다. **적용**을 클릭합니다.

	![장치 업데이트](./media/storsimple-ova-install-update-01/update2m.png)

3.  경고가 표시됩니다. 단일 노드 장치인 경우 업데이트가 적용된 후 장치를 다시 시작하고 가동 중지 시간이 발생합니다. 확인 아이콘을 클릭합니다.

	![장치 업데이트](./media/storsimple-ova-install-update-01/update3m.png)

4. 업데이트가 시작됩니다. 장치가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.

    ![장치 업데이트](./media/storsimple-ova-install-update-01/update5m.png)

5. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 로컬 웹 UI에서 장치 소프트웨어가 업데이트되었는지 확인하려면 **유지 관리** > **소프트웨어 업데이트**로 이동합니다. 표시된 소프트웨어 버전은 업데이트 0.3의 경우 **10.0.0.0.0.10288.0**입니다.

	> [AZURE.NOTE] 로컬 웹 UI 및 Azure 클래식 포털에서 약간 다른 방법으로 소프트웨어 버전을 보고합니다. 예를 들어 같은 버전에 대해 로컬 웹 UI는 **10.0.0.0.0.10288**, Azure 클래식 포털은 **10.0.10288.0**을 보고합니다.

	![장치 업데이트](./media/storsimple-ova-install-update-01/update6m.png)





## Azure 클래식 포털 사용

업데이트 0.2를 실행하는 경우 Azure 클래식 포털을 통해 업데이트를 설치하는 것이 좋습니다. 포털 절차를 사용하려면 사용자가 업데이트를 스캔, 다운로드한 다음 설치해야 합니다. 이 절차를 완료하는 데 약 7분이 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

설치가 완료된 후(작업 상태 100%로 표시) **장치 > 유지 관리 > 소프트웨어 업데이트**로 이동합니다. 표시된 소프트웨어 버전은 10.0.10288.0이어야 합니다.

![장치 업데이트](./media/storsimple-ova-install-update-01/azupdate12m.png)

## 다음 단계

[StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0914_2016-->