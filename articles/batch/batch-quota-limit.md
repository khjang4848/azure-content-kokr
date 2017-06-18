<properties
	pageTitle="배치 서비스 할당량 및 제한 | Microsoft Azure"
	description="기본 Azure 배치 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법에 대해 알아봅니다."
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
	ms.topic="article"
	ms.date="09/10/2016"
	ms.author="marsma"/>

# Azure 배치 서비스에 대한 할당량 및 제한

다른 Azure 서비스와 마찬가지로 배치 서비스와 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 제한 대부분은 Azure 구독 또는 계정 수준에서 적용되는 기본 할당량입니다. 이 문서는 그러한 기본값을 설명하고 할당량 증가를 요청하는 방법을 설명합니다.

배치에서 프로덕션 작업을 실행하려고 계획하는 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료 온라인 [고객지원 요청](#increase-a-quota)을 개설합니다.

>[AZURE.NOTE] 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

## 구독 할당량
**리소스**|**기본 제한**|**최대 제한**
---|---|---
구독당 지역별 배치 계정 | 1 | 50

## 배치 계정 할당량
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## 기타 제한
**리소스**|**최대 제한**
---|---
계산 노드당 [동시 작업](batch-parallel-node-tasks.md) | 4 x 노드 코어 수
배치 계정당 [응용 프로그램](batch-application-packages.md) | 20
응용 프로그램당 응용 프로그램 패키지 | 40
각 응용 프로그램 패키지 크기 | 약 195GB<sup>1</sup>

<sup>1</sup> 최대 블록 Blob 크기에 대한 Azure 저장소 용량 한도

## 배치 할당량 보기

[Azure 포털][portal]에서 배치 계정 할당량을 봅니다.

1. 포털에서 **배치 계정**을 선택한 다음 관심 있는 배치 계정을 선택합니다.

2. 배치 계정의 메뉴 블레이드에서 **속성**을 선택합니다.

3. 속성 블레이드에서 현재 배치 계정에 적용되는 **할당량**을 표시합니다.

	![배치 계정 할당량][account_quotas]

## 할당량 증가

[Azure 포털][portal]에서 할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. 포털 대시보드에서 **도움말 + 지원** 타일을 선택하거나 포털 오른쪽 위 모서리에 있는 물음표(**?**)를 선택합니다.

2. **새 기본 지원 요청** > **기본**을 클릭합니다.

3. **기본 사항** 블레이드:

	a. **문제 형식** > **할당량**

	b. 사용 중인 구독을 선택합니다.

	c. **할당량 형식** > **배치**

	d. **지원 계획** > **할당량 지원-포함됨**

	**다음**을 클릭합니다.

4. **문제** 블레이드:

	a. [비즈니스 영향][support_sev]에 따라 **심각도**를 선택합니다.

	b. **세부 정보**에서 변경하려는 각 할당량과 배치 계정 이름, 새로운 제한을 지정합니다.

	**다음**을 클릭합니다.

5. **연락처 정보** 블레이드:

	a. **기본 연락 방법**을 선택합니다.

	b. 필수 연락처 세부 정보를 확인하고 입력합니다.

	**만들기**를 클릭하여 지원 요청을 제출합니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 참고로 요청 완료까지 업무일 기준 최대 2일이 걸릴 수 있습니다.

## 관련된 항목

* [Azure 포털을 사용하여 Azure 배치 계정 만들기](batch-account-create-portal.md)

* [Azure 배치 기능 개요](batch-api-basics.md)

* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0914_2016-->