<properties
   pageTitle="Java로 Linux에 대한 첫 번째 Service Fabric 응용 프로그램 만들기 | Microsoft Azure"
   description="Java를 사용하여 Service Fabric 응용 프로그램 만들기 및 배포"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>


# 첫 번째 Azure Service Fabric 응용 프로그램 만들기

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric은 .NET Core 및 Java 모두에서 Linux에 대한 서비스를 구축하는 SDK를 제공합니다. 이 자습서에서는 Java를 사용하여 Linux용 응용 프로그램을 만들고 서비스를 구축하는 방법을 살펴보겠습니다.

## 필수 조건

시작하기 전에 [Linux 개발 환경을 설정](service-fabric-get-started-linux.md)하도록 합니다. Mac OS X을 사용하는 경우 [Vagrant를 사용하여 가상 컴퓨터에서 Linux one-box 환경을 설정](service-fabric-get-started-mac.md)할 수 있습니다.

## 응용 프로그램 만들기

서비스 패브릭 응용 프로그램은 응용 프로그램의 기능을 제공하는 특정 역할이 있는 하나 이상의 서비스를 포함할 수 있습니다. Linux용 Service Fabric SDK는 쉽게 첫 번째 서비스를 만들고 나중에 더 추가할 수 있는 [Yeoman](http://yeoman.io/) 생성기를 포함합니다. 단일 서비스를 사용하여 새 응용 프로그램을 만들기 위해 Yeoman을 사용해 보겠습니다.

1. 터미널에서 **yo azuresfjava**를 입력합니다.

2. 응용 프로그램의 이름을 지정합니다.

3. 첫 번째 서비스의 형식을 선택하고 이름을 지정합니다. 이 자습서의 목적에 따라 Reliable Actor 서비스를 선택합니다.

  ![Java용 Service Fabric Yeoman 생성기][sf-yeoman]

>[AZURE.NOTE] 옵션에 대한 자세한 내용은 [서비스 패브릭 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조하세요.

## 응용 프로그램 빌드

Service Fabric Yeoman 템플릿은 [Gradle](https://gradle.org/)에 대한 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 앱을 빌드할 수 있습니다.

  ```bash
  gradle
  ```

## 응용 프로그램 배포

응용 프로그램이 빌드되면 Azure CLI를 사용하여 로컬 클러스터에 배포할 수 있습니다.

1. 로컬 Service Fabric 클러스터에 연결합니다.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. 템플릿에 제공된 설치 스크립트를 사용하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

    ```bash
    cd myapp
    ./install.sh
    ```

3. 브라우저를 열고 http://localhost:19080/Explorer에서 Service Fabric Explorer로 이동합니다(Mac OS X에서 Vagrant를 사용하는 경우 localhost를 VM의 개인 IP로 바꿉니다).

4. 응용 프로그램 노드를 확장하면 응용 프로그램 유형에 대한 항목 및 해당 유형의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

## 테스트 클라이언트 시작 및 장애 조치 수행

행위자 프로젝트 자체에서는 아무 것도 수행하지 않습니다. 메시지를 보낼 다른 서비스 또는 클라이언트가 필요합니다. 행위자 템플릿은 행위자 서비스와 상호 작용하는 데 사용할 수 있는 간단한 테스트 스크립트를 포함합니다.

1. 행위자 서비스의 출력을 확인하려면 조사식 유틸리티를 사용하여 스크립트를 실행합니다.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer에서 행위자 서비스에 대한 기본 복제본을 호스팅하는 노드를 찾습니다. 아래 스크린샷에 있는 노드 3입니다.

    ![Service Fabric Explorer에서 기본 복제본 찾기][sfx-primary]

3. 이전 단계에서 찾은 노드를 클릭한 다음 작업 메뉴에서 **비활성화(다시 시작)**를 선택합니다. 로컬 클러스터에서 다섯 개의 노드 중 하나를 다시 시작하고 다른 노드에서 실행 중인 보조 복제본 중 하나에 장애 조치를 강제합니다. 이렇게 하면 테스트 클라이언트에서 출력에 주의하고 장애 조치에도 불구하고 카운터가 계속증가하게 됩니다.

## Eclipse Neon 플러그 인으로 응용 프로그램 빌드 및 배포

Eclipse Neon에 대한 서비스 플러그 인을 설치한 경우 Java로 Service Fabric 응용 프로그램을 만들고 빌드하고 배포하는 데 사용할 수 있습니다.

### 응용 프로그램 만들기

Service Fabric 플러그 인은 Eclipse 확장성을 통해 사용할 수 있습니다.

1. Eclipse에서 **파일 > 기타 > Service Fabric**을 선택합니다. [행위자] 및 [컨테이너]를 비롯한 옵션 집합이 표시됩니다.

    ![Eclipse의 Service Fabric 템플릿][sf-eclipse-templates]

2. 이 경우에 [상태 비저장 서비스]를 선택합니다.

3. Service Fabric 관점을 사용할지를 확인하는 메시지가 표시되며 Service Fabric 프로젝트와 함께 사용할 Eclipse를 최적화합니다. '예'를 선택합니다.

### 응용 프로그램 배포

Service Fabric 템플릿은 Eclipse를 통해 트리거할 수 있는 응용 프로그램을 빌드하고 배포하기 위한 Gradle 태스크 집합을 포함합니다.

1. **실행 > 구성 실행**을 선택합니다.

2. **Gradle Project**를 확장하고 **ServiceFabricDeployer**를 선택합니다.

3. **실행**을 클릭합니다.

몇 분 내로 앱을 빌드하고 배포합니다. Service Fabric Explorer에서 해당 상태를 모니터링할 수 있습니다.

## 다음 단계

- [Reliable Actors에 대해 자세히 알아보기](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->