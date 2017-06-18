<properties
	pageTitle="HDInsight의 Hadoop을 사용하여 비행 지연 데이터 분석 | Microsoft Azure"
	description="하나의 Windows PowerShell 스크립트를 사용하여 HDInsight 클러스터를 만들고, Hive 작업을 실행하고, Sqoop 작업을 실행하고, 클러스터를 삭제하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
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

#HDInsight의 Hive를 사용하여 비행 지연 데이터 분석

Hive에서는 대규모 데이터의 요약, 쿼리, 분석에 적용할 수 있는 SQL 스타일 스크립트 언어인 *[HiveQL][hadoop-hiveql]*을 통해 Hadoop MapReduce 작업을 실행할 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계에는 Windows 기반 HDInsight 클러스터가 필요합니다. Linux 기반 클러스터를 사용하는 단계는 [HDInsight에서 Hive를 사용하여 비행 지연 데이터 분석(Linux)](hdinsight-analyze-flight-delay-data-linux.md)을 참조하세요.

Azure HDInsight의 주요 이점 중 하나는 데이터 저장소와 계산 기능을 분리할 수 있다는 것입니다. HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 일반적인 작업은 세 부분으로 구성되어 있습니다.

1. **Azure Blob 저장소에 데이터 저장.** 이 작업은 연속적인 프로세스일 수 있습니다. 예를 들어 날씨 데이터, 센서 데이터, 웹 로그 및 이 자습서의 경우 비행 지연 데이터를 Azure Blob 저장소에 저장할 수 있습니다.
2. **작업 실행.** 데이터를 처리해야 할 때는 Windows PowerShell 스크립트나 클라이언트 응용 프로그램을 실행하여 HDInsight 클러스터를 만들고 작업을 실행한 다음 클러스터를 삭제합니다. 작업의 출력 데이터는 Azure Blob 저장소에 저장되며 클러스터가 삭제된 후에도 그대로 유지됩니다. 따라서 사용한 데이터에 대해서만 비용을 지불하면 됩니다.
3. **Azure Blob 저장소에서 출력을 검색**하거나 이 자습서에 설명된 것처럼 데이터를 Azure SQL 데이터베이스로 내보냅니다.

아래 다이어그램에는 이 자습서에서 설명하는 시나리오와 구조가 나와 있습니다.

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**참고**: 다이어그램의 숫자는 섹션 제목에 해당합니다. **M**은 주 프로세스의 약어입니다. **A**는 부록 콘텐츠의 약어입니다.

자습서에서는 주로 Windows PowerShell 스크립트를 사용하여 다음을 수행하는 방법을 보여 줍니다.

- HDInsight 클러스터 만들기
- 클러스터에서 Hive 작업을 실행하여 공항에서의 평균 지연 계산. 비행 지연 데이터는 Azure Blob 저장소 계정에 저장됩니다.
- Sqoop작업을 실행하여 Azure SQL 데이터베이스로 Hive 작업 출력 내보내기
- HDInsight 클러스터 삭제

부록에서 비행 지연 데이터를 업로드하고, Hive 쿼리 문자열을 만들기/업로드하고, Azure SQL 데이터베이스에서 Sqoop 작업을 준비하기 위한 지침을 찾을 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계는 Windows 기반 HDInsight 클러스터를 대상으로 합니다. Linux 기반 클러스터를 사용하는 단계는 [HDInsight에서 Hive를 사용하여 비행 지연 데이터 분석(Linux)](hdinsight-analyze-flight-delay-data-linux.md)을 참조하세요.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**이 자습서에서 사용하는 파일**

이 자습서에서는 [Research and Innovative Technology Administration, Bureau of Transportation Statistics, 즉 RITA][rita-website](영문)의 항공사 비행 데이터 운항정시성을 사용합니다. 데이터의 복사본은 공용 Blob 액세스 권한을 사용하여 Azure Blob 저장소 컨테이너에 업로드되었습니다. PowerShell 스크립트의 일부는 데이터를 공용 blob 컨테이너에서 클러스터의 기본 blob 컨테이너로 복사합니다. HiveQL 스크립트도 같은 Blob 컨테이너에 복사합니다. 자체 저장소 계정으로 데이터를 가져오고 업로드하는 방법과 HiveQL 스크립트 파일을 만들고 업로드하는 방법을 알아보려면 [부록 A](#appendix-a)와 [부록 B](#appendix-b)를 참조하세요.

다음 표는 이 자습서에 사용된 파일을 보여 줍니다.

<table border="1">
<tr><th>파일</th><th>설명</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>실행할 Hive 작업에 사용되는 HiveQL 스크립트 파일입니다. 이 스크립트는 공용 Blob 액세스 권한을 사용하여 Azure Blob 저장소 계정에 업로드되었습니다. <a href="#appendix-b">부록 B</a>에는 이 파일을 준비하고 고유한 Azure Blob 저장소 계정에 업로드하는 방법에 대한 지침이 있습니다.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Hive 작업의 입력 데이터입니다. 이 데이터는 공용 액세스 권한을 사용하여 Azure Blob 저장소 계정에 업로드되었습니다. <a href="#appendix-a">부록 A</a>에는 데이터를 가져오기 고유한 Azure Blob 저장소 계정에 업로드하는 방법에 대한 지침이 있습니다.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Hive 작업의 출력 경로입니다. 기본 컨테이너를 사용하여 출력 데이터를 저장합니다.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>기본 컨테이너의 Hive 작업 상태 폴더입니다.</td></tr>
</table>


##클러스터 만들기 및 Hive/Sqoop 작업 실행

Hadoop MapReduce에서는 작업을 일괄 처리 방식으로 실행합니다. 가장 비용 효율적으로 Hive 작업을 실행하는 방법은 작업의 클러스터를 만들고 완료된 작업은 삭제하는 것입니다. 다음 스크립트에 전체 프로세스가 나와 있습니다. HDInsight 클러스터를 만들고 Hive 작업을 실행하는 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision] 및 [HDInsight에서 Hive 사용][hdinsight-use-hive]을 참조하세요.

**Azure PowerShell로 Hive 쿼리를 실행하려면**

1. [부록 C](#appendix-c)의 지침에 따라 Azure SQL 데이터베이스 및 Sqoop 작업 출력 테이블을 만듭니다.
3. Windows PowerShell ISE를 열고 다음 스크립트를 실행합니다.

		$subscriptionID = "<Azure Subscription ID>"
		$nameToken = "<Enter an Alias>" 
		
		###########################################
		# You must configure the follwing variables
		# for an existing Azure SQL Database
		###########################################
		$existingSqlDatabaseServerName = "<Azure SQL Database Server>"
		$existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
		$existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
		$existingSqlDatabaseName = "<Azure SQL Database name>"
		
		$localFolder = "E:\Tutorials\Downloads" # A temp location for copying files. 
		$azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
		
		###########################################
		# (Optional) configure the following variables
		###########################################
		
		$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

		$resourceGroupName = $namePrefix + "rg"
		$location = "EAST US 2"
		
		$HDInsightClusterName = $namePrefix + "hdi"
		$httpUserName = "admin"
		$httpPassword = "<Enter the Password>"
		
		$defaultStorageAccountName = $namePrefix + "store"
		$defaultBlobContainerName = $HDInsightClusterName # use the cluster name
		
		$existingSqlDatabaseTableName = "AvgDelays"
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
		
		$hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
		
		$jobStatusFolder = "/tutorials/flightdelays/jobstatus"
		
		###########################################
		# Login
		###########################################
		try{
			$acct = Get-AzureRmSubscription
		}
		catch{
			Login-AzureRmAccount
		}
		Select-AzureRmSubscription -SubscriptionID $subscriptionID
		
		###########################################
		# Create a new HDInsight cluster
		###########################################
		
		# Create ARM group
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
		
		# Create the default storage account
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
		
		# Create the default Blob container
		$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
		$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
		New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
		
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
			-DefaultStorageContainer $existingDefaultBlobContainerName 
		
		
		###########################################
		# Prepare the HiveQL script and source data
		###########################################
		
		# Create the temp location  
		New-Item -Path $localFolder -ItemType Directory -Force 
		
		# Download the sample file from Azure Blob storage
		$context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
		$blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
		#$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
		
		# Upload data to default container
		
		$azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
		
		Invoke-Expression -Command:$azcopycmd
		
		###########################################
		# Submit the Hive job
		###########################################
		Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
		$response = Invoke-AzureRmHDInsightHiveJob `
						-Files $hqlScriptFile `
						-DefaultContainer $defaultBlobContainerName `
						-DefaultStorageAccountName $defaultStorageAccountName `
						-DefaultStorageAccountKey $defaultStorageAccountKey `
						-StatusFolder $jobStatusFolder 
		
		write-Host $response
		
		
		###########################################
		# Submit the Sqoop job
		###########################################
		$exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
						-Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $hdinsightClusterName `
						-HttpCredential $httpCredential `
						-JobDefinition $sqoopDef #-Debug -Verbose
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-HttpCredential $httpCredential `
			-WaitTimeoutInSeconds 3600 `
			-Job $sqoopJob.JobId
		
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-HttpCredential $httpCredential `
			-DefaultContainer $existingDefaultBlobContainerName `
			-DefaultStorageAccountName $defaultStorageAccountName `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-JobId $sqoopJob.JobId `
			-DisplayOutputType StandardError
		
		###########################################
		# Delete the cluster
		###########################################
		Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. SQL 데이터베이스에 연결하고 AvgDelays 테이블에서 도시별 평균 비행 지연을 확인합니다.

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>부록 A - Azure Blob 저장소에 비행 지연 데이터 업로드
데이터 파일과 HiveQL 스크립트 파일을 업로드하려면([부록 B](#appendix-b) 참조) 약간의 계획이 필요합니다. 그 계획은 HDInsight 클러스터를 만들고 Hive 작업을 실행하기 전에 데이터 파일과 HiveQL 파일을 저장하는 것입니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- **HDInsight 클러스터에서 기본 파일 시스템으로 사용하는 것과 같은 Azure 저장소 계정을 사용합니다.** HDInsight 클러스터에는 저장소 계정 액세스 키가 포함되므로 추가로 변경할 필요가 없습니다.
- **HDInsight 클러스터 기본 파일 시스템과 다른 Azure 저장소 계정을 사용합니다.** 이 경우 [HDInsight 클러스터 만들기 및 Hive/Sqoop 작업 실행](#runjob)에 있는 Windows PowerShell 스크립트의 생성 부분을 수정하여 저장소 계정을 추가 저장소 계정으로서 연결해야 합니다. 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision]를 참조하세요. 그러면 HDInsight 클러스터가 저장소 계정의 액세스 키를 인식합니다.

>[AZURE.NOTE] 데이터 파일의 Blob 저장소 경로는 HiveQL 스크립트 파일에 하드 코드됩니다. 수정 내용에 따라 이를 업데이트해야 합니다.

**비행 데이터를 다운로드하려면**

1. [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website](영문)로 이동합니다.
2. 페이지에서 다음 값을 선택합니다.

	<table border="1">
	<tr><th>이름</th><th>값</th></tr>
	<tr><td>Filter Year</td><td>2013 </td></tr>
	<tr><td>Filter Period</td><td>January</td></tr>
	<tr><td>필드</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay*(다른 모든 필드는 선택하지 않음)</td></tr>
	</table>

3. **다운로드**를 클릭합니다.
4. 압축 파일을 **C:\\Tutorials\\FlightDelays\\2013Data** 폴더에 풉니다. 각 파일은 CSV 파일이며 크기가 60GB 정도입니다.
5.	파일 이름을 데이터가 포함된 달로 변경합니다. 예를 들어 1월 데이터가 포함된 파일의 이름은 *January.csv*가 됩니다.
6. 2단계와 5단계를 반복하여 2013년의 12개월에 해당하는 각 파일을 다운로드합니다. 자습서를 실행하려면 파일이 하나 이상 있어야 합니다.

**Azure Blob 저장소에 비행 지연 데이터를 업로드하려면**

1. 매개 변수를 준비합니다.

	<table border="1">
	<tr><th>변수 이름</th><th>참고 사항</th></tr>
	<tr><td>$storageAccountName</td><td>데이터를 업로드할 Azure 저장소 계정입니다.</td></tr>
	<tr><td>$blobContainerName</td><td>데이터를 업로드할 Blob 컨테이너입니다.</td></tr>
	</table>
2. Azure PowerShell ISE를 엽니다.
3. 다음 스크립트를 스크립트 창에 붙여 넣습니다.

		[CmdletBinding()]
		Param(

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
		$destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
			foreach ($item in Get-ChildItem -Path $localFolder){
				$fileName = "$localFolder\$item"
				$blobName = "$destFolder/$item"
		
				Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
				Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
			}
		}
		else
		{
			Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion




4. **F5** 키를 눌러 스크립트를 실행합니다.

다른 메서드를 사용하여 파일을 업로드하도록 선택한 경우에는 파일 경로가 tutorials/flightdelay/data여야 합니다. 파일을 액세스하는 구문은 다음과 같습니다.

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

tutorials/flightdelay/data 경로는 파일을 업로드했을 때 만든 가상 폴더입니다. 달마다 하나씩 12개의 파일이 있는지 확인합니다.

>[AZURE.NOTE] 새 위치에서 읽으려면 Hive 쿼리를 업데이트해야 합니다.

> 컨테이너 액세스 권한을 공용으로 구성하거나 저장소 계정을 HDInsight 클러스터에 바인딩해야 합니다. 그렇지 않으면 Hive 쿼리 문자열이 데이터 파일에 액세스할 수 없습니다.

---
##<a id="appendix-b"></a>부록 B - HiveQL 스크립트 만들기 및 업로드

Azure PowerShell을 사용하여 여러 HiveQL 문을 한 번에 하나씩 실행하거나 HiveQL 문을 스크립트 파일에 패키지할 수 있습니다. 이 섹션에서는 HiveQL 스크립트를 만든 다음 Azure PowerShell을 사용하여 Azure Blob 저장소에 업로드하는 방법을 보여 줍니다. Hive에서는 HiveQL 스크립트를 Azure Blob 저장소에 저장해야 합니다.

HiveQL 스크립트는 다음을 수행합니다.

1. **delays\_raw 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
2. **delays\_raw 외부 Hive 테이블을 만듭니다**. 이 테이블은 비행 지연 파일이 있는 Blob 저장소 위치를 가리킵니다. 이 쿼리는 필드는 ","로 구분되고 줄은 "\\n"로 끝나도록 지정합니다. Hive가 필드 구분 기호인 쉼표와 필드 값(ORIGIN\_CITY\_NAME 및 DEST\_CITY\_NAME에 대한 필드 값의 경우)의 일부인 쉼표를 구분할 수 없기 때문에 필드 값에 쉼표가 포함될 경우 문제가 발생합니다. 이 문제를 해결하기 위해 쿼리는 TEMP 열을 만들어 열에 잘못 분할된 데이터를 저장합니다.
3. **delays 테이블을 삭제합니다**. 해당 테이블이 이미 있는 경우에 삭제합니다.
4. **delays 테이블을 만듭니다**. 더 처리하기 전에 데이터를 정리하는 데 도움이 됩니다. 이 쿼리는 delays\_raw 테이블에서 새 테이블인 *delays*를 만듭니다. TEMP 열(앞에서 언급함)은 복사되지 않으며, **substring** 함수는 해당 데이터에서 따옴표를 제거하는 데 사용됩니다.
5. **평균 날씨 지연을 계산하고 그 결과를 도시 이름별로 그룹화합니다.** 또한 결과를 Blob 저장소에 출력합니다. 쿼리에서 데이터의 아포스트로피가 제거되고 **weather\_delay**의 값이 null인 행은 제외됩니다. 이 작업은 이 자습서의 뒷부분에서 사용되는 Sqoop가 기본적으로 이러한 값을 정상적으로 처리하지 않기 때문에 필요합니다.

HiveQL 명령의 전체 목록을 보려면 [Hive 데이터 정의 언어][hadoop-hiveql](영문)를 참조하세요. 각 HiveQL 명령은 세미콜론으로 끝나야 합니다.

**HiveQL 스크립트 파일을 만들려면**

1. 매개 변수를 준비합니다.

	<table border="1">
	<tr><th>변수 이름</th><th>참고 사항</th></tr>
	<tr><td>$storageAccountName</td><td>HiveQL 스크립트를 업로드할 Azure 저장소 계정입니다.</td></tr>
	<tr><td>$blobContainerName</td><td>HiveQL 스크립트를 업로드할 Blob 컨테이너입니다.</td></tr>
	</table>
2. Azure PowerShell ISE를 엽니다.

3. 다음 스크립트를 복사하여 스크립트 창에 붙여 넣습니다.

		[CmdletBinding()]
		Param(

		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelay/data"
		$dstDataFolder = "/tutorials/flightdelay/output"
		#endregion

		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
			$isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
			if ($isDelete.ToLower() -ne "y")
			{
				Exit
			}
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
			Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
			new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
					-ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
				"YEAR string, " +
				"FL_DATE string, " +
				"UNIQUE_CARRIER string, " +
				"CARRIER string, " +
				"FL_NUM string, " +
				"ORIGIN_AIRPORT_ID string, " +
				"ORIGIN string, " +
				"ORIGIN_CITY_NAME string, " +
				"ORIGIN_CITY_NAME_TEMP string, " +
				"ORIGIN_STATE_ABR string, " +
				"DEST_AIRPORT_ID string, " +
				"DEST string, " +
				"DEST_CITY_NAME string, " +
				"DEST_CITY_NAME_TEMP string, " +
				"DEST_STATE_ABR string, " +
				"DEP_DELAY_NEW float, " +
				"ARR_DELAY_NEW float, " +
				"CARRIER_DELAY float, " +
				"WEATHER_DELAY float, " +
				"NAS_DELAY float, " +
				"SECURITY_DELAY float, " +
				"LATE_AIRCRAFT_DELAY float) " +
			"ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
			"LINES TERMINATED BY '\n' " +
			"STORED AS TEXTFILE " +
			"LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
			"SELECT YEAR AS year, " +
				"FL_DATE AS flight_date, " +
				"substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
				"substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
				"substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
				"ORIGIN_AIRPORT_ID AS origin_airport_id, " +
				"substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
				"substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
				"substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
				"DEST_AIRPORT_ID AS dest_airport_id, " +
				"substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
				"substring(DEST_CITY_NAME,2) AS dest_city_name, " +
				"substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
				"DEP_DELAY_NEW AS dep_delay_new, " +
				"ARR_DELAY_NEW AS arr_delay_new, " +
				"CARRIER_DELAY AS carrier_delay, " +
				"WEATHER_DELAY AS weather_delay, " +
				"NAS_DELAY AS nas_delay, " +
				"SECURITY_DELAY AS security_delay, " +
				"LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
			"FROM delays_raw;"
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
			"SELECT regexp_replace(origin_city_name, '''', ''), " +
				"avg(weather_delay) " +
			"FROM delays " +
			"WHERE weather_delay IS NOT NULL " +
			"GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	스크립트에서 사용되는 변수는 다음과 같습니다.

	- **$hqlLocalFileName** - 스크립트는 HiveQL 스크립트 파일을 Blob 저장소에 업로드하기 전에 로컬에 저장합니다. 이 상수가 해당 파일 이름입니다. 기본값은 <u>C:\\tutorials\\flightdelay\\flightdelays.hql</u>입니다.
	- **$hqlBlobName** - Azure Blob 저장소에서 사용되는 HiveQL 스크립트 파일 Blob 이름입니다. 기본값은 tutorials/flightdelay/flightdelays.hql입니다. 파일은 Azure Blob 저장소에 직접 기록되므로 Blob 이름 앞에는 "/"가 없습니다. Blob 저장소의 파일에 액세스하려면 파일 이름 앞에 "/"를 추가해야 합니다.
	- **$srcDataFolder** 및 **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"


---
##<a id="appendix-c"></a>부록 C - Azure SQL 데이터베이스에서 Sqoop 작업 출력 준비
**SQL 데이터베이스를 준비하려면(이 작업을 Sqoop 스크립트와 통합)**

1. 매개 변수를 준비합니다.

	<table border="1">
	<tr><th>변수 이름</th><th>참고 사항</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Azure SQL 데이터베이스 서버의 이름입니다. 새 서버를 만들려면 아무 내용도 입력하지 않습니다.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Azure SQL 데이터베이스 서버의 로그인 이름입니다. $sqlDatabaseServerName이 기본 서버이면 로그인 및 로그인 암호가 서버에서 인증하는 데 사용됩니다. 새 서버를 만드는 데도 사용됩니다.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Azure SQL 데이터베이스 서버의 로그인 암호입니다.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>이 값은 새 Azure 데이터베이스 서버를 만드는 경우에만 사용됩니다.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Sqoop 작업의 AvgDelays 테이블을 만드는 데 사용되는 SQL 데이터베이스입니다. 이 매개 변수를 비워 두면 HDISqoop라는 데이터베이스가 만들어집니다. Sqoop 작업 출력의 테이블 이름은 AvgDelays입니다. </td></tr>
	</table>
2. Azure PowerShell ISE를 엽니다.
3. 다음 스크립트를 복사하여 스크립트 창에 붙여 넣습니다.

		[CmdletBinding()]
		Param(
		
			# Azure Resource group variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
			[String]$resourceGroupName,
		
			# SQL database server variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseServer, 
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user.")]
			[String]$sqlDatabaseLogin,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user password.")]
			[String]$sqlDatabasePassword,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the region to create the Database in.")]
			[String]$sqlDatabaseLocation,   #For example, West US.
		
			# SQL database variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
					[origin_city_name] [nvarchar](50) NOT NULL,
					[weather_delay] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
					[origin_city_name] ASC
				)
				)"
		#endregion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#region - Create and validate Azure resouce group
		try{
			Get-AzureRmResourceGroup -Name $resourceGroupName
		}
		catch{
			New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
		}
		
		#EndRegion
		
		#region - Create and validate Azure SQL database server
		try{
			Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
		catch{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
		
			$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
			$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
		
			$sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
			Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
			Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
			$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
		
			#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
		}
		
		#endregion
		
		#region - Create and validate Azure SQL database
		
		try {
			Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		}
		catch {
			Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
			New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
		}
		
		#endregion
		
		#region -  Execute an SQL command to create the AvgDelays table
		
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
		
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] 스크립트는 REST(Representational State Transfer) 서비스(http://bot.whatismyipaddress.com)를 사용하여 외부 IP 주소를 검색합니다. IP 주소는 SQL 데이터베이스 서버용 방화벽 규칙을 만드는 데 사용됩니다.

	스크립트에서 사용되는 일부 변수는 다음과 같습니다.

	- **$ipAddressRestService** - 기본값은 http://bot.whatismyipaddress.com입니다. 외부 IP 주소를 가져오기 위한 공용 IP 주소 REST 서비스입니다. 원하는 경우 다른 서비스를 사용할 수 있습니다. 서비스를 통해 검색된 외부 IP 주소를 사용하여 Azure SQL 데이터베이스 서버용 방화벽 규칙을 만듭니다. 따라서 Windows PowerShell 스크립트를 사용하여 워크스테이션에서 데이터베이스에 액세스할 수 있습니다.
	- **$fireWallRuleName** - Azure SQL 데이터베이스 서버에 대한 방화벽 규칙의 이름입니다. 기본 이름은 <u>FlightDelay</u>입니다. 원하는 경우 이름을 바꿀 수 있습니다.
	- **$sqlDatabaseMaxSizeGB** - 이 값은 새 Azure SQL 데이터베이스 서버를 만드는 경우에만 사용됩니다. 기본 크기는 10GB입니다. 이 자습서에서는 크기가 10GB이면 충분합니다.
	- **$sqlDatabaseName** - 이 값은 새 Azure SQL 데이터베이스를 만드는 경우에만 사용됩니다. 기본값은 HDISqoop입니다. 이 상수의 이름을 바꾸는 경우에는 Sqoop Windows PowerShell 스크립트도 그에 따라 업데이트해야 합니다.

4. **F5** 키를 눌러 스크립트를 실행합니다.
5. 스크립트 출력의 유효성을 검사합니다. 스크립트가 정상적으로 실행되었는지 확인합니다.

##<a id="nextsteps"></a> 다음 단계
이제 파일을 Azure Blob 저장소로 업로드하는 방법, Azure Blob 저장소의 데이터를 사용하여 Hive 테이블을 채우는 방법, Hive 쿼리를 실행하는 방법, Sqoop을 사용하여 HDFS의 데이터를 Azure SQL 데이터베이스로 내보내는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 시작][hdinsight-get-started]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
* [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

<!---HONumber=AcomDC_0914_2016-->