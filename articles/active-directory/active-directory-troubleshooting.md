<properties
   pageTitle="문제 해결: 'Active Directory' 항목이 없거나 사용할 수 없음 | Microsoft Azure "
   description="Azure 관리 포털에 Active Directory 메뉴 항목이 표시되지 않을 경우 수행할 작업입니다."
   services="active-directory"
   documentationCenter="na"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# 문제 해결: 'Active Directory' 항목이 없거나 사용할 수 없음

Azure Active Directory 기능 및 서비스 사용에 대한 지침은 대부분 "Azure 관리 포털로 이동한 다음 **Active Directory**를 클릭합니다."로 시작합니다. 하지만 Active Directory 확장 또는 메뉴 항목이 표시되지 않거나 **사용할 수 없음**으로 표시되는 경우 어떻게 해야 하나요? 이 항목은 도움을 주기 위해 작성되었습니다. **Active Directory**가 표시되지 않거나 사용할 수 없는 조건 및 계속 진행하는 방법을 설명합니다.

## Active Directory가 없음

일반적으로 **Active Directory** 항목은 왼쪽 탐색 메뉴에 나타납니다. Azure Active Directory 절차의 지침에서는 이 항목이 표시된다고 가정합니다.

![스크린샷: Azure의 Active Directory](./media/active-directory-troubleshooting/typical-view.png)

다음 조건 중 하나라도 true이면 Active Directory 항목이 왼쪽 탐색 메뉴에 나타납니다. 그렇지 않으면 항목이 표시되지 않습니다.

* 현재 사용자가 Microsoft 계정(이전의 Windows Live ID)을 사용하여 로그온했습니다.

    또는

* Azure 테넌트에 디렉터리가 있고 현재 계정이 디렉터리 관리자입니다.

    또는

* Azure 테넌트에 ACS(Azure AD 액세스 제어) 네임스페이스가 하나 이상 있습니다. 자세한 내용은 [액세스 제어 네임스페이스](https://msdn.microsoft.com/library/azure/gg185908.aspx)를 참조하세요.

    또는

* Azure 테넌트에 Azure Multi-Factor Authentication 공급자가 하나 이상 있습니다. 자세한 내용은 [Azure Multi-Factor Authentication 공급자 관리](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)를 참조하세요.

액세스 제어 네임스페이스 또는 Multi-Factor Authentication 공급자를 만들려면 **+새로 만들기** > **앱 서비스** > **Active Directory**를 클릭합니다.

디렉터리에 대한 관리자 권한을 얻으려면 관리자가 계정에 관리자 역할을 할당하도록 합니다. 자세한 내용은 [관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

## Active Directory를 사용할 수 없음

**+새로 만들기** > **앱 서비스**를 클릭하면 **Active Directory** 항목이 나타납니다. 특히, Active Directory 항목은 현재 사용자가 디렉터리, 액세스 제어 또는 Multi-Factor Authentication 공급자와 같은 Active Directory 기능을 모두 사용할 수 있을 때 나타납니다.

그러나 페이지가 로드되는 동안에는 항목이 흐리게 표시되며 **사용할 수 없음**으로 표시됩니다. 이는 임시 상태입니다. 몇 초간 기다리면 항목을 사용할 수 있습니다. 지연이 길어질 경우 웹 페이지를 새로 고치면 문제가 해결되는 경우가 많습니다.

![스크린샷: Active Directory를 사용할 수 없음](./media/active-directory-troubleshooting/not-available.png)

<!---HONumber=AcomDC_0921_2016-->