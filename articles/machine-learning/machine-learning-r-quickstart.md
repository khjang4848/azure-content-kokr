<properties
	pageTitle="기계 학습을 위한 R 언어의 빠른 시작 자습서 | Microsoft Azure"
	description="이 R 프로그래밍 자습서를 사용하여 신속하게 Azure 기계 학습 스튜디오에서 R 언어를 사용하여 예측 솔루션을 만듭니다."
	keywords="빠른 시작, r 언어, r 프로그래밍 언어, r 프로그래밍 자습서"
	services="machine-learning"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# Azure 기계 학습에 대한 프로그래밍 언어는 R에 대한 빠른 시작 자습서

Stephen F Elston, Ph.D.

##  소개

이 빠른 시작 자습서는 R 프로그래밍 언어를 사용하여 Azure 기계 학습을 빠르게 확장하는 데 도움이 됩니다. 이 R 프로그래밍 자습서를 따라 Azure 기계 학습 내에서 R 코드를 만들고 테스트하고 실행합니다. 자습서를 진행하면서 Azure 기계 학습에서 R 언어를 사용하여 완벽한 예측 솔루션이 만들어집니다.

Microsoft Azure 기계 학습에는 강력한 기계 학습 및 데이터 조작 모듈이 많이 포함되어 있습니다. 강력한 R 언어는 분석의 대표 언어라고 표현되어 왔습니다. R을 사용하여 Azure 기계 학습의 분석 및 데이터 조작을 확장할 수 있습니다. 이렇게 하면 R의 유연성 및 심층 분석을 통해 쉽게 Azure 기계 학습을 배포하고 확장할 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


###예측 및 데이터 집합

예측은 널리 활용되고 매우 유용한 분석 방법입니다. 이는 계절성 품목의 매출 예측 및 최적의 재고 수준 결정에서부터 거시 경제 변수 예측에 이르기까지 일반적으로 사용됩니다. 일반적으로 예측은 시계열 모델을 통해 수행됩니다.

시계열 데이터는 값에 시간 인덱스가 있는 데이터입니다. 시간 인덱스는 예를 들어 매월 또는 매분처럼 규칙적이거나 불규칙적일 수 있습니다. 시계열 모델은 시계열 데이터를 기반으로 합니다. R 프로그래밍 언어는 시계열 데이터를 위한 유연한 프레임워크 및 광범위한 분석이 포함되어 있습니다.

이 빠른 시작 가이드에서는 캘리포니아 유제품 생산 및 가격 데이터를 사용합니다. 이러한 데이터에는 기준 상품, 즉 몇몇 유제품 생산 및 유지방 가격에 대한 월별 정보가 포함됩니다.

이 문서에 사용된 데이터 및 R 스크립트는 [여기서 다운로드][download]할 수 있습니다. 이 데이터는 원래 위스콘신 대학교의 http://future.aae.wisc.edu/tab/production.html에서 사용할 수 있는 정보에서 만들어졌습니다.

###	조직

몇몇 단계를 통해 Azure 기계 학습 환경에서 분석 및 데이터 조작 R 코드를 작성, 테스트 및 실행하는 방법을 배우게 됩니다.

* 먼저 Azure 기계 학습 스튜디오 환경에서의 R 언어 사용에 대한 기본 사항을 살펴봅니다.

* 그 다음 Azure 기계 학습 환경에서 데이터, R 코드, 그래픽의 I/O에 대해 다양한 측면으로 알아봅니다.

* 그런 다음 데이터 정리 및 변환용 코드를 만들어 예측 솔루션의 첫 번째 부분을 만듭니다.

* 준비된 데이터를 가지고 데이터 집합에서 여러 변수 간의 상관관계를 분석합니다.

* 마지막으로 우유 생산의 계절성 시계열 예측 모델을 만듭니다.

##<a id="mlstudio"></a>기계 학습 스튜디오에서 R 언어와 상호 작용

이 섹션에서는 기계 학습 스튜디오 환경에서 R 프로그래밍 언어와 상호 작용하는 방법에 대한 일부 기본적인 과정을 안내합니다. R 언어는 Azure 기계 학습 환경에서 사용자 지정된 분석 및 데이터 조작 모듈을 만드는 강력한 도구를 제공합니다.

작은 규모로 R 코드를 개발, 테스트 및 디버그하기 위해 RStudio를 사용합니다. 그런 다음 이 코드를 잘라서 기계 학습 스튜디오에서 [R 스크립트 실행][execute-r-script] 모듈에 붙여넣어 실행할 준비를 합니다.

###R 스크립트 실행 모듈

기계 학습 스튜디오에서 R 스크립트는 [R 스크립트 실행][execute-r-script] 모듈 내에서 실행됩니다. 그림 1에서는 기계 학습 스튜디오에서의 [R 스크립트 실행 모듈][execute-r-script] 예를 보여 줍니다.

 ![R 프로그래밍 언어: R 스크립트 실행 모듈이 기계 학습 스튜디오에서 선택됩니다.][1]

*그림 1. 선택된 R 스크립트 실행 모듈을 보여 주는 기계 학습 스튜디오 환경*

그림 1을 참고하여 [R 스크립트 실행][execute-r-script] 모듈 작업을 위한 몇 가지 주요한 기계 학습 스튜디오 환경을 살펴보겠습니다.

- 가운데 창에 실험에 사용된 모듈이 표시됩니다.

- 오른쪽 창의 윗 부분은 R 스크립트를 보고 편집하는 창입니다.

- 오른쪽 창의 아래 부분에서는 [R 스크립트 실행][execute-r-script]의 일부 속성을 보여 줍니다. 이 창에서 적절한 지점을 클릭하여 오류 및 출력 로그를 볼 수 있습니다.

물론 이 문서의 나머지 부분에서 [R 스크립트 실행][execute-r-script]에 대해 더 자세히 설명합니다.

복잡한 R 함수를 사용할 경우에는 RStudio에서 편집, 테스트 및 디버그하는 것이 좋습니다. 모든 소프트웨어 개발에서와 마찬가지로 코드를 점차적으로 확장하고 간단한 작은 테스트 사례에서 테스트하세요. 그런 다음 함수를 잘라내어 [R 스크립트 실행][execute-r-script] 모듈의 R 스크립트 창에 붙여넣습니다. 이 방법을 사용하면 RStudio IDE(통합 개발 환경)와 Azure 기계 학습의 강력한 기능을 모두 이용할 수 있습니다.

####R 코드 실행

[R 스크립트 실행][execute-r-script] 모듈의 R 코드는 **실행** 단추를 클릭하여 실험을 실행할 때 실행됩니다. 실행이 완료되면 [R 스크립트 실행][execute-r-script] 아이콘에 확인 표시가 나타납니다.

####Azure 기계 학습용 방어 R 코딩

Azure 기계 학습을 사용하여 웹 서비스용 R 코드를 개발하는 경우 코드가 예기치 않은 데이터 입력 및 예외를 처리할 방법을 확실히 계획해야 합니다. 정확성을 유지하기 위해 표시된 대부분의 코드 예제에서 확인 또는 예외를 처리하는 데 필자는 거의 관여하지 않았습니다. 하지만 계속 진행하면서 R의 예외 처리 기능을 사용하는 몇 가지 함수 예를 제공하겠습니다.

R 예외 처리를 보다 완벽하게 수행하려면 [부록 B - 추가 정보](#appendixb)에 나열된 Wickham의 저서에서 해당 섹션을 읽어보시기 바랍니다


####기계 학습 스튜디오에서 R 디버깅 및 테스트

다시 한 번 강조하지만 RStudio에서 작은 규모로 R 코드를 테스트 및 디버그하는 것이 좋습니다. 그러나 [R 스크립트 실행][execute-r-script]에서 R 코드 문제를 추적해야 하는 경우도 있습니다. 또한 기계 학습 스튜디오에서 결과를 확인하는 것이 좋습니다.

R 코드 및 Azure 기계 학습 플랫폼에서의 실행 출력은 주로 output.log에서 찾을 수 있습니다. 일부 추가 정보는 error.log에 표시됩니다.

R 코드를 실행하는 동안 기계 학습 스튜디오에서 오류가 발생하면 제일 먼저 error.log를 살펴봐야 합니다. error.log에는 오류 메시지를 이해하고 수정하는 데 도움이 되는 유용한 오류 메시지가 포함될 수 있습니다. error.log를 보려면 오류가 포함된 [R 스크립트 실행][execute-r-script]의 **속성 창**에서 **오류 로그 보기**를 클릭하세요.

예를 들어 [R 스크립트 실행][execute-r-script] 모듈에서 다음 R 코드를 정의되지 않은 변수 y와 함께 실행했습니다.

	x <- 1.0
	z <- x + y

이 코드가 실행되지 않아 결국 오류가 발생합니다. **속성 창**에서 **오류 로그 보기**를 클릭하면 그림 2와 같은 화면이 나타납니다.

  ![오류 메시지 팝업][2]

*그림 2. 오류 메시지 팝업*

R 오류 메시지를 보려면 output.log에서 살펴봐야 할 것 같습니다. [R 스크립트 실행][execute-r-script]을 클릭한 다음 **속성 창** 오른쪽에서 **output.log** 보기 항목을 클릭하세요. 새 브라우저 창이 열리고 다음이 표시됩니다.


    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found
    
    
    object 'y' not found
    ----------- End of error message from R -----------

이 오류 메시지는 특별한 사항 없이 명확하게 문제를 식별합니다.

R의 개체 값을 검사하기 위해 output.log 파일에 해당 값을 인쇄할 수 있습니다. 개체 값 검사 규칙은 기본적으로 대화형 R 세션에서와 같습니다. 예를 들어 줄에 변수 이름을 입력하면 해당 개체 값이 output.log 파일에 인쇄됩니다.

####기계 학습 스튜디오의 패키지

Azure 기계 학습은 350개가 넘는 사전 설치된 R 언어 패키지를 함께 제공합니다. [R 스크립트 실행][execute-r-script] 모듈에서 다음 코드를 사용하여 사전 설치된 패키지 목록을 검색할 수 있습니다.

	data.set <- data.frame(installed.packages())
	maml.mapOutputPort("data.set")

이 코드의 마지막 줄이 지금 이해되지 않아도 괜찮습니다. 이 문서의 나머지 부분에서 Azure 학습 환경에서의 R 사용에 대해 광범위하게 설명됩니다.

###	RStudio 소개

RStudio는 R용으로 널리 사용되는 IDE입니다. 이 빠른 시작 가이드에서 사용되는 일부 R 코드는 RStudio를 사용하여 편집, 테스트, 디버그합니다. R 코드를 테스트하여 준비가 되면 RStudio 편집기에서 잘라내어 기계 학습 스튜디오의 [R 스크립트 실행][execute-r-script] 모듈에 붙여넣기만 하면 됩니다.

데스크톱 컴퓨터에 R 프로그래밍 언어가 설치되어 있지 않으면 지금 설치하는 것이 좋습니다. 오픈 소스 R 언어는 광범위한 R 보관 네트워크 또는 [http://www.r-project.org/](http://www.r-project.org/)의 CRAN에서 무료로 다운로드할 수 있습니다. Windows, MacOS, Linux/UNIX용 다운로드가 있습니다. 인접한 미러를 선택하고 다운로드 지침을 따르세요. 또한 CRAN에는 유용한 분석 및 데이터 조작 패키지가 풍부하게 들어 있습니다.

RStudio를 처음 사용하는 경우에는 데스크톱 버전을 다운로드하고 설치해야 합니다. http://www.rstudio.com/products/RStudio/에서 Windows, Mac OS 및 Linux/UNIX용 다운로드를 찾을 수 있습니다. 제공된 지침에 따라 데스크톱 컴퓨터에 RStudio를 설치하세요.

RStudio에 대한 자습서는 https://support.rstudio.com/hc/sections/200107586-Using-RStudio에 있습니다.

[부록 A][appendixa]에 RStudio 사용에 대한 추가 정보가 일부 제공됩니다.

##<a id="scriptmodule"></a>R 스크립트 실행 모듈의 데이터 입출력 가져오기

이 섹션에서는 [R 스크립트 실행][execute-r-script] 모듈의 데이터를 입력 및 출력하는 방법을 설명합니다. [R 스크립트 실행][execute-r-script] 모듈의 내부 및 외부로 읽은 다양한 데이터 형식을 처리하는 방법을 검토합니다.

이 섹션의 전체 코드는 앞서 다운로드한 zip 파일에 들어 있습니다.

###기계 학습 스튜디오에서 데이터 로드 및 확인

####<a id="loading"></a>데이터 집합 로드

**csdairydata.csv** 파일을 Azure 기계 학습 스튜디오로 로드하여 시작합니다.

- Azure 기계 학습 스튜디오 환경을 시작합니다.

- 화면의 왼쪽 아래에 있는 __+ 새로 만들기__를 클릭하고 **데이터 집합**을 선택합니다.

- **로컬 파일에서**를 선택한 다음 **찾아보기**를 선택하여 파일을 선택합니다.

- **Generic CSV file with header (.csv)(헤더가 포함된 일반 CSV 파일(.csv))**를 데이터 집합 형식으로 선택했는지 확인합니다.

- 확인 표시를 클릭합니다.

- 데이터 집합을 업로드한 후 **데이터 집합** 탭을 클릭하여 새 데이터 집합을 확인해야 합니다.

####실험 만들기

이제 기계 학습 스튜디오에 일부 데이터가 있으므로 분석을 수행하기 위한 실험을 만들어야 합니다.

- 왼쪽 아래에 있는 __+ 새로 만들기__를 클릭하고 **실험**, **빈 실험**을 차례로 선택합니다.

- 페이지 위쪽에 있는 **Experiment created on ...(...에서 만든 실험)**을 선택하고 수정하여 실험의 이름을 지정할 수 있습니다. 예를 들어 **CA Dairy Analysis**로 변경합니다.

- 실험 페이지의 왼쪽에서 **저장된 데이터 집합**, **내 데이터 집합**을 차례로 확장합니다. 이전에 업로드한 **cadairydata.csv**가 표시되어야 합니다.

- **csdairydata.csv 데이터 집합**을 실험에 끌어다 놓습니다.

- 왼쪽 창 맨 위의 **실험 항목 검색** 상자에 Execute [R Script(R 스크립트 실행)][execute-r-script]를 입력합니다. 검색 목록에 모듈이 표시됩니다.

- [R 스크립트 실행][execute-r-script] 모듈을 팔레트에 끌어다 놓습니다.

- **csdairydata.csv 데이터 집합**의 출력을 [R 스크립트 실행][execute-r-script]의 가장 왼쪽에 있는 입력(**Dataset1**)에 연결합니다.

- **반드시 '저장'을 클릭하세요.**

이때 실험이 그림 3과 같이 표시됩니다.

![데이터 집합이 있는 캘리포니아 유제품 분석 실험 및 R 스크립트 실행 모듈][3]

*그림 3. 데이터 집합이 있는 캘리포니아 유제품 분석 실험 및 R 스크립트 실행 모듈*

####데이터 확인

실험에 로드한 데이터를 살펴보겠습니다. 실험에서 **cadairydata.csv** 데이터 집합의 출력을 클릭하고 **시각화**를 선택합니다. 그림 4와 같이 표시됩니다.

![cadairydata.csv 데이터 집합 요약][4]

*그림 4. cadairydata.csv 데이터 집합 요약*

여기에서 많은 유용한 정보를 볼 수 있습니다. 또한 해당 데이터 집합의 처음 몇 행도 볼 수 있습니다. 열을 선택하면 해당 열에 관한 추가 정보가 통계 섹션에 표시됩니다. 예를 들어 Feature Type 행은 Azure 기계 학습 스튜디오가 열에 할당된 데이터 형식을 보여줍니다. 중요한 작업을 시작하기 전에 이렇게 빠르게 확인하면 온전성을 검사하는 데 좋습니다.

###	첫 번째 R 스크립트

Azure 기계 학습 스튜디오에서 실험할 간단한 첫 번째 R 스크립트를 만들어 보겠습니다. RStudio에서 다음 스크립트를 만들고 테스트했습니다.

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	str(cadairydata)
	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

이제 이 스크립트를 Azure 기계 학습 스튜디오로 전송해야 합니다. 간단히 잘라내고 붙여넣을 수 있지만 이 경우에서는 R 스크립트를 zip 파일을 통해 전송하겠습니다.

###	R 스크립트 실행 모듈에 데이터 입력

[R 스크립트 실행][execute-r-script] 모듈에 대한 입력을 살펴보겠습니다. 이 예제에서는 캘리포니아 유제품 데이터를 [R 스크립트 실행][execute-r-script] 모듈로 읽어옵니다.

[R 스크립트 실행][execute-r-script] 모듈에 대해 사용 가능한 입력에는 세 가지가 있습니다. 응용 프로그램에 따라 이러한 입력을 하나만 사용하거나 모두 사용할 수 있습니다. 또한 입력이 전혀 없는 R 스크립트를 사용하는 것도 합리적일 수 있습니다.

이 입력을 각각 왼쪽에서 오른쪽으로 살펴보겠습니다. 입력 위에 커서를 놓고 도구 설명을 읽어 각 입력의 이름을 볼 수 있습니다.

#### 스크립트 번들

스크립트 번들 입력을 사용하면 zip 파일의 내용을 [R 스크립트 실행][execute-r-script] 모듈에 전달할 수 있습니다. 다음 명령 중 하나를 사용하여 zip 파일의 내용을 R 코드에 읽어올 수 있습니다.

	source("src/yourfile.R") # Reads a zipped R script
	load("src/yourData.rdata") # Reads a zipped R data file

> [AZURE.NOTE] Azure 기계 학습은 src/ 디렉터리에 있는 것처럼 파일을 zip 파일로 처리하므로 이 디렉터리 이름을 파일 이름 앞에 붙여야 합니다. 예를 들어 zip 파일의 루트에 `yourfile.R` 및 `yourData.rdata` 파일이 포함된 경우 `source` 및 `load`를 사용할 때 이러한 파일을 `src/yourfile.R` 및 `src/yourData.rdata`로 처리합니다.

[데이터 집합 로드](#loading)는 데이터 집합 로드에서 이미 설명했습니다 이전 섹션에 나와 있는 R 스크립트를 만들고 테스트한 후에는 다음을 수행하세요.

1. R 스크립트를 .R 파일로 저장합니다. 이 스크립트 파일을 "simpleplot.R"이라고 하겠습니다. 콘텐츠는 다음과 같습니다.

        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')

2.  Zip 파일을 만들고 스크립트를 이 zip 파일에 복사합니다. Windows에서 파일을 마우스 오른쪽 단추로 클릭하고 __보내기__를 선택한 다음 __압축 폴더__를 선택할 수 있습니다. 그러면 "simpleplot.R" 파일을 포함하는 zip 파일이 새로 만들어집니다.

3.	파일을 **zip** 형식으로 지정하여 기계 학습 스튜디오의 **데이터 집합**에 추가합니다. 이제 데이터 집합에 zip 파일이 표시됩니다.

4.	Zip 파일을 **데이터 집합**에서 **기계 학습 스튜디오 캔버스**로 끌어다 놓습니다.

5.	**Zip 데이터** 아이콘의 출력을 [R 스크립트 실행][execute-r-script] 모듈의 **스크립트 번들** 입력에 연결합니다.

6.	`source()` 함수를 zip 파일 이름과 함께 [R 스크립트 실행][execute-r-script] 모듈의 코드 창에 입력합니다. 제 경우 `source("src/simpleplot.R")`를 입력합니다.

7.	반드시 **저장**을 클릭하세요.

이러한 단계가 완료되면 [R 스크립트 실행][execute-r-script] 모듈은 실험이 실행될 때 해당 zip 파일의 R 스크립트를 실행합니다. 이때 실험이 그림 5와 같이 표시됩니다.

![압축된 R 스크립트를 사용한 실험][6]

*그림 5. 압축된 R 스크립트를 사용한 실험*

#### Dataset1

Dataset1 입력을 사용하여 사각형의 데이터 테이블을 R 코드에 전달할 수 있습니다. 간단한 이 스크립트에서 `maml.mapInputPort(1)` 함수는 포트 1에서 데이터를 읽습니다. 그런 다음 이 데이터는 코드에서 데이터 프레임 변수 이름에 할당됩니다. 간단한 이 스크립트에서 코드의 첫 번째 줄은 할당을 수행합니다.

	cadairydata <- maml.mapInputPort(1)

**실행** 단추를 클릭하여 실험을 실행하세요. 실행이 끝나면 [R 스크립트 실행][execute-r-script] 모듈을 클릭한 후 속성 창에서 **출력 로그 보기**를 클릭하세요. Output.log 파일의 내용을 보여 주는 새 페이지가 브라우저에 표시됩니다. 아래로 스크롤하면 다음과 같이 표시됩니다.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

페이지 아래쪽에 열에 대한 상세 정보가 다음과 같이 나타납니다.

	[ModuleOutput] [1] "Loading variable port1..."
	[ModuleOutput]
	[ModuleOutput] 'data.frame':	228 obs. of  9 variables:
	[ModuleOutput]
	[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
	[ModuleOutput]
	[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
	[ModuleOutput]
	[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
	[ModuleOutput]
	[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
	[ModuleOutput]
	[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
	[ModuleOutput]
	[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
	[ModuleOutput]
	[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

결과는 거의 예상대로 dataframe에 228번의 관찰과 9개의 열이 나왔습니다. 각 열의 열 이름, R 데이터 형식, 샘플을 볼 수 있습니다.

> [AZURE.NOTE] 이와 동일한 인쇄된 출력은 [R 스크립트 실행][execute-r-script] 모듈의 R 장치 출력에서 편리하게 사용할 수 있습니다. 다음 섹션에서 [R 실행 모듈][execute-r-script]의 출력을 설명합니다.

####Dataset2

Dataset2 입력의 동작은 Dataset1과 동일합니다. 이 입력을 사용하여 두 번째 사각형의 데이터 테이블을 R 코드에 전달할 수 있습니다. 인수 2를 가진 `maml.mapInputPort(2)` 함수를 사용하여 이 데이터를 전달합니다.

###R 스크립트 실행 출력

####데이터 프레임 출력

`maml.mapOutputPort()` 함수를 사용하여 결과 데이터 집합 1 포트를 통해 R 데이터 프레임의 내용을 사각형 테이블처럼 출력할 수 있습니다. 간단한 이 R 스크립트에서는 다음 줄을 통해 이를 수행할 수 있습니다.

	maml.mapOutputPort('cadairydata')

실험을 실행한 후에는 Result Dataset1 출력 포트를 클릭한 다음 **시각화**를 클릭하세요. 그림 6과 같이 표시됩니다.

![캘리포니아 유제품 데이터 출력의 시각화][7]

*그림 6. 캘리포니아 유제품 데이터 출력의 시각화*

이 출력은 예상대로 입력과 같아 보입니다.

###	R 장치 출력

[R 스크립트 실행][execute-r-script] 모듈의 장치 출력은 메시지 및 그래픽 출력을 포함합니다. R의 표준 출력 및 표준 오류 메시지는 둘 다 R 장치 출력 포트로 전송됩니다.

R 장치 출력을 보려면 해당 포트를 클릭한 다음 **시각화**를 클릭하세요. 그림 7에서 R 스크립트의 표준 출력 및 표준 오류를 볼 수 있습니다.

![R 장치 포트의 표준 출력 및 표준 오류][8]

*그림 7. R 장치 포트의 표준 출력 및 표준 오류*

아래로 스크롤하면 그림 8에서 R 스크립트의 그래픽 출력을 볼 수 있습니다.

![R 장치 포트의 그래픽 출력][9]

*그림 8. R 장치 포트의 그래픽 출력*

##<a id="filtering"></a>데이터 필터링 및 변환

이 섹션에서는 캘리포니아 유제품 데이터에 몇 가지 기본 데이터 필터링 및 변환 작업을 수행합니다. 이 섹션의 끝 부분에서 분석 모델을 구축하기에 적합한 형식의 데이터를 얻게 됩니다.

더욱 구체적으로 설명하자면 이 섹션에서 여러 일반적인 데이터 정리 및 변환 작업(형식 변환, 데이터 프레임 필터링, 새 계산 열 추가, 값 변환)을 수행합니다. 이 경험이 실제로 겪게 될 많은 다양한 문제를 해결하는 데 도움이 될 것입니다.

이 섹션의 전체 R 코드는 앞서 다운로드한 zip 파일에서 사용할 수 있습니다.

###	형식 변환

이제 캘리포니아 유제품 데이터를 [R 스크립트 실행][execute-r-script] 모듈의 R 코드로 읽어올 수 있으므로 열의 데이터가 지정된 형식 및 서식으로 되어 있는지 확인해야 합니다.

R은 동적으로 입력되는 언어이므로 필요에 따라 데이터 형식이 강제 변환됩니다. R의 원자 데이터 형식에는 숫자, 논리값, 문자가 포함됩니다. 요소 형식은 범주 데이터를 조밀하게 저장하는 데 사용됩니다. [부록 B - 추가 정보](#appendixb)의 참고 자료에서 데이터 형식에 대해 훨씬 더 많은 정보를 찾을 수 있습니다.

표 형식 데이터를 외부 소스에서 R로 읽어올 경우에는 생성되는 열의 형식을 항상 확인하는 것이 좋습니다. 문자 형식의 열을 원하더라도 많은 경우 이 열은 요소로 표시되거나 그 반대로 요소가 문자 형식으로 표시됩니다. 그 밖의 경우 구상 중인 열은 부동 소수점 숫자가 아닌 문자 데이터, 즉 1.23이 아닌 '1.23'으로 나타내야 합니다.

다행히 매핑이 가능한 경우에는 형식끼리 변환하기가 쉽습니다. 예를 들어 '네바다'를 숫자 값으로 변환할 수는 없지만 요소(범주 변수)로는 변환할 수 있습니다. 또 다른 예로 숫자 1을 문자 '1'이나 요소로 변환할 수 있습니다.

이러한 변환 구문은 다음과 같이 간단합니다. `as.datatype()` 이러한 형식 변환 함수는 다음과 같습니다.

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

우리가 이전 섹션에서 입력한 열의 데이터 유형을 보면 모든 열의 형식은 문자 형식인 'Month'라고 지정된 열을 제외하고는 모두 숫자 형식입니다. 이 형식을 요소로 변환하고 결과를 테스트해보겠습니다.

산점도 행렬을 만든 줄을 삭제하고 'Month' 열을 요소로 변환하는 줄을 추가했습니다. 이 실험에서 R 코드를 잘라 [R 스크립트 실행][execute-r-script] 모듈의 코드 창에 붙여넣기만 하겠습니다. Zip 파일을 업데이트하고 Azure 기계 학습 스튜디오에 업로드할 수도 있으나 몇 단계를 거쳐야 합니다.

	## Only one of the following two lines should be used
	## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
	## If in RStudio, use the second line with read.csv()
	cadairydata <- maml.mapInputPort(1)
	# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(cadairydata$Month)
	str(cadairydata) # Check the result
	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('cadairydata')

이 코드를 실행하고 R 스크립트에 대한 출력 로그를 살펴보겠습니다. 로그의 관련 데이터는 그림 9와 같습니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*그림 9. 요소 변수가 포함된 데이터 프레임 요약*

이제 Month의 형식은 '**Factor w/ 14 levels**'입니다. 이는 1년에 12달만 있으므로 문제가 됩니다. 결과 데이터 집합 포트의 **시각화** 형식이 '**범주별**'인지 확인할 수도 있습니다.

문제는 'Month' 열이 체계적으로 코딩되지 않았다는 점입니다. 어떤 경우에는 April라고 하고 또 어떤 경우에는 Apr로 줄여서 표시하기도 합니다. 문자열을 3자로 줄여서 이 문제를 해결할 수 있습니다. 이제 코드 줄이 다음과 같이 표시됩니다.

	## Ensure the coding is consistent and convert column to a factor
	cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

실험을 다시 실행하고 출력 로그를 봅니다. 예상 결과는 그림 10과 같습니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*그림 10. 요소 수준의 개수가 올바른 데이터 프레임 요약*

이제 요소 변수의 수준이 원하는 대로 12가 되었습니다.

###기본 데이터 프레임 필터링

R 데이터 프레임은 강력한 필터링 기능을 지원합니다. 행이나 열에 논리 필터를 사용하여 데이터 집합을 하위 집합으로 만들 수 있습니다. 대부분의 경우 복잡한 필터 조건을 입력해야 합니다. [부록 B-추가 자료](#appendixb)의 참고 자료에 데이터 프레임 필터링에 대한 방대한 예가 포함되어 있습니다.

이 데이터 집합에서 수행해야 하는 필터링이 하나 있습니다. cadairydata 데이터 프레임의 열을 살펴보면 두 개의 불필요한 열을 볼 수 있습니다. 첫 번째 열은 단지 행 번호만 가지고 있으며 그리 유용하지 않습니다. 두 번째 열인 Year.Month에는 불필요한 정보가 포함되어 있습니다. 다음 R 코드를 사용하여 이 두 열을 쉽게 제외시킬 수 있습니다.

> [AZURE.NOTE] 지금부터 이 섹션에서 [R 스크립트 실행][execute-r-script] 모듈에 추가하는 추가 코드만 보여드리겠습니다. 새 줄은 `str()` 함수 **앞**에 추가합니다. 이 함수는 Azure 기계 학습 스튜디오에서 결과를 확인하는 데 사용됩니다.

다음 줄을 [R 스크립트 실행][execute-r-script] 모듈의 R 코드에 추가합니다.

	# Remove two columns we do not need
	cadairydata <- cadairydata[, c(-1, -2)]

자체 실험에서 이 코드를 실행하고 출력 로그에서 결과를 확인하세요. 이러한 결과는 그림 11에 나와 있습니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*그림 11. 두 개의 열을 제거한 데이터 프레임 요약*

기쁘게도 원하는 결과를 얻었습니다.

###새 열 추가

시계열 모델을 만들려면 시계열 시작 이후의 월을 포함하는 열을 사용하는 것이 편리합니다. 'Month.Count'라는 열을 새로 만듭니다.

코드 구성을 돕기 위해 먼저 간단한 `num.month()` 함수를 만듭니다. 그런 다음 이 함수를 적용하여 데이터 프레임에 새 열을 만듭니다. 새 코드는 다음과 같습니다.

	## Create a new column with the month count
	## Function to find the number of months from the first
	## month of the time series
	num.month <- function(Year, Month) {
	  ## Find the starting year
	  min.year  <- min(Year)

	  ## Compute the number of months from the start of the time series
	  12 * (Year - min.year) + Month - 1
	}

	## Compute the new column for the dataframe
	cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

이제 업데이트된 실험을 실행하고 출력 로그를 사용하여 결과를 보세요. 이러한 결과는 그림 12에 나와 있습니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*그림 12. 열이 추가된 데이터 프레임 요약*

모두 제대로 작동하는 것 같으며 원하는 값을 가진 새 열이 데이터 프레임에 포함되었습니다.

###값 변환

이 섹션에서는 데이터 프레임 열 중 일부 열의 값을 간단히 변환해보겠습니다. R 언어는 거의 임의의 값 변환을 지원합니다. [부록 B-추가 자료](#appendixb)의 참고 자료에 방대한 예가 포함되어 있습니다.

데이터 프레임 요약에서 값을 살펴보면 이상한 점을 확인할 수 있습니다. 캘리포니아에서 아이스크림이 우유보다 더 많이 생산되나요? 아닙니다. 물론 말이 안 되지만 일부 아이스크림 애호가로 인해 이렇게 된 것 같습니다. 단위도 다릅니다. 금액은 미국 파운드, 우유는 1M 미국 파운드, 아이스크림은 1,000 미국 갤론, 코티지 치즈는 1,000 미국 파운드 단위로 되어 있습니다. 갤론당 아이스크림 무게가 약 6.5파운드라고 가정하면 모두 동일하게 1,000파운드 단위가 되도록 곱셈을 사용하여 이러한 값을 쉽게 변환할 수 있습니다.

이 예측 모델의 경우 이 데이터의 추세 및 계절성 조정을 위해 승법 모형을 사용합니다. 로그 변환을 사용하면 이 과정을 단순화하는 선형 모델을 사용할 수 있습니다. 승수가 적용되는 동일한 함수에서 로그 변환을 적용할 수 있습니다.

다음 코드에서는 `log.transform()`이라는 함수를 새로 정의하고 숫자 값을 포함하는 행에 적용합니다. R `Map()` 함수는 선택한 dataframe 열에 `log.transform()` 함수를 적용하는 데 사용됩니다. `Map()` 함수는 `apply()` 함수와 비슷하지만 함수에 두 개 이상의 인수 목록이 허용됩니다. 승수 목록이 `log.transform()` 함수에 두 번째 인수를 제공합니다. `na.omit()` 함수는 데이터 프레임에서 누락되거나 정의되지 않은 값이 없는지 확인하기 위한 일종의 정리 기능으로 사용됩니다.

	log.transform <- function(invec, multiplier = 1) {
	  ## Function for the transformation, which is the log
	  ## of the input value times a multiplier

	  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
	                       "ERROR: Arguments to function log.transform must be greate than zero",
	                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
	                       "ERROR: Invalid time seies value encountered in function log.transform"
	                       )

	  ## Check the input arguments
	  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
	  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
	  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

	  ## Wrap the multiplication in tryCatch
	  ## If there is an exception, print the warningmessage to
	  ## standard error and return NA
	  tryCatch(log(multiplier * invec),
	           error = function(e){warning(warningmessages[4]); NA})
	}


	## Apply the transformation function to the 4 columns
	## of the dataframe with production data
	multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
	cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

	## Get rid of any rows with NA values
	cadairydata <- na.omit(cadairydata)  

`log.transform()` 함수에서는 꽤 많은 일이 일어납니다. 이 코드의 대부분은 인수와 관련된 잠재적인 문제를 확인하거나 계산하는 동안 생길 수 있는 예외를 처리합니다. 이 코드의 몇 줄만이 실제로 계산을 수행합니다.

방어적 프로그래밍의 목표는 프로세스를 계속 진행하지 못하게 하는 단일 함수의 실패를 방지하는 것입니다. 장기 실행 분석의 갑작스런 실패는 사용자에게 상당한 불만을 초래할 수 있습니다. 이러한 상황을 방지하려면 기본 반환 값을 선택하여 손상을 다운스트림 처리로 제한해야 합니다. 또한 문제가 있다는 것을 사용자에게 경고하기 위한 메시지도 생성됩니다.

R에서 방어적 프로그래밍이 익숙하지 않으면 이 코드가 모두 약간 부담스러울 수 있습니다. 주요 단계를 안내합니다.

1. 네 개 메시지의 벡터가 정의됩니다. 이러한 메시지는 이 코드에서 발생할 수 있는 일부 오류 및 예외에 대한 정보를 전달하는 데 사용됩니다.

2. 각각의 경우에 대해 NA 값을 반환합니다. 부작용이 더 적을 수 있는 다른 가능성이 많습니다. 예를 들어 0의 벡터나 원래 입력 벡터를 반환할 수 있습니다.

3. 함수에 대한 인수를 확인합니다. 오류가 감지되는 경우에는 기본값이 반환되며 `warming()` 함수가 메시지를 생성합니다. `stop()`은 실행을 종료하므로 방지하려는 경우에는 `warning()`을 사용합니다. 이 코드의 경우 함수 접근 방법으로 작성하면 복잡하고 난해해 보이므로 프로시저 방식으로 작성하였습니다.

4. 예외가 발생해 프로세스를 갑자기 중단하지 않도록 로그 계산이 `tryCatch()`로 래핑됩니다. `tryCatch()`가 없으면 R 함수에 의해 발생한 대부분의 오류는 중지 작업을 수행하는 중지 신호가 됩니다.

자체 실험에서 이 R 코드를 실행하고 output.log 파일의 인쇄된 출력을 살펴보세요. 그림 13에서 볼 수 있듯이 이제 로그에 있는 네 개의 열 값이 변환된 것을 확인할 수 있습니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*그림 13. 데이터 프레임의 변환된 값 요약*

변환된 값을 볼 수 있습니다. 이제 우유 생산이 다른 모든 유제품 생산을 훨씬 초과합니다. 현재 로그 눈금으로 보고 있다는 사실을 상기하세요.

이 시점에서 데이터가 정리되며 모델링할 준비가 됩니다. [R 스크립트 실행][execute-r-script] 모듈의 Result Dataset 출력에 대한 시각화된 요약을 보면 'Month' 열이 원하는 12개의 고유값을 가진 'Categorical'이라는 것을 알 수 있습니다.

##<a id="timeseries"></a>시계열 개체 및 상관관계 분석

이 섹션에서는 몇 가지 기본 R 시계열 개체를 살펴보고 일부 변수 간의 상관관계를 분석합니다. 여기서는 여러 지연 상황에서 쌍별 상관관계 정보를 포함하는 데이터 프레임을 출력하는 것이 목표입니다.

이 섹션의 전체 R 코드는 앞서 다운로드한 zip 파일에 들어 있습니다.

###R의 시계열 개체

이미 언급했듯이 시계열은 시간으로 인덱싱된 일련의 데이터 값입니다. R 시계열 개체는 시간 인덱스를 만들고 관리하는 데 사용됩니다. 시계열 개체를 사용하면 다음과 같은 몇 가지 이점이 있습니다. 시계열 개체를 사용하면 개체에 캡슐화된 시계열 인덱스 값을 관리하지 않아도 됩니다. 또한 시계열 개체를 통해 그리기, 인쇄, 모델링을 위한 다양한 시계열 메서드를 사용할 수 있습니다.

POSIXct 시계열 클래스가 일반적으로 사용되며 비교적 간단합니다. 이 시계열 클래스는 1970년 1월 1일 Epoch 시작부터 시간을 측정합니다. 이 예제에서는 POSIXct 시계열 개체를 사용합니다. 널리 사용되는 다른 R 시계열 개체 클래스에는 확장 가능한 시계열인 zoo와 xts가 있습니다.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

###	시계열 개체 예

예제로 시작해 보겠습니다. **새** [R 스크립트 실행][execute-r-script] 모듈을 실험에 끌어다 놓습니다. 기존 [R 스크립트 실행][execute-r-script] 모듈의 Result Dataset1 출력 포트를 새 [R 스크립트 실행][execute-r-script] 모듈의 Dataset1 입력 포트에 연결하세요.

첫 번째 예제에서와 마찬가지로 예제를 진행하는 동안 어떤 부분에서는 각 단계에서 추가된 R 코드 줄만 표시하겠습니다.

####	데이터 프레임 읽기

우선 데이터 프레임을 읽고 원하는 결과를 얻는지 확인합니다. 다음 코드가 작업을 수행해야 합니다.

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)
	str(cadairydata) # Check the results

이제 실험을 실행하면 새 R 스크립트 실행 모양의 로그는 그림 14와 같아야 합니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*(그림 14). R 스크립트 실행 모듈의 데이터 프레임 요약*

이 데이터의 형식 및 서식이 예상한 것과 같습니다. 'Month' 열은 요소 형식으로 되어 있으며 수준 개수가 예상대로 입니다.

####시계열 개체 만들기

시계열 개체를 데이터 프레임에 추가해야 합니다. 현재 코드를 다음과 같이 새 POSIXct 클래스 열을 추가하도록 바꿉니다.

	# Comment the following if using RStudio
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata) # Check the results

이제 로그를 확인하세요. 그림 15와 같이 표시됩니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*그림 15. 시계열 개체가 포함된 데이터 프레임 요약*

요약에서 보면 새 열이 POSIXct 클래스라는 사실을 알 수 있습니다.

###데이터 탐색 및 변환

이 데이터 집합에서 일부 변수를 살펴보겠습니다. 산점도 행렬은 간단히 살펴보기에 좋습니다. 이전 R 코드의 `str()` 함수를 다음 줄로 바꿉니다.

	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

이 코드를 실행하고 결과를 살펴보세요. R 장치 포트에서 생성된 결과는 그림 16과 같이 표시됩니다.

![선택한 변수의 산점도 행렬][17]

*그림 16. 선택한 변수의 산점도 행렬*

이러한 변수 간의 관계가 약간 이상해 보입니다. 변수를 표준화하지 않은 점과 데이터의 추세로 인해 이렇게 된 것 같습니다.

###상관관계 분석

상관관계 분석을 수행하려면 변수를 비추세화하고 표준화해야 합니다. 간단히 R `scale()` 함수를 사용하여 변수를 중심값으로 하고 그 크기를 조정할 수 있습니다. 이 함수가 더 빨리 실행될 수 있지만 R의 방어적 프로그래밍 예를 보여주려고 합니다.

아래 표시된 `ts.detrend()` 함수는 이러한 작업을 모두 수행합니다. 다음 두 줄의 코드는 데이터를 비추세화한 후 값을 표준화합니다.

	ts.detrend <- function(ts, Time, min.length = 3){
	  ## Function to de-trend and standardize a time series

	  ## Define some messages if they are NULL  
	  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
	                'ERROR: ts.detrend requires argument ts to be of type numeric',
	                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
	                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
	                'ERROR: Detrend regression has failed in ts.detrend',
	                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  	)
	  # Create a vector of zeros to return as a default in some cases
	  zerovec  <- rep(length(ts), 0.0)

	  # The input arguments are not of the same length, return ts and quit
	  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

	  # If the ts is not numeric, just return a zero vector and quit
	  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

	  # If the ts is too short, just return it and quit
	  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

	  ## Check that the Time variable is of class POSIXct
	  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

	  ## De-trend the time series by using a linear model
	  ts.frame  <- data.frame(ts = ts, Time = Time)
	  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
	           error = function(e){warning(messages[5]); zerovec})

	  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
	             ts <- ts/stdev},
	            error = function(e){warning(messages[6]); zerovec})

	  ts
	}  
	## Apply the detrend.ts function to the variables of interest
	df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

	## Plot the results to look at the relationships
	pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

`ts.detrend()` 함수에서는 꽤 많은 일이 일어납니다. 이 코드의 대부분은 인수와 관련된 잠재적인 문제를 확인하거나 계산하는 동안 생길 수 있는 예외를 처리합니다. 이 코드의 몇 줄만이 실제로 계산을 수행합니다.

방어적 프로그램의 예는 [값 변환](#valuetransformations)에서 이미 설명했습니다. 두 계산 블록이 모두 `tryCatch()`로 래핑됩니다. 일부 오류의 경우는 원래 입력 벡터를 반환하고 그 밖의 경우에는 0의 벡터를 반환합니다.

비추세화에 사용된 선형 회귀는 시계열 회귀입니다. 예측 변수는 시계열 개체입니다.

일단 `ts.detrend()`가 정의되면 데이터 프레임에서 관심 있는 변수에 적용합니다. `lapply()`의 결과로 만들어진 목록을 `as.data.frame()`을 사용하여 데이터 프레임의 데이터로 강제 변환해야 합니다. `ts.detrend()`의 방어적 측면으로 인해 변수 중 하나를 처리하지 못해도 다른 변수를 올바르게 처리할 수 있습니다.

마지막 코드 줄이 쌍별 산점도를 만듭니다. R 코드를 실행한 후의 산점도 결과는 그림 17에 나와 있습니다.

![비추세화되고 표준화된 시계열의 쌍별 산점도][18]

*그림 17. 비추세화되고 표준화된 시계열의 쌍별 산점도*

이러한 결과를 그림 17의 결과와 비교할 수 있습니다. 제거된 추세와 표준화된 변수를 통해 이러한 변수 간의 관계가 크게 줄어든 것을 볼 수 있습니다.

R ccf 개체로서 상관관계를 계산할 코드는 다음과 같습니다.

	## A function to compute pairwise correlations from a
	## list of time series value vectors
	pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
	  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
	}

	## A list of the pairwise indices
	corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

	## Compute the list of ccf objects
	cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

	cadairycorrelations

이 코드를 실행하면 그림 18과 같은 로그가 생성됩니다.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*그림 18. 쌍별 상관관계를 분석하여 얻은 ccf 개체 목록*

각 지연에 대해 상관관계 값이 있습니다. 이러한 상관관계 값은 크지 않아서 모두 중요하지 않습니다. 따라서 각 변수를 독립적으로 모델링할 수 있다는 결론을 얻을 수 있습니다.

###데이터 프레임 출력

쌍별 상관관계는 R ccf 개체 목록으로서 계산되었습니다. Result Dataset 출력 포트에서 실제로 데이터 프레임이 필요한 경우 이로 인해 약간의 문제가 발생합니다. 또한 ccf 개체는 그 자체가 목록이며, 이 목록의 첫 번째 요소 값 및 다양한 지연에서의 상관관계만 필요합니다.

다음 코드는 개체 자체가 목록인 ccf 개체의 목록에서 지연 값을 추출합니다.

	df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

	c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
	r.names  <- c("Corr Cot Cheese - Ice Cream",
	              "Corr Cot Cheese - Milk Prod",
	              "Corr Cot Cheese - Fat Price",
	              "Corr Ice Cream - Mik Prod",
	              "Corr Ice Cream - Fat Price",
	              "Corr Milk Prod - Fat Price")

	## Build a dataframe with the row names column and the
	## correlation data frame and assign the column names
	outframe <- cbind(r.names, df.correlations)
	colnames(outframe) <- c.names
	outframe


	## WARNING!
	## The following line works only in Azure Machine Learning
	## When running in RStudio, this code will result in an error
	#maml.mapOutputPort('outframe')

코드의 첫 번째 줄이 조금 어려워서 약간의 설명이 줄을 이해하는 데 도움이 됩니다. 다음과 같이 안의 함수에서 바깥의 함수 순서로 처리합니다.

1.  인수가 '**[[**'인 '**1**' 연산자는 ccf 개체 목록의 첫 번째 요소의 지연에서 상관관계의 벡터를 선택합니다.

2.  `do.call()` 함수는 `lapply()`가 반환하는 목록의 요소에 `rbind()` 함수를 적용합니다.

3.  `data.frame()` 함수는 `do.call()`에 의해 생성된 결과를 데이터 프레임에 강제 반환합니다.

행 이름은 데이터 프레임의 열에 있습니다. 이렇게 하면 [R 스크립트 실행][execute-r-script]에서 출력될 때 행 이름이 유지됩니다.

코드를 실행하고 Result Dataset 포트에서 출력을 **시각화**하면 그림 19와 같이 출력됩니다. 행 이름은 의도한 대로 첫 번째 열에 있습니다.

![상관관계 분석 결과 출력][20]

*그림 19. 상관관계 분석 결과 출력*

##<a id="seasonalforecasting"></a>시계열 예: 계절별 예측

이제 데이터가 분석에 적합한 형식이 되었으며 변수 간에는 중요한 상관관계가 없습니다. 이제 시계열 예측 모델을 만들어 보겠습니다. 이 모델을 사용하여 2013년 12개월 동안의 캘리포니아 우유 생산을 예측합니다.

예측 모델에는 두 가지 구성 요소, 즉 추세 구성 요소와 계절 구성 요소가 포함됩니다. 이 두 구성 요소를 통해 완벽하게 예측할 수 있습니다. 이런 형식의 모델을 승법 모형이라고 합니다. 대안으로는 가법 모형이 있습니다. 이 분석을 다룰 수 있는 변수에 이미 로그 변환을 적용했습니다.

이 섹션의 전체 R 코드는 앞서 다운로드한 zip 파일에 들어 있습니다.

###	분석을 위한 데이터 프레임 만들기

**새** [R 스크립트 실행][execute-r-script] 모듈을 실험에 추가하여 시작하세요. 기존 [R 스크립트 실행][execute-r-script] 모듈의 **Result Dataset** 출력을 새 모듈의 **Dataset1** 입력에 연결합니다. 결과는 그림 20과 같이 표시됩니다.

![새 R 스크립트 실행 모듈이 추가된 실험][21]

*그림 20. 새 R 스크립트 실행 모듈이 추가된 실험*

방금 완료한 상관관계 분석과 마찬가지로 POSIXct 시계열 개체가 포함된 열을 추가해야 합니다. 다음 코드가 이 작업을 수행합니다.

	# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
	cadairydata <- maml.mapInputPort(1)

	## Create a new column as a POSIXct object
	Sys.setenv(TZ = "PST8PDT")
	cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

	str(cadairydata)

이 코드를 실행하고 로그를 확인합니다. 결과는 그림 21과 같이 표시됩니다.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':	228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*그림 21. 데이터 프레임 요약*

이제 분석을 시작할 준비가 되었습니다.

###학습 데이터 집합 만들기

생성된 데이터 프레임을 사용하여 학습 데이터 집합을 만들어야 합니다. 이 데이터는 테스트 데이터 집합인 2013년의 마지막 12월을 제외한 모든 관찰 결과를 포함합니다. 다음 코드는 데이터 프레임을 하위 집합으로 만들고 유제품 생산 및 가격 변수 도표를 만듭니다. 네 개의 생산 및 가격 변수 도표를 만들겠습니다. 익명 함수를 사용하여 도표에 일부 인수를 정의한 후 `Map()`을 사용하여 다른 두 인수 목록을 반복합니다. 여기서는 For 루프가 잘 작동했겠지만 R은 함수 언어이므로 함수를 사용한 접근 방식을 보여 주고 있습니다.

	cadairytrain <- cadairydata[1:216, ]

	Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
	               "Log CA Ice Cream Production, 1000s lb",
	               "Log CA Milk Production 1000s lb",
	               "Log North CA Milk Milk Fat Price per 1000 lb")

	Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

코드를 실행하면 그림 22와 같이 R 장치 출력에서 일련의 시계열 도표가 생성됩니다. 시간 축은 시계열 도표 방식의 장점인 날짜 단위로 되어 있습니다.

![캘리포니아 유제품 생산 및 가격 데이터의 첫 번째 시계열 도표](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![캘리포니아 유제품 생산 및 가격 데이터의 두 번째 시계열 도표](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![캘리포니아 유제품 생산 및 가격 데이터의 세 번째 시계열 도표](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![캘리포니아 유제품 생산 및 가격 데이터의 네 번째 시계열 도표](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*그림 22. 캘리포니아 유제품 생산 및 가격 데이터의 시계열 도표*

###	추세 모델

시계열 개체를 만들고 데이터를 살펴보았으면 캘리포니아 우유 생산 데이터의 추세 모델을 생성해 보겠습니다. 시계열 회귀로 이 작업을 수행할 수 있습니다. 하지만 도표에서 보면 학습 데이터에서 관찰한 추세를 정확하게 모델링하려면 기울기와 절편이 두 개 이상 필요하다는 사실을 알 수 있습니다.

주어진 데이터가 적으면 RStudio에서 추세 모델을 구축한 후 생성된 모델을 잘라서 Azure 기계 학습에 붙여넣습니다. RStudio는 이러한 유형의 대화형 분석에 대화형 환경을 제공합니다.

먼저 최대 3의 멱수로 다항 회귀를 시도합니다. 이러한 종류의 모형을 과적합하게 하는 것은 매우 위험합니다. 따라서 고차항을 사용하지 않는 것이 좋습니다. `I()` 함수는 내용 해석을 금지하며('있는 그대로" 내용을 해석) 이 함수를 사용하여 문자 그대로 해석된 함수를 회귀 수식에 작성할 수 있습니다.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
	summary(milk.lm)

다음과 같이 생성됩니다.

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
	##     data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.12667 -0.02730  0.00236  0.02943  0.10586
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
	## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
	## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
	## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0418 on 212 degrees of freedom
	## Multiple R-squared:  0.941,	Adjusted R-squared:  0.94
	## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

이 출력의 P 값(Pr(>|t|))을 통해 제곱항이 중요하지 않을 수 있다는 사실을 알 수 있습니다. 제곱항을 삭제하고 `update()` 함수를 사용하여 이 모델을 수정합니다.

	milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
	summary(milk.lm)

다음과 같이 생성됩니다.

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.12597 -0.02659  0.00185  0.02963  0.10696
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
	## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
	## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0417 on 213 degrees of freedom
	## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
	## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

보기가 더 낫습니다. 모든 항이 중요합니다. 하지만 2e-16 값은 기본값이며 너무 심각하게 실행해서는 안 됩니다.

온전성 테스트로서 추세선이 표시된 캘리포니아 유제품 생산 데이터의 시계열 도표를 만듭니다. 모델을 만들어 도표를 만들도록 다음 코드를 Azure 기계 학습 [R 스크립트 실행][execute-r-script] 모듈(RStudio 아님)에 추가했습니다. 그림 23은 그 결과입니다.

	milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![추세 모델을 표시한 캘리포니아 우유 생산 데이터](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*그림 23. 추세 모델을 표시한 캘리포니아 우유 생산 데이터*

추세 모델이 데이터와 잘 맞는 것 같습니다. 또한 모델 곡선이 이상하게 흔들리듯이 크게 맞춘 것 같지 않습니다.

###계절 모델

현재 다루고 있는 추세 모델을 계속 사용하며 계절의 영향을 포함해야 합니다. 선형 모델에서 월을 더미 변수로 사용하여 월별 영향을 파악합니다. 요소 변수를 모델에 삽입할 때 절편을 계산하면 안 됩니다. 절편을 계산하면 수식이 과도하게 지정되고 R이 절편항은 유지한 채 원하는 요소 중 하나를 삭제합니다.

추세 모델이 만족스러우므로 `update()` 함수를 사용하여 기존 모델에 새 항을 추가할 수 있습니다. 업데이트 식의 -1은 절편항을 삭제합니다. 우선은 RStudio에서 계속합니다.

	milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
	summary(milk.lm2)

다음과 같이 생성됩니다.

	##
	## Call:
	## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
	##     data = cadairytrain)
	##
	## Residuals:
	##      Min       1Q   Median       3Q      Max
	## -0.06879 -0.01693  0.00346  0.01543  0.08726
	##
	## Coefficients:
	##                   Estimate Std. Error t value Pr(>|t|)
	## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
	## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
	## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
	## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
	## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
	## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
	## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
	## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
	## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
	## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
	## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
	## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
	## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
	## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	##
	## Residual standard error: 0.0263 on 202 degrees of freedom
	## Multiple R-squared:     1,	Adjusted R-squared:     1
	## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

모델에 절편항이 없고 12개의 중요한 월 요소가 있음을 알 수 있습니다. 이것이 바로 원했던 결과입니다.

계절 모델이 얼마나 잘 작동하는지 확인하기 위해 캘리포니아 유제품 생산 데이터의 다른 시계열 도표를 만들어 보겠습니다. 모델을 만들어 도표를 만들도록 다음 코드를 Azure 기계 학습 [R 스크립트 실행][execute-r-script]에 추가했습니다.

	milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

	plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
	lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Azure 기계 학습에서 이 코드를 실행하면 그림 24와 같은 도표가 생성됩니다.

![계절의 영향이 포함된 캘리포니아 우유 생산 모델](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*그림 24. 계절의 영향이 포함된 캘리포니아 우유 생산 모델*

그림 24에 표시된 데이터와 맞추는 것이 좋습니다. 추세와 계절 영향(월별 변동)이 둘 다 적절해 보입니다.

모델에서 오차를 살펴 보겠습니다. 다음 코드는 두 모델에서 예측 값을 계산하고 계절 모델의 오차를 계산한 후 학습 데이터의 이러한 오차를 그립니다.

	## Compute predictions from our models
	predict1  <- predict(milk.lm, cadairydata)
	predict2  <- predict(milk.lm2, cadairydata)

	## Compute and plot the residuals
	residuals <- cadairydata$Milk.Prod - predict2
	plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

그림 25에서는 오차를 보여 줍니다.

![학습 데이터의 계절 모델 오차](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*그림 25. 학습 데이터의 계절 모델 오차*

이 정도의 오차는 적절해 보입니다. 2008-2009년 불경기의 영향을 제외하고는 모델에서 특별히 잘 설명하지 못하는 특정 구조가 없습니다.

그림 25에 표시된 도표는 오차에서 시간 종속 패턴을 검색하는 데 유용합니다. 오차 계산 및 그리기에 사용한 명시적 접근 방식은 오차를 시간순으로 도표에 배치합니다. 반면에 `milk.lm$residuals`를 그린 경우에는 도표가 시간순으로 정렬되지 않았습니다.

`plot.lm()`을 사용하여 일련의 진단 도표를 생성할 수도 있습니다.

	## Show the diagnostic plots for the model
	plot(milk.lm2, ask = FALSE)

이 코드는 그림 26에서 보여 주는 일련의 진단 도표를 생성합니다.

![계절 모델의 첫 번째 진단 도표](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![계절 모델의 두 번째 진단 도표](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![계절 모델의 세 번째 진단 도표](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![계절 모델의 네 번째 진단 도표](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*그림 26. 계절 모델의 진단 도표*

이러한 도표에서 몇 가지 큰 영향을 주는 사항이 발견되었으나 크게 신경 쓸 정도는 아닙니다. 또한 오차가 일반적 분포와 근접한 일반 Q-Q 도표에서 선형 모델의 중요한 가정을 볼 수 있습니다.

###예측 및 모델 평가

예제를 완료하기 위해 한 가지 작업이 더 있습니다. 예측을 계산하고 실제 데이터에 대한 오류를 측정해야 합니다. 2013년의 12개월에 대해 예측합니다. 학습 데이터 집합의 일부가 아닌 실제 데이터에 이 예측의 오차 측정을 계산할 수 있습니다. 또한 18년의 훈련 데이터를 12개월의 테스트 데이터와 성능을 비교할 수 있습니다.

시계열 모델의 성능을 측정하는 데 여러 측정 기준이 사용됩니다. 이 경우 RMS(제곱 평균 오차)를 사용합니다. 다음 함수는 두 계열 간의 RMS 오차를 계산합니다.

	RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
	  ## Function to compute the RMS error or difference between two
	  ## series or vectors

	  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
	                "ERROR: Input vector to function RMS.error is too short",
	                "ERROR: Input vectors to function RMS.error must be of same length",
	                "WARNING: Funtion rms.error has received invald input time series.")

	  ## Check the arguments
	  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    	warning(messages[1])
	    return(NA)}

	  if(length(series1) < min.length) {
    	warning(messages[2])
	    return(NA)}

	  if((length(series1) != length(series2))) {
	   	warning(messages[3])
	    return(NA)}

	  ## If is.log is TRUE exponentiate the values, else just copy
	  if(is.log) {
	    tryCatch( {
	      temp1 <- exp(series1)
	      temp2 <- exp(series2) },
	      error = function(e){warning(messages[4]); NA}
    	)
	  } else {
	    temp1 <- series1
	    temp2 <- series2
	  }

	 ## Compute predictions from our models
	predict1  <- predict(milk.lm, cadairydata)
	predict2  <- predict(milk.lm2, cadairydata)

	## Compute the RMS error in a dataframe
	  tryCatch( {
	    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
	    error = function(e){warning(messages[4]); NA})
	}

값 변환 섹션에서 설명한 `log.transform()` 함수와 마찬가지로 이 함수에는 많은 오류 검사 및 예외 복구 코드가 있습니다. 사용된 원칙은 동일합니다. `tryCatch()`로 래핑된 두 곳에서 작업이 수행되었으며, 첫 번째는 많은 값으로 작업했으므로 시계열이 크게 증가합니다. 두 번째는 실제 RMS 오차가 계산됩니다.

RMS 오차를 측정하는 함수를 사용하여 RMS 오차가 포함된 데이터 프레임을 구축하고 출력해 보겠습니다. 추세 모델용 및 계절 요소가 포함된 전체 모델에 대한 용어를 포함합니다. 다음 코드는 생성한 두 선형 모델을 사용하여 작업을 수행합니다.

	## Compute the RMS error in a dataframe
	## Include the row names in the first column so they will
	## appear in the output of the Execute R Script
	RMS.df  <-  data.frame(
	rowNames = c("Trend Model", "Seasonal Model"),
	  Traing = c(
	  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
	  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
	  Forecast = c(
	    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
	    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
	)
	RMS.df

	## The following line should be executed only when running in
	## Azure Machine Learning Studio
	maml.mapOutputPort('RMS.df')

이 코드를 실행하면 Result Dataset 출력 포트에서 그림 27과 같이 출력됩니다.

![모델의 RMS 오차 비교][26]

*그림 27. 모델의 RMS 오차 비교*

이러한 결과를 통해 모델에 계절 요소를 추가하면 RMS 오차가 현저히 감소한다는 사실을 알 수 있습니다. 별로 놀랍지는 않지만 학습 데이터의 RMS 오차는 예측의 RMS 오차보다 약간 적습니다.

##<a id="appendixa"></a>부록 A - RStudio 가이드

RStudio는 문서화된 관련 증거가 많으므로 이 부록에서는 시작할 수 있도록 RStudio 설명서의 주요 섹션에 대한 일부 링크를 제공합니다.

1.	프로젝트 만들기

	R RStudio를 사용하여 R 코드를 프로젝트에서 구성하고 관리할 수 있습니다. 프로젝트를 사용하는 설명서는 https://support.rstudio.com/hc/articles/200526207-Using-Projects에서 찾을 수 있습니다.

	지침에 따라 이 문서의 R 코드 예제 프로젝트를 만들어 보는 것이 좋습니다.

2.	R 코드 편집 및 실행

	RStudio는 R 코드의 편집 및 실행을 위해 통합 환경을 제공합니다. 설명서는 https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code에서 찾을 수 있습니다

3.	디버그

	RStudio에는 강력한 디버그 기능이 포함되어 있습니다. 이러한 기능에 대한 설명서는 https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio에 있습니다.

	중단점 문제 해결 기능은 https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting에 설명되어 있습니다.

##<a id="appendixb"></a>부록 B - 추가 정보

이 R 프로그래밍 자습서는 Azure 기계 학습 스튜디오에서 R 언어를 사용해야 하는 기본 사항을 소개합니다. R에 익숙하지 않은 경우 CRAN에서 두 가지 소개 자료를 사용할 수 있습니다.

- R for Beginners by Emmanuel Paradis(http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf)는 초보자가 시작하기에 아주 좋은 곳입니다.

- W. N. Venables 등이 http://cran.r-project.org/doc/manuals/R-intro.html에서 R에 대해 좀 더 구체적으로 소개합니다.

R을 시작하는 데 도움을 되는 서적이 많이 있습니다. 몇 가지 유용한 서적은 다음과 같습니다.

- 'The Art of R Programming: A Tour of Statistical Software Design'(저자: Norman Matloff)(영문)은 훌륭한 R 프로그래밍 입문서입니다.

- 'R Cookbook'(저자: Paul Teetor)(영문)은 R을 사용한 문제 및 해결 접근 방식을 제공합니다.

- 'R in Action'(저자: Robert Kabacoff)(영문)도 유용한 입문서입니다. 입문자를 위한 Quick R 웹사이트(http://www.statmethods.net/)는 유용한 리소스입니다.

- 'R Inferno'(저자: Patrick Burns)(영문)는 R로 프로그래밍할 때 발생할 수 있는 많은 까다롭고 어려운 문제를 유쾌하게 다룹니다. 이 책은 http://www.burns-stat.com/documents/books/the-r-inferno/(영문)에서 무료로 사용할 수 있습니다.

- R에 대한 깊이 있는 정보는 'Advanced R'(저자: Hadley Wickham)(영문)에서 확인해 보세요. 이 책의 온라인 버전은 http://adv-r.had.co.nz/(영문)에서 무료로 사용할 수 있습니다.

R 시계열 패키지 카탈로그는 다음 CRAN Task View for time series analysis(http://cran.r-project.org/web/views/TimeSeries.html, 영문)에서 찾을 수 있습니다. 특정 시계열 개체 패키지에 대한 자세한 내용은 해당 패키지에 대한 설명서를 참조하세요.

'Introductory Time Series with R'(공저: Paul Cowpertwait와 Andrew Metcalfe)(영문)은 R을 사용한 시계열 분석에 대해 소개합니다. 많은 이론서에서 R 예제를 제공합니다.

다음은 유용한 인터넷 리소스입니다.

- DataCamp: 브라우저에서 편안하게 비디오 레슨 및 코드 연습을 배울 수 있습니다. 최신 R 기술 및 패키지에 대한 대화형 자습서가 제공됩니다. https://www.datacamp.com/courses/introduction-to-r에서 무료 대화형 R 자습서를 받을 수 있습니다.

- 클라크슨 대학교의 Kelly Black이 제공하는 빠른 R 자습서 http://www.cyclismo.org/tutorial/R/

- 60개 이상의 R 리소스가 있는 http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=AcomDC_0914_2016-->