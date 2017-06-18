<properties
 pageTitle="Azure IoT Hub SDK 목록 | Microsoft Azure"
 description="다양한 Azure IoT Hub 장치 및 서비스 SDK에 대한 링크와 정보입니다."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/13/2016"
 ms.author="dobett"/>

# IoT Hub SDK

이 문서에서는 [Microsoft Azure IoT SDK][]에 대한 다양한 정보와 추가 리소스 링크를 제공합니다.

## IoT Hub 장치 SDK

Microsoft Azure IoT 장치 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 장치와 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 IoT 장치 SDK는 GitHub에서 다운로드할 수 있습니다.

- [C용 Azure IoT 장치 SDK][] - 이식성과 광범위한 플랫폼 호환성을 위해 ANSI C(C99)로 작성됨
- [.NET용 Azure IoT 장치 SDK][]
- [Java용 Azure IoT 장치 SDK][]
- [Node.js용 Azure IoT 장치 SDK][]
- [Python 2.7용 Microsoft Azure IoT 장치 SDK][]

### OS 플랫폼 및 하드웨어 호환성

특정 하드웨어 장치 호환성에 대한 자세한 내용은 다음 문서를 참조하세요.

- [OS 플랫폼 및 장치 SDK와의 하드웨어 호환성][OS Platforms and hardware compatibility]
- [IoT용 Microsoft Azure Certified 프로그램][].

## IoT Hub 서비스 SDK

Microsoft Azure IoT 서비스 SDK에는 장치와 보안을 관리하기 위해 IoT Hub 서비스를 직접 조작하는 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

다음 IoT 서비스 SDK는 GitHub에서 다운로드할 수 있습니다.

- [Node.js용 Azure IoT 서비스 SDK][]
- [Java용 Azure IoT 서비스 SDK][]

## Azure IoT Gateway SDK

이 Azure IoT Gateway SDK는 IoT 게이트웨이 솔루션을 만드는 인프라 및 모듈을 포함합니다. SDK를 확장하여 모든 종단 간 시나리오에 맞는 게이트웨이를 만들 수 있습니다.

GitHub에서 [Azure IoT Gateway SDK][]를 다운로드할 수 있습니다.

## 온라인 API 참조 설명서

다음은 Azure IoT 장치, 서비스 및 게이트웨이 라이브러리용 온라인 API 참조 설명서에 대한 링크 목록입니다.

- [IoT(사물 인터넷) .NET][]
- [IoT Hub REST][]
- [C용 Microsoft Azure IoT 장치 SDK][]
- [Java용 Microsoft Azure IoT 장치 SDK][]
- [Java용 Microsoft Azure IoT 서비스 SDK][]
- [Node.js용 Microsoft Azure IoT 장치 SDK][]
- [Node.js용 Microsoft Azure IoT 서비스 SDK][]
- [Microsoft Azure IoT gateway SDK][]

## 다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

[Microsoft Azure IoT SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[C용 Azure IoT 장치 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[.NET용 Azure IoT 장치 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[Java용 Azure IoT 장치 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[Java용 Azure IoT 서비스 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[Node.js용 Azure IoT 장치 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[Node.js용 Azure IoT 서비스 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[Python 2.7용 Microsoft Azure IoT 장치 SDK]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[IoT용 Microsoft Azure Certified 프로그램]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[IoT(사물 인터넷) .NET]: https://msdn.microsoft.com/library/mt488521.aspx
[C용 Microsoft Azure IoT 장치 SDK]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[Java용 Microsoft Azure IoT 장치 SDK]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[Node.js용 Microsoft Azure IoT 장치 SDK]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.14/index.html
[IoT Hub REST]: https://msdn.microsoft.com/library/mt548492.aspx
[Java용 Microsoft Azure IoT 서비스 SDK]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[Node.js용 Microsoft Azure IoT 서비스 SDK]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.16/index.html
[Microsoft Azure IoT gateway SDK]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->