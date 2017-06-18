<properties
	pageTitle="Azure 스트림 분석 및 Azure 기계 학습을 사용한 정서 분석 | Microsoft Azure"
	description="스트림 분석 작업에서 사용자 정의 함수 및 기계 학습을 사용하는 방법"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>


<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/08/2016" 
	ms.author="jeffstok"
/>

# Azure 스트림 분석 및 Azure 기계 학습을 사용한 정서 분석 #

이 문서는 간단한 Azure 스트림 분석 작업을 Azure 기계 학습과 신속하게 통합하는 방법을 알려주기 위해 설계되었습니다. 여기서는 Cortana Intelligence 갤러리의 정서 분석 기계 학습 모델을 사용하여 스트리밍 텍스트 데이터를 분석하고 정서 점수를 실시간으로 확인할 것입니다. 이 문서의 정보는 스트리밍 Twitter 데이터의 실시간 정서 분석, 고객 지원 담당자와 고객 대화 기록 분석, 포럼, 블로그, 비디오에 대한 코멘트, 기타 여러 실시간 예측 점수 매기기 등의 시나리오를 이해하는 데 많은 도움이 될 수 있습니다.

이 문서는 다음 이미지에 표시된 Azure Blob 저장소에 대한 입력으로 텍스트를 포함하는 샘플 CSV 파일을 제공합니다. 이 작업에서 정서 분석 모델을 Blob 저장소의 샘플 텍스트에 UDF(사용자 정의 함수)으로 적용할 것입니다. 최종 결과는 같은 Blob 저장소에 또 다른 CSV 파일로 배치됩니다.

![스트림 분석 기계 학습](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

다음 이미지는 이러한 구성을 보여 줍니다. 좀 더 현실적인 시나리오에서는 Blob 저장소를 Azure 이벤트 허브 입력의 스트리밍 Twitter 데이터로 바꿀 수 있습니다. 또한 종합 정서의 [Microsoft Power BI](https://powerbi.microsoft.com/) 실시간 시각화를 빌드할 수도 있습니다.

![스트림 분석 기계 학습](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## 필수 조건

이 문서에서 설명하는 작업을 완료하기 위한 필수 구성 요소는 다음과 같습니다.

-	활성 Azure 구독.
-	데이터가 포함된 CSV 파일. [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv)에서 그림 1에 표시된 파일을 다운로드하거나 파일을 직접 만들 수 있습니다. 이 문서에서는 GitHub에서 다운로드할 수 있게 제공된 파일을 사용한다고 가정합니다.

좀 더 높은 수준에서 이 문서에 설명된 작업을 완료하려면 다음을 수행합니다.

1.	CSV 입력 파일을 Azure Blob 저장소에 업로드합니다.
2.	Cortana Intelligence 갤러리의 정서 분석 모델을 Azure 기계 학습 작업 영역에 추가합니다.
3.	이 모델을 기계 학습 작업 영역 내부의 웹 서비스로 배포합니다.
4.	텍스트 입력의 정서를 결정하는 함수로 이 웹 서비스를 호출하는 스트림 분석 작업을 만듭니다.
5.	스트림 분석 작업을 시작하고 출력을 관찰합니다.

## Blob 저장소에 CSV 입력 파일을 업로드합니다.

이 단계의 경우 GitHub에서 다운로드할 수 있도록 이미 지정된 파일을 비롯하여 어떤 CSV 파일도 사용할 수 있습니다. [Azure 저장소 탐색기](http://storageexplorer.com/) 또는 Visual Studio를 사용하여 파일을 업로드하거나 사용자 지정 코드를 사용할 수 있습니다. Visual Studio에 따라 예제를 사용합니다.

1.	Visual Studio에서 **Azure** > **저장소** > **외부 저장소 연결**을 클릭합니다. **계정 이름** 및 **계정 키**를 입력합니다.

    ![스트림 분석 기계 학습, 서버 탐색기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	1단계에서 연결한 저장소를 확장하고 **Blob 컨테이너 만들기**를 선택한 다음 논리적 이름을 입력합니다. 컨테이너를 만든 후 열어 해당 내용을 확인합니다. (지금은 비어 있습니다.)

    ![스트림 분석 기계 학습, Blob 만들기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	CSV 파일을 업로드하려면 **Blob 업로드**를 클릭하고 **로컬 디스크의 파일**을 클릭합니다.

## Cortana Intelligence Gallery의 정서 분석 모델 추가

1.	Cortana Intelligence Gallery에서 [예측 정서 분석 모델](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)을 다운로드합니다.
2.	기계 학습 스튜디오에서 **스튜디오에서 열기**를 클릭합니다.

    ![스트림 분석 기계 학습, 기계 학습 스튜디오 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	로그인하여 작업 영역으로 이동합니다. 현재 위치에 가장 적합한 위치를 선택합니다.
4.	페이지 아래쪽에서 **실행**을 클릭합니다.
5.	프로세스가 성공적으로 실행되면 **웹 서비스 배포**를 클릭합니다.
6.	정서 분석 모델을 사용할 준비가 완료되었습니다. 유효성을 검사하려면 **테스트** 버튼을 클릭하고 "I love Microsoft"와 같은 텍스트 입력을 제공합니다. 그러면 다음과 같은 결과가 반환됩니다.

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![스트림 분석 기계 학습, 분석 데이터](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

**앱** 열에서 **Excel 2010 또는 이전 통합 문서** 링크를 클릭하여 API 키 및 URL을 가져옵니다. 나중에 스트림 분석 작업을 설정할 때 필요합니다. (이 단계는 또 다른 Azure 계정 작업 영역에서 기계 학습 모델을 사용하려는 경우에만 필요합니다. 이 문서에서는 해당 시나리오를 해결하는 것으로 가정합니다.)

아래와 같이 다운로드한 Excel 파일의 웹 서비스 URL과 선택키를 기록해 둡니다.

![스트림 분석 기계 학습, 간략 상태](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## 기계 학습 모델을 사용하는 스트림 분석 작업 만들기

1.	[Azure 포털](https://manage.windowsazure.com)로 이동합니다.
2.	**새로 만들기** > **데이터 서비스** > **스트림 분석** > **빠른 생성**을 클릭합니다. **작업 이름**에 작업에 대한 이름하고 **지역**에서 작업에 대한 해당 지역을 입력한 후 **지역별 모니터링 저장소 계정**에서 해당 계정을 선택합니다.
3.	작업이 생성되면 **입력** 탭에서 **입력 추가**를 클릭합니다.

    ![스트림 분석 기계 학습, 기계 학습 입력 추가](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	**입력 추가** 마법사 창의 첫 번째 페이지에서 **데이터 스트림**을 클릭하고 **다음** 다음 페이지에서 **Blob 저장소**를 입력으로 클릭하고 **다음**을 클릭합니다.
5.	마법사의 **Blob 저장소 설정** 페이지에서, 앞에서 데이터를 업로드할 때 정의한 저장소 계정 Blob 컨테이너 이름을 입력합니다. **다음**을 클릭합니다. **이벤트 직렬화 형식**으로 **CSV**를 클릭합니다. **직렬화 설정** 페이지의 나머지 부분에서는 기본값을 그대로 적용합니다. **확인**을 클릭합니다.
6.	**출력** 탭에서 **출력 추가**를 클릭합니다.

    ![스트림 분석 기계 학습, 출력 추가](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	**Blob 저장소**를 클릭한 후 컨테이너를 제외하고 동일한 매개 변수를 입력합니다. **입력** 값은 **CSV** 파일이 업로드된 "test"라는 이름의 컨테이너에서 읽도록 구성되었습니다. **출력**으로 "testoutput"을 입력합니다. 컨테이너 이름은 달라야 합니다. 이 컨테이너가 있는지 확인합니다.
8.	**다음**을 클릭하여 출력의 **직렬화 설정**을 구성합니다. **입력**과 마찬가지로 **CSV**를 클릭하고 **확인** 버튼을 클릭합니다.
9.	**함수** 탭에서 **기계 학습 함수 추가**를 클릭합니다.

    ![스트림 분석 기계 학습, 기계 학습 함수 추가](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	**기계 학습 웹 서비스 설정** 페이지에서 기계 학습 작업 영역, 웹 서비스 및 기본 끝점을 찾습니다. 이 문서에서는 여러분이 URL 및 API 키를 알고 있고 키를 갖고 있는 작업 영역에 익숙해지도록 수동으로 설정을 적용하겠습니다. 끝점 **URL** 및 **API 키**를 입력합니다. **확인**을 클릭합니다.

    ![스트림 분석 기계 학습, 기계 학습 웹 서비스](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	**쿼리** 탭에서 다음과 같이 쿼리를 수정합니다.

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Scored Labels]  
 	Into output  
 	From subquery  
 ```    
12.	**저장**을 클릭하여 쿼리를 저장합니다.

## 스트림 분석 작업을 시작하고 출력 관찰

1.	작업 페이지 아래쪽에서 **시작**을 클릭합니다.
2.	**쿼리 시작 대화 상자**에서 **사용자 지정 시간**을 클릭한 다음 Blob 저장소에 CSV가 업로드되기까지의 시간을 선택합니다. **확인**을 클릭합니다.

    ![스트림 분석 기계 학습, 사용자 지정 시간](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	예를 들어 CSV 파일을 업로드하는 데 사용했던 도구(예: Visual Studio)를 사용하여 Blob 저장소로 이동합니다.
4.	작업 시작 후 몇 분이 지나면 출력 컨테이너 만들어지고 그 컨테이너에 CSV 파일이 업로드됩니다.
5.	기본 CSV 편집기에서 파일을 엽니다. 다음과 유사한 항목이 표시됩니다.

    ![스트림 분석 기계 학습, CSV 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## 결론

이 문서에서는 스트리밍 텍스트 데이터를 읽고 정서 분석을 실시간으로 데이터에 적용하는 스트림 분석 작업을 만드는 방법을 보여 줍니다. 복잡한 정서 분석 모델 빌드에 대한 걱정 없이 이 모든 작업을 수행할 수 있습니다. 이것이 바로 Cortana Intelligence Suite의 장점 중 하나입니다.

또한 Azure 기계 학습 함수 관련 메트릭도 확인할 수 있습니다. 이를 수행하려면 **모니터** 탭을 클릭합니다. 세 가지 함수 관련 메트릭이 표시됩니다.

- **함수 요청**은 기계 학습 웹 서비스로 전송되는 요청 수를 나타냅니다.
- **함수 이벤트**는 요청의 이벤트 수를 나타냅니다. 기본적으로 기계 학습 웹 서비스에 대한 각 요청에는 최대 1,000개의 이벤트가 포함됩니다.
- **실패한 함수 요청**은 기계 학습 웹 서비스로 전송되는 요청 중 실패한 요청 수를 나타냅니다.

    ![스트림 분석 기계 학습, 기계 학습 모니터 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0921_2016-->