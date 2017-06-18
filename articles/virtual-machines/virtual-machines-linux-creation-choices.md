<properties
	pageTitle="Linux VM을 만드는 다양한 방법 | Microsoft Azure"
	description="각 방법에 대한 도구 및 자습서를 비롯하여 Azure에 Linux 가상 컴퓨터를 만드는 다양한 방법을 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Azure에서 Linux 가상 컴퓨터를 만드는 다양한 방법

Azure에는 편리한 도구와 워크플로를 사용하여 Linux VM(가상 컴퓨터)을 만드는 유연성이 있습니다. 이 문서에서는 Linux 가상 컴퓨터를 만드는 경우의 차이점과 옵션을 요약하고 있습니다.


## Azure CLI 

Azure CLI는 npm 패키지, 배포판 제공 패키지 또는 Docker 컨테이너를 통해 여러 플랫폼에서 사용할 수 있습니다. [Azure CLI를 설치하고 구성하는 방법](../xplat-cli-install.md)에 대해 자세히 알아볼 수 있습니다. 다음 자습서에서는 Azure CLI를 사용하는 방법에 대한 예제를 제공합니다. 다음과 같이 표시되는 CLI 빠른 시작 명령에 대한 자세한 내용은 각 문서를 참고합니다.

- [개발 및 테스트를 위해 Azure CLI에서 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)
	- 다음 예제에서는 `azure_id_rsa.pub`라는 공개 키를 사용하여 CoreOS VM을 만듭니다.

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Azure 템플릿을 사용하여 안전한 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- 다음 예제에서는 템플릿에 저장된 GitHub를 사용하여 VM을 만듭니다.

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Azure CLI를 사용하여 완전한 Linux 환경 만들기](virtual-machines-linux-create-cli-complete.md)
	- 가용성 집합에서 부하 분산 장치 및 여러 VM을 만드는 방법이 포함됩니다.

- [Linux VM에 디스크 추가](virtual-machines-linux-add-disk.md)
	- 다음 예제에서는 `TestVM`라는 기존 VM에 5Gb 디스크를 추가합니다.

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Azure 포털

[Azure Portal](https://portal.azure.com)을 사용하면 시스템에 설치할 프로그램이 없으므로 VM을 신속하게 만들 수 있습니다. Azure 포털을 사용하여 다음과 같이 VM을 만듭니다.

- [Azure 포털을 사용하여 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md)
- [Azure 포털을 사용하여 디스크 연결](virtual-machines-linux-attach-disk-portal.md)


## 운영 체제 및 이미지 선택
VM을 만드는 경우 실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 미리 설치된 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 업로드합니다([다음 섹션](#use-your-own-image)을 참조).

### Azure 이미지
`azure vm image` CLI 명령을 사용하여 게시자, 배포판 릴리스 및 빌드에 따라 사용 가능한 기능을 확인합니다.

사용 가능한 게시자를 다음과 같이 나열합니다.

```bash
azure vm image list-publishers --location WestUS
```

지정된 게시자에 사용 가능한 제품(제품)을 다음과 같이 나열합니다.

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

지정된 제품 중 사용 가능한 SKU(배포판 릴리스)를 다음과 같이 나열합니다.

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

지정된 릴리스에 사용 가능한 모든 이미지를 다음과 같이 나열합니다.

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

사용 가능한 이미지 검색 및 사용에 대한 추가 예제는 [Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.

`azure vm quick-create` 및 `azure vm create` 명령에는 일반적인 배포판 및 해당 최신 릴리스에 신속하게 액세스하는 데 사용할 수 있는 몇 가지 별칭이 있습니다. 별칭을 사용하는 방법이 VM을 만들 때마다 게시자, 제품, SKU 및 버전을 지정하는 것보다 빠릅니다.

| Alias | 게시자 | 제안 | SKU | 버전 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 최신 |
| CoreOS | CoreOS | CoreOS | Stable | 최신 |
| Debian | credativ | Debian | 8 | 최신 |
| openSUSE | SUSE | openSUSE | 13\.2 | 최신 |
| RHEL | Redhat | RHEL | 7\.2 | 최신 |
| SLES | SLES | SLES | 12-SP1 | 최신 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 최신 |

### 사용자 고유의 이미지 사용

특정 사용자 지정이 필요한 경우 해당 VM을 *캡처*하여 기존 Azure VM을 기반으로 하는 이미지를 사용할 수 있습니다. 이미지로 만든 온-프레미스를 업로드할 수도 있습니다. 지원되는 배포판 및 사용자 고유의 이미지를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 인증 배포](virtual-machines-linux-endorsed-distros.md)

- [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md)

- [Linux 가상 컴퓨터를 Resource Manager 템플릿으로 캡처하는 방법](virtual-machines-linux-capture-image.md).
	- 기존 VM을 캡처하는 빠른 시작 예제 명령은 다음과 같습니다.

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## 다음 단계

- [CLI](virtual-machines-linux-quick-create-cli.md)로 [포털](virtual-machines-linux-quick-create-portal.md)에서 Linux VM을 만들거나 [Azure Resource Manager 템플릿](virtual-machines-linux-cli-deploy-templates.md)을 사용하여 Linux VM을 만듭니다.

- Linux VM을 만든 후에 [데이터 디스크를 추가합니다](virtual-machines-linux-add-disk.md).

- [암호 또는 SSH 키 다시 설정 및 사용자 관리](virtual-machines-linux-using-vmaccess-extension.md)하는 빠른 단계

<!---HONumber=AcomDC_0928_2016-->