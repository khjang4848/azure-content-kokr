<properties
	pageTitle="기계 학습을 사용한 신용 위험에 대 한 예측 솔루션 | Microsoft Azure"
	description="Azure 기계 학습 스튜디오의 신용 위험 평가에 대한 예측 분석 솔루션을 만드는 방법을 보여주는 자세한 연습."
	keywords="신용 위험, 예측 분석 솔루션, 위험 평가"
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
	ms.topic="get-started-article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# 연습: Azure 기계 학습의 신용 위험 평가에 대한 예측 분석 솔루션 개발

신용대출 지원 시 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.

신용 위험 평가는 물론 복잡한 문제이지만 질문의 매개 변수를 약간 간소화합니다. 그런 다음 Microsoft Azure 기계 학습과 기계 학습 스튜디오 및 기계 학습 웹 서비스를 사용하여 이러한 예측 솔루션을 만들 수 있는 방법의 예제로 사용할 수 있습니다.

이 자세한 연습에서는 기계 학습 스튜디오에서 예측 분석 모델을 개발한 다음 Azure 기계 학습 웹 서비스로 배포하는 프로세스를 따릅니다. 공개적으로 사용 가능한 신용 위험 데이터에서 시작하여 해당 데이터를 기반으로 예측 모델의 학습을 진행한 다음 다른 사람이 사용할 수 있는 웹 서비스로 모델을 배포합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] 기계 학습 스튜디오의 기능을 개략적으로 제공하는 다이어그램을 다운로드하고 인쇄하려면 [Azure 기계 학습 스튜디오 기능 개요](machine-learning-studio-overview-diagram.md)를 참조하세요.

신용 위험 평가 솔루션을 만들려면 다음 단계를 따릅니다.

1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)
6.	[웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

이 연습은 [Cortana Intelligence 갤러리](http://gallery.cortanaintelligence.com/)에서 [이진 분류: 신용 위험 예측](http://go.microsoft.com/fwlink/?LinkID=525270) 샘플 실험의 간소화된 버전을 기반으로 합니다.

<!---HONumber=AcomDC_0921_2016-->