<properties
   pageTitle="Hadoop 자습서: Windows에서 Hadoop 시작 | Microsoft Azure"
   description="HDInsight에서 Hadoop을 시작합니다. Windows에서 Hadoop 클러스터를 프로비전하고, 데이터에 대해 Hive 쿼리를 실행하고 Excel에서 결과를 분석하는 방법을 알아봅니다."
   keywords="hadoop 자습서, windows의 hadoop, hadoop 클러스터, hadoop 알아보기, hive 쿼리"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# Hadoop 자습서: Windows의 HDInsight에서 Hadoop 및 Hive 쿼리 시작

> [AZURE.SELECTOR]
- [Windows](../hdinsight-hadoop-tutorial-get-started-windows-v1.md)
- [Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)

Windows의 Hadoop에 대해 알아보고 HDInsight를 사용하여 시작하는 데 도움이 되도록 이 자습서에서는 Hadoop 클러스터의 구조화되지 않은 데이터에서 Hive 쿼리를 실행하고 Microsoft Excel에서 결과를 분석하는 방법을 보여줍니다.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Windows의 HDInsight에서 Hadoop 시작](hdinsight-hadoop-tutorial-get-started-windows.md)

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 이 Hadoop 자습서의 목표

구조화되지 않은 대량의 데이터 집합이 있고 Hive 쿼리를 실행하여 의미 있는 일부 정보를 추출하려고 한다면 이 자습서를 통해 적절한 도움을 얻을 수 있을 것입니다. 해당 방법은 다음과 같습니다.

   !["Hadoop 자습서: 계정 만들기, Hadoop 클러스터 프로비전, Hive 쿼리 제출, Excel에서 데이터 분석.][image-hdi-getstarted-flow]

이 자습서의 데모 비디오를 통해 HDInsight의 Hadoop에 알아보세요.

![첫 번째 Hadoop 자습서의 비디오: Hadoop 클러스터에서 Hive 쿼리를 제출하고 Excel에서 결과를 분석.][img-hdi-getstarted-video]

**[YouTube에서 HDInsight용 Hadoop 자습서 보기](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


Azure HDInsight의 일반적인 가용성과 더불어 Microsoft는 Azure용 HDInsight Emulator(이전의 *Microsoft HDInsight Developer Preview*)도 제공합니다. 이 Emulator는 개발자 시나리오를 대상으로 하기 때문에 단일 노드 배포만 지원합니다. HDInsight Emulator 사용에 대한 자세한 내용은 [HDInsight Emulator 시작][hdinsight-emulator]을 참조하세요.

> [AZURE.NOTE] HBase 클러스터를 프로비전하는 방법에 대한 지침은 [HDInsight에서 HBase 클러스터 프로비전][hdinsight-hbase-custom-provision](영문)을 참조하세요. 두 가지 데이터베이스 중 하나를 선택해야 하는 이유를 이해하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop과 HBase 차이점</a>(영문)을 참조하세요.

## 필수 조건

Windows의 Hadoop에 대해 이 자습서를 시작하기 전에 다음이 있어야 합니다.


- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus가 있는 **워크스테이션 컴퓨터**

**이 자습서를 완료하기 위한 예상 시간:** 30분



##<a name="storage"></a>Azure 저장소 계정 만들기

HDInsight의 Hadoop 클러스터를 프로비전할 때 Azure 저장소 계정을 지정합니다. Hadoop 분산 파일 시스템(HDFS)의 경우처럼 해당 계정의 특정 Blob 저장소 컨테이너는 기본 파일 시스템으로 지정됩니다. HDInsight 클러스터는 기본적으로 사용자가 지정한 저장소 계정과 동일한 데이터 센터에 프로비전됩니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

>[AZURE.NOTE] 여러 Hadoop 클러스터의 기본 Blob 저장소 컨테이너를 공유하지 마세요.

사용자 지정 방식으로 클러스터를 구성할 때는 이 저장소 계정 외에 다른 저장소 계정을 추가할 수 있습니다. 이 추가 저장소 계정은 동일하거나 다른 Azure 구독에서 가져올 수 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

이 자습서에서는 기본 Blob과 기본 저장소 계정만 사용합니다.

**Azure 저장소 계정을 만들려면**

1. [Azure 클래식 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 하단에서 **새로 만들기**를 클릭하고 이미지에 표시된 대로 값을 입력합니다.

	![빠른 생성을 사용하여 새 저장소 계정을 설정할 수 있는 Azure 클래식 포털.][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]  클러스터에 대해 지원되는 위치에 저장소 계정을 만들어야 합니다. **동아시아**, **동남아시아**, **북유럽**, **서유럽**, **미국 동부**, **미국 서부**, **미국 중북부**, **미국 중남부**입니다.

목록에서 새 저장소 계정을 선택하고 페이지 아래쪽에서 **액세스 키 관리**를 클릭합니다. **기본 액세스 키**(또는 **보조 액세스 키** 중 작동하는 키)를 적어 둡니다. 이 정보는 자습서의 뒷부분에서 필요합니다. 자세한 내용은 [저장소 계정을 만드는 방법][azure-create-storageaccount]을 참조하세요.

##<a name="provision"></a>Hadoop 클러스터 프로비전

클러스터를 프로비전할 때는 Hadoop과 관련 응용 프로그램을 포함하는 Azure 계산 리소스를 프로비전합니다. 이 섹션에서는 Hadoop 버전 2.4를 기반으로 하는 HDInsight 버전 3.1 클러스터를 프로비전합니다. Azure 클래식 포털, HDInsight PowerShell cmdlet 또는 HDInsight .NET SDK를 사용하여 다른 버전용 Hadoop 클러스터를 만들 수도 있습니다. 지침에 대해서는 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. HDInsight 버전 및 해당 SLA에 대한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


**Hadoop 클러스터를 프로비전하려면**

1. [Azure 클래식 포털][azure-management-portal]에 로그인합니다.

2. 왼쪽 하단에서 **새로 만들기**를 클릭하고 이미지에 표시된 대로 값을 입력합니다.

	![HDInsight에 Hadoop 클러스터 만들기.][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:


	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account.
	</td></tr>
	</table>

	Keep a copy of the cluster name. You will need it later in the tutorial.


5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE] 이러한 단계에서는 버전 3.1로 HDInsight 클러스터를 프로비전합니다. 다른 클러스터 버전을 만들려면 포털에서 **사용자 지정 만들기** 방법을 사용하거나 Azure PowerShell을 사용합니다. 각 버전 간 차이에 대한 자세한 내용은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][hdinsight-versions](영문)을 참조하세요. **사용자 지정 만들기** 옵션 사용에 대한 자세한 내용은 [사용자 지정 옵션을 사용하여 HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.


##<a name="sample"></a>포털에서 샘플 데이터 실행

올바르게 프로비전된 HDInsight 클러스터에서는 포털에서 샘플을 직접 실행하기 위해 시작 갤러리를 포함하는 쿼리 콘솔을 제공합니다. 샘플을 통해 몇 가지 기본 시나리오를 단계별로 수행하여 HDInsight를 사용하는 방법을 익힐 수 있습니다. 이러한 샘플에는 분석할 데이터, 데이터에 대해 실행할 쿼리 등의 필요한 구성 요소가 모두 함께 제공됩니다. 시작 갤러리의 샘플에 대한 자세한 내용은 [HDInsight 시작 갤러리를 사용하여 HDInsight의 Hadoop에 대해 알아보기](hdinsight-learn-hadoop-use-sample-gallery.md)를 참조하세요.

**샘플을 실행하려면** Azure 클래식 포털에서 샘플을 실행할 클러스터 이름을 클릭한 다음 페이지 아래쪽에서 **쿼리 콘솔**을 클릭합니다. 그러면 열리는 웹 페이지에서 **시작 갤러리** 탭을 클릭하고 **샘플** 범주에서 실행할 샘플을 클릭합니다. 그런 다음 웹 페이지의 지침에 따라 샘플을 완료합니다. 다음 표에는 몇 가지 샘플이 나열되어 있으며 각 샘플이 수행하는 내용에 대한 자세한 정보를 제공합니다.

샘플 | 수행하는 작업
------ | ---------------
[센서 데이터 분석][hdinsight-sensor-data-sample] | HDInsight를 사용하여 HVAC(공기조화 냉난방) 시스템에서 생성하는 기록 데이터를 처리해 설정된 온도를 안정적으로 유지할 수 없는 시스템을 식별하는 방법에 대해 알아봅니다.
[웹 사이트 로그 분석][hdinsight-weblogs-sample] | HDInsight를 사용하여 웹 사이트 로그 파일을 분석해 외부 웹 사이트로부터의 일일 웹 사이트 방문 빈도를 파악하고 사용자에게 발생하는 웹 사이트 오류의 요약을 확인하는 방법에 대해 알아봅니다.
[Twitter 추세 분석](hdinsight-analyze-twitter-data.md) | HDInsight를 사용하여 Twitter에서 추세를 분석하는 방법에 대해 알아봅니다.



##<a name="hivequery"></a>포털에서 Hive 쿼리 실행
HDInsight 클러스터를 프로비전했으므로 다음 단계는 Hive 작업을 실행하여 샘플 Hive 테이블을 쿼리하는 것입니다. HDInsight 클러스터와 함께 제공되는 *hivesampletable* 을 사용합니다. 이 테이블에는 모바일 장치 제조업체, 플랫폼 및 모델에 대한 데이터가 들어 있습니다. 이 테이블의 Hive 쿼리는 특정 제조업체별로 모바일 장치의 데이터를 검색합니다.

> [AZURE.NOTE] Visual Studio용 HDInsight 도구는 Azure SDK for .NET 버전 2.5 이상과 함께 제공됩니다. Visual Studio에서 이 도구를 사용하여 HDInsight 클러스터에 연결하고, Hive 테이블을 만들고, Hive 쿼리를 실행할 수 있습니다. 자세한 내용은 [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]을 참조하세요.

**클러스터 대시보드에서 Hive 작업 실행**

1. [Azure 클래식 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 이전 섹션에서 방금 만든 클러스터를 포함하여 클러스터의 목록이 표시됩니다.
3. Hive 작업을 실행하는 데 사용할 클러스터 이름을 클릭하고 페이지 아래쪽에서 **쿼리 콘솔**을 클릭합니다.
4. 웹 페이지가 다른 브라우저 탭에서 열립니다. Hadoop 사용자 계정과 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 클러스터를 프로비전하는 동안 입력한 암호입니다. 대시보드는 다음과 유사하게 표시됩니다.

	![HDInsight 클러스터 대시보드의 Have 편집기 탭.][img-hdi-dashboard]

	페이지의 위쪽에 탭이 여러 개 있습니다. 기본 탭은 **Hive 편집기**이고 다른 탭은 **작업 기록** 및 **파일 브라우저**입니다. 대시보드를 사용하여 Hive 쿼리를 제출하고, Hadoop 작업 로그를 확인하며, 저장소에서 파일을 찾아볼 수 있습니다.

	> [AZURE.NOTE] 웹 페이지의 URL은 *&lt;ClusterName&gt;.azurehdinsight.net*입니다. 따라서 포털에서 대시보드를 여는 대신, URL을 사용하여 웹 브라우저에서 대시보드를 열 수도 있습니다.

6. **Hive 편집기** 탭에서 **쿼리 이름**으로 **HTC20**을 입력합니다. 쿼리 이름은 작업 제목입니다. 쿼리 창에서 이미지에 표시된 대로 Hive 쿼리를 입력합니다.

	![Hive 편집기의 쿼리 창에 입력된 Hive 쿼리.][img-hdi-dashboard-query-select]

4. **Submit**를 클릭합니다. 잠시 후에 결과가 표시됩니다. 화면은 30초마다 한 번씩 새로 고쳐집니다. **새로 고침**을 클릭하여 화면을 새로 고칠 수도 있습니다.

    ![클러스터 대시보드의 맨 아래에 나열된 Hive 쿼리의 결과.][img-hdi-dashboard-query-select-result]

5. 작업이 완료된 것으로 상태가 표시된 후 출력을 보려면 화면에서 쿼리 이름을 클릭합니다. **작업 시작 시간(UTC)**을 적어둡니다. 이 시간은 나중에 필요합니다.

    ![HDInsight 클러스터 대시보드의 작업 기록 탭에 나열된 작업 시작 시간.][img-hdi-dashboard-query-select-result-output]

    페이지에는 **작업 출력**과 **작업 로그**도 표시됩니다. 출력 파일(\_stdout)과 로그 파일(\_stderr)을 다운로드하기 위한 옵션도 제공됩니다.


**출력 파일 찾아보기**

1. 클러스터 대시보드에서 **파일 브라우저**를 클릭합니다.
2. 저장소 계정 이름을 클릭하고 컨테이너 이름(클러스터 이름과 동일)을 클릭한 후 **사용자**를 클릭합니다.
3. **admin**을 클릭하고 마지막으로 수정한 시간(앞서 기록한 작업 시작 시간보다 약간 나중)이 있는 GUID를 클릭합니다. 이 GUID를 복사합니다. 다음 섹션에서 필요합니다.


   	![파일 브라우저 탭에 나열된 Hive 쿼리 출력 파일 GUID.][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Excel용 Microsoft 비즈니스 인텔리전스 도구에 연결

Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 HDInsight의 작업 출력을 Excel로 내보낼 수 있습니다. 그런 다음 Microsoft 비즈니스 인텔리전스 도구를 사용하여 결과를 추가적으로 분석할 수 있습니다.

이 자습서 부분을 완료하려면 Excel 2013 또는 2010이 설치되어 있어야 합니다.

**Microsoft Excel용 파워 쿼리 다운로드**

- [Microsoft 다운로드 센터](http://www.microsoft.com/download/details.aspx?id=39379)에서 Microsoft Excel용 파워 쿼리를 다운로드하여 설치합니다.

**HDInsight 데이터 가져오기**

1. Excel을 열고 새로운 통합 문서를 만듭니다.
3. **파워 쿼리** 메뉴를 클릭하고 **기타 원본**을 클릭한 후 **Azure HDInsight에서**를 클릭합니다.

	![Azure HDInsight에 대해 열린 Excel 파워 쿼리 가져오기 메뉴.][image-hdi-gettingstarted-powerquery-importdata]

3. 클러스터와 연결된 Azure Blob 저장소 계정의 **계정 이름**을 입력하고 **확인**을 클릭합니다. (이 계정은 자습서의 앞부분에서 만든 저장소 계정입니다.)
4. Azure Blob 저장소 계정의 **계정 키**를 입력한 다음 **저장**을 클릭합니다.
5. 오른쪽 창에서 blob 이름을 두 번 클릭합니다. 기본적으로 blob 이름은 클러스터 이름과 동일합니다.

6. **이름** 열에서 **stdout**을 찾습니다. 해당 **폴더 경로** 열의 GUID가 이전에 적어둔 GUID와 일치하는지 확인합니다. 두 GUID가 일치하는 경우 출력 데이터가 제출한 작업에 해당하는 것입니다. **stdout**의 왼쪽 열에서 **이진**을 클릭합니다.

	![콘텐츠 목록에서 GUID로 데이터 출력 찾기.][image-hdi-gettingstarted-powerquery-importdata2]

9. 왼쪽 위 구석에서 **닫은 후 로드**를 클릭하여 Hive 작업 출력을 Excel로 가져옵니다.

##클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="nextsteps"></a>다음 단계
이 Hadoop 자습서에서는 HDInsight에서 Windows에 Hadoop 클러스터를 프로비전하고, 데이터에 대해 Hive 쿼리를 실행한 후, 비즈니스 인텔리전스 도구를 사용하여 추가 처리하고 그래픽으로 표시할 수 있는 Excel로 결과를 가져오는 방법을 배웠습니다. 자세한 내용은 다음 자습서를 참조하세요.

- [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]
- [HDInsight Emulator 시작][hdinsight-emulator]
- [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]
- [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
- [HDInsight에 데이터 업로드][hdinsight-upload-data]
- [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]
- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
- [HDInsight용 Java MapReduce 프로그램 개발][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage/storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#setup-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=AcomDC_0914_2016-->