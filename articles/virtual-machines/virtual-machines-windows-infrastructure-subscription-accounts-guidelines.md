<properties
	pageTitle="구독 및 계정 지침 | Microsoft Azure"
	description="Azure의 구독 및 계정에 대한 핵심 디자인 및 구현 지침에 대해 알아봅니다."
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

# 구독 및 계정 지침

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

이 문서에서는 환경 및 사용자 기반이 커질 때 구독 및 계정 관리에 접근하는 방식을 이해하는 데 주안점을 둡니다.


## 구독 및 계정에 대한 구현 지침

의사 결정:

- IT 작업 또는 인프라를 호스트하는 데 필요한 구독 및 계정 집합은 무엇인가?
- 조직에 맞게 계층을 어떻게 분류해야 하는가?

작업:

- 구독 수준에서 관리하려고 하므로 논리 조직 계층을 정의합니다.
- 이러한 논리 계층 구조에 맞게 필요한 계정을 정의하고 각 계정 아래에 구독을 정의합니다.
- 명명 규칙을 사용하여 구독 및 계정 집합을 만듭니다.


## 구독 및 계정

Azure를 사용하려면 하나 이상의 Azure 구독이 필요합니다. VM(가상 컴퓨터) 또는 가상 네트워크와 같은 리소스는 해당 구독에 존재합니다.

- 기업 고객은 일반적으로 기업 등록 계약을 합니다. 이는 계층에서 가장 중요한 리소스이며 하나 이상의 계정과 관련됩니다.
- 기업 등록 계약이 없는 소비자 및 고객의 경우, 가장 중요한 리소스는 계정입니다.
- 구독은 계정과 관련되며 계정당 하나 이상의 구독이 될 수 있습니다. Azure는 구독 단계에서 청구 정보를 기록합니다.

계정/구독 관계에 두 계층 단계의 제한이 있기 때문에 청구 요구에 계정 및 구독의 명명 규칙을 할당하는 것은 중요합니다. 예를 들어 글로벌 기업에서 Azure를 사용하는 경우 지역당 하나의 계정을 갖도록, 지역 수준에서 구독이 관리되도록 선택할 수 있습니다.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

예를 들어, 다음 구조를 사용할 수 있습니다.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

한 지역에 특정 그룹과 관련된 두 개 이상의 구독을 갖도록 결정하는 경우 명명 규칙은 계정 또는 구독 이름에 추가 데이터를 인코딩하기 위해 하나의 방법으로 통합해야 합니다. 이 조직에서는 청구 보고 중에 새 계층 단계를 생성하는 데 청구 데이터 조작을 허용합니다.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

조직은 다음과 같습니다.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

기업 계약의 단일 계정 또는 모든 계정에 대해 다운로드한 파일을 통해 자세한 청구를 제공합니다.


## 다음 단계

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->