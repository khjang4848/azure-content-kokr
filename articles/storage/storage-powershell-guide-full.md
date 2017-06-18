<properties
	pageTitle="Azure 저장소와 함께 Azure PowerShell 사용 ㅣ Microsoft Azure"
	description="Azure 저장소의 PowerShell cmdlets를 생성 및 ; blob, 테이블, 큐, 그리고 파일;을 포함하는 관리 저장소 계정구성과 쿼리 저장소 분석, 공유액세스 서명을 만드는 방법을 알아봅니다."
	services="storage"
	documentationCenter="na"
	authors="robinsh"
	manager="carmonm"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="robinsh"/>

# Azure 저장소와 함께 Azure PowerShell 사용

## 개요

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. 작업 기반 명령줄 셸 및 시스템 관리를 위해 특별히 설계된 스크립트 언어로, PowerShell을 사용하면 Azure 서비스 및 응용 프로그램의 관리를 을 쉽게 제어하고 자동화할 수 있습니다. 예를 들어 cmdlet을 사용하여 [Azure 포털](https://portal.azure.com)을 통해 수행할 수 있는 것과 동일한 작업을 수행할 수 있습니다.

이 가이드에서는 [Azure Storage Cmdlet](https://msdn.microsoft.com/library/azure/mt269418.aspx)을 사용하여 Azure 저장소와 다양한 개발 및 관리 작업을 수행할 수 있는 방법을 설명합니다.

이 가이드에서는 이전에 [Azure 저장소](https://azure.microsoft.com/documentation/services/storage/) 및 [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx)을 사용해 본 경험이 있다고 가정합니다. 이 가이드는 Azure 저장소에서 PowerShell을 사용하는 방법을 보여 주는 몇 가지 스크립트를 제공합니다. 각 스크립트를 실행하기 전에 구성에 따라 스크립트 변수를 업데이트 해야 합니다.

이 가이드의 첫 번째 섹션에서는 Azure 저장소 및 PowerShell의 간략 상태를 제공합니다. 자세한 정보 및 지침을 보려면 [Azure 저장소에서 Azure PowerShell을 사용하기 위한 필수 구성 요소](#prerequisites-for-using-azure-powershell-with-azure-storage)에서 시작하세요.


## 5분 안에 Azure 저장소 및 PowerShell 시작하기

이 섹션에서는 5분 안에 PowerShell을 통해 Azure 저장소에 액세스하는 방법을 보여 줍니다.

**Azure에 새로 만들기:** Microsoft Azure 구독 및 해당 구독과 연결된 Microsoft 계정을 가져옵니다. Azure 구입 옵션에 대한 자세한 내용은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/), [구입 옵션](https://azure.microsoft.com/pricing/purchase-options/) 및 [회원 제안](https://azure.microsoft.com/pricing/member-offers/)(MSDN, Microsoft 파트너 네트워크, BizSpark 및 기타 Microsoft 프로그램의 회원인 경우)을 참조하세요.

Azure 구독에 대한 자세한 내용은 [Azure AD(Azure Active Directory)에서 관리자 역할 할당](https://msdn.microsoft.com/library/azure/hh531793.aspx)을 참조하세요.

**Microsoft Azure 구독 및 계정을 만든 후:**

1.	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)를 다운로드하여 설치합니다.
2.	Windows PowerShell ISE(통합 스크립팅 환경)를 시작합니다. 로컬 컴퓨터에서 **시작** 메뉴로 이동합니다. **관리 도구**를 입력하고 클릭하여 관리 도구를 실행합니다. **관리 도구** 창에서**Windows PowerShell ISE**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다.
3.	**Windows PowerShell ISE**에서, **파일** > **새로 만들기**를 클릭하여 새 스크립트 파일을 만듭니다.
4.	이제 Azure 저장소에 액세스할 수 있는 기본 PowerShell 명령을 보여 주는 간단한 스크립트를 살펴보겠습니다. 이 스크립트는 먼저 로컬 PowerShell 환경에 Azure 계정을 추가하기 위해 Azure 계정 자격 증명을 묻습니다. 그런 다음 이 스크립트는 기본 Azure 구독을 설정하고 Azure에 새 저장소 계정을 만듭니다. 그런 다음 이 스크립트는 새 저장소 계정에 새 컨테이너를 만들고 해당 컨테이너에 기존 이미지 파일(Blob)을 업로드합니다. 스크립트가 해당 컨테이너의 모든 Blob을 나열한 후 로컬 컴퓨터에 새 대상 디렉터리를 만들고 이미지 파일을 다운로드합니다.
5.	다음 코드 섹션에서 주석인 **#begin**과 **#end** 간의 스크립트를 선택합니다. CTRL + C 키를 눌러 해당 스크립트를 클립보드로 복사합니다.

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName = "YourSubscriptionName"

    	# Give a name to your new storage account. It must be lowercase!
    	$StorageAccountName = "yourstorageaccountname"

    	# Choose "West US" as an example.
    	$Location = "West US"

    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"

    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"

    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"

    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount

    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

    	# Create a new storage account.
    	New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off

    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName

    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName

    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  

    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
    	#end

5.	**Windows PowerShell ISE**에서, CTRL + V를 눌러 스크립트를 복사합니다. **파일** >**저장**을 클릭합니다. **다른 이름으로 저장** 대화 상자 창에 "mystoragescript"와 같은 스크립트 파일의 이름을 입력합니다. **저장**을 클릭합니다.

6.	이제, 구성 설정에 따라 스크립트 변수를 업데이트해야 합니다. **$SubscriptionName** 변수를 사용자 고유의 구독으로 업데이트해야 합니다. 스크립트에 지정된 다른 변수를 그대로 유지하거나 원하는 대로 해당 변수를 업데이트할 수 있습니다.

	- **$SubscriptionName:** 변수를 사용자 고유의 구독 이름으로 업데이트해야 합니다. 구독 이름을 찾으려면 다음 세 방법 중 하나를 수행 합니다.

		a. **Windows PowerShell ISE**에서, **파일** > **새로 만들기**를 클릭하여 새 스크립트 파일을 만듭니다. 다음 스크립트를 새 스크립트 파일에 복사하고 **디버그** >**실행**을 클릭합니다. 다음 스크립트는 먼저 로컬 PowerShell 환경에 Azure 계정을 추가하기 위해 Azure 계정 자격 증명을 요구한 다음, 로컬 PowerShell 세션에 연결된 모든 구독을 표시합니다. 이 자습서를 수행하는 동안 사용할 구독의 이름을 기록해 둡니다.

			Add-AzureAccount
				Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

		b. [Azure 포털](https://portal.azure.com)에서 구독 이름을 찾아서 복사하려면 왼쪽의 허브 메뉴에서 **구독**을 클릭합니다. 이 가이드에서 스크립트를 실행하는 동안 사용할 구독의 이름을 복사합니다.

		![Azure 포털][Image2]

		c. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 구독 이름을 찾아서 복사하려면 아래로 스크롤하여 포털의 왼쪽에서 **설정**을 클릭합니다. 구독 목록을 보려면 **구독**을 클릭합니다. 이 가이드에서 제공하는 스크립트를 실행하는 동안 사용할 구독의 이름을 복사합니다.

		![Azure 클래식 포털][Image1]

	- **$StorageAccountName:** 스크립트에 지정된 이름을 사용하거나 사용자 저장소 계정에 대한 새 이름을 입력합니다. **중요:** 저장소 계정 이름은 Azure에서 고유해야 합니다. 소문자여야 합니다.

	- **$Location:**: 스크립트에 지정된 "West US(미국 서부)"를 사용하거나 미국 동부, 북유럽 등의 다른 Azure 위치를 선택합니다.

	- **$ContainerName:**: 스크립트에 지정된 이름을 사용하거나 사용자 컨테이너에 대한 새 이름을 입력합니다.

	- **$ImageToUpload:** 로컬 컴퓨터의 그림 경로(예: "C:\\Images\\HelloWorld.png")를 입력합니다.

	- **$DestinationFolder:**: Azure 저장소에서 다운로드한 파일을 보관할 로컬 디렉터리의 경로(예: "C:\\DownloadImages")를 입력합니다.

7.	"mystoragescript.ps1" 파일의 스크립트 변수를 업데이트한 후 **파일** > **저장**을 클릭합니다. 그런 다음 **디버그** > **실행**을 클릭하거나 **F5** 키를 눌러 스크립트를 실행합니다.

스크립트가 실행된 후 다운로드한 이미지 파일을 포함하는 로컬 대상 폴더가 있어야 합니다. 다음 스크린샷은 예제 출력을 보여 줍니다.

![Blob 다운로드][Image3]


> [AZURE.NOTE] "5분 안에 Azure 저장소 및 PowerShell 시작하기' 섹션에서는 Azure 저장소와 함께 Azure PowerShell을 사용하는 방법을 간략히 소개합니다. 자세한 정보 및 지침을 보려면 다음 섹션을 참조하세요.

## Azure 저장소에서 Azure PowerShell을 사용하기 위한 필수 구성 요소
이 가이드에 제공된 PowerShell cmdlet을 실행하기 위해 위에서 설명한 Azure 구독 및 계정이 필요합니다.

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. Azure PowerShell 설치 및 설정에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. 이 가이드를 사용하기 전에 최신 Azure PowerShell 모듈을 다운로드 및 설치하거나, 최신 버전으로 업그레이드하는 것이 좋습니다.

표준 Windows PowerShell 콘솔 또는 Windows PowerShell ISE(통합 스크립팅 환경)에서 cmdlet을 실행할 수 있습니다. 예를 들어, **Windows PowerShell ISE**를 열려면 시작 메뉴로 이동하여 관리 도구를 입력하고, 이 도구를 클릭하여 실행합니다. 관리 도구 창에서 Windows PowerShell ISE를 마우스 오른쪽 단추로 클릭하고 관리자 권한으로 실행을 클릭합니다.

## Azure에서 저장소 계정을 관리하는 방법

### 기본 Azure 구독을 설정하는 방법
Azure PowerShell을 사용하여 Azure 저장소를 관리하려면 Azure Active Directory 인증 또는 인증서 기반 인증을 통해 Azure에서 클라이언트 환경을 인증해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md) 자습서를 참조하세요. 이 가이드에서는 Azure Active Directory 인증을 사용합니다.

1.	Windows PowerShell ISE에서 다음 명령을 입력하여 로컬 PowerShell 환경에 Azure 계정을 추가합니다.

    `Add-AzureAccount`

2.	"Microsoft Azure에 로그인" 창에서 계정과 연결된 메일 주소와 암호를 입력합니다. Azure가 자격 증명 정보를 인증 및 저장한 후 창을 닫습니다.

3.	이제, 다음 명령을 실행하여 로컬 PowerShell 환경에서 Azure 계정을 표시하고 자신의 계정이 나열되어 있는지 확인합니다.

	`Get-AzureAccount`

4.	다음 cmdlet을 실행하여 로컬 PowerShell 세션에 연결된 모든 구독을 표시하고 자신의 구독이 나열되어 있는지 확인합니다.

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.	기본 Azure 구독을 설정하려면 Select-azuresubscription cmdlet을 실행합니다.

	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.	Get-AzureSubscription cmdlet을 실행하여 기본 구독 이름을 확인합니다.

	`Get-AzureSubscription -Default`

7.	Azure 저장소에 사용 가능한 모든 PowerShell cmdlet을 보려면 다음을 실행합니다.

	`Get-Command -Module Azure -Noun *Storage*`

### 새 Azure 저장소 계정을 만드는 방법
Azure 저장소를 사용하려면 저장소 계정이 있어야 합니다. 구독에 연결하도록 컴퓨터를 구성한 후 새 Azure 저장소 계정을 만들 수 있습니다.

1.	Get-AzureLocation cmdlet을 실행하여 사용 가능한 모든 데이터 센터 위치를 찾습니다.

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	이제 New-AzureStorageAccount cmdlet을 실행하여 새 저장소 계정을 만듭니다. 다음 예제는 "West US(미국 동부)" 데이터 센터에 새 저장소 계정을 만듭니다.

    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] 저장소 계정의 이름은 Azure 내에서 고유하며 소문자여야 합니다. 명명 규칙 및 제한에 대해서는 [Azure 저장소 계정 정보](storage-create-storage-account.md), [컨테이너와 Blob, 메타데이터의 이름 지정 및 참조](http://msdn.microsoft.com/library/azure/dd135715.aspx)를 참조하세요.

### 기본 Azure 저장소 계정을 설정하는 방법
구독에서 여러 저장소 계정을 사용할 수 있습니다. 그중 하나를 선택하고 동일한 PowerShell 세션의 모든 저장소 명령에 대한 기본 저장소 계정으로 설정할 수 있습니다. 이렇게 하면 저장소 컨텍스트를 명시적으로 지정하지 않고 Azure PowerShell 저장소 명령을 실행할 수 있습니다.

1.	구독에 대한 기본 저장소 계정을 설정하려면 Set-AzureSubscription cmdlet을 실행할 수 있습니다.

		$SubscriptionName = "Your subscription name"
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.	이제 Get-AzureSubscription cmdlet을 실행하여 저장소 계정이 기본 구독 계정에 연결된 것인지 확인합니다. 이 명령은 현재 저장소 계정을 비롯하여 현재 구독의 구독 속성을 반환합니다.

	    Get-AzureSubscription –Current

### 구독에서 모든 Azure 저장소 계정을 나열하는 방법
각 Azure 구독에는 최대 100개의 저장소 계정을 사용할 수 있습니다. 제한 사항에 대한 최신 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

다음 cmdlet을 실행하여 현재 구독에서 사용하는 저장소 계정의 이름 및 상태를 확인합니다.

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### Azure 저장소 컨텍스트를 만드는 방법
Azure 저장소 컨텍스트는 저장소 자격 증명을 캡슐화하는 PowerShell의 개체입니다. 임의의 후속 cmdlet을 실행하는 동안 저장소 컨텍스트를 사용하면 저장소 계정 및 해당 액세스 키를 명시적으로 지정하지 않고 요청을 인증할 수 있습니다. 저장소 계정 이름과 액세스 키, SAS(공유 액세스 서명) 토큰, 연결 문자열, 익명 등을 다양한 방식으로 저장소 컨텍스트를 만들 수 있습니다. 자세한 내용은 [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx)를 참조하세요.

다음 세 방법 중 하나를 사용하여 저장소 컨텍스트를 만듭니다.

- [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) cmdlet을 실행하여 Azure 저장소 계정에 대한 기본 저장소 액세스 키를 찾습니다. [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) cmdlet을 호출하여 저장소 컨텍스트를 만듭니다.

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Azure 저장소 컨테이너에 대한 공유 액세스 서명 토큰 생성을 생성하여 저장소 컨텍스트를 만듭니다.

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	자세한 내용은 [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) 및 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

- 일부 경우에는 새 저장소 컨텍스트를 만들 때 서비스 끝점을 지정하는 것이 좋습니다. 이런 작업은 Blob 서비스를 통해 저장소 계정에 대한 사용자 지정 도메인 이름을 등록하거나 저장소 리소스에 액세스하는 데 공유 액세스 서명을 사용 하려는 경우에 필요할 수 있습니다. 연결 문자열에 서비스 끝점을 설정하고 아래와 같이 새 저장소 컨텍스트를 만드는 데 사용합니다.

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

저장소 연결 문자열을 구성하는 방법에 대한 자세한 내용은 [연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

이제 컴퓨터를 설정하고 Azure PowerShell을 사용하여 구독 및 저장소 계정을 관리하는 방법을 알아보았으니, 다음 섹션으로 이동하여 Azure Blob 및 스냅숏 Blob을 관리하는 방법을 알아보세요.

### Azure 저장소 키 검색 및 다시 생성 방법

Azure 저장소 계정과 두 계정 키를 함께 제공합니다. 다음 cmdlet을 사용하여 키를 검색할 수 있습니다.

	Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

다음 cmdlet을 사용하여 특정 키를 검색합니다. 유효한 값은 Primary 및 Secondary입니다.

	(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

	(Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

키를 다시 생성하려는 경우 다음 cmdlet을 사용합니다. -KeyType에 대한 유효한 값은 "Primary" 및 "Secondary"입니다.

	New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

	New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## Azure Blob 관리 방법
Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다. 이 섹션에서는 Azure Blob 저장소 서비스 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [.NET을 사용하여 Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md) 및 [Blob 서비스 개념](http://msdn.microsoft.com/library/azure/dd179376.aspx)을 참조하세요.

### 컨테이너를 만드는 방법
Azure 저장소의 모든 Blob은 컨테이너에 있어야 합니다. New-AzureStorageContainer cmdlet을 사용하여 개인 컨테이너를 만들 수 있습니다.

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] 익명 읽기 액세스의 세가지 수준은 **해제**, **Blob** 및 **컨테이너**입니다. Blob에 대한 익명 액세스를 방지하려면 권한 매개 변수를 **해제**로 설정합니다. 기본적으로 새 컨테이너는 전용이며 계정 소유자만 액세스할 수 있습니다. 익명 공용 읽기 권한을 Blob 리소스에 대해 허용하지만 컨테이너 메타데이터나 컨테이너의 Blob 목록에 대해서는 허용하지 않으려면, 사용 권한 매개 변수를 **Blob**으로 설정하세요. Blob 리소스, 컨테이너 메타데이터 및 컨테이너의 Blob 목록에 대한 전체 공용 읽기 권한을 허용하려면, 권한 매개 변수를 **컨테이너**로 설정하세요. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.

### 컨테이너에 Blob을 업로드하는 방법
Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 자세한 내용은 [블록 Blob,추가 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

컨테이너에 Blob을 업로드하기 위해 [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) cmdlet을 사용할 수 있습니다. 기본적으로 이 명령은 로컬 파일을 블록 Blob에 업로드합니다. Blob의 종류를 지정하기 위해 -BlobType 매개 변수를 사용할 수 있습니다.

다음 예제에서는 [Get-ChildItem ](http://technet.microsoft.com/library/hh849800.aspx)cmdlet을 실행하여 지정된 폴더의 모든 파일을 가져온 다음 파이프라인 연산자를 사용하여 다음 cmdlet에 전달합니다. [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) cmdlet은 컨테이너에 로컬 파일을 업로드합니다.

    Get-ChildItem –Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### 컨테이너에서 Blob을 다운로드하는 방법
다음 예제에서는 컨테이너에서 Blob을 다운로드하는 방법을 보여 줍니다. 이 예제는 먼저 저장소 계정 이름 및 해당 기본 액세스 키를 포함하는 저장소 계정 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) cmdlet을 사용하여 Blob 참조를 검색합니다. 그리고 [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) cmdlet을 사용하여 로컬 대상 폴더에 Blob을 다운로드합니다.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### 저장소 컨테이너 간에 Blob을 복사하는 방법
저장소 계정 및 지역에 걸쳐 비동기적으로 Blob을 복사할 수 있습니다. 다음 예제에서는 두 개의 저장소 계정으로 저장소 컨테이너 간에 Blob을 복사하는 방법을 보여 줍니다. 이 예제에서는 먼저 원본 및 대상 저장소 계정의 변수를 설정하고 각 계정에 대한 저장소 컨텍스트를 만듭니다. 그런 다음, [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) cmdlet을 사용하여 원본 컨테이너에서 대상 컨테이너로 Blob을 복사합니다. 이 예제에서는 원본 및 대상 저장소 계정과 컨테이너가 이미 존재한다고 가정합니다.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

이 예제에서는 비동기 복사본을 수행합니다. [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) cmdlet을 실행하여 각 복사본의 상태를 모니터링할 수 있습니다.

### 보조 위치에서 blob를 복사하는 방법
활성화 RA-GRS 계정의 보조 locatioon에서 blob을 복사할 수 있습니다.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### Blob을 삭제하는 방법
Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 해당 참조에 대해 Remove-AzureStorageBlob cmdlet을 호출합니다. 다음 예제에서는 지정된 컨테이너의 모든 Blob을 삭제합니다. 이 예제에서는 먼저 저장소 계정에 대한 변수를 설정하고 저장소 컨텍스트를 만듭니다. 그런 다음, [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) cmdlet을 사용하여 Blob 참조를 검색하고 [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) cmdlet을 실행하여 Azure 저장소의 컨테이너에서 BLOB을 제거합니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## Azure Blob 스냅숏을 관리하는 방법
Azure를 사용하면 Blob의 스냅숏을 만들 수 있습니다. 스냅숏은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다. 스냅숏이 생성된 후에는 읽거나 복사하거나 삭제할 수 있지만 수정할 수는 없습니다. 스냅숏을 사용하면 특정 시점에서 표시된 대로 Blob을 백업할 수 있습니다. 자세한 내용은 [Blob의 스냅숏 만들기](http://msdn.microsoft.com/library/azure/hh488361.aspx)를 참조하세요.

### Blob 스냅숏을 만드는 방법
Blob을 만들려면 먼저 Blob 참조를 가져온 다음 그에 대한 `ICloudBlob.CreateSnapshot` 메서드를 호출합니다. 다음 예제에서는 먼저 저장소 계정에 대한 변수를 설정하고 저장소 컨텍스트를 만듭니다. 그런 다음, [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) cmdlet을 사용하여 Blob 참조를 검색하고 [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) 메서드를 실행하여 스냅숏을 만듭니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### Blob의 스냅숏을 나열하는 방법
Blob에 대해 원하는 수 만큼 많은 스냅샷을 만들 수 있습니다. Blob에 연결된 스냅숏을 나열하여 최신 스냅숏을 추적할 수 있습니다. 다음 예제에서는 미리 정의된 Blob을 사용하며 [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) cmdlet을 호출하여 Blob의 스냅숏을 나열합니다.

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### Blob의 스냅숏을 복사하는 방법
Blob의 스냅숏을 복사하여 Blob의 스냅숏을 복원할 수 있습니다. 자세한 내용 및 제한 사항에 대해서는 [Blob의 스냅숏 만들기](http://msdn.microsoft.com/library/azure/hh488361.aspx)를 참조하세요. 다음 예제에서는 먼저 저장소 계정에 대한 변수를 설정하고 저장소 컨텍스트를 만듭니다. 그런 다음, 컨테이너 및 Blob 이름 변수를 정의합니다. 이 예제는[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) cmdlet을 사용하여 Blob 참조를 검색하고 [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) 메서드를 실행하여 스냅숏을 만듭니다. 그런 다음, [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) cmdlet을 실행하여 원본 Blob에 대한 ICloudBlob 개체를 통해 Blob의 스냅숏을 복사합니다. 이 예제를 실행하기 전에 구성에 따라 변수를 업데이트해야 합니다. 다음 예제에서는 원본 및 대상 컨테이너와 원본 Blob이 이미 존재한다고 가정합니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

이제 Azure PowerShell을 사용하여 Azure Blob 및 Blob 스냅숏을 관리하는 방법을 알아보았으니, 다음 섹션으로 이동하여 테이블, 큐 및 파일을 관리하는 방법을 알아보세요.

## Azure 테이블 및 테이블 엔터티를 관리하는 방법
Azure 테이블 저장소 서비스는 구조화된 비관계형 데이터의 거대 집합을 저장하고 쿼리하는 데 사용할 수 있는 NoSQL 데이터 저장소입니다. 서비스의 주요 구성 요소로는 테이블, 엔터티 및 속성이 있습니다. 테이블은 엔터티 컬렉션입니다. 엔터티는 속성의 집합입니다. 각 엔터티는 모두 이름 값 쌍으로 구성된 속성을 최대 252개 가질 수 있습니다. 이 섹션에서는 Azure 테이블 저장소 서비스 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](http://msdn.microsoft.com/library/azure/dd179338.aspx) 및 [.NET을 사용하여 Azure 테이블 저장소 시작](storage-dotnet-how-to-use-tables.md)을 참조하세요.

다음 하위 섹션에서는 Azure PowerShell을 사용하여 Azure 테이블 저장소 서비스를 관리하는 방법을 알아봅니다. 여기서 다루는 시나리오에는 **테이블** **생성**, **삭제**, **검색**뿐만 아니라 테이블 엔터티 **삭제**, **추가**, **쿼리**도 포함됩니다.

### 테이블을 만드는 방법
모든 테이블은 Azure 저장소 계정에 있어야 합니다. 다음 예제에서는 Azure 저장소에 테이블을 만드는 방법을 보여 줍니다. 이 예제는 먼저 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 계정 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음, [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet을 사용하여 Azure 저장소에 테이블을 만듭니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### 테이블을 검색하는 방법
저장소 계정에서 한 테이블 또는 모든 테이블을 쿼리하고 검색할 수 있습니다. 다음 예제에서는 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) cmdlet을 사용하여 지정된 테이블을 검색하는 방법을 보여 줍니다.

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

매개 변수 없이 Get-AzureStorageTable cmdlet을 호출하는 경우 저장소 계정에 대한 모든 저장소 테이블을 가져옵니다.

### 테이블을 삭제하는 방법
[Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) cmdlet을 사용하여 저장소 계정에서 테이블을 삭제할 수 있습니다.

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### 테이블 엔터티를 관리하는 방법
현재, Azure PowerShell은 테이블 엔터티를 직접 관리할 수 있는 cmdlet을 제공하지 않습니다. 테이블 엔터티에 대한 작업을 수행하려면 [.NET용 Azure 저장소 클라이언트 라이브러리](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)에 지정된 클래스를 사용할 수 있습니다.

#### 테이블 엔터티를 추가하는 방법
테이블에 엔터티를 추가하려면 먼저 엔터티 속성을 정의하는 개체를 만듭니다. 엔터티는 3개의 시스템 속성 즉, **PartitionKey**, **RowKey**, **Timestamp**를 포함하여 최대 255개의 속성을 포함할 수 있습니다. **PartitionKey** 및 **RowKey**의 값을 삽입 및 업데이트하는 작업은 사용자가 해야 합니다. 서버는 수정할 수 없는 **Timestamp** 값을 관리합니다. **PartitionKey** 및 **RowKey**가 함께 테이블 내의 모든 엔터티를 고유하게 식별합니다.

-	**PartitionKey**: 엔터티가 저장되는 파티션을 결정합니다.
-	**RowKey**: 파티션 내에서 엔터티를 고유하게 식별합니다.

엔터티에 대한 사용자 지정 속성을 최대 252개 정의할 수 있습니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](http://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.

다음 예제에서는 테이블에 엔터티를 추가하는 방법을 보여 줍니다. 이 예제에는 직원 테이블을 검색하고 여기에 여러 엔터티를 추가하는 방법을 보여 줍니다. 먼저, 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 계정 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) cmdlet을 사용하여 지정된 테이블을 검색합니다. 테이블이 없는 경우 [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet이 Azure 저장소에 테이블을 만드는 데 사용됩니다. 각 엔터티의 파티션 및 행 키를 지정하여 테이블에 엔터티를 추가하는 사용자 지정 Add-Entity를 정의합니다. Add-Entity 함수는 [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) 클래스에 대한 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) cmdlet을 호출하여 엔터티 개체를 만듭니다. 나중에, 이 예제는 이 엔터티 개체에 대한 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) 메서드를 호출하여 테이블에 추가합니다.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### 테이블 엔터티를 쿼리하는 방법
테이블을 쿼리하기 위해 [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) 클래스를 사용합니다. 다음 예제에서는 가이드의 엔터티를 추가하는 방법 섹션에 지정된 스크립트 이미 실행한 것으로 가정합니다. 이 예제는 먼저 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) cmdlet을 사용하여 앞서 만든 "Employees" 테이블을 검색합니다. Microsoft.WindowsAzure.Storage.Table.TableQuery 클래스에 대해 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) cmdlet을 호출하면 새 쿼리 개체가 생성됩니다. 이 예제에서는 값이 문자열 필터에 지정된 대로 1 인 'ID' 열을 포함하는 엔터티를 찾습니다. 자세한 내용은 [테이블 및 엔터티 쿼리](http://msdn.microsoft.com/library/azure/dd894031.aspx)를 참조하세요. 이 쿼리를 실행하면 필터 조건과 일치하는 모든 엔터티가 반환됩니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### 테이블 엔터티를 삭제하는 방법
파티션 및 행 키를 사용하여 엔터티를 삭제할 수 있습니다. 다음 예제에서는 가이드의 엔터티를 추가하는 방법 섹션에 지정된 스크립트 이미 실행한 것으로 가정합니다. 이 예제는 먼저 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) cmdlet을 사용하여 앞서 만든 "Employees" 테이블을 검색합니다. 테이블이 있는 경우 이 예제는 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) 메서드를 호출하여 파티션 및 행 키 값을 기준으로 엔터티를 검색합니다. 그런 다음 엔터티를 삭제할 [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) 메서드로 전달합니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## Azure 큐 및 큐 메시지를 관리하는 방법
Azure 큐 저장소는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 이 섹션에서는 Azure 큐 저장소 서비스 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [.NET을 사용하여 Azure 큐 저장소 시작](storage-dotnet-how-to-use-queues.md)을 참조하세요.

이 섹션에서는 Azure PowerShell을 사용하여 Azure 큐 저장소 서비스를 관리하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입** 및 **삭제**뿐만 아니라 큐 **생성**, **삭제** 및 **검색**도 포함됩니다.

### 큐를 만드는 방법
다음 예제는 먼저 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 계정 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) cmdlet을 호출하여 'queuename'이라는 큐를 만듭니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Azure 큐 서비스에 대한 명명 규칙에 대해서는 [큐 및 메타데이터 이름 지정](http://msdn.microsoft.com/library/azure/dd179349.aspx)을 참조하세요.

### 큐를 검색하는 방법
저장소 계정의 특정 큐 또는 모든 큐의 목록을 쿼리하고 검색할 수 있습니다. 다음 예제에서는 [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) cmdlet을 사용하여 지정된 큐를 검색하는 방법을 보여 줍니다.

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

매개 변수 없이 [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) cmdlet을 호출하는 경우 모든 큐의 목록을 가져옵니다.

### 큐를 삭제하는 방법
큐와 해당 큐에 포함된 모든 메시지를 삭제하려면 Remove-AzureStorageQueue cmdlet을 호출하세요. 다음 예제에서는 Remove-AzureStorageQueue cmdlet을 사용하여 지정된 큐를 삭제하는 방법을 보여 줍니다.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### 큐에 메시지를 삽입하는 방법
기존 큐에 메시지를 삽입하려면 먼저 [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) 클래스의 새 인스턴스를 만듭니다. 그런 다음, [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 메서드를 호출합니다. 문자열(UTF-8 형식) 또는 바이트 배열에서 CloudQueueMessage를 만들 수 있습니다.

다음 예제에서는 큐에 메시지를 추가하는 방법을 보여 줍니다. 이 예제는 먼저 저장소 계정 이름 및 해당 액세스 키를 포함하는 저장소 계정 컨텍스트를 사용하여 Azure 저장소에 대한 연결을 설정합니다. 그런 다음 [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) cmdlet을 사용하여 지정된 큐를 검색합니다. 큐가 있는 경우 [New-Object](http://technet.microsoft.com/library/hh849885.aspx) cmdlet이 [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx)클래스의 인스턴스를 만드는 데 사용됩니다. 나중에, 이 예제는 이 메시지 개체에 대한 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) 메서드를 호출하여 큐에 추가합니다. 다음은 큐를 검색하고 'MessageInfo' 메시지를 삽입하는 코드입니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### 다음 메시지에서 큐를 제거하는 방법
다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) 메서드를 호출하면 큐에서 다음 메시지를 가져옵니다. **GetMessage**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) 메서드도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **DeleteMessage**를 호출합니다.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## Azure 파일 공유 및 파일을 관리하는 방법
Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 응용 프로그램을 위한 공유 저장소를 제공합니다. Microsoft Azure 가상 컴퓨터 및 클라우드 서비스는 탑재된 공유를 통해 여러 응용 프로그램 구성 요소에서 파일 데이터를 공유할 수 있으며 온-프레미스 응용 프로그램은 파일 저장소 API 또는 Azure PowerShell을 통해 공유의 파일 데이터에 액세스할 수 있습니다.

Azure 파일 저장소에 대한 자세한 내용은 [Windows에서 Azure 파일 저장소 시작](storage-dotnet-how-to-use-files.md) 및 [파일 서비스 REST API](http://msdn.microsoft.com/library/azure/dn167006.aspx)를 참조하세요.

## 저장소 분석을 설정 및 쿼리하는 방법
[Azure 저장소 분석](storage-analytics.md)을 통해 Azure 저장소 계정에서 메트릭(저장소 메트릭)을 수집하고 저장소 계정에 전송된 요청에 대한 데이터(저장소 로깅)를 기록할 수 있습니다. 저장소 메트릭을 사용하여 저장소 계정의 상태를 모니터링하고, 저장소 로깅을 사용하여 저장소 계정에 대한 문제를 진단 및 해결할 수 있습니다. 기본적으로 저장소 메트릭은 저장소 서비스에 대해 사용되지 않습니다. Azure 포털 또는 Windows PowerShell을 사용하거나 저장소 클라이언트 라이브러리를 사용하여 프로그래밍 방식으로 모니터링을 사용하도록 설정할 수 있습니다. 저장소 로깅은 서버 쪽에서 발생하며, 이를 통해 저장소 계정의 성공한 요청 및 실패한 요청에 대한 세부 정보를 기록할 수 있습니다. 이러한 로그를 사용하여 테이블, 큐 및 Blob에 대한 읽기, 쓰기 및 삭제 작업뿐만 아니라 실패한 요청의 이유에 대한 세부 정보를 볼 수 있습니다.

PowerShell을 사용하여 저장소 메트릭 데이터를 사용하도록 설정하고 확인하는 방법을 알아보려면 [PowerShell을 사용하여 저장소 메트릭을 사용하도록 설정하는 방법](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell)을 참조하세요.

PowerShell을 사용하여 로깅 데이터 저장소 사용 및 검색하는 방법을 배우려면 [PowerShell을 사용하여 로깅 저장소 사용하는 방법](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell)과 [로그 데이터를 로깅 저장소에서 찾기](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)를 참조하세요. 저장소 메트릭 및 저장소 로깅을 사용하여 저장소 문제를 해결하는 방법에 대한 자세한 정보는 [Microsoft Azure 저장소 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

## SAS(공유 액세스 서명) 및 저장된 액세스 정책을 관리하는 방법
공유 액세스 서명은 Azure 저장소를 사용하는 모든 응용 프로그램에 대한 보안 모델의 중요한 부분입니다. 공유 액세스 서명은 저장소 계정에 대한 제한된 권한을 계정 키가 필요하지 않은 클라이언트에 제공하는 데 유용합니다. 기본적으로는 저장소 계정 소유자만 해당 계정 내의 Blob, 테이블 및 큐에 액세스할 수 있습니다. 사용하는 서비스 또는 응용 프로그램에서 자신의 액세스 키를 공유하지 않고 다른 클라이언트가 이러한 리소스를 사용할 수 있도록 설정해야 할 경우 다음과 같은 세 가지 옵션이 제공됩니다.

- 컨테이너 및 해당 Blob에 대한 익명 읽기 액세스를 허용하도록 컨테이너의 사용 권한을 설정합니다. 테이블 또는 큐에 대해서는 이렇게 할 수 없습니다.
- 일정 기간 동안 컨테이너, Blob, 큐 및 테이블에 제한된 액세스 권한을 부여하는 공유 액세스 서명을 사용합니다.
- 컨테이너나 해당 Blob, 큐 또는 테이블에 대한 공유 액세스 서명을 더 효과적으로 제어하기 위한 저장된 액세스 정책을 사용합니다. 저장된 액세스 정책을 사용하여 시작 시간, 만료 시간 또는 서명 사용 권한을 변경하거나 발급 후에 서명을 취소할 수 있습니다.

공유 액세스 서명은 다음 두 가지 형식 중 하나일 수 있습니다.

- **Ad hoc SAS**: 애드혹 SAS를 만들 때 SAS의 시작 시간, 만료 시간 및 사용 권한이 SAS URI에 모두 지정됩니다. 이 유형의 SAS는 컨테이너, Blob, 테이블 또는 큐에서 만들 수 있으며, 취소할 수 없습니다.
- **저장된 액세스 정책 사용 SAS:** 저장된 액세스 정책은 리소스 컨테이너(Blob 컨테이너, 테이블 또는 큐)에서 정의되며, 하나 이상의 공유 액세스 서명에 대한 제약 조건을 관리하는 데 사용할 수 있습니다. SAS를 공유 액세스 정책과 연결할 경우 SAS는 저장된 액세스 정책에 대해 정의된 제약 조건(시작 시간, 만료 시간 및 사용 권한)을 상속합니다. 이 유형의 SAS는 취소할 수 있습니다.

자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md) 및 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.

다음 섹션에서는 Azure 테이블에 대한 공유 액세스 서명 토큰 및 저장된 액세스 정책을 만드는 방법을 배웁니다. Azure PowerShell은 컨테이너, Blob, 큐에 대해 유사한 cmdlet을 제공합니다. 이 섹션의 스크립트를 실행하려면 [Azure PowerShell 버전 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) 이상을 다운로드하세요.

### 공유 액세스 서명 토큰에 따라 정책을 만드는 방법
저장된 새 액세스 정책을 만들려면 New-AzureStorageTableStoredAccessPolicy cmdlet을 사용합니다. 그런 다음 [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) cmdlet을 호출하여 Azure 저장소 테이블에 대한 새 정책 기반의 공유 액세스 서명 토큰을 만듭니다.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### 임시(취소 불가능한) 공유 액세스 서명 토큰을 만드는 방법
Azure 저장소 테이블에 대한 임시(취소 불가능한) 공유 액세스 서명 토큰을 만들려면 [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) cmdlet을 사용합니다.

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### 저장된 액세스 정책을 만드는 방법
Azure 저장소 테이블에 대해 저장된 새 액세스 정책을 만들려면 New-AzureStorageTableStoredAccessPolicy cmdlet을 사용합니다.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### 저장된 액세스 정책을 업데이트하는 방법
Azure 저장소 테이블에 대해 저장된 기존 새 액세스 정책을 업데이트하려면 Set-AzureStorageTableStoredAccessPolicy cmdlet을 사용합니다.

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### 저장된 액세스 정책을 삭제하는 방법
Azure 저장소 테이블에서 저장된 액세스 정책을 삭제하려면 Remove-AzureStorageTableStoredAccessPolicy cmdlet을 사용합니다.

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## 미국 정부 및 Azure 중국용 Azure 저장소를 사용하는 방법
[미국 정부용 Azure Government](https://azure.microsoft.com/features/gov/), [글로벌 Azure용 AzureCloud](https://portal.azure.com) 및 [중국의 21Vianet에서 운영하는 Azure용 AzureChinaCloud](http://www.windowsazure.cn/) 등의 Azure 환경은 Microsoft Azure의 독자적인 배포입니다. 미국 정부 및 Azure 중국을 위한 새로운 Azure 환경을 배포할 수 있습니다.

AzureChinaCloud와 함께 Azure 저장소를 사용하려면 AzureChinaCloud와 연결된 저장소 컨텍스트를 만들어야 합니다. 시작하려면 다음 단계를 따르세요.

1.	[Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet을 실행하여 사용 가능한 Azure 환경을 확인합니다.

    `Get-AzureEnvironment`

2.	Windows PowerShell에 Azure 중국 계정을 추가합니다.

    `Add-AzureAccount –Environment AzureChinaCloud`

3.	AzureChinaCloud 계정에 대한 저장소 컨텍스트를 만듭니다.

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

[U.S. Azure Government](https://azure.microsoft.com/features/gov/)에서 Azure 저장소를 사용하려면 새 환경을 정의한 다음 이 환경으로 새 저장소 컨텍스트를 생성해야 합니다.

1.	[Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet을 실행하여 사용 가능한 Azure 환경을 확인합니다.

    `Get-AzureEnvironment`

2.	Windows PowerShell에 Azure 미국 정부 계정을 추가합니다.

    `Add-AzureAccount –Environment AzureUSGovernment`

3.	AzureUSGovernment 계정에 대한 저장소 컨텍스트를 만듭니다.

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

자세한 내용은 다음을 참조하세요.

- [Microsoft Azure Government 개발자 가이드](../azure-government-developer-guide.md)
- [중국 서비스에서 응용 프로그램을 만들 때의 차이점 개요](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## 다음 단계
이 가이드에서는 Azure PowerShell을 사용하여 Azure 저장소를 관리하는 방법에 대해 알아보았습니다. 다음은 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [Azure 저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)
- [Azure 저장소 PowerShell Cmdlet(영문)](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Windows PowerShell 참조](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next

<!---HONumber=AcomDC_0914_2016-->