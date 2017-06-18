<properties 
	pageTitle="데이터 팩터리 SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리 | Microsoft Azure" 
	description="데이터 팩터리 SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하고, 관리하는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016" 
	ms.author="spelluru"/>

# 데이터 팩터리 .NET SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리
## 개요
데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하며, 관리할 수 있습니다. 이 문서에는 데이터 팩터리를 만들고 모니터링하는 샘플 .NET 콘솔 응용 프로그램을 만들 수 있는 연습이 포함되어 있습니다. 데이터 팩터리 .NET SDK에 대한 자세한 내용은 [데이터 팩터리 클래스 라이브러리 참조](https://msdn.microsoft.com/library/mt415893.aspx)를 참조하세요.

## 필수 조건

- Visual Studio 2012, 2013 또는 2015
- [Azure .NET SDK](http://azure.microsoft.com/downloads/)를 다운로드하여 설치합니다.
- Azure Active Directory에 네이티브 클라이언트 응용 프로그램을 추가합니다. 응용 프로그램을 추가하는 단계는 [Azure Active Directory와 응용 프로그램 통합](../active-directory/active-directory-integrating-applications.md)을 참조하세요. **구성** 페이지에 **클라이언트 ID** 및 **URI 리디렉션**을 적어둡니다.
- Azure **구독 ID** 및 **테넌트 ID**.얻기. 자세한 내용은 [Azure 구독 및 테넌트 ID 얻기](#get-azure-subscription-and-tenant-ids)를 참조하세요.
- Azure Data Factory용 NuGet 패키지 다운로드 및 설치. 지침은 연습에 있습니다.

## 연습
1. Visual Studio 2012 또는 2013을 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.
	1. **Visual Studio 2012/2013/2015**을 실행합니다.
	2. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다.
	3. **템플릿**을 확장하고 **Visual C#**을 선택합니다. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.
	4. 오른쪽의 프로젝트 형식 목록에서 **콘솔 응용 프로그램**을 선택합니다.
	5. **DataFactoryAPITestApp**을 **이름**으로 입력합니다.
	6. **C:\\ADFGetStarted**를 **위치**로 선택합니다.
	7. **확인**을 클릭하여 프로젝트를 만듭니다.
2. **도구**를 클릭하고 **NuGet 패키지 관리자**를 가리킨 다음 **패키지 관리자 콘솔**을 클릭합니다.
3.	**패키지 관리자 콘솔**에서 다음 명령을 하나씩 실행합니다.

			Install-Package Microsoft.Azure.Management.DataFactories
			Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
4. 다음 **appSetttings** 섹션을 **App.config** 파일에 추가합니다. 이러한 구성 값은 **GetAuthorizationHeader** 메서드가 사용합니다.

	> [AZURE.IMPORTANT] **AdfClientId**, **RedirectUri**, **SubscriptionId** 및 **ActiveDirectoryTenantId**의 값을 고유한 값으로 대체합니다.
 
		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
		    <add key="AdfClientId" value="Your AAD application ID" />
		    <add key="RedirectUri" value="Your AAD application's redirect URI" />
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
5. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. **DataPipelineManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 **Main** 메서드에 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 입력 및 출력 데이터 집합과 파이프라인을 만듭니다. 또한 이 개체를 사용하여 런타임 시 데이터 집합의 조각을 모니터링합니다.

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE] **resourcegroupname**을 Azure 리소스 그룹의 이름으로 바꿉니다. [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다.

7. **데이터 팩터리**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );

8. **연결된 서비스**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

	> [AZURE.NOTE] **ConnectionString**에 Azure 저장소 계정의 **계정 이름** 및 **계정 키**를 사용합니다.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. **입력 및 출력 데이터 집합**을 만드는 다음 코드를 **Main** 메서드에 추가합니다.

	입력 Blob의 **FolderPath**는 **adftutorial/**로 설정됩니다. 여기서 **adftutorial**은 Blob 저장소에 있는 컨테이너의 이름입니다. Azure Blob 저장소에 이 컨테이너가 없는 경우 이름이 **adftutorial**인 컨테이너를 만들고 텍스트 파일을 컨테이너로 업로드합니다.
	
	출력 Blob의 FolderPath는 **adftutorial/apifactoryoutput/{Slice}**로 설정됩니다. 여기서 **Slice**는 **SliceStart**(각 조각의 시작 날짜-시간) 값을 기반으로 동적으로 계산됩니다.
 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

10. **파이프라인을 만들고 활성화**하는 다음 코드를 **Main** 메서드에 추가합니다. 이 파이프라인에는 **BlobSource**를 원본으로 사용하고 **BlobSink**를 싱크로 사용하는 **CopyActivity**가 포함되어 있습니다.

	복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

11. **Main** 메서드에 사용되는 다음 도우미 클래스를 **Program** 클래스에 추가합니다. 이 메서드는 Azure 포털에 로그인하는 데 사용하는 **사용자 이름** 및 **암호**를 입력할 수 있는 대화 상자를 표시합니다.
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
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
 
13. **Main** 메서드에 다음 코드를 추가하여 출력 데이터 집합의 데이터 조각 상태를 가져옵니다. 이 샘플에서는 조각만 필요합니다.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

13. **(선택 사항)** 데이터 조각의 실행 정보를 가져오는 다음 코드를 **Main** 메서드에 추가합니다.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();

14. 솔루션 탐색기에서 프로젝트(**DataFactoryAPITestApp**)를 확장하고 **참조**를 마우스 오른쪽 단추로 클릭한 다음 **참조 추가**를 클릭합니다. `System.Configuration` 어셈블리에 대한 확인란을 선택하고 **확인**을 클릭합니다.
15. 콘솔 응용 프로그램을 빌드합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
16. Azure Blob 저장소의 adftutorial 컨테이너에 하나 이상의 파일이 있는지 확인합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 Emp.txt 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

        John, Doe
		Jane, Doe 
17. 메뉴에서 **디버그** -> **디버깅 시작**을 클릭하여 샘플을 실행합니다. **데이터 조각의 실행 정보 가져오기**가 표시되면 몇 분 동안 기다린 다음 **ENTER** 키를 누릅니다.
18. Azure 포털을 사용하여 데이터 팩터리 **APITutorialFactory**가 다음 아티팩트로 생성되었는지 확인합니다.
	- 연결된 서비스: **LinkedService\_AzureStorage**
	- 데이터 집합: **DatasetBlobSource** 및 **DatasetBlobDestination**.
	- 파이프라인: **PipelineBlobSample**
18. 출력 파일이 **adftutorial** 컨테이너의 **apifactoryoutput** 폴더에 만들어졌는지 확인합니다.

## 팝업 대화 상자 없이 로그인 
연습에 나오는 샘플 코드는 대화 상자를 시작하여 Azure 자격 증명을 입력할 수 있게 합니다. 대화 상자를 사용하지 않고 프로그래밍 방식으로 로그인해야 하는 경우 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell)을 참조하세요.

> [AZURE.IMPORTANT] Azure Active Directory에 웹 응용 프로그램을 추가하고 클라이언트 ID 및 응용 프로그램의 클라이언트 암호를 적어둡니다.

### 예

GetAuthorizationHeaderNoPopup 메서드를 만듭니다.

    public static string GetAuthorizationHeaderNoPopup()
    {
        var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        var context = new AuthenticationContext(authority.AbsoluteUri);
        var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
        AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }

**Main** 함수에서 **GetAuthorizationHeader** 호출을 **GetAuthorizationHeaderNoPopup**에 대한 호출로 바꿉니다.

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeaderNoPopup());

서비스 주체인 Active Directory 응용 프로그램을 만든 다음 데이터 팩터리 참가자 역할에 할당할 수 있는 방법은 다음과 같습니다.

1. AD 응용 프로그램을 만듭니다.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"

2. AD 서비스 주체를 만듭니다.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. 데이터 팩터리 참가자 역할에 서비스 주체를 추가합니다.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

4. 응용 프로그램 ID를 가져옵니다.

		$azureAdApplication


응용 프로그램 ID 및 암호(클라이언트 암호)을 기록해 두고 연습에 사용합니다.

## Azure 구독 및 테넌트 ID 얻기
컴퓨터에 Azure PowerShell의 최신 버전이 설치되어 있지 않다면, 설치하기 위해 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md) 문서의 지침을 따릅니다.

1. Azure PowerShell을 시작하고 다음 명령을 실행합니다.
2. 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

		Login-AzureRmAccount

	이 계정에 연결된 Azure 구독이 하나 뿐인 경우 다음 두 단계를 수행할 필요가 없습니다.
3. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

		Get-AzureRmSubscription
4. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **NameOfAzureSubscription**을 Azure 구독의 이름으로 바꿉니다.

		Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext 

**SubscriptionId** 및 **TenantId** 값을 적어둡니다.

<!---HONumber=AcomDC_0914_2016-->