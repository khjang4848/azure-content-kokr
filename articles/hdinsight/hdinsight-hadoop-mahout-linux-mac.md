<properties
	pageTitle="Mahout 및 Linux 기반 HDInsight를 사용하여 추천 생성 | Microsoft Azure"
	description="Apache Mahout 기계 학습 라이브러리를 사용하여 Linux 기반 HDInsight(Hadoop)에서 영화 추천을 생성하는 방법을 알아봅니다."
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
	ms.date="08/30/2016"
	ms.author="larryfr"/>

#HDInsight의 Linux 기반 Hadoop와 함께 Apache Mahout를 사용하여 영화 추천 생성

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](http://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다.

Mahout은 Apache Hadoop용 [기계 학습][ml] 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 문서에서는 추천 엔진을 사용하여 친구가 본 영화를 기준으로 영화 추천을 생성합니다.

> [AZURE.NOTE] 이 문서의 단계에는 HDInsight 클러스터의 Linux 기반 Hadoop가 필요합니다. Windows 기반 클러스터와 함께 Mahout 사용에 대한 자세한 내용은 [HDInsight에서 Windows 기반 Hadoop로 Apache Mahout을 사용하여 영화 추천 생성](hdinsight-mahout.md)을 참조하세요.

##필수 조건

* HDInsight 클러스터의 Linux 기반 Hadoop HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 사용 시작][getstarted]을 참조하세요.

##Mahout 버전 관리

HDInsight 클러스터에 포함된 Mahout 버전에 대한 자세한 내용은 [HDInsight 버전 및 Hadoop 구성 요소](hdinsight-component-versioning.md)를 참조하세요.

> [AZURE.WARNING] HDInsight 클러스터에 다른 버전의 Mahout를 업로드할 수 있지만, HDInsight 클러스터와 함께 제공된 구성 요소만 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/)).

##<a name="recommendations"></a>이해 권장 사항

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 사용자 선호도) 형식의 데이터를 허용합니다. 그런 다음 Mahout에서 동시 발생 분석을 수행하여 _특정 항목에 대한 선호도를 가진 사용자가 다른 항목에 대한 선호도도 갖고 있는지_ 확인할 수 있습니다. Mahout은 좋아하는 항목 선호도를 가진 사용자를 확인하며, 이 선호도는 추천하는 데 사용할 수 있습니다.

다음은 영화를 사용하는 매우 단순한 예제입니다.

* __동시 발생__: Joe, Alice 및 Bob은 모두 _스타워즈_, _제국의 역습_ 및 _제다이의 귀환_을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* __동시 발생__: Bob 및 Alice는 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_도 좋아합니다. Mahout은 이전의 3개 영화를 좋아하는 사용자가 이 3개 영화도 좋아하는지 확인합니다.

* __유사성 추천__: Joe가 첫 3개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_를 추천합니다.

###데이터 이해

편의를 위해 [GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다. 이 데이터는 클러스터의 기본 저장소(`/HdiSamples/HdiSamples/MahoutMovieData`)에서 사용할 수 있습니다.

`moviedb.txt`(영화에 대한 정보) 및 `user-ratings.txt`의 두 가지 파일이 있습니다. user-ratings.txt 파일은 분석 중에 사용되고, moviedb.txt는 분석 결과를 표시할 때 사용자에게 친숙한 텍스트 정보를 제공하는 데 사용됩니다.

user-ratings.txt에 포함된 데이터의 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`이며, 각 사용자의 영화 등급 평가를 보여 줍니다. 다음은 데이터의 예제입니다.


    196	242	3	881250949
    186	302	3	891717742
    22	377	1	878887116
    244	51	2	880606923
    166	346	1	886397596

##분석 실행

다음 명령을 실행하여 추천 작업을 실행합니다.

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] 작업을 완료하려면 몇 분정도 걸릴 수 있으며 여러 MapReduce 작업을 실행할 수 있습니다.

##출력 보기

1. 작업이 완료되면 다음 명령을 사용하여 생성된 결과를 봅니다.

		hdfs dfs -text /example/data/mahoutout/part-r-00000

	출력은 다음과 같이 표시됩니다.

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

2. moviedb.txt와 함께 출력을 사용하여 사용자에게 보다 친숙한 정보를 표시할 수 있습니다. 먼저, 다음 명령을 사용하여 파일을 로컬로 복사해야 합니다.

		hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

	그러면 영화 데이터 파일과 함께 현재 디렉터리에 있는 **recommendations.txt**라는 파일에 출력 데이터가 복사됩니다.

3. 권장 사항 출력의 데이터에 대한 영화 이름을 조회하는 새 Python 스크립트를 만들려면 다음 명령을 사용합니다.

		nano show_recommendations.py

	편집기가 열리면 파일의 내용으로 다음을 사용합니다.

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

	**Ctrl X**, **Y**, **Enter**를 차례로 누르고 데이터를 저장합니다.

3. 파일이 실행하도록 다음 명령을 사용합니다.

		chmod +x show_recommendations.py

4. Python 스크립트를 실행합니다. 다음에서는 모든 파일이 다운로드된 디렉터리에 있다고 가정합니다.

		./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

	이 사용자 ID 4에 대해 생성된 권장 사항을 살펴봅니다.

	* **user-ratings.txt** 파일은 사용자가 등급을 평가하는 영화를 검색하는 데 사용됩니다.
	* **moviedb.txt** 파일은 동영상의 이름을 검색하는 데 사용됩니다.
	* **recommendations.txt**는 이 사용자에 대한 영화 추천을 검색하는데 사용됩니다

	이 명령의 출력은 다음과 유사합니다.

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##임시 데이터 삭제

Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 경로로 분리하기 위해 예제 작업에서 지정되었습니다. 임시 파일을 제거하려면 다음 명령을 사용합니다.

	hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] 명령을 다시 실행하려는 경우 출력 디렉터리도 삭제해야 합니다. 이 디렉터리를 삭제하려면 다음을 사용합니다.
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## 다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0914_2016-->