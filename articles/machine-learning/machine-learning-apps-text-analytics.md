<properties
	pageTitle="기계 학습 API: 텍스트 분석 | Microsoft Azure"
	description="Microsoft의 기계 학습 텍스트 분석 API를 사용하여 정서 분석, 핵심 문구 추출, 언어 검색 및 토픽 검색에 대해 구조화되지 않은 텍스트를 분석할 수 있습니다."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="onewth"/>


# 기계 학습 API: 정서, 핵심 구문 추출, 언어 검색 및 토픽 검색을 위한 텍스트 분석

>[AZURE.NOTE] 이 가이드는 API의 버전 1용입니다. 버전 2의 경우 [**이 문서를 참조**](../cognitive-services/cognitive-services-text-analytics-quick-start.md)하세요. 현재 버전 2가 기본 설정된 API 버전입니다.

## 개요

텍스트 분석 API는 Azure 기계 학습을 사용하여 빌드한 텍스트 분석 [웹 서비스](https://datamarket.azure.com/dataset/amla/text-analytics) 제품군입니다. 이 API를 사용하여 정서 분석, 핵심 문구 추출, 언어 검색 및 토픽 검색과 같은 작업에 대한 구조화되지 않은 텍스트를 분석할 수 있습니다. 학습 데이터 없이 이 API를 사용할 수 있으며, 텍스트 데이터를 가져오기만 하면 됩니다. 이 API는 고급 자연어 처리 기술을 사용하여 클래스 예측을 가장 잘 전달합니다.

[데모 사이트](https://text-analytics-demo.azurewebsites.net/)에서 작업에 대한 텍스트 분석을 볼 수 있으며 이 사이트에는 C# 및 Python에서 텍스트 분석을 구현하는 방법에 대한 [샘플](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)도 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## 정서 분석

이 API는 0에서 1 사이의 숫자 점수를 반환합니다. 1에 가까운 점수는 긍정적인 정서를 나타내고, 0에 가까운 점수는 부정적인 정서를 나타냅니다. 정서 점수는 분류 기술을 사용하여 생성됩니다. 분류자의 입력 기능에는 N-그램, 음성 부분 태그에서 생성된 기능 및 단어 포함이 포함됩니다. 현재 지원되는 언어는 영어뿐입니다.
 
## 핵심 문구 추출

이 API는 입력 텍스트의 핵심 요지를 나타내는 문자열 목록을 반환합니다. Microsoft Office의 정교한 자연어 처리 도구 키트에서 제공되는 기술을 사용합니다. 현재 지원되는 언어는 영어뿐입니다.

## 언어 검색

API는 검색된 언어 및 0에서 1 사이의 숫자 점수를 반환합니다. 점수가 1에 가까울수록 식별된 언어가 true라는 100% 확실성을 나타냅니다. 총 120개의 언어가 지원됩니다.

## 토픽 검색

새로 발표된 API이며 제출된 텍스트 레코드 목록에 대해 검색된 상위 토픽을 반환합니다. 토픽은 핵심 문구로 식별되며 하나 이상의 관련 단어를 가질 수 있습니다. 이 API는 제출되는 텍스트 레코드 수가 100개 이상 필요하지만 수백 개에서 수천 개의 레코드에서 토픽을 검색할 수 있습니다. 이 API는 제출된 텍스트 레코드당 하나의 트랜잭션을 사용합니다. 이 API는 리뷰와 사용자 피드백 등 짧고 직접 작성한 텍스트 사용 시 더 효과적입니다.

---

## API 정의

### 헤더

다음과 같이 요청에 올바른 헤더가 포함되었는지 확인합니다.

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

[Azure 데이터 마켓](https://datamarket.azure.com/account/keys)에서 계정의 계정 키를 찾을 수 있습니다. 현재는 JSON의 경우에만 입력 및 출력 형식이 허용됩니다. XML은 지원되지 않습니다.

---

## 단일 응답 API

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**예제 요청**

아래 호출에서는 "Hello World"라는 문구에 대한 정서 분석을 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

그러면 다음과 같이 응답을 반환합니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**예제 요청**

아래 호출에서는 "It was a wonderful hotel to stay at, with unique decor and friendly staff" 텍스트에 있는 핵심 문구를 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

그러면 다음과 같이 응답을 반환합니다.

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**예제 요청**

아래 호출에서는 *Hello World* 텍스트의 핵심 문구에 대한 정서를 요청합니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

그러면 다음과 같이 응답을 반환합니다.

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**선택적 매개 변수**

`NumberOfLanguagesToDetect`는 선택적 매개 변수입니다. 기본값은 1입니다.

---

## 배치 API

텍스트 분석 서비스를 통해 배치 모드에서 정서 및 키 구문 추출 작업을 수행할 수 있습니다. 점수가 매겨진 각 레코드는 하나의 트랜잭션으로 계산됩니다. 예를 들어 단일 호출에서 1000개의 레코드에 대한 정서를 요청하면 1000개의 트랜잭션이 공제됩니다.

시스템에 입력한 ID가 시스템에서 반환한 ID입니다. 웹 서비스는 이러한 ID가 고유한지 검사하지 않습니다. 호출자가 고유성을 확인해야 합니다.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**예제 요청**

아래 POST 호출에서는 요청 본문에 있는 "Hello World", "Hello Foo World", "Hello My World" 문구에 대한 정서를 요청합니다.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

본문 요청:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
	    {"Id":"2","Text":"hello foo world"},
	    {"Id":"3","Text":"hello my world"},
	]}

아래 응답에서 텍스트 ID와 연결된 점수 목록을 확인할 수 있습니다.

	{
	  "odata.metadata":"<url>", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**예제 요청**

이 예제에서는 다음 텍스트의 핵심 문구에 대한 정서 목록을 요청합니다.

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

다음 요청은 끝점에 대한 POST 호출로 생성되었습니다.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

본문 요청:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

아래 응답에서 텍스트 ID와 연결된 핵심 문구 목록을 확인할 수 있습니다.

	{ "odata.metadata":"<url>",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

아래의 POST 호출에서는 두 텍스트 입력에 대한 언어 검색을 요청하고 있습니다.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

본문 요청:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

그러면 다음 응답이 반환되는데 첫 번째 입력에서 영어가 검색되고 두 번째 입력에서 프랑스어가 검색됩니다.

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## 토픽 검색 API

새로 발표된 API이며 제출된 텍스트 레코드 목록에 대해 검색된 상위 토픽을 반환합니다. 토픽은 핵심 문구로 식별되며 하나 이상의 관련 단어를 가질 수 있습니다. 이 API는 제출된 텍스트 레코드당 하나의 트랜잭션을 사용합니다.

이 API는 제출되는 텍스트 레코드 수가 100개 이상 필요하지만 수백 개에서 수천 개의 레코드에서 토픽을 검색할 수 있습니다.


### 토픽 - 작업 제출

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**예제 요청**


아래 POST 호출에서는 100개 문서 집합에 대해 토픽을 요청하여 첫 번째 및 마지막 입력 문서를 표시하고 두 개의 StopPhrases를 포함합니다.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

본문 요청:

	{"Inputs":[
		{"Id":"1","Text":"I loved the food at this restaurant"},
		...,
		{"Id":"100","Text":"I hated the decor"}
	],
	"StopPhrases":[
		"restaurant", “visitor"
	]}

아래 응답에서 제출된 작업에 대한 JobId를 가져옵니다.

	{
		"odata.metadata":"<url>",
		"JobId":"<JobId>"
	}

토픽으로 반환할 수 없는 한 단어 또는 여러 단어 문구의 목록입니다. 매우 일반적인 토픽을 필터링하는 데 사용할 수 있습니다. 예를 들어 호텔 리뷰에 대한 데이터 집합에서 "호텔" 및 "호스텔"은 합리적인 중지 문구가 될 수 있습니다.

### 토픽 - 작업 결과에 대한 설문 조사

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**예제 요청**

'작업 제출' 단계에서 반환된 JobId를 전달하여 결과를 가져옵니다. 응답에 Status='Complete'가 표시될 때까지 1분마다 이 끝점을 호출하는 것이 좋습니다. 한 작업을 완료하는 데 약 10분의 시간이 소요되며 레코드 수가 수천 개인 작업의 경우 더 오랜 시간이 걸립니다.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


처리하는 동안 다음과 같은 응답이 표시됩니다.

	{
		"odata.metadata":"<url>",
		"Status":"Running",
 		"TopicInfo":[],
		"TopicAssignment":[],
		"Errors":[]
	}


API는 다음과 같은 형식의 JSON 형식으로 출력을 반환합니다.

	{
		"odata.metadata":"<url>",
		"Status":"Finished",
		"TopicInfo":[
		{
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Score":8.0,
			"KeyPhrase":"food"
		},
		...
		{
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Score":6.0,
			"KeyPhrase":"decor"
    		}
  		],
		"TopicAssignment":[
		{
			"Id":"1",
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Distance":0.7809
		},
		...
		{
			"Id":"100",
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Distance":0.8034
		}
		],
		"Errors":[]


응답의 각 부분에 대한 속성은 다음과 같습니다.

**TopicInfo 속성**

| 키 | 설명 |
|:-----|:----|
| TopicId | 각 토픽에 대한 고유 식별자입니다. |
| Score | 토픽에 할당된 레코드 개수입니다. |
| KeyPhrase | 토픽에 대한 요약 단어 또는 구입니다. 한 단어 또는 여러 단어가 될 수 있습니다. |

**TopicAssignment 속성**

| 키 | 설명 |
|:-----|:----|
| Id | 레코드에 대한 식별자입니다. 입력에 포함된 ID와 같습니다. |
| TopicId | 레코드가 할당된 토픽 ID입니다. |
| Distance | 레코드가 토픽에 속할 신뢰도입니다. Distance가 0에 가까울수록 신뢰도가 높아집니다. |

<!---HONumber=AcomDC_0914_2016-->