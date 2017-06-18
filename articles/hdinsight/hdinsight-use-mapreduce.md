<properties
   pageTitle="HDInsight의 Hadoop 및 MapReduce | Microsoft Azure"
   description="HDInsight 클러스터의 Hadoop에서 MapReduce 작업을 실행하는 방법을 알아봅니다. Java MapReduce 작업으로 구현된 기본 단어 개수 계산 작업을 실행합니다."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# HDInsight에서 Hadoop과 MapReduce 사용

[AZURE.INCLUDE [mapreduce 선택기](../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서 HDInsight 클러스터의 Hadoop에서 MapReduce작업을 실행하는 방법에 대해 배웁니다. Java MapReduce 작업으로 구현된 기본 단어 계산 작업을 실행합니다.

##<a id="whatis"></a>MapReduce는 무엇입니까?

Hadoop MapReduce는 방대한 양의 데이터를 처리하는 작업을 작성하기 위한 소프트웨어 프레임워크입니다. 입력된 데이터는 사용자 클러스터의 노드에 걸쳐 동시에 처리되는 독립적인 청크로 분할됩니다. MapReduce 작업은 두 함수로 구성됩니다:

* **매퍼**: 입력된 데이터를 소비하고 분석하며(일반적으로 필터 및 정렬 작업) 튜플을 내보냅니다.(키-값 쌍)
* **리듀서**: 매퍼에서 나온 튜플을 소배하고 매퍼 데이터에서 더 작고 결합된 결과를 생성하는 요약 작업을 수행합니다.

다음 다이어그램에서는 기본 단어 계산 MapReduce 작업 예제를 보여줍니다.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

이 작업의 출력은 분석된 텍스트에서 각 단어가 발생한 횟수입니다.

* 매퍼는 입력 텍스트의 각 줄을 입력으로 가져오고 해당 줄을 단어로 구분합니다. 단어 뒤에 1이 표시되는 단어가 발생할 때마다 키/값 쌍을 내보냅니다. 리듀서로 보내기 전에 출력이 정렬됩니다.

* 리듀서가 각 단어의 개별 발생 수를 합산하고 단어 뒤에 발생 합계가 포함된 단일 키/값 쌍을 내보냅니다.

MapReduce는 다양한 언어로 구현할 수 있습니다. Java는 가장 일반적인 구현으로 해당 문서의 데모 용도로 사용됩니다.

### Hadoop 스트리밍

Java 및 Java 가상 컴퓨터(예를 들어, Scalding 또는 Cascading)를 기반으로 하는 언어 또는 프레임 워크는 Java 응용 프로그램과 비슷한 MapReduce 작업으로 직접 실행할 수 있습니다. C# 또는 Python 같은 기타 또는 독립 실행형 실행 파일은 Hadoop 스트리밍을 사용해야 합니다.

Hadoop 스트리밍은 STDIN 및 STDOUT를 통해 매퍼 및 리듀서와 통신합니다. - 매퍼 및 리듀서는 STDIN에서 데이터 한 줄을 한번에 읽고 STDOUT에 출력을 작성합니다. 매퍼 및 리듀서가 읽거나 내보낸 각 줄은 탭 문자로 구분된 키/값 쌍의 형식이어야 합니다.

    [key]/t[value]

자세한 내용은 [Hadoop 스트리밍](http://hadoop.apache.org/docs/r1.2.1/streaming.html)을 참조하세요.

HDInsight에서 Hadoop 스트리밍을 사용하는 예는 다음을 참조하세요:

* [Python MapReduce 작업 개발](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>샘플 데이터 정보

이 예에서는 샘플 데이터로 HDInsight 클러스터에서 텍스트 문서로 제공되는 Leonardo Da Vinci의 노트북을 사용합니다.

샘플 데이터는 HDInsight가 Hadoop 클러스터의 기본 파일 시스템으로 사용하는 Azure Blob 저장소에 저장됩니다. HDInsight에서는 **wasb** 접두사를 사용하여 Blob 저장소에 저장된 파일에 액세스할 수 있습니다. 예를 들어 sample.log 파일에 액세스하려는 경우 다음 구문을 사용합니다.

	wasbs:///example/data/gutenberg/davinci.txt

Azure Blob 저장소가 HDInsight의 기본 저장소이므로 **/example/data/gutenberg/davinci.txt**를 사용하여 파일에 액세스할 수도 있습니다.

> [AZURE.NOTE] 이전 구문에서 **wasbs:///**는 HDInsight 클러스터의 기본 저장소 컨테이너에 저장된 파일에 액세스하는 데 사용됩니다. 클러스터를 프로비전할 때 추가 저장소 계정을 지정한 경우 이러한 계정에 저장된 파일에 액세스하려면 컨테이너 이름과 저장소 계정 주소를 지정하여 데이터에 액세스하면 됩니다. 예: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>예제 MapReduce 정보

이 예제에서 사용되는 MapReduce 작업은 **wasbs://example/jars/hadoop-mapreduce-examples.jar**에위치하며 HDInsight 클러스터와 제공됩니다. 여기에는 사용자가 **davinci.txt**를 대상으로 실행하는 단어 계산 예가 포함되어 있습니다.

> [AZURE.NOTE] HDInsight 2.1 클러스터에서 파일 위치는 **wasbs:///example/jars/hadoop-examples.jar**입니다.

다음은 참조용 단어 계산 MapReduce 작업의 Java 코드입니다:

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

고유한 MapReduce 작업을 작성하는 방법에 대한 지침은 [HDInsight용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce-linux.md)을 참조하세요.

##<a id="run"></a>MapReduce 실행

HDInsight는 다양한 메서드를 사용하여 HiveQL 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

| **사용 기능**... | **...다음을 수행합니다** | ...**클러스터 운영 체제**로 | ...**클라이언트 운영 체제**에서 |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) | **SSH**를 통한 Hadoop 명령 사용 | Linux | Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) | **REST**을 사용하여 작업을 원격으로 제출 | Linux 또는or Windows | Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | **Windows PowerShell**을 사용하여 작업을 원격으로 제출 | Linux 또는or Windows | Windows |
| [원격 데스크톱](hdinsight-hadoop-use-mapreduce-remote-desktop) | **원격 데스트톱**을 통한 Hadoop 명령 사용 | Windows | Windows |

##<a id="nextsteps"></a>다음 단계

MapReduce는 강력한 진단 기능을 제공하는 반면 익히기 어려울 수 있습니다. HDInsight에서 데이터로 작업하는 편리한 방법을 제공하는 Pig 및 Hive와 같은 기술 뿐만 아니라 MapReduce 응용 프로그램을 쉽게 정의할 수 있는 Java 기반 프레임 워크도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight용 Java MapReduce 프로그램 개발](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [HDInsight용 Python 스트리밍 MapReduce 프로그램 개발](hdinsight-hadoop-streaming-python.md)

* [HDInsight에서 Apache Hadoop을 사용하여 Scalding MapReduce 작업 개발](hdinsight-hadoop-mapreduce-scalding.md)

* [HDInsight에서 Hive 사용][hdinsight-use-hive]

* [HDInsight에서 Pig 사용][hdinsight-use-pig]

* [HDInsight 샘플 실행][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

<!---HONumber=AcomDC_0914_2016-->