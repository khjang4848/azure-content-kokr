<properties
    pageTitle="Azure RemoteApp 모범 사례 | Microsoft Azure"
    description="Azure RemoteApp 구성 및 사용 모범 사례"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# Azure RemoteApp 구성 및 사용 모범 사례

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

다음 정보는 Azure RemoteApp을 생산적으로 구성하고 사용하는 데 도움이 됩니다.

## 연결


- 항상 최신 클라이언트 버전을 사용합니다. 이전 클라이언트를 사용하면 연결 문제 및 기타 성능 저하가 발생할 수 있습니다. 장치에 자동 응용 프로그램 업데이트를 사용하면 항상 최신 클라이언트가 설치됩니다.
- 항상 사용 가능한 가장 안정적이고 신뢰할 수 있는 인터넷 연결을 사용합니다.
- 최적 연결 성능을 위해 지원되는 프록시 연결만 사용합니다. SOCKS 프록시는 지원되지 않습니다.

## 응용 프로그램


- 응용 프로그램에서 작업을 마치면 RemoteApp 응용 프로그램을 저장하고 닫습니다. 응용 프로그램을 닫지 않으면 데이터가 손실될 수도 있습니다.
- Azure RemoteApp에서 사용하기 전에 사용자 지정 응용 프로그램의 유효성을 검사합니다. 여기에는 다중 세션 플랫폼에서 작동하는지, 그리고 동일한 컬렉션의 다른 사용자가 이용할 수 없도록 메모리, CPU 등의 리소스를 불필요하게 소모하지 않는지 확인하는 작업이 포함됩니다. 자세한 내용을 보려면 [원격 데스크톱 서비스에 대한 응용 프로그램 호환성 모범 사례](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf)를 다운로드하여 검토하세요.

## 구성 및 관리


- 필요에 따라 소프트웨어 업데이트 및 기타 중요 수정 프로그램을 설치하여 템플릿 이미지를 최신 상태로 유지합니다. 그러면 Azure RemoteApp이 용량을 충족하기 위해 자동으로 크기를 조정할 때 각 인스턴스에 패치가 적용됩니다.
- AD FS(Active Directory Federation Services) 배포가 안전하고 신뢰할 수 있는지 확인합니다. 그렇지 않으면 클라이언트 인증이 실패하여 사용자가 Azure RemoteApp에 액세스하지 못할 수 있습니다.
- 설치된 응용 프로그램, 역할 또는 기능이 포함된 템플릿 이미지를 상태 비저장이 되도록 구성합니다. RemoteApp 서비스의 가상 컴퓨터 인스턴스가 영구 상태에 있는 것으로 간주해서는 안 됩니다.
	- 모든 사용자 데이터를 사용자 프로필이나 서비스 외부의 기타 저장소 위치(예: 온-프레미스 파일 공유 또는 OneDrive)에 저장합니다.
	- 공유 데이터를 서비스 외부의 기타 저장소 위치(예: 온-프레미스 파일 공유 또는 OneDrive)에 저장합니다.
	- 서비스의 개별 가상 컴퓨터 대신 템플릿 이미지에서 시스템 수준 설정을 구성합니다.
	- 게시된 응용 프로그램에 자동 소프트웨어 업데이트를 사용하지 않도록 설정합니다. 대신, 게시된 응용 프로그램을 템플릿 이미지에 수동으로 적용하고, 템플릿에서 배포하기 전에 테스트합니다.

<!---HONumber=AcomDC_0921_2016-->