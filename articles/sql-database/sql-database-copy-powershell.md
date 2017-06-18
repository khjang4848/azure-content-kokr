<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 복사 | Microsoft Azure" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스 사본 만들기" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/08/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 Azure SQL 데이터베이스 복사


> [AZURE.SELECTOR]
- [개요](sql-database-copy.md)
- [Azure 포털](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

이 문서에서는 PowerShell을 사용하여 동일한 서버나 다른 서버에 SQL 데이터베이스를 복사하거나 데이터베이스를 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)에 복사하는 방법을 보여줍니다. 데이터베이스 복사 작업에서는 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) cmdlet을 사용합니다.


이 문서를 완료하려면 다음이 필요합니다.

- Azure SQL 데이터베이스(복사할 데이터베이스). SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- 최신 버전의 Azure PowerShell. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.


SQL Database의 여러 새로운 기능은 [Azure Resource Manager 배포 모델](../resource-group-overview.md)을 사용할 때 지원되므로 예제는 Resource Manager에 [Azure SQL Database PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)을 사용합니다. 이전 버전과 호환성을 위해 기존 클래식 배포 모델 [Azure SQL Database(클래식) cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)이 지원되지만 Resource Manager cmdlet을 사용하는 것이 좋습니다.


>[AZURE.NOTE] 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.


## 동일한 서버에 SQL 데이터베이스 복사

동일한 서버에 복사본을 만들려면 `-CopyServerName` 매개 변수를 생략합니다(또는 동일한 서버로 설정).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## SQL 데이터베이스를 다른 서버에 복사

다른 서버에 복사본을 만들려면 `-CopyServerName` 매개 변수를 포함하고 다른 서버로 설정합니다. *복사본* 서버가 이미 존재해야 합니다. 다른 리소스 그룹에 있는 경우 `-CopyResourceGroupName` 매개 변수도 포함해야 합니다.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## SQL 데이터베이스를 탄력적 데이터베이스 풀에 복사

풀에서 SQL 데이터베이스의 복사본을 만들려면 `-ElasticPoolName` 매개 변수를 기존 풀로 설정합니다.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## 로그인 확인

복사 작업이 완료된 후에 로그인을 확인하려면 [로그인 확인](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)을 참조하세요.


## PowerShell 스크립트 예

다음 스크립트는 모든 리소스 그룹, 서버 및 풀이 이미 존재한다고 가정합니다(변수 값을 기존 리소스로 바꾸기). 데이터베이스 복사본을 제외한 모든 항목이 존재해야 합니다.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## 다음 단계

- Azure SQL 데이터베이스 복사에 대한 개요를 보려면 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요.
- Azure 포털을 사용하여 데이터베이스를 복사하려면 [Azure 포털을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-portal.md)를 참조하세요.
- Transact-SQL을 사용하여 데이터베이스를 복사하려면 [T-SQL을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-transact-sql.md)를 참조하세요.
- 다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.


## 추가 리소스

- [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [로그인 관리](sql-database-manage-logins.md)
- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database PowerShell Cmdlet 참조](https://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0914_2016-->