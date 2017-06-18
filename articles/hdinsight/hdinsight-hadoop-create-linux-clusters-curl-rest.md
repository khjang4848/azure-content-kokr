<properties
   	pageTitle="cURL 및 Azure REST API를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 만들기 | Microsoft Azure"
   	description="cURL, Azure 리소스 관리자 템플릿 및 Azure REST API를 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법을 알아봅니다. 클러스터 유형(Hadoop, HBase 또는 Storm)을 지정하거나 스크립트를 사용하여 사용자 지정 구성 요소를 설치할 수 있습니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/27/2016"
   	ms.author="larryfr"/>

#cURL 및 Azure REST API를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST API를 사용하면 Linux 기반 HDInsight 클러스터 등과 같은 새 리소스 생성을 포함하여 Azure 플랫폼에서 호스팅되는 관리 작업을 수행할 수 있습니다. 이 문서에서는 HDInsight 클러스터 및 관련 저장소를 구성하는 Azure 리소스 관리자 템플릿을 만들고 cURL을 사용하여 템플릿을 Azure REST API에 배치하여 새 HDInsight 클러스터를 만듭니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 HDInsight 클러스터에 대해 작업자 노드 (4)의 기본 갯수를 사용합니다. 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

##필수 조건

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __Azure CLI__ Azure CLI는 서비스 주체를 만드는 데 사용됩니다. 서비스 주체는 Azure REST API에 대한 요청의 인증 토큰을 생성하는 데 사용됩니다.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. 이 유틸리티는 패키지 관리 시스템을 통해서나 [http://curl.haxx.se/](http://curl.haxx.se/)에서 다운로드하여 사용할 수 있습니다.

    > [AZURE.NOTE] PowerShell을 사용하여 이 문서의 명령을 실행하는 경우 먼저 기본적으로 생성한 `curl` 별칭을 제거합니다. 이 별칭은 PowerShell 프롬프트에서 `curl` 명령을 사용할 때 cURL 대신 PowerShell cmdlet인 Invoke-WebRequest을 사용하며 이 문서에서 사용되는 많은 명령에 대해 오류를 반환합니다.
    > 
    > 이 별칭을 제거하려면 PowerShell 프롬프트에서 다음을 사용합니다.
    >
    > `Remove-item alias:curl`
    >
    > 별칭을 제거한 후에는 시스템에 설치한 cURL 버전을 사용할 수 있어야 합니다.

##템플릿 만들기

Azure 리소스 관리 템플릿은 __리소스 그룹__과 그 안의 모든 리소스를 설명하는 JSON 문서입니다(예: HDInsight). 이 템플릿 기반 접근 방식을 사용하면 HDInsight에 필요한 모든 리소스를 하나의 템플릿에 정의하고 그룹에 변경 내용을 적용하는 __배포__를 통해 그룹에 대한 변경 내용을 전체적으로 관리할 수 있습니다.

템플릿은 보통 템플릿 그 자체와, 구성에 특정한 값으로 사용자가 입력한 매개 변수 파일 등의 두 부분으로 구성됩니다. 예를 들어 클러스터 이름, 관리자 이름, 암호가 있습니다. REST API를 직접 사용할 때는 이 두 항목을 한 파일로 결합해야 합니다. 이 JSON 문서의 형식은 다음과 같습니다.

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

예를 들어, 다음은 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)의 템플릿과 매개 변수 파일의 병합기로, 암호를 사용하여 SSH 사용자 계정을 보호하는 Linux 기반 클러스터를 만듭니다.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
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
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
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
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

이 예는 이 문서의 단계에서 사용됩니다. 문서 마지막의 __매개 변수__ 섹션에서 자리표시자 _values_의 값을, 클러스터에 사용할 값으로 교체해야 합니다.

##Azure 구독에 로그인

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md)에서 설명된 단계를 따라 `azure login` 명령을 사용하여 구독에 연결합니다.

##서비스 주체 만들기

> [AZURE.NOTE] 이러한 단계는 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) 문서의 _암호를 사용하여 서비스 주체 인증 - Azure CLI_ 섹션에서 제공된 정보의 요약된 버전입니다. 이러한 단계는 HDInsight 클러스터와 같은 Azure 리소스를 만드는 데 사용된 REST API 요청을 인증하는 데 사용할 수 있는 새 서비스 주체를 만듭니다.

1. 명령 프롬프트, 터미널 세션 또는 셸에서 다음 명령을 사용하여 Azure 구독을 나열합니다.

        azure account list
        
    목록에서 사용하려는 구독을 선택하고 __Id__ 열을 확인합니다. 이 ID는 __구독 ID__이며 이 문서의 대부분의 단계에서 사용됩니다.

2. Azure Active Directory에서 새 응용 프로그램을 만듭니다.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    `--name`, `--home-page` 및 `--identifier-uris`에 대한 값을 고유한 값으로 대체합니다. 새 Active Directory 항목에 대한 암호를 제공합니다.
    
    > [AZURE.NOTE] 서비스 주체를 통해 인증에 대해 이 응용 프로그램을 만들었으므로 `--home-page` 및 `--identifier-uris` 값은 인터넷에서 호스트되는 실제 웹 페이지를 참조할 필요가 없습니다. 고유한 URI여야만 합니다.
    
    반환되는 데이터에서 __AppId__ 값을 저장합니다.
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. 이전에 반환된 __AppId__ 값을 사용하여 서비스 주체를 만듭니다.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     반환되는 데이터에서 __개체 ID__ 값을 저장합니다.
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. 이전에 반환된 __개체 ID__ 값을 사용하여 __소유자__ 역할을 서비스 주체에 할당합니다. 또한 이전에 받은 __구독 ID__도 사용해야 합니다.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    이 명령이 완료되면 이제 서비스 주체는 지정된 구독 ID에 대한 소유자 액세스를 가집니다.

##인증 토큰 가져오기

1. 다음을 사용하여 구독에 대한 __테넌트 ID__를 찾습니다.

        azure account show -s <subscription ID>
        
    반환되는 데이터에서 __테넌트 ID__를 찾습니다.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Azure REST API를 사용하여 새 토큰을 생성합니다.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    __TenantID__, __AppID__ 및 __암호__를 이전에 받거나 사용한 값으로 대체합니다.

    이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 JSON 문서가 포함되어 있습니다.

    이 요청에서 반환한 JSON 문서는 이름이 __access\_token__인 요소를 포함하며, 이 요소의 값은 이 문서의 다음 섹션에서 사용되는 요청을 인증하는 데 필요한 액세스 토큰입니다.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##리소스 그룹 만들기

다음을 사용하여 새 리소스 그룹을 만듭니다. HDInsight 클러스터와 같은 리소스를 만들려면 먼저 그룹을 만들어야 합니다.

* __SubscriptionID__를 서비스 주체를 만들 때 받은 구독 ID로 바꿉니다.
* __AccessToken__을 이전 단계에서 받은 액세스 토큰으로 바꿉니다.
* __DataCenterLocation__을 리소스 그룹과 리소스를 만들려는 데이터 센터로 바꿉니다. 예를 들어 "미국 중남부"입니다.
* __ResourceGroupName__을 이 그룹에 사용하려는 이름으로 바꿉니다.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 그룹 정보가 담긴 JSON 문서가 포함되어 있습니다. `"provisioningState"` 요소는 `"Succeeded"` 값을 포함합니다.

##배포 만들기

다음을 사용하여 리소스 그룹에 클러스터 구성(템플릿 및 매개 변수 값)을 배포합니다.

* __SubscriptionID__ 및 __AccessToken__을 이전에 사용한 값으로 바꿉니다.
* __ResourceGroupName__을 이전 섹션에서 만든 리소스 그룹 이름으로 바꿉니다.
* __DeploymentName__을 이 배포에 사용하려는 이름으로 바꿉니다.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] 템플릿과 매개 변수가 담긴 JSON 문서를 파일로 저장한 경우 `-d "{ template and parameters}"` 대신 다음을 사용할 수 있습니다.
>
> `--data-binary "@/path/to/file.json"`

이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 배포 작업 정보가 담긴 JSON 문서가 포함되어 있습니다.

> [AZURE.IMPORTANT] 이 시점에서는 배포가 제출되었으나 완료된 것은 아닙니다. 배포가 완료되려면 보통 몇 분(보통 15분 전후)이 소요됩니다.

##배포 상태 확인

배포 상태를 확인하려면 다음을 사용합니다.

* __SubscriptionID__ 및 __AccessToken__을 이전에 사용한 값으로 바꿉니다.
* __ResourceGroupName__을 이전 섹션에서 만든 리소스 그룹 이름으로 바꿉니다.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

그러면 배포 작업 관련 정보가 담긴 JSON 문서가 반환됩니다. `"provisioningState"` 요소에는 배포 상태가 포함되어 있습니다. `"Succeeded"` 값이 포함되었으면 배포가 성공적으로 완료된 것입니다. 이 시점에서 클러스터를 사용할 수 있어야 합니다.

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux.md)

###Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0914_2016-->