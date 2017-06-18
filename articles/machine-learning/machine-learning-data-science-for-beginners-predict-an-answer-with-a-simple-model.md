<properties
   pageTitle="단순 모델을 사용하여 답변 예측 - 회귀 모델 | Microsoft Azure"
   description="초급자를 위한 데이터 과학 비디오 4에서는 다이아몬드의 가격을 예측하는 단순 모델을 만드는 방법이 나옵니다. 대상 데이터와 함께 기본 선형 회귀가 포함됩니다."                                  
   keywords="모델 만들기,간단한 모델,간단한 데이터 모델,가격 예측,간단한 회귀 모델"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="cgronlun;brohrer;garye"/>

# 단순 모델을 사용하여 답변 예측

## 비디오 4: 초급자를 위한 데이터 과학 시리즈

초급자를 위한 데이터 과학 비디오 4에서는 다이아몬드의 가격을 예측하는 단순 모델을 만드는 방법을 알아봅니다. 대상 데이터를 사용하여 회귀 모델을 그려볼 것입니다.

시리즈를 최대한 활용하려면 순서대로 시청하는 것이 좋습니다. [비디오 목록으로 이동](#other-videos-in-this-series)

<div class="wa-video-container" data-control="video-container" data-slug="data-science-for-beginners-series-predict-an-answer-with-a-simple-model"> <a title="Data science for beginners: Predict an answer with a simple model" class="wa-video-thumbnail" href="/en-us/documentation/videos/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/" data-control="video" data-event="videopage-clicked-videothumbnail" data-event-property="data-science-for-beginners-series-predict-an-answer-with-a-simple-model" data-caption="" data-ch9="//channel9.msdn.com/Blogs/Windows-Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player/" data-expanding="false"> <img class="thumbnail" alt="Data science for beginners: Predict an answer with a simple model" src="https://sec.ch9.ms/ch9/d060/48bc9b8a-987c-4f60-94fe-fc482da2d060/DataScienceForBeginnersSeriesPredictAnAnswerWithA_960.jpg"> <img class="play-icon" src="/cdn/cvt-a29a0e789afe7f47464f393c011b53f210c511ee5fed2c4c9c0633442f30d6ec/images/icon/VideoPlay.svg"> <span class="metadata"> <span class="date"> <span>06-28-2016</span> <span>07 min, 42 sec</span> </span> </span> </a> </div>

## 이 시리즈의 다른 비디오

*초급자를 위한 데이터 과학*은 다섯 개의 짧은 비디오를 통해 데이터 과학을 간략히 소개합니다.

  * 비디오 1: [데이터 과학으로 답변할 수 있는 5가지 질문](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5분 14초)*
  * 비디오 2: [데이터 과학에 사용할 수 있게 데이터가 준비되었나요?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4분 56초)*
  * 비디오 3: [데이터로 대답할 수 있는 질문하기](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4분 17초)*
  * 비디오 4: 단순 모델을 사용하여 답변 예측
  * 비디오 5: [데이터 과학을 수행하기 위해 다른 사람의 작품 복사](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3분 18초)*

## 비디오 내용: 단순 모델을 사용하여 답변 예측

“초급자를 위한 데이터 과학” 시리즈 중 4번째 비디오를 시작합니다. 여기서는 간단한 모델을 빌드하고 예측을 진행할 것입니다.

*모델*은 데이터에 대한 간소화된 이야기입니다. 무슨 의미인지 보여드리겠습니다.

## 관련성 있고, 정확하고, 연결된 충분한 데이터 수집

다이아몬드를 구입하려고 합니다. 할머니께 물려받은 1.35 캐럿 다이아몬드 반지의 가격이 얼마나 되는지 알고 싶습니다. 노트와 펜을 들고 보석점에 가서 케이스에 들어 있는 모든 다이아몬드의 가격과 캐럿 무게를 적습니다. 첫 번째 다이아몬드는 1.01 캐럿에 $7,366입니다.

매장의 다른 모든 다이아몬드에 대해서도 같은 작업을 수행합니다.

![다이아몬드 데이터 열](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

목록에는 두 개의 열이 있습니다. 각 열은 캐럿 무게와 가격의 두 가지 다른 특성을 포함하며 각 행은 1개의 다이아몬드를 나타내는 단일 데이터 요소를 포함합니다.

실제로 여기에 작은 데이터 집합인 표를 만들었습니다. 이 표가 우리의 품질 기준을 충족한다는 것을 알 수 있습니다.

* 데이터가 **관련 있음** - 무게가 가격이 확실히 연관되어 있습니다.
* **정확함** - 적어놓은 가격을 한 번 더 확인했습니다.
* **연결되어 있음** - 이러한 열에는 공백이 없습니다.
* 또한 여기서 확인된 것처럼 질문에 답변할 수 있는 **충분한** 데이터가 됩니다.

## 정확하게 질문하기

이제 “1.35 캐럿의 다이아몬드를 구입하는 비용은 얼마나 될까요?”와 같이 더 상세하게 질문해보겠습니다.

목록에는 1.35 캐럿 다이아몬드가 없으므로 나머지 데이터를 사용해서 질문에 대한 답변을 얻어야 합니다.

## 기존 데이터 도식화

가장 먼저 수행할 작업은 축에 해당하는 수평선을 그려 무게를 차트로 나타내는 것입니다. 무게 범위는 0~2이므로 해당 범위를 포함하는 선을 그리고 1/2 캐럿 단위로 눈금을 표시합니다.

그런 다음 수직선을 그려 가격을 기록한 후 가로의 무게 축에 연결합니다. 단위는 달러가 됩니다. 이제 좌표축이 설정되었습니다.

![무게 및 가격 축](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

이제 이 데이터를 가져와서 *산점도*로 나타낼 것입니다. 이 방법은 수치 데이터 집합을 시각화하는 데 유용합니다.

첫 번째 데이터 요소의 경우 1.01 캐럿에 해당하는 수직선을 확인합니다. 그런 다음 $7,366에 해당하는 수평선을 확인합니다. 만나는 위치에 점을 그립니다. 이것은 첫 번째 다이아몬드를 나타냅니다.

이제 이 목록의 모든 다이아몬드에 대해 동일한 작업을 수행합니다. 다 끝나면 각 다이아몬드에 하나씩 여러 개의 점이 그려집니다.

![산점도](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## 데이터 요소를 통해 모델 그리기

이제 눈을 가늘게 뜨고 점들을 응시하면 두리뭉실한 선처럼 보입니다. 마커를 사용해서 그 사이로 직선을 그릴 수 있습니다.

선을 그려서 *모델*을 만들었습니다. 이러한 과정은 실제 세계에서 아주 간단한 만화 버전을 만드는 것으로 간주할 수 있습니다. 그 만화는 잘못된 것입니다. 선이 모든 데이터 요소를 지나가지는 않으니까요. 하지만 유용한 단순화 기법입니다.

![선형 회귀 선](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

모든 점이 정확히 선을 지나가지 않아도 괜찮습니다. 데이터 과학자들은 이제 선에 해당하는 모델이 형성되었고 각 점에는 이와 연관된 *노이즈* 또는 *분산*이 있다고 설명합니다. 기본적인 완벽한 관계가 있으며, 또한 노이즈와 불확실성이 적용되는 단호한 실제 세계가 있는 것입니다.

여기서는 *가격은 얼마일까요?*라는 질문에 답변하려고 하기 때문에 이를 *회귀*라고 합니다. 또한 직선을 사용하므로 *선형 회귀*에 해당합니다.

## 모델을 사용하여 해답 찾기

이제 우리에게는 모델이 있고 “1.35 캐럿의 다이아몬드 가격은 얼마나 될까요?”라는 질문을 해보겠습니다.

질문에 대한 답변을 얻기 위해 1.35 캐럿에서 수직선을 그립니다. 모델 선을 지나는 위치에서 달러 축과 만나는 수평선을 그려 확인합니다. 정확히 10,000에 닿네요. 와우 그렇다면 이게 정답입니다. 1.35 캐럿 다이아몬드의 가격은 약 10,000달러입니다.

![모델에서 해답 찾기](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## 신뢰 구간 만들기

이 예측이 얼마나 정확한지 궁금해하는 것은 당연하겠죠. 1.35 캐럿 다이아몬드 가격이 10,000에 아주 가까운지, 약간 더 높은지 또는 더 낮은지를 알면 도움이 됩니다. 이를 알아내기 위해 회귀 직선 주위에 대부분의 점들을 포함하는 범위를 그려보겠습니다. 이 범위를 *신뢰 구간*이라고 합니다. 앞서 확인한 것처럼 대부분의 가격이 이 범위에 속한다고 확신할 수 있습니다. 1.35 캐럿 선이 해당 범위의 맨 위와 맨 아래에서 교차하는 수평선을 2개 더 그릴 수 있습니다.

![신뢰 구간](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

이제 이 신뢰 구간에 대해 이렇게 말할 수 있습니다. 1.35 캐럿 다이아몬드의 가격은 약 10,000달러이지만 최하 8,000달러에서 최고 12,000달러가 될 수도 있습니다.

## 수학이나 컴퓨터 없이도 해냈습니다.

데이터 과학자들이 돈을 받고 하는 일을 우리가 해넀습니다. 그림만 그려서 말이죠.

* 데이터로 답변할 수 있는 질문을 했습니다.
* *선형 회귀*를 사용하여 *모델*을 작성했습니다.
* *신뢰 구간*을 토대로 *예측*을 했습니다.

또한 수학이나 컴퓨터를 사용하지 않고 이 모든 것을 해냈습니다.

우리에게 더 많은 정보가 있었다면, 예를 들어

* 다이아몬드 커트
* 색상 변형(다이아몬드가 흰색에 가까운 정도)
* 다이아몬드의 함유물 수 같은 것 말입니다.

그러면 더 많은 열이 형성될 것입니다. 이 경우에는 수학이 도움이 됩니다. 열이 두 개보다 많은 경우 종이에 점을 그리기가 어렵습니다. 수학을 사용하면 데이터에 좀 더 잘 맞는 선이나 평면을 그릴 수 있습니다.

또한 다이아몬드 몇 개가 아니라 2,000개 또는 2백만 개가 있다면 컴퓨터로 훨씬 더 빠르게 이러한 작업을 처리할 수 있습니다.

오늘은 선형 회귀를 수행하는 방법을 논의했으며 데이터를 사용해서 예측을 수행했습니다.

Microsoft Azure 기계 학습의 “초급자를 위한 데이터 과학”에 있는 다른 비디오도 확인해보세요.



## 다음 단계

  * [Azure 기계 학습으로 첫 번째 데이터 과학 실험 시도](machine-learning-create-experiment.md)
  * [Microsoft Azure의 기계 학습 소개 보기](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->