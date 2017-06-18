<properties 
	pageTitle="기계 학습 권장 사항 API 설명서 | Microsoft Azure" 
	description="Microsoft Azure 마켓플레이스에서 사용할 수 있는 권장 사항 엔진에 대한 Azure 기계 학습 권장 사항 API 설명서입니다." 
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
	ms.author="LuisCa"/>

#Azure 기계 학습 권장 사항 API 설명서

>[AZURE.NOTE] 이 버전 대신 Recommendations API Cognitive 서비스를 사용하기 시작해야 합니다. Recommendations Cognitive 서비스가 이 서비스를 대체하게 되며, 모든 새로운 기능이 여기에서 개발됩니다. 일괄 처리 지원, 개선된 API 탐색기, 보다 깔끔한 API 노출 영역, 보다 일관적인 등록/청구 경험 등의 새로운 기능이 있습니다. [새로운 Cognitive 서비스로 마이그레이션](http://aka.ms/recomigrate)에 대해 자세히 알아보세요.

이 문서에서는 Microsoft Azure 기계 학습 권장 사항 API에 대해 설명합니다.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. 일반 개요
이 문서는 API 참조입니다. “Azure 기계 학습 권장 사항 – 빠른 시작” 문서로 시작해야 합니다.

Azure 기계 학습 추천 API는 다음 논리 그룹으로 나뉩니다.

- <ins>제한 사항</ins> - 추천 API 제한 사항입니다.
- <ins>일반 정보</ins> - 인증, 서비스 URI 및 버전 관리에 대한 정보입니다.
- <ins>모델 기본</ins> - 모델에 대한 기본 작업(예: 모델 만들기, 업데이트 및 삭제)을 수행할 수 있는 API입니다.
- <ins>모델 고급</ins> – 모델에 대한 고급 데이터 정보를 얻을 수 있는 API입니다.
- <ins>모델 비즈니스 규칙</ins> – 모델 권장 사항 결과에서 비즈니스 규칙을 관리할 수 있는 API입니다.
- <ins>카탈로그</ins> – 모델 카탈로그에 대한 기본 작업을 수행할 수 있는 API입니다. 카탈로그에는 사용 데이터의 항목에 대한 메타데이터 정보가 포함되어 있습니다.
- <ins>기능</ins> - 항목에 대한 정보를 카탈로그로 작성하고 이 정보를 사용하여 더 나은 권장 사항을 작성하는 방법을 알 수 있도록 하는 API입니다.
- <ins>사용 데이터</ins> - 모델 사용 데이터에 대한 기본 작업을 수행할 수 있는 API입니다. 기본 형식의 사용 데이터는 &#60;userId&#62;,&#60;itemId&#62; 쌍을 포함하는 행으로 구성됩니다.
- <ins>빌드</ins> – 모델 빌드를 트리거하고 이 빌드와 관련된 기본 작업을 수행할 수 있는 API입니다. 유용한 사용 데이터가 있으면 모델 빌드를 트리거할 수 있습니다.
- <ins>권장 사항</ins> – 모델 빌드가 종료되면 권장 사항을 사용할 수 있는 API입니다.
- <ins>사용자 데이터</ins> - 사용자의 사용 현황 데이터 관련 정보를 가져올 수 있는 API입니다.
- <ins>알림</ins> - API 작업과 관련된 문제에 대한 알림을 받을 수 있는 API입니다. 예를 들어 데이터 취득을 통해 사용 데이터를 보고하고 대부분의 이벤트 처리에 실패한 경우 오류 알림이 발생합니다.

##2\. 제한 사항

- 구독당 최대 모델 수는 10개입니다.
- 모델당 최대 빌드 수는 20입니다.
- 카탈로그에 포함할 수 있는 최대 항목 수는 100,000개입니다.
- 유지되는 사용 포인트의 최대 수는 5,000,000개입니다. 새 포인트가 업로드되거나 보고되면 가장 오래된 포인트가 삭제됩니다.
- POST로 전송할 수 있는 최대 데이터 크기(예: 카탈로그 데이터 가져오기, 사용 데이터 가져오기)는 200MB입니다.
- 권장 사항을 가져올 때 질문할 수 있는 최대 항목 수는 150입니다.

##3\. API – 일반 정보

###3\.1. 인증
인증과 관련된 Microsoft Azure 마켓플레이스 지침을 따르세요. 마켓플레이스에서는 기본 또는 OAuth 인증 방법을 지원합니다.

###3\.2. 서비스 URI
Azure 기계 학습 권장 사항 API에 대한 서비스 루트 URI는 [여기](https://api.datamarket.azure.com/amla/recommendations/v3/)입니다.

전체 서비스 URI는 OData 사양의 요소를 사용하여 표현됩니다.

###3\.3. API 버전
각 API 호출은 1.0으로 설정해야 하는 apiVersion이라는 쿼리 매개 변수 종료 시 포함됩니다.

###3\.4. ID 대/소문자 구분
API에서 반환되는 ID는 대/소문자를 구분하며, 후속 API 호출에서 매개 변수로 전달될 때에도 마찬가지입니다. 예를 들어 모델 ID와 카탈로그 ID는 대/소문자를 구분합니다.

##4\. 권장 사항 품질 및 콜드 항목

###4\.1. 권장 사항 품질

권장 사항 모델 만들기는 일반적으로 시스템에서 권장 사항을 제공하도록 하는 데 충분합니다. 그러나 권장 사항 품질은 처리한 사용 현황 및 카탈로그의 적용 범위에 따라 달라집니다. 예를 들어 콜드 항목(중요한 사용 현황이 없는 항목)이 많은 경우에는 시스템에서 해당 항목에 대한 권장 사항을 제공하거나 해당 항목을 권장 항목으로 사용하기 어렵습니다. 콜드 항목 문제를 해결하기 위해 시스템에서는 항목의 메타데이터를 사용하여 권장 사항을 개선할 수 있도록 합니다. 이 메타데이터를 기능이라고 합니다. 일반적인 기능은 책의 저자 또는 동영상의 배우입니다. 기능은 키/값 문자열 형식으로 카탈로그를 통해 제공됩니다. 카탈로그 파일의 전체 형식은 [카탈로그 가져오기 섹션](#81-import-catalog-data)을 참조하세요.

###4\.2. 순위 빌드

기능은 권장 사항 모델을 개선할 수 있지만 그러려면 의미 있는 기능을 사용해야 합니다. 이를 위해 순위 빌드라는 새 빌드가 도입되었습니다. 이 빌드는 기능의 유용성 순위를 매깁니다. 의미 있는 기능은 순위 점수가 2 이상인 기능입니다. 의미 있는 기능을 이해한 후에는 의미 있는 기능 목록(또는 하위 목록)으로 권장 사항 빌드를 트리거합니다. 이러한 기능을 사용하여 웜 항목과 콜드 항목 모두를 개선할 수 있습니다. 웜 항목에 사용하려면 `UseFeatureInModel` 빌드 매개 변수를 설정해야 합니다. 콜드 항목에 사용하려면 `AllowColdItemPlacement` 빌드 매개 변수를 설정해야 합니다. 참고: `UseFeatureInModel`을 설정하지 않고 `AllowColdItemPlacement`를 사용할 수 없습니다.

###4\.3. 권장 사항 추론

권장 사항 추론은 기능 사용의 또 다른 측면입니다. 실제로 Azure 기계 학습 권장 사항 엔진은 기능을 사용하여 권장 사항 설명(추론이라고도 함)을 제공할 수 있으며, 이는 권장 소비자에게 권장 항목에 대한 확신을 심어 줍니다. 추론을 사용하려면 권장 사항 빌드를 요청하기 전에 `AllowFeatureCorrelation` 및 `ReasoningFeatureList` 매개 변수를 설정해야 합니다.


##5\. 모델 기본

###5\.1. 모델 만들기
"모델 만들기" 요청을 만듭니다.

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

- `feed/entry/content/properties/id` – 모델 ID를 포함합니다. **참고**: 모델 ID는 대/소문자를 구분합니다.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###5\.2. 모델 가져오기
"모델 가져오기" 요청을 만듭니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	id |	모델의 고유 식별자(대/소문자 구분) |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

다음 요소 아래에서 모델 데이터를 찾을 수 있습니다.

- `feed/entry/content/properties/Id` – 모델의 고유 ID
- `feed/entry/content/properties/Name` – 모델 이름
- `feed/entry/content/properties/Date` - 모델을 만든 날짜
- `feed/entry/content/properties/Status` – 모델 상태 다음 중 하나
    - Created - 모델이 생성되었으며 카탈로그 및 사용 현황을 포함하지 않음
	- ReadyForBuild – 모델이 생성되었으며 카탈로그 및 사용 현황을 포함함
- `feed/entry/content/properties/HasActiveBuild` – 모델이 성공적으로 빌드된 경우를 나타냄
- `feed/entry/content/properties/BuildId` – 모델 활성 빌드 ID
- `feed/entry/content/properties/Mpr` – 모델 평균 백분위수 순위(MPR - 자세한 내용은 ModelInsight 참조)
- `feed/entry/content/properties/UserName` – 모델 내부 사용자 이름

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Get A List Of All Models</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T14:35:51Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
		<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
	  </entry>
	</feed>

###5\.3. 모든 모델 가져오기
현재 사용자의 모든 모델을 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

- `feed/entry/content/properties/Id` – 모델의 고유 ID
- `feed/entry/content/properties/Name` – 모델 이름
- `feed/entry/content/properties/Date` - 모델을 만든 날짜
- `feed/entry/content/properties/Status` – 모델 상태 다음 중 하나
  - Created - 모델이 생성되었으며 카탈로그 및 사용 현황을 포함하지 않음
  - ReadyForBuild – 모델이 생성되었으며 카탈로그 및 사용 현황을 포함함
- `feed/entry/content/properties/HasActiveBuild` – 모델이 성공적으로 빌드된 경우를 나타냄
- `feed/entry/content/properties/BuildId` – 모델 활성 빌드 ID
- `feed/entry/content/properties/Mpr` – 모델 MPR(자세한 내용은 ModelInsight 참조)
- `feed/entry/content/properties/UserName` – 모델 내부 사용자 이름
- `feed/entry/content/properties/UsageFileNames` – 쉼표로 구분된 모델 사용 파일 목록
- `feed/entry/content/properties/CatalogId` – 모델 카탈로그 ID
- `feed/entry/content/properties/Description` – 모델 설명
- `feed/entry/content/properties/CatalogFileName` – 모델 카탈로그 파일 이름

OData XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get A List Of All Models</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-28T14:35:51Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
					<d:Name m:type="Edm.String">vah-11111</d:Name>
					<d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
					<d:Status m:type="Edm.String">ReadyForBuild</d:Status>
					<d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
					<d:BuildId m:type="Edm.String">-1</d:BuildId>
					<d:Mpr m:type="Edm.String">0</d:Mpr>
					<d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
					<d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
					<d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
					<d:Description m:type="Edm.String">short description</d:Description>
					<d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
				</m:properties>
			</content>
		</entry>
	</feed>

###5\.4. 모델 업데이트

모델 설명 또는 활성 빌드 ID를 업데이트할 수 있습니다.<br> <ins>활성 빌드 ID</ins> - 모든 모델에 대한 모든 빌드에는 "빌드 ID"가 있습니다. 활성 빌드 ID는 모든 새 모델 중 처음 성공한 빌드입니다. 활성 빌드 ID가 있는데 같은 모델에 대한 추가 빌드를 수행하려면 이 활성 빌드 ID를 기본 빌드 ID로 명시적으로 설정해야 합니다. 권장 사항을 소비할 때 사용할 빌드 ID를 지정하지 않으면 자동으로 기본 빌드 ID가 사용됩니다.<br> 이 메커니즘을 사용하면 프로덕션에 권장 사항 모델을 포함하고 나서 새 모델을 빌드하고 프로덕션으로 수준을 올리기 전에 테스트할 수 있습니다.


| HTTP 메서드 | URI |
|:--------|:--------|
|PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	id | 모델의 고유 식별자(대/소문자 구분) |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>XML 태그 Description 및 ActiveBuildId는 선택 사항입니다. Description 또는 ActiveBuildId를 설정하지 않으려면 전체 태그를 제거합니다.|

**응답**:

HTTP 상태 코드: 200

###5\.5. 모델 삭제
ID별로 기존 모델을 삭제합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	id |	모델의 고유 식별자(대/소문자 구분) |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Delete Model by Id</subtitle>
	  <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
	  <rights type="text" />
	  <updated>2014-10-28T10:39:33Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
		<d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
	  </entry>
	</feed>

##6\. 모델 고급

###6\.1. 모델 데이터 정보
이 모델을 빌드할 때 사용된 사용 데이터에 대한 통계 데이터를 반환합니다.

권장 사항 빌드에만 사용할 수 있습니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

데이터는 속성 컬렉션으로 반환됩니다.

- `feed/entry/id/content/properties/key` - 속성 이름 유지
- `feed/entry/id/content/properties/value` - 속성 값 유지

아래 표에서는 각 키가 나타내는 값을 설명합니다.

|키|설명|
|:-----|:----|
| AvgItemLength | 항목당 평균 고유 사용자 수 |
| AvgUserLength | 사용자당 평균 고유 항목 수 |
| DensificationNumberOfItems | 모델링할 수 없는 항목을 정리한 후의 항목 수 |
| DensificationNumberOfUsers | 모델링할 수 없는 사용자 및 항목을 정리한 후의 사용 포인트 수 |
| DensificationNumberOfRecords | 모델링할 수 없는 사용자 및 항목을 정리한 후의 사용 포인트 수 |
| MaxItemLength | 가장 널리 사용되는 항목의 고유 사용자 수 |
| MaxUserLength | 사용자의 최대 고유 항목 수 |
| MinItemLength | 항목의 최대 고유 사용자 수 |
| MinUserLength | 사용자의 최소 고유 항목 수 |
| RawNumberOfItems | 사용 파일에 있는 항목 수 |
| RawNumberOfUsers | 정리하기 전의 사용 포인트 수 |
| RawNumberOfRecords | 정리하기 전의 사용 포인트 수 |
| SamplingNumberOfItems | 해당 없음 |
| SamplingNumberOfRecords | 해당 없음 |
| SamplingNumberOfUsers | 해당 없음 |

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get data insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
	</entry>
	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
	<title type="text">GetDataInsightStatisticsEntity</title>
	<updated>2014-10-27T14:21:21Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
	<content type="application/xml">
		<m:properties>
			<d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
			<d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxItemLength</d:Key>
				<d:Value m:type="Edm.String">4,704</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
    	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    	<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MaxUserLength</d:Key>
				<d:Value m:type="Edm.String">190</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinItemLength</d:Key>
				<d:Value m:type="Edm.String">8</d:Value>
			</m:properties>
    	</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">MinUserLength</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
				<d:Value m:type="Edm.String">2,000</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
				<d:Value m:type="Edm.String">72,022</d:Value>
			</m:properties>
		</content>
    </entry>
    <entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
		<title type="text">GetDataInsightStatisticsEntity</title>
		<updated>2014-10-27T14:21:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
				<d:Value m:type="Edm.String">9,044</d:Value>
			</m:properties>
		</content>
    </entry>
    </feed>

###6\.2. 모델 정보
활성 빌드 또는 특정 빌드(지정된 경우)에 대한 모델 정보를 반환합니다.

권장 사항 빌드에만 사용할 수 있습니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |활성 빌드 ID 사용:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>특정 빌드 ID 사용:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	buildId |	(선택 사항) - 성공적인 빌드를 식별하는 번호 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

데이터는 속성 컬렉션으로 반환됩니다.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


아래 표에서는 각 키가 나타내는 값을 설명합니다.

| 키 | 설명 |
|:---- |:----|
| CatalogCoverage | 사용 패턴으로 모델링할 수 있는 카탈로그의 부분. 나머지 항목에는 콘텐츠 기반 기능이 필요합니다. |
| Mpr | 모델의 평균 백분위수 순위. 낮을수록 좋습니다. |
| NumberOfDimensions | 행렬 인수 분해 알고리즘에서 사용된 차원 수 |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get model insight statistics</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">CatalogCoverage</d:Key>
				<d:Value m:type="Edm.String">1.000</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">Mpr</d:Key>
				<d:Value m:type="Edm.String">0.367</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetModelInsightStatisticsEntity</title>
		<updated>2014-10-27T14:27:11Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
				<d:Value m:type="Edm.String">20</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###6\.3. 모델 샘플 가져오기
권장 사항 모델의 샘플을 가져옵니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>특정 빌드 ID 사용:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

OData XML

원시 텍스트 형식으로 응답이 반환됩니다.

<pre>
Level 1
---------------
655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel
	655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Rating: 0.5215
	3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Rating: 0.5151
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5148
	6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Rating: 0.5146
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.514
56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai
	56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Rating: 0.5218
	53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Rating: 0.5212
	fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Rating: 0.5188
	8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Rating: 0.5156
	d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Rating: 0.5156
50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path
	cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Rating: 0.5266
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5252
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5244
	e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Rating: 0.5227
	6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Rating: 0.5222
5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club)
	5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Rating: 0.537
	5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Rating: 0.5277
	bc5b69db-733b-4346-adde-3927544258f7, Downtown Rating: 0.5275
	31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Rating: 0.5252
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5238
68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived
	68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Rating: 0.5379
	6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Rating: 0.5345
	cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Rating: 0.5325
	382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Rating: 0.5309
	6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Rating: 0.5285
37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships
	37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Rating: 0.5397
	f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Rating: 0.5207
	ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Rating: 0.5195
	0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Rating: 0.5194
	883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Rating: 0.5184
ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball
	d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Rating: 0.5416
	ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Rating: 0.5403
	49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Rating: 0.5394
	cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Rating: 0.5379
	8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Rating: 0.5377
12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles
	07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5417
	e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.5416
	1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Rating: 0.5371
	56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Rating: 0.5366
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5366
df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish
	56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Rating: 0.5417
	0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Rating: 0.5416
	df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Rating: 0.5408
	400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Rating: 0.5383
	f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Rating: 0.5358
de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology
	de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Rating: 0.5422
	b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Rating: 0.5385
	34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Rating: 0.5379
	306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Rating: 0.5376
	e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Rating: 0.5367

Level 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony)
	352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Rating: 0.5425
	74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Rating: 0.5387
	9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Rating: 0.5372
	e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Rating: 0.5353
	b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5332
c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days
	0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5433
	c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Rating: 0.543
	a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Rating: 0.5327
	6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Rating: 0.5307
	798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Rating: 0.5301
73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics)
	cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Rating: 0.5434
	5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Rating: 0.5406
	73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Rating: 0.5403
	d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Rating: 0.539
	b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Rating: 0.5367
271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings
	271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Rating: 0.5445
	2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Rating: 0.5329
	d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Rating: 0.5316
	c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Rating: 0.5305
	8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Rating: 0.5289
9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God
	9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Rating: 0.5446
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5389
	65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Rating: 0.5387
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5355
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5344
5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback))
	5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Rating: 0.5446
	57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Rating: 0.5332
	efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Rating: 0.5329
	1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Rating: 0.5309
	24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Rating: 0.5303
4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl
	4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Rating: 0.5449
	cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Rating: 0.5329
	19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Rating: 0.5267
	15689d09-c711-4844-84d8-130a90237b26, Bel Canto Rating: 0.5245
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5235
98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories
	f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Rating: 0.5451
	98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Rating: 0.5442
	0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Rating: 0.5421
	15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Rating: 0.5409
	329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Rating: 0.54
5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover))
	5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Rating: 0.5462
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5372
	604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Rating: 0.5341
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5334
	da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5319
d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven
	d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Rating: 0.5491
	ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5401
	c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5393
	8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5382
	973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5367

</pre>


##7\. 모델 비즈니스 규칙

지원되는 규칙 유형은 다음과 같습니다.
- <strong>BlockList</strong> - 권장 사항 결과에서 반환하지 않을 항목 목록을 제공할 수 있습니다.

- <strong>FeatureBlockList</strong> - 해당 기능의 값을 기반으로 항목을 차단할 수 있습니다.

*단일 차단 목록 규칙에 1000개보다 많은 항목을 전송하지 마세요. 호출 시간 제한에 도달할 수 있습니다. 1000개보다 많은 항목을 차단할 해야 할 경우 여러 개의 차단 목록을 호출하면 됩니다.*

- <strong>Upsale</strong> - 권장 사항 결과에서 항목을 강제로 반환할 수 있습니다.

- <strong>WhiteList</strong> - 허용 목록은 항목의 목록에서 권장 사항만 제안할 수 있습니다.

- <strong>FeatureWhiteList</strong> - 기능 허용 목록은 특정 기능 값을 가진 항목만 추천할 수 있습니다.

- <strong>PerSeedBlockList</strong> - 권장 사항 결과로 반환할 수 없는 항목 목록을 항목별로 제공할 수 있습니다.




###7\.1. 모델 규칙 가져오기

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>예:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

- `feed/entry/content/properties/Id` – 이 규칙의 고유 식별자
- `feed/entry/content/properties/Type` – 규칙 유형
- `feed/entry/content/properties/Parameter` – 규칙 매개 변수

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get a list of rules for a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T12:58:57Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000043</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfRulesForAModelEntity</title>
		<updated>2014-11-05T12:58:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000044</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.2. 규칙 추가

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 
<ins>비즈니스 규칙에 대한 항목 ID를 제공할 때마다 항목의 외부 ID를 사용하도록 합니다(카탈로그 파일에서 사용한 것과 동일한 ID)</ins><br>
<ins>차단 목록 규칙을 추가하려면:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>
<ins>FeatureBlockList 규칙을 추가하려면:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>
Upsale 규칙을 추가하려면:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br> <ins>
허용 목록 규칙을 추가하려면:</ins><br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>FeatureWhiteList 규칙을 추가하려면:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>
PerSeedBlockList 규칙을 추가하려면:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|


**응답**:

HTTP 상태 코드: 200

이 API는 새로 만든 규칙을 해당 세부 정보와 함께 반환합니다. 다음 경로에서 규칙 속성을 검색할 수 있습니다.

- `feed/entry/content/properties/Id` – 이 규칙의 고유 식별자
- `feed/entry/content/properties/Type` – 규칙 유형: BlockList 또는 Upsale
- `feed/entry/content/properties/Parameter` – 규칙 매개 변수

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Add A Rule</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T11:13:28Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AddARuleEntity</title>
		<updated>2014-11-05T11:13:28Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">1000041</d:Id>
				<d:Type m:type="Edm.String">BlockList</d:Type>
				<d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
			</m:properties>
		</content>
	</entry>
	</feed>

###7\.3. 규칙 삭제

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>예:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	filterId |	필터의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

###7\.4. 모든 규칙 삭제

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>예:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

##8\. 카탈로그

###8\.1. 카탈로그 데이터 가져오기

여러 번 호출하여 여러 카탈로그 파일을 같은 모델에 업로드하면 새 카탈로그 항목만 삽입됩니다. 기존 항목은 원래 값으로 유지됩니다. 이 메서드를 사용하여 카탈로그 데이터를 업데이트할 수 없습니다.

카탈로그 데이터는 다음 형식을 따라야 합니다.

- 기능 사용 안 함 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 기능 사용 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

참고: 최대 파일 크기는 200MB입니다.

**형식 세부 정보**

| Name | 필수 | 형식 | 설명 |
|:---|:---|:---|:---|
| 항목 ID |예 | [A-z], [a-z], [0-9], [\_] &#40;밑줄&#41;, [-] &#40;대시&#41;<br> 최대 길이: 50 | 항목의 고유 식별자 |
| Item Name | 예 | 영숫자 문자<br> 최대 길이: 255 | 항목 이름 | 
| Item Category | 예 | 영숫자 문자 <br> 최대 길이: 255 | 이 항목이 속하는 범주(예: 요리책, 드라마...). 비어 있을 수 있습니다. |
| 설명 | 아니요, 기능이 없는 경우(그러나 비어 있을 수 있음) | 영숫자 문자 <br> 최대 길이: 4000 | 이 항목에 대한 설명 |
| Features list | 아니요 | 영숫자 문자 <br> 최대 길이: 4000; 최대 기능 수: 20 | 쉼표로 구분된 기능 이름 목록 = 모델 권장 사항을 향상 시키기는 데 사용할 수 있는 기능 값; [고급 항목](#2-advanced-topics) 섹션을 참조하세요. |


| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
| filename | 카탈로그의 텍스트 식별자.<br>문자(A-Z, a-z), 숫자(0-9), 하이픈(-) 및 밑줄(\_)만 사용할 수 있습니다.<br>최대 길이: 50 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 예(기능 사용):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |


**응답**:

HTTP 상태 코드: 200

API는 가져오기 보고서를 반환합니다.
- `feed\entry\content\properties\LineCount` – 허용되는 줄 수
- `feed\entry\content\properties\ErrorCount` – 오류로 인해 삽입되지 않은 줄 수

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Import catalog file</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-10-05T06:58:04Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
	<entry>
   	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ImportCatalogFileEntity2</title>
		<updated>2014-10-05T06:58:04Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:LineCount m:type="Edm.String">4</d:LineCount>
				<d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.2. 카탈로그 가져오기
모든 카탈로그 항목을 검색합니다. 한 번에 한 페이지씩 카탈로그가 검색됩니다. 특정 인덱스에서 항목을 가져오려는 경우 $skip odata 매개 변수를 사용할 수 있습니다. 예를 들어 100 위치에서 시작하는 항목을 가져오려면 $skip=100 매개 변수를 요청에 추가합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>예:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

응답은 카탈로그 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `feed/entry/content/properties/ExternalId` – 카탈로그 항목 외부 ID, 고객이 제공
- `feed/entry/content/properties/InternalId` – 카탈로그 항목 내부 ID, Azure 기계 학습 권장 사항에서 생성
- `feed/entry/content/properties/Name` – 카탈로그 항목 이름
- `feed/entry/content/properties/Category` – 카탈로그 항목 범주
- `feed/entry/content/properties/Description` – 카탈로그 항목 설명
- `feed/entry/content/properties/Metadata` – 카탈로그 항목 메타데이터


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get All Catalog Items</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
		<entry>
        	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
				<d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
				<d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
				<d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
				<d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
				<d:Name m:type="Edm.String">Spadework</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">AllCatalogItemsEntity</title>
		<updated>2014-10-29T11:13:26Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
				<d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
				<d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
				<d:Category m:type="Edm.String">Book</d:Category>
				<d:Description m:type="Edm.String"></d:Description>
				<d:Metadata m:type="Edm.String"></d:Metadata>
			</m:properties>
		</content>
	</entry>
	</feed>

###8\.3. 토큰별 카탈로그 항목 가져오기

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>예:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
|	token |	카탈로그 항목 이름의 토큰 3자 이상 포함해야 합니다. |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

응답은 카탈로그 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `feed/entry/content/properties/InternalId` – 카탈로그 항목 내부 ID, Azure 기계 학습 권장 사항에서 생성
- `feed/entry/content/properties/Name` – 카탈로그 항목 이름
- `feed/entry/content/properties/Rating` - (나중에 사용)
- `feed/entry/content/properties/Reasoning` - (나중에 사용)
- `feed/entry/content/properties/Metadata` - (나중에 사용)
- `feed/entry/content/properties/FormattedRating` - (나중에 사용)

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get Catalog items that contain a token</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-29T11:48:19Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">CatalogItemsThatContainATokenEntity</title>
			<updated>2014-10-29T11:48:19Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    		<content type="application/xml">
      			<m:properties>
					<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
        			<d:Name m:type="Edm.String">Clara Callan</d:Name>
        			<d:Rating m:type="Edm.Double">0</d:Rating>
        			<d:Reasoning m:type="Edm.String"></d:Reasoning>
        			<d:Metadata m:type="Edm.String"></d:Metadata>
        			<d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
      			</m:properties>
			</content>
		</entry>
	</feed>

##9\. 사용 데이터
###9\.1. 사용 데이터 가져오기
####9\.1.1. 파일 업로드
이 섹션에서는 파일을 사용하여 사용 데이터를 업로드하는 방법을 보여 줍니다. 사용 데이터를 사용하여 이 API를 여러 번 호출할 수 있습니다. 모든 호출에 대한 모든 사용 데이터가 저장됩니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
| filename | 카탈로그의 텍스트 식별자.<br>문자(A-Z, a-z), 숫자(0-9), 하이픈(-) 및 밑줄(\_)만 사용할 수 있습니다.<br>최대 길이: 50 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 사용 데이터. 형식:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>이름</th><th>필수</th><th>형식</th><th>설명</th></tr><tr><td>사용자 ID</td><td>예</td><td>[a-z], [a-z], [0-9], [\_] &#40;Underscore&#41;, [-] &#40;Dash&#41;<br> 최대 길이: 255 </td><td>사용자의 고유 식별자입니다.</td></tr><tr><td>항목 ID</td><td>예</td><td>[a-z], [a-z], [0-9], [&#95;] &#40;Underscore&#41;, [-] &#40;Dash&#41;<br> 최대 길이: 50</td><td>항목의 고유 식별자입니다.</td></tr><tr><td>시간</td><td>No</td><td>날짜 형식: YYYY/MM/DDTHH:MM:SS (예: 2013/06/20T10:00:00)</td><td>데이터의 시간.</td></tr><tr><td>이벤트</td><td>아니요; 제공되면 그 다음 날짜를 삽입해야 합니다</td><td>다음 중 하나:<br>• 클릭<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• 구매</td><td></td></tr></table><br>최대 파일 크기: 200MB<br><br>예제:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**응답**:

HTTP 상태 코드: 200

- `Feed\entry\content\properties\LineCount` – 허용되는 줄 수
- `Feed\entry\content\properties\ErrorCount` – 오류로 인해 삽입되지 않은 줄 수
- `Feed\entry\content\properties\FileId` – 파일 식별자

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####9\.1.2. 데이터 취득 사용
이 섹션에서는 일반적으로 웹 사이트에서 Azure 기계 학습 권장 사항에 실시간으로 이벤트를 전송하는 방법을 보여 줍니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	apiVersion | 1\.0 |
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
					<PurchaseItem>
						<ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
						<Count>1</Count>
					</PurchaseItem>
					<PurchaseItem>
						<ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
						<Count>3</Count>
					</PurchaseItem>
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

###9\.2. 모델 사용 파일 나열
모든 모델 사용 파일의 메타데이터를 검색합니다. 한 번에 한 페이지씩 사용 파일이 검색됩니다. 각 페이지는 100개의 항목을 포함합니다. 특정 인덱스에서 항목을 가져오려는 경우 $skip odata 매개 변수를 사용할 수 있습니다. 예를 들어 100 위치에서 시작하는 항목을 가져오려면 $skip=100 매개 변수를 요청에 추가합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	forModelId |	모델의 고유 식별자 |
|	apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

응답은 사용 파일당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `feed\entry\content\properties\Id` – 사용 파일 ID
- `feed\entry\content\properties\Length` – 사용 파일 길이(MB)
- `feed\entry\content\properties\DateModified` – 사용 파일이 만들어진 날짜
- `feed\entry\content\properties\UseInModel` – 사용 파일이 모델에서 사용되는지 여부

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of model's usage files info</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
			<updated>2014-10-30T09:40:25Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
					<d:Length m:type="Edm.Double">0</d:Length>
					<d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
					<d:UseInModel m:type="Edm.String">true</d:UseInModel>
				</m:properties>
			</content>
		</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
		<updated>2014-10-30T09:40:25Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
				<d:Length m:type="Edm.Double">0.001</d:Length>
				<d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
				<d:UseInModel m:type="Edm.String">true</d:UseInModel>
          	</m:properties>
		</content>
	</entry>
</feed>

###9\.3. 사용 통계 가져오기
사용 통계를 가져옵니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| startDate |	시작 날짜입니다. 형식: yyyy/MM/ddTHH:mm:ss |
| endDate |	종료 날짜입니다. 형식: yyyy/MM/ddTHH:mm:ss |
| eventTypes |	쉼표로 구분된 이벤트 유형 문자열이거나 null(모든 이벤트를 가져오려는 경우) |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

키/값 요소의 컬렉션입니다. 각 컬렉션은 시간별로 그룹화된 특정 이벤트 유형에 대한 이벤트 합계를 포함합니다.

- `feed\entry[i]\content\properties\Key` – 시간(시간별로 그룹화) 및 이벤트 유형 포함
- `feed\entry[i]\content\properties\Value` - 총 이벤트 수

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-18T11:39:16Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
				<d:Value m:type="Edm.String">5</d:Value>
			</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
          	</m:properties>
		</content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
            	<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
        </content>
	</entry>
	<entry>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
		<title type="text">GetUsageStatisticsEntity</title>
		<updated>2014-11-18T11:39:16Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
				<d:Value m:type="Edm.String">10</d:Value>
			</m:properties>
		</content>
	</entry>
	</feed>

###9\.4. 사용 파일 샘플 가져오기
사용 파일 내용의 처음 2KB를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| fileId |	모델 사용 파일의 고유 식별자 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

원시 텍스트 형식으로 응답이 반환됩니다.
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
</pre>


###9\.5. 모델 사용 파일 가져오기
사용 파일의 전체 내용을 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| mId |	모델의 고유 식별자 |
| fid |	모델 사용 파일의 고유 식별자 |
| 다운로드 | 1 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

원시 텍스트 형식으로 응답이 반환됩니다.
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1
274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1
171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
</pre>

###9\.6. 사용 파일 삭제
지정한 모델 사용 파일을 삭제합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| 매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| fileId | 삭제할 파일의 고유 식별자 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200


###9\.7. 모든 사용 파일 삭제
모든 모델 사용 파일을 삭제합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| 매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

##10\. 기능
이 섹션에서는 가져온 기능과 해당 값, 해당 순위 및 이 순위가 할당된 시점 등 기능 정보를 검색하는 방법을 보여 줍니다. 기능은 카탈로그 데이터의 일부로 가져오며, 순위 빌드가 완료되면 해당 순위가 연결됩니다.
기능 순위는 사용 데이터의 패턴 및 항목 유형에 따라 변경될 수 있습니다. 그러나 일관된 사용/항목을 위해 순위는 조금만 변동되어야 합니다.
기능의 순위는 음수가 아닌 숫자입니다. 숫자 0은 기능의 순위가 매겨지지 않았음을 의미합니다(첫 번째 순위 빌드가 완료되기 전에 이 API를 호출한 경우에 발생). 순위가 지정된 날짜를 점수 유효 시간이라고 합니다.

###10\.1. 기능 정보 가져오기(마지막 순위 빌드)
마지막으로 성공한 순위 빌드에 대해 순위를 포함한 기능 정보를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| 매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
|samplingSize| 카탈로그에 있는 데이터에 따라 각 기능에 대해 포함할 값 수 <br/>가능한 값은 다음과 같습니다.<br> -1 - 모든 샘플 <br>0 - 샘플링 없음 <br>N - 각 기능 이름별로 N개의 샘플 반환|
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |


**응답**:

HTTP 상태 코드: 200

응답은 기능 정보 항목의 목록을 포함합니다. 각 항목에는 다음이 포함됩니다.

- `feed/entry/content/m:properties/d:Name` - 기능 이름
- `feed/entry/content/m:properties/d:RankUpdateDate` - 이 기능에 순위가 할당된 날짜(점수 유효 시간이라고도 함). 과거 날짜('0001-01-01T00:00:00')는 순위 빌드가 수행되지 않았음을 나타냅니다.
- `feed/entry/content/m:properties/d:Rank` - 기능 순위(부동 소수점). 2.0 이상의 순위가 유용한 기능으로 간주됩니다.
- `feed/entry/content/m:properties/d:SampleValues` - 요청한 샘플링 크기까지의 쉼표로 구분된 값 목록

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get the features of a model</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-01-08T13:15:02Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Author</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Name m:type="Edm.String">Publisher</d:Name>
				<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
				<d:Rank m:type="Edm.String">0</d:Rank>
				<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
			</m:properties>
		</content>
	</entry>
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
		<title type="text">ModelFeaturesEntity</title>
		<updated>2015-01-08T13:15:02Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
		<contenttype="application/xml">
		<m:properties>
			<d:Name m:type="Edm.String">Year</d:Name>
			<d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
			<d:Rank m:type="Edm.String">0</d:Rank>
			<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
		</m:properties>
		</content>
	</entry>
</feed>


###10\.2. 기능 정보 가져오기(특정 순위 빌드)

특정 순위 빌드에 대해 순위를 포함한 기능 정보를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| 매개 변수 이름 |	유효한 값 |
|:--------			|:--------			|
| modelId |	모델의 고유 식별자 |
|samplingSize| 카탈로그에 있는 데이터에 따라 각 기능에 대해 포함할 값 수<br/> 가능한 값은 다음과 같습니다.<br> -1 - 모든 샘플 <br>0 - 샘플링 없음 <br>N - 각 기능 이름별로 N개의 샘플 반환|
|rankBuildId| 순위 빌드의 고유 식별자 또는 마지막 순위 빌드의 경우 -1|
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |


**응답**:

HTTP 상태 코드: 200

응답은 기능 정보 항목의 목록을 포함합니다. 각 항목에는 다음이 포함됩니다.

- `feed/entry/content/m:properties/d:Name` - 기능 이름
- `feed/entry/content/m:properties/d:RankUpdateDate` - 이 기능에 순위가 할당된 날짜(점수 유효 시간이라고도 함). 과거 날짜('0001-01-01T00:00:00')는 순위 빌드가 수행되지 않았음을 나타냅니다.
- `feed/entry/content/m:properties/d:Rank` - 기능 순위(부동 소수점). 2.0 이상의 순위가 유용한 기능으로 간주됩니다.
- `feed/entry/content/m:properties/d:SampleValues` - 요청한 샘플링 크기까지의 쉼표로 구분된 값 목록

OData

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get the features of a model</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:54:22Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Author</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">3.38867426</d:Rank>
					<d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Publisher</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.67839336</d:Rank>
					<d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">ModelFeaturesEntity</title>
			<updated>2015-01-08T13:54:22Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Name m:type="Edm.String">Year</d:Name>
					<d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
					<d:Rank m:type="Edm.String">1.12352145</d:Rank>
					<d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
				</m:properties>
			</content>
		</entry>
	</feed>


##11\. 빌드

  이 섹션에서는 빌드와 관련된 다른 API를 설명합니다. 세 가지 유형의 빌드(권장 사항 빌드, 순위 빌드 및 FBT(자주 함께 구매됨) 빌드)가 있습니다.

권장 빌드는 예측에 사용되는 권장 사항 모델을 생성하는 데 사용됩니다. 예측(이 유형의 빌드 관련)은 다음 두 가지 유형으로 제공됩니다.
* I2I (항목-항목 권장 사항) - 항목 또는 항목 목록이 제공될 경우 이 옵션은 가장 높은 관심을 받을 항목 목록을 예측합니다.
* U2I (사용자-항목 권장 사항) - 사용자 ID(필요에 따라 항목 목록)가 제공될 경우 이 옵션은 지정된 사용자(및 추가 선택 항목)에 대해 가장 높은 관심을 받을 항목 목록을 예측합니다. U2I 권장 사항은 모델이 작성되었을 때까지 사용자에 대해 관심을 받은 항목의 기록을 기반으로 합니다.

순위 빌드는 기능의 유용성에 대해 알아볼 수 있는 기술 빌드입니다. 일반적으로 기능과 관련된 권장 사항 모델에 대한 최상의 결과를 얻으려면 다음 단계를 수행해야 합니다.
- 순위 빌드를 트리거(기능 점수가 안정적이지 않은 경우)하고 기능 점수를 얻을 때까지 기다립니다.
- [Get Features Info](#101-get-features-info-for-last-rank-build) API를 호출하여 기능의 순위를 검색합니다.
- 다음 매개 변수를 사용하여 권장 사항 빌드를 구성합니다.
	- `useFeatureInModel` - True로 설정합니다.
	- `ModelingFeatureList` - 이전 단계에서 검색한 순위에 따라 점수가 2.0 이상인 기능의 쉼표로 구분된 목록으로 설정합니다.
	- `AllowColdItemPlacement` - True로 설정합니다.
	- 필요한 경우 `EnableFeatureCorrelation`을 True로 설정하고, `ReasoningFeatureList`을(를) 설명에 사용할 기능 목록(일반적으로 모델링 또는 하위 목록에 사용되는 동일한 기능 목록)으로 설정할 수 있습니다.
- 구성된 매개 변수를 사용하여 권장 사항 빌드를 트리거합니다.

참고: 매개 변수를 구성하지 않은 경우(예: 매개 변수 없이 권장 사항 빌드를 호출한 경우) 또는 기능 사용을 명시적으로 해제하지 않은 경우(예: `UseFeatureInModel`을 False로 설정) 순위 빌드가 있으면 기능 관련 매개 변수가 위에 설명된 값으로 자동으로 설정됩니다.

같은 모델에 대해 순위 빌드와 권장 사항 빌드를 동시에 실행하는 것에 대한 제한 사항은 없습니다. 그러나 같은 모델에서 동일한 유형의 두 빌드를 동시에 실행할 수는 없습니다.

FBT(자주 함께 구매됨) 빌드는 유형이 다른(같은 유형: 책, 영화, 일부 음식, 패션, 다른 유형: 컴퓨터와 장치, 크로스 도메인, 고도로 다양) 카탈로그에 적합한 “보수적인" 추천이라고도 하는 다른 권장 사항 알고리즘입니다.

참고: 업로드한 사용 파일에 선택적 필드 "event type"이 포함된 경우에는 FBT 모델링에 "Purchase" 이벤트만 사용됩니다. 이벤트 유형이 제공되지 않은 경우에는 모든 이벤트가 구매로 간주됩니다.


####11\.1 빌드 매개 변수

각 빌드 형식은 매개 변수 집합(아래 설명)을 통해 구성될 수 있습니다. 매개 변수를 구성하지 않으면 빌드를 트리거할 때 존재한 정보에 따라 매개 변수 값이 자동으로 할당됩니다.

#####11\.1.1. 사용 콘덴서
사용 포인트가 거의 없는 사용자 또는 항목은 정보보다 노이즈를 더 많이 포함할 수 있습니다. 시스템에서는 모델에서 사용할 사용자/항목당 최소 사용 포인트 수를 예측합니다. 이 수는 항목의 경우 ItemCutoffLowerBound 및 ItemCutoffUpperBound 매개 변수로 정의되는 범위 내에 속하며, 사용자의 경우 UserCutOffLowerBound 및 UserCutoffUpperBound 매개 변수로 정의되는 범위 내에 속합니다. 항목 또는 사용자에 대한 콘덴서 효과는 해당 경계 중 하나 이상을 0으로 설정하여 최소화할 수 있습니다.

#####11\.1.2. 순위 빌드 매개 변수

아래 표에서는 순위 빌드에 대한 빌드 매개 변수를 설명합니다.

|키|설명|형식|유효한 값|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 모델에서 수행하는 반복 횟수는 전체 계산 시간과 모델 정확도로 반영됩니다. 숫자가 높을수록 정확도는 높지만 계산 시간이 더 오래 걸립니다.| Integer | 10-50 |
| NumberOfModelDimensions | 차원 수는 모델이 데이터 내에서 찾으려는 '기능' 수와 관련이 있습니다. 차원 수를 늘리면 결과를 더 작은 클러스터로 미세 조정할 수 있습니다. 그러나 차원 수가 너무 많으면 모델이 항목 간의 상관 관계를 찾지 못하게 됩니다. | Integer | 10-40 |
|ItemCutOffLowerBound| 콘덴서의 항목 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|ItemCutOffUpperBound| 콘덴서의 항목 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffLowerBound| 콘덴서의 사용자 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffUpperBound| 콘덴서의 사용자 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |

#####11\.1.3. 권장 사항 빌드 매개 변수
아래 표에서는 권장 사항 빌드에 대한 빌드 매개 변수를 설명합니다.

|키|설명|형식|유효한 값|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 모델에서 수행하는 반복 횟수는 전체 계산 시간과 모델 정확도로 반영됩니다. 숫자가 높을수록 정확도는 높지만 계산 시간이 더 오래 걸립니다.| Integer | 10-50 |
| NumberOfModelDimensions | 차원 수는 모델이 데이터 내에서 찾으려는 '기능' 수와 관련이 있습니다. 차원 수를 늘리면 결과를 더 작은 클러스터로 미세 조정할 수 있습니다. 그러나 차원 수가 너무 많으면 모델이 항목 간의 상관 관계를 찾지 못하게 됩니다. | Integer | 10-40 |
|ItemCutOffLowerBound| 콘덴서의 항목 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|ItemCutOffUpperBound| 콘덴서의 항목 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffLowerBound| 콘덴서의 사용자 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffUpperBound| 콘덴서의 사용자 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
| 설명 | 빌드 설명 | String | 모든 텍스트, 최대 512자 |
| EnableModelingInsights | 권장 사항 모델에 대한 메트릭을 계산할 수 있습니다. | Boolean | True/False |
| UseFeaturesInModel | 기능을 사용하여 권장 사항 모델을 개선할 수 있는지 나타냅니다. | Boolean | True/False |
| ModelingFeatureList | 권장 사항을 개선하기 위해 권장 사항 빌드에서 사용할 기능 이름의 쉼표로 구분된 목록입니다. | String | 기능 이름, 최대 512자 |
| AllowColdItemPlacement | 권장 사항에서 기능 유사성을 통해 콜드 항목을 푸시해야 하는지 나타냅니다. | Boolean | True/False |
| EnableFeatureCorrelation | 추론에서 기능을 사용할 수 있는지 나타냅니다. | Boolean | True/False |
| ReasoningFeatureList | 추론 문장(예: 권장 사항 설명)에 사용할 기능 이름의 쉼표로 구분된 목록입니다. | String | 기능 이름, 최대 512자 |
| EnableU2I | U2I(사용자-항목 권장 사항)라고도 하는 개인 설정된 권장 구성을 허용합니다. | Boolean | True/False(기본값 true) |

#####11\.1.4. FBT 빌드 매개 변수
아래 표에서는 권장 사항 빌드에 대한 빌드 매개 변수를 설명합니다.

|키|설명|형식|유효한 값(기본값)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | 모델의 보수적인 정도입니다. 모델링 시 고려할 항목의 공동 발생 횟수입니다.| Integer | 3-50 (6) |
|FbtMaxItemSetSize | FBT 집합의 항목 수를 제한합니다.| Integer | 2-3(2) |
|FbtMinimalScore | 반환된 결과에 포함하기 위해 필요한 FBT 집합의 최소 점수입니다. 높을수록 좋습니다.| Double | 0 이상(0) |
|FbtSimilarityFunction | 빌드에서 사용할 유사성 함수를 정의합니다. Lift는 우연성을 우위에 두고, Co-occurrence는 예측 가능성을 우위에 두며, Jaccard는 이 둘을 적절히 절충합니다. | String | cooccurrence, lift, jaccard (lift) |


###11\.2. 권장 사항 빌드 트리거

  기본적으로 이 API는 권장 사항 모델 빌드를 트리거합니다. 기능의 점수를 매기기 위해 순위 빌드를 트리거하려면 빌드 형식 매개 변수가 있는 빌드 API 변형을 사용해야 합니다.


| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`(요청 본문을 보내는 경우)|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| userDescription | 카탈로그의 텍스트 식별자입니다. 공백을 사용하는 경우 대신 %20을 사용하여 인코드해야 합니다. 위 예제를 참조하세요.<br>최대 길이: 50 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 왼쪽이 비어 있으면 빌드가 기본 빌드 매개 변수로 실행됩니다.<br><br>빌드 매개 변수를 설정하려면 다음 샘플과 같이 매개 변수를 본문에 XML로 보내야 합니다. 매개 변수에 대한 설명은 "빌드 매개 변수" 섹션을 참조하세요.`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**응답**:

HTTP 상태 코드: 200

비동기 API입니다. 응답으로 빌드 ID가 제공됩니다. 빌드가 종료된 시점을 알려면 "모델의 빌드 상태 가져오기" API를 호출하여 응답에서 이 빌드 ID를 찾습니다. 데이터 크기에 따라 빌드를 가져오는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.

빌드가 종료될 때까지 권장 사항을 소비할 수 없습니다.

유효한 빌드 상태:

- Create - 빌드 요청이 만들어짐
- Queued – 빌드 요청을 보냈으며 큐에 대기됨
- Building – 빌드가 진행 중임
- Success – 빌드가 성공적으로 종료됨
- Error – 오류로 인해 빌드가 종료됨
- Cancelled – 빌드가 취소됨
- Cancelling – 빌드에 대한 취소 요청을 보냄


다음 경로에서 빌드 ID를 찾을 수 있습니다. `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###11\.3. 빌드(권장 사항, 순위 또는 FBT) 트리거

| HTTP 메서드 | URI |
|:--------|:--------|
|POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|HEADER |`"Content-Type", "text/xml"`(요청 본문을 보내는 경우)|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId |	모델의 고유 식별자 |
| userDescription | 카탈로그의 텍스트 식별자입니다. 공백을 사용하는 경우 대신 %20을 사용하여 인코드해야 합니다. 위 예제를 참조하세요.<br>최대 길이: 50 |
| buildType | 호출할 빌드의 형식: <br/> - 권장 사항 빌드의 경우 'Recommendation' <br> - 순위 빌드의 경우 'Ranking' <br/> - FBT 빌드의 경우 'Fbt'
| apiVersion | 1\.0 |
|||
| 요청 본문 | 왼쪽이 비어 있으면 빌드가 기본 빌드 매개 변수로 실행됩니다.<br><br>빌드 매개 변수를 설정하려면 다음 샘플과 같이 매개 변수를 본문에 XML로 보내야 합니다. 매개 변수에 대한 설명 및 매개 변수 전체 목록을 보려면 "빌드 매개 변수" 섹션을 참조하세요.`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

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

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###11\.4. 모델의 빌드 상태 가져오기
지정된 모델의 빌드 및 해당 상태를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	modelId |	모델의 고유 식별자 |
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
- Rank - 순위 빌드
- Recommendation - 권장 사항 빌드


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a model</subtitle>
    	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T17:51:10Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
		<entry>
    		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T17:51:10Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.5. 빌드 상태 가져오기
사용자의 모든 모델에 대한 빌드 상태를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
|	onlyLastBuild |	모델의 빌드 기록을 모두 반환할지 또는 최신 빌드의 상태만 반환할지를 나타냅니다. |
|	apiVersion |	1\.0 |


**응답**:

HTTP 상태 코드: 200

응답은 빌드당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.

- `feed/entry/content/properties/UserName` - 사용자의 이름
- `feed/entry/content/properties/ModelName` - 모델의 이름
- `feed/entry/content/properties/ModelId` – 모델의 고유 식별자
- `feed/entry/content/properties/IsDeployed` – 빌드 배포 여부
- `feed/entry/content/properties/BuildId` – 빌드의 고유 식별자
- `feed/entry/content/properties/BuildType` - 빌드의 형식
- `feed/entry/content/properties/Status` – 빌드 상태 다음 중 하나일 수 있음: Error, Building, Queued, Cancelled, Cancelling, Success
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
- Rank - 순위 빌드
- Recommendation - 권장 사항 빌드


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get builds status of a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-05T18:41:21Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildsStatusEntity</title>
			<updated>2014-11-05T18:41:21Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###11\.6. 빌드 삭제
빌드를 삭제합니다.

참고: <br>활성 빌드는 삭제할 수 없습니다. 활성 빌드를 삭제하려면 먼저 모델을 다른 활성 빌드로 업데이트해야 합니다.<br>진행 중인 빌드는 삭제할 수 없습니다. 먼저 <strong>빌드 취소</strong>를 호출하여 빌드를 취소해야 합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| buildId | 빌드의 고유 식별자 |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200

###11\.7. 빌드 취소
빌드 중 상태인 빌드를 취소합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| buildId | 빌드의 고유 식별자 |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200

###11\.8. 빌드 매개 변수 가져오기
빌드 매개 변수를 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| buildId | 빌드의 고유 식별자 |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200

이 API는 키/값 요소의 컬렉션을 반환합니다. 각 요소는 매개 변수와 해당 값을 나타냅니다.
- `feed/entry/content/properties/Key` - 빌드 매개 변수 이름.
- `feed/entry/content/properties/Value` - 빌드 매개 변수 값.

아래 표에서는 각 키가 나타내는 값을 설명합니다.

|키|설명|형식|유효한 값|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 모델에서 수행하는 반복 횟수는 전체 계산 시간과 모델 정확도로 반영됩니다. 숫자가 높을수록 정확도는 높지만 계산 시간이 더 오래 걸립니다.| Integer | 10-50 |
| NumberOfModelDimensions | 차원 수는 모델이 데이터 내에서 찾으려는 '기능' 수와 관련이 있습니다. 차원 수를 늘리면 결과를 더 작은 클러스터로 미세 조정할 수 있습니다. 그러나 차원 수가 너무 많으면 모델이 항목 간의 상관 관계를 찾지 못하게 됩니다. | Integer | 10-40 |
|ItemCutOffLowerBound| 콘덴서의 항목 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|ItemCutOffUpperBound| 콘덴서의 항목 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffLowerBound| 콘덴서의 사용자 하한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
|UserCutOffUpperBound| 콘덴서의 사용자 상한을 정의합니다. 위의 사용 콘덴서를 참조하세요. | Integer | 2 이상(0 - 콘덴서 사용 안 함) |
| 설명 | 빌드 설명 | String | 모든 텍스트, 최대 512자 |
| EnableModelingInsights | 권장 사항 모델에 대한 메트릭을 계산할 수 있습니다. | Boolean | True/False |
| UseFeaturesInModel | 기능을 사용하여 권장 사항 모델을 개선할 수 있는지 나타냅니다. | Boolean | True/False |
| ModelingFeatureList | 권장 사항을 개선하기 위해 권장 사항 빌드에서 사용할 기능 이름의 쉼표로 구분된 목록입니다. | String | 기능 이름, 최대 512자 |
| AllowColdItemPlacement | 권장 사항에서 기능 유사성을 통해 콜드 항목을 푸시해야 하는지 나타냅니다. | Boolean | True/False |
| EnableFeatureCorrelation | 추론에서 기능을 사용할 수 있는지 나타냅니다. | Boolean | True/False |
| ReasoningFeatureList | 추론 문장(예: 권장 사항 설명)에 사용할 기능 이름의 쉼표로 구분된 목록입니다. | String | 기능 이름, 최대 512자 |


OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get build parameters</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2015-01-08T13:50:57Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
					<d:Value m:type="Edm.String">False</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
			<titletype="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
					<d:Value m:type="Edm.String">10</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
					<d:Value m:type="Edm.String">2</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
					<d:Value m:type="Edm.String">2147483647</d:Value>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
					<d:Value m:type="Edm.String"/>
				</m:properties>
			</content>
		</entry>
		<entry>
			<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
			<title type="text">GetBuildParametersEntity</title>
			<updated>2015-01-08T13:50:57Z</updated>
			<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:Key m:type="Edm.String">Description</d:Key>
					<d:Value m:type="Edm.String">rankBuild</d:Value>
				</m:properties>
			</content>
		</entry>
	</feed>

##12\. 권장 사항
###12\.1. 항목 권장 사항 가져오기(활성 빌드)

활성 빌드 형식이 "Recommendation" 또는 시드(입력) 항목 목록을 기반으로 하는 "Fbt"인 권장 사항을 가져옵니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| itemIds | 권장할 항목의 쉼표로 구분된 목록입니다. <br>활성 빌드가 FBT 형식인 경우에는 항목을 하나만 보낼 수 있습니다. <br>최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 <br>최대: 150 |
| includeMetatadata | 나중에 사용, 항상 false |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

아래 예제 응답은 10개의 권장 항목을 포함합니다.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###12\.2. 항목 권장 사항 가져오기(특정 빌드)

특정 빌드 형식 "Recommendation" 또는 "Fbt"의 권장 사항을 가져옵니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| itemIds | 권장할 항목의 쉼표로 구분된 목록입니다. <br>활성 빌드가 FBT 형식인 경우에는 항목을 하나만 보낼 수 있습니다. <br>최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 <br>최대: 150 |
| includeMetatadata | 나중에 사용, 항상 false
| buildId | 이 권장 사항 요청에 사용할 빌드 ID |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.1의 응답 예제 참조

###12\.3. FBT 권장 사항 가져오기(활성 빌드)

활성 빌드 형식이 시드(입력) 항목 목록을 기반으로 하는 "Fbt"인 권장 사항을 가져옵니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| itemId | 권장할 항목입니다. <br>최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 <br>최대: 150 |
| minimalScore | 반환된 결과에 포함하기 위해 필요한 FBT 집합의 최소 점수입니다. |
| includeMetatadata | 나중에 사용, 항상 false |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목 집합(일반적으로 시드/입력 항목과 함께 구매하는 항목 집합)당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id1` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name1` - 항목의 이름.
- `Feed\entry\content\properties\Id2` - 두 번째 권장된 항목 ID(선택 사항).
- `Feed\entry\content\properties\Name2` – 두 번째 항목의 이름(선택 사항).
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

아래 예제 응답은 3개의 권장 항목 집합을 포함합니다.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
		<d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
		<d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###12\.4. FBT 권장 사항 가져오기(특정 빌드)

특정 빌드 유형 "Fbt"의 권장 사항 가져오기

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| itemId | 권장할 항목입니다. <br>최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 <br>최대: 150 |
| minimalScore | 반환된 결과에 포함하기 위해 필요한 FBT 집합의 최소 점수입니다. |
| includeMetatadata | 나중에 사용, 항상 false |
| buildId | 이 권장 사항 요청에 사용할 빌드 ID |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목 집합(일반적으로 시드/입력 항목과 함께 구매하는 항목 집합)당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id1` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name1` - 항목의 이름.
- `Feed\entry\content\properties\Id2` - 두 번째 권장된 항목 ID(선택 사항).
- `Feed\entry\content\properties\Name2` – 두 번째 항목의 이름(선택 사항).
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.3의 응답 예제 참조

###12\.5. 사용자 권장 사항 가져오기(활성 빌드)

빌드 형식이 활성 빌드로 표시된 "Recommendation"인 사용자 권장 사항을 가져옵니다.

API는 사용자의 사용 기록에 따라 예측된 항목의 목록을 반환합니다.

참고:
 1. FBT 빌드에 대한 사용자 권장 사항은 없습니다.
 2. 활성 빌드가 FBT이면 이 메서드는 오류를 반환합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| userId | 사용자의 고유 식별자 |
| numberOfResults | 필요한 결과 수 |
| includeMetatadata | 나중에 사용, 항상 false |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.1의 응답 예제 참조

###12\.6. 항목 목록이 있는 사용자 권장 사항 가져오기(활성 빌드)

추가 항목 목록이 있으며 빌드 형식이 활성 빌드로 표시된 "Recommendation"인 사용자 권장 사항을 가져옵니다.

API는 사용자의 사용 기록에 따른 예측된 항목의 목록과 제공된 추가 항목을 반환합니다.

참고:
 1. FBT 빌드에 대한 사용자 권장 사항은 없습니다.
 2. 활성 빌드가 FBT이면 이 메서드는 오류를 반환합니다.


| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| userId | 사용자의 고유 식별자 |
| itemsIds | 권장할 항목의 쉼표로 구분된 목록입니다. 최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 |
| includeMetatadata | 나중에 사용, 항상 false |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.1의 응답 예제 참조

###12\.7. 사용자 권장 사항 가져오기(특정 빌드)

특정 빌드 형식이 "Recommendation"인 사용자 권장 사항을 가져옵니다.

API는 사용자의 사용 기록에 따라 예측된 항목의 목록을 반환합니다(특정 빌드에 사용).

참고: FBT 빌드에 대한 사용자 권장 사항은 없습니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| userId | 사용자의 고유 식별자 |
| numberOfResults | 필요한 결과 수 |
| includeMetatadata | 나중에 사용, 항상 false |
| buildId | 이 권장 사항 요청에 사용할 빌드 ID |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.1의 응답 예제 참조


###12\.8. 항목 목록이 있는 사용자 권장 사항 가져오기(특정 빌드)

특정 빌드 형식이 "Recommendation"인 사용자 권장 사항과 추가 항목 목록을 가져옵니다.

API는 사용자의 사용 기록에 따른 예측된 항목 목록과 추가 항목 목록을 반환합니다.

참고: FBT 빌드에 대한 사용자 권장 사항은 없습니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| userId | 사용자의 고유 식별자 |
| itemIds | 권장할 항목의 쉼표로 구분된 목록입니다. 최대 길이: 1024 |
| numberOfResults | 필요한 결과 수 |
| includeMetatadata | 나중에 사용, 항상 false |
| buildId | 이 권장 사항 요청에 사용할 빌드 ID |
| apiVersion | 1\.0 |

**응답:**

HTTP 상태 코드: 200


응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 권장 사항의 등급(숫자가 클수록 신뢰도가 높음).
- `Feed\entry\content\properties\Reasoning` - 권장 사항 추론(예: 권장 사항 설명).

12\.1의 응답 예제 참조

##13\. 사용자 사용 기록
권장 모델이 작성되면 시스템은 작성에 사용된 사용자 기록(특정 사용자와 관련된 항목)을 검색할 수 있습니다.
이 API를 통해 사용자 기록을 검색할 수 있습니다.

참고: 이 사용자 기록은 현재 권장 사항 작성에만 사용할 수 있습니다.

###13\.1 사용자 기록 검색
활성 빌드 또는 지정된 사용자 ID에 대해 지정된 빌드에 사용되는 항목 목록을 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET | 활성 빌드에 대한 사용자 기록을 가져오기<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>지정된 빌드에 대한 사용자 기록 가져오기 `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>예제:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자입니다.|
| userId | 사용자의 고유 식별자입니다.
| buildId | 사용자 기록을 가져올 원본 빌드를 나타내기 위한 선택적 매개 변수입니다.
| apiVersion | 1\.0 |


**응답:**

HTTP 상태 코드: 200

응답은 권장 항목당 하나의 항목을 포함합니다. 각 항목에는 다음과 같은 데이터가 있습니다.
- `Feed\entry\content\properties\Id` - 권장된 항목 ID.
- `Feed\entry\content\properties\Name` - 항목의 이름.
- `Feed\entry\content\properties\Rating` - 해당 없음.
- `Feed\entry\content\properties\Reasoning` - 해당 없음.

OData XML

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get User History</subtitle>
	<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2015-05-26T15:32:47Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
	<entry>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
		<title type="text">CatalogItemsThatContainATokenEntity</title>
		<updated>2015-05-26T15:32:47Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
		<content type="application/xml">
			<m:properties>
				<d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
				<d:Name m:type="Edm.String">Clara Callan</d:Name>
				<d:Rating m:type="Edm.Double">0</d:Rating>
				<d:Reasoning m:type="Edm.String"/>
				<d:Metadata m:type="Edm.String"/>
				<d:FormattedRating m:type="Edm.Double" m:null="true"/>
			</m:properties>
		</content>
	</entry>
</feed>

##14\. 알림
Azure 기계 학습 권장 사항에서는 시스템에서 영구적 오류가 발생한 경우 알림을 생성합니다. 다음과 같은 세 가지 유형의 알림이 있습니다.
1.	빌드 오류 – 이 알림은 모든 빌드 오류에 대해 트리거됩니다.
2.	데이터 취득 처리 오류 - 이 알림은 지난 5분간의 사용 이벤트 처리에서 모델당 오류가 100개가 넘는 경우 트리거됩니다.
3.	권장 사항 소비 오류 - 이 알림은 지난 5분간의 권장 사항 요청 처리에서 모델당 오류가 100개가 넘는 경우 트리거됩니다.


###14\.1. 알림 가져오기
모든 모델 또는 단일 모델에 대한 모든 알림을 검색합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>모든 모델에 대한 모든 알림 가져오기:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>특정 모델에 대한 알림을 가져오는 예:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 선택적 매개 변수. 생략한 경우 모든 모델에 대한 모든 알림을 가져옵니다. <br>유효한 값: 모델의 고유 식별자|
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답:**

HTTP 상태 코드: 200

OData XML

    The response includes one entry per notification. Each entry has the following data:
		* feed\entry\content\properties\UserName – Internal user name identification.
		* feed\entry\content\properties\ModelId – Model ID.
		* feed\entry\content\properties\Message – Notification message.
		* feed\entry\content\properties\DateCreated – Date that this notification was created in UTC format.
		* feed\entry\content\properties\NotificationType – Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

	<feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
		<title type="text" />
		<subtitle type="text">Get a list of Notifications for a user</subtitle>
		<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
		<rights type="text" />
		<updated>2014-11-04T13:24:23Z</updated>
		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
		<entry>
				<id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
			<title type="text">GetAListOfNotificationsForAUserEntity</title>
			<updated>2014-11-04T13:24:23Z</updated>
    		<link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
			<content type="application/xml">
				<m:properties>
					<d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
					<d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
					<d:Message m:type="Edm.String">Build failed for user</d:Message>
					<d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
					<d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
				</m:properties>
			</content>
		</entry>
	</feed>

###14\.2. 모델 알림 삭제
모델에 대한 읽은 알림을 모두 삭제합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>예:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| modelId | 모델의 고유 식별자 |
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200

###14\.3. 사용자 알림 삭제
모든 모델에 대한 모든 알림을 삭제합니다.

| HTTP 메서드 | URI |
|:--------|:--------|
|삭제 |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|	매개 변수 이름 |	유효한 값 |
|:--------			|:--------								|
| apiVersion | 1\.0 |
|||
| 요청 본문 | 없음 |

**응답**:

HTTP 상태 코드: 200




##15\. 법적 정보
이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 뷰는 통지 없이 변경될 수 있습니다.<br><br>
여기에서 설명하는 일부 예는 설명 목적으로만 제공되는 가상의 예이며, 어떠한 실제 사례와도 연관시킬 의도가 없으며 그렇게 유추해서도 안 됩니다.<br><br>
이 문서는 Microsoft 제품의 지적 소유권에 대한 법적 권한을 사용자에게 제공하지 않습니다. 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.<br><br>
© 2015 Microsoft. All rights reserved.
 

<!---HONumber=AcomDC_0914_2016-->