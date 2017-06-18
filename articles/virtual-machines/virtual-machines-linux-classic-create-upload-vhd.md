<properties
	pageTitle="Linux VHD 만들기 및 업로드 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 또한 [Azure Resource Manager를 사용하여 사용자 지정 디스크 이미지를 업로드](virtual-machines-linux-upload-vhd.md)할 수도 있습니다.

이 문서에서는 VHD(가상 하드 디스크)를 생성 및 업로드하고 이를 Azure에서 가상 컴퓨터를 만들기 위한 고유한 이미지로 사용하는 방법을 소개합니다. 또한 이 이미지를 기반으로 여러 개의 가상 컴퓨터를 만들 수 있도록 운영 체제를 준비하는 방법을 살펴봅니다.

>  [AZURE.NOTE] 잠시 시간을 내어 사용 환경에 대한 [간단한 설문](https://aka.ms/linuxdocsurvey)에 응답하여 Azure Linux VM 설명서를 개선하는 데 도움을 주세요. 모든 답변은 작업 수행에 도움이 될 것입니다.

## 필수 조건
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

- **.vhd 파일에 설치된 Linux 운영 체제** - 가상 디스크에 VHD 형식으로 [Azure 보증 Linux 배포판](virtual-machines-linux-endorsed-distros.md)(또는 [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md) 참조)을 설치했습니다. VM과 VHD를 만드는 도구는 여러 가지가 있습니다.
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 또는 [KVM](http://www.linux-kvm.org/page/RunningKVM)을 설치 및 구성하고 VHD를 이미지 형식으로 사용하도록 주의합니다. 필요한 경우 `qemu-img convert`를 사용하여 [이미지를 변환](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)할 수 있습니다.
	- 또한 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 또는 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)에서 Hyper-v를 사용할 수 있습니다.

> [AZURE.NOTE] 새 VHDX 형식은 Azure에서 지원되지 않습니다. VM을 만들 때 VHD를 형식으로 지정합니다. 필요하다면 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 또는 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet을 사용하여 VHDX 디스크를 VHD로 변환할 수 있습니다. 그뿐 아니라 Azure는 동적 VHD 업로드를 지원하지 않으므로 업로드하기 전에 이러한 디스크를 정적 VHD로 변환해야 합니다. Azure로 업로딩하는 과정 중에 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go)와 같은 도구를 사용하여 동적 디스크를 변환할 수 있습니다.

- **Azure 명령줄 인터페이스** - VHD를 업로드하려면 최신 [Azure 명령줄 인터페이스](../virtual-machines-command-line-tools.md)를 설치합니다.

<a id="prepimage"> </a>
## 1단계: 업로드할 이미지 준비

Azure에서는 다양한 Linux 배포를 지원합니다([보증 배포판](virtual-machines-linux-endorsed-distros.md) 참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다. 다음 가이드의 단계를 완료한 후 여기로 돌아오면 Azure에 VHD 파일을 업로드할 수 있습니다.

- **[CentOS 기반 배포판](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 및 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[기타 - 보증되지 않는 배포](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] [Azure 인증 배포의 Linux](virtual-machines-linux-endorsed-distros.md)의 '지원되는 버전'에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux OS를 실행하는 가상 컴퓨터에 Azure 플랫폼 SLA가 적용됩니다. Azure 이미지 갤러리의 모든 Linux 배포는 필요한 구성이 포함된 보증 배포판입니다.

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 일반적인 추가 팁은 **[Linux 설치 참고 사항](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**을 참조하세요.


<a id="connect"> </a>
## 2단계: Azure 연결 준비

클래식 배포 모델(`azure config mode asm`)에서 Azure CLI를 사용하고 있는지 확인한 다음 계정에 로그인합니다.

```
azure login
```


<a id="upload"> </a>
## 3단계: Azure에 이미지 업로드

VHD 파일을 업로드할 저장소 계정이 필요합니다. 기존 저장소 계정을 선택하거나 [새 계정을 만들 수 있습니다](../storage/storage-create-storage-account.md).

Azure CLI에서 다음 명령을 사용하여 이미지를 업로드합니다.

```bash
azure vm image create <ImageName> `
	--blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
	--os Linux <PathToVHDFile>
```

이전 예제:

- **BlobStorageURL**은 사용하려는 저장소 계정에 대한 URL입니다.
- **YourImagesFolder**는 이미지를 저장하려는 Blob 저장소 내 컨테이너입니다.
- **VHDName**은 가상 하드 디스크를 식별하기 위해 포털에 표시되는 레이블입니다.
- **PathToVHDFile**은 컴퓨터에 있는 .vhd 파일의 전체 경로 및 이름입니다.

다음은 전체 예제입니다.

```bash
azure vm image create UbuntuLTS `
	--blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
	--os Linux /home/ahmet/UbuntuLTS.vhd
```

## 4단계: 이미지에서 VM 만들기
일반 VM과 같은 방식으로 `azure vm create`를 사용하여 VM을 만듭니다. 이전 단계에서 이미지에 부여한 이름을 지정합니다. 다음 예제에서는 이전 단계에서 지정한 **UbuntuLTS** 이미지 이름을 사용합니다.

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
	--location "West US" "DeployedUbuntu" UbuntuLTS
```

고유한 VM을 만들려면 자신의 사용자 이름 + 암호, 위치, DNS 이름 및 이미지 이름을 제공합니다.

## 다음 단계

자세한 내용은 [Azure 클래식 배포 모델에 대한 Azure CLI 참조](../virtual-machines-command-line-tools.md)를 참조하세요.

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0914_2016-->