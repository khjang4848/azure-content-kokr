<properties
   pageTitle="Docker Machine으로 Azure에서 Docker 호스트 만들기 | Microsoft Azure"
   description="Docker Machine을 사용하여 Azure에서 Docker 호스트를 만드는 방법에 대해 설명합니다."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# Docker Machine으로 Azure에서 Docker 호스트 만들기

[Docker](https://www.docker.com/) 컨테이너를 실행하려면 호스트 VM이 docker 데몬을 실행해야 합니다. 이 항목에서는 Docker 데몬과 함께 구성된, Azure에서 실행되는 새 Linux VM을 만들기 위해 [docker-machine](https://docs.docker.com/machine/) 명령을 사용하는 방법에 대해 설명합니다.

**참고:**
- *이 문서는 docker-machine 버전 0.7.0 이상에 따라 달라집니다.*
- *가까운 미래에 Windows 컨테이너가 docker-machine을 통해 지원될 예정입니다.*

## Docker Machine으로 VM 만들기

`azure` 드라이버를 사용한 `docker-machine create` 명령으로 Azure에서 Docker 호스트를 만듭니다.

Azure 드라이버에는 구독 ID가 필요합니다. Azure 구독을 검색하려면 [Azure CLI](xplat-cli-install.md) 또는 [Azure Portal](https://portal.azure.com)을 사용할 수 있습니다.

**Azure 포털 사용**
- 왼쪽 탐색 페이지에서 구독을 선택하고, 구독 ID를 복사합니다.

**Azure CLI 사용**
- ```azure account list```을 입력하고 구독 ID를 복사합니다.

옵션과 해당 옵션의 기본값을 보려면 `docker-machine create --driver azure`을 입력합니다. 또한 자세한 내용은 [Docker Azure 드라이버 설명서](https://docs.docker.com/machine/drivers/azure/)를 참조할 수 있습니다.

다음 예제는 기본값을 사용하지만 인터넷 액세스에 대해 VM에서 선택적으로 포트 80을 엽니다.

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## Docker-machine으로 docker 호스트를 선택합니다.
호스트에 대해 docker-machine에 항목이 있다면, docker 명령을 실행할 때 기본 호스트를 설정할 수 있습니다.
##PowerShell 사용

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##Bash 사용

```bash
eval $(docker-machine env MyDockerHost)
```

이제 지정된 호스트에 대해 docker 명령을 실행할 수 있습니다.

```
docker ps
docker info
```

## 컨테이너 실행

호스트 구성을 마치면, 이제 간단한 웹 서버를 실행하여 호스트가 올바르게 구성되어 있는지 테스트할 수 있습니다. 여기서는 표준 nginx 이미지를 사용하고 포트 80에서 수신 대기하도록 지정하며 호스트 VM이 다시 시작될 경우 컨테이너도 다시 시작합니다(`--restart=always`).

```bash
docker run -d -p 80:80 --restart=always nginx
```

다음과 유사하게 출력됩니다.

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## 컨테이너 테스트

`docker ps`를 사용하여 실행 중인 컨테이너 검사

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

그리고 실행 중인 컨테이너를 확인하고 `docker-machine ip <VM name>`을 입력하여 브라우저에 입력할 IP 주소를 찾습니다.

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![ngnix 컨테이너 실행](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##요약
Docker-machine을 사용하면 개별 docker 호스트 유효성 검사를 위해 Azure에서 docker 호스트를 쉽게 프로비전할 수 있습니다. 컨테이너의 프로덕션 호스팅은 [Azure 컨테이너 서비스](http://aka.ms/AzureContainerService)를 참조합니다.

Visual Studio를 사용한 .NET 핵심 응용 프로그램 개발은 [Visual Studio 용 Docker 도구](http://aka.ms/DockerToolsForVS)를 참조합니다.

<!---HONumber=AcomDC_0921_2016-->