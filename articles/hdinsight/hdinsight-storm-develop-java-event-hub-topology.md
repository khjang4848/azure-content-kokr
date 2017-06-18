<properties
   pageTitle="Java를 사용하여 HDInsight의 Storm으로 이벤트 허브에서 이벤트 처리 | Azure"
   description="Maven으로 만든 Java Storm 토폴로지를 사용하여 이벤트 허브 데이터를 처리하는 방법을 알아봅니다."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="larryfr"/>

# HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)

Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. 이벤트 허브 Spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 분석하기가 쉽습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다.

이 자습서에서는 이벤트 허브 Spout 및 Bolt를 사용하여 Java 기반 Storm 토폴로지에 데이터를 읽고 쓰는 방법을 알아봅니다.

## 필수 조건

* HDInsight의 Apache Storm 클러스터입니다. 다음 시작 문서 중 하나를 사용하여 클러스터를 만듭니다.

    - [HDInsight 클러스터의 Linux 기반 Storm](hdinsight-apache-storm-tutorial-get-started-linux.md): SSH를 사용하여 Linux, Unix, OS X 또는 Windows 클라이언트에서 클러스터를 사용하려면 이 옵션을 선택합니다.

    - [HDInsight 클러스터의 Windows 기반 Storm](hdinsight-apache-storm-tutorial-get-started.md): PowerShell을 사용하여 Windows 클라이언트에서 클러스터를 사용하려면 이 옵션을 선택합니다.

    > [AZURE.NOTE] 이 문서의 단계는 HDInsight 클러스터 3.3 이상에서 Storm을 사용하는 경우를 기준으로 합니다. 이러한 클러스터는 Storm 0.10.0 및 Hadoop 2.7을 제공하므로 이 예제를 작동하는 데 필요한 단계 수를 줄일 수 있습니다.
    >
    > HDInsight 3.2에서 Storm 0.9.3을 사용하는 이 예제 버전의 경우 예제 리포지토리의 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 분기를 참조하세요.

* [Azure 이벤트 허브](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [OpenJDK](http://openjdk.java.net/)와 같은 [Oracle Java 개발자 키트(JDK) 버전 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 또는 그와 동등

* [Maven](https://maven.apache.org/download.cgi): Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* 텍스트 편집기 또는 Java 통합 개발 환경(IDE)

	> [AZURE.NOTE] 편집기 또는 IDE에 이 문서에서 다루지 않은 Maven과 함께 동작하는 특정 기능이 있을 수 있습니다. 편집 환경 기능에 대한 내용은 사용 중인 제품의 설명서를 참조하세요.

 * SSH 클라이언트. HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

    - [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

* SCP 클라이언트입니다. 모든 Linux, Unix 및 OS X 체제로 제공됩니다. Windows 클라이언트의 경우 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 사용할 수 있는 PSCP를 사용하는 것이 좋습니다.

##예제 이해

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 예제는 두 토폴로지를 포함합니다.

__com.microsoft.example.EventHubWriter__는 임의 데이터를 Azure 이벤트 허브에 기록합니다. 데이터는 Spout에서 생성되는 임의의 장치 ID 및 장치 값입니다. 따라서 문자열 ID 및 숫자 값을 내보내는 하드웨어 일부를 시뮬레이션 중입니다.

__com.microsoft.example.EventHubReader__는 이벤트 허브(EventHubWriter에서 작성한 데이터)에서 데이터를 읽고 /devicedata 디렉터리의 HDFS에 저장됩니다.(Azure HDInsight로 작성되고 테스트되었기 때문에 이 경우 WASB)

데이터는 이벤트 허브에 기록되기 전에 JSON 문서로 형식이 지정되고 판독기에서 읽을 때 JSON에서 튜플로 구문이 분석됩니다. JSON 형식은 다음과 같습니다.

    { "deviceId": "unique identifier", "deviceValue": some value }

JSON 문서를 사용하여 이벤트 허브에 데이터를 저장하는 이유는 이벤트 허브 Spout 및 Bolt의 내부 형식 지정 메커니즘에 의존하지 않고 어떤 형식인지 알기 위해서입니다.

###프로젝트 구성

**POM.xml** 파일은 이 Maven 프로젝트에 대한 구성 정보를 포함합니다. 흥미로운 부분은 다음과 같습니다.

####EventHubs Storm Spout 종속성

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

storm-eventhubs 패키지에 대한 종속성을 추가하며 이는 이벤트 허브에서 읽기의 경우 Spout 및 쓰기의 경우 Bolt를 모두 포함합니다.

> [AZURE.NOTE] 이 패키지는 Storm 버전 0.10.0 이상에서만 사용할 수 있습니다. Storm 0.9.3을 사용하는 경우 Microsoft에서 제공하는 Spout 패키지를 수동으로 설치해야 합니다. Storm 0.9.3 사용 예제에 대해서는 예제 리포지토리의 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 분기를 참조하세요.

####HdfsBolt 및 WASB 구성 요소

HdfsBolt는 일반적으로 Hadoop 분산 파일 시스템 HDFS에 데이터를 저장하는 데 사용됩니다. 그러나 HDInsight 클러스터는 Azure 저장소 (WASB)의 기본 데이터 저장소로 사용하므로 HdfsBolt가 WASB 파일 시스템을 이해할 수 있는 여러 구성 요소를 로드해야 합니다.

      <!--HdfsBolt stuff -->
        <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            </exclusion>
            <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            </exclusion>
        </exclusions>
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [AZURE.NOTE] 이전 버전의 Hdinsight(예: 버전 3.2)를 사용하는 경우 이러한 구성 요소를 수동으로 등록해야 합니다. 이에 대한 예제와 이전 HDInsight 클러스터에 필요한 사용자 지정 비트에 대한 자세한 내용은 예제 리포지토리의 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 분기를 참조하세요.

####maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

프로젝트가 Java 7에 대한 호환성으로 컴파일되어야 하며 이것이 HDInsight 클러스터에서 사용되었음을 Maven에 알려줍니다.

####maven-shade-plugin

      <!-- build an uber jar -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <transformers>
            <!-- Keep us from getting a can't overwrite file error -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
            <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
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

프로젝트 코드 및 필요한 종속성을 포함하는 uber jar에 솔루션을 패키지하는 데 사용됩니다. 다음에 사용됩니다.

* 종속성에 대한 라이선스 파일 이름 바꾸기: 이 동작을 수행하지 않으면 Windows 기반 HDInsight 클러스터에서 런타임에 오류가 발생할 수 있습니다.

* 보안/서명 제외: 이 동작을 수행하지 않으면 HDInsight 클러스터에서 런타임에 오류가 발생할 수 있습니다.

* 동일한 인터페이스의 여러 구현이 하나의 항목으로 병합됩니다. 이 작업이 수행되지 않으면 Storm-HDFS bolt가 WASB 파일 시스템과 통신하는 방법을 파악할 수 없다는 오류가 표시됩니다.

####exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
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

다음 명령을 사용하여 사용자의 개발 환경에서 토폴로지를 로컬로 실행하도록 합니다.

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

예: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####리소스 섹션

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

프로젝트에서 필요한 리소스를 정의합니다.

- **EventHubs.properties**: Azure 이벤트 허브에 연결하는 데 사용되는 정보를 포함합니다.
- **core-site.xml**: HDInsight 클러스터에서 사용되는 Azure 저장소에 대한 정보를 포함합니다.

이벤트 허브 및 HDInsight 클러스터에 대한 정보로 이러한 두 가지를 채워야 합니다.

##환경 변수 구성

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA\_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`과 유사한 값이어야 합니다.

* **PATH** - 다음 경로를 포함해야 합니다.

	* **JAVA\_HOME** 또는 그와 동등한 경로

	* **JAVA\_HOME\\bin** 또는 그와 동등한 경로

	* Maven이 설치된 디렉터리

## 이벤트 허브 구성

이벤트 허브는 이 예제의 데이터 원본입니다. 다음 단계에 따라 새 이벤트 허브를 만듭니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **새로 만들기** > **서비스 버스** > **이벤트 허브** > **사용자 지정 만들기**를 선택합니다.

2. **새 이벤트 허브 추가** 화면에서 **이벤트 허브 이름**을 입력하고 허브를 만들 **하위 지역**을 선택한 다음 새 네임스페이스를 만들거나 기존 네임스페이스를 선택합니다. **화살표**를 클릭하여 계속합니다.

	![마법사 페이지 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 대기 시간 및 비용을 줄이려면 HDInsight 서버의 Storm과 동일한 **위치**를 선택해야 합니다.

2. **이벤트 허브 구성** 화면에서 **파티션 개수** 및 **메시지 보존** 값을 입력합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다. 파티션 개수 값은 나중에 필요하므로 기록해 둡니다.

	![마법사 페이지 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. 이벤트 허브를 만든 후 네임스페이스를 선택하고 **이벤트 허브**를 선택한 다음 앞에서 만든 이벤트 허브를 선택합니다.

4. **구성**을 선택하고 다음 정보를 사용하여 새 액세스 정책 두 개를 만듭니다.

	<table>
	<tr><th>이름</th><th>권한</th></tr>
	<tr><td>기록기</td><td>보내기</td></tr>
	<tr><td>읽기 권한자</td><td>수신 대기</td></tr>
	</table>

	권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 그러면 이 이벤트 허브로 보내고(기록기) 수신하는(판독기) 데 사용되는 공유 액세스 정책이 만들어집니다.

	![정책](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. 정책을 저장한 후 페이지 아래쪽의 **공유 액세스 키 생성기**를 사용하여 **기록기** 및 **판독기** 정책에 대한 키를 검색합니다. 이러한 키는 나중에 사용되므로 저장합니다.

## 프로젝트 다운로드 및 빌드

1. GitHub에서 프로젝트 다운로드: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)입니다. zip 아카이브로 패키지를 다운로드하거나 [git](https://git-scm.com/)를 사용하여 프로젝트를 로컬로 복제할 수 있습니다.

2. 다음을 사용하여 프로젝트를 빌드하고 패키징합니다.

        mvn package

    필요한 종속성을 다운로드하고 프로젝트를 빌드 및 패키징합니다. 출력은 __/target__ 디렉터리에 __EventHubExample-1.0-SNAPSHOT.jar__로 저장됩니다.

## 토폴로지 배포

이 프로젝트에서 만든 jar는 __com.microsoft.example.EventHubWriter__ 및 __com.microsoft.example.EventHubReader__와 같은 두 개의 토폴로지를 포함합니다. EventHubReader에서 읽은 이벤트 허브에 이벤트를 쓰기 때문에 EventHubWriter 토폴로지를 먼저 시작해야 합니다.

###Linux 기반 클러스터를 사용하는 경우

1. SCP를 사용하여 HDInsight 클러스터에 jar 패키지를 복사합니다. 클러스터에 SSH 사용자로 사용자 이름을 바꿉니다. CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 예: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    > [AZURE.NOTE] 클라이언트가 Windows 워크스테이션이면 설치된 SCP 명령이 없을 수 있습니다. [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 PSCP를 사용하는 것이 좋습니다.

    이 명령은 클러스터의 SSH 사용자의 홈 디렉터리에 파일을 복사합니다.

1. 파일 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결할 수 있습니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME**을 HDInsight 클러스터 이름으로 바꿉니다.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    PuTTY를 사용하는 경우 __호스트 이름(또는 IP 주소)__ 필드에 `CLUSTERNAME-ssh.azurehdinsight.net`를 입력한 다음 __열기__를 클릭하여 연결합니다. SSH 계정 이름을 입력하라는 메시지가 표시됩니다.

    > [AZURE.NOTE] SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정으로 SSH 키를 사용하는 경우 다음 단계를 사용하여 키를 선택해야 합니다.
    >
    > 1. **Category**에서 **Connection**, **SSH**를 차례로 확장하고 **Auth**를 선택합니다.
    > 2. **찾아보기**를 클릭하고 개인 키가 포함된 .ppk 파일을 선택합니다.
    > 3. __열기__를 클릭하여 연결합니다.

2. 다음 명령을 사용하여 토폴로지를 시작합니다.

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    토폴로지를 시작하고 "판독기" 및 "작성자"에 친숙한 이름을 지정합니다.

3. 토폴로지가 이벤트 허브에서 이벤트를 쓰고 읽도록 하려면 1~2분 정도 기다렸다가 다음 명령을 사용하여 EventHubReader가 사용자의 HDInsight 저장소에 데이터를 저장하고 있는지 확인합니다.

        hadoop fs -ls /devicedata

    그러면 다음과 같은 파일 목록이 반환됩니다.

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] EventHubReader에서 만들어진 대로 일부 파일의 크기는 0이지만 아직 해당 파일에 데이터가 저장되지 않았습니다.

    다음 명령을 사용하여 파일의 내용을 볼 수 있습니다.

        hadoop fs -text /devicedata/*.txt

    이 명령은 다음과 유사한 데이터를 반환합니다.

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    첫 번째 열은 장치 ID 값을 포함하고 두 번째 열은 장치 값입니다.

4. 다음 명령을 사용하여 토폴로지를 중지합니다.

        storm kill reader
        storm kill writer

###Windows 기반 클러스터를 사용하는 경우

1. 브라우저를 https://CLUSTERNAME.azurehdinsight.net로 엽니다. 메시지가 표시되면 HDInsight 클러스터에 관리자 자격 증명을 입력합니다. Storm 대시보드에 도달하게 됩니다.

2. __Jar 파일__ 드롭다운을 사용하여 빌드 환경에서 EventHubExample-1.0-SNAPSHOT.jar 파일을 찾고 선택합니다.

3. __클래스 이름__에 `com.mirosoft.example.EventHubWriter`를 입력합니다.

4. __추가 매개 변수__에 `writer`를 입력합니다. 마지막으로 __제출__을 클릭하여 jar을 업로드하고 EventHubWriter 토폴로지를 시작합니다.

5. 토폴로지가 시작되면 형식을 사용하여 EventHubReader를 시작합니다.

    * __Jar 파일__: 이전에 업로드된 EventHubExample-1.0-SNAPSHOT.jar를 선택합니다.
    * __클래스 이름__: `com.microsoft.example.EventHubReader`를 입력합니다.
    * __추가 매개 변수__: `reader`를 입력합니다.

    EventHubReader 토폴로지를 시작하려면 제출을 클릭합니다.

6. 토폴로지가 이벤트를 생성한 다음 Azure 저장소에 저장하도록 하려면 몇 분 정도 기다렸다가 __Storm 대시보드__ 페이지 위쪽에 있는 __Hadoop 쿼리 콘솔__ 탭을 선택합니다.

7. __쿼리 콘솔__에서 __하이브 편집기__를 선택하고 기본 `select * from hivesampletable`을 다음으로 바꿉니다.

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;

    __선택__을 클릭하여 쿼리를 실행합니다. EventHubReader에서 Azure 저장소(WASB)로 작성한 데이터에서 10개의 행을 반환합니다. 쿼리가 완료되면 다음과 유사한 데이터가 표시됩니다.

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. 페이지의 위쪽에서 __Storm 대시보드__를 선택한 다음 __Storm UI__를 선택합니다. __Storm UI__에서__판독기__ 토폴로지에 대한 링크를 선택한 다음 __중단__ 단추를 사용하여 토폴로지를 중지합니다. __작성자__ 토폴로지에 프로세스를 반복합니다.



### 검사점 설정

EventHubSpout는 해당 상태의 검사점을 큐에서 읽은 메시지의 현재 오프셋을 저장하는 Zookeeper 노드로 주기적으로 설정합니다. 이를 통해 구성 요소는 다음 시나리오에서 저장된 오프셋으로 메시지 수신을 시작할 수 있습니다.

* 구성 요소 인스턴스가 실패하고 다시 시작된 경우

* 노드를 추가하거나 제거하여 클러스터를 확장 또는 축소한 경우

* 토폴로지자 종료되고 **동일한 이름**으로 다시 시작된 경우

####Windows 기반 HDInsight 클러스터에서

영구 검사점을 WASB(HDInsight 클러스터에서 사용하는 Azure 저장소)로 내보내고 가져올 수 있습니다. 이 작업을 수행하는 스크립트는 HDInsight의 Storm 클러스터(**c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**)에 있습니다.

>[AZURE.NOTE] 클러스터에 설치된 Storm 버전이 나중에 변경될 수 있으므로 경로의 버전 번호는 다를 수 있습니다.

이 디렉터리의 스크립트는 다음과 같습니다.

* **stormmeta\_import.cmd**: 클러스터 기본 저장소 컨테이너의 모든 Storm 메타데이터를 Zookeeper로 가져옵니다.

* **stormmeta\_export.cmd**: Zookeeper의 모든 Storm 메타데이터를 클러스터 기본 저장소 컨테이너로 내보냅니다.

* **stormmeta\_delete.cmd**: Zookeeper에서 모든 Storm 메타데이터를 삭제합니다.

내보내기 및 가져오기를 통해 클러스터를 삭제해야 하는 경우 검사점 데이터를 유지할 수 있지만 새 클러스터를 다시 온라인으로 전환할 때 허브의 현재 오프셋에서 처리를 다시 시작할 수 있습니다.

> [AZURE.NOTE] 데이터가 기본 저장소 컨테이너에 유지되므로 새 클러스터는 **반드시** 이전 클러스터와 동일한 저장소 계정 및 컨테이너를 사용해야 합니다.

## 클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##문제 해결

/devicedata 위치에 저장되지 않은 파일이 표시되지 않으면(`hadoop fs -ls /devicedata` 명령 또는 쿼리 콘솔에서 Hive 명령을 사용하여) Storm UI를 사용하여 토폴로지에서 반환된 오류를 찾습니다.

Storm UI 사용에 대한 자세한 내용은 다음 항목을 참조하세요.

* HDInsight 클러스터에서 __Linux 기반__ Storm을 사용하고 있다면 [Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요

* HDInsight 클러스터에서 __Windows 기반__ Storm을 사용하고 있다면 [Windows 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요

##다음 단계

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->