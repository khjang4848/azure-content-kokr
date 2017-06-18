<properties
   pageTitle="Azure Backup 자격 증명 모음 삭제 | Microsoft Azure"
   description="Azure Backup 자격 증명 모음을 삭제하는 방법입니다. 백업 자격 증명 모음을 삭제할 수 없는 문제 해결 "
   services="service-name"
   documentationCenter="dev-center-name"
   authors="markgalioto"
   manager="cfreeman"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/29/2016"
   ms.author="markgal;trinadhk"/>

# Azure Backup 자격 증명 모음 삭제

Azure Backup 서비스에는 Backup 자격 증명 모음과 Recovery Services 자격 증명 모음의 두 가지 자격 증명 모음 유형이 있습니다. Backup 자격 증명 모음을 먼저 제공했습니다. 그런 다음 Recovery Services 자격 증명 모음에서 확장된 Resource Manager 배포가 지원됩니다. 자격 증명 모음에 저장해야 하는 정보 종속성 및 확장된 기능으로 인해 Recovery Services 자격 증명 모음을 삭제하는 것이 저장하는 것보다 더 어려워 보일 수 있습니다.

|**배포 유형**|**포털**|**자격 증명 모음 이름**|
|--------------|----------|---------|
|클래식|클래식|백업 자격 증명 모음|
|리소스 관리자|Azure|복구 서비스 자격 증명 모음|


> [AZURE.NOTE] 백업 자격 증명 모음을 사용하여 Resource Manager에서 배포한 솔루션을 보호할 수는 없습니다. 그러나 Recovery Services 자격 증명 모음을 사용하면 클래식 방식으로 배포한 서버와 VM을 보호할 수는 있습니다.

이 문서에서는 자격 증명 모음이라는 용어를 사용하여 일반 형식의 Backup 자격 증명 모음 또는 Recovery Services 자격 증명 모음을 나타냅니다. 자격 증명 모음을 구분할 필요가 있을 경우 공식 이름(Backup 자격 증명 모음 또는 Recovery Services 자격 증명 모음)을 사용합니다.



## Recovery Services 자격 증명 모음 삭제

Recovery Services 자격 증명 모음을 삭제하는 것은 1단계 프로세스입니다. - *자격 증명 모음에 어떤 리소스도 포함되어 있지 않은 경우* Recovery Services 자격 증명 모음을 삭제하려면 먼저 자격 증명 모음의 모든 리소스를 제거하거나 삭제해야 합니다. 리소스를 포함하는 자격 증명 모음을 삭제하려면 다음 이미지와 같이 오류가 표시됩니다.

![자격 증명 모음 삭제 오류](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

자격 증명 모음에서 리소스를 지우기 전에는 **다시 시도**를 클릭해도 같은 오류가 표시됩니다. 오류 메시지에서 멈춰 있는 경우 **취소**를 클릭하고 아래 단계에 따라 Recovery Services 자격 증명 모음에 있는 리소스를 삭제합니다.


### VM을 보호하는 자격 증명 모음에서 항목 제거

Recovery Services 자격 증명 모음이 이미 열려 있는 경우 두 번째 단계를 건너뜁니다.

1.  Azure Portal을 열고 대시보드에서 삭제할 자격 증명 모음을 엽니다.

    허브 메뉴에서 대시보드에 고정된 Recovery Services 자격 증명 모음이 없는 경우 **서비스 더 보기**를 클릭하고 리소스 목록에서 **Recovery Services**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **복구 서비스 자격 증명 모음**을 클릭합니다.

    ![복구 서비스 자격 증명 모음 만들기 1단계](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다. 목록에서 삭제할 자격 증명 모음을 선택합니다.

    ![목록에서 자격 증명 모음 선택](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)

2. 자격 증명 모음 보기에서 **필수** 창을 확인합니다. 자격 증명 모음을 삭제하려면 보호된 항목이 없어야 합니다. 0이 아닌 숫자가 있는 경우 **백업 항목** 또는 **백업 관리 서버** 아래에서 해당 항목을 제거해야 자격 증명 모음을 삭제할 수 있습니다.

    ![보호된 항목에 대한 필수 창 확인](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    VM 및 파일/폴더는 백업 항목으로 간주되고 필수 창의 **백업 항목** 영역에 나열됩니다. DPM 서버는 필수 창의 **백업 관리 서버** 영역에 나열됩니다. **복제된 항목**은 Azure Site Recovery 서비스와 관계가 있습니다.

3. 자격 증명 모음에서 보호된 항목을 제거하기 시작하려면 자격 증명 모음에서 항목을 찾습니다. 자격 증명 모음 대시보드에서 **설정**을 클릭한 다음 **백업 항목**을 클릭하여 해당 블레이드를 엽니다.

    ![목록에서 자격 증명 모음 선택](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    **백업 항목** 블레이드에는 Azure Virtual Machines 또는 파일-폴더 등 항목 형식에 따라 별도의 목록이 있습니다(이미지 참조). 표시된 기본 항목 형식 목록은 Azure Virtual Machines입니다. 자격 증명 모음에서 파일-폴더 항목의 목록을 보려면 드롭다운 메뉴에서 **파일-폴더**를 선택합니다.

4. VM을 보호하는 자격 증명 모음에서 항목을 삭제하려면 먼저 항목의 백업 작업을 중지하고 복구 지점 데이터를 삭제해야 합니다. 자격 증명 모음의 각 항목에 대해 다음 단계를 따르세요.

    a. **백업 항목** 블레이드에서 해당 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 작업 중지](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    백업 중지 블레이드가 열립니다.

    b. **백업 중지** 블레이드의 **옵션 선택** 메뉴에서 **백업 데이터 삭제** > 항목 이름 입력을 선택하고 **백업 중지**를 클릭합니다.

      항목 이름을 입력하여 항목을 삭제할지 확인합니다. 중지할 항목을 확인하기 전에는 **백업 중지** 단추가 활성화되지 않습니다. 백업 항목의 이름을 입력할 대화 상자가 보이지 않으면 **백업 데이터 보관** 옵션을 선택한 경우입니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

      필요에 따라 데이터를 삭제하는 이유를 제공하고 메모를 추가할 수 있습니다. **백업 중지**를 클릭하면 삭제 작업이 완료되어 자격 증명 모음을 삭제할 수 있습니다. 작업이 완료되었는지 확인하려면 Azure 메시지 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png)를 확인합니다. <br/> 작업이 완료되면 백업 프로세스가 중지되고 해당 항목에 대한 백업 데이터가 삭제되었다는 메시지를 받게 됩니다.

    c. 목록에서 항목을 삭제한 후 **백업 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 나머지 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)

      목록에 항목이 없는 경우 Backup 자격 증명 모음 블레이드에서 **필수** 창을 스크롤합니다. 어떤 **백업 항목**, **백업 관리 서버** 또는 **복제된 항목**도 나열되지 않아야 합니다. 자격 증명 모음에 항목이 아직 나타날 경우 위의 3단계로 돌아가서 다른 항목 유형 목록을 선택합니다.

5. 자격 증명 모음 도구 모음에 더 이상 항목이 없을 때 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-vault.png)

6. 자격 증명 모음을 삭제할 것인지에 대한 확인 메시지가 나타나면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.


## 백업 프로세스를 중지했지만 데이터가 남아 있는 경우

백업 프로세스를 중지했지만 실수로 데이터가 *남아 있는* 경우 자격 증명 모음을 삭제하려면 먼저 백업 데이터를 삭제해야 합니다. 백업 데이터를 삭제하려면 다음을 수행합니다.

1. **백업 항목** 블레이드에서 항목을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **백업 데이터 삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **백업 데이터 삭제** 블레이드가 열립니다.

2. **백업 데이터 삭제** 블레이드에서 항목 이름을 입력하고 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-retained-vault.png)

    데이터를 삭제한 후 위의 4단계의 c로 이동하여 프로세스를 계속합니다.

## DPM 서버를 보호하는 데 사용되는 자격 증명 모음 삭제

DPM 서버를 보호하는 데 사용되는 자격 증명 모음을 삭제하려면 먼저 만들어진 모든 복구 지점을 지운 다음 자격 증명 모음에서 서버 등록을 취소해야 합니다.

보호 그룹과 연결된 데이터를 삭제하려면 다음을 수행합니다.

1. DPM 관리자 콘솔에서 **보호**를 클릭하고, 보호 그룹을 선택하고, 보호 그룹 구성원을 선택하고, 도구 리본에서 **제거**를 클릭합니다. 도구 리본에 표시하도록 **제거** 단추에 대한 구성원을 선택해야 합니다. 예제에서 구성원은 **dummyvm9**입니다. 보호 그룹에 여러 구성원이 있는 경우 Ctrl 키를 누른 채로 여러 멤버를 선택합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    **보호 중지** 대화 상자가 열립니다.

2. **보호 중지** 대화 상자에서 **보호된 데이터 삭제**를 선택하고 **보호 중지**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    보호된 데이터를 삭제하려면 자격 증명 모음을 지워야 하므로 보호된 데이터를 보관하지 않습니다. 보호 그룹의 복구 지점 수와 데이터의 양에 따라 데이터를 삭제하는 데 수 초에서 수 분 정도 걸릴 수 있습니다. 작업이 완료되면 **보호 중지** 대화 상자에 상태가 표시됩니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/success-deleting-protection-group.png)

3. 모든 보호 그룹의 모든 멤버에 대해 이 프로세스를 계속합니다.

    모든 보호된 데이터와 보호 그룹을 제거해야 합니다.

4. 보호 그룹에서 모든 구성원을 삭제한 후 Azure Portal로 전환합니다. 자격 증명 모음 대시보드를 열고 **백업 항목**, **백업 관리 서버** 또는 **복제된 항목**이 없는지 확인합니다. 자격 증명 모음 도구 모음에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-vault.png)

    자격 증명 모음에 등록된 Backup 관리 서버가 있는 경우 자격 증명 모음에 데이터가 없더라도 자격 증명 모음을 삭제할 수 없습니다. 자격 증명 모음과 연결된 Backup 관리 서버를 삭제했지만 **필수** 창에 여전히 서버가 표시되는 경우 [자격 증명 모음에 등록된 백업 관리 서버 찾기](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault)를 참조하세요.

5. 자격 증명 모음을 삭제할 것인지에 대한 확인 메시지가 나타나면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.


## 프로덕션 서버를 보호하는 데 사용되는 자격 증명 모음 삭제

프로덕션 서버를 보호하는 데 사용되는 자격 증명 모음을 삭제하려면 먼저 자격 증명 모음에서 서버를 삭제하거나 등록을 취소해야 합니다.

자격 증명 모음과 연결된 프로덕션 서버를 삭제하려면

1. Azure Portal에서 자격 증명 모음 대시보드를 열고 **설정** > **백업 인프라** > **프로덕션 서버**를 클릭합니다.

    ![프로덕션 서버 블레이드 열기](./media/backup-azure-delete-vault/delete-production-server.png)

    **프로덕션 서버** 블레이드가 열리고 자격 증명 모음에 모든 프로덕션 서버가 나열됩니다.

    ![프로덕션 서버 목록](./media/backup-azure-delete-vault/list-of-production-servers.png)

2. **프로덕션 서버** 블레이드에서 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

    ![프로덕션 서버 삭제](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    **삭제** 블레이드가 열립니다.

    ![프로덕션 서버 삭제](./media/backup-azure-delete-vault/delete-blade.png)

3. **삭제** 블레이드에서 삭제할 서버 이름을 확인하고 **삭제**를 클릭합니다. **삭제** 단추를 활성화하려면 서버 이름을 올바르게 입력해야 합니다.

    자격 증명 모음이 삭제되면 자격 증명 모음이 삭제되었다는 메시지를 받습니다. 자격 증명 모음에서 모든 서버를 삭제한 후 자격 증명 모음 대시보드의 필수 창으로 다시 스크롤합니다.

4. 자격 증명 모음 대시보드에서 **백업 항목**, **백업 관리 서버** 또는 **복제된 항목**이 없는지 확인합니다. 자격 증명 모음 도구 모음에서 **삭제**를 클릭합니다.

5. 자격 증명 모음을 삭제할 것인지에 대한 확인 메시지가 나타나면 **예**를 클릭합니다.

    자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.


## Backup 자격 증명 모음 삭제

다음은 클래식 포털에서 Backup 자격 증명 모음을 삭제하기 위한 지침입니다. Backup 자격 증명 모음과 Recovery Services 자격 증명 모음은 동일합니다. 자격 증명 모음을 삭제하려면 먼저 항목 및 보관된 데이터를 삭제해야 합니다.

1. 클래식 포털을 엽니다.

2. 백업 자격 증명 모음 목록에서 삭제할 자격 증명 모음을 선택합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    자격 증명 모음 대시보드가 열립니다. 자격 증명 모음과 연결된 Windows 서버 및/또는 Azure Virtual Machines의 수를 확인합니다. 또한 Azure에서 사용된 총 저장소를 살펴봅니다. 자격 증명 모음을 삭제하기 전에 모든 백업 작업을 중지하고 기존 데이터를 삭제해야 합니다.

3. **보호된 항목** 탭을 클릭한 다음 **보호 중지**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    **'자격 증명 모음' 보호 중지** 대화 상자가 나타납니다.

4. **'자격 증명 모음' 보호 중지** 대화 상자에서 **연결된 백업 데이터 삭제**를 선택하고 ![확인 표시](./media/backup-azure-delete-vault/checkmark.png)를 클릭합니다. <br/> 필요에 따라 보호를 중지하는 이유를 선택하고 메모를 제공할 수 있습니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    자격 증명 모음에서 항목을 삭제한 후 자격 증명 모음을 비워 둡니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)

5. 탭 목록에서 **등록된 항목**을 클릭합니다. 자격 증명 모음에 등록된 각 항목에 대해 항목을 선택하고 **등록 취소**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-unregister.png)

6. 탭 목록에서 **대시보드**를 클릭하여 해당 탭을 엽니다. 클라우드에 보호된 Azure Virtual Machines 또는 등록된 서버가 없는지 확인합니다. 또한 저장소에 데이터가 없는지도 확인합니다. **삭제**를 클릭하여 자격 증명 모음을 삭제합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    Backup 자격 증명 모음 삭제 확인 화면이 열립니다. 자격 증명 모음을 삭제하는 이유에 대한 옵션을 선택하고 ![확인 표시](./media/backup-azure-delete-vault/checkmark.png)를 클릭합니다. <br/>

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    자격 증명 모음이 삭제되면 클래식 포털 대시보드로 돌아갑니다.

### 자격 증명 모음에 등록된 Backup 관리 서버 찾기

자격 증명 모음에 여러 서버가 등록되어 있는 경우 기억하기 어려울 수 있습니다. 자격 증명 모음에 등록된 서버를 확인하고 삭제하려면

1. 자격 증명 모음 대시보드를 엽니다.

2. **필수** 창에서 **설정**을 클릭하여 해당 블레이드를 엽니다.

    ![설정 블레이드 열기](./media/backup-azure-delete-vault/backup-vault-click-settings.png)

3. **설정 블레이드**에서 **백업 인프라**를 클릭합니다.

4. **백업 인프라** 블레이드에서 **백업 관리 서버**를 클릭합니다. Backup 관리 서버 블레이드가 열립니다.

    ![백업 관리 서버 목록](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)

5. 목록에서 서버를 삭제하려면 서버 이름을 마우스 오른쪽 단추로 클릭한 다음 **삭제**를 클릭합니다. **삭제** 블레이드가 열립니다.

6. **삭제** 블레이드에서 서버 이름이 제공됩니다. 이름이 긴 경우 Backup 관리 서버 목록에서 복사하고 붙여넣을 수 있습니다. 그런 다음 **삭제**를 클릭합니다.

<!---HONumber=AcomDC_0914_2016-->