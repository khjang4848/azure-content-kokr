<properties 
   pageTitle="정부 포털에서 StorSimple 장치 배포 | Microsoft Azure"
   description="Azure 정부 포털에서 StorSimple 업데이트 2 장치 및 서비스를 배포하기 위한 단계 및 모범 사례를 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="v-sharos" />

# 정부 포털에서 온-프레미스 StorSimple 장치 배포(업데이트 2)

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## 개요

Microsoft Azure StorSimple 장치 배포를 시작합니다. 이러한 배포 자습서는 Azure 정부 포털에서 업데이트 2 소프트웨어를 실행하는 StorSimple 8000 시리즈에 적용됩니다. 이 자습서 시리즈에서는 StorSimple 장치를 구성하는 방법에 대해 설명하며 구성 검사 목록, 구성 필수 조건 목록 및 자세한 구성 단계를 포함합니다.

이 자습서의 정보는 안전 주의 사항을 검토했으며, StorSimple 장치의 포장을 풀었고, 랙을 탑재했으며, 케이블에 연결되어 있다고 가정합니다. 여전히 이러한 작업을 수행해야 하는 경우 [안전 주의 사항](storsimple-safety.md) 검토로 시작하세요. 장치를 개봉, 랙 탑재 및 케이블로 연결하려면 장치별 지침을 따릅니다.

- [8100 개봉, 랙 탑재, 케이블 연결](storsimple-8100-hardware-installation.md)
- [8600 개봉, 랙 탑재, 케이블 연결](storsimple-8600-hardware-installation.md)

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 시작하기 전에 구성 검사 목록을 검토하는 것이 좋습니다. 배포 및 구성 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [AZURE.NOTE] Microsoft Azure 웹 사이트에 게시된 StorSimple 배포 정보는 StorSimple 8000 시리즈 장치에만 적용됩니다. 7000 시리즈 장치에 대한 자세한 내용은 [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)으로 이동합니다. 7000 시리즈 배포 정보는 [StorSimple 시스템 퀵 스타트 가이드](http://onlinehelp.storsimple.com/111_Appliance/)를 참조하세요.

## 배포 단계

StorSimple 장치를 구성하여 StorSimple 관리자 서비스에 연결하려면 다음과 같은 필수 단계를 수행합니다. 필수 단계 외에 선택적 단계 및 배포하는 동안 완료해야 할 수도 있는 절차가 있습니다. 단계별 배포 지침은 각 선택적 단계를 수행해야 하는 시기를 나타냅니다.


| 단계 | 설명 |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **필수 조건** | 향후 배포 준비 과정에서 완료해야 합니다. |
|[배포 구성 검사 목록](#deployment-configuration-checklist) | 이 검사 목록을 사용하여 배포 이전 및 배포하는 동안 정보를 수집하고 기록합니다. |
| [배포 필수 조건](#deployment-prerequsites) | 배포할 준비가 되어 있는 환경인지 유효성을 검사합니다. |
| | |
| **단계별 배포** | 프로덕션 환경에서 StorSimple 장치를 배포하려면 다음 단계가 필요합니다. |
| [1단계: 새 서비스 만들기](#step-1-create-a-new-service) | StorSimple 장치에 대한 클라우드 관리 및 저장소를 설정합니다. *다른 StorSimple 장치에 대해 기존 서비스가 있는 경우 이 단계를 건너뜁니다*. |
| [2단계: 서비스 등록 키 받기](#step-2-get-the-service-registration-key) | 이 키를 사용하여 StorSimple 장치를 관리 서비스에 등록 및 연결합니다. |
| [3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록](step 3-configure-and-register-the-device-through-windows-powershell-for-storsimple) | 장치를 네트워크에 연결하고 관리 서비스를 사용하여 Azure로 등록하여 설정을 완료합니다. |
| [4단계: 최소 장치 설정 완료](#step-4-complete-the-minimum-device-setup) </br>선택 사항: StorSimple 장치를 업데이트합니다. | 관리 서비스를 사용하여 장치 설정을 완료하고 저장소를 제공할 수 있도록 설정합니다. |
| [5단계: 볼륨 컨테이너 만들기](#step-5-create-a-volume-container) | 볼륨을 프로비전할 컨테이너를 만듭니다. 볼륨 컨테이너에는 저장소 계정, 대역폭 및 그 안에 포함된 모든 볼륨에 대한 암호화 설정이 있습니다. |
| [6단계: 볼륨 만들기](#step-6-create-a-volume) | 서버에 대한 StorSimple 장치의 저장소 볼륨을 프로비전합니다. |
| [7단계: 볼륨 탑재, 초기화 및 포맷](#step-7-mount-initialize-and-format-a-volume) </br>선택 사항: MPIO를 구성합니다. | 서버를 장치에서 제공하는 iSCSI 저장소에 연결합니다. 필요에 따라 MPIO를 구성하여 서버가 링크, 네트워크 및 인터페이스 실패를 허용할 수 있도록 합니다. |
| [8단계: 백업 수행](#step-8-take-a-backup) | 백업 정책을 설정하여 데이터 보호 |
| | |
| **기타 절차** | 솔루션을 배포하는 경우 이러한 절차를 참조해야 합니다. |
| [서비스에 대한 새 저장소 계정 구성](#configure-a-new-storage-account-for-the-service) | |
| [장치 직렬 콘솔 연결에 PuTTY 사용](#use-putty-to-connect-to-the-device-serial-console) | |
| [업데이트 검색 및 적용](#scan-for-and-apply-updates) | |
| [Windows Server 호스트의 IQN 가져오기](#get-the-iqn-of-a-windows-server-host) | |
| [수동 백업 만들기](#create-a-manual-backup) | 
| [MPIO 구성](#configure-mpio) |

## 배포 구성 검사 목록

StorSimple 장치를 배포하기 전에 정보를 수집하여 장치에 소프트웨어를 구성해야 합니다. 이 정보의 일부를 미리 준비하면 사용자 환경에서 StorSimple 장치를 배포하는 과정을 간소화하는데 도움이 됩니다. 또한 이 검사 목록을 다운로드 및 사용하여 장치를 배포하는 동안 구성 세부 정보를 기록합니다.

[StorSimple 배포 구성 검사 목록 다운로드](http://www.microsoft.com/download/details.aspx?id=49159)

## 배포 필수 조건

다음 섹션에서는 StorSimple 관리자 서비스 및 StorSimple 장치에 대한 필수 조건에 대해 설명합니다.

### StorSimple 관리자 서비스의 경우

시작하기 전에 다음 사항을 확인합니다.

- 액세스 자격 증명이 있는 Microsoft 계정이 있습니다.

- 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.

- 사용자의 Microsoft Azure 구독을 StorSimple Manager 서비스에 사용할 수 있습니다. 구독은 [기업 계약](https://azure.microsoft.com/pricing/enterprise-agreement/)을 통해 구매해야 합니다.

- PuTTY와 같은 터미널 에뮬레이션 소프트웨어에 액세스할 수 있습니다.

### 데이터 센터에서 장치의 경우

장치를 구성하기 전에 다음 사항을 확인합니다.

- 다음에서 설명한 대로 장치가 완전히 개봉, 랙에 탑재되고 전원, 네트워크 및 직렬 액세스에 완전히 연결되었습니다.

	-  [8100 장치 개봉, 랙 탑재, 케이블 연결](storsimple-8100-hardware-installation.md)
	-  [8600 장치 개봉, 랙 탑재, 케이블 연결](storsimple-8600-hardware-installation.md)


### 데이터 센터에서 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

- [StorSimple 장치에 대한 네트워킹 요구 사항](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)에서 설명한 대로 데이터 센터 방화벽에서 포트가 열려 있어 iSCSI 및 클라우드 트래픽을 허용합니다.

## 단계별 배포

다음 단계별 지침을 사용하여 데이터 센터에서 StorSimple 장치를 배포합니다.

## 1단계: 새 서비스 만들기

StorSimple 관리자 서비스는 여러 StorSimple 장치를 관리할 수 있습니다. StorSimple 관리자 서비스의 새 인스턴스를 만들려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] 서비스와 함께 저장소 계정을 자동으로 만들도록 설정하지 않은 경우, 서비스를 성공적으로 만든 후 하나 이상의 저장소 계정을 만들어야 합니다. 이 저장소 계정은 볼륨 컨테이너를 만들 때 사용됩니다.
>
> * 저장소 계정을 자동으로 만들지 않은 경우 자세한 지침은 [서비스에 대한 새 저장소 계정 구성](#configure-a-new-storage-account-for-the-service)을 참조하세요.
> * 저장소 계정을 자동으로 생성하도록 설정한 경우, [2단계: 서비스 등록 키 받기](#step-2-get-the-service-registration-key)로 이동합니다.

## 2단계: 서비스 등록 키 받기

StorSimple 관리자 서비스를 실행한 후에는 서비스 등록 키를 받아야 합니다. 이 키는 StorSimple 장치를 서비스에 등록 및 연결하는 데 사용됩니다.

정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## 3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록

다음 절차에서 설명한 대로 StorSimple 장치의 초기 설정을 완료하려면 StorSimple용 Windows PowerShell을 사용합니다. 이 단계를 완료하려면 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 자세한 내용은 [장치 직렬 콘솔 연결에 PuTTY 사용](#use-putty-to-connect-to-the-device-serial-console)을 참조하세요.

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov-u2.md)]

## 4단계: 최소 장치 설정 완료

StorSimple 장치의 최소 장치 구성에는 다음 사항이 필요합니다.

- 보조 DNS 서버를 설정합니다.
- 하나 이상의 네트워크 인터페이스에서 iSCSI를 사용하도록 설정합니다.
- 두 컨트롤러에 고정된 IP 주소를 할당합니다.

최소 장치 설정을 완료하려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 5단계: 볼륨 컨테이너 만들기

볼륨 컨테이너에는 저장소 계정, 대역폭 및 그 안에 포함된 모든 볼륨에 대한 암호화 설정이 있습니다. StorSimple 장치에서 볼륨 프로비저닝을 시작하려면 볼륨 컨테이너를 만들어야 합니다.

볼륨 컨테이너를 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 6단계: 볼륨 만들기

볼륨 컨테이너를 만든 후에 서버에 대한 StorSimple 장치에 저장소 볼륨을 프로비전할 수 있습니다. 볼륨을 만들려면 정부 포털에서 다음 단계를 수행합니다.

> [AZURE.IMPORTANT] Azure StorSimple은 씬 프로비저닝된 볼륨만 만들 수 있습니다. Azure StorSimple 시스템에서 완전히 프로비전되거나 부분적으로 프로비전된 볼륨은 만들 수 없습니다.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## 7단계: 볼륨 탑재, 초기화 및 포맷

Windows Server 호스트에서 이러한 단계를 수행합니다.

> [AZURE.IMPORTANT]

> - StorSimple 솔루션의 고가용성을 위해 iSCSI를 구성하기 전에 호스트 서버에 MPIO를 구성하는 것이 좋습니다(선택 사항). 호스트 서버에 MPIO를 구성하면 서버가 링크, 네트워크 또는 인터페이스 오류를 허용할 수 있습니다.

> - Windows Server 호스트에서 MPIO 및 iSCSI 설치 및 구성 지침은 [StorSimple 장치에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)으로 이동합니다. StorSimple 볼륨을 탑재, 초기화 및 포맷하는 단계도 포함됩니다.

> - Linux 호스트에서 MPIO 및 iSCSI 설치 및 구성 지침은 [StorSimple Linux 호스트에 대한 MPIO 구성](storsimple-configure-mpio-on-linux.md)으로 이동합니다.

MPIO를 구성하지 않으려는 경우 다음 단계를 수행하여 Windows Server 호스트에서 StorSimple 볼륨을 탑재, 초기화 및 포맷합니다.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 8단계: 백업 수행

백업은 볼륨의 지정 시간 보호 기능을 제공하며 복원 시간을 최소화하면서 복구 기능을 개선합니다. StorSimple 장치에서 두 유형(로컬 스냅숏 및 클라우드 스냅숏)의 백업을 수행할 수 있습니다. 이러한 각 유형의 백업은 **예약됨** 또는 **수동**이 될 수 있습니다.

예약된 백업을 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

언제든지 수동 백업을 수행할 수 있습니다. 과정은 [수동 백업 만들기](#create-a-manual-backup)로 이동합니다.

## 서비스에 대한 새 저장소 계정 구성

서비스와 저장소 계정을 자동으로 생성하도록 설정하지 않은 경우에만 수행해야 하는 선택적 단계입니다. StorSimple 볼륨 컨테이너를 만들려면 Microsoft Azure 저장소 계정이 필요합니다.

다른 지역에 Azure 저장소 계정을 만들어야 하는 경우 단계별 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.

**StorSimple 관리자 서비스** 페이지의 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## PuTTY를 사용하여 장치 직렬 콘솔에 연결

StorSimple용 Windows PowerShell에 연결하려면 PuTTY와 같은 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 직렬 콘솔을 통해 직접 또는 원격 컴퓨터에서 텔넷 세션을 열어 장치에 액세스할 때 PuTTY를 사용할 수 있습니다.

[AZURE.INCLUDE [장치 직렬 콘솔 연결에 PuTTY 사용](../../includes/storsimple-use-putty.md)]

## 업데이트 검색 및 적용

장치 업데이트는 몇 시간이 걸릴 수 있습니다. 다음 단계를 수행하여 장치에서 업데이트를 검색 및 적용합니다.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### 장치를 업데이트하려면

1.	장치 **빠른 시작** 페이지에서 **장치**를 클릭합니다. 물리적 장치를 선택하고 **유지 관리**를 클릭한 다음 **업데이트 검색**을 클릭합니다.
2.	사용 가능한 업데이트를 검색하는 작업이 만들어집니다. 업데이트를 사용할 수 있는 경우 **업데이트 검색**이 **업데이트 설치**로 변경됩니다. **업데이트 설치**를 클릭합니다.
3.	업데이트 작업이 만들어집니다. **작업**으로 이동하여 업데이트 상태를 모니터링합니다.

	> [AZURE.NOTE] 업데이트 작업이 시작되면 50%로 상태를 즉시 표시합니다. 업데이트 작업이 완료된 후에 상태를 100%로 변경합니다. 업데이트 프로세스에 대한 실시간 상태가 없습니다.

4.	장치가 성공적으로 업데이트된 후 데이터 2 및 데이터 3이 비활성화된 경우 데이터 2 및 데이터 3 네트워크 인터페이스를 사용하도록 설정합니다.

## Windows Server 호스트의 IQN 가져오기

Windows Server® 2012를 실행하는 Windows 호스트의 iSCSIㅋIQN(정규화된 iSCSI 이름)을 가져오려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [수동 백업 만들기](../../includes/storsimple-get-iqn.md)]

## 수동 백업 만들기

StorSimple 장치에서 단일 볼륨에 대한 주문형 수동 백업을 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [수동 백업 만들기](../../includes/storsimple-create-manual-backup-gov.md)]

## MPIO 구성

MPIO(다중 경로 I/O)는 선택적 기능이며 Windows Server에 기본적으로 설치되지 않습니다. 서버 관리자를 통해 기능으로 설치해야 합니다. MPIO 설치 지침은 [StorSimple 장치에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)으로 이동합니다.

Linux 호스트에 연결된 StorSimple 도구에 대한 MPIO 설치 지침은 [Linux 호스트에 대한 MPIO 구성](storsimple-configure-mpio-on-linux.md)으로 이동합니다.

> [AZURE.NOTE] MPIO는 Azure의 StorSimple 가상 장치에서 지원되지 않습니다.

## 다음 단계

- [가상 장치](storsimple-virtual-device-u2.md)를 구성합니다.

- [StorSimple 관리자 서비스](https://msdn.microsoft.com/library/azure/dn772396.aspx)를 사용하여 StorSimple 장치를 관리할 수 있습니다.
 

<!---HONumber=AcomDC_0914_2016-->