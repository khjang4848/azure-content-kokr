<properties
   pageTitle="VM 재시작 또는 크기 조정 문제 | Microsoft Azure"
   description="Azure의 기존 Windows 가상 컴퓨터 재시작 또는 크기 조정 관련 Resource Manager 배포 문제 해결"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# Azure의 기존 Windows 가상 컴퓨터 재시작 또는 크기 조정 관련 Resource Manager 배포 문제 해결

중지된 Azure 가상 컴퓨터(VM)를 시작하거나, 기존 Azure AM의 크기를 조정하려다 접하는 일반적인 오류는 할당 오류입니다. 이런 오류는 클러스터나 지역에 사용할 수 있는 리소스가 없거나 요청한 VM 크기를 지원할 수 없을 때 발생합니다.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 감사 로그 수집

문제 해결을 시작하려면 문제와 관련된 오류를 파악하기 위해 감사 로그를 수집합니다. 다음 링크에는 프로세스에 대한 자세한 내용이 포함되어 있습니다.

[Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)

[리소스 관리자로 작업 감사](../resource-group-audit.md)

## 문제: 중지된 VM 시작 시 오류

중지된 VM을 시작하려는데 할당 오류가 발생했습니다.

### 원인

중지된 VM을 시작하기 위한 요청은 클라우드 서비스를 호스트하는 원본 클러스터에서 시도되어야 합니다. 하지만, 클러스터에 요청을 수행하는 데 사용할 여유 공간이 없습니다.

### 해결 방법

*	가용성 집합의 VM을 모두 중지하고 각각의 VM을 다시 시작합니다.

  1. **리소스 그룹** > _사용자의 리소스 그룹_ > **리소스** > _사용자의 가용성 집합_ > **가상 컴퓨터** > _사용자의 가상 컴퓨터_ > **중지**를 클릭합니다.

  2. VM을 모두 중지한 후에, 중지된 각각의 VM을 선택하고 시작을 클릭합니다.

*	나중에 다시 시작 요청을 다시 시도합니다.

## 문제: 기존 VM 재시작 시 오류

기존 VM의 크기를 조정하려는데 할당 오류가 발생했습니다.

### 원인

VM 크기를 조정하기 위한 요청은 클라우드 서비스를 호스트하는 원본 클러스터에서 시도되어야 합니다. 하지만, 클러스터가 요청한 VM 크기를 지원하지 않습니다.

### 해결 방법

* 더 작은 VM 크기를 사용하여 요청을 다시 시도합니다.

* 요청한 VM의 크기를 변경할 수 없으면:

  1. 가용성 집합의 VM을 모두 중지합니다.

    * **리소스 그룹** > _사용자의 리소스 그룹_ > **리소스** > _사용자의 가용성 집합_ > **가상 컴퓨터** > _사용자의 가상 컴퓨터_ > **중지**를 클릭합니다.

  2. VM을 모두 중지한 후에, 원하는 VM을 더 크게 조정합니다.
  3. 크기가 조정된 VM을 선택하고 **시작**을 클릭한 다음 중지된 각각의 VM을 시작합니다.

## 다음 단계

Azure에서 새 Windows VM을 만들 때 문제가 발생하면 [Azure에서 새 Windows 가상 컴퓨터 생성 관련 배포 문제 해결](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->