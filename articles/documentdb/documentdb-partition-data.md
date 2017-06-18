<properties 
	pageTitle="Azure DocumentDB의 분할 및 크기 조정 | Microsoft Azure"      
	description="Azure DocumentDB에서 분할 작동 방법, 분할 및 파티션 키를 구성하는 방법 및 응용 프로그램에 대한 올바른 파티션 키를 선택하는 방법에 대해 알아봅니다."         
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="arramac"/>

# Azure DocumentDB의 분할 및 크기 조정
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)는 신속하고 예측 가능한 성능을 얻을 수 있고, 응용 프로그램 증가에 따라 효율적인 확장이 가능하도록 설계되었습니다. 이 문서에서는 DocumentDB에서 분할이 작동하는 방식을 개괄적으로 살펴보고, 응용 프로그램을 효과적으로 확장하도록 DocumentDB 컬렉션을 구성하는 방법을 설명합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- Azure DocumentDB에서 분할이 작동하는 방식
- DocumentDB에서 분할을 구성하는 방법
- 파티션 키의 정의 및 응용 프로그램에 올바른 파티션 키를 선택하는 방법

코드를 시작하려면 [DocumentDB 성능 테스트 드라이버 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)에서 프로젝트를 다운로드합니다.

## DocumentDB의 분할

DocumentDB에서는 규모에 상관없이 밀리초 단위의 응답 시간 순으로 스키마 없는 JSON 문서를 저장하고 쿼리할 수 있습니다. DocumentDB는 **컬렉션**이라는 데이터 저장용 컨테이너를 제공합니다. 컬렉션은 하나 이상의 물리적 파티션 또는 서버에 걸쳐 있을 수 있는 논리적 리소스입니다. 파티션 수는 컬렉션의 프로비전된 처리량 및 저장소 크기에 따라 DocumentDB에 의해 결정됩니다. DocumentDB의 모든 파티션은 고정된 양의 SSD 지원 저장소에 연결되며, 고가용성을 위해 복제됩니다. 파티션 관리는 Azure DocumentDB에 의해 완전히 관리되므로 복잡한 코드를 작성하거나 파티션을 관리할 필요가 없습니다. DocumentDB 컬렉션은 저장소 및 처리량이 **거의 무제한**입니다.

분할은 응용 프로그램에 완전히 투명합니다. DocumentDB는 빠른 읽기 및 쓰기, SQL 및 LINQ 쿼리, JavaScript 기반 트랜잭션 논리, 일관성 수준 및 단일 컬렉션 리소스에 대한 REST API 호출을 통한 세분화된 액세스 제어를 지원합니다. 이 서비스는 파티션 간의 데이터 분산 및 올바른 파티션에 대한 쿼리 요청을 처리합니다.

작동 방식 DocumentDB에 컬렉션을 만드는 경우 **파티션 키 속성** 구성을 지정할 수 있습니다. 이는 DocumentDB에서 여러 서버 또는 파티션 간에 데이터를 분산시키는 데 사용할 수 있는 문서 내 JSON 속성(또는 경로)입니다. DocumentDB는 파티션 키 값을 해시하고 해시된 결과를 사용하여 JSON 문서를 저장할 파티션을 결정합니다. 파티션 키가 동일한 모든 문서가 동일한 파티션에 저장됩니다.

직원 및 부서에 대한 데이터를 DocumentDB에 저장하는 응용 프로그램을 예로 들어 보겠습니다. 데이터를 부서별로 확장하기 위해 `"department"`를 파티션 키 속성으로 선택합니다. DocumentDB의 모든 문서는 필수 `"id"` 속성을 포함해야 합니다. 이 속성은 동일한 파티션 키 값을 가진 모든 문서에 대해 고유해야 합니다(예: `"Marketing`"). 컬렉션에 저장된 모든 문서에는 파티션 키와 ID의 고유한 조합이 있어야 합니다(예: `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` 및 `{ "Department": "Sales", "id": "0001" }`). 즉, (파티션 키, id)의 복합 속성이 컬렉션의 기본 키입니다.

### 파티션 키
파티션 키의 선택은 디자인 타임에서 결정해야 하는 중요한 사항입니다. 광범위한 값을 가지고 균등하게 분산된 액세스 패턴이 있을 가능성이 높은 JSON 속성 이름을 선택해야 합니다. 파티션 키는 JSON 경로로 지정되며 예를 들어 `/department`는 속성 부서를 나타냅니다.

다음 표는 파티션 키 정의 및 각각에 해당하는 JSON 값의 예를 보여 줍니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>파티션 키 경로</strong></p></td>
            <td valign="top"><p><strong>설명</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>doc가 문서인 doc.department의 JSON 값에 해당합니다.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>doc가 문서인 doc.properties.name의 JSON 값에 해당합니다(중첩된 속성).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>doc.id의 JSON 값에 해당합니다(ID 및 파티션 키는 동일한 속성).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"department name"</p></td>
            <td valign="top"><p>doc가 문서인 doc["department name"]의 JSON 값에 해당합니다.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] 파티션 키 경로에 대한 구문은 값 대신 속성에 해당하는 경로인 주요 차이점으로 인덱싱 정책 경로에 대한 경로 지정과 비슷합니다. 즉, 끝에는 와일드 카드가 없습니다. 예를 들어 부서 아래에서 값을 인덱싱하도록 /department/?를 지정하지만 /department를 파티션 키 정의로 지정합니다. 파티션 키 경로는 암시적으로 인덱싱되며 인덱싱 정책 재정의를 사용하여 인덱싱에서 제외될 수 없습니다.

파티션 키의 선택이 응용 프로그램의 성능에 미치는 영향을 살펴보겠습니다.

### 분할 및 프로비전된 처리량
DocumentDB는 예측 가능한 성능을 위해 설계되었습니다. 컬렉션을 만들 때 **초당 RU([요청 단위](documentdb-request-units.md))** 면에서 처리량을 예약합니다. 각 요청에는 작업에 소비된 CPU 및 IO 같은 시스템 리소스의 양에 비례하는 요청 단위 요금이 할당됩니다. 세션 일관성에 따라 1kB 문서를 읽을 경우 1RU(요청 단위)가 소비됩니다. 저장된 항목 수 또는 동시에 실행되는 요청 수에 상관없이 읽기당 1RU입니다. 문서의 크기가 클수록 크기에 따라 더 높은 요청 단위가 필요합니다. 응용 프로그램에 지원해야 하는 엔터티 크기 및 읽기 수를 알고 있는 경우 응용 프로그램의 읽기 요구 사항에 필요한 정확한 양의 처리량을 프로비전할 수 있습니다.

DocumentDB는 문서를 저장할 때 파티션 키 값에 따라 파티션 간에 문서를 균등하게 분산시킵니다. 처리량도 사용 가능한 파티션 간에 균등하게 분산됩니다(즉, 파티션당 처리량 = (컬렉션당 총 처리량)/(파티션 수)).

>[AZURE.NOTE] 컬렉션의 전체 처리량을 실현하려면 다양한 파티션 키 값 간에 요청을 균등하게 분산시킬 수 있는 파티션 키를 선택해야 합니다.

## 단일 파티션 및 분할된 컬렉션
DocumentDB에서는 단일 파티션과 분할된 컬렉션을 모두 만들 수 있습니다.

- **분할된 컬렉션**은 여러 파티션에 걸쳐 있고 매우 많은 양의 저장소와 처리량을 지원할 수 있습니다. 컬렉션에 대한 파티션 키를 지정해야 합니다.
- **단일 파티션 컬렉션**은 더욱 저렴한 가격 옵션을 제공하며 모든 컬렉션 데이터에서 쿼리 및 트랜잭션을 수행할 수 있습니다. 단일 파티션의 확장성 및 저장소 제한이 있습니다. 이러한 컬렉션에 대해서는 파티션 키를 지정할 필요가 없습니다.

![DocumentDB의 분할된 컬렉션][2]

많은 양의 저장소 또는 처리량이 필요하지 않은 시나리오에는 단일 파티션 컬렉션이 적합합니다. 단일 파티션 컬렉션에는 단일 파티션의 확장성 및 저장소 제한이 있습니다. 즉, 최대 10GB의 저장소 및 초당 최대 10,000개의 요청 단위가 지원됩니다.

분할된 컬렉션은 매우 많은 양의 저장소와 처리량을 지원할 수 있습니다. 그러나 기본적으로 최대 250GB를 저장하고 초당 최대 250,000개의 요청 단위로 확장할 수 있습니다. 컬렉션당 더 높은 저장소 또는 처리량이 필요한 경우 [Azure 지원](documentdb-increase-limits.md)에 사용자 계정에 대한 용량 증가를 요청해야 합니다.

다음 표에서 단일 파티션 작업과 분할된 컬렉션 작업의 차이점을 보여 줍니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>단일 파티션 컬렉션</strong></p></td>
            <td valign="top"><p><strong>분할된 컬렉션</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Partition Key</p></td>
            <td valign="top"><p>없음</p></td>
            <td valign="top"><p>필수</p></td>
        </tr>
        <tr>
            <td valign="top"><p>문서의 기본 키</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>&lt;파티션 키>와 "id"의 복합 키</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최소 저장소</p></td>
            <td valign="top"><p>0GB</p></td>
            <td valign="top"><p>0GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최대 저장소</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>무제한(기본적으로 250GB)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최소 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 400개</p></td>
            <td valign="top"><p>초당 요청 단위 10,000개</p></td>
        </tr>
        <tr>
            <td valign="top"><p>최대 처리량</p></td>
            <td valign="top"><p>초당 요청 단위 10,000개</p></td>
            <td valign="top"><p>무제한(기본적으로 초당 요청 단위 250,000개)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API 버전</p></td>
            <td valign="top"><p>모두</p></td>
            <td valign="top"><p>API 2015-12-16 이상</p></td>
        </tr>
    </tbody>
</table>

## SDK 사용

Azure DocumentDB에는 [REST API 버전 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx)을 사용한 자동 분할에 대한 지원이 추가되었습니다. 분할된 컬렉션을 만들려면 지원되는 SDK 플랫폼(.NET, Node.js, Java, Python) 중 하나에서 SDK 버전 1.6.0 이상을 다운로드해야 합니다.

### 분할된 컬렉션 만들기

다음 샘플에서는 처리량이 초당 20,000개의 요청 단위인 장치 원격 분석 데이터를 저장하는 컬렉션을 만드는 .NET 코드 조각을 보여 줍니다. SDK는 OfferThroughput 값을 설정합니다(이 값은 REST API에서 `x-ms-offer-throughput` 요청 헤더를 설정함). 여기에서는 `/deviceId`를 파티션 키로 설정합니다. 선택한 파티션 키는 이름 및 인덱싱 정책과 같은 나머지 컬렉션 메타데이터와 함께 저장됩니다.

이 샘플에서는 (a) 장치 수가 많고, 쓰기가 파티션 간에 균등하게 분산될 수 있으며, 대용량 데이터를 수집하도록 데이터베이스를 확장할 수 있으며, (b) 장치에 대한 최신 읽기 가져오기와 같은 요청이 대부분 단일 deviceId로 범위가 지정되고 단일 파티션에서 검색될 수 있음을 알고 있기 때문에 `deviceId`를 선택했습니다.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] 분할된 컬렉션을 만들려면 초당 요청 단위 10,000개의 처리량 값을 지정해야 합니다. 처리량은 100의 배수이므로 10,100 이상이어야 합니다.

이 메서드에서 DocumentDB에 대한 REST API 호출을 실행하면 이 서비스가 요청된 처리량에 따라 파티션 수를 프로비전합니다. 성능은 발전이 필요하므로 컬렉션의 처리량을 변경할 수 있습니다. 자세한 내용은 [성능 수준](documentdb-performance-levels.md)을 참조하세요.

### 문서 읽기 및 쓰기

이제 DocumentDB에 데이터를 삽입해 보겠습니다. 다음은 장치 읽기 및 새 장치 읽기를 컬렉션에 삽입하는 CreateDocumentAsync 호출이 포함된 샘플 클래스입니다.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


파티션 키와 id로 문서를 읽고, 업데이트한 다음, 마지막 단계로 파티션 키와 id로 해당 문서를 삭제해 보겠습니다. 읽기에 PartitionKey 값(REST API의 `x-ms-documentdb-partitionkey` 요청 헤더에 해당)이 포함됩니다.

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### 분할된 컬렉션 쿼리

분할된 컬렉션에서 데이터를 쿼리하면 DocumentDB에서 필터에 지정된 파티션 키 값(있는 경우)에 해당하는 파티션으로 쿼리를 자동으로 라우팅합니다. 예를 들어 이 쿼리는 파티션 키 "XMS-0001"이 포함된 파티션으로만 라우팅됩니다.

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    	UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

다음 쿼리는 파티션 키(DeviceId)에 대한 필터가 없으므로 파티션의 인덱스에 대해 실행되는 모든 파티션으로 팬아웃됩니다. SDK가 파티션에 걸쳐 쿼리를 실행하도록 EnableCrossPartitionQuery(REST API의 `x-ms-documentdb-query-enablecrosspartition`)를 지정해야 합니다.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### 병렬 쿼리 실행

DocumentDB SDK 1.9.0 이상에서는 많은 수의 파티션에 연결해야 할 경우에도 분할된 컬렉션에 대해 대기 시간이 짧은 쿼리를 수행할 수 있도록 하는 병렬 쿼리 실행 옵션을 지원합니다. 예를 들어 다음 쿼리는 파티션에 걸쳐 병렬로 실행되도록 구성되어 있습니다.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

- `MaxDegreeOfParallelism`을 설정하여 컬렉션의 파티션에 대한 최대 동시 네트워크 연결 수를 나타내는 병렬 처리 수준을 제어할 수 있습니다. 이 값을 -1로 설정하는 경우 병렬 처리 수준이 SDK에서 관리됩니다. `MaxDegreeOfParallelism` 값이 지정되지 않거나 기본값인 0으로 설정된 경우 컬렉션의 파티션에 단일 네트워크 연결이 생성됩니다.
- `MaxBufferedItemCount`를 설정하여 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률 간에 균형을 유지할 수 있습니다. 이 매개 변수를 생략하거나 -1로 설정하는 경우 병렬 쿼리 실행 중에 버퍼링되는 항목의 수가 SDK에서 관리됩니다.

컬렉션에 동일한 상태를 지정할 경우, 병렬 쿼리는 직렬 실행의 경우와 동일한 순서로 결과를 반환합니다. 정렬(ORDER BY 및/또는 TOP)을 포함하는 파티션 간 쿼리를 수행할 경우 DocumentDB SDK는 파티션에 걸쳐 병렬로 쿼리를 실행하고, 클라이언트 쪽에서 부분적으로 정렬된 결과를 병합하여 전역으로 정렬된 결과를 생성합니다.

### 저장된 프로시저 실행

장치 ID가 동일한 문서에 대해 원자성 트랜잭션을 실행할 수도 있습니다(예: 장치의 최신 상태 또는 집계를 단일 문서에서 유지 관리하는 경우).

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

다음 섹션에서는 단일 파티션 컬렉션에서 분할된 컬렉션으로 이동하는 방법에 대해 살펴봅니다.

<a name="migrating-from-single-partition"></a>
### 단일 파티션에서 분할된 컬렉션으로 마이그레이션
단일 파티션 컬렉션을 사용한 응용 프로그램에 더 높은 처리량(>10,000RU/s) 또는 더 큰 데이터 저장소(>10GB)가 필요한 경우 [DocumentDB 데이터 마이그레이션 도구](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)를 사용하여 단일 파티션 컬렉션에서 분할된 컬렉션으로 데이터를 마이그레이션할 수 있습니다.

단일 파티션 컬렉션에서 분할된 컬렉션으로 마이그레이션하려면

1. 단일 파티션 컬렉션에서 JSON으로 데이터를 내보냅니다. 자세한 내용은 [JSON 파일로 내보내기](documentdb-import-data.md#export-to-json-file)를 참조하세요.
2. 아래 예제와 같이 파티션 키 정의 및 초당 요청 단위 10,000개 이상의 처리량을 사용하여 만든 분할된 컬렉션으로 데이터를 가져옵니다. 자세한 내용은 [DocumentDB로 가져오기](documentdb-import-data.md#DocumentDBSeqTarget)를 참조하세요.

![DocumentDB에서 분할된 컬렉션으로 데이터 마이그레이션][3]

>[AZURE.TIP] 가져오기 시간을 단축하기 위해 분할된 컬렉션에 사용할 수 있는 더 높은 처리량을 활용하도록 병렬 요청의 수를 100 이상으로 증가시키는 것이 좋습니다.

이제 기본 사항을 완료했으므로 DocumentDB에서 파티션 키로 작업할 때 고려해야 하는 몇 가지 중요한 디자인 사항을 살펴보겠습니다.

## 분할 디자인
파티션 키의 선택은 디자인 타임에서 결정해야 하는 중요한 사항입니다. 이 섹션에서는 컬렉션에 대한 파티션 키를 선택할 때의 일부 장단점을 설명합니다.

### 파티션 키를 트랜잭션 경계로 사용
파티션 키를 선택할 때는 트랜잭션 사용에 대한 요구 사항과 여러 파티션 키에 엔터티를 분산하는 데 대한 요구 사항(솔루션의 확장성 향상) 간에 균형을 유지해야 합니다. 한 쪽으로 치우치면 모든 문서에 대해 동일한 파티션 키를 설정할 수 있지만 솔루션의 확장성이 제한될 수 있습니다. 다른 쪽으로 치우치면 각 문서에 대해 고유한 파티션 키를 할당할 수 있으므로 확장성이 매우 높지만 저장 프로시저 및 트리거를 통해 문서 트랜잭션 간에 사용할 수 없습니다. 이상적인 파티션 키는 효율적인 쿼리를 사용할 수 있도록 하고 솔루션을 확장할 수 있는 충분한 카디널리티가 있는 키입니다.

### 저장소 및 성능 병목 현상 방지 
다양한 값 간에 쓰기를 분산시킬 수 있는 속성을 선택해야 합니다. 동일한 파티션 키에 대한 요청은 단일 파티션의 처리량을 초과할 수 없으며 제한됩니다. 따라서 응용 프로그램 내에서 **"핫 스폿"**을 초래하지 않는 파티션 키를 선택해야 합니다. 또한 파티션 키가 동일한 문서에 대한 총 저장소 크기는 저장소에서 10GB를 초과할 수 없습니다.

### 적절한 파티션 키의 예
응용 프로그램의 파티션 키를 선택하는 방법에 대한 몇 가지 예는 다음과 같습니다.

* 사용자 프로필 백 엔드를 구현하는 경우 사용자 ID를 파티션 키로 선택하는 것이 좋습니다.
* IoT 데이터(예: 장치 상태)를 저장하는 경우 장치 ID를 파티션 키로 선택하는 것이 좋습니다.
* 시계열 데이터를 기록하는 데 DocumentDB를 사용하는 경우 호스트 이름 또는 프로세스 ID를 파티션 키로 선택하는 것이 좋습니다.
* 다중 테넌트 아키텍처가 있는 경우 테넌트 ID를 파티션 키로 선택하는 것이 좋습니다.

일부 사례(예: 위에 설명된 IoT 및 사용자 프로필)에서는 파티션 키가 사용자의 id(문서 키)와 동일할 수 있습니다. 시계열 데이터와 같은 다른 사례에서는 id와 다른 파티션 키를 선택할 수 있습니다.

### 분할 및 로깅/시계열 데이터
DocumentDB의 가장 일반적인 사용 사례 중 하나는 로깅 및 원격 분석입니다. 방대한 양의 데이터를 읽기/쓰기 해야 할 수 있으므로 적절한 파티션 키를 선택해야 합니다. 선택은 사용자의 읽기 및 쓰기 비율 및 실행할 쿼리의 종류에 따라 달라집니다. 다음은 적절한 파티션 키를 선택하는 방법에 대한 몇 가지 팁입니다.

- 사용 사례가 오랜 기간 동안 축적하는 작은 비율의 쓰기를 포함하고 타임스탬프 및 다른 필터의 범위를 기준으로 쿼리해야 하는 경우 파티션 키로 타임스탬프(예: 날짜)의 롤업을 사용하는 것이 좋은 방법입니다. 이렇게 하면 단일 파티션에서 날짜에 대한 모든 데이터를 쿼리할 수 있습니다.
- 작업이 일반적으로 더 일반적인 쓰기 작업이 많은 경우 DocumentDB가 파티션 수에 쓰기를 균등하게 분산할 수 있도록 타임스탬프에 기반하지 않는 파티션 키를 사용해야 합니다. 여기서 높은 카디널리티의 호스트 이름, 프로세스 ID, 작업 ID 또는 다른 속성을 선택하는 것이 좋습니다.
- 세 번째 방법은 여러 컬렉션이 있는(각 일/월에 대해 하나) 하이브리드이며 파티션 키는 호스트 이름처럼 세부적인 속성입니다. 시간 창에 기반한 다양한 성능 수준을 설정할 수 있다는 장점이 있습니다. 예를 들어 현재 월에 대한 컬렉션은 읽기 및 쓰기를 제공하므로 높은 처리량으로 프로비전되는 반면 이전 달은 읽기만을 제공하므로 낮은 처리량으로 프로비전됩니다.

### 분할 및 다중 테넌트
DocumentDB를 사용하여 다중 테넌트 응용 프로그램을 구현하는 경우 DocumentDB로 테넌시를 구현하는 두 가지 주요 패턴이 있습니다. 즉, 테넌트당 하나의 파티션 키와 테넌트당 하나의 컬렉션입니다. 각각의 장단점은 다음과 같습니다.

* 테넌트당 하나의 파티션 키: 이 모델에서는 테넌트가 단일 컬렉션 내에 함께 배치됩니다. 그러나 단일 파티션 내의 문서에 대한 쿼리 및 삽입을 단일 파티션에 대해 수행할 수 있습니다. 또한 테넌트 내의 모든 문서에서 트랜잭션 논리를 구현할 수 있습니다. 다중 테넌트는 컬렉션을 공유하기 때문에 각 테넌트에 대한 추가 헤드룸을 프로비전하는 대신 테넌트에 대한 리소스를 풀링하여 저장소 및 처리량을 단일 컬렉션 내에 저장할 수 있습니다. 단점은 테넌트당 성능 격리가 없다는 점입니다. 성능/처리량 증가가 대상 테넌트에 대한 증가가 아니라 전체 컬렉션에 적용됩니다.
* 테넌트당 하나의 컬렉션: 각 테넌트에 고유한 컬렉션이 있습니다. 이 모델에서는 테넌트당 성능을 예약할 수 있습니다. DocumentDB의 새로운 사용량 기반 가격 책정 모델을 사용하는 이 모델은 테넌트 수가 적은 다중 테넌트 응용 프로그램에 보다 비용 효율적입니다.

소수의 테넌트를 함께 배치하고 더 큰 테넌트를 고유한 컬렉션으로 마이그레이션하는 조합/계층형 접근 방식을 사용할 수도 있습니다.

## 다음 단계
이 문서에서는 Azure DocumentDB에서 분할이 작동하는 방식, 분할된 컬렉션을 만드는 방법 및 응용 프로그램에 적합한 파티션 키를 선택하는 방법을 설명했습니다.

-   DocumentDB를 사용하여 규모 및 성능 테스트를 수행합니다. 샘플에 대해서는 [Azure DocumentDB를 사용한 성능 및 규모 테스트](documentdb-performance-testing.md)를 참조하세요.
-   [SDK](documentdb-sdk-dotnet.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 코딩 시작
-   [DocumentDB의 프로비전된 처리량](documentdb-performance-levels.md)에 대한 자세한 정보
-   응용 프로그램에서 분할을 수행하는 방법을 사용자 지정하려는 경우 고유한 클라이언트 쪽 분할 구현에 연결할 수 있습니다. [클라이언트 쪽 분할 지원](documentdb-sharding.md)을 참조하세요.

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png

 

<!---HONumber=AcomDC_0921_2016-->