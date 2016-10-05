<properties
   pageTitle="서비스 패브릭 역방향 프록시 | Microsoft Azure"
   description="서비스 패브릭의 역방향 프록시를 사용하여 클러스터 내부 및 외부에서 마이크로 서비스와 통신"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman,vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="vturecek"/>

# 서비스 패브릭 역방향 프록시

서비스 패브릭 역방향 프록시는 서비스 패브릭 클러스터에서 HTTP 끝점을 표시하는 마이크로 서비스를 주소 지정할 수 있도록 서비스 패브릭에 기본 제공되는 역방향 프록시입니다.

## 마이크로 서비스 통신 모델

서비스 패브릭의 마이크로 서비스는 일반적으로 VM의 일부를 클러스터에서 실행하고 여러 가지 이유로 한 VM을 다른 VM으로 이동할 수 있습니다. 따라서 마이크로 서비스에 대한 끝점은 동적으로 변할 수 있습니다. 마이크로 서비스와 통신하는 일반적인 패턴은 아래의 확인 루프입니다.

1. 처음에는 이름 지정 서비스를 통해 서비스 위치를 확인합니다.
2. 서비스에 연결합니다.
3. 연결 실패의 원인을 결정하고 필요한 경우 서비스 위치를 다시 확인합니다.

이 프로세스는 일반적으로 서비스 확인 및 재시도 정책을 구현하는 재시도 루프에 클라이언트 쪽 통신 라이브러리의 매핑을 포함합니다. 이 토픽에 대한 자세한 내용은 [서비스와 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요.

### SF 역방향 프록시를 통해 통신
서비스 패브릭 역방향 프록시는 클러스터의 모든 노드에서 실행됩니다. 클라이언트를 대신하여 전체 서비스 확인 프로세스를 수행한 다음 클라이언트 요청을 전달합니다. 따라서 클러스터에서 실행하는 클라이언트는 클라이언트 쪽 HTTP 통신 라이브러리를 사용하여 동일한 노드에서 로컬로 실행 중인 SF 역방향 프록시를 통해 대상 서비스와 대화합니다.

![내부 통신][1]

## 클러스터 외부에서 마이크로 서비스에 연결
마이크로 서비스에 대한 기본 외부 통신 모델은 **옵트인**이며, 여기서 각 서비스는 기본적으로 외부 클라이언트에서 직접 액세스될 수 없습니다. [Azure Load Balancer](../load-balancer/load-balancer-overview.md)는 마이크로 서비스와 외부 클라이언트 간의 네트워크 경계이며, 네트워크 주소 변환을 수행하고 외부 요청을 내부 **ip: port** 끝점에 전달합니다. 마이크로 서비스의 끝점에서 외부 클라이언트에 직접 액세스할 수 있도록 하려면 먼저 Azure Load Balancer를 클러스터의 서비스가 사용하는 각 포트에 트래픽을 전달하도록 구성해야 합니다. 또한 대부분의 마이크로 서비스(특히 상태 저장 마이크로 서비스)는 클러스터의 모든 노드에 사용되지 않고 장애 조치 시에 노드 간에 이동할 수 있습니다. 따라서 그러한 경우 Azure Load Balancer는 복제의 대상 노드가 트래픽을 전달할 위치에 있는지 효과적으로 결정할 수 없습니다.

### 클러스터 외부에서 SF 역방향 프록시를 통해 마이크로 서비스에 연결

Azure 부하 분산 장치에서 개별 서비스의 포트를 구성 하는 대신 Azure Load Balancer에서 SF 역방향 프록시 포트를 구성할 수 있습니다. 이렇게 하면 클러스터 외부의 클라이언트가 추가 구성 없이 역방향 프록시를 통해 클러스터 내부의 서비스에 연결할 수 있습니다.

![외부 통신][0]

>[AZURE.WARNING] 부하 분산 장치에서 역방향 프록시의 포트를 구성하면 http 끝점을 표시하는 클러스터의 모든 마이크로 서비스를 클러스터 외부에서 주소 지정할 수 있습니다.


## 역방향 프록시를 통해 서비스를 주소 지정하기 위한 URI 형식

역방향 프록시는 특정 URI 형식을 사용하여 수신 요청을 전달해야 하는 서비스 파티션을 식별합니다.

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http(s):** 역방향 프록시를HTTP 또는 HTTPS 트래픽을 허용하도록 구성할 수 있습니다. HTTPS 트래픽의 경우 SSL 종료는 역방향 프록시에서 발생합니다. 클러스터에서 역방향 프록시에 의해 서비스에 전달되는 요청은 http 상에서 이루어집니다.
 - ** 클러스터 FQDN| internal IP:** For external clients, the reverse proxy can be configured so that it is reachable through the cluster domain (e.g., mycluster.eastus.cloudapp.azure.com). By default the reverse proxy runs on every node, so for internal traffic it can be reached on localhost or on any internal node IP (e.g., 10.0.0.1).
 - **포트:** 역방향 프록시에 대해 지정된 포트입니다. 예: 19008.
 - **ServiceInstanceName:** "fabric:/" 체계 없이 연결하려고 하는 서비스의 정규화된 배포된 서비스 인스턴스 이름입니다. 예를 들어 서비스 *fabric:/myapp/myservice/*에 연결하려면 *myapp/myservice*를 사용합니다.
 - **접미사 경로:** 연결할 서비스에 대한 실제 URL 경로입니다. 예, *myapi/values/add/3*
 - **PartitionKey:** 분할 서비스의 경우 연결할 파티션의 계산된 파티션 키입니다. 참고로 이는 파티션 ID GUID가 *아닙니다*. 이 매개 변수는 단일 파티션 체계를 사용하는 서비스에는 필요하지 않습니다.
 - **PartitionKind:** 서비스 파티션 체계입니다. 이는 'Int64Range' 또는 'Named'일 수 있습니다. 이 매개 변수는 단일 파티션 체계를 사용하는 서비스에는 필요하지 않습니다.
 - **Timeout:** 서비스에 대한 역방향 프록시가 클라이언트 요청을 대신하여 만든 http 요청에 대한 시간 제한을 지정합니다. 기본값은 60초입니다. 선택적 매개 변수입니다.

### 사용 예

예를 들어 다음 URL에서 HTTP 수신기를 여는 **fabric:/MyApp/MyService** 서비스를 살펴보겠습니다.

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

다음 리소스를 사용:

 - `/index.html`
 - `/api/users/<userId>`

서비스가 단일 분할 체계를 사용하는 경우 *PartitionKey* 및 *PartitionKind* 쿼리 문자열 매개 변수는 필요하지 않으며 다음과 같이 게이트웨이를 통해 서비스에 연결할 수 있습니다.

 - 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - 내부에서: `http://localhost:19008/MyApp/MyService`

서비스가 Uniform Int64 분할 체계를 사용하는 경우 *PartitionKey* 및 *PartitionKind* 쿼리 문자열 매개 변수를 사용하여 서비스의 파티션에 연결해야 합니다.

 - 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - 내부에서: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

서비스가 표시하는 리소스에 연결하려면 URL의 서비스 이름 뒤에 리소스 경로를 추가합니다.

 - 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - 내부에서: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

그러면 게이트웨이가 이 요청을 서비스의 URL에 전달합니다.

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## 포트 공유 서비스에 대한 특수 처리

응용 프로그램 게이트웨이는 서비스 주소의 다시 확인을 시도하고 서비스에 연결할 수 없는 경우 요청을 재시도합니다. 이는 클라이언트 코드가 자체 서비스 확인 및 확인 루프를 구현할 필요가 없으므로 게이트웨이의 주요 이점 중 하나입니다.

일반적으로 서비스에 연결할 수 없는 경우 이는 서비스 인스턴스 또는 복제가 일반적인 수명 주기의 일부로 다른 노드로 이동되었음을 의미합니다. 이 경우 게이트웨이는 끝점이 더 이상 원래 확인된 주소에 열려 있지 않음을 나타내는 네트워크 연결 오류를 수신할 수 있습니다.

그러나 복제 또는 서비스 인스턴스는 호스트 프로세스를 공유할 수 있으며 다음을 비롯하여 http.sys 기반 웹 서버에 의해 호스팅될 경우 포트를 공유할 수도 있습니다.

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET 코어 WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [카타나](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

이 상황에서 웹 서버는 호스트 프로세스에서 사용할 수 있고 요청에 응답할 가능성이 있지만 확인된 서비스 인스턴스 또는 복제는 더 이상 호스트에서 사용할 수 없습니다. 이 경우 게이트웨이는 웹 서버에서 HTTP 404 응답을 수신합니다. 따라서 HTTP 404에는 두 가지 의미가 있습니다.

 1. 서비스 주소가 올바르지만 사용자가 요청한 리소스가 없습니다.
 2. 서비스 주소가 잘못되었으며 사용자가 요청한 리소스가 실제로 다른 노드에 존재할 수 있습니다.

첫 번째 경우, 이는 사용자 오류로 간주되는 일반적인 HTTP 404입니다. 그러나 두 번째 경우 사용자는 존재하는 리소스를 요청했지만 서비스 자체가 이동되어 해당 리소스를 찾을 수 없었으며, 이 경우 게이트웨이가 주소를 다시 확인하고 다시 시도해야 합니다.

그러므로 게이트웨이는 두 경우를 구별하는 방법이 필요합니다. 이러한 구별을 하려면 서버에서 제공하는 힌트가 필요합니다.

 - 기본적으로 응용 프로그램 게이트웨이는 경우 # 2를 가정하여 요청을 다시 확인하고 다시 실행하려고 시도합니다.
 - 응용 프로그램 게이트웨이에 대해 경우 #1을 나타내려면 서비스가 다음과 같은 HTTP 응답 헤더를 반환해야 합니다.

`X-ServiceFabric : ResourceNotFound`

이 HTTP 응답 헤더는 요청한 리소스가 존재하지 않는 일반적인 HTTP 404 상황을 나타내며, 게이트웨이는 서비스 주소를 다시 확인하려고 시도하지 않습니다.

## 설정 및 구성
[Azure Resource Manager 템플릿](./service-fabric-cluster-creation-via-arm.md)을 통해 클러스터에 대해 서비스 패브릭 역방향 프록시를 사용하도록 설정할 수 있습니다.

배포하려는 클러스터에 대한 템플릿이 있으면(샘플 템플릿이 있거나 사용자 지정 리소스 관리자 템플릿을 만들어) 다음 단계에 따라 템플릿에서 역방향 프록시를 사용하도록 설정할 수 있습니다.

1. 템플릿의 [매개 변수 섹션](../resource-group-authoring-templates.md)에서 역방향 프록시에 대한 포트를 정의합니다.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. **클러스터** [리소스 형식 섹션](../resource-group-authoring-templates.md)에서 각 Nodetype 개체에 대한 포트를 지정합니다.

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Azure 클러스터 외부에서 역방향 프록시를 주소 지정하려면 1단계에서 지정한 포트에 대해 **Azure Load Balancer 규칙**을 설정합니다.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. 역방향 프록시에 대한 포트에서 SSL 인증서를 구성하려면 **클러스터** [리소스 유형 섹션](../resource-group-authoring-templates.md)에서 해당 인증서를 httpApplicationGatewayCertificate 속성에 추가합니다.

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## 다음 단계
 - [GitHUb의 샘플 프로젝트](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount)에서 서비스 간 HTTP 통신의 예를 참조하세요.

 - [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)

 - [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)

 - [Reliable Services를 사용한 WCF 통신](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

<!---HONumber=AcomDC_0921_2016-->