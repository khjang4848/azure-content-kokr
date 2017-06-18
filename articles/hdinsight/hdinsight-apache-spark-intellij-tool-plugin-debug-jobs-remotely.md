 <properties
	pageTitle="IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터에서 실행 중인 응용 프로그램을 원격으로 디버그 | Microsoft Azure"
	description="IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터에서 실행 중인 응용 프로그램을 원격으로 디버그하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="nitinme"/>


# HDInsight Spark Linux 클러스터에서 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램을 원격으로 디버그

이 문서에서는 IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 HDInsight Spark 클러스터에서 Spark 작업을 제출한 다음 데스크톱 컴퓨터에서 원격으로 디버그하는 방법에 대한 단계별 지침을 제공합니다. 이렇게 하려면 다음과 같은 개략적인 단계를 수행해야 합니다.

1. 사이트 간 또는 지점 및 사이트 간 Azure 가상 네트워크를 만듭니다. 이 문서의 단계에서는 사이트 간 네트워크를 사용하는 것으로 가정합니다.

2. 사이트 간 Azure 가상 네트워크의 일부인 Azure HDInsight에서 Spark 클러스터를 만듭니다.

3. 클러스터 헤드 노드 및 데스크톱 간의 연결을 확인합니다.

4. IntelliJ IDEA에서 Scala 응용 프로그램을 만들고 원격 디버깅을 위해 구성합니다.

5. 응용 프로그램을 실행하고 디버그합니다.

##필수 조건

* Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
 
* Oracle Java Development 키트. [여기](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)에서 설치할 수 있습니다.
 
* IntelliJ IDEA. 이 문서에서는 버전 15.0.1을 사용합니다. [여기](https://www.jetbrains.com/idea/download/)에서 설치할 수 있습니다.
 
* IntelliJ용 Azure 도구 키트의 HDInsight 도구. IntelliJ용 HDInsight 도구는 IntelliJ용 Azure 도구 키트에 포함되어 제공됩니다. Azure 도구 키트를 설치하는 방법에 대한 지침은 [IntelliJ용 Azure 도구 키트 설치](../azure-toolkit-for-intellij-installation.md)를 참조하세요.

* IntelliJ IDEA에서 Azure 구독에 로그인. [여기](hdinsight-apache-spark-intellij-tool-plugin.md#log-into-your-azure-subscription)에 나와 있는 지침을 따르세요.
 
* Windows 컴퓨터에서 원격 디버깅을 위해 Spark Scala 응용 프로그램을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에 설명된 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없는 경우 발생합니다. 이 오류를 해결하려면 [여기에서 실행 파일을 다운로드](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\\WinUtils\\bin** 등의 위치에 저장해야 합니다. 그런 다음 환경 변수 **HADOOP\_HOME**을 추가하고 변수 값을 **C\\WinUtils**로 설정합니다.

## 1단계: Azure 가상 네트워크 만들기

아래 링크의 지침을 따라 Azure 가상 네트워크를 만든 다음 데스크톱 및 Azure 가상 네트워크 간의 연결을 확인합니다.

* [Azure 포털을 사용하여 사이트 간 VPN 연결로 VNet 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [PowerShell을 사용하여 사이트 간 VPN 연결로 VNet 만들기](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [PowerShell을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## 2단계: HDInsight Spark 클러스터 만들기

또한 만든 Azure 가상 네트워크의 일부인 Azure HDInsight에서 Apache Spark 클러스터를 만들어야 합니다. [HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)에서 제공되는 정보를 사용합니다. 선택적 구성의 일부로 이전 단계에서 만든 Azure 가상 네트워크를 선택합니다.

## 3단계: 클러스터 헤드 노드 및 데스크톱 간의 연결 확인

1. 헤드 노드의 IP 주소를 가져옵니다. 클러스터에 대한 Ambari UI를 엽니다. 클러스터 블레이드에서 **대시보드**를 클릭합니다.

	![헤드 노드 IP 찾기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)

2. Ambari UI의 오른쪽 위 모서리에서 **호스트**를 클릭합니다.

	![헤드 노드 IP 찾기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)

3. 헤드 노드, 작업자 노드 및 zookeeper 노드 목록이 표시됩니다. 헤드 노드는 **hn*** 접두사를 가집니다. 첫 번째 헤드 노드를 클릭합니다.

	![헤드 노드 IP 찾기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)

4. 열리는 페이지의 아래쪽에 **요약** 상자에서 헤드 노드의 IP 주소 및 호스트 이름을 복사합니다.

	![헤드 노드 IP 찾기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)

5. 헤드 노드의 IP 주소 및 호스트 이름을 Spark 작업을 실행하고 원격으로 디버그하려는 컴퓨터의 **호스트** 파일에 포함합니다. 이렇게 하면 IP 주소뿐만 아니라 호스트 이름을 사용하여 헤드 노드와 통신할 수 있습니다.

	1. 관리자 권한으로 메모장을 엽니다. 파일 메뉴에서 **열기**를 클릭한 다음 호스트 파일의 위치로 이동합니다. Windows 컴퓨터에서 `C:\Windows\System32\Drivers\etc\hosts`입니다.

	2. **호스트** 파일에 다음을 추가합니다.

			# For headnode0
			192.xxx.xx.xx hn0-nitinp
			192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

			# For headnode1
			192.xxx.xx.xx hn1-nitinp
			192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net


5. HDInsight 클러스터에서 사용되는 Azure 가상 네트워크에 연결된 컴퓨터에서 IP 주소뿐만 아니라 호스트 이름을 사용하여 두 헤드 노드에 ping을 실행할 수 있는지 확인합니다.

6. [SSH를 사용하여 HDInsight 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)의 지침을 사용하여 클러스터 헤드 노드에 SSH합니다. 클러스터 헤드 노드에서 데스크톱 컴퓨터의 IP 주소에 ping을 실행합니다. 컴퓨터에 할당된 두 IP 주소에 대한 연결을 테스트해야 합니다. 하나는 네트워크 연결이고 다른 하나는 컴퓨터가 연결된 Azure 가상 네트워크에 대한 것입니다.

7. 다른 헤드 노드에서도 해당 단계를 반복합니다.

## 4단계: IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 및 원격 디버깅이 가능하도록 구성

1. IntelliJ IDEA를 시작하고 새 프로젝트를 만듭니다. 새 프로젝트 대화 상자에서 다음과 같이 선택하고 **다음**을 클릭합니다.

	![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 왼쪽 창에서 **HDInsight**를 선택합니다.
	* 오른쪽 창에서 **HDInsight의 Spark(Scala)**를 선택합니다.
	* **다음**을 클릭합니다.

2. 다음 창에서 프로젝트 세부 정보를 제공합니다.

	* 프로젝트 이름과 프로젝트 위치를 제공합니다.
	* **프로젝트 SDK**의 경우 7보다 높은 Java 버전을 제공해야 합니다.
	* **Scala SDK**의 경우 **만들기**를 클릭하고 **다운로드**를 클릭한 다음 사용할 Scala의 버전을 선택합니다. **2.11.x 버전은 사용하지 마세요**. 이 샘플에서는 버전 **2.10.6**을 사용합니다.

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-version.png)

	* **Spark SDK**의 경우 [여기](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)에서 SDK를 다운로드하여 사용합니다. 이 메시지를 무시하고 [Spark Maven 리포지토리](http://mvnrepository.com/search?q=spark)를 대신 사용할 수도 있으나 Spark 응용 프로그램을 개발하기 위한 올바른 Maven 리포지토리가 설치되어 있어야 합니다. (예를 들어 Spark 스트리밍을 사용하는 경우 Spark 스트리밍 부분이 설치되어 있는지 확인해야 합니다. 또한 Scala 2.10으로 표시된 리포지토리를 사용하고 있는지 확인해야 합니다. Scala 2.11로 표시된 리포지토리는 사용하지 마세요.)

		![Spark Scala 응용 프로그램 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)

	* **마침**을 클릭합니다.

3. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 아티팩트를 확인하려면 이 단계를 따릅니다.

	1. **파일** 메뉴에서 **프로젝트 구조**를 클릭합니다.
	2. **프로젝트 구조** 대화 상자에서 **아티팩트**를 클릭하여 만들어지는 기본 아티팩트를 봅니다.

		![JAR 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)

	위의 그림에서 강조 표시된 **+** 아이콘을 클릭하여 사용자 고유의 아티팩트를 만들 수도 있습니다.

4. **프로젝트 구조** 대화 상자에서 **프로젝트**를 클릭합니다. **프로젝트 SDK**가 1.8로 설정되어 있다면 **프로젝트 언어 수준**이 **7 - 다이아몬드, ARM, 다중 캐치 등**으로 설정되어 있는지 확인합니다.

	![프로젝트 언어 수준 설정](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/set-project-language-level.png)

4. 프로젝트에 라이브러리를 추가합니다. 라이브러리를 추가하려면 프로젝트 트리에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **모듈 설정 열기**를 클릭합니다. **프로젝트 구조** 대화 상자의 왼쪽 창에서 **라이브러리**, (+) 기호를 클릭한 다음 **Maven에서**를 클릭합니다.

	![라이브러리 추가](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

	**Maven 리포지토리에서 라이브러리 다운로드** 대화 상자에서 다음 라이브러리를 검색하고 추가합니다.

	* `org.scalatest:scalatest_2.10:2.2.1`
	* `org.apache.hadoop:hadoop-azure:2.7.1`

5. 클러스터 헤드 노드에서 `yarn-site.xml` 및 `core-site.xml`을(를) 복사하고 프로젝트에 추가합니다. 다음 명령을 사용하여 파일을 복사합니다. [Cygwin](https://cygwin.com/install.html)을 사용하여 클러스터 헤드 노드에서 파일을 복사하도록 다음 `scp` 명령을 실행할 수 있습니다.

		scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

	데스크톱의 호스트 파일에 대한 클러스터 헤드 노드 IP 주소 및 호스트 이름을 이미 추가했으므로 다음과 같이 **scp** 명령을 사용할 수 있습니다.

		scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
		scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

	프로젝트 트리의 **/src** 아래에 복사하여 이러한 파일을 프로젝트에 추가합니다(예: `<your project directory>\src`).

6. `core-site.xml`을(를) 업데이트하여 다음과 같이 변경합니다.

	1. `core-site.xml`은(는) 클러스터와 연결된 저장소 계정에 암호화된 키를 포함합니다. 프로젝트에 추가한 `core-site.xml`에서 암호화된 키를 기본 저장소 계정과 연결된 실제 저장소 키로 대체합니다. [저장소 액세스 키 관리](../storage/storage-create-storage-account.md#manage-your-storage-account)를 참조하세요.

			<property>
	      		<name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
	      		<value>access-key-associated-with-the-account</value>
	    	</property>

	2. `core-site.xml`에서 다음 항목을 제거합니다.

			<property>
      			<name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
      			<value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
			</property>

			<property>
		      	<name>fs.azure.shellkeyprovider.script</name>
		      	<value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
		    </property>

			<property>
      			<name>net.topology.script.file.name</name>
      			<value>/etc/hadoop/conf/topology_script.py</value>
    		</property>

	3. 파일을 저장합니다.

7. 응용 프로그램에 대한 기본 클래스를 추가합니다. **프로젝트 탐색기**에서 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala class(Scala 클래스)**를 클릭합니다.

	![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

8. **Create New Scala Class(새 Scala 클래스 만들기)** 대화 상자에서 이름을 제공하고 **종류**에 대해 **개체**를 선택하고 **확인**을 클릭합니다.

	![소스 코드 추가](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

9. `MyClusterAppMain.scala` 파일에서 다음 코드를 붙여 넣습니다. 이 코드는 Spark 컨텍스트를 만들고 `SparkSample` 개체에서 `executeJob` 메서드를 실행합니다.


		import org.apache.spark.{SparkConf, SparkContext}

		object SparkSampleMain {
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		                           "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		                           "wasbs:///HVACOut")
		  }
		}

10. 위의 8 및 9단계를 반복하여 `SparkSample`(이)라는 새 Scala 개체를 추가합니다. 이 클래스에 다음 코드를 추가합니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 **/HVACOut**에 씁니다.

		import org.apache.spark.SparkContext
	
		object SparkSample {
		  def executeJob (sc: SparkContext, input: String, output: String): Unit = {
		    val rdd = sc.textFile(input)
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
		    println(s)
		
		    rdd1.saveAsTextFile(output)
		    //rdd1.collect().foreach(println)
		  }
		
		}

11. 위의 8 및 9단계를 반복하여 `RemoteClusterDebugging`(이)라는 새 클래스를 추가합니다. 이 클래스는 응용 프로그램 디버깅에 사용되는 Spark 테스트 프레임워크를 구현합니다. `RemoteClusterDebugging` 클래스에 다음 코드를 추가합니다.

		import org.apache.spark.{SparkConf, SparkContext}
		import org.scalatest.FunSuite
		
		class RemoteClusterDebugging extends FunSuite {
		
		  test("Remote run") {
		    val conf = new SparkConf().setAppName("SparkSample")
		                              .setMaster("yarn-client")
		                              .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
		                              .set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
		                              .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
		                              .set("spark.hadoop.validateOutputSpecs", "false")
		    val sc = new SparkContext(conf)
		
		    SparkSample.executeJob(sc,
		      "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
		      "wasbs:///HVACOut")
		  }
		}

	유의해야 할 몇 가지 중요한 사항은 다음과 같습니다.
	
	* `.set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`의 경우 Spark 어셈블리 JAR를 지정된 경로의 클러스터 저장소에서 사용할 수 있는지 확인합니다.
	* `setJars`의 경우 아티팩트 jar를 만들 위치를 지정합니다. 일반적으로 `<Your IntelliJ project directory>\out<project name>_DefaultArtifact\default_artifact.jar`입니다.


11. `RemoteClusterDebugging` 클래스에서 마우스 오른쪽 단추로 `test` 키워드를 클릭하고 **RemoteClusterDebugging 구성 만들기**를 선택합니다.

	![원격 구성 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

12. 대화 상자에서 구성에 대한 이름을 제공하고 **테스트 이름**으로 **테스트 종류**를 선택합니다. 기본적으로 다른 모든 값을 그대로 두고 **적용**을 클릭한 다음 **확인**을 클릭합니다.

	![원격 구성 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)



13. 이제 메뉴 모음에서 **원격 실행** 구성 드롭다운이 표시됩니다.

	![원격 구성 만들기](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## 5단계: 디버그 모드에서 응용 프로그램 실행

1. IntelliJ IDEA 프로젝트에서 `SparkSample.scala`을(를) 열고 'val rdd1' 옆에 중단점을 만듭니다. 중단점을 만들기 위한 팝업 메뉴에서 **함수 executeJob에서 줄**을 선택합니다.

	![중단점 추가](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)

2. **원격 실행** 구성 드롭다운 옆의 **디버그 실행** 단추를 클릭하여 응용 프로그램 실행을 시작합니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)

3. 프로그램 실행이 중단점에 도달하면 아래 창에 **디버거** 탭이 표시됩니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)

4. (**+**) 아이콘을 클릭하여 아래 이미지에 표시된 것처럼 시계를 추가합니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

	여기에서 변수 `rdd1`이(가) 만들어지기 전에 응용 프로그램이 중단되기 때문에 이 시계를 사용하여 변수 `rdd`에서 첫 번째 5행이 무엇인지 볼 수 있습니다. **ENTER** 키를 누릅니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

	위의 이미지에서 보는 것은 런타임 시이며 테라바이트의 데이터를 쿼리하고 응용 프로그램이 진행되는 방법을 디버그할 수 있습니다. 예를 들어 위의 이미지에 표시된 출력에서 출력의 첫 번째 행이 헤더임을 볼 수 있습니다. 이를 기반으로 필요한 경우 응용 프로그램 코드를 헤더 행을 건너뛰도록 수정할 수 있습니다.

5. 이제 **프로그램 다시 시작** 아이콘을 클릭하여 응용 프로그램 실행을 진행할 수 있습니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)

6. 응용 프로그램이 성공적으로 완료되면 다음과 유사한 출력이 표시됩니다.

	![디버그 모드에서 프로그램 실행](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

 

## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)

* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### 도구 및 확장

* [IntelliJ용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Eclipse용 Azure 도구 키트의 HDInsight 도구를 사용하여 Spark 응용 프로그램 만들기](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->