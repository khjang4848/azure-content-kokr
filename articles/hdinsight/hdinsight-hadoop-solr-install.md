<properties
	pageTitle="스크립트 작업을 사용하여 Hadoop 클러스터에 Solr 설치 | Microsoft Azure"
	description="스크립트 작업을 사용하여 Solr로 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# HDInsight Hadoop 클러스터에서 Solr 설치 및 사용

스크립트 동작을 사용하여 Solr로 Windows 기반 HDInsight 클러스터를 사용자 지정하는 방법 및 데이터를 검색하기 위해 Solr를 사용하는 방법을 알아봅니다. Linux 기반 클러스터와 함께 Solr을 사용한 작업에 대한 자세한 내용은 [HDInsight Hadoop 클러스터에 Solr 설치 및 사용(Linux)](hdinsight-hadoop-solr-install-linux.md)을 참조하세요.
 
*스크립트 작업*을 사용하여 Azure HDInsight에서 모든 형식의 클러스터(Hadoop, Storm, HBase, Spark)에 Solr을 설치할 수 있습니다. HDInsight 클러스터에 Solr을 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1))에서 다운로드할 수 있습니다.

샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

이 항목에서 사용된 샘플 스크립트로는 특정 구성의 Windows 기반 Solr 클러스터가 만들어집니다. 서로 다른 컬렉션, 분할, 스키마, 복제 등으로 Solr 클러스터를 구성하려는 경우 그에 따라 이 스크립트와 Solr 바이너리를 수정해야 합니다.

**관련된 문서**

- [HDInsight Hadoop 클러스터에서 Solr 설치 및 사용(Linux)](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md).


## Solr이란?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a>은 데이터에 대한 강력한 전체 텍스트 검색을 가능하게 해주는 엔터프라이즈 검색 플랫폼입니다. Hadoop는 막대한 양의 데이터를 저장 및 관리할 수 있도록 해주고 Apache Solr은 이 데이터를 신속하게 검색할 수 있는 검색 기능을 제공합니다.

## 포털을 사용하여 Solr 설치

1. [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md#portal)에서 설명한 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터를 만들기 시작합니다.
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다. 예를 들면 <b>Install Solr</b>과 같습니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI(Uniform Resource Identifier)를 지정합니다. 예를 들면 <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i>과 같습니다.</td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Solr을 설치하는 스크립트에는 매개 변수가 필요하지 않으므로 비워 둘 수 있습니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 만들기를 시작합니다.


## Solr 사용

데이터 파일로 Solr을 인덱싱하는 것부터 시작해야 합니다. 그런 다음 인덱싱한 데이터에 대해 Solr을 사용하여 검색 쿼리를 실행할 수 있습니다. HDInsight 클러스터에서 Solr을 사용하려면 다음 단계를 수행하세요.

1. **RDP(원격 데스크톱 프로토콜)를 사용하여 Solr이 설치된 HDInsight 클러스터에 원격으로 연결**합니다. Azure 포털에서 Solr을 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 원격으로 연결합니다. 지침은 [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)을 참조하세요.

2. **데이터 파일을 업로드하여 Solr을 인덱싱**합니다. Solr을 인덱싱할 때 검색이 필요할 수 있는 문서를 포함시킵니다. Solr을 인덱싱하려면 RDP를 사용하여 클러스터에 원격으로 연결한 후 Hadoop 명령줄을 열고 **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**로 이동합니다. 다음 명령을 실행합니다.

		java -jar post.jar solr.xml monitor.xml

	콘솔에 다음 출력이 표시됩니다.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar 유틸리티는 두 개의 샘플 문서 **solr.xml** 및 **monitor.xml**로 Solr을 인덱싱합니다. post.jar 유틸리티와 샘플 문서는 Solr 설치에서 사용할 수 있습니다.

3. **Solr 대시보드를 사용하여 인덱싱된 문서 내에서 검색**합니다. HDInsight 클러스터에 대한 RDP 세션에서 Internet Explorer를 열고 **http://headnodehost:8983/solr/#/**에서 Solr 대시보드를 실행합니다. 왼쪽 창의 **Core Selector** 드롭다운에서 **collection1**을 선택하고, 그 안에서 **Query**를 클릭합니다. 한 예로, Solr의 모든 문서를 선택하고 반환하려면 다음 값을 제공하세요.

	* **q** 텍스트 상자에 ***:***을 입력합니다. 이렇게 하면 Solr에서 인덱싱되는 문서는 모두 반환됩니다. 문서 내에서 특정 문자열을 검색하려는 경우 여기에 해당 문자열을 입력할 수 있습니다.
	
	* **wt** 텍스트 상자에서 출력 형식을 선택합니다. 기본값은 **json**입니다. **Execute Query**를 클릭합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Solr 대시보드에서 쿼리 실행")
	
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


4. **권장: Solr에서 인덱싱된 데이터를 HDInsight 클러스터와 연결된 Azure Blob 저장소에 백업합니다**. Solr 클러스터 노드에서 인덱싱된 데이터를 Azure Blob 저장소에 백업하는 것이 좋습니다. 이렇게 하려면 다음 단계를 수행합니다.

	1. RDP 세션에서 Internet Explorer를 열고 다음 URL을 가리킵니다.

			http://localhost:8983/solr/replication?command=backup

		다음과 같은 응답이 표시됩니다.

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. 원격 세션에서 {SOLR\_HOME}\\{Collection}\\data로 이동합니다. 샘플 스크립트를 통해 만든 클러스터의 경우 이 경로는 **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**입니다. 이 위치에 **snapshot.*timestamp***와 유사한 이름으로 만든 스냅숏 폴더가 표시됩니다.

	3. 스냅숏 폴더를 압축하고 Azure Blob 저장소에 업로드합니다. Hadoop 명령줄에서 다음 명령을 사용하여 스냅숏 폴더의 위치로 이동합니다.

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		이 명령은 스냅숏을 클러스터와 연결된 기본 저장소 계정 내의 컨테이너 아래에 있는 /example/data/에 복사합니다.

## Aure PowerShell을 사용하여 Solr 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)을 참조하세요. 샘플은 Azure PowerShell을 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)을 사용해야 합니다.

## .NET SDK를 사용하여 Solr 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)을 참조하세요. 샘플은 .NET SDK를 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)을 사용해야 합니다.



## 참고 항목

- [HDInsight Hadoop 클러스터에서 Solr 설치 및 사용(Linux)](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]\: Spark 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 R 설치][hdinsight-install-r]\: R 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 Giraph 설치](hdinsight-hadoop-giraph-install.md): Giraph 설치에 대한 스크립트 작업 샘플입니다.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0914_2016-->