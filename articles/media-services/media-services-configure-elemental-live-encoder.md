<properties 
	pageTitle="Elemental 라이브 인코더를 구성하여 단일 비트 전송률 라이브 스트림 보내기 | Microsoft Azure" 
	description="이 항목에서는 Elemental 라이브 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/19/2016"
	ms.author="cenkdin;anilmur;juliako"/>

#Elemental 라이브 인코더를 사용하여 단일 비트 전송률 라이브 스트림 보내기

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

이 항목에서는 [Elemental 라이브](http://www.elementaltechnologies.com/products/elemental-live) 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다. 자세한 내용은 [Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

이 자습서에서는 AMSE(Azure 미디어 서비스 탐색기) 도구를 사용하여 AMS(Azure 미디어 서비스)를 관리하는 방법을 보여 줍니다. 이 도구는 Windows PC에서만 실행됩니다. Mac 또는 Linux에서는 Azure 클래식 포털을 사용하여 [채널](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 및 [프로그램](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)을 만듭니다.

##필수 조건

- 라이브 이벤트를 만들려면 Elemental 라이브 웹 인터페이스 사용에 대한 실무 지식이 있어야 합니다.
- [Azure 미디어 서비스 계정 만들기](media-services-create-account.md)
- 하나 이상의 스트리밍 단위가 할당된 스트리밍 끝점이 실행되고 있는지 확인합니다. 자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md)를 참조하세요.
- 최신 버전의 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 도구를 설치합니다.
- 이 도구를 시작하고 AMS 계정에 연결합니다.

##팁

- 가능하면 하드웨어에 내장된 인터넷 연결을 사용합니다.
- 대역폭 요구 사항을 결정할 때에 가장 좋은 방법은 스트리밍 비트 전송률을 두 배로 하는 것입니다. 이는 필수 요구 사항은 아니지만 네트워크 정체로 인한 영향을 줄이는 데 도움이 됩니다.
- 소프트웨어 기반 인코더를 사용하는 경우 불필요한 프로그램을 모두 닫습니다.

## RTP 수집을 사용한 Elemental 라이브

이 섹션에서는 RTP를 통해 단일 비트 전송률 라이브 스트림을 전송하는 Elemental 라이브 인코더를 구성하는 방법을 보여 줍니다. 자세한 내용은 [RTP를 통한 MPEG-TS 스트림](media-services-manage-live-encoder-enabled-channels.md#channel)을 참조하세요.

### 채널 만들기

1.  AMSE 도구에서 **라이브** 탭으로 이동하고 채널 영역 안을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **Create channel...**(채널 만들기...)을 선택합니다.

![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. 채널 이름을 지정합니다. 설명 필드는 선택 사항입니다. 채널 설정에서 라이브 인코딩 옵션에 대해 **표준**을 선택하고 입력 프로토콜은 **RTP(MPEG-TS)**로 설정합니다. 다른 모든 설정은 그대로 유지할 수 있습니다.


**새 채널 지금 시작**이 선택되었는지 확인합니다.

3. **채널 만들기**를 클릭합니다. ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] 채널을 시작하는 데 20분 정도 걸릴 수 있습니다.

채널을 시작하는 동안 [인코더 구성](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp)을 수행할 수 있습니다.

>[AZURE.IMPORTANT] 채널이 준비 상태가 되는 즉시 요금이 청구되기 시작합니다. 자세한 내용은 [채널 상태](media-services-manage-live-encoder-enabled-channels.md#states)를 참조하세요.

###<a id=configure_elemental_rtp></a>Elemental 라이브 인코더 구성 

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


####구성 단계

1. **Elemental 라이브** 웹 인터페이스로 이동하고 **UDP/TS** 스트리밍에 대한 인코더를 설정합니다.

2. 새 이벤트가 만들어지면 출력 그룹까지 아래로 스크롤하고 **UDP/TS** 출력 그룹을 추가합니다.

3. **새 스트림**을 선택한 다음 **출력 추가**를 클릭하여 새 출력을 만듭니다.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
	
	>[AZURE.NOTE] 스트림 오류가 발생한 경우 Elemental 이벤트에서 인코더를 다시 연결할 수 있도록 하는 "시스템 시계"로 설정하는 시간 코드를 갖는 것이 좋습니다.

4. 이제 출력이 만들어졌으므로 **스트림 추가**를 클릭합니다. 이제 출력 설정을 구성할 수 있습니다.
5. 방금 만든 "스트림 1"까지 아래로 스크롤하고 왼쪽의 **비디오** 탭을 클릭하고 **고급** 설정 섹션을 확장합니다.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

	Elemental 라이브는 사용 가능한 다양한 범위의 사용자 지정이 있지만 AMS에 스트리밍을 시작하는데 다음 설정이 좋습니다.
	
	- 해상도: 1280 x 720
	- 프레임 속도: 30
	- GOP 크기: 60프레임
	- 인터레이스 모드: Progressive
	- 비트 전송률: 5,000,000bit/s(네트워크 제한 사항에 따라 조정 가능)
	

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. 채널의 입력 URL을 가져옵니다.
	
	AMSE 도구로 다시 이동하여 채널 완료 상태를 확인합니다. 상태가 **시작**에서 **실행**으로 변경되었으면 입력 URL을 가져올 수 있습니다.
	  
	채널이 실행 중일 때 채널 이름을 마우스 오른쪽 단추로 클릭하고 아래로 이동하여 **입력 URL을 클립보드로 복사**를 가리킨 다음 **(기본 입력 URL**을 선택합니다.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
	
1. Elemental의 **기본 대상** 필드에 해당 정보를 붙여넣습니다. 다른 모든 설정은 기본값으로 남아 있을 수 있습니다.
	
	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

	추가 중복의 경우 UDP/TS 스트리밍에 대한 별도 "출력" 탭을 만들어서 보조 입력 URL로 이러한 단계를 반복합니다.
	
7. **만들기**(새 이벤트가 생성된 경우) 또는 **업데이트**(기존 이벤트를 편집하는 경우)를 클릭하고 인코더 시작을 진행합니다.

>[AZURE.IMPORTANT] Elemental 라이브 웹 인터페이스에서 **시작**을 클릭하기 전에 채널이 준비되었는지 **반드시** 확인해야 합니다. 또한 15분을 초과할 때까지 이벤트 없이 채널을 준비 상태로 그대로 두지 않도록 해야 합니다.

스트림을 30초 동안 실행한 후 AMSE 도구 및 테스트 재생으로 다시 이동합니다.

###테스트 재생
  
1. AMSE 도구로 이동하고 테스트할 채널을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **Playback the Preview**(미리 보기 재생)를 가리키고 **with Azure Media Player**(Azure 미디어 플레이어 사용)를 선택합니다.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

스트림이 플레이어에 나타나면 인코더가 AMS에 연결되도록 제대로 구성된 것입니다.

오류가 수신되면 채널을 다시 설정해서 인코더 설정을 조정해야 합니다. 참고 자료를 보려면 [문제 해결](media-services-troubleshooting-live-streaming.md) 항목을 참조하세요.

###프로그램 만들기

1. 채널 재생이 확인되면 프로그램을 만듭니다. AMSE 도구의 **라이브** 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 **새 프로그램 만들기**를 선택합니다.

	![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. 프로그램 이름을 지정하고 필요한 경우 **보관 창 길이**(기본값은 4시간)를 조정합니다. 또한 저장소 위치를 지정하거나 기본값을 그대로 둘 수 있습니다.
3. **지금 프로그램 시작** 상자를 선택합니다.
4. **프로그램 만들기**를 클릭합니다.
  
	참고: 프로그램 만들기는 채널 만들기보다 시간이 덜 걸립니다.
 
5. 프로그램이 실행되고 있으면 프로그램을 마우스 오른쪽 단추로 클릭하고 **프로그램 재생**으로 이동한 다음 **Azure 미디어 플레이어 사용**을 선택하여 재생을 확인합니다.
6. 확인되면 프로그램을 마우스 오른쪽 단추로 다시 클릭하고 **Copy the Output URL to Clipboard**(클립보드로 출력 URL 복사)를 선택하거나 메뉴의 **Program information and settings**(프로그램 정보 및 설정) 옵션에서 이 정보를 검색합니다.

이제 스트림을 플레이어에 포함하거나 실시간 보기를 위해 대상 그룹에게 배포할 준비가 되었습니다.

## 문제 해결

참고 자료를 보려면 [문제 해결](media-services-troubleshooting-live-streaming.md) 항목을 참조하세요.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->