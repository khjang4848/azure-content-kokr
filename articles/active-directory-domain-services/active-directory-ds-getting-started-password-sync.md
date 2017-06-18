<properties
	pageTitle="Azure AD 도메인 서비스: 암호 동기화 활성화 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스에 대한 암호 동기화 활성화
앞의 태스크에서 Azure AD 테넌트에 대한 Azure AD 도메인 서비스를 활성화했습니다. 다음 태스크는 NTLM 및 Kerberos 인증에 필요한 자격 증명 해시를 사용하도록 설정하여 Azure AD 도메인 서비스를 동기화하는 것입니다. 자격 증명 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다.

관련된 단계는 조직에 클라우드 전용 Azure AD 테넌트가 있는지, 아니면 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정되었는지에 따라 다릅니다.

<br>

> [AZURE.SELECTOR]
- [클라우드 전용 Azure AD 테넌트](active-directory-ds-getting-started-password-sync.md)
- [동기화된 Azure AD 테넌트](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## 작업 5: 클라우드 전용 Azure AD 테넌트에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화
관리되는 도메인에 대한 사용자를 인증하려면 Azure AD 도메인 서비스에는 NTLM 및 Kerberos 인증에 적합한 형식으로 해시 자격 증명이 필요합니다. 테넌트에 AAD 도메인 서비스를 사용하지 않으면 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식으로 자격 증명 해시를 생성하거나 저장하지 않습니다. 확실한 보안을 위해 Azure AD는 일반 텍스트 형식으로 자격 증명을 저장하지 않습니다. 따라서 Azure AD를 통해 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 자격 증명 해시를 생성할 수 없습니다.

> [AZURE.NOTE] 조직에 클라우드 전용 Azure AD 테넌트가 있는 경우 Azure AD 도메인 서비스를 사용해야 하는 사용자는 암호를 변경해야 합니다.

암호 변경 프로세스를 수행하면 Kerberos 및 NTLM 인증을 위해 Azure AD 도메인 서비스에 필요한 자격 증명 해시가 Azure AD에서 생성됩니다. Azure AD 도메인 서비스를 사용해야 하는 테넌트의 모든 사용자에 대한 암호를 만료시키거나, 암호를 변경하도록 이러한 사용자에게 지시할 수 있습니다.


### 클라우드 전용 Azure AD 테넌트에 대해 NTLM 및 Kerberos 자격 증명 해시 생성 활성화
다음과 같은 최종 사용자에게 제공해야 하는 지침을 따라 암호를 변경할 수 있습니다.

1. [http://myapps.microsoft.com](http://myapps.microsoft.com)에서 조직의 Azure AD 액세스 패널 페이지로 이동합니다.

2. 이 페이지에서 **프로필** 탭을 선택합니다.

3. 이 페이지에서 **암호 변경** 타일을 클릭합니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] 액세스 패널 페이지에서 **암호 변경** 옵션이 표시되지 않으면 조직이 [Azure AD에서 암호 관리](../active-directory/active-directory-passwords-getting-started.md)를 구성하도록 합니다.

4. **암호 변경** 페이지에서 이전 암호를 입력하고 새 암호를 입력한 다음 확인합니다. **제출**을 클릭합니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

암호를 변경한 후에 새 암호를 즉시 Azure AD 도메인 서비스에 사용할 수 있습니다. 몇 분(일반적으로 약 20분) 후에 새로 변경한 암호를 사용하여 관리되는 도메인에 가입된 컴퓨터에 로그인할 수 있습니다.

<br>

## 관련 콘텐츠

- [고유한 암호를 업데이트하는 방법](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Azure AD에서 암호 관리 시작](../active-directory/active-directory-passwords-getting-started.md)

- [동기화된 Azure AD 테넌트에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)

- [Windows 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)

- [Red Hat Enterprise Linux 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!-----HONumber=AcomDC_0921_2016-->