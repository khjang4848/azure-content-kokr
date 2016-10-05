<properties 
	pageTitle="스트림 분석에서 스트리밍 작업을 시작 하는 방법 | Microsoft Azure" 
	description="Azure 스트림 분석에서 스트리밍 작업을 실행 하는 방법 | 학습 경로 세그먼트"
    keywords="스트리밍 작업"
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

# Azure 스트림 분석에서 스트리밍 작업을 실행 하는 방법

작업 입력, 쿼리 및 출력을 모두 지정했으면 스트림 분석 작업을 시작할 수 있습니다.

작업을 시작하려면

1.	Azure 클래식 포털의 작업 대시보드의 페이지 아래쪽에서 **시작**을 클릭합니다.

    ![작업 시작 단추](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    Azure 포털의 작업 페이지 맨 위에서 **시작**을 클릭합니다.

    ![Azure 포털 작업 시작 단추](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	**출력 시작** 값을 지정하여 이 작업이 출력 생성을 시작하는 시기를 결정합니다. 이전에 시작되지 않은 작업에 대한 기본 설정은 **작업 시작 시간**으로 이는 작업이 즉시 데이터 처리를 시작함을 의미합니다. 과거(기록 데이터 사용을 위해) 또는 미래(미래 시간까지 처리 지연을 위해)의 **사용자 지정** 시간을 지정할 수도 있습니다. 작업이 이전에 시작 및 중지된 경우 마지막 출력 시간부터 작업을 다시 시작하고 데이터 손실을 방지하기 위해 **마지막 중지 시간** 옵션을 사용할 수 있습니다.

    ![스트리밍 작업 시작 시간](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Azure 포털 스트리밍 작업 시작 시간](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	선택을 확인합니다. 작업 상태가 *시작 중* 으로 변경되었다가 작업이 시작되면 곧 *실행 중* 으로 변경됩니다. **알림 허브**에서 **시작** 작업의 진행률을 모니터링할 수 있습니다.

    ![스트림 작업 진행률](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Azure 포털 스트리밍 작업 진행률](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->