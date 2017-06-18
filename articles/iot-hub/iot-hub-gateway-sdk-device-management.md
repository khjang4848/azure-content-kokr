<properties
	pageTitle="게이트웨이 SDK를 사용하는 장치 관리 | Microsoft Azure"
	description="게이트웨이 SDK를 사용하는 경우 장치 관리를 구현하는 방법을 보여 주는 Azure IoT Hub 게이트웨이 SDK 연습"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# IoT 게이트웨이 SDK(베타) – 게이트웨이 SDK를 사용한 장치 관리

이 자습서에서는 [Azure IoT Hub 게이트웨이 SDK][lnk-gateway-sdk]와 함께 IoT Hub의 [장치 관리][lnk-device-management] 기능을 사용하는 방법을 보여 줍니다. 자습서에서는 Intel Edison 계산 모듈을 사용하여 IoT Hub로 원격 분석을 전송하는 시뮬레이션된 장치와 함께 게이트웨이를 호스팅합니다. IoT Hub에서 장치 관리 기능을 사용하여 Edison 보드의 원격 펌웨어 업데이트를 수행합니다.

이 자습서에서는 다음 내용을 다룹니다.

- **아키텍처**: 장치 관리 샘플에 대한 중요한 아키텍처 정보입니다.

- **빌드 및 실행**: 샘플을 빌드하고 실행하는 데 필요한 단계입니다.

## 아키텍처

이 자습서에서는 IoT Hub에 연결된 IoT 장치 게이트웨이의 역할을 하는 Intel Edison 보드를 프로비전합니다. 또한 IoT Hub를 통해 관리할 수 있도록 Edison 보드를 구성합니다. 다음 다이어그램에서는 이 자습서에서 작성한 솔루션의 핵심 요소를 보여 줍니다.

![게이트웨이 및 장치 관리 아키텍처][img-architecture]

### 장치

이 솔루션에는 IoT Hub에 연결된 세 개의 IoT 장치가 있습니다.

- Edison 보드는 **GW-device**라는 장치입니다. 이 자습서에서는 IoT Hub의 장치 관리 기능을 사용하여 물리적 장치의 펌웨어를 업데이트합니다.

- 시뮬레이션된 두 개의 장치(**GW-ble1-demo** 및 **GW-ble2-demo**)입니다. 이러한 장치는 게이트웨이를 통해 IoT Hub에 연결하고 허브에 온도 원격 분석을 전송하는 Bluetooth 저에너지 장치를 시뮬레이션합니다.

### 게이트웨이 소프트웨어

게이트웨이 소프트웨어는 Edison 보드에서 서비스로 실행됩니다. 시뮬레이션된 두 개의 장치는 온도 원격 분석을 생성합니다. 매핑 모듈은 IoT Hub를 사용하여 등록된 장치에 이러한 시뮬레이션된 장치를 매핑하고 HTTP 모듈은 IoT Hub 끝점을 사용하여 통신을 처리합니다. [IoT 게이트웨이 SDK – 시뮬레이션된 장치를 사용하여 장치-클라우드 메시지 보내기][lnk-gateway-scenario] 문서에서는 이러한 시나리오를 자세하게 설명합니다.

### 장치 관리 클라이언트

[장치 관리 클라이언트][lnk-device-management]는 Edison 보드에서 서비스로 실행됩니다. 이렇게 하면 [펌웨어 업데이트][lnk-dm-jobs], 재부팅 및 구성 업데이트와 같은 Edison 보드에서 원격 작업을 실행할 뿐만 아니라 장치 속성을 쿼리할 수 있습니다. 이 자습서에서 장치 관리 클라이언트는 Edison 보드에 대한 펌웨어 업데이트를 수행하기 위한 요청을 받고 처리합니다.

### IoT 허브

[Azure IoT Hub][lnk-iot-hub]는 수백만의 IoT 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 이 자습서에서 IoT Hub는 다음을 수행합니다.

- 세 가지 장치를 클라우드에 연결할 수 있습니다.
- 게이트웨이에서 시뮬레이션된 장치에 의해 생성된 원격 분석을 받습니다.
- Edison 보드에 펌웨어 업데이트 요청을 보낼 수 있습니다.
- 펌웨어 업데이트 작업의 진행률을 모니터링합니다.

### 장치 관리 샘플 UI

[장치 관리 샘플 UI][lnk-dm-sample-ui]는 대화형 웹 UI에서 IoT Hub의 장치 관리 기능을 사용할 수 있도록 하는 샘플 웹 응용 프로그램입니다. 예를 들어, 샘플 UI를 사용하여 IoT Hub에 연결된 장치를 쿼리하고 장치에 펌웨어 업데이트 작업을 제출할 수 있습니다. 이 자습서에서는 샘플 UI를 사용하여 Edison 보드에 펌웨어 업데이트 작업을 제출하고 완료될 때까지 해당 작업의 진행률을 모니터링합니다.

## 구축 및 실행

이 샘플을 실행하려면 IoT Hub 게이트웨이 소프트웨어 및 장치 관리 클라이언트를 포함하는 Edison 보드에 대한 사용자 지정 이미지를 작성해야 합니다.

시작하기 전에 Edison 보드를 무선 네트워크에 연결할 수 있는지 확인해야 합니다. Edison 보드를 설정하려면 호스트 컴퓨터에 연결해야 합니다. 나중에 만든 사용자 지정 이미지를 사용하여 Edison 보드를 플래싱하기 위해 호스트 컴퓨터를 사용합니다. Intel에는 다음 운영 체제에 대한 가이드를 포함하여 일련의 시작 가이드가 있습니다.

- [Windows 64비트에서 Intel Edison 개발 보드 시작][lnk-setup-win64]
- [Windows 32비트에서 Intel Edison 개발 보드 시작][lnk-setup-win32]
- [Linux에서 Intel® Edison 보드 시작][lnk-setup-linux]

Edison 보드를 설정하고 친숙해지려면 다음을 제외하고 이 "시작" 문서의 모든 단계를 완료해야 합니다.

- 최신 펌웨어를 플래싱합니다. 이 자습서의 일부로 펌웨어를 업데이트하기에 지금은 이 단계를 완료하지 않아도 됩니다.
- 마지막 단계인 "IDE 선택"은 현재 자습서에 필요하지 않습니다.

Edison 보드를 설정하고 호스트 컴퓨터에 필요한 드라이버를 설치했다면 직렬 터미널을 사용하여 Edison 보드에 연결할 수 있는지 확인해야 합니다. Intel 웹 사이트에서 [직렬 터미널 설정][lnk-serial-connection] 페이지는 Windows 및 Linux와 같은 호스트 운영 체제에 대한 지침을 설정하는 링크를 포함합니다.

또한 다음 작업을 완료해야 합니다.

- Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 자습서를 완료하려면 허브의 이름이 필요합니다. Azure 구독이 아직 없는 경우 [무료 계정][lnk-free-trial]을 얻을 수 있습니다.
- IoT Hub에 3개의 장치(**GW-ble1-demo**, **GW-ble2-demo** 및 **GW-device**)를 추가하고 ID 및 장치 키를 기록합니다. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 이러한 장치를 추가하고 해당 키를 검색할 수 있습니다. 이러한 장치 중 두 가지(**GW-ble1-demo** and **GW-ble2-demo**)를 게이트웨이에 연결된 시뮬레이션된 BLE 장치로 사용하고 하나의 장치장치(**GW-device**)를 IoT Hub에서 관리할 수 있는 장치 관리 클라이언트로 Edison 게이트웨이 장치를 식별하는 데 사용합니다.

### 빌드 환경 준비 및 사용자 지정 이미지를 빌드할 수 있는지 확인

Edison 보드에 대한 사용자 지정 이미지를 만들려면 Linux 환경이 필요합니다. 이러한 단계는 작성할 당시에 사용하도록 권장되는 플랫폼인 Ubuntu 14.04를 사용한다고 가정합니다. 홈 파티션에 적어도 사용 가능한 40GB가 있어야 합니다.

> [AZURE.NOTE] 사용자 지정 이미지를 구축하는 스크립트는 4개 CPU 코어 컴퓨터에서 실행되는 데 최대 6시간이 걸릴 수 있습니다. 더 많은 CPU 코어가 있는 보다 강력한 컴퓨터를 사용하여 이 시간을 줄일 수 있습니다.

이 섹션의 단계에서는 [Intel Edison 보드 지원 패키지][lnk-inteledison-bsp], [원본에서 Intel Edison 보드에 대한 Yocto 이미지 수동으로 구축][lnk-hackgnar] 및 [Edison(2.1 릴리스)에 대한 사용자 지정 Linux 커널 만들기][lnk-shawnhymel] 등의 문서를 참조했습니다.

1. Ubuntu 14.04 컴퓨터에 로그인하고 홈 폴더에서 다음 명령을 실행하여 Edison 원본 패키지를 다운로드합니다.
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. 원본 패키지의 압축을 풀어서 다음 명령을 사용하여 홈 폴더에 **edison-src** 폴더를 만들고 새 **edison-src** 폴더로 이동합니다.
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. 필수 구성 요소 패키지를 설치합니다.
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. **edison-src** 폴더에서 두 개의 새로운 디렉터리를 만듭니다.
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. 다음 스크립트를 실행하여 구축 환경을 다운로드합니다. 4개 이상의 CPU 코어를 사용하는 경우 사용할 수 있는 코어 수에 **--parallel\_make** 및 **--bb\_number\_thread** 스크립트 인수를 설정합니다.
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Paho git 리포지토리의 위치를 업데이트합니다. **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** 파일을 편집하고 URL을 `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/`와 `git://github.com/eclipse/paho.mqtt.c.git`으로 바꿉니다.

7. 다음 명령을 사용하여 구축 환경을 초기화합니다.
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. 다음 명령을 사용하여 사용자 지정 이미지를 구축합니다. 처음으로 이 명령을 실행하는 경우 4개 CPU 코어 컴퓨터에서 실행되는 데 최대 6시간이 걸릴 수 있습니다. 고유한 사용자 지정을 추가하면 후속 재구축 작업은 훨씬 빨라집니다.
    
    ```
    bitbake edison-image
    ```

9. 다음 명령을 실행하여 구축을 완료합니다.
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Edison 보드를 플래싱해야 하는 파일은 이제 **~/edison-src/out/linux64/build/toFlash/** 폴더에 있습니다.

### 사용자 지정 이미지에 게이트웨이 SDK 추가

이 섹션의 단계에서는 부팅 시 Edison 보드가 IoT 게이트웨이로 작동할 수 있도록 사용자 지정 이미지에 게이트웨이 SDK를 추가하는 과정을 안내합니다. 이 자습서에서는 게이트웨이에 대한 온도 원격 분석을 생성하는 두 개의 BLE(Bluetooth 저에너지) 장치를 시뮬레이션하는 모듈을 게이트웨이에 포함하여 IoT Hub에 전달합니다.

이전 섹션에서 Edison 이미지를 구축하는 데 사용한 동일한 Ubuntu 14.04 컴퓨터에서 다음 단계를 완료합니다.

1. 홈 폴더에 게이트웨이 SDK를 복제합니다.
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. 게이트웨이를 구성하여 IoT Hub에 연결하고 두 개의 장치를 시뮬레이션합니다. **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** 파일을 편집하고 **IoTHubName**, **IoTHubSuffix**, **deviceID** 및 **deviceKey** 자리 표시자를 IoT Hub를 구성할 때 기록한 값으로 바꿉니다. 이전에 만든 **GW-ble1-demo** 및 **GW-ble2-demo** 장치를 사용합니다.

3. 새 작성법을 포함할 폴더를 만듭니다.
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** 폴더에서 방금 만든 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** 폴더로 **azure-iot-field-gateway-sdk.bb**라는 작성법 파일을 복사합니다. 파일을 편집하여 `<<userName>>`라는 두 개의 발생 수를 현재 사용자 이름으로 바꿉니다.

5. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb**를 편집하여 새 작성법에 대한 항목을 추가합니다. 파일 끝에 다음 줄을 추가합니다.
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. 이제 새 작성법을 구축하는 **bitbake** 명령을 실행하여 변경 내용을 테스트할 수 있습니다.
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Azure IoT Hub 장치 관리 클라이언트를 사용자 지정 이미지에 추가

이 섹션의 단계에서는 IoT Hub에서 Edison 게이트웨이 장치를 관리할 수 있도록 사용자 지정 이미지에 IoT Hub 장치 관리 클라이언트를 추가하는 프로세스를 안내합니다. 이 자습서에서 장치 관리 클라이언트는 Edison 게이트웨이 장치에 펌웨어 업데이트를 사용할 수 있도록 하는 샘플 코드를 포함합니다.

이전 섹션에서 Edison 이미지에 게이트웨이를 추가하는 데 사용한 동일한 Ubuntu 14.04 컴퓨터에서 다음 단계를 완료합니다.

1. 홈 폴더에 IoT Hub SDK 리포지토리의 **dmpreview** 분기를 복제합니다.
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. 새 작성법을 포함하는 다음과 같은 폴더를 만듭니다.
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** 폴더에서 **iotdm-edison-sample.bb** 파일을 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample** 폴더로 복사합니다.

4. 파일 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/iotdm-edison-sample.bb**를 편집하고 `-Duse_http:BOOL=OFF`를 `-Duse_http:BOOL=ON`와 바꿉니다.

5. **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** 폴더에서 **iotdm\_edison\_sample.service** 파일을 **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files** 폴더로 복사합니다.

6. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** 파일을 편집하여 새 작성법에 대한 항목을 추가합니다. 파일 끝에 다음 줄을 추가합니다.
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

7. 게이트웨이 SDK 및 장치 관리 클라이언트가 일부 라이브러리를 공유하기 때문에 **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass** 파일을 편집해야 합니다. 이 파일 끝에 다음 줄을 추가합니다. `<your user>`를 현재 사용자 이름으로 바꿔야 합니다.
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

8. **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** 파일을 편집하고 다음 줄을 파일 끝에 추가하여 WiFi가 Edison 보드에서 자동으로 시작되도록 구성합니다. `<your wifi ssid>` 및 `<your wifi password>`를 WiFi 네트워크에 대한 올바른 값으로 바꿔야 합니다.
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

9. 이제 게이트웨이 SDK 및 장치 관리 클라이언트를 포함하는 Edison 보드에 대한 이미지를 구축할 수 있습니다. **bitbake** 명령은 새 작성법을 빌드하고 이미지에 추가하기 때문에 이전보다 훨씬 더 빠르게 실행됩니다.
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

10. 다음 명령을 실행하여 구축을 완료합니다.
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

이제 Edison 보드를 업데이트해야 하는 파일이 **~/edison-src/out/linux64/build/toFlash/** 폴더에 있습니다.

### 사용자 지정 이미지를 사용하여 Intel Edison 플래싱

이제 IoT Hub 장치 관리 클라이언트 및 게이트웨이 소프트웨어를 모두 포함하는 사용자 지정 이미지를 사용하여 Edison 보드를 플래싱할 수 있습니다.

USB 케이블을 사용하여 Edison 보드에 연결된 컴퓨터에 사용자 지정 이미지를 빌드하는 데 사용한 Ubuntu 컴퓨터의 **toFlash** 폴더에서 파일을 복사합니다.

Windows 컴퓨터를 사용하여 USB 케이블로 Edison에 연결할 경우 **flashall.bat** 스크립트를 실행하여 Edison을 업데이트해야 합니다. Linux 컴퓨터를 사용하여 USB 케이블로 Edison에 연결할 경우 **flashall.bat** 스크립트를 실행하여 Edison을 업데이트해야 합니다.

업데이트 프로세스가 완료되면 호스트 컴퓨터에서 [직렬 연결][lnk-serial-connection]을 사용하여 Edison에 연결하고 **루트**로 로그인합니다. Edison 보드가 이제 WiFi 네트워크에 연결되는지 확인해야 합니다.

### 샘플 실행

Edison 보드의 장치 관리 클라이언트를 구성하여 IoT Hub에 **GW-device** 장치로 연결해야 합니다. 텍스트 편집기를 사용하여(예: **vi** 또는 **nano**) Edison의 /home/root 폴더에 **.cs**라는 파일을 만듭니다. 이 파일은 **GW-device**의 연결 문자열을 포함해야 합니다. 이 연결 문자열을 이전에 적어두지 않은 경우 [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 IoT Hub 장치 레지스트리에서 이 장치 연결 문자열을 검색할 수 있습니다.

**.cs** 파일을 만든 경우 다음 명령을 사용하여 Edison 보드를 재부팅합니다.

```
reboot -h now
```

Edison을 재부팅할 때, 장치 관리 및 게이트웨이 서비스가 모두 **확인** 상태로 시작하는지 확인합니다.

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

IoT 게이트웨이 서비스와 관련된 문제를 해결하려면 Edison의 **/deviceCloudUploadGatewaylog.log** 파일에서 항목을 확인합니다. 또한 다음 명령을 사용하여 서비스에서 모든 출력을 볼 수 있습니다.

```
systemctl status simulated_device_cloud_upload.service
```

IoT 장치 관리 서비스와 관련된 문제를 해결하려면 다음 명령을 사용하여 모든 출력을 봅니다.

```
systemctl status iotdm_edison_sample.service
```

### 펌웨어 업데이트 작업 시작

IoT 장치 관리 서비스에서 요청한 Edison의 펌웨어 업데이트는 일반적으로 URL에서 펌웨어를 포함하는 zip 파일을 다운로드합니다. 이 자습서를 간소화하려면 업데이트를 요청받은 경우 Edison 보드에 zip 파일을 수동으로 복사한 다음 **http://** URL 대신 **file://** URL을 사용합니다.

또한 자습서를 간소화하기 위해 펌웨어 업데이트는 새로운 이미지를 사용하는 대신 동일한 펌웨어 이미지를 다시 적용합니다. Edison 보드에 적용되는 이 이미지를 볼 수 있습니다.

사용자 지정 이미지를 만드는 데 사용한 Ubuntu 컴퓨터의 **toFlash** 폴더에 있는 모든 파일과 하위 폴더를 포함하는 **edison.zip**이라는 zip 파일을 만듭니다. **toFlash** 폴더의 파일이 zip 파일의 루트에 있어야 합니다. SCP(또는 Putty를 사용하는 경우 PSCP)와 같은 도구를 사용하여 Edison 보드의 /home/root 폴더에 **edison.zip** 파일을 복사합니다.

펌웨어 업데이트 작업을 제출하고 진행률을 모니터링하려면 Node.js [장치 관리 샘플 UI][lnk-dm-sample-ui]를 사용합니다. Windows 또는 Linux에서 이 샘플을 실행하려면 [Node.js][lnk-nodejs] 6.1.0 이상이 필요합니다. 데스크톱 컴퓨터에서 장치 관리 샘플 UI를 검색하고 구축하며 실행하려면 다음 단계를 수행합니다.

1. **명령 프롬프트**를 엽니다.

2. `node --version`을 입력하여 Node.js 6.1.0 이상을 설치했는지 확인합니다.

3. 다음 명령을 실행하여 Azure IoT 장치 관리 UI GitHub 리포지토리를 복제합니다.

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Azure IoT 장치 관리 UI 리포지토리에 있는 복제된 복사본의 루트 폴더에서 다음 명령을 실행하여 종속 패키지를 검색합니다.

    ```
    npm install
    ```

5. npm 설치 명령이 완료되면 다음 명령을 실행하여 코드를 작성합니다.

    ```
    npm run build
    ```

6. 텍스트 편집기를 사용하여 복제된 폴더의 루트에서 user-config.json 파일을 엽니다. "&lt;YOUR CONNECTION STRING HERE&gt;"라는 텍스트를 IoT Hub 연결 문자열로 바꿉니다. 이 연결 문자열은 Azure [포털][lnk-azure-portal]에서 찾을 수 있습니다.

7. 명령 프롬프트에서 다음 명령을 실행하여 장치 관리 UX 앱을 시작합니다.

    ```
    npm run start
    ```

8. 명령 프롬프트가 "서비스 시작"을 보고하면 웹 브라우저를 열고 다음 URL에서 장치 관리 앱으로 이동하여 장치를 봅니다. <http://127.0.0.1:3003>.

    ![장치 관리 UI][img-dm-ui]

9. **GW-device** 장치를 선택하고 **장치 작업** 드롭다운에서 **펌웨어 업데이트**를 선택한 다음 **시작**을 클릭합니다.

10. **패키지 URI** 필드에 **file:///home/root/edison.zip**를 입력하여 Edison 보드에 이전에 복사한 이미지 파일을 사용합니다. **전송**, **예** 및 **작업 기록** 링크를 차례로 클릭하여 실행 중인 새 부모 및 자식 작업을 확인합니다.

    ![작업 기록 링크][img-history-link]

11. 몇 분 후에 Edison 보드에 연결된 직렬 터미널에서 Edison 재부팅을 확인하고 펌웨어 변경 사항을 적용합니다.

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Edison을 재부팅하면 장치 관리 샘플 UI에서 페이지를 새로 고쳐서 이제 두 개의 펌웨어 업데이트 작업에 대한 작업 상태가 **완료됨**인지 확인합니다.

    ![작업 상태 완료][img-job-status]

이제 Intel Edison 보드에서 IoT Hub 게이트웨이 소프트웨어 및 장치 관리 클라이언트를 사용하는 방법을 보여 주는 자습서를 완료했습니다. 이 자습서의 일부로 다음을 수행했습니다.

- IoT Hub 장치 관리 클라이언트 및 게이트웨이 소프트웨어를 포함하는 사용자 지정 Intel Edison 이미지가 언제든지 Edison 보드가 부팅되기 시작하도록 만들었습니다.
- Edison 보드 및 장치 관리 클라이언트가 IoT Hub에 연결되도록 구성했습니다.
- Edison 보드를 다시 부팅하고 새 펌웨어 이미지를 적용하는 샘플 UI에서 장치 관리 작업을 시작했습니다.

## 다음 단계

IoT Hub 및 샘플 UI를 사용한 장치 관리에 대해 자세히 알아보려면 [Azure IoT Hub 장치 관리의 개요][lnk-device-management] 문서를 참조하세요.

게이트웨이 SDK와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 [Azure IoT 게이트웨이 SDK][lnk-gateway-sdk]를 방문하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->