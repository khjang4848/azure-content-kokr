<properties
	pageTitle="Azure Active Directory 연결 응용 프로그램의 액세스 제어를 위한 Azure Active Directory 장치 기반 조건부 액세스 정책을 설정하는 방법"
	description="IT 관리자가 Azure AD 연결 응용 프로그램에 대한 장치 기반 조건부 액세스를 설정할 수 있는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="markvi"/>


# Azure Active Directory 연결 응용 프로그램의 액세스 제어를 위한 Azure Active Directory 장치 기반 조건부 액세스 정책을 설정하는 방법 


Azure Active Directory 장치 기반 조건부 액세스는 다음으로부터 조직의 리소스를 보호하는 데 필요한 기능입니다.

- 알 수 없는/관리되지 않는 장치의 액세스
- 조직에서 정의한 보안 정책을 준수하지 않는 장치

조건부 액세스에 대한 개요는 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.

장치 기반 조건부 액세스 정책을 설정하여 다음을 보호할 수 있습니다.

- 조직의 사이트 및 문서를 보호하는 Office 365 SharePoint Online

- 조직의 전자 메일을 보호하는 Office 365 Exchange Online

- 인증을 위해 Azure AD에 연결된 SaaS 응용 프로그램

- Azure AD 응용 프로그램 프록시를 통해 게시되는 온-프레미스 응용 프로그램


Azure 관리 포털에서 디렉터리의 특정 응용 프로그램으로 이동하여 이 정책을 설정할 수 있습니다.

 
  ![응용 프로그램](./media/active-directory-conditional-access-policy-connected-applications/01.png "응용 프로그램")


응용 프로그램을 선택한 후 **구성** 탭을 클릭하여 조건부 액세스 정책을 설정합니다.


  ![장치 기반 액세스 규칙](./media/active-directory-conditional-access-policy-connected-applications/02.png "장치 기반 액세스 규칙")


 

장치 기반 조건부 액세스 정책을 사용하도록 설정하려면 **장치 기반 액세스 규칙** 섹션에서 **액세스 규칙 사용**에 대해 **설정**을 선택합니다.

이 정책은 다음 세 가지 구성 요소로 구성됩니다.

1. **적용 대상** - 응용 프로그램에 액세스할 때 이 정책이 적용되는 대상 사용자의 범위입니다.

2. **장치 규칙** - 응용 프로그램에 액세스하기 전에 장치가 충족해야 하는 필수 조건입니다.

3. **응용 프로그램 적용** - 정책을 평가해야 하는 클라이언트 응용 프로그램(네이티브 대 브라우저)입니다.

  ![장치 기반 액세스 규칙](./media/active-directory-conditional-access-policy-connected-applications/03.png "장치 기반 액세스 규칙")
 

## 정책이 적용되는 사용자 선택 

**적용 대상** 섹션에서 이 정책이 적용되는 사용자 범위를 선택할 수 있습니다.

범위에 대한 두 가지 옵션이 있습니다.

- **모든 사용자** - 응용 프로그램에 액세스하는 모든 사용자

- **그룹** - 하나 이상의 그룹에 속해 있는 사용자로 범위 제한

![적용 대상](./media/active-directory-conditional-access-policy-connected-applications/11.png "적용 대상")


**제외**를 선택하면 응용 프로그램에 액세스하는 동안 이 정책에서 사용자를 제외할 수 있습니다. 이는 특정 사용자가 일시적으로 응용 프로그램에 액세스할 수 있도록 해야 하는 경우에 유용합니다. 예를 들어 일부 사용자가 조건부 액세스가 준비되지 않은(아직 등록되지 않았거나 규정을 준수하지 않는 등) 장치를 사용하는 경우에 이 옵션을 선택합니다.
 

## 장치가 준수해야 하는 조건 선택 

**장치 규칙**에서 장치가 응용 프로그램에 액세스할 수 있는 조건을 설정합니다.

장치 기반 조건부 액세스의 경우 다음 장치가 지원됩니다.

- Windows 10 1주년 업데이트, Windows 7 및 Windows 8.1

- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 및 Windows Server 2008 R2

- iOS 장치(iPad, iPhone)

- Android 장치

Mac은 곧 지원될 예정입니다.

  ![장치](./media/active-directory-conditional-access-policy-connected-applications/04.png "응용 프로그램")



 >[AZURE.NOTE] 도메인 가입과 Azure AD 가입의 차이점에 대한 자세한 내용은 [작업 공간에서 Windows 10 장치 사용](active-directory-azureadjoin-windows10-devices.md)을 참조하세요.


장치 규칙에 대한 두 가지 옵션이 있습니다.

- **모든 장치가 준수해야 합니다.** - 응용 프로그램에 액세스하는 모든 장치 플랫폼이 규정을 준수해야 합니다. 장치 기반 조건부 액세스를 지원하지 않는 플랫폼의 경우 장치의 액세스가 거부됩니다.

- **선택한 장치만 준수해야 합니다.** - 선택한 장치 플랫폼만 규정을 준수해야 합니다. 선택하지 않은 플랫폼 또는 응용 프로그램에 액세스하는 다른 플랫폼은 액세스가 허용됩니다.

  ![응용 프로그램](./media/active-directory-conditional-access-policy-connected-applications/05.png "응용 프로그램")



Azure AD 가입 장치는 Microsoft Intune 또는 Azure AD와 통합된 타사 MDM(모바일 장치 관리) 시스템에 의해 디렉터리에 **준수**로 표시된 경우 규정을 준수합니다.

도메인 가입 장치는 다음 두 가지 방법 중 하나로 규정을 준수합니다.

- Azure AD에 등록된 경우 많은 조직에서 신뢰할 수 있는 장치로 처리하는 도메인에 가입되었다는 사실

- System Center Configuration Manager 2016에 의해 Azure AD에 ‘준수’로 표시된 경우

 ![장치 규칙](./media/active-directory-conditional-access-policy-connected-applications/06.png "장치 규칙")
 

Windows 개인 장치는 Microsoft Intune 또는 Azure AD와 통합된 타사 MDM(모바일 장치 관리) 시스템에 의해 디렉터리에 **준수**로 표시된 경우 규정을 준수합니다.

비 Windows 장치는 Microsoft Intune에 의해 디렉터리에 **준수**로 표시된 경우 규정을 준수합니다.

 >[AZURE.NOTE] 관리 시스템에 의한 장치 규정 준수에 대해 Azure AD를 설정하는 방법에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.


특정 장치 플랫폼을 선택한 경우 Android, iOS, Windows Mobile(Windows 8.1 휴대폰 및 태블릿) 및 Windows(모든 Windows 10 장치를 포함하는 다른 모든 Windows 장치)를 포함하여 하나 이상의 옵션을 선택할 수 있습니다. 이 옵션을 사용하면 선택한 플랫폼에 대해서만 정책 평가가 발생합니다. 선택하지 않은 장치에서 액세스가 시도되는 경우에는 장치 정책이 평가되지 않고 장치가 허용됩니다.

![장치 규칙](./media/active-directory-conditional-access-policy-connected-applications/07.png "장치 규칙")
  

## 정책을 평가할 클라이언트 응용 프로그램의 유형 선택 

**응용 프로그램 적용** 섹션에서 정책을 평가해야 하는 응용 프로그램의 유형을 설정합니다.


응용 프로그램에 대한 두 가지 옵션이 있습니다.

- 브라우저 및 네이티브 응용 프로그램용
- 네이티브 응용 프로그램 전용


![응용 프로그램](./media/active-directory-conditional-access-policy-connected-applications/08.png "응용 프로그램")


**브라우저 및 네이티브 응용 프로그램용**을 선택하면 다음에 의한 응용 프로그램 액세스에 정책이 적용됩니다.

- 브라우저(예: Windows 10의 Edge, iOS의 Safari 등)
- 모든 플랫폼에서 ADAL(Active Directory 인증 라이브러리)을 사용하는 응용 프로그램 또는 Windows 10에서 WAM(웹 계정 관리자) API를 사용하는 응용 프로그램

>[AZURE.NOTE] 장치 기반 CA 보호 응용 프로그램에 액세스하는 최종 사용자에 대한 브라우저 지원 및 기타 고려 사항은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.

 

## Exchange Active Sync 기반 응용 프로그램의 전자 메일 액세스 방지 

Office 365 Exchange Online 응용 프로그램에는 **Exchange Activesync**라는 추가 섹션이 있습니다. 이 섹션에서는 Exchange Active Sync 기반 메일 응용 프로그램에 대한 전자 메일 액세스를 차단할 수 있습니다.

![응용 프로그램](./media/active-directory-conditional-access-policy-connected-applications/09.png "응용 프로그램")
 
![응용 프로그램](./media/active-directory-conditional-access-policy-connected-applications/10.png "응용 프로그램")

 
## 추가 항목

- [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0914_2016-->