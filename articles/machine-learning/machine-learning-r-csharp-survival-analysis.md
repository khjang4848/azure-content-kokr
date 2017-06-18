<properties 
	pageTitle="Azure 기계 학습을 사용한 생존 분석 | Microsoft Azure" 
	description="생존 분석 이벤트 발생 확률" 
	services="machine-learning" 
	documentationCenter="" 
	authors="zhangya" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="zhangya"/>


#생존 분석 

대부분 시나리오에서 평가되는 주요 결과는 관련 이벤트에 대한 시간입니다. 즉, "이 이벤트가 언제 발생할까요?"라고 질문합니다. 예제로서 데이터가 관련 이벤트(질병 재발, 박사 학위 취득, 브레이크 패드 고장)가 발생할 때까지 경과된 시간(일, 년, 마일리지 등)을 설명하는 상황을 고려해 보세요. 데이터의 각 인스턴스는 특정 개체(환자, 학생, 자동차 등)를 나타냅니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

이 [웹 서비스](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)에서는 "x 개체에 대해 n 시간까지 관련 이벤트가 발생할 확률은 얼마인가요?"라는 질문에 답합니다. 생존 분석 모델을 제공함으로써 이 웹 서비스에서는 사용자가 모델을 학습하고 테스트하기 위한 데이터를 제공할 수 있도록 합니다. 실험의 주요 테마는 관련 이벤트가 발생할 때까지 경과된 시간의 길이를 모델링하는 것입니다.

>사용자는 이 웹 서비스를 모바일 앱이나 웹 사이트를 통해 사용하거나 로컬 컴퓨터에서도 사용할 수 있습니다. 하지만 이 웹 서비스는 Azure 기계 학습을 사용하여 R 코드 기반의 웹 서비스를 만드는 방법을 보여 주는 예로 제공되기도 합니다. Azure 기계 학습 스튜디오 내에서 R 코드 몇 줄을 포함하고 단추를 몇 번 클릭하면 R 코드를 사용하여 실험을 만들고 웹 서비스로 게시할 수 있습니다. 그런 다음 웹 서비스를 Azure 마켓플레이스에 게시하면 웹 서비스 작성자가 인프라를 설정하지 않고도 전 세계의 사용자와 장치에서 이러한 웹 서비스를 사용할 수 있습니다.

##웹 서비스 사용

웹 서비스의 입력 데이터가 다음 표에 표시됩니다. 학습 데이터, 테스트 데이터, 관련 시간, "시간" 차원 인덱스, "이벤트" 차원 인덱스 및 변수 유형(연속 또는 요소)의 6가지 정보가 입력으로 필요합니다. 학습 데이터는 행이 쉼표로 구분되고 열이 세미콜론으로 구분되는 문자열로 표시됩니다. 데이터 기능 수는 유연합니다. 입력 문자열의 모든 요소는 숫자여야 합니다. 학습 데이터에서 "시간" 차원은 연구 시작점(환자가 약물 치료 프로그램을 받음, 학생이 박사 과정을 시작함, 자동차가 운행을 시작함 등) 이후 관련 이벤트(환자가 약물을 사용함, 학생이 박사 학위를 받음, 자동차의 브레이크 패드가 고장 남 등)가 발생할 때까지 경과된 시간 단위(일, 년, 마일리지 등) 수를 나타냅니다. "이벤트" 차원은 연구 종료 시 관련 이벤트가 발생하는지를 나타냅니다. "이벤트=1" 값은 관련 이벤트가 "시간" 차원으로 지정된 시간에 발생함을 의미하지만 "이벤트=0"은 관련 이벤트가 "시간" 차원으로 지정된 시간에 발생하지 않았음을 의미합니다.

- trainingdata - 문자열입니다. 행은 쉼표로 구분되고 열은 세미콜론으로 구분됩니다. 각 행에는 "시간" 차원, "이벤트" 차원 및 예측 요인 변수가 포함됩니다.
- testingdata - 특정 개체에 대한 예측 요인 변수를 포함하는 데이터의 한 행입니다.
- time\_of\_interest - 경과된 관련 시간 n입니다.
- index\_time - "시간" 차원의 열 인덱스(1부터 시작)입니다.
- index\_event - "이벤트" 차원의 열 인덱스(1부터 시작)입니다.
- variable\_types - 세미콜론을 구분 기호로 사용하는 문자열입니다. 0은 연속 변수를 나타내고 1은 요소 변수를 나타냅니다.


출력은 특정 시간까지 발생하는 이벤트의 확률입니다.

>Azure 마켓플레이스에서 호스트되는 이 서비스는 OData 서비스이고 POST 또는 GET 메서드를 통해 호출할 수 있습니다.

다양한 자동화된 방식으로 서비스를 사용할 수 있습니다(예제 앱은 [여기](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx) 참조).

###웹 서비스 사용의 C# 코드 시작:
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




이 테스트의 해석은 다음과 같습니다. 데이터의 목적이 두 가지 치료 프로그램의 하나를 받은 환자가 약물을 다시 사용할 때까지 경과된 시간을 모델링하는 것이라고 가정합니다. 웹 서비스의 출력은 다음과 같이 해석됩니다. 이전에 약물 치료를 두 번 받고, 긴 거주 치료 프로그램을 받고, 헤로인과 코카인을 둘 다 사용한 35세 환자의 경우 약물을 다시 사용할 확률은 500일차까지 95.64%입니다.

##웹 서비스 만들기

>이 웹 서비스는 Azure 기계 학습을 사용하여 만들었습니다. 무료 평가판 및 실험을 만들고 [웹 서비스를 게시](machine-learning-publish-a-machine-learning-web-service.md)하는 방법에 대한 소개 비디오는 [azure.com/ml](http://azure.com/ml)을 참조하세요. 다음은 웹 서비스를 만든 실험과 실험 내 각 모듈의 예제 코드의 스크린샷입니다.

Azure 기계 학습 내에서 새로운 빈 실험이 만들어졌으며 두 개의 [R 스크립트 실행][execute-r-script] 모듈을 작업 영역으로 가져왔습니다. 데이터 스키마는 웹 서비스에 대한 입력 데이터 스키마를 정의하는 간단한 [R 스크립트 실행][execute-r-script]을 사용하여 만들어졌습니다. 이 모듈은 주요 작업을 수행하는 두 번째 [R 스크립트 실행][execute-r-script] 모듈에 연결됩니다. 이 모듈은 데이터 전처리, 모델 작성 및 예측을 수행합니다. 데이터 전처리 단계에서 긴 문자열로 표시되는 입력 데이터는 데이터 프레임으로 변형 및 변환됩니다. 모델 작성 단계에서 외부 R 패키지 "survival\_2.37-7.zip"이 생존 분석을 수행하기 위해 먼저 설치됩니다. 그리고 나서 "coxph" 함수가 계열 데이터 처리 작업 후에 실행됩니다. 생존 분석에 대한 "coxph" 함수에 대한 자세한 내용은 R 설명서를 참조하세요. 예측 단계에서 테스트 인스턴스가 "surfit" 함수를 통해 학습된 모델에 제공되고 이 테스트 인스턴스에 대한 생존 곡선이 "curve" 변수로 생성됩니다. 마지막으로 관련 시간의 확률을 얻습니다.

###실험 흐름:

![실험 흐름][1]

####모듈 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####모듈 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##제한 사항

이 웹 서비스는 기능 변수(열)로 숫자 값만 사용할 수 있습니다. "이벤트" 열에는 0 또는 1 값만 사용할 수 있습니다. "시간" 열은 양의 정수여야 합니다.

##FAQ
웹 서비스 사용 또는 Azure 마켓플레이스 게시 방법과 관련한 질문과 대답은 [여기](machine-learning-marketplace-faq.md)를 참조하세요.

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0921_2016-->