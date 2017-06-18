<properties
   pageTitle="청구서 이해하기 | Microsoft Azure"
   description="Azure 구독에 대한 사용량 및 청구를 읽고 이해하는 방법에 대해 알아봅니다."
   services=""
   documentationCenter=""
   authors="erihur"
   manager="stevenpo"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="erihur;genli"/>


# Microsoft Azure 청구서 이해

> [AZURE.NOTE] 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

Microsoft Azure 구독 요금은 요금제에 따라 다릅니다. Visual Studio Enterprise(MPN) 구독자와 같은 일부 요금제에는 필요에 따라 Azure 서비스에서 사용할 수 있는 월별 크레딧이 포함되어 있습니다.

이전 청구 기간의 최대 24시간까지 잠재 사용량에 대한 비용은 현재 청구 기간에 보고될 수 있습니다.

소비와 요금 계획에 관한 자세한 내용은 [Microsoft Azure 구입 옵션 페이지](https://azure.microsoft.com/pricing/purchase-options/)를 참조하세요.

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### Microsoft Azure 청구서 보기 또는 다운로드

1. Microsoft 계정 또는 조직 ID를 사용하여 [계정 센터](https://account.windowsazure.com/subscriptions)에 로그인합니다.

2. 세부 정보 및 사용 현황을 확인할 구독을 클릭합니다.

3. **청구 내역**을 클릭합니다.

    ![요약 - 청구 내역 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)

4. **청구 내역** 섹션에는 지난 청구 기간과 현재 미청구 기간에 대한 명세서가 나열됩니다. 현재 기간에 대한 명세서는 예상 요금이 생성된 날짜의 예상 요금입니다. 이 정보는 매일 업데이트되며 지금까지 발생한 모든 사용 내역을 포함하지 않을 수 있습니다. 월별 청구서는 이 예상과 다를 수 있습니다.

    ![요약 - 청구 내역 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. **현재 명세서 보기**를 클릭하여 예상 요금이 생성된 날짜의 예상 요금을 확인합니다. 이 정보는 매일 업데이트되며 지금까지 발생한 모든 사용 내역을 포함하지 않을 수 있습니다. 월별 청구서는 이 예상과 다를 수 있습니다.

    ![요약 - 청구 내역 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![요약 - 청구 내역 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. **청구서 다운로드**를 클릭하여 이전 청구서의 복사본을 확인합니다.

    ![요약 - 청구 내역 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


> [AZURE.NOTE] 전 세계 고객용 청구 명세서에 나열된 요금은 은행이 전환율에 대해 서로 다른 비용을 부과하므로 예측 용도로만 사용합니다.

다음은 Microsoft Azure에서 사용 가능한 두 개의 제안에 대한 일부 샘플 문입니다.

 제품 유형 | 설명 | 다운로드 |
 :--------- |:-------- | :-------|
Pay-As-You-Go | 후불 월정액 | [샘플 파일](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
약정 제안 | 선불 약정에서 공제된 지출 | [샘플 파일](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)

## 계정 정보

계정 정보 섹션은 사용 현황 및 프로필과 관련된 정보를 식별합니다.

![머리글](./media/billing-understand-your-bill/Header.png)

| 용어 | 설명 |
|---------------------|-----------------------------------------------------------------------------------------------------|
| 청구서 번호 | 추적을 위한 고유한 청구서 식별자입니다. |
| 대금 청구 주기 | 사용 현황이 발생한 시간 프레임 |
| 청구서 날짜 | 청구서가 생성된 날짜 |
| 결제 방법 | 계정에 사용되는 지불 유형(청구서 또는 신용 카드) |
| 청구지 | Microsoft Azure 지불 주소 |
| 구독 제품 | 구매한 구독 제품의 유형(종량제, BizSpark Plus, Azure Pass 등) |
| 계정 소유자 메일 | Microsoft Azure 계정이 등록된 계정 전자 메일 주소 |

## 청구서 요약 이해

청구서의 **청구서 요약** 섹션은 마지막 청구 이후의 트랜잭션 및 현재 사용 요금을 요약합니다.

![청구서 요약](./media/billing-understand-your-bill/InvoiceSummary.png)

청구서의 이전 잔액, 지불 및 미지불 잔액 섹션에는 마지막 청구 이후의 트랜잭션이 요약됩니다.

| 용어 | 설명 |
|---------------------------------------------------|------------------------------------------------------------------------------------------|
| 이전 잔액 | 마지막 청구에서 지불할 총 금액 |
| 지불 | 마지막 청구에 적용되는 총 지불액 |
| 미결제 잔액(이전 청구 주기) | 마지막 청구 이후 계정에 적용되는 모든 청구 조정액(크레딧 또는 잔액) |

## 현재 요금 이해
청구서의 현재 요금 섹션에는 월별 요금에 대한 세부 정보가 포함됩니다. 링크는 다음 하위 섹션으로 구성됩니다.

| 용어 | 설명 |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 사용 요금 | 사용 요금은 구독에 대한 총 월별 요금입니다. 지난 달의 사용 요금이 후불로 청구됩니다. |
| 할인 | 현재 청구에 적용되는 서비스 할인은 이 품목에서 반영됩니다. |
| 조정 | 기타 조정은 현재 청구서에 적용되는 기타 크레딧 또는 미결제 요금입니다. 예를 들어 Visual Studio Enterprise with MSDN 제품을 사용하는 경우 이 품목에 월별 크레딧이 표시됩니다. 구독을 취소한 경우에는 현재 청구 기간부터 구독 취소일까지 제품에 포함된 월별 크레딧을 초과하는 월별 사용량에 대한 요금이 표시됩니다.|

## 바닥글 정보
![바닥글](./media/billing-understand-your-bill/footerinformation.png)

## 추가 정보 이해
추가 정보 페이지에서는 청구서를 이해하는 데 도움이 되는 기타 리소스 및 사용 현황과 청구서에 대한 기타 관련 정보를 볼 수 있는 링크를 제공합니다.

![추가 정보](./media/billing-understand-your-bill/AdditionalInformation.png)

### 자세한 사용 현황
**자세한 사용 현황** 아래의 설명에 있는 링크를 클릭하면 이 구독에 대한 세부 사용 현황을 볼 수 있는 계정 센터로 이동합니다. 이제 두 버전을 다운로드할 수 있습니다: **.csv 버전 1**은 이전 명명 규칙 및 사용 현황 필드를 포함하고 **.csv 버전 2**는 Microsoft Azure에서 사용하는 서비스를 이해할 수 있도록 돕는 각 범주를 더한 추가 필드에 고객 친화적인 이름을 포함합니다. .csv 버전 1에는 Azure Resource Manager 세부 정보가 없습니다. Azure Resource Manager 정보는 .csv 버전 2에서 확인할 수 있습니다.

### 추가 정보 및 유용한 리소스
이 섹션에는 계산 인스턴스 크기 및 SQL DB 요금에 대해 간단히 질문할 수 있는 링크와 추가 질문에 대답하는 데 유용한 링크가 있습니다.

| 용어 | 설명 |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| 구매처 | 계정의 프로필 주소로 미리 채워집니다. |
| 지불 관련 지침 | 이 섹션에서는 지불 방법이 청구서인 경우 수표, 전신 송금 또는 심야 수표를 보낼 곳에 대한 지불 관련 지침을 제공합니다. |

## 세부 사용 요금 이해

Azure 사용을 쉽게 관리하도록 고객을 지원하는 지속적인 노력의 일환으로 Azure 서비스 사용 및 비용을 보고하는 다운로드 사용 현황 파일을 개선했습니다. 다운로드 링크는 사용 파일의 두 가지 버전을 포함합니다.

- **버전 1**은 기존 형식을 사용합니다.

- **버전 2**는 추가 정보를 포함하고 일일 사용량 섹션에서 열 이름을 업데이트합니다.

사용 요금은 구독에 대한 총 **월별** 요금에서 크레딧 또는 할인을 제한 금액입니다. 지난 달의 사용 요금이 후불로 청구됩니다. 파일의 위쪽 섹션에서 이전 월 청구 주기 중에 청구된 서비스의 세부 정보를 표시합니다. 이전 테이블에서 각 .csv 버전 파일에 대한 열의 이름을 나열합니다.

버전 1 | 버전 2 | 설명|
:---------------| :---------------- | --------|
청구 기간 | 청구 기간 | 리소스가 사용된 청구 기간입니다.
이름 | 측정기 범주 | 이 사용 현황이 속한 최상위 서비스를 식별합니다.
형식 | 측정기 하위 범주 | 이 열에서 Azure 서비스를 유형별로 추가 정의할 수 있으며, 이는 요금에 영향을 줄 수 있습니다.
리소스 | 측정기 이름 | 사용 중인 리소스에 대한 측정 단위를 식별합니다.
지역 | 측정기 영역 | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.
SKU | SKU | 각 Azure 리소스에 대한 고유한 시스템 식별자를 식별합니다.
단위 | 단위 | 서비스 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다.
사용 | 소비된 수량 | 청구 기간 동안 소비된 리소스의 양을 포함합니다.
포함됨 | 표함된 수량 | 현재 청구 기간 동안 무료로 제공되는 리소스의 양을 포함합니다.
청구 가능 | 초과 수량 | 사용량이 포함된 양을 초과하는 경우 이 열에 그 차이가 표시됩니다. 이 양에 대한 요금이 청구됩니다. 제품에 포함된 양이 없는 종량제 제품의 경우 이 합계는 사용량과 동일합니다.
약정 기간 내 | 약정 기간 내 | 6개월 또는 12개월 제품과 관련된 약정 금액에서 차감되는 리소스 요금을 포함합니다. 리소스 요금은 시간 순서대로 약정 금액에서 차감됩니다.
통화 | 통화 | 현재 청구 기간에 반영되는 통화를 식별합니다.
초과분 | 초과분 | 6개월 또는 12개월 제품과 관련된 약정 금액을 초과하는 리소스 요금을 포함합니다.
약정 요율 | 약정 요율 | 6개월 또는 12개월 제품과 관련된 총 약정 금액을 기반으로 하는 약정 요율을 포함합니다.
비용 | 비용 | 청구 가능한 단위별로 청구되는 요금입니다.
값 | 값 | 청구 가능 열과 요금 열을 곱한 결과를 표시합니다. 사용량이 포함된 양을 초과하는 경우에는 이 열에 청구 금액이 없습니다.

## 일일 사용 현황 데이터 분석
사용량에 따라 수천 개의 일일 사용 데이터 행이 있을 수 있습니다. 이 데이터를 분석하려는 경우 **사용량 다운로드**를 클릭하고 쉼표로 구분된 변수 파일(.csv) 버전을 선택하여 적절한 청구 기간에 대한 일일 사용량 데이터를 참조합니다. 참조용으로 아래 각 버전의 샘플 .csv 파일을 다운로드할 수 있습니다.

 이름 | 다운로드 |
 :----------:| :-------: |
 자세한 사용 현황 .csv 버전 1| [샘플 파일](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
 자세한 사용 현황 .csv 버전 2 | [샘플 파일](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



.csv 파일에는 현재 청구 기간 내에 사용된 각 리소스의 양의 목록을 표시하도록 항목이 세분화되어 있습니다.

![csv 스냅숏](./media/billing-understand-your-bill/csvsnapshotportal.png)

다음 열은 청구 기간이 시작될 때 요금에 영향을 주는 세부 정보를 표시합니다.

버전 1 | 버전 2 | 설명 |
:---------------| :----------------| -----|
사용 날짜 | 사용 날짜 | 리소스를 내보낸 날짜입니다.
이름 | 측정기 범주 | 이 사용 현황이 속한 최상위 서비스를 식별합니다.
리소스 GUID | 측정기 ID | 청구된 측정기 식별자입니다. 가격 결재에 사용되는 식별자로 사용됩니다.
형식 | 측정기 하위 범주 | 이 열에서 Azure 서비스를 유형별로 추가 정의할 수 있으며, 이는 요금에 영향을 줄 수 있습니다.
리소스 | 측정기 이름 | 사용 중인 리소스에 대한 측정 단위를 식별합니다.
지역 | 측정기 영역 | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.
단위 | 단위 | 서비스 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다.
사용 | 소비된 수량 | 해당 날짜에 사용된 리소스의 양을 포함합니다.
하위 지역 | 리소스 위치 | 리소스가 실행되고 있는 데이터 센터를 식별합니다.
부여 | 사용되는 서비스 | 이 열은 이름 열에서 특별히 식별할 수 없는 개별 Azure 플랫폼 서비스를 추적하는 데 사용됩니다. 이 서비스 열은 사용 현황이 속한 특정 서비스를 나타냅니다.
해당 없음 | 리소스 그룹 | _**새 열을 추가합니다.**_ 배포된 리소스가 실행되는 리소스 그룹입니다. [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.
구성 요소 | 인스턴스 ID | 실행 중인 리소스에 대한 식별자입니다. 식별자를 만들 때 리소스에 대해 지정한 이름을 포함합니다.
해당 없음 | 태그 | _**새 열을 추가합니다.**_ Azure에서 새 리소스 유형을 사용하면 리소스를 태그할 수 있습니다. [태그를 사용하여 Azure 리소스 구성](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요.
추가 정보 | 추가 정보 | 서비스와 관련된 추가적 메타데이터입니다.
서비스 정보 1 | 서비스 정보 1 | 이 열은 구독에서 서비스가 속한 프로젝트 이름을 제공합니다.
서비스 정보 2 | 서비스 정보 2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다.

일부 새 필드 및 csv 버전 2로 이름 변경 외에도 아래 필드에 데이터에 대한 표준화된 형식이 있을 것입니다.

- **인스턴스 ID**: 인스턴스 ID 필드가 프로비전된 서비스에 대한 사용자 지정 식별자를 나타냅니다. 현재 리소스의 이름 또는 정규화된 리소스 ID인 인스턴스 ID가 표시되는 두 형식이 있습니다. Microsoft Azure 서비스는 표준화되고 정규화된 리소스 ID 형식으로 인스턴스 ID가 나타나도록 전환하고 있습니다. _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. 서비스가 새 형식으로 전환하면 인스턴스 ID 데이터 필드가 단순한 리소스 이름에서 리소스 ID로 변경됨을 볼 수 있습니다. 리소스 ID는 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx)가 사용하는 형식으로 구독에서 리소스를 식별합니다.

![InstanceId](./media/billing-understand-your-bill/instanceid.png)

- **추가 정보**: 사용량.csv에서 추가 정보 열은 서비스 특정 메타데이터를 지정합니다. 예를 들어 VM용 이미지 형식입니다. 현재 서비스는 추가 정보, 서비스 Info1 및 서비스 정보 2 필드 등 여러 열에서 서비스 특정 메타데이터를 내보냅니다. Microsoft Azure 서비스는 추가 정보 열에서만 서비스 특정 메타데이터를 내보내며 표준화될 것입니다. 표준화된 형식의 스냅숏을 아래에서 참조하십시오.

![additionalinfo\_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **태그**:이 열은 사용자 특정 리소스 태그를 포함합니다. 청구 레코드를 그룹화하는 데 태그를 사용할 수 있습니다. 예를 들어 서비스를 사용하는 부서는 비용을 배분하는데 태그를 사용할 수 있습니다. [태그를 사용하여 Azure 리소스 구성](./resource-group-using-tags.md)에 대한 자세한 내용을 알아봅니다. 태그를 내보내도록 지원하는 서비스는 다음과 같습니다.

    - 가상 컴퓨터

    - 저장소 및

    - [Azure 리소스 관리자 API](https://msdn.microsoft.com/library/azure/dn790567.aspx)를 사용하여 프로비전된 네트워킹 서비스

![tags](./media/billing-understand-your-bill/tags.png)


## 다음 단계

- [청구 경고 설정](billing-set-up-alerts.md)

- [지불 방법 관리](billing-how-to-change-credit-card.md)

- [Azure 마켓플레이스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)

- [Azure 청구 및 구독 FAQ](billing-subscription-faq.md)

> [AZURE.NOTE] 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->

<!---HONumber=AcomDC_0914_2016-->