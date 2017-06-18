<properties
	pageTitle="Azure Active Directory 미리 보기에 새 사용자 추가 | Microsoft Azure"
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
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Azure Active Directory 미리 보기에 새 사용자 추가

> [AZURE.SELECTOR]
- [Azure 포털](active-directory-users-create-azure-portal.md)
- [Azure 클래식 포털](active-directory-create-users.md)

이 문서에서는 Azure Active Direstory(Azure AD) 미리 보기에서 조직에 새 사용자를 추가하는 방법을 설명합니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md)

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.  **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![사용자 관리 열기](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  **사용자 및 그룹** 블레이드에서 **모든 사용자**를 선택한 다음 **추가**를 선택합니다.

    ![추가 명령 선택](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  **이름** 및 **사용자 이름**과 같은 사용자에 대한 세부 정보를 입력합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "foo.onmicrosoft.com" 도메인 이름 또는 "contoso.com"과 같은 확인된 페더레이션되지 않은 도메인 이름이어야 합니다.

5. 이 프로세스가 완료된 후 사용자에게 제공할 수 있도록 복사하거나 그렇지 않은 경우 생성된 사용자 암호를 기록합니다.

6. 필요에 따라 **프로필** 블레이드, **그룹** 블레이드 또는 사용자에 대한 **디렉터리 역할** 블레이드의 정보를 열고 입력할 수 있습니다. 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

7.  **사용자** 블레이드에서 **만들기**를 선택합니다.

8. 사용자가 로그인할 수 있도록 새 사용자에게 생성된 암호를 안전하게 배포합니다.

## 다음 단계

- [외부 사용자 추가](active-directory-users-create-external-azure-portal.md)
- [새 Azure 포털에서 사용자의 암호 재설정](active-directory-users-reset-password-azure-portal.md)
- [사용자의 작업 정보 변경](active-directory-users-work-info-azure-portal.md)
- [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
- [Azure AD에서 사용자 삭제](active-directory-users-delete-user-azure-portal.md)
- [Azure AD의 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->