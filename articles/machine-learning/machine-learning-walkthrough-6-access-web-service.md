<properties
	pageTitle="6단계: 기계 학습 웹 서비스 액세스 | Microsoft Azure"
	description="예측 솔루션 연습 개발의 6단계: 활성 Azure 기계 학습 웹 서비스 액세스"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# 연습 6단계: Azure 기계 학습 웹 서비스 액세스

[Azure 기계 학습에서 예측 분석 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 연습의 마지막 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)
6.	**웹 서비스 액세스**

----------

이 연습의 이전 단계에서 신용 위험 예측 모델을 사용하는 웹 서비스를 배포했습니다. 이제 사용자는 서비스에 데이터를 보내고 결과를 받을 수 있어야 합니다.

웹 서비스는 다음 두 방법의 하나로 REST API를 사용하여 데이터를 받고 반환할 수 있는 Azure 웹 서비스입니다.

-	**요청/응답** - 사용자가 HTTP 프로토콜을 사용하여 하나 이상의 신용 데이터 행을 서비스에 보내고 서비스에서는 하나 이상의 결과 집합으로 응답합니다.
-	**일괄 처리 실행** - 사용자가 Azure Blob 신용 데이터 행 하나 이상을 저장한 다음 Blob 위치를 서비스에 보냅니다. 서비스에서는 입력 Blob의 모든 데이터 행에 대한 점수를 매기고 결과를 다른 Blob에 저장한 다음 해당 컨테이너의 URL을 반환합니다.

웹 서비스를 액세스하는 가장 빠르고 쉬운 방법은 [Azure 웹앱 마켓플레이스](https://azure.microsoft.com/marketplace/web-applications/all/)에서 사용 가능한 웹앱 템플릿을 통한 것입니다. 이러한 웹앱 템플릿은 웹 서비스의 입력 데이터 및 예상 결과를 알고 있는 사용자 지정 웹앱을 구축할 수 있습니다. 따라서 웹 서비스 및 데이터에 액세스하도록 하기만 하면 나머지 작업은 템플릿이 수행합니다.

웹앱 템플릿 사용에 대한 자세한 내용은 [웹앱 템플릿을 사용한 Azure 기계 학습 웹 서비스 사용](machine-learning-consume-web-service-with-web-app-template.md)을 참조하세요.

또한 R, C# 및 Python 프로그래밍 언어에서 제공하는 시작 코드를 사용하여 웹 서비스에 액세스하는 사용자 지정 응용 프로그램을 개발할 수도 있습니다. 자세한 내용은 [Azure 기계 학습 실험에서 게시된 기계 학습 웹 서비스를 사용하는 방법](machine-learning-consume-web-services.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->