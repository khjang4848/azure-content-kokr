<properties
	pageTitle="Azure 검색 관리 REST API 버전 2015-02-28 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색 관리 REST API: 버전 2015-02-28"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist" />

# 관리 API: 버전 2015-02-28

Azure 검색은 Microsoft Azure에서 호스팅되는 클라우드 검색 서비스입니다. 이 문서에서는 Azure 검색 관리 REST API의 **2015-02-28* 버전에 대해 설명합니다. 이후에 최신 버전으로 대체되었습니다. 최신 정보는 MSDN에서 [Azure 검색 관리 REST API 2015-08-19](https://msdn.microsoft.com/library/dn832684.aspx)를 참조하세요.

##서비스 관리 작업

Azure 검색 서비스 관리 REST API는 관리자가 다음 작업을 자동화할 수 있도록 포털을 통해 사용할 수 있는 기능 대부분에 프로그래밍 방식의 액세스를 제공합니다.

- Azure 검색 서비스를 만들거나 삭제합니다.
- 검색 데이터 작업 인증에 사용되는 관리 키에 대한 일상적인 변경을 자동화하기 위해 `api-keys`를 만들거나 다시 생성하거나 검색합니다.
- 쿼리 볼륨 또는 저장소 요구 사항의 변경에 따라 Azure 검색 서비스의 규모를 조정합니다.

서비스를 프로그래밍 방식으로 완전히 관리하려면 두 개의 API 즉, Azure 검색의 관리 REST API와 일반적인 [Azure 리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)가 필요합니다. 리소스 관리자 API는 특정 서비스가 아닌 구독 데이터의 쿼리, 지리적 위치의 나열 등 일반적인 작업에 사용됩니다. 구독에서 Azure 검색 서비스를 만들고 관리하려면, HTTP 요청이 리소스 관리자 끝점, 구독 ID, 공급자(이 경우 Azure 검색), 검색 서비스 관련 작업을 포함하는지 확인하세요.

[Azure 검색 관리 REST API 시작](http://go.microsoft.com/fwlink/p/?linkID=516968) 은 응용 프로그램 구성 및 서비스 관리 작업을 보여주는 샘플 코드에 대 한 연습입니다. 샘플 응용 프로그램은 Azure 리소스 관리자 API 뿐만 아니라 Azure 검색을 위한 서비스 관리 API에 요청하여, 두 API를 이용하는 결합형 응용 프로그램을 종합하는 방법에 대한 아이디어를 제공합니다.

### 끝점 ###

서비스 관리 작업의 끝점은 Azure 리소스 관리자 `https://management.azure.com`의 URL입니다.

모든 관리 API 호출은 구독 ID 및 API 버전을 포함해야 합니다.

### 버전 ###

Azure 검색 관리 REST API의 현재 버전은 `api-version=2015-02-28`입니다. 이전 버전인 `api-version=2014-07-31-Preview`는 사용되지 않습니다. 이후 몇 개월 동안 계속 작동하지만 가능한 한 빨리 새 버전으로 전환하는 것이 좋습니다.

### 인증 및 액세스 제어###

Azure 검색 관리 REST API는 Azure 리소스 관리자의 확장이며 종속성을 공유합니다. 이와 같이 Active Directory는 Azure 검색 서비스 관리의 필수 구성 요소입니다. 클라이언트 코드의 모든 관리 요청은 요청이 리소스 관리자에 도달하기 전에 Azure Active Directory를 사용하여 인증되어야 합니다.

응용 프로그램 코드가 인덱스 또는 문서에서 *서비스 관리 작업*뿐만아니라 *데이터 작업*를 처리하는 경우, 각 Azure 검색 API에 대해 두 가지 인증 방법을 사용하게 됩니다.

- 리소스 관리자에 대 한 종속성으로 인해 서비스 및 키 관리는 인증을 위해 Active Directory에 의존합니다.
- 인덱스 만들기 또는 문서 검색과 같은 Azure 검색 서비스 끝점에 대한 데이터 요청은 요청 헤더에 있는 `api-key`을(를) 사용합니다. 데이터 요청 인증에 대한 정보는 [Azure 검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 참조하세요.

[Azure 검색 관리 REST API 시작](http://go.microsoft.com/fwlink/p/?linkID=516968)에 문서화되어 있는 샘플 응용 프로그램은 각 유형에 대한 인증 기술을 보여줍니다. Active Directory를 사용하기 위한 클라이언트 응용 프로그램 구성 지침은 시작하기에 포함됩니다.

Azure 리소스 관리자를 위한 액세스 제어는 기본 제공 소유자, 참가자 및 독자 역할을 사용합니다. 기본적으로 모든 서비스 관리자는 소유자 역할의 구성원입니다. 자세한 내용은 [Azure 클래식 포털의 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.


### API의 요약 ##

작업에는 다음과 같은 API가 포함됩니다.

- <a name="CreateService">검색 서비스 만들기</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">검색 서비스 가져오기</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListService">검색 서비스 나열</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28`

- <a name="DeleteService">검색 서비스 삭제</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="UpdateService">검색 서비스 업데이트</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">관리 키 나열</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">관리 키 다시 생성</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">쿼리 키 만들기</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">쿼리 키 나열</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28`

- <a name="DeleteQueryKey">쿼리 키 삭제</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28`

<a name="ServiceOps"></a>
##서비스 작동

Azure 구독에 대한 HTTP 요청을 실행하여 Azure 검색 서비스를 프로비전하거나 프로비전을 해제할 수 있습니다. 이러한 작업을 통해 사용할 수 있게 된 시나리오에는 사용자 지정 관리 도구 또는 종단간 프로덕션 또는 개발 환경(서비스 만들기에서 인덱스 채우기까지) 지원을 포함합니다. 마찬가지로, 클라우드 솔루션을 설계하고 판매하는 솔루션 공급업체는 각각의 신규 고객에게 서비스를 프로비저닝하기 위해 자동화되고 반복 가능한 방식이 필요할 수 있습니다.

**서비스에 대한 작업**

서비스 관련 옵션은 다음 API가 포함됩니다.

- <a name="CreateService">검색 서비스 만들기</a>
- <a name="GetService">검색 서비스 가져오기</a>
- <a name="ListService">검색 서비스 나열</a>
- <a name="DeleteService">검색 서비스 삭제</a>
- <a name="UpdateService">검색 서비스 업데이트</a>


<a name="CreateService"></a>
###검색 서비스 만들기

**검색 서비스 만들기** 작업은 새 검색 서비스에 지정된 매개 변수를 프로비닝합니다. 이 API는 기존 서비스 정의를 업데이트하는 데 사용할 수도 있습니다.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### URI 매개 변수 요청

`subscriptionId`: 필수 Azure 사용자용 `subscriptionID`입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름은 소문자, 숫자 또는 대시만 사용해야 하고, 처음 두 문자 또는 마지막 문자에는 대시를 사용할 수 없으며, 연속적으로 대시를 사용할 수 없고, 길이가 2 ~ 15자여야 합니다. 모든 이름이 <name>.search.windows.net으로 끝나고 있기 때문에, 서비스 이름은 전체적으로 고유해야 합니다. 구독 및 리소스 그룹 내에서 또는 걸쳐 두 서비스의 이름이 같을 수 없습니다. 서비스 이름을 만든 후에는 변경할 수 없습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.


#### 요청 헤더

`Content-Type`: 필수 이 헤더를 application/json에 설정합니다.

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.


#### 요청 본문

{ "위치": "검색 서비스의 위치", "태그": { "key": "value", ... }, "속성": { "sku": { "name": "free | standard | standard2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

#### 요청 본문 매개 변수#

`location`: 필수 지원되고 등록된 Azure 지리적 영역(예: 미국 서부, 미국 동부, 동남 아시아 등) 중 하나입니다. 일단 리소스의 위치가 생성되면 변경할 수 없습니다.

`tags`: 선택 사항 리소스를 설명하는 키 값 쌍의 목록입니다. 이러한 태그는 리소스 그룹 간의 리소스 보기 및 그룹화에 사용할 수 있습니다. 리소스에 대해 최대 10개의 태그를 제공할 수 있습니다. 각 태그에는 128자 미만의 키와 256 자 미만의 값이 있어야 합니다.

`sku`: 필수 유효한 값은 `free` 및 `standard`입니다. `standard2`도 유효하지만 Microsoft 지원에 의해 Azure 구독에 대해 활성화된 경우에만 사용할 수 있습니다. `free`은(는) 공유 클러스터에서 서비스를 프로비저닝하고, `standard`은(는) 전용된 클러스터에서 서비스를 프로비저닝합니다. 무료 가격 책정 계층에 하나의 검색 서비스만 만들 수 있습니다. 표준 가격 책정 계층에 추가 서비스를 만들어야 합니다. 기본적으로 서비스는 하나의 파티션과 하나의 복제본으로 만들어집니다. 추가 파티션 및 복제본은 검색 단위별로 가격이 책정됩니다. 자세한 내용은 [한도 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요. 서비스 이름을 만든 후에는 `sku`을(를) 변경할 수 없습니다.

`replicaCount`: 선택 사항 기본값은 1입니다. 유효한 값은 1 ~ 6입니다. `sku`이(가) `standard`인 경우에만 유효합니다.

`partitionCount`: 선택 사항 기본값은 1입니다. 유효한 값은 1, 2, 3, 4, 6 또는 12입니다. `sku`이(가) `standard`인 경우에만 유효합니다.


### 응답

서비스 정의가 업데이트된 경우 HTTP 200 (OK)이 반환됩니다. 새 서비스가 만들어진 경우 HTTP 201 (Created)이 반환됩니다.


#### 응답 헤더

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.


#### 응답 본문 

HTTP 200 및 201의 경우, 응답 본문에는 서비스 정의가 포함됩니다.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


#### 응답 본문 요소

`id`: id는 이 검색 서비스에 대한 URL(호스트 이름/구성표 제외)입니다.

`name`: 검색 서비스의 이름입니다.

`location`: 지원되고 등록된 Azure 지리적 영역(예: 미국 서부, 미국 동부, 동남 아시아 등) 중 하나입니다.

`tags`: 리소스를 설명하는 키 값 쌍의 목록이며, 리소스 그룹 간의 리소스 보기 및 그룹화에 사용됩니다.

`sku`: 가격 책정 계층을 나타냅니다. 유효한 값은 다음과 같습니다.

- `free`: 공유 클러스터
- `standard`: 전용 클러스터
- `standard2`: Microsoft 지원 지침에 의해서만 사용합니다.

`replicaCount`: 서비스가 가질 수 있는 복제본 수를 나타냅니다. 유효한 값은 1 ~ 6입니다.

`partitionCount`: 서비스가 가질 수 있는 파티션 수를 나타냅니다. 유효한 값은 1, 2, 3, 4, 6 또는 12입니다.

`status`: 작업이 호출된 시간의 검색 서비스의 상태입니다. 유효한 값은 다음과 같습니다.

- `running`: 검색 서비스를 만듭니다.
- `provisioning`: 검색 서비스가 프로비저닝되고 있습니다.
- `deleting`: 검색 서비스가 삭제되고 있습니다.
- `degraded`: 검색 서비스가 저하됩니다. 이는 클러스터에 서비스가 올바르게 작동하지 못하도록 하거나 작동하도록 하는 오류가 발생한 경우에 발생할 수 있습니다.
- `disabled`: 검색을 사용할 수 없습니다. 이 상태에서 서비스는 모든 AP 요청을 거부합니다.
- `error`: 검색 서비스가 오류 상태입니다.

**참고**: 서비스가 `degraded`, `disabled` 또는 `error` 상태에 있는 경우 Azure 검색 팀이 문제의 원인을 적극적으로 조사하고 있음을 의미합니다. 이러한 상태에서도 전용 서비스는 프로비저닝된 검색 단위 수에 따라 여전히 요금을 부과할 수 있습니다.

`statusDetails`: 세부 정보 상태입니다.

`provisioningState`: 서비스 프로비저닝의 현재 상태를 나타냅니다. 유효한 값은 다음과 같습니다.

- `succeeded`: 프로비저닝이 성공적으로 수행되었습니다.
- `provisioning`: 서비스가 프로비저닝되고 있습니다.
- `failed`: 서비스가 프로비저닝되지 않았습니다.

프로비저닝은 서비스 용량을 설정하는 동안 발생하는 중간 상태입니다. 용량을 설정한 후 `provisioningState`은(는) "성공" 또는 "실패"로 변경합니다. 클라이언트 응용 프로그램은 작업이 완료되는 때를 확인하기 위해 **검색 서비스 가져오기** 작업을 사용하여 프로비저닝 상태를 폴링할 수 있습니다(권장 폴링 간격은 30초이며, 최대 1분임). 무료 서비스를 사용하는 경우 이 값은 서비스를 만드는 호출에서 바로 "성공"이 되려는 경향이 있습니다. 이는 무료 서비스가 이미 설정된 용량을 사용하기 때문입니다.

<a name="GetService"></a>
### 검색 서비스 가져오기

**검색 서비스 가져오기** 작업은 지정된 검색 서비스에 대한 속성을 반환합니다. 관리 키는 반환되지 않습니다. **관리 키 가져오기** 작업을 사용하여 관리 키를 검색하세요.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### 요청 URI

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

#### 요청 헤더

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.


#### 요청 본문

없음.


#### 응답 상태 코드

성공한 경우 HTTP 200 (OK)입니다.


#### 응답 헤더

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

#### 응답 본문

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### 응답 본문 요소

`id`: id는 이 검색 서비스에 대한 URL(호스트 이름/구성표 제외)입니다.

`name`: 검색 서비스의 이름입니다.

`location`: 리소스의 위치입니다. 이는 지원되고 등록된 Azure 지리적 영역(예: 미국 서부, 미국 동부, 동남 아시아 등) 중 하나가 됩니다.

`tags`: 태그는 리소스를 설명하는 키 값 쌍의 목록입니다. 이러한 태그는 리소스 그룹 간의 리소스 보기 및 그룹화에 사용할 수 있습니다.

`sku`: 가격 책정 계층을 나타냅니다. 유효한 값은 다음과 같습니다.

- `free`: 공유 클러스터
- `standard`: 전용 클러스터
- `standard2`: Microsoft 지원 지침에 의해서만 사용합니다.

`replicaCount`: 서비스가 가질 수 있는 복제본 수를 나타냅니다. 유효한 값은 1 ~ 6입니다.

`partitionCount`: 서비스가 가질 수 있는 파티션 수를 나타냅니다. 유효한 값은 1, 2, 3, 4, 6 또는 12입니다.

`status`: 작업이 호출된 시간의 검색 서비스의 상태입니다. 유효한 값은 다음과 같습니다.

- `running`: 검색 서비스를 만듭니다.
- `provisioning`: 검색 서비스가 프로비저닝되고 있습니다.
- `deleting`: 검색 서비스가 삭제되고 있습니다.
- `degraded`: 검색 서비스가 저하됩니다. 이는 클러스터에 서비스가 올바르게 작동하지 못하도록 하거나 작동하도록 하는 오류가 발생한 경우에 발생할 수 있습니다.
- `disabled`: 검색을 사용할 수 없습니다. 이 상태에서 서비스는 모든 AP 요청을 거부합니다.
- `error`: 검색 서비스가 오류 상태입니다.
 
**참고**: 서비스가 `degraded`, `disabled` 또는 `error` 상태에 있는 경우 Azure 검색 팀이 문제의 원인을 적극적으로 조사하고 있음을 의미합니다. 이러한 상태에서도 전용 서비스는 프로비저닝된 검색 단위 수에 따라 여전히 요금을 부과할 수 있습니다.
 
`statusDetails`: 세부 정보 상태입니다.

`provisioningState`: 유효한 값은 다음과 같습니다.

- `succeeded`: 프로비저닝이 성공했습니다.
- `provisioning`: 서비스가 프로비저닝되고 있습니다.
- `failed`: 프로비저닝이 실패했습니다.


<a name="ListService"></a>
### 검색 서비스 나열

**서비스 목록** 작업은 특정 리소스 그룹의 구독에서 모든 검색 서비스의 목록을 반환합니다. 이 작업에는 admin api 키를 제외한 서비스 정의를 반환합니다. **관리 키 가져오기** 작업을 사용하여 관리 키를 검색하세요.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28
    
#### URI 매개 변수 요청

`subscriptionId`: 필수 Azure 사용자용 `subscriptionID`입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

#### 요청 헤더

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

####요청 본문

없음.

####응답

성공한 경우 상태 코드는 HTTP 200 (OK)입니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.


####응답 본문 

응답 본문은 서비스의 목록이며, 각 서비스는 **검색 서비스 가져오기** 작업의 형식을 따르는 JSON 배열로 반환됩니다.

현재 버전이 페이징을 지원하지 않기 때문에 `nextLink` 필드는 항상 null입니다. 이후 호환성을 유지하기 위해 빈 값으로 반환됩니다.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
### 서비스 삭제

**서비스 삭제** 작업은 모든 인덱스 및 문서를 포함하여 검색 서비스 및 검색 데이터를 삭제합니다.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**참고:** 관리자와 개발자는 프로덕션 서버에서 응용 프로그램 데이터를 삭제하기 전에 백업하는 것이 익숙합니다. Azure 검색에는 백업 작업이 없습니다. 응용 프로그램의 기본 저장소로서 인덱스를 사용하는 경우, 외부적으로 저장할 수 있는 인덱스에 모든 데이터를 반환하기 위해 검색 작업을 사용해야 합니다.

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

###요청 헤더###

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

HTTP 200의 경우, 응답 본문은 비게 됩니다. 리소스가 존재하지 않는 경우 HTTP 200 (OK)은 올바른 응답입니다.

**검색 서비스 API 가져오기**를 사용하여 삭제 서비스의 상태를 폴링할 수 있습니다. 폴링 간격은 30초 ~ 1분이 좋습니다.

###응답 헤더###

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

없음.

<a name="UpdateService"></a>
### 업데이트 서비스 ##

**업데이트 서비스** 작업은 검색 서비스 구성을 변경합니다. 유효한 변경 내용에는 청구 가능 이벤트로서 서비스에 검색 단위를 추가하거나 제거하는 태그, 파티션 또는 복제본 횟수 변경이 포함됩니다. 기존 검색 모음을 저장하는 데 필요한 양 이하로 파티션을 줄이려 하는 경우 오류가 발생하고 작업이 차단됩니다. 서비스 토폴로지로 변경하는 작업은 시간이 걸릴 수 있습니다. 데이터 센터에서 데이터를 재배치할 때, 그리고 클러스터를 설정하거나 끊는 데는 시간이 걸립니다.

이름, 위치, sku를 변경할 수 없습니다. 이러한 속성을 변경하려면 새 서비스를 만들어야 합니다.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

또는 PUT를 사용할 수 있습니다.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

> [AZURE.NOTE] PUT을 사용하여 서비스 업데이트를 하는 경우 [서비스 만들기](#CreateService) 요청에 사용된 동일한 본문을 사용해야 합니다.

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자용 `subscriptionID`입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

###요청 헤더###

`Content-Type`: 필수 이 헤더를 application/json에 설정합니다.

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###요청 본문 매개 변수###

`tags`: 선택 사항 리소스를 설명하는 키 값 쌍의 목록입니다. 이러한 태그는 이 리소스(리소스 그룹 간)의 보기 및 그룹화에 사용할 수 있습니다. 리소스에 대해 최대 10개의 태그를 제공할 수 있습니다. 각 태그에는 128자 미만의 키와 256 자 미만의 값이 있어야 합니다.

`replicaCount`: 선택 사항 기본값은 1입니다. 유효한 값은 1 ~ 6입니다. `sku`이(가) `standard`인 경우에만 유효합니다.

`partitionCount`: 선택 사항 기본값은 1입니다. 유효한 값은 1, 2, 3, 4, 6 또는 12입니다. `sku`이(가) `standard`인 경우에만 유효합니다.

###응답###

작업이 성공한 경우 HTTP 200 (OK)이 반환됩니다. **검색 서비스 API 가져오기**를 사용하여 업데이트 서비스의 상태를 폴링할 수 있습니다. 폴링 간격은 30초 ~ 1분이 좋습니다.


### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

### 응답 본문 ###

응답 본문에는 업데이트된 서비스 정의가 포함됩니다. 예를 들어, **검색 서비스 API 가져오기**를 참조하세요.


<a name="KeyOps"></a>
## 키 작업

Azure 검색 서비스에 대한 인증은 검색 서비스 URL와 api 키 등 두 가지 정보가 필요합니다. api 키는 서비스가 만들어질 때 생성되며, 서비스가 프로비저닝된 후 요청 시 다시 생성할 수 있습니다. api 키에는 두 가지 유형이 있습니다.

- 관리 키: 모든 작업에 액세스를 부여합니다(서비스당 최대 2개).
- 쿼리 키: 쿼리 요청만 인증합니다(서비스당 최대 50개).

Azure 검색 서비스의 관리 및 쿼리 키를 프로그래밍 방식으로 관리하는 능력은 사용자 지정 도구를 구축하거나, 정기 보안 모범 사례에 따라 키를 정기적으로 롤오버하거나, 직원이 퇴사할 때 키를 롤오버하거나, 솔루션을 배포하는 데 스크립트 방식 또는 프로그래밍 방식을 사용하는 경우 서비스 프로비저닝 시 키를 생성 및 획득하는 방법을 제공합니다.

쿼리 키를 획득하고 만들고 삭제할 수 있습니다. 관리 키 작업은 기존 키 값 획득 및 다시 생성이 제한됩니다. 관리 키를 삭제하면 서비스는 영구적으로 잠기고 작업을 사용할 수 있게 됩니다.

키는 숫자와 대문자 문자의 임의 조합으로 구성된 문자열입니다. api 키는 만들어진 후 정기적으로 변경할 수 있는 서비스만 함께 사용할 수 있습니다(보안 모범 사례로서 키 롤오버 전략을 채택하는 경우).

api 키, 특히 관리 키는 중요한 데이터로서 처리해야 합니다. 관리 키를 획득한 사람은 인덱스에서 데이터를 삭제하거나 읽을 수 있습니다.

**키에 대한 작업**

키 관련 작업에는 다음과 같은 API가 포함됩니다.

- <a name="ListAdminKey">관리 키 나열</a>
- <a name="RegenAdminKey">관리 키 다시 생성</a>
- <a name="CreateQueryKey">쿼리 키 만들기</a>
- <a name="ListQueryKey">쿼리 키 나열</a>
- <a name="DeleteQueryKey">쿼리 키 삭제</a>


<a name="ListAdminKey"></a>
## 관리 키 나열 ##

**관리 키 나열** 작업은 지정된 검색 서비스에 대한 기본 및 보조 관리 키를 반환합니다. 이 작업이 읽기-쓰기 키를 반환하기 때문에 POST 메서드가 사용됩니다.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

관리 키는 서비스와 함께 만들어집니다. 항상 기본과 보조 두 개의 키가 있습니다. 이들 키를 다시 생성할 수 있지만 삭제할 수는 없습니다.

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

`listAdminKeys`: 필수 이 작업은 검색 서비스의 기본 및 보조 관리 키를 검색합니다.

###헤더 요청###

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

작업이 성공한 경우 HTTP 200 (OK)이 반환됩니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## 관리 키 다시 생성 ##

**관리 키 다시 생성** 작업은 기본 또는 보조 키를 삭제하고 다시 생성합니다. 한번에 하나의 키만 다시 생성할 수 있습니다. 키를 다시 생성하는 경우, 서비스에 대한 액세스를 어떻게 유지할지를 고려하세요. 보조 키는 기본 키를 롤오버하는 경우 사용할 수 있습니다. 모든 서비스에는 항상 두 키가 있습니다. 키를 다시 생성할 수 있지만 삭제할 수 없으며 키 없이 서비스를 실행할 수 없습니다.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.
	
`regenerateKey`: 필수 이 작업은 기본 또는 보조 관리 키를 다시 생성할지 지정합니다.

`keyKind`: 필수 어떤 키를 다시 생성할지 지정합니다. 유효한 값은 다음과 같습니다.

- `primary`
- `secondary`

###요청 헤더###

`Content-Type`: 필수 이 헤더를 application/json에 설정합니다.

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

작업이 성공한 경우 HTTP 200 (OK)이 반환됩니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###응답 본문 요소###

`primaryKey`: 다시 생성된 경우 기본 관리 키입니다.

`secondaryKey`: 다시 생성된 경우 보조 관리 키입니다.



<a name="CreateQueryKey"></a>
## 쿼리 키 만들기 ##

**쿼리 키 만들기** 작업은 검색 서비스에 대한 새로운 쿼리 키를 생성합니다. 서비스당 최대 50개의 쿼리 키를 만들 수 있습니다.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

`createQueryKey`: 필수 이 작업은 검색 서비스에 대한 쿼리 키를 만듭니다.

`name`: 필수 새 키의 이름입니다.

###요청 헤더###

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

작업이 성공한 경우 응답 상태 코드는 HTTP 200 (OK)입니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

    {
      "name": "name of key",
      "key": "api key"
    }


###응답 본문 요소###

`name`: 쿼리 키의 이름입니다.

`key`: 쿼리 키의 값입니다.

<a name="ListQueryKey"></a>
## 쿼리 키 나열 ##


**쿼리 키 나열** 작업은 지정된 검색 서비스에 대한 쿼리 키를 반환합니다. 쿼리 키는 쿼리 API(읽기 전용) 호출을 검색 서비스에 보내는 데 사용됩니다. 서비스당 최대 50개의 쿼리 키가 있을 수 있습니다.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.
	
`listQueryKeys`: 필수 이 동작은 검색 서비스에 대한 쿼리 키를 검색합니다.

###요청 헤더###

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

작업이 성공한 경우 응답 상태 코드로 HTTP 200 (OK)이 반환됩니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###응답 본문 요소###

`name`: 쿼리 키의 이름입니다.

`key`: 쿼리 키의 값입니다.


<a name="DeleteQueryKey"></a>
## 쿼리 키 삭제 ##

**쿼리 키 삭제** 작업은 지정된 쿼리 키를 삭제합니다. 쿼리 키는 선택 사항이며 읽기 전용 쿼리에 사용됩니다.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

관리 키와는 달리, 쿼리 키는 다시 생성되지 않습니다. 쿼리 키를 다시 생성하는 프로세스는 삭제한 다음 다시 생성하는 것입니다.

###URI 매개 변수 요청###

`subscriptionId`: 필수 Azure 사용자를 위한 구독 ID입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`resourceGroupName`: 필수 사용자의 구독 내에 있는 리소스 그룹의 이름입니다. Azure 리소스 관리자 API 또는 포털에서 이 값을 얻을 수 있습니다.

`serviceName`: 필수 지정된 리소스 그룹 내에 있는 검색 서비스의 이름입니다. 서비스 이름을 모르는 경우 검색 서비스 목록(Azure 검색 API)를 사용하 여 목록을 얻을 수 있습니다.

`api-version`: 필수 이 요청에 사용되는 프로토콜의 버전을 지정합니다. 현재 버전은 `2015-02-28`입니다.

`deleteQueryKey`: 필수 이 작업은 검색 서비스에 대한 기존 쿼리 키를 삭제합니다.

`key`: 필수 삭제할 키입니다.

###요청 헤더###

`x-ms-client-request-id`: 선택 사항 이 요청을 식별하는 클라이언트 생성 GUID 값입니다. 지정된 경우, 이 값은 요청을 매핑하는 방법으로 응답 메시지에 포함됩니다.

###요청 본문###

없음.

###응답###

성공한 경우 응답 상태 코드는 HTTP 200 (OK)입니다.

### 응답 헤더 ###

`Content-Type`: 이 헤더는 항상 application/json으로 설정됩니다.

`x-ms-request-id`: 서비스에 의해 생성된 현재 작업에 대한 고유 식별자입니다.

###응답 본문###

없음.

<!---HONumber=AcomDC_0914_2016-->