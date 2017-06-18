<properties
   pageTitle="독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거 | Microsoft Azure"
   description="Windows Server를 실행하는 실제 또는 가상 컴퓨터에서 Azure 서비스 패브릭 클러스터에 노드를 추가하거나 제거하는 방법(온-프레미스 또는 클라우드에 위치 가능)을 알아봅니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# Windows Server가 실행되는 독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거 | Microsoft Azure

[Windows Server 컴퓨터에 독립 실행형 서비스 패브릭 클러스터를 만든](service-fabric-cluster-creation-for-windows-server.md) 후 비즈니스 요구가 변경될 수 있으므로 클러스터에 여러 노드를 추가 또는 제거해야 할 수 있습니다. 이 문서에서는 이 목표를 달성하는 자세한 단계를 제공합니다.


## 클러스터에 노드 추가

1. [클러스터 배포를 위한 필수 조건을 충족하는 컴퓨터 준비](service-fabric-cluster-creation-for-windows-server.md#preparemachines) 섹션에 나와 있는 단계에 따라 클러스터에 추가할 VM/컴퓨터를 준비합니다.
2. 이 VM/컴퓨터를 추가하려는 장애 도메인 및 업그레이드 도메인을 계획합니다.
3. 클러스터에서 추가하려는 VM/컴퓨터로 RDP(원격 데스크톱)를 수행합니다.
4. 이 VM/컴퓨터에 [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 패키지 압축을 풉니다.
5. Powershell을 관리자로 실행하고 압축을 푼 패키지의 위치로 이동합니다.
6. 추가할 새 노드를 설명하는 매개 변수를 사용하여 *AddNode.ps1* Powershell을 실행합니다. 아래 예제에서는 NodeType0 유형, IP 주소 182.17.34.52를 사용하여 VM5라는 새 노드를 UD1 및 FD1에 추가합니다. *ExistingClusterConnectionEndPoint*는 기존 클러스터에 이미 있는 노드에 대한 연결 끝점입니다. 이 끝점의 경우 클러스터에서 *모든* 노드의 IP 주소를 선택할 수 있습니다.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA true
```

## 클러스터에서 노드 제거

1. 클러스터에서 제거하려는 VM/컴퓨터로 RDP(원격 데스크톱).
2. [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690)하고 이 VM/컴퓨터에 패키지 압축을 풉니다.
3. Powershell을 관리자로 실행하고 압축을 푼 패키지의 위치로 이동합니다.
4. *RemoveNode.ps1* Powershell을 실행합니다. 아래 예제에서는 클러스터에서 현재 노드를 제거합니다. *ExistingClusterConnectionEndPoint*는 기존 클러스터에 이미 있는 노드에 대한 연결 끝점입니다. 이 끝점의 경우 클러스터에서 *모든* 노드의 IP 주소를 선택할 수 있습니다.

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```


## 다음 단계
- [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
- [Windows 보안을 사용하여 독립 실행형 클러스터 보호](service-fabric-windows-cluster-windows-security.md)
- [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)
- [Windows를 실행하는 Azure VM에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)

<!---HONumber=AcomDC_0921_2016-->