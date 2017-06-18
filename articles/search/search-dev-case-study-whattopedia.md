<properties 
	pageTitle="Azure 검색 개발자 사례 연구: WhatToPedia가 Microsoft Azure에서 정보 미디어 포털을 구축한 방법 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
	description="개발자를 위한 클라우드 호스팅 검색 서비스인 Azure 검색을 사용하여 정보 포털 및 메타 검색 엔진을 구축하는 방법을 알아봅니다." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="jhubbard"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="08/29/2016" 
	ms.author="heidist"/>

# Azure 검색 개발자 사례 연구

## [WhatToPedia.com](http://whattopedia.com/)이 Microsoft Azure에서 정보 미디어 포털을 구축한 방법

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">주요 개념</font>


우리의 아이디어는 여행객이 미지의 지역에 있을 때 여행 포털에서 여행객을 호텔, 레스토랑 및 엔터테인먼트와 일치시키는 방법과 유사하게 관련성이 높은 범위 지정 검색 환경을 통해 쇼핑객을 판매점과 연결하도록 도와주는 정보 포털을 구축하는 것입니다.

우리가 추구하는 포털은 지정된 시장의 판매점 데이터에 대한 뛰어난 품질의 검색 환경을 제공하여 쇼핑객이 위치 및 기타 판매점에서 제공하는 서비스에 따라 매장을 쉽게 찾을 수 있도록 도와줍니다. 검색 엔진은 초기 데이터 집합으로 구성되지만 시간이 지남에 따라 자신의 비즈니스에 대한 정보를 게시하는 판매점 구독자의 도움으로 더 높은 가치가 축적될 것입니다. 프로모션, 신상품, 인기 있는 브랜드, 사내 전문 서비스 등은 사이트에 가치를 더해 주는 데이터의 예입니다. 판매자가 구독자로 등록하면 이 데이터가 자체 보고되며 검색 모음에 통합됩니다. 광고는 구독 모델과 함께 우리의 새 비즈니스에 대한 수익원을 제공합니다.

검색은 순수 클라우드 플랫폼에서 주된 사용자 상호 작용 모델이 될 것입니다. 확장성 및 비용 절감을 위해 포털 환경부터 원본 제어까지 우리가 수행하는 거의 모든 작업은 온라인 서비스를 통해 이루어집니다. 필요한 기능을 즉시 제공하는 검색 엔진을 사용하면 검색 엔진을 직접 구축 및 관리하지 않고도 검색 응용 프로그램을 신속하게 만들 수 있습니다.

## 구축 내용

WhatToPedia는 스타트업 정보 미디어 회사입니다. 우리는 [WhatToPedia.com](http://whattopedia.com/)을 구축했으며, 2015년 2월 2일 출시를 목표로 현재 북유럽 시장에서 테스트 중입니다. 우리의 소비자층은 주로 관리 및 유지 보수가 간편한 경제적인 온라인 서비스를 필요로 하는 오프라인 매장입니다.

우리의 작업은 뛰어난 온라인 검색 환경을 통해 쇼핑객을 유치하여 도시 또는 인접 지역, 브랜드, 매장 이름, 매장 유형 등에 따라 결과를 향상시키는 것입니다. 쇼핑객 유치는 판매점이 우리의 포털 사이트를 구독하게 만드는 파급 효과가 있습니다. 구독은 유료이며 요금이 저렴합니다.

 ![][7]

구독에 등록한 후에는 판매점이 프로모션, 주요 브랜드 또는 공지 사항에 대한 추가 데이터로 업데이트하여 자신의 기존 프로필(초기에는 우리가 구매 데이터를 바탕으로 생성)을 관리할 수 있습니다. 구사하는 언어, 허용되는 통화, 면세 쇼핑 등의 내부 기능은 이러한 서비스를 찾는 쇼핑객을 보다 잘 유치하도록 자체 보고될 수 있습니다.

## 회사 소개

저는 Thomas Segato(Microsoft Consulting)이며, WhatToPedia의 수석 개발자인 Jesper Boelling과 함께 솔루션을 디자인했습니다.

WhatToPedia는 60,000만 개의 판매점 대부분이 오프라인 SME(중소기업)인 스웨덴에서 새로운 포털 비즈니스를 시험 출시한 신생 기업입니다. 유럽의 쇼핑객은 여러 언어를 구사하고 다양한 통화를 사용하기 때문에 우리는 다국어 쇼핑객을 수용할 수 있는 솔루션을 구축해야 했습니다. 따라서 다국어 요구 사항을 지원하는 검색 엔진이 필요했으며 Azure 검색에서 이를 찾았습니다.

Azure 검색은 우리 프로젝트의 판도를 바꾸어 놓았습니다. Azure 검색을 사용할 수 있기 전까지는 자체 검색 엔진을 구축하는 방법을 고민하는 데 많은 에너지를 소모했습니다. Azure 검색을 온라인 서비스로 채택함으로써 솔루션의 최대 난간인 기술 및 관리 문제가 해결되어 시장 출시 시점이 단축되고 검색 환경이 더욱 강력해졌습니다.

## 구축 방법

우리의 비전은 클라우드 서비스만을 기반으로 하는 완벽한 인프라를 구축하는 것이었습니다. Microsoft를 전략적 플랫폼으로 선택한 이유는 공동 작업 및 개발 모두에 필요한 서비스, 주문형 확장 및 경제적인 가격을 제공하는 공급자였기 때문입니다.
 
### 대략적인 구성 요소

우리는 사이트가 아니라 비즈니스를 구축했습니다. 전체 작업을 지원하기 위해 광범위한 도구와 응용 프로그램이 필요했습니다. 우리는 모든 사이트 관련 작업 및 저장소에 Microsoft Azure를 도입했을 뿐만 아니라 개발에 Visual Studio 및 Visual Studio Team Services를 도입하고, 원본 제어 및 스크럼 관리에 TFS(Team Foundation Service) Online을 도입하고, 통신 및 공동 작업에 Office 365를 도입했습니다. Visual Studio를 통해 IDE는 생산성을 향상시키는 TFS Online과의 통합으로 Azure에 대한 직접 프로비전닝을 제공했습니다.

아래 다이어그램에서는 WhatToPedia 인프라에서 사용되는 대략적인 구성 요소를 보여 줍니다.

   ![][8]

### Microsoft Azure 사용 방법

위 다이어그램의 녹색 상자를 살펴보면 WhatToPedia 솔루션이 다음 서비스를 기반으로 하고 있음을 알 수 있습니다.

- [Azure 검색](https://azure.microsoft.com/services/search/)
- [MVC 4를 사용하는 Azure 웹 사이트](https://azure.microsoft.com/services/websites/)
- [예약된 작업을 위한 Azure WebJobs](../app-service-web/websites-webjobs-resources.md)
- [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)
- [Azure Blob 저장소](https://azure.microsoft.com/services/storage/)
- [SendGrid 전자 메일 서비스](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

솔루션의 핵심은 데이터와 검색입니다. Reseller 공급자에서 최종 소비자로의 데이터 흐름은 다음과 같습니다.

  ![][9]

주 데이터 저장소는 Azure SQL 데이터베이스의 판매점 및 계정 데이터입니다. 이는 초기 데이터 집합과 시간이 지남에 따라 추가되는 판매점 관련 데이터로 구성됩니다. 우리는 Azure WebJob을 사용하여 SQL 데이터베이스에서 Azure 검색의 검색 모음으로 업데이트를 게시합니다.

### 프레젠테이션 계층

이 포털은 MVC4와 [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29)으로 구현된 Azure 웹 사이트입니다. MVC를 선택한 이유는 HTML에 대한 접근 방법이 ASP.NET 양식 기반 개발보다 훨씬 깔끔하기 때문입니다. 또한 여러 장치용 앱을 만들고 여러 모바일 플랫폼을 유지 관리해야 하는 필요성을 없애기 위해 모든 장치 및 플랫폼을 지원하는 Twitter Bootstrap을 선택했습니다.

### 인증, 권한 및 중요한 데이터

쇼핑객은 사이트를 익명으로 검색합니다. 따라서 쇼핑객에 대한 로그인 요구 사항이 없으며 소비자 데이터가 저장되지도 않습니다.

그러나 판매점은 다릅니다. 공용 프로필 정보, 대금 청구 정보 및 사이트에 노출할 미디어 콘텐츠가 저장됩니다. 사이트를 구독한 모든 판매점은 구독자의 프로필을 업데이트하기 전에 사용자를 인증하는 데 필요한 사용자 로그인을 가져옵니다. 모든 구독자 데이터는 Azure SQL 데이터베이스 및 Azure BLOB 저장소에 안전하게 저장됩니다. 인증 모델은 .NET 양식 기반 인증을 기반으로 합니다. 이 방법을 선택한 이유는 간소성 때문입니다. 다른 방법과 달리 역할, UI 지원 및 기타 불필요한 기능이 필요 없습니다.

판매점이 해당 판매점에 속한 데이터만 볼 수 있도록 하기 위해 각 판매점에 대한 판매점 ID를 만들었으며, 이 ID는 이후에 판매점 관련 데이터가 포함된 모든 읽기 및 쓰기 작업에서 사용됩니다. 이 방법을 사용하면 개별 판매점에 데이터베이스 권한을 부여할 필요가 없습니다. 모든 판매점은 판매점 ID를 데이터 격리 기술로 사용하는 단일 데이터베이스 역할을 통해 시스템과 상호 작용합니다.

우리의 비즈니스는 다운스트림 효과(판매점에 대한 더 많은 비즈니스를 유도하여 광고 및 구독에 대한 인센티브 생성)와 관련이 있으므로 웹을 통해 구매를 처리할 수 있습니다. 따라서 사이트에서 쇼핑 카트를 찾을 필요가 없으므로 보안 요구 사항이 간소화됩니다.

또한 대금 청구 및 외상 매입금 작업을 아웃소싱하는 것도 간소화되었습니다. 소비자 지불 정보를 타사([SveaWebPay](http://www.sveawebpay.se/))에 직접 라우팅하므로 데이터 저장소에 중요한 데이터를 저장하고 보호하는 것과 관련된 위험이 줄어듭니다.

### 검색 엔진

우리 솔루션 핵심은 Azure 검색 서비스를 기반으로 하는 검색 엔진입니다. 처음에는 사용자 지정 검색 엔진을 구축했지만 이 과정에서 복잡성과 매우 많은 노력이 필요하다는 것을 깨닫고 다른 대안을 찾기 시작했습니다.

우리에게 가장 중요한 기본 기능은 다음과 같습니다.

- 필터
- 패싯 탐색
- 결과 강화
- AJAX를 통한 페이징

우리는 인터넷 검색에서 찾은 [TechEd Europe의 심층 분석](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)이라는 비디오를 통해 Azure 검색을 시도해 볼 영감을 얻었습니다.

비디오를 시청한 후 이를 기반으로 프로토타입을 만들 준비가 완료되었습니다. MVC에 데이터 모델이 이미 있었기 때문에 프로토타입 만들기가 간단했습니다. 즉, 데이터에 검색 가능한 용어가 포함되어 있고 데이터를 정렬, 패싯 및 필터링할 방법에 대한 요구 사항을 이미 마련해 두었기 때문입니다.

프로토타입을 구축한 방법은 다음과 같습니다.

**Azure 검색 서비스 구성**

1. Azure 클래식 포털에 로그인하여 검색 서비스를 구독에 추가했습니다. 공유 버전(구독에 무료로 제공)을 사용했습니다.
2. 인덱스를 만듭니다. 프로토타입에는 포털 UI를 사용하여 검색 필드를 정의하고 점수 매기기 프로필을 만들었습니다. 점수 매기기 프로필은 위치 데이터인 국가 | 도시 | 주소(점수 매기기 프로필 추가 참조)를 기반으로 합니다.
3. 서비스 URL 및 admin api-key를 구성 파일에 복사합니다. 이 키는 포털의 검색 서비스 페이지에 있으며, 서비스를 인증하는 데 사용됩니다.
	
**검색 인덱서 작업 개발 – Windows 콘솔**

1. 데이터베이스에서 모든 판매점을 읽습니다.
2. Azure 검색 서비스 API를 호출하여 판매점을 하나씩 업로드합니다(http://msdn.microsoft.com/library/azure/dn798930.aspx 참조).
3. 데이터베이스에서 판매점을 증분 인덱싱하는 속성을 설정합니다. 이를 위해 각 프로필의 인덱스 상태(인덱싱 여부에 상관없음)를 저장하는 'indexer' 필드를 추가했습니다.

인덱서 작업을 작성하는 코드 조각은 부록을 참조하세요.

**검색 웹 포털 개발 – MVC**

1. Azure 검색 서비스를 호출하여 검색에서 모든 문서를 가져옵니다(http://msdn.microsoft.com/library/azure/dn798927.aspx 참조).
2. 검색 서비스 응답에서 다음을 추출합니다(json.net http://james.newtonking.com/json 사용).
   - 결과
   - 패싯
   - 결과 개수
   - 검색 결과, 패싯 및 개수를 표시하는 사용자 인터페이스를 개발합니다(이미 가지고 있음).

Azure 검색에서 결과를 가져오는 데 사용한 코드는 다음과 같습니다.

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**위치에 따라 상위에 배치**

프로토타입에서 확인할 가장 중요한 요구 사항은 쿼리에 위치 검색 키워드를 추가하는 것과 관련이 있습니다. 포털에서는 사용자가 검색 쿼리에 도시 이름을 입력한 경우 지정된 도시에 있는 판매점이 설명에 있는 도시 키워드를 가진 판매점보다 높은 순위로 지정되어야 합니다. 이 요구 사항을 위해 점수 매기기 프로필을 사용하여 도시 필드를 다른 필드보다 높은 순위로 지정했습니다.

**여러 언어 지원**

올바른 검색 결과를 올바른 언어로 표시하고 같은 결과를 여러 언어로 찾을 수 있는 옵션을 제공해야 했습니다. 이 문제에는 다음 두 가지 측면이 있습니다.

- 여러 언어의 단어 검색
- 올바른 언어로 검색 결과 표시

우리는 지역화된 텍스트가 있는 각 언어의 문서와 해당 언어의 속성을 추가하여 프레젠테이션 부분을 해결했습니다. 사용자가 검색 용어를 입력한 경우 `$filter` 식을 사용하여 사용자가 선택한 언어를 필터링했습니다.

각 문서에는 컬렉션 형식을 기반으로 하는 "cities"라는 숨겨진 속성이 있습니다. 이 속성은 사용자가 여러 언어로 검색할 수 있도록 도시 이름을 모든 언어로 저장합니다.

###데이터 저장소

모든 데이터(프로필, 구독 및 계정)는 SQL 데이터베이스에 저장됩니다. 판매점에서 제공하는 이미지와 비디오를 포함하여 모든 미디어 파일은 Azure BLOB 저장소에 저장됩니다. 개별 BLOB 저장소를 사용하면 파일 업로드 효과가 격리됩니다. 즉, 파일이 웹 사이트와 혼합되지 않으므로 파일을 추가할 때마다 사이트를 다시 빌드할 필요가 없습니다.

이 저장소 디자인의 중요한 이점은 여러 개발자가 단일 개발 저장소를 공유할 수 있다는 점입니다. WhatToPedia 프로젝트의 요구 사항 중 하나는 판매점 데이터, 이미지 및 비디오를 포함하여 개발 환경을 15분 이내에 만들 수 있도록 하는 것이었습니다. TFS Online에서 최신 데이터를 가져오고, SQL 스크립트를 실행하고, 가져오기 작업을 실행하여 전체 환경을 신속하게 구축할 수 있습니다. 이 방법은 준비 프로세스도 향상시킵니다.

###웹 작업

Azure WebJobs를 사용하여 데이터를 인덱스에 업데이트합니다. 검색 인덱서 작업을 만들어 인덱싱 부분을 솔루션에 매우 쉽게 통합할 수 있었습니다. 인덱서 작업을 적용하기 위해 수행한 코드 변경은 인덱스 상태를 나타내는 `Indexed` 필드를 데이터 모델에 추가한 것뿐이었습니다. 새 프로필을 추가하거나 업데이트할 때마다 `Indexed` 필드는 false로 설정됩니다. 이는 판매점이 포털을 통해 프로필 데이터를 변경한 경우에도 마찬가지입니다.

작업에서는 `Indexed`가 false로 설정된 모든 행을 찾습니다. 행을 찾으면 문서가 Azure 검색에 게시되고 `Indexed` 필드가 true로 설정됩니다. 데이터 추가 및 업데이트는 실제로 Azure 검색 서비스에서 처리하기 때문에 이에 대한 계획을 세울 필요가 없었습니다. 이미 있는 문서를 추가하면 서비스에서 자동으로 업데이트를 수행합니다.

모든 웹 작업은 Azure 웹 사이트에 ZIP 파일로 업로드하고, 압축을 푼 다음, 예약할 수 있는 콘솔 응용 프로그램으로 개발되었습니다.

작업은 예약된 웹 작업으로 5분마다 실행되도록 예약됩니다. 서비스에서 3,000개의 문서를 업로드하는 데 약 3분 정도 걸리는 것으로 계산되었으며, 이는 우리의 요구 사항에 부합했습니다.

> [AZURE.NOTE] 최근에 Azure 검색에 도입된 프로토타입 인덱서 기능이 있습니다. 이 기능은 우리의 첫 번째 릴리스에서 사용하기에는 너무 늦게 제공되었지만 인덱서 작업을 사용한 것과 동일한 문제, 즉 데이터 로드 작업 자동화를 해결합니다.


###백업 전략

치명적인 오류에서 개별 트랜잭션 복구에 이르는 다양한 시나리오에서 복구할 수 있는 다중 계층 백업 전략을 개발했습니다. 보호할 자산은 세 가지 데이터, 즉 웹 사이트, 구독자 데이터 및 미디어 파일입니다.

첫째, 웹 사이트 원본 코드를 TFS Online에 유지함으로써 사이트 작동이 중단된 경우 TFS에서 다시 게시하여 사이트를 다시 구축할 수 있습니다.

Azure SQL 데이터베이스의 구독자 데이터는 가장 중요한 자산입니다. 이 데이터는 기본 제공 기능을 사용하여 백업합니다([Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx) 참조). 백업은 일주일에 한 번씩 전체 데이터베이스 백업이 수행되고, 하루에 한 번씩 차등 데이터베이스 백업이 수행되며, 5분마다 트랜잭션 로그 백업이 수행되도록 예약됩니다. 데이터 크기가 지정된 경우 이 솔루션은 우리의 즉각적이고 프로젝션된 데이터 볼륨에 보다 적합합니다.

셋째, 이미지 및 비디오 파일을 Azure BLOB 저장소에 저장합니다. 우리는 이 데이터에 대한 최종 백업 계획을 여전히 평가하는 중이며, Cloudberry Explorer for Azure를 잠재적 솔루션으로 고려하고 있습니다. 현재는 WebJob을 사용하여 이미지 및 비디오를 다른 위치에 복사하고 있습니다.

##습득한 지식

우리에게는 데이터가 이미 있었기 때문에 개념 증명을 쉽게 설정할 수 있었습니다. 몇 시간 이내에 패싯과 카운터, 페이징, 순위가 매겨진 프로필 및 검색 결과로 프로토타입을 만들었습니다. 검색 결과가 매우 정확하여 최종 소비자에게 제공되는 일부 필터를 제거하기로 결정했습니다.

가장 놀라운 점은 Azure 검색을 습득한 속도와 이를 적용한 범위였습니다. 프런트 엔드 응용 프로그램(및 새 WebJob)에서 500개의 코드 줄을 3개의 코드 줄로 바꿔 개념 증명을 몇 시간 내에 설정하여 더 나은 결과를 얻을 수 있었습니다(아래 참고 참조).

이전 코드는 페이징, 개수 및 검색에 표준인 기타 동작을 구현했습니다. Azure 검색을 사용하니 결과에 검색 적중 항목, 패싯, 페이지 데이터, 개수 등 필요한 모든 항목이 포함되어 직접 제공할 필요가 없어졌습니다. 또한 원래 솔루션에 없던 향상된 패싯 탐색 및 기본 제공 패싯 탐색 기능도 포함되었습니다.

구현 과정에서 가장 큰 과제는 미리 보기 버전이기 때문에 정보 찾기 및 공유 환경이 까다롭다는 점이었습니다. 몇 가지 정보를 연관지어 생각한 후 우리는 Azure 검색 서비스는 REST API 및 JSON 데이터 형식 때문에 사용하기 매우 간편하다는 사실을 알았습니다. JQuery JSON.Net과 같은 대부분의 오픈 소스 플러그 인에서 프레임워크를 직접 호출할 수 있으며, Fiddler와 같은 도구를 사용하여 신속하게 실험 및 디버그할 수 있습니다.

> [AZURE.NOTE] 데이터를 준비된 상태로 유지하는 것 외에 프로토타입을 이미 구축한 경우 검색 기술의 작동 방식을 이해하여 생산성을 높이고 기본 제공 기능을 보다 효율적으로 활용할 수 있습니다. 검색 쿼리 생성, 패싯 탐색, 필터 등을 늘려야 하는 경우 프로토타이핑 시간이 더 오래 걸립니다.

###검색 프레젠테이션 페이지의 패싯 제어

개념 증명 중에 배운 한 가지는 패싯을 사전에 신중하게 계획해야 한다는 점이었습니다. 많은 데이터를 솔루션에 로드한 후 사용자에게 제공할 패싯 양이 급상승한다는 것을 알았습니다.

패싯 개수 매개 변수를 제한하여 이 문제를 해결했습니다. 개수 매개 변수는 사용자에게 반환되는 패싯 수를 강제로 제한합니다. 개수 매개 변수에 대한 설명이 포함된 링크는 [여기](search-faceted-navigation.md)에서 확인할 수 있습니다.

###작업 예약을 위한 WebJobs

Azure 검색만 놀라움을 선사한 것이 아니었습니다. 우리는 WebJobs를 사용하여 Azure 검색에 대한 데이터 로드 작업을 자동화하는 것이 이전 방법보다 훨씬 뛰어나며, 여기에는 예약된 작업에서 Windows Scheduler를 실행하는 전용 VM을 사용하여 검색 인덱스를 업데이트하는 기능이 포함되어 있다는 것을 발견했습니다. WebJobs는 구성이 보다 간단하고 디버그하기 쉬우며 전용 VM보다 훨씬 저렴합니다.

###이미지 업데이트를 위한 Azure BLOB 저장소 탐색기

우리는 [Azure BLOB 저장소 탐색기](https://azurestorageexplorer.codeplex.com/)(codeplex에서 사용 가능)를 사용할 경우 사이트에서 이미지 및 비디오 업데이트를 관리하는 것이 매우 편리하다는 것을 알았습니다. 이를 개발자 도구로 사용하여 기본 사이트의 일부인 이미지 및 비디오를 수동으로 업데이트합니다. 이는 변경 내용을 포털에 배포하는 것보다 훨씬 유연하며, 이미지를 업데이트할 때마다 전체 테스트를 반복해야 할 필요가 없습니다.

##맺음말

사례를 공유할 수 있게 해준 WhatToPedia의 동료들에게 감사드립니다.

이 사례 연구가 도움이 되시기를 바랍니다. Azure 검색을 사용하려는 경우 도움이 되는 몇 가지 권장 리소스는 다음과 같습니다.

- [Azure 검색 전용 MSDN 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow에도 태그가 있음](http://stackoverflow.com/questions/tagged/azure-search)
- [Azure.com의 설명서 페이지](https://azure.microsoft.com/documentation/services/search/)
- [MSDN의 Azure 검색 설명서](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##부록: 검색 인덱서 WebJob

다음 코드는 프로토타입 구축에 관한 섹션에서 언급한 인덱서를 작성합니다.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=AcomDC_0914_2016-->