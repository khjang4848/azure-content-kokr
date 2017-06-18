<properties
pageTitle="Azure Active Directory 미리 보기에서 로그인 페이지 사용자 지정 | Microsoft Azure"
description="회사 브랜딩을 Azure 로그인 페이지 에 추가하는 방법에 대해 알아봅니다."
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

# Azure Active Directory 미리 보기에서 로그인 페이지에 회사 브랜딩 추가

혼동을 피하기 위해 대부분의 회사는 관리하는 모든 웹 사이트 및 서비스에 일관된 모양과 느낌을 적용하고자 합니다. Azure Active Directory 미리 보기는 회사 로고 및 사용자 지정 색 구성표를 포함하도록 로그인 페이지의 외관을 사용자 지정하는 방식으로 이 기능을 제공합니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 로그인 페이지는 Office 365 또는 Azure AD를 ID 공급자로 사용하는 기타 웹 기반 응용 프로그램에 로그인할 경우에 표시되는 페이지입니다. 자격 증명을 입력하려면 이 페이지와 상호 작용합니다.

이 페이지에 회사 브랜드, 색 및 기타 사용자지정 가능한 요소를 표시하려면 다음 이미지를 참조하여 두 환경의 차이를 이해하세요.

다음 스크린샷은 사용자 지정을 하기 **전에** 데스크톱 컴퓨터에서 Office 365 로그인 페이지에 대한 예제를 보여 줍니다.

![사용자 지정하기 전 Office 365 로그인 페이지](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

다음 스크린샷은 사용자 지정 **후** 데스크톱 컴퓨터 Office 365 로그인 페이지의 예제를 보여 줍니다.

![사용자 지정한 후 Office 365 로그인 페이지](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## 로그인 페이지 사용자 지정

일반적으로 조직이 구독하는 클라우드 앱 및 서비스에 대해 브라우저 기반 액세스가 필요한 경우 로그인 페이지를 사용합니다.

로그인 페이지에 변경 내용을 적용할 경우 변경 내용을 표시하려면 최대 1시간이 걸릴 수 있습니다.

https://outlook.com/**contoso**.com 또는 https://mail.**contoso**.com와 같은 테넌트 특정 URL로 서비스를 이용할 경우 브랜드가 지정된 로그인 페이지만이 나타납니다.

테넌트 특정 URL이 아닌 다른 URL(예: https://mail.office365.com)로 서비스를 이용할 경우 브랜드가 지정되지 않은 로그인 페이지가 나타납니다. 이 경우에 사용자 ID를 입력하거나 사용자 타일을 선택하면 브랜딩이 나타납니다.

> [AZURE.NOTE]
>
- 도메인 이름은 브랜딩을 구성한 Azure 포털의 **도메인** 부분에서 "활성"으로 표시되어야 합니다. 자세한 내용은 [사용자 지정 도메인 이름 추가](active-directory-domains-add-azure-portal.md)를 참조하세요.
- 로그인 페이지 브랜딩은 Microsoft의 소비자 로그인 페이지에 적용되지 않습니다. Microsoft 계정으로 로그인한 사용자는 Azure AD에서 렌더링하는 브랜드가 지정된 사용자 타일 목록을 볼 수 있지만 조직의 브랜딩이 Microsoft 계정 로그인 페이지에 적용되지는 않습니다.

**디렉터리에 회사 브랜딩을 추가하려면**

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.  **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![사용자 관리 열기](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. **사용자 및 그룹** 블레이드에서 **회사 브랜딩**을 선택합니다.

4. **사용자 및 그룹 - 회사 브랜딩** 블레이드에서 **편집** 명령을 선택합니다.

    ![사용자 지정 브랜딩 편집](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. 사용자 지정할 요소를 수정합니다. 모든 요소는 선택 사항입니다.

6. **Save**를 클릭합니다.

로그인 페이지 브랜딩의 변경 내용을 보려면 최대 1시간이 걸릴 수 있습니다.

## 다음 단계

[언어별 회사 브랜딩 추가](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->