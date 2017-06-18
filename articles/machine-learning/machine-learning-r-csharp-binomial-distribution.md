<properties 
	pageTitle="이항 분포 패키지 | Microsoft Azure" 
	description="이항 분포 패키지" 
	services="machine-learning" 
	documentationCenter="" 
	authors="ireiter" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2016" 
	ms.author="ireiter"/>


#이항 분포 패키지




이항 분포 패키지는 이항 분포를 생성하고 처리하는 데 도움이 되는 일련의 샘플 웹 서비스([이항 생성기](https://datamarket.azure.com/dataset/aml_labs/bdg5), [확률 계산기](https://datamarket.azure.com/dataset/aml_labs/bdp4), [변위치 계산기](https://datamarket.azure.com/dataset/aml_labs/bdq5))입니다. 이 서비스를 통해 임의 길이의 이항 분포 시퀀스를 생성하고 지정된 확률에서 변위치를 계산하며 지정된 변위치에서 확률을 계산할 수 있습니다. 각 서비스는 선택된 서비스를 기반으로 서로 다른 출력을 내보냅니다(아래 설명 참조). 이항 분포 패키지는 R 통계 패키지에 포함된 R 함수 qbinom, rbinom 및 pbinom을 기반으로 합니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>사용자는 이 웹 서비스를 마켓플레이스에서 직접 사용하거나, 모바일 앱이나 웹 사이트를 통해 사용하거나, 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.

##웹 서비스 사용
이항 분포 패키지에는 다음과 같은 3가지 서비스가 포함되어 있습니다.

###이항 분포 변위치 계산기
이 서비스는 정규 분포의 인수 4개를 받아들여 연관된 변위치를 계산합니다. 입력 인수는 다음과 같습니다.

- p - 여러 시도의 단일 집계 확률
- size – 시도 횟수
- prob – 시도의 성공 확률
- Side – L: 분포의 아래쪽, U: 분포의 위쪽

이 서비스에서는 제공된 확률과 연결된 계산된 분위수가 결과 값으로 표시됩니다.

###이항 분포 확률 계산기
이 서비스는 이항 분포의 인수 4개를 받아들여 연관된 확률을 계산합니다. 입력 인수는 다음과 같습니다.

- q – 이항 분포에서 이벤트의 단일 변위치
- size – 시도 횟수
- prob – 시도의 성공 확률
- side – L: 분포의 아래쪽, U: 분포의 위쪽 또는 E: 성공의 단일 횟수와 같음

이 서비스에서는 제공된 분위수와 연결된 계산된 확률이 결과 값으로 표시됩니다.

###이항 분포 생성기
이 서비스는 이항 분포의 인수 3개를 받아들여 이항 분포된 숫자의 임의 시퀀스를 생성합니다. 요청 내에서 다음과 같은 인수가 서비스에 제공되어야 합니다.

- n - 관찰 수
- size – 시도 횟수
- prob – 성공 확률

이 서비스의 출력은 이항 분포에서 size 및 prob 인수를 기반으로 한 길이 n의 시퀀스입니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

서비스를 자동화된 방식으로 사용하는 방법은 여러 가지가 있습니다(예제 앱은 [생성기](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [확률 계산기](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [변위치 계산기](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)에 제공됨).

###웹 서비스 사용의 C# 코드 시작:

###이항 분포 변위치 계산기
	public class Input
	{
	        public string p;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
    void main()
	{
	        var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###이항 분포 확률 계산기
	public class Input
	{
	        public string q;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


###이항 분포 생성기
	public class Input
	{
	        public string n;
	        public string size;
	        public string p;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

###이항 분포 변위치 계산기

![작업 영역 만들기][4]

####모듈 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####모듈 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###이항 분포 확률 계산기

![작업 영역 만들기][5]

####모듈 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####모듈 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###이항 분포 생성기

![작업 영역 만들기][6]

####모듈 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####모듈 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##제한 사항 
이러한 예제는 매우 간단한 이항 분포 예제입니다. 위의 예제 코드에서 볼 수 있듯이 오류 catch가 거의 구현되어 있지 않습니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 

<!---HONumber=AcomDC_0914_2016-->