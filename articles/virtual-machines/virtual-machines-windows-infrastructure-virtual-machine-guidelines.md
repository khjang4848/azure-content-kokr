<properties
	pageTitle="Windows 가상 컴퓨터 지침 | Microsoft Azure"
	description="Azure에서 Windows 가상 컴퓨터를 배포하기 위한 핵심 디자인 및 구현 지침에 대해 알아봅니다."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# 가상 컴퓨터 지침

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

이 문서에서는 Azure 환경 내에서 VM(가상 컴퓨터)을 만들고 관리하는 데 필요한 계획 단계를 이해하는 데 주안점을 둡니다.

## VM에 대한 구현 지침
의사 결정:

- 인프라의 다양한 응용 프로그램 계층 및 구성 요소를 위해 몇 개의 VM이 필요한가?
- 각 VM에 어떤 CPU 및 메모리 리소스가 필요하며 저장소 요구 사항은 무엇인가?

작업:

- 응용 프로그램의 워크로드와 VM에 필요한 리소스를 정의합니다.
- 해당 VM 크기 및 저장소 형식에 따라 각 VM의 리소스 요구를 결정합니다.
- 인프라의 다양한 계층 및 구성 요소에 대한 리소스 그룹을 정의합니다.
- VM 명명 규칙을 정의합니다.
- Azure PowerShell, 웹 포털 또는 Resource Manager 템플릿을 사용하여 VM을 만듭니다.

## 가상 컴퓨터

Azure 환경 내의 주요 구성 요소 중 하나가 VM일 것입니다. 여기서 응용 프로그램, 데이터베이스, 인증, 서비스 등을 실행합니다.

성능 및 비용 측면에서 환경 규모를 올바르게 조정하려면 [다양한 VM 크기](virtual-machines-windows-sizes.md)를 이해하는 것이 중요합니다. VM에 충분한 CPU 코어 또는 메모리가 없으면 디자인 및 개발 품질과 관계없이 응용 프로그램의 성능이 저하됩니다. 인프라의 각 구성 요소에 사용할 VM 크기를 결정할 때는 각 VM 시리즈에 대해 제안된 워크로드를 먼저 검토해야 합니다. 배포 후에 [VM의 크기를 변경](https://azure.microsoft.com/blog/resize-virtual-machines/)할 수 있습니다.

저장소는 VM의 성능에 중요한 역할을 합니다. 일반 스핀 디스크를 사용하는 Standard 저장소를 사용할 수도 있고, I/O 워크로드가 높고 최고의 성능이 필요한 경우에는 SSD 디스크를 사용하는 Premium 저장소를 사용할 수도 있습니다. VM 크기와 마찬가지로 저장 미디어를 선택하는 데 비용을 고려해야 합니다. 최적의 VM 성능을 위해 적절한 저장소를 디자인하는 방법을 이해하려는 경우 [저장소 인프라 지침 문서](virtual-machines-windows-infrastructure-storage-solutions-guidelines.md)를 읽어보세요.


## 리소스 그룹
VM과 같은 구성 요소는 손쉬운 관리 및 유지 관리를 위해 [Azure 리소스 그룹](../resource-group-overview.md)을 사용하여 논리적으로 그룹화됩니다. 리소스 그룹을 사용하여 지정된 응용 프로그램을 구성하는 모든 리소스를 만들고, 관리하고, 모니터링할 수 있습니다. [역할 기반 액세스 제어](../active-directory/role-based-access-control-what-is.md)를 구현하여 팀 내의 사람 사람들에게 필요한 리소스에 대한 액세스 권한만 부여할 수도 있습니다. 시간을 투입하여 리소스 그룹 및 역할 할당을 계획합니다. 실제로 리소스 그룹을 디자인 및 구현하는 다양한 접근 방법이 있으므로 VM을 구축하는 최선의 방법을 이해하려면 [리소스 그룹 지침 문서](virtual-machines-windows-infrastructure-resource-groups-guidelines.md)를 읽어보세요.


## 템플릿 
선언적 JSON 파일에 정의되는 템플릿을 작성하여 VM을 만들 수 있습니다. 템플릿은 VM 자체는 물론, 일반적으로 필요한 저장소, 네트워킹, 네트워크 인터페이스, IP 주소 지정 등을 작성합니다. 템플릿을 통해 개발 및 테스트를 위한 일관되고 재현 가능한 환경을 만들어 프로덕션 환경에 쉽게 복제하고 그 반대도 가능합니다. VM을 만들어 배포하는 데 템플릿을 사용하는 방법을 이해하려면 [템플릿 작성 및 사용](../resource-group-overview.md#template-deployment)에 대해 자세히 읽어보세요.


## 다음 단계
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->