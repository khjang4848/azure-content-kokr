<properties
   pageTitle="Windows 및 Linux IaaS VM용 Azure 디스크 암호화 | Microsoft Azure"
   description="이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure 디스크 암호화에 대한 개요를 제공합니다."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="devtiw"/>


#Windows 및 Linux IaaS VM용 Azure 디스크 암호화

Microsoft Azure는 데이터 프라이버시, 데이터 독립성을 보장하기 위해 노력하고 있으며 암호화 키를 암호화, 제어 및 관리하고 데이터 액세스를 제어 및 감사하는 광범위한 고급 기술을 통해 Azure 호스팅 데이터를 제어할 수 있도록 합니다. 또한 Azure 고객에게 비즈니스 요구에 가장 잘 맞는 솔루션을 선택할 수 있는 유연성을 제공합니다. 이 문서에는 "Windows 및 Linux IaaS VM용 Azure 디스크 암호화"라는 새로운 기술 솔루션을 소개하여 조직의 보안 및 규정 준수 약정에 따라 데이터를 보호하도록 합니다. 이 문서에서는 지원되는 시나리오와 사용자 환경을 비롯하여 Azure 디스크 암호화 기능을 사용하는 방법에 대한 자세한 지침을 제공합니다.

**참고**: 여기에 포함된 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생할 수 있습니다.

## 개요

Azure 디스크 암호화는 Windows 및 Linux IaaS 가상 컴퓨터 디스크를 암호화할 수 있도록 하는 새로운 기능입니다. Azure 디스크 암호화는 업계 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 이 솔루션은 [Azure 주요](https://azure.microsoft.com/documentation/services/key-vault/) 자격 증명 모음과 함께 통합되어 주요 자격 증명 모음 구독에서 디스크 암호화 키 및 암호를 제어하고 관리할 수 있도록 하며 가상 컴퓨터 디스크의 모든 휴지 상태 데이터가 Azure 저장소에서 암호화되도록 보장합니다.

> [AZURE.NOTE] Windows IaaS VM용 Azure 디스크 암호화는 지금 [일반 공급](https://blogs.msdn.microsoft.com/azuresecurity/2016/05/20/azure-disk-encryption-for-windows-virtual-machines-reaches-general-availability/) 상태입니다.

### 암호화 시나리오

Azure 디스크 암호화 솔루션은 다음의 고객 시나리오를 지원합니다.

- 미리 암호화된 VHD 및 암호화 키에서 만든 새 IaaS VM에서 암호화 사용
- Azure 갤러리 이비지에서 만든 새 IaaS VM에서 암호화 사용
- Azure에서 이미 실행 중인 기존 IaaS VM에서 암호화 사용
- Windows IaaS VM에서 암호화 사용 안 함

솔루션은 Microsoft Azure에서 활성화된 경우 IaaS VM에 대해 다음을 지원합니다.

- Azure 주요 자격 증명 모음과 통합

- 표준 [A, D 및 G 시리즈 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)

- Windows 및 Linux IaaS VM에서 암호화 사용

- Windows IaaS VM에서 암호화 사용 안 함

- Windows 클라이언트 OS를 실행하는 IaaS VM에서 암호화 사용

- 탑재 경로가 있는 볼륨에서 암호화 사용

- 모든 Azure 공용 지역은 공개 미리 보기에서 지원되며 Azure 오스트레일리아 지역은 Windows IaaS VM용 GA에서 지원됩니다.

솔루션은 릴리스에서 다음 시나리오, 기능 및 기술을 지원하지 않습니다.

- 기본 VM 및 표준 DS(프리미엄 저장소) 시리즈 IaaS VM

- 클래식 VM 만들기 메서드를 사용하여 만든 IaaS VM

- Azure에서 이미 실행 중인 Linux IaaS VM에서 OS 디스크 암호화 사용

- Azure 디스크 암호화를 통해 설정된 Linux IaaS VM에서 암호화 사용 안 함

- 온-프레미스 키 관리 서비스와의 통합

- Windows Server 2016 Technical Preview 3 이상

- Azure 파일(Azure 파일 공유), NFS(네트워크 파일 시스템), 동적 볼륨, 소프트웨어 기반 RAID 시스템


### 암호화 기능

Azure IaaS VM에 대한 Azure 디스크 암호화를 사용하도록 설정하고 배포할 때 제공된 구성에 따라 다음 기능이 활성화됩니다.

- 고객 저장소에서 휴지 상태 부팅 볼륨을 보호하기 위해 OS 볼륨의 암호화

	- Azure에서 이미 실행 중인 Linux IaaS VM에서 OS 볼륨의 암호화는 현재 지원되지 않습니다. Linux IaaS VM용 OS 볼륨의 암호화는 미리 암호화된 VHD 시나리오에 대해서만 지원됩니다.
	
- 고객 저장소에서 휴지 상태 데이터 볼륨을 보호하기 위해 데이터 볼륨의 암호화

- Windows IaaS VM에서 암호화 사용 안 함

- 고객 Azure 주요 자격 증명 모음 구독에서 암호화 키 및 암호 보호

- 암호화된 IaaS VM의 암호화 상태 보고

- IaaS 가상 컴퓨터에서 디스크 암호화 구성 설정 제거

Windows 및 Linux 솔루션용 IaaS VM의 Azure 디스크 암호화에는 Windows용 디스크 암호화 확장, Linux용 디스크 암호화 확장, 디스크 암호화 PowerShell cmdlet, 디스크 암호화 CLI cmdlet 및 디스크 암호화 Azure 리소스 관리자 템플릿이 포함됩니다. Azure 디스크 암호화 솔루션은 Windows 또는 Linux OS를 실행하는 IaaS VM에서 지원됩니다. 지원되는 운영 체제에 대한 자세한 내용은 아래의 필수 구성 요소 섹션을 참조하세요.

**참고:** Azure 디스크 암호화로 VM 디스크를 암호화하는 작업에 대한 요금은 부과되지 않습니다.

### 가치 제안

Azure 디스크 암호화 관리 솔루션을 사용하면 클라우드에서 다음 비즈니스 요구 사항이 충족됩니다.

-   업계 표준 암호화 기술을 사용하여 휴지 상태의 IaaS VM이 보호되어 조직의 보안 및 규정 준수 요구 사항을 충족합니다.

-   IaaS VM은 고객이 제어하는 키 및 정책에 따라 부팅되며 주요 자격 증명 모음에서 이러한 사용을 감사할 수 있습니다.


### 암호화 워크플로
Windows 및 Linux VM용 디스크 암호화를 사용하도록 설정하는 데 필요한 대략적인 단계입니다.

1. 고객은 위의 3개 암호화 시나리오 중에서 암호화 시나리오를 선택합니다.

2. 고객은 Azure 디스크 암호화 ARM 템플릿, PS cmdlet 또는 CLI 명령을 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

    - 고객 암호화 VHD 시나리오의 경우 고객은 암호화된 VHD를 자신의 저장소 계정에, 암호화 키 자료를 주요 자격 증명 모음에 업로드하고 암호화 구성을 제공하여 새 IaaS VM에서 암호화를 사용하도록 합니다.

    - Azure 갤러리에서 만든 새 VM과 Azure에서 이미 실행 중인 기존의 VM의 경우 고객은 암호화 구성을 제공하여 IaaS VM에서 암호화를 사용하도록 합니다.

3. 고객은 Azure 플랫폼에 대한 액세스를 부여하여 주요 자격 증명 모음에서 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 읽고 IaaS VM에서 암호화를 사용하도록 합니다.

4. 고객은 암호화 키 자료를 주요 자격 증명 모음에 쓰도록 Azure AD ID를 제공하여 위의 시나리오 2와 3에 대한 IaaS VM에서 암호화를 사용하도록 합니다.

5.  Azure 서비스 관리는 암호화 및 주요 자격 증명 모음 구성으로 VM 서비스 모델을 업데이트하고 고객을 위해 암호화된 VM을 프로비전합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig1.JPG)

### 암호 해독 워크플로

Windows IaaS VM용 디스크 암호화를 사용하지 않도록 설정하는 데 필요한 대략적인 단계입니다.

1. 고객이 Azure 디스크 암호화 ARM 템플릿 또는 PS cmdlet을 통해 Azure에서 실행 중인 Windows IaaS VM에 암호화(암호 해독)를 사용하지 않도록 선택하고 암호 해독 구성을 지정할 수 있습니다.

2. 암호화 사용 안 함 단계는 Windows IaaS VM에 대해서만 지원되고 Linux IaaS VM에서는 지원되지 않습니다.

3. 암호화 사용 안 함 단계는 실행 중인 Windows IaaS VM에서 OS 또는 데이터 볼륨이나 둘 모두의 암호화를 사용하지 않습니다.

4. Azure 서비스 관리는 VM 서비스 모델을 업데이트하고 Windows IaaS VM은 암호가 해독되었다고 표시합니다. VM의 콘텐츠는 더 이상 휴지 상태로 암호화되지 않습니다.

5. 암호화 사용 안 함은 Windows에 대한 BitLocker 암호화 키 및 Linux에 대한 암호인 고객 키 자격 증명 모음 및 암호화 키 자료를 삭제하지 않습니다.

## 필수 조건

다음은 개요 섹션에 설명된 지원되는 시나리오에 대해 Azure IaaS VM에서 Azure 디스크 암호화를 사용하도록 설정하기 위한 필수 조건입니다.

- 사용자는 유효한 활성 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.

- Azure 디스크 암호화는 다음 Windows Server SKU - Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2에서 지원됩니다. 솔루션은 Windows Server 2008 운영 체제에서 지원되지 않습니다. Windows Server 2016 Technical Preview 3은 이 릴리스에서 지원되지 않습니다.

Azure 디스크 암호화는 다음 Windows 클라이언트 SKU의 Windows 8 클라이언트 및 Windows 10 클라이언트에서 지원됩니다.

**참고**: Windows Server 2008 R2의 경우 Azure에서 암호화를 사용하도록 설정하기 전에 .Net framework 4.5를 설치해야 합니다. 선택적 업데이트인 "Windows Server 2008 R2 x64 기반 시스템용 Microsoft .NET Framework 4.5.2([KB2901983](https://support.microsoft.com/kb/2901983))"를 설치하여 Windows 업데이트에서 설치할 수 있습니다.

- Azure 디스크 암호화는 다음 Linux Server SKU - Ubuntu, CentOS, SUSE 및 SLES(SUSE Linux Enterprise Server)와 Red Hat Enterprise Linux에서 지원됩니다.

- 모든 리소스(예: 주요 자격 증명 모음, 저장소 계정, VM 등)는 같은 Azure 지역 및 구독에 속해야 합니다.

**참고** -Azure 디스크 암호화는 키 자격 증명 모음 및 VM이 동일한 Azure 지역에 있어야 합니다. 별도 하위 지역에서 구성하면 Azure 디스크 암호화 기능의 사용에 오류가 발생합니다.

- Azure 디스크 암호화 사용에 대해 Azure 주요 자격 증명 모음을 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure 디스크 암호화 사용에 대한 Azure 주요 자격 증명 모음 설정 및 구성**을 참조하세요.

- Azure 디스크 암호화 사용에 대해 Azure Active Directory의 Azure AD 응용 프로그램을 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure Active Directory에서 Azure AD 응용 프로그램 설치**를 참조하세요.

- Azure AD 응용 프로그램에 대한 주요 자격 증명 모음 액세스 정책을 설정하고 구성하려면 이 문서의 *필수 조건* 섹션에서 **Azure AD 응용 프로그램에 대한 주요 자격 증명 모음 액세스 정책 설정**을 참조하세요.

- 미리 암호화된 Windows VHD를 준비하려면 이 문서의 부록에서 **미리 암호화된 Windows VHD 준비** 섹션을 참조하세요.

- 미리 암호화된 Linux VHD를 준비하려면 이 문서의 부록에서 **미리 암호화된 Linux VHD 준비** 섹션을 참조하세요.

- Azure 플랫폼은 VM을 부팅하고 가상 컴퓨터 OS 볼륨을 해독할 수 있도록 하기 위해 고객 Azure 키 자격 증명 모음에서 암호화 키 또는 암호에 액세스해야 합니다. Azure 플랫폼에서 주요 자격 증명 모음에 액세스하도록 권한을 부여하려면 다음 요구 사항에 따라 주요 자격 증명 모음에서 **enabledForDiskEncryption** 속성을 설정해야 합니다. 자세한 내용은 이 문서의 부록에서 **Azure 디스크 암호화 사용에 대한 Azure 키 자격 증명 설정 및 구성** 섹션을 참조하세요.

- 주요 자격 증명 모음 암호 및 주요 암호화 키(KEK) URL 버전을 지정해야 합니다. Azure 서비스 관리에서 이 버전 관리 제한을 적용합니다. 유효한 암호와 KEK URL은 아래 예제를 참조하세요.

	- 유효한 암호 URL의 예:

		*https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- 유효한 KRK KEK의 예:

		*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Azure 디스크 암호화는 주요 자격 증명 모음 암호 및 KEK URL의 일부로 지정되는 포트 번호를 지원하지 않습니다. 지원되는 주요 자격 증명 모음 URL은 아래 예제를 참조하세요.

 	- 허용되지 않은 주요 자격 증명 모음 URL

		*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

	- 허용되는 주요 자격 증명 모음 URL:

		*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

- Azure 디스크 암호화 기능을 사용하도록 설정하려면 IaaS VM이 다음 네트워크 끝점 구성 요구 사항을 충족해야 합니다.

	- Azure 주요 자격 증명 모음에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 끝점 [Login.windows.net]에 연결할 수 있어야 합니다.

	- 암호화 키를 고객 주요 자격 증명 모음에 쓰려면 IaaS VM에서 Azure 주요 자격 증명 모음 끝점에 연결할 수 있어야 합니다.

	- IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure 저장소 끝점 및 VHD 파일을 호스팅하는 Azure 저장소 계정에 연결할 수 있어야 합니다.

**참고:** 보안 정책이 Azure VM에서 인터넷에 액세스를 제한하는 경우 연결이 필요하고 특정 규칙을 구성하여 IP에 대한 아웃 바운드 연결을 허용하는 위의 URI를 해결할 수 있습니다.

- Azure PowerShell SDK의 최신 버전을 사용하여 Azure 디스크 암호화를 구성합니다. 최신 버전의 [Azure PowerShell 버전 1.3.0](https://github.com/Azure/azure-powershell/releases/download/v1.3.0-March2016/azure-powershell.1.3.0.msi) 이상을 다운로드합니다.

**참고:** Azure 디스크 암호화는 [Azure PowerShell SDK 버전 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016)에서 지원되지 않습니다. Azure PowerShell 1.1.0을 사용하는 경우 관련된 오류가 나타나면 [Azure PowerShell 1.1.0과 관련된 Azure 디스크 암호화 오류](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) 문서를 참고하세요.

- Azure CLI 명령을 실행하고 Azure 구독에 연결하려면 먼저 Azure CLI 버전을 설치해야 합니다.

	- Azure CLI를 설치하고 Azure 구독에 연결하려면 [Azure CLI 설치 및 구성하는 방법](../xplat-cli-install.md)을 참조하세요.

	- Azure 리소스 관리자에서 Mac, Linux 및 Windows용 Azure CLI 사용에 대한 내용은 [여기](azure-cli-arm-commands.md)를 참조하세요.

- Azure 디스크 암호화 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. VM이 가입된 도메인인 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 마십시오. "호환되는 TPM 없이 BitLocker 허용"을 위한 자세한 그룹 정책은 [이 문서](https://technet.microsoft.com/library/ee706521)를 참조하세요.

- Azure AD 응용 프로그램을 만들고 새로운 주요 자격 증명 모음을 만들거나 기존 주요 자격 증명 모음을 설정하고 암호화를 활성화하는 Azure 디스크 암호화 필수 요소 PowerShell 스크립트는 [여기](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)에 있습니다.

### Azure 디스크 암호화 사용을 위해 Azure 주요 자격 증명 모음 설정 및 구성

Azure 디스크 암호화는 Azure 주요 자격 증명 모음에서 디스크 암호화 키 및 암호를 보호합니다. 아래 각 섹션의 단계에 따라 Azure 디스크 암호화 사용을 위해 주요 자격 증명 모음을 설정합니다.

#### 새 주요 자격 증명 모음 만들기
새 주요 자격 증명 모음을 만들려면 아래에 나열된 두 옵션 중 하나를 사용합니다.

- [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)에 있는 "101-Create-KeyVault" ARM 템플릿 사용
- Azure PowerShell 주요 자격 증명 모음 cmdlet 사용

**참고:** 구독에 대한 주요 자격 증명 모음 설정이 이미 있는 경우 다음 섹션을 진행하세요.

#### 주요 암호화 키 프로비전(선택 사항)

BitLocker 암호화 키를 래핑하기 위한 추가 보안 계층으로 주요 암호화 키(KEK)를 사용하려는 경우 프로비전 프로세스에 사용하기 위해 KEK를 주요 자격 증명 모음에 추가해야 합니다. [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) cmdlet을 사용하여 주요 자격 증명 모음에 새 주요 암호화 키를 만듭니다. 자세한 내용은 [주요 자격 증명 모음 설명서](https://azure.microsoft.com/documentation/services/key-vault/)를 참조하세요.

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

#### Azure 플랫폼에서 키와 암호에 액세스할 수 있도록 주요 자격 증명 모음 권한을 설정합니다.

Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 주요 자격 증명 모음의 암호화 키 또는 암호에 액세스해야 합니다. Azure 플랫폼에 주요 자격 증명 모음에 액세스할 수 있는 권한을 부여하려면 주요 자격 증명 모음에서 *enabledForDiskEncryption* 속성을 설정해야 합니다. 주요 자격 증명 모음 PS cmdlet을 사용하여 주요 자격 증명 모음에 enabledForDiskEncryption 속성을 설정할 수 있습니다.

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

앞서 설명한 것처럼 주요 자격 증명 모음에 *enabledForDiskEncryption* 속성을 설정해야 합니다. https://resources.azure.com을 방문하여 속성을 설정할 수 있습니다. 위에서 자세히 설명된 속성이 바르게 설정되었는지 확인합니다. 그렇지 않은 경우 배포가 실패합니다.

#### Azure Active Directory에서 Azure AD 응용 프로그램 설정

Azure에서 실행 중인 VM에서 암호화를 사용하도록 설정해야 하는 경우 Azure 디스크 암호화가 암호화 키를 생성하고 주요 자격 증명 모음에 기록합니다. 주요 자격 증명 모음에서 암호화 키를 관리하려면 Azure AD 인증이 필요합니다.

이런 목적으로 Azure AD 응용 프로그램이 만들어집니다. 응용 프로그램을 등록하는 자세한 단계는 이 [블로그 게시물](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)의 "응용 프로그램 ID 가져오기" 섹션에서 확인할 수 있습니다. 이 게시물에는 주요 자격 증명 모음 프로비전 및 구성에 대한 유용한 예제도 다수 포함되어 있습니다. 인증을 위해 클라이언트 암호 기반 인증 또는 클라이언트 인증서 기반 Azure AD 인증을 사용할 수 있습니다.

##### Azure AD에 대한 클라이언트 암호 기반 인증

이어지는 섹션에서는 Azure AD에 대한 클라이언트 암호 기반 인증을 구성하는 데 필요한 단계를 설명합니다.

##### Azure PowerShell을 사용하여 새 Azure AD 앱 만들기

아래 PowerShell cmdlet을 사용하여 새 Azure AD 앱을 만듭니다.

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

.**참고:** $azureAdApplication.ApplicationId는 Azure AD ClientID이며 $aadClientSecret은 나중에 ADE를 설정하는 데 사용할 클라이언트 암호입니다.Azure AD 클라이언트 암호를 적절하게 보호해야 합니다


##### Azure 서비스 관리 포털에서 Azure AD 클라이언트 ID 및 암호 프로비전

https://manage.windowsazure.com에서 Azure 서비스 관리 포털을 사용하여 Azure AD 클라이언트 ID 및 암호도 프로비전할 수 있으며 이 작업을 수행하려면 아래 단계를 따르세요.

1\. 아래 그림에 나와있는 것처럼 Active Directory 탭을 클릭합니다.

![Azure 디스크 암호화](./media/azure-security-disk-encryption/disk-encryption-fig3.JPG)

2\. 응용 프로그램 추가를 클릭하고 아래와 같이 응용 프로그램 이름을 입력합니다.

![Azure 디스크 암호화](./media/azure-security-disk-encryption/disk-encryption-fig4.JPG)

3\. 화살표 단추를 클릭하고 아래와 같이 앱 속성을 구성합니다.

![Azure 디스크 암호화](./media/azure-security-disk-encryption/disk-encryption-fig5.JPG)

4\. 왼쪽 하단에 있는 확인 표시를 클릭하여 마칩니다. 앱의 구성 페이지가 나타납니다. 아래 그림처럼 Azure AD 클라이언트 ID는 페이지의 아래쪽에 있습니다.

![Azure 디스크 암호화](./media/azure-security-disk-encryption/disk-encryption-fig6.JPG)

5\. 저장 단추를 클릭하여 Azure AD 클라이언트 암호를 저장합니다. 저장 단추를 클릭하고 키 텍스트 상자에서 암호를 확인합니다. 이것이 Azure AD 클라이언트 암호입니다. Azure AD 클라이언트 암호를 적절하게 보호해야 합니다.

![Azure 디스크 암호화](./media/azure-security-disk-encryption/disk-encryption-fig7.JPG)


**참고:** 위의 작업 과정은 포털에서 지원되지 않습니다.

##### 기존 앱 사용

아래 명령을 실행하려면 Azure AD PowerShell 모듈이 필요하며 [여기](https://technet.microsoft.com/library/jj151815.aspx)에서 얻을 수 있습니다.

**참고:** 새 PowerShell 창에서 아래 명령을 실행해야 합니다. 이러한 명령을 실행하는 데 Azure PowerShell 또는 Azure 리소스 관리자 창을 사용하지 마십시오. 이러한 cmdlet이 MSOnline 모듈 또는 Azure AD PowerShell에 있기 때문입니다.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Azure AD에 대한 인증서 기반 인증

이어지는 섹션에서는 Azure AD에 대한 인증서 기반 인증을 구성하는 데 필요한 단계를 설명합니다.

##### 새 Azure AD 앱 만들기

아래 PowerShell cmdlet을 실행하여 새 Azure AD 앱을 만듭니다.

**참고:** 아래 “yourpassword” 연결 문자열을 보안 암호로 바꾸고 암호를 보호합니다.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

이 단계를 마치면 .pfx 파일을 주요 자격 증명 모음에 업로드하고 인증서를 VM에 배포하는 데 필요한 액세스 정책을 사용하도록 설정합니다.

##### 기존 Azure AD 앱 사용
기존 앱을 위한 인증서 기반 인증을 구성하는 경우 아래 PowerShell cmdlet을 사용합니다. 새 PowerShell 창에서 실행해야 합니다.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

이 단계를 마치면 .pfx 파일을 주요 자격 증명 모음에 업로드하고 인증서를 VM에 배포하는 데 필요한 액세스 정책을 사용하도록 설정합니다.

##### PFX 파일을 주요 자격 증명 모음에 업로드
이 프로세스가 작동하는 방식에 대한 자세한 설명은 이 [블로그 게시물](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)에서 확인할 수 있습니다. 그러나 아래 PowerShell cmdlet만으로도 이 작업을 충분히 수행할 수 있습니다. Azure PowerShell 콘솔에서 실행해야 합니다.

**참고:** 아래 “yourpassword” 연결 문자열을 보안 암호로 바꾸고 암호를 보호합니다.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### 주요 자격 증명 모음의 인증서를 기존 VM에 배포
PFX 업로드를 완료한 후 아래 단계에 따라 주요 자격 증명 모음의 인증서를 기존 VM에 배포합니다.

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Azure AD 응용 프로그램에 대한 주요 자격 증명 모음 액세스 정책 설정

Azure AD 응용 프로그램에 자격 증명 모음의 키 또는 암호에 액세스할 권한이 필요합니다. –ServicePrincipalName 매개 변수 값으로 클라이언트 ID(응용 프로그램이 등록될 때 생성됨)를 사용하여 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) cmdlet으로 응용 프로그램에 권한을 부여합니다. 이에 대한 몇 가지 예는 [이 블로그 게시물](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)에서 확인할 수 있습니다. 다음은 PowerShell을 통해 이 작업을 수행하는 방법에 대한 예입니다.

    $keyVaultName = ‘yourKeyVaultName’
    $aadClientID = '<youAadAppClientID>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys all -PermissionsToSecrets all

## 용어

이 기술에 사용되는 일반적인 용어를 이해하려면 참조로 용어 표를 사용하세요.


| 용어 | 정의 |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD는 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)입니다. Azure AD 계정은 주요 자격 증명 모음에서 암호를 인증, 저장 및 검색하기 위한 필수 구성 요소입니다. |
| Azure 주요 자격 증명 모음 [AKV] | Azure 주요 자격 증명 모음은 암호화 키 및 중요한 암호를 안전하게 보호하는 FIPS 유효성을 검사한 하드웨어 보안 모듈 기반의 암호화 키 관리 서비스입니다. 자세한 내용은 [주요 자격 증명 모음](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| ARM | Azure 리소스 관리자 |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx)는 Windows IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 업계에서 공인된 Windows 볼륨 암호화 기술입니다. |
| BEK | BitLocker 암호화 키는 OS 부팅 볼륨 및 데이터 볼륨을 암호화하는 데 사용됩니다. BitLocker 키는 고객의 Azure 주요 자격 증명 모음에서 비밀 보안 수단입니다. |
| CLI | [Azure 명령줄 인터페이스](../xplat-cli-install.md) |
| DM-Crypt | [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)는 Linux IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 Linux 기반의 투명한 디스크 암호화 하위 시스템입니다. |
| KEK | 주요 암호화 키는 원하는 경우 암호를 보호하거나 래핑하는 데 사용되는 비대칭 키(RSA 2048)입니다. HSM 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure 주요 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)을 참조하세요. |
| PS cmdlet | [Azure PowerShell cmdlet](powershell-install-configure.md) |

## 디스크 암호화 배포 시나리오 및 사용자 환경

디스크 암호화를 사용하도록 설정할 수 있는 수많은 시나리오가 있으며 단계는 시나리오에 따라 다를 수 있습니다. 이어지는 섹션에서는 이러한 시나리오에 대해 자세히 설명합니다.

### Azure 갤러리에서 만든 새 IaaS VM에서 암호화 사용

[여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) 게시된 ARM 템플릿을 사용하여 Azure에서 Azure 갤러리의 새 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다. Azure 빠른 시작 템플릿에서 "Azure에 배포" 단추를 클릭하고 매개 변수 블레이드에 암호화 구성을 입력하고 확인을 클릭합니다. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 만들기 단추를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

**참고:** 이 템플릿은 Windows Server 2012 갤러리 이미지를 사용하여 새 암호화된 Windows VM을 만듭니다.

아래 표에서 Azure AD 클라이언트 ID를 사용하는 Azure 갤러리 시나리오에서 새 VM에 대한 ARM 템플릿 매개 변수 세부 정보를 확인할 수 있습니다.

| 매개 변수 | 설명|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | 가상 컴퓨터의 관리 사용자 이름 |
| adminPassword | 가상 컴퓨터의 관리 사용자 암호 |
| newStorageAccountName | OS 및 데이터 vhd를 저장할 저장소 계정의 이름 |
| vmSize | VM의 크기. 현재 표준 A, D 및 G 시리즈만 지원됩니다. |
| virtualNetworkName | VM NIC가 속해야 하는 VNet의 이름. |
| subnetName | VM NIC가 속해야 하는 VNet의 서브넷 이름. |
| AADClientID | 주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 ID |
| AADClientSecret | 주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 암호 |
| keyVaultResourceID,ResourceID | ARM에서 주요 자격 증명 모음 리소스를 식별합니다. PowerShell cmdlet을 사용하여 얻을 수 있음: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).ResourceId |
| keyVaultURL | BitLocker 키가 업로드될 주요 자격 증명 모음의 URL. cmdlet을 사용하여 얻을 수 있음: (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI |
| keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. 선택 사항입니다. |
| vmName | 암호화 작업을 수행할 VM의 이름.


**참고:** KeyEncryptionKeyURL은 선택적 매개 변수입니다. 주요 자격 증명 모음에서 데이터 암호화 키(암호 비밀 정보)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

### 고객 암호화 VHD 및 암호화 키에서 만든 새 IaaS VM에서 암호화 사용

이 시나리오에서는 ARM 템플릿, PowerShell cmdletsor CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 아래 섹션에서는 ARM 템플릿과 CLI 명령에 대해 자세히 설명합니다.

#### ARM 템플릿 사용

[여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm) 게시된 ARM 템플릿을 사용하여 고객 암호화 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. Azure 빠른 시작 템플릿에서 "Azure에 배포" 단추를 클릭하고 매개 변수 블레이드에 암호화 구성을 입력하고 확인을 클릭합니다. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 만들기 단추를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

고객 암호화 VHD 시나리오에 대한 ARM 템플릿 매개 변수 정보는 아래 표에서 설명합니다.

| 매개 변수 | 설명|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | 암호화된 OS vhd를 저장할 저장소 계정의 이름. 이 저장소 계정은 VM과 동일한 리소스 그룹 및 동일한 위치에 이미 만들어져 있어야 합니다. |
| osVhdUri | 저장소 계정에서 OS vhd의 URI |
| osType | OS 제품 유형(Windows/Linux) |
| virtualNetworkName | VM NIC가 속해야 하는 VNet의 이름. VM과 동일한 리소스 그룹 및 동일한 위치에 이미 만들어져 있어야 합니다. |
| subnetName | VM NIC가 속해야 하는 VNet의 서브넷 이름. |
| vmSize | VM의 크기. 현재 표준 A, D 및 G 시리즈만 지원됩니다. |
| keyVaultResourceID | ARM에서 주요 자격 증명 모음 리소스를 식별하는 ResourceID입니다. PowerShell cmdlet을 사용하여 얻을 수 있음: (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId |
| keyVaultSecretUrl | ​주요 자격 증명 모음에 프로비전된 디스크 암호화 키의 URL |
| keyVaultKekUrl | 생성된 디스크 암호화 키를 암호화하기 위한 주요 암호화 키의 URL |
| ​vmName | ​IaaS VM의 이름   



####PowerShell cmdlet 사용

[여기](https://msdn.microsoft.com/library/azure/mt603746.aspx) 게시된 PS cmdlet을 사용하여 고객 암호화 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다.

####CLI 명령 사용

CLI 명령을 사용하여 이 시나리오에 대한 디스크 암호화를 사용하도록 설정하려면 아래 단계를 따르세요.

1. 주요 자격 증명 모음에 대한 액세스 정책을 설정합니다.
	- ‘EnabledForDiskEncryption’ 플래그를 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Azure AD 앱이 KeyVault에 암호를 쓸 수 있는 권한을 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 기존/실행 중인 VM에서 암호화를 사용하도록 설정하려면 다음을 입력합니다. *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 암호화 상태를 가져옵니다. *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 고객 암호화 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 "azure vm create" 명령에 아래 매개 변수를 사용합니다.
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Azure에서 기존 또는 실행 중인 IaaS Windows VM에서 암호화 사용

이 시나리오에서는 ARM 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 아래 섹션에서는 ARM 템플릿과 CLI 명령을 사용하여 암호화를 사용하도록 설정하는 방법에 대해 자세히 설명합니다.

#### ARM 템플릿 사용

[여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) 게시된 ARM 템플릿을 사용하여 Azure에서 기존/실행 중인 IaaS Windows VM에서 디스크 암호화를 사용하도록 설정할 수 있습니다. Azure 빠른 시작 템플릿에서 "Azure에 배포" 단추를 클릭하고 매개 변수 블레이드에 암호화 구성을 입력하고 확인을 클릭합니다. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 만들기 단추를 클릭하여 기존/실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

Azure AD 클라이언트 ID를 사용하는 기존/실행 중인 VM 시나리오에 대한 ARM 템플릿 매개 변수 세부 정보가 아래 표에 제공됩니다.

| 매개 변수 | 설명|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 ID |
| AADClientSecret | ​주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 암호 |
| keyVaultName | BitLocker 키가 업로드될 주요 자격 증명 모음의 이름입니다. Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName> cmdlet을 사용하여 가져올 수 있습니다. Vaultname |
| ​ keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. UseExistingKek 드롭다운에서 “nokek”를 선택하면 이 옵션이 선택 사항입니다. UseExistingKek 드롭다운에서 “kek”를 선택하면 keyEncryptionKeyURL 값을 반드시 입력해야 합니다. |
| ​volumeType | ​암호화 작업을 수행할 볼륨의 유형. 유효한 값은 "OS", "데이터", "모두"입니다. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| ​vmName | ​암호화 작업을 수행할 VM의 이름.


**참고:** KeyEncryptionKeyURL은 선택적 매개 변수입니다. 주요 자격 증명 모음에서 데이터 암호화 키(BitLocker 암호화 비밀 정보)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

#### PowerShell cmdlet 사용

PS cmdlet을 사용하는 Azure 디스크 암호화를 사용하여 암호화를 사용하는 방법에 대한 자세한 내용은 **Azure PowerShell을 사용하여 Azure 디스크 암호화 탐색** 블로그 게시물 [1부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) 및 [2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)를 참조하세요.

#### CLI 명령 사용

CLI 명령을 사용하여 Azure에서 기존/실행 중인 IaaS Windows VM에서 암호화를 사용하도록 설정하려면 아래 단계를 따르세요.

1. 주요 자격 증명 모음에 대한 액세스 정책을 설정합니다.
	- ‘EnabledForDiskEncryption’ 플래그를 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Azure AD 앱이 KeyVault에 암호를 쓸 수 있는 권한을 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 기존/실행 중인 VM에서 암호화를 사용하도록 설정하려면 다음을 입력합니다. *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 암호화 상태를 가져옵니다. *“azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”*
4. 고객 암호화 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 "azure vm create" 명령에 아래 매개 변수를 사용합니다.
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Azure에서 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용

[여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 게시된 ARM 템플릿을 사용하여 Azure에서 기존/실행 중인 IaaS Linux VM에서 디스크 암호화를 사용하도록 설정할 수 있습니다. Azure 빠른 시작 템플릿에서 "Azure에 배포" 단추를 클릭하고 매개 변수 블레이드에 암호화 구성을 입력하고 확인을 클릭합니다. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 만들기 단추를 클릭하여 기존/실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

Azure AD 클라이언트 ID를 사용하는 기존/실행 중인 VM 시나리오에 대한 ARM 템플릿 매개 변수 세부 정보를 아래 표에서 설명합니다.

| 매개 변수 | 설명|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 ID |
| AADClientSecret | ​주요 자격 증명 모음에 암호를 쓸 수 있는 권한이 있는 Azure AD 앱의 클라이언트 암호 |
| keyVaultName | BitLocker 키가 업로드될 주요 자격 증명 모음의 이름입니다. Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName> cmdlet을 사용하여 가져올 수 있습니다. Vaultname |
| ​ keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. UseExistingKek 드롭다운에서 “nokek”를 선택하면 이 옵션이 선택 사항입니다. UseExistingKek 드롭다운에서 “kek”를 선택하면 keyEncryptionKeyURL 값을 반드시 입력해야 합니다. |
| ​volumeType | ​암호화 작업을 수행할 볼륨의 유형. 지원되는 유효한 값은 "데이터"입니다. Linux VM에서는 실행 중 Linux VM의 OS 볼륨에서 암호화 사용을 지원하지 않습니다. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| ​vmName | ​암호화 작업을 수행할 VM의 이름.
| passPhrase | 데이터 암호화 키로 강력한 암호를 입력합니다. |                                                                                                                                                                                                                                                      

**참고:** KeyEncryptionKeyURL은 선택적 매개 변수입니다. 주요 자격 증명 모음에서 데이터 암호화 키(암호 비밀 정보)에 대한 보안을 강화하기 위해 고유한 KEK를 만들 수 있습니다.

#### CLI 명령

[여기](../xplat-cli-install.md)에서 설치된 CLI 명령을 사용하여 고객 암호화 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. CLI 명령을 사용하여 Azure에서 기존/실행 중인 IaaS Linux VM에서 암호화를 사용하도록 설정하려면 아래 단계를 따르세요.

1. 주요 자격 증명 모음에 대한 액세스 정책을 설정합니다.
	- ‘EnabledForDiskEncryption’ 플래그를 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- Azure AD 앱이 KeyVault에 암호를 쓸 수 있는 권한을 설정합니다. “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 기존/실행 중인 VM에서 암호화를 사용하도록 설정하려면 다음을 입력합니다. *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 암호화 상태를 가져옵니다. “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. 고객 암호화 VHD에서 새 VM에 암호화를 사용하도록 설정하려면 "azure vm create" 명령에 아래 매개 변수를 사용합니다.
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### 암호화된 IaaS VM의 암호화 상태 가져오기

Azure 관리 포털, [PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt622700.aspx) 또는 CLI 명령을 사용하여 암호화 상태를 가져올 수 있습니다. 아래 섹션에서는 Azure 포털 및 CLI 명령을 사용하여 암호화 상태를 가져오는 방법에 대해 설명합니다.

#### Azure 관리 포털을 사용하여 암호화된 IaaS VM의 암호화 상태 가져오기

Azure 관리 포털에서 IaaS VM의 암호화 상태를 가져올 수 있습니다. https://portal.azure.com/에서 Azure 포털에 로그온하고 왼쪽 메뉴에서 가상 컴퓨터 링크를 클릭하여 구독에서 가상 컴퓨터의 요약 보기를 확인합니다. 구독 드롭다운에서 구독 이름을 선택하여 가상 컴퓨터 보기를 필터링할 수 있습니다. 가상 컴퓨터 페이지 메뉴의 위쪽에 있는 열을 클릭합니다. 선택 열 블레이드에서 디스크 암호화 열을 선택하고 업데이트를 클릭합니다. 아래 그림에 표시된 것처럼 각 VM에 대한 "사용" 또는 "사용 안 함" 암호화 상태를 보여주는 디스크 암호화 열이 표시됩니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig2.JPG)

#### 디스크 암호화 PS Cmdlet을 사용하여 암호화된 IaaS VM의 암호화 상태 가져오기
디스크 암호화 PS cmdlet "Get-AzureRmVMDiskEncryptionStatus"에서 IaaS VM의 암호화 상태를 가져올 수 있습니다. VM에 대한 암호화 설정을 가져오려면 Azure PowerShell 세션에 다음을 입력합니다.

    PS C:\Windows\System32\WindowsPowerShell\v1.0> Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName <yourResourceGroupName> -VMName <yourVMName>

    OsVolumeEncrypted: True
    OsVolumeEncryptionSettings : {
      "DiskEncryptionKey": {
       SecretUrl":"https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "SourceVault": {
            "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxxx/providers/Mi                            crosoft.KeyVault/vaults/xxxxxxx"
                                }
                            },
                    "KeyEncryptionKey": null
                             }
    DataVolumesEncrypted: True

OSVolumeEncrypted 및 DataVolumesEncrypted 설정 값이 "True"로 설정되어 두 볼륨이 Azure 디스크 암호화를 사용하여 암호화됨을 보여줍니다. PS cmdlet을 사용하는 Azure 디스크 암호화를 사용하여 암호화를 사용하는 방법에 대한 자세한 내용은 **Azure PowerShell을 사용하여 Azure 디스크 암호화 탐색** 블로그 게시물 [1부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) 및 [2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)를 참조하세요.

#### 디스크 암호화 CLI 명령에서 IaaS VM의 암호화 상태 가져오기

디스크 암호화 CLI 명령 *azure vm show-disk-encryption-status*에서 IaaS VM의 암호화 상태를 가져올 수 있습니다. VM에 대한 암호화 설정을 가져오려면 Azure CLI 세션에 다음을 입력합니다.

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### 실행 중인 Windows IaaS VM에서 암호화 사용 안 함

Azure 디스크 암호화 ARM 템플릿 또는 PS cmdlet을 통해 실행 중인 Windows IaaS VM에서 암호화를 사용하지 않고 암호 해독 구성을 지정할 수 있습니다. 암호화 사용 안 함 단계는 Windows IaaS VM에 대해서만 지원되고 Linux IaaS VM에서는 지원되지 않습니다. 암호화 사용 안 함 단계는 실행 중인 Windows IaaS VM에서 OS 또는 데이터 볼륨이나 둘 모두의 암호화를 사용하지 않습니다. OS 볼륨을 사용하지 않고 암호화된 데이터 볼륨은 그대로 둘 수 없습니다. 암호화 사용 안 함 단계를 수행하면 Azure 서비스 관리는 VM 서비스 모델을 업데이트하고 Windows IaaS VM은 암호가 해독되었다고 표시합니다. VM의 콘텐츠는 더 이상 휴지 상태로 암호화되지 않습니다. 암호화 사용 안 함은 Windows에 대한 BitLocker 암호화 키 및 Linux에 대한 암호인 고객 키 자격 증명 모음 및 암호화 키 자료를 삭제하지 않습니다.

##### ARM 템플릿을 사용하는 Azure에서 기존/실행 중인 IaaS Windows에 암호화 사용

[여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 게시된 ARM 템플릿을 사용하여 실행 중인 Windows IaaS VM에서 디스크 암호화를 사용하지 않을 수 있습니다. Azure 빠른 시작 템플릿에서 "Azure에 배포" 단추를 클릭하고 매개 변수 블레이드에 암호 해독 구성을 입력하고 확인을 클릭합니다. 구독, 리소스 그룹, 리소스 그룹 위치, 약관 및 규약을 선택하고 만들기 단추를 클릭하여 새 IaaS VM에서 암호화를 사용하도록 설정합니다.

실행 중인 Windows IaaS VM에서 암호화를 사용하지 않는 ARM 템플릿 매개 변수 세부 정보는 다음과 같습니다.

| ​vmName | ​암호화 작업을 수행할 VM의 이름. |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​volumeType | ​암호 해독 작업을 수행할 볼륨의 유형. 유효한 값은 "OS", "데이터", "모두"입니다. **참고:** "데이터" 볼륨에서 암호화를 사용하고도 실행 중인 Windows IaaS VM OS/부팅 볼륨에서 암호화를 사용하지 않을 수 없습니다. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호 해독 작업이 수행될 때마다 이 버전 번호가 증가합니다. |

##### PS cmdlet을 사용하는 Azure에서 기존/실행 중인 IaaS Windows에 암호화 사용

PS cmdlet을 사용하지 않으려면 [Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) cmdlet을 통해 IaaS(infrastructure as a service) 가상 컴퓨터에서 암호화를 사용하지 않습니다. 이 cmdlet은 Windows 가상 컴퓨터에만 지원되고 Linux 가상 컴퓨터에는 지원되지 않습니다. 이 cmdlet은 가상 컴퓨터에서 확장을 설치하여 암호화를 사용하지 않습니다. 이름 매개 변수를 지정하지 않으면 "Windows VM용 AzureDiskEncryption" 기본 이름을 포함한 확장이 만들어집니다.

**참고**: 이 cmdlet은 가상 컴퓨터를 다시 부팅합니다.

## 부록

### 구독에 연결

진행하기 전에 이 문서의 *필수 조건* 섹션을 검토해야 합니다. 모든 필수 조건이 충족되었는지 확인한 후 아래 단계에 따라 구독에 연결합니다.

1\. Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.

    Login-AzureRmAccount

2\. 여러 구독이 있고 사용할 특정 구독을 지정하려면 다음을 입력하여 계정에 대한 구독을 확인합니다.

    Get-AzureRmSubscription

3\. 사용할 구독을 지정하려면 다음을 입력합니다.

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. 구성된 구독이 올바른지 확인하려면 다음을 입력합니다.

    Get-AzureRmSubscription

5\. Azure 디스크 암호화 cmdlet이 설치되었는지 확인하려면 다음을 입력합니다.

    Get-command *diskencryption*

6\. Azure 디스크 암호화 PowerShell 설치를 확인하는 아래 출력이 표시됩니다.

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	 Version    Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus       1.1.0      AzureRM.Compute                                                    
    Cmdlet       Remove-AzureRmVMDiskEncryptionExtension 1.1.0      AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension    1.1.0      AzureRM.Compute                                                     

### 사전에 암호화된 Windows VHD 준비
Azure IaaS에서 암호화된 VHD로 배포용으로 사전에 암호화된 Windows VHD를 준비하려면 이어지는 섹션이 필요합니다. 이 단계는 Hyper-V 또는 Azure에서 최신 Windows VM(vhd)을 준비 및 부팅하는 데 사용됩니다.

#### OS 보호를 위해 비-TPM을 허용하도록 그룹 정책 업데이트
BitLocker 드라이브 암호화라는 BitLocker 그룹 정책 설정을 구성해야 하며 로컬 컴퓨터 정책 \\컴퓨터 구성\\관리 템플릿\\Windows 구성 요소 아래에 있습니다. 아래 그림처럼 이 설정을 *운영 체제 드라이브 - 시작 시 추가 인증 요구 - 호환되는 TPM 없이 BitLocker 허용*으로 변경합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig8.JPG)

#### BitLocker 기능 구성 요소 설치
Windows Server 2012 이상에는 아래 명령을 사용합니다.

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Windows Server 2008 R2에는 아래 명령을 사용합니다.

    ServerManagerCmd -install BitLockers

#### bdehdcfg를 사용하여 BitLocker에 대한 OS 볼륨 준비

아래 명령을 실행하여 OS 파티션을 압축하고 BitLocker에 대한 컴퓨터를 준비합니다.

    bdehdcfg -target c: shrink -quiet

#### BitLocker를 사용하여 OS 볼륨 보호
[manage-bde](https://technet.microsoft.com/library/ff829849.aspx) 명령으로 외부 키 보호기를 사용하여 부팅 볼륨에서 암호화를 사용하도록 설정하고 외부 드라이브 또는 볼륨에 외부 키(.bek 파일)를 배치합니다. 암호화는 다음 재부팅 후 시스템/부팅 볼륨에 사용하도록 설정됩니다.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**참고:** BitLocker를 사용하여 외부 키를 가져오기 위해서는 별도의 데이터/리소스 vhd로 VM을 준비해야 합니다.

#### 사전에 암호화된 Linux VHD 준비

##### Ubuntu 14.

1\. 아래 스크립트 내용으로 /usr/local/sbin/azure\_crypt\_key.sh라는 파일을 만듭니다. Azure에서 배치되는 암호 파일 이름이므로 KeyFileName에 주의해야 합니다.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. */etc/crypttab*에서 암호화 구성을 변경합니다. 다음과 같이 표시되어야 합니다.

    Sda5_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. Windows에서 *azure\_crypt\_key.sh*를 편집 중이고 Linux에 복사하는 경우 잊지 말고 *dos2unix /usr/local/sbin/azure\_crypt\_key.sh*를 실행하세요. 4. 줄을 추가하여 */etc/initramfs-tools/modules*을 편집합니다.

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5\. *update-initramfs -u -k all*을 실행하여 initramfs를 업데이트하고 keyscript가 적용되도록 합니다.

##### openSUSE 13.2.

1\. /etc/dracut.conf add\_drivers+="vfat ntfs nls\_cp437 nls\_iso8859-1"을 편집합니다.

2\. "/usr/lib/dracut/modules.d/90crypt/module-setup.sh" 파일의 끝에서 다음 줄을 주석 처리합니다.

    #    inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator


3\. "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh" 파일의 시작 부분에 DRACUT\_SYSTEMD=0을 추가하고 모든 "if [ -z "$DRACUT\_SYSTEMD" ]; then"을 "if [ 1 ]; then"으로 변경합니다.

4\. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh를 편집하고 "# Open LUKS device" 뒤에 추가합니다.

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
       echo "> Trying device:$SFS..." >&2
       mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
       mount ${SFS}1 $MountPoint -t ntfs -r >&2
       if [ -f $MountPoint/$KeyFileName ]; then
          echo "> keyfile got..." >&2
          luksfile=$MountPoint/$KeyFileName
          break
       fi
    done

5\."dracut –f -v"를 실행하여 initrd를 업데이트합니다.

##### CentOS 7
1\. /etc/dracut.conf add\_drivers+=" vfat ntfs nls\_cp437 nls\_iso8859-1"을 편집합니다.

2\. "/usr/lib/dracut/modules.d/90crypt/module-setup.sh" 파일의 끝에서 다음 줄을 주석 처리합니다.

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. "/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh" 파일의 시작 부분에 DRACUT\_SYSTEMD=0을 추가하고 모든 "if [ -z "$DRACUT\_SYSTEMD" ]; then"을 "if [ 1 ]; then"으로 변경합니다.

4\. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh를 편집하고 "# Open LUKS device" 뒤에 추가합니다.

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        luksfile=$MountPoint/$KeyFileName
        break
    fi
    done


5\. "/usr/sbin/dracut -f -v"를 실행하여 initrd를 업데이트합니다.

###Azure 저장소 계정에 암호화된 VHD 업로드
BitLocker 암호화 또는 DM-Crypt 암호화를 사용하도록 설정한 후에는 로컬 암호화된 VHD를 저장소 계정에 업로드해야 합니다.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### 사전에 암호화된 VM에 대한 디스크 암호화 암호를 주요 자격 증명 모음에 업로드
이전에 가져온 디스크 암호화 암호를 주요 자격 증명 모음에 암호로 업로드해야 합니다.

#### KEK로 암호화되지 않은 디스크 암호화 암호
[Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx)을 사용하여 주요 자격 증명 모음에서 암호 프로비전 Windows 가상 컴퓨터의 경우 bek 파일이 base64 문자열로 인코딩된 후 Set-AzureKeyVaultSecret cmdlet을 사용하여 주요 자격 증명 모음으로 업로드됩니다. Linux의 경우 암호는 base64 문자열로 인코딩된 후 주요 자격 증명 모음으로 업로드됩니다. 또한 주요 자격 증명 모음에서 암호를 만드는 동안 다음 태그가 설정되었는지 확인합니다.

    "tags":
    {
       “DiskEncryptionKeyEncryptionAlgorithm”: “RSA-OAEP (optional)”
       "DiskEncryptionKeyFileName": "Bek file name (windows) or Passphrase filename (linux)"
    }

    param(
      [Parameter(Mandatory=$True)]
      [String]$BekFilePath = "C:\vm\nbox\2640EE52-41B3-426C-87B9-484232452CE4.BEK",
      [String]$VaultName = "DiskEncryptionTestAus",
      [String]$SecretName = "BitLockerKey"
      )

    #"EAN//ojeIQk="
    $bekFileName = split-path $BekFilePath -leaf
    echo "Bek file name = $bekFileName"

    $secretBytes = [System.IO.File]::ReadAllBytes($BekFilePath);
    $secret = [Convert]::ToBase64String($secretBytes);
    echo "Secret = $secret"

    $secureSecret = ConvertTo-SecureString $secret -AsPlainText -Force
    $tags = @{"DiskEncryptionKeyFileName" = "$bekFileName"}

    echo "Tags = $tags"
    echo "Vault = $VaultName"
    echo "Secret name = $SecretName"
    echo "Adding secret to Key vault"

    Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secureSecret -tags $tags


#### KEK로 암호화된 디스크 암호화 암호

필요에 따라 주요 자격 증명 모음으로 암호를 업로드하기 전에 주요 암호화 키로 암호화할 수 있습니다. 먼저 래핑 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx)를 사용하여 주요 암호화 키로 암호를 암호화합니다. 이 래핑 작업의 출력은 base64 URL 인코딩 문자열로, [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) cmdlet을 사용하여 암호로 업로드됩니다.


##이 가이드 다운로드
[TechNet 갤러리](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)에서 이 가이드를 다운로드할 수 있습니다.


## Blob에 대한 자세한 내용은
[Azure PowerShell를 사용하여 Azure 디스크 암호화 탐색](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Azure PowerShell를 사용하여 Azure 디스크 암호화 탐색 - 2부](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0921_2016-->