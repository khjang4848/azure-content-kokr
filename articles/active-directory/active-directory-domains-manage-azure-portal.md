<properties
	pageTitle="Azure Active Directory 미리 보기에서 사용자 지정 도메인 이름 관리 | Microsoft Azure"
	description="Azure Active Directory에서 도메인 이름 관리에 대한 관리 개념 및 방법"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory 미리 보기에서 사용자 지정 도메인 이름 관리

도메인 이름은 많은 디렉터리 리소스에 대한 식별자의 중요한 부분입니다. 사용자의 경우 사용자 이름 또는 메일 주소 부분이고 그룹의 경우 주소 부분이며 응용 프로그램의 경우 앱 ID URI 부분일 수 있습니다. Azure Active Directory(Azure AD) 미리 보기의 리소스는 리소스를 포함하는 디렉터리가 소유하는 이미 확인된 도메인 이름을 포함할 수 있습니다. [무엇이 미리 보기 상태인가요?](active-directory-preview-explainer.md) 전역 관리자만 Azure AD에서 도메인 관리 작업을 수행할 수 있습니다.

## Azure AD 디렉터리에 대한 주 도메인 이름 설정

디렉터리를 만든 경우 'contoso.onmicrosoft.com'과 같은 초기 도메인 이름은 주 도메인 이름이기도 합니다. 주 도메인은 새 사용자를 만들 때 새 사용자에 대한 기본 도메인 이름입니다. 관리자에 대한 프로세스를 간소화하여 포털에 새 사용자를 만듭니다. 주 도메인 이름을 변경하려면:

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.  **더 많은 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음 **Enter**를 선택합니다.

    ![사용자 관리 열기](./media/active-directory-domains-add-azure-portal/user-management.png)

3. ***디렉터리-이름*** 블레이드에서 **도메인 이름**을 선택합니다.

4. ***디렉터리 이름* - 도메인 이름** 블레이드에서 주 도메인 이름을 만들려는 도메인 이름을 선택합니다.

5.  ***domainname*** 블레이드(즉, 제목에 새 도메인 이름이 있는 것을 여는 블레이드)에서 **Make primary**를 선택합니다. 메시지가 표시되면 선택을 확인합니다.

    ![주 도메인 이름 만들기](./media/active-directory-domains-manage-azure-portal/make-primary.png)

페더레이션되지 않은 모든 확인된 사용자 지정 도메인이 되도록 디렉터리에 대한 주 도메인 이름을 변경할 수 있습니다. 디렉터리에 대한 주 도메인을 변경해도 기존 사용자에 대한 사용자 이름은 변경되지 않습니다.

## Azure AD에 사용자 지정 도메인 이름 추가

각 Azure AD 디렉터리에 최대 900개의 사용자 지정 도메인 이름을 추가할 수 있습니다. [추가 사용자 지정 도메인 이름을 추가](active-directory-domains-add-azure-portal.md)하는 프로세스는 첫 번째 사용자 지정 도메인 이름에 대해 동일합니다.

## 사용자 지정 도메인의 하위 도메인 추가

디렉터리에 'europe.contoso.com'과 같은 세 번째 수준 도메인 이름을 추가하려면 먼저 contoso.com과 같은 두 번째 수준 도메인을 추가 및 확인해야 합니다. Azure AD에서 자동으로 하위 도메인을 확인합니다. 방금 추가한 하위 도메인이 확인되었는지 보려면 도메인을 나열하는 브라우저의 페이지를 새로 고칩니다.

## 사용자 지정 도메인 이름의 DNS 등록 기관을 변경하는 경우 수행할 작업

사용자 지정 도메인 이름을 위해 DNS 등록 기관을 변경할 경우 추가 구성 작업 및 중단 없이 Azure AD 자체로 사용자 지정 도메인 이름을 계속 사용할 수 있습니다. Office 365, Intune 또는 Azure AD의 사용자 지정 도메인 이름을 사용하는 다른 서비스에서 사용자 지정 도메인 이름을 사용하는 경우 해당 서비스에 대한 설명서를 참조하세요.

## 사용자 지정 도메인 이름 삭제

조직이 더 이상 해당 도메인 이름을 사용하지 않는 경우 또는 다른 Azure AD와 해당 도메인 이름을 사용해야 하는 경우, Azure AD에서 사용자 지정 도메인 이름을 삭제할 수 있습니다.

사용자 지정 도메인 이름을 삭제하려면 먼저 디렉터리에 도메인 이름을 사용하는 리소스가 없는지 확인해야 합니다. 다음의 경우 디렉터리에서 도메인 이름을 삭제할 수 없습니다.

-   임의 사용자가 도메인 이름을 포함하는 사용자 이름, 메일 주소 또는 프록시 주소 사용

-   임의 그룹이 도메인 이름을 포함하는 메일 주소 또는 프록시 주소 사용

-   Azure AD의 임의 응용 프로그램이 도메인 이름을 포함하는 앱 ID URI 사용

사용자 지정 도메인 이름을 삭제하려면 먼저 Azure AD 디렉터리에서 이러한 리소스를 변경 또는 삭제해야 합니다.

## PowerShell 또는 Graph API를 사용하여 도메인 이름 관리

Azure Active Directory의 도메인 이름에 대한 대부분의 관리 작업은 Microsoft PowerShell을 사용하거나 프로그래밍 방식으로 공개 미리 보기에서 Azure AD Graph API를 사용하여 완료할 수도 있습니다.

-   [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Graph API를 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## 다음 단계

-   [사용자 지정 도메인 이름 추가](active-directory-domains-add-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->