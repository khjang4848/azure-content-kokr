<properties
   pageTitle="Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure Resource Manager 템플릿을 사용하여 Azure HDInsight에 클러스터를 만드는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

ARM(Azure 리소스 관리자) 템플릿을 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다. 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](../resource-group-template-deploy.md)를 참조하세요. 다른 클러스터 만들기 도구 및 기능은 이 페이지 맨 위에 있는 탭 선택을 클릭하거나 [클러스터 생성 방법](hdinsight-provision-clusters.md#cluster-creation-methods)을 참조하세요.

##필수 조건:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- [Azure 구독](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell 및/또는 Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## 리소스 관리자 템플릿

Resource Manager 템플릿을 사용하면 단일 조정된 작업의 응용 프로그램에 HDInsight 클러스터, 해당 종속 리소스(예: 기본 저장소 계정) 및 기타 리소스(예: Apache Sqoop을 사용하는 Azure SQL 데이터베이스)를 쉽게 만들 수 있습니다. 템플릿에서 응용 프로그램에 필요한 리소스를 정의하고 다양한 환경에 따라 값을 입력하는 배포 매개 변수를 지정합니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다.

HDInsight 클러스터 및 종속 Azure Storage 계정을 만들기 위한 Resource Manager 템플릿은 [부록 A](#appx-a-arm-template)에서 찾을 수 있습니다. 플랫폼 간 [VSCode](https://code.visualstudio.com/#alt-downloads)를 [Resource Manager 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) 또는 텍스트 편집기에서 사용하여 템플릿을 워크스테이션의 파일에 저장합니다. 다양한 방법을 사용하여 템플릿을 호출하는 방법을 알아봅니다.

Resource Manager 템플릿에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)
- [Azure 리소스 관리자 템플릿으로 응용 프로그램 배포](../resource-group-template-deploy.md)

특정 요소에 대한 JSON 스키마를 찾으려면 다음 절차를 따를 수 있습니다.

1. [Azure 포털](https://porta.azure.com)을 열고 HDInsight 클러스터를 만듭니다. [Azure 포털을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.
2. JSON 스키마에 필요한 요소를 구성합니다.
3. **만들기**를 클릭하기 전에 다음 스크린샷과 같이 **자동화 옵션**을 클릭합니다.

    ![HDInsight Hadoop의 클러스터 Resource Manager 템플릿 스키마 자동화 옵션 만들기](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)

    포털은 구성을 기반으로 Resource Manager 템플릿을 만듭니다.
## PowerShell을 사용하여 배포

다음 절차는 Linux 기반 HDInsight 클러스터를 만듭니다.

**Resource Manager 템플릿을 사용하여 클러스터를 배포하려면**

1. [부록 A](#appx-a-arm-template)의 json 파일을 워크스테이션에 저장합니다. PowerShell 스크립트에서 파일 이름은 *C:\\HDITutorials-ARM\\hdinsight-arm-template.json*입니다.
2. 필요한 경우 매개 변수 및 변수를 설정합니다.
3. 다음 PowerShell 스크립트를 사용하여 템플릿을 실행합니다.

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	PowerShell 스크립트는 클러스터 이름만 구성합니다. 저장소 계정 이름이 템플릿에 하드코드되어 있습니다. 클러스터 사용자 암호(기본 사용자 이름은 *admin*) 및 SSH 사용자 암호(기본 SSH 사용자 이름은 *sshuser*)를 입력하라는 메시지가 표시됩니다.
	
자세한 내용은 [PowerShell로 배포](../resource-group-template-deploy.md#deploy-with-powershell)를 참조하세요.

## Azure CLI를 사용하여 배포

다음 샘플에서는 Resource Manager 템플릿을 호출하여 클러스터 및 해당 종속 저장소 계정과 컨테이너를 만듭니다.

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
클러스터 이름, 클러스터 사용자 암호(기본 사용자 이름은 *admin*) 및 SSH 사용자 암호(기본 SSH 사용자 이름은 *sshuser*)를 입력하라는 메시지가 표시됩니다. 인라인 매개 변수를 제공하려면

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

## REST API를 사용하여 배포

[REST API를 사용하여 배포](../resource-group-template-deploy.md#deploy-with-the-rest-api)를 참조하세요.

## Visual Studio를 사용하여 배포

Visual Studio를 사용하여 리소스 그룹 프로젝트를 만들고 사용자 인터페이스를 통해 Azure에 배포할 수 있습니다. 프로젝트에 포함할 리소스 유형을 선택하면 해당 리소스가 리소스 관리자 템플릿에 자동으로 추가됩니다. 또한 프로젝트에서는 템플릿을 배포할 수 있는 PowerShell 스크립트를 제공합니다.

Visual Studio를 리소스 그룹과 함께 사용하는 방법에 대한 소개는 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

##다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../virtual-machines/virtual-machines-windows-csharp-template.md)를 참조하세요.
- 응용 프로그램 배포에 대한 자세한 예제는 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](../app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](../solution-dev-test-environments.md)을 참조하세요.
- Azure Resource Manager 템플릿 섹션에 대한 자세한 내용은 [템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.
- Azure Resource Manager 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](../resource-group-template-functions.md)를 참조하세요.

##부록 A: Resource Manager 템플릿

다음 Azure 리소스 관리자 템플릿은 종속 Azure 저장소 계정을 사용하여 Linux 기반 Hadoop 클러스터를 만듭니다.

> [AZURE.NOTE] 샘플에는 Hive metastore와 Oozie metastore에 대한 구성 정보가 포함됩니다. 템플릿을 사용하기 전에 섹션을 제거하거나 섹션을 구성합니다.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

<!---HONumber=AcomDC_0914_2016-->