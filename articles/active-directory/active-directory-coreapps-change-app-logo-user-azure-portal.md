<properties
	pageTitle="Azure Active Directory 미리 보기에서 엔터프라이즈 앱의 이름 또는 로고 변경 | Microsoft Azure"
	description="Azure Active Directory에서 사용자 지정 엔터프라이즈 앱에 대한 이름 또는 로고를 변경하는 방법"
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

# Azure Active Directory 미리 보기에서 엔터프라이즈 앱의 이름 또는 로고 변경

Azure Active Directory(Azure AD) 미리 보기에서 사용자 정의 엔터프라이즈 응용 프로그램에 대한 이름 또는 로고를 변경하는 것은 쉽습니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 이렇게 변경하려면 적절한 권한이 있어야 합니다. 현재 미리 보기에서 사용자 지정 앱의 작성자이어야 합니다.

## 엔터프라이즈 앱의 이름 또는 로고를 변경하려면 어떻게 해야 합니까?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **더 많은 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음 **Enter**를 선택합니다.

3. **Azure Active Directory - *directoryname*** 블레이드(즉, 관리 중인 디렉터리에 대한 Azure AD 블레이드)에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

	![엔터프라이즈 앱 열기](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)

4. **엔터프라이즈 응용 프로그램** 블레이드에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.

5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 블레이드에서 앱을 선택합니다.

6. ***appname*** 블레이드(즉, 제목에서 선택된 앱의 이름을 가진 블레이드)에서 **속성**을 선택합니다.

	![속성 명령 선택](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)

7. ***appname*** **-속성** 블레이드에서 새 로고로 사용하거나 앱 이름 또는 둘 다를 편집할 파일을 찾습니다.

	![앱 로고 또는 nameproperties 명령 변경](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)

8. **저장** 명령을 선택합니다.

## 다음 단계

- [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
- [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)
- [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](active-directory-coreapps-disable-app-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->