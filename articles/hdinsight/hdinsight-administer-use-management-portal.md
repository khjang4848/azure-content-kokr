<properties
	pageTitle="Azure Portal을 사용하여 HDInsight의 Hadoop 클러스터 관리 | Microsoft Azure"
	description="HDInsight 서비스를 관리하는 방법에 대해 알아봅니다. HDInsight 클러스터를 만들고 대화형 JavaScript 콘솔을 열고 Hadoop 명령 콘솔을 여는 방법에 대해 설명합니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
    ms.date="09/14/2016"
	ms.author="jgao"/>

# Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]

[Azure Portal][azure-portal]에서 Azure HDInsight의 Hadoop 클러스터를 만들고, Hadoop 사용자의 암호를 변경하고, RDP (원격 데스크톱 프로토콜)를 사용하도록 설정하여 클러스터의 Hadoop 명령 콘솔에 액세스할 수 있습니다.

이 문서의 정보는 Windows 기반 HDInsight 클러스터에만 적용됩니다. Linux 기반 클러스터 관리에 대한 내용을 보려면 위의 탭 선택기를 클릭합니다.

다른 도구를 사용하여 HDInsight에서 Hadoop 클러스터를 만드는 정보를 보려면 탭 선택기를 클릭합니다.

**필수 구성 요소**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **Azure 저장소 계정** - HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. Azure Blob 저장소가 HDInsight 클러스터에서 매끄럽게 작동하는 방식에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요. Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법](../storage/storage-create-storage-account.md)을 참조하세요.

##포털 열기

1. [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. 포털을 연 후 다음을 수행할 수 있습니다.

	- 왼쪽 메뉴에서 **새로 만들기**를 클릭하여 새 클러스터를 만듭니다.
	
		![새 HDInsight 클러스터 단추](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
	- 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭합니다.
	
		![Azure 포털 HDInsight 클러스터 단추](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

	왼쪽 메뉴에 **HDInsight**가 표시되지 않으면 **찾아보기**를 클릭합니다.

	![Azure Portal 찾아보기 클러스터 단추](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##클러스터 만들기

포털을 사용하여 만드는 지침은 [HDInsight 클러스터 만들기](hdinsight-provision-clusters.md#create-using-the-preview-portal)를 참조하세요.

HDInsight는 다양한 Hadoop 구성 요소에서 작동합니다. 검증되어 지원되는 구성 요소 목록은 [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)(영문)을 참조하세요. 다음 옵션 중 하나를 사용하여 HDInsight를 사용자 지정할 수 있습니다.

- 스크립트 작업을 사용하여 클러스터 구성을 변경하거나 Giraph 또는 Solr과 같은 사용자 지정 구성 요소를 설치하도록 클러스터를 사용자 지정할 수 있는 사용자 지정 스크립트를 실행합니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)(영문)을 참조하세요.
- 클러스터를 만드는 동안 HDInsight .NET SDK 또는 Azure PowerShell의 클러스터 사용자 지정 매개 변수를 사용합니다. 그러면 이러한 구성 변경 내용이 클러스터 수명 동안 유지되며 Azure 플랫폼이 유지 관리를 위해 정기적으로 수행하는 클러스터 노드 이미지로 다시 설치의 영향을 받지 않습니다. 클러스터 사용자 지정 매개 변수 사용에 대한 자세한 내용은 [HDInsight 클러스터 만들기](hdinsight-provision-clusters.md)를 참조하세요.
- Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Blob 저장소에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)을 참조하세요.

	>[AZURE.NOTE] HDInsight 클러스터에 Jar 파일을 배포하거나 HDInsight 클러스터에서 Jar 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원 센터](https://azure.microsoft.com/support/options/)로 문의하세요.

	> Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)을 참조하세요.

원격 데스크톱 연결을 사용하여 클러스터에 사용자 지정 소프트웨어를 설치하는 기능은 지원되지 않습니다. 헤드 노드의 드라이브에는 파일을 저장하면 안 됩니다. 클러스터를 다시 만들어야 하는 경우 파일이 손실됩니다. Azure Blob 저장소에 파일을 저장하는 것이 좋습니다. Blob 저장소는 영구적입니다.

##클러스터 나열 및 표시

1. [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭합니다.
3. 클러스터 이름을 클릭합니다. 클러스터 목록이 긴 경우 페이지 상단의 필터를 사용할 수 있습니다.
4. 목록에서 클러스터를 두 번 클릭하여 세부 정보를 표시합니다.

	**메뉴 및 요점**:

	![Azure 포털 HDInsight 클러스터 요점](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
	
	- 메뉴를 사용자 지정하려면 메뉴의 아무 곳이나 마우스 오른쪽 단추로 클릭한 후 **사용자 지정**을 클릭합니다.
	- **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.
	- **대시보드**, **클러스터 대시보드** 및 **URL: 이러한 항목을 통해 Linux 기반 클러스터용 Ambari 웹인 클러스터 대시보드에 액세스할 수 있습니다.
	- **Secure Shell**: SSH(Secure Shell) 연결을 사용하여 클러스터에 연결하는 방법에 대한 지침을 보여 줍니다.
	- **클러스터 크기 조정**: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.
	- **삭제**: 클러스터를 삭제합니다.
	- **빠른 시작(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.
	- **사용자**(![사용자 아이콘](./media/hdinsight-administer-use-portal-linux/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.
	
		> [AZURE.IMPORTANT] 이는 _오직_ Azure 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.
	- **태그(![태그 아이콘](./media/hdinsight-administer-use-portal-linux/tags.png))**: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.
	- **Ambari 뷰**: Ambari 웹에 연결합니다.
	
	> [AZURE.IMPORTANT] HDInsight 클러스터에 의해 제공되는 서비스를 관리하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

	**사용 현황**
	
	![Azure 포털 HDInsight 클러스터 사용 현황](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
	
5. **설정**을 클릭합니다.

	![Azure 포털 HDInsight 클러스터 사용 현황](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	- **속성**: 클러스터 속성을 표시합니다.
	- **클러스터 AAD ID**:
	- **Azure 저장소 키**: 기본 저장소 계정 및 키를 표시합니다. 저장소 계정은 클러스터를 만드는 과정에서 구성됩니다.
	- **클러스터 로그인**: 클러스터 HTTP 사용자 이름 및 암호를 변경합니다.
	- **외부 Metastore**: Hive 및 Oozie Metastore를 표시합니다. Metastore는 클러스터 생성 과정 중에만 구성될 수 있습니다.
	- **클러스터 크기 조정**: 클러스터 작업자 노드의 수를 늘리거나 줄입니다.
	- **원격 데스크톱**: 원격 데스크톱(RDP) 액세스를 활성화하거나 비활성화하고 RDP 사용자 이름을 구성합니다. RDP 사용자 이름은 HTTP 사용자 이름과 반드시 달라야 합니다.
	- **공식 파트너**:
    
    > [AZURE.NOTE] 이것은 사용 가능한 설정의 일반적인 목록이며 여기의 모든 설정이 모든 클러스터 유형에 제공되는 것은 아닙니다.

6. **속성**을 클릭합니다.

	속성에 다음 내용이 나열됩니다.
	
	- **호스트 이름**: 클러스터 이름입니다.
	- **클러스터 URL**.
	- **상태**: 중단됨, 수락됨, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, 운영, 실행 중, 오류, 삭제 중, 삭제됨, 시간 초과됨, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization를 포함합니다.
	- **지역**: Azure 위치입니다. 지원되는 Azure 위치를 보려면, [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)의 **지역** 드롭다운 목록 상자를 참고하세요.
	- **생성된 데이터**.
	- **운영 체제**: **Windows** 또는 **Linux**입니다.
	- **형식**: Hadoop, HBase, Storm, Spark.
	- **버전**. [HDInsight 버전](hdinsight-component-versioning.md)을 참조하세요.
	- **구독**: 구독 이름입니다.
	- **구독 ID**.
	- **주 데이터 원본**. Azure Blob 저장소 계정이 기본 Hadoop 파일 시스템으로 사용됩니다.
	- **작업자 노드 가격 책정 계층**.
	- **헤드 노드 가격 책정 계층**.

##클러스터 삭제

클러스터 삭제는 기본 저장소 계정이나 연결된 저장소 계정을 삭제하지 않습니다. 동일한 저장소 계정과 동일한 Metastore를 사용하여 클러스터를 다시 만들 수 있습니다.

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **삭제**를 클릭하고 지침을 따릅니다.

참고 항목: [클러스터 일시 중지/종료](#pauseshut-down-clusters)

##클러스터 크기 조정
클러스터 크기 조정 기능을 사용하여 클러스터를 다시 생성하지 않고 Azure HDInsight에서 실행되는 클러스터에서 사용되는 작업자 노드 수를 변경합니다.

>[AZURE.NOTE] HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다. [클러스터 나열 및 표시](hdinsight-adminster-use-management-portal.md#list-and-show-clusters)를 참조하세요.

HDInsight에서 지원되는 클러스터의 각 형식에 대한 데이터 노드 수를 변경하는 영향은 다음과 같습니다.

- Hadoop은

	모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.

	데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.

- HBase

	HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	HBase shell 사용에 대한 자세한 내용은 다음을 참조하세요.
- Storm

	실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 다시 토폴로지 균형을 조정해야 합니다.

	다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

	* Storm 웹 UI
	* 명령줄 인터페이스(CLI) 도구

	자세한 내용은 [Apache Storm 설명서](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)(영문)을 참조하세요.

	Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

	![HDinsight Storm 규모 균형 재조정](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	다음은 CLI 명령을 사용하여 Storm 토폴로지 균형을 다시 조정하는 방법의 예입니다.

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**클러스터 크기를 조정하려면**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **설정**을 클릭한 다음 **클러스터 크기 조정**을 클릭합니다.
4. **작업자 노드 수**를 입력합니다. 클러스터 노드 수에 대한 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의할 수 있습니다. 비용 정보는 노드 수에 대한 변경 내용을 반영합니다.

	![HDinsight Hadoop Hbase Storm Spark 크기 조정](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##클러스터 일시 중지/종료

대부분의 Hadoop 작업은 이따금 실행되는 일괄 처리 작업입니다. 대부분의 Hadoop 클러스터는 프로세스에 사용되지 않는 기간이 깁니다. HDInsight를 사용하면 데이터가 Azure 저장소에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다.
HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

프로세스를 프로그래밍할 수 있는 방법은 다양합니다.

- 사용자 Azure 데이터 팩터리. 주문형 및 자체 정의 HDInsight 연결된 서비스는 [Azure HDInsight 연결된 서비스](../data-factory/data-factory-compute-linked-services.md) 및 [Azure 데이터 팩터리를 사용한 분석 및 변환](../data-factory/data-factory-data-transformation-activities.md)을 참조하세요.
- Azure PowerShell 사용 [비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data.md)을 참조하세요.
- Azure CLI 사용 [Azure CLI를 사용하여 HDInsight 클러스터 관리](hdinsight-administer-use-command-line.md)를 참조하세요.
- HDInsight .NET SDK 사용 [Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)을 참조하세요.

가격 정보는 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요. 포털에서 클러스터를 삭제하려면 [클러스터 삭제](#delete-clusters)를 참조하세요.

##클러스터 사용자 이름 변경

HDInsight 클러스터마다 두 개의 사용자 계정이 포함될 수 있습니다. HDInsight 클러스터 사용자 계정은 만드는 과정 중에 생성됩니다. RDP를 통해 클러스터에 액세스하기 위해 RDP 사용자 계정을 만들 수도 있습니다. [원격 데스크톱 사용](#connect-to-hdinsight-clusters-by-using-rdp)을 참조하세요.

**HDInsight 클러스터 사용자 이름 및 암호 변경**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **설정**을 클릭한 다음 **클러스터 로그인**을 클릭합니다.
4. **클러스터 로그인**이 활성화되어 있으면 **사용 안함**을 클릭한 후 **사용**을 클릭해야 사용자 이름과 암호를 변경할 수 있습니다.
4. **클러스터 로그인 이름** 및/또는 **클러스터 로그인 암호**를 변경한 다음 **저장**을 클릭합니다.

	![HDinsight 클러스터 사용자 사용자 이름 암호 http 사용자 변경](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##액세스 권한 부여/해지

HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 끝점이 있음).

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. Azure 포털에서 액세스 권한을 해지/부여할 수 있습니다.

>[AZURE.NOTE] 액세스 권한을 부여/해지하여 클러스터 사용자 이름 및 암호를 다시 설정합니다.

**HTTP 웹 서비스 액세스 권한을 부여/해지하려면**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **설정**을 클릭한 다음 **클러스터 로그인**을 클릭합니다.
4. **클러스터 로그인**이 활성화되어 있으면 **사용 안함**을 클릭한 후 **사용**을 클릭해야 사용자 이름과 암호를 변경할 수 있습니다.
6. **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**에 클러스터의 새 사용자 이름 및 암호를 각각 입력합니다.
7. **저장**을 클릭합니다.

	![HDinsight 총합계 http 웹 서비스 액세스 제거](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##기본 저장소 계정 찾기

각 HDInsight 클러스터에는 기본 저장소 계정이 있습니다. 클러스터의 기본 저장소 계정 및 키는 **설정**/**속성**/**Azure Storage 키** 아래에 표시됩니다. [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

	
##리소스 그룹 찾기 

Azure Resource Manager 모드에서는 각각의 HDInsight 클러스터가 Azure 리소스 그룹과 함께 만들어집니다. 클러스터가 속하는 Azure 리소스 그룹이 아래 표시됩니다.

- 클러스터 목록에 **리소스 그룹** 열이 있습니다.
- 클러스터 **Essential** 타일.

[클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.
   
##HDInsight 쿼리 콘솔 열기

HDInsight 쿼리 콘솔에는 다음 기능이 포함됩니다.

- **시작 갤러리**: 갤러리를 사용하려면 [Azure HDInsight 시작 갤러리를 사용하여 Hadoop에 대해 알아보기](hdinsight-learn-hadoop-use-sample-gallery.md)를 참조하세요.
- **Hive 편집기**: Hive 작업 제출을 위한 GUI 웹 인터페이스입니다. [쿼리 콘솔을 사용하여 Hive 쿼리 실행](hdinsight-hadoop-use-hive-query-console.md)을 참조하세요.

	![HDInsight 포털 Hive 편집기](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **작업 내역**: Hadoop 작업을 모니터링합니다.

	![HDInsight 포털 작업 내역](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

	작업 속성, **작업 쿼리**, **작업 출력을 비롯한 세부 정보를 표시하려면 **쿼리 이름**을 클릭합니다. 사용자의 워크스테이션에 쿼리와 출력을 모두 다운로드할 수도 있습니다.

- **파일 브라우저**: 기본 저장소 계정 및 연결된 저장소 계정을 찾아봅니다.

	![HDInsight 포털 파일 브라우저 찾아보기](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

	스크린샷에서 **<계정>** 형식은 해당 항목이 Azure 저장소 계정이라는 것을 나타냅니다. 계정 이름을 클릭하여 파일을 찾아봅니다.
	
- **Hadoop UI**.

	![HDInsight 포털 Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
	
	**Hadoop UI*에서 파일을 찾아보고 로그를 확인할 수 있습니다.

- **Yarn UI**.

	![HDInsight 포털 YARN UI](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##Hive 쿼리 실행

포털에서 Hive 작업을 실행하려면 HDInsight 쿼리 콘솔에서 **Hive 편집기**를 클릭합니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.

##작업 모니터링

포털에서 작업을 모니터링하려면 HDInsight 쿼리 콘솔에서 **작업 내역**을 클릭합니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.

##파일 찾아보기

기본 저장소 계정 및 연결된 저장소 계정에 저장된 파일을 찾아보려면 HDInsight 쿼리 콘솔에서 **파일 브라우저**를 클릭합니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.

HDInsight 콘솔의 **Hadoop UI**에서 **파일 시스템 찾아보기** 유틸리티를 사용할 수도 있습니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.



##클러스터 사용 현황 모니터링

HDInsight 클러스터 블레이드의 __사용량__ 섹션은 해당 클러스터에 할당된 코어 수 및 이 클러스터 내에서 노드에 할당되는 방법뿐만 아니라 HDInsight와 함께 사용하기 위해 구독에 사용할 수 있는 코어 수에 대한 정보도 표시합니다. [클러스터 나열 및 표시](#list-and-show-clusters)를 참조하세요.

> [AZURE.IMPORTANT] HDInsight 클러스터에 의해 제공되는 서비스를 모니터링하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.


##Hadoop UI 열기

클러스터를 모니터링하고, 파일 시스템을 찾아보고, 로그를 확인하려면 HDInsight 쿼리 콘솔에서 **Hadoop UI**를 클릭합니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.

##Yarn UI 열기

Yarn 사용자 인터페이스를 사용하려면 HDInsight 쿼리 콘솔에서 **Yarn UI**를 클릭합니다. [HDInsight 쿼리 콘솔 열기](#open-hdinsight-query-console)를 참조하세요.

##RDP를 사용하여 클러스터에 연결

생성 시 제공한 클러스터의 자격 증명은 원격 데스크톱을 통해 클러스터 자체가 아니라 클러스터의 서비스에 대한 액세스를 제공합니다. 클러스터를 프로비전할 때 또는 클러스터가 프로비전된 후 원격 데스크톱 액세스를 켤 수 있습니다. 생성 시 원격 데스크톱을 사용하도록 설정하는 방법에 대한 지침은 [HDInsight 클러스터 만들기](hdinsight-provision-clusters.md)를 참조하세요.

**원격 데스크톱을 사용하도록 설정하려면**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **설정**을 클릭한 다음 **원격 데스크톱**을 클릭합니다.
4. **만료 날짜**, **원격 데스크톱 사용자 이름** 및 **원격 데스크톱 암호**를 입력한 다음 **사용**을 클릭합니다.

	![HDinsight 원격 데스크톱 설정 해제 구성](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	만료 날짜에 대한 기본 값은 1주일입니다.
> [AZURE.NOTE] HDInsight .NET SDK를 통해 클러스터에서 원격 데스크톱을 사용하도록 설정할 수도 있습니다. 다음과 같은 방식으로 HDInsight 클라이언트 개체에서 **EnableRdp** 메서드를 사용합니다 **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. 마찬가지로, 클러스터에서 원격 데스크톱을 사용하지 않도록 설정하려면 **client.DisableRdp(clustername, location)**을 사용할 수 있습니다. 이러한 메서드에 대한 자세한 내용은 [HDInsight .NET SDK 참조](http://go.microsoft.com/fwlink/?LinkId=529017)를 참조하세요. Windows에서 실행되는 HDInsight 클러스터에만 적용됩니다.

**RDP를 사용하여 클러스터에 연결하려면**

1. [포털][azure-portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **모두 찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭하고 클러스터 이름을 클릭합니다.
3. 상단 메뉴에서 **설정**을 클릭한 다음 **원격 데스크톱**을 클릭합니다.
4. **연결**을 클릭하고 지침을 따릅니다. 연결이 사용 안함인 경우 먼저 활성화해야 합니다. 원격 데스크톱 사용자 이름 및 암호를 사용하고 있는지 확인합니다. 클러스터 사용자 자격 증명을 사용할 수 없습니다.


##Hadoop 명령줄 열기

원격 데스크톱을 통해 클러스터에 연결하고 Hadoop 명령줄을 사용하려면 이전 섹션에 설명된 대로 먼저 클러스터에 대한 원격 데스크톱 액세스를 사용하도록 설정해야 합니다.

**Hadoop 명령줄을 열려면**

1. 원격 데스크톱을 사용하여 클러스터에 연결합니다.
8. 데스크톱에서 **Hadoop 명령줄**을 두 번 클릭합니다.

	![HDI.HadoopCommandLine][image-hadoopcommandline]

	Hadoop 명령에 대한 자세한 내용은 [Hadoop 명령 참조](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)(영문)를 참조하세요.

이전 스크린샷의 폴더 이름에는 Hadoop 버전 번호가 포함되어 있습니다. 버전 번호는 클러스터에 설치된 Hadoop 구성 요소의 버전에 따라 변경될 수 있습니다. Hadoop 환경 변수를 사용하여 해당 폴더를 참조할 수 있습니다. 예:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%
	
##다음 단계
이 문서에서는 포털을 사용하여 HDInsight 클러스터를 만드는 방법과 Hadoop 명령줄 도구를 여는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md)
* [HDInsight 클러스터 만들기](hdinsight-provision-clusters.md)
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight에 포함된 Hadoop 버전](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop 명령줄"

<!---HONumber=AcomDC_0914_2016-->