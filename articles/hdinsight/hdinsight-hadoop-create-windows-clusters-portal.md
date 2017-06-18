<properties
   pageTitle="HDInsight의 Hadoop 클러스터 만들기 | Microsoft Azure"
   	description="Azure 포털을 사용하여 Azure HDInsight에 클러스터를 만드는 방법을 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Azure 포털을 사용하여 HDInsight의 Windows 기반 Hadoop 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-selector-create-clusters.md)]

Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터를 만드는 방법을 알아봅니다. Microsoft [Azure 포털](../azure-portal-overview.md)은 Azure 리소스를 프로비전하고 관리할 수 있는 중앙 위치입니다. Azure 포털은 HDInsight에서 Linux 기반 또는 Windows 기반 Hadoop 클러스터를 만드는 데 사용할 수 있는 도구 중 하나입니다. 다른 클러스터 만들기 도구 및 기능은 이 페이지 맨 위에 있는 탭 선택을 클릭하거나 [클러스터 생성 방법](hdinsight-provision-clusters.md#cluster-creation-methods)을 참조하세요.

##필수 조건:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 지침을 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

## 클러스터 만들기


**HDInsight 클러스터를 만들려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 분석** 및 **HDInsight**를 차례로 클릭합니다.

    ![Azure 포털에서 새 클러스터 만들기](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Azure 포털에서 새 클러스터 만들기")

3. 다음 값을 입력하거나 선택합니다.

  * **클러스터 이름**: 클러스터의 이름을 입력합니다. 이름을 사용할 수 있는 경우 클러스터 이름 옆에 녹색 확인 표시가 나타납니다.
  * **클러스터 유형**: **Hadoop**을 선택합니다. 다른 옵션은 **HBase**, **Storm** 및 **Spark**를 포함합니다.
  * **클러스터 운영 체제**: **Windows**를 선택합니다. Linux 기반 클러스터를 만들려면 **Linux**를 선택합니다.
  * **버전**: [HDInsight 버전](hdinsight-component-versioning.md)을 참조하세요.
  * **구독**: 이 클러스터를 만드는 데 사용할 Azure 구독을 선택합니다.
  * **리소스 그룹**: 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.
  * **자격 증명**: Hadoop 사용자(HTTP 사용자)의 사용자 이름 및 암호를 구성합니다. 클러스터에 원격 데스크톱을 사용하도록 설정하는 경우 원격 데스크톱 사용자의 사용자 이름 및 암호와 계정 만료 날짜를 구성해야 합니다. 아래쪽의 **선택**을 클릭하여 변경 내용을 저장합니다.

	   	![클러스터 자격 증명 제공](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "클러스터 자격 증명 제공")

  * **데이터 원본**: 클러스터의 기본 파일 시스템으로 사용할 기존 Azure 저장소 계정을 선택하거나 새 Azure 저장소 계정을 만듭니다.

   		![데이터 원본 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "데이터 원본 구성 제공")

  		* **선택 방법**: 모든 구독에서 저장소 계정을 찾을 수 있도록 하려면 이 항목을 **모든 구독에서**로 설정합니다. 기존 저장소 계정의 **저장소 이름** 및 **액세스 키**를 입력하려면 이 항목을 **액세스 키**로 설정합니다.
  		* **저장소 계정 선택/새로 만들기**: 클러스터와 연결할 기존 저장소 계정을 찾아 선택하려면 **저장소 계정 선택**을 클릭합니다. 또는 새 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 저장소 계정의 이름을 입력할 때 나타나는 필드를 사용합니다. 이름을 사용할 수 있는 경우 녹색 확인 표시가 나타납니다.
  		* **기본 컨테이너 선택**: 클러스터에 사용할 기본 컨테이너의 이름을 입력하려면 이 항목을 사용합니다. 여기에 아무 이름이나 입력할 수 있지만, 컨테이너가 이 특정 클러스터에 사용됨을 쉽게 인식할 수 있도록 클러스터와 같은 이름을 사용하는 것이 좋습니다.
  		* **위치**: 저장소 계정이 있거나 저장소 계정을 만들 지리적인 지역입니다. 이 위치는 클러스터 위치를 결정합니다. 클러스터와 해당 기본 저장소 계정은 같은 Azure 데이터 센터에 공동 배치되어야 합니다.
  	
  * **노드 가격 책정 계층**: 클러스터에 필요한 작업자 노드 수를 설정합니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.
  

		![노드 가격 책정 계층 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "클러스터 노드 수 지정")


  * **옵션 구성**: 클러스터 버전을 선택하고 **가상 네트워크**에 가입, Hive 및 Oozie의 데이터를 유지하기 위한 **외부 Metastore** 설정 등 기타 선택적 설정을 구성하고 스크립트 동작을 사용하여 사용자 지정 구성 요소를 설치하기 위해 클러스터를 사용자 지정하거나 클러스터에 추가 저장소 계정을 사용합니다.

  		* **HDInsight 버전**: 클러스터에 사용할 버전을 선택합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.
  		* **가상 네트워크**: 클러스터를 가상 네트워크에 배치하려는 경우 Azure 가상 네트워크 및 서브넷을 선택합니다.

			![가상 네트워크 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "가상 네트워크 세부 정보 지정")

			가상 네트워크에 대한 특정 구성 요구 사항을 포함하여 가상 네트워크로 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.
  

  		
		* **외부 메타 저장소**: 클러스터와 연결된 Hive 및 Oozie 메타데이터를 저장하는 데 사용할 Azure SQL 데이터베이스를 지정합니다.
 
            > [AZURE.NOTE] HBase 클러스터 유형에는 Metastore 구성을 사용할 수 없습니다.

			![사용자 지정 Metastore 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "외부 Metastore 지정")


			**Hive 메타데이터에 기존 SQL DB 사용**에 대해 **예**를 클릭하고 SQL 데이터베이스를 선택한 다음 데이터베이스의 사용자 이름/암호를 입력합니다. **Oozie 메타데이터에 기존 SQL DB 사용**을 원하는 경우 이러한 단계를 반복합니다. **옵션 구성** 블레이드로 돌아갈 때까지 **선택**을 클릭합니다.


			>[AZURE.NOTE] 메타스토어에 사용되는 Azure SQL 데이터베이스는 Azure HDInsight를 비롯한 다른 Azure 서비스로의 연결을 허용해야 합니다. Azure SQL 데이터베이스 대시보드의 오른쪽에서 서버 이름을 클릭합니다. 이 서버는 SQL 데이터베이스 인스턴스가 실행되는 서버입니다. 서버 보기에서 **구성**을 클릭하고 **Azure 서비스**에 대해 **예**를 클릭한 다음 **저장**을 클릭합니다.

            &nbsp;

            > [AZURE.IMPORTANT] Metastore를 만들 때 클러스터를 만드는 프로세스가 실패할 수 있으므로 대시 또는 하이픈을 포함하는 데이터베이스 이름을 사용하지 않습니다.
		
  		* **스크립트 동작**: 클러스터를 만들 때 사용자 지정 스크립트를 사용하여 클러스터를 사용자 지정하려는 경우에 사용합니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요. 스크립트 동작 블레이드에서는 화면 캡처에 표시된 것과 같은 세부 정보를 제공합니다.
  	

			![스크립트 동작 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "스크립트 동작 지정")


    	* **Azure 저장소 키**: 클러스터와 연결할 추가 저장소 계정을 지정합니다. **Azure 저장소 키** 블레이드에서 **저장소 키 추가**를 클릭한 다음 기존 저장소 계정을 선택하거나 새 계정을 만듭니다.
    

			![추가 저장소 블레이드](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "추가 저장소 계정 지정")


4. **만들기**를 클릭합니다. **시작 보드에 고정**을 선택하면 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘이 클러스터를 만드는 중임을 나타내고 생성이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.
	
    클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 프로비전 프로세스를 확인하세요.
	

5. 생성이 완료되면 시작 보드에서 클러스터 타일을 클릭하여 클러스터 블레이드를 시작합니다. 클러스터 블레이드는 이름, 속한 리소스 그룹, 위치, 운영 체제, 클러스터 대시보드의 URL 등 클러스터에 대한 필수 정보를 제공합니다.


	![클러스터 블레이드](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "클러스터 속성")


	다음을 사용하여 이 블레이드의 위쪽과 **필수** 섹션에 있는 아이콘을 이해합니다.


	* **설정** 및 **모든 설정**: 클러스터의 자세한 구성 정보에 액세스할 수 있는 클러스터의 **설정** 블레이드를 표시합니다.
	* **대시보드**, **클러스터 대시보드** 및 **URL**: 이러한 항목을 통해 클러스터에서 작업을 실행하기 위한 웹 포털인 클러스터 대시보드에 액세스할 수 있습니다.
	* **원격 데스크톱**: 클러스터 노드에 대해 원격 데스크톱을 사용하거나 사용하지 않도록 설정할 수 있습니다.
	* **클러스터 크기 조정**: 이 클러스터의 작업자 노드 수를 변경할 수 있습니다.
	* **삭제**: HDInsight 클러스터를 삭제합니다.
	* **빠른 시작**(![구름과 벼락 아이콘 = 빠른 시작](./media/hdinsight-provision-clusters/quickstart.png)): HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.
	* **사용자**(![사용자 아이콘](./media/hdinsight-provision-clusters/users.png)): Azure 구독의 다른 사용자에 대해 이 클러스터의 _포털 관리_ 권한을 설정할 수 있습니다.
	

		> [AZURE.IMPORTANT] 이는 _오직_ 포털에서 이 클러스터에 대한 액세스 및 권한에만 영향을 미치며, HDInsight 클러스터에 연결하거나 작업을 제출할 수 있는 사용자에게는 영향을 미치지 않습니다.
		
	* **태그(![태그 아이콘](./media/hdinsight-provision-clusters/tags.png))**: 태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 __project__라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.

##클러스터 사용자 지정

- [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.
- [스크립트 작업을 사용하여 Windows 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

##다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight 클러스터를 시작하는 방법을 알아봅니다.
* [프로그래밍 방식으로 Hadoop 작업 제출](hdinsight-submit-hadoop-jobs-programmatically.md) - 프로그래밍 방식으로 작업을 HDInsight에 제출하는 방법을 알아봅니다.
* [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md)


<!---HONumber=AcomDC_0914_2016-->