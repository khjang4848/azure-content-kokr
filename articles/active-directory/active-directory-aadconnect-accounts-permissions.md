<properties
   pageTitle="Azure AD Connect: 계정 및 사용 권한 | Microsoft Azure"
   description="이 항목에서는 사용되고 만든 계정 및 필요한 권한을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/13/2016"
   ms.author="andkjell;billmath"/>


# Azure AD Connect: 계정 및 사용 권한
Azure AD Connect 설치 마법사는 두 가지 다른 경로를 제공합니다.

- Express 설정에서 마법사는 사용자를 만들거나 사용 권한을 별도로 구성하지 않고도 구성을 쉽게 설정할 수 있도록 더 많은 권한이 필요합니다.

- 사용자 지정 설정에서 마법사는 더 많은 선택과 옵션을 제공하지만, 사용자가 올바른 사용 권한이 있는지 확인해야 하는 경우도 있습니다.

## 관련 설명서
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대한 설명서를 읽지 않은 경우 다음 테이블에서 관련 항목에 대한 링크를 제공합니다.

항목 |  
--------- | ---------
Express 설정을 사용하여 설치 | [Azure AD Connect의 빠른 설치](active-directory-aadconnect-get-started-express.md)
사용자 지정 설정을 사용하여 설치 | [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## Express 설정 설치
Express 설정에서는 온-프레미스 Active Directory를 Azure AD Connect에 필요한 사용 권한으로 구성하도록 AD DS 설치 마법사가 엔터프라이즈 관리자 자격 증명을 요청합니다. DirSync에서 업그레이드하는 경우 AD DS Enterprise 관리자 자격 증명은 DirSync에서 사용되는 계정의 암호를 다시 설정하는 데 사용됩니다. 또한 Azure AD 전역 관리자 자격 증명이 필요합니다.

마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한| 용도
------------- | ------------- |------------- |-------------
해당 없음|설치 마법사를 실행하는 사용자| 로컬 서버의 관리자| <li>[엔진 서비스 계정 동기화](#azure-ad-connect-sync-service-account)로 사용되는 로컬 계정을 만듭니다.
Azure에 연결| Azure AD 디렉터리 자격 증명 | Azure AD에서 글로벌 관리자 역할 | <li>Azure AD 디렉터리에서 동기화를 사용하도록 설정합니다.</li> <li>Azure AD에서 진행 중인 동기화 작업에 사용할 수 있는 [Azure AD 계정](#azure-ad-service-account)을 만드는 데 사용됩니다.</li>
AD DS에 연결 | 온-프레미스 Active Directory 자격 증명 | Active Directory의 Enterprise Admins(EA) 그룹의 구성원| <li>Active Directory에서 [계정](#active-directory-account)을 만들고 권한을 부여합니다. 만든 계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용됩니다.</li>

### 엔터프라이즈 관리자 자격 증명
이러한 자격 증명은 설치 중에 사용되고 설치가 완료된 후에 사용됩니다. 도메인 관리자가 아닌 엔터프라이즈 관리자가 모든 도메인에서 Active Directory에 사용 권한을 설정할 수 있습니다.

### 글로벌 관리자 자격 증명
이러한 자격 증명은 설치 중에 사용되고 설치가 완료된 후에 사용되지 않습니다. Azure AD에 대한 변경 내용을 동기화하는 데 사용된 [Azure AD 계정](#azure-ad-service-account)을 만드는 데 사용합니다. 또한 계정을 사용하면 Azure AD에서 기능으로 동기화할 수 있습니다.

### Express 설정에서 만들어진 AD DS 계정에 대한 권한
AD DS에 대해 읽고 쓰도록 만들어진 [계정](#active-directory-account)은 Express 설정에서 만들어질 때 다음과 같은 사용 권한이 있습니다.

사용 권한 | 용도
---- | ----
<li>디렉터리 변경 내용 복제</li><li>모든 디렉터리 변경 내용 복제 | 암호 동기화
모든 속성 사용자 읽기/쓰기 | 가져오기 및 Exchange 하이브리드
모든 속성 iNetOrgPerson 읽기/쓰기 | 가져오기 및 Exchange 하이브리드
모든 속성 그룹 읽기/쓰기 | 가져오기 및 Exchange 하이브리드
모든 속성 연락처 읽기/쓰기 | 가져오기 및 Exchange 하이브리드
암호 재설정 | 비밀번호 쓰기 저장을 사용하기 위한 준비

## 사용자 지정 설정 설치
사용자 지정 설정을 사용하는 경우 Active Directory에 연결하는 데 사용된 계정은 설치하기 전에 만들어야 합니다. 이 계정에 부여해야 하는 권한은 [AD DS 계정 만들기](#create-the-ad-ds-account)에서 찾을 수 있습니다.

마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한| 용도
------------- | ------------- |------------- |-------------
해당 없음 | 설치 마법사를 실행하는 사용자|<li>로컬 서버의 관리자</li><li>전체 SQL Server를 사용하는 경우 사용자는 SQL의 시스템 관리자(SA)여야 합니다</li>| 기본적으로 [엔진 서비스 계정 동기화](#azure-ad-connect-sync-service-account)로 사용되는 로컬 계정을 만듭니다. 계정은 관리자가 특정 계정을 지정하지 않은 경우에 만들어집니다.
동기화 서비스, 서비스 계정 옵션을 설치합니다. | AD 또는 로컬 사용자 계정 자격 증명 | 사용자, 권한은 설치 마법사에서 부여됩니다. | 관리자가 계정을 지정하는 경우, 이 계정은 동기화 서비스에 대한 서비스 계정으로 사용됩니다.
Azure에 연결 | Azure AD 디렉터리 자격 증명| Azure AD에서 글로벌 관리자 역할| <li>Azure AD 디렉터리에서 동기화를 사용하도록 설정합니다.</li> <li>Azure AD에서 진행 중인 동기화 작업에 사용할 수 있는 [Azure AD 계정](#azure-ad-service-account)을 만드는 데 사용됩니다.</li>
디렉터리에 연결 | Azure AD에 연결되는 각 포리스트의 온-프레미스 Active Directory 자격 증명 | 사용 권한은 어떤 기능을 사용하는지에 따라 달라지며 [AD DS 계정 만들기](#create-the-ad-ds-account)에서 찾을 수 있습니다. |계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용됩니다.
AD FS 서버 | 목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다. | 도메인 관리자 | AD FS 서버 역할 설치 및 구성
웹 응용 프로그램 프록시 서버 |목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다. | 대상 컴퓨터의 로컬 관리자 | WAP 서버 역할 설치 및 구성
프록시 트러스트 자격 증명 |페더레이션 서비스 자격 증명(FS에서 프록시가 신뢰 인증서를 등록하는 데 사용하는 자격 증명) |AD FS 서버의 로컬 관리자인 도메인 계정 | FS-WAP 신뢰 인증서의 초기 등록.
AD FS 서비스 계정 페이지에서 "도메인 사용자 계정 옵션 사용" | AD 사용자 계정 자격 증명 | 도메인 사용자 | 해당 자격 증명을 제공하는 AD 사용자 계정이 AD FS 서비스의 로그온 계정으로 사용됩니다.

### AD DS 계정 만들기
Azure AD Connect를 설치할 때 **디렉터리 연결** 페이지에 지정한 계정은 Active Directory에 있어야 하고 필요한 권한이 부여되어야 합니다. 설치 마법사는 사용 권한을 확인하지 않고 문제는 동기화 중에 발견됩니다.

어떤 사용 권한이 필요한지는 사용하도록 설정할 선택적 기능에 따라 달라집니다. 여러 도메인이 있는 경우 포리스트의 모든 도메인에 대한 사용 권한이 부여되어야 합니다. 이러한 기능을 사용하지 않는 경우 기본 **도메인 사용자** 사용 권한만으로도 충분합니다.

기능 | 권한
------ | ------
암호 동기화 | <li>디렉터리 변경 내용 복제</li> <li>모든 디렉터리 변경 복제
Exchange 하이브리드 배포 | 사용자, 그룹 및 연락처에 대한 [Exchange 하이브리드 쓰기 저장](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)에 설명된 특성에 사용 권한을 작성합니다.
비밀번호 쓰기 저장 | 사용자에 대한 [암호 관리 시작](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions)에 설명된 특성에 사용 권한을 작성합니다.
장치 쓰기 저장 | [장치 쓰기 저장](active-directory-aadconnect-feature-device-writeback.md)에 설명한 대로 PowerShell 스크립트에 부여된 사용 권한입니다.
그룹 쓰기 저장 | 배포 그룹을 찾을 수 있어야 하는 OU에서 그룹 개체를 읽기, 만들기, 업데이트 및 삭제합니다.

## 업그레이드
Azure AD Connect의 한 버전에서 새 릴리스로 업그레이드하는 경우 다음 권한이 필요합니다.

주체 | 필요한 사용 권한 | 용도
---- | ---- | ----
설치 마법사를 실행하는 사용자 | 로컬 서버의 관리자 | 이진을 업데이트합니다.
설치 마법사를 실행하는 사용자 | ADSyncAdmins의 구성원 | 동기화 규칙 및 기타 구성을 변경합니다.
설치 마법사를 실행하는 사용자 | 전체 SQL Server를 사용하는 경우, 동기화 엔진 데이터베이스의 DBO(또는 유사한 권한) | 새 열을 사용한 테이블 업데이트와 같이 데이터베이스 수준을 변경합니다.

## 만든 계정에 대한 자세한 내용

### Active Directory 계정
Express 설정을 사용하는 경우 계정은 동기화에 사용되는 Active Directory에서 만들어집니다. 만든 계정은 사용자 컨테이너의 포리스트 루트 도메인에 위치하고 **MSOL\_**를 접두사로 하는 이름을 갖습니다. 계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다. 사용자 도메인에 암호 정책이 있는 경우 길고 복잡한 암호가 이 계정에 대해 허용되는지 확인합니다.

![AD 계정](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Azure AD Connect 동기화 서비스 계정
로컬 서비스 계정은 설치 마법사에서 만듭니다.(사용자 지정 설정에 사용할 계정을 지정하지 않으면) 계정은 **AAD\__**를 접두사로 하며 실행할 실제 동기화 서비스에 사용됩니다. 도메인 컨트롤러에 Azure AD Connect를 설치하는 경우 계정은 도메인에 만들어집니다. SQL Server를 실행하는 원격 서버를 사용하거나 인증이 필요한 프록시를 사용하는 경우 **AAD\__** 서비스 계정이 도메인에 있어야 합니다.

![서비스 계정 동기화](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다.

이 계정은 다른 계정의 암호를 안전하게 저장하는 데 사용됩니다. 이러한 다른 계정 암호는 데이터베이스에 암호화된 상태로 저장됩니다. 암호화 키의 개인 키는 Windows DPAPI(데이터 보호 API)를 사용하여 암호화 서비스 비밀 키 암호화로 보호됩니다. 보안상의 이유로 Windows에서 암호화 키를 삭제할 수 있으므로 서비스 계정에서 암호를 다시 설정하면 안 됩니다.

전체 SQL Server를 사용하는 경우 서비스 계정은 동기화 엔진에 대해 만든 데이터베이스의 DBO입니다. 서비스는 다른 사용 권한이 의도한 대로 작동하지 않습니다. SQL 로그인도 생성됩니다.

또한 계정에는 동기화 엔진에 관련된 파일, 레지스트리 키 및 다른 개체에 대한 사용 권한이 부여됩니다.

### Azure AD 서비스 계정
Azure AD의 계정은 동기화 서비스의 사용에 생성됩니다. 이 계정은 표시 이름으로 식별할 수 있습니다.

![AD 계정](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

계정을 사용하는 서버의 이름은 사용자 이름의 두 번째 부분에서 식별할 수 있습니다. 그림에서 서버 이름은 FABRIKAMCON입니다. 준비 서버가 있는 경우 각 서버는 고유한 계정을 포함합니다. Azure AD에서 동기화 서비스 계정은 10개로 제한됩니다.

서비스 계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다. 또한 디렉터리 동기화 작업을 수행할 수 있는 유일한 권한이 있는 특별한 역할인 **디렉터리 동기화 계정**이 부여됩니다. 이 특별한 기본 제공 역할은 Azure AD Connect 마법사 외부에서 부여할 수 없으며, Azure 포털은 **사용자** 역할에서만 이 계정을 표시합니다.

![AD 계정 역할](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## 다음 단계

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0914_2016-->