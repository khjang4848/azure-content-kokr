<properties
	pageTitle="Azure 배치 계정 만들기 | Microsoft Azure"
	description="클라우드에서 대규모 병렬 작업을 실행하도록 Azure 포털에서 Azure 배치 계정을 만드는 방법에 대해 알아봅니다."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/21/2016"
	ms.author="marsma"/>

# Azure 포털을 사용하여 Azure 배치 계정 만들기

> [AZURE.SELECTOR]
- [Azure 포털](batch-account-create-portal.md)
- [배치 관리 .NET](batch-management-dotnet.md)

[Azure 포털][azure_portal]에서 Azure 배치 계정을 만드는 방법 및 액세스 키 및 계정 URL과 같은 중요한 계정 속성의 위치를 알아봅니다. [응용 프로그램 패키지](batch-application-packages.md) 및 [작업 및 태스크 출력 지속](batch-task-output.md)을 사용할 수 있도록 배치 계정에 대한 배치 가격 책정 및 Azure Storage 계정 연결을 설명합니다.

## 배치 계정 만들기

1. [Azure 포털][azure_portal]에 로그인합니다.

2. **새로 만들기** > **계산** > **배치 서비스**를 클릭합니다.

	![Marketplace에서 배치][marketplace_portal]

3. **새로운 배치 계정** 블레이드가 표시됩니다. 각 블레이드 요소에 대한 설명은 아래 *a* ~ *e* 항목을 참조하세요.

    ![배치 계정 만들기][account_portal]

	a. **계정 이름**: 배치 계정에 대한 고유한 이름입니다. 이 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 *위치* 참조). 소문자, 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

	b. **구독**: 배치 계정을 만들 구독입니다. 하나의 구독만 보유하는 경우 기본적으로 선택됩니다.

	c. **리소스 그룹**: 새 배치 계정에 대한 기존 리소스 그룹을 사용하거나 필요에 따라 새 리소스 그룹을 만듭니다.

	d. **위치**: 배치 계정을 만들 Azure 지역입니다. 구독 및 리소스 그룹에서 지원하는 지역만 옵션으로 표시됩니다.

    e. **저장소 계정**(선택 사항): 새 배치 계정에 연결(링크)한 **범용** 저장소 계정입니다. 자세한 내용은 아래의 [연결된 Azure Storage 계정](#linked-azure-storage-account)을 참조하세요.

4. **만들기**를 클릭하여 계정을 만듭니다.

  포털에서는 계정을 **배포 중**임을 표시하며 완료되면 **배포 성공** 알림이 *알림*에 나타납니다.

## 배치 계정 속성 보기

계정이 만들어지면 **배치 계정 블레이드**를 열어 해당 설정 및 속성에 액세스할 수 있습니다. 배치 계정 블레이드의 왼쪽 메뉴를 사용하여 모든 계정 설정 및 속성에 액세스할 수 있습니다.

![Azure 포털에서 배치 계정 블레이드][account_blade]

* **배치 계정 URL**: [배치 개발 API](batch-technical-overview.md#batch-development-apis)로 만든 응용 프로그램은 계정에서 리소스를 관리하고 작업을 실행할 계정 URL이 필요합니다. 배치 계정 URL의 형식은 다음과 같습니다.

    `https://<account_name>.<region>.batch.azure.com`

![포털에서 배치 계정 URL][account_url]

* **선택키**: 또한 배치 계정에서 리소스를 사용하는 경우 응용 프로그램에는 선택키가 필요합니다. 배치 계정의 선택키를 보거나 다시 생성하려면 배치 계정 블레이드에 있는 왼쪽 메뉴 **검색** 상자에 `keys`를 입력한 다음 **키**를 선택합니다.

    ![Azure 포털에서 배치 계정 키][account_keys]

## 가격

배치 계정은 "무료 계층"으로 제공되며 즉, 배치 계정 자체에 대한 요금이 부과되지 않습니다. 배치 솔루션에서 사용하는 기본 Azure 계산 리소스 및 워크로드를 실행할 때 다른 서비스에서 사용하는 리소스에 대해 요금이 부과됩니다. 예를 들어 풀의 계산 노드 및 Azure Storage에 태스크의 입력 및 출력으로 저장한 데이터에 대해 요금이 부과됩니다. 마찬가지로 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능을 사용하는 경우 응용 프로그램 패키지를 저장하는 데 사용한 Azure Storage 리소스에 대해 요금이 부과됩니다. 자세한 내용은 [배치 가격 책정][batch_pricing]을 참조하세요.

## 연결된 Azure Storage 계정

앞서 언급했듯이 (경우에 따라) **범용** 저장소 계정을 배치 계정에 연결할 수 있습니다. 배치의 [응용 프로그램 패키지](batch-application-packages.md) 기능은 [배치 파일 규칙 .NET](batch-task-output.md) 라이브러리와 마찬가지로 연결된 일반 용도 저장소 계정에서 Blob 저장소를 사용합니다. 이러한 선택적 기능은 배치 태스크를 실행하는 응용 프로그램을 배포하고 생성된 데이터를 유지하도록 지원합니다.

배치는 현재 [Azure Storage 계정 정보](../storage/storage-create-storage-account.md)의 5단계 [저장소 계정 만들기](../storage/storage-create-storage-account.md#create-a-storage-account)에서 설명한 대로 **범용** 저장소 계정 유형*만* 지원합니다. Azure Storage 계정을 배치 계정에 연결할 경우 **범용** 저장소 계정*만* 연결해야 합니다.

!["범용" 저장소 계정 만들기][storage_account]

배치 계정에서 배타적으로 사용할 저장소 계정을 만드는 것이 좋습니다.

>[AZURE.WARNING] 연결된 저장소 계정의 선택키를 다시 생성할 때는 주의해야 합니다. 하나의 저장소 계정 키를 다시 생성하고 연결된 저장소 계정 블레이드에서 **동기화 키**를 클릭합니다. 키를 풀의 계산 노드에 전파한 다음 필요한 경우 다른 키를 다시 생성하고 동기화할 수 있으려면 5분 동안 기다립니다. 동시에 두 키를 다시 생성하는 경우 계산 노드에서 키를 동기화할 수 없으며 저장소 계정에 액세스할 수 없게 됩니다.

  ![저장소 계정 키 다시 생성][4]

## 배치 서비스 할당량 및 제한

Azure 구독 및 다른 Azure 서비스처럼 특정 [할당량 및 제한](batch-quota-limit.md)이 배치 계정에 적용되도록 주의하세요. 배치 계정의 현재 할당량이 계정 **속성**의 포털에 표시됩니다.

![Azure 포털에서 배치 계정 할당량][quotas]

배치 워크로드를 디자인하고 강화할 때 이 할당량에 주의합니다. 예를 들어, 풀이 지정한 계산 노드의 대상 수에 도달하지 않는 경우 배치 계정의 주요 할당량 한도에 도달했을 수 있습니다.

또한 Azure 구독에 대한 단일 배치 계정에 제한되지 않습니다. 단일 배치 계정에서 여러 배치 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독의 배치 계정 간에 워크로드를 배포할 수 있습니다.

대부분의 이러한 할당량은 Azure 포털에 제출된 무료 제품 지원 요청으로 간단히 늘릴 수 있습니다. 할당량 증가를 요청하는 방법에 대한 자세한 내용은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## 다른 배치 계정 관리 옵션

Azure 포털을 사용하는 것 외에도 다음 항목으로 배치 계정을 만들고 관리할 수 있습니다.

* [배치 PowerShell cmdlets](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [배치 관리 .NET](batch-management-dotnet.md)

## 다음 단계

* 배치 서비스 개념 및 기능에 대해 자세히 알아보려면 [Azure 배치 기능 개요](batch-api-basics.md)를 참조하세요. 이 문서에서는 풀, 계산 노드, 작업 및 태스크 등의 기본 배치 리소스에 대해 설명하며 대규모 계산 워크로드 실행을 사용할 수 있는 서비스 기능에 대한 개요를 제공합니다.

* [배치 .NET 클라이언트 라이브러리](batch-dotnet-get-started.md)를 사용하여 배치 가능 응용 프로그램 개발에 대한 기본 사항을 알아봅니다. [소개 자료](batch-dotnet-get-started.md)에서는 배치 서비스를 사용하여 여러 계산 노드에서 워크로드를 실행하는 응용 프로그램 작업 과정을 안내하며 워크로드 파일 스테이징 및 검색을 위해 Azure Storage를 사용하는 과정을 포함합니다.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "저장소 계정 키 다시 생성"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

<!---HONumber=AcomDC_0921_2016-->