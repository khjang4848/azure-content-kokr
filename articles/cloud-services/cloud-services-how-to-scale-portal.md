<properties
	pageTitle="포털에서 클라우드 서비스의 크기를 자동으로 조정하는 방법 | Microsoft Azure"
	description="포털을 사용하여 Azure에서 클라우드 서비스 웹 역할 또는 작업자 역할에 대한 자동 크기 조정 규칙을 구성하는 방법에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="adegeo"/>


# 클라우드 서비스 크기를 자동으로 조정하는 방법

> [AZURE.SELECTOR]
- [Azure 포털](cloud-services-how-to-scale-portal.md)
- [Azure 클래식 포털](cloud-services-how-to-scale.md)

규모 감축 또는 규모 확장 작업을 트리거하는 클라우드 서비스 작업자 역할에 대해 조건을 설정할 수 있습니다. 역할에 대한 조건은 CPU, 디스크 또는 역할의 네트워크 부하를 기반으로 할 수 있습니다. 메시지 큐 또는 구독에 연결된 일부 다른 Azure 리소스의 메트릭을 기준으로 조건을 설정할 수도 있습니다.

>[AZURE.NOTE] 이 문서에서는 클라우드 서비스 웹 및 작업자 역할에 중점을 둡니다. 가상 컴퓨터(클래식)를 직접 만든 경우 이 가상 컴퓨터는 클라우드 서비스에서 호스트됩니다. 표준 가상 컴퓨터를 [가용성 집합](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)에 연결하여 규모를 조정하고 수동으로 켜거나 끌 수 있습니다.

## 고려 사항

응용 프로그램의 크기 조정을 구성하기 전에 다음 내용을 고려해야 합니다.

- 크기 조정은 코어 사용량의 영향을 받습니다. 역할 인스턴스가 클수록 더 많은 코어를 사용합니다. 응용 프로그램의 크기는 구독에 대한 코어 제한 내에서만 조정할 수 있습니다. 예를 들어 구독의 코어 제한이 20이고 중간 크기의 클라우드 서비스 두 대에서 응용 프로그램을 실행할 경우(총 코어 수 4개), 구독에 있는 다른 클라우드 서비스 배포를 16코어까지만 확장할 수 있습니다. 크기에 대한 자세한 내용은 [클라우드 서비스 크기](cloud-services-sizes-specs.md)를 참조하세요.

- 큐 메시지 임계값에 따라 크기를 조정할 수 있습니다. 큐 사용 방법에 대한 자세한 내용은 [큐 저장소 서비스를 사용하는 방법](../storage/storage-dotnet-how-to-use-queues.md)을 참조하세요.

- 구독에 연결된 다른 리소스의 크기도 조정할 수 있습니다.

- 응용 프로그램의 가용성을 높이려면 응용 프로그램이 두 개 이상의 역할 인스턴스와 함께 배포되는지 확인해야 합니다. 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## 크기 조정 기능이 제공되는 위치

클라우드 서비스를 선택하면 클라우드 서비스 블레이드가 표시되어야 합니다.

1. 클라우드 서비스 블레이드의 **역할 및 인스턴스** 타일에서 클라우드 서비스의 이름을 선택합니다. **중요**: 역할 아래에 있는 역할 인스턴스가 아니라 클라우드 서비스 역할을 클릭해야 합니다.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)

2. **크기 조정** 타일을 선택합니다.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## 자동 크기 조정

**수동** 또는 **자동**의 두 가지 모드 중 하나를 사용하여 역할에 대한 크기 조정 설정을 구성할 수 있습니다. 수동에서는 예상할 수 있는 절대 인스턴스 수를 설정합니다. 하지만 자동을 사용하면 크기 조정 방법과 정도를 제어하는 규칙을 설정할 수 있습니다.

**크기 조정 기준** 옵션을 **일정 및 성능 규칙**으로 설정합니다.

![프로필 및 규칙을 사용하는 클라우드 서비스 크기 조정 설정](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. 기존 프로필입니다.
2. 부모 프로필에 대한 규칙을 추가합니다.
3. 다른 프로필을 추가합니다.

**프로필 추가**를 선택합니다. 프로필은 **항상**, **되풀이**, **고정 날짜** 중에서 크기 조정에 사용할 모드를 결정합니다.

프로필 및 규칙을 구성한 후 맨 위에 있는 **저장** 아이콘을 선택합니다.

#### 프로필

프로필은 크기 조정의 최소 및 최대 인스턴스와 이 크기 조정 범위가 활성화되는 경우를 설정합니다.

* **항상**

    항상은 이 범위의 인스턴스를 항상 사용 가능하게 유지합니다.

    ![항상 크기를 조정하는 클라우드 서비스](./media/cloud-services-how-to-scale-portal/select-always.png)
    
* **되풀이**

    크기를 조정할 요일 집합을 선택합니다.

    ![되풀이 일정을 사용하는 클라우드 서비스 크기 조정](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
    
* **고정 날짜**

    역할 크기를 조정할 고정된 날짜 범위입니다.

    ![고정 날짜를 사용하는 클라우드 서비스 크기 조정](./media/cloud-services-how-to-scale-portal/select-fixed.png)

프로필을 구성한 후 프로필 블레이드 아래쪽에 있는 **확인** 단추를 선택합니다.

#### 규칙

규칙이 프로필에 추가되고 크기 조정을 트리거하는 조건을 나타냅니다.

규칙 트리거는 조건 값을 추가할 수 있는 클라우드 서비스(CPU 사용량, 디스크 작업 또는 네트워크 작업)의 메트릭을 기준으로 합니다. 또한 메시지 큐 또는 구독에 연결된 일부 다른 Azure 리소스의 메트릭을 기준으로 트리거가 작동되도록 할 수 있습니다.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

규칙을 구성한 후 규칙 블레이드 아래쪽에 있는 **확인** 단추를 선택합니다.

## 수동 크기 조정으로 돌아가기

[크기 조정 설정](#where-scale-is-located)으로 이동한 후 **크기 조정 기준** 옵션을 **수동으로 입력한 인스턴스 수**로 설정합니다.

![프로필 및 규칙을 사용하는 클라우드 서비스 크기 조정 설정](./media/cloud-services-how-to-scale-portal/manual-basics.png)

이렇게 하면 역할에서 자동 크기 조정이 제거되고 인스턴스 수를 직접 설정할 수 있게 됩니다.

1. 크기 조정(수동 또는 자동) 옵션입니다.
2. 크기 조정할 인스턴스를 설정하기 위한 역할 인스턴스 슬라이더입니다.
3. 크기를 조정할 역할의 인스턴스입니다.

크기 조정 설정을 구성한 후 맨 위에 있는 **저장** 아이콘을 선택합니다.

<!---HONumber=AcomDC_0914_2016-->