<properties
	pageTitle="HBase 자습서: Hadoop 및 HBase로 시작 | Microsoft Azure"
	description="HDInsight에서 Hadoop을 통해 Apache HBase 사용을 시작하려면 이 HBase 자습서를 따르세요. HBase 셸에서 테이블을 만들고 Hive를 사용하여 쿼리합니다."
	keywords="apache hbase, hbase, hbase 셸, hbase 자습서"
	services="hdinsight"
	documentationCenter=""
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



# HBase 자습서: HDInsight에서 Windows 기반 Hadoop을 통해 Apache HBase 사용 시작

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

HDInsight에서 HBase 클러스터를 만들고, HBase 테이블을 만들고 Apache Hive를 사용하여 테이블을 쿼리하는 방법에 대해 알아봅니다. 일반 HBase 정보는 [HDInsight HBase 개요][hdinsight-hbase-overview]를 참조하세요.

이 문서에 있는 정보는 Windows 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 클러스터에 대한 내용을 보려면, 페이지 상단의 탭 선택기를 사용하여 전환합니다.

> [AZURE.NOTE] Windows 기반 HDInsight의 HBase(버전 0.98.0)는 HDInsight 3.1 클러스터에서만 사용할 수 있습니다.(Apache Hadoop 및 YARN 2.4.0 기준) 버전 정보는 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능][hdinsight-versions](영문)을 참조하세요.

###시작하기 전에

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 HBase 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Microsoft Azure 구독**: [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- Visual Studio 2013 이상이 포함된 **워크스테이션**: 지침은 [Visual Studio 설치](http://msdn.microsoft.com/library/e2h7fzkw.aspx)를 참조하세요.

## HBase 클러스터 만들기

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Azure 포털을 사용하여 HBase 클러스터를 만들려면**

1. [Azure 포털][azure-management-portal]에 로그인합니다.
2. 왼쪽 위 모서리에서 **새로 만들기** 또는 **+**를 클릭한 다음 **데이터 + 분석**, **HDInsight**를 차례로 클릭합니다.
3. 다음 값을 입력합니다.

	- **클러스터 이름** - 이 클러스터를 식별하기 위한 이름을 입력합니다.
	- **클러스터 유형** - **HBase**를 선택합니다.
	- **클러스터 운영 체제** - **Windows**를 선택합니다. Linux 기반 HBase 클러스터 만들기는 [HBase 자습서: HDInsight에서 Hadoop을 통해 Apache HBase를 사용 시작(Linux)](hdinsight-hbase-tutorial-get-started-linux.md)을 참조하세요.
	- **버전** - HBase 버전을 선택합니다.
	- **구독** - 이 클러스터를 만드는 데 사용할 Azure 구독을 선택합니다.
	- **리소스 그룹** - 새 리소스 그룹을 만들거나 기존 Azure 리소스 그룹을 선택합니다. 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하세요.
	- **자격 증명** - Windows 기반 클러스터의 경우 클러스터 사용자(HTTP 사용자, HTTP 웹 서비스 사용자라고도 함) 및 원격 데스크톱 사용자를 만들 수 있습니다. **원격 데스크톱 사용**을 클릭하여 원격 데스크톱 사용자 자격 증명을 추가합니다. 다음 섹션에는 RDP가 필요합니다.
	- **데이터 원본** - 클러스터의 기본 파일 시스템으로 사용할 기존 Azure 저장소 계정을 선택하거나 새 Azure 저장소 계정을 만듭니다. 기본 저장소 계정 위치는 클러스터 위치를 결정합니다. 기본 저장소 계정 및 클러스터는 같은 데이터 센터에 공동 배치되어야 합니다.
	- **노드 가격 책정 계층** - HBase 클러스터에 사용할 하위 지역 서버 수를 선택합니다.

		> [AZURE.WARNING] HBase 서비스의 고가용성을 위해 **3개** 이상의 노드가 포함된 클러스터를 만들어야 합니다. 이렇게 하면 하나의 노드가 작동이 중지된 경우 다른 노드에서 HBase 데이터 영역을 사용할 수 있습니다.

		> HBase를 학습하는 경우 항상 클러스터 크기로 1을 선택하고 각 사용 후에는 클러스터를 삭제하여 비용을 줄입니다.

	- **옵션 구성** - Azure 가상 네트워크를 구성하고 스크립트 동작을 구성하며 추가 저장소 계정을 추가합니다.

4. **만들기**를 클릭합니다.

>[AZURE.NOTE] HBase 클러스터를 삭제한 후에 동일한 기본 저장소 계정 및 기본 Blob 컨테이너를 사용하여 다른 HBase 클러스터를 만들 수 있습니다. 새 클러스터에서는 원래 클러스터에서 만든 HBase 테이블을 선택합니다. 불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다.

## 테이블 만들기 및 데이터 삽입

현재 HBase에 액세스하는 두 가지 방법이 있습니다. 이 섹션에서는 HBase 셸을 사용하여 설명합니다. 다음 섹션에서는.NET SDK를 사용하여 설명합니다.

대부분의 사람들의 경우, 데이터는 테이블 형식으로 나타납니다.

![hdinsight hbase 테이블 형식 데이터][img-hbase-sample-data-tabular]

BigTable의 구현인 HBase에서 동일한 데이터는 다음과 같이 표시됩니다.

![hdinsight hbase 빅 테이블 데이터][img-hbase-sample-data-bigtable]

다음 절차를 완료한 후가 더 적절합니다.

**HBase 셸을 사용하려면**

1. RDP를 사용하여 HDInsight에서 HBase 클러스터에 연결합니다. RDP 지침의 경우, [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리][hdinsight-manage-portal]를 참조하세요.
2. RDP 세션 내에서 바탕 화면에 있는 **Hadoop 명령줄** 바로 가기를 클릭합니다.
3. HBase 셸을 엽니다.

		cd %HBASE_HOME%\bin
		hbase shell

4. 두 열 패밀리가 있는 HBase를 만듭니다.

		create 'Contacts', 'Personal', 'Office'
		list
5. 일부 데이터 삽입:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![hdinsight hadoop hbase 셸][img-hbase-shell]

6. 단일 행 가져오기

		get 'Contacts', '1000'

	행이 하나만 있기 때문에 스캔 명령을 사용하여 동일한 결과가 표시됩니다.

	Hbase 테이블 스키마에 대한 자세한 내용은 [HBase 스키마 디자인 소개][hbase-schema]를 참조하세요. HBase 명령에 대한 자세한 내용은 [Apache HBase 참조 가이드][hbase-quick-start]를 참조하세요.


6. 셸 종료

		exit

**연락처 HBase 테이블로 대량으로 데이터 로드**

HBase는 테이블로 데이터를 로드하는 여러 방법을 포함합니다. 자세한 내용은 [대량 로드](http://hbase.apache.org/book.html#arch.bulk.load)를 참조하세요.


샘플 데이터 파일은 공용 Blob 컨테이너, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt로 업로드되었습니다. 데이터 파일 내용은 다음과 같습니다.

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

텍스트 파일을 만들고 원하는 경우 고유한 저장소 계정에 파일을 업로드할 수 있습니다. 지침은 [HDInsight에서 Hadoop 작업에 대한 데이터 업로드][hdinsight-upload-data]를 참조하세요.

> [AZURE.NOTE] 이 절차는 마지막 절차에서 만든 연락처 HBase 테이블을 사용합니다.

1. RDP 세션 내에서 바탕 화면에 있는 **Hadoop 명령줄** 바로 가기를 클릭합니다.
2. 디렉터리 변경:

		cd %HBASE_HOME%\bin

3. 데이터 파일을 StoreFiles로 변환하고 Dimporttsv.bulk.output에서 지정된 상대 경에 저장하려면 다음 명령을 실행합니다.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. /Example/data/storeDataFileOutput에서 HBase 테이블로 데이터를 업로드하려면 다음 명령을 실행합니다.

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. HBase 셸을 열고 스캔 명령을 사용하여 테이블 내용을 나열할 수 있습니다.



## Hive를 사용하여 HBase 테이블 쿼리

Hive를 사용하여 HBase 테이블에 저장된 데이터를 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

**클러스터 대시보드를 열려면**

1. **https://<HDInsight Cluster Name>.azurehdinsight.net/**으로 이동합니다.
5. Hadoop 사용자 계정의 사용자 이름 및 암호를 입력합니다. 기본 사용자 이름은 **admin**이고 암호는 만들기 프로세스에서 입력한 암호입니다. 새 브라우저 탭이 열립니다.
6. 페이지 위쪽에서 **Hive 편집기**를 클릭합니다. Hive 편집기는 다음과 같습니다.

	![HDInsight 클러스터 대시보드][img-hdinsight-hbase-hive-editor]

**Hive 쿼리를 실행하려면**

1. 다음 HiveQL 스크립트를 Hive 편집기에 입력하고 **제출**을 클릭하여 HBase 테이블에 매핑되는 Hive 테이블을 만듭니다. 이 문을 실행하기 전에 HBase 셸을 사용하여 이 자습서의 앞에서 참조한 샘플 테이블을 만들었는지 확인합니다.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	**상태**가 **완료**로 업데이트될 때까지 기다립니다.

2. Hive 편집기에 다음 HiveQL 스크립트를 입력한 다음 **제출**을 클릭합니다. 하이브 쿼리는 HBase 테이블에 있는 데이터를 쿼리합니다.

     	SELECT count(*) FROM hbasecontacts;

4. Hive 쿼리 결과를 검색하려면 작업 실행이 완료된 후 **작업 세션** 창에서 **세부 정보 보기** 링크를 클릭합니다. HBase 테이블에 하나의 레코드만 입력했기 때문에 하나의 작업 출력 파일만 있습니다.




**출력 파일을 찾아보려면**

1. 쿼리 콘솔에서 **파일 브라우저**를 클릭합니다.
2. HBase 클러스터에 대한 기본 파일 시스템으로 사용되는 Azure 저장소 계정을 클릭합니다.
3. HBase 클러스터 이름을 클릭합니다. 기본 Azure 저장소 계정 컨테이너에서 클러스터 이름을 사용합니다.
4. **사용자**를 클릭한 다음 **Admin**을 클릭합니다. 이는 Hadoop 사용자 이름입니다.
6. SELECT Hive 쿼리가 실행된 시간과 일치하는 **마지막 수정** 시간의 작업 이름을 클릭합니다.
4. **stdout**를 클릭합니다. 파일을 저장하고 이 파일을 메모장으로 엽니다. 하나의 출력 파일이 있습니다.

	![HDInsight HBase Hive 편집기 파일 브라우저][img-hdinsight-hbase-file-browser]

## .NET HBase REST API 클라이언트 라이브러리 사용

GitHub에서 HBase REST API Client Library for .NET을 다운로드하고 HBase .NET SDK를 사용할 수 있도록 프로젝트를 빌드해야 합니다. 다음 절차에는 이 작업에 대한 지침이 있습니다.

1. 새 C# Visual Studio Windows 데스크톱 콘솔 응용 프로그램을 만듭니다.
2. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 차례로 클릭하여 NuGet 패키지 관리자 콘솔을 엽니다.
3. 콘솔에서 다음 NuGet 명령을 실행합니다.

		Install-Package Microsoft.HBase.Client

5. 파일 맨 위에 다음 **using** 문을 추가합니다.

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. **Main** 함수를 다음으로 바꿉니다.

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. **Main** 함수에서 처음 세 개의 변수를 설정합니다.
8. **F5** 키를 눌러 응용 프로그램을 실행합니다.

## 클러스터 상태 확인

HDInsight에서 HBase는 클러스터 모니터링에 대한 웹 UI와 함께 제공됩니다. 웹 UI를 사용하여 지역에 대한 정보 또는 통계를 요청할 수 있습니다.

웹 UI를 열려면 클러스터로 RDP한 다음 바탕 화면에서 HMaster 정보 웹 UI 바로 가기를 클릭하거나 웹 브라우저에서 다음 URL을 사용합니다.

	http://zookeeper[0-2]:60010/master-status

고가용성 클러스터에서 WebUI를 호스트하는 현재 활성 HBase 마스터 노드에 대한 링크를 찾을 수 있습니다.

##클러스터 삭제
불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다. [AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 다음 작업
HDInsight에 대한 이 HBase 자습서에서는 HBase 클러스터를 만드는 방법 및 테이블을 만들고 HBase 셸에서 가져온 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 HBase 테이블에서 데이터에 대해 Hive 쿼리를 사용하는 방법 및 HBase C# REST API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

자세한 내용은 다음을 참조하세요.

- [HDInsight HBase 개요][hdinsight-hbase-overview]. HBase는 구조화되지 않은/반구조화된 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.
- [Azure 가상 네트워크에 HBase 클러스터 만들기][hdinsight-hbase-provision-vnet]. 가상 네트워크 통합을 사용하면 응용 프로그램이 HBase와 직접 통신할 수 있도록 응용 프로그램과 동일한 가상 네트워크에 HBase 클러스터를 배포할 수 있습니다.
- [HDInsight에서 HBase 복제 구성](hdinsight-hbase-geo-replication.md) 두 Azure 데이터 센터에서 HBase 복제를 구성하는 방법에 대해 알아봅니다.
- [HDInsight에서 HBase를 사용하여 Twitter 데이터 분석][hbase-twitter-sentiment] HDInsight의 Hadoop 클러스터에서 HBase를 사용하여 빅 데이터에 대한 실시간 [데이터 분석](http://en.wikipedia.org/wiki/Sentiment_analysis)을 수행하는 방법을 알아봅니다.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0914_2016-->