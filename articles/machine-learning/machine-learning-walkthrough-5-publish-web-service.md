<properties
	pageTitle="5단계: 기계 학습 웹 서비스 배포 | Microsoft Azure"
	description="예측 솔루션 개발 연습 5단계: Azure 기계 학습 스튜디오에서 예측 실험을 웹 서비스로 배포합니다."
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


# 연습 5단계: Azure 기계 학습 웹 서비스 배포

[Azure 기계 학습에서 예측 분석 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 연습의 다섯 번째 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	[새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**웹 서비스 배포**
6.	[웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

----------

다른 사용자가 이 연습에서 개발한 예측 모델을 사용할 수 있도록 Azure의 웹 서비스로 배포할 예정입니다.

지금까지는 모델을 학습하는 실험을 진행했습니다. 하지만 배포된 서비스는 더 이상 학습을 수행하지 않고 모델에 따라 사용자 입력의 점수를 매겨서 예측을 생성합니다. 그러므로 몇 가지 예측을 수행하여 ***학습*** 실험을 ***예측*** 실험으로 변환하겠습니다.

이 작업은 두 단계로 이루어집니다.

1. 만든 *학습 실험*을 *예측 실험*으로 변환
2. 예측 실험을 웹 서비스로 배포

하지만 먼저 이 실험을 약간 간단히 줄여야 합니다. 실험에는 두 개의 다른 모델이 있지만 웹 서비스로 배포할 때는 모델이 하나여야 합니다.

향상된 트리 모델을 사용할 더 효율적인 모델로 결정했다고 가정합니다. 가장 먼저 수행할 작업은 [2클래스 Support Vector Machine][two-class-support-vector-machine] 모듈과 학습에 사용된 모듈을 제거하는 것입니다. 먼저 실험 캔버스 아래쪽에 있는 **다른 이름으로 저장**을 클릭하여 실험 복사본을 만들 수 있습니다.

다음 모듈을 삭제해야 합니다.

- [2클래스 Support Vector Machine][two-class-support-vector-machine]
- 연결된 [모델 학습][train-model] 및 [모델 점수 매기기][score-model] 모듈
- [데이터 표준화][normalize-data](둘 다)
- [모델 평가][evaluate-model]

모듈을 선택하고 <Delete> 키를 누르거나 모듈을 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

이제 [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree]를 사용하여 이 모듈을 배포할 준비가 되었습니다.

## 학습 실험에서 예측 실험으로 변환

예측 실험으로 변환하는 프로세스는 다음 세 단계로 구성됩니다.

1. 학습한 모델을 저장한 다음 학습 모듈 바꾸기
2. 학습에만 필요한 모듈을 제거하여 실험 축소
3. 웹 서비스에서 입력을 수락할 위치 및 출력을 생성할 위치 정의

다행히도 실험 캔버스의 맨 아래에 있는 **웹 서비스 설정**을 클릭하여 세 단계를 모두 수행할 수 있습니다(**예측 웹 서비스** 옵션 선택).

**웹 서비스 설정**을 클릭하면 다음과 같은 결과가 발생합니다.

- 학습한 모델이 실험 캔버스 왼쪽의 모듈 팔레트에 단일 **학습된 모델** 모듈로 저장됩니다(**학습된 모델** 아래에서 찾을 수 있음).
- 학습에 사용된 모듈은 제거됩니다. 구체적으로 살펴보면 다음과 같습니다.
  - [2클래스 향상된 의사 결정 트리][two-class-boosted-decision-tree]
  - [모델 학습][train-model]
  - [데이터 분할][split]
  - 테스트 데이터에 사용된 두 번째 [R 스크립트 실행][execute-r-script] 모듈
- 저장된 학습된 모델이 실험에 다시 추가됩니다.
- **웹 서비스 입력** 및 **웹 서비스 출력** 모듈이 추가됩니다.

> [AZURE.NOTE] 실험 캔버스 맨 위에 추가된 탭 아래에 두 부분으로 실험이 저장되었습니다. 원래 학습 실험은 **학습 실험** 탭 아래에, 새로 생성된 예측 실험은 **예측 실험** 아래에 저장됩니다.

이 특정 실험과 함께 하나의 추가 단계를 수행해야 합니다. 두 개의 [R 스크립트 실행][execute-r-script] 모듈을 추가하여 학습 및 테스트에 대한 데이터에 가중치 기능을 제공했습니다. 최종 모델에 대해서는 수행할 필요가 없습니다. 기계 학습 스튜디오에서 [분할][split] 모듈을 제거할 때 한 [R 스크립트 실행][execute-r-script] 모듈을 제거했으므로 이제 다른 모듈을 제거하고 [메타데이터 편집기][metadata-editor]를 [모델 점수 매기기][score-model]에 직접 연결할 수 있습니다.

이제 실험은 다음과 같이 표시됩니다.

![학습된 모델 점수 매기기][4]


> [AZURE.NOTE] 예측 실험에서 UCI 독일어 신용 카드 데이터의 데이터 집합을 남겨둔 이유가 궁금할 것입니다. 서비스에서는 원래 데이터 집합이 아니라 사용자 데이터를 사용하려고 하는데 모델에 원래 데이터 집합으로 두는 이유는 무엇인가요?
>
>서비스에 원래 신용 카드 데이터가 필요하지 않은 것은 사실입니다. 하지만 열 수 및 숫자인 열 같은 정보가 포함된 해당 데이터에 대한 스키마는 필요하지 않습니다. 이 스키마 정보는 사용자 데이터를 해석하기 위해 필요합니다. 서비스가 실행 중일 때 점수 매기기 모듈에 데이터 집합 스키마가 포함되도록 이러한 구성 요소를 연결된 상태로 유지합니다. 데이터는 사용되지 않고 스키마만 사용됩니다.

마지막으로 실험을 한 번 실행합니다(**실행** 클릭). 모델이 계속 작동 중인지 확인하려면 [모델 점수 매기기][score-model] 모듈의 출력을 클릭하고 **결과 보기**를 선택합니다. 원래 데이터가 신용 위험 값("점수를 매긴 레이블") 및 점수 매기기 확률 값("점수를 매긴 확률")과 함께 표시됩니다.

## 웹 서비스 배포

실험을 Azure Resource Manager에 기반하는 기존 웹 서비스 또는 새 웹 서비스로 배포할 수 있습니다.

### 기존 웹 서비스로 배포 ###

실험에서 파생된 기존 웹 서비스를 배포하려면 캔버스에서 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[신규]**를 선택합니다. 기계 학습 스튜디오에서 실험을 웹 서비스로 배포하고 웹 서비스에 대한 대시보드로 이동합니다. 실험으로 돌아가서(**스냅숏 보기** 또는 **최신 항목 보기**) 간단한 웹 서비스 테스트를 실행(**테스트** 단추 - 아래 **웹 서비스 테스트** 참조)할 수 있습니다. 여기에는 웹 서비스에 액세스할 수 있는 응용 프로그램을 만들기 위한 정보도 있습니다. 자세한 내용은 이 연습의 다음 단계를 참조하세요.

![웹 서비스 대시보드][6]

**구성** 탭을 클릭하여 서비스를 구성할 수 있습니다. 여기서 서비스 이름(기본적으로 실험 이름이 지정됨)을 수정하고 설명을 제공할 수 있습니다. 입력 및 출력 열에 대해 더 친숙한 레이블을 제공할 수도 있습니다.

![웹 서비스 구성][5]

### 새 웹 서비스로 배포

실험에서 파생된 새 웹 서비스를 배포하려면 캔버스에서 **웹 서비스 배포** 및 **웹 서비스 배포[신규]**를 클릭합니다. 기계 학습 스튜디오를 통해 Azure 기계 학습 웹 서비스 배포 실험 페이지로 이동합니다.

웹 서비스의 이름을 입력하고 가격 책정 계획을 선택합니다. 기존 가격 책정 계획이 있는 경우 해당 계획을 선택하고 그렇지 않으면 서비스에 대한 새 가격 계획을 만들어야 합니다.

1.	**가격 계획** 드롭다운에서 기존 계획을 선택하거나 **새 계획 선택** 옵션을 선택합니다.
2.	**계획 이름**에 청구서의 계획을 식별하는 이름을 입력합니다.
3.	**월별 계획 계층** 중 하나를 선택합니다. 계획 계층이 기본적으로 기본 영역에 대한 계획으로 설정되고 웹 서비스는 해당 지역에 배포됩니다.

**배포**를 클릭하면 웹 서비스에 대한 **빠른 시작** 페이지가 열립니다.

**구성** 메뉴 옵션을 클릭하여 서비스를 구성할 수 있습니다. 여기서 서비스 이름(기본적으로 실험 이름이 지정됨)을 수정하고 설명을 제공할 수 있습니다. 또한 서비스를 테스트 하려면 사용할 수 있는 오류 로깅 및 샘플 데이터를 사용하거나 사용하지 않도록 설정할 수 있습니다.

웹 서비스를 테스트하려면 **테스트** 메뉴 옵션을 클릭합니다(아래의 **웹 서비스 테스트** 참조). 웹 서비스에 액세스할 수 있는 응용 프로그램을 만드는 방법에 대한 정보는 **사용** 메뉴 옵션을 클릭합니다(자세한 내용은 이 연습의 다음 단계 참조).


> [AZURE.TIP] 웹 서비스를 배포하고 나서 업데이트할 수 있습니다. 예를 들어 모델을 변경하려면 학습 실험을 편집하고, 모델 매개 변수를 조정하고, **웹 서비스 배포**를 클릭합니다. 그런 다음 **웹 서비스 배포[기존]** 또는 **웹 서비스 배포[신규]**를 선택합니다. 실험을 다시 배포하면 이 실험이 웹 서비스를 대체하고 이제 업데이트된 모델을 사용합니다.


## 웹 서비스 테스트

**기존 웹 서비스 테스트** **대시보드** 페이지에서 **기본 끝점**의 **테스트** 단추를 클릭합니다. 대화 상자가 팝업되고 서비스에 대한 입력 데이터를 요구합니다. 이는 원래 독일 신용 위험 데이터 집합에 나타난 열과 같습니다.

데이터 집합을 입력하고 **확인**을 클릭합니다.

웹 서비스에서 데이터는 **웹 서비스 입력** 모듈 및 [메타데이터 편집기][metadata-editor] 모듈을 통해 점수가 매겨지는 [모델 점수 매기기][score-model] 모듈에 입력됩니다. 그런 다음 웹 서비스의 결과가 **웹 서비스 출력**을 통해 출력됩니다.

**새 웹 서비스 테스트**

Azure 기계 학습 웹 서비스 포털에서 페이지의 맨 위에 있는 **테스트** 메뉴 옵션을 클릭합니다. 테스트 페이지가 열리고 서비스에 대한 데이터를 입력할 수 있습니다. 표시된 입력 필드는 원래 독일 신용 위험 데이터 집합에 나타난 열과 같습니다.

데이터 집합을 입력하고 **요청-응답 테스트**를 클릭합니다.

요청-응답 서비스를 테스트하는 데 사용할 수 있는 샘플 데이터도 사용할 수 있습니다. 기계 학습 스튜디오에서 웹 서비스를 만든 경우 샘플 데이터는 모델을 학습하는 데 사용된 데이터에서 가져옵니다.

테스트의 결과는 출력 열에 있는 페이지의 오른쪽에 표시됩니다.

> [AZURE.TIP] 예측 실험을 구성한 방법대로 [모델 점수 매기기][score-model] 모듈에서 전체 결과가 반환됩니다. 여기에는 모든 입력 데이터와 신용 위험 값 및 점수 매기기 확률이 포함됩니다. 신용 위험 값만 반환하는 등 다른 결과를 반환하려면 [프로젝트 열][project-columns] 모듈을 [모델 점수 매기기][score-model]와 **웹 서비스 출력** 사이에 삽입하고 웹 서비스에서 반환하지 않으려는 열을 제거합니다.

## 웹 서비스 관리

**기존 웹 서비스 관리**

기존 웹 서비스를 배포한 후에는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 관리할 수 있습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. Microsoft Azure 서비스 패널에서 **기계 학습**을 클릭합니다.
3. 작업 영역을 클릭합니다.
4. **웹 서비스** 탭을 클릭합니다.
5. 방금 만든 웹 서비스를 클릭합니다.
6. "기본" 끝점을 클릭합니다.

여기에서 웹 서비스를 수행하는 방법을 모니터링하고 서비스에서 처리할 수 있는 동시 호출 수를 변경하여 성능을 조정하는 등의 작업을 수행할 수 있습니다. Azure 마켓플레이스에 웹 서비스를 게시할 수도 있습니다.

자세한 내용은 다음을 참조하세요.

- [끝점 만들기](machine-learning-create-endpoint.md)
- [웹 서비스 확장](machine-learning-scaling-webservice.md)
- [Azure 마켓플레이스에 Azure 기계 학습 웹 서비스 게시](machine-learning-publish-web-service-to-azure-marketplace.md)

**새 웹 서비스 관리**

새 웹 서비스를 배포하면 [Azure 기계 학습 웹 서비스 포털](https://servics.azureml.net)에서 관리할 수 있습니다.

웹 서비스의 성능을 모니터링하려면 다음을 수행합니다.
1. [Azure 기계 학습 웹 서비스 포털](https://servics.azureml.net)에 로그인합니다.
2. **웹 서비스**를 클릭합니다.
3. 웹 서비스를 클릭합니다.
4. **대시보드**를 클릭합니다.

----------

**다음: [웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/ko-KR/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0921_2016-->