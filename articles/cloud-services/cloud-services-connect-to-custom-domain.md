<properties
  pageTitle="클라우드 서비스를 사용자 지정 도메인 컨트롤러에 연결 | Microsoft Azure"
  description="PowerShell 및 AD 도메인 확장을 사용하여 사용자 지정 AD 도메인에 웹/작업자 역할을 연결하는 방법을 알아봅니다."
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# Azure 클라우드 서비스 역할을 Azure에서 호스팅되는 사용자 지정 AD 도메인 컨트롤러에 연결

먼저 Azure에서 가상 네트워크(VNet)를 설정합니다. 그런 다음 VNet에 Active Directory 도메인 컨트롤러(Azure 가상 컴퓨터에서 호스팅되는)를 추가합니다. 그런 다음, 기존 클라우드 서비스 역할을 사전에 만든 VNet에 추가한 후 도메인 컨트롤러에 연결합니다.

시작하기 전에 다음 몇 가지를 유의하십시오.

1.	이 자습서에서는 PowerShell을 사용하므로, Azure PowerShell이 설치되어 있고 사용할 수 있는지 확인하세요. Azure PowerShell 설정에 대한 도움을 얻으려면 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

2.	사용자의 AD 도메인 컨트롤러와 웹/작업자 역할 인스턴스는 VNet에 있어야 합니다.

이 단계별 가이드를 따르고 문제가 발생하는 경우 아래에 의견을 남겨 주세요. 담당자가 연락드립니다(물론, 저희는 여러분의 의견을 확인합니다.).

## 가상 네트워크 만들기

Azure 클래식 포털 또는 PowerShell을 사용하여 Azure에서 가상 네트워크를 만들 수 있습니다. 이 자습서에서는 PowerShell을 사용합니다. Azure 클래식 포털을 사용하여 가상 네트워크를 만들려면 [가상 네트워크 만들기](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)를 참조하세요.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## 가상 컴퓨터 만들기

가상 네트워크 설정을 완료한 후에 AD 도메인 컨트롤러를 만들어야 합니다. 이 자습서는 Azure 가상 컴퓨터에서 AD 도메인 컨트롤러를 설정합니다.

이 작업을 수행하려면 아래 명령을 사용하여 PowerShell을 통해 가상 컴퓨터를 만듭니다.

```powershell
# Initialize variables

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## 가상 컴퓨터를 도메인 컨트롤러로 승격
가상 컴퓨터를 AD 도메인 컨트롤러로 구성하려면 VM에 로그인하여 구성해야 합니다.

VM에 로그인하려면 PowerShell을 통해 RDP 파일을 가져올 수 있으며 아래 명령을 사용합니다.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

VM에 로그인한 후 [고객 AD 도메인 컨트롤러 설치 방법에 대한 단계별 가이드](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)에 따라 가상 컴퓨터를 AD 도메인 컨트롤러로 설정합니다.

## 가상 네트워크에 클라우드 서비스 추가

그런 다음 방금 만든 VNet에 클라우드 서비스 배포를 추가해야 합니다. 이 작업을 수행하려면 Visual Studio 또는 원하는 편집기를 사용하여 cscfg에 관련 섹션을 추가하여 클라우드 서비스 cscfg를 수정합니다.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

그런 다음 클라우드 서비스 프로젝트를 빌드하고 Azure에 배포합니다. 서비스 패키지를 Azure에 클라우드 배포에 대한 도움을 얻으려면 [클라우드 서비스 만들기 및 배포 방법](cloud-services-how-to-create-deploy.md#deploy)을 참조하세요.

## 웹/작업자 역할을 도메인에 연결

Azure에서 클라우드 서비스 프로젝트가 배포되면 AD 도메인 확장명을 사용하여 사용자의 역할 인스턴스가 사용자 지정 AD 도메인에 연결합니다. AD 도메인 확장명을 기존 클라우드 서비스 배포에 추가하고 사용자 지정 도메인에 가입하려면 PowerShell에서 다음 명령을 실행합니다.

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

지금까지 전반적인 내용을 알아보았습니다.

클라우드 서비스가 이제 사용자 지정 도메인 컨트롤러에 가입되어 있어야 합니다. AD 도메인 확장을 구성하는 방법에 대해 사용할 수 있는 다른 옵션에 대한 자세한 내용을 보려면, 아래와 같이 PowerShell 도움말을 사용합니다.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

가상 컴퓨터를 도메인 컨트롤러로 승격하는 확장명이 유용한 경우, 여러분의 피드백을 주세요. 유용하다고 생각하시는 경우, 저희들이 알 수 있도록 의견 섹션에 의견을 남겨 주세요.

<!---HONumber=AcomDC_0914_2016-->