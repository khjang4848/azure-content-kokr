<properties
	pageTitle="기계 학습 스튜디오로 데이터 가져오기 | Microsoft Azure"
	description="다양한 데이터 원본에서 Azure 기계 학습 스튜디오로 데이터를 가져오는 방법 지원되는 데이터 형식에 대해 알아봅니다."
	keywords="데이터 가져오기, 데이터 형식, 데이터 유형, 데이터 원본, 학습 데이터"
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
	ms.date="09/19/2016"
	ms.author="garye;bradsev" />


# 다양한 데이터 원본에서 Azure 기계 학습 스튜디오로 학습 데이터를 가져오기

다음을 수행하면 기계 학습 스튜디오에 사용자 고유의 데이터를 사용하여 예측 분석 솔루션을 개발 및 학습할 수 있습니다.

- 미리 하드 드라이브의 **로컬 파일**에서 데이터를 업로드하여 사용자의 작업 영역에 데이터 집합 모듈을 만듭니다.
- [데이터 가져오기][import-data] 모듈을 사용하여 실험을 실행하는 동안 여러 **온라인 데이터 원본** 중 하나에서 데이터에 액세스합니다.
- **데이터 집합**으로 저장된 또 다른 Azure 기계 학습 실험에서 나온 데이터를 사용합니다.

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

이러한 각 옵션을 위 메뉴의 토픽 중 하나에서 설명합니다. 이 토픽에서는 기계 학습 스튜디오에서 사용하기 위해 이러한 여러 데이터 원본에서 데이터를 가져오는 방법을 보여 줍니다.

> [AZURE.NOTE] 기계 학습 스튜디오에는 이 용도로 사용할 수 있는 샘플 데이터 집합이 많이 있습니다. 이에 대한 자세한 내용은 [Azure 기계 학습 스튜디오의 샘플 데이터 집합 사용](machine-learning-use-sample-datasets.md))을 참조하세요.

또한 이 소개 토픽에서는 기계 학습 스튜디오에 사용할 준비가 완료된 데이터를 가져오는 방법을 보여 주고 지원되는 데이터 형식과 데이터 유형을 설명합니다.

> [AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Azure 기계 학습 스튜디오에서 사용할 준비가 완료된 데이터 가져오기
기계 학습 스튜디오는 데이터베이스에서 구분되었거나 구조화된 데이터인 텍스트 데이터 등의 직사각형 또는 테이블 형식 데이터에 대해 작업하도록 설계되어 있습니다. 단, 경우에 따라 직사각형이 아닌 데이터도 사용할 수 있습니다.

데이터가 비교적 정리되어 있는 것이 좋습니다. 즉, 실험에 데이터를 업로드하기 전에 따옴표가 없는 문자열 등의 문제를 처리합니다.

그러나 기계 학습 스튜디오에는 실험에서 데이터를 조작할 수 있는 모듈도 있습니다. 사용할 기계 학습 알고리즘에 따라 누락된 값 및 스파스 데이터와 같은 데이터 구조 문제 해결 방법을 결정해야 하며, 이때 도움을 줄 수 있는 모듈이 있습니다. 모듈 팔레트의 **데이터 변환** 섹션에서 이러한 함수를 수행하는 모듈을 찾습니다.

실험을 수행하는 동안 언제든지 출력 포트를 마우스 오른쪽 단추로 클릭하여 모듈에서 생성한 데이터를 보거나 다운로드할 수 있습니다. 모듈에 따라 서로 다른 다운로드 옵션을 사용할 수 있습니다. 또는 기계 학습 스튜디오의 웹 브라우저에서 데이터를 볼 수도 있습니다.

## 지원되는 데이터 형식 및 데이터 유형

데이터를 가져오는 데 사용하는 메커니즘과 데이터의 출처에 따라 실험에 여러 데이터 형식을 가져올 수 있습니다.

- 일반 텍스트(.txt)
- 헤더가 있거나(.csv) 없는(.nh.csv) 쉼표로 구분된 값(CSV)
- 헤더가 있거나(.tsv) 없는(.nh.tsv) 탭으로 구분된 값(TSV)
- Excel 파일
- Azure 테이블
- Hive 테이블
- SQL 데이터베이스 테이블
- OData 값
- SVMLight 데이터(.svmlight)(형식 정보는 [SVMLight 정의](http://svmlight.joachims.org/) 참조)
- 특성 관계 파일 형식(ARFF) 데이터(.arff)(형식 정보는 [ARFF 정의](http://weka.wikispaces.com/ARFF) 참조)
- Zip 파일(.zip)
- R 개체 또는 작업 영역 파일(. RData)

메타 데이터를 포함하는 ARFF와 같은 형식으로 데이터를 가져오는 경우, 기계 학습 스튜디오에서는 이 메타 데이터를 사용하여 각 열의 머리글과 데이터 형식을 정의합니다.

이 메타 데이터를 포함하지 않는 TSV 또는 CSV 형식과 같은 데이터를 가져오는 경우, 기계 학습에서는 데이터를 샘플링하여 각 열의 데이터 형식을 유추합니다. 데이터에 열 머리글이 없는 경우 기계 학습 스튜디오에서 기본 이름을 제공합니다.

[메타 데이터 편집][edit-metadata]을 사용하여 열의 머리글과 데이터 유형을 명시적으로 지정하거나 변경할 수 있습니다.

기계 학습 스튜디오에서 인식하는 **데이터 유형**은 다음과 같습니다.

- 문자열
- Integer
- double
- Boolean
- DateTime
- TimeSpan

기계 학습 스튜디오에서는 ***데이터 테이블***이라는 내부 데이터 형식을 사용하여 모듈 간에 데이터를 전달합니다. [데이터 집합으로 변환][convert-to-dataset] 모듈을 사용하여 명시적으로 데이터를 데이터 테이블 형식으로 변환할 수 있습니다.

데이터 테이블 이외의 형식을 허용하는 모든 모듈에서는 다음 모듈에 데이터를 전달하기 전에 데이터를 데이터 테이블로 자동 변환합니다.

필요한 경우 다른 변환 모듈을 사용하여 데이터 테이블 형식을 다시 CSV, TSV, ARFF 또는 SVMLight 형식으로 변환할 수 있습니다. 모듈 팔레트의 **데이터 형식 변환** 섹션에서 이러한 함수를 수행하는 모듈을 찾습니다.



<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->