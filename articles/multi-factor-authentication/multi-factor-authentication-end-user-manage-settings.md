<properties
	pageTitle="Azure Multi-Factor Authentication에 문제가 있는 경우 | Microsoft Azure"
	description="이 문서는 Azure 다단계 인증에 문제가 있는 경우 수행할 작업에 대한 정보를 제공합니다."
	services="multi-factor-authentication"
	keywords = "다단계 인증 클라이언트, 인증 문제, 상관관계 ID"
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

# Azure Multi-Factor Authentication에 문제가 있는 경우
>[AZURE.IMPORTANT]
이 페이지를 개선하도록 도와주세요. 이 페이지에서 문제에 대한 답변을 찾지 못한 경우 이에 대한 내용을 추가할 수 있도록 자세한 피드백을 제공해주세요.

발생할 수 있는 일반적인 문제 중 일부를 도와주는 다음 정보가 제공됩니다.


- [상관관계 ID 오류](#correlation-id-errors)
- [전화를 분실했거나 도난당한 경우](#i-have-lost-my-phone-or-it-was-stolen)
- [휴대폰 번호를 변경하려는 경우](#i-want-to-change-my-phone-number)
- [새 전화를 구입했고 내 전화 번호를 변경해야 하는 경우](#i-have-a-new-phone-and-need-to-change-my-phone-number)
- [휴대폰에서 코드를 받지 못하는 경우](#i-am-not-receiving-a-code-or-a-call-on-my-phone)
- [앱 암호가 작동하지 않는 경우](#app-passwords-are-not-working)
- [이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동하려는 경우](#how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one)
- [문제에 대한 답변을 찾을 수 없는 경우](#i-didnt-find-an-answer-to-my-problem)

##상관관계 ID 오류
아래의 문제 해결 단계를 시도해도 여전히 문제가 발생하면 [Azure AD 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)에 질문을 게시하거나, [Microsoft 기술 자료(KB)를 검색하거나](https://www.microsoft.com/ko-KR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport), [지원에 문의하시면](https://support.microsoft.com/ko-KR) 빠른 시일 내에 문제를 살펴보겠습니다.

지원에 문의할 때 다음 정보를 포함 하는 것이 좋습니다.

 - **오류에 대한 일반적인 설명** – 사용자가 본 정확한 오류 메시지 정의 오류 메시지가 있는 경우 알아낸 예기치 않은 동작을 자세히 설명합니다.
 - **페이지** – 오류(URL 포함)가 나타나는 경우 페이지 정의
 - **ErrorCode** - 수신한 특정 오류 코드
 - **SessionId** - 수신한 특정 세션 ID
 - **상관관계 ID** – 사용자가 오류를 확인했을 때 생성된 상관관계 ID
 - **타임스탬프** – 오류(표준 시간대 포함)가 나타나는 경우 정확한 날짜 및 시간 정의

![상관관계 ID](./media/multi-factor-authentication-end-user-manage/correlation.png)

 - **사용자 ID** – 오류가 나타난 사용자의 ID 정의(예: user@contoso.com)
 - **사용자에 대한 정보** – 페더레이션된 사용자, 동기화된 암호 해시, 클라우드 정의 Azure AD Premium, Enterprise Mobility 또는 Azure AD Basic 라이선스 할당 여부 Office 365 등을 사용하는지 여부

이 정보를 포함하면 최대한 빨리 문제를 해결하는데 도움이 됩니다.

## 전화를 분실했거나 도난당한 경우
전화를 분실했거나 도난당한 경우 관리자에게 [앱 암호](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords)를 초기화하고 [기억된 장치](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-suspended-devices-for-a-user)를 삭제하도록 문의하는 것이 좋습니다.

사용자 계정으로 돌아가는 데는 두 가지 옵션이 있습니다. 첫 번째는 대체 인증 전화 번호를 설정한 경우 이를 사용하여 사용자 계정으로 돌아가 보안 설정을 변경할 수 있습니다.

보조 인증 전화 번호를 지정한 경우 이를 사용하여 로그인할 수 있습니다. ![설정](./media/multi-factor-authentication-end-user-manage/altphone.png) 위의 스크린샷에서 두 개의 전화 번호가 설정되었습니다. 67 및 30에서 하나씩 끝납니다.

대체 전화 번호를 사용하여 로그인하려면 일반적으로 로그인한 다음 **다른 인증 옵션 사용**을 선택하면 됩니다. ![다양한 확인](./media/multi-factor-authentication-end-user-manage/differentverification.png)

그런 다음 다른 전화번호를 선택합니다. 이 경우 **다음 번호로 호출: +X XXXXXXXX30**을 선택합니다.

![대체 전화](./media/multi-factor-authentication-end-user-manage/altphone2.png)

>[AZURE.IMPORTANT]
보조 인증 전화 번호를 구성하는 것이 중요합니다. 기본 전화 번호 및 모바일 앱은 아마도 동일한 전화상에 있으므로 보조 전화 번호는 전화를 분실하거나 도난당한 경우 사용자 계정으로 돌아갈 수 있는 유일한 방법입니다.

보조 인증 전화 번호를 구성하지 않은 경우 관리자에게 설정을 삭제하도록 문의하여 다음 번 로그인 시 [Multi-Factor Authentication 설정](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again)을 다시 하라는 메시지가 나타나도록 해야 합니다.

## 휴대폰 번호를 변경하려는 경우
다중 요소 인증을 사용하는 방법에 따라 전화 번호와 같은 설정을 변경할 수 있는 몇 가지 위치가 있습니다. 자신에게 가장 잘 맞는 한 가지를 선택하도록 아래의 표를 사용합니다.

다단계 인증을 사용하는 방법|설명
:------------- | :------------- |
[Office 365에서 사용](#changing-your-settings-with-office-365)| Office 365 포털을 통해 설정을 변경할 수 있습니다.
[잘 모름](#changing-your-settings-with-the-myapps-portal)|즉, [http://myapps.microsoft.com](http://myapps.microsoft.com)에 로그인한 후 여기에서 설정을 변경할 수 있습니다.
[Microsoft Azure에서 사용](#changing-your-settings-with-microsoft-azure)| Azure 포털을 통해 설정을 변경할 수 있습니다.



### Office 365에서 설정 변경


Office 365와 함께 다단계 인증을 사용하는 경우 Office 365 포털을 통해 추가 보안 확인 설정을 관리할 수 있습니다.

#### Office 365 포털에서 설정을 변경하려면

1. [Office 365 포털](https://login.microsoftonline.com/)에 로그온합니다.
2. 오른쪽 위 모서리에서 위젯을 선택하고 Office 365 설정을 선택합니다.
3. 추가 보안 인증을 클릭합니다.
4. 오른쪽의 **Update my phone numbers used for account security(계정 보안에 사용되는 전화 번호 업데이트)** 링크를 클릭합니다. ![O365](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 이렇게 하면 설정을 변경할 수 있는 페이지로 이동합니다. ![O365](./media/multi-factor-authentication-end-user-manage/o365b.png)


### Myapps 포털에서 설정 변경

Multi-Factor Authentication을 사용하는 방법을 잘 모르는 경우 myapps 포털을 통해 항상 설정을 변경할 수 있습니다.

#### Myapps 포털에서 설정을 변경하려면

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
2. 위쪽에서 프로필을 선택합니다.
3. 추가 보안 인증을 선택합니다. ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 설정을 변경할 수 있는 페이지로 이동합니다.

![검사](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

### Microsoft Azure에서 설정 변경

Azure에서 Multi-Factor Authentication을 사용하는 경우 Azure 포털을 통해 설정을 변경할 수 있습니다.

#### Azure 포털에서 추가 보안 확인 설정에 액세스하려면


1. Azure 포털에 로그온합니다.
2. Azure 포털의 위쪽에서 사용자 이름을 클릭합니다. 그러면 드롭다운 상자가 표시됩니다.
3. 드롭다운 상자에서 추가 보안 인증을 선택합니다. ![Azure](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. 이렇게 하면 설정을 변경할 수 있는 페이지로 이동합니다.![검사](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

##새 전화를 구입했고 내 전화 번호를 변경해야 하는 경우

새 전화를 구입했고 mfa에서 사용할 기본 연락처 번호를 변경해야 하는 경우 두 가지 방법 중 하나로 이 작업을 수행할 수 있습니다.

>[AZURE.IMPORTANT]
보조 인증 전화 번호를 구성하는 것이 중요합니다. 기본 전화 번호 및 모바일 앱은 아마도 동일한 전화상에 있으므로 보조 전화 번호는 전화를 분실하거나 도난당한 경우 사용자 계정으로 돌아갈 수 있는 유일한 방법입니다.

첫 번째 방법은 보조 인증 방법을 사용하는 것입니다. 보조 인증 전화 번호를 지정한 경우 이를 사용하여 로그인할 수 있습니다. ![설정](./media/multi-factor-authentication-end-user-manage/altphone.png) 위의 스크린샷에서 두 개의 전화 번호가 설정되었습니다. 67 및 30에서 하나씩 끝납니다.

대체 전화 번호를 사용하여 로그인하려면 일반적으로 로그인한 다음 **다른 인증 옵션 사용**을 선택하면 됩니다. ![다양한 확인](./media/multi-factor-authentication-end-user-manage/differentverification.png)

그런 다음 다른 전화번호를 선택합니다. 이 경우 **다음 번호로 호출: +X XXXXXXXX30**을 선택합니다.

![대체 전화](./media/multi-factor-authentication-end-user-manage/altphone2.png)

두 번째는 관리자 또는 mfa를 설정한 사람에게 문의하는 방법입니다. 보조 인증 전화 번호를 구성하지 않은 경우 이 작업을 수행해야 합니다. 그렇지 않은 경우 관리자 또는 mfa를 설정한 사람에게 설정을 삭제하도록 문의하여 다음 번 로그인 시 [Multi-Factor Authentication 설정](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again)을 다시 하라는 메시지가 나타나도록 해야 합니다.

##휴대폰에서 코드를 받지 못하는 경우

먼저 다음을 확인해야 합니다.



- 휴대폰으로 전화 통화를 수신하도록 선택한 경우 적절한 셀 신호가 있는지 확인합니다. 전송 속도 및 가용성은 위치와 서비스 공급자에 따라 달라질 수 있습니다.
- 텍스트 메시지로 휴대폰에 인증 코드를 받도록 선택한 경우 서비스 요금제를 확인하고 장치가 텍스트 메시지 전송을 지원하는지 확인합니다. 전송 속도 및 가용성은 위치와 서비스 공급자에 따라 달라질 수 있습니다. 또한 이러한 코드를 수신하려고 할 때 적절한 셀 신호가 있는지 확인합니다.
- 모바일 앱을 통해 인증을 받도록 선택한 경우 명확한 셀 신호가 있는지 확인합니다. 또한 전송 속도 및 가용성은 위치와 서비스 공급자에 따라 달라질 수 있습니다.

스마트폰이 있으면 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)용으로 제공되는 Microsoft Authenticator 앱을 사용하는 것이 좋습니다.

로그인 시 **다른 인증 옵션 사용**을 선택하여 모바일 앱을 통해 텍스트 메시지로 인증 코드를 받는 방법 간에 전환할 수 있습니다.

![다양한 확인](./media/multi-factor-authentication-end-user-manage/differentverification.png)

경우에 따라 이러한 서비스 중 한 가지 전송 방법이 다른 것보다 안정적입니다.

여러 인증 코드를 받은 경우 최신 코드만 작동합니다.

이전에 백업 휴대폰을 구성한 경우 로그인 페이지에서 확인 메시지가 표시될 때 해당 휴대폰을 선택하여 다시 시도하는 것이 좋습니다. 구성한 다른 방법이 없는 경우 관리자에게 설정을 삭제하도록 문의하여 다음 번 로그인 시 [Multi-Factor Authentication 설정](multi-factor-authentication-manage-users-and-devices.md#require-selected-users-to-provide-contact-methods-again)을 다시 수행하도록 합니다.

##앱 암호가 작동하지 않는 경우
먼저 앱 암호를 올바르게 입력했는지 확인합니다. 계속 작동하지 않으면 로그인을 시도하고 [새 앱 암호를 생성](multi-factor-authentication-end-user-app-passwords.md)합니다. 계속 작동하지 않으면 관리자에게 [기존 앱 암호를 삭제](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords)하도록 문의한 다음 새 암호를 만들어 사용합니다.

##이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동하려는 경우
장치에서 앱을 제거하거나 장치를 재점멸하는 경우 백 엔드에서 정품 인증을 제거하지 않습니다. [새 장치로 이동](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app)에 설명된 단계를 사용해야 합니다.

##문제에 대한 답변을 찾을 수 없는 경우
이 페이지에서 문제에 대한 답변을 찾을 수 없는 경우 [Azure AD 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)에 질문을 게시하거나, [Microsoft 기술 자료(KB)를 검색하거나](https://www.microsoft.com/ko-KR/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport), [지원에 문의](https://support.microsoft.com/ko-KR)할 수 있습니다.

또한, 관리자 또는 Multi-Factor Authentication을 설정한 사람에게 문의하여 도움을 받을 수 있습니다.

마지막으로, 이 페이지를 업데이트하고 보다 많은 정보를 제공하여 지속적으로 개선할 수 있도록 이 페이지에 자세한 피드백을 남겨야 합니다.

<!---HONumber=AcomDC_0921_2016-->