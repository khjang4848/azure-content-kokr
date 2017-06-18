<properties
	pageTitle="기계 학습 웹 서비스용 Excel 추가 기능 | Microsoft Azure"
	description="코드를 작성하지 않고 Excel에서 직접 Azure 기계 학습 웹 서비스를 사용하는 방법입니다."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="jhubbard"
	editor="cgronlun"
    tags=""/>

<tags
	ms.service="machine-learning"
    ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/06/2016"
	ms.author="tedway;garye" />

# Azure 기계 학습 웹 서비스용 Excel 추가 기능

Excel을 사용하면 코드를 작성할 필요 없이 쉽게 직접 웹 서비스를 호출할 수 있습니다.

## 통합 문서에서 기존 웹 서비스를 사용하는 단계

1. 타이타닉호의 승객에 대한 데이터와 Excel 추가 기능이 들어 있는 [샘플 Excel 파일](http://aka.ms/amlexcel-sample-2)을 엽니다.
2. 웹 서비스를 클릭하여 선택합니다(이 예제의 경우 "Titanic Survivor Predictor (Excel Add-in Sample) [Score]").

    ![웹 서비스 선택][01]

3. 이렇게 하면 **Predict** 섹션으로 이동합니다. 이 통합 문서에는 이미 샘플 데이터가 포함되어 있지만 통합 문서가 비어 있는 경우에는 Excel에서 셀 하나를 선택하고 **샘플 데이터 사용**을 클릭할 수 있습니다.
4. 머리글이 있는 데이터를 선택하고 입력 데이터 범위 아이콘을 클릭합니다. "My data has headers" 상자가 선택되어 있는지 확인합니다.
5. **Output** 아래에 출력을 배치할 셀 번호를 입력합니다. 여기서는 "H1"을 입력합니다.
6. **Predict**를 클릭합니다.

	![Predict 섹션][02]

## 새 웹 서비스를 추가하는 단계

웹 서비스를 게시([이 페이지](machine-learning-walkthrough-5-publish-web-service.md)에 게시 방법이 설명되어 있음)하거나 기존 웹 서비스를 사용합니다.

웹 서비스에 대한 API 키를 가져옵니다. 새 웹 서비스의 기존 웹 서비스를 게시하는지 여부에 따라 이 작업을 수행하는 위치가 달라집니다.

**기존 웹 서비스**

1. 기계 학습 스튜디오에서 왼쪽 창의 **WEB SERVICES** 섹션을 클릭한 다음 웹 서비스를 선택합니다.

	![Studio 웹 서비스 선택][04]

2. 웹 서비스에 대한 API 키를 복사합니다.

	![Studio API 키][05]

3. 웹 서비스의 **DASHBOARD** 탭에서 **REQUEST/RESPONSE** 링크를 클릭합니다.
4. **요청 URI** 섹션을 찾습니다. URL을 복사하고 저장합니다.

**새 웹 서비스**

1. Azure 기계 학습 웹 서비스 포털에서 **웹 서비스**를 클릭한 다음 사용자의 웹 서비스를 선택합니다.
2. **사용**을 클릭합니다.
3. **기본 사용량 정보** 섹션을 찾습니다. **기본 키** 및 **요청-응답** URL을 복사하고 저장합니다.


## 새 웹 서비스를 추가하는 단계

1. 웹 서비스를 게시([이 페이지](machine-learning-walkthrough-5-publish-web-service.md)에 게시 방법이 설명되어 있음)하거나 기존 웹 서비스를 사용합니다.
2. Excel에서 **Web Services** 섹션으로 이동합니다(**Predict** 섹션에 있는 경우 뒤로 화살표를 클릭하여 웹 서비스 목록으로 이동).

	![웹 서비스 선택으로 이동][03]
3. **Add Web Service**를 클릭합니다.
4. URL을 **URL**이라고 레이블이 지정된 텍스트 상자에 붙여 넣습니다.
5. API/기본 키를 **API 키** 텍스트 상자에 붙여넣습니다.
6. **추가**를 클릭합니다.

	![기존 웹 서비스에 대한 URL 및 API 키][06]

10.	웹 서비스를 사용하려면 위의 지침 "기존 웹 서비스를 사용하는 단계"를 따릅니다.

## 통합 문서 공유

통합 문서를 저장하면 추가한 웹 서비스의 API/기본 키도 저장됩니다. 즉, 신뢰할 수 있는 사용자와만 통합 문서를 공유해야 합니다.

아래 또는 [포럼](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)에서 질문을 할 수 있습니다.

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

<!---HONumber=AcomDC_0914_2016-->