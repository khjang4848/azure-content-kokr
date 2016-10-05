<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure "
	description="Azure AD Privileged Identity Management가 무엇이고 클라우드 보안을 개선하기 위한 PIM 사용 방법을 설명하는 항목입니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Azure Active Directory(AD) Privileged Identity Management를 사용하여 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. Azure AD의 리소스 및 Office 365 또는 Microsoft Intune과 같은 다른 Microsoft 온라인 서비스에 대한 액세스를 포함합니다.

> [AZURE.NOTE] Privileged Identity Management는 Azure Active Directory의 Premium P2 버전에만 사용할 수 있습니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

조직에서는 악의적인 사용자가 해당 액세스 권한을 갖게 될 가능성을 줄일 수 있기 때문에 보안 정보 또는 리소스에 액세스하는 사용자 수를 최소화하려고 합니다. 하지만, 사용자는 여전히 Azure, Office 365 또는 SaaS 앱에서 권한 있는 작업을 수행해야 합니다. 조직은 사용자에게 Azure AD에서 권한 있는 액세스를 제공하며 사용자가 자신의 관리자 권한을 가지고 무엇을 하는지 모니터링하지 않습니다. Azure AD Privileged Identity Management는 이 위험을 해결하는 데 도움이 됩니다.

Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

- Azure AD 관리자인 사용자를 확인할 수 있습니다.
- Office 365 및 Intune 등의 Microsoft Online Services에 대해 주문형으로 “Just-In-Time”에 관리 권한을 사용하도록 설정할 수 있습니다.
- 관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서 가져오기
- 권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

Azure AD Privileged Identity Management는 다음을 포함하여 기본 제공된 Azure AD 조직 역할을 관리할 수 있습니다.

- 전역 관리자
- 대금 청구 관리자
- 서비스 관리자
- 사용자 관리자
- 암호 관리자

## 시간에 맞추는 관리자 액세스

지금까지 Azure 클래식 포털 또는 Windows PowerShell을 통해 사용자를 관리자 역할에 할당할 수 있었습니다. 그 결과, 해당 사용자는 **영구 관리자**가 되어 할당된 역할에 항상 활성 상태가 됩니다. Azure AD Privileged Identity Management는 **적격인 관리자** 개념을 소개합니다. 적격인 관리자는 매일은 아니지만 때때로 권한 있는 액세스가 필요한 사용자여야 합니다. 역할은 사용자가 액세스가 필요할 때까지 비활성으로 있다가, 활성화 프로세스를 완료하고 미리 정해진 시간 동안 활성 관리자가 됩니다.

## 디렉터리에서 Privileged Identity Management 사용

[Azure 포털](https://portal.azure.com/)에서 Azure AD Privileged Identity Management 사용을 시작할 수 있습니다.

>[AZURE.NOTE] 디렉터리에 대해 Azure AD Privileged Identity Management를 사용하려면 Microsoft 계정(예: @outlook.com)이 아닌 조직 계정(예: @yourdomain.com)의 전역 관리자여야 합니다.

1. 해당 디렉터리의 전역 관리자로 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 선택합니다. Azure AD Privileged Identity Management를 사용할 디렉터리를 선택합니다.
3. **더 많은 서비스**를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management**를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.

디렉터리에서 Azure AD Privileged Identity Management를 처음 사용하는 사용자이면 [보안 마법사](active-directory-privileged-identity-management-security-wizard.md)가 초기 할당 환경을 안내합니다. 이 작업 이후 자동으로 디렉터리의 첫 번째 **보안 관리자** 및 **권한 있는 역할 관리자**가 됩니다.

권한 있는 역할 관리자만 다른 관리자의 액세스 권한을 관리할 수 있습니다. [PIM 관리 기능을 다른 사용자에게 제공](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)할 수 있습니다.

## Privileged Identity Management 대시보드

Azure AD 권한 있는 ID 관리자는 다음과 같은 중요한 정보를 전달하는 대시보드를 제공합니다.

- 보안을 향상시킬 기회를 알려주는 경고
- 각 권한 있는 역할에 할당된 사용자 수
- 적격 및 영구 관리자 수
- 지속적인 액세스 검토

![PIM 대시보드 - 스크린샷][2]

## 권한 있는 역할 관리

Azure AD Privileged Identity Management로 각 역할에 영구 또는 적격 관리자를 추가하거나 제거하여 관리자를 관리할 수 있습니다.

![PIM 추가/제거 관리자 - 스크린샷][3]

## 역할 활성화 설정 구성

[역할 설정](active-directory-privileged-identity-management-how-to-change-default-settings.md)을 사용하여 다음을 포함한 적격 역할 활성화 속성을 구성할 수 있습니다.

- 역할 활성화 기간
- 역할 활성화 알림
- 역할 활성화 프로세스 중 사용자가 제공해야 하는 정보

![PIM 설정 - 관리자 활성화 - 스크린샷][4]

이미지에서 **Multi-Factor Authentication**에 대한 단추가 비활성입니다. 높은 권한이 필요한 특정 역할에 대해 우리는 강화된 보호를 위해 MFA가 필요합니다.

## 역할 활성화  

[역할을 활성화](active-directory-privileged-identity-management-how-to-activate-role.md)하기 위해 적격 관리자는 역할에 대해 시간 제한이 있는 "활성화"를 요청합니다. Azure AD Privileged Identity Management에서 **내 역할 활성화** 옵션을 사용하여 활성화를 요청할 수 있습니다.

역할을 활성화하려는 관리자는 Azure 포털에서 Azure AD Privileged Identity Management를 초기화해야 합니다.

역할 활성화는 사용자 지정할 수 있습니다. PIM 설정에서 활성화 길이 및 역할을 활성화하기 위해 관리자가 제공해야 하는 정보를 결정할 수 있습니다.

![PIM 관리자 요청 역할 활성화 - 스크린샷][5]

## 역할 작업 검토

직원 및 관리자가 권한 있는 역할을 사용하는 방법을 추적하는 방법은 두 가지가 있습니다. 첫 번째 옵션은 [감사 기록](active-directory-privileged-identity-management-how-to-use-audit-log.md)을 사용하는 것입니다. 감사 기록 로그는 권한 있는 역할 할당 및 역할 활성화 기록의 변경 내용을 추적합니다.

![PIM 활성화 기록 - 스크린샷][6]

두 번째 옵션은 정기적인 [액세스 검토](active-directory-privileged-identity-management-how-to-start-security-review.md)를 설정하는 것입니다. 이러한 액세스 검토는 할당된 검토자(예: 팀 관리자) 또는 자체적으로 검토할 수 있는 직원에 의해 수행될 수 있습니다. 사용자의 액세스 필요 여부를 모니터링하는 가장 좋은 방법입니다.


## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0921_2016-->