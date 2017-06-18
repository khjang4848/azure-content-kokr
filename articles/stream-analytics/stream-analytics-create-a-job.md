<properties 
	pageTitle="스트림 분석을 위한 데이터 분석 처리 작업을 만드는 방법 | Microsoft Azure" 
	description="스트림 분석을 위한 데이터 분석 처리 작업 만들기 | 학습 경로 세그먼트."
	keywords="데이터 분석 처리"
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

# 스트림 분석을 위한 데이터 분석 처리 작업을 만드는 방법

Azure 스트림 분석에서 최상위 리소스는 스트림 분석 작업입니다. 하나 이상의 입력 데이터 원본, 데이터 변환을 표현하는 쿼리 및 결과가 기록되는 하나 이상의 출력 대상으로 이루어져 있습니다. 이러한 요소가 모여서 데이터 시나리오 스트리밍을 위한 데이터 분석 처리 작업을 수행할 수 있게 해줍니다.

스트림 분석 사용을 시작하려면 먼저 새 스트림 분석 작업을 만듭니다. 이 작업에 대한 비용은 작업이 시작될 때까지 청구되지 않습니다.

1.  [Azure 클래식 포털](http://manage.windowsazure.com) 또는 [Azure 포털](https://portal.azure.com/)에서 온라인으로 로그인합니다.
2.  포털에서: **새로 만들기를 클릭**한 다음 사용자의 포털에 따라 **데이터 서비스** 또는 **데이터 분석**을 클릭한 다음 **Azure 스트림 분석** 또는 **스트림 분석**, **빨리 만들기**를 차례로 클릭합니다.

    ![데이터 분석 처리 작업 마법사](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    ![데이터 분석 처리 작업 만들기](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  스트림 분석 작업에 대한 원하는 구성을 지정합니다.
	- **작업 이름** 상자에 스트림 분석 작업을 식별하는 이름을 입력합니다. **작업 이름**의 유효성이 검사되면 작업 이름 상자에 녹색 확인 표시가 나타납니다. **작업 이름**에는 영숫자 문자와 '-' 문자만 포함될 수 있으며, 3자에서 63자 사이여야 합니다.
	- Azure 포털에서 **지역** 또는 Azure 포털에서 **위치**를 사용하여 작업을 실행하려는 지리적 위치를 지정합니다.
	- Azure 포털을 사용하는 경우 **지역별 모니터링 저장소 계정**으로 사용하려는 저장소 계정을 선택하거나 만듭니다. 이 저장소 계정은 이 지역에서 실행 중인 모든 스트림 분석 작업에 대한 모니터링 데이터를 저장하는 데 사용됩니다.
	- Azure 포털을 사용하는 경우, 응용 프로그램에 대한 관련된 리소스를 저장하는 신규 및 기존 **리소스 그룹**을 지정해야 합니다.

4.  새 스트림 분석 작업 옵션이 구성되면 **스트림 분석 작업 만들기**를 클릭합니다. 스트림 분석 작업을 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 알림 허브에서 진행률을 모니터링할 수 있습니다.

    ![데이터 분석 처리 작업 알림 허브](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Azure 포털 데이터 분석 처리 작업 만들기](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  새 작업이 **생성됨** 상태로 표시됩니다. **시작** 단추를 사용할 수 없습니다. 작업을 시작하려면 먼저 작업 입력, 쿼리 및 출력을 구성해야 합니다.

    ![데이터 분석 처리 작업 상태](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Azure 포털 데이터 분석 처리 작업 상태](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0914_2016-->