<properties
pageTitle="Windows 기반 HDInsight에서 Linux 기반 HDInsight로 마이그레이션 | Azure"
description="Windows 기반 HDInsight 클러스터에서 Linux 기반 HDInsight 클러스터로 마이그레이션하는 방법에 대해 알아봅니다."
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
ms.date="07/25/2016"
ms.author="larryfr"/>

#Windows 기반 HDInsight 클러스터에서 Linux 기반 클러스터로 마이그레이션

Windows 기반 HDInsight는 클라우드에서 Hadoop을 간편하게 사용하는 방법을 제공하지만 솔루션에 필요한 도구와 기술을 이용하기 위해서는 Linux 기반 클러스터가 필요합니다. Hadoop 에코시스템 내의 많은 개체가 Linux 기반 시스템에서 개발되며 일부는 Windows 기반 HDInsight에서 사용하지 못할 수도 있습니다. 또한 많은 책, 동영상 및 기타 교육 자료에서 Hadoop 작업 시 Linux 시스템을 사용한다고 가정합니다.

이 문서는 Windows와 Linux에서의 HDInsight 차이점과 기존 작업에서 Linux 기반 클러스터로 마이그레이션하는 방법에 대한 세부 정보를 제공합니다.

> [AZURE.NOTE] HDInsight 클러스터는 클러스터의 노드에 대한 운영 체제로 Ubuntu LTS(장기 지원)를 사용합니다. HDInsight 3.3 및 3.4 클러스터는 Ubuntu 14.0.4 LTS를 사용합니다. HDInsight의 이전 버전은 Ubuntu 12.04.05 LTS를 사용했습니다.

## 마이그레이션 작업

마이그레이션에 대한 일반적인 워크플로는 다음과 같습니다.

![마이그레이션 워크플로 다이어그램](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  기존의 워크플로, 작업 등을 Linux 기반 클러스터로 마이그레이션할 때 필요한 변경 사항을 파악하려면 이 문서의 각 섹션을 읽어보세요.

2.  테스트/품질 보증 환경으로 Linux 기반 클러스터를 만듭니다. Linux 기반 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

3.  기존의 작업, 데이터 원본 및 싱크를 새 환경으로 복사합니다. 자세한 내용은 테스트 환경 섹션에 데이터 복사를 참조하세요.

4.  작업이 새 클러스터에서 예상대로 작동하는지 확인하려면 유효성 검사 테스트를 수행합니다.

예상대로 작동하는 것이 확인되면 마이그레이션을 위해 가동 중지 시간을 예약합니다. 이 가동 중지 시간에 다음 작업을 수행합니다.

1.  클러스터 노드에 로컬로 저장된 모든 임시 데이터를 백업합니다. 예를 들어 헤드 노드에 직접 저장된 데이터가 있는 경우입니다.

2.  Windows 기반 클러스터를 삭제합니다.

3.  Windows 기반 클러스터가 사용한 기본 데이터 저장소를 동일하게 사용하여 Linux 기반 클러스터를 만듭니다. 이렇게 하면 새 클러스터에서 기존의 프로덕션 데이터에 대해 작업을 계속할 수 있습니다.

4.  백업한 모든 임시 데이터를 가져옵니다.

5.  새 클러스터를 사용하여 작업을 시작하거나 계속 처리합니다.

### 테스트 환경으로 데이터 복사

데이터 및 작업을 복사하는 방법은 여러 가지가 있지만, 이 섹션에서 설명하는 두 가지 방법이 파일을 테스트 클러스터로 직접 이동시키는 가장 간단한 방법입니다.

#### HDFS DFS 복사

다음 단계에 따라 Hadoop HDFS 명령을 사용하여 기존 프로덕션 클러스터를 위한 저장소에서 새 테스트 클러스터를 위한 저장소로 데이터를 직접 복사할 수 있습니다.

1. 기존 클러스터에 대한 저장소 계정 및 기본 컨테이너 정보를 찾습니다. 다음 Azure PowerShell 스크립트를 사용하여 이 작업을 수행할 수 있습니다.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. 새 테스트 환경을 만들려면 HDInsight에서 Linux 기반 클러스터 만들기 문서에 나와 있는 단계를 따릅니다. 클러스터를 만들기 전에 **옵션 구성**을 선택합니다.

3. 옵션 구성 블레이드에서 **연결된 저장소 계정**을 선택합니다.

4. **저장소 키 추가**를 선택하고 메시지가 표시되면, 1단계의 PowerShell 스크립트에서 반환된 저장소 계정을 선택합니다. 각 블레이드에서 **선택**을 클릭하고 이를 닫습니다. 마지막으로 클러스터를 만듭니다.

5. 클러스터가 만들어졌으면 **SSH**를 사용하여 클러스터에 연결합니다. HDInsight에서 SSH를 사용하는 방법을 잘 모르는 경우 다음 문서 중 하나를 참조하세요.

    * [Windows 클라이언트에서 Linux 기반 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Linux, Unix 및 Mac 클라이언트에서 Linux 기반 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

6. SSH 세션에서 다음 명령을 사용하여 연결된 저장소 계정에서 새 기본 저장소 계정으로 파일을 복사합니다. CONTAINER와 ACCOUNT를 1단계의 PowerShell 스크립트에서 반환된 컨테이너 및 계정 정보로 바꿉니다. 데이터 경로를 데이터 파일 경로로 바꿉니다.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] 데이터를 포함하는 디렉터리 구조가 테스트 환경에 없는 경우 다음 명령을 사용하면 만들 수 있습니다.

        hdfs dfs -mkdir -p /new/path/to/create

    `-p` 스위치를 사용하면 경로의 모든 디렉터리를 만들 수 있습니다.

#### Azure 저장소 Blob 간 직접 복사

또는 `Start-AzureStorageBlobCopy` Azure PowerShell cmdlet을 사용하여 HDInsight 외부의 저장소 계정 간에 Blob을 복사할 수 있습니다. 자세한 내용은 Azure 저장소에서 Azure PowerShell 사용에 대한 Azure Blob 섹션 관리 방법을 참조하세요.

##클라이언트 쪽 기술

일반적으로 [Azure PowerShell cmdlet](../powershell-install-configure.md), [Azure CLI](../xplat-cli-install.md) 또는 [Hadoop용 .NET SDK](https://hadoopsdk.codeplex.com/)와 같은 클라이언트 쪽 기술은 Linux 기반 클러스터와 동일하게 작동하는데, 이는 두 클러스터의 OS 형식에서 동일한 REST API를 사용하기 때문입니다.

##서버 쪽 기술

다음 표는 Windows와 관련된 서버 쪽 구성 요소 마이그레이션에 대한 참고 자료를 제공합니다.

| 사용 기술 | 수행 작업 |
| ----- | ----- |
| **PowerShell**(서버 쪽 스크립트, 클러스터 생성 중 사용한 스크립트 동작 포함) | Bash 스크립트로 다시 작성합니다. 스크립트 동작의 경우 [스크립트 동작에서 Linux 기반 HDInsight 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md) 및 [Linux 기반 HDInsight에 대한 스크립트 동작 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요. |
| **Azure CLI**(서버 쪽 스크립트) | Linux에서 Azure CLI를 사용할 수 있지만, HDInsight 클러스터 헤드 노드에 사전에 설치되어 있지는 않습니다. 서버 쪽 스크립팅에 필요한 경우 Linux 기반 플랫폼 설치에 대한 정보는 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요. |
| **.NET 구성 요소** | 현재 .NET은 Linux 기반 HDInsight 클러스터에서 지원되지 않지만 향후 업데이트에 추가됩니다. 지금 마이그레이션해야 하는 경우에는 Java 또는 Python의 구성 요소를 다시 작성해야 합니다. |
| **Win32 구성 요소 또는 기타 Windows 전용 기술** | 참고 자료는 구성 요소 또는 기술에 따라 다릅니다. Linux와 호환되는 버전을 찾을 수도 있고 대체 솔루션을 찾거나 이 구성 요소를 다시 작성해야 할 수도 있습니다. |

##클러스터 만들기

이 섹션에서는 클러스터 만들기의 차이에 대한 정보를 제공합니다.

### SSH 사용자

Linux 기반 HDInsight 클러스터는 클러스터 노드에 원격 액세스를 제공하기 위해 **SSH(Secure Shell)** 프로토콜을 사용합니다. Windows 기반 클러스터용 원격 데스크톱과는 달리 대부분의 SSH 클라이언트는 그래픽 사용자 환경을 제공하지 않으며 그 대신 클러스터에서 명령을 실행할 수 있는 명령줄을 제공합니다. 일부 클라이언트(예: [MobaXterm](http://mobaxterm.mobatek.net/))는 원격 명령줄 외에 그래픽 파일 시스템 브라우저를 제공합니다.

클러스터를 생성하는 동안 인증을 위해 SSH 사용자와 더불어 **암호** 또는 **공개 키 인증서** 중 하나를 제공해야 합니다.

암호보다는 공개 키 인증서가 더 안전하므로 공개 키 인증서를 사용하는 것이 좋습니다. 서명된 공개/개인 키 쌍을 생성한 후 클러스터를 생성할 때 공개 키를 제공하면 인증서 인증이 이루어집니다. SSH를 사용하여 서버에 연결할 때 클라이언트의 개인 키는 연결을 위한 인증을 제공합니다.

HDInsight와 함께 SSH를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Windows 클라이언트에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Linux, Unix 및 OS X 클라이언트에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

### 클러스터 사용자 지정

Linux 기반 클러스터에서 사용된 **스크립트 동작**은 Bash 스크립트에서 작성해야 합니다. 스크립트 동작은 클러스터 생성 중 사용할 수 있지만 Linux 기반 클러스터의 경우 클러스터가 작업을 실행한 후 사용자 지정을 수행할 때에도 사용할 수 있습니다. 자세한 내용은 [스크립트 동작에서 Linux 기반 HDInsight 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md) 및 [Linux 기반 HDInsight에 대한 스크립트 동작 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

다른 사용자 지정 기능은 **부트스트랩**입니다. Windows 클러스터의 경우 이 기능을 사용하면 Hive와 함께 사용하기 위한 추가 라이브러리 위치를 지정할 수 있습니다. 클러스터를 생성한 다음 이러한 라이브러리는 `ADD JAR`를 사용할 필요 없이 자동으로 Hive 쿼리와 함께 사용할 수 있습니다.

Linux 기반 클러스터용 부트스트랩은 이 기능을 제공하지 않습니다. 대신 [클러스터 생성 중 Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md)에 설명된 스크립트 동작을 사용합니다.

### 가상 네트워크

Windows 기반 HDInsight 클러스터는 클래식 가상 네트워크에서만 작동하는 반면 Linux 기반 HDInsight 클러스터는 리소스 관리자 가상 네트워크가 필요합니다. Linux-HDInsight 클러스터에서 연결해야 하는 Classic Virtual Network에 리소스가 있는 경우 [Resource Manager Virtual Network에 Classic Virtual Network 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

HDInsight에서 Azure 가상 네트워크를 사용하기 위한 구성 요구 사항에 대한 자세한 내용은 [가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

##관리 및 모니터링

작업 기록 또는 Yarn UI와 같이 Windows 기반 HDInsight와 함께 사용했을 수도 있는 여러 웹 UI는 Ambari를 통해 사용할 수 있습니다. 또한 Ambari Hive 보기는 사용자의 웹 브라우저를 사용하여 Hive 쿼리를 실행하는 방법을 제공합니다. Ambari 웹 UI는 https://CLUSTERNAME.azurehdinsight.net의 Linux 기반 클러스터에서 사용할 수 있습니다.

Ambari 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Ambari 웹](hdinsight-hadoop-manage-ambari.md)

- [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### Ambari 경고

Ambari에는 클러스터의 잠재적인 문제를 알려주는 경고 시스템이 있습니다. 경고는 Ambari 웹 UI에 빨간색 또는 노란색 항목으로 나타나며 REST API를 통해서도 검색할 수 있습니다.

> [AZURE.IMPORTANT] Ambari 경고는 문제가 *있을 수 있다*라는 의미이며, 문제가 *있다*는 것은 아닙니다. 예를 들어 평소처럼 액세스할 수 있는데도 HiveServer2에 액세스할 수 없다는 경고가 나타날 수 있습니다.
>
> 많은 경고는 서비스에서 간격 기반 쿼리로 구현되므로 특정 시간 프레임 내에서 응답을 기대합니다. 그러므로 경고가 발생했다고 해서 반드시 해당 서비스가 다운되었다는 것이 아니라 예상된 시간 프레임 내에 결과를 반환하지 않았다는 것을 의미합니다.

일반적으로 사용자는 조치를 취하기 전에 경고가 오랜 시간 동안 발생했는지 또는 클러스터에 이전에 보고되었던 사용자 문제를 반영하는지 여부를 평가해야 합니다.

##파일 시스템 위치

Linux 클러스터 파일 시스템은 Windows 기반 HDInsight 클러스터와는 다르게 배치됩니다. 다음 표를 사용하여 일반적으로 사용되는 파일을 찾습니다.

| 찾는 대상 | 위치 |
| ----- | ----- |
| 구성 | `/etc`. 예: `/etc/hadoop/conf/core-site.xml` |
| 로그 파일 | `/var/logs` |
| HDP(Hortonworks Data Platform) | `/usr/hdp`.두 개의 디렉터리가 여기에 있으며 현재 HDP 버전(예: `2.2.9.1-1`) 및 `current`입니다. `current` 디렉터리에는 버전 번호 디렉터리에 있는 파일 및 디렉터리에 대한 기호화된 링크가 포함되어 있으며 HDP 버전이 업데이트될 때 버전 번호도 변하므로 HDP 파일에 액세스하기 위한 편리한 방법으로 이 디렉터리가 제공됩니다. |
| hadoop-streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

일반적으로 파일 이름을 알면 파일 경로를 찾을 때 SSH 세션에서 다음 명령을 사용할 수 있습니다.

    find / -name FILENAME 2>/dev/null

파일 이름과 함께 와일드카드를 사용할 수도 있습니다. 예를 들어 `find / -name *streaming*.jar 2>/dev/null`는 파일 이름 중 'streaming'이라는 단어를 포함하는 모든 jar 파일의 경로를 반환합니다.

##Hive, Pig 및 MapReduce

Linux 기반 클러스터에서 Pig 및 MapReduce 작업은 매우 유사하며 주요 차이점은 원격 데스크톱을 사용하여 Windows 기반 클러스터에 연결하고 작업을 수행하는 경우 Linux 기반 클러스터에서 SSH를 사용한다는 것입니다.

- [SSH와 함께 Pig 사용](hdinsight-hadoop-use-pig-ssh.md)

- [SSH와 함께 MapReduce 사용](hdinsight-hadoop-use-mapreduce-ssh.md)

### Hive

다음 차트는 Hive 작업 마이그레이션에 대한 참고 자료를 제공합니다.

| Windows 기반 | Linux 기반에서... |
| ----- | ----- |
| **Hive 편집기** | [Ambari에서 Hive 보기](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` - Tez 사용 설정 | Tez는 Linux 기반 클러스터에 대한 기본 실행 엔진이므로 SET 문이 더 이상 필요하지 않습니다. |
| Hive 작업의 일부로 호출된 서버의 CMD 파일 또는 스크립트 | Bash 스크립트 사용 |
| 원격 데스크톱에서 `hive` 명령 | [SSH 세션에서 Hive](hdinsight-hadoop-use-hive-ssh.md) 또는 [Beeline](hdinsight-hadoop-use-hive-beeline.md) 사용 |

##Storm

| Windows 기반 | Linux 기반에서... |
| ----- | ----- |
| Storm 대시보드 | Storm 대시보드를 사용할 수 없습니다. 토폴로지를 제출하는 방법은 [Linux 기반 HDInsight에서 Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology-linux.md)를 참조하세요. |
| Storm UI | Storm UI는 https://CLUSTERNAME.azurehdinsight.net/stormui에서 사용할 수 있습니다. |
| C# 또는 하이브리드 토폴로지를 생성, 배포 및 관리하기 위한 Visual Studio | Linux 기반 클러스터는 현재 .NET 토폴로지를 지원하지 않지만 향후 업데이트에서 지원이 추가됩니다. 그 전에 마이그레이션해야 하는 경우 Java에서 사용자의 토폴로지를 재구현해야 합니다. Java 기반 토폴로지 생성 방법에 대한 자세한 내용은 [Java 기반 토폴로지 개발](hdinsight-storm-develop-java-topology.md)을 참조하세요. |

##HBase

Linux 기반 클러스터에서 HBase에 대한 znode 상위는 `/hbase-unsecure`입니다. 기본 HBase Java API를 사용하는 모든 Java 클라이언트 응용 프로그램 구성에서 이를 설정해야 합니다.

이 값을 설정하는 예제 클라이언트에 대한 내용은 [Java 기반 HBase 응용 프로그램 빌드](hdinsight-hbase-build-java-maven.md)를 참조하세요.

##Spark

미리 보기 중 Windows 클러스터에서 Spark 클러스터를 사용할 수 있지만 릴리스의 경우 Spark는 Linux 기반 클러스터에서만 사용할 수 있습니다. Windows 기반 Spark 미리 보기 클러스터에서 릴리스 Linux 기반 Spark 클러스터까지의 마이그레이션 경로는 없습니다.

##알려진 문제

### Azure Data Factory 사용자 지정 .NET 작업

Azure Data Factory 사용자 지정 .NET 작업은 현재 Linux 기반 HDInsight 클러스터에서 지원되지 않습니다. 대신 ADF 파이프라인의 일부로 사용자 지정 작업을 구현하기 위해서는 다음 방법 중 하나를 사용해야 합니다.

-   Azure 배치 풀에서 .NET 작업을 실행합니다. [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용](../data-factory/data-factory-use-custom-activities.md#AzureBatch)의 Azure 배치와 연결된 서비스 섹션 사용을 참조하세요.

-   MapReduce 작업으로 작업을 구현합니다. 자세한 내용은 [데이터 팩터리에서 MapReduce 프로그램 호출](../data-factory/data-factory-map-reduce.md)을 참조하세요.

### 줄 끝

일반적으로 Windows 기반 시스템에서는 줄 끝으로 CRLF를 사용하며, Linux 기반 시스템에서는 LF를 사용합니다. CRLF 줄 끝을 사용하여 데이터를 생성 또는 예상하는 경우, LF 줄 끝을 사용하여 작업하려면 공급자 또는 소비자를 수정해야 할 수 있습니다.

예를 들어 Windows 기반 클러스터에서 Azure PowerShell을 사용하여 HDInsight를 쿼리하면 CRLF를 사용하여 데이터를 반환합니다. Linux 기반 클러스터의 동일한 쿼리는 LF를 반환합니다. 대부분의 경우 이는 데이터 소비자에게 문제가 되지 않지만 Linux 기반 클러스터로 마이그레이션하기 전에 조사해야 합니다.

만약 Linux 클러스터 노드에서 직접 실행되는 스크립트(예: Hive 또는 MapReduce 작업에 사용되는 Python 스크립트)가 있다면 줄 끝으로 항상 LF를 사용해야 합니다. CRLF를 사용하는 경우 Linux 기반 클러스터에서 스크립트를 실행할 때 오류가 표시될 수 있습니다.

포함된 CR 문자가 있는 문자열이 스크립트에 없는 경우 다음 방법 중 하나를 사용하여 줄 끝을 대량 변경할 수 있습니다.

-   **클러스터에 업로드하려는 스크립트가 있는 경우** 클러스터에 스크립트를 업로드하기 전에 다음 PowerShell 문을 사용하여 줄 끝을 CRLF에서 LF로 변경합니다.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **이미 클러스터에 의해 사용되는 저장소에 스크립트가 있는 경우** SSH 세션에서 Linux 기반 클러스터까지 다음 명령을 사용하여 스크립트를 수정합니다.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##다음 단계

-   [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)

-   [Windows 클라이언트에서 SSH를 사용하여 Linux 기반 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Linux, Unix 또는 Mac 클라이언트에서 SSH를 사용하여 Linux 기반 클러스터에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Ambari를 사용하여 Linux 기반 클러스터 관리](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0914_2016-->