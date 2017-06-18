<properties
	pageTitle="Linux 기반 HDInsight(Hadoop)의 고가용성 기능 | Microsoft Azure"
	description="Linux 기반 HDInsight 클러스터에서 추가 헤드 노드를 사용하여 안정성과 가용성을 높이는 방법을 알아봅니다. 이것이 Ambari 및 Hive 같은 Hadoop 서비스에 어떻게 영향을 미치는지 알아보고, SSH를 사용하여 각 헤드 노드를 개별적으로 연결하는 방법을 알아봅니다."
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
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="larryfr"/>

#HDInsight에서 Hadoop 클러스터의 가용성 및 안정성

Hadoop은 클러스터의 노드에서 서비스와 데이터의 중복 복사본을 배포하여 가용성 및 안정성을 높입니다. 그러나 Hadoop의 표준 배포에는 일반적으로 단일 헤드 노드만 있습니다. 단일 헤드 노드의 가동 중단으로 인해 클러스터의 작동이 중지될 수 있습니다.

이 잠재적 문제를 해결하기 위해 Azure의 Linux 기반 HDInsight 클러스터는 두 개의 헤드 노드를 제공하여 실행 중인 Hadoop 서비스와 작업의 안정성 및 가용성을 높입니다.

> [AZURE.NOTE] 이 문서에 사용된 단계는 Linux 기반 HDInsight 클러스터에 한정됩니다. Windows 기반 클러스터를 사용하는 경우 Windows 관련 정보는 [HDInsight에서 Windows 기반 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability.md)을 참조하세요.

##노드 이해

HDInsight 클러스터에 있는 노드는 Azure 가상 컴퓨터를 사용하여 구현됩니다. 노드가 실패하는 경우 오프라인 상태가 되고 실패한 노드를 대체하는 새 노드가 생성됩니다. 노드가 오프라인 상태인 동안 새 노드가 온라인 상태가 될 때까지 동일한 형식의 다른 노드를 사용합니다.

> [AZURE.NOTE] 노드가 실패한 경우 데이터를 분석하고 있었다면 작업의 진행 상태도 손실됩니다. 실패한 노드가 작업 중이던 작업을 다른 노드로 다시 전송합니다.

다음 섹션에서는 HDInsight와 함께 사용되는 개별 노드 유형을 설명합니다. 일부 노드 유형은 클러스터 유형에 사용되지 않습니다. 예를 들어, Hadoop 클러스터 유형에는 Nimbus 노드가 없습니다. HDInsight 클러스터 유형에서 사용하는 노드에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)의 클러스터 유형 섹션을 참조하세요.

###헤드 노드

일부 Hadoop 구현에서는 작업자 노드의 오류를 원활하게 관리하는 서비스 및 구성 요소를 호스트하는 단일 헤드 노드가 사용됩니다. 그러나 헤드 노드에서 실행 중인 마스터 서비스가 중단되면 클러스터 작동이 중단됩니다.

HDInsight 클러스터는 주 노드에서 오류가 발생할 경우 마스터 서비스 및 구성 요소가 보조 노드에서 계속 실행될 수 있도록 보조 헤드 노드를 제공합니다.

> [AZURE.IMPORTANT] 두 헤드 노드는 동시에 클러스터 내에서 활성화되어 실행됩니다. 일부 서비스(예: HDFS 또는 YARN)는 항상 헤드 노드 하나에서만 '활성화'됩니다(다른 헤드 노드에서는 '대기'). 다른 서비스(예: HiveServer2 또는 Hive MetaStore)는 두 헤드 노드에서 동시에 활성화됩니다.

헤드 노드(및 HDInsight의 다른 노드)에는 노드 호스트 이름의 일부인 숫자 값이 있습니다. 예를 들면 `hn0-CLUSTERNAME` 또는 `hn4-CLUSTERNAME`과 같습니다.

> [AZURE.IMPORTANT] 기본 노드 또는 보조 노드인지를 숫자 값과 연결하지 않습니다. 숫자 값은 각 노드에 고유한 이름을 제공하기 위해 표시됩니다.

###Nimbus 노드

Storm 클러스터의 경우 Nimbus 노드는 작업자 노드에 대한 프로세싱을 배포하고 모니터링하여 Hadoop JobTracker에 유사한 기능을 제공합니다. HDInsight은 Storm 클러스터 유형에 대해 2개의 Nimbus 노드를 제공합니다.

###Zookeeper 노드

[ZooKeeper](http://zookeeper.apache.org/) 노드(ZK)는 헤드 노드에서 마스터 서비스의 리더 선택에 사용되고 서비스, 데이터(작업자) 노드 및 게이트웨이에서 마스터 서비스가 활성화된 헤드 노드를 알도록 하는 데 사용됩니다. 기본적으로 HDInsight는 3개의 ZooKeeper 노드를 제공합니다.

###작업자 노드

작업자 노드는 클러스터에 작업이 제출될 경우 실제 데이터 분석을 수행합니다. 작업자 노드가 실패하면 수행되던 작업이 다른 작업자 노드로 전송됩니다. 기본적으로 HDInsight는 4개의 작업자 노드를 만들지만 필요에 따라 클러스터를 만드는 동안 및 후에 해당 번호를 변경할 수 있습니다.

###에지 노드

에지 노드는 클러스터 내에서 데이터 분석에 적극적으로 참여하지 않지만 Hadoop로 작업하는 경우 개발자 또는 데이터 과학자가 대신 사용합니다. 에지 노드는 클러스터의 다른 노드와 동일한 Azure 가상 네트워크에 있으며 다른 모든 노드에 직접 액세스할 수 있습니다. 클러스터에 대한 데이터 분석에 포함되지 않기 때문에 중요한 Hadoop 서비스 또는 분석 작업에서 리소스를 정리할 걱정 없이 사용할 수 있습니다.

현재 HDInsight의 R 서버는 기본적으로 에지 노드를 제공하는 유일한 클러스터 유형입니다. HDInsight의 R 서버의 경우 에지 노드는 분산된 처리를 위해 클러스터에 제출하기 전에 노드에서 로컬로 R 코드를 테스트하는 데 사용됩니다.

[에지 노드에서 Hue로 Linux 기반 HDInsight 클러스터 만들기](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/)는 에지 노드가 있는 Hadoop 클러스터 유형을 만드는 데 사용될 수 있는 예제 템플릿입니다.


## 노드 액세스

인터넷을 통한 클러스터에 대한 액세스는 공용 게이트웨이를 통해 제공되며 헤드 노드 및 (HDInsight 클러스터의 R 서버인 경우)에지 노드에 연결되는 데 제한됩니다. 헤드 노드에서 실행되는 서비스에 대한 액세스는 공용 게이트웨이 경로가 요청된 서비스를 호스팅하는 헤드 노드에 요청한 대로 여러 헤드 노드의 존재에 영향을 받지 않습니다. 예를 들어 현재 Ambari가 보조 헤드 노드에서 호스팅되는 경우 게이트웨이는 Ambari에 들어오는 요청을 해당 노드에 라우팅합니다.

SSH를 사용하여 클러스터에 액세스하고 포트 22(SSH의 기본값)를 통해 연결할 경우 기본 헤드 노드에 연결되고 포트 23을 통해 연결할 경우 보조 헤드 노드에 연결됩니다. 예를 들어 `ssh username@mycluster-ssh.azurehdinsight.net`은 __mycluster__라는 클러스터의 기본 헤드 노드에 연결합니다.

> [AZURE.NOTE] SFTP(SSH 파일 전송 프로토콜)와 같은 SSH에 기반한 프로토콜에도 적용됩니다.

HDInsight 클러스터의 R Server와 함께 제공되는 에지 노드도 포트 22를 통해 SSH를 사용하여 직접 액세스될 수 있습니다. 예를 들어 `ssh username@RServer.mycluster.ssh.azurehdinsight.net`은 __mycluster__라는 HDInsight 클러스터의 R 서버에 대한 에지 노드에 연결합니다.

### 내부 FQDN(정규화된 도메인 이름)

HDInsight 클러스터의 노드는 클러스터에서만 액세스할 수 있는 내부 IP 주소와 FQDN을 가집니다(예: 헤드 노드에 대한 SSH 세션 또는 클러스터에서 실행 중인 작업). 내부 FQDN 또는 IP 주소를 사용하여 클러스터의 서비스에 액세스할 경우, Ambari를 사용하여 서비스 액세스 시 사용할 IP 또는 FQDN을 확인해야 합니다.

예를 들어 Oozie 서비스는 헤드 노드에서만 실행될 수 있으며, SSH 세션에서 `oozie` 명령을 사용하려면 서비스에 대한 URL이 필요합니다. 다음 명령을 사용하여 Ambari에서 URL을 검색할 수 있습니다.

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

그러면 다음과 유사한 값이 반환되며, 이 값은 `oozie` 명령에 사용할 내부 URL을 포함합니다.

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### 다른 노드 유형에 액세스

다음과 같은 방법을 사용하여 인터넷을 통해 직접 액세스할 수 없는 노드에 연결할 수 있습니다.

* __SSH__: SSH를 사용하여 헤드 노드에 연결되면 SSH를 사용하여 헤드 노드에서 클러스터의 다른 노드에 연결할 수 있습니다.
* __SSH 터널__: 인터넷에 노출되지 않는 노드 중에서 호스팅된 웹 서비스에 액세스해야 하는 경우 [SSH 터널을 사용](hdinsight-linux-ambari-ssh-tunnel.md)해야 합니다.
* __Azure 가상 네트워크__: HDInsight 클러스터가 Azure 가상 네트워크의 일부인 경우 동일한 가상 네트워크의 리소스는 클러스터의 모든 노드에 직접 액세스할 수 있습니다.

## 서비스 상태를 확인하는 방법

Ambari 웹 UI 또는 Ambari REST API를 사용하여, 헤드 노드에서 실행되는 서비스의 상태를 확인할 수 있습니다.

###Ambari 웹 UI

Ambari 웹 UI는 https://CLUSTERNAME.azurehdinsight.net에서 볼 수 있습니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 클러스터의 HTTP 사용자 자격 증명을 입력합니다. 기본 HTTP 사용자 이름은 **admin**이고 암호는 클러스터를 만들 때 입력한 암호입니다.

Ambari 페이지로 이동하면 설치된 서비스가 페이지 왼쪽에 나열됩니다.

![설치된 서비스](./media/hdinsight-high-availability-linux/services.png)

서비스 옆에 상태를 나타내는 여러 아이콘이 표시될 수 있습니다. 서비스와 관련된 모든 경고는 페이지 위쪽의 **Alert(경고)** 링크를 사용하여 볼 수 있습니다. 각 서비스를 선택하여 해당 서비스에 대한 자세한 내용을 볼 수 있습니다.

서비스 페이지에서는 각 서비스의 상태 및 구성에 대한 정보를 제공하지만 서비스가 실행되고 있는 헤드 노드에 대한 정보는 제공하지 않습니다. 이 정보를 보려면 페이지 위쪽의 **Hosts(호스트)** 링크를 사용하세요. 그러면 헤드 노드를 비롯한 클러스터 내의 호스트가 표시됩니다.

![호스트 목록](./media/hdinsight-high-availability-linux/hosts.png)

헤드 노드 중 하나의 링크를 선택하면 해당 노드에서 실행되고 있는 서비스 및 구성 요소가 표시됩니다.

![구성 요소 상태](./media/hdinsight-high-availability-linux/nodeservices.png)

###Ambari REST API

Ambari REST API는 인터넷을 통해 사용할 수 있고 공용 게이트웨이는 REST API를 현재 호스팅하는 헤드 노드에 라우팅 요청을 처리합니다.

다음 명령을 사용하여 Ambari REST API를 통해 서비스의 상태를 확인할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* **PASSWORD**를 HTTP 사용자(관리자) 계정 암호로 바꿉니다.

* **CLUSTERNAME**을 클러스터 이름으로 바꿉니다.

* **SERVICENAME**을 상태를 확인할 서비스의 이름으로 바꿉니다.

예를 들어 **password** 암호를 사용하여 **mycluster**라는 클러스터의 **HDFS** 서비스 상태를 확인하려면 다음을 사용합니다.

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

응답은 다음과 유사합니다.

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

URL은 현재 서비스가 __hn0-CLUSTERNAME__라는 헤드 노드에서 실행되고 있음을 알려줍니다.

상태는 현재 서비스가 실행되고 있음(**STARTED**)을 알려줍니다.

클러스터에 설치된 서비스를 모르는 경우 다음을 사용하여 목록을 검색할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####서비스 구성 요소

서비스는 개별적으로 상태를 확인하려는 구성 요소를 포함할 수 있습니다. 예를 들어 HDFS는 NameNode 구성 요소를 포함합니다. 구성 요소에 대한 정보를 보려면 명령은 다음과 같습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

서비스에서 제공하는 구성 요소를 모르는 경우 다음을 사용하여 목록을 검색할 수 있습니다.

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## 헤드 노드의 로그 파일에 액세스하는 방법

###SSH

SSH를 통해 헤드 노드에 연결된 동안에는 **/var/log**에서 로그 파일을 찾을 수 있습니다. 예를 들어 **/var/log/hadoop-yarn/yarn**은 YARN에 대한 로그를 포함합니다.

각 헤드 노드는 고유한 로그 항목을 포함할 수 있으므로 두 가지 다에서 로그를 확인해야 합니다.

###SFTP

또한 SSH 파일 전송 프로토콜 또는 SFTP(보안 파일 전송 프로토콜)을 사용하여 헤드 노드에 연결하고 로그 파일을 직접 다운로드할 수 있습니다.

클러스터의 SSH 사용자 계정 이름 및 SSH 주소를 제공해야 하는 클러스터에 연결하는 경우 SSH 클라이언트를 사용하는 것과 비슷합니다. 예: `sftp username@mycluster-ssh.azurehdinsight.net`. 또한 대화 상자가 나타나면 계정에 대한 암호를 제공하거나 `-i` 매개 변수를 사용하여 공개 키를 제공해야 합니다.

연결되면 `sftp>` 프롬프트가 나타납니다. 이 프롬프트에서 디렉터리를 변경하고 파일을 업로드 및 다운로드할 수 있습니다. 예를 들어 다음 명령은 디렉터리를 **/var/log/hadoop/hdfs** 디렉터리고 변경하고 디렉터리에 있는 모든 파일을 다운로드합니다.

    cd /var/log/hadoop/hdfs
    get *

사용 가능한 명령 목록의 경우 `sftp>` 프롬프트에 `help`을 입력합니다.

> [AZURE.NOTE] 또한 SFTP를 사용하여 연결하는 경우 파일 시스템을 시각화할 수 있는 그래픽 인터페이스가 있습니다. 예를 들어 [MobaXTerm](http://mobaxterm.mobatek.net/)을 사용하면 Windows 탐색기와 비슷한 인터페이스를 사용하는 파일 시스템을 찾아볼 수 있습니다.


###Ambari

> [AZURE.NOTE] Ambari를 통해 로그 파일에 액세스하려면 SSH 터널이 필요합니다. 개별 서비스의 웹 사이트는 인터넷에서 공개적으로 노출되지 않기 때문입니다. SSH 터널의 사용에 대한 정보는 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie, 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

Ambari 웹 UI에서 로그를 보려는 서비스(예: YARN)를 선택한 다음 **빠른 연결**을 사용하여 로그를 볼 헤드 노드를 선택합니다.

![빠른 연결을 사용하여 로그 보기](./media/hdinsight-high-availability-linux/viewlogs.png)

## 노드 크기를 구성하는 방법 ##

노드의 크기는 클러스터를 만들 동안에만 선택할 수 있습니다. [HDInsight 가격 책정 페이지](https://azure.microsoft.com/pricing/details/hdinsight/)에서 HDInsight에 사용할 수 있는 다양한 VM 크기(각각의 코어, 메모리 및 로컬 저장소 포함)의 목록을 찾을 수 있습니다.

새 클러스터를 만들 때 노드 크기를 지정할 수 있습니다. 다음은 [Azure 포털][preview-portal], [Azure PowerShell][azure-powershell] 및 [Azure CLI][azure-cli]를 사용하여 크기를 지정하는 방법에 대한 정보를 제공합니다.

* **Azure 포털**: 새 클러스터를 만들 때 헤드의 크기(가격 책정 계층), 작업자 및 (클러스터 유형에서 사용하는 경우)클러스터에 대한 ZooKeeper 노드를 설정하는 옵션이 제공됩니다.

	![노드 크기 선택이 포함된 클러스터 만들기 마법사의 이미지](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**: `azure hdinsight cluster create` 명령을 사용하는 경우 `--headNodeSize`, `--workerNodeSize` 및 `--zookeeperNodeSize` 매개 변수를 사용하여 헤드 노드의 크기, 작업자 및 ZooKeeper 노드를 설정할 수 있습니다.

* **Azure PowerShell**: `New-AzureRmHDInsightCluster` cmdlet을 사용하는 경우 `-HeadNodeVMSize`, `-WorkerNodeSize` 및 `-ZookeeperNodeSize` 매개 변수를 사용하여 헤드 노드의 크기, 작업자 및 ZooKeeper 노드를 설정할 수 있습니다.

##다음 단계

이 문서에서는 Azure HDInsight가 Hadoop에 고가용성을 제공하는 방법에 대해 알아보았습니다. 이 문서에 언급된 항목에 대한 자세한 내용을 보려면 다음을 사용하세요.

- [Ambari REST 참조](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Azure CLI 설치 및 구성](../xplat-cli-install.md)

- [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)

- [Ambari를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)

- [Linux 기반 HDInsight 클러스터 프로비전을](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0921_2016-->