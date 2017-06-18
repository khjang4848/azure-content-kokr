<properties
	pageTitle="기계 학습 실험 실험에서 예측 실험으로 변환 | Microsoft Azure"
	description="예측 분석 모델을 학습하는 데 사용되는 기계 학습 학습 실험을 웹 서비스로 배포할 수 있는 예측 실험으로 변환하는 방법입니다."
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
	ms.date="08/19/2016"
	ms.author="garye"/>

# 기계 학습 실험 실험에서 예측 실험으로 변환

Azure 기계 학습을 사용하여 예측 분석 솔루션을 빌드, 테스트 및 배포할 수 있습니다.

*학습 실험*을 만들고 반복하여 예측 분석 모델을 학습하고 이를 사용하여 새 데이터의 점수를 매길 준비가 완료되었으면 점수 매기기를 위해 실험을 준비하고 간소화해야 합니다. 그런 다음 사용자가 모델로 데이터를 보내고 모델의 예측을 받을 수 있도록 이 *예측 실험*을 Azure 웹 서비스로 배포할 수 있습니다.

예측 실험으로 변환하면 학습된 모델을 웹 서비스로 배포할 준비가 완료됩니다. 웹 서비스 사용자가 입력 데이터를 모델로 보내면 모델에서 예측 결과가 다시 전송됩니다. 따라서 예측 실험을 변환할 때 다른 사람이 모델을 사용할 것을 염두에 둘 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

학습 실험을 예측 실험으로 변환하는 프로세스는 세 단계로 구성됩니다.

1.	학습한 기계 학습 모델을 저장한 다음 기계 학습 알고리즘과 [모델 학습][train-model] 모듈을 저장한 학습된 모델로 바꿉니다.
2.	점수 매기기에 필요한 모듈로만 실험을 자릅니다. 학습 실험에는 학습에 필요하지만 모델이 학습되고 점수 매기기에 사용할 준비가 완료된 후에는 필요 없는 여러 모듈이 포함되어 있습니다.
3.	실험에서 웹 서비스 사용자의 데이터를 허용할 위치 및 반환할 데이터를 정의합니다.

## 웹 서비스 설정 단추

실험을 실행하고 나면 (실험 캔버스의 아래쪽에 있는 **실행** 단추), **웹 서비스 설정** 단추 (**예측 웹 서비스** 옵션 선택함)가 학습 실험을 예측 실험으로 전환하는 세 가지 단계를 수행합니다.

1.	학습된 모듈을 모듈 팔레트(실험 캔버스의 왼쪽)의 **학습된 모델** 섹션에 모듈로 저장한 다음 기계 학습 알고리즘과 [모델 학습][train-model] 모듈을 저장한 학습된 모델로 바꿉니다.
2.	명확히 필요 없는 모듈을 제거합니다. 이 예제에서는 [분할 데이터][split], 두 번째 [모델 점수 매기기][score-model] 및 [모델 평가][evaluate-model] 모듈이 여기에 포함됩니다.
3.	웹 서비스 입력 및 출력 모듈을 만들어 실험의 기본 위치에 추가합니다.

예를 들어 다음 실험은 샘플 인구 조사 데이터를 사용하여 2클래스 향상된 의사 결정 트리 모델을 학습합니다.

![학습 실험][figure1]

이 실험의 모듈은 기본적으로 네 가지 기능을 수행합니다.

![모듈 함수][figure2]

이 학습 실험을 예측 실험으로 변환하면 이러한 모듈 중 일부가 더 이상 필요 없거나 다른 용도로 사용됩니다.

- **데이터** - 이 샘플 데이터 집합의 데이터는 점수를 매기는 동안 사용되지 않습니다. 웹 서비스 사용자가 점수를 매길 데이터를 제공합니다. 그러나 데이터 형식과 같은 이 데이터 집합의 메타데이터는 학습된 모델에서 사용됩니다. 따라서 이 메타데이터를 제공할 수 있도록 예측 실험에서 데이터 집합을 유지해야 합니다.

- **준비** - 점수 매기기를 위해 제출할 데이터에 따라 들어오는 데이터를 처리하는 데 이러한 모듈이 필요할 수도 있고 그렇지 않을 수도 있습니다.

	예를 들어 이 예제에서는 샘플 데이터 집합에 누락된 값이 있을 수 있으며, 모델을 학습하는 데 필요 없는 열이 포함되어 있습니다. 따라서 누락된 값을 처리하기 위해 [누락된 데이터 정리][clean-missing-data] 모듈이 포함되었으며, 데이터 흐름에서 이러한 추가 열을 제외하기 위해 [데이터 집합의 열 선택][select-columns] 모듈이 포함되었습니다. 점수 매기기를 위해 웹 서비스를 통해 제출할 데이터에 누락된 값이 있다는 것을 아는 경우 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다. 그러나 [데이터 집합의 열 선택][select-columns] 모듈은 점수를 매길 기능 집합을 정의하는 데 유용하기 때문에 이 모듈은 유지해야 합니다.

- **학습** - 모델이 성공적으로 학습되었으면 단일 학습된 모델 모듈로 저장합니다. 그런 다음 이러한 개별 모듈을 저장한 학습된 모델로 바꿉니다.

- **점수** - 이 예제에서는 분할 모듈을 사용하여 데이터 스트림을 테스트 데이터 및 학습 데이터 집합으로 나눕니다. 예측 실험에서는 필요하지 않으므로 제거할 수 있습니다. 마찬가지로 두 번째 [모델 점수 매기기][score-model] 모듈 및 [모델 평가][evaluate-model] 모듈은 테스트 데이터의 결과를 비교하는 데 사용되므로 이러한 모듈도 예측 실험에는 필요 없습니다. 그러나 나머지 [모델 점수 매기기][score-model] 모듈은 웹 서비스를 통해 점수 결과를 반환하는 데 필요합니다.

다음은 **웹 서비스 설정**을 클릭한 후 이 예제의 변화된 모양입니다.

![변환된 예측 실험][figure3]

이 정도면 실험을 웹 서비스로 배포하도록 준비하는 데 충분할 수 있습니다. 그러나 실험에 특정한 몇 가지 추가 작업을 수행할 수도 있습니다.

### 입력 및 출력 모듈 조정

학습 실험에서는 학습 데이터 집합을 사용한 다음 일부 처리를 수행하여 기계 학습 알고리즘에 필요한 형식의 데이터를 가져왔습니다. 웹 서비스를 통해 받아야 하는 데이터에 이 처리가 필요 없는 경우 **웹 서비스 입력 모듈**을 실험의 다른 노드로 이동할 수 있습니다.

예를 들어 기본적으로 **웹 서비스 설정**은 위 그림과 같이 데이터 흐름의 맨 위에 **웹 서비스 입력** 모듈을 둡니다. 그러나 입력 데이터에 이 처리가 필요 없는 경우에는 **웹 서비스 입력**을 데이터 처리 모듈 뒤에 수동으로 배치할 수 있습니다.

![웹 서비스 입력 이동][figure4]

이제 웹 서비스를 통해 제공되는 입력 데이터가 전처리 없이 모델 점수 매기기 모듈로 직접 전달됩니다.

마찬가지로 기본적으로 **웹 서비스 설정**은 웹 서비스 출력 모듈을 데이터 흐름의 맨 아래에 둡니다. 이 예제에서 웹 서비스는 사용자에게 전체 입력 데이터 벡터와 점수 매기기 결과가 포함된 [모델 점수 매기기][score-model] 모듈을 반환합니다.

그러나 다른 결과를 반환하려는 경우, 예를 들어 입력 데이터의 전체 벡터를 제외하고 점수 매기기 결과만 반환하려는 경우 점수 매기기 결과를 제외하고 모든 열을 제외하도록 [데이터 집합의 열 선택][select-columns] 모듈을 삽입할 수 있습니다. 그런 다음 **웹 서비스 출력** 모듈을 [데이터 집합의 열 선택][select-columns] 모듈의 출력으로 이동합니다.

![웹 서비스 출력 이동][figure5]

### 추가 데이터 처리 모듈 추가 또는 제거

점수를 매기는 동안 필요 없다는 것을 알고 있는 추가 모듈이 실험에 있는 경우 이러한 모듈을 제거할 수 있습니다. 예를 들어 **웹 서비스 입력** 모듈을 데이터 처리 모듈 이후의 시점으로 이동했기 때문에 예측 실험에서 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다.

이제 예측 실험은 다음과 같은 모양입니다.

![추가 모듈 제거][figure6]

### 선택적 웹 서비스 매개 변수 추가

경우에 따라 서비스에 액세스할 때 웹 서비스 사용자가 모듈의 동작을 변경하도록 허용할 수 있습니다. *웹 서비스 매개 변수*를 통해 이 작업을 수행할 수 있습니다.

일반적인 예는 배포된 웹 서비스의 사용자가 웹 서비스에 액세스할 때 다른 데이터 원본을 지정할 수 있도록 [데이터 가져오기][import-data] 모듈을 설정하는 것입니다. 또는 다른 대상을 지정할 수 있도록 [데이터 내보내기][export-data] 모듈을 구성하는 것입니다.

웹 서비스 매개 변수를 정의하여 하나 이상의 모듈 매개 변수와 연결하고 이러한 매개 변수가 필수인지 또는 선택 사항인지 지정할 수 있습니다. 그런 다음 웹 서비스의 사용자는 서비스에 액세스할 때 이러한 매개 변수의 값을 제공할 수 있으며, 그에 따라 모듈 동작이 수정됩니다.

웹 서비스 매개 변수에 대한 자세한 내용은 [Azure 기계 학습 웹 서비스 매개 변수 사용][webserviceparameters]을 참조하세요.

[webserviceparameters]: machine-learning-web-service-parameters.md


## 예측 실험을 웹 서비스로 배포

이제 예측 실험이 충분히 준비되었으므로 이를 Azure 웹 서비스로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

전체 배포 프로세스에 대한 자세한 내용은 [Azure 기계 학습 웹 서비스 배포][deploy]를 참조하세요.

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

<!---HONumber=AcomDC_0914_2016-->