<properties
   pageTitle="액세스 검토를 수행하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 응용 프로그램을 사용하여 검토를 수행하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management의 액세스 검토를 수행하는 방법

Azure AD(Active Directory) Privileged Identity Management를 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.

관리 역할에 할당된 경우 조직의 권한 있는 역할 관리자가 작업에 해당 역할이 여전히 필요한지 정기적으로 확인하도록 요청할 수 있습니다. 링크가 포함된 메일을 받거나 [Azure 포털](https://portal.azure.com)로 바로 이동할 수 있습니다. 할당된 역할을 자체 검토하려면 이 문서의 단계를 따릅니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자인 경우 [액세스 검토를 시작하는 방법](active-directory-privileged-identity-management-how-to-start-security-review.md)에서 자세한 내용을 참조하세요.

## Privileged Identity Management 응용 프로그램 추가

[Azure 포털](https://portal.azure.com/)에서 Azure AD PIM(Privileged Identity Management) 응용 프로그램을 사용하여 검토를 수행할 수 있습니다. Azure AD Privileged Identity Management 응용 프로그램이 포털에 없는 경우 다음 단계에 따라 시작합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure 포털의 오른쪽 위에서 사용자 이름을 선택하고 작동할 디렉터리를 선택합니다.
3. **더 많은 서비스**를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management**를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.


## 액세스 승인 또는 거부

액세스를 승인하거나 거부할 때, 단지 검토자에게 이 역할을 사용할지를 알리는 것입니다. 역할을 유지하려면 **승인**을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부**를 선택합니다. 검토자가 결과를 적용할 때까지는 사용자의 상태가 바로 변경되지 않습니다. 액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. PIM 응용 프로그램에서 **권한 있는 액세스 검토**를 선택합니다. 보류 중인 액세스 검토가 있다면 Azure AD 액세스 검토 블레이드에 표시 됩니다.
2. 완료할 검토를 선택합니다.
3. 검토를 만들지 않은 한, 검토에서 유일한 사용자로 나타납니다. 사용자 이름 옆에 있는 확인 표시를 선택합니다.
4. **승인** 또는 **거부**를 선택합니다. **이유 설명** 텍스트 상자에 결정 이유를 포함해야 합니다.
5. **Review Azure AD roles**(Azure AD 역할 검토)를 닫습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

<!---HONumber=AcomDC_0921_2016-->