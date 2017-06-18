<properties
 pageTitle="Linux VM에서 HPC 팩으로 STAR-CCM+ 실행 | Microsoft Azure"
 description="Azure에서 Microsoft HPC Pack 클러스터를 배포하고 RDMA 네트워크 간의 여러 Linux 계산 노드에서 STAR-CCM+ 작업을 실행합니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩으로 STAR-CCM+ 실행
이 문서에서는 Azure에 Microsoft HPC 팩 클러스터를 배포하고 InfiniBand와 상호 연결된 여러 Linux 계산 노드에서 [CD-adapco STAR-CCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) 작업을 실행하는 방법을 보여 줍니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC 팩에서는 MPI 응용 프로그램을 포함한 다양한 대규모 HPC 및 병렬 응용 프로그램을 Microsoft Azure 가상 컴퓨터의 클러스터에서 실행하는 기능을 제공합니다. HPC 팩은 HPC 팩 클러스터에 배포된 Linux 계산 노드 VM에서 Linux HPC 응용 프로그램의 실행도 지원합니다. HPC 팩으로 Linux 계산 노드 사용에 대한 소개는 [Azure에서 HPC 팩 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)을 참조하세요.

## HPC 팩 클러스터 설정
[다운로드 센터](https://www.microsoft.com/ko-KR/download/details.aspx?id=44949)에서 HPC 팩 IaaS 배포 스크립트를 다운로드하고 로컬로 추출합니다.

Azure PowerShell은 필수 요소입니다. PowerShell이 로컬 컴퓨터에 구성되지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서를 참조하세요.

이 문서를 작성할 당시 Azure 마켓플레이스의 Linux 이미지(Azure용 InfiniBand 포함)는 SLES 12, CentOS 6.5, CentOS 7.1용입니다. 이 문서는 SLES 12 사용을 기반으로 합니다. 마켓플레이스에서 HPC를 지원하는 모든 Linux 이미지의 이름을 검색하려면 다음 PowerShell 명령을 실행합니다.

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

출력에는 이러한 이미지를 사용할 수 있는 위치와 향후 배포 템플릿에 사용할 이미지 이름(**ImageName**)이 나열됩니다.

클러스터를 배포하기 전에 HPC 팩 배포 템플릿 파일을 작성해야 합니다. 소규모 클러스터를 대상으로 하기 때문에 헤드 노드는 도메인 컨트롤러가 되며 로컬 SQL 데이터베이스를 호스팅합니다.

아래 템플릿은 이러한 헤드 노드를 배포하고 **MyCluster.xml**이라는 XML 파일을 만들고 **SubscriptionId**, **StorageAccount**, **Location**, **VMName**, **ServiceName** 값을 사용하는 값으로 바꿉니다.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

관리자 권한 명령 프롬프트에서 PowerShell 명령을 실행하여 헤드 노드 만들기를 시작합니다.

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20~30분 후에 헤드 노드가 준비됩니다. 가상 컴퓨터의 **연결** 아이콘을 클릭하여 Azure 포털에서 헤드 노드에 연결할 수 있습니다.

최종적으로 DNS 전달자를 수정해야 할 수 있습니다. 이 작업을 위해 DNS 관리자를 시작합니다.

1.  DNS 관리자에서 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**, **전달자** 탭을 차례대로 선택합니다.

2.  **편집** 단추를 클릭하여 모든 전달자를 제거한 다음 **확인**을 클릭합니다.

3.  **전달자를 사용할 수 없으면 루트 힌트 사용** 확인란이 선택되어 있는지 확인하고 **확인**을 클릭합니다.

## Linux 계산 노드 설정
헤드 노드를 만들 때 사용한 것과 동일한 배포 템플릿을 사용하여 Linux 계산 노드를 배포합니다.

로컬 컴퓨터에서 헤드 노드로 **MyCluster.xml** 파일을 복사하고 배포하려는 노드 수(20 이하)로 **NodeCount** 태그를 업데이트합니다. 구독에서 각 A9 인스턴스는 16개의 코어를 사용하므로 Azure 할당량에 사용할 수 있는 코어가 충분해야 합니다. 동일한 예산으로 더 많은 VM을 사용하려는 경우 A9 대신 A8 인스턴스(8개 코어)를 사용할 수 있습니다.

헤드 노드에서 HPC 팩 IaaS 배포 스크립트를 복사합니다.

관리자 권한 명령 프롬프트에서 다음의 Azure PowerShell 명령을 실행합니다.

1.  **Add-AzureAccount**를 실행하여 Azure 구독을 연결합니다.

2.  여러 구독이 있는 경우 **Get-AzureSubscription**을 실행하여 나열합니다.

3.  **Select-AzureSubscription -SubscriptionName xxxx -Default** 명령을 실행하여 기본 구독을 설정합니다.

4.  **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml**을 실행하여 Linux 계산 노드 배포를 시작합니다.

    ![헤드 노드 배포 작업][hndeploy]

HPC 팩 클러스터 관리자 도구를 엽니다. 몇 분 후에 클러스터 계산 노드 목록에 Linux 계산 노드가 정기적으로 나타납니다. 클래식 배포 모드에서는 IaaS VM이 순차적으로 생성됩니다. 따라서 노드 수가 중요할 경우 전체를 배포하려면 많은 시간이 소요될 수 있습니다.

![HPC 팩 클러스터 관리자의 Linux 노드][clustermanager]

이제 모든 노드가 클러스터에서 실행 중이며 추가 인프라 설정을 수행해야 합니다.

## Windows 및 Linux 노드에 대한 Azure 파일 공유 설정
Azure 파일 서비스를 사용하여 스크립트, 응용 프로그램 패키지, 데이터 파일을 저장할 수 있습니다. Azure 파일은 Azure Blob 저장소를 영구 저장소로 사용하면서 CIFS 기능을 제공합니다. 이것이 가장 확장성 있는 솔루션은 아니지만 가장 간단하며 전용 VM이 필요하지 않습니다.

[Windows에서 Azure 파일 저장소 시작](..\storage\storage-dotnet-how-to-use-files.md) 문서의 지침에 따라 Azure 파일 공유를 만듭니다.

저장소 계정 이름 **saname**, 파일 공유 이름 **sharename** 및 저장소 계정 키 **sakey**를 유지합니다.

### 헤드 노드에 Azure 파일 공유 탑재
로컬 컴퓨터 자격 증명 모음에 자격 증명을 저장하려면 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

그런 다음 Azure 파일 공유를 탑재하려면 다음을 실행합니다.

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Linux 계산 노드에 Azure 파일 공유 탑재
HPC 팩에는 clusrun이라는 유용한 도구가 기본 제공됩니다. 이 명령줄 도구를 사용하여 계산 노드 집합에서 동일한 명령을 동시에 실행할 수 있습니다. 여기서는 Azure 파일 공유를 탑재하고 다시 부팅 후에도 탑재 공유를 유지하기 위해 사용합니다. 헤드 노드의 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

탑재 디렉터리를 만들려면

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Azure 파일 공유를 탑재하려면

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

탑재 공유를 유지하려면

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## STAR-CCM+ 설치
Azure VM 인스턴스 A8 및 A9는 InfiniBand 지원 및 RDMA 기능을 제공합니다. 이러한 기능을 구현하는 커널 드라이버는 Azure 마켓플레이스에서 Windows Server 2012 R2, SUSE 12, CentOS 6.5, CentOS 7.1 이미지에 사용할 수 있습니다. Microsoft MPI 및 Intel MPI(릴리스 5.x)는 Azure에서 해당 드라이버를 지원하는 두 MPI 라이브러리입니다.

CD-adapco STAR-CCM+ 릴리스 11.x 이상은 Intel MPI 버전 5.x와 함께 제공되므로 Azure에 대한 InfiniBand 지원이 포함됩니다.

[CD-adapco 포털](https://steve.cd-adapco.com)에서 Linux64 STAR-CCM+ 패키지를 다운로드합니다. 이 경우에는 혼합 정밀도의 버전 11.02.010을 사용했습니다.

헤드 노드의 **/hpcdata** Azure 파일 공유에서 다음 내용이 포함된 **setupstarccm.sh**라는 셸 스크립트를 만듭니다. 이 스크립트는 STAR-CCM+를 로컬로 설정하기 위해 각 계산 노드에서 실행합니다.

#### 샘플 setupstarcm.sh 스크립트
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
이제 모든 Linux 계산 노드에 STAR-CCM+를 설정하기 위해 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

명령이 실행되는 동안 클러스터 관리자의 열 지도를 사용하여 CPU 사용량을 모니터링할 수 있습니다. 몇 분 후 모든 노드가 올바르게 설정됩니다.

## STAR-CCM+ 작업 실행
HPC 팩은 STAR-CCM+ 작업을 실행하기 위한 작업 스케줄러 기능에 사용합니다. 이렇게 하려면 작업을 시작하고 STAR-CCM+를 실행하는데 사용되는 몇 가지 스크립트에 대한 지원이 필요합니다. 입력 데이터는 편의상 Azure 파일 공유에 먼저 저장됩니다.

다음 PowerShell 스크립트는 STAR-CCM+ 작업을 큐에 대기시키는 데 사용하며 3가지 인수를 사용합니다.

*  모델 이름

*  사용할 노드 수

*  각 노드에 사용할 코어 수

STAR-CCM+는 메모리 대역폭을 초과할 수 있으므로 계산 노드당 적은 코어를 사용하고 새 노드를 추가하는 것이 좋습니다. 정확한 노드당 코어 수는 프로세서 제품군 및 상호 연결 속도에 따라 달라집니다.

노드는 작업에 단독으로 할당되며 다른 작업과 공유할 수 없습니다. 이 작업은 MPI 작업으로 직접 시작되지 않습니다. **runstarccm.sh** 셸 스크립트에서 MPI 시작 관리자를 시작합니다.

입력 모델 및 **runstarccm.sh** 스크립트는 이전에 탑재된 **/hpcdata** 공유에 저장됩니다.

로그 파일은 작업 ID로 이름이 지정되고 STAR-CCM+ 출력 파일과 함께 **/hpcdata 공유**에 저장됩니다.


#### 샘플 SubmitStarccmJob.ps1 스크립트
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
**runner.java**를 원하는 STAR-CCM+ java 모델 시작 관리자와 로깅 코드로 바꿉니다.

#### 샘플 runstarccm.sh 스크립트
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

이 테스트에서는 Power-On-Demand 라이선스 토큰을 사용했습니다. 이 테스트에서는 **$CDLMD\_LICENSE\_FILE** 환경 변수를 **1999@flex.cd-adapco.com**으로 설정하고 명령줄의 **-podkey** 옵션에 키를 설정해야 합니다.

일부 초기화 후에는 스크립트가 (HPC 팩이 설정하는 **$CCP\_NODES\_CORES** 환경 변수에서) 노드 목록을 추출하여 MPI 시작 관리자가 사용하는 hostfile을 작성합니다. 이 hostfile에는 작업에 사용된 계산 노드 이름의 목록이 포함되며, 한 줄당 하나의 이름이 포함됩니다.

**$CCP\_NODES\_CORES**의 형식은 다음 패턴을 따릅니다.

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

여기서,

* `<Number of nodes>`는 이 작업에 할당된 노드 수입니다.

* `<Name of node_n_...>`은 이 작업에 할당된 각 노드의 이름입니다.

* `<Cores of node_n_...>`은 이 작업에 할당된 노드의 코어 수입니다.

코어 수(**$NBCORES**)도 노드 수(**$NBNODES**) 및 노드당 코어 수(매개 변수 **$NBCORESPERNODE**로 제공)를 기준으로 계산됩니다.

Azure에서 Intel MPI와 함께 사용하는 MPI 옵션은 다음과 같습니다.

*   Intel MPI를 지정하는 `-mpi intel`.

*   Azure InfiniBand 동사를 사용하기 위한 `-fabric UDAPL`.

*   STAR-CCM+를 사용하는 MPI의 대역폭을 최적화하기 위한 `-cpubind bandwidth,v`.

*   Intel MPI가 Azure InfiniBand에서 작동하고 노드당 필요한 코어 수를 설정하기 위한 `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`.

*   UI를 사용하지 않고 배치 모드에서 STAR-CCM+를 시작하기 위한 `-batch`.


마지막으로 작업을 시작하려면 노드가 실행 중이고 클러스터 관리자에서 온라인 상태인지 확인합니다. 그런 다음 PowerShell 명령 창에서 다음을 실행합니다.

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## 노드 중지
나중에 테스트를 완료한 후 다음 HPC 팩 PowerShell 명령을 사용하여 노드를 중지하거나 시작할 수 있습니다.

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## 다음 단계
다른 Linux 워크로드를 실행해 봅니다. 예를 들어 다음을 참조하세요.

* [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0914_2016-->