<properties 
	pageTitle="스트림 분석 릴리스 정보 | Microsoft Azure" 
	description="스트림 분석 릴리스 정보" 
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

#스트림 분석 릴리스 정보

## 스트림 분석의 2016/04/15 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함됩니다.

제목 | 설명
---|---
Power BI 출력에 대한 일반 공급 | [Power BI 출력](stream-analytics-power-bi-dashboard.md)이 이제 일반 공급됩니다. Power BI에 대한 90일 권한 부여 만료가 제거되었습니다. 권한 부여를 갱신해야 하는 시나리오에 대한 자세한 내용은 Power BI 대시보드 만들기의 [권한 부여 갱신](stream-analytics-power-bi-dashboard.md#Renew-authorization) 섹션을 참조하세요.

## 스트림 분석의 03/03/2016 릴리스 정보 ##

이 릴리스에는 다음 업데이트가 포함됩니다.

제목 | 설명
---|---
새 스트림 분석 쿼리 언어 항목 | SAQL에 이제 [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN 페이지"), [TRY\_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN 페이지") 및 [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN 페이지")가 있습니다.

## 스트림 분석의 2015/12/10 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함됩니다.

제목 | 설명
---|---
REST API 버전 업데이트 | REST API 버전은 2015-10-01에 업데이트되었습니다. 자세한 내용은 MSDN의 [스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx) 및 [스트림 분석에서 기계 학습 통합](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)에서 확인할 수 있습니다.
Azure 기계 학습 통합 | 이 릴리스에서는 Azure 기계 학습 사용자 정의 함수에 대해 지원합니다. 추가 정보는 [자습서](stream-analytics-machine-learning-integration-tutorial.md) 및 [일반 블로그 알림](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx)을 참조합니다.

## 스트림 분석의 2015/11/12 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함됩니다.

제목 | 설명
---|---
SELECT의 새 동작 | 스트림 분석의 SELECT가 중첩된 레코드의 속성 접근자로 *를 허용하도록 확장되었습니다. 추가 정보는[http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "복합 데이터 형식")를 참조하세요.

## 스트림 분석의 2015/10/22 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목 | 설명
---|---
추가 쿼리 언어 기능 | 스트림 분석은 [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) 및 [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx) 기능을 포함하여 쿼리 언어를 확장합니다.
집계 제한 사항이 제거되었습니다. | 이 릴리스는 쿼리에서 15개의 집계 제한 사항을 제거합니다. 쿼리당 집계 수에는 이제 제한이 없습니다.
GROUP BY System.Timestamp 기능 추가됨 | 이제 [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) 기능은 window\_type 또는 [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx) 중에 하나를 허용합니다.
연속 및 도약 창에 대한 오프셋 추가됨 | 기본적으로 [연속](https://msdn.microsoft.com/library/azure/dn835055.aspx) 및 [도약](https://msdn.microsoft.com/library/azure/dn835041.aspx) 창은 0으로 된 시간에 대해 정렬됩니다(1/1/0001 12:00:00 AM UTC). 새(선택 사항) 매개 변수 'offsetsize'는 사용자 지정 오프셋(또는 맞춤) 지정을 허용합니다.


## 스트림 분석의 2015/09/29 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목 | 설명
---|---
Azure IoT Suite 공개 미리 보기 | 스트림 분석이 Azure IoT Suite의 공개 미리 보기에 포함되어 있습니다.
Azure 포털 통합 | Azure 관리 포털에서 지속적으로 존재할 뿐만 아니라 이제 스트림 분석은 [Azure 포털](https://azure.microsoft.com/overview/preview-portal/)에 통합되었습니다. Preview 포털의 스트림 분석 기능은 현재 Azure 관리 포털에서 제공되는 기능의 하위 집합이지만 브라우저 내 쿼리 테스트, Power BI 출력 구성, 액세스한 구독에서 새 입력 및 출력 리소스로 이동 또는 만들기에 대한 지원은 없습니다.
DocumentDB 출력에 대한 지원 | 이제 스트림 분석 작업은 [DocumentDB](https://azure.microsoft.com/services/documentdb/)로 출력할 수 있습니다.
IoT Hub 입력에 대한 지원 | 이제 스트림 분석 작업이 IoT Hub에서 데이터를 수집할 수 있습니다.
다른 유형의 이벤트를 위한 TIMESTAMP BY | 단일 데이터 스트림에 서로 다른 필드의 타임스탬프를 가진 여러 이벤트 형식이 포함된 경우 이제 식에서 [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx)를 사용하여 각 사례에 대해 서로 다른 타임스탬프 필드를 지정할 수 있습니다.

## 스트림 분석의 2015/09/10 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목|설명
---|---
PowerBI 그룹에 대한 지원|다른 Power BI 사용자와 데이터 공유를 사용할 수 있도록 이제 스트림 분석 작업에서 Power BI 계정 내의 [Power BI 그룹](stream-analytics-define-outputs.md#power-bi)에 쓸 수 있습니다.

## 스트림 분석의 2015/08/20 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목|설명
---|---
추가된 LAST 함수 |이제 [LAST](http://msdn.microsoft.com/library/mt421186.aspx) 함수를 스트림 분석 작업에서 사용할 수 있으며 특정 기간 내의 이벤트 스트림에서 최신 이벤트를 검색할 수 있습니다.
새 배열 함수|이제 배열 함수 [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) 및 [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx)를 사용할 수 있습니다.
새 레코드 함수|이제 레코드 함수 [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) 및 [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx)를 사용할 수 있습니다.

## 스트림 분석의 2015/07/30 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목|설명
---|---
Azure ID에서 분리된 Power BI 조직 ID|이 기능을 통해 모든 Azure 계정 형식의 ASA 작업에 대한 [Power BI 출력](stream-analytics-power-bi-dashboard.md)을 사용할 수 있습니다(Live ID 또는 조직 ID). 또한 Azure 계정에 대한 하나의 조직 ID를 포함하고 Power BI 출력 권한 부여를 위한 다른 하나를 사용할 수 있습니다.
서비스 버스 큐 출력에 대한 지원|이제 스트림 분석 작업에서 [서비스 버스 큐](stream-analytics-connect-data-event-outputs.md#service-bus-queues) 출력을 사용할 수 있습니다.
서비스 버스 토픽 출력에 대한 지원|이제 스트림 분석 작업에서 [서비스 버스 토픽](stream-analytics-connect-data-event-outputs.md#service-bus-topics) 출력을 사용할 수 있습니다.

## 스트림 분석의 2015/07/09 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.


제목|설명
---|---
사용자 지정 Blob 출력 분할|이제 Blob 저장소 출력은 출력 Blob이 작성되는 빈도 및 구조와 출력 데이터 경로 폴더 구조의 형식을 지정하는 옵션을 허용합니다. 

## 스트림 분석의 2015/05/03 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.


제목|설명
---|---
순서 비지정 허용 오차 창의 최대 증가 값|순서 비지정 허용 오차 창의 최대 크기는 이제 59:59(MM:SS)입니다.
JSON 출력 형식: 구분 줄 또는 배열|이제 Blob 저장소 또는 이벤트 허브로 출력 시 JSON 개체의 배열이나 새 줄로 JSON 개체를 구분하여 출력하는 옵션이 있습니다. 

## 스트림 분석의 2015/04/16 릴리스에 대한 정보 ##


제목|설명
---|---
Azure 저장소 계정 구성 지연|처음에 한 지역에서 스트림 분석 작업을 만들면 해당 지역에서 스트림 분석 작업을 모니터링하기 위해 새 저장소 계정을 만들거나 기존 계정을 지정하라는 메시지가 표시됩니다. 모니터링을 구성하는 데 다소 시간이 소요되므로 동일한 지역에서 30분 이내에 다른 스트림 분석 작업을 만들면 모니터링 저장소 계정 드롭다운에 최근에 구성한 계정이 표시되지 않고 또 다른 저장소 계정을 지정하도록 요구됩니다. 불필요한 저장소 계정을 만들지 않으려면 한 지역에서 추가 작업을 프로비전하기 전에 해당 지역에서 처음 작업을 만든 후에 30분 정도 기다립니다.
작업 업그레이드|현재 스트림 분석에서는 실행 중인 작업의 정의 또는 구성을 실시간으로 편집할 수 없습니다. 실행 중인 작업의 입력, 출력, 쿼리, 규모 또는 구성을 변경하려면 먼저 작업을 중지해야 합니다.
입력 원본에서 유추되는 데이터 형식|CREATE TABLE 문을 사용하지 않은 경우 입력 유형은 입력 형식에서 파생됩니다. 예를 들어 CSV의 모든 필드는 문자열입니다. 형식 불일치 오류를 방지하려면 CAST 함수를 사용하여 필드를 올바른 형식으로 명시적으로 변환해야 합니다.
누락된 필드는 null 값으로 출력됨|입력 원본에 없는 필드를 참조하면 출력 이벤트에 null 값이 생성됩니다.
WITH 문은 SELECT 문 앞에 와야 함|쿼리에서 SELECT 문은 WITH 문에 정의된 하위 쿼리를 따라야 합니다.
메모리 부족 문제|순서가 지정되지 않은 이벤트 및/또는 많은 양의 상태를 유지하는 복합 쿼리에 대해 허용 오차가 큰 스트리밍 분석 작업을 수행하면 메모리가 부족하여 작업이 다시 시작됩니다. 시작 및 중지 작업은 작업의 작업 로그에 표시됩니다. 이러한 동작을 방지하려면 여러 파티션에 걸쳐 쿼리 출력 크기를 조정합니다. 향후 릴리스에서는 영향 받는 작업을 다시 시작하지 않고 성능을 저하시켜 이러한 제한 문제를 해결할 예정입니다.
페이로드 타임스탬프가 없는 대용량 Blob 입력은 메모리 부족 문제를 일으킬 수 있습니다.|TIMESTAMP BY를 통해 타임스탬프 필드를 지정하지 않은 경우 Blob 저장소에서 대용량 파일을 사용하면 스트림 분석 작업이 중단될 수 있습니다. 이 문제를 방지하려면 각 Blob을 10MB 미만으로 유지하세요.
SQL 데이터베이스 이벤트 볼륨 제한|SQL 데이터베이스를 출력 대상으로 사용할 경우 대용량 데이터가 있으면 스트림 분석 작업 시간이 초과될 수 있습니다. 이 문제를 해결하려면 집계 또는 필터 연산자를 사용하여 출력 볼륨을 줄이거나 Azure Blob 저장소 또는 이벤트 허브를 출력 대상으로 대신 선택하세요.
PowerBI 데이터 집합은 하나의 테이블만 포함할 수 있습니다.|PowerBI는 지정된 데이터 집합에서 둘 이상의 테이블을 지원하지 않습니다.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0921_2016-->