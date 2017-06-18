<properties 
	pageTitle="기계 학습 앱: 이상 감지 서비스 | Microsoft Azure" 
	description="이상 감지 API는 Microsoft Azure 기계 학습을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="alokkirpal" 
	manager="jhubbard"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/22/2016" 
	ms.author="alokkirpal"/>


# 기계 학습 이상 감지 서비스#

##개요

이상 감지 API는 Azure 기계 학습을 사용하여 빌드한 예로서, 시간 간격이 불균일한 숫자 값이 있는 시계열 데이터에서 이상을 감지합니다.

이 API는 시계열 데이터에서 다음과 같은 유형의 이상 패턴을 감지할 수 있습니다.

* **긍정적인 추세 및 부정적인 추세**: 예를 들어, 계산에서 메모리 사용량을 모니터링 할 때, 상향 추세는 메모리 누수를 나타낼 수 있기 때문에 유용할 수 있습니다.

* **값의 동적 범위 변화**: 예를 들어 클라우드 서비스에서 발생하는 예외를 모니터링할 때 값의 동적 범위 변화는 서비스 상태의 불안정성을 나타낼 수 있습니다.

* **급증 및 급락**: 예를 들어 서비스 로그인 실패 횟수 또는 전자 상거래 사이트의 체크 아웃 수를 모니터링할 때 급증 또는 급락은 이상 동작을 나타낼 수 있습니다.

이러한 기계 학습 감지기는 시간에 따른 값의 변화를 추적하여 해당 값의 계속적인 변화를 이상 점수로 보고합니다. 임시 임계값을 조정할 필요 없이 해당 점수를 사용하여 가양성 비율을 제어할 수 있습니다. 이상 감지 API는 시간에 따른 KPI 추적을 통한 서비스 모니터링, 검색 횟수, 클릭 횟수 등의 메트릭을 통한 사용 현황 모니터링, 시간에 따른 메모리, CPU, 파일 읽기 등과 같은 카운터를 통한 성능 모니터링 등의 몇 가지 시나리오에 유용합니다.

이상 감지 제품에는 시작하기에 유용한 도구가 함께 제공됩니다.

* [웹 응용 프로그램](http://anomalydetection-aml.azurewebsites.net/)은 데이터에서 비상 감지 API의 결과를 평가하고 시각화하는 데 도움이 됩니다.

* [샘플 코드](http://adresultparser.codeplex.com/)는 프로그래밍 방식으로 API에 액세스하고 결과를 C#으로 구문 분석하는 방법을 보여줍니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##API 정의

이 서비스는 웹 또는 모바일 응용 프로그램, R, Python, Excel 등 다양한 방식으로 사용될 수 있는 REST 기반 API over HTTPS를 제공합니다. REST API 호출을 통해 시계열 데이터를 이 서비스로 보내 위에 설명된 세 가지 이상 유형의 조합을 실행하도록 할 수 있습니다. 이 서비스는 비즈니스 요구 사항에 맞게 원활하게 확장되며 SLA를 제공하는 Azure 기계 학습 플랫폼에서 실행됩니다.

###헤더
다음과 같이 요청에 올바른 헤더가 포함되었는지 확인합니다.

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

[Azure 데이터 마켓](https://datamarket.azure.com/account/keys)에서 계정의 계정 키를 찾을 수 있습니다.

###Score API

Score API는 비계절성 시계열 데이터에 이상 감지를 실행하는 데 사용됩니다. API는 데이터에 다수의 비정상 감지기를 실행하고 그에 해당하는 비정상 점수를 반환합니다. 아래 그림은 Score API에서 감지할 수 있는 이상의 예를 보여줍니다. 이 시계열에는 두 개의 뚜렷한 수준 변화와 세 개의 급증이 있습니다. 빨간색 점은 수준 변화가 감지된 시간을 표시하며, 검은색 점은 감지된 급증을 표시합니다.

![Score API][1]
	
**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**요청 본문 예제**

아래 요청은, 이상 데이터 요소가 있는지를 감지하기 위해 API에 2016년 3월1일에서 2016년 3월10일까지의 데이터 요소를 포함하는 시계열(잘려 보임)과 급증 감지기 매개 변수를 보냅니다.

	{
	  "data": [
	    [ "9/21/2014 11:05:00 AM", "3" ],
	    [ "9/21/2014 11:10:00 AM", "9.09" ],
	    [ "9/21/2014 11:15:00 AM", "0" ]
	  ],
	  "params": {
	    "tspikedetector.sensitivity": "3",
	    "zspikedetector.sensitivity": "3",
	    "trenddetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "postprocess.tailRows": "2"
	  }
	}

이에 대한 응답은 다음과 같습니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
	  "ADOutput":"{
	    "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
		"ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
		"Values":[
		  ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
		  ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
		]
	  }"
	}

###ScoreWithSeasonality API

ScoreWithSeasonality API는 계절성 패턴이 있는 시계열에 대한 이상 감지를 실행하는 데 사용됩니다. 이 API는 계절성 패턴의 편차를 감지하는 데 유용합니다.

다음 그림은 계절성 시계열에서 감지된 이상의 예를 보여줍니다. 시계열에는 한 개의 급증(첫 번째 검정색 점), 두 개의 급락(두 번째와 끝에 있는 검정색 점), 한 개의 수준 변화(빨간색 점)가 있습니다. 시계열 중간의 급락 및 수준 변화 모두 시계열에서 계절성 구성 요소가 제거된 후에만 인식이 가능합니다.

![Seasonality API][2]

**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**요청 본문 예제**

아래 요청은, 이상 데이터 요소가 있는지를 감지하기 위해 API에 2016년 3월1일에서 2016년 3월10일까지의 데이터 요소를 포함하는 시계열(잘려 보임)과 매개 변수를 보냅니다.

	{
	  "data": [
	    [ "9/21/2014 11:10:00 AM", "9" ],
	    [ "9/21/2014 11:15:00 AM", "12" ],
	    [ "9/21/2014 11:20:00 AM", "10" ]
	  ],
	  "params": {
	    "preprocess.aggregationInterval": "0",
	    "preprocess.aggregationFunc": "mean",
	    "preprocess.replaceMissing": "lkv",
	    "postprocess.tailRows": "1",
	    "zspikedetector.sensitivity": "3",
	    "tspikedetector.sensitivity": "3",
	    "upleveldetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "trenddetector.sensitivity": "3.25",
	    "detectors.historywindow": "500",
	    "seasonality.enable": "true",
	    "seasonality.transform": "deseason",
	    "seasonality.numSeasonality": "1"
	  }
	}

이에 대한 응답은 다음과 같습니다.

	{
		"odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
		"ADOutput": "{
			"ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
		  	"ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
		  	"Values":[
		    	["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
		  	]
		}"
	}

###감지기

이상 감지 API는 3가지 폭넓은 범주의 감지기를 지원합니다. 각 감지기의 구체적인 입력 매개 변수 및 출력에 대한 자세한 내용은 다음 테이블에서 찾을 수 있습니다.

|감지기 범주|감지기|설명|입력 매개 변수|출력
|---|---|---|---|---|
|급증 감지기|TSpike 감지기|집합 민감도에 따른 급증 및 급락 감지|*tspikedetector.sensitivity:*는 1-10 범위의 정수 값을 취하고, 기본값은 3이며, 값이 높을수록 민감도가 낮습니다.|TSpike: 이진 값 – 급증/급락이 감지되면 ‘1’, 그 외 경우는 ‘0’|
||ZSpike 감지기|집합 민감도에 따른 급증 및 급락 감지|*zspikedetector.sensitivity:*는 1-10 범위의 정수 값을 취하고, 기본값은 3이며, 값이 높을수록 민감도가 낮습니다.|ZSpike: 이진 값 – 급증/급락이 감지되면 ‘1’, 그 외 경우는 ‘0’|
|느린 추세 감지기|느린 추세 감지기|집합 민감도에 따른 느리고 긍정적인 추세 감지|*trenddetector.sensitivity:* 감지기 점수 임계값(기본값: 3.25, 3.25 – 5는 선택하기에 적절한 범위입니다. 값이 높을수록 민감도가 낮습니다.)|TScore: 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자|
|수준 변화 감지기|단방향 수준 변화 감지기|집합 민감도에 따른 상향 수준 변경 감지|*upleveldetector.sensitivity:* 감지기 점수 임계값(기본값: 3.25, 3.25 – 5는 선택하기에 적절한 범위입니다. 값이 높을수록 민감도가 낮습니다.)|PSscore: 상향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자|
||양방향 수준 변화 감지기|집합 민감도에 따른 상향 및 하향 수준 변경 감지|*bileveldetector.sensitivity:* 감지기 점수 임계값(기본값: 3.25, 3.25 – 5는 선택하기에 적절한 범위입니다. 값이 높을수록 민감도가 낮습니다.)|RPScore: 상향 및 하향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자

###매개 변수

이러한 입력 매개 변수에 대한 자세한 정보는 아래 테이블에 나열되어 있습니다.

|입력 매개 변수|설명|기본 설정|형식|유효 범위|제안 범위|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|입력 시계열 집계에 대한 초 단위 집계 간격|0(집계가 수행되지 않음)|정수|0: 집계 건너뜀, > 0 기타|5분에서 1일, 시계열 종속
|preprocess.aggregationFunc|지정된 AggregationInterval로 데이터를 집계하는 데 사용되는 함수|평균|열거형|평균, 합계, 길이|해당 없음|
|preprocess.replaceMissing|누락된 데이터를 입력하는 데 사용되는 값|lkv(마지막으로 알려진 값)|열거형|0, lkv, 평균|해당 없음|
|detectors.historyWindow|이상 점수 계산에 사용된 내역(데이터 요소의 #)|500|정수|10-2000|시계열에 종속|
|upleveldetector.sensitivity|상향 수준 변화 감지기에 대한 민감도. |3\.25|double|없음|3\.25-5(값이 낮을수록 높은 민감도)|
|bileveldetector.sensitivity|양방향 수준 변화 감지기에 대한 민감도. |3\.25|double|없음|3\.25-5(값이 낮을수록 높은 민감도)|
|trenddetector.sensitivity|긍정적인 추세 감지에 대한 민감도. |3\.25|double|없음|3\.25-5(값이 낮을수록 높은 민감도)|
|tspikedetector.sensitivity |TSpike 감지기의 민감도|3|정수|1-10|3-5(값이 낮을수록 높은 민감도)|
|zspikedetector.sensitivity |ZSpike 감지기의 민감도|3|정수|1-10 |3-5(값이 낮을수록 높은 민감도)|
|seasonality.enable|계절성 분석이 수행될지 여부|true|부울|true, false|시계열에 종속|
|seasonality.numSeasonality |감지할 정기적 주기의 최대 수|1|정수|1, 2|1-2|
|seasonality.transform |이상 감지를 적용하기 전에 계절성 (및) 추세 구성 요소가 제거될지 여부|deseason|열거형|none, deseason, deseasontrend|해당 없음|
|postprocess.tailRows |출력 결과에 유지할 최신 데이터 요소의 수|0|정수|0(모든 데이터 요소 유지), 또는 결과에 유지할 데이터 요소의 수 지정|해당 없음|

###출력
API는 시계열 데이터에서 모든 감지기를 실행하고 각 시점에 대한 이진 급증 표시기와 이상 점수를 반환합니다. 아래 테이블은 API의 결과 목록입니다.

|출력|설명|
|---|---|
|Time|원시 데이터의 타임스탬프 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터|
|OriginalData|원시 데이터의 값 또는 집계 (및/또는) 누락 데이터 대체가 적용되는 경우 집계 (및/또는) 대체된 데이터|
|ProcessedData|다음 중 하나: <ul><li>명확한 계절성이 감지되고 deseason 옵션이 선택된 경우 계절에 따라 조정된 시계열;</li><li> 명확한 계절성이 감지되고 deseasontrend 옵션이 선택된 경우 계절에 따라 조정되고 추세가 제거된 시계열</li><li>기타의 경우, OriginalData와 동일함</li>|
|Tspike|TSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기|
|ZSpike|ZSpike 감지기의 급증 감지 여부를 나타내는 이진 표시기|
|Pscore|상향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자|
|Palert|입력 민감도를 기반으로 상향 수준 변화 이상이 있는지를 나타내는 1/0 값|
|RPScore|양방향 수준 변화에 대한 이상 점수를 나타내는 부동 소수점 숫자|
|RPAlert|입력 민감도를 기반으로 양방향 수준 변화 이상이 있는지를 나타내는 1/0 값|
|TScore|긍정적인 추세에 대한 이상 점수를 나타내는 부동 소수점 숫자|
|TAlert|입력 민감도를 기반으로 긍정적인 추세 이상이 있는지를 나타내는 1/0 값|


이 출력은 [간단한 구문 분석기](https://adresultparser.codeplex.com/)를 사용하여 구문을 분석할 수 있습니다. 여기에는 API에 연결하고 출력의 구문을 분석하는 방법을 보여 주는 샘플 코드가 있습니다. 감지된 이상은 대시보드에 시각화되거나 올바른 조치를 위해 전문가에게 전달되거나 티켓팅 시스템으로 통합될 수 있습니다.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

<!---HONumber=AcomDC_0914_2016-->