<properties
	pageTitle="Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색의 인덱스란 무엇이고 어떻게 사용됩니까?"
	services="search"
	documentationCenter=""
authors="ashmaka"
/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="ashmaka"/>

# Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [개요](search-what-is-an-index.md)
- [포털](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST (영문)](search-create-index-rest-api.md)

## 인덱스란?

*인덱스*는 Azure 검색 서비스에서 사용되는 *문서* 및 기타 구조의 지속형 저장소입니다. 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 예를 들어 전자 상거래 소매점은 판매하는 각 항목에 대한 문서를 포함할 수 있으며 뉴스 조직은 각 기사에 대한 문서를 포함할 수 있습니다. 이러한 개념을 익숙한 데이터베이스와 동일하게 매핑: *인덱스*는 *테이블*과 개념적으로 유사하며 *문서*는 테이블의 *행*과 거의 비슷합니다.

문서를 추가/업로드하고 검색 쿼리를 Azure 검색에 제출하는 경우 요청을 검색 서비스의 특정 인덱스에 제출합니다.

## Azure 검색 인덱스에서 필드 형식 및 속성

스키마를 정의할 때 인덱스에서 각 필드의 이름, 형식 및 특성을 지정해야 합니다. 필드 형식은 해당 필드에 저장된 데이터를 분류합니다. 특성은 개별 필드에 설정되어 필드를 사용하는 방법을 지정합니다. 다음 표에서는 지정할 수 있는 형식 및 특성을 열거합니다.


### 필드 형식
|형식|설명|
|------------|-----------|
|*Edm.String*|전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 텍스트입니다(단어 분리, 형태소 분석 등).|
|*Collection(Edm.String)*|전체 텍스트 검색을 위해 선택적으로 토큰화할 수 있는 문자열 목록입니다. 컬렉션에 있는 항목 수에 이론적인 상한은 없지만 페이로드 크기의 16MB 상한이 컬렉션에 적용됩니다.|
|*Edm.Boolean*|true/false 값을 포함합니다.|
|*Edm.Int32*|32비트 정수 값입니다.|
|*Edm.Int64*|64비트 정수 값입니다.|
|*Edm.Double*|배정밀도 숫자 데이터입니다.|
|*Edm.DateTimeOffset*|날짜 시간 값을 OData V4 형식으로 표현합니다(예: `yyyy-MM-ddTHH:mm:ss.fffZ` 또는 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|전 세계의 지리적 위치를 나타내는 지점입니다.|

Azure 검색의 [지원되는 데이터 형식에 대한 자세한 내용은 MSDN에서](https://msdn.microsoft.com/library/azure/dn798938.aspx) 확인할 수 있습니다.



### 필드 특성
|특성|설명|
|------------|-----------|
|*키*|문서 조회에 사용하는 각 문서의 고유 ID를 제공하는 문자열입니다. 모든 인덱스에는 하나의 키가 있어야 합니다. 필드 한 개만 키가 될 수 있으며, 이 필드 형식을 Edm.String으로 설정해야 합니다.|
|*조회 가능*|검색 결과에서 필드를 반환할 수 있는지 여부를 지정합니다.|
|*필터링 가능*|필드를 필터 쿼리에 사용하도록 허용합니다.|
|*정렬 가능*|쿼리에서 이 필드를 사용하여 검색 결과를 정렬할 수 있습니다.|
|*패싯 가능*|사용자 자기 주도형 필터링을 위해 필드를 [패싯 탐색](search-faceted-navigation.md) 구조에 사용할 수 있습니다. 일반적으로 여러 문서(예를 들어 단일 브랜드 또는 서비스 범주에 속하는 여러 문서)를 그룹화하는 데 사용할 수 있는 반복되는 값을 포함한 필드는 패싯으로 가장 적합합니다.|
|*검색 가능*|전체 텍스트 검색 가능 필드로 표시합니다.|

Azure 검색의 [인덱스 특성에 대한 자세한 내용은 MSDN에서](https://msdn.microsoft.com/library/azure/dn798941.aspx) 확인할 수 있습니다.



## 인덱스 스키마를 정의하기 위한 지침

인덱스를 설계할 때 계획 단계에서 각 의사 결정에 대해 신중하게 생각합니다. 인덱스를 각 필드로 디자인하는 경우 검색 사용자 환경 및 비즈니스 요구를 [적절한 특성](https://msdn.microsoft.com/library/azure/dn798941.aspx)으로 할당해야 한다는 점을 염두하는 것이 중요합니다. 배포된 후 인덱스를 변경하려면 데이터를 다시 작성하고 다시 로드해야 합니다.


데이터 저장소 요구 사항이 시간이 지남에 따라 변하는 경우 파티션을 추가하거나 제거하여 용량을 늘리거나 줄일 수 있습니다. 자세한 내용은 [Azure에서 검색 서비스 관리](search-manage.md) 또는 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->