
<properties
	pageTitle="DataMarket Recommendations API에서 Azure Cognitive Services Recommendations API로 마이그레이션 | Microsoft Azure"
	description="Azure Machine Learning 권장 사항 - 권장 사항 cognitive service로 마이그레이션"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="luisca"/>


# DataMarket Recommendations API에서 Azure Cognitive Services Recommendations API로 마이그레이션
이 문서는 [Microsoft DataMarket Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations)에서 [Microsoft Azure Cognitive Services Recommendations API](https://www.microsoft.com/cognitive-services/ko-KR/recommendations-api)로 마이그레이션하는 방법을 보여 줍니다.

DataMarket Recommendations API는 2016년 12월 31일부터 새 고객을 받아들이지 않으며 2017년 2월 28일에는 더 이상 사용되지 않을 예정입니다.

## Azure Cognitive Services Recommendations API 사용을 시작하려면 어떻게 하나요?

Cognitive Services Recommendations API로 마이그레이션하려면 다음을 수행합니다.

1.	Azure 구독이 아직 없는 경우 하나 [등록](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)합니다.

1.	[빠른 시작 가이드](cognitive-services-recommendations-quick-start.md)에서 단계별 지침을 받아 Cognitive Services Recommendations API에 등록하고 프로그래밍 방식으로 사용합니다.

1.	[Cognitive Services Recommendations API 방문 페이지](https://www.microsoft.com/cognitive-services/ko-KR/recommendations-api)로 이동하여 서비스에 대해 알아보고 설명서를 찾을 수 있습니다.

## 내 모델을 빌드하는 데 Recommendations UI를 사용했습니다. Cognitive Services Recommendations API와 유사한 도구가 있나요?

그렇습니다. 새 [Recommendations UI](http://recommendations-portal.azurewebsites.net/)에 대한 URL은 http://recommendations-portal.azurewebsites.net입니다.

>[AZURE.NOTE] 여기서는 DataMarket 자격 증명이 작동하지 않습니다. Azure Portal에서 구독을 등록하고 새 [Recommendations UI](http://recommendations-portal.azurewebsites.net/)를 사용하는 데 필요한 계정 키를 가져옵니다. 계정 키가 없는 경우 [빠른 시작 가이드](cognitive-services-recommendations-quick-start.md)의 작업 1을 참조하세요.

##새 API 형식은 DataMarket Recommendations API와 동일한가요?

새 API는 DataMarket 버전에서 지원되었던 시나리오와 동일한 시나리오 및 프로세스 흐름을 지원하지만 실제 API 인터페이스는 [Microsoft REST API 지침](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)을 따르도록 업데이트되었습니다. API는 더 일관되고 Swagger를 지원하는 도구와 잘 작동합니다.

각 API를 이해하려면 [API 탐색기](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db)를 확인하세요. *시도* 단추를 사용하여 API 호출을 테스트해 보세요. Recommendations API(카탈로그 및 사용 파일)에서 사용하는 파일 형식은 변경되지 않으므로 해당 파일을 생성하도록 구축된 동일한 파일 및/또는 인프라를 계속 사용할 수 있습니다.

##Cognitive Services Recommendations API의 몇 가지 새로운 기능은 무엇인가요?

최근 두 달 동안 Cognitive Services Recommendations API에 대한 새로운 기능을 출시했습니다.
-	코드 한 줄도 작성할 필요가 없는 학습 및 테스트 모델을 위한 Recommendations UI
-	한 번에 수천 건의 권장 사항을 제공하는 배치 평가
-	권장 사항 모델의 품질을 쿼리하기 위한 빌드 메트릭 지원
-	비즈니스 규칙 지원
-	사용량 및 카탈로그 파일을 열거하고 다운로드하는 기능
-	권장 사항 모델에서 항목 기능의 품질을 쿼리하기 위한 순위 작성 지원
-	카탈로그에서 제품을 검색하는 기능 추가됨

## Microsoft에서 DataMarket Recommendations API에 대한 지원을 언제 중단하나요?

[DataMarket에서 Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations)는 2016년 12월 31일부터 새 고객을 받아들이지 않으며 2017년 2월 28일에는 완전히 사용 중지될 예정입니다.

## Cognitive Services Recommendations API에서 내 모델을 다시 만드는 데 필요한 데이터가 없다면 어떻게 되나요?

이러한 전환이 사용자에게 최대한 쉽도록 하려고 합니다. DataMarket 계정의 이전 모델을 새로운 Azure Cognitive Services Recommendations API 구독으로 이동하도록 지원해드릴 수 있습니다. [mlapi@microsoft.com](mailto://mlapi@microsoft.com)으로 문의하시기 바랍니다. 모델을 귀하의 새 계정과 연결하기 전에 DataMarket 구독 ID와 Cognitive Services 구독 ID를 제공하도록 요청합니다.

<!---HONumber=AcomDC_0914_2016-->