<properties
	pageTitle="등록 시 신용 카드 또는 직불 카드가 거부된 경우 | Microsoft Azure"
	description="Azure에 등록하려고 할 때 내 신용 카드 또는 직불 카드가 거부되는 경우 문제를 해결하는 방법에 대해 알아봅니다."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing,top-support-issue"
	keywords="신용 카드 거부, 직불 카드 거부, 신용 카드가 거부됨, 신용 카드가 인식되지 않음"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="cjiang"/>

# 등록 시 신용 카드 또는 직불 카드가 거부된 경우

> [AZURE.NOTE] 기존 카드를 변경하거나 제거하려는 경우 [결제 정보를 관리](billing-how-to-change-credit-card.md)하는 방법에 대해 알아보세요.

> 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

Azure에 등록하려고 할 때 신용 카드 또는 직불 카드가 거부된 경우 아래 원인 중 하나로 인한 문제가 있을 수 있습니다.

- [허용되는 신용 카드 및 직불 카드 공급자는 국가에 따라 다양하므로 국제 카드를 사용해야 할 수 있습니다](#credit-or-debit-card-details). 신용 카드 선택을 위한 드롭다운 목록에는 선택한 국가에서 유효한 옵션만 표시됩니다.

- [신용 카드 또는 직불 카드 정보가 부정확하거나 불완전합니다](#credit-or-debit-card-details). 계속하려면 지불 정보를 업데이트해야 합니다.

- [두 번째 무료 평가판을 활성화하려고 합니다](#azure-free-trial). Azure 무료 평가판 제품은 사용자당 한 번으로 제한됩니다.

- 가상 카드 또는 선불 카드를 사용하고 있습니다. 가상 카드, 선불 카드 또는 직불 카드는 유효한 지불 옵션으로 허용되지 않습니다.

- 비활성 또는 차단된 카드일 수 있습니다. 실제로 유효한 신용 카드나 직불 카드를 사용하고 있으면 해당 카드가 활성 상태인지, 보안 정책으로 인해 은행에서 온라인 거래를 차단하지 않는지 확인해야 합니다. 이 문제를 해결하려면 은행에 문의하세요.

## 신용 카드 또는 직불 카드 정보

다음 단계에 따라 신용 카드 또는 직불 카드 정보가 유효한지 확인합니다.

- 사용 중인 신용 또는 직불 카드가, 선택한 국가의 드롭다운 목록에 있어야 합니다.
	
	- 일부 경우에는 로컬/기본 통화로 Azure 서비스에 대해 지불하더라도 로컬이 아닌 Microsoft 엔터티를 통해 서비스를 구입하고 신용 카드가 로컬이 아닌 은행에서 처리됩니다. 본인의 신용 카드를 국제 거래에 사용할 수 있는지 확인하려면 해당 은행 또는 카드 발급자에게 문의하세요.
	
	- [지원되는 국가 및 통화](billing-countries-and-currencies.md)를 참조하세요.

- 입력한 이름은 신용/직불 카드에 인쇄된 이름과 정확히 일치해야 합니다. 추가적인 기호나 문자가 없어야 합니다.

- 입력한 주소는 신용/직불 카드 명세서에 표시되는 우편번호, 국가, 시/도, 구/군/시, 번지를 비롯한 주소와 정확히 일치해야 합니다.

- 입력한 CVV 코드는 신용/직불 카드 앞면 또는 뒷면에 표시된 코드와 정확히 일치해야 합니다.

결제 정보를 업데이트한 후 다시 등록을 시도할 수 있습니다.

## Azure 무료 평가판

Azure 무료 평가판 제품은 무료 평가판 활성화가 사용자당 한 번으로 제한됩니다. 이전에 이 신용 카드로 무료 평가판을 활성화했거나 과거에 이 신용 카드와 연결된 Azure 구독이 있었던 경우 시스템에서 무료 평가판 활성화를 허용하지 않습니다. 대신 [Microsoft Azure 제품 세부 정보](https://azure.microsoft.com/support/legal/offer-details/) 페이지에 나열되어 있는 기타 Azure 플랜 중 하나를 사용하세요.

## 다음 단계

### 청구서 설정

수표, 속달 수표(overnight checks), 전신 송금 같은 청구서 지불 방식을 통해 Azure 구독 비용을 지불할 수도 있습니다.

청구서 지불 옵션을 사용하려면, 사업체를 대표해야 합니다. 개인 구독자는 이 옵션을 사용할 수 없습니다. 사업체가 청구서 옵션을 사용하여 지불하도록 설정되면, 나중에 다른 지불 옵션으로 변경할 수 없습니다.

청구서를 설정하는 프로세스를 시작하려면 [Azure 청구 - 청구 방법](https://azure.microsoft.com/pricing/invoicing/)에서 자세한 내용을 확인하세요.

### 신용 카드 또는 직불 카드 정보 업데이트

카드 정보를 업데이트하거나 카드를 변경 또는 제거하려는 경우 [결제 정보를 관리](billing-how-to-change-credit-card.md)하는 방법에 대해 알아보세요.

> [AZURE.NOTE] 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

<!---HONumber=AcomDC_0914_2016-->