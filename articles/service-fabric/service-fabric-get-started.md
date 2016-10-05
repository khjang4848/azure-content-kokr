<properties
   pageTitle="개발 환경 설정 | Microsoft Azure"
   description="런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 응용 프로그램을 빌드할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/13/2016"
   ms.author="ryanwi"/>

# 개발 환경 준비

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 개발 컴퓨터에서 [Azure 서비스 패브릭 응용 프로그램][1]을 빌드 및 실행하려면 런타임, SDK, 도구를 설치해야 합니다. 또한 SDK에 포함된 Windows PowerShell 스크립트의 실행을 사용하도록 설정해야 합니다.

## 필수 조건
### 지원되는 운영 체제 버전
개발을 위해 다음 운영 체제 버전이 지원됩니다.

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7은 기본적으로 Windows PowerShell 2.0만을 포함합니다. 서비스 패브릭 PowerShell cmdlet에는 PowerShell 3.0 이상이 필요합니다. Microsoft 다운로드 센터에서 [Windows PowerShell 5.0을 다운로드][powershell5-download]할 수 있습니다.

## 런타임, SDK 및 도구 설치

웹 플랫폼 설치 관리자는 Service Fabric 개발에 대한 두 가지 구성을 제공합니다.

- [Visual Studio 2015용 Service Fabric 런타임, SDK 및 도구 설치(Visual Studio 2015 업데이트 2 이상 필요)][full-bundle-vs2015]
- [서비스 패브릭 런타임 및 SDK만 설치(Visual Studio 도구 없음)][core-sdk]

## PowerShell 스크립트 실행 활성화

서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 응용 프로그램을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 다음 단계
개발 환경의 설정을 마쳤으므로 앱을 빌드하고 실행하기 시작할 수 있습니다.

- [Visual Studio에서 서비스 패브릭 응용 프로그램 처음 만들기](service-fabric-create-your-first-application-in-visual-studio.md)
- [로컬 클러스터에서 응용 프로그램을 배포하고 관리하는 방법 알아보기](service-fabric-get-started-with-a-local-cluster.md)
- [프로그래밍 모델에 대해 알아보기: Reliable Services 및 Reliable Actors](service-fabric-choose-framework.md)
- [GitHub의 서비스 패브릭 코드 샘플 확인](https://aka.ms/servicefabricsamples)
- [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)
- [서비스 패브릭 학습 경로를 따라 플랫폼에 대한 광범위한 소개 가져오기](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "서비스 패브릭 캠페인 페이지"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 링크"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 링크"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "핵심 SDK WebPI 링크"
[powershell5-download]: https://www.microsoft.com/ko-KR/download/details.aspx?id=50395

<!---HONumber=AcomDC_0928_2016-->