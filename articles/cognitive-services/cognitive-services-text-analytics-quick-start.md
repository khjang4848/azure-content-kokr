<properties
	pageTitle="빠른 시작 가이드: 기계 학습 텍스트 분석 API | Microsoft Azure"
	description="Azure 기계 학습 텍스트 분석 - 빠른 시작 가이드"
	services="cognitive-services"
	documentationCenter=""
	authors="onewth"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="onewth"/>

# 감성, 핵심 문구, 토픽 및 언어를 검색하는 텍스트 분석 API 시작

<a name="HOLTop"></a>

이 문서에서는 [텍스트 분석 API](//go.microsoft.com/fwlink/?LinkID=759711)를 사용하도록 서비스나 응용 프로그램을 등록하는 방법에 대해 설명합니다. 이러한 API를 사용하여 텍스트에서 감성, 핵심 문구, 토픽 및 언어를 검색할 수 있습니다. [대화형 데모 환경을 보려면 여기를 클릭합니다.](//go.microsoft.com/fwlink/?LinkID=759712)

API에 대한 기술 설명서는 [API 정의](//go.microsoft.com/fwlink/?LinkID=759346)를 참조하세요.

이 가이드는 API의 버전 2용입니다. API의 버전 1에 대한 자세한 내용은 [이 문서를 참조](../machine-learning/machine-learning-apps-text-analytics.md)하세요.

이 자습서를 마치면 프로그래밍 방식으로 검색할 수 있습니다.

- **감성** - 감성이 긍정적인지 부정적인지 여부

- **핵심 문구** - 한 문서에서 논의되는 사항

- **토픽** - 여러 문서에 걸쳐 논의되는 사항은 무엇입니까?

- **언어** - 텍스트가 작성된 언어

이 API는 제출된 문서당 하나의 트랜잭션을 사용합니다. 예를 들어 단일 호출에서 1000개의 문서에 대한 정서를 요청하면 1000개의 트랜잭션이 공제됩니다.



<a name="Overview"></a>
## 일반 개요 ##

이 문서는 단계별 가이드입니다. 모델을 학습하는 데 필요한 단계를 안내하고 프로덕션에 적용할 수 있는 리소스를 제공하는 것이 목적입니다. 이 연습에는 30분 정도가 소요됩니다.

이러한 작업에는 편집기가 필요하고 선택한 언어로 RESTful 끝점을 호출합니다.

이제 시작하겠습니다.

## 작업 1 - 텍스트 분석 API에 등록 ####

이 작업에서는 텍스트 분석 서비스에 등록합니다.

1. [Azure 포털](//go.microsoft.com/fwlink/?LinkId=761108)의 **Cognitive Services**로 이동하여 **텍스트 분석**이 'API 형식'으로 선택되어 있는지 확인합니다.

1. 요금제를 선택합니다. **free tier for 5,000 transactions/month(무료 계층 - 5,000개 트랜잭션/월)**을 선택할 수 있습니다. 무료 요금제이므로 서비스 사용 요금이 청구되지 않습니다. Azure 구독에 로그인해야 합니다.

1. 기타 필드를 완료하고 계정을 만듭니다.

1. 텍스트 분석에 등록한 후 **API 키**를 찾습니다. API 서비스를 사용할 때 필요하므로 기본 키를 복사합니다.


## 작업 2 - 감성, 핵심 문구 및 언어 검색 ####

텍스트에서 감성, 핵심 문구 및 언어를 검색하는 것은 쉽습니다. [데모 환경](//go.microsoft.com/fwlink/?LinkID=759712)이 반환하는 것과 동일한 결과를 프로그래밍 방식으로 얻게 됩니다.

>[AZURE.TIP] 감성 분석의 경우 텍스트를 문장으로 분할하는 것이 좋습니다. 이렇게 하면 일반적으로 감성 예측의 정밀도가 더 높아집니다.

지원되는 언어는 다음과 같습니다.

| 기능 | 지원되는 언어 코드 |
|:-----|:----|
| 데이터 | `en`(영어), `es`(스페인어), `fr`(프랑스어), `pt`(포르투갈어) |
| 키 구 | `en`(영어), `es`(스페인어), `de`(독일어), `ja`(일본어) |


1. 다음과 같이 헤더를 설정해야 합니다. 현재는 JSON의 경우에만 API에 대한 입력 형식이 허용됩니다. XML은 지원되지 않습니다.

		Ocp-Apim-Subscription-Key: <your API key>
		Content-Type: application/json
		Accept: application/json

1. 다음으로 JSON에서 입력 행 형식을 지정합니다. 감성, 핵심 문구 및 언어의 경우 형식은 같습니다. 각 ID는 고유해야 하며 시스템에서 반환됩니다. 제출할 수 있는 단일 문서의 최대 크기는 10KB이고 제출된 입력의 총 최대 크기는 1MB입니다. 한 번 호출에 최대 1,000개의 문서를 제출할 수 있습니다. 속도 제한은 분당 100회 호출의 속도로 존재 - 따라서 단일 호출에서 대량의 문서를 제출할 것을 권장합니다. 영어가 아닌 텍스트를 분석하는 경우 언어는 지정해야 하는 선택적 매개 변수입니다. 입력의 예는 아래에서 보여주며 여기서 감정 분석 및 핵심 구문 추출에 대한 선택적 매개 변수 `language`이 포함되어 있습니다.

		{
			"documents": [
				{
					"language": "en",
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"language": "en",
					"id": "100",
					"text": "Final document"
				}
			]
		}

1. 감정, 핵심 문구 및 언어의 입력을 사용하여 시스템에 대한 **POST** 호출을 수행합니다. URL은 다음과 같습니다.

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. 이 호출은 ID 및 검색된 속성과 함께 JSON 형식의 응답을 반환합니다. 다음은 감성에 대한 출력 예입니다(오류 세부 정보 제외). 감성의 경우 각 문서에 대해 0과 1 사이의 점수가 반환됩니다.

        // Sentiment response
		{
		  	"documents": [
				{
					"id": "1",
					"score": "0.934"
		        },
                ...
                {
					"id": "100",
					"score": "0.002"
		        },
			]
		}

        // Key phrases response
        {
		  	"documents": [
				{
					"id": "1",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
                ...
                {
					"id": "100",
					"keyPhrases": ["key phrase 1", ..., "key phrase n"]
		        },
			]
		}

        // Languages response
        {
		  	"documents": [
				{
					"id": "1",
					"detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
		        },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
		        }
			]
		}


## 작업 3 - 텍스트 모음에 있는 토픽 검색 ####

새로 발표된 API이며 제출된 텍스트 레코드 목록에 대해 검색된 상위 토픽을 반환합니다. 토픽은 핵심 문구로 식별되며 하나 이상의 관련 단어를 가질 수 있습니다. 이 API는 리뷰와 사용자 피드백 등 짧고 직접 작성한 텍스트 사용 시 더 효과적입니다.

이 API는 제출되는 **텍스트 레코드 수가 100개 이상** 필요하지만 수백 개에서 수천 개의 레코드에서 토픽을 검색할 수 있습니다. 영어 이외의 레코드 또는 3개 단어보다 적은 레코드는 삭제되므로 토픽에 할당되지 않습니다. 토픽 검색의 경우 제출할 수 있는 단일 문서의 최대 크기는 30KB이고 제출된 입력의 총 최대 크기는 30MB입니다. 토픽 검색은 매 5분마다 5회 제출로 속도가 제한됩니다.

결과의 품질을 향상시키는 데 도움이 될 수 있는 두 가지 추가 **선택적** 입력 매개 변수가 있습니다.

- **중지 단어.** 이러한 단어와 해당 유사 형식(예: 복수형)은 전체 토픽 검색 파이프라인에서 제외됩니다. 일반적인 단어의 경우 이 옵션을 사용합니다.예를 들어 “issue”, “error” 및 “user”는 소프트웨어에 대한 고객 불만 사항에 대한 적절한 선택일 수 있습니다. 각 문자열에는 단어 한 개가 있어야 합니다.
- **중지 문구** - 이러한 문구는 반환된 토픽 목록에서 제외됩니다. 이 옵션을 사용하여 결과에 표시하지 않으려는 일반적 토픽을 제외합니다. 예를 들어, "Microsoft" 및 "Azure"는 제외할 토픽에 대한 적합한 선택입니다. 문자열에는 여러 개의 단어가 포함될 수 있습니다.

텍스트에서 토픽을 검색하려면 다음 단계를 수행합니다.

1. Json에서 입력 형식을 지정합니다. 이때 중지 단어 및 중지 문구를 정의할 수 있습니다.

		{
			"documents": [
				{
					"id": "1",
					"text": "First document"
				},
                ...
                {
					"id": "100",
					"text": "Final document"
				}
			],
			"stopWords": [
				"issue", "error", "user"
			],
			"stopPhrases": [
				"Microsoft", "Azure"
			]
		}

1. 작업 2에서 정의한 대로 동일한 헤더를 사용하여 토픽 끝점에 대한 **POST** 호출을 수행합니다.

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. 이렇게 하면 응답에서 헤더로 `operation-location`을 반환하며, 여기서 값은 결과 토픽에 대해 쿼리하는 URL입니다.

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. **GET** 요청을 사용하여 반환된 `operation-location`을 주기적으로 쿼리합니다. 1분에 한 번이 좋습니다.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. 끝점은 처리 전에는 `{"status": "notstarted"}`, 처리 중에는 `{"status": "running"}` 및 완료되면 출력과 함께 `{"status": "succeeded"}`를 포함한 응답을 반환합니다. 다음 형식으로 출력을 사용할 수 있습니다(오류 형식 및 날짜와 같은 세부 정보는 이 예제에서 제외됨).

		{
			"status": "succeeded",
			"operationProcessingResult": {
			  	"topics": [
                    {
					    "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
					    "score": "5"
					    "keyPhrase": "first topic name"
                    },
                    ...
                    {
					    "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
					    "score": "3"
					    "keyPhrase": "final topic name"
                    }
                ],
			  	"topicAssignments": [
                    {
					    "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
					    "documentId": "1",
					    "distance": "0.354"
                    },
                    ...
                    {
					    "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
					    "documentId": "55",
					    "distance": "0.758"
                    },            
                ]
			}
		}

`operations` 끝점의 토픽에 대한 성공적인 응답에는 다음 스키마가 포함됩니다.

	{
    		"topics" : [{
        		"id" : "string",
        		"score" : "number",
        		"keyPhrase" : "string"
    		}],
    		"topicAssignments" : [{
        		"documentId" : "string",
        		"topicId" : "string",
        		"distance" : "number"
    		}],
    		"errors" : [{
        		"id" : "string",
        		"message" : "string"
    		}]
    	}

응답의 각 부분에 대한 설명은 다음과 같습니다.

**topics**

| 키 | 설명 |
|:-----|:----|
| id | 각 토픽에 대한 고유 식별자입니다. |
| score에 대한 임계값 수준 보기 | 토픽에 할당된 문서의 수입니다. |
| keyPhrase | 토픽에 대한 요약 단어 또는 구입니다. |

**topicAssignments**

| 키 | 설명 |
|:-----|:----|
| documentId | 문서에 대한 식별자입니다. 입력에 포함된 ID와 같습니다. |
| topicId | 문서가 할당된 토픽 ID입니다. |
| distance | 문서에서 토픽까지의 연계 점수(0과 1 사이)입니다. distance 점수가 낮을수록 토픽 연계가 강합니다. |

**errors**

| 키 | 설명 |
|:-----|:----|
| id | 오류에서 참조하는 입력 문서 고유 식별자입니다. |
| message | 오류 메시지입니다. |

## 다음 단계 ##

축하합니다. 데이터에 대한 텍스트 분석 사용을 완료했습니다. 이제 데이터를 시각화하고 통찰력을 자동화하여 텍스트 데이터의 실시간 보기를 제공하는 [Power BI](//powerbi.microsoft.com)와 같은 도구 사용에 대해 살펴보고 싶을 수 있습니다.

감정과 같은 텍스트 분석 기능을 봇의 일부로 사용할 수 있는 방법을 확인하려면 봇 프레임워크 사이트에서 [감정적 봇](http://docs.botframework.com/ko-KR/bot-intelligence/language/#example-emotional-bot) 예제를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->