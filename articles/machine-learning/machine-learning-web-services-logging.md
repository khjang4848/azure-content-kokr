<properties 
	pageTitle="기계 학습 웹 서비스에 대한 로깅 | Microsoft Azure" 
	description="기계 학습 웹 서비스에 대해 로깅을 사용하도록 설정하는 방법을 알아봅니다. 로깅은 API 문제를 해결하는 데 도움이 되는 추가 정보를 제공합니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="08/09/2016"
	ms.author="raymondl;garye"/>

#기계 학습 웹 서비스에 대해 로깅 사용  

이 문서에서는 Azure ML 웹 서비스의 로깅 기능을 설명합니다. 웹 서비스에서 로깅을 사용하도록 설정하면 오류 번호와 메시지 외에 API 문제 해결에 도움이 되는 추가 정보가 제공됩니다.

-	웹 서비스에서 로깅을 사용하도록 설정하는 방법:
	-	[Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
	-	기계 학습을 클릭하고 작업 영역에서 웹 서비스 메뉴 옵션을 클릭합니다.
	-	웹 서비스 목록에서 웹 서비스의 이름을 클릭합니다.
	-	끝점 목록에서 끝점 이름을 클릭합니다.
	-	구성 메뉴 옵션을 클릭합니다.
	-	진단 추적 수준을 오류 또는 모두로 설정한 다음 하단 메뉴 모음에서 저장을 클릭합니다.
-	로깅 사용의 효과:
	-	로깅을 사용하도록 설정하면 선택한 끝점의 모든 진단/오류가 사용자 작업 영역과 연결된 Azure 저장소 계정에 기록됩니다. 이 저장소 계정은 작업 영역의 Azure 클래식 포털 대시보드 보기(간략 상태 섹션의 맨 아래)에서 확인할 수 있습니다.
-	로그를 보는 방법:
	-	Azure 저장소 계정을 '탐색'하는 데 사용할 수 있는 여러 도구 중 하나를 통해 로그를 볼 수 있습니다. 가장 쉬운 방법은 Azure 클래식 포털에서 저장소 계정으로 이동한 다음 컨테이너 탭을 클릭하는 것입니다. 그러면 **ml-diagnostics**라는 컨테이너가 표시됩니다. 이 컨테이너는 이 저장소 계정과 연결된 모든 작업 영역의 모든 웹 서비스 끝점에 대한 모든 진단 정보를 포함합니다.
-	로그 Blob 정보:
	-	컨테이너의 각 Blob은 정확히 다음 중 하나에 대한 진단 정보를 포함합니다.
		-	일괄 처리 실행 메서드 실행
		-	요청-응답 메서드 실행
		-	요청-응답 컨테이너 초기화
	-	각 Blob의 이름에는 {Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type} 형식의 접두사가 있습니다.
-	로그 유형은 다음 값 중 하나를 사용합니다.
	- batch
	- score/requests
	- score/init

 

<!---HONumber=AcomDC_0914_2016-->