<properties 
	pageTitle="DocumentDB 계층적 리소스 모델 및 개념 | Microsoft Azure" 
	description="데이터베이스, 컬렉션, UDF(사용자 정의 함수), 문서, 리소스 관리 권한 등으로 구성된 DocumentDB의 계층적 모델에 대해 알아봅니다."
	keywords="계층적 모델, documentdb, azure, Microsoft azure"	
	services="documentdb" 
	documentationCenter="" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="anhoh"/>

# DocumentDB 계층적 리소스 모델 및 개념

DocumentDB에서 관리하는 데이터베이스 엔터티를 **리소스**라고 합니다. 각 리소스는 논리적 URI를 통해 고유하게 식별됩니다. 표준 HTTP 동사, request/response 헤더 및 상태 코드를 사용해서 리소스를 사용할 수 있습니다.

이 문서를 읽어 보면 다음을 알게 됩니다.

- DocumentDB의 리소스 모델이란 무엇입니까?
- 시스템 정의 리소스와 사용자 정의 리소스의 차이는 무엇인가요?
- 리소스를 어떻게 해결하나요?
- 컬렉션을 어떻게 사용하나요?
- 저장된 프로시저, 트리거 및 UDF(사용자 정의 함수)를 사용하려면 어떻게 하나요?

## 계층적 리소스 모델
다음 다이어그램에 표시된 대로, DocumentDB의 계층적 **리소스 모델**은 단일 데이터베이스 계정 아래에 있고 각각 논리적이고 안정적인 URI를 통해 주소 지정이 가능한 리소스 집합으로 구성됩니다. 이 문서에서는 리소스 집합을 **피드**라고 합니다.

>[AZURE.NOTE] DocumentDB는 통신 모델이 RESTful이며 [.NET 클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 통해 사용할 수 있는 효율성 높은 TCP 프로토콜을 제공합니다.

![DocumentDB 계층적 리소스 모델][1]  
**계층적 리소스 모델**

리소스 작업을 시작하려면 Azure 구독을 사용해서 [DocumentDB 데이터베이스 계정을 만들어야](documentdb-create-account.md) 합니다. 데이터베이스는 일련의 **데이터베이스** 집합으로 구성되며, 각 데이터베이스에는 여러 개의 **컬렉션**이 포함되며, 이러한 각 컬렉션에는 또한 **저장 프로시저, 트리거, UDF, 문서** 및 관련 **첨부 파일**(미리 보기 기능)이 포함됩니다. 또한 데이터베이스에는 **사용자**가 연관될 수 있으며, 이러한 각 사용자는 컬렉션, 저장 프로시저, 트리거, UDF, 문서 또는 첨부 파일에 액세스할 수 있는 **권한** 집합을 갖고 있습니다. 데이터베이스, 사용자, 사용 권한 및 컬렉션은 잘 알려진 스키마가 있는 시스템 정의 리소스인 반면 문서 및 첨부 파일에는 임의의 사용자 정의 JSON 콘텐츠가 포함됩니다.

|리소스 |설명
|-----------|-----------
|데이터베이스 계정 |데이터베이스 계정은 데이터베이스 집합 및 고정된 양의 첨부 파일(미리 보기 기능)용 blob 저장소에 연결됩니다. Azure 구독을 사용하여 데이터베이스 계정을 하나 이상 만들 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
|데이터베이스 |데이터베이스는 여러 컬렉션으로 분할된 문서 저장소의 논리적 컨테이너입니다. 또한 사용자 컨테이너입니다.
|사용자 |사용 권한 범위 지정을 위한 논리적 네임스페이스입니다. 
|사용 권한 |특정 리소스에 대한 액세스를 위해 사용자와 연결된 권한 부여 토큰입니다.
|컬렉션 |컬렉션은 JSON 문서 및 관련 JavaScript 응용 프로그램 논리의 컨테이너입니다. 컬렉션은 해당 컬렉션과 연관된 성능 수준에 따라 [비용](documentdb-performance-levels.md)이 결정되는 청구 가능 엔터티입니다. 컬렉션은 하나 이상의 파티션/서버에 걸쳐 있을 수 있으며 크기가 거의 무제한인 저장소 또는 처리량을 처리하도록 확장할 수 있습니다.
|저장 프로시저 |JavaScript로 작성된 응용 프로그램 논리로, 컬렉션에 등록되고 데이터베이스 엔진 내에서 트랜잭션으로 실행됩니다.
|트리거 |삽입, 바꾸기 또는 삭제 작업 전 후에 실행된 JavaScript로 작성된 응용 프로그램 논리입니다.
|UDF |JavaScript로 작성된 응용 프로그램 논리입니다. UDF를 사용하면 사용자 지정 쿼리 연산자를 모델링하여 핵심 DocumentDB 쿼리 언어를 확장할 수 있습니다.
|문서 |사용자 정의(임의) JSON 콘텐츠입니다. 기본적으로 스키마를 정의할 필요가 없거나 컬렉션에 추가된 모든 문서에 대해 보조 인덱스를 제공해야 합니다.
|(미리 보기) 첨부 파일 |첨부 파일은 외부 blob/미디어에 대한 참조 및 관련 메타데이터가 포함된 특수 문서입니다. 개발자는 DocumentDB에서 blob을 관리하도록 선택하거나 OneDrive, Dropbox 등의 외부 blob 서비스 공급자를 사용하여 저장할 수 있습니다. 


## 시스템 정의 리소스와 사용자 정의 리소스 비교
데이터베이스 계정, 데이터베이스, 컬렉션, 사용자, 사용 권한, 저장 프로시저, 트리거 및 UDF와 같은 리소스에는 모두 고정 스키마가 있으며 시스템 리소스라고 합니다. 반면, 문서 및 첨부 파일과 같은 리소스는 스키마에 제한이 없으며 사용자 정의 리소스의 예입니다. DocumentDB에서는 시스템 및 사용자 정의 리소스 둘 다 표준 규격 JSON으로 표시되고 관리됩니다. 모든 리소스(시스템 또는 사용자 정의)에는 다음과 같은 공통 속성이 있습니다.

> [AZURE.NOTE] JSON 표현에서는 리소스의 모든 시스템 생성 속성 앞에 밑줄(\_)이 추가됩니다.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>속성</strong></p></td>
            <td valign="top"><p><strong>사용자 설정 가능 또는 시스템 생성?</strong></p></td>
            <td valign="top"><p><strong>용도</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>시스템 생성</p></td>
            <td valign="top"><p>리소스의 고유한 시스템 생성 계층적 식별자</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>시스템 생성</p></td>
            <td valign="top"><p>낙관적 동시성 제어에 필요한 리소스의 etag</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>시스템 생성</p></td>
            <td valign="top"><p>리소스의 마지막 업데이트 타임스탬프</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>시스템 생성</p></td>
            <td valign="top"><p>리소스의 고유한 주소 지정 가능 URI</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>시스템 생성</p></td>
            <td valign="top"><p>리소스의 고유한 사용자 정의 이름(동일한 파티션 키 값 포함) 사용자가 ID를 지정하지 않는 경우 시스템에서 ID가 생성됩니다.</p></td>
        </tr>
    </tbody>
</table>

### 리소스의 네트워크 표현
DocumentDB는 JSON 표준 또는 특수 인코딩에 대한 소유 확장을 위임하지 않습니다. 표준 규격 JSON 문서로 작동합니다.
 
### 리소스 주소 지정
모든 리소스는 URI로 주소 지정이 가능합니다. 리소스의 **\_self** 속성 값은 리소스의 상대 URI를 나타냅니다. URI의 형식은 /<feed>/{\_rid} 경로 세그먼트로 구성됩니다.

|\_self 값 |설명
|-------------------|-----------
|/dbs |데이터베이스 계정의 데이터베이스 피드
|/dbs/{dbName} |{dbName} 값과 ID가 일치하는 데이터베이스
|/dbs/{dbName}/colls/ |데이터베이스의 컬렉션 피드
|/dbs/{dbName}/colls/{collName} |{collName} 값과 ID가 일치하는 컬렉션
|/dbs/{dbName}/colls/{collName}/docs |컬렉션의 문서 피드
|/dbs/{dbName}/colls/{collName}/docs/{docId} |{doc} 값과 ID가 일치하는 문서
|/dbs/{dbName}/users/ |데이터베이스의 사용자 피드
|/dbs/{dbName}/users/{userId} |{user} 값과 ID가 일치하는 사용자
|/dbs/{dbName}/users/{userId}/permissions |사용자의 사용 권한 피드
|/dbs/{dbName}/users/{userId}/permissions/{permissionId} |{permission} 값과 ID가 일치하는 사용 권한
  
각 리소스에는 id 속성을 통해 노출되는 고유한 사용자 정의 이름도 있습니다. 참고: 문서의 경우 사용자가 id를 지정하지 않는 경우 시스템이 자동으로 문서에 대한 고유 id를 생성합니다. id는 특정 상위 리소스 컨텍스트 내에서 고유한 최대 256자로 된 사용자 정의 문자열입니다.

각 리소스에는 \_rid 속성을 통해 사용할 수 있는 시스템 생성 계층적 리소스 식별자(RID라고도 함)도 있습니다. RID는 지정된 리소스의 전체 계층 구조를 인코드하며 분산 방식으로 참조 무결성을 적용하는 데 사용되는 매우 편리한 내부 표현입니다. RID는 데이터베이스 계정 내에서 고유하며 파티션 간 조회가 필요 없는 효율적인 라우팅을 위해 DocumentDB에서 내부적으로 사용됩니다. \_self 및 \_rid 속성 값은 둘 다 리소스의 대체 정식 표현입니다.

DocumentDB REST API는 id 및 \_rid 속성으로 리소스의 주소 지정과 요청의 라우팅을 지원합니다.

## 데이터베이스 계정
Azure 구독을 사용하여 DocumentDB 데이터베이스 계정을 하나 이상 프로비전할 수 있습니다.

Azure Portal([http://portal.azure.com/](https://portal.azure.com/))을 통해 [DocumentDB 데이터베이스 계정을 만들고 관리](documentdb-create-account.md)할 수 있습니다. 데이터베이스 계정을 만들고 관리하려면 관리 액세스 권한이 필요하며 Azure 구독으로만 수행할 수 있습니다.

### 데이터베이스 계정 속성
데이터베이스 계정 프로비전 및 관리의 일부로 다음 속성을 구성하고 읽을 수 있습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>속성 이름</strong></p></td>
            <td valign="top"><p><strong>설명</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>일관성 정책</p></td>
            <td valign="top"><p>데이터베이스 계정 아래의 모든 컬렉션에 대한 기본 일관성 수준을 구성하려면 이 속성을 설정합니다. [[x-ms-consistency-level] 요청 헤더를 사용하여 요청 단위로 일관성 수준을 재정의할 수 있습니다. <p><p>이 속성은 <i>사용자 정의 리소스</i>에만 적용됩니다. 모든 시스템 정의 리소스는 강력한 일관성으로 읽기/쿼리를 지원하도록 구성됩니다.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>권한 부여 키</p></td>
            <td valign="top"><p>이러한 키는 데이터베이스 계정 아래의 모든 리소스에 대한 관리 액세스 권한을 제공하는 기본 및 보조 마스터 키이며 읽기 전용 키입니다.</p></td>
        </tr>
    </tbody>
</table>

Azure Portal에서 데이터베이스 계정을 프로비전, 구성 및 관리하는 것뿐 아니라 [Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 및 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 프로그래밍 방식으로 DocumentDB 데이터베이스 계정을 만들고 관리할 수도 있습니다.

## 데이터베이스
DocumentDB 데이터베이스는 다음 다이어그램에 표시된 것처럼 하나 이상의 컬렉션 및 사용자의 논리적 컨테이너입니다. DocumentDB 데이터베이스 계정에 데이터베이스를 개수에 제한 없이 만들 수 있으며 제공 한도가 적용됩니다.

![데이터베이스 계정 및 컬렉션 계층적 모델][2]  
**데이터베이스는 사용자 및 컬렉션의 논리적 컨테이너입니다.**

데이터베이스에는 컬렉션으로 분할된 문서 저장소가 거의 무제한으로 포함될 수 있으며, 컬렉션은 포함된 문서에 대한 트랜잭션 도메인을 형성합니다.

### DocumentDB 데이터베이스의 탄력적인 확장
DocumentDB 데이터베이스는 기본적으로 탄력적이며 SSD 지원 문서 저장소 및 프로비전된 처리량이 몇 GB에서 페타바이트 범위까지 다양합니다.

기존의 RDBMS 데이터베이스와 달리 DocumentDB의 데이터베이스는 단일 컴퓨터로 범위가 지정되지 않습니다. DocumentDB를 사용하면 응용 프로그램 확장 요구에 따라 컬렉션, 데이터베이스 또는 둘 다를 더 만들 수 있습니다. 실제로 Microsoft 내의 다양한 자사 응용 프로그램은 각각 테라바이트의 문서 저장소가 있는 컬렉션이 수천 개 포함된 대형 DocumentDB 데이터베이스를 만들어 소비자 배율로 DocumentDB를 사용했습니다. 응용 프로그램의 크기 조정 요구 사항을 충족하기 위해 컬렉션을 추가 또는 제거하여 데이터베이스를 확장하거나 축소할 수 있습니다.

제품에 적용되는 데이터베이스 내에 컬렉션을 개수에 제한 없이 만들 수 있습니다. 각 컬렉션에는 선택한 성능 계층에 따라 SSD 지원 저장소 및 처리량이 프로비전됩니다.

DocumentDB 데이터베이스는 사용자 컨테이너이기도 합니다. 다시 사용자는 컬렉션, 문서 및 첨부 파일에 대한 미세 조정된 권한 부여 및 액세스 권한을 제공하는 사용 권한 집합의 논리적 네임스페이스입니다.
 
DocumentDB 리소스 모델의 다른 리소스와 마찬가지로, [Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 데이터베이스를 쉽게 만들거나, 바꾸거나, 삭제하거나, 읽거나, 열거할 수 있습니다. DocumentDB는 데이터베이스 리소스의 메타데이터 읽기 또는 쿼리에 대해 강력한 일관성을 보장합니다. 데이터베이스를 삭제하면 자동으로 컬렉션 또는 컬렉션 내에 포함된 사용자에 액세스할 수 없게 됩니다.

## 컬렉션
DocumentDB 컬렉션은 JSON 문서의 컨테이너입니다. 컬렉션은 배율, 트랜잭션 및 쿼리 단위이기도 합니다.

### 탄력적 SSD 지원 문서 저장소
컬렉션은 본질적으로 탄력적입니다. 문서를 추가하거나 제거하면 자동으로 확장 및 축소됩니다. 컬렉션은 하나 이상의 물리적 파티션 또는 서버에 걸쳐 있을 수 있는 논리적 리소스입니다. 컬렉션 내의 파티션 수는 컬렉션의 프로비전된 처리량 및 저장소 크기에 따라 DocumentDB에 의해 결정됩니다. DocumentDB의 모든 파티션은 고정된 양의 SSD 지원 저장소에 연결되며, 고가용성을 위해 복제됩니다. 파티션 관리는 Azure DocumentDB에 의해 완전히 관리되므로 복잡한 코드를 작성하거나 파티션을 관리할 필요가 없습니다. DocumentDB 컬렉션은 저장소 및 처리량이 **거의 무제한**입니다.

### 컬렉션의 자동 인덱싱
DocumentDB는 진정한 스키마 없는 데이터베이스 시스템입니다. JSON 문서에 대해 스키마를 가정하거나 요구하지 않습니다. 컬렉션에 문서를 추가하면 DocumentDB에서 자동으로 인덱싱하며 문서를 쿼리할 수 있습니다. 스키마 또는 보조 인덱스가 필요 없는 자동 문서 인덱싱은 DocumentDB의 주요 기능이며 쓰기 최적화되고 잠금이 없으며 로그 구조화된 인덱스 유지 관리 기술을 통해 사용할 수 있습니다. DocumentDB는 일관성 있는 쿼리를 제공하는 동시에 매우 빠른 쓰기의 지속적인 볼륨을 지원합니다. 문서 및 인덱스 저장소는 각 컬렉션에서 사용하는 저장소를 계산하는 데 사용됩니다. 컬렉션에 대한 인덱싱 정책을 구성하여 인덱싱과 관련된 저장소 및 성능 절충을 제어할 수 있습니다.

### 컬렉션의 인덱싱 정책 구성
각 컬렉션의 인덱싱 정책을 사용하여 인덱싱과 관련된 성능 및 저장소 절충을 수행할 수 있습니다. 인덱싱 구성의 일부로 사용할 수 있는 옵션은 다음과 같습니다.

-	컬렉션에서 모든 문서를 자동으로 인덱싱할지 여부를 선택합니다. 기본적으로 모든 문서가 자동으로 인덱싱됩니다. 자동 인덱싱을 해제하고 특정 문서만 인덱스에 선택적으로 추가할 수 있습니다. 반대로 특정 문서만 선택적으로 제외할 수도 있습니다. 문서를 삽입하거나 바꾸거나 삭제하는 동안 컬렉션의 indexingPolicy에서 automatic 속성을 true 또는 false로 설정하고 [x-ms-indexingdirective] 요청 헤더를 사용하면 됩니다.
-	문서의 특정 경로나 패턴을 인덱스에 포함할지 아니면 제외할지를 선택합니다. 컬렉션의 indexingPolicy에서 각각 includedPaths 및 excludedPaths를 설정하면 됩니다. 특정 경로 패턴의 범위 및 해시 쿼리에 대해 저장소 및 성능 절충을 구성할 수도 있습니다.
-	동기(일관성) 및 비동기(지연) 인덱스 업데이트 간에 선택합니다. 기본적으로 컬렉션의 문서를 삽입하거나 바꾸거나 삭제할 때마다 인덱스가 동기적으로 업데이트됩니다. 이렇게 하면 쿼리에 문서 읽기와 동일한 일관성 수준을 적용할 수 있습니다. DocumentDB는 쓰기 최적화되며 동기 인덱스 유지 관리 및 일관성 있는 쿼리 처리와 함께 지속적인 대량 문서 쓰기를 지원하지만 인덱스를 지연 업데이트하도록 특정 컬렉션을 구성할 수 있습니다. 지연 인덱스는 쓰기 성능을 더욱 향상하며 주로 읽기 중심인 컬렉션에 대한 대량 수집 시나리오에 적합합니다.

인덱싱 정책은 컬렉션에서 PUT를 실행하여 변경할 수 있습니다. [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx), [Azure 포털](https://portal.azure.com) 또는 [Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 통해 달성할 수 있습니다.

### 컬렉션 쿼리
컬렉션 내의 문서는 임의 스키마를 포함할 수 있으며, 스키마 또는 보조 인덱스를 미리 제공하지 않고 컬렉션 내의 문서를 쿼리할 수 있습니다. JavaScript 기반 UDF를 통해 풍부한 계층적, 관계형 및 공간 연산자와 확장성을 제공하는 [DocumentDB SQL 구문](https://msdn.microsoft.com/library/azure/dn782250.aspx)을 사용하여 컬렉션을 쿼리할 수 있습니다. JSON 문법을 통해 JSON 문서를 트리로 모델링하고 레이블을 트리 노드로 사용할 수 있습니다. 이 기능은 DocumentDB의 자동 인덱싱 기술과 DocumentDB의 SQL 언어 둘 다에서 사용됩니다. DocumentDB 쿼리 언어는 다음 세 가지 주요 측면으로 구성됩니다.

1.	기본적으로 계층적 쿼리 및 프로젝션을 포함하는 트리 구조에 매핑되는 작은 쿼리 작업 집합
2.	컴퍼지션, 필터, 프로젝션, 집계 및 자체 조인을 포함하는 관계형 작업 하위 집합
3.	(1) 및 (2)로 작동하는 순수 JavaScript기반 UDF

DocumentDB 쿼리 모델은 기능, 효율성 및 간결성 간의 균형을 이루려고 합니다. DocumentDB의 데이터베이스 엔진은 기본적으로 SQL 쿼리 문을 컴파일하고 실행합니다. [Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 컬렉션을 쿼리할 수 있습니다. .NET SDK에는 LINQ 공급자가 포함되어 있습니다.

> [AZURE.TIP] [쿼리 실습](https://www.documentdb.com/sql/demo)에서 DocumentDB를 사용해 보고 데이터 집합에 대해 SQL 쿼리를 실행할 수 있습니다.

### 다중 문서 트랜잭션
데이터베이스 트랜잭션은 데이터 동시 변경을 처리하기 위한 안전하고 예측 가능한 프로그래밍 모델을 제공합니다. RDBMS에서 비즈니스 논리를 작성하는 기존 방법은 **저장 프로시저** 및/또는 **트리거**를 작성하고 트랜잭션 실행을 위해 데이터베이스 서버에 전달하는 것입니다. RDBMS에서는 응용 프로그램 프로그래머가 다음 두 가지 프로그래밍 언어를 처리해야 합니다.

- (비트랜잭션) 응용 프로그램 프로그래밍 언어(예: JavaScript, Python, C#, Java 등)
- T-SQL(데이터베이스에서 고유하게 실행되는 트랜잭션 프로그래밍 언어)

데이터베이스 엔진 내에 직접 JavaScript 및 JSON을 전체 통합하므로 DocumentDB는 저장 프로시저 및 트리거 측면에서 컬렉션에 대해 직접 JavaScript 기반 응용 프로그램 논리를 실행하기 위한 직관적 프로그래밍 모델을 제공합니다. 여기에서는 다음 두 가지가 허용됩니다.

- 동시성 제어를 위한 효율적인 구현, 복구, 데이터베이스에 엔진에서 JSON 개체 그래프에 대한 직접적인 자동 인덱싱
- 자연스러운 제어 흐름 표현, 변수 범위 지정, JavaScript 프로그래밍 언어로 직접 데이터베이스 트랜잭션과 예외 처리 Primitive의 통합

그런 다음 컬렉션 수준에서 등록된 JavaScript 논리가 지정된 컬렉션의 문서에 대해 데이터베이스 작업을 실행할 수 있습니다. DocumentDB는 JavaScript 기반 저장 프로시저와 트리거를 앰비언트 ACID 트랜잭션 내에 암시적으로 래핑하고 컬렉션 내 문서에 스냅숏 격리를 사용합니다. 실행 중 JavaScript에서 예외가 발생하면 전체 트랜잭션이 중단됩니다. 결과로 생성된 프로그래밍 모델은 매우 단순하지만 강력합니다. JavaScript 개발자는 익숙한 언어 구문과 라이브러리 기본 형식을 사용하는 동시에 "내구성" 있는 프로그래밍 모델을 얻게 됩니다.

버퍼 풀과 동일한 주소 공간에 있는 데이터베이스 엔진 내에서 직접 JavaScript를 실행할 수 있으므로 컬렉션 문서에 대한 데이터베이스 작업의 신속한 트랜잭션 실행이 가능합니다. 또한 DocumentDB 데이터베이스 엔진은 JSON 및 JavaScript를 전체 통합하므로 응용 프로그램과 데이터베이스의 형식 시스템 간 불일치가 제거됩니다.

컬렉션을 만든 후 [Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 저장 프로시저, 트리거 및 UDF를 컬렉션에 등록할 수 있습니다. 등록이 완료되면 참조하고 실행할 수 있습니다. 아래 코드의 JavaScript로만 작성된 다음 저장 프로시저는 두 개의 인수(책 이름 및 저자 이름)를 사용하며 새 문서를 만들고 문서를 쿼리한 다음 업데이트합니다. 모든 작업은 암시적 ACID 트랜잭션 내에서 수행됩니다. 실행 중 언제든지 JavaScript 예외가 발생하면 전체 트랜잭션이 중단됩니다.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don’t need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

클라이언트는 HTTP POST를 통한 트랜잭션 실행을 위해 위의 JavaScript 논리를 데이터베이스에 "전달"할 수 있습니다. HTTP 메서드 사용에 대한 자세한 내용은 [DocumentDB 리소스와 RESTful 상호 작용](https://msdn.microsoft.com/library/azure/mt622086.aspx)을 참조하세요.

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


데이터베이스는 기본적으로 JSON 및 JavaScript를 이해하므로 형식 시스템 불일치가 없으며 "OR 매핑" 또는 코드 생성 기술도 필요 없습니다.

저장 프로시저와 트리거는 현재 컬렉션 컨텍스트를 노출하는 잘 정의된 개체 모델을 통해 컬렉션 및 컬렉션 내 문서를 조작합니다.

[Azure DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 DocumentDB의 컬렉션을 쉽게 만들거나 삭제하거나 읽거나 열거할 수 있습니다. DocumentDB는 컬렉션의 메타데이터 읽기 또는 쿼리에 대해 항상 강력한 일관성을 제공합니다. 컬렉션을 삭제하면 자동으로 컬렉션 내에 포함된 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF에 액세스할 수 없게 됩니다.

## 저장 프로시저, 트리거 및 UDF(사용자 정의 함수)
이전 섹션에서 설명한 대로 데이터베이스 엔진의 내부 트랜잭션 내에서 직접 실행할 응용 프로그램 논리를 작성할 수 있습니다. 전적으로 JavaScript로 응용 프로그램 논리를 작성하고 저장 프로시저, 트리거 또는 UDF로 모델링할 수 있습니다. 저장 프로시저 또는 트리거 내의 JavaScript 코드는 컬렉션 내 문서를 삽입하거나 바꾸거나 삭제하거나 읽거나 쿼리할 수 있습니다. 반면에 UDF 내에서 JavaScript는 문서를 삽입, 바꾸기 또는 삭제할 수 없습니다. UDF는 쿼리 결과 집합의 문서를 열거하고 다른 결과 집합을 생성합니다. 다중 테넌트 지원을 위해 DocumentDB는 엄격한 예약 기반 리소스 거버넌스를 적용합니다. 각 저장 프로시저, 트리거 또는 UDF는 작업 수행을 위해 운영 체제 리소스의 고정 퀀텀을 받습니다. 또한 저장 프로시저, 트리거 또는 UDF는 외부 JavaScript 라이브러리에 대해 연결할 수 없으며 할당된 리소스 예산을 초과할 경우 블랙리스트에 추가됩니다. REST API를 통해 저장 프로시저, 트리거 또는 UDF를 컬렉션에 등록하거나 등록 취소할 수 있습니다. 등록하면 저장 프로시저, 트리거 또는 UDF가 사전 컴파일되고 나중에 실행되는 바이트 코드로 저장됩니다. 다음 섹션에서는 DocumentDB JavaScript SDK를 사용하여 저장 프로시저, 트리거 및 UDF를 등록, 실행 및 등록 취소하는 방법을 보여 줍니다. JavaScript SDK는 [DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 위의 단순한 래퍼입니다.

### 저장 프로시저 등록
저장 프로시저 등록은 HTTP POST를 통해 컬렉션에 새 저장 프로시저 리소스를 만드는 것입니다.

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

### 저장 프로시저 실행
저장 프로시저 실행은 요청 본문에서 프로시저에 매개 변수를 전달하여 기존 저장 프로시저 리소스에 대해 HTTP POST를 실행하여 수행합니다.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

### 저장 프로시저 등록 취소
저장 프로시저 등록 취소는 단순히 기존 저장 프로시저 리소스에 대해 HTTP DELETE를 실행하여 수행합니다.

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


### 사전 트리거 등록
트리거 등록은 HTTP POST를 통해 컬렉션에 새 트리거 리소스를 만들어 수행합니다. 트리거가 사전 트리거인지 아니면 사후 트리거인지를 지정하고 연결될 수 있는 작업 유형(예: 만들기, 바꾸기, 삭제 또는 모두)을 지정할 수 있습니다.

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

### 사전 트리거 실행
트리거 실행은 요청 헤더를 통해 문서 리소스의 POST/PUT/DELETE 요청을 실행할 때 기존 트리거의 이름을 지정하여 수행합니다.
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

### 사전 트리거 등록 취소
트리거 등록 취소는 단순히 기존 트리거 리소스에 대해 HTTP DELETE를 실행하여 수행합니다.

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

### UDF 등록
UDF 등록은 HTTP POST를 통해 컬렉션에 새 UDF 리소스를 만들어 수행합니다.

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

### 쿼리의 일부로 UDF 실행
UDF는 SQL 쿼리의 일부로 지정될 수 있으며, [DocumentDB의 핵심 SQL 쿼리 언어](https://msdn.microsoft.com/library/azure/dn782250.aspx)를 확장하는 방법으로 사용됩니다.

	var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

### UDF 등록 취소 
UDF 등록 취소는 단순히 기존 UDF 리소스에 대해 HTTP DELETE를 실행하여 수행합니다.

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

위 조각에서는 [DocumentDB JavaScript SDK](https://github.com/Azure/azure-documentdb-js)를 통한 등록(POST), 등록 취소(PUT), 읽기/나열(GET) 및 실행(POST)을 보여 주었지만 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 다른 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용할 수도 있습니다.

## 문서
컬렉션의 임의 JSON 문서를 삽입하고, 바꾸고, 삭제하고, 읽고, 열거하고, 쿼리할 수 있습니다. DocumentDB는 스키마를 위임하지 않으며 컬렉션 내 문서 쿼리를 지원하기 위해 보조 인덱스가 필요하지 않습니다.

진정한 개방형 데이터베이스 서비스인 DocumentDB는 직렬화된 데이터 형식(예: 날짜/시간) 또는 JSON 문서에 대한 특정 인코딩을 고안하지 않습니다. DocumentDB는 다양한 문서 간의 관계를 분류하기 위한 특별한 JSON 규칙이 필요 없습니다. DocumentDB의 SQL 구문에서 특별한 주석 없이 문서를 쿼리 및 프로젝션하는 강력한 계층적 관계형 쿼리 연산자를 제공하거나 고유 속성을 사용하여 문서 간의 관계를 분류해야 합니다.
 
다른 모든 리소스와 마찬가지로, REST API 또는 [클라이언트 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 사용하여 문서를 쉽게 만들고, 바꾸고, 삭제하고, 읽고, 열거하고, 쿼리할 수 있습니다. 문서를 삭제하면 중첩된 모든 첨부 파일에 해당하는 할당량이 즉시 해제됩니다. 문서의 읽기 일관성 수준은 데이터베이스 계정의 일관성 정책을 따릅니다. 응용 프로그램의 데이터 일관성 요구 사항에 따라 요청 단위로 이 정책을 재정의할 수 있습니다. 문서를 쿼리할 때 읽기 일관성은 컬렉션에 설정된 인덱싱 모드를 따릅니다. "일관성"의 경우 계정의 일관성 정책을 따릅니다.

## 첨부 파일 및 미디어
>[AZURE.NOTE] 첨부 파일 및 미디어 리소스는 미리 보기 기능입니다.
 
DocumentDB는 DocumentDB에 포함 또는 원격 미디어 스토어에 이진 Blob/미디어를 저장하도록 허용합니다. 또한 첨부 파일이라는 특수 문서 측면에서 미디어의 메타데이터를 나타낼 수 있습니다. DocumentDB의 첨부 파일은 다른 곳에 저장된 미디어/blob을 참조하는 특수(JSON) 문서입니다. 첨부 파일은 단순히 원격 미디어 저장소에 저장된 미디어의 메타데이터(예: 위치, 작성자 등)를 캡처하는 특수 문서입니다.

DocumentDB를 사용하여 잉크 주석과 설명, 강조, 책갈피, 등급, 좋아요/싫어요 등 지정된 사용자의 전자책에 대해 연결된 메타데이터를 저장하는 특수 읽기 응용 프로그램을 고려해 보세요.

-	책 자체의 내용은 DocumentDB 데이터베이스 계정의 일부로 사용 가능한 미디어 저장소나 원격 미디어 저장소에 저장됩니다.
-	응용 프로그램은 각 사용자의 메타데이터를 개별 문서로 저장할 수 있습니다. 예를 들어 book1에 대한 Joe의 메타데이터는 /colls/joe/docs/book1로 참조된 문서에 저장됩니다.
-	지정된 사용자 책의 콘텐츠 페이지를 가리키는 첨부 파일은 해당 문서 아래에 저장됩니다(예: /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 등).

위에 나열된 예제에서는 ID를 사용하여 리소스 계층 구조를 전달합니다. 고유한 리소스 ID를 사용하여 REST API를 통해 리소스에 액세스합니다.

DocumentDB에서 관리되는 미디어의 경우 첨부 파일의 \_media 속성이 해당 URI로 미디어를 참조합니다. DocumentDB는 처리 중인 참조가 모두 삭제되면 미디어를 가비지 수집합니다. 새 미디어를 업로드하면 DocumentDB에서 자동으로 첨부 파일을 생성하고 새로 추가한 미디어를 가리키도록 \_media를 채웁니다. 직접 관리하는 원격 blob 저장소(예: OneDrive, Azure Storage, DropBox 등)에 미디어를 저장하는 경우에도 첨부 파일을 사용하여 미디어를 참조할 수 있습니다. 이 경우 직접 첨부 파일을 만들고 해당 \_media 속성을 채웁니다.

다른 모든 리소스와 마찬가지로, REST API 또는 클라이언트 SDK를 사용하여 첨부 파일을 쉽게 만들거나, 바꾸거나, 삭제하거나, 읽거나, 열거할 수 있습니다. 문서와 마찬가지로, 첨부 파일의 읽기 일관성 수준은 데이터베이스 계정의 일관성 정책을 따릅니다. 응용 프로그램의 데이터 일관성 요구 사항에 따라 요청 단위로 이 정책을 재정의할 수 있습니다. 첨부 파일을 쿼리할 때 읽기 일관성은 컬렉션에 설정된 인덱싱 모드를 따릅니다. "일관성"의 경우 계정의 일관성 정책을 따릅니다.  
## 사용자
DocumentDB 사용자는 사용 권한 그룹화를 위한 논리적 네임스페이스를 나타냅니다. DocumentDB의 사용자는 ID 관리 시스템이나 미리 정의된 응용 프로그램 역할의 사용자에 해당할 수 있습니다. DocumentDB에서 사용자는 단순히 데이터베이스 아래의 사용 권한 집합을 그룹화하는 추상화를 나타냅니다.

응용 프로그램에 대해 다중 테넌트 지원을 구현하기 위해 실제 사용자 또는 응용 프로그램 테넌트에 해당하는 사용자를 DocumentDB에 만들 수 있습니다. 그런 다음 지정된 사용자에 대해 다양한 컬렉션, 문서, 첨부 파일 등의 액세스 제어에 해당하는 사용 권한을 만들 수 있습니다.

사용자 증가와 더불어 응용 프로그램이 확장되어야 하므로 데이터를 분할하는 다양한 방법을 채택할 수 있습니다. 각 사용자를 다음과 같이 모델링할 수 있습니다.

-	각 사용자가 데이터베이스에 매핑됩니다.
-	각 사용자가 컬렉션에 매핑됩니다.
-	여러 사용자에 해당하는 문서가 전용 컬렉션으로 이동합니다.
-	여러 사용자에 해당하는 문서가 컬렉션 집합으로 이동합니다.

선택한 특정 분할 전략에 관계없이 실제 사용자를 DocumentDB 데이터베이스의 사용자로 모델링하고 세분화된 사용 권한을 각 사용자에 연결할 수 있습니다.

![사용자 컬렉션][3] 
**분할 전략 및 사용자 모델링**

다른 모든 리소스와 마찬가지로, REST API 또는 클라이언트 SDK를 사용하여 DocumentDB의 사용자를 쉽게 만들거나, 바꾸거나, 삭제하거나, 읽거나, 열거할 수 있습니다. DocumentDB는 사용자 리소스의 메타데이터 읽기 또는 쿼리에 대해 항상 강력한 일관성을 제공합니다. 사용자를 삭제하면 자동으로 사용자 내에 포함된 사용 권한에 액세스할 수 없게 됩니다. DocumentDB는 삭제된 사용자의 일부인 사용 권한 할당량을 백그라운드에서 회수하지만 삭제된 사용 권한을 즉시 다시 사용할 수 있습니다.

## 권한
액세스 제어 관점에서 데이터베이스 계정, 데이터베이스, 사용자, 사용 권한 등의 리소스는 관리 권한이 필요하기 때문에 *관리* 리소스로 간주됩니다. 반면, 컬렉션, 문서, 첨부 파일, 저장 프로시저, 트리거 및 UDF를 비롯한 리소스는 지정된 데이터베이스 아래로 범위가 지정되며 *응용 프로그램 리소스*로 간주됩니다. 이를 액세스하는 두 가지 유형의 리소스 및 역할(즉, 관리자 및 사용자)에 따라 권한 부여 모델은 두 가지 유형의 *액세스 키*인 *마스터 키*와 *리소스 키*를 정의합니다. 마스터 키는 데이터베이스 계정의 일부이며 데이터베이스 계정을 프로비전하는 개발자(또는 관리자)에게 제공됩니다. 이 마스터 키는 관리 리소스 및 응용 프로그램 리소스 둘 다에 대한 액세스 권한 부여에 사용될 수 있다는 점에서 관리자 의미 체계를 갖습니다. 반면, 리소스 키는 *특정* 응용 프로그램 리소스에 대한 액세스를 허용하는 세분화된 액세스 키입니다. 따라서 데이터베이스 사용자와 특정 리소스(예: 컬렉션, 문서, 첨부 파일, 저장 프로시저, 트리거 또는 UDF)에 대한 이 사용자의 사용 권한 간 관계를 캡처합니다.

리소스 키를 받으려면 지정된 사용자로 사용 권한 리소스를 만들어야 합니다. 사용 권한을 만들거나 검색하려면 권한 부여 헤더에 마스터 키가 있어야 합니다. 사용 권한 리소스는 리소스, 해당 액세스 권한 및 사용자를 연결합니다. 사용 권한 리소스를 만든 후에는 사용자가 관련 리소스에 액세스하기 위해 관련 리소스 키만 제공하면 됩니다. 따라서 리소스 키를 사용 권한 리소스의 단순한 논리적 표현으로 간주할 수 있습니다.

다른 모든 리소스와 마찬가지로, REST API 또는 클라이언트 SDK를 사용하여 DocumentDB의 사용 권한을 쉽게 만들거나, 바꾸거나, 삭제하거나, 읽거나, 열거할 수 있습니다. DocumentDB는 사용 권한의 메타데이터 읽기 또는 쿼리에 대해 항상 강력한 일관성을 제공합니다.

## 다음 단계
[DocumentDB 리소스와의 RESTful 상호 작용](https://msdn.microsoft.com/library/azure/mt622086.aspx)에서 HTTP 명령을 사용하여 리소스를 사용하는 방법을 알아봅니다.


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

<!---HONumber=AcomDC_0921_2016-->