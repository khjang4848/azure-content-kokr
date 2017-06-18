<properties
   pageTitle="Visual Studio에서 응용 프로그램 관리 | Microsoft Azure"
   description="Visual Studio를 사용하여 서비스 패브릭 응용 프로그램과 서비스를 만들고, 개발하고, 배포하며 디버그합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# Visual Studio를 사용하여 서비스 패브릭 응용 프로그램 쓰기 및 관리 단순화하기

Visual Studio를 통해 Azure 서비스 패브릭 응용 프로그램 및 서비스를 관리할 수 있습니다. [개발 환경을 설정](service-fabric-get-started.md)한 후, Visual Studio를 사용하여 로컬 개발 클러스터에서 서비스 패브릭 응용 프로그램을 만들거나 서비스를 추가하거나 응용 프로그램의 패키징, 등록 및 배포를 수행할 수 있습니다.

## 서비스 패브릭 응용 프로그램 배포

기본적으로 응용 프로그램을 배포하는 작업은 다음 단계를 하나의 간단한 작업으로 통합합니다.

1. 응용 프로그램 패키지 만들기
2. 이미지 저장소에 응용 프로그램 패키지 업로드
3. 응용 프로그램 유형 등록
4. 실행 중인 모든 응용 프로그램 인스턴스 제거
5. 새 응용 프로그램 인스턴스 만들기

Visual Studio에서 **F5** 키를 눌러도 응용 프로그램이 배포되고 모든 응용 프로그램 인스턴스에 디버거가 첨부됩니다. **Ctrl+F5**를 사용하여 디버그하지 않고 응용 프로그램을 배포하거나, 게시 프로필을 사용하여 로컬 또는 원격 클러스터에 게시할 수 있습니다. 자세한 내용은 [Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 게시](service-fabric-publish-app-remote-cluster.md)를 참조하세요.

### 응용 프로그램 디버그 모드

기본적으로 Visual Studio는 디버깅을 중지하거나(디버거를 연결하지 않고 앱을 배포한 경우) 응용 프로그램을 재배포하는 경우 응용 프로그램 유형의 기존 인스턴스를 제거합니다. 이 경우 응용 프로그램의 모든 데이터가 제거됩니다. 로컬로 디버깅하는 동안 응용 프로그램의 새 버전을 테스트할 때 이미 만든 데이터를 유지하거나, 응용 프로그램을 계속 실행하거나, 후속 디버그 세션이 응용 프로그램을 업그레이드하도록 할 수 있습니다. Visual Studio 서비스 패브릭 도구는 **응용 프로그램 디버그 모드**라는 속성을 제공합니다. 이 속성은 **F5** 키로 응용 프로그램을 제거할지, 디버그 세션이 끝난 후에도 응용 프로그램이 계속 실행되도록 할지, 후속 디버깅 세션에서 응용 프로그램을 제거한 후 재배포하지 않고 업그레이드할지를 제어합니다.

#### 응용 프로그램 디버그 모드 속성을 설정하려면

1. 응용 프로그램 프로젝트의 바로 가기 메뉴에서 **속성**을 선택하거나 **F4** 키를 누릅니다.
2. **속성** 창에서 **응용 프로그램 디버그 모드** 속성을 설정합니다.

    ![응용 프로그램 디버그 모드 속성 설정][debugmodeproperty]

사용 가능한 **응용 프로그램 디버그 모드** 옵션은 다음과 같습니다.

1. **자동 업그레이드**: 디버그 세션이 종료될 때 응용 프로그램이 계속 실행됩니다. 다음에 **F5** 키를 누르면 모니터링되지 않는 자동 모드를 사용하여 배포가 업그레이드로 처리되어, 응용 프로그램이 새로운 버전으로(추가된 날짜 문자열과 함께) 신속하게 업그레이드됩니다. 업그레이드 프로세스는 이전 디버그 세션에서 입력한 모든 데이터를 유지합니다.

2. **응용 프로그램 유지**: 디버그 세션이 종료될 때 클러스터에서 응용 프로그램이 계속 실행됩니다. 다음에 **F5** 키를 누르면 응용 프로그램이 제거되고 새로 빌드된 응용 프로그램이 클러스터에 배포됩니다.

3. **응용 프로그램 제거**를 선택하면 디버그 세션이 종료될 때 응용 프로그램이 제거됩니다.

**자동 업그레이드**의 경우 데이터는 서비스 패브릭의 응용 프로그램 업그레이드 기능을 적용하여 보존되지만 안전보다는 성능을 위해 최적화하도록 조정됩니다. 응용 프로그램 업그레이드 및 실제 환경에서 업그레이드를 수행하는 방법에 대한 자세한 내용은 [서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)를 참조하세요.

![데이터가 추가된 새 응용 프로그램 버전의 예][preservedata]

>[AZURE.NOTE] Visual Studio용 서비스 패브릭 도구 1.1 버전 이전에는 이 속성이 없습니다. 1.1 버전 이전에는 **시작 시 데이터 보존** 속성을 사용하여 동일한 동작을 얻을 수 있습니다. "응용 프로그램 유지" 옵션은 Visual Studio용 서비스 패브릭 도구 버전 1.2에 도입되었습니다.

## 서비스 패브릭 응용 프로그램에 서비스 추가

응용 프로그램에 새 서비스를 추가하여 기능을 확장할 수 있습니다. 응용 프로그램 패키지에 서비스가 포함되도록 하려면 **새 패브릭 서비스...** 메뉴 항목을 통해 서비스를 추가합니다.

![응용 프로그램에 새 패브릭 서비스 추가][newservice]

응용 프로그램에 추가할 서비스 패브릭 프로젝트 유형을 선택하고 서비스의 이름을 지정합니다. 사용할 서비스 유형을 결정하는 데 도움이 필요하면 [서비스를 위한 프레임워크 선택](service-fabric-choose-framework.md)을 참조하세요.

![응용 프로그램에 추가할 패브릭 서비스 프로젝트 유형 선택][addserviceproject]

새 서비스가 솔루션 및 기존 응용 프로그램 패키지에 추가됩니다. 서비스 참조 및 기본 서비스 인스턴스는 응용 프로그램 매니페스트에 추가됩니다. 다음 번에 응용 프로그램을 배포할 때 서비스가 만들어지고 시작됩니다.

![새 서비스가 응용 프로그램 매니페스트에 추가됩니다.][newserviceapplicationmanifest]

## 서비스 패브릭 응용 프로그램 패키징

응용 프로그램 및 해당 서비스를 클러스터에 배포하려면 응용 프로그램 패키지를 만들어야 합니다. 패키지는 응용 프로그램 매니페스트, 서비스 매니페스트 및 특정 레이아웃에서 필요한 기타 파일로 구성됩니다. Visual Studio는 응용 프로그램 프로젝트의 폴더인 'pkg' 디렉터리에서 패키지를 관리합니다. **응용 프로그램** 상황에 맞는 메뉴에서 **패키지**를 클릭하면 응용 프로그램 패키지가 생성되거나 업데이트됩니다. 사용자 지정 PowerShell 스크립트를 사용하여 응용 프로그램을 배포하는 경우 이 작업을 수행할 수 있습니다.

## 클라우드 탐색기를 사용하여 응용 프로그램 및 응용 프로그램 유형 제거

**보기** 메뉴에서 시작할 수 있는 클라우드 탐색기를 사용하여 Visual Studio 내에서 기본 클러스터 관리 작업을 수행할 수 있습니다. 예를 들어 응용 프로그램을 삭제하고 로컬 또는 원격 클러스터에서 응용 프로그램 유형을 프로비전 해제할 수 있습니다.

![응용 프로그램 제거](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] 다양한 클러스터 관리 기능은 [Service Fabric Explorer로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

- [서비스 패브릭 응용 프로그램 모델](service-fabric-application-model.md)
- [서비스 패브릭 응용 프로그램 배포](service-fabric-deploy-remove-applications.md)
- [여러 환경에 대한 응용 프로그램 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)
- [서비스 패브릭 응용 프로그램 디버깅](service-fabric-debugging-your-application.md)
- [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0921_2016-->