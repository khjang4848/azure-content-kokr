<properties
	pageTitle="기계 학습 작업 영역 만들기 | Microsoft Azure"
	description="Azure 기계 학습 스튜디오의 작업 영역을 만드는 방법"
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
	ms.date="08/16/2016"
	ms.author="garye;bradsev;ahgyger"/>


# Azure 기계 학습 작업 영역 만들기 및 공유

이 메뉴는 CAPS(Cortana 분석 프로세스)에서 사용하는 다양한 데이터 과학 환경을 설정하는 방법을 설명하는 항목에 연결됩니다.

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Azure 기계 학습 스튜디오를 사용하려면 기계 학습 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 작업 영역을 만들려면

1. [Microsoft Azure 클래식 포털]에 로그인합니다.

> [AZURE.NOTE] 로그인하려면 Azure 구독 관리자여야 합니다. 기계 학습 작업 영역 소유자라고 해서 [Microsoft Azure 클래식 포털]에 대한 액세스가 제공되는 것은 아닙니다. 자세한 내용은 [Azure 구독 관리자 및 작업 영역 소유자의 권한](#subscriptionvsworkspace)을 참조하세요.

2. Microsoft Azure 서비스 패널에서 **기계 학습**을 클릭합니다.

    ![기계 학습 서비스][1]

3. 창 아래쪽에서 **+새로 만들기**를 클릭합니다.
4. **데이터 서비스**, **기계 학습**, **빠른 생성**을 차례로 클릭합니다.

	![새 작업 영역의 빠른 생성][3]

5. 작업 영역에 대한 **작업 영역 이름**을 입력합니다.
6. Azure **위치**를 지정하고 기존 Azure **저장소 계정**을 입력하거나 **새 저장소 계정 만들기**를 클릭하여 새 저장소 계정을 만듭니다.
7. **기계 학습 작업 영역 만들기**를 클릭합니다.

기계 학습 작업 영역이 만들어진 후에는 **기계 학습** 페이지에 나열됩니다.

## Azure 기계 학습 작업 영역 공유

기계 학습 작업 영역이 만들어진 후에는 사용자를 작업 영역에 초대하고 작업 영역과 모든 실험에 대한 액세스를 공유할 수 있습니다. 두 개의 사용자 역할을 지원합니다.

- **사용자** - 작업 영역 사용자는 작업 영역에서 데이터 집합, 실험 및 웹 서비스를 만들기, 열기, 수정 및 삭제할 수 있습니다.
- **소유자** - 소유자는 사용자가 수행할 수 있는 작업 외에도 작업 영역에 대한 액세스와 함께 사용자를 초대, 제거 및 나열할 수 있습니다. Notebook에 대한 액세스 권한도 보유합니다.

### 작업 영역을 공유하려면
1. [Azure 기계 학습 스튜디오]에 로그인합니다.
2. 기계 학습 스튜디오 패널에서 **설정**을 클릭합니다.
3. **사용자**를 클릭합니다.
4. **더 많은 사용자 초대**를 클릭합니다.

    ![더 많은 사용자 초대][4]

5. 하나 이상의 전자 메일 주소를 입력합니다. 사용자에게는 유효한 Microsoft 계정(예: name@outlook.com) 또는 Azure Active Directory의 조직 계정만 필요합니다.
6. 선택 단추를 클릭합니다.

추가된 각 사용자는 공유 작업 영역에 로그인하는 명령으로 전자 메일을 받게 됩니다.

작업 영역을 관리하는 방법에 대한 자세한 내용은 [Azure 기계 학습 작업 영역 관리]를 참조하세요. 작업 영역을 만드는 데 문제가 발생한 경우 [문제 해결 가이드: 기계 학습 작업 영역 만들기 및 연결]을 참조하세요.

## <a name="subscriptionvsworkspace"></a>Azure 구독 관리자 및 작업 영역 소유자의 권한

다음은 Azure 구독 관리자와 작업 영역 소유자 간의 차이를 명확히 보여주는 테이블입니다.

| 작업 | Azure 구독 관리자 | 작업 영역 소유자 |
| --------------			|:------------------------:| :----------------:|
| [Microsoft Azure 클래식 포털] 액세스| 예 | 아니요 |
| 새 작업 영역 만들기 | 예 | 아니요 |
| 작업 영역 삭제 | 예 | 아니요 |
| 웹 서비스에 끝점 추가 | 예 | 아니요 |
| 웹 서비스에서 끝점 삭제 | 예 | 아니요 |
| 웹 서비스에 대한 동시성 변경 | 예 | 아니요 |
| [기계 학습 스튜디오] 액세스 | 아니요 * | 예 |


> [AZURE.NOTE] * Azure 구독 관리자는 작업 영역 소유자 권한으로 만든 작업 영역에 자동으로 추가됩니다. 그러나 단순히 Azure 구독 관리자가 되었다고 해서 해당 구독의 모든 작업 영역에 대한 액세스 권한이 부여되는 것은 아닙니다.

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Azure 기계 학습 작업 영역 관리]: machine-learning-manage-workspace.md
[문제 해결 가이드: 기계 학습 작업 영역 만들기 및 연결]: machine-learning-troubleshooting-creating-ml-workspace.md
[Azure 기계 학습 스튜디오]: https://studio.azureml.net/
[기계 학습 스튜디오]: https://studio.azureml.net/
[Microsoft Azure 클래식 포털]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0914_2016-->