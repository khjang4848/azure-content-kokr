<properties 
	pageTitle="예측: ARIMA(자동 회귀 통합 이동 평균) | Microsoft Azure" 
	description="예측 - ARIMA(자동 회귀 통합 이동 평균)" 
	services="machine-learning" 
	documentationCenter="" 
	authors="yijichen" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="yijichen"/>

 
#예측 - ARIMA(자동 회귀 통합 이동 평균)

이 [서비스](https://datamarket.azure.com/dataset/aml_labs/arima)는 사용자가 제공한 기록 데이터를 기반으로 예측을 생성하는 ARIMA(자동 회귀 통합 이동 평균)를 구현합니다. 올해 특정 제품에 대한 수요가 늘어납니까? 재고를 효과적으로 계획할 수 있도록 크리스마스 시즌의 제품 판매를 예측할 수 있습니까? 예측 모델로 이러한 질문을 해결할 수 있습니다. 과거 데이터가 제공되면 이러한 모델은 숨겨진 추세와 계절성을 조사하여 미래 추세를 예측합니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.

##웹 서비스 사용 

이 서비스는 4개의 인수를 받아들여 ARIMA 예측을 계산합니다. 입력 인수는 다음과 같습니다.

* 빈도 - 원시 데이터(일별/주별/월별/분기별/연도별)의 빈도를 나타냅니다.
* 출시가 임박한 시점 - 미래 예측 시간 프레임입니다.
* 날짜 - 시간의 새 시계열 데이터에 추가합니다.
* 값 - 새 시계열 데이터 값에 추가합니다.

서비스의 출력은 계산된 예측 값입니다.

샘플 입력은 다음과 같을 수 있습니다.

* 빈도 - 12
* 출시가 임박한 시점 - 12
* 날짜 - 1/15/2012, 2/15/2012, 3/15/2012, 4/15/2012, 5/15/2012, 6/15/2012, 7/15/2012, 8/15/2012, 9/15/2012, 10/15/2012, 11/15/2012, 12/15/2012, 1/15/2013, 2/15/2013, 3/15/2013, 4/15/2013, 5/15/2013, 6/15/2013, 7/15/2013, 8/15/2013, 9/15/2013, 10/15/2013, 11/15/2013, 12/15/2013, 1/15/2014, 2/15/2014, 3/15/2014, 4/15/2014, 5/15/2014, 6/15/2014, 7/15/2014, 8/15/2014, 9/15/2014
* 값 - 3.479, 3.68, 3.832, 3.941, 3.797, 3.586, 3.508, 3.731, 3.915, 3.844, 3.634, 3.549, 3.557, 3.785, 3.782, 3.601, 3.544, 3.556, 3.65, 3.709, 3.682, 3.511, 3.429, 3.51, 3.523, 3.525, 3.626, 3.695, 3.711, 3.711, 3.693, 3.571, 3.509
 
>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx) 참조).

###웹 서비스 사용의 C# 코드 시작:

	public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
	void Main()
	{
  		var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
		var json = JsonConvert.SerializeObject(input);
		var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	       
	  	var httpClient = new HttpClient();
	   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
	   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
	  	var result = query.Result.Content;
	  	var scoreResult = result.ReadAsStringAsync().Result;
  	}

##웹 서비스 만들기 

>이 웹 서비스는 Azure 기계 학습을 사용하여 만들었습니다. 무료 평가판 및 실험을 만들고 [웹 서비스를 게시](machine-learning-publish-a-machine-learning-web-service.md)하는 방법에 대한 소개 비디오는 [azure.com/ml](http://azure.com/ml)을 참조하세요. 다음은 웹 서비스를 만든 실험과 실험 내 각 모듈의 예제 코드의 스크린샷입니다.

Azure 기계 학습 내에서 새로운 빈 실험이 만들어졌습니다. 샘플 입력 데이터는 미리 정의된 데이터 스키마로 업로드되었습니다. 데이터 스키마에 연결된 것은 R의 ‘auto.arima’ 및 ‘forecast’ 함수를 사용하여 ARIMA 예측 모델을 생성하는 [R 스크립트 실행][execute-r-script] 모듈입니다.

###실험 흐름:

![작업 영역 만들기][2]

####모듈 1:
 
	# Add in the CSV file with the data in the format shown below 
![작업 영역 만들기][3]

####모듈 2:
	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- auto.arima(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forecasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");


##제한 사항 

이 예제는 매우 간단한 ARIMA 예측 예제입니다. 위의 예제 코드에서 볼 수 있듯이 오류 catch가 구현되어 있지 않으며, 이 서비스에서는 모든 변수가 연속/양수 값이고 빈도가 1보다 큰 정수여야 한다고 가정합니다. 날짜와 값 벡터의 길이는 동일해야 합니다. 날짜 변수는 ‘mm/dd/yyyy’ 형식을 준수해야 합니다.

##FAQ
웹 서비스 사용 또는 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0914_2016-->