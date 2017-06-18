<properties 
	pageTitle="Azure 자동화에서 Runbook 테스트 | Microsoft Azure"
	description="Azure 자동화에서 Runbook을 게시하기 전에 테스트하여 예상대로 작동하는지 확인할 수 있습니다. 이 문서에서는 Runbook을 테스트하고 해당 출력을 보는 방법에 대해 설명합니다."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/12/2016"
	ms.author="magoedte;bwren" />

# Azure 자동화에서 Runbook 테스트
Runbook을 테스트할 때 [초안 버전](automation-creating-importing-runbook.md#publishing-a-runbook)이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 만들어지지 않지만 [출력](automation-runbook-output-and-messages.md#output-stream)과 [경고 및 오류](automation-runbook-output-and-messages.md#message-streams) 스트림은 테스트 출력 창에 표시됩니다. [자세한 정보 스트림](automation-runbook-output-and-messages.md#message-streams)에 대한 메시지는 [$VerbosePreference 변수](automation-runbook-output-and-messages.md#preference-variables)가 Continue로 설정되는 경우에만 출력 창에 표시됩니다.

초안 버전이 실행 중이어도 Runbook은 여전히 정상적으로 워크플로를 실행하고 해당 환경에서 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 프로덕션이 아닌 리소스에서만 Runbook을 테스트해야 합니다.

각 [Runbook 유형](automation-runbook-types.md)을 테스트하는 절차는 동일하며 Azure 포털에서 테스트할 때 텍스트 편집기와 그래픽 편집기 간에 차이가 없습니다.


## Azure 포털에서 Runbook을 테스트하려면

Azure 포털에서 모든 [Runbook 유형](automation-runbook-types.md) 작업을 수행할 수 있습니다.

1. [텍스트 편집기](automation-editing-a-runbook.md#Portal) 또는 [그래픽 편집기](automation-graphical-authoring-intro.md)에서 Runbook의 초안 버전을 엽니다.
2. **테스트** 단추를 클릭하여 테스트 블레이드를 엽니다.
3. Runbook에 매개 변수가 있는 경우 테스트에 사용되는 값을 제공할 수 있는 왼쪽 창에 나열됩니다.
4. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 테스트를 실행하려는 경우는 **실행 설정**을 **Hybrid Worker**로 변경하고 대상 그룹의 이름을 선택합니다. 그렇지 않은 경우 기본 **Azure**를 유지하여 클라우드에서 테스트를 실행합니다.
5. **시작** 단추를 클릭하여 테스트를 시작합니다.
6. Runbook이 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks)인 경우 출력 창 아래의 단추를 사용하여 테스트되는 동안 중지하거나 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단하기 전에 현재 작업을 완료합니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
7. 출력 창에서 Runbook의 출력을 검사합니다.


## 다음 단계

- Runbook을 만들거나 가져오는 방법을 알아보려면 [Azure 자동화에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)를 참조하세요.
- 그래픽 작성에 대해 자세히 알아보려면 [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
- PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
- 권장 방법을 포함하여 상태 메시지 및 오류를 반환하도록 Runbook을 구성하는 방법에 대한 자세한 내용은 [Azure 자동화에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->