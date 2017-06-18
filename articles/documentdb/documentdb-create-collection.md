<properties 
	pageTitle="DocumentDB 데이터베이스 컬렉션 만들기 | Microsoft Azure" 
	description="Azure DocumentDB용 온라인 서비스 포털, 클라우드 기반 NoSQL 문서 데이터베이스를 사용하여 JSON 문서 컬렉션을 만드는 방법을 알아봅니다. 지금 무료 평가판을 사용해보세요." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="mimig"/>

# Azure 포털을 사용하여 DocumentDB 컬렉션을 만드는 방법

Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), [데이터베이스](documentdb-create-database.md), 컬렉션 및 문서가 있어야 합니다. 이 토픽에서는 Azure 포털에서 DocumentDB 컬렉션을 만드는 방법을 설명합니다.

컬렉션이 무엇인지 모르시겠습니까? [DocumentDB 컬렉션이란 무엇인가요?](#what-is-a-documentdb-collection)를 참조하세요.

1.  [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB(NoSQL)**를 클릭한 다음 **DocumentDB(NoSQL)** 블레이드에서 컬렉션을 추가할 계정을 선택합니다.

    ![이동 표시줄에서 DocumentDB 계정을, DocumentDB 계정 블레이드에서 계정을, DocumentDB 계정 블레이드와 데이터베이스 렌즈에서 데이터베이스를 강조 표시한 스크린샷](./media/documentdb-create-collection/docdb-database-creation-1-2.png)
    
    이동 표시줄에 **DocumentDB (NoSQL)**이 표시되지 않으면 **서비스 더 보기**를 클릭한 다음 **DocumentDB (NoSQL)**를 클릭합니다. 나열된 계정이 없는 경우 [DocumentDB 계정을 만들](documentdb-create-account.md)어야 합니다.

2. 선택한 계정에 대한 **DocumentDB 계정** 블레이드의 **데이터베이스** 렌즈에서 컬렉션을 추가할 데이터베이스를 선택합니다.

    ![이동 표시줄에서 DocumentDB 계정을, DocumentDB 계정 블레이드에서 계정을, DocumentDB 계정 블레이드와 데이터베이스 렌즈에서 데이터베이스를 강조 표시한 스크린샷](./media/documentdb-create-collection/docdb-database-creation-3.png)

3. **데이터베이스** 블레이드에서 **컬렉션 추가**를 클릭합니다.

	![데이터베이스 블레이드의 컬렉션 추가 단추, 컬렉션 추가 블레이드의 설정 및 확인 단추를 강조 표시하는 스크린샷 - DocumentDB용 Azure 포털 - NoSQL JSON 데이터베이스용 클라우드 기반 데이터베이스 작성자](./media/documentdb-create-collection/docdb-collection-creation-4.png)

4. **컬렉션 추가** 블레이드의 **ID** 상자에 새로운 컬렉션에 대한 ID를 입력합니다. 컬렉션 이름은 1~255자 사이여야 하며 `/ \ # ?` 또는 후행 공백을 포함할 수 없습니다. 이름의 유효성이 검사되면 ID 상자에 녹색 확인 표시가 나타납니다.

	![데이터베이스 블레이드의 컬렉션 추가 단추, 컬렉션 추가 블레이드의 설정 및 확인 단추를 강조 표시하는 스크린샷 - DocumentDB용 Azure 포털 - NoSQL JSON 데이터베이스용 클라우드 기반 데이터베이스 작성자](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

5. 기본적으로 **가격 책정 계층**은 컬렉션에 대한 처리량 및 저장소를 사용자 지정할 수 있도록 **표준**으로 설정됩니다. 가격 책정 계층에 대한 자세한 내용은 [DocumentDB의 성능 수준](documentdb-performance-levels.md)을 참조하세요.

6. **단일 파티션** 또는 **분할된** 경우 컬렉션에 **분할 모드**를 선택합니다.

    **단일 파티션**의 예약된 저장소 용량은 10GB이며 처리량 수준은 초당 400-10,000개 요청(RU/s) 단위까지 가능합니다. 하나의 RU는 1KB 문서를 읽는 처리량에 해당합니다. 요청 단위에 대한 자세한 내용은 [요청 단위](documentdb-request-units.md)를 참조하세요.

    **분할된 컬렉션**은 확장을 통해 여러 파티션에 걸쳐 제한 없는 양의 저장소를 처리할 수 있고 처리량 수준은 초당 10,100RU/s까지 가능합니다. 포털에서 예약할 수 있는 가장 큰 저장소는 250GB이며 예약할 수 있는 최대 처리량은 250,000RU/s입니다. 할당량을 증가시키려면 [증가된DocumentDB 계정 할당량 요청](documentdb-increase-limits.md)에서 설명한 대로 요청을 제출합니다. 분할된 컬렉션에 대한 자세한 내용은 [단일 파티션 및 분할된 컬렉션](documentdb-partition-data.md#single-partition-and-partitioned-collections)을 참조하세요.

    표준 가격 책정 계층을 사용하는 경우 언제든지 이 블레이드로 돌아와 컬렉션의 처리량을 변경할 수 있습니다.

7. 컬렉션에 대한 **처리량**을 선택합니다. 처리량 요구 추정에 대한 자세한 내용은 [처리량 요구 추정](documentdb-request-units.md#estimating-throughput-needs) 및 [Capacity Planner](https://www.documentdb.com/capacityplanner)를 참조하세요.

8. 분할된 컬렉션을 만드는 경우 컬렉션에 대한 **파티션 키**를 선택합니다. 올바른 파티션 키를 선택하는 것은 성능이 뛰어난 컬렉션을 만드는 데 중요합니다. 파티션 키의 선택에 대한 자세한 내용은 [분할 설계](documentdb-partition-data.md#designing-for-partitioning)를 참조하세요.

9. 화면 맨 아래의 **확인**을 클릭하여 새 컬렉션을 만듭니다.

10. 이제 **데이터베이스** 블레이드의 **컬렉션** 렌즈에 새 컬렉션이 표시됩니다.
 
	![데이터베이스 블레이드의 새 컬렉션 스크린샷 - DocumentDB용 Azure 포털 - NoSQL JSON 데이터베이스용 클라우드 기반 데이터베이스 작성자](./media/documentdb-create-collection/docdb-collection-creation-9.png)

## DocumentDB 컬렉션이란 무엇인가요? 

컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. 컬렉션은 컬렉션의 프로비전된 처리량에 따라 [비용](documentdb-performance-levels.md)이 결정되는 청구 가능 엔터티입니다. 컬렉션은 하나 이상의 파티션/서버에 걸쳐 있을 수 있으며 크기가 거의 무제한인 저장소 또는 처리량을 처리하도록 확장할 수 있습니다.

컬렉션은 DocumentDB에서 하나 이상의 물리적 서버에 자동으로 분할됩니다. 컬렉션을 만들 때 초당 요청 단위 및 파티션 키 속성 면에서 프로비전된 처리량을 지정할 수 있습니다. 이 속성 값은 DocumentDB에서 파티션 간에 문서를 분산시키고 쿼리와 같은 요청을 라우팅하는 데 사용됩니다. 파티션 키 값은 저장 프로시저 및 트리거에 대한 트랜잭션 경계 역할도 수행합니다. 각 컬렉션에는 동일 계정에서 다른 컬렉션과 공유되지 않는 해당 컬렉션에 해당되는 예약된 처리량을 갖습니다. 따라서 저장소 및 처리량의 측면에서 응용 프로그램의 규모를 확장할 수 있습니다.

컬렉션은 관계형 데이터베이스의 테이블과 동일하지 않습니다. 컬렉션은 스키마를 적용하지 않으며 사실상 DocumentDB는 스키마를 적용하지 않는 스키마 없는 데이터베이스입니다. 따라서 동일한 컬렉션에서도 다양한 스키마를 사용해서 여러 유형의 문서를 저장할 수 있습니다. 테이블에서와 같은 방식으로 컬렉션을 사용해서 단일 유형의 개체를 저장할 수도 있습니다. 모델 효율성은 쿼리 및 트랜잭션에 데이터가 표시되는 방법에 따라서만 결정됩니다.

## DocumentDB 컬렉션을 만드는 다른 방법

포털에서 컬렉션을 만들지 않아도 됩니다. [DocumentDB SDK](documentdb-sdk-dotnet.md) 및 REST API를 사용하여 컬렉션을 만들 수도 있습니다.

- C# 코드 샘플은 [C# 컬렉션 샘플](documentdb-dotnet-samples.md#collection-examples)을 참조하세요.
- Node.js 코드 샘플은 [Node.js 컬렉션 샘플](documentdb-nodejs-samples.md#collection-examples)을 참조하세요.
- Python 코드 샘플은 [Python 컬렉션 샘플](documentdb-python-samples.md#collection-examples)을 참조하세요.
- REST API 샘플은 [컬렉션 만들기](https://msdn.microsoft.com/library/azure/mt489078.aspx)를 참조하세요.

## 문제 해결

**컬렉션 추가**가 Azure 포털에서 비활성화된 경우 이는 계정이 현재 비활성화됨을 의미하며 일반적으로 월에 모든 혜택 크레딧이 사용될 때 발생합니다.

## 다음 단계

이제 컬렉션이 있으므로 다음 단계는 컬렉션에 문서를 추가하거나 문서를 가져오는 것입니다. 컬렉션에 문서를 추가하는 경우 다음과 같은 몇 가지 옵션이 있습니다.

- 포털의 문서 탐색기를 사용하여 [문서를 추가](documentdb-view-json-document-explorer.md)할 수 있습니다.
- DocumentDB 데이터 마이그레이션 도구를 사용하여 [문서 및 데이터를 가져올](documentdb-import-data.md) 수 있습니다. 이 도구를 통해 SQL Server, MongoDB, Azure 테이블 저장소 및 다른 DocumentDB 컬렉션의 데이터와 JSON 및 CSV 파일을 가져올 수 있습니다.
- 또는 [DocumentDB SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서를 추가할 수도 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. DocumentDB.NET SDK를 사용하여 문서로 작업하는 방법을 보여주는 C# 코드 샘플은 [C# 문서 샘플](documentdb-dotnet-samples.md#document-examples)을 참조하세요. DocumentDB Node.js SDK를 사용하여 문서로 작업하는 방법을 보여주는 Node.js 코드 샘플은 [Node.js 문서 샘플](documentdb-nodejs-samples.md#document-examples)을 참조하세요.

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

<!---HONumber=AcomDC_0914_2016-->