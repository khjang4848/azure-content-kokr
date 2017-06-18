<properties
	pageTitle="Azure Multi-Factor Authentication 정의 | Microsoft Azure"
	description="이 항목은 Multi-Factor Authentication(mfa)란 무엇인가에 대해 설명하고 MFA를 사용하는 이유, Multi-Factor Authentication 클라이언트 및 사용 가능한 다양한 방법과 버전에 대해 설명합니다. Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대해 한층 강화된 보안을 제공합니다."
	keywords="MFA 소개, mfa 개요, mfa 정의"
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
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication 정의
Multi-Factor Authentication(MFA)은 두 개 이상의 검증 방법을 사용해야 하며 사용자 로그인 및 트랜잭션에 중요한 제2의 보안 계층을 추가하는 인증 방법입니다. 이러한 인증에서는 다음 중 두 가지 이상의 검증 방법을 요구합니다.

- 사용자가 알고 있는 정보(일반적으로 암호)
- 사용자가 보유한 장치(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 장치)
- 사용자의 신원 정보(생체 인식)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 두번째 계층을 제공합니다.

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OATH 토큰과 같은 다양한 손쉬운 확인 옵션을 통해 강력한 인증을 전달합니다.

Azure Multi-Factor Authentication의 작동 원리 개요는 다음 비디오를 참조하세요.


>[AZURE.VIDEO multi-factor-authentication-overview]

##Azure Multi-Factor Authentication을 사용하는 이유

오늘날, 어느 때 보다 연결된 사람들이 늘어나고 있습니다. 스마트폰, 태블릿, 랩톱 및 PC를 사용하여, 사람들은 연결하고 언제든지 연결 상태를 유지하는 방법에 대한 여러 다른 옵션을 사용합니다. 사람들이 어디에서나 계정 및 응용 프로그램에 액세스할 수 있으며, 이는 더 많은 작업을 수행할 수 있고 고객에게 더 나은 서비스를 제공할 수 있음을 의미합니다.

Azure Multi-Factor Authentication은 인증의 두번째 메서드를 제공하여 사용자가 항상 보호되는 사용하기 쉽고 확장 가능한 신뢰할 수 있는 솔루션입니다.

![용이성](./media/multi-factor-authentication/simple.png)| ![확장성](./media/multi-factor-authentication/scalable.png)| ![항상 보호](./media/multi-factor-authentication/protected.png)|![안정성](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**용이성**|**확장성**|**항상 보호**|**안정성**

- **용이성** - Azure Multi-Factor Authentication은 간단하게 설정하고 사용할 수 있습니다. Azure Multi-Factor Authentication과 함께 제공되는 추가 보호를 사용하면 자신의 장치를 관리하고 대부분의 경우 간단한 몇 번 클릭으로 설치할 수 있습니다.
- **확장성** - Azure Multi-Factor Authentication은 클라우드의 강력한 클라우드의 기능을 이용하며 온-프레미스 AD와 사용자 지정 앱을 통합합니다. 이러한 보호는 고용량 업무상 중요 한 시나리오에도 확장됩니다.
- **항상 보호** -Azure Multi-Factor Authentication은 가장 높은 업계 표준을 사용하여 강력한 인증을 제공합니다.
- **안정성** -Azure Multi-Factor Authentication의 99.9%의 가용성을 보장합니다. Multi-Factor Authentication에 대한 인증 요청을 수신하거나 처리할 수 없는 경우 서비스는 사용할 수 없는 것으로 간주됩니다.

Azure Multi-Factor Authentication을 사용하는 이유에 대한 추가 정보는 다음 비디오를 참조하세요.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## Azure Multi-Factor Authentication 작동 방법

다단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 신뢰할 수 있는 장치가 없다면 어찌할 수 없습니다. 사용자가 장치를 분실한 경우에도 습득한 사람이 암호를 모르면 장치를 사용할 수 없습니다.

![검사](./media/multi-factor-authentication-how-it-works/howitworks.png)



간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다.

- 전화 통화
- 문자 메시지
- 모바일 앱 알림-사용자가 원하는 방법을 선택할 수 있도록 허용
- 모바일 앱 확인 코드
- 타사 OATH 토큰

작동 방법에 대한 추가 정보는 다음 비디오를 참조하세요.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## 다단계 인증에 사용할 수 있는 방법
사용자가 로그인하면 사용자에게 추가 확인이 전송됩니다. 이 두 번째 확인에 사용할 수 있는 방법의 목록은 다음과 같습니다.

확인 방법 | 설명
------------- | ------------- |
전화 통화 | 우물 정자를 눌러 로그인을 확인하도록 요청하는 통화가 사용자의 스마트폰으로 갑니다. 이것으로 확인 프로세스가 완료됩니다. 이 옵션은 구성 가능하고 지정한 코드로 변경할 수 있습니다.
문자 메시지 | 6자리 코드가 있는 문자 메시지가 사용자의 스마트폰으로 전송됩니다. 확인 프로세스를 완료하려면 이 코드를 입력합니다.
모바일 앱 알림 | 모바일 앱에서 확인을 선택하여 확인을 완료하라는 확인 요청이 사용자의 스마트폰에 전송됩니다. 기본 확인 방법으로 앱 알림을 선택한 경우 전송됩니다. 로그인하지 않았을 때 이 알림을 수신하는 경우 사기 행위로 보고하도록 선택할 수 있습니다.</li><br><p> [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.
모바일 앱을 통한 확인 코드 | 사용자의 스마트폰에서 실행되고 있는 모바일 앱으로 확인 코드가 전송됩니다. 기본 확인 방법으로 확인 코드를 선택한 경우 전송됩니다.</li><br><p> [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.


## Azure Multi-Factor Authentication의 사용 가능한 버전
세 가지 다른 버전으로 Azure Multi-Factor Authentication을 사용할 수 있습니다. 다음 표에서 각 항목에 대해 자세히 설명합니다.

버전 | 설명
------------- | ------------- |
Office 365용 Multi-Factor Authentication | 이 버전은 Office 365 응용프로그램에서만 작동되며 Office 365 포털에서 관리됩니다. 따라서 이제 관리자는 다단계 인증을 사용하여 Office 365 리소스의 보안을 유지할 수 있습니다. 이 버전은 Office 365 구독과 함께 제공됩니다.
Azure 관리자를 위한 Multi-Factor Authentication | 모든 Azure 관리자는 Office 365에 적용되는 것과 동일한 Multi-Factor Authentication 기능을 무료로 사용할 수 있습니다. Azure 구독의 모든 관리 계정은 이제 이 핵심 다단계 인증 기능을 사용하여 추가의 보호 기능을 얻을 수 있습니다. 따라서 VM 및 웹 사이트 만들기, 저장소나 모바일 서비스 또는 다른 Azure 서비스 관리를 위해 Azure 포털에 액세스하려는 관리자는 자신의 관리자 계정에 다단계 인증을 추가할 수 있습니다.
Azure Multi-Factor Authentication | Azure Multi-factor Authentication은 가장 다양한 기능을 제공합니다. Azure 관리 포털, 고급 보고, 온-프레미스 범위에 대한 지원 및 클라우드 응용프로그램을 통해 추가 구성 옵션을 제공합니다. Azure Multi-Factor Authentication은 Azure Active Directory Premium 및 Enterprise Mobility Suite의 일부로 제공됩니다.

## 버전 기능 비교
다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다.


기능 | Office 365용 Multi-Factor Authentication(Office 365 SKU에 포함됨)|Azure 관리자용 Multi-Factor Authentication(Azure 구독에 포함됨) | Azure Multi-Factor Authentication(Azure AD Premium 및 Enterprise Mobility Suite에 포함됨)
------------- | :-------------: |:-------------: |:-------------: |
관리자는 MFA를 사용하여 계정을 보호할 수 있습니다.| * | * (Azure 관리자 계정에 대해서만 사용 가능)|*
두 번째 단계로 모바일 앱|* | * | *
두 번째 단계로 전화 통화|* | * | *
두 번째 단계로 SMS|* | * | *
MFA를 지원하지 않는 클라이언트에 대한 앱 암호|* | * | *
인증 방법에 대한 관리자 제어| *|* | *
PIN 모드| | | *
사기 행위 경고| | | *
MFA 보고서| | | *
일회성 바이패스| | | *
전화 통화에 대한 사용자 지정 인사말| | | *
전화 통화에 대한 발신자 번호 사용자 지정| | | *
이벤트 확인| | | *
신뢰할 수 있는 IP| | | *
신뢰할 수 있는 장치에 대한 MFA 기억 |* | * | *
MFA SDK | | | * Multi-factor Auth 공급자 및 전체 Azure 구독 필요
MFA 서버를 사용한 온-프레미스 응용프로그램에 대한 MFA| | | *



## Azure Multi-Factor Authentication 획득 방법

Office 365 사용자와 Azure 관리자에게만 제공되는 기능 대신 Azure Multi-Factor Authentication에서 제공하는 전체 기능을 사용하려는 경우 몇 가지 옵션이 있습니다.

1.	Azure Multi-Factor Authentication 라이선스를 구매하여 사용자에게 할당합니다.
2.	Azure Active Directory Premium, Enterprise Mobility Suite 또는 Enterprise Cloud Suite 등 Azure Multi-Factor Authentication이 함께 제공되는 라이선스를 구매하여 사용자에게 할당합니다.
3.	Azure 구독 내에서 Azure Multi-Factor Authentication 공급자를 만듭니다. Azure 구독이 아직 없는 경우 Azure 평가판 사용에서 Azure 평가판 구독에 등록할 수 있습니다. 평가판이 만료되기 전 평가판 구독을 일반 구독으로 전환해야 합니다.

Azure Multi-Factor Authentication 제공자를 사용하는 경우 Azure 구독을 통해 청구되는 두 가지 사용 모델을 사용할 수 있습니다.


- **사용자당**. 규칙적으로 인증이 필요한 고정된 수의 직원에 대해 Multi-Factor Authentication을 사용하려는 기업에 적합합니다.
- **인증 당**. 비규칙적으로 인증이 필요한 큰 그룹의 외부 사용자에 대해 Multi-Factor Authentication을 사용하려는 기업에 적합합니다.

Azure Multi-Factor Authentication은 클라우드와 서버 모두에 대해 선택 가능한 확인 방법을 제공합니다. 즉, 다단계 인증을 사용할 때 사용자에게 제공할 방법을 선택할 수 있다는 것을 의미합니다. 이 기능은 현재 다단계 인증의 클라우드 버전에 대한 공개 미리 보기 상태입니다. 자세한 내용은 [선택 가능한 확인 방법](multi-factor-authentication-whats-next.md#selectable-verification-methods)을 참조하세요.

가격 책정에 대한 자세한 내용은 [Azure MFA 가격 책정](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)을 참조하십시오.

조직에 가장 적합한 사용자 단위 또는 소비 기반 모델을 선택합니다. 그런 다음 시작하려면 [시작](multi-factor-authentication-get-started.md)을 참조하세요.

## 다단계 보안 솔루션 선택

Azure Multi-Factor Authentication에는 여러 가지 버전이 있기 때문에 사용하기에 적절한 버전을 파악하기 위해 몇 가지 사항을 확인해야 합니다. 이러한 사항은 다음과 같습니다.

-	[보안을 유지하려는 대상은 무엇입니까](#what-am-i-trying-to-secure)
-	[사용자는 어디에 있습니까](#where-are-the-users-located)

다음 섹션에서는 이러한 각 결정에 대한 지침을 제공합니다.

### 보안을 유지하려는 대상은 무엇입니까?

올바른 다단계 인증 솔루션을 결정하기 위해 먼저 두 번째 인증 방법으로 보안을 유지하려는 대상이 무엇인지 답해야 합니다. Azure에 있는 응용프로그램 입니까? 아니면 예를 들어 원격 액세스 시스템입니까? 보안을 유지하려는 대상이 무엇인지 결정하여 다단계 인증 활성화가 필요한 곳에 대한 질문의 답을 확인합니다.



보안을 유지하려는 대상은 무엇입니까| 클라우드에서 Multi-Factor Authentication|Multi-Factor Authentication 서버
------------- | :-------------: | :-------------: |
자사 Microsoft 앱|* |* |
앱 갤러리의 Saas 앱|* |* |
Azure AD 응용프로그램 프록시를 통해 IIS 응용프로그램 게시됨|* |* |
Azure AD 응용프로그램 프록시를 통해 IIS 응용프로그램 게시되지 않음 | |* |
VPN, RDG와 같은 원격 액세스| |* |



### 사용자는 어디에 있습니까

다음으로 사용자의 위치에 따라 사용할 올바른 솔루션과 클라우드에 있는 다중 요소 인증인지 MFA 서버를 사용한 온-프레미스인지 여부를 확인할 수 있습니다.



사용자 위치| 해결 방법
------------- | :------------- |
Azure Active Directory| 클라우드에서 Multi-Factor Authentication|
Azure AD 및 AD FS로 페더레이션을 사용한 온-프레미스 AD| 클라우드의 MFA 및 MFA 서버 모두 사용 가능한 옵션입니다
Azure AD 및 DirSync를 사용한 온-프레미스 AD, Azure AD Sync, Azure AD Connect - 암호 동기화 없음|클라우드의 MFA 및 MFA 서버 모두 사용 가능한 옵션입니다
Azure AD 및 DirSync를 사용한 온-프레미스 AD, Azure AD Sync, Azure AD Connect - 암호 동기화 사용|클라우드에서 Multi-Factor Authentication
온-프레미스 Active Directory|Multi-Factor Authentication 서버

다음 표는 클라우드에서 Multi-Factor Authentication을 사용하는 경우와 Multi-Factor Authentication 서버를 사용하는 경우에 대한 기능 비교입니다.

 | 클라우드에서 Multi-Factor Authentication | Multi-Factor Authentication 서버
------------- | :-------------: | :-------------: |
두 번째 단계로 모바일 앱 알림 | ● | ● |
두 번째 단계로 모바일 앱 확인 코드 | ● | ●
두 번째 단계로 전화 통화 | ● | ●
두 번째 단계로 단방향 SMS | ● | ●
두 번째 단계로 양방향 SMS | | ●
두 번째 단계로 하드웨어 토큰 | | ●
MFA를 지원하지 않는 클라이언트에 대한 앱 암호 | ● |  
인증 방법에 대한 관리자 제어 | ● | ●
PIN 모드 | | ●
사기 행위 경고 | ● | ●
MFA 보고서 | ● | ●
일회성 바이패스 | | ●
전화 통화에 대한 사용자 지정 인사말 | ● | ●
전화 통화에 대한 사용자 지정 가능한 발신자 번호 | ● | ●
신뢰할 수 있는 IP | ● | ●
신뢰할 수 있는 장치에 대한 MFA 기억| ● |  
조건부 액세스 | ● | ●
캐시 | | ●

클라우드 다단계 인증 또는 MFA 서버 온-프레미스 사용 여부를 결정했으므로 Azure Multi-Factor Authentication을 설정하고 사용을 시작할 수 있습니다. **시나리오를 나타내는 아이콘을 선택하십시오!**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0921_2016-->