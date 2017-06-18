<properties
	pageTitle="HDInsight에서 Apache Hive로 Twitter 데이터 분석 | Microsoft Azure"
	description="Python을 사용하여 특정 키워드를 포함한 트윗을 저장하고, HDInsight에서 Hive 및 Hadoop을 사용하여 원시 Twitter 데이터를 검색 가능한 Hive 테이블로 변환하는 방법을 알아봅니다."
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

# HDInsight에서 Hive를 사용하여 Twitter 데이터 분석

이 문서에는 Twitter 스트리밍 API를 사용하여 트윗을 가져온 다음 Linux 기반 HDInsight 클러스터에서 Apache Hive를 사용하여 JSON 형식의 데이터를 처리합니다. 결과는 특정 단어가 포함된 많은 트윗을 보낸 Twitter 사용자의 목록이 됩니다.

> [AZURE.NOTE] Windows 기반 HDInsight 클러스터(예: Python)와 함께 이 문서의 개별 항목을 사용할 수 있지만 이 문서의 많은 단계는 Linux 기반 HDInsight 클러스터 사용을 기준으로 합니다. Windows 기반 클러스터에 대한 특정 단는 [HDInsight에서 Hive를 사용하여 Twitter 데이터 분석](hdinsight-analyze-twitter-data.md)을 참조하세요.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- __Linux 기반 Azure HDInsight 클러스터__. 클러스터를 만드는 방법에 대한 정보는 클러스터 만들기 단계의 [Linux 기반 HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

- __SSH 클라이언트__. Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ 및 [pip](https://pypi.python.org/pypi/pip)

##Twitter 피드 가져오기

Twitter를 사용하여 [각 트윗에 대한 데이터](https://dev.twitter.com/docs/platform-objects/tweets)를 REST API를 통해 JSON(JavaScript Notation) 개체로서 검색할 수 있습니다. [OAuth](http://oauth.net)는 API에 대한 인증을 필요로 합니다. API에 액세스하는데 사용되는 설정이 포함된 _Twitter 응용 프로그램_도 만들어야 합니다.

###Twitter 응용 프로그램 만들기

1. 웹 브라우저에서[https://apps.twitter.com/](https://apps.twitter.com/)으로 로그인합니다. (영문)에 로그인합니다. Twitter 계정이 없는 경우 **Sign up now** 링크를 클릭합니다.
2. **Create New App**을 클릭합니다.
3. **Name**, **Description**, **Website**를 입력합니다. **Website** 필드의 URL을 구성할 수 있습니다. 다음 표는 사용할 샘플 값을 보여 줍니다.

	| 필드 | 값 |
	|:----- |:----- |
	| 이름 | MyHDInsightApp |
	| 설명 | MyHDInsightApp |
	| 웹 사이트 | http://www.myhdinsightapp.com |
	
4. **Yes, I agree**를 선택한 후 **Create your Twitter application**을 클릭합니다.
5. **Permissions** 탭을 클릭합니다. 기본 권한은 **Read only**입니다. 이 자습서에는 이 권한이면 충분합니다.
6. **Keys and Access Tokens** 탭을 클릭합니다.
7. **Create my access token**을 클릭합니다.
8. 페이지의 오른쪽 위에서 **Test OAuth**를 클릭합니다.
9. **consumer key**, **Consumer secret**, **Access token** 및 **Access token secret**을 기록해 둡니다. 나중에 이 값이 필요합니다.

>[AZURE.NOTE] Windows에서 curl 명령을 사용할 때는 옵션 값에 작은따옴표 대신 큰따옴표를 사용합니다.

###트윗 다운로드

다음 Python 코드는 Twitter에서 10,000개의 트윗을 다운로드하고 __tweets.txt__라는 파일에 저장합니다.

> [AZURE.NOTE] 다음 단계는 Python이 이미 설치되어 있으므로 HDInsight 클러스터에서 수행됩니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	SSH 사용자 계정을 보호하도록 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
		
	Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
	
	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows)
	
2. 기본적으로 __pip__ 유틸리티는 HDInsight 헤드 노드에 설치되어있지 않습니다. 다음을 사용하여 설치한 다음 이 유틸리티를 업데이트합니다.

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. 트윗을 다운로드하는 코드는[Tweepy](http://www.tweepy.org/) 및 [Progressbar](https://pypi.python.org/pypi/progressbar/2.2)를 사용합니다. 설치하려면, 다음 명령을 사용합니다.

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE] Python-openssl 제거, python-dev, libffi-dev, libssl-dev, pyOpenSSL 및 requests[security] 제거에 관한 비트는 Python에서 SSL을 통해 Twitter에 연결할 때 InsecurePlatform 경고를 방지합니다.
	>
	> Tweepy v3.2.0은 트윗을 처리할 때 발생할 수 있는 [오류](https://github.com/tweepy/tweepy/issues/576)를 방지하기 위해 사용됩니다.

4. 다음 명령을 사용하여 __gettweets.py__라는 새 파일을 만듭니다.

		nano gettweets.py

5. 다음을 __gettweets.py__ 파일의 콘텐츠로 사용합니다. __consumer/_secret__, __consumer/_key_\_, __access/_token__ 및 __access/_token/_secret__의 자리 표시자 정보를 Twitter 응용 프로그램의 정보로 바꿉니다.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. __Ctrl + X__, __Y__를 차례로 사용하여 파일을 저장합니다.

7. 다음 명령을 사용하여 파일을 실행하고 트윗을 다운로드합니다.

		python gettweets.py

	진행률 표시기가 표시되어야 하며 트윗으로 최대 100%의 횟수가 다운로드되며 파일로 저장됩니다.

    > [AZURE.NOTE] 진행률 표시줄이 전진하는 데 매우 긴 시간이 걸리는 경우 추세 항목을 추적하는 필터를 변경해야 합니다. 필터링할 항목에 대한 트윗이 많은 경우 필요한 트윗 10,000개를 매우 신속하게 가져올 수 있습니다.

###데이터 업로드

WASB(HDInsight에서 사용하는 분산 파일 시스템)에 데이터를 업로드하려면 다음 명령을 사용합니다.

	hdfs dfs -mkdir -p /tutorials/twitter/data
	hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

클러스터의 모든 노드에서 액세스할 수 있는 위치에 데이터를 저장합니다.

##HiveQL 작업 실행


1. 다음 명령을 사용하여 HiveQL 문이 포함 된 파일을 만듭니다.

		nano twitter.hql
	
	파일 내용으로 다음을 사용합니다.

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response,	'$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
		
		
3. __Ctrl + X__, __Y__를 차례로 누르고 파일을 저장합니다.

4. 다음 명령을 사용하여 파일에 포함된 HiveQL을 실행합니다.

		beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql		
		
	Hive 셸을 로드하고 __twitter.hql__ 파일에서 HiveQL을 실행하고 마지막으로 `jdbc:hive2//localhost:10001/>` 프롬프트를 반환합니다.
	
5. Beeline 프롬프트에서 다음을 사용하여 __twitter.hql__ 파일의 HiveQL에서 만든 __트윗__ 테이블에서 데이터를 선택할 수 있음을 확인합니다.
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	메시지 텍스트에 단어 __Azure__를 포함하는 최대 10개의 트윗을 반환합니다.

##다음 단계

이 자습서에서는 비구조적 JSON 데이터 집합을 구조적 Hive 테이블로 변환하여 Azure의 HDInsight를 통해 Twitter 데이터를 쿼리하고 탐색하고 분석하는 방법을 살펴보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
- [HDInsight를 사용하여 비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=AcomDC_0914_2016-->