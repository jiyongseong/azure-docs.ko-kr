---
title: Azure IoT Hub MQTT 지원 이해 | Microsoft Docs
description: 개발자 가이드 - MQTT 프로토콜을 사용하여 IoT Hub 장치 지향 끝점에 연결하는 장치를 지원합니다. Azure IoT 장치 SDK의 기본 제공 MQTT 지원에 대한 정보를 포함합니다.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b058f716c2435b70244c9293b1c5d897ec215f7f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT 프로토콜을 사용하여 IoT 허브와 통신

IoT Hub를 사용하면 다음을 사용하여 IoT Hub 장치 엔드포인트와 통신할 수 있습니다.

* 포트 8883에서 [MQTT v3.1.1][lnk-mqtt-org] 
* 포트 443에서 WebSocket을 통해 MQTT v3.1.1

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub와 통신하는 모든 장치는 TLS/SSL을 사용하여 보호되어야 합니다. 따라서 IoT Hub는 1883 포트를 통한 비보안 연결을 지원하지 않습니다.

## <a name="connecting-to-iot-hub"></a>IoT Hub에 연결

장치는 다음을 통해 MQTT 프로토콜을 사용하여 IoT 허브에 연결할 수 있습니다.

* [Azure IoT Sdk][lnk-device-sdks]의 라이브러리
* 또는 MQTT 프로토콜 직접 사용

## <a name="using-the-device-sdks"></a>장치 SDK 사용

MQTT 프로토콜을 지원하는 [장치 SDK][lnk-device-sdks]는 Java, Node.js, C, C# 및 Python에서 사용할 수 있습니다. 장치 SDK는 표준 IoT Hub 연결 문자열을 사용하여 IoT Hub에 대한 연결을 설정합니다. MQTT 프로토콜을 사용하려면 클라이언트 프로토콜 매개 변수를 **MQTT**에 설정해야 합니다. 기본적으로는 장치 SDK는 **CleanSession** 플래그가 **0**으로 설정된 IoT Hub에 연결되고 **QoS 1**을 사용하여 IoT Hub와 메시지를 교환합니다.

장치가 IoT Hub에 연결되면 장치 SDK는 IoT Hub와 메시지를 교환할 수 있게 하는 메서드를 제공합니다.

다음 테이블에는 지원되는 각 언어에 대한 코드 샘플에 대 한 링크가 있고 MQTT 프로토콜을 사용하여 IoT Hub에 연결하는 데 사용하는 매개 변수를 지정합니다.

| 언어 | 프로토콜 매개 변수 |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>장치 앱을 AMQP에서 MQTT로 마이그레이션

[장치 SDK][lnk-device-sdks]를 사용하는 경우 이전에 언급한 대로 AMQP 사용에서 MQTT로 전환하려면 클라이언트 초기화에서 프로토콜 매개 변수를 변경해야 합니다.

이렇게 할 경우 다음 항목을 확인해야 합니다.

* AMQP는 많은 조건에 대한 오류를 반환하는 한편 MQTT는 연결을 종료합니다. 결과적으로 예외 처리 논리를 일부 변경해야 합니다.
* MQTT는 [클라우드-장치 메시지][lnk-messaging]를 수신할 때 *reject* 작업을 지원하지 않습니다. 백 엔드 앱이 장치 앱에서 응답을 수신해야 할 경우 [직접 메서드][lnk-methods] 사용을 고려합니다.

## <a name="using-the-mqtt-protocol-directly"></a>MQTT 프로토콜 직접 사용

장치가 장치 SDK를 사용할 수 없는 경우라도 포트 8883에서 MQTT 프로토콜을 사용하는 공용 장치 끝점에 연결할 수 있습니다. **CONNECT** 패킷에서 장치는 다음 값을 사용해야 합니다.

* **ClientId** 필드에 **deviceId**를 사용합니다.

* **Username** 필드에 `{iothubhostname}/{device_id}/api-version=2016-11-14`를 사용합니다. 여기서 `{iothubhostname}`은 IoT Hub의 전체 CName입니다.

    예를 들어, IoT Hub의 이름이 **contoso.azure devices.net**이고 장치의 이름이 **MyDevice01**이면 전체 **Username** 필드에 다음이 포함되어야 합니다.

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTPS 및 AMQP 프로토콜에 대해 동일합니다.

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509 인증서 인증을 사용하는 경우 SAS 토큰 암호는 필요하지 않습니다. 자세한 내용은 [Azure IoT Hub의 X.509 보안 설정][lnk-x509]을 참조하세요.

  SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [IoT Hub 보안 토큰 사용][lnk-sas-tokens]의 장치 섹션을 참조하세요.

  테스트할 때 [장치 탐색기][lnk-device-explorer] 도구를 사용하여 복사한 후 자체 코드에 붙여넣을 수 있는 SAS 토큰을 빠르게 생성할 수도 있습니다.

  1. **장치 탐색기**의 **관리** 탭으로 이동합니다.
  2. **SAS 토큰** (오른쪽 위)을 클릭합니다.
  3. **SASTokenForm**의 **DeviceID** 드롭다운에서 장치를 선택합니다. **TTL**을 설정합니다.
  4. **생성** 을 클릭하여 토큰을 만듭니다.

     생성되는 SAS 토큰은 다음 구조를 갖습니다.

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     이 토큰에서 MQTT를 사용하여 연결할 때 **암호** 필드에서와 같이 사용하는 부분은 다음과 같습니다.

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT 연결 및 분리 패킷의 경우, IoT Hub는 **작업 모니터링** 채널의 이벤트를 발행합니다. 이 이벤트에에는 연결 문제 해결에 도움이 되는 추가 정보가 있습니다.

장치 앱은 **CONNECT** 패킷에 **Will** 메시지를 지정할 수 있습니다. 장치 앱은 `devices/{device_id}/messages/events/{property_bag}` 또는 `devices/{device_id}/messages/events/{property_bag}`를 **Will** 항목 이름으로 사용하여 원격 분석 메시지로서 전달할 **Will** 메시지를 정의할 수 있습니다. 이 경우 네트워크 연결이 닫혀 있지만 **DISCONNECT** 패킷이 이전에 장치에서 수신되지 않은 경우 IoT Hub는 **CONNECT** 패킷에 제공된 **Will** 메시지를 원격 분석 채널로 전송합니다. 원격 분석 채널은 기본 **이벤트** 끝점 또는 IoT Hub 라우팅으로 정의되는 사용자 지정 끝점일 수 있습니다. 메시지에는 **Will** 값이 할당된 **iothub MessageType** 속성이 지정됩니다.

### <a name="tlsssl-configuration"></a>TLS/SSL 구성

MQTT 프로토콜을 직접 사용하려면 클라이언트가 *반드시* TLS/SSL를 통해 연결되어야 합니다. 이 단계를 건너뛰려고 하면 연결 오류가 발생하여 실패합니다.

TLS 연결을 설정하려면 DigiCert Baltimore 루트 인증서를 다운로드하여 참조해야 할 수 있습니다. 이 인증서는 Azure가 연결 보호에 사용합니다. 이 인증서는 [Azure-iot-sdk-c][lnk-sdk-c-certs] 저장소에 있습니다. 이 인증서에 대한 자세한 내용은 [Digicert의 웹 사이트][lnk-digicert-root-certs]에 있습니다.

Eclipse Foundation에서 Python 버전의 [Paho MQTT 라이브러리][lnk-paho]를 사용하여 이것을 구현하는 방법의 예는 다음과 유사합니다.

먼저, 명령줄 환경에서 Paho 라이브러리를 설치합니다.

```cmd/sh
pip install paho-mqtt
```

그런 다음, Python 스크립트로 클라이언트를 구현합니다. 다음과 같이 자리 표시자를 바꿉니다.

* `<local path to digicert.cer>`는 DigiCert Baltimore Root 인증서가 들어 있는 로컬 파일의 경로입니다. C에 대한 Azure IoT SDK에서 [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c)의 인증서 정보를 복사하여 이 파일을 만들 수 있습니다. `-----BEGIN CERTIFICATE-----` 및 `-----END CERTIFICATE-----` 줄을 포함하고 모든 줄의 시작과 끝에서 `"` 표시를 제거한 다음, 모든 줄의 마지막에서 `\r\n` 문자를 제거합니다.
* `<device id from device registry>` IoT Hub에 추가된 장치의 ID입니다.
* `<generated SAS token>`은 이 문서의 앞에서 설명한 장치에 대한 SAS 토큰입니다.
* `<iot hub name>`은 IoT Hub의 이름입니다.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>장치-클라우드 메시지 보내기

성공적인 연결을 구축한 후 장치는 `devices/{device_id}/messages/events/` 또는 `devices/{device_id}/messages/events/{property_bag}`를 **토픽 이름**으로 사용하여 IoT Hub에 메시지를 보낼 수 있습니다. `{property_bag}` 요소는 URL 인코딩 형식의 속성을 추가하여 메시지를 보내는 장치를 사용할 수 있습니다. 예: 

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> 이 `{property_bag}` 요소는 HTTPS 프로토콜의 쿼리 문자열과 동일한 인코딩을 사용합니다.

다음은 IoT Hub 구현 관련 동작의 목록입니다.

* IoT Hub에서는 QoS 2 메시지를 지원하지 않습니다. 장치 앱이 **QoS 2**의 메시지를 게시하는 경우, IoT Hub는 네트워크 연결을 닫습니다.
* IoT Hub에서는 보관 메시지가 지속되지 않습니다. 장치가 **RETAIN** 플래그가 1로 설정된 메시지를 게시하는 경우, IoT Hub는 **x-opt-retain** 응용 프로그램 속성을 메시지에 추가합니다. 이 경우에 IoT Hub는 보관 메시지를 유지하지 않고 백 엔드 앱에 전달합니다.
* IoT Hub는 장치 당 하나의 활성 MQTT 연결만을 지원합니다. 동일한 장치 ID를 대신하는 모든 새 MQTT 연결로 인해 IoT Hub가 기존 연결을 삭제하게 됩니다.

자세한 내용은 [메시징 개발자 가이드][lnk-messaging]를 참조하세요.

### <a name="receiving-cloud-to-device-messages"></a>클라우드-장치 메시지 수신

IoT Hub에서 메시지를 수신하려면 장치는 `devices/{device_id}/messages/devicebound/#` 을 **토픽 필터**로 사용하여 구독해야 합니다. 토픽 필터에 다중 레벨 와일드카드 `#`는 장치가 토픽 이름에 추가 속성을 수신하도록 하려는 경우에만 사용됩니다. IoT Hub는 하위 토픽의 필터링을 위한 `#` 또는 `?` 와일드카드의 사용을 허용하지 않습니다. IoT Hub는 범용 발행-구독 메시징 브로커가 아니므로 문서화된 토픽 이름 및 토픽 필터만 지원합니다.

장치는 `devices/{device_id}/messages/devicebound/#` 항목 필터로 표시되는 장치 특정 끝점을 성공적으로 구독하기 전에는 IoT Hub로부터 어떠한 메시지도 수신하지 않습니다 구독이 설정된 후에는 장치가 구독 시간 이후 전송된 클라우드-장치 메시지를 수신합니다. 장치가 **CleanSession** 플래그가 **0**으로 설정되어 연결되면 다양한 세션 간에 구독이 유지됩니다. 이 경우 다음 번에 장치가 **CleanSession 0**으로 연결될 때, 연결되지 않은 동안 보내진 미해결 메시지를 수신하게 됩니다. 장치가 **1**로 설정된 **CleanSession** 플래그를 사용하는 경우 장치-끝점을 구독할 때까지 IoT Hub에서 어떠한 메시지도 수신하지 않습니다.

IoT Hub는 메시지 속성이 있는 경우 **토픽 이름** `devices/{device_id}/messages/devicebound/`, 또는 `devices/{device_id}/messages/devicebound/{property_bag}`와 함께 메시지를 배달합니다. `{property_bag}` 에는 메시지 속성의 URL 인코딩된 키/값 쌍이 있습니다. 응용 프로그램 속성 및 사용자 설정 가능 시스템 속성(예: **messageId** 또는 **correlationId**)만 속성 모음에 포함됩니다. 시스템 속성 이름에는 접두사 **$**가 있고, 응용 프로그램 속성은 접두사가 없는 원래 속성 이름을 사용합니다.

장치 앱이 **QoS 2**의 토픽을 구독하는 경우, IoT Hub는 **SUBACK** 패킷에서 최대 QoS level 1을 부여합니다. 그런 다음 IoT Hub는 메시지를 QoS 1을 사용하는 장치에 전달합니다.

### <a name="retrieving-a-device-twins-properties"></a>장치 쌍 속성 검색

먼저 작업의 응답을 수신하기 위해 장치가 `$iothub/twin/res/#`을 구독합니다. 그런 다음 **요청 ID**에 채워진 값을 사용하여 빈 메시지를 `$iothub/twin/GET/?$rid={request id}` 항목에 보냅니다. 그러면 서비스는 요청과 동일한 **요청 ID**를 사용하여 `$iothub/twin/res/{status}/?$rid={request id}` 항목에 대한 장치 쌍 데이터를 포함하는 응답 메시지를 보냅니다.

요청 ID는 [IoT Hub 메시징 개발자 가이드][lnk-messaging]에 따라 메시지 속성 값에 대한 유효한 값이며 status는 정수로 확인됩니다.

응답 본문은 장치 쌍의 properties 섹션을 포함합니다. 다음 코드 조각에서는 “properties” 구성원으로 제한된 ID 레지스트리 항목의 본문을 보여 줍니다. 예:

```json
{
    "properties": {
        "desired": {
            "telemetrySendFrequency": "5m",
            "$version": 12
        },
        "reported": {
            "telemetrySendFrequency": "5m",
            "batteryLevel": 55,
            "$version": 123
        }
    }
}
```

가능한 상태 코드:

|상태 | 설명 |
| ----- | ----------- |
| 200 | 성공 |
| 429 | 너무 많은 요청(제한됨), [IoT Hub 제한][lnk-quotas] 참조 |
| 5** | 서버 오류 |

자세한 내용은 [장치 쌍 개발자 가이드][lnk-devguide-twin]를 참조하세요.

### <a name="update-device-twins-reported-properties"></a>장치 쌍의 reported 속성 업데이트

다음 시퀀스에서는 장치가 IoT Hub의 장치 쌍에서 보고된 속성을 업데이트하는 방법을 설명합니다.

1. 장치는 먼저 `$iothub/twin/res/#` 항목을 구독하여 IoT Hub에서 작업의 응답을 수신해야 합니다.

1. 장치는 장치 쌍 업데이트를 포함하는 메시지를 `$iothub/twin/PATCH/properties/reported/?$rid={request id}` 항목에 전송합니다. 이 메시지는 **요청 ID** 값을 포함합니다.

1. 그러면 서비스에서는 항목 `$iothub/twin/res/{status}/?$rid={request id}`에 대해 보고된 속성 컬렉션의 새 ETag 값을 포함하는 응답 메시지를 보냅니다. 이 응답 메시지는 동일한 **요청 ID**를 요청으로 사용합니다.

요청 메시지 본문은 보고된 속성에 대한 새 값을 포함하는 JSON 문서를 포함합니다. JSON 문서의 각 구성원은 장치 쌍의 문서에 있는 해당 구성원을 업데이트하거나 추가합니다. `null`로 설정된 구성원은 포함하는 개체에서 구성원을 삭제합니다. 예: 

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

가능한 상태 코드:

|상태 | 설명 |
| ----- | ----------- |
| 200 | 성공 |
| 400 | 잘못된 요청. 형식이 잘못된 JSON |
| 429 | 너무 많은 요청(제한됨), [IoT Hub 제한][lnk-quotas] 참조 |
| 5** | 서버 오류 |

자세한 내용은 [장치 쌍 개발자 가이드][lnk-devguide-twin]를 참조하세요.

### <a name="receiving-desired-properties-update-notifications"></a>desired 속성 업데이트 알림 수신

장치가 연결되면 IoT Hub는 `$iothub/twin/PATCH/properties/desired/?$version={new version}` 항목에 알림을 보내는데 여기에는 솔루션 백 엔드에 의해 수행된 업데이트 콘텐츠가 포함됩니다. 예: 

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

속성 업데이트의 경우 `null` 값은 JSON 개체 구성원이 삭제되고 있음을 의미합니다.

> [!IMPORTANT]
> IoT Hub는 장치가 연결된 경우에만 변경 알림을 생성하여 desired 속성이 IoT Hub와 장치 앱 간에 동기화 상태를 유지하기 위해 [장치 다시 연결 흐름][lnk-devguide-twin-reconnection]이 수행되도록 합니다.

자세한 내용은 [장치 쌍 개발자 가이드][lnk-devguide-twin]를 참조하세요.

### <a name="respond-to-a-direct-method"></a>직접 메서드에 응답

먼저 장치가 `$iothub/methods/POST/#`을 구독해야 합니다. IoT Hub는 `$iothub/methods/POST/{method name}/?$rid={request id}` 항목에 유효한 JSON 또는 빈 본문으로 메서드 요청을 보냅니다.

응답하기 위해 장치는 올바른 JSON 또는 빈 본문이 있는 메시지를 `$iothub/methods/res/{status}/?$rid={request id}` 토픽에 보냅니다. 이 메시지에서 **요청 ID**는 요청 메시지의 것과 일치하고 **상태**는 정수여야 합니다.

자세한 내용은 [직접 메서드 개발자 가이드][lnk-methods]를 참조하세요.

### <a name="additional-considerations"></a>추가 고려 사항

최종 고려 사항에서 클라이언트 쪽에서 MQTT 프로토콜 동작을 사용자 지정해야 하는 경우 [Azure IoT 프로토콜 게이트웨이][lnk-azure-protocol-gateway]를 검토해야 합니다. 이 소프트웨어를 통해 IoT Hub와 직접 인터페이스되는 고성능 사용자 지정 프로토콜을 배포할 수 있습니다. Azure IoT 프로토콜 게이트웨이를 사용하면 brownfield MQTT 배포를 수용하는 장치 프로토콜 또는 기타 사용자 지정 프로토콜을 사용자 지정할 수 있습니다. 그렇지만 이 방법을 사용하는 경우 사용자 지정 프로토콜 게이트웨이를 실행하고 작동해야 합니다.

## <a name="next-steps"></a>다음 단계

MQTT 프로토콜에 대한 자세한 내용은 [MQTT 설명서][lnk-mqtt-docs]를 참조하세요.

IoT Hub 배포를 계획하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [IoT용 Azure Certified 장치 카탈로그][lnk-devices]
* [추가 프로토콜 지원][lnk-protocols]
* [Event Hubs와 비교][lnk-compare]
* [크기 조정, HA 및 DR][lnk-scaling]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
