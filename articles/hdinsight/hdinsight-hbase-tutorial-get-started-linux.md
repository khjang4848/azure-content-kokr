<properties
	pageTitle="HBase 자습서: Hadoop에서 Linux 기반 HBase 클러스터 시작 | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="07/25/2016"
	ms.author="jgao"/>



# HBase 자습서: HDInsight에서 Linux 기반 Hadoop을 통해 Apache HBase 사용 시작 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

HDInsight에서 HBase 클러스터를 만들고, HBase 테이블을 만들고 Hive를 사용하여 테이블을 쿼리하는 방법에 대해 알아봅니다. 일반 HBase 정보는 [HDInsight HBase 개요][hdinsight-hbase-overview]를 참조하세요.

이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 클러스터에 대한 내용을 보려면, 페이지 상단의 탭 선택기를 사용하여 전환합니다.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###필수 조건

이 HBase 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- [SSU(Secure Shell)](hdinsight-hadoop-linux-use-ssh-unix.md).
- [curl](http://curl.haxx.se/download.html).

## HBase 클러스터 만들기

다음 절차는 Azure Resource Manager 템플릿을 사용하여 HBase 클러스터를 만듭니다. 절차에 사용되는 매개 변수와 다른 클러스터 생성 메서드를 이해하려면 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. Azure Portal에서 템플릿을 열려면 다음 이미지를 클릭합니다. 템플릿은 공용 Blob 컨테이너에 있습니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **매개 변수** 블레이드에서 다음을 입력합니다.

    - **ClusterName**: 만들려는 HBase 클러스터의 이름을 입력합니다.
    - **Cluster login name and password**(클러스터 로그인 이름 및 암호): 기본 로그인 이름은 **admin**입니다.
    - **SSH username and password**(SSH 사용자 이름 및 암호): 기본 사용자 이름은 **sshuser**입니다. 이름은 변경할 수 있습니다.
     
    다른 매개 변수는 선택 사항입니다.
    
    각 클러스터에는 Azure Blob 저장소 계정 종속성이 있습니다. 클러스터를 삭제한 후에는 데이터가 저장소 계정에 유지됩니다. 클러스터 기본 저장소 계정 이름은 "저장소"가 추가된 클러스터 이름이입니다. 템플릿 변수 섹션에 하드 코딩됩니다.
        
3. **확인**을 클릭하여 매개 변수를 저장합니다.
4. **사용자 지정 배포** 블레이드에서 **리소스 그룹** 드롭다운 상자를 클릭한 다음 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 클러스터, 종속 저장소 계정 및 기타 연결된 리소스를 그룹화하는 컨테이너입니다.
5. **약관**을 클릭한 다음 **만들기**를 클릭합니다.
6. **만들기**를 클릭합니다. 클러스터를 만들려면 20분 정도가 걸립니다.


>[AZURE.NOTE] HBase 클러스터를 삭제한 후에는 동일한 기본 Blob 컨테이너를 사용하여 다른 HBase 클러스터를 만들 수 있습니다. 새 클러스터에서는 원래 클러스터에서 만든 HBase 테이블을 선택합니다. 불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다.

## 테이블 만들기 및 데이터 삽입

SSH를 사용하여 HBase 클러스터를 연결하고 HBase 셸을 사용하여 HBase 테이블을 만들고 데이터 및 쿼리 데이터를 삽입할 수 있습니다. Linux, Unix, OS X 및 Windows에서 SSH 사용에 대한 자세한 내용은 [Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop로 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md) 및 [Windows에서 HDInsight의 Linux 기반 Hadoop로 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.
 

대부분의 사람들의 경우, 데이터는 테이블 형식으로 나타납니다.

![hdinsight hbase 테이블 형식 데이터][img-hbase-sample-data-tabular]

BigTable의 구현인 HBase에서 동일한 데이터는 다음과 같이 표시됩니다.

![hdinsight hbase 빅 테이블 데이터][img-hbase-sample-data-bigtable]

다음 절차를 완료한 후가 더 적절합니다.


**HBase 셸을 사용하려면**

1. SSH에서 다음 명령을 실행합니다.

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


샘플 데이터 파일은 공용 Blob 컨테이너, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*로 업로드되었습니다. 데이터 파일 내용은 다음과 같습니다.

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

1. SSH에서 데이터 파일을 StoreFiles로 변환하고 Dimporttsv.bulk.output에서 지정된 상대 경에 저장하려면 다음 명령을 실행합니다. HBase 셸인 경우에는 exit 명령을 사용하여 종료합니다.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. /Example/data/storeDataFileOutput에서 HBase 테이블로 데이터를 업로드하려면 다음 명령을 실행합니다.

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. HBase 셸을 열고 스캔 명령을 사용하여 테이블 내용을 나열할 수 있습니다.



## Hive를 사용하여 HBase 쿼리

Hive를 사용하여 HBase 테이블의 데이터를 쿼리할 수 있습니다. 이 섹션에서는 HBase 테이블에 매핑되는 Hive 테이블을 만들고 이를 사용하여 HBase 테이블의 데이터를 쿼리합니다.

1. **PuTTY**를 열고 클러스터에 연결합니다. 이전 절차의 지침을 참조하세요.
2. Hive 셸을 엽니다.

	   hive
3. 다음 HiveQL 스크립트를 HBase 테이블에 매핑되는 Hive 테이블을 만듭니다. 이 문을 실행하기 전에 HBase 셸을 사용하여 이 자습서의 앞에서 참조한 샘플 테이블을 만들었는지 확인합니다.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. 다음 HiveQL 스크립트를 실행합니다. 하이브 쿼리는 HBase 테이블에 있는 데이터를 쿼리합니다.

     	SELECT count(*) FROM hbasecontacts;

## Curl을 사용하여 HBase REST API 사용

> [AZURE.NOTE] WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.
>
> 이 섹션의 명령에서 **USERNAME**은 클러스터에 대해 인증할 사용자로 바꾸고 **PASSWORD**는 사용자 계정의 암호로 바꿉니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.
>
> REST API는 [기본 인증](http://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	그러면 다음과 같은 응답이 표시됩니다.

		{"status":"ok","version":"v1"}

	이 명령에서 사용된 매개 변수는 다음과 같습니다.

	* **-u** - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
	* **-G** - GET 요청임을 나타냅니다.

2. 다음 명령을 사용하여 기존의 HBase 테이블을 나열합니다.

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. 다음 명령을 사용하여 두 열 패밀리가 있는 새 HBase 테이블을 만듭니다.

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"@name":"Contact1","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}" \
		-v

	스키마는 JSon 형식으로 제공됩니다.

4. 다음 명령을 사용하여 데이터 일부를 삽입합니다.

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"Row":{"key":"MTAwMA==","Cell":{"column":"UGVyc29uYWw6TmFtZQ==", "$":"Sm9obiBEb2xl"}}}" \
		-v

	-d 스위치에 지정된 값을 base64로 인코딩해야 합니다. 예제에서:

	- MTAwMA==: 1000
	- UGVyc29uYWw6TmFtZQ==: Personal:Name
	- Sm9obiBEb2xl: John Dole

	[false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single)를 사용하면 여러 (일괄 처리된) 값을 삽입할 수 있습니다.

5. 다음 명령을 사용하여 행을 가져옵니다.

		curl -u <UserName>:<Password> \
		-X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
		-H "Accept: application/json" \
		-v

HBase Rest에 대한 자세한 내용은 [Apache HBase 참조 가이드](https://hbase.apache.org/book.html#_rest)를 참조하세요.

## 클러스터 상태 확인

HDInsight에서 HBase는 클러스터 모니터링에 대한 웹 UI와 함께 제공됩니다. 웹 UI를 사용하여 지역에 대한 정보 또는 통계를 요청할 수 있습니다.

SSH는 웹 요청과 같은 로컬 요청을 HDInsight 클러스터에 터널링하는 데 사용할 수도 있습니다. HDInsight 클러스터 헤드 노드에서 발생하는 경우 요청이 요청된 리소스에 라우팅됩니다. 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop로 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)을 참조하세요.

**SSH 터널링 세션을 설정하려면**

1. **PuTTY**를 엽니다.
2. 생성 과정에서 사용자 계정을 생성할 때 SSH 키를 제공한 경우 다음 단계를 수행하여 클러스터에 인증할 때 사용하려는 개인 키를 선택해야 합니다.

	**Category**에서 **Connection**, **SSH**를 차례로 확장하고 **Auth**를 선택합니다. 마지막으로 **Browse**를 클릭하고 개인 키가 포함된 .ppk 파일을 선택합니다.

3. **Category**에서 **Session**을 클릭합니다.
4. PuTTY 세션 화면에 대한 기본 옵션에서 다음 값을 입력합니다.

	- **호스트 이름**: 호스트 이름에서 HDInsight 서버의 SSH 주소(또는 IP 주소) 필드입니다. SSH 주소는 **-ssh.azurehdinsight.net**이 뒤에 오는 클러스터 이름입니다. 예를 들면 *mycluster-ssh.azurehdinsight.net*과 같습니다.
	- **포트**: 22. 기본 헤드에 대한 ssh 포트는 22입니다.
5. 대화 상자의 왼쪽에 있는 **카테고리** 섹션에서 **연결**, **SSH**를 차례로 확장한 다음 **터널**을 클릭합니다.
6. SSH 포트 전달을 제어하는 옵션 양식에 다음 정보를 제공합니다.

	- **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 9876과 같습니다.
	- **Dynamic** - 동적 SOCKS 프록시 라우팅을 활성화합니다.
7. **추가**를 클릭하여 설정을 추가합니다.
8. 대화 상자의 아래 쪽에서 **열기**를 클릭하여 SSH 연결을 엽니다.
9. 메시지가 표시되면 SSH 계정을 사용하여 서버에 로그인합니다. 이 SSH 세션을 설정하고 터널을 사용하도록 설정합니다.

**Ambari를 사용하여 Zookeeper의 FQDN을 찾으려면**

1. https://<ClusterName>.azurehdinsight.net/으로 이동합니다.
2. 클러스터 사용자 계정 자격 증명을 두 번 입력합니다.
3. 왼쪽된 메뉴에서 **Zookeeper**를 클릭합니다.
4. 요약 목록에서 세 가지 **ZooKeeper 서버** 링크 중 하나를 클릭합니다.
5. **호스트 이름**을 복사합니다. 예를 들어 zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net입니다.

**클라이언트 프로그램(Firefox)을 구성하고 클러스터 상태를 확인하려면**

1. Firefox를 엽니다.
2. **열기 메뉴** 단추를 클릭합니다.
3. **옵션**을 클릭합니다.
4. **고급**을 클릭하고 **네트워크**를 클릭한 다음 **설정**을 클릭합니다.
5. **수동 프록시 구성**을 선택합니다.
6. 다음 값을 입력합니다.

	- **Socks 호스트**: localhost
	- **포트**: Putty SSH 터널링에서 구성한 동일한 포트를 사용합니다. 예를 들면 9876과 같습니다.
	- **SOCKS v5**: (선택됨)
	- **원격 DNS**: (선택됨)
7. **확인**을 클릭하여 변경 내용을 저장합니다.
8. ZooKeeper>:60010/master-status의 http://&lt;The FQDN으로 이동합니다.

고가용성 클러스터에서 WebUI를 호스트하는 현재 활성 HBase 마스터 노드에 대한 링크를 찾을 수 있습니다.

##클러스터 삭제

불일치를 방지하기 위해 클러스터를 삭제하기 전에 HBase 테이블을 사용하지 않도록 설정하는 것이 좋습니다.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 다음 단계

HDInsight에 대한 이 HBase 자습서에서는 HBase 클러스터를 만드는 방법 및 테이블을 만들고 HBase 셸에서 가져온 데이터를 이 테이블에서 보는 방법을 알아보았습니다. 또한 HBase 테이블에서 데이터에 대해 Hive 쿼리를 사용하는 방법 및 HBase C# REST API를 사용하여 HBase 테이블을 만들고 이 테이블에서 데이터를 검색하는 방법도 알아보았습니다.

자세한 내용은 다음을 참조하세요.

- [HDInsight HBase 개요][hdinsight-hbase-overview]\: HBase는 비구조적/반구조적 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0921_2016-->