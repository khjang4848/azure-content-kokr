<properties 
	pageTitle="기계 학습 웹 서비스를 Azure 마켓플레이스에 게시 | Microsoft Azure" 
	description="Azure 마켓플레이스에 Azure 기계 학습 웹 서비스를 게시하는 방법" 
	services="machine-learning" 
	documentationCenter="" 
	authors="BharathS" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="bharaths"/>

# Azure 마켓플레이스에 Azure 기계 학습 웹 서비스 게시 

Azure 마켓플레이스에서는 외부 고객이 사용하도록 유료 또는 무료 서비스로 Azure 기계 학습 웹 서비스를 게시하는 기능을 제공합니다. 이 문서에서는 시작 지침에 대한 링크와 함께 프로세스의 개요를 제공합니다. 이 프로세스를 사용하여 다른 개발자가 응용 프로그램에서 사용할 수 있도록 웹 서비스를 제공할 수 있습니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 게시 프로세스 개요 

Azure 기계 학습 웹 서비스를 Azure 마켓플레이스에 게시하는 단계는 다음과 같습니다.

1. 기계 학습 요청-응답 서비스(RR)를 만들어서 게시합니다.
2. 서비스를 프로덕션 환경에 배포하고 API 키 및 OData 끝점 정보를 가져옵니다.
3. 게시된 웹 서비스의 URL을 사용하여 [Azure 마켓플레이스(데이터 마켓)](https://publish.windowsazure.com/workspace/)에 게시합니다.
4. 제출된 제품은 검토되고 고객이 구매를 시작하기 전에 승인되어야 합니다. 게시 프로세스는 영업일 기준 몇 일이 걸릴 수 있습니다.

## 방법 설명
###1단계: 기계 학습 요청-응답 서비스(RR)를 만들어서 게시###
 아직 게시하지 않은 분은 이 [방법 설명](machine-learning-walkthrough-5-publish-web-service.md)을 살펴보세요.

###2단계: 서비스를 프로덕션 환경에 배포하고 API 키 및 OData 끝점 정보 가져오기###
1. [Azure 클래식 포털](http://manage.windowsazure.com)에서,왼쪽에 있는 탐색 모음에서 **기계 학습** 옵션을 선택하고 작업 영역을 선택합니다.

2. **웹 서비스** 탭을 클릭하고 마켓플레이스에 게시할 웹 서비스를 선택합니다.

	![Azure 마켓플레이스][workspace]

3. 마켓플레이스에서 사용되기를 원하는 끝점을 선택합니다. 추가 끝점을 하나도 만들지 않은 경우 **기본** 끝점을 선택할 수 있습니다.

4. 끝점에서 클릭하면 **API 키**가 보일 것입니다. 3단계에서 이 정보가 필요하므로 키를 복사해 둡니다.

	![Azure 마켓플레이스][apikey]

5. **REQUEST/RESPONSE** 메서드를 클릭합니다. 현재는 마켓플레이스에 일괄 처리 실행 서비스를 게시할 수 없습니다. Request/Response 메서드에 대한 API 도움말 페이지로 이동될 것입니다.

6. **OData 끝점 주소**를 복사합니다. 3단계에서 이 정보가 필요합니다.

	![Azure 마켓플레이스][odata]




프로덕션 환경에 서비스를 배포합니다.



###3단계: 게시된 웹 서비스의 URL을 사용하여 Azure 마켓플레이스(데이터 마켓)에 게시###

1.  [Azure 마켓플레이스(데이터 마켓)](http://datamarket.azure.com/home)으로 이동합니다.
2.  페이지 상단의 **게시** 링크를 클릭합니다. [Microsoft Azure 게시 포털](https://publish.windowsazure.com)로 이동될 것입니다.
3.  **게시자** 섹션을 클릭하여 게시자로 등록합니다.
4.	새 제품을 만들 때 **데이터 서비스**를 선택하고 **새 데이터 서비스 만들기**를 클릭합니다.
 
	![Azure 마켓플레이스][image1]

	<br />


5.	**요금제** 아래에서 가격 요금제를 포함하여 제품에 대한 정보를 입력합니다. 무료 또는 유료 서비스를 제공할지 결정합니다. 유료로 제공하려면 은행 및 세금 정보와 같은 결제 정보를 입력해야 합니다.

6.	**마케팅** 아래에서 제목, 제품에 대한 설명 등 제품 정보를 입력합니다.

7.	**요금제** 아래에서 특정 국가에 대한 요금제 가격을 설정하거나 시스템에서 제품 가격을 "자동 설정"하도록 합니다.

8. **데이터 서비스** 탭에서 **데이터 원본**으로 **웹 서비스**를 클릭합니다.

	![Azure 마켓플레이스][image2]

9.	위의 2단계에서 설명한 대로 Azure 클래식 포털에서 웹 서비스 URL 및 API 키를 가져옵니다.

10.	마켓플레이스 데이터 서비스 설정 대화 상자에서 OData 끝점 주소를 **서비스 URL** 텍스트 상자에 붙여넣습니다.

11. **인증**에 대해 **헤더**를 **인증 체계**로 선택합니다.

	- **헤더 이름**.으로 "권한 부여"를 입력합니다.
	- **헤더 값**에 대해 "Bearer"(따옴표 제외)를 입력하고, **스페이스 바**를 클릭한 다음 API 키를 붙여넣습니다.
	- **This Service is OData(이 서비스는 OData입니다.)** 확인란을 선택합니다.
	- **연결 테스트**를 클릭하여 연결을 테스트합니다.

12.	**범주** 아래에서 **기계 학습**을 선택합니다.

13. 제품에 대한 모든 메타데이터를 입력했으면 **게시**를 클릭한 다음 **스테이징 환경으로 푸시**를 클릭합니다. 이 시점에서 수정이 필요한 나머지 문제에 대한 알림이 제공될 것입니다.

14. 모든 미해결 문제를 해결한 후 **프로덕션 환경으로 푸시하도록 승인 요청**을 클릭합니다. 게시 프로세스는 영업일 기준 몇 일이 걸릴 수 있습니다.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=AcomDC_0914_2016-->