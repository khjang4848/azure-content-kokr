<properties 
	pageTitle="Application Insights를 사용하여 ASP.NET 앱의 실패 및 예외 진단" 
	description="요청 원격 분석과 함께 ASP.NET 앱에서 예외를 캡처합니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>


# Application Insights 설정: 예외 진단

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


[Visual Studio Application Insights][start]로 응용 프로그램을 모니터링하면 클라이언트와 서버에서 실패한 요청을 예외 및 다른 이벤트와 상호 연결하여 원인을 신속하게 진단할 수 있습니다.

ASP.NET 앱을 모니터링하려면 응용 프로그램에 [Application Insights SDK를 추가][greenbrown]하거나, [IIS 서버에 상태 모니터를 설치][redfield]하거나, 또는 앱이 Azure 웹 앱인 경우 [Application Insights Extension](app-insights-azure-web-apps.md)을 설치해야 합니다.

## Visual Studio를 사용하여 예외 진단

디버깅에 도움이 되도록 Visual Studio에서 앱 솔루션을 엽니다.

F5 키를 사용하여 서버 또는 개발 컴퓨터에서 앱을 실행합니다.

Visual Studio에서 Application Insights 검색 창을 열고 앱에서 이벤트를 표시하도록 설정합니다. 디버깅하는 동안 Application Insights 단추를 클릭하여 이를 수행할 수 있습니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 및 열기를 선택합니다.](./media/app-insights-asp-net-exceptions/34.png)

예외를 표시하도록 보고서를 필터링 할 수 있습니다.

*예외가 표시되지 않나요? [예외 캡처](#exceptions)를 참조하세요.*

예외 보고서를 클릭하여 해당 스택 추적을 표시합니다.

![예외를 통해 클릭합니다.](./media/app-insights-asp-net-exceptions/35.png)

스택 추적에서 참조를 클릭하여 관련 파일을 엽니다.

## Azure 포털을 사용하여 오류 진단

앱의 Application Insights 개요에서 실패 타일은 가장 많은 실패를 유발하는 요청 URL 목록과 함께 예외 및 실패한 HTTP 요청에 대한 차트를 보여 줍니다.

![설정 선택, 오류](./media/app-insights-asp-net-exceptions/012-start.png)

목록의 실패한 요청 유형 중 하나를 클릭하면 개별 실패 항목이 표시됩니다. 여기서 해당 항목에 관련된 예외 또는 모든 추적 데이터를 찾아갑니다.

![실패한 요청 인스턴트를 선택하고, 예외 세부 정보 아래에서 예외 인스턴스로 이동합니다.](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**또는** 더 아래에 있는 실패 블레이드의 예외 목록에서 시작할 수 있습니다. 개별 예외에 도달할 때까지 계속 클릭합니다.


![드릴스루](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*예외가 표시되지 않나요? [예외 캡처](#exceptions)를 참조하세요.*

여기서 스택 추적 및 각 예외의 자세한 속성을 살펴보고 관련 로그 추적 또는 기타 이벤트를 찾을 수 있습니다.


![드릴스루](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[진단 검색에 대해 자세히 알아보세요][diagnostic].



## 종속성 실패

*종속성*은 응용 프로그램에서 일반적으로 REST API 또는 데이터베이스 연결을 통해 호출하는 서비스입니다. [Application Insights 상태 모니터][redfield]는 다양한 유형의 종속성 호출을 자동으로 모니터링하여 호출 기간과 성공 또는 실패 여부를 측정합니다.

종속성 데이터를 얻으려면 IIS 서버에 [상태 모니터를 설치][redfield]하거나 또는 앱이 Azure 웹 앱인 경우 [Application Insights Extension](app-insights-azure-web-apps.md)을 사용해야 합니다.

실패한 종속성 호출은 실패 블레이드에 표시되며, 요청 세부 정보 및 예외 세부 정보의 관련 항목 아래에서도 찾을 수 있습니다.

*종속성 실패가 없나요? 좋은 현상입니다. 하지만 종속성 데이터가 있는지 확인하기 위해 성능 블레이드를 열고 종속성 기간 차트를 살펴보세요.*

 

## 사용자 지정 추적 및 로그 데이터

진단 데이터 특성을 사용자 프로그램으로 불러오려면, 사용자 고유의 원격 분석 전송에 코드를 삽입합니다. 요청, 페이지 보기 및 기타 자동으로 수집된 데이터와 함께 진단 검색에 표시됩니다.

여러 옵션이 있습니다.

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event)는 일반적으로 사용 패턴 모니터링을 위해 사용되지만 진단 검색의 사용자 지정 이벤트에서도 전송하는 데이터를 표시합니다. 이벤트의 이름을 지정하고, [진단 검색을 필터링][diagnostic]할 수 있는 문자열 속성 및 숫자 메트릭 수를 수행할 수있습니다.
* 사용자는 [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace)를 사용하여 POST 정보와 같은 긴데이터를 보낼 수있습니다.
* [TrackException()](#exceptions)은 스택 추적을 보냅니다. [예외에 대해 자세히 알아보세요](#exceptions).
* 사용자가 이미 Log4Net 또는 NLog와 같은 로깅 프레임워크를 사용하는 경우, 요청과 예외 데이터와 함께 진단 검색 안에서 [이러한 로그를 캡처][netlogs]하고 볼 수 있습니다.

이러한 이벤트를 보려면, [검색][diagnostic]과 필터를 차례대로 열고 사용자 지정 이벤트, 추적 또는 예외를 선택합니다.


![드릴스루](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)


> [AZURE.NOTE] 앱에서 다양한 원격 분석을 생성하는 경우 적응 샘플링 모듈은 이벤트의 대표적인 분수만 전송하여 포털에 전송되는 볼륨을 자동으로 줄입니다. 동일한 작업에 속하는 이벤트를 그룹으로 선택하거나 선택 취소되므로 관련된 이벤트를 탐색할 수 있습니다. [샘플링에 대해 알아봅니다.](app-insights-sampling.md)

### 요청 게시 데이터를 참조하는 방법

요청 세부 정보에는 POST 호출에서 앱으로 전송된 데이터가 포함되지 않습니다. 이 데이터에 대한 보고를 받으려면 다음을 수행합니다.

* 응용 프로그램 프로젝트에 [SDK를 설치][greenbrown]합니다.
* 응용 프로그램에 코드를 삽입하여 [Microsoft.ApplicationInsights.TrackTrace()][api]를 호출합니다. 메시지 매개 변수의 POST 데이터를 보냅니다. 허용되는 크기에 제한이 있으므로 꼭 필요한 데이터만 보내야 합니다.
* 실패한 요청을 조사할 때 연결된 추적을 찾습니다.

![드릴스루](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a> 예외 및 관련 진단 데이터 캡처

처음에는 앱에서 실패를 유발하는 예외가 포털에 전부 표시되지 않을 것입니다. 웹 페이지에서 [JavaScript SDK][client]를 사용 중이라면 브라우저 예외가 보일 것입니다. 하지만 대부분 서버 예외는 IIS에서 catch하며 서버 예외를 보려면 약간의 코드를 작성해야 합니다.

다음을 수행할 수 있습니다.

* 예외 처리기에 예외를 보고하는 코드를 삽입하여 **예외를 명시적으로 기록**합니다.
* ASP.NET 프레임워크를 구성하여 **예외를 자동으로 캡처**합니다. 프레임워크 유형에 따라 추가할 항목이 다릅니다.

## 예외를 명시적으로 보고

가장 간단한 방법은 예외 처리기에 TrackException()에 대한 호출을 삽입하는 것입니다.

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

속성 및 측정 매개 변수는 선택적이지만 추가 정보를 [필터링 및 추가][diagnostic]하는 데 유용합니다. 예를 들어 여러 게임을 실행할 수 있는 앱이 있는 경우 특정 게임과 관련된 모든 예외 보고서를 찾을 수 있습니다. 각 사전에 원하는 만큼 항목을 추가할 수 있습니다.

## 브라우저 예외

대부분의 브라우저 예외가 보고됩니다.

웹 페이지에 콘텐츠 배달 네트워크 또는 다른 도메인의 스크립트 파일이 포함되는 경우 스크립트 태그에 ```crossorigin="anonymous"``` 특성이 있고 서버에서 [CORS 헤더](http://enable-cors.org/)를 전송하는지 확인하세요. 이러한 리소스에서 처리되지 않은 JavaScript 예외에 대한 스택 추적 및 세부 정보를 가져올 수 있을 것입니다.

## 웹 양식

웹 양식의 경우 CustomErrors를 사용하여 구성된 리디렉션이 없으면 HTTP 모듈에서 예외를 수집할 수 있습니다.

하지만 활성 리디렉션이 있다면 Global.asax.cs의 Application\_Error 함수에 다음 줄을 추가합니다 (아직 없는 경우 Global.asax 파일 추가).

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

[CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx)가 `Off`로 구성되어 있으면 [HTTP 모듈](https://msdn.microsoft.com/library/ms178468.aspx)에서 예외를 수집할 수 있습니다. 그러나 `RemoteOnly`(기본값) 또는 `On`으로 설정되어 있으면 예외가 지워지고 Application Insights에서 자동으로 수집할 수 없습니다. [System.Web.Mvc.HandleErrorAttribute 클래스](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)를 재정의하고, 재정의된 클래스를 아래와 같이 다른 MVC 버전에 적용하여 이 문제를 해결할 수 있습니다([github 자료](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)).

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

HandleError 특성을 컨트롤러의 새 특성으로 바꿉니다.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[샘플](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

Global.asax.cs에서 `AiHandleErrorAttribute`를 글로벌 필터로 등록합니다.

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[샘플](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4, MVC5

FilterConfig.cs에서 AiHandleErrorAttribute를 글로벌 필터로 등록합니다.

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[샘플](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## Web API 1.x


System.Web.Http.Filters.ExceptionFilterAttribute를 재정의합니다.

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

재정의된 특성을 특정 컨트롤러에 추가하거나 WebApiConfig 클래스에서 글로벌 필터 구성에 추가할 수 있습니다.

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[샘플](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

예외 필터에서 처리할 수 없는 다양한 경우가 있습니다. 예:

* 컨트롤러 생성자에서 throw된 예외
* 메시지 처리기에서 throw된 예외
* 라우팅 중에 throw된 예외
* 응답 콘텐츠를 직렬화하는 동안 throw된 예외

## Web API 2.x

IExceptionLogger를 추가로 구현합니다.

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

WebApiConfig에서 서비스에 추가합니다.

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[샘플](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

또는 다음 방법을 사용해도 됩니다.

2. 유일한 ExceptionHandler를 IExceptionHandler의 사용자 지정 구현으로 바꿉니다. 이 특성은 프레임 워크에서 보낼 응답 메시지를 선택할 수 있는 때에만 호출됩니다. 인스턴스에 대한 연결이 끊어지면 호출되지 않습니다.
3. 위의 Web API 1.x 컨트롤러 섹션에서 설명한 것처럼 예외 필터는 어떤 경우에도 호출되지 않습니다.


## WCF

특성을 확장하고 IErrorHandler 및 IServiceBehavior를 구현하는 클래스를 추가합니다.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

서비스 구현에 특성을 추가합니다.

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[샘플](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## 예외 성능 카운터

서버에 [상태 모니터를 설치][redfield]한 경우 .NET에서 측정된 예외 속도 차트를 확인할 수 있습니다. 여기에는 처리된 .NET 예외와 처리되지 않은 .NET 예외가 모두 포함됩니다.

메트릭 탐색기 블레이드를 열고 새 차트를 추가한 다음 성능 카운터 아래에 나열된 **예외 속도**를 선택합니다.

.NET Framework는 간격의 예외 수를 계산하고 간격의 길이로 나누어 속도를 계산합니다.

TrackException 보고서를 계산하여 Application Insights 포털에서 계산되는 '예외' 개수와는 다릅니다. 샘플링 간격이 다르며, SDK에서 처리된 예외 및 처리되지 않은 예외 둘 다에 대한 TrackException 보고서를 보내지 않습니다.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0914_2016-->