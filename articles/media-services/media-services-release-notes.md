<properties 
	pageTitle="미디어 서비스 릴리스 정보 | Microsoft Azure" 
	description="미디어 서비스 릴리스 정보" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako"/>

# Azure 미디어 서비스 릴리스 정보

이 릴리스 정보에는 이전 릴리스 이후의 변경 내용과 알려진 문제가 요약되어 있습니다.

>[AZURE.NOTE] Azure 팀은 고객의 의견을 수렴하여 고객에게 영향을 주는 문제를 해결하기 위해 노력하고 있습니다. 문제를 보고하거나 질문이 있는 경우 [Azure 미디어 서비스 MSDN 포럼]에 게시해 주세요.


##<a id="issues"></a>현재 알려진 문제

### <a id="general_issues"></a>미디어 서비스 관련 일반 문제

문제|설명
---|---
REST API에 다양한 일반 HTTP 헤더가 제공되지 않습니다.|REST API를 사용하여 미디어 서비스 응용 프로그램을 개발하는 경우 CLIENT-REQUEST-ID, REQUEST-ID, RETURN-CLIENT-REQUEST-ID를 비롯한 몇 가지 일반 HTTP 헤더 필드가 지원되지 않습니다. 이 헤더는 이후 업데이트에서 추가될 예정입니다.
퍼센트 인코딩은 허용되지 않습니다.|미디어 서비스는 스트리밍 콘텐트에 대해 URL을 작성할 때 IAssetFile.Name 속성의 값을 사용합니다(예: http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. **Name** 속성 값에는 !*'();:@&=+$,/?%#"과 같은 [퍼센트 인코딩 예약 문자](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.
Azure 저장소 SDK 버전 3.x의 일부분인 ListBlobs 메서드에서 오류가 발생합니다.|미디어 서비스에서는 [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) 버전을 기반으로 SAS URL을 생성합니다. Azure 저장소 SDK를 사용하여 Blob 컨테이너의 Blob을 나열하려는 경우 Azure 저장소 SDK 버전 2.x에 포함된 [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) 메서드를 사용합니다. Azure 저장소 SDK 버전 3.x의 일부분인 ListBlobs 메서드에서는 오류가 발생합니다.
미디어 서비스 제한 메커니즘은 서비스에 과도한 요청을 보내는 응용 프로그램의 리소스 사용을 제한합니다. 해당 서비스에서 서비스를 사용할 수 없음(503) HTTP 상태 코드가 반환될 수 있습니다.|자세한 내용은 [Azure 미디어 서비스 오류 코드](http://msdn.microsoft.com/library/azure/dn168949.aspx) 항목의 503 HTTP 상태 코드 설명을 참조하세요.
엔터티를 쿼리할 때 한 번에 반환되는 엔터티 수는 최대 1000개입니다. 공용 REST v2에서는 쿼리 결과를 1000개로 제한하기 때문입니다. | [이 .NET 예제](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) 및 [이 REST API 예제](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)에 설명된 대로 **Skip** 및 **Take**(.NET)/**top**(REST)을 사용해야 합니다. 
일부 클라이언트에 부드러운 스트리밍 매니페스트의 반복 태그 문제가 발생할 수 있습니다.|자세한 내용은 [이](media-services-deliver-content-overview.md#known-issues) 섹션을 참조하세요.
Azure 미디어 서비스 .NET SDK 개체는 직렬화할 수 없으며 따라서 Azure 캐싱에서 작동하지 않습니다.|SDK AssetCollection 개체를 직렬화하여 Azure 캐싱에 추가하려는 경우 예외가 Throw됩니다.
메시지 문자열 "Stage: DownloadFile. Code: System.NullReferenceException"을 나타내며 인코딩 작업이 실패합니다.|일반적인 인코딩 워크플로는 입력 자산에 입력 비디오 파일을 업로드하고 해당 입력 자산을 추가로 수정하지 않고 해당 입력 자산에 대한 하나 이상의 인코딩 작업을 제출하는 것입니다. 그러나 입력 자산을 수정하는 경우(예: 자산 내에서 파일 추가/삭제/이름 바꾸기) 후속 작업은 DownloadFile 오류로 실패할 수 있습니다. 해결 방법은 입력 자산을 삭제하고 입력 파일을 새 자산에 다시 업로드하는 것입니다. 

##<a id="rest_version_history"></a>REST API 버전 기록

미디어 서비스 REST API 버전 기록에 대한 자세한 내용은 [Azure 미디어 서비스 REST API 참조]를 참조하세요.

##<a id="july_changes16"></a>2016년 7월 릴리스

###인코딩 작업으로 생성된 매니페스트 파일(*.ISM)에 대한 업데이트

인코딩 작업이 미디어 인코더 표준 또는 Azure 미디어 인코더에 제출된 경우 인코딩 작업은 출력 자산에 [스트리밍 매니페스트 파일](media-services-deliver-content-overview.md)(*.ism)을 생성합니다. 최신 서비스 릴리스와 함께 이 스트리밍 매니페스트 파일의 구문이 업데이트되었습니다.

>[AZURE.NOTE]스트리밍 매니페스트(.ism) 파일의 구문은 내부 용도로 예약되며 향후 릴리스에서 변경될 수 있습니다. 이 파일의 내용을 수정하거나 조작하지 마세요.

###인코딩 작업이 하나 이상의 MP4 파일을 출력하는 경우 새 클라이언트 매니페스트(*. ISMC) 파일이 출력 자산에 생성됩니다.

최신 서비스 릴리스부터, 하나 이상의 MP4 파일을 생성하는 인코딩 작업이 완료되면 출력 자산에 스트리밍 클라이언트 매니페스트(*.ismc) 파일도 포함됩니다. .ismc 파일을 통해 동적 스트리밍의 성능을 향상시킬 수 있습니다.

>[AZURE.NOTE]클라이언트 매니페스트(.ismc) 파일의 구문은 내부 용도로 예약되며 향후 릴리스에서 변경될 수 있습니다. 이 파일의 내용을 수정하거나 조작하지 마세요.

자세한 내용은 [이](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) 블로그를 참조하세요.

### 알려진 문제

일부 클라이언트에 부드러운 스트리밍 매니페스트의 반복 태그 문제가 발생할 수 있습니다. 자세한 내용은 [이](media-services-deliver-content-overview.md#known-issues) 섹션을 참조하세요.

##<a id="apr_changes16"></a>2016년 4월 릴리스

### Azure 미디어 분석

Azure 미디어 서비스는 강력한 비디오 인텔리전스를 위한 Azure 미디어 분석을 출시했습니다. 자세한 내용은 [Azure 미디어 서비스 분석 개요](media-services-analytics-overview.md)를 참조하세요.

### Apple FairPlay(미리 보기)

이제 Azure 미디어 서비스를 사용하여 Apple FairPlay에서 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화할 수 있습니다. 또한 AMS 라이선스 배달 서비스를 사용하여 클라이언트에 FairPlay 라이선스를 배달할 수 있습니다. 자세한 내용은 [Azure 미디어 서비스를 사용하여 Apple FairPlay로 보호되는 HLS 콘텐츠 스트리밍](media-services-protect-hls-with-fairplay.md)을 참조하세요.
  
##<a id="feb_changes16"></a>2016년 2월 릴리스

최신 버전의 .NET용 Azure 미디어 서비스 SDK(3.5.3)에는 Widevine 관련 버그 수정이 포함되어 있습니다. 문제: Widevine으로 암호화된 여러 자산에 대해 AssetDeliveryPolicy를 다시 사용할 수 없습니다. 이 버그 수정의 일환으로 **WidevineBaseLicenseAcquisitionUrl** 속성이 SDK에 추가되었습니다.
	
	Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
	    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
	{
	    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
	    
	};

##<a id="jan_changes_16"></a>2016년 1월 릴리스

인코더 이름의 혼동을 줄이기 위해 인코딩 예약 단위 이름이 바뀌었습니다.

기본, 표준 및 프리미엄 인코딩 예약 단위의 이름이 S1, S2 및 S3 예약 단위로 각각 바뀌었습니다. 현재 기본 인코딩 RU를 사용하는 고객에게는 Azure 포털(및 청구서)에서 S1이 레이블로 표시되고 표준 및 프리미엄 고객에게는 S2 및 S3이 각각 레이블로 표시됩니다.

##<a id="dec_changes_15"></a>2015년 12월 릴리스

Azure SDK 팀은 Microsoft Azure 미디어 서비스에 대한 업데이트 및 새 기능을 포함하는 [PHP용 Azure SDK](http://github.com/Azure/azure-sdk-for-php) 패키지의 새 릴리스를 게시합니다. 특히, PHP용 Azure 미디어 서비스 SDK는 현재 최신 [콘텐츠 보호](media-services-content-protection-overview.md) 기능(토큰 제한을 사용하거나 사용하지 않는 AES 및 DRM(PlayReady 및 Widevine)을 사용하는 동적 암호화)을 지원합니다. 또한 [인코딩 단위](media-services-dotnet-encoding-units.md) 크기 조정을 지원합니다.

자세한 내용은 다음을 참조하세요.

- [PHP용 Microsoft Azure 미디어 서비스 SDK](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) 블로그
- 빨리 시작하는 데 도움이 되는 [코드 샘플](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)은 다음과 같습니다.
	- **vodworkflow\_aes.php**: AES-128 동적 암호화 및 키 배달 서비스를 사용하는 방법을 보여 주는 PHP 파일입니다. 이 파일은 [이](media-services-protect-with-aes128.md) 문서에 설명되어 있는 .NET 샘플을 기반으로 합니다.
	- **vodworkflow\_aes.php**: PlayReady 동적 암호화 및 License Delivery 서비스를 사용하는 방법을 보여 주는 PHP 파일입니다. 이 파일은 [이](media-services-protect-with-drm.md) 문서에 설명되어 있는 .NET 샘플을 기반으로 합니다.
	- **scale\_encoding\_units.php**: 인코딩 예약 단위 크기를 조정하는 방법을 보여 주는 PHP 파일입니다.


##<a id="nov_changes_15"></a>2015년 11월 릴리스

이제 Azure 미디어 서비스는 클라우드에서 Google Widevine 라이선스 배달 서비스를 제공합니다. 자세한 내용은 [이 공지 사항 블로그](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)를 참조하세요. 또한 [이 자습서](media-services-protect-with-drm.md) 및 [GitHub 리포지토리](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)도 참조하세요.

Azure 미디어 서비스에서 제공하는 Widevine 라이선스 배달 서비스는 미리 보기로 제공됩니다. 자세한 내용은 [이 게시물](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)을 참조하세요.

##<a id="oct_changes_15"></a>2015년 10월 릴리스

AMS(Azure 미디어 서비스)는 이제 브라질 남부, 인도 서부, 인도 남부 및 인도 중부 데이터 센터에서도 사용 가능합니다. 이제 Azure 클래식 포털을 사용하여 [미디어 서비스 계정을 만들고](media-services-create-account.md#create-a-media-services-account-using-quick-create) [여기](https://azure.microsoft.com/documentation/services/media-services/)에서 설명한 다양한 작업을 수행할 수 있습니다. 그러나 라이브 인코딩은 이러한 데이터 센터에서 활성화되지 않습니다. 또한 모든 유형의 인코딩 예약 단위를 이러한 데이터 센터에서 사용할 수 없습니다.

- 브라질 남부: 표준 및 기본 인코딩 예약 단위만 사용 가능
- 인도 서부, 인도 남부 및 인도 중부: 기본 인코딩 예약 단위만 사용 가능


##<a id="september_changes_15"></a>2015년 9월 릴리스 

- 이제 AMS에서 Widevine 모듈식 DRM 기술을 사용하여 VOD(Video-On-Demand)와 라이브 스트림 둘 다를 보호하는 기능을 제공합니다. 배달 서비스 파트너 [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/)를 사용하여 Widevine 라이선스를 배달할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)를 참조하세요.

	[AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)(버전 3.5.1부터 시작) 또는 REST API를 통해 Widevine을 사용하도록 AssetDeliveryConfiguration을 구성할 수 있습니다.

- AMS에서 Apple ProRes 동영상 지원을 추가했습니다. 이제 Apple ProRes 또는 기타 코덱을 사용하는 QuickTime 원본 동영상 파일을 업로드할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)를 참조하세요.

- 이제 미디어 인코더 표준을 사용하여 하위 클리핑 및 라이브 보관 추출을 수행할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)를 참조하세요.

- 다음 필터링이 업데이트되었습니다.

	- 이제 오디오 전용 필터로 Apple HTTP 라이브 스트리밍(HLS) 포맷을 사용할 수 있습니다. 이 업데이트를 통해 오디오 전용 트랙을 URL에 지정하여(audio-only=false) 제거할 수 있습니다.
	- 자산에 대한 필터를 정의할 때 이제는 단일 URL에 여러(최대 3개) 필터를 조합할 수 있습니다.

	자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)를 참조하세요.

- AMS는 이제 HLS v4에 I-프레임을 지원합니다. I-프레임 지원은 빨리 감기와 되감기 작업을 최적화합니다. 기본적으로 모든 HLS v4 출력은 I-프레임 재생 목록(EXT-X-I-FRAME-STREAM-INF)를 포함합니다.
 
	자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)를 참조하세요.

##<a id="august_changes_15"></a>2015년 8월 릴리스

- Azure Media Services SDK for Java V0.8.0 릴리스 및 새로운 샘플이 제공됩니다. 자세한 내용은 다음을 참조하세요.

	- [블로그 게시물](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
	- [Java 샘플 리포지토리](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- 다중 오디오 스트림을 지원하는 Azure 미디어 플레이어 업데이트. 자세한 내용은 다음을 참조하세요.
	- [블로그 게시물](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>2015년 7월 릴리스

- 미디어 인코더 표준의 일반 공급 발표 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)을 참조하세요.

	미디어 인코더 표준은 [이](http://go.microsoft.com/fwlink/?LinkId=618336) 섹션에 설명된 기본 설정을 사용합니다. 4k 인코드에 대한 기본 설정을 사용하는 경우 **프리미엄** 예약 단위 형식을 가져와야 합니다. 자세한 내용은 [인코딩 크기를 조정하는 방법](media-services-scale-media-processing-overview.md)을 참조하세요.
- Azure 미디어 서비스 및 플레이어의 라이브 실시간 캡션. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)을 참조하세요.

###미디어 서비스 .NET SDK 업데이트

Azure 미디어 서비스 .NET SDK의 현재 버전은 3.4.0.0입니다. 이 릴리스에서는 다음 기능이 추가되었습니다.

- 라이브 아카이브에 대한 지원 구현 라이브 아카이브를 포함한 자산은 다운로드할 수 없습니다.
- 동적 필터에 대한 지원 구현
- 사용자가 자산을 삭제하는 동안 저장소 컨테이너를 유지할 수 있도록 하는 기능 구현
- 채널에서 재시도 정책 관련 버그 수정
- **미디어 인코더 프리미엄 워크플로** 사용

##<a id="june_changes_15"></a>2015년 6월 릴리스

###미디어 서비스 .NET SDK 업데이트

Azure 미디어 서비스 .NET SDK의 현재 버전은 3.3.0.0입니다. 이 릴리스에서는 다음 기능이 추가되었습니다.

- OpenId Connect Discovery 사양 지원
- ID 공급자 측의 키 롤오버 처리 지원

OpenID Connect Discovery 문서를 노출하는 ID 공급자(예: Azure Active Directory, Google, Salesforce)를 사용하는 경우 Azure 미디어 서비스에 OpenID Connect Discovery의 JWT 토큰에 대한 유효성을 검사하기 위한 서명 키를 가져오도록 지시할 수 있습니다.

자세한 내용은 [OpenID Connect Discovery의 Json 웹 키를 사용하여 Azure 미디어 서비스에서 JWT 인증 토큰 사용](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)을 참조하세요.


##<a id="may_changes_15"></a>2015년 5월 릴리스

다음과 같은 새로운 기능이 도입되었습니다.

- [미디어 서비스로 라이브 인코딩 미리 보기](media-services-manage-live-encoder-enabled-channels.md)
- [동적 매니페스트](media-services-dynamic-manifest-overview.md)
- [Azure 미디어 Hyperlapse 미디어 프로세서 미리 보기](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>2015년 4월 릴리스

 ###일반 미디어 서비스 업데이트

- [Azure 미디어 플레이어가 도입](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)되었습니다.
- 미디어 서비스 REST 2.10부터 RTMP 프로토콜을 수집하도록 구성된 채널이 기본 및 보조 수집 URL을 통해 생성됩니다. 자세한 내용은 [채널 수집 구성](media-services-live-streaming-with-onprem-encoders.md#channel_input)을 참조하세요.
- Azure 미디어 인덱서 업데이트
- 스페인어 지원
- 새로운 구성 xml 형식

자세한 내용은 [이 게시물](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)을 참조하세요.
###미디어 서비스 .NET SDK 업데이트

Azure 미디어 서비스 .NET SDK의 현재 버전은 3.2.0.0입니다.

다음은 고객을 위한 몇 가지 업데이트입니다.

- **주요 변경 내용**: **TokenRestrictionTemplate.Issuer** 및 **TokenRestrictionTemplate.Audience**가 문자열 형식으로 변경됨
- 사용자 지정 다시 시도 정책 만들기 관련 업데이트
- 파일 업로드/다운로드 관련 버그 수정
- **MediaServicesCredentials** 클래스에서 이제 기본 및 보조 액세스 제어 끝점에 대한 인증을 허용합니다.



##<a id="march_changes_15"></a>2015년 3월 릴리스

### 일반 미디어 서비스 업데이트

- 미디어 서비스는 이제 Azure CDN 통합을 제공합니다. 통합을 지원하기 위해 **CdnEnabled** 속성이 **StreamingEndpoint**에 추가되었습니다. **CdnEnabled**는 버전 2.9부터 REST API와 함께 사용할 수 있습니다(자세한 내용은 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) 참조). **CdnEnabled**는 버전 3.1.0.2부터 .NET SDK와 함께 사용할 수 있습니다(자세한 내용은 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx))) 참조).
- **미디어 인코더 Premium 워크플로** 알림. 자세한 내용은 [Azure 미디어 서비스의 프리미엄 인코딩 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)를 참조하세요.
 


##<a id="february_changes_15"></a>2015년 2월 릴리스

### 일반 미디어 서비스 업데이트

이제 미디어 서비스 REST API의 버전이 2.9입니다. 이 버전부터 스트리밍 끝점과 Azure CDN 통합을 사용할 수 있습니다. 자세한 내용은 [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx)를 참조하세요.

##<a id="january_changes_15"></a>2015년 1월 릴리스

### 일반 미디어 서비스 업데이트

동적 암호화를 통해 GA(General Availability)의 콘텐츠 보호 알림. 자세한 내용은 [Azure 미디어 서비스에서 DRM 기술의 일반적인 가용성으로 스트리밍 보안 강화](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)를 참조하세요.

###미디어 서비스 .NET SDK 업데이트

이제 Azure 미디어 서비스 .NET SDK의 버전은 3.1.0.1입니다.

이 릴리즈에서 기본 Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate 생성자는 사용되지 않음으로 표시됩니다. TokenType를 인수로 사용하는 새로운 생성자입니다.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>2014년 12월 릴리스

###일반 미디어 서비스 업데이트

- 일부 업데이트 및 새 기능이 Azure 인덱서 미디어 프로세서에 추가 되었습니다. 자세한 내용은[ Azure 미디어 인덱서 버전 1.1.6.7 릴리스 정보](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)를 참조하세요.
- 인코딩 예약 단위를 업데이트할 수 있는 새 REST API인 [EncodingReservedUnitType이 REST](http://msdn.microsoft.com/library/azure/dn859236.aspx)에 추가되었습니다.
- 키 배달 서비스를 위한 CORS 지원이 추가되었습니다.
- 권한 부여 정책 옵션을 쿼리하는 성능이 향상되었습니다.
- 중국 데이터 센터에서는 이제 다른 데이터 센터와 마찬가지로 [키 배달 URL](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url)이 고객별로 하나씩 할당됩니다.
- HLS 자동 대상 기간이 추가되었습니다. 라이브 스트리밍을 수행할 때 HLS는 항상 동적으로 패키지됩니다. 기본적으로 미디어 서비스는 라이브 인코더에서 수신되는, GOP(Group of Pictures)라고도 하는 키 프레임 간격(KeyFrameInterval)에 따라 자동으로 HLS 세그먼트 패키징 비율(FragmentsPerSegment)을 계산합니다. 자세한 내용은 [Azure 미디어 서비스 라이브 스트리밍 사용]을 참조하세요.
 
###미디어 서비스 .NET SDK 업데이트

- [Azure 미디어 서비스 .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/)의 현재 버전은 3.1.0.0입니다.
- .Net SDK 종속성이 .NET 4.5 Framework로 업그레이드되었습니다.
- 인코딩 예약 단위를 업데이트할 수 있는 새로운 API가 추가되었습니다. 자세한 내용은 [.NET을 사용하여 예약 단위 유형 업데이트 및 증가 인코딩 RU 증가](http://msdn.microsoft.com/library/azure/jj129582.aspx)를 참조하세요.
- 노큰 인증을 위한 JWT(JSON 웹 토큰) 지원이 추가되었습니다. 자세한 내용은 [Azure 미디어 서비스 및 동적 암호화의 JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조하세요.
- PlayReady 라이선스 템플릿에 BeginDate 및 ExpirationDate에 대한 상대적 오프셋이 추가되었습니다.


##<a id="november_changes_14"></a>2014년 11월 릴리스

        - Media Services now enables you to ingest a live Smooth Streaming (FMP4) content over an SSL connection. To ingest over SSL, make sure to update the ingest URL to HTTPS.  For more information about live streaming, see [Working with Azure Media Services Live Streaming].
        - Note that currently, you cannot ingest an RTMP live stream over an SSL connection.
        - You can also stream your content over an SSL connection. To do this, make sure your streaming URLs start with HTTPS.
        - Note that you can only stream over SSL if the streaming endpoint from which you deliver your content was created after September 10th, 2014. If your streaming URLs are based on the streaming endpoints created after September 10th, the URL contains “streaming.mediaservices.windows.net” (the new format). Streaming URLs that contain “origin.mediaservices.windows.net” (the old format) do not support SSL. If your URL is in the old format and you want to be able to stream over SSL, [create a new streaming endpoint](media-services-portal-manage-streaming-endpoints.md). Use URLs created based on the new streaming endpoint to stream your content over SSL.

        ##<a id="october_changes_14"></a>October 2014 Release

### <a id="new_encoder_release"></a>미디어 서비스 인코더 릴리스

미디어 서비스 Azure 미디어 인코더의 새 릴리스가 발표되었습니다. 최신 Azure 미디어 인코더에서는 출력량(GB)에 대해 요금이 청구되며 새 인코더의 기능은 이전 인코더와 호환됩니다. 자세한 내용은 [미디어 서비스 가격 정보]를 참조하세요.

### <a id="oct_sdk"></a>미디어 서비스 .NET SDK 

이제 .NET용 미디어 서비스 SDK 확장의 버전이 2.0.0.3입니다.

이제 .NET용 미디어 서비스 SDK의 버전이 3.0.0.8입니다.

변경된 사항은 다음과 같습니다.

- 재시도 정책 클래스에서 리팩터링합니다.
- http 요청 헤더에 사용자 에이전트 문자열이 추가되었습니다.
- Nuget 복원 빌드 단계를 추가합니다.
- 리포지토리의 x509 인증서를 사용하도록 시나리오 테스트를 수정합니다.
- 채널 및 스트리밍 끝점을 업데이트할 때 설정의 유효성을 감사합니다.
 

### 미디어 서비스 샘플을 호스트하는 새 GitHub 리포지토리

샘플은 [Azure 미디어 서비스 샘플 GitHub 리포지토리](https://github.com/Azure/Azure-Media-Services-Samples)에 있습니다.


##<a id="september_changes_14"></a>2014년 9월 릴리스

이제 미디어 서비스 REST 메타데이터의 버전이 2.7입니다. 최신 REST 업데이트에 대한 자세한 내용은 [Azure 미디어 서비스 REST API 참조]를 참조하세요.

이제 .NET용 미디어 서비스 SDK의 버전이 3.0.0.7입니다.
 
### <a id="sept_14_breaking_changes"></a>주요 변경 내용

* **Origin**의 이름이 [StreamingEndpoint]로 바뀌었습니다.
* **Azure 클래식 포털**을 사용하여 MP4 파일로 인코딩한 다음 게시할 때의 기본 동작이 변경되었습니다.

이전에는 Azure 클래식 포털을 사용하여 단일 파일 MP4 비디오 자산을 게시할 때 SAS URL이 생성되었습니다. SAS URL을 통해 Blob 저장소에서 비디오를 다운로드할 수 있습니다. 현재는 Azure 클래식 포털을 사용하여 단일 파일 MP4 비디오 자산을 인코딩한 다음 게시하면 생성되는 URL이 Azure 미디어 서비스 스트리밍 끝점을 가리킵니다. 이 변경 내용은 미디어 서비스에 직접 업로드되고 Azure 미디어 서비스를 통한 인코딩 없이 게시되는 MP4 비디오에는 영향을 주지 않습니다.

현재 다음의 두 가지 옵션을 통해 문제를 해결할 수 있습니다.

* 스트리밍 단위를 사용하도록 설정하고 동적 패키징을 사용하여 .mp4 자산을 부드러운 스트리밍 프레젠테이션으로 스트리밍합니다.

* .mp4를 다운로드하거나 점진적으로 재생할 SAS URL을 만듭니다. SAS 로케이터를 만드는 방법에 대한 자세한 내용은 [콘텐츠 배달]을 참조하세요.


### <a id="sept_14_GA_changes"></a>GA 릴리스에 포함된 새 기능/시나리오

* **인덱서 미디어 프로세서**. 자세한 내용은 [Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱]을 참조하세요.

* 이제 [StreamingEndpoint] 엔터티를 통해 사용자 지정 도메인(호스트) 이름을 추가할 수 있습니다.

	사용자 지정 도메인 이름을 미디어 서비스 스트리밍 끝점 이름으로 사용하려면 스트리밍 끝점에 사용자 지정 호스트 이름을 추가해야 합니다. 미디어 서비스 REST API 또는 .NET SDK를 통해 사용자 지정 호스트 이름을 추가합니다.
	
	고려 사항은 다음과 같습니다.
	
	* 사용자 지정 도메인 이름에 대한 소유권이 있어야 합니다.
	
	* Azure 미디어 서비스에서 도메인 이름 소유권의 유효성을 확인해야 합니다. 도메인이 유효한지 검사하려면 <MediaServicesAccountId>.<parent domain>을 verifydns.<mediaservices-dns-zone>에 매핑하는 CName을 만듭니다.
	
	* 사용자 지정 호스트 이름(예: sports.contoso.com)을 미디어 서비스 StreamingEndpont 호스트 이름(예: amstest.streaming.mediaservices.windows.net)에 매핑되는 다른 CName을 만들어야 합니다.


	자세한 내용은 [StreamingEndpoint] 항목의 **CustomHostNames** 속성을 참조하세요.

### <a id="sept_14_preview_changes"></a>공개 미리 보기 릴리스에 포함된 새 기능/시나리오

* 라이브 스트리밍 미리 보기. 자세한 내용은 [Azure 미디어 서비스 라이브 스트리밍 사용]을 참조하세요.

* 키 전달 서비스. 자세한 내용은 [AES-128 동적 암호화 및 키 전달 서비스 사용]을 참조하세요.

* AES 동적 암호화. 자세한 내용은 [AES-128 동적 암호화 및 키 전달 서비스 사용]을 참조하세요.

* PlayReady License Delivery 서비스. 자세한 내용은 [PlayReady 동적 암호화 및 License Delivery 서비스 사용]을 참조하세요.

* PlayReady 동적 암호화. 자세한 내용은 [PlayReady 동적 암호화 및 License Delivery 서비스 사용]을 참조하세요.

* 미디어 서비스 PlayReady 라이선스 템플릿. 자세한 정보는 [미디어 서비스 PlayReady 라이선스 템플릿 개요]를 참조하세요.

* 저장소에서 암호화된 자산 스트리밍. 자세한 내용은 [저장소에서 암호화된 콘텐츠 스트리밍]를 참조하세요.

##<a id="august_changes_14"></a>2014년 8월 릴리스

자산을 인코딩하는 경우 출력 자산은 인코딩 작업 완료 시 생성됩니다. 이번 릴리스까지 Azure 미디어 서비스 인코더는 출력 자산에 대한 메타데이터를 생성했습니다. 이번 릴리스부터 이 인코더는 입력 자산에 대한 메타데이터도 생성합니다. 자세한 내용은 [입력 메타데이터] 및 [출력 메타데이터] 항목을 참조하세요.


##<a id="july_changes_14"></a>2014년 7월 릴리스

Azure 미디어 서비스 패키지 작성 도구 및 암호기에 대한 다음 버그가 수정되었습니다.

* 라이브 보관 자산을 HTTP 라이브 스트리밍에 트랜스믹싱할 때 오디오만 재생됨 - 이 문제는 해결되었으며, 이제 오디오와 비디오 둘 다 재생됩니다.

* HTTP 라이브 스트리밍 및 AES 128비트 봉투 암호화에 자산을 패키지한 경우 Android 장치에서 패키지 스트림이 재생되지 않음 - 이 버그는 수정되었으며, HTTP 라이브 스트리밍을 지원하는 Android 장치에서 패키지 스트림이 재생됩니다.

##<a id="may_changes_14"></a>2014년 5월 릴리스

### <a id="may_14_changes"></a>일반 미디어 서비스 업데이트

이제 [동적 패키징]을 사용하여 HLS(HTTP 라이브 스트리밍) v3을 스트리밍할 수 있습니다. HLS v3를 스트리밍하려면 원래 로케이터 경로에 *.ism/manifest(format=m3u8-aapl-v3) 형식을 추가합니다. 자세한 내용은 [Nick Drouin의 블로그]를 참조하세요.

이제 동적 패키징에서는 PlayReady를 통해 정적으로 암호화된 부드러운 스트리밍을 기반으로 하여 PlayReady로 암호화된 HLS(v3 및 v4)도 배달할 수 있습니다. PlayReady로 부드러운 스트리밍을 암호화하는 방법에 대한 자세한 내용은 [PlayReady로 부드러운 스트림 및 MPEG DASH 보호]를 참조하세요.

### <a name="may_14_donnet_changes"></a>미디어 서비스 .NET SDK 업데이트

미디어 서비스 .NET SDK 3.0.0.5 릴리스에서는 다음 기능이 향상되었습니다.

* 미디어 자산을 업로드하거나 다운로드할 경우 속도와 복원력이 향상되었습니다.

* 다시 시도 논리와 일시적 예외 처리 시 다음이 향상되었습니다.

	* 쿼리, 변경 내용 저장, 파일 업로드 또는 다운로드로 인해 발생한 예외의 경우 일시적인 오류 감지 및 다시 시도 논리가 향상되었습니다.
	
	* 웹 예외(예: ACS 토큰 요청 중)가 발생할 경우 치명적인 오류가 급속도로 사라집니다.

자세한 내용은 [.NET용 미디어 서비스 SDK의 다시 시도 논리]를 참조하세요.

##<a id="april_changes_14"></a>2014년 4월 인코더 릴리스

### <a name="april_14_enocer_changes"></a>미디어 서비스 인코더 업데이트

* Grass Valley EDIUS 비선형 편집기를 사용하여 제작한 AVI 파일(Grass Valley HQ/HQX 코덱을 사용하여 비디오가 약간 압축됨) 수집을 위한 지원이 추가되었습니다. 자세한 내용은 [Grass Valley의 클라우드를 통한 EDIUS 7 스트리밍 발표]를 참조하세요.

* 미디어 인코더에서 생성된 파일의 명명 규칙을 지정하는 데 대한 지원이 추가되었습니다. 자세한 내용은 [미디어 서비스 인코더 출력 파일 이름 제어]를 참조하세요.

* 비디오 및/또는 오디오 오버레이에 대한 지원이 추가되었습니다. 자세한 내용은 [오버레이 만들기]를 참조하세요.

* 여러 비디오 세그먼트를 함께 연결하는 데 대한 지원이 추가되었습니다. 자세한 내용은 [비디오 세그먼트 연결]을 참조하세요.

* 오디오가 MP3(MPEG-1 Audio layer 3)로 인코딩된 경우 MP4의 코드 변환 관련 버그가 수정되었습니다.


##<a id="jan_feb_changes_14"></a>2014년 1월/2월 릴리스

### <a name="jan_fab_14_donnet_changes"></a>Azure 미디어 서비스 .NET SDK 3.0.0.1, 3.0.0.2 및 3.0.0.3

3\.0.0.1 및 3.0.0.2의 변경 내용은 다음과 같습니다.

* OrderBy 문을 통한 LINQ 쿼리 사용에 관련된 문제가 해결되었습니다.

* [GitHub]의 테스트 솔루션이 단위 기반 테스트와 시나리오 기반 테스트로 분할되었습니다.

변경 내용에 대한 자세한 내용은 [Azure 미디어 서비스 .NET SDK 3.0.0.1 및 3.0.0.2 릴리스]를 참조하세요.

3\.0.0.3에서 변경된 사항은 다음과 같습니다.

* 버전 3.0.3.0을 사용하도록 Azure 저장소 종속성이 업그레이드되었습니다.

* 3\.0.*.* 릴리스에 대한 이전 버전과의 호환성 문제가 해결되었습니다.


##<a id="december_changes_13"></a>2013년 12월 릴리스

### <a name="dec_13_donnet_changes"></a>Azure 미디어 서비스 .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x 릴리스는 이전 버전인 2.4.x.x 릴리스와 호환되지 않습니다.

이제 .NET용 미디어 서비스의 최신 버전이 3.0.0.0입니다. Nuget에서 최신 패키지를 다운로드하거나 [GitHub]에서 비트를 받을 수 있습니다.

미디어 서비스 SDK 버전 3.0.0.0부터 [Azure Active Directory ACS(액세스 제어 서비스)] 토큰을 다시 사용할 수 있습니다. 자세한 내용은 [.NET용 미디어 서비스 SDK를 통해 미디어 서비스에 연결] 항목의 "액세스 제어 서비스 토큰 다시 사용" 섹션을 참조하세요.

### <a name="dec_13_donnet_ext_changes"></a>Azure 미디어 서비스 .NET SDK 확장 2.0.0.0

Azure 미디어 서비스 .NET SDK Extensions는 코드를 단순화하고 Azure 미디어 서비스를 사용하여 더욱 쉽게 개발할 수 있도록 지원하는 일련의 확장 메서드 및 도우미 함수입니다. [Azure 미디어 서비스 .NET SDK Extensions]에서 최신 파일을 구할 수 있습니다.

##<a id="november_changes_13"></a>2013년 11월 릴리스

### <a name="nov_13_donnet_changes"></a>Azure 미디어 서비스 .NET SDK 변경 내용

이 버전부터 .NET용 미디어 서비스 SDK는 미디어 서비스 REST API 계층에 대한 호출을 실행할 때 발생할 수 있는 일시적인 오류를 처리합니다.

##<a id="august_changes_13"></a>2013년 8월 릴리스

### <a name="aug_13_powershell_changes"></a>Azure SDK 도구에 포함된 미디어 서비스 PowerShell cmdlet

다음 미디어 서비스 PowerShell cmdlet이 [azure-sdk-tools]에 포함되었습니다.

* Get-AzureMediaServices

	예: `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount

	예: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* New-AzureMediaServicesKey

	예: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount

	예: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>2013년 6월 릴리스

### <a name="june_13_general_changes"></a>Azure 미디어 서비스 변경 내용

이 섹션에 언급된 변경 내용은 2013년 6월 미디어 서비스 릴리스에 포함된 업데이트입니다.

* 여러 저장소 계정을 미디어 서비스 계정에 연결할 수 있는 기능

	StorageAccount
	
	Asset.StorageAccountName 및 Asset.StorageAccount

* Job.Priority를 업데이트하는 기능

* 알림 관련 엔터티 및 속성:

	JobNotificationSubscription
	
	NotificationEndPoint
	
	작업

* Asset.Uri

* Locator.Name

### <a name="june_13_dotnet_changes"></a>Azure 미디어 서비스 .NET SDK 변경 내용

2013년 6월 미디어 서비스 SDK 릴리스에 포함된 변경 내용은 다음과 같습니다. 최신 미디어 서비스 SDK는 GitHub에서 제공됩니다.

* 버전 2.3.0.0부터는 미디어 서비스 SDK에서 미디어 서비스 계정에 여러 저장소 계정을 연결하도록 지원합니다. 이 기능을 지원하는 API는 다음과 같습니다.
	
	IStorageAccount 형식
	
	Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 속성
	
	StorageAccount 속성
	
	StorageAccountName 속성
	
	자세한 내용은 [여러 저장소 계정에서 미디어 서비스 자산 관리]를 참조하세요.

* 알림 관련 API. 버전 2.2.0.0부터는 Azure 큐 저장소 알림을 수신할 수 있습니다. 자세한 내용은 [미디어 서비스 작업 알림 처리]를 참조하세요.
	
	Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 속성
	
	Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 형식
	
	Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 형식
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 형식
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 형식
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 형식

* Azure 저장소 클라이언트 SDK 2.0(Microsoft.WindowsAzure.StorageClient.dll)에 대한 종속성

* OData 5.5(Microsoft.Data.OData.dll)에 대한 종속성


##<a id="december_changes_12"></a>2012년 12월 릴리스

### <a name="dec_12_dotnet_changes"></a>Azure 미디어 서비스 .NET SDK 변경 내용

* Intellisense: 다양한 유형의 누락된 Intellisense 설명서가 추가되었습니다.

* Microsoft.Practices.TransientFaultHandling.Core: SDK가 여전히 이 어셈블리의 이전 버전에 종속되는 문제를 해결합니다. 현재 SDK는 이 어셈블리의 5.1.1209.1 버전을 참조합니다.

2012년 11월 SDK에서 확인되어 수정된 문제:

* IAsset.Locators.Count: 이제 모든 로케이터가 삭제된 후 이 수가 새로운 IAsset 인터페이스에서 올바르게 보고됩니다.

* IAssetFile.ContentFileSize: 이제 IAssetFile.Upload(filepath)로 업로드한 후 이 값이 올바르게 설정됩니다.

* IAssetFile.ContentFileSize: 이제 자산 파일을 만들 때 이 속성을 설정할 수 있습니다. 이전에는 이 속성이 읽기 전용이었습니다.

* IAssetFile.Upload(filepath): 자산에 여러 파일을 업로드할 때 이 동기식 업로드 메서드가 "서버가 요청을 인증하지 못했습니다. 서명을 비롯한 권한 부여 헤더 값이 올바르게 구성되어 있는지 확인하세요." 오류를 Throw하는 문제를 해결합니다.

* IAssetFile.UploadAsync: 5개가 넘는 파일을 동시에 업로드할 수 없는 문제를 해결합니다.

* IAssetFile.UploadProgressChanged: 이제 SDK에서 이 이벤트가 제공됩니다.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): 이제 이 메서드 오버로드가 제공됩니다.

* IAssetFile.DownloadAsync: 5개가 넘는 파일을 동시에 다운로드할 수 없는 문제를 해결합니다.

* IAssetFile.Delete(): IAssetFile로 업로드된 파일이 없는 경우 delete를 호출하면 예외가 Throw되는 문제를 해결합니다.

* Jobs: 작업 템플릿을 사용하여 "MP4를 부드러운 스트림으로 변환 작업"과 "PlayReady Protection 작업"을 연결해도 작업이 전혀 만들어지지 않는 문제를 해결합니다.

* EncryptionUtils.GetCertificateFromStore(): 이 메서드는 인증서 구성 문제로 인해 인증서를 찾지 못하여 발생하는 Null 참조 예외를 더 이상 Throw하지 않습니다.

##<a id="november_changes_12"></a>2012년 11월 릴리스

이 섹션에 설명된 변경 내용은 2012년 11월(버전 2.0.0.0) SDK에 포함된 업데이트입니다. 이러한 변경 내용에서는 2012년 6월 SDK 미리 보기 릴리스용으로 작성된 코드를 수정하거나 다시 작성해야 할 수 있습니다.

* 자산
	
	IAsset.Create(assetName)는 유일한 자산 작성 함수입니다. IAsset.Create는 더 이상 메서드 호출을 통해 파일을 업로드하지 않고, IAssetFile을 사용하여 업로드합니다.
	
	서비스 SDK에서 IAsset.Publish 메서드와 AssetState.Publish 열거 값이 제거되었습니다. 이 값을 기반으로 하는 모든 코드를 다시 작성해야 합니다.

* FileInfo

	이 클래스는 제거되었으며 IAssetFile로 바뀌었습니다.

	IAssetFiles

	IAssetFile은 FileInfo를 대체하며 동작이 다릅니다. 이를 사용하려면 IAssetFiles 개체를 인스턴스화한 후 미디어 서비스 SDK 또는 Azure 저장소 SDK를 사용하여 파일을 업로드합니다. 사용할 수 있는 IAssetFile.Upload 오버로드는 다음과 같습니다.

	* IAssetFile.Upload(filePath): 스레드를 차단하는 동기 메서드로, 단일 파일을 업로드할 때만 권장됩니다.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): 비동기 메서드로, 기본 설정된 업로드 메커니즘입니다.

	알려진 버그: cancellationToken을 사용하면 실제로 업로드가 취소되지만 작업의 취소 상태가 여러 상태 중 하나일 수 있습니다. 예외를 올바르게 파악하여 처리해야 합니다.

* 로케이터
	
	원본 관련 버전이 제거되었습니다. SAS 특정 context.Locators.CreateSasLocator(asset, accessPolicy)가 더 이상 사용되지 않거나 GA에 의해 제거된 것으로 표시됩니다. 업데이트된 동작에 대한 자세한 내용은 새 기능 아래의 로케이터 섹션을 참조하세요.


##<a id="june_changes_12"></a>2012년 6월 미리 보기 릴리스

11월 SDK 릴리스의 새로운 기능은 다음과 같습니다.

* 엔터티 삭제

	이제 cloudMediaContext.ObjCollection.Delete(objInstance) 등의 컬렉션에서 삭제할 필요 없이 IObject.Delete() 등의 개체 수준에서 IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey 개체가 삭제되었습니다.

* 로케이터

	이제 로케이터는 CreateLocator 메서드를 사용하여 만들어야 하며 LocatorType.SAS 또는 LocatorType.OnDemandOrigin 열거 값을 만들려는 특정 로케이터 유형에 대한 인수로 사용해야 합니다.

	로케이터에 새로운 속성이 추가되었으므로 콘텐츠에 사용할 수 있는 URI를 더욱 쉽게 얻을 수 있습니다. 이렇게 로케이터를 새롭게 디자인함으로써 향후 타사 제품으로 보다 유연하게 확장할 수 있으며 미디어 클라이언트 응용 프로그램의 사용 편의를 높일 수 있습니다.

* 비동기 메서드 지원

	모든 메서드에 비동기 지원이 추가되었습니다.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure 미디어 서비스 MSDN 포럼]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure 미디어 서비스 REST API 참조]: http://msdn.microsoft.com/library/azure/hh973617.aspx
[미디어 서비스 가격 정보]: http://azure.microsoft.com/pricing/details/media-services/
[입력 메타데이터]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[출력 메타데이터]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[콘텐츠 배달]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Azure 미디어 서비스 라이브 스트리밍 사용]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[AES-128 동적 암호화 및 키 전달 서비스 사용]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[PlayReady 동적 암호화 및 License Delivery 서비스 사용]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[미디어 서비스 PlayReady 라이선스 템플릿 개요]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[저장소에서 암호화된 콘텐츠 스트리밍]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[동적 패키징]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin의 블로그]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[PlayReady로 부드러운 스트림 및 MPEG DASH 보호]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[.NET용 미디어 서비스 SDK의 다시 시도 논리]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley의 클라우드를 통한 EDIUS 7 스트리밍 발표]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[미디어 서비스 인코더 출력 파일 이름 제어]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[오버레이 만들기]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[비디오 세그먼트 연결]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure 미디어 서비스 .NET SDK 3.0.0.1 및 3.0.0.2 릴리스]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory ACS(액세스 제어 서비스)]: http://msdn.microsoft.com/library/hh147631.aspx
[.NET용 미디어 서비스 SDK를 통해 미디어 서비스에 연결]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure 미디어 서비스 .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[여러 저장소 계정에서 미디어 서비스 자산 관리]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[미디어 서비스 작업 알림 처리]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 

<!---HONumber=AcomDC_0921_2016-->