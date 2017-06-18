<properties
	pageTitle="R로 실험 확장 | Microsoft Azure"
	description="R 스크립트 실행 모듈을 사용하여 R 언어를 통해 Azure 기계 학습 스튜디오의 기능을 확장하는 방법."
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
	ms.date="08/19/2016"
	ms.author="garye" />


# R을 사용하여 실험 확장

[R 스크립트 실행][execute-r-script] 모듈을 사용하여 R 언어를 통해 기계 학습 스튜디오의 기능을 확장할 수 있습니다.

이 모듈에서는 여러 입력 데이터 집합을 허용하고 출력으로 단일 데이터 집합을 생성합니다. [R 스크립트 실행][execute-r-script] 모듈의 **R 스크립트** 매개 변수에 R 스크립트를 입력할 수 있습니다.

다음과 비슷한 코드를 사용하여 모듈의 각 입력 포트에 액세스합니다.

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 현재 설치된 모든 패키지 나열

설치된 패키지 목록을 변경할 수 있습니다. 각 패키지의 설명을 포함하여 설치된 패키지의 완전한 현재 목록을 가져오려면 [R 스크립트 실행][execute-r-script] 모듈에 다음 코드를 입력합니다.

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

[R 스크립트 실행][execute-r-script] 모듈의 출력 포트에 패키지의 목록을 전송합니다. 패키지 목록을 보려면 [CSV로 변환][convert-to-csv]과 같은 변환 모듈을 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결하고, 실험을 실행한 다음, 변환 모듈의 출력을 클릭하고 **다운로드**를 선택합니다.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)

<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## 패키지 가져오기

[R 스크립트 실행][execute-r-script] 모듈 및 압축된 패키지 아카이브에서 다음 명령을 사용하여 스테이징된 기계 학습 스튜디오 리포지토리에서 아직 설치되지 않은 패키지도 가져올 수 있습니다.

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

여기서 `my_favorite_package.zip`에는 패키지의 zip이 포함되어 있습니다.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0914_2016-->