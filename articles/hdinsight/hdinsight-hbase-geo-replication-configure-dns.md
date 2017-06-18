<properties 
   pageTitle="두 Azure 가상 네트워크 간 DNS 구성 | Microsoft Azure" 
   description="VPN 연결을 구성하는 방법, 두 가상 네트워크 간의 도메인 이름 확인을 구성하는 방법 및 HBase 지역에서 복제를 구성하는 방법에 대해 알아봅니다." 
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
   ms.date="06/28/2016"
   ms.author="jgao"/>

# 두 Azure 가상 네트워크 간의 DNS 구성

> [AZURE.SELECTOR]
- [VPN 연결 구성](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [DNS 구성](hdinsight-hbase-geo-replication-configure-DNS.md)
- [HBase 복제 구성](hdinsight-hbase-geo-replication.md)


Azure 가상 네트워크에 DNS 서버를 추가하고 구성하여 가상 네트워크 내부 및 가상 네트워크 간에 이름 확인을 처리하는 방법에 대해 알아봅니다.

이 자습서는 HBase 지역에서 복제 만들기에 대한 [시리즈][hdinsight-hbase-geo-replication]의 두 번째 부분입니다.

- [두 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-geo-replication-vnet]
- 가상 네트워크에 대한 DNS 구성(이 자습서)
- [HBase 지역에서 복제 구성][hdinsight-hbase-geo-replication]


다음 다이어그램에서는 [두 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-geo-replication-vnet]에서 만든 두 가상 네트워크를 보여 줍니다.

![HDInsight HBase 복제 가상 네트워크 다이어그램][img-vnet-diagram]

##필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**.

	PowerShell 스크립트를 실행하기 전에 다음의 cmdlet을 사용하여 Azure 구독에 연결되어 있는지 확인합니다.

		Add-AzureAccount

	여러 Azure 구독이 있는 경우 다음 cmdlet을 사용하여 현재 구독을 설정합니다.

		Select-AzureSubscription <AzureSubscriptionName>
		
	[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **VPN으로 연결된 두 개의 Azure 가상 네트워크**. 자세한 내용은 [두 Azure 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-geo-replication-vnet]을 참조하세요.

>[AZURE.NOTE] Azure 서비스 이름과 가상 컴퓨터 이름은 고유해야 합니다. 이 자습서에서 사용되는 이름은 Contoso-[Azure Service/VM name]-[EU/US]입니다. 예를 들어 Contoso-VNet-EU는 북유럽 데이터 센터에 있는 Azure 가상 네트워크이고, Contoso-DNS-US는 미국 동부 데이터 센터에 있는 DNS 서버 VM입니다. 사용자 고유의 이름을 사용해야 합니다.
 
 
##DNS 서버로 사용할 Azure 가상 컴퓨터 만들기

**Contoso-VNet-EU 내에서 Contoso-DNS-EU라는 가상 컴퓨터를 만들려면**

1.	**새로 만들기** > **계산** > **가상 컴퓨터** > **갤러리에서**를 클릭합니다.
2.	**Windows Server 2012 R2 Datacenter**를 선택합니다.
3.	다음을 입력합니다.
	- **가상 컴퓨터 이름**: Contoso-DNS-EU
	- **새 사용자 이름**:
	- **새 암호**:
4.	다음을 입력합니다.
	- **클라우드 서비스**: 새 클라우드 서비스를 만듭니다.
	- **지역/선호도 그룹/가상 네트워크**:(Contoso-VNet-EU 선택)
	- **가상 네트워크 서브넷**: 서브넷-1
	- **저장소 계정**: 자동으로 생성된 저장소 계정 사용
	
		클라우드 서비스 이름은 가상 컴퓨터 이름과 같아야 합니다. 이 경우 Contoso-DNS-EU입니다. 이후 가상 컴퓨터의 경우, 동일한 클라우드 서비스를 사용하도록 선택할 수 있습니다. 동일한 클라우드 서비스 내의 모든 가상 컴퓨터는 동일한 가상 네트워크 및 도메인 접미사를 공유합니다.

		저장소 계정이 가상 컴퓨터 이미지 파일을 저장하는데 사용됩니다.
	- **끝점**:(아래로 스크롤하여 **DNS** 선택)

가상 컴퓨터를 만든 후 내부 IP 및 외부 IP를 확인합니다.

1.	가상 컴퓨터 이름 **Contoso-DNS-EU**를 클릭합니다.
2.	**대시보드**를 클릭합니다.
3.	다음 정보를 적어 둡니다.
	- 공용 가상 IP 주소
	- 내부 IP 주소


**Contoso-VNet-US 내에서 Contoso-DNS-US라는 가상 컴퓨터를 만들려면**

- 다음 값으로 같은 절차를 반복하여 가상 컴퓨터를 만듭니다.
	- 가상 컴퓨터 이름: Contoso-DNS-US
	- 지역/선호도 그룹/가상 네트워크: Contoso-VNET-US 선택
	- 가상 네트워크 서브넷: 서브넷-1
	- 저장소 계정: 자동으로 생성된 저장소 계정 사용
	- 끝점:(DNS 선택)

##두 가상 컴퓨터에 대한 고정 IP 주소 설정

DNS 서버에는 고정 IP 주소가 필요합니다. 이 단계는 Azure 클래식 포털에서 수행할 수 없습니다. Azure PowerShell을 사용합니다.

**두 가상 컴퓨터에 대한 고정 IP 주소를 구성하려면**

1. Windows PowerShell ISE를 엽니다.
2. 다음 cmdlet을 실행합니다.

		Add-AzureAccount
		Select-AzureSubscription [YourAzureSubscriptionName]
		
		Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
		Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

	ServiceName은 클라우드 서비스 이름입니다. DNS 서버는 클라우드 서비스의 첫 번째 가상 컴퓨터이므로 클라우드 서비스 이름은 가상 컴퓨터 이름과 동일합니다.

	ServiceName 및 Name을 사용자가 소유한 이름과 일치하도록 업데이트해야 할 수 있습니다.


##두 가상 컴퓨터에 DNS 서버 역할 추가

**Contoso-DNS-EU에 대한 DNS 서버 역할을 추가하려면**

1.	Azure 클래식 포털에서 왼쪽에 있는 **가상 컴퓨터**를 클릭합니다.
2.	**Contoso-DNS-EU**를 클릭합니다.
3.	위쪽에서 **대시보드**를 클릭합니다.
4.	아래쪽에서 **연결**을 클릭하고 지침에 따라 RDP를 통해 가상 컴퓨터에 연결합니다.
2.	RDP 세션 내에서 왼쪽 아래에 있는 Windows 단추를 클릭하여 시작 화면을 엽니다.
3.	**서버 관리자** 타일을 클릭합니다.
4.	**역할 및 기능 추가**를 클릭합니다.
5.	**다음**를 클릭합니다.
6.	**역할 기반 또는 기능 기반 설치**를 선택하고 **다음**을 클릭합니다.
7.	DNS 가상 컴퓨터(이미 강조 표시되어 있음)를 선택하고 **다음**을 클릭합니다.
8.	**DNS 서버**를 선택합니다.
9.	**기능 추가**를 클릭하고 **계속**을 클릭합니다.
10.	**다음**을 세 번 클릭한 후 **설치**를 클릭합니다.

**Contoso-DNS-US에 대한 DNS 서버 역할을 추가하려면**

- 같은 단계를 반복하여 **Contoso-DNS-US**에 DNS 역할을 추가합니다

##가상 네트워크에 DNS 서버 할당

**두 DNS 서버를 등록하려면**

1.	Azure 클래식 포털에서 **새로 만들기**, **네트워크 서비스**, **가상 네트워크**, **DNS 서버 등록**을 클릭합니다.
2.	다음을 입력합니다.
	- **이름**: Contoso-DNS-EU
	- **DNS 서버 IP 주소**: 10.1.0.4 - IP 주소는 DNS 서버 가상 컴퓨터 IP 주소와 일치해야 합니다.
	 
3.	프로세스를 반복하여 다음 설정으로 Contoso-DNS-US를 등록합니다.
	- **이름**: Contoso-DNS-US
	- **DNS 서버 IP 주소**: 10.2.0.4

**두 가상 네트워크에 두 DNS 서버를 할당하려면**

1.	클래식 포털의 왼쪽 창에서 **네트워크**를 클릭합니다.
2.	**Contoso-VNet-EU**를 클릭합니다.
3.	**구성**을 클릭합니다.
4.	**DNS 서버** 섹션에서 **Contoso-DNS-EU**를 선택합니다.
5.	페이지 아래쪽에서 **저장**을 클릭한 후 **예**를 클릭하여 확인합니다.
6.	프로세스를 반복하여 **Contoso-VNet-US** 가상 네트워크에 **Contoso-DNS-US** DNS 서버를 할당합니다.

DNS 서버 구성을 업데이트하려면 가상 네트워크에 배포된 모든 가상 컴퓨터를 다시 부팅해야 합니다.

**가상 컴퓨터를 다시 부팅하려면**

1. Azure 클래식 포털에서 왼쪽에 있는 **가상 컴퓨터**를 클릭합니다.
2. **Contoso-DNS-EU**를 클릭합니다.
3. 위쪽에서 **대시보드**를 클릭합니다.
4. 아래쪽에서 **다시 시작**을 클릭합니다.
5. 같은 단계를 반복하여 **Contoso-DNS-US**를 다시 부팅합니다.


##DNS 조건부 전달자 구성

각 가상 네트워크의 DNS 서버에서 해당 가상 네트워크 내 DNS 이름을 확인할 수 있습니다. 피어 가상 네트워크에서의 이름 확인을 위해 피어 DNS 서버를 가리키도록 조건부 전달자를 구성해야 합니다.

조건부 전달자를 구성하려면 두 DNS 서버의 도메인 접미사를 알아야 합니다. DNS 접미사는 가상 컴퓨터를 만들 때 사용한 클라우드 서비스 구성에 따라 다를 수 있습니다. 가상 네트워크에 사용되는 각 DNS 접미사에 대해 조건부 전달자를 추가해야 합니다.

**두 DNS 서버의 도메인 접미사를 찾으려면**

1. **Contoso-DNS-EU**로 RDP를 연결합니다.
2. Windows PowerShell 콘솔 또는 명령 프롬프트를 엽니다.
3. **ipconfig**를 실행하고 **연결별 DNS 접미사**를 적어 둡니다.
4. RDP 세션을 닫지 마세요. 계속 필요합니다.
5. 같은 단계를 반복하여 **Contoso-DNS-US**의 **연결별 DNS 접미사**를 확인합니다.


**DNS 전달자를 구성하려면**
 
1.	**Contoso-DNS-EU**에 대한 RDP 세션에서 왼쪽 아래에 있는 Windows 키를 클릭합니다.
2.	**관리 도구**를 클릭합니다.
3.	**DNS**를 클릭합니다.
4.	왼쪽 창에서 **DSN**, **Contoso-DNS-EU**를 확장합니다.
5.	다음 정보를 입력합니다.
	- **DNS 도메인**: Contoso-DNS-US의 DNS 접미사를 입력합니다. 예를 들면 Contoso-DNS-US.b5.internal.cloudapp.net과 같습니다.
	- **마스터 서버의 IP 주소**: Contoso-DNS-US의 IP 주소인 10.2.0.4를 입력합니다.
6.	**Enter** 키를 누르고 **확인**을 클릭합니다. 이제 Contoso-DNS-EU에서 Contoso-DNS-US의 IP 주소를 확인할 수 있습니다.
7.	다음 값으로 단계를 반복하여 Contoso-DNS-US 가상 컴퓨터의 DNS 서비스에 DNS 전달자를 추가합니다.
	- **DNS 도메인**: Contoso-DNS-EU의 DNS 접미사를 입력합니다.
	- **마스터 서버의 IP 주소**: Contoso-DNS-EU의 IP 주소인 10.2.0.4를 입력합니다.

##가상 네트워크에서 이름 확인 테스트

이제 가상 네트워크에서 이름 확인을 테스트할 수 있습니다. Ping은 기본적으로 방화벽에 의해 차단됩니다. nslookup을 사용하여 피어 네트워크에서 DNS 서버 가상 컴퓨터(FQDN을 사용해야 함)를 확인할 수 있습니다.


##다음 단계

이 자습서에서는 VPN 연결을 사용하여 가상 네트워크에서 이름 확인을 구성하는 방법에 대해 알아보았습니다. 시리즈의 다른 두 문서에서는 다음 내용을 다룹니다.

- [두 Azure 가상 네트워크 간의 VPN 연결 구성][hdinsight-hbase-geo-replication-vnet]
- [HBase 지역에서 복제 구성][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png

<!---HONumber=AcomDC_0914_2016-->