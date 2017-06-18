<properties
	pageTitle="휴대폰용 Microsoft Authenticator 앱 | Microsoft Azure"
	description="최신 버전의 Azure Authenticator로 업그레이드하는 방법에 알아봅니다."
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

# Microsoft Authenticator

Microsoft Authenticator 앱은 Azure 계정(예: bsimon@contoso.onmicrosoft.com), 온-프레미스 회사 계정(예: bsimon@contoso.com) 또는 Microsoft 계정(예: bsimon@outlook.com)의 추가 보안 수준을 제공합니다.

이 앱은 두 가지 방법 중 하나로 작동합니다.

- **알림**. 이 앱을 사용하면 스마트폰 또는 태블릿에 알림을 푸시하여 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지할 수 있습니다. 알림을 확인한 후 올바르면 **확인**을 선택합니다. 그렇지 않으면 **거부**를 선택합니다. 알림을 거부하는 방법은 Multi-Factor Authentication에 사기 행위 거부 및 보고 기능을 사용하는 방법을 참조하세요.

- **암호와 확인 코드**. 이 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 메시지가 나타나면 사용자 이름, 암호와 함께 앱에서 제공한 코드를 로그인 화면에 입력합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

Microsoft Authenticator 앱이 릴리스됨에 따라 기존의 Azure Authenticator 앱이 신규 앱으로 교체되고 있습니다. Azure Authenticator 앱은 계속 사용할 수 있지만 새 Microsoft Authenticator 앱으로 전환할 것인지 결정해야 하며 이 문서에서 방법을 설명합니다.

## 앱 설치

[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## 앱에 계정 추가

Microsoft Authenticator 앱에 추가하려는 각 계정에 대해 다음 절차 중 하나를 사용합니다.

### QR 코드 스캐너를 사용하여 앱에 계정 추가

1. 보안 확인 설정 화면으로 이동합니다. 이 화면으로 이동하는 방법은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.

2. **구성**을 선택합니다.

	![보안 확인 설정 화면의 구성 단추](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	QR 코드가 있는 화면이 표시됩니다.

	![QR 코드를 제공하는 화면](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **+**를 선택한 다음 회사 또는 학교 계정을 추가할지 지정합니다.

	![더하기 기호가 있는 계정 화면](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![회사 또는 학교 계정을 지정하기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. 카메라를 사용하여 QR 코드를 스캔한 다음 **완료**를 선택하여 QR 코드 화면을 닫습니다.

	![QR 코드를 스캔하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	카메라가 올바르게 작동하지 않으면 QR 코드 및 URL을 수동으로 입력할 수 있습니다. 자세한 내용은 [앱에 수동으로 계정 추가](#add-an-account-to-the-app-manually)를 참조하세요.

5. 계정이 활성화되는 동안 기다립니다. 활성화가 완료되면 **연락처**를 선택합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.

	![로그인에 대해 확인을 선택하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. 회사에서 로그인 확인 인증 시 PIN이 필요한 경우 입력합니다.

	![PIN 입력을 위한 상자](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. PIN 입력이 끝나면 **닫기**를 선택합니다. 이제 확인이 성공적으로 수행된 것입니다.
8. 앱에 액세스할 수 없는 경우 휴대폰 번호를 입력하는 것이 좋습니다. 드롭다운 목록에서 국가를 선택하고 국가 이름 옆의 상자에 휴대폰 번호를 입력합니다. **다음**을 선택합니다.
9. 이제 연락 방법이 설정되었습니다. 다음으로 Outlook 2010 또는 이전 버전 등 비브라우저 앱을 위한 암호를 설정합니다. 이러한 앱을 사용하지 않으면 **완료**를 선택합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

	![앱 암호를 만들기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. 비브라우저 앱을 사용하는 경우 제공된 앱 암호를 복사하고 암호를 앱에 붙여넣습니다. Outlook 및 Lync 등의 개별 앱에 대한 단계를 보려면 메일의 암호를 앱 암호로 변경하는 방법 및 응용 프로그램의 암호를 앱 암호로 변경하는 방법을 참조하세요.
11. **완료**를 선택합니다.

이제 **계정** 화면에서 새 계정을 볼 수 있습니다.

![계정 화면](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### 앱에 수동으로 계정 추가

1. 보안 확인 설정 화면으로 이동합니다. 이 화면으로 이동하는 방법은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md)을 참조하세요.

2. **구성**을 선택합니다.

	![보안 확인 설정 화면의 구성 단추](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	QR 코드가 있는 화면이 표시됩니다. 코드와 URL을 기록합니다.

	![QR 코드와 URL을 제공하는 화면](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **+**를 선택한 다음 회사 또는 학교 계정을 추가할지 지정합니다.

	![더하기 기호가 있는 계정 화면](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![회사 또는 학교 계정을 지정하기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. 스캐너에서 **수동으로 코드 입력**을 선택합니다.

	![QR 코드를 스캔하는 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. 앱에서 해당 상자에 코드와 URL을 입력합니다.

	![코드와 URL을 입력하기 위한 화면](./media/multi-factor-authentication-azure-authenticator/manual.png)

	![코드와 URL을 입력하기 위한 화면](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. 계정이 활성화되는 동안 기다립니다. 활성화가 완료되면 **연락처**를 선택합니다. 이렇게 하면 휴대폰으로 알림 또는 확인 코드가 전송됩니다. **확인**을 선택합니다.

이제 **계정** 화면에서 새 계정을 볼 수 있습니다.

![계정 화면](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Touch ID를 사용하여 앱에 계정 추가

IOS의 Microsoft Authenticator 앱은 Touch ID를 지원합니다. Azure Multi-Factor Authentication을 사용하면 조직에서 장치에 대해 PIN을 요청할 수 있습니다. Touch ID를 사용할 경우 iOS 사용자는 PIN을 입력할 필요가 없습니다. 대신 지문을 스캔하고 **승인**을 선택할 수 있습니다.

Microsoft Authenticator를 통한 Touch ID 설정은 간단합니다. PIN을 사용하여 일반 확인 인증을 완료합니다. 장치에서 Touch ID를 지원하는 경우 Microsoft Authenticator는 자동으로 해당 계정에 대해 Touch ID를 설정합니다.

![Touch ID 설정 확인](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

해당 위치 다음부터 로그인 확인이 필요한 경우 받은 푸시 알림을 선택하고 PIN을 입력하는 대신 지문을 스캔합니다.

![푸시 알림](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## 이전 Azure 인증 앱 제거

새 앱에 모든 계정을 추가한 후 휴대폰에서 이전 앱을 제거할 수 있습니다.

## 계정 삭제

Microsoft Authenticator 앱에서 계정을 제거하려면 계정을 선택하고 **삭제**를 선택합니다.

![삭제 단추](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0921_2016-->