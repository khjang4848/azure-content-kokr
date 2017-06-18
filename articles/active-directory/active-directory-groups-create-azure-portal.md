<properties
	pageTitle="Azure Active Directory 미리 보기에서 새 그룹 만들기 | Microsoft Azure"
	description="Azure Active Directory에서 그룹을 만들고 그룹에 사용자(멤버)를 추가하는 방법"
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


# Azure Active Directory 미리 보기에서 새 그룹 만들기

이 문서는 Azure Active Directory(Azure AD) 미리 보기에서 새 그룹을 만들고 채우는 방법을 설명합니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 그룹을 사용하여 한 번에 많은 사용자 또는 장치에 라이선스 또는 사용 권한을 할당하는 등 관리 작업을 수행합니다.

## 그룹을 만드는 방법

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

  ![사용자 관리 열기](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. **사용자 및 그룹** 블레이드에서 **모든 그룹**을 선택합니다.

  ![그룹 블레이드 열기](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. **사용자 및 그룹 - 모든 그룹** 블레이드에서 **추가** 명령을 선택합니다.

  ![추가 명령 선택](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. **그룹** 블레이드에서 그룹에 대한 이름 및 설명을 추가합니다.

6. 그룹에 추가할 멤버를 선택하려면 **멤버 자격 유형** 상자에서 **할당**을 선택한 다음 **멤버**를 선택합니다. 그룹의 멤버 자격을 동적으로 관리하는 방법에 대한 자세한 내용은 [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-users-azure-portal.md) 또는 [그룹의 장치에 대한 동적 규칙 관리](active-directory-groups-dynamic-devices-azure-portal.md)를 참조하세요.

  ![추가할 멤버 선택](./media/active-directory-groups-create-azure-portal/select-members.png)

5. **멤버** 블레이드에서 그룹에 추가할 하나 이상의 사용자 또는 장치를 선택하고 블레이드 아래쪽의 **선택** 단추를 선택하여 그룹에 추가합니다. **사용자** 상자는 사용자 또는 장치 이름 부분에 대한 항목 일치를 기반으로 한 표시를 필터링합니다. 와일드카드 문자는 해당 상자에서 허용되지 않습니다.

6. 그룹에 멤버 추가를 마치면 **그룹** 블레이드에서 **만들기**를 선택합니다.

  ![그룹 확인 만들기](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## 추가 정보

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 관리](active-directory-groups-members-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->