<properties 
	pageTitle="스트림 분석에서 Azure 기계 학습 끝점을 구성하는 방법 | Microsoft Azure" 
	description="스트림 분석의 기계 언어 사용자 정의 함수"
	keywords=""
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
	ms.author="jeffstok"
/>

# 스트림 분석의 기계 학습 통합

스트림 분석은 Azure 기계 학습 끝점을 호출하는 사용자 정의 함수를 지원합니다. 이 기능에 대한 REST API 지원은 [스트림 분석 REST API 라이브러리](https://msdn.microsoft.com/library/azure/dn835031.aspx)에 자세히 설명되어 있습니다. 이 문서에서는 스트림 분석에서 이 기능을 성공적으로 구현하기 위해 필요한 추가 정보를 제공합니다. 자습서도 게시되어 있으며 [여기](stream-analytics-machine-learning-integration-tutorial.md)서 확인할 수 있습니다.

## 개요: Azure 기계 학습 용어

Microsoft Azure 기계 학습은 데이터에 대한 예측 분석 솔루션을 빌드, 테스트, 배포할 수 있는 공동 끌어서 놓기 도구입니다. 이 도구를 *Azure 기계 학습 스튜디오*라고 부릅니다. 이 스튜디오는 기계 학습 리소스와 상호 작용하고 설계를 간편하게 빌드, 테스트 및 반복하는 데 사용됩니다. 이러한 리소스 및 해당 정의는 다음과 같습니다.

- **작업 영역**: *작업 영역*은 관리 및 제어를 위해 다른 모든 기계 학습 리소스를 함께 보관하는 컨테이너입니다.
- **실험**: 데이터 과학자가 데이터 집합을 활용하고 기계 학습 모델을 교육하기 위해 *실험*을 만듭니다.
- **끝점**: *끝점*은 입력으로 기능을 가져오고, 지정된 기계 학습 모델을 적용하고, 점수가 매겨진 출력을 반환하는 데 사용되는 Azure 기계 학습 개체입니다.
- **점수 매기기 웹 서비스**: *점수 매기기 웹 서비스*는 위에서 언급한 대로 끝점 컬렉션입니다.

각 끝점에는 배치 실행 및 동기 실행을 위한 API가 있습니다. 스트림 분석은 동기 실행을 사용합니다. 특정 서비스는 AzureML 스튜디오에 있는 [Request/Response Service](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs)라는 서비스입니다.

## 스트림 분석 작업에 필요한 기계 학습 리소스

스트림 분석 작업을 처리하려면 요청/응답 끝점, [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key) 및 swagger 정의가 모두 있어야 성공적으로 실행됩니다. 스트림 분석에는 swagger 끝점에 대한 url을 생성하고, 인터페이스를 조회하고, 사용자에게 기본 UDF 정의를 반환하는 추가 끝점이 있습니다.

## REST API를 통해 스트림 분석 및 기계 학습 UDF 구성

REST API를 사용하여 Azure 기계 언어 함수를 호출하는 작업을 구성할 수 있습니다. 단계는 다음과 같습니다.

1. 스트림 분석 작업 만들기
2. 입력 정의
3. 출력 정의
4. UDF(사용자 정의 함수) 만들기
5. UDF를 호출하는 스트림 분석 변환 작성
6. 작업 시작

## 기본 속성을 사용하여 UDF 만들기

예를 들어 다음 샘플 코드는 Azure 기계 학습 끝점에 바인딩되는 *newudf*라고 하는 스칼라 UDF를 만듭니다. *끝점*(서비스 URI)은 선택한 서비스에 대한 API 도움말 페이지에서 찾을 수 있고 *apiKey*는 서비스 기본 페이지에서 찾을 수 있습니다.

PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>

요청 본문 예제:

	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
						"apiKey": "replacekeyhere"
					}
				}
			}
		}
	}

## 기본 UDF에 대한 RetrieveDefaultDefinition 끝점 호출

기초 UDF를 만든 후에는 완전한 UDF 정의가 필요합니다. RetreiveDefaultDefinition 끝점은 Azure 기계 학습 끝점에 바인딩된 스칼라 함수의 기본 정의를 가져오는 데 도움이 됩니다. 아래 페이로드는 Azure 기계 학습 끝점에 바인딩된 스칼라 함수의 기본 UDF 정의를 필요로 합니다. PUT 요청 동안 이미 끝점이 제공되었기 때문에 실제 끝점을 지정하지 않습니다. 끝점이 명시적으로 제공되면 스트림 분석은 요청에 제공된 끝점을 호출합니다. 그렇지 않으면 원래 참조하던 끝점을 사용합니다. 다음 UDF는 단일 문자열 매개 변수(문장)를 가져와서 해당 문장에 대한 “sentiment” 레이블의 단일 문자열 형식을 반환합니다.

POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>

요청 본문 예제:

	{
		"bindingType": "Microsoft.MachineLearning/WebService",
		"bindingRetrievalProperties": {
			"executeEndpoint": null,
			"udfType": "Scalar"
		}
	}

이 샘플 출력은 아래와 비슷하게 표시됩니다.


	{
		"name": "newudf",
		"properties": {
			"type": "Scalar",
			"properties": {
				"inputs": [{
					"dataType": "nvarchar(max)",
					"isConfigurationParameter": null
				}],
				"output": {
					"dataType": "nvarchar(max)"
				},
				"binding": {
					"type": "Microsoft.MachineLearning/WebService",
					"properties": {
						"endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
						"apiKey": null,
						"inputs": {
							"name": "input1",
							"columnNames": [{
								"name": "tweet",
								"dataType": "string",
								"mapTo": 0
							}]
						},
						"outputs": [{
							"name": "Sentiment",
							"dataType": "string"
						}],
						"batchSize": 10
					}
				}
			}
		}
	}

## 응답과 함께 UDF 패치 

이제 아래와 같이 이전 응답과 함께 UDF를 패치해야 합니다.

PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>

요청 본문: RetrieveDefaultDefinition의 출력

## UDF를 호출하는 스트림 분석 변환 구현

이제 모든 입력 이벤트에 대해 UDF(여기서는 scoreTweet)를 쿼리하고 해당 이벤트에 대한 응답을 출력으로 작성해야 합니다.

	{
		"name": "transformation",
		"properties": {
			"streamingUnits": null,
			"query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
		}
	}

자세한 내용은 다음을 참조하세요.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->