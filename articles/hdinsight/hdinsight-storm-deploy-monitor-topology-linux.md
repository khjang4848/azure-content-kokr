<properties
   pageTitle="Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리 | Microsoft Azure"
   description="Linux 기반 HDInsight에서 Storm 대시보드를 사용하여 Apache Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 배웁니다. Visual Studio용 Hadoop 도구를 사용합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리

이 문서에서는 HDInsight 클러스터의 Linux 기반 Storm에서 실행되는 Storm 토폴로지의 모니터링 및 관리하는 기본 사항을 알아봅니다.

> [AZURE.IMPORTANT] 이 문서의 단계에는 HDInsight 클러스터의 Linux 기반 Storm이 필요합니다. Windows 기반 HDInsight에서 토폴로지의 배포 및 모니터링에 대한 정보는 [Windows 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology.md)를 참조하세요.

## 필수 조건

* **HDInsight 클러스터의 Linux 기반 Storm**: 클러스터를 만드는 단계는 [HDInsight에서 Apache Storm 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)을 참조하세요.

* **SSH 및 SCP의 기본적인 지식**: HDInsight에서 SSH와 SCP를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

    * **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

    * **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

* **SCP 클라이언트**: 모든 Linux, Unix 및 OS X 체제로 제공됩니다. Windows 클라이언트의 경우 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 사용할 수 있는 PSCP를 사용하는 것이 좋습니다.

## Storm 토폴로지 시작

### SSH 및 Storm 명령 사용

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME**을 HDInsight 클러스터 이름으로 바꿉니다.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    SSH를 사용하여 HDInsight 클러스터로 연결하는 방법에 대한 자세한 내용은 다음 문서를 참조합니다.
    
    * **Linux, Unix 또는 OS X 클라이언트**: [Linux, OS X 또는 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
    
    * **Windows 클라이언트**: [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

2. 다음 명령을 사용하여 예제 토폴로지를 시작합니다.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    클러스터에서 예제 WordCount 토폴로지를 시작합니다. 임의로 문장을 생성하고 문장에서 각 단어의 발생 횟수를 계산합니다.

    > [AZURE.NOTE] 클러스터에 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 Jar 파일을 포함하는 클러스터를 복사해야 합니다. 파일이 있는 클라이언트에서 `scp` 명령을 사용하여 수행할 수 있습니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`과 같습니다.
    >
    > WordCount 예제 및 다른 Storm 스타터 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에서 클러스터에 이미 포함되어 있습니다.

### 프로그래밍 방식

클러스터에서 호스트되는 Nimbus 서비스와 통신하여 HDInsight에서 Storm에 대한 토폴로지를 프로그래밍 방식으로 배포할 수 있습니다. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)는 Nimbus 서비스를 통해 토폴로지를 배포하고 시작하는 방법을 보여 주는 예제 Java 응용 프로그램을 제공합니다.

## Storm 명령을 사용한 모니터링 및 관리

`storm` 유틸리티를 사용하면 명령줄에서 실행하는 토폴로지로 작업할 수 있습니다. 다음은 일반적으로 사용하는 명령 목록입니다. 전체 명령 목록에는 `storm -h`를 사용합니다.

### 목록 토폴로지

다음 명령을 사용하여 실행하는 토폴로지를 나열합니다.

    storm list
    
이 명령은 다음과 유사한 정보를 반환합니다.

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### 비활성화 및 다시 활성화

토폴로지를 비활성화하면 작업을 중단하거나 다시 활성화할 때까지 일시 중지합니다. 다음을 사용하여 비활성화 및 다시 활성화합니다.

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### 실행 중인 토폴로지를 중단합니다.

Storm 토폴로지가 일단 시작되면 중지될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

    storm stop TOPOLOGYNAME

### 균형 재조정

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. 예를 들어 클러스터 크기를 조정하고 더 많은 메모를 추가하려면 균형을 재조정하여 실행 중인 토폴로지가 새 노드를 사용하도록 합니다.

> [AZURE.WARNING] 먼저 토폴로지의 균형을 재조정하면 토폴로지를 비활성화하고 클러스터에 작업자를 균등하게 다시 배포한 다음 마지막으로 토폴로지를 균형 재조정이 발생하기 전의 상태로 반환합니다. 따라서 토폴로지가 활성화되었으면 작업이 다시 활성화됩니다. 비활성화되어 있다면 비활성화된 상태를 유지합니다.

    storm rebalance TOPOLOGYNAME

## Storm UI를 사용한 모니터링 및 관리

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고 HDInsight 클러스터에 포함됩니다. Storm UI를 보려면 웹 브라우저를 사용하여 __https://CLUSTERNAME.azurehdinsight.net/stormui__을 엽니다. 여기서 __CLUSTERNAME\_\_은 클러스터의 이름입니다.

> [AZURE.NOTE] 사용자 이름 및 암호를 제공하도록 요청을 받으면 클러스터를 만들 때 사용한 클러스터 관리자(관리자) 및암호를 입력합니다.


### 기본 페이지

Storm UI의 기본 페이지에서는 다음 정보를 제공합니다.

* **클러스터 요약**: Storm 클러스터에 대한 기본 정보입니다.

* **토폴로지 요약**: 실행 중인 토폴로지 목록입니다. 특정 토폴로지에 대한 자세한 정보를 보려면 이 섹션의 링크를 사용하세요.

* **감독자 요약**: Storm 감독자에 대한 정보입니다.

* **Nimbus 구성**: 클러스터에 대한 Nimbus 구성입니다.

### 토폴로지 요약

**토폴로지 요약** 섹션의 링크를 선택하면 토폴로지에 대한 다음 정보가 표시됩니다.

* **토폴로지 요약**: 토폴로지에 대한 기본 정보입니다.

* **토폴로지 동작**: 토폴로지에 대해 수행할 수 있는 관리 작업입니다.

  * **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.

  * **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.

  * **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이렇게 하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가하거나 감소한 노드 수를 보충할 수 있습니다.

    자세한 내용은 <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.

  * **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

* **토폴로지 통계**: 토폴로지에 대한 통계입니다. **창** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **Spout**: 토폴로지에서 사용하는 Spout입니다. 이 섹션의 링크를 사용하면 특정 Spout에 대한 자세한 내용을 볼 수 있습니다.

* **Bolt**: 토폴로지에서 사용하는 Bolt입니다. 이 섹션의 링크를 사용하면 특정 Bolt에 대한 자세한 내용을 볼 수 있습니다.

* **토폴로지 구성**: 선택한 토폴로지의 구성입니다.

### Spout 및 Bolt 요약

**Spout** 또는 **Bolt** 섹션에서 Spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

* **구성 요소 요약**: Spout 또는 Bolt에 대한 기본 정보입니다.

* **Spout/Bolt 통계**: Spout 또는 Bolt에 대한 통계입니다. **창** 열에 있는 링크를 사용하여 페이지에서 나머지 항목에 대한 시간 프레임을 설정할 수 있습니다.

* **입력 통계**(Bolt에만 해당): Bolt에서 사용하는 입력 스트림에 대한 정보입니다.

* **출력 통계**: 이 Spout 또는 Bolt가 내보낸 스트림에 대한 정보입니다.

* **실행자**: Spout 또는 Bolt의 인스턴스에 대한 정보입니다. 특정 실행자에 대한 **Port** 항목을 선택하면 이 인스턴스에 대해 처리된 진단 정보의 로그를 볼 수 있습니다.

* **오류**: 이 Spout 또는 Bolt에 대한 오류 정보입니다.

## REST API

Storm UI는 REST API의 맨 위에 기본 제공되므로 REST API를 사용하여 유사한 관리 및 모니터링 기능을 수행할 수 있습니다. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

자세한 내용은 [Storm UI REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html)를 참조하세요. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

> [AZURE.IMPORTANT] Storm REST API는 인터넷을 통해 공개적으로 사용할 수 없고 HDInsight 클러스터 헤드 노드에 SSH 터널을 사용하여 액세스되어야 합니다. SSH 터널의 생성 및 사용에 대한 정보는 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie, 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

### 기본 URI

Linux 기반 HDInsight 클러스터에서 REST API에 대한 기본 URI는 **https://HEADNODEFQDN:8744/api/v1/**에서 사용할 수 있지만 헤드 노드의 도메인 이름은 클러스터 생성 동안 생성되며 고정되어 있지 않습니다.

다양한 방법으로 클러스터 헤드 노드의 정규화된 도메인 이름(FQDN)을 찾을 수 있습니다.

* __SSH 세션에서__: SSH 세션에서 클러스터로 `headnode -f` 명령을 사용합니다.

* __Ambari 웹에서__: 페이지 맨 위에서 __서비스__를 선택한 다음 __Storm__을 선택합니다. __요약__ 탭에서 __Storm UI 서버__를 선택합니다. Storm UI 및 REST API가 실행 중인 노드의 FQDN은 페이지 맨 위에 있습니다.

* __Ambari REST API에서__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 명령을 사용하여 Storm UI 및 REST API가 실행 중인 노드에 관한 정보를 검색합니다. __PASSWORD__를 클러스터의 관리자 암호로 바꿉니다. __CLUSTERNAME__을 클러스터 이름으로 바꿉니다. 응답에서 "host\_name" 항목에는 노드의 FQDN이 포함됩니다.

### 인증

REST API 요청에서는 **기본 인증**을 사용해야 하므로 HDInsight 클러스터 관리자 이름 및 암호를 사용합니다.

> [AZURE.NOTE] 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 **항상** HTTPS를 사용해야 합니다.

### 반환 값

REST API에서 반환되는 정보는 클러스터와 동일한 Azure 가상 네트워크에 있는 클러스터 또는 가상 컴퓨터 내에서만 사용할 수 있습니다. 예를 들어, Zookeeper 서버에 대해 반환된 FQDN(정규화된 도메인 이름)은 인터넷에서 액세스할 수 없습니다.

## 다음 단계

Storm 대시보드를 사용하여 토폴로지를 배포 및 모니터링하는 방법에 대해 배웠으므로 [Maven을 사용하여 Java 기반 토폴로지를 개발](hdinsight-storm-develop-java-topology.md)하는 방법을 알아봅니다.

추가 예제 토폴로지 목록은 [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->