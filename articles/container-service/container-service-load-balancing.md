<properties
   pageTitle="Azure Container Service 클러스터에서 컨테이너 부하 분산 | Microsoft Azure"
   description="Azure Container Service 클러스터에 있는 여러 컨테이너에 대한 부하 분산입니다."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="컨테이너, 마이크로 서비스, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# Azure Container Service 클러스터에서 컨테이너 부하 분산

이 문서에서는 Marathon-LB를 사용하여 DC/OS 관리되는 Azure Container Service에서 내부 부하 분산 장치를 만드는 방법을 탐색합니다. 이렇게 하면 응용 프로그램을 수평으로 확장할 수 있습니다. 또한 이렇게 하면 부하 분산 장치를 공용 클러스터에 배치하고 응용 프로그램 컨테이너를 개인 클러스터에 배치하여 공용 및 개인 에이전트 클러스터를 활용할 수 있습니다.

## 필수 조건

DC/OS Orchestrator 유형을 사용하여 [Azure 컨테이너 서비스의 인스턴스를 배포](container-service-deployment.md)하고 [클라이언트가 클러스터에 연결할 수 있는지 확인합니다](container-service-connect.md).

## 부하 분산

컨테이너 서비스 클러스터에는 두 개의 부하 분산 계층이 있습니다.

  1. Azure Load Balancer는 공용 진입점(최종 사용자가 도달)을 제공합니다. 이 진입점은 Azure 컨테이너 서비스에서 자동으로 제공하며 기본적으로 포트 80, 443 및 8080을 노출하도록 구성됩니다.
  2. Marathon Load Balancer(marathon-lb)는 인바운드 요청을 해당 요청을 서비스하는 컨테이너 인스턴스로 라우팅합니다. 웹 서비스를 제공하는 컨테이너를 확장하면 marathon-lb는 동적으로 조정됩니다. Load Balancer는 컨테이너 서비스에서 기본적으로 제공되지 않지만 쉽게 설치할 수 있습니다.

## Marathon Load Balancer

Marathon Load Balancer는 배포한 컨테이너를 기준으로 동적으로 자체 재구성합니다. 컨테이너 또는 에이전트 손실에 복원력이 있으며 손실이 발생하는 경우 Apache Mesos는 다른 곳에서 컨테이너를 다시 시작하고 marathon-lb를 적용합니다.

Marathon Load Balancer를 설치하려면 DC/OS 웹 UI 또는 명령줄 중 하나를 사용할 수 있습니다.

### DC/OS 웹 UI를 사용하여 Marathon-LB 설치

  1. 'Universe'을 클릭합니다.
  2. 'Marathon-LB'를 검색합니다.
  3. '설치'를 클릭합니다.

![DC/OS 웹 인터페이스를 통해 marathon-lb 설치](./media/dcos/marathon-lb-install.png)

### DC/OS CLI를 사용하여 Marathon-LB 설치

DC/OS CLI를 설치하고 클러스터에 연결할 수 있는지 확인한 후에 클라이언트 컴퓨터에서 다음 명령을 실행합니다.

```bash
dcos package install marathon-lb
```

이 명령은 공용 에이전트 클러스터에 부하 분산 장치를 자동으로 설치합니다.

## 부하 분산된 웹 응용 프로그램 배포

이제 marathon-lb 패키지가 있으므로 부하를 분산하려는 응용 프로그램 컨테이너를 배포할 수 있습니다. 이 예에서는 다음 구성을 사용하여 간단한 웹 서버를 배포합니다.

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * `HAProxy_0_VHOST` 값을 에이전트에 대한 부하 분산 장치의 FQDN으로 설정합니다. `<acsName>agents.<region>.cloudapp.azure.com` 양식입니다. 예를 들어 `West US` 지역에 `myacs`이라는 컨테이너 서비스 클러스터를 만든 경우 FQDN은 `myacsagents.westus.cloudapp.azure.com`이 됩니다. 또한 [Azure Portal](https://portal.azure.com)의 컨테이너 서비스에 대해 만든 리소스 그룹에서 리소스를 조사할 때 "에이전트"를 포함한 부하 분산 장치를 검색하여 찾을 수도 있습니다.
  * servicePort를 10,000 이상의 포트로 설정합니다. 이 컨테이너에서 실행 중인 서비스를 식별하고 marathon-lb는 이를 사용하여 균형을 조정할 서비스를 식별합니다.
  * `HAPROXY_GROUP` 레이블을 "외부"로 설정합니다.
  * `hostPort`을 0으로 설정합니다. 이렇게 하면 해당 Marathon에서 사용 가능한 포트를 임의로 할당합니다.
  * `instances`을 만들 인스턴스 수로 설정합니다. 나중에 언제든지 확장하거나 축소할 수 있습니다.

Marathon은 기본적으로 개인 클러스터를 배포합니다. 즉, 위의 배포는 부하 분산 장치를 통해서만 액세스할 수 있고 이 동작을 일반적으로 수행하려고 합니다.

### DC/OS 웹 UI를 사용하여 배포

  1. [SSH 터널](container-service-connect.md)을 설정한 다음 http://localhost/marathon에서 Marathon 페이지에 방문하고 `Create Appliction`을 클릭합니다.
  2. `New Application` 대화 상자의 오른쪽 위 모퉁이에서 `JSON Mode`를 클릭합니다.
  3. 위의 JSON을 편집기에 붙여 넣습니다.
  4. `Create Appliction`을 클릭합니다.

### DC/OS CLI를 사용하여 배포

DC/OS CLI를 사용하여 이 응용 프로그램을 배포하려면 위의 JSON을 `hello-web.json`이라는 파일에 복사하고 실행합니다.

```bash
dcos marathon app add hello-web.json
```

## Azure Load Balancer

Azure Load Balancer는 기본적으로 포트 80, 8080 및 443을 노출합니다. 이러한 세 가지 포트 중 하나를 사용하는 경우(위의 예에서 실행하는 것처럼) 수행할 작업이 없습니다. 에이전트 부하 분산 장치의 FQDN에 도달할 수 있어야 하고 새로 고칠 때마다 라운드 로빈 방식으로 3개의 웹 서버 중 하나에 도달합니다. 하지만 다른 포트를 사용하는 경우 부하 분산 장치에서 사용한 포트에 대해 라운드 로빈 규칙 및 프로브를 추가해야 합니다. 이는 [Azure CLI](../xplat-cli-azure-resource-manager.md)에서 `azure lb rule create` 및 `azure lb probe create` 명령으로 수행할 수 있습니다. Azure 포털을 사용하여 수행할 수도 있습니다.


## 추가 시나리오

다른 서비스를 노출하기 위해 다른 도메인을 사용하는 시나리오를 포함할 수 있습니다. 예:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

이를 달성하려면 도메인을 특정 marathon-lb 경로에 연결하는 방법을 제공하는 [가상 호스트](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)를 확인하세요.

또는 서로 다른 포트를 노출하고 marathon-lb 뒤의 올바른 서비스에 다시 매핑할 수 있습니다. 예:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## 다음 단계

[marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/)에 대한 자세한 내용은 DC/OS 설명서를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->