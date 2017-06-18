<properties
	pageTitle="HDInsight를 사용하여 스크립트 작업 개발 | Microsoft Azure"
	description="스크립트 작업을 사용하여 Hadoop 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>

# HDInsight용 스크립트 작업 스크립트 개발

HDInsight용 스크립트 작업 스크립트를 작성하는 방법을 알아봅니다. 스크립트 동작 스크립트 사용에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요. Linux 기반 HDInsight 클러스터용으로 작성된 동일한 문서를 보려면 [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

스크립트 작업은 Hadoop 클러스터에서 실행되는 추가 소프트웨어를 설치하거나 클러스터에 설치된 응용 프로그램의 구성을 변경하기 위해 사용할 수 있습니다. 스크립트 작업은 HDInsight 클러스터를 배포할 때 클러스터 노드에서 실행되는 스크립트이며 클러스터의 노드에서 HDInsight 구성이 완료되면 실행됩니다. 스크립트 작업은 시스템 관리자 계정 권한으로 실행되며 클러스터 노드에 대한 모든 액세스 권한을 제공합니다. 각 클러스터에는 지정된 순서로 실행되는 스크립트 작업 목록이 제공될 수 있습니다.

> [AZURE.NOTE] 다음 오류 메시지가 나타나는 경우
> 
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> 도우미 메서드를 포함하지 않았기 때문입니다. [사용자 지정 스크립트에 대한 도우미 메서드](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts)를 참조하세요.

## 샘플 스크립트

Windows 운영 체제에서 HDInsight 클러스터를 만드는 경우 스크립트 작업은 Azure PowerShell 스크립트입니다. 다음은 사이트 구성 파일 구성에 대한 샘플 스크립트입니다.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

	param (
	    [parameter(Mandatory)][string] $ConfigFileName,
	    [parameter(Mandatory)][string] $Name,
	    [parameter(Mandatory)][string] $Value,
	    [parameter()][string] $Description
	)

	if (!$Description) {
	    $Description = ""
	}

	$hdiConfigFiles = @{
	    "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
	    "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
	    "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
	    "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
	    "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
	}

	if (!($hdiConfigFiles[$ConfigFileName])) {
	    Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
	    return
	}

	[xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

	$existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

	if ($existingproperty) {
	    $existingproperty.Value = $Value
	    $existingproperty.Description = $Description
	} else {
	    $newproperty = @($configFile.configuration.property)[0].Clone()
	    $newproperty.Name = $Name
	    $newproperty.Value = $Value
	    $newproperty.Description = $Description
	    $configFile.configuration.AppendChild($newproperty)
	}

	$configFile.Save($hdiConfigFiles[$ConfigFileName])

	Write-HDILog "$configFileName has been configured."

스크립트에서는 구성 파일 이름, 수정하려는 속성, 설정하려는 값 및 설명의 4가지 매개 변수가 사용됩니다. 예:

	hive-site.xml hive.metastore.client.socket.timeout 90 

이러한 매개 변수는 hive-site.xml 파일에서 hive.metastore.client.socket.timeout 값을 90으로 설정합니다. 기본값은 60초입니다.

이 샘플 스크립트는 [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1)에도 나옵니다.

HDInsight는 HDInsight 클러스터에 추가 구성 요소를 설치하는 여러 스크립트를 제공합니다.

이름 | 스크립트
----- | -----
**Spark 설치** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]을 참조하세요.
**R 설치** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-r-scripts]을 참조하세요.
**Solr 설치** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)을 참조하세요.
- **Giraph 설치** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)을 참조하세요.

스크립트 작업은 Azure 포털, Azure PowerShell에서 배포하거나 HDInsight .NET SDK를 사용하여 배포할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]을 참조하세요.

> [AZURE.NOTE] 샘플 스크립트는 HDInsight 클러스터 버전 3.1 이상에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.





## 사용자 지정 스크립트에 대한 도우미 메서드

스크립트 작업 도우미 메서드는 사용자 지정 스크립트를 쓰는 동안 사용할 수 있는 유틸리티입니다. 이러한 메서드는 [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)에 정의되어 있으며 다음을 사용하여 스크립트에 포함할 수 있습니다.

    # Download config action module from a well-known directory.
	$CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
	$CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
	$webclient = New-Object System.Net.WebClient;
	$webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);
	
	# (TIP) Import config action helper method module to make writing config action easy.
	if (Test-Path ($CONFIGACTIONMODULE))
	{ 
		Import-Module $CONFIGACTIONMODULE;
	} 
	else
	{
		Write-Output "Failed to load HDInsightUtilities module, exiting ...";
		exit;
	}

다음은 이 스크립트에서 제공하는 도우미 메서드입니다.

도우미 메서드 | 설명
-------------- | -----------
**Save-HDIFile** | 파일을 지정된 URI(Uniform Resource Identifier)에서 클러스터에 할당된 Azure VM 노드와 연결된 로컬 디스크의 위치로 다운로드합니다.
**Expand-HDIZippedFile** | 압축된 파일의 압축을 풉니다.
**Invoke-HDICmdScript** | cmd.exe에서 스크립트를 실행합니다.
**Write-HDILog** | 스크립트 작업에 사용되는 사용자 지정 스크립트의 출력을 씁니다.
**Get-Services** | 스크립트가 실행되는 컴퓨터에서 실행 중인 서비스의 목록을 가져옵니다.
**Get-Service** | 입력으로 특정 서비스 이름을 사용하는 이 메서드는 스크립트가 실행되는 컴퓨터의 특정 서비스에 대한 세부 정보(서비스 이름, 프로세스 ID, 상태 등)를 가져옵니다.
**Get-HDIServices** | 스크립트가 실행되는 컴퓨터에서 실행 중인 HDInsight 서비스의 목록을 가져옵니다.
**Get-HDIService** | 입력으로 특정 HDInsight 서비스 이름을 사용하는 이 메서드는 스크립트가 실행되는 컴퓨터의 특정 서비스에 대한 세부 정보(서비스 이름, 프로세스 ID, 상태 등)를 가져옵니다.
**Get-ServicesRunning** | 스크립트가 실행되는 컴퓨터에서 실행 중인 서비스의 목록을 가져옵니다.
**Get-ServiceRunning** | 특정 서비스(이름별)가 스크립트가 실행되는 컴퓨터에서 실행 중인지 확인합니다.
**Get-HDIServicesRunning** | 스크립트가 실행되는 컴퓨터에서 실행 중인 HDInsight 서비스의 목록을 가져옵니다.
**Get-HDIServiceRunning** | 특정 HDInsight 서비스(이름별)가 스크립트가 실행되는 컴퓨터에서 실행 중인지 확인합니다.
**Get-HDIHadoopVersion** | 스크립트가 실행되는 컴퓨터에 설치된 Hadoop의 버전을 가져옵니다.
**Test-IsHDIHeadNode** | 스크립트가 실행되는 컴퓨터가 헤드 노드인지 확인합니다.
**Test-IsActiveHDIHeadNode** | 스크립트가 실행되는 컴퓨터가 활성 헤드 노드인지 확인합니다.
**Test-IsHDIDataNode** | 스크립트가 실행되는 컴퓨터가 데이터 노드인지 확인합니다.
**Edit-HDIConfigFile** | 구성 파일 hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml 또는 yarn-site.xml을 편집합니다.


## 스크립트 개발을 위한 모범 사례

HDInsight 클러스터용으로 사용자 지정 스크립트를 개발할 때 유의해야 하는 몇 가지 모범 사례는 다음과 같습니다.

- Hadoop 버전 확인

	HDInsight 버전 3.1(Hadoop 2.4) 이상만 클러스터에 사용자 지정 구성 요소 설치를 위한 스크립트 작업 사용을 지원합니다. 스크립트의 다른 작업을 계속 수행하기 전에 사용자 지정 스크립트에서 **Get-HDIHadoopVersion** 도우미 메서드를 사용하여 Hadoop 버전을 확인해야 합니다.


- 스크립트 리소스에 대한 안정적인 링크 제공

	사용자는 클러스터의 사용자 지정에서 사용되는 모든 스크립트와 기타 아티팩트가 클러스터의 전체 수명 동안 사용 가능한 상태로 유지되고 이러한 파일의 버전이 이 기간 동안 변경되지 않는지 확인해야 합니다. 클러스터 노드의 재이미징이 필요한 경우 이러한 리소스를 사용해야 합니다. 모든 리소스를 사용자가 제어하는 저장소 계정으로 다운로드하고 보관하는 것이 좋습니다. 이 계정은 기본 저장소 계정이나 사용자 정의된 클러스터에 대해 클러스터 배포 시 지정된 추가 저장소 계정 중 하나일 수 있습니다. 예를 들어 설명서에 제공된 Spark 및 R 사용자 지정된 클러스터 샘플에서는 이 저장소 계정 https://hdiconfigactions.blob.core.windows.net/에 리소스의 로컬 복사본을 만들었습니다.


- 클러스터 사용자 지정 스크립트가 멱등원인지 확인

	HDInsight 클러스터의 노드가 클러스터 수명 동안 재이미징된다고 예상해야 합니다. 클러스터가 재이미징될 때마다 클러스터 사용자 지정 스크립트가 실행됩니다. 이 스크립트는 멱등원이 되도록 설계해야 합니다. 즉, 재이미징될 때 스크립트에서는 클러스터가 처음 생성될 때 스크립트가 처음으로 실행된 직후의 상태와 동일한 사용자 지정된 상태로 클러스터가 돌아가는지 확인해야 합니다. 예를 들어 사용자 지정 스크립트가 처음 실행될 때와 이후 실행될 때마다 응용 프로그램을 D:\\AppLocation에 설치 설치한 경우 재이미징될 때 스크립트에서는 응용 프로그램이 D:\\AppLocation 위치에 있는지 확인한 이후에 스크립트의 다른 단계를 진행해야 합니다.


- 최적의 위치에 사용자 지정 구성 요소 설치

	클러스터 노드가 재이미징될 때 C:\\ 리소스 드라이브 및 D:\\ 시스템 드라이브를 다시 포맷하면 해당 드라이브에 설치된 응용 프로그램 및 데이터를 잃을 수 있습니다. 클러스터의 일부인 Azure VM(가상 컴퓨터) 노드가 작동 중단되거나 새 노드로 대체되는 경우에도 이 문제가 발생할 수 있습니다. 클러스터에서 D:\\ 드라이브나 C:\\apps 위치에 구성 요소를 설치할 수 있습니다. C:\\ 드라이브의 다른 모든 위치는 예약되어 있습니다. 클러스터 사용자 지정 스크립트에 설치할 응용 프로그램 또는 라이브러리의 위치를 지정합니다.


- 클러스터 아키텍처의 고가용성 확인

	HDInsight는 고가용성을 위해 한 헤드 노드는 활성 모드(HDInsight 서비스가 실행 중)이고 다른 헤드 노드는 대기 모드(HDInsight 서비스가 실행 중이지 않음)인 활성-수동 아키텍처를 사용합니다. 노드는 HDInsight 서비스가 중단되는 경우 활성 모드와 수동 모드를 전환합니다. 고가용성을 위해 두 헤드 노드에서 서비스를 설치하기 위해 스크립트 작업이 사용되는 경우, HDInsight 장애 조치(Failover) 메커니즘이 이러한 사용자가 설치한 서비스를 자동으로 장애 조치(Failover)할 수 없습니다. 그러므로 활성-수동 모드 또는 활성-수동 모드인 경우 가용성이 높을 것으로 예상되는 HDInsight 헤드 노드에 사용자가 설치한 서비스에는 고유한 장애 조치(Failover) 메커니즘이 있어야 합니다.

	헤드 노드 역할이 *ClusterRoleCollection* 매개 변수에 값으로 지정된 경우 두 헤드 노드 모두에서 HDInsight 스크립트 동작 명령이 실행됩니다. 따라서 사용자 지정 스크립트를 설계할 때는 스크립트에서 이 설정을 인식하는지 확인해야 합니다. 두 헤드 노드 모두에서 동일한 서비스가 설치 및 시작되어 서로 경쟁하게 되는 문제가 발생해서는 안 됩니다. 또한 재이미징 동안 데이터가 손실되므로 스크립트 작업을 통해 설치된 소프트웨어는 이러한 이벤트에 대해 복원력이 있어야 합니다. 응용 프로그램은 여러 노드에 배포되는 고가용성 데이터와 작동하도록 설계되어야 합니다. 클러스터의 노드 중 1/5 정도가 동시에 재이미징될 수 있다는 점에 유의하세요.


- Azure Blob 저장소를 사용하도록 사용자 지정 구성 요소 구성

	클러스터 노드에 설치하는 사용자 지정 구성 요소에는 HDFS(Hadoop Distributed File System) 저장소를 사용하기 위한 기본 구성이 있을 수 있습니다. Azure Blob 저장소를 사용하도록 구성을 변경해야 합니다. 클러스터 재이미지 시 HDFS 파일 시스템은 포맷되고 거기에 저장된 데이터를 잃게 됩니다. 대신 Azure Blob 저장소를 사용하면 데이터가 유지됩니다.

## 일반적인 사용 패턴

이 섹션에서는 사용자 고유의 사용자 지정 스크립트를 작성하는 동안 실행할 수 있는 일반적인 사용 패턴 중 일부를 구현하는 방법에 대한 지침을 제공합니다.

### 환경 변수 구성

스크립트 작업 개발에 환경 변수를 설정해야 하는 경우가 자주 있습니다. 예를 들어 가장 가능성이 높은 시나리오는 외부 사이트에서 이진 파일을 다운로드하여 클러스터에 설치하고 'PATH' 환경 변수에 설치된 위치를 추가하는 경우입니다. 다음 코드 조각에서는 사용자 지정 스크립트에서 환경 변수를 설정하는 방법을 보여 줍니다.

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

이 문은 환경 변수 **MDS\_RUNNER\_CUSTOM\_CLUSTER**를 'true' 값으로 설정하고 이 변수의 범위를 컴퓨터 수준이 되도록 설정합니다. 환경 변수를 적절한 범위(컴퓨터 또는 사용자)로 설정하는 것이 중요한 경우가 있습니다. 환경 변수 설정에 대한 자세한 내용은 [여기][1]를 참조하세요.

### 사용자 지정 스크립트가 저장된 위치 액세스

클러스터를 사용자 지정하는 데 사용되는 스크립트는 클러스터의 기본 저장소 계정 또는 다른 모든 저장소 계정의 공용 읽기 전용 컨테이너에 있어야 합니다. 스크립트에서 다른 곳에 있는 리소스에 액세스할 경우 이러한 리소스는 공개적으로 액세스 가능(최소한 공용 읽기 전용)해야 합니다. 예를 들어 파일에 액세스한 후 SaveFile-HDI 명령을 사용하여 해당 파일을 저장할 수 있습니다.

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

이 예에서는 'somestorageaccount' 저장소 계정의 'somecontainer' 컨테이너가 공개적으로 액세스할 수 있는지 확인해야 합니다. 그렇지 않은 경우 스크립트는 ' 찾을 수 없음 ' 예외를 발생시키고 실패합니다.

### Add-AzureRmHDInsightScriptAction cmdlet에 매개 변수 전달

여러 매개 변수를 Add-AzureRmHDInsightScriptAction cmdlet에 전달하려면 스크립트에 대한 모든 매개 변수를 포함하도록 문자열 값의 형식을 지정해야 합니다. 예:

	"-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"
 
또는

	$parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### 실패한 클러스터 배포에 대한 예외 발생

클러스터 사용자 지정이 예상대로 성공하지 못했음을 정확히 알리는 알림을 받으려면 예외를 발생시키고 클러스터 만들기를 실패로 처리해야 합니다. 예를 들어 파일이 존재하면 파일을 처리하고, 파일이 존재하지 않으면 오류 사례를 처리할 수 있습니다. 이렇게 하면 스크립트가 정상적으로 종료되고 클러스터의 상태가 올바르게 알려집니다. 다음 코드 조각에서는 이를 실현하는 방법에 대한 예제를 제공합니다.

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

이 코드 조각에서 파일이 존재하지 않는 경우, 스크립트가 오류 메시지를 인쇄 후 정상적으로 실제로 종료하고 클러스터는 클러스터 사용자 지정 프로세스가 "성공적으로" 완료되었다고 가정하고 실행 중인 상태에 도달합니다. 누락된 파일 때문에 예상대로 클러스터 사용자 지점이 성공하지 못했다는 사실을 정확하게 알리지 않으려면, 예외를 발생하고 클러스터 사용자 지정 단계를 실패하는 것이 더 적절합니다. 이렇게 하려면 다음 샘플 코드 조각을 대신 사용해야 합니다.

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	throw
	}


## 스크립트 작업 배포를 위한 검사 목록
이러한 스크립트 배포를 준비할 때 수행하는 단계는 다음과 같습니다.

1. 사용자 지정 스크립트가 포함된 파일을 배포 중 클러스터 노드에서 액세스할 수 있는 위치에 배치합니다. 기본 또는 클러스터 배포 시 지정된 추가 저장소 계정, 또는 공개적으로 액세스할 수 있는 저장소 컨테이너가 될 수 있습니다.
2. 스크립트가 동일한 노드에서 여러 번 실행될 수 있도록 멱등원으로 실행되는지 확인하는 검사를 스크립트에 추가합니다.
3. **Write-Output** Azure PowerShell cmdlet을 사용하여 STDOUT 및 STDERR로 인쇄합니다. **Write-Host**를 사용하지 마세요.
4. 임시 파일 폴더(예: $env:TEMP)를 사용하여 스크립트에서 사용되는 다운로드된 파일을 보관하고 스크립트가 실행된 후 이 파일을 정리합니다.
5. D:\\ 또는 C:\\apps에만 사용자 지정 소프트웨어를 설치합니다. C: 드라이브의 다른 위치는 예약되어 있으므로 사용하면 안 됩니다. C: 드라이브에서 C:\\apps 외의 폴더에 파일을 설치하면 노드 재이미징 동안 설치에 실패할 수 있습니다.
6. OS 수준 설정이나 Hadoop 서비스 구성 파일이 변경된 경우에는 HDInsight 서비스에서 스크립트에 설정된 환경 변수와 같은 OS 수준 설정을 선택할 수 있도록 해당 서비스를 다시 시작할 수 있습니다.

## 사용자 지정 스크립트 디버그

스크립트 오류 로그는 클러스터 생성 시 클러스터에 대해 지정한 기본 스토리지 계정에 다른 출력과 함께 저장됩니다. 오류는 *u<\\cluster-name-fragment><\\time-stamp>setuplog*라는 이름으로 테이블에 저장됩니다. 이러한 로그는 클러스터에서 스크립트가 실행되는 모든 노드(헤드 노드 및 작업자 노드)의 레코드를 포함하는 집계된 로그입니다. 로그를 확인하는 쉬운 방법은 Visual Studio용 HDInsight 도구를 사용하는 것입니다. 도구 설치에 대해서는 [HDInsight용 Visual Studio Hadoop 도구 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)을 참조하세요.

**Visual Studio를 사용하여 로그를 확인하려면**

1. Visual Studio를 엽니다.
2. **보기**를 클릭하고 **서버 탐색기**를 클릭합니다.
3. 마우스 오른쪽 단추로 "Azure"를 클릭하고, **Microsoft Azure Subscriptions**에 연결을 클릭한 다음 자격 증명을 입력합니다.
4. **저장소**를 확장하고 기본 파일 시스템으로 사용되는 Azure 저장소 계정을 확장한 후 **테이블**을 확장하고 테이블 이름을 두 번 클릭합니다.


또한 클러스터 노드에 원격 액세스하여 사용자 지정 스크립트의 STDOUT 및 STDERR을 확인할 수 있습니다. 각 노드의 로그는 해당 노드 전용이며 **C:\\HDInsightLogs\\DeploymentAgent.log**에 로깅됩니다. 이러한 로그 파일은 사용자 지정 스크립트의 모든 출력을 기록합니다. Spark 스크립트 작업에 대한 예제 로그 조각은 다음과 같습니다.

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;
	...

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
	Version : 2.1.0.0;
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
	AzureVMName : HEADNODE0;
	IsException : False;
	ExceptionType : ;
	ExceptionMessage : ;
	InnerExceptionType : ;
	InnerExceptionMessage : ;
	Exception : ;


이 로그에서는 Spark 스크립트 작업이 HEADNODE0이라는 VM에서 실행되었고 실행 중 예외가 throw되지 않았음을 알 수 있습니다.

실행 오류가 발생하는 경우 오류를 설명하는 출력도 이 로그 파일에 포함됩니다. 이러한 로그에 제공되는 정보는 발생할 수 있는 스크립트 문제를 디버그할 때 유용합니다.


## 참고 항목

- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]
- [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-r-scripts]
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)
- [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

<!---HONumber=AcomDC_0914_2016-->