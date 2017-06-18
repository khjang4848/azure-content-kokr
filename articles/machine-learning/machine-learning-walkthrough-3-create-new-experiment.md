<properties
	pageTitle="3단계: 새 기계 학습 실험 만들기 | Microsoft Azure"
	description="예측 솔루션 개발 연습 3단계: Azure 기계 학습 스튜디오에서 새 학습 실험을 만듭니다."
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


# 연습 3단계: 새 Azure 기계 학습 실험 만들기

[Azure 기계 학습에서 예측 분석 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md) 연습의 세 번째 단계입니다.


1.	[기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3.	**새 실험 만들기**
4.	[모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)
6.	[웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

----------

이 연습의 다음 단계는 업로드한 데이터 집합을 사용하는 기계 학습 스튜디오에서 새 실험을 만드는 것입니다.

1.	스튜디오의 창 하단에 있는 **+새로 만들기**를 클릭합니다.
2.	**실험**을 선택하고 "빈 실험"을 선택합니다. 캔버스 위쪽에서 기본 실험 이름을 선택하고 이를 의미 있는 이름으로 바꿉니다.

	> [AZURE.TIP] **속성** 창에 실험에 대한 **요약** 및 **설명**을 입력하는 것이 좋습니다. 이러한 속성을 사용하면 실험을 문서화할 수 있어 나중에 실험을 참조하는 모든 사용자가 실험의 목표와 방법론을 이해할 수 있습니다.

3.	실험 캔버스의 왼쪽에 있는 모듈 팔레트에서 **저장된 데이터 집합**을 확장합니다.
4.	**내 데이터 집합** 아래에서 만든 데이터 집합을 찾고 캔버스로 끌어 옵니다. 팔레트 위의 **검색** 상자에 이름을 입력하여 데이터 집합을 찾을 수도 있습니다.  

## 데이터 준비

데이터 집합(아래 작은 원)의 출력 포트를 클릭하고 **시각화**를 선택하여 데이터의 처음 100개 행 및 전체 데이터 집합에 대한 일부 통계 정보를 볼 수 있습니다.

데이터 파일에는 열 제목이 없으므로 스튜디오에서 일반적인 제목(Col1, Col2, *등*)을 제공했습니다. 적합한 제목은 모델 작성 시 필수 사항은 아니지만 제목이 있으면 실험에서 데이터를 더 쉽게 사용할 수 있습니다. 또한 최종적으로 웹 서비스에서 이 모델을 게시할 때 제목을 사용하여 서비스 사용자에 대한 열을 식별할 수 있습니다.

[메타데이터 편집][edit-metadata] 모듈을 사용하여 열 제목을 추가할 수 있습니다. [메타데이터 편집][edit-metadata] 모듈을 사용하여 데이터 집합과 연결된 메타데이터를 변경합니다. 이 경우에는 좀 더 친숙한 열 머리글 이름을 지정할 수 있습니다.

[메타데이터 편집][edit-metadata]을 사용하려면 먼저 수정할 열(이 경우 모든 열)을 지정한 다음 해당 열에 대해 수행할 작업(이 경우 열 제목 변경)을 지정합니다.

1.	모듈 팔레트에서 **검색** 상자에 "metadata"를 입력합니다. 모듈 목록에 [메타데이터 편집][edit-metadata]이 나타나는 것을 확인할 수 있습니다.
2.	[메타데이터 편집][edit-metadata] 모듈을 클릭하고 캔버스로 끌어서 이전에 추가한 데이터 집합 아래에 놓습니다.
3.	데이터 집합을 [메타데이터 편집][edit-metadata]에 연결합니다. 데이터 집합의 출력 포트(데이터 집합 맨 아래의 작은 원)를 클릭하여 [메타데이터 편집][edit-metadata]의 입력 포트(모듈 맨 위의 작은 원)로 끌어 놓습니다. 데이터 집합과 모듈은 캔버스에서 이동해도 연결 상태를 유지합니다.

    이제 실험이 다음과 같이 표시됩니다.

    ![메타데이터 편집 추가][2]
    
    빨간색 느낌표는 이 모듈에 대한 속성이 아직 설정되지 않았다는 것을 나타냅니다. 다음에 수행합니다.
    
    > [AZURE.TIP] 모듈을 두 번 클릭하고 텍스트를 입력하여 모듈에 주석을 추가할 수 있습니다. 그러면 모듈이 실험에서 수행하는 내용을 한눈에 볼 수 있습니다. 이 경우 [메타데이터 편집][edit-metadata] 모듈을 두 번 클릭하고 주석 "열 제목 추가"를 입력합니다. 캔버스의 다른 곳을 클릭하여 텍스트 상자를 닫습니다. 주석을 표시하려면 모듈에서 아래쪽 화살표를 클릭합니다.

4.	[메타데이터 편집][edit-metadata]을 선택한 다음 캔버스 오른쪽의 **속성** 창에서 **열 선택기 시작**을 클릭합니다.
5.	**열 선택** 대화 상자의 **사용 가능한 열**에서 모든 행을 선택하고 >를 클릭하여 **선택한 열**로 이동시킵니다. 대화 상자는 다음과 같습니다. ![모든 열이 선택된 열 선택기][4]
7.	**확인** 확인 표시를 클릭합니다.
8.	**속성** 창으로 돌아가서 **새 열 이름** 매개 변수를 찾습니다. 이 필드에 데이터 집합의 21개 열에 대한 이름 목록을 쉼표로 구분하여 열 순서로 입력합니다. UCI 웹 사이트의 데이터 집합 설명서에서 열 이름을 얻거나 편의를 위해 다음 목록을 복사하여 붙여넣을 수 있습니다.

		  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    속성 창은 다음과 같이 표시됩니다.

    ![메타데이터 편집에 대한 속성][1]

> [AZURE.TIP] 열 제목을 확인하려면 실험을 실행(실험 캔버스 아래의 **실행** 클릭)합니다. 실행이 완료되면([메타데이터 편집][edit-metadata]에 녹색 확인 표시가 나타남) [메타 데이터 편집][edit-metadata] 모듈의 출력 포트를 클릭하고 **시각화**를 선택합니다. 같은 방법으로 모듈의 출력을 표시하여 실험을 통해 데이터 진행률을 확인할 수 있습니다.

## 학습 및 테스트 데이터 집합 만들기

실험의 다음 단계에서는 모델 학습 및 테스트 모두에 사용할 개별 데이터 집합을 생성합니다.

이 작업을 하기 위해 [데이터 분할][split] 모듈을 사용합니다.

1.	[데이터 분할][split] 모듈을 찾고 캔버스로 끌어서 마지막 [메타데이터 편집][edit-metadata] 모듈에 연결합니다.
2.	기본적으로 분할 비율은 0.5이고 **무작위 분할** 매개 변수가 설정됩니다. 이는 데이터의 임의 절반이 [데이터 분할][split] 모듈의 한 포트를 통해 출력되고 나머지 절반은 다른 포트를 통해 출력됨을 의미합니다. 이러한 매개 변수와 **임의 초기값** 매개 변수를 조정하여 학습 및 테스트 데이터 간의 분할을 변경합니다. 이 예제에서는 매개 변수를 그대로 유지합니다.
	> [AZURE.TIP] **첫 번째 출력 데이터 집합의 행의 비율** 속성은 왼쪽 출력 포트를 통해 출력되는 데이터의 양을 결정합니다. 예를 들어 비율을 0.7로 설정하면 데이터의 70%가 왼쪽 포트를 통해 출력되고 30%는 오른쪽 포트를 통해 출력됩니다.
3. [데이터 분할][split] 모듈을 두 번 클릭하고 주석 "데이터 분할 50% 학습/테스트"를 입력합니다.

[데이터 분할][split] 모듈의 출력을 사용할 수 있지만 왼쪽 출력을 학습 데이터로 사용하고 오른쪽 출력을 테스트 데이터로 사용하도록 선택합니다.

UCI 웹 사이트에 언급된 대로 높은 신용 위험을 낮음으로 오분류한 비용은 낮은 신용 위험을 높음으로 오분류한 비용보다 5배 더 큽니다. 이를 고려하기 위해 이 비용 함수를 반영하는 새 데이터 집합을 생성합니다. 새 데이터 집합에서 위험이 높은 각 예제는 5번 복제되지만 위험이 낮은 각 예제는 복제되지 않습니다.

R 코드를 사용하여 이 복제를 수행할 수 있습니다.

1.	[R 스크립트 실행][execute-r-script] 모듈을 찾아 실험 캔버스로 끌어 놓고 [데이터 분할][split] 모듈의 왼쪽 출력 포트를 [R 스크립트 실행][execute-r-script] 모듈의 첫 번째 입력 포트("데이터 집합1")에 연결합니다.
2. [R 스크립트 실행][execute-r-script] 모듈을 두 번 클릭하고 주석 "비용 조정 설정"을 입력합니다.
2.	**속성** 창에서 **R 스크립트** 매개 변수의 기본 텍스트를 삭제하고 다음 스크립트를 입력합니다.

		  dataset1 <- maml.mapInputPort(1)
		  data.set<-dataset1[dataset1[,21]==1,]
		  pos<-dataset1[dataset1[,21]==2,]
		  for (i in 1:5) data.set<-rbind(data.set,pos)
		  maml.mapOutputPort("data.set")


학습 및 테스트 데이터에 같은 비용 조정이 포함되도록 [데이터 분할][split] 모듈의 각 출력에 대해 이와 같은 복제 작업을 수행해야 합니다.

1.	[R 스크립트 실행][execute-r-script] 모듈을 마우스 오른쪽 단추로 클릭하고 **복사**를 선택합니다.
2.	실험 캔버스를 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.
3.	이 [R 스크립트 실행][execute-r-script] 모듈의 첫 번째 입력 포트를 [데이터 분할][split] 모듈의 오른쪽 출력 포트에 연결합니다.

> [AZURE.TIP] R 스크립트 실행 모듈의 복사본에는 원래 모듈과 같은 스크립트가 포함됩니다. 캔버스에서 모듈을 복사하고 붙여넣으면 복사본에서 원본의 모든 속성이 유지됩니다.

이제 실험은 다음과 같이 표시됩니다.

![분할 모듈 및 R 스크립트 추가][3]

실험에서의 R 스크립트 사용에 대한 자세한 내용은 [R을 사용하여 실험 확장](machine-learning-extend-your-experiment-with-r.md)을 참조하세요.

**다음: [모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->