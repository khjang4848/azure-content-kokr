<properties
	pageTitle="스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정 | Microsoft Azure"
	description="스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터에 사용자 지정 구성 요소를 추가하는 방법을 알아봅니다. 스크립트 작업은 클러스터 노드에서 실행되는 Bash 스크립트로, 클러스터 구성을 사용자 지정하거나 다른 서비스 및 유틸리티(예: Hue, Solr 또는 R)를 추가하는 데 사용할 수 있습니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="larryfr"/>

# 스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정

HDInsight는 클러스터를 사용자 지정하는 사용자 지정 스크립트를 호출하는 **스크립트 작업**이라는 구성 옵션을 제공합니다. 이러한 스크립트는 클러스터를 만들 때 또는 이미 실행 중인 클러스터에 사용할 수 있으며, 추가 구성 요소를 설치하거나 구성 설정을 변경하는 데 사용됩니다.

> [AZURE.NOTE] 이미 실행 중인 클러스터에 스크립트 작업을 사용하는 기능은 Linux 기반 HDInsight 클러스터에만 제공됩니다. Windows 기반 클러스터에 스크립트 작업을 사용하는 방법은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Windows)](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

스크립트 작업을 Azure 마켓플레이스에 HDInsight 응용 프로그램으로 게시할 수도 있습니다. 이 문서의 일부 예제는 PowerShell 및.NET SDK의 스크립트 작업 명령을 사용하여 HDInsight 응용 프로그램을 설치하는 방법을 보여 줍니다. HDInsight 응용 프로그램에 대한 자세한 내용은 [Azure 마켓플레이스에 HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md)를 참조하세요.

## 스크립트 작업 이해

스크립트 작업은 사용자로부터 URL 및 매개 변수를 제공 받은 후 HDInsight 클러스터 노드에서 실행되는 간단한 Bash 스크립트입니다. 다음은 스크립트 작업의 특징 및 기능입니다.

* HDInsight 클러스터에서 액세스할 수 있는 URI에 저장되어야 합니다. 가능한 저장소 위치는 다음과 같습니다.

    * HDInsight 클러스터에 대한 기본 또는 추가 저장소 계정인 Blob 저장소 계정. HDInsight는 클러스터를 만드는 동안 이러한 두 유형을 저장소 계정 모두에 대해 액세스 권한을 부여받으므로 public이 아닌 스크립트 작업을 사용할 수 있게 됩니다.
    
    * Azure Blob, GitHub, OneDrive, Dropbox 등과 같은 공개적으로 읽을 수 있는 URI.
    
    Blob 컨테이너(공개적으로 읽을 수 있음)에 저장된 스크립트의 URI 예를 보려면 [예제 스크립트 작업 스크립트](#example-script-action-scripts) 섹션을 참조하세요.

* 헤드 노드 또는 작업자 노드와 같은 __특정 노드 유형에서만 실행__되도록 제한할 수 있습니다.

    > [AZURE.NOTE] HDInsight Premium에서 사용할 경우 스크립트를 에지 노드에서 사용해야 한다고 지정할 수 있습니다.

* __지속형__ 또는 __임시__ 스크립트일 수 있습니다.

    __지속형__ 스크립트는 작업자 노드에 적용되며 클러스터를 강화할 때 생성되는 새 노드에서 자동으로 실행됩니다.

    또한 지속형 스크립트는 헤드 노드 같은 다른 노드 유형에 변경 내용을 적용할 수도 있지만, 기능의 관점에서 스크립트를 지속하는 유일한 이유는 클러스터를 강화할 때 생성되는 새 작업자 노드에 적용하기 위함입니다.

    > [AZURE.IMPORTANT] 지속형 스크립트 작업은 고유한 이름이 있어야 합니다.

    __임시__ 스크립트는 보존되지 않습니다. 하지만 이후에 임시 스크립트를 지속형 스크립트로 승격하거나 지속형 스크립트를 임시 스크립트로 강등할 수 있습니다.

    > [AZURE.IMPORTANT] 클러스터를 만들 때 사용되는 스크립트 작업은 자동으로 보존됩니다.
    >
    > 사용자가 지속형 스크립트로 지정하더라도 실패하는 스크립트는 보존되지 않습니다.

* 실행 중에 스크립트에서 사용하는 __매개 변수__를 수락할 수 있습니다.

* 클러스터 노드에서 __루트 수준 권한__으로 실행됩니다.

* __Azure 포털__, __Azure PowerShell__, __Azure CLI__ 또는 __HDInsight .NET SDK__를 통해 사용할 수 있습니다.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

클러스터에 어떤 스크립트가 적용되었는지 이해하고 승격 또는 강등할 스크립트 ID를 확인하는 데 도움을 주기 위해, 지금까지 실행된 모든 스크립트 기록이 클러스터에 보관됩니다.

> [AZURE.IMPORTANT] 스크립트 작업을 통해 변경된 내용을 자동으로 취소하는 방법은 없습니다. 스크립트 효과를 되돌리려면 변경된 내용을 이해하고 수동으로 되돌려야(또는 변경 내용을 되돌리는 스크립트 작업을 제공) 합니다

### 클러스터 만들기 프로세스의 스크립트 작업

클러스터를 만들 때 사용되는 스크립트 작업은 기존 클러스터에서 실행되는 스크립트 작업과 약간 다릅니다.

* 이 스크립트는 __자동으로 보존__됩니다.

* 스크립트가 __실패__하면 클러스터 만들기 프로세스가 실패할 수 있습니다.

다음 다이어그램에서는 만들기 프로세스 중 스크립트 작업을 실행할 때를 보여줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 만드는 동안의 단계][img-hdi-cluster-states]

HDInsight를 구성하는 동안 스크립트를 실행합니다. 이 단계에서 스크립트는 클러스터의 지정된 모든 노드에서 병렬 및 루트 권한으로 실행됩니다.

> [AZURE.NOTE] 스크립트가 클러스터 노드에서 루트 수준 권한으로 실행되므로 Hadoop 관련 서비스를 비롯한 서비스의 중지 및 시작과 같은 작업을 수행할 수 있습니다. 서비스를 중지하는 경우 스크립트 실행이 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다.

클러스터를 만드는 동안, 지정된 순서대로 호출되는 여러 스크립트 작업을 지정할 수 있습니다.

> [AZURE.IMPORTANT] 스크립트 작업은 60분 이내에 완료하지 않으면 시간이 초과됩니다. 클러스터 프로비전 중에는 스크립트가 다른 설정 및 구성 프로세스와 동시에 실행됩니다. CPU 시간 또는 네트워크 대역폭 등의 리소스에 대한 경합으로 인해 스크립트 실행이 개발 환경에서보다 더 오래 걸릴 수 있습니다.
>
> 스크립트 실행 시간을 최소화하려면 다운로드 및 소스에서의 응용 프로그램 컴파일 등과 같은 작업은 실행하지 않습니다. 대신 응용 프로그램을 사전에 컴파일하여 Azure Blob 저장소에 이진으로 저장하면 신속하게 클러스터에 다운로드할 수 있습니다.

###실행 중인 클러스터의 스크립트 작업

클러스터를 만드는 동안 사용되는 스크립트 작업과 달리, 이미 실행 중인 클러스터에서 실행되는 스크립트가 실패해도 클러스터가 실패 상태로 자동 변경되지 않습니다. 스크립트가 완료되면 클러스터가 "실행 중" 상태로 돌아갈 것입니다.

> [AZURE.IMPORTANT] 실행 중인 클러스터가 잘못된 작업을 수행하는 스크립트의 영향을 받지 않는다는 뜻이 아닙니다. 예를 들어 스크립트가 클러스터에 필요한 파일을 삭제하고, 구성을 변경하여 서비스가 실패할 수 있습니다.
>
> 스크립트 작업은 루트 권한으로 실행되므로 클러스터에 스크립트 작업을 적용하기 전에 스크립트가 어떤 일을 하는지 정확하게 이해해야 합니다.

클러스터에 스크립트를 적용하면 클러스터 상태가 __실행 중__에서 __수락됨__으로 바뀌었다가 다시 __HDInsight 구성__으로 바뀝니다. 그리고 스크립트가 성공하면 다시 __실행 중__으로 바뀝니다. 스크립트 상태는 스크립트 작업 내역에 기록되며, 이 내역을 사용하여 스크립트가 성공했는지 아니면 실패했는지 확인할 수 있습니다. 예를 들어 `Get-AzureRmHDInsightScriptActionHistory` PowerShell cmdlet을 사용하여 스크립트 상태를 볼 수 있습니다. 이 명령은 다음과 유사한 정보를 반환합니다.

    ScriptExecutionId : 635918532516474303
    StartTime         : 2/23/2016 7:40:55 PM
    EndTime           : 2/23/2016 7:41:05 PM
    Status            : Succeeded

> [AZURE.NOTE] 클러스터를 만든 후 클러스터 사용자(관리자) 암호를 변경한 경우 이 클러스터를 실행하는 스크립트 작업에 오류가 발생할 수 있습니다. 작업자 노드를 대상으로 하는 지속적인 스크립트 작업이 있는 경우 작업의 크기 조정을 통해 클러스터에 노드를 추가할 때 실패할 수 있습니다.

## 예제 스크립트 작업 스크립트

스크립트 작업 스크립트는 Azure 포털, Azure PowerShell, Azure CLI 또는 HDInsight.NET SDK에서 사용할 수 있습니다. HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 스크립트를 제공합니다.

이름 | 스크립트
----- | -----
**Azure Storage 계정 추가** | https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. [실행 중인 클러스터에 스크립트 작업 적용](#apply-a-script-action-to-a-running-cluster)을 참조하세요.
**Hue 설치** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)을 참조하세요.
**R 설치** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. [HDInsight 클러스터에서 R 설치 및 사용](hdinsight-hadoop-r-scripts-linux.md)을 참조하세요.
**Solr 설치** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)을 참조하세요.
**Giraph 설치** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)을 참조하세요.
| **Hive 라이브러리 사전 로드** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. [HDInsight 클러스터에서 Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md)를 참조하세요. |

## 클러스터를 만드는 동안 스크립트 작업 사용

이 섹션에서는 HDInsight 클러스터를 만들 때 스크립트 작업을 사용하는 다양한 방법, 다시 말해서 Azure 포털, Azure Resource Manager 템플릿, PowerShell cmdlet 및 .NET SDK를 사용하는 방법에 대해 설명합니다.

### 클러스터를 만드는 동안 Azure 포털에서 스크립트 작업 사용

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 클러스터를 만들기 시작합니다.

2. 아래와 같이, __선택적 구성__의 **스크립트 작업** 블레이드에 대해 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| 속성 | 값 |
	| -------- | ----- |
	| 이름 | 스크립트 작업의 이름을 지정합니다. |
	| 스크립트 URI | 클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다. |
	| 헤드/작업자 | 사용자 지정 스크립트가 실행되는 노드(**헤드**, **작업자** 또는 **ZooKeeper**)를 지정합니다. |
	| 매개 변수 | 스크립트에 필요한 경우 매개 변수를 지정합니다. |

	ENTER 키를 누르고 두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다.

3. **선택**을 클릭하여 구성을 저장하고 계속 클러스터를 만듭니다.

### Azure 리소스 관리자 템플릿에서 스크립트 작업 사용

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 HDInsight 클러스터를 만들며, 스크립트 작업을 사용하여 클러스터에 사용자 지정 구성 요소(이 예제에서는 R)를 설치합니다. 이 섹션에서는 스크립트 작업을 사용하여 클러스터를 만드는 샘플 템플릿을 제공합니다.

> [AZURE.NOTE] 이 섹션의 단계는 스크립트 작업을 사용하여 클러스터를 만드는 방법을 보여 줍니다. HDInsight 응용 프로그램을 사용하여 템플릿에서 클러스터를 만드는 예를 보려면 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

#### 시작하기 전에

* HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.
* 템플릿을 만드는 방법에 대한 지침은 [Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.
* 이전에 리소스 관리자에서 Azure PowerShell을 사용하지 않은 경우 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

#### 스크립트 작업을 사용하여 클러스터 만들기

1. 다음 템플릿을 컴퓨터의 위치에 복사합니다. 이 템플릿은 헤드 노드에 Giraph를 설치하며 클러스터의 작업자 노드도 설치합니다. 또한 JSON 템플릿이 유효한지 확인할 수 있습니다. 템플릿 콘텐츠를 [JSONLint](http://jsonlint.com/), 즉, 온라인 JSON 유효성 검사기 도구에 붙여 넣습니다.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installGiraph",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Azure PowerShell을 시작하고 Azure 계정에 로그인합니다. 자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] `Get-AzureRmSubscription`을 사용하여 계정과 관련한 모든 구독 목록을 가져올 수 있습니다. 여기에는 각 구독에 대한 구독 ID가 포함되어 있습니다.

5. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 새 리소스 그룹에 대한 요약이 반환됩니다.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. 리소스 그룹에 대한 새 배포를 만들려면 **New-AzureRmResourceGroupDeployment** 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름 및 만든 템플릿의 경로 또는 URL이 포함됩니다. 템플릿에 매개 변수가 필요한 경우 해당 매개 변수를 전달해야 합니다. 이 경우 클러스터에서 R을 설치하는 스크립트 작업은 매개 변수가 필요하지 않습니다.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	템플릿에 정의된 매개 변수에 대한 값을 제공하라는 메시지가 표시됩니다.

7. 리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. 배포에 실패할 경우 다음 cmdlet을 사용하여 오류에 대한 정보를 가져올 수 있습니다.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### 클러스터를 만드는 동안 Azure PowerShell에서 스크립트 작업 사용

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 콘솔을 열고 다음 정보를 사용하여 Azure 구독에 로그인한 후 일부 PowerShell 변수를 선언합니다.

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. 구성 값(예: 클러스터의 노드) 및 사용할 기본 저장소를 지정합니다.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. **Add-AzureRmHDInsightScriptAction** cmdlet을 사용하여 스크립트를 호출합니다. 다음 예제에서는 클러스터에 Giraph를 설치하는 스크립트를 사용합니다.

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install Giraph"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

	**Add-AzureRmHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	| 매개 변수 | 정의 |
	| --------- | ---------- |
	| 구성 | 스크립트 작업 정보가 추가되는 구성 개체입니다. |
	| 이름 | 스크립트 작업의 이름입니다. |
	| NodeType | 사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 **HeadNode**(헤드 노드에 설치),**WorkerNode**(모든 데이터 노드에 설치) 또는 **ZookeeperNode**(zookeeper 노드에 설치)입니다. |
	| 매개 변수 | 스크립트에 필요한 매개 변수입니다. |
	| Uri | 실행되는 스크립트의 URI를 지정합니다. |

4. 클러스터에 대한 관리자/HTTPS 사용자를 설정합니다.

        $httpCreds = get-credential

    메시지가 표시되면 이름으로 '관리'를 입력하고 암호를 제공합니다.

5. SSH 자격 증명을 설정합니다.

        $sshCreds = get-credential

    메시지가 표시되면 SSH 사용자 이름 및 암호를 입력합니다. 암호 대신 인증서를 사용하여 SSH 계정을 보호하려는 경우 빈 암호를 사용하고 `$sshPublicKey`를 사용하려는 인증서 공개 키의 내용으로 설정합니다. 예:

        $sshPublicKey = Get-Content .\path\to\public.key -Raw

4. 끝으로, 클러스터를 만듭니다.

        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux

    공개 키를 사용하여 SSH 계정을 보호하려는 경우 `-SshPublicKey $sshPublicKey`를 매개 변수로 지정해야 합니다.

클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

### 클러스터를 만드는 동안 HDInsight .NET SDK에서 스크립트 작업 사용

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 클라이언트 라이브러리를 제공합니다. 코드 샘플은 [.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)를 참조하세요.

## 실행 중인 클러스터에 스크립트 작업 적용

이 섹션에서는 실행 중인 HDInsight 클러스터에 스크립트 작업을 적용하는 다양한 방법, 다시 말해서 Azure 포털, PowerShell CMDlet, 크로스 플랫폼 Azure CLI 및 .NET SDK를 사용하는 방법에 대해 설명합니다. 이 섹션에 사용된 지속형 스크립트 작업은 실행 중인 클러스터에 기존 Azure Storage 계정을 추가합니다. 다른 스크립트 작업을 사용할 수도 있습니다. [예제 스크립트 작업 스크립트](#example-script-action-scripts)를 참조하세요.

### Azure 포털에서 실행 중인 클러스터에 스크립트 작업 적용

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터를 선택합니다.

2. HDInsight 클러스터 블레이드에서 __스크립트 작업__ 타일을 선택합니다.

    ![스크립트 작업 타일](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

    > [AZURE.NOTE] __모든 설정__을 선택한 다음 설정 블레이드에서 __스크립트 작업__을 선택할 수도 있습니다.

4. 스크립트 작업 블레이드 맨 위에서 __새로운 항목 제출__을 선택합니다.

    ![새로운 항목 제출 아이콘](./media/hdinsight-hadoop-customize-cluster-linux/newscriptaction.png)

5. 스크립트 작업 추가 블레이드에서 다음 정보를 입력합니다.

    * __이름__: 이 스크립트 작업에 사용할 이름입니다. 이 예제에서 해당 이름은 `Add Storage account`입니다.
    * __스크립트 URI__: 스크립트 URI입니다. 이 예제에서 해당 URI는 `https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`입니다.
    * __헤드__, __작업자__ 및 __Zookeeper__: 이 스크립트를 적용할 노드를 선택합니다. 이 예제에서는 헤드, 작업자 및 Zookeeper가 선택되었습니다.
    * __매개 변수__: 스크립트에서 매개 변수를 수락하면 여기에 해당 매개 변수를 입력합니다. 이 예제에서는 저장소 계정 이름 및 저장소 계정 키를 입력합니다.

		![실행 중인 클러스터에 대한 hdinsight 지속형 스크립트 작업 계정 저장소 계정](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-persisted-script-action-add-storage-account.png)

		스크린샷에서 `contosodata`는 기존 Azure Storage 계정이고, 두 번째 줄은 Storage 계정 키입니다.
    * __지속형__: 클러스터를 강화할 때 새 작업자 노드에 스크립트가 적용되도록 스크립트를 보존하려면 이 항목을 선택합니다.

6. 마지막으로, __만들기__ 단추를 사용하여 클러스터에 스크립트를 적용합니다.

### Azure PowerShell에서 실행 중인 클러스터에 스크립트 작업 적용

계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

1. Azure PowerShell 콘솔을 열고 다음 정보를 사용하여 Azure 구독에 로그인한 후 일부 PowerShell 변수를 선언합니다.

        # LOGIN TO ZURE
        Login-AzureRmAccount

		# PROVIDE VALUES FOR THESE VARIABLES
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
        $saName = "<ScriptActionName>"                  # Name of the script action
        $saURI = "<URI to the script>"                  # The URI where the script is located
        $nodeTypes = "headnode", "workernode"
        
    > [AZURE.NOTE] HDInsight Premium 클러스터를 사용하는 경우 nodetype의 `"edgenode"`를 사용하여 에지 노드에서 해당 스크립트를 실행할 수 있습니다.

2. 다음 명령을 사용하여 클러스터에 스크립트를 적용합니다.

        Submit-AzureRmHDInsightScriptAction -ClusterName $clusterName -Name $saName -Uri $saURI -NodeTypes $nodeTypes -PersistOnSuccess

    작업이 완료되면 다음과 유사한 정보가 제공됩니다.

        OperationState  : Succeeded
        ErrorMessage    :
        Name            : Giraph
        Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
        Parameters      :
        NodeTypes       : {HeadNode, WorkerNode}

### Azure CLI에서 실행 중인 클러스터에 스크립트 작업 적용

계속하기 전에 Azure CLI를 설치 및 구성했는지 확인하세요. 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요.

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

1. 셸 세션, 터미널, 명령 프롬프트 또는 시스템의 다른 명령줄을 열고 다음 명령을 사용하여 Azure Resource Manager 모드로 전환합니다.

        azure config mode arm

2. 다음 정보를 사용하여 Azure 구독에 인증합니다.

        azure login

3. 다음 명령을 사용하여 실행 중인 클러스터에 스크립트 작업을 적용합니다.

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    이 명령에 대한 매개 변수를 생략한 경우 해당 매개 변수를 묻는 메시지가 나타납니다. `-u`와 함께 지정한 스크립트에서 매개 변수를 허용하는 경우 `-p` 매개 변수를 지정할 수 있습니다.

    유효한 __nodetype__은 __headnode__, __workernode__ 및 __zookeeper__입니다. 스크립트를 여러 노드 형식에 적용해야 하는 경우 세미콜론(';')으로 구분하여 형식을 지정합니다. 예: `-n headnode;workernode`

    스크립트를 보존하려면 `--persistOnSuccess`를 추가합니다. 나중에 `azure hdinsight script-action persisted set`를 사용하여 스크립트를 유지할 수도 있습니다.
    
    작업이 완료되면 다음과 유사한 출력이 나타납니다.
    
        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### REST API를 사용하여 실행 중인 클러스터에 스크립트 작업 적용

[실행 중인 클러스터의 스크립트 작업](https://msdn.microsoft.com/library/azure/mt668441.aspx)을 참조하세요.
### HDInsight .NET SDK에서 실행 중인 클러스터에 스크립트 작업 적용

.NET SDK를 사용하여 클러스터에 스크립트를 적용하는 예제는 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)에서 확인할 수 있습니다.

## 기록 보기, 스크립트 작업 승격 및 강등

### Azure 포털 사용

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터를 선택합니다.

2. HDInsight 클러스터 블레이드에서 __설정__을 선택합니다.

    ![설정 아이콘](./media/hdinsight-hadoop-customize-cluster-linux/settingsicon.png)

3. 설정 블레이드에서 __스크립트 작업__을 선택합니다.

    ![스크립트 작업 링크](./media/hdinsight-hadoop-customize-cluster-linux/settings.png)

4. 스크립트 작업 블레이드에 클러스터에 적용된 스크립트 기록뿐만 아니라 지속형 스크립트의 목록이 표시됩니다. 아래 스크린샷을 보시면 Solr 스크립트가 이 클러스터에서 실행되었지만 스크립트 작업이 보존되지 않는 것을 확인할 수 있습니다.

    ![스크립트 작업 블레이드](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionhistory.png)

5. 기록에서 스크립트를 선택하면 이 스크립트의 속성 블레이드가 표시됩니다. 블레이드 맨 위에서 스크립트를 다시 실행하거나 승격할 수 있습니다.

    ![스크립트 작업 속성 블레이드](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionproperties.png)

6. 또한 스크립트 작업 블레이드에서 항목 오른쪽에 있는 __...__를 사용하여 다시 실행, 보존 또는 삭제(보존된 작업의 경우) 등의 작업을 수행할 수 있습니다.

    ![스크립트 작업 ... 사용량](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### Azure PowerShell 사용

| 사용하는 명령 | 수행하는 동작 |
| ----- | ----- |
| Get-AzureRmHDInsightPersistedScriptAction | 지속형 스크립트 작업에 대한 정보 검색 |
| Get-AzureRmHDInsightScriptActionHistory | 클러스터에 적용된 스크립트 작업의 기록 또는 특정 스크립트에 대한 세부 정보 검색 |
| Set-AzureRmHDInsightPersistedScriptAction | 임시 스크립트 작업을 지속형 스크립트 작업으로 승격 |
| Remove-AzureRmHDInsightPersistedScriptAction | 지속형 스크립트 작업을 임시 작업으로 강등 |

> [AZURE.IMPORTANT] `Remove-AzureRmHDInsightPersistedScriptAction` 명령을 사용해도 스크립트가 수행한 작업이 취소되지 않습니다. 이 명령은 클러스터에 추가된 새 작업자 노드에서 스크립트가 실행되지 않도록 지속형 플래그만 제거합니다.

다음 예제 스크립트는 cmdlet을 사용하여 스크립트를 승격한 후 다시 강등하는 방법을 보여줍니다.

    # Get a history of scripts
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster

    # From the list, we want to get information on a specific script
    Get-AzureRmHDInsightScriptActionHistory -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Promote this to a persisted script
    # Note: the script must have a unique name to be promoted
    # if the name is not unique, you will receive an error
    Set-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -ScriptExecutionId 635920937765978529

    # Demote the script back to ad hoc
    # Note that demotion uses the unique script name instead of
    # execution ID.
    Remove-AzureRmHDInsightPersistedScriptAction -ClusterName mycluster -Name "Install Giraph"

### Azure CLI 사용

| 사용하는 명령 | 수행하는 동작 |
| ----- | ----- |
| `azure hdinsight script-action persisted list <clustername>` | 지속형 스크립트 작업의 목록을 검색합니다. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` | 특정 지속형 스크립트 작업에 대한 정보를 검색합니다. |
| `azure hdinsight script-action history list <clustername>` | 클러스터에 적용된 스크립트 작업의 기록을 검색합니다. |
| `azure hdinsight script-action history show <clustername> <scriptname>` | 특정 스크립트 작업에 대한 정보 검색 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` | 임시 스크립트 작업을 지속형 스크립트 작업으로 승격 |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` | 지속형 스크립트 작업을 임시 작업으로 강등 |

> [AZURE.IMPORTANT] `azure hdinsight script-action persisted delete` 명령을 사용해도 스크립트가 수행한 작업이 취소되지 않습니다. 이 명령은 클러스터에 추가된 새 작업자 노드에서 스크립트가 실행되지 않도록 지속형 플래그만 제거합니다.

### HDInsight .NET SDK 사용

.NET SDK를 사용하여 클러스터에서 스크립트 기록을 검색하거나 스크립트를 승격 또는 강등하는 예제는 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)에서 확인할 수 있습니다.

> [AZURE.NOTE] 이 예제에서는 .NET SDK를 사용하여 HDInsight 응용 프로그램을 설치하는 방법도 보여 줍니다.

## 문제 해결

Ambari 웹 UI를 사용하여 스크립트 작업에서 기록한 정보를 볼 수 있습니다. 스크립트가 클러스터 만들기에 사용되었으며 스크립트의 오류로 인해 클러스터 생성에 실패한 경우, 클러스터와 연결된 기본 저장소 계정에서도 로그를 사용할 수 있습니다. 이 섹션에서는 두 옵션을 모두 사용하여 로그를 검색하는 방법에 대한 정보를 제공합니다.

### Ambari 웹 UI 사용

1. 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.

	메시지가 표시되면 클러스터의 관리자 계정 이름(관리자) 및 암호를 입력합니다. 웹 폼에서 관리자 자격 증명을 다시 입력해야 합니다.

2. 페이지 맨 위에 있는 모음에서 __작업__ 항목을 선택합니다. Ambari 통해 클러스터에서 수행된 현재 및 이전 작업의 목록이 표시됩니다.

	![선택한 작업으로 Ambari 웹 UI 모음](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. __작업__ 열에서 __run\_customscriptaction__이 있는 항목을 찾습니다. 스크립트 작업을 실행할 때 생성됩니다.

	![작업의 스크린샷](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	이 항목을 선택하고 링크를 통해 드릴다운하여 스크립트가 클러스터에서 실행되었을 때 생성된 STDOUT 및 STDERR 출력을 봅니다.

### 기본 저장소 계정에서 로그 액세스

스크립트 작업의 오류로 클러스터 생성에 실패할 경우, 클러스터와 연결된 기본 저장소 계정에서 직접 스크립트 작업 로그에 액세스할 수 있습니다.

* 저장소 로그는 `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`에서 제공합니다.

	![작업의 스크린샷](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	여기에서 로그가 헤드 노드, 작업자 노드, Zookeeper 노드에 대해 별도로 구성됩니다. 일부 사례:
	* **헤드 노드** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
	* **작업자 노드** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
	* **Zookeeper 노드** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 해당 호스트의 모든 stdout 및 stderr은 저장소 계정에 업로드됩니다. 각 스크립트 작업마다 하나의 **output-*.txt** 및 **errors-*.txt**가 있습니다. output-*.txt 파일은 호스트에서 실행되는 스크립트의 URI 정보를 포함합니다. 예를 들면 다음과 같습니다.

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 같은 이름으로 반복해서 스크립트 작업 클러스터를 만들 수 있습니다. 이 경우에는 날짜 폴더 이름을 기준으로 관련 로그를 구분할 수 있습니다. 예를 들어, 서로 다른 날짜에 만든 클러스터(mycluster)의 폴더 구조는 다음과 같습니다.
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 같은 날 같은 이름으로 스크립트 작업 클러스터를 만든 경우 고유의 접두사를 사용하여 관련 로그 파일을 식별할 수 있습니다.

* 일과 끝에 클러스터를 만든 경우 로그 파일이 이틀에 걸쳐 있을 수 있습니다. 이 경우 같은 클러스터에 날짜가 다른 두 폴더가 있게 됩니다.

* 기본 컨테이너에 로그 파일을 업로드하는 작업은 특히 대형 클러스터의 경우 최대 5분이 소요됩니다. 따라서 로그에 액세스하려면 스크립트 작업이 실패할 경우 클러스터를 즉시 삭제해서는 안 됩니다.


## HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Hadoop에 형성된 오픈 소스 기술의 에코시스템을 사용하여 클라우드에 빅 데이터 응용 프로그램을 빌드할 수 있는 유연한 플랫폼입니다. Microsoft Azure에서는 [Azure 지원 FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션에 설명된 대로 일반적인 수준의 오픈 소스 기술을 제공합니다. HDInsight 서비스는 아래에 설명된 일부 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

- **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어, YARN ResourceManager, Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)에 있습니다.

- **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

> [AZURE.WARNING] HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 설치되고 사용되는 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음은 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법의 목록입니다.

1. 작업 제출 - 사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 기타 유형의 작업을 클러스터에 제출할 수 있습니다.

2. 클러스터 사용자 지정 - 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.

3. 샘플 - 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.

##문제 해결

###클러스터를 만드는 동안 기록에 스크립트가 표시되지 않음

2016년 3월 15일 전에 클러스터를 만든 경우에는 클러스터를 만드는 동안 사용된 모든 스크립트의 스크립트 작업 기록에 항목이 나타나지 않을 수 있습니다. 그러나 2016년 3월 15일 이후에 클러스터의 크기를 조정하면 클러스터를 만들 때 사용되는 스크립트가 기록에 나타납니다. 크기 조정 작업의 일부로 클러스터의 새 노드에 적용되기 때문입니다.

두 가지 예외 사항이 있습니다.

* 클러스터가 2015년 9월 1일 전에 생성되었습니다. 스크립트 작업이 이 날에 도입되었습니다. 따라서 이 날짜 이전에 생성된 클러스터는 클러스터 생성에 스크립트 작업이 사용되지 않았습니다.

* 클러스터를 만들 때 여러 스크립트 작업을 사용하고 여러 스크립트에 같은 이름을 사용했거나 여러 스크립트의 이름과 URI는 같지만 매개 변수는 서로 다르게 했습니다. 이 경우에는 다음 오류가 표시됩니다.

    기존 스크립트에 충돌하는 스크립트 이름이 있어서 이 클러스터에서 새 스크립트 작업을 실행할 수 없습니다. 클러스터 만들기에 제공되는 스크립트 이름이 모두 고유해야 합니다. 기존 스크립트는 여전히 크기를 조정할 때 실행됩니다.

## 다음 단계

클러스터를 사용자 지정하는 스크립트를 만들고 사용하는 데 대한 정보 및 예제는 다음을 참조하세요.

- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "클러스터를 만드는 동안의 단계"

<!---HONumber=AcomDC_0914_2016-->