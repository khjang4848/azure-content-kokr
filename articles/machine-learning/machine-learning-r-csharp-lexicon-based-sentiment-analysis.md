<properties 
	pageTitle="어휘집 기반 감정 분석 | Microsoft Azure" 
	description="어휘집 기반 감정 분석" 
	services="machine-learning" 
	documentationCenter="" 
	authors="pengxia" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2016" 
	ms.author="pengxia"/>



#어휘집 기반 감정 분석 

온라인 소셜 네트워크(예: Facebook 게시물, 트윗, 리뷰 등)에서 브랜드나 주제에 대한 사용자의 의견과 태도를 어떻게 측정할 수 있을까요? 감정 분석을 통해 이러한 질문을 분석할 수 있습니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

감정 분석 방법으로는 일반적으로 두 가지 방법이 있습니다. 하나는 감독되는 학습 알고리즘을 사용하는 것이고 다른 하나는 자율 학습으로 간주할 수 있습니다. 감독되는 학습 알고리즘은 일반적으로 주석이 지정된 큰 모음을 기반으로 분류 모델을 작성합니다. 그 정확도는 주로 주석의 품질을 기반으로 하며, 대개 학습 프로세스에 걸리는 시간이 깁니다. 그 외에도 알고리즘을 다른 도메인에 적용할 때 결과가 대개 좋지 않습니다. 감독되는 학습과 비교하여, 어휘집 기반 자율 학습은 큰 데이터 모음 저장과 학습이 불필요한 감정 사전을 사용하여 전체 프로세스가 훨씬 더 빠릅니다.

이 [서비스](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis)는 가장 일반적으로 사용되는 주관성 어휘집 중 하나인 MPQA 주관성 어휘집(http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/)(영문)을 기반으로 작성되었습니다. MPQA에는 5097개의 부정적인 감정과 2533개의 긍정적인 감정이 있습니다. 또한 이러한 모든 단어에는 강하거나 약한 극성으로 주석이 지정되어 있습니다. 전체 모음은 GNU General Public License의 적용을 받습니다. 이 웹 서비스는 트윗 및 Facebook 게시물과 같은 짧은 문장에 적용될 수 있습니다.

>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.

##웹 서비스 사용

입력 데이터는 임의 텍스트일 수 있지만 이 웹 서비스는 짧은 문장과 더 잘 작동합니다. 출력은 -1과 1 사이의 숫자 값입니다. 0보다 작은 모든 값은 텍스트의 감정이 부정적임을 나타내고, 0보다 큰 값은 텍스트의 감정이 긍정적임을 나타냅니다. 결과의 절대값은 연관된 감정의 강도를 나타냅니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/) 참조).

###웹 서비스 사용의 C# 코드 시작:

	public class ScoreResult
	{
	        [DataMember]
	        public double result
	        {
	            get;
	            set;
	        }
	}

	void main()
	{
	        using (var wb = new WebClient())
	        {
	            var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
	            DataServiceContext ctx = new DataServiceContext(acitionUri);
	            var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
	            var cache = new CredentialCache();
	
	            cache.Add(acitionUri, "Basic", cred);
	            ctx.Credentials = cache;
	            var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
	            ScoreResult scoreResult = query.ElementAt(0);
	            double result = scoreResult.result;
	    	}
	}



입력이 "Today is a good day.(오늘은 좋은 날입니다.)"인 경우 출력은 긍정적인 감정이 입력 문장과 연관되었음을 나타내는 "1"입니다.

##웹 서비스 만들기
>이 웹 서비스는 Azure 기계 학습을 사용하여 만들었습니다. 무료 평가판 및 실험을 만들고 [웹 서비스를 게시](machine-learning-publish-a-machine-learning-web-service.md)하는 방법에 대한 소개 비디오는 [azure.com/ml](http://azure.com/ml)을 참조하세요. 다음은 웹 서비스를 만든 실험과 실험 내 각 모듈의 예제 코드의 스크린샷입니다.


Azure 기계 학습 내에서 새로운 빈 실험이 만들어졌습니다. 아래의 그림은 어휘집 기반 감정 분석의 실험 흐름을 보여 줍니다. "sent\_dict.csv" 파일은 MPQA 주관성 어휘집이며 [R 스크립트 실행][execute-r-script]의 입력 중 하나로 설정되었습니다. 다른 입력은 테스트를 위해 Amazon 리뷰 데이터 집합에서 샘플링된 리뷰입니다. 여기서는 선택, 열 이름 수정, 분할 작업을 수행했습니다. 해시 패키지를 사용하여 메모리에 주관성 어휘집을 저장하고 점수 계산 프로세스를 가속화합니다. 전체 텍스트는 "tm" 패키지에 의해 토큰화되고 감정 사전에 있는 단어와 비교됩니다. 마지막으로, 텍스트에 있는 주관적인 각 단어의 가중치를 더하여 점수가 계산됩니다.

###실험 흐름:

![실험 흐름][2]


####모듈 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # Install hash package install.packages("src/hash\_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##제한 사항

알고리즘 관점에서 봤을 때 어휘집 기반 감정 분석은 특정 필드에 대해 분류 방법보다 성능이 좋지 않을 수도 있는 일반 감정 분석 도구입니다. 부정 문제가 잘 처리되지 않습니다. 여기서는 프로그램에 몇 가지 부정 단어를 하드 코드하지만, 더 좋은 방법은 부정 사전을 사용하고 몇 가지 규칙을 작성하는 것입니다. 이 웹 서비스는 Amazon 리뷰와 같은 길고 복잡한 문장보다 트윗 및 Facebook 게시물과 같은 짧고 간단한 문장에 대해 더 잘 수행됩니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

<!---HONumber=AcomDC_0921_2016-->