<properties
	pageTitle="Linux 기반 HDInsight에서 R 설치 | Microsoft Azure"
	description="R을 설치하고 사용하여 Linux 기반 Hadoop 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="larryfr"/>

# HDInsight Hadoop 클러스터에 R 설치 및 사용

**스크립트 작업** 클러스터 사용자 지정을 사용하여 HDInsight의 Hadoop에서 모든 유형의 클러스터에 R을 설치할 수 있습니다. 이렇게 하면 데이터 과학자 및 분석가가 R을 사용하여 HDInsight에 배포된 Hadoop 클러스터에서 많은 양의 데이터를 처리하기 위한 강력한 MapReduce/YARN 프로그래밍 프레임워크를 배포할 수 있습니다.

> [AZURE.IMPORTANT] HDInsight용 [Premium 계층](https://azure.microsoft.com/pricing/details/hdinsight/) 제품에는 HDInsight 클러스터의 일부로 R 서버가 포함됩니다. 이를 통해 R 스크립트에서 MapReduce 및 Spark를 사용하여 분산된 계산을 실행할 수 있습니다. 자세한 내용은 [HDInsight에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요.


## R이란?

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a>은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. R은 수백 개의 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 자체 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다. R은 다양한 분야에서 전문 통계학자와 과학자 대부분의 기본 프로그래밍 환경입니다.

R 스크립트는 클러스터를 만들 때 스크립트 작업을 사용하여 사용자 지정하여 R 환경을 설치한 HDInsight의 Hadoop 클러스터에서 실행할 수 있습니다. R은 Azure Blob 저장소(WASB)와 호환되므로 HDInsight의 R을 사용하여 이 저장소에 저장된 데이터를 처리할 수 있습니다.

## 스크립트가 수행하는 작업

HDInsight 클러스터에 R를 설치하는 데 사용되는 스크립트는 기본 R 설치를 제공하는 다음 Ubuntu 패키지를 설치합니다.

* [r-base](http://packages.ubuntu.com/precise/r-base): 기본 GNU R 패키지
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): 보조 GNU R 패키지

HDFS 및 MapReduce와의 통합을 제공하는다음 RHadoop 패키지도 설치됩니다.

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): R 개발자가 Hadoop MapReduce를 사용하도록 허용
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): R 개발자가 Hadoop HDFS(HDInsight의 경우 WASB)를 사용하도록 허용

또한 다음 R 패키지가 설치됩니다.

| R 패키지 | 제공하는 기능 |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | 하위 수준 R-Java 인터페이스 |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | R 및 C++ 통합 |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | R 개체를 JSON으로 직렬화/역직렬화 |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | 정수 벡터에 대한 비트 연산 함수입니다. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) | R 개체에 대해 암호화 해시 다이제스트를 만듭니다. |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, Compose 및 기타 자주 사용되는 함수 |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | 데이터를 유연하게 재구성하고 집계합니다. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | 일반적인 문자열 작업에 대한 단순하고 일관된 래퍼입니다. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | 데이터 분할, 적용 및 결합을 위한 도구입니다. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | 이동하는 창 통계를 위한 도구(GIF, Base64, ROC AUC 등)입니다. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | 문자열 일치 및 문자열 거리 함수의 값을 대략적으로 구합니다. |

## 스크립트 동작을 사용하여 R 설치

다음 스크립트 작업은 HDInsight 클러스터에 R을 설치하는 데 사용합니다. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    
이 섹션에서는 Azure 포털을 사용하여 새 클러스터를 만들 때 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE] Azure PowerShell, Azure CLI, HDInsight .NET SDK 또는 Azure Resource Manager 템플릿을 스크립트 동작을 적용하는 데 사용할 수도 있습니다. 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)의 단계를 사용하여 클러스터를 프로비전하되, 완료하지는 않도록 합니다.

2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래 정보를 제공합니다.

	* __이름__: 스크립트 동작의 이름을 입력합니다.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __HEAD__:이 옵션 선택
	* __WORKER__:이 옵션 선택
	* __ZOOKEEPER__: 이 옵션을 선택하여 Zookeeper 노드에 설치합니다.
	* __PARAMETERS__: 이 필드는 공백으로 둡니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 블레이드의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

4. [Linux 기반 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)에서 설명한 대로 클러스터를 계속 프로비전합니다.

## R 스크립트 실행

클러스터가 프로비전을 완료하면 다음 단계에 따라 R을 사용하여 클러스터에서 MapReduce 작업을 수행합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

	* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

2. `username@hn0-CLUSTERNAME:~$` 프롬프트가 표시되면 다음 명령을 입력하여 대화형 R 세션을 시작합니다.

		R

3. 다음 R 프로그램을 입력합니다. 이렇게 하면 1에서 100 사이의 숫자가 생성된 후 2로 곱해집니다.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

	첫 번째 줄은 MapReduce 작업에 사용되는 RHadoop 라이브러리 rmr2를 호출합니다.

	두 번째 줄은 1-100의 값을 생성한 다음 `to.dfs`를 사용하여 Hadoop 파일 시스템에 저장합니다.

	세 번째 줄은 rmr2에서 제공하는 기능을 사용하여 MapReduce 프로세스를 만들고 처리를 시작합니다. 처리가 시작되면 시작을 넘어가면 몇 개의 줄이 스크롤됩니다.

4. 이제 다음을 사용하여 MapReduce 출력에 저장된 임시 경로를 확인합니다.

		print(calc())

	`/tmp/file5f615d870ad2`과 비슷합니다. 실제 출력을 보려면 다음을 사용합니다.

		print(from.dfs(calc))

	출력은 다음과 같습니다.

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. R을 끝내려면 다음을 입력합니다.

		q()


## 다음 단계

- [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)입니다. Hue는 기본 저장소에서 HDInsight 클러스터를 쉽게 찾을 뿐만 아니라 Pig 및 Hive 작업을 편리하게 만들고 실행하고 저장할 수 있도록 하는 웹 UI입니다.

- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

- [HDInsight 클러스터에서 Hue를 설치](hdinsight-hadoop-hue-linux.md)합니다. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 Hue를 설치합니다. Hue는 Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 응용 프로그램 집합입니다.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->