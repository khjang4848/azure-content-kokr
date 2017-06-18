<properties 
	pageTitle="Azure Multi-Factor Authentication 보고서"
	description="사용자가 증명 프로세스를 다시 수행하도록 하는 등의 사용자 설정을 변경하는 방법에 대해 설명합니다."
	documentationCenter=""
	services="multi-factor-authentication"
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# 클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 장치 설정을 관리할 수 있습니다.

- [연락처 메서드를 다시 제공하도록 선택된 사용자 요구](#require-selected-users-to-provide-contact-methods-again)
- [사용자 기존 앱 암호 삭제](#delete-users-existing-app-passwords)
- [사용자에 대해 일시 중단된 모든 장치에서 MFA 복원](#restore-mfa-on-all-suspended-devices-for-a-user)






컴퓨터 또는 장치를 분실하거나 도난당한 경우 또는 사용자 액세스를 제거해야 하는 경우에 유용합니다.


## 연락처 메서드를 다시 제공할 선택된 사용자 요구

이 설정은 사용자가 로그인할 때 등록 프로세스를 다시 완료 하도록 합니다. 비브라우저 앱은 사용자가 해당 앱 암호를 소지한 경우 계속 작동됩니다. 또한 **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제**를 선택하여 사용자 앱 암호를 삭제할 수 있습니다.

### 연락처 메서드를 다시 제공할 사용자를 요구하는 방법




1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. 디렉터리 아래에서 연락처 메서드를 다시 제공하도록 요구하려는 사용자에 대한 디렉터리를 클릭합니다.
4. 위쪽에서 사용자를 클릭합니다.
5. 페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. Multi-Factor Auth 페이지를 엽니다.
6. 관리하려는 사용자를 찾고 이름 옆에 있는 확인란을 선택합니다. 위쪽에서 보기를 변경해야 할 수 있습니다.
7. 이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭하십시오.
8. **연락처 메서드를 다시 제공하도록 선택된 사용자 요구**를 선택합니다.![연락처 메서드 제공](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. 저장을 클릭합니다.
11. 닫기를 클릭합니다.

## 사용자 기존 앱 암호 삭제

사용자가 만든 모든 앱 암호가 삭제됩니다. 이러한 앱 암호와 연결된 비브라우저 앱은 새로운 앱 암호가 만들어질 때까지 작동이 중단됩니다.

### 사용자 기존 앱 암호를 삭제하는 방법

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. 디렉터리 아래에서 앱 암호를 삭제하려는 사용자에 대한 디렉터리를 클릭합니다.
4. 위쪽에서 사용자를 클릭합니다.
5. 페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. Multi-Factor Auth 페이지를 엽니다.
6. 관리하려는 사용자를 찾고 이름 옆에 있는 확인란을 선택합니다. 위쪽에서 보기를 변경해야 할 수 있습니다.
7. 이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭하십시오.
8. **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제**에 체크 표시합니다.![앱 암호 삭제](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. 저장을 클릭합니다.
10. 닫기를 클릭합니다.

## 사용자에 대해 기억된 모든 장치에서 MFA 복원

관리자는 사용자 장치 및 브라우저에서 Multi-Factor Authentication을 복원할 수 있습니다. 이 작업을 수행하면 모든 사용자의 장치 및 브라우저에서 MFA 기억이 제거되어 다음에 로그인 할 때 사용자는 MFA를 사용해야 합니다.

### 사용자에 대해 일시 중단된 모든 장치에서 MFA를 복원하는 방법

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. 디렉터리 아래에서 MFA를 복원하려는 사용자에 대한 디렉터리를 클릭합니다.
4. 위쪽에서 사용자를 클릭합니다.
5. 페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. Multi-Factor Auth 페이지를 엽니다.
6. 관리하려는 사용자를 찾고 이름 옆에 있는 확인란을 선택합니다. 위쪽에서 보기를 변경해야 할 수 있습니다.
7. 이렇게 하면 오른쪽에 **사용자 설정 관리** 링크가 표시됩니다. 이 단추를 클릭하십시오.
8. **모든 저장된 장치에서 다단계 인증 복원**을 선택합니다. ![앱 암호 삭제](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 저장을 클릭합니다.
10. 닫기를 클릭합니다.

<!---HONumber=AcomDC_0921_2016-->