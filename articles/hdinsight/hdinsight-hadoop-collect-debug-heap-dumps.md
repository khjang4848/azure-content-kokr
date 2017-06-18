<properties
	pageTitle="힙 덤프를 사용하여 Hadoop 서비스 디버그 및 분석 | Microsoft Azure"
	description="자동으로 Hadoop 서비스에 대한 힙 덤프를 수집하고 디버깅 및 분석을 위해 Azure Blob 저장소 계정 내부에 배치합니다."
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


# Blob 저장소에서 힙 덤프를 수집하여 Hadoop 서비스 디버그 및 분석

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

힙 덤프는 덤프가 만들어질 당시의 변수 값을 비롯해 응용 프로그램의 메모리에 대한 스냅숏을 포함합니다. 따라서 런타임에 발생하는 문제를 진단하는 데 매우 유용합니다. Hadoop 서비스의 힙 덤프가 자동으로 수집되어 사용자 Azure Blob 저장소 계정 내의 HDInsightHeapDumps/ 아래에 저장될 수 있습니다.

개별 클러스터의 서비스에 대해 다양한 서비스의 힙 덤프 수집을 사용하도록 설정해야 합니다. 이 기능은 클러스터에 대해 기본적으로 해제됩니다. 이러한 힙 덤프는 크기가 클 수 있으므로 수집을 사용하도록 설정했으면 힙 덤프가 저장되는 Blob 저장소 계정을 모니터링하는 것이 좋습니다.

> [AZURE.NOTE] 이 문서의 정보는 Windows 기반 HDInsight에만 적용됩니다. Linux 기반 HDInsight에 대한 자세한 내용은 [Linux 기반 HDInsight에서 Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)을 참조하세요.

## 힙 덤프에 적합한 서비스

다음 서비스에 힙 덤프를 사용할 수 있습니다.

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## 힙 덤프를 사용하도록 설정하는 구성 요소

서비스에 대해 힙 덤프를 설정하려면 사용자가 **service\_name**에 지정된 해당 서비스의 섹션에 적절한 구성 요소를 설정해야 합니다.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service\_name**의 값은 위에 나열된 tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode 또는 namenode 서비스 중 하나일 수 있습니다.

## Azure PowerShell을 사용하여 사용

예를 들어 jobhistoryserver에 대해 Azure PowerShell을 사용하여 힙 덤프를 설정하려면 다음을 수행합니다.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## .NET SDK 사용하여 사용

예를 들어 jobhistoryserver에 대해 Azure HDInsight .NET SDK를 사용하여 힙 덤프를 설정하려면 다음을 수행합니다.

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=AcomDC_0914_2016-->