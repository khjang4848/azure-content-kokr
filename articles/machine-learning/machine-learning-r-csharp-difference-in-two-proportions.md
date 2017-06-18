<properties 
	pageTitle="비율 차이 테스트 | Microsoft Azure" 
	description="비율 차이 테스트" 
	services="machine-learning" 
	documentationCenter="" 
	authors="aniedea" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="aniedea"/>


#비율 차이 테스트


두 비율이 통계적으로 다른가요? 사용자가 두 개의 동영상을 비교하여 한 동영상의 '좋아요' 비율이 다른 동영상에 비해 상당히 더 높은지 확인하려 한다고 가정합니다. 큰 샘플을 사용할 경우 비율 0.50과 0.51 간에는 통계적으로 상당한 차이가 있을 수 있습니다. 작은 샘플을 사용할 경우 이러한 비율이 실제로 다른지를 확인하기 위한 데이터가 충분하지 않을 수 있습니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

이 [웹 서비스](https://datamarket.azure.com/dataset/aml_labs/prop_test)는 두 비교 그룹에 대한 성공 횟수와 총 시도 횟수의 사용자 입력을 기반으로 하여 두 비율 간의 차이에 대한 가설 테스트를 수행합니다. 가능한 한 가지 시나리오에서, 동영상 비교 앱 내에서 이 웹 서비스를 호출하여 사용자에게 동영상 등급을 기반으로 하여 동영상 중 하나가 다른 동영상보다 실제로 '좋아요'를 더 자주 받았는지 여부를 알려줄 수 있습니다.

>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.


##웹 서비스 사용

이 서비스는 4개의 인수를 받아들여 비율의 가설 테스트를 수행합니다.

입력 인수는 다음과 같습니다.

* Successes1 - 샘플 1의 성공 이벤트 수
* Successes2 - 샘플 2의 성공 이벤트 수
* Total1 - 샘플 1의 크기
* Total2 - 샘플 2의 크기

이 서비스의 출력은 가설 테스트를 카이제곱 통계, df, p 값, 샘플-1/2의 비율 및 신뢰 구간 경계와 함께 사용한 결과입니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx) 참조).

###웹 서비스 사용의 C# 코드 시작:

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


##웹 서비스 만들기

>이 웹 서비스는 Azure 기계 학습을 사용하여 만들었습니다. 무료 평가판 및 실험을 만들고 [웹 서비스를 게시](machine-learning-publish-a-machine-learning-web-service.md)하는 방법에 대한 소개 비디오는 [azure.com/ml](http://azure.com/ml)을 참조하세요. 다음은 웹 서비스를 만든 실험과 실험 내 각 모듈의 예제 코드의 스크린샷입니다.

Azure 기계 학습 내에서 두 개의 [R 스크립트 실행][execute-r-script] 모듈을 사용하여 새로운 빈 실험을 만들었습니다. 첫 번째 모듈에서는 데이터 스키마가 정의된 반면, 두 번째 모듈에서는 R 내에 prop.test 명령을 사용하여 두 비율에 대한 가설 테스트를 수행합니다.


###실험 흐름:

![실험 흐름][2]


####모듈 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####모듈 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##제한 사항 

이 예제는 두 비율 간의 차이를 테스트하는 매우 간단한 예제입니다. 위의 예제 코드에서 볼 수 있듯이 오류 catch가 구현되어 있지 않으며, 이 서비스에서는 모든 변수가 연속 변수라고 가정합니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->