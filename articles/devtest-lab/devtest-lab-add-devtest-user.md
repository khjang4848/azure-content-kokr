<properties
	pageTitle="Azure DevTest Labs에 소유자 및 사용자 추가 | Microsoft Azure"
	description="Azure Portal 또는 PowerShell을 사용하여 Azure DevTest Labs에 소유자 및 사용자 추가"
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

# Azure DevTest Labs에 소유자 및 사용자 추가

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

Azure DevTest Labs의 액세스는 [Azure RBAC(역할 기반 액세스 제어)](../active-directory/role-based-access-control-what-is.md)를 통해 제어됩니다. RBAC를 사용하면 팀 내에서 업무를 수행하기 위해 사용자에게 필요한 만큼의 액세스 권한을 부여하는 *역할*로 업무를 분리할 수 있습니다. 이러한 세 가지 RBAC 역할은 *소유자*, *DevTest Labs 사용자* 및 *참여자*입니다. 이 문서에서는 세 가지 주요 RBAC 역할에서 각각 수행할 수 있는 작업을 학습합니다. 여기서 포털 및 PowerShell 스크립트를 통해 랩에 사용자를 추가하는 방법과 구독 수준에서 사용자를 추가하는 방법을 설명합니다.

## 각 역할에서 수행할 수 있는 작업

사용자를 할당할 수 있는 세 가지 주요 역할이 있습니다.

- 소유자
- DevTest Lab 사용자
- 참여자

다음 표에는 이러한 각 역할의 사용자가 수행할 수 있는 작업에 대해 설명합니다.

| **이 역할의 사용자가 수행할 수 있는 작업** | **DevTest Lab 사용자** | **소유자** | **참여자** |
|---|---|---|---|
| **랩 작업** | | | |
| 랩에 사용자 추가 | 아니요 | 예 | 아니요 |
| 비용 설정 업데이트 | 아니요 | 예 | 예 |
| **VM 기본 작업** | | | |
| 사용자 지정 이미지 추가 및 제거 | 아니요 | 예 | 예 |
| 수식 추가, 업데이트 및 삭제 | 예 | 예 | 예 |
| Azure Marketplace 이미지를 허용 목록에 추가 | 아니요 | 예 | 예 |
| **VM 작업** | | | |
| VM 만들기 | 예 | 예 | 예 |
| VM 시작, 중지 및 삭제 | 사용자가 만든 VM만 | 예 | 예 |
| VM 정책 업데이트 | 아니요 | 예 | 예 |
| VM에 데이터 디스크 추가/VM에서 데이터 디스크 제거 | 사용자가 만든 VM만 | 예 | 예 |
| **아티팩트 작업** | | | |
| 아티팩트 리포지토리 추가 및 제거 | 아니요 | 예 | 예 |
| 아티팩트 적용 | 예 | 예 | 예 |

> [AZURE.NOTE] 사용자가 VM을 만들면 해당 사용자는 만든 VM의 **소유자** 역할에 자동으로 할당됩니다.

## 랩 수준에서 소유자 또는 사용자 추가

Azure Portal을 통해 랩 수준에서 소유자 및 사용자를 추가할 수 있습니다. 여기에는 유효한 [MSA(Microsoft 계정)](devtest-lab-faq.md#what-is-a-microsoft-account)를 가진 외부 사용자도 포함됩니다. 다음 단계는 Azure DevTest Labs에서 랩에 소유자 또는 사용자를 추가하는 과정을 안내합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **서비스 더 보기**를 선택한 후 목록에서 **DevTest Lab**을 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. 랩의 블레이드에서 **구성**을 선택합니다.

1. **구성** 블레이드에서 **사용자**를 선택합니다.

1. **사용자** 블레이드에서 **추가**를 선택합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 원하는 역할을 선택합니다. [각 역할에서 수행할 수 있는 작업](#actions-that-can-be-performed-in-each-role) 섹션에서 소유자, DevTest 사용자 및 참여자 역할의 사용자가 수행할 수 있는 다양한 작업을 나열합니다.

1. **사용자 추가** 블레이드에서 지정한 역할에 추가할 사용자의 메일 주소 또는 이름을 입력합니다. 사용자를 찾을 수 없는 경우 문제를 설명하는 오류 메시지가 표시됩니다. 사용자를 찾은 경우 해당 사용자가 나열되고 선택됩니다.

1. **선택**을 선택합니다.

1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

1. **사용자** 블레이드로 돌아가면 사용자가 추가되어 있습니다.

## PowerShell을 사용하여 랩에 외부 사용자 추가

Azure Portal에 사용자를 추가하는 것 외에도 PowerShell 스크립트를 사용하여 랩에 외부 사용자를 추가할 수 있습니다. 다음 예제에서 **변경할 값** 설명대로 매개 변수 값을 간단히 수정합니다. Azure Portal의 랩 블레이드에서 `subscriptionId`, `labResourceGroup` 및 `labName` 값을 검색할 수 있습니다.

> [AZURE.NOTE]
샘플 스크립트는 지정된 사용자가 Active Directory에 게스트로 추가된 것으로 가정하고 사례가 없는 경우 실패합니다. Active Directory에 없는 사용자를 랩에 추가하려면 [랩 수준에서 소유자 또는 사용자 추가](#add-an-owner-or-user-at-the-lab-level) 섹션에 설명된 대로 Azure Portal을 사용하여 사용자를 역할에 할당합니다.

	# Add an external user in DevTest Labs user role to a lab
	# Ensure that guest users can be added to the Azure Active directory:
	# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

	# Values to change
	$subscriptionId = "<Enter Azure subscription ID here>"
	$labResourceGroup = "<Enter lab's resource name here>"
	$labName = "<Enter lab name here>"
	$userDisplayName = "<Enter user's display name here>"

	# Log into your Azure account
	Login-AzureRmAccount
	
	# Select the Azure subscription that contains the lab. 
	# This step is optional if you have only one subscription.
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	# Retrieve the user object
	$adObject = Get-AzureRmADUser -SearchString $userDisplayName
	
	# Create the role assignment. 
	$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
	New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## 구독 수준에서 소유자 또는 사용자 추가

Azure 권한은 Azure의 부모 범위에서 자식 범위로 전파됩니다. 따라서 랩을 포함하는 Azure 구독 소유자는 자동으로 해당 랩의 소유자입니다. 랩의 사용자가 만든 다른 리소스와 VM 및 Azure DevTest Labs 서비스의 소유자이기도 합니다.

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩의 블레이드를 통해 랩에 소유자를 더 추가할 수 있습니다. 그러나 추가된 소유자의 관리 범위는 구독 소유자 범위보다 더 좁습니다. 예를 들어 추가된 소유자는 DevTest Labs 서비스에서 구독에 만든 일부 리소스에 대해서는 모든 권한이 없습니다.

Azure 구독에 소유자를 추가하려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **서비스 더 보기**를 선택한 다음 목록에서 **Subscriptions**를 선택합니다.

1. 원하는 구독을 선택합니다.

1. **액세스** 아이콘을 선택합니다.

	![사용자 액세스](./media/devtest-lab-add-devtest-user/access-users.png)

1. **사용자** 블레이드에서 **추가**를 선택합니다.

	![사용자 추가](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. **역할 선택** 블레이드에서 **소유자**를 탭합니다.

1. **사용자 추가** 블레이드에서 소유자로 추가할 사용자의 메일 주소 또는 이름을 입력합니다. 사용자를 찾을 수 없는 경우 문제를 설명하는 오류 메시지가 표시됩니다. 사용자를 찾은 경우 해당 사용자가 **사용자** 텍스트 상자 아래에 나열됩니다.

1. 찾은 사용자 이름을 탭합니다.

1. **선택**을 선택합니다.

1. **확인**을 탭하여 **액세스 추가** 블레이드를 닫습니다.

1. **사용자** 블레이드로 돌아가면 사용자가 소유자로 추가됩니다. 이제 이 사용자는 이 구독 아래에 만든 랩의 소유자이므로 소유자 작업을 수행할 수 있습니다.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<!---HONumber=AcomDC_0914_2016-->