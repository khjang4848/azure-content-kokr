<properties
	pageTitle="PowerShell을 사용하여 앱 서비스 앱 백업 및 복원"
	description="Azure 앱 서비스에서 PowerShell을 사용하여 앱을 백업하고 복원하는 방법 알아보기"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>
# PowerShell을 사용하여 앱 서비스 앱 백업 및 복원

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [REST API](../app-service-web/websites-csm-backup.md)

Azure PowerShell을 사용하여 [앱 서비스 앱](https://azure.microsoft.com/services/app-service/web/)을 백업 및 복원하는 방법에 대해 알아봅니다. 요구 사항 및 제한 사항을 포함한 웹앱 백업에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 백업](../app-service-web/web-sites-backup.md)을 참조하세요.

## 필수 조건
PowerShell을 사용하여 앱 백업을 관리하려면 다음이 필요합니다.

- **SAS URL**을 통해 Azure 저장소 컨테이너에 대한 액세스를 읽고 쓸 수 있습니다. SAS URL에 대한 설명은 [SAS 모델 이해](../storage/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요. PowerShell을 사용하여 Azure Storage를 관리하는 예제는 [Azure Storage에서 Azure PowerShell 사용](../storage/storage-powershell-guide-full.md)을 참조하세요.
- 웹앱과 함께 데이터베이스를 백업하려는 경우 **데이터베이스 연결 문자열**.

### 웹앱 백업 cmdlet에서 사용할 SAS URL을 생성하는 방법
PowerShell을 사용하여 SAS URL을 생성할 수 있습니다. 이 문서에서 설명하는 cmdlet에서 사용할 수 있는 SAS URL을 생성하는 방법에 대한 예제는 다음과 같습니다.

		$storageAccountName = "<your storage account's name>"
		$storageAccountRg = "<your storage account's resource group>"

		# This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

		$blobContainerName = "<name of blob container for app backups>"
		$sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## Azure PowerShell 1.3.2 이상 설치

Azure PowerShell 설치 및 사용에 대한 지침은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-install-configure.md)을 참조하세요.

## 백업 만들기

New-AzureRmWebAppBackup cmdlet을 사용하여 웹앱의 백업을 만듭니다.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

자동으로 생성된 이름으로 백업을 만듭니다. 백업에 대한 이름을 제공하려는 경우 BackupName 선택적 매개 변수를 사용합니다.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

백업에 데이터베이스를 포함하려면 먼저 New-AzureRmWebAppDatabaseBackupSetting cmdlet을 사용하여 데이터베이스 백업 설정을 만든 다음 New-AzureRmWebAppBackup cmdlet의 Databases 매개 변수에서 해당 설정을 제공합니다. Databases 매개 변수는 둘 이상의 데이터베이스를 백업할 수 있도록 데이터베이스 설정의 배열을 수락합니다.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## 백업 가져오기

Get-AzureRmWebAppBackupList cmdlet은 웹앱에 대한 모든 백업의 배열을 반환합니다. 웹앱 및 해당 리소스 그룹의 이름을 제공해야 합니다.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

특정 백업을 가져오려면 Get-AzureRmWebAppBackup cmdlet을 사용합니다.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

또한 편의를 위해 백업 관리 cmdlet으로 웹앱 개체를 파이프할 수도 있습니다.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## 자동 백업 예약

지정된 간격으로 자동으로 실행되도록 백업을 예약할 수 있습니다. 백업 일정을 구성하려면 Edit-AzureRmWebAppBackupConfiguration cmdlet을 사용합니다. 이 cmdlet은 여러 매개 변수를 사용합니다.

- **Name** - 웹앱의 이름
- **ResourceGroupName** – 웹앱이 포함된 리소스 그룹의 이름
- **Slot** - 선택 사항. 웹앱 슬롯의 이름.
- **StorageAccountUrl** - 백업을 저장하는 데 사용되는 Azure Storage 컨테이너에 대한 SAS URL
- **FrequencyInterval** - 백업을 생성해야 하는 빈도에 대한 숫자 값. 양의 정수여야 합니다.
- **FrequencyUnit** - 백업을 생성해야 하는 빈도에 대한 시간 단위. 옵션은 Hour 및 Day.
- **RetentionPeriodInDays** - 자동으로 삭제되기 전에 자동 백업을 저장해야 하는 일 수
- **StartTime** - 선택 사항. 자동 백업이 시작되어야 하는 시간. 이 필드가 null인 경우 백업이 즉시 시작됩니다. DateTime이어야 합니다.
- **Databases** - 선택 사항. 백업할 데이터베이스에 대한 DatabaseBackupSettings의 배열.
- **KeepAtLeastOneBackup** - 선택적 전환된 매개 변수. 기간에 관계 없이 백업을 저장소 계정에 항상 유지해야 하는 경우 이를 제공합니다.

다음은 이 cmdlet을 사용하는 방법에 대한 예입니다.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

현재 백업 일정을 가져오려면 Get-AzureRmWebAppBackupConfiguration cmdlet을 사용합니다. 이미 구성된 일정을 수정하는 데 유용할 수 있습니다.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## 백업으로 웹앱 복원

웹앱을 백업에서 복원하려면 Restore-AzureRmWebAppBackup cmdlet을 사용합니다. 이 cmdlet을 사용하는 가장 쉬운 방법은 Get-AzureRmWebAppBackup cmdlet 또는 Get-AzureRmWebAppBackupList cmdlet에서 검색된 백업 개체에 파이프하는 것입니다.

백업 개체가 있으면 Restore-AzureRmWebAppBackup cmdlet으로 파이프할 수 있습니다. Overwrite 스위치 매개 변수를 지정하여 백업의 콘텐츠로 웹앱의 콘텐츠를 덮어쓰려는 것을 표시합니다. 백업이 데이터베이스를 포함하는 경우 해당 데이터베이스도 복원됩니다.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

다음은 모든 매개 변수를 지정하여 Restore-AzureRmWebAppBackup을 사용하는 방법의 예입니다.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## 백업 삭제

백업을 삭제하려면 Remove-AzureRmWebAppBackup cmdlet을 사용합니다. 저장소 계정에서 백업이 제거됩니다. 앱 이름, 해당 리소스 그룹 및 삭제하려는 백업의 ID를 지정합니다.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Remove-AzureRmWebAppBackup cmdlet으로 백업 개체를 파이프하여 삭제할 수도 있습니다.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0921_2016-->