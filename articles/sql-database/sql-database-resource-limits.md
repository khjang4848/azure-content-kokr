<properties
	pageTitle="Azure SQL 데이터베이스 리소스 제한"
	description="이 페이지에서는 Azure SQL 데이터베이스에 대한 몇 가지 일반적인 리소스 제한을 설명합니다."
	services="sql-database"
	documentationCenter="na"
	authors="CarlRabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="07/19/2016"
	ms.author="carlrab" />


# Azure SQL 데이터베이스 리소스 제한

## 개요

Azure SQL 데이터베이스는 **리소스 관리**와 **제한 적용**이라는 서로 다른 두 메커니즘을 사용하는 데이터베이스에서 사용 가능한 리소스를 관리합니다. 이 항목에서는 리소스 관리의 다음 두 주요 영역을 설명합니다.

## 리소스 관리
Basic, Standard 및 Premium 서비스 계층의 설계 목표 중 하나는 데이터베이스가 다른 데이터베이스로부터 완전히 격리된 자체 컴퓨터에서 실행되는 것처럼 Azure SQL 데이터베이스가 동작하도록 하는 것입니다. 리소스 관리는 이러한 동작을 에뮬레이션합니다. 집계 리소스 사용률이 데이터베이스에 할당된 최대 사용 가능한 CPU, 메모리, 로그 I/O 및 데이터 I/O 리소스에 도달하면 리소스 관리가 실행 중인 쿼리를 큐에 대기시키고 리소스가 확보되는 대로 큐에 대기 중인 쿼리에 리소스를 할당합니다.

전용 컴퓨터에서도 사용 가능한 모든 리소스를 활용하면 현재 실행 중인 쿼리의 실행 시간이 길어지며 이것은 클라이언트에서 명령 시간 초과로 이어질 수 있습니다. 적극적인 재시도 논리를 사용하는 응용 프로그램 및 데이터베이스에 대한 쿼리 실행 빈도가 높은 응용 프로그램에서 동시 요청 수 제한에 도달한 경우 새 쿼리를 실행하려고 할 때 오류 메시지가 발생할 수 있습니다.

### 권장 사항:
데이터베이스의 최대 사용률에 근접한 경우 리소스 사용률 및 평균 쿼리 응답 시간을 모니터링합니다. 쿼리 대기 시간이 높아졌을 때 일반적으로 세 가지 옵션이 있습니다.

1.	데이터베이스로 들어오는 요청의 양을 줄여 시간 초과와 요청이 밀리는 일을 방지합니다.

2.	데이터베이스에 보다 높은 수준의 성능을 할당합니다.

3.	쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 Azure SQL 데이터베이스 성능 지침 문서의 쿼리 튜닝/힌트 섹션을 참조하세요.

## 제한 적용
CPU, 메모리, 로그 I/O 및 데이터 I/O 이외의 리소스는 제한에 도달할 때 새 요청을 거부하여 적용합니다. 클라이언트는 제한에 도달하면 [오류 메시지](sql-database-develop-error-messages.md)를 받게 됩니다.

예를 들어 SQL 데이터베이스에 대한 연결 수 및 처리할 수 있는 동시 요청 수는 제한되어 있습니다. SQL 데이터베이스는 데이터베이스에 대한 연결 수가 동시 요청 수를 초과하는 것을 허용하여 연결 풀링을 지원합니다. 사용할 수 있는 연결의 양은 응용 프로그램에서 쉽게 제어할 수 있지만, 병렬 요청의 양은 예측하고 제어하기가 보통 어렵습니다. 특히 최대 로드 중에 응용 프로그램이 너무 많은 요청을 보내거나 데이터베이스가 리소스 한계에 도달하고 길어진 쿼리 실행 시간으로 작업자 스레드가 밀리기 시작한 경우 오류가 발생할 수 있습니다.

## 서비스 계층 및 성능 수준

독립 실행형 데이터베이스 및 탄력적 풀 모두에 대한 서비스 계층과 성능 수준이 있습니다.

### 독립 실행형 데이터베이스

독립 실행형 데이터베이스에 대한 데이터베이스 제한은 데이터베이스 서비스 계층 및 성능 수준에 의해 정의됩니다. 다음 표에서는 다양한 성능 수준에서 Basic, Standard 및 Premium 데이터베이스의 특징을 설명합니다.

[AZURE.INCLUDE [SQL DB 서비스 계층 테이블](../../includes/sql-database-service-tiers-table.md)]

### 탄력적 풀

[탄력적 풀](sql-database-elastic-pool.md)은 풀의 데이터베이스에서 리소스를 공유합니다. 다음 표에서는 Basic, Standard 및 Premium 탄력적 데이터베이스 풀의 특징을 설명합니다.

[AZURE.INCLUDE [탄력적 데이터베이스에 대한 SQL DB 서비스 계층 표](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

이전 표에 나열된 각 리소스의 확장된 정의는 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)의 설명을 참조하세요. 서비스 계층에 대한 개요는 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)을 참조하세요.

## 기타 SQL 데이터베이스 제한

| 영역 | 제한 | 설명 |
|---|---|---|
| 구독당 자동화된 내보내기를 사용하는 데이터베이스 | 10 | 자동화된 내보내기를 사용하면 SQL 데이터베이스 백업에 대한 사용자 지정 일정을 만들 수 있습니다. 자세한 내용은 [SQL 데이터베이스: 자동화된 SQL 데이터베이스 내보내기 지원](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines)을 참조하세요.|
| 서버당 데이터베이스 | 최대 5000 | V12 서버에서는 서버당 최대 5,000개의 데이터베이스를 허용합니다. |  
| 서버당 DTU | 45000 | V12 서버에서는 데이터베이스, 탄력적 풀 및 데이터 웨어하우스를 프로비전하는 데 서버당 45,000개의 DTU를 사용할 수 있습니다. |



## 리소스

[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)

[SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md)

<!---HONumber=AcomDC_0914_2016-->