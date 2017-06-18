<properties
	pageTitle="Azure Multi-factor Authentication을 통해 연락 방법으로 모바일 앱 사용 | Microsoft Azure"
	description="이 문서에서는 Azure Multi-Factor Authentication에 대한 기본 연락 방법으로 모바일 앱을 사용하는 방법을 보여줍니다."
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
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Azure Multi-factor Authentication을 통해 연락 방법으로 모바일 앱 사용

기본 연락 방법으로 Microsoft Authenticator 앱을 사용하려는 경우 이 문서를 사용할 수 있습니다. 이 문서에서는 기본 연락 방법으로 모바일 앱을 사용하도록 Azure Multi-Factor Authentication을 설정하는 과정을 안내합니다.

[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## Microsoft Authenticator를 연락 방법으로 사용


1. **추가 보안 인증** 화면의 드롭다운 목록에서 **모바일 앱**을 선택합니다.
2. **알림** 또는 **일회용 암호** 중 하나를 선택하고 **설정**을 선택합니다.

	![추가 보안 확인 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Microsoft Authenticator 앱이 설치된 휴대폰에서 앱을 열고 **+**를 선택하여 계정을 추가합니다.
4. 회사 또는 학교 계정을 추가할지 지정합니다. 그러면 QR 코드 스캐너가 열립니다. 카메라가 제대로 작동하지 않는 경우 회사 정보를 수동으로 입력할 수 있습니다. 자세한 내용은 [수동으로 계정 추가](#add-an-account-manually)를 참조하세요.

	![계정을 선택하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

	![QR 코드를 스캔할지 수동으로 계정을 입력할지 여부를 선택하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

5. 모바일 앱 구성 화면에 표시되는 QR 코드 그림을 스캔합니다. **완료**를 선택하여 QR 코드 화면을 닫습니다.

	![QR 코드 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	스캔할 QR 코드가 표시되지 않으면 정보를 수동으로 입력할 수 있습니다.

	![수동으로 정보를 입력하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

6. 휴대폰에서 활성화가 완료되면 **연락처**를 선택합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.

	![로그인에 대해 확인을 선택하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

7. 회사에서 로그인 확인 인증 시 PIN이 필요한 경우 입력합니다.

	![PIN 입력을 위한 상자](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. PIN 입력이 끝나면 **닫기**를 선택합니다. 이제 확인이 성공적으로 수행된 것입니다.
9. 모바일 앱에 액세스할 수 없는 경우 휴대폰 번호를 입력하는 것이 좋습니다. 드롭다운 목록에서 국가를 선택하고 국가 이름 옆의 상자에 휴대폰 번호를 입력합니다. **다음**을 선택합니다.
10. 이제 연락 방법이 설정되었습니다. 다음으로 Outlook 2010 또는 이전 버전 등 비브라우저 앱을 위한 암호를 설정합니다. 이러한 앱을 사용하지 않으면 **완료**를 선택합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

	![앱 암호를 만들기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

11. 비브라우저 앱을 사용하는 경우 제공된 앱 암호를 복사하고 암호를 앱에 붙여넣습니다. Outlook 및 Lync 등의 개별 앱에 대한 단계를 보려면 메일의 암호를 앱 암호로 변경하는 방법 및 응용 프로그램의 암호를 앱 암호로 변경하는 방법을 참조하세요.
12. **완료**를 선택합니다.


## 수동으로 계정 추가
계정을 수동으로 추가하려면:

1. **수동으로 계정 입력** 단추를 선택합니다.

	![코드와 URL을 입력하기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

	![코드와 URL을 입력하기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

2. Azure Multi-Factor Authentication이 이미 있는 계정이 있다면 바코드가 표시되는 페이지에서 제공되는 URL과 코드를 입력합니다. 이 정보는 모바일 앱의 **코드**와 **URL** 상자로 입력됩니다.

	![설정](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. 활성화가 완료되면 **연락처**를 선택합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.

<!---HONumber=AcomDC_0921_2016-->