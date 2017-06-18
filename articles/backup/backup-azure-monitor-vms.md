<properties
   pageTitle="Resource Manager로 배포된 가상 컴퓨터 백업 모니터링 | Microsoft Azure"
   description="Resource Manager로 배포된 가상 컴퓨터 백업에서 이벤트 및 경고를 모니터링합니다. 경고에 기반한 전자 메일을 보냅니다."
   services="backup"
   documentationCenter="dev-center-name"
   authors="markgalioto"
   manager="cfreeman"
   editor=""/>

<tags
ms.service="backup"
ms.workload="storage-backup-recovery"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/25/2016"
ms.author="trinadhk; giridham;"/>

# Azure 가상 컴퓨터 백업에 대한 경고 모니터링

경고는 이벤트 임계값을 만족하거나 초과한 서비스에서 나오는 응답입니다. 문제가 시작되는 시기를 아는 것은 비즈니스 비용을 낮게 유지하는 데 중요할 수 있습니다. 일반적으로 경고는 일정에 따라 발생하지 않으므로 경고가 발생한 후 가능한 빨리 아는 것이 유용합니다. 예를 들어 백업 또는 복원 작업이 실패할 경우 실패의 5분 내에서 경고가 발생합니다. 자격 증명 모음 대시보드의 백업 경고 타일에 중요 및 경고 수준 이벤트를 표시합니다. 백업 경고 설정에서 모든 이벤트를 볼 수 있습니다. 그러나 별개의 문제에 대해 작업하는 경우 경고가 발생하면 어떻게 하나요? 경고가 발생하는 시기를 모르면 조금 불편할 수도 있고 데이터를 손상시킬 수도 있습니다. 정확한 담당자가 경고를 인식하게 하려면 경고가 발생할 때 전자 메일을 통해 경고 알림을 보내도록 서비스를 구성합니다. 전자 메일 알림 설정에 대한 자세한 내용은 [알림 구성](backup-azure-monitor-vms.md#configure-notifications)을 참조하세요.

## 경고에 대한 정보를 찾으려면 어떻게 해야 합니까?

경고를 발생시킨 이벤트에 관한 정보를 보려면 백업 경고 블레이드를 열어야 합니다. 두 가지 방법으로: 즉, 자격 증명 모음 대시보드의 백업 경고 타일에서 또는 경고 및 이벤트 블레이드에서 백업 경고 블레이드를 열 수 있습니다.

백업 경고 타일에서 백업 경고 블레이드를 열려면:

- 자격 증명 모음 대시보드의 **백업 경고** 타일에서 **중요** 또는 **경고**를 클릭하여 해당 심각도 수준에 대해 작동할 수 있는 이벤트를 봅니다.

    ![백업 경고 타일](./media/backup-azure-monitor-vms/backup-alerts-tile.png)


경고 및 이벤트 블레이드에서 백업 경고 블레이드를 열려면:

1. 자격 증명 모음 대시보드에서 **모든 설정**을 클릭합니다. ![모든 설정 단추](./media/backup-azure-monitor-vms/all-settings-button.png)

2. **설정** 블레이드에서 **경고 및 이벤트**를 클릭합니다. ![경고 및 이벤트 단추](./media/backup-azure-monitor-vms/alerts-and-events-button.png)

3. **경고 및 이벤트** 블레이드에서 **백업 경고**를 클릭합니다. ![백업 경고 단추](./media/backup-azure-monitor-vms/backup-alerts.png)

    **백업 경고** 블레이드가 열리고 필터링된 경고가 표시됩니다.

    ![백업 경고 타일](./media/backup-azure-monitor-vms/backup-alerts-critical.png)

4. 특정 경고에 관한 세부 정보를 보려면 이벤트 목록에서 경고를 클릭하여 해당 **세부 정보** 블레이드를 엽니다.

    ![이벤트 세부 정보](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    목록에 표시된 특성을 사용자 지정하려면 [추가 이벤트 특성 보기](backup-azure-monitor-vms.md#view-additional-event-attributes) 참조

## 알림 구성

 지난 한 시간 동안 발생한 경고에 대해 또는 특정 유형의 이벤트가 발생할 때 전자 메일 알림을 보내도록 서비스를 구성할 수 있습니다.

경고에 대한 전자 메일 알림을 설정하려면

1. 백업 경고 메뉴에서 **알림 구성**을 클릭

    ![백업 경고 메뉴](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    알림 구성 블레이드가 열립니다.

    ![알림 구성 블레이드](./media/backup-azure-monitor-vms/configure-notifications.png)

2. 알림 구성 블레이드에서 전자 메일 알림에 대해 **켜기**를 클릭합니다.

    받는 사람 및 심각도 대화 상자에는 해당 정보가 필수이기 때문에 별표가 표시됩니다. 최소 하나 이상의 전자 메일 주소를 제공하고 최소 하나 이상의 심각도를 선택합니다.

3. **받는 사람(전자 메일)** 대화 상자에 알림을 받을 전자 메일 주소를 입력합니다. 사용할 형식: username@domainname.com. 세미콜론(;)을 사용하여 여러 전자 메일 주소를 구분합니다.

4. **알림** 영역에서 **경고별**을 선택하여 지정된 경고가 발생하면 알림을 보내거나 **시간별 요약**을 선택하여 지난 한 시간에 대한 요약을 보냅니다.

5. **심각도** 대화 상자에서 전자 메일 알림을 트리거할 수준을 하나 이상 선택합니다.

6. **Save**를 클릭합니다.
### Azure IaaS VM 백업에 사용할 수 있는 경고 유형은 무엇입니까?
| 경고 수준 | 전송되는 경고 |
| ------------- | ------------- |
| 중요 | Backup failure, recovery failure |
| Warning | 없음 |
| 정보 제공 | 없음 | 

### 알림이 구성된 경우에도 전자 메일이 전송되지 않는 경우가 있습니까?

알림이 제대로 구성되었더라도 경고가 전송되지 않는 경우가 있습니다. 이처럼 전자 메일 알림이 전송되지 않는 경우는 다음과 같습니다.

- 알림이 매시간 다이제스트로 구성되고 알림이 발생하고 한 시간 이내에 확인되는 경우.
- 작업이 취소됩니다.
- 백업 작업이 트리거된 다음 실패하고 다른 백업 작업이 진행 중입니다.
- 리소스 관리자 활성화된 VM에 대한 예약된 백업 작업이 시작되지만 VM이 더 이상 존재하지 않습니다.

## 이벤트 보기 사용자 지정

**감사 로그** 설정은 미리 정의된 필터 집합 및 작동 가능한 이벤트 정보를 표시하는 열과 함께 제공됩니다. **이벤트** 블레이드가 열릴 때 원하는 정보를 표시하도록 보기를 사용자 지정할 수 있습니다.

1. [자격 증명 모음 대시보드](./backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)에서 **감사 로그**를 찾아 클릭하여 **이벤트** 블레이드를 엽니다.

    ![감사 로그](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    **이벤트** 블레이드는 현재 자격 증명 모음에 대해서만 필터링되는 작동 이벤트에 열립니다.

    ![감사 로그 필터](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    블레이드에서는 지난 주에 발생한 중요, 오류, 경고 및 정보 이벤트의 목록을 보여 줍니다. 시간 범위는 **필터**에 설정된 기본값입니다. **이벤트** 블레이드에서는 이벤트가 발생했을 때 추적하는 가로 막대형 차트도 보여 줍니다. 가로 막대형 차트를 표시하지 않으려는 경우 **이벤트** 메뉴에서 **차트 숨기기**를 클릭하여 차트를 해제합니다. 이벤트의 기본 보기는 작업, 수준, 상태, 리소스 및 시간 정보를 표시합니다. 추가 이벤트 특성의 표시에 대한 자세한 내용은 [이벤트 정보 확장](backup-azure-monitor-vms.md#view-additional-event-attributes) 섹션을 참조하세요.

2. 작동할 수 있는 이벤트에 대한 자세한 내용을 보려면 **작업** 열에서 작동할 수 있는 이벤트를 클릭하여 해당 블레이드를 엽니다. 블레이드는 이벤트에 대한 자세한 정보를 포함합니다. 이벤트는 상관 관계 ID 및 시간 범위에 발생한 이벤트 목록별로 그룹화됩니다.

    ![작업 세부 정보](./media/backup-azure-monitor-vms/audit-logs-details-window.png)

3. 특정 이벤트에 대한 자세한 정보를 보려면 이벤트의 목록에서 이벤트를 클릭하여 해당 **세부 정보** 블레이드를 엽니다.

    ![이벤트 세부 정보](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    이벤트 수준 정보는 정보에서 가져오는 만큼 자세합니다. 각 이벤트에 대해 많은 정보를 보기를 선호하고 이러한 많은 정보를 **이벤트** 블레이드에 추가하려는 경우 [이벤트 정보 확장](backup-azure-monitor-vms.md#view-additional-event-attributes) 섹션을 참조하세요.


## 이벤트 필터 사용자 지정
**필터**를 사용하여 특정 블레이드에 나타나는 정보를 조정하거나 선택합니다. 이벤트 정보를 필터링하려면:

1. [자격 증명 모음 대시보드](./backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)에서 **감사 로그**를 찾아 클릭하여 **이벤트** 블레이드를 엽니다.

    ![감사 로그](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    **이벤트** 블레이드는 현재 자격 증명 모음에 대해서만 필터링되는 작동 이벤트에 열립니다.

    ![감사 로그 필터](./media/backup-azure-monitor-vms/audit-logs-filter.png)

2. **이벤트** 메뉴에서 **필터**를 클릭하여 해당 블레이드를 엽니다.

    ![필터 블레이드 열기](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)

3. **필터** 블레이드에서 **수준**, **시간 범위** 및 **호출자** 필터를 조정합니다. 복구 서비스 자격 증명 모음에 대한 현재 정보를 제공하도록 설정되었기 때문에 다른 필터를 사용할 수 없습니다.

    ![감사 로그 쿼리 세부 정보](./media/backup-azure-monitor-vms/filter-blade.png)

    이벤트의 **수준**을 중요, 오류, 경고 또는 정보로 지정할 수 있습니다. 이벤트 수준의 조합을 선택할 수 있지만 하나 이상의 수준을 선택해야 합니다. 수준을 설정하거나 해제합니다. **시간 범위** 필터를 사용하면 이벤트 캡처에 대한 시간의 길이를 지정할 수 있습니다. 사용자 지정 시간 범위를 사용하는 경우 시작 및 종료 시간을 설정할 수 있습니다.

4. 필터를 사용하여 작업 로그를 쿼리할 준비가 되면 **업데이트**를 클릭합니다. **이벤트** 블레이드에 결과가 표시됩니다.

    ![작업 세부 정보](./media/backup-azure-monitor-vms/edited-list-of-events.png)


### 추가 이벤트 특성 보기
**열** 단추를 사용하여 추가 이벤트 특성을 **이벤트** 블레이드의 목록에 나타나게 할 수 있습니다. 이벤트의 기본 목록은 작업, 수준, 상태, 리소스 및 시간 정보를 표시합니다. 추가 특성을 사용하려면:

1. **이벤트** 블레이드에서 **열**을 클릭합니다.

    ![열 열기](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    **열 선택** 블레이드가 열립니다.

    ![열 블레이드](./media/backup-azure-monitor-vms/columns-blade.png)

2. 특성을 선택하려면 확인란을 클릭합니다. 특성 확인란이 설정 및 해제됩니다.

3. **이벤트** 블레이드의 특성 목록을 재설정하려면 **재설정**을 클릭합니다. 목록에서 특성을 추가 또는 제거한 후 **재설정**을 사용하여 이벤트 특성의 새 목록을 봅니다.

4. **업데이트**를 클릭하여 이벤트 특성의 데이터를 업데이트합니다. 다음 표는 각 특성에 대한 정보를 제공합니다.

| 열 이름 |설명|
| -----------------|-----------|
| 작업|작업 이름|
| 수준|작업 수준, 가능한 값: 정보, 경고, 오류 또는 중요|
|가동 상태|작업에 대한 설명이 포함된 상태|
|리소스|리소스를 식별하는 URL. 리소스 ID라고도 함|
|Time|시간, 현재 시간부터 이벤트가 발생한 시간까지 측정|
|Caller|이벤트를 호출하거나 트리거한 사람 또는 대상. 시스템 또는 사용자일 수 있음|
|Timestamp|이벤트 트리거된 시간|
|리소스 그룹|연결된 리소스 그룹|
|리소스 종류|리소스 관리자가 사용하는 내부 리소스 유형|
|구독 ID|연결된 구독 ID|
|Category|이벤트의 범주|
|상관관계 ID|관련된 이벤트에 대한 일반적인 ID|



## PowerShell을 사용하여 경고 사용자 지정
포털에서 작업에 대한 사용자 지정 경고 알림을 받을 수 있습니다. 다음 작업을 가져오려면 작업 로그 이벤트에서 PowerShell 기반 경고 규칙을 정의합니다. *Azure PowerShell 버전 1.3.0 이상*을 사용합니다.

사용자 지정 알림을 정의하여 백업 실패에 대해 경고하려면 다음 스크립트와 같은 명령을 사용:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: 감사 로그에서 ResourceId를 가져올 수 있습니다. ResourceId는 작업 로그의 리소스 열에 제공된 URL입니다.

**OperationName**: OperationName은 "Microsoft.RecoveryServices/recoveryServicesVault/*EventName*" 형식이며, 여기서 *EventName*은 다음 중 하나일 수 있음:<br/>
- 등록 <br/>
- 등록 취소 <br/>
- ConfigureProtection <br/>
- 백업 <br/>
- 복원 <br/>
- StopProtection <br/>
- DeleteBackupData <br/>
- CreateProtectionPolicy <br/>
- DeleteProtectionPolicy <br/>
- UpdateProtectionPolicy <br/>

**상태**: 지원되는 값은 시작함, 성공함 또는 실패함입니다.

**ResourceGroup** : 리소스가 속한 리소스 그룹입니다. 생성된 로그에 리소스 그룹 열을 추가할 수 있습니다. 리소스 그룹은 사용할 수 있는 이벤트 정보의 종류 중 하나입니다.

**이름**: 경고 규칙의 이름입니다.

**CustomEmail** : 경고 알림을 보낼 사용자 지정 메일 주소를 지정

**SendToServiceOwners**: 이 옵션은 구독의 모든 관리자 및 공동 관리자에게 경고 알림을 보냅니다. **New-AzureRmAlertRuleEmail** cmdlet에 사용될 수 있음

### 경고에 대한 제한
이벤트 기반 경고에 다음과 같은 제한 사항이 적용됨:

1. 복구 서비스 자격 증명 모음의 모든 가상 컴퓨터에서 경고를 유발합니다. 복구 서비스 자격 증명 모음에서 가상 컴퓨터의 하위 집합에 대한 경고를 사용자 지정할 수 없습니다.
2. 이 기능은 미리 보기 상태입니다. [자세히 알아보기](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. "alerts-noreply@mail.windowsazure.com"으로부터 경고가 전송됩니다. 현재는 전자 메일 보낸 사람을 수정할 수 없습니다.


## 다음 단계

이벤트 로그를 사용하면 백업 작업에 대한 post-mortem 및 감사 지원이 가능합니다. 다음 작업이 기록됨:

- 등록
- 등록 취소
- 보호 구성
- 백업(예약된 백업 및 주문형 백업 모두)
- 복원
- 보호 중지
- 백업 데이터 삭제
- 정책 추가
- 정책 삭제
- 정책 업데이트
- 작업 취소

Azure 서비스 전반에 걸쳐 이벤트, 작업 및 감사 로그에 대한 광범위한 설명은 [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md) 문서를 참조하세요.

복구 지점에서 가상 컴퓨터를 다시 만드는 방법에 대한 내용은 [Azure VM 복원](backup-azure-restore-vms.md)을 확인합니다. 가상 컴퓨터를 보호하는 방법에 대한 정보가 필요한 경우 [먼저 보기: 복구 서비스 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요. [Azure 가상 컴퓨터 백업 관리](backup-azure-manage-vms.md) 문서에서 VM 백업에 대한 관리 작업에 관하여 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->