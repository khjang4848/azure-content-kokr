<properties
	pageTitle="데이터 연결: 이벤트 스트림의 데이터 스트림 입력 | Microsoft Azure"
	description="'inputs'이라는 스트림 분석에 대해 데이터 연결을 설정하는 방법을 알아보세요. 입력에는 이벤트의 데이터 스트림과 참조 데이터가 포함되어 있습니다."
	keywords="데이터 스트림, 데이터 연결, 이벤트 스트림"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>

# 데이터 연결: 이벤트에서 스트림 분석으로의 데이터 스트림 입력에 대해 알아보기

스트림 분석에 데이터를 연결하는 것은 이벤트에서 데이터 원본으로 데이터를 스트리밍하는 것입니다. 이것을 "입력"이라고 부릅니다. Stream Analytics는 분석 작업과 동일한 또는 분석 작업과 다른 Azure 구독의 Azure 데이터 스트림 원본 Event Hub, IoT Hub 및 Blob 저장소와 높은 수준으로 통합됩니다.

## 데이터 입력 유형: 데이터 스트림 및 참조 데이터
데이터가 해당 데이터 원본에 푸시되면 스트림 분석 작업에서 사용되고 실시간으로 처리됩니다. 입력은 데이터 스트림 입력과 참조 데이터 입력의 두 가지 고유한 형식으로 나뉩니다.

### 데이터 스트림 입력
데이터 스트림은 시간이 지남에 따라 생성되는 이벤트의 제한 없는 시퀀스입니다. 스트림 분석 작업은 작업에서 사용되고 변환될 하나 이상의 데이터 스트림 입력을 포함해야 합니다. Blob 저장소, 이벤트 허브 및 IoT 허브는 데이터 스트림 입력 소스로 지원됩니다. 이벤트 허브는 소셜 미디어 활동 피드, 주식 거래 정보 또는 센서의 데이터와 같은 여러 장치 및 서비스에서 이벤트 스트림을 수집하는 데 사용됩니다. IoT 허브는 IoT(사물 인터넷) 시나리오에서 연결된 장치로부터 데이터를 수집하는 데 최적화됩니다. Blob 저장소를 스트림으로 대량 데이터 수집을 위한 입력 소스로 사용할 수 있습니다.

### 참조 데이터
스트림 분석은 참조 데이터라는 두 번째 유형의 입력을 지원합니다. 이 데이터는 정적이거나 시간에 따라 변화가 느리고 일반적으로 상관관계 및 조회를 수행하는 데 사용되는 보조 데이터입니다. Azure Blob 저장소는 현재 유일하게 지원되는 참조 데이터용 입력 소스입니다. 참조 데이터 소스 Blob은 크기가 100MB로 제한됩니다. 참조 데이터 입력을 만드는 방법을 알아보려면 [참조 데이터 사용](stream-analytics-use-reference-data.md)을 참조하세요.

## 이벤트 허브로 데이터 스트림 입력 만들기

[Azure 이벤트 허브](https://azure.microsoft.com/services/event-hubs/)는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수백만 개의 이벤트를 수집할 수 있으므로 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. 스트림 분석에 일반적으로 사용됩니다. 이벤트 허브 및 스트림 분석은 함께 고객에게 실시간 분석을 위한 종단간 솔루션을 제공합니다. 이벤트 허브를 사용하면 이벤트를 실시간으로 Azure에 공급하고 스트림 분석 작업으로는 실시간으로 처리할 수 있습니다. 예를 들어 고객은 웹 클릭, 센서 판독값, 온라인 로그 이벤트를 이벤트 허브에 보내고 스트림 분석 작업을 만들어 실시간 필터링, 집계 및 상관관계를 위해 이벤트 허브를 입력 데이터 스트림으로 사용할 수 있습니다.

스트림 분석의 이벤트 허브에서 오는 이벤트의 기본 타임스탬프가 EventEnqueuedUtcTime인 이벤트 허브에 도착한 이벤트인 타임스탬프인지 확인해야 합니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

### 소비자 그룹

각 스트림 분석 이벤트 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 자체 조인 또는 여러 입력이 포함된 경우 일부 입력이 둘 이상의 판독기 다운스트림에서 읽혀 단일 소비자 그룹의 판독기 수에 영향을 줍니다 파티션당 소비자 그룹마다 5 판독기의 이벤트 허브 한도 초과를 방지하려면 각 스트림 분석 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)를 참조하세요.

### 입력 데이터 스트림으로 이벤트 허브 구성

다음 테이블은 해당 설명과 이벤트 허브 입력 탭의 각 속성을 설명합니다.

| 속성 이름 | 설명 |
|------|------|
| 입력 별칭 | 이 입력을 참조하도록 작업 쿼리에서 사용될 이름 |
| 서비스 버스 네임스페이스 | 서비스 버스 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 서비스 버스 네임스페이스도 만들었습니다. |
| 이벤트 허브 | 이벤트 허브 입력의 이름 |
| 이벤트 허브 정책 이름 | 이벤트 허브 구성 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 서비스 버스 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키 |
| 이벤트 허브 소비자 그룹(선택 사항) | 이벤트 허브에서 데이터를 수집하는 소비자 그룹입니다. 지정되지 않은 경우, 스트림 분석 작업은 기본 소비자 그룹을 사용하여 이벤트 허브에서 데이터를 수집합니다. 각 스트림 분석 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다. |
| 이벤트 직렬화 형식 | 쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다. |
| 인코딩 | 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |

데이터를 이벤트 허브 원본에서 가져온 경우 스트림 분석 쿼리에서 적은 수의 메타데이터 필드에 액세스할 수 있습니다. 다음 테이블은 필드 및 해당 설명을 나열합니다.

| 속성 | 설명 |
|------|------|
| EventProcessedUtcTime | 이벤트가 스트림 분석으로 처리되는 날짜 및 시간입니다. |
| EventEnqueuedUtcTime | 이벤트 허브에서 이벤트를 받은 날짜 및 시간입니다. |
| PartitionId | 입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 다음과 같은 쿼리를 작성할 수 있습니다.

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## IoT 허브 데이터 스트림 입력 만들기

Azure Iot 허브는 IoT 시나리오에 최적화된, 확장성이 뛰어난 게시-구독 이벤트 처리기입니다. 스트림 분석의 IoT 허브에서 오는 이벤트의 기본 타임스탬프가 EventEnqueuedUtcTime인 IoT 허브에 도착한 이벤트인 타임스탬프인지 확인해야 합니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

> [AZURE.NOTE] DeviceClient 속성으로 전송된 메시지만 처리할 수 있습니다.

### 소비자 그룹

각 스트림 분석 IoT 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 자체 조인 또는 여러 입력이 포함된 경우 일부 입력이 둘 이상의 판독기 다운스트림에서 읽혀 단일 소비자 그룹의 판독기 수에 영향을 줍니다 파티션당 소비자 그룹마다 5 판독기의 IoT 허브 한도 초과를 방지하려면 각 스트림 분석 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다.

### IoT 허브를 데이터 스트림 입력으로 구성

다음 테이블은 해당 설명과 IoT 허브 입력 탭의 각 속성을 설명합니다.

| 속성 이름 | 설명 |
|------|------|
| 입력 별칭 | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| IoT 허브 | IoT 허브는 메시징 엔터티 집합에 대한 컨테이너입니다. |
| 끝점 | IoT 허브 끝점의 이름입니다. |
| 공유 액세스 정책 이름 | IoT 허브에 액세스 권한을 부여하기 위한 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 공유 액세스 정책 키 | IoT 허브에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 소비자 그룹(선택 사항) | IoT 허브에서 데이터를 수집하는 소비자 그룹입니다. 지정되지 않은 경우, 스트림 분석 작업은 기본 소비자 그룹을 사용하여 IoT 허브에서 데이터를 수집합니다. 각 스트림 분석 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다. |
| 이벤트 직렬화 형식 | 쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다. |
| 인코딩 | 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |

데이터를 IoT 허브 원본에서 가져온 경우 스트림 분석 쿼리에서 적은 수의 메타데이터 필드에 액세스할 수 있습니다. 다음 테이블은 필드 및 해당 설명을 나열합니다.

| 속성 | 설명 |
|------|------|
| EventProcessedUtcTime | 이벤트가 처리되는 날짜 및 시간입니다. |
| EventEnqueuedUtcTime | IoT 허브에서 이벤트를 받은 날짜 및 시간입니다. |
| PartitionId | 입력 어댑터의 0부터 시작하는 파티션 ID입니다. |
| IoTHub.MessageId | IoT 허브에서 양방향 통신을 상호 연결하는 데 사용합니다. |
| IoTHub.CorrelationId | IoT 허브에서 메시지 응답 및 피드백에 사용됩니다. |
| IoTHub.ConnectionDeviceId | 이 메시지를 보내는 데 사용되는 인증된 ID로, IoT 허브에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| IoTHub.ConnectionDeviceGenerationId | 이 메시지를 보내는 데 사용되는 인증 장치의 generationId로, IoT 허브에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| IoTHub.EnqueuedTime | IoT Hub에서 메시지가 수신된 시간입니다. |
| IoTHub.StreamId | 보낸 사람 장치에 의해 추가된 사용자 지정 이벤트 속성입니다. |

## Blob 저장소 데이터 스트림 입력 만들기

클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Blob 저장소는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. [Blob 저장소](https://azure.microsoft.com/services/storage/blobs/)의 데이터는 일반적으로 데이터를 "있는 그대로" 간주하지만 스트림 분석으로 데이터 스트림으로 처리될 수 있습니다. 스트림 분석을 사용한 Blob 저장소 입력에 대한 일반적인 시나리오 중 하나는 로그 처리이며 여기에서 원격 분석은 의미 있는 데이터를 추출하려면 시스템에서 캡처되고 구문 분석되고 처리되어야 합니다.

스트림 분석에서 Blob 저장소 이벤트의 기본 타임 스탬프는 Blob이 마지막으로 수정된 *isBlobLastModifiedUtcTime* 라는 타임스탬프인 점에 유의하세요. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

또한 CSV 형식의 입력은 데이터 집합에 대한 필드를 정의하는 헤더 행이 **필요**합니다. 추가 헤더 행 필드는 모두 **고유**해야 합니다.

> [AZURE.NOTE] 스트림 분석에서는 기존 blob에 콘텐츠를 추가할 수 없습니다. 스트림 분석에서는 blob을 한 번만 확인하며, 이후에 수행된 모든 변경 작업은 처리되지 않습니다. 따라서 모든 데이터를 한 번에 업로드하고 blob 저장소에 추가 이벤트를 추가하지 않는 것이 좋습니다.

다음 테이블은 해당 설명과 Blob 저장소의 각 속성을 설명합니다.

<table>
<tbody>
<tr>
<td>속성 이름</td>
<td>설명</td>
</tr>
<tr>
<td>입력 별칭</td>
<td>이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.</td>
</tr>
<tr>
<td>저장소 계정</td>
<td>Blob 파일이 위치한 저장소 계정의 이름입니다.</td>
</tr>
<tr>
<td>저장소 계정 키</td>
<td>저장소 계정과 연결된 비밀 키입니다.</td>
</tr>
<tr>
<td>저장소 컨테이너
</td>
<td>컨테이너는 Microsoft Azure Blob 서비스에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.</td>
</tr>
<tr>
<td>경로 접두사 패턴 [선택 사항]</td>
<td>지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로. 경로 내에서 다음 3개 변수의 인스턴스 중 하나 이상을 지정하도록 선택할 수도 있습니다.<BR>{date}, {time},<BR>{partition}<BR>예제 1: cluster1/logs/{date}/{time}/{partition}<BR>예제 2: cluster1/logs/{date}<P>"*"는 pathprefix에 허용되는 값이 아닙니다. 유효한 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob 문자</a>만 허용됩니다.</td>
</tr>
<tr>
<td>날짜 형식[선택 사항]</td>
<td>접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD</td>
</tr>
<tr>
<td>시간 형식[선택 사항]</td>
<td>접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다.</td>
</tr>
<tr>
<td>이벤트 직렬화 형식</td>
<td>쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식(JSON, CSV 또는 Avro)을 알고 있어야 합니다.</td>
</tr>
<tr>
<td>인코딩</td>
<td>CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다.</td>
</tr>
<tr>
<td>구분 기호</td>
<td>스트림 분석은 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.</td>
</tr>
</tbody>
</table>

데이터를 Blob 저장소 원본에서 가져온 경우 스트림 분석 쿼리에서 적은 수의 메타데이터 필드에 액세스할 수 있습니다. 다음 테이블은 필드 및 해당 설명을 나열합니다.

| 속성 | 설명 |
|------|------|
| BlobName | 이 이벤트가 발생한 입력 Blob의 이름입니다. |
| EventProcessedUtcTime | 이벤트가 스트림 분석으로 처리되는 날짜 및 시간입니다. |
| BlobLastModifiedUtcTime | Blob이 마지막으로 수정된 날짜 및 시간입니다. |
| PartitionId | 입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 다음과 같은 쿼리를 작성할 수 있습니다.

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계
스트림 분석 작업을 위한 Azure의 데이터 연결 옵션에 대해 알아보았습니다. 스트림 분석에 대한 자세한 내용은 다음 항목을 참조하세요.

- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0914_2016-->