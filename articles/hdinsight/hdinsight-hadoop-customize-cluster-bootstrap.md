<properties
	pageTitle="부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정 | Microsoft Azure"
	description="부트스트랩을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>

# 부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정

경우에 따라 다음과 같이 구성 파일을 구성해야 할 수 있습니다.

- clusterIdentity.xml
- core-site.xml
- gateway.xml
- hbase-env.xml
- hbase-site.xml
- hdfs-site.xml
- hive-env.xml
- hive-site.xml
- mapred-site
- oozie-site.xml
- oozie-env.xml
- storm-site.xml
- tez-site.xml
- webhcat-site.xml
- yarn-site.xml

클러스터는 재이미징하므로 변경 내용을 유지할 수 없습니다. 재이미징에 대한 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)을 참조하세요. 클러스터의 수명 동안 변경 내용을 유지하려면 생성 프로세스 중에 HDInsight 클러스터 사용자 지정을 사용할 수 있습니다. 클러스터의 구성을 변경하고 이러한 Azure 이미지로 다시 설치 다시 부팅 다시 시작 이벤트에서 구성을 유지하려면 이 방법을 사용하는 것이 좋습니다. 구성 변경 내용은 서비스가 시작되기 전에 적용되므로 서비스를 다시 시작할 필요가 없습니다.

Bootstrap을 사용하는 방법은 3가지가 있습니다.

- Azure PowerShell 사용

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- .NET SDK 사용
- Azure Resource Manager 템플릿 사용

만든 시간 동안 HDInsight 클러스터에 추가 구성 요소 설치에 대한 내용은 다음을 참조하세요.

- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Windows)](hdinsight-hadoop-customize-cluster.md)

## Azure PowerShell 사용

다음 PowerShell 코드는 Hive 구성을 사용자 지정합니다.

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

완전히 작동하는 PowerShell 스크립트는 [부록 A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample)에서 찾을 수 있습니다.

**변경을 확인하려면:**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **찾아보기**를 클릭한 다음 **HDInsight 클러스터**를 클릭합니다.
3. PowerShell 스크립트를 사용하여 방금 만든 클러스터를 클릭합니다.
4. 블레이드 맨 위에서 **대시보드**를 클릭하여 Ambari UI를 엽니다.
5. 왼쪽 메뉴에서 **Hive**를 클릭합니다.
6. **요약**에서 **HiveServer2**를 클릭합니다.
7. **Configs** 탭을 클릭합니다.
8. 왼쪽 메뉴에서 **Hive**를 클릭합니다.
9. **고급** 탭을 클릭합니다.
10. 아래로 스크롤한 다음 **고급 hive 사이트**를 확장합니다.
11. 섹션에서 **hive.metastore.client.socket.timeout**을 찾습니다.

다른 구성 파일을 사용자 지정하는 추가 샘플:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

자세한 내용은 Azim Uddin의 [HDInsight 클러스터 만들기 사용자 지정](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) 블로그를 참조하세요.

## .NET SDK 사용

[.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap)를 참조하세요.

## Resource Manager 템플릿 사용

Resource Manager 템플릿에서 부트스트랩을 사용할 수 있습니다.

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop 사용자 지정 클러스터 부트스트랩 azure resource manager 템플릿](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## 참고 항목

- [HDInsight의 Hadoop 클러스터 만들기][hdinsight-provision-cluster]에서는 다른 사용자 지정 옵션을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침을 제공합니다.
- [HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]
- [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-install-r]
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)
- [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "클러스터를 만드는 동안의 단계"

## 부록 A: PowerShell 샘플

이 PowerShell 스크립트는 HDInsight 클러스터를 만들고 Hive 설정을 사용자 지정합니다.

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---HONumber=AcomDC_0914_2016-->