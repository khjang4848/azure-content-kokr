<properties
	pageTitle="빠른 시작 가이드: Machine Learning Recommendations API | Microsoft Azure"
	description="Azure Machine Learning 추천 - 빠른 시작 가이드"
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
	ms.date="09/20/2016"
	ms.author="luisca"/>

#  빌드 형식 및 모델 품질 #

<a name="TypeofBuilds"></a>
## 지원되는 빌드 형식 ##

Recommendations API는 현재 *추천*과 *FBT*의 두 가지 빌드 형식을 지원합니다. 각각은 다양한 알고리즘을 사용하여 빌드되며 서로 다른 강점이 있습니다. 이 문서에서는 각 빌드 및 생성된 모델의 품질을 비교하는 기법에 대해 설명합니다.

[빠른 시작 가이드](cognitive-services-recommendations-quick-start.md)를 아직 완료하지 않은 경우, 먼저 완료하는 것이 좋습니다.

<a name="RecommendationBuild"></a>
### 추천 빌드 형식 ###

추천 빌드 형식은 특성 추출 방식(Matrix Factorization)을 사용하여 추천 서비스를 제공합니다. 각 항목을 설명하는 트랜잭션에 기반하여 [잠재적 특성](https://en.wikipedia.org/wiki/Latent_variable) 벡터를 생성한 다음 이러한 잠재적 벡터를 사용하여 유사한 항목을 비교합니다.

전자 제품 상점에서 구매한 이력을 바탕으로 모델을 학습시키고 Lumia 650 휴대폰을 해당 모델에 입력으로 제공한다면, 이 모델은 Lumia 650 휴대폰을 구입할 가능성이 있는 사용자가 구매하기 쉬운 일련의 상품을 반환하게 됩니다. 이러한 상품이 상호 보완적인 관계는 아닐 수 있습니다. 이 예제에서 Lumia 650을 좋아하는 사용자가 다른 휴대폰을 좋아할 수도 있기 때문에 다른 휴대폰이 반환될 가능성 역시 있습니다.

추천 빌드에는 유용한 기능이 두 가지 있습니다.

***콜드 항목* 권장 지원**

눈에 띄는 사용 현황이 없는 항목을 콜드 항목이라고 합니다. 예를 들어 이전에 판매한 적이 없는 휴대폰이 배송된 경우, 시스템에서는 트랜잭션만으로 이 제품에 대한 추천 여부를 유추할 수 없습니다. 따라서 시스템은 제품 자체에 대한 정보로부터 학습을 해야 합니다.

콜드 항목 권장 기능을 사용하려면 카탈로그에 각 항목에 대한 특성 정보를 제공해야 합니다. 다음은 카탈로그에 표시될 수 있는 처음 몇 줄이 나와 있습니다(키=해당 특성에 대한 값 형식).

>6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft

>73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature

>WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth

또한 다음과 같이 빌드 매개 변수를 설정해야 합니다.

| 빌드 매개 변수 | 참고 사항
|------------------     |-----------
|*useFeaturesInModel* | **true**로 설정합니다. 특성을 사용하여 추천 모델을 개선할 수 있는지 여부를 나타냅니다.
|*allowColdItemPlacement* | **true**로 설정합니다. 추천 시 특성의 유사성을 통해 콜드 항목을 푸시해야 하는지 여부를 나타냅니다.
| *modelingFeatureList* | 추천을 개선하기 위해 추천 빌드에서 사용할 특성 이름 목록으로, 쉼표로 구분됩니다. 예를 들어 앞의 예제에서는 "Language,Storage"가 여기에 해당합니다.

**사용자 추천 지원**

추천 빌드에서 [사용자 추천](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)이 지원됩니다. 사용자의 트랜잭션 기록을 바탕으로 해당 사용자에게 맞춤형 항목을 추천할 수 있습니다. 사용자 추천의 경우 해당 사용자의 사용자 ID 또는 최근 트랜잭션 기록을 제공할 수 있습니다.

사용자 추천을 적용할 수 있는 전형적인 예를 한 가지 들어보자면, 시작 페이지에서 로그인하는 경우가 있습니다. 여기에서 특정 사용자에게 적용되는 콘텐츠를 홍보할 수 있습니다.

또한 사용자가 체크아웃하려고 할 때 추천 빌드 형식을 적용할 수도 있습니다. 이 때는 사용자가 구매하고자 하는 상품 목록이 있으므로 현재 장바구니에 들어있는 항목을 바탕으로 추천할 수 있습니다.

#### 추천 빌드 매개 변수

| 이름 | 	설명 |	 형식, <br> 유효한 값, <br>(기본값)
|-------|-------------------|------------------
| *NumberOfModelIterations* |	모델에서 수행하는 반복 횟수에는 전반적인 계산 시간과 모델 정확도가 반영됩니다. 숫자가 높을수록 모델의 정확도는 높지만 계산 시간이 오래 걸립니다. |	 정수, <br> 10 ~ 50<br>(40)
| *NumberOfModelDimensions* |	차원 수는 모델이 데이터 내에서 찾으려는 특성의 수와 관련되어 있습니다. 차원 수를 늘리면 결과를 더 작은 클러스터로 훨씬 더 미세하게 조정할 수 있습니다. 그러나 차원 수가 너무 많으면 항목 간의 상관 관계를 찾지 못하게 됩니다. |	정수, <br> 10 ~ 40 <br>(20) |
| *ItemCutOffLowerBound* |	모델에서 고려되기 위해 항목이 포함되어야 하는 사용 포인트의 최소 수를 정의합니다. |		정수, <br> 2 이상<br>(2) |
| *ItemCutOffUpperBound* | 	모델에서 고려되기 위해 항목이 포함되어야 하는 사용 포인트의 최대 수를 정의합니다. | 정수, <br>2 이상<br>(2147483647) |
|*UserCutOffLowerBound* |	모델에서 고려되기 위해 사용자가 수행해야 하는 트랜잭션의 최소 수를 정의합니다. |	정수, <br> 2 이상<br>(2)
| *UserCutOffUpperBound* |	모델에서 고려되기 위해 사용자가 수행해야 하는 트랜잭션의 최대 수를 정의합니다. |	정수, <br>2 이상<br>(2147483647)|
| *UseFeaturesInModel* |	특성을 사용하여 추천 모델을 개선할 수 있는지 여부를 나타냅니다. | 	 부울<br> 기본값: True
|*ModelingFeatureList* |	추천을 개선하기 위해 추천 빌드에서 사용할 특성 이름 목록으로, 쉼표로 구분됩니다. 이 목록은 특성의 중요도에 따라 달라집니다. |	문자열, 최대 512자
| *AllowColdItemPlacement* |	추천 시 특성의 유사성을 통해 콜드 항목을 푸시해야 하는지 여부를 나타냅니다. | 부울<br> 기본값: False
| *EnableFeatureCorrelation* | 추론에서 특성을 사용할 수 있는지 여부를 나타냅니다. |	부울<br> 기본값: False
| *ReasoningFeatureList* |	추천 설명 등과 같은 추론문에 사용할 특성 이름 목록으로, 쉼표로 구분됩니다. 이 목록은 고객에게 중요한 특성에 따라 달라집니다. | 문자열, 최대 512자
| *EnableU2I* |	U2I(사용자 중심) 추천이라고도 하는 개인 맞춤형 추천을 사용하도록 설정합니다. | 부울<br> 기본값: True
|*EnableModelingInsights* |	모델링 정보(즉, 정확도 및 다양성 메트릭)를 수집하기 위해 오프라인 평가를 수행할지 여부를 정의합니다. true로 설정하면 데이터의 하위 집합은 모델을 테스트하는 데 사용해야 하므로 학습에 사용되지 않습니다. [오프라인 평가](#OfflineEvaluation)에 대해 자세히 알아보세요. | 부울<br> 기본값: False
| *SplitterStrategy* | 모델링 정보 활용이 *true*로 설정된 경우 평가 목적으로 데이터를 분할하는 방법입니다. | 문자열, *RandomSplitter* 또는 *LastEventSplitter* <br>기본값: RandomSplitter


<a name="FBTBuild"></a>
### FBT 빌드 형식 ###

FBT(자주 함께 구매하는 항목) 빌드는 둘 또는 세 개의 서로 다른 제품에 대한 구매가 함께 이루어지는 횟수를 계산하는 분석을 수행합니다. 그 다음, 유사성 함수(**co-occurrences**, **Jaccard**, **lift**)에 따라 해당 집합을 정렬합니다.

**Jaccard** 및 **lift**를 공동 발생을 정규화하는 방법으로 생각하십시오. 즉, 해당 항목은 시드 항목과 함께 구매한 경우에만 반환됩니다.

Lumia 650 휴대폰 예에서, Lumia 650 휴대폰과 동일한 세션에서 휴대폰 X를 구매한 경우에만 휴대폰 X가 반환됩니다. 하지만 이 제품이 반환되지 않을 가능성도 있기 때문에 Lumia 650을 보완하는 상품으로 Lumia 650용 화면 보호기 또는 전원 어댑터가 반환될 수도 있습니다.

현재, 두 상품이 동일한 사용자 ID와 타임스탬프를 가진 트랜잭션에서 발생하는 경우 두 상품은 동일한 세션에서 구매하는 것으로 간주됩니다.

FBT 빌드에서는 콜드 항목을 지원하지 않는데, 정의에 따라 두 상품이 동일한 트랜잭션 내에서 구매되는 것으로 예상할 수 있기 때문입니다. FBT 빌드에서 항목 집합(3개)을 반환할 수 있지만 입력으로 단일 시드 항목을 받아들이므로 개인 맞춤형 추천은 지원되지 않습니다.


#### FBT 빌드 매개 변수

| 이름 | 	설명 |		형식, <br> 유효한 값, <br>(기본값)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | 모델의 보수적인 정도로, 모델링 시 고려할 항목의 공동 발생 횟수입니다. | 정수, <br> 3 ~ 50 <br>(6)
| *FbtMaxItemSetSize* | FBT 집합의 항목 수를 제한합니다.| 정수, <br> 2 ~ 3 <br>(2)
| *FbtMinimalScore* | FBT 집합이 반환된 결과에 포함되어야 할 최소 점수입니다. 높을수록 좋습니다. | 이중 <br> 0 이상 <br>(0)
| *FbtSimilarityFunction* | 빌드에서 사용할 유사성 함수를 정의합니다. **Lift**는 우연성을 우위에 두고, **co-occurrence**는 예측 가능성을 우위에 두며, **Jaccard**는 이 둘을 적절히 절충합니다. | 문자열, <br> <i>cooccurrence, lift, jaccard</i><br> 기본값: <i>jaccard</i>

<a name="SelectBuild"></a>
## 빌드 평가 및 선택 ##

이 지침은 추천 빌드 또는 FBT 빌드 중 어느 것을 사용해야 할지 결정하는 데 도움이 될 수 있지만 둘 중 하나를 사용할 수 있는 경우에 대한 확실한 대답을 주지는 않습니다. 또한 FBT 빌드 형식을 사용하려는 경우에도 유사성 함수로 **Jaccard** 또는 **lift**를 선택할 수 있습니다.

두 가지 빌드 중에서 최선의 선택을 하는 방법은 실제 환경에서 둘을 테스트(온라인 평가)하고 다른 빌드에 대한 전환율을 추적하는 것입니다. 전환율은 추천 클릭, 표시된 추천에서 실제 구입한 수 또는 여러 추천이 표시된 경우의 실제 구입액을 바탕으로 측정할 수 있습니다. 비즈니스 목표에 따른 전환율 메트릭을 선택할 수도 있습니다.

경우에 따라서는 프로덕션에 적용하기 전에 오프라인으로 모델을 평가할 수 있습니다. 오프라인 평가는 온라인 평가를 대체하는 것이 아니며 하나의 메트릭으로서 제공될 수 있습니다.

<a name="OfflineEvaluation"></a>
## 오프라인 평가  ##

오프라인 평가의 목적은 추천 정밀도(추천 항목 중 하나를 구입할 사용자 수) 및 다양성(추천 항목 수)을 예측하기 위한 것입니다. 정밀도 및 다양성 메트릭 평가의 일환으로, 시스템은 사용자의 샘플을 찾은 다음, 해당 사용자의 트랜잭션을 학습 데이터 집합과 테스트 데이터 집합의 두 그룹으로 분할합니다.

> [AZURE.NOTE] 오프라인 메트릭을 사용하려면 사용 현황 데이터에 타임스탬프가 있어야 합니다. 학습 및 테스트 데이터 집합 간에 사용 현황을 정확하게 분할하기 위해서는 시간 데이터가 필요합니다.

> 또한 오프라인 평가에서는 작은 사용 파일에 대한 결과가 산출되지 않을 수 있습니다. 완벽한 평가를 위해서는 테스트 데이터 집합에 최소 1,000개의 사용 포인트가 있어야 합니다.

<a name="Precision"></a>
### 정밀도-K ###
다음 표는 정밀도-K 오프라인 평가 결과를 나타냅니다.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|백분율 |	13\.75 |	18\.04 | 21 |	24\.31 |	26\.61
|테스트에 참가한 사용자 |	10,000 |	10,000 |	10,000 |	10,000 |	10,000
|고려 대상 사용자 |	10,000 |	10,000 |	10,000 |	10,000 |	10,000
|비고려 대상 사용자 |	0 |	0 |	0 |	0 |	0

#### K
위의 표에서 *K*는 고객에게 표시된 추천 수를 나타냅니다. 이 표를 읽는 방법은 "테스트 기간 중 고객에게 추천 항목이 하나만 표시된 경우, 사용자 중 13.75명만 해당 추천 항목을 구매했습니다"와 같습니다. 이 문장은 모델이 구매 데이터를 사용하여 학습되었다는 가정을 바탕으로 합니다. 이 표는 "1에서의 정밀도는 13.75입니다"와 같이 읽을 수도 있습니다.

고객에게 많은 항목이 표시될수록 추천 항목을 구매할 가능성이 높아지는 것을 알 수 있습니다. 위의 실험에서 5개 항목을 추천했을 때의 확률은 거의 두 배인 26.61%입니다.

#### 백분율
표시된 *K*개의 추천 항목 중 하나 이상과 상호 작용한 사용자의 비율입니다. 백분율은 하나 이상의 추천 항목과 상호 작용한 사용자 수를 고려 대상 사용자의 총 수로 나누어 계산합니다. 자세한 내용은 고려 대상 사용자를 참조하세요.

#### 테스트에 참가한 사용자
이 행의 데이터는 테스트 데이터 집합에 있는 사용자의 총 수를 나타냅니다.

#### 고려 대상 사용자
사용자는 시스템이 학습 데이터 집합을 사용하여 생성된 모델을 기반으로 하는 최소한 *K*개 이상의 항목을 추천한 경우에만 고려 대상이 됩니다.

#### 비고려 대상 사용자
이 행의 데이터는 고려 대상에서 제외된 모든 사용자를 나타냅니다. 적어도 *K*개 이상의 추천 항목을 받지 못한 사용자입니다.

비고려 대상 사용자 = 테스트에 참가한 사용자 – 고려 대상 사용자

<a name="Diversity"></a>
### 다양성 ###
다양성 메트릭은 추천된 항목의 유형을 측정합니다. 다음 표에는 다양성 오프라인 평가의 결과가 나와 있습니다.

|백분위수 버킷 |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|백분율 | 34\.258 | 55\.127| 10\.615


총 추천 항목: 100,000

고유한 추천 항목: 954

#### 백분위수 버킷
각 백분위수 버킷은 범위(0에서 100 사이의 최소값과 최대값)로 표시됩니다. 100에 가까운 항목이 가장 인기 있는 항목이고 0에 가까운 항목은 가장 인기 없는 항목입니다. 예를 들어 99-100 백분위수 버킷의 백분율 값이 10.6이면 10.6%의 추천에서 상위 1%의 가장 인기 있는 항목만 반환되었음을 의미합니다. 백분위수 버킷 최소값이 포함되며 최대값은 100 이외의 다른 값은 제외됩니다.
#### 고유한 추천 항목
고유한 추천 항목 메트릭은 평가에 대해 반환된 고유 항목 수를 나타냅니다.
#### 총 추천 항목
총 추천 항목 메트릭은 추천된 항목 수를 나타냅니다. 일부 항목이 중복될 수 있습니다.

<a name="ImplementingEvaluation"></a>
### 오프라인 평가 메트릭 ###
정밀도 및 다양성 오프라인 메트릭은 사용할 빌드를 선택할 때 유용할 수 있습니다. 빌드 시, 해당 FBT 또는 추천 빌드 매개 변수의 일부로 다음을 수행합니다.

-	*enableModelingInsights* 빌드 매개 변수를 **true**로 설정합니다.
-	필요에 따라 *splitterStrategy*(*RandomSplitter* 또는 *LastEventSplitter* 중 하나)를 선택합니다. *RandomSplitter*는 지정된 *randomSplitterParameters* 테스트 백분율 및 임의 초기값을 기준으로 학습 및 테스트 집합의 사용 현황 데이터를 분할합니다. *LastEventSplitter*는 각 사용자에 대한 마지막 트랜잭션을 기반으로 학습 및 테스트 집합의 사용 현황 데이터를 분할합니다.

학습을 위해 데이터의 하위 집합만 사용하는 빌드를 트리거하고 나머지 데이터는 평가 메트릭을 계산하는 데 사용합니다. 빌드가 완료된 후 평가 결과를 가져오려면 해당 *modelId* 및 *buildId*를 전달하는 [빌드 메트릭 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f)를 호출해야 합니다.

 다음은 샘플 평가에 대한 JSON 출력입니다.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0921_2016-->