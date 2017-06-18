<properties
   pageTitle="템플릿을 사용하는 Linux VM의 사용자 지정 스크립트 | Microsoft Azure"
   description="리소스 관리자 템플릿과 함께 사용자 지정 스크립트 확장을 사용하여 VM 구성 작업을 자동화합니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Resource Manager 템플릿을 사용하여 Linux VM에 대한 사용자 지정 스크립트 확장 사용

이 문서에서는 Linux VM의 워크로드를 부트스트랩하기 위해 사용자 지정 스크립트 확장을 사용하여 Azure Resource Manager 템플릿을 작성하는 방법을 간략하게 설명합니다.

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Linux VM의 템플릿 예제

템플릿의 리소스 섹션에서 다음 확장 리소스 정의

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "MyCustomScriptExtension",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh"
      ],
      "commandToExecute": "sh mongo-install-ubuntu.sh"
    },
    "protectedSettings": {}
  }
}
```

위의 예제에서 사용자 고유의 설정으로 파일 URL 및 파일 이름을 바꿉니다.

템플릿을 작성한 후 Azure CLI를 사용하여 배포할 수 있습니다.

사용자 지정 스크립트 확장을 사용하여 VM에서 응용 프로그램을 구성하는 전체 샘플은 아래 예제를 참조하세요.

* [Linux VM의 사용자 지정 스크립트 확장](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0914_2016-->