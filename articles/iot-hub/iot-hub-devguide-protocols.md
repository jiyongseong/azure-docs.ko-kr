---
title: "Azure IoT Hub 통신 프로토콜 및 포트 | Microsoft Docs"
description: "개발자 가이드 - 장치-클라우드 및 클라우드-장치 간 통신에 지원되는 통신 프로토콜과 열어야 하는 포트 번호에 대해 설명합니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: f115d1e7313d2c9d378129c021a37489674fe81f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="reference---choose-a-communication-protocol"></a>참조 - 통신 프로토콜 선택

IoT Hub를 사용하면 장치가 장치 쪽 통신에 다음 프로토콜을 사용할 수 있습니다.

* [MQTT][lnk-mqtt]
* WebSocket을 통한 MQTT
* [AMQP][lnk-amqp]
* Websocket 통한 AMQP
* HTTPS

이러한 프로토콜이 특정 IoT Hub 기능을 지원하는 방식에 대한 내용은 [장치-클라우드 통신 지침][lnk-d2c-guidance] 및 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

다음 표에는 선택한 프로토콜에 대한 고급 권장 지침이 나와 있습니다.

| 프로토콜 | 이 프로토콜을 선택해야 하는 경우 |
| --- | --- |
| MQTT <br> WebSocket을 통한 MQTT |동일한 TLS 연결을 통해 여러 장치(각각 장치별 자격 증명 보유)에 연결할 필요가 없는 모든 장치에서 사용합니다. |
| AMQP <br> WebSocket을 통한 AMQP |장치 간에 연결 멀티플렉싱을 활용하기 위해 필드 및 클라우드 게이트웨이에서 사용합니다. |
| HTTPS |다른 프로토콜을 지원할 수 없는 장치에 사용됩니다. |

장치 측 통신용 프로토콜을 선택할 때는 다음 사항을 고려해야 합니다.

* **클라우드-장치 패턴**. HTTPS에는 서버 푸시를 구현하는 효율적인 방법이 없습니다. 이와 같이 HTTPS를 사용하는 경우 장치는 클라우드-장치 메시지에 IoT Hub를 폴링합니다. 이 방법은 장치와 IoT Hub 모두에 비효율적입니다. 현재 HTTPS 지침에 따르면 각 장치는 25분 이상 간격으로 메시지를 폴링해야 합니다. MQTT 및 AMQP는 클라우드-장치 메시지를 받을 때 서버 푸시를 지원합니다. 따라서 IoT Hub의 메시지가 장치에 즉시 푸시될 수 있습니다. 전달 대기 시간이 중요한 경우 MQTT 또는 AMQP는 가장 사용하기 적합한 프로토콜입니다. 드물게 연결되는 장치의 경우 HTTPS도 작동합니다.
* **현장 게이트웨이**. MQTT 및 HTTPS를 사용하는 경우 동일한 TLS 연결을 사용하여 여러 장치(각각 장치별 자격 증명 보유)를 연결할 수 없습니다. 연결된 각 장치에 대해 필드 게이트웨이와 IoT Hub 간에 하나의 TLS 연결이 필요한 [필드 게이트웨이 시나리오][lnk-azure-gateway-guidance]의 경우 이러한 프로토콜이 최적의 방법은 아닙니다.
* **낮은 리소스 장치**. MQTT 및 HTTPS 라이브러리는 AMQP 라이브러리보다 적은 공간을 차지합니다. 이와 같이 장치에 리소스가 제한되어 있으면(예: 1MB RAM보다 작음) 이러한 프로토콜은 사용할 수 있는 유일한 프로토콜 구현일 수도 있습니다.
* **네트워크 통과**. 표준 AMQP 프로토콜은 포트 5671을 사용하고 MQTT는 8883 포트에서 수신 대기합니다. 이러한 포트를 사용하면 비 HTTPS 프로토콜에 근접한 네트워크에서 문제를 일으킬 수 있습니다. 이 시나리오에서 WebSockets를 통한 MQTT, WebSockets를 통한 AMQP 및 HTTPS를 사용합니다.
* **페이로드 크기**. MQTT 및 AMQP는 바이너리 프로토콜로, HTTPS보다 더 많이 압축된 페이로드를 발생합니다.

> [!WARNING]
> HTTPS를 사용하는 경우 각 장치는 25분 이상마다 클라우드-장치에 대해 폴링합니다. 그렇지만 개발하는 동안에는 25분 보다 좀 더 자주 폴링을 하도록 할 수도 있습니다.

## <a name="port-numbers"></a>포트 번호

장치는 다양한 프로토콜을 사용하여 Azure에서 IoT Hub와 통신할 수 있습니다. 일반적으로 프로토콜은 솔루션의 특정 요구 사항에 따라 선택됩니다. 다음 표에는 특정 프로토콜을 사용할 수 있는 장치에 대해 열려 있어야 하는 아웃바운드 포트가 나와 있습니다.

| 프로토콜 | 포트 |
| --- | --- |
| MQTT |8883 |
| WebSocket을 통한 MQTT |443 |
| AMQP |5671 |
| Websocket 통한 AMQP |443 |
| HTTPS |443 |

Azure 지역에 IoT Hub를 만들면 IoT Hub는 해당 IoT Hub의 수명 동안 동일한 IP 주소를 유지합니다. 그러나 서비스 품질을 유지하기 위해 Microsoft가 IoT Hub를 다른 배율 단위로 이동하는 경우에는 새 IP 주소로 할당됩니다.


## <a name="next-steps"></a>다음 단계

IoT Hub가 MQTT 프로토콜을 구현하는 방법에 대한 자세한 내용은 [MQTT 프로토콜을 사용하여 IoT 허브와 통신][lnk-mqtt-support]을 참조하세요.

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
