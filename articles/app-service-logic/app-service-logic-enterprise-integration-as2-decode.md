<properties 
	pageTitle="엔터프라이즈 통합 팩 AS2 메시지 디코딩 커넥터에 대해 알아보기 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 파트너를 사용하는 방법 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# AS2 메시지 디코딩 시작

메시지를 전송하는 동안 AS2 메시지 디코딩에 연결하여 보안 및 안정성을 설정합니다. MDN(메시지 처리 알림)을 통해 디지털 서명, 암호 해독 및 승인을 제공합니다.

## 연결 만들기

### 필수 조건

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.

* 통합 계정은 AS2 메시지 디코딩 커넥터를 사용해야 합니다. [통합 계정](./app-service-logic-enterprise-integration-create-integration-account.md), [파트너](./app-service-logic-enterprise-integration-partners.md) 및 [AS2 규약](./app-service-logic-enterprise-integration-as2.md)을 만드는 방법에 대한 세부 사항을 확인합니다.

### 다음 단계를 사용하여 AS2 메시지 디코딩에 연결합니다.

1. [논리 앱 만들기](./app-service-logic-create-a-logic-app.md)에서는 예제를 제공합니다.

2. 이 연결에는 트리거가 필요하지 않습니다. 다른 트리거를 사용하여 요청 트리거와 같은 논리 앱을 시작합니다. 논리 앱 디자이너에서 트리거를 추가하고 작업을 추가합니다. 드롭다운 목록에서 Microsoft 관리되는 API 표시를 선택하고 검색 상자에 "AS2"를 입력합니다. AS2 – AS2 메시지 디코딩 선택

	![AS2 검색](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. 이전에 통합 계정에 대한 연결을 만들지 않은 경우 연결 세부 정보를 지정하라는 메시지가 표시됩니다.

	![통합 연결 만들기](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. 통합 계정 세부 정보를 입력합니다. 별표가 있는 속성은 필수 사항입니다.

	| 속성 | 세부 정보 |
	| --------   | ------- |
	| 연결 이름 * | 연결의 이름을 입력합니다. |
	| 통합 계정 * | 통합 계정 이름을 입력합니다. 통합 계정 및 논리 앱은 동일한 Azure 위치에 있어야 합니다. |

  	완료되면 연결 정보가 다음과 비슷하게 표시됩니다.

  	![통합 연결](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. **만들기**를 선택합니다.
	
6. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다.

	![통합 연결 생성](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png)

7. 요청 출력에서 본문 및 헤더를 선택합니다.

	![필수 필드 제공](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png)

## AS2 디코딩은 다음 작업을 수행합니다.

* AS2/HTTP 헤더 처리
* 서명 확인(구성된 경우)
* 메시지 암호 해독(구성된 경우)
* 메시지 압축 해제(구성된 경우)
* 원본 아웃바운드 메시지와 함께 수신된 MDN 조정
* 부인 방지 데이터베이스에서 레코드 업데이트 및 연결
* AS2 상태 보고에 대한 레코드 작성
* 출력 페이로드 콘텐츠는 base64로 인코딩됨
* MDN이 필요한지 여부 및 MDN을 AS2 규약의 구성에 따라 동기화해야 하는지 또는 비동기화해야 하는지 결정
* (규약 구성에 따라)동기 또는 비동기 MDN 생성
* MDN에 대한 상관 관계 토큰과 속성 설정

##직접 체험해 보기

체험해 보세요. [여기](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 클릭하여 논리 앱 AS2 기능을 사용하여 완벽하게 작동하는 고유한 논리 앱을 배포합니다.

## 다음 단계

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")

<!---HONumber=AcomDC_0914_2016-->