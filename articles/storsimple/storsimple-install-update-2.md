<properties
   pageTitle="StorSimple 장치에 업데이트 2 설치 | Microsoft Azure"
   description="StorSimple 8000 시리즈 장치에서 StorSimple 8000 시리즈 업데이트 2를 설치하는 방법에 대해 설명합니다."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# StorSimple 장치에 업데이트 2 설치

## 개요

이 자습서에서는 Azure 클래식 포털을 통해 이전 소프트웨어 버전을 실행하는 StorSimple 장치에 업데이트 2를 설치하는 방법에 대해 설명합니다. 또한 이 자습서는 StorSimple 장치의 DATA 0 이외의 다른 네트워크 인터페이스에서 게이트웨이를 구성하고 업데이트 1 이전 소프트웨어 버전에서 업데이트를 시도하는 경우 업데이트에 필요한 단계를 다룹니다.

업데이트 2는 장치 소프트웨어 업데이트, LSI 드라이버 업데이트 및 디스크 펌웨어 업데이트를 포함합니다. 장치 소프트웨어 및 LSI 업데이트는 무중단 업데이트이며 Azure 클래식 포털을 통해 적용될 수 있습니다. 디스크 펌웨어 업데이트는 작업 중단 업데이트이며 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다.

> [AZURE.IMPORTANT]

> -  업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트 2를 볼 수는 없습니다. 해당 업데이트를 곧 사용할 수 있게 되므로 몇 일 후에 업데이트를 스캔합니다.
> - 설치하기 전에 일련의 수동 및 자동 전 검사를 수행하며 하드웨어 상태 및 네트워크 연결 측면에서 장치 상태를 확인합니다. Azure 클래식 포털에서 업데이트를 적용하는 경우 이러한 사전 검사를 수행합니다.
> - Azure 클래식 포털을 통해 소프트웨어 및 드라이버 업데이트를 설치하는 것이 좋습니다. 포털에서 사전 업데이트 게이트웨이 검사가 실패한 경우 (업데이트를 설치하려면) 장치의 Windows PowerShell 인터페이스로 이동해야 합니다. 업데이트를 설치하는 데 4-7시간이 걸릴 수 있습니다(Windows 업데이트 포함). 장치의 Windows PowerShell 인터페이스를 통해 유지 관리 모드 업데이트를 설치해야 합니다. 유지 관리 모드 업데이트는 중단 업데이트입니다. 이는 장치에 중단 시간을 발생시킵니다.
> - 선택적 StorSimple Snapshot Manager를 실행하는 경우 장치를 업데이트하기 전에 Snapshot Manager 버전을 업데이트 2로 업그레이드했는지 확인합니다.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Azure 클래식 포털을 통해 업데이트 2 설치

다음 단계를 수행하여 장치를 [업데이트 2](storsimple-update2-release-notes.md)로 업데이트합니다.


> [AZURE.NOTE]
업데이트 2를 통해 Microsoft는 장치로부터 추가적인 진단 정보를 확보할 수 있습니다. 따라서 운영 팀에서 문제가 있는 장치를 확인하는 경우, 장치로부터 정보를 수집하고 문제를 진단할 준비가 더욱 잘 갖추어져 있습니다. 업데이트 2를 수락하면, 이러한 주도적 지원을 제공해도 된다고 허락하는 것입니다.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. 장치가 **StorSimple 8000 시리즈 업데이트 2(6.3.9600.17673)**를 실행하고 있는지 확인합니다. **마지막 업데이트 날짜**도 수정해야 합니다. 또한 유지 관리 모드 업데이트가 사용 가능하다고 표시됩니다(이 메시지는 업데이트를 설치한 후 최대 24시간 동안 계속 표시될 수 있음).

    유지 관리 모드 업데이트는 작업 중단 업데이트이므로 장치 가동 중지 시간이 발생할 수 있으며, 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다. 일부 경우에 업데이트 1.2를 실행할 때 디스크 펌웨어가 이미 있을 최신 버전일 수 있습니다. 이 경우 모든 유지 관리 모드 업데이트를 설치할 필요는 없습니다.

13. [핫픽스를 다운로드하려면](#to-download-hotfixes)에 나열된 단계를 사용하여 유지 관리 모드 업데이트를 다운로드한 후 KB3121899를 검색한 후 다운로드합니다. 이 KB는 디스크 펌웨어 업데이트를 설치합니다(다른 업데이트가 이미 설치되어 있어야 함).

13. [유지 관리 모드 핫픽스 설치 및 확인](#to-install-and-verify-maintenance-mode-hotfixes)에 나열된 단계를 따라 유지 관리 모드 업데이트를 설치합니다.


## 핫픽스로 업데이트 2 설치

Azure 클래식 포털을 통해 업데이트를 설치하려고 할 때 게이트웨이 검사에 실패하는 경우 이 절차를 따릅니다. 비데이터 0 네트워크 인터페이스에 할당된 게이트웨이가 있고 장치가 업데이트 1 이전의 소프트웨어 버전을 실행하는 경우 확인이 실패합니다.

핫픽스 메서드를 사용하여 업그레이드할 수 있는 소프트웨어 버전은 업데이트 0.1, 업데이트 0.2, 업데이트 0.3, 업데이트 1, 업데이트 1.1, 업데이트 1.2입니다. 핫픽스 메서드에는 다음 세 단계가 포함됩니다.

- Microsoft Update 카탈로그에서 핫픽스를 다운로드합니다.
- 일반 모드 핫픽스를 설치 및 확인합니다.
- 유지 관리 모드 핫픽스를 설치 및 확인합니다.

업데이트 2를 핫픽스로 설치하려면 다음 핫픽스를 다운로드한 후 설치해야 합니다.

| 순서 | KB | 설명 | 업데이트 유형 |
|--------|-----------|-------------------------|------------- |
| 1 | KB3121901 | 소프트웨어 업데이트 | 일반 |
| 2 | KB3121900 | LSI 드라이버 | 일반 |
| 3 | KB3080728 | Storport로 </br> Windows Server 2012 R2 수정 | 일반 |
| 4 | KB3090322 | Spaceport로 </br> Windows Server 2012 R2 수정 | 일반 |
| 5 | KB3121899 | 디스크 펌웨어 | 유지 관리 |


> [AZURE.IMPORTANT]
>
> - 장치가 릴리스(GA) 버전을 실행하는 경우 이 업데이트 설치를 위한 지원을 받으려면 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)하세요.
> - 이 절차는 업데이트 2에만 적용하도록 한 번만 수행해야 됩니다. Azure 클래식 포털을 사용하여 후속 업데이트를 적용할 수 있습니다.
> - 각 핫픽스 설치를 완료하려면 약 20분 정도 걸릴 수 있습니다. 총 설치 시간은 2시간에 가깝습니다.
> - 이 절차에 따라 업데이트를 적용하기 전에 두 장치 컨트롤러가 온라인 상태이고 모든 하드웨어 구성 요소가 정상 상태에 있는지를 확인합니다.

다음 단계를 수행하여 이 업데이트를 핫픽스로 적용합니다.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## 다음 단계

[업데이트 2 릴리스](storsimple-update2-release-notes.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->