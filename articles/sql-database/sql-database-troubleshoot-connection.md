<properties
	pageTitle="현재 서버의 데이터베이스를 사용할 수 없습니다, SQL 데이터베이스에 연결하세요 | Microsoft Azure"
	description="응용 프로그램이 SQL 데이터베이스에 연결할 때 현재 서버의 데이터베이스를 사용할 수 없습니다 오류를 해결합니다."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""
	keywords="현재 서버의 데이터베이스를 사용할 수 없습니다, sql 데이터베이스에 연결하세요 | Microsoft Azure"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="daleche"/>

# SQL 데이터베이스에 연결할 때 “현재 서버의 데이터베이스를 사용할 수 없습니다” 오류
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

응용 프로그램이 Azure SQL 데이터베이스에 연결할 때 다음 오류 메시지가 표시됩니다.

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] 이 오류 메시지는 대개 일시적으로 나타납니다.

Azure 데이터베이스를 이동하거나 다시 구성하는 중이어서 SQL데이터베이스에 대한 응용 프로그램의 연결이 끊기면 이 오류가 발생합니다. SQL 데이터베이스 다시 구성 이벤트는 계획된 이벤트(예: 소프트웨어 업그레이드) 또는 계획되지 않은 이벤트(예: 프로세스 충돌 또는 부하 분산) 때문에 발생합니다. 대부분의 다시 구성 이벤트는 보통 일시적으로 발생하며 최대 60초 이내에 완료됩니다. 그러나 큰 트랜잭션으로 인해 복구가 오래 실행되는 등 이러한 이벤트가 완료되는 데 시간이 더 오래 걸리는 경우도 있습니다.

## 일시적인 연결 문제를 해결하는 단계
1.	[Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status)에서 응용 프로그램이 오류를 보고한 시간 동안 발생한 알려진 서비스 중단을 확인합니다.
2. Azure SQL 데이터베이스와 같이 클라우드 서비스에 연결하는 응용 프로그램에서는 주기적으로 다시 구성 이벤트가 발생하므로, 이러한 이벤트를 사용자에게 응용 프로그램 오류로 표시하는 대신 해당 오류를 처리하는 다시 시도 논리를 구현해야 합니다. 자세한 내용과 일반적인 다시 시도 전략을 확인하려면 [일시적인 오류](sql-database-connectivity-issues.md) 섹션과 [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md)에서 모범 사례 및 설계 지침을 검토하세요. 그런 다음 세부 사항은 [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)에서 코드 샘플을 참조하세요.
3.	데이터베이스가 리소스 한계에 도달하면 일시적인 연결 문제가 발생한 것처럼 보일 수 있습니다. [성능 문제 해결](sql-database-troubleshoot-performance.md)을 참조하세요.
4.	연결 문제가 계속 발생하거나 응용 프로그램에서 오류가 발생하는 기간이 60초를 초과하는 경우 또는 특정일에 오류가 여러 번 발생하는 경우에는 [Azure 지원](https://azure.microsoft.com/support/options) 사이트에서 **지원 받기**를 선택하여 Azure 지원 요청을 접수합니다.

## 다음 단계
- 다른 오류가 발생하는 경우에는 [오류 메시지](sql-database-develop-error-messages.md)를 점검하여 오류의 원인을 파악합니다.
- 문제가 지속되면 [Azure SQL 데이터베이스의 일반적인 연결 문제 해결](sql-database-troubleshoot-common-connection-issues.md)의 지침을 확인해 보세요.

<!---HONumber=AcomDC_0921_2016-->