<properties
   pageTitle="Azure 클러스터 및 해당 리소스 삭제 | Microsoft Azure"
   description="클러스터가 포함된 리소스 그룹을 삭제하거나 리소스를 선별적으로 삭제하여 서비스 패브릭 클러스터를 완전히 삭제하는 방법을 알아봅니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# Azure의 서비스 패브릭 클러스터와 사용하는 리소스 삭제

서비스 패브릭 클러스터는 클러스터 리소스 자체 외에도 다른 많은 Azure 리소스로 이루어져 있습니다. 따라서 서비스 패브릭 클러스터를 완벽하게 삭제하려면 구성되어 있는 모든 리소스를 삭제해야 합니다. 이 작업을 수행하는 데 두 가지 옵션이 있습니다. 클러스터가 속한 리소스 그룹을 삭제(리소스 그룹의 다른 리소스와 클러스터 리소스 삭제)하거나 클러스터 리소스와 관련 리소스(리소스 그룹의 다른 리소스 제외)를 특별히 삭제합니다.

>[AZURE.NOTE] 클러스터 리소스를 삭제해도 서비스 패브릭 클러스터를 구성하고 있는 기타 리소스를 모두 삭제하지는 **않습니다**.

## 서비스 패브릭 클러스터가 속한 리소스 그룹(RG) 전체를 삭제합니다.

리소스 그룹을 포함해 클러스터와 연결된 모든 리소스를 삭제하는 가장 쉬운 방법입니다. PowerShell을 사용하거나 Azure 포털을 통해 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹에 서비스 패브릭 클러스터와 관련되지 않은 리소스가 있다면 특정 리소스를 삭제할 수 있습니다.

### Azure PowerShell을 사용하여 리소스 그룹 삭제

또한 다음 Azure PowerShell cmdlets를 실행하여 리소스 그룹을 삭제할 수도 있습니다. 컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. 이전에 수행한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)에 요약된 단계에 따르세요.

PowerShell 창을 열고 다음 PS cmdlets를 실행합니다.

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

*-Force* 옵션을 사용하지 않은 경우 삭제를 확인하는 메시지가 나타납니다. 확인 시 RG 및 포함된 모든 리소스가 삭제됩니다.

### Azure 포털에서 리소스 그룹 삭제  

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 삭제하려는 서비스 패브릭 클러스터로 이동합니다.
3. 클러스터 필수 페이지에서 리소스 그룹 이름을 클릭합니다.
4. 이렇게 하면 **리소스 그룹 필수** 페이지가 표시됩니다.
5. **삭제**를 클릭합니다.
6. 해당 페이지의 지침에 따라 리소스 그룹 삭제를 완료합니다.

![리소스 그룹 삭제][ResourceGroupDelete]


## 리소스 그룹의 다른 리소스는 제외하고 클러스터 리소스와 사용하는 리소스를 삭제합니다.

리소스 그룹에 삭제하려는 서비스 패브릭 클러스터와 관련된 리소스만 있는 경우 리소스 그룹 전체를 삭제하기 훨씬 쉽습니다. 리소스 그룹의 리소스를 단계별로 선별하여 삭제하려면 다음 단계를 따르세요.

포털을 사용하거나 템플릿 갤러리에서 서비스 패브릭 Resource Manager 템플릿 중 하나를 사용하여 클러스터를 배포한 경우 클러스터에서 사용하는 모든 리소스에는 다음 두 개의 태그가 지정됩니다. 삭제하려는 리소스를 결정하기 위해 사용할 수 있습니다.

***Tag#1:*** 키 = clusterName, 값 = '클러스터 이름'

***Tag#2:*** 키 = resourceName, 값 = ServiceFabric

### Azure 포털에서 특정 리소스 삭제

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 삭제하려는 서비스 패브릭 클러스터로 이동합니다.
3. 필수 블레이드에서 **모든 설정**으로 이동합니다.
4. 설정 블레이드의 **리소스 관리**에서 **태그**를 클릭합니다.
5. 태그 블레이드에서 **태그** 중 하나를 클릭하여 해당 태그와 관련된 모든 리소스 목록을 얻습니다.

    ![리소스 태그][ResourceTags]

6. 태그가 지정된 리소스 목록이 있으면 각 리소스를 클릭하고 삭제합니다.

    ![태그가 지정된 리소스][TaggedResources]

### Azure PowerShell을 사용하여 리소스 삭제

다음 Azure PowerShell cmdlets를 실행하여 리소스를 단계별로 삭제할 수 있습니다. 컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. 이전에 수행한 적이 없는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)에 요약된 단계에 따르세요.

PowerShell 창을 열고 다음 PS cmdlets를 실행합니다.

```powershell
Login-AzureRmAccount
```
삭제하려는 각 리소스에 대해 다음을 실행합니다.

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

클러스터 리소스를 삭제하려면 다음을 실행합니다.

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## 다음 단계
또한 클러스터 업그레이드 및 분할 서비스에 대해 자세히 알아 보려면 다음을 읽습니다.

- [클러스터 업그레이드 알아보기](service-fabric-cluster-upgrade.md)
- [최대 크기를 위해 상태 저장 서비스를 분할하는 방법 알아보기](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

<!---HONumber=AcomDC_0921_2016-->