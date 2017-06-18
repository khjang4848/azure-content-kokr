<properties
	pageTitle="파워 쿼리로 Hadoop에 Excel 연결 | Microsoft Azure"
	description="비즈니스 인텔리전스 구성 요소를 사용하는 방법과 Excel용 파워 쿼리를 사용하여 HDInsight의 Hadoop에 저장된 데이터에 액세스하는 방법에 대해 알아봅니다."
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
	ms.date="07/25/2016"
	ms.author="jgao"/>


#파워 쿼리를 사용하여 Hadoop에 Excel 연결

Microsoft의 빅데이터 솔루션이 가진 주요 기능 중 하나는 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 Azure HDInsight의 Hadoop 클러스터와 통합하는 것입니다. 이 통합의 주요 예제로 Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 Hadoop 클러스터와 연결된 데이터가 포함된 Azure 저장소 계정에 Excel을 연결하는 기능이 있습니다. 이 자습서에서는 파워 쿼리를 설정하고 사용하여 HDInsight로 관리하는 Hadoop 클러스터와 연결된 데이터를 쿼리하는 방법을 단계별로 안내합니다.

> [AZURE.NOTE] 이 문서의 단계는 Linux 또는 Windows 기반 HDInsight 클러스터와 함께 사용할 수 있지만, Windows는 클라이언트 워크스테이션에 필요합니다.

### 필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **HDInsight 클러스터**. 구성하려면 [Azure HDInsight 시작][hdinsight-get-started]을 참조하세요.
- Windows 7, Windows Server 2008 R2 이상 운영 체제를 실행 중인 **워크스테이션**.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone 또는 Office 2010 Professional Plus**.


## 파워 쿼리 설치

파워 쿼리를 사용하여 다양한 원본의 데이터를 Microsoft Excel로 가져올 수 있으며 여기서 파워 쿼리는 PowerPivot 및 파워 뷰와 같은 BI 도구를 구동할 수 있습니다. 특히 파워 쿼리는 출력되었거나 HDInsight 클러스터에 대해 실행되는 Hadoop 작업에서 생성된 데이터를 가져올 수 있습니다.

[Microsoft 다운로드 센터][powerquery-download]에서 Microsoft Excel용 파워 쿼리를 다운로드하여 설치합니다.

## Excel로 HDInsight 데이터 가져오기

Excel용 파워 쿼리 추가 기능을 사용하면 HDInsight 클러스터에서 Excel로 쉽게 데이터를 가져올 수 있으며, 여기서 PowerPivot 및 파워 맵 같은 BI 도구를 사용하여 데이터를 검사하고 분석하며 표시할 수 있습니다.

**HDInsight 클러스터에서 데이터 가져오기**

1. Excel을 엽니다.

2. 비어 있는 새 통합 문서를 만듭니다.

3. **파워 쿼리** 메뉴를 클릭하고 **Azure에서**를 클릭한 후 **Microsoft Azure HDInsight에서**를 클릭합니다.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	**참고**: **파워 쿼리** 메뉴가 표시되지 않는 경우 **파일** > **옵션** > **추가 기능**으로 이동하여 페이지 아래쪽에 있는 드롭다운 **관리** 상자에서 **COM 추가 기능**을 선택합니다. **이동...** 단추를 선택하고 Excel용 파워 쿼리 추가 기능에 해당하는 상자가 선택되어 있는지 확인합니다.

	**참고:** 또한 파워 쿼리를 사용하면 **기타 원본에서**를 클릭하여 HDFS에서 데이터를 가져올 수 있습니다.

3. **계정 이름**으로 클러스터와 연결된 Azure Blob 저장소 계정을 입력한 다음 **확인**을 클릭합니다. [기본 저장소 계정](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) 또는 연결된 저장소 계정이 될 수 있습니다. 형식은 *https://<StorageAccountName>.blob.core.windows.net/*입니다.

4. **계정 키**에 Blob 저장소 계정의 키를 입력한 다음 **저장**을 클릭합니다. (이 저장소에 처음 액세스할 때만 이 단계를 수행하면 됩니다.)

5. 쿼리 편집기 왼쪽에 있는 **탐색기** 창에서 Blob 저장소 컨테이너 이름을 두 번 클릭합니다. 기본적으로 컨테이너 이름은 클러스터 이름과 같습니다.

6. **Name** 열에서 **HiveSampleData.txt**(폴더 경로 **../hive/warehouse/hivesampletable/**)를 찾은 후 HiveSampleData.txt 왼쪽에 있는 **Binary**를 클릭합니다. HiveSampleData.txt는 모든 클러스터와 함께 제공됩니다. 필요에 따라 사용자의 파일을 사용할 수 있습니다.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 원하는 경우 열 이름을 바꿀 수 있습니다. 준비가 되면 **닫은 후 로드**를 클릭합니다. 통합 문서에 데이터가 로드됩니다.

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## 다음 단계

이 문서에서는 파워 쿼리를 사용하여 HDInsight에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight에서 Azure SQL 데이터베이스로 데이터를 가져올 수 있습니다. 데이터를 HDInsight에 업로드할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Microsoft Hive ODBC 드라이버로 HDInsight에 Excel 연결](../../articles/hdinsight/hdinsight-connect-excel-hive-odbc-driver.md)
* [HDInsight에 데이터 업로드][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

<!---HONumber=AcomDC_0914_2016-->