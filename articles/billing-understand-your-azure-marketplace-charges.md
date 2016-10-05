<properties
	pageTitle="Azure 마켓플레이스 요금의 이해 | Microsoft Azure"
	description="마켓플레이스 주문과 관련된 요금을 이해하는 방법을 설명합니다."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="cjiang"/>

# Azure 마켓플레이스 요금의 이해
이 문서에서는 마켓플레이스 주문과 관련된 요금을 이해하는 방법을 개략적으로 설명합니다. 청구서를 검토할 때 다양한 가격 책정 유형(기본만, 사용량만, 기본 + 사용량)이 있음을 염두에 두어야 합니다. 사용자가 발주하는 마켓플레이스 주문마다 고유한 월별 청구 주기가 적용됩니다. 이에 대한 토픽은 아래에서 자세히 다루겠습니다.

> [AZURE.NOTE] 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## 마켓플레이스 청구서 검토
[Azure 계정 포털]( https://account.windowsazure.com/subscriptions/)에 로그인하면 현재와 과거의 청구서를 검토 및 다운로드할 수 있습니다.

청구서를 보거나 다운로드하려면

1. 조직 ID를 사용하여 Azure 계정 포털에 로그인합니다.
2. **마켓플레이스**를 클릭합니다. 표시되는 목록은 해당 Azure 계정을 통해 발주된 마켓플레이스 주문의 전체 목록입니다. 주문이 부모 Azure 구독 이름 및 ID별로 그룹화된 것을 볼 수 있습니다.

	![마켓플레이스 주문](./media/billing-understand-your-azure-marketplace-charges/marketplace-orders.png)

3. 표시된 주문 중 하나를 클릭하면 현재 청구 주기에 대한 해당 주문의 요약 개요로 이동됩니다. 다른 가격 책정 모델에 대한 설명은 [마켓플레이스 FAQ](https://azure.microsoft.com/marketplace/faq/)에서 찾을 수 있습니다.

	![주문 요약](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

요약 보기에는 각 주문에 대해 다음 정보가 있습니다.
- 가격
- 대금 청구 주기
- 구매 날짜
- 연결된 부모 구독
- 청구 통화

이 정보가 표시되는 방식이 약간 바뀌었습니다. 이전에는 세금을 포함한 주문 가격이 표시되었지만 이제는 사용자가 소속된 국가에 따라 세금이 포함된 가격이 표시될 수도 있고 세금을 제외한 가격이 표시될 수도 있습니다. 그리고 가격의 위치가 오른쪽 탐색에서 가운데로 이동되었습니다. 또한 이러한 요금이 어떤 Azure 구독에 연결되는지 간편하게 추적할 수 있도록 오른쪽 탐색에 Azure 구독 이름 및 ID와 **지불 방법 변경** 옵션이 추가되었습니다.

## 지불 방법 및 주문 관리 업데이트
요약 페이지에는 사용자 작업이 있어서 사용자가 지불 모델 및 주문 관리 기능을 업데이트할 수 있습니다.

> [AZURE.NOTE] 조직 ID를 사용하여 개인 정보를 변경하려면 지원을 통해 티켓을 기록해야 합니다.

지불 방법을 업데이트하려면 페이지 오른쪽의 **지불 방법 변경** 링크를 클릭합니다.

![주문 요약](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

그러면 원하는 지불 방법으로 변경할 수 있는 다른 포털로 이동될 것입니다.

지불 방법을 변경하려면 다음 단계를 수행합니다.

1. **지불 방법 변경**을 클릭합니다.

	![구독](./media/billing-understand-your-azure-marketplace-charges/subscriptions.jpg)

2. 원하는 지불 방법을 선택합니다. **지불 방법** 옵션을 사용하여 신용 카드를 선택할 수 있습니다. **새 지불 방법 추가** 옵션을 사용하여 새 신용 카드를 추가할 수 있습니다.

	![지불 방법 변경](./media/billing-understand-your-azure-marketplace-charges/change-payment-method.jpg)

## 주문 관리
요약 개요 페이지에는 **주문 관리** 링크가 표시됩니다. 이 링크를 클릭하면 모든 마켓플레이스 주문을 보고, 서비스 상태를 보고, 지원을 요청할 수 있는 포털로 이동됩니다.

![포털](./media/billing-understand-your-azure-marketplace-charges/portal.jpg)

지원을 요청하려면 **도움말 + 지원** 타일을 클릭합니다. 그러면 다음을 수행할 수 있는 **도움말 + 지원** 페이지로 이동합니다.
- 새 지원 요청을 엽니다.
- 기존 지원 요청을 관리합니다.
- 리소스 상태를 관리합니다.

![지원 요청](./media/billing-understand-your-azure-marketplace-charges/request-support.jpg)

## 청구 내역
요약 페이지에는 가상 컴퓨터처럼 상품을 기반으로 사용량을 볼 수 있는 새 기능이 추가되었습니다. 이제는 청구 주기를 기반으로 현재 기간과 완료된 기간의 사용량을 다운로드할 수 있습니다. 사용량을 보려면 보고 싶은 기간의 **사용량 다운로드** 링크를 클릭합니다.

실제 요금을 보고 싶으면 **요금 보기**를 클릭합니다. 그러면 세금이 포함된 모든 요금을 볼 수 있는 다른 포털로 리디렉션됩니다. 조직 ID를 사용하는 경우에는 **요금 보기** 단추를 사용할 수 없고 요금 요약 정보를 요청하는 지원 티켓을 제출해야 합니다.

![청구 내역](./media/billing-understand-your-azure-marketplace-charges/billing-history.png)

실제 요금을 보려면 다음 단계를 수행합니다.

1. **청구 내역** 페이지에서 **요금 보기**를 클릭합니다.

	[주문 내역](https://account.microsoft.com/billing/orders#/)을 보여 주는 새 탭이 열립니다.

    청구 및 계정 관리 포털에서 가공되지 않은 사용량 정보에 액세스하려면 페이지 하단의 지원 링크를 클릭하여 지원을 요청해야 합니다.

	![주문 내역](./media/billing-understand-your-azure-marketplace-charges/order-history.jpg)

2. 검토하려는 주문을 찾아서 **세부 정보**를 클릭하면 소계, 세금 및 총 요금이 포함된 요금 명세서를 볼 수 있습니다.

	![주문 세부 정보](./media/billing-understand-your-azure-marketplace-charges/order-details.jpg)

> [AZURE.NOTE] 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

<!---HONumber=AcomDC_0824_2016-->
