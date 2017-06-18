<properties
	pageTitle="Azure Active Directory 장치 등록 개요| Microsoft Azure"
	description="장치 기반 조건부 액세스 시나리오의 기초입니다. 장치가 등록될 경우 Azure Active Directory Device Registration은 사용자가 로그인할 때 장치를 인증하는 데 사용되는 ID로 장치를 프로비전합니다."
	services="active-directory"
	keywords="장치 등록, 장치 등록 사용, 장치 등록 및 MDM"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/27/2016"
	ms.author="Markvi"/>

# Azure Active Directory 장치 등록 시작

Azure Active Directory Device Registration은 장치 기반 조건부 액세스 시나리오의 기초입니다. 장치가 등록될 경우 Azure Active Directory 장치 등록은 사용자가 로그인할 때 장치를 인증하는 데 사용되는 ID로 장치를 제공합니다. 그런 다음 인증된 장치 및 그 장치의 특성을 사용하여 클라우드 및 온-프레미스에 호스트되는 응용 프로그램에 조건부 액세스 정책을 적용할 수 있습니다.

Microsoft Intune과 같은 MDM(모바일 장치 관리) 솔루션과 함께 사용할 경우 Azure Active Directory의 장치 특성이 장치에 대한 추가 정보로 업데이트됩니다. 이렇게 하면 장치의 액세스를 적용하여 보안 및 규정 준수에 대한 표준을 충족하는 조건부 액세스 규칙을 만들 수 있습니다. Microsoft Intune에서 장치를 등록하는 방법에 대한 자세한 내용은 [Intune에서 관리에 대해 장치 등록](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요.

## Azure Active Directory Device Registration으로 사용할 수 있는 시나리오

Azure Active Directory 장치 등록에서는 iOS, Android 및 Windows 장치를 지원합니다. Azure AD 장치 등록을 활용하는 개별 시나리오에는 보다 구체적인 요구 사항 및 플랫폼 지원이 있을 수도 있습니다. 이러한 시나리오는 다음과 같습니다.

- **온-프레미스에서 호스트되는 응용 프로그램에 대한 조건부 액세스**: Windows Server 2012 R2에서 AD FS를 사용하도록 구성된 응용 프로그램에 대한 액세스 정책이 있는 등록된 장치를 사용할 수 있습니다. 온-프레미스에 대해 조건부 액세스를 설정하는 방법에 대한 자세한 내용은 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](active-directory-conditional-access-on-premises-setup.md)을 참조하세요.

- **Microsoft Intune을 사용한 Office 365 응용 프로그램에 대한 조건부 액세스**: IT 관리자는 조건부 액세스 장치 정책을 프로비전하여 규격 장치를 사용하는 정보 작업자가 서비스에 액세스할 수 있도록 하는 동시에 회사 리소스를 보호할 수 있습니다. 자세한 내용은 [Office 365 서비스에 대한 조건부 액세스 장치 정책](active-directory-conditional-access-device-policies.md)을 참조하세요.

##Azure Active Directory Device Registration 설정

잘 알려진 DNS 레코드를 조회하여 모바일 장치가 서비스를 검색할 수 있도록 Azure 포털에서 Azure AD 장치 등록을 사용하도록 설정해야 합니다. Windows 10, Windows 8.1, Windows 7, Android 및 iOS 장치가 서비스를 검색하고 사용할 수 있도록 회사 DNS를 구성해야 합니다. Azure Active Directory에서 관리자 포털을 사용하여 등록된 장치를 보고 사용/사용하지 않도록 설정할 수 있습니다.

>[AZURE.NOTE]
 자동 장치 등록을 설정하는 방법에 대한 최신 지침은 [Azure Active Directory를 사용하여 Windows 도메인 가입 장치의 자동 등록을 설정하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.

### Azure Active Directory 장치 등록 서비스 사용

1. 관리자 권한으로 Microsoft Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
4. **구성** 탭을 선택합니다.
5. **장치** 섹션으로 스크롤합니다.
6. **사용자가 장치에 작업 공간을 연결할 수 있습니다.**에 대해 **모두**를 선택합니다.
7. 사용자당 권한을 부여하려는 최대 장치 수를 선택합니다.

>[AZURE.NOTE]
Office 365에 대한 모바일 장치 관리 및 Microsoft Intune에 등록하려면 작업 공간 연결이 필요합니다. 이러한 서비스 중 하나를 구성한 경우 모두가 선택되고 없음 단추는 사용할 수 없습니다.

기본적으로 2단계 인증은 서비스에 대해 사용되지 않습니다. 그러나 장치를 등록하는 경우 2단계 인증을 사용하는 것이 좋습니다.

- 이 서비스에 대해 2단계 인증을 요구하기 전에 Azure Active Directory에서 2단계 인증 공급자를 구성하고 Multi-Factor Authentication에 대해 사용자 계정을 구성해야 합니다. [Azure Active Directory에 Multi-Factor Authentication 추가](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)를 참조하세요.

- Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS에서 2단계 인증 모듈을 구성해야 합니다. [Active Directory Federation Services로 Multi-Factor Authentication 사용](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)을 참조하세요.

## Azure Active Directory Device Registration 검색 구성
Windows 7 및 Windows 8.1 장치는 사용자 계정 이름을 잘 알려진 장치 등록 서버 이름과 결합하여 장치 등록 서비스를 검색합니다.

Azure Active Directory 장치 등록 서비스와 관련된 A 레코드를 가리키는 DNS CNAME 레코드를 만들어야 합니다. CNAME 레코드는 잘 알려진 접두사 enterpriseregistration과 조직에서 사용자 계정에 사용되는 UPN 접미사를 순서대로 사용해야 합니다. 조직에서 여러 UPN 접미사를 사용하는 경우 DNS에 여러 CNAME 레코드를 만들어야 합니다.

예를 들어 조직에서 @contoso.com 및 @region.contoso.com이라는 두 개의 UPN 접미사를 사용하는 경우 다음과 같은 DNS 레코드를 만듭니다.

| 항목 | 형식 | 주소 |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Azure Active Directory에서 장치 개체 보기 및 관리
1. Azure 관리자 포털에서 장치를 보고 차단 및 차단 해제할 수 있습니다. 차단된 장치는 등록된 장치만 허용하도록 구성된 응용 프로그램에 더 이상 액세스할 수 없습니다.
2. 관리자 권한으로 Microsoft Azure 포털에 로그온합니다.
3. 왼쪽 창에서 **Active Directory**를 선택합니다.
4. 디렉터리를 선택합니다.
5. **사용자** 탭을 선택합니다. 그런 다음 사용자를 선택하여 해당 장치를 표시합니다.
6. **장치** 탭을 선택합니다.
7. 드롭다운 메뉴에서 **등록된 장치**를 선택합니다.
8. 여기서 사용자가 등록한 장치를 보거나 차단 또는 차단 해제할 수 있습니다.

## 추가 항목

Azure AD 장치 등록을 사용하여 Windows 7 및 Windows 8.1 도메인 가입 장치를 등록할 수 있습니다. 다음 항목에서는 Windows 7 및 Windows 8.1 장치에서 장치 등록을 구성하는 데 필요한 단계 및 필수 조건에 대한 자세한 정보를 제공합니다.

- [도메인 가입 Windows 장치의 Azure Active Directory 자동 장치 등록](active-directory-conditional-access-automatic-device-registration.md)
- [Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Windows 10 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0928_2016-->