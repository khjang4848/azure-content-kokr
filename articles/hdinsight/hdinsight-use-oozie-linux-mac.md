<properties
	pageTitle="Linux 기반 HDInsight에서 Hadoop Oozie 워크플로 사용 | Microsoft Azure"
	description="Linux 기반 HDInsight에서 Hadoop Oozie를 사용합니다. 또한 Oozie 워크플로를 정의하고 Oozie 작업을 제출하는 방법에 대해서도 살펴봅니다."
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
	ms.date="07/25/2016"
	ms.author="larryfr"/>


# Hadoop과 함께 Oozie를 사용하여 Linux 기반 HDInsight에서 워크플로 정의 및 실행

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apache Oozie를 사용하여 Hive 및 Sqoop를 사용하는 워크플로 정의한 다음 Linux 기반 HDInsight 클러스터에서 워크플로 실행하는 방법에 대해 알아봅니다.

Apache Oozie는 Hadoop 작업을 관리하는 워크플로/코디네이션 시스템입니다. Hadoop 스택과 통합되며 Apache MapReduce, Apache Pig, Apache Hive, Apache Sqoop에 Hadoop 작업을 지원합니다. 또한 Java 프로그램이나 셸 스크립트와 같은 시스템에 특정한 작업을 예약하는 데 사용할 수도 있습니다.

> [AZURE.NOTE] HDInsight를 사용하여 워크플로를 정의하는 또 다른 옵션은 Azure 데이터 팩터리입니다. Azure 데이터 팩터리에 대한 자세한 내용은 [데이터 팩터리에서 Pig 및 Hive 사용][azure-data-factory-pig-hive]을 참조하세요.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**: [Azure 무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.

- **Azure CLI**: [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
	
	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- **HDInsight 클러스터**: [Linux에서 HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

- **Azure SQL 데이터베이스**: 이 문서의 단계를 사용하여 생성됩니다.

##예제 워크플로

이 문서의 지침에 따라 구현할 워크플로에는 두 가지 동작이 포함되어 있습니다. 동작은 Hive, Sqoop, MapReduce 또는 기타 프로세스 실행과 같은 작업에 대한 정의입니다.

![워크플로 다이어그램][img-workflow-diagram]

1. Hive 동작은 HiveQL을 실행하여 HDInsight에 포함된 **hivesampletable**에서 레코드를 추출합니다. 데이터의 각 행은 특정 모바일 장치에서의 방문을 설명합니다. 레코드 형식은 다음과 유사하게 표시됩니다.

		8       18:54:20        ko-KR   Android Samsung SCH-i500        California     United States    13.9204007      0       0
		23      19:19:44        ko-KR   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
		23      19:19:46        ko-KR   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

	이 문서에서 사용된 Hive 스크립트는 각 플랫폼(예: Android 또는 iPhone)에 대한 총 방문 횟수를 계산하여 새 Hive 테이블에 저장합니다.

	Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.

2.  Sqoop 동작은 새 Hive 테이블의 내용을 Azure SQL 데이터베이스의 테이블로 내보냅니다. Sqoop에 대한 자세한 내용은 [HDInsight에서 Hadoop Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

> [AZURE.NOTE] HDInsight 클러스터에서 지원되는 Oozie 버전에 대해서는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions]을 참조하세요.

##작업 디렉터리 만들기

Oozie에는 작업을 같은 디렉터리에 저장하는 데 사용되는 리소스가 필요합니다. 이 예제는 **wasbs:///tutorials/useoozie**를 사용합니다. 다음 명령을 사용하여 이 디렉터리와 이 워크플로에서 만든 새 Hive 테이블을 유지할 data 디렉터리를 만듭니다.

	hdfs dfs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE] `-p` 매개 변수는 경로의 모든 디렉터리(없는 경우)가 만들어지도록 합니다. **data** 디렉터리는 **useooziewf.hql** 스크립트에서 사용되는 데이터를 유지하는 데 사용됩니다.

또한 Hive 및 Sqoop 작업을 실행할 때 Oozie가 사용자 계정을 가장할 수 있도록 다음 명령을 실행합니다. **USERNAME**을 로그인 이름으로 바꿉니다.

	sudo adduser USERNAME users

사용자가 이미 users의 멤버라는 오류가 발생하는 경우 무시하면 됩니다.

##데이터베이스 드라이버 추가

이 워크플로에서는 Sqoop를 사용하여 SQL 데이터베이스로 데이터를 내보내므로 SQL 데이터베이스와 통신하는 데 사용되는 JDBC 드라이버의 복사본을 제공해야 합니다. 다음 명령을 사용하여 작업 디렉터리에 복사합니다.

	hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

워크플로에서 MapReduce 응용 프로그램이 포함된 jar과 같은 다른 리소스를 사용하는 경우 이러한 리소스도 추가해야 합니다.

##Hive 쿼리 정의

다음 단계를 사용하여 쿼리를 정의하는 는 HiveQL 스크립트를 만듭니다. 이 스크립트는 이 문서의 뒷부분에 나오는 Oozie 워크플로에서 사용됩니다.

1. SSH를 사용하여 Linux 기반 HDInsight 클러스터에 연결합니다.

    * **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

    * **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

2. 다음 명령을 사용하여 새 파일을 만듭니다.

        nano useooziewf.hql

1. nano 편집기가 열리면 파일 내용으로 다음을 사용합니다.

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
		FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

	스크립트에서 사용되는 두 가지 변수는 다음과 같습니다.

	- **${hiveTableName}**: 만들려는 테이블의 이름을 포함합니다.
	- **${hiveDataFolder}**: 테이블에 대한 데이터 파일을 저장할 위치를 포함합니다

	워크플로 정의 파일(이 자습서에서는 workflow.xml)은 런타임 시 이러한 값을 이 HiveQL 스크립트에 전달합니다.

2. Ctrl-X를 눌러 편집기를 종료합니다. 메시지가 나타나면 **Y**를 선택하여 파일을 저장한 다음 **Enter** 키를 눌러 **useooziewf.hql** 파일 이름을 사용합니다.

3. 다음 명령을 사용하여 **useooziewf.hql**을 **wasbs:///tutorials/useoozie/useooziewf.hql**에 복사합니다.

		hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

	이러한 명령은 이 클러스터와 연결된 Azure 저장소 계정에 **useooziewf.hql** 파일을 저장합니다. 그러면 클러스터가 삭제된 경우에도 파일이 보존됩니다. 이렇게 하면 클러스터를 사용하지 않을 때 작업 및 워크플로를 유지하면서 클러스터를 삭제하여 비용을 절감할 수 있습니다.

##워크플로 정의

Oozie 워크플로 정의는 hPDL(XML 프로세스 정의 언어)로 작성되었습니다. 다음 단계를 사용하여 워크플로를 정의합니다.

1. 다음 문을 사용하여 새 파일을 만들고 편집합니다.

        nano workflow.xml

1. nano 편집기가 열리면 파일 내용으로 다음을 입력합니다.

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>
            <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
            </action>
            <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>

	워크플로에 정의된 두 가지 동작은 다음과 같습니다.

	- **RunHiveScript**: 시작 동작이며 **useooziewf.hql** Hive 스크립트를 실행합니다.

	- **RunSqoopExport**: Sqoop를 사용하여 Hive 스크립트에서 만든 데이터를 SQL 데이터베이스로 내보냅니다. 이 동작은 **RunHiveScript** 동작에 성공한 경우에만 실행됩니다.

		> [AZURE.NOTE] Oozie 워크플로 및 워크플로 작업 사용에 대한 자세한 내용은 [Apache Oozie 4.0 설명서][apache-oozie-400](HDInsight 버전 3.0의 경우) 또는 [Apache Oozie 3.3.2 설명서][apache-oozie-332](HDInsight 버전 2.1의 경우)를 참조하세요.

	워크플로에는 이 문서의 뒷부분에 나오는 작업 정의에서 사용하는 값으로 대체될 `${jobTracker}`와 같은 여러 항목이 있습니다.

	Sqoop 섹션의 `<archive>sqljdbc4.jar</arcive>` 항목도 있습니다. 이 항목은 이 동작을 실행할 때 이 보관 파일을 Sqoop에 사용할 수 있게 설정하도록 Oozie에 지시합니다.

2. Ctrl-X를 사용한 다음 **Y**와 **Enter** 키를 사용하여 파일을 저장합니다.

3. 다음 명령을 사용하여 **workflow.xml** 파일을 **wasbs:///tutorials/useoozie/workflow.xml**에 복사합니다.

		hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

##데이터베이스 만들기

[SQL 데이터베이스 만들기](../sql-database/sql-database-get-started.md) 문서의 단계에 따라 새 SQL 데이터베이스를 만듭니다. 데이터베이스를 만들 때 데이터베이스 이름으로 __oozietest__를 사용합니다. 또한 다음 섹션에 필요하므로 데이터베이스 서버에 사용된 이름을 기록해 둡니다.

###테이블 만들기

> [AZURE.NOTE] 여러 가지 방법으로 SQL 데이터베이스에 연결하여 테이블을 생성할 수 있습니다. 다음 단계는 HDInsight 클러스터의 [FreeTDS](http://www.freetds.org/)를 사용합니다.

3. 다음 명령을 사용하여 HDInsight 클러스터에 FreeTDS를 설치합니다.

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. FreeTDS가 설치되면 다음 명령을 사용하여 이전에 생성한 SQL 데이터베이스 서버에 연결합니다.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest

    다음과 유사한 출력이 표시됩니다.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. `1>` 프롬프트에 다음 행을 입력합니다.

		CREATE TABLE [dbo].[mobiledata](
		[deviceplatform] [nvarchar](50),
		[count] [bigint])
		GO
		CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
		GO

    `GO` 명령문을 입력하면 이전 명령문이 평가됩니다. 이 명령문은 Sqoop에서 기록할 **mobiledata**라는 새 테이블을 만듭니다.

    다음을 사용하여 테이블이 생성되었는지 확인합니다.

        SELECT * FROM information_schema.tables
        GO

    다음과 비슷한 결과가 나타나야 합니다.

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. `1>` 프롬프트에서 `exit`를 입력하여 tsql 유틸리티를 종료합니다.

##작업 정의 만들기

작업 정의는 workflow.xml 및 워크플로에서 사용되는 기타 파일(예: useooziewf.hql)을 찾을 수 있는 위치를 설명합니다. 또한 워크플로 및 관련 파일 내에서 사용되는 속성 값을 정의합니다.

1. 다음 명령을 사용하여 전체 WASB 주소를 기본 저장소로 가져옵니다. 이 주소는 잠시 후 구성 파일에서 사용됩니다.

		sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

	다음과 유사한 정보가 반환됩니다.

		<name>fs.defaultFS</name>
		<value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>

	다음 단계에서 사용되므로 **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** 값을 저장합니다.

2. 다음 명령을 사용하여 클러스터 헤드 노드의 FQDN을 가져옵니다. FQDN은 클러스터의 JobTracker 주소에 사용됩니다. 이 주소는 잠시 후 구성 파일에서 사용됩니다.

		hostname -f

	이 명령은 다음과 유사한 정보를 반환합니다.

		hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

	JobTracker에 사용되는 포트는 8050이므로 JobTracker에 사용할 전체 주소는 **hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**입니다.

1. 다음을 사용하여 Oozie 작업 정의 구성을 만듭니다.

		nano job.xml

2. nano 편집기가 열리면 파일 내용으로 다음을 사용합니다.

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		  <property>
		    <name>nameNode</name>
		    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
		  </property>

		  <property>
		    <name>jobTracker</name>
		    <value>JOBTRACKERADDRESS</value>
		  </property>

		  <property>
		    <name>queueName</name>
		    <value>default</value>
		  </property>

		  <property>
		    <name>oozie.use.system.libpath</name>
		    <value>true</value>
		  </property>

		  <property>
		    <name>hiveScript</name>
		    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
		  </property>

		  <property>
		    <name>hiveTableName</name>
		    <value>mobilecount</value>
		  </property>

		  <property>
		    <name>hiveDataFolder</name>
		    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
		  </property>

		  <property>
		    <name>sqlDatabaseConnectionString</name>
		    <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
		  </property>

		  <property>
		    <name>sqlDatabaseTableName</name>
		    <value>mobiledata</value>
		  </property>

		  <property>
		    <name>user.name</name>
		    <value>YourName</value>
		  </property>

		  <property>
		    <name>oozie.wf.application.path</name>
		    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		  </property>
		</configuration>

	* **wasbs://mycontainer@mystorageaccount.blob.core.windows.net**의 모든 인스턴스를 이전에 받은 값으로 바꿉니다.

	> [AZURE.WARNING] 컨테이너 및 저장소 계정이 경로의 일부로 포함된 전체 WASB 경로를 사용해야 합니다. 약식 표현(wasbs:///)을 사용하면 작업이 시작될 때 RunHiveScript 동작이 실패합니다.

	* **JOBTRACKERADDRESS**를 이전에 받은 JobTracker/ResourceManager 주소로 바꿉니다.

	* **YourName**을 HDInsight 클러스터의 로그인 이름으로 바꿉니다.

	* **serverName**, **adminLogin** 및 **adminPassword**를 Azure SQL 데이터베이스에 대한 정보로 바꿉니다.

	이 파일의 정보는 대부분 workflow.xml 또는 ooziewf.hql 파일에서 사용되는 값(예: ${nameNode})을 채우는 데 사용됩니다.

	> [AZURE.NOTE] **oozie.wf.application.path** 항목은 이 작업에서 실행한 워크플로를 포함하는 workflow.xml 파일을 찾을 수 있는 위치를 정의합니다.

2. Ctrl-X를 사용한 다음 **Y**와 **Enter** 키를 사용하여 파일을 저장합니다.

##작업 제출 및 관리

다음 단계에서는 Oozie 명령을 사용하여 클러스터에서 Oozie 워크플로를 제출 및 관리합니다. Oozie 명령은 [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)를 통해 제공되는 친숙한 인터페이스입니다.

> [AZURE.IMPORTANT] Oozie 명령을 사용할 때는 HDInsight 헤드 노드에 대한 FQDN을 사용해야 합니다. 이 FQDN은 클러스터에서만 액세스할 수 있으며, 클러스터가 Azure 가상 네트워크에 있는 경우에는 같은 네트워크에 있는 다른 컴퓨터에서 액세스할 수 있습니다.

1. 다음을 사용하여 Oozie 서비스의 URL을 가져옵니다.

		sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

	이 명령은 다음과 유사한 값을 반환합니다.

		<name>oozie.base.url</name>
		<value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

	**http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** 부분은 Oozie 명령에서 사용할 URL입니다.

2. 모든 명령에 입력할 필요 없이 다음을 사용하여 URL에 대한 환경 변수를 만들 수 있습니다.

		export OOZIE_URL=http://HOSTNAMEt:11000/oozie

	URL을 이전에 받은 URL로 바꿉니다.

3. 다음을 사용하여 작업을 제출합니다.

		oozie job -config job.xml -submit

	이 명령은 **job.xml**에서 작업 정보를 로드하고 Oozie에 제출하지만 실행하지는 않습니다.

	명령이 완료되면 작업의 ID가 반환됩니다. 예: `0000005-150622124850154-oozie-oozi-W`. 이 명령은 작업을 관리하는 데 사용됩니다.

4. 다음 명령을 사용하여 작업의 상태를 확인합니다. 이전 명령에서 반환된 작업 ID를 입력합니다.

		oozie job -info <JOBID>

	이 명령은 다음과 유사한 정보를 반환합니다.

		Job ID : 0000005-150622124850154-oozie-oozi-W
		------------------------------------------------------------------------------------------------------------------------------------
		Workflow Name : useooziewf
		App Path      : wasbs:///tutorials/useoozie
		Status        : PREP
		Run           : 0
		User          : USERNAME
		Group         : -
		Created       : 2015-06-22 15:06 GMT
		Started       : -
		Last Modified : 2015-06-22 15:06 GMT
		Ended         : -
		CoordAction ID: -
		------------------------------------------------------------------------------------------------------------------------------------

	이 작업의 상태는 `PREP`입니다. 이는 작업이 제출되었지만 아직 시작되지 않았음을 나타냅니다.

4. 다음을 사용하여 작업을 시작합니다.

		oozie job -start JOBID

	이 명령 후 상태를 확인하면 실행 중 상태가 표시되고 작업 내 동작에 대한 정보가 반환됩니다.

5. 작업이 성공적으로 완료되면 다음 명령을 사용하여 데이터가 생성되고 SQL 데이터베이스 테이블로 내보내졌음을 확인할 수 있습니다.

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

	`1>` 프롬프트에 다음을 입력합니다.

		SELECT * FROM mobiledata
		GO

	다음과 유사한 정보가 표시됩니다.

		deviceplatform  count
		Android 31591
		iPhone OS       22731
		proprietary development 3
		RIM OS  3464
		Unknown 213
		Windows Phone   1791
		(6 rows affected)

Oozie 명령에 대한 자세한 내용은 [Oozie 명령줄 도구](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)를 참조하세요.

##Oozie REST API

Oozie REST API를 사용하면 Oozie와 함께 작동하는 사용자 고유의 도구를 빌드할 수 있습니다. 다음은 Oozie REST API 사용에 대한 HDInsight 관련 정보입니다.

* **URI**: 클러스터 외부의 `https://CLUSTERNAME.azurehdinsight.net/oozie`에서 REST API에 액세스할 수 있습니다

* **인증**: 클러스터 HTTP 계정(admin) 및 암호를 사용하여 API를 인증해야 합니다. 예:

		curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Oozie REST API 사용에 대한 자세한 내용은 [Oozie 웹 서비스 API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)를 참조하세요.

##Oozie 웹 UI

Oozie 웹 UI는 클러스터의 Oozie 작업 상태에 대한 웹 기반 보기를 제공합니다. 이를 통해 작업 상태, 작업 정의, 구성, 작업의 동작에 대한 그래프 및 작업 로그를 볼 수 있습니다. 또한 작업 내의 동작에 대한 세부 정보를 볼 수 있습니다.

Oozie 웹 UI에 액세스하려면 다음 단계를 사용하세요.

1. HDInsight 클러스터에 대한 SSH 터널을 만듭니다. 자세한 내용은 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

2. 터널을 만든 후 웹 브라우저에서 Ambari 웹 UI를 엽니다. Ambari 사이트의 URI는 **https://CLUSTERNAME.azurehdinsight.net**입니다. **CLUSTERNAME**을 Linux 기반 HDInsight 클러스터의 이름으로 바꿉니다.

3. 페이지의 왼쪽부터 **Oozie**, **빠른 링크**, **Oozie 웹 UI**를 차례로 선택합니다.

	![메뉴 이미지](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Oozie 웹 UI는 기본적으로 실행 중인 워크플로 작업을 표시합니다. 모든 워크플로 작업을 보려면 **모든 작업**을 선택합니다.

	![모든 작업 표시](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. 작업에 대한 자세한 정보를 보려면 해당 작업을 선택합니다.

	![작업 정보](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Job Info 탭에서 기본 작업 정보 및 작업 내의 개별 동작을 볼 수 있습니다. 맨 위에 있는 탭을 사용하여 작업 정의 및 작업 구성을 보거나, 작업 로그에 액세스하거나, 작업의 DAG(방향성 비순환 그래프)를 확인할 수 있습니다.

	* **작업 로그**: **GetLogs** 단추를 선택하여 작업에 대한 모든 로그를 가져오거나 **검색 필터 입력** 필드를 사용하여 로그를 필터링합니다.

		![작업 로그](./media/hdinsight-use-oozie-linux-mac/joblog.png)

	* **JobDAG**: DAG는 워크플로를 통해 가져온 데이터 경로의 그래픽 개요입니다.

		![작업 DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. **작업 정보** 탭에서 동작 중 하나를 선택하면 해당 동작에 대한 정보가 표시됩니다. 예를 들어 **RunHiveScript** 동작을 선택합니다.

	![동작 정보](./media/hdinsight-use-oozie-linux-mac/action.png)

8. 작업에 대한 JobTracker 정보를 보는 데 사용할 수 있는 **콘솔 URL**의 링크를 포함하여 동작에 대한 세부 정보를 확인할 수 있습니다.

##작업 예약

코디네이터를 통해 작업의 시작, 종료 및 발생 빈도를 지정하여 특정 시간대를 예약할 수 있습니다.

워크플로 일정을 정의하려면 다음 단계를 사용합니다.

1. 다음을 사용하여 **coordinator.xml**이라는 새 파일을 만듭니다.

		nano coordinator.xml

	파일 내용으로 다음을 사용합니다.

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
	      <action>
	        <workflow>
	          <app-path>${workflowPath}</app-path>
	        </workflow>
	      </action>
		</coordinator-app>

	여기에 사용된 `${...}` 변수는 작업 정의의 값으로 대체됩니다. 변수는 다음과 같습니다.

	* **${coordFrequency}**: 실행 중인 작업 인스턴스 간의 시간
	* **${coordStart}**: 작업 시작 시간
	* **${coordEnd}**: 작업 종료 시간
	* **${coordTimezone}**: 일광 절약 시간제(일반적으로 UTC를 사용하여 표시됨) 없이 고정된 표준 시간대에서 코디네이터 작업을 처리합니다. 이 시간대를 "Oozie 처리 시간대"라고 합니다.
	* **${wfPath}**: workflow.xml의 경로

2. Ctrl-X를 사용한 다음 **Y**와 **Enter** 키를 사용하여 파일을 저장합니다.

3. 다음을 사용하여 이 작업에 대한 작업 디렉터리에 복사합니다.

		hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. 다음을 사용하여 **job.xml** 파일을 수정합니다.

		nano job.xml

	다음과 같이 변경합니다.

	* `<name>oozie.wf.application.path</name>`을 `<name>oozie.coord.application.path</name>`으로 변경합니다. 이렇게 하면 Oozie가 워크플로 파일 대신 코디네이터 파일을 실행합니다.

	* 다음을 추가하여 coordinator.xml에 사용된 변수를 workflow.xml의 위치를 가리키도록 설정합니다.

		    <property>
		      <name>workflowPath</name>
		      <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		    </property>

		**mycontainer** 및 **mystorageaccount** 값을 job.xml 파일의 다른 항목에서 사용되는 값으로 바꿉니다.

	* 다음을 추가하여 coordinator.xml 파일에 사용할 시작, 종료 및 빈도를 정의합니다.

		    <property>
		      <name>coordStart</name>
		      <value>2015-06-25T12:00Z</value>
		    </property>

		    <property>
		      <name>coordEnd</name>
		      <value>2015-06-27T12:00Z</value>
		    </property>

		    <property>
		      <name>coordFrequency</name>
		      <value>1440</value>
		    </property>

		    <property>
		      <name>coordTimezone</name>
		      <value>UTC</value>
		    </property>

		시작 시간은 2015년 6월 25일 오후 12시로 설정되고, 종료 시간은 2015년 6월 27일로 설정되며, 이 작업의 실행 간격은 매일(빈도는 분 단위이므로 24시간 x 60분 = 1440분)로 설정됩니다. 마지막으로, 표준 시간대는 UTC로 설정됩니다.

5. Ctrl-X를 사용한 다음 **Y**와 **Enter** 키를 사용하여 파일을 저장합니다.

6. 작업을 실행하려면 다음 명령을 사용합니다.

		oozie job -config job.xml -run

	이 명령은 작업을 제출하고 시작합니다.

7. Oozie 웹 UI를 방문하여 **코디네이터 작업** 탭을 선택하면 다음과 유사한 정보가 표시됩니다.

	![코디네이터 작업 탭](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

	**다음 Materialization** 항목을 기록해 둡니다. 이 항목은 다음에 작업을 실행할 때 사용됩니다.

8. 이전 워크플로 작업과 마찬가지로 웹 UI에서 작업 항목을 선택하면 작업에 대한 정보가 표시됩니다.

	![코디네이터 작업 정보](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

	성공한 작업 실행만 표시되며, 예약된 워크플로 내의 개별 동작은 표시되지 않습니다. 확인하려면 **작업** 항목 중 하나를 선택합니다. 이전 워크플로 작업에 대해 검색된 것과 유사한 정보가 표시됩니다.

	![동작 정보](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##문제 해결

Oozie UI를 사용하면 Oozie 로그뿐 아니라 Hive 쿼리와 같은 MapReduce 작업에 대한 JobTracker 로그의 링크를 쉽게 볼 수 있으므로 Oozie 작업 문제를 해결할 때 매우 유용합니다. 일반적으로 문제 해결 패턴은 다음과 같습니다.

1. Oozie 웹 UI에서 작업을 확인합니다.

2. 특정 동작에 대한 오류 또는 실패가 있는 경우 해당 동작을 선택하여 **오류 메시지** 필드에 오류에 대한 자세한 정보가 제공되는지 확인합니다.

3. 사용 가능한 경우 동작의 URL을 사용하여 해당 동작에 대한 자세한 정보(예: JobTracker 로그)를 확인할 수 있습니다.

다음은 발생할 수 있는 특정 오류 및 해결 방법입니다.

###JA009: 클러스터를 초기화할 수 없음

**증상**: 작업 상태가 **일시 중단**으로 변경됩니다. 작업에 대한 세부 정보에 RunHiveScript 상태가 **START\_MANUAL**로 표시됩니다. 동작을 선택하면 다음과 같은 오류 메시지가 표시됩니다.

	JA009: Cannot initialize Cluster. Please check your configuration for map

**원인**: **job.xml** 파일에 사용된 WASB 주소에 저장소 컨테이너 또는 저장소 계정 이름이 포함되어 있지 않습니다. WASB 주소 형식은 `wasbs://containername@storageaccountname.blob.core.windows.net`이어야 합니다.

**해결 방법**: 작업에서 사용하는 WASB 주소를 변경합니다.

###JA002: Oozie에서 &lt;사용자>를 가장할 수 없음

**증상**: 작업 상태가 **일시 중단**으로 변경됩니다. 작업에 대한 세부 정보에 RunHiveScript 상태가 **START\_MANUAL**로 표시됩니다. 동작을 선택하면 다음과 같은 오류 메시지가 표시됩니다.

	JA002: User: oozie is not allowed to impersonate <USER>

**원인**: 현재 사용 권한 설정에서 Oozie가 지정된 사용자 계정을 가장하도록 허용하지 않습니다.

**해결 방법**: **사용자** 그룹에서 Oozie가 사용자를 가장하도록 허용합니다. `groups USERNAME`을 사용하여 사용자 계정이 멤버로 속해 있는 그룹을 확인합니다. 사용자가 **users** 그룹의 멤버가 아닌 경우 다음 명령을 사용하여 사용자를 그룹에 추가합니다.

	sudo adduser USERNAME users

> [AZURE.NOTE] HDInsight에서 사용자가 그룹에 추가된 것을 인식하는 데 몇 분 정도 걸릴 수 있습니다.

###시작 관리자 오류(Sqoop)

**증상**: 작업 상태가 **종료**로 변경됩니다. 작업에 대한 세부 정보에 RunSqoopExport 상태가 **오류**로 표시됩니다. 동작을 선택하면 다음과 같은 오류 메시지가 표시됩니다.

	Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**원인**: Sqoop가 데이터베이스에 액세스하는 데 필요한 데이터베이스 드라이버를 로드할 수 없습니다.

**해결 방법**: Oozie 작업에서 Sqoop를 사용할 경우 작업에서 사용하는 다른 리소스(예: workflow.xml)에 데이터베이스 드라이버를 포함해야 합니다.

또한 workflow.xml의 `<sqoop>...</sqoop>` 섹션에서 데이터베이스 드라이버가 포함된 보관 파일을 참조해야 합니다.

예를 들어 이 문서의 작업에는 다음 단계를 사용합니다.

1. sqljdbc4.1.jar 파일을 /tutorials/useoozie 디렉터리에 복사합니다.

		 hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar

2. workflow.xml을 수정하여 `</sqoop>` 위의 새 줄에 다음을 추가합니다.

		<archive>sqljdbc41.jar</archive>

##다음 단계
이 자습서에서는 Oozie 워크플로를 정의하는 방법 및 Oozie 작업을 실행하는 방법을 알아보았습니다. HDInsight 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight에서 시간 기준의 Oozie 코디네이터 사용][hdinsight-oozie-coordinator-time]
- [HDInsight에서 Hadoop 작업용 데이터 업로드][hdinsight-upload-data]
- [HDInsight에서 Hadoop과 Sqoop 사용][hdinsight-use-sqoop]
- [HDInsight에서 Hadoop과 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Hadoop과 Pig 사용][hdinsight-use-pig]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/ko-KR/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=AcomDC_0914_2016-->