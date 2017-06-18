<properties 
	pageTitle="엔터프라이즈 통합 팩에서 B2B 솔루션 만들기 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩의 B2B 기능을 사용하여 데이터를 수신하는 방법에 대해 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 엔터프라이즈 통합 팩의 B2B 기능을 사용하여 데이터를 수신하는 방법에 대해 알아보기#

## 개요 ##

이 문서는 논리 앱 엔터프라이즈 통합 팩의 일부입니다. 개요를 확인하여 [엔터프라이즈 통합 팩의 기능](./app-service-logic-enterprise-integration-overview.md)에 대해 자세히 알아봅니다.

## 필수 조건 ##

AS2 및 X12 작업을 사용하려면 엔터프라이즈 통합 계정이 필요합니다

[엔터프라이즈 통합 계정을 만드는 방법](./app-service-logic-enterprise-integration-accounts.md)

## 논리 앱 B2B 커넥터를 사용하는 방법 ##

통합 계정을 만들고 파트너 및 이에 대한 규약을 추가하면 B2B(기업 간) 워크플로를 구현하는 논리 앱을 만들 준비가 완료됩니다.

이 설명 내용에서 AS2 및 X12 작업을 사용하여 거래 파트너의 데이터를 받는 비즈니스 논리 앱에 비즈니스를 만드는 방법을 확인합니다.

1. 새 논리 앱을 만들고 [통합 계정에 연결](./app-service-logic-enterprise-integration-accounts.md)합니다.
2. **요청 - HTTP 요청을 받은 경우** 트리거를 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)
3. 먼저 **작업 추가**를 선택하여 **AS2 디코딩** 작업을 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)
4. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 **as2**라는 단어를 입력합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)
6. **AS2 - AS2 메시지 디코딩** 작업을 선택합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)
7. 표시된 대로 입력으로 사용할 **본문**을 추가합니다. 이 예제에서는 논리 앱을 트리거한 HTTP 요청의 본문을 선택합니다. 또는 식을 입력하여 **헤더** 필드에 헤더를 입력할 수 있습니다.

    @triggerOutputs()['headers']

8. AS2에 필요한 **헤더**가 추가됩니다. HTTP 요청 헤더에 위치합니다. 이 예제에서는 논리 앱을 트리거한 HTTP 헤더의 본문을 선택합니다.
9. 이제 **작업 추가**를 다시 선택하여 X12 메시지 디코딩 작업을 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)
10. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 **x12**라는 단어를 입력합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)
11. **X12 - X12 메시지 디코딩** 작업을 선택하여 논리 앱에 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)
12. 이제 위의 AS2 작업의 출력인 이 작업에 대한 입력을 지정해야 합니다. 실제 메시지 내용은 JSON 개체에 있으며 base64로 인코딩됩니다. 따라서 식을 입력으로 지정해야 하기 때문에 다음 식을 **디코딩할 X12 플랫 파일 메시지** 입력 필드에 입력합니다.

    @base64ToString(body('Decode\_AS2\_message')?['AS2Message']?['Content'])

13. 이 단계는 거래 파트너로부터 수신한 X12 데이터를 디코딩하고 JSON 개체에 항목의 수를 출력합니다. 데이터의 수신을 파트너에게 알리기 위해 HTTP 응답 작업에서 AS2 MDN(메시지 처리 알림)을 포함하는 응답을 다시 보낼 수 있습니다.
14. **작업 추가**를 선택하여 **응답** 작업을 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)
15. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 **응답**이라는 단어를 입력합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)
16. **응답** 작업을 선택하여 추가합니다. ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)
17. **X12 메시지 디코딩** 작업의 출력에서 MDN 액세스하려면 다음 식을 사용하여 응답 **본문** 필드를 설정합니다.

    @base64ToString(body('Decode\_AS2\_message')?['OutgoingMdn']?['Content'])

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)
18. 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)

이 시점에서 B2B 논리 앱의 설정이 완료됩니다. 실제 응용 프로그램에서는 LOB 응용 프로그램 또는 데이터 저장소에 디코딩한 X12 데이터를 저장할 수 있습니다. 작업을 더 추가하여 이를 수행하거나 사용자 지정 API를 작성하여 고유한 LOB 응용 프로그램에 연결하고 논리 앱에서 이러한 API를 사용할 수 있습니다.

## 기능 및 사용 사례 ##

- AS2 및 X12 디코딩 및 인코딩 작업을 사용하면 논리 앱을 사용하는 산업 표준 프로토콜을 사용하여 거래 파트너에게서 데이터를 받고 파트너에게 데이터를 보낼 수 있습니다.
- AS2 및 X12를 함께 또는 개별로 사용하여 필요에 따라 거래 파트너와 데이터를 교환할 수 있습니다.
- B2B 작업을 사용하면 통합 계정에서 파트너 및 규약을 쉽게 만들고 논리 앱에서 사용할 수 있습니다.
- 다른 작업에서 논리 앱을 확장하여 다른 응용 프로그램 및 SalesForce와 같은 서비스 간에 데이터를 보내고 받을 수 있습니다.

## 자세한 정보 ##

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0914_2016-->