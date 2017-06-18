<properties
	pageTitle="Azure 포털을 사용하여 Azure SQL 데이터베이스 복사 | Microsoft Azure"
	description="Azure SQL 데이터베이스 사본 만들기"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Azure 포털을 사용하여 Azure SQL 데이터베이스 복사

> [AZURE.SELECTOR]
- [개요](sql-database-copy.md)
- [Azure 포털](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

다음 단계에서는 [Azure 포털](https://portal.azure.com)을 통해 동일한 서버나 다른 서버에 SQL 데이터베이스를 복사하는 방법을 보여 줍니다.

SQL 데이터베이스를 복사하려면 다음 항목이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 복사할 SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.


## SQL 데이터베이스 복사

복사할 데이터베이스에 대한 SQL 데이터베이스 페이지를 엽니다.

1.	[Azure 포털](https://portal.azure.com)로 이동합니다.
2.	**더 많은 서비스** > **SQL 데이터베이스**를 클릭하고 원하는 데이터베이스를 클릭합니다.
3.	SQL 데이터베이스 페이지에서 **복사**를 클릭합니다.

    ![SQL 데이터베이스](./media/sql-database-copy-portal/sql-database-copy.png)

1.  **복사** 페이지에 기본 데이터베이스 이름이 제공됩니다. 원하는 경우 다른 이름을 입력합니다(서버에 있는 모든 데이터베이스 이름은 고유해야 함).
2.  **대상 서버**를 선택합니다. 대상 서버는 데이터베이스 복사본이 만들어지는 위치입니다. 동일한 서버 또는 다른 서버로 데이터베이스를 복사할 수 있습니다. 서버를 만들거나 목록에서 기존 서버 목록를 선택할 수 있습니다.
3.  **대상 서버**를 선택하면 **탄력적인 데이터베이스 풀** 및 **가격 책정 계층** 옵션이 사용되도록 설정됩니다. 서버에 풀이 있는 경우 데이터베이스를 복사할 수 있습니다.
3.  **확인**을 클릭하여 복사 프로세스를 시작합니다.

    ![SQL 데이터베이스](./media/sql-database-copy-portal/copy-page.png)


## 복사 작업 진행률 모니터링

- 복사 시작 후 세부 정보를 확인하려면 포털 알림을 클릭합니다.

    ![알림][3]
 
    ![모니터][4]


## 데이터베이스가 서버에서 라이브 상태인지 확인

- **더 많은 서비스** > **SQL 데이터베이스**를 클릭하고 새 데이터베이스가 **온라인** 상태인지 확인합니다.


## 로그인 확인

복사 작업이 완료된 후에 로그인을 확인하려면 [로그인 확인](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)을 참조하세요.


## 다음 단계

- Azure SQL 데이터베이스 복사에 대한 개요를 보려면 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요.
- PowerShell을 사용하여 데이터베이스를 복사하려면 [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell.md)를 참조하세요.
- Transact-SQL을 사용하여 데이터베이스를 복사하려면 [T-SQL을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-transact-sql.md)를 참조하세요.
- 다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.



## 추가 리소스

- [로그인 관리](sql-database-manage-logins.md)
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0921_2016-->