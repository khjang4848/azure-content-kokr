<properties
   pageTitle="Azure VM 백업 실패: 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없음 - 스냅숏 VM 하위 작업 시간 초과 | Microsoft Azure"
   description="Azure VM 백업 실패에 대한 증상 원인 및 해결 방법은 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없는 문제와 관련이 있습니다. 스냅숏 VM 하위 작업 시간 초과 오류"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jwhit"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/14/2016"
    ms.author="jimpark; markgal;genli"/>

# Azure VM 백업 실패: 스냅숏 상태에 대해 VM 에이전트와 통신할 수 없음 - 스냅숏 VM 하위 작업 시간 초과

## 요약

Azure 백업에 대한 VM(가상 컴퓨터)을 등록 및 예약 후, Azure 백업 서비스는 지정 시간 스냅숏을 생성하는 VM에서 백업 확장과 통신하여 예약된 시간에 백업 작업을 시작합니다. 스냅숏이 트리거되어 백업이 실패하지 않도록 해야 하는 조건이 있습니다. 이 문서에서는 스냅숏 시간 초과 오류와 관련된 Azure VM 백업 오류 관련 문제에 대한 문제 해결 단계를 제공합니다.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 증상

IaaS(서비스 제공 인프라) VM에 대한 Microsoft Azure 백업이 실패하면 Azure 포털의 작업 오류 세부 정보에 다음 오류 메시지가 반환됩니다.

**스냅숏 상태에 대해 VM 에이전트와 통신할 수 없습니다. 스냅숏 VM 하위 작업이 시간 초과되었습니다.**

## 원인
이 오류의 일반적인 원인은 다음과 같이 네 가지가 있습니다.

- VM이 인터넷에 액세스할 수 없습니다.
- VM에 설치된 Microsoft Azure VM 에이전트가 최신이 아닙니다(Linux VM의 경우).
- 백업 확장을 업데이트 또는 로드할 수 없습니다.
- 스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.

## 원인 1: VM이 인터넷에 액세스할 수 없습니다.
배포 요구 사항에 따라 VM이 인터넷에 연결되어 있지 않거나 Azure 인프라에 대한 액세스를 차단하는 위치에 대한 제한 사항이 있습니다.

백업 확장이 제대로 작동하려면 Azure 공용 IP 주소에 연결되어야 하는데 이는 VM의 스냅숏을 관리하도록 Azure 저장소 끝점(HTTP URL)에 명령을 보내기 때문입니다. 확장이 공용 인터넷에 액세스할 수 없는 경우 백업은 결국 실패합니다.

### 해결 방법
이 시나리오에서 문제를 해결하려면 다음 방법 중 하나를 사용합니다.

- Azure 데이터 센터 IP 범위 허용 목록
- HTTP 트래픽을 보내는 경로 만들기

### Azure 데이터 센터 IP 범위 허용 목록에 추가하려면

1. 허용 목록에 추가된 [Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 가져옵니다.
2. New-NetRoute cmdlet을 사용하여 IP 차단을 해제합니다. 관리자 권한 PowerShell 창(관리자 권한으로 실행)의 Azure VM 내에서 이 cmdlet을 실행합니다.
3. NSG(네트워크 보안 그룹)가 있는 경우 IP에 대한 액세스를 허용하도록 NSG에 규칙을 추가합니다.

### HTTP 트래픽을 보내는 경로를 만들려면

1. 네트워크 제한이 있는 경우(예: NSG) 트래픽을 라우트하는 HTTP 프록시 서버를 배포합니다.
2. NSG(네트워크 보안 그룹)가 있는 경우 HTTP 프록시에서 인터넷에 액세스할 수 있도록 규칙을 추가합니다.

[VM 백업에 HTTP 프록시를 설정](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)하는 방법에 대해 알아봅니다.

## 원인 2: VM에 설치된 Microsoft Azure VM 에이전트가 최신이 아닙니다(Linux VM의 경우).

### 해결 방법
Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 일반적인 지침으로 이 문제를 해결하는 첫 번째 단계는 다음과 같습니다.

1. [최신 Azure VM 에이전트를 설치합니다](https://github.com/Azure/WALinuxAgent).
2. VM에 Azure 에이전트가 실행 중인지 확인합니다. 이렇게 하려면 다음 명령을 실행합니다. ```ps -e```

    이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

    Ubuntu의 경우: ```service walinuxagent start```

    기타 배포의 경우: ```service waagent start
```

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. 새 테스트 백업을 실행합니다. 오류가 지속되면 추가 디버깅을 위해 다음 폴더에서 로그를 수집합니다.

    고객의 VM에서 다음 로그가 필요합니다.

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

waagent의 자세한 로깅이 필요한 경우 다음 단계에 따라 사용하도록 설정합니다.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다.

    Enable verbose logging (y|n)

2. **Logs.Verbose** 값을 n에서 y로 변경합니다.
3. 변경 내용을 저장하고 이 섹션의 이전 단계를 수행하여 waagent를 다시 시작합니다.

## 원인 3: 백업 확장을 업데이트 또는 로드할 수 없습니다.
확장을 로드할 수 없는 경우 스냅숏을 만들 수 없기 때문에 백업이 실패합니다.

### 해결 방법
Windows 게스트의 경우:

1. Iaasvmprovider 서비스가 사용하도록 설정되어 있는지와 자동 시작 유형인지를 확인합니다.
2. 이렇게 구성되어 있지 않으면 서비스를 사용하도록 설정하여 다음 백업 성공 여부를 결정합니다.

Linux 게스트:

VMSnapshot Linux 최신 버전(백업에 사용되는 확장)은 1.0.91.0입니다.

백업 확장을 여전히 업데이트 또는 로드할 수 없는 경우 확장을 제거하여 VMSnapshot 확장이 다시 로드되도록 할 수 있습니다. 다음 백업 시도 시 확장이 다시 로드됩니다.

### 확장을 제거하려면

1. [Azure 포털](https://portal.azure.com/)로 이동합니다.
2. 백업 문제가 있는 특정 VM을 찾습니다.
3. **설정**을 클릭합니다.
4. **확장**을 클릭합니다.
5. **Vmsnapshot 확장**을 클릭합니다.
6. **제거**를 클릭합니다.

이렇게 하면 다음 백업 동안 확장을 다시 설치해야 합니다.

## 원인 4: 스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.
VM 백업은 기본 저장소에 대한 스냅숏 명령 실행을 사용합니다. 저장소에 액세스할 수 없거나 스냅숏 작업 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

### 해결 방법
다음 조건으로 인해 스냅숏 작업 오류가 발생할 수 있습니다.

| 원인 | 해결 방법 |
| ----- | ----- |
| Microsoft SQL Server가 있는 VM 백업이 구성되었습니다. 기본적으로 VM 백업은 Windows VM에서 VSS 전체 백업을 실행합니다. SQL Server 기반 서버를 실행하고 SQL Server 백업이 구성된 VM에서 스냅숏 실행이 지연될 수 있습니다. | 스냅숏 문제로 인해 백업이 실패하면 다음 레지스트리 키를 설정하세요.<br><br>[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM이 RDP에서 종료되므로 VM 상태가 잘못 보고됩니다. RDP에서 가상 컴퓨터를 종료하는 경우 VM 상태가 올바르게 반영되는지 여부를 확인하려면 포털을 확인합니다. | 그러지 않은 경우 VM 대시보드의 "종료" 옵션을 사용하여 포털에서 VM을 종료합니다. |
| 동일한 클라우드 서비스에서 여러 VM이 동시에 백업하도록 구성됩니다. | 다른 백업 일정을 갖도록 동일한 클라우드 서비스에서 VM을 분산하는 것이 모범 사례입니다. |
| VM이 사용량이 높은 CPU 또는 메모리에서 실행 중입니다. | VM이 사용량이 높은 CPU(90% 이상) 또는 메모리에서 실행 중인 경우 스냅숏 작업이 큐에 대기 및 지연되어 결국 시간 초과됩니다. 이 상황에서는 주문형 백업을 시도하세요. |
|VM이 DHCP에서 호스트/패브릭 주소를 가져올 수 없습니다.|IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. VM이 DHCP 응답 245에서 호스트/패브릭 주소를 가져올 수 없는 경우에는 어떤 확장도 다운로드하거나 실행할 수 없습니다. 고정 개인 IP가 필요한 경우 플랫폼을 통해 구성해야 합니다. VM 내 DHCP 옵션은 사용 가능한 상태로 두어야 합니다. [고정 내부 개인 IP 설정](../virtual-network/virtual-networks-reserved-private-ip.md)에 대한 자세한 내용을 확인합니다.|

<!---HONumber=AcomDC_0921_2016-->