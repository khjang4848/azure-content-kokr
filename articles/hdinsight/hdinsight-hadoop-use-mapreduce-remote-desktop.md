<properties
   pageTitle="HDInsight에서 Hadoop과 MapReduce 및 원격 데스크톱 사용 | Microsoft Azure"
   description="원격 데스크톱을 사용하여 HDInsight에서 Hadoop에 연결하여 MapReduce 작업을 실행하는 방법을 알아봅니다."
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
   ms.date="07/12/2016"
   ms.author="larryfr"/>

# 원격 데스크톱을 사용하는 HDInsight에서 Hadoop과 MapReduce 사용

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 원격 데스크톱을 사용하여 HDInsight 클러스터에서 Hadoop에 연결한 다음 Hadoop 명령을 사용하여 MapReduce 작업을 실행하는 방법을 배웁니다.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Windows 기반 HDInsight(HDInsight의 Hadoop) 클러스터

* Windows 10, Window 8 또는 Windows 7을 실행하는 클라이언트 컴퓨터

##<a id="connect"></a>원격 데스크톱을 사용하여 연결

HDInsight 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)의 지침에 따라 연결합니다.

##<a id="hadoop"></a>Hadoop 명령 사용

HDInsight 클러스터에 대한 데스크톱에 연결된 후 Hadoop 명령을 사용하여 MapReduce 작업을 실행하려면 다음 단계를 따르세요.

1. HDInsight 데스크톱에서 **Hadoop 명령줄**을 시작합니다. **c:\\apps\\dist\\hadoop-&lt;version number>** 디렉터리에서 새 명령 프롬프트가 열립니다.

	> [AZURE.NOTE] 버전 번호는 Hadoop를 업데이트할 때 변경됩니다. 해당 경로를 찾으려면 **HADOOP\_HOME** 환경 변수를 사용할 수 있습니다. 예를 들어 `cd %HADOOP_HOME%`은 버전 번호를 알 필요 없이 디렉터리를 Hadoop 디렉터리로 변경합니다.

2. **Hadoop** 명령을 사용하여 예제 MapReduce 작업을 실행하려면 다음 명령을 사용합니다.

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

	이 명령은 현재 디렉터리에 있는 **hadoop-mapreduce-examples.jar** 파일의 **wordcount** 클래스를 시작합니다. 입력으로 **wasbs://example/data/gutenberg/davinci.txt** 문서를 사용하며, 출력은 **wasbs:///example/data/WordCountOutput**에 저장됩니다.

	> [AZURE.NOTE] 이 MapReduce 작업 및 예제 데이터에 대한 자세한 내용은 <a href="hdinsight-use-mapreduce.md">HDInsight Hadoop에서 MapReduce 사용</a>을 참조하세요.

2. 작업이 처리되는 동안 세부 정보를 내보내며 마지막으로 작업이 완료될 때 반환 정보는 다음과 유사합니다.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 작업이 완료되면 다음 명령을 사용하여 **wasbs://example/data/WordCountOutput**에 저장된 출력 파일을 나열합니다.

		hadoop fs -ls wasbs:///example/data/WordCountOutput

	**\_SUCCESS** 및 **part-r-00000**이라는 두 개의 파일이 표시됩니다. **part-r-00000** 파일은 이 작업에 대한 출력을 포함합니다.

	> [AZURE.NOTE] 일부 MapReduce 작업은 여러 **part-r-#####** 파일로 결과를 분할할 수 있습니다. 그럴 경우 ##### 접미사가 파일의 순서를 나타냅니다.

4. 출력을 보려면 다음 명령을 사용합니다.

		hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

	**wasbs://example/data/gutenberg/davinci.txt** 파일에 포함된 단어 목록과 각 단어의 발생 횟수가 표시됩니다. 다음은 파일에 포함된 데이터의 예입니다.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Hadoop 명령은 HDInsight 클러스터에서 MapReduce 작업을 실행하고 작업 출력을 볼 수 있는 쉬운 방법을 제공합니다.

##<a id="nextsteps"></a>다음 단계

HDInsight의 MapReduce 작업에 대한 일반적인 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->