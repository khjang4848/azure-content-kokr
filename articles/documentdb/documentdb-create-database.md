<properties 
	pageTitle="DocumentDB에서 데이터베이스를 만드는 방법 | Microsoft Azure" 
	description="획기적인 속도, 세계적인 규모의 NoSQL 데이터베이스인, Azure DocumentDB용 온라인 서비스 포털을 사용하여 데이터베이스를 만드는 방법을 알아봅니다." 
	keywords="데이터베이스를 만드는 방법" 
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
	ms.date="08/25/2016" 
	ms.author="mimig"/>

# Azure 포털을 사용하여 DocumentDB용 데이터베이스를 만드는 방법

Microsoft Azure DocumentDB를 사용하려면 [DocumentDB 계정](documentdb-create-account.md), 데이터베이스, 컬렉션 및 문서가 있어야 합니다. 이 토픽에서는 Microsoft Azure 포털에서 DocumentDB 데이터베이스를 만드는 방법을 설명합니다. 여러 SDK 중 하나를 사용하여 데이터베이스를 만드는 방법에 대한 자세한 내용은 [DocumentDB 데이터베이스를 만드는 다른 방법](#other-ways-to-create-a-documentdb-database)을 참조하세요.

1.  [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB(NoSQL)**를 클릭합니다. **DocumentDB(NoSQL)**가 표시되지 않으면 **서비스 더 보기**를 클릭한 다음 **DocumentDB(NoSQL)**를 클릭합니다.


    ![데이터베이스를 만드는 방법을 보여주는 스크린샷(찾아보기 블레이드의 DocumentDB 계정 및 DocumentDB 계정 블레이드의 DocumentDB 계정이 강조 표시되어 있음)](./media/documentdb-create-database/docdb-database-creation-1-2.png)

2.  **DocumentDB(NoSQL)** 블레이드에서 DocumentDB NoSQL 데이터베이스를 추가할 계정을 선택합니다. 나열된 계정이 없는 경우 [DocumentDB 계정을 만들](documentdb-create-account.md)어야 합니다.

3. **DocumentDB 계정** 블레이드에서 **데이터베이스 추가**를 클릭합니다.

    ![데이터베이스를 만드는 방법을 보여주는 스크린샷(데이터베이스 추가 단추, ID 상자 및 확인 단추가 강조 표시되어 있음)](./media/documentdb-create-database/docdb-database-creation-3-5.png)

4. **데이터베이스 추가** 블레이드에서 새 데이터베이스의 ID를 입력합니다. 이름의 유효성이 검사되면 **ID** 상자에 녹색 확인 표시가 나타납니다. 그런 후 **OK**를 클릭합니다.

    ![데이터베이스를 만드는 방법을 보여주는 스크린샷(데이터베이스 추가 단추, ID 상자 및 확인 단추가 강조 표시되어 있음)](./media/documentdb-create-database/docdb-database-creation-4.png)

5. 이제 **DocumentDB 계정** 블레이드의 **데이터베이스** 렌즈에 새 데이터베이스가 표시됩니다.
 
	![DocumentDB 계정 블레이드의 새 데이터베이스 스크린샷](./media/documentdb-create-database/docdb-database-creation-6.png)

## DocumentDB 데이터베이스를 만드는 다른 방법

포털을 사용하여 데이터베이스를 만들지 않아도 됩니다. 또한 [DocumentDB SDK](documentdb-sdk-dotnet.md) 또는 [REST API](https://msdn.microsoft.com/library/mt489072.aspx)를 사용하여 데이터베이스를 만들 수 있습니다. .NET SDK를 사용한 데이터베이스 작업에 대한 자세한 내용은 [.NET 데이터베이스 예제](documentdb-dotnet-samples.md#database-examples)를 참조하세요. Node.js SDK를 사용한 데이터베이스 작업에 대한 자세한 내용은 [Node.js 데이터베이스 예제](documentdb-nodejs-samples.md#database-examples)를 참조하세요.

## 다음 단계

이제 DocumentDB 데이터베이스를 만드는 방법을 알았으므로 다음 단계에서는 [컬렉션을 만들어야](documentdb-create-collection.md) 합니다.

컬렉션을 만든 후에 포털의 문서 탐색기를 사용하여 [JSON 문서를 추가](documentdb-view-json-document-explorer.md)하고, DocumentDB 데이터 마이그레이션 도구를 사용하여 컬렉션으로 [문서를 가져오거나](documentdb-import-data.md), [DocumentDB SDK](documentdb-sdk-dotnet.md) 하나를 사용하여 CRUD 작업을 수행할 수 있습니다. DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다. 문서 만들기, 제거, 업데이트 및 삭제 방법을 보여주는 .NET 코드 샘플의 경우, [.NET 문서 예제](documentdb-dotnet-samples.md#document-examples)를 참조하세요. Node.js SDK를 사용한 문서 작업에 대한 자세한 내용은 [Node.js 문서 예제](documentdb-nodejs-samples.md#document-examples)를 참조하세요.

컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](documentdb-sdk-dotnet.md) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#executing-sql-queries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

<!---HONumber=AcomDC_0831_2016--->