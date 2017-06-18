<properties 
	pageTitle="기계 학습 권장 사항: JavaScript 통합 | Microsoft Azure" 
	description="Azure 기계 학습 권장 사항 - JavaScript 통합 - 설명서" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="luisca"/>

# Azure 기계 학습 권장 사항 - JavaScript 통합

>[AZURE.NOTE] 이 버전 대신 Recommendations API Cognitive 서비스를 사용하기 시작해야 합니다. Recommendations Cognitive 서비스가 이 서비스를 대체하게 되며, 모든 새로운 기능이 여기에서 개발됩니다. 일괄 처리 지원, 개선된 API 탐색기, 보다 깔끔한 API 노출 영역, 보다 일관적인 등록/청구 경험 등의 새로운 기능이 있습니다. [새로운 Cognitive 서비스로 마이그레이션](http://aka.ms/recomigrate)에 대해 자세히 알아보세요.


이 문서에는 JavaScript를 사용하여 사이트를 통합하는 방법을 설명합니다. JavaScript를 통해 데이터 취득 이벤트를 전송하고, 권장 모델을 작성한 후 권장 사항을 사용할 수 있습니다. JS를 통해 수행하는 모든 작업은 서버 쪽에서도 수행할 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. 일반 개요
Azure ML 권장 사항과 사이트를 통합하는 과정은 다음 두 단계로 이루어집니다.

1.	Azure ML 권장 사항으로 이벤트를 전송합니다. 그러면 권장 모델을 작성할 수 있습니다.
2.	권장 사항을 사용합니다. 모델이 작성되고 나면 권장 사항을 사용할 수 있습니다. 이 문서에서는 모델을 작성하는 방법을 설명하지 않습니다. 방법에 대한 자세한 내용은 빠른 시작 가이드를 참조하세요.


<ins>I단계</ins>

첫 번째 단계에서는 페이지가 데이터 마켓을 통해 html 페이지에서 발생하는 이벤트를 Azure ML 권장 사항 서버로 전송할 수 있게 해주는 작은 JavaScript 라이브러리를 html 페이지에 삽입합니다.

![Drawing1][1]

<ins>II단계</ins>

권장 사항을 페이지에 표시하려는 두 번째 단계에서는 다음 옵션 중 하나를 선택합니다.

1\. 서버(페이지 렌더링 단계)가 데이터 마켓을 통해 Azure ML 권장 사항 서버를 호출하여 권장 사항을 가져옵니다. 결과는 항목 ID 목록을 포함합니다. 서버는 항목 메타데이터(예: 이미지, 설명)로 결과를 보완하고 만든 페이지를 브라우저로 전송해야 합니다.

![Drawing2][2]

2\. 다른 옵션은 1단계의 작은 JavaScript 파일을 사용하여 권장 항목의 단순 목록을 가져오는 것입니다. 여기서 받은 데이터는 첫 번째 옵션에서 받은 데이터보다 간결합니다.

![Drawing3][3]

##2\. 필수 조건

1. API를 사용하여 새 모델을 만듭니다. 수행 방법에 대한 빠른 시작 가이드를 참조하세요.
2. &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt;를 base64로 인코드합니다. 이 코드는 JS 코드가 API를 호출할 수 있게 하는 기본 인증에 사용됩니다.


##3\. JavaScript를 사용하여 데이터 취득 이벤트 전송
다음 단계는 이벤트 전송에 도움이 됩니다.

1.	JQuery 라이브러리를 코드에 포함합니다. 다음 URL의 nuget에서 다운로드할 수 있습니다.

		http://www.nuget.org/packages/jQuery/1.8.2
2.	다음 URL의 권장 사항 JavaScript 라이브러리를 포함합니다. http://aka.ms/RecoJSLib1

3.	적절한 매개 변수를 사용하여 Azure ML 권장 사항 라이브러리를 초기화합니다.

		<script>
			AzureMLRecommendationsStart("<base64encoding of username:key>",
			"<model_id>");
		</script>

4.	적절한 이벤트를 전송합니다. 모든 이벤트 형식에 대한 아래 세부 섹션을 참조하세요(Click 이벤트 예).

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { 		
        	        	AzureMLRecommendationsEvent = [];
	                }
			AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
		</script>


###3\.1. 제한 사항 및 브라우저 지원
참조 구현이며, 있는 그대로 제공됩니다. 모든 주요 브라우저를 지원합니다.

###3\.2. 이벤트 형식
라이브러리에서 지원하는 5가지 이벤트 형식은 Click, Recommendation Click, Add to Shop Cart, Remove from Shop Cart 및 Purchase입니다. 사용자 컨텍스트를 설정하는 데 사용되는 Login이라는 추가 이벤트가 있습니다.

####3\.2.1. Click 이벤트
이 이벤트는 사용자가 항목을 클릭할 때마다 사용해야 합니다. 일반적으로 사용자가 항목을 클릭하면 항목 세부 정보가 포함된 새 페이지가 열립니다. 이 페이지에서 이 이벤트가 트리거되어야 합니다.

매개 변수:
- event(문자열, 필수) - "click"
- item(문자열, 필수) - 항목의 고유 식별자
- itemName(문자열, 선택) - 항목의 이름
- itemDescription(문자열, 선택) - 항목에 대한 설명
- itemCategory(문자열, 선택) - 항목의 범주
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
		</script>

또는 선택적 데이터를 포함합니다.

		<script>
			if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
		</script>


####3\.2.2. Recommendation Click 이벤트
이 이벤트는 사용자가 Azure ML 권장 사항에서 권장 항목으로 받은 항목을 클릭할 때마다 사용해야 합니다. 일반적으로 사용자가 항목을 클릭하면 항목 세부 정보가 포함된 새 페이지가 열립니다. 이 페이지에서 이 이벤트가 트리거되어야 합니다.

매개 변수:
- event(문자열, 필수) - "recommendationclick"
- item(문자열, 필수) - 항목의 고유 식별자
- itemName(문자열, 선택) - 항목의 이름
- itemDescription(문자열, 선택) - 항목에 대한 설명
- itemCategory(문자열, 선택) - 항목의 범주
- seeds(문자열 배열, 선택) - 권장 사항 쿼리를 생성한 시드
- recoList(문자열 배열, 선택) - 클릭한 항목을 생성한 권장 사항 요청의 결과
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
		</script>

또는 선택적 데이터를 포함합니다.

		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"] 				});
		</script>


####3\.2.3. Add Shopping Cart 이벤트
이 이벤트는 사용자가 장바구니에 항목을 추가할 때 사용해야 합니다. 매개 변수:
* event(문자열, 필수) - "addshopcart"
* item(문자열, 필수) - 항목의 고유 식별자
* itemName(문자열, 선택) - 항목의 이름
* itemDescription(문자열, 선택) - 항목에 대한 설명
* itemCategory(문자열, 선택) - 항목의 범주
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
		</script>

####3\.2.4. Remove Shopping Cart 이벤트
이 이벤트는 사용자가 장바구니에서 항목을 제거할 때 사용해야 합니다.

매개 변수:
* event(문자열, 필수) - "removeshopcart"
* item(문자열, 필수) - 항목의 고유 식별자
* itemName(문자열, 선택) - 항목의 이름
* itemDescription(문자열, 선택) - 항목에 대한 설명
* itemCategory(문자열, 선택) - 항목의 범주
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
		</script>

####3\.2.5. Purchase 이벤트
이 이벤트는 사용자가 장바구니를 구매했을 때 사용해야 합니다.

매개 변수:
* event(문자열) – "purchase"
* items(Purchased) – 구매한 각 항목에 대한 항목이 저장되는 배열<br><br> Purchased 형식:
	* item(문자열) - 항목의 고유 식별자
	* count(정수 또는 문자열) – 구매한 항목 수
	* price(부동 소수점 수 또는 문자열) – 선택적 필드 - 항목의 가격

아래 예제에서는 3개 항목(33, 34, 35)의 구매를 보여 줍니다. 두 항목은 모든 필드가 채워져 있고(항목, 개수, 가격), 한 항목(항목 34)은 가격이 없습니다.

		<script>
			if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
		</script>

####3\.2.6. User Login 이벤트
Azure ML 권장 사항 이벤트 라이브러리는 동일한 브라우저에서 제공된 이벤트를 식별하기 위해 쿠키를 만들고 사용합니다. 모델 결과를 개선하기 위해 Azure ML 권장 사항에서 쿠키 사용을 재정의하는 사용자 고유 ID를 설정할 수 있습니다.

이 이벤트는 사용자가 사이트에 로그인한 후에 사용해야 합니다.

매개 변수:
* event(문자열) – "userlogin"
* user(문자열) – 사용자의 공유 ID. 

		<script>
 			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = ; } 
			AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
		</script>

##4\. JavaScript 통해 권장 사항 사용
권장 사항을 사용하는 코드는 클라이언트 웹 페이지의 일부 JavaScript 이벤트에 의해 트리거됩니다. 권장 사항 응답에는 권장 항목 ID, 이름 및 해당 등급이 포함됩니다. 권장 항목의 목록 표시에만 이 옵션을 사용하는 것이 좋습니다. 더 복잡한 처리(예: 항목의 메타데이터 추가)는 서버 쪽 통합에서 수행해야 합니다.

###4\.1 권장 사항 사용
권장 사항을 사용하려면 필요한 JavaScript 라이브러리를 페이지에 포함하고 AzureMLRecommendationsStart를 호출해야 합니다. 섹션 2를 참조하세요.

하나 이상의 항목에 대해 권장 사항을 사용하려면 AzureMLRecommendationsGetI2IRecommendation이라는 메서드를 호출해야 합니다.

매개 변수:
* items(문자열 배열) - 권장 사항을 가져올 하나 이상의 항목. Fbt 빌드를 사용하는 경우 하나의 항목만 설정할 수 있습니다.
* numberOfResults(int) – 필요한 결과 수
* includeMetadata(부울, 선택) - ‘true’로 설정된 경우 결과에서 메타데이터 필드를 채워야 함을 나타냄
* 처리 함수 - 반환된 권장 사항을 처리할 함수. 반환되는 데이터의 배열:
	* Item - 항목의 고유 ID
	* name – 항목 이름(카탈로그에 있는 경우)
	* rating - 권장 사항 등급
	* metadata - 항목의 메타데이터를 나타내는 문자열

예제: 다음 코드는 "64f6eb0d-947a-4c18-a16c-888da9e228ba" 항목에 대한 8개 권장 사항을 요청하고(includeMetadata를 지정하지 않아 메타데이터가 필요 없음을 암시적으로 나타냄) 그 결과를 버퍼에 연결합니다.

		<script>
 			var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
 				var buff = "";
 				for (var ii = 0; ii < reco.length; ii++) {
   					buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
 				}
 				alert(buff);
			});
		</script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

<!----HONumber=AcomDC_0914_2016-->