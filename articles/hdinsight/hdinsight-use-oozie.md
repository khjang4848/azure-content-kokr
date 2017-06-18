<properties
	pageTitle="HDInsight에서 Hadoop Oozie 사용 | Microsoft Azure"
	description="빅데이터 서비스인 HDInsight에서 Hadoop Oozie를 사용하는 방법을 알아봅니다. 또한 Oozie 워크플로를 정의하고 Oozie 작업을 제출하는 방법에 대해서도 살펴봅니다."
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


# Hadoop과 함께 Oozie를 사용하여 HDInsight에서 워크플로 정의 및 실행

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

워크플로를 정의하고 HDInsight에서 워크플로를 실행하기 위해 Apache Oozie를 사용하는 방법을 알아보세요. Oozie 코디네이터에 대해 알아보려면 [HDInsight에서 시간 기준의 Oozie 코디네이터 사용][hdinsight-oozie-coordinator-time]을 참조하세요. Azure 데이터 팩터리를 알아보려면 [데이터 팩터리에서 Pig 및 Hive 사용][azure-data-factory-pig-hive]을 참조하세요.

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/코디네이션 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive, Apache Sqoop에 Hadoop 작업을 지원합니다. 또한 Java 프로그램이나 셸 스크립트와 같이 시스템에 고유한 작업을 예약하는 데 사용할 수 있습니다.

두 동작을 포함하는 해당 자습서의 지침에 따라 사용자가 구현할 워크플로입니다:

![워크플로 다이어그램][img-workflow-diagram]

1. log4j 파일에서 각 로그 수준 유형의 수를 계산하는 HiveQL 스크립트를 실행하는 Hive 작업. 각 log4j 파일은 유형과 심각도를 표시하는 [LOG LEVEL] 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어 다음과 같습니다.

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	Hive 스크립트 출력은 다음과 같습니다.

		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.

2.  HiveQL 출력을 Azure SQL 데이터베이스의 테이블에 내보내는 Sqoop 작업. Sqoop에 대한 자세한 내용은 [HDInsight에서 Hadoop Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

> [AZURE.NOTE] HDInsight 클러스터에서 지원되는 Oozie 버전에 대해서는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure PowerShell이 포함된 워크스테이션**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
    Windows PowerShell 스크립트를 실행하려면 관리자로서 실행하고 실행 정책을 *RemoteSigned*로 설정해야 합니다. 자세한 내용은 [Windows PowerShell 스크립트 실행][powershell-script]을 참조하세요.

##Oozie 워크플로 및 관련 HiveQL 스크립트 정의

Oozie 워크플로 정의는 hPDL(XML 프로세스 정의 언어)로 작성되었습니다. 기본 워크플로 파일 이름은 *workflow.xml*입니다. 다음은 이 자습서에서 사용할 워크플로 파일입니다.

	<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		<start to = "RunHiveScript"/>

		<action name="RunHiveScript">
			<hive xmlns="uri:oozie:hive-action:0.2">
				<job-tracker>${jobTracker}</job-tracker>
				<name-node>${nameNode}</name-node>
				<configuration>
					<property>
						<name>mapred.job.queue.name</name>
						<value>${queueName}</value>
					</property>
				</configuration>
				<script>${hiveScript}</script>
				<param>hiveTableName=${hiveTableName}</param>
				<param>hiveDataFolder=${hiveDataFolder}</param>
				<param>hiveOutputFolder=${hiveOutputFolder}</param>
			</hive>
			<ok to="RunSqoopExport"/>
			<error to="fail"/>
		</action>

		<action name="RunSqoopExport">
			<sqoop xmlns="uri:oozie:sqoop-action:0.2">
				<job-tracker>${jobTracker}</job-tracker>
				<name-node>${nameNode}</name-node>
				<configuration>
					<property>
						<name>mapred.compress.map.output</name>
						<value>true</value>
					</property>
				</configuration>
			<arg>export</arg>
			<arg>--connect</arg>
			<arg>${sqlDatabaseConnectionString}</arg>
			<arg>--table</arg>
			<arg>${sqlDatabaseTableName}</arg>
			<arg>--export-dir</arg>
			<arg>${hiveOutputFolder}</arg>
			<arg>-m</arg>
			<arg>1</arg>
			<arg>--input-fields-terminated-by</arg>
			<arg>"\001"</arg>
			</sqoop>
			<ok to="end"/>
			<error to="fail"/>
		</action>

		<kill name="fail">
			<message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		</kill>

		<end name="end"/>
	</workflow-app>

워크플로에 2가지 작업이 정의되어 있습니다. 시작 작업은 *RunHiveScript*입니다. 작업이 성공적으로 실행되면 다음 작업은 *RunSqoopExport*입니다.

RunHiveScript에는 몇 개의 변수가 있습니다. Azure PowerShell을 사용하여 워크스테이션에서 Oozie 작업을 제출할 때 이러한 값을 전달합니다.

<table border = "1">
<tr><th>워크플로 변수</th><th>설명</th></tr>
<tr><td>${jobTracker}</td><td>Hadoop 작업 추적기의 URL을 지정합니다. HDInsight 버전 2.1 및 3.0에서 <strong>jobtrackerhost: 9010</strong>를 사용하세요.</td></tr>
<tr><td>${nameNode}</td><td>Hadoop 이름 노드의 URL을 지정합니다. 기본 파일 시스템 주소를 사용하세요. 예: <i>wasbs://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>작업을 제출할 큐 이름을 지정합니다. <strong>기본</strong>을 사용하세요.</td></tr>
</table>

<table border = "1">
<tr><th>Hive 작업 변수</th><th>설명</th></tr>
<tr><td>${hiveDataFolder}</td><td>Hive Create Table 명령의 소스 디렉터리를 지정합니다.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE 문의 출력 폴더를 지정합니다.</td></tr>
<tr><td>${hiveTableName}</td><td>log4j 데이터 파일을 참조하는 Hive 테이블의 이름을 지정합니다.</td></tr>
</table>

<table border = "1">
<tr><th>Sqoop 작업 변수</th><th>설명</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>SQL 데이터베이스 연결 문자열을 지정합니다.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>데이터를 내보낼 SQL 데이터베이스 테이블을 지정합니다.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Hive INSERT OVERWRITE 문의 출력 폴더를 지정합니다. 이 폴더는 Sqoop 내보내기(내보내기 디렉터리)와 동일한 폴더입니다.</td></tr>
</table>

Oozie 워크플로 및 워크플로 작업 사용에 대한 자세한 내용은 [Apache Oozie 4.0 설명서][apache-oozie-400](HDInsight 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서][apache-oozie-332](HDInsight 버전 2.1의 경우)를 참조하세요.


워크플로의 Hive 작업은 HiveQL 스크립트 파일을 호출합니다. 이 스크립트 파일에는 세 개의 HiveQL 문이 포함되어 있습니다.

	DROP TABLE ${hiveTableName};
	CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
	INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **DROP TABLE 문**은 log4j Hive 테이블이 있는 경우 이 테이블을 삭제합니다.
2. **CREATE TABLE 문**은 log4j 로그 파일 위치를 가리키는 log4j Hive 외부 테이블을 만듭니다. 필드 구분 기호는 ","입니다. 기본 줄 구분 기호는 "\\n"입니다. Oozie 워크플로를 여러 번 실행하려는 경우 데이터 파일이 원래 위치에서 제거되지 않도록 하는 데 Hive 외부 테이블이 사용됩니다.
3. **INSERT OVERWRITE 문**은 log4j Hive 테이블에서 각 로그 수준 유형의 수를 계산하고 그 출력 결과를 Azure 저장소의 blob에 저장합니다.


스크립트에서 사용되는 3개의 변수가 있습니다.

- ${hiveTableName}
- ${hiveDataFolder}
- ${hiveOutputFolder}

워크플로 정의 파일(이 자습서에서는 workflow.xml)은 런타임 시 이러한 값을 이 HiveQL 스크립트에 전달합니다.

워크플로 파일과 HiveQL 파일은 모두 Blob 컨테이너에 저장되어 있습니다. 이 자습서의 뒷부분에서 사용할 PowerShell 스크립트는 이 두 파일을 기본 저장소 계정에 복사합니다.

##PowerShell을 사용하여 Oozie 작업 제출

Azure PowerShell은 Oozie 작업을 정의하는 데 현재 어떤 cmdlet도 제공하지 않습니다. **Invoke-RestMethod** cmdlet을 사용하여 Oozie 웹 서비스를 불러올 수 있습니다. Oozie 웹 서비스 API는 HTTP REST JSON API입니다. Oozie 웹 서비스 API에 대한 자세한 내용은 [Apache Oozie 4.0 설명서][apache-oozie-400](HDInsight 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서][apache-oozie-332](HDInsight 버전 2.1의 경우)를 참조하세요.

이 섹션의 PowerShell 스크립트는 다음 단계를 수행합니다.

1. Azure에 연결
2. Azure 리소스 그룹 만들기 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
3. Azure SQL 데이터베이스 서버, Azure SQL 데이터베이스 및 두 개의 테이블을 만듭니다. 이러한 작업은 워크플로의 Sqoop 작업에서 사용됩니다.

	테이블 이름은 *log4jLogCount*입니다.

4. Oozie 작업을 실행하는 데 사용되는 HDInsight 클러스터를 만듭니다.

	클러스터를 검사하려면 Azure 포털 또는 Azure PowerShell을 사용하면 됩니다.

5. Oozie 워크플로 파일 및 HiveQL 스크립트 파일을 기본 파일 시스템에 복사합니다.

	두 파일은 공용 Blob 컨테이너에 저장되어 있습니다.
	
	- HiveQL 스크립트(useoozie.hql)를 Azure storage에 복사합니다(wasbs:///tutorials/useoozie/useoozie.hql).
	- workflow.xml를 wasbs:///tutorials/useoozie/workflow.xml에 복사합니다.
	- 데이터 파일(/example/data/sample.log)을 wasbs:///tutorials/useoozie/data/sample.log에 복사합니다.
	 
6. Oozie 작업을 제출합니다.

	Oozie 작업 결과를 검사하려면 Visual Studio 또는 다른 도구를 사용하여 Azure SQL 데이터베이스에 연결합니다.

스크립트는 다음과 같습니다. Windows PowerShell ISE에서 스크립트를 실행할 수 있습니다. 처음 7개의 변수를 구성하기만 하면 됩니다.

	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure subscription ID>"
	
	# SQL Database server login credentials used for creating and connecting
	$sqlDatabaseLogin = "<Enter SQL Database Login Name>"
	$sqlDatabasePassword = "<Enter SQL Database Login Password>"
	
	# HDInsight cluster HTTP user credential used for creating and connectin
	$httpUserName = "admin"  # The default name is "admin"
	$httpPassword = "<Enter HDInsight Cluster HTTP User Password>"
	
	# Used for creating Azure service names
	$nameToken = "<Enter an Alias>"
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{
		Login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionId $subscriptionID
	}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $sqlLoginCredentials `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	#endregion
	
	#region - Create SQL database tables
	Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green
	
	$sqlDatabaseTableName = "log4jLogsCount"
	$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
			[Level] [nvarchar](10) NOT NULL,
			[Total] float,
		CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
		(
		[Level] ASC
		)
		)"
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jCountTable
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	#endregion
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName)[0].Value
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $HDInsightClusterName `
		-Location $location `
		-ClusterType Hadoop `
		-OSType Windows `
		-ClusterSizeInNodes 2 `
		-HttpCredential $httpCredential `
		-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - copy Oozie workflow and HiveQL files
	
	Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
	
	# Both files are stored in a public Blob
	$publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
	
	# WASB folder for storing the Oozie tutorial files.
	$destFolder = "tutorials/useoozie"  # Do NOT use the long path here
	
	Start-CopyAzureStorageBlob `
		-Context $publicBlobContext `
		-SrcContainer "useoozie" `
		-SrcBlob "useooziewf.hql"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-DestBlob "$destFolder/useooziewf.hql" `
		-Force
	
	Start-CopyAzureStorageBlob `
		-Context $publicBlobContext `
		-SrcContainer "useoozie" `
		-SrcBlob "workflow.xml"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-DestBlob "$destFolder/workflow.xml" `
		-Force
	
	#validate the copy
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/workflow.xml
	
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/useooziewf.hql
	
	#endregion
	
	#region - copy the sample.log file
	
	Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
	
	Start-CopyAzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-SrcContainer $defaultBlobContainerName `
		-SrcBlob "example/data/sample.log"  `
		-DestContext $defaultStorageAccountContext `
		-DestContainer $defaultBlobContainerName `
		-destBlob "$destFolder/data/sample.log" 
	
	#validate the copy
	Get-AzureStorageBlob `
		-Context $defaultStorageAccountContext `
		-Container $defaultBlobContainerName `
		-Blob $destFolder/data/sample.log
	
	#endregion
	
	#region - submit Oozie job
	
	$storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"
	
	$oozieJobName = $namePrefix + "OozieJob"
	
	#Oozie WF variables
	$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
	$waitTimeBetweenOozieJobStatusCheck=10
	
	#Hive action variables
	$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
	$hiveTableName = "log4jlogs"
	$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
	$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
	
	#Sqoop action variables
	$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
	$OoziePayload =  @"
	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	
	<property>
		<name>nameNode</name>
		<value>$storageUrI</value>
	</property>
	
	<property>
		<name>jobTracker</name>
		<value>jobtrackerhost:9010</value>
	</property>
	
	<property>
		<name>queueName</name>
		<value>default</value>
	</property>
	
	<property>
		<name>oozie.use.system.libpath</name>
		<value>true</value>
	</property>
	
	<property>
		<name>hiveScript</name>
		<value>$hiveScript</value>
	</property>
	
	<property>
		<name>hiveTableName</name>
		<value>$hiveTableName</value>
	</property>
	
	<property>
		<name>hiveDataFolder</name>
		<value>$hiveDataFolder</value>
	</property>
	
	<property>
		<name>hiveOutputFolder</name>
		<value>$hiveOutputFolder</value>
	</property>
	
	<property>
		<name>sqlDatabaseConnectionString</name>
		<value>";$sqlDatabaseConnectionString";</value>
	</property>
	
	<property>
		<name>sqlDatabaseTableName</name>
		<value>$SQLDatabaseTableName</value>
	</property>
	
	<property>
		<name>user.name</name>
		<value>$httpUserName</value>
	</property>
	
	<property>
		<name>oozie.wf.application.path</name>
		<value>$oozieWFPath</value>
	</property>
	
	</configuration>
	"@
	
	Write-Host "Checking Oozie server status..." -ForegroundColor Green
	$clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	$response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus
	
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$oozieServerSatus = $jsonResponse[0].("systemMode")
	Write-Host "Oozie server status is $oozieServerSatus."
	
	# create Oozie job
	Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	Write-Host "`n--------`n$OoziePayload`n--------"
	$clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
	$response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
	
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$oozieJobId = $jsonResponse[0].("id")
	Write-Host "Oozie job id is $oozieJobId..."
	
	# start Oozie job
	Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	$clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	$response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug
	
	# get job status
	Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	
	Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	$clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	$response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
	$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	$JobStatus = $jsonResponse[0].("status")
	
	while($JobStatus -notmatch "SUCCEEDED|KILLED")
	{
		Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
		Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		$response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
		$jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		$JobStatus = $jsonResponse[0].("status")
		$jobStatus
	}
	
	Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green
	
	#endregion


**자습서를 다시 실행하려면**

워크플로를 다시 실행하려면 다음을 삭제해야 합니다:

- Hive 스크립트 출력 파일
- log4jLogsCount 테이블의 데이터

다음은 사용할 수 있는 PowerShell 스크립트 샘플입니다.

	$resourceGroupName = "<AzureResourceGroupName>"
	
	$defaultStorageAccountName = "<AzureStorageAccountName>"
	$defaultBlobContainerName = "<ContainerName>"

	#SQL database variables
	$sqlDatabaseServerName = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"

	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
	$destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()

	$conn.close()

##다음 단계
이 자습서에서는 Oozie 워크플로를 정의하는 방법 및 PowerShell을 사용하여 Oozie 작업을 실행하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight에서 시간 기준의 Oozie 코디네이터 사용][hdinsight-oozie-coordinator-time]
- [휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop 사용 시작][hdinsight-get-started]
- [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]
- [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
- [HDInsight에서 Hadoop 작업용 데이터 업로드][hdinsight-upload-data]
- [HDInsight에서 Hadoop과 Sqoop 사용][hdinsight-use-sqoop]
- [HDInsight에서 Hadoop과 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Hadoop과 Pig 사용][hdinsight-use-pig]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/ko-KR/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=AcomDC_0914_2016-->