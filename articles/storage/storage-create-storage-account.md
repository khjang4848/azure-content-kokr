<properties
	pageTitle="Azure 포털에서 저장소 계정을 만들거나, 관리하거나, 삭제하는 방법 | Microsoft Azure"
	description="Azure 포털에서 새 저장소 계정을 만들고 계정 액세스 키를 관리하거나 저장소 계정을 삭제합니다. 표준 및 프리미엄 저장소 계정에 대해 알아봅니다."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/26/2016"
	ms.author="micurd;robinsh"/>


# Azure 저장소 계정 정보

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## 개요

Azure 저장소 계정은 Azure 저장소 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다. 저장소 계정의 모든 개체는 그룹으로 합산 청구됩니다. 기본적으로 계정에 대한 데이터는 사용자 계정 소유자에만 사용할 수 있습니다.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## 저장소 계정 사용 비용

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Azure 가상 컴퓨터를 만드는 경우 배포 위치에 저장소 계정이 아직 없으면 해당 위치에서 자동으로 저장소 계정이 만들어집니다. 따라서 가상 컴퓨터 디스크에 대한 저장소 계정을 만들기 위해 아래 단계를 수행할 필요가 없습니다. 저장소 계정 이름은 가상 컴퓨터 이름을 기반으로 합니다. 자세한 내용은 [Azure 가상 컴퓨터 설명서](https://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

## 저장소 계정 끝점

Azure 저장소에 저장되는 모든 개체에는 고유한 URL 주소가 있습니다. 저장소 계정 이름은 해당 주소의 하위 도메인을 구성합니다. 하위 도메인과 도메인 이름의 조합은 각 서비스와 관련되며 저장소 계정의 *끝점* 을 구성합니다.

예를 들어 저장소 계정의 이름이 *mystorageaccount* 일 경우 저장소 계정의 기본 끝점은 다음과 같습니다.

- Blob 서비스: http://*mystorageaccount*.blob.core.windows.net

- 테이블 서비스: http://*mystorageaccount*.table.core.windows.net

- 큐 서비스: http://*mystorageaccount*.queue.core.windows.net

- 파일 서비스: http://*mystorageaccount*.file.core.windows.net

> [AZURE.NOTE] Blob 저장소 계정은 Blob 서비스 끝점만 노출합니다.

저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 끝점에 추가하여 작성됩니다. 예를 들어 Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

사용자 지정 도메인 이름을 구성하여 저장소 계정에서 사용할 수도 있습니다. 클래식 저장소 계정의 경우 자세한 내용은 [Blob 저장소 끝점에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)을 참조하세요. Resource Manager 저장소 계정의 경우 이 기능은 [Azure 포털](https://portal.azure.com)에 아직 추가되지 않았지만 PowerShell을 사용하여 구성할 수 있습니다. 자세한 내용은 [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) cmdlet을 참조하세요.

## 저장소 계정 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기** -> **데이터 + 저장소** -> **저장소 계정**을 선택합니다.

3. 저장소 계정의 이름을 입력합니다. 저장소 계정 이름을 Azure 저장소에서 개체를 처리하는 데 사용하는 방법에 대한 자세한 내용은 [저장소 계정 끝점](#storage-account-endpoints)을 참조하세요.

	> [AZURE.NOTE] 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
	>  
	> 저장소 계정 이름은 Azure 내에서 고유해야 합니다. 선택한 저장소 계정 이름이 이미 사용 중인 경우 Azure 포털에 표시됩니다.

4. 사용할 배포 모델을 **Resource Manager** 또는 **클래식**으로 지정합니다. **리소스 관리자**는 권장되는 배포 모델입니다. 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md)를 참조하세요.

	> [AZURE.NOTE] Blob 저장소 계정은 리소스 관리자 배포 모델을 사용해서만 만들 수 있습니다.

5. 저장소 계정 유형을 **범용** 또는 **Blob 저장소**로 선택합니다. **범용**이 기본값입니다.

*선호도 그룹* 은 Azure 저장소 계정을 사용하여 Azure 서비스와 VM을 지리적으로 그룹화한 것을 말합니다. 선호도 그룹은 동일한 데이터 센터 또는 대상 사용자 그룹 인근의 컴퓨터 작업을 찾아 서비스 성능을 향상시킬 수 있습니다. 또한 같은 선호도 그룹에 속한 다른 서비스에서 저장소 계정의 데이터에 액세스할 경우 송신 요금이 청구되지 않습니다.

	**Blob 저장소**를 선택한 경우 액세스 계층을 **핫** 또는 **쿨**로 지정합니다. 기본값은 **핫**입니다. 자세한 내용은 [Azure Blob 저장소: 쿨 및 핫 계층](storage-blob-storage-tiers.md)을 참조하세요.

6. 저장소 계정에 대한 복제 옵션을 **LRS**, **GRS**, **RA-GRS** 또는 **ZRS**로 선택합니다. 기본값은 **RA-GRS**입니다. Azure 저장소 복제 옵션에 대한 자세한 내용은 아래의 [Azure 저장소 복제](storage-redundancy.md)를 참조하세요.

7. 새 저장소 계정을 만들려는 구독을 선택합니다.

8. 새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

9. 저장소 계정에 대한 지리적 위치를 선택합니다. 각 지역에서 어떤 서비스가 가능한지에 대한 자세한 정보는 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

10. **만들기**를 클릭하여 저장소 계정을 만들 수 있습니다.

## 저장소 계정 관리

### 계정 구성 변경

저장소 계정을 만든 후에는 Blob 저장소 계정에 대한 액세스 계층 변경 또는 계정에 사용되는 복제 옵션 변경 등, 해당 구성을 수정할 수 있습니다. [Azure 포털](https://portal.azure.com)에서 저장소 계정으로 이동하고 **모든 설정**을 클릭한 다음 **구성**을 클릭하여 계정 구성을 보기 및/또는 변경합니다.

> [AZURE.NOTE] 저장소 계정을 만들 때 선택한 성능 계층에 따라 일부 복제 옵션을 사용하지 못할 수 있습니다.

복제 옵션을 변경하면 가격 책정이 변경됩니다. 자세한 내용은 [Azure 저장소 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지를 참조하세요.

Blob 저장소 계정의 경우 가격 책정 변경 외에도 액세스 계층을 변경하면 그에 따른 요금이 발생할 수 있습니다. 자세한 내용은 [Blob 저장소 계정 - 가격 책정 및 청구](storage-blob-storage-tiers.md#pricing-and-billing)를 참조하세요.

### 저장소 액세스 키 다시 관리

저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

> [AZURE.NOTE]저장소 액세스 키를 다른 사람과 공유하지 않는 것이 좋습니다. 액세스 키를 제공하지 않고 저장소 리소스에 대한 액세스를 허용하려는 경우에는 *공유 액세스 서명* 을 사용할 수 있습니다. 공유 액세스 서명에서는 정의된 간격으로 지정된 권한을 사용하여 계정의 리소스에 액세스할 수 있습니다. 자세한 내용은 [공유 액세스 서명 자습서](storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

#### 저장소 액세스 키 보기 및 복사

[Azure 포털](https://portal.azure.com)에서 저장소 계정으로 이동하고 **모든 설정**을 클릭한 다음 **선택키**를 클릭하여 계정 액세스 키를 보고, 복사하며 다시 생성합니다. 또한 **선택키** 블레이드는 응용 프로그램에서 사용하기 위해 복사할 수 있는 기본 및 보조 키를 사용하여 미리 구성된 연결 문자열을 포함합니다.

#### 저장소 액세스 키 다시 생성

저장소 연결을 안전하게 유지하는 데 도움이 되도록 정기적으로 저장소 계정의 액세스 키를 변경하는 것이 좋습니다. 두 개의 액세스 키가 할당되므로 하나의 액세스 키를 다시 생성하는 동안 다른 액세스 키를 사용하여 저장소 계정에 대한 연결을 유지할 수 있습니다.

> [AZURE.WARNING] 액세스 키를 다시 생성하면 저장소 계정에 종속된 모든 자체 응용 프로그램과 Azure의 서비스에 영향을 미칩니다. 액세스 키를 사용하여 저장소 계정에 액세스하는 모든 클라이언트에서 새 키를 사용하도록 업데이트해야 합니다.

**미디어 서비스** - 저장소 계정에 종속된 미디어 서비스가 있는 경우 키를 다시 생성한 후 미디어 서비스와 액세스 키를 다시 동기화해야 합니다.

**응용 프로그램** - 저장소 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 키를 다시 생성하면 연결이 끊어집니다.

**저장소 탐색기** - [저장소 탐색기 응용 프로그램](storage-explorers.md)을 사용할 경우 해당 응용 프로그램에서 사용하는 저장소 키를 업데이트해야 할 수 있습니다.

저장소 액세스 키를 회전하기 위한 프로세스는 다음과 같습니다.

1. 저장소 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 연결 문자열을 업데이트합니다.

2. 저장소 계정의 기본 액세스 키를 다시 생성합니다. **선택키** 블레이드에서 **키1 다시 생성**을 클릭하고 **예**를 클릭하여 새 키를 생성하려는 것을 확인합니다.

3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

4. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

## 저장소 계정 삭제

더 이상 사용하지 않는 저장소 계정을 제거하려면 [Azure 포털](https://portal.azure.com)의 저장소 계정으로 이동하여 **삭제**를 클릭합니다. 저장소 계정 삭제는 계정의 모든 데이터를 포함한 전체 계정을 삭제합니다.

> [AZURE.WARNING] 삭제된 저장소 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 또한 해당 계정의 리소스에 대해 true를 유지합니다. Blob, 테이블, 큐 또는 파일을 삭제하면 영구적으로 삭제됩니다.

Azure 가상 컴퓨터와 연결된 저장소 계정을 삭제하려면 먼저 가상 컴퓨터 디스크가 삭제되었는지 확인해야 합니다. 가상 컴퓨터 디스크를 먼저 삭제하지 않은 경우 저장소 계정을 삭제하려고 할 때 다음과 유사한 오류 메시지가 나타납니다.

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

저장소 계정이 클래식 배포 모델을 사용하는 경우 [Azure 포털](https://manage.windowsazure.com)에서 이러한 단계를 수행하여 가상 컴퓨터 디스크를 제거할 수 있습니다.

1. [클래식 Azure 포털](https://manage.windowsazure.com)로 이동합니다.
2. 가상 컴퓨터 탭으로 이동합니다.
3. 디스크 탭을 클릭합니다.
4. 데이터 디스크를 선택한 다음 디스크 삭제를 클릭합니다.
5. 디스크 이미지를 삭제하려면 이미지 탭으로 이동하여 계정에 저장된 모든 이미지를 삭제합니다.

자세한 내용은 [Azure 가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)를 참조하세요.

## 다음 단계

- [Azure Blob 저장소: 쿨 및 핫 계층](storage-blob-storage-tiers.md)
- [Azure 저장소 복제](storage-redundancy.md)
- [Azure 저장소 연결 문자열 구성](storage-configure-connection-string.md)
- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하세요.

<!---HONumber=AcomDC_0928_2016-->