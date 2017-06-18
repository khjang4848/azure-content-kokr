<properties
   pageTitle="데이터 과학에 사용할 수 있게 데이터가 준비되었나요? 데이터 평가 | Microsoft Azure"
   description="데이터 과학에 사용할 수 있게 데이터를 준비하기 위한 4가지 기준을 알아보세요. 초급자를 위한 데이터 과학 비디오 2에는 기본 데이터 평가에 도움이 되는 몇 가지 구체적인 예제가 있습니다."
   keywords="데이터 평가,관련 데이터,데이터 평가,데이터 준비,데이터 기준,데이터 준비 완료"
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


# 데이터 과학에 사용할 수 있게 데이터가 준비되었나요?

## 비디오 2: 초급자를 위한 데이터 과학 시리즈

데이터를 평가하여 데이터가 데이터 과학의 기본 조건을 충족하는지 확인하는 방법을 알아봅니다.

시리즈를 최대한 활용하려면 순서대로 시청하는 것이 좋습니다. [비디오 목록으로 이동](#other-videos-in-this-series)

<div class="wa-video-container" data-control="video-container" data-slug="data-science-for-beginners-series-is-your-data-ready-for-data-science"> <a href="/en-us/documentation/videos/data-science-for-beginners-series-is-your-data-ready-for-data-science/" data-control="video" data-expanding="false" class="wa-video-thumbnail" title="Data science for beginners: Is your data ready for data science?" data-ch9="//channel9.msdn.com/Shows/SupervisionNotRequired/9/player/" data-caption="" data-event="videopage-clicked-videothumbnail" data-event-property="data-science-for-beginners-series-is-your-data-ready-for-data-science"> <img src="https://sec.ch9.ms/ch9/8c9a/aab9492b-4167-41f0-9b9f-4eab661e8c9a/DataScienceForBeginnersIsYourDataReadyForDataScie_960.jpg" alt="Data science for beginners: Is your data ready for data science?" class="thumbnail"> <img class="play-icon" src="/cdn/cvt-a29a0e789afe7f47464f393c011b53f210c511ee5fed2c4c9c0633442f30d6ec/images/icon/VideoPlay.svg"> <span class="metadata"> <span class="date"> <span>06-27-2016</span> <span>04 min, 56 sec</span> </span> </span> </a> </div>

## 이 시리즈의 다른 비디오

*초급자를 위한 데이터 과학*은 다섯 개의 짧은 비디오를 통해 데이터 과학을 간략히 소개합니다.

  * 비디오 1: [데이터 과학으로 답변할 수 있는 5가지 질문](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5분 14초)*
  * 비디오 2: 데이터 과학에 사용할 수 있게 데이터가 준비되었나요?
  * 비디오 3: [데이터로 대답할 수 있는 질문하기](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4분 17초)*
  * 비디오 4: [단순 모델을 사용하여 답변 예측](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7분 42초)*
  * 비디오 5: [데이터 과학을 수행하기 위해 다른 사람의 작품 복사](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3분 18초)*

## 비디오 내용: 데이터 과학에 사용할 수 있게 데이터가 준비되었나요?

*초급자를 위한 데이터 과학* 시리즈의 두 번째 비디오인 "데이터 과학에 사용할 수 있게 데이터가 준비되었나요?"를 시작하겠습니다.

데이터 과학을 통해 원하는 대답을 얻으려면 먼저 작업할 고품질 원재료를 제공해야 합니다. 피자를 만들 때처럼 더 나은 재료로 시작할수록 더 나은 최종 제품을 얻을 수 있습니다.

## 데이터 기준

데이터 과학의 경우 모아야 하는 몇 가지 재료가 있습니다.

다음과 같은 데이터가 필요합니다.

  *	관련성
  *	연결됨
  *	정확함
  *	작업하는 데 충분함

## 여러분의 데이터는 관련성이 있나요?

첫 번째 재료가 바로 이것입니다. 우리에게는 관련성이 있는 데이터가 필요합니다.

![관련 데이터 및 관련이 없는 데이터 - 데이터 평가](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

왼쪽의 표를 보세요. 우리는 보스턴 술집 밖에서 7명의 사람들을 만나서 그들의 혈중 알코올 농도, 마지막 Red Sox 게임에서 Red Sox의 평균 타율, 가장 가까운 편의점 우유 가격을 측정했습니다.

이 모든 것은 완벽하게 합법적인 데이터입니다. 유일한 결함은 서로 관련성이 없다는 것입니다. 이러한 수치 간에는 명확한 관계가 없습니다. 현재 우유 가격과 Red Sox 타율 평균을 안다고 해서 혈중 알코올 농도도 알 수 있는 방법은 없습니다.

이제 오른쪽의 표를 보세요. 이번에는 각 사람의 체질량을 측정하고 몇 잔의 술을 마셨는지 계산했습니다. 이제 각 행의 숫자는 서로 관련성이 있습니다. 체질량과 마르가리타 잔 수를 알면 혈중 알코올 농도를 추측할 수 있을 것입니다.

## 연결된 데이터가 있습니까?

다음 재료는 연결된 데이터입니다.

![연결된 데이터 및 연결되지 않은 데이터 - 데이터 기준, 데이터 준비](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

햄버거 품질에 대해 관련성 있는 데이터인 그릴 온도, 패티 무게, 현지 식품 잡지에서 받은 등급이 있습니다. 그러나 왼쪽의 표에는 빈 공간이 있습니다.

대부분의 데이터 집합에서 일부 값이 누락되어 있습니다. 이러한 누락은 일반적인 것이며 해결 방법이 있습니다. 그렇지만 누락된 데이터가 너무 많으면 스위스 치즈처럼 보이기 시작합니다.

왼쪽의 표를 보면 누락된 데이터가 많이 있으므로 그릴 온도와 패티 두께 간 관계를 추측하기 어렵습니다. 연결되지 않은 데이터의 예입니다.

그렇지만 오른쪽의 표는 꽉 차서 완전합니다. 연결된 데이터의 예로 볼 수 있습니다.

## 데이터가 정확한가요?

필요한 다음 재료는 정확성입니다. 화살로 맞추려는 4개의 과녁이 있습니다.

![정확한 데이터 및 부정확한 데이터 - 데이터 기준](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

오른쪽 위의 과녁을 보세요. 과녁 중앙 근처에 화살이 밀집되어 있습니다. 물론 정확한 편입니다. 이상하게도 데이터 과학의 언어에서는 오른쪽 아래에 있는 과녁에서 거둔 성과도 정확한 것으로 간주됩니다.

이러한 화살의 중심을 배치해보면 과녁의 중앙에 매우 가깝다는 것을 알 수 있습니다. 화살은 과녁 전체에 퍼져 있으므로 정밀하지 않은 것으로 간주되지만 과녁의 중앙 근처에 있으므로 정확한 것으로 간주됩니다.

이제 왼쪽 위 과녁을 살펴보겠습니다. 화살끼리 매우 가깝게 모여 있습니다. 정확한 편이지만 중심이 과녁의 중앙을 벗어나 있으므로 정확하지 않습니다. 물론 왼쪽 아래 과녁의 화살표는 부정확하고 정밀하지도 않습니다. 이 궁수는 좀 더 연습이 필요합니다.

## 사용할 충분한 데이터가 있나요?

마지막으로 재료 4는 충분한 데이터가 필요하다는 것입니다.

![분석을 위한 충분한 데이터가 있나요? 데이터 평가](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

표의 각 데이터 요소를 그림의 붓 자국을 생각해보세요. 붓 자국이 몇 개뿐이면 그림이 상당히 흐릿해져서 어떤 그림을 그렸는지 알기 어렵습니다.

붓 자국을 좀 더 추가하면 그림이 좀 더 선명해지기 시작합니다.

붓 자국이 어느 정도 있어야 대략적인 결정을 내릴 수 있게 됩니다. 제가 가보고 싶은 곳일까요? 밝고 깨끗한 물처럼 보입니다. 맞습니다. 제가 휴가를 보내고 싶은 곳입니다.

더 많은 데이터를 추가하면 그림이 좀 더 명확해지고 보다 자세한 결정을 내릴 수 있게 됩니다. 이제 왼쪽 강둑에 있는 세 곳의 호텔을 볼 수 있습니다. 아시다시피 저는 앞쪽에 있는 호텔의 건축 특성을 정말 좋아합니다. 그 호텔 3층에 머물 것입니다.

관련성이 있고, 연결되고, 정확하고, 충분한 데이터가 있으면 고품질의 데이터 과학을 수행하는 데 필요한 모든 재료를 갖추게 되는 것입니다.

Microsoft Azure 기계 학습의 *초급자를 위한 데이터 과학*에 포함된 다른 4가지 비디오도 확인해보세요.




## 다음 단계

  * [Azure 기계 학습으로 첫 번째 데이터 과학 실험 시도](machine-learning-create-experiment.md)
  * [Microsoft Azure의 기계 학습 소개 보기](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->