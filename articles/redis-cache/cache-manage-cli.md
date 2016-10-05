<properties 
	pageTitle="Azure CLI(Azure 명령줄 인터페이스)를 사용하여 Azure Redis Cache를 만들고 관리하는 방법 | Microsoft Azure" 
	description="모든 플랫폼에서 Azure CLI를 설치하고, Azure CLI를 사용하여 Azure 계정에 연결하고, Azure CLI에서 Redis cache를 만들고 관리하는 방법에 대해 알아봅니다." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>

# Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Azure Redis Cache를 만들고 관리하는 방법

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Azure CLI를 사용하면 어떤 플랫폼에서나 Azure 인프라를 효율적으로 관리할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 Azure Redis Cache 인스턴스를 만들고 관리하는 방법을 보여 줍니다.

## 필수 조건

Azure CLI를 사용하여 Azure Redis Cache 인스턴스를 만들고 관리하려면 다음 단계를 완료해야 합니다.

-	Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.
-	[Azure CLI를 설치합니다](../xplat-cli-install.md).
-	Azure CLI 설치를 개인 Azure 계정이나 회사 또는 학교 Azure 계정에 연결하고 `azure login` 명령을 사용하여 Azure CLI에서 로그인합니다. 차이점을 이해하고 선택하려면 [Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독 연결](../xplat-cli-connect.md)을 참조하세요.
-	다음 명령 중 하나를 실행하기 전에 `azure config mode arm` 명령을 실행하여 Azure CLI를 리소스 관리자 모드로 전환합니다. 자세한 내용은 [Azure Resource Manager 모드 설정](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode)을 참조하세요.

## Redis Cache 속성

Redis Cache 인스턴스를 만들고 업데이트하는 경우에 다음 속성이 사용됩니다.

| 속성 | Switch | 설명 |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Redis Cache의 이름입니다. |
| 리소스 그룹 | -g, --resource-group | 리소스 그룹의 이름입니다. |
| location | -l, --location | 캐시를 만드는 위치입니다. |
| size | -z, --size | Redis Cache의 크기입니다. 유효한 값: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku | -x, --sku | Redis SKU입니다. 다음 중 하나여야 합니다. [기본, 표준, 프리미엄] |
| EnableNonSslPort | -e, --enable-non-ssl-port | Redis Cache의 EnableNonSslPort 속성입니다. 캐시에 대해 비 SSL 포트를 사용하도록 설정하려는 경우 이 플래그를 추가합니다. |
| Redis 구성 | -c, --redis-configuration | Redis 구성입니다. 구성 키 및 값의 JSON 형식 문자열을 여기에 입력합니다. 형식:"{"":"","":""}" |
| Redis 구성 | -f, --redis-configuration-file | Redis 구성입니다. 구성 키 및 값이 있는 파일의 경로를 여기에 입력합니다. 파일 항목에 대한 형식: {"":"","":""} |
| 분할된 데이터베이스 수 | -r, --shard-count | 클러스터링을 사용하는 프리미엄 클러스터 캐시에서 만드는 분할된 데이터베이스 수입니다. |
| 가상 네트워크 | -v, --virtual-network | VNET에서 캐시를 호스트하는 경우 Redis Cache를 배포하는 가상 네트워크의 정확한 ARM 리소스 ID를 지정합니다. 형식 예: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 키 유형 | -t, --key-type | 갱신하는 키의 유형입니다. 유효한 값: [주, 보조] |
| StaticIP | -p, --static-ip <static-ip> | VNET에서 캐시를 호스팅하는 경우 서브넷에서 캐시에 대한 고유 IP 주소를 지정합니다. 제공되지 않으면 하나의 IP 주소가 서브넷에서 자동으로 선택됩니다. |
| 서브넷 | t, --subnet <subnet> | VNET에서 캐시를 호스팅하는 경우에 캐시를 배포할 서브넷의 이름을 지정합니다. |
| VirtualNetwork | -v, --virtual-network <virtual-network> | VNET에서 캐시를 호스트하는 경우 Redis Cache를 배포하는 가상 네트워크의 정확한 ARM 리소스 ID를 지정합니다. 형식 예: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 구독 | -s, --subscription | 구독 식별자입니다. |

## 모든 Redis Cache 명령 참조

모든 Redis Cache 명령 및 매개 변수를 보려면 `azure rediscache -h` 명령을 사용합니다.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Redis Cache 만들기

Redis Cache를 만들려면 다음 명령을 사용합니다.

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

이 명령에 대한 자세한 내용은 `azure rediscache create -h` 명령을 실행합니다.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                               output usage information
	help:      -v, --verbose                                            use verbose output
	help:      -vv                                                      more verbose with debug output
	help:      --json                                                   use json output
	help:      -n, --name <name>                                        Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>                    Name of the Resource Group
	help:      -l, --location <location>                                Location to create cache.
	help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
	help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
	help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
	help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
	help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
	help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
	help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
	help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
	help:      -s, --subscription <id>                                  the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## 기존 Redis Cache 삭제

Redis Cache를 삭제하려면 다음 명령을 사용합니다.

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

이 명령에 대한 자세한 내용은 `azure rediscache delete -h` 명령을 실행합니다.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## 구독 또는 리소스 그룹 내 모든 Redis Cache 나열

구독 또는 리소스 그룹 내 모든 Redis Cache를 나열하려면 다음 명령을 사용합니다.

	azure rediscache list [options]

이 명령에 대한 자세한 내용은 `azure rediscache list -h` 명령을 실행합니다.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
	help:
	help:    Usage: rediscache list [options]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## 기존 Redis Cache의 속성 표시

기존 Redis Cache의 속성을 표시하려면 다음 명령을 사용합니다.

	azure rediscache show [--name <name> --resource-group <resource-group>]

이 명령에 대한 자세한 내용은 `azure rediscache show -h` 명령을 실행합니다.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## 기존 Redis Cache의 설정 변경

기존 Redis Cache의 설정을 변경하려면 다음 명령을 사용합니다.

	azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

이 명령에 대한 자세한 내용은 `azure rediscache set -h` 명령을 실행합니다.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
	help:
	help:    Options:
	help:      -h, --help                                               output usage information
	help:      -v, --verbose                                            use verbose output
	help:      -vv                                                      more verbose with debug output
	help:      --json                                                   use json output
	help:      -n, --name <name>                                        Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>                    Name of the Resource Group
	help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
	help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
	help:      -s, --subscription <subscription>                        the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## 기존 Redis Cache에 대한 인증 키 갱신

기존 Redis Cache에 대한 인증 키를 갱신하려면 다음 명령을 사용합니다.

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

`key-type`에 대해 `Primary` 또는 `Secondary`를 지정합니다.

이 명령에 대한 자세한 내용은 `azure rediscache renew-key -h` 명령을 실행합니다.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## 기존 Redis Cache의 주 및 보조 키 나열

기존 Redis Cache의 주 및 보조 키를 나열하려면 다음 명령을 사용합니다.

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

이 명령에 대한 자세한 내용은 `azure rediscache list-keys -h` 명령을 실행합니다.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=AcomDC_0921_2016-->