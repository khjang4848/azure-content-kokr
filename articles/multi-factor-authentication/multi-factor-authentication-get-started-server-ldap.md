<properties 
	pageTitle="LDAP 인증 및 Azure Multi-Factor Authentication 서버"
	description="LDAP 인증 및 Azure Multi-Factor Authentication 서버 배포에 도움이 되는 Azure Multi-Factor 인증 페이지입니다."
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
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# LDAP 인증 및 Azure Multi-Factor Authentication 서버


기본적으로 Azure Multi-Factor Authentication 서버는 Active Directory에서 사용자를 가져오거나 동기화하도록 구성됩니다. 그러나 ADAM 디렉터리 또는 특정 Active Directory 도메인 컨트롤러 같은 다른 LDAP 디렉터리에 바인딩하도록 구성할 수 있습니다. LDAP를 통해 디렉터리에 연결하도록 구성된 경우 Azure Multi-Factor Authentication 서버를 LDAP 프록시 역할을 하도록 구성하여 인증을 수행할 수 있습니다. 또한 RADIUS 대상으로서 LDAP 바인딩 사용, IIS 인증 사용 시 사용자의 사전 인증 또는 Azure Multi-Factor Authentication 사용자 포털에서 기본 인증이 가능합니다.

Azure Multi-Factor Authentication을 LDAP 프록시로 사용하는 경우 다단계 인증을 추가하기 위해 Azure Multi-Factor Authentication 서버가 LDAP 클라이언트(예: VPN 어플라이언스, 응용 프로그램)와 LDAP 디렉터리 서버 사이에 삽입됩니다. Azure Multi-Factor Authentication이 작동하려면 Azure Multi-Factor Authentication 서버가 클라이언트 서버 및 LDAP 디렉터리와 모두 통신할 수 있도록 구성되어야 합니다. 이 구성에서 Azure Multi-Factor Authentication 서버는 클라이언트 서버 및 응용 프로그램의 LDAP 요청을 수락하고 대상 LDAP 디렉터리 서버에 전달하여 기본 자격 증명의 유효성을 검사합니다. 기본 자격 증명이 유효하다는 LDAP 디렉터리의 응답이 나타나면 Azure Multi-Factor Authentication이 2단계 인증을 수행하고 다시 LDAP 클라이언트로 응답을 보냅니다. LDAP 서버 인증과 다단계 인증이 모두 성공하는 경우에만 전체 인증이 성공합니다.





## LDAP 인증 구성


LDAP 인증을 구성하려면 Windows 서버에 Azure Multi-Factor Authentication 서버를 설치합니다. 이렇게 하려면 다음 절차를 수행합니다.

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 LDAP 인증 아이콘을 클릭합니다.
2. LDAP 인증 사용 확인란을 선택합니다.![LDAP 인증](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. 구성되는 클라이언트의 LDAP 요청을 수신하기 위해 Azure Multi-Factor Authentication LDAP 서비스가 비표준 포트에 바인딩해야 하는 경우 클라이언트 탭에서 TCP 포트 및 SSL 포트를 변경합니다.
4. 클라이언트에서 Azure Multi-Factor Authentication 서버까지 LDAPS를 사용하려는 경우 서버가 실행되는 서버에 SSL 인증서가 설치되어야 합니다. SSL 인증서 상자 옆의 찾아보기... 단추를 클릭하고 보안 연결에 사용할 설치된 인증서를 선택합니다.
5. 추가... 단추를 클릭합니다.
6. Add LDAP Client(LDAP 클라이언트 추가) 대화 상자에서 서버 및 응용 프로그램 이름(선택 사항)을 인증할 응용 프로그램, 서버, 어플라이언스의 IP 주소를 입력합니다. 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
7. 모든 사용자를 서버로 가져왔거나 가져올 예정이고 다단계 인증의 적용을 받는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 매우 많은 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외하려면 이 확인란을 선택하지 않습니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
8. 추가 LDAP 클라이언트를 추가하려면 5-7 단계를 반복하면 됩니다.
9. Azure Multi-Factor Authentication이 LDAP 인증을 받도록 구성된 경우 해당 인증을 LDAP 디렉터리로 프록시해야 합니다. 따라서 대상 탭에는 LDAP 대상을 사용하기 위해 단일한 회색 옵션만 표시됩니다. LDAP 디렉터리 연결을 구성하려면 Directory Integration(디렉터리 통합) 아이콘을 클릭합니다.
10. 설정 탭에서 특정 LDAP 구성 사용 라디오 단추를 선택합니다.
11. 편집... 단추를 클릭합니다.
12. Edit LDAP Configuration(LDAP 구성 편집) 대화 상자에서 LDAP 디렉터리 연결에 필요한 정보를 필드에 입력합니다. 아래 표에는 필드에 대한 설명이 나와 있습니다. 참고: 이 내용은 Azure Multi-Factor Authentication 서버 도움말 파일에도 포함되어 있습니다.![디렉터리 통합](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. 테스트 단추를 클릭하여 LDAP 연결을 테스트합니다.
14. LDAP 연결 테스트가 성공한 경우 확인 단추를 클릭합니다.
15. 필터 탭을 클릭합니다. Active Directory에서 컨테이너, 보안 그룹 및 사용자를 로드하도록 서버가 미리 구성되어 있습니다. 다른 LDAP 디렉터리에 바인딩하는 경우 표시되는 필터를 편집해야 합니다. 필터에 대한 자세한 내용은 도움말 링크를 클릭합니다.
16. 특성 탭을 클릭합니다. Active Directory의 특성을 매핑하도록 서버가 미리 구성되어 있습니다.
17. 다른 LDAP 디렉터리에 바인딩하거나 미리 구성된 특성 매핑을 변경하려는 경우 편집... 단추를 클릭합니다.
18. Edit Attributes(특성 편집) 대화 상자에서 디렉터리에 대한 LDAP 특성 매핑을 수정합니다. 각 필드 옆에 있는 ... 단추를 클릭하면 특성 이름을 입력하거나 선택할 수 있습니다.
19. 특성에 대한 자세한 내용은 도움말 링크를 클릭합니다.
20. 확인 단추를 클릭합니다.
21. Company Settings(회사 설정) 아이콘을 클릭하고 Username Resolution(사용자 이름 확인) 탭을 선택합니다. 22.도메인에 가입된 서버에서 Active Directory에 연결하는 경우 사용자 이름 라디오 단추와 일치시키기 위해 Windows 보안 식별자(SID) 사용을 선택한 상태로 그대로 둘 수 있습니다. 그렇지 않으면 사용자 이름 라디오 단추와 일치시키기 위해 LDAP 고유 식별자 특성 사용을 선택합니다. 선택하면 Azure Multi-Factor Authentication 서버가 LDAP 디렉터리의 고유 식별자에 대한 각 사용자 이름을 확인하려고 시도합니다. LDAP 검색은 Directory Integration(디렉터리 통합) -> 특성 탭에서 정의된 사용자 이름 특성에 대해 수행됩니다. 사용자가 인증할 때 사용자 이름이 LDAP 디렉터리의 고유 식별자로 확인되고 고유 식별자는 Azure Multi-Factor Authentication 데이터 파일의 사용자를 일치시키기 위해 사용됩니다. 이를 통해 길고 짧은 사용자 이름 양식뿐만 아니라 대소문자 구분이 없는 비교도 가능합니다. 이렇게 해서 Azure Multi-Factor Authentication 서버 구성이 완료됩니다. 이제 서버는 구성된 클라이언트의 LDAP 액세스 요청에 대해 구성된 포트에서 수신하고, 인증을 위해 이러한 요청을 LDAP 디렉터리로 프록시하도록 설정됩니다.


## LDAP 클라이언트 구성

LDAP 클라이언트를 구성하려면 다음 지침을 사용합니다.

- 마치 LDAP 디렉터리인 것처럼 LDAP를 통해 Azure Multi-Factor Authentication 서버를 인증하도록 어플라이언스, 서버 또는 응용 프로그램을 구성합니다. 일반적으로 LDAP 디렉터리에 직접 연결하기 위해 사용하는 설정과 동일한 설정을 사용합니다. 단 Azure Multi-Factor Authentication 서버에 사용할 서버 이름 또는 IP 주소는 그렇지 않습니다.
- LDAP 디렉터리를 사용하여 사용자 자격 증명의 유효성을 검사할 시간이 있도록 LDAP 제한 시간을 30-60초로 구성하고 2단계 인증을 수행하고 응답을 받은 다음 LDAP 액세스 요청에 응답합니다.
- LDAPS를 사용하는 경우 LDAP 쿼리를 만드는 어플라이언스 또는 서버는 Azure Multi-Factor Authentication 서버에 설치된 SSL 인증서를 신뢰해야 합니다.

<!---HONumber=AcomDC_0921_2016-->