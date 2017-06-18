<properties
    pageTitle="리소스 그룹 제거 자동화 | Microsoft Azure"
    description="구독에서 모든 리소스 그룹을 제거하는 runbook을 포함하는 Azure Automation 시나리오의 PowerShell 워크플로 버전입니다."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>

# Azure Automation 시나리오 - 리소스 그룹 제거 자동화

많은 고객이 만든 여러 리소스 그룹을 만들면 그 중 일부 리소스 그룹에서 전용으로 프로덕션 응용 프로그램을 관리하고 다른 리소스 그룹에서 환경을 개발, 테스트 및 스테이징합니다. 이러한 리소스의 배포를 자동화하는 것이 중요하지만 단추를 한 번 클릭하여 리소스 그룹을 해제하는 것도 중요합니다. 이를 처리하기 위해 Automation을 사용하는 것이 일반적인 관리 태스크를 간소화하는 가장 좋은 방법입니다. 예를 들어 MSDN 또는 Microsoft 파트너 네트워크 클라우드 Essentials 프로그램과 같은 구성원 혜택을 통해 지출 한도가 있는 Azure 구독을 사용하는 경우에도 유용합니다.

이 시나리오는 PowerShell Runbook을 기반으로 하고 구독에서 지정하는 하나 이상의 리소스 그룹을 제거하도록 만들어졌습니다. Runbook은 계속 진행하기 전에 기본값인 첫 번째를 테스트하도록 지원합니다. 이러한 방법으로 이 절차를 완료할 준비가 되었는지 확실히 확인하지 않고 실수로 삭제하지 않을 수 있습니다.

## 시나리오 가져오기

이 시나리오는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript)에서 다운로드할 수 있거나 Azure Portal의 [Runbook Gallery](automation-runbook-gallery.md)에서 직접 가져올 수 있는 PowerShell Runbook으로 구성됩니다.<br><br>

Runbook | 설명|
----------|------------|
Remove-ResourceGroup | 구독에서 하나 이상의 Azure 리소스 그룹 및 해당 리소스를 제거합니다.  
<br> 다음 입력 매개 변수는 이 runbook에 대해 정의됩니다.

매개 변수 | 설명|
----------|------------|
NameFilter(필수) | 이름 필터를 지정하여 삭제하려는 리소스 그룹을 제한할 수 있습니다. 쉼표로 구분된 목록을 사용하여 여러 값을 전달할 수 있습니다.<br>필터는 대/소문자를 구분하지 않으며 문자열을 포함하는 모든 리소스 그룹과 일치합니다.|
PreviewMode(선택 사항) | runbook을 실행하여 삭제되지만 아무 작업도 수행하지 않는 리소스 그룹을 확인합니다.<br>기본값은 **true**로 runbook에 전달된 하나 이상의 리소스 그룹을 실수로 삭제하지 않도록 합니다.  

## 이 시나리오 설치 및 구성

### 필수 조건

이 Runbook은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 사용하여 인증합니다.

### Runbook 설치 및 게시

Runbook을 다운로드한 후에 [Runbook 가져오기 절차](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation)의 절차를 사용하여 Runbook을 가져올 수 있습니다. Runbook을 Automation 계정으로 가져온 후에 게시합니다.


## Runbook 사용

다음 단계에서는 이 runbook의 실행 과정을 알아보고 작동 방법에 대해 이해하도록 합니다. 이 예제에서는 runbook을 테스트하지만 실제로 리소스 그룹을 삭제하지는 않을 것입니다.

1. Azure 포털에서 자동화 계정을 열고 **Runbook** 타일을 클릭합니다.
2. **Remove-ResourceGroup** runbook을 선택하고 **시작**을 클릭합니다.
3. Runbook을 시작할 때 매개 변수에 대한 다음 값을 구성할 수 있는 **Runbook 시작** 블레이드가 열립니다. 테스트하려는 구독에 하나 이상의 리소스 그룹 이름을 입력하면 실수로 삭제되더라도 지장이 발생하지 않습니다.<br> ![Remove-ResouceGroup 매개 변수](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] 선택한 리소스 그룹이 삭제되는 것을 방지하기 위해 **Previewmode** 옵션을 **true**로 설정해야 합니다. 이 runbook은 자신을 실행하는 Automation 계정을 포함하는 리소스 그룹을 제거하지 않는다는 점을 **기억**하세요.

4. 모든 매개 변수 값을 구성한 후에 **확인**을 클릭하면 runbook이 실행되기를 큐에서 대기합니다.

Azure Portal에서 **Remove-ResourceGroup** Runbook 작업의 세부 정보를 보려면 Runbook의 **작업** 타일을 선택합니다. 작업 요약은 작업 및 발생하는 모든 예외에 대한 일반 정보 외에도 입력 매개 변수 및 출력 스트림을 표시합니다.<br> ![Remove-ResourceGroup Runbook 작업 상태](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

**작업 요약**은 출력, 경고 및 오류 스트림의 메시지를 포함합니다. Runbook 실행의 자세한 결과를 보려면 **출력** 타일을 선택합니다.<br> ![Remove-ResourceGroup Runbook 출력 결과](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## 다음 단계

- 고유한 Runbook을 만들기 시작하려면 [Azure Automation에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)를 참조하세요.
- PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->