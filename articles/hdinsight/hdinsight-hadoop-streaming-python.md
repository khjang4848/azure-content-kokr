<properties
   pageTitle="HDInsight에서 Python MapReduce 작업 개발 | Microsoft Azure"
   description="Linux 기반 HDInsight 클러스터에서 Python MapReduce 작업을 만들고 실행하는 방법에 대해 알아봅니다."
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
   ms.date="07/27/2016"
   ms.author="larryfr"/>

#HDInsight용 Python 스트리밍 프로그램 개발

Hadoop은 MapReduce용 스트리밍 API를 제공합니다. 이 API를 사용하여 Java 이외의 언어로 map 및 reduce 함수를 작성할 수 있습니다. 이 문서에서는 MapReduce 작업을 수행하기 위해 Python을 사용하는 방법을 배웁니다.

> [AZURE.NOTE] 이 문서의 Python 코드는 Windows 기반 HDInsight 클러스터에서 사용할 수 있지만 이 문서의 단계는 Linux 기반 클러스터에 해당됩니다.

이 문서는 [Python으로 Hadoop MapReduce 프로그램 작성](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)에서 Michael Noll이 게시한 정보 및 예제를 기반으로 합니다.

##필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Linux 기반 Hadoop

* 텍스트 편집기

    > [AZURE.IMPORTANT] 텍스트 편집기에서 줄 끝으로 LF를 사용해야 합니다. CRLF를 사용하는 경우 Linux 기반 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 오류가 발생할 수 있습니다. 확실하지 않은 경우 [MapReduce 실행](#run-mapreduce) 섹션의 선택 단계를 사용하여 모든 CRLF를 LF로 변환합니다.

* Windows 클라이언트, PuTTY 및 PSCP 이러한 유틸리티는 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY 다운로드 페이지</a>에서 사용할 수 있습니다.

##단어 개수

이 예제에서는 mapper 및 reducer를 사용하여 기본 단어 수 계산을 구현합니다. mapper는 문장을 개별 단어로 끊고 reducer는 출력하기 위해 단어와 개수를 집계합니다.

다음 순서도는 map 및 reduce 단계가 어떻게 진행되는지 보여 줍니다.

![map reduce 그림](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Python을 사용하는 이유

Python은 범용, 상위 수준 프로그래밍 언어이며 다른 언어에 비해 더 적은 줄의 코드로 개념을 표현할 수 있습니다. 데이터 과학자들로부터 인터프린트가 자연스러우며 동적으로 입력할 수 있고, 세련된 구문을 가지고 있어서 빠른 응용 프로그램 개발에 적합한 프로토타입 언어로 인기를 얻고 있습니다.

Python은 모든 HDInsight 클러스터에 설치됩니다.

##MapReduce 스트리밍

Hadoop을 사용하면 작업에서 사용되는 map 및 reduce 논리를 포함하는 파일을 지정할 수 있습니다. map 및 reduce 논리에 대한 특정 요구 사항은 다음과 같습니다.

* **입력**: map 및 reduce 구성 요소는 STDIN에서 입력 데이터를 읽어야 합니다.

* **출력**: map 및 reduce 구성 요소는 STDOUT에 출력 데이터를 작성해야 합니다.

* **데이터 형식**: 소비되고 생성되는 데이터는 탭 문자로 구분하는 키/값 쌍이어야 합니다

Python은 STDIN에서 읽을 수 있는 **sys** 모듈 및 STDOUT에 출력하는 **print**를 사용하여 이러한 요구 사항을 쉽게 처리할 수 있습니다. 나머지 작업은 키와 값 사이에 탭(`\t`) 문자를 사용하여 데이터 서식을 지정하기만 하면 됩니다.

##mapper 및 reducer 만들기

mapper 및 reducer는 텍스트 파일이며 이 예제에서는 어떤 것이 어떤 작업을 수행하는지 명확하게 하기 위해 **mapper.py** 및 **reducer.py**를 사용합니다. 선택한 편집기를 사용하여 mapper와 reducer를 만들 수 있습니다.

###Mapper.py

**mapper.py**라는 새 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

코드를 읽어 보면 이 코드의 내용을 이해할 수 있습니다.

###Reducer.py

**reducer.py**라는 새 파일을 만들고 다음 코드를 그 내용으로 사용합니다.

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##파일 업로드

**mapper.py** 및 **reducer.py** 모두 실행하기 전에 클러스터의 헤드 노드에 있어야 합니다. 업로드하는 가장 쉬운 방법은 **scp**(Windows 클라이언트를 사용하는 경우 **pscp**)를 사용하는 것입니다.

**mapper.py** 및 **reducer.py**와 동일한 디렉터리에 있는 클라이언트에서 다음 명령을 사용 합니다. **username**은 SSH 사용자로, **clustername**은 클러스터의 이름으로 바꿉니다.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

로컬 시스템에서 헤드 노드로 파일이 복사됩니다.

> [AZURE.NOTE] SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다(예: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`).

##MapReduce 실행

1. SSH를 사용하여 클러스터에 연결합니다.

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE] SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다(예: `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`).

2. (선택 사항) mapper.py 및 reducer.py 파일을 만들 때 줄 끝으로 CRLF를 사용하는 텍스트 편집기를 사용하거나 편집기가 사용하는 줄 끝을 모르는 경우 다음 명령을 사용하여 mapper.py 및 reducer.py에서 CRLF를 LF로 변환합니다.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. 다음 명령을 사용하여 MapReduce 작업을 시작합니다.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

	다음은 명령어의 일부입니다.

	* **hadoop-streaming.jar**: 스트리밍 MapReduce 작업을 수행할 때 사용합니다. 제공하는 외부 MapReduce 코드로 Hadoop에 접속합니다.

	* **-files**: 이 MapReduce 작업에 필요한 Hadoop 특정 파일을 지정하고 모든 작업자 노드에 복사됩니다.

	* **-mapper**: Hadoop mapper로 사용될 파일을 지정합니다.

	* **-reducer**: Hadoop reducer로 사용할 파일을 지정합니다.

	* **-input**: 단어 수를 계산할 입력 파일을 지정합니다.

	* **-output**: 출력 내용을 작성할 디렉터리를 지정합니다.

		> [AZURE.NOTE] 이 명령어로 디렉터리를 새로 만들 수 있습니다.

작업을 시작하면 많은 **INFO** 문이 표시되며 마지막으로 **map** 및 **reduce** 작업 비율이 표시됩니다.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

작업이 완료되면 작업에 대한 상태 정보가 표시됩니다.

##출력 보기

작업이 완료되면 다음 명령을 사용하여 출력을 확인합니다.

	hdfs dfs -text /example/wordcountout/part-00000

그러면 단어의 목록과 단어가 나타나는 빈도가 표시됩니다. 다음은 출력 데이터의 예제입니다.

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##다음 단계

HDInsight에서 스트리밍 MapRedcue 작업을 사용하는 방법을 배웠으므로 이제 아래 링크를 사용하여 Azure HDInsight에서 작업하는 다른 방법을 살펴봅니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->