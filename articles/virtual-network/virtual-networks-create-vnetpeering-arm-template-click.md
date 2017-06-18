<properties
   pageTitle="Resource Manager 템플릿을 사용하여 VNet 피어링 만들기 | Microsoft Azure"
   description="Resource Manager에서 템플릿을 사용하여 가상 네트워크 피어링을 만드는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# Resource Manager 템플릿을 사용하여 VNet 피어링 만들기

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Resource Manager 템플릿을 사용하여 VNet 피어링을 만들려면 다음 단계를 수행하세요.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.

    > [AZURE.NOTE] VNet 피어링을 관리하기 위한 PowerShell cmdlet은 [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)에 포함되어 있습니다.

2. 아래 텍스트에서는 위의 시나리오를 기반으로 VNet1-VNet2에 대한 VNet 피어링 연결의 정의를 보여 줍니다. 아래의 콘텐츠를 복사하고 VNetPeeringVNet1.json 파일에 저장합니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. 아래 섹션에서는 위의 시나리오를 기반으로 VNet2-VNet1에 대한 VNet 피어링 연결의 정의를 보여 줍니다. 아래의 콘텐츠를 복사하고 VNetPeeringVNet2.json 파일에 저장합니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    위의 템플릿에서 본 대로 VNet 피어링에 대한 몇 가지 구성 가능한 속성이 있습니다.

    |옵션|설명|기본값|
    |:-----|:----------|:------|
    |AllowVirtualNetworkAccess|피어링 VNet의 주소 공간이 virtual\_network 태그의 일부로 포함되는지 여부입니다.|예|
    |AllowForwardedTraffic|피어링된 VNet에서 시작된 트래픽이 허용되거나 삭제되는지 여부입니다.|아니요|
    |AllowGatewayTransit|피어링 VNet이 VNet 게이트웨이를 사용할 수 있습니다.|아니요|
    |UseRemoteGateways|피어링한 VNet 게이트웨이를 사용합니다. 피어링 VNet에는 게이트웨이가 구성되어 있어야 하고 AllowGatewayTransit을 선택해야 합니다. 구성된 게이트웨이가 있는 경우 이 옵션을 사용할 수 없습니다.|아니요|

    VNet 피어링의 링크 각각에는 위의 속성 집합이 있습니다. 예를 들어, AllowVirtualNetworkAccess를 VNet 피어링 링크 VNet2-VNet1에 대해 True로 설정하고 다른 방향의 VNet 피어링 링크에 대해 False로 설정할 수 있습니다.


4. 템플릿 파일을 배포하려면 New-AzureRmResourceGroupDeployment cmdlet을 실행하여 배포를 만들고 업데이트할 수 있습니다. Resource Manager 템플릿을 사용하는 방법에 대한 자세한 내용은 이 [문서](../resource-group-template-deploy.md)를 참조하세요.

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] 적절하게 리소스 그룹 이름 및 템플릿 파일을 교체하세요.

    다음은 위의 시나리오를 기반으로 한 예제입니다.

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    출력은 다음과 같습니다.

        DeploymentName		: VNetPeeringVNet1
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:05:03 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    출력은 다음과 같습니다.

        DeploymentName		: VNetPeeringVNet2
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:07:22 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. 배포가 완료된 후에 아래 cmdlet을 실행하여 피어링 상태를 볼 수 있습니다.

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    출력은 다음과 같습니다.

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: VNet101
        VirtualNetworkName	: VNet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

	이 시나리오에서 피어링을 설정한 후에 두 VNet의 어떤 가상 컴퓨터에서 가상 컴퓨터로 연결을 시작할 수 있어야 합니다. 기본적으로 AllowVirtualNetworkAccess는 True이며 VNet 피어링은 적절한 ACL을 프로비전하여 VNet 간의 통신을 허용합니다. 특정 서브넷 또는 가상 컴퓨터 간의 연결을 차단하도록 NSG(네트워크 보안 그룹) 규칙을 적용하여 두 가상 네트워크 간의 액세스를 세부적으로 제어할 수 있습니다. NSG 규칙을 만드는 자세한 내용은 이 [문서](virtual-networks-create-nsg-arm-ps.md)를 참조하세요.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

구독에 VNet 피어링을 만들려면 다음 단계를 따르세요.

1. 구독-A에 대한 권한이 있는 사용자-A 계정으로 Azure에 로그인하고 다음 cmdlet을 실행합니다.

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

	이는 요구 사항이 아니며 요청이 일치하면 사용자가 해당하는 VNet에 대한 피어링 요청을 개별적으로 요구하는 경우에도 피어링을 설정할 수 있습니다. 다른 VNet의 권한있는 사용자를 로컬 VNet의 사용자로 추가하면 설치가 수월해집니다.

2. 구독-B에 대한 권한이 있는 사용자-B 계정으로 Azure에 로그인하고 다음 cmdlet을 실행합니다.

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. 사용자-A의 로그인 세션에서 다음 cmdlet을 실행합니다.

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    JSON 파일을 정의하는 방법은 다음과 같습니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. 사용자-B의 로그인 세션에서 다음 cmdlet을 실행합니다.

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

	JSON 파일을 정의하는 방법은 다음과 같습니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

 	이 시나리오에서 피어링을 설정한 후에 다른 구독에 걸쳐 두 VNet의 어떤 가상 컴퓨터에서 가상 컴퓨터로 연결을 시작할 수 있어야 합니다.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 이 시나리오에서는 다음 샘플 템플릿을 배포하여 VNet 피어링을 설정할 수 있습니다. AllowForwardedTraffic 속성을 True로 설정해야 피어링된 VNet의 네트워크 가상 어플라이언스가 트래픽을 주고 받을 수 있습니다.

	HubVNet에서 VNet1에 VNet 피어링을 만들기 위한 템플릿은 다음과 같습니다. AllowForwardedTraffic을 false로 설정합니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. VNet1에서 HubVnet에 VNet 피어링을 만들기 위한 템플릿은 다음과 같습니다. AllowForwardedTraffic을 true로 설정합니다.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. 피어링을 설정하면 UDR(사용자 정의 경로)를 정의하는 이 [문서](virtual-network-create-udr-arm-ps.md)를 참조하여 그 기능을 사용하는 가상 어플라이언스를 통해 VNet1 트래픽을 리디렉션할 수 있습니다. 경로에 다음 홉 주소를 지정하면 피어링된 VNet HubVNet에서 가상 어플라이언스의 IP 주소를 설정할 수 있습니다.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

다양한 배포 모델의 가상 네트워크 간에 피어링을 만들려면 다음 단계를 수행합니다.
1. 아래 텍스트는 이 시나리오에서 VNET1-VNet2에 대한 VNet 피어링 연결의 정의를 보여 줍니다. Azure Resource Manager 가상 네트워크에 클래식 가상 네트워크를 피어링하는 데 하나의 링크만이 필요합니다.

    클래식 가상 네트워크 또는 VNET2이 있는 구독 ID에 위치하도록 하고 MyResouceGroup을 적절한 리소스 그룹 이름으로 변경해야 합니다.

    { "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parameters": { }, "variables": { }, "resources": [ { "apiVersion": "2016-06-01", "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "name": "VNET1/LinkToVNET2", "location": "[resourceGroup().location]", "properties": { "allowVirtualNetworkAccess": true, "allowForwardedTraffic": false, "allowGatewayTransit": false, "useRemoteGateways": false, "remoteVirtualNetwork": { "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]" } } } ] }

2. 템플릿 파일을 배포하려면 배포를 생성하거나 업데이트기 위해 다음 cmdlet을 실행합니다.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. 배포가 성공한 후에 다음 cmdlet을 실행하여 피어링 상태를 볼 수 있습니다.

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

클래식 VNet과 리소스 관리자 VNet 간에 피어링이 설정된 후에 VNET1의 가상 컴퓨터에서 VNET2의 가상 컴퓨터로 연결을 시작할 수 있어야 하고 반대로도 가능해야 합니다.

<!---HONumber=AcomDC_0921_2016-->