<properties
   pageTitle="원하는 상태 구성 Resource Manager 템플릿 | Microsoft Azure"
   description="Azure에서 원하는 상태 구성에 대한 Resource Manager 템플릿 정의 및 예제와 문제 해결"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# Azure Resource Manager 템플릿을 사용하여 Windows VMSS 및 원하는 상태 구성
이 문서에서는 [필요한 상태 구성 확장 처리기](virtual-machines-windows-extensions-dsc-overview.md)를 위한 Resource Manager 템플릿에 대해 설명합니다.

## Windows VM의 템플릿 예제

다음 코드 조각은 템플릿의 Resource 섹션에 대한 코드입니다.

```json
			"name": "Microsoft.Powershell.DSC",
			"type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## Windows VMSS의 템플릿 예제

VMSS 노드에는 "VirtualMachineProfile", "extensionProfile" 특성을 포함하는 "properties" 섹션이 있습니다. "extensions" 아래에 DSC가 추가됩니다.

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## 자세한 설정 정보

다음 스키마는 Azure Resource Manager 템플릿에 있는 Azure DSC 확장의 설정 부분에 대한 것입니다.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## 세부 정보
| 속성 이름 | 형식 | 설명 |
| --- | --- | --- |
| settings.wmfVersion | string | VM에 설치해야 하는 Windows Management Framework의 버전을 지정합니다. 이 속성을 'latest'로 설정하면 WMF의 최신 업데이트 버전이 설치됩니다. 현재 이 속성에 대해 설정 가능한 값은 **'4.0', '5.0', '5.0PP' 및 'latest'**뿐입니다. 가능한 값은 업데이트에 따라 달라집니다. 기본값은 'latest'입니다.|
| settings.configuration.url | string | DSC 구성 zip 파일을 다운로드할 URL 위치를 지정합니다. 제공된 URL에서 액세스를 위해 SAS 토큰을 요구하는 경우 protectedSettings.configurationUrlSasToken 속성을 SAS 토큰 값으로 설정해야 합니다. settings.configuration.script 및/또는 settings.configuration.function이 정의된 경우 이 속성이 필요합니다. |
| settings.configuration.script | string | DSC 구성의 정의를 포함하는 스크립트의 파일 이름을 지정합니다. 이 스크립트는 configuration.url 속성에 지정된 URL에서 다운로드된 zip 파일의 루트 폴더에 있어야 합니다. settings.configuration.url 및/또는 settings.configuration.script가 정의된 경우 이 속성이 필요합니다. |
| settings.configuration.function | string | DSC 구성의 이름을 지정합니다. 명명된 구성은 configuration.script에 정의된 스크립트에 있어야 합니다. settings.configuration.url 및/또는 settings.configuration.function이 정의된 경우 이 속성이 필요합니다. |
| settings.configurationArguments | 컬렉션 | DSC 구성을 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화되지 않습니다. |
| settings.configurationData.url | string | DSC 구성에 대한 입력으로 사용할 구성 데이터(.pds1) 파일을 다운로드할 URL을 지정합니다. 제공된 URL에서 액세스를 위해 SAS 토큰을 요구하는 경우 protectedSettings.configurationDataUrlSasToken 속성을 SAS 토큰 값으로 설정해야 합니다.|
| settings.privacy.dataEnabled | string | 원격 분석 수집을 사용하거나 사용하지 않도록 설정합니다. 이 속성에 설정할 수 있는 값은 **'Enable', 'Disable', '' 또는 $null**뿐입니다. 이 속성을 비워 두거나 null로 설정하면 원격 분석이 사용됩니다. 기본값은 ''입니다. [추가 정보](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | 컬렉션 | WMF를 다운로드할 대체 위치를 정의합니다. [추가 정보](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | 컬렉션 | DSC 구성을 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationUrlSasToken | string | configuration.url에서 정의한 URL에 액세스하기 위해 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationDataUrlSasToken | string | configuration.url에서 정의한 URL에 액세스하기 위해 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |

## Settings 및 ProtectedSettings
모든 설정은 VM의 설정 텍스트 파일에 저장됩니다. 'settings'의 속성은 설정 텍스트 파일에서 암호화되지 않으므로 공용 속성입니다. 'protectedSettings'의 속성은 인증서를 사용하여 암호화되므로 VM에서 이 파일에 일반 텍스트로 표시되지 않습니다.

구성에 자격 증명이 필요한 경우 protectedSettings에 포함될 수 있습니다.

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
       	    "userName": "UsernameValue1",
       	    "password": "PasswordValue1"
        }
    }
}
```

## 예

다음 예제는 [DSC 확장 처리기 개요 페이지](virtual-machines-windows-extensions-dsc-overview.md)의 "시작" 섹션에서 파생됩니다. 이 예제에서는 cmdlet 대신 Resource Manager 템플릿을 사용하여 확장을 배포합니다. "IisInstall.ps1" 구성을 저장하고 .ZIP 파일에 배치한 다음 파일을 액세스 가능한 URL에 업로드합니다. 이 예제에서는 Azure Blob Storage를 사용하지만 임의의 위치에서 .ZIP 파일을 다운로드할 수 있습니다.

Azure Resource Manager 템플릿에서 다음 코드는 VM에 올바른 파일을 다운로드하고 적절한 PowerShell 함수를 실행하도록 지시합니다.

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## 이전 형식에서 업데이트
이전 형식의 모든 설정(공용 속성 ModulesUrl, ConfigurationFunction, SasToken 또는 Properties 포함)은 현재 형식으로 자동 조정되며 이전과 같이 실행됩니다.

이전 설정 스키마는 다음과 같습니다.

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

다음은 현재 형식에 맞게 이전 형식을 조정하는 방법입니다.

| 속성 이름 | 이전 스키마에 해당 |
| --- | --- |
| settings.wmfVersion | settings.WMFVersion |
| settings.configuration.url | settings.ModulesUrl |
| settings.configuration.script | settings.ConfigurationFunction의 첫 번째 부분('\\\' 앞) |
| settings.configuration.function | settings.ConfigurationFunction의 두 번째 부분('\\\' 뒤) |
| settings.configurationArguments | settings.Properties |
| settings.configurationData.url | protectedSettings.DataBlobUri(SAS 토큰 없이) |
| settings.privacy.dataEnabled | settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | settings.SasToken |
| protectedSettings.configurationDataUrlSasToken | protectedSettings.DataBlobUri의 SAS 토큰 |


## 문제 해결 - 오류 코드 1100
오류 코드 1100은 DSC 확장에 대한 사용자 입력에 문제가 있다는 것을 나타냅니다. 이러한 오류의 텍스트는 변수이며 변경될 수 있습니다. 다음은 발생할 수 있는 일부 오류와 해결 방법입니다.

### 잘못된 값
"Privacy.dataCollection이 '{0}'입니다. 가능한 값은 '', 'Enable' 및 'Disable'뿐입니다." "WmfVersion이 '{0}'입니다. 가능한 값은 … 및 'latest'뿐입니다."

문제점: 제공된 값이 허용되지 않습니다.

해결 방법: 잘못된 값을 올바른 값으로 변경합니다. 세부 정보 섹션의 표를 참조하세요.

### 잘못된 URL
"ConfigurationData.url은 '{0}'입니다. 유효한 URL이 아닙니다." "DataBlobUri가 '{0}'입니다. 유효한 URL이 아닙니다." "Configuration.url이 '{0}'입니다. 유효한 URL이 아닙니다."

문제점: 제공된 URL이 유효하지 않습니다.

해결 방법: 제공된 모든 URL을 확인합니다. 모든 URL이 원격 컴퓨터의 확장 기능에서 액세스할 수 있는 올바른 위치인지 확인합니다.

### 잘못된 ConfigurationArgument 형식
"잘못된 configurationArguments 형식 {0}입니다."

문제점: ConfigurationArguments 속성을 Hashtable 개체로 해석할 수 없습니다.

해결 방법: ConfigurationArguments 속성을 해시 테이블로 만듭니다. 위의 예제에서 제공한 형식을 따릅니다. 따옴표, 쉼표 및 중괄호를 확인합니다.

### 중복 ConfigurationArguments
"공용 및 보호된 configurationArguments에 중복 인수 '{0}'이(가) 있습니다."

문제점: 공용 설정의 ConfigurationArguments 및 보호된 설정의 ConfigurationArguments에 동일한 이름의 속성이 포함되어 있습니다.

해결 방법: 중복된 속성 중 하나를 제거합니다.

### 누락된 속성
"Configuration.function을 사용하려면 configuration.url 또는 configuration.module을 지정해야 합니다."

"Configuration.url을 사용하려면 configuration.script를 지정해야 합니다."

"Configuration.script를 사용하려면 configuration.url을 지정해야 합니다."

"Configuration.url을 사용하려면 configuration.function을 지정해야 합니다."

"ConfigurationUrlSasToken을 사용하려면 configuration.url을 지정해야 합니다."

"ConfigurationDataUrlSasToken을 사용하려면 configurationData.url을 지정해야 합니다."

문제점: 정의된 속성에 누락된 다른 속성이 필요합니다.

솔루션:
- 누락된 속성을 제공합니다.
- 누락된 속성을 요구하는 속성을 제거합니다.


## 다음 단계
[DSC 및 VMSS에 대한 자세한 정보](virtual-machines-scale-sets-dsc.md)

[DSC의 보안 자격 증명 관리](virtual-machines-windows-extensions-dsc-credentials.md)에서 자세한 내용을 참조하세요.

Azure DSC 확장 처리기에 대한 자세한 내용은 [Azure 필요한 상태 구성 확장 처리기 소개](virtual-machines-windows-extensions-dsc-overview.md)를 참조하세요.

PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터를 방문하세요](https://msdn.microsoft.com/powershell/dsc/overview).

<!---HONumber=AcomDC_0921_2016-->