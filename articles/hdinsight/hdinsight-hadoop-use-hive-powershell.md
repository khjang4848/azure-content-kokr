<properties
   pageTitle="HDInsight에서 PowerShell과 Hadoop Hive 사용 | Microsoft Azure"
   description="PowerShell을 사용하여 HDInsight의 Hadoop에서 Hive 쿼리 실행"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#PowerShell을 사용하여 Hive 쿼리 실행

[AZURE.INCLUDE [hive-선택기](../../includes/hdinsight-selector-use-hive.md)]

이 문서는 Azure 리소스 그룹 모드에서 Azure PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 Hive 쿼리를 실행하는 예제를 제공합니다.

> [AZURE.NOTE] 이 문서에는 예제에 사용된 HiveQL 문이 수행하는 작업에 대해 자세한 설명을 제공하지 않습니다. 이 예제에서 사용된 HiveQL에 대한 자세한 내용은 [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)을 참조하세요.


**필수 구성 요소**

이 문서의 단계를 완료하려면 다음이 필요합니다.

- **Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 기반 또는 Linux 기반)**
- **Azure PowerShell이 포함된 워크스테이션**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##Azure PowerShell을 사용하여 Hive 쿼리 실행

Azure PowerShell은 HDInsight에서 Hive 쿼리를 원격으로 실행할 수 있는 *cmdlet*을 제공합니다. 내부적으로는 HDInsight 클러스터에서 실행되는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)(이전의 Templeton)에 대한 REST 호출을 사용하여 이 작업을 수행합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 Hive 쿼리를 실행할 때 사용됩니다.

* **Add-AzureRmAccount**: Azure 구독에 대해 Azure PowerShell을 인증합니다.

* **New-AzureRmHDInsightHiveJobDefinition**: 지정한 HiveQL 문을 사용하여 새 *작업 정의*를 만듭니다.

* **Start-AzureRmHDInsightJob**: HDInsight로 작업 정의를 보내고, 작업을 시작하고, 작업 상태를 확인하는 데 사용할 수 있는 *작업* 개체를 반환합니다.

* **Wait-AzureRmHDInsightJob**: 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료되거나 대기 시간이 초과될 때까지 기다립니다.

* **Get-AzureRmHDInsightJobOutput**: 작업 출력을 검색하는 데 사용됩니다.

* **Invoke-AzureRmHDInsightHiveJob**: HiveQL 문을 실행하는 데 사용됩니다. 쿼리 차단을 완료한 다음 결과를 반환합니다.

* **Use-AzureRmHDInsightCluster**: **Invoke-AzureRmHDInsightHiveJob** 명령에 사용하기 위해 현재 클러스터를 설정합니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용하여 다음 코드를 **hivejob.ps1**로 저장합니다. **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

		#Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. 새 **Azure PowerShell** 명령 프롬프트를 엽니다. **hivejob.ps1** 파일의 디렉터리 위치를 변경한 다음 명령을 사용하여 스크립트를 실행합니다.

		.\hivejob.ps1

    스크립트를 실행할 때 클러스터에 대한 HTTPS/관리자 계정 자격 증명을 입력하라는 메시지가 표시됩니다. Azure 구독에 로그인하라는 메시지도 표시될 수 있습니다.
    
7. 작업이 완료되면 다음과 유사한 정보가 반환됩니다.

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 앞서 설명한 것처럼 **Invoke-hive**는 쿼리를 실행하고 응답을 기다리는 데 사용할 수 있습니다. 다음 명령을 사용하여 **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

	출력은 다음과 같이 표시됩니다.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] 더 긴 HiveQL 쿼리에는 Azure PowerShell **Here-Strings** cmdlet 또는 HiveQL 스크립트 파일을 사용할 수 있습니다. 다음 코드 조각은 **Invoke-Hive** cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 wasbs://에 업로드해야 합니다.
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> **Here-Strings**에 대한 자세한 내용은 <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings 사용</a>을 참조하세요.

##문제 해결

작업이 완료될 때 정보가 반환되지 않은 경우, 처리하는 동안 오류가 발생했을 수 있습니다. 이 작업에 대한 오류 정보를 보려면 **hivejob.ps1** 파일의 끝에 다음 내용을 추가하고 파일을 저장한 다음 다시 실행합니다.

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

이 명령은 작업을 실행할 때 서버의 STDERR에 기록된 정보를 반환하며 이 정보는 작업이 실패한 이유를 확인하는 데 도움이 될 수 있습니다.

##요약

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

##다음 단계

HDInsight의 Hive에 대한 일반적인 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->