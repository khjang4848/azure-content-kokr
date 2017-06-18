<properties
	pageTitle="스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정 | Microsoft Azure"
	description="스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="nitinme"/>

# 스크립트 작업을 사용하여 Windows 기반 HDInsight 클러스터 사용자 지정

**스크립트 동작**은 클러스터 생성 과정 중 클러스터에 추가 소프트웨어를 설치하기 위해 [사용자 지정 스크립트](hdinsight-hadoop-script-actions.md)를 호출하는 데 사용될 수 있습니다.

이 문서에 있는 정보는 Windows 기반 HDInsight 클러스터에 지정됩니다. Linux 기반 클러스터는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

HDInsight 클러스터를 사용자 지정하는 방법은 추가 Azure 저장소 계정 포함, hadoop 구성 파일(core-site.xml, hive-site.xml 등) 변경, 클러스터의 공통 위치에 공유 라이브러리(예: Hive, Oozie) 추가 등을 비롯해 다양합니다. 이러한 사용자 지정은 Azure PowerShell, Azure HDInsight .NET SDK 또는 Azure 포털을 통해 수행할 수 있습니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision-cluster]를 참조하세요.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## 클러스터 만들기 프로세스의 스크립트 작업

스크립트 작업은 클러스터를 만드는 프로세스 동안에만 사용됩니다. 다음 다이어그램에서는 만들기 프로세스 중 스크립트 작업을 실행할 때를 보여줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 만드는 동안의 단계][img-hdi-cluster-states]

스크립트가 실행 중이면 클러스터의**Cluster 사용자 지정** 단계가 시작됩니다. 이 단계에서 스크립트는 시스템 관리자 계정으로 클러스터의 지정된 모든 노드에서 병렬로 실행되며 해당 노드에서 모든 관리자 권한을 제공합니다.

> [AZURE.NOTE] **Cluster 사용자 지정** 단계 중 클러스터 노드에 대한 관리 권한이 있으므로 스크립트를 사용하여 Hadoop 관련 서비스를 비롯한 서비스의 중지 및 시작과 같은 작업을 수행할 수 있습니다. 따라서 스크립트의 일부로 스크립트 실행이 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다. 클러스터에서 이러한 서비스에 영향을 주는 구성을 변경하는 경우 제공되는 도우미 함수를 사용해야 합니다. 도우미 함수에 대한 자세한 내용은 [HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]을 참조하세요.

스크립트의 출력 및 오류 로그는 클러스터에 대해 지정한 기본 저장소 계정에 저장됩니다. 오류는 **u<\\cluster-name-fragment><\\time-stamp>setuplog**라는 이름으로 테이블에 저장됩니다. 클러스터의 모든 노드(헤드 노드 및 작업자 노드)에서 실행된 스크립트의 집계 로그입니다.

각 클러스터에서는 지정된 순서대로 호출되는 여러 스크립트 작업을 사용할 수 있습니다. 스크립트를 헤드 노드, 작업자 노드 또는 두 노드 모두에서 실행할 수 있습니다.

HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 여러 스크립트를 제공합니다.

이름 | 스크립트
----- | -----
**Spark 설치** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]을 참조하세요.
**R 설치** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. [HDInsight 클러스터에서 R 설치 및 사용][hdinsight-install-r]을 참조하세요.
**Solr 설치** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)을 참조하세요.
- **Giraph 설치** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install.md)을 참조하세요.
| **Hive 라이브러리 사전 로드** | https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1. [HDInsight 클러스터에서 Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md)를 참조하세요. |


## Azure 포털을 사용하여 스크립트 호출

**Azure 포털**

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 클러스터를 만들기 시작합니다.
2. 아래와 같이, 선택적 구성의 **스크립트 작업** 블레이드에 대해 **스크립트 동작 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다.</td></tr>
		<tr><td>헤드/작업자</td>
			<td>사용자 지정 스크립트가 실행되는 노드(**헤드** 또는 **작업자**)를 지정합니다.</b>
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다.</td></tr>
	</table>

	ENTER 키를 누르고 두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다.

3. **선택**을 클릭하여 스크립트 작업 구성을 저장하고 계속 클러스터를 만듭니다.

## Azure PowerShell을 사용하여 스크립트 호출

이 다음 PowerShell 스크립트는 Windows 기반 HDInsight 클러스터에 Spark를 설치하는 방법을 보여줍니다.

	# Provide values for these variables
	$subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

	$nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	
	$resourceGroupName = $namePrefix + "rg"
	$location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
	
	$hdinsightClusterName = $namePrefix + "spark"
	$httpUserName = "admin"
	$httpPassword = "<Enter a Password>"
	
	$defaultStorageAccountName = "$namePrefix" + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	
	#############################################################
	# Connect to Azure
	#############################################################
	
	Try{
		Get-AzureRmSubscription
	}
	Catch{
		Login-AzureRmAccount
	}
	Select-AzureRmSubscription -SubscriptionId $subscriptionID
	
	#############################################################
	# Prepare the dependent components
	#############################################################
	
	# Create resource group
	New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	
	# Create storage account
	New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
	$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
	New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
	
	#############################################################
	# Create cluster with ApacheSpark
	#############################################################
	
	# Specify the configuration options
	$config = New-AzureRmHDInsightClusterConfig `
				-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
				-DefaultStorageAccountKey $defaultStorageAccountKey 
				
	
	# Add a script action to the cluster configuration
	$config = Add-AzureRmHDInsightScriptAction `
				-Config $config `
				-Name "Install Spark" `
				-NodeType HeadNode `
				-Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
	
	# Start creating a cluster with Spark installed
	New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-Location $location `
			-ClusterSizeInNodes 2 `
			-ClusterType Hadoop `
			-OSType Windows `
			-DefaultStorageContainer $defaultBlobContainerName `
			-Config $config


다른 소프트웨어를 설치하려면 스크립트에서 스크립트 파일을 바꿔야 합니다.


메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

## .NET SDK를 사용하여 스크립트 호출 

다음 샘플은 Windows 기반 HDInsight 클러스터에서 Spark를 설치하는 방법을 보여줍니다. 다른 소프트웨어를 설치하려면 코드에서 스크립트 파일을 바꿔야 합니다.

**Spark로 HDInsight 클러스터를 만들려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

		Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Program.cs 파일에서 다음 using 문을 사용합니다.

		using System;
		using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. 클래스의 코드를 다음과 같이 둡니다.

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. **F5** 키를 눌러 응용 프로그램을 실행합니다.


## HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원
Microsoft Azure HDInsight 서비스는 Hadoop에 형성된 오픈 소스 기술의 에코시스템을 사용하여 클라우드에 빅 데이터 응용 프로그램을 빌드할 수 있는 유연한 플랫폼입니다. Microsoft Azure에서는 <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure 지원 FAQ 웹 사이트</a>의 **지원 범위** 섹션에 설명된 대로 일반적인 수준의 오픈 소스 기술을 제공합니다. HDInsight 서비스는 아래에 설명된 일부 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

- **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어, YARN ResourceManager, Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)</a>에 있습니다.
- **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

기본 제공 구성 요소는 완전히 지원되며, Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.

> [AZURE.WARNING] HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/)).

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 설치되고 사용되는 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음은 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법의 목록입니다.

1. 작업 제출 - 사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 기타 유형의 작업을 클러스터에 제출할 수 있습니다.
2. 클러스터 사용자 지정 - 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.
3. 샘플 - 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.

## 스크립트 작업 스크립트 개발

[HDInsight용 스크립트 작업 스크립트 개발][hdinsight-write-script]을 참조하세요.


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

<!---HONumber=AcomDC_0914_2016-->