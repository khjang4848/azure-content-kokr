<properties 
	pageTitle="Python 기계 학습 스크립트 실행 | Microsoft Azure" 
	description="Azure 기계 학습에서 Python 스크립트를 지원하는 데 기본이 되는 디자인 원칙 및 기본 사용 시나리오, 기능 및 제한 사항을 간략히 설명합니다." 
	keywords="python 기계 학습, pandas, python pandas, python 스크립트, python 스크립트 실행"
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
	ms.author="bradsev" />


# Azure 기계 학습 스튜디오에서 Python 기계 학습 스크립트 실행

이 항목에서는 Azure Machine Learning에서 현재 Python 스크립트를 지원하는 데 기본이 되는 디자인 원칙을 설명합니다. 기존 코드 가져오기와 시각화 내보내기 지원을 포함하는 주요 기능이 설명되어 있으며, 마지막으로 일부 제한 및 진행 중인 작업에 대해서도 설명합니다.

[Python](https://www.python.org/)은 여러 데이터 과학자들의 도구 상자에 필수적인 도구입니다. 여기에는:

-  세련되고 간결한 구문,
-  크로스 플랫폼 지원,
-  방대한 양의 강력한 라이브러리 컬렉션 및
-  완성도 있는 개발 도구가 포함되어 있습니다.

Python은 데이터 수집 및 처리에서 기능 생성을 거쳐 교육, 유효성 검사 및 모델 배포에 이르기까지 기계 학습 모델링에서 일반적으로 사용하는 워크플로의 모든 단계에서 사용되고 있습니다.

Azure 기계 학습 스튜디오에서는 기계 학습 환경의 다양한 부분에 Python 스크립트를 포함시키고, Microsoft Azure에 확장 가능한 운영 웹 서비스로 원활하게 게시하는 작업도 지원합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## 기계 학습에서 Python 스크립트의 디자인 원칙
Azure 기계 학습 스튜디오에서 Python의 기본 인터페이스는 그림 1에 표시된 [Python 스크립트 실행][execute-python-script] 모듈을 통해 실행됩니다.

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

그림 1. **Python 스크립트 실행** 모듈입니다.

[Python 스크립트 실행][execute-python-script] 모듈에서는 R 아날로그인 [R 스크립트 실행][execute-r-script] 모듈과 마찬가지로 최대 세 개의 입력을 허용하고 최대 두 개의 출력(아래 설명)을 생성합니다. 실행할 Python 코드는 `azureml_main`이라는 특별하게 명명된 진입점 함수로 매개 변수 상자에 입력됩니다. 다음은 이 모듈을 구현하는 데 사용하는 핵심 디자인 원칙입니다.

1.	*Python 사용자에게 자연스러워야 합니다.* 대부분의 Python 사용자는 코드를 모듈 내의 함수로 고려해야 하므로, 최상위 모듈에 실행 가능 문을 많이 두는 경우는 비교적 드뭅니다. 따라서 스크립트 상자도 일련의 문장이 아니라 특별하게 명명된 Python 함수를 사용합니다. 함수에 노출된 개체는 [Pandas](http://pandas.pydata.org/) 데이터 프레임 및 [NumPy](http://www.numpy.org/) 배열과 같은 표준 Python 라이브러리입니다.
2.	*로컬 및 클라우드 실행 간의 충실도가 높아야 합니다.* Python 코드를 실행하는 데 사용하는 백 엔드는 널리 사용되는 크로스 플랫폼 과학 Python 배포인 [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1을 기반으로 합니다. 가장 일반적인 Python 패키지가 거의 200개 포함되어 있습니다. 따라서 데이터 과학자는 자신의 로컬 Azure Machine Learning 호환 Anaconda 환경에서 코드를 디버그하고 한정할 수 있습니다. 그리고 [IPython](http://ipython.org/) Notebook 또는 [Python Tools for Visual Studio](http://aka.ms/ptvs)와 같은 기존 개발 환경을 사용하여 Azure Machine Learning 실험의 일부로 자신 있게 실행합니다. 또한 `azureml_main` 진입점은 바닐라 Python 함수이며 Azure 기계 학습 특정 코드 또는 SDK가 설치되어 있지 않아도 작성할 수 있습니다.
3.	*다른 Azure 기계 학습 모듈로 원활하게 구성할 수 있어야 합니다.* [Python 스크립트 실행][execute-python-script] 모듈이 입력 및 출력으로 표준 Azure 기계 학습 데이터 집합을 허용합니다. 기본 프레임워크를 통해 투명하고 효율적으로 Azure 기계 학습과 Python 런타임을 연결합니다(누락된 값 등의 지원 기능). 따라서 Python은 R 및 SQLite로 호출되는 워크플로를 비롯하여 기존 Azure 기계 학습 워크플로와 함께 사용할 수 있습니다. 따라서 다음과 같은 워크플로가 해당될 수 있습니다.
  * 데이터 사전 처리 및 정리에 Python과 Pandas를 사용,
  * SQL 변환으로 데이터를 피드하고, 여 데이터 집합을 조인하여 기능 구성,
  * Azure 기계 학습에서 광범위한 알고리즘 컬렉션을 사용하여 모델 학습 및
  * R을 사용하여 결과 평가 및 사후 처리.


## Python 스크립트에 대한 기계 학습의 기본 사용 시나리오
이 섹션에서는 [Python 스크립트 실행][execute-python-script] 모듈의 몇 가지 기본 사용에 대해 살펴봅니다. 앞서 언급한 대로 Python 모듈에 입력되는 모든 사항은 Pandas 데이터 프레임으로 노출됩니다. Python Pandas에 대한 정보 및 Python Pandas를 사용하여 효과적이고 효율적으로 데이터를 조작하는 방법은 W, McKinney의 저서 *Python for Data Analysis*(O'Reilly, 2012)에서 찾을 수 있습니다. 이 함수는 튜플, 목록 또는 Numpy 배열과 같이 Python [시퀀스](https://docs.python.org/2/c-api/sequence.html) 내부에 패키지된 단일 Pandas 데이터 프레임을 반환해야 합니다. 그러면 이 시퀀스의 첫 번째 요소가 모듈의 첫 번째 출력 포트에 반환됩니다. 이 스키마는 그림 2에 표시됩니다.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

그림 2. 입력 포트를 매개 변수에 매핑 및 출력 포트에 값 반환.

입력 포트가 `azureml_main` 함수에 매핑되는 방법에 대한 자세한 의미 체계는 표 1에 표시되어 있습니다.

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

표 1. 입력 포트를 함수 매개 변수에 매핑.

입력 포트와 함수 매개 변수 사이의 매핑은 위치와 관련됩니다. 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑되고, 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.

## 입력 및 출력 유형 변환
이전에 설명한 대로 Azure 기계 학습의 입력 데이터 집합은 Pandas의 데이터 프레임으로 변환되고, 출력 데이터 프레임은 다시 Azure 기계 학습 데이터 집합으로 변환됩니다. 다음과 같은 변환이 수행됩니다.

1.	문자열과 숫자 열은 현상태 그대로 변환되고, 데이터 집합의 누락된 값은 Pandas에서 ‘NA’ 값으로 변환됩니다. 그 반대의 경우에도 마찬가지로 변환됩니다(Pandas의 NA 값은 Azure 기계 학습의 누락된 값으로 변환됨).
2.	Pandas의 인덱스 벡터는 Azure Machine Learning에서 지원되지 않습니다. Python 함수의 모든 입력 데이터 프레임에는 항상 0부터 시작하여 행 수에서 1을 뺀 인덱스까지 64비트의 숫자 인덱스가 있습니다.
3.	Azure 기계 학습 데이터 집합에는 중복된 열 이름과 문자열이 아닌 열 이름이 있을 수 없습니다. 출력 데이터 프레임에 숫자가 아닌 열이 포함된 경우 프레임워크를 통해 해당 열에서 `str`을 호출합니다. 마찬가지로 중복된 모든 열 이름은 손상된 것으로 자동 처리하여 이름을 고유하게 유지합니다. 접미사(2)는 첫 번째 중복에 추가되고 (3)은 두 번째 중복에 추가되는 식입니다.

## Python 스크립트 운영 가능화
점수 매기기 실험에서 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시될 때 호출됩니다. 예를 들어, 그림 3에서는 단일 Python 식을 평가하는 코드가 포함된 점수 매기기 실험을 보여줍니다.

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

그림 3. Python 식을 평가하기 위한 웹 서비스입니다.

이 실험에서 생성된 웹 서비스는 Python 식(문자열로)을 입력으로 받아, Python 인터프리터에 보낸 다음, 식과 평가된 값 모두를 포함하는 표를 반환합니다.

## 기존 Python 스크립트 모듈 가져오기
많은 과학자들이 일반적으로 사용하는 사례는 기존 Python 스크립트를 Azure 기계 학습 실험으로 통합하는 것입니다. 모든 코드를 단일 스크립트 상자에 연결하고 붙여넣는 대신 [Python 스크립트 실행][execute-python-script] 모듈에서는 Python 모듈을 포함하는 zip 파일이 연결될 수 있는 세 번째 입력 포트를 허용합니다. 그런 다음 런타임 시 실행 프레임워크에서 파일의 압축을 해제하고 Python 인터프리터의 라이브러리 경로에 내용을 추가합니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다.

예를 들어, 간단한 “Hello, World” 함수를 포함하는 Hello.py라는 파일을 살펴보겠습니다.

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

그림 4. 사용자 정의 함수.

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

그림 5. 사용자 정의 Python 코드가 포함된 Zip 파일.

그런 다음 이 파일을 데이터 집합으로 Azure 기계 학습 스튜디오에 업로드합니다. 그림에 표시된 대로, Python 스크립트 실행의 세 번째 입력 포트에 연결하여 Hello.zip 파일에 포함된 Python 코드를 사용하는 간단한 실험을 만들어서 실행합니다.

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

그림 6. zip 파일로 업로드된 사용자 정의 Python 코드를 사용하는 샘플 실험.

모듈 출력에는 zip 파일의 패키지가 해제되었으며, `print_hello` 함수도 실행되었음을 표시합니다. ![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
그림 7. [Python 스크립트 실행][execute-python-script] 모듈에서 사용 중인 사용자 정의 함수.

## 시각화 작업
브라우저에서 시각화할 수 있는 MatplotLib를 사용하여 만든 그림은 [Python 스크립트 실행][execute-python-script]을 통해 반환할 수 있습니다. 그러나 R을 사용할 때 그림은 현상태 그대로 이미지로 자동 리디렉션되므로, Azure 기계 학습으로 다시 반환하려는 경우 명시적으로 모든 그림을 PNG 파일로 저장해야 합니다.

MatplotLib에서 이미지를 생성하려면 다음 절차를 완료해야 합니다.

* 기본 Qt 기반 렌더러에서 “AGG”로 백 엔드 전환
* 새 그림 개체 만들기
* 축을 가져오고 축에 모든 그림을 생성
* PNG 파일에 그림 저장

Pandas에서 scatter\_matrix 함수를 사용하여 산점도 행렬을 생성하는 이 프로세스는 다음 그림 8에 설명되어 있습니다.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

그림 8. MatplotLib 그림을 이미지에 저장.



그림 9에서는 이전에 표시된 스크립트를 사용하여 두 번째 출력 포트를 통해 그림을 반환하는 실험을 보여줍니다.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)
	 
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

그림 9. Python 코드에서 생성된 그림 시각화.

여러 수치를 서로 다른 이미지에 저장하여 반환하고, Azure Machine Learning 런타임 시 모든 이미지를 선택한 다음, 시각화를 위해 연결할 수 있습니다.


## 고급 예제
Azure Machine Learning에 설치된 Anaconda 환경에는 NumPy, SciPy 및 Scikits-Learn과 같은 공통 패키지가 들어 있으며, 일반 기계 학습 파이프라인의 다양한 데이터 처리 작업에 효율적으로 사용할 수 있습니다. 예를 들어, 다음 실험과 스크립트에서는 데이터 집합의 기능 중요도 점수를 계산하기 위해 Scikits-Learn의 앙상블 학습자 사용에 대해 설명합니다. 점수는 다른 기계 학습 모델에 피드하기 전에 감독된 기능 선택을 수행하는 데 사용할 수 있습니다.

중요도 점수를 계산한 다음 이 점수에 따라 기능의 순서를 지정하는 Python 함수가 아래 표시됩니다.

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

그림 10. 점수별로 기능의 순위를 지정하는 기능.   다음 실험에서는 Azure 기계 학습의 “Pima Indian Diabetes” 데이터 집합에 있는 기능의 중요도 점수를 계산하여 반환합니다.

![image12](./media/machine-learning-execute-python-scripts/figure9a.png) ![image13](./media/machine-learning-execute-python-scripts/figure9b.png)
	
그림 11. Pima Indian Diabetes 데이터 집합에서 기능의 순위를 지정하는 실험.

## 제한 사항 
[Python 스크립트 실행][execute-python-script]에는 현재 다음과 같은 제한 사항이 있습니다.

1.	*샌드박스 실행.* 현재 Python 런타임은 샌드박스로 작동하므로, 지속적인 방식으로 네트워크나 로컬 파일 시스템에 액세스할 수 없습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.
2.	*정교한 개발 및 디버깅 지원이 부족합니다.* 현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 런타임 시 모듈이 실패하는 경우, 전체 Python 스택 추적을 사용할 수 있지만 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.
3.	*단일 데이터 프레임 출력.* Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. 현재로서는 학습된 모델과 같은 임의의 Python 개체를 Azure 기계 학습 런타임에 직접 반환할 수 없습니다. 동일한 제한 사항이 있는 [R 스크립트 실행][execute-r-script]과 마찬가지로, 대부분의 경우 바이트 배열에 개체를 선택한 다음 데이터 프레임 내부에 반환할 수 있습니다.
4.	*Python 설치를 사용자 지정할 수 없음*. 현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 이 방법은 작은 모듈의 경우 가능하지만, 모듈이 크거나(특히 네이티브 DLL이 있는 모듈) 모듈 수가 많은 경우 번거로울 수 있습니다.


##결론
데이터 과학자가 [Python 스크립트 실행][execute-python-script] 모듈을 사용하여 Azure 기계 학습의 클라우드 호스팅 기계 학습 워크플로에 기존 Python 코드를 통합하고 웹 서비스의 일부로 원활하게 운영할 수 있습니다. Python 스크립트 모듈은 Azure 기계 학습의 다른 모듈과 자연스럽게 상호 운용하고, 데이터 탐색에서 사전 처리, 기능 추출, 결과 평가 및 후처리에 이르는 다양한 작업에 사용할 수 있습니다. 실행에 사용되는 백 엔드 런타임은 충분히 테스트되어 널리 사용되는 Python 배포인 Anaconda를 기반으로 합니다. 따라서 기존 코드 자산을 클라우드에 등록하기가 쉬워집니다.

[Python 스크립트 실행][execute-python-script] 모듈에 추가 기능(예: Python의 모듈을 학습하고 운영화하는 기능 및 Azure Machine Learning Studio에서의 코드 개발 및 디버깅 지원 향상)을 제공할 것으로 기대합니다.

## 다음 단계

자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=AcomDC_0914_2016-->