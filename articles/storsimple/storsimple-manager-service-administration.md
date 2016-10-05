<properties 
   pageTitle="StorSimple 관리자 서비스 관리 | Microsoft Azure"
   description="Azure 클래식 포털에서 StorSimple 관리자 서비스를 사용하여 StorSimple 장치를 관리하는 방법을 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# StorSimple Manager 서비스를 사용하여 StorSimple 장치 관리

## 개요

이 문서에서는 연결 방법, 사용 가능한 여러 옵션 및 이 UI를 통해 수행될 수 있는 특정 워크로드에 대한 링크를 포함한 StorSimple Manager 서비스 인터페이스를 설명합니다. 이 지침은 StorSimple 실제 및 가상 장치, 두 작업 모두에 적용됩니다.

이 문서를 읽은 후 다음에 대해 알 수 있습니다.

- StorSimple Manager 서비스에 연결
- StorSimple Manager UI로 이동
- StorSimple Manager 서비스를 통한 StorSimple 장치 관리


## StorSimple Manager 서비스에 연결

StorSimple 관리자 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 장치에 연결됩니다. 이러한 장치를 관리하는 브라우저에서 실행되는 중앙 Microsoft Azure 클래식 포털을 사용합니다. StorSimple Manager 서비스에 연결하려면 다음을 수행합니다.

#### 해당 서비스에 연결하려면

1. [https://manage.windowsazure.com/](https://manage.windowsazure.com/)으로 이동합니다.

1. Microsoft 계정 자격 증명을 사용하여 Microsoft Azure 클래식 포털(해당 창의 상단 오른쪽에 있는)로 로그온합니다.

1. StorSimple Manager 서비스에 액세스하려면 왼쪽 탐색 창으로 아래로 스크롤합니다.


## StorSimple Manager 서비스 UI로 이동

StorSimple Manager 서비스 UI에 대한 탐색 기록이 다음 테이블에 표시됩니다.

- **StorSimple Manager** 방문 페이지에서 서비스 내 모든 장치에 적용 가능한 UI 서비스 수준 페이지로 이동합니다.

- **장치** 페이지에서 특정 장치에 적용 가능한 장치 수준 UI 페이지로 이동합니다.

- **볼륨 컨테이너** 페이지는 장치와 연결 된 모든 볼륨을 보여주는 볼륨 페이지로 이동합니다.


#### StorSimple Manager 서비스 탐색 계층

|방문 페이지|서비스 수준 페이지|장치 수준 페이지|장치 수준 페이지|
|---|---|---|---|
|StorSimple 관리자 서비스|서비스 대시보드|장치 대시보드||
||장치 →|모니터|
||백업 카탈로그|볼륨 컨테이너→|볼륨|
||(서비스) 구성|백업 정책||
||작업|(장치) 구성|
||경고|유지 관리|

![동영상 사용 가능](./media/storsimple-manager-service-administration/Video_icon.png) **동영상 사용 가능**

StorSimple Manager 서비스 사용자 인터페이스를 안내하는 동영상을 시청하려면 [여기](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/)를 클릭하세요.

## StorSimple Manager 서비스를 사용한 StorSimple 장치 관리

다음 표에서 모든 일반 관리 작업 및 StorSimple Manager 서비스 UI 내에서 수행할 수 있는 복잡한 워크플로의 요약을 보여줍니다. 이러한 작업은 시작되는 UI 페이지에 따라 구성됩니다.

각 워크플로에 대한 자세한 내용은 표에서 적절한 절차를 클릭합니다.

#### StorSimple Manager 워크플로

|수행하려는 작업 ...|이 UI 페이지로 이동 ...|이 절차를 사용합니다.|
|---|---|---|
|서비스 만들기</br>서비스 삭제</br>서비스 등록 키 얻기</br>서비스 등록 키 다시 생성|StorSimple 관리자 서비스|[StorSimple Manager 서비스 배포](storsimple-manage-service.md)
|서비스 데이터 암호화 키 변경</br>작업 로그 보기|StorSimple Manager 서비스 → 대시보드|[StorSimple 관리자 서비스 대시보드 사용](storsimple-service-dashboard.md)|
|장치 비활성화</br>장치 삭제|StorSimple Manager 서비스 → 장치|[장치 비활성화 또는 장치 삭제](storsimple-deactivate-and-delete-device.md)|
|재해 복구 및 장치 장애 조치(Failover)</br>물리적 장치에 장애 조치(Failover)</br>가상 장치에 장애 조치(Failover)</br>비즈니스 연속성 재해 복구(BCDR)에 대해 알아봅니다.|StorSimple Manager 서비스 → 장치|[StorSimple 장치에 대한 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)|
|볼륨에 대한 백업 목록</br>백업 세트 선택</br>백업 세트 삭제|StorSimple Manager 서비스 → 백업 카탈로그|[백업 관리](storsimple-manage-backup-catalog.md)|
|볼륨 복제|StorSimple Manager 서비스 → 백업 카탈로그|[볼륨 복제](storsimple-clone-volume.md)|
|백업 세트 복원|StorSimple Manager 서비스 → 백업 카탈로그|[백업 세트 복원](storsimple-restore-from-backup-set.md)|
|저장소 계정 정보</br>저장소 계정 추가</br>저장소 계정 편집</br>저장소 계정 삭제</br>저장소 계정의 키 회전|StorSimple Manager 서비스 → 구성|[저장소 계정 관리](storsimple-manage-storage-accounts.md)|
|대역폭 템플릿 정보</br>대역폭 템플릿 추가</br>대역폭 템플릿 편집</br>대역폭 템플릿 삭제</br>기본 대역폭 템플릿 사용</br>지정된 시간에 시작되는 하루 종일 대역폭 템플릿 만들기|StorSimple Manager 서비스 → 구성|[대역폭 템플릿 관리](storsimple-manage-bandwidth-templates.md)|
|액세스 제어 레코드 정보</br>액세스 제어 레코드 만들기</br>액세스 제어 레코드 편집</br>액세스 제어 레코드 삭제|StorSimple Manager 서비스 → 구성|[액세스 제어 레코드 관리](storsimple-manage-acrs.md)|
|작업 세부 정보 보기</br>작업 취소|StorSimple Manager 서비스 → 작업|[작업 관리](storsimple-manage-jobs.md)
|경고 알림 받기</br>경고 관리</br>경고 검토|StorSimple Manager 서비스 → 경고|[StorSimple 경고 보기 및 관리](storsimple-manage-alerts.md)
|연결된 시작자 보기</br>장치 일련번호 찾기</br>대상 IQN 찾기|StorSimple Manager 서비스 → 장치 → 대시보드|[StorSimple 장치 대시보드 사용](storsimple-device-dashboard.md)|
|모니터링 차트 만들기|StorSimple Manager 서비스 → 장치 → 모니터링|[StorSimple 장치 모니터링](storsimple-monitor-device.md)|
|볼륨 컨테이너 추가</br>볼륨 컨테이너 수정</br>볼륨 컨테이너 삭제|StorSimple Manager 서비스 → 장치 → 볼륨 컨테이너|[볼륨 컨테이너 관리](storsimple-manage-volume-containers.md)|
|볼륨 추가</br>볼륨 수정</br>볼륨을 오프라인으로 전환</br>볼륨 삭제</br>볼륨 모니터링|StorSimple Manager 서비스 → 장치 → 볼륨 컨테이너 → 볼륨|[볼륨 관리](storsimple-manage-volumes.md)|
|장치 설정 수정</br>시간 설정 수정</br>DNS.md 설정 수정</br>네트워크 인터페이스 구성|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 장치에 대한 장치 구성 수정](storsimple-modify-device-config.md)|
|웹 프록시 설정 보기|StorSimple Manager 서비스 → 장치 → 구성|[장치에 대한 웹 프록시 구성](storsimple-configure-web-proxy.md)|
|장치 관리자 암호 수정</br>StorSimple 스냅숏 관리자 암호 수정|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 암호 변경](storsimple-change-passwords.md)|
|원격 관리 구성|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 장치에 원격으로 연결](storsimple-remote-connect.md)|
|경고 설정 구성|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 경고 보기 및 관리](storsimple-manage-alerts.md)|
|StorSimple 장치에 대한 CHAP 구성|StorSimple Manager 서비스 → 장치 → 구성|[StorSimple 장치에 대한 CHAP 구성](storsimple-configure-chap.md)|
|백업 정책 추가</br>일정 추가 또는 수정</br>백업 정책 삭제</br>수동 백업 수행</br>여러 볼륨 및 일정으로 사용자 지정 백업 정책 만들기|StorSimple Manager 서비스 → 장치 → 백업 정책|[백업 정책 관리](storsimple-manage-backup-policies.md)|
|장치 컨트롤러 중지</br>장치 컨트롤러 다시 시작</br>장치 컨트롤러 종료</br>장치 출하시 기본값으로 재설정</br>(위는 온-프레미스 장치용)|StorSimple Manager 서비스 → 장치 → 유지 관리|[StorSimple 장치 컨트롤러 관리](storsimple-manage-device-controller.md)|
|StorSimple 하드웨어 구성 요소</br>하드웨어 상태 모니터링</br>(위는 온-프레미스 장치용)에 대해 알아봅니다.|StorSimple Manager 서비스 → 장치 → 유지 관리|[하드웨어 구성 요소 모니터링](storsimple-monitor-hardware-status.md)|
|지원 패키지 만들기|StorSimple Manager 서비스 → 장치 → 유지 관리|[지원 패키지 만들기 및 관리](storsimple-create-manage-support-package.md)|
|소프트웨어 업데이트 설치|StorSimple Manager 서비스 → 장치 → 유지 관리|[장치 업데이트](storsimple-update-device.md)|


##다음 단계
StorSimple 장치의 일상적인 작업 또는 해당 하드웨어 구성 요소 사용 시 발생하는 모든 문제는 다음을 참조하세요.

- [작동 가능 장치 문제 해결](storsimple-troubleshoot-operational-device.md)
- [StorSimple 모니터링 표시기 LED 사용](storsimple-monitoring-indicators.md)

문제를 해결할 수 없고 서비스 요청을 만드는 데 필요한 경우 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)를 참조합니다.

<!---HONumber=AcomDC_0921_2016-->