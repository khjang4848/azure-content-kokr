<properties
	pageTitle="HDInsight에서 R을 사용하여 클러스터 사용자 지정 | Microsoft Azure"
	description="스크립트 동작을 사용하여 R을 설치하는 방법을 알아보고 HDInsight 클러스터에서 R을 사용합니다."
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
    ms.date="09/14/2016"
	ms.author="jgao"/>

# HDInsight Hadoop 클러스터에 R 설치 및 사용

스크립트 동작을 사용하여 R로 Windows 기반 HDInsight 클러스터를 사용자 지정하는 방법 및 HDInsight 클러스터에서 R을 사용하는 방법을 알아봅니다. HDInsight용 [Premium 계층](https://azure.microsoft.com/pricing/details/hdinsight/) 제품에는 HDInsight 클러스터의 일부로 R 서버가 포함됩니다. 이를 통해 R 스크립트에서 MapReduce 및 Spark를 사용하여 분산된 계산을 실행할 수 있습니다. 자세한 내용은 [HDInsight에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요. Linux 기반 클러스터와 함께 R을 사용한 작업에 대한 자세한 내용은 [HDInsight Hadoop 클러스터에 R 설치 및 사용(Linux)](hdinsight-hadoop-r-scripts-linux.md)을 참조하세요.
 
*스크립트 작업*을 사용하여 Azure HDInsight에서 모든 형식의 클러스터(Hadoop, Storm, HBase, Spark)에 R을 설치할 수 있습니다. HDInsight 클러스터에 R을 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1))에서 다운로드할 수 있습니다.

**관련된 문서**

- [HDInsight Hadoop 클러스터에 R 설치 및 사용(Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)

## R이란?

<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a>은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. R은 수백 개의 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 자체 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다. R은 다양한 분야에서 전문 통계학자와 과학자 대부분의 기본 프로그래밍 환경입니다.

R은 Azure Blob 저장소(WASB)와 호환되므로 HDInsight의 R을 사용하여 이 저장소에 저장된 데이터를 처리할 수 있습니다.

## R 설치

HDInsight 클러스터에 R을 설치하기 위한 [샘플 스크립트](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)는 Azure 저장소의 읽기 전용 Blob에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 포털을 사용하여 클러스터를 만드는 동안 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE] 샘플 스크립트는 HDInsight 클러스터 버전 3.1에서 도입되었습니다. HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.

1. 포털에서 HDInsight 클러스터를 만들 때 **선택적 구성**을 클릭한 다음 **스크립트 동작**을 클릭합니다.
2. **스크립트 동작** 페이지에서 다음 값을 입력합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름(예: <b>Install R</b>)을 지정합니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 대한 URI(예: <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i>)를 지정합니다.</td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>헤드 노드만</b> 또는 <b>작업자 노드만</b>을 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다. 그러나 R을 설치하는 스크립트는 매개 변수가 필요하지 않으므로 공백으로 둘 수 있습니다.</td></tr>
	</table>

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 만들기를 시작합니다.

스크립트를 사용하여 Azure PowerShell 또는 HDInsight.NET SDK로 HDInsight에 R을 설치할 수도 있습니다. 이 절차에 대한 자세한 내용은 이 문서의 뒷부분에 제공됩니다.

## R 스크립트 실행
이 섹션에서는 HDInsight를 사용하는 Hadoop 클러스터에서 R 스크립트를 실행하는 방법에 대해 설명합니다.

1. **클러스터에 대한 원격 데스크톱 연결 설정**: 포털에서 R을 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 연결합니다. 지침은 [RDP를 사용하여 HDInsight 클러스터에 연결](hdinsight-administer-use-management-portal.md#rdp)을 참조하세요.

2. **R 콘솔 열기**: R 설치에서는 R 콘솔에 대한 링크를 헤드 노드의 데스크톱에 놓습니다. 이 링크를 클릭하여 R 콘솔을 엽니다.

3. **R 스크립트 실행**: R 콘솔에서 R 스크립트를 붙여 넣고 선택한 후 Enter 키를 눌러 콘솔에서 직접 스크립트를 실행할 수 있습니다. 다음은 1에서 100까지의 숫자를 생성한 후 각 숫자에 2를 곱하는 간단한 예제 스크립트입니다.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

첫 번째 두 줄은 R이 설치된 RHadoop 라이브러리를 호출하고, 마지막 줄은 결과를 콘솔에 인쇄합니다. 출력은 다음과 같습니다.

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200


## Azure PowerShell을 사용하여 R 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)을 참조하세요. 샘플은 Azure PowerShell을 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)을 사용해야 합니다.

## .NET SDK를 사용하여 R 설치

[스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)을 참조하세요. 샘플은 .NET SDK를 사용하여 Spark를 설치하는 방법을 보여줍니다. 스크립트를 사용자 지정하여 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11)을 사용해야 합니다.


## 참고 항목

- [HDInsight Hadoop 클러스터에 R 설치 및 사용(Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight에서 Hadoop 클러스터 만들기](hdinsight-provision-clusters.md): HDInsight 클러스터를 만드는 방법에 대한 일반 정보입니다.
- [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize]\: 스크립트 동작을 사용하여 HDInsight 클러스터를 사용자 지정하는 데 대한 일반 정보입니다.
- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions.md)
- [HDInsight 클러스터에서 Spark 설치 및 사용][hdinsight-install-spark]\: Spark 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 Giraph 설치](hdinsight-hadoop-giraph-install.md): Giraph 설치에 대한 스크립트 작업 샘플입니다.
- [HDInsight 클러스터에서 Solr 설치](hdinsight-hadoop-solr-install-linux.md): Solr 설치에 대한 스크립트 작업 샘플입니다.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md

<!---HONumber=AcomDC_0914_2016-->