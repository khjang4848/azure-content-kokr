<properties
	pageTitle="기계 학습 웹 서비스 배포 | Microsoft Azure"
	description="학습 실험을 예측 실험으로 변환하고 배포할 준비를 한 다음 Azure 기계 학습 웹 서비스로 배포하는 방법입니다."
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
	ms.date="07/06/2016"
	ms.author="garye"/>

# Azure 기계 학습 웹 서비스 배포

Azure 기계 학습을 사용하여 예측 분석 솔루션을 빌드, 테스트 및 배포할 수 있습니다.

대략적인 관점에서 이 작업은 다음 세 단계로 수행됩니다.

- **[학습 실험 만들기]** - Azure 기계 학습 스튜디오는 사용자가 제공하는 학습 데이터를 사용하여 예측 분석 모델을 학습하고 테스트하는 데 사용하는 시각적 공동 개발 환경입니다.
- **[예측 실험으로 변환]** - 기존 데이터로 모델을 학습시키고 새 데이터의 점수를 매기는 데 사용할 준비가 되면, 예측을 위해 실험을 준비하고 간소화합니다.
- **웹 서비스로 배포** - 예측 실험을 [신규] 또는 [기존] Azure 웹 서비스로 배포할 수 있습니다. 사용자 모델에 데이터를 보내고 모델의 예측을 받을 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 학습 실험 만들기

예측 분석 모델을 학습시키려면 Azure 기계 학습 스튜디오에서 학습 데이터를 로드하고, 필요한 대로 데이터를 준비하며, 기계 학습 알고리즘을 적용하고 결과를 평가하는 다양한 모듈을 포함하는 학습 실험을 만듭니다. 실험은 반복할 수 있으며 여러 다른 기계 학습 알고리즘을 시도하여 결과를 비교하고 평가할 수 있습니다.

학습 실험을 만들고 관리하는 프로세스는 다른 위치에서 더욱 철저히 다룹니다. 자세한 내용과 예제를 보려면 다음 문서를 참조하세요.

- [Azure 기계 학습 스튜디오에서 간단한 실험 만들기](machine-learning-create-experiment.md)
- [Azure 기계 학습을 사용하여 예측 솔루션 개발](machine-learning-walkthrough-develop-predictive-solution.md)
- [Azure 기계 학습 스튜디오에 학습 데이터 가져오기](machine-learning-data-science-import-data.md)
- [Azure 기계 학습 스튜디오에서 반복 실험 관리](machine-learning-manage-experiment-iterations.md)

## 학습 실험에서 예측 실험으로 변환

모델을 학습시키고 나면 새 데이터의 점수를 매기는 데 사용할 준비가 됩니다. 이 작업을 수행하려면 학습 실험을 예측 실험으로 변환합니다.

예측 실험으로 변환하면 학습된 모델을 점수 매기기 웹 서비스로 배포할 준비가 완료됩니다. 웹 서비스 사용자가 입력 데이터를 모델로 보내면 모델에서 예측 결과가 다시 전송됩니다. 따라서 예측 실험을 변환할 때 다른 사람이 모델을 사용할 것을 염두에 둘 수 있습니다.

학습 실험을 예측 실험으로 변환하려면 실험 캔버스의 맨 아래에서 **실행**을 클릭한 다음 **웹 서비스 설정**을 클릭한 다음 **예측 웹 서비스**를 선택합니다.

![점수 매기기 실험으로 변환](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

이 변환을 수행하는 방법에 대한 자세한 내용은 [기계 학습 실험을 예측 실험으로 변환](machine-learning-convert-training-experiment-to-scoring-experiment.md)을 참조하세요.

다음 단계에서는 예측 실험을 새로 배포하는 방법을 보여 줍니다

다음 단계는 예측 실험을 [새] 웹 서비스로 배포하는 방법을 설명합니다. 실험을 [기존] 웹 서비스로 배포할 수도 있습니다.

## 예측 실험을 새 웹 서비스로 배포

이제 예측 실험이 충분히 준비되었으므로 이를 Azure 웹 서비스로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

예측 실험을 배포하려면 실험 캔버스의 맨 아래에서 **실행**을 클릭합니다. 실험 실행이 완료되면 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[신규]**를 선택합니다. 기계 학습 웹 서비스 포털의 배포 페이지가 열립니다.

### 기계 학습 웹 서비스 포털 배포 실험 페이지
실험 배포 페이지에서 웹 서비스의 이름을 입력합니다. 가격 책정 계획을 선택합니다. 기존 가격 책정 계획이 있는 경우 해당 계획을 선택하고 그렇지 않으면 서비스에 대한 새 가격 계획을 만들어야 합니다.

1.	**가격 계획** 드롭다운에서 기존 계획을 선택하거나 **새 계획 선택** 옵션을 선택합니다.
2.	**계획 이름**에 청구서의 계획을 식별하는 이름을 입력합니다.
3.	**월별 계획 계층** 중 하나를 선택합니다. 계획 계층이 기본적으로 기본 영역에 대한 계획으로 설정되고 웹 서비스는 해당 지역에 배포됩니다.

**배포**를 클릭하면 웹 서비스에 대한 **빠른 시작** 페이지가 열립니다.

웹 서비스 빠른 시작 페이지를 사용하면 새 웹 서비스를 만든 후 수행할 가장 일반적인 작업에 대한 액세스 권한 및 지침을 제공합니다. 여기에서 테스트 페이지 및 사용 페이지에 쉽게 액세스할 수 있습니다.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### 웹 서비스 테스트

새 웹 서비스를 테스트하려면 일반적인 작업에서 **웹 서비스 테스트**를 클릭합니다. 테스트 페이지에서 웹 서비스를 RRS(요청-응답 서비스) 또는 BES(배치 실행 서비스)로 테스트할 수 있습니다.

RRS 테스트 페이지에서는 실험에 대해 정의된 입력, 출력 및 모든 전역 매개 변수를 표시합니다. 웹 서비스를 테스트하려면 입력에 적절한 값을 수동으로 입력하거나 테스트 값을 포함하는 CSV(쉼표로 구분된 값) 형식의 파일을 제공할 수 있습니다.

RRS를 테스트하려면 목록 보기 모드에서 입력에 적절한 값을 입력하고 **요청-응답 테스트**를 클릭합니다. 예측 결과는 왼쪽의 출력 열에 표시됩니다.

![웹 서비스 배포](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

BES를 테스트하려면 **배치**를 클릭합니다. 배치 테스트 페이지의 입력에서 찾아보기를 클릭하고 적절한 샘플 값이 포함된 CSV 파일을 선택합니다. CSV 파일이 없고 기계 학습 스튜디오를 사용하여 예측 실험을 만든 경우 예측 실험에 대한 데이터 집합을 다운로드하고 사용할 수 있습니다.

데이터 집합을 다운로드하려면 기계 학습 스튜디오를 엽니다. 예측 실험을 열고 실험에 대한 입력을 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **데이터 집합**을 선택한 다음 **다운로드**를 선택합니다.

![웹 서비스 배포](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

**테스트**를 클릭합니다. 배치 실행 작업의 상태는 **배치 작업 테스트**의 오른쪽에 표시됩니다.

![웹 서비스 배포](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

**구성** 페이지에서 설명, 제목을 변경하고 저장소 계정 키를 업데이트하며 웹 서비스에 대한 샘플 데이터를 사용하도록 설정할 수 있습니다.

![웹 서비스 구성](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

웹 서비스를 배포한 후 다음 작업을 수행할 수 있습니다.

- 웹 서비스 API를 통해 **액세스**
- Azure 기계 학습 웹 서비스 포털 또는 Azure 클래식 포털을 통해 **관리**
- 모델이 변경되는 경우 **업데이트**

### 웹 서비스 액세스

기계 학습 스튜디오에서 웹 서비스를 배포하면 서비스에 데이터를 보내고 프로그래밍 방식으로 응답을 받을 수 있습니다.

**사용** 페이지에서는 웹 서비스에 액세스하는 데 필요한 모든 정보를 제공합니다. 예를 들어, 서비스에 대한 권한이 부여된 액세스를 허용하도록 API 키를 제공합니다.

기계 학습 웹 서비스에 액세스하는 방법에 대한 자세한 내용은 [배포된 Azure 기계 학습 웹 서비스를 사용하는 방법](machine-learning-consume-web-services.md)을 참조하세요.

### 새 웹 서비스 관리

기존 웹 서비스 기계 학습 웹 서비스 포털을 관리할 수 있습니다. [기본 포털 페이지](https://services.azureml-test.net/)에서 **웹 서비스**를 클릭합니다. 웹 서비스 페이지에서 서비스를 삭제하거나 복사할 수 있습니다. 특정 서비스를 모니터링하려면 서비스를 클릭한 다음 **대시보드**를 클릭합니다. 웹 서비스와 연결된 배치 작업을 모니터링하려면 **배치 요청 로그**를 클릭합니다.

## 예측 실험을 기존 웹 서비스로 배포

이제 예측 실험이 충분히 준비되었으므로 이를 Azure 웹 서비스로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

예측 실험을 배포하려면 실험 캔버스의 맨 아래에서 **실행**을 클릭한 다음 **웹 서비스 배포**를 클릭합니다. 웹 서비스가 설정되고 웹 서비스 대시보드에 배치됩니다.

![웹 서비스 배포](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

웹 서비스를 테스트하려면 웹 서비스 대시보드에서 **테스트** 링크를 클릭합니다. 서비스에 대한 입력 데이터를 요청하는 대화 상자가 나타납니다. 이러한 열이 점수 매기기 실험에 필요합니다. 데이터 집합을 입력하고 **확인**을 클릭합니다. 웹 서비스에서 생성된 결과가 대시보드 아래쪽에 표시됩니다.

![웹 서비스 테스트](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

**구성** 탭에서 서비스의 표시 이름을 변경하고 설명을 제공할 수 있습니다. 웹 서비스를 관리하는 [Azure 클래식 포털](http://manage.windowsazure.com/)에 이름과 설명이 표시됩니다.

**입력 스키마**, **출력 스키마** 및 **웹 서비스 매개 변수**의 각 열에 문자열을 입력하여 입력 데이터, 출력 데이터 및 웹 서비스 매개 변수의 설명을 제공할 수 있습니다. 이러한 설명은 웹 서비스에 제공된 샘플 코드 설명서에서 사용합니다. 웹 서비스에 액세스할 때 표시되는 실패를 진단하기 위해 로깅도 사용할 수도 있습니다.

자세한 내용은 [기계 학습 웹 서비스에 대해 로깅 사용](machine-learning-web-services-logging.md)을 참조하세요.

![웹 서비스 구성](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

### 웹 서비스 액세스

기계 학습 스튜디오에서 웹 서비스를 배포하면 서비스에 데이터를 보내고 프로그래밍 방식으로 응답을 받을 수 있습니다.

대시보드에서는 웹 서비스에 액세스하는 데 필요한 모든 정보를 제공합니다. 예를 들어, 서비스에 대해 권한 부여된 액세스가 가능하도록 API 키가 제공되고, 코드 작성을 시작하는 데 도움이 되도록 API 도움말 페이지가 제공됩니다.

기계 학습 웹 서비스에 액세스하는 방법에 대한 자세한 내용은 [배포된 Azure 기계 학습 웹 서비스를 사용하는 방법](machine-learning-consume-web-services.md)을 참조하세요.

### Azure 클래식 포털에서 웹 서비스 관리

[Azure 클래식 포털](http://manage.windowsazure.com/)에서 **기계 학습** 서비스를 클릭하고 기계 학습 작업 영역을 연 다음 **웹 서비스** 탭에서 웹 서비스를 열어 웹 서비스를 관리할 수 있습니다. 이 페이지에서 웹 서비스를 모니터링하고 업데이트하며 삭제할 수 있습니다. 배포할 때 생성되는 기본 끝점 외에, 웹 서비스의 두 번째 끝점도 추가할 수 있습니다.

자세한 내용은 [Azure 기계 학습 작업 영역 관리](machine-learning-manage-workspace.md)를 참조하세요.
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## 웹 서비스 업데이트

추가 학습 데이터로 모델 업데이트와 같은 방식으로 웹 서비스를 변경하고 다시 배포하여 원래 웹 서비스를 덮어쓸 수 있습니다.

웹 서비스를 업데이트하려면 웹 서비스를 배포하는 데 사용한 원래 예측 실험을 열고 **다른 이름으로 저장**을 클릭하여 편집 가능한 사본을 만듭니다. 변경한 다음 **웹 서비스 배포**를 클릭합니다.

이 실험을 이전에 배포했기 때문에 기존 서비스를 덮어쓰거나(기존 웹 서비스) 업데이트(새 웹 서비스)할지 묻는 메시지가 표시됩니다. **예** 또는 **업데이트**를 클릭하여 기존 웹 서비스를 중지하고 새 예측 실험을 대신 배포합니다.

> [AZURE.NOTE] 예를 들어, 새 표시 이름 또는 설명을 입력하여 원래 웹 서비스의 구성을 변경한 경우 해당 값을 다시 입력해야 합니다.

웹 서비스를 업데이트하기 위한 한 가지 옵션은 프로그래밍 방식으로 모델을 다시 학습하는 것입니다. 자세한 내용은 [프로그래밍 방식으로 기계 학습 모델 다시 학습](machine-learning-retrain-models-programmatically.md)을 참조하세요.


<!-- internal links -->
[학습 실험 만들기]: #create-a-training-experiment
[예측 실험으로 변환]: #convert-the-training-experiment-to-a-predictive-experiment
[새]: #deploy-the-predictive-experiment-as-a-new-web-service
[신규]: #deploy-the-predictive-experiment-as-a-new-web-service
[기존]: #deploy-the-predictive-experiment-as-a-new-web-service
[Access]: #access-the-web-service
[Manage]: #manage-the-web-service-in-the-azure-management-portal
[Update]: #update-the-web-service

<!---HONumber=AcomDC_0914_2016-->