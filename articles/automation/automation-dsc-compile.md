<properties 
   pageTitle="Azure 자동화 DSC에서 구성을 컴파일 | Microsoft Azure" 
   description="필요한 상태 구성(DSC) 구성을 컴파일하는 두 가지 방법의 개요: Azure 포털에서와 Windows PowerShell을 사용하는 방법입니다. " 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#Azure 자동화 DSC에서 구성을 컴파일#

Azure 자동화를 사용하여 두 가지 방법으로 필요한 상태 구성(DSC) 구성을 컴파일할 수 있습니다. Azure 포털에서와 Windows PowerShell을 사용하는 방법입니다. 다음 테이블에서는 각각의 특징을 기반으로 어떤 방법을 언제 사용할지 결정하도록 합니다.

###Azure 미리 보기 포털###
- 대화형 사용자 인터페이스를 사용하는 간단한 방법
- 단순한 매개 변수 값을 제공하는 양식
- 작업 상태를 쉽게 추적
- Azure 로그온을 사용하여 액세스 인증

###Windows PowerShell###
- Windows PowerShell cmdlet을 사용하여 명령줄에서 호출
- 여러 단계로 구성된 자동화된 솔루션에 포함할 수 있음
- 단순한 매개 변수 값 및 복잡한 매개 변수 값 제공
- 작업 상태 추적
- 클라이언트에서 PowerShell cmdlet을 지원해야 함
- ConfigurationData 전달
- 자격 증명을 사용하는 구성을 컴파일

컴파일 방법을 결정했다면 컴파일을 시작하기 위해 아래의 해당 절차를 수행할 수 있습니다.

##Azure 포털을 사용하여 DSC 구성 컴파일##

1.  자동화 계정에서 **구성**을 클릭합니다.
2.  구성을 클릭하여 해당 블레이드를 엽니다.
3.  **컴파일**을 클릭합니다.
4.  구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 **컴파일 구성** 블레이드를 열어 매개 변수 값을 제공할 수 있습니다. 매개 변수에 대한 자세한 내용은 아래의 <a href="#basic-parameters">**기본 매개 변수**</a> 섹션을 참조하세요.
5.  **컴파일 작업** 블레이드를 열어서 컴파일 작업의 상태 및 노드 구성(MOF 구성 문서)을 추적할 수 있습니다. Azure 자동화 DSC 끌어오기 서버에 배치되게 됩니다.

##Windows PowerShell을 사용하여 DSC 구성을 컴파일##

Windows PowerShell을 사용하여 컴파일하기 시작하는 데 [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx)를 사용할 수 있습니다. 다음 샘플 코드는 **SampleConfig**이라는 DSC 구성의 컴파일을 시작합니다.

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob`는 해당 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx)과 함께 이 컴파일 작업 개체를 사용하여 컴파일 작업의 상태를 확인하고 [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx)로 해당 스트림(출력)을 볼 수 있습니다. 다음 샘플 코드는 **SampleConfig** 구성의 컴파일을 시작하고 완료될 때까지 대기한 다음 해당 스트림을 표시합니다.
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##기본 매개 변수##

매개 변수 형식 및 속성을 포함하는 DSC 구성의 매개 변수 선언은 Azure 자동화 runbook과 동일하게 작동합니다. [Azure 자동화에서 runbook 시작](automation-starting-a-runbook.md)을 참조하여 runbook 매개 변수에 대한 자세한 내용을 알아봅니다.

다음 예제에서는 **FeatureName** 및 **IsPresent**라는 두 개의 매개 변수를 사용하여 **ParametersExample.sample** 노드 구에서 속성의 값을 결정하며 이는 컴파일하는 동안 생성됩니다.

    Configuration ParametersExample
    {
    	param(
    		[Parameter(Mandatory=$true)]
    
    		[string] $FeatureName,
    
    		[Parameter(Mandatory=$true)]
    		[boolean] $IsPresent
    	)
    
    	$EnsureString = "Present"
    	if($IsPresent -eq $false)
    	{
    		$EnsureString = "Absent"
    	}
    
    	Node "sample"
    	{
    		WindowsFeature ($FeatureName + "Feature")
    		{
    			Ensure = $EnsureString
    			Name = $FeatureName
    		}
    	}
    }

Azure 자동화 DSC 포털 또는 Azure PowerShell로 기본 매개 변수를 사용하는 DSC 구성을 컴파일할 수 있습니다.

###포털###

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![대체 텍스트](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell은 키와 매개 변수 이름이 일치하고 매개 변수 값과 값이 같은 [hashtable](http://technet.microsoft.com/library/hh847780.aspx)에 매개 변수가 필요합니다.

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 <a href="#credential-assets">**자격 증명 자산**</a>을 참조하세요.

##ConfigurationData##

**ConfigurationData**를 사용하면 PowerShell DSC를 사용하는 동안 환경의 특정 구성에서 구조적 구성을 구분할 수 있습니다. [PowerShell DSC의 "위치"에서 "대상" 분리](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)를 참조하여 **ConfigurationData**에 대해 자세히 알아봅니다.

>[AZURE.NOTE] Azure PowerShell을 사용하여 Azure 포털이 아닌 Azure 자동화 DSC에서 컴파일하는 경우 **ConfigurationData**를 사용할 수 있습니다.

다음 예제 DSC 구성은 **$ConfigurationData** 및 **$AllNodes** 키워드를 통해 **ConfigurationData**를 사용합니다. 또한 다음과 같이 예를 들어 [**xWebAdministration** 모듈](https://www.powershellgallery.com/packages/xWebAdministration/)이 필요합니다.

     Configuration ConfigurationDataSample
     {
    	Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
    	Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
    	Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    	{
    		xWebsite Site
    		{
    			Name = $Node.SiteName
    			PhysicalPath = $Node.SiteContents
    			Ensure   = "Present"
    		}
    	}
    }

PowerShell로 위의 DSC 구성을 컴파일할 수 있습니다. 아래 PowerShell은 Azure 자동화 DSC 끌어오기 서버에 **ConfigurationDataSample.MyVM1** 및 **ConfigurationDataSample.MyVM3**과 같은 두 개의 노드 구성을 추가합니다.

    $ConfigData = @{
    	AllNodes = @(
			@{
    			NodeName = "MyVM1"
    			Role = "WebServer"
    		},
    		@{
    			NodeName = "MyVM2"
    			Role = "SQLServer"
    		},
    		@{
    			NodeName = "MyVM3"
    			Role = "WebServer"
    
    		}
    
    	)
    
    	NonNodeData = @{
    		SomeMessage = "I love Azure Automation DSC!"
    
    	}
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##자산##

자산 참조는 Azure 자동화 DSC 구성 및 runbook에서 동일합니다. 자세한 내용은 다음을 참조하세요.

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [변수](automation-variables.md)

###자격 증명 자산###
Azure Automation에서 DSC 구성은 **Get-AzureRmAutomationCredential**을 사용하여 자격 증명 자산을 참조할 수 있지만 원하는 경우 자격 증명 자산은 매개 변수를 통해 전달될 수 있습니다. 구성이 **PSCredential** 형식의 매개 변수를 사용하는 경우 PSCredential 개체가 아닌 Azure 자동화 자격 증명 자산의 문자열 이름을 해당 매개 변수 값으로 전달해야 합니다. 내부적으로 해당 이름을 가진 Azure 자동화 자격 증명 자산은 검색되고 구성에 전달됩니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. Azure 자동화는 이 한 단계를 추가로 수행하고 전체 MOF 파일을 암호화합니다. 그러나 현재 PowerShell DSC가 노드 구성 MOF을 생성하는 동안 자격 증명을 일반 텍스트로 출력해도 되는지 알아야 합니다. PowerShell DSC은 Azure 자동화가 컴파일 작업을 통해 생성된 후에 전체 MOF 파일을 암호화한다는 것을 모르기 때문입니다.

PowerShell DSC가 <a href="#configurationdata">**ConfigurationData**</a>를 사용하여 생성된 노드 구성 MOF에서 자격 증명을 일반 텍스트로 출력해도 되는지 알 수 있습니다. DSC 구성에 표시되고 자격 증명을 사용하는 각 노드 블록 이름의 경우 **ConfigurationData**를 통해 `PSDscAllowPlainTextPassword = $true`을 전달해야 합니다.

다음 예제에서는 자동화 자격 증명 자산을 사용하는 DSC 구성을 보여줍니다.

    Configuration CredentialSample
    {
       $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"
    
    	Node $AllNodes.NodeName
    	{ 
    		File ExampleFile
    		{ 
    			SourcePath = "\\Server\share\path\file.ext" 
    			DestinationPath = "C:\destinationPath" 
    			Credential = $Cred 
       		}
    	}
    }

PowerShell로 위의 DSC 구성을 컴파일할 수 있습니다. 아래 PowerShell은 Azure 자동화 DSC 끌어오기 서버에 **CredentialSample.MyVM1** 및 **CredentialSample.MyVM2**와 같은 두 개의 노드 구성을 추가합니다.


    $ConfigData = @{
    	AllNodes = @(
    		@{
    			NodeName = "*"
    			PSDscAllowPlainTextPassword = $True
    		},
    		@{
    			NodeName = "MyVM1"
    		},
    		@{
    			NodeName = "MyVM2"
    		}
    	)
    }
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

<!---HONumber=AcomDC_0921_2016-->