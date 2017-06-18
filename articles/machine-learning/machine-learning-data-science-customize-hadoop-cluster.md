<properties 
	pageTitle="팀 데이터 과학 프로세스용 Hadoop 클러스터 사용자 지정 | Microsoft Azure" 
	description="일반적인 Python 모듈을 사용자 지정 Azure HDInsight Hadoop 클러스터에서 사용할 수 있습니다."
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="hangzh;bradsev" />

# 팀 데이터 과학 프로세스용 Azure HDInsight Hadoop 클러스터 사용자 지정 

이 문서에서는 HDInsight 서비스로 클러스터를 프로비전할 때 64비트 Anaconda(Python 2.7)를 각 노드에 설치하여 HDInsight Hadoop 클러스터를 사용자 지정하는 방법에 대해 설명합니다. 또한 헤드 노드에 액세스하여 사용자 지정 작업을 클러스터에 제출하는 방법을 보여 줍니다. 이 사용자 지정을 통해 클러스터에서 Hive 레코드를 처리하도록 설계된 UDF(사용자 정의 함수)에서 Anaconda에 포함된 널리 사용되는 많은 Python 모듈을 편리하게 사용할 수 있습니다. 이 시나리오에 사용되는 프로시저에 대한 지침은 [Hive 쿼리를 제출하는 방법](machine-learning-data-science-move-hive-tables.md#submit)을 참조하세요.

다음 메뉴는 [TDSP(팀 데이터 과학 프로세스)](data-science-process-overview.md)에서 사용되는 다양한 데이터 과학 환경을 설정하는 방법을 설명하는 항목에 연결됩니다.

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Azure HDInsight Hadoop 클러스터 사용자 지정

사용자 지정 HDInsight Hadoop 클러스터를 만들려면 [**Azure 클래식 포털**](https://manage.windowsazure.com/)에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭한 다음 데이터 서비스 -> HDINSIGHT -> **사용자 지정 만들기**를 선택하여 **클러스터 세부 정보** 창을 불러와야 합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

구성 페이지 1에서 만들려는 클러스터의 이름을 입력하고 나머지 필드에서는 기본값을 적용합니다. 화살표를 클릭하여 다음 구성 페이지로 이동합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

구성 페이지 2에서 **데이터 노드** 수를 입력하고 **지역/가상 네트워크**를 선택한 다음 **헤드 노드** 및 **데이터 노드**의 크기를 선택합니다. 화살표를 클릭하여 다음 구성 페이지로 이동합니다.

>[AZURE.NOTE] **지역/가상 네트워크**는 HDInsight Hadoop 클러스터에 사용할 저장소 계정의 지역과 동일해야 합니다. 그렇지 않으면 네 번째 구성 페이지에서 사용자가 사용하려는 저장소 계정이 **계정 이름** 드롭다운 목록에 표시되지 않습니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

구성 페이지 3에서 HDInsight Hadoop 클러스터의 사용자 이름 및 암호를 제공합니다. _Hive/Oozie Metastore 입력_은 선택하지 **마세요**. 그런 다음 화살표를 클릭하여 다음 구성 페이지로 이동합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

구성 페이지 4에서 HDInsight Hadoop 클러스터의 기본 컨테이너인 저장소 계정 이름을 지정합니다. 사용자가 **기본 컨테이너** 드롭다운 목록에서 _기본 컨테이너 만들기_를 선택한 경우 클러스터와 이름이 같은 컨테이너가 만들어집니다. 화살표를 클릭하여 마지막 구성 페이지로 이동합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

마지막 **스크립트 동작** 구성 페이지에서 **스크립트 동작 추가** 단추를 클릭하고 텍스트 필드를 다음 값으로 채웁니다.
 
* **이름** - 이 스크립트 동작의 이름인 모든 문자열입니다.
* **노드 유형** - **모든 노드**를 선택합니다.
* **스크립트 URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts*는 저장소 계정의 공용 컨테이너입니다.
	* *getgoing*은 Azure에서 사용자의 작업을 용이하게 하기 위해 PowerShell 스크립트 파일을 공유하는 데 사용됩니다.
* **매개 변수** - 비어 있는 상태로 둡니다.

마지막으로 확인 표시를 클릭하여 사용자 지정 HDInsight Hadoop 클러스터 만들기를 시작합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a> Hadoop 클러스터의 헤드 노드 액세스

RDP를 통해 Hadoop 클러스터의 헤드 노드에 액세스하려면 먼저 Azure에서 Hadoop 클러스터에 대한 원격 액세스를 설정해야 합니다.

1. [**Azure 클래식 포털**](https://manage.windowsazure.com/)에 로그인하여 왼쪽에서 **HDInsight**를 선택하고 클러스터 목록에서 Hadoop 클러스터를 선택한 후 **구성** 탭을 클릭하고 페이지 아래쪽에서 **원격 사용** 아이콘을 클릭합니다.
	
	![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. **원격 데스크톱 구성** 창에서 사용자 이름 및 암호 필드를 입력하고 원격 액세스의 만료 날짜를 선택합니다. 그런 다음 확인 표시를 클릭하여 Hadoop 클러스터의 헤드 노드에 대한 원격 액세스를 설정합니다.

	![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
	
>[AZURE.NOTE] 원격 액세스용 사용자 이름 및 암호는 Hadoop 클러스터를 만들 때 사용하는 사용자 이름 및 암호가 아닙니다. 이는 별도의 자격 증명 집합입니다. 또한 원격 액세스의 만료 날짜는 현재 날짜로부터 7일 이내여야 합니다.

원격 액세스를 설정한 후에는 페이지 아래쪽에서 **연결**을 클릭하여 헤드 노드로 원격 액세스합니다. 이전에 지정한 원격 액세스 사용자의 자격 증명을 입력하여 Hadoop 클러스터의 헤드 노드에 로그온합니다.

![작업 영역 만들기](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

고급 분석 프로세스의 다음 단계는 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)에서 매핑되며, 데이터를 HDInsight로 이동한 후 Azure 기계 학습에서 데이터를 통해 학습할 준비를 수행하면서 데이터를 처리 및 샘플링하는 단계를 포함할 수 있습니다.

클러스터에 저장된 Hive 레코드를 처리하는 데 사용된 UDF(사용자 정의 함수)의 클러스터 헤드 노드에서 Anaconda에 포함된 Python 모듈에 액세스하는 방법에 대한 지침은 [Hive 쿼리를 제출하는 방법](machine-learning-data-science-move-hive-tables.md#submit)을 참조하세요.

 

<!---HONumber=AcomDC_0921_2016-->