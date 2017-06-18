<properties
   pageTitle="서비스 패브릭 노드 형식 및 VM 크기 조정 설정 | Microsoft Azure"
   description="VM 크기 조정 설정과 서비스 패브릭 노드 유형의 관계 및 VM 크기 조정 설정 인스턴스 또는 클러스터 노드에 원격으로 연결하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# 서비스 패브릭 노드 형식과 가상 컴퓨터 확장 집합 간의 관계

가상 컴퓨터 규모 집합은 가상 컴퓨터의 컬렉션을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. 서비스 패브릭 클러스터에 정의된 모든 노드 유형은 별도의 VM 크기 집합으로 설치됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다.

다음 스크린 샷에서는 프런트 엔드 및 백 엔드 등 두 가지 노드 유형이 있는 클러스터를 보여줍니다. 각 노드 형식에는 5개의 노드가 있습니다.

![두 가지 노드 유형이 있는 클러스터의 스크린 샷][NodeTypes]

## 노드에 VM 크기 조정 설정 인스턴스 매핑

위에서 볼 수 있듯이 VM 크기 조정 설정 인스턴스는 0에서 시작하여 커집니다. 이름에 번호 매기기를 반영합니다. 예를 들어 BackEnd_0은 백 엔드 VM 크기 집합의 인스턴스 0입니다. 이 특정 VM 크기 집합에는 BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 및 BackEnd_4라는 5개의 인스턴스가 있습니다.

VM 크기 조정 설정을 확대하는 경우 새 인스턴스가 생성됩니다. 새 VM 크기 조정 설정 인스턴스 이름은 일반적으로 VM 크기 조정 설정 이름 + 다음 인스턴스 번호입니다. 이 예제에서는 BackEnd\_5입니다.


## 각 노드 형식/VM 크기 조정 집합에 VM 크기 조정 설정 부하 분산 장치 매핑

포털에서 클러스터를 배포했거나 제공한 샘플 Resource Manager 템플릿을 사용한 경우 리소스 그룹에서 모든 리소스의 목록을 가져올 때 각 VM 크기 집합 또는 노드 유형에 대한 부하 분산 장치가 표시됩니다.

이름은 **LB-&lt;NodeType name&gt;**와 같습니다. 예를 들어 이 스크린 샷에 표시된 대로 LB-sfcluster4doc-0입니다.


![리소스][Resources]


## VM 크기 조정 설정 인스턴스 또는 클러스터 노드에 원격 연결
클러스터에 정의된 모든 노드 유형은 별도의 VM 크기 집합으로 설치됩니다. 노드 유형이 독립적으로 확장 또는 축소될 수 있고 VM SKU로 구성될 수 있습니다. 단일 VM 인스턴스와 달리 VM 크기 조정 설정 인스턴스는 고유한 가상 IP 주소를 가져오지 못합니다. 따라서 특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 찾기란 조금 어려울 수 있습니다.

찾기 위해 수행할 수 있는 단계는 다음과 같습니다.

### 1단계: 노드 형식에 대한 가상 IP 주소 및 RDP에 대한 인바운드 NAT 규칙 찾기

이를 가져오려면 **Microsoft.Network/loadBalancers**에 대한 리소스 정의의 일부로 정의된 인바운드 NAT 규칙 값을 가져와야 합니다.

포털에서 부하 분산 장치 블레이드 및 **설정**으로 이동합니다.

![LBBlade][LBBlade]


**설정**에서 **인바운드 NAT 규칙**을 클릭합니다. 이제 첫 번째 VM 크기 조정 설정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 제공합니다. 아래 스크린샷에서 **104.42.106.156** 및 **3389**입니다.

![NATRules][NATRules]

### 2단계: 특정 VM 크기 조정 설정 인스턴스/노드에 원격 연결하는 데 사용할 수 있는 포트 찾기

이 문서의 앞부분에서 VM 크기 조정 설정 인스턴스를 노드에 매핑하는 방법에 대해 이야기했습니다. 정확한 포트를 찾기 위해 그 방법을 사용합니다.

포트는 VM 크기 집합 인스턴스의 오름차순으로 할당됩니다. 따라서 프런트 엔드 노드 유형에 대한 예제에서 5개의 인스턴스 각각에 대한 포트는 다음과 같습니다. 이제 VM 크기 집합 인스턴스에 동일한 매핑을 수행해야 합니다.

|**VM 크기 집합 인스턴스**|**포트**|
|-----------------------|--------------------------|
|FrontEnd\_0|3389|
|FrontEnd\_1|3390|
|FrontEnd\_2|3391|
|FrontEnd\_3|3392|
|FrontEnd\_4|3393|
|FrontEnd\_5|3394|


### 3단계: 특정 VM 크기 조정 설정 인스턴스에 원격 연결

아래 스크린샷에서 원격 데스크톱 연결을 사용하여 FrontEnd\_1에 연결합니다.

![RDP][RDP]

## RDP 포트 범위 값을 변경하는 방법

### 클러스터 배포 전에

Resource Manager 템플릿을 사용하여 클러스터를 설정하는 경우 **inboundNatPools**에서 범위를 지정할 수 있습니다.

**Microsoft.Network/loadBalancers**에 대한 리소스 정의로 이동합니다. 여기서 **inboundNatPools**에 대한 설명을 찾을 수 있습니다. *frontendPortRangeStart* 및 *frontendPortRangeEnd* 값을 바꿉니다.

![InboundNatPools][InboundNatPools]


### 클러스터 배포 후에
조금 더 복잡하고 VM이 재활용될 수 있습니다. 이제 Azure PowerShell을 사용하여 새 값을 설정해야 합니다. 컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. 이전에 수행한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)에 요약된 단계에 따라 수행하는 것이 좋습니다.

Azure 계정에 로그인합니다. Powershell이 어떤 이유로 인해 실패하면 Azure PowerShell이 올바르게 설치되었는지 확인해야 합니다.

```
Login-AzureRmAccount
```

부하 분산 장치에 대한 자세한 내용을 보려면 다음을 실행하고 **inboundNatPools**에 대한 설명에서 값이 확인합니다.

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

*frontendPortRangeEnd* 및 *frontendPortRangeStart*를 원하는 값으로 설정합니다.

```
$PropertiesObject = @{
	#Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## 다음 단계

- ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)
- [클러스터 보안](service-fabric-cluster-security.md)
- [서비스 패브릭 SDK 및 시작](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

<!---HONumber=AcomDC_0921_2016-->