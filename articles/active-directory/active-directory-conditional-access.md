<properties
	pageTitle="Azure Active Directory 조건부 액세스 | Microsoft Azure"  
    description="조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 조건이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다."  
    services="active-directory" 
	keywords="앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="09/14/2016"
	ms.author="markvi"/>


# Azure Active Directory 조건부 액세스   
  
회사 리소스에 대한 액세스를 보호하는 것은 모든 조직에 중요합니다. 클라우드 서비스 및 모바일 장치가 개발됨에 따라 사용자가 회사 리소스에 액세스하는 방법이 크게 변경되었습니다. 보안에 대해 새로운 방식이 필요합니다.
  
## 조건부 액세스를 사용하는 이유  

Azure Active Directory의 조건부 액세스 제어 기능은 클라우드 및 온-프레미스에서 회사의 보안 리소스를 보호하는 간단한 방법을 제공합니다. 다단계 인증을 요구함으로써 자격 증명의 도난 및 피싱 위험으로부터 보호할 뿐만 아니라, 민감한 서비스에 액세스 권한을 부여하는 Intune 관리 장치를 요구함으로써 회사 데이터를 안전하게 보호하기 위해 조건부 액세스 정책을 사용할 수 있습니다.



## 라이선스 요구 사항

Azure Active Directory 조건부 액세스는 [Azure AD Premium](http://www.microsoft.com/identity)의 기능입니다. 조건부 액세스 정책이 적용된 응용 프로그램에 액세스하는 모든 사용자는 Azure AD Premium 라이선스가 있어야 합니다. [허가되지 않은 사용자 보고서](https://aka.ms/utc5ix)를 통해 사용 현황에 대해 자세히 알아볼 수 있습니다.



## 조건부 액세스 제어는 어떻게 적용되나요?  

조건부 액세스 제어를 통해 Azure Active Directory는 사용자를 인증할 때 및 응용 프로그램에 대한 액세스를 허용하기 전에 선택한 특정 조건을 확인합니다. 이러한 액세스 요구 사항이 충족되면 사용자가 인증되고 응용 프로그램에 대한 액세스가 허용됩니다.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## 조건
  
- **그룹 멤버 자격**: 그룹에 대한 멤버 자격을 기반으로 사용자의 액세스 수준을 제어할 수도 있습니다.

- **위치**: 사용자가 신뢰할 수 있는 네트워크에 없는 경우 MFA를 트리거하고 컨트롤을 차단하는 사용자 위치를 사용할 수 있습니다.

- **장치 플랫폼**: iOS, Android, Windows Mobile 및 Windows와 같은 장치 플랫폼 유형을 정책 적용의 조건으로서 사용할 수 있습니다.

- **장치 사용**: 장치 정책 평가 시 장치 사용/사용 안 함 상태의 유효성을 검사합니다. 디렉터리에서 분실하거나 도난 당한 장치를 사용하지 못하게 하면 정책 요구 사항을 충족하는 데 더 이상 사용할 수 없게 됩니다.

- **로그인 및 사용자 위험**: 조건부 액세스 위험 정책은 Azure AD ID 보호에서 제공되며, 위험 이벤트와 비정상 로그인 활동을 바탕으로 고급 보호 기능을 제공합니다.


## 컨트롤
   
- **MFA(다단계 인증)**: MFA를 사용하는 강력한 인증을 요구할 수 있습니다. ADFS를 사용하여 Azure MFA 또는 온-프레미스 MFA 공급자가 MFA를 제공할 수 있습니다. MFA 인증은 유효한 사용자의 사용자 이름과 암호에 대한 액세스 권한을 얻은 권한이 없는 사용자가 액세스하는 것으로부터 리소스를 보호합니다.

- **차단**: 사용자 위치와 같은 조건 하에서 액세스를 적용할 수 있습니다. 예를 들어, 사용자가 신뢰할 수 있는 네트워크에 있지 않은 경우 액세스를 차단합니다.

- **호환 장치**: 장치 수준에서 도메인에 가입된 컴퓨터 또는 MDM(모바일 장치 관리)에 등록되고 규정을 준수하는 모바일 장치만 액세스를 허용하는 정책을 설정할 수 있습니다. Microsoft Intune은 장치에 대한 규정 준수를 확인한 다음 응용 프로그램 액세스 중에 적용하도록 이를 Azure Active Directory에 다시 보고하는 데 사용됩니다.
 

## 응용 프로그램

- 이러한 정책을 사용하여 설정할 수 있는 액세스 수준은 클라우드 또는 온-프레미스의 응용 프로그램 및 서비스에 적용할 수 있습니다. 정책은 웹 사이트 또는 서비스에 직접 적용됩니다. 그런 다음 브라우저 액세스뿐만 아니라 서비스에 액세스하는 응용 프로그램에 대해 정책이 적용됩니다. 적용할 수 있는 서비스 정책 목록은 여기에서 찾을 수 있습니다.


## 장치 기반 조건부 액세스

Azure AD에 등록되고 특정 조건을 충족하는 장치에서 응용 프로그램에 액세스하는 것을 제한할 수도 있습니다. 이는 다음과 같은 장치에서 이러한 리소스에 액세스하는 유효한 사용자로부터 조직의 리소스를 보호하는 데 유용합니다.

- 알 수 없는/관리되지 않는 장치
- 조직에서 정의한 보안 정책을 준수하지 않는 장치

다음 요구 사항에 따라 정책을 설정할 수 있습니다.

- **도메인 가입 장치** - 온-프레미스 Active Directory 도메인에 가입되고 Azure AD에 등록된 장치에 대한 액세스를 제한하는 정책을 설정할 수 있습니다. 이 정책은 Azure AD에 등록되고 온-프레미스 Active Directory 도메인에 속해 있는 Windows 데스크톱, 랩톱 또는 엔터프라이즈 태블릿에 적용됩니다. 도메인 가입 장치를 Azure AD에 자동으로 등록하도록 설정하는 방법에 대한 자세한 내용은 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 설정하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.

- **호환 장치** - 관리 시스템에 의해 디렉터리에 **준수**로 표시된 장치에 대한 액세스를 제한하는 정책을 설정할 수 있습니다. 이 정책을 설정하면 장치에서 파일 암호화를 적용하는 등 보안 정책을 준수하는 장치만 액세스가 허용됩니다. 이 정책을 사용하여 다음 장치에서의 액세스를 제한할 수 있습니다.

    - 하이브리드 구성에서 배포된 System Center Configuration Manager(현재 분기)에 의해 관리되는 **Windows 도메인 가입 장치**

    - Microsoft Intune 또는 지원되는 타사 MDM(모바일 장치 관리) 시스템에 의해 관리되는 **Windows 10 Mobile 회사 또는 개인 장치**

    - Microsoft Intune에 의해 관리되는 **iOS 및 Android 장치**


장치 기반 CA 정책에 의해 보호되는 응용 프로그램에 액세스하는 사용자는 이 정책을 준수하는 장치에서 이 작업을 수행해야 합니다. 정책을 준수하지 않는 장치에서는 액세스가 거부됩니다.

Azure AD에서 장치 기반 CA 정책을 구성하는 방법에 대한 자세한 내용은 [Azure Active Directory 연결 응용 프로그램의 액세스 제어를 위한 장치 기반 조건부 액세스 정책을 구성하는 방법](active-directory-conditional-access-policy-connected-applications.md)을 참조하세요.

## Azure Active Directory 조건부 액세스에 대한 문서 색인
  
다음 콘텐츠 맵에는 현재 배포에서 조건부 액세스를 사용하도록 설정하는 방법을 알아보기 위해 참조해야 하는 문서가 나열되어 있습니다.


### MFA 및 위치 정책

- [그룹, 위치 및 MFA 정책에 따라 Azure AD 연결 앱에 조건부 액세스 시작하기](active-directory-conditional-access-azuread-connected-apps.md)

- [지원되는 응용 프로그램 종류](active-directory-conditional-access-supported-apps.md)


### 장치 기반 조건부 액세스

- [Azure Active Directory 연결 응용 프로그램의 액세스 제어를 위한 장치 기반 조건부 액세스 정책을 설정하는 방법](active-directory-conditional-access-policy-connected-applications.md)

- [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 설정하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Azure AD 장치 기반 조건부 액세스 보호된 응용 프로그램에 액세스할 때 사용자 수정](active-directory-conditional-access-device-remediation.md)

- [Microsoft Intune을 요구하는 데이터 보호 지원](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### 로그인 위험을 기반으로 리소스 보호

[Azure AD ID 보호](active-directory-identityprotection.md)

### 추가 정보

- [조건부 액세스 FAQ](active-directory-conditional-faqs.md)
- [기술 참조](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0914_2016-->