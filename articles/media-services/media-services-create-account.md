<properties
	pageTitle="미디어 서비스 계정 만들기 | Microsoft Azure"
	description="Azure에서 새 Azure 미디어 서비스 계정을 만드는 방법을 설명합니다."
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
	ms.topic="get-started-article"
	ms.date="09/26/2016"
	ms.author="juliako"/>


# Azure 미디어 서비스 계정 만들기

> [AZURE.SELECTOR]
- [포털](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST (영문)](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](/pricing/free-trial/?WT.mc_id=A261C142F)을 참조하세요.
 
Azure 클래식 포털을 통해 Azure 미디어 서비스 계정을 신속하게 만들 수 있습니다. 계정을 사용하여 Azure에서 미디어 콘텐츠를 저장, 암호화, 인코딩, 관리 및 스트리밍할 수 있는 미디어 서비스에 액세스할 수 있습니다. 미디어 서비스 계정을 만들 때 미디어 서비스 계정과 동일한 지역에 관련 저장소 계정도 만들거나 기존 저장소 계정을 사용합니다.

이 문서에서는 빠른 생성 방법을 사용하여 새 미디어 서비스 계정을 만든 후 저장소 계정과 연결하는 방법을 설명합니다.

<a id="concepts"></a>
## 개념

미디어 서비스에 액세스하려면 다음 두 개의 관련 계정이 필요합니다.

-   **미디어 서비스 계정**. 계정을 통해 Azure에서 사용할 수 있는 클라우드 기반 미디어 서비스 집합에 액세스할 수 있습니다. 미디어 서비스 계정은 실제 미디어 콘텐츠를 저장하지 않습니다. 대신, 미디어 콘텐츠 및 미디어 처리 작업에 대한 메타데이터를 계정에 저장합니다. 계정을 만들 때 사용 가능한 미디어 서비스 영역을 선택합니다. 선택한 영역은 계정에 대한 메타데이터 레코드를 저장하는 데이터 센터입니다.

	사용 가능한 미디어 서비스(AMS) 지역에는 다음이 포함됩니다. 북유럽, 서유럽, 미국 서부, 미국 동부, 동남 아시아, 동아시아, 일본 서부, 일본 동부 미디어 서비스는 선호도 그룹을 사용하지 않습니다.
	
	AMS는 이제 다음 데이터 센터에서도 사용 가능합니다. 브라질 남부, 인도 서부, 인도 남부 및 인도 중부 이제 Azure 클래식 포털을 사용하여 [미디어 서비스 계정을 만들고](media-services-create-account.md#create-a-media-services-account-using-quick-create) [여기](https://azure.microsoft.com/documentation/services/media-services/)에서 설명한 다양한 작업을 수행할 수 있습니다. 그러나 라이브 인코딩은 이러한 데이터 센터에서 활성화되지 않습니다. 또한 모든 유형의 인코딩 예약 단위를 이러한 데이터 센터에서 사용할 수 없습니다.
	
	- 브라질 남부: 표준 및 기본 인코딩 예약 단위만 사용 가능
	- 인도 서부, 인도 남부 및 인도 중부: 기본 인코딩 예약 단위만 사용 가능


-   **관련 저장소 계정**. 저장소 계정은 미디어 서비스 계정과 관련된 Azure 저장소 계정입니다. 저장소 계정은 미디어 파일에 대한 Blob 저장소를 제공하며, 미디어 서비스 계정과 동일한 지역에 있어야 합니다. 미디어 서비스 계정을 만들 때 동일한 지역의 기존 저장소 계정을 선택하거나 동일한 지역에 새 저장소 계정을 만들 수 있습니다. 미디어 서비스 계정을 삭제하는 경우 관련 저장소 계정의 Blob은 삭제되지 않습니다.

<a id="quick"></a>
## 빠른 생성을 사용하여 미디어 서비스 계정 만들기

1. [Azure 클래식 포털][]에서 **새로 만들기**, **미디어 서비스** 및 **빠른 생성**을 차례로 클릭합니다.

![미디어 서비스 빠른 생성](./media/media-services-create-account/wams-QuickCreate.png)

2. **이름**에 새 계정의 이름을 입력합니다. 미디어 서비스 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.

3. **지역**에서 미디어 서비스 계정에 대한 메타데이터 레코드를 저장하는 데 사용할 지역을 선택합니다. 사용 가능한 미디어 서비스 지역만 드롭다운 목록에 표시됩니다.

4. **저장소 계정**에서 미디어 서비스 계정의 미디어 콘텐츠가 포함된 Blob 저장소를 제공할 저장소 계정을 선택합니다. 미디어 서비스 계정과 동일한 지역의 기존 저장소 계정을 선택하거나 새 저장소 계정을 만들 수 있습니다. 동일한 지역에 새 저장소 계정이 생성됩니다.

5. 새 저장소 계정을 만든 경우 **새 저장소 계정 이름**에 저장소 계정의 이름을 입력합니다. 저장소 계정 이름에 대한 규칙은 미디어 서비스 계정의 경우와 같습니다.

6. 양식 맨 아래에 있는 **빠른 생성**을 클릭합니다.

창 아래쪽에 있는 메시지 영역에서 프로세스의 상태를 모니터링할 수 있습니다.

계정이 생성되면 상태가 활성으로 변경됩니다. **미디어 서비스** 페이지가 열리고 새 계정이 표시됩니다.

페이지의 맨 아래에 **키 관리** 단추가 나타납니다. 이 단추를 클릭하면 미디어 서비스 계정 이름과 기본 키 및 보조 키가 포함된 페이지가 표시됩니다. 프로그래밍 방식으로 미디어 서비스 계정에 액세스하려면 계정 이름과 기본 키 정보가 필요합니다.

![미디어 서비스 페이지](./media/media-services-create-account/wams-mediaservices-page.png)

계정 이름을 두 번 클릭하면 기본적으로 **빠른 시작** 페이지가 표시됩니다. 이 페이지를 통해 포털의 다른 페이지에서도 사용할 수 있는 몇 가지 관리 작업을 수행할 수 있습니다. 예를 들어 이 페이지에서 비디오 파일을 업로드하거나 **콘텐츠** 페이지에서 업로드할 수 있습니다.

뿐만 아니라 Azure 미디어 서비스 SDK를 사용하여 동영상을 업로드, 인코드 및 게시하는 작업을 완료하는 코드를 볼 수 있습니다. **코드 작성** 섹션의 링크 중 하나를 클릭하고 코드를 복사한 후 응용 프로그램에서 사용할 수 있습니다.



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## 다음 단계

- [.NET SDK를 사용한 VoD(주문형 비디오) 콘텐츠 제공 시작](media-services-dotnet-get-started.md)

- [.NET SDK를 사용하여 단일 비트 전송률에서 다중 비트 전송률 스트림으로 라이브 인코딩을 수행하는 채널 만들기](media-services-dotnet-creating-live-encoder-enabled-channel.md)

<!-- Reusable paths. -->

<!-- Anchors. -->
  [Concepts]: #concepts
  [Before you begin]: #begin
  [How to: Create a Media Services account using Quick Create]: #quick

<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386

  [Azure 클래식 포털]: http://manage.windowsazure.com/

<!---HONumber=AcomDC_0928_2016-->