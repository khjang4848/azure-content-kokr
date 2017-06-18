<properties
	pageTitle="AD FS 2.0과 함께 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지"
	description="Azure MFA 및 AD FS 2.0 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다."
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
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>
# AD FS 2.0과 함께 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지

조직이 Azure Active Directory와 페더레이션되고 보안을 유지하려는 리소스가 온-프레미스 또는 클라우드에 있는 경우 Azure Multi-Factor Authentication 서버를 사용하여 AD FS에서 작동하도록 구성함으로써 중요한 끝점에 대해 다단계 인증이 트리거되도록 하여 보안을 유지할 수 있습니다.

이 문서에서는 AD FS 2.0과 함께 Azure Multi-Factor Authentication 서버를 사용하는 방법을 소개합니다. Windows Serve 2012 R2 AD FS에서 Azure Multi-Factor Authentication을 사용하는 방법에 대한 자세한 내용은 [Windows Server 2012 R2 AD FS에서 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지](multi-factor-authentication-get-started-adfs-w2k12.md)를 참조하세요.


## AD FS 2.0 프록시
프록시로 AD FS 2.0 보안을 유지하려면 ADFS 프록시 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 단계에 따라 서버를 구성합니다.

### 프록시로 AD FS 2.0 보안을 유지하려면

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 IIS 인증 아이콘을 클릭합니다.
2. 양식 기반 탭을 클릭합니다.
3. 추가... 단추를 클릭합니다.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. 사용자 이름, 암호 및 도메인 변수를 자동으로 검색하려면 양식 기반 웹 사이트 자동 구성 대화 상자 내에 로그인 URL(예: https://sso.contoso.com/adfs/ls)을 입력하고 확인을 클릭합니다.
5. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증을 사용하려는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
6. 페이지 변수를 자동으로 검색할 수 없는 경우 Auto-Configure Form-Based Website(양식 기반 웹 사이트 자동 구성) 대화 상자에서 Specify Manually…(수동으로 지정...) 단추를 클릭합니다.
7. 양식 기반 웹 사이트 추가 대화 상자에서 URL 제출 필드에 ADFS 로그인 페이지의 URL(예: https://sso.contoso.com/adfs/ls)을 입력하고 응용 프로그램 이름을 입력합니다(선택 사항). 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다. URL 제출에 대한 자세한 내용은 도움말 파일을 참조하세요.
8. 요청 형식을 "POST 또는 GET"으로 설정합니다.
9. Username 변수(ctl00$ContentPlaceHolder1$UsernameTextBox) 및 Password 변수(ctl00$ContentPlaceHolder1$PasswordTextBox)를 입력합니다. 양식 기반 로그인 페이지에 도메인 텍스트 상자가 표시되면 Domain 변수도 입력합니다. 웹 브라우저에서 로그인 페이지로 이동해야 할 경우 해당 페이지를 마우스 오른쪽 단추로 클릭하고 "소스 보기"를 선택하여 로그인 페이지 내에서 입력 상자의 이름을 찾습니다.
10. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증을 사용하려는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. 고급... 단추를 클릭하여 사용자 지정 거부 페이지 파일을 선택하는 기능을 비롯한 고급 설정을 검토하고, 쿠키를 사용하여 특정 기간 동안 웹 사이트에 대한 성공적인 인증을 캐시하고, 기본 자격 증명을 인증하는 방법을 선택할 수 있습니다.
12. ADFS 프록시 서버는 도메인에 연결될 가능성이 낮으므로 LDAP를 사용하여 사용자 가져오기 및 사전 인증을 위해 도메인 컨트롤러에 연결할 수 있습니다. 고급 양식 기반 웹 사이트 대화 상자에서 기본 인증 탭을 클릭하고 사전 인증 인증 유형에 대해 "LDAP 바인딩"을 선택합니다.
13. 작업을 완료하면 확인 단추를 클릭하여 양식 기반 웹 사이트 추가 대화 상자로 돌아갑니다. 고급 설정에 대한 자세한 내용은 도움말 파일을 참조하세요.
14. 확인 단추를 클릭하여 대화 상자를 닫습니다.
15. URL 및 페이지 변수가 검색되거나 입력되면 양식 기반 패널에 웹 사이트 데이터가 표시됩니다.
16. 네이티브 모듈 탭을 클릭하고 서버, ADFS 프록시가 실행 중인 웹 사이트(예: "기본 웹 사이트") 또는 원하는 수준에서 IIS 플러그 인을 사용하도록 설정하기 위한 ADFS 프록시 응용 프로그램(예: "adfs" 아래의 "ls")을 선택합니다.
17. 화면 위쪽에서 IIS 인증 사용 상자를 클릭합니다.
18. 이제 IIS 인증이 사용되도록 설정되었습니다. 그러나 LDAP를 통해 AD(Active Directory)에 대한 사전 인증을 수행하려면 도메인 컨트롤러에 대한 LDAP 연결을 구성해야 합니다. 이 작업을 수행하려면 디렉터리 통합 아이콘을 클릭합니다.
19. 설정 탭에서 특정 LDAP 구성 사용 라디오 단추를 선택합니다.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. 편집... 단추를 클릭합니다.
21. LDAP 구성 편집 대화 상자에서 AD 도메인 컨트롤러에 연결하는 데 필요한 정보를 필드에 입력합니다. 아래 표에는 필드에 대한 설명이 나와 있습니다. 참고: 이 정보는 Azure Multi-Factor Authentication 서버 도움말 파일에도 포함되어 있습니다.
22. 테스트 단추를 클릭하여 LDAP 연결을 테스트합니다.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. LDAP 연결 테스트가 성공한 경우 확인 단추를 클릭합니다.
24. 다음으로 회사 설정 아이콘을 클릭하고 사용자 이름 확인 탭을 선택합니다.
25. 사용자 이름 일치에 LDAP 고유 식별자 특성 사용 라디오 단추를 선택합니다.
26. 사용자가 ADFS 프록시 로그인 양식에 "domain\\username" 형식으로 사용자 이름을 입력하는 경우 서버는 LDAP 쿼리를 만들 때 사용자 이름에서 도메인을 제거할 수 있어야 합니다. 이 작업은 레지스트리 설정을 통해 수행할 수 있습니다.
27. 64비트 서버에서 레지스트리 편집기를 열고 HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor로 이동합니다. 32비트 서버에서는 경로에 "Wow6432Node"를 지정합니다. "Usernamecxz\_stripprefixdomain"이라는 새 DWORD 레지스트리 키를 만들고 값을 1로 설정합니다. 이제 Azure Multi-Factor Authentication을 통해 ADFS 프록시 보안이 유지됩니다. Active Directory에서 서버로 사용자를 가져왔는지 확인합니다. 해당 위치에서 웹 사이트에 로그인할 때 2단계 인증이 필요하지 않도록 내부 IP 주소를 허용 목록에 추가하려면 아래의 신뢰할 수 있는 IP 섹션을 참조하세요.

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## 프록시 없이 AD FS 2.0 직접

AD FS 프록시가 사용되지 않을 때 AD FS 보안을 유지하려면 AD FS 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 단계에 따라 서버를 구성합니다.

### 프록시를 사용하지 않고 AD FS 2.0 보안을 유지하려면
1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 IIS 인증 아이콘을 클릭합니다.
2. HTTP 탭을 클릭합니다.
3. 추가... 단추를 클릭합니다.
4. 기준 URL 추가 대화 상자에서 HTTP 인증이 수행되는 ADFS 웹 사이트의 URL(예:https://sso.domain.com/adfs/ls/auth/integrated)을 기준 URL 필드에 입력하고 응용 프로그램 이름을 입력합니다(선택 사항). 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
5. 원하는 경우 유휴 시간 제한 및 최대 세션 시간을 조정합니다.
6. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증을 사용하려는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
7. 원하는 경우 쿠키 캐시 상자를 선택합니다.
<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. 확인 단추를 클릭합니다.
9. 네이티브 모듈 탭을 클릭하고 서버, ADFS가 실행 중인 웹 사이트(예: "기본 웹 사이트") 또는 원하는 수준에서 IIS 플러그 인을 사용하도록 설정하기 위한 ADFS 응용 프로그램(예: "adfs" 아래의 "ls")을 선택합니다.
10. 화면 위쪽에서 IIS 인증 사용 상자를 클릭합니다. 이제 Azure Multi-Factor Authentication을 통해 ADFS 보안이 유지됩니다. Active Directory에서 서버로 사용자를 가져왔는지 확인합니다. 해당 위치에서 웹 사이트에 로그인할 때 2단계 인증이 필요하지 않도록 내부 IP 주소를 허용 목록에 추가하려면 아래의 신뢰할 수 있는 IP 섹션을 참조하세요.


## 신뢰할 수 있는 IP
신뢰할 수 있는 IP를 사용하면 특정 IP 주소 또는 서브넷에서 시작된 웹 사이트 요청에 대한 Azure Multi-Factor Authentication을 바이패스할 수 있습니다. 예를 들어 사무실에서 로그인한 동안에는 사용자를 Azure Multi-Factor Authentication에서 제외시킬 수 있습니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다.

### 신뢰할 수 있는 IP를 구성하려면


1. IIS 인증 섹션에서 신뢰할 수 있는 IP 탭을 클릭합니다.
1. 추가... 단추를 클릭합니다.
1. 신뢰할 수 있는 IP 추가 대화 상자가 나타나면 단일 IP, IP 범위 또는 서브넷 라디오 단추를 선택합니다.
1. IP 주소, IP 주소 범위 또는 허용 목록에 추가할 서브넷을 입력합니다. 서브넷을 입력하는 경우 해당 네트워크 마스크를 선택하고 확인 단추를 클릭합니다. 이제 신뢰할 수 있는 IP가 추가되었습니다.


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=AcomDC_0921_2016-->