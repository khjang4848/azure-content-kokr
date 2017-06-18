<properties
	pageTitle="사용자의 Azure AD 조인 설정 | Microsoft Azure"
	description="관리자가 온-프레미스 디렉터리 및 장치 등록에 Azure AD 조인을 설치하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# 조직에서 Azure AD 조인 설정

Azure Active Directory 연결(Azure AD 연결)을 설정하기 전에 사용자의 온-프레미스 디렉터리를 클라우드와 동기화하거나 Azure AD에서 관리되는 계정을 수동으로 만들어야 합니다.

온-프레미스 사용자와 Azure AD의 동기화에 대한 자세한 지침을 보려면 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.


Azure AD에서 사용자를 수동으로 만들고 관리하려면 [Azure AD에서 사용자 관리](https://msdn.microsoft.com/library/azure/hh967609.aspx)를 참조하세요.

## 장치 등록 설정
1. 관리자 권한으로 Azure 포털에 로그온합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
4. **구성** 탭을 선택합니다.
5. **장치** 섹션으로 이동합니다.
6. **장치** 탭에서 다음을 설정합니다.
   * **사용자당 최대 장치 수**: Azure AD에서 사용자가 보유할 수 있는 장치의 최대 수를 선택합니다. 사용자가 이 할당량에 도달하는 경우 기존 장치 중 하나 이상이 제거될 때까지 장치를 더 추가할 수 없습니다.
   * **장치에 연결하기 위해 다단계 인증 필요**: 사용자가 Azure AD에 장치를 연결하기 위해 또 다른 인증 수단을 제공해야 하는지 여부를 설정합니다. Azure Multi-Factor Authentication에 대한 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md)을 참조하세요.
   * **사용자가 Azure AD에 장치를 연결할 수 있음**: Azure AD에 장치를 연결하도록 허용된 사용자 및 그룹을 선택합니다.
   * **Azure AD 연결 장치의 추가 관리자**: Azure AD Premium 또는 EMS(Enterprise Mobility Suite)를 사용하여 장치에 대한 로컬 관리자 권한을 부여 받은 사용자를 선택할 수 있습니다. 전역 관리자 및 장치 소유자에게는 기본적으로 로컬 관리자 권한이 부여됩니다.

<center>![장치 등록 설정](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

사용자를 위해 Azure AD 조인을 설정하면 해당 회사 또는 개인 장치를 통해 Azure AD에 연결할 수 있습니다.

다음은 사용자가 Azure AD 연결을 설정할 수 있는 세 가지 시나리오입니다.

- 사용자가 Azure AD에 직접 회사 소유의 장치를 연결합니다.
- 사용자가 온-프레미스 Active Directory에 회사 소유의 장치를 연결한 다음 장치를 Azure AD로 확장합니다.
- 사용자가 개인 장치의 Windows에 회사 또는 학교 계정 추가

## 추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->