<properties
	pageTitle="SQL 데이터베이스 성능 및 옵션: 서비스 계층 | Microsoft Azure"
	description="규모를 조정하는 동안 비용 및 기능의 균형을 유지하도록 서비스 계층의 SQL 데이터베이스 성능 및 비즈니스 연속성 기능을 비교합니다."
	keywords="데이터베이스 옵션, 데이터베이스 성능"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor="CarlRabeler"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/10/2016"
	ms.author="carlrab"/>

# SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해

[Azure SQL Database](sql-database-technical-overview.md)는 여러 성능 수준의 3가지 서비스 계층을 제공하여 여러 워크로드를 다룹니다. 각 성능 수준은 더욱 높은 처리량을 제공하도록 설계된 증가된 리소스 집합을 제공합니다. 고유한 성능 수준을 포함한 고유한 [서비스 계층](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels)에서 각 데이터베이스를 관리할 수 있습니다. 공유 리소스 집합을 포함한 [탄력적 풀](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)에서 여러 데이터베이스를 관리할 수도 있습니다. 독립 실행형 데이터베이스에 사용할 수 있는 리소스는 DTU(데이터베이스 트랜잭션 단위)의 측면에서 표현되고 탄력적 풀에 사용할 수 있는 리소스는 탄력적인 DTU 또는 eDTU의 측면에서 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU란?](sql-database-what-is-a-DTU.md)을 참조하세요.

두 경우 모두 서비스 계층은 **Basic**, **Standard** 및 **Premium**을 포함합니다. 이러한 계층의 데이터베이스 옵션은 독립 실행형 데이터베이스 및 탄력적 풀에서 유사하지만 탄력적 풀에 대해 추가 고려 사항이 있습니다. 이 문서에서는 독립 실행형 데이터베이스 및 탄력적 풀에 대한 서비스 계층에 대해 자세히 설명합니다.

## 서비스 계층 및 데이터베이스 옵션
기본, 표준 및 프리미엄 서비스 계층은 모두 가동 시간 SLA가 99.99%이고 예측 가능한 성능, 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간당 대금 청구 기능을 제공합니다. 다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
|---|---|
| **Basic** | 일반적으로 지정된 시기에 활성 작업 한 개를 지원하는 작은 데이터베이스에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 데이터베이스가 이에 해당합니다. |
| **Standard** | 대부분의 클라우드 응용 프로그램에 적합한 옵션이며 복수의 동시 쿼리를 지원합니다. 예를 들어 작업 그룹 또는 웹 응용 프로그램이 이 계층에 적합한 예입니다. |
| **Premium** | 트랜잭션 양이 많은 경우에 적합하며 많은 동시 사용자를 지원하고 가장 높은 수준의 비즈니스 연속성 기능을 요구합니다. 예를 들어 업무 필수 응용 프로그램을 지원하는 데이터베이스가 이 계층에 적합합니다. |

>[AZURE.NOTE] Web 및 Business 버전은 사용이 중지되었습니다. Web 및 Business Edition을 계속 사용하려는 경우 [지원 종료 FAQ](https://azure.microsoft.com/pricing/details/sql-database/web-business/)를 잘 읽어 보세요.

## 독립 실행형 데이터베이스 서비스 계층 및 성능 수준
독립 실행형 데이터베이스의 경우 각 서비스 계층 내에는 여러 성능 수준이 있습니다. 워크로드 요구에 가장 잘 맞는 수준을 선택할 수 있는 유연성이 있습니다. 규모를 확장 또는 축소해야 하는 경우는 데이터베이스의 계층을 간편하게 변경할 수 있습니다. 자세한 내용은 [데이터베이스 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md)을 참조하세요.

여기에 나열된 성능 특성은 [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하여 만든 데이터베이스에 적용됩니다. 호스팅된 데이터베이스 수에 관계 없이, 데이터베이스는 보장된 리소스 집합을 가져오며 데이터베이스의 예상되는 성능 특징은 영향을 받지 않습니다.

[AZURE.INCLUDE [SQL DB 서비스 계층 테이블](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] 이 서비스 계층 테이블에서 다른 모든 행에 대한 자세한 설명은 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)을 참조하세요.

## 탄력적 풀 서비스 계층 및 eDTU의 성능
독립 실행형 데이터베이스를 만들고 규모를 확장하는 것 외에도 [탄력적 풀](sql-database-elastic-pool.md) 내에 복수의 데이터베이스를 관리하는 옵션이 있습니다. 탄력적 풀에 있는 모든 데이터베이스는 공통의 리소스 집합을 공유합니다. 성능 특성은 *탄력적 데이터베이스 트랜잭션 장치*(eDTU)에 의해 측정됩니다. 독립 실행형 데이터베이스의 경우와 마찬가지로 풀은 세 가지 서비스 계층 **Basic**, **Standard** 및 **Premium**으로 제공됩니다. 풀의 경우 이러한 세 가지 서비스 계층은 여전히 전체 성능 한계와 여러 기능을 정의합니다.

풀을 사용하여 데이터베이스가 풀 내의 각 데이터베이스에 특정 성능 수준을 할당할 필요 없이 DTU 리소스를 공유하고 사용할 수 있습니다. 예를 들어 표준 풀의 독립 실행형 데이터베이스에서 eDTU를 0개 사용하다가 풀을 구성할 때 설정한 최대 데이터베이스 eDTU 사용으로 전환할 수 있습니다. 풀을 사용하여 다양한 워크로드를 가진 복수의 데이터베이스에서 전체 풀에 사용 가능한 eDTU 리소스를 효율적으로 사용할 수 있습니다. 자세한 내용은 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

다음 표에서는 풀 서비스 계층의 특성을 설명합니다.

[AZURE.INCLUDE [탄력적 풀에 대한 SQL DB 서비스 계층 표](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

또한 풀 내의 각 데이터베이스는 해당 계층에 대한 독립 실행형 데이터베이스 특성을 준수합니다. 예를 들어 Basic 풀의 최대 세션 수 한계는 풀당 4800 ~ 28800개이지만, Basic 풀 내 개별 데이터베이스의 데이터베이스 세션 한계는 300개입니다.

## 서비스 계층 선택

서비스 계층을 결정하려면, 데이터베이스가 독립 실행형 데이터베이스이거나 탄력적 풀의 일부인지 확인함으로써 시작합니다.

### 독립 실행형 데이터베이스에 대한 서비스 계층 선택

독립 실행형 데이터베이스에 대한 서비스 계층을 결정하려면, SQL 데이터베이스 버전을 선택해야 하는 데이터베이스 기능을 확인함으로써 시작합니다.

- 데이터베이스 크기(성능 수준에 따라 기본 2GB, 표준 250GB 및 프리미엄 500GB-1TB)
- 데이터베이스 백업 보존 기간(기본 7일, 표준 35일 및 프리미엄 35일)

SQL 데이터베이스 버전을 결정하면 데이터베이스에 대한 성능 수준을 결정할 준비가 되었습니다(DTU의 수). 실제 경험을 토대로 추측한 다음 [동적으로 확장 또는 축소](sql-database-scale-up.md)할 수 있습니다. [DTU 계산기](http://dtucalculator.azurewebsites.net/)를 사용하여 필요한 DTU의 수를 대략적으로 예상할 수 있습니다.

### 탄력적 데이터베이스 풀에 대한 서비스 계층 선택

탄력적 데이터베이스 풀에 대한 서비스 계층을 결정하려면 풀에 대한 서비스 계층을 선택해야 하는 데이터베이스 기능을 확인함으로써 시작합니다.

- 데이터베이스 크기(기본 2GB, 표준 250GB 및 프리미엄 500GB)
- 데이터베이스 백업 보존 기간(기본 7일, 표준 35일 및 프리미엄 35일)
- 풀당 데이터베이스 수(기본 400, 표준 400 및 프리미엄 50)
- 풀당 최대 저장소(기본 117GB, 표준 1200GB 및 프리미엄 750GB)

풀에 대한 서비스 계층을 결정하면 풀에 대한 성능 수준을 결정할 준비가 되었습니다(eDTU). 실제 경험을 토대로 추측한 다음 [동적으로 강화 또는 축소](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool)할 수 있습니다. 풀에 대한 상한값을 결정하는 데 도움이 되도록 풀의 각 데이터베이스에 필요한 DTU의 수를 대략적으로 예상하기 위해 [DTU 계산기](http://dtucalculator.azurewebsites.net/)를 사용할 수 있습니다.

## 다음 단계
- [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)에서 이러한 계층의 가격에 대해 자세히 알아보세요.
- [탄력적 풀](sql-database-elastic-pool-guidance.md) 및 [탄력적 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)에 대해 자세히 알아봅니다.
- [탄력적 풀을 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)하고 [독립 실행형 데이터베이스의 성능을 모니터링](sql-database-single-database-monitor.md)하는 방법에 대해 알아봅니다.
- SQL 데이터베이스 계층에 대해 알아 보았으면 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 사용해보고 [첫 번째 SQL 데이터베이스를 만드는 방법](sql-database-get-started.md)에 대해 알아보세요.

## 추가 리소스

다중 테넌트 SaaS(software-as-a-service) 데이터베이스 응용 프로그램의 일반적인 데이터 아키텍처 패턴에 대한 정보는 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

<!-----HONumber=AcomDC_0921_2016-->