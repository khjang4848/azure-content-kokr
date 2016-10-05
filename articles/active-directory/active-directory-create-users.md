<properties
	pageTitle="Azure Active Directory에 새 사용자 추가 | Microsoft Azure"
	description="Azure Active Directory에서 새 사용자를 추가하거나 사용자 정보를 변경하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="curtand"/>

# Azure Active Directory에 새 사용자 또는 Microsoft 계정이 있는 사용자 추가

사용자를 추가하여 디렉터리를 채웁니다. 이 문서에서는 조직 내에서 새 사용자를 추가하는 방법 및 Microsoft 계정이 있는 사용자를 추가하는 방법을 설명합니다. Azure Active Directory의 다른 디렉터리의 사용자 추가 또는 파트너 회사의 사용자 추가에 대한 자세한 내용은 [Azure Active Directory의 다른 디렉터리 또는 파트너 회사의 사용자 추가](active-directory-create-users-external.md)를 참조하세요. 기본적으로 추가된 사용자에게는 관리자 권한이 없지만 언제든 역할을 할당할 수 있습니다.

## 사용자 추가

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **Active Directory**를 선택한 다음 조직 디렉터리의 이름을 선택합니다.
3. **사용자** 탭을 선택한 다음 명령 모음에서 **사용자 추가**를 선택합니다.
4. **이 사용자에 대해 알리기** 페이지의 **사용자 형식**에서 다음 중 하나를 선택합니다.

	- **조직의 새 사용자** – 디렉터리에 새 사용자 계정을 추가합니다.
	- **기존 Microsoft 계정이 있는 사용자** – 디렉터리에 기존 사용자 Microsoft 고객 계정(예: Outlook 계정)을 추가합니다.

5. **사용자 유형**에 따라 사용자 이름(새 사용자) 또는 전자 메일 주소(Microsoft 계정이 있는 사용자)를 입력합니다.
6. 사용자 **프로필** 페이지에 이름과 성, 별명 및 **역할 목록**의 사용자 역할을 입력합니다. 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요. 사용자에 대해 **Multi-Factor Authentication 사용** 여부를 지정합니다.
7. **임시 암호 가져오기** 페이지에서 **만들기**를 선택합니다.

> [AZURE.IMPORTANT] 조직에서 둘 이상의 도메인을 사용하는 경우 사용자 계정을 추가할 때 다음과 같은 문제를 알고 있어야 합니다.
>
> - 도메인 간에 동일한 UPN(사용자 계정 이름)을 갖는 여러 개의 사용자 계정을 추가하려면 예를 들어 **먼저** geoffgrisso@contoso.onmicrosoft.com을 추가한 **다음** geoffgrisso@contoso.com을 추가합니다.
> - geoffgrisso@contoso.onmicrosoft.com을 추가하기 전에 geoffgrisso@contoso.com을 추가하지 **마세요**. 이 작업은 중요하며 실행을 취소하기가 복잡할 수 있습니다.

## 사용자 정보 변경

개체 ID를 제외하고 모든 사용자 특성을 변경할 수 있습니다.

1. 디렉터리를 엽니다.
2. **사용자** 탭을 선택하고 변경하려는 사용자의 표시 이름을 선택합니다.
3. 변경을 완료하고 **저장**을 클릭합니다.

변경하려는 사용자가 온-프레미스 Active Directory 서비스와 동기화된 경우 이 절차를 사용하여 사용자 정보를 변경할 수 없습니다. 사용자를 변경하려면 온-프레미스 Active Directory 관리 도구를 사용합니다.

## 게스트 사용자 관리 및 제한 사항

게스트 계정은 SharePoint 문서, 응용 프로그램 또는 기타 Azure 리소스에 액세스할 수 있도록 내 디렉터리에 초대된 다른 디렉터리의 사용자를 말합니다. 디렉터리의 게스트 계정은 기본 UserType 특성이 "게스트"로 설정됩니다. 일반 사용자(즉, 내 디렉터리의 멤버)는 UserType 특성이 "멤버"입니다.

게스트는 디렉터리에서 제한된 권한 집합을 갖습니다. 이러한 권한은 디렉터리의 다른 사용자에 대한 정보를 검색하는 게스트의 기능을 제한합니다. 그러나 게스트 사용자가 작업하는 리소스와 관련된 사용자 및 그룹과 계속 상호 작용할 수 있습니다. 게스트 사용자는 다음 작업을 수행할 수 있습니다.

- 할당된 Azure 구독과 관련된 다른 사용자 및 그룹 보기
- 속한 그룹의 멤버 보기
- 사용자의 전체 메일 주소를 알고 있는 경우 디렉터리의 다른 사용자 조회
- 조회하는 사용자의 제한된 특성 보기 - 표시 이름, 메일 주소, UPN(사용자 계정 이름) 및 축소한 사진으로 제한됨
- 디렉터리의 확인된 도메인 목록 가져오기
- 디렉터리의 멤버와 동일한 액세스 권한을 부여하며 응용 프로그램에 동의

## 게스트 사용자 액세스 정책 설정

디렉터리의 **구성** 탭에는 게스트 사용자의 액세스를 제어하는 옵션이 포함되어 있습니다. 이러한 옵션은 디렉터리 전역 관리자에 의해 Azure 클래식 포털에서만 변경될 수 있습니다. 현재는 PowerShell 또는 API 메서드가 없습니다.

Azure 클래식 포털에서 **구성** 탭을 열려면 **Active Directory**를 선택한 다음 디렉터리의 이름을 선택합니다.

![Azure Active Directory의 구성 탭][1]

그러면 게스트 사용자의 액세스를 제어하는 옵션을 편집할 수 있습니다.

![게스트 사용자에 대한 액세스 제어 옵션][2]


## 다음 단계

- [Azure Active Directory의 다른 디렉터리 또는 파트너 회사의 사용자 추가](active-directory-create-users-external.md)
- [Azure AD 관리](active-directory-administer.md)
- [Azure AD에서 암호 관리](active-directory-manage-passwords.md)
- [Azure AD에서 그룹 관리](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0928_2016-->