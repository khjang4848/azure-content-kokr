<properties
   pageTitle="HDInsight에서 Apache Storm을 사용하는 Twitter 추세 항목| Microsoft Azure"
   description="Trident를 사용하여 해시 태그에 기반하는 Twitter에서 추세 항목을 확인하는 Apache Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/12/2016"
   ms.author="larryfr"/>

#HDInsight에서 Apache Storm을 사용하여 Twitter 추세 항목 확인

Trident를 사용하여 Twitter에서 추세 항목(해시 태그)을 확인하는 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.

Trident는 조인, 집계, 그룹화, 함수 및 필터와 같은 도구를 제공하는 높은 수준의 추상화입니다. 또한 Trident는 상태 저장, 증분 처리를 수행하기 위한 기본 요소를 추가합니다. 이 예제에서는 사용자 지정 spout, 함수 및 trident에서 제공하는 여러 기본 제공 함수를 사용하여 토폴로지를 구축하는 방법을 보여 줍니다.

> [AZURE.NOTE] 이 예제는 주로 Juan Alonso의 [Trident Storm](https://github.com/jalonsoramos/trident-storm) 예제를 기반으로 합니다.

##요구 사항

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java 및 JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter 개발자 계정

##프로젝트를 다운로드합니다.

다음 코드를 사용하여 프로젝트를 로컬로 복제합니다.

	git clone https://github.com/Blackmist/TwitterTrending

##토폴로지

이 예제의 토폴로지는 다음과 같습니다.

![토폴로지](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] 이는 토폴로지의 단순화된 보기입니다. 구성 요소의 여러 인스턴스가 클러스터 내의 노드 간에 배포됩니다.

토폴로지를 구현하는 Trident 코드는 다음과 같습니다.

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

이 코드는 다음을 수행합니다.

1. Spout에서 새 스트림을 만듭니다. Spout는 Twitter에서 트윗을 검색하여 특정 키워드(이 예제의 경우 love, music 및 coffee)로 필터링합니다.

2. 사용자 지정 함수인 HashtagExtractor는 각 트윗에서 해시 태그를 추출하는 데 사용됩니다. 이는 스트림으로 보내집니다.

3. 스트림은 해시 태그로 그룹화되어 집계로 전달됩니다. 이 집계는 각 해시 태그가 발생한 횟수를 계산합니다. 이 데이터는 메모리에 유지됩니다. 마지막으로 해시 태그 및 개수가 포함된 새 스트림을 내보냅니다.

4. 이 예제에서는 주어진 트윗 배치에 가장 자주 사용된 해시 태그에만 관심이 있기 때문에 **FirstN** 어셈블리는 count 필드를 기반으로 하여 상위 10개의 값만 반환하도록 적용됩니다.

> [AZURE.NOTE] spout 및 HashtagExtractor 외에도 기본 제공된 Trident 기능을 사용하고 있습니다.
>
> 기본 제공 작업에 대한 자세한 내용은 <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">Package storm.trident.operation.builtin</a>을 참조하세요.
>
> MemoryMapState 외에 Trident 상태 구현에 대한 내용은 다음을 참조하세요.
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Storm Trident 탄력적인 검색</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>

###spout

**TwitterSpout** Spout는 <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a>를 사용하여 Twitter에서 트윗을 검색합니다. 필터(이 예제의 경우 love, music 및 coffee)가 만들어지며 해당 필터와 일치하는 들어오는 트윗(상태)이 Linked Blocking Queue에 저장됩니다. 자세한 내용은 <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue 클래스</a>를 참조하세요. 마지막으로 항목을 큐에서 가져와 토폴로지로 내보냅니다.

###HashtagExtractor

해시태그를 추출하기 위해 <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a>를 사용하여 트윗에 포함된 모든 해시 태그를 검색합니다. 그런 다음 스트림으로 보내집니다.

##Twitter 사용

새 Twitter 응용 프로그램을 등록하고를 Twitter에서 읽는 데 필요한 소비자 및 액세스 토큰 정보를 받으려면 다음 단계를 참조하세요.

1. <a href="https://apps.twitter.com" target="_blank">Twitter Apps</a>로 이동하여 **Create new app** 단추를 클릭합니다. 양식을 작성할 때 **Callback URL** 필드는 비워 둡니다.

2. 앱이 만들어지면 **Keys and Access Tokens** 탭을 클릭합니다.

3. **Consumer Key** 및 **Consumer Secret** 정보를 복사합니다.

4. 토큰이 없는 경우 페이지 아래쪽에서 **Create my access token**을 선택합니다. 토큰을 만들었으면 **Access Token** 및 **Access Token Secret** 정보를 복사합니다.

5. 앞에서 복사한 **TwitterSpoutTopology** 프로젝트에서 **resources/twitter4j.properties** 파일을 열고 이전 단계에서 수집한 정보를 추가한 다음 파일을 저장합니다.

##토폴로지 작성

다음 코드를 사용하여 프로젝트를 빌드합니다.

		cd [directoryname]
		mvn compile

##포톨로지 테스트

다음 명령을 사용하여 토폴로지를 로컬로 테스트합니다.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

토폴로지가 시작된 후 해당 토폴로지에서 내보낸 해시 태그 및 개수가 포함된 디버그 정보가 표시됩니다. 출력은 다음과 유사합니다.

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##다음 단계

토폴로지를 로컬로 테스트했으므로 이제 [HDInsight의 Apache Strom 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology.md)에서 토폴로지를 배포하는 방법에 대해 알아봅니다.

다음과 같은 Storm 항목을 참조할 수도 있습니다.

* [Maven을 사용하여 HDInsight의 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)

* [Visual Studio를 사용하여 HDInsight의 Storm용 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)

HDinsight에 대한 추가 Storm 예제:

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->