<properties
	pageTitle="스트림 분석의 Power BI 대시보드 | Microsoft Azure"
	description="실시간 스트리밍 Power BI 대시보드를 사용하여 비즈니스 인텔리전스를 수집하고 스트림 분석 작업에서 대량의 데이터를 분석합니다."
	keywords="분석 대시보드, 실시간 대시보드"
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

#  스트림 분석 및 Power BI: 스트리밍 데이터에 대한 실시간 분석 대시보드

Azure 스트림 분석을 사용하면 최고의 비즈니스 인텔리전스 도구 중 하나인 Microsoft Power BI를 이용할 수 있습니다. Azure 스트림 분석을 사용하여 대용량, 스트리밍 데이터를 분석하고 실시간 Power BI 분석 대시보드에서 이해를 넓히는 방법에 대해 알아봅니다.

[Microsoft Power BI](https://powerbi.com/)를 사용하여 라이브 대시보드를 빠르게 만들 수 있습니다. [시나리오를 설명하는 동영상을 시청합니다](https://www.youtube.com/watch?v=SGUpT-a99MA).

이 문서에서는 Azure 스트림 분석 작업에 대한 출력으로 Power BI를 사용하여 사용자 고유의 사용자 지정 비즈니스 인텔리전스 도구를 만들고 실시간 대시보드를 활용하는 방법에 대해 알아봅니다.

## 필수 조건

* Microsoft Azure 계정
* 스트리밍 데이터를 소비하는 스트림 분석 작업에 대한 입력. 스트림 분석은 Azure 이벤트 허브 또는 Azure Blob 저장소의 입력을 허용합니다.
* Power BI에 대한 회사 또는 학교 계정

## Azure 스트림 분석 작업 만들기

[Azure 클래식 포털](https://manage.windowsazure.com)에서, **새로 만들기, 데이터 서비스, 스트림 분석, 빠른 생성**을 클릭합니다.

다음 값을 지정한 다음 **스트림 분석 작업 만들기**를 클릭합니다.

* **작업 이름** - 작업 이름을 입력합니다. 예를 들어, **DeviceTemperatures**입니다.
* **지역** - 작업이 위치할 지역을 선택합니다. 최적의 성능을 보장하고 지역 간의 데이터 전송 비용이 발생하지 않도록 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋습니다.
* **저장소 계정** -이 영역 내에서 실행되는 모든 스트림 분석 작업에 대한 모니터링 데이터를 저장하는데 사용하려는 저장소 계정을 선택합니다. 기존 저장소 계정을 선택하거나 새 계정을 만드는 옵션이 있습니다.

왼쪽 창에서 **스트림 분석**을 클릭하여 스트림 분석 작업을 표시합니다.

![graphic1][graphic1]

> [AZURE.TIP] 새 작업은 **시작되지 않음** 상태로 표시됩니다. 페이지 맨 아래의 **시작** 단추가 비활성화됩니다. 작업을 시작하기 전에 작업 입력, 출력, 쿼리 등을 구성해야 할 때 예상되는 동작입니다.

## 작업 입력 지정

이 자습서에서는 JSON 직렬화 및 UTF-8 인코딩을 포함한 입력으로 이벤트 허브를 사용한다고 가정합니다.

* 작업 이름을 클릭합니다.
* 페이지의 맨 위에서 **입력**을 클릭한 다음 **입력 추가**를 클릭합니다. 열리는 대화 상자에서 다양한 단계를 진행하면서 입력을 설정합니다.
*	**데이터 스트림**을 선택한 다음 오른쪽 단추를 클릭합니다.
*	**이벤트 허브**를 선택한 다음 오른쪽 단추를 클릭합니다.
*	세 번째 페이지에서 다음 값을 입력하거나 선택합니다.
  *	**입력 별칭** - 이 작업 입력의 이름을 입력합니다. 이 이름은 나중에 쿼리에서 사용하게 됩니다.
  * **이벤트 허브** -만든 이벤트 허브가 스트림 분석 작업과 동일한 구독에 포함된 경우 이벤트 허브가 있는 네임스페이스를 선택합니다.
*	이벤트 허브가 다른 구독에 있으면 **다른 구독의 이벤트 허브 사용**을 선택하고 **서비스 버스 네임스페이스**, **이벤트 허브 이름**, **이벤트 허브 정책 이름**, **이벤트 허브 정책 키** 및 **이벤트 허브 하티션 수**에 대한 정보를 수동으로 입력합니다.

> [AZURE.NOTE]	이 샘플에서는 기본값인 16개인 파티션을 사용합니다.

* **이벤트 허브 이름** - 사용하는 Azure 이벤트 허브의 이름을 선택합니다.
* **이벤트 허브 정책 이름** -사용 중인 이벤트 허브에 대한 이벤트 허브 정책을 선택합니다. 이 정책에 관리 권한이 있는지 확인합니다.
*	**이벤트 허브 소비자 그룹** – 이 그룹을 비우거나 이벤트 허브에 있는 소비자 그룹을 지정할 수 있습니다. 이벤트 허브의 각 소비자 그룹에는 한 번에 5개의 판독기만 허용됩니다. 따라서 사용자 작업에 따라 올바른 소비자 그룹을 결정합니다. 필드를 비워두면 기본 소비자 그룹을 사용합니다.

*	오른쪽 단추를 클릭합니다.
*	다음 값을 지정합니다.
  *	**이벤트 직렬 변환기 형식** - JSON
  *	**인코딩** - UTF8
*	확인 단추를 클릭하여 이 소스를 추가하고 스트림 분석이 이벤트 허브에 성공적으로 연결될 수 있는지 확인합니다.

## Power BI 출력 추가

1.  페이지의 맨 위에서 **출력**을 클릭한 다음 **출력 추가**를 클릭합니다. Power BI가 출력 옵션으로 표시됩니다.

    ![graphic2][graphic2]

2.  **Power BI**를 선택한 다음 오른쪽 단추를 클릭합니다.
3.  다음과 유사한 화면이 표시됩니다.

    ![graphic3][graphic3]

4.  이 단계에서는 스트림 분석 작업 출력을 위한 회사 또는 학교 계정을 제공합니다. Power BI 계정이 있는 경우 **지금 권한 부여**를 선택합니다. 그렇지 않은 경우 **지금 등록**을 선택합니다. [다음은 Power BI 등록에 대한 세부 정보를 통해 탐색하는 좋은 블로그입니다](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]

5.  그러면 다음과 같은 화면이 표시됩니다.

    ![graphic4][graphic4]

아래와 같이 값을 제공합니다.

* **출력 별칭** – 쉽게 참조할 수 있는 모든 출력 별칭을 입력할 수 있습니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우에 특히 유용합니다. 이 경우 쿼리에서 이 출력을 참조해야 합니다. 예를 들어, 출력 별칭 값 = “OutPbi”을 사용합니다.
* **데이터 집합 이름** - Power BI 출력에 포함할 데이터 집합 이름을 입력합니다. 예를 들어, “pbidemo”를 사용합니다.
*	**테이블 이름** -Power BI 출력의 데이터 집합 아래 테이블 이름을 입력합니다. "Pbidemo"라고 입력합니다. 현재, 스트림 분석 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다.
*	**작업 영역** – 데이터 집합을 만들려고 하는 Power BI 테넌트에서 작업 영역을 선택합니다.

>	[AZURE.NOTE] Power BI 계정에서 이 데이터 집합과 테이블을 명시적으로 만들면 안 됩니다. 스트림 분석 작업을 시작하면 자동으로 만들어지며 이 작업에서 출력을 Power BI로 펌프하기 시작합니다. 작업 쿼리에서 아무 결과도 반환하지 않는 경우에는 데이터 집합과 테이블이 만들어지지 않습니다.

*	**확인**, **연결 테스트**를 클릭하면 이제 출력 구성이 완료됩니다.

>	[AZURE.WARNING] 또한 이 스트림 분석 작업에서 제공한 이름과 동일한 이름의 데이터 집합과 테이블이 Power BI에 이미 있는 경우에는 기존 데이터를 덮어씁니다.


## 쿼리 작성

작업의 **쿼리** 탭으로 이동합니다. Power BI에서 원하는 쿼리, 출력을 작성합니다. 예를들어 다음 SQL 쿼리와 같을 수 있습니다.

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
        OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,1),
    	dspl



사용자의 작업을 시작합니다. 이벤트 허브는 이벤트를 수신하고 쿼리가 예상된 결과를 생성하는지 확인합니다. 쿼리가 0개의 행을 출력하면, Power BI 데이터 집합 및 테이블이 자동으로 만들어지지 않습니다.

## Power BI에서 대시보드 만들기

[Powerbi.com](https://powerbi.com)으로 이동하고 회사 또는 학교 계정으로 로그인합니다. 스트림 분석 작업 쿼리가 결과를 출력하면, 사용자 데이터 집합이 이미 생성되어 표시됩니다.

![graphic5][graphic5]

대시보드를 만들기 위해 대시보드 옵션으로 이동하고 새 대시보드를 만듭니다.

![graphic6][graphic6]

이 예에서는 "데모 대시보드"라고 레이블을 지정합니다.

이제 스트림 분석 작업에서 만든 데이터 집합을 클릭합니다(현재 예제에서는 pbidemo). 이 데이터 집합 위에 차트를 만들려면 페이지로 이동합니다. 다음은 만들 수 있는 보고서의 한 예입니다.

Σ temp 및 시간 필드를 선택합니다. 자동으로 차트에 대한 값과 축으로 이동됩니다.

![graphic7][graphic7]

이렇게 하면 아래와 같이 차트를 자동으로 가져옵니다.

![graphic8][graphic8]

값 섹션에서 temp에 대한 드롭 다운을 클릭하고 차트의 온도로 **평균**을 선택하고 **시각화**를 선택하고 **꺾은선형 차트**를 선택합니다.

![graphic9][graphic9]

이제 시간이 지남에 따라 꺾은선형 차트의 평균을 얻을 수 있습니다. 아래와 같이 Pin 옵션을 사용하면, 이전에 만든 대시보드로 고정할 수 있습니다.

![graphic10][graphic10]

이제이 고정된 이 보고서를 사용하여 대시보드를 보면 실시간으로 업데이트되는 보고서가 표시됩니다. 스파이크 temp나 이와 같은 이벤트에서 데이터를 변경하려고 시도하면 사용자 대시보드에 반영된 실시간 효과가 표시됩니다.

이 자습서에서는 데이터 집합에 대한 한 종류의 차트를 만드는 방법에 대해 설명합니다. Power BI는 조직에 대한 다른 고객 비즈니스 인텔리전스 도구를 만드는 데 도움이 됩니다. Power BI 대시보드의 다른 예제는 [Power BI 시작](https://youtu.be/L-Z_6P56aas?t=1m58s) 비디오를 보세요.

Power BI 출력 구성 및 Power BI 그룹 사용 방법에 대한 자세한 내용은 [스트림 분석 출력 이해](stream-analytics-define-outputs.md "스트림 분석 출력 이해")의 [Power BI 섹션](stream-analytics-define-outputs.md#power-bi)을 참조하세요. Power BI를 사용한 대시보드 만들기에 대해 알 수 있는 다른 유용한 리소스는 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)입니다.

## 제한 사항 및 모범 사례

Power BI는 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI 가격 책정")에 설명된 바와 같이 동시성과 처리량 제약 조건을 모두 사용합니다.

때문에 Power BI는 Azure 스트림 분석으로 상당한 데이터 부하 감소가 이루어 지는 경우에 가장 적합합니다. TumblingWindow 또는 HoppingWindow를 사용하여 데이터 푸시가 최대 1초마다 푸시되도록 하고 쿼리가 처리량 요구 사항 이내에 들도록 하는 것이 좋습니다. 다음 수식을 사용하여 수 초 내에 창에 공급할 값을 계산할 수 있습니다.

![수식 1](./media/stream-analytics-power-bi-dashboard/equation1.png)

예를 들어 매초마다 데이터를 보내는 1,000개의 장치가 있다면 시간당 1,000,000행을 지원하는 Power BI Pro SKU를 사용하면서 Power BI에서 장치별 평균 데이터를 확인하고 아래 표시된 것과 같이 장치별로 최대 4초마다 푸시를 수행할 수 있습니다.

![수식 2](./media/stream-analytics-power-bi-dashboard/equation2.png)

이를 위해 원래 쿼리가 아래와 같이 변경됩니다.

    SELECT
    	MAX(hmdt) AS hmdt,
    	MAX(temp) AS temp,
    	System.TimeStamp AS time,
    	dspl
    INTO
    	OutPBI
    FROM
    	Input TIMESTAMP BY time
    GROUP BY
    	TUMBLINGWINDOW(ss,4),
    	dspl

### PowerBI 보기 새로 고침

일반적인 질문은 다음과 같습니다. "PowerBI에서 대시보드가 왜 자동으로 업데이트되지 않나요?"

이에 대한 답변을 얻기 위해 PowerBI에서 질문 및 답변에 "타임스탬프가 오늘인 임시 최대값"과 같이 질문하고 대시보드에 해당 타일을 고정합니다.

### 권한 부여 갱신

작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우에 Power BI 계정을 다시 인증해야 합니다. MFA(Multi-Factor Authentication)가 AAD(Azure Active Directory) 테넌트에 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 이 문제의 증상은 작업 출력이 없으며 작업 로그에 "사용자 인증 오류"가 표시됩니다.

![graphic12][graphic12]

마찬가지로 토큰이 만료된 동안 작업이 시작되려고 하는 경우 오류가 발생하고 작업이 실패합니다. 오류는 다음과 같이 표시됩니다.

![PowerBI 유효성 검사 오류](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png)
 

이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다. "권한 부여 갱신" 링크를 클릭하고 마지막 중지 시간부터 작업을 다시 시작하여 데이터 손실을 방지합니다.

![PowerBI 유효성 검사 갱신](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png)

Power BI를 사용하여 권한 부여가 새로 고쳐지면 권한 부여 영역에서 녹색 경고가 표시됩니다.

![PowerBI 유효성 검사 갱신](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png)

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

<!---HONumber=AcomDC_0921_2016-->