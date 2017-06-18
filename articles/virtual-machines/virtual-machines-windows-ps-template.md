<properties
	pageTitle="리소스 관리자 템플릿을 사용하여 VM 만들기 | Microsoft Azure"
	description="리소스 관리자 템플릿 및 PowerShell을 사용하여 새 Windows 가상 컴퓨터를 쉽게 만들 수 있습니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# 리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기

이 문서에서는 Azure Resource Manager 템플릿을 소개하고 PowerShell을 사용하여 배포하는 방법을 보여 줍니다. 템플릿은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 컴퓨터를 배포합니다.

이 문서의 단계를 수행하려면 약 20분이 걸립니다.

> [AZURE.IMPORTANT] VM을 가용성 집합의 일부가 되도록 하려면 VM을 만들 때 해당 집합에 추가해야 합니다. 현재는 VM을 만든 이후에 가용성 집합에 추가하는 방법이 없습니다.

## 1단계: 템플릿 파일 만들기

[Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)에 있는 정보를 사용하여 고유의 템플릿을 만들 수 있습니다. [Azure Quickstarts 템플릿](https://azure.microsoft.com/documentation/templates/)으로 만든 고유의 템플릿을 배포할 수도 있습니다.

1. 원하는 텍스트 편집기를 열고 이 JSON 정보를 *VirtualMachineTemplate.json*이라는 새 파일에 복사합니다.

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [
                    "[variables('addressPrefix')]"
                  ]
                },
                "subnets": [
                  {
                    "name": "[variables('subnetName')]",
                    "properties": {
                      "addressPrefix": "[variables('subnetPrefix')]"
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [
                  {
                    "name": "ipconfig1",
                    "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                      },
                      "subnet": {
                        "id": "[variables('subnetRef')]"
                      }
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
                    "version":"latest"
                  },
                  "osDisk" : {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces": [
                    {
                      "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                    }
                  ]
                }
              }
            }
          ]
        }
        
    >[AZURE.NOTE] 이 문서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.
    
2. 템플릿 파일을 저장합니다.

## 2단계: 매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 포함된 매개 변수 파일을 만들고 템플릿과 함께 리소스 관리자에 제출합니다.

1. 텍스트 편집기에서 이 JSON 정보를 *Parameters.json*이라는 새 파일에 복사합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] 자세한 내용은 [사용자 이름 및 암호 요구 사항](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)을 참조하세요.

2. 매개 변수 파일을 저장합니다.

## 3단계: Azure PowerShell 설치

최신 버전의 Azure PowerShell을 설치하는 방법, 사용할 구독을 선택하는 방법, Azure 계정에 로그인하는 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## 4단계: 리소스 그룹 만들기

모든 리소스는 리소스 그룹에 배포되어야 합니다. 자세한 내용은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.

	    Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. **$locName** 값을 목록의 위치(예: **미국 중부**)로 바꿉니다. 변수를 만듭니다.

        $locName = "location name"
        
3. **$rgName** 값을 새 리소스 그룹 이름으로 바꿉니다. 변수 및 리소스 그룹을 만듭니다.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    다음과 유사한 결과가 표시됩니다.
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### 5단계: 템플릿 및 매개 변수를 사용하여 리소스 만들기

1. **$deployName** 값을 배포 이름으로 바꿉니다. **$templatePath** 값을 템플릿 파일의 경로 및 이름으로 바꿉니다. **$parameterFile** 값을 매개 변수 파일의 경로 및 이름으로 바꿉니다. 변수를 만듭니다.

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

2. 템플릿을 배포합니다.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    다음과 같이 표시됩니다.

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] Azure 저장소 계정에서 템플릿 및 매개 변수를 배포할 수도 있습니다. 자세한 내용은 [Azure Storage와 함께 Azure PowerShell 사용](../storage-powershell-guide-full.md)을 참조하세요.

## 다음 단계

- 배포에 문제가 있는 경우 다음 단계로서 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 살펴봅니다.
- [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 검토하여 방금 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->