<properties
	pageTitle="Azure 구독이 비활성화된 경우 어떻게 해야 합니까? | Microsoft Azure"
	description="Azure 구독이 비활성화되는 원인과 해결 방법을 설명"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="genli"/>

# Azure 구독을 사용하지 않도록 설정할 경우 어떻게 해야 하나요?

> [AZURE.NOTE] 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

Azure 구독이 사용하지 않도록 설정되면 이 문서의 단계를 사용하여 다시 사용하도록 설정할 수 있습니다. 다음과 같은 이유로 Azure 구독이 비활성화될 수 있습니다.

## 지출 한도에 도달했습니다.

사용자가 제품에 포함된 사용량을 실수로 초과하여 요금이 부과되는 일이 없도록 보호하기 위해 [지출 한도](https://azure.microsoft.com/pricing/spending-limits/) 기능을 도입했습니다. 제안에 포함된 월별 할당량을 모두 사용할 경우 해당 청구월의 나머지 기간 동안 서비스가 비활성화되는 내용을 설명합니다. 이 지출 한도를 제거하여 이 문제를 방지하고 [Microsoft Azure 구독에 대한 청구 경고를 설정](billing-set-up-alerts.md)하여 Azure 계정에 대한 청구 작업을 모니터링 및 관리할 수 있습니다.

다음은 지출 한도를 제거하는 방법입니다.

1. [Azure 계정 센터](https://account.windowsazure.com/Home/Index)에 로그인합니다.

2. **구독**을 클릭합니다.

3. 구독을 선택합니다.

4. *구독이 지출 한도에 도달하여 요금이 부과되지 않도록 비활성화되었습니다*라는 메시지가 표시되면 클릭합니다.

	또는 **구독 상태** 영역에서 **지출 한도 제거**를 클릭합니다.

5. 다음 중 적절한 옵션을 선택합니다.

|옵션|결과|
|------|------|
|지출 한도 무기한 제거|다음 청구 기간 시작 시 자동으로 설정되지 않도록 지출 한도를 제거합니다.|
|현재 청구 기간에 대한 지출 한도 제거|다음 청구 기간 시작 시 자동으로 다시 설정되도록 지출 한도를 제거합니다.|

>[AZURE.NOTE] 무료 평가판 구독을 사용하는 경우 지출 한도를 제거하면 무료 평가판이 끝나고 종량제로 구독을 변환합니다.

## 청구 기한 경과

전체 금액에서 미지불된 금액을 결제하면 됩니다. 이렇게 하려면 문서 [Azure 구독에 미지불액이 있다는 알림을 받는지 궁금하신가요?](billing-azure-subscription-past-due-balance.md#what-can-you-do-to-resolve-the-issue)를 참조하세요.

## 신용 카드 한도 초과

이 문제를 해결하려면 다음 방법 중 하나를 사용합니다.

- [다른 신용 카드로 전환](billing-how-to-change-credit-card.md)합니다.
- 비즈니스와 관련된 경우 [지불의 송장 방법으로 전환할](https://azure.microsoft.com/pricing/invoicing/) 수 있습니다.

## 구독이 계정 관리자에 의해 취소됨

>[AZURE.NOTE] 현재 이 방법은 종량제 구독에만 적용됩니다. 다른 구독 유형인 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)해야 합니다.

계정 관리자인 경우 다음 단계를 수행하여 종량제 구독을 다시 활성화할 수 있습니다.

- 취소된 구독을 선택합니다.
- 구독 세부 정보 페이지에서 **다시 활성화** 링크 중 하나를 클릭합니다.

![reactivate-sub](./media/billing-how-to-cancel-azure-subscription/reactivate-sub.png)

## 비종량제 구독을 다시 활성화하는 방법

사용하지 않도록 설정된 구독을 다시 활성화하려면 지원 티켓을 만들어야 합니다. [Azure 포털](https://portal.azure.com/)에 로그인하여 **도움말 + 지원**>**새 지원 요청**을 선택합니다.

![helpandsupportbutton](./media/billing-how-to-create-billing-support-ticket/helpandsupport.png)

포털에 액세스할 수 없으면 Azure 지원 웹 사이트 또는 Azure 계정 센터에서 지원 요청을 제출합니다.

 * [Azure 지원 웹 사이트](https://azure.microsoft.com/support/options/)에서 지원 티켓을 제출하려면 **지원 받기**를 클릭합니다.
 * [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 지원 티켓을 제출하려면 구독을 선택하고 **Microsoft 지원에 문의**를 클릭합니다.

자세한 내용은 [Azure 청구 및 구독 문제에 대한 지원 티켓을 만드는 방법](billing-how-to-create-billing-support-ticket.md)을 참조하세요.

> [AZURE.NOTE] 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

<!---HONumber=AcomDC_0914_2016-->