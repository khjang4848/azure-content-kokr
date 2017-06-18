<properties
	pageTitle="사이트 복구로 온-프레미스 Hyper-V 가상 컴퓨터와 Azure(VMM 없음) 간 복제 | Microsoft Azure"
	description="이 문서에서는 컴퓨터가 VMM 클라우드에서 관리되지 않는 경우 Azure 사이트 복구를 사용하여 Azure에 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/19/2016"
	ms.author="raynew"/>


# Azure Site Recovery로 온-프레미스 Hyper-V 가상 컴퓨터와 Azure(VMM 없음) 간 복제

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - ARM](site-recovery-deploy-with-powershell-resource-manager.md)
- [클래식 포털](site-recovery-hyper-v-site-to-azure-classic.md)

이 문서를 참고하여 Hyper-V 호스트가 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되지 않는 경우 Hyper-V 가상 컴퓨터를 Azure에 복제하도록 사이트 복구를 배포하는 방법을 알아봅니다.

이 문서는 복제 설정을 구성하고 가상 컴퓨터에 대한 보호를 설정하도록 돕는 배포 필수 조건을 요약합니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

이 문서를 참고한 후에 맨 아래 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## 개요

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 비즈니스 연속성 및 재해 복구(BCDR) 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 하고 워크로드를 지속적으로 가용 상태로 유지하는 솔루션에 초점을 맞추고 있습니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 사이트로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다.

사이트 복구는 다양한 시나리오에서 사용할 수 있으며 많은 워크로드를 보호할 수 있습니다. [Azure Site Recovery란?](site-recovery-overview.md)에서 자세한 내용을 확인해 보세요.


## Azure 필수 조건

- [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
- 복제된 데이터를 저장하려면 Azure 저장소 계정이 있어야 합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. [Azure 저장소에 대해 자세히 알아보세요](../storage/storage-introduction.md). 여러 리소스 그룹에 [새 Azure 포털](../storage/storage-create-storage-account.md)을 사용하여 만든 저장소 계정의 이동을 지원하지 않습니다.
- 기본 사이트에서 장애 조치(failover) 시 Azure 가상 컴퓨터가 네트워크에 연결되도록 하기 위해서는 Azure 가상 네트워크가 필요합니다.

## Hyper-V 필수 조건

- 원본 온-프레미스 사이트에서 Hyper-V 역할이 설치된 Windows Server 2012 R2를 실행하는 하나 이상의 서버가 있어야 합니다. 이 서버는 다음 조건을 충족해야 합니다.
- 하나 이상의 가상 컴퓨터를 포함해야 합니다.
- 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.
- 기술 자료 [2961977](https://support.microsoft.com/ko-KR/kb/2961977 "KB2961977")에 설명된 수정 사항을 실행 중이어야 합니다.

## 가상 컴퓨터 필수 조건

보호할 가상 컴퓨터는 [Azure 가상 컴퓨터 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements)에 맞아야 합니다.

## 공급자 및 에이전트 필수 조건

Azure Site Recovery 배포의 일환으로 Hyper-V 서버마다 Azure Site Recovery 공급자 및 Azure 복구 서비스 에이전트를 설치합니다. 다음 사항에 유의하세요.

- 항상 최신 버전의 공급자 및 에이전트를 실행하는 것이 좋습니다. 이에 대한 내용은 사이트 복구 포털에서 제공됩니다.
- 자격 증명 모음의 모든 Hyper-V 서버는 동일한 버전의 공급자 및 에이전트를 포함해야 합니다.
- 서버에서 실행되는 공급자는 인터넷을 통해 사이트 복구에 연결됩니다. 현재 Hyper-V 서버에 구성된 프록시 설정 또는 공급자 설치 중에 구성하는 사용자 지정 프록시 설정을 사용하여 프록시 없이 이를 수행할 수 있습니다. 사용하려는 프록시 서버가 Azure 연결을 위해 이러한 URL에 액세스할 수 있는지 확인해야 합니다.
	- *.hypervrecoverymanager.windowsazure.com
	- *.accesscontrol.windows.net
	- *.backup.windowsazure.com
	- *.blob.core.windows.net
	- *.store.core.windows.net
	
- 또한 [Azure 데이터센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 및 HTTPS(443) 프로토콜에 설명된 IP 주소를 허용합니다. 사용하려는 Azure 지역 및 미국 서부의 IP 범위를 허용해야 합니다.


이 그림에서는 조정 및 복제를 위해 사이트 복구에서 사용되는 다양한 통신 채널 및 포트를 보여줍니다.

![B2A 토폴로지](./media/site-recovery-hyper-v-site-to-azure-classic/b2a-topology.png)


## 1단계: 자격 증명 모음 만들기

1. [관리 포털](https://portal.azure.com)에 로그인합니다.


2. **데이터 서비스** > **복구 서비스**를 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.


3. **새로 만들기** > **빠른 생성**을 클릭합니다.

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 하위 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.

6. **자격 증명 모음 만들기**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-hyper-v-site-to-azure-classic/vault.png)

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 **활성**으로 나열됩니다.


## 2단계: Hyper-V 사이트 만들기

1. 복구 서비스 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![빠른 시작](./media/site-recovery-hyper-v-site-to-azure-classic/quick-start-icon.png)

2. 드롭다운 목록에서 **온-프레미스 Hyper-V 사이트와 Azure 간**을 선택합니다.

	![Hyper-V 사이트 시나리오](./media/site-recovery-hyper-v-site-to-azure-classic/select-scenario.png)

3. **Create a Hyper-V Site(Hyper-V 사이트 만들기)**에서 **Create Hyper-V site((Hyper-V 사이트 만들기)**를 클릭합니다. 사이트 이름을 지정하고 저장합니다.

	![Hyper-V 사이트](./media/site-recovery-hyper-v-site-to-azure-classic/create-site.png)


## 3단계: 공급자 및 에이전트 설치
보호할 VM이 있는 Hyper-V 서버마다 공급자 및 에이전트를 설치합니다.

Hyper-V 클러스터를 설치하는 경우 장애 조치 클러스터의 각 노드에서 5~11단계를 수행합니다. 모든 노드를 등록하고 보호 기능을 설정한 후 가상 컴퓨터는 클러스터 내의 노드 간에 마이그레이션할 경우에도 보호됩니다.

1. **Hyper-V 서버 준비**에서 **등록 키 다운로드** 파일을 클릭합니다.
2. **등록 키 다운로드** 페이지에서 사이트 옆의 **다운로드**를 클릭합니다. Hyper-V 서버에서 쉽게 액세스할 수 있는 안전한 위치로 키를 다운로드합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

	![등록 키](./media/site-recovery-hyper-v-site-to-azure-classic/download-key.png)

4. **공급자 다운로드**를 클릭하여 최신 버전을 가져옵니다.
5. 자격 증명 모음에 등록하려는 각 Hyper-V 서버에서 파일을 실행합니다. 이 파일은 다음 두 구성 요소를 설치합니다.
	- **Azure Site Recovery 공급자** - Hyper-V 서버와 Azure Site Recovery 포털 간의 통신 및 오케스트레이션을 처리합니다.
	- **Azure 복구 서비스 에이전트** - 원본 Hyper-V 서버에 실행 중인 가상 컴퓨터와 Azure 저장소 간의 데이터 전송을 처리합니다.
6. **Microsoft 업데이트**에서 업데이트를 선택할 수 있습니다. 이 설정을 사용하도록 설정하면 공급자와 에이전트 업데이트가 Microsoft 업데이트 정책에 따라 설치됩니다.

	![Microsoft 업데이트](./media/site-recovery-hyper-v-site-to-azure-classic/provider1.png)

7. **설치**에 Hyper-V 서버에서 공급자 및 에이전트를 설치할 위치를 지정합니다.

	![설치 위치](./media/site-recovery-hyper-v-site-to-azure-classic/provider2.png)

8. 설치가 완료되면 설정을 계속 진행하여 자격 증명 모음에 서버를 등록합니다.

9. **자격 증명 모음 설정** 페이지에서 **찾아보기**를 클릭하고 키 파일을 선택합니다. Azure Site Recovery 구독, 자격 증명 모음 이름 및 Hyper-V 서버가 속한 Hyper-V 사이트를 지정합니다.

	![서버 등록](./media/site-recovery-hyper-v-site-to-azure-classic/provider8.PNG)

10. **인터넷 연결** 페이지에서 공급자가 Azure Site Recovery에 연결하는 방법을 지정합니다. 서버에 구성되어 있는 기본 인터넷 연결 설정을 사용하려면 **기본 시스템 프록시 설정 사용**을 선택합니다. 값을 지정 하지 않으면 기본 설정이 사용됩니다.

	![인터넷 설정](./media/site-recovery-hyper-v-site-to-azure-classic/provider7.PNG)

11. 서버를 자격 증명 모음에 등록하는 등록이 시작됩니다.

	![서버 등록](./media/site-recovery-hyper-v-site-to-azure-classic/provider15.PNG)

11. 등록이 완료되면 Azure Site Recovery에서 Hyper-V 서버의 메타데이터를 가져오고 서버가 자격 증명 모음의 **서버** 페이지에 있는 **Hyper-V 사이트** 탭에 표시됩니다.


### 명령줄에서 공급자 설치

또한 명령줄에서 Azure Site Recovery 공급자를 설치할 수도 있습니다. Windows Server Core 2012 R2를 실행하는 컴퓨터에 공급자를 설치하려면 이 방법을 사용해야 합니다. 다음과 같이 명령줄에서 실행합니다.

1. 공급자 설치 파일 및 등록 키를 폴더로 다운로드합니다. 예: C:\\ASR.
2. 관리자로 명령 프롬프트 창을 실행하고 다음을 입력합니다.

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

3. 다음을 실행하여 공급자를 설치합니다.

		C:\ASR> setupdr.exe /i

4. 등록을 완료하려면 다음을 실행합니다.

    	CD C:\Program Files\Microsoft Azure Site Recovery Provider
    	C:\Program Files\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

여기서 매개 변수는 다음과 같습니다.

- **/Credentials**: 다운로드한 등록 키의 위치를 지정합니다.
- **/FriendlyName**: Hyper-V 호스트 서버를 식별하는 이름을 지정합니다. 이 이름이 포털에 나타납니다.
- **/EncryptionEnabled**: 선택 사항입니다. Azure(암호화 미사용)에서 복제본 가상 컴퓨터를 암호화할 것인지 여부를 지정합니다.
- **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: 선택 사항입니다. 사용자 지정 프록시를 사용하거나 기존 프록시에 인증이 필요한 경우 프록시 매개 변수를 지정합니다.


## 4단계: Azure 저장소 계정 만들기 

1. **리소스 준비**에서 **저장소 계정 만들기**를 선택하여 Azure 저장소 계정이 없는 경우 만듭니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다.

	![저장소 계정 만들기](./media/site-recovery-hyper-v-site-to-azure-classic/create-resources.png)

>[AZURE.NOTE] 1. 여러 리소스 그룹에 [새 Azure 포털](../storage/storage-create-storage-account.md)을 사용하여 만든 저장소 계정의 이동을 지원하지 않습니다. 2. [저장소 계정 마이그레이션](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 저장소 계정에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

## 5단계: 보호 그룹 만들기 및 구성

보호 그룹은 동일한 보호 설정을 사용하여 보호하려는 가상 컴퓨터의 논리적 그룹화입니다. 보호 설정을 보호 그룹에 적용하면 이러한 설정은 그룹에 추가한 가상 컴퓨터 모두에 적용됩니다.

1. **보호 그룹 만들기 및 구성**에서 **보호 그룹 만들기**를 클릭합니다. 필수 조건이 갖추어지지 않은 경우 메시지가 표시되며 **세부 정보 보기**를 클릭하여 자세한 내용을 확인할 수 있습니다.

2. **보호 그룹** 탭에서 보호 그룹을 추가합니다. 이름, 소스 Hyper-V 사이트, 대상 **Azure**, Azure Site Recovery 구독 이름 및 Azure 저장소 계정을 지정합니다.

	![보호 그룹](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group.png)


2. **복제 설정**에서 **복사 빈도**를 설정하여 원본과 대상 간에 데이터 델타가 동기화되는 빈도를 지정합니다. 30초, 5분 또는 15분으로 설정할 수 있습니다.
3. **복구 지점 보존 기간**에서 복구 기록을 보관하는 시간을 지정합니다.
4. **응용프로그램에 일관된 스냅숏의 빈도**에서 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용프로그램이 일관된 상태가 되도록 할지 지정할 수 있습니다. 기본적으로 수행되지는 않습니다. 값을 설정할 경우 구성할 추가 복구 지점 수보다 적은지 확인하세요. 이는 가상 컴퓨터가 Windows 운영 체제를 실행하는 경우에만 지원됩니다.
5. **초기 복제 시작 시간**에서 보호 그룹에 가상 컴퓨터의 초기 복제를 Azure로 보내도록 지정합니다.

	![보호 그룹](./media/site-recovery-hyper-v-site-to-azure-classic/protection-group2.png)


## 6단계: 가상 컴퓨터 보호 사용


가상 컴퓨터를 보호 그룹에 추가하여 보호를 사용합니다.

>[AZURE.NOTE] 고정 IP 주소를 사용하여 Linux를 실행하는 VM 보호는 지원되지 않습니다.

1. 보호 그룹의 **컴퓨터** 탭에서 **Virtual Machine를 보호 그룹에 추가하여 보호합니다.**를 클릭합니다.
2. **가상 컴퓨터 보호 사용** 페이지에서 보호할 가상 컴퓨터를 선택합니다.

	![가상 컴퓨터 보호 사용](./media/site-recovery-hyper-v-site-to-azure-classic/add-vm.png)

	보호 사용 작업이 시작됩니다. **작업** 탭에서 진행률을 추적할 수 있습니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다.
3. 보호가 설정된 후 다음을 수행할 수 있습니다.

	- **보호된 항목** > **보호 그룹** > *protectiongroup\_name* > **가상 컴퓨터**에서 보호된 가상 컴퓨터를 볼 수 있습니다. **속성** 탭에서 컴퓨터 세부 정보를 자세히 다룰 수 있습니다.
	- **보호된 항목** > **보호 그룹** > *protectiongroup\_name* > **가상 컴퓨터** *virtual\_machine\_name* > **구성**에서 가상 컴퓨터의 장애 조치 속성을 구성합니다. 다음을 구성할 수 있습니다.
		- **이름**: Azure의 가상 컴퓨터 이름입니다.
		- **크기**: 장애 조치된 가상 컴퓨터의 대상 크기입니다.

		![가상 컴퓨터 속성 구성](./media/site-recovery-hyper-v-site-to-azure-classic/vm-properties.png)
	- 다음을 포함하여 *보호된 항목** > **보호 그룹** > *protectiongroup\_name* > **Virtual Machine* virtual\_machine\_name* > **구성**에서 추가적인 Virtual Machine의 설정을 구성합니다.

		- **네트워크 어댑터**: 네트워크 어댑터 수가 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다. [가상 컴퓨터 크기 사양](../virtual-machines/virtual-machines-linux-sizes.md#size-tables)에서 가상 컴퓨터 크기에 의해 지원되는 NIC의 수를 확인하세요.


			가상 컴퓨터의 크기를 수정하고 설정을 저장하면 다음에 **구성** 페이지를 열 때 네트워크 어댑터의 수가 변경됩니다. 대상 가상 컴퓨터의 네트워크 어댑터 수는 원본 가상 컴퓨터의 네트워크 어댑터 수 이상이어야 하며 선택한 가상 머신 크기에서 지원하는 네트워크 어댑터 수 이하여야 합니다. 아래 설명을 참조하세요.


			- 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일해야 합니다.
			- 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
			- 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는 1개의 어댑터만 있어야 합니다.
		- **Azure 네트워크**: 가상 컴퓨터가 장애 조치되는 네트워크를 지정합니다. 가상 컴퓨터에 여러 네트워크가 있는 경우 모든 어댑터는 동일한 Azure 네트워크에 연결되어야 합니다.
		- **서브넷** 가상 컴퓨터에서 각 네트워크 어댑터에 대해 장애 조치 후 컴퓨터가 연결되는 Azure 네트워크의 서브넷을 선택합니다.
		- **대상 IP 주소**: 원본 가상 컴퓨터의 네트워크 어댑터를 고정 IP 주소를 사용하도록 구성하는 경우, 대상 가상 컴퓨터의 IP 주소를 지정하여 장애 조치 후 컴퓨터가 동일한 IP 주소를 갖도록 할 수 있습니다. IP 주소를 지정하지 않으면 사용 가능한 임의의 주소가 장애 조치 시 할당됩니다. 사용 중인 주소를 지정하는 경우 장애 조치가 실패합니다.
		
        > [AZURE.NOTE] [Migration of networks](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 네트워크에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

		![가상 컴퓨터 속성 구성](./media/site-recovery-hyper-v-site-to-azure-classic/multiple-nic.png)




## 7단계: 복구 계획 만들기

배포를 테스트하기 위해 단일 가상 컴퓨터에 대한 테스트 장애 조치(failover)를 실행하거나 하나 이상의 가상 컴퓨터를 포함한 복구 계획을 실행할 수 있습니다. 복구 계획 만들기에 대해 [자세히 알아보세요](site-recovery-create-recovery-plans.md).

## 8단계: 배포 테스트

Azure로의 테스트 장애 조치(Failover)를 실행하는 두 가지 방법이 있습니다.

- **Azure 네트워크를 사용하지 않는 테스트 장애 조치(Failover)** - 이 유형의 테스트 장애 조치는 Azure에서 가상 컴퓨터가 제대로 작동하는지 확인합니다. 장애 조치(Failover) 후에 가상 컴퓨터가 Azure 네트워크에 연결되지 않습니다.
- **Azure 네트워크를 사용하는 테스트 장애 조치(Failover)** - 이 유형의 테스트 장애 조치는 전체 복제 환경이 예상대로 작동하고 장애 조치된 가상 컴퓨터가 지정한 대상 Azure 네트워크에 연결되는지 확인합니다. 테스트 장애 조치(Failover)의 경우 테스트 가상 컴퓨터의 서브넷이 복제본 가상 컴퓨터의 서브넷에 따라 확인됩니다. 이는 복제본 가상 컴퓨터의 서브넷이 원본 가상 컴퓨터의 서브넷을 기반으로 하는 일반 복제의 경우와 다릅니다.

Azure 네트워크를 지정하지 않고 테스트 장애 조치(Failover)를 실행하려는 경우 아무 것도 준비할 필요가 없습니다.

Azure 대상 네트워크를 사용하여 테스트 장애 조치(Failover)를 실행하려면 Azure 프로덕션 네트워크에서 격리된 새 Azure 네트워크를 만듭니다.(Azure에서 새 네트워크를 만들 때의 기본 동작) 자세한 내용은 [테스트 장애 조치(Failover) 실행](site-recovery-failover.md#run-a-test-failover)을 살펴보세요.


복제 및 네트워크 배포를 완벽하게 테스트하려면 복제된 가상 컴퓨터가 예상대로 작동하도록 인프라를 설정해야 합니다. 이렇게 하는 한 가지 방법은 DNS를 사용하여 가상 컴퓨터를 도메인 컨트롤러로 설정하고 사이트 복구를 사용하여 이를 Azure에 복제하고 테스트 장애 조치(Failover)를 실행하여 테스트 네트워크에 만드는 것입니다. Active Directory와 관련된 자세한 테스트 장애 조치(failover) 고려 사항은 [여기](site-recovery-active-directory.md#considerations-for-test-failover)를 참조하세요.

다음과 같이 테스트 장애 조치(Failover)를 실행합니다.

>[AZURE.NOTE] Azure에 장애 조치를 수행할 때 최상의 성능을 얻으려면 보호된 컴퓨터에 Azure 에이전트를 설치하도록 합니다. 더 빨리 부팅하고 문제가 발생한 경우 진단에도 도움이 됩니다. Linux 에이전트는 [여기](https://github.com/Azure/WALinuxAgent)에서 찾을 수 있습니다. Windows 에이전트는 [여기](http://go.microsoft.com/fwlink/?LinkID=394789)에서 찾을 수 있습니다.

1. **복구 계획** 탭에서 계획을 선택하고 **테스트 장애 조치**를 클릭합니다.
2. **테스트 장애 조치 확인** 페이지에서 **없음** 또는 특정 Azure 네트워크를 선택합니다. **없음**을 선택하면 테스트 장애 조치에서 가상 컴퓨터가 Azure로 올바르게 복제되었는지 확인하지만 복제 네트워크 구성은 확인하지 않습니다.

	![테스트 장애 조치(Failover)](./media/site-recovery-hyper-v-site-to-azure-classic/test-nonetwork.png)

3. **작업** 탭에서 장애 조치 진행률을 추적할 수 있습니다. 또한 Azure 포털에서 가상 컴퓨터 테스트 복제본을 확인할 수 있어야 합니다. 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.
4. 장애 조치가 **테스트 완료** 단계에 도달하면 **테스트 완료**를 클릭하여 테스트 장애 조치를 완료합니다. **작업** 탭으로 드릴다운하여 장애 조치 진행률 및 상태를 추적하고 필요한 작업을 수행할 수 있습니다.
5. 장애 조치(Failover) 후에는 Azure 포털에서 가상 컴퓨터 테스트 복제본을 확인할 수 있습니다. 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.

	1. 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
    2. 장애 조치(Failover) 후에 원격 데스크탑을 사용하여 Azure의 가상 컴퓨터에 연결하려면 가상 컴퓨터에서 원격 데스크탑 연결을 사용하도록 설정하고 나서 테스트 장애 조치(Failover)를 실행합니다. 가상 컴퓨터에서 RDP 끝점을 추가해야 합니다. 이를 위해서 [Azure Automation Runbook](site-recovery-runbook-automation.md)을 활용할 수 있습니다.
    3. 장애 조치(Failover)는 원격 데스크톱을 사용하여 Azure에서 가상 컴퓨터에 연결하기 위해 공용 IP 주소를 사용하는 경우, 공용 주소를 사용하여 가상 컴퓨터에 연결하지 않는 도메인 정책이 없어야 합니다.

6. 테스트가 완료되면 다음을 수행합니다.

	- **테스트 장애 조치(Failover)가 완료되었습니다**를 클릭합니다. 테스트 환경을 정리하여 자동으로 테스트 가상 컴퓨터의 전원을 끄고 컴퓨터를 삭제합니다.
	- **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
7. 장애 조치(Failover)가 **테스트 완료** 단계에 도달하면 다음과 같이 검증을 마칩니다.
	1. Azure 포털에서 복제본 가상 컴퓨터를 봅니다. 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
	2. 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.
	3. **테스트 완료**를 클릭하여 테스트를 마칩니다.
	4. **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
	5.  **테스트 장애 조치(Failover)가 완료되었습니다**를 클릭합니다. 테스트 환경을 정리하여 자동으로 테스트 가상 컴퓨터의 전원을 끄고 컴퓨터를 삭제합니다.

## 다음 단계

배포가 설정되고 실행된 후 장애 조치에 대해 [알아봅니다](site-recovery-failover.md).

<!---HONumber=AcomDC_0921_2016-->