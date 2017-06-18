<properties 
	pageTitle="Windows 인증 및 Azure Multi-Factor Authentication 서버"
	description="Windows 인증 및 Azure Multi-Factor Authentication을 배포하는 데 도움이 되는 Azure Multi-Factor Authentication 페이지입니다."
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

# Windows 인증 및 Azure Multi-Factor Authentication 서버

Windows 인증 섹션에서 관리자는 하나 이상의 응용 프로그램에 대해 Windows 인증을 설정하고 구성할 수 있습니다. 다음은 Windows 인증을 설정하기 전에 고려해야할 사항의 목록입니다.

-  터미널 서비스에 대한 Azure Multi-Factor Authentication이 적용되기 전에 다시 부팅해야 합니다.
-  'Azure Multi-Factor Authentication 사용자 일치'을 선택하고 사용자 목록에 없는 경우, 다시 부팅하면 컴퓨터에 로그인할 수 없습니다.
-  신뢰할 수 있는 IP는 응용 프로그램이 클라이언트 IP에 인증을 제공할 수 있는지 여부에 따라 달라집니다. 현재는 터미널 서비스만 지원됩니다.







>[AZURE.NOTE]이 기능은 Windows Server 2012 R2에서 보안 터미널 서비스를 보안하는 데 지원되지 않습니다.




## Windows 인증을 사용하는 응용 프로그램을 보호하려면 다음 절차를 사용합니다.

1. Azure Multi-Factor Authentication 서버에서 Windows 인증 아이콘을 클릭합니다. ![Windows 인증](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Windows 인증 사용 확인란을 선택합니다. 이 상자는 기본적으로 선택되지 않습니다.
3. 응용 프로그램 탭에서 관리자는 Windows 인증을 위해 하나 이상의 응용 프로그램을 구성할 수 있습니다.
4. 서버 또는 응용 프로그램을 선택합니다. 서버/응용 프로그램이 사용되는지 여부를 지정합니다. 확인을 클릭합니다.
5. 추가... 단추를 클릭합니다.
6. 신뢰할 수 있는 IP 탭에서 특정 IP에서 오는 Windows용 Azure Multi-Factor Authentication 세션을 건너뛸 수 있습니다. 예를 들어, 직원이 사무실과 집에서 응용 프로그램을 사용하는 경우 사무실에 있는 동안 Azure Multi-Factor Authentication 확인용 전화를 원하지 않음을 결정할 수 있습니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다.
7. 추가... 단추를 클릭합니다.
8. 단일 IP 주소를 건너뛰려면 단일 IP를 선택합니다.
9. 전체 IP 범위를 건너뛰려면 IP 범위를 선택합니다. 예 10.63.193.1-10.63.193.100.
10. 서브넷 표기법을 사용하여 IP 범위를 지정하려면 서브넷을 선택합니다. 시작 IP 서브넷을 입력하고 드롭다운 목록에서 적절한 네트워크 마스크를 선택합니다.
11. 확인 단추를 클릭합니다.

<!---HONumber=AcomDC_0921_2016-->