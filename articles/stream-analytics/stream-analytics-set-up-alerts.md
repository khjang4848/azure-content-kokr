<properties 
	pageTitle="스트림 분석에서 쿼리에 대한 경고 설정 | Microsoft Azure" 
	description="스트림 분석 경고 이해" 
	keywords="경고 설정"
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


# Azure 스트림 분석 작업에 대한 경고 설정

## 소개: 모니터 페이지

메트릭이 지정한 조건에 도달하면 경고를 트리거하도록 경고를 설정할 수 있습니다.

예를 들어 "최근 15분간의 출력 이벤트가 100개 미만이면 메일 ID xyz@company.com으로 메일 알림을 보냅니다."라고 설정합니다.

포털을 통해 메트릭에 대한 규칙을 설정하거나 [프로그래밍 방식](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)으로 작업 로그 데이터에 대한 규칙을 구성할 수 있습니다.

## Azure 클래식 포털을 통해 경고 설정

Azure 관리 포털에서 알림을 설정하는 방법은 두 가지가 있습니다.

1.	스트림 분석 작업의 **모니터** 탭
2.	관리 서비스의 작업 로그

## 포털에서 작업의 모니터 탭을 통해 경고 설정

1.	모니터 탭에서 메트릭을 선택하고 대시보드의 맨 아래에 있는 **규칙 추가** 단추를 클릭한 다음 규칙을 설정합니다.

    ![대시보드](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	경고의 이름 및 설명 정의

    ![규칙 만들기](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	임계값, 경고 평가 기간 및 경고에 대한 작업을 입력합니다.

    ![조건 정의](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## 작업 로그를 통해 경고 설정

1.	[Azure 클래식 포털](https://manage.windowsazure.com)에 관리 서비스의 **경고** 탭으로 이동합니다.
2.	**규칙 추가**를 클릭합니다.

    ![조건](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	경고의 이름 및 설명을 정의합니다. 서비스 유형으로 '스트림 분석'을 선택하고 서비스 이름으로 작업 이름을 선택합니다.

    ![경고 정의](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Azure 포털에서 경고 설정 ##

Azure 포털에서 경고하고자 하는 스트림 분석 작업을 찾아 **모니터링** 섹션을 클릭합니다. **메트릭** 블레이드가 열리면 **경고 추가** 명령을 클릭합니다.

  ![Azure 포털 설치](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

경고 규칙의 이름을 지정하고 알림 전자 메일에 표시되는 설명을 선택할 수 있습니다.

메트릭을 선택하면 메트릭에 대한 조건 및 임계값을 선택할 수 있습니다.

  ![Azure 포털 선택 메트릭](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Azure 포털에서 경고를 구성에 대한 자세한 내용은 [경고 알림 받기](../azure-portal/insights-receive-alert-notifications.md)를 참조하세요.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->