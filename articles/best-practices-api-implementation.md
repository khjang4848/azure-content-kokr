<properties
   pageTitle="API 구현 지침 | Microsoft Azure"
   description="API를 구현하는 방법에 대한 지침입니다."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>

# API 구현 지침

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

이 지침에 포함된 일부 항목은 논의 중이며 나중에 변경될 수 있습니다. 귀하의 피드백을 환영합니다!

## 개요
신중하게 설계된 RESTful Web API는 클라이언트 응용 프로그램에 액세스할 수 있는 리소스, 관계, 탐색 스키마를 정의합니다. Web API를 구현하고 배포하는 경우 Web API를 호스팅하는 환경의 실제 요구 사항을 고려하고 데이터의 논리 구조보다는 Web API가 생성된 방식을 고려해야 합니다. 이 지침은 Web API를 구현하고 클라이언트 응용 프로그램에서 사용할 수 있도록 게시하는 것에 대한 모범 사례를 집중적으로 살펴봅니다. 보안 문제는 API 보안 지침 문서에 별도로 설명됩니다. Web API 디자인에 대한 자세한 정보는 API 디자인 지침 문서에서 찾을 수 있습니다.

## RESTful Web API 구현에 대한 고려 사항
다음 섹션에서는 RESTful Web API를 빌드하기 위한 ASP.NET Web API 템플릿 사용의 모범 사례를 보여줍니다. Web API 템플릿 사용에 대한 자세한 정보를 보려면 Microsoft 웹 사이트의 [Learn About ASP.NET Web API](http://www.asp.net/web-api) 페이지를 방문합니다.

## 요청 라우팅 구현에 대한 고려 사항

ASP.NET Web API를 사용하여 구현된 서비스에서 각 요청은 _controller_ 클래스의 메서드로 라우팅됩니다. Web API 프레임워크는 라우팅 구현을 위한 두 가지 주요 옵션 즉, _규칙 기반_ 라우팅 및 _특성 기반_ 라우팅을 제공합니다. Web API에서 요청을 라우팅하는 가장 좋은 방법을 결정하는 경우 다음과 같은 사항을 고려합니다.

- **규칙 기반 라우팅의 제한 사항 및 요구 사항을 이해합니다**.

	기본적으로 Web API 프레임워크는 규칙 기반 라우팅을 사용합니다. Web API 프레임워크는 다음 항목을 포함하는 초기 라우팅 테이블을 생성합니다.

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	경로는 일반적이며, _api_와 같은 리터럴과 _{controller}_ 및 _{id}_와 같은 변수로 구성됩니다. 규칙 기반 라우팅은 경로의 일부 요소를 선택하도록 허용합니다. Web API 프레임워크는 요청에 포함된 HTTP 메서드를 API에 포함된 메서드 이름의 처음 부분과 비교한 후 선택적인 매개 변수를 부합시키는 방법으로 컨트롤러에서 호출할 메서드를 결정합니다. 예를 들어, 이름이 _orders_인 컨트롤러에 _GetAllOrders()_ 또는 _GetOrderByInt(int id)_ 메서드가 포함되면 GET 요청은 \_http://www.adventure-works.com/api/orders/_ _GetAlllOrders()_ 메서드로 직접 연결되고 GET 요청 \_http://www.adventure-works.com/api/orders/99_은(는) _GetOrderByInt(int id)_ 메서드로 라우팅됩니다. 컨트롤러에 접두사 Get으로 시작하는 부합되는 메서드가 없는 경우, Web API 프레임워크는 HTTP 405(메서드가 허용되지 않음) 메시지로 응답합니다. 또한 라우팅 테이블에 지정된 매개 변수 이름(id)이 _GetOrderById_ 메서드에 대한 매개 변수의 이름과 반드시 같아야 하며, 그렇지 않으면 Web API 프레임워크는 HTTP 404(찾을 수 없음)으로 응답합니다.

	POST, PUT 및 DELETE HTTP 요청에 동일한 규칙이 적용됩니다. order 101의 세부 정보를 업데이트 하는 PUT 요청이 URI \_http://www.adventure-works.com/api/orders/101_에 전달되고, 메시지 본문은 order에 대한 새로운 세부 정보를 포함하고, 이 정보는 orders 컨트롤러에 포함된 메서드에 _PutOrder_와 같이 이름이 접두사 _Put_으로 시작되는 매개 변수로 전달됩니다.

	기본 라우팅 테이블은 \_http://www.adventure-works.com/api/customers/1/orders_(customer 1에 의해 배치된 모든 orders의 세부 사항을 찾는)와 같이 RESTful Web API의 자식 리소스를 참조하는 요청과 부합하지 않습니다. 이러한 경우를 처리하려면, 라우팅 테이블에 사용자 지정 경로를 추가합니다.

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	이 경로는 URI가 부합하는 요청을 _Customers_ 컨트롤러의 _GetOrdersForCustomer_ 메서드로 전달합니다. 이 메서드는 이름이 _custI_인 단일 매개 변수를 취해야 합니다.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    public IEnumerable<Order> GetOrdersForCustomer(int custId)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	> [AZURE.TIP] 가능한 한 기본 라우팅을 활용합니다. 사용자 지정 경로를 복잡하게 많이 정의하면 불안정해지고(메서드를 컨트롤러에 추가하면 모호한 경로가 발생하기 쉽습니다.) 성능이 감소되기 때문에(라우팅 테이블이 크면 URI에 부합하는 라우팅을 파악하기 위해 Web API 프레임워크에서 처리할 작업이 더 많아집니다.) 이를 피하도록 합니다. API와 경로를 단순하게 유지합니다. 자세한 내용은 API 디자인 지침의 Web API 기반 리소스 구성 섹션을 참조하세요. 사용자 지정 경로를 반드시 정의해야 하는 경우에는 이 섹션의 뒤쪽에 나오는 특성 기반 라우팅을 사용하는 것이 바람직합니다.

	규칙 기반 라우팅에 대한 자세한 내용은 Microsoft 웹 사이트의 [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) 페이지를 참조하세요.

- **라우팅의 모호성을 예방합니다**.

	규칙 기반 라우팅은 컨트롤러의 여러 메서드가 동일한 경로에 부합하는 경우 경로가 모호해질 수 있습니다. 이런 경우, Web API 프레임워크는 “요청에 부합하는 여러 작업이 발견됨” 텍스트를 포함하는 HTTP 500(내부 서버 오류) 응답 메시지를 반환합니다.

- **특성 기반 라우팅을 선호합니다**.

	특성 기반 라우팅은 컨트롤러의 메서드에 경로를 연결하는 대체 수단을 제공합니다. 규칙 기반 라우팅의 패턴 부합 기능에 의존하기 보다는 컨트롤러의 메서드에 명시적으로 주석(연결해야 하는 경로의 세부 정보로)을 추가할 수 있습니다. 이러한 접근 방식은 잠재적인 모호성을 제거하는데 도움이 됩니다. 또한 설계를 하는 동안 명시적인 경로가 정의되기 때문에 프로그램 실행 시 규칙 기반 라우팅보다 효율적입니다. 다음 코드는 Customers 컨트롤러의 메서드에 _Route_ 특성을 적용하는 방법을 보여줍니다. 이 메서드는 _HTTP GET_ 요청에 응답해야 한다는 것을 나타내기 위해 HttpGet 특성을 사용합니다. 이 특성은 규칙 기반 라우팅에 의해 예상되는 스키마 대신, 편리한 이름 지정 스키마를 사용하여 메서드의 이름을 지정할 수 있도록 합니다. 다른 유형의 HTTP 요청에 응답하는 메서드를 정의하기 위하여 메서드에 _HttpPost_, _HttpPut_ 및 _HttpDelete_ 특성으로 주석을 달 수도 있습니다.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	    [Route("api/customers/{id}/orders")]
	    [HttpGet]
	    public IEnumerable<Order> FindOrdersForCustomer(int id)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	특성 기반 라우팅에는 앞으로 코드를 유지해야 하는 개발자를 위해 문서 역할을 한다는 유용한 파생 효과가 있습니다. 어떤 메서드가 어떤 경로에 속하는지가 명확히 준비되어 있으며, _HttpGet_ 특성은 메서드가 응답해야 하는 HTTP 요청의 유형을 명확히 나타냅니다.

	특성 기반 라우팅은 매개 변수를 부합시키는 방법을 제한하는 제약 조건을 정의할 수 있도록 합니다. 제약 조건은 매개 변수 유형을 지정할 수 있고, 경우에 따라 허용되는 매개 변수 값의 범위를 나타낼 수도 있습니다. 다음 예제에서 _FindCustomerByID_ 메서드에 대한 id 매개 변수는 반드시 음수가 아닌 정수여야 합니다. 응용 프로그램에서 음수의 고객 번호로 HTTP GET 요청을 제출하면 Web API 프레임워크는 HTTP 405(메서드가 허용되지 않음) 메시지로 응답합니다.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id:int:min(0)}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	}
	```

	특성 기반 라우팅에 대한 자세한 내용은 Microsoft 웹 사이트의 [Attribute Routing in Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)(Web API 2에서 특성 라우팅) 페이지를 참조하세요.

- **경로에 유니코드 문자를 지원합니다**.

	GET 요청에서 리소스를 식별하는데 사용된 키는 문자열일 수 있습니다. 따라서 글로벌 응용 프로그램에서, 영어가 아닌 문자를 포함하는 URI를 지원해야 할 수 있습니다.

- **라우팅하지 말아야 하는 메서드를 구분합니다**.

	규칙 기반 라우팅을 사용하는 경우 HTTP 동작에 해당하지 않는 메서드를 _NonAction_ 특성으로 데코레이트하여 나타냅니다. 이것은 일반적으로 컨트롤러 내의 다른 메서드에서 사용하기 위해 정의한 도우미 메서드에 적용되며 이 특성은 이러한 메서드가 잘못된 HTTP 요청에 의해 부합되어 호출되는 것을 방지합니다.

- **API를 하위 도메인에 배치하는 것에 대한 장단점을 고려합니다**.

	기본적으로 ASP.NET Web API는 API를 도메인의 _/api_ 디렉터리로 구성합니다(예: \_http://www.adventure-works.com/api/orders_). 이 디렉터리는 동일한 호스트에 의해 노출되는 다른 서비스와 같은 도메인에 존재합니다. URI를 사용하여 개별 호스트에서 실행되는 자체 하위 도메인으로 Web API를 분리하는 것이 유익할 수 있습니다(예: \_http://api.adventure-works.com/orders_). 이렇게 분리하면 _www.adventure-works.com_ 도메인에서 실행되는 다른 웹 응용 프로그램이나 서비스에 영향을 미치지 않고 보다 효과적으로 Web API의 크기와 파티션을 조정할 수 있습니다.

	하지만, Web API를 다른 하위 도메인에 배치하면 보안 문제가 발생할 수 있습니다. 다른 위치에서 실행되는 Web API를 호출하는 _www.adventure-works.com_에 호스팅되는 웹 응용 프로그램이나 서비스는 많은 웹 브라우저의 동일 원본 정책을 위반할 수 있습니다. 이런 경우, 호스트 간에 크로스-원본 자원 공유(CORS)를 사용할 수 있도록 해야 합니다. 자세한 정보는 API 보안 지침 문서를 참조하세요.

## 요청 처리에 대한 고려 사항

클라이언트 응용 프로그램의 요청이 Web API의 메서드에 성공적으로 라우팅되고 나면 해당 요청은 가급적 효율적인 방식으로 처리되어야 합니다. 요청을 처리하기 위해 코드를 구현하는 경우 다음 사항을 고려합니다.

- **GET, PUT, DELETE, HEAD 및 PATCH 작업은 멱등원이어야 합니다**.

	이러한 요청을 구현하는 코드는 파생 효과를 부과하지 말아야 합니다. 동일한 리소스에 대해 동일한 요청이 반복되면 그 결과는 동일한 상태여야 합니다. 예를 들어 동일한 URI에 DELETE 요청을 여러 개 보내면 응답 메시지의 HTTP 상태 코드는 다를 수 있지만(첫 번째 DELETE 요청은 상태 코드 204(내용 없음)를 반환하고 그 다음 DELETE 요청은 상태 코드 404(찾을 수 없음)를 반환) 효과는 동일해야 합니다.

> [AZURE.NOTE] Jonathan Oliver의 블로그에 있는 [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/)(멱등 패턴) 문서에는 멱등에 대한 개요 및 데이터 관리 옵션과의 관계가 제공되어 있습니다.

- **새 리소스를 생성하는 POST 작업은 무관한 파생 효과 없이 수행되어야 합니다**.

	POST 요청이 새 리소스를 생성하기 위한 것이라면, 그 효과는 새 리소스(및 연계가 있는 경우에는 가급적 직접적으로 연관된 리소스)로 한정되어야 합니다. 예를 들어, 전자 상거래 시스템에서 고객을 위해 새 주문을 생성하는 POST 요청은 제고 수준을 변경하고 대금 청구 정보도 생성하지만 주문과 직접적인 관련이 없거나 시스템의 전반적인 상태에 다른 파생 효과를 미치는 정보는 수정하지 말아야 합니다.

- **번잡한 POST, PUT 및 DELETE 작업을 구현하지 않습니다**.

	리소스 컬렉션을 통해 POST, PUT 및 DELETE 요청을 지원합니다. POST 요청은 다수의 새로운 리소스에 대한 세부 정보를 포함할 수 있으며 그 모두를 동일한 컬렉션에 추가할 수 있고, PUT 요청은 컬렉션에 포함된 전체 리소스를 바꿀 수 있고, DELETE 요청은 컬렉션 전체를 제거할 수 있습니다.

	ASP.NET Web API 2에 포함된 OData 지원은 여러 요청을 일괄 처리할 수 있는 기능을 제공합니다. 클라이언트 응용 프로그램은 여러 개의 Web API 요청을 패키지로 만들어서 단일 HTTP 요청으로 서버에 보낼 수 있고, 각 요청에 대한 응답을 포함하는 단일 HTTP 응답을 수신할 수 있습니다. 자세한 내용은 Microsoft 웹 사이트의 [Introducing Batch Support in Web API and Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx)(Web API OData 및 Web API의 배치 지원 소개) 페이지를 참조하세요.

- **클라이언트 응용 프로그램에 응답을 반환할 때 HTTP 프로토콜을 준수합니다**.

	Web API는 클라이언트가 결과를 처리할 방법을 판단할 수 있도록 올바른 HTTP 상태 코드를 포함하고, 클라이언트가 결과의 특성을 이해할 수 있도록 적절한 HTTP 헤더를 포함하고, 클라이언트가 결과를 분석할 수 있도록 적절한 형식으로 구성된 본문을 포함하는 메시지를 반환해야 합니다. ASP.NET Web API 템플릿을 사용하는 경우, HTTP POST 요청에 응답하는 메서드를 구현하는 기본 전략은 다음 예제에 예시된 바와 같이 새로 생성된 리소스의 사본을 반환하는 것입니다.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public Customer CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        // This method returns a customer with a unique ID allocated
	        // by the repository
	        var newCust = repository.Add(customerDetails);
	        // Return the newly added customer
	        return newCust;
	    }
	    ...
	}
	```

	POST 작업에 성공한 경우 Web API 프레임 워크는 상태 코드 200(OK) 및 고객의 세부 정보로 구성된 메시지 본문으로 HTTP 응답을 생성합니다. 하지만, 이런 경우, HTTP 프로토콜에 따라, POST 작업은 상태 코드 201(생성됨)을 반환해야 하고 응답 메시지는 응답 메시지의 Location 헤더에 새로 생성된 리소스의 URI를 포함해야 합니다.

	이 기능을 제공하려면 `IHttpActionResult` 인터페이스를 사용하여 고유한 HTTP 응답 메시지를 반환합니다. 다음 코드 예제와 같이, 이러한 접근 방식을 통해 HTTP 상태 코드, 응답 메시지의 헤더, 응답 메시지 본문에 포함된 데이터의 형식을 잘 제어할 수 있게 됩니다. 이 버전의 `CreateNewCustomer` 메서드는 HTTP 프로토콜을 따르는 클라이언트의 기대 수준을 보다 면밀히 준수합니다. `ApiController` 클래스의 `Created` 메서드는 지정된 데이터로 응답 메시지를 구성하고 그 결과에 Location 헤더를 추가합니다.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public IHttpActionResult CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        var newCust = repository.Add(customerDetails);

	        // Create a value for the Location header to be returned in the response
	        // The URI should be the location of the customer including its ID,
	        // such as http://adventure-works.com/api/customers/99
	        var location = new Uri(...);

            // Return the HTTP 201 response,
            // including the location and the newly added customer
	        return Created(location, newCust);
	    }
	    ...
	}
	```

- **콘텐츠 협상을 지원합니다**.

	응답 메시지의 본문에는 다양한 형식의 데이터가 포함될 수 있습니다. 예를 들어 HTTP GET 요청은 JSON 또는 XML 형식으로 데이터를 반환할 수 있습니다. 클라이언트가 요청을 제출할 때, 클라이언트가 처리할 수 있는 데이터 형식을 지정하는 Accept 헤더를 요청에 포함시킬 수 있습니다. 이러한 형식은 미디어 형식으로 지정됩니다. 예를 들어, 이미지를 검색하는 GET 요청을 생성하는 클라이언트는 클라이언트가 처리할 수 있는 미디어 유형을 나열하는(예: "image/jpeg, image/gif, image/png") Accept 헤더를 지정할 수 있습니다. Web API에서 결과를 반환할 때 이러한 미디어 유형 중 하나를 데이터 형식으로 사용해야 하며, 응답의 Content-Type 헤더에 해당 형식을 명시해야 합니다.

	클라이언트에서 Accept 헤더를 명시하지 않는 경우, 응답 본문에 대해 합당한 기본 형식을 사용합니다. 한 예로, ASP.NET Web API 프레임워크는 텍스트 기반 데이터에 대해 JSON을 기본 형식으로 사용합니다.

	> [AZURE.NOTE] ASP.NET Web API 프레임워크는 Accept 헤더의 일부 자동 감지를 수행하고 응답 메시지의 본문에 포함된 데이터 형식을 기반으로 헤더를 처리합니다. 예를 들어, 응답 메시지 본문에 CLR(공용 언어 런타임) 개체가 포함되어 있는 경우, ASP.NET Web API는 클라이언트가 결과를 XML로 요구한다는 표시가 없으면 자동으로 응답의 Content-Type 헤더를 "application/json"으로 설정하고 JSON 형식으로 응답을 생성합니다. 결과를 XML로 요구한다는 표시가 있으면 ASP.NET Web API 프레임워크는 응답을 XML 형식으로 생성하고 응답의 Content-Type 헤더를 "text/xml"로 설정합니다. 하지만 작업에 대한 구현 코드에 다른 미디어 형식을 명시적으로 나타내는 Accept 헤더를 처리하는 것이 필요할 수 있습니다.

- **HATEOAS 스타일 탐색 및 리소스 발견을 지원하는 링크를 제공합니다**.

	API 디자인 지침은 HATEOAS 접근 방식을 통해 클라이언트가 초기 시작 지점으로부터 리소스를 발견하고 탐색할 수 있도록 하는 방법을 설명합니다. 이것은 URI를 포함하는 링크를 사용하여 이루어 집니다. 클라이언트가 리소스를 확보하기 위해 HTTP GET 요청을 발행하는 경우, 직접적으로 연관된 리소스의 위치를 클라이언트 응용 프로그램이 신속하게 찾을 수 있도록 하는 URI가 응답에 포함되어야 합니다. 예를 들어, 전자 상거래 솔루션을 지원하는 Web API에서 고객이 다수의 주문을 할 수 있습니다. 클라이언트 응용 프로그램에서 고객의 세부 정보를 검색하는 경우, 클라이언트 응용 프로그램이 주문을 찾을 수 있는 HTTP GET 요청을 보낼 수 있도록 하는 링크가 응답에 포함되어야 합니다. 또한 HATEOAS 스타일 링크는 각 요청을 수행하도록 해당 URI와 함께 연결된 리소스가 함께 지원하는 다른 작업(POST, PUT, DELETE 등)을 설명해야 합니다. 이 방법은 API 디자인 지침 문서에 보다 자세히 설명되어 있습니다.

	현재 HATEOAS 구현을 제어하는 표준은 없으며 다음 예제에서 가능한 접근 방식을 볼 수 있습니다. 이 예제에서 고객에 대한 세부 정보를 찾는 HTTP GET 요청은 해당 고객의 주문을 참조하는 HATEOAS 링크를 포함하는 응답을 반환합니다.

	```HTTP
	GET http://adventure-works.com/customers/2 HTTP/1.1
	Accept: text/json
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Content-Type: application/json; charset=utf-8
	...
	Content-Length: ...
	{"CustomerID":2,"CustomerName":"Bert","Links":[
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"PUT",
	   "types":["application/x-www-form-urlencoded"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"DELETE",
	   "types":[]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"POST",
	   "types":["application/x-www-form-urlencoded"]}
	]}
	```

	이 예제에서 고객 데이터는 다음 코드 조각에 표시된 `Customer` 클래스로 나타납니다. HATEOAS 링크는 `Links` 컬렉션 속성에 보관됩니다.

	```C#
	public class Customer
	{
    	public int CustomerID { get; set; }
    	public string CustomerName { get; set; }
    	public List<Link> Links { get; set; }
    	...
	}

	public class Link
	{
    	public string Rel { get; set; }
    	public string Href { get; set; }
    	public string Action { get; set; }
    	public string [] Types { get; set; }
	}
	```

	HTTP GET 작업은 저장소에서 고객 데이터를 가져오고 `Customer` 개체를 구성한 후 `Links` 컬렉션을 채웁니다. 결과는 JSON 응답 메시지 형식으로 생성됩니다. 각 링크는 다음 필드를 구성합니다.

	- 반환되는 개체와 링크로 설명되는 개체 사이의 관계. 이 경우 "self"는 링크가 개체 스스로를 참조한다는 것을 나타내며(다수의 개체 지향 언어에서 `this` 포인터와 유사한) "orders"는 관련된 주문 정보를 포함하는 컬렉션의 이름입니다.

	- URI 형태의 링크로 설명되는 개체에 대한 하이퍼링크(`Href`).

	- 이 URI에 전송될 수 있는 HTTP 요청 유형(`Action`).

	- HTTP 요청에 제공되어야 하는 데이터 형식 또는 요청 유형에 따라 응답으로 반환될 수 있는 데이터의 형식(`Types`).

	HTTP 응답 예제에 있는 HATEOAS 링크는 클라이언트 응용 프로그램이 다음 작업을 수행할 수 있다는 것을 나타냅니다.

	- URI \_http://adventure-works.com/customers/2_에 대한 HTTP GET 요청: 고객 세부 정보를 (다시) 가져오기 위한 요청입니다. 데이터는 XML 또는 JSON으로 반환될 수 있습니다.

	- URI \_http://adventure-works.com/customers/2_에 대한 HTTP PUT 요청: 고객의 세부 정보를 수정하기 위한 요청입니다. 요청 메시지에 x-www-form-urlencoded 형식의 새로운 데이터가 제공되어야 합니다.

	- URI \_http://adventure-works.com/customers/2_에 대한 HTTP DELETE 요청: 고객을 삭제하기 위한 요청입니다. 이 요청은 추가적인 정보를 요구하지 않거나 응답 메시지 본문에 데이터를 반환합니다.

	- URI \_http://adventure-works.com/customers/2/orders_에 대한 HTTP GET 요청: 고객의 모든 주문을 찾기 위한 요청입니다. 데이터는 XML 또는 JSON으로 반환될 수 있습니다.

	- URI \_http://adventure-works.com/customers/2/orders_에 대한 HTTP PUT 요청: 고객의 새 주문을 생성하기 위한 요청입니다. 요청 메시지에 x-www-form-urlencoded 형식의 데이터가 제공되어야 합니다.

## 예외 처리에 대한 고려 사항
기본적으로, ASP.NET Web API 프레임워크에서, 작업이 catch할 수 없는 예외를 throw하면 프레임워크는 HTTP 상태 코드 500(내부 서버 오류)으로 응답 메시지를 반환합니다. 대부분의 경우 이렇게 극도로 단순한 접근 방식은 구분하는데 유용하지 않으며 예외의 원인을 판단하기 어렵게 만듭니다. 따라서 다음 사항을 고려하고, 예외를 처리하기 위하여 보다 포괄적인 접근 방식을 채택해야 합니다.

- **예외 사항을 확인하고 클라이언트에 의미 있는 응답을 반환합니다**.

	HTTP 작업을 구현하는 코드는 catch할 수 없는 예외가 Web API 프레임워크에 퍼지도록 하기 보다는 포괄적인 예외 처리를 제공해야 합니다. 예외로 인해 작업을 성공적으로 완료하는 것이 불가능한 경우에는 예외 사항이 응답 메시지에 전달될 수 있습니다. 하지만 예외를 유발한 오류에 대하여 의미 있는 설명을 포함해야 합니다. 예외는 모든 상황에 대해 단순히 상태 코드 500만 반환하기 보다는 적절한 HTTP 상태 코드도 포함해야 합니다. 예를 들어, 사용자 요청으로 인해 제약 조건에 위배되는 데이터베이스 업데이트(예: 주문량이 탁월한 고객을 삭제하려는 시도)가 발생한 경우 상태 코드 409(충돌)와 충돌 이유를 나타내는 메시지 본문을 반환해야 합니다. 다른 조건이 달성할 수 없는 요청을 렌더링하는 경우에는 상태 코드 400(잘못된 요청)을 반환할 수 있습니다. HTTP 상태 코드의 전체 목록은 W3C 웹 사이트의 [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(상태 코드 정의) 페이지에서 찾을 수 있습니다.

	다음 코드는 다른 조건을 트래핑하고 적절한 응답을 반환하는 예제입니다.

	```C#
	[HttpDelete]
	[Route("customers/{id:int}")]
	public IHttpActionResult DeleteCustomer(int id)
	{
		try
		{
			// Find the customer to be deleted in the repository
			var customerToDelete = repository.GetCustomer(id);

			// If there is no such customer, return an error response
			// with status code 404 (Not Found)
			if (customerToDelete == null)
			{
					return NotFound();
			}

			// Remove the customer from the repository
			// The DeleteCustomer method returns true if the customer
			// was successfully deleted
			if (repository.DeleteCustomer(id))
			{
				// Return a response message with status code 204 (No Content)
				// To indicate that the operation was successful
				return StatusCode(HttpStatusCode.NoContent);
			}
			else
			{
				// Otherwise return a 400 (Bad Request) error response
				return BadRequest(Strings.CustomerNotDeleted);
			}
		}
		catch
		{
			// If an uncaught exception occurs, return an error response
			// with status code 500 (Internal Server Error)
			return InternalServerError();
		}
	}
	```

	> [AZURE.TIP] 공격자가 Web API에 침투를 시도하는데 유용할 수 있는 정보를 포함시키지 마십시오. 자세한 정보를 보려면 Microsoft 웹 사이트의 [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) 페이지를 방문하십시오.

	> [AZURE.NOTE] 많은 웹 서버에서 Web API에 도달하기 전에 오류 조건을 자체 트래핑합니다. 예를 들어, 웹 사이트에 대한 인증을 구성했는데 사용자가 올바른 인증 정보를 제공하지 못하면 웹 서버는 상태 코드 401(권한 없음)로 응답해야 합니다. 클라이언트가 인증된 후에는 이 클라이언트가 요청한 리소스에 액세스할 수 있는지를 검증하기 위하여 코드로 자체 확인을 수행할 수 있습니다. 권한 부여가 실패하면 상태 코드 403(사용 권한 없음)을 반환해야 합니다.

- **일관된 방식으로 예외를 처리하고 오류에 대한 정보를 기록합니다**.

	일관된 방식으로 예외를 처리하려면 Web API에 대해 전체적인 오류 처리 전략을 구현하는 것이 좋습니다. 컨트롤러에서 `HttpResponseException` 예외가 아닌 처리할 수 없는 예외를 throw할 때마다 실행되는 예외 필터를 생성하면 이 전략의 일부를 달성할 수 있습니다. 이 방법은 Microsoft 웹 사이트의 [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) 페이지에 설명되어 있습니다.

	하지만 예외 필터에서 예외를 catch할 수 없는 다음과 같은 몇 가지 상황이 있습니다.

	- 컨트롤러 생성자에서 throw된 예외

	- 메시지 처리기에서 throw된 예외

	- 라우팅 중에 throw된 예외

	- 응답 메시지의 콘텐츠를 직렬화하는 동안 throw된 예외

	이런 경우를 처리하기 위해 심화된 사용자 지정 방식을 구현할 필요가 있습니다. 각 예외의 전체적인 세부 사항을 모두 파악하도록 오류 로깅을 통합해야 합니다. 이 오류 로그는 웹을 통해 클라이언트에 액세스할 수 있도록 만들지 않는 한 자세한 정보를 포함할 수 있습니다. Microsoft 웹 사이트의 [Web API Global Error Handling](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) 문서에 이 작업을 수행하는 한 가지 방법이 있습니다.

- **클라이언트쪽 오류와 서버쪽 오류를 구별합니다**.

	HTTP 프로토콜은 클라이언트 응용 프로그램으로 인해 발생하는 오류(HTTP 4xx 상태 코드)와 서버의 문제 때문에 발생한 오류(HTTP 5xx 상태 코드)를 구분합니다. 모든 오류 응답 메시지에 대하여 이 규칙을 반드시 따라야 합니다.

<a name="considerations-for-optimizing"></a>
## 클라이언트쪽 데이터 액세스 최적화를 위한 고려 사항

웹 서버와 클라이언트 응용 프로그램을 포함하는 분산된 환경에서 주요 관심사 중 하나는 네트워크입니다. 이것은 상당한 병목 지점이 될 수 있으며, 클라이언트 응용 프로그램이 빈번하게 요청을 보내거나 데이터를 받는 경우에는 특히 그렇습니다. 따라서 네트워크를 통해 이동하는 트래픽 양을 최소화시킨다는 목표를 가져야 합니다. 데이터를 가져오고 유지하기 위하여 코드를 구현하는 경우 다음 사항을 고려합니다.

- **클라이언트쪽 캐싱을 지원합니다**.

	HTTP 1.1 프로토콜은 클라이언트 및 Cache-Control 헤더를 사용하여 요청을 라우팅하는 중간 서버에서 캐싱을 지원합니다. 클라이언트 응용 프로그램이 Web API에 HTTP GET 요청을 보내면 응답은 본문에 포함된 데이터가 클라이언트 또는 중간 서버(요청이 라우팅되는)에 의해 안전하게 캐싱될 수 있는지와 얼마 만에 만료되는지, 오래된 것으로 간주되는 지를 나타내는 Cache-Control 헤더를 포함할 수 있습니다. 다음 예제는 HTTP GET 요청 및 Cache-Control 헤더를 포함하는 해당 응답입니다.

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	이 예제에서 Cache-Control 헤더는 반환된 데이터가 600초 후에 만료되어야 하고, 단일 클라이언트에만 적합하며, 다른 클라이언트가 사용하는 공유 캐시에 저장되지 말아야 한다고 명시합니다 (_private_입니다.) Cache-Control 헤더는 공유 캐시에 데이터를 저장할 수 있는 경우 _private_이 아닌 _public_을 지정합니다. 클라이언트에서 데이터를 캐싱하지 **말아야** 하는 경우 _no-store_로 지정합니다. 다음 코드 예제는 응답 메시지에서 Cache-Control 헤더를 구성하는 방법입니다.

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...
    		// Create a Cache-Control header for the response
    		var cacheControlHeader = new CacheControlHeaderValue();
    		cacheControlHeader.Private = true;
    		cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    		...

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			CacheControlHeader = cacheControlHeader
    		};
    		return response;
    	}
    	...
	}
	```

	이 코드는 이름이 `OkResultWithCaching`인 사용자 지정 `IHttpActionResult` 클래스를 활용합니다. 이 클래스는 컨트롤러가 캐시 헤더 콘텐츠를 설정할 수 있도록 합니다.

	```C#
	public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }

        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }

        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }

        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);

            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;

            return response;
        }
    }
	```

	> [AZURE.NOTE] HTTP 프로토콜은 Cache-Control 헤더에 대해 _no-cache_ 지시문을 정의합니다. 다소 혼란스럽지만, 이 지시문은 "캐시하지 말라"는 의미가 아니고 "캐시된 정보를 반환하기 전에 서버에서 재검증하라"는 의미입니다. 데이터를 캐싱할 수 있지만 최신 상태인지 확인하기 위하여 사용될 때마다 확인합니다.

	캐시 관리는 클라이언트 응용 프로그램 또는 중간 서버의 책임입니다. 하지만 제대로 구현되면 대역폭을 절약할 수 있고 이미 최근에 검색한 데이터를 가져올 필요가 없기 때문에 성능을 향상시킬 수 있습니다.

	Cache-Control 헤더의 _max-age_ 값은 가이드일 뿐이며 지정된 시간 동안 해당 데이터가 변경되지 않는다고 보장하는 것은 아닙니다. Web API는 예상되는 데이터 변동성에 따라서 max-age를 적당한 값으로 설정해야 합니다. 이 기간이 만료되면 클라이언트는 해당 개체를 캐시에서 삭제해야 합니다.

	> [AZURE.NOTE] 설명한 대로, 대부분의 최신 웹 브라우저는 적절한 cache-control 헤더를 요청에 추가하고 결과의 헤더를 검토하는 방식으로 클라이언트쪽 캐싱을 지원합니다. 하지만 일부 오래된 브라우저는 쿼리 문자열을 포함하는 URL로부터 반환되는 값을 캐시하지 않습니다. 보통 이것은 여기에서 논의한 프로토콜을 기반으로 자체적인 캐시 관리 전략을 구현하는 사용자 지정 클라이언트 응용 프로그램에서는 문제가 되지 않습니다.
	>
	> 일부 오래된 프록시는 동일한 행태를 보이며 URL을 기반으로 하는 요청을 쿼리 문자열로 캐시하지 않습니다. 이것은 그러한 프록시를 통해 웹 서버에 연결하는 사용자 지정 클라이언트 응용 프로그램에서 문제가 될 수 있습니다.

- **쿼리 처리를 최적화하기 위해 ETag를 제공합니다**.

	클라이언트 응용 프로그램이 개체를 검색하는 경우, 응답 메시지에 _ETag_(엔터티 태그)를 포함할 수 있습니다. ETag는 리소스의 버전을 나타내는 불투명 문자열입니다. 리소스가 변경될 때마다 Etag도 수정됩니다. ETag는 클라이언트 응용 프로그램에 의해 데이터의 일부로 캐시되어야 합니다. 다음 코드 예제는 HTTP GET 요청에 대한 응답의 일부로 ETag를 추가하는 방법입니다. 이 코드는 개체를 식별하는 숫자 값을 생성하기 위해 개체의 `GetHashCode` 메서드를 사용합니다. (필요한 경우 이 메서드를 무효화하고 MD5와 같은 알고리즘을 사용하여 자체 해시를 생성할 수 있습니다.)

	```C#
	public class OrdersController : ApiController
	{
    	...
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...

    		var hashedOrder = order.GetHashCode();
    		string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    		var eTag = new EntityTagHeaderValue(hashedOrderEtag);

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			...,
    			ETag = eTag
    		};
    		return response;
    	}
    	...
    }
	```

	Web API에서 게시한 응답 메시지는 다음과 같습니다.

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	> [AZURE.TIP] 보안을 위해 민감한 데이터 또는 인증된(HTTPS) 연결을 통해 반환되는 데이터가 캐시되도록 허용하지 마십시오.

	클라이언트 응용 프로그램은 언제든 동일한 리소스를 검색하기 위하여 후속으로 GET 요청을 발행할 수 있습니다. 만약 리소스가 변경되면(ETag가 다르며) 캐시된 버전은 삭제되고 새 버전이 캐시에 추가됩니다. 리소스가 크고 클라이언트로 전송하기 위해 상당한 양의 대역폭이 필요한 경우, 동일한 데이터를 가져오기 위한 반복 요청은 비능률적일 수 있습니다. 이 문제를 해결하기 위해, HTTP 프로토콜은 Web API에서 지원해야 하는 GET 요청을 최적화하기 위해 다음 프로세스를 정의합니다.

	- 클라이언트는 If-None-Match HTTP 헤더에서 참조하는 리소스의 현재 캐시 버전에 대한 ETag를 포함하는 GET 요청을 구성합니다.

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- Web API의 GET 작업은 요청한 데이터(위 예제의 order 2)에 대한 현재 ETag를 확보하고 If-None-Match 헤더의 값과 비교합니다.

	- 요청한 데이터의 현재 ETag가 요청에서 제공한 ETag와 부합하는 경우, 리소스는 변경되지 않은 것이며 Web API는 빈 메시지 본문과 상태 코드 304(수정되지 않음)로 HTTP 응답을 반환해야 합니다.

	- 요청한 데이터의 현재 ETag가 요청에서 제공한 ETag와 부합하지 않으면, 데이터는 변경된 것이며 Web API는 메시지 본문에 새로운 데이터를 넣고 상태 코드 200(OK)으로 HTTP 응답을 반환해야 합니다.

	- 요청한 데이터가 더 이상 존재하지 않으면 Web API는 상태 코드 404(찾을 수 없음)로 HTTP 응답을 반환해야 합니다.

	- 클라이언트는 캐시를 유지하기 위해 상태 코드를 사용합니다. 데이터가 변경되지 않은 경우(상태 코드 304)에 개체는 캐시된 상태로 남고 클라이언트 응용 프로그램은 이 버전의 개체를 계속 사용해야 합니다. 데이터가 변경된 경우(상태 코드 200)에는 캐시된 개체를 삭제하고 새 개체를 삽입해야 합니다. 데이터를 더 이상 사용할 수 없는 경우(상태 코드 404)에는 개체를 캐시에서 제거해야 합니다.

	> [AZURE.NOTE] 응답 헤더에 Cache-Control 헤더 no-store가 포함되면 HTTP 상태 코드에 상관없이 캐시에서 개체를 항상 제거해야 합니다.

	아래 코드는 If-None-Match 헤더를 지원하기 위해 확장된 `FindOrderByID` 메서드입니다. If-None-Match 헤더가 생략되면 지정된 order를 가져옵니다.

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderById(int id)
        {
            try
            {
                // Find the matching order
    		    Order order = ...;

                // If there is no such order then return NotFound
                if (order == null)
                {
                    return NotFound();
                }

                // Generate the ETag for the order
                var hashedOrder = order.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Create the Cache-Control and ETag headers for the response
                IHttpActionResult response = null;
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Public = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                // Retrieve the If-None-Match header from the request (if it exists)
                var nonMatchEtags = Request.Headers.IfNoneMatch;

                // If there is an ETag in the If-None-Match header and
                // this ETag matches that of the order just retrieved,
                // then create a Not Modified response message
                if (nonMatchEtags.Count > 0 &&
                    String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
                {
                    response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NotModified,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }
                // Otherwise create a response message that contains the order details
                else
                {
                    response = new OkResultWithCaching<Order>(order, this)
                    {
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }

                return response;
            }
            catch
            {
                return InternalServerError();
            }
        }
    ...
    }
	```

	이 예제는 이름이 `EmptyResultWithCaching`인 사용자 지정 `IHttpActionResult` 클래스를 통합합니다. 이 클래스는 응답 본문을 포함하지 않는 `HttpResponseMessage` 개체의 래퍼 역할을 합니다.

	```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
        public HttpStatusCode StatusCode { get; set; }
		public Uri Location { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = new HttpResponseMessage(StatusCode);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = this.ETag;
            response.Headers.Location = this.Location;
            return response;
        }
    }
	```

	> [AZURE.TIP] 이 예제에서 데이터에 대한 ETag는 기본 데이터 원본에서 가져온 데이터를 해시하여 생성됩니다. ETag를 다른 방법으로 계산할 수 있으면, 프로세스를 더 많이 최적화시킬 수 있고 데이터가 변경되면 데이터 원본으로부터 데이터만 가져오면 됩니다. 이 방법은 데이터가 크거나 데이터 원본에 액세스할 때 상당한 대기 시간이 발생하는 경우(예: 데이터 원본이 원격 데이터베이스인 경우)에 특히 유용합니다.

- **낙관적 동시성을 지원하기 위해 ETag를 사용합니다.**.

	이전에 캐시한 데이터를 업데이트 할 수 있도록, HTTP 프로토콜은 낙관적 동시성 전략을 지원합니다. 리소스를 가져오고 캐시한 후에 클라이언트 응용 프로그램이 리소스를 변경하거나 제거하기 위해 PUT 또는 DELETE 요청을 계속해서 보내면 ETag를 참조하는 If-Match 헤더를 포함시켜야 합니다. 그러면 Web API는 이 정보를 사용하여 리소스를 가져온 후에 다른 사용자가 리소스를 변경했는지 여부를 판단하고, 클라이언트 응용 프로그램에 다음과 같이 적절한 응답을 보낼 수 있습니다.

	- 클라이언트는 리소스에 대해 새로운 세부 정보를 포함하는 PUT 요청과 If-Match HTTP 헤더에 참조되는 리소스의 현재 캐시 버전에 대한 ETag를 구성합니다. 다음 예제는 order를 업데이트하는 PUT 요청입니다.

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	productID=3&quantity=5&orderValue=250
	```

	- Web API의 PUT 작업은 요청된 데이터(위 예제의 order 1)에 대한 현재 ETag를 확보하여 If-Match 헤더에 포함된 값과 비교합니다.

	- 요청된 데이터의 현재 ETag가 요청에 의해 제공된 ETag와 부합하는 경우, 리소스는 변경되지 않았고 Web API는 업데이트를 수행해야 하며, 성공적으로 수행한 후에는 HTTP 상태 코드 204(내용 없음)를 포함하는 메시지를 반환해야 합니다. 응답은 업데이트된 리소스 버전에 대한 Cache-Control 및 ETag 헤더를 포함할 수 있습니다. 응답은 새롭게 업데이트된 리소스의 URI를 참조하는 Location 헤더를 항상 포함해야 합니다.

	- 요청한 데이터의 현재 ETag가 요청에서 제공한 ETag와 부합하지 않으면, 데이터를 가져온 후 다른 사용자가 데이터를 변경한 것이며 Web API는 빈 메시지 본문과 상태 코드 412(전제 조건 실패)로 HTTP 응답을 반환해야 합니다.

	- 업데이트할 데이터가 더 이상 존재하지 않으면 Web API는 상태 코드 404(찾을 수 없음)와 함께 HTTP 응답을 반환해야 합니다.

	- 클라이언트는 캐시를 유지하기 위해 상태 코드와 응답 헤더를 사용합니다. 데이터가 업데이트된(상태 코드 204) 후에는 개체가 캐시된 상태로 남을 수 있지만(Cache-Control 헤더가 no-store를 명시하지 않는 한) ETag는 업데이트되어야 합니다. 데이터가 변경되거나(상태 코드 412) 찾을 수 없는(상태 코드 404) 다른 사용자에 의해 변경된 경우, 캐시된 개체는 삭제되어야 합니다.

	다음 코드 예제는 Orders 컨트롤러에 대한 PUT 작업의 구현입니다.

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[HttpPut]
    	[Route("api/orders/{id:int}")]
    	        public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
        {
            try
            {
                var baseUri = Constants.GetUriFromConfig();
                var orderToUpdate = this.ordersRepository.GetOrder(id);
                if (orderToUpdate == null)
                {
                    return NotFound();
                }

                var hashedOrder = orderToUpdate.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Retrieve the If-Match header from the request (if it exists)
                var matchEtags = Request.Headers.IfMatch;

                // If there is an Etag in the If-Match header and
                // this etag matches that of the order just retrieved,
                // or if there is no etag, then update the Order
                if (((matchEtags.Count > 0 &&
                     String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                     matchEtags.Count == 0)
                {
                    // Modify the order
                    orderToUpdate.OrderValue = order.OrderValue;
                    orderToUpdate.ProductID = order.ProductID;
                    orderToUpdate.Quantity = order.Quantity;

                    // Save the order back to the data store
                    // ...

                    // Create the No Content response with Cache-Control, ETag, and Location headers
                    var cacheControlHeader = new CacheControlHeaderValue();
                    cacheControlHeader.Private = true;
                    cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                    hashedOrder = order.GetHashCode();
                    hashedOrderEtag = String.Format(""{0}"", hashedOrder);
                    var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                    var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                    var response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NoContent,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag,
                        Location = location
                    };

                    return response;
                }

                // Otherwise return a Precondition Failed response
                return StatusCode(HttpStatusCode.PreconditionFailed);
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
	```

	> [AZURE.TIP] If-Match 헤더 사용이 완전히 선택적이며 생략된 경우에, Web API는 지정된 order를 항상 업데이트하려고 시도할 것이고 다른 사용자에 의해 수행된 업데이트를 무작정 덮어쓸 수도 있습니다. 업데이트 손실로 인한 문제를 피하려면 If-Match 헤더를 항상 제공합니다.

<a name="considerations-for-handling-large"></a>
## 큰 요청 및 응답 처리를 위한 고려 사항

클라이언트 응용 프로그램에서 크기가 수 MB(또는 그 이상)인 데이터를 보내거나 받는 요청을 발급해야 하는 경우가 있습니다. 이 정도 크기의 데이터가 전송되는 동안 대기하다가 클라이언트 응용 프로그램이 응답하지 않는 상태가 될 수 있습니다. 상당한 양의 데이터를 포함하는 요청을 처리해야 하는 경우 다음 사항을 고려합니다.

- **큰 개체를 포함하는 요청 및 응답을 최적화합니다**.

	일부 리소스는 큰 개체이거나 그래픽 이미지 또는 다른 형식의 이진 데이터와 같이 큰 필드를 포함할 수 있습니다. Web API는 이러한 리소스의 업로딩과 다운로딩을 최적화할 수 있도록 스트리밍을 지원해야 합니다.

	HTTP 프로토콜은 큰 데이터 개체를 클라이언트로 다시 스트리밍하기 위하여 청크된 전송 인코딩 메커니즘을 제공합니다. 클라이언트가 큰 개체에 대한 HTTP GET 요청을 보내면, Web API는 HTTP 연결을 통해 증분 _청크_로 회신을 보낼 수 있습니다. 회신에 포함된 데이터의 길이를 처음에는 모를 수 있기 때문에(생성될 수 있습니다) Web API를 호스팅하는 서버는 Content-Length 헤더가 아닌 Transfer-Encoding: Chunked 헤더를 지정하는 각각의 청크를 포함하는 응답 메시지를 보내야 합니다. 클라이언트 응용 프로그램은 각각의 청크를 차례로 받아서 완전한 응답을 빌드할 수 있습니다. 데이터 전송은 서버에서 크기가 0인 마지막 청크를 보내면 완료됩니다. ASP.NET Web API에서 `PushStreamContent` 클래스를 사용하여 청크를 구현할 수 있습니다.

	다음 예제는 제품 이미지에 대한 HTTP GET 요청에 응답하는 작업입니다.

	```C#
	public class ProductImagesController : ApiController
	{
    	...
    	[HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);

                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	이 예제에서 `ConnectBlobToContainer`는 Azure Blob 저장소에 명시된 컨테이너(이름이 표시되지 않음)에 연결하는 도우미 메서드입니다. `BlobExists`는 명시된 이름의 Blob이 Blob 저장소 컨테이너에 존재하는지를 알리는 부울 값을 반환하는 또 다른 도우미 메서드입니다.

	각 제품에는 Blob 저장소에 보관되는 각각의 이미지가 있습니다. `FileDownloadResult` 클래스는 `PushStreamContent` 개체를 사용하여 적절한 Blob으로부터 이미지 데이터를 읽어서 응답 메시지의 콘텐츠로 비동기 전송하는 사용자 지정 `IHttpActionResult` 클래스입니다.

	```C#
	public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });

            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
	```

	클라이언트가 큰 개체를 포함하는 새 리소스를 POST해야 하는 경우에는 업로드 작업에 스트리밍을 적용할 수 있습니다. 다음 예제는 `ProductImages` 컨트롤러에 대한 Post 메서드입니다. 이 메서드는 클라이언트가 새 제품 이미지를 업로드할 수 있도록 합니다.

	```C#
	public class ProductImagesController : ApiController
	{
    	...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	이 코드는 `FileUploadResult`라는 또 다른 사용자 지정 `IHttpActionResult` 클래스를 사용합니다. 이 클래스는 데이터를 비동기식으로 업로드하는 논리를 포함합니다.

	```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
	```

	> [AZURE.TIP] 웹 서비스에 업로드할 수 있는 데이터의 양은 스트리밍의 제약을 받지 않으며 단일 요청이 상당한 리소스를 소비하는 대량 개체가 될 수 있습니다. 스트리밍 프로세스 중에 Web API가 요청에 포함된 데이터의 양이 허용할 수 있는 한도를 초과했다고 판단하면 작업을 중단하고 상태 코드 413(요청 엔터티 너무 큼)과 함께 응답 메시지를 반환할 수 있습니다.

	HTTP 압축을 사용하여 네트워크를 통해 전송되는 큰 개체의 크기를 최소화할 수 있습니다. 이 방법은 Web API를 호스팅하는 서버와 클라이언트에서 추가적인 프로세스를 필요로 하지만 네트워크 트래픽의 양 및 그와 연관된 네트워크 대기 시간을 줄이는데 도움이 됩니다. 예를 들어, 압축 데이터를 수신할 것으로 예상되는 클라이언트 응용 프로그램은 Accept-Encoding: gzip(다른 압축 알고리즘도 지정될 수 있습니다.) 요청 헤더를 포함할 수 있습니다. 압축을 지원하는 서버는 Content-Encoding: gzip 응답 헤더와 gzip 형식으로 포함된 콘텐츠를 메시지 본문에 넣어 응답해야 합니다.

	> [AZURE.TIP] 인코딩된 압축을 스트리밍과 결합할 수 있습니다. 데이터를 스트리밍하기 전에 우선 압축하고 메시지 헤더에 gzip 콘텐츠 인코딩과 청크된 전송 인코딩을 명시합니다. 일부 웹 서버(예: Internet Information Server)는 Web API에서의 데이터 압축 여부와 상관 없이 HTTP 응답을 자동으로 압축하도록 구성될 수 있습니다.

- **비동기 작업을 지원하지 않는 클라이언트에 대해 부분 응답을 구현합니다**.

	비동기 스트리밍에 대한 대안으로 클라이언트 응용 프로그램은 큰 개체의 데이터를 명시적으로 청크로 요청할 수 있으며 이를 부분 응답이라고도 합니다. 클라이언트 응용 프로그램은 개체에 대한 정보를 입수하기 위해 HTTP HEAD 요청을 보냅니다. Web API가 부분 응답을 지원하면 HEAD 요청에 대해 Accept-Ranges 헤더 및 개체의 총 크기를 나타내는 Content-Length 헤더를 포함하지만 메시지 본문은 빈 상태의 응답 메시지로 응답해야 합니다. 클라이언트 응용 프로그램은 이 정보를 사용하여 수신할 바이트의 범위를 지정하는 일련의 GET 요청을 구성할 수 있습니다. Web API는 HTTP 상태 코드 206(부분 콘텐츠), 응답 메시지의 본문에 포함된 실제 데이터 양을 나타내는 Content-Length 헤더, 데이터가 개체의 어느 부분(예: 4000~ 8000바이트)에 해당하는지를 나타내는 Content-Range 헤더를 포함하는 응답 메시지를 반환해야 합니다.

	HTTP HEAD 요청 및 부분 응답은 API 디자인 지침에 보다 자세히 설명되어 있습니다.

- **클라이언트 응용 프로그램에서 불필요한 계속 상태 메시지 전송을 자제합니다**.

	서버에 대량의 데이터를 보내려고 하는 클라이언트 응용 프로그램은 우선 서버에서 실제로 요청을 수신하려고 하는지를 판단합니다. 데이터를 보내기 전에 클라이언트 응용 프로그램은 Expect: 100-Continue 헤더와 데이터의 크기를 나타내는 Content-Length 헤더를 포함시키고 메시지 본문은 빈 상태로 HTTP 요청을 제출할 수 있습니다. 서버가 요청을 처리하려고 하는 경우, HTTP 상태 100(계속)을 나타내는 메시지로 응답합니다. 그러면 클라이언트 응용 프로그램은 메시지 본문에 데이터를 포함하는 완전한 요청을 보냅니다.

	IIS를 사용하여 서비스를 호스팅하는 경우, 웹 응용 프로그램에 요청을 전달하기 전에 HTTP.sys 드라이버가 Expect: 100-Continue 헤더를 자동으로 감지하여 처리합니다. 따라서 응용 프로그램 코드에서 이러한 헤더를 볼 가능성이 없으며, IIS가 적절하지 않거나 너무 큰 것으로 간주되는 모든 메시지를 이미 필터링 한 것으로 추정할 수 있습니다.

	.NET Framework를 사용하여 클라이언트 응용 프로그램을 빌드하는 경우에는 모든 POST 및 PUT 메시지가 기본적으로 Expect: 100-Continue 헤더를 포함하는 메시지를 우선 보냅니다. 서버쪽의 경우 .NET Framework에 의해 프로세스가 투명하게 처리됩니다. 하지만 이 프로세스는 POST 및 PUT 요청에 대해(작은 요청에 대해서도) 서버와의 2회 왕복을 유발합니다. 응용 프로그램이 대량의 데이터를 포함하는 요청을 보내지 않는 경우, `ServicePointManager` 클래스를 사용하여 클라이언트 응용 프로그램에서 `ServicePoint` 개체를 생성하도록 하여 이 기능을 비활성화시킬 수 있습니다. `ServicePoint` 개체는 서버의 리소스를 식별하는 URI의 호스트 조각과 스키마를 기반으로 클라이언트가 서버에 만드는 연결을 처리합니다. 그 후 `ServicePoint` 개체의 `Expect100Continue`속성을 false로 설정할 수 있습니다. `ServicePoint` 개체의 호스트 조각 및 스키마와 부합하는 URI를 통해 클라이언트에서 만드는 모든 후속 POST 및 PUT 요청은 Expect: 100-Continue 헤더 없이 전송됩니다. 다음 코드는 `http` 스키마와 `www.contoso.com` 호스트를 포함하는 URI로 전송되는 모든 요청을 구성하는 `ServicePoint` 개체를 구성하는 방법을 보여줍니다.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	후속으로 생성되는 모든 `ServicePoint` 개체에 대해 이 속성의 기본값을 지정하기 위하여 `ServicePointManager` 클래스의 정적 `Expect100Continue` 속성을 설정할 수도 있습니다. 자세한 정보는 Microsoft 웹 사이트의 [ServicePoint 클래스](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) 페이지를 참조하세요.

- **다수의 개체를 반환할 수 있는 요청에 대해 페이지 매김을 지원합니다**.

	컬렉션에 다수의 리소스가 포함되어 있는 경우 해당 URI에 GET 요청을 발급하면 Web API를 호스팅하는 서버에 상당한 양의 프로세스를 발생시켜서 성능에 영향을 미치고 상당한 양의 네트워크 트래픽이 발생하여 대기 시간을 증가시킬 수 있습니다.

	이런 경우를 처리하기 위하여 Web API는 클라이언트 응용 프로그램이 비교적 처리가 쉬운 블록(이나 페이지)에서 요청을 구체화하거나 데이터를 가져올 수 있도록 하는 쿼리 문자열을 지원해야 합니다. ASP.NET Web API 프레임워크는 쿼리 문자열의 구문을 분석하고 앞에서 설명한 라우팅 규칙에 따라서 적절한 메서드로 전달되는 일련의 매개 변수/값 쌍으로 쪼갭니다. 메서드는 쿼리 문자열에 지정된 것과 같은 이름을 사용하여 이들 매개 변수를 받아 들이도록 구현되어야 합니다. 또한 이들 매개 변수는 선택적이어야 하며(클라이언트가 요청에서 쿼리 문자열을 생략하는 경우) 의미 있는 기본값을 가져야 합니다. 아래 코드는 `Orders` 컨트롤러의 `GetAllOrders` 메서드를 보여줍니다. 이 메서드는 order의 세부 정보를 검색합니다. 이 메서드에 제약이 없다면 아마도 대량의 데이터를 반환할 수 있을 것입니다. `limit` 및 `offset` 매개 변수는 데이터의 양을 보다 작은 하위 집합으로 줄이기 위한 것이며, 이 경우에는 기본적으로 처음 10개의 order만 해당합니다.

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders")]
    	[HttpGet]
    	public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    	{
    	    // Find the number of orders specified by the limit parameter
    	    // starting with the order specified by the offset parameter
    	    var orders = ...
    	    return orders;
    	}
    	...
	}
	```

	클라이언트 응용 프로그램은 URI \_http://www.adventure-works.com/api/orders?limit=30&offset=50_를 사용하여 offset 50에서 시작하여 order 30개를 가져오기 위해 요청을 발급할 수 있습니다.

	> [AZURE.TIP] 2000자 보다 긴 URI를 생성하는 쿼리 문자열을 지정하도록 클라이언트 응용 프로그램을 사용하지 않습니다. 많은 웹 클라이언트 및 서버는 이렇게 긴 URI를 처리할 수 없습니다.

<a name="considerations-for-maintaining-responsiveness"></a>
## 응답성, 확장성 및 가용성 유지 관리를 위한 고려 사항

동일한 Web API를 전세계 어디에서나 실행되고 있는 많은 클라이언트 응용 프로그램에서 활용할 수 있습니다. Web API는 부하가 큰 경우에도 응답성을 유지하도록, 변화가 매우 큰 워크로드를 지원하기 위해 축소와 확장이 가능하도록, 주요 비즈니스 작업을 수행하는 클라이언트에 대한 가용성을 보장하도록 구현하는 것이 중요합니다. 이러한 요구 사항을 충족하기 위한 방법을 결정할 때 다음 사항을 고려합니다.

- **오래 실행되는 요청에 대해 비동기 지원을 제공합니다**.

	처리에 긴 시간에 소요되는 요청은 요청을 제출한 클라이언트를 차단하지 말고 수행되어야 합니다. Web API는 요청의 유효성을 검사하기 위한 초기 확인을 수행하고 업무를 수행할 개별 작업을 시작하고, HTTP 코드 202(수락됨)와 함께 응답 메시지를 반환할 수 있습니다. 작업은 Web API 프로세스의 일부로 비동기 실행되거나 Azure WebJob(Web API가 Azure 웹 사이트에 의해 호스팅되는 경우) 또는 작업자 역할(Web API가 Azure 클라우드 서비스로 구현되는 경우)로 오프로드될 수 있습니다.

	> [AZURE.NOTE] Azure 웹 사이트와 WebJobs사용에 대한 자세한 내용은 Microsoft 웹 사이트의 [Microsoft Azure 웹 사이트에서 WebJobs를 사용하여 백그라운드 작업 실행](../articles/app-service-web/web-sites-create-web-jobs.md) 페이지를 참조하세요.

	Web API는 처리 결과를 클라이언트 응용 프로그램에 반환하기 위한 메커니즘을 제공해야 합니다. 처리가 완료되었는지를 주기적으로 쿼리하고 결과를 입수하기 위하여 클라이언트 응용 프로그램에 대한 폴링 메커니즘을 제공하거나 작업이 완료되면 Web API가 알림을 보낼 수 있도록 하는 방법으로 구현할 수 있습니다.

	다음 방법을 사용하여 가상 리소스 역할을 하는 _polling_ URI를 제공하여 간단한 폴링 메커니즘을 구현할 수 있습니다.

	1. 클라이언트 응용 프로그램이 Web API에 초기 요청을 보냅니다.

	2. Web API가 요청에 대한 정보를 Microsoft Azure 캐시 또는 테이블 저장소에 있는 테이블에 저장하고 이 항목에 대한 고유 키를 가급적 GUID 형태로 생성합니다.

	3. Web API는 별도의 작업으로 처리를 시작합니다. Web API는 테이블에 작업 상태를 _Running_으로 기록합니다.

	4. Web API는 HTTP 상태 코드 202(수락됨)와 함께 메시지 본문에 테이블 항목의 GUID를 넣어서 응답 메시지를 반환합니다.

	5. 작업이 완료되면 Web API는 결과를 테이블에 저장하고 작업의 상태를 _Complete_으로 설정합니다. 작업이 실패하면 Web API는 실패에 대한 정보를 보관하고 상태를 _Failed_로 설정할 수 있습니다.

	6. 작업이 실행되는 동안 클라이언트는 자체적인 프로세스를 계속 수행할 수 있습니다. URI _/polling/{guid}_에 주기적으로 요청을 보낼 수 있으며, _{guid}_는 Web API에 의해 202 응답 메시지로 반환된 GUID입니다.

	7. _/polling{guid}_ URI의 Web API는 테이블에 있는 해당 작업의 상태를 쿼리하고 이 상태(_Running_, _Complete_, 또는 _Failed_)를 포함하는 HTTP 상태 코드 200(정상)과 함께 응답 메시지를 반환합니다. 작업이 완료되거나 실패하면 응답 메시지에 처리 결과 또는 실패의 이유에 대한 정보를 포함시킬 수 있습니다.

	알림을 구현하려는 경우 가능한 옵션은 다음과 같습니다.

	- Azure 알림 허브를 사용하여 클라이언트 응용 프로그램에 대한 비동기 응답을 푸시합니다. Microsoft 웹 사이트의 [Azure Notification Hubs Notify Users](notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)(Azure 알림 허브 사용자에게 알림) 페이지에 자세한 내용이 제공되어 있습니다.

	- Comet 모델을 사용하여 클라이언트와 Web API를 호스팅하는 서버 사이의 영구적인 네트워크 연결을 유지하고, 이 연결을 사용하여 서버의 메시지를 클라이언트로 푸시합니다. MSDN Magazine [Building a Simple Comet Application in the Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx)(Microsoft .NET Framework에서 간단한 Comet 응용 프로그램 빌드) 문서에 예제 솔루션 설명이 있습니다.

	- SignalR을 사용하여 영구적인 네트워크 연결을 통해 실시간으로 웹 서버에서 클라이언트로 데이터를 푸시합니다. SignalR은 ASP.NET 웹 응용프로그램에서 NuGet 패키지로 사용할 수 있습니다. [ASP.NET SignalR](http://signalr.net/) 웹 페이지에서 자세한 내용을 확인할 수 있습니다.

	> [AZURE.NOTE] Comet 및 SignalR 모두 웹 서버와 클라이언트 응용 프로그램 사이의 영구적인 네트워크 연결을 활용합니다. 이것은 많은 수의 클라이언트에서 그만큼 많은 수의 동시 연결을 필요로 할 수 있기 때문에 확장성에 영향을 줄 수 있습니다.

- **각 요청은 상태 비저장이어야 합니다**.

	각 요청을 원자성으로 간주해야 합니다. 클라이언트 응용 프로그램이 만든 요청과 동일한 클라이언트에서 제출한 후속 요청 사이에는 종속성이 없어야 합니다. 이 방법은 확장성을 지원합니다. 웹 서비스 인스턴스는 수많은 서버에 배포될 수 있습니다. 클라이언트 요청은 이들 중 어떤 인스턴스에도 전달될 수 있으며 결과는 항상 동일해야 합니다. 비슷한 이유로 가용성을 향상시킵니다. 웹 서버가 실패하면 요청은 서버가 재시작되는 동안 클라이언트 응용 프로그램에 나쁜 효과를 미치지 않고 다른 인스턴스로(Azure 트래픽 관리자를 사용하여) 라우팅될 수 있습니다.

- **클라이언트를 추적하고 제한을 구현하여 DOS 공격 가능성을 줄입니다**.

	특정 클라이언트가 일정 시간 안에 대단히 많은 수의 요청을 하면, 서비스를 독점할 수 있고 다른 클라이언트의 성능에 영향을 미칠 수 있습니다. 이 문제를 완화하기 위하여 Web API는 들어오는 모든 요청의 IP 주소를 추적하거나 각각의 인증된 액세스를 기록하는 방식으로 클라이언트 응용 프로그램에서 나오는 호출을 모니터링할 수 있습니다. 리소스 액세스를 제한하기 위해 이 정보를 사용할 수 있습니다. 클라이언트가 정해진 제한을 초과하면 Web API는 상태 503(서비스를 사용할 수 없음)과 함께 다음 요청을 언제 보낼 수 있는지(거부되지 않고)를 나타내는 Retry-After 헤더를 포함하는 응답 메시지를 반환할 수 있습니다. 이 전략은 시스템을 지연시키는 클라이언트로부터 DoS(서비스 거부) 공격의 가능성을 줄이는데 도움이 될 수 있습니다.

- **영구적인 HTTP 연결을 신중하게 관리합니다**.

	HTTP 프로토콜은 영구적인 HTTP 연결(이 가능한 곳에서)을 지원합니다. HTTP 1.0 사양은 클라이언트 응용 프로그램이 새로 연결을 만들지 않고 동일한 연결을 사용하여 후속 요청을 보낼 수 있음을 서버에 지시할 수 있도록 하는 Connection:Keep-Alive 헤더를 추가했습니다. 호스트가 정한 기간 내에 클라이언트가 연결을 재사용하지 않으면 연결이 자동으로 닫힙니다. 이러한 동작은 Azure 서비스에 의해 사용되며 HTTP 1.1의 기본값입니다. 따라서 메시지에 Keep-Alive 헤더를 포함시킬 필요가 없습니다.

	연결을 열린 상태로 유지하면 대기 시간과 네트워크 혼잡이 감소되어 응답성 향상에 도움이 되지만 불필요한 연결을 필요한 시간 보다 길게 열린 상태로 유지하고 다른 클라이언트의 동시간대 연결 성능을 제한하기 때문에 확장성에는 불리할 수 있습니다. 클라이언트 응용 프로그램이 모바일 장치에서 실행되는 경우 배터리 수명에도 영향을 미칠 수 있습니다. 응용 프로그램이 서버에 간헐적인 요청을 보내는 경우, 연결을 열린 상태로 유지하면 배터리가 보다 빨리 방전될 수 있습니다. HTTP 1.1 연결을 영구적으로 만들지 않으려면 클라이언트는 기본 동작을 재정의하는 메시지에 Connection:Close 헤더를 포함할 수 있습니다. 마찬가지로 서버가 매우 많은 수의 클라이언트를 처리하는 경우 응답 메시지에 Connection:Close 헤더를 포함할 수 있으며 이렇게 하면 연결이 닫히고 서버 자원을 절약할 수 있습니다.

	> [AZURE.NOTE] 영구적인 HTTP 연결은 반복적으로 통신 채널을 수립하면서 발생하는 네트워크 오버헤드를 줄이기 위한 선택적인 기능입니다. Web API나 클라이언트 응용 프로그램 그 어느 쪽도 영구적인 HTTP 연결(을 사용할 수 있다고 해도)에 의존하지 말아야 합니다. Comet 스타일 알림 시스템을 구현하기 위해 영구적인 HTTP 연결을 사용하지 마십시오. 대신 TCP 계층의 소켓(또는 가능하면 Websocket)을 활용하십시오. 클라이언트 응용 프로그램이 프록시를 통해 서버와 통신하는 경우 Keep-Alive 헤더는 별로 쓸모가 없습니다. 클라이언트와 프록시의 연결만 영구적입니다.

## Web API 게시 및 관리에 대한 고려 사항

Web API를 클라이언트 응용 프로그램에서 사용할 수 있도록 하려면 Web API가 호스트 환경에 배포되어야 합니다. 일반적으로 이런 환경은(다른 유형의 호스트 프로세스가 될 수도 있지만) 웹 서버입니다. Web API를 게시하는 경우 다음 사항을 고려합니다.

- 모든 요청은 인증되고 권한이 부여되어야 하며, 적절한 액세스 제어 수준이 강제 적용되어야 합니다.
- 상용 Web API는 응답 시간에 대한 다양한 품질 보장을 받을 수 있습니다. 시간 별로 부하가 상당히 많이 달라질 수 있다면, 호스트 환경의 확장성을 확인하는 것이 중요합니다.
- 경제적인 목적을 위해 요청을 계량 측정하는 것이 필요할 수 있습니다.
- Web API에 대한 트래픽의 흐름을 규제하고 할당량이 소진된 클라이언트에 대해 제한을 구현하는 것이 필요할 수 있습니다.
- 규제 요구 사항은 모든 요청과 응답의 기록 및 감사를 위임할 수 있습니다.
- 가용성을 보장하기 위하여 Web API를 호스팅하는 서버의 상태를 모니터링하고 필요하면 재시작해야 할 수 있습니다.

Web API 구현과 관련하여, 이러한 문제를 기술적인 문제와 분리하는 것이 유용할 수 있습니다. 따라서 별도 프로세스로 실행되면서 Web API에 요청을 라우팅하는 [façade](http://en.wikipedia.org/wiki/Facade_pattern)(퍼사드) 생성을 고려하는 것이 좋습니다. façade(퍼사드)는 관리 작업을 제공하고 유효성을 검사한 요청을 Web API로 전달할 수 있습니다. façade(퍼사드)를 사용하면 다음과 같이 기능적인 이점이 많이 있습니다.

- 여러 Web API에 대한 통합 지점 역할을 합니다.
- 메시지를 변환하고 다양한 기술을 사용하여 빌드한 클라이언트의 통신 프로토콜을 변환합니다.
- Web API를 호스팅하는 서버의 부하를 줄이기 위해 요청 및 응답을 캐시합니다.

## Web API 테스트에 대한 고려 사항
Web API는 다른 소프트웨어와 마찬가지로 철저히 테스트해야 합니다. 다른 유형의 응용 프로그램과 마찬가지로, 각 작업 기능의 유효성을 검사하기 위한 단위 테스트를 생성하는 것이 좋습니다. 자세한 내용은 Microsoft 웹 사이트의 [단위 테스트를 사용하여 코드 확인](https://msdn.microsoft.com/library/dd264975.aspx) 페이지를 참조하세요.

> [AZURE.NOTE] 이 지침과 함께 사용할 수 있는 샘플 Web API에는 선택한 작업에 대한 단위 테스트 수행 방법을 보여주는 테스트 프로젝트가 포함되어 있습니다.

Web API의 특성에 따라 제대로 작동하는지를 확인하기 위한 요구 사항이 달라집니다. 다음과 같은 측면에 특히 주의를 기울여야 합니다.

- 올바른 작업을 호출하는지 확인하기 위하여 모든 경로를 테스트합니다. 예상치 않은 HTTP 상태 코드 405(메서드가 허용되지 않음) 반환에 특히 유의하십시오. 경로 및 경로에 디스패치할 수 있는 HTTP 메서드(GET, POST, PUT, DELETE) 사이의 불일치를 나타내는 것일 수 있습니다.

	HTTP 요청을 지원하지 않는 경로에 HTTP 요청을 보냅니다. 예를 들어 특정 리소스에 POST 요청을 제출합니다. POST 요청은 리소스 컬렉션에만 전송해야 합니다. 이런 경우 유효한 응답은 상태 코드 405(허용되지 않음)여야 _합니다_.

- 모든 경로가 적절하게 보호되는지 적절한 인증 및 권한에 속하는지 확인합니다.

	> [AZURE.NOTE] 사용자 인증과 같은 보안의 일부 측면은 Web API가 아닌 호스트 환경의 책임일 가능성이 높지만 배포 과정의 일부로 보안 테스트를 포함시킬 필요가 있습니다.

- 각 작업에 의해 수행되는 예외 처리를 테스트한 후 적절하고 의미 있는 HTTP 응답이 클라이언트 응용 프로그램에 반환되는지를 확인합니다.
- 요청 및 응답 메시지가 올바르게 구성되었는지 확인합니다. 예를 들어, HTTP POST 요청이 x-www-form-urlencoded 형식으로 된 새로운 리소스의 데이터를 포함하는 경우, 해당 작업이 데이터를 올바르게 분석하는지, 리소스를 생성하는지, 새 리소스의 세부 사항(올바른 Location 헤더를 포함하여)을 포함하는 응답을 반환하는지를 확인합니다.
- 응답 메시지의 모든 링크와 URI를 확인합니다. 예를 들어, HTTP POST 메시지는 새로 생성된 리소스의 URI를 반환해야 합니다. 모든 HATEOAS 링크는 유효해야 합니다.

	> [AZURE.IMPORTANT] API 관리 서비스를 통해 Web API를 게시하는 경우, 이 URI는Web API를 호스팅하는 웹 서버의 URL이 아닌, 관리 서비스의 URL을 반영해야 합니다.

- 각 작업이 다양한 입력 조합에 대해 올바른 상태 코드를 반환하는지 확인합니다. 예:
	- 쿼리가 성공적이면 상태 코드 200(OK)을 반환해야 합니다.
	- 리소스를 찾을 수 없으면 작업은 HTTP 상태 코드 404(찾을 수 없음)을 반환해야 합니다.
	- 클라이언트가 요청을 전송하여 리소스 삭제를 완료한 경우, 상태 코드는 204(콘텐츠 없음)여야 합니다.
	- 클라이언트에서 새 리소스를 생성하는 요청을 보낸 경우 상태 코드는 201(생성됨)이어야 합니다.

5xx 범위의 예상치 못한 응답 상태 코드에 유의하시기 바랍니다. 이런 메시지는 보통 호스트 서버가 유효한 요청을 수행할 수 없다는 것을 나타내기 위해 보고됩니다.

- 클라이언트 응용 프로그램에서 지정할 수 있는 다양한 조합의 헤더 요청을 테스트하고 Web API가 응답 메시지에 예상 정보를 반환하는지 확인합니다.

- 쿼리 문자열을 테스트합니다. 작업이 선택적인 매개 변수(예: 페이지 매김 요청)를 취할 수 있으면 매개 변수의 다른 조합과 순서를 테스트합니다.

- 비동기 작업이 완료되었는지 확인합니다. Web API가 큰 바이너리 개체를 반환하는 요청에 대해 스트리밍을 지원하는 경우 데이터가 스트리밍되는 동안 클라이언트 요청이 차단되지 않도록 합니다. Web API가 오래 실행되는 데이터 수정 작업에 대해 폴링을 수행하는 경우, 작업이 진행되는 동안 상태를 제대로 보고하는지 확인합니다.

Web API가 감금 하에서도 만족스럽게 작동하는지 확인하기 위하여 성능 테스트를 생성하고 실행해야 합니다. Visual Studio Ultimate을 사용하여 웹 성능 및 부하 테스트를 빌드할 수 있습니다. 자세한 정보는 Microsoft 웹 사이트의 [앱에서 성능 테스트 실행](https://msdn.microsoft.com/library/dn250793.aspx) 페이지를 참조하세요.

## Azure API 관리 서비스를 사용하여 Web API 게시 및 관리

Azure에는 Web API를 게시 및 관리하는데 사용할 수 있는 [API 관리 서비스](https://azure.microsoft.com/documentation/services/api-management/)가 제공됩니다. 이 기능을 사용하여 하나 이상의 Web API에 대해 façade(퍼사드) 역할을 하는 서비스를 생성할 수 있습니다. 서비스는 Azure 관리 포털을 사용하여 만들고 구성할 수 있는 축소/확장이 가능한 웹 서비스입니다. 이 서비스를 사용하여 다음과 같이 Web API를 게시 및 관리할 수 있습니다.

1. Web API를 웹 사이트, Azure 클라우드 서비스 또는 Azure 가상 컴퓨터에 배포합니다.

2. API 관리 서비스를 Web API에 연결합니다. 관리 API의 URL로 전송된 요청은 Web API의 URI로 매핑됩니다. 동일한 API 관리 서비스가 하나 이상의 Web API에 요청을 라우팅할 수 있습니다. 이를 통해 여러 개의 Web API를 단일 관리 서비스로 모을 수 있습니다. 마찬가지로, 다른 응용 프로그램에서 사용할 수 있는 기능을 제한하거나 분할하는 경우 동일한 Web API를 하나 이상의 API 관리 서비스에서 참조할 수 있습니다.

	> [AZURE.NOTE] HTTP GET 요청에 대한 응답의 일부로 생성된 HATEOAS 링크의 URI는 Web API를 호스팅하는 웹 서버가 아닌 API 관리 서비스의 URL을 참조해야 합니다.

3. 각각의 Web API에 대해 Web API가 작업에 입력 내용으로 취할 수 있는 다른 선택적인 매개 변수와 함께 노출하는 HTTP 작업을 명시합니다. API 관리 서비스가 동일한 데이터에 대해 반복되는 요청을 최적화하기 위해 Web API로부터 수신한 응답을 캐시해야 할지를 구성할 수 있습니다. 각각의 작업이 생성하는 HTTP 응답의 세부 사항을 기록합니다. 이 정보는 개발자를 위한 문서를 생성하는데 사용됩니다. 따라서 정확하고 완전한 정보를 기록하는 것이 중요합니다.

	Azure 관리 포털에서 제공하는 마법사를 사용하여 수동으로 작업을 정의하거나 WADL 또는 Swagger 형식의 정의를 포함하는 파일로부터 가져올 수 있습니다.

4. API 관리 서비스와 Web API를 호스팅하는 서버 사이의 통신에 대한 보안 설정을 구성합니다. API 관리 서비스는 OAuth 2.0 사용자 권한 부여 및 인증서를 사용하여 기본 인증 및 상호 인증을 지원합니다.

5. 제품을 생성합니다. 제품은 게시 단위입니다. 이전에 관리 서비스에 연결한 Web API를 제품에 추가합니다. 제품이 게시되면 Web API를 개발자들이 사용할 수 있게 됩니다.

	> [AZURE.NOTE] 제품 게시에 앞서, 제품에 액세스할 수 있는 사용자 그룹을 정의하고 그룹에 사용자를 추가할 수 있습니다. 이렇게 하면 Web API를 사용할 수 있는 개발자와 응용 프로그램을 관리할 수 있습니다. 승인을 받아야 하는 Web API에 대해 액세스 권한을 확보하려면 개발자는 제품 관리자에게 반드시 요청을 보내야 합니다. 관리자는 개발자의 액세스를 허용하거나 거부할 수 있습니다. 상황이 변하면 기존 개발자를 차단할 수도 있습니다.

6.	각각의 Web API에 대한 정책을 구성합니다. 정책은 도메인 간 호출이 허용되어야 하는지, 클라이언트를 어떻게 인증할지, XML과 JSON 데이터 형식 간의 변환을 투명하게 처리할지, 특정 IP 범위에서 들어오는 호출을 제한할지,사용 할당량, 호출 속도를 제한할지 등과 같은 측면을 제어합니다. 정책은 전체 프로젝트 포괄적으로 적용되거나 제품에 포함된 단일 Web API에 적용되거나 Web API에 포함된 개별 작업에 대해 적용될 수 있습니다.

이러한 작업을 수행하는 방법에 대한 자세한 내용은 Microsoft 웹 사이트의 [API 관리](https://azure.microsoft.com/services/api-management/) 페이지를 참조하세요. Azure API 관리 서비스에는 Web API 구성 과정을 간소화하기 위한 사용자 지정 인터페이스를 빌드할 수 있도록 자체적인 REST 인터페이스가 제공됩니다. 자세한 내용을 보려면 Microsoft 웹 사이트의 [Azure API 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn776326.aspx) 페이지를 방문하세요.

> [AZURE.TIP] Azure에는 장애 조치(failover) 및 부하 분산을 구현하고, 지리적으로 다른 위치에서 호스팅되는 웹 사이트의 복수 인스턴스에 대해 대기 시간을 줄일 수 있도록 하는 Azure 트래픽 관리자가 제공됩니다. Azure 트래픽 관리자를 API 관리 서비스와 결합하여 사용할 수 있습니다. API 관리 서비스는 Azure 트래픽 관리자를 통해 웹 사이트의 인스턴스로 요청을 라우팅할 수 있습니다. 자세한 내용은 Microsoft 웹 사이트의 [트래픽 관리자 라우팅 메서드](../articles/traffic-manager/traffic-manager-routing-methods.md) 페이지를 방문하세요.

> 이런 구조에서 웹 사이트에 사용자 지정 DNS 이름을 사용하면 Azure 트래픽 관리자 웹 사이트의 DNS 이름을 포인트하도록 각 웹 사이트에 대해 적절한 CNAME 레코드를 구성해야 합니다.

## 클라이언트 응용 프로그램을 빌드하는 개발자 지원
클라이언트 응용 프로그램을 구축하는 개발자들에게는 일반적으로 Web API 액세스 방법, 웹 서비스와 클라이언트 응용 프로그램 간의 다양한 요청과 응답을 명시하는 매개 변수, 데이터 유형, 반환 유형 및 반환 코드에 관한 문서가 필요합니다.

### Web API에 대한 REST 작업 문서화
Azure API 관리 서비스는 Web API에 의해 노출되는 REST 작업을 설명하는 개발자 포털을 포함합니다. 제품이 게시되면 포털에 표시됩니다. 개발자는 이 포털을 사용하여 액세스 등록을 할 수 있습니다. 그 후 관리자는 요청을 수락하거나 거부할 수 있습니다. 승인된 개발자에게는 그들이 개발하는 클라이언트 응용 프로그램의 호출을 인증하는데 사용되는 구독 키가 할당됩니다. 이 키는 각각의 Web API에 대해 제공되어야 하며 그렇지 않으면 거부됩니다.

이 포털에는 다음과 같은 내용도 제공됩니다.

- 제품이 노출하는 작업 목록, 필요한 매개 변수, 반환될 수 있는 다양한 응답 등을 을 포함하는 제품에 대한 문서. 이 정보는 [Publishing a web API by using the Microsoft Azure API Management Service](#publishing-a-web-API)(Microsoft Azure API 관리 서비스를 사용한 Web API 게시) 섹션의 3단계에 제공되는 세부 내용으로부터 생성됩니다.

- JavaScript, C#, Java, Ruby, Python 및 PHP를 비롯한 언어로부터 작업을 호출하는 방법을 보여주는 코드 조각.

- 개발자가 제품에 포함된 각각의 작업을 테스트하기 위해 HTTP 요청을 보내고 결과를 볼 수 있게 하는 개발자 콘솔.

- 개발자가 발견한 이슈나 문제를 보고할 수 있는 페이지.

Azure 관리 포털에서 개발자는 스타일이나 레이아웃을 회사의 브랜딩에 맞게 변경하기 위하여 개발자 포털을 사용자 지정할 수 있습니다.

### 클라이언트 SDK 구현
Web API를 액세스하기 위한 REST 요청을 호출하는 클라이언트 응용 프로그램을 빌드하려면 각각의 요청을 구성하여 적절한 형식을 갖추도록 하고, 웹 서비스를 호스팅하는 서버에 요청을 보내고, 응답의 구문을 해석하여 요청이 성공했는지 실패했는지를 파악하고 반환된 데이터를 추출하도록 하는 등의 코드를 상당히 많이 작성해야 합니다. 클라이언트 응용 프로그램에 대해 이러한 염려를 해결하기 위하여 기능적인 메서드 세트 내부에 하위 수준의 세부 내용을 요약하고 REST 인터페이스를 요약해 놓은 SDK를 제공할 수 있습니다. 클라이언트 응용 프로그램은 이런 메서드를 사용하여 호출을 REST 요청으로 투명하게 변환한 후 응답을 다시 메서드 반환 값으로 변환합니다. 이것은 Azure SDK를 비롯한 많은 서비스에서 구현되는 일반적인 방법입니다.

클라이언트쪽 SDK 생성은 일관적으로 구현되어야 하고 신중한 테스트를 거쳐야 하기 때문에 상당한 작업입니다. 하지만, 이런 프로세스의 대부분은 기계적으로 생성될 수 있으며, 많은 공급 업체가 이런 작업을 자동화할 수 있는 도구를 제공하고 있습니다.

## Web API 모니터링

Web API를 게시하고 배포한 방법에 따라서 Web API를 직접 모니터링하거나 API 관리 서비스를 통과하는 트래픽을 분석하여 사용 정보와 상태 정보를 모을 수 있습니다.

### Web API 직접 모니터링
ASP.NET Web API 템플릿(Azure 클라우드 서비스의 Web API 프로젝트 또는 웹 역할로) 또는 Visual Studio 2013을 사용하여 Web API를 구현한 경우 ASP.NET Application Insights를 사용하여 가용성, 성능 및 사용 현황 데이터를 수집할 수 있습니다. Application Insights는 Web API가 클라우드에 배포될 때 요청과 응답에 대한 정보를 투명하게 추적하고 기록하는 패키지입니다. 패키지를 설치하고 구성한 후에는 사용을 위해 Web API의 코드를 수정할 필요가 없습니다. Azure 웹 사이트에 Web API를 배포하는 경우 모든 트래픽은 검사되며 다음과 같은 통계 자료가 수집됩니다.

- 서버 응답 시간.

- 서버 요청의 수 및 각 요청의 세부 내용.

- 평균 응답 시간 기준 가장 느린 요청.

- 실패한 요청에 대한 세부 정보.

- 다른 브라우저 및 사용자 에이전트에 의해 시작된 세션의 수.

- 가장 많이 조회된 페이지(Web API보다는 주로 웹 응용 프로그램에 유용함).

- Web API에 액세스하는 다른 사용자 역할.

이런 데이터를 Azure 관리 포털에서 실시간으로 볼 수 있습니다. Web API 상태를 모니터링하는 웹 테스트를 생성할 수도 있습니다. 웹 테스트는 Web API의 URI에 주기적으로 요청을 보내고 응답을 캡처합니다. 성공적인 응답의 정의(예: HTTP 상태 코드 200)를 명시할 수 있고, 요청이 이런 응답을 반환하지 않으면 관리자에게 경고를 보내도록 준비할 수 있습니다. 필요한 경우 관리자는 Web API에 오류가 발생하면 Web API를 호스팅하는 서버를 재시작할 수 있습니다.

Microsoft 웹 사이트의 [Application Insights - ASP.NET 시작](../articles/application-insights/app-insights-asp-net.md) 페이지에서는 자세한 정보를 제공합니다.

### API 관리 서비스를 통한 Web API 모니터링

API 관리 서비스를 사용하여 Web API를 게시한 경우 Azure 관리 포털의 API 관리 페이지에 서비스의 전반적인 성능을 볼 수 있는 대시보드가 포함됩니다. 분석 페이지에서는 제품이 어떻게 사용되는지를 자세히 드릴다운할 수 있습니다. 이 페이지는 다음과 같은 탭을 포함합니다.

- **사용 현황**. 이 탭은 실행된 API 호출의 수와 시간 별로 호출을 처리하는데 사용된 대역폭에 대한 정보를 제공합니다. 제품, API, 작업 별로 사용 현황 세부 내용을 필터링할 수 있습니다.

- **상태**. 이 탭에서는 API 요청의 결과(반환된 HTTP 상태 코드), 캐시 정책의 효율성, API 응답 시간, 서비스 응답 시간을 볼 수 있습니다. 제품, API, 작업 별로 상태 데이터를 필터링할 수 있습니다.

- **활동**. 이 탭에는 성공적인 호출, 실패한 호출, 차단된 호출의 수와 평균 응답 시간 그리고 각 제품, Web API 및 작업에 대한 응답 시간을 요약한 텍스트가 제공됩니다. 이 페이지에는 각 개발자가 생성한 호출의 수가 나열되어 있습니다.

- **개요**. 이 탭에는 최다 API 호출, 제품, Web API의 생성에 책임이 있는 개발자 그리고 이런 호출을 수신하는 작업을 비롯한 성능 데이터에 대한 요약 정보가 표시됩니다.

이 정보를 사용하여 병목 현상을 유발하는 Web API나 작업이 있는지 호스트 환경을 축소/확장할 필요가 있는 서버를 더 추가할 필요가 있는지를 판단할 수 있습니다. 하나 또는 그 이상의 응용 프로그램이 균형이 맞지 않는 양의 리소스를 사용하고 있는지를 확인하고 할당량을 설정하여 호출 속도를 제한하는 적절한 정책을 적용할 수 있습니다.

> [AZURE.NOTE] 게시된 제품의 세부 정보를 변경할 수 있고 변경 내용은 즉시 적용됩니다. 예를 들면, Web API를 포함하는 제품을 다시 게시할 필요 없이Web API의 작업을 추가하거나 삭제할 수 있습니다.

## 관련된 패턴
- [façade](http://en.wikipedia.org/wiki/Facade_pattern)(퍼사드) 패턴은 Web API에 인터페이스를 제공하는 방법을 설명합니다.

## 자세한 정보
- Microsoft 웹 사이트의 [Learn About ASP.NET Web API](http://www.asp.net/web-api)(ASP.NET 웹 API에 대해 자세히 알아보기) 페이지에는 Web API를 사용한 RESTful 웹 서비스 구축에 대한 자세한 소개가 제공되어 있습니다.
- Microsoft 웹 사이트의 [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)(ASP.NET 웹 API에서 라우팅을) 페이지는 ASP.NET Web API 프레임워크에서 규칙 기반 라우팅이 작동하는 방식을 설명합니다.
- 특성 기반 라우팅에 대한 자세한 내용은 Microsoft 웹 사이트의 [Attribute Routing in Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)(Web API 2에서 특성 라우팅) 페이지를 참조하세요.
- OData 웹 사이트의 [Basic Tutorial](http://www.odata.org/getting-started/basic-tutorial/) 페이지는 OData 프로토콜의 기능에 대한 소개를 제공합니다.
- Microsoft 웹 사이트의 [ASP.NET Web API OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) 페이지는 ASP.NET을 사용하여 OData 웹을 구현하는 자세한 정보와 예제를 포함합니다.
- Microsoft 웹 사이트의 [Introducing Batch Support in Web API and Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx)(Web API 및 Web API OData의 배치 지원 소개) 페이지는 OData를 사용하여 Web API에 배치 작업을 구현하는 방법을 설명합니다.
- Jonathan Oliver의 블로그에 있는 [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/)(멱등 패턴) 문서에는 멱등에 대한 개요 및 데이터 관리 옵션과의 관계가 제공되어 있습니다.
- W3C 웹 사이트의 [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(상태 코드 정의) 페이지는 HTTP 상태 코드의 전체 목록과 그에 대한 설명을 포함합니다.
- ASP.NET Web API를 통한 HTTP 예외 처리에 대한 자세한 정보를 보려면 Microsoft 웹 사이트의 [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling)(ASP.NET Web API에서 예외 처리) 페이지를 방문하세요.
- Microsoft 웹 사이트의 [Web API Global Error Handling](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling)(Web API 전역 오류 처리) 문서는 Web API에 대한 전역 오류 처리 및 로깅 전략을 구현하는 방법을 설명합니다.
- Microsoft 웹 사이트의 [WebJob을 사용하여 백그라운드 작업 실행](../articles/app-service-web/web-sites-create-web-jobs.md) 페이지는 Azure 웹 사이트에서 백그라운드 작업을 수행하기 위해 WebJob을 사용하는 것에 대한 정보와 예제를 제공합니다.
- Microsoft 웹 사이트의 [Azure Notification Hubs Notify Users](notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)(Azure 알림 허브 사용자 알림) 페이지는 Azure 알림 허브를 사용하여 클라이언트 응용 프로그램에 비동기 응답을 푸시하는 방법을 보여줍니다.
- Microsoft 웹 사이트의 [API 관리](https://azure.microsoft.com/services/api-management/) 페이지는 Web API에 제어 및 보안 액세스를 제공하는 제품을 게시하는 방법을 설명합니다.
- Microsoft 웹 사이트의 [Azure API Management REST API Reference](https://msdn.microsoft.com/library/azure/dn776326.aspx)(Azure API 관리 REST API 참조) 페이지는 API 관리 REST API를 사용하여 사용자 지정 관리 응용 프로그램을 빌드하는 방법을 설명합니다.
- Microsoft 웹 사이트의 [트래픽 관리자 라우팅 메서드](../articles/traffic-manager/traffic-manager-routing-methods.md) 페이지는 Web API를 호스팅하는 웹 사이트의 복수 인스턴스에 대한 부하 분산 요청에 Azure 트래픽 관리자를 사용하는 방법을 간추려 설명합니다.
- Microsoft 웹 사이트의 [Application Insights - ASP.NET 시작](../articles/application-insights/app-insights-asp-net.md) 페이지에서는 ASP.NET Web API 프로젝트에서 Application Insights를 설치하고 구성하는 방법에 대한 자세한 정보를 제공합니다.
- Microsoft 웹 사이트의 [단위 테스트를 사용하여 코드 확인](https://msdn.microsoft.com/library/dd264975.aspx) 페이지는 Visual Studio를 사용하여 단위 테스트를 생성하고 관리하는 자세한 정보를 제공합니다.
- Microsoft 웹 사이트의 [앱에서 성능 테스트 실행](https://msdn.microsoft.com/library/dn250793.aspx) 페이지는 Visual Studio Ultimate을 사용하여 웹 성능 및 부하 테스트 프로젝트를 생성하는 방법을 설명합니다.

<!---HONumber=AcomDC_0907_2016-->