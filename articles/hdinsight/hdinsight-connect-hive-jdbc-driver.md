<properties
 pageTitle="JDBC를 사용하여 Azure HDInsight에서 Hive 쿼리"
 description="JDBC를 사용하여 Azure HDInsight의 Hive를 연결하고 클라우드에 저장된 데이터에서 쿼리를 원격으로 실행하는 방법을 알아봅니다."
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
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#Hive JDBC 드라이버를 사용하여 Azure HDInsight에서 Hive에 연결

[AZURE.INCLUDE [ODBC JDBC 선택기](../../includes/hdinsight-selector-odbc-jdbc.md)]

이 문서에서는 Java 응용 프로그램에서 JDBC를 사용하여 원격으로 HDInsight 클러스터에 Hive 쿼리를 제출하는 방법에 대해 배웁니다. SQuirreL SQL 클라이언트에서 연결하는 방법 및 Java에서 프로그래밍 방식으로 연결하는 방법을 학습합니다.

Hive JDBC 인터페이스에 대한 자세한 내용은 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)를 참조하세요.

##필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Hadoop. Linux 또는 Windows 기반의 클러스터가 작동합니다.

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL은 JDBC 클라이언트 응용 프로그램입니다.

이 문서에서 연결된 Java 예제 응용 프로그램을 빌드하고 실행하려면 다음이 필요합니다.

* [JDK(Java Developer Kit) 버전 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 이상

* [Apache Maven](https://maven.apache.org). Maven은 이 문서와 관련된 프로젝트에서 사용되는 Java 프로젝트에 대한 프로젝트 빌드 시스템입니다.

##연결 문자열

Azure에서 HDInsight 클러스터에 대한 JDBC가 443을 통해 연결되어 트래픽이 SSL을 사용하여 보호됩니다. 클러스터가 뒤에 있는 공용 게이트웨이는 HiveServer2에서 실제로 수신하는 포트로 트래픽을 리디렉션합니다. 따라서 일반적인 연결 문자열은 다음과 같습니다.

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

__CLUSTERNAME__을 HDInsight 클러스터의 이름으로 바꿉니다.

##인증

연결을 설정할 때 HDInsight 클러스터 관리자 이름 및 암호를 사용하여 클러스터 게이트웨이에 대해 인증해야 합니다. SQuirreL SQL 등의 JDBC 클라이언트에서 연결할 때 클라이언트 설정에 관리자 이름 및 암호를 입력해야 합니다.

Java 응용 프로그램에서 연결을 설정할 때 이름 및 암호를 사용해야 합니다. 예를 들어 다음 Java 코드는 연결 문자열, 관리자 이름 및 암호를 사용하여 새 연결을 엽니다.

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##SQuirreL SQL 클라이언트를 사용하여 연결

SQuirreL SQL은 HDInsight 클러스터와 함께 Hive 쿼리를 원격으로 실행하는 데 사용할 수 있는 JDBC 클라이언트입니다. 다음 단계에서는 SQuirreL SQL을 이미 설치했다고 가정하고 Hive에 대한 드라이버를 다운로드 및 구성하는 과정을 안내합니다.

1. HDInsight 클러스터에서 Hive JDBC 드라이버를 복사합니다.

    * __Linux 기반 HDInsight__의 경우 다음 단계에 따라 필요한 jar 파일을 다운로드합니다.

        1. 파일을 포함할 새 디렉터리를 만듭니다. 예: `mkdir hivedriver`.

        2. 명령 프롬프트, Bash, PowerShell 또는 다른 명령줄 프롬프트에서 새 디렉터리로 변경하고 다음 명령을 사용하여 HDInsight 클러스터에서 파일을 복사합니다.

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            __USERNAME__을 클러스터에 대한 SSH 사용자 계정으로 바꿉니다. __CLUSTERNAME__을 HDInsight 클러스터 이름으로 바꿉니다.

            > [AZURE.NOTE] Windows 환경에서는 scp 대신 PSCP 유틸리티를 사용해야 합니다. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있습니다.

    * __Windows 기반 HDInsight__의 경우 다음 단계에 따라 jar 파일을 다운로드합니다.

        1. Azure 포털에서, HDInsight 클러스터를 선택한 다음 __원격 데스크톱__ 아이콘을 선택합니다.

            ![원격 데스크톱 아이콘](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. 원격 데스크톱 블레이드에서 __연결__ 단추를 사용하여 클러스터에 연결합니다. 원격 데스크톱을 사용하지 않는 경우 사용자 이름 및 암호를 입력하는 양식을 사용한 후 __사용__을 선택하여 클러스터에 대해 원격 데스크톱을 사용하도록 합니다.

            ![원격 데스크톱 블레이드](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            __연결__을 선택하면 .rdp 파일이 다운로드됩니다. 이 파일을 사용하여 원격 데스크톱 클라이언트를 시작합니다. 메시지가 표시되면 원격 데스크톱 액세스에 입력한 사용자 이름 및 암호를 사용합니다.

        3. 연결되면 원격 데스크톱 세션에서 다음 파일을 로컬 컴퓨터에 복사합니다. `hivedriver`라는 이름의 로컬 디렉터리에 넣습니다.

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] 경로 및 파일 이름에 포함된 버전 번호는 클러스터마다 다를 수 있습니다.

        4. 파일을 복사한 후 원격 데스크톱 세션의 연결을 끊습니다.

3. SQuirreL SQL 응용 프로그램을 시작합니다. 왼쪽 창에서 __드라이버__를 선택합니다.

    ![창 왼쪽의 드라이버 탭](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. __드라이버__ 대화 상자 맨 위에 있는 아이콘에서 __+__ 아이콘을 선택하여 새 드라이버를 만듭니다.

    ![드라이버 아이콘](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. 드라이버 추가 대화 상자에 다음 정보를 추가합니다.

    * __이름__: Hive
    * __예제 URL__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __추가 클래스 경로__: 추가 단추를 사용하여 이전에 다운로드한 jar 파일을 추가합니다.
    * __클래스 이름__: org.apache.hive.jdbc.HiveDriver

    ![드라이버 추가 대화 상자](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    __확인__을 클릭하여 이러한 설정을 저장합니다.

6. SQuirreL SQL 창의 왼쪽에서 __별칭__을 선택합니다. 그런 다음 __+__ 아이콘을 클릭하여 새 연결 별칭을 만듭니다.

    ![새 별칭 추가](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. __별칭 추가__ 대화 상자에 대해 다음 값을 사용합니다.

    * __이름__: Hive on HDInsight
    * __드라이버__: 드롭다운을 사용하여 __Hive__ 드라이버 선택
    * __URL__: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        __CLUSTERNAME__을 HDInsight 클러스터의 이름으로 바꿉니다.

    * __사용자 이름__: HDInsight 클러스터에 대한 클러스터 로그인 계정 이름입니다. 기본값은 `admin`입니다.
    * __암호__: 클러스터 로그인 계정의 암호입니다. HDInsight 클러스터를 만들 때 제공한 암호입니다.

    ![별칭 추가 대화 상자](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    __테스트__ 단추를 사용하여 연결이 작동하는지 확인합니다. __연결 대상: Hive on HDInsight__ 대화 상자가 나타나면 __연결__을 선택하여 테스트를 수행합니다. 테스트가 성공하면 __연결 성공__ 대화 상자가 표시됩니다.

    __별칭 추가__ 대화 상자의 하단에서 __확인__을 사용하여 연결 별칭을 저장합니다.

8. SQuirreL SQL 맨 위의 __연결 대상__ 드롭다운에서 __Hive on HDInsight__를 선택합니다. 메시지가 표시되면 __연결__을 선택합니다.

    ![연결 대화 상자](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. 연결되면 SQL 쿼리 대화 상자에 다음 쿼리를 입력한 후 __실행__ 아이콘을 선택합니다. 결과 영역에 쿼리 결과가 표시됩니다.

        select * from hivesampletable limit 10;

    ![결과를 포함한 sql 쿼리 대화 상자](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##Java 응용 프로그램 예제에서 연결

HDInsight에서 Hive를 쿼리하는 데 Java 클라이언트를 사용하는 예제를 [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)에서 사용할 수 있습니다. 리포지토리의 지침에 따라 샘플을 빌드하고 실행합니다.

##문제 해결

### SQL 연결을 열 때 예기치 않은 오류가 발생했습니다.

__증상__: 버전 3.3 또는 3.4인 HDInsight 클러스터에 연결할 때 예기치 않은 오류가 발생할 수 있습니다. 이 오류에 대한 스택 추적은 다음 줄로 시작합니다.

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__원인__: 이 오류는 SQuirreL에서 사용하는 common-codec.jar 파일 버전과 HDInsight 클러스터에서 다운로드한 Hive JDBC 구성 요소에 필요한 파일의 버전 불일치로 인해 발생합니다.

__해결__:이 오류를 해결하려면 다음 단계를 수행합니다.

1. HDInsight 클러스터에서 common-codec jar 파일을 다운로드합니다.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. SQuirreL을 종료한 다음 시스템에서 SQuirreL이 설치된 디렉터리로 이동합니다. SquirreL 디렉터리의 `lib` 디렉터리에서 기존 common-codec.jar 파일을 HDInsight 클러스터에서 다운로드한 파일로 바꿉니다.

3. SQuirreL을 다시 시작합니다. HDInsight에서 Hive에 연결할 때 오류가 더 이상 발생하지 않아야 합니다.

##다음 단계

JDBC를 사용하여 Hive와 함께 작업하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드](hdinsight-upload-data.md)
* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->