<properties
    pageTitle="Resource Manager 템플릿을 사용하여 서비스 버스 네임스페이스 만들기 | Microsoft Azure "
    description="Azure Resource Manager 템플릿을 사용하여 서비스 버스 네임스페이스 만들기"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Azure Resource Manager 템플릿을 사용하여 서비스 버스 네임스페이스 만들기

이 문서에서는 표준/기본 SKU가 있는 **메시징** 형식의 서비스 버스 네임스페이스를 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 이 문서는 또한 배포의 실행에 대해 지정된 매개 변수도 정의합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성][]을 참조하세요.

전체 템플릿은 GitHub에서 [서비스 버스 네임스페이스 템플릿][]을 참조하세요.

>[AZURE.NOTE] 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
>
>-    [이벤트 허브 및 소비자 그룹이 있는 이벤트 허브 네임스페이스 만들기](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [큐가 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
>-    [토픽 및 구독이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
>-    [큐 및 권한 부여 규칙이 있는 서비스 버스 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
>
>최신 템플릿을 확인하려면 서비스 버스에 대한 [Azure 빠른 시작 템플릿][] 갤러리 및 검색을 방문하세요.

## 배포할 항목

이 템플릿을 사용하여 [기본, 표준 또는 프리미엄](https://azure.microsoft.com/pricing/details/service-bus/) SKU가 있는 서비스 버스 네임스페이스를 배포합니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)로 바꿉니다.

## 매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters`라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### serviceBusNamespaceName

만들 서비스 버스 네임스페이스 이름입니다.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### serviceBusSKU

만들 서비스 버스 [SKU](https://azure.microsoft.com/pricing/details/service-bus/) 이름입니다.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

이 매개 변수에 허용되는 값(기본, 표준 또는 프리미엄)을 정의하고, 값이 지정되지 않은 경우에는 기본값(표준)을 할당하는 템플릿입니다.

서비스 버스 가격에 대한 자세한 내용은 [서비스 버스 가격 책정 및 대금 청구][]를 참조하세요.

### serviceBusApiVersion

템플릿의 서비스 버스 API 버전입니다.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## 배포할 리소스

### 서비스 버스 네임스페이스

**메시징** 형식의 표준 서비스 버스 네임스페이스를 만듭니다.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## 배포 실행 명령

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## 다음 단계

이제 Azure Resource Manager를 사용하여 리소스를 만들고 배포했으므로 다음 문서를 참조하여 이러한 리소스를 관리하는 방법에 대해 알아봅니다.

- [Azure 자동화를 사용하여 Azure 서비스 버스 관리](service-bus-automation-manage.md)
- [PowerShell을 사용하여 서비스 버스 관리](service-bus-powershell-how-to-provision.md)
- [서비스 버스 탐색기로 서비스 버스 리소스 관리](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager 템플릿 작성]: ../resource-group-authoring-templates.md
  [서비스 버스 네임스페이스 템플릿]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Azure 빠른 시작 템플릿]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [서비스 버스 가격 책정 및 대금 청구]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0921_2016-->