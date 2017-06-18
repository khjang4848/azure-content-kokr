<properties
	pageTitle="Azure Multi-Factor Authentication을 사용할 경우 Azure MFA 로그인 환경"
	description="이 페이지에서는 Azure MFA에서 사용할 수 있는 다양한 로그인 방법을 확인할 수 있는 위치에 대한 지침을 제공합니다."
	keywords="사용자 인증, 로그인 환경, 휴대폰으로 로그인, 사무실 전화로 로그인"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication의 로그인 환경
> [AZURE.NOTE]  이 페이지에 제공된 다음의 문서는 일반적인 로그인 환경을 보여줍니다. 로그인 관련 지원은 [Azure Multi-Factor Authentication에 문제가 있는 경우](multi-factor-authentication-end-user-manage-settings.md)를 참조하세요.



## 어떤 로그인 환경이 제공되나요?
로그인 및 다단계 인증 사용 방식에 따라 환경이 달라집니다. 이 섹션에서는 로그인 시 예상되는 환경에 대한 정보를 제공합니다. 수행하는 작업을 가장 잘 설명하는 항목을 선택합니다.


수행하는 작업|설명
:------------- | :------------- |
[휴대폰 또는 사무실 전화로 로그인](#signing-in-with-mobile-or-office-phone) | 휴대폰 또는 사무실 전화를 사용하여 로그인할 때 예상할 수 있는 환경입니다.
[알림을 사용하여 Microsoft Authenticator 앱에 로그인](#signing-in-with-the-microsoft-authenticator-app-using-notification) | 인증을 통해 Microsoft Authenticator 앱을 사용할 때 예상할 수 있는 환경입니다.
[확인 코드를 사용하여 Microsoft Authenticator 앱에 로그인](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|확인 코드를 통해 Microsoft Authenticator 앱을 사용할 때 예상할 수 있는 환경입니다.
[다른 방법을 사용하여 로그인](#signing-in-with-an-alternate-method)|여기서는 대체 방법을 사용하려면 경우 예상할 수 있는 환경을 보여 줍니다.

## 휴대폰 또는 사무실 전화로 로그인

다음 정보에서는 휴대폰 또는 사무실 전화를 통해 다단계 인증을 사용하는 환경에 대해 설명합니다.

### 사무실 전화 또는 휴대폰으로 걸려오는 전화를 사용하여 로그인하려면

- 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
- Microsoft가 사용자에게 전화를 합니다.

![Microsoft 호출](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- 전화를 받고 # 키를 누릅니다.

![응답](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- 사용자가 로그인됩니다.</li>

## 알림을 사용하여 Microsoft Authenticator 앱에 로그인

다음 정보에서는 알림을 받았을 때 Microsoft Authenticator에서 Multi-Factor Authentication을 사용하는 환경에 대해 설명합니다.

### 받은 알림을 사용하여 Microsoft Authenticator 앱에 로그인하려면

- 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
- Microsoft가 알림을 보냅니다.

![Microsoft가 알림을 보냄](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- 전화를 받고 확인 키를 누릅니다. 회사에서 PIN을 요구하는 경우 이 시점에서 PIN을 입력해야 합니다.

![Verify](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- 사용자가 로그인됩니다.


## 확인 코드를 사용하여 Microsoft Authenticator 앱에 로그인

다음 정보에서는 확인 코드를 함께 사용할 때 Microsoft Authenticator 앱에서 Multi-Factor Authentication을 사용하는 환경에 대해 설명합니다.

### 확인 코드를 사용하여 Microsoft Authenticator 앱에 로그인하려면

- 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
- Microsoft가 확인 코드를 요구합니다.

![확인 코드 입력](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- 휴대폰에서 Microsoft Authenticator 앱을 열고 로그인 위치에 제공되는 상자에 코드를 입력합니다.

![코드 받기](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- 사용자가 로그인됩니다.


## 다른 방법을 사용하여 로그인


다음 섹션에서는 기본 방법을 사용할 수 없을 때 대체 방법을 사용하여 로그인하는 방법을 보여 줍니다.

### 대체 방법을 사용하여 로그인하려면

- 사용자 이름 및 암호를 사용하여 Office 365와 같은 응용 프로그램 또는 서비스에 로그인합니다.
- 다른 확인 옵션을 사용하도록 선택합니다. 다양한 옵션이 제공됩니다. 표시되는 옵션 수는 설정 수에 따라 다릅니다.

![대체 방법 사용](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- 대체 방법을 선택하고 로그인합니다.

<!---HONumber=AcomDC_0921_2016-->