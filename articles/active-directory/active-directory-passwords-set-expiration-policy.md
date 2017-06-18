<properties
	pageTitle="Azure Active Directory에서 암호 만료 정책 설정 | Microsoft Azure"
	description="한 사용자나 여러 사용자의 Azure Active directory 암호에 대한 만료 정책을 확인하고 변경하는 방법을 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="curtand"/>


# Azure Active Directory에서 암호 만료 정책 설정

> [AZURE.IMPORTANT] **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).

Microsoft 클라우드 서비스의 전역 관리자는 Windows PowerShell용 Microsoft Azure Active Directory 모듈을 사용하여 사용자의 암호가 만료되지 않도록 설정할 수 있습니다. 또한 Windows PowerShell cmdlet을 사용하여 만료되지 않는 구성을 제거하거나 어떤 사용자 암호가 만료되지 않도록 설정되어 있는지 확인할 수 있습니다. 이 문서에서는 ID 및 디렉터리 서비스로 Microsoft Azure Active Directory를 사용하는 Microsoft Intune, Office 365와 같은 클라우드 서비스에 대한 도움말을 제공합니다.

  > [AZURE.NOTE] 디렉터리 동기화를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [디렉터리 동기화 로드맵](https://msdn.microsoft.com/library/azure/hh967642.aspx)의 항목 목록을 참조하세요.

Windows PowerShell cmdlet을 사용하려면 먼저 이를 설치해야 합니다.

## 원하는 작업을 선택하세요.

- [암호에 대한 만료 정책 확인 방법](#how-to-check-expiration-policy-for-a-password)

- [암호가 만료되도록 설정](#set-a-password-to-expire)

- [암호가 만료되지 않도록 설정](#set-a-password-to-never-expire)

## 암호에 대한 만료 정책 확인 방법

1.  회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.

2.  다음 중 하나를 수행합니다.

	- 사용자의 암호가 만료되지 않도록 설정되어 있는지 확인하려면 해당 사용자의 사용자 계정 이름(UPN)(예: aprilr@contoso.onmicrosoft.com) 또는 사용자 ID를 사용하여 `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires` cmdlet을 실행합니다.

	- 모든 사용자에 대한 "암호 만료되지 않음" 설정을 보려면 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires` cmdlet을 실행합니다.

## 암호가 만료되도록 설정

1.  회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.

2.  다음 중 하나를 수행합니다.

	- 특정 사용자의 암호가 만료되도록 설정하려면 해당 사용자의 사용자 계정 이름(UPN) 또는 사용자 ID를 사용하여 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false` cmdlet을 실행합니다.

	- 조직의 모든 사용자 암호가 만료되도록 설정하려면 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false` cmdlet을 사용합니다.

## 암호가 만료되지 않도록 설정

1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.

2.  다음 중 하나를 수행합니다.

	- 특정 사용자의 암호가 만료되지 않도록 설정하려면 해당 사용자의 사용자 계정 이름(UPN) 또는 사용자 ID를 사용하여 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true` cmdlet을 실행합니다.

	- 조직의 모든 사용자 암호가 만료되지 않도록 설정하려면 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true` cmdlet을 사용합니다.

## 다음 단계

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).

<!---HONumber=AcomDC_0921_2016-->