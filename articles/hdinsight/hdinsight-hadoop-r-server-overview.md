<properties
	pageTitle="HDInsight의 R이란? HDInsight의 R 서버 소개(미리 보기) | Microsoft Azure"
	description="HDInsight(미리 보기)의 R 서버에 대한 정의 및 빅 데이터 분석용 응용 프로그램을 만드는 데 R 서버를 사용하는 방법입니다."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# HDInsight의 R 서버 개요(미리 보기)

Microsoft Azure HDInsight Premium을 사용하는 경우 Azure에서 HDInsight 클러스터를 만들 때 Microsoft R 서버를 옵션으로 사용할 수 있습니다. 이 새로운 기능은 데이터 과학자, 통계학자 및 R 프로그래머에게 HDInsight의 확장 가능한 분산형 분석 방법에 요청 시 액세스할 수 있도록 해줍니다.

클러스터 크기를 프로젝트 및 작업에 맞게 손쉽게 조정하고 더 이상 필요 없을 때 클러스터를 폐기할 수 있습니다. 이러한 클러스터는 Azure HDInsight의 일부이므로 엔터프라이즈 수준의 연중무휴 24시간 지원, 99.9% 가동 시간의 SLA, Azure 에코시스템의 다른 구성 요소와 통합할 수 있는 유연성이 제공됩니다.

>[AZURE.NOTE] R 서버는 HDInsight Premium에서만 사용할 수 있습니다. 또한 HDInsight Premium은 현재 Hadoop 및 Spark 클러스터에만 사용할 수 있습니다. 따라서 HDInsight의 Hadoop 및 Spark 클러스터에서만 R 서버를 사용할 수 있습니다. 자세한 내용은 [HDInsight에서 사용할 수 있는 다양한 서비스 수준 및 Hadoop 구성 요소](hdinsight-component-versioning.md)를 참조하세요.

HDInsight의 R 서버는 Azure Blob 저장소에 로드된 대규모 데이터 집합에 대한 R 기반 분석을 위한 최신 기능을 제공합니다. R 서버는 오픈 소스 R을 기반으로 하기 때문에 빌드한 R 기반 응용 프로그램에서 8000 이상의 오픈 소스 R 패키지와 R 서버에 포함된 Microsoft의 빅 데이터 분석 패키지인 ScaleR의 루틴을 활용할 수 있습니다.

Premium 클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 장소를 제공합니다. 에지 노드를 사용하는 경우 에지 노드 서버의 코어에서 ScaleR의 병렬화된 분산 함수를 실행하는 옵션이 제공됩니다. 또한 ScaleR의 Hadoop Map Reduce 또는 Spark 계산 컨텍스트를 사용하여 클러스터의 노드에서 함수를 실행하는 옵션도 제공됩니다.

분석 결과에서 얻은 모델 또는 예측을 온-프레미스 용도로 다운로드할 수 있습니다. [Azure 기계 학습 스튜디오](http://studio.azureml.net) [웹 서비스](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md) 등을 통해 Azure의 다른 곳에서 작동할 수도 있습니다.

## HDInsight에서 R 시작

Azure 포털을 사용하여 클러스터를 만들 때 HDInsight 클러스터에 R 서버를 포함하려면 Premium 옵션을 사용하여 Hadoop 또는 Spark 클러스터를 만들어야 합니다. 두 클러스터 유형 모두 클러스터의 데이터 노드에 R 서버를 포함하는 동일한 구성을 사용하며, R 서버 기반 분석의 연결 영역으로 에지 노드를 사용합니다. 클러스터 만들기에 대한 자세한 연습은 [HDInsight에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)을 참조하세요.

## 데이터 저장소 옵션에 대해 자세히 알아보기

HDInsight 클러스터에 대한 기본 저장소는 HDFS 파일 시스템이 Blob 컨테이너에 매핑된 Blob 저장소입니다. 따라서 분석하는 동안 클러스터 저장소에 업로드되거나 클러스터 저장소에 작성된 모든 데이터가 영구적으로 설정됩니다. Blob 간에 데이터를 복사하는 데 [AzCopy](../storage/storage-use-azcopy.md) 유틸리티를 사용할 수 있습니다.

Blob 저장소를 사용하는 것 외에, 클러스터에 [Azure Data Lake 저장소](https://azure.microsoft.com/services/data-lake-store/)를 사용하는 옵션이 있습니다. Data Lake를 사용하는 경우 HDFS 저장소에 대해 Blob 저장소와 Data Lake를 모두 사용할 수 있습니다.

[Azure 파일](../storage/storage-how-to-use-files-linux.md)을 에지 노드용 저장소 옵션으로 사용할 수도 있습니다. Azure 파일을 사용하면 Azure 저장소에서 만든 파일 공유를 Linux 파일 시스템에 마운트할 수 있습니다. HDInsight 클러스터의 R 서버에 대한 데이터 저장소 옵션에 대한 자세한 내용은 [HDInsight 클러스터의 R 서버에 대한 저장소 옵션](hdinsight-hadoop-r-server-storage.md)을 참조하세요.

## 클러스터에서 R 서버에 액세스

R 서버를 사용하여 클러스터를 만든 후에는 SSH/PuTTY를 통해 클러스터의 에지 노드에서 R 콘솔에 연결할 수 있습니다. 에지 노드에 선택적으로 RStudio 서버 IDE를 설치하도록 선택한 경우 브라우저를 통해 연결할 수도 있습니다. RStudio 서버 설치에 대한 자세한 내용은 [HDInsight 클러스터에 RStudio 서버 설치](hdinsight-hadoop-r-server-install-r-studio.md)를 참조하세요.

## R 스크립트 개발 및 실행

사용자가 만들고 실행하는 R 스크립트는 ScaleR 라이브러리의 병렬화된 분산 루틴 외에 8000개 이상의 오픈 소스 R 패키지를 사용할 수 있습니다. 일반적으로 에지 노드의 R 서버에서 실행되는 스크립트는 해당 노드의 R 인터프리터 내에서 실행됩니다. Hadoop Map Reduce(RxHadoopMR) 또는 Spark(RxSpark)로 설정된 계산 컨텍스트에 ScaleR 함수를 호출하는 단계는 예외입니다.

이러한 경우 함수는 참조 데이터와 연결된 클러스터의 데이터(작업) 노드에서 분산된 방식으로 실행됩니다. 다양한 계산 컨텍스트 옵션에 대한 자세한 내용은 [HDInsight Premium의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)을 참조하세요.

## 모델 운영

데이터 모델링이 완료되면 Azure와 온-프레미스 모두에서 새 데이터를 예측하는 모델을 운영할 수 있습니다. 이 프로세스를 점수 매기기라고 합니다. 몇 가지 예를 들면 다음과 같습니다.

### HDInsight에서 점수 매기기

HDInsight에서 점수를 매기려면 모델을 호출하여 저장소 계정에 로드한 새 데이터 파일에 대해 예측하는 R 함수를 작성합니다. 그런 다음 예측을 저장소 계정에 다시 저장합니다. 클러스터의 에지 노드에서 요청 시 루틴을 실행하거나 예약된 작업을 사용하여 루틴을 실행할 수 있습니다.

### Azure 기계 학습에서 점수 매기기

Azure Machine Learning 웹 서비스를 사용하여 점수를 매기려면 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)로 알려진 오픈 소스 Azure Machine Learning R 패키지를 사용하여 모델을 Azure 웹 서비스로 게시합니다. 이 패키지는 편의상 에지 노드에 미리 설치됩니다. 다음으로, 기계 학습의 기능을 사용하여 웹 서비스에 대한 사용자 인터페이스를 만든 후 점수 매기기에 필요한 대로 웹 서비스를 호출합니다.

이 옵션을 선택한 경우 ScaleR 모델 개체를 웹 서비스에서 사용할 동등한 오픈 소스 모델 개체로 변환해야 합니다. 이 작업은 앙상블 기반 모델에 대해 ScaleR 강제 변환 함수(예: `as.randomForest()`)를 통해 수행할 수 있습니다.


### 온-프레미스 점수 매기기

모델을 만든 후 온-프레미스에서 점수를 매기려면 R에서 모델을 serialize하여 다운로드하고 deserialize한 다음 새 데이터의 점수 매기기에 사용합니다. 앞서 [HDInsight에서 점수 매기기](#scoring-in-hdinsight)에서 설명한 접근 방식을 사용하거나 [DeployR](https://deployr.revolutionanalytics.com/)을 사용하여 새 데이터의 점수를 매길 수 있습니다.

## 클러스터 유지 관리

### R 패키지 설치 및 유지 관리

사용하는 대부분의 R 패키지는 R 스크립트의 대부분이 에지 노드에서 실행되므로 에지 노드에 필요합니다. 에지 노드에 추가 R 패키지를 설치하려면 R에서 일반적인 `install.packages()` 메서드를 사용할 수 있습니다.

클러스터에 ScaleR 라이브러리의 루틴을 사용하는 경우에는 대부분 데이터 노드에 추가 R 패키지를 설치할 필요가 없습니다. 그러나 데이터 노드에서 **rxExec** 또는 **RxDataStep** 실행을 사용하도록 지원하려면 추가 패키지가 필요할 수 있습니다.

이 경우 클러스터를 만든 후 스크립트 동작을 통해 이러한 추가 패키지를 지정해야 합니다. 자세한 내용은 [R 서버를 사용하여 HDInsight 클러스터 만들기](hdinsight-hadoop-r-server-get-started.md)를 참조하세요.

### Hadoop Map Reduce 메모리 설정 변경

Map Reduce 작업을 실행할 때 R 서버에 사용 가능한 메모리 양을 변경하기 위해 클러스터를 수정할 수 있습니다. 클러스터를 수정하려면 클러스터의 Azure 포털 블레이드를 통해 제공되는 Apache Ambari UI를 사용합니다. 클러스터의 Ambari UI에 액세스하는 방법에 대한 지침은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

다음과 같이 **RxHadoopMR**에 대한 호출에서 Hadoop 스위치를 사용하여 R 서버에 사용 가능한 메모리 양을 변경할 수도 있습니다.

	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### 클러스터 규모 조정

포털을 통해 기존 클러스터를 확장하거나 축소할 수 있습니다. 규모를 조정하여 더 큰 처리 작업에 필요한 추가 용량을 얻거나 유휴 상태일 때 클러스터 크기를 다시 조정할 수 있습니다. 클러스터 크기를 조정하는 방법에 대한 지침은 [HDInsight 클러스터 관리](hdinsight-administer-use-portal-linux.md)를 참조하세요.

### 시스템 유지 관리

유지 관리는 업무 외 시간 중에 OS 패치 및 기타 업데이트를 적용하는 등의 작업을 수행하기 위해 HDInsight 클러스터의 기본 Linux VM에서 수행됩니다. 일반적으로 유지 관리는 월요일과 목요일마다 오전 3시 30분(VM의 로컬 시간에 따라)에 수행됩니다. 업데이트는 한 번에 클러스터의 ¼ 이상에 영향을 주지 않는 방식으로 수행됩니다.

헤드 노드가 중복되어 있고 모든 데이터 노드가 영향을 받는 것은 아니므로 이 시간 중에 실행되는 작업은 속도가 느려질 수 있습니다. 하지만 완료될 때까지 계속 실행되어야 합니다. 모든 사용자 지정 소프트웨어 또는 로컬 데이터는 클러스터를 다시 빌드해야 하는 심각한 오류가 발생하지 않는 한 이러한 유지 관리에서 보존됩니다.

## HDInsight 클러스터의 R 서버에 대한 IDE 옵션에 대해 자세히 알아보기

HDInsight Premium 클러스터의 Linux 에지 노드는 R 기반 분석의 연결 영역입니다. 클러스터에 연결한 후 Linux 명령 프롬프트에 **R**을 입력하여 R 서버에 대한 콘솔 인터페이스를 시작할 수 있습니다. R 스크립트 개발용 텍스트 편집기를 다른 창에서 실행하고 필요에 따라 스크립트의 섹션을 복사하여 R 콘솔에 붙여 넣는 경우 콘솔 인터페이스의 사용이 개선됩니다.

R 스크립트를 개발하기 위한 보다 복잡한 도구는 데스크톱에서 사용할 R 기반 IDE(예: Microsoft에서 최근 발표한 RTVS([Visual Studio용 R 도구](https://www.visualstudio.com/ko-KR/features/rtvs-vs.aspx)))입니다. [RStudio](https://www.rstudio.com/products/rstudio-server/)의 데스크톱 및 서버 도구 제품군입니다. Walware의 Eclipse 기반 [StatET](http://www.walware.de/goto/statet)를 사용할 수도 있습니다.

또 다른 옵션은 Linux 에지 노드 자체에 IDE를 설치하는 것입니다. 일반적으로 [RStudio 서버](https://www.rstudio.com/products/rstudio-server/)를 선택하며 이는 원격 클라이언트에서 사용할 브라우저 기반 IDE를 제공합니다. RStudio 서버를 HDInsight Premium 클러스터의 에지 노드에 설치하면 클러스터의 R 서버를 사용하여 R 스크립트를 개발 및 실행할 수 있는 완벽한 IDE 환경이 제공됩니다. R 콘솔보다 생산성을 크게 높일 수 있습니다. RStudio 서버를 사용하려는 경우 [HDInsight 클러스터에 RStudio 서버 설치](hdinsight-hadoop-r-server-install-r-studio.md)를 참조하세요.

## 가격 책정에 대해 알아보기

R 서버가 포함된 HDInsight Premium 클러스터와 연관된 요금은 표준 HDInsight 클러스터와 유사한 방식으로 구성됩니다. Premium에 대한 코어 시간 증가와 함께 이름, 데이터 및 에지 노드에서 기본 VM의 크기를 기반으로 합니다. 공개 미리 보기 중의 가격 책정 및 30일 무료 평가판 제공 등을 포함하여 HDInsight Premium 가격 책정에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## 다음 단계

HDInsight 클러스터에서 R 서버를 사용하는 방법에 대한 자세한 내용은 아래 링크를 참조하세요.

- [HDInsight에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)

- [HDInsight Premium에 RStudio 서버 추가](hdinsight-hadoop-r-server-install-r-studio.md)

- [HDInsight의 R 서버에 대한 계산 컨텍스트 옵션(미리 보기)](hdinsight-hadoop-r-server-compute-contexts.md)

- [HDInsight Premium의 R 서버에 대한 Azure 저장소 옵션](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0914_2016-->