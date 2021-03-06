<properties
   pageTitle="JavaScript API를 사용하여 보고서와 상호 작용 | Microsoft Azure"
   description="Power BI Embedded, JavaScript API를 사용하여 보고서와 상호 작용"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="08/26/2016"
   ms.author="mblythe"/>

# JavaScript API를 사용하여 Power BI 보고서와 상호 작용

Power BI JavaScript API를 사용하면 응용 프로그램에 Power BI 보고서를 쉽게 포함할 수 있습니다. 응용 프로그램에서 API를 사용하여 프로그래밍 방식으로 페이지 및 필터와 같은 다른 보고서 요소와 상호 작용할 수 있습니다. 이러한 상호 작용을 통해 Power BI 보고서를 응용 프로그램의 통합된 일부로 만들 수 있습니다.

응용 프로그램의 일부로 호스팅되는 iframe을 사용하여 응용 프로그램에 Power BI 보고서를 포함합니다. iframe은 다음 이미지에 표시된 대로 응용 프로그램 및 보고서 간에 경계의 역할을 합니다.

![Javascript API가 포함되지 않은 Power BI Embedded iframe](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

iframe을 통해 포함 프로세스가 훨씬 쉬워지지만 JavaScript API 없이 보고서와 응용 프로그램은 서로 상호 작용할 수 없습니다. 상호 작용의 부재로 인해 보고서가 실제로 응용 프로그램의 일부가 아니라고 느낄 수 있습니다. 보고서와 응용 프로그램은 실제로 다음 이미지와 같이 서로 통신해야 합니다.

![Javascript API가 포함된 Power BI Embedded iframe](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

Power BI JavaScript API를 사용하면 안전하게 iframe 경계를 통과할 수 있는 코드를 작성할 수 있습니다. 이를 통해 응용 프로그램은 프로그래밍 방식으로 보고서에서 작업을 수행하고 보고서 내에서 사용자가 만든 작업에서 이벤트를 수신 대기할 수 있습니다.

## Power BI JavaScript API를 사용하여 할 수 있는 작업은 무엇인가요?
JavaScript API를 통해 보고서를 관리하고 보고서의 페이지를 탐색하고 보고서를 필터링하고 포함 이벤트를 처리할 수 있습니다. 다음 다이어그램은 API의 구조를 보여 줍니다.

![Power BI JavaScript API 다이어그램](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### 보고서 관리
Javascript API를 통해 보고서 및 페이지 수준에서 동작을 관리할 수 있습니다.

- 응용 프로그램에 특정 Power BI 보고서를 안전하게 포함 - [데모 응용 프로그램 포함](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)을 사용해 보세요.
  - 액세스 토큰 설정
- 보고서 구성
  - 필터 창 및 페이지 탐색 창 사용 및 사용 안 함 - [설정 데모 응용 프로그램 업데이트](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)를 사용해 보세요.
  - 페이지 및 필터에 대한 기본값 설정 - [기본값 데모 설정](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)을 사용해 보세요.
- 전체 화면 모드 시작 및 종료

[포함 보고서에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### 보고서의 페이지로 이동
JavaScript API를 통해 보고서의 모든 페이지를 검색하고 현재 페이지를 설정할 수 있습니다. [탐색 데모 응용 프로그램](http://azure-samples.github.io/powerbi-angular-client/#/scenario3)을 사용해 보세요.

[페이지 탐색에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### 보고서 필터링
JavaScript API는 포함된 보고서 및 보고서 페이지에 대한 기본 및 고급 필터링 기능을 제공합니다. [데모 응용 프로그램 필터링](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)을 사용해 보고 여기에서 소개 코드를 검토합니다.


#### 기본 필터
기본 필터는 열 또는 계층 수준에 배치되어 포함하거나 제외할 값의 목록을 포함합니다.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### 고급 필터
고급 필터는 논리 연산자 AND 또는 OR을 사용하고 고유한 해당 연산자 및 값과 함께 각각 하나 또는 두 개의 조건을 수용합니다. 지원되는 조건은 다음과 같습니다.

- 없음
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- 포함
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Is
- IsNot
- IsBlank
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[필터링에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### 이벤트 처리
응용 프로그램은 iframe에 정보를 보낼 뿐만 아니라 iframe에서 가져온 다음 이벤트에 대한 정보를 받을 수 있습니다.

- Embed
  - loaded
  - error
- 보고서
  - pageChanged
  - dataSelected(출시 예정)

[이벤트를 처리하는 방법에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## 다음 단계
Power BI JavaScript API에 대한 자세한 내용은 다음 링크를 확인하세요.

- [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [개체 모델 참조](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- 샘플
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [라이브 데모](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!---HONumber=AcomDC_0907_2016-->