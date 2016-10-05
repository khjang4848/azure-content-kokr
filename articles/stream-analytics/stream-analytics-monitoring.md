<properties 
	pageTitle="스트림 분석 작업 모니터링 이해 | Microsoft Azure" 
	description="스트림 분석 작업 모니터링 이해" 
	keywords="쿼리 모니터"
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
	ms.date="09/19/2016" 
	ms.author="jeffstok"/>

# 스트림 분석 작업 모니터링 및 쿼리를 모니터링하는 방법 이해

## 소개: 모니터 페이지

Azure 관리 포털과 Azure 포털 둘 다 쿼리 및 작업 성능을 모니터링하고 문제를 해결하는 데 사용할 수 있는 핵심 성과 메트릭을 표시합니다.

Azure 관리 포털에서 실행 중인 스트림 분석 작업의 **모니터** 탭을 클릭하여 이러한 메트릭을 확인합니다. 모니터 페이지에 성능 메트릭이 표시되는 데는 대부분 1분 정도의 지연이 있습니다.

  ![작업 모니터링 대시보드](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

Azure 포털에서, 찾는 메트릭 중 관심있는 스트림 분석 작업을 찾은 다음 **모니터링** 섹션을 봅니다.

  ![Azure 포털 모니터링 작업 대시보드](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

어떤 영역에서 스트림 분석 작업이 처음 만들어지면 해당 영역에 대한 진단을 구성해야 합니다. 이 작업을 수행하려면 **모니터링** 섹션의 아무데나 클릭합니다. 그러면 **진단** 블레이드가 표시됩니다. 여기서 진단을 사용하도록 설정하고 모니터링 데이터를 위한 저장소 계정을 지정할 수 있습니다.

  ![Azure 포털 쿼리 진단 구성](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## 스트림 분석에 사용 가능한 메트릭


| 메트릭 | 정의 |
|--------|-------------|
| SU % 사용률 | 작업의 크기 조정 탭에서 작업에 할당한 스트리밍 단위의 사용률입니다. 이 표시가 80% 이상에 도달하면 이벤트 처리가 지연되거나 진행을 중단할 가능성이 큽니다. |
| 입력 이벤트 | 스트림 분석 작업이 받은 데이터의 양(이벤트 수)입니다. 입력 소스로 전송되는 이벤트의 유효성을 검사하는 데 사용할 수 있습니다. |
| 출력 이벤트 | 스트림 분석 작업이 출력 대상에 보낸 데이터의 양입니다(이벤트 수). |
| 순서 비지정 이벤트 | 이벤트 순서 지정 정책에 기반하여 조정된 타임스탬프를 받거나 삭제된 순서가 정해지지 않은 수신 이벤트의 수입니다. 잘못된 순서 허용 시간 설정의 구성에 의해 영향을 받을 수 있습니다. |
| 데이터 변환 오류 | 스트림 분석 작업에 의해 발생하는 데이터 변환 오류 수입니다. |
| 런타임 오류 | 스트림 분석 작업을 실행하는 동안 발생하는 오류 수입니다. |
| 늦은 입력 이벤트 | 지연 도착 허용 시간 설정의 이벤트 순서 지정 정책 구성에 기반하여 타임스탬프가 조정되었거나 삭제된 소스에서 늦게 도착한 이벤트의 수입니다. |

## Azure 관리 포털에서 모니터링 사용자 지정 ##

최대 6개의 메트릭을 차트에 표시할 수 있습니다.

상대 값(각 메트릭에 대한 최종 값만)과 절대 값(Y축에 표시됨) 표시를 전환하려면 차트 맨 위에서 상대 또는 절대를 선택합니다.

  ![쿼리 모니터 상대 절대](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

모니터 차트에서 1시간, 12시간, 24시간 또는 7일의 집계로 메트릭을 볼 수 있습니다.

메트릭 차트가 표시되는 시간 범위를 변경하려면 차트 맨 위에서 1시간, 24시간 또는 7일을 선택합니다.

  ![쿼리 모니터 시간 단위](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

작업이 정의된 임계값을 초과하는 경우 전자 메일로 알릴 수 있는 규칙을 설정할 수 있습니다.

## Azure 포털에서 모니터링 사용자 지정하기 ##

차트 편집 설정에서 차트 유형, 표시되는 메트릭 및 시간 범위를 조정할 수 있습니다. 자세한 내용은 [모니터링을 사용자 지정하는 방법](../azure-portal/insights-how-to-customize-monitoring.md)을 참조하세요.

  ![Azure 포털 쿼리 모니터 시간 단위](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## 작업 상태

작업의 목록을 볼 수 있는 Azure 클래식 포털에서 스트림 분석 작업의 상태를 확인할 수 있습니다. Azure 클래식 포털에서 스트림 분석 아이콘을 클릭하여 작업 목록을 볼 수 있습니다.

| 가동 상태 | 정의 |
|--------|------------|
| 작성자 | 그러나 작업이 만들어졌지만 시작되지는 않았습니다. |
| Starting | 사용자가 작업 시작을 클릭하면 작업이 시작됩니다. |
| 실행 중 | 작업이 할당되어 입력을 처리하거나 입력 처리를 대기 중입니다. 작업이 결과를 생성하지 않은 실행 중 상태인 경우, 데이터 처리 시간 창이 크거나 쿼리 논리가 복잡할 가능성이 있습니다. 또 다른 이유는 현재 작업에 전송되는 데이터가 없기 때문일 수 있습니다. |
| 중지 중 | 사용자가 작업 중지를 클릭하면 작업이 중지됩니다. |
| 중지됨 | 작업이 중지되었습니다. |
| 성능 저하됨 | 이 상태는 스트림 분석 작업에 일시적인 오류가 발생했음을 나타냅니다(예: 입력/출력 오류, 처리 오류, 변환 오류 등). 하지만 작업은 계속 실행되고 많은 오류가 생성됩니다. 이 작업은 고객의 주의가 필요하고 고객은 작업 로그에서 오류를 확인할 수 있습니다. |
| Failed | 오류로 인해 작업이 실패하고 처리가 중지되었음을 나타냅니다. 고객은 오류를 디버깅하기 위해 작업 로그를 확인해야 합니다. |
| 삭제 중 | 작업이 삭제됨을 나타냅니다. |

## 진단

Azure 관리 포털에서 작업 대시보드는 진단(즉, 입력, 출력 및/또는 작업 로그)을 위해 확인해야 하는 정보를 제공합니다. 해당 위치로 이동하는 링크를 클릭하여 진단을 확인할 수 있습니다.

  ![쿼리 모니터 오류](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

입력 또는 출력 리소스를 클릭하면 자세한 진단 정보가 제공됩니다. 작업이 실행되는 동안 최신 진단 정보로 새로 고쳐집니다.

  ![쿼리 진단](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->