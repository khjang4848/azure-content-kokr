<properties 
	pageTitle="자습서: .NET API를 사용하여 복사 작업이 있는 파이프라인 만들기 | Microsoft Azure" 
	description="이 자습서에서는 .NET API를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다." 
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
	ms.topic="get-started-article" 
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# 자습서: .NET API를 사용하여 복사 작업이 있는 파이프라인 만들기
> [AZURE.SELECTOR]
- [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [복사 마법사](data-factory-copy-data-wizard-tutorial.md)

이 자습서에서는 .NET API를 사용하여 Azure Data Factory를 만들고 모니터링하는 방법을 보여 줍니다. 데이터 팩터리의 파이프라인은 복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사합니다.

복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.

> [AZURE.NOTE] 
이 문서는 모든 데이터 팩터리 .NET API를 다루지 않습니다. 데이터 팩터리 .NET SDK에 대한 자세한 내용은 [데이터 팩터리 .NET API 참조](https://msdn.microsoft.com/library/mt415893.aspx)를 참조하세요.

## 필수 조건
- [자습서 개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 살펴보고 자습서 개요를 가져와서 **필수 구성 요소**를 완료합니다.
- Visual Studio 2012, 2013 또는 2015
- [Azure .NET SDK](http://azure.microsoft.com/downloads/) 다운로드 및 설치
- Azure PowerShell. [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell을 사용하여 Azure Active Directory 응용 프로그램을 만듭니다.

### Azure Active Directory에서 응용 프로그램 만들기
Azure Active Directory 응용 프로그램을 만든 다음 응용 프로그램의 서비스 주체를 만들고 **데이터 팩터리 참가자** 역할에 할당합니다.

1. **PowerShell**을 시작합니다.
1. 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
	
		Login-AzureRmAccount   
2. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

		Get-AzureRmSubscription 
3. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;를 Azure 구독의 이름으로 바꿉니다.

		Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

	> [AZURE.IMPORTANT] 이 명령의 출력에서 **SubscriptionId** 및 **TenantId**를 적어둡니다.
4. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup**이라는 Azure 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	리소스 그룹이 이미 있다면 업데이트(Y)할지 또는 유지(N)할지를 지정합니다.

	다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.
5. Azure Active Directory 응용 프로그램을 만듭니다.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

	다음과 같은 오류가 발생하면 다른 URL을 지정하고 명령을 다시 실행합니다.

		Another object with the same value for property identifierUris already exists.

6. AD 서비스 주체를 만듭니다.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. **데이터 팩터리 참가자** 역할에 서비스 주체를 추가합니다.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. 응용 프로그램 ID를 가져옵니다.

		$azureAdApplication

	응용 프로그램 ID(출력에서 **applicationID**)를 적어둡니다.

이 단계에서 다음과 같은 4가지 값이 있어야 합니다.

- 테넌트 ID
- 구독 ID
- 응용 프로그램 UI
- (첫 번째 명령에 지정된)암호

## 연습
1. Visual Studio 2012/2013/2015를 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.
	1. **Visual Studio** 2012/2013/2015를 실행합니다.
	2. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다.
	3. **템플릿**을 확장하고 **Visual C#**을 선택합니다. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.
	4. 오른쪽의 프로젝트 형식 목록에서 **콘솔 응용 프로그램**을 선택합니다.
	5. 이름에 **DataFactoryAPITestApp**을 입력합니다.
	6. 위치에 **C:\\ADFGetStarted**를 선택합니다.
	7. **확인**을 클릭하여 프로젝트를 만듭니다.
2. **도구**를 클릭하고 **NuGet 패키지 관리자**를 가리킨 다음 **패키지 관리자 콘솔**을 클릭합니다.
3.	**패키지 관리자 콘솔**에서 다음 명령을 하나씩 실행합니다.

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
6. 다음 **appSetttings** 섹션을 **App.config** 파일에 추가합니다. 이 설정은 도우미 메서드 **GetAuthorizationHeader**에서 사용됩니다.

	**응용 프로그램 ID**, **암호**, **구독 ID** 및 **테넌트 ID** 값을 고유한 값으로 바꿉니다.

		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
    		<add key="ApplicationId" value="<Application ID>" />
    		<add key="Password" value="<Password>" />    
		    <add key="SubscriptionId" value= "Subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="tenant ID" />
		</appSettings>
6. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

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
        	string resourceGroupName = "ADFTutorialResourceGroup";
        	string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.IMPORTANT] 
	**resourceGroupName** 값을 Azure 리소스 그룹의 이름으로 바꿉니다.
	> 
	> 데이터 팩터리 이름(**dataFactoryName**)을 고유한 이름으로 업데이트합니다. 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.

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

8. **Azure Storage 연결된 서비스**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

	> [AZURE.IMPORTANT] **storageaccountname** 및 **accountkey**를 Azure Storage 계정 이름 및 키로 바꿉니다.

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. **Azure SQL 연결된 서비스**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.
 
	> [AZURE.IMPORTANT] **servername**, **databasename**, **username** 및 **password**를 Azure SQL Server의 이름, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.

			// create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. **입력 및 출력 데이터 집합**을 만드는 다음 코드를 **Main** 메서드에 추가합니다.

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
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


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. **파이프라인을 만들고 활성화**하는 다음 코드를 **Main** 메서드에 추가합니다. 이 파이프라인에는 **BlobSource**를 원본으로 사용하고 **BlobSink**를 싱크로 사용하는 **CopyActivity**가 포함되어 있습니다.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

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
                                    Name = "BlobToAzureSql",
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

12. **Main** 메서드에 다음 코드를 추가하여 출력 데이터 집합의 데이터 조각 상태를 가져옵니다. 이 샘플에서는 조각만 필요합니다.
 
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

14. 데이터 조각의 실행 정보를 가져오는 다음 코드를 **Main** 메서드에 추가합니다.

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

13. **Main** 메서드에 사용되는 다음 도우미 클래스를 **Program** 클래스에 추가합니다.
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. 솔루션 탐색기에서 프로젝트(**DataFactoryAPITestApp**)를 확장하고 **참조**를 마우스 오른쪽 단추로 클릭한 다음 **참조 추가**를 클릭합니다. "**System.Configuration**" 어셈블리에 대한 확인란을 선택하고 **확인**을 클릭합니다.
16. 콘솔 응용 프로그램을 빌드합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
16. Azure Blob 저장소의 **adftutorial** 컨테이너에 하나 이상의 파일이 있는지 확인합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 **Emp.txt** 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

        John, Doe
		Jane, Doe
	 
17. 메뉴에서 **디버그** -> **디버깅 시작**을 클릭하여 샘플을 실행합니다. **데이터 조각의 실행 정보 가져오기**가 표시되면 몇 분 동안 기다린 다음 **ENTER** 키를 누릅니다.
18. Azure 포털을 사용하여 데이터 팩터리 **APITutorialFactory**가 다음 아티팩트로 생성되었는지 확인합니다.
	- 연결된 서비스: **LinkedService\_AzureStorage**
	- 데이터 집합: **DatasetBlobSource** 및 **DatasetBlobDestination**.
	- 파이프라인: **PipelineBlobSample**
18. 출력 파일이 **adftutorial** 컨테이너의 "**apifactoryoutput**" 폴더에 만들어졌는지 확인합니다.

## 다음 단계

- [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참고하며 여기서 이 자습서에서 사용한 복사 작업에 대한 자세한 정보를 제공합니다.
- 데이터 팩터리 .NET SDK에 대한 자세한 내용은 [데이터 팩터리 .NET API 참조](https://msdn.microsoft.com/library/mt415893.aspx)를 참조하세요. 이 문서는 모든 데이터 팩터리 .NET API를 다루지 않습니다.

 

<!---HONumber=AcomDC_0921_2016-->