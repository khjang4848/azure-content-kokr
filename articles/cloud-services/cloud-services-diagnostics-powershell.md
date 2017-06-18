<properties
	pageTitle="PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용 | Microsoft Azure"
	description="PowerShell을 사용하여 클라우드 서비스에 진단을 사용하도록 설정하는 방법을 알아봅니다."
	services="cloud-services"
	documentationCenter=".net"
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="adegeo"/>


# PowerShell을 사용하여 Azure 클라우드 서비스에 진단 사용

Azure 진단 확장을 사용하여 클라우드 서비스로부터 응용 프로그램 로그, 성능 카운터 등과 같은 진단 데이터를 수집할 수 있습니다. 이 문서는 PowerShell을 사용하여 클라우드 서비스에 대해 Azure 진단 확장을 사용하도록 설정하는 방법을 설명합니다. 이 문서에 요구되는 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

## 클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정

이 접근 방식은 진단 확장이 클라우드 서비스의 배포 중 일부로 사용될 수 있는 연속 통합 형식의 시나리오에 유용합니다. 새 클라우드 서비스 배포를 만들 경우 *ExtensionConfiguration* 매개 변수에서 [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) cmdlet으로 전달하여 진단 확장을 사용할 수 있습니다. *ExtensionConfiguration* 매개 변수는 [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) cmdlet을 사용하여 만들 수 있는 진단 구성 배열을 사용합니다.

다음 예제는 각각 진단 구성이 다른 WebRole 및 WorkerRole을 사용하여 클라우드 서비스에 대해 진단을 사용하도록 설정하는 방법을 보여줍니다.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

진단 구성 파일이 저장소 계정 이름으로 StorageAccount 요소를 지정할 경우 New-AzureServiceDiagnosticsExtensionConfig cmdlet에서 해당 저장소 계정을 자동으로 사용합니다. 이렇게 작동하려면, 저장소 계정이 배포된 클라우드 서비스와 동일한 구독에 있어야 합니다.

Azure SDK 2.6 이후부터 MSBuild 게시 대상 출력에 의해 생성된 확장 구성 파일은 서비스 구성 파일(.cscfg)에 지정된 진단 구성 문자열에 기반한 저장소 계정 이름을 포함합니다. 아래의 스크립트에서는 게시 대상 출력에서 확장 구성 파일을 구문 분석하고 클라우드 서비스를 배포할 때 각 역할에 대한 진단 확장을 구성하는 방법을 보여줍니다.

	$service_name = "MyService"
	$service_package = "C:\build\output\CloudService.cspkg"
	$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

	#Find the Extensions path based on service configuration file
	$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

	$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
	$diagnosticsConfigurations = @()
	foreach ($extPath in $diagnosticsExtensions)
	{
	#Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
	$roleName = ""
	$roles = $extPath -split ".",0,"simplematch"
	if ($roles -is [system.array] -and $roles.Length -gt 1)
	    {
	    $roleName = $roles[1]
	    $x = 2
	    while ($x -le $roles.Length)
	        {
	           if ($roles[$x] -ne "PubConfig")
	            {
	                $roleName = $roleName + "." + $roles[$x]
	            }
	            else
	            {
	                break
	            }
	            $x++
	        }
	    $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
	    $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
	    $diagnosticsConfigurations += $diagnosticsconfig
	    }
	}
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online은 진단 확장으로 클라우드 서비스의 자동화된 배포에 대해 유사한 접근 방식을 사용합니다. 전체 예제는 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1)을 참조하세요.

진단 구성에 StorageAccount가 지정되지 않은 경우 cmdlet에 StorageAccountName 매개 변수를 전달해야 합니다. StorageAccountName 매개 변수가 지정된 경우 cmdlet은 항상 진단 구성 파일에 지정된 저장소 계정이 아닌 매개 변수에 지정된 저장소 계정을 사용합니다.

진단 저장소 계정이 클라우드 서비스와 다른 구독에 있는 경우 StorageAccountName 및 StorageAccountKey 매개 변수를 cmdlet에 명시적으로 전달해야 합니다. 진단 저장소 계정이 동일한 구독에 있는 경우 진단 확장을 사용하도록 설정하면 cmdlet이 키 값을 자동으로 쿼리하고 설정할 수 있으므로 StorageAccountKey 매개 변수가 필요하지 않습니다. 하지만 진단 저장소 계정이 다른 구독에 있는 경우에는 cmdlet이 자동으로 키를 얻지 못할 수 있으며, 사용자가 StorageAccountKey 매개 변수를 통해 키를 명시적으로 지정해야 합니다.

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## 기존 클라우드 서비스에 진단 확장을 사용하도록 설정

[Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) cmdlet을 사용하여 이미 실행 중인 클라우드 서비스에 진단 구성을 사용하거나 업데이트할 수 있습니다.


	$service_name = "MyService"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

	Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## 현재 진단 확장 구성 가져오기
[Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) cmdlet을 사용하여 클라우드 서비스에 대한 현재 진단 구성을 가져올 수 있습니다.

	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## 진단 확장 제거
클라우드 서비스에서 진단을 해제하려면 [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) cmdlet을 사용합니다.

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

*Role* 매개 변수 없이 *Set-AzureServiceDiagnosticsExtension* 또는 *New-AzureServiceDiagnosticsExtensionConfig*를 사용하여 진단 확장을 사용하도록 설정한 경우에는 *Role* 매개 변수 없이 *Remove-AzureServiceDiagnosticsExtension*을 사용하여 확장을 제거할 수 있습니다. *Role* 매개 변수가 확장을 사용하도록 설정할 때 사용되었으면, 확장을 제거할 때도 사용되어야 합니다.

각각의 개별 역할에서 진단 확장을 제거하려면:

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## 다음 단계

- 문제 해결을 위한 Azure 진단 및 기타 기법 사용에 대한 추가 지침은 [Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 사용](cloud-services-dotnet-diagnostics.md)을 참조하세요.
- [진단 구성 스키마](https://msdn.microsoft.com/library/azure/dn782207.aspx)는 진단 확장에 대한 다양한 XML 구성 옵션을 설명합니다.
- 가상 컴퓨터에 대해 진단 확장을 사용하도록 설정하는 방법을 알아보려면 [Azure 리소스 관리자 템플릿을 사용한 모니터링 및 진단으로 Windows 가상 컴퓨터 만들기(영문)](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->