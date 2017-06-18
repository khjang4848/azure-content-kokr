<properties
	pageTitle="프로그래밍 방식으로 기계 학습 모델 다시 학습 | Microsoft Azure"
	description="Azure 기계 학습에서 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다."
	services="machine-learning"
	documentationCenter=""
	authors="raymondlaghaeian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="raymondl;garye;v-donglo"/>


#프로그래밍 방식으로 기계 학습 모델 다시 학습  

Azure 기계 학습에서 기계 학습 모델 운영 프로세스의 일부로 모델은 학습 및 저장됩니다. 그런 다음 이를 서술적 웹 서비스를 만드는 데 사용합니다. 그러면 웹 사이트, 대시보드 및 모바일 앱에서 웹 서비스를 사용할 수 있습니다.

기계 학습을 사용하여 만드는 모델은 일반적으로 정적이지 않습니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다. 또는 데이터의 하위 집합을 가져오고 모델을 다시 학습하는 필터를 적용해야 할 수도 있습니다.

재학습은 자주 발생할 수 있습니다. 프로그래밍 방식의 재학습 API 기능을 사용하면 재학습 API를 통해 프로그래밍 방식으로 모델을 다시 학습하고 새로 학습된 모델과 함께 웹 서비스를 업데이트할 수 있습니다.

이 문서에서는 재학습 프로세스를 설명하고 재학습 API를 사용하는 방법을 보여 줍니다.

## 다시 학습 이유: 문제 정의  

ML 학습 프로세스의 일부로, 데이터 집합을 사용하여 모델을 학습합니다. 기계 학습을 사용하여 만드는 모델은 일반적으로 정적이지 않습니다. 새 데이터를 사용할 수 있는 경우 또는 API 소비자가 자체적인 데이터를 가진 경우 모델을 재학습해야 합니다. 추가 시나리오의 경우 데이터의 하위 집합을 가져오고 모델을 다시 학습하는 필터를 적용해야 할 수도 있습니다.

이러한 시나리오에서 개발자나 API 소비자는 프로그래밍 방식의 API를 통해 일회성이나 주기적으로 고유한 데이터를 사용하여 모델을 다시 학습할 수 있는 클라이언트를 만들 수 있습니다. 그런 후에 다시 학습의 결과를 평가하고 새로 학습된 모델을 사용하도록 웹 서비스 API를 업데이트할 수 있습니다.

##다시 학습하는 방법: 종단 간 프로세스  

처음에는 프로세스에 학습 실험 및 웹 서비스로 게시된 서술적 실험이 구성 요소로 포함됩니다. 학습된 모델을 다시 학습할 수 있으려면 학습 실험은 학습된 모델의 출력을 사용하여 웹 서비스로 게시되어야 합니다. 그러면 API가 다시 학습을 위해 모델에 액세스할 수 있습니다.

다시 학습을 설정하는 프로세스에는 다음 단계가 포함됩니다.

![재학습 프로세스 개요][1]

다이어그램 1: 다시 학습 프로세스 개요

## 학습 실험 만들기
 
이 예의 경우 Microsoft Azure 기계 학습 샘플의 "샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합"을 사용합니다.
	
실험을 만들려면:

1.	Microsoft Azure 기계 학습 스튜디오에 로그인합니다.
2.	대시보드의 오른쪽 아래 모서리에서 **새로 만들기**를 클릭합니다.
3.	Microsoft 샘플에서 샘플 5를 선택합니다.
4.	실험의 이름을 바꾸려면 실험 캔버스의 위쪽에서 실험 이름 "샘플 5: 이진 분류에 대한 학습, 테스트, 평가: 성인 데이터 집합"을 선택합니다.
5.	Census Model을 입력합니다.
6.	실험 캔버스 맨 아래에서 **실행**을 클릭합니다.
7.	**웹 서비스 설정**을 클릭하고 **재학습 웹 서비스**를 선택합니다.

 	![초기 실험.][2]

다이어그램 2: 초기 실험.

## 점수 매기기 실험을 만들고 웹 서비스로 게시  

다음으로 서술적 실험을 만듭니다.

1.	실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **예측 웹 서비스**를 선택합니다. 모델을 학습된 모델로 저장한 다음 웹 서비스 입력 및 출력 모듈을 추가합니다.
2.	**실행**을 클릭합니다.
3.	실험 실행이 종료된 후 **웹 서비스 배포[기존]**를 클릭합니다. 서술적 실험을 기존 웹 서비스로 배포합니다.

## 학습 실험을 학습 웹 서비스로 배포

학습된 모델을 다시 학습하려면 재학습 웹 서비스로 만든 학습 실험을 배포해야 합니다. 이 웹 서비스가 학습된 모델을 새로 생성할 수 있으려면 웹 서비스 출력 모듈이 [학습 모델][train-model] 모듈에 연결되어야 합니다.

1. 학습 실험으로 돌아가려면 왼쪽 창에서 실험 아이콘을 클릭한 다음 Census Model이라는 실험을 클릭합니다.
2. 실험 항목 검색 검색 상자에 웹 서비스를 입력합니다.
3. 웹 서비스 입력 모듈을 실험 캔버스로 끌어 놓고 해당 출력을 누락된 데이터 정리 모듈에 연결합니다.
4. 두 *웹 서비스 출력* 모듈을 실험 캔버스로 끌어 놓습니다. *학습 모델* 모듈의 출력을 하나에 연결하고 *평가 모델* 모듈의 출력을 다른 하나에 연결합니다. 학습 모델에 대한 웹 서비스 출력에서 새로 학습된 모델을 제공합니다. 평가 모델에 연결된 출력은 해당 모듈의 평가 모델 출력을 반환합니다.
5. **실행**을 클릭합니다.
6. 실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭하고 **재학습 웹 서비스**를 선택합니다. 학습된 모델 및 모델 평가 결과를 생성하는 웹 서비스로 학습 실험을 배포합니다. 웹 서비스 **대시보드**가 배치 실행을 위한 API 키 및 API 도움말 페이지와 함께 표시됩니다. 학습된 모델을 만들 때는 배치 실행 방법만 사용할 수 있습니다.
  
실험 실행이 완료된 후 결과 워크플로는 다음과 같이 표시됩니다.

![실행 후 결과 워크플로.][4]

다이어그램 3: 실행 후 결과 워크플로.

## 새 끝점 추가
 
배포한 예측 웹 서비스는 기본 점수 매기기 끝점입니다. 기본 끝점은 원래 학습 및 점수 매기기 실험과 동기화 상태를 유지하므로 기본 끝점에 대한 학습된 모델을 바꿀 수 없습니다.

학습된 모델과 함께 업데이트될 수 있는 예측 웹 서비스에서 새 점수 매기기 끝점을 만들려면:

>[AZURE.NOTE] 끝점을 학습 웹 서비스가 아닌 예측 웹 서비스에 추가해야 합니다. 학습 및 예측 웹 서비스를 모두 올바르게 배포한 경우 나열된 두 개의 별도 웹 서비스가 표시됩니다. 예측 웹 서비스는 "[예측 exp.]"로 끝나야 합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **기계 학습**을 클릭합니다.
3. 이름 아래에서 작업 영역을 클릭한 다음 **웹 서비스**를 클릭합니다.
4. 이름 아래에서 **Census Model[예측 exp.]**을 클릭합니다.
5. 페이지 맨 아래에 있는 **끝점 추가**를 클릭합니다. 끝점 추가에 대한 자세한 내용은 [끝점 만들기](machine-learning-create-endpoint.md)를 참조하세요.

이 [GitHub 리포지토리](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs)에서 제공된 샘플 코드를 사용하여 점수 매기기 끝점을 추가할 수도 있습니다.

## BES를 사용하여 새 데이터로 모델 다시 학습

재학습 API를 호출하려면:

1. Visual Studio(New->Project->Windows Desktop->Console Application)에서 C# 콘솔 응용 프로그램을 만듭니다.
2. 배치 실행을 위한 학습 웹 서비스 API 도움말 페이지에서 샘플 C# 코드를 복사한 다음 네임스페이스를 그대로 유지하여 Program.cs 파일에 붙여 넣습니다.
3. 샘플 코드에는 업데이트해야 하는 코드 부분을 나타내는 주석이 있습니다.
4. 요청 페이로드에서 출력 위치를 지정할 때 *RelativeLocation*에서 지정된 파일의 확장명을 csv에서 ilearner로 변경해야 합니다. 다음 예제를 참조하세요.

		Outputs = new Dictionary<string, AzureBlobDataReference>()
		{
			{
				"output1",
				new AzureBlobDataReference()
				{
					ConnectionString = "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==",
					RelativeLocation = "mycontainer/output1results.ilearner"
				}
			},
		},

>[AZURE.NOTE] 출력 위치 이름은 웹 서비스 출력 모듈을 추가한 순서에 따라 이 연습의 출력 위치 이름과 다를 수 있습니다. 이 학습 실험을 두 개의 출력으로 설정했으므로 결과는 둘 모두에 대한 저장소 위치 정보를 포함합니다.

### Azure 저장소 정보 업데이트

BES 샘플 코드는 로컬 드라이브에서(예: "C:\\temp\\CensusIpnput.csv") Azure 저장소로 파일을 업로드하고 이를 처리하고 결과를 Azure 저장소에 다시 작성합니다.

이 작업을 수행하려면 Azure 클래식 포털에서 저장소 계정에 대한 저장소 계정 이름, 키 및 컨테이너 정보를 검색한 다음 코드에서 해당 값을 업데이트해야 합니다.

또한 코드에 지정한 위치에서 입력 파일을 사용할 수 있는지 확인해야 합니다.

![재학습 출력][6]

다이어그램 4: 재학습 출력.

## 재학습 결과 평가
 
응용 프로그램을 실행할 때 출력은 평가 결과를 액세스하는 데 필요한 URL 및 SAS 토큰을 포함합니다.

*output2*에 대한 출력 결과의 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 조합하고 브라우저 주소 표시줄에 전체 URL을 붙여 넣어 다시 학습된 모델의 성능 결과를 확인할 수 있습니다.

새로 학습된 모델이 기존 모델을 대체할 만큼 성능이 뛰어난지 여부를 확인하도록 결과를 검사합니다.

## 추가된 끝점의 학습된 모델 업데이트

재학습 프로세스를 완료하려면 추가한 새 끝점의 학습된 모델을 업데이트해야 합니다.

* Azure 포털을 사용하여 새 끝점을 추가한 경우 Azure 포털에서 새 끝점의 이름을 클릭한 다음 **UpdateResource** 링크를 클릭하여 끝점의 모델을 업데이트하는 데 필요한 URL을 가져올 수 있습니다.
* 샘플 코드를 사용하여 끝점을 추가한 경우 출력에서 *HelpLocationURL* 값으로 식별되는 도움말 URL의 위치를 포함합니다.

경로 URL을 검색하려면:

1. URL을 복사하여 브라우저에 붙여 넣습니다.
2. 업데이트 리소스 링크를 클릭합니다.
3. PATCH 요청의 POST URL을 복사합니다. 예:

		PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

이제 학습된 모델을 사용하여 이전에 만든 점수 매기기 끝점을 업데이트할 수 있습니다.

다음 샘플 코드는 *BaseLocation*, *RelativeLocation*, *SasBlobToken* 및 PATCH URL을 사용하여 끝점을 업데이트하는 방법을 보여 줍니다.

	private async Task OverwriteModel()
	{
		var resourceLocations = new
		{
			Resources = new[]
			{
				new
				{
					Name = "Census Model [trained model]",
					Location = new AzureBlobDataReference()
					{
						BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
						RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
						SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
					}
				}
			}
		};

		using (var client = new HttpClient())
		{
			client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

			using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
			{
				request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
				HttpResponseMessage response = await client.SendAsync(request);

				if (!response.IsSuccessStatusCode)
				{
					await WriteFailedResponse(response);
				}

				// Do what you want with a successful response here.
			}
		}
	}

호출에 대한 *apiKey* 및 *endpointUrl*은 끝점 대시보드에서 가져올 수 있습니다.

*리소스*의 *Name* 매개 변수의 값은 예측 실험의 저장된 학습된 모델의 리소스 이름과 일치해야 합니다. 리소스 이름을 가져오려면:

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **기계 학습**을 클릭합니다.
3. 이름 아래에서 작업 영역을 클릭한 다음 **웹 서비스**를 클릭합니다.
4. 이름 아래에서 **Census Model[예측 exp.]**을 클릭합니다.
5. 추가한 새 끝점을 클릭합니다.
6. 끝점 대시보드에서 *업데이트 리소스*를 클릭합니다.
7. 웹 서비스에 대한 업데이트 리소스 API 설명서 페이지에서 **업데이트할 수 있는 리소스** 아래의 **리소스 이름**을 찾을 수 있습니다.

끝점 업데이트를 완료하기 전에 SAS 토큰이 만료되는 경우 작업 ID로 GET을 수행하여 새 토큰을 가져와야 합니다.

코드가 성공적으로 실행된 경우 새 끝점은 다시 학습된 모델을 사용하여 약 30초 내에 시작해야 합니다.

##요약  
재학습 API를 사용하여 다음과 같은 시나리오를 활성화하는 예측 웹 서비스의 학습된 모델을 업데이트할 수 있습니다.

* 새 데이터를 사용하는 주기적 모델 재학습.
* 자신의 데이터를 사용하여 모델을 다시 학습할 수 있도록 하는 것을 목표로 고객에게 모델 배포.

## 다음 단계
[Azure 기계 학습 기존 웹 서비스의 재학습 문제 해결](machine-learning-troubleshooting-retraining-models.md)

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->