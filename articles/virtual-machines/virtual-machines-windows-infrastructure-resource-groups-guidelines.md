<properties
	pageTitle="리소스 그룹 지침 | Microsoft Azure"
	description="Azure 인프라 서비스에서 리소스 그룹을 배포하기 위한 핵심 디자인 및 구현 지침에 대해 알아봅니다."
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

# Azure 리소스 그룹 지침

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

이 문서에서는 작업 환경을 논리적으로 구축하고 리소스 그룹의 모든 구성 요소를 함께 그룹화하는 방법을 집중적으로 설명합니다.


## 리소스 그룹에 대한 구현 지침

의사 결정:

- 리소스 그룹을 핵심 인프라 구성 요소를 통해 구축하려고 하나? 아니면 전체 응용 프로그램 배포를 통해 구축하려고 하나?
- 역할 기반 액세스 제어를 사용하여 리소스 그룹에 대한 액세스를 제한해야 하나?

작업:

- 필요한 핵심 인프라 구성 요소와 전용 리소스 그룹을 정의합니다.
- 일관되고 재현 가능한 배포를 위해 Resource Manager 템플릿을 구현하는 방법을 검토합니다.
- 리소스 그룹에 대한 액세스를 제어하는 데 필요한 사용자 액세스 역할을 정의합니다.
- 명명 규칙을 사용하여 리소스 그룹 집합을 만듭니다. Azure PowerShell 또는 포털을 사용할 수 있습니다.


## 리소스 그룹

Azure에서 저장소 계정, 가상 네트워크 및 가상 컴퓨터(VM)와 같은 관련된 리소스를 논리적으로 그룹화하여 단일 엔터티로 배포, 관리 및 유지 관리합니다. 이 방법을 통해 관리 관점에서 관련된 모든 리소스를 함께 유지하면서 응용 프로그램을 보다 배포할 수도 있고, 다른 사용자에게 해당 리소스 그룹에 대한 액세스 권한을 부여할 수도 있습니다. 리소스 그룹을 보다 포괄적으로 이해하려면 [Azure Resource Manager 개요](../resource-group-overview.md)를 읽어보세요.

리소스 그룹의 주요 기능은 템플릿을 사용하여 사용자 환경을 구축하는 기능입니다. 템플릿은 저장소, 네트워킹 및 계산 리소스를 선언하는 JSON 파일입니다. 관련된 모든 사용자 지정 스크립트 또는 적용할 구성을 정의할 수도 있습니다. 이러한 템플릿을 사용하여 응용 프로그램에 대해 일관되고 재현 가능한 배포를 만듭니다. 이 방법을 통해 개발 환경을 쉽게 구축한 다음 동일한 템플릿을 사용하여 프로덕션 배포를 만들 수도 있고 그 반대도 가능합니다. 템플릿 사용 방식을 더욱 잘 이해하기 위해 템플릿 작성의 각 단계를 안내하는 [템플릿 연습](../resource-manager-template-walkthrough.md)을 읽어보세요.

리소스 그룹을 사용하여 환경을 디자인할 때 다음 두 가지 방식을 활용할 수 있습니다.

- 저장소 계정, 가상 네트워크 및 서브넷, VM, 부하 분산 장치 등을 결합하는 각 응용 프로그램 배포를 위한 리소스 그룹.
- 핵심 가상 네트워킹과 서브넷 또는 저장소 계정이 포함된 중앙 집중식 리소스 그룹. 사용자의 응용 프로그램은 VM, 부하 분산 장치, 네트워크 인터페이스 등만을 포함하는 자체 리소스 그룹에 있게 됩니다.

가상 네트워킹 및 서브넷에 대한 중앙 집중식 리소스 그룹을 만드는 방식이 규모 확장 시 하이브리드 연결 옵션에 맞는 크로스-프레미스 네트워크 연결을 보다 쉽게 만들 수 있습니다. 또 다른 방법은 각 응용 프로그램이 구성 및 유지 관리가 필요한 자체 가상 네트워크를 포함하도록 하는 것입니다. [역할 기반 액세스 제어](../active-directory/role-based-access-control-what-is.md)는 리소스 그룹에 대한 액세스를 보다 세밀하게 제어할 수 있도록 합니다. 프로덕션 응용 프로그램에서는 이러한 리소스에 액세스할 수 있는 사용자를 제어할 수 있고, 핵심 인프라 리소스의 경우에는 인프라 엔지니어만 사용할 수 있게 제안할 수 있습니다. 응용 프로그램 소유자는 작업 환경의 핵심 Azure 인프라가 아닌 리소스 그룹 내의 응용 프로그램 구성 요소에만 액세스할 수 있습니다. 작업 환경을 디자인할 때 리소스에 액세스해야 하는 사용자를 고려하고 그에 맞게 리소스 그룹을 디자인해야 합니다.


## 다음 단계

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->