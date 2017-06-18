<properties
	pageTitle="점수 매기기 프로필(Azure 검색 REST API 버전 2015-02-28-Preview) | Microsoft Azure | Azure 검색 미리 보기 API"
	description="Azure 검색은 사용자가 정의한 점수 매기기 프로필을 기초로 순위의 결과를 조정할 수 있도록 하는 호스팅되는 클라우드 검색 서비스입니다."
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
	ms.author="heidist"
	ms.date="08/29/2016" />

# 점수 매기기 프로필(Azure 검색 REST API 버전 2015-02-28-Preview)

> [AZURE.NOTE] 이 문서에서는 [2015-02-28-Preview](search-api-2015-02-28-preview.md)에서 제공되는 점수 매기기 프로필을 설명합니다. 현재 [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx)에 문서화된 `2015-02-28` 버전과 여기에 설명된 `2015-02-28-Preview` 버전 간에 차이는 없지만 이 문서에서는 전체 API를 다룹니다.

## 개요

점수 매기기는 검색 결과에 반환된 모든 항목의 검색 점수를 계산하는 것입니다. 점수는 현재 검색 작업의 컨텍스트에서 항목의 관련성을 나타내는 표시기입니다. 점수가 높을수록 항목의 관련성도 높습니다. 검색 결과에서 항목은 각 항목에 대해 계산된 검색 점수를 기준으로 높은 순위부터 낮은 순위로 정렬됩니다.

Azure 검색에서는 점수를 계산할 때 기본 점수 매기기 기능을 사용하지만 점수 매기기 프로필을 통해 계산을 사용자 지정할 수 있습니다. 점수 매기기 프로필을 사용하면 검색 결과에서 항목의 순위를 보다 강력하게 제어할 수 있습니다. 예를 들어 잠재 수익을 기준으로 하여 특정 항목을 상승시키거나, 새 항목을 프로모션하거나, 너무 오랫동안 재고에 포함되어 있던 항목을 상승시킬 수 있습니다.

점수 매기기 프로필은 필드, 함수 및 매개 변수로 구성된 인덱스 정의의 일부입니다.

점수 매기기 프로필을 대략적으로 파악할 수 있도록 아래 예제에서는 'geo'라는 간단한 프로필을 보여 줍니다. 이 프로필은 `hotelName` 필드에 검색 용어가 포함된 항목을 상승시킵니다. 또한 `distance` 함수를 사용하여 현재 위치에서 10km 이내에 있는 항목의 순위를 높입니다. 'inn'이라는 용어를 검색하는 경우 'inn'이 호텔 이름의 일부분이라면 이름에 'inn'이 포함된 호텔이 들어 있는 문서가 검색 결과에서 더 높은 순위로 표시됩니다.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

이 점수 매기기 프로필을 사용하는 경우 쿼리 문자열에서 프로필을 지정하도록 쿼리가 작성됩니다. 아래 쿼리에서 요청의 쿼리 매개 변수 `scoringProfile=geo`에 주목합니다.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

이 쿼리는 용어 'inn'을 검색하고 현재 위치를 전달합니다. 이 쿼리에는 `scoringParameter` 등의 다른 매개 변수도 포함되어 있습니다. 쿼리 매개 변수에 대해서는 [문서 검색(Azure 검색 API)](search-api-2015-02-28-preview.md#SearchDocs)에서 설명합니다.

점수 매기기 프로필에 대한 자세한 예제를 검토하려면 [예제](#example)를 클릭하세요.

## 기본 점수 매기기

점수 매기기에서는 순위를 기준으로 정렬된 결과 집합의 각 항목에 대한 검색 점수를 계산합니다. 검색 결과 집합의 모든 항목에는 검색 점수가 할당된 다음 점수가 가장 높은 항목부터 가장 낮은 항목으로 순위가 지정됩니다. 그리고 점수가 높은 항목이 응용 프로그램에 반환됩니다. 기본적으로는 상위 50개 항목이 반환되지만 `$top` 매개 변수를 사용하여 반환되는 항목 수를 늘리거나 줄일 수 있습니다(단일 응답에서 최대 1,000개의 항목 반환 가능).

기본적으로 검색 점수는 쿼리와 데이터의 통계 속성에 따라 계산됩니다. Azure 검색에서는 `searchMode`에 따라 쿼리 문자열의 검색 용어를 일부 또는 모두 포함하는 문서를 찾은 다음 검색 용어가 많이 포함된 문서에 높은 점수를 할당합니다. 해당 용어가 데이터 모음에는 거의 없지만 해당 문서 내에서는 자주 나오는 경우 검색 점수가 더 높아집니다. 이와 같은 관련성 계산 방식의 기준을 TF-IDF(용어 빈도와 문서 빈도 반비례)라고 합니다.

사용자 지정 정렬이 적용되지 않는다고 가정할 때 결과는 검색 점수를 기준으로 순위가 지정된 다음 호출 응용 프로그램으로 반환됩니다. `$top`을 지정하지 않으면 검색 점수가 가장 높은 항목 50개가 반환됩니다.

전체 결과 집합에서 검색 점수 값이 반복될 수 있습니다. 예를 들어 점수가 1.2인 항목이 10개, 1.0인 항목이 20개, 0.5인 항목이 20개 있을 수 있습니다. 여러 적중 항목의 검색 점수가 같은 경우 동일 점수의 항목 순서는 정의되지 않았으므로 항목이 안정적으로 정렬되지 않습니다. 따라서 쿼리를 다시 실행하면 항목 위치가 바뀌어 나타날 수 있습니다. 즉, 두 항목의 점수가 같은 경우 어떤 항목이 먼저 표시되는지 보장되지 않습니다.

## 사용자 지정 점수 매기기를 사용하는 경우

기본 순위 지정 동작으로 비즈니스 목표를 충족할 수 없을 때는 점수 매기기 프로필을 하나 이상 만들어야 합니다. 새로 추가하는 항목의 검색 관련성을 높게 설정하려는 경우를 예로 들 수 있습니다. 이익률을 포함하는 필드나 잠재 수익을 나타내는 다른 필드의 경우에도 마찬가지입니다. 업무상 이점을 제공하는 적중 항목을 상승시키는 것이 점수 매기기 프로필의 사용 결정 시 중요한 요인으로 작용할 수 있습니다.

또한 관련성 기반 순서 지정도 점수 매기기 프로필을 통해 구현됩니다. 이전에 사용했던 검색 결과 페이지에서는 가격, 날짜, 등급 또는 관련성을 기준으로 결과를 정렬했다면, Azure 검색에서는 점수 매기기 프로필을 통해 '관련성' 옵션을 적용할 수 있습니다. 관련성의 정의는 제공하려는 검색 환경의 유형과 비즈니스 목표를 통해 직접 제어할 수 있습니다.

<a name="example"></a>
## 예

위에서 설명한 것처럼 인덱스 스키마에 정의된 하나 이상의 점수 매기기 프로필을 통해 사용자 지정된 점수 매기기를 구현합니다.

아래 예제에서는 `boostGenre` 및 `newAndHighlyRated`의 두 점수 매기기 프로필이 포함된 인덱스의 스키마를 보여 줍니다. 쿼리 매개 변수로 두 프로필 중 하나를 포함하는 쿼리를 이 인덱스에 대해 실행하는 경우 해당 프로필을 사용하여 결과 집합의 점수를 계산합니다.

[다음 예제를 실행해 보세요.](search-get-started-scoring-profiles.md)

    {
      "name": "musicstoreindex",
	  "fields": [
	    { "name": "key", "type": "Edm.String", "key": true },
	    { "name": "albumTitle", "type": "Edm.String" },
	    { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	    { "name": "genre", "type": "Edm.String" },
	    { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	    { "name": "artistName", "type": "Edm.String" },
	    { "name": "orderableOnline", "type": "Edm.Boolean" },
	    { "name": "rating", "type": "Edm.Int32" },
	    { "name": "tags", "type": "Collection(Edm.String)" },
	    { "name": "price", "type": "Edm.Double", "filterable": false },
	    { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	    { "name": "inventory", "type": "Edm.Int32" },
	    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	  ],
      "scoringProfiles": [
        {
	      "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
	    },
        {
	      "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## 워크플로

사용자 지정 점수 매기기 동작을 구현하려면 인덱스를 정의하는 점수 매기기 프로필을 스키마에 추가합니다. 인덱스 하나에 여러 점수 매기기 프로필을 포함할 수는 있지만 프로필은 어떠한 지정된 쿼리에도 한 번에 하나만 지정할 수 있습니다.

먼저 이 항목에서 제공하는 [템플릿](#bkmk_template)을 사용해 보세요.

이름을 입력합니다. 점수 매기기 프로필은 선택 사항이지만 추가하려는 경우 이름을 입력해야 합니다. 이름은 필드의 이름 지정 규칙에 따라 문자로 시작해야 하며 특수 문자와 예약어는 사용할 수 없습니다. 자세한 내용은 [이름 지정 규칙](http://msdn.microsoft.com/library/azure/dn857353.aspx)을 참조하세요.

점수 매기기 프로필의 본문은 가중치가 적용된 필드와 함수에서 생성됩니다.

### Weights ###

점수 매기기 프로필의 `weights` 속성은 필드에 상대적 가중치를 할당하는 이름-값 쌍을 지정합니다. [예제](#example)에서는 albumTitle, genre 및 artistName 필드가 각각 1.5, 5 및 2로 상승합니다. genre가 다른 필드보다 훨씬 크게 상승하는 이유는, `musicstoreindex`에서 'genre'의 경우처럼 비교적 비슷한 데이터를 검색하는 경우 상대 가중치의 편차가 더 커야 할 수 있기 때문입니다. 예를 들어 `musicstoreindex`에서 'rock'은 장르로도 표시되고 같은 구를 사용하는 장르 설명에도 표시됩니다. 이 경우 장르 설명보다 장르에 더 높은 가중치를 적용하려면 genre 필드에 훨씬 높은 상대 가중치를 적용해야 합니다.

### 함수 ###

함수는 특정 컨텍스트에 대해 추가 계산을 수행해야 하는 경우 사용됩니다. 올바른 함수 유형은 `freshness`, `magnitude`, `distance` 및 `tag`입니다. 각 함수에는 고유한 매개 변수가 있습니다.

  - 항목의 최신 또는 오래된 상태를 기준으로 상승시키려면 `freshness`을(를) 사용해야 합니다. 이 함수는 datetime 필드(`Edm.DataTimeOffset`)에만 사용할 수 있습니다. `boostingDuration` 특성은 freshness 함수에서만 사용됩니다.
  - 숫자 값의 크기를 기준으로 상승시키려면 `magnitude`을(를) 사용해야 합니다 이 함수를 사용해야 하는 시나리오에는 이익률, 최고 가격, 최저 가격 또는 다운로드 수를 기준으로 상승시키는 경우가 포함됩니다. 반전 패턴을 사용하려면(예: 높은 가격의 항목보다 낮은 가격의 항목을 상승시키는 경우) 범위를 상위에서 하위로 반전시킬 수 있습니다. 가격 범위가 $100에서 $1 사이인 경우 100에서 `boostingRangeStart`을(를) 설정하고 1에서 `boostingRangeEnd`을(를) 설정하여 낮은 가격의 항목을 상승시킬 수 있습니다. 이 함수는 double 및 integer 필드에서만 사용할 수 있습니다.
  - 근접도나 지리적 위치를 기준으로 상승시키려면 `distance`을(를) 사용해야 합니다 이 함수는 `Edm.GeographyPoint` 필드에만 사용할 수 있습니다.
  - 문서와 검색 쿼리 간에 공통적인 태그를 기준으로 상승시키려면 `tag`을(를) 사용해야 합니다. 이 함수는 `Edm.String` 및 `Collection(Edm.String)` 필드에만 사용할 수 있습니다.
  
#### 함수 사용 규칙 ####

  - 함수 유형(freshness, magnitude, distance, tag)은 소문자여야 합니다.
  - 함수는 null 또는 빈 값을 포함할 수 없습니다. 특히 fieldname를 포함하는 경우에는 값을 설정해야 합니다.
  - 함수는 필터링 가능한 필드에만 적용할 수 있습니다. 필터링 가능한 필드에 대한 자세한 내용은 [인덱스 만들기](search-api-2015-02-28.md#createindex)를 참조하세요.
  - 함수는 인덱스의 필드 컬렉션에 정의된 필드에만 적용할 수 있습니다.

인덱스를 정의한 후 인덱스 스키마와 문서를 차례로 업로드하여 인덱스를 작성합니다. 이러한 작업에 대한 지침은 [인덱스 만들기](search-api-2015-02-28-preview.md#createindex) 및 [문서 추가, 업데이트 또는 삭제](search-api-2015-02-28-preview.md#AddOrUpdateDocuments)를 참조하세요. 인덱스가 작성되면 검색 데이터에 사용할 수 있는 점수 매기기 프로필을 만들어야 합니다.

<a name="bkmk_template"></a>
## Template
이 섹션에서는 점수 매기기 프로필의 구문과 템플릿에 대해 설명합니다. 특성 설명은 다음 섹션의 [인덱스 특성 참조](#bkmk_indexref)를 참조하세요.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## 점수 매기기 프로필 속성 참조

**참고** 점수 매기기 함수는 필터링 가능한 필드에만 적용할 수 있습니다.

| 속성 | 설명 |
|----------|-------------|
| `name` | 필수입니다. 점수 매기기 프로필의 이름입니다. 필드와 동일한 이름 지정 규칙을 따릅니다. 즉, 이름은 문자로 시작해야 하고 마침표, 콜론 또는 @ 기호를 포함할 수 없으며 'azureSearch' 구(대/소문자 구분)로 시작할 수 없습니다. |
| `text` | Weights 속성을 포함합니다. |
| `weights` | 선택 사항입니다. 필드 이름 및 상대적 가중치를 지정하는 이름-값 쌍입니다. 상대적 가중치는 양의 정수 또는 부동 소수점 숫자여야 합니다. 해당하는 가중치 없이 필드 이름을 지정할 수 있습니다. 가중치는 다른 필드를 기준으로 특정 필드의 중요도를 나타내는 데 사용됩니다. |
| `functions` | 선택 사항입니다. 점수 매기기 함수는 필터링 가능한 필드에만 적용할 수 있습니다. |
| `type` | 점수 매기기 함수의 필수 항목으로, 사용할 함수의 유형을 나타냅니다. 유효한 값은 `magnitude`, `freshness`, `distance` 또는 `tag`입니다. 각 점수 매기기 프로필에 둘 이상의 함수를 포함할 수 있습니다. 함수 이름은 소문자여야 합니다. |
| `boost` | 점수 매기기 함수의 필수 항목으로, 원점수의 승수로 사용되는 양수입니다. 값이 1일 수는 없습니다. |
| `fieldName` | 점수 매기기 함수의 필수 항목으로, 점수 매기기 함수는 인덱스 필드 컬렉션의 일부이며 필터링 가능한 필드에만 적용할 수 있습니다. 또한 각 함수 유형에서는 추가적인 제한이 적용됩니다. 예를 들어 freshness는 datetime 필드에, magnitude는 integer/double 필드에, distance는 location 필드에, tag는 string/string collection 필드에 사용할 수 있습니다. 필드는 함수 정의당 하나만 지정할 수 있습니다. 예를 들어 같은 프로필에서 magnitude를 두 번 사용하려면 각 필드에 하나씩 두 개의 magnitude 정의를 포함해야 합니다. |
| `interpolation` | 점수 매기기 함수의 필수 항목으로, 범위 시작부터 범위 끝까지 점수 상승이 증가하는 기울기를 정의합니다. 유효한 값은 `linear`(기본값), `constant`, `quadratic` 또는 `logarithmic`입니다. 자세한 내용은 [보간 설정](#bkmk_interpolation)을 참조하세요. |
| `magnitude` | magnitude 점수 매기기 함수는 숫자 필드의 값 범위를 기준으로 순위를 변경하는 데 사용됩니다. 이 함수를 사용하는 가장 일반적인 몇 가지 예는 다음과 같습니다. <ul><li>별 등급: "별 등급" 필드 내의 값을 기준으로 점수 매기기를 변경합니다. 관련 항목이 두 개이면 등급이 높은 항목이 먼저 표시됩니다.</li><li>이익: 소매업체에서 두 개의 관련 문서 중 이익이 더 높은 문서를 상승시키려는 경우를 예로 들 수 있습니다.</li><li>클릭 횟수: 제품이나 페이지에 대한 클릭 동작을 추적하는 응용 프로그램의 경우 magnitude를 사용하여 가장 많은 트래픽을 받는 항목을 상승시킬 수 있습니다.</li><li>다운로드 횟수: 다운로드를 추적하는 응용 프로그램의 경우 magnitude 함수를 사용하면 다운로드 수가 가장 많은 항목을 상승시킬 수 있습니다.</li></ul> |
| `magnitude:boostingRangeStart` | magnitude의 점수를 매길 범위의 시작 값을 설정합니다. 이 값은 정수이거나 부동 소수점 숫자여야 합니다. 별 등급 1~4에서 시작 값은 1이고 50%를 초과하는 이익의 경우에는 시작 값이 50입니다. |
| `magnitude:boostingRangeEnd` | magnitude의 점수를 매길 범위의 끝 값을 설정합니다. 이 값은 정수이거나 부동 소수점 숫자여야 합니다. 별 등급 1~4에서 끝 값은 4가 됩니다. |
| `magnitude:constantBoostBeyondRange` | 유효한 값은 true 또는 false(기본값)입니다. 값을 true로 설정하면 대상 필드의 값이 범위 상한보다 큰 문서에 완전 상승이 계속해서 적용됩니다. 값을 false로 설정하는 경우에는 대상 필드의 값이 범위를 벗어나는 문서에 이 함수의 상승이 적용되지 않습니다. |
| `freshness` | freshness 점수 매기기 함수는 DateTimeOffset 필드의 값을 기준으로 항목의 순위 점수를 변경하는 데 사용됩니다. 예를 들어 보다 최근 날짜의 항목에 오래된 날짜의 항목보다 더 높은 순위를 지정할 수 있습니다. (참고로 현재 날짜에 더 가까운 항목 같은 미래 날짜를 가진 일정 이벤트 등의 항목을 더 먼 미래의 항목보다 높게 순위를 설정할 수 있습니다.) 현재 서비스 릴리스에서는 범위의 한쪽 끝이 현재 시간으로 고정됩니다. 반대쪽은 `boostingDuration`을(를) 기반으로 하는 과거의 시간입니다. 나중에 시간 범위를 늘리려면 음수 `boostingDuration`을(를) 사용합니다. 상승 기준이 최대 범위에서 최소 범위로 변경되는 비율은 점수 매기기 프로필에 적용되는 보간을 통해 결정됩니다(아래 그림 참조). 적용된 상승 계수의 방향을 바꾸려면 1보다 작은 상승 계수를 선택합니다. |
| `freshness:boostingDuration` | 특정 문서에 대해 상승을 중지할 만료 기간을 설정합니다. 구문 및 예제는 다음 섹션의 [boostingDuration 설정][#bkmk\_boostdur]을 참조하세요. |
| `distance` | distance 점수 매기기 함수는 참조 지리적 위치와의 거리를 기준으로 문서 점수를 변경하는 데 사용됩니다. 참조 위치는 매개 변수에서 쿼리의 일부로 lon, lat 인수로 제공됩니다(`scoringParameter` 쿼리 매개 변수 사용). |
| `distance:referencePointParameter` | 참조 위치로 사용하도록 쿼리에 전달할 매개 변수입니다. scoringParameter는 쿼리 매개 변수입니다. 쿼리 매개 변수의 설명은 [문서 검색](search-api-2015-02-28-preview.md#SearchDocs)을 참조하세요. |
| `distance:boostingDistance` | 참조 위치로부터 상승 범위가 종료되는 거리(km)를 설정합니다. |
| `tag` | tag 점수 매기기 함수는 문서 및 검색 쿼리의 태그를 기반으로 문서 점수에 영향을 주는 데 사용됩니다. 검색 쿼리와 공통적인 태그가 있는 문서가 상승됩니다. 검색 쿼리에 대한 태그는 각 검색 요청에서 점수 매기기 매개 변수로 제공됩니다(`scoringParameter` 쿼리 매개 변수 사용). |
| `tag:tagsParameter` | 특정 요청에 대한 태그를 지정하도록 쿼리에 전달할 매개 변수입니다. `scoringParameter`은(는) 쿼리 매개 변수입니다. 쿼리 매개 변수의 설명은 [문서 검색](search-api-2015-02-28-preview.md#SearchDocs)을 참조하세요. |
| `functionAggregation` | 선택 사항입니다. 함수를 지정할 때만 적용됩니다. 유효한 값은 `sum`(기본값), `average`, `minimum`, `maximum` 또는 `firstMatching`입니다. 검색 점수는 여러 함수를 비롯한 여러 변수에서 계산되는 단일 값입니다. 이 특성은 모든 함수의 상승이 단일 집계 상승으로 결합된 다음 기본 문서 점수에 적용되는 방식을 나타냅니다. 기본 점수는 문서 및 검색 쿼리에서 계산되는 tf-idf 값을 기준으로 합니다. |
| `defaultScoringProfile` | 검색 요청을 실행할 때 점수 매기기 프로필이 지정되어 있지 않으면 기본 점수 매기기(tf-idf만)가 사용됩니다. 여기서 기본 점수 매기기 프로필 이름을 설정할 수 있으며 그러는 경우 Azure 검색은 검색 요청에 특정 프로필이 지정되어 있지 않으면 해당 프로필을 사용합니다. |

<a name="bkmk_interpolation"></a>
## 보간 설정

보간을 사용하면 범위 시작부터 범위 끝까지 점수 상승이 증가하는 기울기를 정의할 수 있습니다. 다음과 같은 보간을 사용할 수 있습니다.

  - `Linear`: max 및 min 범위 내의 항목에 대해 항목에 적용되는 상승의 값이 지속적으로 감소합니다. Linear는 점수 매기기 프로필의 기본 보간입니다.
  - `Constant` 시작 범위와 끝 범위 내의 항목에 대해 일정한 상승이 순위 결과에 적용됩니다.
  - `Quadratic`: 상승 값이 일정하게 감소하는 Linear 보간과는 달리 Quadratic 보간에서는 처음에 값이 조금씩 감소했다가 끝 범위가 가까워지면 값이 훨씬 큰 간격으로 감소합니다. 이 보간 옵션은 tag 점수 매기기 함수에서 허용되지 않습니다.
  - `Logarithmic`: 상승 값이 일정하게 감소하는 Linear 보간과는 달리 Logarithmic 보간에서는 처음에 값이 크게 감소했다가 끝 범위가 가까워질수록 값이 훨씬 작은 간격으로 감소합니다. 이 보간 옵션은 tag 점수 매기기 함수에서 허용되지 않습니다.

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>
## boostingDuration 설정

`boostingDuration`은 freshness 함수의 특성입니다. 이 특성을 사용하여 특정 문서에 대해 상승이 중지되는 만료 기간을 설정합니다. 예를 들어 프로모션 기간 10일 동안 특정 제품 라인이나 브랜드를 상승시키려는 경우 해당 문서에 대해 10일의 기간을 "P10D"로 지정합니다. 또는 다음 주에 예정된 이벤트를 더 중요시하려면 "-P7D"를 지정합니다.

`boostingDuration`의 형식은 XSD "dayTimeDuration" 값(ISO 8601 기간 값의 제한된 하위 집합)으로 지정해야 합니다. 해당 패턴은 `[-]P[nD][T[nH][nM][nS]]`입니다.

다음 표에 여러 예제가 나와 있습니다.

| 기간 | boostingDuration |
|----------|------------------|
| 1일 | "P1D" |
| 2일 12시간 | "P2DT12H" |
| 15분 | "PT15M" |
| 30일, 5시간, 10분, 6.334초 | "P30DT5H10M6.334S" |

더 많은 예제를 보려면 [XML 스키마: Datatypes(W3.org 웹 사이트)](http://www.w3.org/TR/xmlschema11-2/)를 참조하세요.

**참고 항목** MSDN의 [Azure 검색 서비스 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) MSDN의 <br/> [인덱스 만들기(Azure 검색 API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) MSDN의 <br/> [검색 인덱스에 점수 매기기 프로필 추가](http://msdn.microsoft.com/library/azure/dn798928.aspx) <br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png

<!---HONumber=AcomDC_0914_2016-->