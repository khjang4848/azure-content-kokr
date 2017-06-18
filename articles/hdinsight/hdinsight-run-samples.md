<properties
	pageTitle="HDInsight에서 Hadoop 샘플 실행 | Microsoft Azure"
	description="제공된 샘플을 사용하여 Azure HDInsight 서비스 사용을 시작합니다. 또한 데이터 클러스터에 대해 MapReduce 프로그램을 실행하는 PowerShell 스크립트를 사용합니다."
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

#Windows 기반 HDInsight에서 Hadoop MapReduce 샘플 실행

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Azure HDInsight를 사용하여 Hadoop 클러스터에서 MapReduce 작업을 실행하는 데 도움이 되는 일련의 샘플이 제공됩니다. 이 샘플은 직접 만든 각 HDInsight의 관리되는 클러스터에 대해 사용할 수 있습니다. 이러한 샘플을 실행하면 Azure PowerShell cmdlet을 사용하여 Hadoop 클러스터에 대해 작업을 실행하는 방법을 알 수 있게 됩니다.

- [**단어 개수**][hdinsight-sample-wordcount]\: 텍스트 파일에 나오는 단어 수를 계산합니다.
- [**C# 스트리밍 단어 개수**][hdinsight-sample-csharp-streaming]\: Hadoop 스트리밍 인터페이스를 사용하여 텍스트 파일에 나오는 단어 수를 계산합니다.
- [**Pi 추정**][hdinsight-sample-pi-estimator]\: 통계(준난수 몬테카를로) 방법을 사용하여 Pi 값을 추정합니다.
- [**10GB Graysort**][hdinsight-sample-10gb-graysort]\: HDInsight를 사용하여 10GB 파일에 대해 일반적인 용도의 GraySort를 실행합니다. 실행할 작업에는 데이터를 생성하는 Teragen, 데이터를 정렬하는 Terasort, 데이터가 제대로 정렬되었는지 확인하는 Teravalidate의 세 가지가 있습니다.

>[AZURE.NOTE] 부록에서 소스 코드를 찾을 수 있습니다.

Hadoop 관련 기술(예: Java 기반 MapReduce 프로그래밍 및 스트리밍)에 대한 다양한 추가 설명서 및 Windows PowerShell 스크립팅에 사용되는 cmdlet에 대한 설명서를 웹에서 찾을 수 있습니다. 이러한 리소스에 대한 자세한 내용은 다음을 참조하세요.

- [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [HDInsight에서 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Azure HDInsight 소개][hdinsight-introduction]

오늘날에 많은 사람들이 MapReduce를 통한 Hive 및 Pig를 선택합니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
- [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
 
**필수 조건**:

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **HDInsight 클러스터**. 이러한 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md)를 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>단어 개수 - Java 

MapReduce 프로젝트를 제출하려면 먼저 MapReduce 작업 정의를 만듭니다. 작업 정의에서 MapReduce 프로그램 jar 파일 및 jar 파일이 있는 위치(여기서 **wasbs:///example/jars/hadoop-mapreduce-examples.jar**), 클래스 이름 및 인수를 지정합니다. 단어 개수 MapReduce 프로그램은 두 인수로, 단어를 계산하는 데 사용할 소스 파일과 출력 위치를 사용합니다.

[부록 A](#apendix-a---the-word-count-MapReduce-program-in-java)에서 소스 코드를 찾을 수 있습니다.

Java MapReduce 프로그램을 개발하는 절차는 [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce-linux.md)을 참조하세요.
 
**단어 개수 MapReduce 작업을 제출하려면**

1. **Windows PowerShell ISE**를 엽니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.
2. 다음 PowerShell 스크립트를 붙여 넣습니다.

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription -SubscriptionName $subscriptionName
		
		# Define the MapReduce job
		$mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$mrJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $mrJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $mrJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $mrJob.JobId `
			-DisplayOutputType StandardError

		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	MapReduce 작업에서 *part-r-00000*이라는 파일을 생성하며 단어와 개수를 포함합니다. 스크립트는 **findstr** 명령을 사용하여 *"there"*가 포함된 모든 단어를 나열합니다.

3. 처음 3개 변수를 설정하고 스크립트를 실행합니다.

## <a name="hdinsight-sample-csharp-streaming"></a>단어 개수 - C# 스트리밍

Hadoop은 맵을 작성하고 Java가 아닌 다른 언어의 함수를 줄일 수 있는 스트리밍 API를 MapReduce에 제공합니다.

> [AZURE.NOTE] 이 자습서의 단계는 Windows 기반 HDInsight 클러스터에만 적용됩니다. Linux 기반 HDInsight 클러스터를 스트리밍하는 예제는 [HDInsight용 Python 스트리밍 프로그램 개발](hdinsight-hadoop-streaming-python.md)을 참조하세요.

예제에서 mapper 및 reducer는 [stdin][stdin-stdout-stderr]에서 입력을 줄 단위로 읽고 출력을 [stdout][stdin-stdout-stderr]으로 내보내는 실행 파일입니다. 프로그램은 텍스트의 모든 단어 수를 계산합니다.

**매퍼**에 대해 실행 파일이 지정된 경우 각 매퍼 작업은 매퍼가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 mapper 작업은 입력을 줄로 변환하고 프로세스의 [stdin][stdin-stdout-stderr]에 줄을 공급합니다.

그동안 mapper는 프로세스의 stdout에서 줄 기반 출력을 수집합니다. 각 줄을 mapper의 출력으로 수집되는 키/값 쌍으로 변환합니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다. 줄에 탭 문자가 없는 경우에는 전체 줄이 키로 간주되고 값은 null입니다.

**리듀서**에 대해 실행 파일이 지정된 경우 각 리듀서 작업은 리듀서가 초기화될 때 개별 프로세스로 실행 파일을 시작합니다. 실행 시 reducer 작업은 입력 키/값 쌍을 줄로 변환하고 프로세스의 [stdin][stdin-stdout-stderr]에 줄을 공급합니다.

그동안 reducer는 프로세스의 [stdout][stdin-stdout-stderr]에서 줄 기반 출력을 수집합니다. 각 줄을 reducer의 출력으로 수집되는 키/값 쌍으로 변환합니다. 기본적으로 첫 번째 탭 문자까지 줄의 접두사는 키이고 줄의 나머지(탭 문자 제외)는 값입니다.

Hadoop 스트리밍 인터페이스에 대한 자세한 내용은 [Hadoop 스트리밍][hadoop-streaming](영문)을 참조하세요.

**C# 스트리밍 단어 개수 작업을 제출하려면**

- [단어 개수 - Java](#word-count-java)의 절차에 따라 작업 정의를 다음으로 바꿉니다.

		$mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
    							-Files "/example/apps/cat.exe","/example/apps/wc.exe" `
    							-Mapper "cat.exe" `
    							-Reducer "wc.exe" `
    							-InputPath "/example/data/gutenberg/davinci.txt" `
    							-OutputPath "/example/data/StreamingOutput/wc.txt"  


	출력 파일은 다음과 같습니다.
	
		example/data/StreamingOutput/wc.txt/part-00000		
								
## <a name="hdinsight-sample-pi-estimator"></a>PI 추정

Pi 추정은 통계(준난수 몬테카를로) 방법을 사용하여 Pi 값을 추정합니다. 단위 정사각형 내에 무작위로 놓인 점은 원의 영역과 같은 확률인 Pi/4로 해당 정사각형 내의 내접원 안에 들어갑니다. Pi의 값은 4R의 값에서 추정할 수 있습니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에 있는 점 개수의 비율입니다. 사용한 점 샘플이 크면 클수록 추정이 향상됩니다.

이 샘플을 위해 제공된 스크립트는 Hadoop jar 작업을 제출하고 하나의 값으로 16개의 맵을 실행하도록 설정되며, 각 맵은 매개 변수 값으로 천만 개의 샘플 점을 계산하는 데 필요합니다. 이러한 매개 변수 값은 Pi의 추정값을 개선하기 위해 변경할 수 있습니다. 참고로 Pi의 첫 소수점 이하의 10자리는 3.1415926535입니다.

**Pi 추정 작업을 제출하려면**

- [단어 개수 - Java](#word-count-java)의 절차에 따라 작업 정의를 다음으로 바꿉니다.

		$mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "pi" `
									-Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10GB Graysort

이 샘플에서는 비교적 빠르게 실행할 수 있도록 적절한 10GB의 데이터를 사용합니다. 또한 2009년에 0.578TB/분(173분에 100TB)의 속도로 연간 범용("daytona") 테라바이트 정렬 벤치마크를 획득한 Owen O'Malley 및 Arun Murthy가 개발한 MapReduce 응용 프로그램을 사용합니다. 이 정렬 벤치마크 및 다른 정렬 벤치마크에 대한 자세한 내용은 [정렬 벤치마크](http://sortbenchmark.org/)(영문) 사이트를 참조하십시오.

이 샘플에서는 세 가지 집합의 MapReduce 프로그램을 사용합니다.

1. **TeraGen**은 정렬할 데이터의 행을 생성하는 데 사용할 수 있는 MapReduce 프로그램입니다.
2. **TeraSort**는 입력 데이터를 샘플링하여 데이터를 전체 순서로 정렬하는 데 MapReduce를 사용합니다. TeraSort는 각 reduce의 키 범위를 정의하는 N-1 샘플 키의 정렬된 목록을 사용하는 사용자 지정 파티셔너를 제외하면 MapReduce 함수의 표준 정렬입니다. 특히, sample[i-1] <= key < sample[i]와 같은 모든 키는 reduce i로 전송됩니다. 이는 reduce i의 출력이 모두 reduce i+1의 출력보다 작다는 것을 보증합니다.
3. **TeraValidate**는 출력이 전역으로 정렬되는지 확인하는 MapReduce 프로그램입니다. 이 프로그램은 출력 디렉터리에 파일당 하나의 맵을 만들며 각 맵에서 각 키가 이전 키보다 작거나 같은지 확인합니다. 또한 map 함수는 각 파일의 첫 번째와 마지막 키의 레코드를 생성하며, reduce 함수는 파일 i의 첫 번째 키가 파일 i-1의 마지막 키보다 큰지 확인합니다. 모든 문제가 순서에서 벗어난 키와 함께 reduce의 출력으로 보고됩니다.

세 응용 프로그램 모두에 사용된 입력 및 출력 형식은 올바른 형식의 텍스트 파일을 읽고 씁니다. 벤치마크 콘테스트에서는 출력 데이터를 여러 노드로 복제할 필요가 없으므로 reduce의 출력에서 복제를 기본값인 3 대신 1로 설정했습니다.

소개 부분에서 설명한 각 MapReduce 프로그램에 해당하는 샘플에는 다음과 같은 세 가지 작업이 필요합니다.

1. **TeraGen** MapReduce 작업을 실행하여 정렬할 데이터를 생성합니다.
2. **TeraSort** MapReduce 작업을 실행하여 데이터를 정렬합니다.
3. **TeraValidate** MapReduce 작업을 실행하여 데이터가 올바르게 정렬되었는지 확인합니다.

**작업을 제출하려면**

- [단어 개수 - Java](#word-count-java)의 절차에 따라 작업 정의를 사용합니다.

	$teragen = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teragen" ` -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"
	
	$terasort = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "terasort" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"
	
	$teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teravalidate" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"


##다음 단계 

이 문서 및 각 샘플의 문서에서 HDInsight 클러스터에 포함된 샘플을 Azure PowerShell을 사용하여 실행하는 방법을 알아보았습니다. HDInsight에서 Pig, Hive 및 MapReduce를 사용하는 방법에 대한 자습서는 다음 항목을 참조하세요.

* [휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop 사용 시작][hdinsight-get-started]
* [HDInsight에서 Hadoop과 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hadoop과 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Hadoop 작업 제출][hdinsight-submit-jobs]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]
* [HDInsight에서 Hadoop 디버그: 오류 메시지][hdinsight-errors]


## 부록 A - 단어 개수 소스 코드

	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}

  	public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}


## 부록 B - 단어 개수 스트리밍 소스 코드

MapReduce 프로그램은 cat.exe 응용 프로그램을 매핑 인터페이스로 사용하여 텍스트를 콘솔로 스트리밍하고 wc.exe 응용 프로그램을 리듀싱 인터페이스로 사용하여 문서에서 스트리밍되는 단어 수를 계산합니다. mapper와 reducer는 둘 다 표준 입력 스트림(stdin)에서 문자를 줄 단위로 읽고 표준 출력 스트림(stdout)에 씁니다.

	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



cat.cs 파일의 mapper 코드는 [StreamReader][streamreader] 개체를 사용하여 들어오는 스트림의 문자를 콘솔로 읽어오며, 콘솔에서 정적 [Console.Writeline][console-writeline] 메서드를 사용하여 스트림을 표준 출력 스트림에 씁니다.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


wc.cs 파일의 리듀서 코드는 [StreamReader][streamreader] 개체를 사용하여 cat.exe 매퍼에 의해 출력된 표준 입력 스트림에서 문자를 읽습니다. [Console.Writeline][console-writeline] 메서드를 사용하여 문자를 읽고 공백 및 각 단어 끝에 있는 줄의 끝 문자를 계산하여 단어 수를 셉니다. 그런 다음 [Console.Writeline][console-writeline] 메서드를 사용하여 합계를 표준 출력 스트림에 작성합니다.





## 부록 C - Pi 추정 소스 코드

mapper 및 reducer 함수가 포함된 Pi 추정 Java 코드를 아래 검사에 사용할 수 있습니다. 매퍼 프로그램은 단위 정사각형 내에 무작위로 놓인 점의 지정된 개수를 생성한 후 원 내부에 있는 해당 점 개수를 계산합니다. reducer 프로그램은 mapper에서 계산된 점을 누적한 후 수식 4R에서 Pi의 값을 추정합니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에서 계산된 점 개수의 비율입니다.

 	/**
 	* Licensed to the Apache Software Foundation (ASF) under one
 	* or more contributor license agreements. See the NOTICE file
 	* distributed with this work for additional information
 	* regarding copyright ownership. The ASF licenses this file
 	* to you under the Apache License, Version 2.0 (the
 	* "License"); you may not use this file except in compliance
 	* with the License. You may obtain a copy of the License at
 	*
	* http://www.apache.org/licenses/LICENSE-2.0
 	*
 	* Unless required by applicable law or agreed to in writing, software
 	* distributed under the License is distributed on an "AS IS" BASIS,
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and
 	* limitations under the License.
 	*/

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi
	//using quasi-Monte Carlo method.
	//
	//Mapper:
	//Generate points in a unit square
	//and then count points inside/outside of the inscribed circle of the square.
	//
	//Reducer:
	//Accumulate points inside/outside results from the mappers.
	//Let numTotal = numInside + numOutside.
	//The fraction numInside/numTotal is a rational approximation of
	//the value (Area of the circle)/(Area of the square),
	//where the area of the inscribed circle is Pi/4
	//and the area of unit square is 1.
	//Then, Pi is estimated value to be 4(numInside/numTotal).
	//

 	public class PiEstimator extends Configured implements Tool {
	//tmp directory for input/output
 	static private final Path TMP_DIR = new Path(
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

	//2-dimensional Halton sequence {H(i)},
	//where H(i) is a 2-dimensional point and i >= 1 is the index.
	//Halton sequence is used to generate sample points for Pi estimation.
 	private static class HaltonSequence {
	// Bases
 	static final int[] P = {2, 3};
	//Maximum number of digits allowed
 	static final int[] K = {63, 40};

 	private long index;
 	private double[] x;
 	private double[][] q;
 	private int[][] d;

	//Initialize to H(startindex),
	//so the sequence begins with H(startindex+1).
 	HaltonSequence(long startindex) {
 	index = startindex;
 	x = new double[K.length];
 	q = new double[K.length][];
 	d = new int[K.length][];
 	for(int i = 0; i < K.length; i++) {
 	q[i] = new double[K[i]];
 	d[i] = new int[K[i]];
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;

 	for(int j = 0; j < K[i]; j++) {
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
 	d[i][j] = (int)(k % P[i]);
 	k = (k - d[i][j])/P[i];
 	x[i] += d[i][j] * q[i][j];
 	}
 	}
 	}

	//Compute next point.
	//Assume the current point is H(index).
	//Compute H(index+1).
	//@return a 2-dimensional point with coordinates in [0,1)^2
 	double[] nextPoint() {
 	index++;
 	for(int i = 0; i < K.length; i++) {
 	for(int j = 0; j < K[i]; j++) {
 	d[i][j]++;
 	x[i] += q[i][j];
 	if (d[i][j] < P[i]) {
 	break;
 	}
 	d[i][j] = 0;
 	x[i] -= (j == 0? 1.0: q[i][j-1]);
 	}
 	}
 	return x;
 	}
 	}

	//Mapper class for Pi estimation.
	//Generate points in a unit square and then
	//count points inside/outside of the inscribed circle of the square.
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.
	//@param offset samples starting from the (offset+1)th sample.
	//@param size the number of samples for this map
	//@param out output {ture->numInside, false->numOutside}
	//@param reporter
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
 	long numInside = 0L;
 	long numOutside = 0L;

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.
	//Accumulate points inside/outside results from the mappers.
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.
 	@Override
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.
	// @param isInside Is the points inside?
	// @param values An iterator to a list of point counts
	// @param output dummy, not used here.
	// @param reporter

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}

 	//Reduce task done, write output to a file.
 	@Override
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class,
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}

	//Run a map/reduce job for estimating Pi.
	//@return the estimated value of Pi.
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	)
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());

 	jobConf.setInputFormat(SequenceFileInputFormat.class);

 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);

 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);

 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);

 	// turn off speculative execution, because DFS doesn't handle
 	// multiple writers to the same file.
 	jobConf.setSpeculativeExecution(false);

 	//setup input/output directories
 	final Path inDir = new Path(TMP_DIR, "in");
 	final Path outDir = new Path(TMP_DIR, "out");
 	FileInputFormat.setInputPaths(jobConf, inDir);
 	FileOutputFormat.setOutputPath(jobConf, outDir);

 	final FileSystem fs = FileSystem.get(jobConf);
 	if (fs.exists(TMP_DIR)) {
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");
	 }
	 if (!fs.mkdirs(inDir)) {
	 throw new IOException("Cannot create input directory " + inDir);
	 }

	 //generate an input file for each map task
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);
	 final LongWritable offset = new LongWritable(i * numPoints);
	 final LongWritable size = new LongWritable(numPoints);
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }

	 //start a map/reduce job
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");

	 //read outputs
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }

	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }

	//Parse arguments and then runs a map/reduce job.
	//Print output in standard out.
	//@return a non-zero if there is an error. Otherwise, return 0.
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);

	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);

	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }

	 //main method for running it as a stand alone command.
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
	 
## 부록 D - 10gb graysort 소스 코드

이 섹션에서는 TeraSort MapReduce 프로그램 코드가 검사용으로 제시되었습니다.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }
	}














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx

<!---HONumber=AcomDC_0914_2016-->