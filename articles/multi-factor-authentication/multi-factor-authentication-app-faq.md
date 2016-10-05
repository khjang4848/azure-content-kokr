<properties
	pageTitle="Microsoft Authenticator 앱 FAQ"
	description="Microsoft Authentication 앱 및 Azure Multi-Factor Authentication과 관련된 질문과 대답 목록을 제공합니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="pblachar, librown"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="kgremban"/>

# Microsoft Authenticator 응용 프로그램 FAQ

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure Multi-factor Authentication을 사용하는 경우 권장됩니다. 이 앱은 Windows Phone, Android 및 iOS에 사용할 수 있습니다.

## 질문과 대답

- **보안 코드에 Microsoft Authenticator 응용 프로그램을 이미 사용하고 있습니다. 원 클릭 푸시 알림으로 전환하려면 어떻게 해야 하나요?**

	개인 계정으로 Microsoft 계정을 사용하고 있으며 푸시 알림으로 전환하려는 경우 계정을 다시 추가해야 합니다. 앱이 일회용 암호를 사용하기 때문입니다. 장치를 계정에 다시 등록하고 푸시 알림을 설정합니다.

	계정에 2단계 확인이 사용되도록 설정되지 않은 경우 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)를 참조하여 적절한지 알아봅니다.

- **iPhone 또는 iPad에서 원 클릭 푸시 알림을 언제 사용할 수 있나요?**

	이 기능은 8월 말까지 베타로 제공됩니다. 그 이후에는 Microsoft 계정에 대해 광범위하게 사용할 수 있게 됩니다. 베타 프로그램에 참여하려는 경우 msauthenticator@microsoft.com에 전자 메일을 보내주세요. 메시지에 이름, 성 및 Apple ID를 포함합니다.

- **Microsoft 이외의 계정에서도 원 클릭 푸시 알림이 작동하나요?**

	아니요. 푸시 알림은 Microsoft 계정 및 Azure Active Directory 계정에만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 이 기능을 사용하지 않도록 설정할 수 있습니다.

- **백업에서 장치를 복원했으며 내 계정 코드가 누락되었거나 작동하지 않습니다. 어떻게 된 건가요?**

	보안을 위해 이번에는 앱 백업에서 계정을 복원하지 않습니다. 백업에서 iOS 앱을 복원하는 경우 계정이 계속 표시되지만 로그인 확인을 수신하기 위해 작동되거나 보안 코드를 생성하지 않습니다. 앱을 복원한 후에 계정을 삭제한 후 다시 추가합니다.

## 관련된 항목

- [Azure Multi-Factor Authentication FAQ](multi-factor-authentication-faq.md)
- Microsoft 계정에 대한 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
- [ID 확인 앱 FAQ](https://support.microsoft.com/help/12414/microsoft-account-identity-verification-apps-faq)

<!---HONumber=AcomDC_0921_2016-->