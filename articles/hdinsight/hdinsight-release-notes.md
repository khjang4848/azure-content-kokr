<properties
	pageTitle="Azure HDInsight에서 Hadoop 구성 요소에 대한 릴리스 정보 | Microsoft Azure"
	description="Azure HDInsight용 Hadoop 구성 요소의 최신 릴리스 정보 및 버전입니다. Hadoop, Apache Storm 및 HBase에 대한 개발 팁 및 세부 정보를 가져옵니다."
	services="hdinsight"
	documentationCenter=""
	editor="cgronlun"
	manager="jhubbard"
	authors="nitinme"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="nitinme"/>


# Azure HDInsight에서 Hadoop 구성 요소에 대한 릴리스 정보

## HDInsight에 대한 R Server의 2016/08/30 릴리스 정보

이 릴리스와 함께 배포된 Linux 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |Ambari 빌드 |
|----|----------------------|----|------------|-------------|
|3\.2 |3\.2.1000.0.8268980 |2\.2 |2\.2.9.1-19 |2\.2.1.12-4 |
|3\.3 |3\.3.1000.0.8268980 |2\.3 |2\.3.3.1-25 |2\.2.1.12-4 |
|3\.4 |3\.4.1000.0.8269383 |2\.4 |2\.4.2.4-5 |2\.2.1.12-4 |

이 릴리스와 함께 배포된 Windows 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.1033.2559206 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.1033.2559206 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.1033.2559206 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.1033.2559206 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.1033.2559206 |2\.3 |2\.3.3.1-25 |

## HDInsight에 대한 R Server의 2016/08/17 릴리스 정보

- R Server 8.0.5 – 주로 버그 수정 릴리스입니다. 자세한 정보는 [R Server 릴리스 정보](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes)를 참조하세요.
- 에지 노드에 대한 AzureML 패키지 – [이 R 패키지](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)를 통해 R 모델을 게시하고 Azure ML 웹 서비스로 사용할 수 있습니다. 자세한 정보는 ["HDInsight의 R 서버 개요"](hdinsight-hadoop-r-server-overview.md) 문서의 ["모델 운영"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) 섹션을 참조하세요.
- [상위 100개 가장 인기 있는 R 패키지](https://github.com/metacran/cranlogs)의 Linux 종속성 - 해당 Linux 패키지 종속성은 사전 설치되어 있습니다.
- R 패키지를 데이터 노드에 추가할 때 CRAN 리포지토리를 사용하는 옵션입니다. 자세한 정보는 ["HDInsight에서 R 서버 사용 시작"](hdinsight-hadoop-r-server-get-started.md) 문서의 ["R 패키지 설치"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) 섹션을 참조하세요.
- 클러스터가 만들어질 때 R 서버 프로비저닝의 안정성이 개선되었습니다.


## HDInsight의 2016/08/01 릴리스 정보

이 릴리스와 함께 배포된 Linux 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |Ambari 빌드 |
|----|----------------------|----|------------|-------------|
|3\.2 |3\.2.1000.0.8028416 |2\.2 |2\.2.9.1-19 |2\.2.1.12-4 |
|3\.3 |3\.3.1000.0.8028416 |2\.3 |2\.3.3.1-25 |2\.2.1.12-4 |
|3\.4 |3\.4.1000.0.8053402 |2\.4 |2\.4.2.4-5 |2\.2.1.12-4 |

이 릴리스와 함께 배포된 Windows 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.1005.2488842 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.1005.2488842 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.1005.2488842 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.1005.2488842 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.1005.2488842 |2\.3 |2\.3.3.1-25 |

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Spark, Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDInsight 3.4 클러스터에 대한 변경 내용 | 성능 향상을 위해 다음과 같은 hive 구성에 대한 기본값이 변경되었습니다. <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| 부여 | 모두| 해당 없음|
| 이 릴리스에서 다음 수정 사항이 포함됨 | HIVE-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| 부여 | 모두| 해당 없음

## HDInsight의 2016/07/14 릴리스 정보

이 릴리스와 함께 배포된 Linux 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |Ambari 빌드 |
|----|----------------------|----|------------|-------------|
|3\.2 |3\.2.1000.0.7932505 |2\.2 |2\.2.9.1-11 |2\.2.1.12-2 |
|3\.3 |3\.3.1000.0.7932505 |2\.3 |2\.3.3.1-18 |2\.2.1.12-2 |
|3\.4 |3\.4.1000.0.7933003 |2\.4 |2\.4.2.0 |2\.2.1.12-2 |

이 릴리스와 함께 배포된 Windows 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.989.2441725 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.989.2441725 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.989.2441725 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.989.2441725 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.989.2441725 |2\.3 |2\.3.3.1-21 |

## HDInsight의 2016/07/07 릴리스 정보

이 릴리스와 함께 배포된 Linux 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |
|----|----------------------|----|------------|
|3\.2 |3\.2.1000.0.7864996 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.1000.0.7864996 |2\.3 |2\.3.3.1-18 |
|3\.4 |3\.4.1000.0.7861906 |2\.4 |2\.4.2.0 |

이 릴리스와 함께 배포된 Windows 기반 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDI |HDI 클러스터 버전 |HDP |HDP 빌드 |
|----|----------------------|----|--------------|
|2\.1 |2\.1.10.977.2413853 |1\.3 |1\.3.12.0-01795|
|3\.0 |3\.0.6.977.2413853 |2\.0 |2\.0.13.0-2117 |
|3\.1 |3\.1.4.977.2413853 |2\.1 |2\.1.16.0-2374 |
|3\.2 |3\.2.7.977.2413853 |2\.2 |2\.2.9.1-11 |
|3\.3 |3\.3.0.977.2413853 |2\.3 |2\.3.3.1-21 |

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Spark, Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [IntelliJ용 HDInsight 도구](hdinsight-apache-spark-intellij-tool-plugin.md) | 이제 HDInsight Spark 클러스터용 IntelliJ IDEA 플러그 인은 IntelliJ용 Azure 도구 키트와 통합됩니다. Azure SDK v2.9.1, 최신 Java SDK를 지원하며 IntelliJ용 독립 실행형 HDInsight 플러그 인의 모든 기능이 포함됩니다.| 도구 | Spark| 해당 없음|
| [Eclipse용 HDInsight 도구](hdinsight-apache-spark-eclipse-tool-plugin.md) | Eclipse용 Azure 도구 키트는 이제 HDInsight Spark 클러스터를 지원합니다. 다음과 같은 기능을 제공합니다. <ul><li>IntelliSense에 대한 첫 번째 클래스 작성 지원, 자동 서식, 오류 검사 등을 사용하여 Scala 및 Java로 Spark 응용 프로그램을 만들고 쓸 수 있습니다.</li><li>Spark 응용 프로그램을 로컬로 테스트할 수 있습니다.</li><li>HDInsight Spark 클러스터로 작업을 전송하고 결과를 검색할 수 있습니다.</li><li>Azure에 로그인하고 Azure 구독에 연결된 모든 Spark 클러스터에 액세스할 수 있습니다.</li><li>HDInsight Spark 클러스터의 모든 연결된 저장소 리소스를 탐색할 수 있습니다.</li></ul>| 도구 | Spark| 해당 없음

이 릴리스부터 Linux 기반 HDInsight 클러스터에 대한 게스트 OS 패치 정책을 변경했습니다. 새 정책의 목표는 패치로 인해 부팅 횟수를 크게 줄이는 것입니다. 새 정책은 월요일 또는 목요일 오전 12시(UTC)마다 시차를 두고 모든 지정된 클러스터의 노드에 있는 Linux 클러스터에서 계속 VM(가상 컴퓨터)을 패치합니다. 그러나 지정된 VM은 게스트 OS 패치로 인해 최대 30일마다 다시 부팅됩니다. 또한 새로 만든 클러스터는 생성된 날짜로부터 30일보다 이전에 첫 번째로 다시 부팅되지 않습니다.

>[AZURE.NOTE] 이러한 변경 내용은 이 릴리스 버전 이상인 새로 만든 클러스터에만 적용됩니다.

## HDInsight의 2016/06/06 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

|HDP |HDI 버전 |Spark 버전 |Ambari 빌드 번호 |HDP 빌드 번호|
|-------|---------------|---------------|-----------------------|----------------|
|2\.3 |3\.3.1000.0.7702215|	1\.5.2|	2\.2.1.8-2|	2\.3.3.1-18|
|2\.4 |3\.4.1000.0.7702224|	1\.6.1|	2\.2.1.8-2|	2\.4.2.0|


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Spark, Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDInsight의 Spark가 일반 공급됨 | 이 릴리스는 HDInsight의 오픈 소스 Apache Spark에 향상된 가용성, 확장성 및 생산성을 제공합니다. <ul><li>까다로운 엔터프라이즈 워크로드에 적합한 업계 최고의 99.9% 가용성 SLA</li><li>Azure Data Lake Store를 사용하는 확장 가능한 저장소 계층</li><li>데이터 탐색 및 개발의 모든 단계를 위한 생산성 도구 사용자 지정된 Spark 커널이 있는 Jupyter Notebook은 대화형 데이터 탐색을 가능하게 하고, Power BI, Tableau 및 Qlik과 같은 BI 대시보드와 통합되어 빠른 데이터 공유 및 지속적인 보고에 적합하며, IntelliJ 플러그 인은 장기적인 코드 아티팩트 개발 및 디버깅을 위한 신뢰할 수 있는 옵션입니다.</li></ul>| 부여 | Spark| 해당 없음|
| IntelliJ용 HDInsight 도구 | HDInsight Spark 클러스터용 IntelliJ IDEA 플러그 인입니다. 다음과 같은 기능을 제공합니다.<ul><li>IntelliSense에 대한 첫 번째 클래스 작성 지원, 자동 서식, 오류 검사 등을 사용하여 Scala 및 Java로 Spark 응용 프로그램을 만들고 쓸 수 있습니다.</li><li>Spark 응용 프로그램을 로컬로 테스트할 수 있습니다.</li><li>HDInsight Spark 클러스터로 작업을 전송하고 결과를 검색할 수 있습니다.</li><li>Azure에 로그인하고 Azure 구독에 연결된 모든 Spark 클러스터에 액세스할 수 있습니다.</li><li>HDInsight Spark 클러스터의 모든 연결된 저장소 리소스를 탐색할 수 있습니다.</li><li>HDInsight Spark 클러스터에 대한 모든 작업 기록 및 작업 정보를 탐색할 수 있습니다.</li><li>데스크톱 컴퓨터에서 원격으로 Spark 작업을 디버그할 수 있습니다.</li></ul>| 도구 | Spark| 해당 없음

## HDInsight의 2016/05/13 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.875.2159884(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.875.2159884(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.922.2266903(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.922.2266903(HDP 2.2.9.1-11)
* HDInsight(Windows) 3.3.0.922.2266903(HDP 2.3.3.1-18)
* HDInsight(Linux) 3.2.1000.0.7565644(HDP 2.2.9.1-11)
* HDInsight(Linux) 3.3.1000.0.7565644(HDP 2.3.3.1-18)
* HDInsight(Linux) 3.4.1000.0.7548380(HDP 2.4.2.0)

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Spark, Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Spark 버전 업데이트 및 기타 버그 수정 | 이 릴리스에서는 HDInsight 클러스터의 Spark 버전을 1.6.1로 업데이트하고 기타 버그를 수정합니다.| 부여 | Spark| 해당 없음

## HDInsight의 2016/04/11 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.889.2191206(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.889.2191206(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.889.2191206(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.889.2191206(HDP 2.2.9.1-10)
* HDInsight(Windows) 3.3.0.889.2191206(HDP 2.3.3.1-16 -변경되지 않음)
* HDInsight(Linux) 3.2.1000.0.7339916(HDP 2.2.9.1-10)
* HDInsight(Linux) 3.3.1000.0.7339916(HDP 2.3.3.1-16)
* HDInsight(Linux) 3.4.1000.0.7338911(HDP 2.4.1.1-3)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDI 3.4에 대한 사용자 지정 Metastore 업그레이드 문제 | 다른 HDInsight 클러스터의 더 낮은 버전에서 이전에 사용한 사용자 지정 Metastore를 사용 하는 경우 클러스터 생성이 실패할 수 있습니다. 이렇게 되는 이유는 업그레이드 스크립트 오류 때문이었으며 지금은 해결됨| 클러스터 만들기 | 모두 | 해당 없음
| Livy Crash 복구 | Livy를 통해 제출된 모든 작업에 대한 작업 상태 복원력 제공 | 안정성 | Linux에서의 Spark| 해당 없음
| Jupyter 콘텐츠 HA | 클러스터와 연결된 저장소 계정에 대해 Jupyter 노트북 콘텐츠를 저장 및 로드하는 기능을 제공합니다. 자세한 내용은 [Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요.| 노트북 | Linux에서의 Spark| 해당 없음
| Jupter 노트북에서 HiveContext 제거 | `%%hive` 매직 대신 `%%sql` 매직을 사용합니다. SqlContext는 hiveContext와 같습니다. 자세한 내용은 [Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요.| 노트북 | Linux에서 Spark 클러스터| 해당 없음
| 이전 Spark 버전의 사용 중단 | 5/31에 이전 버전 Spark 1.3.1이 서비스에서 제거됩니다. | 부여 | Windows에서 Spark 클러스터 | 해당 없음

## HDInsight의 2016/03/29 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.875.2159884(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.875.2159884(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.875.2159884(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.875.2159884(HDP 2.2.9.1-7 - 변경되지 않음)
* HDInsight(Windows) 3.3.0.875.2159884(HDP 2.3.3.1-16)
* HDInsight(Linux) 3.2.1000.0.7193255(HDP 2.2.9.1-8 - 변경되지 않음)
* HDInsight(Linux) 3.3.1000.0.7193255(HDP 2.3.3.1-7 - 변경되지 않음)
* HDInsight(Linux) 3.4.1000.0.7195842(HDP 2.4.1.0-327)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 3.4 버전 추가 및 HDP 버전 업데이트됨 | 이 릴리스에서 HDInsight v3.4(HDP 2.4에 기반)을 추가하고 다른 HDP 버전도 업데이트했습니다. HDP 2.4 릴리스 정보는 [여기](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)에서 확인할 수 있고 HDInsight 버전에 대한 자세한 정보는 [여기](hdinsight-component-versioning.md)에서 확인할 수 있습니다.| 부여 | 모든 Linux 클러스터| 해당 없음
| HDInsight 프리미엄 | HDInsight는 현재 표준 및 프리미엄이라는 두 가지 범주로 제공됩니다. HDInsight 프리미엄은 현재 Preview 상태이며 Linux에서 Hadoop 및 Spark 클러스터에서만 사용할 수 있습니다. 자세한 내용은 [여기](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)를 참조하세요.| 부여 | Linux에서 Hadoop 및 Spark| 해당 없음
| Microsoft R 서버 | HDInsight 프리미엄은 Linux의 Hadoop 및 Spark 클러스터에 포함할 수 있는 Microsoft R 서버를 제공합니다. 자세한 내용은 [HDInsight에서 R Server 개요](hdinsight-hadoop-r-server-overview.md)를 참조하세요.| 부여 | Linux에서 Hadoop 및 Spark| 해당 없음
| Spark 1.6.0 | HDInsight 3.4 클러스터에 Spark 1.6.0이 포함됨| 부여 | Linux에서 Spark 클러스터| 해당 없음
| Jupyter 노트북 기능 향상 | Spark 클러스터와 함께 사용할 수 있는 Jupyter 노트북은 이제 추가 Spark 커널을 제공합니다. 또한 %%magic의 사용, 자동 시각화 및 Python 시각화 라이브러리(예: (matplotlib)와 통합과 같은 향상된 기능도 포함합니다. 자세한 내용은 [Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)을 참조하세요. | 부여 | Linux에서 Spark 클러스터 | 해당 없음

## HDInsight의 2016/03/22 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.875.2159884(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.875.2159884(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.875.2159884(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.875.2159884(HDP 2.2.9.1-7 - 변경되지 않음)
* HDInsight(Windows) 3.3.0.875.2159884(HDP 2.3.3.1-16)
* HDInsight(Linux) 3.2.1000.0.7193255(HDP 2.2.9.1-8 - 변경되지 않음)
* HDInsight(Linux) 3.3.1000.0.7193255(HDP 2.3.3.1-7 - 변경되지 않음)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨 | 이 릴리스에서는 모든 HDInsight 클러스터에 대해 HDInsight 버전을 업데이트함| 부여 | 모두| 해당 없음


## HDInsight의 2016/03/10 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.859.2123216(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.859.2123216(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.859.2123216(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.859.2123216(HDP 2.2.9.1-7)
* HDInsight(Windows) 3.3.0.859.2123216(HDP 2.3.3.1-5 - 변경되지 않음)
* HDInsight(Linux) 3.2.1000.7076817(HDP 2.2.9.1-8)
* HDInsight(Linux) 3.3.1000.7076817(HDP 2.3.3.1-7)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨 | 이 릴리스에서는 모든 HDInsight 클러스터에 대해 HDInsight 버전을 업데이트함| 부여 | 모두| 해당 없음

## HDInsight의 2016/01/27 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.817.2028315(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.817.2028315(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.817.2028315(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.817.2028315(HDP 2.2.9.1-1)
* HDInsight(Windows) 3.3.0.817.2028315(HDP 2.3.3.1-5 - 변경되지 않음)
* HDInsight(Linux) 3.2.1000.4072335(HDP 2.2.9.1-1)
* HDInsight(Linux) 3.3.1000.4072335(HDP 2.3.3.1-1)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨 | 이 릴리스에서는 모든 HDInsight 클러스터에 대해 HDInsight 버전을 업데이트함| 부여 | 모두| 해당 없음

## HDInsight의 2015/12/02 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.763.1931434(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.763.1931434(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.763.1931434(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.763.1931434(HDP 2.2.7.1-34 - 변경되지 않음)
* HDInsight(Windows) 3.3.1000.0(HDP 2.3.3.1-5)
* HDInsight(Linux) 3.2.1000.0.6392801(HDP 2.2.7.1-34 - 변경되지 않음)
* HDInsight(Linux) 3.3.1000.0(HDP 2.3.3.0-3039)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 3.3 버전 추가 및 HDP 버전 업데이트됨 | 이 릴리스에서 HDInsight v3.3(HDP 2.3에 기반)을 추가하고 다른 HDP 버전도 업데이트했습니다. HDP 2.3 릴리스 정보는 [여기](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)에서 확인할 수 있고 HDInsight 버전에 대한 자세한 정보는 [여기](hdinsight-component-versioning.md)에서 확인할 수 있습니다.| 부여 | 모두| 해당 없음

## HDInsight의 2015/11/30 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.757.1923908(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.757.1923908(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.757.1923908(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.757.1923908(HDP 2.2.7.1-34)
* HDInsight(Linux) 3.2.1000.0.6392801(HDP 2.2.7.1-34)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터의 업데이트된 HDInsight 버전 및 HDInsight 3.2 클러스터의 HDP 버전(Windows 및 Linux) | 이 릴리스에서 HDInsight 및 HDP 버전이 업데이트됨 | 부여 | 모두| 해당 없음


## HDInsight의 2015/10/27 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight(Windows) 2.1.10.726.1866228(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight(Windows) 3.0.6.726.1866228(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight(Windows) 3.1.4.726.1866228(HDP 2.1.15.0-2374 - 변경되지 않음)
* HDInsight(Windows) 3.2.7.726.1866228(HDP 2.2.7.1-33)
* HDInsight(Linux) 3.2.1000.0.6035701(HDP 2.2.7.1-33)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨(Windows 및 Linux) | 이 릴리스에서 HDInsight 및 HDP 버전이 업데이트됨 | 부여 | 모두| 해당 없음
| 대문자 클러스터를 사용하여 Windows Spark 클러스터에 대한 Jupyter 수정됨 | 대문자로 지정한 DNS 이름을 가진 클러스터는 원본 요청 검사로 인해 Jupyter Notebook에 문제가 있었습니다. 수정 사항은 Jupyter의 구성에 대한 DNS 이름을 소문자로 변경하는 것이었습니다. | 부여 | HDInsight Spark(Windows)| 해당 없음


## HDInsight 10/20/2015 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 기본 HDP 버전 HDP 2.2로 변경됨 | HDInsight Windows 클러스터의 기본 버전이 HDP 2.2로 변경됩니다. HDInsight 버전 3.2(HDP 2.2)는 2015년 2월 이후 일반에서 사용할 수 있습니다. 이 변경에서는 Azure 포털, PowerShell cmdlet 또는 SDK를 사용하여 프로비전하는 동안 명시적 선택이 없을 때 기본 클러스터 버전만 앞으로 이동합니다. | 부여 | 모두| 해당 없음 |
|단일 가상 네트워크의 Linux 클러스터에서 여러 HDInsight 배포를 위한 VM 이름 형식 변경 | 단일 가상 네트워크에서의 여러 HDInsight Linux 클러스터 배포에 대한 지원이 이 릴리스에 추가되었습니다. 이러한 지원의 일환으로, 클러스터의 가상 컴퓨터 이름 형식이 headnode*, workernode* 및 zookeepernode*에서 각각 hn*, wn* 및 zk*로 변경되었습니다. 이 내용은 변경될 수 있으므로 가상 컴퓨터 이름의 형식에 직접적인 종속성을 만들지 않는 것이 좋습니다. 로컬 컴퓨터나 Ambari API에서 "hostname -f"를 사용하여 호스트 목록과 호스팅 대상 구성 요소의 매핑을 판단합니다. 자세한 내용은[https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) 및 [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md)에서 제공합니다. | 부여 | Linux 기반 HDInsight 클러스터 | 해당 없음 |
| 구성 변경 내용 | HDInsight 3.1 클러스터의 경우 이제 <ul><li>tez.yarn.ats.enabled 및yarn.log.server.url 구성을 사용할 수 있습니다. 이렇게 하면 응용 프로그램 타임라인 서버 및 로그 서버가 로그를 채울 수 있습니다.</li></ul>HDInsight 3.2 클러스터의 경우 <ul><li>mapreduce.fileoutputcommitter.algorithm.version 구성이 2로 설정되었습니다. 이를 통해 FileOutputCommitter의 V2 버전을 사용할 수 있게 됩니다.</li></ul> | 부여 | 모두 | 해당 없음 |


## HDInsight의 2015/09/09 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.675.1768697(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.675.1768697(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.675.1768697(HDP 2.1.15.0-2334 - 변경되지 않음)
* HDInsight 3.2.6.675.1768697(HDP 2.2.6.1-0012 - 변경되지 않음)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨 | 이 릴리스에서 HDInsight 버전이 업데이트됨 | 부여 | 모두| 해당 없음 |

## HDInsight의 2015/07/31 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.640.1695824(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.640.1695824(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.640.1695824(HDP 2.1.15.0-2334 - 변경되지 않음)
* HDInsight 3.2.6.640.1695824(HDP 2.2.6.1-0012 - 변경되지 않음)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Spark 클러스터 노드 다시 이미징 워크플로 수정 | 다시 이미징 후 Spark 클러스터 노드를 복구할 수 없도록 만드는 버그 수정 | 부여 | Spark| 해당 없음 |


## HDInsight의 2015/07/31 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.635.1684502(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.635.1684502(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.635.1684502(HDP 2.1.15.0-2334 - 변경되지 않음)
* HDInsight 3.2.6.635.1684502(HDP 2.2.6.1-0012 - 변경되지 않음)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 모든 HDInsight 클러스터에 대해 HDInsight 버전 업데이트됨 | 이 릴리스에서 HDInsight 버전이 업데이트됨 | 부여 | 모두| 해당 없음 |


## HDInsight의 2015/07/07 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.610.1630216(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.610.1630216(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.610.1630216(HDP 2.1.15.0-2334 - 변경되지 않음)
* HDInsight 3.2.4.610.1630216(HDP 2.2.6.1-0012)
* SDK 1.5.8


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

| 제목 | 설명 | 영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK) | 클러스터 유형(예: Hadoop, HBase 또는 Storm) | JIRA(적용 가능한 경우) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDInsight 3.2 클러스터에 대한 업데이트된 HDP 버전 | 이 릴리스에서는 HDInsight 3.2가 HDP 2.2.6.1-0012를 배포함 | 부여 | 모두 | 해당 없음 |


## HDInsight의 2015/06/26 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.601.1610731(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.601.1610731(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.601.1610731(HDP 2.1.15.0-2334 - 변경되지 않음)
* HDInsight 3.2.4.601.1610731(HDP 2.2.6.1-0011)
* SDK 1.5.8


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>HDInsight 3.2 클러스터에 대한 업데이트된 HDP 버전</td>
<td>이 릴리스에서는 HDInsight 3.2가 HDP 2.2.6.1을 배포함</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

</table>

## HDInsight의 2015/06/18 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>열린 추가 HTTPS 포트</td>
<td>이제 클라우드 서비스는 클러스터에서 8001에서 8005까지의 5개의 포트를 엽니다(예: https://<clustername>.azurehdinsight.net:8001/). 이 URL에 대한 요청은 포트 443으로 동일한 기본 인증 암호 메커니즘을 사용하여 인증됩니다. 이 포트는 활성 헤드 노트에 동일한 포트를 바인딩합니다. 스크립트 작업으로 고객 서비스에서 헤드 노드의 이 포트를 수신하고 클러스터 외부로 경로 지정할 수 있습니다.</td>
<td>클라우드 서비스</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HDInsight 3.2에 대한 일시적인 MapReduce shuffle 문제</td>
<td>대형 클러스터의 Map Reduce Shuffle에서 드문 일시적인 경합 상태에 대한 수정으로 간혹 작업이 실패할 수 있습니다. 자세한 내용은 <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a>을 참조하세요.</td>
<td>Hadoop 코어</td>
<td>모두</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>

<tr>
<td>HDInsight 3.2용 최신 Azure Java SDK 2.2로 이동합니다.</td>
<td>WASB 드라이버에서 사용된 Java용 Azure SDK의 최신 버전으로 이동했습니다. 최신 SDK에는 몇 가지 수정 사항이 있으며 릴리스 정보는 https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt에서 제공됩니다.</td>
<td>Hadoop 코어</td>
<td>모두</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>

<tr>
<td>HDInsight 3.1용 HDP 2.1.15로 이동합니다.</td>
<td>릴리스에 대한 Hortonworks 릴리스 정보는 <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">여기</a>에 있습니다.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

</table>

## HDInsight의 2015/06/04 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - 변경되지 않음)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>스톰 클러스터에 대한 502 잘못된 게이트웨이 오류에 대한 수정</td>
<td>이 릴리스는 다시 부팅한 후 웹 사이트를 중지시키는 작업 제출 API에 영향을 미치는 버그를 수정합니다.</td>
<td>서비스</td>
<td>Storm</td>
<td>해당 없음</td>
</tr>

</table>

## HDInsight의 2015/06/01 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - 변경되지 않음))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - 변경되지 않음)
* SDK 1.5.8


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>다양한 버그 수정</td>
<td>이 릴리스는 클러스터 프로비전과 관련된 버그를 해결합니다.</td>
<td>부여</td>
<td>모든 클러스터 형식</td>
<td>해당 없음</td>
</tr>

</table>

## HDInsight의 2015/05/27 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* 다른 클러스터 버전 및 SDK은 이 릴리스의 일부분으로 배포되지 않습니다.


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>HDP 2.2 업데이트</td>
<td>이 릴리스의 HDInsight 3.2는 HDP 2.2.6을 포함하며, HDInsight에 다양한 중요 버그 수정을 제공합니다. 전체 릴리스 정보는 <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 릴리스 정보</a>에 있습니다.</td>
<td>HDP</td>
<td>모든 클러스터 형식</td>
<td>해당 없음</td>
</tr>

<tr>
<td>기본 Yarn 컨테이너 메모리 구성으로 변경</td>
<td>이 업데이트에서 노드 관리자에서 실행되는 YARN 컨테이너(yarn.nodemanager.resource.memory-mb and yarn.scheduler.maximum-allocation-mb)에 대해 사용 가능한 기본 메모리는 5632MB로 증가합니다. 이전에는 4608MB로 줄었었지만 다양한 작업 실행에 따라 새 값은 대부분의 작업에 더 나은 안정성과 성능을 제공해야 하므로 이 기본값이 더 적절합니다. 보통, 이 메모리 구성에 대한 중요한 종속성이 있는 경우, 클러스터를 만드는 동안 명시적으로 설정합니다.</td>
<td>HDP</td>
<td>모든 클러스터 형식</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HBase 및 스톰 클러스터에 대한 기본 구성 패리티</td>
<td>이 업데이트는 Hadoop 클러스터로 YARN 구성의 동일한 값을 사용하도록 Hbase 및 스톰 클러스터를 복원합니다. 모든 클러스터 형식에서 패리티에 대해 수행됩니다.</td>
<td>HDP</td>
<td>HBase, Storm</td>
<td>해당 없음</td>
</tr>

</table>

## HDInsight의 2015/05/20 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>SCP.NET 이벤트 허브 지원</td>
<td>HDInsight Storm에 대한 업데이트된 클러스터 패키지는 SCP.NET에 새로운 기능을 가져옵니다. 이제 EventHubSpout 또는 Java Spouts를 쉽게 사용할 수 있게 하는 토폴로지 작성기의 새 API에 액세스합니다. 계약이 업데이트되면 SCP.NET 클라이언트 SDK를 업데이트하여 새 클러스터와 작업해야 합니다. 새 API, 사용 및 릴리스 정보(버그 수정을 포함)에 대한 자세한 내용은 SCP.NET nuget 패키지에 포함된 추가 정보를 참조 하세요.</td>
<td>VS 도구</td>
<td>Storm HDInsight 3.2 클러스터</td>
<td>해당 없음</td>
</tr>

<tr>
<td>JDBC 드라이버 업데이트</td>
<td>Sqljdbc_4.1.5605.100에서 지원되는 SQL Server 버전으로 드라이버를 업데이트합니다.</td>
<td>메타 저장소</td>
<td>모두</td>
<td>해당 없음</td>
</tr>
</table>

## HDInsight의 2015/04/27 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.537.1486660(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.537.1486660(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.537.1486660(HDP 2.1.12.0-2329 - 변경되지 않음)
* HDInsight 3.2.3.537.1486660(HDP 2.2.2.2-4)
* SDK 1.5.8

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>DLL 종속성 수정</td>
<td>단위 테스트 프레임워크에 대한 HDInsight 종속성을 제거합니다.</td>
<td>SDK)</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>경합 상태에 대한 버그 수정</td>
<td>이제 클러스터 만들기 요청에서 상태를 폴링하기 전에 PUT 요청 수락을 대기합니다.</td>
<td>SDK)</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>
</table>

## HDInsight의 2015/04/14 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.521.1453250(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.521.1453250(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.521.1453250(HDP 2.1.12.0-2329 - 변경되지 않음)
* HDInsight 3.2.3.525.1459730(HDP 2.2.2.2-2)
* SDK 1.5.6

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>Tez 버그 수정</td>
<td>Apache TEZ 2214 및 TEZ 1923에 대한 수정 프로그램이 이 릴리스의 HDI 3.2에 포함되어 있습니다. 이는 특히 Tez에서 많은 데이터의 순서를 섞어야 하는 특정 Hive 쿼리를 실행하는 데 필요합니다.
</td>
<td>HDP</td>
<td>Hadoop은</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## HDInsight의 2015/04/06 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.521.1453250(HDP 1.3.12.0-01795 - 변경되지 않음)
* HDInsight 3.0.6.521.1453250(HDP 2.0.13.0-2117 - 변경되지 않음)
* HDInsight 3.1.3.521.1453250(HDP 2.1.12.0-2329 - 변경되지 않음)
* HDInsight 3.2.3.521.1453250(HDP 2.2.2.2-1)
* SDK 1.5.6

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Linux에서 HDInsight에 대한 일부 내부 클래스를 제거하는 업데이트입니다.</td>
<td>SDK)</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Avro Library 1.5.6</td>
<td><b>GetAllKnownTypes</b> 메서드에 대한 <b>KnownTypeAttribute</b>가 추가되었습니다. GetAllKnownTypes 메서드에 대해 형식이 null인 경우의 NullReferenceException이 수정되었습니다.</td>
<td>SDK)</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>버그 수정</td>
<td>서비스에 대한 다양한 버그 수정</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

</table>
<br>

## HDInsight의 2015/04/01 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.513.1431705(HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705(HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705(HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705(HDP 2.2.2.1-2600)
* SDK 1.5.5

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>.NET SDK를 통해 Windows 클러스터에서 원격 데스크톱 자격 증명을 사용하도록/사용하지 않도록 설정하는 기능</td>
<td>Windows 클러스터에서 RDP 자격 증명을 사용하도록/사용하지 않도록 설정하는 기능을 프로그래밍 방식으로 지원합니다.</td>
<td>SDK)</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>클러스터를 프로비전하는 동안 원격 데스크톱 자격 증명을 사용하도록 설정하는 기능</td>
<td>클러스터를 만들 때 원격 데스크톱 자격 증명을 사용하도록 설정하는 기능을 프로그래밍 방식으로 지원합니다. 이는 클러스터를 프로비전한 다음 원격 데스크톱을 사용하도록 설정하는 2단계 프로세스를 제거합니다.</td>
<td>SDK)</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>2.7.8로 Python 업그레이드</td>
<td>HDInsight 클러스터의 Python이 HDInsight 버전 2.1, 3.0, 3.1 및 3.2에 대한 중요한 보안 수정 프로그램을 포함하는 Python 2.7.8로 업그레이드되었습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>YARN 구성 변경</td>
<td>HDInsight 버전 3.1 및 3.2의 모든 클러스터 유형에 대해 YARN 구성 yarn.resourcemanager.max-completed-applications가 1000으로 변경되었습니다. 이 값은 YARN UI에서 완료된 응용 프로그램 목록만 제어합니다. UI에 표시된 응용 프로그램 목록 이전에 제출된 응용 프로그램에 대한 정보를 가져오려면 기록 서버로 바로 이동하면 됩니다.</td>
<td>YARN</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HBase 클러스터의 노드 크기 조정</td>
<td>이제 HBase 클러스터에서 HDInsight 버전 3.1 및 3.2에 대한 노드 크기를 조정(확장 및 축소)할 수 있습니다.</td>
<td>부여</td>
<td>HBase</td>
<td>해당 없음</td>
</tr>

<tr>
<td>JDBC 업그레이드</td>
<td>HDInsight 버전 3.2에 대해 SQL JDBC 드라이버가 버전 sqljdbc_4.0.2206.100으로 업그레이드되었습니다. 이 버전에는 중요한 보안 향상 기능이 포함되어 있습니다.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>JVM 구성 업데이트</td>
<td>HDInsight 버전 3.1 및 3.2에 대해 JVM 구성 networkaddress.cache.ttl이 기본값 -1에서 300으로 업데이트되었습니다. 이 구성 값은 이름 서비스에서의 성공적인 이름 조회에 대한 캐싱 정책을 제어합니다. 이는 HBase 클러스터 확장 및 축소와 관련된 버그를 수정합니다.</td>
<td>부여</td>
<td>HBase</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Java 2.0용 Azure 저장소 SDK 업그레이드</td>
<td>HDInsight 버전 3.2가 최신 버전의 Java용 Azure 저장소 SDK를 사용하도록 업그레이드되었습니다. 여기에는 현재 0.6.0 버전에 대한 몇 가지 중요한 버그 수정 프로그램이 포함되어 있습니다.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Apache WASB 소스 코드 업그레이드</td>
<td>이제 HDInsight 버전 3.2에서 Apache Hadoop의 WASB 파일 시스템 드라이버에 최신 코드를 사용합니다. 이 변경 내용으로 이제 WASB 드라이버가 별도의 jar로 패키지됩니다. 이는 순수한 패키징 변경 내용이며 WASB 드라이버의 동작에 대한 변경 내용을 포함하지 않습니다. 이 JAR 파일의 이름은 hadoop-azure-2.6.0.jar입니다.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HDInsight 3.2에서 Jar 파일 이름 업데이트</td>
<td>이 HDInsight 버전 3.2 업데이트에는 여러 버그 수정 프로그램이 포함되어 있으며, HDP의 일부로 패키지된 몇 가지 내부 jar이 업그레이드되었습니다. 이러한 JAR 파일은 HDP 패키지에 속해 있으므로 고객 응용 프로그램에서 직접 사용할 수 없습니다. HDP 내부 JAR 업그레이드로 인해 고객 응용 프로그램이 중단되지 않도록 응용 프로그램에서 고유한 버전의 JAR을 패키지해야 합니다.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

</table>
<br>

## HDInsight의 2015/03/03 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.488.1375841(HDP 1.3.9.0-01351 - 변경되지 않음)
* HDInsight 3.0.6.488.1375841(HDP 2.0.9.0-2097 - 변경되지 않음)
* HDInsight 3.1.3.488.1375841(HDP 2.1.10.0-2290 - 변경되지 않음)
* HDInsight 3.2.3.488.1375841(HDP-2.2.10.0-2340 - 변경되지 않음)
* SDK 1.5.0(변경되지 않음)

이 릴리스에는 다음 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA</th>
</tr>


<tr>
<td>안정성 향상</td>
<td>클러스터 만들기와 관련된 증가된 부하에서 서비스 크기를 보다 잘 조정할 수 있도록 수정했습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>



</table>
<br>

## HDInsight의 2015/02/18 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.471.1342507(HDP 1.3.9.0-01351 - 변경되지 않음)
* HDInsight 3.0.6.471.1342507(HDP 2.0.9.0-2097 - 변경되지 않음)
* HDInsight 3.1.3.471.1342507(HDP 2.1.10.0-2290 - 변경되지 않음)
* HDInsight 3.2.3.471.1342507(HDP-2.2.10.0-2340)
* SDK 1.5.0

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>HDInsight 3.2 클러스터</td>
<td>Hadoop 2.6/HDP2.2는 HDInsight 3.2 클러스터에서 사용할 수 있습니다. 여기에는 모든 오픈 소스 구성 요소에 대한 주요 업데이트가 포함되어 있습니다. 자세한 내용은 HDInsight의 새로운 기능 및 <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 릴리스 정보</a>를 참조하세요.</td>
<td>오픈 소스 소프트웨어</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Linux의 HDInsight(미리 보기)</td>
<td>Ubuntu Linux에서 실행되는 클러스터를 배포할 수 있습니다. 자세한 내용은 Linux에서 HDInsight 시작을 참조하세요.</td>
<td>부여</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Storm 일반 공급</td>
<td>Apache Storm 클러스터가 일반 공급됩니다. 자세한 내용은 HDInsight에서 Storm 사용 시작을 참조하세요.</td>
<td>부여</td>
<td>Storm</td>
<td>해당 없음</td>
</tr>

<tr>
<td>가상 컴퓨터 크기</td>
<td>더 다양한 가상 컴퓨터 유형 및 크기에서 Azure HDInsight를 사용할 수 있습니다. HDInsight는 일반적인 용도로 빌드된 A2 ~ A7 크기(반도체 드라이브(SSD) 기능을 제공하는 D-Series 노드와 60% 더 빠른 프로세서) 및 빠른 네트워킹을 위해 InfiniBand를 지원하는 A8 및 A9 크기를 활용할 수 있습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>클러스터 크기 조정</td>
<td>HDInsight 클러스터를 삭제하거나 다시 만들지 않고도 실행 중인 HDInsight 클러스터의 노드 수를 변경할 수 있습니다. 현재는 Hadoop 쿼리 및 Apache Storm 클러스터에만 이 기능이 있지만 Apache HBase 클러스터 유형에도 곧 이 기능이 제공될 예정입니다. 자세한 내용은 Ambari를 사용하여 HDInsight 관리를 참조하세요.</td>
<td>부여</td>
<td>Hadoop, Storm</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Visual Studio 도구</td>
<td>Apache Storm용 전체 도구 외에 Visual Studio의 Apache Hive용 도구도 문 완성, 로컬 유효성 검사 및 향상된 디버깅 지원을 포함하도록 업데이트되었습니다. 자세한 내용은 Visual Studio용 HDInsight Hadoop 도구 시작을 참조하세요.</td>
<td>도구</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>DocumentDB용 Hadoop 커넥터</td>
<td>DocumentDB용 Hadoop 커넥터를 사용하면 여러 DocumentDB 컬렉션 또는 데이터베이스 계정에 걸쳐 저장된 스키마 없는 JSON 문서에 대해 복잡한 집계, 분석 및 조작을 수행할 수 있습니다. 자세한 내용 및 자습서는 DocumentDB 및 HDInsight를 사용하여 Hadoop 작업 실행을 참조하세요.</td>
<td>부여</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>버그 수정</td>
<td>HDInsight 서비스에 대한 여러 가지 사소한 버그가 수정되었습니다. 고객이 수행해야 하는 동작 변경은 없습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

</table>
<br>

## HDInsight의 2015/02/06 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.463.1325367(HDP 1.3.9.0-01351 - 변경되지 않음)
* HDInsight 3.0.6.463.1325367(HDP 2.0.9.0-2097 - 변경되지 않음)
* HDInsight 3.1.2.463.1325367(HDP 2.1.10.0-2290)
* SDK N/A

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>버그 수정</td>
<td>HDInsight 서비스에 대한 여러 가지 사소한 버그가 수정되었습니다. 고객이 수행해야 하는 동작 변경은 없습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HDP 2.1 유지 관리 업데이트</td>
<td>HDInsight 3.1이 HDP 2.1.10.0을 배포하도록 업데이트되었습니다. 자세한 내용은 <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">HDP-2.1.10 릴리스 정보</a>를 참조하세요. </td>
<td>오픈 소스 소프트웨어</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HDP 이진 업데이트</td>
<td>HBase의 JAR 파일 중 일부의 파일 이름이 업데이트되었습니다. 이러한 JAR 파일은 HBase에서 내부적으로 사용되므로 고객은 해당 JAR 파일 이름에 종속되지 않아도 됩니다. 내용은 다음과 같습니다.
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>오픈 소스 소프트웨어</td>
<td>HBase</td>
<td>해당 없음</td>
</tr>

</table>
<br>

## HDInsight의 2015/1/29 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.455.1309616(HDP 1.3.9.0-01351 - 변경되지 않음)
* HDInsight 3.0.6.455.1309616(HDP 2.0.9.0-2097 - 변경되지 않음)
* HDInsight 3.1.2.455.1309616(HDP 2.1.9.0-2196 - 변경되지 않음)
* SDK N/A

이 릴리스에는 다음 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>영향을 받는 영역(예: 서비스, 구성 요소 또는 SDK)</p></th>
<th>클러스터 유형(예: Hadoop, HBase 또는 Storm)</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>

<td>버그 수정</td>
<td>Azure를 업그레이드하는 동안 HDInsight 클러스터의 안정성을 개선하는 몇 가지 중요한 버그를 수정했습니다.</td>
<td>부여</td>
<td>모두</td>
<td>해당 없음</td>
</tr>



</table>
<br>

## HDInsight의 2015/1/5 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.420.1246118(HDP 1.3.9.0-01351 - 변경되지 않음)
* HDInsight 3.0.6.420.1246118(HDP 2.0.9.0-2097 - 변경되지 않음)
* HDInsight 3.1.2.420.1246118(HDP 2.1.9.0-2196 - 변경되지 않음)


이 릴리스에는 다음 업데이트가 포함되어 있습니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>Twitter 추세 분석 및 Mahout 기반 동영상 권장 사항 샘플</td>
<td><p>이 릴리스에서는 다음과 같이 HDInsight 쿼리 콘솔에 2개의 추가 샘플이 있습니다.</p>

<p><b>Twitter 추세 분석</b><br>
Twitter와 같은 사이트에서 제공하는 공개 API는 대중적인 추세를 분석하고 이해하는 데 유용한 데이터 원본입니다. 이 자습서에서는 Hive를 사용하여 특정 단어를 포함하는 대부분의 트윗을 보낸 Twitter 사용자 목록을 가져오는 방법에 대해 알아봅니다. </p>

<p><b>Mahout 동영상 권장 사항</b><br>
Apache Mahout는 Apache Hadoop 기계 학습 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 자습서에서는 추천 엔진을 사용하여 친구가 본 영화를 기준으로 영화 추천을 생성합니다.</p></td>
<td>쿼리 콘솔</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Hive 구성 hive.auto.convert.join.noconditionaltask.size의 기본값 변경</td>
<td><p>이 크기 구성은 자동으로 변환된 맵 조인에 적용됩니다. 이 값은 메모리 크기에 맞는 해시 맵으로 변환할 수 있는 테이블의 크기를 합한 값을 나타냅니다. 이전 릴리스에서 이 값은 기본값 10MB에서 128MB로 증가했습니다. 그러나 새 값 128MB는 메모리의 부족으로 인한 작업 실패를 초래했습니다. 이 릴리스에서는 기본값을 다시 10MB로 되돌립니다. 고객은 여전히 해당 쿼리 및 테이블 크기가 지정된 클러스터를 만들 때 이 값을 재정의하도록 선택할 수 있습니다. 이 설정에 대한 자세한 내용 및 이 설정을 재정의하는 방법은 Hortonworks 설명서에서 <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">자동 조인 변환 최적화</a>를 참조하세요. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>해당 없음</td>
</tr>

</table>
<br>

## HDInsight의 2014/12/23 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.10.420.1246783(HDP 버전 변경되지 않음)
* HDInsight 3.0.6.420.1246783(HDP 버전 변경되지 않음
* HDInsight 3.1.1.420.1246783(HDP 버전 변경되지 않음

이 릴리스에는 다음 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>


<tr>
<td>과도한 로드로 인한 일시적인 클러스터 생성 실패</td>
<td><p>클러스터를 생성하는 동안의 HDP 패키지 다운로드를 위한 알고리즘이 개선되어 과도한 로드로 인한 실패를 보다 강력하게 처리할 수 있습니다.</p></td>
<td>부여</td>
<td>Hadoop, Hbase, Storm</td>
<td>해당 없음</td>
</tr>



</table>
<br>

## HDInsight의 2014/12/18 릴리스 정보

이 릴리스에는 다음 구성 요소 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">클러스터 사용자 지정 일반 공급</a></td>
<td><p>사용자 지정 시 Apache Hadoop 에코시스템에서 사용할 수 있는 프로젝트로 Azure HDInsight 클러스터를 사용자 지정할 수 있습니다. 이 새로운 기능을 사용하여 Hadoop 프로젝트를 실험하고 Azure HDInsight에 배포할 수도 있습니다. 이 기능은 사용자 지정 스크립트를 사용하여 Hadoop 클러스터를 임의의 방식으로 수정할 수 있는 **스크립트 작업** 기능을 통해 사용할 수 있게 됩니다. 이 사용자 지정 방식은 Hadoop, HBase, Storm을 비롯한 모든 HDInsight 유형에서 사용할 수 있습니다. 이 기능을 보여 주기 위해 많이 사용하는 <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a> 및 <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> 모듈을 설치하는 프로세스를 문서화했습니다. 이 릴리스에서는 고객이 Azure 포털을 통해 사용자 지정 스크립트 작업을 지정할 수 있는 기능도 추가되며 도우미 메서드를 사용하여 사용자 지정 스크립트 작업을 작성하는 방법에 대한 지침 및 모범 사례를 제공하고 스크립트 작업을 테스트하는 방법에 대한 지침도 제공합니다. </p></td>
<td>기능 일반 공급</td>
<td>모두</td>
<td>해당 없음</td>
</tr>


</table>
<br>

## HDInsight의 2014/12/05 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.9.406.1221105(HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105(HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105(HDP 2.1.9.0-2196)
* HDInsight SDK 해당 없음

이 릴리스에는 다음 구성 요소 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>

<tr>
<td>버그 수정: Hive DDL에서 테이블에 많은 파티션을 추가하는 동안 발생하는 일시적인 오류 </td>
<td><p>Hive 테이블에 많은 파티션을 추가할 때 Hive Metastore 데이터베이스에서 일시적인 연결 오류가 발생하면 Hive DDL이 실패할 수 있습니다. 이 실패가 발생하면 Hive 오류 로그에 다음 내용이 표시됩니다. </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482(내부 JIRA이므로 외부에서 인용할 수 없습니다. 여기에 참조용으로 기록되었습니다.)</td>
</tr>

<tr>
<td>버그 수정: HDInsight 쿼리 콘솔의 간헐적 중지</td>
<td>이 문제가 발생하면 WebHCat 시작 관리자 작업에 대한 WebHCat 로그에 다음 내용이 표시될 수 있습니다. <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop은</td>
<td>HIVE-482(내부 JIRA이므로 외부에서 인용할 수 없습니다. 여기에 참조용으로 기록되었습니다.)</td>
</tr>

<tr>
<td>버그 수정: Hbase 쿼리 대기 시간의 간헐적 급증</td>
<td>이 문제가 발생하면 사용자는 Hbase 쿼리 대기 시간에 3초의 간헐적 급증을 확인하게 됩니다. </td>
<td>HDInsight 클러스터 게이트웨이</td>
<td>HBase</td>
<td>해당 없음</td>
</tr>

<tr>
<td>HDP JAR 파일 이름 변경</td>
<td>HDI 클러스터 버전 3.0의 경우 HDP에 의해 설치되는 내부 JAR 파일에 대한 몇 가지 변경 내용이 있습니다. jetty-6.1.26.jar은 jetty-6.1.26.hwx.jar로 대체되고, jetty-util-6.1.26.jar은 jetty-util-6.1.26.hwx.jar로 대체되었습니다. 이 변경 내용은 Hadoop, Mahout, WebHCat 및 Oozie 프로젝트에 적용됩니다.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>해당 없음</td>
</tr>

</table>
<br>


## HDInsight의 2014/11/21 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.9.382.1169709(2014/11/14에서 변경 없음)
* HDInsight 3.0.5.382.1169709(2014/11/14에서 변경 없음)
* HDInsight 3.1.1.382.1169709(2014/11/14에서 변경 없음)
* HDINsight SDK 1.4.0

이 릴리스에는 다음 구성 요소 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>

<tr>
<td>응용 프로그램 로그 액세스</td>
<td>클러스터에서 실행된 응용 프로그램을 프로그래밍 방식으로 열거하고 문제가 있는 응용 프로그램을 디버그하도록 도와주는 관련 응용 프로그램 또는 컨테이너별 로그를 다운로드하는 기능입니다.</td>
<td>SDK)</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>IHdInsightClient.DeleteCluster에서 지역 이름을 지정하는 기능 </td>
<td>Azure HDInsight SDK에서 **DeleteCluster**를 사용할 때 지역 이름을 지정하는 기능을 제공합니다. 이 기능을 통해 서로 다른 지역에서 같은 이름을 가진 2개의 리소스가 있고 둘 중 하나를 삭제할 수 없었던 고객의 차단을 취소할 수 있습니다.</td>
<td>SDK)</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>**ClusterDetails** 개체는 클러스터의 고유 식별자를 나타내는 **DeploymentID** 필드를 반환합니다. 따라서 같은 이름을 사용하는 여러 클러스터 만들기 시도에서 고유해집니다.</td>
<td>SDK)</td>
<td>모두</td>
<td>해당 없음</td>
</tr>
</table>
<br>

## HDInsight의 2014/11/14 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

이 릴리스에는 다음과 같은 새로운 기능, 구성 요소 업데이트 및 버그 수정이 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>

<tr>
<td>스크립트 작업(미리 보기):</td>
<td>사용자 지정 스크립트를 사용하여 임의 방식으로 Hadoop 클러스터를 수정할 수 있는 클러스터 사용자 지정 기능의 미리 보기입니다. 이 기능을 통해 사용자는 Apache Hadoop 에코시스템에서 사용할 수 있는 프로젝트를 실험하고 Azure HDInsight 클러스터에 배포할 수 있습니다. 이 사용자 지정 기능은 Hadoop, HBase, Storm을 비롯한 모든 HDInsight 유형에서 사용할 수 있습니다.</td>
<td>새로운 기능</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Azure 웹 사이트 및 저장소 로그 분석을 위해 미리 빌드된 작업</td>
<td>HDInsight Query 콘솔에는 사용자의 데이터 또는 샘플 데이터를 기반으로 작동하는 솔루션을 지원하는 시작 갤러리가 있습니다.
<p>**사용자의 데이터를 기반으로 작동하는 솔루션**:<br>
고유한 솔루션을 만들기 위한 시작점을 제공하도록 몇 가지 가장 일반적인 데이터 분석 시나리오에 대한 작업을 만들었습니다. 이 작업에서 사용자의 데이터를 사용하여 작동 방식을 확인할 수 있습니다. 그런 다음 준비가 되면 배운 내용에 따라 미리 빌드된 작업 다음에 모델링되는 솔루션을 만듭니다.</p>
<p>**샘플 데이터를 기반으로 작동하는 솔루션**:<br>
웹 로그 및 센서 데이터 분석과 같은 몇몇 기본 시나리오를 살펴봄으로써 HDInsight 사용 방법에 대해 알아봅니다. HDInsight를 사용하여 해당 데이터를 분석하는 방법 및 다른 응용 프로그램과 서비스를 이 데이터에 연결하는 방법을 알아봅니다. Microsoft Excel에 연결하는 방식의 데이터 시각화는 이 접근법이 적용된 한 가지 예입니다.</p></td>
<td>쿼리 콘솔</td>
<td>Hadoop은</td>
<td>해당 없음</td>
</tr>

<tr>
<td>Templeton의 메모리 누수 수정</td>
<td>장기간 실행되는 클러스터가 있거나 1초에 수백 개의 작업 요청을 전송하는 고객에게 영향을 미친 Templeton의 메모리 누수가 해결되었습니다. 이 문제는 Templeton 5xx 오류로 명시되었고 해결 방법은 서비스를 다시 시작하는 것입니다. 이제는 해결 방법이 필요하지 않습니다.</td>
<td>Templeton</td>
<td>모두</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


**참고**: 클러스터 사용자 지정을 통해 사용할 수 있는 새로운 기능을 설명하기 위해 스크립트 동작을 사용하여 Spark 및 R 모듈을 설치하는 절차가 문서에 포함되었습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight 클러스터에 Spark 1.0 설치 및 사용](hdinsight-hadoop-spark-install.md)
* [HDInsight Hadoop 클러스터에 R 설치 및 사용](hdinsight-hadoop-r-scripts.md)




## HDInsight의 2014/11/07 릴리스 정보

이 릴리스와 함께 배포된 HDInsight 클러스터의 전체 버전 번호는 다음과 같습니다.

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

이 릴리스에는 다음 구성 요소 업데이트가 포함됩니다.

<table border="1">
<tr>
<th>제목</th>
<th>설명</th>
<th>구성 요소</th>
<th>클러스터 유형</th>
<th>JIRA(적용 가능한 경우)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>이 릴리스는 HDP(Hortonworks Data Platform) 2.1.7에 기반을 둡니다. 자세한 내용은 <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">HDP 2.1.7 릴리스 정보</a>를 참조하세요.</td>
<td>HDP</td>
<td>모두</td>
<td>해당 없음</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>YARN Timeline Server(Generic Application History Server라고도 함)가 기본적으로 사용하도록 설정되었습니다. Timeline Server는 응용 프로그램 ID, 응용 프로그램 이름, 응용 프로그램 상태, 응용 프로그램 전송 시간, 응용 프로그램 완료 시간 등 완료된 응용 프로그램에 대한 일반적인 정보를 제공합니다. 이 응용 프로그램 정보는 URI http://headnodehost:8188에 액세스하거나 YARN 명령: yarn application –list –appStates ALL을 실행하여 헤드 노드에서 검색할 수 있습니다. 이 정보는 REST API를 통해 https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/에서 원격으로 검색할 수도 있습니다. 자세한 내용은 <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a>를 참조하세요.</td>
<td>서비스, YARN</td>
<td>Hadoop, HBase</td>
<td>해당 없음</td>
</tr>

<tr>
<td>클러스터 배포 ID</td>
<td>SDK 버전 1.3.3.1.5426.29232부터 사용자는 각 클러스터에 대해 HDInsight에서 발급된 고유 ID에 액세스할 수 있습니다. 따라서 고객은 DNS 이름이 만들기/삭제 시나리오에서 재사용될 때 고유한 클러스터 인스턴스를 알아낼 수 있습니다.</td>
<td>SDK)</td>
<td>모두</td>
<td>해당 없음</td>
</tr>
</table>
<br>

**참고**: 전체 버전 번호가 포털에 표시되지 않거나 SDK 또는 Windows PowerShell에서 반환되지 않게 하는 버그가 이 릴리스에서 수정되었습니다.

## 2014/10/15 릴리스 정보

이 핫픽스 릴리스에서는 Templeton을 많이 사용하는 사용자에게 영향을 주는 Templeton의 메모리 누수 현상이 해결되었습니다. 요청을 실행하기 위한 메모리가 부족하여 Templeton을 많이 사용하는 사용자에게 500 오류 코드가 표시되는 경우가 있었습니다. 이 문제를 해결하려면 Templeton 서비스를 다시 시작해야 했습니다. 이 문제가 해결되었습니다.


## 2014/10/7 릴리스 정보

* Ambari 끝점 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}"을 사용할 때 *host\_name* 필드에서 호스트 이름만이 아니라 노드의 FQDN(정규화된 도메인 이름)을 반환합니다. 예를 들어 "**headnode0**"가 반환되는 대신 FQDN인 "**headnode0.{ClusterDNS}.azurehdinsight.net**"이 반환됩니다. 이 변경은 HBase, Hadoop 등의 여러 클러스터 유형을 가상 네트워크 하나에 배포할 수 있는 시나리오를 원활하게 수행하기 위해 필요한 작업이었습니다. 예를 들어 Hadoop의 백 엔드 플랫폼으로 HBase를 사용하는 등의 경우 이 변경이 적용됩니다.

* HDInsight 클러스터의 기본 배포에 대해 새 메모리 설정이 제공됩니다. 이전의 기본 메모리 설정에서는 배포 중인 CPU 코어 수에 대한 지침을 적절하게 고려하지 않았습니다. 이러한 새 메모리 설정은 Hortonworks 권장 사항에 따라 향상된 기본값을 제공합니다. 이를 변경하려면 클러스터 구성 변경에 대한 SDK 참조 설명서를 참조하세요. 아래 표에는 기본 4개 CPU 코어(8개 컨테이너) HDInsight 클러스터에서 사용되는 새 메모리 설정이 개별 항목으로 나와 있습니다. 이 릴리스 이전에 사용되었던 값도 괄호 안에 나와 있습니다.

<table border="1">
<tr><th>구성 요소</th><th>메모리 할당</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB(이전에는 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB(변경되지 않음)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB(변경되지 않음)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB(이전에는 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m(이전에는 -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB(이전에는 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m(이전에는 -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB(이전에는 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m(이전에는 -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB(이전에는 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB(변경되지 않음)</td></tr>

</table><br>

HDInsight에서 사용하는 Hortonworks Data Platform에서 YARN 및 MapReduce에 사용되는 메모리 구성 설정에 대한 자세한 내용은 [HDP 메모리 구성 설정 확인](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)을 참조하세요. Hortonworks에서는 적절한 메모리 설정을 계산하는 도구도 제공합니다.

Azure PowerShell 및 HDInsight SDK 오류 메시지: "*클러스터가 HTTP 서비스 액세스를 위해 구성되어 있지 않습니다.*"

* 이 오류는 HDInsight SDK 또는 Azure PowerShell 버전과 클러스터의 버전 차이로 인해 발생할 수 있는 알려진 [호환성 문제](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)입니다. 8/15 또는 그 이후에 만든 클러스터는 가상 네트워크에 대한 새로운 프로비전 기능을 지원합니다. 그러나 이전 버전의 HDInsight SDK 또는 Azure PowerShell에서는 이 기능이 올바르게 해석되지 않습니다. 따라서 일부 작업 제출 작업이 실패합니다. HDInsight SDK API 또는 Azure PowerShell cmdlet(**Use-AzureRmHDInsightCluster** 또는 **Invoke-AzureRmHDInsightHiveJob**)을 사용하여 작업을 제출하는 경우 해당 작업이 실패하고 "*클러스터 <clustername>이 HTTP 서비스 액세스를 위해 구성되어 있지 않습니다.*" 오류 메시지가 표시되거나 작업에 따라 "*클러스터에 연결할 수 없습니다.*" 등의 다른 오류 메시지가 표시될 수 있습니다.

* HDInsight SDK 및 Azure PowerShell의 최신 버전에서는 이러한 호환성 문제가 해결되었습니다. HDInsight SDK는 버전 1.3.1.6 이상으로, Azure PowerShell 도구는 버전 0.8.8 이상으로 업데이트하는 것이 좋습니다. 최신 HDInsight SDK는 [](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서, Azure PowerShell 도구는 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)에서 얻을 수 있습니다.



## HDInsight 3.1의 2014/9/12 릴리스 정보

* 이 릴리스는 HDP(Hortonworks Data Platform) 2.1.5에 기반을 둡니다. 이 릴리스에서 수정된 버그 목록을 보려면 Hortonworks 사이트에서 [이 릴리스에서 수정됨](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html)을 참조하세요.
* Pig 라이브러리 폴더의 "avro-mapred-1.7.4.jar" 파일이 "avro-mapred-1.7.4-hadoop2.jar"로 변경되었습니다. 이 파일의 내용에는 사소한 버그 수정(줄 바꿈 하지 않음)이 포함되어 있습니다. 고객은 파일 이름을 바꿀 때 줄 바꿈을 방지할 수 있도록 JAR 파일의 이름에 대한 직접 종속성을 사용하지 않는 것이 좋습니다.


## 2014/8/21 릴리스 정보

* Templeton 컨트롤러 작업에 대한 기본 메모리 제한을 1GB로 설정하는 다음 WebHCat 구성(HIVE-7155)이 추가됩니다. 이전 기본값은 512MB였습니다.

	 templeton.mapper.memory.mb (=1024)

	* 이 변경은 낮은 메모리 제한으로 인해 특정 Hive 쿼리가 발생하는 "컨테이너가 실제 메모리 제한 이상으로 실행합니다." 오류를 해결합니다.
	* 이전 기본값으로 되돌리려면 클러스터 생성 시 다음 명령을 사용하여 Azure PowerShell을 통해 이 구성 값을 512로 설정하면 됩니다.

		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* *zookeeper* 역할의 호스트 이름이 zookeeper로 변경되었습니다. 이 변경은 클러스터 내의 이름 확인에 영향을 주지만 외부 REST API에는 영향을 주지 않습니다. *zookeepernode* 호스트 이름을 사용하는 구성 요소가 있는 경우 새 이름을 사용하도록 해당 구성 요소를 업데이트해야 합니다. 3개 zookeeper 노드의 새로운 이름은 다음과 같습니다.
	* zookeeper0
	* zookeeper1
	* zookeeper2
* HBase 버전 지원 매트릭스가 업데이트됩니다. 프로덕션 HBase 워크로드에는 HDInsight 버전 3.1(HBase 버전 0.98)만 지원됩니다. 미리 보기에 사용 가능한 버전 3.0은 앞으로 지원되지 않습니다.

## 2014/8/15 이전에 만든 클러스터에 대한 참고 사항

Azure PowerShell 또는 HDInsight SDK와 클러스터 간의 버전 차이로 인해 Azure PowerShell 또는 HDInsight SDK 오류 메시지 "클러스터 <clustername>이 HTTP 서비스 액세스를 위해 구성되어 있지 않습니다." 또는 작업에 따라 "클러스터에 연결할 수 없습니다."와 같은 기타 오류 메시지가 발생할 수 있습니다. 8/15 또는 그 이후에 만든 클러스터는 가상 네트워크에 대한 새로운 프로비전 기능을 지원합니다. 그러나 이전 버전의 Azure PowerShell 또는 HDInsight SDK에서는 이 기능이 올바르게 해석되지 않아 작업을 제출하는 작업이 실패합니다. HDInsight SDK API 또는 Azure PowerShell cmdlet(예: Use-AzureRmHDInsightCluster 또는 Invoke-AzureRmHDInsightHiveJob)을 사용하여 작업을 제출하는 경우 해당 작업이 실패하고 위에서 설명한 오류 메시지 중 하나가 표시될 수 있습니다.

HDInsight SDK 및 Azure PowerShell의 최신 버전에서는 이러한 호환성 문제가 해결되었습니다. HDInsight SDK는 버전 1.3.1.6 이상으로, Azure PowerShell 도구는 버전 0.8.8 이상으로 업데이트하는 것이 좋습니다. 최신 HDInsight SDK는 [NuGet][nuget-link]에서 액세스할 수 있고, Azure PowerShell 도구는 [Microsoft 웹 플랫폼 설치 관리자][webpi-link]를 사용하여 액세스할 수 있습니다.


## 2014/7/28 릴리스 정보

* **새로운 지역에서 사용 가능한 HDInsight**: HDInsight 제공 지역을 3개 지역으로 확장했습니다. HDInsight 고객은 다음 지역에서 클러스터를 만들 수 있습니다.
	* 동아시아
	* 미국 중북부
	* 미국 중남부
* HDInsight 버전 1.6(HDP 1.1 및 Hadoop 1.0.3) 및 HDInsight 버전 2.1(HDP 1.3 및 Hadoop 1.2)이 Azure 포털에서 제거됩니다. Azure PowerShell cmdlet([New-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)) 또는 [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx)를 사용하여 계속 Hadoop 클러스터를 만들 수 있습니다. 자세한 내용은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md) 페이지를 참조하세요.
* 이 릴리스의 HDP(Hortonworks Data Platform) 변경 내용:

<table border="1">
<tr><th>HDP</th><th>변경 내용</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>변경 내용 없음</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>변경 내용 없음</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## 2014/6/24 릴리스 정보

이 릴리스에는 HDInsight 서비스의 향상된 기능이 포함되어 있습니다.

* **HDP 2.1 사용 가능**: HDP 3.1을 포함하는 HDInsight 2.1이 일반 공급되며 새로운 클러스터의 기본 버전입니다.
* **HBase - Azure 포털 개선 사항**: 미리 보기에서 HBase 클러스터를 사용할 수 있습니다. 몇 번만 클릭하면 포털에서 HBase 클러스터를 만들 수 있습니다.

HBase에서는, 수백만 개의 끝점에서 들어오는 센서 및 원격 분석 데이터를 저장하는 서비스의 대형 데이터 집합을 사용하는 대화형 웹 사이트에서 HDInsight에 대한 여러 실시간 워크로드를 구성할 수 있습니다. 다음 단계는 Hadoop 작업으로 이러한 워크로드의 데이터를 분석하는 것이며, Azure PowerShell 및 Hive 클러스터 대시보드를 통해 HDInsight에서 수행할 수 있습니다.

### HDInsight 3.1에 사전 설치되는 Apache Mahout

 [Mahout](http://hortonworks.com/hadoop/mahout/)이 HDInsight 3.1 Hadoop 클러스터에 사전 설치됩니다. 따라서 추가 클러스터 구성 없이도 Mahout 작업을 실행할 수 있습니다. 예를 들어 RDP(원격 데스크톱 프로토콜)를 사용하여 Hadoop 클러스터에 원격으로 연결할 수 있으며, 추가 단계 없이 다음 Hello World Mahout 명령을 실행할 수 있습니다.

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

이 절차에 대한 자세한 설명은 Apache Mahout 웹 사이트의 [Breiman 예제](https://mahout.apache.org/users/classification/breiman-example.html) 설명서를 참조하세요.


### Hive 쿼리는 HDinsight 3.1에서 Tez를 사용할 수 있음

Hive 0.13은 HDInsight 3.1에서 사용 가능하며 Tez를 사용하여 쿼리를 실행할 수 있으므로, 실질적인 성능 개선을 이룰 수 있습니다. Tez는 기본적으로 Hive 쿼리에 사용할 수 없습니다. Tez를 사용하려면 옵트인해야 합니다. 다음 코드 조각을 실행하여 Tez를 사용하도록 설정할 수 있습니다.

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks에서 표준 벤치마크로 전달되는 Tez와 함께 사용하는 Hive 쿼리 성능 개선 사항의 자세한 분석을 게시했습니다. 자세한 내용은 [Enterprise Hadoop용 Apache Hive 13 벤치마킹](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/)을 참조하세요.

Tez에서 Hive를 사용하는 방법에 대한 자세한 내용은 [Tez의 Hive](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)를 참조하세요.

###전 세계 이용 가능 여부
Hadoop 2.2의 HDInsight 릴리스부터 Azure를 사용할 수 있는 모든 주요 지역에서 HDInsight를 사용할 수 있게 되었습니다. 특별히 서유럽과 동남아시아 데이터 센터를 온라인으로 전환했습니다. 따라서 고객은 준수 요구 사항이 비슷한 영역에 있는, 가까운 데이터 센터에서 클러스터를 찾을 수 있습니다.


###클러스터 버전 간에 수행할 수 있는 작업과 수행할 수 없는 작업

**HDInsight 3.1 클러스터에 사용되는 Oozie Metastore가 이전 버전인 HDInsight 2.1 클러스터와 호환되지 않으므로 해당 이전 버전에서는 사용할 수 없음**

HDInsight 3.1 클러스터와 함께 배포한 사용자 지정 Oozie 메타 저장소 데이터베이스는 HDInsight 2.1 클러스터와 함께 다시 사용할 수 없습니다. 해당 Metastore를 원래 HDInsight 2.1 클러스터에서 만든 경우에도 마찬가지입니다. Metastore를 HDInsight 3.1 클러스터와 함께 사용하면 Metastore 스키마가 업그레이드되어 HDInsight 2.1 클러스터에 필요한 Metastore와는 더 이상 호환되지 않으므로 이 시나리오는 지원되지 않습니다. HDInsight 3.1 클러스터와 함께 사용했던 Oozie Metastore를 다시 사용하려고 하면 HDInsight 2.1 클러스터를 사용할 수 없게 됩니다.

**Oozie Metastore는 클러스터 간에 공유할 수 없음**

Oozie Metastore는 특정 클러스터에 연결되며 클러스터 간에 공유할 수 없습니다.

###주요 변경 내용

**접두사 구문**: HDInsight 3.1 및 3.0 클러스터에서는 "wasbs://" 구문만 지원됩니다. 이전 "asv://" 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.1 또는 3.0 클러스터에서는 지원되지 않습니다. 즉, "asv://" 구문을 명시적으로 사용하며 HDInsight 3.1 또는 3.0 클러스터에 제출된 모든 작업은 실패하게 됩니다. 대신 "wasbs://" 구문을 사용해야 합니다. 또한 "asv://" 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 Metastore로 만들어 HDInsight 3.1 또는 3.0 클러스터에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 “wasbs://” 구문을 사용하여 이러한 Metastore를 다시 만들어야 합니다.


**포트**: HDInsight 서비스에 사용되는 포트가 변경되었습니다. 기존에 사용하는 포트 번호는 Windows 운영 체제의 사용 후 삭제되는 포트 범위 내에 있었습니다. 포트는 수명이 짧은 인터넷 프로토콜 기반 통신용으로 미리 정의된 사용 후 삭제되는 범위에서 자동으로 할당됩니다. 허용되는 새로운 HDP(Hortonworks Data Platform) 서비스 포트 집합은 이 범위를 벗어나므로 헤드 노드에서 실행 중인 서비스에 사용되는 포트로 인해 발생할 수 있는 충돌을 방지합니다. 새로운 포트 번호로 인한 특별한 변경은 없습니다. 사용되는 번호는 다음과 같습니다.

 **HDInsight 1.6(HDP 1.1)** <table border="1">
<tr><th>Name</th><th>값</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 및 3.0(HDP 2.1 및 2.0)** <table border="1">
<tr><th>Name</th><th>값</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###종속성

다음 종속성이 HDInsight 3.x(HDP2.x)에 추가되었습니다.

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic\_1.0\_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation\_1.0\_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta\_1.1\_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

다음 종속성은 더 이상 HDInsight 3.x (HDP2.x)에 존재하지 않습니다.

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json :
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* HBase:
* snappy

###버전 변경 내용

다음 버전 변경 내용이 HDInsight 2.x(HDP1.x)와 HDInsight 3.x(HDP2.x) 간에 수행되었습니다.

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0\.8.0.RELEASE']


### 드라이버
SQL Server용 JDBC(Java Database Connnectivity) 드라이버는 HDInsight에서 내부적으로 사용되며 외부 작업에는 사용되지 않습니다. odbc를 사용하여 HDInsight에 연결하려는 경우 Microsoft Hive odbc(Open Database Connectivity) 드라이버를 사용하세요. 자세한 내용은 [HDInsight에 Microsoft Hive ODBC 드라이버로 Excel 연결](../../articles/hdinsight/hdinsight-connect-excel-hive-odbc-driver.md) 을 참조하세요.


### 버그 수정

이 릴리스에서 몇 가지 버그 수정을 통해 다음 HDInsight 버전을 새로 고쳤습니다.

* HDInsight 2.1(HDP 1.3)
* HDInsight 3.0(HDP 2.0)
* HDInsight 3.1(HDP 2.1)


## Hortonworks 릴리스 정보

HDInsight 버전 클러스터에 사용되는 HDP(Hortonworks Data Platform)에 대한 릴리스 정보는 다음 위치에서 사용할 수 있습니다.

* HDInsight 버전 3.1에서는 [Hortonworks Data Platform 2.1.7][hdp-2-1-7]을 기반으로 하는 Hadoop 배포를 사용합니다. 이는 2014년 11월 7일 이후에 Azure 포털을 사용할 때 만든 기본 Hadoop 클러스터입니다. 2014년 11월 7일 이전에 만들어진 HDInsight 3.1 클러스터는 [Hortonworks Data Platform 2.1.1][hdp-2-1-1]을 기반으로 합니다.

* HDInsight 버전 3.0에서는 [Hortonworks Data Platform 2.0][hdp-2-0-8]을 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 버전 2.1에서는 [Hortonworks Data Platform 1.3][hdp-1-3-0]을 기반으로 하는 Hadoop 배포를 사용합니다.

* HDInsight 버전 1.6에서는 [Hortonworks Data Platform 1.1][hdp-1-1-0]을 기반으로 하는 Hadoop 배포를 사용합니다.

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 

<!---HONumber=AcomDC_0914_2016-->