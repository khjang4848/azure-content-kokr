<properties
	pageTitle="단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침"
	description="이 항목은 응용 프로그램에 적합한 서비스 계층을 결정하는 데 도움이 되는 지침과 Azure SQL 데이터베이스를 최대한 활용할 수 있도록 응용 프로그램을 튜닝하는 방법에 대한 권장 사항을 제공합니다."
	services="sql-database"
	documentationCenter="na"
	authors="CarlRabeler"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="09/13/2016"
	ms.author="carlrab" />

# 단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침

## 개요

Microsoft Azure SQL 데이터베이스에는 세 가지 [서비스 계층](sql-database-service-tiers.md), 즉, Basic, Standard, Premium이 있습니다. 세 서비스 모두 Azure SQL 데이터베이스에 제공된 리소스를 엄격하게 격리하여 예측 가능한 성능을 보장합니다. 성능은 DTU에서 측정됩니다. DTU에 대한 자세한 내용은 [DTU란](sql-database-what-is-a-dtu.md)을 참조하세요. 이 문서는 응용 프로그램에 대한 서비스 계층을 선택하는 데 도움이 되는 지침과 Azure SQL Database를 최대한 활용할 수 있도록 응용 프로그램을 튜닝하는 방법에 대한 권장 사항을 제공합니다.

>[AZURE.NOTE] 이 문서는 SQL 데이터베이스의 단일 데이터베이스에 대한 성능 지침을 중신으로 살펴봅니다. 탄력적 풀과 관련된 성능 지침을 보려면 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요. 하지만 이 문서에서 단일 데이터베이스에 대해 제안하는 많은 튜닝 권장 사항은 유사한 성능 이점을 제공하는 탄력적 풀 내의 데이터베이스에 적용할 수 있습니다.

- **Basic** Basic 서비스 계층은 각 데이터베이스에 시간 단위의 적절한 성능 예측 가능성을 제공하도록 설계되었습니다. Basic 데이터베이스의 DTU는 성능에 대해 여러 번 동시 요청을 하지 않고도 소규모 데이터베이스에 충분한 리소스를 제공하도록 설계되었습니다.
- **Standard** Standard 서비스 계층은 향상된 성능 예측 가능성을 제공하며 작업 그룹 및 웹 응용 프로그램과 같은 여러 개의 동시 요청으로 데이터베이스에 대한 기대 수준을 높여줍니다. Standard 서비스 계층 데이터베이스에서는 분 단위의 예측 가능한 성능을 기준으로 데이터베이스 응용 프로그램의 규모를 조정할 수 있습니다.
- **Premium** Premium 서비스 계층은 각 Premium 데이터베이스에 대한 예측 가능한 성능을 초 단위로 제공합니다. Premium 서비스 계층을 사용하면 데이터베이스의 최고 부하를 기준으로 데이터베이스 응용 프로그램의 규모를 조정하고 대기 시간이 중요한 작업에서 성능 차이로 인해 작은 쿼리가 예상보다 길어지지 않습니다. 이 모델은 최고 리소스 요구 사항, 성능 차이 또는 쿼리 대기 시간이 중요한 응용 프로그램에 필요한 개발 및 제품 유효성 검사 주기를 크게 간소화할 수 있습니다.

각 서비스 계층의 성능 수준 설정으로 필요한 용량에 대해서만 지불하고 워크로드의 변화에 따라 용량을 [늘리거나 줄일](sql-database-scale-up.md) 수 있습니다. 예를 들어, 개학 전 쇼핑 시즌에 데이터베이스 워크로드가 많아질 경우 이 기간 동안에만 데이터베이스의 성능 수준을 높이고 이 피크 기간이 끝난 다음 줄일 수 있습니다. 비즈니스의 계절성에 따라 클라우드 환경을 최적화하여 지불하는 비용을 최소화할 수 있습니다. 이 모델은 소프트웨어 개발 출시 주기에도 적합합니다. 테스트 팀은 테스트 실행 중 용량을 할당하고 테스트가 완료되면 용량을 해제할 수 있습니다. 이와 같은 용량 요청 방식은 필요할 때마다 용량에 지불하는 모델에 적합하며 거의 사용하지 않는 전용 리소스에 대한 비용 지출을 방지합니다.

## 서비스 계층을 사용하는 이유

각 워크로드는 다를 수 있지만 서비스 계층의 목적은 다양한 성능 수준에서 성능 예측 가능성을 제공하는 것입니다. 데이터베이스에 대한 리소스 요구 사항이 큰 고객은 전용 컴퓨팅 환경에서 작업할 수 있습니다.

### Basic 서비스 계층 사용 사례:

- **Azure SQL 데이터베이스 시작**: 개발 중인 응용 프로그램은 높은 성능 수준이 필요하지 않은 경우가 대부분입니다. Basic 데이터베이스는 낮은 가격대로 데이터베이스 개발의 이상적 환경을 제공합니다.
- **사용자가 한 명인 데이터베이스**: 데이터베이스의 사용자가 한 명인 응용 프로그램은 일반적으로 동시성 및 성능에 대한 요구 사항이 높지 않습니다. 이러한 요구 사항을 가진 응용 프로그램은 Basic 서비스 계층이 적합합니다.

### Standard 서비스 계층 사용 사례:

- **동시 요청이 여러 개인 데이터베이스**: 한 번에 둘 이상의 사용자에게 서비스를 제공하는 응용 프로그램입니다. 예를 들어 더 많은 리소스를 요청하는 트래픽 양이 중간 정도인 웹 사이트 또는 부서 응용 프로그램은 Standard 서비스 계층에 적합합니다.

### Premium 서비스 계층 사용 사례:

- **높은 최고 부하**: 작업을 완료하는 데 많은 CPU, 메모리, IO가 필요한 응용 프로그램. 예를 들어 데이터베이스 작업이 장시간 많은 CPU 코어를 사용해야 하는 것으로 확인된 경우 Premium 서비스 계층 사용이 적합합니다.
- **동시 요청이 많은 경우**: 일부 데이터베이스 응용 프로그램은 트래픽 양이 많은 웹 사이트와 같이 많은 동시 요청을 지원합니다. Basic 및 Standard 서비스 계층은 동시 요청 수가 제한적입니다. 추가 연결이 필요한 응용 프로그램은 적절한 예약 크기를 선택하여 필요한 요청의 최대 수를 처리해야 합니다.
- **낮은 대기 시간**: 일부 응용 프로그램은 데이터베이스에서 최소 시간의 응답을 보장해야 합니다. 광범위한 고객 작업의 일부로 저장된 특정 프로시저가 호출될 경우 99%, 20밀리초 이내에 해당 호출에서 반환해야 하는 요구 사항이 있을 수 있습니다. 이러한 종류의 응용 프로그램은 Premium 서비스 계층을 활용하여 컴퓨팅 성능의 가용성을 보장할 수 있습니다.

필요한 정확한 수준은 각 리소스 규격의 최고 부하 요구 사항에 따라 다릅니다. 일부 응용 프로그램은 한 가지 리소스를 매우 적게 사용하는 반면 다른 리소스의 요구 사항은 높을 수 있습니다.

## 대금 청구 및 가격 정보

탄력적 풀은 다음 특성에 따라 대금이 청구됩니다.

- 탄력적 풀은 풀에 데이터베이스가 없더라도 생성 시부터 대금이 청구됩니다.
- 탄력적 풀은 시간당 대금이 청구됩니다. 이 계량 빈도는 단일 데이터베이스의 성능 수준과 같은 계량 빈도입니다.
- 탄력적 풀이 새로운 크기의 eDTU로 조정된 경우, 크기 조정 작업이 완료될 때까지 새 eDTU 크기에 따른 대금이 청구되지 않습니다. 이 청구 패턴은 독립 실행형 데이터베이스의 성능 수준을 변경하는 것과 동일한 패턴을 따릅니다.


- 탄력적 풀의 가격은 풀의 eDTU 수를 기준으로 책정됩니다. 탄력적 풀의 가격은 그 안의 탄력적 데이터베이스 사용률과 무관합니다.
- 가격은 (풀 eDTU 수)x(eDTU 당 단위 가격)으로 계산됩니다.

탄력적 풀의 단위 eDTU 가격은 동일한 서비스 계층에 있는 독립 실행형 데이터베이스의 단위 DTU 가격보다 높습니다. 자세한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

## 서비스 계층 기능 및 제한
각 서비스 계층 및 성능 수준은 서로 다른 제한 및 성능 특징과 연결됩니다. 다음 표에서는 단일 데이터베이스에 대한 이러한 특징을 설명합니다.

[AZURE.INCLUDE [SQL DB 서비스 계층 테이블](../../includes/sql-database-service-tiers-table.md)]

다음 섹션에서는 이러한 제한과 관련된 사용량 보기에 대한 자세한 정보를 제공합니다.

### 최대 메모리 내 OLTP 저장소

**sys.dm\_db\_resource\_stats** 뷰를 사용하여 메모리 내 저장소 사용을 모니터링할 수 있습니다. 모니터링에 대한 자세한 내용은 [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md)을 참조하세요.

>[AZURE.NOTE] 메모리 내 OLTP 미리 보기는 현재 단일 데이터베이스에 대해서만 지원되고 탄력적 데이터베이스 풀에 대해서는 지원되지 않습니다.

### 최대 동시 요청

동시 요청 수를 보려면 SQL 데이터베이스에서 다음 Transact-SQL 쿼리를 실행하세요.

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R

온-프레미스 SQL Server 데이터베이스의 워크로드를 분석하려는 경우 이 쿼리를 수정하여 분석할 특정 데이터베이스에서 필터링해야 합니다. 예를 들어 MyDatabase라는 온-프레미스 데이터베이스가 있다면 다음 Transact-SQL 쿼리는 해당 데이터베이스의 동시 요청 수를 반환합니다.

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

이는 단일 시점의 스냅숏일 뿐입니다. 워크로드를 보다 깊이 이해하려면 시간 변화에 따른 여러 샘플을 수집하여 동시 요청 요구 사항을 이해해야 합니다.

### 최대 동시 로그인

동시 로그인 수 또는 내역을 표시할 수 있는 쿼리나 DMV는 없습니다. 사용자 및 응용 프로그램 패턴을 분석하여 로그인 빈도를 파악할 수 있습니다. 또한 테스트 환경에서 실제 부하를 실행하여 본 항목에서 설명한 이 제한 또는 기타 제한에 도달하지 않도록 보장할 수 있습니다.

여러 클라이언트에서 동일한 연결 문자열을 사용하는 경우 서비스에서는 각 로그인을 인증합니다. 10명의 사용자가 동일한 사용자 이름 및 암호를 사용하여 특정 데이터베이스에 동시에 연결하는 경우 동시 로그인은 10개가 됩니다. 이 제한은 로그인 및 인증 기간에만 적용됩니다. 따라서 동일한 10명의 사용자가 특정 데이터베이스에 순차적으로 연결하는 경우 동시 로그인 수는 절대로 1보다 높을 수 없습니다.

>[AZURE.NOTE] 현재 이 제한은 탄력적 데이터베이스 풀의 데이터베이스에는 적용되지 않습니다.

### 최대 세션

동시 활성 세션 수를 보려면 SQL Database에서 다음 Transact-SQL 쿼리를 실행하세요.

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

온-프레미스 SQL Server 워크로드를 분석하려는 경우 특정 데이터베이스에 집중하도록 쿼리를 수정하세요. 이 쿼리는 데이터베이스를 Azure SQL Database로 이동하려 할 때 가능한 세션 요구 사항을 확인하는 데 도움이 됩니다.

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

다시 한 번 강조하지만, 이러한 쿼리는 지정 시간 수를 반환하므로 시간 경과에 따른 여러 샘플을 수집해야만 세션 사용 현황을 정확하게 파악할 수 있습니다.

SQL 데이터베이스 분석의 경우 **sys.resource\_stats**를 쿼리하여 **active\_session\_count** 열을 사용하는 세션에 대한 통계 자료를 얻을 수 있습니다. 다음 모니터링 섹션에서는 이 뷰 사용에 대한 추가 정보가 제공됩니다.

## 리소스 사용 모니터링
해당 서비스 계층과 관련된 SQL 데이터베이스의 리소스 사용을 모니터링할 수 있는 두 개의 뷰가 있습니다.

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### sys.dm\_db\_resource\_stats 사용
[sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 뷰는 각 SQL 데이터베이스에 있으며 해당 서비스 계층과 관련된 최신 리소스 사용률 데이터를 제공합니다. CPU, 데이터 IO, 로그 쓰기 및 메모리의 평균 백분율을 15초마다 기록하고 한 시간 동안 보관합니다.

이 뷰는 리소스 사용률에 대한 훨씬 구체적인 정보를 제공하므로 현재 상태 분석 또는 문제 해결에 **sys.dm\_db\_resource\_stats**를 먼저 사용해야 합니다. 예를 들어 다음 쿼리는 마지막 시간 동안 현재 데이터베이스의 평균 및 최대 리소스 사용률을 보여 줍니다.

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent',
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent',
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent',
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent'
	FROM sys.dm_db_resource_stats;  

다른 쿼리는 [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)의 예를 참조하세요.

### sys.resource\_stats 사용

[마스터](https://msdn.microsoft.com/library/dn269979.aspx) 데이터베이스의 **sys.resource\_stats** 뷰는 특정 서비스 계층 및 성능 수준 내에서 SQL 데이터베이스의 성능을 모니터링하기 위한 추가 정보를 제공합니다. 데이터는 5분마다 수집되어 약 35일 동안 보관됩니다. 이 뷰는 SQL 데이터베이스 리소스 사용률의 장기적인 기록 분석에 더 유용합니다.

다음 그래프는 한 주 동안 각 시간당 P2 성능 수준의 Premium 데이터베이스에 대한 CPU 리소스 사용률을 보여 줍니다. 이 그래프는 월요일부터 5근무일과 응용 프로그램 사용량이 훨씬 적은 주말까지 표시되어 있습니다.

![SQL DB 리소스 사용률](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

이 데이터베이스의 현재 최고 CPU 부하는 P2 성능 수준에 비해 약 50% 더 많습니다(화요일 낮). CPU가 응용 프로그램의 리소스 프로필에서 가장 지배적인 요인인 경우 P2가 항상 워크로드를 충족하는 데 적합한 성능 수준임을 확인할 수 있습니다. 응용 프로그램이 증가할 것으로 예상되는 경우 응용 프로그램이 최대 한도에 도달하지 않도록 추가 리소스 버퍼가 있는 것이 좋습니다. 성능 수준을 높이면 데이터베이스로 인해 특히 대기 시간에 민감한 환경(예: 데이터베이스 호출 결과를 기준으로 웹 페이를 채우는 응용 프로그램의 지원 데이터베이스)에서 요청을 효과적으로 처리하는 데 충분한 성능이 없는 경우와 같이 고객이 볼 수 있는 오류를 방지할 수 있습니다.

단, 다른 유형의 응용 프로그램은 동일한 그래프를 다르게 해석할 수 있습니다. 예를 들어 응용 프로그램에서 매일 급여 데이터를 처리하고 동일한 차트를 사용하는 경우와 같은 "일괄 처리 작업" 모델은 P1 성능 모델로 충분할 수 있습니다. P2 성능 수준은 200 DTU이지만 P1 성능 수준은 100 DTU입니다. P1 성능 수준은 P2 성능 수준의 절반의 성능만 제공한다는 의미입니다. P2에서 CPU 활용률의 50%는 P1에서 100% CPU 활용률과 동일합니다. 응용 프로그램에 시간 제한이 없는 이상 작업이 오늘 완료되기만 한다면 2시간이 소요되든, 2.5시간이 소요되든 중요하지 않을 수 있습니다. 이 범주의 응용 프로그램은 아마 P1 성능 수준을 사용할 것입니다. 하루 중 리소스 사용량이 낮은 시간대가 있다는 사실을 활용할 수 있습니다. 즉, "최고" 시간대의 작업을 하루 중 사용량이 낮은 시간대 중 하나로 나눌 수 있다는 의미입니다. 작업을 매일 정시에 완료할 수 있는 경우 이러한 응용 프로그램에는 P1 성능 수준이 적합하며 비용도 절감할 수 있습니다.

Azure SQL 데이터베이스는 각 서버에 있는 **마스터** 데이터베이스의 **sys.resource\_stats** 뷰로 각 활성 데이터베이스에 사용된 리소스 정보를 표시합니다. 표의 데이터는 5분 간격으로 집계되어 있습니다. Basic, Standard, Premium 서비스 계층에서 데이터가 테이블에 표시될 때까지 5분 이상이 소요될 수 있어 이 데이터는 거의 실시간에 가까운 분석보다 기록 분석에 더 적합합니다. **sys.resource\_stats** 뷰에 대한 쿼리는 선택한 예약이 필요 시 원하는 성능을 제공했는가 여부를 검증하는 데이터베이스의 최근 기록을 보여줍니다.

>[AZURE.NOTE] 다음 예제의 **sys.resource\_stats**를 쿼리하려면 논리 SQL Database 서버의 **마스터** 데이터베이스에 연결해야 합니다.

다음 예는 이 뷰의 데이터가 표시된 방식을 보여줍니다.

	SELECT TOP 10 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![sys 리소스 통계](./media/sql-database-performance-guidance/sys_resource_stats.png)

다음 예제에서는 **sys.resource\_stats** 카탈로그 뷰를 사용하여 SQL Database의 리소스 사용률을 이해하는 여러 방법을 보여 줍니다.

1. 예를 들어 데이터베이스 "userdb1"의 지난 주 리소스 사용을 살펴보기 위해 다음 쿼리를 실행할 수 있습니다.

		SELECT *
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;

2. 워크로드가 성능 수준에 얼마나 적합한지 평가하려면 리소스 메트릭의 각 측면(CPU, 읽기, 쓰기, 작업자 수, 세션 수)에서 드릴다운해야 합니다. 다음은 이러한 리소스 메트릭의 평균값 및 최대값에 대해 보고하기 위해 sys.resource\_stats를 사용하여 수정한 쿼리입니다.

		SELECT
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_data_io_percent) AS 'Average Physical Data IO Utilization In Percent',
		    max(avg_data_io_percent) AS 'Maximum Physical Data IO Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(max_session_percent) AS 'Average % of Sessions',
		    max(max_session_percent) AS 'Maximum % of Sessions',
		    avg(max_worker_percent) AS 'Average % of Workers',
		    max(max_worker_percent) AS 'Maximum % of Workers'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. 각 리소스 메트릭의 평균값 및 최대값에 대한 이전 정보를 사용하여 선택한 성능 수준에 워크로드가 얼마나 적합한지 평가할 수 있습니다. 일반적으로 sys.resource\_stats의 평균값은 대상 크기에 사용하기 적합한 기준선을 제공하며 기본 측정 기준이 되어야 합니다. 예를 들어 S2 성능 수준에서 Standard 서비스 계층을 사용하고, CPU, I/O 읽기, 쓰기의 평균 활용률이 40% 미만이며, 평균 작업자 수가 50명 미만이고, 평균 세션 수가 200 미만인 경우 워크로드는 S1 성능 수준에 적합할 수 있습니다. 데이터베이스가 작업자 및 세션 제한 이내인지 여부는 쉽게 확인할 수 있습니다. 데이터베이스가 CPU, 읽기, 쓰기 기준의 하위 성능 수준에 적합한지 확인하려면 하위 성능 수준의 DTU 수를 현재 성능 수준의 DTU 수로 나눈 다음 결과에 100을 곱합니다.

	**S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**

	결과는 백분율로 표시한 두 성능 수준 간 상대적 성능 차이입니다. 활용률이 이 백분율을 초과하지 않는 경우 워크로드가 이보다 낮은 성능 수준 이내일 수 있습니다. 하지만 모든 범위의 리소스 사용 값을 살펴보고 데이터베이스가 하위 성능 수준에 적합한 빈도를 백분율로 확인해야 합니다. 다음 쿼리는 이전에 계산된 40%의 임계값을 기준으로 리소스 규격당 적합률을 출력합니다.

		SELECT
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	데이터베이스의 SLO(서비스 수준 목표)를 기준으로 워크로드가 하위 성능 수준에 적합한지 여부를 결정할 수 있습니다. 데이터베이스 워크로드 SLO가 99.9%이고 위 쿼리에서 세 가지 리소스 규격에 대해 99.9보다 큰 값을 반환할 경우 워크로드가 하위 성능 수준에 적합할 가능성이 높습니다.

	적합률을 살펴보면 SLO를 충족하기 위해 상위 성능 수준으로 이동해야 하는지 여부를 알 수 있습니다. 예를 들어 "userdb1"에서 지난 주의 활용률은 다음과 같습니다.

	| 평균 CPU 사용률 | 최대 CPU 사용률 |
	|---|---|
	| 24\.5 | 100\.00 |

	평균 CPU는 성능 수준 한도의 약 1/4이며 데이터베이스 성능 수준에 적합합니다. 하지만 최대값은 데이터베이스가 성능 수준 한도에 도달함을 보여줍니다. 다음 상위 성능 수준으로 이동해야 하나요? 이 경우에도 워크로드가 100%에 도달하는 횟수를 살펴보고 데이터베이스 워크로드 SLO와 비교해야 합니다.

		SELECT
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	이 쿼리에서 세 가지 리소스 규격에 대해 99.9% 미만의 값을 반환할 경우 다음 상위 성능 수준으로 이동하거나 응용 프로그램 튜닝 기술을 사용하여 Azure SQL Database에서 부하를 줄입니다.

4. 이전 연습은 향후 예상되는 워크로드 증가를 고려합니다.

## 응용 프로그램 튜닝

기존 온-프레미스 SQL Server에서 초기 용량 계획 프로세스는 프로덕션 응용 프로그램의 실행 프로세스에서 분리된 경우가 많았습니다. 달리 말해, SQL Server를 실행하기 위해 사전에 하드웨어와 관련 라이선스를 구입하고 그 다음에 성능 튜닝을 수행했습니다. Azure SQL Database를 사용할 경우에는 월 단위로 청구되므로 응용 프로그램의 실행 및 튜닝 프로세스를 맞물려 수행하는 것이 좋습니다. 주문형 용량 지불 모델에서는 응용 프로그램에 대해 어림짐작한 미래 성장 계획(먼 미래를 예측해야 하므로 정확하지 않은 경우가 많음)을 기준으로 과도한 프로비전을 하지 않고, 응용 프로그램을 튜닝하여 현재 필요한 최소 리소스를 사용할 수 있습니다. 일부 고객은 응용 프로그램을 튜닝하지 않고 하드웨어 리소스의 과도한 프로비전을 선택할 수 있습니다. 응용 프로그램 사용률이 높은 기간에 핵심 응용 프로그램을 변경하지 않으려는 경우에는 이 방식이 합리적일 수 있습니다. 응용 프로그램을 튜닝하면 Azure SQL 데이터베이스에서 서비스 계층을 사용할 때 리소스 요구 사항을 최소화하고 월 청구 금액을 낮출 수 있습니다.

### 응용 프로그램의 특성

서비스 계층이 응용 프로그램의 성능 안정성과 예측 가능성을 향상하도록 설계되었지만 응용 프로그램을 튜닝하여 성능 수준 내에서 리소스를 더욱 효율적으로 활용하는 몇 가지 모범 사례가 있습니다. 대부분의 응용 프로그램이 단순히 높은 성능 수준 또는 서비스 계층으로 전환하여 성능을 크게 향상할 수 있는 반면, 일부 응용 프로그램은 추가 튜닝을 하지 않을 경우 이점을 얻을 수 없습니다. 다음과 같은 특성을 가진 응용 프로그램은 Azure SQL 데이터베이스를 사용할 경우 성능을 더욱 향상하기 위해 추가적 응용 프로그램 튜닝도 고려해야 합니다.

- **"잦은" 동작으로 인해 성능이 느려지는 응용 프로그램**: Chatty 응용 프로그램은 네트워크 대기 시간에 민감한 과도한 데이터 액세스 작업을 만듭니다. 이러한 응용 프로그램은 Azure SQL 데이터베이스에 대한 데이터 액세스 작업 수를 줄이기 위한 수정이 필요할 수 있습니다. 예를 들어 임시 쿼리를 일괄 처리로 처리하거나 쿼리를 저장된 프로시저로 이동하는 등의 기법을 사용하여 응용 프로그램 성능을 향상할 수 있습니다. 자세한 내용은 아래의 '쿼리 일괄 처리' 섹션을 참조하세요.
- **단일 시스템 전체로 지원할 수 없는 많은 워크로드의 데이터베이스**: 가장 높은 Premium 성능의 리소스를 초과하는 데이터베이스는 적합하지 않습니다. 이러한 데이터베이스는 워크로드 확장이 도움이 될 수 있습니다. 자세한 내용은 아래의 '교차-데이터베이스 분할' 및 '기능 분할' 섹션을 참조하세요.
- **비최적 쿼리가 포함된 응용 프로그램**: 특히 데이터 액세스 계층에서 잘못 튜닝된 쿼리가 있는 응용 프로그램은 상위 성능 수준을 선택할 경우 도움이 되지 않을 수 있습니다. 예를 들어 WHERE 절이 없거나 인덱스가 누락되거나 통계가 오래된 쿼리가 있습니다. 이러한 응용 프로그램은 표준 쿼리 성능 튜닝 기술을 사용하는 것이 도움이 됩니다. 자세한 내용은 아래의 '인덱스 누락' 및 '쿼리 튜닝/힌팅' 섹션을 참조하세요.
- **비최적 데이터 액세스 설계의 응용 프로그램**: 교착 상태와 같이 본질적인 데이터 액세스 동시성 문제가 있는 응용 프로그램은 상위 성능 수준을 선택할 경우 도움이 되지 않을 수 있습니다. 응용 프로그램 개발자는 Azure 캐싱 서비스 또는 다른 캐싱 기술로 클라이언트 쪽에서 데이터를 캐싱하여 Azure SQL 데이터베이스에 대한 왕복을 줄이는 것을 고려해야 합니다. 아래의 응용 프로그램 계층 캐싱 섹션을 참조하세요.

## 튜닝 기법
이 섹션에서는 Azure SQL 데이터베이스를 튜닝하여 응용 프로그램에서 최고의 성능을 달성하고 최저 성능 수준에서도 실행할 수 있는 몇 가지 기법에 대해 설명합니다. 이러한 기술 중 일부는 기존 SQL Server 튜닝의 모범 사례와 동일하지만 일부 기술은 Azure SQL Database에만 해당합니다. 데이터베이스에 사용된 리소스를 조사하고 추가 튜닝 영역을 찾으면 기존 SQL Server 기법을 확장하여 Azure SQL 데이터베이스에서도 사용할 수 있습니다.

### Query Performance Insight 및 SQL 데이터베이스 관리자
SQL Database는 데이터베이스의 성능 문제를 분석하고 해결할 수 있는 두 가지 도구를 Azure Portal에 제공합니다.

- [쿼리 성능 Insight](sql-database-query-performance.md)
- [SQL 데이터베이스 관리자](sql-database-advisor.md)

각 도구 및 사용 방법에 대한 자세한 내용은 이전 링크를 참조하세요. 누락된 인덱스 및 쿼리 튜닝에 대한 다음 두 섹션에서는 비슷한 성능 문제를 수동으로 찾아서 해결하는 다른 방법을 제공합니다. 먼저 포털의 도구를 사용하여 보다 효율적으로 문제를 진단 및 해결하려고 시도하는 것이 좋습니다. 수동 튜닝 방식은 특별한 경우에만 사용하세요.

### 인덱스 누락
OLTP 데이터베이스 성능의 일반적인 문제는 물리적 데이터베이스 설계와 관련되어 있습니다. 데이터베이스 스키마를 (부하 또는 데이터 볼륨에서) 대규모로 테스트하지 않고 설계 및 배송하는 경우가 많습니다. 하지만 쿼리 계획의 성능이 소규모에서는 만족할 만한 수준인 경우에도 프로덕션 수준의 데이터 볼륨을 처리할 경우 크게 저하될 수 있습니다. 이 문제의 가장 일반적인 원인은 필터 또는 쿼리의 다른 제한을 충족할 수 있는 적절한 인덱스가 없기 때문입니다. 인덱스 누락으로 인해 인덱스 검색으로도 충분한 상황에서도 테이블 검색을 수행하는 경우가 많습니다.

다음 예는 검색이 충분할 상황에서 선택한 쿼리 계획에 스캔이 포함된 경우의 케이스를 만듭니다.

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
	WHERE m1.col2 = 4;

![인덱스가 누락된 쿼리 계획](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL 데이터베이스에는 데이터베이스 관리자가 누락된 인덱스 조건을 쉽게 찾고 해결할 수 있는 기능이 포함되어 있습니다. Azure SQL 데이터베이스에 통합된 DMV(동적 관리 뷰)는 인덱스로 쿼리 실행의 예상 비용을 크게 줄일 수 있는 쿼리 컴파일을 확인합니다. 쿼리 실행 중에는 SQL Database가 각 쿼리 계획이 실행된 빈도뿐만 아니라 쿼리 계획 실행과 해당 인덱스가 있을 경우 예상되는 쿼리 실행 간 예상 차이를 추적합니다. 이러한 DMV를 통해 데이터베이스 관리자는 물리적 데이터베이스 설계를 어떻게 변경해야 특정 데이터베이스와 실제 워크로드의 전반적 워크로드 비용을 개선할 수 있을지 빠르게 추정할 수 있습니다.

다음 쿼리를 사용하여 잠재적 누락 인덱스를 확인할 수 있습니다.

	SELECT CONVERT (varchar, getdate(), 126) AS runtime,
	    mig.index_group_handle, mid.index_handle,
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure,
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
	              (' + ISNULL (mid.equality_columns,'')
	              + CASE WHEN mid.equality_columns IS NOT NULL
	                          AND mid.inequality_columns IS NOT NULL
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')'
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
	    migs.*,
	    mid.database_id,
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

이 예에서는 다음 인덱스를 제안합니다.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

동일한 SELECT 문이 생성되면 스캔이 아닌 검색을 사용하는 다른 계획을 선택하여 다음 쿼리 계획보다 더 효율적으로 실행합니다.

![수정된 인덱스가 있는 쿼리 계획](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

공유된 상용 시스템의 IO 용량은 전용 서버 시스템보다 제한적입니다. 불필요한 IO를 최소화하여 Azure SQL Database에서 서비스 계층의 각 성능 수준의 DTU 내에서 시스템을 최대한 활용하는 것이 유리합니다. 물리적 데이터베이스 설계를 적절히 선택할 경우 개별 쿼리의 대기 시간 및 배율 단위당 처리할 수 있는 동시 요청의 처리량을 크게 개선하고 쿼리를 충족하는 데 필요한 비용을 최소화할 수 있습니다. 누락된 인덱스 DMV에 대한 자세한 내용은 [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx)를 참조하세요.

### 쿼리 튜닝/힌팅
Azure SQL Database 내 쿼리 최적화 프로그램은 기존 SQL Server 쿼리 최적화 프로그램과 유사합니다. 쿼리 튜닝 및 쿼리 최적화 프로그램의 추론 모델 제한 이해에 대한 모범 사례의 대부분은 Azure SQL Database에도 적용됩니다. Azure SQL Database에서 쿼리를 튜닝할 경우 하위 성능 수준에서 실행할 수 있으므로 튜닝하지 않은 경우에 비해 집계 리소스 요구 사항을 줄이고 응용 프로그램을 더 경제적인 비용으로 실행할 수 있습니다.

Azure SQL 데이터베이스에도 적용되는 SQL Server의 일반적 예제는 더욱 최적인 계획을 만들기 위해 컴파일 중 매개 변수가 "스니프"되는 방식과 관련되어 있습니다. 매개 변수 스니프는 쿼리 최적화 프로그램이 더욱 최적인 쿼리 계획을 만들기 위해 쿼리를 컴파일할 때 매개 변수의 현재 값을 고려하는 프로세스입니다. 이 전략으로 매개 변수 값을 모르고 컴파일한 계획보다 훨씬 빠른 쿼리 계획을 만들 수 있는 경우가 많지만, 현재 SQL Server/Azure SQL 데이터베이스 동작은 완벽하지 않습니다. 매개 변수가 스니프되지 않는 경우, 매개 변수가 스니프되었지만 생성된 계획이 워크로드의 전체 매개 변수 값 집합에 최적이 아닌 경우가 있습니다. Microsoft는 더욱 의도적으로 지정하고 매개 변수 스니프의 기본 동작을 재정의할 수 있도록 쿼리 힌트(지침)를 포함합니다. 힌트를 사용하면 기본 SQL Server/Azure SQL 데이터베이스 동작이 지정된 고객 워크로드에서 완벽하지 않은 케이스를 해결할 수 있는 경우도 있습니다.

다음 예는 쿼리 프로세서가 성능 및 리소스 요구 사항에 대해 최적이 아닌 계획을 생성하는 방식과 쿼리 힌트를 사용하여 Azure SQL Database에서 쿼리 런타임 및 리소스 요구 사항을 줄이는 방식을 보여 줍니다.

다음은 예제 설정입니다.

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO

	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO

	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO

	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

설정 코드는 기울어진 데이터 분포가 포함된 테이블을 만듭니다. 최적 쿼리 계획은 선택한 매개 변수에 따라 달라집니다. 하지만 계획 캐싱 동작이 항상 가장 일반적인 매개 변수 값을 기준으로 쿼리를 재컴파일하는 것은 아닙니다. 즉, 다른 계획이 더 나은 평균 계획이 될 수 있는 경우에도 여러 값에 대해 최적이 아닌 계획을 캐싱 및 사용할 가능성이 있습니다. 그런 다음 하나의 특별 쿼리 힌트가 포함된 프로시저를 제외하고 두 개의 동일한 저장된 프로시저를 만듭니다.

**예(1부):**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;

	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**예(2부 - 결과 원격 분석 데이터가 분명히 다르도록 10분간 기다린 다음 이 부분을 시도):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;

	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

이 예의 각 부분은 (테스트 데이터 집합에서 참조할 충분한 부하를 생성하기 위해) 매개 변수가 있는 insert 문의 실행을 1000회시도합니다. 저장된 프로시저를 실행할 경우 쿼리 프로세서는 첫 번째 컴파일 중 프로시저로 전달된 매개 변수 값을 검사합니다(매개 변수 "스니프"라고 함). 매개 변수 값이 다른 경우에도 결과 계획이 캐싱되고 나중의 호출에 사용됩니다. 그 결과 최적의 계획이 사용되지 않는 경우가 있을 수 있습니다. 고객은 쿼리가 최초로 컴파일되었을 때 구체적인 케이스보다 평균적인 케이스에 더 적합한 계획을 선택하도록 경우에 따라 최적화 프로그램을 조정해야 합니다. 이 예에서 초기 계획은 모든 행을 읽어서 매개 변수와 일치하는 각 값을 찾는 "스캔" 계획을 생성합니다.

![쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_1.png)

여기서는 프로시저를 값 1로 실행했기 때문에 결과 계획은 1에 대해 최적이지만 테이블의 나머지 값에 대해서는 최적이 아닙니다. 각 계획을 무작위로 선택할 경우 계획이 더 느리게 실행되고 실행에 더 많은 리소스를 사용하기 때문에 결과 행동이 바람직한 행동이 아닐 가능성이 높습니다.

이 예는 "SET STATISTICS IO ON"으로 테스트를 실행할 경우 내부적으로 완료된 논리적 스캔 작업을 보여줍니다. 계획에서 1148회의 읽기를 수행한 것을 볼 수 있습니다(평균 케이스에서 한 행만 반환해야 하는 경우 비효율적임).

![쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_2.png)

예제의 두 번째 부분은 쿼리 힌트를 사용하여 최적화 프로그램이 컴파일 프로세스 중 특정 값을 사용하도록 합니다. 이 경우 쿼리 프로세서가 매개 변수로 전달된 값을 무시하고, 테이블의 평균 빈도를 가진 값을 의미하는 "UNKNOWN"을 가정합니다(기울기 무시). 결과 계획은 더욱 빠른 검색 기반 계획으로, 예의 1부 계획보다 평균적으로 더 적은 리소스를 사용합니다.

![쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_3.png)

영향은 **sys.resource\_stats** 테이블을 확인하여 알 수 있습니다(테스트를 실행하는 시간에서 데이터가 테이블에 채워지는 시간까지 지연이 있습니다). 이 예제에서 1부는 22:25:00 기간 중 실행되었으며 2부는 22:35:00에 실행되었습니다. 여기서 이전 기간은 (계획 효율성 개선으로 인해) 이후 기간보다 더 많은 리소스를 사용했습니다.

	SELECT TOP 1000 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![쿼리 튜닝](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] 여기서 사용된 예제는 의도적으로 작게 만들었지만 최적이 아닌 매개 변수의 영향은 특히 큰 데이터베이스에서 크게 나타날 수 있습니다. 극한의 경우 그 차이는 빠르고 느린 케이스에서 몇 초 내지 몇 시간이 될 수 있습니다.

**sys.resource\_stats**를 검사하여 특정 테스트의 리소스가 다른 테스트보다 리소스를 더 많이 또는 더 적게 사용했는지 확인할 수 있습니다. 데이터를 비교할 때에는 **sys.resource\_stats** 뷰에서 동일한 5분 기간 동안 그룹화되지 않은 충분한 시간으로 테스트를 구분합니다. 이 연습의 목표는 최대 리소스를 최소화하는 것이 아니라 사용된 총 리소스를 최소화하는 것입니다. 일반적으로 대기 시간의 코드를 최적화할 경우 리소스 소비가 감소합니다. 응용 프로그램에서 고려하는 변경 사항이 필요한지 여부와 쿼리 힌트를 사용할 경우 응용 프로그램을 사용하는 고객의 경험에 부정적 영향을 미치지 않는지 확인합니다.

워크로드에 반복되는 쿼리 집합이 포함된 경우 데이터베이스를 호스트하는 데 필요한 최소 리소스 크기 단위가 결정되므로 선택한 계획의 최적성을 확인하는 것이 좋은 경우가 많습니다. 확인을 마친 다음 이러한 계획을 가끔씩 재검사하여 계획이 저하되지 않았는지 확인합니다. 쿼리 힌트에 대한 자세한 내용은 [쿼리 힌트(Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)를 참조하세요.

### 교차-데이터베이스 분할
Azure SQL Database는 상용 하드웨어에서 실행되므로 기존 온-프레미스 SQL Server 설치보다 단일 데이터베이스에 대한 용량 제한이 낮습니다. 따라서 일부 고객은 분할 기법을 사용하여 데이터베이스 작업이 Azure SQL Database의 단일 데이터베이스 한도를 초과할 경우 데이터베이스 작업을 여러 데이터베이스에 분할합니다. 현재 Azure SQL 데이터베이스에 분할 기법을 사용하는 대부분의 고객은 여러 데이터베이스에서 단일 규격으로 데이터를 분할합니다. 이 방식에서는 OLTP 응용 프로그램이 스키마 내에서 하나의 행 또는 작은 행 그룹에만 적용되는 트랜잭션을 수행하는 경우가 많다는 점을 이해해야 합니다.

>[AZURE.NOTE] 이제 SQL 데이터베이스는 분할을 지원하기 위한 라이브러리를 제공합니다. 자세한 내용은 [탄력적 데이터베이스 클라이언트 라이브러리 개요](sql-database-elastic-database-client-library.md)를 참조하세요.

예를 들어 데이터베이스에 고객, 주문, 주문 정보가 포함된 경우(SQL Server에 기본 제공된 기존 예제 Northwind 데이터베이스와 동일) 관련 주문 및 주문 정보가 있는 고객을 그룹화하고 단일 데이터베이스 내에 유지된다는 점을 확인하여 이 데이터를 여러 데이터베이스로 분할할 수 있습니다. 응용 프로그램은 다양한 고객을 데이터베이스로 분할하여 부하를 여러 데이터베이스로 효과적으로 나눕니다. 분할을 통해 고객은 최대 데이터베이스 한도에 도달하지 않으며, 개별 데이터베이스가 해당 DTU에 적합한 이상 Azure SQL Database가 다양한 성능 수준의 한도보다 훨씬 큰 워크로드를 처리할 수 있습니다.

데이터베이스 분할로 솔루션의 총 리소스 용량이 감소되지는 않지만, 이 기법은 여러 데이터베이스에 분할된 대규모 솔루션을 지원하는 데 효과적이며 각 데이터베이스가 다양한 성능 수준에서 실행되므로 높은 리소스 요구 사항의 매우 큰 "효과적" 데이터베이스를 지원할 수 있습니다.

### 기능 분할
SQL Server 사용자는 단일 데이터베이스 내에 여러 기능을 결합하는 경우가 많습니다. 예를 들어 응용 프로그램에 매장 재고를 관리하는 논리가 포함되어 있을 경우 해당 데이터베이스에는 재고와 관련된 논리, 구매 주문서 추적, 저장된 프로시저, 월말 보고 고 및 기타 기능을 위해 인덱스가 있고/구체화된 뷰가 포함되어 있을 수 있습니다. 이 기법은 데이터베이스에서 백업과 같은 작업을 쉽게 관리할 수 있는 이점을 제공하지만, 응용 프로그램의 모든 기능에서 최고 부하를 처리할 수 있도록 하드웨어 규모를 늘려야 합니다.

Azure SQL Database 내에서 사용되는 확장형 아키텍처에서는 응용 프로그램의 다양한 기능을 다양한 데이터베이스로 분할하는 것이 유리합니다. 이 기술을 통해 각 데이터베이스를 독립적으로 확장할 수 있습니다. 응용 프로그램 사용량이 많아지면(데이터베이스의 부하 증가) 관리자가 응용 프로그램 내 각 기능에 대해 성능 수준을 독립적으로 선택할 수 있습니다. 이 아키텍처에서 한도에 도달할 경우 여러 시스템으로 부하를 분산하여 단일 상용 시스템이 처리할 수 있는 것보다 크게 응용 프로그램을 확장할 수 있습니다.

### 쿼리 일괄 처리
임시 쿼리를 빈번하게 수행하는 방식으로 데이터에 액세스하는 응용 프로그램의 경우 응용 프로그램 계층과 Azure SQL Database 계층 간 네트워크 통신에서 많은 응답 시간이 사용됩니다. 응용 프로그램과 Azure SQL Database가 동일한 데이터 센터에 상주하는 경우에도 데이터 액세스 작업 수가 많으면 그 사이의 네트워크 대기 시간이 커질 수 있습니다. 이러한 데이터 액세스 작업의 네트워크 왕복을 줄이려면 응용 프로그램 개발자가 임시 쿼리를 일괄 처리하거나 저장된 프로시저로 컴파일하는 방법을 고려해야 합니다. 임시 쿼리를 일괄 처리할 경우 복수 쿼리를 Azure SQL Database로 하나의 큰 일괄 처리로 한 번에 보낼 수 있습니다. 임시 쿼리를 저장된 프로시저로 컴파일하면 일괄 처리와 동일한 결과를 얻을 수 있습니다. 저장된 프로시저를 사용하면 Azure SQL 데이터베이스에서 쿼리 계획을 캐싱한 다음 나중에 동일한 저장된 프로시저를 실행하는 데 사용할 수 있습니다.

일부 응용 프로그램은 쓰기를 많이 사용합니다. 쓰기를 일괄 처리하는 방법을 고려하여 데이터베이스에서 총 IO 부하를 줄일 수 있는 경우도 있습니다. 이렇게 하려면 저장된 프로시저 및 임시 배치 내에서 트랜잭션을 자동 커밋하는 대신 명시적 트랜잭션을 사용하기만 하면 됩니다. 사용 가능한 다양한 기법의 평가 정보는 [Azure에서 SQL 데이터베이스 응용 프로그램의 일괄 처리 기법](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)을 참조하세요. 자체 워크로드로 시험하면서 올바른 일괄 처리 모델을 찾아보고 특정 모델은 트랜잭션 일관성 보장이 약간 다를 수 있음을 이해하세요. 리소스 사용을 최소화하는 올바른 워크로드를 찾으려면 일관성과 성능 사이의 올바른 조합을 찾아야 합니다.

### 응용 프로그램 계층 캐싱
일부 데이터베이스 응용 프로그램에는 읽기 작업이 많은 워크로드가 포함되어 잇습니다. 캐싱 계층을 활용하여 데이터베이스의 부하를 줄이고 Azure SQL 데이터베이스를 사용하여 데이터베이스를 지원하는 데 필요한 성능 수준을 줄일 수 있습니다. [Azure Redis 캐시](https://azure.microsoft.com/services/cache/)를 사용하면 고객이 읽기 작업이 많은 워크로드에서 데이터를 한 번만 읽고(또는 구성 방식에 따라 응용 프로그램 계층 시스템당 한 번) 해당 데이터를 Azure SQL 데이터베이스 밖에 저장할 수 있습니다. 그러면 데이터베이스 부하(CPU 및 읽기 IO)가 감소하지만, 캐시에서 읽는 데이터가 데이터베이스에 있는 데이터와 날짜가 다를 수 있어 트랜잭션 일관성에 영향을 미칠 수 있습니다. 비일관성이 허용되는 응용 프로그램도 많지만 모든 워크로드에 대해 허용되는 것은 아닙니다. 응용 프로그램 계층 캐싱 전략을 채택할 경우 응용 프로그램 요구 사항을 완전히 이해해야 합니다.

## 다음 단계

- 서비스 계층에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.
- 탄력적 풀에 대한 자세한 내용은 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.
- 성능 및 탄력적 풀에 대한 자세한 내용은 [탄력적 풀의 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->