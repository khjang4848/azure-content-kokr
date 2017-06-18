<properties
	pageTitle="텍스트 분석 API 버전 2로 업그레이드 | Microsoft Azure"
	description="Azure 기계 학습 텍스트 분석 - 버전 2로 업그레이드"
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

# 텍스트 분석 API 버전 2로 업그레이드 #

이 가이드에서는 [첫 번째 버전의 API](../machine-learning/machine-learning-apps-text-analytics.md)를 사용하는 코드를 두 번째 버전으로 업그레이드하는 프로세스를 안내합니다.

API를 사용해 본 적이 없는 경우 **[여기에서 API에 대해 자세히 알아보거나](//go.microsoft.com/fwlink/?LinkID=759711)** **[빠른 시작 가이드를 따르세요](//go.microsoft.com/fwlink/?LinkID=760860)**. 기술 참조는 **[API 정의](//go.microsoft.com/fwlink/?LinkID=759346)**를 참조하세요.

### 1부. 새 키 가져오기 ###

먼저 **Azure 포털**에서 새 API 키를 가져와야 합니다.

1. [Cortana Intelligence 갤러리](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)를 통해 텍스트 분석 서비스로 이동합니다. 여기에는 설명서 및 코드 샘플에 대한 링크도 있습니다.

1. **등록**을 클릭합니다. 이 링크를 클릭하면 서비스에 등록할 수 있는 Azure 관리 포털로 이동합니다.

1. 요금제를 선택합니다. **free tier for 5,000 transactions/month(무료 계층 - 5,000개 트랜잭션/월)**을 선택할 수 있습니다. 무료 요금제이므로 서비스 사용 요금이 청구되지 않습니다. Azure 구독에 로그인해야 합니다.

1. 텍스트 분석에 등록하면 **API 키**가 제공됩니다. API 서비스를 사용할 때 필요하므로 이 키를 복사해 둡니다.

### 2부. 헤더 업데이트 ###

제출된 헤더 값을 아래와 같이 업데이트합니다. 계정 키는 더 이상 인코딩되지 않습니다.

**버전 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**버전 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### 3부. 기본 URL 업데이트 ###

**버전 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**버전 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### 4a부. 데이터, 키 구 및 언어에 대한 형식 업데이트 ###

#### 끝점 ####

GET 끝점은 이제 사용되지 않으므로 모든 입력을 POST 요청으로 제출해야 합니다. 끝점을 아래와 같은 항목으로 업데이트하세요.

| |버전 1 단일 끝점|버전 1 배치 끝점|버전 2 끝점|
|---|---|---|---|
|호출 유형|GET|POST|POST|
|데이터|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|키 구|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|언어|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### 입력 형식 ####

이제 POST 형식만 허용되므로 이전에 단일 문서 끝점을 사용한 입력의 형식을 다시 지정해야 합니다. 입력은 대/소문자를 구분하지 않습니다.

**버전 1(배치)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**버전 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### 데이터의 출력 ####

**버전 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**버전 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### 키 구의 출력 ####

**버전 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**버전 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### 언어의 출력 ####


**버전 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**버전 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### 4b부. 토픽 형식 업데이트 ###

#### 끝점 ####

| |버전 1 끝점 | 버전 2 끝점|
|---|---|---|
|토픽 검색을 위해 제출(POST)|```StartTopicDetection```|```topics```|
|토픽 결과 가져오기(GET)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### 입력 형식 ####

**버전 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**버전 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### 제출 결과 ####

**버전 1(POST)**

이전에는 작업이 완료된 경우 다음 JSON 출력이 제공되었습니다. 여기서 jobId는 URL에 추가되어 출력을 가져오는 데 사용됩니다.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**버전 2(POST)**

이제 응답에 다음과 같이 헤더 값이 포함됩니다. 여기서 `operation-location`은 결과에 대해 폴링할 끝점으로 사용됩니다.

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### 작업 결과 ####

**버전 1(GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**버전 2(GET)**

이전처럼 출력이 반환될 때까지 **출력을 주기적으로 폴링**(권장 기간은 매분)합니다.

토픽 API가 완료되면 `succeeded`를 나타내는 상태가 반환됩니다. 여기에는 아래와 같은 형식의 출력 결과가 포함됩니다.

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### 5부. 테스트! ###

이제 테스트해 볼 수 있습니다. 작은 샘플로 코드를 테스트하여 데이터를 성공적으로 처리할 수 있는지 확인합니다.

<!---HONumber=AcomDC_0914_2016-->