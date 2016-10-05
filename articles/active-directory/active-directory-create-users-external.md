<properties
	pageTitle="Azure Active Directory의 다른 디렉터리 또는 파트너 회사의 사용자 추가 | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="09/22/2016"
	ms.author="curtand"/>

# Azure Active Directory의 다른 디렉터리 또는 파트너 회사의 사용자 추가

> [AZURE.SELECTOR]
- [Azure 포털](active-directory-users-create-external-azure-portal.md)
- [Azure 클래식 포털](active-directory-create-users-external.md)

이 문서에서는 Azure Active Directory에 있는 다른 디렉터리의 사용자를 추가하거나 파트너 회사의 사용자를 추가하는 방법을 설명합니다. 조직 내에서 새 사용자 추가 및 Microsoft 계정이 있는 사용자 추가에 대한 자세한 내용은 [Azure Active Directory에 새 사용자 추가](active-directory-create-users.md)를 참조하세요. 기본적으로 추가된 사용자에게는 관리자 권한이 없지만 언제든 역할을 할당할 수 있습니다.

## 사용자 추가

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.

2. **Active Directory**를 선택한 다음 디렉터리를 엽니다.

3. **사용자** 탭을 선택한 다음 명령 모음에서 **사용자 추가**를 선택합니다.

4. **이 사용자에 대해 알리기** 페이지의 **사용자 형식**에서 다음 중 하나를 선택합니다.

	- **다른 Azure AD 디렉터리의 사용자** – 다른 Azure AD 디렉터리가 원본인 사용자 계정을 사용자 디렉터리에 추가합니다. 또한 사용자는 해당 디렉터리의 멤버인 경우에만 또 다른 디렉터리의 사용자를 선택할 수 있습니다.
	- **파트너 회사의 사용자** - 파트너 회사 사용자를 디렉터리에 초대하고 권한 부여([Azure Active Directory B2B 공동 작업 참조](active-directory-b2b-what-is-azure-ad-b2b.md)). [전자 메일 주소를 지정하는 CSV 파일을 업로드](active-directory-b2b-references-csv-file-format.md)해야 합니다.

6. 사용자 **프로필** 페이지에 이름과 성, 별명 및 **역할 목록**의 사용자 역할을 입력합니다. 사용자 및 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요. 사용자에 대해 **Multi-Factor Authentication 사용** 여부를 지정합니다.

7. **임시 암호 가져오기** 페이지에서 **만들기**를 선택합니다.

> [AZURE.IMPORTANT] 조직에서 둘 이상의 도메인을 사용하는 경우 사용자 계정을 추가할 때 다음과 같은 문제를 알고 있어야 합니다.
>
> - 도메인 간에 동일한 UPN(사용자 계정 이름)을 갖는 여러 개의 사용자 계정을 추가하려면 예를 들어 **먼저** geoffgrisso@contoso.onmicrosoft.com을 추가한 **다음** geoffgrisso@contoso.com을 추가합니다.
> - geoffgrisso@contoso.onmicrosoft.com을 추가하기 전에 geoffgrisso@contoso.com을 추가하지 **마세요**. 이 작업은 중요하며 실행을 취소하기가 복잡할 수 있습니다.

ID가 온-프레미스 Active Directory 서비스와 동기화된 사용자에 대한 정보를 변경하는 경우 Azure 클래식 포털에서 사용자 정보를 변경할 수 없습니다. 사용자 정보를 변경하려면 온-프레미스 Active Directory 관리 도구를 사용합니다.

## 외부 사용자 추가

또한 사용자가 속해 있는 Azure AD 디렉터리 또는 파트너 회사에서 CSV 파일을 업로드하여 사용자를 추가할 수 있습니다. 외부 사용자를 추가하려면 **사용자 형식**으로 **다른 Microsoft Azure AD 디렉터리의 사용자** 또는 **파트너 회사의 사용자**를 지정합니다.

두 형식의 사용자는 모두 다른 디렉터리가 원본이며 **외부 사용자**로 추가됩니다. 외부 사용자가 새 계정 및 자격 증명을 추가하지 않아도 디렉터리에 있는 다른 사용자와 공동 작업할 수 있습니다. 외부 사용자는 로그인 시 홈 디렉터리로 인증하며 해당 인증은 해당 사용자가 추가된 다른 모든 디렉터리에서 유효합니다.

## 외부 사용자 관리 및 제한 사항

다른 디렉터리의 사용자를 내 디렉터리에 추가하면 해당 사용자는 내 디렉터리에서 외부 사용자가 됩니다. 표시 이름 및 사용자 이름을 해당 홈 디렉터리에서 복사하고 내 디렉터리에서 외부 사용자로 사용합니다. 이후로 외부 사용자 계정의 속성을 완전히 독립적입니다. 해당 홈 디렉터리의 사용자에 대한 속성이 변경된 경우 해당 변경 내용은 내 디렉터리에 있는 외부 사용자 계정에 전파되지 않습니다.

두 계정 사이의 유일한 연계는 사용자가 항상 홈 디렉터리에 대해 인증을 받거나 자신의 Microsoft 계정으로 인증을 받는다는 것입니다. 이러한 이유로 암호를 다시 설정하거나 외부 사용자에 대해 Multi-Factor Authentication을 활성화하는 옵션이 표시되지 않습니다. 현재는 사용자가 로그인 할 때 홈 디렉터리 또는 Microsoft 계정의 인증 정책만 평가됩니다.

> [AZURE.NOTE]
디렉터리에서 외부 사용자가 여전히 비활성화될 수 있으며 이로 인해 디렉터리에 대한 액세스가 차단됩니다.

홈 디렉터리에서 사용자가 삭제되거나 사용자가 자신의 Microsoft 계정을 취소하더라도 해당 외부 사용자는 여전히 내 디렉터리에 존재합니다. 그러나 내 디렉터리의 사용자는 홈 디렉터리 또는 Microsoft 계정으로 인증할 수 없기 때문에 리소스에 액세스할 수 없게 됩니다.

### 현재 Azure AD 외부 사용자의 액세스를 지원하는 서비스

- **Azure 클래식 포털**: 여러 디렉터리의 관리자인 외부 사용자가 해당하는 각 디렉터리를 관리할 수 있습니다.
- **SharePoint Online**: 외부 공유가 활성화될 경우 SharePoint Online에서 권한을 부여한 리소스에 외부 사용자가 액세스할 수 있습니다.
- **Dynamics CRM**: PowerShell을 통해 라이선스를 부여할 경우 사용이 허가된 리소스에 외부 사용자가 액세스할 수 있습니다.
- **Dynamics AX**: PowerShell을 통해 라이선스를 부여할 경우 외부 사용자가 Dynamics AX의 권한이 부여된 리소스에 액세스할 수 있습니다. [Azure AD 외부 사용자](#known-limitations-of-azure-ad-external-users)에 대한 제한은 Dynamics AX의 외부 사용자에게도 적용합니다.

### Azure AD 외부 사용자의 알려진 제한 사항

- 관리자인 외부 사용자는 홈 디렉터리 외부에 있는 디렉터리(B2B 공동 작업)에 파트너 회사의 사용자를 추가할 수 없습니다.
- 외부 사용자가 홈 디렉터리 외부에 있는 디렉터리의 다중 테넌트 응용 프로그램에 동의할 수 없습니다.
- PowerBI는 현재 외부 사용자의 액세스를 지원하지 않습니다.
- Office 포털은 현재 외부 사용자에 대한 라이선싱을 지원하지 않습니다.
- Azure AD PowerShell과 관련하여 외부 사용자는 해당 홈 디렉터리에 로그인되며 외부 사용자인 경우 디렉터리를 관리할 수 없습니다.


## 다음 단계

- [Azure Active Directory에 새 사용자 추가](active-directory-create-users.md)
- [Azure AD 관리](active-directory-administer.md)
- [Azure AD에서 암호 관리](active-directory-manage-passwords.md)
- [Azure AD에서 그룹 관리](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0928_2016-->