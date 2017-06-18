<properties 
	pageTitle="빠른 시작 가이드: 기계 학습 권장 사항 API | Microsoft Azure" 
	description="Azure 기계 학습 권장 사항 - 빠른 시작 가이드" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="luisca"/>

# 기계 학습 권장 사항 API에 대한 빠른 시작 가이드

>[AZURE.NOTE] 이 버전 대신 Recommendations API Cognitive 서비스를 사용하기 시작해야 합니다. Recommendations Cognitive 서비스가 이 서비스를 대체하게 되며, 모든 새로운 기능이 여기에서 개발됩니다. 일괄 처리 지원, 개선된 API 탐색기, 보다 깔끔한 API 노출 영역, 보다 일관적인 등록/청구 경험 등의 새로운 기능이 있습니다. [새로운 Cognitive 서비스로 마이그레이션](http://aka.ms/recomigrate)에 대해 자세히 알아보세요.


이 문서에서는 Microsoft Azure 기계 학습 권장 사항을 사용하도록 서비스나 응용 프로그램을 등록하는 방법에 대해 설명합니다. 권장 사항 API에 대한 자세한 내용은 [갤러리](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2)에서 확인할 수 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##일반 개요

Azure 기계 학습 권장 사항을 사용하려면 다음 단계를 수행해야 합니다.

* 모델 만들기 - 모델은 사용 데이터, 카탈로그 데이터 및 권장 사항 모델의 컨테이너입니다.
* 카탈로그 데이터 가져오기 - 카탈로그에는 항목에 대한 메타데이터 정보가 포함되어 있습니다.
* 사용 데이터 가져오기 - 다음 두 가지 방법의 하나로(또는 둘 다) 사용 데이터를 업로드할 수 있습니다.
	* 사용 데이터를 포함하는 파일을 업로드합니다.
	* 데이터 취득 이벤트를 전송합니다. 일반적으로 초기 권장 사항 모델(부트스트랩)을 만들고 시스템에서 데이터 취득 형식을 사용하여 충분한 데이터를 수집할 때까지 초기 모델을 사용할 수 있도록 사용 파일을 업로드합니다.
* 권장 사항 모델 작성 - 이 단계는 권장 사항 시스템이 모든 사용 데이터를 이용하여 권장 사항 모델을 만드는 비동기 작업입니다. 이 작업은 데이터 크기 및 빌드 구성 매개 변수에 따라 몇 분이나 몇 시간이 걸릴 수 있습니다. 빌드를 트리거하면 빌드 ID가 제공됩니다. 이 빌드 ID를 사용하여 권장 사항 소비를 시작하기 전에 빌드 프로세스가 종료된 시간을 확인합니다.
* 권장 사항 소비 – 특정 항목 또는 항목 목록에 대한 권장 사항을 가져옵니다.

위의 모든 단계는 Azure 기계 학습 권장 사항 API를 통해 수행됩니다. 이러한 각 단계를 구현하는 응용 프로그램 예제를 [갤러리에서도](http://1drv.ms/1xeO2F3) 다운로드할 수 있습니다.

##제한 사항

* 구독당 최대 모델 수는 10개입니다.
* 카탈로그에 포함할 수 있는 최대 항목 수는 100,000개입니다.
* 유지되는 사용 포인트의 최대 수는 5,000,000개입니다. 새 포인트가 업로드되거나 보고되면 가장 오래된 포인트가 삭제됩니다.
* POST로 전송할 수 있는 최대 데이터 크기(예: 카탈로그 데이터 가져오기, 사용 데이터 가져오기)는 200MB입니다.
* 활성화되지 않은 권장 사항 모델 빌드에 대한 초당 트랜잭션 수는 최대 2TPS입니다. 활성화된 권장 사항 모델 빌드는 최대 20TPS를 유지할 수 있습니다.

##통합

###인증
Microsoft Azure 마켓플레이스에서는 기본 또는 OAuth 인증 방법을 지원합니다. 마켓플레이스의 [계정 설정](https://datamarket.azure.com/account/keys)에서 키로 이동하여 계정 키를 쉽게 찾을 수 있습니다.
####기본 인증
인증 헤더 추가:

	Authorization: Basic <creds>
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
	
Base64로 변환(C#)

	var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
	var creds = Convert.ToBase64String(bytes);
	
Base64로 변환(JavaScript)

	var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
	



###서비스 URI
Azure 기계 학습 권장 사항 API에 대한 서비스 루트 URI는 [여기](https://api.datamarket.azure.com/amla/recommendations/v2/)입니다.

전체 서비스 URI는 OData 사양의 요소를 사용하여 표현됩니다.

###API 버전
각 API 호출은 “1.0”으로 설정해야 하는 apiVersion이라는 쿼리 매개 변수 종료 시 포함됩니다.

###ID 대/소문자 구분
API에서 반환되는 ID는 대/소문자를 구분하며, 후속 API 호출에서 매개 변수로 전달될 때에도 마찬가지입니다. 예를 들어 모델 ID와 카탈로그 ID는 대/소문자를 구분합니다.

###모델 만들기
"모델 만들기" 요청 만들기:

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelName |	문자(A-Z, a-z), 숫자(0-9), 하이픈(-) 및 밑줄(\_)만 사용할 수 있습니다.<br>최대 길이: 20 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |


**응답**:

HTTP 상태 코드: 200

- `feed/entry/content/properties/id` - 모델 ID를 포함합니다. **참고**: 모델 ID는 대/소문자를 구분합니다.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###카탈로그 데이터 가져오기

여러 번 호출하여 여러 카탈로그 파일을 같은 모델에 업로드하면 새 카탈로그 항목만 삽입됩니다. 기존 항목은 원래 값으로 유지됩니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자(대/소문자 구분) |
| filename | 카탈로그의 텍스트 식별자.<br>문자(A-Z, a-z), 숫자(0-9), 하이픈(-) 및 밑줄(\_)만 사용할 수 있습니다.<br>최대 길이: 50 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 카탈로그 데이터. 형식:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>이름</th><th>필수</th><th>유형</th><th>설명</th></tr><tr><td>Item Id</td><td>예</td><td>영숫자, 최대 길이 50</td><td>항목의 고유 식별자</td></tr><tr><td>Item Name</td><td>예</td><td>영숫자, 최대 길이 255</td><td>항목 이름</td></tr><tr><td>Item Category</td><td>예</td><td>영숫자, 최대 길이 255</td><td>이 항목이 속해 있는 범주(예: 요리책, 드라마...)</td></tr><tr><td>Description</td><td>아니요</td><td>영숫자, 최대 길이 4000</td><td>이 항목에 대한 설명</td></tr></table><br>최대 파일 크기 200MB<br><br>예:<br><pre>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book</pre> |


**응답**:

HTTP 상태 코드: 200

- `Feed\entry\content\properties\LineCount` – 허용되는 줄 수
- `Feed\entry\content\properties\ErrorCount` – 오류로 인해 삽입되지 않은 줄 수

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###사용 데이터 가져오기

####파일 업로드
이 섹션에서는 파일을 사용하여 사용 데이터를 업로드하는 방법을 보여 줍니다. 사용 데이터를 사용하여 이 API를 여러 번 호출할 수 있습니다. 모든 호출에 대한 모든 사용 데이터가 저장됩니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자(대/소문자 구분) |
| filename | 카탈로그의 텍스트 식별자.<br>문자(A-Z, a-z), 숫자(0-9), 하이픈(-) 및 밑줄(\_)만 사용할 수 있습니다.<br>최대 길이: 50 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 사용 데이터. 형식:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>이름</th><th>필수</th><th>유형</th><th>설명</th></tr><tr><td>사용자 Id</td><td>예</td><td>영숫자</td><td>사용자의 고유 식별자</td></tr><tr><td>Item Id</td><td>예</td><td>영숫자, 최대 길이 50</td><td>항목의 고유 식별자</td></tr><tr><td>Time</td><td>아니요</td><td>다음 형식의 날짜: YYYY/MM/DDTHH:MM:SS(예: 2013/06/20T10:00:00)</td><td>데이터의 시간</td></tr><tr><td>Event</td><td>아니요, 제공된 경우 날짜도 입력해야 함</td><td>다음 중 하나:<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>최대 파일 크기 200MB<br><br>예:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**응답**:

HTTP 상태 코드: 200

- `Feed\entry\content\properties\LineCount` – 허용되는 줄 수
- `Feed\entry\content\properties\ErrorCount` – 오류로 인해 삽입되지 않은 줄 수
- `Feed\entry\content\properties\FileId` – 파일 식별자


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####데이터 취득 사용
이 섹션에서는 일반적으로 웹 사이트에서 Azure 기계 학습 권장 사항에 실시간으로 이벤트를 전송하는 방법을 보여 줍니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|||
|요청 본문| Event data entry for each event you want to send. SessionId 필드에서 같은 사용자 또는 브라우저 세션에 대해 같은 ID를 전송해야 합니다. 아래 이벤트 본문 샘플을 참조하세요.|


- 'Click' 이벤트의 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
		<Name>Click</Name>
		<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
		</EventData>
		</EventData>
		</Event>

- 'RecommendationClick' 이벤트의 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RecommendationClick</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 'AddShopCart' 이벤트의 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 'RemoveShopCart' 이벤트의 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>RemoveShopCart</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    	</EventData>
  		</EventData>
		</Event>

- 'Purchase' 이벤트의 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
		<SessionId>11112222</SessionId>
		<EventData>
		<EventData>
			<Name>Purchase</Name> 
			<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
		</EventData>
		</EventData>
		</Event>

- 두 개의 이벤트 'Click' 및 'AddShopCart'를 보내는 예:

		<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  		<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  		<SessionId>11112222</SessionId>
  		<EventData>
    	<EventData>
      	<Name>Click</Name>
      	<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      	<ItemName>itemName</ItemName>
      	<ItemDescription>item description</ItemDescription>
      	<ItemCategory>category</ItemCategory>
    	</EventData>
    	<EventData>
      	<Name>AddShopCart</Name>
      	<ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    	</EventData>
  		</EventData>
		</Event>

**응답**:
HTTP 상태 코드: 200

###권장 사항 모델 작성

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자(대/소문자 구분) |
| userDescription | 카탈로그의 텍스트 식별자입니다. 공백을 사용하는 경우 대신 %20을 사용하여 인코드해야 합니다. 위 예제를 참조하세요.<br>최대 길이: 50 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

비동기 API입니다. 응답으로 빌드 ID가 제공됩니다. 빌드가 종료된 시점을 알려면 "모델의 빌드 상태 가져오기" API를 호출하여 응답에서 이 빌드 ID를 찾습니다. 데이터 크기에 따라 빌드를 가져오는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.

빌드가 종료될 때까지 권장 사항을 소비할 수 없습니다.

유효한 빌드 상태:

- Create – 모델을 만듦
- Queued – 모델 빌드가 트리거되고 쿼리됨
- Building – 모델을 빌드하는 중
- Success – 빌드가 성공적으로 종료됨
- Error – 오류로 인해 빌드가 종료됨
- Cancelled – 빌드가 취소됨
- Cancelling – 빌드 취소 중


다음 경로에서 빌드 ID를 찾을 수 있습니다. `Feed\entry\content\properties\Id`

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###모델의 빌드 상태 가져오기

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자(대/소문자 구분) |
|	onlyLastBuild |	모델의 빌드 기록을 모두 반환할지 또는 최신 빌드의 상태만 반환할지를 나타냅니다. |
|	apiVersion |	1\.0 |


**응답**:

HTTP 상태 코드: 200

응답은 빌드당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `feed/entry/content/properties/UserName` - 사용자의 이름
- `feed/entry/content/properties/ModelName` - 모델의 이름
- `feed/entry/content/properties/ModelId` – 모델의 고유 식별자
- `feed/entry/content/properties/IsDeployed` – 빌드 배포 여부(활성 빌드라고도 함)
- `feed/entry/content/properties/BuildId` – 빌드의 고유 식별자
- `feed/entry/content/properties/BuildType` - 빌드의 형식
- `feed/entry/content/properties/Status` – 빌드 상태 다음 중 하나일 수 있음: Error, Building, Queued, Cancelling, Cancelled, Success
- `feed/entry/content/properties/StatusMessage` – 자세한 상태 메시지(특정 상태에만 적용)
- `feed/entry/content/properties/Progress` – 빌드 진행률(%)
- `feed/entry/content/properties/StartTime` – 빌드 시작 시간
- `feed/entry/content/properties/EndTime` – 빌드 종료 시간
- `feed/entry/content/properties/ExecutionTime` – 빌드 기간
- `feed/entry/content/properties/ProgressStep` – 진행 중인 빌드의 현재 단계에 대한 세부 정보

유효한 빌드 상태:
- Created – 빌드 요청 항목이 만들어짐
- Queued – 빌드 요청이 트리거되었으며 큐에 대기됨
- Building – 빌드가 진행 중임
- Success – 빌드가 성공적으로 종료됨
- Error – 오류로 인해 빌드가 종료됨
- Cancelled – 빌드가 취소됨
- Cancelling – 빌드 취소 중

유효한 빌드 형식 값:
- Rank - 순위 빌드 (순위 빌드에 대한 자세한 내용은 "기계 학습 권장 사항 API 문서" 설명서를 참조하세요.)
- Recommendation - 권장 사항 빌드
- Fbt - 자주 함께 구매됨 빌드

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###권장 사항 가져오기

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자(대/소문자 구분) |
| itemIds | 권장할 항목의 쉼표로 구분된 목록입니다.<br>최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 |
| includeMetatadata | 나중에 사용, 항상 false |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200

응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `Feed\entry\content\properties\Id` - 권장된 항목 ID
- `Feed\entry\content\properties\Name` - 항목의 이름
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음)
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명)

OData XML

아래 예제 응답은 10개의 권장 항목을 포함합니다.

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###모델 업데이트
모델 설명 또는 활성 빌드 ID를 업데이트할 수 있습니다.
*활성 빌드 ID* – 모든 모델에 대한 모든 빌드에는 빌드 ID가 있습니다. 활성 빌드 ID는 모든 새 모델 중 처음 성공한 빌드입니다. 활성 빌드 ID가 있는데 같은 모델에 대한 추가 빌드를 수행하려면 이 활성 빌드 ID를 기본 빌드 ID로 명시적으로 설정해야 합니다. 권장 사항을 소비할 때 사용할 빌드 ID를 지정하지 않으면 자동으로 기본 빌드 ID가 사용됩니다.

이 메커니즘을 사용하면 프로덕션에 권장 사항 모델을 포함하고 나서 새 모델을 빌드하고 프로덕션으로 수준을 올리기 전에 테스트할 수 있습니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| id | 모델의 고유 식별자(대/소문자 구분) |
| apiVersion | 1\.0 |
|||
| 요청 본문 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>XML 태그 Description 및 ActiveBuildId는 선택 사항입니다. Description 또는 ActiveBuildId를 설정하지 않으려면 전체 태그를 제거합니다. |

**응답**:

HTTP 상태 코드: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
	</feed>

##법적 정보
이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다.
여기에서 설명하는 일부 예는 설명 목적으로만 제공되는 가상의 예이며, 어떠한 실제 사례와도 연관시킬 의도가 없으며 그렇게 유추해서도 안 됩니다.
이 문서는 Microsoft 제품의 지적 소유권에 대한 법적 권한을 사용자에게 제공하지 않습니다. 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다. © 2014 Microsoft. All rights reserved.
 

<!---HONumber=AcomDC_0914_2016-->