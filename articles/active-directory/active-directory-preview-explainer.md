<properties
	pageTitle="Azure Active Directory 미리 보기 설명자 | Microsoft Azure"
	description="클래식 포털의 Azure Active Directory와 Azure 포털의 Azure Active Directory 미리 보기 간의 차이점을 설명하는 항목입니다."
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
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Azure 포털에서 Azure Active Directory 관리 환경의 미리 보기

Azure Active Directory(Azure AD) 관리 환경은 Azure 포털에서 미리 보기 상태입니다. 해당 디렉터리의 전역 관리자로 [Azure 포털](https://portal.azure.com)에 로그인하여 시도할 수 있습니다. 그런 다음 표시된 경우 서비스 목록에서 Azure Active Directory를 선택하거나 **더 많은 서비스**를 선택하여 모든 서비스의 목록을 볼 수 있습니다. Azure 포털에서 Azure AD 관리 환경을 사용하는 데 Azure 구독이 필요하지 않습니다.


## 미리 보기 환경의 기능

미리 보기 환경을 사용하면 사용자, 그룹, 응용 프로그램과 Azure 포털에서 디렉터리 설정과 같은 많은 디렉터리 리소스를 관리할 수 있습니다. [Azure 클래식 포털](https://manage.windowsazure.com)에서 Azure AD 관리 환경에 존재하는 모든 기능을 포함하도록 이러한 환경을 개선하고 있습니다. 그때까지 사용자가 클래식 포털에서 여전히 완료해야 하는 몇 가지 디렉터리 관리 작업이 있습니다.

## 동일한 Azure AD 테넌트 관리

미리 보기 환경은 클래식 포털과 동일한 Azure Active Directory 테넌트 및 Office 365 관리 센터에 대해 읽고 씁니다. 이러한 포털 중 하나에서 변경한 내용은 다른 모두에 반영됩니다.

## 동일한 권한 부여 논리 사용

미리 보기 환경은 기존 Active Directory 클라이언트와 동일한 권한 부여 논리를 사용합니다. 사용자는 전역 관리자, 사용자 관리자, 암호 관리자와 같은 해당 디렉터리 역할에 따라 디렉터리 리소스를 변경할 수 있는 권한이 있습니다. Azure 리소스 또는 Azure 구독에 대한 역할을 가지는 것은 사용자에게 디렉터리 리소스를 관리할 수 있는 권한을 부여하지 않습니다. Azure AD 관리 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

미리 보기 환경은 전역 관리자에 대해 최적화됩니다. 전역 관리자가 아닌 사용자로 로그인한 동안 미리 보기 환경을 사용하는 경우 성능 저하를 경험할 수 있습니다. 예를 들어 디렉터리에서 완료할 수 없는 작업을 시작하도록 하는 단추를 선택할 수 있습니다. 이 환경을 곧 개선할 예정입니다.
 
## 의견을 알려 주세요.

[Azure AD 피드백 포럼](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc)의 관리 포털 섹션에서 미리 보기 환경에 대한 피드백을 제공할 수 있습니다.

<!---HONumber=AcomDC_0914_2016-->