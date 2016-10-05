<properties
	pageTitle="IoT Hub Gateway SDK 시작 | Microsoft Azure"
	description="Azure IoT Hub Gateway SDK를 사용할 때 이해해야 하는 주요 개념을 보여 주는 Linux용 Azure IoT Hub Gateway SDK 연습입니다."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# IoT Gateway SDK(베타) - Linux를 사용하여 시작

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## 샘플을 빌드하는 방법

시작하기 전에 먼저 Linux에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.

1. 셸을 엽니다.
2. **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에서 루트 폴더로 이동합니다.
3. **tools/build.sh** 스크립트를 실행합니다. 이 스크립트는 **cmake** 유틸리티를 사용하여 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그런 다음 솔루션을 빌드하고 테스트를 실행합니다.

> [AZURE.NOTE]  **build.sh** 스크립트를 실행할 때마다 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더가 삭제되었다가 다시 생성됩니다.

## 샘플을 실행하는 방법

1. **build.sh** 스크립트는 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **build** 폴더에 해당 출력을 생성합니다. 여기에는 이 샘플에서 사용된 두 개의 모듈이 들어 있습니다.

    빌드 스크립트는 **liblogger\_hl.so**를 **build/modules/logger/** 폴더에 배치하고 **libhello\_world\_hl.so**를 **build/modules/hello\_world/** 폴더에 배치합니다. 아래의 JSON 설정 파일에 표시된 대로 이러한 경로를 **module path** 값에 사용합니다.

2. **samples/hello\_world/src** 폴더의 **hello\_world\_lin.json** 파일은 샘플을 실행하는 데 사용할 수 있는 Linux용 예제 JSON 설정 파일입니다. 아래 표시된 예제 JSON 설정에서는 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 샘플을 실행하는 것으로 가정합니다.

3. **logger\_hl** 모듈의 경우 **args** 섹션에서 **filename** 값을 로그 데이터를 포함할 파일의 이름 및 경로로 설정합니다.

    다음은 샘플을 실행하는 폴더에 **log.txt**를 작성하는 Linux용 JSON 설정 파일의 예제입니다.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. 셸에서 **azure-iot-gateway-sdk** 폴더로 이동합니다.
4. 다음 명령을 실행합니다.
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->