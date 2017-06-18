<properties
	pageTitle="처리량을 높이기 위한 스트림 분석 작업 규모 지정 | Microsoft Azure"
	description="입력 파티션을 구성하고, 쿼리 정의를 조정하고, 작업 스트리밍 단위를 설정하여 스트림 분석 작업의 크기를 조정하는 방법을 알아봅니다."
	keywords="데이터 스트리밍, 스트리밍 데이터 처리, 분석 조정"
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

# Azure 스트림 분석 작업 크기를 조정하여 스트림 데이터 처리량 증가

분석 작업을 조정하고 스트림 분석의 *스트리밍 단위*를 계산하는 방법 및 입력 파티션을 구성하고 분석 쿼리 정의를 조정하고 작업 스트리밍 단위를 설정하여 스트림 분석 작업의 크기를 조정하는 방법을 알아봅니다.

## 스트림 분석 작업은 무엇으로 구성되나요?
스트림 분석 작업 정의에는 입력, 쿼리 및 출력이 포함됩니다. 입력은 작업이 데이터 스트림을 읽는 위치이고, 쿼리는 데이터 입력 스트림을 변환하는 데 사용되며, 출력은 작업이 작업 결과를 전송하는 위치입니다.

작업에는 데이터 스트림에 대해 하나 이상의 입력 소스가 필요합니다. 데이터 스트림 입력 소스는 Azure 서비스 버스 이벤트 허브 또는 Azure Blob 저장소에 저장될 수 있습니다. 자세한 내용은 [Azure 스트림 분석 소개](stream-analytics-introduction.md) 및 [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)을 참조하세요.

## 스트리밍 단위 구성
SU(스트리밍 단위)는 Azure 스트림 분석 작업을 실행하는 리소스 및 능력을 나타냅니다. SU는 CPU, 메모리의 혼합된 측정치 및 읽기/쓰기 속도를 기반으로 상대적 이벤트 처리 용량을 설명하는 방법을 제공합니다. 각 스트리밍 단위는 대략 1MB/초의 처리량에 해당합니다.

특정 작업에 필요한 SU 수 선택은 입력에 대한 파티션 구성 및 작업에 정의된 쿼리에 따라 달라집니다. Azure 클래식 포털을 사용하여 작업에 대한 스트리밍 단위의 최대 할당량을 선택할 수 있습니다. 기본적으로 각 Azure 구독에는 특정 지역의 모든 분석 작업에 대해 최대 50개의 스트리밍 단위 할당량이 있습니다. 구독의 스트리밍 단위를 늘리려면 [Microsoft 지원](http://support.microsoft.com)에 문의하세요.

작업이 활용할 수 있는 스트리밍 단위 수는 입력에 대한 파티션 구성과 작업에 대해 정의된 쿼리에 따라 다릅니다. 또한 스트림 단위에는 유효한 값이 사용되어야 합니다. 유효한 값은 아래와 같이 1, 3, 6 다음으로 6의 증분 이상에서 시작합니다.

![Azure 스트림 분석 스트림 단위 규모 지정][img.stream.analytics.streaming.units.scale]

이 문서에서는 쿼리를 계산하고 조정하여 분석 작업에 대한 처리량을 늘리는 방법을 보여 줍니다.

## 병렬 처리가 적합한 작업
병렬 처리가 적합한 작업은 Azure 스트림 분석에서 가장 확장성이 뛰어난 시나리오입니다. 하나의 쿼리 인스턴스에 대한 하나의 입력 파티션을 하나의 출력 파티션에 연결합니다. 이 병렬 처리를 위해서는 몇 가지가 필요합니다.

1.  쿼리 논리가 동일한 쿼리 인스턴스에서 처리되는 동일한 키에 따라 다른 경우 이벤트가 동일한 입력 파티션으로 이동하는지 확인해야 합니다. 이벤트 허브의 경우 이는 이벤트 데이터에서 **PartitionKey**를 설정해야 하고 그렇지 않으면 분할된 발신자를 사용할 수 있다는 것입니다. Blob의 경우 이는 이벤트가 같은 파티션 폴더에 전송된다는 것입니다. 쿼리 논리가 동일한 쿼리 인스턴스에서 동일한 키를 처리할 필요가 없는 경우 이 요구 사항을 무시할 수 있습니다. 이에 대한 예로 간단한 선택/프로젝트/필터 쿼리를 참조하세요.
2.	데이터가 입력 측에 있어야 할 때처럼 배치되면 쿼리가 분할되었는지 확인해야 합니다. 그러려면 모든 단계에서 **Partition By**를 사용해야 합니다. 여러 단계가 허용되지만 모두 동일한 키로 분할되어야 합니다. 또 한 가지 유의할 점은 현재 분할 키가 완전한 병렬 작업이 되도록 **PartitionId**로 설정되어야 한다는 것입니다.
3.	현재는 이벤트 허브 및 Blob만 분할된 출력을 지원합니다. 이벤트 허브 출력의 경우 **PartitionKey** 필드가 **PartitionId**가 되도록 구성해야 합니다. Blob의 경우 필요한 작업이 없습니다.
4.	입력 파티션 수가 출력 파티션 수와 같아야 한다는 점도 유의해야 합니다. Blob 출력은 현재 파티션을 지원하지 않지만 업스트림 쿼리의 파티션 구성표를 상속하기 때문에 문제가 없습니다. 완전한 병렬 작업을 허용하는 파티션 값의 예입니다.
	1.	8개의 이벤트 허브 입력 파티션 및 8개의 이벤트 허브 출력 파티션
	2.	8개의 이벤트 허브 입력 파티션 및 Blob 출력
	3.	8개의 Blob 입력 파티션 및 Blob 출력
	4.	8개의 Blob 입력 파티션과 8개의 이벤트 허브 출력 파티션

다음은 병렬 처리가 적합한 작업의 몇 가지 예제 시나리오입니다.

### 단순 쿼리
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - 8개의 파티션이 있는 이벤트 허브

**쿼리:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

이 쿼리는 간단한 필터이므로 이벤트 허브에 보내는 입력의 분할을 염려하지 않아도 됩니다. 쿼리에 **PartitionId**의 **Partition By**가 있으므로 위의 요구 사항 #2가 충족됩니다. 출력의 경우 **PartitionKey** 필드가 **PartitionId**로 설정되도록 작업에서 이벤트 허브 출력을 구성해야 합니다. 마지막으로 입력 파티션이 출력 파티션과 같은지 확인합니다. 이 토폴로지는 병렬 처리가 적합합니다.

### 그룹화 키가 있는 쿼리
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - Blob

**쿼리:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

이 쿼리는 그룹화 키가 있으므로 동일한 키가 동일한 쿼리 인스턴스에서 처리되어야 합니다. 즉, 이벤트를 분할된 방식으로 이벤트 허브에 보내야 합니다. 어떤 키를 신경 써야 하나요? **PartitionId**는 작업 논리 개념이므로 실제로 신경 써야 하는 키는 **TollBoothId**입니다. 즉, 이벤트 허브에 보내는 이벤트 데이터의 **PartitionKey**가 이벤트의 **TollBoothId**가 되도록 설정해야 합니다. 쿼리에 **PartitionId**의 **Partition By**가 있으므로 이 부분에는 문제가 없습니다. 출력의 경우 BLOB이므로 **PartitionKey** 구성을 염려하지 않아도 됩니다. 요구 사항 #4의 경우도 Blob이므로 염려할 필요가 없습니다. 이 토폴로지는 병렬 처리가 적합합니다.

### 그룹화 키가 있는 다중 단계 쿼리 ###
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - 8개의 파티션이 있는 이벤트 허브

**쿼리:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

이 쿼리는 그룹화 키가 있으므로 동일한 키가 동일한 쿼리 인스턴스에서 처리되어야 합니다. 이전 쿼리와 동일한 전략을 사용할 수 있습니다. 이 쿼리에는 여러 단계가 있습니다. 각 단계에 **PartitionId**의 **Partition By**가 있나요? 예, 그러면 좋습니다. 출력의 경우 위에서 설명한 것처럼 **PartitionKey**를 **PartitionId**로 설정해야 하고 입력과 동일한 수의 파티션이 있는 것을 확인할 수도 있습니다. 이 토폴로지는 병렬 처리가 적합합니다.


## 병렬 처리가 적합하지 않은 예제 시나리오

### 일치하지 않는 파티션 수 ###
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - 32개의 파티션이 있는 이벤트 허브

이 경우 입력 파티션 수가 출력 파티션 수와 같지 않기 때문에 쿼리가 무엇인지 중요하지 않습니다.

### 이벤트 허브 또는 Blob을 출력으로 사용하지 않음
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - PowerBI

PowerBI 출력은 현재 분할을 지원하지 않습니다.

### 서로 다른 Partition By 값이 있는 다중 단계 쿼리
입력 - 8개의 파티션이 있는 이벤트 허브 출력 - 8개의 파티션이 있는 이벤트 허브

**쿼리:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
보이는 것처럼 두 번째 단계에서는 **TollBoothId**를 분할 키로 사용합니다. 이는 첫 번째 단계와 동일하지 않으므로 순서를 섞어야 합니다.

여기에 병렬 처리가 적합한 토폴로지와 함께 최대 배율에 대한 잠재력을 수행할 수 있는 스트림 분석 작업의 몇 가지 예제 및 반대 예제가 있습니다. 이러한 프로필 중 하나에 맞지 않는 작업의 경우 일부 다른 정식 스트림 분석 시나리오의 크기를 최대한 조정하는 방법에 대한 자세한 설명을 이후에 업데이트할 예정입니다.

지금은 아래 일반적인 참고 자료를 사용하세요.

## 작업의 최대 스트리밍 단위 계산
스트림 분석 작업에 사용될 수 있는 스트리밍 단위의 총 수는 작업에 대해 정의된 쿼리의 단계 수와 각 단계에 대한 파티션 수에 따라 결정됩니다.

### 쿼리의 단계
하나의 쿼리에는 하나 이상의 단계가 있을 수 있습니다. 각 단계는 **WITH** 키워드를 사용하여 정의하는 하위 쿼리입니다. **WITH** 키워드 밖에 있는 유일한 쿼리 단계도 한 단계로 계산됩니다. 예를 들어 다음 쿼리에서 **SELECT** 문입니다.

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

앞의 쿼리에는 2개의 단계가 있습니다.

> [AZURE.NOTE] 이 샘플 쿼리는 이 문서 뒷부분에 설명되어 있습니다.

### 단계 분할

단계를 분할하려면 다음 조건이 필요합니다.

- 입력 소스는 분할해야 합니다. 자세한 내용은 [이벤트 허브 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)를 참조하세요.
- 쿼리의 **SELECT** 문은 분할된 입력 원본에서 읽어와야 합니다.
- 단계 내의 쿼리에는 **Partition By** 키워드가 있어야 합니다.

쿼리를 분할되면 입력 이벤트가 처리되고 별도의 파티션 그룹에 집계되며 각 그룹에 대해 출력 이벤트가 생성됩니다. 결합된 집계가 필요한 경우 집계할 또 다른 분할되지 않은 단계를 만들어야 합니다.

### 작업의 최대 스트리밍 단위 계산

하나의 스트림 분석 작업에 대해 분할되지 않은 모든 단계를 최대 6개의 스트리밍 단위로 확장할 수 있습니다. 스트리밍 단위를 더 추가하려면 단계를 분할해야 합니다. 각 파티션에는 6개의 스트리밍 단위가 있을 수 있습니다.

<table border="1">
<tr><th>작업의 쿼리</th><th>작업의 최대 스트리밍 단위</th></td>

<tr><td>
<ul>
<li>쿼리는 한 단계를 포함합니다.</li>
<li>이 단계는 분할되지 않습니다.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>입력 데이터 스트림은 3으로 분할됩니다.</li>
<li>쿼리는 한 단계를 포함합니다.</li>
<li>이 단계는 분할됩니다.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>쿼리는 두 단계를 포함합니다.</li>
<li>두 단계모두 분할되지 않습니다.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>데이터 스트림 입력은 3으로 분할됩니다.</li>
<li>쿼리는 두 단계를 포함합니다. 입력 단계는 분할되며 두 번째 단계는 분할되지 않습니다.</li>
<li>SELECT 문은 분할된 입력에서 읽어옵니다.</li>
</ul>
</td>
<td>24(분할된 단계에 대한 18+분할되지 않은 단계에 대한 6)</td></tr>
</table>

### 크기 조정 예제
다음 쿼리는 3분의 기간 내에 세 곳의 요금 징수소가 있는 요금 스테이션을 통과하는 자동차 수를 계산합니다. 이 쿼리는 6개 스트리밍 단위까지 확장될 수 있습니다.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

쿼리에 대한 더 많은 스트리밍 단위를 사용하려면 데이터 스트림 입력 및 쿼리를 모두 분할해야 합니다. 데이터 스트림 파티션이 3으로 설정되어 있다고 가정할 경우, 다음의 수정된 쿼리를 최대 18개 스트리밍 단위로 확장할 수 있습니다.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

쿼리가 분할되면 입력 이벤트가 처리되고 별도의 파티션 그룹에 집계되며 각 그룹에 대해 출력 이벤트가 생성됩니다. 출력 이벤트도 각 그룹에 대해 생성됩니다. **Group-by** 필드가 데이터 입력 스트림의 파티션 키가 아닌 상태에서 분할을 수행하면 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 이전 샘플 쿼리의 **TollBoothId** 필드는 Input1의 파티션 키가 아닙니다. TollBooth #1의 데이터는 여러 파티션으로 분산될 수 있습니다.

각 Input1 파티션은 스트림 분석 기능에서 개별적으로 처리되며, 동일한 연속 기간 동안 동일한 요금 징수소를 통과하는 자동차 수에 대해 여러 개의 레코드가 생성됩니다. 입력 파티션 키를 변경할 수 없는 경우 비분할 단계를 추가하여 이 문제를 해결할 수 있습니다. 예:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

이 쿼리는 24개 스트리밍 단위까지 확장될 수 있습니다.

>[AZURE.NOTE] 두 스트림을 조인하는 경우 스트림이 조인을 수행하는 열의 파티션 키별로 분할되고 있으며 두 스트림에 동일한 수의 파티션이 있는지 확인해야 합니다.


## 스트림 분석 작업 파티션 구성

**작업에 대한 스트리밍 단위를 조정하려면**

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 창에서 **스트림 분석**을 클릭합니다.
3. 크기를 조정할 스트림 분석 작업을 클릭합니다.
4. 페이지 위쪽에서 **규모 지정**을 클릭합니다.

![Azure 스트림 분석 스트림 단위 규모 지정][img.stream.analytics.streaming.units.scale]

Azure 포털의 설정에서 크기 조정을 설정할 수 있습니다.

![Azure 포털 스트림 분석 작업 구성][img.stream.analytics.preview.portal.settings.scale]

## 작업 성능 모니터링

관리 포털을 사용하여 작업의 처리량(이벤트 수/초)을 추적할 수 있습니다.

![Azure 스트림 분석 모니터링 작업][img.stream.analytics.monitor.job]

작업의 예상 처리량(이벤트 수/초)을 계산합니다. 처리량이 예상보다 더 작은 경우 입력 파티션을 조정하고, 쿼리를 조정하고, 작업에 스트리밍 단위를 더 추가합니다.

## 규모별 스트림 분석 처리량 - Raspberry Pi 시나리오


여러 스트리밍 단위에 걸친 처리량 측면에서 일반적인 시나리오로 스트림 분석 작업 규모 지정 방식을 이해하기 위해 다음은 센서 데이터(클라이언트)를 이벤트 허브에 보내는 실험입니다. 이를 처리하고 경고 또는 통계를 출력으로 다른 이벤트 허브에 보냅니다.

클라이언트는 JSON 형식으로 이벤트 허브에 합성된 센서 데이터를 보내므로 스트림 분석 및 데이터 출력도 JSON 형식입니다. 다음은 샘플 데이터의 예입니다.

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

쿼리: “불이 꺼졌을 때 경고를 보냄”

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

처리량 측정: 이 컨텍스트에서 처리량은 고정된 시간량(10분)에 스트림 분석이 처리한 입력 데이터의 양입니다. 입력 데이터에 대해 최상의 처리량을 달성하려면 데이터 스트림 입력 및 쿼리 모두 분할되어야 합니다. 또한 **COUNT()**는 처리된 입력 이벤트 수를 측정하는 쿼리에 포함됩니다. 작업이 발생할 입력 이벤트를 단순히 기다리고 있지 않도록 입력 이벤트 허브의 각 파티션이 충분한 입력 데이터(약 300MB)로 미리 로드되었습니다.

아래는 스트리밍 단위 수 및 이벤트 허브의 해당 파티션 수가 증가하는 결과입니다.

<table border="1">
<tr><th>입력 파티션</th><th>출력 파티션</th><th>스트리밍 단위</th><th>지속적인 처리량
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06MB/초</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06MB/초</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32MB/초</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67MB/초</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27MB/초</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69MB/초</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## 도움말 보기
추가 지원이 필요한 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.


## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0921_2016-->