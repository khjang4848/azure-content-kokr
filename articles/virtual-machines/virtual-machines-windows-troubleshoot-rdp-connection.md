<properties
	pageTitle="Azure VM에 원격 데스크톱 연결 문제 해결 | Microsoft Azure"
	description="Azure VM에 액세스할 수 없으면 빠른 RDP 문제 해결 단계, 오류 메시지별 도움말 및 자세한 네트워크 문제 해결 단계를 확인하세요."
	keywords="원격 데스크톱 오류,원격 데스크톱 연결 오류,VM에 연결할 수 없습니다,원격 데스크톱 문제 해결"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Windows를 실행하는 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결

Windows 기반 Azure VM(가상 컴퓨터)에 RDP(원격 데스크톱 프로토콜) 연결은 여러 이유로 실패하여 VM에 액세스하지 못할 수 있습니다. 이러한 문제는 VM의 원격 데스크톱 서비스, 네트워크 연결 또는 호스트 컴퓨터의 원격 데스크톱 클라이언트에서 발생할 수 있습니다. 이 문서는 RDP 연결 문제를 해결하기 위한 가장 일반적인 방법 중 일부를 안내합니다. 발생한 문제가 여기에 표시되지 않거나 여전히 RDP를 통해 VM에 연결할 수 없으면 [좀 더 자세한 RDP 문제 해결 개념 및 단계](virtual-machines-windows-detailed-troubleshoot-rdp.md)를 읽어볼 수 있습니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 선택합니다.

<a id="quickfixrdp"></a>

## 빠른 문제 해결 단계
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. Azure 포털 또는 Azure PowerShell을 사용하여 원격 액세스를 다시 설정합니다.
2. VM을 다시 시작합니다.
3. VM을 다시 배포합니다.
4. 네트워크 보안 그룹/클라우드 서비스 끝점 규칙을 확인합니다.
5. Azure 포털 또는 Azure PowerShell에서 VM 콘솔 로그를 검토합니다.
6. Azure 포털에서 VM 리소스 상태를 확인합니다.
7. VM 암호를 다시 설정합니다.

자세한 단계가 필요한 경우 뒷부분에서 Resource Manager와 클래식 배포 모델에 대한 설명을 찾을 수 있습니다.


<a id="fix-common-remote-desktop-errors"></a>
## 리소스 관리자 배포 모델을 사용하여 만든 VM 문제 해결

각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

> [AZURE.TIP] 포털에서 '연결' 단추가 회색으로 표시되고 [Express 경로](../expressroute/expressroute-introduction.md) 또는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 연결을 통해 Azure에 연결되지 않는 경우 RDP를 사용하려면 먼저 공용 IP 주소를 만들고 VM에 할당해야 합니다. 자세한 내용은 [Azure의 공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md)에서 확인할 수 있습니다.

1. PowerShell을 사용하여 원격 액세스를 다시 설정합니다.
	- 아직 작업 전이면 [최신 Azure PowerShell을 설치하고 구성](../powershell-install-configure.md)합니다.

	- 다음 PowerShell 명령 중 하나를 사용하여 RDP 연결을 다시 설정합니다. `myRG`, `myVM`, `myVMAccessExtension` 및 위치를 설치에 관련된 값으로 대체합니다.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	또는

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] 앞의 예제에서 `myVMAccessExtension` 또는 `MyVMAccess`는 프로세스의 일부로 설치되는 새 확장에 지정한 이름입니다. 종종 VM의 이름으로 설정됩니다. VMAccessAgent로 이전에 작업한 경우 `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"`을 사용하여 기존 확장의 이름을 가져와서 VM의 속성을 확인할 수 있습니다. 출력의 'Extensions' 섹션에서 이름을 확인합니다. VM에는 하나의 VMAccessAgent만 존재할 수 있으므로 `Set-AzureRmVMExtension`을 사용하는 경우 `-ForceReRun True` 매개 변수도 추가하여 에이전트를 다시 등록해야 합니다.

2. 다른 시작 문제를 해결하기 위해 VM을 다시 시작합니다. **찾아보기** > **가상 컴퓨터** > *VM* > **다시 시작**을 선택합니다.

3. [새 Azure 노드에 VM을 다시 배포](virtual-machines-windows-redeploy-to-new-node.md)합니다.

	이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
	
4. [네트워크 보안 그룹 규칙](../virtual-network/virtual-networks-nsg.md)이 RDP 트래픽을 허용하는지 확인합니다(TCP 포트 3389).

5. VM의 콘솔 로그 또는 스크린샷을 검토하여 부팅 문제를 해결합니다. **찾아보기** > **가상 컴퓨터** > *Windows 가상 컴퓨터* > **지원 + 문제 해결** > **진단 부팅**을 선택합니다.

6. [VM 암호를 다시 설정](virtual-machines-windows-reset-rdp.md)합니다.

RDP 문제가 계속 발생하는 경우 [지원 요청을 열거나](https://azure.microsoft.com/support/options/) [좀 더 자세한 RDP 문제 해결 개념 및 단계](virtual-machines-windows-detailed-troubleshoot-rdp.md)를 읽어볼 수 있습니다.


## 클래식 배포 모델을 사용하여 만든 VM 문제 해결

각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. [Azure 포털](https://portal.azure.com)에서 원격 데스크톱 서비스를 초기화합니다. **찾아보기** > **가상 컴퓨터(클래식)** > *VM* > **원격 액세스 다시 설정**을 선택합니다.

2. 다른 시작 문제를 해결하기 위해 VM을 다시 시작합니다. **찾아보기** > **가상 컴퓨터(클래식)** > *VM* > **다시 시작**을 선택합니다.

3. [새 Azure 노드에 VM을 다시 배포](virtual-machines-windows-redeploy-to-new-node.md)합니다.

	이 작업이 완료되면 임시 디스크 데이터가 손실되고 가상 컴퓨터와 연결된 동적 IP 주소가 업데이트됩니다.
	
4. [클라우드 서비스 끝점이 RDP 트래픽을 허용](../cloud-services/cloud-services-role-enable-remote-desktop.md)하는지 확인합니다.

5. VM의 콘솔 로그 또는 스크린샷을 검토하여 부팅 문제를 해결합니다. **찾아보기** > **가상 컴퓨터(클래식)** > *VM* > **설정** > **진단 부팅**을 선택합니다.

6. VM 리소스 상태에 플랫폼 문제가 있는지 확인합니다. **찾아보기** > **가상 컴퓨터(클래식)** > *VM* > **설정** > **상태 검사**를 선택합니다.

7. [VM 암호를 다시 설정](virtual-machines-windows-reset-rdp.md)합니다.

RDP 문제가 계속 발생하는 경우 [지원 요청을 열거나](https://azure.microsoft.com/support/options/) [좀 더 자세한 RDP 문제 해결 개념 및 단계](virtual-machines-windows-detailed-troubleshoot-rdp.md)를 읽어볼 수 있습니다.


## 특정한 원격 데스크톱 연결 오류 문제 해결

RDP를 통해 VM에 연결하려고 할 때 특정 오류가 나타날 수 있습니다. 다음은 가장 일반적인 오류 메시지입니다.

- [라이선스를 제공할 수 있는 원격 데스크톱 라이선스 서버가 없으므로 원격 세션이 끊겼습니다](#rdplicense).

- [원격 데스크톱에서 컴퓨터 "이름"을 찾을 수 없습니다](#rdpname).

- [인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.](#rdpauth)

- [Windows 보안 오류: 자격 증명이 작동하지 않습니다](#wincred).

- [이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다](#rdpconnect).

<a id="rdplicense"></a>
### 라이선스를 제공할 수 있는 원격 데스크톱 라이선스 서버가 없으므로 원격 세션이 끊겼습니다.

원인: 원격 데스크톱 서버 역할에 대한 120일 라이선스 유예 기간이 만료되었고 라이선스를 설치해야 합니다.

대안으로 포털에서 RDP 파일의 로컬 복사본을 저장하고 PowerShell 명령 프롬프트에서 이 명령을 실행하여 연결합니다. 다음과 같이 해당 연결에만 라이선스를 사용할 수 없게 됩니다.

		mstsc <File name>.RDP /admin

VM에 실제로 두 개 이상의 동시 원격 데스크톱 연결이 필요하지 않을 경우 서버 관리자를 사용하여 원격 데스크톱 서버 역할을 제거할 수 있습니다.

자세한 내용은 ["사용 가능한 원격 데스크톱 라이선스 서버가 없음"과 함께 Azure VM이 실패하는 경우](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/) 블로그 게시물을 참조하세요.

<a id="rdpname"></a>
### 원격 데스크톱에서 컴퓨터 "name"을 찾을 수 없습니다.

원인: 컴퓨터의 원격 데스크톱 클라이언트가 RDP 파일의 설정에 있는 컴퓨터의 이름을 확인할 수 없습니다.

가능한 해결 방법:

- 조직 인트라넷을 사용하는 경우 컴퓨터가 프록시 서버에 액세스할 수 있고 HTTPS 트래픽을 보낼 수 있는지 확인합니다.

- 로컬 컴퓨터에 저장된 RDP 파일을 사용하는 경우 포털에서 생성한 파일을 사용해 보세요. 이때 가상 컴퓨터 또는 클라우드 서비스 및 VM의 끝점 포트에 대한 올바른 DNS 이름을 사용해야 합니다. 포털에서 생성된 RDP 파일의 샘플은 다음과 같습니다.

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

이 RDP 파일의 주소 부분에는
- VM을 포함하는 클라우드 서비스의 정규화된 도메인 이름(이 예제에서는 "tailspin azdatatier.cloudapp.net")이 있습니다.

- 원격 데스크톱 트래픽에 대한 끝점의 외부 TCP 포트(55919)

<a id="rdpauth"></a>
### 인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.

원인: 대상 VM이 사용자의 자격 증명의 사용자 이름 부분에서 보안 기관을 찾지 못했습니다.

사용자 이름이 *SecurityAuthority*\\*UserName* (example: CORP\\User1) 형식인 경우 *SecurityAuthority* 부분은 VM의 컴퓨터 이름(로컬 보안 기관)이거나 Active Directory 도메인 이름입니다.

가능한 해결 방법:

- 계정이 VM의 로컬 계정인 경우 VM 이름의 철자가 올바른지 확인합니다.

- 계정이 Active Directory 도메인에 있는 경우 도메인 이름의 철자를 확인합니다.

- 사용자 계정이 Active Directory 도메인 계정이고 해당 도메인 이름 철자가 올바르게 입력된 경우, 해당 도메인에서 도메인 컨트롤러를 사용할 수 있는지 확인합니다. 도메인 컨트롤러가 시작되지 않았기 때문에 사용할 수 없는 것은 도메인 컨트롤러가 포함된 Azure 가상 네트워크에서 일반적인 문제입니다. 해결 방법으로 도메인 계정 대신 로컬 관리자 계정을 사용할 수 있습니다.

<a id="wincred"></a>
### Windows 보안 오류: 자격 증명이 작동하지 않습니다.

원인: 대상 VM에서 계정 이름 및 암호의 유효성을 확인할 수 없습니다.

Windows 기반 컴퓨터는 로컬 계정 또는 도메인 계정 자격 증명의 유효성을 검사할 수 있습니다.

- 로컬 계정의 경우 *ComputerName*\\*UserName* 구문(예: SQL1\\Admin4798)을 사용합니다.
- 도메인 계정의 경우 *DomainName*\\*UserName* 구문(예: CONTOSO\\peterodman)을 사용합니다.

VM을 새 Active Directory 포리스트의 도메인 컨트롤러로 승격한 경우 사용자가 로그인할 때 사용한 로컬 관리자 계정이 새 포리스트 및 도메인과 같은 암호를 가진 동일한 계정으로 변환됩니다. 그러면 로컬 계정이 삭제됩니다.

예를 들어 DC1\\DCAdmin 로컬 계정으로 로그인하고 가상 컴퓨터를 corp.contoso.com 도메인에 대한 새 포리스트의 도메인 컨트롤러로 승격하는 경우 DC1\\DCAdmin 로컬 계정이 삭제되고 같은 암호를 가진 새 도메인 계정 CORP\\DCAdmin이 생성됩니다.

계정 이름이 가상 컴퓨터가 유효한 계정으로 확인할 수 있는 이름이고 암호가 올바른지 확인하십시오.

로컬 관리자 계정의 암호를 변경해야 하는 경우 [Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법](virtual-machines-windows-reset-rdp.md)을 참조하세요.

<a id="rdpconnect"></a>
### 이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다.

원인: 연결에 사용한 계정에 원격 데스크톱 로그인 권한이 없습니다.

모든 Windows 컴퓨터에는 원격으로 로그인할 수 있는 계정 및 그룹을 포함하는 원격 데스크톱 사용자 로컬 그룹이 있습니다. 로컬 관리자 그룹의 구성원도 액세스 권한을 갖고 있지만 원격 데스크톱 사용자 로컬 그룹에 표시되지 않은 계정도 있습니다. 도메인에 가입된 컴퓨터의 경우 해당 도메인의 도메인 관리자도 로컬 관리자 그룹에 포함됩니다.

연결하는 데 사용하는 계정에 원격 데스크톱 로그인 권한이 있는지 확인합니다. 해결 방법으로 도메인 또는 로컬 관리자 계정을 사용하여 원격 데스크톱을 통해 연결합니다. 원격 데스크톱 사용자 로컬 그룹에 원하는 계정을 추가하려면 Microsoft Management Console 스냅인(**시스템 도구 > 로컬 사용자 및 그룹 > 그룹 > 원격 데스크톱 사용자**)을 사용합니다.

## 일반 원격 데스크톱 오류 문제 해결

이러한 오류가 발생하지 않았는데도 여전히 원격 데스크톱을 통해 VM에 연결할 수 없는 경우 [원격 데스크톱에 대한 자세한 문제 해결 가이드](virtual-machines-windows-detailed-troubleshoot-rdp.md)를 읽어보세요.


## 추가 리소스

[Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법](virtual-machines-windows-reset-rdp.md)

[Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)

[Linux 기반 Azure 가상 컴퓨터에 Secure Shell 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0907_2016-->
