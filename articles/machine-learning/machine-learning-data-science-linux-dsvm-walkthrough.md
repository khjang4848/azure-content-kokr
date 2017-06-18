<properties 
	pageTitle="Linux 데이터 과학 가상 컴퓨터의 데이터 과학 | Microsoft Azure" 
	description="Linux 데이터 과학 VM을 사용하여 몇 가지 일반적인 데이터 과학 작업을 수행하는 방법입니다." 
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
	ms.author="bradsev;paulsh" />


# Linux 데이터 과학 가상 컴퓨터의 데이터 과학

이 연습에서는 Linux 데이터 과학 VM을 사용하여 몇 가지 일반 데이터 과학 작업을 수행하는 방법을 보여 줍니다. Linux DSVM(데이터 과학 가상 컴퓨터)은 데이터 분석 및 기계 학습에 흔히 사용되는 도구 모음과 함께 미리 설치된, Azure에서 사용 가능한 가상 컴퓨터 이미지입니다. 주요 소프트웨어 구성 요소는 [Linux 데이터 과학 가상 컴퓨터 프로비전](machine-learning-data-science-linux-dsvm-intro.md) 항목에 항목별로 나와 있습니다. VM 이미지를 사용하면 각 도구를 개별적으로 설치하고 구성할 필요 없이 몇 분 내에 데이터 과학 작업을 쉽게 시작할 수 있습니다. 필요한 경우 VM을 쉽게 확장하고 사용하지 않을 때 중지할 수 있습니다. 따라서 이 리소스는 탄력적이고 비용 효율적입니다.

이 연습에 설명된 데이터 과학 작업은 [팀 데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)에 설명된 단계를 따릅니다. 이 프로세스는 데이터 과학자 팀이 지능적인 응용 프로그램을 빌드하는 전체 수명 주기를 효율적으로 공동 작업할 수 있도록 데이터 과학에 대한 체계적인 접근 방법을 제공합니다. 또한 데이터 과학 프로세스는 개별 사용자가 수행할 수 있는 데이터 과학을 위한 반복되는 프레임워크를 제공합니다.

이 연습에서는 [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합을 분석합니다. 이는 스팸 또는 햄(스팸이 아니라는 의미)으로 표시되는 메일 집합이며 메일의 내용에 대한 일부 통계도 포함합니다. 포함되어 있는 통계는 다음 한 섹션에만 설명되어 있습니다.


## 필수 조건

Linux 데이터 과학 가상 컴퓨터를 사용하려면 먼저 다음이 있어야 합니다.

- **Azure 구독**. 아직 없을 경우 [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.
- [**Linux 데이터 과학 VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). 이 VM 프로비전에 대한 자세한 내용은 [Linux 데이터 과학 가상 컴퓨터 프로비전](machine-learning-data-science-linux-dsvm-intro.md)을 참조하세요.
- 컴퓨터에 설치되고 XFCE 세션을 연 [X2Go](http://wiki.x2go.org/doku.php). **X2Go 클라이언트** 설치 및 구성에 대한 자세한 내용은 [X2Go 클라이언트 설치 및 구성](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client)을 참조하세요.
- **AzureML 계정**. 아직 없을 경우 [AzureML 홈 페이지](https://studio.azureml.net/)에서 새 계정을 등록합니다. 시작할 수 있도록 무료 사용 계층을 제공합니다.


## spambase 데이터 집합 다운로드

[spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 데이터 집합은 4601개의 예제만 포함하는 비교적 작은 데이터 집합입니다. 이 데이터 집합은 리소스 요구 사항을 적절하게 유지하도록 하므로 데이터 과학 VM의 몇 가지 주요 기능을 보여 줄 때 사용하기 적합한 크기입니다.

>[AZURE.NOTE] 이 연습은 D2 v2 크기의 Linux 데이터 과학 가상 컴퓨터에서 만들었습니다. 이 크기의 DSVM은 이 연습의 절차를 처리할 수 있습니다.

저장소 공간이 더 필요한 경우 추가 디스크를 만들고 VM에 연결할 수 있습니다. 이러한 디스크는 영구 Azure Storage를 사용하므로 서버가 크기 조정으로 인해 다시 프로비전되거나 종료되는 경우에도 해당 데이터는 보존됩니다. 디스크를 추가하고 VM에 연결하려면 [Linux VM에 디스크 추가](../virtual-machines/virtual-machines-linux-add-disk.md)의 지침을 따릅니다. 이러한 단계는 DSVM에 이미 설치되어 있는 Azure CLI(Azure 명령줄 인터페이스)를 사용합니다. 따라서 전적으로 VM 자체에서 이 절차를 수행할 수 있습니다. 저장소를 늘리는 또 다른 옵션은 [Azure Files](../storage/storage-how-to-use-files-linux.md)를 사용하는 것입니다.

데이터를 다운로드하려면 터미널 창을 열고 다음 명령을 실행합니다.

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

다운로드한 파일은 머리글 행이 없으므로 머리글이 있는 다른 파일을 만듭니다. 다음 명령을 실행하여 적합한 머리글이 있는 파일을 만듭니다.

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

그런 후 다음 명령을 사용하여 두 개의 파일을 연결합니다.

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

각 메일의 데이터 집합에는 여러 가지 종류의 통계가 있습니다.

- ***word\_freq\_WORD***와 같은 열은 메일에서 *WORD*와 일치하는 단어의 백분율을 나타냅니다. 예를 들어 *word\_freq\_make*가 1인 경우에는 *make*가 메일에 있는 모든 단어의 1%입니다.
- ***char\_freq\_CHAR***과 같은 메일에 있는 모든 문자 중 *CHAR* 문자의 백분율을 나타냅니다.
- ***capital\_run\_length\_longest***는 대문자 시퀀스의 가장 긴 길이입니다.
- ***capital\_run\_length\_average***는 모든 대문자 시퀀스의 평균 길이입니다.
- ***capital\_run\_length\_total***은 모든 대문자 시퀀스의 총 길이입니다.
- ***spam***은 메일이 스팸으로 간주되는지 여부를 나타냅니다(1 = 스팸, 0 = 스팸이 아님).


## Microsoft R Open을 사용하여 데이터 집합 탐색

R을 사용하여 데이터를 검사하고 몇 가지 기본 기계 학습을 수행해 보겠습니다. 데이터 과학 VM은 [Microsoft R Open](https://mran.revolutionanalytics.com/open/)이 미리 설치된 상태로 제공됩니다. 이 R 버전의 다중 스레드 수학 라이브러리는 다양한 단일 스레드 버전보다 더 나은 성능을 제공합니다. Microsoft R Open은 CRAN 패키지 리포지토리의 스냅숏을 사용하여 재현 가능성을 제공합니다.

이 연습에서 사용하는 코드 샘플의 복사본을 얻으려면 git를 사용하여 VM에 미리 설치된 **Azure-Machine-Learning-Data-Science** 리포지토리를 복제합니다. Git 명령줄 도구에서 다음을 실행합니다.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

터미널 창을 열고 R 대화형 콘솔을 통해 새 R 세션을 시작합니다.

>[AZURE.NOTE] 다음 절차에 RStudio를 사용할 수도 있습니다. RStudio를 설치하려면 터미널에서 다음 명령을 실행합니다. `./Desktop/DSVM\ tools/installRStudio.sh`

데이터를 가져오고 환경을 설정하려면 다음을 실행합니다.

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

각 열에 대한 요약 통계를 보려면

    summary(data)

데이터를 다른 보기로 보려면

    str(data)

이렇게 하면 각 변수 유형과 데이터 집합의 처음 몇 개의 값을 보여 줍니다.

*spam* 열은 정수로 읽었지만 실제로는 범주 변수(또는 요소)입니다. 해당 형식을 설정하려면

    data$spam <- as.factor(data$spam)

일부 예비 분석을 수행하려면 VM에 이미 설치되어 있는 R에 대한 인기 있는 그래프 작성 라이브러리인 [ggplot2](http://ggplot2.org/) 패키지를 사용합니다. 앞에서 표시된 요약 데이터에서 느낌표 문자의 빈도에 대한 요약 통계가 있습니다. 다음 명령을 사용하여 해당 빈도를 도표로 나타내 보겠습니다.

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

0 모음은 도표를 왜곡하기 때문에 제거합니다.

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

흥미롭게도 1보다 큰 특수한 밀도가 있습니다. 해당 데이터를 살펴보겠습니다.

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

스팸과 햄으로 분할합니다.

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

이러한 예제를 통해 다른 열의 비슷한 도표를 작성하여 포함되어 있는 데이터를 탐색할 수 있습니다.


## ML 모델 학습 및 테스트

이제 스팸 또는 햄을 포함하도록 데이터 집합에서 메일을 분류하는 기계 학습 모델을 몇 가지 학습하겠습니다. 이 섹션에서 의사 결정 트리 모델 및 임의 포리스트 모델을 학습하고 해당 예측의 정확도를 테스트합니다.

>[AZURE.NOTE] 다음 코드에 사용되는 rpart(재귀 분할 및 회귀 트리) 패키지는 이미 데이터 과학 VM에 설치되어 있습니다.


먼저 데이터 집합을 학습 및 테스트 집합으로 분할합니다.

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

그런 다음 메일을 분류하는 의사 결정 트리를 만듭니다.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

결과는 다음과 같습니다.

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

학습 집합에 대해 얼마나 잘 수행하는지를 확인하려면 다음 코드를 사용합니다.

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

테스트 집합에 대해 얼마나 잘 수행하는지를 확인하려면

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

임의 포리스트 모델도 살펴보겠습니다. 임의 포리스트는 다양한 의사 결정 트리를 학습하고 모든 개별 의사 결정 트리의 분류 모드인 클래스를 출력합니다. 학습 데이터 집합에 지나치게 맞추는 의사 결정 트리 모델의 추세를 바로잡음으로써 더 강력한 기계 학습 접근 방식을 제공합니다.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## Azure ML에 모델 배포

[Azure Machine Learning Studio](https://studio.azureml.net/)(AzureML)는 예측 분석 모델을 쉽게 빌드하고 배포할 수 있는 클라우드 서비스입니다. AzureML의 뛰어난 기능 중 하나는 R 함수를 웹 서비스로 게시하는 기능입니다. AzureML R 패키지를 사용하면 DSVM의 R 세션에서 쉽게 배포할 수 있습니다.

이전 섹션의 의사 결정 트리 코드를 배포하려면 Azure Machine Learning Studio에 로그인해야 합니다. 로그인할 작업 영역 ID와 권한 부여 토큰이 필요합니다. 이러한 값을 찾아 AzureML 변수를 초기화하려면 다음을 수행합니다.

왼쪽 메뉴에서 **설정**을 선택합니다. **작업 영역 ID**를 적어둡니다. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

오버헤드 메뉴에서 **권한 부여 토큰**을 선택하고 **기본 권한 부여 토큰**을 기록해 둡니다.![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

**AzureML** 패키지를 로드한 다음 DSVM의 R 세션에서 토큰 및 작업 영역 ID로 변수 값을 설정합니다.


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


이 데모를 보다 쉽게 구현할 수 있도록 모델을 단순화해 보겠습니다. 의사 결정 트리에서 루트에 가장 가까운 세 개의 변수를 선택하고 이 세 개의 변수를 사용하여 새 트리를 빌드합니다.

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

입력으로 기능을 사용하고 예측된 값을 반환 하는 예측 함수가 필요합니다.

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

**publishWebService** 함수를 사용하여 AzureML에 predictSpam 함수를 게시합니다.

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

이 함수는 **predictSpam** 함수를 가져와서 입력 및 출력이 정의된 **spamWebService**라는 웹 서비스를 만들고 새 끝점에 대한 정보를 반환합니다.

다음 명령을 사용하여 API 끝점 및 액세스 키를 포함하여 게시된 웹 서비스의 세부 정보를 봅니다.

    spamWebService[[2]]

테스트 집합의 처음 10개의 행에 대해 작업을 수행하려면

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## 사용 가능한 다른 도구 사용

나머지 섹션에서는 Linux 데이터 과학 VM에 설치된 도구 중 일부를 사용하는 방법을 보여 줍니다. 설명할 도구 목록은 다음과 같습니다.

- XGBoost
- Python
- Jupyterhub
- Rattle
- PostgreSQL 및 Squirrel SQL
- SQL Server 데이터 웨어하우스


## XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/)는 빠르고 정확하게 향상된 트리 구현을 제공하는 도구입니다.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost는 python 또는 명령줄에서 호출할 수도 있습니다.

## Python

Python을 사용하여 개발하는 경우를 위해 Anaconda Python 배포 2.7 및 3.5가 DSVM에 설치되었습니다.

>[AZURE.NOTE] Anaconda 배포에는 다른 버전 및/또는 패키지가 설치된 Python용 사용자 지정 환경을 만드는 데 사용할 수 있는 [Condas](http://conda.pydata.org/docs/index.html)가 포함되어 있습니다.

spambase 데이터 집합의 일부를 읽고 scikit-learn에서 벡터 컴퓨터를 지원하는 메일을 분류해 보겠습니다.

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

예측하려면

    clf.predict(X.ix[0:20, :])

AzureML 끝점을 게시하는 방법을 보여 주기 위해 이전에 R 모델을 게시할 때처럼 3개의 변수를 사용하여 더 간단한 모델을 만들어 보겠습니다.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

AzureML에 모델을 게시하려면

	# Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] 이 작업은 python 2.7에만 사용할 수 있으며 3.5에는 아직 지원되지 않습니다. **/anaconda/bin/python2.7**을 사용하여 실행합니다.


## Jupyterhub

DSVM에서 Anaconda 배포에는 Jupyter 노트북, Python R을 공유하는 플랫폼 간 환경 또는 Julia 코드 및 분석이 함께 제공됩니다. JupyterHub을 통해 Jupyter Notebook에 액세스합니다. ***https://\<VM DNS 이름 또는 IP 주소>:8000/***에서 로컬 Linux 사용자 이름 및 암호를 사용하여 로그인합니다. JupyterHub에 대한 모든 구성 파일은 **/etc/jupyterhub** 디렉터리에서 찾을 수 있습니다.

몇 가지 샘플 노트북이 VM에 이미 설치되어 있습니다.

- 샘플 Python 노트북에 대한 내용은 [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)를 참조하세요.
- 샘플 **R** 노트북에 대한 내용은 [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb)을 참조하세요.
- 다른 샘플 **Python** 노트북에 대한 내용은 [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)를 참조하세요.

>[AZURE.NOTE] Julia 언어는 Linux 데이터 과학 VM의 명령줄에서 사용할 수도 있습니다.


## Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html)(R Analytical Tool To Learn Easily)은 데이터 마이닝을 위한 그래픽 R 도구입니다. 손쉽게 데이터를 로드, 탐색 및 변환하고 모델을 빌드 및 평가할 수 있는 직관적인 인터페이스가 있습니다. 문서 [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)(Rattle: R에 대한 데이터 마이닝 GUI)은 해당 기능을 보여 주는 연습을 제공합니다.

다음 명령을 사용하여 Rattle을 설치하고 시작합니다.

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] DSVM에는 설치할 필요가 없습니다. 하지만 Rattle을 로드할 때 추가 패키지를 설치하라는 메시지가 표시될 수 있습니다.

Rattle은 탭 기반 인터페이스를 사용합니다. 대부분의 탭은 데이터를 로드하거나 탐색하는 등 [데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)의 단계에 해당합니다. 데이터 과학 프로세스는 탭을 통해 왼쪽에서 오른쪽으로 진행됩니다. 하지만 마지막 탭에는 Rattle에서 실행하는 R 명령의 로그가 있습니다.


데이터 집합을 로드하고 구성하려면

- 파일을 로드하려면 **데이터** 탭을 선택한 다음
- **파일 이름** 옆에 있는 선택기를 선택하여 **spambaseHeaders.data**를 선택합니다.
- 파일을 로드하려면 단추의 맨 위 행에 있는 **실행**을 선택합니다. 입력, 대상 또는 다른 유형의 변수인지 고유한 값의 수인지 식별된 데이터 형식을 포함하여 각 열에 대한 요약이 표시됩니다.
- Rattle은 **spam** 열을 대상으로 제대로 식별합니다. spam 열을 선택한 다음 **대상 데이터 형식**을 **범주**로 설정합니다.

데이터를 탐색하려면

- **탐색** 탭을 선택합니다.
- **요약**을 클릭한 다음 **실행**을 클릭하면 변수 형식에 대한 일부 정보 및 일부 요약 통계를 볼 수 있습니다.
- 각 변수에 대한 다른 종류의 통계를 보려면 **설명** 또는 **기본 사항** 등 다른 옵션을 선택합니다.

또한 **탐색** 탭을 사용하여 많은 유용한 도표를 생성할 수 있습니다. 데이터의 히스토그램을 나타내려면


- **배포**를 선택합니다.
- **word\_freq\_remove** 및 **word\_freq\_you**에 대해 **히스토그램**을 선택합니다.
- **실행**을 선택합니다. 두 밀도 도표가 단일 그래프 창에 표시되고 여기서 단어 "you"가 "remove"보다 메일에 훨씬 더 자주 나오는 것을 볼 수 있습니다.

상관 관계 도표도 흥미롭습니다. 상관 관계를 만들려면


- **상관 관계**를 **형식**으로 선택한 다음
- **실행**을 선택합니다.
- 권장 최대 변수는 40개라는 경고 메시지가 표시됩니다. 도표를 보려면 **예**를 선택합니다.

여기서 몇 가지 흥미로운 상관 관계를 볼 수 있습니다. 예를 들어 "technology"는 "HP" 및 "labs"와 굉장히 상호 관련되어 있습니다. 데이터 집합 기부자의 지역 번호가 650이기 때문에 "650"과도 강력하게 상호 관련됩니다.

단어 사이의 상관 관계에 대한 숫자 값은 탐색 창에서 사용할 수 있습니다. 예를 들어 "technology"가 "your" 및 "money"와 부정적으로 상호 관련되어 있다는 점도 흥미로운 사실입니다.

Rattle은 몇 가지 일반적인 문제를 처리하기 위해 데이터 집합을 변환할 수 있습니다. 예를 들어 기능 크기 재조정, 누락 값 귀속, 이상값 처리 및 데이터가 누락된 관찰이나 변수를 제거할 수 있습니다. Rattle은 관찰 및/또는 변수 간의 연결 규칙을 식별할 수도 있습니다. 이러한 탭은 이 소개용 연습에 대한 범위를 벗어납니다.

Rattle은 클러스터 분석을 수행할 수도 있습니다. 출력을 더 쉽게 읽을 수 있도록 일부 기능을 제외하겠습니다. **데이터** 탭에서 다음 10개의 항목을 제외하고 각 변수 옆에 있는 **무시**를 선택합니다.

- word\_freq\_hp
- word\_freq\_technology
- word\_freq\_george
- word\_freq\_remove
- word\_freq\_your
- word\_freq\_dollar
- word\_freq\_money
- capital\_run\_length\_longest
- word\_freq\_business
- spam

**클러스터** 탭으로 돌아가서 **KMeans**를 선택하고 *클러스터 수*를 4로 설정합니다. 그런 다음 **실행**을 클릭합니다. 결과가 출력 창에 표시됩니다. 한 클러스터가 "george" 및 "hp"의 빈도가 높고 아마도 합법적인 비즈니스 메일입니다.

간단한 의사 결정 트리 기계 학습 모델을 빌드하려면

- **모델** 탭을 선택합니다.
- **트리**를 **형식**으로 선택합니다.
- **실행**을 선택하여 출력 창에 텍스트 형식으로 트리를 표시합니다.
- **그리기** 단추를 선택하여 그래픽 버전을 봅니다. 이전에 *rpart*를 사용하여 얻은 트리와 아주 유사합니다.

Rattle의 뛰어난 기능 중 하나는 여러 기계 학습 방법을 실행하고 신속하게 평가하는 기능입니다. 절차는 다음과 같습니다.

- **형식**에 대해 **모두**를 선택합니다.
- **실행**을 선택합니다.
- 완료되면 **SVM** 등의 모든 단일 **형식**을 클릭하여 그 결과를 볼 수 있습니다.
- **평가** 탭을 사용하여 유효성 검사 집합에서 모델의 성능을 비교할 수도 있습니다. 예를 들어 **오류 매트릭스**를 선택하면 유효성 검사 집합에서 각 모델에 대한 혼동 행렬, 전체 오류 및 평균 클래스 오류를 볼 수 있습니다.
- 또한 ROC 곡선을 그림으로 나타내고, 민감도 분석을 수행하고, 다른 유형의 모델 평가를 수행할 수도 있습니다.

모델 빌드가 완료된 후 **로그** 탭을 선택하면 세션 동안 Rattle에서 실행한 R 코드를 볼 수 있습니다. **내보내기** 단추를 선택하여 저장할 수 있습니다.

>[AZURE.NOTE] 현재 릴리스의 Rattle에 버그가 있습니다. 스크립트를 수정하거나 나중에 단계를 반복하여 사용하려면 로그 텍스트의 *Export this log...* 앞에 # 문자를 삽입해야 합니다.


## PostgreSQL 및 Squirrel SQL

DSVM은 PostgreSQL이 설치된 상태로 제공됩니다. PostgreSQL은 정교한 오픈 소스 관계형 데이터베이스입니다. 이 섹션에서는 PostgreSQL에 스팸 데이터 집합을 로드한 다음 쿼리하는 방법을 보여 줍니다.

데이터를 로드하기 전에 먼저 localhost에서 암호 인증을 허용해야 합니다. 명령 프롬프트에서:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

구성 파일의 아래쪽 몇 줄은 허용되는 연결을 자세히 설명하는 줄입니다.

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

ident 대신 md5를 사용하도록 "IPv4 local connections" 줄을 변경하면 사용자 이름 및 암호를 사용하여 로그인할 수 있습니다.

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Postgres 서비스를 다시 시작합니다.

    sudo systemctl restart postgresql

PostgreSQL용 대화형 터미널인 psql을 기본 제공 postgres 사용자로 시작하려면 프롬프트에서 다음 명령을 실행합니다.

    sudo -u postgres psql

현재 로그인한 Linux 계정과 동일한 사용자 이름을 사용하여 새 사용자 계정을 만들고 암호를 지정합니다.

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

그런 다음 사용자로 psql에 로그인합니다.

    psql

새 데이터베이스에 데이터를 가져옵니다.

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

이제 JDBC 드라이버를 통해 데이터베이스와 상호 작용할 수 있는 그래픽 도구인 **Squirrel SQL**을 사용하여 데이터를 탐색하고 일부 쿼리를 실행하겠습니다.

시작하려면 응용 프로그램 메뉴에서 Squirrel SQL을 실행합니다. 드라이버를 설정하려면

- **Windows**를 선택한 다음 **드라이버 보기**를 선택합니다.
- **PostgreSQL**을 마우스 오른쪽 단추로 클릭하고 **드라이버 수정**을 선택합니다.
- **추가 클래스 경로**를 선택한 다음 **추가**를 선택합니다.
- **파일 이름**에 ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar***을 입력합니다.
- **열기**를 선택합니다.
- 드라이버 목록 표시를 선택한 다음 **클래스 이름**에서 **org.postgresql.Driver**를 선택하고 **확인**을 선택합니다.

로컬 서버에 연결을 설정하려면
 
- **Windows**를 선택한 다음 **별칭 보기**를 선택합니다.
- **+** 단추를 선택하여 새 별칭을 만듭니다.
- 이름을 *Spam database*라고 지정하고 **드라이버** 드롭다운 목록에서 **PostgreSQL**을 선택합니다.
- URL을 *jdbc:postgresql://localhost/spam*로 설정합니다.
- *사용자 이름* 및 *암호*를 입력합니다.
- **확인**을 클릭합니다.
- **연결** 창을 열려면 별칭 ***Spam database***를 두 번 클릭합니다.
- **연결**을 선택합니다.

일부 쿼리를 실행하려면

- **SQL** 탭을 선택합니다.
- SQL 탭의 맨 위에 있는 쿼리 텍스트 상자에 `SELECT * from data;`와 같이 단순한 쿼리를 입력합니다.
- **Ctrl+Enter**를 눌러 실행합니다. 기본적으로 Squirrel SQL은 쿼리에서 처음 100개의 행을 반환합니다.

이 데이터를 탐색하기 위해 실행할 수 있는 훨씬 많은 쿼리가 있습니다. 예를 들어 스팸과 햄 간에 *make*라는 단어의 빈도가 얼마나 차이가 있을까요?

    SELECT avg(word_freq_make), spam from data group by spam;

또는 *3d*가 자주 포함되는 메일의 특징은 무엇일까요?

    SELECT * from data order by word_freq_3d desc;

*3d*가 자주 발생하는 대부분의 메일은 명백하게 스팸이므로 메일을 분류하는 예측 모델을 빌드하기 위한 유용한 기능이 될 수 있습니다.

PostgreSQL 데이터베이스에 저장된 데이터를 사용하여 기계 학습을 수행하려는 경우 [MADlib](http://madlib.incubator.apache.org/)를 사용하는 것이 좋습니다.

## SQL Server 데이터 웨어하우스

Azure SQL 데이터 웨어하우스는 거대한 양의 관계형 및 비관계형 데이터를 처리할 수 있는 클라우드 기반 규모 확장 데이터베이스입니다. 자세한 내용은 [Azure SQL Data Warehouse란?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)을 참조하세요.

데이터 웨어하우스에 연결하고 테이블을 만들려면 명령 프롬프트에서 다음 명령을 실행합니다.

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

그런 다음 sqlcmd 프롬프트에서 다음 명령을 실행합니다.

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

bcp를 사용하여 데이터 복사:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] 다운로드한 파일의 줄 끝은 Windows 스타일이지만 bcp에는 UNIX 스타일이 필요하므로 -r 플래그를 사용하여 bcp에 알려야 합니다.

sqlcmd를 사용하여 쿼리:

    select top 10 spam, char_freq_dollar from spam;
    GO

Squirrel SQL을 사용하여 쿼리할 수도 있습니다. ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***에 있을 수 있는 Microsoft MSSQL Server JDBC 드라이버를 사용하여 PostgreSQL과 비슷한 단계를 따릅니다.

## 다음 단계

Azure에서 데이터 과학 프로세스를 구성하는 작업을 안내하는 항목에 대한 개요는 [팀 데이터 과학 프로세스](http://aka.ms/datascienceprocess)를 참조하세요.

특정 시나리오에 대한 팀 데이터 과학 프로세스의 단계를 보여 주는 다른 종단 간 연습에 대한 설명은 [팀 데이터 과학 프로세스 연습](data-science-process-walkthroughs.md)을 참조하세요. 또한 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 설명합니다.

<!---HONumber=AcomDC_0914_2016-->