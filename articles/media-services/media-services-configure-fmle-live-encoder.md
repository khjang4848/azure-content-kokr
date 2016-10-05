<properties 
	pageTitle="FMLE 인코더를 구성하여 단일 비트 전송률 라이브 스트림 보내기 | Microsoft Azure" 
	description="이 항목에서는 FMLE(Flash Media Live Encoder) 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="juliako;cenkdin;anilmur"/>

#FMLE 인코더를 사용하여 단일 비트 전송률 라이브 스트림 보내기

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

이 항목에서는 FMLE[(Flash Media Live Encoder)](http://www.adobe.com/products/flash-media-encoder.html) 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다. 자세한 내용은 [Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

이 자습서에서는 AMSE(Azure 미디어 서비스 탐색기) 도구를 사용하여 AMS(Azure 미디어 서비스)를 관리하는 방법을 보여 줍니다. 이 도구는 Windows PC에서만 실행됩니다. Mac 또는 Linux에서는 Azure 클래식 포털을 사용하여 [채널](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 및 [프로그램](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)을 만듭니다.

이 자습서에서는 AAC 사용에 대해 설명합니다. 그러나 FMLE는 AAC를 기본적으로 지원하지 않습니다. MainConcept과 같은 곳에서 AAC 인코딩에 대한 플러그 인([AAC 플러그 인](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html))을 구매해야 합니다.

##필수 조건

- [Azure 미디어 서비스 계정 만들기](media-services-create-account.md)
- 하나 이상의 스트리밍 단위가 할당된 스트리밍 끝점이 실행되고 있는지 확인합니다. 자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md)를 참조하세요.
- 최신 버전의 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 도구를 설치합니다.
- 이 도구를 시작하고 AMS 계정에 연결합니다.

##팁

- 가능하면 하드웨어에 내장된 인터넷 연결을 사용합니다.
- 대역폭 요구 사항을 결정할 때에 가장 좋은 방법은 스트리밍 비트 전송률을 두 배로 하는 것입니다. 이는 필수 요구 사항은 아니지만 네트워크 정체로 인한 영향을 줄이는 데 도움이 됩니다.
- 소프트웨어 기반 인코더를 사용하는 경우 불필요한 프로그램을 모두 닫습니다.

## 채널 만들기

1.  AMSE 도구에서 **라이브** 탭으로 이동하고 채널 영역 안을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **Create channel...**(채널 만들기...)을 선택합니다.

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. 채널 이름을 지정합니다. 설명 필드는 선택 사항입니다. 채널 설정에서 라이브 인코딩 옵션에 대해 **표준**을 선택하고 입력 프로토콜은 **RTMP**로 설정합니다. 다른 모든 설정은 그대로 유지할 수 있습니다.


**새 채널 지금 시작**이 선택되었는지 확인합니다.

3. **채널 만들기**를 클릭합니다. ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] 채널을 시작하는 데 20분 정도 걸릴 수 있습니다.


채널을 시작하는 동안 [인코더 구성](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp)을 수행할 수 있습니다.

>[AZURE.IMPORTANT] 채널이 준비 상태가 되는 즉시 요금이 청구되기 시작합니다. 자세한 내용은 [채널 상태](media-services-manage-live-encoder-enabled-channels.md#states)를 참조하세요.

##<a id=configure_fmle_rtmp></a>FMLE 인코더 구성

이 자습서에서는 다음 출력 설정이 사용됩니다. 이 섹션의 나머지 부분에서 구성 단계를 더 자세히 설명합니다.

**비디오**:
 
- 코덱: H.264
- 프로필: 높음(수준 4.0)
- 비트 전송률: 5,000kbps
- 키 프레임: 2초(60초)
- 프레임 속도: 30
 
**오디오**:

- 코덱: AAC(LC)
- 비트 전송률: 192kbps
- 샘플 속도: 44.1khz


###구성 단계

1. 사용 중인 컴퓨터에 대한 FMLE(Flash Media Live Encoder)의 인터페이스로 이동합니다.

	이 인터페이스는 기본 설정 페이지 중 하나입니다. FMLE를 사용하여 스트리밍 시작하려면 다음 권장 설정에 유의하세요.
	
	- 형식: H.264 프레임 속도: 30.00
	- 입력 크기: 1280 x 720
	- 비트 전송률: 5,000kbps(네트워크 제한 사항에 따라 조정 가능)

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

	인터레이스된 원본을 사용하는 경우 "디인터레이스" 옵션에 확인 표시를 합니다.

2. 형식 옆에 있는 렌치 아이콘을 선택합니다. 이러한 추가 설정은 다음과 같이 설정되어야 합니다.

	- 프로필: 기본
	- 수준: 4.0
	- 키 프레임 빈도: 2초
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. 다음의 중요한 오디오 설정을 설정합니다.
	
	- 형식: AAC
	- 샘플 속도: 44,100hz
	- 비트 전송률: 192kbps
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. FMLE의 **RTMP 끝점**에 할당하기 위해 채널의 입력 URL을 가져옵니다.
	
	AMSE 도구로 다시 이동하여 채널 완료 상태를 확인합니다. 상태가 **시작**에서 **실행**으로 변경되었으면 입력 URL을 가져올 수 있습니다.
	  
	채널이 실행 중일 때 채널 이름을 마우스 오른쪽 단추로 클릭하고 아래로 이동하여 **입력 URL을 클립보드로 복사**를 가리킨 다음 **(기본 입력 URL**을 선택합니다.
	
	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. 출력 섹션의 **FMS URL** 필드에 이 정보를 붙여 넣고 스트림 이름을 할당합니다.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

	추가 중복의 경우 보조 입력 URL을 사용하여 이러한 단계를 반복합니다.
8. **연결**을 선택합니다.

>[AZURE.IMPORTANT] **연결**을 클릭하기 전에 채널이 준비되었는지 **반드시** 확인합니다. 또한 15분을 초과할 때까지 입력 기여 피드 없이 채널을 준비 상태로 그대로 두지 않도록 해야 합니다.

##테스트 재생
  
1. AMSE 도구로 이동하고 테스트할 채널을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **Playback the Preview**(미리 보기 재생)를 가리키고 **with Azure Media Player**(Azure 미디어 플레이어 사용)를 선택합니다.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

스트림이 플레이어에 나타나면 인코더가 AMS에 연결되도록 제대로 구성된 것입니다.

오류가 수신되면 채널을 다시 설정해서 인코더 설정을 조정해야 합니다. 지침을 보려면 [문제 해결](media-services-troubleshooting-live-streaming.md) 항목을 참조하세요.

##프로그램 만들기

1. 채널 재생이 확인되면 프로그램을 만듭니다. AMSE 도구의 **라이브** 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 **새 프로그램 만들기**를 선택합니다.

	![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

2. 프로그램 이름을 지정하고 필요한 경우 **보관 창 길이**(기본값은 4시간)를 조정합니다. 또한 저장소 위치를 지정하거나 기본값을 그대로 둘 수 있습니다.
3. **지금 프로그램 시작** 상자를 선택합니다.
4. **프로그램 만들기**를 클릭합니다.
  
	참고: 프로그램 만들기는 채널 만들기보다 시간이 덜 걸립니다.
 
5. 프로그램이 실행되고 있으면 프로그램을 마우스 오른쪽 단추로 클릭하고 **프로그램 재생**으로 이동한 다음 **Azure 미디어 플레이어 사용**을 선택하여 재생을 확인합니다.
6. 확인되면 프로그램을 마우스 오른쪽 단추로 다시 클릭하고 **Copy the Output URL to Clipboard**(클립보드로 출력 URL 복사)를 선택하거나 메뉴의 **Program information and settings**(프로그램 정보 및 설정) 옵션에서 이 정보를 검색합니다.

이제 스트림을 플레이어에 포함하거나 실시간 보기를 위해 대상 그룹에게 배포할 준비가 되었습니다.


## 문제 해결

지침을 보려면 [문제 해결](media-services-troubleshooting-live-streaming.md) 항목을 참조하세요.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->