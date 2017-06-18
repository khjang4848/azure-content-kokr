<properties
   pageTitle="ASP.NET Web API와의 서비스 통신 | Microsoft Azure"
   description="Reliable Services API를 자체 호스팅하는 OWIN으로 ASP.NET Web API를 사용하여 서비스 통신을 구현하는 방법을 단계별로 알아봅니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/29/2016"
   ms.author="vturecek"/>

# 시작: OWIN 자체 호스팅을 사용하는 서비스 패브릭 Web API 서비스

Azure 서비스 패브릭은 서비스를 사용자, 그리고 다른 서비스와 통신하는 방법을 결정할 때 도움이 됩니다. 이 자습서에서는 서비스 패브릭의 Reliable Services API에서 자체 호스팅되는 OWIN(Open Web Interface for .NET)과 ASP.NET Web API를 사용하여 서비스 통신을 구현하는 방법을 중점적으로 살펴봅니다. Reliable Services 플러그형 통신 API를 자세히 알아봅니다. 또한 단계별 예제에서 Web API 사용하여 사용자 지정 통신 수신기를 설정하는 방법을 보여 줍니다.


## 서비스 패브릭에서 Web API 소개

ASP.NET Web API는 .NET Framework 위에 HTTP API를 구축할 때 널리 사용되는, 강력한 프레임워크입니다. 프레임워크에 익숙하지 않은 경우 자세히 알려면 [ASP.NET Web API 2 시작](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)을 참조하세요.

서비스 패브릭의 Web API는 여러분이 알고 있고 즐겨 사용하는 것과 동일한 ASP.NET Web API입니다. Web API 응용 프로그램을 *호스트*하는 방법에 차이가 있습니다. Microsoft IIS(인터넷 정보 서비스)를 사용하지 않습니다. 차이를 좀 더 잘 이해할 수 있도록 두 부분으로 나누어 보겠습니다.

 1. Web API 응용 프로그램(컨트롤러 및 모델 포함)
 2. 호스트(웹 서버, 일반적으로 IIS)

Web API 응용 프로그램 자체는 변경되지 않습니다. 이전에 작성한 Web API 응용 프로그램과 다르지 않으며 대부분의 응용 프로그램 코드를 이동할 수 있어야 합니다. 하지만 IIS에서 호스팅하는 경우 응용 프로그램을 호스팅하는 위치가 기존과 약간 다를 수 있습니다. 호스팅 부분으로 들어가기 전에 더 친숙한 부분인 Web API 응용 프로그램부터 시작하겠습니다.


## 응용 프로그램 만들기

Visual Studio 2015에서 단일 상태 비저장 서비스로 새 서비스 패브릭 응용 프로그램을 만들기 시작합니다.

![새 서비스 패브릭 응용 프로그램 만들기](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Web API를 사용하는 상태 비저장 서비스용 Visual Studio 템플릿이 제공됩니다. 이 자습서에서는 이 템플릿을 선택한 경우에 제공되는 Web API 프로젝트를 처음부터 빌드합니다.

Web API 프로젝트를 처음부터 빌드하는 방법을 알아보려면 빈 상태 비저장 서비스 프로젝트를 선택하거나 상태 비저장 서비스 Web API 템플릿으로 시작하고 단순히 따를 수 있습니다.

![단일 상태 비저장 서비스 만들기](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

첫 번째 단계는 Web API에 대한 일부 NuGet 패키지를 가져오는 것입니다. 우리가 사용할 패키지는 Microsoft.AspNet.WebApi.OwinSelfHost입니다. 이 패키지에는 필요한 모든 Web API 패키지 및 *호스트* 패키지가 포함되어 있습니다. 호스트 패키지는 나중에 중요하게 사용됩니다.

![NuGet 패키지 관리자를 사용하여 Web API 만들기](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

패키지가 설치되면 기본 Web API 프로젝트 구조를 구축하기 시작할 수 있습니다. Web API를 사용한 적이 있다면 프로젝트 구조가 매우 익숙할 것입니다. 먼저 `Controllers` 디렉터리 및 간단한 값 컨트롤러를 추가합니다.

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

그런 다음, 프로젝트 루트에 Startup 클래스를 추가하여 라우팅, 포맷터 및 기타 구성 설치 프로그램을 등록합니다. 이것은 Web API가 *host*에 플러그인하는 위치이기도 합니다. 이 부분은 나중에 다시 살펴보겠습니다.

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

응용 프로그램 부분은 이제 끝났습니다. 이로써 기본 Web API 프로젝트 레이아웃을 설정했습니다. 지금까지 이전에 작성한 Web API 프로젝트나 기본 Web API 템플릿과 비교하면 많이 다르지 않을 것입니다. 예전과 마찬가지로 컨트롤러와 모델에는 비즈니스 논리가 들어갑니다.

이제 호스팅을 실제로 실행하려면 어떤 작업을 해야 할까요?

## 서비스 호스팅

서비스 패브릭에서 서비스는 서비스 코드를 실행하는 실행 파일인 *서비스 호스트 프로세스*에서 실행됩니다. Reliable Services API를 사용하여 서비스를 작성하는 경우 서비스 프로젝트는 방금 서비스 형식을 등록하고 코드를 실행하는 실행 파일을 컴파일합니다. .NET의 서비스 패브릭에 서비스를 작성하는 대부분의 경우에도 마찬가지입니다. 상태 비저장 서비스 프로젝트에서 Program.cs를 열면 다음이 표시되어야 합니다.

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

콘솔 응용 프로그램에 대한 진입점과 아주 비슷하게 보이는 경우 다음과 같은 이유 때문입니다.

서비스 호스트 프로세스 및 서비스 등록에 대한 더 자세한 내용은 이 문서의 범위를 벗어납니다. *서비스 코드가 자체 프로세스에서 실행 중*이라는 점은 이제 알고 있어야 합니다.

## OWIN 호스트로 자체 호스팅되는 Web API

Web API 응용 프로그램 코드가 자체 프로세스에서 호스팅되는 경우 웹 서버에 연결하는 방법은 무엇일까요? [OWIN](http://owin.org/)을 입력합니다. OWIN은 간단히 말해 .NET 웹 응용 프로그램 및 웹 서버 간의 계약입니다. 일반적으로 ASP.NET(MVC 5까지)이 사용될 때 웹 응용 프로그램은 System.Web을 통해 IIS에 긴밀하게 결합됩니다. 그러나 Web API가 OWIN을 구현하므로 이를 호스팅하는 웹 서버에서 분리된 웹 응용 프로그램을 작성할 수 있습니다. 이 때문에 사용자 고유의 프로세스에서 시작할 수 있는 *자체 호스팅* OWIN 웹 서버를 사용할 수 있습니다. 방금 설명한 서비스 패브릭 호스팅 모델에 완벽하게 적합합니다.

이 문서에서는 Katana를 Web API 응용 프로그램의 OWIN 호스트로 사용합니다. Katana는 [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) 및 Windows [HTTP 서버 API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)를 기반으로 하는 오픈 소스 OWIN 호스트 구현입니다.

> [AZURE.NOTE] Katana에 대한 자세한 내용은 [Katana 사이트](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)로 이동합니다. Katana를 사용하여 Web API를 자체 호스팅하는 방법의 간략한 개요는 [OWIN을 사용하여 ASP.NET Web API 2 자체 호스팅](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)을 참조하세요.


## 웹 서버 설정

Reliable Services API는 사용자와 클라이언트가 서비스에 연결할 수 있도록 해주는 통신 스택을 연결할 수 있는 통신 진입점을 제공합니다.

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

웹 서버(및 WebSockets과 같이 나중에 사용할 수 있는 기타 통신 스택)은 ICommunicationListener 인터페이스를 사용하여 올바르게 시스템과 통합해야 합니다. 그 이유는 다음 단계에서 더욱 명확해집니다.

먼저 ICommunicationListener를 구현하는 OwinCommunicationListener라는 클래스를 만듭니다.

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

ICommunicationListener 인터페이스에서는 서비스에 대한 통신 수신기를 관리하는 세 가지 메서드를 제공합니다.

 - *OpenAsync*. 요청에 대한 수신 대기를 시작합니다.
 - *CloseAsync*. 요청에 대한 수신 대기를 중지하고, 진행 중인 모든 요청을 완료하고, 정상적으로 종료합니다.
 - *Abort*. 모든 것을 취소하고 즉시 중지합니다.

시작하려면 수신기가 작동하는 데 필요한 항목에 대한 private 클래스 멤버를 추가합니다. 이는 생성자를 통해 초기화되며 나중에 수신 대기 URL을 설정할 때 사용됩니다.

```csharp
public class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## OpenAsync 구현

웹 서버를 설정하려면 다음 두 가지 정보가 필요합니다.

 - *URL 경로 접두사*. 선택 사항이지만 응용 프로그램의 여러 웹 서비스를 안전하게 호스팅할 수 있도록 이를 설정하는 것이 좋습니다.
 - *포트*.

웹 서버에 대한 포트를 가져오기 전에 서비스 패브릭에서 응용 프로그램과 응용 프로그램이 실행되는 기본 운영 체제 사이에서 버퍼 역할을 하는 응용 프로그램 계층을 제공한다는 사실을 이해하는 것이 중요합니다. 이와 같이 서비스 패브릭은 서비스의 *끝점*을 구성하는 방법을 제공합니다. 서비스 패브릭은 사용할 서비스에 끝점은 사용할 수 있도록 합니다. 이러한 방식으로 기본 OS 환경에서 끝점을 직접 구성할 필요가 없습니다. 응용 프로그램에 변경 내용을 확인하지 않고도 다양한 환경에서 서비스 패브릭 응용 프로그램을 쉽게 호스팅할 수 있습니다. (예를 들어 Azure 또는 자체 데이터 센터에서 동일한 응용 프로그램을 호스팅할 수 있습니다.)

PackageRoot\\ServiceManifest.xml에 HTTP 끝점을 구성합니다.

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

이 단계는 서비스 호스트 프로세스가 제한된 자격 증명(Windows의 네트워크 서비스)에서 실행되기 때문에 중요합니다. 즉, 서비스에 자체적으로 HTTP 끝점을 설정하는 액세스 권한이 없습니다. 끝점 구성을 사용하여 서비스 패브릭은 서비스가 수신 대기할 URL에 대한 적절한 ACL(액세스 제어 목록)을 설정하는 방법을 압니다. 또한 서비스 패브릭은 끝점을 구성하는 표준 위치를 제공합니다.


다시 OwinCommunicationListener.cs에서 OpenAsync 구현을 시작할 수 있습니다. 여기에서 웹 서버를 시작합니다. 먼저, 끝점 정보를 가져오고 서비스에서 수신 대기할 URL을 만듭니다. URL은 수신기가 상태 비저장 서비스에서 사용되는지 또는 상태 저장 서비스에서 사용되는지 여부에 따라 다릅니다. 상태 저장 서비스의 경우 수신기는 수신 대기하는 모든 상태 저장 서비스 복제본의 고유 주소를 만들어야 합니다. 상태 비저장 서비스의 경우 주소가 훨씬 간단할 수 있습니다.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

여기에 "http://+"가 사용된 점에 주목하시기 바랍니다. 이는 웹 서버가 localhost, FQDN 및 컴퓨터 IP를 비롯한 사용 가능한 모든 주소에서 수신 대기하도록 합니다.

OpenAsync 구현은 웹 서버(또는 모든 통신 스택)를 서비스의 `RunAsync()`에서 바로 여는 대신 ICommunicationListener로 구현하는 가장 중요한 이유 중 하나입니다. OpenAsync의 반환 값은 웹 서버에서 수신 대기 중인 주소입니다. 이 주소가 시스템에 반환되면 서비스에 주소가 등록됩니다. 서비스 패브릭은 클라이언트 및 기타 서비스가 서비스 이름별로 이 주소를 요청할 수 있게 해주는 API를 제공합니다. 서비스 주소가 정적이 아니기 때문에 중요합니다. 서비스는 리소스 균형 조정 및 가용성을 위한 클러스터 주변으로 이동됩니다. 이는 클라이언트가 서비스의 수신 대기 주소를 확인할 수 있도록 하는 메커니즘입니다.

OpenAsync는 이를 염두에 두고 웹 서버를 시작하고 수신 대기 중인 주소를 반환합니다. "http://+"에서 수신 대기하고 있지만 OpenAsync가 주소를 반환하기 전에 "+"가 현재 있는 노드의 IP 또는 FQDN으로 바뀝니다. 메서드에서 반환되는 주소는 시스템으로 등록됩니다. 또는 클라이언트 및 기타 서비스가 서비스의 주소를 요청할 때 표시됩니다. 클라이언트가 이 주소에 올바르게 연결하려면 주소에 실제 IP 또는 FQDN이 필요합니다.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

이는 생성자의 OwinCommunicationListener에 전달된 시작 클래스를 참조합니다. 이 시작 인스턴스는 웹 서버에서 Web API 응용 프로그램을 부트스트랩하는 데 사용됩니다.

나중에 응용 프로그램을 실행하여 웹 서버가 성공적으로 시작되었음을 확인하면 진단 이벤트 창에 `ServiceEventSource.Current.Message()` 줄이 표시됩니다.

## CloseAsync 및 Abort 구현

마지막으로, 웹 서버를 중지하는 CloseAsync 및 Abort를 구현합니다. OpenAsync 중에 만들어진 서버 핸들을 삭제하여 웹 서버를 중지할 수 있습니다.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

이 구현 예제에서는 CloseAsync와 Abort가 웹 서버를 간단히 중지합니다. CloseAsync에서 웹 서버가 더욱 원활하게 조정된 종료를 수행하도록 선택할 수 있습니다. 예를 들어 종료는 반환하기 전에 완료해야 하는 진행 중인 요청을 대기할 수 있습니다.

## 웹 서버 시작

이제 만들고 웹 서버를 시작하는 OwinCommunicationListener의 인스턴스를 만들고 반환할 준비가 되었습니다. 다시 Service 클래스(Service.cs)에서 `CreateServiceInstanceListeners()` 메서드를 재정의합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Web API *응용 프로그램* 및 OWIN *호스트*가 마침내 충족되는 위치입니다. 호스트(OwinCommunicationListener)에는 Startup 클래스를 통해 *응용 프로그램*(Web API)의 인스턴스가 지정됩니다. 그런 다음 서비스 패브릭이 수명 주기를 관리합니다. 일반적으로 모든 통신 스택 뒤에 이와 동일한 패턴이 올 수 있습니다.

## 모든 요소 결합

이 예제에서는 재정의를 간단히 제거할 수 있도록 `RunAsync()` 메서드에서 어떤 작업도 수행할 필요가 없습니다.

마지막 서비스 구현은 매우 간단해야 합니다. 통신 수신기를 만드는 데 필요합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

전체 `OwinCommunicationListener` 클래스는 다음과 같습니다.

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

이제 모든 항목을 결합하면 프로젝트는 Reliable Services API 진입점 및 OWIN 호스트가 있는 일반적인 Web API 응용 프로그램과 유사해야 합니다.


![Reliable Services API 진입점 및 OWIN 호스트를 사용하는 Web API](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## 웹 브라우저를 통한 실행 및 연결

아직 하지 않았다면 [개발 환경을 설정](service-fabric-get-started.md)합니다.


이제 서비스를 빌드하고 배포할 수 있습니다. Visual Studio에서 **F5** 키를 눌러 응용 프로그램을 빌드하고 배포합니다. 진단 이벤트 창에서 웹 서버가 http://localhost:8281/에 열렸음을 나타내는 메시지가 표시됩니다.


![Visual Studio 진단 이벤트 창](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] 컴퓨터에서 다른 프로세스에 의해 포트가 이미 열린 경우 오류가 표시될 수 있습니다. 수신기를 열지 못했음을 나타냅니다. 이러한 경우 ServiceManifest.xml에서 끝점 구성에 다른 포트를 사용해 보세요.


서비스가 실행되면 브라우저를 열고 [http://localhost:8281/api/values](http://localhost:8281/api/values)로 이동하여 테스트합니다.

## 확장

상태 비저장 웹앱을 확장하려면 일반적으로 더 많은 컴퓨터를 추가하고 해당 컴퓨터에서 웹앱을 구동합니다. 서비스 패브릭의 오케스트레이션 엔진은 클러스터에 새 노드가 추가될 때마다 이 작업을 자동으로 수행합니다. 상태 비저장 서비스의 인스턴스를 만들 때 만들려는 인스턴스 수를 지정할 수 있습니다. 서비스 패브릭은 클러스터의 노드에 해당 개수의 인스턴스를 배치합니다. 그리고 한 노드에 둘 이상의 인스턴스를 만들지 않도록 합니다. 또한 인스턴스 수에 **-1**을 지정하여 서비스 패브릭이 모든 노드에서 항상 하나의 인스턴스만 만들도록 지시할 수 있습니다. 이렇게 하면 클러스터를 확장하기 위해 노드를 추가할 때마다 새 노드에 상태 비저장 서비스의 인스턴스가 생성됩니다. 이 값은 서비스 인스턴스의 속성이므로 서비스 인스턴스를 만들 때 설정됩니다. PowerShell을 통해 수행할 수 있습니다.

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

또는 Visual Studio 상태 비저장 서비스 프로젝트의 기본 서비스를 정의할 때 수행할 수 있습니다.

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

응용 프로그램 및 서비스 인스턴스를 만드는 방법에 대한 자세한 내용은 [응용 프로그램 배포](service-fabric-deploy-remove-applications.md)를 참조하세요.

## 다음 단계

[Visual Studio를 사용하여 서비스 패브릭 응용 프로그램 디버그](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0914_2016-->