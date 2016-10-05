<properties 
    pageTitle="Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사 | Microsoft Azure" 
    description="Transact-SQL을 사용하여 Azure SQL 데이터베이스 사본 만들기" 
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


# Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사


> [AZURE.SELECTOR]
- [개요](sql-database-copy.md)
- [Azure 포털](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


다음 단계에서는 Transact-SQL을 사용하여 동일한 서버나 다른 서버에 SQL 데이터베이스를 복사하는 방법을 보여 줍니다. 데이터베이스 복사 작업에는 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 문이 사용됩니다.

이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx). SSMS가 없거나 이 문서에서 설명한 기능을 사용할 수 없는 경우 [최신 버전을 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)합니다.


## SQL 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 주 데이터베이스에 로그온합니다. 데이터베이스를 복사하려면 서버 수준 보안 주체가 아닌 로그인은 dbmanager 역할의 구성원이어야 합니다. 서버에 로그인 및 연결하는 방법에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 문으로 원본 데이터베이스 복사를 시작합니다. 이 문을 실행하면 데이터베이스 복사 프로세스가 시작됩니다. 데이터베이스 복사는 비동기 프로세스이므로 CREATE DATABASE 문은 데이터베이스가 복사를 완료하기 전에 반환됩니다.


### 동일한 서버에 SQL 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 주 데이터베이스에 로그온합니다. 데이터베이스를 복사하려면 서버 수준 보안 주체가 아닌 로그인은 dbmanager 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### SQL 데이터베이스를 다른 서버에 복사

새 데이터베이스를 만들 Azure SQL데이터베이스 서버인 대상 서버의 주 데이터베이스에 로그온합니다. 원본 Azure SQL 데이터베이스 서버에서 원본 데이터베이스의 데이터베이스 소유자(DBO)와 이름 및 암호가 같은 로그인을 사용합니다. 대상 서버의 로그인도 dbmanager 역할의 구성원이거나 서버 수준 보안 주체 로그인이어야 합니다.

이 명령은 server1의 Database1을, server2에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## 복사 작업 진행률 모니터링

sys.databases 및 sys.dm\_database\_copies 뷰 쿼리를 통해 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 COPYING으로 설정됩니다.


- 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 SUSPECT로 설정됩니다. 이 경우 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
- 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 ONLINE으로 설정됩니다. 이 경우 복사가 완료되었으며 새 데이터베이스가 일반 데이터베이스가 되어 원본 데이터베이스와는 독립적으로 변경 가능합니다.

> [AZURE.NOTE] 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 문을 실행합니다. 또는 원본 데이터베이스에서 DROP DATABASE 문을 실행해도 복사 프로세스가 취소됩니다.


## 복사 작업이 완료된 후 로그인 확인

대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://msdn.microsoft.com/library/ms175475.aspx)을 참조하세요. [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 되며 새 보안 식별자(SID)를 할당 받습니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자(DBO)인 복사를 시작한 로그인만 새 데이터베이스에 로그온할 수 있습니다.


## 다음 단계

- Azure SQL 데이터베이스 복사에 대한 개요를 보려면 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요.
- Azure 포털을 사용하여 데이터베이스를 복사하려면 [Azure 포털을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-portal.md)를 참조하세요.
- PowerShell을 사용하여 데이터베이스를 복사하려면 [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell.md)를 참조하세요.
- 다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.



## 추가 리소스

- [로그인 관리](sql-database-manage-logins.md)
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0921_2016-->