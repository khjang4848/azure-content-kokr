<properties 
	pageTitle="스트림 분석에서 쿼리를 작성하는 방법 | Microsoft Azure" 
	description="스트림 분석 및 쿼리 데이터 | 학습 경로 세그먼트에서 쿼리를 작성합니다."
	keywords="쿼리를 작성하는 방법, 쿼리 데이터, 쿼리 작성, 쿼리 작성하기"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

# 스트림 분석에서 쿼리를 작성하는 방법

Azure 스트림 분석의 스트림 처리 논리에 대한 쿼리 작성은 작업이 시작되기 전에 정의되고 데이터가 작업에 도착할 때 실행되는 "고정 쿼리"로 구현됩니다. 데이터 변환은 대체로 임시 의미 체계를 나타내는 데 사용되는 [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx)과 같은 언어 확장이 추가된 T-SQL의 하위 집합인, SQL과 유사한 쿼리 언어로 표현됩니다.

## 쿼리 작성: ##

1. Azure 관리 포털의 스트림 분석 작업에서 **쿼리**를 클릭합니다.

    ![쿼리 선택](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)

    Azure 포털에서 **쿼리**를 클릭합니다.

    ![쿼리 선택 미리 보기](./media/stream-analytics-write-queries/query-preview-portal.png)

2.	새 작업에는 시작하는 데 도움이 되는 쿼리 템플릿이 있습니다. 쿼리 템플릿은 입력 이벤트의 모든 필드를 출력에 프로젝션하는 "통과" 쿼리를 수행합니다.

    - 작업에 대해 입력 및 출력을 하나 이상 정의한 경우 자리 표시자 "[YourOutputAlias]" 및 "[YourInputAlias]" 필드를 먼저 사용하려는 입력 및 출력의 별칭으로 바꿀 수 있습니다. 또한 작업에 입력 및 출력을 정의하지 않고 Azure 클래식 포털에서 쿼리를 작성 및 테스트할 수도 있습니다.
    - 단순한 통과보다 더 많은 처리를 수행하려는 경우 쿼리 정의를 편집할 수 있습니다. 쿼리 작성을 시작하려면 [여기](stream-analytics-stream-analytics-query-patterns.md)에 캡처된 몇 가지 일반적인 쿼리 패턴을 살펴보세요.
  
    ![쿼리 데이터 창](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)

## 쿼리 데이터 작동의 유효성을 검사하려면 ##

브라우저에서 테스트 데이터가 포함된 하나 이상의 로컬 JSON 파일에 대해 쿼리를 실행하여 쿼리가 예상대로 동작하는지 테스트할 수 있습니다. 이 경우 작업이 시작되거나 요금이 청구되지 않습니다.

> [AZURE.NOTE] 현재 Azure 포털에서 브라우저 내 쿼리 테스트는 지원되지 않습니다.

1.	쿼리에 오류가 없는지 확인하고(오류가 있으면 테스트 단추를 사용할 수 없음) 테스트 단추를 클릭합니다.

    ![쿼리 데이터 테스트](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)

2.	쿼리에서 참조된 각 입력에 대해 파일을 지정하라는 메시지가 표시됩니다. 이 예제에서는 템플릿 쿼리가 그대로 유지되므로 "yourinputalias"라는 입력을 요청하는 대화 상자가 표시됩니다.

    ![테스트 데이터 쿼리](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)

3.	테스트 파일을 찾습니다. [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data)에서 여러 샘플 파일을 사용할 수 있으며, 입력 탭에서 Sample Data 함수를 통해 사용자 고유의 데이터 스트림 입력에서 샘플 데이터를 검색할 수도 있습니다.

    ![쿼리 입력](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)

4.	대화 상자를 닫으면 테스트 데이터에 대해 쿼리가 실행되고 쿼리 페이지의 맨 아래에 결과가 표시됩니다.

    ![쿼리 요약](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->