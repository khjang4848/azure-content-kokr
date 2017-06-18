<properties
	pageTitle="Microsoft Azure Multi-Factor Auth 공급자 시작"
	description="Azure Multi-Factor Auth 공급자를 만드는 방법에 대해 알아봅니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>



# Azure Multi-Factor Auth 공급자 시작
Multi-Factor Authentication은 기본적으로 Azure Active Directory 및 Office 365 사용자가 있는 전역 관리자를 위해 사용할 수 있습니다. 그러나 [고급 기능](multi-factor-authentication-whats-next.md)을 활용하려는 경우 Azure MFA의 전체 버전을 구입해야 합니다.

> [AZURE.NOTE]  Azure Multi-factor Authentication 공급자는 정식 버전의 Azure MFA에서 제공하는 기능을 활용하는 데 사용됩니다. **Azure MFA, Azure AD Premium 또는 EMS을 통해 라이선스를 갖고 있지 않은** 사용자를 위한 기능입니다. Azure MFA, Azure AD Premium 및 EMS는 기본적으로 Azure MFA의 전체 버전을 포함합니다. 라이선스를 보유한 경우 Multi-Factor Auth 공급자가 필요하지 않습니다.

SDK를 다운로드하려면 Azure Multi-Factor Auth 공급자가 필요합니다.

> [AZURE.IMPORTANT]  SDK를 다운로드하려는 경우 Azure MFA, AAD Premium 또는 EMS 라이선스가 있더라도 Azure Multi-Factor Auth 공급자를 만들어야 합니다. 이 용도로 Azure Multi-Factor Auth 공급자를 만들고 이미 라이선스를 보유하고 있는 경우 **활성화된 사용자별** 모델로 공급자를 만들고 Azure MFA, Azure AD Premium 또는 EMS 라이선스를 포함하는 디렉터리에 공급자를 연결해야 합니다. 이렇게 하면 사용자가 소유하는 라이선스 수보다 SDK를 사용한 더 많은 고유 사용자가 없는 한 요금은 청구되지 않습니다.

다음 단계를 따라 Azure Multi-Factor Auth 공급자를 만듭니다.

## Multi-Factor Auth 공급자를 만들려면
--------------------------------------------------------------------------------

1. 관리자 권한으로 **Azure 클래식 포털**에 로그온합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. Active Directory 페이지 위쪽에서 **Multi-Factor Authentication 공급자**를 선택합니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 아래쪽에서 **새로 만들기**를 클릭합니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. **앱 서비스**에서 **Multi-Factor Auth 공급자**를 선택합니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. **빠른 생성**을 선택합니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. 다음 필드를 입력하고 **만들기**를 선택합니다.
	1. **이름** – Multi-Factor Auth 공급자의 이름입니다.
	2. **사용 모델** – Multi-Factor Authentication 공급자의 사용 모델입니다.
		- 인증당 - 인증 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 소비자 지향 응용 프로그램에서 Azure Multi-Factor Authentication을 사용하는 시나리오에 사용됩니다.
		- 활성화된 사용자별 – 활성화된 사용자 단위로 요금이 청구되는 구매 모델입니다. 일반적으로 Office 365와 같은 응용 프로그램에 대한 직원 액세스에 사용합니다.
	2. **디렉터리** – Multi-Factor Authentication 공급자와 연결된 Azure Active Directory 테넌트입니다. 다음에 주의하세요.
		- Multi-Factor Auth 공급자를 만드는 데 Azure AD 디렉터리가 필요하지는 않습니다. Azure Multi-Factor Authentication 서버 또는 SDK를 사용하려는 경우 상자를 비워둡니다.
		- Multi-Factor Authentication 공급자를 Azure AD 디렉터리와 연결하여 고급 기능을 활용해야 합니다.
		- Azure AD Connect, AAD Sync 또는 DirSync는 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우에만 필요합니다. 동기화되지 않은 Azure AD 디렉터리를 사용하는 경우 필요하지 않습니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. 만들기를 클릭하면 Multi-Factor Authentication 공급자가 생성되고 **Multi-Factor Authentication 공급자를 성공적으로 만들었습니다**라는 메시지가 표시됩니다. **확인**을 클릭합니다. ![MFA 공급자 만들기](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!---HONumber=AcomDC_0921_2016-->