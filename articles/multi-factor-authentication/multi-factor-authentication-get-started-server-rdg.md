<properties 
	pageTitle="RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버"
	description="RADIUS를 사용하여 RD(Remote Desktop) 게이트웨이 및 Azure Multi-Factor Authentication을 배포하는 데 도움이 되는 Azure Multi-Factor Authentication 페이지입니다."
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

# RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버

대부분의 경우, 원격 데스크톱 게이트웨이는 로컬 NPS를 사용하여 사용자를 인증합니다. 이 문서에서는 원격 데스크톱 게이트웨이(로컬 NPS를 통해)에서 Multi-Factor Authentication 서버까지 RADIUS 요청을 라우팅하는 방법을 설명합니다.

Multi-Factor Authentication 서버는 원격 데스크톱 게이트웨이 서버의 NPS로 RADIUS 요청을 다시 프록시 처리하는 별도 서버에 설치되어야 합니다. NPS는 사용자 이름 및 암호의 유효성을 검사한 후, 결과를 게이트웨이에 반환하기 전에 인증의 두번째 단계를 수행하는 Multi-Factor Authentication 서버에 대한 응답을 반환합니다.





## RD 게이트웨이 구성

Azure Multi-Factor Authentication 서버에 RADIUS 인증을 보내도록 RD 게이트웨이를 구성해야 합니다. RD 게이트웨이가 설치, 구성 및 작동되면 RD 게이트웨이 속성으로 이동합니다. RD CAP 저장소 탭으로 이동하여 NPS를 실행하는 로컬 서버가 아닌 NPS를 실행하는 중앙 서버를 사용하도록 변경합니다. 하나 이상의 Azure Multi-Factor Authentication 서버를 RADIUS 서버로 추가하고 각 서버에 대한 공유 암호를 지정합니다.





## NPS 구성

RD 게이트웨이는 NPS를 사용하여 Azure Multi-Factor Authentication에 RADIUS 요청을 보냅니다. Multi-Factor Authentication이 완료되기 전에 RD 게이트웨이가 제한 시간을 초과하지 않도록 제한 시간을 변경해야 합니다.multi-factor authentication NPS를 구성하려면 다음 절차를 따르십시오.

1. NPS의 왼쪽 열에서 RADIUS 클라이언트 및 서버 메뉴를 확장하고 원격 RADIUS 서버 그룹을 클릭합니다. TS 게이트웨이 서버 그룹의 속성으로 이동합니다. 표시되는 RADIUS 서버를 편집하고 부하 분산 탭으로 이동합니다. "응답 없이 다음 시간(초)이 경과되면 요청이 손실된 것으로 간주" 및 "서버가 사용 불가능 상태로 표시된 경우 요청 사이의 시간(초)"를 30-60초로 변경합니다. 인증/계정 탭을 클릭하고 지정된 RADIUS 포트가 Multi-Factor Authentication 서버에서 수신 대기할 포트와 일치하는지 확인합니다.
2. 또한 NPS는 Azure Multi-Factor Authentication 서버에서 다시 RADIUS 인증을 받도록 구성해야 합니다. 왼쪽 메뉴에서 RADIUS 클라이언트를 클릭합니다. Azure Multi-Factor Authentication 서버를 RADIUS 클라이언트로 추가합니다. 친숙한 이름을 선택하고 공유 암호를 지정합니다.
3. 왼쪽 탐색 창의 정책 섹션을 확장하고 연결 요청 정책을 클릭합니다. RD 게이트웨이를 구성할 때 만든 TS 게이트웨이 권한 부여 정책이라는 연결 요청 정책이 있어야 합니다. 이 정책은 Multi-Factor Authentication 서버에 RADIUS 요청을 전달합니다.
4. 새로 만들도록 이 정책을 복사합니다. 새 정책에서 Azure Multi-Factor Authentication 서버 RADIUS 클라이언트에 대한 위의 2단계에서 친숙한 이름과 클라이언트 이름이 일치하는 조건을 추가합니다. 로컬 컴퓨터에 인증 공급자를 변경합니다. 이 정책은 Azure Multi-Factor Authentication 서버에서 RADIUS 요청을 수신할 때 루프 조건이 될 수 있는 Azure Multi-Factor Authentication 서버에 RADIUS 요청을 전송하는 대신 로컬로 인증이 발생하도록 합니다. 루프 상황을 방지하기 위해, 이 새 정책은 Multi-Factor Authentication 서버에 전달하는 원래 정책 위에 정렬되어야 합니다.

## Azure Multi-Factor Authentication 구성


--------------------------------------------------------------------------------



Azure Multi-Factor Authentication 서버는 RD 게이트웨이 및 NPS 사이의 RADIUS 프록시로 구성됩니다. RD 게이트웨이 서버와 별개의 도메인에 가입된 서버에 설치해야 합니다. 다음 절차에 따라 Azure Multi-Factor Authentication 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버를 열고 RADIUS 인증 아이콘을 클릭합니다. RADIUS 인증 사용 확인란을 선택합니다.
2. 클라이언트 탭에서 포트가 NPS에 구성된 포트와 일치하는 지를 확인하고 추가... 단추를 클릭합니다. RD 게이트웨이 서버 IP 주소, 응용 프로그램 이름(선택 사항) 및 공유 암호를 추가합니다. 공유 암호는 Azure Multi-Factor Authentication 서버 및 RD 게이트웨이 모두에서 동일해야 합니다.
3. 대상 탭을 클릭하고 RADIUS 서버 라디오 단추를 선택합니다.
4. 추가... 단추를 클릭합니다. NPS 서버의 포트 및 공유 암호, IP 주소를 입력합니다. 중앙 NPS를 사용하지 않는 한 RADIUS 클라이언트와 RADIUS 대상은 동일합니다. 공유 암호는 NPS 서버에서 RADIUS 클라이언트 섹션의 설정 하나와 일치해야 합니다.

![RADIUS 인증](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=AcomDC_0921_2016-->