<properties
	pageTitle="일반적인 스트림 분석 사용 패턴에 대한 쿼리 예제 | Microsoft Azure"
	description="일반적인 Azure 스트림 분석 쿼리 패턴 "
	keywords="쿼리 예제"
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
	ms.workload="big-data"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>


# 일반적인 스트림 분석 사용 패턴에 대한 쿼리 예제

## 소개

Azure Stream 분석의 쿼리는 [스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx) 가이드에 문서화된 SQL 유사 언어로 표현됩니다. 이 문서에서는 실제 시나리오에 따른 몇 가지 일반적인 쿼리 패턴에 대한 솔루션을 간략하게 설명합니다. 이 작업은 진행 중이며 새 패턴이 지속적으로 업데이트됩니다.

## 쿼리 예제: 데이터 형식 변환
**설명**: 입력 스트림의 속성 형식을 정의합니다. 예를 들어 자동차 무게가 입력 스트림에 문자열 형식으로 설정되면 합계를 수행하기 위해 INT로 변환해야 합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**출력**:

| 계정을 | 무게 |
| --- | --- |
| Honda | 3000 |

**솔루션**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**설명**: 무게 필드의 CAST 문을 사용하여 해당 형식을 지정합니다(지원되는 데이터 형식의 목록을 보려면[여기](https://msdn.microsoft.com/library/azure/dn835065.aspx)를 참조).


## 쿼리 예제: 패턴을 일치시키기 위해 Like/Not like를 사용합니다.
**설명**: 특정 패턴과 일치시키기 위해 이벤트의 필드 값을 확인합니다. 예를 들어 A로 시작하고 9로 끝나는 번호판을 반환합니다.

**입력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**출력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**솔루션**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**설명**: LIKE 문을 사용하여 LicensePlate 필드 값이 A로 시작하고 0개 이상 문자의 모든 문자열이 있는지와 9로 끝나는지 확인합니다.

## 쿼리 예제: 다른 사례/값(CASE 문)에 대한 논리를 지정합니다.
**설명**: 일부 조건에 따른 필드에 대한 다른 계산을 제공합니다. 예를 들어, 1이라는 특수 사례를 가진 동일한 브랜드의 차가 몇 대 통과했는지에 대한 문자열 설명을 제공합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**솔루션**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**설명**: CASE 절을 사용하면 일부 조건에 따른 다른 계산을 제공할 수 있습니다(이 경우에는 집계 창에 있는 자동차의 수).

## 쿼리 예제: 데이터를 여러 출력으로 보내기
**설명**: 데이터를 단일 작업에서 여러 출력 대상으로 보냅니다. 예를 들어 임계점 기반 경고에 대한 데이터를 분석하고 blob 저장소에 모든 이벤트를 보관합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력1**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력2**:

| 계정을 | Time | 개수 |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**해결 방법**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**설명**: INTO 절은 이 문의 데이터를 어떤 출력에 쓸 것인지에 대한 스트림 분석을 알려줍니다. 첫 번째 쿼리는 ArchiveOutput이라고 지정한 출력에 받은 통과한 데이터입니다. 두 번째 쿼리는 일부 간단한 집계와 필터링을 하고 다운스트림 경고 시스템에 결과를 보냅니다. *참고*: 여려 출력문에서 CTE(즉, WITH 문)의 결과를 재사용할 수 있습니다. 입력 원본의 판독기보다 더 적게 열 수 있는 추가적 이점을 가집니다.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## 쿼리 예제: 고유 값 계산
**설명**: 시간 창 안의 스트림에 나타나는 고유 필드 값 개수를 계산합니다. 예를 들어, 2초 창 안에 톨게이트를 통과한 고유한 브랜드의 차는 몇 대입니까?

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력:**

| 개수 | Time |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**해결 방법:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**설명:** 창 위에 개수를 포함한 고유한 값을 얻기 위해 초기 집계를 합니다. 그리고 얻은 결과에 대한 집계를 합니다. 창에서 주어진 모든 고유 값이 동일한 타임스탬프를 얻은 경우 두 번째 집계 창은 첫 번째 단계의 2개의 창을 집계하지 않기 위해 최소값이 되어야 합니다.

## 쿼리 예제: 값이 변경되었는지 여부를 확인합니다.#
**설명**: 현재 값과 다른지 확인하기 위해 이전 값을 살펴봅니다. 예를 들어, 유료 도로에 있는 이전 차의 결과와 현재 차의 결과가 같은 브랜드인지 살펴봅니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**출력**:

| 계정을 | Time |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**해결 방법**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**설명**: LAG을 사용하여 지난 이벤트의 입력 스트림을 살펴보고, Make 값을 얻습니다. 그런 다음 현재 이벤트와 출력 이벤트의 Make를 비교합니다.

## 쿼리 예제: 창에서 첫 번째 이벤트 찾기
**설명**: 매 10분 간격으로 첫 번째 자동차를 찾습니다.

**입력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**출력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**해결 방법**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

이제 10분 간격마다 특정 브랜드의 첫 번째 차를 찾도록 문제를 변경해보겠습니다.

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**해결 방법**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## 쿼리 예제: 창에서 마지막 이벤트 찾기
**설명**: 매 10분 간격마다 마지막 자동차를 찾습니다.

**입력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**출력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**해결 방법**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**설명**: 쿼리에는 두 단계가 있습니다. - 첫 번째는 10분 창에서 최신 타임스탬프를 찾는 것입니다. 두 번째 단계는 각 창의 최신 타임스탬프와 일치하는 이벤트를 찾기 위해 원본 스트림을 사용한 첫 번째 쿼리의 결과를 조인합니다.

## 쿼리 예제: 이벤트의 부재를 검색합니다.
**설명**: 스트림이 특정 조건에 일치하지 않는 값이 있는지 확인합니다. 예를 들어, 동일한 브랜드의 연속된 2대의 차가 90초 이내에 유료 도로로 진입했는지 확인합니다.

**입력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**출력**:

| 계정을 | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**해결 방법**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**설명**: LAG을 사용하여 지난 이벤트의 입력 스트림을 살펴보고, Make 값을 얻습니다. 그런 다음 현재 이벤트와 출력 이벤트가 같은지 Make를 비교하고, LAG을 사용하여 이전 자동차에 대한 데이터를 얻습니다.

## 쿼리 예제: 이벤트 간 기간 검색
**설명**: 제공된 이벤트의 기간을 찾습니다. 예를 들어 웹 클릭스트림을 지정하여 기능에서 사용한 시간을 결정합니다.

**입력**:
  
| 사용자 | 기능 | 이벤트 | Time |
| --- | --- | --- | --- |
| user@location.com | RightMenu | 시작 | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | 끝 | 2015-01-01T00:00:08.0000000Z |
  
**출력**:
  
| 사용자 | 기능 | 기간 |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**솔루션**

````
    SELECT
    	[user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
    	Event = 'end'
````

**설명**: LAST 함수를 사용하여 이벤트 유형이 'Start'였을 때 마지막 시간 값을 검색합니다. LAST 함수는 PARTITION BY [사용자]를 사용하여 고유한 사용자마다 결과가 계산된다는 것을 나타냅니다. 쿼리에 'Start'와 'Stop' 이벤트 간 시간 차이를 위해 최대 1시간의 임계값이 있지만 필요에 따라 구성 가능합니다(LIMIT DURATION(hour, 1).

## 쿼리 예제: 조건의 기간 감지
**설명**: 조건의 시간이 얼마인지 알아봅니다. 예를 들어 버그가 생겨서 모든 자동차의 중량이 정확하지 않은 경우(20.000파운드 이상) 버그의 기간을 계산하고자 합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**출력**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**솔루션**:

````
	WITH SelectPreviousEvent AS
	(
	SELECT
	*,
		LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
		LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
	FROM input TIMESTAMP BY [time]
	)

	SELECT 
    	LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
    	previousTime [EndFault]
	FROM SelectPreviousEvent
	WHERE
    	[weight] < 20000
	    AND previousWeight > 20000
````

**설명**: LAG를 사용하여 24시간 동안 입력 스트림을 보고 StartFault와 StopFault가 가중치 < 20000인 인스턴스를 찾습니다.

## 쿼리 예제: 누락된 값 입력
**설명**: 누락된 값이 있는 이벤트 스트림의 경우 정기적으로 이벤트 스트림을 생성합니다. 예를 들어, 가장 최근 표시된 데이터 지점을 보고하도록 5초마다 이벤트를 생성합니다.

**입력**:

| t | value |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**출력(처음 10개 행)**:

| windowend | lastevent.t | lastevent.value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**솔루션**:

    SELECT
    	System.Timestamp AS windowEnd,
    	TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
    	input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**설명**:이 쿼리는 5초마다 이벤트를 생성하고 이전에 수신했던 마지막 이벤트를 출력합니다. [도약 창](https://msdn.microsoft.com/library/dn835041.aspx "도약 창 - Azure 스트림 분석") 기간은 쿼리가 마지막 이벤트를 찾는 데 얼마나 돌아가야 하는지를 결정합니다(이 예에서는 300초).


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0921_2016-->