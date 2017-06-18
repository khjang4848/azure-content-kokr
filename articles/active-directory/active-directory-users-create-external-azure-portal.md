<properties
	pageTitle="Azure Active Directory 미리 보기의 다른 디렉터리 또는 파트너 회사에서 사용자 추가 | Microsoft Azure"
	description="외부 및 게스트 사용자를 포함하여 Azure Active Directory에서 사용자를 추가하거나 사용자 정보를 변경하는 방법을 설명합니다."
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
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Azure Active Directory 미리 보기의 다른 디렉터리 또는 파트너 회사에서 사용자 추가

> [AZURE.SELECTOR]
- [Azure 포털](active-directory-users-create-external-azure-portal.md)
- [Azure 클래식 포털](active-directory-create-users-external.md)

이 문서에서는 Azure Active Directory(Azure AD) 미리 보기의 다른 디렉터리 또는 파트너 회사에서 사용자를 추가하는 방법을 설명합니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 조직 내에서 새 사용자 추가 및 Microsoft 계정이 있는 사용자 추가에 대한 자세한 내용은 [Azure Active Directory에 새 사용자 추가](active-directory-users-create-azure-portal.md)를 참조하세요. 기본적으로 추가된 사용자에게는 관리자 권한이 없지만 언제든 역할을 할당할 수 있습니다.

## 사용자 추가

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.  **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![사용자 관리 열기](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  **사용자 및 그룹** 블레이드에서 **사용자**를 선택한 다음 **추가**를 선택합니다.

    ![추가 명령 선택](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. **사용자** 블레이드에서 **이름**에 표시 이름을 제공하고 **사용자 이름**에 사용자의 로그인 이름을 제공합니다.

5. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.

6. 필요에 따라 **프로필**을 선택하여 사용자 이름, 성, 직위, 부서 이름을 추가합니다.

	![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- **그룹**을 선택하여 하나 이상의 그룹에 사용자를 추가합니다.

		![그룹에 사용자 추가](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- **조직 역할**을 선택하여 **역할** 목록의 역할에 사용자를 할당합니다. 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

		![역할에 사용자 할당](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. **만들기**를 선택합니다.

8. 사용자가 로그인할 수 있도록 새 사용자에게 생성된 암호를 안전하게 배포합니다.

> [AZURE.IMPORTANT] 조직에서 둘 이상의 도메인을 사용하는 경우 사용자 계정을 추가할 때 다음과 같은 문제를 알고 있어야 합니다.
>
> - 도메인 간에 동일한 UPN(사용자 계정 이름)을 갖는 여러 개의 사용자 계정을 추가하려면 예를 들어 **먼저** geoffgrisso@contoso.onmicrosoft.com을 추가한 **다음** geoffgrisso@contoso.com을 추가합니다.
> - geoffgrisso@contoso.onmicrosoft.com을 추가하기 전에 geoffgrisso@contoso.com을 추가하지 **마세요**. 이 작업은 중요하며 실행을 취소하기가 복잡할 수 있습니다.

ID가 온-프레미스 Active Directory 서비스와 동기화된 사용자에 대한 정보를 변경하는 경우 Azure 클래식 포털에서 사용자 정보를 변경할 수 없습니다. 사용자 정보를 변경하려면 온-프레미스 Active Directory 관리 도구를 사용합니다.


## 다음 단계

- [사용자 추가](active-directory-users-create-azure-portal.md)
- [새 Azure 포털에서 사용자의 암호 재설정](active-directory-users-reset-password-azure-portal.md)
- [Azure AD의 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)
- [사용자의 작업 정보 변경](active-directory-users-work-info-azure-portal.md)
- [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
- [Azure AD에서 사용자 삭제](active-directory-users-delete-user-azure-portal.md)

<!----HONumber=AcomDC_0914_2016-->