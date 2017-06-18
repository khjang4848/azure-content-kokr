<properties
   pageTitle="HDInsight|Microsoft Azure에서 Hadoop Pig 사용"
   description="HDInsight에서 Hadoop와 함께 Pig를 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# HDInsight에서 Hadoop과 Pig 사용

[AZURE.INCLUDE [pig-선택기](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/)는 *Pig Latin*이라는 절차형 언어를 사용하여 Hadoop용 프로그램을 생성하기 위한 플랫폼입니다. Pig는 *MapReduce*을 만드는 Java를 대체하는 솔루션이며 Azure HDInsight와 함께 포함됩니다.

이 문서에서는 HDInsight와 함께 Pig를 사용하는 방법을 배웁니다.

##<a id="why"></a>Pig를 사용하는 이유

Hadoop의 MapReduce를 사용하여 데이터를 처리할 때 문제는 지도 및 reduce 함수만을 사용하여 처리 논리를 구현하는 것입니다. 복잡한 처리를 위해 종종 처리를 같이 연결된 여러 MapReduce 작업으로 나누어야 원하는 결과를 얻을 수 있습니다.

사용자가 맵만 사용하여 기능을 줄이는 대신 Pig를 사용하면 원하는 출력을 생산하기 위해 데이터가 통과하는 일련의 변환으로 처리를 규정할 수 있게 됩니다.

원하는 출력을 생산하기 위해 Pig Litin 언어를 사용하여 하나 이상의 변환을 거쳐 원시 입력을 데이터 플로로 설명할 수 있습니다. Pig Latin 프로그램이 일반적인 패턴을 따릅니다:

- **부하**: 파일 시스템에서 조작할 데이터 읽기
- **변환**: 데이터 조작
- **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장

또한 Pig Latin는 사용자가 Pig Latin의 모델에 어려운 논리를 구현하는 외부 구성 요소를 적용할 수 있도록 사용자 정의 함수(UDF)를 지원합니다.

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) 및 [Pig Latin 참조 설명서 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)를 참조하십시오.

Pig와 UDF를 사용하는 예로, 다음 문서를 참조하십시오:

* [HDInsight에서 HDInsight와 함께 Pig 사용](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu는 Apache에서 유지 관리하는 유용한 UDF의 컬렉션입니다.

* [HDInsight에서 Pig 및 Hive와 함께 Python 사용](hdinsight-python.md)

* [HDInsight에서 Hive 및 Pig와 함께 C# 사용](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>샘플 데이터 정보

해당 예제는 사용자의 blob 저장 컨테이너의 **/example/data/sample.log**에 저장된 *log4j* 샘플 파일을 사용합니다. 파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]` 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

이전 예에서 로그 수준은 ERROR입니다.

> [AZURE.NOTE] [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 도구 로깅하여 log4j 파일을 생성하고 해당 파일을 사용자의 blob에 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드](hdinsight-upload-data.md)를 참조하세요. HDInsight와 함께 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

샘플 데이터는 HDInsight가 Hadoop 클러스터의 기본 파일 시스템으로 사용하는 Azure Blob 저장소에 저장됩니다. HDInsight에서는 **wasb** 접두사를 사용하여 Blob 저장소에 저장된 파일에 액세스할 수 있습니다. 예를 들어 sample.log 파일에 액세스하려는 경우 다음 구문을 사용합니다.

	wasbs:///example/data/sample.log

WASB가 HDInsight의 기본 저장소이므로 Pig Latin에서 **/example/data/sample.log**를 사용하여 파일에 액세스할 수도 있습니다.

> [AZURE.NOTE] 구문 **wasbs:///**는 HDInsight 클러스터의 기본 저장소 컨테이너에 저장된 파일에 액세스하는 데 사용됩니다. 클러스터를 프로비전할 때 추가 저장소 계정을 지정한 경우 이러한 계정에 저장된 파일에 액세스하려면 컨테이너 이름과 저장소 계정 주소를 지정하여 데이터에 액세스하면 됩니다. 예를 들어 **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**와 같습니다.


##<a id="job"></a>샘플 앱 정보

다음 Pig Latin 작업은 HDInsight 클러스터의 기본 저장소에서 **sample.log** 파일을 로드합니다. 그런 다음 입력된 데이터에서 각 로그 수준이 발생한 횟수로 나타나는 일련의 변환을 수행합니다. 결과는 STDOUT에 버려집니다.

	LOGS = LOAD 'wasbs:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

다음 그림에서는 각 변환이 데이터에서 보이는 고장을 보여줍니다.

![변환의 그래픽 표현][image-hdi-pig-data-transformation]

##<a id="run"></a>Pig Latin 작업 실행

HDInsight는 다양한 메서드를 사용하여 Pig Latin 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르십시오.

| 원하는 경우 **이것을 사용**하세요... | ...**대화형** 셸 | ...**배치** 처리 | ...**클러스터 운영 체제**로 | ...**클라이언트 운영 체제**에서 |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux 또는or Windows | Linux, Unix, Mac OS X, 또는 Windows |
| [Hadoop용 .NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux 또는or Windows | Windows(당분간) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux 또는or Windows | Windows |
| [원격 데스크톱](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |


## 온-프레미스 SQL Server Integration Services를 사용하여 Azure HDInsight에서 Pig 작업 실행

SSIS(SQL Server Integration Services)를 사용하여 Pig 작업을 실행할 수도 있습니다. Azure Feature Pack for SSIS는 HDInsight에서 Pig 작업을 하는 다음 구성 요소를 제공합니다.


- [Azure HDInsight Pig 작업][pigtask]
- [Azure 구독 연결 관리자][connectionmanager]


[여기][ssispack]서 Azure Feature Pack for SSIS에 대해 자세히 알아보세요.


##<a id="nextsteps"></a>다음 단계

Scalding을 사용하여 HDInsight와 함께 Pig를 사용하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Sqoop 사용](hdinsight-use-sqoop.md)
* [HDInsight에서 Oozie 사용](hdinsight-use-oozie.md)
* [HDInsight에서 MapReduce 작업 사용][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=AcomDC_0914_2016-->