<properties 
	pageTitle="미디어 인코더 표준을 사용하여 자산을 인코드하는 방법 | Microsoft Azure" 
	description="미디어 인코더 표준을 사용하여 미디어 서비스에서 미디어 콘텐츠를 인코드하는 방법에 대해 알아봅니다. REST API를 사용하는 코드 샘플입니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako"/>


#미디어 인코더 표준을 사용하여 자산을 인코드하는 방법


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST (영문)](media-services-rest-encode-asset.md)
- [포털](media-services-portal-encode.md)

##개요
인터넷을 통해 디지털 비디오를 배달하려면 미디어를 압축해야 합니다. 디지털 비디오 파일은 크기가 상당히 크기 때문에 인터넷을 통해 전달하거나 고객의 장치에서 제대로 표시하지 못할 수 있습니다. 인코딩은 고객이 미디어를 볼 수 있도록 비디오 및 오디오를 압축하는 과정입니다.

인코딩 작업은 미디어 서비스에서 가장 일반적인 처리 작업 중 하나입니다. 인코딩 작업을 만들어 한 인코딩에서 다른 인코딩으로 미디어 파일을 변환합니다. 인코드할 때는 미디어 서비스 기본 제공 인코더(미디어 인코더 표준)를 사용할 수 있습니다. 또한 미디어 서비스 파트너가 제공하는 인코더를 사용할 수도 있습니다. 타사 인코더는 Azure 마켓플레이스를 통해 사용할 수 있습니다. 인코더에 정의된 미리 설정 문자열을 사용하여 또는 미리 설정 구성 파일을 사용하여 인코딩 작업의 세부 정보를 지정할 수 있습니다. 사용할 수 있는 미리 설정 유형을 보려면 [미디어 인코더 표준에 대한 작업 미리 설정](http://msdn.microsoft.com/library/mt269960)을 참조하세요.

각 작업을 수행하려는 처리 유형에 따라 하나 이상의 작업을 가질 수 있습니다. REST API를 통해 다음 두 가지 방법 중 하나로 작업 및 관련된 작업을 만들 수 있습니다.

- 작업 엔터티에 대한 작업 탐색 속성 또는
- OData 배치 처리를 통해 작업을 인라인으로 정의할 수 있습니다.


항상 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 원하는 형식으로 집합을 변환하는 것이 좋습니다. 동적 패키징을 이용하려면 먼저 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

출력 자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.


>[AZURE.NOTE]미디어 프로세서 참조를 시작하기 전에 올바른 미디어 프로세서 ID를 가지고 있는지 확인하십시오. 자세한 내용은 [미디어 프로세서 가져오기](media-services-rest-get-media-processor.md)를 참조하세요.

##작업을 단일 인코딩 작업으로 만들기

>[AZURE.NOTE] 미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect-programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다.
>
>JSON을 사용하고 요청(예: 연결된 개체 참조)에서 **\_\_metadata** 키워드를 사용하도록 지정할 때 **Accept** 헤더를 [JSON 자세한 정보 표시 형식](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(Accept: application/json;odata=verbose)으로 설정해야 합니다.

다음 예제에서는 특정 해상도와 품질로 비디오를 인코딩하기 위해 하나의 작업 집합으로 작업을 만들어 게시하는 방법을 보여 줍니다. 미디어 인코더 표준으로 인코드할 때 [여기](http://msdn.microsoft.com/library/mt269960)에 지정된 작업 구성 기본 설정을 사용할 수 있습니다.

요청:

POST https://media.windows.net/API/Jobs HTTP/1.1 Content-Type: application/json;odata=verbose Accept: application/json;odata=verbose DataServiceVersion: 3.0 MaxDataServiceVersion: 3.0 x-ms-version: 2.11 Authorization: Bearer <token value> x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

응답:
	
	HTTP/1.1 201 Created

	. . . 

###출력 자산 이름 설정

다음 예제에서는 assetName 특성을 설정하는 방법을 보여 줍니다.

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}

##고려 사항

- TaskBody 속성은 리터럴 XML을 사용하여 작업에서 사용될 입력이나 출력 수를 정의해야 합니다. 작업 항목은 해당 XML에 대한 XML 스키마 정의를 포함합니다.
- TaskBody 정의에서 <inputAsset> 및 <outputAsset>에 대한 각각의 내부 값은 JobInputAsset(value) 또는 JobOutputAsset(value)으로 설정되어야 합니다.
- 작업 출력 자산은 여러 개일 수 있습니다. 작업에서 하나의 JobOutputAsset(x)을 작업 출력으로 한 번만 사용할 수 있습니다.
- JobInputAsset 또는 JobOutputAsset을 작업의 입력 자산으로 지정할 수 있습니다.
- 작업은 주기를 형성해서는 안됩니다.
- JobInputAsset 또는 JobOutputAsset에 전달하는 값 매개변수는 자산에 대한 인덱스 값을 나타냅니다. 실제 자산은 작업 엔터티 정의에 있는 InputMediaAssets 및 OutputMediaAssets 탐색 속성에 정의됩니다.
- 미디어 서비스는 OData v 3를 기반으로 하기 때문에 InputMediaAssets 및 OutputMediaAssets 탐색 속성 컬렉션에 있는 개별 자산은 "\_\_metadata : uri" 이름 값 쌍으로 참조됩니다.
- InputMediaAsset은 미디어 서비스에서 만든 하나 이상의 자산에 매핑됩니다. OutputMediaAsset은 시스템에 의해 생성됩니다. 기존 자산을 참조하지 않습니다.
- OutputMediaAsset은 assetName 특성을 사용하여 명명할 수 있습니다. 이 특성이 없을 경우 OutputMediaAsset의 이름은 <outputAsset> 요소의 내부 텍스트 값이 작업 이름 값 또는 작업 Id 값(이름 속성이 정의되어 있지 않은 경우)의 접미사를 갖는 어떤 것이든 가능합니다. 예를 들어, assetName에 대한 값을 "Sample"로 설정하는 경우 OutputMediaAsset 이름 속성은 "Sample"로 설정됩니다. 하지만 assetName에 대한 값은 설정하지 않았지만 작업 이름을 "NewJob"으로 설정한 경우 OutputMediaAsset 이름은 "JobOutputAsset(값)\_NewJob"이 됩니다.


##연결된 작업으로 작업 만들기

대부분의 응용 프로그램 시나리오에서는 개발자가 일련의 처리 작업을 만들려고 합니다. 미디어 서비스에서 일련의 연결된 작업을 만들 수 있습니다. 각 작업은 서로 다른 처리 단계를 수행하고 다양한 미디어 프로세서를 사용할 수 있습니다. 연결된 작업은 한 작업에서 다른 작업으로 자산을 전달하여 자산에 대한 선형 시퀀스 작업을 수행할 수 있습니다. 그러나 작업에서 수행된 작업은 시퀀스에 있을 필요가 없습니다. 연결된 작업을 만들면 연결된 **ITask** 개체는 단일 **IJob** 개체에 만들어집니다.

>[AZURE.NOTE] 현재 작업은 작업당 30개로 제한됩니다. 30개 이상의 작업을 연결해야 하는 경우 둘 이상의 작업을 만들어 작업을 연결합니다.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


###고려 사항

작업 체인을 사용하려면,

- 작업에 작업이 2개 이상 있어야 합니다.
- 작업에서 입력이 다른 작업의 출력인 작업이 하나 이상 있어야 합니다.

## OData 배치 처리 사용 

다음 예제에서는 OData 배치 처리를 사용하여 작업 및 태스크를 만드는 방법을 보여줍니다. 일괄 처리에 대한 정보는 [Open Data Protocol(OData) 일괄 처리](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)를 참조하세요.
 
	POST https://media.windows.net/api/$batch HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Accept: multipart/mixed
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net
	
	
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-ID: 1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/$1/Tasks HTTP/1.1
	Content-ID: 2
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{  
	   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	   "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="Custom output name">JobOutputAsset(0)</outputAsset></taskBody>"
	}

	--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## JobTemplate을 사용하여 작업 만들기


작업의 공통 집합을 사용하여 여러 자산을 처리할 때 JobTemplates는 기본 작업 기본 설정, 작업의 순서 등을 지정하는 데 유용합니다.

다음 예제는 TaskTemplate 정의된 인라인이 있는 JobTemplate을 만드는 방법을 보여줍니다. TaskTemplate은 미디어 인코더 표준을 MediaProcessor로 사용하여 자산 파일을 인코드합니다. 그러나 다른 Mediaprocessor도 사용할 수 있습니다.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]다른 미디어 서비스 엔터티와 달리, 각 TaskTemplate에 대한 새 GUID 식별자를 정의하고 요청 본문의 taskTemplateId 및 Id 속성에 배치해야 합니다. 콘텐츠 식별 구성표는 Azure 미디어 서비스 엔터티 식별에 설명된 구성표를 따라야 합니다. 또한 JobTemplates는 업데이트할 수 없습니다. 대신 업데이트된 변경 사항으로 새로운 템플릿을 만들어야 합니다.
 

성공하면 다음 응답이 반환됩니다.
	
	HTTP/1.1 201 Created
	
	. . .


다음 예제에서는 JobTemplate Id를 참조하는 작업을 만드는 방법을 보여줍니다.

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

성공하면 다음 응답이 반환됩니다.
	
	HTTP/1.1 201 Created
	
	. . . 



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##다음 단계
자산을 인코드하는 작업을 만드는 방법을 알아보았습니다. 이제 [미디어 서비스 작업 진행 상태를 확인하는 방법](media-services-rest-check-job-progress.md) 항목으로 이동하세요.


##참고 항목

[미디어 프로세서 가져오기](media-services-rest-get-media-processor.md)

<!---HONumber=AcomDC_0921_2016-->