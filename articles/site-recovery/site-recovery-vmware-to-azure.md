<properties
	pageTitle="Azure 포털에서 Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제 | Microsoft Azure"
	description="Azure Site Recovery를 배포하여 Azure 포털을 사용하여 온-프레미스 VMware 가상 컴퓨터 및 Windows/Linux 물리적 서버에서 Azure로 복제, 장애 조치(failover) 및 복구를 오케스트레이션하는 방법에 대해 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="raynew"/>

# Azure 포털에서 Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 컴퓨터 복제

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-vmware-to-azure.md)
- [Azure 클래식](site-recovery-vmware-to-azure-classic.md)
- [Azure 클래식(레거시)](site-recovery-vmware-to-azure-classic-legacy.md)

Azure Site Recovery에 오신 것을 환영합니다! Azure 포털에서 Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 복제하려는 경우 이 문서를 사용합니다.

> [AZURE.NOTE] Azure는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md)로 ARM(Azure Resource Manager)과 클래식을 제공합니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 Azure 클래식 포털이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 Azure 포털입니다.

Azure 포털의 Site Recovery는 여러 새 기능을 제공합니다.

- Azure 백업 및 Azure Site Recovery 서비스가 단일 복구 서비스 자격 증명 모음으로 결합되어 있어서 단일 위치에서 BCDR(무중단 업무 방식 및 재해 복구)을 설정하고 관리할 수 있습니다. 통합 대시보드에서 온-프레미스 사이트와 Azure 공용 클라우드의 작업을 모니터링하고 관리할 수 있습니다.
- CSP(클라우드 솔루션 공급자) 프로그램이 포함된 Azure 구독이 있는 사용자는 이제 Azure 포털에서 사이트 복구 작업을 관리할 수 있습니다.
- Azure 포털의 Site Recovery는 ARM 저장소 계정에 컴퓨터를 복제할 수 있습니다. 장애 조치(Failover) 시 Site Recovery는 Azure에 ARM 기반 VM을 만듭니다.
- Site Recovery는 클래식 저장소 계정에 복제하는 작업을 계속해서 지원합니다. 장애 조치(Failover) 시 Site Recovery는 클래식 모델을 사용하여 VM을 만듭니다.

이 문서를 읽은 후 하단의 Disqus 의견에 의견을 남겨 주세요. 기술 관련 문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의하세요.

## 개요

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 유지하고 워크로드를 지속적으로 가용 상태로 유지해야 합니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [Azure Site Recovery란?](site-recovery-overview.md)에서 자세한 내용을 확인해 보세요.

이 문서는 Azure에 온-프레미스 VMware VM 및 Windows/Linux 물리적 서버를 복제하는 데 필요한 모든 정보를 제공합니다. Azure, 온-프레미스 서버, 복제 설정 및 용량 계획을 구성하기 위한 아키텍처 개요, 계획 정보 및 배포 단계가 포함되어 있습니다. 인프라를 설정한 후에는 보호하려는 컴퓨터에서 복제를 활성화하고 장애 조치(Failover)가 작동하는지 확인할 수 있습니다.

## 비즈니스 이점

- Site Recovery는 VMware VM 및 물리적 서버에서 실행되는 비즈니스 워크로드 및 응용 프로그램에 오프사이트 보호를 제공합니다.
- 복구 서비스 포털은 복제, 장애 조치(Failover) 및 복구를 설정, 관리 및 모니터링하는 단일 위치를 제공합니다.
- Site Recovery는 vSphere 호스트에 추가된 VMware VM을 자동으로 검색할 수 있습니다.
- 간편하게 온-프레미스 인프라에서 Azure로 장애 조치(failover)를 실행하고 Azure에서 온-프레미스 사이트의 VMware VM 서버로 장애 복구(failback)(복원)를 수행할 수 있습니다.
- 여러 컴퓨터에 계층화된 응용 프로그램 워크로드가 동시에 복제되도록 여러 VM을 사용하도록 설정하고 복제 그룹을 만들 수 있습니다. 복제 그룹의 모든 컴퓨터는 장애 조치 시(failover) 크래시 일관성 및 앱 일치 복구 지점을 갖습니다. 장애 조치(failover)의 경우 계층화된 응용 프로그램 워크로드가 함께 장애 조치(failover)되도록 복구 계획에서 여러 컴퓨터를 수집할 수 있습니다.

## 지원되는 운영 체제

### Windows(64비트만 해당)
- Windows Server 2008 R2 SP1+
- Windows Server 2012
- Windows Server 2012 R2

### Linux(64비트만 해당)
- Red Hat Enterprise Linux 6.7, 7.1, 7.2
- CentOS 6.5, 6.6, 6.7, 7.0, 7.1, 7.2
- Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5
- SUSE Linux Enterprise Server 11 SP3

## 시나리오 아키텍처

다음은 시나리오 구성 요소입니다.

- **구성 서버**: 통신을 조정하고 데이터 복제 및 복구 프로세스를 관리하는 온-프레미스 컴퓨터입니다. 이 컴퓨터에서 단일 설치 파일을 실행하여 구성 서버 및 이러한 추가 구성 요소를 설치하게 됩니다.
	- **프로세스 서버**:복제 게이트웨이 역할을 합니다. 보호된 원본 컴퓨터에서 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure 저장소로 전송합니다. 또한 보호되는 컴퓨터에서 모바일 서비스의 푸시 설치를 처리하며 VMware VM의 자동 복구를 수행합니다. 기본 프로세스 서버가 구성 서버에 설치됩니다. 추가 독립 실행형 프로세스 서버를 배포하여 배포를 확장할 수 있습니다.
	- **마스터 대상 서버**: Azure에서 장애 복구(failback) 중에 복제 데이터를 처리합니다.

- **모바일 서비스**: 이 구성 요소는 Azure에 복제하고자 하는 모든 컴퓨터(VMware VM 또는 물리적 서버)에 배포됩니다. 컴퓨터에 기록된 데이터를 캡처하고 프로세스 서버에 전달합니다.
- **Azure**: 복제를 처리하고 Azure로 장애 조치(failover)하기 위해 어떤 Azure VM도 만들 필요가 없습니다. Azure 구독, 복제된 데이터를 저장할 Azure 저장소 계정, 장애 조치(failover) 후에 Azure VM이 네트워크에 연결되도록 할 Azure 가상 네트워크가 필요합니다. 저장소 계정 및 네트워크는 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.
- **장애 복구(failback)**: 장애 조치(failover) 후 Azure에서 온-프레미스 사이트로 장애 복구(failback)할 준비가 되면 임시 프로세스 서버로 Azure VM을 만들어야 합니다. 장애 복구(failback)를 완료한 후 삭제할 수 있습니다. 장애 복구(failback)의 경우 온-프레미스 사이트와 Azure VM이 있는 Azure 네트워크 간에 VPN(또는 Azure Express 경로) 연결도 필요합니다. 장애 복구(failback) 트래픽이 많은 경우 전용 마스터 대상 서버 컴퓨터 온-프레미스를 설정해야 할 수도 있습니다. 트래픽이 적은 경우 구성 서버에서 실행되는 기본 마스터 대상 서버를 사용할 수 있습니다.


다음 그래픽은 이러한 구성 요소가 어떻게 상호 작용하는지 보여 줍니다.

![아키텍처](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**그림 1: VMware/실제 서버에서 Azure로**

## Azure 필수 조건

이 시나리오를 배포하려면 Azure에서 다음이 필요합니다.

**필수 요소** | **세부 정보**
--- | ---
**Azure 계정**| [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).
**Azure 저장소** | 복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 생성됩니다. <br/><br/>데이터를 저장하려면 복구 서비스 자격 증명 모음과 동일한 지역에 표준 또는 프리미엄 저장소 계정이 있어야 합니다.<br/><br/>LRS 또는 GRS 저장소 계정을 사용할 수 있습니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. [자세히 알아봅니다](../storage/storage-redundancy.md).<br/><br/> [프리미엄 저장소](../storage/storage-premium-storage.md)는 IO를 많이 사용하는 워크로드를 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 가상 컴퓨터에 일반적으로 사용됩니다.<br/><br/> 프리미엄 계정을 사용하여 복제된 데이터를 저장하려는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장하는 표준 저장소 계정이 필요할 수도 있습니다.<br/><br/> Azure 포털에서 만든 저장소 계정은 리소스 그룹 간에 이동할 수 없습니다. 또한 인도 중부 및 인도 남부의 프리미엄 저장소 계정에 대한 보호는 현재 지원되지 않습니다.<br/><br/> Azure storage에 대해 [알아봅니다](../storage/storage-introduction.md).
**Azure 네트워크** | 장애 조치(Failover) 발생 시 Azure VM에서 연결할 Azure 가상 네트워크가 필요합니다. Azure 가상 네트워크는 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.
**Azure로부터 장애 복구** | Azure VM으로 임시 프로세스 서버를 설정해야 합니다. 장애 복구(failback)가 준비되면 이를 만들고 장애 복구(failback)가 완료된 후 삭제할 수 있습니다.<br/><br/> 장애 복구(failback)하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 Azure Express 경로)이 필요합니다.

## 구성 서버 필수 조건

온-프레미스 컴퓨터를 구성 서버로 설정합니다.

**필수 요소** | **세부 정보**
--- | ---
**구성 서버**| Windows Server 2012 R2를 실행하는 온-프레미스 물리적 또는 가상 컴퓨터가 있어야 합니다. 모든 온-프레미스 사이트 복구 구성 요소가 이 컴퓨터에 설치됩니다.<br/><br/>VMware VM 복제의 경우 서버를 고가용성의 VMware VM으로 배포하는 것이 좋습니다. 물리적 컴퓨터를 복제하는 경우 컴퓨터가 물리적 서버가 될 수 있습니다.<br/><br/> Azure에서 온-프레미스 사이트로 장애 복구(failback)는 VM 또는 물리적 서버 장애 조치에 관계없이 항상 VMware VM으로 이루어집니다. VMware VM으로 구성 서버를 배포하지 않는 경우 장애 복구(failback) 트래픽을 수신하려면 VMware VM으로 별도 마스터 대상 서버를 설정해야 합니다.<br/><br/>서버가 VMware VM인 경우 네트워크 어댑터 형식은 VMXNET3이어야 합니다. 다른 형식의 네트워크 어댑터를 사용하는 경우 [VMware 업데이트](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)를 vSphere 5.5 서버에 설치해야 합니다.<br/><br/>서버에는 고정 IP 주소가 있어야 합니다.<br/><br/>서버는 도메인 컨트롤러가 아니어야 합니다.<br/><br/>서버의 호스트 이름은 15자 이하여야 합니다.<br/><br/>운영 체제는 영어만 사용해야 합니다.<br/><br/> VMware vSphere PowerCLI 6.0을 구성 서버에 설치해야 합니다.<br/><br/>구성 서버는 인터넷 액세스가 필요합니다. 다음과 같이 아웃바운드 액세스가 필요합니다.<br/><br/>사이트 복구 구성 요소 설치 동안 HTTP 80에서의 임시 액세스(MySQL 다운로드)<br/><br/>복제 관리에 대한 HTTPS 443에서의 지속적인 아웃바운드 액세스<br/><br/>복제 트래픽에 대한 HTTPS 9443에서의 지속적인 아웃바운드 액세스(이 포트를 수정할 수 있음)<br/><br/>Azure에 연결할 수 있도록 서버에서는 다음 URL에 대한 액세스도 필요합니다. *.hypervrecoverymanager.windowsazure.com, *.accesscontrol.windows.net, *.backup.windowsazure.com, *.blob.core.windows.net, *.store.core.windows.net<br/><br/>서버의 IP 주소 기반 방화벽 규칙이 있는 경우 규칙이 Azure에 대한 통신을 허용하는지 확인합니다. [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 프로토콜을 허용해야 합니다.<br/><br/>구독의 Azure 지역 및 미국 서부에 대한 IP 주소 범위를 허용합니다.<br/><br/>MySQL 다운로드를 위해 다음 URL을 허용합니다. .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## VMware vCenter/vSphere 호스트 필수 조건

**필수 요소** | **세부 정보**
--- | ---
**vSphere**| 하나 이상의 VMware vSphere 하이퍼바이저가 있어야 합니다.<br/><br/>하이퍼바이저는 최신 업데이트가 포함된 vSphere 버전 6.0, 5.5 또는 5.1이 실행되어야 합니다.<br/><br/>vSphere 호스트 및 vCenter Server는 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 이 네트워크는 전용 프로세스 서버를 설정하지 않는 한 구성 서버가 있는 네트워크입니다.
**vCenter** | vSphere 호스트를 관리하는 VMware vCenter Server를 배포하는 것이 좋습니다. 최신 업데이트와 함께 vCenter 버전 6.0 또는 5.5를 실행해야 합니다.<br/><br/>사이트 복구에서는 교차 vCenter vMotion, 가상 볼륨 및 저장소 DRS와 같은 새로운 vCenter 및 vSphere 6.0 기능을 지원하지 않습니다. 사이트 복구 지원은 버전 5.5에서도 사용할 수 있는 기능에 제한됩니다.


## 보호된 컴퓨터 필수 조건

**필수 요소** | **세부 정보**
--- | ---
**온-프레미스 VMware VM** | 보호하려는 VMware VM에는 VMware 도구가 설치되어 있고 실행 중이어야 합니다.<br/><br/> 보호하려는 컴퓨터가 Azure VM을 만들기 위한 [Azure 필수 조건](site-recovery-best-practices.md#azure-virtual-machine-requirements)을 준수해야 합니다.<br/><br/>보호된 컴퓨터의 개별 디스크 용량은 1023GB를 초과해서는 안 됩니다. VM은 최대 64개의 디스크(따라서 최대 64TB)를 포함할 수 있습니다. <br/><br/>암호화된 디스크(데이터 디스크와 루트)로 VM을 보호하는 것은 지원되지 않습니다.

공유 디스크 게스트 클러스터는 지원되지 않습니다.<br/><br/>**응용 프로그램 일관성**을 사용하도록 설정하려면 보호된 VM에서 **포트 20004**를 열어 두어야 합니다.

UEFI(Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) 부팅은 지원되지 않습니다.<br/><br/>컴퓨터 이름은 1자에서 63자(문자, 숫자 및 하이픈) 사이여야 합니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. 컴퓨터에 대한 복제를 사용하도록 설정한 후 Azure 이름을 수정할 수 있습니다.<br/><br/>원본 VM에 NIC 팀이 있으면 Azure로 장애 조치(failover) 후 단일 NIC로 변환됩니다.<br/><br/>보호된 VM에 iSCSI 디스크가 있으면 VM이 Azure로 장애 조치(failover)될 때 사이트 복구가 보호된 VM iSCSI 디스크를 VHD 파일로 변환합니다. Azure VM에서 iSCSI 대상에 연결할 수 있는 경우 이 대상에 연결되며 기본적으로 두 개의 디스크(Azure VM의 VHD 디스크 및 원본 iSCSI 디스크)가 표시됩니다. 이 경우 Azure VM에 표시되는 iSCSI 대상의 연결을 끊어야 합니다. **Windows 컴퓨터(실제 또는 VMware)** | 컴퓨터에서 지원되는 64비트 운영 체제(Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 SP1 이상)를 실행해야 합니다.<br/><br/> 운영 체제는 C:\\ 드라이브에 설치해야 합니다. OS 디스크는 동적이 아닌 Windows 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/>사이트 복구는 RDM 디스크를 사용한 VM을 지원합니다. 원래 원본 VM과 RDM 디스크를 사용할 수 있는 경우 Site Recovery에서는 장애 복구(failback) 중에 RDM 디스크를 다시 사용합니다. 사용할 수 없는 경우 장애 복구(failback) 동안 Site Recovery가 각 디스크에 대한 새 VMDK 파일을 만듭니다. **Linux machines** | 지원되는 64비트 운영 체제(Red Hat Enterprise Linux 6.7,7.1,7.2/Centos 6.5, 6.6,6.7,7.0,7.1,7.2/Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5/SUSE Linux Enterprise Server 11 SP3)가 필요합니다.<br/><br/>보호된 컴퓨터의 /etc/hosts 파일은 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소로 매핑하는 항목이 포함되어 있어야 합니다.<br/><br/>장애 조치(failover) 후 SSH(Secure Shell) 클라이언트를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 연결하려면 보호된 컴퓨터의 Secure Shell 서비스가 시스템 부팅 시 자동으로 시작하도록 설정되어 있고 방화벽 규칙에서 SSH 연결을 허용하지를 확인해야 합니다.<br/><br/>호스트 이름, 마운트 지점, 장치 이름, Linux 시스템 경로와 파일 이름(예: /etc/; /usr)은 영어로만 입력해야 합니다.<br/><br/>다음 저장소를 사용하여 Linux 컴퓨터에 보호를 사용하도록 설정할 수 있습니다. 파일 시스템: EXT3, ETX4, ReiserFS, XFS/다중 경로 소프트웨어-장치 매퍼(다중 경로)/볼륨 관리자: (LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.<br/><br/>사이트 복구에서는 RDM 디스크를 사용한 VM을 지원합니다. Linux에 대한 장애 복구(failback) 중에는 사이트 복구에서 RDM 디스크를 다시 사용하지 않습니다. 대신 각 해당 RDM 디스크에 대한 새 VMDK 파일을 만듭니다.<br/><br/>VMware에서 VM의 구성 매개 변수에 disk.enableUUID=true를 설정해야 합니다. 없는 경우 항목을 만듭니다. 올바르게 탑재할 수 있도록 VMDK에 일관성 있는 UUID를 제공해야 합니다. 이 설정을 추가하면 장애 복구(failback) 동안 전체 복제가 아닌 델타 변경 내용만 온-프레미스로 다시 전송됩니다. **모바일 서비스** | **Windows**: Windows를 실행하는 VM에 모바일 서비스를 자동으로 푸시하려면 프로세스 서버가 푸시 설치를 수행할 수 있도록 관리자 계정(Windows 컴퓨터의 로컬 관리자)을 제공해야 합니다.<br/><br/> **Linux**: Linux를 실행하는 VM에 모바일 서비스를 자동으로 푸시하려면 프로세스 서버에서 푸시 설치를 수행하는 데 사용할 수 있는 계정을 만들어야 합니다.<br/><br/> 기본적으로 컴퓨터의 모든 디스크는 복제됩니다. [복제에서 디스크를 제외하려면](#exclude-disks-from-replication) 복제를 사용하도록 설정하기 전에 컴퓨터에 모바일 서비스를 수동으로 설치해야 합니다.

## 배포 준비

배포를 준비하기 위해 다음을 수행해야 합니다.

1. 장애 조치(failover) 후 Azure VM이 작동될 때 배치될 [Azure 네트워크를 설정합니다](#set-up-an-azure-network). 또한 장애 복구(failback)를 수행하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 Azure Express 경로)을 설정해야 합니다.
2. 복제 데이터를 저장할 [Azure 저장소 계정을 설정](#set-up-an-azure-storage-account)합니다.
3. 사이트 복구가 추가되는 VMware VM을 자동으로 검색할 수 있도록 vCenter Server 또는 vSphere 호스트에 [계정을 준비합니다](#prepare-an-account-for-automatic-discovery).
4. 필수 URL에 액세스하고 vSphere PowerCLI 6.0을 설치할 수 있도록 [구성 서버를 준비합니다](#prepare-the-configuration-server).


### Azure 네트워크 설정

- 이 네트워크는 복구 서비스 자격 증명 모음을 배포할 지역과 동일한 Azure 지역에 있어야 합니다.
- 장애 조치(failover)된 Azure VM에 사용하려는 리소스 모델에 따라 [ARM 모드](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 또는 [클래식 모드](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 Azure 네트워크를 설정합니다.
- Azure에서 온-프레미스 VMware 사이트로 장애 복구(failback)를 하기 위해 복제된 Azure VM이 있는 Azure 네트워크에서 구성 서버가 있는 온-프레미스 네트워크로의 VPN 연결(또는 Azure Express 경로 연결)이 필요합니다.
- VPN 사이트 간 연결에 대한 지원되는 배포 모델 및 [연결 설정](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network) 방법에 대해 [알아봅니다](../vpn-gateway/vpn-gateway-site-to-site-create.md).
- 또는 [Azure Express 경로](../expressroute/expressroute-introduction.md)를 설정할 수 있습니다. Express 경로를 사용하여 Azure 네트워크를 설정하는 방법에 대해 [자세히 알아봅니다](../expressroute/expressroute-howto-vnet-portal-classic.md).

> [AZURE.NOTE] [Migration of networks](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 네트워크에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

### Azure 저장소 계정 설정

- Azure로 복제된 데이터를 저장하려면 표준 또는 프리미엄 Azure 저장소 계정이 있어야 합니다. 계정은 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다. 장애 조치(failover)된 Azure VM에 사용하려는 리소스 모델에 따라 [ARM 모드](../storage/storage-create-storage-account.md) 또는 [클래식 모드](../storage/storage-create-storage-account-classic-portal.md)에서 계정을 설정합니다.
- 복제된 데이터에 프리미엄 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 계정을 만들어야 합니다.

> [AZURE.NOTE] [Migration of storage accounts](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 저장소 계정에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

### 자동 검색용 계정 준비

Site Recovery 프로세스 서버는 vSphere 호스트 또는 호스트를 관리하는 vCenter Server에서 VMware VM을 자동으로 검색할 수 있습니다. 자동 검색을 수행하려면 Site Recovery에 VMware 서버에 액세스할 수 있는 자격 증명이 필요합니다. 물리적 컴퓨터에만 복제하는 경우 관련이 없습니다.

1. 자동 검색에 대한 전용 계정을 사용하려면 [필요한 권한](#vmware-account-permissions)이 있는 vCenter 수준에서 역할(예: Azure\_Site\_Recovery)을 만듭니다.
2. vSphere 호스트 또는 vCenter Server에 새 사용자를 만들고 사용자에게 역할을 할당합니다. 나중에 자동 검색을 수행할 수 있도록 이러한 자격 증명에 대해 Site Recovery에 알릴 수 있습니다.

	>[AZURE.NOTE] 읽기 전용 역할이 있는 vCenter 사용자 계정은 장애 조치(failover)를 실행할 수 있지만 보호된 원본 컴퓨터를 종료할 수 없습니다. 이러한 컴퓨터를 종료하려는 경우 [Azure\_Site\_Recovery](#vmware-account-permissions) 역할이 필요합니다. VMware에서 Azure로 VM을 마이그레이션하고 장애 복구(failback)가 필요하지 않은 경우 읽기 전용 역할이면 충분합니다.

### 구성 서버 준비

1.	구성 서버에 사용하는 컴퓨터가 [필수 조건](#configuration-server-prerequisites)을 준수하는지 확인합니다. 특히 컴퓨터가 다음 설정과 함께 인터넷에 연결되어 있는지 확인합니다.

	- 다음 URL에 대한 액세스를 허용합니다. *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net
	- MySQL을 다운로드하도록 [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi)에 대한 액세스를 허용합니다.
	- [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 프로토콜과 함께 Azure와의 방화벽 통신을 허용합니다.

2.	구성 서버에 [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)을 다운로드 및 설치합니다. (현재 버전 6.0의 R 릴리스를 비롯한 다른 버전의 PowerCLI는 지원되지 앟습니다.)


## 복구 서비스 자격 증명 모음 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** > **관리** > **백업 및 사이트 복구(OMS)**를 클릭합니다. 또는 **찾아보기** > **복구 서비스** 자격 증명 모음 > **추가**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 그중에서 하나를 선택합니다.
4. [새 리소스 그룹을 만들거나](../resource-group-template-deploy-portal.md) 기존 그룹을 선택합니다. Azure 지역을 지정합니다. 이 지역에 컴퓨터가 복제됩니다. Site Recovery에 사용되는 Azure 저장소 및 네트워크는 동일한 지역에 있어야 합니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하십시오.
4. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

**대시보드** > **모든 리소스**와 주 **복구 서비스 자격 증명 모음** 블레이드에 새 자격 증명 모음이 표시될 것입니다.

## 시작

Site Recovery는 가능한 한 빨리 실행하도록 디자인된 시작 환경을 제공합니다. 필수 조건을 확인하고 Site Recovery를 배포하는 데 필요한 단계를 안내합니다.

복제할 컴퓨터 유형과 복제할 위치를 선택합니다. 온-프레미스 서버, Azure 설정, 복제 정책 및 용량 계획을 포함하여 인프라를 설정합니다. 인프라를 갖춘 후에 VM 및 물리적 서버의 복제를 사용하도록 설정합니다. 그런 다음 특정 컴퓨터에 대해 장애 조치(failover)를 실행하거나 여러 컴퓨터를 장애 조치(failover)하는 복구 계획을 만들 수 있습니다.

Site Recovery 배포 방법을 선택하여 시작 환경을 시작합니다. 복제 요구 사항에 따라 시작 환경의 흐름이 약간 달라질 수 있습니다.


## 1단계: 보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **복구 서비스 자격 증명 모음** 블레이드에서 자격 증명 모음을 선택하고 **설정**을 클릭합니다.
2. **설정** > **시작**에서 **사이트 복구** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

	![목표 선택](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. **보호 목표**에서 **Azure**를 선택하고 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 후 **OK**를 클릭합니다.

	![목표 선택](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## 2단계: 소스 환경 설정

구성 서버를 설정하고 복구 서비스 자격 증명 모음에 등록합니다. VMware VM을 복제하는 경우 자동 검색에 사용하는 VMware 계정을 지정합니다.

1. **1단계: 인프라 준비** > **원본**을 클릭합니다. **원본 준비**에서 구성 서버가 없는 경우 **+구성 서버**를 클릭하여 하나를 추가합니다.

	![원본 설정](./media/site-recovery-vmware-to-azure/set-source1.png)

2. **서버 추가** 블레이드에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
3. 구성 서버를 설정하기 전에 [필수 조건](#configuration-server-prerequisites)을 확인합니다. 특히 컴퓨터가 필요한 URL에 액세스할 수 있는지 확인합니다.
4.	Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5.	자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

	![원본 설정](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	구성 서버로 사용 중인 컴퓨터에서 통합 설치 프로그램을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다.


### Site Recovery 통합 설치 프로그램 실행

1.	통합 설치 프로그램 설치 파일을 실행합니다.
2.	**시작하기 전**에서 **구성 서버 및 프로세스 서버 설치**를 선택합니다.

	![시작하기 전에](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. **타사 소프트웨어 라이선스**에서 **동의함**을 클릭하고 MySQL을 다운로드 및 설치합니다.

	![타사 소프트웨어](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. **등록**에서 자격 증명 모음에서 다운로드한 등록 키를 찾아 선택합니다.

	![등록](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. **인터넷 설정**에서 구성 서버에서 실행 중인 공급자가 인터넷을 통해 Azure Site Recovery에 연결되는 방법을 지정합니다.

	- 현재 컴퓨터에 설정된 프록시를 사용하여 연결하려면 **기존 프록시 설정을 사용하여 연결**을 선택합니다.
	- 공급자가 직접 연결되도록 하려면 **프록시 없이 직접 연결**을 선택합니다.
	- 기존 프록시에 인증이 필요하거나 공급자 연결에 대해 사용자 지정 프록시를 사용하려면 **사용자 지정 프록시 설정을 사용하여 연결**을 선택합니다.
		- 사용자 지정 프록시를 사용하는 경우 주소, 포트 및 자격 증명을 지정해야 합니다.
		- 프록시를 사용하는 경우 [필수 조건](#configuration-server-prerequisites)에 설명된 URL을 이미 허용했어야 합니다.

	![방화벽](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. **필수 조건 확인**에서 설치 프로그램은 설치가 실행될 수 있는지 확인합니다. **글로벌 시간 동기화 확인**에 대한 경고가 표시되면 시스템 클록의 시간(**날짜 및 시간** 설정)이 표준 시간대와 같은지 확인합니다.

	![필수 조건](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. **MySQL 구성**에서 설치되는 MySQL 서버 인스턴스에 로그온할 자격 증명을 만듭니다.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. **환경 세부 정보**에서 VMware VM을 복제 여부를 선택합니다. 복제할 경우 설치 프로그램에서 PowerCLI 6.0이 설치되어 있는지 확인합니다.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. **설치 위치**에서 이진 파일을 설치하고 캐시를 저장할 위치를 선택합니다. 최소 5GB의 저장 공간이 있는 드라이브를 선택할 수 있지만 600GB 이상의 사용 가능한 공간이 있는 캐시 드라이브를 선택하는 것이 좋습니다.

	![설치 위치](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. **네트워크 선택**에서 구성 서버가 복제 데이터를 전송 및 수신할 수신기(네트워크 어댑터 및 SSL 포트)를 지정합니다. 기본 포트(9443)를 수정할 수 있습니다. 이 포트 외에도 복제 작업을 오케스트레이션하는 웹 서버에서 포트 443이 사용됩니다. 443은 복제 트래픽을 수신하는 데 사용할 수 없습니다.


	![네트워크 선택](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  **요약**에서 정보를 검토하고 **설치**를 클릭합니다. 설치가 완료되면 암호가 생성됩니다. 복제를 사용하도록 설정하는 경우 필요하므로 암호를 복사하고 안전한 위치에 보관합니다.

	![요약](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  등록이 완료되면 자격 증명 모음의 **설정** > **서버** 블레이드에 서버가 표시됩니다.



#### 명령줄에서 설치 실행

명령줄에서 구성 서버를 설정할 수 있습니다.

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

설치가 끝난 경우 등록을 완료하려면:

1. Windows 시작 메뉴에서 "Microsoft Azure 복구 서비스 셸"이라는 응용 프로그램을 시작합니다.
2. 열리는 명령 창에서 다음과 같은 명령 집합을 실행하여 프록시 서버 설정을 구성합니다.

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

매개 변수:

- /ServerMode: 필수. 구성 및 프로세스 서버를 모두 설치할지 또는 프로세스 서버만 설치할지 여부를 지정합니다. 입력 값: CS, PS.
- InstallLocation: 필수. 구성 요소가 설치되어 있는 폴더입니다.
- /MySQLCredsFilePath. 필수. MySQL 서버 자격 증명이 저장되어 있는 파일 경로입니다. 파일은 다음 형식이어야 합니다.
	- [MySQLCredentials]
	- MySQLRootPassword = "<Password>"
	- MySQLUserPassword = "<Password>"
- /VaultCredsFilePath. 필수. 자격 증명 모음 자격 증명 파일의 위치입니다.
- /EnvType. 필수. 설치 유형입니다. 값: VMware, NonVMware
- /PSIP 및 /CSIP. 필수. 프로세스 서버와 구성 서버의 IP 주소입니다.
- /PassphraseFilePath. 필수. 암호 파일의 위치입니다.
- /ByPassProxy. 선택 사항입니다. 구성 서버가 프록시 없이 Azure에 연결되도록 지정합니다.
- /ProxySettingsFilePath. 선택 사항입니다. 프록시 설정(인증이 필요한 기본 프록시 또는 사용자 지정 프록시)입니다. 파일은 다음 형식이어야 합니다.
	- [ProxySettings]
	- ProxyAuthentication = "Yes/No"
	- Proxy IP = "IP Address>"
	- ProxyPort = "<포트>"
	- ProxyUserName="<사용자 이름>"
	- ProxyPassword="<암호>"
- DataTransferSecurePort. 선택 사항입니다. 복제 데이터에 사용할 포트 번호입니다.
- SkipSpaceCheck. 선택 사항입니다. 캐시에 대한 공간 확인을 건너뜁니다.
- AcceptThirdpartyEULA. 필수. 플래그는 타사 EULA에 대한 동의를 의미합니다.
- ShowThirdpartyEULA. 필수. 타사 EULA를 표시합니다. 입력으로 제공되는 경우 다른 모든 매개 변수가 무시됩니다.

### 자동 검색에 사용되는 VMware 계정 추가

 배포에 대해 준비한 경우 사이트 복구에서 자동 검색에 사용할 수 있는 [VMware 계정을 만들었어야](#prepare-an-account-for-automatic-discovery) 합니다. 이 계정은 다음과 같이 추가합니다.

1. **CSPSConfigtool.exe**를 엽니다. 바탕 화면에 바로 가기로 사용 가능하며 [설치 위치]\\home\\svsystems\\bin 폴더에 있습니다.
2. **계정 관리** > **계정 추가**를 클릭합니다.

	![계정 추가](./media/site-recovery-vmware-to-azure/credentials1.png)

3. **계정 세부 정보**에서 자동 검색에 사용할 계정을 추가합니다. 계정 이름이 포털에 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > 서버 이름 > **서버 새로 고침**을 클릭합니다.

	![세부 정보](./media/site-recovery-vmware-to-azure/credentials2.png)

### vSphere 호스트 및 vCenter Server에 연결

VMware VM을 복제하는 경우 vSphere 호스트 및 vCenter Server에 연결합니다.

1. 구성 서버에 vSphere 호스트 및 vCenter Server에 대한 네트워크 액세스가 있는지 확인합니다.
2. **인프라 준비** > **원본**을 클릭합니다. **원본 준비**에서 구성 서버를 선택하고 **+vCenter**를 클릭하여 vSphere 호스트 또는 vCenter Server를 추가합니다.
3. **vCenter 추가**에서 vSphere 호스트 또는 vCenter Server에 대한 식별 이름을 지정하고 서버의 IP 주소 또는 FQDN을 지정합니다. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다. 그런 다음 VMware 서버에 연결하는 데 사용할 계정을 선택합니다. **확인**을 클릭합니다.

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] vCenter 또는 호스트 서버에 대해 관리자 권한이 없는 계정으로 vCenter Server 또는 vSphere 호스트를 추가하는 경우 계정에 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터, vSphere Distributed Switch에 대한 권한이 설정되어 있는지 확인합니다. 또한 vCenter Server에는 저장소 보기 권한이 필요합니다.

Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

## 3단계: 대상 환경 설정

복제용 저장소 계정 및 장애 조치(failover) 후 Azure VM이 연결할 Azure 네트워크가 있는지 확인합니다.

1.	**인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2.	장애 조치(failover) 후 VM에 사용하려는 배포 모델을 지정합니다.
3.	Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

	![대상](./media/site-recovery-vmware-to-azure/gs-target.png)

4.	아직 저장소 계정을 만들지 않았으며 ARM을 사용하여 계정을 만들려면 **+저장소** 계정을 클릭하여 인라인에서 계정을 만듭니다. **저장소 계정 만들기** 블레이드에서 계정 이름, 형식, 구독 및 위치를 지정합니다. 계정이 복구 서비스 자격 증명 모음과 같은 지역에 있어야 합니다.

	![저장소](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	다음 사항에 유의하세요.

	- 클래식 모델을 사용하여 저장소 계정을 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아보기](../storage/storage-create-storage-account-classic-portal.md)
	- 복제된 데이터에 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 저장소 계정을 설정해야 합니다.
	
	> [AZURE.NOTE] 인도 중부 및 인도 남부의 프리미엄 저장소 계정에 대한 보호는 현재 지원되지 않습니다.

4.	Azure 네트워크를 선택합니다. 아직 네트워크를 만들지 않았으며 ARM을 사용하여 네트워크를 만들려면 **+네트워크**를 클릭하여 인라인에서 네트워크를 만듭니다. **가상 네트워크 만들기** 블레이드에서 네트워크 이름, 주소 범위, 서브넷 세부 정보, 구독 및 위치를 지정합니다. 네트워크가 복구 서비스 자격 증명 모음과 같은 위치에 있어야 합니다.

	![네트워크](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

	클래식 모델을 사용하여 네트워크를 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아봅니다](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## 4단계: 복제 설정 지정

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다.
3. **RPO 임계값**: RPO 제한을 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
5. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다. 프리미엄 저장소에 복제된 컴퓨터에 대해 최대 24시간의 보존이 지원됩니다.
6. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(분)를 지정합니다.
7. 복제 정책을 만들 때 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책은 **rep-policy-failback**이 됩니다. 이 정책은 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.
8. **확인**을 클릭하여 정책을 만듭니다.

	![복제 정책](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. 새 정책을 만들면 새 정책이 자동으로 구성 서버에 연결됩니다. **확인**을 클릭합니다.

	![복제 정책](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## 5단계: 용량 계획

기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악할 수 있습니다.

Site Recovery는 원본 환경, 사이트 복구 구성 요소, 네트워킹 및 저장소에 적절한 리소스를 할당할 수 있도록 도와주는 Capacity Planner를 제공합니다. 평균 VM, 디스크 및 저장소 수를 기반으로 예측하는 빠른 모드 또는 워크로드 수준에서 숫자를 입력하는 세부 모드에서 플래너를 실행할 수 있습니다. 시작하기 전에 다음을 수행해야 합니다.

- VM, VM당 디스크, 디스크당 저장소를 포함하여 복제 환경에 대한 정보를 수집합니다.
- 복제된 데이터에 대한 일일 변경(이탈)률을 예측합니다. 이 작업을 수행하는 데 도움이 되는 [vSphere 용량 계획 어플라이언스](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)를 사용할 수 있습니다.

1.	**다운로드**를 클릭하여 도구를 다운로드한 후 실행하세요. 도구와 함께 제공되는 [문서를 읽어보세요](site-recovery-capacity-planner.md).
2.	작업을 마쳤으면 **용량 계획을 완료하셨나요**?에서 **예**를 선택합니다.

	![용량 계획](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

아래 표에는 이 시나리오에 대한 용량 계획에 도움이 되는 다양한 사항이 나와 있습니다.


**구성 요소** | **세부 정보**
--- | --- | ---
**복제** | **일일 최대 변경률** - 보호된 컴퓨터는 하나의 프로세스 서버만 사용할 수 있으며 단일 프로세스 서버는 최대 2TB의 일일 최대 변경률을 처리할 수 있습니다. 따라서 2TB는 보호되는 컴퓨터에 대해 지원되는 최대 일일 데이터 변경률입니다.<br/><br/> **최대 처리량**—복제된 컴퓨터는 Azure에서 하나의 저장소 계정에 속할 수 있습니다. 표준 저장소 계정은 초당 최대 20,000개의 요청을 처리할 수 있으며 원본 컴퓨터 간에 IOPS 수를 20,000으로 유지하는 것이 좋습니다 예를 들어 원본 컴퓨터의 디스크가 5개이고 각 디스크가 원본에서 120 IOPS(8K 크기)를 생성할 경우 Azure 내에서 디스크당 IOPS 한도인 500을 초과하지 않습니다. 필요한 저장소 계정 수 = 총 원본 IOPS/20000.
**구성 서버** | 구성 서버는 보호된 컴퓨터에서 실행되는 모든 워크로드에 대한 일일 변경률 용량을 처리할 수 있어야 하며 데이터를 Azure storage로 지속적으로 복제할 만큼 충분한 대역폭을 보유해야 합니다.<br/><br/> 가장 좋은 방법은 구성 서버가 보호할 컴퓨터와 동일한 네트워크 및 LAN 세그먼트에 있는 것입니다. 다른 네트워크에 있을 수 있지만 보호할 컴퓨터에서 L3 네트워크를 볼 수 있어야 합니다.<br/><br/> 구성 서버에 대한 크기 권장 사항이 아래 표에 요약되어 있습니다.
**프로세스 서버** | 첫 번째 프로세스 서버는 기본적으로 구성 서버에 설치됩니다. 사용자 환경의 크기를 조정하는 추가 프로세스 서버를 배포할 수 있습니다. 참고:<br/><br/> 프로세스 서버는 보호된 원본 컴퓨터에서 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화한 후 Azure로 전송합니다. 프로세스 서버 컴퓨터에는 이러한 작업을 수행할 충분한 리소스가 있어야 합니다.<br/><br/> 프로세스 서버는 디스크 기반 캐시를 사용합니다. 네트워크 병목 현상 또는 중단이 발생하는 경우 저장된 데이터 변경을 처리할 수 있도록 600GB 이상의 별도의 캐시 디스크를 사용하는 것이 좋습니다.

### 구성 서버에 대한 크기 권장 사항

**CPU** | **메모리** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz) | 16GB | 300GB | 500GB 이하 | 100대 미만의 컴퓨터를 복제합니다.
12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz) | 18GB | 600GB | 500GB ~ 1TB | 100-150대 컴퓨터를 복제합니다.
16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz) | 32GB | 1TB | 1TB ~ 2TB | 150-200대 컴퓨터를 복제합니다.
다른 프로세스 서버 배포 | | | > 2TB | 200대 이상의 컴퓨터를 복제하는 경우 또는 일일 데이터 변경률이 2TB를 초과하는 경우 추가 프로세스 서버를 배포합니다.

여기서,

- 각 원본 컴퓨터는 각각 100GB의 디스크 3개로 구성됩니다.
- 캐시 디스크 측정을 위해 벤치마킹 저장소로 RAID 10을 이용한 10K RPM의 8개 SAS 드라이브를 사용했습니다.

### 프로세스 서버에 대한 크기 권장 사항

200대보다 많은 컴퓨터를 보호해야 하거나 일일 변경률이 2TB를 초과하는 경우 복제 로드를 처리할 프로세스 서버를 더 추가할 수 있습니다. 다음과 같이 규모를 확장할 수 있습니다.

- 구성 서버의 수를 늘립니다. 예를 들어 구성 서버를 두 대 사용할 경우 최대 400대의 컴퓨터를 보호할 수 있습니다.
- 프로세스 서버를 더 추가하고 추가한 프로세스 서버를 사용하여 구성 서버 대신(또는 추가로) 트래픽을 처리할 수 있습니다.

다음 표에서는 이에 대한 시나리오를 설명합니다.

- 구성 서버를 프로세스 서버로 사용할 계획이 없습니다.
- 추가 프로세스 서버를 설정했습니다.
- 추가 프로세스 서버를 사용하도록 보호된 가상 컴퓨터를 구성했습니다.
- 보호된 원본 컴퓨터는 각각 100GB의 디스크 3개로 구성됩니다.

**구성 서버** | **추가 프로세스 서버**| **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터**
--- | --- | --- | --- | ---
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 16GB 메모리 | 4개 vCPU(2개 소켓 * 2코어 @ 2.5GHz), 8GB 메모리 | 300GB | 250GB 이하 | 85대 이하의 컴퓨터를 복제합니다.
8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 16GB 메모리 | 8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 12GB 메모리 | 600GB | 250GB ~ 1TB | 85-150대 컴퓨터를 복제합니다.
12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz), 18GB 메모리 | 12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz), 24GB 메모리 | 1TB | 1TB ~ 2TB | 150-225대 컴퓨터를 복제합니다.


서버 크기를 조정하는 방법은 모델을 강화할지, 규모를 확장할지에 대한 선호도에 따라 달라집니다. 몇 가지 고급 구성 및 프로세스 서버를 배포하여 강화하거나 적은 리소스로 더 많은 서버를 배포하여 규모를 확장합니다. 예를 들어 220대 컴퓨터를 보호해야 하는 경우 다음 중 하나를 수행할 수 있습니다.

- 구성 서버를 12vCPU, 18GB 메모리로, 추가 프로세스 서버를 12vCPU, 24GB 메모리로 설정하고 추가 프로세스 서버만 사용하도록 보호된 컴퓨터를 구성합니다.
- 또는 두 구성 서버(2 x 8vCPU, 16GB RAM)와 두 추가 프로세스 서버(1 x 8vCPU 및 4vCPU x 1로 135 + 85(220)대 컴퓨터 처리)를 구성하고 추가 프로세스 서버만 사용하도록 보호된 컴퓨터를 구성합니다.

[이 지침에 따라](#deploy-additional-process-servers) 추가 프로세스 서버를 설정합니다.

### 네트워크 대역폭 고려 사항

Capacity Planner 도구를 사용하여 복제(초기 복제 그 후에 델타)에 필요한 대역폭을 계산할 수 있습니다. 복제에 사용되는 대역폭 사용량을 제어하는 몇 가지 옵션이 있습니다.

- **대역폭 제한**: Azure에 복제하는 VMware 트래픽이 특정 프로세스 서버를 통과합니다. 프로세스 서버로 실행되는 컴퓨터에서 대역폭을 제한할 수 있습니다.
- **대역폭 영향**: 몇 가지 레지스트리 키를 사용하면 복제에 사용되는 대역폭에 영향을 줄 수 있습니다.
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\UploadThreadsPerVM** 레지스트리 값은 디스크의 데이터 전송(초기 또는 델타 복제)에 사용되는 스레드 수를 지정합니다. 값이 높을수록 복제에 사용되는 네트워크 대역폭이 증가합니다.
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\DownloadThreadsPerVM**은 장애 복구(failback) 동안 데이터 전송에 사용되는 스레드 수를 지정합니다.

#### 대역폭 제한

1. 프로세스 서버 역할을 하는 컴퓨터에서 Microsoft Azure 백업 MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure 백업에 대한 바로 가기가 바탕 화면 또는 C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.

	![대역폭 제한](./media/site-recovery-vmware-to-azure/throttle1.png)

3. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택하고 근무 시간 및 근무 외 시간에 대한 한도를 설정합니다. 유효 범위는 초당 512Kbps~102Mbp입니다.

	![대역폭 제한](./media/site-recovery-vmware-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet를 사용하여 제한을 설정할 수도 있습니다. 다음은 샘플입니다.

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle**은 제한이 필요 없다는 뜻입니다.


#### 네트워크 대역폭에 영향

1. 레지스트리에서 **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**으로 이동합니다.
	- 복제 디스크의 대역폭 트래픽에 영향을 주려면 **UploadThreadsPerVM** 값을 수정하거나 없는 경우 키를 만듭니다.
	- Azure에서 장애 복구(failback) 트래픽에 대한 대역폭에 영향을 주려면 **DownloadThreadsPerVM** 값을 수정합니다.
2. 기본값은 4입니다. "과도하게 프로비전된" 네트워크에서는 이러한 레지스트리 키를 기본값에서 변경해야 합니다. 최대값은 32입니다. 트래픽을 모니터링하여 값을 최적화합니다.

## 6단계: 응용 프로그램 복제

복제하려는 컴퓨터가 모바일 서비스 설치에 대해 준비가 되었는지 확인한 다음 복제를 사용하도록 설정합니다.

### 모바일 서비스 설치

가상 컴퓨터 및 물리적 서버에 대해 보안을 설정하는 첫 번째 단계는 모바일 서비스를 설치하는 것입니다. 다음과 같은 몇 가지 방법으로 이를 수행할 수 있습니다.

- **프로세스 서버 푸시**: 컴퓨터에서 복제를 사용하도록 설정할 때 프로세스 서버에서 모바일 서비스 구성 요소를 푸시 및 설치합니다. 컴퓨터가 이미 최신 버전의 구성 요소를 실행 중인 경우 푸시 설치가 발생하지 않습니다.
- **엔터프라이즈 푸시**: WSUS 또는 System Center Configuration Manager와 같은 엔터프라이즈 푸시 프로세스를 사용하여 구성 요소를 자동으로 설치합니다. 이렇게 하려면 먼저 구성 서버를 설정합니다.
- **수동 설치**: 복제하려는 각 컴퓨터에 구성 요소를 수동으로 설치합니다. 이렇게 하려면 먼저 구성 서버를 설정합니다.


#### Windows 컴퓨터에서 자동 푸시 준비

프로세스 서버에서 모바일 서비스를 자동으로 설치할 수 있도록 Windows 컴퓨터를 준비하는 방법은 다음과 같습니다.

1.  프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 만듭니다. 계정에 관리자 권한(로컬 또는 도메인)이 있어야 하고 푸시 설치에만 사용됩니다.

	>[AZURE.NOTE] 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 레지스터의 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System에서 값이 1인 LocalAccountTokenFilterPolicy DWORD 항목을 추가합니다. CLI 형식에서 레지스트리 항목을 추가하려면 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**을 입력합니다.

2.  보호하려는 컴퓨터의 Windows 방화벽에서 **방화벽을 통해 앱 또는 기능 허용**을 선택합니다. **파일 및 프린터 공유**와 **WMI(Windows Management Instrumentation)**를 사용하도록 설정합니다. 도메인에 속하는 컴퓨터의 경우 GPO를 사용하여 방화벽 설정을 구성할 수 있습니다.

	![방화벽 설정](./media/site-recovery-vmware-to-azure/mobility1.png)

2. 만든 계정을 추가합니다.

	- **cspsconfigtool**을 엽니다. 바탕 화면에 바로 가기로 사용 가능하며 [설치 위치]\\home\\svsystems\\bin 폴더에 있습니다.
	- **계정 관리** 탭에서 **계정 추가**를 클릭합니다.
	- 만든 계정을 추가합니다. 계정을 추가한 후 컴퓨터에 복제를 사용하도록 설정할 때 이 자격 증명을 제공해야 합니다.


#### Linux 서버에서 자동 푸시 준비

1.	보호하려는 Linux 컴퓨터가 [보호된 컴퓨터 필수 조건](#protected-machine-prerequisites)에 설명된 대로 지원되는지 확인합니다. Linux 컴퓨터와 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.

2.	프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 만듭니다. 계정은 원본 Linux 서버의 루트 사용자여야 하며 푸시 설치에만 사용됩니다.

	- **cspsconfigtool**을 엽니다. 바탕 화면에 바로 가기로 사용 가능하며 [설치 위치]\\home\\svsystems\\bin 폴더에 있습니다.
	- **계정 관리** 탭에서 **계정 추가**를 클릭합니다.
	- 만든 계정을 추가합니다. 계정을 추가한 후 컴퓨터에 복제를 사용하도록 설정할 때 이 자격 증명을 제공해야 합니다.

3.	원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 포함되어 있는지 확인합니다.
4.	복제할 컴퓨터에 최신 openssh, openssh-server, openssl 패키지를 설치합니다.
5.	포트 22에 SSH를 사용하고 실행 중인지 확인합니다.
6.	다음과 같이 sshd\_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다.

	- 루트로 로그인합니다.
	- /etc/ssh/sshd\_config 파일에서 **PasswordAuthentication**으로 시작하는 줄을 찾습니다.
	- 줄의 주석 처리를 제거하고 값을 **아니요**에서 **예**로 변경합니다.
	- **Subsystem**으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.

		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### 모바일 서비스 수동 설치

설치 관리자는 프로세스 서버의 **C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**에서 사용할 수 있습니다.

원본 운영 체제 | 모바일 서비스 설치 파일
--- | ---
Windows Server(64비트만 해당) | Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6(64비트만 해당) | Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3(64비트만 해당) | Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5(64비트만 해당) | Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz


#### Windows 서버에 수동으로 설치


1. 관련 설치 관리자를 다운로드 및 실행합니다.
2. **시작하기 전**에서 **모바일 서비스**를 선택합니다.

	![모바일 서비스](./media/site-recovery-vmware-to-azure/mobility3.png)

3. **구성 서버 세부 정보**에서 구성 서버의 IP 주소 및 통합 설치 프로그램을 실행했을 때 생성된 암호를 지정합니다. 구성 서버에서 다음을 실행하여 암호를 검색할 수 있습니다. **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –v**.

	![모바일 서비스](./media/site-recovery-vmware-to-azure/mobility6.png)

4. **설치 위치**에서 기본 설정을 그대로 두고 **다음**을 클릭하여 설치를 시작합니다.
5. **설치 진행률**에서 설치를 모니터링하고 메시지가 표시되면 컴퓨터를 다시 시작합니다. 서비스를 설치한 후 포털에서 상태가 업데이트되는 데 약 15분 정도 걸릴 수 있습니다.

또한 명령줄에서도 설치할 수 있습니다.

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <설치 디렉터리>] [/CSIP <등록할 CS의 IP 주소>] [/PassphraseFilePath <암호 파일 경로>] [/LogFilePath <로그 파일 경로>]

여기서,

- /Role: 필수. 모바일 서비스를 설치해야 하는지 여부를 지정합니다.
- /InstallLocation: 필수. 서비스를 설치할 위치를 지정합니다.
- /PassphraseFilePath: 필수. 구성 서버 암호입니다.
- /LogFilePath: 필수. 로그 설치 파일 위치입니다.

#### 모바일 서비스 수동 제거

제어판에서 프로그램 추가/제거를 사용하거나 명령줄을 사용하여 모바일 서비스를 제거할 수 있습니다.

명령줄을 사용하여 모바일 서비스를 제거하는 명령은 다음과 같습니다.

	MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### Linux 서버에 수동으로 설치:

1. 위 표를 기준으로 해당 tar 아카이브를 복제하려는 Linux 컴퓨터에 복사합니다.
2. 셸 프로그램을 열고 다음을 실행하여 압축된 tar 아카이브를 로컬 경로로 추출합니다: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. tar 아카이브의 내용을 추출한 로컬 디렉터리에 passphrase.txt 파일을 만듭니다. 이렇게 하려면 구성 서버의 C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase에서 암호를 복사하고 셸에서 *`echo <passphrase> >passphrase.txt`*를 실행하여 passphrase.txt에 저장합니다.
4. *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*를 실행하여 모바일 서비스를 설치합니다.
5. 구성 서버의 내부 IP 주소를 지정하고 포트 443이 선택되어 있는지 확인합니다. 서비스를 설치한 후 포털에서 상태가 업데이트되는 데 약 15분 정도 걸릴 수 있습니다.

**또한 명령줄에서도 설치할 수 있습니다**.

1. 구성 서버의 C:\\Program Files (x86)\\InMage Systems\\private\\connection에서 암호를 복사하고 구성 서버에 "passphrase.txt"로 저장합니다. 그런 후에 다음 명령을 실행합니다. 예제에서는 구성 서버 IP 주소가 104.40.75.37이며 HTTPS 포트는 443이어야 합니다.

운영 서버에 설치하려면

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

마스터 대상 서버에 설치하려면


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### 복제 활성화

#### 시작하기 전에

VMware 가상 컴퓨터를 복제하는 경우 다음 사항에 유의하세요.

- VMware VM은 15분마다 검색되고 검색 후 포털에 나타나려면 15분 이상 걸릴 수 있습니다. 마찬가지로 vCenter Server 또는 vSphere 호스트를 추가할 때 검색이 15분 이상 걸릴 수 있습니다.
- 가상 컴퓨터의 환경 변경 사항(예: VMware 도구 설치)이 포털에서 업데이트되는 데 15분 이상 소요될 수도 있습니다.
- **구성 서버** 블레이드에서 vCenter Server/vSphere 호스트에 대한 **마지막 연락** 필드에서 VMware VM이 마지막으로 검색된 시간을 확인할 수 있습니다.
- 예약된 검색을 기다리지 않고 복제에 대해 컴퓨터를 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침** 버튼을 클릭합니다.
- 복제를 사용하도록 설정하는 경우 컴퓨터가 준비되면 프로세스 서버가 자동으로 해당 컴퓨터에 모바일 서비스를 설치합니다.

#### 복제에서 디스크 제외

복제를 사용하도록 설정하면 기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다. 디스크를 제외하려는 경우 다음에 유의하세요.

- 이미 모바일 서비스가 설치된 디스크만 제외할 수 있습니다. 모바일 서비스는 복제가 사용하도록 설정된 후 푸시 메커니즘을 사용해야만 설치되기 때문에 [모바일 서비스를 수동으로 설치](#install-the-mobility-service-manually)해야 합니다.
- 기본 디스크만 복제에서 제외할 수 있습니다. OS 또는 동적 디스크를 제외할 수 없습니다.
- 복제를 사용하도록 설정한 후 복제에 대해 디스크를 추가 또는 제거할 수 없습니다. 디스크를 추가하거나 제외하려는 경우 컴퓨터에 대한 보호를 사용하지 않도록 설정한 다음 다시 사용하도록 설정해야 합니다.
- 응용 프로그램 작동에 필요한 디스크를 제외하면 Azure로 장애 조치(failover) 후 복제된 응용 프로그램이 실행될 수 있도록 디스크를 Azure에 수동으로 만들어야 합니다. 또는 Azure 자동화를 복구 계획에 통합하여 컴퓨터의 장애 조치(failover) 동안 디스크를 만들 수 있습니다.
- Azure에서 수동으로 만드는 디스크는 장애 복구(failback)됩니다. 예를 들어 3개의 디스크를 장애 조치(failover)하고 Azure에서 직접 2개를 만든 경우 5개 모두 장애 복구(failback)됩니다. 수동으로 만든 디스크는 장애 복구(failback)에서 제외할 수 없습니다.

**이제 다음과 같이 복제를 활성화합니다.**

1. **2단계: 응용 프로그램 복제** > **소스**를 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.
2. **소스** 블레이드 > **소스**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식** 에서 **가상 컴퓨터** 또는 **실제 컴퓨터** 를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다. 이 설정은 물리적 컴퓨터를 복제하는 경우에는 관련이 없습니다.
5. 프로세스 서버를 선택합니다. 추가 프로세스 서버를 만들지 않은 경우 이 프로세스 서버가 구성 서버의 이름이 됩니다. 그런 후 **OK**를 클릭합니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. **대상**에서 자격 증명 모음 구독을 선택하고 **장애 조치(failover) 후 배포 모델**에서 장애 조치(failover) 후 Azure에서 사용하려는 모델(클래식 또는 리소스 관리)을 선택합니다.
7. 데이터 복제에 사용할 Azure 저장소 계정을 선택합니다. 다음 사항에 유의하세요.

	- 프리미엄 또는 표준 저장소 계정을 선택할 수 있습니다. 프리미엄 계정을 선택하는 경우 지속적인 복제 로그를 위한 추가 표준 저장소 계정을 지정해야 합니다. 계정은 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다.
	- 갖고 있는 저장소 계정 말고 다른 저장소 계정을 사용하려면 [새로 만듭니다](#set-up-an-azure-storage-account). ARM 모델을 사용하여 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 저장소 계정을 만들려면 [Azure 포털에서](../storage/storage-create-storage-account-classic-portal.md) 만들면 됩니다.

8. 장애 조치(Failover) 후 Azure VM이 회전하면 연결될 Azure 네트워크 및 서브넷을 선택합니다 네트워크는 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다. 보호를 위해 선택한 모든 컴퓨터에 네트워크 설정을 적용하려면 **선택한 컴퓨터에 대해 지금 구성**을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 네트워크가 없는 경우 [만들어야](#set-up-an-azure-network) 합니다. ARM 모델을 사용하여 네트워크를 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 [Azure 포털](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 네트워크를 만들 수 있습니다. 해당하는 경우 서브넷을 선택합니다. 그런 후 **OK**를 클릭합니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. **가상 컴퓨터** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. **속성** > **속성 구성**에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다. 기본적으로 모든 디스크가 복제됩니다. **모든 디스크**를 클릭하고 복제하지 않으려는 디스크를 지웁니다. 그런 후 **OK**를 클릭합니다. 나중에 추가 속성을 설정할 수 있습니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다. **설정** > **복제 정책** > 정책 이름 > **설정 편집**에서 복제 정책을 수정할 수 있습니다. 정책에 적용하는 변경 내용은 복제 및 새 컴퓨터에 적용됩니다.

12. 컴퓨터를 복제 그룹으로 수집하려는 경우 **다중 VM 일관성**을 사용하도록 설정하고 그룹에 대한 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 다음 사항에 유의하세요.

	- 복제 그룹의 컴퓨터는 함께 복제되고 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 갖습니다.
	- 워크로드를 미러링하도록 VM 및 물리적 서버를 함께 수집하는 것이 좋습니다. 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크로드를 실행하고 일관성이 필요한 경우에만 사용해야 합니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. **복제 사용**을 클릭합니다. **설정** > **작업** > **사이트 복구 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

> [AZURE.NOTE] 컴퓨터가 푸시 설치에 대해 준비된 경우 보호를 사용하도록 설정하면 모바일 서비스 구성 요소가 설치됩니다. 컴퓨터에 구성 요소가 설치된 후 보호 작업이 시작되고 실패합니다. 실패 후 각 컴퓨터를 수동으로 다시 시작해야 합니다. 다시 시작한 후 보호 작업이 다시 시작되고 초기 복제가 발생합니다.

### VM 속성 보기 및 관리

원본 컴퓨터의 속성을 확인하는 것이 좋습니다. Azure VM 이름이 [Azure 가상 컴퓨터 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements)에 맞아야 합니다.

1. **설정** > **복제된 항목**을 클릭하고 컴퓨터를 선택합니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.

2. **속성**에서 해당 VM에 대한 복제 및 장애 조치(failover) 정보를 볼 수 있습니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. **계산 및 네트워크** > **계산 속성**에서 Azure VM 이름 및 대상 크기를 지정할 수 있습니다. 필요한 경우 Azure 요구 사항을 준수하도록 이름을 수정합니다. 또한 대상 네트워크, 서브넷 및 Azure VM에 할당될 IP 주소에 대한 정보를 보고 추가할 수 있습니다. 다음 사항에 유의하세요.

	- 대상 IP 주소를 설정할 수 있습니다. 주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다. 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다. 주소를 테스트 장애 조치(Failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(Failover)에 동일한 대상 IP 주소를 사용해도 됩니다.
	- 네트워크 어댑터 수는 다음과 같이 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다.

		- 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일해야 합니다.
		- 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
		- 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는 1개의 어댑터만 있어야 합니다.
	- VM에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.

	![복제 활성화](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. **디스크**에서 복제될 VM의 운영 체제 및 데이터 디스크를 볼 수 있습니다.


## 7단계: 배포 테스트

배포를 테스트하기 위해 단일 가상 컴퓨터에 대한 테스트 장애 조치(failover)를 실행하거나 하나 이상의 가상 컴퓨터를 포함한 복구 계획을 실행할 수 있습니다.


### 장애 조치(Failover) 준비

- 테스트 장애 조치(Failover)를 실행하려면 Azure 프로덕션 네트워크에서 격리된 새 Azure 네트워크를 만드는 것이 좋습니다(Azure에서 새 네트워크를 만들 때의 기본 동작). 테스트 장애 조치(failover)에 대해 [자세히 알아보세요](site-recovery-failover.md#run-a-test-failover).
- Azure에 장애 조치(failover)를 수행할 때 최상의 성능을 얻으려면 보호된 컴퓨터에 Azure 에이전트를 설치해야 합니다. 부팅 속도가 빨라지고 문제 해결에 도움이 됩니다. [Linux](https://github.com/Azure/WALinuxAgent) 또는 [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) 에이전트를 설치합니다.
- 배포를 완전하게 테스트하려면 복제된 컴퓨터가 예상대로 작동할 수 있는 인프라가 필요합니다. Active Directory 및 DNS를 테스트하려면 DNS를 사용하여 도메인 컨트롤러로 가상 컴퓨터를 만들고 Azure Site Recovery를 사용하여 이 가상 컴퓨터를 Azure에 복제합니다. [Active Directory에 대한 테스트 장애 조치(failover) 고려 사항](site-recovery-active-directory.md#considerations-for-test-failover)에 대해 자세히 알아보세요.
- 구성 서버가 실행되고 있는지 확인합니다. 그렇지 않으면 장애 조치(Failover)가 실패합니다.
- 복제에서 디스크를 제외했다면 응용 프로그램이 예상대로 실행되도록 장애 조치(failover) 후 Azure에 해당 디스크를 수동으로 만들어야 할 수도 있습니다.
- 테스트 장애 조치(Failover) 대신 계획되지 않은 장애 조치(Failover)를 실행하려면 다음 사항에 주의하세요.

	- 가능한 경우 계획되지 않은 장애 조치를 실행하기 전에 주 컴퓨터를 종료해야 합니다. 이렇게 하면 원본 및 복제본 컴퓨터가 동시에 실행되지 않아도 됩니다. VMware VM을 복제하는 경우 Site Recovery에서 최대한 원본 컴퓨터를 종료하도록 지정할 수 있습니다. 기본 사이트의 상태에 따라 작동하거나 작동하지 않을 수 있습니다. 물리적 서버를 복제하는 경우 사이트 복구에서 이 옵션을 제공하지 않습니다.
	- 계획되지 않은 장애 조치를 실행하면 주 컴퓨터에서 데이터 복제를 중지하므로 계획되지 않은 장애 조치가 시작된 후 모든 데이터 델타가 전송되지 않습니다. 또한 복구 계획에서 계획되지 않은 장애 조치(Failover)를 실행하면 오류가 발생하더라도 완료될 때까지 실행됩니다.

### 장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(Failover) 후 RDP를 사용하여 Azure VM에 연결하려면 다음을 수행해야 합니다.

**장애 조치(failover) 전에 온-프레미스 컴퓨터에서**:

- 인터넷 지원 RDP를 통해 액세스하려면 TCP 및 UDP 규칙을 **공용**에 추가하고, 모든 프로필의 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 RDP를 허용해야 합니다.
- 사이트 간 연결을 통해 액세스하려면 컴퓨터에서 RDP를 활성화하고, **도메인** 및 **개인** 네트워크의 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 RDP를 허용해야 합니다.
- 온-프레미스 컴퓨터에 [Azure VM 에이전트](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 설치합니다.
- 서비스를 자동으로 푸시하는 프로세스 서버를 사용하는 대신 컴퓨터에 [모바일 서비스를 수동으로 설치](#install-the-mobility-service-manually)합니다. 이는 컴퓨터가 복제에 대해 설정된 후에만 푸시 설치가 발생하기 때문입니다.
- 운영 체제의 SAN 정책이 OnlineAll로 설정되어 있는지 확인합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135)
- 장애 조치(Failover)를 실행하기 전에 IPSec 서비스를 끕니다.

**장애 조치(failover) 후 Azure VM에서**:

- RDP 프로토콜(포트 3389)에 대한 공용 끝점을 추가하고 로그인 자격 증명을 지정합니다.
- 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없는지 확인합니다.
- 연결을 시도합니다. 연결할 수 없으면 VM이 실행 중인지 확인합니다. 자세한 문제 해결 팁은 이 [문서](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)를 참조하세요.


ssh(보안 셸 클라이언트)를 사용하여 장애 조치(Failover) 후 Linux를 실행하는 Azure VM에 액세스하려면 다음 작업을 수행합니다.

**장애 조치(failover) 전에 온-프레미스 컴퓨터에서**:

- Azure VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.
- 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.

**장애 조치(failover) 후 Azure VM에서**:

- 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.
- SSH 포트(기본적으로 TCP 포트 22)로 들어오는 연결을 허용하려면 공용 끝점을 만들어야 합니다.
- VPN 연결(Express 경로 또는 사이트 간 VPN)을 통해 VM에 액세스하는 경우 클라이언트를 사용하여 SSH를 통해 VM에 직접 연결할 수 있습니다.

**장애 조치 후에 Azure Windows/Linux VM에서**:

네트워크 보안 그룹이 컴퓨터가 속한 가상 컴퓨터 및 서브넷과 연결된 경우 네트워크 보안 그룹에 HTTP/HTTPS를 허용하는 아웃바운드 규칙이 있는지 확인합니다. 또한 가상 컴퓨터를 장애 조치한 네트워크의 DNS가 올바르게 구성되어 있는지 확인합니다. 그렇지 않으면 장애 조치는 'PreFailoverWorkflow 작업 WaitForScriptExecutionTask 시간이 초과되었습니다.' 오류로 인해 시간이 초과되었습니다. 자세히 이해하려면 [모니터링 및 문제 해결 가이드](site-recovery-monitoring-and-troubleshooting.md#recovery)에서 복구에 대한 섹션을 참조합니다.

## 테스트 장애 조치(Failover) 실행

1. 단일 컴퓨터를 장애 조치(failover)하려면 **설정** > **복제된 항목**에서 VM > **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.

	![테스트 장애 조치(Failover)](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 버튼으로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).

3. **테스트 장애 조치(Failover)**에서 장애 조치(Failover)가 발생한 후에 Azure VM이 연결될 Azure 네트워크를 선택합니다.
4. **확인** 을 클릭하여 장애 조치(Failover)를 시작합니다. VM을 클릭하여 속성을 열거나 자격 증명 모음 이름 > **설정** > **작업** > **사이트 복구 작업** 의 **테스트 장애 조치(failover)** 에서 진행률을 추적할 수 있습니다.
5. 장애 조치(failover)가 **테스트 완료** 상태에 도달하면 다음 작업을 수행합니다.

	1. Azure 포털에서 복제본 가상 컴퓨터를 봅니다. 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
	2. 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.
	3. **테스트 완료**를 클릭하여 테스트를 마칩니다.

		![테스트 장애 조치(Failover)](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
	5. **테스트 장애 조치가 완료됨**을 클릭하면 테스트 환경이 자동으로 정리됩니다. 이 작업이 완료되면 테스트 장애 조치(failover)가 **완료** 상태를 표시합니다.
	6.  이 단계에서는 테스트 장애 조치(Failover) 중에 자동으로 생성된 모든 요소 또는 VM이 삭제됩니다. 테스트 장애 조치(Failover)를 위해 만든 모든 추가 요소는 삭제되지 않습니다.

	> [AZURE.NOTE] 테스트 장애 조치(Failover)가 2주 이상 지속되면 강제로 완료됩니다.


6. 또한 장애 조치(failover)가 완료된 후 Azure 포털 > **가상 컴퓨터**에 Azure 컴퓨터 복제본이 나타나는 것을 확인할 수 있습니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인해야 합니다.
7. [장애 조치(failover) 후 연결을 준비](#prepare-to-connect-to-azure-vms-after-failover)하는 경우 Azure VM에 연결할 수 있어야 합니다.

## 배포 모니터링

다음과 같이 Site Recovery 배포의 구성 설정 및 상태를 모니터링할 수 있습니다.

1. 자격 증명 모음 이름을 클릭하여 **Essentials** 대시보드에 액세스합니다. 이 대시보드에서 Site Recovery 작업, 복제 상태, 복구 계획, 서버 상태 및 이벤트를 모니터링할 수 있습니다. 다른 Site Recovery 및 백업 자격 증명 모음의 상태를 포함하여 가장 유용한 타일과 레이아웃을 표시하도록 Essentials를 사용자 지정할 수 있습니다.
<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. **상태** 타일에서 문제가 있는 사이트 서버(VMM 또는 구성 서버)와 지난 24시간 동안 사이트 복구에 의해 발생한 이벤트를 모니터링할 수 있습니다.
3. **복제된 항목**, **복구 계획** 및 **사이트 복구 작업** 타일에서 복제를 관리 및 모니터링할 수 있습니다. **설정** -> **작업** -> **사이트 복구 작업**에서 작업으로 드릴다운할 수 있습니다.


## 추가 프로세스 서버 배포

200대가 넘는 원본 컴퓨터로 배포 규모를 확장해야 하고 총 이탈률이 2TB를 초과하는 경우 트래픽 볼륨을 처리할 추가 프로세스가 필요합니다.

[프로세스 서버에 대한 크기 권장 사항](#size-recommendations-for-the-process-server)을 확인한 후 다음 지침에 따라 프로세스 서버를 설정합니다. 서버를 설정한 후 이를 사용하도록 원본 컴퓨터를 마이그레이션할 수 있습니다.

### 추가 프로세스 서버 설치

1. **설정** > **사이트 복구 서버**에서 구성 서버 > **프로세스 서버**를 클릭합니다.

	![프로세스 서버 추가](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. **서버 형식**에서 **프로세스 서버(온-프레미스)**를 클릭합니다.

	![프로세스 서버 추가](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Site Recovery 통합 설치 프로그램 파일을 다운로드하고 실행하여 프로세스 서버를 설치하고 자격 증명 모음에 등록합니다.
4. **시작하기 전**에서 **배포 규모 확장을 위해 추가 프로세스 서버 추가**를 선택합니다.
5. 구성 서버를 [설정](#step-2-set-up-the-source-environment)했을 때 수행한 것과 동일한 방식으로 마법사를 완료합니다.

	![프로세스 서버 추가](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. **구성 서버 세부 정보**에서 구성 서버의 IP 주소 및 암호를 지정합니다. 암호를 가져오려면 구성 서버에서 **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n**을 실행합니다.

	![프로세스 서버 추가](./media/site-recovery-vmware-to-azure/add-ps2.png)

### 새 프로세스 서버를 사용하도록 컴퓨터 마이그레이션

1. **설정** > **사이트 복구 서버**에서 구성 서버를 클릭한 다음 **프로세스 서버**를 확장합니다.

	![프로세스 서버 업데이트](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. 현재 사용 중인 프로세스 서버를 마우스 오른쪽 버튼으로 클릭하고 **스위치**를 클릭합니다.

	![프로세스 서버 업데이트](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. **대상 프로세스 서버 선택**에서 사용할 새 프로세스 서버를 선택한 다음 새 프로세스 서버가 처리할 가상 컴퓨터를 선택합니다. 서버에 대한 정보를 얻으려면 정보 아이콘을 클릭합니다. 부하 결정을 할 때 도움이 되도록 선택된 각 가상 컴퓨터를 새 프로세스 서버로 복제하는 데 필요한 평균 공간이 표시됩니다. 새 프로세스 서버로 복제를 시작하려면 확인 표시를 클릭합니다.

## VMware 계정 권한

프로세스 서버는 vCenter Server에서 VM을 자동으로 검색할 수 있습니다. 자동 검색을 수행하려면 사이트 복구에서 VMware Server에 액세스할 수 있도록 [역할(Azure\_Site\_Recovery)을 정의](#prepare-an-account-for-automatic-discovery)해야 합니다. VMware 컴퓨터를 Azure로 마이그레이션해야 하고 Azure에서 장애 복구(failback)가 필요하지 않은 경우 읽기 전용 역할을 정의하는 것만으로도 충분합니다. 필요한 역할 권한이 다음 표에 요약되어 있습니다.

**역할** | **세부 정보** | **권한**
--- | --- | ---
Azure\_Site\_Recovery 역할 | VMware VM 검색 |v-Center Server에 대해 이러한 권한 할당:<br/><br/>데이터 저장소->공간 할당, 데이터 저장소 찾아보기, 하위 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트<br/><br/>네트워크-> 네트워크 할당<br/><br/>리소스 -> 가상 컴퓨터를 리소스 풀에 할당, 전원이 꺼진 가상 컴퓨터 마이그레이션, 전원이 켜진 가상 컴퓨터 마이그레이션<br/><br/>작업 -> 만들기 작업, 업데이트 작업<br/><br/>가상 컴퓨터 -> 구성<br/><br/>가상 컴퓨터 -> 상호 작용 -> 질문 답변, 장치 연결., CD 미디어 구성, 플로피 미디어 구성, 전원 꺼짐, 전원 켜짐, VMware 도구 설치<br/><br/>가상 컴퓨터 -> 인벤토리 -> 만들기, 등록, 등록 취소<br/><br/>가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/>가상 컴퓨터 -> 스냅숏 -> 스냅숏 제거
vCenter 사용자 역할 | VMware VM 검색/원본 VM을 종료하지 않고 장애 조치 | v-Center Server에 대해 이러한 권한 할당:<br/><br/>데이터 센터 개체 –> 자식 개체에 전파, 역할=읽기 전용 <br/><br/>사용자는 데이터 센터 수준에서 할당되므로 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다. 액세스를 제한하려는 경우 **자식 개체에 전파**를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
vCenter 사용자 역할 | 장애 조치 및 장애 복구 | v-Center Server에 대해 이러한 권한 할당:<br/><br/>데이터 센터 개체 – 자식 개체에 전파, 역할=Azure\_Site\_Recovery<br/><br/>사용자는 데이터 센터 수준에서 할당되므로 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다. 액세스를 제한하려는 경우 **자식 개체에 전파**를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.  
## 다음 단계

- 다양한 형식의 장애 조치(failover)에 대해 [자세히 알아봅니다](site-recovery-failover.md).
- Azure에서 실행 중인 장애 조치(failover)된 컴퓨터를 온-프레미스 환경으로 [장애 복구(failback)하는 방법에 대해 자세히 알아봅니다](site-recovery-failback-azure-to-vmware.md).

## 타사 소프트웨어 통지 및 정보

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0921_2016-->