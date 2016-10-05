<properties
	pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드 | Microsoft Azure"
	description="Web 및 Business 데이터베이스를 업그레이드하는 방법을 포함하여 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명하며 PowerShell을 사용하여 V11 서버 업그레이드를 통해 데이터베이스를 탄력적 데이터베이스 풀로 직접 마이그레이션하는 방법을 설명합니다."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="sstein"/>

# PowerShell을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL 데이터베이스 V12가 최신 버전이므로 SQL 데이터베이스 V12로 업그레이드하는 것이 좋습니다. SQL 데이터베이스 V12에는 [이전 버전에 비해 다양한 이점](sql-database-v12-whats-new.md)이 있습니다.

- SQL Server와의 호환성 증가
- 향상된 프리미엄 성능과 새로운 성능 수준
- [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)

이 문서에서는 기존 SQL 데이터베이스 V11 서버 및 데이터베이스를 SQL 데이터베이스 V12로 업그레이드하는 방법에 대해 지침을 제공합니다.

V12로 업그레이드하는 과정에서 Web/Business 데이터베이스를 새로운 서비스 계층으로 업데이트하게 됩니다. 따라서 Web/Business 데이터베이스 업그레이드에 대한 지침도 포함되어 있습니다.

또한 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)로 마이그레이션하는 것이 단일 데이터베이스에 대한 개별 성능 수준(가격 책정 계층)으로 업그레이드하는 것보다 비용 면에서 효율적일 수 있습니다. 개별 데이터베이스의 성능 수준을 개별적으로 관리하지 않고 풀의 성능 설정만 관리하면 되므로, 풀은 데이터베이스 관리를 간소화합니다. 여러 서버에 데이터베이스가 있는 경우에는 이 데이터베이스를 동일한 서버로 이동해서 풀로 유용하게 관리하는 것이 좋습니다.

이 문서의 단계를 따르면 손쉽게 V11 서버에서 탄력적 데이터베이스 풀로 직접 데이터베이스를 자동 마이그레이션할 수 있습니다.

데이터베이스는 업그레이드 작업 동안 온라인 상태로 유지되며 계속 작동합니다. 실제로 새 성능 수준으로 전환할 때 아주 짧은 시간(보통 90초 정도이지만 최대 5분일 수도 있음) 동안 데이터베이스 연결이 일시적으로 끊어질 수 있습니다. 응용 프로그램에 [연결 종료에 대한 일시적인 오류 처리](sql-database-connectivity-issues.md) 기능이 있을 경우 업그레이드 종료 시의 연결 끊김을 방지할 수 있습니다.

SQL 데이터베이스 V12로 업그레이드하고 난 후에는 취소할 수 없습니다. 업그레이드 후에는 서버를 V11로 되돌릴 수 없습니다.

V12로 업그레이드 한 직후에는 서비스가 새 서버의 워크로드를 평가할 때까지 일정 시간 동안 [서비스 계층 권장 사항](sql-database-service-tier-advisor.md) 및 [탄력적 풀 권장 사항](sql-database-elastic-pool-create-portal.md)을 사용할 수 없습니다. V11 서버 권장 사항 기록은 유지되지 않으므로 V12 서버에 적용되지 않습니다.

## 업그레이드 준비

- **모든 Web 및 Business 데이터베이스 업그레이드**: 아래의 [모든 Web 및 Business 데이터베이스 업그레이드](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) 섹션을 참조하거나 [PowerShell을 사용하여 데이터베이스 및 서버 업그레이드](sql-database-upgrade-server-powershell.md)를 수행하세요.
- **지역에서 복제 검토 및 일시 중단**: Azure SQL 데이터베이스가 지역에서 복제가 가능하도록 구성된 경우 데이터베이스의 현재 구성을 문서에 기록해 놓고 [지역에서 복제를 중지](sql-database-geo-replication-portal.md#remove-secondary-database)해야 합니다. 업그레이드가 완료된 후 지역에서 복제 데이터베이스를 다시 구성하세요.
- **Azure VM에 클라이언트가 있는 경우 열어야 하는 포트**: Azure VM(가상 컴퓨터)에서 클라이언트가 실행되면서 클라이언트 프로그램이 SQL 데이터베이스 V12에 연결하는 경우에는 VM에서 11000-11999 및 14000-14999 범위의 포트를 열어야 합니다. 자세한 내용은 [SQL 데이터베이스 V12용 포트](sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.


## 필수 조건

PowerShell을 사용하여 서버를 V12로 업그레이드하려면 최신 Azure PowerShell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.


## 자격 증명 구성 및 구독 선택

Azure 구독에 대한 PowerShell cmdlet을 실행 하려면 먼저 Azure 계정에 대한 액세스를 설정 해야 합니다. 다음을 실행하면 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

	Add-AzureRmAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.

사용할 구독을 선택하려면 구독 ID(**-SubscriptionId**) 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이전 단계에서 해당 정보를 복사하거나, 구독이 여러 개일 경우 **Get-AzureRmSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다.

현재 구독을 설정 하 여 구독 정보로 다음 cmdlet을 실행 합니다.

	Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

다음 명령은 위에서 방금 선택한 구독을 실행합니다.

## 권장 사항 가져오기

서버 업그레이드에 대한 권장 사항을 가져오려면 다음 cmdlet을 실행합니다.

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

자세한 내용은 [탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-portal.md) 및 [Azure SQL 데이터베이스 가격 책정 계층 권장 사항](sql-database-service-tier-advisor.md)을 참조하세요.



## 업그레이드 시작

서버 업그레이드를 시작하려면 다음 cmdlet을 실행합니다.

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


이 명령을 실행하면 업그레이드 프로세스가 시작됩니다. 권장 사항의 출력을 사용자 지정한 다음 편집된 권장 사항을 이 cmdlet에 제공할 수 있습니다.


## 서버 업그레이드


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## 사용자 지정 업그레이드 매핑

권장 사항이 해당 서버 및 비즈니스 사례에 적합하지 않은 경우 데이터베이스 업그레이드 방법을 선택하고 단일 또는 탄력적 데이터베이스에 매핑할 수 있습니다.

ElasticPoolCollection 및 DatabaseCollection 매개 변수는 선택적임:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## SQL 데이터베이스 V12로 업그레이드한 후 데이터베이스 모니터링


업그레이드한 후 데이터베이스를 적극적으로 모니터링하여 원하는 성능으로 응용 프로그램이 실행되고 있는지 확인하고 필요에 따라 사용을 최적화하는 것이 좋습니다.

개별 데이터베이스를 모니터링하는 것 외에, [포털을 사용](sql-database-elastic-pool-manage-portal.md)하거나 [PowerShell](sql-database-elastic-pool-manage-powershell.md)을 사용하여 탄력적 데이터베이스 풀을 모니터링할 수도 있습니다.


**리소스 소비 데이터:** 기본, 표준 및 프리미엄 데이터베이스의 경우 사용자 데이터베이스에서 [sys.dm_ db_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV를 통해 리소스 소비 데이터를 사용할 수 있습니다. 이 DMV는 지난 1시간 동안의 작업에 대해 15초 간격으로 거의 실시간 리소스 사용량 정보를 제공합니다. 특정 간격의 DTU 사용률은 CPU, IO 및 로그 차원의 최대 사용률로 계산됩니다. 다음은 지난 1시간 동안의 평균 DTU 사용률을 계산하는 쿼리입니다.

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

추가 모니터링 정보:

- [단일 데이터베이스의 Azure SQL 데이터베이스 성능 지침](http://msdn.microsoft.com/library/azure/dn369873.aspx)
- [탄력적 데이터베이스 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)
- [동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링](sql-database-monitoring-with-dmvs.md)



**경고:** 업그레이드된 데이터베이스의 DTU 사용량이 지정된 높은 수준에 도달하면 알리도록 Azure 포털에서 '경고'를 설정합니다. 데이터베이스 경고는 DTU, CPU, IO 및 로그와 같은 다양한 성능 메트릭에 대해 Azure 포털에서 설정할 수 있습니다. 데이터베이스로 이동한 후 **설정** 블레이드에서 **경고 규칙**을 선택하면 됩니다.

예를 들어 평균 DTU 백분율 값이 최근 5분 동안 75%를 초과할 경우 "DTU 백분율"에 대해 메일 경고를 설정할 수 있습니다. 경고 알림을 구성하는 방법에 대한 내용은 [경고 알림 받기](../azure-portal/insights-receive-alert-notifications.md)를 참조하세요.



## 다음 단계

- [탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-portal.md) 및 이 풀에 일부 또는 모든 데이터베이스 추가
- [데이터베이스의 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md)



## 관련 정보

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=AcomDC_0921_2016-->