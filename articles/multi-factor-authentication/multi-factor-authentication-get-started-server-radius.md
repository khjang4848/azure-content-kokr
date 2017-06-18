<properties 
	pageTitle="RADIUS 인증 및 Azure Multi-Factor Authentication 서버"
	description="RADIUS 인증 및 Azure Multi-Factor Authentication을 배포하는 데 도움이 되는 Azure Multi-Factor Authentication 페이지입니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>



# RADIUS 인증 및 Azure Multi-Factor Authentication 서버

RADIUS 인증 섹션에서 Azure Multi-Factor Authentication 서버에 대한 RADIUS 인증을 사용하도록 설정하고 구성할 수 있습니다. RADIUS는 인증 요청을 수락하고 이 요청을 처리하는 표준 프로토콜입니다. Azure Multi-Factor Authentication 서버는 RADIUS 서버 역할을 하고 RADIUS 클라이언트(예: VPN 어플라이언스)와 AD(Active Directory), LDAP 디렉터리 또는 다른 RADIUS 서버일 수 있는 인증 대상 사이에 삽입되어, Azure Multi-Factor Authentication을 추가합니다. Azure Multi-Factor Authentication이 동작하려면, 클라이언트 서버와 인증 대상 모두와 통신할 수 있도록 Azure Multi-Factor Authentication 서버를 구성해야 합니다. Azure Multi-Factor Authentication 서버는 RADIUS 클라이언트의 요청을 수락하고, 인증 대상에 대해 자격 증명의 유효성을 검사하고, Azure Multi-Factor Authentication을 추가하고 다시 RADIUS 클라이언트로 응답을 보냅니다. 기본 인증 및 Azure Multi-Factor Authentication 모두가 성공한 경우에만 전체 인증이 성공합니다.

>[AZURE.NOTE]
MFA 서버는 RADIUS 서버로 작동하는 경우 PAP(암호 인증 프로토콜)와 MSCHAPv2(Microsoft의 Challenge-Handshake 인증 프로토콜) RADIUS 프로토콜만 지원합니다. EAP(확장할 수 있는 인증 프로토콜) 같은 기타 프로토콜은 MFA 서버가 Microsoft NPS 같은 프로토콜을 지원하는 또 다른 RADIUS 서버에 대한 RADIUS 프록시로 작동하는 경우에 사용할 수 있습니다. </br> 이 구성에서 다른 프로토콜을 사용하는 경우 MFA 서버가 해당 프로토콜을 사용하여 성공적인 RADIUS Challenge 응답을 시작할 수 없기 때문에 단방향 SMS 및 OATH 토큰이 작동하지 않습니다.


![RADIUS 인증](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## RADIUS 인증 구성

RADIUS 인증을 구성하려면 Windows 서버에 Azure Multi-Factor Authentication 서버를 설치합니다. Active Directory 환경에 있으면 서버가 네트워크 내의 도메인에 가입되어 있어야 합니다. 다음 절차에 따라 Azure Multi-Factor Authentication 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 RADIUS 인증 아이콘을 클릭합니다.
2. RADIUS 인증 사용 확인란을 선택합니다.
3. 클라이언트 탭에서 구성될 클라이언트의 RADIUS 요청을 수신하려면 Azure Multi-Factor Authentication RADIUS 서비스가 비표준 포트에 바인딩해야 하는 경우 인증 포트 및 계정 포트를 변경합니다.
4. 추가... 단추를 클릭합니다.
5. RADIUS 클라이언트 추가 대화 상자에서 Azure Multi-Factor Authentication 서버, 응용 프로그램 이름(옵션) 및 공유 암호를 인증하는 어플라이언스/서버의 IP 주소를 입력합니다. 공유 암호는 Azure Multi-Factor Authentication 서버 및 어플라이언스/서버 모두에서 동일해야 합니다. 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
6. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 Multi-Factor Authentication을 사용하려는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
7. 사용자가 Azure Multi-Factor Authentication 모바일 앱 인증을 사용하고 대역외 전화 통화, SMS 또는 푸시 알림에 대한 대체(fallback) 인증으로 OATH 암호를 사용하려는 경우 대체 OATH 토큰 사용 확인란을 선택합니다.
8. 확인 단추를 클릭합니다.
9. 4-8 단계를 반복하여 추가 RADIUS 클라이언트를 추가할 수 있습니다.
10. 대상 탭을 클릭합니다.
11. Azure Multi-Factor Authentication 서버가 Active Directory 환경의 도메인 연결된 서버에서 실행 중인 경우 Windows 도메인을 선택합니다.
12. LDAP 디렉터리에 대해 사용자를 인증해야하는 경우, LDAP 바인딩을 선택합니다. LDAP 바인딩을 사용 하는 경우, 디렉터리 통합 아이콘을 클릭하고 서버가 디렉터리에 바인딩할 수 있도록 설정 탭에서 LDAP 구성을 편집해야 합니다. LDAP 구성을 위한 지침은 LDAP 프록시 구성 가이드에서 찾을 수 있습니다.
13. 다른 RADIUS 서버에 대해 사용자를 인증 해야하는 경우 RADIUS 서버를 선택합니다.
14. 추가... 단추를 클릭하여 서버가 RADIUS 요청을 프록시 처리하는 서버를 구성합니다.
15. RADIUS 서버 추가 대화 상자에서 RADIUS 서버의 IP 주소와 공유 암호를 입력합니다. 공유 암호는 Azure Multi-Factor Authentication 서버 및 RADIUS 서버 모두에서 동일해야 합니다. RADIUS 서버에서 다른 포트를 사용하는 경우 인증 포트 및 계정 포트를 변경합니다.
16. 확인 단추를 클릭합니다.
17. Azure Multi-Factor Authentication 서버에서 전송되는 액세스 요청을 처리할 수 있도록 다른 RADIUS 서버에서 RADIUS 클라이언트로 Azure Multi-Factor Authentication 서버를 추가해야 합니다. Azure Multi-Factor Authentication 서버에 구성된 동일한 공유 암호를 사용해야 합니다.
18. 이 단계를 반복하여 추가 RADIUS 서버를 추가하고 이동 및 아래로 이동 단추로 서버를 호출하는 순서를 구성할 수 있습니다. Azure Multi-Factor Authentication 서버 구성을 완료합니다. 이제 서버는 구성된 클라이언트에서의 RADIUS 액세스 요청에 대해 구성된 포트에서 수신합니다.


## RADIUS 클라이언트 구성

RADIUS 클라이언트를 구성하려면 지침을 따르십시오.

- RADIUS 서버 역할을 Azure Multi-Factor Authentication 서버의 IP 주소에 RADIUS를 통해 인증하도록 어플라이언스/서버를 구성합니다.
- 위의 구성된 동일한 공유 암호를 사용합니다.
- 사용자의 자격 증명의 유효성을 검사하고, multi-factor authentication을 수행하고, 자신의 응답을 수신한 다음 RADIUS 액세스 요청에 응답할 시간이 있도록 30-60 초 사이로 RADIUS 제한 시간을 구성합니다.

<!---HONumber=AcomDC_0921_2016-->