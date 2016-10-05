<properties
	pageTitle="스트림 분석용 관리 .NET SDK | Microsoft Azure"
	description="스트림 분석 관리 .NET SDK를 시작합니다. 분석 작업 설정 및 실행 방법 알아보기: 프로젝트, 입력, 출력 및 변환을 만듭니다."
	keywords=".net SDK, 분석 API"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>


# 관리 .NET SDK: .NET용 Azure 스트림 분석 API를 사용하여 분석 작업 설정 및 실행

관리 .NET SDK에서 .NET용 Azure 스트림 분석 API를 사용하여 분석 작업을 설정 및 실행하는 방법을 알아봅니다. 프로젝트를 설정하고, 입출력 소스를 만들고, 변환하고, 작업을 시작 및 중지합니다. 분석 작업에 대해 Blob 저장소 또는 이벤트 허브에서 데이터를 스트리밍할 수 있습니다.

[.NET용 스트림 분석 API에 대한 관리 참조 설명서](https://msdn.microsoft.com/library/azure/dn889315.aspx)를 참조하세요.

Azure 스트림 분석은 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. 스트림 분석 기능은 고객이 데이터 스트림을 분석하도록 스트리밍 작업을 설정하고 거의 실시간으로 분석할 수 있도록 해 줍니다.


## 필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

- Visual Studio 2012 또는 2013 설치
- [Azure .NET SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 설치합니다.
- 구독에서 Azure 리소스 그룹을 만듭니다. 다음은 샘플 Azure PowerShell 스크립트입니다. Azure PowerShell 정보는 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.


		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

			# If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
			#Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-	사용하려는 입력 소스 및 출력 대상을 설정합니다. 샘플 입력 설정 방법에 대한 자세한 지침은 [입력 추가](stream-analytics-add-inputs.md)를 참조하고 샘플 출력 설정 방법에 대한 자세한 지침은 [출력 추가](stream-analytics-add-outputs.md)를 참조하세요.


## 프로젝트 설정

.NET용 스트림 분석 API를 사용하여 분석 작업을 만들려면 먼저 프로젝트를 설정합니다.

1. Visual Studio C# .NET 콘솔 응용 프로그램을 만듭니다.
2. 패키지 관리자 콘솔에서 NuGet 패키지를 설치하려면 다음 명령을 실행합니다. 첫 번째는 Azure 스트림 분석 관리.NET SDK입니다. 두 번째는 인증에 사용되는 Azure Active Directory 클라이언트 인증입니다.

		Install-Package Microsoft.Azure.Management.StreamAnalytics
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. 다음 **appSettings** 섹션을 App.config 파일에 추가합니다.

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
		  <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
		</appSettings>


	**SubscriptionId**와 **ActiveDirectoryTenantId**의 값을 Azure 구독 및 테넌트 ID로 바꿉니다. 다음 Azure PowerShell cmdlet을 실행하여 이러한 값을 얻을 수 있습니다.

		Get-AzureAccount

5. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	인증 도우미 메서드를 추가합니다.

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior: PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });

		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();

		    if (result != null)
		    {
		        return result.AccessToken;
		    }

		    throw new InvalidOperationException("Failed to acquire token");
		}  


## 스트림 분석 관리 클라이언트 만들기

**StreamAnalyticsManagementClient** 개체를 사용하면 입력, 출력 및 변환 등의 작업 구성 요소와 작업을 관리할 수 있습니다.

**Main** 메서드의 시작에 다음 코드를 추가합니다.

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());

	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**resourceGroupName** 변수의 값은 이전 단계에서 만들거나 선택한 리소스 그룹의 이름과 동일해야 합니다.

작업 만들기의 자격 증명 프레젠테이션 측면을 자동화하려면 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](../resource-group-authenticate-service-principal.md)을 참조하세요.

이 문서의 나머지 섹션에서는 이 코드가 **Main** 메서드의 시작 부분에 있다고 가정합니다.

## 스트림 분석 작업 만들기

다음 코드는 사용자가 정의한 리소스 그룹 아래 스트림 분석 작업을 만듭니다. 나중에 입력, 출력 및 변환을 작업에 추가합니다.

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};

	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## 스트림 분석 입력 소스 만들기

다음 코드는 blob 입력 소스 형식 및 CSV serialization으로 스트림 분석 입력 소스를 만듭니다. 이벤트 허브 입력 소스를 만들려면 **BlobStreamInputDataSource** 대신 **EventHubStreamInputDataSource**를 사용합니다. 마찬가지로, 입력 소스의 serialization 형식을 사용자 지정할 수 있습니다.

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};

	InputCreateOrUpdateResponse inputCreateResponse =
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Blob 저장소 또는 이벤트 허브의 입력 소스는 특정 작업에 연결됩니다. 다른 작업에 대해 동일한 입력 소스를 사용하려면, 메서드를 다시 호출하고 다른 작업 이름을 지정해야 합니다.


## 스트림 분석 입력 소스 테스트

**TestConnection** 메서드는 스트림 분석 작업이 입력 소스 및 입력 소스 유형에 특정한 다른 측면에도 연결할 수 있는지 여부를 테스트합니다. 예를 들어 이전 단계에서 만든 blob 입력 소스에서 메서드는 저장소 계정 이름 및 키 쌍을 사용하여 저장소 계정에 연결할 수 있는지를 확인하고 지정된 컨테이너가 존재하는지 점검합니다.

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse =
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## 스트림 분석 출력 대상 만들기

출력 대상 만들기는 스트림 분석 입력 소스 만들기와 매우 유사합니다. 입력 소스와 같이 출력 대상은 특정 작업에 연결됩니다. 다른 작업에 대해 동일한 출력 소스를 사용하려면, 메서드를 다시 호출하고 다른 작업 이름을 지정해야 합니다.

다음 코드는 출력 대상(Azure SQL 데이터베이스)를 만듭니다. 출력 대상의 데이터 형식 및/또는 serialization 형식을 사용자 지정할 수 있습니다.

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};

	OutputCreateOrUpdateResponse outputCreateResponse =
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## 스트림 분석 출력 대상 테스트

스트림 분석 출력 대상에는 연결 테스트를 위한 **TestConnection** 메서드가 있습니다.

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse =
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## 스트림 분석 변환 만들기

다음 코드는 쿼리 "입력에서 *선택"를 사용하여 스트림 분석 변환을 만들고 스트림 분석 작업에 대한 하나의 스트리밍 단위를 할당하도록 지정합니다. 스트리밍 단위 조정에 대한 자세한 내용은 [Azure 스트림 분석 작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};

	var transformationCreateResp =
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

입력 및 출력의 경우와 마찬가지로 변환에는 작성된 특정 스트림 분석 작업과 연결됩니다.

## 스트림 분석 작업 시작
스트림 분석 작업 및 해당 입력, 출력 및 변환을 만든 후, **Start** 메서드를 호출하여 시작할 수 있습니다.

다음 샘플 코드는 2012년 12월 12일, 12:12:12 UTC로 시작 시간이 설정된 사용자 지정 출력이 있는 스트림 분석 작업을 시작합니다.

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};

	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## 스트림 분석 작업 중지
**Stop** 메서드를 호출하여 실행 중인 스트림 분석 작업을 중지할 수 있습니다.

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## 스트림 분석 작업 삭제
**Delete** 메서드는 입력, 출력 및 변환 작업을 포함한 기본 하위 리소스 및 작업을 삭제합니다.

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## 지원 받기
추가 지원이 필요한 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.


## 다음 단계

.NET SDK를 사용하여 분석 작업을 만들고 실행하는 기본을 알아보았습니다. 자세한 알아보려면 다음을 참조하세요.

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 관리 .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0921_2016-->