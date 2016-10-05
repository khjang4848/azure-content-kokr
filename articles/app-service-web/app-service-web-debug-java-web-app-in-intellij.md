<properties 
	pageTitle="IntelliJ의 Azure에서 Java 웹앱 디버깅 | Microsoft Azure" 
	description="이 자습서에서는 IntelliJ용 Azure 도구 키트를 사용하여 Azure에서 실행되는 Java 웹앱을 디버깅하는 방법을 보여 줍니다." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="asirveda;robmcm"/>

# IntelliJ의 Azure에서 Java 웹앱 디버깅

이 자습서에서는 [IntelliJ용 Azure 도구 키트]를 사용하여 Azure에서 실행되는 Java 웹앱을 디버깅하는 방법을 보여 줍니다. 편의상 이 자습서에서는 기본 JSP (Java서버 페이지) 예제를 사용하지만 Azure에서 디버깅할 때 Java 서블릿에서도 비슷한 단계를 사용합니다.

이 자습서를 완료한 경우 응용 프로그램을 IntelliJ에 디버깅하면 다음 그림과 같이 표시됩니다.

![][01]
 
## 필수 조건

* JDK(Java 개발자 키트), v 1.8 이상.
* IntelliJ IDEA Ultimate Edition. <https://www.jetbrains.com/idea/download/index.html>에서 다운로드할 수 있습니다.
* Java 기반 웹 서버 또는 응용 프로그램 서버의 배포(예: Apache Tomcat 또는 Jetty)
* Azure 구독(<https://azure.microsoft.com/ko-KR/free/> 또는 <http://azure.microsoft.com/pricing/purchase-options/>에서 구입할 수 있음)
* IntelliJ용 Azure 도구 키트. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치]를 참조하세요.
* Azure 앱 서비스에서 만들고 배포된 동적 웹 프로젝트는 [IntelliJ에서 Azure용 Hello World 웹앱 만들기] 등을 참조하세요.

## Azure에서 Java 웹앱을 디버깅하려면

이 섹션에서 다음 단계를 완료하려면 Azure에서 Java 웹앱으로 이미 배포한 기존 동적 웹 프로젝트를 사용할 수 있습니다. [샘플 동적 웹 프로젝트]를 다운로드하고 [IntelliJ에서 Azure용 Hello World 웹앱 만들기]의 단계를 수행하여 Azure에 배포합니다.

1. IntelliJ에서 Azure에 배포된 Java 웹앱 프로젝트를 엽니다.

1. **실행** 메뉴를 클릭한 다음 **구성 편집...**을 클릭합니다.

    ![][02]

1. **실행/디버깅 구성** 대화 상자가 열리면 다음을 수행합니다.

    1. **Azure 웹앱**을 선택합니다.
    1. **+**를 클릭하여 새 구성을 추가합니다.
    1. 구성에 **이름**을 제공합니다.
    1. Azure 도구 키트가 제안하는 나머지 기본값을 적용한 다음 **확인**을 클릭합니다.

        ![][03]

1. 메뉴의 오른쪽 상단에서 방금 만든 Azure 웹앱 디버깅 구성을 선택하고 **디버깅**을 클릭합니다.

    ![][04]

1. **지금 원격 웹앱에서 원격 디버깅을 사용하나요?**라는 메시지가 나타나면 **확인**을 클릭합니다.

1. **이제 웹앱이 원격 디버깅할 준비가 되었습니다**라는 메시지가 나타나면 **확인**을 클릭합니다.

    ![][05]

1. 메뉴의 오른쪽 상단에서 방금 만든 Azure 웹앱 디버깅 구성을 선택한 다음 **디버깅**을 클릭합니다.

1. Windows 명령 프롬프트 또는 Unix 셸이 열리고 디버깅을 위해 필요한 연결을 준비합니다. 계속하기 전에 원격 Java 웹앱에 대한 연결이 성공할 때까지 대기해야 합니다. Windows를 사용하는 경우 다음 그림과 같이 표시됩니다.

    ![][06]

1. JSP 페이지의 중단점을 삽입한 다음 브라우저에서 Java 웹앱에 대한 URL을 엽니다.

    1. IntelliJ에서 **Azure 탐색기**를 엽니다.
    1. 디버깅하려는 **웹앱** 및 Java 웹앱으로 이동합니다.
    1. 웹앱을 마우스 오른쪽 단추로 클릭하고 **브라우저에서 열기**를 클릭합니다.
    1. 이제 IntelliJ는 디버깅 모드가 됩니다.

## 다음 단계

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터]를 참조하세요.

Azure 웹앱 만들기에 대한 자세한 내용은 [웹앱 개요]를 참조하세요.

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[IntelliJ용 Azure 도구 키트]: ../azure-toolkit-for-intellij.md
[IntelliJ용 Azure 도구 키트 설치]: ../azure-toolkit-for-intellij-installation.md
[IntelliJ에서 Azure용 Hello World 웹앱 만들기]: ./app-service-web-intellij-create-hello-world-web-app.md
[샘플 동적 웹 프로젝트]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java 개발자 센터]: https://azure.microsoft.com/develop/java/
[웹앱 개요]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->