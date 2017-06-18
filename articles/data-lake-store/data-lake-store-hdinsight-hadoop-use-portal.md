<properties
   pageTitle="포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 만들기 | Azure"
   description="Azure 포털을 사용하여 Azure 데이터 레이크 저장소로 HDInsight Hadoop 클러스터 만들기 및 사용"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="nitinme"/>

# Azure 포털을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 만들기

> [AZURE.SELECTOR]
- [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
- [PowerShell 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure 포털을 사용하여 Azure 데이터 레이크 저장소에 대한 액세스 권한을 가진 HDInsight 클러스터(Hadoop, HBase 또는 Storm)를 만드는 방법에 대해 알아봅니다. 이 릴리스에 대한 일부 중요한 고려 사항:

* **Hadoop 클러스터(Windows 및 Linux)의 경우** Data Lake 저장소는 추가 저장소 계정으로만 사용될 수 있습니다. 이러한 클러스터에 대한 기본 저장소 계정은 여전히 Azure 저장소 Blob(WASB)입니다.

* **Storm 클러스터(Windows 및 Linux)의 경우** Data Lake 저장소는 Storm 토폴로지에서 데이터를 쓰는 데 사용될 수 있습니다. 데이터 레이크 저장소는 Storm 토폴로지에서 읽을 수 있는 참조 데이터를 저장하는 데도 사용될 수 있습니다. 자세한 내용은 [Storm 토폴로지에서 Data Lake 저장소 사용](#use-data-lake-store-in-a-storm-topology)을 참조하세요.

* **HBase 클러스터(Windows 및 Linux)의 경우** Data Lake Store를 기본 저장소나 추가 저장소로 사용할 수 있습니다. 자세한 내용은 [HBase 클러스터에서 Data Lake 저장소 사용](#use-data-lake-store-with-hbase-clusters)을 참조하세요.

> [AZURE.NOTE] 염두해 둘 몇 가지 중요한 사항은 다음과 같습니다.
> 
> * Data Lake 저장소에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2 및 3.4(Windows 및 Linux의 경우)에만 사용할 수 있습니다. Linux에서 Spark 클러스터에 대 한이 옵션은 3.4 HDInsight 클러스터에서 사용할 수만 있습니다.
>
> * 위에서 설명했듯이 일부 클러스터 형식(HBase)에 대한 기본 저장소 및 다른 클러스터 형식(예: Hadoop, Spark, Storm)에 대한 추가 저장소로 Data Lake 저장소를 사용할 수 있습니다. Data Lake 저장소를 추가 저장소 계정으로 사용하면 클러스터에서 저장소로 읽고 쓰는 성능 또는 기능에 영향을 주지 않습니다. Data Lake 저장소를 추가 저장소로 사용하는 시나리오에서 처리하려는 데이터는 Data Lake 저장소 계정에 저장되는 반면 클러스터 관련 파일(예: 로그 등)은 기본 저장소(Azure Blob)에 기록됩니다.


## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- **Azure Data Lake 저장소 계정** [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다. 계정을 만든 후 다음 작업을 수행하여 몇몇 샘플 데이터를 업로드합니다. 데이터 레이크 저장소의 데이터에 액세스하는 HDInsight 클러스터에서 작업을 실행하려면 자습서의 뒷부분에서 이 데이터가 필요합니다.

	* [데이터 레이크 저장소에 폴더를 만듭니다](data-lake-store-get-started-portal.md#createfolder).
	* [Data Lake 저장소에 파일을 업로드합니다](data-lake-store-get-started-portal.md#uploaddata). 업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다.

## 비디오를 통해 보다 빠르게 사용 방법을 익힐 수 있습니다.

Data Lake 저장소에 대한 액세스와 HDInsight 클러스터를 프로비전하는 방법을 이해하려면 다음 비디오를 시청하세요.

* [Data Lake 저장소에 대한 액세스와 HDInsight 클러스터 만들기](https://mix.office.com/watch/l93xri2yhtp2)
* 클러스터가 설정된 후, [Hive 및 Pig 스크립트를 사용하여 Data Lake 저장소의 데이터에 액세스](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Azure 데이터 레이크 저장소 계정에 액세스할 수 있는 HDInsight 클러스터를 만듭니다.

이 섹션에서는 추가 저장소로 데이터 레이크 저장소를 사용하는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스에서 Hadoop 클러스터의 경우 데이터 레이크 저장소는 클러스터에 대해 추가 저장소로만 사용될 수 있습니다. 기본 저장소는 여전히 Azure 저장소 Blob(WASB)이 됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만들어 보겠습니다.

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)에서 단계를 따라 HDInsight 클러스터 프로비전을 시작합니다.

3. **선택적 구성** 블레이드에서 **데이터 원본**을 클릭합니다. **데이터 원본** 블레이드에서 저장소 계정 및 저장소 컨테이너에 대한 세부 정보를 지정하고 **위치**를 **미국 동부 2**로 지정한 다음 **클러스터 AAD ID**를 클릭합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

4. **클러스터 AAD ID** 블레이드에서 기존 서비스 주체를 선택하거나 새로 만들기를 선택할 수 있습니다.

	* **새 서비스 주체를 만듭니다.**

		* **클러스터 AAD ID** 블레이드에서 **새로 만들기**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 만들기** 블레이드에서 값을 제공하여 새 서비스 주체를 만듭니다. 이 작업의 일부로 인증서와 Azure Active Directory 응용 프로그램도 생성됩니다. **만들기**를 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 **ADLS 액세스 관리**를 클릭합니다. 창에 구독과 연결된 Data Lake Store 계정이 표시됩니다. 그러나 만든 계정에 대해서만 사용 권한을 설정할 수 있습니다. HDInsight 클러스터와 연결할 계정에 대한 읽기/쓰기/실행 사용 권한을 선택한 다음 **사용 권한 저장**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 **인증서 다운로드**를 클릭하여 만든 서비스 주체와 연결된 인증서를 다운로드합니다. 추가 HDInsight 클러스터를 만드는 동안 나중에 동일한 서비스 주체를 사용하려는 경우에 유용합니다. **선택**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "HDInsight 클러스터에 서비스 주체 추가")


	* **기존 서비스 주체 선택**

		* **클러스터 AAD ID** 블레이드에서 **기존 정보 사용**, **서비스 주체**를 차례로 클릭한 다음 **서비스 주체 선택** 블레이드에서 기존 서비스 주체를 검색합니다. 서비스 주체 이름을 클릭한 다음 **선택**을 클릭합니다.

			![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")

		* **클러스터 AAD ID** 블레이드에서 선택한 서비스 주체와 연결된 인증서(.pfx)를 업로드한 다음 인증서 암호를 제공합니다.

5. **ADLS 액세스 관리**를 클릭한 다음 **파일 권한 선택**을 클릭합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "HDInsight 클러스터에 서비스 주체 추가")

6. **파일 권한 선택** 블레이드의 **계정** 드롭다운에서 HDInsight 클러스터와 연결할 Data Lake Store 계정을 선택합니다. 그러면 블레이드에 선택한 Data Lake Store 계정에서 사용 가능한 파일 및 폴더가 나열됩니다.
 
	![Data Lake Store 액세스 기능 제공](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Data Lake Store 액세스 기능 제공")

	그런 다음 선택한 파일 및 폴더에 대해 제공할 권한을 결정합니다. 폴더의 경우에는 권한을 해당 폴더에만 적용할지 아니면 폴더와 폴더 내의 모든 자식 항목에 적용할지도 지정합니다. **적용 대상** 드롭다운 목록에서 적절한 값을 선택하여 이 옵션을 선택할 수 있습니다. 권한을 제거하려면 **삭제** 아이콘을 클릭합니다.

	![Data Lake Store 액세스 기능 제공](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Data Lake Store 액세스 기능 제공")

	다른 Data Lake Store 계정에서도 연결된 파일 및 폴더에 대해 이러한 단계를 반복합니다. 권한 할당을 완료한 후 블레이드 아래쪽에 있는 **선택**을 클릭합니다.

7. **선택한 사용 권한 할당** 블레이드에서 제공한 권한을 검토한 다음 **실행**을 클릭하여 해당 권한을 부여합니다.

	![Data Lake Store 액세스 기능 제공](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Data Lake Store 액세스 기능 제공")

	상태 열에 진행률이 표시됩니다. 모든 권한이 정상적으로 할당되면 **완료**를 클릭합니다.

6. **클러스터 AAD ID** 및 **데이터 소스** 블레이드에서 **선택**을 클릭한 다음 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)의 설명에 따라 클러스터 만들기를 계속 진행합니다.

7. 클러스터가 프로비전된 후 서비스 주체가 HDInsight 클러스터와 연결되어 있음을 확인할 수 있습니다. 이렇게 하려면 클러스터 블레이드에서 **클러스터 AAD ID**를 클릭하여 연결된 서비스 주체를 확인합니다.

	![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight 클러스터에 서비스 주체 추가")

## HDInsight 클러스터에서 테스트 작업을 실행하여 Azure 데이터 레이크 저장소 사용

HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 Azure 데이터 레이크 저장소의 데이터에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 데이터 레이크 저장소를 대상으로 하는 일부 hive 쿼리를 실행합니다.

### Linux 클러스터의 경우

1. 방금 프로비전한 클러스터에 대한 클러스터 블레이드를 연 다음 **대시보드**를 클릭합니다. Linux 클러스터에 대한 Ambari를 엽니다. Ambari에 액세스할 때 사이트에 인증하라는 메시지가 표시됩니다. 클러스터를 만들 때 사용한 관리자(기본 관리자), 계정 이름 및 암호를 입력합니다.

	![클러스터 대시보드 시작](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "클러스터 대시보드 시작")

	웹 브라우저에 https://CLUSTERNAME.azurehdinsight.net을 입력하여 Ambari로 직접 이동할 수도 있습니다. 여기서 **CLUSTERNAME**은 HDInsight 클러스터의 이름입니다.

2. Hive 뷰를 엽니다. **관리자** 링크와 페이지 오른쪽의 단추 옆에 있는 페이지 메뉴에서 사각형 집합을 선택하여 사용 가능한 보기의 목록을 표시합니다. **Hive** 보기를 선택합니다.

	![Ambari 뷰 선택](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. 다음과 유사한 결과가 표시됩니다.

	![쿼리 편집기 섹션이 포함된 Hive 뷰 페이지 이미지](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. 페이지의 **쿼리 편집기** 섹션에서 다음 HiveQL 문을 워크시트에 붙여넣습니다.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. **쿼리 편집기** 아래쪽에 있는 **실행** 단추를 클릭하여 쿼리를 시작합니다. **쿼리 편집기** 아래에 **쿼리 프로세스 결과** 섹션이 나타나고 작업에 대한 정보가 표시됩니다.

6. 쿼리가 완료되면 **쿼리 프로세스 결과** 섹션에 작업 결과가 표시됩니다. **결과** 탭에는 다음 정보가 표시됩니다.

7. 다음 쿼리를 실행하여 테이블이 생성되었는지 확인합니다.

		SHOW TABLES;

	**결과** 탭에는 다음 정보가 표시됩니다.

		hivesampletable
		vehicles

	**vehicles**는 앞에서 만든 테이블입니다. **hivesampletable**은 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 샘플 테이블입니다.

8. 쿼리를 실행하여 **vehicles** 테이블에서 데이터를 검색할 수도 있습니다.

		SELECT * FROM vehicles LIMIT 5;

### Windows 클러스터의 경우

1. 방금 프로비전한 클러스터에 대한 클러스터 블레이드를 연 다음 **대시보드**를 클릭합니다.

	![클러스터 대시보드 시작](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "클러스터 대시보드 시작")

	메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

2. Microsoft Azure HDInsight 쿼리 콘솔이 열립니다. **Hive 편집기**를 클릭합니다.

	![Hive 편집기 열기](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Hive 편집기 열기")

3. Hive 편집기에서 다음 쿼리를 입력한 다음 **제출**을 클릭합니다.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	이 Hive 쿼리에서 데이터 레이크 저장소에 저장된 데이터에서 테이블을 `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`에 만듭니다. 이 위치에는 이전에 업로드했어야 하는 샘플 데이터 파일이 있습니다.

	아래의 **작업 세션** 테이블은 **초기화 중**에서 **실행 중**, **완료됨**으로 작업 변경 상태를 보여 줍니다. **세부 정보 보기**를 클릭하여 완료된 작업에 대한 자세한 정보를 볼 수 있습니다.

	![테이블 만들기](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "테이블 만들기")

4. 다음 쿼리를 실행하여 테이블이 생성되었는지 확인합니다.

		SHOW TABLES;

	이 쿼리에 해당하는 **세부 정보 보기**를 클릭하면 출력에 다음 정보가 표시됩니다.

		hivesampletable
		vehicles

	**vehicles**는 앞에서 만든 테이블입니다. **hivesampletable**은 모든 HDInsight 클러스터에서 기본으로 사용할 수 있는 샘플 테이블입니다.

5. 쿼리를 실행하여 **vehicles** 테이블에서 데이터를 검색할 수도 있습니다.

		SELECT * FROM vehicles LIMIT 5;


## HDFS 명령을 사용한 액세스 데이터 레이크 저장소

데이터 레이크 저장소를 사용하도록 HDInsight 클러스터를 구성한 후 HDFS 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

### Linux 클러스터의 경우

이 섹션에서 클러스터로 SSH하고 HDFS 명령을 실행합니다. Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop에 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

연결되면 다음 HDFS 파일 시스템 명령을 사용하여 데이터 레이크 저장소에 파일을 나열합니다.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.


### Windows 클러스터의 경우

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭한 다음 만든 HDInsight 클러스터를 클릭합니다.

3. 클러스터 블레이드에서 **원격 데스크톱**을 클릭한 다음 **원격 데스크톱** 블레이드에서 **연결**을 클릭합니다.

	![HDI 클러스터로 원격](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Azure 리소스 그룹 만들기")

	메시지가 표시되면 원격 데스크톱 사용자에 대해 제공된 자격 증명을 입력합니다.

4. 원격 세션에서 Windows PowerShell을 시작하고 HDFS 파일 시스템 명령을 사용하여 Azure 데이터 레이크 저장소의 파일을 나열합니다.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.

## Spark 클러스터로 Data Lake 저장소 사용

이 섹션에서는 HDInsight Spark 클러스터로 사용할 수 있는 Jupyter 노트북을 사용하여, 기본 Azure Storage Blob 계정 대신 HDInsight Spark 클러스터와 관련이 있는 Data Lake 저장소 계정에서 데이터를 읽는 작업을 실행합니다.

1. Spark 클러스터와 연결된 기본 저장소 계정(WASB)에서 해당 클러스터와 연결된 Azure Data Lake 저장소 계정으로 몇 가지 샘플 데이터를 복사합니다. 이 작업에는 [ADLCopy 도구](http://aka.ms/downloadadlcopy)를 사용할 수 있습니다. 링크에서 도구를 다운로드하여 설치합니다.

2. 명령 프롬프트를 열고 AdlCopy가 설치된 디렉터리로 이동합니다(일반적으로 `%HOMEPATH%\Documents\adlcopy`).

3. 다음 명령을 실행하여 원본 컨테이너에서 데이터 레이크 저장소로 특정 BLOB를 복사합니다.

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	이 자습서에서는 **/HdiSamples/HdiSamples/SensorSampleData/hvac/**의 **HVAC.csv** 샘플 데이터 파일을 Azure Data Lake Store 계정으로 복사합니다. 코드 조각은 다음과 같습니다.

		AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	>[AZURE.WARNING] 파일과 경로 이름의 대/소문자가 적절한지 확인합니다.

4. Data Lake Store 계정이 있는 Azure 구독에 대한 자격 증명을 입력하라는 메시지가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

		Initializing Copy.
		Copy Started.
		100% data copied.
		Copy Completed. 1 file copied.

	데이터 파일(**HVAC.csv**)은 Data Lake Store 계정의 **/hvac** 폴더에 복사됩니다.

4. [Azure 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

	> [AZURE.NOTE] 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

	![새 Jupyter 노트북 만들기](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "새 Jupyter 노트북 만들기")

3. 새 노트북이 만들어지고 **Untitled.pynb**라는 이름으로 열립니다.

4. PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 이 시나리오에 필요한 형식을 가져와 시작할 수 있습니다. 이렇게 하려면 셀에 다음 코드 조각을 붙여 넣고 **SHIFT + ENTER**를 누릅니다.

		from pyspark.sql.types import *
		
	Jupyter에서 작업을 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다. 작업이 완료되면 속이 빈 원으로 변경됩니다.

	 ![Jupyter 노트북 작업의 상태](./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Jupyter 노트북 작업의 상태")

4. Data Lake Store 계정에 복사한 **HVAC.csv** 파일을 사용하여 샘플 데이터를 임시 테이블에 로드합니다. 다음 URL 패턴을 사용하여 Data Lake 저장소 계정의 데이터에 액세스할 수 있습니다.

		adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

	빈 셀에는 다음 코드 예제를 붙여넣습니다. 이때 **MYDATALAKESTORE**를 Data Lake Store 계정 이름으로 바꾸고 **Shift+Enter**를 누릅니다. 이 코드 예제는 **hvac**라는 임시 테이블에 데이터로 등록됩니다.

		# Load the data
		hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. PySpark 커널을 사용하기 때문에 이제 `%%sql` 매직을 사용하여 방금 만든 임시 테이블 **hvac**에서 SQL 쿼리를 직접 실행할 수 있습니다. `%%sql` 매직 및 기타 PySpark 커널에서 사용 가능한 매직에 대한 자세한 내용은 [Spark HDInsight 클러스터와 함께 Jupyter Notebook에서 사용 가능한 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)을 참조하세요.
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. 작업이 성공적으로 완료되면 기본적으로 다음 테이블 형식의 출력이 표시됩니다.

 	![쿼리 결과의 테이블 출력](./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "쿼리 결과의 테이블 출력")

	다른 시각화로 결과를 볼 수도 있습니다. 예를 들어 동일한 출력에 대한 영역 그래프는 다음과 같습니다.

	![쿼리 결과의 영역 그래프](./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "쿼리 결과의 영역 그래프")


6. 응용 프로그램 실행을 완료한 후 리소스를 해제하도록 노트북을 종료해야 합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **Close and Halt**를 클릭합니다. 그러면 Notebook이 종료되고 닫힙니다.

## Storm 토폴로지에서 데이터 레이크 저장소 사용

데이터 레이크 저장소를 사용하여 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 수행하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 Azure Data Lake Store 사용](../hdinsight/hdinsight-storm-write-data-lake-store.md)을 참조하세요.

## HBase 클러스터에서 Data Lake 저장소 사용

HBase 클러스터에서 추가 저장소 외에 기본 저장소로 Data Lake 저장소를 사용할 수 있습니다. 이렇게 하려면 다음을 수행합니다.

1.  **데이터 소스** 블레이드에서 **HBase 데이터 위치**로 **Data Lake Store**를 선택합니다.
2.  사용할 Data Lake 저장소의 이름을 선택하거나 새 Data Lake 저장소를 만듭니다.
3.  마지막으로 데이터 Data Lake Store 내에 **HBase 루트 폴더**를 지정합니다. Data Lake 저장소 계정에 루트 폴더가 없으면 새로 만듭니다.

	![Data Lake 저장소와 HBase](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Azure 리소스 그룹 만들기")

### Data Lake 저장소를 HBase 클러스터에 대한 기본 저장소로 사용할 경우 고려 사항

* 둘 이상의 HBase 클러스터에 대해 동일한 Data Lake 저장소 계정을 사용할 수 있습니다. 그러나 클러스터에 대해 제공하는 **HBase 루트 폴더**(위 화면 캡처의 4단계)는 고유해야 합니다. 서로 다른 두 개의 HBase 클러스터에서 동일한 루트 폴더를 사용하면 **안 됩니다**.
* Data Lake 저장소 계정을 기본 저장소로 사용하더라도 HBase 클러스터 로그 파일은 클러스터와 연결된 Azure 저장소 Blob(WASB)에 그대로 저장됩니다. 이 내용은 위 화면 캡처에 파란색 상자로 강조 표시되어 있습니다.



## 참고 항목

* [PowerShell: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0914_2016-->