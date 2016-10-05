<properties
   pageTitle="Azure 마켓플레이스 지급 보고 이해 | Microsoft Azure"
   description="Azure 마켓플레이스 지급 보고서를 검토하고 수집하는 방법을 알아봅니다."
   services="marketplace-publishing"
   documentationCenter="na"
   authors="v-jeana"
   manager="lakoch"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="v-jeana; hascipio; v-dabosl"/>

# Azure 마켓플레이스 지급 보고서 이해

## 지급 보고서 액세스 및 보기

개발자 센터로 전환함에 따라 일부 지급 보고서는 https://dev.windows.com/ko-KR의 개발자 센터에서 제공되지만 일부는 https://publish.windowsazure.com의 게시 포털에서 확인할 수 있습니다.

지급 보고는 최신 지급과 관련된 모든 마켓플레이스 제품에 대한 **개발자 센터**에서 제공되며 현재 다음을 포함합니다.
- VM
- B+C 제품
- EA에서 제공되는 데이터 및 개발자 서비스

다음에 대한 지급 보고는 **게시 포털**에 계속 제공됩니다.
- Direct 웹에 따라 제공되는 데이터 및 개발자 서비스(계속 레거시 지불 시스템 사용).

보고서는 분기가 끝난 후 45일이 지나면 사용할 수 있으며 모든 환불이 이루어진 후에 계산됩니다.

### 개발자 센터에서 지급액 보고서 액세스

1. 개발자 센터(https://dev.windows.com/ko-KR)로 이동합니다.
2. **Dashboard**를 클릭합니다.

    ![LandingPageDashboardHighlight][1]

3. **지급액 요약**을 클릭합니다.

    ![DashboardPayoutSummary][2]


## 개발자 센터에서 지급 보고서 확인

각 분기에 대한 지급 보고서는 해당 분기 내에 발생한 모든 트랜잭션을 기록합니다.

- 예약된 금액은 예정된 결제 주기를 벗어나 발생하는 모든 지급을 나타냅니다(예: 이 금액은 다음 달 다음 결제로 이월됨). 이 금액은 일반적으로 $0(고객이 사전에 제대로 지불하지 않은 경우)입니다.
- 해당 지급에 대한 정보를 확인하려면 예정된 결제 또는 최근 결제 **세부 정보 보기** 링크를 클릭합니다.
- 앱/제품별 수익 세부 정보를 보려면 **지불 명세서**를 클릭합니다.
- 개별 명세서를 보려면 **보기** 링크를 클릭합니다.

    ![PayoutSummaryUpcomingMostRecentLinksStatement][3]

- 여러 앱/제품을 보려면 개별 명세서 하단에 있는 **수익 분석** 필터를 사용합니다(있는 경우).

    ![PayoutSummaryPaymentStatementsFilterControl][4]



## 게시 포털에서 지급 보고서 보기
각 분기에 대한 지급 보고서는 해당 분기 내에 발생한 모든 트랜잭션을 기록합니다.

1. 게시 포털(https://publish.windowsazure.com)로 이동합니다.
2. **게시자** 섹션에서 **지급 보고서**를 클릭합니다.
3. 드롭다운을 클릭하면 사용할 수 있는 모든 분기별 지급 보고서가 표시됩니다.

    ![accessingpayoutreport][5]


### 지급 보고서 읽기

각 분기에 대한 지급 보고서는 해당 분기 내에 발생한 모든 트랜잭션을 기록합니다.

- 특정 분기와 관련된 원장 항목을 찾으려면 드롭다운에서 해당 분기에 대한 지급 보고서를 선택합니다. 예를 들어 2015년 4~6월에 대한 원장 항목을 보려면 드롭다운 메뉴에서 해당 날짜 범위를 선택합니다.
- 특정 분기와 관련된 지급 정보를 찾으려면 드롭다운에서 후속 분기에 대한 지급 보고서를 선택합니다. 예를 들어 2015년 4~6월에 대한 지급을 보려는 경우 2015년 7~9월에 대한 후속 지급 보고서에 해당 금액이 표시됩니다. ![readingpayoutreport][6]

- 재무 요약 패널은 잔액, 대변과 차변을 범주별로 보여 줍니다.
- 원장 항목은 개별 트랜잭션을 표시합니다.

## 정의

**재무 요약 패널:**

![financialdefinitions][7]

**원장 항목:**

![ledgerdefinitions][8]

## 지급 질문

지급에 관한 질문이 있는 경우 지원 팀에 문의하세요.

![payoutquestions][9]

1. 지원 페이지로 이동합니다.
2. **지급액**을 선택합니다.
3. **지급액 관련 문의**를 선택합니다.
4. **요청 시작**을 클릭합니다.

## 다음 단계

기타 지원 쿼리는 <https://portal.azure.com>에서 문제를 기록하세요.

[1]: ./media/marketplace-publishing-report-payout/LandingPage-DashboardHighlight.png
[2]: ./media/marketplace-publishing-report-payout/Dashboard-PayoutSummary.png
[3]: ./media/marketplace-publishing-report-payout/PayoutSummary-UpcomingOrMostRecentPaymentLinksSingleStatementLink.png
[4]: ./media/marketplace-publishing-report-payout/PayoutSummary-PaymentStatements-SingleStatement-FilterControl.png
[5]: ./media/marketplace-publishing-report-payout/accessingpayoutreport.png
[6]: ./media/marketplace-publishing-report-payout/readingpayoutreport.png
[7]: ./media/marketplace-publishing-report-payout/financialdefinitions.png
[8]: ./media/marketplace-publishing-report-payout/ledgerdefinitions.png
[9]: ./media/marketplace-publishing-report-payout/payoutquestions.png

<!---HONumber=AcomDC_0921_2016-->