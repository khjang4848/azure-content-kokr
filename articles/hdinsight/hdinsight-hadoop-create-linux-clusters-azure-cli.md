<properties
   	pageTitle="플랫폼 간 Azure CLI를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 만들기 | Microsoft Azure"
   	description="플랫폼 간 Azure CLI, Azure 리소스 관리자 템플릿 및 Azure REST API를 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법을 알아봅니다. 클러스터 유형(Hadoop, HBase 또는 Storm)을 지정하거나 스크립트를 사용하여 사용자 지정 구성 요소를 설치할 수 있습니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="09/20/2016"
   	ms.author="larryfr"/>

#Azure CLI를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 명령줄 유틸리티입니다. Azure 리소스 관리 템플릿과 함께 사용하여 HDInsight 클러스터를 연결된 저장소 계정 및 기타 서비스와 함께 만들 수 있습니다.

Azure 리소스 관리 템플릿은 __리소스 그룹__과 그 안의 모든 리소스를 설명하는 JSON 문서입니다(예: HDInsight). 이 템플릿 기반 접근 방식을 사용하면 하나의 템플릿에서 HDInsight에 필요한 모든 리소스를 정의할 수 있습니다. 또한 __배포__를 통해 전체적으로 그룹에 대한 변경 내용을 관리할 수 있으며 이렇게 하면 전체 그룹에 변경 내용을 적용합니다.

이 문서의 단계는 Azure CLI 및 템플릿을 사용하여 새 HDInsight 클러스터를 만드는 과정을 안내합니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 HDInsight 클러스터에 대해 작업자 노드 (4)의 기본 갯수를 사용합니다. 클러스터를 만드는 동안 또는 클러스터를 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

##필수 조건

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- __Azure CLI__. 이 문서의 단계는 Azure CLI 버전 0.10.1로 마지막 테스트되었습니다.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Azure 구독에 로그인합니다.

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md)에서 설명된 단계에 따라 __login__ 메서드를 사용하여 구독에 연결합니다.

##클러스터 만들기

다음 단계는 Azure CLI를 설치하고 구성한 후 명령 프롬프트, 셸 또는 터미널 세션에서 수행되어야 합니다.

1. 다음 명령을 사용하여 Azure 구독에 인증합니다.

        azure login

    사용자 이름 및 암호를 제공하라는 메시지가 표시됩니다. 여러 Azure 구독이 있는 경우 `azure account set <subscriptionname>`을 사용하여 Azure CLI 명령이 사용할 구독을 설정합니다.

3. 다음 명령을 사용하여 Azure 리소스 관리자 모드로 전환합니다.

        azure config mode arm

4. 리소스 그룹을 만듭니다. 이 리소스 그룹에는 HDInsight 클러스터 및 연결된 저장소 계정이 포함됩니다.

        azure group create groupname location
        
    * __groupname__을 그룹의 고유한 이름으로 바꿉니다.
    * __location__을 그룹을 만들 지리적 지역으로 바꿉니다.
    
        유효한 위치 목록에 대해서는 `azure location list` 명령을 사용하고 __이름__ 열의 위치 중 하나를 사용합니다.

5. 저장소 계정을 만듭니다. 이 저장소 계정은 HDInsight 클러스터에 대한 기본 저장소로 사용됩니다.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * __groupname__을 이전 단계에서 만든 그룹의 이름으로 바꿉니다.
     * __location__을 이전 단계에서 사용된 동일한 위치로 바꿉니다.
     * __storagename__을 저장소 계정의 고유한 이름으로 바꿉니다.
     
     > [AZURE.NOTE] 이 명령에서 사용된 매개 변수에 대한 자세한 내용을 보려면 `azure storage account create -h`를 사용하여 이 명령에 대한 도움말을 표시합니다.

5. 저장소 계정에 액세스하는 데 사용된 키를 검색합니다.

        azure storage account keys list -g groupname storagename
        
    * __groupname__을 리소스 그룹 이름으로 바꿉니다.
    * __storagename__을 저장소 계정의 이름으로 바꿉니다.
    
    반환된 데이터에서 __key1__의 __키__ 값을 저장합니다.

6. HDInsight 클러스터 만들기

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * __groupname__을 리소스 그룹 이름으로 바꿉니다.
    * __location__을 이전 단계에서 사용된 동일한 위치로 바꿉니다.
    * __storagename__을 저장소 계정 이름으로 바꿉니다.
    * __storagekey__를 이전 단계에서 얻은 키로 바꿉니다.
    * `--defaultStorageContainer` 매개 변수의 경우 클러스터에 사용하는 것과 같은 이름을 사용합니다.
    * __admin__ 및 __httppassword__를 HTTPS를 통해 클러스터에 액세스할 때 사용할 이름 및 암호로 바꿉니다.
    * __sshuser__ 및 __sshuserpassword__를 SSH를 사용하여 클러스터에 액세스할 때 사용할 사용자 이름 및 암호로 바꿉니다.

    클러스터 생성 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 일반적으로 약 15분이 걸립니다.

##다음 단계

Azure CLI를 사용하여 HDInsight 클러스터를 정상적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux.md)

###Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0921_2016-->