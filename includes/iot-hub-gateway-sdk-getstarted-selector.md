> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

이 문서는 [Azure IoT Gateway SDK][lnk-gateway-sdk] 아키텍처의 기본적인 구성 요소를 설명하기 위해 [Hello World 샘플 코드][lnk-helloworld-sample]에 대한 자세한 연습을 제공합니다. 샘플에서는 Gateway SDK를 사용하여 5초마다 파일에 "hello world" 메시지를 기록하는 간단한 게이트웨이를 작성합니다.

이 연습에서는 다음 내용을 다룹니다.

- **개념**: Gateway SDK를 사용하여 만드는 게이트웨이를 구성하는 구성 요소에 대한 개념적인 개요입니다.
- **Hello World 샘플 아키텍처**: Hello World 샘플에 개념이 어떻게 적용되는지 구성 요소가 어떻게 배치되는지를 설명합니다.
- **샘플 작성 방법**: 샘플 작성에 필요한 단계입니다.
- **샘플 실행 방법**: 샘플 실행에 필요한 단계입니다.
- **일반적인 출력**: 샘플을 실행할 때 예상되는 출력의 예입니다.
- **코드 조각**: Hello World 샘플이 주요 게이트웨이 구성 요소를 구현하는 방식을 보여주는 코드 조각 컬렉션입니다.

## Gateway SDK 개념

샘플 코드를 점검하거나 Gateway SDK를 사용하여 자체적인 게이트웨이를 만들기 전에, SDK 아키텍처의 토대가 되는 주요 개념을 이해해야 합니다.

### 모듈

*모듈*을 만들고 조합하여 Azure IoT Gateway SDK를 통해 게이트웨이를 만듭니다. 모듈은 *메시지*를 사용하여 서로 데이터를 교환합니다. 모듈은 메시지를 수신하고, 그에 대해 작업을 수행하고, 필요에 따라 새 메시지로 변환한 다음, 다른 모듈이 처리하도록 게시합니다. 일부 모듈은 새 메시지만 생성하고, 들어오는 메시지를 전혀 처리하지 않습니다. 일련의 모듈은 각 모듈이 파이프라인 내의 한 지점에서 데이터에 대한 변환을 수행하는 데이터 처리 파이프라인을 만듭니다.

![][1]
 
SDK는 다음을 포함합니다.

- 일반 게이트웨이 기능을 수행하는 미리 작성된 모듈.
- 일반 모듈을 작성하기 위해 개발자가 사용할 수 있는 인터페이스.
- 모듈 집합을 배포하고 실행하는 데 필요한 인프라.

SDK는 다양한 운영 체제 및 플랫폼에서 실행되는 게이트웨이를 만들 수 있는 추상화 계층을 제공합니다.

![][2]

### 메시지

모듈이 서로 메시지를 전달하는 것으로 생각하는 것이 게이트웨이 작동 방식을 개념화하는 편리한 방식이지만, 이것은 벌어지는 상황을 정확하게 반영하지는 못합니다. 모듈은 broker를 사용하여 서로 통신하고 broker(버스, pubsub 또는 다른 메시징 패턴)에 메시지를 게시하며 broker가 연결된 모듈에 메시지를 라우팅할 수 있도록 합니다.

모듈은 **Broker\_Publish** 함수를 사용하여 broker에 메시지를 게시합니다. broker는 콜백 함수를 호출하여 모듈에 메시지를 배달합니다. 메시지는 키/값 속성 및 메모리 블록으로 전달되는 콘텐츠의 집합으로 구성됩니다.

![][3]

### 메시지 라우팅 및 필터링

올바른 모듈에 메시지를 전달하는 두 가지 방법이 있습니다. broker가 원본 및 각 모듈에 대한 싱크를 알거나 모듈이 메시지의 속성을 필터링할 수 있도록 링크 집합을 broker에 전달할 수 있습니다. 그렇게 의도한 경우에 모듈은 메시지에서만 작동해야 합니다. 링크 및 메시지 필터링은 메시지 파이프라인을 효과적으로 만드는 기능입니다.

## Hello World 샘플 아키텍처

Hello World 샘플은 이전 섹션에서 설명한 개념을 보여줍니다. Hello World 샘플은 두 가지 모듈로 구성된 파이프라인이 있는 게이트웨이를 구현합니다.

-	*hello world* 모듈은 5초마다 메시지를 생성하여 로거 모듈에 전달합니다.
-	*로거* 모듈은 수신하는 메시지를 파일에 기록합니다.

![][4]

이전 섹션의 설명처럼, Hello World 모듈은 로거 모듈에 5초마다 메시지를 직접 전달하지 않습니다. 대신, 5초마다 broker에 메시지를 게시합니다.

로거 모듈은 broker에서 메시지를 받고 broker에서 메세지를 실행하며 메시지의 내용을 파일에 기록합니다.

로거 모듈은 broker의 메시지를 사용하기만 하고 broker에 새 메시지를 게시하지는 않습니다.

![][5]

위 그림은 Hello World 샘플의 아키텍처와 [리포지토리][lnk-gateway-sdk]에서 샘플의 다른 부분을 구현하는 원본 파일에 대한 상대 경로를 보여줍니다. 코드를 직접 알아보거나 아래 코드 조각을 참조용으로 사용합니다.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

<!---HONumber=AcomDC_0928_2016-->