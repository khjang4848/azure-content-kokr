<properties
	pageTitle="스크립트 작업을 사용하여 Linux 기반 HDInsight에 Solr 설치 | Microsoft Azure"
	description="스크립트 작업을 사용하여 Linux 기반 HDInsight Hadoop 클러스터에 Solr를 설치하는 방법에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="larryfr"/>

# HDInsight Hadoop 클러스터에서 Solr 설치 및 사용

이 항목에서는 스크립트 동작을 사용하여 Azure HDInsight에 Solr을 설치하는 방법에 대해 알아봅니다. Solr은 강력한 검색 플랫폼으로서 Hadoop에서 관리하는 데이터에 대한 엔터프라이즈 수준의 검색 기능을 제공합니다. HDInsight 클러스터에 Solr을 설치한 후에는 Solr을 사용하여 데이터를 검색하는 방법도 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계에는 Linux 기반 HDInsight 클러스터가 필요합니다. Windows 기반 클러스터와 함께 Solr을 사용한 작업에 대한 자세한 내용은 [HDInsight Hadoop 클러스터(Windows)에 Solr 설치 및 사용](hdinsight-hadoop-solr-install.md)을 참조하세요.

이 항목에서 사용된 샘플 스크립트로는 특정 구성의 Solr 클러스터가 만들어집니다. 서로 다른 컬렉션, 분할, 스키마, 복제 등으로 Solr 클러스터를 구성하려는 경우 그에 따라 이 스크립트와 Solr 바이너리를 수정해야 합니다.

## <a name="whatis"></a>Solr이란?

[Apache Solr](http://lucene.apache.org/solr/features.html)은 데이터에 대한 강력한 전체 텍스트 검색을 가능하게 해주는 엔터프라이즈 검색 플랫폼입니다. Hadoop는 막대한 양의 데이터를 저장 및 관리할 수 있도록 해주고 Apache Solr은 이 데이터를 신속하게 검색할 수 있는 검색 기능을 제공합니다. 이 항목에서는 HDInsight 클러스터를 사용자 지정하여 Solr을 설치하는 방법에 대한 지침을 제공합니다.

> [AZURE.WARNING] HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> Solr와 같은 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/)).

## 스크립트가 수행하는 작업

이 스크립트는 HDInsight 클러스터에서 다음을 변경합니다.

* `/usr/hdp/current/solr`에 Solr 설치
* Solr 서비스를 실행하는 데 사용하는새 사용자 __solrusr__을 만듭니다.
* __solruser__을 `/usr/hdp/current/solr`의 소유자로 설정합니다.
* 클러스터 노드를 다시 시작하는 경우 Solr를 시작하는 [Upstart](http://upstart.ubuntu.com/) 구성을 추가합니다. 설치 후에 클러스터 노드에서 Solr을 자동으로 시작합니다.

## <a name="install"></a>스크립트 동작을 사용하여 Solr 설치

HDInsight 클러스터에서 Solr을 설치하는 샘플 스크립트는 다음 위치에서 사용할 수 있습니다.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

이 섹션에서는 Azure 포털을 사용하여 새 클러스터를 만들면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE] Azure PowerShell, Azure CLI, HDInsight .NET SDK 또는 Azure Resource Manager 템플릿을 스크립트 동작을 적용하는 데 사용할 수도 있습니다. 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-provision-linux-clusters.md#portal)의 단계를 사용하여 클러스터를 프로비전하되, 완료하지는 않도록 합니다.

2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래 정보를 제공합니다.

	* __이름__: 스크립트 동작의 이름을 입력합니다.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __HEAD__:이 옵션 선택
	* __WORKER__:이 옵션 선택
	* __ZOOKEEPER__: 이 옵션을 선택하여 Zookeeper 노드에 설치합니다.
	* __PARAMETERS__: 이 필드는 공백으로 둡니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 블레이드의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

4. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

## <a name="usesolr"></a>HDInsight에서 Solr을 사용하는 방법

### 인덱싱 데이터

데이터 파일로 Solr을 인덱싱하는 것부터 시작해야 합니다. 그런 다음 인덱싱한 데이터에 대해 Solr을 사용하여 검색 쿼리를 실행할 수 있습니다. 다음 단계를 사용하여 일부 예제 데이터를 Solr에 추가하고 쿼리할 수 있습니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] 이 문서의 이후 단계에서 SSL 터널을 사용하여 Solr 웹 UI에 연결합니다. 다음이 단계를 사용하려면 SSL 터널을 설정하고 브라우저를 구성하여 사용해야 합니다.
	>
	> 자세한 내용은 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

2. 다음 명령을 사용하여 Solr 인덱스 샘플 데이터를 포함합니다.

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	콘솔에 다음 출력이 표시됩니다.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar 유틸리티는 두 개의 샘플 문서 **solr.xml** 및 **monitor.xml**로 Solr을 인덱싱합니다. Solr 내의 __collection1__에 저장됩니다.

3. 다음을 사용하여 Solr에서 노출되는 REST API를 쿼리합니다.

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	__*:*__와 일치하는 문서에 __collection1__에 대한 쿼리를 발급하고(쿼리 문자열에 *%3A*로 인코딩됨) 응답은 JSON으로 반환되어야 합니다. 응답은 다음과 유사합니다.

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Solr 대시보드 사용

Solr 대시보드는 웹 브라우저를 통해 Solr로 작업할 수 있는 웹 UI입니다. Solr 대시보드는 HDInsight 클러스터에서 인터넷에 직접 드러나지 않지만 SSH 터널을 사용하여 액세스되어야 합니다. SSH 터널의 사용에 대한 자세한 내용은 [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

SSH 터널을 설정하면 다음 단계를 수행하여 Solr 대시보드를 사용합니다.

1. 기본 헤드 노드의 호스트 이름을 결정합니다.

    1. SSH를 사용하여 포트 22에 있는 클러스터에 연결합니다. 예를 들어 `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`에서 __USERNAME__은 SSH 사용자 이름이고 __CLUSTERNAME__은 클러스터의 이름입니다.

        SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

        * [Linux, Unix 또는 Mac OS X 클라이언트에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Windows 클라이언트에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. 다음 명령을 사용하여 정규화된 호스트 이름을 가져옵니다.

            hostname -f

        다음과 유사한 이름을 반환합니다.

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        다음 단계에 사용해야 하는 호스트 이름입니다.
    
1. 브라우저에서 __http://HOSTNAME:8983/solr/#/__에 연결하며 여기에서 __HOSTNAME\_\_은 이전 단계에서 결정한 이름입니다.

    이 요청은 HDInsight 클러스터에 대한 헤드 노드로 SSH 터널을 통해 라우팅해야 합니다. 다음과 유사한 결과가 표시됩니다.

	![Solr 대시보드의 이미지](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. 왼쪽 창에서 **코어 선택기** 드롭다운을 사용하여 **collection1**을 선택합니다. 여러 항목이 __collection1__ 아래에 나타나야 합니다.

3. __collection1__ 아래의 항목에서 __쿼리__를 선택합니다. 검색 페이지를 채우려면 다음 값을 사용합니다.

	* **q** 텍스트 상자에 ***:***을 입력합니다. 이렇게 하면 Solr에서 인덱싱되는 문서는 모두 반환됩니다. 문서 내에서 특정 문자열을 검색하려는 경우 여기에 해당 문자열을 입력할 수 있습니다.

	* **wt** 텍스트 상자에서 출력 형식을 선택합니다. 기본값은 **json**입니다.

	마지막으로 검색 페이지의 아래쪽에서 **쿼리 실행** 단추를 선택합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	출력에는 Solr 인덱싱에 사용되는 두 문서가 반환됩니다. 출력은 다음과 유사합니다.

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Solr 시작 및 중지

Solr를 수동으로 중지하거나 시작해야 하는 경우 다음 명령을 사용합니다.

	sudo stop solr

	sudo start solr

## 인덱싱된 데이터 백업

Solr 클러스터 노드에서 인덱싱된 데이터를 Azure Blob 저장소에 백업하는 것이 좋습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 클러스터에 연결하고 다음 명령을 사용하여 헤드 노드에 호스트 이름을 가져옵니다.

        hostname -f
        
2. 다음을 사용하여 인덱싱된 데이터의 스냅숏을 만듭니다. __HOSTNAME__을 이전 명령에서 반환한 이름으로 바꿉니다.

		curl http://HOSTNAME:8983/solr/replication?command=backup

	다음과 같은 응답이 표시됩니다.

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. 다음으로 디렉터리를 __/usr/hdp/current/solr/example/solr__로 변경합니다. 각 컬렉션에 대한 하위 디텍터리가 여기에 있습니다. 컬렉션의 각 디렉터리는 __데이터__ 디렉터리를 포함하고 여기에 해당 컬렉션에 대한 스냅숏이 있습니다.

	예를 들어 이전 단계를 사용하여 샘플 문서를 인덱싱한 경우 __/usr/hdp/current/solr/example/solr/collection1/data__ 디렉터리는 스냅숏의 날짜 및 시간인 #가 있는 __snapshot.###########__라는 디렉터리를 포함해야 합니다.

3. 다음과 비슷한 명령을 사용하여 스냅숏 폴더의 압축된 아카이브를 만듭니다.

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	__snapshot.20150806185338855.tgz__라는 새 아카이브를 만들며 이는 __snapshot.20150806185338855__ 디렉터리의 내용을 포함합니다.

3. 그러면 다음 명령을 사용하여 클러스터의 기본 저장소에 아카이브를 저장할 수 있습니다.

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Solr 스냅숏을 저장하기 위한 전용 디렉터리를 만들 수 있습니다. 예: `hadoop fs -mkdir /solrbackup`.

Solr 백업 및 복원 작업에 대한 자세한 내용은 [SolrCores의 백업 생성 및 복원](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores)을 참조하세요.


## 참고 항목

- [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)입니다. Hue는 기본 저장소에서 HDInsight 클러스터를 쉽게 찾을 뿐만 아니라 Pig 및 Hive 작업을 편리하게 만들고 실행하고 저장할 수 있도록 하는 웹 UI입니다.

- [HDInsight 클러스터에 R 설치][hdinsight-install-r]. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 R을 설치합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.

- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

- [HDInsight 클러스터에서 Hue를 설치](hdinsight-hadoop-hue-linux.md)합니다. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 Hue를 설치합니다. Hue는 Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 응용 프로그램 집합입니다.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->