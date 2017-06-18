<properties
	pageTitle="Linux 기반 HDInsight에서 Hadoop YARN 응용 프로그램 로그에 액세스 | Microsoft Azure"
	description="명령줄과 웹 브라우저를 사용하여 Linux 기반 HDInsight(Hadoop) 클러스터에서 YARN 응용 프로그램 로그에 액세스하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="Blackmist" 
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# Linux 기반 HDInsight에서 YARN 응용 프로그램 로그에 액세스 

이 문서에서는 Azure HDInsight의 Hadoop 클러스터에서 완료된 YARN(Yet Another Resource Negotiator) 응용 프로그램에 대한 로그에 액세스하는 방법에 대해 설명합니다.

> [AZURE.NOTE] 이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 클러스터에 대한 자세한 내용은 [Windows 기반 HDInsight에서 YARN 응용 프로그램 로그에 액세스](hdinsight-hadoop-access-yarn-app-logs.md)를 참조하세요.

## 필수 조건

* Linux 기반 HDInsight 클러스터입니다.

* ResourceManager 로그 웹 UI에 액세스할 수 있기 전에 [SSH 터널을 만들어야](hdinsight-linux-ambari-ssh-tunnel.md) 합니다.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html)는 두 가지 다른 인터페이스를 통해 완료된 응용 프로그램에 대한 제네릭 정보 및 프레임워크별 응용 프로그램 정보를 제공합니다. 구체적으로 살펴보면 다음과 같습니다.

* 3\.1.1.374 이상 버전에서는 HDInsight 클러스터에서 제네릭 응용 프로그램 정보를 저장하고 검색할 수 있습니다.
* Timeline Server의 프레임워크별 응용 프로그램 정보 구성 요소는 HDInsight 클러스터에서 현재 사용할 수 없습니다.

응용 프로그램에 대한 제네릭 정보에는 다음과 같은 데이터가 포함됩니다.

* 응용 프로그램의 고유한 식별자인 응용 프로그램 ID
* 응용 프로그램을 시작한 사용자
* 응용 프로그램을 완료하려고 시도한 횟수
* 지정된 응용 프로그램 시도에 사용된 컨테이너

## <a name="YARNAppsAndLogs"></a>YARN 응용 프로그램 및 로그

YARN은 여러 프로그래밍 모델(예: MapReduce)을 지원하여 리소스 관리를 응용 프로그램 예약/모니터링과 분리합니다. 이는 전역 *리소스 관리자*(RM), 작업자 노드별 *노드 관리자*(NM) 및 응용 프로그램별 *응용 프로그램 마스터*(AM)를 통해 이루어집니다. 응용 프로그램별 AM은 응용 프로그램을 실행하기 위한 리소스(CPU, 메모리, 디스크, 네트워크)를 RM과 협상합니다. RM은 NM과 협력하여 이러한 리소스를 부여하며, 이 리소스는 *컨테이너는*로 부여됩니다. AM은 RM에 의해 부여받은 컨테이너의 진행률 추적합니다. 응용 프로그램의 특성에 따라 응용 프로그램에 여러 컨테이너가 필요할 수 있습니다.

또한 각 응용 프로그램은 작동 중단이 발생하거나 AM과 RM 간의 통신 손실로 인해 응용 프로그램을 완료하기 위해 여러 *응용 프로그램 시도*로 구성될 수도 있습니다. 따라서 컨테이너는 응용 프로그램의 특정 시도에 부여됩니다. 컨테이너는 YARN 응용 프로그램에서 수행하는 기본 작업 단위에 대한 컨텍스트를 제공하고 컨테이너의 컨텍스트 내에서 수행되는 모든 작업은 컨테이너가 할당된 단일 작업자 노드에서 수행된다고 볼 수 있습니다. 자세한 내용은 [YARN 개념][YARN-concepts](영문)을 참조하세요.

응용 프로그램 로그(및 연관된 컨테이너 로그)는 문제가 있는 Hadoop 응용 프로그램을 디버깅하는 데 매우 중요합니다. YARN은 [로그 집계][log-aggregation](영문) 기능을 사용하여 응용 프로그램 로그를 수집, 집계 및 저장하기 위한 유용한 프레임워크를 제공합니다. 로그 집계 기능은 응용 프로그램이 완료된 후 작업자 노드의 모든 컨테이너에서 로그를 집계하고 이를 작업자 노드별 하나의 집계 파일로 기본 파일 시스템에 저장하므로 응용 프로그램 로그에 더 명확하게 액세스할 수 있도록 지원합니다. 응용 프로그램은 수백 수천 개의 컨테이너를 사용할 수 있지만 단일 작업자 노드에서 실행되는 모든 컨테이너에 대한 로그는 항상 단일 파일로 집계되므로, 응용 프로그램에서 작업자 노드당 하나의 로그 파일만 사용합니다. 로그 집계는 HDInsight 클러스터(버전 3.0 이상)에서 기본적으로 사용하도록 설정되며 집계된 로그는 클러스터의 기본 컨테이너인 다음 위치에 있습니다.

	wasbs:///app-logs/<user>/logs/<applicationId>

이 위치에서 *user*는 응용 프로그램을 시작한 사용자의 이름이고, *applicationId*는 YARN RM이 할당한 응용 프로그램의 고유 식별자입니다.

집계된 로그는 컨테이너별로 인덱싱된 [이진 형식][binary-format]인 [TFile][T-file]로 작성되므로 직접 읽을 수 없습니다. 관심 있는 컨테이너 또는 응용 프로그램에 대한 이러한 로그를 일반 텍스트로 보려면 YARN ResourceManager 로그 또는 CLI 도구를 사용해야 합니다.

##YARN CLI 도구

YARN CLI 도구를 사용하려면 먼저 SSH를 사용하여 HDInsight 클러스터에 연결해야 합니다. HDInsight에서 SSH를 사용하는 방법에 대한 내용은 다음 문서 중 하나를 참조하세요.

- [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)
	
이러한 로그를 다음 명령 중 하나를 실행하여 일반 텍스트로 볼 수 있습니다.

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
	
이러한 명령을 실행할 때 &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> 및 &ltworker-node-address> 정보를 지정해야 합니다.

##YARN ResourceManager UI

YARN ResourceManager UI는 클러스터 헤드 노드에서 실행하고 Ambari 웹 UI를 통해 액세스할 수 있지만 ResourceManager UI에 액세스할 수 있기 전에 먼저 [SSH 터널을 만들어야](hdinsight-linux-ambari-ssh-tunnel.md) 합니다.

SSH 터널을 만든 후 다음 단계를 사용하여 YARN 로그를 봅니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.

2. 왼쪽에 있는 서비스 목록에서 __YARN__을 선택합니다.

	![선택한 Yarn 서비스](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. __빠른 링크__ 드롭다운에서 클러스터 헤드 노드 중 하나를 선택한 다음 __ResourceManager 로그__를 선택합니다.

	![Yarn 빠른 링크](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
	
	YARN 로그에 대한 링크 목록이 나타납니다.

[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0914_2016-->