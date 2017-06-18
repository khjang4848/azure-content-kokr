<properties
	pageTitle="SQL 데이터베이스 성능 튜닝 팁 | Microsoft Azure"
	description="평가 및 개선을 통한 Azure SQL 데이터베이스의 성능 튜닝 관련 팁."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="sql 성능 튜닝, 데이터베이스 성능 튜닝, sql 성능 튜닝 팁, sql 데이터베이스 성능 튜닝"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# SQL 데이터베이스 성능 튜닝 팁
단일 데이터베이스의 [서비스 계층](sql-database-service-tiers.md)을 변경하거나 탄력적 데이터베이스 풀의 eDTU를 늘려 언제든지 성능을 개선할 수 있지만 먼저 쿼리 성능을 개선 및 최적화할 기회를 파악하고 싶을 수 있습니다. 인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이 문서에서는 SQL 데이터베이스의 성능 튜닝에 대한 지침을 제공합니다.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 데이터베이스 성능을 평가 및 조정하는 단계
1.	[Azure 포털](https://portal.azure.com)에서 **SQL 데이터베이스**를 클릭하고 데이터베이스를 선택한 후 모니터링 차트를 사용하여 최대값에 근접한 리소스를 찾습니다. 기본적으로 DTU 사용량이 표시됩니다. **편집**을 클릭하여 표시된 시간 범위 및 값을 변경합니다.
2.	[쿼리 성능 Insight](sql-database-query-performance.md)를 사용하여 DTU를 사용하는 쿼리를 평가한 다음 [SQL 데이터베이스 관리자](sql-database-advisor.md)를 사용하여 인덱스 만들기 및 삭제, 쿼리 매개 변수화 및 스키마 문제 해결에 대한 권장 사항을 확인합니다.
3.	동적 관리 뷰(DMV), 확장 이벤트(Xevents) 및 SSMS의 쿼리 저장소를 사용하여 실시간으로 성능 매개 변수를 가져올 수 있습니다. 자세한 모니터링 및 튜닝 팁은 [성능 지침 항목](sql-database-performance-guidance.md)을 참조하세요.


    > [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).


## 보다 많은 리소스와 함께 데이터베이스 성능을 개선하는 단계
1.	단일 데이터베이스의 경우 필요에 따라 [서비스 계층을 변경](sql-database-scale-up.md)하여 데이터베이스 성능을 개선할 수 있습니다.
2.	여러 데이터베이스의 경우 [탄력적 데이터베이스 풀](sql-database-elastic-pool-guidance.md)을 사용하여 리소스 규모를 자동으로 조정하는 방법을 고려합니다.

<!---HONumber=AcomDC_0914_2016-->