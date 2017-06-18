<properties
	pageTitle="Azure DevTest Labs에서 랩 정책 정의| Microsoft Azure"
	description="VM 크기, 사용자당 최대 VM 수 및 자동 종료와 같은 랩 정책을 정의하는 방법에 대해 알아봅니다."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="tarcher"/>

# Azure DevTest Labs에서 랩 정책 정의

<div class="wa-video-container" data-control="video-container" data-slug="how-to-set-vm-policies-in-a-devtest-lab"> <a href="/en-us/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab/" data-control="video" data-expanding="false" class="wa-video-thumbnail" title="How to set VM policies in DevTest Labs" data-ch9="//channel9.msdn.com/Blogs/Windows-Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player/" data-caption="" data-event="videopage-clicked-videothumbnail" data-event-property="how-to-set-vm-policies-in-a-devtest-lab"> <img src="https://sec.ch9.ms/ch9/d21b/f24707d6-bad4-4f4a-818b-ea6ec1a1d21b/dtlvmpolicies_960.jpg" alt="How to set VM policies in DevTest Labs" class="thumbnail"> <img class="play-icon" src="/cdn/cvt-a29a0e789afe7f47464f393c011b53f210c511ee5fed2c4c9c0633442f30d6ec/images/icon/VideoPlay.svg"> <span class="metadata"> <span class="date"> <span>01-20-2016</span> <span>03 min, 47 sec</span> </span> </span> </a> </div>

Azure DevTest Labs에서 비용을 관리하고 랩에서의 낭비를 최소화하는 데 도움이 될 핵심 정책을 지정할 수 있습니다. 이러한 랩 정책은 각 사용자 및 랩에 대해 만들어지는 VM의 최대 수와 다양한 자동 종료 및 자동 시작 옵션을 포함합니다.

## Azure DevTest Labs의 랩 정책에 액세스

다음 단계에서는 Azure DevTest Labs에서 랩에 대한 정책을 설정하는 과정을 안내합니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **서비스 더 보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **정책 설정**을 선택합니다.

1. **정책 설정** 블레이드에는 지정할 수 있는 설정의 메뉴가 들어 있습니다.

	![정책 설정 블레이드](./media/devtest-lab-set-lab-policy/policies.png)

	정책 설정에 관해 자세히 알아보려면 다음 목록에서 선택합니다.

	- [허용된 가상 컴퓨터 크기](#set-allowed-virtual-machine-sizes) - 랩에서 허용되는 VM 크기 목록을 선택합니다. 사용자는 이 목록에서만 VM을 만들 수 있습니다.

	- [사용자당 가상 컴퓨터](#set-virtual-machines-per-user) - 사용자가 만들 수 있는 최대 VM 수를 지정합니다.

	- [랩당 가상 컴퓨터](#set-virtual-machines-per-lab) - 랩에 대해 만들 수 있는 최대 VM 수를 지정합니다.

	- [자동 종료](#set-auto-shutdown) - 현재 랩의 VM이 자동으로 종료되는 시간을 지정합니다.

	- [자동 시작](#set-auto-start) - 현재 랩의 VM이 자동으로 시작되는 시간을 지정합니다.

## 허용된 가상 컴퓨터 크기를 설정합니다.

허용 VM 크기를 설정하는 정책은 랩에서 허용되는 VM 크기를 지정함으로써 랩의 낭비가 최소화되도록 돕습니다. 이 정책이 활성화되면 이 목록의 VM 크기만 사용하여 VM을 만들 수 있습니다.

1. 랩의 **정책 설정** 블레이드에서, **허용된 가상 컴퓨터 크기**를 선택합니다.

	![허용된 가상 컴퓨터 크기](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정한 경우 랩에서 만들 수 있는 하나 이상의 VM 크기를 선택합니다.

1. **저장**을 선택합니다.

## 사용자당 가상 컴퓨터를 설정합니다.

**사용자당 가상 컴퓨터**에 대한 정책을 사용하면 개별 사용자가 만들 수 있는 최대 VM 수를 지정할 수 있습니다. 사용자 제한에 도달하면 사용자가 VM을 만들려고 하는 경우 VM을 만들 수 없다는 오류 메시지가 표시됩니다.

1. 랩의 **정책 설정** 블레이드에서, **사용자당 가상 컴퓨터**를 선택합니다.

	![사용자당 가상 컴퓨터](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정했다면 사용자가 만들 수 있는 최대 VM 수를 나타내는 숫자 값을 입력합니다. 유효하지 않은 수를 입력하면 필드에 허용되는 최대 수가 UI에 표시됩니다.

1. **저장**을 선택합니다.

## 랩당 가상 컴퓨터를 설정합니다.

**랩당 가상 컴퓨터**에 대한 정책을 사용하면 현재 랩에 대해 생성할 수 있는 최대 VM 수를 지정할 수 있습니다. 랩 제한에 도달하면 사용자가 VM을 만들려고 하는 경우 VM을 만들 수 없다는 오류 메시지가 표시됩니다.

1. 랩의 **정책 설정** 블레이드에서, **랩당 가상 컴퓨터**를 선택합니다.

	![랩당 가상 컴퓨터](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정했다면 현재 랩에 대해 만들어질 수 있는 최대 VM 수를 나타내는 숫자 값을 입력합니다. 유효하지 않은 수를 입력하면 필드에 허용되는 최대 수가 UI에 표시됩니다.

1. **저장**을 선택합니다.

## 자동 종료 설정

자동 종료 정책에서는 이 랩의 VM이 종료되는 시간을 지정하여 랩 낭비를 최소화할 수 있도록 돕습니다.

1. 랩의 **정책 설정** 블레이드에서, **자동 종료**를 선택합니다.

	![자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 현지 시간을 지정합니다.

1. **저장**을 선택합니다.

1. 기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 특정 VM에서 이 설정을 제거하려면 VM의 블레이드를 열고 해당 **자동 종료** 설정을 변경합니다.

## 자동 시작 설정

자동 시작 정책을 사용하면 현재 랩의 VM을 시작할 시기를 지정할 수 있습니다.

1. 랩의 **정책 설정** 블레이드에서, **자동 시작**을 선택합니다.

	![자동 시작](./media/devtest-lab-set-lab-policy/auto-start.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정한 경우 로컬 예약 시작 시간 및 해당 시간이 적용되는 요일을 지정합니다.

1. **저장**을 선택합니다.

1. 사용하도록 설정해도 이 정책이 현재 랩의 VM에 자동으로 적용되지 않습니다. 이 설정을 특정 VM에 적용하려면 VM의 블레이드를 열고 해당 **자동 시작** 설정을 변경합니다.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 다음 단계

랩에 대한 다양한 VM 정책 설정을 정의 및 적용한 후에는 다음 작업을 수행하세요.

- [비용 관리 구성](./devtest-lab-configure-cost-management.md) - **월간 예상 비용 추세** 차트를 사용하여 이번 달의 현재 예상 비용 합계 및 월말 추정 비용을 확인하는 방법을 보여줍니다.
- [사용자 지정 이미지 만들기](./devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 마켓플레이스 이미지 중에서 기준을 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
- [마켓플레이스 이미지 구성](./devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs에서는 Azure Marketplace 이미지를 기준으로 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure 마켓플레이스 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
- [랩에서 VM 만들기](./devtest-lab-add-vm-with-artifacts.md) - 기본 이미지(사용자 지정 또는 마켓플레이스 이미지)에서 VM을 만드는 방법 및 VM의 아티팩트 작업 방법에 대해 설명합니다.

<!---HONumber=AcomDC_0914_2016-->