<properties
	pageTitle="Azure 기계 학습의 기능 엔지니어링 및 선택 | Microsoft Azure"
	description="기능 선택 및 기능 엔지니어링의 목적을 설명하고 기계 학습의 데이터 향상 프로세스에서 수행하는 역할의 예를 제공합니다."
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
	ms.author="zhangya;bradsev" />


# Azure 기계 학습의 기능 엔지니어링 및 선택

이 항목에서는 기계 학습의 데이터 향상 프로세스에서 기능을 엔지니어링하고 기능을 선택하는 목적에 대해 설명합니다. Azure 기계 학습 스튜디오에서 제공하는 예제를 사용하여 이러한 프로세스와 관련된 내용을 설명합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

기계 학습에 사용되는 교육 데이터는 수집된 원시 데이터에서 기능을 선택하거나 추출하여 향상시킬 수 있습니다. 필기 문자의 이미지 분류 방법을 배우는 경우 엔지니어링된 기능의 예로는 원시 비트 분산 데이터에서 구성된 비트 밀도 맵이 있습니다. 이 맵을 사용하면 원시 분산보다 효율적으로 문자의 경계를 찾을 수 있습니다.

기능을 엔지니어링하고 선택하면 데이터에 포함된 주요 정보를 추출하려고 하는 학습 프로세스의 효율성이 증가됩니다. 이러한 모델이 입력 데이터를 정확하게 분류하고 원하는 결과를 더욱 안정적으로 예측하는 기능도 향상됩니다. 컴퓨터를 통해 학습을 다루기가 더욱 쉽도록 기능 엔지니어링 및 선택을 결합할 수도 있습니다. 이 작업은 모델을 보정하거나 학습하는 데 필요한 기능을 향상시키고 해당 수를 줄여 수행합니다. 수학적인 관점에서 보자면 모델을 학습하기 위해 선택한 기능은 데이터의 패턴을 설명한 다음 결과를 성공적으로 예측하는 최소한의 독립 변수 집합입니다.

기능의 엔지니어링 및 선택은 일반적으로 다음 네 단계로 구성된 대규모 프로세스의 한 가지 부분입니다.

* 데이터 수집
* 데이터 향상
* 모델 생성
* 후처리

엔지니어링 및 선택은 기계 학습의 **데이터 향상** 단계입니다. 이 프로세스의 세 가지 요소는 목적에 따라 다음과 같이 구별할 수 있습니다.

* **데이터 사전 처리**: 이 프로세스에서는 수집된 데이터가 정리되어 있으며 일관성이 있는지 확인합니다. 여러 데이터 집합 통합, 누락된 데이터 처리, 일관되지 않은 데이터 처리 및 데이터 유형 변환과 같은 작업이 포함됩니다.
* **기능 엔지니어링** 이 프로세스에서는 데이터의 기존 원시 기능에서 추가 관련 기능을 만들고 학습 알고리즘의 예측 능력을 향상시키려 합니다.
* **선택 기능**: 이 프로세스에서는 학습 문제의 차원 수를 줄이기 위해 원래 데이터 기능의 주요 하위 집합을 선택합니다.

이 항목에서는 데이터 향상 프로세스의 기능 엔지니어링 및 기능 선택 측면만 다룹니다. 데이터 사전 처리 단계에 대한 추가 정보는 [Azure 기계 학습 스튜디오의 사전 처리 데이터](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) 비디오를 참조하세요.


## 데이터에서 기능 만들기 - 기능 엔지니어링

학습 데이터는 각각 기능 집합(열에 저장된 변수 또는 필드)이 있는 예제(행에 저장된 레코드 또는 관찰)로 구성된 행렬로 구성됩니다. 실험 디자인에 지정된 기능은 데이터에서 패턴의 특징을 나타내야 합니다. 많은 원시 데이터 필드를 모델을 학습하는 데 사용하는 선택된 기능 집합에 직접 포함할 수 있지만, 향상된 학습 데이터 집합을 생성하기 위해 원시 데이터의 기능에서 추가(엔지니어링된) 기능을 생성해야 하는 경우가 더욱 많습니다.

모델을 학습할 때 데이터 집합을 향상시키기 위해 작성해야 하는 기능의 유형은 무엇인가요? 학습을 향상시키는 엔지니어링된 기능에서는 데이터에서 패턴을 더욱 잘 구분할 수 있는 정보를 제공합니다. 새 기능에서는 원래 또는 기존 기능 집합에서는 명확하게 캡처하지 못하거나 쉽게 구분되지 않는 추가 정보를 제공해야 합니다. 그러나 이 프로세스는 정교한 작업입니다. 안정되고 생산성이 있는 결정을 내리려면 도메인에 대한 전문 지식이 필요한 경우가 많습니다.

Azure 기계 학습을 시작할 때 스튜디오에 제공된 샘플을 사용하면 이 프로세스를 구체적으로 파악하기가 쉽습니다. 다음은 제공되는 두 가지 예입니다.

* 대상 값이 알려진 감독된 실험에서의 회귀 예제 [자전거 대여 수 예측](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)
* [기능 해싱][feature-hashing]을 사용하는 텍스트 마이닝 분류 예제

### 예 1: 회귀 모델을 위해 시간 기능 추가 ###

Azure Machine Learning Studio의 “자전거 수요 예측" 실험을 사용하여 회귀 작업을 위해 기능을 엔지니어링 하는 방법을 설명해 보겠습니다. 이 실험의 목표는 자전거 수요, 즉 특정 월/일/시간에 자전거 대여 수를 예측하는 것입니다. “자전거 대여 UCI 데이터 집합" 데이터 집합을 원시 입력 데이터로 사용합니다. 이 데이터 집합은 미국, 워싱턴 DC에서 자전거 임대망을 유지 관리하는 Capital Bikeshare 회사의 실제 데이터를 기반으로 합니다. 이 데이터 집합에는 2011년부터 2012년까지, 하루의 특정 시간 대에 자전거 대여 수가 표시되고, 17379 행과 17열이 포함되어 있습니다. 원시 기능 집합에는 날씨 조건(온도/습도/풍속) 및 날의 유형(휴일/주중)이 포함되어 있습니다. 예측할 수 있는 필드는 “cnt”로서, 특정 시간 대의 자전거 대여 수를 나타내는 수이고, 범위는 1 ~ 977입니다.

학습 데이터에 효율적인 기능을 생성한다는 목표를 갖고, 알고리즘은 동일하지만 네 개의 서로 다른 학습 데이터 집합이 있는 네 개의 회귀 모델을 빌드합니다. 네 개의 데이터 집합에서는 동일한 원시 입력 데이터를 표시하지만 기능 집합의 수는 증가합니다. 이러한 기능은 다음 네 가지 범주로 그룹화됩니다.

1. A = 예측 날에 대한 날씨 + 휴일 + 주중 + 주말 기능
2. B = 지난 12시간마다 대여된 자전거 대수
3. C= 지난 12일마다 같은 시간에 대여된 자전거 대수
4. D = 지난 12주마다 같은 시간, 같은 요일에 대여된 자전거 대수

이미 원래 원시 데이터에 있던 기능 집합 A를 제외하고 나머지 세 개의 기능 집합은 기능 엔지니어링 프로세스를 통해 생성됩니다. 기능 집합 B에서는 자전거의 최신 수요를 파악합니다. 기능 집합 C에서는 특정 시간의 자전거 수요를 파악합니다. 기능 집합 D에서는 특정 시간 및 특정 요일에 자전거에 대한 수요를 파악합니다. 네 개의 학습 데이터 집합 각각에는 A, A+B, A+B+C 및 A+B+C+D가 포함되어 있습니다.

Azure 기계 학습 실험에서는 사전 처리된 입력 데이터 집합에서 4개의 분기를 통해 이러한 4개의 학습 데이터 집합을 구성합니다. 가장 왼쪽 분기를 제외한 각 분기에는 [R 스크립트 실행][execute-r-script] 모듈이 포함되어 있습니다. 이 모듈에는 파생 기능 집합(기능 집합 B, C 및 D)이 각각 구성되어 있고, 가져온 데이터 집합에 추가되어 있습니다. 다음 그림에서는 두 번째 왼쪽 분기에서 기능 집합 B를 생성하는 데 사용하는 R 스크립트를 보여줍니다.

![기능 만들기](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

네 가지 모델의 성능 결과를 비교한 내용이 다음 테이블에 요약되어 있습니다. 최상의 결과는 A+B+C 기능으로 표시됩니다. 학습 데이터에 추가 기능 집합이 포함되면 오류 비율이 감소되는 것을 볼 수 있습니다. 따라서 기능 집합 B와 C에서 회귀 작업을 위한 추가 관련 정보를 제공한다는 가정이 검증됩니다. 그러나 D 기능은 오류 비율을 추가적으로 감소시키지 않는 것으로 보입니다.

![결과 비교](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a> 예 2: 텍스트 마이닝에 기능 만들기  

기능 엔지니어링은 문서 분류 및 감성 분석 등의 텍스트 마이닝 관련 작업에 광범위하게 적용됩니다. 예를 들어 문서를 여러 범주로 분류하려는 경우, 일반적으로 한 문서 범주에 포함된 단어/문구가 다른 문서 범주에서 발생할 가능성이 적다고 가정합니다. 즉, 단어/문구 분포 빈도를 통해 서로 다른 문서 범주의 특징을 결정할 수 있습니다. 텍스트 마이닝 응용 프로그램에서는 개별 텍스트 내용이 일반적으로 입력 데이터로 제공되므로, 단어/문구 빈도와 관련된 기능을 생성하려면 기능 엔지니어링 프로세스가 필요합니다.

이 작업을 수행하기 위해 **기능 해싱**이라는 기술을 적용하여 임의의 텍스트 기능을 인덱스로 전환합니다. 각 텍스트 기능(단어/문구)을 특정 인덱스에 연관시키는 대신, 이 메서드에서는 해시 함수를 기능에 적용하고 해시 값을 인덱스로 직접 사용하여 작동합니다.

Azure 기계 학습에는 이러한 단어/문구 기능을 편리하게 생성하는 [기능 해싱][feature-hashing] 모듈이 있습니다. 다음 그림에서는 이 모듈을 사용하는 예를 보여줍니다. 입력 데이터 집합에는 두 개의 열, 즉 1 ~ 5의 서적 등급과 실제 검토 내용이 들어 있습니다. 이 [기능 해싱][feature-hashing] 모듈의 목표는 특정 서적 검토에서 해당하는 단어/문구의 발생 빈도를 표시하는 여러 새 기능을 검색하는 것입니다. 이 모듈을 사용하려면 다음 단계를 완료해야 합니다.

* 먼저 입력 텍스트를 포함하는 열을 선택합니다(이 예에서는 "Col2").
* 둘째, "Hashing bitsize"를 8로 설정합니다. 즉, 2^8=256개의 기능이 생성됩니다. 텍스트의 단어/문구가 256개의 인덱스로 해시됩니다. "Hashing bitsize" 매개 변수의 범위는 1 ~ 31입니다. 이 변수를 더 큰 수로 설정하면 단어/문구가 동일한 인덱스에 해시될 가능성이 적습니다.
* 셋째, "N-grams" 매개 변수를 2로 설정합니다. 그러면 입력 텍스트에서 유니그램(단일 단어마다 하나의 기능) 및 바이그램(인접한 단어 쌍마다 하나의 기능) 발생 빈도를 가져옵니다. 매개 변수 "N-grams”의 범위는 0 ~ 10입니다. 즉, 순차 단어의 최대 수가 기능에 포함됩니다.

!["기능 해싱"모듈](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

다음 그림에서는 이러한 새 기능이 어떻게 표시되는지 보여줍니다.

!["기능 해싱" 예](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## 데이터에서 기능 필터링 - 기능 선택  ##

기능 선택은 분류 또는 회귀 작업과 같은 예측 모델링 작업의 학습 데이터 집합을 생성하는 데 일반적으로 적용되는 프로세스입니다. 최소한의 기능 집합을 사용하여 데이터의 최대 분산 크기를 표시함으로써 차원수를 줄이는 원래 데이터 집합의 하위 집합을 선택하기 위해 사용합니다. 이 기능 하위 집합은 모델을 학습하기 위해 포함되는 기능만 포함합니다. 기능 선택은 두 가지 기본 용도로 사용됩니다.

* 첫째 기능 선택을 수행하면 관련이 없는 중복된 기능이나 고도로 상관된 기능을 제거하여 분류 정확도를 높입니다.
* 둘째, 기능 수가 줄어들어 모델 학습 프로세스의 효율성을 높입니다. 지원 벡터 컴퓨터와 같이 학습하는 데 비용이 많이 드는 학습자에게 특히 중요합니다.

기능 선택에서 모델을 학습시키는 데 사용하는 데이터 집합의 기능 수를 줄이려고 하지만, 일반적으로 “차원 수 감소"라는 용어로 부르지 않습니다. 기능 선택 메서드에서는 원래 기능을 변경하지 않고 데이터에서 원래 기능의 하위 집합을 추출합니다. 차원 수 감소 메서드에서는 원래 기능을 변환하므로 기능을 수정할 수 있는 엔지니어링된 기능을 사용합니다. 차원 수 감소 메서드의 예로는 주성분 분석, 표준 상관 분석 및 특이값 분해가 있습니다.

그중에서도 감독된 컨텍스트에서 가장 널리 적용되는 기능 선택 메서드 범주는 “필터 기반 기능 선택"이라고 합니다. 이러한 메서드에서는 각 기능과 대상 특성 사이의 상관 관계를 평가하여, 각 기능에 점수를 할당하기 위해 통계 측정값을 적용합니다. 그런 다음 특정 기능을 보유하거나 제거하는 임계값을 설정하는 데 사용할 수 있는 점수별로 기능에 순위가 지정됩니다. 이러한 메서드에서 사용하는 통계 측정값의 예로는 Person 상관, 상호 정보 및 카이 제곱 테스트가 있습니다.

Azure 기계 학습 스튜디오에서는 기능 선택에 제공되는 모듈이 있습니다. 다음 그림에 표시된 대로 이러한 모듈에는 [필터 기반 기능 선택][filter-based-feature-selection] 및 [피셔 선형 판별식 분석][fisher-linear-discriminant-analysis]이 포함됩니다.

![기능 선택 예](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


예를 들어, [필터 기반 기능 선택][filter-based-feature-selection] 모듈 사용을 고려하세요. 편의상 이전에 설명된 텍스트 마이닝 예를 계속 사용하겠습니다. [기능 해싱][feature-hashing] 모듈을 통해 256개의 기능 집합을 생성한 후 회귀 모델을 빌드하려고 하며, 응답 변수는 "Col1"이고 1 ~ 5 범위의 서적 검토 등급을 나타낸다고 가정합니다. "기능 점수 매기기 메서드”를 "Pearson 상관"으로 설정하고 "대상 열”은 "Col1"로 설정하며 "원하는 기능 수"는 50으로 설정합니다. 그러면 [필터 기반 기능 선택][filter-based-feature-selection] 모듈에서 대상 특성이 "Col1"과 함께 50개의 기능이 포함된 데이터 집합을 생성합니다. 다음 그림에서는 방금 설명한 입력 매개 변수와 이 실험의 흐름을 보여줍니다.

![기능 선택 예](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

다음 그림에서는 결과 데이터 집합을 보여줍니다. 각 기능과 대상 특성 "Col1" 사이의 Pearson 상관 관계에 따라 기능의 점수를 매깁니다. 점수가 가장 높은 기능이 유지됩니다.

![기능 선택 예](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

선택한 기능의 해당 점수는 다음 그림에 표시되어 있습니다.

![기능 선택 예](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

이 [필터 기반 기능 선택][filter-based-feature-selection] 모듈을 적용하면, 256개의 기능 중 50개가 선택됩니다. 이 50개에는 "Pearson 상관" 점수 매기기 메서드에 따라 대상 변수 “Col1”과 가장 상관 관계가 큰 기능이 있기 때문입니다.

## 결론
기능 엔지니어링과 기능 선택은 기계 학습 모델을 빌드할 때 학습 데이터를 준비하기 위해 일반적으로 수행되는 두 단계입니다. 일반적으로 추가 기능을 생성하기 위해 기능 엔지니어링을 먼저 적용한 다음, 관련이 없는 중복 기능이나 고도로 상관된 기능을 제거하기 위해 기능 선택 단계가 수행됩니다.

기능 엔지니어링이나 기능 선택을 반드시 항상 수행할 필요는 없습니다. 이러한 기능의 필요 여부는 보유하거나 수집한 데이터, 선택한 알고리즘 및 실험 목적에 따라 달라집니다.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

<!---HONumber=AcomDC_0914_2016-->