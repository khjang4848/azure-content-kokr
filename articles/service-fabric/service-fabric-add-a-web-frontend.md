<properties
   pageTitle="ASP.NET Core를 사용하여 응용 프로그램에 대한 프런트 엔드 만들기 | Microsoft Azure"
   description="ServiceProxy를 통해 ASP.NET Core Web API 프로젝트 및 서비스 간 통신을 사용하여 서비스 패브릭 응용 프로그램을 웹에 노출합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# ASP.NET Core를 사용하여 응용 프로그램에 대한 웹 서비스 프런트 엔드 구축

기본적으로 Azure 서비스 패브릭 서비스는 웹에 공용 인터페이스를 제공하지 않습니다. HTTP 클라이언트에 응용 프로그램의 기능을 표시하려면 진입점 역할을 할 웹 프로젝트를 만든 후 그 곳에서 개별 서비스와 통신해야 합니다.

이 자습서에서는 [Visual Studio에서 첫 번째 응용 프로그램 만들기](service-fabric-create-your-first-application-in-visual-studio.md) 자습서에서 남겨둔 항목을 선택하고 상태 저장 카운터 서비스 앞에 웹 서비스를 추가합니다. 아직 수행하지 않은 경우 다시 돌아가서 먼저 해당 자습서를 단계별로 실행해야 합니다.

## 응용 프로그램에 ASP.NET Core 서비스 추가

ASP.NET Core는 최신 웹 UI 및 Web API를 만드는 데 사용할 수 있는 가벼운 크로스 플랫폼 웹 개발 프레임워크입니다. 기존 응용 프로그램에 ASP.NET Web API 프로젝트를 추가하겠습니다.

>[AZURE.NOTE] 이 자습서를 완료하려면 [.NET Core 1.0을 설치][dotnetcore-install]해야 합니다.

1. 솔루션 탐색기에서 응용 프로그램 프로젝트 내의 **서비스**를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 서비스 패브릭 서비스**를 선택합니다.

	![기존 응용 프로그램에 새 서비스 추가][vs-add-new-service]

2. **서비스 만들기** 페이지에서 **ASP.NET Core**를 선택하고 이름을 입력합니다.

	![새 서비스 대화 상자에서 ASP.NET 웹 서비스 선택][vs-new-service-dialog]

3. 다음 페이지에서는 ASP.NET Core 프로젝트 템플릿 집합을 제공합니다. 이러한 템플릿은 서비스 패브릭 응용 프로그램 외부에서 ASP.NET Core 프로젝트를 만든 경우에 표시되는 템플릿과 동일한 템플릿입니다. 이 자습서에서는 **Web API**를 선택합니다. 그러나 전체 웹 응용 프로그램을 구축하는 동일한 개념을 적용할 수 있습니다.

	![ASP.NET 프로젝트 유형 선택][vs-new-aspnet-project-dialog]

    Web API 프로젝트를 만들었으면 응용 프로그램에 두 개의 서비스가 있을 것입니다. 계속해서 응용 프로그램을 구축하는 동안 똑같은 방법으로 더 많은 서비스를 추가합니다. 각 서비스를 독립적으로 버전 지정 및 업그레이드할 수 있습니다.

>[AZURE.TIP] ASP.NET Core 서비스를 구축하는 방법에 대한 자세한 내용 [ASP.NET Core 설명서](https://docs.asp.net)를 참조하세요.

## 응용 프로그램 실행

지금까지 수행한 작업의 결과를 살펴보려면 새 응용 프로그램을 배포하고 ASP.NET Core Web API 템플릿에서 제공하는 기본 동작을 살펴보겠습니다.

1. Visual Studio에서 F5를 눌러 앱을 디버깅합니다.

2. 배포가 완료되면 Visual Studio에서 http://localhost:33003처럼 ASP.NET Web API 서비스의 루트에 브라우저를 실행합니다. 포트 번호는 임의로 할당되며 컴퓨터마다 다를 수 있습니다. ASP.NET Core Web API 템플릿은 루트에 대한 기본 동작을 제공하지 않으므로 브라우저에서 오류가 발생할 것입니다.

3. 브라우저에서 해당 위치에 `/api/values`를 추가합니다. 이렇게 하면 Web API 템플릿에서 ValuesController `Get` 메서드를 호출합니다. 두 개의 문자열을 포함하는 JSON 배열인 템플릿에서 제공하는 기본 응답을 반환합니다.

    ![ASP.NET Core Web API 템플릿에서 반환되는 기본값][browser-aspnet-template-values]

    이 자습서가 끝나기 전에 이러한 기본 값을 상태 저장 서비스의 가장 최신 카운터 값으로 바꿀 것입니다.


## 서비스 연결

서비스 패브릭은 신뢰할 수 있는 서비스와 유연하게 통신할 수 있는 방법을 제공합니다. 단일 응용 프로그램 내에 TCP를 통해 액세스할 수 있는 서비스, HTTP REST API를 통해 액세스할 수 있는 다른 서비스 및 웹 소켓을 통해 액세스할 수 있는 다른 서비스가 있을 수 있습니다. 제공되는 옵션 및 관련 장단점에 대한 배경 정보는 [서비스와의 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요. 이 자습서에서는 보다 간단한 방법 중 하나를 선택하여 SDK에 제공되는 `ServiceProxy`/`ServiceRemotingListener` 클래스를 사용하겠습니다.

`ServiceProxy` 접근 방식(원격 프로시저 호출 또는 RPC에서 모델링)에서 인터페이스를 정의하여 서비스에 대한 공용 계약의 역할을 합니다. 해당 인터페이스를 사용하여 그런 다음 서비스와 상호 작용하기 위한 프록시 클래스를 생성합니다.


### 인터페이스 만들기

ASP.NET Core 프로젝트를 비롯한 상태 저장 서비스와 클라이언트 간의 계약 역할을 수행할 인터페이스부터 만들겠습니다.

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.

2. 왼쪽의 탐색 창에서 **Visual C#** 항목을 선택한 다음 **클래스 라이브러리** 템플릿을 선택합니다. .NET Framework 버전이 **4.5.2**로 설정되어 있는지 확인합니다.

    ![상태 저장 서비스에 대한 인터페이스 프로젝트 만들기][vs-add-class-library-project]

3. `ServiceProxy`에서 사용할 수 있는 인터페이스를 위해 IService 인터페이스에서 파생해야 합니다. 이 인터페이스는 서비스 패브릭 NuGet 패키지 중 하나에 포함됩니다. 패키지에 추가하려면 새 클래스 라이브러리 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

4. **Microsoft.ServiceFabric.Services** 패키지를 검색하고 설치합니다.

    ![서비스 NuGet 패키지 추가][vs-services-nuget-package]

5. 클래스 라이브러리에서 단일 메서드 `GetCountAsync`를 사용하여 인터페이스를 만들고, IService에서 해당 인터페이스를 확장합니다.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### 상태 저장 서비스에서 인터페이스 구현

인터페이스를 정의했으니 이제는 상태 저장 서비스를 구현해야 합니다.

1. 상태 저장 서비스에서 인터페이스가 포함된 클래스 라이브러리 프로젝트에 참조를 추가합니다.

    ![상태 저장 서비스의 클래스 라이브러리 프로젝트에 참조 추가][vs-add-class-library-reference]

2. `MyStatefulService`처럼 `StatefulService`에서 상속하는 클래스를 찾아 확장하여 `ICounter` 인터페이스를 구현합니다.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 이제 `ICounter` 인터페이스인 `GetCountAsync`에 정의된 단일 메서드를 구현합니다.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### 서비스 원격 수신기를 사용하여 상태 저장 서비스를 노출합니다.

`ICounter` 인터페이스가 구현되었으니, 이제 통신 채널만 열면 다른 서비스에서 상태 저장 서비스를 호출할 수 있습니다. 상태 저장 서비스의 경우 서비스 패브릭은 `CreateServiceReplicaListeners`라는 재정의 가능한 메서드를 제공합니다. 이 메서드로 서비스에 사용하려는 통신 형식에 따라 하나 이상의 통신 수신기를 지정할 수 있습니다.

>[AZURE.NOTE] 상태 비저장 서비스에 대한 통신 채널을 열기 위해 제공되는 메서드는 `CreateServiceInstanceListeners`입니다.

이 경우에서는 기존 `CreateServiceReplicaListeners` 메서드를 바꾸고 `ServiceRemotingListener`의 인스턴스를 제공하며 이는 `ServiceProxy`을 통해 클라이언트에서 호출 가능한 RPC 끝점을 만듭니다.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### ServiceProxy 클래스를 사용하여 서버와 상호 작용

상태 저장 서비스는 다른 서비스에서 트래픽을 받을 준비가 되었습니다. 따라서 ASP.NET 웹 서비스에서 해당 서비스와 통신하도록 코드를 추가하는 작업이 남았습니다.

1. ASP.NET 프로젝트에서 `ICounter` 인터페이스가 포함된 클래스 라이브러리에 참조를 추가합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 엽니다. 다음과 유사한 결과가 표시됩니다.

    ![AnyCPU로 클래스 라이브러리를 보여 주는 구성 관리자][vs-configuration-manager]

    임의 CPU를 구축하기 위해 클래스 라이브러리 프로젝트 **MyStatefulService.Interface**가 구성됩니다. 서비스 패브릭을 바르게 작동하려면 x64에서 명시적으로 대상이 지정되어야 합니다. 플랫폼 드롭다운을 클릭하고 **새로 만들기**를 선택한 후 x64 플랫폼 구성을 만듭니다.

    ![클래스 라이브러리에 대한 새로운 플랫폼 만들기][vs-create-platform]

3. 앞에서 클래스 라이브러리 프로젝트에 수행한 것처럼 Microsoft.ServiceFabric.Services 패키지를 ASP.NET 프로젝트에 추가합니다. 이렇게 하면 `ServiceProxy` 클래스가 제공됩니다.

4. **Controllers** 폴더에서 `ValuesController` 클래스를 엽니다. `Get` 메서드가 현재는 하드 코드된 문자열 배열 "value1" 및 "value2"만 반환하며 이 문자열은 앞서 브라우저에서 본 것과 일치합니다. 이것을 다음 코드로 바꿉니다.

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    코드의 첫 번째 줄이 핵심입니다. 상태 저장 서비스에 ICounter 프록시를 만들려면 두 가지 정보, 즉 파티션 ID와 서비스 이름을 제공해야 합니다.

    고객 ID, 우편 번호 등 사용자가 정의하는 키를 기반으로 상태를 여러 버킷으로 분류하여 상태 저장 서비스를 확장하도록 파티션을 사용할 수 있습니다. 간단한 응용 프로그램에서 상태 저장 서비스에는 파티션이 하나밖에 없으므로 키는 문제가 되지 않습니다. 아무 키를 입력해도 같은 파티션으로 연결됩니다. 서비스 분할에 대해 자세히 알아보려면 [서비스 패브릭 Reliable Services 분할 방법](service-fabric-concepts-partitioning.md)을 참조하세요.

    서비스 이름은 양식 패브릭의 URI입니다(예: /&lt;application\_name&gt;/&lt;service\_name&gt;).

    서비스 패브릭에서는 이러한 두 가지 정보를 사용하여 요청을 보내야 하는 컴퓨터를 식별할 수 있습니다. 또한 `ServiceProxy` 클래스는 상태 저장 서비스 파티션을 호스트하는 컴퓨터에 장애가 발생하여 다른 컴퓨터가 그 역할을 대신해야 하는 경우를 원활하게 처리할 수 있습니다. 이 추상화를 사용하면 다른 서비스를 처리하기 위한 클라이언트 코드 쓰기 작업이 훨씬 간단해집니다.

    프록시가 있으면 `GetCountAsync` 메서드만 호출하면 그 결과가 반환됩니다.

5. 수정된 응용 프로그램을 실행하려면 F5 키를 다시 누르면 됩니다. 이전과 마찬가지로, Visual Studio에서 자동으로 웹 프로젝트의 루트에 브라우저를 실행합니다. "api/values" 경로를 추가하면 반환되는 현재 카운터 값이 표시되야 합니다.

    ![브라우저에 표시되는 상태 저장 카운터 값][browser-aspnet-counter-value]

    브라우저를 주기적으로 새로 고쳐 카운터 값 업데이트를 확인하세요.


>[AZURE.WARNING] 템플릿 Kestrel에 제공된 ASP.NET Core 웹 서버는 [현재 직접 인터넷 트래픽을 처리를 지원하지 않습니다](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). 프로덕션 시나리오의 경우 [API 관리][api-management-landing-page] 또는 다른 인터넷 게이트웨이 뒤에 ASP.NET Core 끝점을 호스트하는 것이 좋습니다. 서비스 패브릭은 IIS 내에서 배포하도록 지원되지 않습니다.


## 행위자의 경우는 어떨까요?

이 자습서는 상태 저장 서비스와 통신하는 웹 프런트 엔드를 추가하는 방법에 초점을 맞추고 있습니다. 그러나 매우 비슷한 모델에 따라 행위자와 통신할 수 있습니다. 사실, 방법은 더 간단합니다.

사용자가 행위자 프로젝트를 만들면 Visual Studio에서 자동으로 인터페이스를 생성합니다. 사용자는 이 인터페이스를 사용하여 웹 프로젝트에 행위자와 통신하기 위한 행위자 프록시를 생성할 수 있습니다. 통신 채널은 자동으로 제공됩니다. 따라서 사용자는 이 자습서에서 상태 저장 서비스에 대해 수행한 대로 `ServiceRemotingListener`를 설정하기 위해 동일한 작업을 수행할 필요가 없습니다.

## 웹 서비스가 로컬 클러스터에서 작동하는 방식

일반적으로 로컬 클러스터에 배포된 다중 컴퓨터 클러스터에 동일한 서비스 패브릭 응용 프로그램을 정확히 배포할 수 있고 응용 프로그램이 예상대로 작동할 것으로 확신할 수 있습니다. 왜냐하면 로컬 클러스터가 단일 컴퓨터로 축소된 5 노드 구성이기 때문입니다.

그러나 웹 서비스는 결정적인 차이가 하나 있습니다. Azure처럼 클러스터가 부하 분산 장치 뒤에 있는 경우 모든 컴퓨터에 웹 서비스를 배포해야 합니다. 부하 분산 장치가 단순히 라운드 로빈 방식으로 컴퓨터 간에 트래픽을 분산하기 때문입니다. 서비스에 대한 `InstanceCount`를 특수 값인 "-1"로 설정하여 수행할 수 있습니다.

반면 웹 서비스를 로컬로 실행하는 경우 서비스의 인스턴스가 하나만 실행하도록 해야 합니다. 그렇지 않으면 동일한 경로 및 포트에서 수신하는 여러 프로세스에서 충돌을 일으킵니다. 결과적으로 로컬 배포에 대해 웹 서비스 인스턴스 수를 "1"로 설정해야 합니다.

여러 환경에 대한 여러 가지 구성 방법을 알아보려면 [여러 환경에 대한 응용 프로그램 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)를 참조하세요.

## 다음 단계

- [Azure에서 클러스터를 생성하여 클라우드에 응용 프로그램 배포](service-fabric-cluster-creation-via-portal.md)
- [서비스와의 통신에 대한 자세한 정보](service-fabric-connect-and-communicate-with-services.md)
- [상태 저장 서비스 분할에 대한 자세한 정보](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/

<!---HONumber=AcomDC_0817_2016-->