<properties
   pageTitle="HDInsight에서 .NET과 Hadoop Pig 사용 | Microsoft Azure"
   description="HDInsight에서 Hadoop로 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/06/2016"
   ms.author="larryfr"/>

#HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행

[AZURE.INCLUDE [pig-선택기](../../includes/hdinsight-selector-use-pig.md)]

이 문서는 HDInsight 클러스터의 Hadoop에 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 예시를 보여 줍니다.

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. Pig를 사용하면 일련의 데이터 변환을 모델링하여 MapReduce 작업을 만들 수 있습니다. HDInsight 클러스터에는 Pig 작업을 제출하는 기본 C# 응용 프로그램을 사용하는 방법에 대해 배웁니다.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

## 필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 또는 Linux 기반)
* Visual Studio 2012, 2013 또는 2015

## 응용 프로그램 만들기

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다.


1. Visual Studio 2012 또는 2013 열기
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.
3. 새 프로젝트에서 다음 값을 입력하거나 선택합니다.

	<table>
	<tr>
	<th>속성</th>
	<th>값</th>
	</tr>
	<tr>
	<th>Category</th>
	<th>Templates/Visual C#/Windows</th>
	</tr>
	<tr>
	<th>Template</th>
	<th>콘솔 응용 프로그램</th>
	</tr>
	<tr>
	<th>이름</th>
	<th>SubmitPigJob</th>
	</tr>
	</table>
4. **확인**을 클릭하여 프로젝트를 만듭니다.
5. **도구** 메뉴에서 **라이브러리 패키지 관리자** 또는 **Nuget 패키지 관리자**를 선택한 다음 **패키지 관리자 콘솔**을 선택합니다.
6. 콘솔에서 다음 명령을 실행하여 .NET SDK 패키지를 설치합니다.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다. 기존 코드를 다음으로 바꿉니다.

        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. **F5** 키를 눌러 응용 프로그램을 시작합니다.
8. **Enter** 키를 눌러 응용 프로그램을 종료합니다.

## 요약

이처럼 Hadoop용 .NET SDK를 사용하면 Pig 작업을 HDInsight 클러스터를 제출하고, 작업 상태를 모니터링할 수 있습니다.

## 다음 단계

HDInsight에서 Pig에 대한 일반 정보.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0914_2016-->