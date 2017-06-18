<properties
	pageTitle="Linux 기반 HDInsight용 Java MapReduce 프로그램 개발 | Microsoft Azure"
	description="Linux 기반 HDInsight에서 Java MapReduce 프로그램을 개발하여 Linux 기반 HDInsight로 배포하는 방법에 대해 알아봅니다."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="larryfr"/>

# HDInsight Linux의 Hadoop용 Java MapReduce 프로그램 개발

이 문서에서는 Apache Maven을 사용하여 MapReduce 응용 프로그램을 만든 다음 HDInsight 클러스터의 Linux 기반 Hadoop에서 배포하고 실행하는 과정을 안내합니다.

##<a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- [JDK Java](http://www.oracle.com/technetwork/java/javase/downloads/) 7 이상(또는 OpenJDK와 같은 이와 동등한 프로그램)

- [Apache Maven](http://maven.apache.org/)

- **Azure 구독**

- **Azure CLI**

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##환경 변수 구성

Java 및 JDK를 설치할 때 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA\_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 OS X, Unix 또는 Linux 시스템에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`과 유사한 값이어야 합니다.

* **PATH** - 다음 경로를 포함해야 합니다.

	* **JAVA\_HOME** 또는 그와 동등한 경로

	* **JAVA\_HOME\\bin** 또는 그와 동등한 경로

	* Maven이 설치된 디렉터리

##새 Maven 프로젝트 만들기

1. 터미널 세션 또는 개발 환경의 명령줄에서 이 프로젝트를 저장할 위치로 디렉터리를 변경합니다.

3. Maven과 함께 설치되는 __mvn__ 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	이 명령은 __artifactID__ 매개 변수로 지정된 이름(이 예제에서는 **wordcountjava**)을 사용하여 현재 디렉터리에 새 디렉터리를 만듭니다. 이 디렉터리에는 다음 항목이 포함됩니다.

	* __pom.xml__ - [프로젝트 개체 모델(POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

	* __src__ - __main/java/org/apache/hadoop/examples__ 디렉터리를 포함하는 디렉터리이며, 여기서 응용 프로그램을 작성합니다.

3. __src/test/java/org/apache/hadoop/examples/apptest.java__ 파일은 이 예에서 사용되지 않으므로 삭제합니다.

##종속성 추가

1. __pom.xml__ 파일을 편집하고 `<dependencies>` 섹션 내에 다음을 추가합니다.

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	이 코드를 통해 Maven은 프로젝트에 특정 버전(&lt;version>에 나열됨)의 라이브러리(&lt;artifactId> 내에 나열됨)가 필요하다는 것을 인식합니다. 컴파일 시간에 이 파일이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 리포지토리 검색](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)을 사용하여 자세한 정보를 볼 수 있습니다.

	`<scope>provided</scope>`는 이러한 종속성은 런타임에 HDInsight 클러스터에서 제공되므로 응용 프로그램과 함께 패키징해서는 안 된다는 점을 Maven에 알려 줍니다.

2. __pom.xml__ 파일에 다음을 추가합니다. 이 코드는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어`</dependencies>`과 `</project>` 사이에 있어야 합니다.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
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
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	첫 번째 플러그 인은 응용 프로그램에 필요한 종속성을 포함하는 uberjar(fatjar이라고도 함)을 빌드하는 데 사용되는 [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/)을 구성합니다. 또한 일부 시스템에서 문제를 일으킬 수 있는 jar 패키지 내 라이선스 중복을 방지합니다.

	두 번째 플러그 인은 이 응용 프로그램에 필요한 Java 버전을 HDInsight 클러스터에서 사용되는 버전으로 설정하는 데 사용되는 Maven 컴파일러를 구성합니다.

3. __pom.xml__ 파일을 저장합니다.

##MapReduce 응용 프로그램 만들기

1. __wordcountjava/src/main/java/org/apache/hadoop/examples__ 디렉터리로 이동하여 __App.java__ 파일의 이름을 __WordCount.java__로 바꿉니다.

2. 텍스트 편집기에서 __WordCount.java__ 파일을 열고 내용을 다음으로 바꿉니다.

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

	패키지 이름은 **org.apache.hadoop.examples**이며 클래스 이름은 **WordCount**입니다. MapReduce 작업을 제출할 때 이 이름을 사용합니다.

3. 파일을 저장합니다.

##응용 프로그램 빌드

1. __wordcountjava__ 디렉터리로 변경합니다(아직 이동하지 않은 경우).

2. 다음 명령을 사용하여 응용 프로그램을 포함하는 JAR 파일을 빌드합니다.

		mvn clean package

	이 코드는 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 응용 프로그램을 빌드 및 패키지화합니다.

3. 명령이 완료되면 __wordcountjava/target__ 디렉터리에 __wordcountjava-1.0-SNAPSHOT.jar__라는 파일이 포함됩니다.

	> [AZURE.NOTE] __wordcountjava-1.0-SNAPSHOT.jar__ 파일은 는 WordCount 작업뿐만 아니라 런타임에 작업에서 필요로 하는 종속성을 포함하는 uberjar입니다.


##<a id="upload"></a>jar 업로드

다음 명령을 사용하여 HDInsight 헤드 노드에 jar 파일을 업로드합니다.

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

로컬 시스템에서 헤드 노드로 파일이 복사됩니다.

> [AZURE.NOTE] SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다. 예: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`

##<a name="run"></a>MapReduce 작업 실행

1. 다음 문서에 설명된 대로 SSH를 사용하여 HDInsight에 연결합니다.

    - [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH를 사용합니다.](hdinsight-hadoop-linux-use-ssh-windows.md)

2. SSH 세션에서 다음 명령을 사용하여 MapReduce 응용 프로그램을 실행합니다.

		yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

	이 명령은 WordCount MapReduce 응용 프로그램을 사용하여 davinci.txt 파일에서 단어 수를 계산하고 결과를 \_\_wasbs:///example/data/wordcountout__에 저장합니다. 입력 파일과 출력 모두 클러스터의 기본 저장소에 저장됩니다.

3. 작업이 완료되면 다음을 사용하여 결과를 확인합니다.

		hdfs dfs -cat wasbs:///example/data/wordcountout/*

	다음과 유사한 값을 가진 단어 및 개수 목록이 표시됩니다.

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>다음 단계

이 문서에서는 Java MapReduce 작업을 개발하는 방법에 대해 알아보았습니다. HDInsight로 작업하는 다른 방법은 다음 문서를 참조하세요.

- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

자세한 내용은 [Java 개발자 센터](https://azure.microsoft.com/develop/java/)를 참조하세요.

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

<!---HONumber=AcomDC_0914_2016-->