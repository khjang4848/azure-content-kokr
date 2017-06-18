<properties
   pageTitle="로컬 클러스터에서 앱 배포 및 업그레이드 시작 | Microsoft Azure"
   description="로컬 서비스 패브릭 클러스터를 설정하고 기존 응용 프로그램을 거기에 배포한 다음 해당 응용 프로그램을 업그레이드합니다."
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
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# 로컬 클러스터에서 응용 프로그램 배포 및 업그레이드 시작
Azure 서비스 패브릭 SDK에서는 전체 로컬 개발 환경을 포함하고 로컬 클러스터에서 응용 프로그램을 배포 및 관리하는 작업을 빠르게 시작하는 데 사용할 수 있습니다. 이 문서에서는 Windows PowerShell에서 로컬 클러스터를 만들고 기존 응용 프로그램을 거기에 배포한 다음 새 버전으로 해당 응용 프로그램을 업그레이드합니다.

> [AZURE.NOTE] 이 문서는 이미 [개발 환경을 설정](service-fabric-get-started.md)한 것으로 가정합니다.

## 로컬 클러스터 만들기
서비스 패브릭 클러스터는 응용 프로그램을 배포할 수 있는 하드웨어 리소스의 집합을 나타냅니다. 일반적으로 클러스터는 다섯 대부터 수천 대에 이르는 컴퓨터로 이루어져 있습니다. 그러나 서비스 패브릭 SDK는 단일 컴퓨터에서 실행할 수 있는 클러스터 구성을 포함합니다.

서비스 패브릭 로컬 클러스터는 에뮬레이터 또는 시뮬레이터가 아니라는 점을 이해하는 것이 중요합니다. 다중 컴퓨터 클러스터에 있는 동일한 플랫폼 코드를 실행합니다. 유일한 차이점은 한 컴퓨터에 다섯 대의 컴퓨터에 분산된 플랫폼 프로세스를 실행한다는 점입니다.

SDK는 Windows PowerShell 스크립트 및 로컬 클러스터 관리자 시스템 트레이 앱과 같이 로컬 클러스터를 설치하는 두 가지 방법을 제공합니다. 이 자습서에서는 PowerShell 스크립트를 사용합니다.

> [AZURE.NOTE] Visual Studio에서 응용 프로그램을 배포하여 이미 로컬 클러스터를 만든 경우 이 섹션을 건너뛸 수 있습니다.


1. 새 PowerShell 창을 관리자 권한으로 실행합니다.

2. SDK 폴더에서 클러스터 설치 스크립트를 실행합니다.

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    클러스터 설치는 몇 분 정도 걸립니다. 설치가 완료된 후 다음과 같은 출력이 표시됩니다.

    ![클러스터 설정 출력][cluster-setup-success]

    이제 클러스터에 응용 프로그램 배포를 시도할 준비가 되었습니다.

## 응용 프로그램 배포
서비스 패브릭 SDK는 응용 프로그램을 만들기 위한 다양한 개발자 도구 및 프레임워크의 집합을 포함합니다. Visual Studio에서 응용 프로그램을 만드는 방법에 관심이 있는 경우 [Visual Studio에서 서비스 패브릭 응용 프로그램 처음 만들기](service-fabric-create-your-first-application-in-visual-studio.md)를 참조하세요.

이 자습서에서는 기존 샘플 응용 프로그램(WordCount라고 함)을 사용하므로 배포, 모니터링 및 업그레이드를 포함하는 플랫폼의 관리 측면에 중점을 둘 수 있습니다.


1. 새 PowerShell 창을 관리자 권한으로 실행합니다.

2. 서비스 패브릭 SDK PowerShell 모듈을 가져옵니다.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. 다운로드하고 배포할 응용 프로그램을 저장할 디렉터리(예: C:\\ServiceFabric)를 만듭니다.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. 만든 위치에 [WordCount 응용 프로그램을 다운로드](http://aka.ms/servicefabric-wordcountapp)합니다. 참고: Microsoft Edge 브라우저는 *.zip*으로 확장된 파일을 저장합니다. 파일 확장명을 *.sfpkg*로 변경합니다.

5. 로컬 클러스터에 연결합니다.

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. 이름 및 응용 프로그램 패키지에 대한 경로와 함께 SDK의 배포 명령을 사용하여 새 응용 프로그램을 만듭니다.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    정상적으로 작동하면 다음과 유사한 출력이 표시됩니다.

    ![로컬 클러스터에 응용 프로그램 배포][deploy-app-to-local-cluster]

7. 작업에서 응용 프로그램을 보려면 브라우저를 시작하고 [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html)로 이동합니다. 다음과 같은 결과가 표시됩니다.

    ![배포된 응용 프로그램 UI][deployed-app-ui]

    WordCount 응용 프로그램은 매우 간단합니다. 클라이언트쪽 JavaScript 코드를 포함하여 임의의 다섯 개의 문자 "words"를 생성하며 이는 ASP.NET Web API를 통해 응용 프로그램에 릴레이됩니다. 상태 저장 서비스는 계산된 단어의 수를 추적합니다. 단어의 첫 번째 문자를 기준으로 분할됩니다. [샘플 시작](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/)에서 WordCount 앱에 대한 소스 코드를 찾을 수 있습니다.

    배포된 응용 프로그램은 네 개의 파티션을 포함합니다. 그러므로 A부터 G로 시작하는 단어는 첫 번째 파티션에 저장되고 H부터 N까지로 시작하는 단어는 두 번째 파티션에 저장되는 방식으로 계속됩니다.

## 응용 프로그램 세부 정보 및 상태 보기
응용 프로그램을 배포하였으므로 PowerShell의 앱 세부 정보 중 일부를 살펴보겠습니다.

1. 클러스터에 배포된 모든 응용 프로그램을 쿼리합니다.

    ```powershell
    Get-ServiceFabricApplication
    ```

    WordCount 앱만 배포했다고 가정하면 다음과 같은 메시지가 표시됩니다.

    ![PowerShell에 배포된 모든 응용 프로그램 쿼리][ps-getsfapp]

2. WordCount 응용 프로그램에 포함된 서비스 집합을 쿼리하여 다음 수준으로 이동합니다.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![PowerShell에서 응용 프로그램에 대한 서비스 목록][ps-getsfsvc]

    응용 프로그램은 단어를 관리하는 웹 프런트 엔드 및 상태 저장 서비스라는 두 서비스로 구성됩니다.

3. 마지막으로 WordCountService에 대한 파티션의 목록을 살펴봅니다.

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![PowerShell에서 서비스 파티션 보기][ps-getsfpartitions]

    모든 서비스 패브릭 PowerShell 명령처럼 사용된 명령 집합은 로컬 또는 원격에 연결할 수 있는 모든 클러스터에 사용할 수 있습니다.

    클러스터와의 상호 작용하는 보다 시각적인 방법의 경우 브라우저의 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)로 이동하여 웹 기반 서비스 패브릭 탐색기 도구를 사용할 수 있습니다.

    ![서비스 패브릭 탐색기에서 응용 프로그램 세부 정보 보기][sfx-service-overview]

    > [AZURE.NOTE] Service Fabric Explorer에 대해 자세히 알아보려면 [Service Fabric Explorer를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.

## 응용 프로그램 업그레이드
서비스 패브릭은 클러스터 전체에 걸쳐 롤백되므로 응용 프로그램의 상태를 모니터링하여 가동 중지 업그레이드가 발생하지 않습니다. WordCount 응용 프로그램의 간단한 업그레이드를 수행해보겠습니다.

응용 프로그램의 새 버전은 모음으로 시작하는 단어만 계산합니다. 업그레이드가 출시되면 응용 프로그램의 동작에 두 가지 변경 사항이 나타납니다. 첫째, 적은 단어 수가 계산되므로 수가 증가하면 속도는 느려져야 합니다. 둘째, 첫 번째 파티션에 두 개의 모음(A 및 E)이 있고 다른 모든 파티션이 각각 하나만 포함하므로 결국 개수가 다른 파티션을 앞서도록 시작해야 합니다.

1. v1 패키지를 다운로드한 동일한 위치에 [WordCount v2 패키지를 다운로드](http://aka.ms/servicefabric-wordcountappv2)합니다.

2. PowerShell 창으로 돌아가서 SDK의 업그레이드 명령을 사용하여 클러스터의 새 버전을 등록합니다. 그런 다음 fabric:/WordCount 응용 프로그램을 업그레이드하기 시작합니다.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    업그레이드가 시작되면 다음과 유사한 PowerShell의 출력이 표시됩니다.

    ![PowerShell에서 업그레이드 진행률][ps-appupgradeprogress]

3. 업그레이드를 진행하는 동안 서비스 패브릭 탐색기에서 해당 상태를 모니터링하는 작업이 쉬워집니다. 브라우저 창을 시작하고 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)로 이동합니다. 왼쪽의 트리에서 **응용 프로그램**을 선택한 다음 **WordCount**, **패브릭:/WordCount**를 차례로 선택합니다. 필수 탭에서 클러스터의 업그레이드 도메인을 통해 진행될 때 업그레이드의 상태가 표시됩니다.

    ![서비스 패브릭 탐색기에서 업그레이드 진행][sfx-upgradeprogress]

    각 도메인을 통해 업그레이드가 진행될 때 응용 프로그램이 제대로 작동되도록 상태 검사를 수행합니다.

4. fabric:/WordCount 응용 프로그램에 있는 서비스의 집합에 대한 이전 쿼리를 다시 실행하는 경우 WordCountService의 버전이 변경되지만 WordCountWebService 버전은 변경되지 않습니다.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![업그레이드 후 응용 프로그램 서비스 쿼리][ps-getsfsvc-postupgrade]

    이 서비스 패브릭은 응용 프로그램 업그레이드를 관리하는 방법을 보여줍니다. 이렇게 하면 이는 변경된 서비스 집합(또는 해당 서비스 내에서 코드/구성 패키지)을 터치하고 보다 빠르고 안정적으로 업그레이드 프로세스를 진행할 수 있습니다.

5. 마지막으로 브라우저로 돌아와서 응용 프로그램의 새 버전의 동작을 관찰합니다. 예상된 대로 개수 프로세스는 느려지고 첫 번째 파티션은 볼륨이 더 증가하게 됩니다.

    ![브라우저에서 응용 프로그램의 새 버전 보기][deployed-app-ui-v2]

## 정리

마무리하기 전에, 로컬 클러스터가 존재한다는 것을 기억하는 것이 중요합니다. 응용 프로그램은 제거될 때까지 백그라운드에서 계속 실행됩니다. 앱의 특성에 따라서, 앱을 실행하면 컴퓨터에서 상당한 리소스를 차지할 수 있습니다. 응용 프로그램과 클러스터를 관리하는 몇 가지 옵션이 있습니다.

1. 개별 응용 프로그램 및 모든 데이터를 제거하려면 다음을 실행합니다.

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    또는 Service Fabric Explorer의 **작업** 메뉴 또는 왼쪽 창의 응용 프로그램 목록 보기에 있는 상황에 맞는 메뉴에서 응용 프로그램을 제거합니다.

    ![서비스 패브릭 탐색기에서 응용 프로그램 삭제][sfe-delete-application]

2. 클러스터에서 응용 프로그램을 삭제한 후에 WordCount 응용 프로그램 형식의 버전 1.0.0 및 2.0.0의 등록을 취소할 수 있습니다. 삭제하면 클러스터 이미지 저장소에서 코드와 구성을 포함한 응용 프로그램 패키지를 제거합니다.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    또는 Service Fabric Explorer에서 응용 프로그램에 **프로비전 해제 형식**을 선택합니다.

3. 클러스터는 끄되 응용 프로그램 데이터와 추적은 유지하려면 시스템 트레이 앱에서 **로컬 클러스터 중지**를 클릭합니다.

4. 클러스터를 완전히 제거하려면 시스템 트레이 앱에서 **로컬 클러스터 제거**를 클릭합니다. 다음에 Visual Studio에서 F5 키를 누르면 이 옵션이 다른 느린 배포를 발생시킵니다. 일정 시간 동안 로컬 클러스터를 사용하지 않거나 또는 리소스를 확보해야 할 경우에만 로컬 클러스트를 제거합니다.

## 1개의 노드 및 5개의 노드 클러스터 모드

응용 프로그램을 개발하는 로컬 클러스터를 사용할 때 종종 코드를 작성, 디버깅, 변경 등을 빠르게 반복하여 수행합니다. 이 프로세스를 최적화할 수 있기 위해 로컬 클러스터는 1개의 노드 및 5개의 노드 등 두 가지 모드에서 실행할 수 있습니다. 클러스터 모드는 모두 해당되는 혜택이 있습니다. 5개의 노드 클러스터 모드를 사용하면 실제 클러스터와 함께 사용할 수 있습니다. 장애 조치 시나리오를 테스트하고 더 많은 인스턴스 및 서비스의 복제본을 사용할 수 있습니다. 1개의 노드 클러스터 모드는 서비스를 빠르게 배포하고 등록하는 데 최적화되어 Service Fabric 런타임을 사용하여 신속하게 코드의 유효성을 검사할 수 있습니다.

1개의 노드 클러스터 모드와 5개의 노드 클러스터 모드는 에뮬레이터나 시뮬레이터가 아닙니다. 다중 컴퓨터 클러스터에 있는 동일한 플랫폼 코드를 실행합니다.

> [AZURE.NOTE] 이 기능은 SDK 버전 5.2 이상에서 사용할 수 있습니다.

클러스터 모드를 1개의 노드 클러스터로 변경하려면 다음과 같은 방법으로 Service Fabric 로컬 클러스터 관리자 또는 PowerShell을 사용합니다.

1. 새 PowerShell 창을 관리자 권한으로 실행합니다.

2. SDK 폴더에서 클러스터 설치 스크립트를 실행합니다.

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
	```

    클러스터 설치는 몇 분 정도 걸립니다. 설치가 완료된 후 다음과 같은 출력이 표시됩니다.
    
    ![클러스터 설정 출력][cluster-setup-success-1-node]

Service Fabric 로컬 클러스터 관리자를 사용하는 경우:

![클러스터 모드 전환][switch-cluster-mode]

> [AZURE.WARNING] 클러스터 모드를 변경할 때 현재 클러스터가 시스템에서 제거되고 새 클러스터가 생성됩니다. 클러스터 모드를 변경할 때 클러스터에 저장해야 하는 데이터가 삭제됩니다.

## 다음 단계
- 미리 작성된 응용 프로그램을 배포하고 업그레이드했으니 [Visual Studio에서 응용 프로그램 빌드](service-fabric-create-your-first-application-in-visual-studio.md)를 수행해 보겠습니다.
- 이 문서의 로컬 클러스터에 수행된 작업은 모두 [Azure 클러스터](service-fabric-cluster-creation-via-portal.md)에서도 수행할 수 있습니다.
- 이 문서에서 수행된 업그레이드는 기본이었습니다. 서비스 패브릭 업그레이드의 성능과 유연성에 대해 자세히 알아보려면 [업그레이드 설명서](service-fabric-application-upgrade.md)를 참조하세요.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png

<!---HONumber=AcomDC_0921_2016-->