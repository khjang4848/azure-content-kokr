<properties
pageTitle="Azure Active Directory 미리 보기에서 로그인 페이지에 언어별 회사 브랜딩 추가 | Microsoft Azure"
description="언어별 회사 브랜딩 그림 및 텍스트를 Azure에 로그인 페이지에 추가하는 방법에 대해 알아봅니다."
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

# Azure Active Directory 미리 보기에서 로그인 페이지에 언어별 회사 브랜딩 추가

혼동을 피하기 위해 대부분의 회사는 관리하는 모든 웹 사이트 및 서비스에 일관된 모양과 느낌을 적용하고자 합니다. Azure Active Directory 미리 보기는 회사 로고 및 사용자 지정 색 구성표를 포함하도록 로그인 페이지의 외관을 사용자 지정하는 방식으로 이 기능을 제공합니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 로그인 페이지는 Office 365 또는 Azure AD를 ID 공급자로 사용하는 기타 웹 기반 응용 프로그램에 로그인할 경우에 표시되는 페이지입니다. 자격 증명을 입력하려면 이 페이지와 상호 작용합니다.

## 다른 언어에 대한 로그인 페이지 사용자 지정

[로그인 페이지에 회사 브랜딩 추가](active-directory-branding-custom-signon-azure-portal.md)에 설명된 대로 사용자 지정 로그인 페이지를 이미 만든 경우 사용자 지정 로그인 페이지에 언어별 요소를 추가할 수 있습니다. 기본 사용자 지정 가능 요소 집합으로 디렉터리당 하나의 로그인 페이지를 구성할 수 있습니다. 기본 페이지 요소 집합을 구성한 후 다른 로캘로 추가 버전을 구성할 수 있습니다. 다양한 요소를 적절히 조합하여 사용할 수도 있습니다. 예를 들어 다음과 같이 할 수 있습니다.

- 모든 문화권에 적용되는 기본 **로그인 페이지 이미지**를 만든 다음 영어와 프랑스어에 대한 특정 버전을 만듭니다. 다른 모든 언어에 대해 기본 그림이 표시되는 반면 이 두 언어 중 하나로 브라우저를 설정하면 언어별 이미지가 나타납니다.

- 조직에 따라 다른 로고를 구성합니다(예: 일본어 또는 히브리어 버전).

관리 및 성능 이유로 다양한 언어 변형을 낮게 유지하는 것이 좋습니다.

**디렉터리에 회사 브랜딩을 추가하려면**

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.  **더 많은 서비스**를 선택하고 텍스트 상자에 **사용자 및 그룹**을 입력한 다음 **Enter**를 선택합니다.

    ![사용자 관리 열기](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. **사용자 및 그룹** 블레이드에서 **회사 브랜딩**을 선택합니다.

4. **사용자 및 그룹 - 회사 브랜딩** 블레이드에서 **언어 추가** 명령을 선택합니다.

    ![언어별 브랜딩 요소 추가](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. 사용자 지정할 요소를 수정합니다. 모든 요소는 선택 사항입니다.

6. **Save**를 클릭합니다.

로그인 페이지 브랜딩의 변경 내용을 보려면 최대 1시간이 걸릴 수 있습니다.

## 다음 단계

[로그인 페이지에 회사 브랜딩 추가](active-directory-branding-custom-signon-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->