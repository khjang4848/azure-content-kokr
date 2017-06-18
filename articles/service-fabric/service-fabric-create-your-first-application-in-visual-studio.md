<properties
   pageTitle="Visual Studio에서 서비스 패브릭 응용 프로그램 처음 만들기 | Microsoft Azure"
   description="Visual Studio를 사용하여 서비스 패브릭 응용 프로그램 만들기, 배포 및 디버깅"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="ryanwi"/>


# 첫 번째 Azure Service Fabric 응용 프로그램 만들기

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

서비스 패브릭 SDK는 서비스 패브릭 응용 프로그램을 만들고, 배포하고, 디버그하는 도구를 제공하는 Visual Studio용 추가 기능을 포함합니다. 이 항목에서는 Visual Studio에서 응용 프로그램을 처음 만드는 과정을 안내합니다.

## 필수 조건

시작하기 전에 [개발 환경을 설정](service-fabric-get-started.md)하도록 합니다.

## 연습 동영상

다음 동영상은 이 자습서의 단계를 설명합니다.

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## 응용 프로그램 만들기

서비스 패브릭 응용 프로그램은 응용 프로그램의 기능을 제공하는 특정 역할이 있는 하나 이상의 서비스를 포함할 수 있습니다. 새 프로젝트 마법사를 사용하여 첫 번째 서비스 프로젝트와 함께 응용 프로그램 프로젝트를 만들 수 있습니다. 나중에 더 많은 서비스를 추가할 수 있습니다.

1. 관리자 권한으로 Visual Studio를 시작합니다.

2. **파일 > 새 프로젝트 > 클라우드 > 서비스 패브릭 응용 프로그램**을 클릭합니다.

3. 응용 프로그램 이름을 지정하고 **확인**을 클릭합니다.

	![Visual Studio의 새 프로젝트 대화 상자][1]

4. 다음 페이지에서 응용 프로그램에 포함할 첫 번째 서비스 형식으로 **상태 저장**을 선택합니다. 이름을 지정하고 **확인**을 클릭합니다.

	![Visual Studio의 새 서비스 대화 상자][2]

	>[AZURE.NOTE] 옵션에 대한 자세한 내용은 [서비스 패브릭 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조하세요.

	Visual Studio는 응용 프로그램 프로젝트 및 상태 저장 서비스 프로젝트를 만들고 솔루션 탐색기에 표시합니다.

	![상태 저장 서비스를 사용하여 응용 프로그램 만들기를 수행하는 솔루션 탐색기][3]

	응용 프로그램 프로젝트는 코드를 직접 포함하지 않습니다. 대신 서비스 프로젝트의 집합을 참조합니다. 추가로 3가지 다른 형식을 포함합니다.

	- **게시 프로필**: 다양한 환경에 대한 도구 기본 설정을 관리하는 데 사용됩니다.

	- **스크립트**: 응용 프로그램을 배포/업그레이드하기 위한 PowerShell 스크립트를 포함합니다. Visual Studio는 Visual Studio에서 스크립트 숨은 기능을 사용합니다. 명령줄에서 직접 스크립트를 호출할 수도 있습니다.

	- **응용 프로그램 정의**: *ApplicationPackageRoot*에서 응용 프로그램 매니페스트를 포함합니다. 관련된 응용 프로그램 매개 변수 파일은 *ApplicationParameters*에 있으며 응용 프로그램을 정의하고 지정된 환경에 대해 해당 응용 프로그램을 구체적으로 구성할 수 있습니다.

    서비스 프로젝트의 내용에 대한 개요는 [Reliable Services 시작](service-fabric-reliable-services-quick-start.md)을 참조하세요.

## 응용 프로그램 배포 및 디버깅

이제 응용 프로그램이 있으니 실행해 봅니다.

1. Visual Studio에서 F5 키를 눌러 응용 프로그램을 디버깅하기 위해 배포합니다.

	>[AZURE.NOTE] Visual Studio가 개발에 로컬 클러스터를 만들기 때문에 처음으로 배포하는 데 시간이 걸립니다. 로컬 클러스터는 단일 컴퓨터의 다중 컴퓨터 클러스터에 빌드된 동일한 플랫폼 코드를 실행합니다. Visual Studio 출력 창에 클러스터 만들기 상태가 표시됩니다.

	클러스터가 준비되면 SDK가 포함된 로컬 클러스터 시스템 트레이 관리자 응용 프로그램에서 알림을 받게 됩니다.

	![로컬 클러스터 시스템 트레이 알림][4]

2. 응용 프로그램이 시작되면 Visual Studio는 진단 이벤트 뷰어를 자동으로 표시하며 서비스에서 추적 출력을 확인할 수 있습니다.

	![진단 이벤트 뷰어][5]

	상태 저장 서비스 템플릿의 경우 메시지는 MyStatefulService.cs의 `RunAsync` 메서드에서 증가되는 카운터 갑을 표시합니다.

3. 코드가 실행되는 노드를 포함하여 자세한 정보를 보려면 이벤트 중 하나를 확장합니다. 이 경우에 _Node_2이지만 컴퓨터에서 달라질 수 있습니다.

	![진단 이벤트 뷰어 세부 정보][6]

	로컬 클러스터는 단일 컴퓨터에서 호스트되는 다섯 개의 노드를 포함합니다. 노드는 노드가 서로 다른 컴퓨터에 있는 5 노드 클러스터를 모방합니다. 컴퓨터의 손실을 시뮬레이션하는 동시에 Visual Studio 디버거를 실행하기 위해 로컬 클러스터의 노드 중 하나를 확인합니다.

    >[AZURE.NOTE] 프로젝트 템플릿에서 내보내는 응용 프로그램 진단 이벤트는 포함된 `ServiceEventSource` 클래스를 사용합니다. 자세한 내용은 [로컬로 서비스를 모니터링 및 진단하는 방법](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)을 참조하세요.

4. StatefulService(예: MyStatefulService)에서 파생되는 서비스 프로젝트에서 클래스를 찾고 `RunAsync` 메서드의 첫 줄에 중단점을 설정합니다.

	![상태 저장 서비스 RunAsync 메서드의 중단점][7]

5. Service Fabric Explorer를 시작하기 위해 로컬 클러스터 관리자 시스템 트레이 앱을 마우스 오른쪽 단추로 클릭하고 **로컬 클러스터 관리**를 선택합니다.

    ![로컬 클러스터 관리자에서 서비스 패브릭 탐색기 시작][systray-launch-sfx]

    Service Fabric Explorer는 클러스터의 시각적 표현을 제공하며 이는 여기에 배포된 응용 프로그램 집합 및 만드는 실제 노드 집합을 포함합니다. Service Fabric Explorer에 대해 자세히 알아보려면 [클러스터 시각화(영문)](service-fabric-visualizing-your-cluster.md)를 참조하세요.

6. 왼쪽된 창에서 **클러스터 > 노드**를 확장하고 코드가 실행될 노드를 찾습니다.

7. **작업 > 비활성화(다시 시작)**를 클릭하여 컴퓨터 다시 시작을 시뮬레이션합니다. (왼쪽 창에 있는 노드 목록 보기의 상황에 맞는 메뉴에서 비활성화할 수 있습니다.)

	![서비스 패브릭 탐색기에서 노드 중지][sfx-stop-node]

	한 노드에서 원활하게 수행한 계산이 다른 노드에 장애 조치되면 일시적으로 Visual Studio에서 중단점을 확인해야 합니다.

8. 진단 이벤트 뷰어로 돌아가서 메시지를 관찰합니다. 이벤트가 실제로 다른 노드에서 들어오더라도 카운터는 계속 증분합니다.

    ![장애 조치 후 진단 이벤트 뷰어][diagnostic-events-viewer-detail-post-failover]

## 클러스터 모드 전환

기본적으로 로컬 개발 클러스터는 여러 노드에 배포된 서비스를 디버깅하는 데 유용한 5개의 노드 클러스터로 실행되도록 구성됩니다. 그러나 응용 프로그램을 개발 5개의 노드 개발 클러스터에 배포하는 데는 시간이 걸릴 수 있습니다. 5개의 노드에서 앱을 실행하지 않고도 코드 변경 내용을 신속하게 반복하려는 경우 개발 클러스터를 1개의 노드 모드로 전환할 수 있습니다. 하나의 노드가 있는 클러스터에서 코드를 실행하려면 시스템 트레이에서 로컬 클러스터 관리자를 마우스 오른쪽 단추로 클릭하고 **클러스터 모드 전환 -> 1개의 노드**를 선택합니다.

![클러스터 모드 전환][switch-cluster-mode]

클러스터 모드를 변경하면 개발 클러스터가 다시 설정되고 프로비전되거나 클러스터에서 실행 중인 모든 응용 프로그램이 제거됩니다.

## 정리

  마무리하기 전에, 로컬 클러스터가 실제로 존재한다는 것을 기억하는 것이 중요합니다. 디버거를 중지하면 응용 프로그램 인스턴스를 제거하고 응용 프로그램 형식의 등록을 취소합니다. 하지만 클러스터는 백그라운드에서 계속 실행됩니다. 클러스터를 관리하는 몇 가지 옵션이 있습니다.

  1. 클러스터는 끄되 응용 프로그램 데이터와 추적은 유지하려면 시스템 트레이 앱에서 **로컬 클러스터 중지**를 클릭합니다.

  2. 클러스터를 완전히 제거하려면 시스템 트레이 앱에서 **로컬 클러스터 제거**를 클릭합니다. 다음에 Visual Studio에서 F5 키를 누르면 이 옵션이 다른 느린 배포를 발생시킵니다. 일정 시간 동안 로컬 클러스터를 사용하지 않거나 또는 리소스를 확보해야 할 경우에만 클러스터를 삭제합니다.

## 다음 단계

- [Azure에서 클러스터](service-fabric-cluster-creation-via-portal.md)를 만들거나 [Windows에서 독립 실행형 클러스터](service-fabric-cluster-creation-for-windows-server.md)를 만드는 방법을 알아봅니다.
- [Reliable Services](service-fabric-reliable-services-quick-start.md) 또는 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 서비스를 만들어 봅니다.
- [웹 서비스 프런트 엔드](service-fabric-add-a-web-frontend.md)를 사용하여 인터넷에 서비스를 노출할 수 있는 방법을 알아봅니다.
- [실습 교육](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx)을 안내하고 상태 비저장 서비스를 만들고 모니터링 및 상태 보고서를 구성하고 응용 프로그램 업그레이드를 수행합니다.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png

<!---HONumber=AcomDC_0928_2016-->