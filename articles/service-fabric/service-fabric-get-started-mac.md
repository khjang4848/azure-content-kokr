<properties
   pageTitle="Mac OS X에서 개발 환경 설정 | Microsoft Azure"
   description="런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 완료하면 Mac OS X에서 응용 프로그램을 빌드할 수 있습니다."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# Mac OS X에서 개발 환경 설정

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Mac OS X를 사용하여 Service Fabric 응용 프로그램을 Linux 클러스터에서 실행하도록 빌드할 수 있습니다. 이 문서에서는 개발을 위해 Mac을 설정하는 방법을 설명합니다.

## 필수 조건

Service Fabric은 OS X에서 고유하게 실행되지 않습니다. 로컬 Service Fabric 클러스터를 실행하기 위해 Vagrant 및 VirtualBox를 사용하여 미리 구성된 Ubuntu 가상 컴퓨터를 제공합니다. 시작하기 전에 다음 항목이 필요합니다.

- [Vagrant(v1.8.4 이상)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## 로컬 VM 만들기

5개 노드의 Service Fabric 클러스터를 포함하는 로컬 VM을 만들려면 다음을 수행합니다.

1. Vagrantfile 리포지토리 복제

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. 리포지토리의 로컬 클론으로 이동

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (선택 사항)기본 VM 설정 수정

    기본적으로 로컬 VM은 다음과 같이 구성됩니다.

    - 3GB의 메모리 할당
    - Mac 호스트에서 트래픽을 통과시키는 데 IP 192.168.50.50에서 구성된 개인 호스트 네트워크 사용

    이러한 설정 중 하나를 변경하거나 Vagrantfile의 VM에 다른 구성을 추가할 수 있습니다. 구성 옵션의 전체 목록은 [Vagrant 설명서](http://www.vagrantup.com/docs)를 참조하세요.

4. VM 만들기

    ```bash
    vagrant up
    ```

    이 단계에서는 미리 구성된 VM 이미지를 다운로드하고 로컬로 부팅한 다음 로컬 Service Fabric 클러스터를 설정합니다. 배포를 완료하려면 몇 분 정도가 걸립니다. 설치가 성공적으로 완료되면 출력에 클러스터가 시작되었음을 나타내는 메시지가 표시됩니다.

    ![다음 VM 프로비전을 시작하는 클러스터 설치][cluster-setup-script]

5. http://192.168.50.50:19080/Explorer에서 Service Fabric Explorer로 이동하여 클러스터가 올바르게 설정되었는지 테스트합니다(기본 개인 네트워크 IP를 유지한다고 가정함).

    ![호스트 Mac에서 본 Service Fabric Explorer][sfx-mac]


## Eclipse Neon용 Service Fabric 플러그 인 설치(선택 사항)

Service Fabric은 Java 서비스를 빌드하고 배포하는 프로세스를 간소화할 수 있는 Eclipse Neon IDE에 대한 플러그 인을 제공합니다.

1. Eclipse에서 Buildship 버전 1.0.17 이상을 설치하도록 합니다. **도움말 > 설치 세부 정보**를 선택하여 설치된 구성 요소의 버전을 확인할 수 있습니다. [여기][buildship-update]에 있는 지침을 사용하여 Buildship을 업데이트할 수 있습니다.

2. Service Fabric 플러그 인을 설치하려면 **도움말 > 새 소프트웨어 설치...**를 선택합니다.

3. "사용" 텍스트 상자에 http://dl.windowsazure.com/eclipse/servicefabric을 입력합니다.

4. 추가를 클릭합니다.

    ![Service Fabric용 Eclipse Neon 플러그 인][sf-eclipse-plugin-install]

5. Service Fabric 플러그 인을 선택하고 [다음]을 클릭합니다.

6. 설치를 계속하고 최종 사용자 라이선스 규약에 동의합니다.

## 다음 단계

- [첫 번째 Linux용 Service Fabric 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Azure Portal에서 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
- [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)
- [Service Fabric 응용 프로그램 모델 이해](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->