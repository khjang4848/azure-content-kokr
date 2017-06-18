<properties 
	pageTitle="Azure Multi-Factor Authentication - 작동 방법"
	description="간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다."
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

#Azure Multi-Factor Authentication 작동 방법

다단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 신뢰할 수 있는 장치가 없다면 어찌할 수 없습니다. 사용자가 장치를 분실한 경우에도 습득한 사람이 암호를 모르면 장치를 사용할 수 없습니다.

![검사](./media/multi-factor-authentication-how-it-works/howitworks.png)



간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다.

- 전화 통화
- 문자 메시지
- 모바일 앱 알림-사용자가 원하는 방법을 선택할 수 있도록 허용
- 모바일 앱 확인 코드
- 타사 OATH 토큰

작동 방법에 대한 추가 정보는 다음 비디오를 참조하십시오.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##다단계 인증에 사용할 수 있는 방법
사용자가 로그인하면 사용자에게 추가 확인이 전송됩니다. 이 두 번째 확인에 사용할 수 있는 방법의 목록은 다음과 같습니다.

확인 방법 | 설명
------------- | ------------- |
전화 통화 | 우물 정자를 눌러 로그인을 확인하도록 요청하는 통화가 사용자의 스마트폰으로 갑니다. 이것으로 확인 프로세스가 완료됩니다. 이 옵션은 구성 가능하고 지정한 코드로 변경할 수 있습니다.
문자 메시지 | 6자리 코드가 있는 문자 메시지가 사용자의 스마트폰으로 전송됩니다. 확인 프로세스를 완료하려면 이 코드를 입력합니다.
모바일 앱 알림 | 모바일 앱에서 확인을 선택하여 확인을 완료하라는 확인 요청이 사용자의 스마트폰에 전송됩니다. 기본 확인 방법으로 앱 알림을 선택한 경우 전송됩니다. 로그인하지 않았을 때 이 알림을 수신하는 경우 사기 행위로 보고하도록 선택할 수 있습니다.
모바일 앱을 통한 확인 코드 | 사용자의 스마트폰에서 실행되고 있는 모바일 앱으로 확인 코드가 전송됩니다. 기본 확인 방법으로 확인 코드를 선택한 경우 전송됩니다.


##Azure Multi-Factor Authentication의 사용 가능한 버전
세 가지 다른 버전으로 Azure Multi-Factor Authentication을 사용할 수 있습니다. 다음 표에서 각 항목에 대해 자세히 설명합니다.

버전 | 설명
------------- | ------------- |
Office 365용 Multi-Factor Authentication | 이 버전은 Office 365 응용프로그램에서만 작동되며 Office 365 포털에서 관리됩니다. 따라서 이제 관리자는 다단계 인증을 사용하여 Office 365 리소스의 보안을 유지할 수 있습니다. 이 버전은 Office 365 구독과 함께 제공됩니다.
Azure 관리자를 위한 Multi-Factor Authentication | 모든 Azure 관리자는 Office 365에 적용되는 것과 동일한 Multi-Factor Authentication 기능을 무료로 사용할 수 있습니다. Azure 구독의 모든 관리 계정은 이제 이 핵심 다단계 인증 기능을 사용하여 추가의 보호 기능을 얻을 수 있습니다. 따라서 VM 및 웹 사이트 만들기, 저장소나 모바일 서비스 또는 다른 Azure 서비스 관리를 위해 Azure 포털에 액세스하려는 관리자는 자신의 관리자 계정에 다단계 인증을 추가할 수 있습니다.
Azure Multi-Factor Authentication | Azure Multi-factor Authentication은 가장 다양한 기능을 제공합니다. <br><br>Azure 관리 포털, 고급 보고, 온-프레미스 범위에 대한 지원 및 클라우드 응용프로그램을 통해 추가 구성 옵션을 제공합니다. Azure Multi-Factor Authentication은 독립 실행형 라이선스로 구매할 수 있으며 Azure Active Directory Premium 및 Enterprise Mobility Suite와 함께 제공됩니다. <br><br>또한 Azure 구독에서 Azure Multi-Factor Authentication 공급자를 만들어 소비 기반으로 구매할 수 있습니다.
##버전 기능 비교
다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다.


기능 | Office 365용 Multi-Factor Authentication(Office 365 SKU에 포함됨)|Azure 관리자용 Multi-Factor Authentication(Azure 구독에 포함됨) | Azure Multi-Factor Authentication(Azure AD Premium 및 Enterprise Mobility Suite에 포함됨)
------------- | :-------------: |:-------------: |:-------------: |
관리자는 MFA를 사용하여 계정을 보호할 수 있습니다.| * | * (Azure 관리자 계정에 대해서만 사용 가능)|*
두 번째 단계로 모바일 앱|* | * | *
두 번째 단계로 전화 통화|* | * | *
두 번째 단계로 SMS|* | * | *
MFA를 지원하지 않는 클라이언트에 대한 앱 암호|* | * | *
인증 방법에 대한 관리자 제어| *| *| *
PIN 모드| | | *
사기 행위 경고| | | *
MFA 보고서| | | *
일회성 바이패스| | | *
전화 통화에 대한 사용자 지정 인사말| | | *
전화 통화에 대한 발신자 번호 사용자 지정| | | *
이벤트 확인| | | *
신뢰할 수 있는 IP| | | *
기억된 장치에 대한 MFA 일시 중단(공개 미리 보기)| | | *
MFA SDK| | | *
MFA 서버를 사용한 온-프레미스 응용프로그램에 대한 MFA| | | *


##Azure Multi-Factor Authentication 획득 방법

Office 365 사용자와 Azure 관리자에게만 제공되는 기능 대신 Azure Multi-Factor Authentication에서 제공하는 전체 기능을 사용하려는 경우 몇 가지 옵션이 있습니다.

1.	Azure Multi-Factor Authentication 라이선스를 구매하여 사용자에게 할당합니다.
2.	Azure Active Directory Premium 또는 Enterprise Mobility Suite 등 Azure Multi-Factor Authentication이 함께 제공되는 라이선스를 구매하여 사용자에게 할당합니다.
3.	Azure 구독 내에서 Azure Multi-Factor Authentication 공급자를 만듭니다. Azure 구독이 아직 없는 경우 Azure 평가판 사용에서 Azure 평가판 구독에 등록할 수 있습니다. 평가판이 만료되기 전 평가판 구독을 일반 구독으로 전환해야 합니다.

Azure Multi-Factor Authentication 제공자를 사용하는 경우 Azure 구독을 통해 청구되는 두 가지 사용 모델을 사용할 수 있습니다.


- **사용자당**. 규칙적으로 인증이 필요한 고정된 수의 직원에 대해 다단계 인증을 사용하려는 기업에 일반적입니다.
- **인증 당**. 비규칙적으로 인증이 필요한 큰 그룹의 외부 사용자에 대해 다단계 인증을 사용하려는 기업에 일반적입니다.

가격 책정에 대한 자세한 내용은 [Azure MFA 가격 책정](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)을 참조하십시오.

조직에 가장 적합한 사용자 단위 또는 소비 기반 모델을 선택합니다. 그런 다음 시작하려면 [시작](multi-factor-authentication-get-started.md)을 참조하십시오.

<!---HONumber=AcomDC_0921_2016-->