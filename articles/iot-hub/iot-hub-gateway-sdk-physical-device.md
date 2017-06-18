<properties
	pageTitle="실제 장치에서 게이트웨이 SDK 사용 | Microsoft Azure"
	description="Texas Instruments SensorTag를 사용하여 Intel Edison 계산 모듈에서 실행되는 게이트웨이를 통해 IoT Hub로 데이터를 전송하는 Azure IoT Hub 게이트웨이 SDK 연습"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/31/2016"
     ms.author="cstreet"/>


# IoT Gateway SDK(베타) – Linux를 사용하는 실시간 장치에서 장치-클라우드 메시지 보내기

이 [Bluetooth 저에너지 샘플][lnk-ble-samplecode] 연습에서는 [Microsoft Azure IoT 게이트웨이 SDK][lnk-sdk]를 사용하여 물리적 장치에 IoT Hub로 장치-클라우드 원격 분석을 전달하는 방법과 IoT Hub에서 물리적 장치로 명령을 라우팅하는 방법을 보여 줍니다.

이 연습에서는 다음 내용을 다룹니다.

* **아키텍처**: Bluetooth 저에너지 샘플에 대한 중요한 아키텍처 정보입니다.

* **빌드 및 실행**: 샘플을 빌드하고 실행하는 데 필요한 단계입니다.

## 아키텍처

이 연습에서는 Linux를 실행하는 Intel Edison 계산 모듈에서 IoT 게이트웨이를 빌드 및 실행하는 방법을 보여 줍니다. 이 게이트웨이는 IoT 게이트웨이 SDK를 사용하여 빌드됩니다. 이 샘플은 Texas Instruments SensorTag BLE(Bluetooth 저에너지) 장치를 사용하여 온도 데이터를 수집합니다.

게이트웨이 실행하면 다음 작업이 수행됩니다.

- BLE(Bluetooth 저에너지) 프로토콜을 사용하여 SensorTag 장치에 연결합니다.
- AMQP 프로토콜을 사용하여 IoT Hub에 연결합니다.
- SensorTag 장치에서 IoT Hub로 원격 분석을 전달합니다.
- IoT Hub에서 SensorTag 장치로 명령을 라우팅합니다.

게이트웨이에는 다음 모듈이 포함됩니다.

- *BLE 모듈* - BLE 장치와 연결되며, 장치에서 온도 데이터를 수신하고 장치로 명령을 보냅니다.
- *로거 모듈* - 메시지 버스 진단을 생성합니다.
- *ID 매핑 모듈* - BLE 장치 MAC 주소 및 Azure IoT Hub 장치 ID 간을 변환합니다.
- *IoT Hub HTTP 모듈* - IoT hub에 원격 분석 데이터를 업로드하고 IoT hub에서 장치 명령을 수신합니다.
- *BLE 프린터 모듈* - BLE 장치의 원격 분석을 해석하고 형식이 지정된 데이터를 콘솔에 출력하여 문제 해결 및 디버깅을 사용하도록 설정합니다.

### 게이트웨이를 통해 데이터가 흐르는 방식

다음 블록 다이어그램에서는 원격 분석 업로드 데이터 흐름 파이프라인을 보여 줍니다.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

원격 분석 항목이 BLE 장치에서 IoT Hub로 이동하면서 진행되는 단계는 다음과 같습니다.

1. BLE 장치가 온도 샘플을 생성한 후 Bluetooth를 통해 게이트웨이의 BLE 모듈로 보냅니다.
2. BLE 모듈이 샘플을 수신한 후 장치의 MAC 주소와 함께 메시지 버스에 게시합니다.
3. ID 매핑 모듈이 메시지 버스에서 이 메시지를 선택하고 내부 표를 사용하여 장치의 MAC 주소를 IoT Hub 장치 ID(장치 ID 및 장치 키)로 변환합니다. 그런 후 새 메시지를 온도 샘플 데이터, 장치의 MAC 주소, 장치 ID 및 장치 키가 포함된 메시지 버스에 게시합니다.
4. IoT Hub HTTP 모듈은 메시지 버스에서 새 메시지(ID 매핑 모듈에 의해 생성)를 수신한 후 IoT bub에 게시합니다.
5. 로거 모듈이 메시지 버스의 모든 메시지를 디스크 파일에 기록합니다.

다음 블록 다이어그램에서는 장치 명령 데이터 흐름 파이프라인을 보여 줍니다.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. IoT Hub HTTP 모듈은 새로운 명령 메시지에 대한 IoT Hub를 주기적으로 폴링합니다.
2. IoT Hub HTTP 모듈은 새 명령 메시지를 받으면 메시지 버스에 게시합니다.
3. ID 매핑 모듈은 메시지 버스에서 명령 메시지를 선택하고 내부 표를 사용하여 IoT Hub 장치 ID를 장치 MAC 주소로 변환합니다. 그런 다음 새 메시지를 메시지의 속성 맵에 대상 장치의 MAC 주소가 포함된 메시지 버스에 게시합니다.
4. BLE 모듈은 이 메시지를 선택하고 BLE 장치와 통신하여 I/O 명령을 실행합니다.
5. 로거 모듈이 메시지 버스의 모든 메시지를 디스크 파일에 기록합니다.

## 하드웨어 준비

이 자습서에서는 사용자가 Intel Edison 보드에 연결된 [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) 장치를 사용한다고 가정합니다.

### Edison 보드 설정

시작하기 전에 Edison 장치를 무선 네트워크에 연결할 수 있는지 확인해야 합니다. Edison 장치를 설정하려면 호스트 컴퓨터에 연결해야 합니다. Intel에서는 다음 운영 체제에 대해 시작 가이드를 제공합니다.

- [Windows 64비트에서 Intel Edison 개발 보드 시작][lnk-setup-win64]
- [Windows 32비트에서 Intel Edison 개발 보드 시작][lnk-setup-win32]
- [Mac OS X에서 Intel Edison 개발 보드 시작][lnk-setup-osx]
- [Linux에서 Intel® Edison 보드 시작][lnk-setup-linux]

Edison 장치를 설정하고 친숙해지려면 현재 자습서에서는 불필요한 마지막 단계인 "IDE 선택"을 제외하고 이 "시작" 문서의 모든 단계를 완료해야 합니다. Edison 설치 프로세스가 끝나면 다음과 같은 작업이 완료됩니다.

- Edison이 최신 펌웨어로 업데이트됩니다.
- 호스트에서 Edison으로의 직렬 연결이 설정됩니다.
- **configure\_edison** 스크립트를 실행하여 암호를 설정하고 Edison에서 WiFi를 사용하도록 설정합니다.

### Edison 보드에서 SensorTag 장치로의 연결 설정

이 샘플을 실행하기 전에 Edison 보드에서 SensorTag 장치에 연결할 수 있는지 확인해야 합니다.

먼저 Edison에서 SensorTag 장치에 연결할 수 있는지 확인해야 합니다.

1. Edison에서 Bluetooth를 차단 해제하고 버전 번호가 **5.37**인지 확인합니다.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. **bluetoothctl** 명령을 실행합니다. 현재 대화형 Bluetooth 셸에서 작업하고 있습니다.

3. **power on** 명령을 입력하여 bluetooth 컨트롤러에 전원을 공급합니다. 다음과 유사한 결과가 표시됩니다.
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. 아직 대화형 Bluetooth 셸에 있는 상태에서 **scan on** 명령을 입력하여 Bluetooth 장치를 검색합니다. 다음과 유사한 결과가 표시됩니다.
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. 작은 단추(녹색 표시등이 깜박거림)를 눌러 SensorTag 장치를 검색 가능하게 합니다. Edison은 SensorTag 장치를 검색해야 합니다.
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    이 예제에서는 SensorTag 장치의 MAC 주소가 **A0:E6:F8:B5:F6:00**이라는 것을 확인할 수 있습니다.

6. **scan off** 명령을 입력하여 스캔을 해제합니다.
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. **connect <MAC 주소>**를 입력하고 MAC 주소를 사용하여 SensorTag 장치에 연결합니다. 아래의 샘플 출력은 축약 형태입니다.
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    참고: **list-attributes** 명령을 사용하여 장치의 GATT 특성을 다시 나열할 수 있습니다.

8. 이제 **disconnect** 명령을 사용하여 장치에서 연결을 끊은 다음 **quit** 명령을 사용하여 Bluetooth 셸을 끝낼 수 있습니다.
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

이제 Edison 장치에서 BLE 게이트웨이 샘플 실행할 준비가 되었습니다.

## BLE 게이트웨이 샘플 실행

Edison에서 BLE 샘플을 실행하려면 다음 3가지 작업을 완료해야 합니다.

- IoT Hub에서 두 개의 샘플 장치를 구성합니다.
- Edison 장치에서 게이트웨이 SDK를 빌드합니다.
- Edison 장치에서 BLE 샘플을 구성하고 실행합니다.

작성할 때, 게이트웨이 SDK는 Linux에서 BLE 모듈을 사용하는 게이트웨이만 지원합니다.

### IoT Hub에서 두 개의 샘플 장치 구성

- Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 연습을 완료하려면 허브 이름이 필요합니다. Azure 구독이 아직 없는 경우 [무료 계정][lnk-free-trial]을 얻을 수 있습니다.
- **SensorTag\_01**이라는 장치 1개를 IoT hub에 추가하고 해당 ID와 장치 키를 적어둡니다. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다. 게이트웨이를 구성할 때 이 장치를 SensorTag 장치에 매핑합니다.

### Edison 장치에서 게이트웨이 SDK 빌드

Edison의 **git** 버전이 하위 모듈을 지원하지 않습니다. 게이트웨이 SDK에 대한 전체 소스를 Edison에 다운로드하려면 다음 두 가지 옵션이 있습니다.

- 옵션 #1: Edison의 [Microsoft Azure IoT 게이트웨이 SDK][lnk-sdk] 리포지토리를 복제한 후 각 하위 모듈에 대한 리포지토리를 수동으로 복제합니다.
- 옵션 #2: **git**가 하위 모듈을 지원하는 데스크톱 장치에서 [Microsoft Azure IoT 게이트웨이 SDK][lnk-sdk] 리포지토리를 복제한 후 하위 모듈을 포함하는 전체 리포지토리를 Edison에 복사합니다.

옵션 #2를 선택하는 경우 다음 **git** 명령을 사용하여 게이트웨이 SDK 및 해당 하위 모듈을 복제합니다.

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

그런 후 전체 로컬 저장소를 Edison에 복사하기 전에 단일 보관 파일에 압축해야 합니다. **Putty**에 포함된 **pscp**와 같은 유틸리티를 사용하여 보관 파일을 Edison에 복사할 수 있습니다. 예:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Edison에 게이트웨이 SDK 저장소의 전체 복사본이 있는 경우 다음 명령을 사용하여 SDK가 포함된 폴더에서 빌드할 수 있습니다.

```
./tools/build.sh
```

### Edison 장치에서 BLE 샘플 구성 및 실행

샘플을 부트스트랩하고 실행하려면 게이트웨이에서 참여하는 각 모듈을 구성해야 합니다. 이 구성은 JSON 파일에 제공되며 참여하는 5가지 모듈을 모두 구성해야 합니다. 리포지토리에는 구성 파일을 직접 작성하기 위한 시작 지점으로 사용할 수 있는 **gateway\_sample.json**이라는 샘플 JSON 파일이 제공되어 있습니다. 이 파일은 게이트웨이 SDK 리포지토리의 로컬 복사본에 있는 **samples/ble\_gateway\_hl/src**에 있습니다.

다음 섹션에서는 BLE 샘플에 대해 이 구성 파일을 편집하는 방법을 설명하고 게이트웨이 SDK 리포지토리가 Edison 장치의 **/home/root/azure-iot-gateway-sdk/** 폴더에 있다고 가정합니다. 리포지토리가 다른 위치에 있으면 그에 맞게 경로를 조정해야 합니다.

#### 로거 구성

게이트웨이 리포지토리가 **/home/root/azure-iot-gateway-sdk/** 폴더에 있다고 가정하고 다음과 같이 로거 모듈을 구성합니다.

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### BLE 모듈 구성

BLE 장치에 대한 샘플 구성에서는 Texas Instruments SensorTag 장치를 가정합니다. GATT 주변 기기로 작동할 수 있는 모든 표준 BLE 장치가 작동되어야 하지만 GATT 특성 ID 및 데이터를 업데이트해야 합니다(명령 쓰기용). SensorTag 장치의 MAC 주소를 추가합니다.

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### IoT Hub HTTP 모듈

IoT Hub의 이름을 추가합니다. 일반적으로 접미사 값은 **azure-devices.net**입니다.

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothubhttp/libiothubhttp_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>"
  }
}
```

#### ID 매핑 모듈 구성

SensorTag 장치의 MAC 주소와 IoT Hub에 추가된 **SensorTag\_01** 장치의 장치 ID 및 키를 추가합니다.

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### BLE 프린터 모듈 구성

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

샘플을 실행하려면 JSON 구성 파일에 경로를 전달하는 **ble\_gateway\_hl** 이진 파일을 실행합니다. **gateway\_sample.json** 파일을 사용하는 경우 실행할 명령은 다음과 같습니다.

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

샘플을 실행하기 전에 SensorTag에 있는 작은 단추를 눌러 검색을 가능하게 만들어야 할 수 있습니다.

샘플을 실행할 경우 [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 SensorTag 장치에서 게이트웨이가 전달하는 메시지를 모니터링할 수 있습니다.

## 클라우드-장치 메시지 보내기

또한 BLE 모듈은 Azure IoT Hub에서 장치로 명령을 보내도록 지원합니다. [Azure IoT Hub 장치 탐색기](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) 또는 [IoT Hub 탐색기](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer)를 사용하여 BLE 게이트웨이 모듈이 BLE 장치에 전달하는 JSON 메시지를 보낼 수 있습니다. 예를 들어 Texas Instruments SensorTag 장치를 사용하는 경우 다음 JSON 메시지를 IoT Hub에서 장치에 보낼 수 있습니다.

- 모든 표시등 및 버저 다시 설정(해제)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- I/O를 '원격'으로 구성

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 빨간색 표시등 켜기

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- 녹색 표시등 켜기

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- 버저 켜기

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

HTTP 프로토콜을 사용하여 IoT Hub에 연결하는 장치의 기본 동작은 25분 간격으로 새 명령을 확인하는 것입니다. 따라서 여러 가지 개별 명령을 보내는 경우 장치에서 각 명령을 수신할 때까지 25분 동안 기다려야 합니다.

> [AZURE.NOTE] 게이트웨이는 시작될 때마다 새 명령을 확인하므로 게이트웨이를 중지했다가 시작하여 명령을 강제로 처리할 수 있습니다.

## 다음 단계

게이트웨이 SDK와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 다음 개발자 자습서 및 리소스를 참고하세요.

- [게이트웨이 장치 관리][lnk-manage-devices]
- [Azure IoT Gateway SDK][lnk-gateway-sdk]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->