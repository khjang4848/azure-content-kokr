<properties
 pageTitle="Maven을 사용하여 Scalding MapReduce 작업 개발 | Microsoft Azure"
 description="Maven을 사용하여 Scalding MapReduce 작업을 만든 다음 HDInsight 클러스터의 Hadoop에서 작업을 배포하고 실행하는 방법에 대해 알아봅니다."
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
 ms.date="08/02/2016"
 ms.author="larryfr"/>

# HDInsight에서 Apache Hadoop을 사용하여 Scalding MapReduce 작업 개발

Scalding은 Hadoop MapReduce 작업을 쉽게 만들 수 있도록 해주는 Scala 라이브러리입니다. 간결한 구문을 제공하며 Scala와 긴밀하게 통합됩니다.

이 문서에서는 Maven을 사용하여 Scalding으로 작성된 기본 단어 계산 MapReduce 작업을 만드는 방법에 대해 알아봅니다. 그런 다음 이 작업을 HDInsight 클러스터에서 배포하고 실행하는 방법에 대해 알아봅니다.

## 필수 조건

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **HDInsight 클러스터의 Windows 또는 Linux 기반 Hadoop** 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 프로비전](hdinsight-hadoop-provision-linux-clusters.md) 또는 [HDInsight에서 Windows 기반 Hadoop 프로비전](hdinsight-provision-clusters.md)을 참조하세요.

* **[Maven](http://maven.apache.org/)**

* **[Java 플랫폼 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 이상**

## 프로젝트 만들기 및 빌드

1. 다음 명령을 사용하여 새 Maven 프로젝트를 만듭니다.

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    이 명령은 **scaldingwordcount**라는 새 디렉터리를 만들고 Scala 응용 프로그램용 스캐폴딩을 만듭니다.

2. **scaldingwordcount** 디렉터리에서 **pom.xml** 파일을 열고 내용을 다음으로 바꿉니다.

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
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
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    이 파일은 프로젝트, 종속성 및 플러그 인을 설명합니다. 중요한 항목은 다음과 같습니다.

    * **maven.compiler.source** 및 **maven.compiler.target**:이 프로젝트에 대한 Java 버전을 설정합니다.

    * **repositories**: 이 프로젝트에서 사용되는 종속성 파일이 포함된 리포지토리입니다.

    * **scalding-core\_2.11** 및 **hadoop-core**: 이 프로젝트는 Scalding 및 Hadoop 핵심 패키지 둘 다에 종속됩니다.

    * **maven-scala-plugin**: Scala 응용 프로그램을 컴파일하는 플러그 인입니다.

    * **maven-shade-plugin**: shaded(fat) jar을 만드는 플러그 인입니다. 이 플러그 인은 필터 및 변환을 적용합니다.

        * **필터**: jar 파일에 포함된 메타 정보를 수정하는 필터를 적용합니다. 런타임에서 서명 예외를 방지하도록 종속성과 함께 포함될 수 있는 다양한 서명 파일을 제외합니다.

        * **실행**: 패키지 단계 실행 구성은 **com.twitter.scalding.Tool** 클래스를 패키지에 대한 기본 클래스로 지정합니다. 이렇게 하지 않으면 hadoop 명령으로 작업을 실행할 때 com.twitter.scalding.Tool 및 응용 프로그램을 논리를 포함하는 클래슬르 지정해야 합니다.

3. 이 예제에서는 테스트를 만들지 않을 것이므로 **src/test** 디렉터리를 삭제합니다.

4. **src/main/scala/com/microsoft/example/app.scala** 파일을 열고 내용을 다음으로 바꿉니다.

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    그러면 기본 단어 계산 작업이 구현됩니다.

5. 파일을 저장하고 닫습니다.

6. **scaldingwordcount** 디렉터리에서 다음 명령을 사용하여 응용 프로그램을 빌드 및 패키지합니다.

        mvn package

    이 작업이 완료되면 WordCount 응용 프로그램이 포함된 패키지를 **target/scaldingwordcount-1.0-SNAPSHOT.jar**에서 확인할 수 있습니다.

## Linux 기반 클러스터에서 작업 실행

> [AZURE.NOTE] 다음 단계에서는 SSH 및 Hadoop 명령을 사용합니다. MapReduce 작업을 실행하는 다른 방법은 [HDInsight의 Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

1. 다음 명령을 사용하여 HDInsight 클러스터에 패키지를 업로드합니다.

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    로컬 시스템에서 헤드 노드로 파일이 복사됩니다.

    > [AZURE.NOTE] SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다. 예: `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. 다음 명령을 사용하여 클러스터 헤드 노드에 연결합니다.

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] SSH 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. SSH 키를 사용한 경우 `-i` 매개 변수 및 개인 키에 대한 경로를 사용해야 합니다. 예: `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. 헤드 노드에 연결되면 다음 명령을 사용하여 단어 계산 작업을 실행합니다.

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    이 명령은 앞에서 구현한 WordCount 클래스를 실행합니다. `--hdfs`는 작업에 HDFS를 사용하도록 지시하고, `--input`은 입력 텍스트 파일을 지정하며, `--output`은 출력 위치를 지정합니다.

4. 작업이 완료되면 다음을 사용하여 출력을 확인합니다.

        hdfs dfs -text wasbs:///example/wordcountout/*

    다음과 유사한 정보가 표시됩니다.

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Windows 기반 클러스터에서 작업 실행

다음 단계에서는 Windows PowerShell을 사용합니다. MapReduce 작업을 실행하는 다른 방법은 [HDInsight의 Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Azure PowerShell을 시작하고 Azure 계정에 로그인합니다. 자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.

		Add-AzureRMAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

		Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] `Get-AzureRMSubscription`을 사용하여 계정과 관련한 모든 구독 목록을 가져올 수 있습니다. 여기에는 각 구독에 대한 구독 ID가 포함되어 있습니다.

4. 다음 스크립트를 사용하여WordCount 작업을 업로드 및 실행합니다. `CLUSTERNAME`을 HDInsight 클러스터 이름으로 대체하고, `$fileToUpload`가 __scaldingwordcount-1.0-SNAPSHOT.jar__ 파일의 정확한 경로를 가리키고 있는지 확인합니다.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = "CLUSTERNAME"
        $fileToUpload = "scaldingwordcount-1.0-SNAPSHOT.jar"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"
        
        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                       "--input", `
                       "wasbs:///example/data/gutenberg/davinci.txt", `
                       "--output", `
                       "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     이 스크립트를 실행하면 HDInsight 클러스터에 대한 관리자 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다. 작업 실행 중에 발생한 오류는 콘솔에 기록됩니다.
     
6. 작업이 완료되면 출력이 현재 디렉터리의 __output.txt__ 파일에 다운로드됩니다. 다음 명령을 실행하여 결과를 표시합니다.

        cat output.txt

    파일에는 다음과 유사한 값이 포함되어 있습니다.

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## 다음 단계

Scalding을 사용하여 HDInsight용 MapRedcue 작업을 만드는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에서 하이브 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->