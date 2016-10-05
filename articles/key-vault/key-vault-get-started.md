<properties
	pageTitle="Azure 주요 자격 증명 모음 시작 | Microsoft Azure"
	description="이 자습서를 사용하면 Azure 키 자격 증명 모음으로 시작하여 확정된 컨테이너를 Azure에 만들고 Azure에서 암호화 키와 비밀을 저장하고 관리하는 데 도움이 됩니다."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/27/2016"
	ms.author="cabailey"/>

# Azure 주요 자격 증명 모음 시작 #
Azure 키 자격 증명 모음은 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.

## 소개  
이 자습서를 사용하면 Azure 키 자격 증명 모음으로 시작하여 확정된 컨테이너(자격 증명 모음)를 Azure에 만들고 Azure에서 암호화 키와 비밀을 저장하고 관리하는 데 도움이 됩니다. Azure 응용 프로그램과 함께 사용할 수 있는 키 또는 암호를 포함하는 자격 증명 모음을 만드는 Azure PowerShell을 사용하는 과정을 안내합니다. 그런 다음 응용 프로그램이 해당 키 또는 암호를 사용할 수 있는 방법을 보여 줍니다.

**예상 완료 시간:** 20분

>[AZURE.NOTE]  이 자습서에는 단계 중 하나에 포함되는 Azure 응용 프로그램을 작성하는 방법(즉, 주요 자격 증명 모음의 키나 비밀을 사용하도록 응용 프로그램에 사용 권한을 부여하는 방법)에 대한 지침이 포함되어 있지 않습니다.
>
>이 자습서에서는 Azure PowerShell을 사용합니다. 플랫폼 간 명령줄 인터페이스 지침은 [이 해당 자습서](key-vault-manage-with-cli.md)를 참조하세요.

Azure 키 자격 증명 모음에 대한 개요는 [Azure 키 자격 증명 모음이란?](key-vault-whatis.md)을 참조하세요.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Microsoft Azure에 대한 구독. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- Azure PowerShell, **최소 버전 1.1.0**. Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. 이미 Azure PowerShell가 설치되어 있고 버전을 알 수 없는 경우, Azure PowerShell 콘솔에서 `(Get-Module azure -ListAvailable).Version`을 입력합니다. Azure PowerShell 버전 0.9.1 ~ 0.9.8이 설치된 경우, 일부 약간의 변경이 있지만 이 자습서를 계속 사용할 수 있습니다. 예를 들어, `Switch-AzureMode AzureResourceManager` 명령을 사용해야 하고 Azure 키 자격 증명 모음 명령의 일부가 변경되었습니다. 버전 0.9.1 ~ 0.9.8에 대한 키 자격 증명 모음 목록은 [Azure 키 자격 증명 모음 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052(v=azure.98).aspx)을 참조하세요.
- 이 자습서에서 만드는 키 또는 암호를 사용하여 구성되는 응용 프로그램입니다. 샘플 응용 프로그램은 [Microsoft 다운로드 센터](http://www.microsoft.com/ko-KR/download/details.aspx?id=45343)에서 사용할 수 있습니다. 자세한 내용은 해당 추가 정보 파일을 참조하세요.


이 자습서는 Azure PowerShell 초보자용으로 설계되었지만, 모듈, cmdlet, 세션 등과 같은 기본 개념을 잘 알고 있다고 가정합니다. 자세한 내용은 [Windows PowerShell 시작](https://technet.microsoft.com/library/hh857337.aspx)을 참조하세요.

이 자습서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 **Get-Help** cmdlet을 사용합니다.

	Get-Help <cmdlet-name> -Detailed

예를 들어 **Login-AzureRmAccount** cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

	Get-Help Login-AzureRmAccount -Detailed

Azure PowerShell에서 Azure 리소스 관리자와 친숙해지도록 다음 자습서도 확인하세요.

- [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)
- [리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)


## <a id="connect"></a>구독에 연결 ##

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.

    Login-AzureRmAccount 

예를 들어 Azure Government와 같은 Azure의 특정 인스턴스를 사용 중인 경우 이 명령을 가진 환경 매개 변수를 사용합니다. 예: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 가져와서 기본적으로 첫 번째 구독을 사용합니다.

여러 구독이 있는 경우 Azure 키 자격 증명 모음에 사용할 특정 하나를 지정하려면, 다음을 입력하여 계정에 대한 구독을 표시합니다.

    Get-AzureRmSubscription

그런 후, 사용할 구독을 지정하려면 다음을 입력합니다.

    Set-AzureRmContext -SubscriptionId <subscription ID>

Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.


## <a id="resource"></a>새 리소스 그룹 만들기 ##

Azure 리소스 관리자를 사용하면 관련된 모든 리소스는 리소스 그룹의 내부에 만들어집니다. 이 자습서에서는 **ContosoResourceGroup**이라는 새 리소스 그룹을 만듭니다.

	New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>키 자격 증명 모음 만들기 ##

[New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736(v=azure.200).aspx).aspx) cmdlet을 사용하여 주요 자격 증명 모음을 만듭니다. 이 cmdlet은 3개의 필수 매개 변수, **리소스 그룹 이름**, **키 자격 증명 모음 이름** 및 **지리적 위치**가 있습니다.

예를 들어, 이름이 **ContosoKeyVault**이고, 리소스 그룹 이름이 **ContosoResourceGroup**이며 위치가 **동아시아**인 형식의 자격 증명 모음인 경우, 다음을 입력합니다.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

이 cmdlet의 출력에서는 방금 만든 자격 증명 모음의 속성을 보여줍니다. 가장 중요한 두 개의 속성은 다음과 같습니다.

- **자격 증명 모음 이름**: 예제에서는 **ContosoKeyVault**입니다. 다른 키 자격 증명 모음 cmdlet에 대해 이 이름을 사용합니다.
- **Vault URI**: 예제에서는 https://contosokeyvault.vault.azure.net/ 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 응용 프로그램은 URI를 사용해야 합니다.

Azure 계정은 이제 이 키 자격 증명 모음에서 모든 작업을 수행할 권한을 가지게 됩니다. 아직 다른 사람은 권한이 없습니다.

>[AZURE.NOTE]  새로운 주요 자격 증명 모음을 만들려고 할 때 **구독 'Microsoft.KeyVault' 네임스페이스를 사용하기 위한 구독이 등록되지 않았습니다**라는 오류가 나타나는 경우 `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`을(를) 실행한 다음, New-AzureRmKeyVault 명령을 다시 실행합니다. 자세한 내용은 [Register-AzureRmProvider](https://msdn.microsoft.com/library/azure/mt759831(v=azure.200).aspx)를 참조하세요.
>

## <a id="add"></a>키 또는 비밀을 암호 키 자격 증명 모음에 추가 ##

소프트웨어 보호 키를 만들도록 Azure 키 자격 증명 모음을 사용하려면, [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048(v=azure.200).aspx) cmdlet을 사용하여 다음을 입력합니다.

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

그러나 Azure 키 자격 증명 모음에 업로드할 softkey.pfx라는 파일에 C:\\ 드라이브에 저장된 .PFX 파일에 소프트웨어 보호된 기존 키가 있는 경우, .PFX 파일에 대해 **123**의 암호로 변수 **securepfxpwd**를 설정하려면 다음을 입력합니다.

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

키를 .PFX 파일에서 가져오려면 다음을 입력합니다. 키 자격 증명 모음 서비스의 소프트웨어에서 키를 보호합니다.

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


이제 해당 URI를 사용하여 Azure 키 자격 증명 모음을 만들거나 업로드하는 이 키를 참조할 수 있습니다. **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**를 사용하면 항상 최신 버전을 가져오고 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**를 사용하면 이 특정 버전을 가져옵니다.

이 키에 대한 URI를 표시하려면 다음을 입력합니다.

	$Key.key.kid

암호가 SQLPassword이고 Azure 주요 자격 증명 모음에 대한 Pa$$w0rd 값이 있는 비밀을 자격 증명 모음에 추가하려면 먼저 다음을 입력하여 Pa$$w0rd 값을 비밀 문자열로 변환합니다.

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

그런 다음, 다음을 입력합니다.

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

이제 해당 URI를 사용하여 Azure 키 자격 증명 모음에 추가한 이 암호를 참조할 수 있습니다. **https://ContosoVault.vault.azure.net/secrets/SQLPassword**를 사용하면 항상 최신 버전을 가져오고 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**를 사용하면 이 특정 버전을 가져옵니다.

이 비밀에 대한 URI를 표시하려면 다음을 입력합니다.

	$secret.Id

방금 만든 키 또는 비밀을 보겠습니다.

- 사용자의 키를 보려면 다음을 입력 합니다. `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- 사용자의 비밀을 보려면 다음을 입력 합니다. `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

이제, 사용자 키 자격 증명 모음 또는 비밀은 응용 프로그램에서 사용할 준비가 되었습니다. 응용 프로그램을 사용하여 권한을 부여해야 합니다.

## <a id="register"></a>Azure Active Directory에 응용 프로그램 등록 ##

이 단계는 일반적으로 별도의 컴퓨터에서 개발자가 수행할 수 있습니다. Azure 주요 자격 증명 모음에 한정된 것은 아니지만 완전성을 위해 여기에 포함되어 있습니다.


>[AZURE.IMPORTANT] 이 단계에서 등록된 자습서, 계정, 해당 자격 증명 모음 및 응용 프로그램을 완료하려면, 모두 동일한 Azure 디렉터리에 있어야 합니다.

자격 증명 모음 키를 사용하는 응용 프로그램은 Azure Active Directory에서 토큰을 사용하여 인증해야 합니다. 이렇게 하려면 응용 프로그램의 소유자가 Azure Active Directory에 먼저 응용 프로그램을 등록해야 합니다. 등록 끝에 응용 프로그램 소유자는 다음 값을 가져옵니다.


- **응용 프로그램 ID**(클라이언트 ID라고도 함) 및 **인증 키**(공유 비밀이라고도 함). 응용 프로그램은 토큰을 가져올 Azure Active Directory에 이 두 값 모두가 있어야 합니다. 응용 프로그램 구성 방식은 응용 프로그램에 따라 달라집니다. 키 자격 증명 모음 샘플 응용 프로그램의 경우, 응용 프로그램 소유자는 app.config 파일에서 이러한 값을 설정합니다.

Azure Active Directory에 응용 프로그램을 등록하려면:

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 **Active Directory**를 클릭한 다음 응용 프로그램을 등록할 디렉터리를 선택합니다. <br> <br> **참고:** 주요 자격 증명 모음을 만든 Azure 구독을 포함하는 동일한 디렉터리를 선택해야 합니다. 이 디렉터리를 모르는 경우, **설정**을 클릭하고, 주요 자격 증명 모음을 만든 구독을 식별하고 마지막 열에 표시되는 디렉터리의 이름을 확인합니다.

3. **APPLICATIONS**를 클릭합니다. 디렉터리에 추가한 앱이 없는 경우 이 페이지에는 **앱 추가** 링크만 표시됩니다. 링크를 클릭하거나 명령 모음에서 **추가**를 클릭할 수 있습니다.
4.	**응용 프로그램 추가** 마법사에서 **무엇을 하고 싶나요?** 페이지를 클릭하고, **조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.
5.	**응용 프로그램 정보 제공** 페이지에서 응용 프로그램의 이름을 지정한 다음 **웹 응용 프로그램 및/또는 웹 API**(기본값)를 선택합니다. **다음** 아이콘을 클릭합니다.
6.	**앱 속성** 페이지에서 웹 응용 프로그램에 대한 **로그인 URL** 및 **앱 ID URI**를 지정합니다. 응용 프로그램에 이러한 값이 없는 경우이 단계에서 만들 수 있습니다(예를 들어, 두 상자에 대해 http://test1.contoso.com 를 지정할 수 있음). 이러한 사이트가 존재하는 지가 중요하지 않습니다. 사용자 디렉토리의 각 응용 프로그램에 대해 각 응용 프로그램의 앱 ID URI가 다르다는 것이 중요합니다. 디렉터리는 이 문자열을 사용하여 앱을 식별합니다.
7.	**완료** 아이콘을 클릭하여 마법사의 변경 내용을 저장합니다.
8.	**빠른 시작** 페이지에서 **구성**을 클릭합니다.
9.	**키** 섹션으로 스크롤하고 기간을 선택한 다음 **저장**을 클릭합니다. 페이지가 새로 고쳐지고 이제 키 값을 표시합니다. 이 키 값 및 **클라이언트 ID**가 있는 응용 프로그램을 구성해야 합니다. (이 구성에 대한 지침은 응용 프로그램에 특정된 것입니다.)
10.	사용자 자격 증명 모음에 사용 권한을 설정하려면 다음 단계에서 사용하는 이 페이지에서 클라이언트 ID 값을 복사 합니다.

## <a id="authorize"></a>키 또는 비밀을 사용하여 응용 프로그램 권한 부여 ##

응용 프로그램이 자격 증명 모음의 키 또는 암호에 대한 액세스를 인증하려면 [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625(v=azure.200).aspx) cmdlet을 사용합니다.

예를 들어 자격 증명 모음 이름이 **ContosoKeyVault**이고 권한을 부여하려는 응용 프로그램의 클라이언트 ID가 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed이며 자격 증명 모음에 있는 키로 서명하고 암호 해독하도록 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.


	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

자격 증명 모음에서 기밀 정보를 읽기 위해 동일한 응용 프로그램에 권한을 부여하려면 다음을 실행합니다.


	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>하드웨어 보안 모듈(HSM)을 사용하려는 경우 ##

추가된 보증을 위해, HSM 경계를 절대로 떠나지 않는 하드웨어 보안 모듈(HSMs)에서 키를 가져오거나 생성할 수 있습니다. HSM은 FIPS 140-2 Level 2 유효성 검사가 적용됩니다. 이 요구 사항이 사용자에게 적용되지 않는 경우, 이 섹션을 건너뛰고 [키 자격 증명 모음 및 연결된 키와 암호 삭제](#delete)로 이동합니다.

이러한 HSM 보호되는 키를 만들려면, [HSM 보호되는 키를 지원하는 자격 증명 모음 구독](https://azure.microsoft.com/pricing/free-trial/)이 있어야 합니다. 또한 이 기능은 Azure 중국에 사용할 수 없습니다.


자격 증명 모음을 만들 때 다음과 같이 **-SKU** 매개 변수를 추가합니다.


	New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



이 자격 증명이 모음에 소프트웨어 보호 키(이전에 표시된 대로)와 HSM 보호되는 키를 추가할 수 있습니다. HSM으로 보호되는 키를 만들려면 **-Destination** 매개 변수를 'HSM'으로 설정합니다.

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

다음 명령을 사용하여 사용자 컴퓨터의 .PFX 파일에서 키를 가져올 수 있습니다. 이 명령은 키 자격 증명 모음 서비스에서 HSM으로 키를 가져옵니다.

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


다음 명령은 "bring your own key"(BYOK) 패키지를 가져옵니다. 이 시나리오를 사용하면 로컬 HSM에 키를 생성하고 키가 HSM 경계를 벗어나지 않고 이를 주요 자격 증명 모음 서비스의 HSM에 전달합니다.

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

이 BYOK 패키지를 생성하는 방법에 대한 자세한 내용은 [Azure 주요 자격 증명 모음용으로 HSM 보호 키를 생성하여 전송하는 방법](key-vault-hsm-protected-keys.md)을 참조하세요.

## <a id="delete"></a>키 자격 증명 모음 및 연결된 키와 비밀 삭제 ##

주요 자격 증명 모음 및 이를 포함하는 키나 암호가 더 이상 필요하지 않은 경우 [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485(v=azure.200).aspx) cmdlet을 사용하여 주요 자격 증명 모음을 삭제할 수 있습니다.

	Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

또는 키 자격 증명 모음 및 해당 그룹에 포함된 다른 모든 리소스 및 키를 포함하는 전체 Azure 리소스 그룹을 삭제할 수 있습니다.

	Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>기타 Azure PowerShell Cmdlet ##

Azure 주요 자격 증명 모음을 관리하는 데 유용한 기타 명령은 다음과 같습니다.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: 이 명령은 모든 키와 선택한 속성을 테이블 형식으로 가져옵니다.
- `$Keys[0]`:이 명령은 지정된 키에 대한 속성의 전체 목록을 표시합니다.
- `Get-AzureKeyVaultSecret`: 이 명령은 모든 비밀 이름과 선택한 속성을 테이블 형식으로 표시합니다.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: 특정 키를 제거하는 방법 예
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: 특정 비밀을 제거하는 방법 예


## <a id="next"></a>다음 단계 ##

웹 응용 프로그램에서 Azure 주요 자격 증명 모음을 사용하는 후속 자습서는 [웹 응용 프로그램에서 Azure 주요 자격 증명 모음 사용](key-vault-use-from-web-application.md)을 참조하세요.

주요 자격 증명 모음이 사용되는 방식을 보려면 [Azure 주요 자격 증명 모음 로깅](key-vault-logging.md)을 참조하세요.

Azure Key Vault에 대한 최신 Azure PowerShell cmdlet의 목록은 [Azure Key Vault Cmdlet](https://msdn.microsoft.com/library/azure/dn868052(v=azure.200).aspx)을 참조하세요.
 

프로그래밍 참조는 [Azure 주요 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

<!---HONumber=AcomDC_0928_2016-->