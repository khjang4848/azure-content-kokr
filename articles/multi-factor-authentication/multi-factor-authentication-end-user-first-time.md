<properties
	pageTitle="Azure Multi-Factor Authentication으로 처음 로그인"
	description="이 페이지에서는 처음 로그인할 때의 사용자 환경에 대해 설명합니다."
	services="multi-factor-authentication"
	keywords="Active Directory 사용 방법, 클라우드의 Active Directory, Active Directory 자습서"
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
# Azure Multi-Factor Authentication의 환경 설정

 관리자가 사용자의 ID를 확인하기 위해 휴대폰의 암호 및 응답을 둘 다 사용하도록 사용자 계정을 구성할 경우 추가 보안 확인 설정이 사용됩니다. 관리자가 추가 보안 확인을 요구하도록 계정을 구성한 경우 **자동 등록 프로세스를 완료할 때까지 로그인할 수 없습니다**.

## 다단계 인증을 사용하는 방법 결정

 계정을 구성한 후에 처음 로그인하면 자동 등록 프로세스를 시작하라는 메시지가 표시됩니다. **Set it up now(지금 설정)**를 클릭하여 이 프로세스를 시작할 수 있습니다.

![설정](./media/multi-factor-authentication-end-user-first-time/first.png)

등록 프로세스를 사용하여 기본 설정 확인 방법을 지정할 수 있습니다. 다음 표의 방법 중 하나를 지정할 수 있습니다. 연습이 포함된 추가 정보를 보려면 방법 중 하나를 클릭하면 됩니다.

메서드|설명
:------------- | :------------- |
[휴대폰 통화](multi-factor-authentication-end-user-first-time-mobile-phone.md)| 인증 휴대폰으로 자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. 이 전화 번호는 온-프레미스 Active Directory와 동기화되지 않습니다.
[휴대폰 문자 메시지](multi-factor-authentication-end-user-first-time-mobile-phone.md)|확인 코드를 포함하는 문자 메시지를 사용자에게 보냅니다. 사용자는 확인 코드로 문자 메시지에 응답하거나 또는 로그인 인터페이스에 확인 코드를 입력하도록 요구됩니다.
[사무실 전화 통화](multi-factor-authentication-end-user-first-time-office-phone.md)|사용자에게 자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다.
[모바일 앱](multi-factor-authentication-end-user-first-time-mobile-app.md)|사용자의 스마트폰이나 태블릿의 Microsoft Authenticator 앱에 푸시 알림을 보냅니다. 사용자는 앱에서 "확인"을 탭하여 인증을 받습니다. 또는 앱을 오프라인 인증을 위한 OTP 토큰으로도 사용할 수 있습니다. 사용자가 인증을 위해 로그인 화면에 토큰을 입력합니다.<br><p> Microsoft Authenticator 앱은 Multi-Factor Authentication 서비스가 제공할 수 있는 추가 보안을 제공하기 위해 2가지 다른 모드로 작동할 수 있습니다. 두 가지 모드는 다음과 같습니다.<li>**알림** - 이 모드에서는 Microsoft Authenticator 앱이 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지합니다. 이 작업은 휴대폰이나 등록된 장치로 푸시 알림을 보내는 방식으로 수행됩니다. 알림을 확인한 후 올바르면 인증을 선택합니다. 그렇지 않은 경우 거부를 선택하거나 사기성 알림을 거부한 후 보고하도록 선택할 수 있습니다. 사기성 알림을 보고하는 방법에 대한 자세한 내용은 Multi-Factor Authentication을 위해 사기 행위 거부 및 보고 기능을 사용하는 방법을 참조하세요.</li><p><li>**일회용 암호** - 이 모드에서 Microsoft Authenticator 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 이 확인 코드를 사용자 이름 및 암호와 함께 입력하여 두 번째 형식의 인증을 제공할 수 있습니다.</li><br><p> [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->