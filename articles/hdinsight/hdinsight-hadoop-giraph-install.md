<properties
	pageTitle="HDInsight의 Hadoop 클러스터에 Giraph 설치 및 사용 | Microsoft Azure"
	description="Giraph를 사용하여 HDInsight 클러스터를 사용자 지정하는 방법 및 Giraph를 사용하는 방법을 알아봅니다."
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
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# HDInsight에서 Giraph 설치 및 사용


스크립트 동작을 사용하여 Giraph로 Windows 기반 HDInsight 클러스터를 사용자 지정하는 방법 및 대규모 그래프를 처리하기 위해 Giraph를 사용하는 방법을 알아봅니다. Linux 기반 클러스터와 함께 Giraph를 사용한 작업에 대한 자세한 내용은 [HDInsight Hadoop 클러스터에 Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)를 참조하세요.
 
*스크립트 작업*을 사용하여 Azure HDInsight에서 모든 형식의 클러스터(Hadoop, Storm, HBase, Spark)에 Giraph를 설치할 수 있습니다. HDInsight 클러스터에 Giraph를 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1))에서 다운로드할 수 있습니다. 샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

**관련된 문서**

- [HDInsight Hadoop 클러스터에 Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)

## Giraph 정의

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a>를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다. 그래프는 인터넷과 같은 대규모 네트워크의 라우터 간 연결, 소셜 네트워크(또는 소셜 그래프)상의 사람들 간 관계 등 개체 간의 관계를 모델링합니다. 그래프 처리를 통해 그래프의 개체 간 관계를 추론하여 다음을 수행할 수 있습니다.

- 현재 관계를 기반으로 하여 잠재적인 친구 파악.
- 네트워크의 두 컴퓨터 간에 가장 짧은 경로 파악.
- 웹 페이지의 페이지 순위 계산.


## 포털을 사용하여 Giraph 설치

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터를 만들기 시작합니다.
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다. 예: <b>Install Giraph</b>.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Giraph를 설치하는 스크립트는 매개 변수가 필요하지 않으므로 공백으로 둘 수 있습니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 만들기를 시작합니다.

## Giraph 사용

SimpleShortestPathsComputation 예제를 사용하여 그래프의 개체 간 가장 짧은 경로를 찾기 위한 기본 <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> 구현을 보여줍니다. 다음 단계에 따라 샘플 데이터와 샘플 jar을 업로드하고, SimpleShortestPathsComputation 예제를 사용하여 작업을 실행한 후 결과를 확인합니다.

1. 샘플 데이터 파일을 Azure Blob 저장소에 업로드합니다. 로컬 워크스테이션에서 **tiny\_graph.txt**라는 새 파일을 만듭니다. 이 파일은 다음 줄을 포함해야 합니다.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	tiny\_graph.txt 파일을 HDInsight 클러스터의 기본 저장소에 업로드합니다. 데이터를 업로드하는 방법은 [HDInsight에서 Hadoop 작업에 대한 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.

	이 데이터는 [source\_id, source\_value,[[dest\_id], [edge\_value],...]]의 형식을 사용하여 방향이 지정된 그래프의 개체 간 관계를 설명합니다. 각 줄은 **source\_id** 개체와 하나 이상의 **dest\_id** 개체 간 관계를 나타냅니다. **edge\_value**(또는 가중치)는 **source\_id**와 **dest\_id** 간 연결의 강도 또는 거리로 생각할 수 있습니다.

	개체 간 거리로 위의 값(또는 가중치)을 사용하여 그리면 다음과 같을 수 있습니다.

	![원과 거리가 다른 선으로 그린 tiny\_graph.txt](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. SimpleShortestPathsComputation 예제를 실행합니다. 다음 Azure PowerShell cmdlet에서 tiny\_graph.txt 파일을 입력으로 사용하여 예제를 실행합니다.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasbs:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasbs:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasbs:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments

		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	위의 예제에서는**clustername**을 Giraph가 설치된 HDInsight 클러스터의 이름으로 바꿉니다.

5. 결과를 확인합니다. 작업이 완료되면 결과가 __wasbs:///example/out/shotestpaths__ 폴더에 있는 두 출력 파일에 저장됩니다. 파일 이름은 __part-m-00001__ 및 __part-m-00002\_\_입니다. 다음 단계에 따라 다운로드하고 출력을 확인합니다.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	그러면 워크스테이션의 현재 디렉터리에 __example/output/shortestpaths__ 디렉터리 구조가 생성되고 해당 위치에 두 출력 파일이 다운로드됩니다.

	__Cat__ cmdlet을 사용하여 파일 내용을 표시합니다.

		Cat example/output/shortestpaths/part*

	출력은 다음과 유사합니다.


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation 예제는 개체 ID 1로 시작하도록 하드 코딩되며 다른 개체에 대한 가장 짧은 경로를 찾습니다. 따라서 출력은 `destination_id distance`이며, 여기서 distance는 가장자리를 기준으로 개체 ID 1과 대상 ID 간의 거리 값(또는 가중치)입니다.

	이를 시각화하면 ID 1과 다른 모든 개체 간의 가장 짧은 경로를 이동하여 결과를 확인할 수 있습니다. ID 1과 ID 4 간의 가장 짧은 경로는 5입니다. <span style="color:orange">ID 1과 3</span> 사이의 총 거리와 <span style="color:red">ID 3과 4</span> 사이의 총 거리를 더한 값입니다.

	![가장 짧은 경로와 함께 원으로 그린 개체](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## Aure PowerShell을 사용하여 Giraph 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)을 참조하세요. 샘플은 Azure PowerShell을 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)을 사용해야 합니다.

## .NET SDK을 사용하여 Giraph 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)을 참조하세요. 샘플은 .NET SDK를 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1)을 사용해야 합니다.


## 참고 항목

- [HDInsight Hadoop 클러스터에 Giraph 설치(Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]\: Spark 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 R 설치][hdinsight-install-r]\: R 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 Solr 설치](hdinsight-hadoop-solr-install.md): Solr 설치에 대한 스크립트 작업 샘플입니다.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0914_2016-->