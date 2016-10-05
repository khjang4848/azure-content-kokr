<properties
	pageTitle="프리미엄 저장소: Azure 가상 컴퓨터 작업용 고성능 저장소 | Microsoft Azure"
	description="프리미엄 저장소는 Azure 가상 컴퓨터에서 실행되는 I/O 사용량이 많은 작업에 대해 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Azure DS 시리즈, DSv2 시리즈 및 GS 시리즈 VM은 프리미엄 저장소를 지원합니다."
	services="storage"
	documentationCenter=""
	authors="aungoo-msft"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="aungoo;robinsh"/>


# 프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소

## 개요

Azure 프리미엄 저장소는 I/O 사용량이 많은 작업을 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소를 사용하는 가상 컴퓨터(VM) 디스크는 솔리드 스테이트 드라이브(SSD)에 데이터를 저장합니다. 이 디스크의 속도와 성능 혜택을 활용하여 응용 프로그램의 VM 디스크를 Azure 프리미엄 저장소로 마이그레이션할 수 있습니다.

Azure VM은 여러 프리미엄 저장소 디스크의 연결을 지원하므로 응용 프로그램이 VM당 최대 64TB의 저장소를 지원할 수 있습니다. 프리미엄 저장소를 사용할 경우 읽기 작업의 대기 시간이 매우 짧은 상태로 VM당 80,000 IOPS(초당 입/출력 작업 수) 및 VM당 디스크 처리량 초당 2000MB를 얻을 수 있습니다.

프리미엄 저장소를 사용하여 Azure는 Dynamics AX, Dynamics CRM, Exchange Server, SharePoint Farms 및 SAP Business Suite 등의 까다로운 엔터프라이즈 응용 프로그램을 클라우드로 전환하는 기능을 제공합니다. 일관된 고성능 및 짧은 대기 시간을 필요로 하는 SQL Server, Oracle, MongoDB, MySQL, Redis 등 다양한 성능 집약적 데이터베이스 워크로드를 프리미엄 저장소에서 실행할 수 있습니다.

>[AZURE.NOTE] 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 컴퓨터 디스크를 Azure 프리미엄 디스크로 마이그레이션하는 것이 좋습니다. 디스크에 높은 IOPS가 필요하지 않은 경우, 가상 컴퓨터 디스크 데이터를 SSD가 아닌 하드 디스크 드라이브(HDD)에 저자하는 표준 저장소를 사용하여 비용을 절약할 수 있습니다.

Azure 프리미엄 저장소를 시작하려면 방문 [무료로 시작 하기](https://azure.microsoft.com/pricing/free-trial/) 페이지를 방문하세요. 기존 가상 컴퓨터를 프리미엄 저장소로 마이그레이션하는 것과 관련된 정보는 [Azure 프리미엄 저장소로 마이그레이션](storage-migration-to-premium-storage.md)을 참조하세요.

>[AZURE.NOTE] 프리미엄 저장소는 현재 일부 지역에서 지원됩니다. 사용할 수 있는 지역 목록은 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요.

## 프리미엄 저장소 기능

**프리미엄 저장소 디스크**: Azure 프리미엄 저장소는 프리미엄 저장소 지원 Azure VM(DS, DSv2, GS 또는 Fs 시리즈)에 연결할 수 있는 VM 디스크를 지원합니다. 프리미엄 저장소를 사용할 때 P10(128GiB), P20(512GiB), P30(1024GiB)의 세 가지 디스크 크기 중에 선택할 수 있으며, 각각 성능 사양이 다릅니다. 응용 프로그램 요구 사항에 따라 이러한 디스크 중 하나 이상을 프리미엄 저장소 지원 VM에 연결할 수 있습니다. 사양에 대한 자세한 내용은 [프리미엄 저장소 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets)에 대한 다음 섹션에서 설명하겠습니다.

**프리미엄 페이지 Blob**: 프리미엄 저장소는 Virtual VM(가상 컴퓨터)에 대한 영구 디스크를 보존하는 데 사용되는 Azure 페이지 Blob를 지원합니다. 현재 프리미엄 저장소는 Azure Block Blobs, Azure Append Blobs, Azure Files, Azure Tables 또는 Azure Queues를 지원하지 않습니다. 프리미엄 저장소 계정에 있는 다른 모든 개체는 페이지 Blob이 되고 지원되는 프로비전 크기 중 하나로 맞춥니다. 따라서 프리미엄 저장소 계정은 작은 Blob을 저장하기 위한 저장소 계정이 아닙니다.

**프리미엄 저장소 계정**: 프리미엄 저장소를 사용하려면 프리미엄 저장소 계정을 만들어야 합니다. [Azure 포털](https://portal.azure.com)을 사용하려는 경우 "프리미엄" 및 "LRS(로컬 중복 저장소)"를 복제 옵션으로 지정하여 프리미엄 저장소 계정을 만들 수 있습니다. 또한 [저장소 REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) 버전 2014-02-14 이상, [서비스 관리 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 버전 2014-10-01 이상(클래식 배포), [Azure 저장소 리소스 공급자 REST API 참조](http://msdn.microsoft.com/library/azure/mt163683.aspx)(Resource Manager 배포) 및 [Azure PowerShell](../powershell-install-configure.md) 버전 0.8.10 이상을 사용해 유형을 “Premium\_LRS”로 지정하여 프리미엄 저장소 계정을 만들 수도 있습니다. [프리미엄 저장소 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets)에 대한 다음 섹션에서 프리미엄 저장소 계정 제한에 대해 자세히 알아보세요.

**프리미엄 로컬 중복 저장소**: 프리미엄 저장소 계정은 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하며, 단일 지역 내에 데이터 복사본 3개를 유지합니다. 프리미엄 저장소 사용 시 지역에서 복제와 관련된 고려 사항은 이 문서의 [Blob 스냅숏 생성 및 복사](#snapshots-and-copy-blob)를 참조하세요.

Azure는 저장소 계정을 운영 체제(OS) 및 데이터 디스크의 컨테이너로 사용합니다. Azure DS, DSv2, GS 또는 Fs VM을 만들고 Azure 프리미엄 저장소 계정을 선택하는 경우 운영 체제와 데이터 디스크가 해당 저장소 계정에 저장됩니다.

다음 두 가지 방법 중 하나로 디스크용 프리미엄 저장소를 사용할 수 있습니다.
- 먼저, 새 프리미엄 저장소 계정을 만듭니다. 다음으로, 새 DS, DSv2, GS 또는 Fs VM을 만들 때 저장소 구성 설정에서 프리미엄 저장소 계정을 선택합니다. 또는
- DS, DSv2, GS 또는 Fs VMS VM을 만들 때 저장소 구성 설정에서 새 프리미엄 저장소 계정을 만들거나 Azure 포털이 기본 프리미엄 저장소 계정을 만들게 합니다.

단계별 지침은 이 문서의 뒷부분에 나오는 [빠른 시작](#quick-start)을 참조하세요.

>[AZURE.NOTE] 프리미엄 저장소 계정은 사용자 지정 도메인 이름에 매핑할 수 없습니다.

## 프리미엄 저장소 지원 VM

프리미엄 저장소는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 Fs 시리즈 Azure VM(가상 컴퓨터)을 지원합니다. 프리미엄 저장소 지원 VM에서 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. 하지만 프리미엄 저장소와 호환되지 않는 VM 시리즈에서는 프리미엄 저장소 디스크를 사용할 수 없습니다.

사용 가능한 Azure VM 유형 및 크기에 대한 자세한 내용은 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요. Linux VM용 VM 유형 및 크기에 대한 자세한 내용은 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-linux-sizes.md)를 참조하세요.

다음은 DS, DSv2, GS 및 Fs 시리즈 VM의 일부 기능입니다.

**클라우드 서비스**: DS 시리즈 VM만 포함하는 클라우드 서비스에 DS 시리즈 VM을 추가할 수 있습니다. DS 시리즈가 아닌 VM을 포함하는 기존 클라우드 서비스에 DS 시리즈 VM을 추가하지 마세요. DS 시리즈 VM만 실행하는 새 클라우드 서비스에 기존 VHD를 마이그레이션할 수 있습니다. DS 시리즈 VM을 호스트하는 새 클라우드 서비스에 동일한 VIP(가상 IP 주소)를 유지하려는 경우, [예약된 IP 주소](../virtual-network/virtual-networks-instance-level-public-ip.md)를 사용합니다. GS 시리즈 VM은 G 시리즈 VM만을 실행 중인 기존 클라우드 서비스에 추가될 수 있습니다.

**운영 체제 디스크**: 표준 저장소 계정 또는 프리미엄 저장소 계정에서 호스팅된 운영 체제(OS) 디스크를 사용하도록 프리미엄 저장소 지원 Azure 가상 컴퓨터를 구성할 수 있습니다. 최상의 환경을 위해 OS 디스크 기반 프리미엄 저장소를 사용하는 것이 좋습니다.

**데이터 디스크**: 동일한 프리미엄 저장소 지원 VM에 프리미엄 및 표준 저장소 디스크를 모두 사용할 수 있습니다. 프리미엄 저장소를 사용하여 프리미엄 저장소 지원 VM을 프로비전할 수 있으며 여러 영구 데이터 디스크를 VM에 연결할 수 있습니다. 필요한 경우, 볼륨의 성능과 용량을 늘리도록 디스크에 걸쳐 스트라이핑 할 수 있습니다.

> [AZURE.NOTE] [저장소 공간](http://technet.microsoft.com/library/hh831739.aspx)을 사용하여 프리미엄 저장소 데이터 디스크를 스트라이프하는 경우, 사용되는 각 디스크에 대해 하나의 열로 구성해야 합니다. 그렇지 않은 경우, 디스크에서의 고르지 못한 트래픽 분배로 스트라이프 볼륨의 전반적인 성능이 예상보다 저하될 수 있습니다. 기본적으로 서버 관리자 사용자 인터페이스(UI)를 사용하면 최대 8개의 디스크를 열로 설정할 수 있습니다. 하지만 디스크가 8개 이상인 경우, 볼륨을 만들고 열 수를 수동으로 지정하려면 PowerShell을 사용해야 합니다. 그렇지 않은 경우 서버 관리자 UI는 더 많은 디스크가 있더라도 8개의 열을 사용하여 계속합니다. 예를 들어 단일 스트라이프 세트에 32개의 디스크가 있다면 32개의 열을 지정해야 합니다. [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet의 *NumberOfColumns* 매개 변수를 사용하여 가상 디스크에서 사용되는 열 수를 지정할 수 있습니다. 자세한 내용은 [저장소 공간 개요](http://technet.microsoft.com/library/hh831739.aspx) 및 [저장소 공간 질문과 대답](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)을 참조하세요.

**캐시**: 프리미엄 저장소 지원 VM은 기본 프리미엄 저장소 디스크 성능을 초과하는 높은 처리량 및 대기 시간을 얻을 수 있는 고유의 캐싱 기능이 있습니다. 프리미엄 저장소 디스크의 디스크 캐싱 정책을 ReadOnly, ReadWrite 또는 None으로 구성할 수 있습니다. 기본 디스크 캐싱 정책은 모든 프리미엄 데이터 디스크에 대해서는 ReadOnly이고 운영 체제 디스크에 대해서는 ReadWrite입니다. 최적의 응용 프로그램 성능을 얻으려면 올바른 구성 설정을 사용해야 합니다. 예를 들어 SQL Server 데이터 파일 같은 많은 읽기 또는 읽기 전용 데이터 디스크의 경우에는 디스크 캐싱 정책을 "ReadOnly"로 설정하세요. SQL Server 로그 파일처럼 많은 쓰기 또는 쓰기 전용 데이터 디스크의 경우에는 디스크 캐싱 정책을 "None"으로 설정하세요. [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)에서 프리미엄 저장소로 디자인을 최적화하는 자세한 방법을 알아보세요.

**분석**: 프리미엄 저장소 계정의 디스크를 사용하여 VM 성능을 분석하려면 Azure 포털에서 Azure VM 진단을 활성화하세요. 자세한 내용은 [Azure 진단 확장을 사용한 Microsoft Azure 가상 컴퓨터 모니터링](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)을 참조하세요. 디스크 성능을 보려면 Windows VM용 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 및 Linux VM용 [IOSTAT](http://linux.die.net/man/1/iostat) 같은 운영 체제 기반 도구를 사용하세요.

**VM 크기 제한 및 성능**: 각 프리미엄 저장소 지원 VM 크기는 규모 제한이 있으며 IOPS, 대역폭 및 VM당 연결할 수 있는 디스크 수에 대한 성능 사양이 있습니다. 프리미엄 저장소 지원 VM과 함께 프리미엄 저장소 디스크를 사용할 때 VM에 디스크 트래픽을 운용할 수 있는 IOPS 및 대역폭이 충분한지 확인하세요. 예를 들어, STANDARD\_DS1 VM에는 프리미엄 저장소 디스크 트래픽에 사용할 수 있는 초당 32MB의 전용 대역폭이 있습니다. P10 프리미엄 저장소 디스크는 초당 100MB 대역폭을 제공할 수 있습니다. 이 VM에 연결된 P10 프리미엄 저장소 디스크는 초당 최대 32MB로만 이동할 수 있으며 P10 디스크가 제공할 수 있는 초당 최대 100MB는 이동할 수 없습니다.

현재, DS 계열에서 가장 큰 VM은 STANDARD\_DS14 이며 모든 디스크에서 초당 최대 512MB를 제공할 수 있습니다. GS 시리즈에서 가장 큰 VM은 STANDARD\_GS5 이며 모든 디스크에서 초당 최대 2000MB를 제공할 수 있습니다. 이러한 한도는 캐시 적중 수 및 네트워크 트래픽을 포함하지 않는 디스크 트래픽에만 단독으로 적용됩니다. VM 네트워크 트래픽에 사용할 수 있는 별도 대역폭이 있으며, 프리미엄 저장소 디스크 전용 대역폭과는 다릅니다.

프리미엄 저장소 지원 VM에 대한 최대 IOPS 및 처리량(대역폭)에 대한 최신 정보는 [Windows VM 크기](../virtual-machines/virtual-machines-windows-sizes.md) 또는 [Linux VM 크기](../virtual-machines/virtual-machines-linux-sizes.md)를 참조하세요.

프리미엄 저장소 디스크와 해당 IOPS 및 처리량 한도에 대한 자세한 내용은 이 문서에서 [프리미엄 저장소 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets) 섹션의 테이블을 참조하세요.

## 프리미엄 저장소 확장성 및 성능 목표

이 섹션에서는 프리미엄 저장소 사용 시 고려해야 하는 모든 확장성 및 성능 목표에 설명합니다.

### 프리미엄 저장소 계정 한도

프리미엄 저장소 계정은 다음과 같은 확장성 목표가 있습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>총 계정 용량</strong></td>
	<td><strong>로컬 중복 저장소 계정의 총 대역폭</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>디스크 용량: 35TB</li>
       <li type=round>스냅숏 용량: 10TB</li>
    </ul>
	</td>
	<td>인바운드+아웃바운드에 대해 초당 최대 50기가비트</td>
</tr>
</tbody>
</table>

- 인바운드는 저장소 계정으로 전송되는 모든 데이터(요청)를 가리킵니다.
- 아웃바운드는 저장소 계정에서 수신되는 모든 데이터(응답)를 가리킵니다.

자세한 내용은 [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

응용 프로그램의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 응용 프로그램을 빌드하고 데이터를 이러한 저장소 계정에 분할합니다. 예를 들어 많은 VM에 51TB(테라바이트) 디스크를 연결하려는 경우 35TB가 단일 프리미엄 저장소 계정의 한도이므로 두 개의 저장소 계정에 분산합니다. 단일 프리미엄 저장소 계정에 35TB 이상의 프로비전된 디스크가 없어야 합니다.

### 프리미엄 저장소 디스크 제한

프리미엄 저장소 계정에 대해 디스크를 프로비전할 때 사용할 수 있는 IOPS(초당 입/출력 작업 수) 및 처리량(대역폭)은 디스크 크기에 따라 달라집니다. 현재 프리미엄 저장소 디스크에는 P10, P20 및 P30의 세 가지 유형이 있습니다. 각 디스크에는 다음 표에 지정된 대로 특정 IOPS 및 처리량 한도가 있습니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>프리미엄 저장소 디스크 유형</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>디스크 크기</strong></td>
	<td>128GiB</td>
	<td>512GiB</td>
	<td>1024GiB(1TB)</td>
</tr>
<tr>
	<td><strong>디스크당 IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>디스크당 처리량</strong></td>
	<td>초당 100MB </td>
	<td>초당 150MB </td>
	<td>초당 200MB </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] 앞서 이 문서의 [프리미엄 저장소 지원 VM](#ds-dsv2-and-gs-series-vms) 섹션에서 설명한 대로 디스크 트래픽을 운용하기 위해 VM에서 사용할 수 있는 충분한 대역폭이 있는지 확인합니다. 충분하지 않은 경우, 디스크 처리량 및 IOPS가 이전 표에서 언급된 디스크 한도보다는 VM 한도에 기반한 값보다 더 낮게 제한됩니다.

다음은 프리미엄 저장소 확장성 및 성능 목표에 대해 알고 계셔야 하는 중요한 사항입니다.

- **프로비전된 용량 및 성능**: 프리미엄 저장소 디스크를 프로비전하면 표준 저장소와는 다르게, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P30 디스크를 만들면 Azure가 해당 디스크에 저장소 용량 1024GB, IOPS 5000, 초당 처리량 200MB를 프로비전합니다. 응용 프로그램에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다.

- **디스크 크기**: Azure는 테이블에 지정된 대로 디스크 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 예를 들어 크기가 100GiB인 디스크는 P10 옵션으로 분류되며 초당 최대 500개 IO 단위를 초당 최대 100MB 처리량으로 수행할 수 있습니다. 마찬가지로, 크기가 400GiB인 디스크는 P20 옵션으로 분류되며 초당 최대 2300개 IO 단위를 초당 최대 150MB 처리량으로 수행할 수 있습니다.

	> [AZURE.NOTE] 기존 디스크의 크기를 쉽게 확대할 수 있습니다. 예를 들어, 30GB 디스크의 크기 128GB 또는 1TB로 확대하려는 경우입니다. 또는 더 많은 용량 또는 더 많은 IOPS 및 처리량이 필요하기 때문에 P20 디스크에서 P30 디스크로 변환하려는 경우입니다. "-ResizedSizeInGB" 속성과 함께 "Update-AzureDisk" PowerShell commandlet를 사용하여 디스크를 확대할 수 있습니다. 이 작업 수행을 위해 디스크를 VM에서 분리해야 하거나 VM을 중단해야 합니다.

- **IO 크기**: I/O(입/출력) 단위 크기는 256KB입니다. 전송되는 데이터가 256KB 미만일 경우 하나의 I/O 단위로 간주됩니다. 더 큰 I/O 크기는 256KB 크기의 여러 I/O로 계산됩니다. 예를 들어 1100KB I/O는 I/O 단위 5개로 계산됩니다.

- **처리량**: 처리량 한도에는 디스크에 쓰기뿐 아니라 캐시에서 제공되지 않은 디스크에서 읽기도 포함됩니다. 예를 들어 P10 디스크는 디스크당 초당 처리량이 100MB입니다. 다음은 P10 디스크의 유효한 처리량에 대한 예입니다.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td><strong>Max Throughput per P10 disk</strong></td>
<td><strong>Non-cache Reads from disk</strong></td>
<td><strong>Non-cache Writes to disk</strong></td>
</tr>
<tr>
<td>100 MB per sec</td>
<td>100 MB per sec</td>
<td>0</td>
</tr>
<tr>
<td>100 MB per sec</td>
<td>0</td>
<td>100 MB per sec</td>
</tr>
<tr>
<td>초당 100MB </td>
<td>60 MB per second </td>
<td>초당 40MB </td>
</tr>
</tbody>
</table>

- **캐시 적중 수**: 캐시 적중 수는 디스크의 할당된 IOPS/처리량으로 제한되지 않습니다. 예를 들어 프리미엄 저장소 지원 VM에서 캐시가 ReadOnly로 설정된 데이터 디스크를 사용하는 경우 캐시에서 제공하는 읽기는 프리미엄 저장소 디스크 제한의 대상이 아닙니다. 따라서 워크로드가 대부분 읽기인 경우 디스크에서 상당히 높은 처리량을 얻을 수 있습니다. 캐시는 VM 크기에 기반한 VM 단계에서 별도의 IOPS/처리량으로 제한됩니다. DS 시리즈 VM은 대략 캐시 및 로컬 SSD IO에 대해 코어당 4000 IOPS 및 33MB/초여야 합니다. GS 시리즈 VM의 캐시 및 로컬 SSD IO에 대한 제한은 코어당 5000 IOPS 및 50MB/초입니다.

## 제한
응용 프로그램의 IOPS 또는 처리량이 프리미엄 저장소 디스크에 대한 할당된 한도를 초과하는 경우 또는 VM에서 모든 디스크에 전체 디스크 트래픽이 해당 VM에 사용할 수 있는 디스크 대역폭 한도를 초과하는 경우 제한이 보일 수 있습니다. 제한을 방지하려면 프로비전한 디스크의 확장성 및 성능 목표에 따라 및 VM에 사용할 수 있는 디스크 대역폭에 따라 디스크에 대해 보류 중인 I/O 요청 수를 제한하는 것이 좋습니다.

응용 프로그램이 제한을 방지하도록 설계된 경우 가장 낮은 대기 시간을 얻을 수 있습니다. 반면, 디스크에 대해 보류 중인 I/O 요청 수가 너무 작으면 응용 프로그램이 디스크에서 사용할 수 있는 최대 IOPS 및 처리량 수준을 활용할 수 없습니다.

다음 예제에서는 제한 수준을 계산하는 방법을 보여 줍니다. 모든 계산은 256KB의 I/O 단위 크기를 기반으로 합니다.

### 예제 1:
응용 프로그램이 P10 디스크에서 초당 16KB 크기의 495개 I/O를 수행했습니다. 이는 IOPS(초당 495 I/O 단위)로 계산됩니다. 동일한 초에 2MB I/O를 시도하면 총 I/O 단위 수는 495+8이 됩니다. 이는 I/O 단위 크기가 256KB일 경우 2MB I/O에서 2048KB/256KB = 8개 I/O 단위가 나오기 때문입니다. 495+8의 합계가 디스크 한도인 500개 IOPS를 초과하므로 제한이 발생합니다.

### 예 2:
응용 프로그램이 P10 디스크에서 256KB 크기의 400개 I/O를 수행했습니다. 소비되는 총 대역폭은 (400*256)/1024=100MB/초입니다. P10 디스크의 처리량 제한은 초당 100MB입니다. 응용 프로그램이 해당 초에 추가 I/O를 수행하려고 하면 할당된 한도를 초과하므로 제한됩니다.

### 예 3:
연결된 두 P30 디스크와 DS4 VM이 있습니다. 각 P30 디스크는 처리량이 초당 200MB입니다. 하지만 DS4 VM은 초당 256MB의 총 디스크 대역폭 용량입니다. 따라서 동시에 이 DS4 VM에서 최대 처리량으로 연결된 디스크를 운용할 수 없습니다. 이를 해결하려면 한 디스크에서는 초당 200MB, 다른 디스크에서는 초당 56MB의 트래픽을 유지할 수 있습니다. 디스크 트래픽의 합계는 초당 256MB 이상으로 이동하며 해당 트래픽이 제한됩니다.

>[AZURE.NOTE] 디스크 트래픽이 대체로 작은 I/O 크기로 구성된 경우 응용 프로그램이 처리량 한도 전에 IOPS 한도에 도달할 가능성이 큽니다. 반면, 디스크 트래픽이 대체로 큰 I/O 크기로 구성된 경우에는 응용 프로그램이 IOPS 한도 대신 처리량 한도에 도달할 가능성이 큽니다. 최적 I/O 크기를 사용하고 디스크에 대해 보류 중인 I/O 요청 수를 제한하여 응용 프로그램의 IOPS 및 처리량을 최대화할 수 있습니다.

프리미엄 저장소를 사용한 고성능 설계에 대한 자세한 내용은 [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)을 참조하세요.

## Blob 스냅숏 생성 및 복사
표준 저장소 사용 시 스냅숏을 만드는 것과 동일한 방식으로 프리미엄 저장소에 대한 스냅숏을 만들 수 있습니다. 프리미엄 저장소는 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하므로 스냅숏을 만든 후 지역 중복 표준 저장소 계정에 이러한 스냅숏을 복사하는 것이 좋습니다. 자세한 내용은 [Azure 저장소 중복 옵션](storage-redundancy.md)을 참조하세요.

디스크가 VM에 연결되어 있을 경우 디스크를 지원하는 페이지 Blob에 대해 특정 API 작업을 수행할 수 없습니다. 예를 들어 디스크가 VM에 연결되어 있는 한 해당 Blob에 대해 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx) 작업을 수행할 수 없습니다. 대신, 먼저 [Blob 스냅숏 생성](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API 메서드를 사용하여 해당 Blob의 스냅숏을 만든 후 스냅숏의 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 수행하여 연결된 디스크를 복사합니다. 또는 디스크를 분리한 후 기본 Blob에 대해 필요한 작업을 수행할 수 있습니다.

프리미엄 저장소 Blob 스냅숏에 다음 제한이 적용됩니다.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>프리미엄 저장소 제한</strong></td>
	<td><strong>값</strong></td>
</tr>
<tr>
	<td>Blob당 최대 스냅숏 수</td>
	<td>100</td>
</tr>
<tr>
	<td>스냅숏의 저장소 계정 용량(스냅숏에만 데이터를 포함하고 기본 Blob에는 데이터를 포함하지 않음)</td>
	<td>10TB</td>
</tr>
<tr>
	<td>연속 스냅숏 사이의 최소 시간</td>
	<td>10분</td>
</tr>
</tbody>
</table>

스냅숏의 지역 중복 복사본을 유지하려면 AzCopy 또는 Blob 복사를 사용하여 프리미엄 저장소 계정에서 지역 중복 표준 저장소 계정으로 스냅숏을 복사할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md) 및 [Blob 복사](http://msdn.microsoft.com/library/azure/dd894037.aspx)를 참조하세요.

프리미엄 저장소 계정에서 페이지 Blob에 대한 REST 작업을 수행하는 방법에 대한 자세한 내용은 MSDN 라이브러리에서 [Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)을 참조하세요.

## 프리미엄 저장소와 Linux VM 사용
프리미엄 저장소에서 Linux VM을 구성하기 위한 중요 지침은 아래를 참조하십시오.

- “ReadOnly(읽기 전용)” 또는 “None(없음)”으로 캐시 설정을 한 모든 프리미엄 저장소 디스크의 경우, 프리미엄 저장소에 대한 확장성 목표를 수행하기 위해 파일 시스템을 탑재하는 동안 “barrier(장벽)”을 사용하지 않도록 설정해야 합니다. 프리미엄 저장소 백업 디스크에 쓰기는 이러한 캐시 설정에 대해 내구성이 있기 때문에 이 시나리오에 대한 장벽이 필요하지 않습니다. 쓰기 요청이 성공적으로 완료되면 데이터는 영구 저장소에 작성됩니다. 파일 시스템에 따라 “barrier(장벽)”를 사용하지 않도록 설정하는 방법은 다음 메서드를 사용하십시오.
	- **reiserFS**를 사용하는 경우 탑재 옵션 “barrier=none”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=flush” 사용)
	- **ext3/ext4**를 사용하는 경우 탑재 옵션 “barrier=0”을 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier=1” 사용)
	- **XFS**를 사용하는 경우 탑재 옵션 “nobarrier”를 사용하여 장벽을 사용하지 않도록 설정(장벽 사용의 경우 “barrier” 사용)

- “ReadWrite”으로 캐시가 설정된 프리미엄 저장소 디스크의 경우 쓰기 내구성을 위해 장벽이 설정되어야 합니다.
- 볼륨 레이블의 경우 VM을 다시 부팅한 후 유지하려면 디스크에 UUID 참조로 /etc/fstab을 업데이트해야 합니다. 또한 [Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법](../virtual-machines/virtual-machines-linux-classic-attach-disk.md)을 참조하세요.

다음은 프리미엄 저장소로 유효성을 검사한 Linux 배포판입니다. 프리미엄 저장소 사용 시 더 나은 성능 및 확장성을 위해 이러한 버전 이상으로 VM을 업그레이드하는 것이 좋습니다. 또한 버전 중 일부는 최신 LIS(Microsoft Azure 용 Linux Integration Services v4.0)가 필요합니다. 다운로드 및 설치를 위해 아래 제공된 링크를 따르십시오. 추가 유효성 검사가 완료되면 목록에 대한 자세한 이미지 추가가 계속됩니다. 유효성 검사는 이러한 이미지에 따라 다른 성능을 보여주었으며 해당 이미지의 워크로드 특성 및 설정에 따라서도 달라집니다. 다른 종류의 워크로드에 대해 서로 다른 이미지가 조정됩니다.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>배포</strong></td>
	<td><strong>버전</strong></td>
	<td><strong>지원되는 커널</strong></td>
	<td><strong>세부 정보</strong></td>
</tr>
<tr>
	<td rowspan="2"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110+</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-ko-KR-30GB</td>
</tr>
<tr>
	<td>14.04+</td>
	<td>3.13.0-44.73+</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-ko-KR-30GB</td>
</tr>
<tr>
	<td><strong>Debian</strong></td>
	<td>7.x, 8.x</td>
	<td>3.16.7-ckt4-1+</td>
    <td> </td>
</tr>
<tr>
	<td rowspan="2"><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1+</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td>SLES 11 SP4</td>
    <td>3.0.101-0.63.1+</td>
    <td> </td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0+</td>
	<td>3.18.4+</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 필요 </a> <br/>
		*아래 참조*
	</td>
</tr>
<tr>
	<td>7.1+</td>
	<td>3.10.0-229.1.2.el7+</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 권장 </a> <br/>
		*아래 참조*
	</td>
</tr>
<tr>
	<td><strong>RHEL</strong></td>
	<td>6.8+, 7.2+</td>
	<td> </td>
	<td></td>
</tr>
<tr>
	<td rowspan="3"><strong>Oracle</strong></td>
    <td>6.8+, 7.2+</td>
    <td> </td>
    <td> UEK4 또는 RHCK </td>

</tr>
<tr>
	<td>7.0-7.1</td>
	<td> </td>
	<td>UEK4 또는 RHCK(<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a> 포함)</td>
</tr>
<tr>
	<td>6.4-6.7</td>
	<td></td>
	<td>UEK4 또는 RHCK(<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a> 포함)</td>
</tr>
</tbody>
</table>


### Openlogic CentOS용 LIS 드라이버

OpenLogic CentOS VM을 실행하는 고객은 다음 명령을 실행하여 최신 드라이버를 설치해야 합니다.

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

새 드라이버를 활성화하려면 다시 부팅해야 합니다.

## 가격 책정 및 대금 청구
프리미엄 저장소를 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.
- 프리미엄 저장소 디스크/Blob 크기
- 프리미엄 저장소 스냅숏
- 아웃바운드 데이터 전송

**프리미엄 저장소 디스크/Blob 크기**: 프리미엄 저장소 디스크/Blob에 대한 청구는 프로비전된 디스크/Blob 크기에 따라 달라집니다. Azure는 [프리미엄 저장소 사용 시 확장성 및 성능 목표](#premium-storage-scalability-and-performance-targets) 섹션에 지정된 표에 따라 프로비전된 크기(반올림됨)를 가장 가까운 프리미엄 저장소 디스크 옵션에 매핑합니다. 프리미엄 저장소 계정에 저장된 모든 개체는 지원되는 프로비전 크기 중 하나로 매핑하며 이에 따라 요금이 청구됩니다. 따라서 작은 Blob 저장에는 프리미엄 저장소 계정을 사용하지 마세요. 프로비전된 디스크/Blob에 대한 청구는 프리미엄 저장소 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 P10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 P10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양이나 사용한 IOPS/처리량에 관계없이 적용됩니다.

**프리미엄 저장소 스냅숏**: 프리미엄 저장소의 스냅숏은 스냅숏이 사용한 추가 용량에 대해 청구됩니다. 스냅숏에 대한 자세한 내용은 [Blob의 스냅숏 만들기](http://msdn.microsoft.com/library/azure/hh488361.aspx)를 참조하세요.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/data-transfers/)(Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용에 대해 청구가 발생합니다.

프리미엄 저장소에 대한 가격 책정, 프리미엄 저장소 지원 VM에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/)
- [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/)

## 백업
Azure 백업을 사용하여 프리미엄 저장소를 사용하는 가상 컴퓨터를 백업할 수 있습니다. [자세한 내용](../backup/backup-azure-vms-first-look-arm.md).

## 빠른 시작

## 가상 컴퓨터 데이터 디스크에 대한 프리미엄 저장소 계정 만들기 및 사용

이 섹션에서는 Azure 포털, Azure PowerShell 및 Azure CLI를 사용하여 다음 시나리오를 시연하겠습니다.

- 프리미엄 저장소 계정을 만드는 방법
- 프리미엄 저장소를 사용할 때 가상 컴퓨터를 만들고 해당 가상 컴퓨터에 데이터 디스크를 연결하는 방법
- 가상 컴퓨터에 연결된 데이터 디스크의 디스크 캐싱 정책을 변경하는 방법

### Azure 포털을 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기

#### I. Azure 포털에서 프리미엄 저장소 계정 만들기

이 섹션에서는 Azure 포털을 사용하여 프리미엄 저장소 계정을 만드는 방법을 보여 줍니다.

1.	[Azure 포털](https://portal.azure.com)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 서비스를 확인하세요.

2. 허브 메뉴에서 **새로 만들기** -> **데이터 + 저장소** -> **저장소 계정**을 선택합니다.

3. 저장소 계정의 이름을 입력합니다.

	> [AZURE.NOTE] 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
	>  
	> 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 선택한 저장소 계정 이름이 이미 사용 중인 경우 Azure 포털에 표시됩니다.

4. 사용할 배포 모델을 **Resource Manager** 또는 **클래식**으로 지정합니다. **리소스 관리자**는 권장되는 배포 모델입니다. 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md)를 참조하세요.

5. 저장소 계정의 성능 계층을 **프리미엄**으로 지정합니다.

6. **LRS(로컬 중복 저장소)**는 프리미엄 저장소에서 사용할 수 있는 유일한 복제 옵션입니다. Azure 저장소 복제 옵션에 대한 자세한 내용은 아래의 [Azure 저장소 복제](storage-redundancy.md)를 참조하세요.

7. 새 저장소 계정을 만들려는 구독을 선택합니다.

8. 새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

9. 저장소 계정에 대한 지리적 위치를 선택합니다. [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services)를 참조하여 선택한 위치에 프리미엄 저장소가 제공되는지 확인할 수 있습니다.

10. **만들기**를 클릭하여 저장소 계정을 만들 수 있습니다.

#### II. Azure 포털을 통해 Azure 가상 컴퓨터 만들기

프리미엄 저장소를 사용하려면 프리미엄 저장소 지원 VM을 만들어야 합니다. [Azure 포털에서 Windows 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)의 단계에 따라 새 DS, DSv2, GS 또는 Fs 가상 컴퓨터를 만듭니다.

#### III. Azure 포털을 통해 프리미엄 저장소 데이터 디스크 연결

1. Azure 포털에서 새로운 또는 기존 DS, DSv2, GS 또는 Fs VM을 찾습니다.
2. VM **모든 설정**에서 **디스크**로 이동하여 **새 연결**을 클릭합니다.
3. 데이터 디스크 이름을 입력하고 **유형**으로 **프리미엄**을 선택합니다. 원하는 **크기** 및 **호스트 캐싱** 설정을 선택합니다.

	![프리미엄 디스크][Image1]

자세한 단계는 [Azure 포털에서 데이터 디스크를 연결하는 방법](../virtual-machines/virtual-machines-windows-attach-disk-portal.md)을 참조하세요.

#### IV. Azure 포털을 통해 디스크 캐싱 정책 변경

1. Azure 포털에서 새로운 또는 기존 DS, DSv2, GS 또는 Fs VM을 찾습니다.
2. VM 모든 설정에서 디스크로 이동하여 변경할 디스크를 클릭합니다.
3. 호스트 캐싱 옵션을 None, ReadOnly, ReadWrite 중에 원하는 값으로 변경합니다.

>[AZURE.WARNING] Azure 디스크의 캐시 설정을 변경하면 대상 디스크가 분리되었다가 다시 연결됩니다. 운영 체제 디스크인 경우 VM이 다시 시작됩니다. 디스크 캐시 설정을 변경하기 전에 이 중단의 영향을 받을 수 있는 모든 응용 프로그램/서비스를 중지합니다.

### Azure PowerShell을 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기

#### I. Azure PowerShell에서 프리미엄 저장소 계정 만들기

이 PowerShell 예제에서는 새 프리미엄 저장소 계정을 만들고 새 Azure 가상 컴퓨터에 해당 계정을 사용하는 데이터 디스크를 연결하는 방법을 보여 줍니다.

1. [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)에 제공된 단계에 따라 PowerShell 환경을 설정합니다.
2. PowerShell 콘솔을 시작하고, 구독에 연결한 후 콘솔 창에서 다음 PowerShell cmdlet을 실행합니다. 이 PowerShell 문에 표시된 대로 프리미엄 저장소 계정을 만드는 경우 **Type** 매개 변수를 **Premium\_LRS**로 지정해야 합니다.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Azure PowerShell을 통해 Azure 가상 컴퓨터 만들기

새 DS 시리즈 VM을 만든 후 콘솔 창에서 다음 PowerShell cmdlet을 실행하여 프리미엄 저장소를 만들도록 지정합니다. 동일한 단계를 사용하여 GS 시리즈 VM을 만들 수 있습니다. 명령에서 적절한 VM 크기를 지정합니다. 예: Standard\_GS2:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

#### III. Azure PowerShell을 통해 프리미엄 저장소 데이터 디스크 연결

VM에 추가 디스크 공간을 사용하려면 콘솔 창에서 다음 PowerShell cmdlet을 실행하여 생성 후 기존 프리미엄 저장소 지원 VM에 새 데이터 디스크를 연결합니다.

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Azure PowerShell을 통해 디스크 캐싱 정책 변경

디스크 캐싱 정책을 업데이트하려면 연결된 데이터 디스크의 LUN 번호에 주의해야 합니다. 다음 명령을 실행하여 LUN 번호 2에 연결된 데이터 디스크를 ReadOnly로 업데이트합니다.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

>[AZURE.WARNING] Azure 디스크의 캐시 설정을 변경하면 대상 디스크가 분리되었다가 다시 연결됩니다. 운영 체제 디스크인 경우 VM이 다시 시작됩니다. 디스크 캐시 설정을 변경하기 전에 이 중단의 영향을 받을 수 있는 모든 응용 프로그램/서비스를 중지합니다.

### Azure 명령줄 인터페이스를 통해 프리미엄 저장소를 사용하여 Azure 가상 컴퓨터 만들기

[Azure CLI(Azure 명령줄 인터페이스)](../xplat-cli-install.md)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. 다음 예제에서는 Azure CLI(버전 0.8.14 이상)를 사용하여 프리미엄 저장소 계정, 새 가상 컴퓨터를 만들고 프리미엄 저장소 계정에서 새 데이터 디스크를 연결하는 방법을 설명합니다.

#### I. Azure CLI를 통해 프리미엄 저장소 계정 만들기

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Azure CLI를 통해 DS 시리즈 가상 컴퓨터 만들기

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-KR-30GB" -u "myusername" -p "passwd@123"

가상 컴퓨터에 대한 정보 표시

	azure vm show premium-test-vm

#### III. Azure CLI를 통해 새 프리미엄 데이터 디스크 연결

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

새 데이터 디스크에 대한 정보 표시

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. 디스크 캐싱 정책 변경

Azure CLI를 사용하여 디스크 중 하나에서 캐시 정책을 변경하려면 다음 명령을 실행합니다.

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

캐싱 정책 옵션은 읽기 전용, 없음 또는 읽기/쓰기입니다. 더 많은 옵션은 다음 명령을 실행하여 도움말을 참조하세요.

		azure vm disk attach --help

>[AZURE.WARNING] Azure 디스크의 캐시 설정을 변경하면 대상 디스크가 분리되었다가 다시 연결됩니다. 운영 체제 디스크인 경우 VM이 다시 시작됩니다. 디스크 캐시 설정을 변경하기 전에 이 중단의 영향을 받을 수 있는 모든 응용 프로그램/서비스를 중지합니다.

## FAQ

1. **프리미엄 및 표준 데이터 디스크를 모두 프리미엄 저장소 지원 VM에 연결할 수 있나요?**

	예. 프리미엄 및 표준 데이터 디스크를 모두 프리미엄 저장소 지원 시리즈 VM에 연결할 수 있습니다.

2. **프리미엄 및 표준 데이터 디스크를 모두 D, Dv2, G 또는 F 시리즈 VM에 연결할 수 있나요?**

	아니요. 프리미엄 저장소 지원 시리즈가 아닌 모든 VM에는 표준 데이터 디스크만 연결할 수 있습니다.

3. **크기가 80GB인 기존 VHD로 프리미엄 데이터 디스크를 만들 경우 비용은 얼마가 드나요?**

	80GB VHD로 만든 프리미엄 데이터 디스크는 그 다음 프리미엄 디스크 크기인 P10으로 취급됩니다. P10 디스크 가격 책정에 따라 대금이 청구될 것입니다.

4. **프리미엄 저장소를 사용할 때 발생하는 트랜잭션 비용이 있나요?**

	특정 수의 IOPS 및 처리량이 프로비전되는 디스크 크기마다 고정 비용이 있습니다. 유일한 기타 비용은 아웃 바운드 대역폭 및 스냅숏 용량입니다(해당하는 경우). 자세한 내용은 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

5. **내 프리미엄 저장소 지원 시리즈 VM에 대한 부팅 진단은 어디에 저장할 수 있나요?**

	표준 저장소 계정을 만들어서 프리미엄 저장소 지원 시리즈 VM의 부팅 진단을 저장할 수 있습니다.

6. **디스크 캐시에서 얼마나 많은 IOPS 및 처리량을 얻을 수 있습니까?**

	DS 시리즈의 캐시 및 로컬 SSD에 대한 결합 제한은 코어당 4000 IOPS 및 코어당 초당 33MB입니다. GS 시리즈는 코어당 5000 IOPS 및 코어당 초당 50MB를 제공합니다.

7. **프리미엄 저장소 지원 시리즈 VM에서 로컬 SSD는 무엇인가요?**

	로컬 SSD는 프리미엄 저장소 지원 시리즈 VM에 포함되어 있는 임시 저장소입니다. 이 임시 저장소에 대한 추가 비용은 없습니다. 이 임시 저장소나 로컬 SSD는 Azure Blob 저장소에 보존되지 않으므로 응용 프로그램 데이터를 저장하는 용도로 사용하지 않는 것이 좋습니다.

8. **표준 저장소 계정을 프리미엄 저장소 계정으로 변환할 수 있나요?**

	아니요. 표준 저장소 계정을 프리미엄 저장소 계정으로 또는 그 반대로 변환하는 것은 불가능합니다. 원하는 유형으로 새 저장소 계정을 만들고, 해당하는 경우 새 저장소 계정으로 데이터를 복사해야 합니다.

9. **D 시리즈 VM을 DS 시리즈 VM으로 변환하려면 어떻게 해야 하나요?**

	마이그레이션 가이드 [Azure 프리미엄 저장소로 마이그레이션](storage-migration-to-premium-storage.md)을 참조하여 표준 저장소 계정을 사용하는 D 시리즈 VM에서 프리미엄 저장소 계정을 사용하는 DS 시리즈 VM으로 워크로드를 이동하세요.

## 다음 단계

Azure 프리미엄 저장소에 대한 자세한 내용은 다음 문서를 참조하세요.

### Azure 프리미엄 저장소를 사용하여 디자인 및 구현

- [프리미엄 저장소를 사용한 성능을 위한 디자인](storage-premium-storage-performance.md)
- [Azure 프리미엄 저장소와 함께 Blob 서비스 작업 사용](http://go.microsoft.com/fwlink/?LinkId=521969)

### 운영 가이드

- [Azure 프리미엄 저장소로 마이그레이션](storage-migration-to-premium-storage.md)

### 블로그 게시물

- [일반적으로 제공되는 Azure 프리미엄 저장소](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [GS 시리즈 발표: 공용 클라우드의 최대 VM에 프리미엄 저장소 지원 추가](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

[Image1]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0921_2016-->