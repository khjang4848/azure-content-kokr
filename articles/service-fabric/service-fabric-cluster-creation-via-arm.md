
<properties
   pageTitle="Azure Resource Manager를 사용하여 보안 서비스 패브릭 클러스터 만들기 | Microsoft Azure"
   description="이 문서에서는 클라이언트 인증에 Azure Resource Manager, Azure 키 자격 증명 모음, Azure Active Directory (AAD)를 사용하여 Azure에 보안 서비스 패브릭 클러스터를 설정하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="vturecek"/>

# Azure Resource Manager를 사용하여 서비스 패브릭 클러스터 만들기

> [AZURE.SELECTOR]
- [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
- [Azure 포털](service-fabric-cluster-creation-via-portal.md)

Azure Resource Manager를 사용하여 Azure에 보안 Azure 서비스 패브릭 클러스터를 설정하는 단계를 안내하는 단계별 가이드입니다.단계별 가이드입니다. 이 가이드에서는 다음 단계를 단계별로 안내합니다.

 - 클러스터 및 응용 프로그램 보안에 대한 키를 관리할 키 자격 증명 모음을 설정합니다.
 - Azure Resource Manager를 사용하여 Azure에 보안된 클러스터를 만듭니다.
 - 클러스터 관리를 위해 Azure Active Directory (AAD)를 사용하여 사용자와 인증합니다.

보안 클러스터란 응용 프로그램, 서비스 및 포함된 데이터를 배포, 업그레이드 및 삭제하는 관리 작업에 무단 액세스하는 것을 방지하는 클러스터입니다. 비보안 클러스터란 언제라도 누구든지 연결하여 관리 작업을 수행할 수 있는 클러스터입니다. 비보안 클러스터를 만드는 것이 가능하지만 **보안 클러스터를 만드는 것이 좋습니다**. 비보안 클러스터는 **나중를 보안될 수 없습니다** -새 클러스터를 만들어야 합니다.

## Azure에 로그인
이 가이드에서는 [Azure PowerShell][azure-powershell]을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.

Azure 계정에 로그인합니다.

```powershell
Login-AzureRmAccount
```

구독을 선택합니다.

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## 주요 자격 증명 모음 설정

가이드의 이 부분에서는 Azure에서 서비스 패브릭 클러스터에 대해서와 서비스 패브릭 응용 프로그램에 대해서 주요 자격 증명 모음을 만드는 단계를 안내합니다. 키 자격 증명 모음에 대한 완전한 가이드는 [키 자격 증명 모음 시작 가이드][key-vault-get-started]를 참조하세요.

서비스 패브릭은 클러스터에 보안 적용을 하고 응용 프로그램 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다. Azure 키 자격 증명 모음은 Azure에서 서비스 패브릭 클러스터에 대한 인증서를 관리하는 데 사용됩니다. 클러스터를 Azure에 배포할 때 서비스 패브릭 클러스터 생성을 담당하는 Azure 리소스 공급자는 주요 자격 증명 모음에서 인증서를 가져와 클러스터 VM에 설치합니다.

다음 다이어그램은 주요 자격 증명 모음, 서비스 패브릭 클러스터 및 클러스터를 만들 때 주요 자격 증명 모음에 저장된 인증서를 사용하는 Azure 리소스 공급자 간의 관계를 보여 줍니다.

![인증서 설치][cluster-security-cert-installation]

### 리소스 그룹 만들기

첫 번째 단계는 특히 주요 자격 증명 모음에 대한 새로운 리소스 그룹을 생성하는 것입니다. 주요 자격 증명 모음을 자체 리소스 그룹에 두어 키 및 암호는 유실하지 않고 서비스 패브릭 클러스터가 있는 리소스 그룹과 같은 계산 및 저장소 리소스 그룹을 제거하도록 하는 것이 좋습니다. 사용자의 주요 자격 증명 모음을 가진 리소스 그룹은 그것을 사용 중인 클러스터와 동일한 지역에 있어야 합니다.

```powershell

	New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
	WARNING: The output object type of this cmdlet will be modified in a future release.
	
	ResourceGroupName : mycluster-keyvault
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### 주요 자격 증명 모음 만들기 

새 리소스 그룹에 주요 자격 증명 모음을 만듭니다. 서비스 패브릭 리소스 공급자가 인증서를 가져와 클러스터 노드에 설치하도록 허용하기 위해 주요 자격 증명 모음을 **배포에 대해 사용하도록 설정해야 합니다**.

```powershell

	New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
	
	
	Vault Name                       : myvault
	Resource Group Name              : mycluster-keyvault
	Location                         : West US
	Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
	Vault URI                        : https://myvault.vault.azure.net
	Tenant ID                        : <guid>
	SKU                              : Standard
	Enabled For Deployment?          : False
	Enabled For Template Deployment? : False
	Enabled For Disk Encryption?     : False
	Access Policies                  :
	                                   Tenant ID                :    <guid>
	                                   Object ID                :    <guid>
	                                   Application ID           :
	                                   Display Name             :    
	                                   Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
	                                   Permissions to Secrets   :    all
	
	
	Tags                             :
```

기존 주요 자격 증명 모음이 있는 경우라면 Azure CLI를 사용하여 배포에 대해 사용하도록 설정할 수 있습니다.

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## 주요 자격 증명 모음에 인증서 추가

인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. 서비스 패브릭에서 인증서가 사용되는 방식에 대한 자세한 내용은 [서비스 패브릭 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조하세요.

### 클러스터 및 서버 인증서(필수) 

이 인증서는 클러스터를 보호하고 무단 액세스를 방지하기 위해 필요합니다. 다음 몇 가지 방법으로 클러스터 보안을 제공합니다.
 
 - **클러스터 인증:** 클러스터 페더레이션에 대한 노드 간 통신을 인증합니다. 이 인증서로 자신의 신분을 증명할 수 있는 노드만 클러스터에 가입할 수 있습니다.
 - **서버 인증:** 관리 클라이언트에 클러스터 관리 끝점을 인증하여 관리 클라이언트기 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증서는 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer에 대해 SSL도 제공합니다.

이를 위해 인증서는 다음 요구 사항을 충족해야 합니다.

 - 인증서에 개인 키가 포함되어 있어야 합니다.
 - 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
 - 인증서의 주체 이름은 서비스 패브릭 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이러한 조건이 충족되어야 합니다. `.cloudapp.azure.com` 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

### 응용 프로그램 인증서(선택 사항)

응용 프로그램 보안을 위해 클러스터에 제한 없는 수의 인증서를 추가로 설치할 수 있습니다. 클러스터를 만들기 전에, 다음과 같이 노드에 인증서를 설치하도록 요구하는 응용 프로그램 보안 시나리오를 고려해 보세요.

 - 응용 프로그램 구성 값의 암호화 및 암호 해독
 - 복제 중에 노드 간 데이터 암호화

### Azure 리소스 공급자 사용을 위한 인증서 서식 지정

개인 키 파일(.pfx)을 추가하고 주요 자격 증명 모음을 통해 직접 사용할 수 있습니다. 그렇지만 Azure 리소스 공급자에서는 .pfx를 base-64로 인코딩된 문자열 상태로 포함하고 개인 키 암호를 포함하는 특수한 JSON 형식으로 키를 저장해야 합니다. 이러한 요구를 수용하기 위해 키를 JSON 문자열에 배치한 후 주요 자격 증명 모음에 *암호*로 저장해야 합니다.

이 프로세스를 보다 쉽게 수행할 수 있도록 하기 위해 PowerShell 모듈이 [GitHub에서 사용할 수 있게 제공됩니다][service-fabric-rp-helpers]. 모듈을 사용하려면 다음 단계를 수행합니다.

  1. 리포지토리의 전체 콘텐츠를 로컬 디렉터리에 다운로드합니다.
  2. PowerShell 창으로 모듈을 가져옵니다.

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
이 PowerShell 모듈의 `Invoke-AddCertToKeyVault` 명령은 자동으로 인증서 개인 키 서식을 JSON 문자열에 지정하고 주요 자격 증명 모음에 업로드합니다. 이를 사용하여 클러스터 인증서 및 추가 응용 프로그램 인증서를 주요 자격 증명 모음에 추가합니다. 클러스터에 설치하려는 모든 추가 인증서에 대해 이 단계를 반복합니다.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
	
	Switching context to SubscriptionId <guid>
	Ensuring ResourceGroup mycluster-keyvault in West US
	WARNING: The output object type of this cmdlet will be modified in a future release.
	Using existing valut myvault in West US
	Reading pfx file from C:\path\to\key.pfx
	Writing secret to myvault in vault myvault
	
	
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```


노드 인증, 관리 끝점 보안 및 인증, X.509 인증서를 사용하는 모든 추가 응용 프로그램 보안 기능에 대한 인증서를 설치하는 서비스 패브릭 클러스터 Resource Manager 템플릿을 구성하기 위해 주요 자격 증명 모음에 대해 이러한 작업이 선행되어야 합니다. 이제 Azure에는 다음과 같은 설정이 구성됩니다.

 - 주요 자격 증명 모음 리소스 그룹
   - 키 자격 증명 모음
     - 클러스터 서버 인증 인증서
     - 응용 프로그램 인증서

## 클라이언트 인증에 대한 Azure Active Directory 설정

AAD를 통해 조직(테넌트라고도 함)에서 웹 기반 로그인 UI를 사용하는 응용 프로그램과 네이티브 클라이언트 환경을 사용하는 응용 프로그램으로 구분되는 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다. 이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법][active-directory-howto-tenant]을 참조하세요.

서비스 패브릭 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 AAD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.

서비스 패브릭 클러스터로 AAD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

>[AZURE.NOTE] 스크립트에 클러스터 이름 및 끝점이 필요한 경우 이러한 값은 이미 만들어진 값이 아니라 계획된 값이어야 하므로 클러스터를 만들기 *이전*에 이러한 단계를 수행해야 합니다.

1. [스크립트를 컴퓨터에 다운로드][sf-aad-ps-script-download]합니다.

2. zip 파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택한 후 **차단 해제** 확인란을 선택하고 적용합니다.

3. zip 파일의 압축을 풉니다.

4. TenantId, ClusterName 및 WebApplicationReplyUrl을 매개 변수로 제공하여 `SetupApplications.ps1`을 실행합니다. 예:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Azure 클래식 포털에서 테넌트용 URL을 확인하여 **TenantId**를 찾을 수 있습니다. 해당 URL에 포함된 GUID가 TenantId입니다. 예:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName**은 스크립트에 의해 만들어진 AAD 응용 프로그램에 접두사를 지정하는 데 사용됩니다. 함께 사용되는 서비스 패브릭 클러스터에 AAD 아티팩트를 쉽게 매핑하기 위해서만 사용되므로 실제 클러스터 이름과 정확히 일치하지 않아도 됩니다.

    **WebApplicationReplyUrl**은 로그인 프로세스가 완료된 이후 AAD에서 사용자를 돌려보낼 기본 끝점입니다. 이 끝점을 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 끝점으로 설정해야 합니다.

    https://&lt;cluster_domain&gt;:19080/Explorer

    AAD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 이렇게 하면 스크립트는 서비스 패브릭 클러스터를 나타내는 웹 및 네이티브 응용 프로그램 만들기를 계속 진행합니다. [Azure 클래식 포털][azure-classic-portal]에서 테넌트의 응용 프로그램을 보면 두 개의 새 항목이 표시됩니다.

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    다음 섹션에서 클러스터를 만들 때 스크립트는 Azure Resource Manager 템플릿에서 필요한 Json을 인쇄하므로 PowerShell 창을 계속 열어 둡니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## 서비스 패브릭 클러스터 Resource Manager 템플릿 만들기

이 섹션에서는 이전 PowerShell 명령의 출력이 서비스 패브릭 클러스터 Resource Manager 템플릿에서 사용됩니다.

샘플 리소스 관리자 템플릿은 [GitHub의 Azure 빠른 시작 템플릿 갤러리][azure-quickstart-templates]에 제공됩니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다.

### 리소스 관리자 템플릿 만들기

이 가이드에서는 [5 노드 보안 클러스터][service-fabric-secure-cluster-5-node-1-nodetype-wad] 예제 템플릿과 템플릿 매개 변수를 사용합니다. `azuredeploy.json`와 `azuredeploy.parameters.json`을 컴퓨터에 다운로드하고 선호하는 텍스트 편집기로 두 파일을 엽니다.

### 인증서 추가

인증서는 인증서 키를 포함하는 키 자격 증명 모음을 참조하여 클러스터 Resource Manager 템플릿에 추가합니다. 이러한 키 자격 증명 모음 값은 Resource Manager 템플릿 파일을 재사용할 수 있고 배포에 따라 값이 달라지지 않도록 유지하기 위해 Resource Manager 템플릿 매개 변수 파일에 배치하는 것이 좋습니다.

#### VMSS osProfile에 모든 인증서 추가

클러스터에 설치해야 하는 모든 인증서는 VMSS 리소스(Microsoft.Compute/virtualMachineScaleSets)의 osProfile 섹션에 구성되어야 합니다. 이것은 리소스 공급자에게 인증서를 VM에 설치하도록 지시합니다. 클러스터 인증서뿐 아니라 응용 프로그램에 사용하려는 모든 응용 프로그램 보안 인증서를 포함합니다.

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### 서비스 패브릭 클러스터 인증서 구성

또한 클러스터 인증 인증서는 서비스 패브릭 클러스터 리소스(Microsoft.ServiceFabric/clusters) 및 VMSS 리소스에서 VMSS에 대한 서비스 패브릭 클러스트 확장에서 구성되어야 합니다. 이 경우 서비스 패브릭 리소스 공급자는 클러스터 인증에 대한 사용 및 관리 끝점에 대한 서버 인증을 위해 그것을 구성할 수 있습니다.

##### VMSS 리소스:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### 서비스 패브릭 리소스:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### AAD 구성 삽입

이전에 만든 AAD 구성은 Resource Manager 템플릿에 직접 삽입할 수 있으나 Resource Manager 템플릿 파일을 재사용할 수 있고 배포에 따라 값이 달라지지 않도록 유지하기 위해 값을 매개 변수로 먼저 매개 변수 파일로 추출하는 것이 좋습니다.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### Azure Resource Manager 템플릿 매개 변수 구성

마지막으로, 키 자격 증명 모음 및 AAD PowerShell 명령의 출력 값을 매개 변수 파일을 채우는 데 사용합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
이제 이 시점에 다음과 같은 것들이 있어야 합니다.

 - 주요 자격 증명 모음 리소스 그룹
    - 키 자격 증명 모음
    - 클러스터 서버 인증 인증서
    - 데이터 암호화 인증서
 - Azure Active Directory 테넌트
    - 웹 기반 관리 및 Service Fabric Explorer에 대 한 AAD 응용 프로그램
    - 네이티브 클라이언트 관리에 대한 AAD 응용 프로그램
    - 역할이 할당된 사용자
 - 서비스 패브릭 클러스터 Resource Manager 템플릿
    - 키 자격 증명 모음을 통해 구성된 인증서
    - 구성된 Azure Active Directory

다음 다이어그램은 키 자격 증명 모음 및 AAD 구성이 Resource Manager 템플릿 어디에 적합한지 보여 줍니다.

![리소스 관리자 종속성 맵][cluster-security-arm-dependency-map]

## 클러스터 만들기

이제 [ARM 배포][resource-group-template-deploy]를 사용하여 클러스터를 만들 준비가 되었습니다.

#### 테스트

매개 변수 파일로 Resource Manager 템플릿을 테스트하려면 다음 PowerShell 명령을 사용합니다.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### 배포

Resource Manager 템플릿 테스트를 통과했다면 매개 변수 파일로 Resource Manager 템플릿을 배포하기 위해 다음 PowerShell 명령을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

## 역할에 사용자 할당

클러스터를 나타내는 응용 프로그램을 만들었으면 사용자를 서비스 패브릭에서 지원하는 역할(읽기 전용 및 관리자)에 할당해야 합니다. 이 작업은 [Azure 클래식 포털][azure-classic-portal]에서 수행할 수 있습니다.

1. 테넌트로 이동하고 응용 프로그램을 선택합니다.
2. `myTestCluster_Cluster`라는 이름의 웹 응용 프로그램을 선택합니다.
3. 사용자 탭을 클릭합니다.
4. 할당할 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.

    ![역할에 사용자 할당 단추][assign-users-to-roles-button]

5. 사용자에게 할당할 역할을 선택합니다.

    ![역할에 사용자 할당][assign-users-to-roles-dialog]

>[AZURE.NOTE] 서비스 패브릭의 역할에 대한 자세한 내용은 [서비스 패브릭 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.


## 다음 단계

이제 Azure Active Directory를 사용하여 관리 인증을 제공하는 보안 클러스터가 생겨났습니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [응용 프로그램 암호를 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[aad-graph-api-docs]: https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png

<!---HONumber=AcomDC_0921_2016-->