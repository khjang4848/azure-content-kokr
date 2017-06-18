<properties 
	pageTitle="HDInsight Linux에서 Spark 클러스터와 함께 Zeppelin Notebook 사용 | Azure" 
	description="HDInsight Linux에서 Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="nitinme"/>


# HDInsight Linux에서 Apache Spark 클러스터와 함께 Zeppelin Notebook 사용

Apache Spark 클러스터에 Zeppelin Notebook을 설치하는 방법 및 Zeppelin Notebook을 사용하여 Spark 작업을 실행하는 방법을 알아봅니다.

> [AZURE.IMPORTANT] HDInsight Spark 클러스터용 Zeppelin Notebook은 Azure HDInsight Spark 환경에서 Zeppelin을 사용하는 방법을 보여줍니다. HDInsight Spark와 함께 작동하도록 노트북을 사용하려는 경우에는 Jupyter 노트북을 대신 사용하는 것이 좋습니다. 또한 Jupyter 노트북은 Scala 등의 다른 커널 옵션을 제공하고 지속적으로 향상된 기능을 제공합니다. HDInsight Spark와 Jupyter 노트북을 사용하는 방법에 대한 지침은 [Jupyter 노트북을 사용하여 Spark SQL 쿼리 실행](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter)을 참조하세요.

**필수 조건:**

* 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* Apache Spark 클러스터. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.
* SSH 클라이언트. Linux 및 Unix 배포 또는 Macintosh OS X의 경우 `ssh` 명령은 운영 체제에 제공됩니다. Windows의 경우 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)가 좋습니다.

	> [AZURE.NOTE] `ssh` 또는 PuTTY 이외의 다른 SSH 클라이언트를 사용하려는 경우 SSH 터널을 설정하는 방법에 대해 클라이언트를 위한 설명서를 참조하세요.

* SOCKS 프록시를 사용하도록 구성할 수 있는 웹 브라우저

* __(선택 사항)__: 터널을 통해 특정 요청을 라우팅하는 규칙을 적용할 수 있는 [FoxyProxy](http://getfoxyproxy.org/,)와 같은 플러그인

	> [AZURE.WARNING] FoxyProxy와 같은 플러그인 없이 브라우저를 통해 만들어진 모든 요청은 터널을 통해 라우팅될 수 있습니다. 이 경우 브라우저에서 웹 페이지의 로딩 속도가 느릴 수 있습니다.

## Spark 클러스터에 Zeppelin 설치

스크립트 작업을 사용하여 Spark 클러스터에서 Zeppelin을 설치할 수 있습니다. 스크립트 작업은 사용자 지정 스크립트를 사용하여 기본적으로 사용할 수 없는 클러스터에 구성 요소를 설치합니다. 사용자 지정 스크립트를 사용하여 Azure 포털에서 HDInsight .NET SDK를 사용하거나 Azure PowerShell을 사용하여 Zeppelin을 설치할 수 있습니다. 클러스터 생성 과정의 일부로 또는 클러스터가 작동 및 실행된 이후에 이 스크립트를 사용하여 Zeppelin을 설치할 수 있습니다. 아래 섹션의 링크는 이러한 작업을 수행하는 방법에 지침을 제공합니다.

### Azure 포털 사용

Azure 포털을 사용하여 Zeppelin을 설치하는 스크립트 동작을 실행하는 방법은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal)을 참조하세요. 해당 문서의 지침을 약간 변경해야 합니다.

* Zeppelin을 설치하기 위해 스크립트를 사용해야 합니다. HDInsight의 Spark 클러스터에서 Zeppelin을 설치하는 사용자 지정 스크립트는 다음 링크에서 사용 가능합니다.
	* Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* 헤드 노드에서만 스크립트 작업을 실행해야 합니다.

* 이 스크립트는 매개 변수가 필요 없습니다.

### HDInsight .NET SDK 사용

HDInsight .NET SDK를 사용하여 Zeppelin을 설치하는 스크립트 작업을 실행하는 방법은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk)을 참조하세요. 해당 문서의 지침을 약간 변경해야 합니다.

* Zeppelin을 설치하기 위해 스크립트를 사용해야 합니다. HDInsight의 Spark 클러스터에서 Zeppelin을 설치하는 사용자 지정 스크립트는 다음 링크에서 사용 가능합니다.
	* Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
	* Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* 이 스크립트는 매개 변수가 필요 없습니다.

* Spark에 만들려는 클러스터 유형을 설정합니다.

### Azure PowerShell 사용

다음 PowerShell 조각을 사용하여 Zeppelin이 설치된 HDInsight Linux에 Spark 클러스터를 만듭니다. Spark 클러스터의 버전에 따라 해당 사용자 지정 스크립트에 대한 링크를 포함하도록 다음 PowerShell 조각을 업데이트해야 합니다.

* Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Zeppelin Notebook에 액세스하기 위한 SSH 터널링 설정

HDInsight Linux의 Spark 클러스터에서 실행되는 Zeppelin Notebook에 액세스하기 위해 SSH 터널을 사용할 것입니다. 아래는 ssh 명령줄(Linux) 및 PuTTY(Windows)를 사용하여 SSH 터널을 만드는 단계입니다.

### SSH 명령을 사용하여 터널 만들기(Linux)

`ssh` 명령을 사용하여 SSH 터널을 만들려면 다음 명령을 사용합니다. __USERNAME__을 HDInsight 클러스터에 대한 SSH 사용자로 바꾸고 __CLUSTERNAME__을 HDInsight 클러스터의 이름으로 바꿉니다.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

로컬 포트 9876에서 SSH를 통해 클러스터에 트래픽을 라우팅하는 연결을 만듭니다. 옵션은 다음과 같습니다.

* **D 9876** - 터널을 통해 로컬 포트에서 트래픽 라우팅

* **C** - 웹 트래픽은 대부분 텍스트이므로 모든 데이터 압축

* **2** - SSH가 프로토콜 버전 2만 시도하도록 강요

* **q** - 자동 모드

* **T** - 포트 전달 후 허위 tty 할당 비활성화

* **n** - 포트 전달 후 STDIN 읽지 않음

* **N** - 포트 전달 후 원격 명령 실행 안 함

* **f** - 백그라운드에서 실행

SSH 키를 사용하여 클러스터를 구성한 경우에는 `-i` 매개 변수를 사용하여 개인 SSH 키에 대한 경로를 지정할 수 있습니다.

명령이 완료되면 로컬 컴퓨터에서 9876 포트로 전송되는 트래픽이 SSL(Secure Sockets Layer)을 통해 클러스터 헤드 노드에 라우팅되며 생성된 곳에 나타납니다.

### PuTTY를 사용하여 터널 만들기(Windows)

PuTTY를 사용하여 SSH 터널을 만들려면 다음 단계를 사용합니다.

1. PuTTY를 열고 연결 정보를 입력합니다. PuTTY를 잘 알고 있지 않다면 HDInsight와 함께 사용하는 방법에 대한 정보에 대해 [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조합니다.

2. 대화 상자의 왼쪽에 있는 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장한 다음 **Tunnels**를 선택합니다.

3. **Options controlling SSH port forwarding** 양식에 다음 정보를 제공합니다.

	* **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 **9876**과 같습니다.

	* **Destination** - Linux 기반 HDInsight 클러스터의 SSH 주소입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

	* **Dynamic** - 동적 SOCKS 프록시 라우팅을 활성화합니다.

	![터널링 옵션 이미지](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. **Add**를 클릭하여 설정을 추가한 다음 **Open**을 클릭하여 SSH 연결을 엽니다.

5. 메시지가 표시되면 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

### 브라우저에서 터널 사용

> [AZURE.NOTE] 이 섹션의 단계는 Linux, Unix, Macintosh OS X 및 Windows 시스템에 자유롭게 사용할 수 있으므로 FireFox 브라우저를 사용합니다. Google Chrome, Microsoft Edge 또는 Apple Safari와 같은 다른 최신 브라우저도 제대로 작동하지만 일부 단계에 사용된 FoxyProxy 플러그인은 모든 브라우저에 대해 사용할 수 없습니다.

1. 브라우저를 구성하여 **SOCKS v5** 프록시로 **localhost:9876**을 사용합니다. Firefox 설정은 다음과 같습니다. 9876이 아닌 다른 포트를 사용한 경우 포트를 사용한 포트로 변경합니다.

	![Firefox 설정 이미지](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] **Remote DNS**를 선택하면 HDInsight 클러스터를 통해 DNS(Domain Name System) 요청이 확인됩니다. 선택하지 않은 경우 DNS가 로컬로 확인됩니다.

2. Firefox에서 프록시 설정을 사용하거나 사용하지 않도록 설정한 상태에서 [http://www.whatismyip.com/](http://www.whatismyip.com/)과 같은 사이트를 방문하여 트래픽이 터널을 통해 라우팅되는지 확인합니다. 설정이 활성화되어 있는 동안 IP 주소는 Microsoft Azure 데이터 센터에 있는 컴퓨터의 주소입니다.

### 브라우저 확장

작동하는 터널을 사용하여 브라우저를 구성하는 동안 보통 터널을 통해 모든 트래픽을 라우팅하지 않으려고 할 수 있습니다. [FoxyProxy](http://getfoxyproxy.org/)와 같은 브라우저 확장 프로그램이 URL 요청에 대해 일치하는 패턴을 지원하므로(FoxyProxy Standard 또는 Plus만 해당) 특정 URL에 대한 요청만 터널을 통해 전송됩니다.

FoxyProxy 표준을 설치한 경우 터널을 통해 HDInsight에 대한 트래픽을 전달만 하도록 구성하려면 다음 단계를 따르세요.

1. 브라우저에서 FoxyProxy 확장 프로그램을 엽니다. 예를 들어, Firefox에서 주소 필드 옆에 있는 FoxyProxy 아이콘을 선택합니다.

	![foxyproxy 아이콘](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. **Add New Proxy**를 선택하고 **General** 탭을 선택한 다음 **HDInsightProxy**의 프록시 이름을 입력합니다.

	![foxyproxy 일반](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. **Proxy Details** 탭을 선택하고 다음 필드를 입력합니다.

	* **Host or IP Address** - 로컬 컴퓨터에서 SSH 터널을 사용한 이후의 localhost입니다.

	* **Port** - SSH 터널에 사용한 포트입니다.

	* **SOCKS proxy** - 브라우저에서 터널을 프록시로 사용할 수 있도록 하려면 선택합니다.

	* **SOCKS v5** - 필요한 버전의 프록시를 설정하려면 선택합니다.

	![foxyproxy 프록시](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. **URL Patterns** 탭을 선택한 다음 **Add New Pattern**을 선택합니다. 다음을 사용하여 패턴을 정의하고 **OK**를 클릭합니다.

	* **Pattern Name** - **zeppelinnotebook** - 패턴의 이름입니다.

	* **URL 패턴** - ***hn0*** - Zeppelin Notebook이 호스팅되는 끝점의 내부 정규화된 도메인 이름과 일치하는 패턴을 정의합니다. Zeppelin Notebook은 클러스터의 headnode0에만 사용할 수 있고 끝점은 일반적으로 `http://hn0-<string>.internal.cloudapp.net`이므로, **hn0** 패턴을 사용하면 요청이 반드시 Zeppelin 끝점으로 리디렉션됩니다.

		![foxyproxy 패턴](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. **OK**를 클릭하여 프록시를 추가하고 **Proxy Settings**를 닫습니다.

5. FoxyProxy 대화 상자의 위쪽에서 **Select Mode**를 **Use proxies based on their pre-defined patterns and priorities**로 변경한 다음 **Close**를 클릭합니다.

	![foxyproxy 선택 모드](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

이러한 단계를 따른 후에는 __hn0__ 문자열이 포함된 URL에 대한 요청만 SSL 터널을 통해 라우팅됩니다.

## Zeppelin Notebook에 액세스

SSH 터널링을 설정했으면 아래 단계에 따라 Spark 클러스터의 Zeppelin Notebook에 액세스할 수 있습니다. 이 섹션에서는 %sql 및 %hive 문을 실행하는 방법을 알아봅니다.

1. 웹 브라우저에서 다음 끝점을 엽니다.

		http://hn0-myspar:9995

	* **hn0**은 headnode0을 의미합니다.
	* **myspar**는 Spark 클러스터 이름의 처음 여섯 문자입니다.
	* **9995**는 Zeppelin Notebook이 액세스할 수 있는 포트입니다.

2. 새 Notebook을 만듭니다. 헤더 창에서 **노트북**을 클릭하고 **새 메모 만들기**를 클릭합니다.

	![새 Zeppelin 노트북 만들기](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "새 Zeppelin 노트북 만들기")

	같은 페이지의 **노트북** 제목 아래에 **Note XXXXXXXXX**로 시작하는 이름의 새 노트북이 표시됩니다. 새 노트북을 클릭합니다.

3. 새 노트북에 대한 웹 페이지에서 제목을 클릭하고 원하는 경우 노트북의 이름을 변경합니다. Enter 키를 눌러 변경된 이름을 저장합니다. 또한 노트북 헤더의 오른쪽 위 모서리에 **연결됨** 상태가 표시되는지 확인합니다.

	![Zeppelin 노트북 상태](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin 노트북 상태")

### SQL 문 실행

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들면 샘플 데이터 파일인 **hvac.csv**가 **\\HdiSamples\\SensorSampleData\\hvac** 아래 연결된 저장소 계정에 복사됩니다.

	새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	**Shift + Enter**를 누르거나 단락에 대한 **재생** 단추를 클릭하여 코드 조각을 실행합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

	![원시 데이터에서 임시 테이블 만들기](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "원시 데이터에서 임시 테이블 만들기")

	또한 각 단락에 제목을 제공할 수도 있습니다. 오른쪽 모서리에서 **설정** 아이콘을 클릭하고 **제목 표시**를 클릭합니다.

5. 이제 **hvac** 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	시작 부분의 **%sql** 문은 Notebook에서 Spark SQL 인터프리터를 사용함을 알려줍니다. Notebook 헤더의 **인터프리터** 탭에서 정의된 인터프리터를 확인할 수 있습니다.

	다음 스크린샷은 출력을 보여 줍니다.

	![노트북을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "노트북을 사용하여 Spark SQL 문 실행")

	 동일한 출력에 대해 서로 다른 표현 간을 전환하려면 표시 옵션(사각형으로 강조 표시됨)을 클릭합니다. **설정**을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp\_diff**의 평균을 값으로 사용합니다.

	
6. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 쿼리할 수 있는 값이 포함된 쿼리에 변수 **Temp**를 정의하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 다음 스크린샷은 출력을 보여 줍니다.

	![노트북을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "노트북을 사용하여 Spark SQL 문 실행")

	후속 쿼리에 대해서는 드롭다운에서 새 값을 선택하고 쿼리를 다시 실행할 수 있습니다. **설정**을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp\_diff**의 평균을 값으로, **targettemp**를 그룹으로 사용합니다.

7. Spark SQL 인터프리터를 다시 시작하여 응용 프로그램을 종료합니다. 맨 위쪽에서 **인터프리터** 탭을 클릭하고 Spark 인터프리터에 대해 **다시 시작**을 클릭합니다.

	![Zeppelin 인터프리터 다시 시작](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Zeppelin 인터프리터 다시 시작")

### Hive 문 실행

1. Zeppelin Notebook에서 **Interpreter** 단추를 클릭합니다.

	![Hive 인터프리터 업데이트](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Hive 인터프리터 업데이트")

2. **hive** 인터프리터에 대해 **edit**을 클릭합니다.

	![Hive 인터프리터 업데이트](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Hive 인터프리터 업데이트")

	다음 속성을 업데이트합니다.

	* **default.password**를 HDInsight Spark 클러스터를 만드는 동안 관리자에 대해 지정한 암호로 설정합니다.
	* **default.url**을 `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`로 설정합니다. **<spark\_cluster\_name>**을 Spark 클러스터의 이름으로 바꿉니다.
	* **default.user**를 클러스터를 만들 때 지정한 관리자의 이름으로 설정합니다. 예: *admin*.

3. **Save**를 클릭하고 Hive 인터프리터를 다시 시작하라는 메시지가 표시되면 **OK**를 클릭합니다.

4. 새 Notebook을 만들고 다음 문을 실행하여 클러스터의 모든 hive 테이블을 나열합니다.

		%hive
		SHOW TABLES

	기본적으로 HDInsight 클러스터에는 **hivesampletable**이라는 샘플 테이블이 있으므로 다음과 같은 출력이 표시됩니다.

	![Hive 출력](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Hive 출력")

5. 다음 문을 실행하여 테이블의 레코드를 나열합니다.

		%hive
		SELECT * FROM hivesampletable LIMIT 5

	다음과 유사한 출력이 표시됩니다.

	![Hive 출력](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Hive 출력")

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

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0914_2016-->