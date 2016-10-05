<properties 
	pageTitle="시나리오를 파악하고 고급 분석 데이터 처리를 계획하는 방법 | Microsoft Azure" 
	description="일련의 주요 질문을 고려한 고급 분석 계획" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="bradsev" />


# 시나리오를 파악하고 고급 분석 데이터 처리를 계획하는 방법

데이터 집합에 대해 고급 분석 처리를 수행할 환경을 설정할 때 어떤 리소스를 포함하도록 계획해야 할까요? 이 문서는 시나리오에 적합한 작업과 리소스를 파악하는 데 도움이 될만한 것을 묻는 일련의 질문을 제시합니다. 예측 분석에 대한 고급 단계의 순서는 [TDSP(팀 데이터 과학 프로세스)란 무엇입니까?](data-science-process-overview.md)에 대략적으로 설명되어 있습니다. 각각의 단계에는 개별적인 시나리오에 적합한 리소스를 위한 특정 리소스가 필요합니다. 시나리오를 파악하기 위한 주요 질문은 데이터 로지스틱, 특징, 데이터 집합의 품질, 분석에 사용할 도구 및 언어에 대한 것입니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 로지스틱 질문: 데이터 위치 및 이동
로지스틱 질문은 **데이터 원본**의 위치, Azure 내 **대상**, 일정, 관련 이동량과 리소스를 비롯한 데이터 이동에 필요한 요구 사항에 대한 것입니다. 데이터는 분석 과정에서 여러 번 이동이 필요할 수 있습니다. 일반적인 시나리오는 로컬 데이터를 Azure에 있는 일정 형태의 저장소로 이동한 후 기계 학습 스튜디오로 이동하는 것입니다.

1. **데이터 원본은 무엇인가요?** 로컬인가요? 아니면 클라우드에 있나요? 예:
	- 데이터가 HTTP 주소에 공개적으로 제공됩니다.
	- 데이터가 로컬/네트워크 파일 위치에 있습니다.
	- 데이터가 SQL Server 데이터베이스에 있습니다.
	- 데이터가 Azure 저장소 컨테이너에 저장됩니다.

2. **Azure 대상은 무엇인가요?** 처리 또는 모델링을 위해 요구되는 대상은 무엇인가요? 예:
	- 데이터 이동
	- SQL Azure 데이터베이스
	- Azure VM에서 SQL Server
	- HDInsight(Azure의 Hadoop) 또는 하이브 테이블
	- Azure 기계 학습
	- 탑재식 Azure 가상 하드 디스크

3. **데이터를 어떻게 이동할건가요?** 다양한 저장소에 데이터를 수집하거나 로드하는 데 사용할 수 있는 프로시저와 리소스 및 처리 환경이 다음 문서에 약술되어 있습니다.

	-  [분석용 저장소 환경에 데이터 로드](machine-learning-data-science-ingest-data.md)
	-  [다양한 데이터 원본에서 Azure 기계 학습 스튜디오로 학습 데이터를 가져오기](machine-learning-data-science-import-data,md)

4. **데이터를 정기적으로 이동해야 하나요? 마이그레이션 중에 수정되어야 하나요?** 온-프레미스 및 클라우드 리소스를 모두 액세스하는 하이브리드 시나리오에서 데이터를 지속적으로 마이그레이션해야 하는 경우, 데이터를 트랜잭션 처리하거나 수정해야 하거나 마이그레이션 과정 중에 비즈니스 로직을 추가해야 하는 경우 Azure Data Factory(ADF)를 사용하는 것이 좋습니다. 자세한 내용은 [Azure Data Factory를 사용하여 온-프레미스 SQL Server에서 SQL Azure로 데이터 이동](machine-learning-data-science-move-sql-azure-adf.md)을 참조하세요.

5. **얼마나 많은 양의 데이터를 Azure로 이동해야 하나요?** 대량의 데이터 집합은 특정 환경의 저장소 용량을 초과할 수 있습니다. 예를 들어, 다음 섹션에서 기계 학습 스튜디오의 크기 제한에 대한 논의를 참조하세요. 이런 경우, 분석하는 동안 데이터 샘플이 사용될 수 있습니다. 다양한 Azure 환경에서 데이터 집합을 다운 샘플링하는 방법에 대한 자세한 내용은 [팀 데이터 과학 프로세스의 데이터 샘플링](machine-learning-data-science-sample-data.md)을 참조하세요.


## 데이터 특성 질문: 유형, 형식, 크기
이러한 질문은 다양한 유형의 데이터에 적합하고 특정한 제한 사항이 있는 저장소 및 처리 환경을 계획하는 핵심적인 내용입니다.

1. **데이터 유형은 무엇인가요?** 예:
	- 숫자
	- 범주
	- 문자열
	- 이진

2. **데이터 형식이 어떻습니까?** 예:
    - 쉼표로 구분되거나(CSV) 탭으로 구분된(TSV) 플랫 파일
    - 압축되거나 압축되지 않은
	- Azure Blob
	- Hadoop 하이브 테이블
	- SQL Server 테이블

2. **데이터가 얼마나 큽니까?**
    - 작음: 2GB 미만
    - 보통: 2GB보다 크고 10GB보다 작음
	- 큼: 10GB 초과

Azure 기계 학습 스튜디오 환경을 예로 들어 보겠습니다.

- Azure 기계 학습 스튜디오에서 지원되는 데이터 형식 및 유형의 목록은 [지원되는 데이터 형식 및 데이터 유형](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) 섹션을 참조하세요.
- Azure 기계 학습 스튜디오의 데이터 제한에 대한 정보는 [기계 학습 데이터 가져오기 및 내보내기](machine-learning-faq.md#machine-learning-studio-questions)의 **모듈에 대해 설정할 수 있는 데이터 집합의 크기는 어느 정도인가요?**를 참조하세요.

분석 과정에 사용되는 다른 Azure 서비스의 제한 사항에 대한 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## 데이터 품질 질문: 탐색 및 전처리

1. **데이터에 대해 무엇을 알고 있나요?** 데이터의 기본적인 특성에 대한 이해가 필요하면 데이터를 탐색합니다. 표시되는 패턴 또는 추세, 포함된 이상값 또는 값의 개수가 필요합니다. 이 단계는 가장 적합한 분석 기능이나 유형을 제시할 수 있는 가설을 수식화하고 추가적인 데이터 수집 계획을 수식화하는 데 필요한 전처리의 정도를 판단하는 데 중요합니다. 기술 통계 계산 및 시각화 표현은 데이터 검사에 유용한 기법입니다. 다양한 Azure 환경에서 데이터 집합을 탐색하는 방법에 대한 자세한 내용은 [팀 데이터 과학 프로세스에서 데이터 탐색](machine-learning-data-science-explore-data.md)을 참조하세요.

2. **데이터에 전처리 또는 정리가 필요한가요?** 데이터 전처리 및 정리는 일반적으로 기계 학습에 데이터 집합을 효과적으로 사용할 수 있기 전에 수행해야 하는 중요한 작업입니다. 원시 데이터는 노이즈가 많고, 불안정하고, 값이 누락된 경우가 종종 있습니다. 이러한 데이터를 모델링에 사용하면 결과가 잘못될 수 있습니다. 관련 설명은 [확장된 기계 학습을 위한 데이터 준비 작업](machine-learning-data-science-prepare-data.md)을 참조하세요.

## 도구 및 언어 질문
필요하거나 사용하기에 가장 편리한 언어 및 개발 환경 또는 도구에 따라서 많은 옵션이 있습니다.
 
1. **분석에 사용하려는 언어는 무엇인가요?**
	- R
	- Python
	- SQL

2. **데이터 분석에 사용해야 하는 도구는 무엇인가요?**
	- [Microsoft Azure Powershell](powershell-install-configure.md) – 스크립트 언어로 Azure 리소스를 관리하는 데 사용되는 스크립트 언어입니다.
	- [Azure 기계 학습 스튜디오](machine-learning-what-is-ml-studio/)
	- [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
	- [RStudio](http://www.rstudio.com)
	- [Python Tools for Visual Studio](http://microsoft.github.io/PTVS/)
	- [Anaconda](https://www.continuum.io/why-anaconda)
	- [Jupiter notebooks](http://jupyter.org/)
	- [Microsoft Power BI](http://powerbi.microsoft.com)


## 고급 분석 시나리오 파악
이전 섹션의 질문에 대답하고 나면 어떤 시나리오가 가장 적합한지 결정할 수 있습니다. 샘플 시나리오는 [Azure 기계 학습의 고급 분석 시나리오](machine-learning-data-science-plan-sample-scenarios.md)에 약술되어 있습니다.







 

<!---HONumber=AcomDC_0921_2016-->