<properties 
	pageTitle="스트림 분석에서 작업 및 서비스 로그를 사용한 디버그 | Microsoft Azure" 
	description="스트림 분석 작업 로그 사용 방법" 
	keywords="서비스 로그"
	services="stream-analytics" 
	documentationCenter="" 
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

# 서비스 및 작업 로그를 사용하여 스트림 분석 작업 디버그

모든 Azure 서비스는 관리 작업과 관련된 세부 정보를 기록하는 작업 로깅 메시지를 사용자에게 제공합니다. Azure 스트림 분석에서 이 정보는 작업 상태, 작업 진행률 및 오류 메시지를 보고 시작부터 출력 처리까지 시간에 따른 작업 진행 상황을 추적하는 등 디버깅 목적으로 사용할 수 있습니다.

## Azure 관리 포털에서 작업 로그 찾기

작업 로그는 다음 두 가지 방법으로 액세스할 수 있습니다.

- 스트림 분석 작업의 대시보드
- Azure 클래식 포털의 관리 서비스

## 스트림 분석 작업의 대시보드

스트림 분석 작업의 해당 로그에 대한 링크는 작업의 대시보드 탭에 표시됩니다. 이 링크를 클릭하면 해당 특정 작업에 대한 최신 로그를 표시하는 방식으로 필터가 설정됩니다.

  ![관리 서비스 로그 선택](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## 관리 서비스

Azure 클래식 포털에서 스트림 분석 및 기타 서비스에 대한 작업 로그를 수동으로 이동하는 방법:

1.	[Azure 클래식 포털](https://manage.windowsazure.com)에서 **관리 서비스**를 클릭합니다.
2.	**유형**으로 **스트림 분석**을 선택하고 **서비스 이름**으로 작업 이름을 선택합니다.

  ![스트림 분석 선택](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Azure 포털에서 감사 로그 찾기 ##

Azure 포털에서 스트림 분석 작업에 대한 작업 로그를 찾으려면, **찾아보기**를 클릭하여 **감사 로그**를 선택합니다.

  ![Azure 포털 선택 스트림 분석](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)

그러면 구독의 모든 리소스에 대해 지난 7일 동안의 이벤트를 보여 주는 블레이드가 열립니다. **필터** 명령을 클릭하여 유형 또는 시간 프레임 지정 이벤트를 보도록 필터링할 수 있습니다.

  ![Azure 포털 선택 스트림 분석](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)

## 로그 세부 정보 가져오기

시간 범위 및 상태를 기준으로 필터링하여 작업 로그를 볼 수 있습니다.

Azure 관리 포털에서 창의 맨 아래에 있는 **세부 정보** 단추를 클릭하여 선택한 이벤트에 대한 세부 정보를 표시합니다.

  ![세부 정보 선택](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

Azure 포털에서 로그 항목을 클릭하여 그 안의 자세한 이벤트를 확인합니다.

  ![Azure 포털 선택 세부 정보](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)

거기에서 해당 이벤트를 클릭하면 **세부 정보** 블레이드를 열 수 있습니다.

  ![Azure 포털 선택 세부 정보](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)

## 실패한 작업 디버그

Azure 관리 포털에서 검색 아이콘을 클릭하고 '실패'를 입력합니다. 오류가 있는 모든 로그의 결과가 제공됩니다.

  ![실패한 작업 디버그](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Azure 포털에서 메시지 수준을 필터링하여 **위험** 이벤트를 볼 수 있습니다.

  ![Azure 포털 디버그](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)

오류 중 하나를 선택하고 **세부 정보**를 클릭하면 오류에 대한 자세한 내용을 확인할 수 있습니다. 일부 오류 메시지는 문제를 완화시키는 방법에 대한 정보도 제공합니다.

  ![작업 세부 정보](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

[지원](https://azure.microsoft.com/support/options/)에 문의하거나 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 통해 팀에 정보를 제공해야 하는 경우 작업 세부 정보, 특히 **상관관계 ID**를 적어 두세요.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->