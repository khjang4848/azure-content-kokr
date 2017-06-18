<properties
   pageTitle="샘플 갤러리를 사용하여 HDInsight에서 Hadoop에 대해 알아보기 | Microsoft Azure"
   description="HDInsight 시작 갤러리에서 샘플 응용 프로그램을 실행하여 Hadoop을 학습하는 빠른 방법을 제공합니다. 샘플 데이터를 사용하거나 직접 제공합니다."
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# Azure HDInsight 시작 갤러리를 사용하여 Hadoop에 대해 알아보기

시작 갤러리는 Windows 기반 HDInsight 클러스터에서만 사용할 수 있습니다. 갤러리는 HDInsight에서 샘플 응용 프로그램을 실행하여 쉽고 빠르게 Hadoop을 학습하는 방법을 제공합니다. 일부 샘플이 샘플 데이터와 함께 제공됩니다. 샘플의 나머지 부분에 자신만의 데이터를 제공할 수 있습니다. 현재는 다음 6개의 샘플이 있습니다(향후 추가 예정).

- Azure 데이터를 사용한 솔루션
	- Microsoft Azure 웹사이트 로그 분석
	- Microsoft Azure 저장소 분석
- 샘플 데이터를 사용한 솔루션
	- 센서 데이터 분석
	- Twitter 추세 분석
	- 웹 사이트 로그 분석
	- Mahout 동영상 권장 사항

![샘플 데이터가 포함된 HDInsight Hadoop, Storm 및 HBase 시작 갤러리 솔루션][hdinsight.sample.gallery]

다음 동영상에서는 Twitter 추세 분석 샘플을 실행하는 방법을 보여 줍니다.

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

대시보드는 http://<YourHDInsightClusterName>.azurehdinsight.net/으로 이동하거나 Azure 포털에서 액세스할 수 있습니다.

**시작 갤러리에서 샘플을 실행하려면**

1. [Azure 포털][azure.portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭한 다음 클러스터 이름을 클릭합니다.
3. 위쪽 메뉴에서 **대시보드**를 클릭합니다.
4. HTTP 사용자(클러스터 사용자라고도 함)에 대한 사용자 이름 및 암호를 입력합니다.
6. 페이지 위쪽에서 **시작 갤러리**를 클릭합니다.
7. 샘플 중 하나를 클릭합니다. 각 샘플은 자세한 실행 단계를 알려줍니다. 다음 이미지에서는 Twitter 추세 분석 샘플을 보여 줍니다.

	![HDInsight Twitter 추세 분석 샘플][hdinsight.twitter.sample]

## 다음 단계
HDInsight에 대해 알아볼 수 있는 다른 방법은 다음과 같습니다.

- [HDInsight 학습 맵][hdinsight.learn.map]
- [HDInsight 인포그래픽][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->