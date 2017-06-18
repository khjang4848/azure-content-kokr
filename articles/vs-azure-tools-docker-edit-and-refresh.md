<properties
   pageTitle="로컬 Docker 컨테이너에서 앱 개발 | Microsoft Azure"
   description="로컬 Docker 컨테이너에서 실행 중인 앱을 수정하고, 편집 및 새로 고침을 통해 컨테이너를 새로 고치고, 디버깅 중단점을 설정하는 방법을 알아봅니다."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# 로컬 Docker 컨테이너에서 앱 디버깅

## 개요
Visual Studio Tools for Docker는 Linux Docker 컨테이너에서 로컬로 응용 프로그램을 개발하고 유효성을 검사하는 일관된 방법을 제공합니다. 코드를 변경할 때마다 컨테이너를 다시 시작할 필요가 없습니다. 이 문서에서는 "편집 및 새로 고침" 기능을 사용하여 로컬 Docker 컨테이너에서 ASP.NET Core 웹앱을 시작하고 필요한 내용을 변경한 다음 브라우저를 새로 고쳐 변경 내용을 확인하는 방법을 설명합니다. 또한 디버깅을 위한 중단점 설정 방법도 보여 줍니다.

> [AZURE.NOTE] Windows 컨테이너 지원은 향후 릴리스에서 제공됩니다.

## 필수 조건
다음과 같은 도구를 설치해야 합니다.

- [Visual Studio 2015 업데이트 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Visual Studio 2015 업데이트 3](https://go.microsoft.com/fwlink/?LinkId=691129) 설치
- [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

로컬에서 Docker 컨테이너를 실행하려면 로컬 Docker 클라이언트가 필요합니다. 릴리스된 [Docker 도구 상자](https://www.docker.com/products/overview#/docker_toolbox)는 Hyper-V를 비활성화한 후 사용할 수 있습니다. 또는 [윈도우용 Docke 베타](https://beta.docker.com)(Hyper-v를 사용하고 Windows 10을 필요로 함)를 사용할 수 있습니다.

Docker 도구 상자를 사용하는 경우 [Docker 클라이언트 구성](./vs-azure-tools-docker-setup.md)을 해야 합니다.

## 1\. 웹앱 만들기

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Docker 지원 추가

[AZURE.INCLUDE [docker 지원 추가](../includes/vs-azure-tools-docker-add-docker-support.md)]


## 3\. 코드 편집 및 새로 고침

변경을 신속하게 반복할 수 있도록, 응용 프로그램을 컨테이너에서 시작하고, IIS Express에서 하는 것처럼 변경 내용을 보며, 계속해서 변경해 나갈 수 있습니다.

1. 솔루션 구성을 `Debug`로 설정하고 **&lt;CTRL + F5>** 키를 눌러 Docker 이미지를 빌드하고 로컬에서 실행합니다.

    컨테이너 이미지가 빌드되고 Docker 컨테이너에서 실행되면 Visual Studio는 기본 브라우저에서 웹앱을 시작하려고 합니다. Microsoft Edge 브라우저를 사용하거나 달리 오류가 있는 경우 [문제 해결](vs-azure-tools-docker-troubleshooting-docker-errors.md) 섹션을 참조하세요.

1. 정보 페이지로 이동하며 정보 페이지에서 변경을 수행하게 됩니다.

1. Visual Studio로 돌아가서 `Views\Home\About.cshtml`을 엽니다.

1. 파일의 끝에 다음 HTML 콘텐츠를 추가하고 변경 내용을 저장합니다.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	출력창을 보는 가운데 .NET 빌드가 완료되고 이러한 줄이 표시될 때 브라우저로 전환하고 정보 페이지를 새로 고칩니다.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	변경 내용이 적용되었습니다.

## 4\. 중단점으로 디버깅

흔히 변경은 Visual Studio의 디버깅 기능을 활용한 추가적인 검사를 필요로 합니다.

1.	Visual Studio로 돌아가서 `Controllers\HomeController.cs`를 엽니다.

1.  About() 메서드 내용을 다음과 같이 바꿉니다.

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  `string message`... 줄 왼쪽에 중단점을 설정합니다.

1.  **&lt;F5>** 키를 눌러 디버깅을 시작합니다.

1.  중단점에 도달하려면 정보 페이지로 이동합니다.

1.  Visual Studio로 전환하여 중단점을 보고, 메시지의 값을 검사합니다.

	![][2]

##요약

[Visual Studio 2015용 Docker 도구](https://aka.ms/DockerToolsForVS)를 사용하여, Docker 컨테이너 내에서 개발하는 프로덕션 현실감과 함께 로컬에서 작업하는 생산성을 얻을 수 있습니다.

## 문제 해결

[Visual Studio Docker 개발 문제 해결](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Visual Studio, Windows 및 Azure와 함께 Docker에 대해 자세히 알아보기

- [Visual Studio용 Docker 도구](http://aka.ms/dockertoolsforvs) - 컨테이너에서 .NET Core 코드를 개발함
- [Visual Studio Team Services용 Docker 도구](http://aka.ms/dockertoolsforvsts) -docker 컨테이너를 빌드하고 배포함
- [Visual Studio Code용 Docker 도구](http://aka.ms/dockertoolsforvscode) - 향후 제공될 더 많은 E2E 시나리오와 함께, docker 파일을 편집하기 위한 언어 서비스
- [Windows 컨테이너 정보](http://aka.ms/containers)- Windows Server 및 Nano Server 정보
- [Azure 컨테이너 서비스](https://azure.microsoft.com/services/container-service/) - [Azure 컨테이너 서비스 콘텐츠](http://aka.ms/AzureContainerService)
-    Docker를 사용한 더 많은 예는 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [데모](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)의 [Docker 작업](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker)을 참조하세요. HealthClinic.biz 데모에서 더 빠른 시작은 [Azure 개발자 도구 빠른 시작](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)을 참조하세요.

## 다양한 Docker 도구

[일부 뛰어난 docker 도구 (Steve Lasker의 블로그)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## 좋은 문서

[NGINX의 마이크로 서비스 소개](https://www.nginx.com/blog/introduction-to-microservices/)

## 프레젠테이션

- [Steve Lasker: VS 라이브 Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [ASP.NET Core @ build 2016 소개 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810)
- [컨테이너에서 .NET 앱 개발, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0921_2016-->