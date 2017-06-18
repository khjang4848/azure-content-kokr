<properties
   pageTitle="다중 지역 도움말 문서 | Microsoft Azure"
   description="SCUS(미국 중남부) Azure 지역이 아닌 다른 Azure 지역에서 작업 영역을 만들고 웹 서비스를 게시하는 방법을 알아봅니다."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# 다중 지역 도움말 문서

이 문서에서는 다른 Azure 지역에서 작업 영역을 만들고 웹 서비스를 게시하는 방법을 설명합니다.

## 작업 영역 만들기

1. Azure 클래식 포털에 로그인합니다.

2.  **+새로 만들기** > **데이터 서비스** > **기계 학습** > **빠른 생성**을 클릭합니다. **위치**에서 다른 지역(예: **동남 아시아**)을 선택합니다. ![다중 지역 도움말 이미지 1][1]
3. 작업 영역을 선택한 다음 **기계 학습 스튜디오에 로그인**을 클릭합니다. ![다중 지역 도움말 이미지 2][2]

4. 이제 다른 작업 영역처럼 사용할 수 있는 작업 영역이 다른 지역에 생겼습니다. 작업 영역 간에 전환하려면 화면의 오른쪽 위를 봅니다. 드롭다운을 클릭하고 지역을 선택한 다음 작업 영역을 선택합니다. 모든 항목은 작업 영역 지역에 대해 로컬입니다. 예를 들어 작업 영역에서 만든 모든 웹 서비스는 작업 영역이 있는 지역과 같은 지역에 있게 됩니다. ![다중 지역 도움말 이미지 3][3]

## 갤러리에서 실험 열기

갤러리에서 실험을 열면 실험을 복사할 지역도 선택할 수 있습니다.

![다중 지역 도움말 이미지 4][4a]

## 웹 서비스 관리

프로그래밍 방식으로 웹 서비스를 관리하려면(예: 다시 학습을 위해) 지역별 주소를 사용합니다.
- https://asiasoutheast.management.azureml.net
- https://europewest.management.azureml.net

### 주의할 사항

1.	같은 지역에 속하는 작업 영역 간에만 실험을 복사할 수 있습니다. 다른 지역의 작업 영역 간에 실험을 복사해야 하는 경우 [PowerShell](http://aka.ms/amlps) commandlet [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment)를 사용하여 이 작업을 수행할 수 있습니다. 또 다른 해결 방법은 나열되지 않는 모드로 갤러리에 실험을 게시한 다음 다른 지역의 작업 영역에서 여는 것입니다.
2.	지역 선택기는 한 번에 한 지역의 작업 영역만 표시합니다. 나중에 동시에 모든 지역의 액세스할 수 있는 작업 영역의 전체 목록이 표시되도록 할 예정입니다.
3.	무료 작업 영역 또는 게스트 액세스(익명) 작업 영역은 미국 중남부에서 만들어지고 호스트됩니다. 나중에 선택한 지역에서 무료/게스트 액세스 작업 영역을 만들 수 있도록 할 예정입니다.
4.	동남 아시아의 작업 영역에서 배포된 웹 서비스는 호스팅도 동남 아시아에서 이루어집니다. 나중에 유연하게 한 지역에서 실험을 만들어 생성된 웹 서비스 끝점을 다른 지역에 배포할 수 있도록 할 예정입니다.

## 자세한 정보

[Azure 기계 학습 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)에서 질문하세요.

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=AcomDC_0914_2016-->