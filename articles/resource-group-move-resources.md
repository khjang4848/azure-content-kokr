<properties 
	pageTitle="새 리소스 그룹으로 리소스 이동 | Microsoft Azure" 
	description="Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="tomfitz"/>

# 새 리소스 그룹 또는 구독으로 리소스 이동

이 항목에서는 하나의 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동하는 방법을 보여 줍니다. 새 구독으로 리소스를 이동할 수도 있습니다. 단, 해당 구독이 동일한 [테넌트](./active-directory/active-directory-howto-tenant.md)에 있어야 합니다. 다음과 같은 결정을 내린 경우 리소스를 이동해야 할 수 있습니다.

1. 대금 청구를 위해서는 리소스가 다른 구독 상태여야 합니다.
2. 리소스가 이전에 그룹화되었던 리소스와 동일한 수명 주기를 더 이상 공유하지 않습니다. 해당 리소스를 다른 리소스와는 별도로 관리할 수 있도록 새 리소스 그룹으로 이동하려고 합니다.

리소스를 이동할 때 원본 그룹과 대상 그룹은 모두 작업 중에 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 그룹에서 차단됩니다.

리소스의 위치는 변경할 수 없습니다. 리소스를 이동할 때는 새 리소스 그룹으로만 이동됩니다. 새 리소스 그룹은 다른 위치를 가질 수 있지만 리소스의 위치는 변경되지 않습니다.

> [AZURE.NOTE] 이 문서에서는 기존 Azure 계정 제품 내에서 리소스를 이동하는 방법을 설명합니다. 기존 리소스를 계속 사용하면서 실제로 Azure 계정 제품을 변경하려는 경우(예: 종량제 요금에서 선불로 업그레이드) [Azure 구독을 다른 제품으로 전환](billing-how-to-switch-azure-offer.md)을 참조하세요.

## 리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 서비스는 리소스 이동 기능을 지원해야 합니다. [리소스 이동을 지원하는 서비스](#services-that-support-move)에 대한 자세한 내용은 아래 목록을 참조하세요.
2. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음**을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 문제가 발생할 수 있습니다. 등록 상태 및 등록 리소스 공급자를 확인하는 방법은 [리소스 공급자 및 형식](../resource-manager-supported-services.md#resource-providers-and-types)을 참조하세요.
3. Azure PowerShell 또는 Azure CLI를 사용하는 경우 최신 버전을 사용합니다. 사용 중인 버전을 업데이트하려면 Microsoft 웹 플랫폼 설치 관리자를 실행하고 새 버전을 사용할 수 있는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md) 및 [Azure CLI 설치](xplat-cli-install.md)를 참조하세요.
4. 앱 서비스 앱을 이동하는 경우 [앱 서비스 제한](#app-service-limitations)을 먼저 검토해야 합니다.
5. 클래식 모델을 통해 배포된 리소스를 이동하는 경우 [클래식 배포 제한 사항](#classic-deployment-limitations)을 먼저 검토해야 합니다.

## 이동을 지원하는 서비스

현재, 새 리소스 그룹 및 구독으로의 이동을 모두 지원하는 서비스는 다음과 같습니다.

- API 관리
- 앱 서비스 앱(웹앱) - [앱 서비스 제한](#app-service-limitations) 참조
- 자동화
- 배치
- CDN
- 클라우드 서비스 - [클래식 배포 제한 사항](#classic-deployment-limitations) 참조
- 데이터 팩터리
- DNS
- DocumentDB
- HDInsight 클러스터
- 키 자격 증명 모음
- 미디어 서비스
- 모바일 고객 관리
- 알림 허브
- Operational Insights
- Redis 캐시
- 스케줄러
- 검색
- 저장소
- 저장소(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations) 참조
- SQL 데이터베이스 서버 - 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 여기에 속한 모든 데이터베이스도 함께 이동합니다.
- 가상 컴퓨터
- 가상 컴퓨터(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations) 참조
- 가상 네트워크

## 이동을 지원하지 않는 서비스

현재 리소스 이동을 지원하지 않는 서비스는 다음과 같습니다.

- 응용 프로그램 게이트웨이
- Application Insights
- Express 경로
- Recovery Services 자격 증명 모음 - Recovery Services 자격 증명 모음과 연결된 Compute, Network 및 Storage 리소스도 이동하지 않습니다. [Recovery Services 제한 사항](#recovery-services-limitations)을 참조하세요.
- 가상 컴퓨터 크기 집합
- 가상 네트가상 네트워크(클래식) - [클래식 배포 제한 사항](#classic-deployment-limitations) 참조
- VPN 게이트웨이

## 앱 서비스 제한

앱 서비스 앱으로 작업할 경우에는 앱 서비스 계획만 이동할 수 없습니다. 앱 서비스 앱을 이동할 때는 다음 옵션을 사용할 수 있습니다.

- 앱 서비스 계획과 해당 리소스 그룹에 있는 모든 기타 앱 서비스 리소스를 이미 앱 서비스 리소스가 없는 새 리소스 그룹으로 이동합니다. 이 요구 사항은 App Service 계획에 연결되지 않은 App Service 리소스도 이동해야 함을 의미합니다.
- 앱을 다른 리소스 그룹으로 이동하지만 모든 앱 서비스는 원래 리소스 그룹에 유지합니다.

원래 리소스 그룹에 Application Insights 리소스도 포함하는 경우 Application Insights가 현재 이동 작업을 지원하지 않으므로 해당 리소스를 이동할 수 없습니다. App Service 앱을 이동할 때 Application Insights 리소스를 포함 하는 경우 전체 이동 작업이 실패 합니다. 하지만, Application Insights와 앱 서비스 계획은 제대로 기능하기 위해 해당 앱에 대해 앱과 같은 리소스 그룹에 상주하지 않아도 됩니다.

예를 들어 리소스 그룹에 다음 사항이 포함된 경우:

- **plan-a** 및 **app-insights-a**와 연결된 **web-a**
- **plan-b** 및 **app-insights-b**와 연결된 **web-b**

옵션은 다음과 같습니다.

- **web-a**, **plan-a**, **web-b** 및 **plan-b** 이동
- **web-a** 및 **web-b** 이동
- **web-a** 이동
- **web-b** 이동

다른 모든 조합에는 이동할 수 없는 리소스 형식(Application Insights)을 이동하거나 앱 서비스 계획 이동 시 그대로 남겨 둘 수 없는 리소스 형식(앱 서비스 리소스 형식)을 남겨두는 것이 있습니다.

웹앱이 해당 앱 서비스 계획과는 다른 리소스 그룹에 상주하지만 이 두 가지를 새로운 리소스 그룹으로 이동하려는 경우에는 두 단계로 이동을 수행해야 합니다. 예:

- **web-a**가 **web-group**에 상주하는 경우
- **plan-a**가 **plan-group**에 상주하는 경우
- **web-a** 및 **plan-a**를 **combined-group**에 상주시키려는 경우

이 이동을 수행하려면 다음 순서로 두 개의 별도 이동 작업을 수행하세요.

1. **web-a**를 **plan-group**으로 이동
2. **web-a** 및 **plan-a**를 **combined-group**으로 이동

## Recovery Services 제한 사항

Azure Site Recovery로 재해 복구를 설정하는 데 사용된 Storage, Network 또는 Compute 리소스에 대해서는 이동이 지원되지 않습니다.

예를 들어, 온-프레미스 컴퓨터에서 저장소 계정(Storage1)으로 복제를 설정했고 Azure에 장애 조치(failover) 후 가상 네트워크(Network1)에 연결된 가상 컴퓨터(VM1)로 보호되는 컴퓨터를 실행하려고 한다고 가정합니다. 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 이러한 Azure 리소스(Storage1, VM1, Network1) 작업 중 어떠한 것도 이동할 수 없습니다.

## 클래식 배포 제한 사항

구독 내 또는 새 구독으로 리소스를 이동할지 여부에 따라 클래식 모델을 통해 배포된 리소스의 이동 옵션은 다릅니다.

한 리소스 그룹에서 **같은 구독 내** 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

- 가상 네트워크(클래식)은 이동할 수 없습니다.
- 가상 컴퓨터(클래식)는 클라우드 서비스로 이동해야 합니다.
- 클라우드 서비스는 이동에 모든 가상 컴퓨터가 포함된 경우에만 이동할 수 있습니다.
- 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
- 한 번에 하나의 저장소 계정(클래식)만 이동할 수 있습니다.
- 저장소 계정(클래식)은 가상 컴퓨터 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

**새 구독**으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

- 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
- 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
- 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 **동일한 구독 내의** 새 리소스 그룹으로 이동하려면, [포털](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli) 또는 [REST API](#use-rest-api)를 사용합니다.

**새 구독으로 클래식 리소스**를 이동하려면 클래식 리소스에 한정된 REST 작업을 사용해야 합니다. 구독을 클래식 리소스의 교차 구독 이동에서 원본 또는 대상 구독으로 참여할 수 있는지 확인하려면 다음 작업을 사용하세요.

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
원본 구독의 경우 요청 본문을 사용합니다.

    {
        "role": "source"
    }

대상 구독의 경우 요청 본문을 사용합니다.

    {
        "role": "target"
    }

유효성 검사 작업에 대한 응답은 다음과 같습니다.

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

한 구독에서 다른 구독으로 모든 클래식 리소스를 이동하려면 다음 작업을 사용하세요.

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

요청 본문:

    {
        "target": "/subscriptions/{target-subscription-id}"
    }


## 포털 사용

리소스를 동일한 구독의 새 리소스 그룹으로 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 **이동** 단추를 선택합니다.

![리소스 이동](./media/resource-group-move-resources/edit-rg-icon.png)

리소스를 새 구독으로 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 구독 편집 아이콘을 선택합니다.

![리소스 이동](./media/resource-group-move-resources/change-subscription.png)

이동할 리소스와 대상 리소스 그룹을 선택합니다. 이러한 리소스에 대해 스크립트를 업데이트해야 함을 승인하고 **확인**을 선택합니다. 이전 단계에서 구독 편집 아이콘을 선택한 경우 대상 구독도 선택해야 합니다.

![대상 선택](./media/resource-group-move-resources/select-destination.png)

**알림**에서 이동 작업이 실행 중임을 볼 수 있습니다.

![이동 상태 표시](./media/resource-group-move-resources/show-status.png)

완료되면 결과를 알려 줍니다.

![이동 결과 표시](./media/resource-group-move-resources/show-result.png)

## PowerShell 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 **Move-AzureRmResource** 명령을 사용합니다.

첫 번째 예제는 새 리소스 그룹에 하나의 리소스를 이동하는 방법을 보여 줍니다.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

두 번째 예제는 새 리소스 그룹에 여러 리소스를 이동하는 방법을 보여 줍니다.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

새 구독으로 이동하려면 **DestinationSubscriptionId** 매개 변수 값을 포함합니다.

지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Azure CLI 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 **azure resource move** 명령을 사용합니다. 다음 예제에서는 Redis Cache를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. **-i** 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
지정한 리소스를 이동할 것인지 묻는 메시지가 나타납니다.
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## REST API 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 다음을 실행합니다.

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다. 이동 REST 작업에 대한 자세한 내용은 [리소스 이동](https://msdn.microsoft.com/library/azure/mt218710.aspx)을 참조하세요.




## 다음 단계
- 구독을 관리하기 위한 PowerShell cmdlet에 대한 자세한 내용은 [Resource Manager에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
- 구독을 관리하기 위한 Azure CLI 명령에 대한 자세한 내용은 [Resource Manager에서 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
- 구독을 관리하기 위한 포털 기능에 대한 자세한 내용은 [Azure 포털을 사용하여 리소스 관리](./azure-portal/resource-group-portal.md)를 참조하세요.
- 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->