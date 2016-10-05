<properties
	pageTitle="4단계: 예측 분석 모델 학습 및 평가 | Microsoft Azure"
	description="예측 솔루션 개발 연습 4단계: Azure 기계 학습 스튜디오에서 다중 모델을 학습하고, 점수를 매기고, 평가합니다."
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


# 연습 4단계: 예측 분석 모델 학습 및 평가

[Azure 기계 학습에서 예측 분석 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 연습의 네 번째 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**모델 학습 및 평가**
5.	[웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)
6.	[웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

----------

Azure 기계 학습 스튜디오를 사용하여 기계 학습 모델을 만들 때의 이점 중 하나는 실험에서 한 번에 두 개 이상의 모델 유형을 시도하고 결과를 비교할 수 있다는 점입니다. 이 유형의 실험을 사용하면 문제에 대한 최상의 솔루션을 찾을 수 있습니다.

이 연습으로 개발하고 있는 실험에서는 두 가지 모델을 만들고 모델의 점수 매기기 결과를 비교하여 최종 실험에서 사용할 알고리즘을 결정합니다.

다양한 모델을 선택할 수 있습니다. 사용할 수 있는 모델을 보려면 모듈 팔레트에서 **기계 학습** 노드를 확장하고 **모델 초기화** 및 그 아래 노드를 확장합니다. 이 실험에서는 SVM(Support Vector Machine) 및 2클래스 향상된 의사 결정 트리 모듈을 선택합니다.

> [AZURE.TIP] 해결하려는 특정 문제에 가장 적합한 기계 학습 알고리즘을 결정하는 데 대한 도움말을 보려면 [Microsoft Azure 기계 학습을 위한 알고리즘 선택 방법](machine-learning-algorithm-choice.md)을 참조하세요.

## 모델 학습

먼저 향상된 의사 결정 트리 모델을 설정해 보겠습니다.

1.	모듈 팔레트에서 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈을 찾고 캔버스로 끌어서 놓습니다.
2.	[모델 학습][train-model] 모듈을 찾고 캔버스로 끌고 나서 향상된 의사 결정 트리 모듈의 출력을 [모델 학습][train-model] 모듈의 왼쪽 입력 포트("학습되지 않은 모델")에 연결합니다.
    
    [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree] 모듈은 일반 모델을 초기화하고 [모델 학습][train-model]에서는 학습 데이터를 사용하여 모델을 학습합니다.
     
3.	[R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력(“결과 데이터 집합”)을 [모델 학습][train-model] 모듈의 오른쪽 입력 포트("데이터 집합")에 연결합니다.

	> [AZURE.TIP] 이 실험에서는 [R 스크립트 실행][execute-r-script] 모듈에 대한 입력 중 두 개와 출력 중 하나가 필요하지 않으므로 연결되지 않은 상태로 유지합니다. 몇몇 모듈에서는 이 상태가 일반적입니다.

4.	[모델 학습][train-model] 모듈을 선택합니다. **속성** 창에서 **Launch column selector**(열 선택기 시작)를 클릭하고, 첫 번째 드롭다운에서 **포함**을 선택하고, 두 번째 드롭다운에서 **열 이름**을 선택하고, 텍스트 필드에 "신용 위험"을 입력합니다(**열 인덱스**를 선택하고 "21"을 입력할 수도 있음). 이는 모델에서 예측할 열로서 열 21, 신용 위험 값을 나타냅니다.


실험의 이 부분은 이제 다음과 같이 표시됩니다.

![모델 학습][1]

다음으로 SVM 모델을 설정합니다.

먼저 SVM에 대한 간단한 설명입니다. 향상된 의사 결정 트리는 모든 기능 유형에서 제대로 작동합니다. 그러나 SVM 모듈은 선형 분류자를 생성하므로 여기서 생성하는 모델에는 모든 숫자 기능의 크기가 같을 때 최적의 테스트 오류가 포함됩니다. 그러므로 모든 숫자 기능을 같은 배율로 변환하기 위해 "Tanh" 변환([데이터 정규화][normalize-data] 모듈 사용)을 사용하여 숫자를 [0,1] 범위로 변환합니다. 문자열 기능은 SVM 모듈에 의해 범주 기능으로 변환된 다음 이진 0/1 기능으로 변환되므로 문자열 기능을 수동으로 변환할 필요가 없습니다. 또한 신용 위험 열(열 21)을 변환하지 않으려고 합니다. 이 열은 숫자 값이지만 모델을 학습하여 예측할 값이므로 단독으로 유지해야 합니다.

SVM 모델을 설정하려면 다음을 수행합니다.

1.	모듈 팔레트에서 [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈을 찾고 캔버스로 끌어서 놓습니다.
2.	[모델 학습][train-model] 모듈을 마우스 오른쪽 단추로 클릭하고 **복사**를 선택하고 나서 캔버스를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다. [모델 학습][train-model] 모듈의 복사본에는 원래 모듈과 같은 열이 선택되어 있습니다.
3.	SVM 모듈의 출력을 두 번째 [모델 학습][train-model] 모듈의 왼쪽 입력 포트("학습되지 않은 모델")에 연결합니다.
4.	[데이터 정규화][normalize-data] 모듈을 찾고 캔버스로 끌어옵니다.
5.	이 모듈의 입력을 왼쪽 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결합니다. 모듈의 출력 포트는 두 개 이상의 다른 모듈에 연결할 수 있습니다.
6.	[데이터 정규화][normalize-data] 모듈의 왼쪽 출력 포트("변환된 데이터 집합")를 두 번째 [모델 학습][train-model] 모듈의 오른쪽 입력 포트("데이터 집합")에 연결합니다.
7.	[데이터 정규화][normalize-data] 모듈에 대한 **속성** 창에서 **변환 메서드** 매개 변수에 대해 **Tanh**을 선택합니다.
8.	**Launch column selector**(열 선택기 시작)을 클릭하고, **Begin With**(시작 문자)에 대해 “열 없음"(No columns)을 선택하고, 첫 번째 드롭다운에서 **Include**(포함)를 선택하고, 두 번째 드롭다운에서 **column type**(열 형식)을 선택하고, 세 번째 드롭다운에서 **Numeric**(숫자)을 선택합니다. 이렇게 하면 모든 숫자 열(및 숫자열만)이 변환되도록 지정됩니다.
9.	이 행 오른쪽에 있는 더하기 기호(+)를 클릭하면 새 드롭다운 행이 만들어집니다. 첫 번째 드롭다운에서 **제외**를 선택하고 두 번째 드롭다운에서 **열 이름**을 선택한 다음 텍스트 필드를 클릭하고 열 목록에서 "신용 위험"을 선택합니다. 이렇게 하면 신용 위험 열이 무시됩니다. 이 열은 숫자이므로 그렇지 않은 경우 변환되기 때문에 이 작업이 필요합니다.
10.	**확인**을 클릭합니다.


이제 [데이터 정규화][normalize-data] 모듈은 신용 위험 열을 제외한 모든 숫자 열에서 Tanh 변환을 수행하도록 설정됩니다.

실험의 이 부분은 이제 다음과 같이 표시됩니다.

![두 번째 모델 학습][2]

## 모델 점수 매기기 및 평가

[데이터 분할][split] 모듈을 통해 구분된 테스트 데이터를 사용하여 학습된 모듈의 점수를 매깁니다. 그리고 나서 두 모델의 결과를 비교하여 더 나은 결과를 생성한 모듈을 확인할 수 있습니다.

1.	[모델 점수 매기기][score-model] 모듈을 찾아 캔버스로 끌어서 놓습니다.
2.	이 모듈의 왼쪽 입력 포트를 향상된 의사 결정 트리 모델에 연결합니다. 즉, [2클래스 향상된 의사 결정 트리][train-model] 모듈에 연결된 [모델 학습][two-class-boosted-decision-tree] 모듈의 출력 포트에 연결합니다.
3.	[모델 점수 매기기][score-model] 모듈의 오른쪽 입력 포트를 오른쪽 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결합니다.

    이제 [모델 점수 매기기][score-model] 모듈에서 테스트 데이터의 신용 정보를 사용하고, 모듈을 통해 실행하고, 모델이 생성하는 예측을 테스트 데이터의 실제 신용 위험 열과 비교할 수 있습니다.

4.	[모델 점수 매기기][score-model] 모듈을 복사하고 붙여넣어 두 번째 복사본을 만들거나 새 모듈을 캔버스로 끌어서 놓습니다.
5.	이 모듈의 왼쪽 입력 포트를 SVM 모델에 연결합니다. 즉, [2클래스 Support Vector Machine][train-model] 모듈에 연결된 [모델 학습][two-class-support-vector-machine] 모듈의 출력 포트에 연결합니다.
6.	SVM 모델에서는 학습 데이터에 대해 수행한 것과 같은 변환을 테스트 데이터에 대해 수행해야 합니다. 따라서 [데이터 정규화][normalize-data] 모듈을 복사하고 붙여넣고 두 번째 복사본을 만들고 오른쪽 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결합니다.
7.	[모델 점수 매기기][score-model] 모듈의 오른쪽 입력 포트를 [데이터 정규화][normalize-data] 모듈의 왼쪽 출력에 연결합니다.

점수 매기기 결과 두 개를 평가하려면 [모델 평가][evaluate-model] 모듈을 사용합니다.

1.	[모델 평가][evaluate-model] 모듈을 찾아 캔버스로 끌어서 놓습니다.
2.	왼쪽 입력 포트를 향상된 의사 결정 트리 모델과 연결된 [모델 점수 매기기][score-model] 모듈의 출력 포트에 연결합니다.
3.	오른쪽 입력 포트를 다른 [모델 점수 매기기][score-model] 모듈에 연결합니다.

캔버스 아래에서 **실행** 단추를 클릭하여 실험을 실행합니다. 몇 분이 걸릴 수 있습니다. 각 모듈에 실행 중임을 나타내는 회전 표시기가 표시되고 나서 모듈이 완료되면 녹색 확인 표시가 표시됩니다. 모든 모듈에 확인 표시가 있으면 실험 실행이 완료된 것입니다.

이제 실험이 다음과 같이 표시됩니다.

![두 모델 모두 평가][3]

결과를 확인하려면 [모델 평가][evaluate-model] 모듈의 출력 포트를 클릭하고 **시각화**를 선택합니다.

[모델 평가][evaluate-model] 모듈에서는 점수를 매긴 모델 두 개의 결과를 비교할 수 있는 곡선 및 메트릭 쌍을 생성합니다. 결과를 ROC(Receiver Operator Characteristic) 곡선, 정밀도/리콜 곡선 또는 리프트 곡선으로 볼 수 있습니다. 표시된 추가 데이터에는 혼동 행렬, AUC(Area Under the Curve)에 대한 누적 값 및 기타 메트릭이 포함됩니다. 슬라이더를 왼쪽이나 오른쪽으로 이동하여 임계값을 변경하고 메트릭 집합에 어떤 영향을 미치는 확인할 수 있습니다.

그래프 오른쪽에 있는 **점수를 매긴 데이터 집합** 또는 **비교할 점수를 매긴 데이터 집합**을 클릭하여 관련 곡선을 강조 표시하고 아래에 관련 메트릭을 표시합니다. 곡선 범례에서 "점수를 매긴 데이터 집합"은 [모델 평가][evaluate-model] 모듈의 왼쪽 입력 포트(이 경우 향상된 의사 결정 트리 모델)에 해당합니다. "비교할 점수를 매긴 데이터 집합"은 오른쪽 입력 포트(이 경우 SVM 모델)에 해당합니다. 이러한 레이블의 하나를 클릭하면 해당 모델의 곡선이 강조 표시되고 아래에 해당 메트릭이 표시됩니다.

![모델에 대한 ROC 곡선][4]

이러한 값을 검토하여 찾고 있는 결과를 제공하는 것에 가장 가까운 모델을 결정할 수 있습니다. 돌아가서 다른 모델에서 값을 변경하여 실험을 반복할 수 있습니다.

> [AZURE.TIP] 실험을 실행할 때마다 해당 반복에 대한 레코드가 실행 기록에서 유지됩니다. 이러한 반복을 확인하고 캔버스 아래에서 **실행 기록 보기**를 클릭하여 원하는 반복으로 돌아갈 수 있습니다. **속성** 창에서 **이전 실행**을 클릭하여 열었던 반복의 바로 이전 반복으로 돌아갈 수도 있습니다. 캔버스 아래에서 **다른 이름으로 저장**을 클릭하여 실험을 반복하도록 복사본을 만들 수 있습니다. 실험의 **요약** 및 **설명** 속성을 사용하여 실험 반복에서 시도한 항목을 기록합니다.

>  자세한 내용은 [Azure 기계 학습 스튜디오에서 실험 반복 관리](machine-learning-manage-experiment-iterations.md)를 참조하세요.


----------

**다음: [웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->