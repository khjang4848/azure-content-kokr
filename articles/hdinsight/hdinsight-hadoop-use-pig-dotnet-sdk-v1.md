<properties
   pageTitle="HDInsight에서 .NET과 Hadoop Pig 사용 | Microsoft Azure"
   description="HDInsight에서 Hadoop로 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/04/2016"
   ms.author="larryfr"/>

#HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행

[AZURE.INCLUDE [pig-선택기](../../includes/hdinsight-selector-use-pig.md)]

이 문서는 HDInsight 클러스터의 Hadoop에 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 예시를 보여 줍니다.

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. Pig를 사용하면 일련의 데이터 변환을 모델링하여 MapReduce 작업을 만들 수 있습니다. HDInsight 클러스터에는 Pig 작업을 제출하는 기본 C# 응용 프로그램을 사용하는 방법에 대해 배웁니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 Azure 클래식 포털을 사용합니다. 새 서비스를 만들 때 클래식 포털을 사용하지 않는 것이 좋습니다. Azure 포털의 장점에 대한 자세한 내용은 [Microsoft Azure 포털](https://azure.microsoft.com/features/azure-portal/)을 참조하세요.
>
> 또한 이 문서에는 HDInsight .NET SDK를 사용하는 방법에 대한 정보도 포함됩니다. 제공된 코드 조각은 ASM(Azure 서비스 관리)를 사용하여 HDInsight와 작동하고 현재 __사용되지 않는__ 명령을 기반으로 작성되었습니다. 이러한 명령은 2017년 1월 1일에 제거됩니다.
>
>ARM(Azure Resource Manager)을 사용하는 HDInsight .NET SDK 코드 조각과 함께 Azure 포털을 사용하는 이 문서의 버전에 대해서는 [HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행](hdinsight-hadoop-use-pig-dotnet-sdk.md)을 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 또는 Linux 기반)

* Visual Studio 2012 또는 2013

##<a id="certificate"></a>관리 인증서 만들기

Azure HDInsight에 대한 응용 프로그램을 인증하려면 자체 서명된 인증서를 만들고 개발 워크스테이션에 설치하여 Azure 구독에 업로드해야 합니다.

이 작업을 수행하는 방법에 대한 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.

> [AZURE.NOTE] 인증서를 만들 때 나중에 사용할 것이므로 친숙한 이름을 사용해야 합니다.

##<a id="subscriptionid"></a>구독 ID 찾기

각각의 Azure 구독은 구독 ID라는 GUID 값으로 식별됩니다. 이 값을 찾으려면 다음 단계를 이용합니다.

1. [Azure 관리 콘솔](https://manage.windowsazure.com/)을 방문합니다.

2. 포털 왼쪽에 있는 막대에서 **설정**을 선택합니다.

3. 페이지 오른쪽에 있는 정보에서 사용할 구독을 찾아 **구독 ID** 열에 해당 값을 기록해 둡니다.

나중에 사용할 것이므로 구독 ID를 저장합니다.

##<a id="create"></a>응용 프로그램 만들기

1. Visual Studio 2012, 2013 또는 2015 열기

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

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다. 기존 코드를 다음으로 바꿉니다.

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace HDInsightSubmitPigJobsDotNet
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					var ExistingClusterName = "<HDInsightClusterName>";
					var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
					var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
					var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		            // The Pig Latin statements to run
		            string queryString = "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		
		            HDInsightJobManagementClient _hdiJobManagementClient;
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            // Define the Pig job
		            var parameters = new PigJobSubmissionParameters()
		            {
		                Query = queryString,
		            };
		
		            System.Console.WriteLine("Submitting the Pig job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            Console.WriteLine("Press ENTER to continue ...");
		            Console.ReadLine();
		        }
		    }
		}

7. **F5** 키를 눌러 응용 프로그램을 시작합니다.
8. **Enter** 키를 눌러 응용 프로그램을 종료합니다.

##<a id="summary"></a>요약

이처럼 Hadoop용 .NET SDK를 사용하면 Pig 작업을 HDInsight 클러스터를 제출 하고, 작업 상태를 모니터링하며, 출력을 검색하는 .NET 응용 프로그램을 만들 수 있습니다.

##<a id="nextsteps"></a>다음 단계

HDInsight에서 Pig에 대한 일반 정보.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->