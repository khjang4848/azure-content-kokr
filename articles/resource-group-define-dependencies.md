<properties
   pageTitle="Resource Manager 템플릿의 종속성 | Microsoft Azure"
   description="리소스가 올바른 순서대로 배포되도록 배포 중 다른 리소스에 종속된 것으로 리소스를 설정하는 방법에 대해 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 템플릿에서 종속성 정의

주어진 리소스에 대해 해당 리소스를 배포하기 전에 존재해야 하는 다른 리소스가 있을 수 있습니다. 예를 들어 SQL 데이터베이스를 배포하려면 SQL Server가 있어야 합니다. 하나의 리소스를 다른 리소스에 종속된 것으로 표시하여 이 관계를 정의합니다. 일반적으로 **dependsOn** 요소로 종속성을 정의하지만 **reference** 함수를 통해 정의할 수도 있습니다.

Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다.

## dependsOn

템플릿 내에서 dependsOn 요소를 사용하면 하나의 리소스를 하나 이상의 리소스에 종속된 것으로 정의할 수 있습니다. 값은 리소스 이름의 쉼표로 구분된 목록일 수 있습니다.

다음 예제에서는 부하 분산 장치, 가상 네트워크 및 여러 저장소 계정을 만드는 루프에 종속된 가상 컴퓨터 크기 집합을 보여 줍니다. 이러한 다른 리소스는 다음 예제에 표시되어 있지 않지만 템플릿 내 다른 곳에 존재해야 합니다.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

리소스와 copy 루프를 통해 만들어진 리소스 사이의 종속성을 정의하려면 dependsOn 요소를 루프의 이름으로 설정합니다. 예제는 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

dependsOn을 사용하여 리소스 간의 관계를 매핑하도록 할 수 있지만 배포의 성능에 영향을 줄 수 있으므로 해당 작업을 수행하는 이유를 이해하는 것이 중요합니다. 예를 들어, 리소스가 상호 연결되는 방식을 문서화하려면, dependsOn은 올바른 접근 방법이 아닙니다. 배포 후 dependsOn 요소에 어떤 리소스가 정의되었는지 쿼리할 수 없습니다. dependsOn을 사용하면 Resource Manager는 종속성이 있는 두 리소스를 병렬로 배포하지 않으므로 배포 시간에 잠재적으로 영향을 줍니다. 리소스 간의 관계를 문서화하려면 [리소스 연결](resource-group-link-resources.md)을 대신 사용합니다.

## 자식 리소스

resources 속성을 사용하면 정의되는 리소스에 관련된 자식 리소스를 지정할 수 있습니다. 자식 리소스는 5개 수준 깊이까지만 정의할 있습니다. 자식 리소스 및 부모 리소스 간에 암시적 종속성은 생성되지 않습니다. 부모 리소스 다음에 자식 리소스를 배포해야 하는 경우 dependsOn 속성을 사용하여 해당 종속성을 확실하게 명시해야 합니다.

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 허용되는 리소스 종류는 부모 리소스의 [템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas)에서 지정됩니다. 자식 리소스 종류의 이름에는 부모 리소스 종류의 이름이 포함됩니다. 예를 들어 **Microsoft.Web/sites/config**와 **Microsoft.Web/sites/extensions**는 둘 다 **Microsoft.Web/sites**의 자식 리소스입니다.

다음 예제에서는 SQL Server 및 SQL 데이터베이스를 보여 줍니다. 데이터베이스가 서버의 자식인 경우에도 SQL 데이터베이스와 SQL Server 간에 명시적 종속성이 정의되어 있습니다.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## reference 함수

[reference 함수](resource-group-template-functions.md#reference)를 사용하면 식을 다른 JSON 이름 및 값 쌍 또는 런타임 리소스에서 해당 값을 파생시키는 식을 작성할 수 있습니다. 참조 식은 한 리소스가 다른 리소스에 종속되어 있음을 암시적으로 선언합니다.

    reference('resourceName').propertyPath

이 요소 또는 dependsOn 요소를 사용하여 종속성을 지정할 수 있지만 같은 종속 리소스에 대해 두 가지를 모두 사용할 필요는 없습니다. 가능하면 불필요한 종속성을 실수로 추가하지 않도록 암시적 참조를 사용합니다.

자세한 내용은 [reference 함수](resource-group-template-functions.md#reference)를 참조하세요.

## 다음 단계

- Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->