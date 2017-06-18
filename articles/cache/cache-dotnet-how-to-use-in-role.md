<properties 
	pageTitle="In-Role Cache 사용 방법(.NET) | Microsoft Azure" 
	description="Azure In-Role Cache를 사용하는 방법을 알아봅니다. 샘플은 C# 코드로 작성되었고 .NET API를 사용합니다." 
	services="cache" 
	documentationCenter=".net" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>






# Azure 캐시용 In-Role Cache 사용 방법

이 가이드에서는 **Azure 캐시용 In-Role Cache**를 사용하는 방법에 대해 설명합니다. 샘플은 C# 코드로 작성되었고 .NET API를 사용합니다. **캐시 클러스터 구성**, **캐시 클라이언트 구성**, **캐시에서 개체 추가 및 삭제, 캐시에 ASP.NET 세션 상태 저장**, **캐시를 사용하여 ASP.NET 페이지 출력 캐싱 활성화** 등의 시나리오를 다룹니다. In-Role Cache 사용에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

>[AZURE.IMPORTANT]작년 [알림](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)에 따라 Azure 관리된 캐시 서비스 및 Azure In-Role Cache 서비스가 2016년 11월 30일에 종료됩니다. [Azure Redis Cache](https://azure.microsoft.com/services/cache/)를 사용할 것을 권장합니다. 마이그레이션에 대한 자세한 내용은 [관리된 캐시 서비스에서 Azure Redis Cache로 마이그레이션](../redis-cache/cache-migrate-to-redis.md)을 참조하세요.

<a name="what-is"></a>
## In-Role Cache란?

In-Role Cache는 Azure 응용 프로그램에 캐싱 계층을 제공합니다. 캐싱은 다른 백엔드 소스의 정보를 임시로 메모리에 저장하여 성능을 높이며, 클라우드에서 데이터베이스 트랜잭션과 관련된 비용을 줄입니다. In-Role Cache의 특성은 다음과 같습니다.

-   ASP.NET이 미리 내장되어 세션 상태 및 페이지 출력 캐싱을 제공하므로 응용 프로그램 코드를 수정하지 않고도 웹 응용 프로그램을 가속화할 수 있습니다.
-   직렬화 가능한 모든 관리 개체(예: CLR 개체, 행, XML, 이진 데이터)를 캐시합니다.
-   Azure와 Windows Server AppFabric에서 모두 일관된 개발 모델을 제공합니다.

In-Role Cache는 Azure 클라우드 서비스(호스티드 서비스라고도 함)에 역할 인스턴스를 호스트하는 가상 컴퓨터의 메모리 일부를 사용하여 캐싱을 수행하는 방법을 제공합니다. 배포 옵션에서 유연성이 훨씬 뛰어나고, 매우 큰 크기의 캐시도 사용할 수 있으며, 캐시와 관련된 할당량 제한이 없습니다.

>[AZURE.IMPORTANT] Azure SDK 2.6부터 In-Role Cache에서는 Microsoft Azure 저장소 SDK 버전 4.3을 사용합니다. 이전 버전의 Azure SDK에서는 In-Role Cache에서 Azure Storage SDK 1.7을 사용했습니다. Azure SDK가 2.6 이전 버전인 In-Role Cache를 사용하는 응용 프로그램은 Azure 저장소 버전 2011-08-18이 사용 중단되는 2016년 8월 1일 전까지 Azure SDK 2.6으로 마이그레이션해야 합니다. 자세한 내용은 [Azure SDK 2.6 릴리스 정보 - In-Role Cache](../azure-sdk-dotnet-release-notes-2-6.md#in-role-cache-updates) 및 [Microsoft Azure 저장소 서비스 버전 제거 업데이트(2016에 확장)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)를 참조하세요.

역할 인스턴스에 대한 캐싱에는 다음과 같은 이점이 있습니다.

-	캐싱에 비용을 지불하지 않습니다. 캐시를 호스트하는 계산 리소스에 대해서만 지불합니다.
-	캐시 할당량과 제한이 없습니다.
-	제어 및 격리 기능이 더 뛰어납니다.
-	성능이 향상됩니다.
-	역할이 확장되거나 축소될 때 캐시 크기가 자동으로 조정됩니다. 역할 인스턴스가 추가 또는 제거될 때 캐시의 확장이나 축소에 사용할 수 있는 메모리 크기가 효율적으로 조정됩니다.
-	완전히 신뢰할 수 있는 개발 시간 디버깅 기능을 제공합니다.
-	memcache 프로토콜을 지원합니다.

또한 역할 인스턴스에 대한 캐싱은 다음과 같은 구성 가능한 옵션을 제공합니다.

-	캐싱을 위한 전용 역할을 구성하거나 기존 역할에 캐싱을 공동 배치할 수 있습니다.
-	같은 클라우드 서비스 배포 내 여러 클라이언트에서 캐시를 이용할 수 있습니다.
-	다른 속성을 포함하는 명명된 캐시를 여러 개 만들 수 있습니다.
-	개별 캐시에서 고가용성을 선택적으로 구성할 수 있습니다.
-	지역, 태그, 알림 등 확장된 캐싱 기능을 사용할 수 있습니다.

이 가이드에서는 In-Role Cache를 시작하기 위한 개요를 제공합니다. 이 시작하기 가이드의 범위를 벗어나는 기능에 대해 자세히 알아보려면 [In-Role Cache 개요][]를 참조하세요.

<a name="getting-started-cache-role-instance"></a>
## In-Role Cache 시작

In-Role Cache는 역할 인스턴스를 호스트하는 가상 컴퓨터의 메모리를 사용하여 캐시를 활성화하는 방법을 제공합니다. 캐시를 호스트하는 역할 인스턴스를 **캐시 클러스터**라고도 합니다. 역할 인스턴스의 캐싱에 대한 두 가지 배포 토폴로지가 있습니다.

-	**전용 역할** 캐싱 - 역할 인스턴스만 캐싱에 사용됩니다.
-	**공동 배치된 역할** 캐싱 - 캐시가 응용 프로그램과 VM 리소스(대역폭, CPU 및 메모리)를 공유합니다.

역할 인스턴스에 캐싱을 사용하려면 캐시 클러스터를 구성한 다음 캐시 클라이언트를 구성해야 합니다. 그래야 캐시 클라이언트가 캐시 클러스터에 액세스할 수 있습니다.

-	[캐시 클러스터 구성][]
-	[캐시 클라이언트 구성][]

<a name="enable-caching"></a>
## 캐시 클러스터 구성

**공동 배치된 역할** 캐시 클러스터를 구성하려면 캐시 클러스터를 호스트할 역할을 선택합니다. **솔루션 탐색기**에서 역할 속성을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

![RoleCache1][RoleCache1]

**캐싱** 탭으로 전환하고, **캐싱 사용** 확인란을 선택하고, 원하는 캐싱 옵션을 지정합니다. **작업자 역할** 또는 **ASP.NET 웹 역할**에서 캐싱을 사용하도록 설정하면 기본 구성은 **공동 배치된 역할** 캐싱이 되고, 역할 인스턴스 메모리의 30%가 캐싱에 할당됩니다. 기본 캐시는 자동으로 구성되며, 원하는 경우 명명된 캐시를 추가로 만들 수 있습니다. 이러한 캐시는 할당된 메모리를 공유하게 됩니다.

![RoleCache2][RoleCache2]

**Dedicated Role** 캐시 클러스터를 구성하려면 프로젝트에 **캐시 작업자 역할**을 추가합니다.

![RoleCache7][RoleCache7]

프로젝트에 **캐시 작업자 역할**을 추가하면 기본 구성은 **전용 역할** 캐싱이 됩니다.

![RoleCache8][RoleCache8]

캐싱이 활성화되었으면 캐시 클러스터 저장소 계정을 구성할 수 있습니다. In-Role Cache를 사용하려면 Azure 저장소 계정이 필요합니다. 이 저장소 계정은 캐시 클러스터를 구성하는 모든 가상 컴퓨터에서 액세스하는 캐시 클러스터에 대한 구성 데이터를 유지하는 데 사용됩니다. 캐시 클러스터 역할 속성 페이지의 **캐싱** 탭(**명명된 캐시 설정** 위에 있음)에서 이 저장소 계정을 지정할 수 있습니다.

![RoleCache10][RoleCache10]

>이 저장소 계정을 구성하지 않으면 역할을 시작할 수 없습니다.

캐시의 크기를 결정하는 구성 요소는 역할의 VM 크기, 역할의 인스턴스 개수, 캐시 클러스터가 전용 역할과 공동 배치된 역할 중 무엇으로 구성되어 있는가 등입니다.

>이 섹션에서는 캐시 크기 구성에 대한 간단한 개요를 제공합니다. 캐시 크기 및 기타 용량 계획 고려 사항에 대한 자세한 내용은 [In-Role Cache 캐시 용량 고려 사항][]을 참조하십시오.

가상 컴퓨터 크기 및 역할 인스턴스의 수를 구성하려면 **솔루션 탐색기**에서 역할 속성을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

![RoleCache1][RoleCache1]

**구성** 탭으로 전환합니다. 기본 **인스턴스 개수**는 1이고, 기본 **VM 크기**는 **작음**입니다.

![RoleCache3][RoleCache3]

VM 크기별 총 메모리는 다음과 같습니다.

-	**작음**: 1.75GB
-	**중간**: 3.5GB
-	**큼**: 7GB
-	**매우 큼**: 14GB


> 이러한 메모리 크기는 OS, 캐시 프로세스, 캐시 데이터 및 응용 프로그램에서 공유하는 VM에서 사용할 수 있는 총 메모리의 양을 나타냅니다. 가상 컴퓨터 크기 구성에 대한 자세한 내용은 [가상 컴퓨터 크기 구성 방법][]을 참조하세요. **매우 큼** VM 크기에서는 캐시가 지원되지 않습니다.

**공동 배치된 역할** 캐싱을 지정하면 가상 컴퓨터 메모리의 지정된 백분율에 따라 캐시 크기가 결정됩니다. **전용 역할** 캐싱을 지정하면 가상 컴퓨터에서 사용할 수 있는 모든 메모리가 캐싱에 사용됩니다. 역할 인스턴스 두 개를 구성하면 가상 컴퓨터의 결합된 메모리가 사용됩니다. 이렇게 하여 캐시 클러스터가 형성됩니다. 사용 가능한 캐싱 메모리는 여러 역할 인스턴스 간에 분산되지만 단일 리소스로서 캐시의 클라이언트에 표시됩니다. 추가 역할 인스턴스를 구성하면 마찬가지로 캐시 크기가 커집니다. 원하는 크기의 캐시를 프로비전하는 데 필요한 설정을 지정하려면 용량 계획 스프레드시트를 사용할 수 있습니다. 자세한 내용은 [In-Role Cache 용량 계획 고려 사항][]을 참조하십시오.

캐시 클러스터 구성을 마쳤으면 캐시에 액세스하도록 캐시 클라이언트를 구성할 수 있습니다.

<a name="NuGet"></a>
## 캐시 클라이언트 구성

In-Role Cache 캐시에 액세스하려면 클라이언트가 동일한 배포 내에 있어야 합니다. 캐시 클러스터가 전용 역할 캐시 클러스터인 경우 클라이언트는 해당 배포의 다른 역할입니다. 캐시 클러스터가 공동 배치된 역할 캐시 클러스터인 경우 클라이언트는 해당 배포의 다른 역할일 수도 있고, 캐시 클러스터를 호스트하는 역할 자체일 수도 있습니다. 캐시에 액세스하는 각 클라이언트 역할을 구성하는 데 사용할 수 있는 NuGet 패키지가 제공됩니다. Caching NuGet 패키지를 사용하여 캐시 클러스터에 액세스할 역할을 구성하려면 **솔루션 탐색기**에서 역할 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![RoleCache4][RoleCache4]

**In-Role Cache**를 선택하고 **설치**와 **동의함**을 차례로 클릭합니다.

>**In-Role Cache**가 목록에 나타나지 않으면 **온라인 검색** 텍스트 상자에 **WindowsAzure.Caching**을 입력한 후 결과에서 선택합니다.

![RoleCache5][RoleCache5]

NuGet 패키지는 역할의 구성 파일에 필요한 구성을 추가하고, Azure 응용 프로그램의 ServiceConfiguration.cscfg 파일에 캐시 클라이언트 진단 수준 설정을 추가하고, 필요한 어셈블리 참조를 추가하는 여러 가지 작업을 수행합니다.

>ASP.NET 웹 역할의 경우 Caching NuGet 패키지는 web.config에 주석으로 처리된 두 개의 섹션도 추가합니다. 첫 번째 섹션은 세션 상태가 캐시에 저장되도록 하고, 두 번째 섹션은 ASP.NE페이지 출력 캐싱을 활성화합니다. 자세한 내용은 [방법: 캐시에 ASP.NET 세션 상태 저장] 및 [방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장][]을 참조하세요.

NuGet 패키지는 역할의 web.config 또는 app.config에 다음과 같은 구성 요소를 추가합니다. **dataCacheClients** 섹션 및 **cacheDiagnostics** 섹션이 **configSections** 요소 아래에 추가됩니다. **configSections** 요소가 없는 경우 **configuration** 요소의 하위 요소로서 하나가 생성됩니다.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

이 두 섹션에는 **dataCacheClients** 요소와 **cacheDiagnostics** 요소에 대한 참조가 포함됩니다. 이러한 요소는 **configuration** 요소에도 추가됩니다.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

구성이 추가되었으면 **[cache cluster role name]**을 캐시 클러스터를 호스트하는 역할의 이름으로 교체합니다.

>**[cache cluster role name]**을 캐시 클러스터를 호스트하는 역할의 이름으로 교체하지 않으면, 캐시에 액세스할 때 내부 **DatacacheException** 및 "No such role exists"라는 메시지와 함께 **TargetInvocationException**이 발생합니다.

또한 NuGet 패키지는 ServiceConfiguration.cscfg의 캐시 클라이언트 역할의 **ConfigurationSettings**에 **ClientDiagnosticLevel** 설정을 추가합니다. 다음 예제는 ServiceConfiguration.cscfg 파일의 **WebRole1** 섹션이며, **ClientDiagnosticLevel**은 1입니다. 이는 기본 **ClientDiagnosticLevel**입니다.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>In-Role Cache는 캐시 서버 및 캐시 클라이언트 진단 수준을 모두 제공합니다. 진단 수준은 캐싱을 위해 수집되는 진단 정보의 수준을 구성하는 단일 설정입니다. 자세한 내용은 [In-Role Cache 문제 해결 및 진단][]을 참조하십시오.

NuGet 패키지는 또한 다음 어셈블리에 참조를 추가합니다.

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

ASP.NET 웹 역할인 경우에는 다음 어셈블리 참조도 추가됩니다.

-	Microsoft.Web.DistributedCache.dll.

클라이언트 프로젝트의 캐싱을 구성했으면 캐시 작업에 대해 다음 섹션에서 설명하는 기술을 사용할 수 있습니다.

<a name="working-with-caches"></a>
## 캐시 작업

이 섹션의 각 단계에서는 일반적인 캐싱 작업 수행 방법에 대해 설명합니다.

-	[방법: DataCache 개체 만들기][]
-   [방법: 캐시에서 개체 추가 및 검색][]
-   [방법: 캐시에서 개체의 만료 지정][]
-   [방법: 캐시에 ASP.NET 세션 상태 저장][]
-   [방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장][]

<a name="create-cache-object"></a>
## 방법: DataCache 개체 만들기

프로그래밍 방식으로 캐시 작업을 하려면 캐시에 대한 참조가 필요합니다. In-Role Cache를 사용할 파일의 상단에 다음을 추가합니다.

    using Microsoft.ApplicationServer.Caching;

>필요한 참조를 추가하는 Caching NuGet 패키지를 설치한 후에도 Visual Studio에서 using 문의 유형을 인식하지 못하면 프로젝트의 대상 프로필이 .NET Framework 4.0 이상인지, **Client Profile**을 지정하지 않는 프로필 중 하나를 선택했는지를 확인하세요. 캐시 클라이언트 구성에 대한 자세한 내용은 [캐시 클라이언트 구성][]을 참조하십시오.

**DataCache** 개체를 만드는 두 가지 방법이 있습니다. 첫 번째 방법은 단순히 **DataCache**를 만들고 원하는 캐시 이름으로 전달하는 것입니다.

    DataCache cache = new DataCache("default");

**DataCache**가 인스턴스화되면, 다음 섹션에서 설명한 것처럼 이를 사용해 캐시를 조작할 수 있습니다.

두 번째 방법은 기본 생성자를 사용하여 응용 프로그램에 새로운 **DataCacheFactory** 개체를 만드는 것입니다. 이렇게 하면 캐시 클라이언트가 구성 파일의 설정을 사용합니다. **DataCache** 개체를 반환하는 새로운 **DataCacheFactory** 인스턴스의 **GetDefaultCache** 메서드를 호출하거나, **GetCache** 메서드를 호출하고 캐시의 이름으로 전달합니다. 두 가지 메서드 모두 프로그래밍 방식으로 캐시에 액세스하기 위해 사용할 수 있는 **DataCache** 개체를 반환합니다.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## 방법: 캐시에서 개체 추가 및 검색

캐시에 항목을 추가하려면 **Add** 또는 **Put** 메서드를 사용할 수 있습니다. **Add** 메서드는 키 매개 변수 값의 키와 함께 지정한 개체를 캐시에 추가합니다.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

동일한 키의 개체가 이미 캐시에 있으면 다음 메시지와 함께 **DataCacheException**이 발생합니다.

> ErrorCode:SubStatus: 캐시에 이미 있는 키를 사용하여 개체를 만들려고 하고 있습니다. 캐시는 개체에 대해 고유한 키 값만 허용합니다.

특정 키로 개체를 검색하려면 **Get** 메서드를 사용할 수 있습니다. 개체가 있으면 반환되고 없으면 Null이 반환됩니다.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

**Put** 메서드는 지정한 키의 개체를 캐시에 추가하거나(없는 경우) 개체를 교체합니다(있는 경우).

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## 방법: 캐시에서 개체의 만료 지정

기본적으로 캐시에 배치된 항목은 10분 후 만료됩니다. 이 값은 캐시 클러스터를 호스트하는 역할의 역할 속성에 있는 **TTL(Time to Live)(분)** 설정에서 구성할 수 있습니다.

![RoleCache6][RoleCache6]

**만료 유형**에는 **없음**, **절대**, **슬라이딩 윈도우**의 세 가지가 있습니다. 만료 유형은 **TTL(Time to Live)(분)**을 사용하여 만료를 결정하는 방법을 구성합니다. 기본 **만료 유형**은 **절대**입니다. 이는 항목이 캐시에 배치되면 항목의 만료에 대한 카운트다운 타이머가 시작된다는 뜻입니다. 항목에 대해 지정한 시간이 경과하면 항목이 만료됩니다. **슬라이딩 윈도우**를 지정하면, 캐시에서 항목이 액세스될 때마다 만료 카운트다운이 다시 설정되며 마지막 액세스 이후 지정된 시간이 경과하기 전에는 항목이 만료되지 않습니다. **없음**을 지정하면 **TTL(Time to Live)(분)**을 **0**으로 설정해야 합니다. 이 경우 항목은 만료되지 않으며 캐시에 있는 한 유효한 상태가 유지됩니다.

역할 속성에서 구성한 것보다 더 길거나 짧은 시간 제한 간격이 필요한 경우, 캐시에서 항목이 추가 또는 업데이트될 때 **TimeSpan** 매개 변수가 있는 **Add** 및 **Put** 오버로드를 사용하여 특정 기간을 지정할 수 있습니다. 다음 예제에서는 **item** 키와 함께 문자열 **value**가 캐시에 추가되었고 시간 제한은 30분입니다.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

캐시에 있는 항목의 남은 시간 제한 간격을 보려면 **GetCacheItem** 메서드를 사용하여, 남은 시간 제한 간격을 비롯하여 캐시의 항목에 대한 정보를 포함하는 **DataCacheItem** 개체를 검색할 수 있습니다.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## 방법: 캐시에 ASP.NET 세션 상태 저장

In-Role Cache의 세션 상태 공급자는 ASP.NET 응용 프로그램의 Out of Process 저장소 메커니즘입니다. 이 공급자를 사용하면 메모리 내 또는 SQL Server 데이터베이스가 아니라 Azure 캐시에 세션 상태를 저장할 수 있습니다. 캐싱 세션 상태 공급자를 사용하려면 먼저 캐시 클러스터를 구성하고, [In-Role Cache 시작하기][]에서 설명한 대로 Caching NuGet 패키지를 사용하여 캐싱용 ASP.NET 응용 프로그램을 구성하십시오. Caching NuGet 패키지를 설치하면 주석으로 처리된 섹션이 web.config에 추가됩니다. 이 섹션에는 ASP.NET 응용 프로그램에서 In-Role Cache에 세션 상태 제공자를 사용하는 데 필요한 구성이 포함되어 있습니다.

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>Caching NuGet 패키지를 설치한 후에도 주석으로 처리된 이 섹션이 web.config에 포함되지 않으면 [NuGet 패키지 관리자 설치][]에서 최신 NuGet 패키지 관리자를 설치했는지 확인하고, 패키지를 제거한 후 다시 설치합니다.

In-Role Cache에 세션 상태 공급자를 사용하려면 지정된 섹션의 주석 처리를 제거합니다. 제공된 코드 조각에 기본 캐시가 지정되어 있습니다. 다른 캐시를 사용하려면 **cacheName** 특성에서 원하는 캐시를 지정하십시오.

캐싱 서비스 세션 상태 공급자에 대해 자세히 알아보려면 [In-Role Cache용 세션 상태 공급자][]를 참조하세요.

<a name="store-page"></a>
## 방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장

In-Role Cache의 출력 캐시 공급자는 출력 캐시 데이터의 Out of Process 저장소 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다. 출력 캐시 공급자를 사용하려면 먼저 캐시 클러스터를 구성하고, [In-Role Cache 시작][]에서 설명한 대로 Caching NuGet 패키지를 사용하여 캐싱용 ASP.NET 응용 프로그램을 구성하세요. Caching NuGet 패키지를 설치하면 주석으로 처리된 다음 섹션이 web.config에 추가됩니다. 이 섹션에는 ASP.NET 응용 프로그램에서 In-Role Cache에 출력 캐시 공급자를 사용하는 데 필요한 구성이 포함되어 있습니다.

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>Caching NuGet 패키지를 설치한 후에도 주석으로 처리된 이 섹션이 web.config에 포함되지 않으면 [NuGet 패키지 관리자 설치][]에서 최신 NuGet 패키지 관리자를 설치했는지 확인하고, 패키지를 제거한 후 다시 설치합니다.

In-Role Cache에 출력 캐시 공급자를 사용하려면 지정된 섹션의 주석 처리를 제거합니다. 제공된 코드 조각에 기본 캐시가 지정되어 있습니다. 다른 캐시를 사용하려면 **cacheName** 특성에서 원하는 캐시를 지정하십시오.

출력을 캐시하고자 하는 각 페이지에 **OutputCache** 지시문을 추가합니다.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

이 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 사용 가능한 옵션에 대한 자세한 내용은 [OutputCache 지시문][]을 참조하십시오.

In-Role Cache에 출력 캐시 공급자를 사용하는 방법에 대한 자세한 내용은 [In-Role Cache용 출력 캐시 공급자][]를 참조하세요.

<a name="next-steps"></a>
## 다음 단계

이제 In-Role Cache의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 캐싱 작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [In-Role Cache][]
-   In-Role Cache로 마이그레이션하는 방법: [In-Role Cache로 마이그레이션][]
-   샘플 확인: [In-Role Cache 샘플][]
-	In-Role Cache에 대한 TechEd 2013에서 [최대 성능: Azure 캐싱으로 클라우드 서비스 응용 프로그램 가속화][] 세션을 참조하세요.

<!-- INTRA-TOPIC LINKS -->
[다음 단계]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[In-Role Cache 시작]: #getting-started-cache-role-instance
[In-Role Cache 시작하기]: #getting-started-cache-role-instance
[캐시 클러스터 구성]: #enable-caching
[Configure the desired cache size]: #cache-size
[캐시 클라이언트 구성]: #NuGet
[Working with Caches]: #working-with-caches
[방법: DataCache 개체 만들기]: #create-cache-object
[방법: 캐시에서 개체 추가 및 검색]: #add-object
[방법: 캐시에서 개체의 만료 지정]: #specify-expiration
[방법: 캐시에 ASP.NET 세션 상태 저장]: #store-session
[방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[가상 컴퓨터 크기 구성 방법]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[In-Role Cache 용량 계획 고려 사항]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache 캐시 용량 고려 사항]: http://go.microsoft.com/fwlink/?LinkId=252651
[In-Role Cache 샘플]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[In-Role Cache]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[최대 성능: Azure 캐싱으로 클라우드 서비스 응용 프로그램 가속화]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[In-Role Cache로 마이그레이션]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet 패키지 관리자 설치]: http://go.microsoft.com/fwlink/?LinkId=240311
[In-Role Cache용 출력 캐시 공급자]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[OutputCache 지시문]: http://go.microsoft.com/fwlink/?LinkId=251979
[In-Role Cache 개요]: http://go.microsoft.com/fwlink/?LinkId=254172
[In-Role Cache용 세션 상태 공급자]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[In-Role Cache 문제 해결 및 진단]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 

<!---HONumber=AcomDC_0921_2016-->