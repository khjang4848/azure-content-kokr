<properties 
	pageTitle="프로그래밍 방식으로 스트림 분석에서 작업 모니터링| Microsoft Azure" 
	description="REST API, Azure SDK 또는 PowerShell을 통해 생성된 스트림 분석 작업을 프로그래밍 방식으로 모니터링하는 방법에 대해 알아봅니다."
	keywords=".net 모니터, 작업 모니터, 응용 프로그램 모니터링"
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


# 프로그래밍 방식으로 스트림 분석 작업 모니터 만들기
 이 문서에서는 스트림 분석 작업에 대한 모니터링을 사용하는 방법을 보여줍니다. REST API, Azure SDK 또는 PowerShell을 통해 생성된 스트림 분석 작업은 기본적으로 모니터링이 설정되어 있지 않습니다. 작업의 모니터 페이지로 이동하고 사용 버튼을 클릭하여 Azure 포털에서 수동으로 설정하거나 이 문서의 단계를 수행하여 이 프로세스를 자동화할 수 있습니다. 모니터링 데이터는 스트림 분석 작업에 대해 Azure 포털의 “모니터” 탭에서 볼 수 있습니다.

![작업 모니터 작업 탭](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## 필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

- Visual Studio 2012 또는 2013.
- [Azure .NET SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 설치합니다.
- 모니터링 설정이 필요한 기존 스트림 분석 작업.

## 프로젝트 설정

1.	Visual Studio C# .NET 콘솔 응용 프로그램을 만듭니다.
2.	패키지 관리자 콘솔에서 NuGet 패키지를 설치하려면 다음 명령을 실행합니다. 첫 번째는 Azure 스트림 분석 관리.NET SDK입니다. 두 번째는 모니터링을 사용하도록 설정하는 데 사용되는 Azure Insights SDK입니다. 마지막은 인증에 사용되는 Azure Active Directory 클라이언트입니다.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	다음 appSettings 섹션을 App.config 파일에 추가합니다.

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
    	<add key="JobName" value="YOUR JOB NAME" />
    	<add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
    	<add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
*SubscriptionId*와 *ActiveDirectoryTenantId*의 값을 Azure 구독 및 테넌트 ID로 바꿉니다. 다음 PowerShell cmdlet을 실행하여 이러한 값을 얻을 수 있습니다.

    ```
    Get-AzureAccount
    ```
4.	프로젝트의 원본 파일(Program.cs)에 다음 using 문을 추가합니다.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.	인증 도우미 메서드를 추가합니다.

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

## 관리 클라이언트 만들기
다음 코드는 필수 변수 및 관리 클라이언트를 설정합니다.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
    	new TokenCloudCredentials(
    		ConfigurationManager.AppSettings["SubscriptionId"],
    		GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## 기존 스트림 분석 작업에 모니터링 사용

다음 코드는 **기존** 스트림 분석 ㅈ가업에 모니터링을 사용합니다. 코드의 첫 번째 부분은 스트림 분석 서비스에 대해 GET 요청을 수행하여 특정 스트림 분석 작업에 대한 정보를 검색합니다. 스트림 분석 작업에 모니터링을 사용하기 위해 Insights 서비스에 PUT 요청을 보내는 코드의 나머지 부분에서 Put 메서드에 대한 매개 변수로 “Id” 속성(GET 요청에서 검색됨)을 사용합니다.

> [AZURE.WARNING]
Azure 포털을 통해 또는 아래 코드를 통해 프로그래밍 방식으로 서로 다른 스트림 분석 작업에 대한 모니터링을 이전에 설정한 경우, **이전에 모니터링을 활성화했을 때 했던 동일한 저장소 계정 이름을 제공하는 것이 좋습니다.**
> 
> 저장소 계정은 작업 자체에 특정되지 않고 스트림 분석 작업에서 만든 지역에 연결됩니다.
> 
> 동일 지역의 모든 스트림 분석 작업(및 다른 모든 Azure 리소스)은 이 저장소 계정을 공유하여 모니터링 데이터를 저장합니다. 사용자가 다른 저장소 계정을 제공하면, 다른 스트림 분석 작업 및/또는 다른 Azure 리소스에 대한 모니터링에 의도하지 않은 부작용이 발생할 수 있습니다.
> 
> 아래 ```“<YOUR STORAGE ACCOUNT NAME>”```를 바꾸는데 사용된 저장소 계정 이름은 모니터링을 사용할 스트림 분석 작업과 동일한 구독에 있는 저장소 계정이어야 합니다.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
    	PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
    		Properties = new ServiceDiagnosticSettings()
    		{
        		StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
    		}
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## 지원 받기
추가 지원이 필요한 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.


## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0921_2016-->