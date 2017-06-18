<properties 
   pageTitle="학습 PowerShell 워크플로"
   description="이 문서는 PowerShell과 PowerShell 워크플로 간의 중대한 차이를 이해하는 PowerShell에 익숙한 작성자의 간단한 설명으로 제공됩니다."
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

# Windows PowerShell 워크플로 학습

Azure 자동화의 Runbook은 Windows PowerShell 워크플로로 구현됩니다. Windows PowerShell 워크플로는 Windows PowerShell 스크립트와 비슷해 보이지만 신규 사용자가 혼동할 수 있는 중요한 차이점이 있습니다. 이 문서는 이미 PowerShell에 익숙한 사용자를 대상으로 작성되었고, runbook에서 사용하기 위해 PowerShell 스크립트를 PowerShell워크플로로 변환하는 경우 필요한 개념에 대해 간략히 설명합니다.

워크플로는 장기 실행 작업을 수행하거나 여러 장치 또는 관리 노드에서 여러 단계를 조정해야 하는 프로그래밍 방식으로 연결된 일련의 단계입니다. 워크플로는 일반적인 스크립트에 비해 여러 장치에서 작업을 동시에 수행하고 오류로부터 자동으로 복구할 수 있다는 장점이 있습니다. Windows PowerShell 워크플로는 Windows Workflow Foundation을 활용하는 Windows PowerShell 스크립트입니다. 워크플로는 Windows PowerShell 구문으로 작성되고 Windows PowerShell에서 시작되지만 Windows Workflow Foundation에서 처리됩니다.

이 기사의 항목에 대한 자세한 내용은 [Windows PowerShell 워크플로 시작](http://technet.microsoft.com/library/jj134242.aspx)을 참조하세요.

## Runbook의 유형

Azure 자동화의 runbook에는 *PowerShell 워크플로*, *PowerShell* 및 *그래픽*의 세 유형이 있습니다. runbook을 만들 때, runbook의 유형을 정할 수 있고, 한번 만들어진 runbook은 유형을 변경할 수 없습니다.

PowerShell 워크플로 runbook 및 PowerShell runbook은 Azure 자동화의 텍스트 편집기 또는 PowerShell ISE같은 오프라인 편집기를 사용하여 PowerShell 코드를 직접 작업하려는 사용자에게 적합합니다. PowerShell 워크플로 runbook을 만드는 경우 이 문서의 정보를 이해해야 합니다.

그래픽 runbook은 동일한 활동 및 cmdlets를 사용하지만 근본적인 PowerShell 워크플로의 복잡성을 숨겨주는 그래픽 인터페이스를 사용합니다. 이 문서의 개념(예: 검사점과 병렬 실행)은 그래픽 runbook에도 적용되지만, 사용자는 자세한 구문을 걱정할 필요는 없습니다.

## 워크플로의 기본 구조

PowerShell 스크립트를 PowerShell 워크플로로 변환하는 첫 단계는 **워크플로** 키워드로 둘러싸는 것입니다. 워크플로는 **Workflow** 키워드와 그 뒤에 중괄호로 묶인 스크립트의 본문으로 시작됩니다. 워크플로 이름은 다음 구문과 같이 **Workflow** 키워드를 따릅니다.

    Workflow Test-Workflow
    {
       <Commands>
    }

워크플로 이름은 자동화 Runbook의 이름과 일치해야 합니다. Runbook을 가져오려면 파일 이름이 워크플로 이름과 일치하고 .ps1로 끝나야 합니다.

워크플로에 매개 변수를 추가 하려면, 스크립트와 마찬가지로 **Param**이 키워드 입니다.

## 코드 변경 내용

PowerShell 워크플로 코드는 몇 가지 중요한 변경 내용을 제외하고는 PowerShell 스크립트 코드와 거의 동일합니다. 다음 섹션에서 설명하는 변경 사항을 사용자가 워크플로에서 실행하려면 PowerShell script를 만들 필요가 있습니다.

### 활동

활동은 워크플로의 특정 작업입니다. 하나 이상의 명령으로 구성된 스크립트와 마찬가지로 워크플로는 시퀀스로 수행되는 하나 이상의 활동으로 구성됩니다. Windows PowerShell 워크플로는 워크플로를 실행할 때 많은 Windows PowerShell cmdlet을 활동으로 자동으로 변환합니다. Runbook에서 이러한 cmdlet 중 하나를 지정한 경우 해당 활동은 실제로 Windows Workflow Foundation에 의해 실행됩니다. 해당 활동이 없는 cmdlet의 경우 Windows PowerShell 워크플로는 [InlineScript](#inlinescript) 활동 내에서 cmdlet을 자동으로 실행합니다. InlineScript 블록에 명시적으로 포함하지 않으면 워크플로에서 제외되고 사용할 수 없는 cmdlet 집합이 있습니다. 이러한 개념에 자세한 내용은 [스크립트 워크플로에서 활동 사용](http://technet.microsoft.com/library/jj574194.aspx)을 참조하세요.

워크플로 활동은 해당 작업을 구성하기 위해 일반 매개 변수 집합을 공유합니다. 워크플로 일반 매개 변수에 대한 자세한 내용은 [about\_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)를 참조하세요.

### 위치 매개 변수

워크플로에서 위치매개 변수는 활동 및 cmdlets와 같이 사용할 수 없습니다. 사용자가 매개 변수 이름을 사용해야 한다는 뜻입니다.

예를 들어, 다음 모든 실행 중인 서비스의 코드를 가져오는 것이 좋습니다.

	 Get-Service | Where-Object {$_.Status -eq "Running"}

동일한 코드를 워크플로에서 실행하려고 한다면, 다음과 같은 메시지를 얻게 됩니다. “지정된 이름 매개 변수를 사용하여 매개 변수 집합을 해결할 수 없습니다.” 이 문제를 해결하려면 다음과 같은 매개 변수 이름을 제공합니다.

	Workflow Get-RunningServices
	{
		Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
	}

### 역직렬화된 개체

개체는 워크플로에서 역직렬화됩니다. 즉, 이 말은 해당 속성은 사용할수 있지만 메서드는 사용할 수 없다는 의미입니다. 서비스 개체의 stop 메서드를 사용하여 서비스를 중지시키는 다음의 Powershell 코드를 예로 들 수 있습니다.

	$Service = Get-Service -Name MyService
	$Service.Stop()

워크플로에서 실행하는 경우, 다음과 같은 오류 메시지를 얻게 됩니다. “Windows PowerShell 워크플로에서는 메서드 호출이 지원되지 않습니다.”

한가지 옵션은 블록 내에서 case $Service가 서비스되는 [InlineScript](#InlineScript) 블록의 코드 두줄을 래핑하는 것입니다.

	Workflow Stop-Service
	{
		InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
		}
	} 

다른 옵션은 하나의 cmdlet가 사용 가능할 경우, 메서드와 동일한 기능을 수행하는 다른 cmdlet를 사용하는 것입니다. 이 샘플의 경우, Stop 서비스 cmdlet는 Stop 메서드와 같은 기능을 제공하고, 워크플로를 따라서 사용 가능합니다.

	Workflow Stop-MyService
	{
		$Service = Get-Service -Name MyService
		Stop-Service -Name $Service.Name
	}


## InlineScript

**InlineScript** 활동은 PowerShell 워크플로 대신 전통적인 PowerShell 스크립트처럼 하나 혹은 그 이상의 명령을 실행해야 할 때 유용합니다. 워크플로의 명령은 처리를 위해 Windows Workflow Foundation으로 전송되지만 InlineScript 블록의 명령은 Windows PowerShell에 의해 처리됩니다.

InlineScript에는 아래 표시된 구문이 사용됩니다.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

출력을 변수에 할당하여 InlineScript에서 출력을 반환 할 수 있습니다. 다음 예제에서는 서비스를 중지하고 서비스 이름을 출력합니다.

	Workflow Stop-MyService
	{
		$Output = InlineScript {
			$Service = Get-Service -Name MyService
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


값을 InlineScript 블록으로 전달할 수 있지만, **$Using**범위 한정자를 사용해야 합니다. 다음 예제는 변수에서 제공된 서비스 이름을 제외하고는 이전의 예제와 똑같습니다.

	Workflow Stop-MyService
	{
		$ServiceName = "MyService"
	
		$Output = InlineScript {
			$Service = Get-Service -Name $Using:ServiceName
			$Service.Stop()
			$Service
		}

		$Output.Name
	}


InlineScript 활동은 특정 워크플로에서 중요할 수 있지만 워크플로 구문을 지원하지 않으며 다음과 같은 이유로 필요한 경우에만 사용해야 합니다.

- [검사점](#Checkpoints)을 InlineScript 블록 내에서.사용할 수 없습니다. 블록 내에서 오류가 발생한 경우 블록의 처음부터 다시 시작해야 합니다.
- [병렬 실행](#parallel-execution)을 InlineScriptBlock 내부에서 사용할 수 없습니다.
- InlineScript는 InlineScript 블록의 전체 길이 동안 Windows PowerShell 세션을 유지하므로 워크플로의 확장성에 영향을 줍니다.

InlineScript 사용에 대한 자세한 내용은 [워크플로에서 Windows PowerShell 명령 실행](http://technet.microsoft.com/library/jj574197.aspx) 및 [about\_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)를 참조하세요.


## 병렬 처리

Windows PowerShell 워크플로의 한 가지 장점은 일반적인 스크립트처럼 명령 집합을 순차적으로 수행하지 않고 병렬로 수행할 수 있다는 점입니다.

**Parallel** 키워드를 사용하여 동시에 실행할 여러 명령이 포함된 스크립트 블록을 만들 수 있습니다. 여기에는 아래 표시된 구문이 사용됩니다. 이 예제에서 Activity1과 Activity2는 동시에 시작됩니다. Activity3은 Activity1과 Activity2가 모두 완료된 후에만 시작됩니다.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


네트워크 대상에 여러 파일을 복사하는 다음 PowerShell 명령을 예로 들 수 있습니다. 하나의 파일이 복사가 완전히 끝나야만 다음 복사가 시작되므로 이 명령은 순차적으로 실행됩니다.

	$Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
	$Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
	$Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

다음 워크플로는 복사를 동시에 시작하기 때문에 동일한 명령을 병렬로 실행합니다. 복사가 완전히 끝난 후 완료 메시지가 표시됩니다.

	Workflow Copy-Files
	{
		Parallel 
		{
			$Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
			$Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
		}

		Write-Output "Files copied."
	}


**ForEach -Parallel** 구문을 사용하여 컬렉션의 각 항목에 대한 명령을 동시에 처리할 수 있습니다. 이 경우 컬렉션의 항목은 병렬로 처리되지만 스크립트 블록의 명령은 순차적으로 실행됩니다. 여기에는 아래 표시된 구문이 사용됩니다. 이 예제에서 Activity1은 컬렉션의 모든 항목에 대해 동시에 시작됩니다. 각 항목에 대해 Activity2는 Activity1이 완료된 후에 시작됩니다. Activity3은 모든 항목에 대해 Activity1과 Activity2가 모두 완료된 후에만 시작됩니다.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

다음 예제에서는 동시에 파일을 복사하는 이전 예제와 비슷합니다. 이 경우 복사한 후 각 파일에 대 한 메시지가 표시 됩니다. 복사가 완전히 끝난 후 최종 완료 메시지가 표시됩니다.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach -Parallel ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
		}
		
		Write-Output "All files copied."
	}

> [AZURE.NOTE]  자식 runbook을 병렬로 실행하는 것은 추천하지 않는 이유는 결과물이 신뢰할 수 없기 때문입니다. 자식 runbook에서의 출력은 때때로 나타나지 않을 수도 있고, 한 자식 runbook의 설정이 다른 병렬 자식 runbook에 영향을 미칠 수 있습니다.


## 검사점

*검사점* 은 워크플로의 현재 상태에 대한 스냅숏으로, 변수의 현재 값 및 해당 지점에 생성된 모든 출력을 포함합니다. 워크플로가 오류 때문에 종료되었거나 일시 중단한 다음 실행하면 워크플로 처음부터 시작하는 게 아니라 해당 마지막 검사점에서 시작됩니다. **Checkpoint-Workflow** 활동을 사용하여 워크플로에서 검사점을 설정할 수 있습니다.

다음 샘플 코드에서는 Activity2 이후에 예외가 발생하여 워크플로가 끝납니다. 설정된 마지막 검사점 직후이므로 워크플로를 다시 시작하면 Activity2를 실행하는 것으로 작업이 시작됩니다.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

예외가 발생할 수 있으며 워크플로를 다시 시작한 경우 반복되지 않아야 하는 활동 뒤에 R워크플로의 검사점을 설정해야 합니다. 예를 들어 Runbook에서 가상 컴퓨터를 만들 수 있습니다. 가상 컴퓨터를 만드는 명령 앞뒤 모두에 검사점을 설정할 수 있습니다. 만들기에 실패하면, 워크플로가 다시 시작하는 경우 명령을 반복합니다. 생성 후 워크플로가 실패하는 경우, 워크플로를 재시작할 때에도 가상 컴퓨터는 다시 생성되지 않습니다.

다음 예제는 네트워크 위치에 여러 파일들을 복사하고 각 파일에 검사점을 설정합니다. 네트워크 위치가 손실 된 경우 워크플로가 오류로 종료 됩니다. 다시 실행하면, 마지막 검사점에서 재시작되는 데 이것은 이미 복사된 파일들은 생략한다는 것을 의미합니다.

	Workflow Copy-Files
	{
		$files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

		ForEach ($File in $Files) 
		{
			$Copy-Item -Path $File -Destination \\NetworkPath
			Write-Output "$File copied."
			Checkpoint-Workflow
		}
		
		Write-Output "All files copied."
	}

[Suspend-workflow](https://technet.microsoft.com/library/jj733586.aspx) 작업을 호출한 후 또는 마지막 검사점 이후에 사용자 이름 자격 증명을 유지하지 않기 때문에 자격 증명을 null로 설정하고 **Suspend-workflow** 또는 검사점을 호출한 후에 자산 저장소에서 다시 검색해야 합니다. 그렇지 않으면 다음과 같은 오류 메시지가 나타날 수 있습니다. *지속성 데이터를 완전히 저장할 수 없거나 저장된 지속성 데이터가 손상되었기 때문에 워크플로 작업을 다시 시작할 수 없습니다. 워크플로를 다시 시작해야 합니다.*

다음과 같은 코드에서는 PowerShell 워크플로 runbook에서 이를 처리하는 방법을 보여 줍니다.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


서비스 주체로 구성된 실행 계정을 사용하여 인증하는 경우에는 필요하지 않습니다.

검사점에 대한 자세한 내용은 [스크립트 워크플로에 검사점 추가](http://technet.microsoft.com/library/jj574114.aspx)를 참조하세요.


## 다음 단계

- PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->