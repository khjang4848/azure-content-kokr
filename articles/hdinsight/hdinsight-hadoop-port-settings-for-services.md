<properties
pageTitle="HDInsight에 사용되는 포트 | Azure"
description="HDInsight에서 실행 중인 Hadoop 서비스에 사용된 포트 목록입니다."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/13/2016"
ms.author="larryfr"/>

# HDInsight에 사용되는 포트 및 URI

이 문서에서는 Linux 기반 HDInsight 클러스터에서 실행되는 Hadoop 서비스에 사용되는 포트 목록을 제공합니다. 또한 SSH를 사용하여 클러스터에 연결하는 데 사용된 포트에 대한 정보도 제공합니다.

## 공용 포트 및 비-공용 포트

Linux 기반 HDInsight 클러스터는 인터넷에서 세 개의 포트(22, 23, 443)만 공개적으로 노출합니다. 이러한 포트는 SSH 및 보안 HTTPS 프로토콜을 통해 노출된 서비스를 사용하여 클러스터에 안전하게 액세스하는 데 사용됩니다.

내부적으로 HDInsight는 Azure 가상 네트워크에서 실행되는 여러 Azure 가상 컴퓨터(클러스터 내의 노드)에 의해 구현됩니다. 가상 네트워크 내에서 인터넷을 통해 노출되지 않은 포트를 액세스할 수 있습니다. 예를 들어 SSH를 사용하여 헤드 노드 중 하나에 연결하는 경우 헤드 노드부터 시작하여 클러스터 노드에서 실행 중인 서비스에 직접 액세스할 수 있습니다.

> [AZURE.IMPORTANT] HDInsight 클러스터를 만드는 경우 구성 옵션으로 Azure 가상 네트워크를 지정하지 않으면 클러스터가 만들어지지만 다른 컴퓨터(예: 다른 Azure 가상 컴퓨터 또는 클라이언트 개발 컴퓨터)를 이 자동으로 생성된 가상 네트워크에 조인할 수 없습니다.

추가 컴퓨터를 가상 네트워크에 조인하려면 먼저 가상 네트워크를 만든 후 HDInsight 클러스터를 만들 때 이를 지정해야 합니다. 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

## 공용 포트

HDInsight 클러스터의 모든 노드는 Azure 가상 네트워크에 있으며 인터넷에서 직접 액세스할 수 없습니다. 공용 게이트웨이는 모든 HDInsight 클러스터 유형에 대해 일반적인 다음 포트에 대한 인터넷 액세스를 제공합니다.

| 부여 | 포트 | 프로토콜 | 설명 |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | 기본 헤드 노드에서 sshd에 클라이언트를 연결합니다. [Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요. |
| sshd | 22 | SSH | 에지 노드에서 클라이언트를 sshd에 연결합니다(HDInsight 프리미엄만 해당). [HDInsight에서 R 서버 사용 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요. |
| sshd | 23 | SSH | 보조 헤드 노드에서 sshd에 클라이언트를 연결합니다. [Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요. |
| Ambari | 443 | HTTPS | Ambari 웹 UI. [Ambari 웹 UI를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요. |
| Ambari | 443 | HTTPS | Ambari REST API. [Ambari REST API를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari-rest-api.md)를 참조하세요. |
| WebHCat | 443 | HTTPS | HCatalog REST API. [Curl에서 Hive 사용](hdinsight-hadoop-use-Pig-curl.md), [Curl에서 Pig 사용](hdinsight-hadoop-use-Pig-curl.md), [Curl에서 MapReduce 사용](hdinsight-hadoop-use-mapreduce-curl.md)을 참조하세요. |
| HiveServer2 | 443 | ODBC | ODBC를 사용하여 Hive에 연결합니다. [Microsoft ODBC 드라이버로 HDInsight에 Excel 연결](hdinsight-connect-excel-hive-odbc-driver.md)을 참조하세요. |
| HiveServer2 | 443 | JDBC | JDBC를 사용하여 Hive에 연결합니다. [Hive JDBC 드라이버를 사용하여 HDInsight에서 Hive에 연결](hdinsight-connect-hive-jdbc-driver.md)을 참조하세요. |

다음은 특정 클러스터 유형에 대해 사용할 수 있습니다.

| 부여 | 포트 | 프로토콜 |클러스터 유형 | 설명 |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | HBase REST API. [HBase를 사용하여 시작](hdinsight-hbase-tutorial-get-started-linux.md)을 참조하세요. |
| Livy | 443 | HTTPS | Spark | Spark REST API. [Livy를 사용하여 원격으로 Spark 작업 제출](hdinsight-apache-spark-livy-rest-interface.md)을 참조하세요. |
| Storm | 443 | HTTPS | Storm | Storm 웹 UI. [HDInsight에서 Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요.

### 인증

인터넷에서 공개적으로 노출되는 모든 서비스를 인증해야 합니다.

| 포트 | 자격 증명 |
| ---- | ----------- |
| 22 또는 23 | 클러스터를 만드는 동안 지정된 SSH 사용자 자격 증명 |
| 443 | 클러스터를 만드는 동안 설정된 로그인 이름(기본값: admin) 및 암호 |

## 비-공용 포트

> [AZURE.NOTE] 일부 서비스는 특정 클러스터 형식에서만 사용할 수 있습니다. 예를 들어 HBase는 HBase 클러스터 형식에서만 사용할 수 있습니다.

### HDFS 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- | 
| NameNode 웹 UI | 헤드 노드 | 30070 | HTTPS | 현재 상태를 보기 위한 웹 UI |
| NameNode 메타데이터 서비스 | 헤드 노드 | 8020 | IPC | 파일 시스템 메타데이터 
| DataNode | 모든 작업자 노드 | 30075 | HTTPS | 상태, 로그 등을 보기 위한 웹 UI |
| DataNode | 모든 작업자 노드 | 30010 | &nbsp; | 데이터 전송 |
| DataNode | 모든 작업자 노드 | 30020 | IPC | 메타데이터 작업 |
| 보조 NameNode | 헤드 노드 | 50090 | HTTP | NameNode 메타데이터에 대한 검사점 |

### YARN 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| Resource Manager 웹 UI | 헤드 노드 | 8088 | HTTP | Resource Manager용 웹 UI |
| Resource Manager 웹 UI | 헤드 노드 | 8090 | HTTPS | Resource Manager용 웹 UI |
| Resource Manager 관리 인터페이스 | 헤드 노드 | 8141 | IPC | 응용 프로그램 제출용(Hive, Hive server, Pig 등) |
| Resource Manager 스케줄러 | 헤드 노드 | 8030 | HTTP | 관리 인터페이스 |
| Resource Manager 응용 프로그램 인터페이스 | 헤드 노드 | 8050 | HTTP |응용 프로그램 관리자 인터페이스의 주소 |
| NodeManager | 모든 작업자 노드 | 30050 | &nbsp; | 컨테이너 관리자의 주소 |
| NodeManager 웹 UI | 모든 작업자 노드 | 30060 | HTTP | Resource Manager 인터페이스 |
| 타임라인 주소 | 헤드 노드 | 10200 | RPC | 타임라인 서비스 RPC 서비스. |
| 타임라인 웹 UI | 헤드 노드 | 8181 | HTTP | 타임라인 서비스 웹 UI |

### Hive 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | 헤드 노드 | 10001 | Thrift | 프로그래밍 방식으로 Hive에 연결하기 위한 서비스(Thrift/JDBC) |
| HiveServer | 헤드 노드 | 10000 | Thrift | 프로그래밍 방식으로 Hive에 연결하기 위한 서비스(Thrift/JDBC) |
| Hive Metastore | 헤드 노드 | 9083 | Thrift | 프로그래밍 방식으로 Hive 메타데이터에 연결하기 위한 서비스(Thrift/JDBC) |

### WebHCat 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| WebHCat 서버 | 헤드 노드 | 30111 | HTTP | HCatalog 및 기타 Hadoop 서비스 맨 위의 웹 API |

### MapReduce 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | 헤드 노드 | 19888 | HTTP | MapReduce JobHistory 웹 UI |
| JobHistory | 헤드 노드 | 10020 | &nbsp; | MapReduce JobHistory 서버 |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | 중간 맵 출력을 요청 리듀서에 전송 |

### Oozie

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| Oozie 서버 | 헤드 노드 | 11000 | HTTP | Oozie 서비스에 대한 URL |
| Oozie 서버 | 헤드 노드 | 11001 | HTTP | Oozie 관리자에 대한 포트 |

### Ambari 메트릭

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| 타임라인(응용 프로그램 기록) | 헤드 노드 | 6188 | HTTP | 타임라인 서비스 웹 UI |
| 타임라인(응용 프로그램 기록) | 헤드 노드 | 30200 | RPC | 타임라인 서비스 웹 UI |

### HBase 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | 헤드 노드 | 16000 | &nbsp; | &nbsp; |
| HMaster 정보 웹 UI | 헤드 노드 | 16010 | HTTP | HBase 마스터 웹 UI에 대한 포트 |
| Region 서버 | 모든 작업자 노드 | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | 클라이언트가 ZooKeeper 연결에 사용하는 포트 |

### Kafka 포트

| 부여 | 노드 | 포트 | 프로토콜 | 설명 |
| ------- | ------- | ---- | -------- | ----------- |
| Broker | 작업자 노드 | 9092 | [Kafka 유선 프로토콜](http://kafka.apache.org/protocol.html) | 클라이언트 통신에 사용됨 |
| &nbsp; | Zookeeper 노드 | 2181 | &nbsp; | 클라이언트가 ZooKeeper 연결에 사용하는 포트 |

<!---HONumber=AcomDC_0921_2016-->