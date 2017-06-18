<properties
	pageTitle="기계 학습 알고리즘 선택 방법 | Microsoft Azure"
	description="클러스터링, 분류 또는 회귀 실험에서 감독 및 자율 학습에 대한 Azure 기계 학습 알고리즘을 선택하는 방법입니다."
	services="machine-learning"
	documentationCenter=""
	authors="brohrer"
	manager="jhubbard"
	editor="cgronlun"
    tags=""/>
    
<tags
	ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/09/2016"
	ms.author="brohrer;garye" />

# Microsoft Azure 기계 학습을 위한 알고리즘 선택 방법

"어떤 기계 학습 알고리즘을 사용해야 하나요?"라는 질문에 대한 대답은 항상 "상황마다 다릅니다."입니다. 데이터의 크기, 품질 및 특성에 따라 다릅니다. 대답으로 수행할 작업에 따라 다릅니다. 수학 알고리즘을 사용 중인 컴퓨터를 위한 명령어로 변환하는 방법에 따라 다릅니다. 시간이 얼마나 있는지에 따라 다릅니다. 아무리 숙련된 과학자라고 해도 대부분의 과학자는 직접 시도해보기 전에는 어떤 알고리즘이 최적으로 수행된다고 단언할 수 없습니다.

## 기계 학습 알고리즘 치트 시트

**Microsoft Azure 기계 학습 알고리즘 치트 시트**를 사용하면 알고리즘의 Microsoft Azure 기계 학습 라이브러리에서 예측 분석 솔루션에 대해 올바른 기계 학습 알고리즘을 선택할 수 있습니다. 이 문서에서는 사용 방법을 안내합니다.

> [AZURE.NOTE] 치트 시트를 다운로드하고 이 문서를 따르려면 [Microsoft Azure 기계 학습 스튜디오용 기계 학습 알고리즘 치트 시트](machine-learning-algorithm-cheat-sheet.md)로 이동합니다.

이 치트 시트는 Azure 기계 학습 스튜디오에서 시작할 알고리즘을 선택하려는 재학생 수준의 기계 학습 지식을 지닌 초급 데이터 과학자라는 매우 구체적인 대상을 염두에 두고 작성되었습니다. 따라서 일반화되고 지나치게 단순화되었을 수 있으나 안전한 방향으로 사용자를 안내합니다. 여기에 나와 있는 것 외에도 수많은 알고리즘이 있습니다. Azure 기계 학습이 보다 완전하게 제공되는 방법을 더 포함하도록 확장됨에 따라 알고리즘을 더 추가할 예정입니다.

이러한 권장 사항은 수많은 데이터 과학자 및 기계 학습 전문가로부터 수집한 피드백 및 팁입니다. 우리는 모든 내용에 동의한 것은 아니지만 부분적으로나마 합의에 도달하기 위해 의견을 조율하는 데 노력해왔습니다. 대부분의 의견 충돌은 "상황에 따라 다릅니다…"라는 문구로 시작됩니다.

### 치트 시트를 사용하는 방법

차트에서 "For *&lt;path label&gt;* use *&lt;algorithm&gt;*."로 표시된 경로와 알고리즘 레이블을 확인합니다. 예를 들어, "For *speed* use *two class logistic regression*."과 같습니다. 경우에 따라 두 개 이상의 분기가 적용됩니다. 완벽하게 맞는 것이 없는 경우도 있습니다. 경험에 기반한 권장 방법으로 제공되는 것이므로 정확하지 않은 것에 대해 걱정하지 않아도 됩니다. 수많은 데이터 과학자가 최적의 알고리즘을 찾는 가장 확실한 방법은 모든 알고리즘을 시도해보는 것이라고 말합니다.

다음은 동일한 데이터에 대해 여러 알고리즘을 시도하고 그 결과를 비교하는 실험에 대한 [Cortana Intelligence 갤러리](http://gallery.cortanaintelligence.com/)의 예제입니다. [다중 클래스 분류자 비교: 문자 인식](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] 기계 학습 스튜디오의 기능을 개략적으로 제공하는 다이어그램을 다운로드하고 인쇄하려면 [Azure 기계 학습 스튜디오 기능 개요](machine-learning-studio-overview-diagram.md)를 참조하세요.

## 기계 학습의 다양한 특징

### 감독

감독 학습 알고리즘은 예제 집합을 토대로 예측합니다. 예를 들어 향후 주가 가격에 대한 위험 추측에 주가 기록을 사용할 수 있습니다. 학습에 사용된 각 예제는 관련 값으로 레이블(이 경우, 주가)이 표시됩니다. 감독 학습 알고리즘은 이러한 값 레이블에서 패턴을 찾습니다. 요일, 계절, 회사의 재무 데이터, 업종, 문제가 되는 지정학적 사건의 유무 등 관련될 수 있는 모든 정보를 사용할 수 있으며 각 알고리즘은 다양한 유형의 패턴을 찾습니다. 알고리즘이 가능한 최적의 패턴을 찾은 후에는 이러한 패턴을 사용하여 레이블이 지정되지 않은 테스트 데이터(오늘 가격)에 대해 예측을 합니다.

이는 널리 사용되고 유용한 기계 학습 유형입니다. 한 가지 예외를 제외하고, Azure 기계 학습의 모든 모듈은 감독 학습 알고리즘입니다. Azure 기계 학습 내에서는 분류, 회귀 및 이상 감지의 여러 특정 유형의 감독 학습이 표시됩니다.

* **분류**. 범주를 예측하는 데 데이터를 사용하는 감독 학습을 분류라고도 합니다. '고양이' 또는 '개' 그림으로 이미지를 할당하는 경우가 해당합니다. 선택 항목이 두 가지뿐인 경우 **2클래스** 또는 **이항 분류**라고 합니다. NCAA March Madness(미국 대학농구 토너먼트전) 대회의 승자를 예측하는 경우처럼 더 많은 범주가 있는 경우 이 문제를 **다중 클래스 분류**라고 합니다.

* **회귀**. 주가와 같은 값을 예측하는 경우 감독 학습을 회귀라고 합니다.

* **이상 감지**. 단순히 비정상적인 데이터 요소를 식별하는 것이 목표인 경우도 있습니다. 예를 들어 이상 금융 거래 감지에서 매우 비정상적인 신용 카드 지출 패턴이 의심 대상입니다. 가능한 변형 형태는 너무 많지만 학습 예제는 극히 적으므로 사기성 활동의 형태를 학습하기는 쉽지 않습니다. 이상 감지를 수행하는 방법은 단순히 정상적인 활동의 형태(사기성이 없는 과거 트랜잭션 사용)를 학습하고 여기에서 크게 벗어나는 활동을 식별하는 것입니다.

### 자율

자율 학습에서는 데이터 요소에 연결된 레이블이 없습니다. 대신, 자율 학습 알고리즘의 목표는 어떤 방식으로든 데이터를 구성하거나 해당 구조를 설명하는 것입니다. 이는 클러스터로 그룹화하거나 복잡한 데이터가 보다 단순하게 또는 조직화되어 표시되도록 데이터를 바라보는 다양한 방법을 찾는 것을 의미할 수 있습니다.

### 보충 학습

보충 학습에서는 알고리즘이 각 데이터 요소에 대한 응답으로 작업을 선택합니다. 또한 학습 알고리즘은 짧은 시간 후에 결정이 얼마나 효율적이었는지를 나타내는 보상 신호를 받습니다. 이 신호에 따라 알고리즘은 가장 높은 보상을 달성하기 위해 해당 전략을 수정합니다. 현재 Azure 기계 학습에는 보충 학습 알고리즘 모듈이 없습니다. 보충 학습은 지정 시간 센서 판독값 집합이 한 데이터 요소이며 알고리즘으로 로봇의 다음 동작을 선택해야 하는 로봇 공학에서 일반적입니다. 사물 인터넷의 응용 프로그램에 적합한 학습이기도 합니다.

## 알고리즘 선택 시 고려 사항

### 정확도

항상 가장 정확한 응답을 얻어야 하는 것은 아닙니다. 용도에 따라 근사치가 적절한 경우도 있습니다. 이 경우 보다 근접한 방법을 고수하여 처리 시간을 크게 단축할 수 있습니다. 보다 근접한 방법의 다른 이점은 기본적으로 [과잉 맞춤](https://youtu.be/DQWI1kvmwRg) 경향을 피할 수 있다는 것입니다.

### 학습 시간

모델을 학습하는 데 필요한 시간 또는 분은 알고리즘에 따라 크게 다릅니다. 학습 시간은 대체로 정확도와 밀접하게 연결되어 일반적으로 서로를 수반합니다. 또한 일부 알고리즘은 다른 항목보다 데이터 요소 수에 보다 민감합니다. 시간이 제한된 경우, 특히 데이터 집합이 큰 경우 알고리즘 선택 항목을 유도할 수 있습니다.

### 선형성

많은 기계 학습 알고리즘에서 선형성을 활용합니다. 선형 분류 알고리즘은 클래스를 직선(또는 고차원의 아날로그)으로 구분할 수 있다고 가정합니다. 여기에는 로지스틱 회귀 및 지원 벡터 컴퓨터가 포함됩니다(Azure 기계 학습에 구현됨). 선형 회귀 알고리즘은 데이터가 직선을 따르는 경향이 있다고 가정합니다. 이러한 가정은 일부 문제에 대해서는 그다지 나쁘지 않지만 어떤 면에서는 정확도가 떨어질 수 있습니다.

![비선형 클래스 경계][1]

***비선형 클래스 경계*** *- 선형 분류 알고리즘에 의존하며 결과의 정확도가 떨어짐*

![비선형 추세 반영 데이터][2]

***선형 추세 반영 데이터*** *- 선형 회귀 방법을 사용하면 필요한 것보다 훨씬 많은 오류가 생성됨*

위험성에도 불구하고, 선형 알고리즘은 공격에 대한 최전선으로 매우 널리 사용됩니다. 알고리즘 방식으로 간단하고 학습 시간이 빠른 경향이 있습니다.

### 매개 변수 수

매개 변수는 데이터 과학자가 알고리즘을 설정할 때 전환하기 위한 노브입니다. 허용 오차, 반복 횟수 또는 알고리즘이 동작하는 방식의 변형 간 옵션 등 알고리즘의 동작에 영향을 주는 숫자입니다. 알고리즘의 학습 시간 및 정확도는 적합한 설정에 따라 크게 좌우될 수 있습니다. 일반적으로 매개 변수가 많은 알고리즘의 경우 적절한 조합을 찾기 위해 대부분 시행착오와 오류를 겪어야 합니다.

또는 Azure 기계 학습에는 [매개 변수 스위핑](machine-learning-algorithm-parameters-optimize.md) 모듈 블록이 있어 선택한 세분성에서 모든 매개 변수 조합을 자동으로 시도합니다. 이 방법은 매개 변수 공간을 스팬하는 데는 좋지만 모델을 학습하는 데 필요한 시간이 매개 변수 수에 따라 기하급수적으로 증가한다는 단점이 있습니다.

장점은 일반적으로 매개 변수를 많이 포함할수록 알고리즘의 유연성이 향상된다는 것입니다. 또한 정확도도 매우 뛰어날 수 있습니다. 이러한 조건 하에서 매개 변수 설정의 적합한 조합을 얻을 수 있습니다.

### 기능 수

특정 데이터 형식의 경우 데이터 요소에 비해 기능 수가 매우 클 수 있습니다. 보통 genetics 또는 텍스트 데이터가 그렇습니다. 기능 수가 많으면 일부 학습 알고리즘이 교착 상태에 빠질 수 있으며 이로 인해 학습 시간이 상당히 길어집니다. 지원 벡터 컴퓨터는 특히 다음 사례에 적합합니다(아래 참조).

### 특수 사례

일부 학습 알고리즘에서는 데이터 구조 또는 원하는 결과에 대해 특수한 가정을 합니다. 사용자 요구에 적합한 것을 찾을 수 있는 경우 보다 유용한 결과, 보다 정확한 예측 또는 단축된 교육 시간을 제공할 수 있습니다.

|**알고리즘**|**정확도**|**학습 시간**|**선형성**|**매개 변수**|**참고 사항**|
|---|:---:|:---:|:---:|:---:|---|
|**2클래스 분류**| | | | | |
|[로지스틱 회귀](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |●|●|5| |
|[의사 결정 포리스트](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[의사 결정 정글](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|적은 메모리 공간|
|[향상된 의사 결정 트리](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|큰 메모리 공간|
|[신경망](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[추가 사용자 지정 가능](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[평균 퍼셉트론](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[지원 벡터 컴퓨터](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|큰 기능 집합의 적합|
|[로컬 심층 지원 벡터 컴퓨터](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|큰 기능 집합의 적합|
|[Bayes 지점 컴퓨터](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**다중 클래스 분류**| | | | | |
|[로지스틱 회귀](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[의사 결정 포리스트](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[의사 결정 정글](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|적은 메모리 공간|
|[신경망](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[추가 사용자 지정 가능](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|선택한 2클래스 메서드의 속성 참조|
|**회귀**| | | | | |
|[선형](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesian 선형](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[의사 결정 포리스트](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[향상된 의사 결정 트리](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|큰 메모리 공간|
|[빠른 포리스트 분위수](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|포인트 예측 대신 분포|
|[신경망](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[추가 사용자 지정 가능](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[포아송](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|기술적으로 로그-선형. 개수 예측용|
|[서수](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|순위 예측용|
|**이상 감지**| | | | | |
|[지원 벡터 컴퓨터](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|특히 큰 기능 집합에 적합|
|[PCA 기반 이상 감지](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|클러스터링 알고리즘|


**알고리즘 속성:**

**●** - 뛰어난 정확도, 빠른 교육 시간 및 선형성 사용 나타냄

**○** - 양호한 정확도 및 보통의 교육 시간 나타냄

## 알고리즘 참고 사항

### 선형 회귀

이전에 설명한 것처럼 [선형 회귀](https://msdn.microsoft.com/library/azure/dn905978.aspx)는 데이터 집합에 대한 선(또는 평면, 초평면(hyperplane))에 적합합니다. 자주 사용되고 간단하며 빠르지만 일부 문제에 대해서는 지나치게 단순화될 수 있습니다. [선형 회귀 자습서](machine-learning-linear-regression-in-azure.md)는 여기를 확인하세요.

![선형 추세 반영 데이터][3]

***선형 추세 반영 데이터***

### 로지스틱 회귀

이름에 혼동을 줄 수 있는 '회귀'가 포함되어 있지만, 로지스틱 회귀 분석은 실제로 [2클래스](https://msdn.microsoft.com/library/azure/dn905994.aspx) 및 [다중 클래스](https://msdn.microsoft.com/library/azure/dn905853.aspx) 분류를 위한 강력한 도구입니다. 빠르고 단순합니다. 직선 대신 'S' 모양 곡선을 사용한다는 사실 때문에 데이터를 그룹으로 나누는 데 적합합니다. 로지스틱 회귀는 선형 클래스 경계를 제공하므로 이를 사용할 때는 선형 근사값이 수락할 수 있는 것인지 확인해야 합니다.

![하나의 기능을 포함하는 2클래스 데이터에 대한 로지스틱 회귀][4]

***한 가지 기능만 있는 2클래스 데이터에 대한 로지스틱 회귀 분석*** *- 클래스 경계가 로지스틱 곡선이 두 클래스에 가까운 지점임*

### 트리, 포리스트 및 정글

의사 결정 포리스트([회귀](https://msdn.microsoft.com/library/azure/dn905862.aspx), [2클래스](https://msdn.microsoft.com/library/azure/dn906008.aspx) 및 [다중 클래스](https://msdn.microsoft.com/library/azure/dn906015.aspx)), 의사 결정 정글([2클래스](https://msdn.microsoft.com/library/azure/dn905976.aspx) 및 [다중 클래스](https://msdn.microsoft.com/library/azure/dn905963.aspx)) 및 향상된 의사 결정 트리([회귀](https://msdn.microsoft.com/library/azure/dn905801.aspx) 및 [2클래스](https://msdn.microsoft.com/library/azure/dn906025.aspx))는 모두 의사 결정 트리, 기본적인 기계 학습 개념을 기반으로 합니다. 의사 결정 트리에는 다양한 변형이 있지만 수행하는 작업은 동일합니다. 기능 공간을 대부분 같은 레이블을 포함하는 하위 지역으로 세분화합니다. 분류 또는 회귀를 수행 중인지 여부에 따라 일관된 범주 또는 상수 값의 하위 지역일 수 있습니다.

![의사 결정 트리가 기능 공간 세분화][5]

***의사 결정 트리는 기능 공간을 대략적으로 균일한 값의 하위 지역으로 세분화함***

기능 공간은 임의의 작은 하위 지역으로 세분화할 수 있으므로 극단적인 과잉 맞춤의 예로 하위 지역당 하나의 데이터 요소를 포함할 만큼 미세하게 분할하는 것을 구상하기 쉽습니다. 이 문제를 방지하기 위해 트리가 상호 관련되지 않은 특수한 수치 연산을 고려하여 큰 트리 집합을 생성합니다. 이 "의사 결정 포리스트"의 평균이 과잉 맞춤을 피하는 트리입니다. 의사 결정 포리스트는 많은 양의 메모리를 사용할 수 있습니다. 의사 결정 정글은 약간 긴 학습 시간 대신 메모리 사용량이 적은 변형된 형태입니다.

향상된 의사 결정 트리는 세분화할 수 있는 횟수 및 각 하위 지역에 허용되는 데이터 요소 수를 제한하여 과잉 맞춤을 피합니다. 알고리즘은 트리 시퀀스를 생성하며 각각은 이전 트리가 남긴 오류에 대한 보정을 학습합니다. 그 결과 많은 양의 메모리를 사용하는 경향이 있는 매우 정확한 학습자가 나타납니다. 자세한 기술적인 설명은 [Friedman의 원본 문서](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf)를 참조하세요.

[빠른 포리스트 분위수 회귀](https://msdn.microsoft.com/library/azure/dn913093.aspx)는 일반(중앙값) 값뿐만 아니라 분위수 형태에 대한 분포도 알고 싶은 하위 지역 내의 특수한 사례에 대한 변형된 의사 결정 트리입니다.

### 신경망 및 퍼셉트론

신경망은 [다중 클래스](https://msdn.microsoft.com/library/azure/dn906030.aspx), [2클래스](https://msdn.microsoft.com/library/azure/dn905947.aspx) 및 [회귀](https://msdn.microsoft.com/library/azure/dn905924.aspx)를 반영한 두뇌 영감 학습 알고리즘입니다. 무제한의 다양성을 포함하지만 Azure 기계 학습 내의 신경망은 모두 방향성 비순환 그래프 형태입니다. 따라서 입력 기능은 출력으로 전환되기 전에 계층 시퀀스를 통해 앞으로(뒤로는 불가능) 전달됩니다. 각 계층에서 입력은 다양한 조합으로 가중치가 부여되고 합계를 구한 후 다음 계층으로 전달됩니다. 이러한 단순한 조합의 계산으로 정교한 클래스 경계 및 데이터 추세를 겉보기에는 마법처럼 간단하게 학습할 수 있습니다. 이러한 종류의 다계층화된 네트워크는 수많은 기술 보고 및 공상 과학을 뒷받침하는 "심층 학습"을 수행합니다.

하지만 이러한 높은 성능을 위해서는 대가가 따릅니다. 신경망은 학습 시간이 길며 특히 많은 기능을 포함하는 큰 데이터 집합인 경우 시간이 오래 걸릴 수 있습니다. 또한 대부분의 알고리즘보다 더 많은 매개 변수를 포함하므로 매개 변수 스위핑으로 학습 시간은 엄청나게 길어집니다. [자신의 고유한 네트워크 구조를 지정](http://go.microsoft.com/fwlink/?LinkId=402867)하고 싶어하는 과잉 성취자에게 가능성은 무궁무진합니다.

![신경망으로 학습된 경계][6]
---------------------------

***신경망으로 학습하는 경계는 복잡하고 불규칙할 수 있습니다.***

[2클래스 평균 퍼셉트론](https://msdn.microsoft.com/library/azure/dn906036.aspx)은 급증하는 학습 시간에 대한 신경망의 해답입니다. 2클래스 평균 퍼셉트론은 선형 클래스 경계를 제공하는 네트워크 구조를 사용합니다. 오늘날의 기준으로 거의 원형에 가깝지만 오랫동안 견고하게 작동해왔으며 작기 때문에 신속한 학습이 가능합니다.

### SVM

SVM(Support Vector Machine)은 가능한 넓은 여백으로 클래스를 구분하는 경계를 찾습니다. 두 클래스를 확실하게 구분할 수 없는 경우 알고리즘이 가능한 최적의 경계를 찾습니다. Azure 기계 학습에 나와 있는 것처럼 [2클래스 SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx)은 직선만으로 이 작업을 수행합니다. (SVM-speak에서 선형 커널을 사용합니다.) 이 선형 근사치를 생성하므로 매우 신속하게 실행할 수 있습니다. 텍스트 또는 유전자처럼 기능 중심의 데이터에서 특히 유용합니다. 이러한 경우 SVM은 적절한 수준의 메모리를 필요로 하면서도 대부분의 다른 알고리즘보다 과잉 맞춤을 피하면서 클래스를 보다 신속하게 구분할 수 있습니다.

![지원 벡터 컴퓨터 클래스 경계][7]

***일반적인 지원 벡터 컴퓨터 클래스 경계는 두 클래스를 구분하는 여백을 최대화합니다.***

Microsoft Research의 다른 제품인 [2클래스 로컬 심층 SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx)은 선형 버전의 속도 및 메모리 효율성을 대부분 유지하는 SVM의 비선형 변형입니다. 선형 방식으로 정확하고 충분한 해답을 얻을 수 없는 경우 적합합니다. 개발자는 문제를 여러 개의 작은 선형 SVM 문제로 분석하여 신속하게 처리했습니다. 이 트릭을 수행하는 방법에 대한 자세한 내용은 [전체 설명](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf)을 읽으세요.

비선형 SVM의 적절한 확장을 사용하여 [1클래스 SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx)은 전체 데이터 집합을 긴밀하게 요약하는 경계를 그립니다. 이것은 이상 감지에 유용합니다. 경계를 멀리 벗어나는 모든 새로운 데이터 요소는 비정상적이며 주목할 만합니다.

### Bayesian 메서드

Bayesian 메서드는 과잉 맞춤을 방지하는 매우 뛰어난 품질을 자랑합니다. 대답에 대한 가능한 분포에 대해 사전에 몇 가지 가정을 하여 이 작업을 수행합니다. 이 방법에 대한 다른 부산물은 매우 적은 매개 변수입니다. Azure 기계 학습은 분류([2클래스 Bayes 지점 컴퓨터](https://msdn.microsoft.com/library/azure/dn905930.aspx)) 및 회귀([Bayesian 선형 회귀](https://msdn.microsoft.com/library/azure/dn906022.aspx)) 모두에 대해 두 Bayesian 알고리즘을 포함합니다. 여기서는 데이터를 분할하거나 직선에 맞출 수 있다고 가정합니다.

역사를 거슬러 보면, Bayes 지점 컴퓨터는 Microsoft Research에서 개발되었습니다. 그 이면에는 매우 뛰어난 이론적인 작업을 포함하고 있습니다. 관심이 있는 학생은 [JMLR의 원본 문서](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf)와 [Chris Bishop의 통찰력 있는 블로그](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)를 확인하세요.

### 특수화된 알고리즘

매우 구체적인 목표가 있다면 운이 좋을 수 있습니다. Azure 기계 학습 컬렉션 내에는 순위 예측([서수 회귀](https://msdn.microsoft.com/library/azure/dn906029.aspx)), 개수 예측([포아송 회귀](https://msdn.microsoft.com/library/azure/dn905988.aspx)) 및 이상 감지(하나는 [주체 구성 요소 분석](https://msdn.microsoft.com/library/azure/dn913102.aspx)을 기반으로 하고 하나는 [지원 벡터 컴퓨터](https://msdn.microsoft.com/library/azure/dn913103.aspx)를 기반으로 함)에 특수화된 알고리즘이 있습니다. 그리고 단독의 클러스터링 알고리즘도 있습니다([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![PCA 기반 이상 감지][8]

***PCA 기반 이상 감지*** *- 정형화된 분포에 속하는 대부분의 데이터의 경우 해당 분포에서 크게 벗어나는 지점이 의심 대상입니다*

![K-means를 사용하여 그룹화된 데이터 집합][9]

***K-means를 사용하여 데이터 집합을 5개 클러스터로 그룹화합니다.***

또한 N-클래스 분류 문제를 N-1 2클래스 분류 문제로 나누는 앙상블 [one-v-all 다중 클래스 분류자](https://msdn.microsoft.com/library/azure/dn905887.aspx)도 있습니다. 정확도, 학습 시간 및 선형성 속성은 사용된 2클래스 분류자에 의해 결정됩니다.

![2클래스 분류자를 결합하여 3클래스 분류자 형성][10]

***2클래스 분류자 쌍을 결합하여 3클래스 분류자를 형성합니다.***

Azure 기계 학습에는 [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf)이라는 제목의 강력한 기계 학습 프레임워크에 대한 액세스도 포함됩니다. VW는 분류 및 회귀를 모두 학습할 수 있으며 부분적으로 레이블이 지정되지 않은 데이터에서도 학습이 가능하므로 여기에서 분류를 거부합니다. 다양한 학습 알고리즘, 손실 함수 및 최적화 알고리즘 중 하나를 사용하도록 구성할 수 있습니다. 기초부터 효율적, 병렬, 초고속 형태로 설계되었습니다. 적은 작업으로 엄청나게 큰 기능 집합을 처리합니다. Microsoft Research의 John Langford가 시작하여 진행한 VW는 스톡 카 알고리즘 분야에서 포뮬러 원(Formula One) 엔트리입니다. 모든 문제가 VW에 적합하지는 않지만 해당하는 경우 인터페이스에 대한 학습 곡선을 따른다면 도움이 될 수 있습니다. 또한 여러 언어로 된 [독립 실행형 오픈 소스 코드](https://github.com/JohnLangford/vowpal_wabbit)도 제공됩니다.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png

<!---HONumber=AcomDC_0914_2016-->