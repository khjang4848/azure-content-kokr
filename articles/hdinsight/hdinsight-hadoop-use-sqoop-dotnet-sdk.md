<properties
	pageTitle="HDInsight에서 Hadoop Sqoop 사용 | Microsoft Azure"
	description="HDInsight .NET SDK를 사용하여 Hadoop 클러스터와 Azure SQL 데이터베이스 사이에서 Sqoop 가져오기 및 내보내기를 실행하는 방법을 알아봅니다."
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
   ms.date="09/14/2016"
	ms.author="jgao"/>

#HDInsight에서 Hadoop용 .NET SDK를 사용하여 Sqoop 작업 실행

[AZURE.INCLUDE [sqoop 선택기](../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight에서 HDInsight .NET SDK를 사용하여 HDInsight 클러스터와 Azure SQL 데이터베이스 또는 SQL Server 데이터베이스 사이에서 가져오기 및 내보내는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 단계는 Windows 기반 또는 Linux 기반 HDInsight 클러스터와 사용할 수 있지만 이 단계에서만 Windows 클라이언트에서 작동합니다. 이 문서의 맨 위에 있는 탭 선택기를 사용하여 다른 방법을 선택합니다.

###필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **HDInsight에 Hadoop 클러스터**. [클러스터 및 SQL 데이터베이스 만들기](hdinsight-use-sqoop.md#create-cluster-and-sql-database)를 참조하세요.

## .NET SDK를 사용하여 Sqoop 실행

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 섹션에서는 C# 콘솔 응용 프로그램을 만들어 이 자습서의 앞 부분에서 만든 SQL 데이터베이스 테이블에 hivesampletable 내보냅니다.

**Sqoop 작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. Visual Studio 패키지 관리자 콘솔에서 다음 Nuget 명령을 실행하여 패키지를 가져옵니다.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Program.cs 파일에서 다음 코드를 사용합니다.

        using System.Collections.Generic;
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
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. **F5** 키를 눌러 프로그램을 실행합니다.

##제한 사항

* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL 데이터베이스에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 현재 대량 삽입을 지원하지 않습니다.

* 배치 - Linux 기반 HDInsight와 함께 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 삽입 작업을 수행합니다.

##다음 단계

이제 Sqoop을 사용하는 방법에 대해 알아봤습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight와 함께 Oozie 사용](hdinsight-use-oozie.md): Oozie 워크플로에서 Sqoop 작업을 사용합니다.
- [HDInsight를 사용하여 비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data.md): Hive를 사용하여 비행 지연 데이터를 분석한 후 Sqoop을 사용하여 데이터를 Azure SQL 데이터베이스로 내보냅니다.
- [HDInsight에 데이터 업로드](hdinsight-upload-data.md): HDInsight/Azure Blob 저장소에 데이터를 업로드하는 다른 방법을 찾습니다.

<!---HONumber=AcomDC_0914_2016-->