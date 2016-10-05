<properties 
   pageTitle=".NET SDK를 사용하여 Azure 데이터 레이크 분석 시작 | Azure" 
   description=".NET SDK를 사용하여 데이터 레이크 저장소 계정을 만들고, 데이터 레이크 분석 작업을 만들고, U-SQL로 작성된 작업을 제출하는 방법에 대해 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="edmaca"/>

# 자습서: .NET SDK를 사용하여 Azure 데이터 레이크 분석 시작

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Azure .NET SDK를 사용하여 [U-SQL](data-lake-analytics-u-sql-get-started.md)로 작성된 작업을 Data Lake Analytics에 제출하는 방법을 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

이 자습서에서는 TSV(탭으로 구분된 값) 파일을 읽어 CSV(쉼표로 구분된 값) 파일로 변환하는 U-SQL 작업을 제출하는 C# 콘솔 응용 프로그램을 개발합니다. 지원되는 다른 도구를 사용하여 같은 자습서를 진행하려면 이 문서의 맨 위에 있는 탭을 클릭하세요.

##필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012와 Visual C++ 설치**.
- **.NET 버전 2.5 이상용 Microsoft Azure SDK**. [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
- **Azure 데이터 레이크 분석 계정**. [Azure .NET SDK를 사용한 Data Lake Analytics 관리](data-lake-analytics-manage-use-dotnet-sdk.md)를 참조하세요.

##콘솔 응용 프로그램 만들기

이 자습서에서는 몇 가지 검색 로그를 처리합니다. 검색 로그는 데이터 레이크 저장소 또는 Azure Blob 저장소에 저장할 수 있습니다.

샘플 검색 로그는 공용 Azure Blob 컨테이너에 있습니다. 응용 프로그램에서 파일을 사용자의 워크스테이션에 다운로드한 다음 Data Lake Analytics 계정의 기본 Data Lake Store 계정에 업로드합니다.

**응용 프로그램을 만들려면**

1. Visual Studio를 엽니다.
2. C# 콘솔 응용 프로그램을 만듭니다.
3. NuGet 패키지 관리 콘솔을 열고 다음 명령을 실행합니다.

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage

4. 새 파일을 **SampleUSQLScript.txt**라는 프로젝트에 추가한 후 다음 U-SQL 스크립트를 붙여넣습니다. 데이터 레이크 분석 작업은 U-SQL 언어로 작성됩니다. U-SQL에 대한 자세한 내용은 [U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md) 및 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/?LinkId=691348)를 참조하세요.

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	이 U-SQL 스크립트는 **Extractors.Tsv()**를 사용하여 원본 데이터 파일을 읽은 다음 **Outputters.Csv()**를 사용하여 csv 파일을 만듭니다.
    
    C# 프로그램에서 **/Samples/Data/SearchLog.tsv** 파일 및 **/Output/** 폴더를 준비해야 합니다.
	
	기본 데이터 레이크 계정에 저장된 파일의 상대 경로를 사용하는 것이 더 쉽습니다. 절대 경로를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    연결된 저장소 계정의 파일에 액세스하려면 절대 경로를 사용해야 합니다. 연결된 Azure 저장소 계정에 저장된 파일에 대한 구문은 다음과 같습니다.
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] 현재 Azure Data Lake 서비스와 관련된 알려진 문제가 있습니다. 샘플 앱이 중단되거나 오류가 발생할 경우, 스크립트가 생성하는 Data Lake 저장소 및 Data Lake 분석 계정을 수동으로 삭제해야 할 수 있습니다. 포털에 친숙하지 않다면 [Azure Portal을 사용한 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md) 가이드로 시작해 보세요.
       
5. Program.cs에서 다음 코드를 붙여넣습니다.

		using System;
		using System.IO;
		using System.Collections.Generic;
		using System.Threading;
		using Microsoft.Rest;
		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.WindowsAzure.Storage.Blob;

		namespace SdkSample
		{
		  class Program
		  {
			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
			private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;
			private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
			private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		
		    private static void Main(string[] args)
		    {
				string localFolderPath = @"c:\temp";

				// Connect to Azure
				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				SetupClients(creds, SUBSCRIPTIONID);

				// Transfer the source file from a public Azure Blob container to Data Lake Store.
				CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
				blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
				UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
				WaitForNewline("Source data file prepared.", "Submitting a job.");


				// Submit the job
				Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
				WaitForNewline("Job submitted.", "Waiting for job completion.");

				// Wait for job completion
				WaitForJob(jobId);
				WaitForNewline("Job completed.", "Downloading job output.");

				// Download job output
				DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		
		      	WaitForNewline("Job output downloaded. You can now exit.");
		    }
		
			public static ServiceClientCredentials AuthenticateAzure(
				string domainName,
				string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
			{
				_adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
				_adlaClient.SubscriptionId = subscriptionId;

				_adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

				_adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
			}

			public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
			{
				var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
				var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
				var uploader = new DataLakeStoreUploader(parameters, frontend);
				uploader.Execute();
			}

			public static void DownloadFile(string srcPath, string destPath)
			{
				var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
				var fileStream = new FileStream(destPath, FileMode.Create);

				stream.CopyTo(fileStream);
				fileStream.Close();
				stream.Close();
			}

			// Helper function to show status and wait for user input
			public static void WaitForNewline(string reason, string nextAction = "")
			{
				Console.WriteLine(reason + "\r\nPress ENTER to continue...");

				Console.ReadLine();

				if (!String.IsNullOrWhiteSpace(nextAction))
					Console.WriteLine(nextAction);
			}


			// List all Data Lake Analytics accounts within the subscription
			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
				for (int i = 0; i < accounts.Count; i++)
				{
					Console.WriteLine(accounts[i].Name);
				}

				return accounts;
			}
			public static Guid SubmitJobByPath(string scriptPath, string jobName)
			{
				var script = File.ReadAllText(scriptPath);

				var jobId = Guid.NewGuid();
				var properties = new USqlJobProperties(script);
				var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
				var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

				return jobId;
			}

			public static JobResult WaitForJob(Guid jobId)
			{
				var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				while (jobInfo.State != JobState.Ended)
				{
					jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				}
				return jobInfo.Result.Value;
			}
		  }
		}

6. **F5** 키를 눌러 응용 프로그램을 실행합니다. 출력은 다음과 같습니다.

	![Azure Data Lake Analytics 작업 U-SQL .NET SDK 출력](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. 출력 파일을 확인합니다. 기본 경로 및 파일 이름은 c:\\Temp\\SearchLog-from-Data-Lake.csv입니다.

## 참고 항목

- 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
- 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
- U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- U-SQL에 대해 알아보려면 [Azure Data Lake 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md) 및 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/?LinkId=691348)를 참조하세요.
- 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
- 데이터 레이크 분석에 대한 개요를 보려면 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0928_2016-->