<properties
   pageTitle="Azure 검색 서비스 REST API 버전 2015-02-28-Preview | Microsoft Azure | Azure 검색 미리 보기 API"
   description="Azure 검색 서비스 REST API 버전 2015-02-28-Preview에는 자연어 분석기 및 moreLikeThis 검색과 같은 실험적 기능이 포함되어 있습니다."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# Azure 검색 서비스 REST API: 버전 2015-02-28-Preview

이 문서는 `api-version=2015-02-28-Preview`에 대한 참조 설명서입니다. 이 미리 보기는 다음과 같은 실험적 기능을 제공하여 현재 일반적으로 사용할 수 있는 버전인 [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx)을 확장합니다.

- [문서 검색](#SearchDocs) API의 `moreLikeThis` 쿼리 매개 변수. 이 매개 변수는 다른 특정 문서와 관련된 다른 문서를 찾습니다.

`2015-02-28-Preview` REST API의 몇 가지 추가 파트는 별도로 설명됩니다. 내용은 다음과 같습니다.

- [점수 매기기 프로필](search-api-scoring-profiles-2015-02-28-preview.md)
- [인덱서](search-api-indexers-2015-02-28-preview.md)

Azure 검색 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

## 이 문서의 API

Azure 검색 서비스 API는 API 작업을 위한 두 URL 구문, 즉 simple 및 OData(자세한 내용은 [OData(Azure 검색 API)](http://msdn.microsoft.com/library/azure/dn798932.aspx)를 참조하세요). 다음 목록에서는 간단한 구문을 보여 줍니다.

[인덱스 만들기](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[인덱스 업데이트](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[인덱스 가져오기](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[인덱스 나열](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[인덱스 통계 가져오기](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[테스트 분석기](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[인덱스 삭제](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[인덱스 내에서 데이터 추가, 삭제 및 업데이트](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[문서 검색](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[문서 조회](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[문서 수 계산](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[제안](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## 인덱스 작업

Azure 검색 서비스에서 간단한 HTTP 요청(POST, GET, PUT, DELETE)을 통해 지정된 인덱스 리소스에 대해 인덱스를 만들고 관리할 수 있습니다. 인덱스를 만들려면 먼저 인덱스 스키마를 설명하는 JSON 문서에 대해 POST를 수행합니다. 스키마는 인덱스의 필드, 필드의 데이터 형식 및 필드 사용 방법(예: 전체 텍스트 검색, 필터, 정렬 또는 패싯)을 정의합니다. 또한 인덱스의 동작을 구성하는 기타 특성과 점수 매기기 프로필 및 확인기도 정의합니다.

다음 예제에서는 Description 필드가 두 언어로 정의된 호텔 정보를 검색하는 데 사용되는 스키마를 보여 줍니다. 특성이 필드 사용 방식을 제어하는 방법을 살펴보세요. 예를 들어 `hotelId`는 문서 키(`"key": true`)로 사용되며 전체 텍스트 검색에서 제외됩니다(`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

인덱스를 만든 후에는 인덱스를 채울 문서를 업로드합니다. 이 작업을 수행하는 다음 단계는 [문서 추가 또는 업데이트](#AddOrUpdateDocuments)를 참조하세요.

Azure 검색의 인덱싱을 소개하는 비디오는 [Azure 검색에 대한 Channel 9의 Cloud Cover 에피소드](http://go.microsoft.com/fwlink/p/?LinkId=511509)를 참조하세요.


<a name="CreateIndex"></a>
## 인덱스 만들기

인덱스는 Azure 검색에서 문서를 구성하고 검색하는 데 기본적으로 사용되며, 데이터베이스에서 테이블이 레코드를 구성하는 방식과 비슷하게 작동합니다. 각 인덱스는 모두 인덱스 스키마(필드 이름, 데이터 형식, 속성)를 준수하는 문서 컬렉션을 포함할 뿐 아니라, 기타 검색 동작을 정의하는 추가 구문(확인기, 점수 매기기 프로필, CORS 옵션)도 지정합니다.

HTTP POST 또는 PUT 요청을 사용하여 Azure 검색 서비스 내에서 새 인덱스를 만들 수 있습니다. 요청 본문은 인덱스 및 구성 정보를 지정하는 JSON 스키마입니다.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 인덱스 이름을 지정할 수 있습니다. 인덱스가 없으면 인덱스가 만들어집니다.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

인덱스를 만들면 저장되고 검색 작업에서 사용되는 문서의 구조가 결정됩니다. 인덱스에 데이터를 입력하는 작업은 별도로 수행해야 합니다. 이 단계에는 [인덱서](https://msdn.microsoft.com/library/azure/mt183328.aspx)(지원되는 데이터 원본에 사용 가능)를 사용하거나 [문서 추가, 업데이트 또는 삭제](https://msdn.microsoft.com/library/azure/dn798930.aspx) 작업을 수행할 수 있습니다. 문서를 게시하면 반전된 인덱스가 생성됩니다.

**참고**: 허용되는 최대 인덱스 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱스를 3개까지 사용할 수 있으며 표준 서비스에서는 검색 서비스당 인덱스 50개를 만들 수 있습니다. 자세한 내용은 [한도 및 제약 조건](http://msdn.microsoft.com/library/azure/dn798934.aspx)을 참조하세요.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. POST 또는 PUT 메서드를 사용하여 **인덱스 만들기** 요청을 생성할 수 있습니다. POST를 사용할 때는 인덱스 스키마 정의와 함께 요청 본문에 인덱스 이름을 제공해야 합니다. PUT을 사용하는 경우 인덱스 이름이 URL에 포함됩니다. 인덱스가 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

인덱스 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 인덱스 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.

`api-version`은 필수 사항입니다. 사용 가능한 버전 목록은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는
- 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 만들기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

<a name="RequestData"></a> **요청 본문 구문**

요청 본문에는 이 인덱스에 공급할 문서 내의 데이터 필드 목록, 데이터 형식, 특성 및 쿼리 시 일치하는 문서의 점수를 매기는 데 사용되는 선택적 점수 매기기 프로필 목록이 들어 있는 스키마 정의가 포함됩니다.

POST 요청의 경우에는 요청 본문에 인덱스 이름을 지정해야 합니다.

인덱스에는 key 필드를 하나만 포함할 수 있습니다. 이 필드는 문자열 필드여야 하며, 인덱스와 함께 저장되는 각 문서의 고유 식별자를 나타냅니다.

인덱스의 주요 부분은 다음과 같습니다.

- `name`
- `fields`: 이름, 데이터 형식 및 해당 필드에 대해 허용되는 작업을 정의하는 속성을 포함하여 이 인덱스에 공급할 필드입니다.
- `suggesters`: 자동 완성 또는 미리 입력 쿼리에 사용됩니다.
- `scoringProfiles`: 사용자 지정 검색 점수 순위에 사용됩니다. 자세한 내용은 [점수 매기기 프로필 추가](https://msdn.microsoft.com/library/azure/dn798928.aspx)를 참조하세요.
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters`: 문서/쿼리를 인덱싱 가능/검색 가능 토큰으로 구분하는 방법을 정의하는 데 사용됩니다. 자세한 내용은 [Azure 검색의 분석](https://aka.ms//azsanalysis)을 참조하세요.
- `defaultScoringProfile`: 기본 점수 매기기 동작을 덮어쓰는 데 사용됩니다.
- `corsOptions`: 인덱스에 대한 교차 원본 쿼리를 허용합니다.

요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,		      
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
	  "analyzers":(optional)[ ... ],
	  "charFilters":(optional)[ ... ],
	  "tokenizers":(optional)[ ... ],
	  "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**인덱스 특성**

인덱스를 만들 때 설정할 수 있는 특성은 다음과 같습니다. 점수 매기기 및 점수 매기기 프로필에 대한 자세한 내용은 [점수 매기기 프로필 추가](https://msdn.microsoft.com/library/azure/dn798928.aspx)를 참조하세요.

`name` - 필드의 이름을 설정합니다.

`type` - 필드의 데이터 형식을 설정합니다. 지원되는 형식 목록은 [지원되는 데이터 형식](#DataTypes)을 참조하세요.

`searchable` - 필드를 전체 텍스트 검색 가능 필드로 표시합니다. 이렇게 표시된 필드의 경우 인덱싱 중에 단어 분리 등의 분석이 수행됩니다. `searchable` 필드를 "sunny day" 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 "sunny"와 "day"로 분할됩니다. 따라서 이러한 용어에 대한 전체 텍스트 검색을 수행할 수 있습니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필드는 기본적으로 `searchable`로 설정됩니다. 다른 형식의 필드는 `searchable`로 설정할 수 없습니다.

  - **참고**: Azure 검색에서는 전체 텍스트 검색을 위한 필드 값의 토큰화된 버전을 추가로 저장하므로 `searchable` 필드는 인덱스에서 추가적인 공간을 사용합니다. 검색에 필드를 포함할 필요가 없으며 인덱스의 공간을 저장하려는 경우에는 `searchable`을 `false`로 설정합니다.

`filterable` - `$filter` 쿼리에서 필드를 참조할 수 있습니다. `filterable`은 문자열 처리 방법에서 `searchable`과 차이가 있습니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`이며 `filterable`인 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 `f`를 "sunny day"로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. 기본적으로 모든 필드는 `filterable`로 설정됩니다.

`sortable` - 시스템에서는 기본적으로 점수를 기준으로 결과를 정렬하지만 사용자는 대부분의 경우 문서의 필드를 기준으로 결과를 정렬합니다. 형식이 `Collection(Edm.String)`인 필드는 `sortable`로 설정할 수 없습니다. 기타 모든 필드는 기본적으로 `sortable`로 설정됩니다.

`facetable` - 일반적으로 범주별 적중 횟수를 포함하는 검색 결과 표시에 사용됩니다. 디지털 카메라를 검색한 다음 브랜드, 메가픽셀, 가격 등을 기준으로 적중 항목을 표시하는 경우를 예로 들 수 있습니다. 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. 기타 모든 필드는 기본적으로 `facetable`로 설정됩니다.

  - **참고**: 형식이 `Edm.String`이며 `filterable`, `sortable` 또는 `facetable`로 설정된 필드의 최대 길이는 32KB입니다. 이러한 필드는 단일 검색 용어로 처리되는데 Azure 검색에서 용어의 최대 길이는 32KB이기 때문입니다. 단일 문자열 필드에 이보다 많은 텍스트를 저장해야 하는 경우에는 인덱스 정의에서 `filterable`, `sortable` 및 `facetable`을 명시적으로 `false`로 설정해야 합니다.

  - **참고**: 위의 모든 특성(`searchable`, `filterable`, `sortable`, `facetable`)이 `true`로 설정되어 있지 않은 필드는 반전된 인덱스에서 제외됩니다. 쿼리에서 사용되지는 않지만 검색 결과에는 필요한 필드의 경우 이 옵션을 사용하면 유용합니다. 이러한 필드를 인덱스에서 제외하면 성능이 향상됩니다.

`key` - 필드가 인덱스 내의 문서에 대한 고유 식별자를 포함하는 것으로 표시합니다. 정확히 하나의 필드를 `key` 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다. 키 필드를 사용하면 [문서 조회](#LookupAPI)를 통해 문서를 직접 조회할 수 있습니다.

`retrievable` - 검색 결과에서 필드를 반환할 수 있는지 여부를 설정합니다. 이익 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `key` 필드의 경우 이 특성을 `true`로 설정해야 합니다.

`analyzer` - 필드의 검색 시간 및 인덱싱 시간에 사용할 분석기 이름을 설정합니다. 허용되는 값은 [분석기](https://msdn.microsoft.com/library/mt605304.aspx)를 참조하세요. 이 옵션은 `searchable` 필드에만 사용할 수 있으며 `searchAnalyzer` 또는 `indexAnalyzer`와 함께 설정할 수 없습니다. 필드에 대해 분석기를 선택한 후에는 변경할 수 없습니다.

`searchAnalyzer` - 필드의 검색 시간에 사용되는 분석기 이름을 설정합니다. 허용되는 값은 [분석기](https://msdn.microsoft.com/library/mt605304.aspx)를 참조하세요. 이 옵션은 `searchable` 필드에만 사용할 수 있습니다. `indexAnalyzer`와 함께 설정해야 하며 `analyzer` 옵션과 함께 설정할 수 없습니다. 이 분석기는 기존 필드에서 업데이트할 수 있습니다.

`indexAnalyzer` - 필드의 인덱싱 시간에 사용되는 분석기 이름을 설정합니다. 허용되는 값은 [분석기](https://msdn.microsoft.com/library/mt605304.aspx)를 참조하세요. 이 옵션은 `searchable` 필드에만 사용할 수 있습니다. `searchAnalyzer`와 함께 설정해야 하며 `analyzer` 옵션과 함께 설정할 수 없습니다. 필드에 대해 분석기를 선택한 후에는 변경할 수 없습니다.

`suggesters` - 검색 모드 및 제안 내용의 원본인 필드를 설정합니다. 자세한 내용은 [확인기](#Suggesters)를 참조하세요.

`scoringProfiles` - 검색 결과에서 더 위쪽에 표시할 항목을 제어할 수 있는 사용자 지정 점수 매기기 동작을 정의합니다. 점수 매기기 프로필은 필드 가중치와 함수로 구성됩니다. 점수 매기기 프로필에서 사용되는 특성에 대한 자세한 내용은 [점수 매기기 프로필 추가](https://msdn.microsoft.com/library/azure/dn798928.aspx)를 참조하세요.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a> **언어 지원**

검색 가능 필드에서는 대개 단어 분리, 텍스트 정규화, 용어 필터링 등을 포함하는 분석이 수행됩니다. 기본적으로 Azure 검색에서는 [Apache Lucene 표준 분석기](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html)를 사용하여 검색 가능 필드를 분석합니다. 이 분석기는 ["유니코드 텍스트 구분"](http://unicode.org/reports/tr29/) 규칙에 따라 텍스트를 요소로 분리합니다. 또한 표준 분석기에서는 모든 문자를 소문자 형식으로 변환합니다. 인덱싱 및 쿼리 처리 중에는 인덱싱된 문서와 검색 용어 둘 다에 대해 분석을 수행합니다.

Azure 검색은 다양한 언어를 지원합니다. 각 언어를 사용하려면 지정된 언어의 특성을 고려할 수 있는 비표준 텍스트 분석기가 필요합니다. Azure 검색에서는 다음 두 가지 유형의 분석기를 제공합니다.

- Lucene에서 지원하는 35개의 분석기
- Office 및 Bing에서 사용되는 Microsoft 소유 자연어 처리 기술로 지원되는 50개의 분석기

일부 개발자는 보다 친숙하고 간단한 Lucene의 오픈 소스 솔루션을 선호할 수 있습니다. Lucene 분석기가 보다 빠르지만 Microsoft 분석기에는 분류 정리, 단어 분해(독일어, 덴마크어, 네덜란드어, 스웨덴어, 노르웨이어, 에스토니아어, 핀란드어, 헝가리어, 슬로바키아어와 같은 언어에서) 및 엔터티 인식(URL, 전자 메일, 날짜, 숫자)과 같은 고급 기능이 있습니다. 가능한 경우 Microsoft 분석기와 Lucene 분석기를 비교하여 어떤 것이 더 적합한지 결정해야 합니다.

***비교 방법***

Lucene 영어 분석기는 표준 분석기를 확장합니다. 이 분석기는 단어에서 소유격(후행 's)을 제거하고, [Porter 형태소 분석 알고리즘](http://tartarus.org/~martin/PorterStemmer/)에 따라 형태소 분석을 적용하며, 영어의 [중지 단어](http://en.wikipedia.org/wiki/Stop_words)를 제거합니다.

반면 Microsoft 분석기는 형태소 분석 대신 분류 정리를 수행합니다. 즉, 어형이 변화되고 불규칙한 단어 형태 관련 검색 결과에서 더 정확한 결과로 처리할 수 있습니다.(자세한 내용은 [Azure 검색 MVA 프레젠테이션](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps)의 모듈 7 보기)

언어에 따라 Microsoft 분석기를 사용한 인덱싱은 Lucene보다 평균 두세 배 정도 더 느립니다. 검색 성능은 평균 크기 쿼리에 크게 영향을 받지 않아야 합니다.

***구성***

인덱스 정의의 각 필드에 대해 `analyzer` 속성을 언어 및 공급업체를 지정하는 분석기 이름으로 설정할 수 있습니다. 해당 필드를 인덱싱 및 검색하는 경우 동일한 분석기를 적용합니다. 예를 들어 영어, 프랑스어, 스페인어 호텔 설명을 표시하는 개별 필드를 같은 인덱스에서 나란히 표시할 수 있습니다. ['searchFields' 쿼리 매개 변수](#SearchQueryParameters)를 사용하여 쿼리에서 검색하는 특정 언어 필드를 지정합니다. `analyzer` 속성을 포함하는 쿼리 예제는 [문서 검색](#SearchDocs)에서 검토할 수 있습니다.

***분석기 목록***

다음은 Lucene 및 Microsoft 분석기 이름으로 지원되는 언어의 목록입니다.

<table style="font-size:12">
    <tr>
		<th>언어</th>
		<th>Microsoft 분석기 이름</th>
		<th>Lucene 분석기 이름</th>
	</tr>
    <tr>
		<td>아랍어</td>
		<td>ar.microsoft</td>
		<td>ar.lucene</td>		
	</tr>
    <tr>
    	<td>아르메니아어</td>
		<td></td>
    	<td>hy.lucene</td>
  	</tr>
    <tr>
		<td>벵골어</td>
		<td>bn.microsoft</td>
		<td></td>
	</tr>
  	<tr>
    	<td>바스크어</td>
		<td></td>
    	<td>eu.lucene</td>
    </tr>
  	<tr>
 		<td>불가리아어</td>
		<td>bg.microsoft</td>
    	<td>bg.lucene</td>
  	</tr>
  	<tr>
    	<td>카탈로니아어</td>
    	<td>ca.microsoft</td>
		<td>ca.lucene</td>  		
  	</tr>
    <tr>
		<td>중국어 간체</td>
		<td>zh-Hans.microsoft</td>
		<td>zh-Hans.lucene</td>		
	</tr>
    <tr>
		<td>중국어 번체</td>
		<td>zh-Hant.microsoft</td>
		<td>zh-Hant.lucene</td>		
	<tr>
    <tr>
		<td>크로아티아어</td>
		<td>hr.microsoft</td>
		<td/></td>
	</tr>
    <tr>
		<td>체코어</td>
		<td>cs.microsoft</td>
		<td>cs.lucene</td>		
	</tr>    
    <tr>
		<td>덴마크어</td>
		<td>da.microsoft</td>
		<td>da.lucene</td>		
	</tr>    
    <tr>
		<td>네덜란드어</td>
		<td>nl.microsoft</td>
		<td>nl.lucene</td>	
	</tr>    
    <tr>
		<td>영어</td>		
		<td>en.microsoft</td>
		<td>en.lucene</td>		
	</tr>
    <tr>
		<td>에스토니아어</td>
		<td>et.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>핀란드어</td>
		<td>fi.microsoft</td>
		<td>fi.lucene</td>		
	</tr>    
    <tr>
		<td>프랑스어</td>
		<td>fr.microsoft</td>
		<td>fr.lucene</td>		
	</tr>
    <tr>
    	<td>갈리시아어</td>
	    <td></td>
		<td>gl.lucene</td>    	
  	</tr>
    <tr>
		<td>독일어</td>
		<td>de.microsoft</td>
		<td>de.lucene</td>		
	</tr>
    <tr>
		<td>그리스어</td>
		<td>el.microsoft</td>
		<td>el.lucene</td>		
	</tr>
    <tr>
		<td>구자라트어</td>
		<td>gu.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>히브리어</td>
		<td>he.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>힌디어</td>
		<td>hi.microsoft</td>
		<td>hi.lucene</td>		
	</tr>
    <tr>
		<td>헝가리어</td>		
		<td>hu.microsoft</td>
		<td>hu.lucene</td>
	</tr>
    <tr>
		<td>아이슬란드어</td>
		<td>is.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>인도네시아어(공용어)</td>
		<td>id.microsoft</td>
		<td>id.lucene</td>		
	</tr>
    <tr>
    	<td>아일랜드어</td>
		<td></td>
      	<td>ga.lucene</td>
    </tr>
    <tr>
		<td>이탈리아어</td>
		<td>it.microsoft</td>
		<td>it.lucene</td>		
	</tr>
    <tr>
		<td>일본어</td>
		<td>ja.microsoft</td>
		<td>ja.lucene</td>
		
	</tr>
    <tr>
		<td>카나다어</td>
		<td>ka.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>한국어</td>
		<td>ko.microsoft</td>
		<td>ko.lucene</td>
	</tr>
    <tr>
		<td>라트비아어</td>		
		<td>lv.microsoft</td>
		<td>lv.lucene</td>	
	</tr>
    <tr>
		<td>리투아니아어</td>
		<td>lt.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>말라얄람어</td>
		<td>ml.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>말레이어(라틴 문자)</td>
		<td>ms.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>마라티어</td>
		<td>mr.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>노르웨이어</td>
		<td>nb.microsoft</td>
		<td>no.lucene</td>		
	</tr>
  	<tr>
    	<td>페르시아어</td>
		<td></td>
		<td>fa.lucene</td>    	
  	</tr>
    <tr>
		<td>폴란드어</td>
		<td>pl.microsoft</td>
		<td>pl.lucene</td>		
	</tr>
    <tr>
		<td>포르투갈어(브라질)</td>
		<td>pt-Br.microsoft</td>
		<td>pt-Br.lucene</td>		
	</tr>
    <tr>
		<td>포르투갈어(포르투갈)</td>
		<td>pt-Pt.microsoft</td>		
		<td>pt-Pt.lucene</td>
	</tr>
    <tr>
		<td>펀잡어</td>
		<td>pa.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>루마니아어</td>
		<td>ro.microsoft</td>
		<td>ro.lucene</td>
	</tr>
    <tr>
		<td>러시아어</td>
		<td>ru.microsoft</td>
		<td>ru.lucene</td>	
	</tr>
    <tr>
		<td>세르비아어(키릴자모)</td>
		<td>sr-cyrillic.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>세르비아어(라틴 문자)</td>
		<td>sr-latin.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>슬로바키아어</td>
		<td>sk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>슬로베니아어</td>
		<td>sl.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>스페인어</td>
		<td>es.microsoft</td>
		<td>es.lucene</td>
	</tr>
    <tr>
		<td>스웨덴어</td>
		<td>sv.microsoft</td>
		<td>sv.lucene</td>
	</tr>

    <tr>
		<td>타밀어</td>
		<td>ta.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>텔루구어</td>
		<td>te.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>태국어</td>
		<td>th.microsoft</td>
		<td>th.lucene</td>
	</tr>
    <tr>
		<td>터키어</td>
		<td>tr.microsoft</td>
		<td>tr.lucene</td>		
	</tr>
    <tr>
		<td>우크라이나어</td>
		<td>uk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>우르두어</td>
		<td>ur.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>베트남어</td>
		<td>vi.microsoft</td>
		<td></td>
	</tr>
	<td colspan="3">Azure 검색에서는 추가적으로 언어에 관계없는 분석기 구성을 제공합니다.</td>
    <tr>
		<td>표준 ASCII 접기</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>유니코드 텍스트 구분(표준 토크나이저)입니다.</li>
			<li>ASCII 접기 필터 - 처음 127개 ASCII 문자 집합에 속하지 않는 유니코드 문자를 해당 ASCII 문자로 변환합니다. 분음 부호(악센트)를 제거하는 데 유용합니다.</li>
		</ul>
		</td>
	</tr>
</table>

이름에 <i>lucene</i> 주석이 포함된 모든 분석기는 [Apache Lucene 언어 분석기](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html)를 통해 구동됩니다. ASCII 접기 필터에 대한 자세한 내용은 [여기](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)에서 확인할 수 있습니다.

**확인기**

`suggester`은(는) 검색에 자동 완성을 지원하기 위해 사용되는 인덱스에 있는 필드를 정의합니다. 일반적으로는 사용자가 검색 쿼리를 입력하는 동안 부분 검색 문자열이 [제안 API](#Suggestions)로 전송되며 API는 제안 구 집합을 반환합니다. 인덱스에서 정의하는 확인기는 미리 입력 검색 용어를 작성하는 데 사용되는 필드를 결정합니다. 구성 세부 정보는 [확인기](#Suggesters)를 참조하세요.

**점수 매기기 프로필**

`scoringProfile`은(는) 검색 결과에서 더 위쪽에 표시할 항목을 제어할 수 있는 사용자 지정 점수 매기기 동작을 정의합니다. 점수 매기기 프로필은 필드 가중치와 함수로 구성됩니다. 사용하려면 쿼리 문자열에서 이름별로 프로필을 지정합니다.

기본 점수 매기기 프로필은 결과 집합에 있는 모든 항목에 대한 검색 점수를 계산하기 위해 백그라운드에서 작동합니다. 이름이 지정되지 않은 내부 점수 매기기 프로필을 사용할 수 있습니다. 또는 사용자 지정 프로필이 쿼리 문자열에서 지정되지 않을 때마다 호출되는 기본으로 사용자 지정 프로필을 사용하도록 `defaultScoringProfile`을(를) 설정합니다.

세부 정보는 [검색 인덱스(Azure 검색 서비스 REST API)에 점수 매기기 프로필 추가](search-api-scoring-profiles-2015-02-28-preview.md)를 참조하세요.

**CORS 옵션**

브라우저에서는 모든 교차 원본 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 교차 원본 쿼리를 허용하려면 `corsOptions` 특성을 설정하여 CORS(교차 원본 자원 공유)를 사용하도록 설정합니다. 보안상 쿼리 API에서만 CORS가 지원됩니다. CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

- `allowedOrigins`(필수): 인덱스에 대한 액세스 권한을 부여할 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://fully-qualified-domain-name:port` 형식이지만 포트는 대개 생략됩니다. 자세한 내용은 [이 문서](http://go.microsoft.com/fwlink/?LinkId=330822)를 참조하세요.
 - 모든 원본에 대한 액세스를 허용하려면 `allowedOrigins` 배열에서 `*`를 단일 항목으로 포함합니다. **프로덕션 검색 서비스에서는 이러한 방식을 사용하지 않는 것이 좋습니다**. 개발 또는 디버깅용으로는 이 기능이 유용할 수 있습니다.
- `maxAgeInSeconds`(선택): 브라우저는 이 값을 사용하여 CORS 실행 전 응답을 캐시할 기간(초)을 결정합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

<a name="CreateUpdateIndexExample"></a> **요청 본문 예제**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**응답**

요청이 성공적으로 실행되면 “201 생성됨”이 반환됩니다.

기본적으로 응답 본문에는 생성된 인덱스 정의에 대 한 JSON 포함됩니다. 그러나 `Prefer` 요청 헤더를 `return=minimal`로 설정하면 응답 본문은 비어 있게 되며 성공 상태 코드는 "201 생성됨"이 아닌 "204 콘텐츠 없음"으로 표시됩니다. 인덱스를 만드는 데 사용한 방법(PUT 또는 POST)에 상관없이 결과는 동일합니다.

**설명**

현재는 인덱스 스키마 업데이트가 제한적으로 지원됩니다. 필드 형식을 변경하는 등 인덱싱을 다시 수행해야 하는 모든 스키마 업데이트는 현재 지원되지 않습니다. 기존 필드를 변경하거나 삭제할 수는 없지만 언제든지 기존 인덱스에 새 필드를 추가할 수는 있습니다. 새 필드를 추가하면 인덱스의 모든 기존 문서에서 해당 필드에 null 값이 자동으로 포함됩니다. 새 문서를 인덱스에 추가할 때까지는 저장소 공간이 추가로 사용되지 않습니다.

<a name="Suggesters"></a>
## 확인기

Azure 검색에서 제안 기능은 검색 상자에 입력한 부분 문자열 입력에 대한 응답에 잠재적인 검색 용어 목록을 제공하는 미리 입력 또는 자동 완성 쿼리 기능입니다. 상용 웹 검색 엔진 사용 시 쿼리 제안을 보았을 것입니다. Bing에 ".NET"를 입력하면 ".NET 4.5", ".NET Framework 3.5" 등에 대한 용어 목록이 생성됩니다. 검색 서비스 REST API 사용 시, 사용자 지정 Azure 검색 응용 프로그램에서 제안 사항을 구현하려면 다음이 필요합니다.

- 이름, 검색 모드를 제공하는 인덱스에 **확인기** 구문을 추가하고 미리 입력이 호출될 필드 목록을 추가하여 제안을 사용하도록 설정합니다. 예를 들어, 원본 필드로 "cityName"을 지정하는 경우, 부분 검색 문자열인 "Sea"를 입력하면 사용자에게 쿼리 제안 사항으로 "Seattle", "Seaside" 및 "Seatac"(세 곳 모두 실제 도시 이름)이 제공됩니다.

- 응용 프로그램 코드에서 [제안 사항 API](#Suggestions)를 호출하여 제안 사항을 호출합니다. 일반적으로는 사용자가 검색 쿼리를 입력하는 동안 부분 검색 문자열이 서비스에 전송되며 이 API는 제안 구 집합을 반환합니다.

이 문서에서는 **확인자**를 구성하는 방법에 대해 설명합니다. 확인자가 사용되는 방법에 대한 자세한 내용은 [제안 사항 API](#Suggestions)를 참조할 수도 있습니다.

**사용 현황**

`Suggesters`은(는) 인덱스에서 만들어지며 광범위한 용어나 구보다는 특정 문서를 제안하는 데 사용하는 경우 가장 효율적입니다. 확인기를 사용하기에 적합한 필드는 제목, 이름 및 항목을 식별할 수 있는 비교적 짧은 기타 구입니다. 반면 범주/태그 등의 반복 필드나 설명/주석 등의 매우 긴 필드는 확인기를 사용하기에 덜 적합합니다.

인덱스 정의의 일부로 단일 확인기를 `suggesters` 컬렉션에 추가할 수 있습니다. 확인기를 정의하는 속성은 다음과 같습니다.

- `name`: 확인기의 이름입니다. `suggest` API를 호출할 때 suggester의 이름을 사용합니다.
- `searchMode`: 제안 가능한 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.
- `sourceFields`: 제안 내용의 원본인 필드 하나 이상이 포함된 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 또한 사용자 지정 언어 분석기가 설정되지 않은 필드만 사용할 수 있습니다.

**확인기 예제**

확인기는 인덱스의 일부입니다. 현재 버전에서 하나의 확인기는 필드 컬렉션 및 `scoringProfiles`와(과) 함께 `suggesters` 컬렉션에만 존재할 수 있습니다.

		{
		  "name": "hotels",
		  "fields": [
		     . . .
		   ],
		  "suggesters": [
		    {
		    "name": "sg",
		    "searchMode": "analyzingInfixMatching",
		    "sourceFields: ["hotelName", "category"]
		    }
		  ],
		  "scoringProfiles": [
		     . . .
		  ]
		}

> [AZURE.NOTE]  Azure 검색의 공개 미리 보기 버전을 사용하는 경우에는 3~25자 사이의 짧은 문자열에 대해서만 접두사 제안을 지원했던 이전 부울 속성 `"suggestions": false` 대신 `suggesters`가 사용됩니다. 이 속성 대신 제공되는 `suggesters`는 필드 내용 시작 부분이나 중간에서 일치하는 용어를 찾는 중위 일치 기능을 지원하므로 검색 문자열을 잘못 입력해도 올바른 결과가 반환될 확률이 높아집니다. 일반적으로 사용 가능한 릴리스를 포함하여 이것이 현재 제안 API의 유일한 구현입니다. `api-version=2014-07-31-Preview`에 도입되었던 이전 `suggestions` 속성도 해당 버전에서는 계속 작동하지만 Azure 검색의 `2015-02-28` 이상 버전에서는 작동하지 않습니다.

<a name="UpdateIndex"></a>
## 인덱스 업데이트

HTTP PUT 요청을 사용하여 Azure 검색 내에서 기존 인덱스를 업데이트할 수 있습니다. 수행 가능한 업데이트 작업으로는 기존 스키마에 새 필드 추가, CORS 옵션 수정, 점수 매기기 프로필 수정 등이 있습니다. 자세한 내용은 [점수 매기기 프로필 추가](https://msdn.microsoft.com/library/azure/dn798928.aspx)를 참조하세요. 요청 URI에서 업데이트할 인덱스의 이름을 지정합니다.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**중요:** 인덱스 스키마 업데이트에 대한 지원은 검색 인덱스를 다시 작성하지 않아도 되는 작업으로 제한됩니다. 필드 형식을 변경하는 등 인덱싱을 다시 수행해야 하는 모든 스키마 업데이트는 현재 지원되지 않습니다. 기존 필드를 변경하거나 삭제할 수는 없지만 언제든지 새 필드를 추가할 수는 있습니다. 이는 `suggesters`에도 적용됩니다. 필드를 추가할 때 suggester에 새 필드를 추가할 수 있지만 `suggesters`에서 필드를 제거하거나 `suggesters`에 기존 필드를 추가할 수는 없습니다.

새 필드를 인덱스에 추가하면 인덱스의 모든 기존 문서에서 해당 필드에 null 값이 자동으로 포함됩니다. 새 문서를 인덱스에 추가할 때까지는 저장소 공간이 추가로 사용되지 않습니다.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 업데이트** 요청은 HTTP PUT을 사용하여 생성됩니다. PUT을 사용하는 경우 인덱스 이름이 URL에 포함됩니다. 인덱스가 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

인덱스 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 인덱스 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 업데이트** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문 구문**

기존 인덱스를 업데이트할 때 본문은 원래 스키마 정의와 새로 추가하는 필드뿐만 아니라 수정된 점수 매기기 프로필, suggesters 및 CORS 옵션(있는 경우)도 포함해야 합니다. 점수 매기기 프로필과 CORS 옵션을 수정하지 않는 경우에는 인덱스를 만들 때의 원래 값을 포함해야 합니다. 일반적으로 업데이트에 사용할 수 있는 가장 효율적인 패턴은 GET을 사용하여 인덱스 정의를 검색하여 수정한 다음 PUT을 사용하여 정의를 업데이트하는 것입니다.

여기서는 편의상 인덱스를 만드는 데 사용되는 스키마 구문을 재현합니다. 자세한 내용은 [인덱스 만들기](#CreateIndex)를 참조하세요.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
		  "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
	  "analyzers":(optional)[ ... ],
	  "charFilters":(optional)[ ... ],
	  "tokenizers":(optional)[ ... ],
	  "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**응답**

요청이 성공적으로 실행되면 “204 콘텐츠 없음”이 반환됩니다.

기본적으로 응답 본문은 비어 있습니다. 그러나 `Prefer` 요청 헤더가 `return=representation`으로 설정되어 있으면 응답 본문에는 업데이트된 인덱스 정의의 JSON이 포함됩니다. 이 경우의 성공 상태 코드는 "200 OK"입니다.

**사용자 지정 분석기로 인덱스 정의 업데이트**

분석기, 토크나이저, 토큰 필터 또는 문자 필터는 일단 정의한 후에는 수정할 수 없습니다. 인덱스 업데이트 요청에서 `allowIndexDowntime` 플래그가 true로 설정된 경우에만 이러한 새 항목을 기존 인덱스에 추가할 수 있습니다.

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

이 작업 중에 인덱스가 몇 초 이상 오프라인 상태가 되면 인덱싱 및 쿼리 요청이 실패합니다. 인덱스의 성능 및 쓰기 가용성은 인덱스를 업데이트한 후 몇 분 동안, 인덱스가 아주 큰 경우에는 더 긴 시간 동안 제대로 작동하지 않을 수 있습니다.

<a name="ListIndexes"></a>
## 인덱스 나열

**인덱스 나열** 작업은 현재 Azure 검색 서비스에 있는 인덱스의 목록을 반환합니다.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 나열** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 나열** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 인덱스 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /indexes?api-version=2015-02-28-Preview&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

이 기술은 검색 서비스에 많은 인덱스가 있는 경우 대역폭을 절감하는 데 유용합니다.

<a name="GetIndex"></a>
## 인덱스 가져오기

**인덱스 가져오기** 작업은 Azure 검색에서 인덱스 정의를 가져옵니다.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 가져오기** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 인덱스 컬렉션에서 반환할 인덱스를 지정합니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 가져오기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 페이로드의 예제는 [인덱스 만들기 및 업데이트](#CreateUpdateIndexExample)에서 예제 JSON을 참조하세요.

<a name="DeleteIndex"></a>
## 인덱스 삭제

**인덱스 삭제** 작업은 Azure 검색 서비스에서 인덱스 및 관련 문서를 제거합니다. Azure 포털의 서비스 대시보드나 API에서 인덱스 이름을 가져올 수 있습니다. 자세한 내용은 [인덱스 나열](#ListIndexes)을 참조하세요.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 삭제** 요청은 DELETE 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 인덱스 컬렉션에서 삭제할 인덱스를 지정합니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **인덱스 삭제** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="GetIndexStats"></a>
## 인덱스 통계 가져오기

**인덱스 통계 가져오기** 작업은 현재 인덱스의 문서 수와 저장소 사용량을 Azure 검색에서 반환합니다.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] 문서 수와 저장소 크기에 대한 통계는 실시간이 아니라 몇 분 간격으로 수집됩니다. 따라서 이 API에서 반환된 통계는 최근 인덱싱 작업으로 인해 발생한 변경 내용을 반영하지 않을 수 있습니다.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **인덱스 통계 가져오기** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 지정한 인덱스의 인덱스 통계를 반환하도록 서비스에 지시합니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.


**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **인덱스 통계 가져오기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문의 형식은 다음과 같습니다.

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## 테스트 분석기

**분석 API**는 분석기가 텍스트를 토큰으로 구분하는 방법을 보여 줍니다.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. **분석 API** 요청을 POST 메서드를 사용하여 생성할 수 있습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.


**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **분석 API** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key`를 포함해야 합니다.

요청 URL을 생성하려면 인덱스 이름과 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

또는

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

`analyzer_name`, `tokenizer_name`, `token_filter_name` 및 `char_filter_name`은 인덱스에 대한 사전 정의 또는 사용자 지정 분석기, 토크나이저, 토큰 필터 및 문자 필터의 유효한 이름이어야 합니다. 어휘 분석 프로세스에 대한 자세한 내용은 [Azure 검색에서 분석](https://aka.ms/azsanalysis)을 참조하세요.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문의 형식은 다음과 같습니다.

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**분석 API 예제**

**요청**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**응답**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## 문서 작업

Azure 검색에서는 인덱스가 클라우드에 저장되며 서비스에 업로드하는 JSON 문서를 사용하여 인덱스를 채웁니다. 업로드하는 모든 문서는 검색 데이터 모음으로 구성됩니다. 문서에는 필드가 포함되며, 이러한 필드 중 일부는 문서 업로드 시 검색 용어로 토큰화됩니다. Azure 검색 API의 `/docs` URL 세그먼트는 인덱스의 문서 컬렉션을 나타냅니다. 문서 업로드/병합/삭제/쿼리 등 컬렉션에 대해 수행하는 모든 작업은 단일 인덱스의 컨텍스트에서 수행되므로 이러한 작업의 URL은 항상 지정된 인덱스 이름에 대해 `/indexes/[index name]/docs`로 시작됩니다.

응용 프로그램 코드는 Azure 검색에 업로드할 수 있는 JSON 문서를 생성해야 합니다. 또는 데이터 원본이 Azure SQL 데이터베이스 또는 DocumentDB인 경우 [인덱서](https://msdn.microsoft.com/library/dn946891.aspx)를 사용하여 문서를 로드할 수도 있습니다. 일반적으로 인덱스는 사용자가 제공하는 단일 데이터 집합에서 채워집니다.

검색하려는 항목당 문서 하나를 포함하도록 계획해야 합니다. 예를 들어 영화 대여 응용 프로그램은 영화당 문서 하나를, 상점 응용 프로그램은 SKU당 문서 하나를, 온라인 교육 과정 응용 프로그램은 과정당 문서 하나를, 리서치 업체는 리포지토리의 학술 문서당 문서 하나를 포함할 수 있습니다.

문서는 하나 이상의 필드로 구성됩니다. 필드는 Azure 검색에서 검색 용어로 토큰화되는 텍스트를 포함할 수 있으며 필터 또는 점수 매기기 프로필에서 사용할 수 있는 토큰화되지 않는 값 또는 텍스트가 아닌 값도 포함할 수 있습니다. 각 필드에 대해 지원되는 이름, 데이터 형식, 검색 기능은 인덱스 스키마에 의해 결정됩니다. 각 인덱스 스키마의 필드 중 하나는 ID로 지정해야 하며 각 문서는 인덱스에서 해당 문서를 고유하게 식별하는 ID 필드 값을 포함해야 합니다. 다른 모든 문서 필드는 선택 사항이며 지정하지 않으면 null 값으로 기본 설정됩니다. null 값은 검색 인덱스에서 공간을 차지하지 않습니다.

문서를 업로드하려면 먼저 서비스에 인덱스를 만들어야 합니다. 이 첫 단계에 대한 자세한 내용은 [인덱스 만들기](#CreateIndex)를 참조하세요.

<a name="AddOrUpdateDocuments"></a>
## 문서 추가, 업데이트 또는 삭제

HTTP POST를 사용하여 지정한 인덱스에서 문서를 업로드, 병합, 병합하거나 업로드 또는 삭제할 수 있습니다. 업데이트할 문서 수가 많으면 문서 일괄 처리(일괄 처리당 문서 최대 1,000개 또는 일괄 처리당 약 16MB)를 수행하는 것이 좋습니다.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. HTTP POST를 사용하여 지정한 인덱스에서 문서를 업로드, 병합, 병합하거나 업로드 또는 삭제할 수 있습니다.

요청 URI에는 문서를 게시할 인덱스를 지정하는 [index name]이 포함됩니다. 문서는 한 번에 하나의 인덱스에만 게시할 수 있습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 사항입니다. `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **문서 추가** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

요청 본문에는 인덱싱할 하나 이상의 문서가 포함됩니다. 고유 키로 문서를 식별합니다. 각 문서는 upload, merge, mergeOrupload 또는 delete 작업과 연결됩니다. 업로드 요청은 키/값 쌍 집합으로 문서 데이터를 포함해야 합니다.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] 문서 키에는 문자, 숫자, 대시("-"), 밑줄("\_") 및 등호("=")만 포함될 수 있습니다. 자세한 내용은 [명명 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx)을 참조하세요.

**문서 작업**

- `upload`: 업로드 작업은 새 문서는 삽입하고 기존 문서는 업데이트/교체하는 "upsert"와 비슷합니다. 업데이트 시에는 모든 필드가 바뀝니다.
- `merge`: 병합에서는 기존 문서를 지정한 필드로 업데이트합니다. 문서가 없으면 병합하지 못합니다. 문서의 기존 필드는 병합에서 지정하는 필드로 바뀝니다. 여기에는 `Collection(Edm.String)` 형식 필드가 포함됩니다. 예를 들어 값이 `["budget"]`인 "tags" 필드가 포함되어 있는 문서에서 "tags"에 대해 `["economy", "pool"]` 값과의 병합을 실행하면 "tags" 필드의 최종 값은 `["economy", "pool"]`이 됩니다. `["budget", "economy", "pool"]`이 **아닙니다**.
- `mergeOrUpload`: 이 작업은 지정된 키를 포함하는 문서가 인덱스에 이미 있는 경우 `merge`와 동일하게 동작합니다. 문서가 없는 경우에는 새 문서에 대해 `upload`와 같이 동작합니다.
- `delete`: 삭제 시에는 지정된 문서를 인덱스에서 제거합니다. `delete` 작업에서 키 필드 외의 지정한 모든 필드는 무시됩니다. 개별 필드를 문서에서 제거하려는 경우에는 대신 `merge`를 사용하고 필드를 명시적으로 `null`로 설정합니다.

**응답**

작업 성공 시의 응답에는 상태 코드 200 (OK)가 반환됩니다. 이 응답은 모든 항목이 올바르게 인덱싱되었음을 의미합니다. 이는 모든 항목에 대해 `status` 속성이 true로 설정되고 `statusCode` 속성이 201(새롭게 업로드된 문서의 경우) 또는 200(병합 또는 삭제된 문서의 경우)으로 설정되는 것을 나타냅니다.

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

하나 이상의 항목이 성공적으로 인덱싱되지 않은 경우 상태 코드 207(다중 상태)이 반환됩니다. 인덱싱되지 않은 항목은 `status` 필드가 false로 설정됩니다. `errorMessage` 및 `statusCode` 속성은 인덱싱 오류의 원인을 표시합니다.

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

다음 표는 응답에 반환될 수 있는 다양한 문서별 상태 코드를 설명합니다. 일부는 요청 자체로 문제를 나타내는 반면 일부는 임시 오류 상태를 나타냅니다. 후자는 지연 후 다시 시도해야 합니다.

<table style="font-size:12">
    <tr>
		<th>상태 코드</th>
		<th>의미</th>
		<th>재시도 가능</th>
		<th>참고 사항</th>
	</tr>
    <tr>
		<td>200</td>
		<td>문서가 성공적으로 수정되었거나 삭제되었습니다.</td>
		<td>해당 없음</td>
		<td>삭제 작업은 <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>입니다. 즉, 문서 키가 인덱스에 없더라도 해당 키로 삭제 작업을 시도하면 200 상태 코드가 발생합니다.</td>
	</tr>
    <tr>
		<td>201</td>
		<td>문서를 성공적으로 만들었습니다.</td>
		<td>해당 없음</td>
		<td></td>
	</tr>
    <tr>
		<td>400</td>
		<td>인덱싱되는 것을 방지하는 문서에 오류가 발생했습니다.</td>
		<td>아니요</td>
		<td>응답에서 오류 메시지는 문서의 문제를 표시합니다.</td>
	</tr>
    <tr>
		<td>404</td>
		<td>지정된 키가 인덱스에 존재하지 않으므로 문서를 병합할 수 없습니다.</td>
		<td>아니요</td>
		<td>새 문서를 만들기 때문에 업로드에 이 오류가 발생하지 않으며 <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>이므로 삭제에 오류가 발생하지 않습니다.</td>
	</tr>
    <tr>
		<td>409</td>
		<td>문서를 인덱싱하려고 할 때 버전 충돌이 감지되었습니다.</td>
		<td>예</td>
		<td>동일한 문서를 동시에 한 번 이상 인덱싱하려고 하는 경우 발생할 수 있습니다.</td>
	</tr>
    <tr>
		<td>422</td>
		<td>'true'로 설정된 'allowIndexDowntime' 플래그가 업데이트되었으므로 인덱스를 일시적으로 사용할 수 없습니다.</td>
		<td>예</td>
		<td></td>
	</tr>
    <tr>
		<td>503</td>
		<td>큰 부하로 인해 검색 서비스를 일시적으로 사용할 수 없습니다.</td>
		<td>예</td>
		<td>이 경우 다시 시도하기 전에 코드는 대기해야 합니다. 그렇지 않으면 서비스 사용 불가가 연장될 위험이 있습니다.</td>
	</tr>
</table> 

**참고**: 클라이언트 코드에서 207 응답이 자주 반환된다면 시스템 부하가 높기 때문일 수 있습니다. 503에 대한 `statusCode` 속성을 통해 시스템 부하가 원인인지 확인할 수 있습니다. 시스템 부하가 원인이라면 ***인덱싱 요청을 제한***하는 것이 좋습니다. 인덱싱 트래픽이 감소하지 않는 경우에는 시스템에서 모든 요청을 거부하며 503 오류가 발생할 수 있습니다.

상태 코드 429는 인덱스당 문서 수의 할당량이 초과되었음을 나타냅니다. 이 경우에는 새 인덱스를 만들거나 업그레이드를 통해 용량 제한을 높여야 합니다.

**예제:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## 문서 검색

**검색** 작업은 GET 또는 POST 요청으로 실행되며, 일치하는 문서의 선택 조건을 제공하는 매개 변수를 지정합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**GET 대신 POST를 사용하는 경우**

HTTP GET을 사용하여 **검색** API를 호출하는 경우 요청 URL의 길이는 8KB를 초과할 수 없습니다. 이 크기는 일반적으로 대부분의 응용 프로그램에서 충분합니다. 그러나 일부 응용 프로그램은 매우 큰 쿼리 또는 OData 필터 식을 생성합니다. 이러한 응용 프로그램의 경우 GET보다 더 많은 필터와 쿼리를 허용할 수 있는 HTTP POST를 사용하는 것이 좋습니다. POST를 사용하면 쿼리의 절 또는 용어 수는 제한되지만 POST의 요청 크기 제한이 약 16MB이므로 원시 쿼리의 크기는 제한되지 않습니다.

> [AZURE.NOTE] POST 요청 크기 제한이 매우 크긴 하지만 검색 쿼리 및 필터 식을 임의로 복잡하게 작성할 수는 없습니다. 검색 쿼리 및 필터 복잡성 제한에 대한 자세한 내용은 [Lucene 쿼리 구문](https://msdn.microsoft.com/library/mt589323.aspx) 및 [OData 식 구문](https://msdn.microsoft.com/library/dn798921.aspx)을 참조하세요.

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **검색** 요청은 GET 또는 POST 메서드를 사용하여 생성할 수 있습니다.

요청 URI는 매개 변수와 일치하는 모든 문서에 대해 쿼리할 인덱스를 지정합니다. GET 요청은 쿼리 문자열에 매개 변수를 지정하고 POST 요청은 요청 본문에 지정합니다.

GET 요청을 만드는 경우, REST API를 직접 호출할 때는 특정 쿼리 매개 변수를 [URL로 인코딩](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)하는 것이 좋습니다. **검색** 작업의 경우 다음이 포함됩니다.

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

URL 인코딩은 위 쿼리 매개 변수에만 권장됩니다. 실수로 전체 쿼리 문자열을 URL로 인코딩한 경우(모든 항목을 ? 뒤에 입력)에는 요청이 중단됩니다.

또한 URL 인코딩은 GET을 사용하여 REST API를 직접 호출할 때만 필요합니다. POST를 사용하여 **검색**을 호출할 때 또는 URL 인코딩을 자동으로 처리하는 [.NET 클라이언트 라이브러리](https://msdn.microsoft.com/library/dn951165.aspx)를 사용할 때는 URL 인코딩이 필요하지 않습니다.

<a name="SearchQueryParameters"></a> **쿼리 매개 변수**

**검색**은 쿼리 조건을 제공하고 검색 동작을 지정하는 여러 매개 변수를 허용합니다. GET을 통해 **검색**을 호출할 때 URL 쿼리 문자열로, 그리고 POST를 통해 **검색**을 호출할 때 요청 본문에 JSON 속성으로 이러한 매개 변수를 제공합니다. 일부 매개 변수에 대한 구문은 GET 및 POST 간에 약간 다릅니다. 이러한 차이가 아래에 나와 있습니다.

`search=[string]`(선택) - 검색할 텍스트입니다. `searchFields`를 지정하지 않으면 기본적으로 모든 `searchable` 필드가 검색됩니다. `searchable` 필드를 검색할 때는 검색 텍스트 자체가 토큰화되므로 여러 용어를 공백으로 구분할 수 있습니다(예: `search=hello world`). 모든 용어를 일치시키려면 `*`를 사용합니다(부울 필터 쿼리에 유용할 수 있음). 이 매개 변수를 생략하는 것은 `*`로 설정하는 것과 같습니다. 검색 구문에 대한 세부 사항은 [단순 쿼리 구문](https://msdn.microsoft.com/library/dn798920.aspx)을 참조하세요.

  - **참고**: `searchable` 필드를 쿼리할 때 놀라운 결과가 나타나는 경우가 있습니다. 토크나이저에는 아포스트로피, 숫자 안의 쉼표 등과 같은 영어 텍스트에 일반적인 사례를 처리하는 논리가 포함되어 있습니다. 예를 들어 `search=123,456`은 개별 용어인 123과 456이 아니라 단일 용어인 123,456과 일치합니다. 여기서 쉼표는 영어에서 큰 숫자에 사용되는 1000 단위 구분 기호이기 때문입니다. 따라서 `search` 매개 변수에서 용어를 구분할 때는 마침표 대신 공백을 사용하는 것이 좋습니다.

`searchMode=any|all`(선택, 기본적으로 `any`) - 문서를 일치 항목으로 계산하기 이해 검색 용어의 일부를 일치시켜야 하는지 또는 전부를 일치시켜야 하는지 지정합니다.

`searchFields=[string]`(선택) - 지정한 텍스트를 검색할 쉼표로 구분된 필드 이름의 목록입니다. 대상 필드는 `searchable`로 표시되어야 합니다.

`queryType=simple|full` (선택 사항, 기본값 `simple`) - "simple"로 설정하면 +, * 및 “” 등과 같은 기호에 허용되는 단순 쿼리 언어를 사용하여 검색 텍스트가 해석됩니다. 쿼리는 기본적으로 각 문서의 모든 검색 가능 필드(또는 `searchFields`에 나타난 필드)에 걸쳐 평가됩니다. 쿼리 유형을 `full`(으)로 설정하면 필드별 및 가중치 적용 검색에 허용되는 Lucene 쿼리 언어를 사용하여 검색 텍스트가 해석됩니다. 검색 구문에 대한 구체적인 사항은 [단순 쿼리 구문](https://msdn.microsoft.com/library/dn798920.aspx) 및 [Lucene 쿼리 구문](https://msdn.microsoft.com/library/mt589323.aspx)을 참조하세요.
 
> [AZURE.NOTE] Lucene 쿼리 언어의 범위 검색은 유사한 기능을 제공하는 $filter를 위해 지원되지 않습니다.

`moreLikeThis=[key]`(선택) **중요:** 이 기능은 `2015-02-28-Preview`에서만 사용할 수 있습니다. 텍스트 검색 매개 변수 `search=[string]`이 포함된 쿼리에는 이 옵션을 사용할 수 없습니다. `moreLikeThis` 매개 변수는 문서 키로 지정된 문서와 유사한 문서를 찾습니다. 검색 요청이 `moreLikeThis`를 통해 이루어진 경우에는 원본 문서에서 용어의 빈도 및 희귀성에 따라 검색 용어 목록이 생성됩니다. 그런 다음 이러한 용어가 요청에 사용됩니다. `searchFields`가 검색되는 필드를 제한하는 데 사용되지 않은 한 기본적으로 모든 `searchable` 필드의 내용이 고려됩니다.

`$skip=#`(선택 사항) - 건너뛸 검색 결과 수입니다. 100,000보다 클 수 없습니다. 문서를 순차적으로 검색하려는 경우 이 제한으로 인해 `$skip`을 사용할 수 없다면 대신 전체 정렬된 키에서 `$orderby`를 사용하고 범위 쿼리에 `$filter`를 사용하는 것이 좋습니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$skip` 대신 `skip`입니다.

`$top=#`(선택 사항) - 검색할 검색 결과 수입니다. 검색 결과의 클라이언트쪽 페이징을 구현하기 위해 `$skip`과 함께 사용할 수 있습니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$top` 대신 `top`입니다.

`$count=true|false`(선택, 기본적으로 `false`) - 총 결과 수를 가져올지 여부를 지정합니다. `search` 및 `$filter` 매개 변수와 일치하는 모든 문서의 개수이며 `$top` 및 `$skip`을 무시합니다. 이 값을 `true`로 설정하면 성능이 저하될 수 있습니다. 반환되는 개수는 근사값입니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$count` 대신 `count`입니다.

`$orderby=[string]`(선택) - 결과 정렬 기준으로 사용할 쉼표로 구분된 식 목록입니다. 각 식은 필드 이름이거나 `geo.distance()` 함수 호출일 수 있습니다. 각 식 뒤에는 오름차순을 나타내는 `asc` 또는 내림차순을 나타내는 `desc`가 올 수 있습니다. 기본값은 오름차순입니다. 동률은 문서의 일치 점수로 구분됩니다. `$orderby`를 지정하지 않은 경우 기본 정렬 순서는 문서 일치 점수별 내림차순입니다. `$orderby` 절은 32개로 제한됩니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$orderby` 대신 `orderby`입니다.

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않으면 스키마에서 검색 가능으로 표시된 모든 필드가 포함됩니다. 이 매개 변수를 `*`로 설정하여 모든 필드를 명시적으로 요청할 수도 있습니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$select` 대신 `select`입니다.

`facet=[string]`(0 이상) - 패싯할 필드입니다. 필요에 따라 문자열은 쉼표로 구분된 `name:value` 쌍으로 표현된 패싯을 사용자 지정하는 매개 변수를 포함할 수 있습니다. 유효한 매개 변수는 다음과 같습니다.

- `count`(패싯 용어의 최대 개수, 기본값은 10). 최대값은 없지만 값이 크면 성능이 저하되며, 특히 패싯 필드에 포함된 고유한 항목 수가 많을수록 더욱 그렇습니다.
  - 예: `facet=category,count:5`는 패싯 결과에서 상위 5개의 범주를 가져옵니다.
  - **참고**: `count` 매개 변수가 고유한 항목 수보다 작으면 결과가 정확하지 않을 수 있습니다. 이는 패싯 쿼리가 분할된 데이터베이스에서 분산된 방식 때문입니다. `count`를 늘리면 일반적으로 용어 수의 정확도가 높아지지만 성능이 저하됩니다.
- `sort`(개수별로 *내림차순* 정렬하는 `count`, 개수별로 *오름차순* 정렬하는 `-count`, 값별로 *오름차순* 정렬하는 `value`, 값별로 *내림차순* 정렬하는 `-value` 중 하나)
  - 예: `facet=category,count:3,sort:count`는 각 도시 이름이 있는 문서 수를 기준으로 내림차순으로 정렬된 패싯 결과에서 상위 3개의 범주를 가져옵니다. 예를 들어 상위 3개의 범주가 Budget, Motel, Luxury인 경우 적중 항목 수가 Budget은 5개, Motel은 6개, Luxury는 4개이면 버킷이 Motel, Budget, Luxury 순으로 정렬됩니다.
  - 예: `facet=rating,sort:-value`는 값을 기준으로 내림차순으로 정렬된 가능한 모든 등급의 버킷을 생성합니다. 예를 들어 등급이 1~5인 경우 각 등급과 일치하는 문서 수에 상관없이 버킷이 5, 4, 3, 2, 1 순으로 정렬됩니다.
- `values`(파이프로 구분된 숫자 또는 패싯 항목 값의 동적 집합을 지정하는 `Edm.DateTimeOffset` 값)
  - 예: `facet=baseRate,values:10|20`은 각각 기본 요금이 1~9, 10~19 및 20 이상인 버킷 3개를 생성합니다.
  - 예: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z`는 각각 2010년 2월 이전에 리노베이션된 호텔과 2010년 2월 1일 이후에 리노베이션된 호텔에 대한 버킷 2개를 생성합니다.
- `interval`(숫자의 경우 0보다 큰 정수 간격 또는 날짜/시간 값의 경우 `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year`)
  - 예: `facet=baseRate,interval:100`은 기본 요금 범위 100을 기준으로 버킷을 생성합니다. 예를 들어 기본 요금이 모두 $60에서 $600 사이에 속한 경우 0-100, 100-200, 200-300, 300-400, 400-500 및 500-600에 대한 버킷이 생성됩니다.
  - 예: `facet=lastRenovationDate,interval:year`는 호텔이 리노베이션된 각 연도에 대해 하나의 버킷을 생성합니다.
- `timeoffset` ([+-]hh:mm, [+-]hhmm, 또는 [+-]hh) `timeoffset`은 선택 사항입니다. `interval` 옵션과 함께 `Edm.DateTimeOffset` 형식의 필드에 적용할 때만 사용할 수 있습니다. 이 값은 설정 시간 경계에서 고려할 UTC 시간 오프셋을 지정합니다.
  - 예를 들어 `facet=lastRenovationDate,interval:day,timeoffset:-01:00`에서는 01:00:00 UTC(대상 표준 시간대의 자정)부터 시작하는 날짜 경계를 사용합니다.
- **참고**: `count`와 `sort`는 동일한 패싯 사양에서 함께 사용할 수 있지만 `interval` 또는 `values` 및 `interval`과 `values`는 함께 사용할 수 없습니다.
- **참고**: `timeoffset`을 지정하지 않은 경우 날짜 시간의 간격 패싯은 UTC 시간을 기준으로 계산됩니다. 예를 들어 `facet=lastRenovationDate,interval:day`인 경우 날짜 경계는 00:00:00 UTC에 시작합니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `facet` 대신 `facets`입니다. 또한 문자열의 JSON 배열로 지정할 수도 있습니다. 여기서 각 문자열은 별도의 패싯 식입니다.

`$filter=[string]`(선택) - 표준 OData 구문의 구조화된 검색 식입니다. Azure 검색에서 지원하는 OData 식 문법의 하위 집합에 대한 자세한 내용은 [OData 식 구문](#ODataExpressionSyntax)을 참조하세요.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `$filter` 대신 `filter`입니다.

`highlight=[string]`(선택) - 적중 항목을 강조 표시하는 데 사용되는 쉼표로 구분된 필드 이름 집합입니다. `searchable` 필드만 적중 항목 강조 표시에 사용할 수 있습니다.

`highlightPreTag=[string]`(선택, 기본적으로 `<em>`) - 적중 항목 강조 표시 앞에 추가되는 문자열 태그입니다. `highlightPostTag`로 설정해야 합니다.

> [AZURE.NOTE] GET을 사용하여 **검색**을 호출하는 경우 URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`highlightPostTag=[string]`(선택, 기본적으로 `</em>`) - 적중 항목 강조 표시에 추가되는 문자열 태그입니다. `highlightPreTag`로 설정해야 합니다.

> [AZURE.NOTE] GET을 사용하여 **검색**을 호출하는 경우 URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`scoringProfile=[string]`(선택) - 결과를 정렬하기 위해 일치하는 문서의 일치 점수를 평가할 점수 매기기 프로필의 이름입니다.

`scoringParameter=[string]`(0 이상) - `name-value1,value2,...` 형식을 사용하여 점수 매기기 함수에 정의된 각 매개 변수(예: `referencePointParameter`)의 값을 나타냅니다.

- 예를 들어 점수 매기기 프로필이 "mylocation"라는 매개 변수를 사용하여 함수를 정의하는 경우 쿼리 문자열 옵션은 `&scoringParameter=mylocation--122.2,44.8`입니다. 첫 번째 대시는 값 목록에서 이름을 구분하는 반면 두 번째 대시는 첫 번째 값의 일부입니다(이 예제의 경도).
- 쉼표를 포함할 수 있는 태그 가속과 같은 점수 매기기 매개 변수의 경우 작은따옴표를 사용하여 목록에서 해당 값을 이스케이프할 수 있습니다. 값 자체가 작은따옴표를 포함하는 경우 두 배로 높여 이스케이프할 수 있습니다.
  - 예를 들어 "mytag"라는 태그 가속 매개 변수가 있고 "Hello, O'Brien" 및 "Smith" 태그 값을 상승하려는 경우 쿼리 문자열 옵션은 `&scoringParameter=mytag-'Hello, O''Brien',Smith`입니다. 따옴표는 쉼표를 포함하는 값에만 필요합니다.

> [AZURE.NOTE] POST를 사용하여 **검색**을 호출하는 경우 이 매개 변수의 이름은 `scoringParameter` 대신 `scoringParameters`입니다. 또한 문자열의 JSON 배열로 지정할 수도 있습니다. 여기서 각 문자열은 별도의 `name-values` 쌍입니다.

`minimumCoverage`(선택 사항, 기본값 100까지) - 성공으로 보고할 쿼리를 위해 검색 쿼리를 통해 처리해야 하는 인덱스의 백분율을 나타내는 0과 100 사이의 숫자입니다. 기본적으로 전체 인덱스를 사용할 수 있습니다. 또는 `Search`이(가) HTTP 상태 코드 503을 반환합니다. `minimumCoverage` 및 `Search` 성공을 설정하는 경우 HTTP 200을 반환하며 쿼리에 포함되었던 인덱스의 백분율을 나타내는 응답에 `@search.coverage` 값을 포함합니다.

> [AZURE.NOTE] 100보다 작은 값으로 이 매개 변수를 설정하면 하나의 복제본만을 사용하여 서비스에 검색 가용성을 보장하는 데 유용할 수 있습니다. 하지만 일치하는 모든 문서가 검색 결과에 나타나도록 허용되는 것은 아닙니다. 검색 회수가 가용성보다 응용 프로그램에 더욱 중요한 경우, `minimumCoverage`을(를) 기본 값 100으로 두는 것이 가장 좋습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업의 경우 `api-version`은 **검색** 호출 시 GET을 사용하든 또는 POST를 사용하든 관계 없이 URL에 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **검색** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

GET의 경우: 없음.

POST의 경우:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**부분 검색 응답의 연속 작업**

Azure 검색이 요청된 모든 결과를 단일 검색 응답에서 반환하지 못하는 경우가 종종 있습니다. 이러한 현상이 발생하는 여러 이유가 있는데, `$top`을 지정하지 않거나 `$top`에 너무 큰 값을 지정하여 쿼리에서 너무 많은 문서를 요청하는 경우를 예로 들 수 있습니다. 이러한 경우 Azure 검색에서는 응답 본문에 `@odata.nextLink` 주석을 넣고, 만약 POST 요청인 경우에는 `@search.nextPageParameters`까지 넣습니다. 이러한 주석의 값을 사용하여 검색 응답의 다음 부분을 가져오는 또 다른 검색 요청을 작성할 수 있습니다. 이것을 원래 검색 요청의 ***연속 작업***이라고 부르며, 주석은 일반적으로 ***연속 토큰***이라고 합니다. 이러한 주석의 구문 및 응답 본문에서 이러한 것들이 표시되는 위치에 대한 자세한 내용은 [아래 예](#SearchResponse)를 참조하세요.

Azure 검색이 연속 토큰을 반환하는 이유는 구현에 따라 그리고 대상에 따라 다릅니다. 견고한 클라이언트라면 예상보다 적은 문서가 반환되고 문서 검색을 계속할 수 있도록 연속 토큰이 포함되는 상황을 언제든지 처리할 수 있어야 합니다. 또한 검색을 계속하려면 원래 요청과 동일한 HTTP 메서드를 사용해야 합니다. 예를 들어 GET 요청을 보냈으면 보내는 모든 연속 작업 요청에서도 GET을 사용해야 합니다(POST도 마찬가지).

<a name="SearchResponse"></a> **응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**예제:**

추가 예제는 [Azure 검색에 대한 OData 식 구문](https://msdn.microsoft.com/library/azure/dn798921.aspx) 페이지에서 확인할 수 있습니다.

1) 날짜를 기준으로 내림차순으로 정렬된 인덱스를 검색합니다.


    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "orderby": "lastRenovationDate desc"
    }

2) 패싯 검색에서 인덱스를 검색하고 범주, 등급, 태그 및 특정 범위의 baseRate이 있는 항목에 대한 패싯을 검색합니다.


    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ]
    }

3) 필터를 사용하여 사용자가 등급 3 및 범주 "Motel"을 클릭한 후 이전 패싯 쿼리 결과의 범위를 좁힙니다.


    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }

4) 패싯 검색에서 쿼리에 반환되는 고유 항목의 상한을 설정합니다. 기본값은 10이지만 `facet` 특성의 `count` 매개 변수를 사용하여 이 값을 늘리거나 줄일 수 있습니다.


    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "city,count:5" ]
    }

5) 특정 필드(예: 언어 관련 필드) 내에서 인덱스를 검색합니다.


    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hôtel",
      "searchFields": "description_fr"
    }

6) 여러 필드에서 인덱스를 검색합니다. 예를 들어 모두 동일한 인덱스 내에서 여러 언어로 된 검색 가능 필드를 저장하고 쿼리할 수 있습니다. 같은 문서에 영어 설명과 프랑스어 설명이 둘 다 있는 경우 쿼리 결과에서 둘 중 하나 또는 둘 다를 반환할 수 있습니다.


	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

	POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hotel",
      "searchFields": "description, description_fr"
    }

한 번에 하나의 인덱스만 쿼리할 수 있습니다. 한번에 하나씩 쿼리하려는 경우가 아니면 각 언어에 대한 여러 인덱스를 만들지 마세요.

7) 페이징 - 항목의 첫 번째 페이지(페이지 크기는 10)를 가져옵니다.


    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 0,
      "top": 10
    }

8) 페이징 - 항목의 두 번째 페이지(페이지 크기는 10)를 가져옵니다.


    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 10,
      "top": 10
    }

9) 특정 필드 집합을 검색합니다.


    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "select": "hotelName, description"
    }

10) 특정 필터 식과 일치하는 문서를 검색합니다.


    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }

11) 인덱스를 검색하여 적중 항목이 강조 표시된 조각을 반환합니다.


    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "highlight": "description"
    }

12) 인덱스를 검색하여 참조 위치에서 가까이 있는 순으로 정렬된 문서를 반환합니다.


    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "orderby": "geo.distance(location, geography'POINT(-122.12315 47.88121)')"
    }

13) 두 개의 거리 점수 매기기 함수를 사용하는 "geo"라는 점수 매기기 프로필이 있는 것으로 가정하여 인덱스를 검색합니다. 두 함수는 각각 "currentLocation"이라는 매개 변수와 "lastLocation"이라는 매개 변수를 정의합니다.


    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&scoringParameter=lastLocation--121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "scoringProfile": "geo",
      "scoringParameters": [ "currentLocation--122.123,44.77233", "lastLocation--121.499,44.2113" ]
    }

14) [단순 쿼리 구문](https://msdn.microsoft.com/library/dn798920.aspx)을 사용하여 인덱스에서 문서를 찾습니다. 이 쿼리는 검색 가능한 필드에 "comfort" 및 "location"이라는 용어가 있지만 "motel"이라는 용어가 없는 호텔을 반환합니다.


    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "comfort +location -motel",
      "searchMode": "all"
    }

위에서 `searchMode=all`의 사용에 주의하세요. 이 매개 변수를 포함하면 기본값 `searchMode=any`가 재정의되어 `-motel`이 "OR NOT" 대신 "AND NOT"을 의미하게 됩니다. `searchMode=all`을 사용하지 않으면 검색 결과를 제한하는 것이 아니라 확장하는 "OR NOT"을 가져오므로 일부 사용자에게 직관적으로 보이지 않을 수 있습니다.

15) [Lucene 쿼리 구문](https://msdn.microsoft.com/library/mt589323.aspx)을 사용하여 인덱스에서 문서를 찾습니다. 이 쿼리는 범주 필드에 “예산"이라는 용어가 포함된 호텔 및 “최근 혁신된" 문구가 포함된 모든 검색 가능 필드를 반환합니다. 용어 boost 값(3)의 결과로 "최근 혁신된" 문구가 포함된 문서가 더 높은 순위가 됩니다.

    GET /indexes/hotels/docs?search=category:budget AND "recently renovated"^3&searchMode=all&api-version=2015-02-28-Preview&querytype=full

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "category:budget AND "recently renovated"^3",
      "queryType": "full",
      "searchMode": "all"
    }

<a name="LookupAPI"></a>
## 문서 조회

**문서 조회** 작업은 Azure 검색에서 문서를 검색합니다. 사용자가 특정 검색 결과를 클릭할 때 해당 문서에 대한 특정 세부 정보를 조회하려는 경우 이 작업을 사용하면 유용합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. 다음과 같이 **문서 조회** 요청을 생성할 수 있습니다.

    GET /indexes/[index name]/docs/key?[query parameters]

기존 OData 구문을 키 조회에 사용할 수도 있습니다.

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

요청 URI에는 검색할 문서와 문서를 검색할 인덱스를 지정하는 [index name] 및 [key]가 포함됩니다. 한 번에 하나의 문서만 가져올 수 있습니다. 단일 요청에서 여러 문서를 가져오려면 **Search**를 사용합니다.

**쿼리 매개 변수**

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않거나 `*`로 지정하면 스키마에서 검색 가능으로 표시된 모든 필드가 프로젝션에 포함됩니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업에는 `api-version`은 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **문서 조회** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**예제**

'2' 키가 있는 문서 조회

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

OData 구문을 사용하여 '3' 키가 있는 문서 조회

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## 문서 수 계산

**문서 수 계산** 작업은 검색 인덱스의 문서 수를 검색합니다. `$count` 구문은 OData 프로토콜의 일부입니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **문서 수 계산** 요청은 GET 메서드를 사용하여 생성할 수 있습니다.

요청 URI의 [index name]은 지정한 인덱스의 문서 컬렉션에 포함된 모든 항목의 수를 반환하도록 서비스에 지시합니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Accept`: 이 값은 `text/plain`으로 설정해야 합니다.
- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **문서 수 계산** 요청은 `api-key`에 대해 관리 키 또는 쿼리 키를 지정할 수 있습니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

없음.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문에는 일반 텍스트로 서식이 지정된 정수로 개수 값이 포함됩니다.

<a name="Suggestions"></a>
## 제안

**제안** 작업에서는 부분 검색 입력을 기준으로 제안을 검색합니다. 이 작업은 일반적으로 사용자가 검색 용어를 입력할 때 미리 입력된 제안을 제공하기 위해 검색 상자에서 사용됩니다.

제안 요청은 대상 문서를 제공하기 위한 것이므로 같은 검색 입력과 일치하는 후보 문서가 여러 개이면 제안되는 텍스트가 반복될 수 있습니다. `$select`를 사용하면 각 제안의 원본인 문서를 확인할 수 있도록 문서 키를 비롯한 다른 문서 필드를 검색할 수 있습니다.

**제안** 작업이 GET 또는 POST 요청으로 발생합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**GET 대신 POST를 사용하는 경우**

HTTP GET을 사용하여 **제안** API를 호출하는 경우 요청 URL의 길이는 8KB를 초과할 수 없습니다. 이 크기는 일반적으로 대부분의 응용 프로그램에서 충분합니다. 그러나 일부 응용 프로그램은 매우 큰 쿼리, 특히 OData 필터 식을 생성합니다. 이러한 응용 프로그램의 경우 GET보다 더 많은 필터를 허용할 수 있는 HTTP POST를 사용하는 것이 좋습니다. POST를 사용하면 필터의 절 수는 제한되지만 POST의 요청 크기 제한이 약 16MB이므로 원시 필터 문자열의 크기는 제한되지 않습니다.

> [AZURE.NOTE] POST 요청 크기 제한이 매우 크긴 하지만 필터 식을 임의로 복잡하게 작성할 수는 없습니다. 필터 복잡성 제한에 대한 자세한 내용은 [OData 식 구문](https://msdn.microsoft.com/library/dn798921.aspx)을 참조하세요.

**요청**

서비스 요청에는 HTTPS를 사용해야 합니다. **제안** 요청은 GET 또는 POST 메서드를 사용하여 생성할 수 있습니다.

요청 URI는 쿼리할 인덱스의 이름을 지정합니다. GET 요청은 쿼리 문자열에 부분적 입력 검색 용어 등의 매개 변수를 지정하고 POST 요청은 요청 본문에 지정합니다.

GET 요청을 만드는 경우, REST API를 직접 호출할 때는 특정 쿼리 매개 변수를 [URL로 인코딩](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx)하는 것이 좋습니다. **제안** 작업의 경우 다음이 포함됩니다.

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

URL 인코딩은 위 쿼리 매개 변수에만 권장됩니다. 실수로 전체 쿼리 문자열을 URL로 인코딩한 경우(모든 항목을 ? 뒤에 입력)에는 요청이 중단됩니다.

또한 URL 인코딩은 GET을 사용하여 REST API를 직접 호출할 때만 필요합니다. POST를 사용하여 **제안**을 호출할 때 또는 URL 인코딩을 자동으로 처리하는 [.NET 클라이언트 라이브러리](https://msdn.microsoft.com/library/dn951165.aspx)를 사용할 때는 URL 인코딩이 필요하지 않습니다.

**쿼리 매개 변수**

**제안**은 쿼리 조건을 제공하고 검색 동작을 지정하는 여러 매개 변수를 허용합니다. GET을 통해 **제안**을 호출할 때 URL 쿼리 문자열로, 그리고 POST를 통해 **제안**을 호출할 때 요청 본문에 JSON 속성으로 이러한 매개 변수를 제공합니다. 일부 매개 변수에 대한 구문은 GET 및 POST 간에 약간 다릅니다. 이러한 차이가 아래에 나와 있습니다.

`search=[string]` - 쿼리를 제안하는 데 사용할 검색 텍스트입니다. 최소 1자가 되어야 하며 100자를 넘지 않아야 합니다.

`highlightPreTag=[string]`(선택) - 검색의 적중 항목 앞에 추가할 문자열 태그입니다. `highlightPostTag`로 설정해야 합니다.

> [AZURE.NOTE] GET을 사용하여 **제안**을 호출하는 경우 URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`highlightPostTag=[string]`(선택) - 검색의 적중 항목에 추가할 문자열 태그입니다. `highlightPreTag`로 설정해야 합니다.

> [AZURE.NOTE] GET을 사용하여 **제안**을 호출하는 경우 URL의 예약 문자는 %로 인코딩해야 합니다. 예를 들어 # 대신 %23을 사용해야 합니다.

`suggesterName=[string]` - 인덱스 정의의 일부인 `suggesters` 컬렉션에 지정된 확인기의 이름입니다. 이 `suggester`에 따라 제안된 쿼리 용어를 검색할 필드가 결정됩니다. 자세한 내용은 [확인기](#Suggesters)를 참조하세요.

`fuzzy=[boolean]`(선택, 기본값=false) - true로 설정하면 검색 텍스트에 대체되었거나 누락된 문자가 있더라도 이 API는 제안을 찾습니다. 이 경우 일부 시나리오에서는 검색 환경이 개선되지만, 유사 제안 검색 속도가 느려지며 리소스가 더 많이 사용되므로 성능은 저하됩니다.

`searchFields=[string]`(선택) - 지정된 검색 텍스트를 검색하기 위한 쉼표로 구분된 필드 이름 목록입니다. 대상 필드가 제안을 사용하도록 설정되어 있어야 합니다.

`$top=#`(선택, 기본값=5) - 검색할 제안의 수입니다. 값은 1~100 사이의 숫자여야 합니다.

> [AZURE.NOTE] POST를 사용하여 **제안**을 호출하는 경우 이 매개 변수의 이름은 `$top` 대신 `top`입니다.

`$filter=[string]`(선택) - 제안을 검색할 때 고려할 문자를 필터링하는 식입니다.

> [AZURE.NOTE] POST를 사용하여 **제안**을 호출하는 경우 이 매개 변수의 이름은 `$filter` 대신 `filter`입니다.

`$orderby=[string]`(선택) - 결과 정렬 기준으로 사용할 쉼표로 구분된 식 목록입니다. 각 식은 필드 이름이거나 `geo.distance()` 함수 호출일 수 있습니다. 각 식 뒤에는 오름차순을 나타내는 `asc` 또는 내림차순을 나타내는 `desc`가 올 수 있습니다. 기본값은 오름차순입니다. `$orderby` 절은 32개로 제한됩니다.

> [AZURE.NOTE] POST를 사용하여 **제안**을 호출하는 경우 이 매개 변수의 이름은 `$orderby` 대신 `orderby`입니다.

`$select=[string]`(선택) - 검색할 쉼표로 구분된 필드 목록입니다. 지정하지 않으면 문서 키와 제안 텍스트만 반환됩니다. 이 매개 변수를 `*`로 설정하여 모든 필드를 명시적으로 요청할 수 있습니다.

> [AZURE.NOTE] POST를 사용하여 **제안**을 호출하는 경우 이 매개 변수의 이름은 `$select` 대신 `select`입니다.

`minimumCoverage`(선택 사항, 기본값 80까지) - 성공으로 보고할 쿼리를 위해 제안 사항 쿼리를 통해 처리해야 하는 인덱스의 백분율을 나타내는 0과 100 사이의 숫자입니다. 기본적으로 인덱스의 80% 이상을 사용할 수 있습니다. 또는 `Suggest`이(가) HTTP 상태 코드 503을 반환합니다. `minimumCoverage` 및 `Suggest` 성공을 설정하는 경우 HTTP 200을 반환하며 쿼리에 포함되었던 인덱스의 백분율을 나타내는 응답에 `@search.coverage` 값을 포함합니다.

> [AZURE.NOTE] 100보다 작은 값으로 이 매개 변수를 설정하면 하나의 복제본만을 사용하여 서비스에 검색 가용성을 보장하는 데 유용할 수 있습니다. 하지만 일치하는 모든 제안 사항이 결과에 나타나도록 허용되는 것은 아닙니다. 회수가 가용성보다 응용 프로그램에 더욱 중요한 경우, 아래의 `minimumCoverage`보다 낮지 않도록 기본 값 80으로 두는 것이 가장 좋습니다.

`api-version=[string]`(필수). 미리 보기 버전은 `api-version=2015-02-28-Preview`입니다. 자세한 내용 및 대체 버전은 [검색 서비스 버전 관리](http://msdn.microsoft.com/library/azure/dn864560.aspx)를 참조하세요.

참고: 이 작업의 경우 `api-version`은 **제안** 호출 시 GET을 사용하든 또는 POST를 사용하든 관계 없이 URL에 쿼리 매개 변수로 지정됩니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `api-key`: `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스 URL에 고유한 문자열 값입니다. **제안** 요청은 관리 키 또는 쿼리 키를 `api-key`로 지정할 수 있습니다.

요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 Azure 포털의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

**요청 본문**

GET의 경우: 없음.

POST의 경우:

    {
      "filter": "odata_filter_expression",
	  "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
	  "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

프로젝션 옵션을 사용하여 필드를 검색하는 경우에는 배열의 각 요소에 필드가 포함됩니다.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**예제**

부분 검색 입력이 'lux'인 제안 5개 검색

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }

<!---HONumber=AcomDC_0914_2016-->