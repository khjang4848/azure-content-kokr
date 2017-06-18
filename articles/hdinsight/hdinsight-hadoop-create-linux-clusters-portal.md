<properties
   	pageTitle="포털을 사용하여 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터 만들기 | Microsoft Azure"
   	description="웹 브라우저와 Azure Preview 포털을 사용하여 Linux 기반 HDInsight에서 Hadoop, HBase, Storm 또는 Spark 클러스터를 만드는 방법을 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/07/2016"
   	ms.author="nitinme"/>


#Azure 포털을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure 포털은 Microsoft Azure 클라우드에 호스트된 서비스와 리소스에 대한 웹 기반 관리 도구입니다. 이 문서에서는 포털을 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다.

## 필수 조건

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- __최신 웹 브라우저__. Azure 포털은 HTML5 및 Javascript를 사용하며 이전 웹 브라우저에서 제대로 작동하지 않을 수 있습니다.

##클러스터 만들기

Azure 포털은 대부분의 클러스터 속성을 노출합니다. Azure Resource Manager 템플릿을 사용하여 이러한 많은 속성을 숨길 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Azure 포털에서 새 클러스터 만들기")
3. **클러스터 이름** 입력: 이 이름은 전역적으로 고유해야 합니다.
4. **클러스터 유형 선택**을 클릭한 후 다음을 선택합니다.

    - **클러스터 유형**: 어떤 유형을 선택할지 잘 모르는 경우 **Hadoop**을 선택합니다. 가장 인기 있는 클러스터 유형입니다.
    - **운영 체제**: **Linux**를 선택합니다.
    - **버전**: 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.
    - **클러스터 계층**: Azure HDInsight는 빅 데이터 클라우드 제품을 표준 계층 및 프리미엄 계층의 두 범주로 제공합니다. 자세한 내용은 [클러스터 계층](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers)을 참조하세요.
    
    ![HDInsight 프리미엄 계층 구성](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

4. **구독**을 클릭하여 이 클러스터에 사용할 Azure 구독을 선택합니다.

5. **리소스 그룹**을 클릭하여 기존 리소스 그룹을 선택하거나 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다.

	> [AZURE.NOTE] 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.

6. **자격 증명**을 클릭한 다음 관리자의 암호를 입력합니다. 또한 **SSH 사용자 이름**과 **암호** 또는 **공개 키**(SSH 사용자를 인증하는 데 사용됨) 중 하나를 입력해야 합니다. 공개 키를 사용하는 것이 권장 방식입니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.

	![클러스터 자격 증명 제공](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **데이터 원본**을 클릭하여 클러스터의 기존 데이터 원본을 선택하거나 새로 만듭니다.

	![데이터 원본 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

	현재 HDInsight 클러스터의 데이터 원본으로 Azure 저장소 계정을 선택할 수 있습니다. 다음을 사용하여 **데이터 원본** 블레이드의 항목을 이해합니다.

	- **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **모든 구독에서**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.

	- **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.

	- **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.

	- **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지리적인 지역입니다.

		> [AZURE.IMPORTANT] 기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.
        
    - **클러스터 AAD ID**: 이를 구성하여 클러스터에 AAD 구성에 따라 Azure Data Lake 저장소에 액세스하도록 합니다.

	**선택**을 클릭하여 데이터 원본 구성을 저장합니다.

8. **노드 가격 책정 계층**을 클릭하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	![노드 가격 책정 계층 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "클러스터 노드 수 지정")
    
    > [AZURE.IMPORTANT] 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
    >
    > 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

	**선택**을 클릭하여 노드 가격 책정 구성을 저장합니다.

9. **옵션 구성**을 클릭하여 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하고 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

	* **가상 네트워크**: 클러스터를 가상 네트워크에 배치하려는 경우 Azure 가상 네트워크 및 서브넷을 선택합니다.

		![가상 네트워크 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "가상 네트워크 세부 정보 지정")

    	가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

	* **외부 Metastore**를 클릭하여 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장하는데 사용할 SQL 데이터베이스를 지정합니다.
    
        > [AZURE.NOTE] HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

		![사용자 지정 Metastore 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "외부 Metastore 지정")

		**Hive 메타데이터에 기존 SQL DB 사용**에 대해 **예**를 클릭하고 SQL 데이터베이스를 선택한 다음 데이터베이스의 사용자 이름/암호를 입력합니다. **Oozie 메타데이터에 기존 SQL DB 사용**을 원하는 경우 이러한 단계를 반복합니다. **옵션 구성** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.

		>[AZURE.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

        &nbsp;

        > [AZURE.IMPORTANT] Metastore를 만들 때 클러스터를 만드는 프로세스가 실패할 수 있으므로 대시 또는 하이픈을 포함하는 데이터베이스 이름을 사용하지 않습니다.

	* **스크립트 동작**: 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 사용합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요. 스크립트 동작 블레이드에서는 화면 캡처에 표시된 것과 같은 세부 정보를 제공합니다.

		![스크립트 동작 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "스크립트 동작 지정")

	* **연결된 저장소 계정**을 클릭하여 클러스터와 연결할 추가 저장소 계정을 지정합니다. **Azure 저장소 키** 블레이드에서 **저장소 키 추가**를 클릭한 다음 기존 저장소 계정을 선택하거나 새 계정을 만듭니다.

		![추가 저장소 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "추가 저장소 계정 지정")

		클러스터를 만든 후에 저장소 계정을 더 추가할 수도 있습니다. [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

		**새 HDInsight 클러스터** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.
        
        Blob 저장소 계정 외에 Azure Data Lake 저장소를 연결할 수도 있습니다. 기본 저장소 계정 및 기본 컨테이너를 구성한 데이터 원본에서 AAD를 구성하여 이 구성을 완료할 수 있습니다.

10. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 프로비전 중임을 나타내고 프로비전이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

	| 프로비전 중 | 프로비전 완료 |
	| ------------------ | --------------------- |
	| ![시작 보드에 표시기 프로비전](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![프로비전된 클러스터 타일](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] 클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.

11. 생성 프로세스가 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.

	![클러스터 블레이드](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "클러스터 속성")

	다음을 사용하여 이 블레이드의 위쪽과 **필수** 섹션에 있는 아이콘을 이해합니다.

	* **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.

	* **대시보드**, **클러스터 대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.

	* **보안 셸**: SSH를 사용하여 클러스터에 액세스하는 데 필요한 정보입니다.

	* **삭제**: HDInsight 클러스터를 삭제합니다.

	* **빠른 시작**(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.

	* **사용자**(![사용자 아이콘](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.

		> [AZURE.IMPORTANT] 이는 _오직_ Azure 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.

	* **태그(![태그 아이콘](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png))**: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

##클러스터 사용자 지정

- [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.
- [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

##클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

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

###Spark 클러스터

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0914_2016-->