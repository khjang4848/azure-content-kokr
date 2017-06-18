<properties 
	pageTitle="Azure 기계 학습에서 모델 디버그 | Microsoft Azure" 
	description="Azure 기계 학습에서 모델을 디버그하는 방법에 대해 설명합니다." 
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
	ms.date="09/09/2016" 
	ms.author="bradsev;garye" />

# Azure 기계 학습에서 모델 디버그

이 문서에서는 Microsoft Azure 기계 학습에서 모델을 디버그하는 방법에 대해 설명합니다. 특히 모델을 실행할 때 다음 두 가지 오류 시나리오 중 하나가 발생할 수 있는 잠재적 원인을 알아봅니다.

* [모델 학습][train-model] 모듈에서 오류 발생
* [모델 점수 매기기][score-model] 모듈에서 잘못된 결과 생성

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 모델 학습 모듈에서 오류 발생

![image1](./media/machine-learning-debug-models/train_model-1.png)

[모델 학습][train-model] 모듈에는 다음 두 가지 입력이 필요합니다.

1. Azure 기계 학습에서 제공하는 모델 컬렉션에 있는 분류/회귀 모델 유형
2. 지정된 레이블 열이 있는 학습 데이터. 레이블 열은 예측할 변수를 지정합니다. 포함된 나머지 열은 기능으로 간주됩니다.

다음과 같은 경우 이 모듈에서 오류가 발생합니다.

1. 둘 이상의 열을 레이블로 선택하거나 잘못된 열 인덱스를 선택했기 때문에 레이블 열을 잘못 지정한 경우. 예를 들어 두 번째 경우는 열이 25개뿐인 입력 데이터 집합에서 열 인덱스 30을 사용한 경우에 적용됩니다.

2. 데이터 집합에 기능 열이 없는 경우. 예를 들어 입력 데이터 집합에 열이 1개뿐이고 이 열이 레이블 열로 표시된 경우 모델을 빌드할 기능이 없습니다. 이 경우에는 [모델 학습][train-model] 모듈에서 오류가 발생합니다.

3. 입력 데이터 집합(기능 또는 레이블)에 Infinity가 값으로 포함된 경우


## 모델 점수 매기기 모듈에서 올바른 결과를 생성하지 않음

![image2](./media/machine-learning-debug-models/train_test-2.png)

감독 학습에 대한 일반적인 학습/테스트 그래프에서 [데이터 분할][split] 모듈은 원래 데이터 집합을 모델을 학습하는 데 사용되는 부분과 학습된 모델이 학습하지 않은 데이터에서 얼마나 잘 작동하는지 점수를 매기는 데 예약된 부분의 두 부분으로 나눕니다. 그런 다음 학습된 모델은 테스트 데이터의 점수를 매기는 데 사용되며, 그 결과가 모델의 정확도를 확인하기 위해 평가됩니다.

[모델 점수 매기기][score-model] 모듈에는 다음 두 개의 입력이 필요합니다.

1. [모델 학습][train-model] 모듈의 학습된 모델 출력
2. 모델이 학습되지 않은 점수 매기기 데이터 집합

실험에 성공한 경우에도 [모델 점수 매기기][score-model] 모듈에서 잘못된 결과가 생성될 수 있습니다. 다음과 같은 여러 시나리오에서 이러한 상황이 발생할 수 있습니다.

1. 지정된 레이블이 범주인 경우 데이터에 대해 회귀 모델이 학습되면 [모델 점수 매기기][score-model] 모듈에서 잘못된 출력이 생성됩니다. 회귀에는 연속 응답 변수가 필요하기 때문입니다. 이 경우 분류 모델을 사용하는 것이 보다 적합합니다.
2. 마찬가지로 레이블 열에 부동 소수점 숫자가 있는 데이터 집합에 대해 분류 모델이 학습된 경우 바람직하지 않은 결과가 생성될 수 있습니다. 분류에는 범위가 유한하고 일반적으로 클래스 집합이 작은 값만 허용하는 불연속 응답 변수가 필요하기 때문입니다.
3. 점수 매기기 데이터 집합에 모델을 학습하는 데 사용되는 일부 기능이 포함되지 않은 경우 [모델 점수 매기기][score-model]에서 오류가 생성됩니다.
4. 해당 기능 중 하나에 대해 무한한 값 또는 누락된 값이 있는 점수 매기기 데이터 집합의 행에 해당하는 출력은 [모델 점수 매기기][score-model]에서 생성되지 않습니다.
5. [모델 점수 매기기][score-model]에서는 점수 매기기 데이터 집합의 모든 행에 대해 동일한 출력을 생성할 수 있습니다. 예를 들어 선택한 리프 노드당 최소 샘플 수가 사용 가능한 학습 예제 수보다 많은 경우 의사 결정 포리스트를 사용하여 분류할 때 이러한 상황이 발생할 수 있습니다.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=AcomDC_0914_2016-->