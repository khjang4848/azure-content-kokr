<properties 
pageTitle="인덱서 작업(Azure 검색 서비스 REST API: 2015-02-28-Preview) | Azure 검색 미리 보기 API" 
description="인덱서 작업(Azure 검색 서비스 REST API: 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#인덱서 작업(Azure 검색 서비스 REST API: 2015-02-28-Preview)#

> [AZURE.NOTE] 이 문서에서는 [2015-02-28-Preview REST API](search-api-2015-02-28-preview.md)의 인덱서를 설명합니다. 이 API 버전에는 문서 추출 기능이 있는 Azure Blob 저장소 인덱서와 Azure 테이블 저장소 인덱서의 미리 보기 버전 및 기타 향상된 기능이 추가되었습니다. 또한 API는 Azure SQL 데이터베이스, Azure VM SQL Server 및 Azure DocumentDB용 인덱서를 비롯하여 일반 공급(GA) 인덱서도 지원합니다.

## 개요 ##

코드를 작성하여 데이터를 인덱싱하지 않고도 일부 일반적인 데이터 원본과 Azure 검색을 직접 통합할 수 있습니다. 이를 설정하기 위해 Azure 검색 API를 호출하여 **인덱서** 및 **데이터 원본**을 만들고 관리할 수 있습니다.

**인덱서**는 데이터 원본을 대상 검색 인덱스에 연결하는 리소스입니다. 인덱서는 다음과 같은 방법으로 사용됩니다.

- 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
- 예약에 따라 인덱스를 데이터 소스의 변경 사항과 동기화합니다. 일정은 인덱서 정의의 일부입니다.
- 필요에 따라 요청 시 인덱스 업데이트를 호출합니다.

**인덱서**는 인덱스를 정기적으로 업데이트하려는 경우에 유용합니다. 인라인 일정을 인덱서 정의의 일부로 설정하거나 [인덱서 실행](#RunIndexer)을 사용하여 요청 시 실행할 수 있습니다.

**데이터 원본**은 인덱싱해야 하는 데이터, 데이터에 액세스할 수 있는 자격 증명, Azure 검색에서 데이터 변경 내용(예: 데이터베이스 테이블에서 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 이는 독립된 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

현재 지원되는 데이터 원본은 다음과 같습니다.

- **Azure SQL 데이터베이스** 및 **Azure VM의 SQL Server** 대상 연습은 [이 문서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)를 참조하세요.
- **Azure DocumentDB** 대상 연습은 [이 문서](../documentdb/documentdb-search-indexer.md)를 참조하세요.
- **Azure Blob 저장소** - PDF, Microsoft Office(DOCX/DOC, XSLX/XLS, PPTX/PPT, MSG), HTML, XML, ZIP 및 일반 텍스트 파일(JSON 포함) 문서 형식을 포함합니다. 대상 연습은 [이 문서](search-howto-indexing-azure-blob-storage.md)를 참조하세요.
- **Azure Table Storage**. 대상 연습은 [이 문서](search-howto-indexing-azure-tables.md)를 참조하세요.
	 
향후 추가 데이터 원본에 대한 지원을 추가할 예정입니다. 이러한 의사 결정의 우선 순위를 지정하는 데 도움이 되도록 [Azure 검색 사용자 의견 포럼](http://feedback.azure.com/forums/263029-azure-search)에서 사용자 의견을 제공해 주시기 바랍니다.

인덱서 및 데이터 원본 리소스와 관련된 최대 제한은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

## 일반적인 사용 흐름 ##

지정된 `data source` 또는 `indexer` 리소스에 대한 간단한 HTTP 요청(POST, GET, PUT, DELETE)을 통해 인덱서 및 데이터 원본을 만들고 관리할 수 있습니다.

자동 인덱싱은 일반적으로 다음 4단계 프로세스를 통해 설정됩니다.

1. 인덱싱해야 하는 데이터가 포함된 데이터 원본을 식별합니다. Azure 검색에서는 데이터 원본에 있는 일부 데이터 형식을 지원하지 않을 수 있습니다. 목록은 [지원되는 데이터 형식](https://msdn.microsoft.com/library/azure/dn798938.aspx)을 참조하세요.

2. 스키마가 데이터 원본과 호환되는 Azure 검색 인덱스를 만듭니다.
  
3. [데이터 원본 만들기](#CreateDataSource)에 설명된 대로 Azure 검색 데이터 원본을 만듭니다.
  
4. [인덱서 만들기](#CreateIndexer)에 설명된 대로 Azure 검색 인덱서를 만듭니다.

모든 대상 인덱스 및 데이터 소스 조합에 대해 하나의 인덱스를 생성하도록 계획해야 합니다. 동일한 인덱스에 쓰는 여러 인덱서를 만들 수 있으며, 여러 인덱서에 동일한 데이터 원본을 다시 사용할 수 있습니다. 그러나 인덱서는 한 번에 하나의 데이터 원본만 사용할 수 있으며, 단일 인덱스에만 쓸 수 있습니다.

인덱서를 만든 후에는 [인덱서 상태 가져오기](#GetIndexerStatus) 작업을 사용하여 해당 실행 상태를 검색할 수 있습니다. 또한 [인덱서 실행](#RunIndexer) 작업을 사용하여 언제든지 인덱서를 실행할 수 있습니다(일정에 따라 주기적으로 실행하는 대신 또는 이러한 방법 외에).

<!-- MSDN has 2 art files plus a API topic link list -->


## 데이터 원본 만들기 ##

Azure 검색에서 데이터 원본은 인덱서와 함께 사용되며 대상 인덱스의 임시 또는 예약된 데이터 새로 고침에 대한 연결 정보를 제공합니다. HTTP POST 요청을 사용하여 Azure 검색 서비스 내에 새 데이터 원본을 만들 수 있습니다.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**참고**: 허용되는 최대 데이터 원본 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 데이터 원본을 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

**요청**

모든 서비스 요청에는 HTTPS를 사용해야 합니다. POST 또는 PUT 메서드를 사용하여 **데이터 원본 만들기** 요청을 생성할 수 있습니다. POST를 사용할 때는 데이터 원본 정의와 함께 요청 본문에 데이터 원본 이름을 제공해야 합니다. PUT을 사용하는 경우 이름이 URL에 포함됩니다. 데이터 원본이 없으면 만들어지고 이미 있으면 새 정의로 업데이트됩니다.

데이터 원본 이름은 소문자여야 하고 문자나 숫자로 시작되어야 합니다. 또한 슬래시나 마침표를 포함할 수 없으며 128자 미만이어야 합니다. 문자나 숫자로 시작하는 데이터 원본 이름의 뒤에는 원하는 모든 문자, 숫자, 대시(여러 대시를 연속적으로 포함할 수는 없음)를 사용할 수 있습니다. 자세한 내용은 [이름 지정 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx)을 참조하세요.

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

**요청 헤더**

다음 목록에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.

- `Content-Type`: 필수 `application/json`으로 설정합니다.
- `api-key`: 필수 사항입니다. `api-key`는 검색 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **데이터 원본 만들기** 요청은 쿼리 키가 아니라 관리 키로 설정된 `api-key` 헤더를 포함해야 합니다.
 
요청 URL을 생성하려면 서비스 이름도 필요합니다. 서비스 이름과 `api-key`는 [Azure Portal](https://portal.azure.com/)의 서비스 대시보드에서 가져올 수 있습니다. 페이지 탐색 도움말은 [포털에서 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

<a name="CreateDataSourceRequestSyntax"></a> **요청 본문 구문**

요청 본문에는 데이터 원본 정의가 포함됩니다. 데이터 원본 정의에는 데이터 원본의 형식, 데이터를 읽는 데 필요한 자격 증명 그리고 선택적 데이터 변경/삭제 검색 정책이 들어 있으며, 이러한 정책은 정기적으로 예약되는 인덱서와 함께 사용할 때 데이터 원본에서 변경되거나 삭제된 데이터를 효율적으로 식별하는 데 사용됩니다.

요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
    	"credentials" : { "connectionString" : "Required. Connection string for your data source" },
    	"container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

요청에는 다음 속성이 포함됩니다.

- `name`: 필수 데이터 소스의 이름입니다. 데이터 원본 이름은 소문자, 숫자 또는 대시만 포함할 수 있고 대시로 시작하거나 끝날 수 없으며 128자로 제한됩니다.
- `description`: 선택적 설명입니다.
- `type`: 필수 다음과 같은 지원되는 데이터 원본 유형 중 하나여야 합니다.
	- `azuresql` - Azure SQL 데이터베이스 또는 Azure VM의 SQL Server
	- `documentdb` - Azure DocumentDB
	- `azureblob` - Azure Blob Storage
	- `azuretable` - Azure Table Storage
- `credentials`:
	- 필수 `connectionString` 속성은 데이터 원본의 연결 문자열을 지정합니다. 연결 문자열의 형식은 데이터 원본 유형에 따라 달라 집니다.
		- Azure SQL의 경우 일반적인 SQL Server 연결 문자열입니다. Azure Portal을 사용하여 연결 문자열을 검색하는 경우 `ADO.NET connection string` 옵션을 사용합니다.
		- DocumentDB의 경우 연결 문자열은 `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"` 형식이어야 합니다. 모든 값이 필요합니다. [Azure Portal](https://portal.azure.com/)에서 이러한 값을 확인할 수 있습니다.
		- Azure Blob 및 Table Storage의 경우 저장소 계정 연결 문자열입니다. 형식은 [여기](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/)에서 설명합니다. HTTPS 끝점 프로토콜이 필요합니다.
- `container`, 필수: `name` 및 `query` 속성을 사용하여 인덱스에 데이터를 지정합니다:
	- `name`, 필수:
		- Azure SQL: 테이블 또는 뷰를 지정합니다. `[dbo].[mytable]`과 같은 스키마로 한정된 이름을 사용할 수 있습니다.
		- DocumentDB: 컬렉션을 지정합니다.
		- Azure Blob Storage: 저장소 컨테이너를 지정합니다.
		- Azure Table Storage: 테이블의 이름을 지정합니다.
	- `query`, 선택 사항:
		- DocumentDB: Azure 검색에서 인덱싱할 수 있는 플랫 스키마로 임의 JSON 문서 레이아웃을 평면화하는 쿼리를 지정할 수 있습니다.
		- Azure Blob 저장소: Blob 컨테이너 내에 가상 폴더를 지정할 수 있습니다. 예를 들어 BLOB 경로 `mycontainer/documents/blob.pdf`, `documents`의 경우 가상 폴더로 사용할 수 있습니다.
		- Azure Table Storage: 가져올 행 집합을 필터링하는 쿼리를 지정할 수 있습니다.
		- Azure SQL: 쿼리는 지원되지 않습니다. 이 기능이 필요하면 [이 제안](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)에 투표해 주세요.
- 선택적 `dataChangeDetectionPolicy` 및 `dataDeletionDetectionPolicy` 속성은 아래에서 설명합니다.

<a name="DataChangeDetectionPolicies"></a> **데이터 변경 검색 정책**

데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 지원되는 정책은 데이터 원본 형식에 따라 다릅니다. 아래 섹션에서 각 정책에 대해 설명합니다.

***상위 워터마크 변경 검색 정책***

다음 기준을 충족하는 열이나 속성이 데이터 원본에 포함되어 있으면 이 정책을 사용합니다.
 
- 모든 삽입 시 열의 값을 지정합니다.
- 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
- 각 변경 시에 열의 값이 증가합니다.
- `WHERE [High Water Mark Column] > [Current High Water Mark Value]`와 같은 필터 절을 사용하는 쿼리를 효율적으로 실행할 수 있습니다.

예를 들어 Azure SQL 데이터 원본을 사용하는 경우 상위 워터마크 정책을 사용하기에 적합한 열은 인덱싱된 `rowversion` 열입니다.

이 정책은 다음과 같이 지정할 수 있습니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

> [AZURE.NOTE] DocumentDB 데이터 원본을 사용할 때는 DocumentDB에서 제공되는 `_ts` 속성을 사용해야 합니다.

> [AZURE.NOTE] Azure Blob 데이터 원본을 사용하는 경우 Azure 검색은 Blob의 마지막 수정 타임스탬프를 기반으로 상위 워터마크 변경 감지 정책을 자동으로 사용하므로 이러한 정책을 직접 지정할 필요가 없습니다.

***SQL 통합 변경 검색 정책***

SQL 데이터베이스에서 [변경 내용 추적](https://msdn.microsoft.com/library/bb933875.aspx)을 지원하는 경우 SQL 통합 변경 내용 추적 정책을 사용하는 것이 좋습니다. 이 정책을 사용하면 변경 내용을 가장 효율적으로 추적할 수 있으며, 스키마에서 "soft delete" 열을 명시적으로 지정하지 않아도 Azure 검색에서 삭제된 행을 식별할 수 있습니다.

통합 변경 내용 추적은 다음 SQL Server 데이터베이스 버전부터 지원됩니다.
- Azure VM SQL Server를 사용하는 경우 SQL Server 2008 R2.
- Azure SQL 데이터베이스 V12(Azure SQL 데이터베이스를 사용하는 경우)

SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다.

이 정책은 테이블에만 사용할 수 있으며 보기에는 사용할 수 없습니다. 이 정책을 사용하려면 사용 중인 테이블에 대해 변경 내용 추적을 사용하도록 설정해야 합니다. 지침은 [변경 내용 추적 설정 및 해제](https://msdn.microsoft.com/library/bb964713.aspx)를 참조하세요.
 
**데이터 원본 만들기** 요청을 구성할 때 SQL 통합 변경 내용 추적 정책을 다음과 같이 지정할 수 있습니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **데이터 삭제 검색 정책**

데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재 지원되는 정책은 `Soft Delete` 정책뿐입니다. 이 정책을 통해 데이터 원본의 `soft delete` 열 또는 속성 값에 따라 삭제된 항목을 식별할 수 있습니다. 이 정책은 다음과 같이 지정할 수 있습니다.

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

**참고:** 문자열, 정수 또는 부울 값이 있는 열만 지원됩니다. `softDeleteMarkerValue`로 사용되는 값은 해당 열에 정수 또는 부울 값이 있는 경우에도 문자열이어야 합니다. 예를 들어 데이터 원본에 표시된 값이 1인 경우 `"1"`을 `softDeleteMarkerValue`로 사용합니다.

<a name="CreateDataSourceRequestExamples"></a> **요청 본문 예제**

일정에 따라 실행되는 인덱서에서 데이터 원본을 사용하려는 경우 이 예제에 나와 있는 변경 및 삭제 정책 지정 방법을 참조할 수 있습니다.

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}

일회성 데이터 복사에만 데이터 원본을 사용하려는 경우에는 정책을 생략해도 됩니다.

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**응답**

요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.

<a name="UpdateDataSource"></a>
## 데이터 원본 업데이트 ##

HTTP PUT 요청을 사용하여 기존 데이터 원본을 업데이트할 수 있습니다. 요청 URI에서 업데이트할 데이터 원본의 이름을 지정합니다.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다. [Azure 검색 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx)에서는 대체 버전에 대한 세부 정보 및 추가 정보를 제공합니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**요청**

요청 본문 구문은 [데이터 원본 만들기 요청](#CreateDataSourceRequestSyntax)의 본문 구문과 같습니다.

> [AZURE.NOTE] 기존 데이터 원본의 경우 일부 속성을 업데이트할 수 없습니다. 예를 들어 기존 데이터 원본의 형식은 변경할 수 없습니다.

> [AZURE.NOTE] 기존 데이터 원본에 대한 연결 문자열을 변경하지 않으려면 연결 문자열에 대해 `<unchanged>` 리터럴을 지정합니다. 데이터 원본을 업데이트해야 하지만 보안에 민감한 데이터라서 연결 문자열에 액세스하는 것이 힘든 경우에 유용합니다.

**응답**

요청이 성공적으로 실행되면 새 데이터 원본이 생성된 경우 ‘201 생성됨’이 반환되고 기존 데이터 원본이 업데이트된 경우 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="ListDataSource"></a>
## 데이터 원본 나열 ##

**데이터 원본 나열** 작업은 Azure 검색 서비스에 있는 데이터 원본의 목록을 반환합니다.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

요청이 성공적으로 실행되면 '200 OK'가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 데이터 원본 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /datasources?api-version=205-02-28&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

이 기술은 검색 서비스에 많은 데이터 원본이 있는 경우 대역폭을 절감하는 데 유용합니다.

<a name="GetDataSource"></a>
## 데이터 원본 가져오기 ##

**데이터 원본 가져오기** 작업은 Azure 검색에서 데이터 원본 정의를 가져옵니다.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답은 [데이터 원본 만들기 예제 요청](#CreateDataSourceRequestExamples)의 예제와 유사합니다.

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : "true" }
	}

> [AZURE.NOTE] 이 API를 호출할 때는 `Accept` 요청 헤더를 `application/json;odata.metadata=none`으로 설정하지 마세요. 이렇게 설정하면 `@odata.type` 특성이 응답에서 생략되며, 서로 다른 유형의 데이터 변경 및 데이터 삭제 검색 정책을 구분할 수 없습니다.

<a name="DeleteDataSource"></a>
## 데이터 원본 삭제 ##

**데이터 원본 삭제** 작업은 Azure 검색 서비스에서 데이터 원본을 제거합니다.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] 삭제하는 데이터 원본을 참조하는 인덱서가 있어도 삭제 작업은 진행됩니다. 그러나 이러한 인덱서는 다음에 실행할 때 오류 상태로 전환됩니다.

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="CreateIndexer"></a>
## 인덱서 만들기 ##

HTTP POST 요청을 사용하여 Azure 검색 서비스 내에서 새 인덱서를 만들 수 있습니다.
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

또는 PUT을 사용하여 URI에서 데이터 원본 이름을 지정할 수 있습니다. 데이터 원본이 없으면 만들어집니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] 허용되는 최대 인덱서 수는 가격 책정 계층에 따라 다릅니다. 무료 서비스에서는 인덱서를 3개까지 사용할 수 있으며 표준 서비스에서는 50개까지 사용할 수 있습니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.


<a name="CreateIndexerRequestSyntax"></a> **요청 본문 구문**

요청 본문에는 인덱서 정의가 포함됩니다. 이 정의는 인덱싱의 데이터 원본 및 대상 인덱스와 선택적 인덱싱 일정 및 매개 변수를 지정합니다.


요청 페이로드 구조를 지정하는 구문은 다음과 같습니다. 이 항목에서 샘플 요청이 추가로 제공됩니다.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
	}

**인덱서 일정**

인덱서는 선택적으로 일정을 지정할 수 있습니다. 일정이 제공된 경우, 인덱서가 일정에 따라 주기적으로 실행됩니다. 일정은 다음과 같은 특성을 갖습니다.

- `interval`: 필수 인덱서 실행 간격 또는 기간을 지정하는 기간 값입니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값([ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `"P[nD][T[nH][nM]]"`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

- `startTime`: 필수 인덱서 실행을 시작해야 하는 UTC 날짜/시간입니다.

**인덱서 매개 변수**

인덱서는 필요에 따라 동작에 영향을 주는 여러 매개 변수를 지정할 수 있습니다. 모든 매개 변수는 선택 사항입니다.

- `maxFailedItems`: 인덱서 실행을 실패한 것으로 간주하기 전까지 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 0입니다. 실패한 항목에 대한 정보는 [인덱서 상태 가져오기](#GetIndexerStatus) 작업에서 반환됩니다.

- `maxFailedItemsPerBatch`: 인덱서 실행을 실패한 것으로 간주하기 전까지 각 일괄 처리에서 허용되는 인덱싱 실패 가능 항목의 수입니다. 기본값은 0입니다.

- `base64EncodeKeys`: 문서 키를 base-64로 인코딩할지 여부를 지정합니다. Azure 검색에서는 문서 키에 포함할 수 있는 문자에 제한이 적용됩니다. 그러나 원본 데이터의 값은 유효하지 않은 문자를 포함할 수 있습니다. 이러한 값을 문서 키로 인덱싱해야 하는 경우에는 이 플래그를 true로 설정할 수 있습니다. 기본값은 `false`입니다.

- `batchSize`: 성능을 향상시키기 위해 단일 배치로 인덱싱되고 데이터 원본에서 읽는 항목 수를 지정합니다. 기본값은 데이터 원본 형식에 따라 달라 집니다. Azure SQL 및 DocumentDB의 경우 1000, Azure Blob 저장소의 경우 10입니다.

**필드 매핑**

필드 매핑을 사용하여 데이터 원본의 필드 이름을 대상 인덱스의 다른 필드 이름에 매핑할 수 있습니다. `_id` 필드가 있는 원본 테이블을 예로 들어보겠습니다. Azure 검색에서는 밑줄로 시작하는 필드 이름을 사용할 수 없으므로 필드 이름을 바꿔야 합니다. 이렇게 하려면 다음과 같이 인덱서의 `fieldMappings` 속성을 사용하면 됩니다.
	
	"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

여러 필드 매핑을 지정할 수 있습니다.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

원본 필드 이름과 대상 필드 이름 모두 대/소문자를 구분하지 않습니다.

<a name="FieldMappingFunctions"></a> ***필드 매핑 함수***

필드 매핑을 사용하는 경우 *매핑 함수*를 통해 원본 필드 값을 변환할 수도 있습니다.

현재 `jsonArrayToStringCollection` 함수만 지원됩니다. 이 함수는 JSON 배열 형식의 문자열이 포함된 필드를 대상 인덱스에서 Collection(Edm.String) 필드로 구문 분석합니다. SQL에는 네이티브 컬렉션 데이터 형식이 없기 때문에 이 함수는 특히 Azure SQL 인덱서와 함께 사용됩니다. 다음과 같이 사용할 수 있습니다.

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

예를 들어 원본 필드에 `["red", "white", "blue"]` 문자열이 포함된 경우 `Collection(Edm.String)` 형식의 대상 필드는 세 개의 값 `"red"`, `"white"` 및 `"blue"`로 채워집니다.

`targetFieldName` 속성은 선택 사항입니다. 비워 둘 경우 `sourceFieldName` 값이 사용됩니다.

<a name="CreateIndexerRequestExamples"></a> **요청 본문 예제**

다음 예제에서는 `ordersds` 데이터 원본에서 참조하는 테이블의 데이터를 2015년 1월 1일 UTC에 시작하여 매 시간 실행되는 일정으로 `orders` 인덱스에 복사하는 인덱서를 만듭니다. 각 일괄 처리에서 인덱싱에 실패하는 항목이 5개 이하이며 인덱싱에 실패하는 총 항목 수가 10개 이하이면 각 인덱서 호출은 성공합니다.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**응답**

요청이 성공적으로 실행되면 '201 생성됨'이 반환됩니다.


<a name="UpdateIndexer"></a>
## 인덱서 업데이트 ##

HTTP PUT 요청을 사용하여 기존 인덱서를 업데이트할 수 있습니다. 요청 URI에서 업데이트할 인덱서의 이름을 지정합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version`은 필수 사항입니다. 현재 버전은 `2015-02-28`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**요청**

요청 본문 구문은 [인덱서 만들기 요청](#CreateIndexerRequestSyntax)의 본문 구문과 같습니다.

**응답**

요청이 성공적으로 실행되면 새 인덱서가 생성된 경우 ‘201 생성됨’이 반환되고 기존 인덱서가 업데이트된 경우 ‘204 콘텐츠 없음’이 반환됩니다.


<a name="ListIndexers"></a>
## 인덱서 나열 ##

**인덱서 나열** 작업은 Azure 검색 서비스의 인덱서 목록을 반환합니다.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다. [Azure 검색 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx)에서는 대체 버전에 대한 세부 정보 및 추가 정보를 제공합니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

요청이 성공적으로 실행되면 '200 OK'가 반환됩니다.

아래에는 예제 응답 본문이 나와 있습니다.

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

원하는 속성만 포함하도록 응답을 필터링할 수 있습니다. 예를 들어 인덱서 이름 목록만 포함하려면 다음과 같이 OData `$select` 쿼리 옵션을 사용합니다.

    GET /indexers?api-version=2014-10-20-Preview&$select=name

이 경우 위 예제의 응답은 다음과 같이 표시됩니다.

    {
      "value" : [ { "name": "myindexer" } ]
    }

이 기술은 검색 서비스에 많은 인덱서가 있는 경우 대역폭을 절감하는 데 유용합니다.


<a name="GetIndexer"></a>
## 인덱서 가져오기 ##

**인덱서 가져오기** 작업은 Azure 검색에서 인덱서 정의를 가져옵니다.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답은 [인덱서 만들기 예제 요청](#CreateIndexerRequestExamples)의 예제와 유사합니다.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}


<a name="DeleteIndexer"></a>
## 인덱서 삭제 ##

**인덱서 삭제** 작업은 Azure 검색 서비스에서 인덱서를 제거합니다.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

인덱서를 삭제하면 해당 시점에 진행 중이던 인덱서 실행은 완료될 때까지 실행되지만 추가 실행은 예약되지 않습니다. 없는 인덱서를 사용하려고 하면 HTTP 상태 코드 ‘404 찾을 수 없음’이 반환됩니다.
 
`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음’이 반환됩니다.

<a name="RunIndexer"></a>
## 인덱서 실행 ##

인덱서를 일정에 따라 주기적으로 실행할 수 있을 뿐 아니라 **인덱서 실행** 작업을 통해 요청 시에 호출할 수도 있습니다.

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘202 수락됨'이 반환됩니다.

<a name="GetIndexerStatus"></a>
## 인덱서 상태 가져오기 ##

**인덱서 상태 가져오기** 작업은 인덱서의 현재 상태 및 실행 기록을 검색합니다.

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘200 OK’가 반환됩니다.

응답 본문에는 전반적인 인덱서 상태, 마지막 인덱서 호출 및 최근 인덱서 호출 기록(있는 경우)에 대한 정보가 포함됩니다.

아래에 샘플 응답 본문이 나와 있습니다.

	{
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
         },
		"executionHistory":[ {
			"status":"success",
         	"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
		}]
	}

**인덱서 상태**

인덱서 상태는 다음 값 중 하나일 수 있습니다.

- `running`: 인덱서가 정상적으로 실행되고 있음을 나타냅니다. 이 상태가 표시되더라도 일부 인덱서 실행은 실패할 수 있으므로 `lastResult` 속성도 확인하는 것이 좋습니다.

- `error`: 인덱서에서 오류가 발생하여 사용자가 직접 해결해야 함을 나타냅니다. 데이터 원본 자격 증명이 만료되었거나 대상 인덱스 또는 데이터 원본의 스키마가 잘못 변경된 경우를 예로 들 수 있습니다.

**인덱서 실행 결과**

인덱서 실행 결과에는 단일 인덱서 실행에 대한 정보가 포함됩니다. 최신 결과는 인덱서 상태의 `lastResult` 속성으로 표시됩니다. 기타 최근 결과는 있는 경우 인덱서 상태의 `executionHistory` 속성으로 반환됩니다.

인덱서 실행 결과에는 다음 속성이 포함됩니다.

- `status`: 실행의 상태입니다. 자세한 내용은 아래의 [인덱서 실행 상태](#IndexerExecutionStatus)를 참조하세요.

- `errorMessage`: 실패한 실행에 대한 오류 메시지입니다.

- `startTime`: 이 실행이 시작된 시간(UTC)입니다.

- `endTime`: 이 실행이 종료된 시간(UTC)입니다. 계속 실행 중인 경우에는 이 값이 설정되지 않은 것입니다.

- `errors`: 항목 수준 오류(있는 경우)의 배열입니다. 각 항목은 문서 키(`key` 속성) 및 오류 메시지(`errorMessage` 속성)를 포함하고 있습니다.

- `itemsProcessed`: 이 실행 중에 인덱서에서 인덱싱하려고 한 데이터 원본 항목(예: 테이블 행)의 수입니다.

- `itemsFailed`: 이 실행 중에 인덱싱하지 못한 항목의 수입니다.
 
- `initialTrackingState`: 첫 번째 인덱서 실행인 경우 또는 사용하는 데이터 원본에 대해 데이터 변경 내용 추적 정책을 사용하도록 설정하지 않은 경우에는 항상 `null`입니다. 해당 정책을 사용하도록 설정한 경우 후속 실행에서 이 값은 해당 실행에서 처리한 첫 번째(최소) 변경 내용 추적 값을 나타냅니다.

- `finalTrackingState`: 사용하는 데이터 원본에 대해 데이터 변경 내용 추적 정책을 사용하도록 설정하지 않은 경우 항상 `null`입니다. 그렇지 않으면 해당 실행에서 정상적으로 처리한 최신(최대) 변경 내용 추적 값을 나타냅니다.

<a name="IndexerExecutionStatus"></a> **인덱서 실행 상태**

인덱서 실행 상태에는 단일 인덱서 실행의 상태가 캡처됩니다. 이 상태의 값은 다음과 같을 수 있습니다.

- `success`: 인덱서 실행이 정상적으로 완료되었음을 나타냅니다.

- `inProgress`: 인덱서 실행이 진행 중임을 나타냅니다.

- `transientFailure`: 인덱서 실행에 실패했음을 나타냅니다. 자세한 내용은 `errorMessage` 속성을 참조하세요. 실패하면 해결하기 위해 사람이 개입해야(예를 들어 데이터 원본과 대상 인덱스 사이의 구성표 비호환성 해결) 할 수도 있고 그렇지 않을 수도 있는 반면에, 임시 데이터 원본의 가동 중지 시간은 그렇지 않습니다. 인덱서 호출은 일정(있는 경우)에 따라 계속 진행됩니다.

- `persistentFailure`: 사용자가 작업을 수행해야 방식에서 인덱서 오류가 발생했음을 나타냅니다. 이 상태에서는 예약된 인덱서 실행이 중지됩니다. 문제를 해결한 후 인덱서 다시 설정API를 사용하여 예약된 실행을 다시 시작합니다.

- `reset`: 인덱서 다시 설정 API(아래 참조) 호출을 통해 인덱서가 다시 설정되었음을 나타냅니다.

<a name="ResetIndexer"></a>
## 인덱서 다시 설정 ##

**인덱서 다시 설정** 작업은 인덱서와 관련된 변경 내용 추적 상태를 다시 설정합니다. 그러면 데이터 원본 스키마가 변경된 등의 경우 인덱스를 처음부터 다시 트리거하거나 인덱서와 연결된 데이터 원본의 데이터 변경 검색 정책을 변경할 수 있습니다.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version`은 필수 사항입니다. 미리 보기 버전은 `2015-02-28-Preview`입니다.

`api-key`는 쿼리 키가 아니라 관리 키여야 합니다. 키에 대한 자세한 내용은 [검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인증 섹션을 참조하세요. [포털에서 검색 서비스 만들기](search-create-service-portal.md)에서는 요청에 사용된 서비스 URL 및 키 속성을 가져오는 방법을 설명합니다.

**응답**

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음'이 반환됩니다.

## SQL 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑 ##

<table style="font-size:12">
<tr>
<td>SQL 데이터 형식</td>	
<td>허용되는 대상 인덱스 필드 유형</td>
<td>참고 사항</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Azure 검색에서는 decimal 형식을 Edm.Double로 변환하면 정밀도가 떨어지기 때문에 이를 지원하지 않습니다.
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>문자열 열을 Collection(Edm.String)으로 변환하는 방법에 대한 자세한 내용은 이 문서에서 [필드 매핑 함수](#FieldMappingFunctions)를 참조하세요.</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>SRID가 4326(기본값)인 POINT 형식의 지리 인스턴스만 지원됩니다.</td>
</tr>
<tr>
<td>rowversion</td>
<td>해당 없음</td>
<td>행 버전 열은 변경 내용 추적에 사용할 수 있지만 검색 인덱스에 저장할 수는 없습니다.</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image,<br>xml, geometry, CLR 형식</td>
<td>해당 없음</td>
<td>지원되지 않음</td>
</tr>
</table>

## JSON 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑 ##

<table style="font-size:12">
<tr>
<td>JSON 데이터 형식</td>	
<td>허용되는 대상 인덱스 필드 유형</td>
<td>참고 사항</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>정수</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>부동 소수점 숫자</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>기본 형식의 배열, 예: ["a", "b", "c"]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>날짜처럼 보이는 문자열</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON point 개체</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON point는 { "type" : "Point", "coordinates" : [long, lat] } 형식의 JSON 개체입니다. </td>
</tr>
<tr>
<td>기타 JSON 개체</td>
<td>해당 없음</td>
<td>지원되지 않음. Azure 검색에서는 현재 기본 형식과 문자열 컬렉션만 지원합니다.</td>
</tr>
</table>

<!---HONumber=AcomDC_0914_2016-->