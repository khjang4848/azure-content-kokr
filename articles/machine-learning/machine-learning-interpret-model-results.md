<properties 
	pageTitle="기계 학습에서 모델 결과를 해석하는 방법 | Microsoft Azure" 
	description="모델 점수 매기기 출력을 사용하고 시각화하여 알고리즘에 설정된 최적의 매개 변수를 선택하는 방법" 
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


# Azure 기계 학습에서 모델 결과를 해석하는 방법 
 
**'모델 점수 매기기’ 출력 이해 및 시각화** 이 항목에서는 Azure Machine Learning Studio에서 예측 결과를 시각화하고 해석하는 방법을 설명합니다. 모델을 학습시키고 모델에 대한 예측을 수행한 다음(“모델 점수 매기기") 확보한 예측 결과를 파악하고 해석해야 합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Azure 기계 학습에는 다음과 같이 네 가지 주된 유형의 기계 학습 모델이 있습니다.

* 분류
* 클러스터링
* 회귀
* 추천 시스템

이러한 모듈에서 예측을 수행하는 데 사용하며 몇 가지 테스트 데이터를 제공하고 “점수 매기기"라고 하는 모듈은 다음과 같습니다.

* 분류 및 회귀를 위한 [모델 점수 매기기][score-model] 모듈,
* 클러스터링을 위한 [클러스터에 할당][assign-to-clusters] 모듈
* 추천 시스템을 위한 [매치박스 추천 점수 매기기][score-matchbox-recommender]
 
이 문서에서는 이러한 각 모듈의 예측 결과를 해석하는 방법에 대해 설명합니다. 이러한 유형의 모델에 대한 개요는 [Azure 기계 학습에서 알고리즘을 최적화하는 매개 변수를 선택하는 방법](machine-learning-algorithm-parameters-optimize.md)을 참조하세요.

이 항목에서는 예측 해석에 대해 다루지만 모델 평가는 다루지 않습니다. 모델 평가 방법에 대한 자세한 내용은 [Azure Machine Learning에서 모델 성능을 평가하는 방법](machine-learning-evaluate-model-performance.md)을 참조하세요.

Azure 기계 학습을 처음 사용하며 시작하기 위해 간단한 실험을 생성하는 방법에 대한 도움이 필요한 경우, Azure 기계 학습 스튜디오의 [Azure 기계 학습 스튜디오에서 간단한 실험 만들기](machine-learning-create-experiment.md)를 참조하세요.

##분류
분류 문제의 하위 범주는 다음 두 가지가 있습니다.

* 2클래스에만 문제가 있음(2클래스 또는 이진 분류)
* 세 개 이상의 클래스에 문제가 있음(다중 클래스 분류)

Azure 기계 학습에서는 서로 다른 모델에서 이러한 유형의 분류를 각각 다룹니다. 그러나 예측 결과를 해석하는 방법은 비슷합니다. 먼저 2클래스 분류 문제에 대해 설명한 다음 다중 클래스 분류 문제에 대해 설명합니다.

###2클래스 분류
**예제 실험**

2클래스 분류 문제의 예로는 붓꽃 분류가 있습니다. 작업은 붓꽃의 기능에 따라 붓꽃을 분류하는 것입니다. Azure Machine Learning에서 제공하는 붓꽃 데이터 집합은 널리 사용되는 [붓꽃 데이터 집합](http://en.wikipedia.org/wiki/Iris_flower_data_set)의 하위 집합입니다. 이 집합에는 꽃의 종류가 두 가지(클래스 0과 1)뿐입니다. 각 꽃에는 네 가지 특징이 있습니다(꽃받침 길이, 꽃받침 너비, 꽃잎 길이 및 꽃잎 너비).

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

그림 1 붓꽃 2클래스 분류 문제 실험

그림 1에 표시된 대로 이 문제를 해결하기 위해 실험을 수행했습니다. 2클래스의 향상된 의사 결정 트리 모델이 학습되어 점수가 지정되었습니다. 이제 [모델 점수 매기기][score-model] 모듈의 출력 부분을 클릭하고 표시된 메뉴에서 **시각화**를 클릭하여 [모델 점수 매기기][score-model] 모듈의 예측 결과를 시각화할 수 있습니다. 그러면 그림 2에 표시된 대로 점수 매기기 결과가 표시됩니다.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

그림 2 2클래스 분류의 모델 점수 매기기 결과 시각화

**결과 해석**

결과 테이블에 6 개의 열이 있습니다. 왼쪽에 있는 네 개의 열이 네 개의 기능입니다. 오른쪽 두 개의 열인 점수가 매겨진 레이블 및 점수가 매겨진 확률이 예측 결과입니다. 점수가 매겨진 확률 열에는 꽃이 양의 클래스(클래스 1)에 속할 확률이 표시됩니다. 예를 들어, 열의 첫 번째 숫자 0.028571은 첫 번째 꽃이 클래스 1에 속할 확률이 0.028571임을 나타냅니다. 점수가 매겨진 레이블 열에는 각 꽃의 예측 클래스가 표시됩니다. 이 값은 점수가 매겨진 확률 열을 기반으로 합니다. 꽃의 점수가 매겨진 확률이 0.5보다 크면 클래스 1로 예측되며, 그렇지 않으면 클래스 0으로 예측됩니다.

**웹 서비스 게시**

예측 결과를 철저히 파악하고 판단한 후에 웹 서비스에 실험을 게시하면, 다양한 응용 프로그램에 배포하여 모든 새 붓꽃에 대한 클래스 예측값을 얻기 위해 호출할 수 있습니다. 학습 실험을 점수 매기기 실험으로 변경하여 웹 서비스로 게시하는 방법에 대한 절차는 [Azure 기계 학습 웹 서비스 게시](machine-learning-walkthrough-5-publish-web-service.md)를 참조하세요. 이 절차에 따르면 그림 3에 표시된 대로 점수 매기기 실험이 제공됩니다.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

그림 3 붓꽃 2클래스 분류 문제 점수 매기기 실험

이제 웹 서비스의 입력 및 출력을 설정해야 합니다. 입력은 붓꽃 기능 입력인 [모델 점수 매기기][score-model]의 오른쪽 입력 부분입니다. 출력은 관심 있는 사항이 예측 클래스(점수가 매겨진 레이블)인지 점수가 매겨진 확률인지 아니면 둘 다인지에 따라 선택합니다. 여기에서는 둘 다에 관심이 있다고 가정합니다. 원하는 출력 열을 선택하기 위해 [데이터 집합의 열 선택][select-columns] 모듈을 사용해야 합니다. [데이터 집합의 열 선택][select-columns] 모듈을 클릭하고, 오른쪽 패널에서 **열 선택기 시작**을 클릭한 다음 **점수가 매겨진 레이블** 및 **점수가 매겨진 확률**을 선택합니다. [데이터 집합의 열 선택][select-columns] 모듈의 출력 포트를 설정하고 다시 실행하고 나면, 맨 아래에 있는 **웹 서비스 게시** 단추를 클릭하여 점수 매기기 실험을 웹 서비스로 게시할 준비가 되어야 합니다. 마지막 실험은 그림 4와 같이 표시됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

그림 4 붓꽃 2클래스 분류 문제의 마지막 점수 매기기 실험

웹 서비스를 실행하고 테스트 인스턴스의 기능 값을 입력하고 나면 반환된 결과에서 두 숫자를 반환합니다. 첫 번째 숫자는 점수가 매겨진 레이블이고 두 번째는 점수가 매겨진 확률입니다. 이 꽃은 확률이 0.9655인 클래스 1로 예측됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

그림 5 붓꽃 2클래스 분류의 웹 서비스 결과

###다중 클래스 분류
**예제 실험**

이 실험에서는 다중 클래스 분류의 예로 문자 인식 작업을 수행합니다. 필기 이미지에서 추출된 필기 특성 값이 있다는 전제 하에 분류자를 통해 특정 문자(클래스)를 예측합니다. 학습 데이터에 필기 문자 이미지에서 추출된 16개의 기능이 있습니다. 26자가 26개의 클래스를 구성합니다. 그림 6에 표시된 대로 문자 인식을 위해 다중 클래스 분류 모델을 설정하고 테스트 데이터 집합에 설정된 동일한 기능에 대한 예측을 수행하도록 실험이 설정되었습니다.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

그림 6 문자 인식 다중 클래스 분류 문제 실험

[모델 점수 매기기][score-model] 모듈의 출력 부분을 마우스 오른쪽 단추로 클릭/왼쪽 단추로 클릭한 다음 **시각화**를 클릭하여 [모델 점수 매기기][score-model]의 결과를 시각화하면 그림 7에서와 같이 창이 표시되어야 합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

그림 7 다중 클래스 분류에서 점수 매기기 모델 결과 시각화

**결과 해석**

왼쪽에 있는 16개의 열이 테스트 집합의 기능 값을 나타냅니다. 클래스 “XX”의 점수가 매겨진 확률이라고 이름이 지정된 열은 2클래스 경우의 점수가 매겨진 확률 열과 같습니다. 해당 항목이 특정 클래스에 속할 확률을 보여줍니다. 예를 들어, 첫 번째 항목에 “A”인 0.003571 확률과 “B”인 0.000451 확률 등이 있습니다. 마지막 열인 점수가 매겨진 레이블은 2클래스에서의 점수가 매겨진 레이블과 같습니다. 점수가 매겨진 확률이 가장 높은 클래스를 해당 항목의 예측 클래스로 선택합니다. 예를 들어, 첫 번째 항목에서 가장 큰 확률은 “F”(0.916995)이므로 점수가 매겨진 레이블은 “F”입니다.

**웹 서비스 게시**

이때, [데이터 집합의 열 선택][select-columns]을 사용하여 웹 서비스의 출력이 될 열을 선택하지 않고, 각 항목의 점수가 매겨진 레이블과 점수가 매겨진 레이블의 확률을 가져오려고 합니다. 기본 논리는 점수가 매겨진 확률 중에서 가장 큰 확률을 찾는 것입니다. 그러려면 [R 스크립트 실행][execute-r-script] 모듈을 사용해야 합니다. R 코드는 그림 8에 표시되고 실험은 그림 9와 같습니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

그림 8 점수가 매겨진 레이블 및 레이블의 연관된 확률을 추출하는 R 코드
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

그림 9 문자 인식 다중 클래스 분류 문제의 마지막 점수 매기기 실험

웹 서비스를 게시하고 실행한 다음 입력 기능 값을 입력하고 나면 그림 10과 같은 결과가 반환됩니다. 16개의 기능이 추출된 이 필기 문자는 확률이 0.9715인 “T”로 예측됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

그림 10 붓꽃 2클래스 분류의 웹 서비스 결과

##회귀

회귀 문제는 분류 문제와 다릅니다. 분류 문제에서는 붓꽃이 속한 클래스와 같이 개별 클래스를 예측하려고 합니다. 하지만 회귀 문제에서는 다음 예에서 볼 수 있듯이, 자동차 가격과 같은 연속 변수에 대해 예측하려고 합니다.

**예제 실험**

회귀 예제로 자동차 가격 예측을 사용합니다. 제조사, 연료 유형, 차체 유형, 구동률 등을 포함하는 특징에 따라 자동차의 가격을 예측하려고 합니다. 이 실험은 그림 11에 표시됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

그림 11 자동차 가격 회귀 문제 실험

[모델 점수 매기기][score-model] 모듈을 시각화하면 결과는 그림 12와 비슷합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

그림 12 자동차 가격 예측 문제의 점수 매기기 결과 시각화

**결과 해석**

이 점수 매기기 결과에서 점수가 매겨진 레이블이 결과 열입니다. 숫자는 각 자동차의 예상 가격입니다.

**웹 서비스 게시**

웹 서비스에 회귀 실험을 게시한 다음 2클래스 분류 사용 사례에서와 동일한 방법으로 자동차 가격 예측을 호출할 수 있습니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

그림 13 자동차 가격 회귀 문제의 점수 매기기 실험

웹 서비스를 실행하면 반환된 결과는 그림 14와 비슷합니다. 이 자동차의 예상 가격은 15085.52입니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

그림 14 자동차 가격 회귀 문제의 웹 서비스 결과

##클러스터링

**예제 실험**

다시 붓꽃 데이터 집합을 사용하여 클러스터링 실험을 작성하겠습니다. 여기에서는 특징만 보유하고 클러스터링에 사용할 수 있도록 데이터 집합의 클래스 레이블을 필터링합니다. 이 붓꽃 사용 사례에서는 학습 프로세스 중에 클러스터의 수를 2로 지정하겠습니다. 즉, 꽃을 2클래스로 클러스터링합니다. 실험은 그림 15에 표시됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

그림 15 붓꽃 클러스터링 문제 실험

클러스터링은 학습 데이터 집합에 자체 실측 자료가 없다는 점에서 분류와 다릅니다. 대신 학습 데이터 집합 인스턴스를 개별 클러스터로 그룹화하는 방법에 관심이 있습니다. 학습 프로세스 중에 모델에서 해당 특징 사이의 차이점을 학습하여 항목의 레이블을 지정합니다. 그런 다음 학습된 모델을 사용하여 나중에 항목을 분류할 수 있습니다. 클러스터링 문제에서 결과의 두 부분에 관심이 있습니다. 첫 번째 부분은 학습 데이터 집합에 레이블을 지정하는 방법이고, 다른 부분은 학습된 모델을 통해 새 데이터 집합을 분류하는 방법입니다.

결과의 첫 번째 부분은 [클러스터링 모델 학습][train-clustering-model] 모듈의 왼쪽 출력 부분을 클릭하고 **시각화**를 클릭하여 시각화할 수 있습니다. 시각화 창은 그림 16에 표시되어 있습니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

그림 16 학습 데이터 집합의 클러스터링 결과 시각화

결과의 번째 부분, 즉 학습된 클러스터링 모델로 새 항목 클러스터링은 그림 17에 표시되어 있습니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

그림 17 새 데이터 집합에 대한 클러스터링 결과 시각화

**결과 해석**

두 부분의 결과가 서로 다른 실험 단계에서 기인하지만 결과가 정확하게 일치하며 동일한 방식으로 해석됩니다. 처음 네 개의 열이 특징입니다. 마지막 열 할당이 예측 결과입니다. 같은 값에 할당된 항목은 같은 클러스터에 있는 것으로 예측됩니다. 즉, 일정한 방식으로 유사성을 공유합니다(이 실험에서는 기본 유클리드 거리 메트릭을 사용함). 클러스터의 수를 2로 지정한 점을 상기하세요. 따라서 할당 열에는 항목의 레이블이 0이거나 1이 됩니다.

**웹 서비스 게시**

웹 서비스에 클러스터링 실험을 게시한 다음 2클래스 분류 사용 사례에서와 동일한 방법으로 클러스터링 예측을 호출할 수 있습니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

그림 18 붓꽃 클러스터링 문제 점수 매기기 실험

웹 서비스를 실행하면 반환된 결과는 그림 19와 비슷합니다. 이 꽃은 클러스터 0에 있는 것으로 예측됩니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

그림 19 붓꽃 2클래스 분류의 웹 서비스 결과

##추천 시스템
**예제 실험**

추천 시스템의 경우, 음식점 추천 문제를 예로 사용합니다. 고객의 등급 지정 내역을 기반으로 고객에게 음식점을 추천합니다. 입력 데이터는 다음 세 부분으로 구성됩니다.

* 고객의 음식점 등급
* 고객 특징 데이터
* 음식점 특징 데이터

Azure 기계 학습의 기본 제공 [매치박스 추천 학습][train-matchbox-recommender] 모듈을 사용하여 여러 가지를 수행할 수 있습니다.

- 지정된 사용자와 항목의 등급 예측
- 지정된 사용자를 위한 항목 추천
- 지정된 사용자와 관련된 사용자 찾기
- 지정된 항목과 관련된 항목 찾기

오른쪽 패널의 **추천 예측 유형** 메뉴에 있는 네 개의 옵션에서 선택하여 수행할 사항을 선택할 수 있습니다. 여기에서는 네 개의 시나리오를 모두 단계별로 수행합니다. 추천 시스템의 일반적인 Azure 기계 학습 실험은 그림 20과 비슷합니다. 추천 시스템 모듈 사용 방법에 대한 자세한 내용은 [매치박스 추천 학습][train-matchbox-recommender] 및 [매치박스 추천 점수 매기기][score-matchbox-recommender]의 도움말 페이지를 참조하세요.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

그림 20 추천 시스템 실험

**결과 해석**

*특정된 사용자와 항목의 등급 예측*

**추천 예측 유형** 메뉴에서 등급 예측을 선택하여 추천 시스템에서 지정된 사용자와 항목의 등급을 예측하도록 요청합니다. [매치박스 추천 점수 매기기][score-matchbox-recommender] 출력의 시각화는 그림 21과 비슷합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

그림 21 추천 시스템 - 등급 예측의 점수 매기기 결과 시각화

세 개의 열이 있습니다. 처음 두 열은 입력된 데이터에서 제공하는 사용자-항목 쌍입니다. 세 번째 열은 특정 항목에 대한 사용자의 예측 등급입니다. 예를 들어, 첫 번째 행에서 U1048 고객은 135026 음식점의 등급을 2로 지정할 것으로 예측됩니다.

*지정된 사용자에게 항목 추천*

**추천 예측 유형** 메뉴에서 **항목 추천**을 선택하여 추천 시스템에서 지정된 사용자에게 항목을 추천하도록 요청합니다. 이 시나리오에서는 추천 항목 선택이라는 매개 변수를 하나 더 선택해야 합니다. **등급이 지정된 항목에서(모델 평가용)** 옵션은 주로 학습 프로세스 중에 모델 평가용으로 사용됩니다. 이 예측 단계에서는 **모든 항목에서**를 선택합니다. [매치박스 추천 점수 매기기][score-matchbox-recommender] 출력의 시각화는 그림 22와 비슷합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

그림 22 추천 시스템 - 항목 추천의 점수 매기기 결과 시각화

6개의 열이 있습니다. 첫 번째 열은 입력 데이터에서 제공한 항목을 추천받도록 지정된 사용자 ID를 나타냅니다. 나머지 다섯 개의 열은 사용자에게 추천될 항목을 나타내며 관련성에 따라 내림차순으로 정렬됩니다. 예를 들어, 첫 번째 행에서 U1048 고객에게 가장 많이 추천된 음식점은 134986이고, 다음으로 135018 134975 135021 및 132862 순으로 추천되었습니다.

*지정된 사용자와 관련된 사용자 찾기*

“추천 예측 유형” 메뉴에서 관련 사용자를 선택하여 추천 시스템에서 지정된 사용자와 관련된 사용자를 찾도록 요청합니다. 관련된 사용자는 기본 설정이 비슷한 사용자입니다. 이 시나리오에서는 관련된 사용자 선택이라는 매개 변수를 하나 더 선택해야 합니다. “등급을 지정한 사용자로부터(모델 평가용)” 옵션은 주로 학습 프로세스 중에 모델 평가용으로 사용됩니다. 이 예측 단계에서는 “모든 사용자로부터”를 선택합니다. [매치박스 추천 점수 매기기][score-matchbox-recommender] 출력의 시각화는 그림 23과 비슷합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

그림 23 추천 시스템 - 관련 사용자의 점수 매기기 결과 시각화

6개의 열이 있습니다. 첫 번째 열은 입력 데이터에서 제공된 관련 사용자를 찾는 대상이 되는 지정된 사용자 ID입니다. 나머지 다섯 개의 열에는 사용자의 예측 관련 사용자가 저장되어 있고, 관련성에 따라 내림차순으로 정렬됩니다. 예를 들어, 첫 번째 행에서 U1048 고객과 가장 관련성이 높은 고객은 U1051이고, 그 뒤를 이어 U1066 U1044 U1017 및 U1072 순입니다.

**지정된 항목과 관련된 항목 찾기**

**추천 예측 유형** 메뉴에서 **관련 항목**을 선택하여 추천 시스템에서 지정된 항목과 관련된 항목을 찾도록 요청합니다. 관련 항목은 동일한 사용자가 좋아할 가능성이 가장 큰 항목입니다. 이 시나리오에서는 관련된 항목 선택이라는 매개 변수를 하나 더 선택해야 합니다. **등급이 지정된 항목에서(모델 평가용)** 옵션은 주로 학습 프로세스 중에 모델 평가용으로 사용됩니다. 이 예측 단계에서는 **모든 항목에서**를 선택합니다. [매치박스 추천 점수 매기기][score-matchbox-recommender] 출력의 시각화는 그림 24와 비슷합니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

그림 24 추천 시스템 - 관련 항목의 점수 결과 시각화

6개의 열이 있습니다. 첫 번째 열은 입력 데이터에서 제공한 관련 항목을 찾을 지정된 사용자 ID를 나타냅니다. 나머지 다섯 개의 열에는 항목의 예측 관련 항목이 저장되어 있고, 관련성에 따라 내림차순으로 정렬됩니다. 예를 들어, 첫 번째 행에서 135026 항목의 가장 관련성이 높은 항목은 135074이고, 그 다음은 135035 132875 135055 및 134992 순입니다. 웹 서비스 게시

이러한 실험을 웹 서비스로 게시하여 예측을 얻는 프로세스는 네 개의 각 시나리오와 비슷합니다. 두 번째 시나리오에서는 지정된 사용자에게 항목을 추천합니다. 다른 세 가지와 동일한 절차를 따르면 됩니다.

학습된 추천 시스템을 학습된 모델로 저장하고 요청된 대로 입력 데이터를 단일 사용자 ID 열로 필터링하면, 그림 25에서와 같이 실험을 연결하여 웹 서비스로 게시할 수 있습니다.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
그림 25 음식점 추천 문제의 점수 매기기 실험

웹 서비스를 실행하면 반환된 결과는 그림 14와 비슷합니다. U1048 사용자에게 추천된 5개의 음식점은 134986, 135018, 134975, 135021 및 132862입니다.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

그림 26 음식점 추천 문제의 웹 서비스 결과


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=AcomDC_0914_2016-->