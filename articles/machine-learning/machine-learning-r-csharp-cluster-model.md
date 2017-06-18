<properties 
	pageTitle="클러스터 모델 | Microsoft Azure" 
	description="클러스터 모델" 
	services="machine-learning" 
	documentationCenter="" 
	authors="FrancescaLazzeri" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="lazzeri"/>


#클러스터 모델    

신용 카드 발급자의 결손 공제 위험을 줄이기 위해 신용 카드 소유자의 행동 그룹을 어떻게 예측할 수 있을까요? 회사에서 직원의 성과를 향상시키기 위해 직원의 성격 특성 그룹을 어떻게 정의할 수 있을까요? 의사는 환자의 질병 특성을 기반으로 하여 환자를 어떻게 그룹으로 분류할 수 있을까요? 원칙적으로 클러스터 분석을 통해 이러한 모든 질문에 대답할 수 있습니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
   
클러스터 분석에서는 변수 조합을 기반으로 하여 관찰 집합을 둘 이상의 상호 배타적인 알 수 없는 그룹으로 분류합니다. 클러스터 분석의 목적은 관찰(일반적으로 사람 또는 해당 특성)을 그룹으로 구성하는 체계를 발견하는 것입니다. 여기서 그룹의 구성원은 속성을 공통적으로 공유합니다. 이 [서비스](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)에서는 일반적으로 사용되는 클러스터링 기법인 K-Means 방법론을 사용하여 임의 데이터를 그룹으로 클러스터합니다. 이 웹 서비스는 데이터 및 k 클러스터 수를 입력으로 사용하여 각 관찰이 속하는 k 그룹에 대한 예측을 생성합니다.

>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.

##웹 서비스 사용   
이 웹 서비스는 데이터를 k 그룹 집합으로 그룹화하고 각 행의 그룹 할당을 출력합니다. 이 웹 서비스는 최종 사용자가 데이터를 행은 쉼표(,)로 구분되고 열은 세미콜론(;)으로 구분된 문자열로 입력할 것으로 예상합니다. 이 웹 서비스는 한 번에 1개의 행을 예상합니다. 예제 데이터 집합은 다음과 같습니다.

![샘플 데이터][1]

사용자가 이 데이터를 상호 배타적인 세 개의 그룹으로 구분하려 한다고 가정합니다. 위 데이터 집합의 데이터 입력은 다음과 같습니다. value = “10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4”; k=”3” 출력은 각 행의 예측된 그룹 멤버 자격입니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx) 참조).

###웹 서비스 사용의 C# 코드 시작:

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

Azure 기계 학습 내에서 새로운 빈 실험이 만들어졌으며 두 개의 [R 스크립트 실행][execute-r-script] 모듈을 작업 영역으로 가져왔습니다. 간단한 [R 스크립트 실행][execute-r-script]을 사용하여 데이터 스키마를 만든 후 데이터 스키마를 클러스터 모델 섹션에 연결하고 다시 [R 스크립트 실행][execute-r-script]을 사용하여 데이터 스키마를 만들었습니다. 그런 다음 클러스터 모델에 사용된 [R 스크립트 실행][execute-r-script]에서 웹 서비스는 Azure 기계 학습의 [R 스크립트 실행][execute-r-script]에 미리 빌드된 “k-means” 함수를 활용합니다.
   

     
![실험 흐름][3]

####모듈 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	
	maml.mapOutputPort("mydata");     
	

####모듈 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##제한 사항
이 예제는 매우 간단한 클러스터링 웹 서비스 예제입니다. 위의 예제 코드에서 알 수 있듯이 오류 catch는 구현되지 않고 서비스에서는 이 웹 서비스를 만들 때 숫자 값만 입력하므로 모든 항목이 연속 변수인 것으로 가정합니다(범주 기능이 허용되지 않음). 또한 웹 서비스가 호출될 때마다 모델이 맞춰진다는 사실과 웹 서비스 호출의 요청/응답 속성 때문에 서비스에서는 현재 제한된 데이터 크기를 처리합니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0914_2016-->