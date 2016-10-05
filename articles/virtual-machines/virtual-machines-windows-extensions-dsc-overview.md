<properties
   pageTitle="Azure에 필요한 상태 구성 개요 | Microsoft Azure"
   description="PowerShell 필요한 상태 구성에 대한 Microsoft Azure 확장 처리기를 사용하는 개요입니다. 필수 구성 요소, 아키텍처, cmdlet...을 포함"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# Azure 필요한 상태 구성 확장 처리기 소개 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 에이전트 및 연결된 확장은 Microsoft Azure 인프라 서비스의 일부입니다. VM 확장은 VM 기능을 확장하고 다양한 VM 관리 작업을 단순화하는 소프트웨어 구성 요소입니다. 예를 들어 VMAccess 확장은 관리자의 암호를 다시 설정하는 데 사용될 수 있고 사용자 지정 확장은 VM에서 스크립트를 실행하는 데 사용할 수 있습니다.

이 문서에서는 Azure PowerShell SDK의 일부로 Azure VM에 대한 PowerShell DSC(필요한 상태 구성) 확장을 소개합니다. 새로운 cmdlet을 사용하여 PowerShell DSC 확장을 사용하는 Azure VM에 PowerShell DSC 구성을 업로드하고 적용할 수 있습니다. PowerShell DSC 확장은 VM에서 받은 DSC 구성을 적용하도록 PowerShell DSC에 호출됩니다. 또한 이 기능은 Azure 포털을 통해 사용할 수 있습니다.

## 필수 조건 ##
**로컬 컴퓨터** Azure VM 확장과 상호 작용하려면 Azure 포털 또는 Azure PowerShell SDK를 사용해야 합니다.

**게스트 에이전트** DSC 구성으로 구성된 Azure VM은 WMF(Windows Management Framework) 4.0 또는 5.0를 지원하는 OS여야 합니다. 지원되는 OS 버전의 전체 목록은 [DSC 확장 버전 기록](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)에 있습니다.

## 용어 및 개념 ##
이 가이드에서는 다음과 같은 개념에 익숙합니다.

구성 - DSC 구성 문서.

노드 - DSC 구성에 대한 대상. 이 문서에서 "노드"는 Azure VM을 나타냅니다.

구성 데이터 - 구성에 대한 환경 데이터를 포함하는 .psd1 파일

## 아키텍처 개요 ##

Azure DSC 확장은 Azure VM 에이전트 프레임워크를 사용하여 Azure VM에서 실행되는 DSC 구성을 제공하고 적용하며 보고합니다. DSC 확장은 Azure PowerShell SDK 또는 Azure 포털을 통해 제공되는 최소한의 구성 문서 및 일련의 매개 변수를 포함하는 .zip 파일이 필요합니다.

확장이 처음으로 호출되면 설치 프로세스를 실행합니다. 이 프로세스는 다음 논리를 사용하여 WMF(Windows Management Framework)의 버전을 설치합니다.

1. Azure VM OS가 Windows Server 2016이면 아무 작업도 수행되지 않습니다. Windows Server 2016에는 이미 최신 버전의 PowerShell이 설치되어 있습니다.
2. `wmfVersion` 속성을 지정하면 VM의 OS와 호환되지 않는 경우가 아닌 경우 해당 버전의 WMF가 설치됩니다.
3. `wmfVersion` 속성을 지정하면 WMF의 적용 가능한 최신 버전이 설치됩니다.

WMF를 설치하려면 다시 부팅해야 합니다. 다시 부팅한 후에 확장은 `modulesUrl` 속성에 지정된 .zip 파일을 다운로드합니다. 이 위치가 Azure Blob 저장소인 경우 SAS 토큰은 `sasToken` 속성에 지정되어 파일에 액세스할 수 있습니다. .zip을 다운로드하고 압축을 푼 후에 `configurationFunction`에 정의된 구성 함수를 실행하여 MOF 파일을 생성합니다. 그런 다음 확장은 생성된 MOF 파일에 `Start-DscConfiguration -Force`를 실행합니다. 확장은 출력을 캡처하고 Azure 상태 채널에 다시 작성합니다. 이 시점부터 DSC LCM은 모니터링 및 수정을 정상적으로 처리합니다.

## PowerShell cmdlet ##

PowerShell cmdlet은 ARM 또는 ASM과 함께 사용되어 DSC 확장 배포를 패키징하고 게시하며 모니터링할 수 있습니다. 나열된 다음 cmdlet은 ASM 모듈이지만 "Azure"는 ARM 모델을 사용하는 "AzureRm"으로 바꿀 수 있습니다. 예를 들어 `Publish-AzureVMDscConfiguration`은 ASM을 사용하며 `Publish-AzureRmVMDscConfiguration`은 ARM을 사용합니다.

`Publish-AzureVMDscConfiguration`은 구성 파일을 받고 종속 DSC 리소스를 검색하며 구성 및 구성을 적용하는 데 필요한 DSC 리소스를 포함하는 .zip 파일을 만듭니다. 또는 `-ConfigurationArchivePath` 매개 변수를 사용하여 패키지를 로컬로 만들 수도 있습니다. 그렇지 않으면 Azure Blob 저장소에 .zip 파일을 게시하고 SAS 토큰으로 보호합니다.

이 cmdlet에서 만든 .zip 파일은 보관 폴더의 루트에서 .ps1 구성 스크립트를 포함합니다. 리소스에는 보관 폴더에 위치한 모듈 폴더가 있습니다.

`Set-AzureVMDscExtension`은 VM 구성 개체에 PowerShell DSC 확장에서 필요한 설정을 삽입하며 이는 `Update-AzureVM`를 사용하여 Azure VM에 적용될 수 있습니다.

`Get-AzureVMDscExtension`은 특정 VM의 DSC 확장 상태를 검색합니다.

`Get-AzureVMDscExtensionStatus`는 DSC 확장 처리기에 의해 적용되는 DSC 구성의 상태를 검색합니다. 이 작업을 단일 VM 또는 VM 그룹에 대해 수행할 수 있습니다.

`Remove-AzureVMDscExtension`은 지정된 가상 컴퓨터에서 확장 처리기를 제거합니다. 이 cmdlet은 구성을 제거하거나 WMF를 제거하거나 가상 컴퓨터에 적용된 설정을 변경하지 **않습니다**. 확장 처리기를 제거합니다.

**ASM 및 ARM cmdlet의 주요 차이점**

- ARM cmdlet은 동기입니다. ASM cmdlet은 비동기입니다.
- ResourceGroupName, VMName, ArchiveStorageAccountName, 버전, 위치는 모두 새 필수 매개 변수입니다.
- ArchiveResourceGroupName는 ARM에 대한 새로운 선택적 매개 변수입니다. 저장소 계정이 가상 컴퓨터를 만들 위치가 아닌 다른 리소스 그룹에 속해 있는 경우 이 매개 변수를 지정할 수 있습니다.
- ConfigurationArchive는 ARM에서 ArchiveBlobName이라고 합니다.
- ContainerName은 ARM에서 ArchiveContainerName이라고 합니다.
- StorageEndpointSuffix는 ARM에서 ArchiveStorageEndpointSuffix라고 합니다.
- AutoUpdate 스위치가 ARM에 추가되어 확장 처리기를 사용할 수 있는 경우 최신 버전으로 자동으로 업데이트할 수 있습니다. 이 매개 변수를 사용하면 새 버전의 WMF가 릴리스될 때 VM이 다시 시작될 가능성이 있습니다.


## Azure 포털 기능 ##
클래식 VM으로 이동합니다. 설정 -> 일반에서 "확장"을 클릭합니다. 새 창을 만듭니다. "추가"를 클릭하고 PowerShell DSC를 선택합니다.

포털에 입력해야 합니다. **구성 모듈 또는 스크립트**: 이 필드는 필수 필드입니다. .zip 파일 내의 모듈 폴더에서 루트 및 모든 종속 리소스 루트에 구성 스크립트를 포함하는 .ps1 파일 또는 .ps1 구성 스크립트를 포함하는 .zip 파일이 필요합니다. Azure PowerShell SDK에 포함된 `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet으로 만들 수 있습니다. .zip 파일은 SAS 토큰에 의해 보호된 사용자 Blob 저장소에 업로드됩니다.

**구성 데이터 PSD1 파일**: 이 필드는 선택적 필드입니다. 구성에 .psd1의 구성 데이터 파일이 필요한 경우 이 필드를 사용하여 선택하고 사용자 Blob 저장소에 업로드합니다. 이 저장소는 SAS 토큰으로 보호됩니다.
 
**구성의 모듈 정규화된 이름**: .ps1 파일에는 여러 개의 구성 함수가 있을 수 있습니다. 예를 들어 ''에 이어서 구성 .ps1 스크립트의 이름 및 구성 함수의 이름을 입력합니다. 예를 들어 .ps1 스크립트 이름이 "configuration.ps1"이고 구성이 "IisInstall"이면 `configuration.ps1\IisInstall`을 입력합니다.

**구성 인수**: 구성 함수가 인수를 사용하는 경우 `argumentName1=value1,argumentName2=value2` 형식에서 여기에 입력합니다. PowerShell cmdlet 또는 Resource Manager 템플릿을 통해 구성 인수를 수락하는 방법과는 다른 서식입니다.

## 시작 ##

Azure DSC 확장은 DSC 구성 문서를 수용하고 Azure VM에 적용합니다. 구성의 간단한 예제가 뒤따릅니다. "IisInstall.ps1"으로 다음과 같이 로컬로 저장합니다.

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

다음 단계는 지정된 VM에서 IisInstall.ps1 스크립트를 배치하고 구성을 실행하며 상태를 다시 보고합니다.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## 로깅 ##

로그는 다음에 배치됩니다.

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[Version Number]

## 다음 단계 ##

PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터를 방문하세요](https://msdn.microsoft.com/powershell/dsc/overview).

[DSC 확장에 대한 Azure Resource Manager 템플릿](virtual-machines-windows-extensions-dsc-template.md)을 검토합니다.

PowerShell DSC로 관리할 수 있는 추가 기능을 찾으려면 추가 DSC 리소스는 [PowerShell 갤러리에서 찾아보세요](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).

중요한 매개 변수를 구성에 전달하는 세부 정보는 [DSC 확장 처리기로 안전하게 자격 증명 관리](virtual-machines-windows-extensions-dsc-credentials.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->