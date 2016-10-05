<properties
   pageTitle="Azure VM에서 SAP HANA 수동 설치에 대한 빠른 시작 가이드 | Microsoft Azure"
   description="Azure VM에서 SAP HANA 수동 설치에 대한 빠른 시작 가이드"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Azure VM에서 단일 인스턴스 SAP HANA 수동 설치에 대한 빠른 시작 가이드

## 소개

이 빠른 시작 가이드를 사용하면 SAP NetWeaver 7.5 및 SAP HANA SP12를 수동으로 설치하여 Azure VM에서 단일 인스턴스 SAP HANA 프로토타입/데모 시스템을 설정할 수 있습니다. 이 가이드는 독자가 JSON 템플릿 사용 옵션을 포함하여 Azure Portal 또는 Powershell/CLI를 통한 가상 컴퓨터나 가상 네트워크 배포 방법과 같은 Azure IaaS 기본 사항에 대해 잘 알고 있다는 것을 전제로 합니다. 또한 독자가 SAP HANA, SAP NetWeaver 및 온-프레미스 설치 방법에 익숙하고

이 문서 마지막의 일반 정보 섹션에 언급된 일반 SAP-Azure 설명서에 대해서도 알고 있다고 간주합니다.

프로덕션 환경이 아닌 시스템에서는 제한이 있으므로 이 가이드에서는 HA, 백업, DR, 고성능 또는 특수 보안 고려 사항에 대해 다루지 않습니다.

SAP-Linux-Azure는 Azure Resource Manager에서만 지원되고 클래식 모델에서는 지원되지 않으므로 이 샘플 설정에서는 두 대의 가상 컴퓨터를 사용하여 Azure Resource Manager 모델을 통해 분산 SAP NetWeaver 설치를 완료했습니다. Azure Resource Manager에 대한 자세한 내용의 링크는 이 문서 끝 부분의 일반 정보 섹션에 나와 있습니다.

샘플 설치에 사용된 두 테스트 VM은 다음과 같습니다.

* NW 7.5 ASCS 인스턴스 + PAS를 호스트하는 hana-appsrv(형식 DS3)
* HANA SP12를 호스트하는 hana-dbsrv(형식 GS4)
* 하나의 Azure Virtual Network(azure-hana-test-vnet)에 속한 두 VM
* 두 경우 모두 OS는 SLES 12 SP1


2016년 7월부터 SAP HANA는 Azure VM 형식 GS5의 OLAP(BW) 프로덕션 시스템에서만 완전히 지원됩니다. 테스트 목적으로 공식 SAP 지원이 필요하지 않은 경우 GS4 등 더 낮은 버전을 사용할 수도 있습니다. Azure의 SAP HANA에서는 HANA 데이터 및 로그 파일을 위해 항상 Azure Premium Storage를 사용해야 합니다. 자세한 내용은 뒷부분의 "디스크 설정"을 참조하세요. Azure에서 지원되는 SAP 제품에 대한 자세한 내용은 이 문서 끝 부분에 있는 일반 정보 섹션을 참조하세요.


이 가이드에서는 두 가지 방법으로 Azure VM에 SAP HANA를 수동으로 설치합니다.

* "데이터베이스 인스턴스" 단계에서 분산 NetWeaver 설치의 일부로 SWPM(SAP Software Provisioning Manager)을 통해 SAP HANA를 설치합니다.
* HANA Life Cycle Manager 도구 hdblcm을 사용하여 SAP HANA를 설치한 다음 NetWeaver를 설치합니다.

SWPM을 사용하여 모든 구성 요소(SAP HANA, SAP 응용 프로그램 서버, ASCS 인스턴스, SAP GUI)를 단일 VM에 설치할 수도 있습니다. 이 옵션은 이 문서에서 설명되지 않지만 고려해야 할 사항은 동일합니다.

설치를 시작하기 전에 아래의 검사 목록 다음에 나오는 Azure 테스트 VM 설정에 대한 섹션을 읽어 기본 Azure VM 구성만 사용할 때 발생하는 여러 가지 기본적인 실수를 방지해야 합니다.


## SAP SWPM을 통한 SAP HANA 설치 검사 목록

다음은 분산 SAP NW 7.5 설치를 수행하는 SAP SWPM을 통해 데모 또는 프로토타입 용도로 단일 인스턴스 SAP HANA를 수동으로 설치하는 것과 관련된 주요 항목에 대한 간단한 검사 목록입니다. 문서 전체에서 항목을 개별적으로 설명하고 스크린샷 형식으로 보다 자세한 내용을 제공합니다.

* 두 개의 테스트 VM이 포함된 Azure Virtual Network 만들기
* Azure Resource Manager 모델을 통해 OS SLES 12 SP1이 설치된 두 개의 Azure VM 배포
* 앱 서버 VM에 두 개의 표준 저장소 디스크 연결(예: 75GB 및 500GB)
* 4개의 디스크를 HANA DB 서버 VM에 연결 - 앱 서버 VM용 등 2개의 표준 저장소 디스크 + 2개의 Premium Storage 디스크(예: 2x512GB)
* 크기 및/또는 처리량 요구 사항에 따라 여러 디스크를 연결하고 lvm 또는 mdadm을 사용하여 OS 수준에서 VM 내부에 스트라이프 볼륨 만들기
* 연결된 디스크/논리 볼륨에 XFS 파일 시스템 만들기
* OS 수준에서 새 XFS 파일 시스템 탑재. 한 파일 시스템을 사용하여 모든 SAP 소프트웨어와 다른 데이터(예: sapmnt 디렉터리와 백업)를 보관합니다. SAP HANA DB 서버에서 Premium Storage 디스크의 XFS 파일 시스템을 /hana 및 /usr/sap로 탑재합니다. 이 작업은 Linux Azure VM의 루트 파일 시스템이 너무 커지지 않도록 유지하는 데 필요합니다.
* /etc/hosts에 테스트 VM의 로컬 IP 주소 입력
* /etc/fstab에 nofail 매개 변수 입력
* HANA-SLES-12 SAP Note에 따라 커널 매개 변수 설정(자세한 내용은 커널 매개 변수 섹션의 뒷부분 참조)
* 스왑 공간 추가
* 원하는 경우 테스트 VM에 그래픽 데스크톱을 설치하고 그렇지 않은 경우 원격 sapinst 설치 사용
* SAP Service Marketplace에서 SAP 소프트웨어 다운로드
* 앱 서버 VM에 SAP ASCS 인스턴스 설치
* NFS를 통해 테스트 VM 간에 sapmnt 디렉터리 공유(앱 서버 VM이 NFS 서버)
* DB 서버 VM에 SWPM을 통해 HANA를 포함한 데이터베이스 인스턴스 설치
* 앱 서버 VM에 PAS 설치
* SAP MC를 시작하고 SAP GUI/HANA Studio 등을 통해 연결



## hdblcm을 통한 SAP HANA 설치 검사 목록

다음은 분산 SAP NW 7.5 설치를 수행하는 SAP SWPM을 통해 데모 또는 프로토타입 용도로 단일 인스턴스 SAP HANA를 수동으로 설치하는 것과 관련된 주요 항목에 대한 간단한 검사 목록입니다. 문서 전체에서 항목을 개별적으로 설명하고 스크린샷 형식으로 보다 자세한 내용을 제공합니다.

* 두 개의 테스트 VM이 포함된 Azure Virtual Network 만들기
* Azure Resource Manager 모델을 통해 OS SLES 12 SP1이 설치된 두 개의 Azure VM 배포
* 앱 서버 VM에 두 개의 표준 저장소 디스크 연결(예: 75GB 및 500GB)
* 4개의 디스크를 HANA DB 서버 VM에 연결 - 앱 서버 VM용 등 2개의 표준 저장소 + 2개의 Premium Storage 디스크(예: 2x512GB)
* 크기 및/또는 처리량 요구 사항에 따라 여러 디스크를 연결하고 lvm 또는 mdadm을 사용하여 OS 수준에서 VM 내부에 스트라이프 볼륨 만들기
* 연결된 디스크/논리 볼륨에 XFS 파일 시스템 만들기
* OS 수준에서 새 XFS 파일 시스템 탑재. 한 파일 시스템을 사용하여 모든 SAP 소프트웨어와 다른 데이터(예: sapmnt 디렉터리와 백업)를 보관합니다. SAP HANA DB 서버에서 Premium Storage 디스크의 XFS 파일 시스템을 /hana 및 /usr/sap로 탑재합니다. 이 작업은 Linux Azure VM의 루트 파일 시스템이 너무 커지지 않도록 유지하는 데 필요합니다.
* /etc/hosts에 테스트 VM의 로컬 IP 주소 입력
* /etc/fstab에 nofail 매개 변수 입력
* HANA-SLES-12 SAP Note에 따라 커널 매개 변수 설정(자세한 내용은 커널 매개 변수 섹션의 뒷부분 참조)
* 스왑 공간 추가
* 원하는 경우 테스트 VM에 그래픽 데스크톱을 설치하고 그렇지 않은 경우 원격 sapinst 설치 사용
* SAP Service Marketplace에서 SAP 소프트웨어 다운로드
* HANA DB 서버 VM에 그룹 ID가 1001인 "sapsys" 그룹 만들기
* hdblcm을 사용하여 DB 서버 VM에 SAP HANA 설치
* 앱 서버 VM에 SAP ASCS 인스턴스 설치
* NFS를 통해 테스트 VM 간에 sapmnt 디렉터리 공유(앱 서버 VM이 NFS 서버)
* DB 서버 VM에 SWPM을 통해 HANA를 포함한 데이터베이스 인스턴스 설치
* 앱 서버 VM에 PAS 설치
* SAP MC를 시작하고 SAP GUI/HANA Studio 등을 통해 연결




## SAP HANA의 수동 설치를 위한 Azure VM 준비

이 장에서는 SAP HANA 수동 설치를 위한 Azure VM 준비에 대해 설명하며 다음 5개 섹션으로 구성됩니다.

* 디스크 설정
* 커널 매개 변수
* 파일 시스템
* /etc/hosts
* /etc/fstab


### 디스크 설정

Azure에서 Linux VM의 루트 파일 시스템 크기는 제한되어 있습니다. 따라서 SAP를 실행하기 위한 추가 디스크 공간을 VM에 연결해야 합니다. 순수 프로토타입/데모 환경에서 사용되는 SAP 앱 서버 VM의 경우 Azure 표준 저장소 디스크를 사용하는 것이 좋습니다. 그러나 SAP HANA DB 데이터 및 로그 파일에 대해서는 프로덕션 환경이 아닌 경우에도 Azure Premium Storage 디스크를 사용해야 합니다.

Linux VM에 디스크를 연결하는 방법에 대한 자세한 내용은 [여기](virtual-machines-linux-add-disk.md)를 참조하세요.

Azure 디스크 캐싱의 경우 HANA 트랜잭션 로그를 저장하는 데 사용할 디스크에 대해 "없음"을 사용해야 합니다. HANA 데이터 파일의 경우 읽기 캐싱을 사용해도 괜찮습니다. HANA는 메모리 내 데이터베이스이므로 전반적인 사용 패턴에 따라 Azure 디스크 수준에서 성능을 향상시키는 읽기 캐시의 양이 달라집니다(예: HANA를 시작하고 디스크의 데이터를 메모리로 읽기).

Azure Premium Storage에 대한 자세한 내용은 [여기](../storage/storage-premium-storage.md)를 참조하세요.

VM을 만들기 위한 샘플 JSON 템플릿은 [여기](https://github.com/Azure/azure-quickstart-templates)를 참조하세요. "101-vm-simple-linux"는 100GB 데이터 디스크를 추가하는 저장소 섹션을 포함하여 기본 템플릿이 어떻게 표시되는지 보여 줍니다.

[이 문서](virtual-machines-linux-sap-on-suse-quickstart.md)에서는 Powershell 또는 CLI를 통해 SUSE 이미지를 찾는 방법과 UUID를 통한 디스크 연결의 중요성에 대해 설명합니다.


시스템의 크기와 처리량 요구 사항에 따라 하나의 디스크 대신 여러 디스크를 연결하고 나중에 OS 수준에서 이러한 디스크에 대해 스트라이프 세트를 만들어야 할 수 있습니다. 다음은 여러 Azure 디스크에 대해 스트라이프 세트를 만드는 두 가지 이유입니다.

* 처리량 증가
* 현재 Azure 디스크 크기 제한이 1TB(2016년 7월부터)이므로 1TB보다 큰 단일 파일 시스템 필요


스트라이프를 구성하는 두 가지 주요 도구에 대한 자세한 정보는 다음을 참조하세요.

[Azure VM에서 mdadm을 사용하여 Linux 소프트웨어 RAID 구성에 대한 문서](virtual-machines-linux-configure-raid.md)

[Linux Azure VM에서 논리 볼륨 관리자 구성에 대한 문서](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

테스트 환경에서 두 개의 Azure 표준 저장소 디스크가 SAP 앱 서버 VM에 연결되어 있습니다. 한 디스크는 모든 SAP 소프트웨어 설치(예: NetWeaver 7.5, SAP GUI, SAP HANA .... )를 저장하는 데 사용되고, 다른 디스크는 동일한 SAP 환경에 속한 모든 VM에서 공유하는 sapmnt 디렉터리(예: SAP 프로필)를 비롯한 여러 데이터(예: 백업, 테스트 데이터)를 저장하는 데 필요하므로 충분한 공간이 있어야 합니다.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

앱 서버 VM과 달리 SAP HANA 서버 VM에는 4개의 디스크가 연결되어 있습니다. 이전처럼 SAP 소프트웨어를 보관하는 디스크 하나(NFS를 통해 SAP 소프트웨어 디스크를 공유할 수도 있음)와 백업 등을 위해 충분한 공간이 있는 디스크 하나를 사용했습니다. 두 개의 추가 디스크는 SAP HANA 데이터와 로그 파일 및 /usr/sap 디렉터리를 저장하는 Azure Premium Storage 디스크입니다.


### 커널 매개 변수


SAP HANA에는 표준 Azure 갤러리 이미지의 일부가 아니어서 수동으로 설정해야 하는 특정 Linux 커널 설정이 필요합니다. 설정을 설명하는 특정 SAP Note가 있습니다.


SAP Note SAP HANA DB: SLES 12/SLE for SAP Applications 12를 위한 권장 운영 체제 설정: [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)

SLES의 SAP HANA 실행과 관련된 페이지-캐시에 대한 추가 항목은 [여기](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache)(6.1장 Kernel: Page-Cache Limit(커널: 페이지-캐시 제한))를 참조하세요.

페이지-캐시 제한 [SAP Note 1557506](https://service.sap.com/sap/support/notes/1557506)과 관련된 SAP Note도 있습니다.

SLES 12에는 이전 sapconf 유틸리티를 대체하는 새로운 도구가 있습니다. 바로 "tuned-adm"이며 특별한 SAP HANA 프로필을 사용할 수 있습니다. 이 도구에 대한 자세한 내용은 아래의 두 링크를 참조하세요.

tuned-adm 프로필 sap-hana에 대한 SLES 설명서는 [여기](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)를 참조하세요.

tuned-adm 프로필 sap-hana에 대한 SLES 설명서 6.2장 Tuning Systems for SAP Workloads with tuned-adm(tuned-adm을 사용하여 SAP 작업을 위해 시스템 튜닝)은 [여기](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)를 참조하세요.


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

여기에서는 "tuned-adm"이 필수 SAP HANA 설정에 따라 numa\_balancing 값뿐만 아니라 transparent\_hugepage를 변경한 방법을 볼 수 있습니다.


SAP HANA 커널 설정을 영구적으로 만들려면 SLES 12에서 grub2를 사용해야 합니다. grub2에 대한 추가 정보는 [여기](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)를 참조하세요.


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

이 스크린샷에서는 구성 파일에서 커널 설정을 변경한 다음 grub2-mkconfig를 통해 "컴파일하는" 방법을 보여 줍니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

또 다른 옵션은 Yast 및 부팅 로더 커널 매개 변수 설정을 통해 설정을 변경하는 것입니다.


### 파일 시스템 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

여기에서는 SAP 앱 서버 VM에 연결된 두 개의 Azure 표준 저장소 디스크에 만들어진 두 개의 파일 시스템을 볼 수 있습니다. 두 파일 시스템의 형식은 XFS이며 /sapdata 및 /sapsoftware에 탑재됩니다.

반드시 이러한 방식으로 작업을 수행할 필요는 없습니다. 다른 옵션으로 디스크 공간을 구성할 수 있습니다. 가장 중요한 사항은 루트 파일 시스템의 공간이 부족하게 되는 것을 방지하는 것입니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

SAP HANA DB VM과 관련하여 sapinst(swpm)를 통해 데이터베이스를 설치하거나 간단한 "일반" 설치 옵션을 사용하는 경우에는 기본적으로 /hana 및 /usr/sap에 설치된다는 점을 알고 있는 것이 중요합니다. SAP HANA 로그 백업에 대한 기본 설정의 예는 /usr/sap입니다. 이전처럼 가장 중요한 사항은 루트 파일 시스템의 공간이 부족해지는 것을 방지하는 것입니다. 따라서 swpm을 통해 SAP HANA를 설치하기 전에 /hana 및 /usr/sap에 충분한 여유 공간이 있는지 확인해야 합니다.

[이 SAP 문서에서는](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) SAP HANA의 표준 파일 시스템 레이아웃에 대해 설명합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

표준 SLES 12 Azure 갤러리 이미지에 SAP NetWeaver 설치 시 스왑 공간이 없다는 메시지가 표시됩니다. 이 메시지를 제거하려면 이 문서에 설명된 대로 dd, mkswap 및 swapon을 통해 스왑 파일을 수동으로 추가합니다. [이 문서](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)에서 "Adding a Swap File Manually"(수동으로 스왑 파일 추가)를 검색하세요.

또는 Linux VM 에이전트를 통해 스왑 공간을 구성할 수도 있습니다. 자세한 내용은 [여기](virtual-machines-linux-agent-user-guide.md)를 참조하세요.


### /etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

SAP 설치를 시작하기 전에 SAP VM의 호스트 이름 및 IP 주소를 /etc/hosts 파일에 포함하는 것이 중요합니다. 하나의 Azure Virtual Network에 모든 SAP VM을 배포한 다음 내부 IP 주소를 사용해야 합니다.

### /etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

테스트 단계에서 fstab에 nofail 매개 변수를 추가하는 것이 좋습니다. 디스크에 오류가 발생한 경우에도 VM은 계속 작동하므로 부팅 프로세스가 중단되지 않습니다. 그러나 이 경우 추가 디스크 공간을 사용할 수 없어 프로세스가 루트 파일 시스템을 채울 수 있으므로 주의해야 합니다. /hana를 누락한 경우 SAP HANA가 시작되지 않습니다.


## SLES 12에 그래픽 Gnome 데스크톱 설치

이 장은 다음 항목을 다루는 두 개의 섹션으로 구성되어 있습니다.

* SLES 12에 Gnome 데스크톱 및 xrdp 설치
* SLES 12에서 Firefox를 사용하여 Java 기반 SAP MC 실행

Xterminal, VNC와 같은 대체 방법을 사용할 수 있지만 2016년 9월부터 이 문서에서는 xrdp만 설명합니다.

### SLES 12에 Gnome 데스크톱 및 xrdp 설치

Microsoft Windows에 대한 배경 지식이 있고 SAP Linux VM 내에서 직접 그래픽 데스크톱을 사용하여 Firefox, Sapinst, SAP GUI, SAP MC 또는 HANA Studio를 실행하려고 하고 Microsoft Windows 컴퓨터에서 RDP를 통해 VM에 연결하려는 경우 간단한 방법으로 이를 수행할 수 있습니다. 이는 프로덕션 데이터베이스 서버 등에는 적절하지 않을 수 있지만 순수 프로토타입/데모 환경에는 적합합니다. Azure SLES 12 VM에 Gnome 데스크톱을 설치하는 단계는 다음과 같습니다.

다음 명령에 따라 gnome 데스크톱을 설치합니다(예: Putty 창에서).

   zypper in -t pattern gnome-basic

그런 다음 RDP를 통해 VM에 연결을 허용하도록 xrdp를 설치합니다.

   zypper in xrdp

/etc/sysconfig/windowmanager를 편집하고 기본 창 관리자를 Gnome으로 설정합니다.

   DEFAULT\_WM="gnome"

chkconfig를 실행하여 재부팅 후 xrdp가 자동으로 시작되도록 합니다.

  chkconfig -level 3 xrdp on

RDP 연결에 문제가 있는 경우 다시 시작합니다(Putty 창 외부에서).

  /etc/xrdp/xrdp.sh restart

xrdp를 재시작할 때 위에서 언급한 대로 작동하지 않으면 .pid 파일이 있는지 확인하고 제거합니다.

  /var/run을 확인하고 xrdp.pid를 찾아서 제거한 다음 다시 시작합니다.



### SAP MC


이전 섹션에 설명된 대로 Gnome 데스크톱 설치 후 Azure SLES 12 VM에서 실행 중인 Firefox 외부에서 그래픽 Java 기반 SAP MC를 시작하려고 하면 Java 브라우저 플러그 인이 없다는 오류가 발생합니다.

SAP MC를 시작하는 URL은 <server>:5<instance\_number>13입니다.

자세한 내용은 [여기](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)를 참조하세요.


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

위의 스크린샷에서 Java 브라우저 플러그 인이 없는 경우 표시되는 오류 메시지를 볼 수 있습니다.

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

문제를 해결하는 하나의 간단한 옵션은 Yast를 통해 누락된 플러그 인을 설치하는 것입니다.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

SAP MC URL을 반복하면 처음으로 플러그 인을 활성화할 것인지 묻는 작은 대화 상자가 나타납니다.


javafx.properties 파일이 없다는 오류 메시지가 발생할 수도 있습니다. 이 오류는 SAP GUI 7.4에 필요한 Java 1.8 설치와 밀접하게 관련되어 있습니다.

Yast를 통해 표시되는 IBM Java 버전에는 이 파일이 없습니다. 이 문제를 해결하려면 Oracle에서 Java를 다운로드합니다. 이 문제에 대한 자세한 내용은 [여기](https://scn.sap.com/thread/3908306)를 참조하세요.



## NetWeaver 7.5 설치의 일부로 SWPM을 통해 수동 SAP HANA 설치


다음 스크린샷 목록에서는 SWPM(sapinst)을 통해 SAP NetWeaver 7.5 및 SAP HANA SP12를 설치하는 주요 단계를 보여 줍니다. NW 7.5 설치의 일부로 SWPM은 HANA 데이터베이스를 단일 인스턴스로 설치할 수 있습니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

샘플 테스트 환경에서는 하나의 ABAP 앱 서버만 설치되었습니다. "분산 시스템" 옵션을 사용하여 한 Azure VM에 ASCS 인스턴스 및 기본 앱 서버 인스턴스를 설치하고 또 다른 Azure VM에 SAP HANA를 데이터베이스 시스템으로 설치했습니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

ASCS 인스턴스를 앱 서버 VM에 설치하고 SAP MC에서 "녹색"으로 설정한 후 SAP 프로필 디렉터리 등을 포함하는 sapmnt 디렉터리를 SAP HANA DB 서버 VM과 공유해야 합니다. DB 설치 단계에서 이 정보에 액세스해야 합니다. 가장 좋은 방법은 Yast를 사용하여 구성할 수 있는 NFS를 사용하는 것입니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

앱 서버 VM에서 sapmnt 디렉터리는 "rw" 및 "no\_root\_squash" 옵션을 사용하여 NFS를 통해 공유되어야 합니다. 기본값은 "ro" 및 "root\_squash"이며 데이터베이스 인스턴스 설치 시 문제가 발생할 수 있습니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

SAP HANA DB 서버 VM에서 앱 서버 VM의 sapmnt 공유는 "NFS 클라이언트"(예: Yast 사용)를 통해 구성해야 합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

그런 다음 SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작하여 분산 NW 7.5 설치의 다음 단계인 "데이터베이스 인스턴스"를 완료해야 합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

SAP HANA 설치 시 "일반" 설치를 선택한 경우에는 입력할 사항이 많지 않습니다. 설치 미디어의 경로 외에 DB SID, 호스트 이름, 인스턴스 번호 및 DB Sys Admin 암호를 입력해야 합니다.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

다음 단계는 DBACOCKPIT 스키마에 대한 암호를 입력하는 것입니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

그런 다음 SAPABAP1 스키마 암호를 입력합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

마지막에는 DB 설치의 모든 단계 앞에 녹색 확인 표시만 나타나야 하고 "..... 데이터베이스 인스턴스의 실행이 완료되었습니다."라는 작은 메시지 상자가 나타나야 합니다.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

설치 성공 후 SAP MC에 DB 인스턴스가 "녹색"으로 표시되고 SAP HANA 프로세스의 전체 목록(예: hdbindexserver, hdbcompileserver)이 표시되어야 합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

이 스크린샷에서는 HANA 설치 중 SWPM에서 만든 /hana/shared의 파일 구조 일부를 보여 줍니다. 다른 경로를 지정할 수는 없습니다. 따라서 SWPM을 통해 SAP HANA를 설치하기 전에 루트 파일 시스템의 공간이 부족해지지 않도록 /hana에 추가 디스크 공간을 탑재하는 것이 중요합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

여기에서 앞의 /usr/sap 디렉터리에 대해 설명한 것과 동일한 것을 볼 수 있습니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

분산 ABAP 설치의 마지막 단계는 "기본 응용 프로그램 서버 인스턴스"입니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

PAS와 SAP GUI가 설치되면 트랜잭션 "dbacockpit"를 통해 SAP HANA 설치에 문제가 없는지 확인할 수 있습니다.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

마지막 단계로 SAP 앱 서버 VM에 SAP HANA Studio를 설치하고 DB 서버 VM에서 실행 중인 SAP HANA 인스턴스에 연결할 수 있습니다.




## HANA Life Cycle Manager 도구 hdblcm을 통해 수동 SAP HANA 설치


SWPM을 통한 분산 설치의 일부로 SAP HANA를 설치하는 방법 외에도 먼저 hdblcm을 사용하여 독립 실행형 HANA를 설치한 다음 SAP NetWeaver 7.5 등을 설치할 수도 있습니다. 아래 스크린샷 목록에서는 작동 방식을 보여 줍니다.

다음은 HANA hdblcm 도구에 대해 설명하는 세 개의 문서입니다.

[Choosing the Correct SAP HANA HDBLCM for Your Task(작업을 위해 올바른 SAP HANA HDBLCM 선택)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA Lifecycle Management Tools(SAP HANA 수명 주기 관리 도구)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Installation and Update Guide(SAP HANA 서버 설치 및 업데이트 가이드)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

나중에 hdblcm 도구로 만든 <HANA SID>adm 사용자에 대한 기본 그룹 ID에 문제가 발생하지 않도록 hdblcm을 사용하여 SAP HANA를 설치하기 전에 그룹 ID가 1001인 새 그룹 "sapsys"를 정의해야 합니다.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

hdblcm을 처음 시작한 다음 간단한 시작 메뉴에서 항목 1 "Install new System"(새 시스템 설치)을 선택해야 합니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

이 스크린샷에서 전에 입력한 주요 옵션을 모두 볼 수 있습니다. 중요 - HANA 로그 및 데이터 볼륨의 이름을 사용한 디렉터리와 설치 경로(이 샘플에서는 /hana/shared) 및 /usr/sap는 Azure VM 설정 섹션에 설명된 대로 루트 파일 시스템에 포함되지 않지만 VM에 연결된 Azure 데이터 디스크에 속해야 합니다. 이렇게 하면 루트 파일 시스템의 공간이 부족해지는 것을 방지할 수 있습니다. 또한 HANA admin 사용자의 사용자 ID가 1005이고 설치 전에 정의한 sapsys 그룹(ID 1001)에 속하는 것을 알 수 있습니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

/etc/passwd에서 HANA <HANA SID>adm(이 샘플에서는 azdadm) 사용자 세부 정보를 확인할 수 있습니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

hdblcm을 사용하여 SAP HANA를 설치한 다음 SAP HANA Studio에서 볼 수 있습니다. 예를 들어 모든 SAP NetWeaver 테이블을 포함하는 SAPABAP1 스키마는 아직 사용할 수 없습니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

SAP HANA를 설치한 후 이를 기반으로 SAP NetWeaver를 설치할 수 있습니다. 이 샘플에서는 위의 해당 섹션에 설명된 대로 SWPM을 통한 "분산 설치"를 사용하여 수행되었습니다. SWPM을 통해 데이터베이스 인스턴스 설치 시 이전에 hdblcm을 사용하여 설치했을 때와 동일한 데이터(예: 호스트 이름, HANA SID, 인스턴스 번호)를 입력해야 합니다. 그러면 SWPM이 기존 HANA 설치를 사용하고 스키마를 더 추가합니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

다음은 DBACOCKPIT 스키마와 관련하여 데이터를 입력해야 하는 SWPM 설치 단계에 대한 그림입니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

그런 다음 SAPABAP1 스키마에 대한 데이터를 입력해야 합니다.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

SWPM 데이터베이스 인스턴스 설치가 완료되면 HANA Studio에서 SAPABAP1 스키마를 볼 수 있습니다.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

마지막으로 SAP 앱 서버 및 SAP GUI를 설치한 후 트랜잭션 "dbacockpit"를 사용하여 HANA DB 인스턴스를 확인할 수 있어야 합니다.




## SAP Azure 인증서, Azure에서 SAP HANA 실행 및 SAP 소프트웨어 다운로드와 관련된 일반 정보

* Windows OS에서 클래식 모드로 Azure의 SAP를 실행하는 것에 대한 일반 SAP Azure 문서: [Azure의 Windows 가상 컴퓨터에서 SAP 사용](virtual-machines-windows-classic-sap-get-started.md)

* 고객이 사용할 수 있는 기존 SAP 템플릿에 대한 자세한 내용: [Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)(SAP용 Azure 빠른 시작 템플릿)

* Linux OS에서 Azure Resource Manager 모델로 Azure의 SAP를 실행하는 것에 대한 일반 SAP Azure 문서: [Linux VM(가상 컴퓨터)에서 SAP 사용](virtual-machines-linux-sap-get-started.md)

* 프로덕션에 대해 지원되는 Azure VM 형식을 나열하는 인증된 SAP HANA 하드웨어 디렉터리: [Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)(인증된 SAP HANA® 하드웨어 디렉터리)

* 특히 Linux 작업을 위한 가상 컴퓨터 크기에 대한 정보: [Azure에서 가상 컴퓨터에 대한 크기](virtual-machines-linux-sizes.md)

* Azure에서 지원되는 모든 SAP 제품 및 SAP에 대해 지원되는 Azure VM 형식을 나열한 SAP Note: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Azure의 Linux VM을 사용한 SAP "고급 모니터링"에 대한 SAP Note: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* Azure "큰 인스턴스"에 대한 SAP HANA 제공 사항. 이 정보는 Azure VM에서의 SAP HANA 실행에 대한 내용이 아니라 SAP 앱 서버가 Azure VM에서 실행되지만 SAP HANA가 운영 체제 미설치 서버에서 실행되는 하이브리드 환경에 대한 내용입니다. 자세한 내용: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Linux의 SAPOSCOL에 대한 SAP Note: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Microsoft Azure의 SAP용 주요 모니터링 메트릭: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Azure Resource Manager에 대한 정보: [Azure Resource Manager 개요](../resource-group-overview.md)

* 템플릿을 통한 Linux VM 배포에 대한 정보: [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-linux-cli-deploy-templates.md)

* Azure Resource Manager와 클래식 간의 배포 모델 비교: [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../resource-manager-deployment-model.md)

* SAP Service Marketplace에서 Linux/HANA용 NetWeaver 7.5 다운로드: ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* SAP Service Marketplace에서 HANA SP12 플랫폼 버전 다운로드: ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!---HONumber=AcomDC_0921_2016-->