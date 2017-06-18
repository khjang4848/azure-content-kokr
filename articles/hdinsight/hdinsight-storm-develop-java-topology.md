<properties
   pageTitle="Apache Storm에 대한 Java 기반 토폴로지 개발| Microsoft Azure"
   description="간단한 단어 개수 토폴로지를 만들어 Java에서 Storm 토폴로지를 만드는 방법에 대해 배웁니다."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

#HDInsight에서 Apache Storm 및 Maven으로 기본 단어 개수 응용 프로그램에 대한 Java 기반 토폴로지를 개발합니다.

Maven을 사용하여 HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지를 만드는 방법을 알아봅니다. Maven 및 Java를 사용하여 기본 단어 개수 응용 프로그램을 만드는 과정을 안내하며 여기서 토폴로지는 Java로 정의됩니다. 그런 다음 Flux 프레임워크를 사용하여 토폴로지를 정의하는 방법을 알아봅니다.

> [AZURE.NOTE] Flux 프레임워크는 Storm 0.10.0 이상에서 사용할 수 있습니다. Storm 0.10.0은 HDInsight 3.3 및 3.4에서 사용할 수 있습니다.

이 문서의 단계를 완료하면 HDInsight에서 Apache Storm에 배포할 수 있는 기본 토폴로지가 생깁니다.

> [AZURE.NOTE] 이 문서에서 만든 토폴로지의 완료된 버전은 [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)에서 사용할 수 있습니다.

##필수 조건

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">JDK(Java Developer Kit) 버전 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* 메모장, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a> 등의 텍스트 편집기. 또는 <a href="https://eclipse.org/" target="_blank">Eclipse</a>(Luna 버전 이상)와 같은 IDE(통합 개발 환경)를 사용할 수 있습니다.

	> [AZURE.NOTE] 편집기 또는 IDE에 이 문서에서 다루지 않은 Maven과 함께 동작하는 특정 기능이 있을 수 있습니다. 편집 환경 기능에 대한 내용은 사용 중인 제품의 설명서를 참조하세요.

##환경 변수 구성

Java 및 JDK를 설치할 때 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA\_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`과 유사한 값이어야 합니다.

* **PATH** - 다음 경로를 포함해야 합니다.

	* **JAVA\_HOME** 또는 그와 동등한 경로

	* **JAVA\_HOME\\bin** 또는 그와 동등한 경로

	* Maven이 설치된 디렉터리

##새 Maven 프로젝트 만들기

명령줄에서 다음 코드를 사용하여 **WordCount**라는 새 Maven을 만듭니다.

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

그러면 기본 Maven 프로젝트를 포함하는 현재 위치에 **WordCount**라는 새 디렉터리가 만들어집니다.

**WordCount** 디렉터리는 다음 항목을 포함합니다.

* **pom.xml**: Maven 프로젝트에 대한 설정을 포함합니다.

* **src\\main\\java\\com\\microsoft\\example**:응용 프로그램 코드를 포함합니다.

* **src\\test\\java\\com\\microsoft\\example**:응용 프로그램에 대한 테스트를 포함합니다. 이 예제에서는 테스트를 만들지 않습니다.

###예제 코드를 제거합니다.

처음부터 응용 프로그램을 만들 것이므로 생성된 테스트 및 응용 프로그램 필드를 삭제합니다.

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.java**

*  **src\\main\\java\\com\\microsoft\\example\\App.java**

##속성 추가

Maven을 사용하면 속성이라고 하는 프로젝트 수준 값을 정의할 수 있습니다. `<url>http://maven.apache.org</url>` 줄 뒤에 다음을 추가합니다.

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/ko-KR/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

이제 다른 섹션에서 이러한 값을 사용할 수 있습니다. 예를 들어 Storm 구성 요소의 버전을 지정할 때 값을 하드 코딩하는 대신 `${storm.version}`을 사용할 수 있습니다.

##종속성 추가

이는 Storm 토폴로지이므로 Storm 구성 요소에 대한 종속성을 추가해야 합니다. **pom.xml** 파일을 열고 **&lt;dependencies>** 섹션에 다음 코드를 추가합니다.

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

컴파일 시간에 Maven 리포지토리에서 **storm-core**를 찾기 위해 Maven은 이 정보를 사용합니다. 먼저 로컬 컴퓨터의 리포지토리에서 찾습니다. 파일이 없으면 공개 Maven 리포지토리에서 이를 다운로드하여 로컬 리포지토리에 저장합니다.

> [AZURE.NOTE] 추가한 섹션에서 `<scope>provided</scope>` 줄을 확인합니다. 이는 Maven에 만든 JAR 파일에서 **storm-core**를 제외하도록 요청합니다. 시스템을 통해 제공되기 때문입니다. 이를 통해 만든 패키지를 좀 더 작게 할 수 있으며 이는 HDInsight에서 Storm에 포함된 **storm-core** 비트를 사용합니다.

##빌드 구성

Maven 플러그인을 사용하면 프로젝트를 컴파일하는 방법 또는 JAR 파일로 패키지하는 방법과 같은 프로젝트 빌드 단계를 사용자 지정할 수 있습니다. **pom.xml** 파일을 열고 `</project>` 줄 바로 위에 다음 코드를 추가합니다.

	<build>
	  <plugins>
	  </plugins>
      <resources>
      </resources>
	</build>

이 섹션은 플러그 인, 리소스 및 다른 빌드 구성 옵션을 추가하는 데 사용됩니다. __pom.xml__ 파일에 대한 전체 참조는 [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html)을 참조하세요.

###플러그 인 추가

Storm 토폴로지의 경우 <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Exec Maven 플러그 인</a>을 사용하면 개발 환경에서 로컬로 토폴로지를 쉽게 실행할 수 있으므로 유용합니다. Exec Maven 플러그 인을 추가하려면 **pom.xml** 파일의 `<plugins>` 섹션에 다음을 추가합니다.

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [AZURE.NOTE] `<mainClass>` 항목은 `${storm.topology}`를 사용합니다. 앞서 속성 섹션에서 정의하지 않았습니다(하지만 정의할 수도 있음). 대신 이후 단계의 개발 환경에서 토폴로지를 실행하는 경우 명령줄에서 이 값을 설정합니다.

다른 유용한 플러그 인은 컴파일 옵션을 변경하는 데 사용되는 <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven 컴파일러 플러그 인</a>입니다. 이 플러그 인이 필요한 주요한 이유는 사용자의 응용 프로그램에 대한 원본 및 대상에 Maven이 사용하는 Java 버전을 변경하기 위해서입니다. 버전 1.7이 필요합니다.

Apache MavenCompiler 플러그 인을 포함하고 원본 및 대상 버전을 1.7로 설정하려면 **pom.xml**의 `<plugins>` 섹션에 다음을 추가합니다.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###리소스 구성

리소스 섹션을 사용하면 토폴로지에 구성 요소에 필요한 구성 파일과 같은 비코드 리소스를 포함할 수 있습니다. 이 예제에서는 **pom.xml** 파일의 `<resources>` 섹션 내에 다음을 추가합니다.

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

그러면 프로젝트의 루트에 있는 리소스 디렉터리(`${basedir}`)가 리소스를 포함하는 위치로 추가되고 __log4j2.xml__이라는 파일이 포함됩니다. 이 파일은 토폴로지에서 기록하는 정보를 구성하는 데 사용됩니다.

##토폴로지 만들기

Java 기반 Storm 토폴로지는 사용자가 작성자이거나 종속성으로 참조되는 세 개의 구성 요소로 이루어져 있습니다.

* **Spout**: 외부 소스에서 데이터를 읽고 데이터의 스트림을 토폴로지로 내보냅니다.

* **Bolt**: Spout 또는 다른 Bolt가 내보낸 스트림에서 처리를 수행하고 하나 이상의 스트림을 내보냅니다.

* **토폴로지**: Spout 및 Bolt 배열 방식을 정의하고 토폴로지에 대한 진입점을 제공합니다.

###Spout 만들기

외부 데이터 소스 설정에 대한 요구를 줄이기 위해 다음 spout가 임의의 문장을 내보냅니다. 이는 [Storm-Starter 예제](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)와 함께 제공된 Spout의 수정된 버전입니다.

> [AZURE.NOTE] 외부 데이터 소스에서 읽는 Spout의 예는 다음 예제 중 하나를 참조하세요.
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Twitter에서 읽는 예제 Spout
>
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Kafka에서 읽는 Spout

Spout의 경우, **src\\main\\java\\com\\microsoft\\example** 디렉터리에 **RandomSentenceSpout.java**라는 새 파일을 만들고 다음을 파일 내용으로 사용합니다.

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

코드 주석을 읽어보면 이 spout가 어떻게 동작하는지 이해할 수 있습니다.

> [AZURE.NOTE] 이 토폴로지는 하나의 spout만 사용하지만 다른 토폴로지는 다른 소스에서 해당 토폴로지로 데이터를 피드하는 여러 spout를 사용할 수 있습니다.

###Bolt 만들기

Bolt는 데이터 처리를 다룹니다. 이 토폴로지의 경우 다음 두 가지 Bolt가 있습니다.

* **SplitSentence**: **RandomSentenceSpout**를 통해 내보낸 문장을 개별 단어로 분리합니다.

* **WordCount**: 각각의 단어가 발생한 횟수를 계산합니다.

> [AZURE.NOTE] Bolt는 계산, 지속성, 외부 구성 요소에 말하기 등 문자 그대로 아무 작업이나 수행할 수 있습니다.

**src\\main\\java\\com\\microsoft\\example** 디렉터리에 **SplitSentence.java** 및 **WordCount.Java**라는 두 개의 새 파일을 만듭니다. 파일 내용으로 다음을 사용합니다.

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

코드 주석을 읽어보면 각각의 Bolt가 어떻게 동작하는지 이해할 수 있습니다.

###토폴로지 정의

토폴로지는 spout 및 bolt를 그래프로 묶습니다. 이 그래프는 구성 요소 사이의 데이터 흐름 방식을 정의합니다. Storm이 클러스터 내에서 구성 요소의 인스턴스를 만들 대 사용하는 병렬 처리 힌트도 제공합니다.

다음은 이 토폴로지에 대한 구성 요소 그래프의 기본 다이어그램입니다.

![Spout 및 Bolt 배열을 보여 주는 다이어그램](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

토폴로지를 구현하려면 **WordCountTopology.java**라는 새 파일을 **src\\main\\java\\com\\microsoft\\example** 디렉터리에 만듭니다. 파일 내용으로 다음을 사용합니다.

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

코드 주석을 읽어보면 토폴로지가 정의되고 클러스터로 제출되는 방식을 이해할 수 있습니다.

###로깅 구성

Storm은 Apache Log4j를 사용하여 정보를 기록합니다. 로깅을 구성하지 않으면 토폴로지는 읽기 어려울 수 있는 많은 진단 정보를 내보냅니다. 로깅되는 내용을 제어하려면 __resources__ 디렉터리에 __log4j2.xml__이라는 파일을 만듭니다. 다음을 파일 콘텐츠로 사용합니다.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

이렇게 하면 이 예제 토폴로지의 구성 요소를 포함하는 __com.microsoft.example__ 클래스에 대한 새 로거가 구성됩니다. 수준은 이 로거에 대한 추적으로 설정되며, 이 토폴로지의 구성 요소에서 내보낸 모든 로깅 정보가 캡처됩니다. 이 프로젝트에 대한 코드를 다시 살펴보면 WordCount.java 파일만 로깅을 구현하며 각 단어의 개수를 기록합니다.

`<Root level="error">` 섹션은 루트 수준의 로깅(모든 항목이 __com.microsoft.example__에 있지는 않음)을 오류 정보만 기록하도록 구성합니다.

> [AZURE.IMPORTANT] 이렇게 하면 개발 환경에서 토폴로지를 테스트할 때 기록되는 정보가 크게 줄어들지만, 프로덕션의 클러스터에서 실행할 경우 생성되는 모든 디버그 정보가 제거되지는 않습니다. 해당 정보를 줄이려면 클러스터에 제출된 구성에서도 디버깅을 false로 설정해야 합니다. 예제는 이 문서의 WordCountTopology.java 코드를 참조하세요.

Log4j에 대한 로깅 구성과 관련된 자세한 내용은 [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html)을 참조하세요.

> [AZURE.NOTE] Storm 버전 0.10.0은 Log4j 2.x를 사용합니다. 이전 버전의 Storm은 로그 구성에 다른 형식을 사용하는 Log4j 1.x를 사용합니다. 이전 구성에 대한 자세한 내용은 [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat)을 참조하세요.

##로컬에서 토폴로지 테스트

파일을 저장한 후 다음 명령을 사용하여 토폴로지를 로컬로 테스트할 수 있습니다.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

실행할 때 토폴로지가 시작 정보를 표시한 다음, 문장을 Spout에서 내보낸 다음 Bolt로 처리했기 때문에 아래와 유사한 줄을 표시하기 시작합니다.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

WordCount Bolt가 내보낸 로깅을 보면 'and'를 113번 내보낸 것을 알 수 있습니다. Spout가 계속해서 동일한 문장을 내보내기 때문에 토폴로지를 실행하면 개수는 계속 증가합니다.

또한 단어 내보내기와 개수 사이에는 5초의 간격이 있습니다. 이는 틱 튜플이 도달할 때 정보를 내보내도록 __WordCount__ 구성 요소를 구성했기 때문에 발생하며 이러한 튜플이 기본적으로 5초마다 배달되도록 요청합니다.

## 토폴로지를 Flux로 변환

Flux는 구현에서 구성을 분리할 수 있는 Storm 0.10.0에서 사용할 수 있는 새로운 프레임워크입니다. 구성 요소(Bolt 및 Spout)는 Java로 정의되지만 토폴로지는 YAML 파일을 사용하여 정의됩니다.

YAML 파일은 토폴로지에 사용할 구성 요소, 구성 요소 간의 데이터 흐름 방식 및 구성 요소를 초기화할 때 사용할 값을 정의합니다. 배포할 때 프로젝트를 포함하는 jar 파일의 일부로 YAML 파일을 포함하거나 토폴로지를 시작할 때 외부 YAML 파일을 사용할 수 있습니다.

1. 프로젝트에서 __WordCountTopology.java__ 파일을 이동합니다. 이전에 이 토폴로지를 정의했지만 Flux에 사용하지 않습니다.

2. __리소스__ 디렉터리에서 __topology.yaml__라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

        # topology definition

        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"

        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1

        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1

        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1

        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1

        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE

        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]

    __WordCountTopology.java__ 파일에서 각 섹션의 용도 및 Java 기반 정의에 관련된 방법을 읽고 이해합니다.

3. __pom.xml__ 파일을 다음과 같이 변경합니다.

    * `<dependencies>` 섹션에서 다음 새 종속성을 추가합니다.

            <!-- Add a dependency on the Flux framework -->
            <dependency>
                <groupId>org.apache.storm</groupId>
                <artifactId>flux-core</artifactId>
                <version>${storm.version}</version>
            </dependency>

    * `<plugins>` 섹션에 다음 플러그 인을 추가합니다. 이 플러그 인은 프로젝트에 대한 패키지(jar 파일)를 만들도록 처리하고 패키지를 만들 때 Flux에 특정된 일부 변환을 적용합니다.

            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                        <!-- Keep us from getting a "can't overwrite file error" -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                        <!-- We're using Flux, so refer to it as main -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>org.apache.storm.flux.Flux</mainClass>
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

    * __exec-maven-plugin__ `<configuration>` 섹션에서 `<mainClass>` 값을 `org.apache.storm.flux.Flux`로 변경합니다. 이렇게 하면 개발 시 로컬로 실행하는 경우 Flux가 토폴로지를 실행하여 처리할 수 있습니다.

    * `<resources>` 섹션의 `<includes>`에 다음을 추가합니다. 여기에는 토폴로지를 프로젝트의 일환으로 정의하는 YAML 파일이 포함됩니다.
    
            <include>topology.yaml</include>

## 로컬에서 Flux 토폴로지 테스트

1. Maven을 사용하여 Flux 토폴로지를 컴파일하고 실행하려면 다음을 사용합니다.

        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    
    PowerShell을 사용하는 경우 다음을 사용합니다.
    
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    Linux/Unix/OS X 시스템에서 [개발 환경에 Storm을 설치](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html)한 경우 다음 명령을 대신 사용할 수 있습니다.

        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml

    `--local` 매개 변수는 개발 환경에서 토폴로지를 로컬 모드로 실행합니다. `-R /topology.yaml` 매개 변수는 jar 파일에서 `topology.yaml` 파일 리소스를 사용하여 토폴로지를 정의합니다.

    실행할 때 토폴로지가 시작 정보를 표시한 다음, 문장을 Spout에서 내보낸 다음 Bolt로 처리했기 때문에 아래와 유사한 줄을 표시하기 시작합니다.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    
    WordCount 구성 요소를 만들 때 `topology.yaml` 파일이 `10` 값을 전달하면 기록된 정보의 배치 간에 10초 지연이 발생합니다. 틱 튜플에 대한 지연 간격을 10초로 설정합니다.

2.  프로젝트에서 `topology.yaml` 파일의 복사본을 만듭니다. `newtopology.yaml`라고 합니다. 파일에서 다음 섹션을 찾고 `10` 값을 `5`로 변경합니다. 단어 수의 배치를 내보내는 간격을 10초에서 5로 변경합니다.

          - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 5
            parallelism: 1

3. 토폴로지를 실행하려면 다음 명령을 사용합니다.

        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"

    또는 Linux/Unix/OS X 개발 환경에 Storm이 있는 경우:

        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml

    `/path/to/newtopology.yaml`을 이전 단계에서 만든 newtopology.yaml 파일의 경로로 변경합니다. 이 명령은 newtopology.yaml을 토폴로지 정의로 사용합니다. `compile` 매개 변수를 포함하지 않았기 때문에 Maven은 이전 단계에서 빌드한 프로젝트의 버전을 다시 사용합니다.

    토폴로지가 시작되면 내보낸 배치 간의 간격이 newtopology.yaml에 있는 값을 반영하도록 변경되었다는 점에 유의해야 합니다. 따라서 토폴로지를 다시 컴파일하지 않고도 YAML 파일을 통해 구성을 변경할 수 있습니다.

런타임 시 전달된 매개 변수에 따라 YAML 파일 또는 환경 변수에서 변수를 대체하는 것과 같은 Flux에서 제공하는 다른 기능을 여기에서 다루지 않았습니다. Flux 프레임워크의 다른 기능에 대한 자세한 내용은 [Flux(https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html)를 참조하세요.

##Trident

Trident는 Storm에서 제공하는 높은 수준의 추상화이며 상태 저장 처리를 지원합니다. Trident의 주요 이점은 토폴로지가 입력하는 모든 메시지가 한 번만 처리된다는 점입니다. 해당 메시지를 한 번 이상 처리하는 원시 Java 토폴로지에서는 이루기 어렵습니다. Bolt를 만드는 대신 사용할 수 있는 기본 제공 구성 요소와 같은 다른 차이점도 있습니다. 사실 Bolt는 필터, 프로젝션 및 함수와 같이 덜 일반적인 구성 요소로 완전히 대체됩니다.

Trident 응용 프로그램은 Maven 프로젝트를 사용하여 만들 수 있습니다. 이 문서의 앞부분에 제공된 것과 동일한 기본 단계를 거치며 코드만 다릅니다. Trident도 현재 Flux 프레임워크에서 사용할 수 없습니다.

Trident에 대한 자세한 내용은 <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Trident API 개요</a>를 참조하세요.

Trident 응용 프로그램 예제는 [HDInsight에서 Apache Storm을 사용하는 Twitter 추세 항목](hdinsight-storm-twitter-trending.md)을 참조하세요.

##다음 단계

Java를 사용하여 Storm 토폴로지를 만드는 방법을 배웠으므로 이제 다음으로 이동합니다.

* [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology.md)

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Storm 토폴로지에 대한 추가 예제는 [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->