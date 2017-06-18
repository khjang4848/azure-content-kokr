<properties
   pageTitle="Linux에서 개발 환경 설정 | Microsoft Azure"
   description="Linux에서 런타임 및 SDK를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 응용 프로그램을 빌드할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# Linux에서 개발 환경 준비


> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Linux 개발 컴퓨터에서 [Azure Service Fabric 응용 프로그램](service-fabric-application-model.md)을 배포하고 실행하려면 런타임 및 일반적인 SDK를 설치해야 합니다. 또한 Java 및 .NET Core용 선택적 SDK를 설치할 수 있습니다.

## 필수 조건
### 지원되는 운영 체제 버전
개발을 위해 다음 운영 체제 버전이 지원됩니다.

- Ubuntu 16.04(Xenial Xerus)

## APT 원본 업데이트

apt-get을 통해 SDK 및 관련된 런타임 패키지를 설치하려면 먼저 APT 원본을 업데이트해야 합니다.

1. 터미널을 엽니다.
2. Service Fabric 리포지토리를 원본 목록에 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. APT 키링에 새 GPG 키를 추가합니다.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. 새로 추가된 리포지토리에 따라 패키지 목록을 새로 고칩니다.

    ```bash
    sudo apt-get update
    ```

## SDK 설치 및 설정 방법

원본을 업데이트하면 SDK를 설치할 수 있습니다.

1. Service Fabric SDK 패키지를 설치합니다. 설치를 확인하고 라이선스 규약에 동의하라는 메시지가 표시됩니다.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. SDK 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## Azure 플랫폼 간 CLI 설정

[Azure 플랫폼 간 CLI][azure-xplat-cli-github]는 클러스터 및 응용 프로그램을 비롯하여 Service Fabric 엔터티와 상호 작용하기 위한 명령을 포함합니다. 아래의 지침을 계속 진행하기 전에 Node.js에 기반하여 [노드를 설치했는지 확인][install-node]합니다.

1. 개발 컴퓨터에 GitHub 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. NPM(Node Package Manager)을 사용하여 복제된 리포지토리로 전환하고 CLI의 종속성을 설치합니다.

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. 복제된 bin/azure 폴더에서 /usr/bin/azure 폴더에 symlink를 만들어서 경로에 추가하고 모든 디렉터리에서 사용할 수 있도록 합니다.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. 마지막으로 자동 완성 Service Fabric 명령을 사용하도록 설정합니다.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## 로컬 클러스터를 설정합니다.

모든 항목을 성공적으로 설치하면 로컬 클러스터를 시작할 수 있습니다.

1. 클러스터 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. 웹 브라우저를 열고 http://localhost:19080/Explorer(으)로 이동합니다. 클러스터를 시작하는 경우 Service Fabric Explorer 대시보드가 표시됩니다.

    ![Linux의 Service Fabric Explorer][sfx-linux]

이 시점에서 게스트 컨테이너 또는 게스트 실행 파일에 따라 미리 빌드된 Service Fabric 응용 프로그램 패키지 또는 새 응용 프로그램 패키지를 배포할 수 있습니다. Java 또는 .NET Core SDK를 사용하여 새 서비스를 빌드하려면 다음 선택 설정 단계를 수행합니다.

## Java SDK 및 Eclipse Neon 플러그 인 설치(선택 사항)

Java SDK에서는 Java를 사용하여 Service Fabric 서비스를 빌드하는 데 필요한 라이브러리 및 템플릿을 제공합니다.

1. Java SDK 패키지를 설치합니다.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. SDK 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Neon Eclipse IDE 내에서 Service Fabric용 Eclipse 플러그 인을 설치할 수 있습니다.

1. Eclipse에서 Buildship 버전 1.0.17 이상을 설치하도록 합니다. **도움말 > 설치 세부 정보**를 선택하여 설치된 구성 요소의 버전을 확인할 수 있습니다. [여기][buildship-update]에 있는 지침을 사용하여 Buildship을 업데이트할 수 있습니다.

2. Service Fabric 플러그 인을 설치하려면 **도움말 > 새 소프트웨어 설치...**를 선택합니다.

3. "사용" 텍스트 상자에 http://dl.windowsazure.com/eclipse/servicefabric을 입력합니다.

4. 추가를 클릭합니다.

    ![Eclipse 플러그 인][sf-eclipse-plugin]

5. Service Fabric 플러그 인을 선택하고 [다음]을 클릭합니다.

6. 설치를 계속하고 최종 사용자 라이선스 규약에 동의합니다.

## .NET Core SDK 설치(선택 사항)

.NET Core SDK에서는 플랫폼 간 .NET Core를 사용하여 Service Fabric 서비스를 빌드하는 데 필요한 라이브러리 및 템플릿을 제공합니다.

1. .NET Core SDK 패키지를 설치합니다.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. SDK 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## 다음 단계

- [Linux에서 첫 번째 Java 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-java.md)

- [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

<!---HONumber=AcomDC_0928_2016-->