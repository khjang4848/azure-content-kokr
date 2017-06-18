<properties 
	pageTitle="Azure 기계 학습에서 알고리즘을 최적화하는 매개 변수를 선택하는 방법 | Microsoft Azure" 
	description="Azure 기계 학습에서 알고리즘에 대한 최적 매개 변수 집합을 선택하는 방법에 대해 설명합니다." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="bradsev" />


# Azure 기계 학습에서 알고리즘을 최적화하는 매개 변수를 선택하는 방법

이 토픽에서는 Azure Machine Learning에서 알고리즘에 대한 올바른 하이퍼 매개 변수 집합을 선택하는 방법에 대해 설명합니다. 대부분의 기계 학습 알고리즘은 설정할 매개 변수를 포함하고 있습니다. 모델을 학습할 때 이러한 매개 변수의 값을 제공해야 합니다. 학습된 모델의 효율성은 선택한 모델 매개 변수에 따라 달라집니다. 최적의 매개 변수 집합을 찾는 프로세스를 모델 선택이라고 합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

모델 선택을 수행할 수 있는 여러 가지 방법이 있습니다. 기계 학습에서는 교차 유효성 검사가 모델 선택에 가장 널리 사용되는 방법 중 하나이며, Azure 기계 학습의 기본 모델 선택 메커니즘입니다. Azure 기계 학습에서는 R과 Python을 둘 다 지원하므로 언제든지 R 또는 Python을 사용하여 고유한 모델 선택 메커니즘을 구현할 수 있습니다.

최상의 매개 변수 집합을 찾는 프로세스는 4단계로 구성됩니다.

1.	**매개 변수 공간 정의**:먼저 알고리즘에 대해 고려할 정확한 매개 변수 값을 결정합니다.
2.	**교차 유효성 검사 설정을 정의**:데이터 집합에 대해 교차 유효성 검사 접기 수를 선택하는 방법을 결정합니다.
3.	**메트릭 정의**: 그런 다음 최상의 매개 변수 집합(예: 정확도, 평균 제곱근 오차, 정밀도, 재현율 또는 f-score)을 결정하는 데 사용할 메트릭을 결정합니다.
4.	**학습, 평가 및 비교**: 매개 변수 값의 각 고유한 조합에 대해 교차 유효성 검사를 수행하고, 사용자가 정의한 오류 메트릭을 기반으로 가장 적합한 모델을 선택할 수 있습니다.

다음 실험에서는 Azure 기계 학습에서 이를 수행할 수 있는 방법을 보여 줍니다.

![image1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## 매개 변수 공간 정의
모델 초기화 단계에서 매개 변수 집합을 정의할 수 있습니다. 모든 기계 학습 알고리즘의 매개 변수 창에는 두 가지 강사 모드(**단일 매개 변수** 및 **매개 변수 범위**)가 있습니다. 이 예에서는 **매개 변수 범위** 모드를 선택해야 합니다(그림 1). 이 모드에서는 각 매개 변수의 여러 값을 입력할 수 있습니다. 즉, 쉼표로 구분된 값을 텍스트 상자에 입력할 수 있습니다. 또는 **범위 작성기 사용**을 사용하여 표의 최대 및 최소 요소 수와 생성할 총 요소 수를 정의할 수 있습니다. 기본적으로 매개 변수 값은 선형 눈금으로 생성됩니다. 그러나 **로그 눈금** 상자를 선택한 경우에는 로그 눈금으로 값이 생성됩니다(즉, 인접 요소의 비율이 차이가 나지 않고 일정함). 정수 매개 변수의 경우 범위를 하이픈 “-“으로 정의할 수 있습니다. 예: "1-10" 즉, 1에서 10(모두 포함) 사이의 모든 정수가 매개 변수 집합을 형성합니다. 혼합 모드도 지원됩니다.예: "1-10, 20, 50" 이 경우 1-10의 정수 외에 20 및 50도 매개 변수 집합에 추가됩니다.
  
![image2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![image3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## 교차 유효성 검사 접기 정의
[파티션 및 샘플][partition-and-sample] 모듈을 사용하여 데이터에 접기 수를 임의로 할당할 수 있습니다. 다음 그림에는 다섯 번 접기를 정의하고 샘플 인스턴스에 접기 수를 임의로 할당한 모듈에 대한 예제 구성이 나와 있습니다.

![image4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## 메트릭 정의
[모델 조정 하이퍼 매개 변수][tune-model-hyperparameters] 모듈에서는 주어진 알고리즘 및 데이터 집합에 대한 최상의 매개 변수 집합을 경험적으로 선택할 수 있습니다. 이 모듈의 속성 창에는 학습 모델에 대한 기타 정보 외에 최상의 매개 변수 집합을 결정하는 데 사용할 메트릭이 포함됩니다. 분류 및 회귀 알고리즘 각각에 대한 두 개의 드롭다운 목록이 있습니다. 고려 중인 알고리즘은 분류 알고리즘인 경우에는 회귀 메트릭이 무시되고 그 반대의 경우도 마찬가지입니다. 이 특정 예제에서는 **정확도**를 메트릭으로 선택했습니다.
 
![image5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## 학습, 평가 및 비교  
동일한 [모델 조정 하이퍼 매개 변수][tune-model-hyperparameters] 모듈이 매개 변수 집합에 해당하는 모든 모델을 학습하고, 여러 메트릭을 평가한 다음, 선택한 메트릭을 기반으로 최상의 학습된 모델을 출력합니다. 이 모듈에는 다음 두 개의 필수 입력이 있습니다.

* 미숙한 학습자
* 데이터 집합

또한 이 모듈은 선택적 데이터 집합 입력을 포함하고 있습니다. 접기 정보가 있는 데이터 집합을 필수 데이터 집합 입력에 연결합니다. 데이터 집합에 접기 정보가 할당되지 않은 경우에는 기본적으로는 접기 수가 10개인 교차 유효성 검사가 자동으로 실행됩니다. 접기 할당이 수행되지 않고 유효성 검사 데이터 집합이 최적의 데이터 집합 포트에서 제공된 경우에는 학습 테스트 모드가 선택되고 첫 번째 데이터 집합이 각 매개 변수 조합에 대한 모델의 학습에 사용됩니다. 그런 다음 유효성 검사 데이터 집합에 대해 모델이 평가됩니다. 모듈의 왼쪽 출력 포트에는 매개 변수 값 함수로 다양한 메트릭이 표시됩니다. 오른쪽 출력 포트에는 선택한 메트릭(이 예의 경우 정확도)별로 최상의 모델에 해당하는 학습된 모델이 제공됩니다.

![image6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![image7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
오른쪽 출력 포트를 시각화하여 선택한 정확한 매개 변수를 확인할 수 있습니다. 이 모델은 테스트 집합을 채점하거나 학습된 모델로 저장한 후 조작 가능한 웹 서비스를 제공하는 데 사용될 수 있습니다.


<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=AcomDC_0914_2016-->