<properties
	pageTitle="Azure Active Directory Domain Services: 관리 가이드 | Microsoft Azure"
	description="Windows 가상 컴퓨터를 Azure PowerShell 및 클래식 배포 모델을 사용하여 관리되는 도메인에 가입합니다."
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>


# PowerShell을 사용하여 Windows Server 가상 컴퓨터를 관리되는 도메인에 가입

> [AZURE.SELECTOR]
- [Azure 클래식 포털 - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure에는 리소스를 만들고 작업하기 위한 두 가지 다양한 배포 모델이 있습니다. [리소스 관리자 및 클래식](../resource-manager-deployment-model.md) 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. Azure AD 도메인 서비스는 현재 리소스 관리자 모델을 지원하지 않습니다.

다음 단계에서는 구성 요소 접근 방식을 사용하여 Windows 기반 Azure 가상 컴퓨터를 만들고 미리 구성하는 Azure PowerShell 명령 집합을 사용자 지정하는 방법을 보여 줍니다. 다음 단계는 Windows 기반 Azure 가상 컴퓨터를 빌드하고 Azure AD 도메인 서비스 관리되는 도메인에 가입하는 데 도움이 됩니다.

다음 단계에서는 빈 칸 채우기 접근 방식에 따라 Azure PowerShell 명령 집합을 만듭니다. 이 접근 방식은 PowerShell을 처음 접하거나 성공적인 구성을 위해 지정할 값을 알기를 원하는 경우에 유용할 수 있습니다. 고급 PowerShell 사용자는 명령을 가져와 고유한 변수 값("$"로 시작하는 줄)을 대체할 수 있습니다.

[Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다(아직 설치하지 않은 경우). 그런 다음 Windows PowerShell 명령 프롬프트를 엽니다.

## 1단계: 사용자 계정 추가

1. PowerShell 프롬프트에서 **Add-AzureAccount**를 입력하고 **입력**을 클릭합니다.
2. Azure 구독과 연관된 메일 주소를 입력하고 **계속**을 클릭합니다.
3. 계정에 암호를 입력합니다.
4. **로그인**을 클릭합니다.

## 2단계: 구독 및 저장소 계정 설정

Windows PowerShell 명령 프롬프트에서 다음 명령을 실행하여 Azure 구독 및 저장소 계정을 설정합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** 명령의 출력에 표시된 SubscriptionName 속성에서 올바른 구독 이름을 가져올 수 있습니다. **Select-AzureSubscription** 명령을 실행한 후 **Get-AzureStorageAccount** 명령의 출력에 표시된 Label 속성에서 올바른 저장소 계정 이름을 가져올 수 있습니다.


## 3단계: 단계별 연습 - 가상 컴퓨터 프로비전 및 관리되는 도메인에 가입
다음은 이 가상 컴퓨터를 만드는 해당 Azure PowerShell 명령 집합입니다. 각 블록 사이의 빈 줄은 가독성을 위해 추가된 것입니다.

프로비전할 Windows 가상 컴퓨터에 대한 정보를 지정합니다.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

D-, DS- 또는 G-시리즈 가상 컴퓨터에 대한 InstanceSize 값은 [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](https://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

관리되는 도메인에 대한 정보를 제공합니다.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

클라우드 서비스의 이름을 지정합니다.

    $svcname="Contoso100-test"

VM이 가입되는 가상 네트워크의 이름을 지정합니다. AAD-DS 관리되는 도메인을 이 가상 네트워크에서 사용할 수 있는지 확인합니다.

    $vnetname="MyPreviewVnet"

VM을 프로비전하는 데 사용할 VM 이미지를 선택합니다.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

VM을 구성합니다. VM 이름, 인스턴스 크기 및 사용할 이미지를 설정합니다.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

VM에 대한 로컬 관리자 자격 증명을 가져옵니다. 강력한 로컬 관리자 암호를 선택합니다. 암호 강도를 확인하려면 [암호 검사기: 강력한 암호 사용](https://www.microsoft.com/security/pc-security/password-checker.aspx)을 참조하세요.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

VM이 관리되는 도메인에 가입하려면 'AAD DC 관리자' 그룹에 속하는 사용자 계정의 자격 증명을 얻습니다. 도메인 이름을 지정하지 않습니다. 예를 들어 예제에서는 'bob'을 사용자 이름으로 지정합니다.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

VM을 구성합니다. 도메인 가입 요구 사항 및 필요한 자격 증명을 지정합니다.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

VM에 대한 서브넷을 설정합니다.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

선택 사항: 도메인의 IP 주소를 가리키도록 합니다. 가상 네트워크에 대한 DNS 서버가 되도록 Azure AD 도메인 서비스 관리되는 도메인의 IP 주소를 설정하는 경우 이 단계가 필요하지 않습니다.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

이제 도메인에 가입된 Windows VM을 프로비전합니다.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## Windows VM을 프로비전하고 AAD 도메인 서비스 관리되는 도메인에 자동으로 가입하는 스크립트
이 PowerShell 명령 집합은 다음과 같은 LOB(기간 업무) 서버용 가상 컴퓨터를 만듭니다.

- Windows Server 2012 R2 Datacenter 이미지를 사용함
- 매우 작은 가상 컴퓨터임
- 이름이 contoso-test임
- contoso100 관리되는 도메인에 자동으로 가입된 도메인임
- 관리되는 도메인과 동일한 가상 네트워크에 추가됨

Windows 가상 컴퓨터를 만들고 자동으로 Azure AD 도메인 서비스 관리되는 도메인에 가입하는 전체 샘플 스크립트는 다음과 같습니다.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## 관련 콘텐츠
- [Azure AD 도메인 서비스 - 시작 가이드](./active-directory-ds-getting-started.md)

- [Azure AD 도메인 서비스 관리되는 도메인 관리](./active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0921_2016-->