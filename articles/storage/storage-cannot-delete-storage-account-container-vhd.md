<properties
	pageTitle="Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결| Microsoft Azure"
	description="Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor="tysonn"
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;genli;robinsh"/>

# Azure 저장소 계정, 컨테이너 또는 VHD 삭제 문제 해결

## 요약
[Azure 포털](https://portal.azure.com/)이나 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 Azure 저장소 계정, 컨테이너 또는 VHD를 삭제하려고 하면 오류가 발생할 수 있습니다. 다음과 같은 상황으로 인해 문제가 발생할 수 있습니다.

-	VM을 삭제할 때 디스크와 VHD는 자동으로 삭제되지 않습니다. 따라서 저장소 계정 삭제 오류가 발생할 수 있습니다. 다른 VM을 마운트할 수 있도록 디스크는 삭제되지 않습니다.

-	즉, 디스크에 대한 임대 또는 디스크와 연결된 blob가 계속 남아 있을 수 있습니다.

Azure 문제를 이 문서에서 다루지 않은 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼을 방문하세요. 이러한 포럼이나 Twitter의 @AzureSupport에 문제를 게시할 수 있습니다. 또한 [Azure 지원](https://azure.microsoft.com/support/options/) 사이트에서 **지원 받기**를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

## 해결 방법
가장 일반적인 문제는 다음과 같은 방법으로 해결할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)로 전환합니다.
2. **가상 컴퓨터** > **디스크**를 선택합니다.

	![Azure 클래식 포털에 표시된 가상 컴퓨터의 디스크 이미지입니다.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 삭제하려는 저장소 계정, 컨테이너 또는 VHD와 연결된 디스크를 찾습니다. 디스크 위치를 확인하면 연결된 저장소 계정, 컨테이너 또는 VHD를 찾을 수 있습니다.

	![Azure 클래식 포털에서 디스크의 위치 정보를 보여주는 이미지](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. 디스크의 **다음에 연결됨** 필드에 나열된 VM이 없는지 확인한 후 디스크를 삭제합니다.

 	> [AZURE.NOTE] VM에 연결되어 있는 디스크는 삭제할 수 없습니다. 디스크는 삭제된 VM에서 비동기적으로 분리됩니다. 따라서 VM을 삭제한 후 이 필드가 지워질 때까지 몇 분 정도 걸릴 수 있습니다.

5. **가상 컴퓨터** > **이미지**를 선택한 다음 저장소 계정, 컨테이너 또는 VHD에 연결된 이미지를 삭제합니다.

	그런 다음 저장소 계정, 컨테이너 또는 VHD를 다시 삭제해 봅니다.

> [AZURE.WARNING] 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 삭제된 저장소 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 계정의 리소스도 마찬가지입니다. VHD, blob, 테이블, 큐 또는 파일은 삭제하는 경우 영구적으로 삭제됩니다. 리소스가 사용되고 있지 않은지 확인합니다.

## 일반적인 문제

다음 섹션에서는 Azure 저장소 계정, 컨테이너 또는 VHD를 삭제하려고 할 때 발생할 수 있는 일반적인 오류에 대해 설명합니다.

### 시나리오 1: 저장소 계정을 삭제할 수 없음

[Azure 포털](https://portal.azure.com/) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 저장소 계정으로 이동하여 **삭제**를 선택하면 다음 오류 메시지가 표시될 수 있습니다.

**Azure 포털**:

*저장소 계정 <vm-storage-account-name>을(를) 삭제하지 못했습니다. <vm-storage-account-name> 저장소 계정을 삭제할 수 없습니다. '저장소 계정 <vm-storage-account-name>에 일부 활성 이미지 및/또는 디스크가 있습니다. 이 저장소 계정을 삭제하려면 이러한 이미지 및/또는 디스크를 제거해야 합니다.'.*

**Azure 클래식 포털**:

*저장소 계정 <vm-storage-account-name>에 xxxxxxxxx- xxxxxxxxx-O-209490240936090599과(와) 같은 일부 활성 이미지 및/또는 디스크가 있습니다. 이 저장소 계정을 삭제하려면 이러한 이미지 및/또는 디스크를 제거해야 합니다.*

다음 오류도 표시될 수 있습니다.

**Azure 포털**:

*저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크 아티팩트를 포함하는 1 컨테이너가 있습니다. 이 저장소 계정을 삭제하려면 이미지 리포지토리에서 이러한 아티팩트를 제거해야 합니다*.

**Azure 클래식 포털**:

*제출 실패 저장소 계정 <vm-storage-account-name>에 활성 이미지 및/또는 디스크 아티팩트를 포함하는 1 컨테이너가 있습니다. 이 저장소 계정을 삭제하려면 이미지 리포지토리에서 이러한 아티팩트를 제거해야 합니다. 저장소 계정을 삭제하려고 하는데 해당 저장소 계정과 연결된 활성 디스크가 남아 있는 경우에는 남아 있는 활성 디스크를 삭제해야 한다는 내용의 메시지가 표시됩니다*.

### 시나리오 2: 컨테이너를 삭제할 수 없음

저장소 컨테이너를 삭제하려고 할 때 다음 오류가 표시될 수 있습니다.

*<container name> 저장소 컨테이너를 삭제하지 못했습니다. 오류: '현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다*.

### 시나리오 3: VHD를 삭제할 수 없음

VM을 삭제한후 연결된 VHD의 blob를 삭제하려고 하면 다음 메시지가 표시될 수 있습니다.

*'path/XXXXXX-XXXXXX-os-1447379084699.vhd' Blob을 삭제하지 못했습니다. 오류: '현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.*

## 중지됨(할당 취소됨) 상태 관련 정보

클래식 배포 모델에서 만든 후 보존된 VM은 [Azure 포털](https://portal.azure.com/) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **중지됨(할당 취소됨)** 상태입니다.

**Azure 클래식 포털**:

![Azure 포털에 표시된 VM의 중지됨(할당 취소됨) 상태입니다.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure 포털**:

![Azure 클래식 포털에 표시된 VM의 중지됨(할당 취소됨) 상태입니다.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

"중지됨(할당 취소됨)" 상태에서는 CPU, 메모리, 네트워크 등의 컴퓨터 리소스가 해제됩니다. 그러나 사용자가 필요 시 빠르게 VM을 다시 만들 수 있도록 디스크는 계속 보존됩니다. 이러한 디스크는 Azure 저장소에서 지원하는 VHD를 기반으로 만들어집니다. 저장소 계정에 이러한 VHD가 포함되어 있으며 디스크에는 이러한 VHD의 임대가 있습니다.

## 다음 단계

- [저장소 계정 삭제](storage-create-storage-account.md#delete-a-storage-account)
- [Microsoft Azure(PowerShell)에서 Blob 저장소의 임대 잠금을 해제하는 방법](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->