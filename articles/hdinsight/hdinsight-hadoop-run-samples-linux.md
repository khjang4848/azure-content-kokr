<properties
	pageTitle="Linux 기반 HDInsight에서 Hadoop MapReduce 샘플을 실행 | Microsoft Azure"
	description="Linux 기반 HDInsight에서 MapReduce 샘플 사용 시작 SSH를 사용하여 클러스터에 연결한 다음 Hadoop 명령을 사용하여 샘플 작업을 실행합니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
    tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>




#HDInsight에서 Hadoop 샘플 실행

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Linux 기반 HDInsight 클러스터는 Hadoop MapReduce 작업 실행을 익히는 데 사용할 수 있는 MapReduce 샘플 집합을 제공합니다. 이 문서에서는 사용 가능한 샘플을 살펴보고 그 중 일부를 실행하는 과정을 안내합니다.

##필수 조건

- **Azure 구독**: [Azure 무료 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

- **Linux 기반 HDInsight 클러스터**: [Linux의 HDInsight에서 Hive와 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

- **SSH 클라이언트**: HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

    - [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

## 샘플 ##

**위치**: 샘플은 HDInsight 클러스터(**/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**)에 있습니다.

**내용**: 이 보관 파일에는 다음 샘플이 들어 있습니다.

- **aggregatewordcount**: 입력 파일의 단어 수를 계산하는 집계 기반 map/reduce 프로그램
- **aggregatewordhist**: 입력 파일의 단어 히스토그램을 계산하는 집계 기반 map/reduce 프로그램
- **bbp**: Bailey-Borwein-Plouffe를 사용하여 Pi의 정확한 소수 자릿수를 계산하는 map/reduce 프로그램
- **dbcount**: 데이터베이스에서 페이지 보기 횟수를 계산하는 예제 작업
- **distbbp**: BBP 형식의 수식을 사용하여 Pi의 정확한 비트를 계산하는 map/reduce 프로그램
- **grep**: 입력에서 정규식 일치 항목 수를 계산하는 map/reduce 프로그램
- **join**: 동일하게 분할되고 정렬된 데이터 집합의 조인에 영향을 주는 작업
- **multifilewc**: 여러 파일에서 단어 수를 계산하는 작업
- **pentomino**: pentomino 문제에 대한 해결 방법을 찾는 map/reduce 타일 배치 프로그램
- **pi**: 준난수 몬테카를로 방법을 사용하여 Pi를 추정하는 map/reduce 프로그램
- **randomtextwriter**: 노드당 10GB의 임의 텍스트 데이터를 기록하는 map/reduce 프로그램
- **randomwriter**: 노드당 10GB의 임의 데이터를 기록하는 map/reduce 프로그램
- **secondarysort**: reduce에 대한 보조 정렬을 정의하는 예제
- **sort**: 임의 기록기에 의해 기록된 데이터를 정렬하는 map/reduce 프로그램
- **sudoku**: sudoku 해 찾기
- **teragen**: terasort에 대한 데이터 생성
- **terasort**: terasort 실행
- **teravalidate**: terasort 결과 확인
- **wordcount**: 입력 파일의 단어 수를 계산하는 map/reduce 프로그램
- **wordmean**: 입력 파일의 평균 단어 길이를 계산하는 map/reduce 프로그램
- **wordmedian**: 입력 파일의 중앙 단어 길이를 계산하는 map/reduce 프로그램
- **wordstandarddeviation**: 입력 파일의 단어 길이 표준 편차를 계산하는 map/reduce 프로그램

**소스 코드**: 이러한 샘플에 대한 소스 코드는 HDInsight 클러스터(**/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**)에 포함되어 있습니다.

> [AZURE.NOTE] 경로의 `2.2.4.9-1`은 HDInsight 클러스터용 Hortonworks Data Platform의 버전이며 HDInsight 업데이트 시 변경될 수 있습니다.

## 샘플을 실행하는 방법 ##

1. 다음 문서에 설명된 대로 SSH를 사용하여 HDInsight에 연결합니다.

    - [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

2. `username@#######:~$` 프롬프트에서 다음 명령을 사용하여 샘플을 나열합니다.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    그러면 이 문서의 이전 섹션에 나와 있는 샘플 목록이 생성됩니다.

3. 특정 샘플에 대한 도움말을 보려면 다음 명령을 사용합니다. 이 명령은 **wordcount** 샘플에 적용됩니다.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    다음과 같은 메시지가 표시됩니다.

        Usage: wordcount <in> [<in>...] <out>

    이 메시지는 원본 문서에 대한 여러 입력 경로를 제공할 수 있음을 나타내며, 최종 경로는 출력(원본 문서의 단어 수)이 배치되는 위치입니다.

4. 다음을 사용하여 클러스터와 함께 샘플 데이터로 제공되는 Notebooks of Leonardo Da Vinci의 모든 단어 수를 계산할 수 있습니다.

    	yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    이 작업에 대한 입력은 **wasbs:///example/data/gutenberg/davinci.txt**에서 읽습니다.

    이 예의 출력은 **wasbs:///example/data/davinciwordcount**에 저장됩니다.

    > [AZURE.NOTE] Wordcount 샘플에 대한 도움말에서 설명했듯이 여러 입력 파일을 지정할 수도 있습니다. 예를 들어 `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount`는 davinci.txt와 ulysses.txt 모두에서 단어 수를 계산합니다.

5. 작업이 완료되면 다음 명령을 사용하여 결과를 확인합니다.

        hdfs dfs -cat /example/data/davinciwordcount/*

    이 명령은 작업에서 생성된 모든 출력 파일을 연결하고 표시합니다. 이 기본 예제에는 파일이 하나만 있지만 파일이 더 많은 경우 모든 파일에서 이 명령이 반복됩니다.

    이 명령의 출력은 다음과 유사합니다.

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    각 줄은 단어와 해당 단어가 입력 데이터에서 발생한 횟수를 나타냅니다.

## Sudoku

Sudoku 예제에는 "Include a puzzle on the command line" 등 다소 유용하지 않은 사용 지침이 있습니다.

[Sudoku](https://en.wikipedia.org/wiki/Sudoku)는 9개의 3x3 표로 구성된 논리 퍼즐입니다. 표의 일부 셀에는 숫자가 있고 다른 셀은 비어 있으며, 빈 셀을 해결하는 것이 목표입니다. 위의 링크에는 퍼즐에 대한 자세한 내용이 나와 있지만 이 샘플의 목적은 빈 셀을 해결하는 것입니다. 따라서 입력은 다음과 같은 형식의 파일이어야 합니다.

- 9개 열의 9개 행

- 각 열은 숫자 또는 `?`(빈 셀을 나타냄)를 포함할 수 있음

- 셀은 공백으로 구분됨

열이나 행에서 숫자를 반복할 수 없다는 Sudoku 퍼즐을 작성하는 특정 방법이 있습니다. 적절히 구성된 HDInsight 클러스터에 대한 예제가 있습니다. 이 예는 **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta**에 있으며, 다음을 포함합니다.

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] 경로의 `2.2.4.9-1` 부분은 HDInsight 클러스터에 업데이트가 적용됨에 따라 변경될 수 있습니다.

Sudoku 예제를 통해 이를 실행하려면 다음 명령을 사용합니다.

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

결과는 다음과 유사합니다.

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Pi(π)

Pi 샘플에서는 통계(준난수 몬테카를로) 방법을 사용하여 Pi 값을 추정합니다. 단위 정사각형 내에 무작위로 놓인 점은 원의 영역과 같은 확률인 Pi/4로 해당 정사각형 내의 내접원 안에 들어갑니다. Pi의 값은 4R의 값에서 추정할 수 있습니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에 있는 점 개수의 비율입니다. 사용한 점 샘플이 크면 클수록 추정이 향상됩니다.

이 샘플의 mapper는 단위 정사각형 내에 무작위로 놓인 점의 개수를 생성한 후 원 내부에 있는 해당 점 개수를 계산합니다.

그런 다음 reducer는 mapper에서 계산된 점을 누적하고 수식 4R에서 Pi의 값을 추정합니다. 여기에서 R은 정사각형 내에 있는 점의 총수에 대한 원 내부에서 계산된 점 개수의 비율입니다.

다음 명령을 사용하여 이 샘플을 실행합니다. 이 명령에서는 각각 10,000,000개의 샘플이 있는 16개의 맵을 사용하여 pi 값을 추정합니다.

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

이 명령에서 반환되는 값은 **3.14159155000000000000**과 유사합니다. 참고로, Pi의 소수점 이하 10자리는 3.1415926535입니다.

##10GB Greysort

GraySort는 메트릭이 대량의 데이터를 정렬하는 동안 도달하는 정렬 속도(TB/분)가 일반적으로 최소 100TB인 벤치마크 정렬입니다.

이 샘플에서는 비교적 빠르게 실행할 수 있도록 적절한 10GB의 데이터를 사용합니다. 또한 2009년에 0.578TB/분(173분에 100TB)의 속도로 연간 범용("daytona") 테라바이트 정렬 벤치마크를 획득한 Owen O'Malley 및 Arun Murthy가 개발한 MapReduce 응용 프로그램을 사용합니다. 이 정렬 벤치마크 및 다른 정렬 벤치마크에 대한 자세한 내용은 [정렬 벤치마크](http://sortbenchmark.org/)(영문) 사이트를 참조하십시오.

이 샘플에서는 세 가지 집합의 MapReduce 프로그램을 사용합니다.

- **TeraGen**: 정렬할 데이터의 행을 생성하는 MapReduce 프로그램

- **TeraSort**: 입력 데이터를 샘플링하고 MapReduce를 사용하여 데이터를 전체 순서로 정렬

    TeraSort는 각 reduce의 키 범위를 정의하는 N-1 샘플 키의 정렬된 목록을 사용하는 사용자 지정 파티셔너를 제외하면 MapReduce 함수의 표준 정렬입니다. 특히, sample[i-1] <= key < sample[i]와 같은 모든 키는 reduce i로 전송됩니다. 이는 reduce i의 출력이 모두 reduce i+1의 출력보다 작다는 것을 보증합니다.

- **TeraValidate**: 출력이 전역으로 정렬되는지 확인하는 MapReduce 프로그램

    이 프로그램은 출력 디렉터리에 파일당 하나의 맵을 만들며 각 맵에서 각 키가 이전 키보다 작거나 같은지 확인합니다. 또한 map 함수는 각 파일의 첫 번째와 마지막 키의 레코드를 생성하며, reduce 함수는 파일 i의 첫 번째 키가 파일 i-1의 마지막 키보다 큰지 확인합니다. 모든 문제가 순서에서 벗어난 키와 함께 reduce의 출력으로 보고됩니다.

데이터를 생성하고 정렬한 다음 출력의 유효성을 검사하려면 다음 단계를 사용합니다.

1. HDInsight 클러스터의 기본 저장소(**wasbs:///example/data/10GB-sort-input**)에 저장되는 10GB의 데이터를 생성합니다.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

	`-Dmapred.map.tasks`는 이 작업에 사용할 map 작업 수를 Hadoop에 알려 줍니다. 마지막 두 매개 변수는 10GB 분량의 데이터를 만들어**wasbs:///example/data/10GB-sort-input**에 저장하도록 지시합니다.

2. 다음 명령을 사용하여 데이터를 정렬합니다.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

	`-Dmapred.reduce.tasks`는 작업에 사용할 reduce 작업 수를 Hadoop에 알려 줍니다. 마지막 두 매개 변수는 데이터의 입력 및 출력 위치입니다.

3. 다음을 사용하여 정렬에 의해 생성된 데이터의 유효성을 검사합니다.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##다음 단계 ##

이 문서에서는 Linux 기반 HDInsight 클러스터에 포함된 샘플을 실행하는 방법을 알아보았습니다. HDInsight에서 Pig, Hive 및 MapReduce를 사용하는 방법에 대한 자습서는 다음 항목을 참조하세요.

* [HDInsight에서 Hadoop과 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 Hadoop과 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Hadoop과 MapReduce 사용][hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=AcomDC_0914_2016-->