<properties
 pageTitle="원격 모니터링 솔루션의 장치 정보 메타데이터 | Microsoft Azure"
 description="Azure IoT에 대한 설명은 원격 모니터링 솔루션 및 해당 아키텍처를 미리 구성합니다."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# 미리 구성된 원격 모니터링 솔루션의 장치 정보 메타데이터

미리 구성된 Azure IoT Suite 원격 모니터링 솔루션은 장치 메타데이터를 관리하기 위한 방식을 보여 줍니다. 이 문서에서는 이 솔루션이 취하는 방식을 이해할 수 있도록 설명합니다.

- 솔루션이 저장한 장치 메타데이터입니다.
- 솔루션이 장치 메타데이터를 관리하는 방법입니다.

## Context

미리 구성된 원격 모니터링 솔루션은 [Azure IoT Hub][lnk-iot-hub]를 사용하여 장치가 클라우드로 데이터를 보낼 수 있도록 합니다. IoT Hub는 [장치 ID 레지스트리][lnk-identity-registry]를 포함하여 IoT Hub에 액세스하도록 제어합니다. IoT Hub 장치 ID 레지스트리는 장치 정보 메타데이터를 저장하는 원격 모니터링 솔루션에 특정된 *장치 레지스트리*와 분리되어 있습니다. 원격 모니터링 솔루션은 [DocumentDB][lnk-docdb] 데이터베이스를 사용하여 장치 정보 메타데이터를 저장하는 해당 장치 레지스트리를 구현합니다. [Microsoft Azure IoT 참조 아키텍처][lnk-ref-arch]는 일반적인 IoT 솔루션의 장치 레지스트리 역할을 설명합니다.

> [AZURE.NOTE] 미리 구성된 원격 모니터링 솔루션은 장치 레지스트리와 동기화된 장치 ID 레지스트리를 유지합니다. 둘 다 동일한 장치 ID를 사용하여 IoT Hub에 연결된 각 장치를 고유하게 식별합니다.

[IoT Hub 장치 관리 미리 보기][lnk-dm-preview]에서는 이 문서에 설명된 장치 정보 관리 기능과 유사한 기능을 IoT Hub에 추가합니다. 현재 원격 모니터링 솔루션은 IoT Hub에서 일반적으로 사용 가능한(GA) 기능만을 사용합니다.

## 장치 정보 메타데이터

장치 레지스트리 DocumentDB 데이터베이스에 저장된 장치 정보 메타데이터 JSON 문서는 다음과 같은 구조입니다.

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: 장치 자체는 이러한 속성을 작성하고 장치에는 이 데이터에 대한 권한이 있습니다. 기타 예제 장치 속성에는 제조업체, 모델 번호 및 일련 번호가 포함됩니다.
- **DeviceID**: 고유 장치 ID입니다. 이 값은 IoT Hub 장치 ID 레지스트리에서 동일합니다.
- **HubEnabledState**: IoT Hub에서 장치의 상태입니다. 장치가 처음 연결될 때까지 이 값을 우선 **null**로 설정합니다. 솔루션 포털에서 **null** 값은 "등록되었지만 표시되지 않는" 장치로 나타납니다.
- **CreatedTime**: 장치가 만들어진 시간입니다.
- **DeviceState**: 장치에서 보고한 상태입니다.
- **UpdatedTime**: 장치가 솔루션 포털을 통해 마지막으로 업데이트된 시간입니다.
- **SystemProperties**: 솔루션 포털은 시스템 속성을 작성하며 장치에는 이러한 속성에 대한 정보가 없습니다. 솔루션에 권한이 있고 SIM을 사용한 장치를 관리하는 서비스에 연결하는 경우 예제 시스템 속성은 **ICCID**입니다.
- **Commands**: 장치가 지원하는 명령 목록입니다. 장치는 솔루션에 이 정보를 제공합니다.
- **CommandHistory**: 원격 모니터링 솔루션에서 장치 및 해당 명령의 상태에 보낸 명령 목록입니다.
- **IsSimulatedDevice**: 장치를 시뮬레이션된 장치로 식별하는 플래그입니다.
- **id**: 이 장치 문서에 대한 고유 DocumentDB 식별자입니다.

> [AZURE.NOTE] 장치 정보는 장치가 IoT Hub에 전송하는 원격 분석을 설명하는 메타데이터를 포함할 수도 있습니다. 원격 모니터링 솔루션은 원격 분석 메타데이터를 사용하여 대시보드가 [동적 원격 분석][lnk-dynamic-telemetry]을 표시하는 방법을 사용자 지정합니다.

## 수명 주기

솔루션 포털에서 장치를 처음 만드는 경우 솔루션은 이전과 같이 장치 레지스트리에서 항목을 만듭니다. 대부분의 정보는 처음부터 지워져 있고 **HubEnabledState**는 **null**로 설정됩니다. 또한 이 시점에서 솔루션은 장치가 IoT Hub로 인증하는 데 사용할 키를 생성하는 장치 ID 레지스트리에서 장치에 대한 항목을 만듭니다.

먼저 장치가 솔루션에 연결되면 장치 정보 메시지를 보냅니다. 이 장치 정보 메시지에는 장치 제조업체, 모델 번호 및 일련 번호와 같은 장치 속성이 포함됩니다. 장치 정보 메시지에는 명령 매개 변수에 대한 정보를 비롯하여 장치에서 지원하는 명령 목록도 포함됩니다. 솔루션이 이 메시지를 받으면 장치 레지스트리의 장치 정보 메타데이터를 업데이트합니다.

### 솔루션 포털에서 장치 정보 보기 및 편집

솔루션 포털의 장치 목록은 **상태**, **DeviceId**, **제조업체**, **모델 번호**, **일련 번호**, **펌웨어**, **플랫폼**, **프로세서** 및 **설치된 RAM** 등의 장치 속성을 열로 표시합니다. 장치 속성 **위도** 및 **경도**는 대시보드의 Bing 맵에서 위치를 결정합니다.

![장치 목록][img-device-list]

솔루션 포털의 **장치 세부 정보** 창에서 **편집**을 클릭하면 이러한 속성을 모두 편집할 수 있습니다. 이러한 속성을 편집하면 DocumentDB 데이터베이스의 장치에 대한 레코드가 업데이트됩니다. 그러나 장치가 업데이트된 장치 정보 메시지를 보내면 솔루션 포털의 모든 변경 내용을 덮어씁니다. 이러한 속성에 대한 권한이 장치에만 있기 때문에 솔루션 포털에서 **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState** 및 **UpdatedTime** 속성을 편집할 수 없습니다.

![장치 편집][img-device-edit]

솔루션 포털을 사용하여 솔루션에서 장치를 제거할 수 있습니다. 장치를 제거하면 솔루션은 솔루션 장치 레지스트리에서 장치 정보 메타데이터를 제거하고 IoT Hub 장치 ID 레지스트리에서 장치 항목을 제거합니다. 장치를 사용하지 않도록 설정해야 제거할 수 있습니다.

![장치 제거][img-device-remove]

## 장치 정보 메시지 처리

장치에서 보낸 장치 정보 메시지는 원격 분석 메시지와 구별됩니다. 장치 정보 메시지는 장치 속성, 장치가 응답할 수 있는 명령 및 명령 기록과 같은 정보를 포함합니다. IoT Hub 자체에는 장치 정보 메시지에 포함된 메타데이터의 정보가 없으며 장치-클라우드 메시지를 처리하는 것과 동일한 방식으로 메시지를 처리합니다. 원격 모니터링 솔루션에서 [Azure 스트림 분석][lnk-stream-analytics](ASA) 작업은 IoT Hub의 메시지를 읽습니다. **DeviceInfo** 스트림 분석 작업은 **"ObjectType": "DeviceInfo"**를 포함하는 메시지를 필터링하고 웹 작업에서 실행되는 **EventProcessorHost** 호스트 인스턴스에 전달합니다. **EventProcessorHost** 인스턴스의 논리는 장치 ID를 사용하여 특정 장치에 대한 DocumentDB 레코드를 찾고 레코드를 업데이트합니다. 이제 장치 레지스트리 레코드는 장치 속성, 명령 및 명령 기록 등의 정보를 포함합니다.

> [AZURE.NOTE] 장치 정보 메시지는 표준 장치-클라우드 메시지입니다. 솔루션은 ASA 쿼리를 사용하여 장치 정보 메시지와 원격 분석 메시지를 구분합니다.

## 예제 장치 정보 레코드

미리 구성된 원격 모니터링 솔루션은 두 종류의 장치 정보 레코드를 사용합니다. 하나는 솔루션을 사용하여 배포된 시뮬레이션된 장치에 대한 레코드이며 다른 하나는 솔루션에 연결한 사용자 지정 장치에 대한 레코드입니다.

### 시뮬레이션된 장치

다음 예제에서는 시뮬레이션된 장치에 대한 JSON 장치 정보 레코드를 보여 줍니다. 이 레코드에는 장치가 IoT Hub에 **DeviceInfo** 메시지를 전송했음을 나타내는 **UpdatedTime**에 대한 값 집합이 있습니다. 레코드에는 몇 가지 일반적인 장치 속성이 포함되며, 시뮬레이션된 장치가 지원하는 6개의 명령을 정의하고 **1**로 설정된 **IsSimulatedDevice** 플래그가 있습니다.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### 사용자 지정 장치

다음 예제에서는 사용자 지정 장치에 대한 JSON 장치 정보 레코드를 보여 주고 **0**으로 설정된 **IsSimulatedDevice** 플래그가 포함됩니다. 이 사용자 지정 장치가 두 개의 명령을 지원하고 솔루션 포털에서 장치에 **SetTemperature** 명령을 전송한 것을 확인할 수 있습니다.

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

다음은 장치에서 장치 정보 메타데이터를 업데이트하도록 전송한 JSON **DeviceInfo** 메시지를 보여 줍니다.

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## 다음 단계

지금까지 미리 구성된 솔루션은 사용자 지정하는 방법을 살펴보았으므로 IoT Suite의 미리 구성된 솔루션이 제공하는 기능 및 기타 특징에 대해 살펴보겠습니다.

- [예측 정비 사전 구성 솔루션 개요][lnk-predictive-overview]
- [IoT Suite에 대한 질문과 대답][lnk-faq]
- [처음부터 IoT 보안을 고려][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0914_2016-->