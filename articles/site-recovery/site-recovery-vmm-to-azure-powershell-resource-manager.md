<properties
	pageTitle="Azure Site Recovery 및 PowerShell을 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제(Resource Manager) | Microsoft Azure"
	description="Azure Site Recovery 및 PowerShell을 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제"
	services="site-recovery"
	documentationCenter=""
	authors="Rajani-Janaki-Ram"
	manager="rochakm"
	editor="raynew"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="rajanaki"/>

# PowerShell 및 Azure Resource Manager를 사용하여 Azure에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-vmm-to-azure.md)
- [PowerShell - ARM](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [클래식 포털](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - 클래식](site-recovery-deploy-with-powershell.md)



## 개요

Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

이 문서에서는 System Center VMM 클라우드의 Hyper-V 가상 컴퓨터를 Azure 저장소로 복제하도록 Azure Site Recovery를 설정할 때 수행해야 하는 일반적인 작업을 PowerShell을 사용하여 자동화하는 방법을 보여 줍니다.

이 문서는 시나리오의 필수 조건을 포함하고 있으며, 다음 내용을 보여 줍니다.

- 복구 서비스 자격 증명 모음을 설정하는 방법
- 원본 VMM 서버에 Azure Site Recovery 공급자 설치
- 자격 증명 모음에 서버를 등록하고, Azure 저장소 계정 추가
- Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트 설치
- VMM 클라우드에 대한 보호 설정 구성, 이 구성은 모든 보호되는 가상 컴퓨터에 적용됩니다.
- 이러한 가상 컴퓨터의 보호 활성화
- 모든 기능이 예상대로 작동하는지 확인하기 위해 장애 조치(Failover) 테스트

이 시나리오를 설정하는 동안 문제가 발생할 경우 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의 사항을 게시하세요.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델이 있습니다. [리소스 관리자 및 클래식](../resource-manager-deployment-model.md) 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다.

## 시작하기 전에

다음 필수 조건이 충족되었는지 확인합니다.

### Azure 필수 조건

- [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. 계정이 없는 분은 [무료 계정](https://azure.microsoft.com/free)으로 시작할 수 있습니다. [Azure Site 복구 관리자 가격 책정](https://azure.microsoft.com/pricing/details/site-recovery/)에 대해서도 알아보세요.
- CSP 구독 시나리오에 복제하려면 CSP 구독이 필요합니다. [CSP 프로그램에 등록하는 방법](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx)에서 CSP 프로그램에 대해 자세히 알아보세요.
- Azure로 복제된 데이터를 저장하려면 Azure v2 저장소(ARM) 계정이 있어야 합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 서비스와 같은 지역에 있어야 하며, 같은 구독 또는 CSP 구독에 연결되어야 합니다. Azure 저장소 설정에 대한 자세한 내용은 [Microsoft Azure 저장소 소개](../storage/storage-introduction.md)를 참조하세요.
- 보호할 가상 컴퓨터가 [Azure 가상 컴퓨터 필수 조건](site-recovery-best-practices.md#azure-virtual-machine-requirements)을 준수하는지 확인해야 합니다.

> [AZURE.NOTE] 현재, VM 수준 작업만 Powershell을 통해 수행할 수 있습니다. 복구 계획 수준 작업에 대한 지원이 곧 제공될 것입니다. 지금은 '보호되는 VM' 수준에서만 장애 조치(Failover)를 수행할 수 있고 복구 수준에서는 수행할 수 없습니다.

### VMM 필수 구성 요소
- System Center 2012 R2에서 실행되는 VMM 서버가 필요합니다.
- 보호할 가상 컴퓨터를 포함하는 모든 VMM 서버가 Azure Site Recovery 공급자를 실행해야 합니다. 이 공급자는 Azure Site Recovery 배포 중에 설치됩니다.
- 보호할 VMM 서버에 클라우드가 하나 이상 있어야 합니다. 클라우드에는 다음이 포함되어야 합니다.
	- 하나 이상의 VMM 호스트 그룹.
	- 각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터
	- 원본 Hyper-V 서버에 있는 하나 이상의 가상 컴퓨터.
- VMM 클라우드 설정에 대해 자세히 알아봅니다.
	- [System Center 2012 R2 VMM에서 사설 클라우드의 새로운 기능](http://go.microsoft.com/fwlink/?LinkId=324952)과 [VMM 2012 및 클라우드](http://go.microsoft.com/fwlink/?LinkId=324956)에서 사설 VMM 클라우드에 대해 알아봅니다.
	- [VMM 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)에 대해 알아봅니다.
	- 클라우드 패브릭 요소가 구현되면 [VMM에서 사설 클라우드 만들기](http://go.microsoft.com/fwlink/p/?LinkId=324953) 및 [연습: System Center 2012 SP1 VMM으로 사설 클라우드 만들기](http://go.microsoft.com/fwlink/p/?LinkId=324954)에 대해 알아보세요.


### Hyper-V 필수 조건

- 호스트 Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.
- 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. 자세한
- 자세한 내용은 [Hyper-V 복제본 Broker 구성 방법](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)을 참조하세요.
- 보호를 관리할 Hyper-V 호스트 서버 또는 클러스터가 모두 VMM 클라우드에 포함되어야 합니다.

### 네트워크 매핑 필수 조건
Azure에서 가상 컴퓨터를 보호하는 경우 네트워크 매핑은 원본 VMM 서버의 VM 네트워크와 대상 Azure 네트워크를 매핑하여 다음을 가능하게 합니다.

- 속해 있는 복구 계획에 관계없이 동일한 네트워크에서 장애 조치(Failover)되는 모든 컴퓨터가 서로 연결할 수 있습니다.
- 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다.
- 네트워크 매핑을 구성하지 않으면 동일한 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure로의 장애 조치(Failover) 후에 서로 연결할 수 있습니다.

네트워크 매핑을 배포하려면 다음이 필요합니다.

- 원본 VMM 서버에서 보호할 가상 컴퓨터가 VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
- 복제된 가상 컴퓨터가 장애 조치(Failover) 후 연결할 수 있는 Azure 네트워크. 이 네트워크는 장애 조치(Failover) 시 선택합니다. 네트워크는 Azure Site Recovery 구독과 동일한 지역에 있어야 합니다.

네트워크 매핑에 대해 자세히 알아보기

- [VMM에서 논리 네트워크를 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [VMM에서 VM 네트워크 및 게이트웨이를 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Azure에서 가상 네트워크를 구성 및 모니터링하는 방법](https://azure.microsoft.com/documentation/services/virtual-network/)


###PowerShell 필수 구성 요소
Azure PowerShell을 사용할 준비가 되었는지 확인하세요. 이미 PowerShell을 사용하고 있는 경우 버전 0.8.10 이상으로 업그레이드해야 합니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. PowerShell을 설정 및 구성하면 [여기](https://msdn.microsoft.com/library/dn850420.aspx)에서 서비스에 사용 가능한 모든 cmdlet을 볼 수 있습니다.

Azure PowerShell에서 매개 변수 값, 입력, 출력이 일반적으로 처리되는 방법 등 cmdlet를 사용하는 데 도움이 되는 팁을 보려면 [Azure Cmdlet 시작하기](https://msdn.microsoft.com/library/azure/jj554332.aspx)를 참조하세요.

## 1단계: 구독 설정 

1. Azure powershell에서 다음 cmdlet을 사용하여 Azure 계정에 로그인합니다.
 
		$UserName = "<user@live.com>"
		$Password = "<password>"
		$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
		$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
		Login-AzureRmAccount #-Credential $Cred 
	

2. 구독 목록을 가져옵니다. 각 구독의 subscriptionID 목록도 함께 표시될 것입니다. 복구 서비스 자격 증명 모음을 만들려는 구독의 subscriptionID를 메모합니다.

		Get-AzureRmSubscription 

3. 구독 ID를 알려주어 복구 서비스 자격 증명 모음을 만들 구독을 설정합니다.

		Set-AzureRmContext –SubscriptionID <subscriptionId>


## 2단계: 복구 서비스 자격 증명 모음 만들기 

1. 아직 ARM 리소스 그룹이 없으면 지금 만듭니다.

		New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. 새 복구 서비스 자격 증명 모음을 만들고, 만든 ASR 자격 증명 모음 개체를 변수에 저장합니다(나중에 사용됨). Get-AzureRMRecoveryServicesVault cmdlet을 사용하여 ASR 자격 증명 모음 개체 게시 만들기를 검색할 수도 있습니다.

		$vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## 3단계: 복구 서비스 자격 증명 모음 설정

1.  다음 명령을 실행하여 자격 증명 모음 컨텍스트를 설정합니다.

		Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## 4단계: Azure Site Recovery 공급자 설치

1.	VMM 컴퓨터에서 다음 명령을 실행하여 디렉터리를 만듭니다.
	
		New-Item c:\ASR -type directory
		
2.	다음 명령을 실행하고 다운로드한 공급자를 사용하여 파일을 추출합니다.
	
		pushd C:\ASR\
		.\AzureSiteRecoveryProvider.exe /x:. /q

	
3.	다음 명령을 사용하여 공급자를 설치합니다.
	
		.\SetupDr.exe /i
		$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
		do
		{
		                $isNotInstalled = $true;
		                if(Test-Path $installationRegPath)
		                {
		                                $isNotInstalled = $false;
		                }
		}While($isNotInstalled)

    설치가 완료될 때까지 기다립니다.
	
4.	다음 명령을 사용하여 자격 증명 모음에 서버를 등록합니다.
	
		$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
		pushd $BinPath
		$encryptionFilePath = "C:\temp".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

	
## 5단계: Azure 저장소 계정 만들기

1. Azure 저장소 계정이 없는 경우 다음 명령을 실행하여 자격 증명 모음과 동일한 지역에 지역에서 복제가 활성화된 계정을 만듭니다.

		$StorageAccountName = "teststorageacc1"	#StorageAccountname
		$StorageAccountGeo  = "Southeast Asia" 	
		$ResourceGroupName =  “myRG” 			#ResourceGroupName 
		$RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

저장소 계정은 Azure Site Recovery 서비스와 같은 지역에 있고 같은 구독과 연결되어야 합니다.

## 6단계: Azure 복구 서비스 에이전트 설치

1. [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent)에서 Azure 복구 서비스 에이전트를 다운로드하여 보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에 설치합니다.

2. 모든 VMM 호스트에 다음 명령을 실행합니다.

	marsagentinstaller.exe /q /nu

## 7단계: 클라우드 보호 설정 구성

1.	다음 명령을 실행하여 Azure에 대한 복제 정책을 만듭니다.

	
		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points 

		$policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.	다음 명령을 실행하여 보호 컨테이너를 가져옵니다.
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
  
3.	생성된 작업을 사용하고 친숙한 정책 이름을 알려주어 변수에 정책 세부 정보를 가져옵니다.

		$policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.	복제 정책을 사용하여 보호 컨테이너 연결을 시작합니다.

		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.	작업이 완료되면 다음 명령을 실행합니다.
   
		$job = Get-AzureRmSiteRecoveryJob -Job $associationJob
   		if($job -eq $null -or $job.StateDescription -ne "Completed")
   		 {
        $isJobLeftForProcessing = $true;
    	}

6.	작업에서 처리를 완료하면 다음 명령을 실행합니다.

		if($isJobLeftForProcessing)
    	{
    	Start-Sleep -Seconds 60
    	}
        }While($isJobLeftForProcessing)

작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.

## 8단계: 네트워크 매핑 구성

네트워크 매핑을 시작하기 전에 원본 VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되었는지 확인합니다. 또한 Azure 가상 네트워크를 하나 이상 만듭니다.

[Azure Resource Manager 및 PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)에서 Azure Resource Manager 및 PowerShell을 사용하여 가상 네트워크를 만드는 방법을 자세히 알아보세요.

단일 Azure 네트워크에 여러 가상 컴퓨터 네트워크를 매핑할 수 있습니다. 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.

1. 첫 번째 명령은 현재 Azure Site Recovery 자격 증명 모음의 서버를 가져옵니다. 이 명령은 $Servers 어레이 변수에 Microsoft Azure Site Recovery 서버를 저장합니다.

		$Servers = Get-AzureRmSiteRecoveryServer

2. 두 번째 명령은 $Servers 어레이의 첫 번째 서버에 대한 사이트 복구 네트워크를 가져옵니다. 이 명령은 $Networks 변수에 네트워크를 저장합니다.


    	$Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. 세 번째 명령은 Azure 가상 네트워크를 가져온 다음 해당 값을 $AzureVmNetworks 변수에 저장합니다.
   
		$AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. 최종 cmdlet은 기본 네트워크와 Azure 가상 컴퓨터 네트워크 사이에 매핑을 만듭니다. cmdlet은 $Networks의 첫 번째 요소로 기본 네트워크를 지정합니다. cmdlet은 $AzureVmNetworks의 첫 번째 요소로 가상 컴퓨터 네트워크를 지정합니다.

		New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## 9단계: 가상 컴퓨터의 보호 활성화

서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다.

 다음 사항에 유의하세요.

 - 가상 컴퓨터는 Azure 요구 사항을 충족해야 합니다. 계획 가이드의 [필수 조건 및 지원](site-recovery-best-practices.md)에서 해당 요구 사항을 확인하세요.

 - 보호를 사용하도록 설정하려면 가상 컴퓨터에 대해 운영 체제 및 운영 체제 디스크 속성을 설정해야 합니다. VMM에서 가상 컴퓨터 템플릿을 사용하여 가상 컴퓨터를 만들 때 속성을 설정할 수 있습니다. 가상 컴퓨터 속성의 **일반** 및 **하드웨어 구성** 탭에서 기존 가상 컴퓨터에 대해 이러한 속성을 설정할 수도 있습니다. 이러한 속성을 VMM에서 설정하지 않는 경우 Azure Site Recovery 포털에서 구성할 수 있습니다.


  1. 보호를 활성화하려면 다음 명령을 실행하여 보호 컨테이너를 가져옵니다.
	
			$ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
	
  2. 다음 명령을 실행하여 VM(보호 엔터티)을 가져옵니다.
	
	 		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
		
  3. 다음 명령을 실행하여 VM에 DR을 사용하도록 설정합니다.

			$jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## 배포 테스트

배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다. 다음 사항에 유의하세요.

- 장애 조치(Failover) 후에 원격 데스크탑을 사용하여 Azure의 가상 컴퓨터에 연결하려면 가상 컴퓨터에서 원격 데스크탑 연결을 사용하도록 설정하고 나서 테스트 장애 조치(Failover)를 실행합니다.
- 장애 조치(Failover) 후에 공개 IP 주소를 사용하여 원격 데스크탑을 통해 Azure의 가상 컴퓨터에 연결합니다. 이 작업을 하려면 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없어야 합니다.

작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.


### 테스트 장애 조치(Failover) 실행

1.	다음 명령을 실행하여 테스트 장애 조치(Failover)를 시작합니다.
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### 계획된 장애 조치(Failover) 실행

1. 다음 명령을 실행하여 계획된 장애 조치(Failover)를 시작합니다.
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### 계획되지 않은 장애 조치 실행

1. 다음 명령을 실행하여 계획되지 않은 장애 조치(Failover)를 시작합니다.
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

	
## <a name=monitor></a> 작업 모니터

다음 명령을 사용하여 작업을 모니터합니다. 처리가 완료될 때까지 기다린 후 다음 작업을 시작할 수 있습니다.

	Do
	{
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        	$isJobLeftForProcessing = $true;
        }

	if($isJobLeftForProcessing)
        {
        	Start-Sleep -Seconds 60
        }
	}While($isJobLeftForProcessing)



## 다음 단계

Azure Resource Manager PowerShell cmdlet과 함께 Azure Site Recovery에 대해 [자세히 알아보세요](https://msdn.microsoft.com/library/azure/mt637930.aspx).

<!---HONumber=AcomDC_0921_2016-->