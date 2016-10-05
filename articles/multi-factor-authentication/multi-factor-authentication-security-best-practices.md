<properties 
	pageTitle="Azure MFA 사용에 대한 보안 모범 사례"
	description="이 문서에서는 Azure 계정으로 Azure MFA를 사용하는 모범 사례를 제공합니다."
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
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure AD 계정으로 Azure Multi-Factor Authentication을 사용하기 위한 보안 모범 사례

인증 프로세스를 향상시키려 할 때 Multi-Factor Authentication이 대부분의 조직에 바람직한 선택입니다. Azure MFA(Multi-Factor Authentication)를 사용하면 회사에서 자체의 보안 및 준수 요구사항을 충족하면서도 사용자에게 단순한 로그인 경험을 제공할 수 있습니다. 이 문서에서는 Azure MFA를 채택하기 위한 계획을 할 때 고려해야 할 몇 가지 모범 사례를 설명합니다.

## 클라우드에서 Azure Multi-Factor Authentication에 대한 모범 사례
모든 사용자에게 Multi-Factor Authentication을 제공하고 Azure Multi-Factor Authentication이 제공하는 확장 기능을 이용하려면 모든 사용자에 대해 Azure Multi-Factor Authentication을 사용하도록 설정해야 합니다. 이 작업은 다음 중 하나를 사용하여 수행합니다.

- Azure AD Premium 또는 Enterprise Mobility Suite
- Multi-Factor Auth 공급자

### Azure AD Premium/Enterprise Mobility Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Azure AD Premium 또는 Enterprise Mobility Suite를 사용하여 클라우드에서 Azure MFA를 채택하기 위한 첫 번째 권장 단계는 사용자에게 라이선스를 할당하는 것입니다. Azure Multi-factor Authentication은 이러한 제품군의 일부이며 따라서 Multi-factor Authentication을 조직의 모든 사용자에게 확장하기 위해 추가로 아무 것도 필요하지 않습니다.

Multi-factor Authentication을 설정할 때 고려해야 할 사항:

- Multi-Factor Auth 공급자를 만들 필요가 없습니다. Azure AD Premium 및 Enterprise Mobility Suite에 Azure Multi-Factor Authentication이 함께 제공됩니다. Auth 공급자를 만들면 중복 청구될 수 있습니다.
- Azure AD Connect는 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우에만 필요합니다. Active Directory의 온-프레미스 인스턴스와 동기화되지 않은 Azure AD 디렉터리만 사용하는 경우에는 Azure AD Connect가 필요하지 않습니다.


### Multi-Factor Auth 공급자

![Multi-Factor Auth 공급자](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Azure AD Premium 또는 Enterprise Mobility Suite가 없는 경우 클라우드에서 Azure MFA를 채택하기 위한 첫 번째 권장 단계는 MFA Auth 공급자를 만드는 것입니다. MFA는 기본적으로 Azure Active Directory를 가지고 있는 글로벌 관리자가 사용할 수 있지만, 조직을 위해 MFA를 배포하는 경우 Multi-Factor Authentication 기능을 모든 사용자로 확장해야 하며 Multi-Factor Authentication Auth 공급자가 필요합니다. Auth 공급자를 선택하는 경우 디렉터리를 선택하고 다음 사항을 고려해야 합니다.

- Multi-Factor Auth 공급자를 만드는 데 Azure AD 디렉터리가 필요하지는 않습니다.
- 다단계 인증을 모든 사용자에게 확장하고 전역 관리자가 관리 포털, 사용자 지정 인사말, 보고서 등의 기능을 활용할 수 있게 하려면 Multi-Factor Auth 공급자를 Azure AD 디렉터리에 연결해야 합니다.
- DirSync 또는 AAD Sync는 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우에만 필요합니다. Active Directory의 온-프레미스 인스턴스와 동기화되지 않은 Azure AD 디렉터리만 사용하는 경우에는 DirSync 또는 AAD 동기화가 필요하지 않습니다.
- Azure AD Premium 또는 Enterprise Mobility Suite가 있으면 Multi-Factor Auth 공급자를 만들 필요가 없습니다. 사용자에게 라이선스를 할당하기만 하면 사용자에 대한 MFA를 켤 수 있습니다.
- 비즈니스에 알맞은 사용 모델(인증별 또는 활성화된 사용자별)을 선택해야 하며, 일단 사용 모델을 선택한 후에는 변경할 수 없습니다.

### 사용자 계정
사용자에 대해 MFA를 사용하도록 설정하는 경우 사용자 계정을 비활성화, 활성화 또는 적용의 세 가지 주요 상태 중 하나로 지정할 수 있습니다. 배포를 위해 가장 적절한 옵션을 사용하기 위해 아래 지침을 따르십시오.

- 사용자의 상태가 비활성화로 설정된 경우 해당 사용자는 Multi-Factor Authentication을 사용하고 있지 않은 것입니다. 이 설정이 기본 상태입니다.
- 사용자의 상태가 활성화로 설정된 경우 사용자가 활성화되었지만 등록 프로세스를 완료하지 않은 것을 의미합니다. 다음 로그인 시 프로세스를 완료하라는 메시지가 표시됩니다. 이 설정은 브라우저용이 아닌 앱에 영향을 주지 않습니다. 모든 앱은 등록 프로세스가 완료될 때까지 계속 작동합니다.
- 사용자의 상태가 적용으로 설정된 경우 사용자가 등록을 완료했을 수도 있고 그렇지 않을 수도 있음을 의미합니다. 등록 프로세스를 완료한 경우 다단계 인증을 사용합니다. 그렇지 않은 경우 다음 로그인 시 등록 프로세스를 완료하라는 메시지가 표시됩니다. 이 설정은 브라우저용이 아닌 앱에 영향을 줍니다. 이 앱은 앱 암호가 생성되어 사용될 때까지 작동하지 않습니다.
- [클라우드에서 Azure Multi-Factor Authentication 시작](multi-factor-authentication-get-started-cloud.md) 문서에서 사용할 수 있는 사용자 알림 템플릿을 사용하여 MFA 채택에 관한 이메일을 사용자에게 보내십시오.

### 지원 가능성

사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자에게 인식하게 하는 것이 중요합니다. 이렇게 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 아래 지침을 따르십시오.

- 모바일 앱 또는 전화에서 알림 또는 전화 통화를 받지 않았고 이 때문에 사용자가 로그인하지 못하는 시나리오를 처리할 수 있도록 기술 지원 요원을 교육해야 합니다. 기술 지원 요원은 사용자가 Multi-Factor Authentication을 "바이패스"하여 1회 인증할 수 있도록 일회성 바이패스 옵션을 사용하도록 설정할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다.
- 필요하면 Azure MFA의 신뢰할 수 있는 IP 기능을 활용할 수 있습니다. 이 기능을 사용하여 관리되는 또는 페더레이션된 테넌트의 관리자가 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 Multi-Factor Authentication을 바이패스할 수 있습니다. 기능은 Azure AD Premium, Enterprise Mobility Suite 또는 Azure Multi-Factor Authentication 라이선스가 있는 Azure AD 테넌트에서 사용할 수 있습니다.


## Azure Multi-Factor Authentication 온-프레미스에 대한 모범 사례
회사가 자체 인프라를 이용하여 MFA를 사용하도록 결정한 경우 Azure Multi-Factor Authentication 서버 온-프레미스를 배포해야 합니다. MFA 서버 구성 요소는 아래 다이어그램에 나와 있습니다.

![Multi-Factor Auth 공급자](./media/multi-factor-authentication-security-best-practices/server.png) *기본적으로 설치하지 않음** 설치하지만 기본적으로 사용하지 않음


Azure Multi-factor Authentication 서버를 사용하여 Azure AD 계정이 액세스하는 클라우드 리소스 및 온-프레미스 리소스를 보호할 수 있습니다. 그러나 이 기능은 페더레이션을 사용해서만 수행할 수 있습니다. 즉, AD FS를 보유하고 이를 Azure AD 테넌트와 페더레이션해야 합니다. Multi-factor Authentication 서버를 설정할 때 고려해야 할 사항:

- Active Directory Federation Services를 사용하여 Azure AD 리소스를 보호하는 경우 인증의 첫 번째 요인은 AD FS를 사용하여 온-프레미스로 수행하고 두 번째 요인은 클레임을 적용하여 온-프레미스로 수행합니다.
- Azure Multi-Factor Authentication 서버를 AD FS 페더레이션 서버에 반드시 설치해야 하는 것은 아니지만 AD FS용 Multi-Factor Authentication 어댑터를 AD FS가 실행되는 Windows Server 2012 R2에 설치해야 합니다. 지원되는 버전이기만 하면 다른 컴퓨터에 서버를 설치할 수 있으며, AD FS 어댑터를 AD FS 페더레이션 서버에 따로 설치할 수 있습니다. 어댑터를 별도로 설치하는 방법에 대한 지침은 아래 절차를 참조하세요.
- Multi-Factor Authentication AD FS 어댑터 설치 마법사는 Active Directory에 PhoneFactor Admins라는 보안 그룹을 만든 다음 페더레이션 서비스의 AD FS 서비스 계정을 이 그룹에 추가합니다. PhoneFactor Admins 그룹이 실제로 생성되었는지와 AD FS 서비스 계정이 이 그룹의 구성원인지를 도메인 컨트롤러에서 확인하는 것이 좋습니다. 필요한 경우 도메인 컨트롤러의 PhoneFactor Admins 그룹에 AD FS 서비스 계정을 수동으로 추가합니다.

### 사용자 포털
이 포털은 인터넷 정보 서버(IIS) 웹 사이트에서 실행되며 셀프 서비스 기능을 사용할 수 있고 사용자 관리 기능의 전체 집합을 제공합니다. 이 구성 요소를 구성하려면 아래 지침을 따르십시오.

- IIS 6 이상이 필요
- ASP.NET v2.0.507207을 설치하고 등록해야 함
- 이 서버는 경계 네트워크에 배포할 수 있습니다.



### 앱 암호
조직이 Azure AD와 함께 SSO를 사용하여 페더레이션되는 경우 앱 암호를 사용할 때 다음 사항에 유의하십시오(이는 페더레이션된(SSO) 사용자에게만 적용됨).

- Azure AD로 앱 암호를 확인하므로 페더레이션을 바이패스합니다. 앱 암호를 설정할 때에만 페더레이션이 사용됩니다.
- 페더레이션된(SSO) 사용자의 경우 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 실시간으로 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함/삭제 설정은 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
- 앱 암호를 사용할 경우 온-프레미스 클라이언트 액세스 제어 설정은 적용되지 않습니다.
- 온-프레미스 인증 로깅 / 감사 기능은 앱 암호에 사용할 수 없습니다
- 추가 최종 사용자 교육이 Microsoft Lync 2013 클라이언트에 필요합니다.
- 특정 고급 아키텍처 디자인은 클라이언트와 Multi-Factor Authentication을 사용하는 경우 인증 위치에 따라 조직의 사용자 이름과 암호 및 앱 암호를 조합하여 사용할 필요가 있습니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.
- 기본적으로 사용자는 앱 암호를 생성할 수 없으며, 회사가 이를 필요로 하거나 사용자가 어떤 시나리오에서 앱 암호를 생성할 수 있도록 해야 하는 경우 사용자가 앱 암호를 생성하여 브라우저용이 아닌 응용 프로그램에 로그인할 수 있음 옵션을 선택해야 합니다.

## 추가 고려 사항
온-프레미스로 배포할 각 구성 요소에 대한 일부 추가 고려 사항과 모범 사례를 이해하려면 아래 목록을 사용하십시오.

메서드|설명
:------------- | :------------- |
[Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md)|AD FS를 사용한 Azure Multi-Factor Authentication 설정에 대한 정보입니다.
[RADIUS 인증](multi-factor-authentication-get-started-server-radius.md)| RADIUS를 사용한 Azure MFA 서버 구성 및 설치에 대한 정보입니다.
[IIS 인증](multi-factor-authentication-get-started-server-iis.md)|IIS를 사용한 Azure MFA 서버 구성 및 설치에 대한 정보입니다.
[Windows 인증](multi-factor-authentication-get-started-server-windows.md)| Windows 인증을 사용한 Azure MFA 서버 구성 및 설치에 대한 정보입니다.
[LDAP 인증](multi-factor-authentication-get-started-server-ldap.md)|LDAP 인증을 사용한 Azure MFA 서버 구성 및 설치에 대한 정보입니다.
[RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버](multi-factor-authentication-get-started-server-rdg.md)| RADIUS를 사용한 원격 데스크톱 게이트웨이에서 Azure MFA 서버 구성 및 설치에 대한 정보입니다.
[Windows Server Active Directory와 동기화](multi-factor-authentication-get-started-server-dirint.md)|Active Directory와 Azure MFA 서버 간의 동기화 설정 및 구성에 대한 정보입니다.
[Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스](multi-factor-authentication-get-started-server-webservice.md)|Azure MFA 서버 웹 서비스 구성 및 설치에 대한 정보입니다.
[Azure Multi-Factor Authentication으로 고급 VPN 구성](multi-factor-authentication-advanced-vpn-configurations.md)|LDAP 또는 RADIUS를 사용한 Cisco ASA, Citrix Netscaler 및 Juniper/Pulse Secure VPN 어플라이언스 구성에 대한 정보.


## 추가 리소스
이 문서에서는 Azure MFA에 대한 몇 가지 모범 사례를 강조하지만 MFA 배포를 계획하는 동안에도 사용할 수 있는 다른 리소스가 있습니다. 아래 목록에는 이 프로세스 중에 도움이 될 수 있는 몇 가지 주요 문서가 있습니다.

- [Azure Multi-Factor Authentication에서 보고서](multi-factor-authentication-manage-reports.md)
- [Azure Multi-Factor Authentication의 환경 설정](multi-factor-authentication-end-user-first-time.md)
- [Azure Multi-Factor Authentication FAQ](multi-factor-authentication-faq.md)

<!---HONumber=AcomDC_0921_2016-->