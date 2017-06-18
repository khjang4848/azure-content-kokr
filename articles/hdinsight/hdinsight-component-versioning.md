<properties
	pageTitle="HDInsight 클러스터에서 제공되는 다양한 구성 요소 | Microsoft Azure"
	description="HDInsight는 여러 Hadoop 클러스터 구성 요소 및 버전을 지원합니다. 이 문서에서는 지원되는 Hadoop 및 HDP(HortonWorks Data Platform) 배포 버전에 대해 알아봅니다."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="jgao"/>


# HDInsight에서 제공되는 다양한 Hadoop 구성 요소

HDInsight에서 제공하는 다양한 서비스 수준과 HDInsight에 포함된 여러 Hadoop 구성 요소의 버전에 대해 설명합니다.

## HDInsight Standard 및 HDInsight Premium

Azure HDInsight는 빅 데이터 클라우드 제품을 **Standard** 및 **Premium**의 두 범주로 제공합니다. 아래 섹션의 표에는 Premium**에서만** 사용 가능한 기능이 나와 있습니다. 표에 명시적으로 나와 있지 않은 기능은 Standard에서 제공됩니다.

>[AZURE.NOTE] HDInsight Premium 제품은 현재 Preview 상태이며 Linux 클러스터에서만 사용할 수 있습니다.

| HDInsight Premium 기능 | 설명 |
|--------------|---------------|
| Microsoft R Server(Preview) | Microsoft R Server는 확장성 있는 R용으로 가장 광범위하게 배포되는 엔터프라이즈급 분석 플랫폼입니다. R 언어는 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능을 지원합니다. HDInsight Premium에서는 이제 R Server를 사용하여 대규모 데이터 집합 및 모델에서 즉시 사용 가능한 HDInsight 클러스터를 만들 수 있습니다. 이 새로운 기능을 통해 데이터 공학자와 통계학자는 번거로운 클러스터 설정 및 유지 관리를 수행하지 않고도 HDInsight를 통해 요청 시 확장 가능한 친숙한 R 인터페이스를 활용할 수 있습니다. <br> <br>자세한 내용은 [HDInsight에서 R Server 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요.

### Premium용으로 지원되는 클러스터 유형

다음 표에는 HDInsight 클러스터 유형 및 Premium 지원 행렬이 나와 있습니다.

| 클러스터 유형 | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop은 | 예 | 예 |
| Spark | 예 | 예 |
| HBase | 예 | 아니요 |
| Storm | 예 | 아니요 |

이 표는 HDInsight Premium에 추가 클러스터 유형이 포함되면 업데이트될 예정입니다.

### 가격 및 SLA

HDInsight Premium의 가격 및 SLA에 대한 자세한 내용은 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## 각 HDInsight 버전에서 제공되는 Hadoop 구성 요소

Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 각 버전을 선택하면 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포에 포함된 구성 요소 집합이 만들어집니다. HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 항목별로 구분되어 있습니다. Azure HDInsight에서 사용하는 기본 클러스터 버전은 현재 3.4이지만, 2016년 9월 14일 시점에서는 HDP 2.4.7에 기반하고 있습니다.

> [AZURE.NOTE] 서비스의 기본 버전은 예고 없이 변경 될 수 있습니다. 버전 종속성이 있는 경우 .NET SDK/Azure PowerShell 및 Azure CLI를 사용하여 클러스터를 만들 때 버전을 지정하는 것이 좋습니다.

구성 요소|HDInsight 버전 3.4(기본값) | HDInsight 버전 3.3 | HDInsight 버전 3.2 |HDInsight 버전 3.1 |HDInsight 버전 3.0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2\.0|
Apache Hadoop & YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive & HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0(Linux만 해당)|1\.5.2(Linux만 해당/ 실험적 빌드)|1\.3.1(Windows만 해당)|||


**현재 구성 요소 버전 정보 가져오기**

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 HDInsight에 대한 향후 업데이트에서 변경될 수 있습니다. 사용 가능한 구성 요소를 알아보고 사용 중인 버전을 확인하는 방법은 클러스터에서 Ambari REST API를 사용하는 것입니다. 또한 **GetComponentInformation** 명령을 사용하여 서비스 구성 요소에 대한 정보를 검색할 수도 있습니다. 자세한 내용은 [Ambari 설명서][ambari-docs](영문)를 참조하세요. 이 정보를 가져오는 다른 방법은 원격 데스크톱을 사용하여 클러스터에 로그인하고 "C:\\apps\\dist" 디렉터리의 콘텐츠를 직접 검토하는 것입니다.


**릴리스 정보**

최신 HDInsight 버전과 관련된 추가 릴리스 정보에 대해서는 [HDInsight 릴리스 정보](hdinsight-release-notes.md)를 참조하세요.


## 지원되는 HDInsight 버전
다음 표는 현재 사용 가능한 HDInsight 버전, 버전에서 사용하는 해당 Hortonworks Data Platform 버전 및 릴리스 날짜를 나열합니다. 알려진 경우 지원 만료 날짜와 사용이 중단된 날짜도 나와 있습니다. 다음 사항에 유의하세요.

* 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 2.1 이상에 대해 배포됩니다. HDInsight 1.6 클러스터에서는 사용할 수 없습니다.
* 특정 버전에 대한 지원이 만료되면 Azure 포털을 통해 사용할 수 없게 됩니다. 다음 표에는 Azure 클래식 포털에서 사용할 수 있는 버전이 나와 있습니다. 클러스터 버전은 사용이 중단될 때까지 Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) 명령의 `Version` 매개 변수 및 .NET SDK를 사용하여 계속 사용할 수 있습니다.

HDInsight 버전|HDP 버전|VM OS|고가용성|릴리스 날짜|Azure 포털에서 사용 가능 여부|지원 만료 날짜|사용 중단 날짜
---|---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|예|2016/03/29|예||
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS 또는 Windows Server 2012R2|예|12/02/2015|예|06/27/2016|07/31/2017
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS 또는 Windows Server 2012R2|예|2/18/2015|예|3/1/2016|04/01/2017
HDI 3.1|HDP 2.1|Windows Server 2012R2|예|6/24/2014|아니요|05/18/2015|06/30/2016
HDI 3.0|HDP 2.0|Windows Server 2012R2|예|2014/02/11|아니요|2014/09/17|2015/06/30
HDI 2.1|HDP 1.3|Windows Server 2012R2|예|2013/10/28|아니요|2014/05/12|2015/05/31
HDI 1.6|HDP 1.1||아니요|2013/10/28|아니요|2014/04/26|2015/05/31

**기본이 아닌 클러스터의 배포**

### HDInsight 클러스터 버전의 서비스 수준 계약

SLA는 "지원 기간" 면에서 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 클러스터 버전을 지원하는 기간을 나타냅니다. HDInsight 클러스터는 해당 버전의 **지원 만료 날짜**가 현재 날짜를 지난 경우 지원 기간을 넘긴 것입니다. 지원되는 HDInsight 클러스터 버전 목록은 위의 표에서 찾아볼 수 있습니다. 새로운 X+1 버전이 사용 가능해질 경우 지정된 HDInsight 버전의 지원 만료 날짜는 다음 수식 중 후자로 계산됩니다.

- 수식 1: HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
- 수식 2: HDInsight 클러스터 버전 X+1(X 이후의 후속 버전)을 포털에서 사용할 수 있는 날짜에 90일을 추가합니다.

**사용 중단 날짜**는 HDInsight에서 해당 클러스터 버전을 만들 수 없게 되는 날짜입니다.

> [AZURE.NOTE] Windows 기반 HDInsight 클러스터(버전 2.1, 3.0, 3.1, 3.2 및 3.3 포함)는 Azure Guest OS Family 4에서 실행되고 64 비트 버전의 Windows Server 2012 R2를 사용하고 .NET Framework 4.0, 4.5, 4.5.1 및 4.5.2를 지원합니다.

## HDInsight 버전과 관련된 Hortonworks 릴리스 정보##

* HDInsight 클러스터 버전 3.4에서는 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)를 기반으로 하는 Hadoop 배포를 사용합니다. 이는 포털을 사용할 때 만든 **기본** Hadoop 클러스터입니다.



* HDInsight 클러스터 버전 3.3에서는 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)(영문)를 기반으로 하는 Hadoop 배포를 사용합니다.
	* Apache Storm 릴리스 정보는 [여기](https://storm.apache.org/2015/11/05/storm0100-released.html)에 사용할 수 있습니다.
	* Apache Hive 릴리스 정보는 [여기](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)에 사용할 수 있습니다.

* HDInsight 클러스터 버전 3.2에서는 [Hortonworks Data Platform 2.2][hdp-2-2](영문)를 기반으로 하는 Hadoop 배포를 사용합니다.

	* Release notes for specific Apache components - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* HDInsight 클러스터 버전 3.1은 [Hortonworks Data Platform 2.1.7][hdp-2-1-7]을 기반으로 하는 Hadoop 배포를 사용합니다. 2014년 11월 7일 이전에 만들어진 HDInsight 3.1 클러스터는 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]에 기반합니다.

* HDInsight 클러스터 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8](영문)를 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 클러스터 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0](영문)를 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 클러스터 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0](영문)를 기반으로 하는 Hadoop 배포를 사용합니다.


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0921_2016-->