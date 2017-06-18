<properties 
	pageTitle="이진 분류자 | Microsoft Azure" 
	description="이진 분류자" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2016" 
	ms.author="jaymathe"/>



#이진 분류자

데이터 집합이 있고 독립 변수를 기반으로 이진 종속 변수를 예측하려 한다고 가정합니다. '로지스틱 회귀 분석'은 이러한 예측에 널리 사용되는 통계 기법입니다. 여기서 종속 변수는 이진 또는 이분법적이고 p는 관심을 가지는 특성의 존재 확률입니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

연구원이 정보(고등학교 평점, 가족 수입, 주거 상태, 성별)를 기반으로 하여 잠재 학생이 대학 입학 허가를 받아들일지 여부를 예측하려고 하는 간단한 시나리오가 있을 수 있습니다. 예측된 결과는 잠재 학생이 입학 허가를 받아들일 확률입니다. 이 [웹 서비스](https://datamarket.azure.com/dataset/aml_labs/log_regression)는 로지스틱 회귀 분석 모델을 데이터에 맞춤화하여 데이터의 각 관찰에 대한 확률 값(y)을 출력합니다.
  
>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.
  

##웹 서비스 사용  
이 웹 서비스는 모든 관찰에 대한 독립 변수에 따라 종속 변수의 예측 값을 제공합니다. 웹 서비스에서는 최종 사용자가 데이터를 행이 쉼표(,)로 구분되고 열이 세미콜론(;)으로 구분된 문자열로 입력할 것으로 예상합니다. 웹 서비스에서는 한 번에 하나의 행이 입력되고 첫 열은 종속 변수일 것으로 예상합니다. 예제 데이터 집합은 다음과 같습니다.

![샘플 데이터][1]

종속 변수가 없는 관찰은 y에 대해 “NA”로 입력됩니다. 위 데이터 집합에 대한 데이터 입력은 다음과 같은 문자열입니다. “1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4”. 출력은 독립 변수에 따른 각 행에 대한 예측 값입니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx) 참조).

###웹 서비스 사용의 C# 코드 시작:

	public class Input
	{
   		public string value;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
		byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
		return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
		var input = new Input() { value = TextBox1.Text };
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

Azure 기계 학습 내에서 새로운 빈 실험이 만들어졌으며 두 개의 [R 스크립트 실행][execute-r-script] 모듈을 작업 영역으로 가져왔습니다. 이 웹 서비스에서는 기본 R 스크립트를 사용하여 Azure 기계 학습 실험을 실행합니다. 이 실험은 스키마 정의 및 모델 학습 및 점수 매기기의 두 부분으로 구성됩니다. 첫 번째 모듈에서는 첫 번째 변수가 종속 변수이고 나머지 변수가 독립 변수인 입력 데이터 집합의 예상 구조를 정의합니다. 두 번째 모듈에서는 일반 로지스틱 회귀 분석 모델을 입력 데이터에 맞춤화합니다.

![실험 흐름][2]

####모듈 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) 
	maml.mapOutputPort("data");  

####모듈 2:
	#GLM modeling   
	data <- maml.mapInputPort(1) # class: data.frame  
	
	data.split <- strsplit(data[1,1], ",")[[1]] 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	
	model <- glm(data.split$V1 ~., family='binomial', data=data.split)  
	out <- data.frame(predict(model,data.split, type="response")) 
	pred1 <- as.data.frame(out) 
	group <- array(1:nrow(pred1)) 
	for (i in 1:nrow(pred1))  
		{
		if(as.numeric(pred1[i,])>0.5) {group[i]=1} 
		else {group[i]=0}
		} 
	pred2 <- as.data.frame(group) 
	maml.mapOutputPort("pred2");  


##제한 사항
이 예제는 매우 간단한 이진 분류 웹 서비스 예제입니다. 위의 예제 코드에서 볼 수 있듯이 오류 catch가 구현되어 있지 않으며, 이 서비스에서는 이 웹 서비스를 만들 때 숫자 값만 입력하므로 모든 항목이 이진/연속 변수(범주 기능은 허용되지 않음)라고 가정합니다. 또한 웹 서비스가 호출될 때마다 모델이 맞춰진다는 사실과 웹 서비스 호출의 요청/응답 속성 때문에 서비스에서는 현재 제한된 데이터 크기를 처리합니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->