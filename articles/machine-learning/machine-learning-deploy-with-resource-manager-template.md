<properties
	pageTitle="Azure Resource Manager 템플릿을 사용하여 기계 학습 작업 영역 배포 | Microsoft Azure"
	description="Azure Resource Manager 템플릿을 사용하여 Azure 기계 학습에 대한 작업 영역을 배포하는 방법"
	services="machine-learning"
	documentationCenter=""
	authors="ahgyger"
	manager="haining"
	editor="garye"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="ahgyger"/>
# Azure Resource Manager를 사용하여 기계 학습 작업 영역 배포

## 소개
Azure Resource Manager 배포 템플릿을 사용하면 유효성 검사와 상호 연결된 구성 요소를 배포하고 메커니즘을 다시 시도하는 확장성 있는 방법을 제공하여 시간을 절약할 수 있습니다. Azure 기계 학습 작업 영역을 설정하려면 예를 들어 먼저 Azure 저장소 계정을 구성한 다음 작업 영역을 배포해야 합니다. 수백 개의 작업 영역에 대해 이 작업을 수동으로 수행한다고 가정합니다. 쉬운 대안은 Azure Resource Manager 템플릿을 사용하여 Azure 기계 학습 작업 영역 및 모든 종속성을 배포하는 것입니다. 이 문서는 이 과정을 단계별로 안내합니다. Azure Resource Manager에 대한 개요는 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

## 단계별: 기계 학습 작업 영역 만들기
Azure 리소스 그룹을 만든 다음 리소스 관리자 템플릿을 사용하여 새 Azure 저장소 계정 및 새 Azure 기계 학습 작업 영역을 배포합니다. 배포가 완료되면 생성된 작업 영역에 대한 중요한 정보를 인쇄합니다(기본 키, workspaceID 및 작업 영역에 대한 URL).

### Azure Resource Manager 템플릿 만들기
기계 학습 작업 영역은 연결된 데이터 집합을 저장하려면 Azure 저장소 계정이 필요합니다. 다음 템플릿은 리소스 그룹의 이름을 사용하여 저장소 계정 이름 및 작업 영역 이름을 생성합니다. 또한 작업 영역을 만들 때 속성으로 저장소 계정 이름을 사용합니다.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
c:\\temp 아래에 mlworkspace.json 파일로 이 템플릿을 저장합니다.

### 템플릿을 기반으로 리소스 그룹 배포
* PowerShell 열기
* Azure Resource Manager 및 Azure 서비스 관리에 대한 모듈 설치

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   이러한 단계는 나머지 단계를 완료하는 데 필요한 모듈을 다운로드 및 설치합니다. PowerShell 명령을 실행하는 환경에서 한 번만 수행하면 됩니다.

* Azure에 대한 인증

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
이 단계는 각 세션에 대해 반복해야 합니다. 인증되면 구독 정보가 표시됩니다.

![Azure 계정][1]

이제 Azure에 대한 액세스가 있으므로 리소스 그룹을 만들 수 있습니다.

* 리소스 그룹 만들기

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

리소스 그룹이 올바르게 프로비전되었는지 확인합니다. **ProvisioningState**는 "Succeeded"여야 합니다. 리소스 그룹 이름은 저장소 계정 이름을 생성하는 템플릿에 의해 사용됩니다. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다.

![리소스 그룹][2]

* 리소스 그룹 배포를 사용하여 새 기계 학습 작업 영역을 배포합니다.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

배포가 완료되면 배포한 작업 영역의 속성에 액세스하는 것은 간단합니다. 예를 들어 기본 키 토큰에 액세스할 수 있습니다.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

기존 작업 영역의 토큰을 검색하는 또 다른 방법은 Invoke-AzureRmResourceAction 명령을 사용하는 것입니다. 예를 들어 모든 작업 영역의 기본 및 보조 토큰을 나열할 수 있습니다.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
작업 영역이 프로비전되면 [Azure 기계 학습에 대한 PowerShell 모듈](http://aka.ms/amlps)을 사용하여 많은 Azure 기계 학습 스튜디오 작업을 자동화할 수도 있습니다.

## 다음 단계 
* [Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)에 대해 자세히 알아봅니다.
* [Azure 빠른 시작 템플릿 리포지토리](https://github.com/Azure/azure-quickstart-templates)를 살펴봅니다.
* [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)에 대한 이 동영상을 시청합니다.
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

<!---HONumber=AcomDC_0921_2016-->