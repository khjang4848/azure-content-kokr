<properties
	pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 관리 | Microsoft Azure"
	description="PowerShell을 사용한 Azure SQL 데이터베이스 관리."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# PowerShell을 사용하여 Azure SQL 데이터베이스 관리


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-manage-portal.md)
- [Transact-SQL(SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

이 항목에서는 많은 Azure SQL Database 작업을 수행하는 데 사용되는 PowerShell cmdlet을 보여줍니다. 전체 목록은 [Azure SQL Database cmdlet](https://msdn.microsoft.com/library/mt574084.aspx)을 참조하세요.


## 리소스 그룹 만들기

[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx) cmdlet으로 SQL Database 및 관련 Azure 리소스에 대한 리소스 그룹을 만듭니다.

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 만들기](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)를 참조하세요.

## SQL Database 서버 만들기

[New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet을 사용하여 SQL Database 서버를 만듭니다. *server1*을 서버의 이름으로 바꿉니다. 서버 이름은 모든 Azure SQL Database 서버 간에 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 이 명령을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 리소스 그룹이 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

자세한 내용은 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 만들기](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)를 참조하세요.


## SQL Database 서버 방화벽 규칙 만들기

[New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) cmdlet을 사용하여 서버에 액세스하기 위한 방화벽 규칙을 만듭니다. 다음 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다. 리소스 그룹 및 서버가 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 만들고 `-StartIpAddress` 및 `-EndIpAddress`를 모두 **0.0.0.0**으로 설정합니다. 이렇게 하면 특별한 방화벽 규칙을 통해 서버에 대한 모든 Azure 트래픽 액세스가 허용됩니다.

자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 만들기](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)를 참조하세요.


## SQL 데이터베이스 만들기(비어 있음)

[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet을 사용하여 데이터베이스를 만듭니다. 리소스 그룹 및 서버가 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

자세한 내용은 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 만들기](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)를 참조하세요.


## SQL 데이터베이스의 성능 수준을 변경 합니다.

[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) cmdlet을 사용하여 데이터베이스 규모를 확장하거나 축소합니다. 리소스 그룹, 서버 및 데이터베이스가 구독에 이미 있어야 합니다. 기본 계층의 경우 `-RequestedServiceObjectiveName`을 단일 공백(예: 다음 코드 조각)으로 설정합니다. 이전의 다른 계층에 대한 예처럼 *S0*, *S1*, *P1*, *P6* 등으로 설정합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

자세한 내용은 [SQL Database 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해](sql-database-service-tiers.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 샘플 PowerShell 스크립트](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)를 참조하세요.

## 동일한 서버에 SQL 데이터베이스 복사

[New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx) cmdlet을 사용하여 SQL 데이터베이스를 동일한 서버에 복사합니다. `-CopyServerName` 및 `-CopyResourceGroupName`을 원본 데이터베이스 서버 및 리소스 그룹과 동일한 값으로 설정합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

자세한 내용은 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 복사](sql-database-copy-powershell.md#example-powershell-script)를 참조하세요.


## SQL 데이터베이스 삭제하기

[Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) cmdlet을 사용하여 SQL 데이터베이스를 삭제합니다. 리소스 그룹, 서버 및 데이터베이스가 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## SQL Database 서버 삭제

[Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) cmdlet을 사용하여 서버를 삭제합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## PowerShell을 사용하여 탄력적 데이터베이스 풀 만들기 및 관리

PowerShell을 사용하여 탄력적 데이터베이스 만들기에 대한 자세한 내용은 [PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)를 참조하세요.

PowerShell을 사용하여 탄력적 데이터베이스 풀 관리에 대한 자세한 내용은 [PowerShell을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-powershell.md)를 참조하세요.



## 관련 정보

- [Azure SQL 데이터베이스 cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->