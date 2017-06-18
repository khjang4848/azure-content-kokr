<properties
   pageTitle="Linux VM 배포 RM 문제 해결 | Microsoft Azure"
   description="Azure에서 새 Linux 가상 컴퓨터 생성 시 Resource Manager 배포 문제 해결"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# Azure에서 새 Linux 가상 컴퓨터 생성 관련 Resource Manager 배포 문제 해결

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 감사 로그 수집

문제 해결을 시작하려면 문제와 관련된 오류를 파악하기 위해 감사 로그를 수집합니다. 다음 링크에는 수행할 프로세스에 대한 자세한 내용이 포함되어 있습니다.

[Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)

[리소스 관리자로 작업 감사](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** OS가 일반화된 Linux인 경우, 일반화된 설정을 사용하여 업로드되거나 캡처되고, 오류는 발생하지 않습니다. 마찬가지로 OS가 특수화된 Linux인 경우, 특수화된 설정을 사용하여 업로드되거나 캡처되고, 오류는 발생하지 않습니다.

**업로드 오류:**

**N<sup>1</sup>:** OS가 일반화된 Linux이고 특수화된 것으로 업로드된다면, VM이 프로비전 단계에서 중단되기 때문에 프로비전 시간 초과 오류가 발생합니다.

**N<sup>2</sup>:** OS가 특수화된 Linux이고 일반화된 것으로 업로드된다면, 새 VM이 원래 컴퓨터 이름, 사용자 이름 및 암호로 실행되기 때문에 프로비전 실패 오류가 발생합니다.

**해결 방법:**

이 두 가지 오류를 모두 해결하려면, 온-프레미스에 있는 원본 VHD를, OS와 같은 설정(일반화/특수화)으로 업로드합니다. 일반화된 것으로 업로드하려면, 먼저 프로비전 해제를 실행해야 합니다.

**캡처 오류:**

**N<sup>3</sup>:** OS가 일반화된 Linux이고 특수화된 것으로 캡처된다면, 원본 VM이 일반화된 것으로 표시되어 사용할 수 없기 때문에 프로비전 시간 초과 오류가 발생합니다.

**N<sup>4</sup>:** OS가 특수화된 Linux이고 일반화된 것으로 캡처된다면, 새 VM이 원래 컴퓨터 이름, 사용자 이름 및 암호로 실행되기 때문에 프로비전 실패 오류가 발생합니다. 또한, 원본 VM이 일반화된 것으로 표시되기 때문에 사용할 수 없습니다.

**해결 방법:**

이 두 가지 오류를 모두 해결하려면, 현재 이미지를 포털에서 제거하고, OS와 같은 설정(일반화/특수화)으로 [현재 VHD에서 다시 캡처](virtual-machines-linux-capture-image.md)합니다.

## 문제: 사용자 지정/ 갤러리/ 마켓플레이스 이미지; 할당 오류
이 오류는 요청되는 VM 크기를 지원할 수 없거나 요청을 수용할 여유 공간이 없는 클러스터에 새 VM 요청이 고정된 상황에서 발생합니다.

**원인 1:** 클러스터가 요청한 VM 크기를 지원할 수 없습니다.

**해결 방법 1:**

- 더 작은 VM 크기를 사용하여 요청을 다시 시도합니다.
- 요청한 VM의 크기를 변경할 수 없으면:
  - 가용성 집합의 VM을 모두 중지합니다. **리소스 그룹** > *사용자의 리소스 그룹* > **리소스** > *사용자의 가용성 집합* > **가상 컴퓨터** > *사용자의 가상 컴퓨터* > **중지**를 클릭합니다.
  - VM을 모두 중지한 후에, 원하는 크기로 VM을 새로 만듭니다.
  - 먼저 VM을 시작한 후에 중지된 각각의 VM을 선택하고 **시작**을 클릭합니다.

**원인 2:** 클러스터에 여유 리소스가 없습니다.

**해결 방법 2:**

- 요청을 나중에 다시 시도합니다.
- 새 VM이 다른 가용성 집합의 일부가 될 수 있다면
  - 다른 가용성 집합(동일한 지역의)에 새 VM을 만듭니다.
  - 새 VM을 동일한 가상 네트워크에 추가합니다.

## 다음 단계
중지된 Linux VM을 시작하거나 Azure에서 기존 Linux VM의 크기를 조정할 때 문제가 발생하면 [Azure의 기존 Linux 가상 컴퓨터 재시작 또는 크기 조정 관련 Resource Manager 배포 문제 해결](virtual-machines-linux-restart-resize-error-troubleshooting.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->