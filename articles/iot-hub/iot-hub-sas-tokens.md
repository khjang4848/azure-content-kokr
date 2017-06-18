<properties
 pageTitle="IoT Hub 보안 토큰을 생성하는 방법 | Microsoft Azure"
 description="IoT Hub에 사용되는 다양한 보안 토큰 유형(예: SAS 및 X.509)과 이를 생성하는 방법에 대한 설명입니다."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="06/07/2016"
 ms.author="elioda"/>

# IoT Hub 보안 토큰 및 X.509 인증서 사용

IoT Hub는 네트워크에서 토큰이 전송되는 것을 피하기 위해 보안 토큰을 사용하여 장치 및 서비스를 인증합니다. 또한 보안 토큰은 유효 기간 및 범위가 제한됩니다. [Azure IoT Hub SDK][lnk-apis-sdks]는 특별한 구성이 필요하지 않고 토큰을 자동으로 생성합니다. 그러나 일부 시나리오에서는 사용자가 보안 토큰을 직접 생성하고 사용해야 합니다. 여기에는 [IoT Hub 지침][lnk-guidance-security]에 설명된 대로 MQTT, AMQP 또는 HTTP 표면의 직접 사용 또는 토큰 서비스 패턴의 구현이 포함됩니다.

또한 IoT Hub를 사용하면 장치가 X.509 인증서를 통해 IoT Hub에서 인증될 수 있습니다. IoT Hub는 AMQP, Websocket 통한 AMQP 및 HTTP 프로토콜을 사용한 장치에 대한 X.509 기반 인증을 지원합니다.

이 문서에서는 다음을 설명합니다.

* 보안 토큰 형식 및 생성 방법
* 보안 토큰을 사용하여 장치 및 백 엔드 서비스를 인증하는 기본 사용 사례
* 장치 인증을 위해 지원되는 X.509 인증서
* 특정 장치에 연결된 X.509 클라이언트 인증서 등록 프로세스
* 인증을 위해 X.509 클라이언트 인증서를 사용하는 장치 및 IoT Hub 간 런타임 흐름


## 보안 토큰 구조
보안 토큰을 사용하여 장치 및 서비스에서 IoT Hub의 특정 기능에 대한 시간 제한 액세스 권한을 부여합니다. 승인된 장치 및 서비스만 연결할 수 있도록 하려면 보안 토큰을 ID 레지스트리에 장치 ID로 저장된 공유 액세스 정책 키 또는 대칭 키로 서명해야 합니다.

공유 액세스 정책 키로 서명된 토큰은 공유 액세스 정책 권한과 관련된 모든 기능에 대한 액세스를 부여합니다. [IoT Hub 개발자 가이드의 보안 섹션][lnk-devguide-security]을 참조하세요. 한편, 장치 ID의 대칭 키로만 서명된 토큰은 관련된 장치 ID에 대해 **DeviceConnect** 권한을 부여합니다.

보안 토큰의 형식은 다음과 같습니다.

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

다음은 필요한 값입니다.

| 값 | 설명 |
| ----- | ----------- |
| {signature} | 형식의 HMAC-SHA256 서명 문자열은 `{URL-encoded-resourceURI} + "\n" + expiry`입니다. **중요**: 키는 base64에서 디코딩되며 HMAC-SHA256 계산을 수행하는 데 키로 사용됩니다. |
| {resourceURI} | 이 토큰으로 액세스할 수 있는 끝점의 URI 접두사(세그먼트별)이며 IoT Hub의 호스트 이름으로 시작합니다(프로토콜 없음). 위치(예:`myHub.azure-devices.net/devices/device1` |
| {expiry} | 1970년 1월 1일 epoch 0시 UTC 이후의 초 수에 대한 UTF8 문자열입니다. |
| {URL-encoded-resourceURI} | 소문자 URL-소문자 리소스 URI의 인코딩 |
| {policyName} | 이 토큰을 참조하는 공유 액세스 정책의 이름입니다. 장치 레지스트리 자격 증명을 참조하는 토큰의 경우가 없습니다. |

**접두사에 대한 참고**: 문자가 아니라에 세그먼트에 의해 계산된 URI 접두사입니다. 예를 들어 `/a/b`는 `/a/b/c`에 대한 접두사이지만 `/a/bc`에 대한 접두사는 아닙니다.

이것은 입력 `resourceUri, signingKey, policyName, expiresInMins`에서 토큰을 계산하는 노드 함수입니다. 다음 섹션에서는 여러 토큰 사용 사례에 대해 서로 다른 입력을 초기화하는 방법을 자세히 설명합니다.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };
 
 비교를 위해 해당 Python 코드는 다음과 같습니다.
 
    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode
    
    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())
     
        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [AZURE.NOTE] IoT Hub 컴퓨터에서 토큰의 유효 기간이 확인되므로 토큰을 생성하는 컴퓨터의 시계에서 편차가 최소여야 합니다.

## 장치로 SAS 토큰 사용

장치 ID 키를 사용하거나 공유 액세스 정책 키를 사용하는 등 두 가지 방법으로 보안 토큰과 함께 IoT Hub에서 **DeviceConnect** 권한을 얻을 수 있습니다.

또한 장치에서 액세스할 수 있는 모든 기능이 `/devices/{deviceId}` 접두사가 있는 끝점에서 디자인 시 노출되는 것을 유념해야 합니다.

> [AZURE.IMPORTANT] IoT Hub에서 특정 장치를 인증하는 유일한 방법은 장치 ID 대칭 키를 사용하는 것입니다. 장치 기능에 액세스하는 데 공유 액세스 정책을 사용하는 경우 솔루션은 보안 토큰을 신뢰할 수 있는 하위 구성 요소로 발급하는 구성 요소를 고려해야 합니다.

장치 연결 끝점은 다음과 같습니다(프로토콜과 관련 없음).

| 끝점 | 기능 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | 장치-클라우드 메시지를 보냅니다. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | 클라우드-장치 메시지를 받습니다. |

### ID 레지스트리에서 대칭 키 사용

장치 ID의 대칭 키를 사용하여 토큰을 생성하는 경우 토큰의 policyName(`skn`) 요소가 생략됩니다.

예를 들어, 모든 장치 기능에 액세스하기 위해 만든 토큰은 다음 매개 변수를 포함해야 합니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* 서명 키: `{device id}` ID에 대한 모든 대칭 키,
* 정책 이름 없음,
* 임의의 만료 시간.

위의 노드 함수를 사용하는 예제는 다음과 같습니다.

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

device1의 모든 기능에 액세스 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] .NET 도구 [장치 탐색기][lnk-device-explorer]를 사용하여 보안 토큰을 생성할 수 있습니다.

### 공유 액세스 정책 사용

공유 액세스 정책에서 토큰을 만들 때 정책 이름 필드 `skn`이 사용된 정책 이름으로 설정되어야 합니다. 또한 정책에서 **DeviceConnect** 권한을 부여해야 합니다.

장치 기능에 액세스하는 데 공유 액세스 정책을 사용하는 두 가지 주요 시나리오는 다음과 같습니다.

* [클라우드 프로토콜 게이트웨이][lnk-azure-protocol-gateway],
* 사용자 지정 인증 체계를 구현하는 데 사용된 [토큰 서비스][lnk-devguide-security].

공유 액세스 정책은 모든 장치로 연결하기 위해 잠재적으로 액세스 권한을 부여할 수 있으므로 보안 토큰을 만들 때 올바른 리소스 URI를 사용하는 것이 중요합니다. 리소스 URI를 사용하여 토큰을 특정 장치로 범위를 제한해야 하는 토큰 서비스에서 특히 중요합니다. 프로토콜 게이트웨이는 모든 장치에 대한 트래픽을 이미 조정하므로 관련성이 떨어집니다.

예를 들어 **장치**라는 미리 생성된 공유 액세스 정책을 사용하는 토큰 서비스는 다음 매개 변수로 토큰을 만듭니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* 서명 키: `device` 정책의 키 중 하나,
* 정책 이름: `device`,
* 임의의 만료 시간.

위의 노드 함수를 사용하는 예제는 다음과 같습니다.

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

device1의 모든 기능에 액세스 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

프로토콜 게이트웨이는 단순히 리소스 URI를 `myhub.azure-devices.net/devices`로 설정하여 모든 장치에 대해 동일한 토큰을 사용할 수 있습니다.

## 서비스 구성 요소에서 보안 토큰 사용

[IoT Hub 개발자 가이드의 보안 섹션][lnk-devguide-security]에 설명된 대로 서비스 구성 요소는 적절한 권한을 부여하는 공유 액세스 정책을 사용하여 보안 토큰을 생성할 수 있습니다.

다음은 끝점에 노출된 서비스 기능입니다.

| 끝점 | 기능 |
| ----- | ----------- |
| `{iot hub host name}/devices` | 장치 ID를 만들기, 업데이트, 검색 및 삭제합니다. |
| `{iot hub host name}/messages/events` | 장치-클라우드 메시지를 받습니다. |
| `{iot hub host name}/servicebound/feedback` | 클라우드-장치 메시지에 대한 피드백을 받습니다. |
| `{iot hub host name}/devicebound` | 클라우드-장치 메시지를 보냅니다. |

예를 들어 **registryRead**라는 미리 생성된 공유 액세스 정책을 사용하여 생성하는 서비스는 다음 매개 변수로 토큰을 만듭니다.

* 리소스 URI: `{IoT hub name}.azure-devices.net/devices`,
* 서명 키: `registryRead` 정책의 키 중 하나,
* 정책 이름: `registryRead`,
* 임의의 만료 시간.

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

모든 장치 ID에 대한 읽기 권한을 부여하는 결과는 다음과 같습니다.

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## 지원되는 X.509 인증서

X.509 인증서를 사용하여 IoT Hub에서 장치를 인증할 수 있습니다. 다음 내용이 포함됩니다.

-   **기존 X.509 인증서**. 장치에 X.509 인증서가 이미 연결되어 있을 수 있습니다. 장치는 이 인증서를 사용하여 IoT Hub에서 인증을 받을 수 있습니다.

-   **자체 생성 및 자체 서명 X-509 인증서**. 장치 제조업체 또는 사내 배포자는 이러한 인증서를 생성하고 장치에 해당 개인 키(및 인증서)를 저장할 수 있습니다. 이 목적을 위해 [OpenSSL] 및 [Windows SelfSignedCertificate] 유틸리티와 같은 도구를 사용할 수 있습니다.

-   **CA 서명 X.509 인증서**. 또한 CA(인증 기관)에서 생성 및 서명한 X.509 인증서를 사용하여 장치를 식별하고 IoT Hub에서 장치 인증을 받을 수도 있습니다.

장치는 인증을 위해 X.509 인증서 또는 보안 토큰 중 하나만 사용할 수 있습니다.

## 장치에 대해 X.509 클라이언트 인증서 등록

[C#에 대한 Azure IoT 서비스 SDK][lnk-service-sdk](버전 1.0.8+)는 인증을 위해 X.509 클라이언트 인증서를 사용하는 장치의 등록을 지원합니다. 장치 가져오기/내보내기 같은 기타 API에서도 X.509 클라이언트 인증서를 지원합니다.

### C# 지원

**RegistryManager** 클래스는 장치를 등록하는 프로그래밍 방식을 제공합니다. 특히 **AddDeviceAsync** 및 **UpdateDeviceAsync** 메서드를 사용하면 IoT Hub 장치 ID 레지스트리에서 사용자를 등록하고 장치를 업데이트할 수 있습니다. 이러한 두 메서드는 입력으로 **Device** 인스턴스를 수락합니다. **Device** 클래스에는 사용자가 기본 및 보조 X.509 인증서 지문을 지정할 수 있도록 하는 **인증Authentication** 속성이 포함되어 있습니다. 지문은 X.509 인증서의 SHA-1 해시(이진 DER 인코딩 사용)를 나타냅니다. 기본 지문이나 보조 지문 또는 둘 다를 지정하는 옵션이 제공됩니다. 인증서 롤오버 시나리오를 처리하기 위해 기본 및 보조 지문이 지원됩니다.

> [AZURE.NOTE] IoT Hub는 전체 X.509 클라이언트 인증서를 요구하거나 저장하지 않으며 지문만 요구하거나 저장합니다.

X.509 클라이언트 인증서를 사용하여 장치를 등록하는 샘플 C# 코드 조각은 다음과 같습니다.

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

## 런타임 작업 중에 X.509 클라이언트 인증서 사용

[.NET용 Azure IoT 장치 SDK][lnk-client-sdk](버전 1.0.11+)는 X.509 클라이언트 인증서의 사용을 지원합니다.

### C# 지원

클래스 **DeviceAuthenticationWithX509Certificate**는 X.509 클라이언트 인증서를 사용하여 **DeviceClient** 인스턴스를 만들도록 지원합니다.

다음은 샘플 코드 조각입니다.

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[OpenSSL]: https://www.openssl.org/
[Windows SelfSignedCertificate]: https://technet.microsoft.com/library/hh848633
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device

<!---HONumber=AcomDC_0914_2016-->