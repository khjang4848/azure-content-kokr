<properties
	pageTitle="Linux의 보증 배포판 | Microsoft Azure"
	description="Ubuntu, OpenLogic, Oracle 및 SUSE 관련 지침을 포함하여 Azure에서 Linux의 인증 배포를 수행하는 방법에 대해 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"
	/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>



#Azure 인증 배포의 Linux

> [AZURE.NOTE] 잠시 시간을 내어 사용 환경에 대한 [간단한 설문](https://aka.ms/linuxdocsurvey)에 응답하여 Azure Linux VM 설명서를 개선하는 데 도움을 주세요. 모든 답변은 작업 수행에 도움이 될 것입니다.

Azure 갤러리 또는 마켓플레이스의 Linux 이미지는 다양한 파트너에서 제공되며, 여러 Linux 커뮤니티와의 작업을 통해 좀 더 다양한 인증 배포 목록을 추가합니다. 그러는 동안 이 갤러리에서 사용할 수 없는 배포의 경우, [이 페이지](virtual-machines-linux-classic-create-upload-vhd.md)의 지침에 따라 자신만의 Linux를 언제든지 가져올 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## 지원 되는 배포판 및 버전 ##

다음 표에서 Linux 배포판 및 Azure에서 지원 되는 버전을 나열합니다. 자세한 내용을 확인하려면 [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/ko-KR/kb/2941892)도 참조하세요.

Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 Microsoft가 업스트림 Linux 커널로 직접 제공되는 커널 모듈입니다. LIS 드라이버는 기본적으로 분포의 커널로 빌드되거나 이전 RHEL/CentOS 기반 분포는 [여기](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)에서 별도의 다운로드로 사용 가능합니다. LIS 드라이버에 대한 자세한 내용은 [이 문서](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements)를 참조하세요.

Azure Linux 에이전트는 Azure 갤러리 이미지에 사전에 이미 설치되며 분포의 패키지 저장소에서 일반적으로 사용할 수 있습니다. 소스 코드는 [GitHub](https://github.com/azure/walinuxagent)에서 찾을 수 있습니다.

배포|버전|드라이버|에이전트
---|---|---|---
OpenLogic의 CentOS | CentOS 6.3 이상, 7.0 이상 | CentOS 6.3:[LIS 다운로드](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: 커널에 있음 | 패키지: "WALinuxAgent"의 [OpenLogic 리포지토리](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494\.4.0 이상 | 커널에 있음 | 소스 코드: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9 이상, 8.2 이상 | 커널에 있음 | 패키지: "waagent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6\.4 이상, 7.0 이상 | 커널에 있음 | 패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6.7+, 7.1+ | 커널에 있음|패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12 이상 및 SAP 11.3 이상용 <p> SLES | 커널에 있음 | 패키지: "WALinuxAgent"의 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음 <br/>소스 코드: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2+ | 커널에 있음 | 패키지: "WALinuxAgent"의 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04 및 16.04 | 커널에 있음 | 패키지: "walinuxagent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent)


## 파트너

### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic은 클라우드와 데이터 센터에 있어서 엔터프라이즈 오픈 소스 솔루션의 선두 주자입니다. OpenLogic은 오픈 소스 소프트웨어를 안전하게 얻고 지원하고 제어하도록 다양한 산업 분야에 걸쳐 수 많은 선두 엔터프라이즈를 돕습니다. OpenLogic은 OpenLogic Expert Community에서 지원하는 600개의 오픈 소스 패키지에 대한 상업용 기술 지원 및 보장을 제공하며, 여기에는 Azure에 CentOS 이미지를 제공하기 위한 착수 파트너가 되는 것뿐만 아니라 CentOS에 대한 엔터프라이즈 수준의 지원도 포함됩니다.

### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS 웹사이트:

*CoreOS는 보안, 일관성 및 안정성을 위해 설계되었습니다. yum 또는 apt를 통한 패키지를 설치하는 대신 CoreOS는 더 높은 추상화 수준에서 서비스를 관리하려면 Linux 컨테이너를 사용합니다. 단일 서비스 코드 및 모든 종속성이 하나 또는 여러 CoreOS 컴퓨터에서 실행할 수 있는 컨테이너 내에서 패키지됩니다.*


### Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ는 독립적인 컨설팅 회사로써, 무료 소프트웨어를 이용한 전문 솔루션 개발 및 제공이 주요 사업 분야입니다. 오픈 소스 전문 회사인 Credative는 저희의 지원을 받는 많은 IT 부서들과 함께 국제적 명성을 얻고 있습니다. 현재 Credativ는 Microsoft와 협력 관계를 맺고 Debian 8(제시) 및 Azure에서 실행하도록 특별히 제작되고 플랫폼을 통해 쉽게 관리할 수 있는 7 전의 Debian(위지)에 상응하는 Debian 이미지를 준비 중입니다. Credativ는 또한 자체적인 오픈 소스 지원 센터를 통해 장기적인 유지 및 Azure의 Debian 이미지 업데이트를 지원합니다.

### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle의 전략은 공용 및 사설 클라우드에 대한 솔루션의 다양한 포트폴리오를 제공하며, 다른 클라우드뿐만 아니라 Oracle 클라우드에서 Oracle 소프트웨어를 배포하는 방법에 있어서 고객에게 선택권과 유연성을 제공하는 것입니다. Oracle과 Microsoft의 파트너 관계를 통해 고객은 Oracle의 확실한 인증 및 지원으로 Microsoft 공용 및 사설 클라우드에서 Oracle 소프트웨어를 배포할 수 있습니다. Oracle 공용 및 사설 클라우드 솔루션에 대한 Oracle의 이행 및 투자는 변하지 않습니다.

### Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat은 전 세계의 오픈 소스 솔루션 시장을 선도하는 기업입니다. Fortune지 선정 500대 기업의 90% 이상이 Red Hat의 도움을 받아 비즈니스 과제를 해결하고, IT와 비즈니스 전략을 연결하고, 기술의 미래를 준비하고 있습니다. 이를 위해 Red Hat은 개방형 비즈니스 모델과 경제적이고 예측 가능한 구독 모델을 통해 안전한 솔루션을 제공합니다.

### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure는 클라우드 컴퓨팅에 대해 우수한 안정성과 보안을 제공하는 검증된 플랫폼입니다. SUSE의 다양한 Linux 플랫폼은 Azure 클라우드 서비스와 자연스럽게 통합되어 쉽게 관리할 수 있는 클라우드 환경을 제공합니다. 1,800곳이 넘는 SUSE Linux Enterprise Server의 독립 소프트웨어 공급업체에서 9,200개 이상의 인증된 응용 프로그램을 통해 SUSE는 데이터 센터에서 지원되는 실행 중인 작업이 확실히 Azure에 배포될 수 있도록 합니다.

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical 엔지니어링과 개방형 커뮤니티 관리로 인해 고객에 대한 개인 클라우드 서비스와 클라이언트, 서버 및 클라우드 컴퓨팅에서 Ubuntu가 성공할 수 있었습니다. Ubuntu의 통합된 무료 플랫폼에 대해 Canonical은 전화에서 클라우드에 이르기까지 전화, 태블릿, TV 및 데스크톱에 일관된 인터페이스를 보여주어 공용 클라우드 공급자에서 가전 제품 제조업체에 이르기까지 다양한 기관에서 Ubuntu를 우선적으로 선택하도록 만들고 개인 과학 분야 기술자들 사이에서 선호하도록 만듭니다.

Canonical은 전 세계의 개발자 및 엔지니어링 센터와 함께 PC에서 서버 및 핸드헬드 장치에 이르는 업계에 Ubuntu 솔루션을 제공하기 위해 하드웨어 제조업체, 콘텐츠 공급자 및 소프트웨어 개발자와 유일하게 파트너 입장에 있습니다.

<!---HONumber=AcomDC_0914_2016-->