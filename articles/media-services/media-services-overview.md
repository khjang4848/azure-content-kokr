<properties 
	pageTitle="Azure Media Services 개요 및 일반적인 시나리오 | Microsoft Azure" 
	description="이 항목에서는 Azure 미디어 서비스에 대한 개요를 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="09/19/2016"
	ms.author="juliako;anilmur"/>

#Azure Media Services 개요 및 일반적인 시나리오

Microsoft Azure 미디어 서비스는 개발자가 확장 가능한 미디어 관리 및 배달 응용 프로그램을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. 미디어 서비스는 다양한 클라이언트(예: TV, PC 및 모바일 장치)로 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키징할 수 있는 REST API를 기반으로 합니다.

전체 미디어 서비스를 사용하여 종단 간 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 미디어 서비스를 사용하여 업로드, 보호, 패키징 및 배달합니다.

콘텐츠를 라이브로 스트리밍하고 주문 시 콘텐츠를 배달하도록 선택할 수 있습니다. 이 항목에서는 [라이브](media-services-overview.md#live_scenarios) 또는[주문 시](media-services-overview.md#vod_scenarios) 콘텐츠를 배달하는 일반적인 시나리오를 보여줍니다. 이 항목은 관련 항목으로도 연결됩니다.

## SDK 및 도구

미디어 서비스 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

- [미디어 서비스 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 사용 가능한 클라이언트 SDK 중 하나:
- [.NET용 Azure 미디어 서비스 SDK](https://github.com/Azure/azure-sdk-for-media-services),
- [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java),
- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
- [Node.js용 Azure 미디어 서비스](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js)(Node.js SDK의 Microsoft가 아닌 타사 버전입니다. 커뮤니티에서 유지 관리하고 현재 AMS API를 100% 포함하지 않습니다).
- 기존 도구:
- [Azure 클래식 포털](http://manage.windowsazure.com/)
- [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE(Azure 미디어 서비스 탐색기)는 Windows용 Winforms/C# 응용 프로그램임)

##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##필수 조건

Azure 미디어 서비스 사용을 시작하려면 다음이 있어야 합니다.

3. Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com)을 참조하십시오.
2. Azure 미디어 서비스 계정. Azure 클래식 포털, .NET 또는 REST API를 사용하여 Azure 미디어 서비스 계정을 만듭니다. 자세한 내용은 [계정 만들기](media-services-create-account.md)를 참조하세요.
3. (선택 사항) 개발 환경 설정. 개발 환경에 .NET 또는 REST API를 선택합니다. 자세한 내용은 [환경 설정](media-services-dotnet-how-to-use.md)을 참조하세요.

프로그래밍 방식으로 자동으로 [연결](media-services-dotnet-connect-programmatically.md)하는 방법도 알아봅니다.
4. (권장) 하나 이상의 배율 단위 할당. 프로덕션 환경에서 응용 프로그램에 대한 하나 이상의 확장 단위를 할당하는 것이 좋습니다. 자세한 내용은 [스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md)를 참조하세요.

##개념 및 개요

Azure 미디어 서비스 개념은 [개념](media-services-concepts.md)을 참조하세요.

Azure 미디어 서비스의 모든 주요 구성 요소를 소개하는 사용 방법 시리즈는 [Azure 미디어 서비스 단계별 자습서](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series)를 참조하세요. 이 시리즈에는 개념에 대한 훌륭한 개요가 포함되어 있으며 AMSE 도구를 사용하여 AMS 작업을 보여 줍니다. AMSE 도구는 Windows 도구입니다. 이 도구는 [.NET용 AMS SDK](https://github.com/Azure/azure-sdk-for-media-services), [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java) 또는 [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)를 사용하여 프로그래밍 방식으로 수행할 수 있는 대부분의 작업을 지원합니다.

##<a id="vod_scenarios"></a>Azure 미디어 서비스를 사용하여 주문형 미디어 제공: 일반적인 시나리오 및 작업

이 섹션에서는 일반적인 시나리오를 설명하고 관련 항목에 대한 링크를 제공합니다. 다음 다이어그램에서는 주문 시 콘텐츠 배달과 관련된 미디어 서비스 플랫폼의 주요 부분을 보여 줍니다.

![VoD 워크플로](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###저장소에서 콘텐츠 보호 및 암호화하지 않고 스트리밍 미디어 배달(암호화되지 않음)

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
	
	업로드하는 동안 및 저장소에 있는 동안 콘텐츠를 보호하기 위해 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다.
 
1. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.

	그대로 있는 콘텐츠를 보호하기 위해 출력 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다.
	
1. 자산 배달 정책(동적 패키징에서 사용)을 구성합니다.
	
	자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 **합니다**.

1. 주문형 로케이터를 만들어 자산을 게시합니다.

	콘텐츠를 스트림하려는 스트리밍 끝점에서 최소 1개의 스트리밍 예약 단위가 있어야 합니다.

1. 게시된 콘텐츠를 스트리밍합니다.

###저장소에서 콘텐츠를 보호하고 암호화된 스트리밍 미디어를 동적으로 배달합니다.  

동적 암호화를 사용할 수 있으려면 먼저 암호화된 콘텐츠를 스트리밍하려는 스트리밍 끝점에서 하나 이상의 스트리밍 예약 단위를 가져와야 합니다.

1. 자산에 고품질 mezzanine 파일을 업로드합니다. 저장소 암호화 옵션을 자산에 적용합니다.
1. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다. 저장소 암호화 옵션을 출력 자산에 적용합니다.
1. 재생하는 동안 동적으로 암호화하려는 경우 자산에 대한 암호화 콘텐츠 키를 만듭니다.
2. 콘텐츠 키 인증 정책을 구성합니다.
1. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
1. 주문형 로케이터를 만들어 자산을 게시합니다.
1. 게시된 콘텐츠를 스트리밍합니다.

###미디어 분석을 사용하여 비디오에 대한 실질적인 통찰력 얻기 

미디어 분석은 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음입니다. 자세한 내용은 [Azure 미디어 서비스 분석 개요](media-services-analytics-overview.md)를 참조하세요.

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
2. 다음 미디어 분석 서비스 중 하나를 사용하여 비디오를 처리합니다.
	
	- **인덱서** – [Azure 미디어 인덱서 2를 통해 비디오 처리](media-services-process-content-with-indexer2.md)
	- **Hyperlapse** – [미디어 파일 및 Azure 미디어 Hyperlapse](media-services-hyperlapse-content.md)
	- **동작 감지** – [Azure 미디어 분석을 위한 동작 감지](media-services-motion-detection.md)
	- **얼굴 감지 및 표정** – [Azure 미디어 분석을 위한 얼굴 및 표정 감지](media-services-face-and-emotion-detection.md)
	- **비디오 요약 만들기** – [Azure 미디어 비디오 미리 보기를 사용하여 비디오 요약 만들기](media-services-video-summarization.md)
3. 미디어 분석 미디어 프로세서는 MP4 파일 또는 JSON 파일을 생성합니다. 미디어 프로세서가 MP4 파일을 생한 경우 파일을 점진적으로 다운로드할 수 있습니다. 미디어 프로세서가 JSON 파일을 생성한 경우 Azure Blob 저장소에서 해당 파일을 다운로드할 수 있습니다.


###점진적 다운로드 제공 

1. 자산에 고품질 mezzanine 파일을 업로드합니다.
1. 하나의 MP4 파일로 인코딩합니다.
1. 주문형 또는 SAS 로케이터를 만들어 자산을 게시합니다.

	주문형 로케이터를 사용하는 경우, 점진적으로 콘텐츠를 다운로드하려는 스트리밍 끝점에 하나 이상의 스트리밍 예약 단위가 있어야 합니다.

	SAS 로케이터를 사용하는 경우 콘텐츠는 Azure blob 저장소에서 다운로드됩니다. 이 경우 스트리밍 예약 단위가 필요 없습니다.
  
1. 콘텐츠를 점진적으로 다운로드합니다.

##<a id="live_scenarios"></a>Azure 미디어 서비스를 사용하여 라이브 스트리밍 이벤트 제공

라이브 스트리밍 작업을 수행할 때 일반적으로 다음 구성 요소가 관련됩니다.

- 이벤트를 브로드캐스트하는 데 사용되는 카메라
- 카메라에서 라이브 스트리밍 서비스로 보내는 스트림까지 신호를 변환하는 라이브 비디오 인코더.

필요에 따라 여러 라이브 시간에 동기화된 인코더. 뛰어난 고가용성과 체감 품질이 요구되는 특정 중요한 라이브 이벤트의 경우, 데이터 손실 없이 원활하게 장애 조치(failover)를 실현하기 위해 시간이 동기화된 활성-활성 중복 인코더를 사용하는 것이 바람직합니다.
- 다음을 수행할 수 있도록 하는 라이브 스트리밍 서비스:

- 다양한 라이브 스트리밍 프로토콜(예: RTMP 또는 부드러운 스트리밍)을 사용하여 라이브 콘텐츠 수집
- (선택 사항) 스트림을 적응 비트 전송률 스트림으로 인코딩
- 라이브 스트림 미리 보기
- 나중에 스트리밍하기 위해 수집된 콘텐츠 기록 및 저장(주문형 비디오)
- 일반적인 스트리밍 프로토콜(예: MPEG DASH, 부드러운, HLS, HDS)을 통해 고객에게 직접 또는 추가 배포를 위해 CDN(콘텐츠 배달 네트워크)에 콘텐츠 배달


**Microsoft AMS(Azure 미디어 서비스)**에서는 라이브 스트리밍 콘텐츠를 수집, 인코딩, 미리 보기, 저장 및 배달하는 기능을 제공합니다.

콘텐츠를 고객에게 배달할 때는 서로 다른 네트워크 조건에 따라 다양한 장치에 고화질 영상을 제공하는 것이 목표입니다. 품질 및 네트워크 상태를 관리하려면 라이브 인코더를 사용하여 사용자의 스트림을 다중 비트 전송률(적응 비트 전송률) 비디오 스트림으로 인코딩합니다. 여러 장치에서 스트리밍을 관리하려면 미디어 서비스 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 스트림을 여러 프로토콜에 동적으로 다시 패키징합니다. 미디어 서비스에서 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다.

Azure 미디어 서비스에서 **채널**, **프로그램** 및 **스트리밍 끝점**은 수집, 형식 지정, DVR, 보안, 확장성 및 중복성을 포함한 라이브 스트리밍 기능 전반을 처리합니다.

**채널**은 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 채널은 다음 방식으로 라이브 입력 스트림을 수신할 수 있습니다.

- 온-프레미스 라이브 인코더가 다중 비트 전송률 **RTMP** 또는 **부드러운 스트리밍**(조각화된 MP4)을 **통과** 전달을 위해 구성된 채널에 보냅니다. 어떠한 추가적인 처리 없이 수집된 스트림이 **채널**을 통과하는 경우를 **통과** 전달이라고 합니다. 다중 비트 전송률 부드러운 스트리밍을 출력하는 라이브 인코더인 Elemental, Envivio, Cisco를 사용할 수 있습니다. RTMP를 출력하는 라이브 인코더는 Adobe Flash Live, Telestream Wirecast 및 Tricaster 트랜스코더입니다. 또한 라이브 인코더는 라이브 인코딩이 사용되지 않는 채널에 단일 비트 전송률 스트림을 전송할 수 있지만 이 방법은 권장되지 않습니다. 요청된 경우 미디어 서비스는 고객에게 스트림을 배달합니다.

>[AZURE.NOTE] 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](/pricing/details/media-services/) 세부 정보를 참조하세요.

- 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 RTP(MPEG-TS), RTMP 또는 부드러운 스트리밍(조각화된 MP4) 형식의 하나로 미디어 서비스를 통해 라이브 인코딩을 수행할 수 있는 채널에 전송합니다. 그러면 채널은 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다. 요청된 경우 미디어 서비스는 고객에게 스트림을 배달합니다.


###온-프레미스 인코더(통과)에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업

다음 다이어그램에서는 **통과** 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로][live-overview2]

자세한 내용은 [온-프레미스 인코더의 다중 비트 전송률 라이브 스트림을 수신하는 채널 사용](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

###Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업

다음 다이어그램에서는 채널이 미디어 서비스를 통해 라이브 인코딩을 수행할 수 있는 라이브 스트리밍 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로][live-overview1]

자세한 내용은 [Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.


##콘텐츠 사용

Azure 미디어 서비스는 iOS 장치, Android 장치, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 응용 프로그램을 만드는 데 필요한 도구를 제공합니다. 다음 항목에서 제공하는 SDK 및 플레이어 프레임워크 링크를 사용하여 미디어 서비스의 스트리밍 미디어를 사용할 수 있는 클라이언트 응용 프로그램을 개발할 수 있습니다.

[비디오 플레이어 응용 프로그램 개발](media-services-develop-video-players.md)

##Azure CDN 사용하기

미디어 서비스는 Azure CDN과의 통합을 지원합니다. Azure CDN을 사용하도록 설정하는 방법에 대한 자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

##Media Services 계정 크기 조정하기

계정에서 프로비전할 **스트리밍 예약 단위** 및 **인코딩 예약 단위**의 수를 지정하여 **미디어 서비스**를 확장할 수 있습니다.

또한 저장소 계정을 추가하여 미디어 서비스 계정을 확장할 수 있습니다. 각 저장소 계정은 500TB로 제한됩니다. 여러 저장소 계정을 단일 미디어 서비스 계정에 연결하여 기본 제한 이상으로 저장소를 확장할 수 있습니다.

[이](media-services-portal-scale-streaming-endpoints.md) 항목은 관련 항목으로 연결됩니다.

##지원

[Azure 지원](https://azure.microsoft.com/support/options/)에서는 미디어 서비스를 포함하여 Azure에 대한 지원 옵션을 제공합니다.

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##SLA(서비스 수준 계약)

- 미디어 서비스 인코딩을 위해 REST API 트랜잭션의 99.9% 가용성을 보장합니다.
- 하나 이상의 스트리밍 단위를 구매하는 경우 스트리밍을 위해 기존 미디어 콘텐츠에 대해 99.9% 가용성을 보장하는 요청을 서비스합니다.
- 라이브 채널을 위해 실행 중인 채널이 최소 99.9%의 시간 동안 외부 연결을 사용할 수 있도록 보장됩니다.
- 콘텐츠 보호를 위해 최소 99.9%의 시간 동안 주요 요청을 처리할 것을 보장합니다.
- 인덱서를 위해 99.9%의 시간 동안 인코딩 예약 단위로 처리되는 인덱서 작업 요청을 서비스합니다.

자세한 내용은 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_0921_2016-->