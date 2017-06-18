<properties
   pageTitle="HDInsight에서 Hadoop과 MapReduce 및 SSH 연결 사용 | Microsoft Azure"
   description="SSH를 사용하여 HDInsight에서 Hadoop으로 MapReduce 작업을 실행하는 방법에 대해 알아봅니다."
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

# SSH를 사용하여 HDInsight에서 Hadoop과 MapReduce 사용

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 HDInsight 클러스터에서 SSH(보안 셸)를 사용하여 Hadoop에 연결하고 Hadoop 명령을 사용하여 MapReduce 작업을 제출하는 방법을 배웁니다.

> [AZURE.NOTE] 이미 익숙한 Linux 기반 Hadoop 서버를 사용하지만 HDInsight는 처음인 경우 [Linux 기반 HDInsight 팁](hdinsight-hadoop-linux-information.md)을 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Linux 기반 HDInsight(HDInsight의 Hadoop) 클러스터

* SSH 클라이언트. Linux, Unix 및 Mac 운영 체제는 SSH 클라이언트와 함께 제공됩니다. Windows 사용자는 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)와 같은 클라이언트를 다운로드해야 합니다.

##<a id="ssh"></a>SSH를 사용하여 연결

SSH 명령을 사용하여 HDInsight 클러스터의 FQDN(정규화된 도메인 이름)에 연결합니다. FQDN은 지정한 클러스터 이름과 그 뒤에 오는 **.azurehdinsight.net**으로 구성됩니다. 예를 들어 다음 명령은 **myhdinsight**라는 클러스터에 연결합니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증을 위해 인증서 키를 제공한 경우** HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다. 예를 들면 다음과 같습니다.

	ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증을 위해 암호를 제공한 경우** HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 제공해야 합니다.

HDInsight에서 SSH 사용에 대한 자세한 내용은 [Linux, OS X 및 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

###PuTTY(Windows 클라이언트)

Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

##<a id="hadoop"></a>Hadoop 명령 사용

1. HDInsight 클러스터에 연결되면 다음 **Hadoop** 명령을 사용하여 MapReduce 작업을 시작합니다.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	이 명령은 **hadoop-mapreduce-examples.jar** 파일에 포함된 **wordcount** 클래스를 시작합니다. 입력으로 **wasbs://example/data/gutenberg/davinci.txt** 문서를 사용하며, 출력은 **wasbs:///example/data/WordCountOutput**에 저장됩니다.

	> [AZURE.NOTE] 이 MapReduce 작업 및 예 데이터에 대한 자세한 내용은 [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

2. 작업이 처리되는 동안 세부 정보를 내보내며 마지막으로 작업이 완료될 때 반환 정보는 다음과 유사합니다.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. 작업이 완료되면 다음 명령을 사용하여 **wasbs://example/data/WordCountOutput**에 저장된 출력 파일을 나열합니다.

		hdfs dfs -ls wasbs:///example/data/WordCountOutput

	**\_SUCCESS** 및 **part-r-00000**이라는 두 개의 파일이 표시됩니다. **part-r-00000** 파일은 이 작업에 대한 출력을 포함합니다.

	> [AZURE.NOTE] 일부 MapReduce 작업은 여러 **part-r-#####** 파일로 결과를 분할할 수 있습니다. 그럴 경우 ##### 접미사가 파일의 순서를 나타냅니다.

4. 출력을 보려면 다음 명령을 사용합니다.

		hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

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