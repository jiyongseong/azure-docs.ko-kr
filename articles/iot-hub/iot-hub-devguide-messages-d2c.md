---
title: Azure IoT Hub 장치-클라우드 메시징 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub를 사용하여 장치-클라우드 메시징을 사용하는 방법입니다. 원격 분석 및 비원격 분석 데이터를 전송하고 라우팅을 사용하여 메시지를 전송하는 방법에 대한 정보가 포함됩니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 54b2961713f529438f5508bb0615824ec6bec214
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>IoT Hub에 장치-클라우드 메시지 보내기

시계열 원격 분석 및 경고를 장치에서 솔루션 백 엔드로 보내려면 장치-클라우드 메시지를 장치에서 IoT Hub로 보냅니다. IoT Hub에서 지원하는 기타 장치-클라우드 옵션에 대한 설명은 [장치-클라우드 통신 지침][lnk-d2c-guidance]을 참조하세요.

장치 지향 끝점(**/devices/{deviceId}/messages/events**)을 통해 장치-클라우드 메시지를 보냅니다. 그런 다음 라우팅 규칙은 메시지를 IoT Hub의 서비스 지향 끝점 중 하나로 라우팅합니다. 라우팅 규칙은 장치-클라우드 메시지의 헤더 및 본문을 사용하여 허브를 라우팅할 위치를 결정합니다. 기본적으로 메시지는 [Event Hubs][lnk-event-hubs]와 호환되는 기본 제공 서비스 연결 끝점(**messages/events**)으로 라우팅됩니다. 따라서 표준 [Event Hubs 통합 및 SDK][lnk-compatible-endpoint]를 사용하여 솔루션 백 엔드에서 장치-클라우드 메시지를 받을 수 있습니다.

IoT Hub는 스트리밍 메시징 패턴을 사용하여 장치-클라우드 메시징을 구현합니다. IoT Hub의 장치 - 클라우드 메시지는 여러 독자가 읽을 수 있는 서비스를 통과하는 많은 양의 이벤트가 있다는 점에서 [Service Bus][lnk-servicebus] *메시지*보다는 [Event Hubs][lnk-event-hubs] *이벤트*에 가깝습니다.

IoT Hub를 사용한 장치-클라우드 메시징의 특징은 다음과 같습니다.

* 장치-클라우드 메시지는 영구적이며 IoT Hub의 기본 **messages/events** 끝점에 최대 7일 동안 보관됩니다.
* 장치-클라우드 메시지는 최대 256KB까지 가능하며 보내기를 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. 배치는 최대 256KB가 될 수 있습니다.
* [IoT Hub에 대한 액세스 제어][lnk-devguide-security] 섹션에서 설명한 것처럼 IoT Hub는 장치 단위 인증 및 액세스 제어를 허용합니다.
* IoT Hub를 사용하면 최대 10개의 사용자 지정 끝점을 만들 수 있습니다. 메시지는 IoT Hub에 구성된 경로에 따라 끝점으로 전달됩니다. 자세한 내용은 [라우팅 규칙](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions)을 참조하세요.
* IoT Hub를 사용하면 수백만 대의 장치를 동시에 연결할 수 있습니다([할당량 및 제한][lnk-quotas] 참조).
* IoT Hub는 임의 분할을 허용하지 않습니다. 장치-클라우드 메시지는 원래 **deviceId**와 관련하여 분할됩니다.

IoT Hub와 Event Hubs의 차이점에 대한 자세한 내용은 [Azure IoT Hub 및 Azure Event Hubs의 비교][lnk-comparison]를 참조하세요.

## <a name="send-non-telemetry-traffic"></a>비-원격 분석 트래픽 보내기

때때로 원격 분석 외에도 장치는 솔루션 백 엔드에서 별도로 실행하고 처리해야 하는 메시지와 요청을 보냅니다. 예를 들어 백 엔드에서 특정 작업을 트리거해야 하는 중요한 경고가 있습니다. 이러한 형식의 메시지를 메시지 헤더 또는 메시지 본문의 값을 기반으로 처리 전용 끝점에 보내는 [라우팅 규칙][lnk-devguide-custom]을 작성할 수 있습니다.

이런 종류의 메시지를 처리하는 최선의 방법에 대한 정보는 [자습서: IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-d2c-tutorial] 자습서를 참조하세요.

## <a name="route-device-to-cloud-messages"></a>장치-클라우드 메시지 라우팅

백 엔드 앱에 장치-클라우드 메시지를 라우팅하는 두 가지 옵션이 있습니다.

* 허브에서 수신한 장치-클라우드 메시지를 백 엔드 앱에서 읽을 수 있도록 기본 제공 [Event Hub 호환 끝점][lnk-compatible-endpoint]을 사용합니다. 기본 제공 Event Hub 호환 끝점에 대해 알아보려면 [기본 제공 끝점에서 장치-클라우드 메시지 읽기][lnk-devguide-builtin]를 참조하세요.
* 라우팅 규칙을 사용하여 IoT Hub의 사용자 지정 끝점에 메시지를 보냅니다. 사용자 지정 끝점을 사용하면 백 엔드 앱에서 Event Hubs, Service Bus 큐 또는 Service Bus 토픽을 사용하여 장치-클라우드 메시지를 읽을 수 있습니다. 라우팅 및 사용자 지정 끝점에 대해 알아보려면 [장치-클라우드 메시지에 대한 사용자 지정 끝점 및 라우팅 규칙 사용][lnk-devguide-custom]을 참조하세요.

## <a name="anti-spoofing-properties"></a>스푸핑 방지 속성

장치-클라우드 메시지에서 스푸핑된 장치를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

처음 두 가지는 [장치 ID 속성][lnk-device-properties]에 따라 원래 장치의 **deviceId** 및 **generationId**를 포함합니다.

**ConnectionAuthMethod** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>다음 단계

장치-클라우드 메시지를 보내는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK][lnk-sdks]를 참조하세요.

[시작하기][lnk-get-started] 자습서에서는 시뮬레이트된 장치와 물리적 장치 둘 다에서 장치-클라우드 메시지를 보내는 방법을 보여 줍니다. 자세한 내용은 [경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리][lnk-d2c-tutorial] 자습서를 참조하세요.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
