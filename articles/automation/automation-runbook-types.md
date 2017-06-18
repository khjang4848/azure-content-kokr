<properties 
   pageTitle="Azure 자동화 Runbook 형식"
   description="Azure 자동화에서 사용할 수 있는 다양한 형식의 Runbook을 설명하고 사용할 형식을 결정할 때 고려해야 하는 사항을 설명합니다. "
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
   ms.author="bwren" />

# Azure 자동화 Runbook 형식

Azure 자동화는 네 가지 형식의 runbook을 지원하며 다음 테이블에 간략한 설명이 있습니다. 아래 섹션은 각각을 사용할 경우에 대한 고려 사항을 포함하여 각 형식에 대해 자세한 정보를 제공합니다.


| 형식 | 설명 |
|:---|:---|
| [그래픽](#graphical-runbooks) | Windows PowerShell을 기반으로 하며 Azure 포털의 그래픽 편집기로 완전하게 생성 및 편집됩니다. | 
| [그래픽 PowerShell 워크플로](#graphical-runbooks) | Windows PowerShell 워크플로를 기반으로 하며 Azure 포털의 그래픽 편집기로 완전하게 생성 및 편집됩니다. 
| [PowerShell](#powershell-runbooks) | Windows PowerShell 스크립트를 기반으로 하는 텍스트 Runbook
| [PowerShell 워크플로](#powershell-workflow-runbooks) | Windows PowerShell 워크플로를 기반으로 하는 텍스트 Runbook |


## 그래픽 Runbook

[그래픽](automation-runbook-types.md#graphical-runbooks) 및 그래픽 PowerShell 워크플로 Runbook은 Azure 포털의 그래픽 편집기로 생성 및 편집됩니다. 파일로 내보내서 다른 자동화 계정으로 가져올 수 있지만 다른 도구에서 만들거나 편집할 수 없습니다. 그래픽 Runbook은 PowerShell 코드를 생성하지만 코드를 직접 보거나 수정할 수 없습니다. 그래픽 Runbook은 [텍스트 형식](automation-runbook-types.md) 중 하나로 변환될 수 없고 텍스트 Runbook은 그래픽 형식으로 변환될 수 없습니다. 가져오는 동안 그래픽 Runbook을 그래픽 PowerShell 워크플로 Runbook으로 변환하거나 그 반대로 변환할 수 있습니다.

### 장점

- 최소한의 [PowerShell](automation-powershell-workflow.md) 지식으로 Runbook을 만듭니다.
- 관리 과정을 시각적으로 나타냅니다.
- 다른 Runbook을 자식 Runbook으로 포함하여 높은 수준의 워크플로를 만들 수 있습니다.


### 제한 사항

- Azure 포털 외부에서 Runbook을 편집할 수 없습니다.
- 복잡한 논리를 수행기 위해 PowerShell 코드를 포함하는 코드 작업이 필요할 수 있습니다.
- 그래픽 워크플로에 의해 만들어진 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 코드 작업에서 만든 코드는 볼 수 있습니다.


## PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure 자동화로 [Runbook 가져오기](http://msdn.microsoft.com/library/azure/dn643637.aspx)가 가능합니다.

### 장점

- PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
- Runbook은 실행 전에 컴파일이 필요 없기 때문에 그래픽 또는 PowerShell 워크플로 Runbook보다 빨리 시작됩니다.

### 제한 사항

- PowerShell 스크립팅에 대해 잘 알아야 합니다.
- 여러 작업을 병렬 수행하도록 [병렬 처리](automation-powershell-workflow.md#parallel-processing)를 사용할 수 없습니다.
- 오류 발생 시 Runbook을 다시 시작하도록 [검사점](automation-powershell-workflow.md#checkpoints)을 사용할 수 없습니다.
- PowerShell 워크플로 Runbook 및 그래픽 Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해서만 자식 Runbook으로 포함시킬 수 있습니다.

### 알려진 문제
PowerShell Runbook에 대해 현재 알려진 문제는 다음과 같습니다.

- PowerShell Runbook은 null 값으로 암호화되지 않은 [변수 자산](automation-variables.md)을 검색할 수 없습니다.
- PowerShell Runbook은 이름에 *~*을 사용하여 [변수 자산](automation-variables.md)을 검색할 수 없습니다.
- PowerShell Runbook의 반복적인 Get-Process는 80회 반복 후에 작동이 중단될 수 있습니다.
- PowerShell Runbook은 한 번에 스트림을 출력하기 위해 매우 큰 데이터를 쓰려는 시도를 하면 실패할 수 있습니다. 일반적으로 큰 개체로 작업하는 경우 필요한 정보만 출력하면 이 문제를 극복할 수 있습니다. 예를 들어 *Get-Process* 같은 출력 대신 *Get-Process | Select ProcessName, CPU*를 사용하여 필요한 필드만 출력할 수 있습니다.

## PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure 자동화로 [Runbook 가져오기](http://msdn.microsoft.com/library/azure/dn643637.aspx)가 가능합니다.

### 장점

- PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
- 오류가 발생하면 [검사점](automation-powershell-workflow.md#checkpoints)을 사용하여 Runbook을 다시 시작합니다.
- [병렬 처리](automation-powershell-workflow.md#parallel-processing)를 사용하여 여러 작업을 병렬로 수행합니다.
- 다른 그래픽 Runbook 및 PowerShell 워크플로 Runbook을 자식 Runbook으로 포함시켜 고급 워크플로를 만들 수 있습니다.


### 제한 사항

- 작성자는 PowerShell 워크플로를 잘 알아야 합니다.
- Runbook은 [역직렬화된 개체](automation-powershell-workflow.md#code-changes)와 같은 PowerShell 워크플로의 부가적인 복잡성을 다루어야 합니다.
- Runbook은 실행 전에 컴파일이 필요하기 때문에 PowerShell Runbook보다 시작 시간이 깁니다.
- PowerShell Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해서만 자식 Runbook으로 포함시킬 수 있습니다.


## 고려 사항

특정한 Runbook에 사용할 형식을 결정할 때 다음과 같은 사항을 추가로 고려해야 합니다.

- Runbook를 그래픽에서 텍스트 형식으로 또는 그 반대로 변환할 수는 없습니다.
- 형식이 다른 Runbook을 자식 Runbook으로 사용하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure 자동화의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

  
## 다음 단계

- 그래픽 Runbook 작성에 대해 자세히 알아보려면 [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
- Runbook용 PowerShell 및 PowerShell 워크플로 간의 차이점을 이해하려면 [Windows PowerShell 워크플로 학습](automation-powershell-workflow.md)을 참조하세요.
- Runbook을 만들거나 가져오는 방법에 대한 자세한 내용은 [Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->