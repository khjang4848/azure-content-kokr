<properties 
	pageTitle="Azure 주문형 미디어 인코더 개요 및 비교 | Microsoft Azure" 
	description="이 항목에서는 Azure 주문형 미디어 인코더를 대략적으로 설명하고 비교한 데이터를 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="juliako"/>

#Azure 주문형 미디어 인코더 개요 및 비교

##인코딩 개요

Azure 미디어 서비스는 클라우드에서 미디어의 인코딩에 대한 여러 옵션을 제공합니다.

미디어 서비스로 시작하는 경우 코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다. 코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다.

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

[동적 패키징](media-services-dynamic-packaging-overview.md)을 이용하려면 다음을 수행해야 합니다.

- mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 주문형 스트리밍 단위를 구성합니다. 자세한 내용은 [주문형 스트리밍 예약 단위를 확장하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요

미디어 서비스는 이 문서에서 설명하는 다음 주문형 인코더를 지원합니다.

- [미디어 인코더 표준](media-services-encode-asset.md#media-encoder-standard)
- [미디어 인코더 Premium 워크플로](media-services-encode-asset.md#media-encoder-premium-workflow)

이 문서에서는 주문형 미디어 인코더에 대한 간략한 개요와 보다 자세한 정보를 제공하는 문서에 대한 링크를 제공합니다. 또한 항목에서는 인코더에 대한 비교를 제공합니다.

기본적으로 각 미디어 서비스 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 인코딩 예약 단위에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 자세한 내용은 [인코딩 단위 크기 조정](media-services-scale-media-processing-overview.md)을 참조하세요.

##미디어 인코더 표준

###사용 방법

[미디어 인코더 표준으로 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)

###형식

[형식 및 코덱](media-services-media-encoder-standard-formats.md)

###기본 설정

미디어 인코더 표준은 [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

###입력 및 출력 메타데이터

인코더 입력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783120.aspx)에서 설명합니다.

인코더 출력 메타데이터는 [여기](http://msdn.microsoft.com/library/azure/dn783217.aspx)에서 설명합니다.

###미리 보기 생성

자세한 내용은 [미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법](media-services-custom-mes-presets-with-dotnet.md#thumbnails)을 참조하세요.

###비디오 자르기(클리핑)

자세한 내용은 [미디어 인코더 표준을 사용하여 비디오를 자르는 방법](media-services-custom-mes-presets-with-dotnet.md#trim_video)을 참조하세요.

###오버레이 만들기

자세한 내용은 [미디어 인코더 표준을 사용하여 오버레이를 만드는 방법](media-services-custom-mes-presets-with-dotnet.md#overlay)을 참조하세요.

###참고 항목

[미디어 서비스 블로그](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##미디어 인코더 Premium 워크플로

###개요

[Azure 미디어 서비스의 프리미엄 인코딩 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###사용 방법

미디어 인코더 Premium 워크플로는 복잡한 워크플로를 사용하여 구성됩니다. 워크플로 파일은 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 만들고 업데이트할 수 있습니다.

[Azure 미디어 서비스의 프리미엄 인코딩 사용 방법](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###알려진 문제

입력된 비디오에 자막이 없는 경우, 출력 자산은 빈 TTML 파일을 포함합니다.


##<a id="compare_encoders"></a>인코더 비교

###<a id="billing"></a>각 인코더에서 사용되는 요금 청구 기준

미디어 프로세서 이름|적용 가능한 가격|참고 사항
---|---|---
**미디어 인코더 표준** |인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](인코더 열 아래)에 지정된 비율로 청구됩니다.
**미디어 인코더 Premium 워크플로** |프리미엄 인코더|인코딩 작업은 출력 자산의 크기(기가바이트 단위)에 따라 [여기][1](프리미엄 인코더 열 아래)에 지정된 비율로 청구됩니다.


이 섹션에서는 **미디어 인코더 표준** 및 **미디어 인코더 프리미엄 워크플로**의 인코딩 기능을 비교합니다.


###입력 컨테이너/파일 형식

입력 컨테이너/파일 형식|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
Adobe® Flash® F4V |예|예
MXF/SMPTE 377M |예|예
GXF |예|예
MPEG-2 전송 스트림 |예|예
MPEG-2 프로그램 스트림 |예|예
MPEG-4/MP4 |예|예
Windows Media/ASF |예|예
AVI(압축되지 않은 8비트/10비트)|예|예
3GPP/3GPP2 |예|아니요
부드러운 스트리밍 파일 형식(PIFF 1.3)|예|아니요
[DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|예|아니요
Matroska/WebM |예|아니요
QuickTime(.mov) |예|아니요

###입력 비디오 코덱

입력 비디오 코덱|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0 및 4:2:2|예
Avid DNxHD(MXF) |예|예
DVCPro/DVCProHD(MXF) |예|예
JPEG2000 |예|예
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|최대 422 프로필|예
MPEG-1 |예|예
Windows Media 비디오/VC-1 |예|예
Canopus HQ/HQX |아니요|아니요
Mpeg-4 2부 |예|아니요
[Theora](https://en.wikipedia.org/wiki/Theora) |예|아니요
Apple ProRes 422 |예|아니요
Apple ProRes 422 LT |예|아니요
Apple ProRes 422 HQ |예|아니요
Apple ProRes Proxy|예|아니요
Apple ProRes 4444 |예|아니요
Apple ProRes 4444 XQ |예|아니요

###입력 오디오 코덱

입력 오디오 코덱|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
AES(SMPTE 331M 및 302M, AES3-2003) |아니요|예
Dolby® E |아니요|예
Dolby® Digital(AC3) |아니요|예
Dolby® Digital Plus(E-AC3) |아니요|예
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예
MPEG Layer 2|예|예
MP3(MPEG-1 Audio Layer 3)|예|예
Windows Media 오디오|예|예
WAV/PCM|예|예
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|예|아니요
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |예|아니요
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|예|아니요


###출력 컨테이너/파일 형식

출력 컨테이너/파일 형식|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
Adobe® Flash® F4V|아니요|예
MXF(OP1a, XDCAM 및 AS02)|아니요|예
DPP(AS11 포함)|아니요|예
GXF|아니요|예
MPEG-4/MP4|예|예
MPEG-TS|예|예
Windows Media/ASF|아니요|예
AVI(압축되지 않은 8비트/10비트)|아니요|예
부드러운 스트리밍 파일 형식(PIFF 1.3)|아니요|예

###출력 비디오 코덱

출력 비디오 코덱|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra)|8비트 4:2:0만|예
Avid DNxHD(MXF)|아니요|예
DVCPro/DVCProHD(MXF)|아니요|예
MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함)|아니요|예
MPEG-1|아니요|예
Windows Media 비디오/VC-1|아니요|예
JPEG 축소판 그림 만들기|아니요|예

###출력 오디오 코덱

출력 오디오 코덱|미디어 인코더 표준|미디어 인코더 Premium 워크플로
---|---|---
AES(SMPTE 331M 및 302M, AES3-2003)|아니요|예
Dolby® Digital(AC3)|아니요|예
Dolby® Digital Plus(E-AC3) 최대 7.1|아니요|예
AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1)|예|예
MPEG Layer 2|아니요|예
MP3(MPEG-1 Audio Layer 3)|아니요|예
Windows Media 오디오|아니요|예


##오류 코드  

다음 표에서는 인코딩 작업을 실행하는 동안 오류가 발생한 경우 반환될 수 있는 오류 코드를 나열합니다. .NET 코드에서 오류 세부 정보를 가져오려면 [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) 클래스를 사용합니다. REST 코드에서 오류 세부 정보를 가져오려면 [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API를 사용합니다.

ErrorDetail.Code|가능한 오류 원인
-----|-----------------------
알 수 없음| 작업을 실행하는 동안 알 수 없는 오류입니다.
ErrorDownloadingInputAssetMalformedContent|잘못된 파일 이름, 길이가 0인 파일, 잘못된 형식 등 입력 자산을 다운로드하는 동안 발생하는 오류를 포함하는 오류 범주입니다.
ErrorDownloadingInputAssetServiceFailure|다운로드하는 동안 발생하는 네트워크 또는 저장소 오류 등 서비스 쪽의 문제를 포함하는 오류 범주입니다.
ErrorParsingConfiguration|구성이 잘못된 시스템 기본 설정이거나 잘못된 XML이 포함된 경우 등 작업 <see cref="MediaTask.PrivateData"/>(구성)가 잘못된 경우의 오류 범주입니다.
ErrorExecutingTaskMalformedContent|입력 미디어 파일 내의 문제로 인해 실패가 발생한 작업을 실행하는 동안 발생하는 오류 범주입니다.
ErrorExecutingTaskUnsupportedFormat|미디어 프로세서가 지정한 미디어 형식이 지원되지 않는 파일을 처리할 수 없거나 구성과 일치하지 않는 경우의 오류 범주입니다. 예를 들어 비디오만 사용할 수 있는 자산에서 오디오 전용 출력을 생성하려고 하는 경우
ErrorProcessingTask|콘텐츠와 관련되지 않은 작업을 처리하는 동안 미디어 프로세서에서 발생하는 기타 오류 범주입니다.
ErrorUploadingOutputAsset|출력 자산을 업로드할 때 발생하는 오류를 포함하는 오류 범주입니다.
ErrorCancelingTask|작업을 취소하려고 할 때 발생하는 오류를 포함하는 오류 범주입니다.
TransientError|일시적인 문제를 포함하는 오류 범주(예: Azure 저장소의 임시 네트워킹 문제)입니다.


**미디어 서비스** 팀에 도움을 요청하려면 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 엽니다.



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##관련 문서

- [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
- [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_0921_2016-->