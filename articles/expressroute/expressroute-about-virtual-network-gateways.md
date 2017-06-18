<properties 
   pageTitle="ExpressRoute 가상 네트워크 게이트웨이 정보 | Microsoft Azure"
   description="ExpressRoute의 가상 네트워크 게이트웨이에 대해 알아봅니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# ExpressRoute에 대한 가상 네트워크 게이트웨이 정보


가상 네트워크 게이트웨이는 Azure 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용됩니다. ExpressRoute 연결을 구성할 때 가상 네트워크 게이트웨이 및 가상 네트워크 게이트웨이 연결을 만들고 구성해야 합니다.

가상 네트워크 게이트웨이를 만들 때 몇 가지 설정을 지정합니다. 필수 설정 중 하나에서는 ExpressRoute 또는 VPN Gateway 트래픽에 게이트웨이를 사용할지 여부를 지정합니다. Resource Manager 배포 모델에서 이 설정은 '-GatewayType'입니다.

네트워크 트래픽을 전용 개인 연결에 전송하면 'Express 경로' 게이트웨이 유형을 사용합니다. 이를 Express 경로 게이트웨이라고도 합니다. 네트워크 트래픽을 공용 연결을 통해 암호화하여 전송하면 'VPN' 게이트웨이 유형을 사용합니다. 이를 VPN Gateway라고도 합니다. 사이트 간, 지점 및 사이트 간, VNet 간 연결은 모두 VPN Gateway를 사용합니다.

각각의 가상 네트워크에는 게이트웨이 유형당 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 예를 들어 -GatewayType Vpn을 사용하는 하나의 가상 네트워크 게이트웨이와 -GatewayType Express 경로를 사용하는 하나의 가상 네트워크 게이트웨이가 있을 수 있습니다. 이 문서에서는 ExpressRoute 가상 네트워크 게이트웨이를 중점적으로 다룹니다.

## <a name="gwsku"></a>게이트웨이 SKU

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Azure Portal을 사용하여 Resource Manager 가상 네트워크 게이트웨이를 만들려면 기본적으로 표준 SKU를 사용하여 가상 네트워크 게이트웨이를 구성합니다. 현재, Azure Portal에서 Resource Manager 배포 모델에 다른 SKU를 지정할 수 없습니다. 그러나 게이트웨이를 만든 후에 'Resize-AzureRmVirtualNetworkGateway' PowerShell cmdlet을 사용하여 더 강력한 게이트웨이 SKU로 업그레이드할 수 있습니다(예: 기본/표준에서 HighPerformance로).

###  <a name="aggthroughput"></a>게이트웨이 SKU에 의해 예상된 총 처리량


다음 테이블에서는 게이트웨이 형식과 예상 총 처리량을 보여 줍니다. 이 표는 리소스 관리자 배포 모델과 클래식 배포 모델 모두에 적용됩니다.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]


## <a name="resources"></a>REST API 및 PowerShell cmdlet

가상 네트워크 게이트웨이 구성을 위해 REST API와 PowerShell cmdlet을 사용할 경우 추가 기술 리소스 및 특정 구문 요구 사항에 대해서는 다음 페이지를 참조하세요.

|**클래식** | **리소스 관리자**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## 다음 단계

사용 가능한 연결 구성에 대한 자세한 내용은 [ExpressRoute 개요](expressroute-introduction.md)를 참조하세요.







 

<!---HONumber=AcomDC_0921_2016-->