<properties 
	pageTitle="IIS 인증 및 Azure Multi-Factor Authentication 서버"
	description="IIS 인증 및 Azure Multi-Factor Authentication 서버 배포에 도움이 되는 Azure Multi-Factor 인증 페이지입니다."
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

# IIS 인증

Azure Multi-Factor Authentication 서버의 IIS 인증 섹션을 통해 Microsoft IIS 웹 응용 프로그램과의 통합을 위한 IIS 인증을 사용하도록 설정하고 구성할 수 있습니다. Azure Multi-Factor Authentication 서버는 Azure Multi-Factor Authentication을 추가하기 위해 IIS 웹 서버에 요청하는 사항을 필터링할 수 있는 플러그인을 설치합니다. IIS 플러그인은 양식 기반 인증 및 통합 Windows HTTP 인증을 지원합니다. 신뢰할 수 있는 IP는 2단계 인증에서 내부 IP 주소를 제외하도록 구성할 수도 있습니다.


![IIS 인증](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Azure Multi-Factor Authentication 서버에서 양식 기반 IIS 인증 사용

양식 기반 인증을 사용하는 IIS 웹 응용 프로그램을 보호하려면 IIS 웹 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 절차에 따라 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 IIS 인증 아이콘을 클릭합니다.
2. 양식 기반 탭을 클릭합니다.
3. 추가... 단추를 클릭합니다.
4. 사용자 이름, 암호 및 도메인 변수를 자동으로 검색하려면 Auto-Configure Form-Based Website(양식 기반 웹 사이트 자동 구성) 대화 상자 내에서 로그인 URL(예: https://localhost/contoso/auth/login.aspx)을 입력하고 확인을 클릭합니다.
5. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증의 적용을 받는 경우 Require Multi-Factor Authentication user match(Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다.
6. 페이지 변수를 자동으로 검색할 수 없는 경우 Auto-Configure Form-Based Website(양식 기반 웹 사이트 자동 구성) 대화 상자에서 Specify Manually…(수동으로 지정...) 단추를 클릭합니다.
7. Add Form-Based Website(양식 기반 웹 사이트 추가) 대화 상자에서 Submit URL(URL 제출) 필드에 로그인 페이지의 URL을 입력하고 응용 프로그램 이름을 입력합니다(선택 사항). 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다. URL 제출에 대한 자세한 내용은 도움말 파일을 참조하세요.
8. 올바른 요청 양식을 선택합니다. 대부분의 웹 응용 프로그램에 대해 "POST 또는 GET"으로 설정됩니다.
9. 사용자 이름 변수, 암호 변수 및 도메인 변수를 입력합니다(로그인 페이지에 나타나는 경우). 웹 브라우저에서 로그인 페이지로 이동해야 할 경우 해당 페이지를 마우스 오른쪽 단추로 클릭하고 "소스 보기"를 선택하여 페이지 내에서 입력 상자의 이름을 찾습니다.
10. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증을 사용하려는 경우 Require Azure Multi-Factor Authentication user match(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
11.  고급... 단추를 클릭하여 사용자 지정 거부 페이지 파일을 선택하거나 쿠키를 사용하여 특정 기간 동안 웹 사이트에 대한 성공적인 인증을 캐시하거나 Windows 도메인, LDAP 디렉터리 또는 RADIUS 서버에 대해 기본 자격 증명의 인증 여부를 선택하는 기능을 비롯한 고급 설정을 검토합니다. 작업을 완료하면 확인 단추를 클릭하여 Add Form-Based Website(양식 기반 웹 사이트 추가) 대화 상자로 돌아갑니다. 고급 설정에 대한 자세한 내용은 도움말 파일을 참조하세요.
12. 확인 단추를 클릭합니다.
13. URL 및 페이지 변수가 검색되거나 입력되면 양식 기반 패널에 웹 사이트 데이터가 표시됩니다.
14. IIS 인증 구성을 완료하기 위해 바로 아래 나와 있는 Azure Multi-Factor Authentication 서버에 대해 IIS 플러그인 사용 섹션을 참조하세요.

## Azure Multi-Factor Authentication 서버에서 Windows 통합 인증 사용

통합 Windows HTTP 인증을 사용하는 IIS 웹 응용 프로그램을 보호하려면 IIS 웹 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 절차에 따라 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 IIS 인증 아이콘을 클릭합니다.
2. HTTP 탭을 클릭합니다. 양식 기반 탭을 클릭합니다.
3. 추가... 단추를 클릭합니다.
4. Add Base URL(기준 URL 추가) 대화 상자에서 Base URL(기준 URL) 필드에 HTTP 인증이 수행되는 웹 사이트의 URL(예:http://localhost/owa)을 입력하고 응용 프로그램 이름을 입력합니다(선택 사항). 응용 프로그램 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
5. 기본값이 충분하지 않으면 유휴 제한 시간 및 최대 세션 시간을 조정합니다.
6. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증의 적용을 받는 경우 Require Multi-Factor Authentication user match(Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 도움말 파일을 참조하세요.
7. 원하는 경우 쿠키 캐시 상자를 선택합니다.
8. 확인 단추를 클릭합니다.
9. IIS 인증 구성을 완료하기 위해 바로 아래 나와 있는 [Azure Multi-Factor Authentication 서버에 대해 IIS 플러그인 사용](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) 섹션을 참조하세요.


## Azure Multi-Factor Authentication 서버에 대해 IIS 플러그인 사용

양식 기반 또는 HTTP 인증 URL 및 설정을 구성했으면 Azure Multi-Factor Authentication IIS 플러그인을 로드하고 IIS에서 사용하도록 설정할 위치를 선택해야 합니다. 이렇게 하려면 다음 절차를 수행합니다.

1. IIS 6에서 실행하는 경우 ISAPI 탭을 클릭하고 웹 응용 프로그램이 실행되고 있는 웹 사이트(예: 기본 웹 사이트)를 선택하여 해당 사이트에 대해 Azure Multi-Factor Authentication ISAPI 필터 플러그인을 사용하도록 설정합니다.
2. IIS 7 이상에서 실행하는 경우 네이티브 모듈 탭을 클릭하고 서버, 웹 사이트 또는 응용 프로그램을 선택하여 원하는 수준에서 IIS 플러그인을 사용하도록 설정합니다.
3. 화면 위쪽에서 IIS 인증 사용 상자를 클릭합니다. 이제 Azure Multi-Factor Authentication이 선택된 IIS 응용 프로그램을 보호합니다. 사용자를 서버에 가져왔는지 확인합니다. 해당 위치에서 웹 사이트에 로그인할 때 2단계 인증이 필요하지 않도록 내부 IP 주소를 허용 목록에 추가하려면 아래의 신뢰할 수 있는 IP 섹션을 참조하세요.


## 신뢰할 수 있는 IP

신뢰할 수 있는 IP를 사용하면 특정 IP 주소 또는 서브넷에서 시작된 웹 사이트 요청에 대한 Azure Multi-Factor Authentication을 바이패스할 수 있습니다. 예를 들어 사무실에서 로그인한 동안에는 사용자를 Azure Multi-Factor Authentication에서 제외시킬 수 있습니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다. 신뢰할 수 있는 IP를 구성하려면 다음 절차를 사용합니다.

1. IIS 인증 섹션에서 신뢰할 수 있는 IP 탭을 클릭합니다.
2. 추가... 단추를 클릭합니다.
3. 신뢰할 수 있는 IP 추가 대화 상자가 나타나면 단일 IP, IP 범위 또는 서브넷 라디오 단추를 선택합니다.
4. 허용 목록에 추가할 IP 주소, IP 주소 범위 또는 서브넷을 입력합니다. 서브넷을 입력하는 경우 해당 네트워크 마스크를 선택하고 확인 단추를 클릭합니다. 이제 허용 목록이 추가되었습니다.

<!---HONumber=AcomDC_0921_2016-->