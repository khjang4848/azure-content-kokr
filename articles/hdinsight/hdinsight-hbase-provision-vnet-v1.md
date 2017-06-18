<properties
	pageTitle="가상 네트워크에 HBase 클러스터 프로비전 | Microsoft Azure"
	description="Azure HDInsight에서 HBase 사용 시작 Azure 가상 네트워크에 HDInsight HBase 클러스터를 만드는 방법을 알아봅니다."
	keywords=""
	services="hdinsight,virtual-network"
	documentationCenter=""
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure 가상 네트워크에 HBase 클러스터 프로비전

[Azure 가상 네트워크][1]에서 Azure HDInsight HBase 클러스터를 만드는 방법에 대해 알아봅니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 Azure 클래식 포털을 사용합니다. 새 서비스를 만들 때 클래식 포털을 사용하지 않는 것이 좋습니다. Azure 포털의 장점에 대한 자세한 내용은 [Microsoft Azure 포털](https://azure.microsoft.com/features/azure-portal/)을 참조하세요.
>
> 또한 이 문서는 Azure PowerShell을 사용하는 방법에 관한 정보도 포함하고 있습니다. 제공된 코드 조각은 ASM(Azure 서비스 관리)을 사용하여 HDInsight와 작동하고 현재 __사용되지 않는__ 명령을 기반으로 작성되었습니다. 이러한 명령은 2017년 1월 1일에 제거됩니다.
>
>ARM(Azure Resource Manager)을 사용하는 Azure 리소스 PowerShell 코드 조각과 함께 Azure 포털을 사용하는 이 문서의 버전에 대해서는 [Azure 가상 네트워크에 HBase 클러스터 프로비전](hdinsight-hbase-provision-vnet.md)을 참조하세요.

가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다. 이점은 다음과 같습니다.

- 웹 응용 프로그램을 HBase 클러스터의 노드에 직접 연결하므로 HBase Java RPC(원격 프로시저 호출) API를 통해 통신할 수 있습니다.
- 트래픽이 여러 게이트웨이 및 부하 분산 장치를 통과하지 않도록 하여 성능을 향상시킵니다.
- 공용 끝점을 노출하지 않고 중요한 정보를 보다 안전한 방식으로 처리할 수 있습니다.

##필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요. 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요. Azure PowerShell 스크립트를 실행하려면 관리자로 Azure PowerShell을 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. [Set-executionpolicy cmdlet 사용][2]을 참조하세요.

	Azure PowerShell 스크립트를 실행하기 전에 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>


##가상 네트워크에 HBase 클러스터 프로비전

HBase 클러스터를 프로비전하기 전에 Azure 가상 네트워크가 있어야 합니다.

**Azure 클래식 포털을 사용하여 가상 네트워크를 만들려면**

1. [Azure 클래식 포털][azure-portal]에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭하고, **네트워크 서비스**, **가상 네트워크**, **빠른 생성**을 차례로 클릭합니다.
3. 다음 값을 입력하거나 선택합니다.

	- **이름** - 가상 네트워크의 이름입니다.
	- **주소 공간** - 클러스터의 모든 노드에 대한 주소를 제공하기에 충분히 큰 가상 네트워크의 주소 공간을 선택합니다. 그렇지 않으면 프로비전에 실패합니다. 이 자습서를 살펴볼 때 다음 세 가지 선택 사항 중 하나를 선택할 수 있습니다.
	- **최대 VM 수** - 최대 VM(가상 컴퓨터) 수 중 하나를 선택합니다. 이 값은 주소 공간에서 만들 수 있는 가능한 호스트(VM)의 수를 결정합니다. 이 자습서를 살펴보는 데에는 **4096 [CIDR: /20]**이면 충분합니다.
	- **위치** - 위치는 만들 HBase 클러스터와 동일해야 합니다.
	- **DNS 서버** - 이 자습서에서는 Azure에서 제공하는 내부 DNS(Domain Name System) 서버를 사용하므로 **없음**을 선택할 수 있습니다. 사용자 지정 DNS 서버가 포함된 고급 네트워킹 구성도 지원됩니다. 자세한 지침이 필요하면 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.
4. 오른쪽 아래에서 **가상 네트워크 만들기**를 클릭합니다. 새 가상 네트워크 이름이 목록에 나타납니다. 상태 열에 **생성됨**이 표시될 때까지 기다립니다.
5. 기본 창에서 방금 만든 가상 네트워크를 클릭합니다.
6. 페이지 위쪽에서 **대시보드**를 클릭합니다.
7. **간략 상태** 아래에서 가상 네트워크 ID를 적어 둡니다. HBase 클러스터를 프로비전할 때 필요합니다.
8. 페이지 위쪽에서 **구성**을 클릭합니다.
9. 페이지 맨 아래에서 기본 서브넷 이름은 **Subnet-1**입니다. 선택적으로 서브넷의 이름을 바꾸거나 HBase 클러스터의 새 서브넷을 추가할 수 있습니다. 서브넷 이름을 적어 둡니다. 클러스터를 프로비전할 때 필요합니다.
10. 클러스터에 사용할 서브넷의 **CIDR(주소 수)**을 확인합니다. 주소 수는 작업자 노드 수에 7(게이트웨이: 2, 헤드 노드: 2, Zookeeper: 3)을 더한 값보다 커야 합니다. 예를 들어 10개 노드 HBase 클러스터가 필요한 경우 서브넷의 주소 수는 17(10+7)보다 커야 합니다. 그렇지 않으면 배포에 실패합니다.
11. 서브넷 값을 업데이트했으면 페이지 맨 아래에서 **저장**을 클릭합니다.


**가상 네트워크에 DNS 서버 가상 컴퓨터를 추가하려면**

DNS 서버는 선택 사항이지만 반드시 필요한 경우도 있습니다. 절차는 [두 Azure 가상 네트워크 간의 DNS 구성][hdinsight-hbase-replication-dns]에 나와 있습니다. 기본적으로 다음 단계를 수행해야 합니다.

1. 가상 네트워크에 Azure 가상 컴퓨터 추가
2. 가상 컴퓨터의 고정 IP 주소 설정
3. 가상 컴퓨터에 DNS 서버 역할 추가
4. 가상 네트워크에 DNS 서버 할당


**클러스터에서 사용할 Azure 저장소 계정 및 Blob 저장소 컨테이너를 만들려면**

> [AZURE.NOTE] HDInsight 클러스터는 Azure Blob 저장소를 사용하여 데이터를 저장합니다. 자세한 내용은 [HDInsight에서 Hadoop와 함께 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. 저장소 계정과 Blob 저장소 컨테이너가 필요합니다. 저장소 계정 위치는 가상 네트워크 위치 및 클러스터 위치와 일치해야 합니다.

다른 HDInsight 클러스터와 마찬가지로 HBase 클러스터는 Azure 저장소 계정 및 Blob 저장소 컨테이너를 기본 파일 시스템으로 사용해야 합니다. 저장소 계정 위치는 가상 네트워크 위치 및 클러스터 위치와 일치해야 합니다. 자세한 내용은 [HDInsight에서 Hadoop와 함께 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요. HBase 클러스터를 프로비전할 때 새로 만들거나 기존 클러스터를 사용할 수 있는 옵션이 제공됩니다. 이 절차에서는 Azure 클래식 포털을 사용하여 저장소 계정 및 Blob 저장소 컨테이너를 만드는 방법을 보여 줍니다.

1. [Azure 클래식 포털][azure-portal]에 로그인합니다.
2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **저장소**를 차례로 가리킨 후 **빠른 생성**을 클릭합니다.

3. 다음 값을 입력하거나 선택합니다.

	- **URL** - 저장소 계정의 이름입니다.
	- **위치** - 저장소 계정의 위치입니다. 가상 네트워크 위치와 일치해야 합니다. 선호도 그룹은 지원되지 않습니다.
	- **복제** - 테스트 목적으로 **로컬 중복**을 사용하여 비용을 줄입니다.

4. **CREATE STORAGE ACCOUNT**를 클릭합니다. 저장소 목록에 새 저장소 계정이 표시됩니다.
5. 새 저장소 계정의 **상태**가 **온라인**으로 변경될 때까지 기다립니다.
6. 목록에서 새 저장소 계정을 클릭하여 선택합니다.
7. 페이지 맨 아래에서 **액세스 키 관리**를 클릭합니다.
8. 저장소 계정 이름 및 기본 액세스 키(또는 보조 액세스 키 - 두 키 중 하나가 작동)를 기록해 둡니다. 이 정보는 자습서의 뒷부분에서 필요합니다.
9. 페이지 맨 위에서 **컨테이너**를 클릭합니다.
10. 페이지 맨 아래에서 **추가**를 클릭합니다.
11. 컨테이너 이름을 입력합니다. 이 컨테이너는 HBase 클러스터의 기본 컨테이너로 사용됩니다. 기본적으로 기본 컨테이너 이름은 클러스터 이름과 일치합니다. **액세스** 필드를 **비공개**로 유지합니다.  
12. 확인 표시를 클릭하여 컨테이너를 만듭니다.

**Azure 클래식 포털을 사용하여 HBase 클러스터를 프로비전하려면**

> [AZURE.NOTE] Azure PowerShell을 사용하여 새 HBase 클러스터를 프로비전하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 HBase 클러스터 프로비전](#powershell)을 참조하세요.

1. [Azure 클래식 포털][azure-portal]에 로그인합니다.

2. 왼쪽 아래에서 **새로 만들기**를 클릭하고 **데이터 서비스**, **HDINSIGHT**를 차례로 가리킨 후 **사용자 지정 만들기**를 클릭합니다.

3. 클러스터 이름을 입력하고 HBase를 클러스터 유형으로 선택한 다음 Windows Server 2012 운영 체제, HDInsight 버전을 차례로 선택하고 오른쪽 단추를 클릭합니다.

	![HBase 클러스터에 대한 세부 정보 제공][img-provision-cluster-page1]


	> [AZURE.NOTE] HBase 클러스터의 경우 Windows Server만 OS 옵션으로 사용할 수 있습니다.

4. **클러스터 구성** 페이지에서 다음을 입력하거나 선택합니다.

	![HBase 클러스터에 대한 세부 정보 제공](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>데이터 노드</td><td>배포하려는 데이터 노드 수를 선택합니다. 테스트 목적으로 단일 노드 클러스터를 만듭니다. <br />클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 Azure 청구 지원 팀에 문의하세요.</td></tr>
		<tr><td>지역/가상 네트워크</td><td><p>지역 또는 Azure 가상 네트워크를 선택합니다(이미 만든 경우). 이 자습서에서는 앞에서 만든 네트워크를 선택한 다음 해당 서브넷을 선택합니다. 기본 이름은 <b>Subnet-1</b>입니다.</p></td></tr>
		<tr><td>헤드 노드 크기</td><td><p>헤드 노드에 대한 VM 크기를 선택합니다.</p></td></tr>
		<tr><td>데이터 노드 크기</td><td><p>데이터 노드에 대한 VM 크기를 선택합니다.</p></td></tr>
		<tr><td>Zookeeper 크기</td><td><p>Zookeeper 노드에 대한 VM 크기를 선택합니다.</p></td></tr>
	</table>

	>[AZURE.NOTE] 선택한 VM에 따라 비용이 달라질 수 있습니다. HDInsight에서는 클러스터 노드에 모든 표준 계층 VM을 사용합니다. VM 크기가 가격에 미치는 영향에 대한 자세한 내용은 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 가격</a>을 참조하세요.

	오른쪽 단추를 클릭합니다.

5. 이 클러스터에 사용할 Hadoop 사용자 이름 및 암호를 입력한 다음 오른쪽 단추를 클릭합니다.

	![Hadoop HDInsight 클러스터에 대한 저장소 계정 제공](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>HTTP 사용자 이름</td>
			<td>HDInsight 클러스터 사용자 이름 지정</td></tr>
		<tr><td>HTTP 암호/암호 확인</td>
			<td>HDInsight 클러스터 사용자 암호 지정</td></tr>
		<tr><td>클러스터에 대한 원격 데스크톱 활성화</td>
			<td>클러스터가 프로비전된 후 원격 데스크톱 사용자가 클러스터 노드에 원격으로 연결할 수 있는 사용자 이름, 암호 및 만료 날짜를 지정하려면 이 확인란을 선택합니다. 클러스터가 프로비전된 후 나중에 원격 데스크톱을 사용하도록 설정할 수도 있습니다. 지침은 <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP를 사용하여 HDInsight 클러스터에 연결</a>을 참조하세요.</td></tr>
	</table>

6. **저장소 계정** 페이지에서 다음 값을 제공합니다.

    ![Hadoop HDInsight 클러스터에 대한 저장소 계정 제공](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>저장소 계정</td>
			<td>HDInsight 클러스터의 기본 파일 시스템으로 사용할 Azure 저장소 계정 지정. 세 가지 옵션 중 하나를 선택할 수 있습니다.
			<ul>
				<li><strong>기존 저장소 사용</strong></li>
				<li><strong>새 저장소 만들기</strong></li>
				<li><strong>다른 구독의 저장소 사용</strong></li>
			</ul>
			</td></tr>
		<tr><td>계정 이름</td>
			<td><ul>
				<li>기존 저장소를 사용하도록 선택한 경우 <strong>계정 이름</strong>에 대해 기존 저장소 계정을 선택합니다. 드롭다운에는 클러스터를 프로비전하도록 선택한 것과 동일한 데이터 센터에 있는 저장소 계정만 표시됩니다.</li>
				<li><strong>새 저장소 만들기</strong> 또는 <strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우에는 저장소 계정 이름을 제공해야 합니다.</li>
			</ul></td></tr>
		<tr><td>계정 키</td>
			<td><strong>다른 구독의 저장소 사용</strong> 옵션을 선택한 경우 해당 저장소 계정의 계정 키를 지정합니다.</td></tr>
		<tr><td>기본 컨테이너</td>
			<td><p>HDInsight 클러스터의 기본 파일 시스템으로 사용할 저장소 계정의 기본 컨테이너를 지정합니다. <strong>저장소 계정</strong> 필드에 대해 <strong>기존 저장소 사용</strong>을 선택한 경우, 해당 계정에 기존 컨테이너가 없으면 기본적으로 클러스터 이름과 동일한 이름으로 컨테이너가 생성됩니다. 클러스터의 이름을 가진 컨테이너가 이미 있는 경우에는 컨테이너 이름에 시퀀스 번호가 추가됩니다. 예를 들어 mycontainer1, mycontainer2 등과 같습니다. 그렇지만 기존 저장소 계정에 지정한 클러스터 이름과는 다른 이름의 컨테이너가 들어 있으면 해당 컨테이너도 사용할 수 있습니다.</p>
	        <p>새 저장소를 만들거나 다른 Azure 구독의 저장소를 사용하도록 선택한 경우 기본 컨테이너 이름을 지정해야 합니다.</p>
	    </td></tr>
		<tr><td>추가 저장소 계정</td>
			<td>필요한 경우 클러스터의 추가 저장소 계정을 지정합니다. HDInsight는 여러 저장소 계정을 지원합니다. 클러스터에서 사용할 수 있는 추가 저장소 계정에는 한도가 없습니다. 하지만 Azure 클래식 포털을 사용하여 클러스터를 만드는 경우에는 UI 제약으로 인해 7개로 제한됩니다. 지정하는 각 추가 저장소 계정은 마법사에 계정 정보를 지정할 수 있는 <strong>저장소 계정</strong> 페이지를 더합니다. 예를 들어 위의 스크린샷에서는 선택된 추가 저장소 계정이 없으므로 추가 페이지가 마법사에 추가되지 않았습니다.</td></tr>
	</table>

	오른쪽 화살표를 클릭합니다.

7. **스크립트 작업** 페이지에서 오른쪽 아래에 있는 확인 표시를 선택합니다. 이 자습서에서는 사용자 지정 클러스터 설정이 필요 없으므로 **스크립트 작업 추가** 단추를 클릭하지 마세요.

	![스크립트 작업을 구성하여 HDInsight HBase 클러스터 사용자 지정][img-provision-cluster-page5]

	> [AZURE.NOTE] 이 페이지를 사용하여 설치하는 동안 클러스터를 사용자 지정할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

새 HBase 클러스터 사용을 시작하려는 경우 [HDInsight에서 Hadoop을 통해 HBase 사용 시작](hdinsight-hbase-tutorial-get-started.md)의 절차를 사용할 수 있습니다.

##HBase Java RPC API를 사용하여 가상 네트워크에 프로비전된 HBase 클러스터에 연결

1.	동일한 Azure 가상 네트워크와 동일한 서브넷에 IaaS(infrastructure as a service) 가상 컴퓨터를 프로비전합니다. 따라서 가상 컴퓨터와 HBase 클러스터가 둘 다 동일한 내부 DNS 서버를 사용하여 호스트 이름을 확인합니다. 이렇게 하려면 **갤러리에서** 옵션을 선택한 다음 데이터 센터 대신 가상 네트워크를 선택해야 합니다. 지침에 대해서는 [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)를 참조하세요. 작은 VM 크기의 표준 Windows Server 2012 이미지면 충분합니다.

2.	Java 응용 프로그램을 사용하여 HBase에 원격으로 연결할 때는 FQDN(정규화된 도메인 이름)을 사용해야 합니다. FQDN을 확인하려면 HBase 클러스터의 연결별 DNS 접미사를 가져와야 합니다. 이렇게 하려면 Curl을 사용하여 Ambari를 쿼리하거나 원격 데스크톱을 사용하여 클러스터에 연결합니다.

	* **Curl** - 다음 명령을 사용합니다.

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		반환되는 JSON(JavaScript Object Notation) 데이터에서 "host\_name" 항목을 찾습니다. 이 항목에는 클러스터의 노드에 대한 FQDN이 포함되어 있습니다. 예:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다. 예를 들면 mycluster.b1.cloudapp.net과 같습니다.

	* **Azure PowerShell** - 다음 Azure PowerShell 스크립트를 사용하여 DNS 접미사를 반환하는 데 사용할 수 있는 **Get-ClusterDetail** 함수를 등록합니다.

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Azure PowerShell 스크립트를 실행한 후 다음 명령을 사용하여 **Get-ClusterDetail** 함수를 통해 DNS 접미사를 반환합니다. 이 명령을 사용할 때는 HDInsight HBase 클러스터 이름, 관리자 이름 및 관리자 암호를 지정합니다.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		그러면 DNS 접미사가 반환됩니다. 예를 들면 **yourclustername.b4.internal.cloudapp.net**과 같습니다.

	> [AZURE.NOTE] 원격 데스크톱을 사용하여 HBase 클러스터에 연결하고(헤드 노드에 연결됨) 명령 프롬프트에서 **ipconfig**를 실행하여 DNS 접미사를 가져올 수도 있습니다. RDP를 사용하도록 설정하고 RDP(원격 데스크톱 프로토콜)를 통해 클러스터에 연결하는 방법에 대한 지침은 [Azure 클래식 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리][hdinsight-admin-portal]를 참조하세요.
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

가상 컴퓨터가 HBase 클러스터와 통신할 수 있는지 확인하려면 가상 컴퓨터에서 `ping headnode0.<dns suffix>` 명령을 사용합니다. 예를 들면 ping headnode0.mycluster.b1.cloudapp.net을 사용합니다.

Java 응용 프로그램에서 이 정보를 사용하려는 경우 [Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성](hdinsight-hbase-build-java-maven.md)의 단계에 따라 응용 프로그램을 만들 수 있습니다. 응용 프로그램이 원격 HBase 서버에 연결하도록 하려면 이 예제의 **hbase-site.xml** 파일이 ZooKeeper의 FQDN을 사용하도록 수정합니다. 예:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] 자체 DNS 서버를 사용하는 방법을 포함한 Azure 가상 네트워크의 이름 확인에 대한 자세한 내용은 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

##Azure PowerShell을 사용하여 HBase 클러스터 프로비전

**Azure PowerShell을 사용하여 HBase 클러스터를 프로비전하려면**

1. Azure PowerShell ISE(통합 스크립팅 환경)를 엽니다.
2. 다음을 복사하여 스크립트 창에 붙여 넣습니다.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. **스크립트 실행**을 클릭하거나 **F5** 키를 누릅니다.
4. 클러스터의 유효성을 검사하기 위해 Azure 클래식 포털에서 클러스터를 확인하거나 맨 아래 창에서 다음 Azure PowerShell cmdlet을 실행할 수 있습니다.

	Get-AzureHDInsightCluster

##다음 단계

이 자습서에서는 HBase 클러스터를 프로비전하는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md)
- [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-provision-clusters.md)
- [HDInsight의 Hadoop에서 HBase 사용 시작](hdinsight-hbase-tutorial-get-started.md)
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석](hdinsight-hbase-analyze-twitter-sentiment.md)
- [가상 네트워크 개요][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://management.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "새 HBase 클러스터에 대한 프로비전 정보"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "스크립트 작업을 사용하여 HBase 클러스터 사용자 지정"

<!---HONumber=AcomDC_0914_2016-->