---
title: Azure IoT Hub 끝점 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub 장치 지향 및 서비스 지향 끝점에 대한 참조 정보
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: d9378a084d8ff421539a445986a5a18d073f3ce6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="reference---iot-hub-endpoints"></a>참조 - IoT Hub 끝점

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub 이름

**개요** 블레이드에서 포털에서 끝점을 호스팅하는 IoT Hub의 이름을 찾을 수 있습니다. 기본적으로 IoT Hub의 DNS 이름은 다음과 같은 형태입니다. `{your iot hub name}.azure-devices.net`.

Azure DNS를 사용하여 IoT Hub에 대한 사용자 지정 DNS 이름을 만들 수 있습니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](../dns/dns-custom-domain.md)을 참조하세요.

## <a name="list-of-built-in-iot-hub-endpoints"></a>기본 제공 IoT Hub 끝점 목록

Azure IoT Hub는 다중 테넌트 서비스이며 다양한 행위자에게 기능을 노출합니다. 아래 다이어그램에서는 IoT Hub가 노출하는 다양한 끝점을 보여줍니다.

![IoT Hub 끝점][img-endpoints]

다음 목록에서는 끝점을 설명합니다.

* **리소스 공급자**. IoT Hub 리소스 공급자는 [Azure Resource Manager][lnk-arm] 인터페이스를 노출합니다. 이 인터페이스를 통해 Azure 구독 소유자는 IoT Hub를 생성 및 삭제하고 IoT Hub 속성을 업데이트할 수 있습니다. IoT Hub 속성은 장치 수준 액세스 제어와는 달리 [허브 수준 보안 정책][lnk-accesscontrol]과 클라우드-장치 및 장치-클라우드 메시징을 위한 기능 옵션을 제어합니다. 또한 IoT Hub 리소스 공급자는 사용자가 [장치 ID를 내보낼][lnk-importexport] 수 있도록 합니다.
* **장치 ID 관리**. 각 IoT Hub는 HTTPS REST 끝점 집합을 노출하여 장치 ID를 관리합니다(만들기, 검색, 업데이트 및 삭제). [장치 ID][lnk-device-identities]는 장치 인증 및 액세스 제어에 사용됩니다.
* **장치 쌍 관리**. 각 IoT Hub에서 [장치 쌍][lnk-twins](tags 및 속성 업데이트)을 쿼리하고 업데이트하기 위해 서비스 지향 HTTPS REST 끝점 집합을 공개합니다.
* **작업 관리**. 각 IoT Hub에서 [jobs][lnk-jobs]을 쿼리하고 업데이트하기 위해 서비스 지향 HTTPS REST 끝점 집합을 공개합니다.
* **장치 끝점**. IoT Hub는 ID 레지스트리의 각 장치에 대해 끝점 집합을 노출합니다.

  * *장치-클라우드 메시지 보내기*. 장치는 이 끝점을 사용하여 [장치-클라우드 메시지를 보냅니다][lnk-d2c].
  * *클라우드-장치 메시지 받기*. 장치에서 이 끝점을 사용하여 대상인 [클라우드-장치 메시지][lnk-c2d]를 수신합니다.
  * *파일 업로드를 시작합니다*. [파일을 업로드][lnk-upload]하기 위해 장치에서 이 끝점을 사용하여 IoT Hub에서 Azure Storage SAS URI를 수신합니다.
  * *장치 쌍 속성 검색 및 업데이트*. 장치에서 이 끝점을 사용하여 [장치 쌍][lnk-twins] 속성에 액세스합니다.
  * *직접 메서드 요청 수신*. 장치에서 이 끝점을 사용하여 [직접 메서드][lnk-methods] 요청을 수신합니다.

    이러한 끝점은 [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 및 [AMQP 1.0][lnk-amqp] 프로토콜을 사용하여 공개됩니다. AMQP는 포트 443의 [WebSockets][lnk-websockets]를 통해서도 사용할 수 있습니다.

* **서비스 끝점**. 각 IoT Hub는 솔루션 백 엔드에서 장치와 통신하기 위한 끝점 집합을 노출합니다. 한 가지를 제외하고 이러한 끝점은 [AMQP][lnk-amqp] 프로토콜을 사용해서만 노출됩니다. 메서드 호출 끝점은 HTTPS 프로토콜을 통해 노출됩니다.
  
  * *장치-클라우드 메시지 받기*. 이 끝점은 [Azure Event Hubs][lnk-event-hubs]와 호환됩니다. 백 엔드 서비스는 이 끝점을 사용하여 장치에서 보낸 모든 [장치-클라우드 메시지][lnk-d2c]를 읽을 수 있습니다. 이 기본 제공 끝점 외에도 IoT Hub에 사용자 지정 끝점을 만들 수 있습니다.
  * *클라우드-장치 메시지를 보내고 배달 승인 받기*. 이러한 끝점을 사용하면 솔루션 백 엔드에서 신뢰할 수 있는 [클라우드-장치 메시지][lnk-c2d]를 전송하고 해당 전달 또는 만료 승인을 수신할 수 있습니다.
  * *파일 알림을 받습니다*. 이 메시징 끝점을 사용하면 장치가 성공적으로 파일을 업로드하는 경우 알림을 받을 수 있습니다. 
  * *직접 메서드 호출*. 이 끝점을 사용하면 백 엔드 서비스가 장치에서 [직접 메서드][lnk-methods]를 호출할 수 있습니다.
  * *작업 모니터링 이벤트를 수신합니다*. 이 끝점을 통해 IoT Hub가 작업 모니터링 이벤트를 내보내도록 구성된 경우 작업 모니터링 이벤트를 수신할 수 있습니다. 자세한 내용은 [IoT Hub 작업 모니터링][lnk-operations-mon]을 참조하세요.

[Azure IoT SDK][lnk-sdks] 문서에서는 이러한 끝점에 액세스할 수 있는 다양한 방법에 대해 설명합니다.

모든 IoT Hub 끝점은 [TLS][lnk-tls] 프로토콜을 사용하고 어떤 끝점도 암호화되지/보안되지 않은 채널에 공개되지 않습니다.

## <a name="custom-endpoints"></a>사용자 지정 끝점

구독의 기존 Azure 서비스를 IoT Hub에 연결하여 메시지 라우팅을 위한 끝점 역할을 할 수 있습니다. 이러한 끝점은 서비스 끝점 역할을 하며 메시지 경로에 대한 싱크로 사용됩니다. 장치는 추가 끝점에 직접 쓸 수 없습니다. 메시지 경로에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]의 개발자 가이드 항목을 참조하세요.

IoT Hub는 현재 추가 끝점으로 다음과 같은 Azure 서비스를 지원합니다.

* Azure Storage 컨테이너 
* Event Hubs
* Service Bus 큐
* Service Bus 토픽

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 끝점에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 끝점을 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. IoT 솔루션을 처음 구성하는 경우 추가 끝점을 모니터링하고 실제 부하에 맞게 조정해야 할 수 있습니다.

메시지가 동일한 끝점을 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 끝점에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. 분할된 큐에서 파티션 선호도는 메시지 순서를 보장합니다.

추가할 수 있는 끝점의 수에 대한 제한은 [할당량 및 제한][lnk-devguide-quotas]을 참조하세요.

### <a name="when-using-azure-storage-containers"></a>Azure Storage 컨테이너를 사용하는 경우

IoT Hub는 데이터를 Azure Storage 컨테이너에 [Apache Avro](http://avro.apache.org/) 형식의 BLOB으로 쓰는 것만 지원합니다. IoT Hub는 메시지를 일괄 처리하고 다음 조건이 충족될 때마다 Blob에 데이터를 씁니다.

* 일괄 처리가 특정 크기에 도달할 경우
* 또는 특정 시간이 경과할 경우

쓸 데이터가 없으면 IoT Hub가 빈 Blob을 작성합니다.

IoT Hub는 기본적으로 다음 파일 명명 규칙을 따릅니다.

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

원하는 파일 명명 규칙을 사용할 수 있으나 열거된 모든 토큰을 사용해야 합니다.

### <a name="when-using-service-bus-queues-and-topics"></a>Service Bus 큐 및 토픽을 사용할 경우

IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 끝점이 **연결할 수 없음**으로 표시됩니다.

## <a name="field-gateways"></a>현장 게이트웨이

IoT 솔루션에서 *필드 게이트웨이*는 장치와 IoT Hub 끝점 사이에 위치하며 일반적으로 장치 가까이에 위치합니다. 장치는 해당 장치에서 지원되는 프로토콜을 사용하여 필드 게이트웨이와 직접 통신합니다. 필드 게이트웨이는 IoT Hub에서 지원하는 프로토콜을 사용하여 IoT Hub 끝점에 연결됩니다. 필드 게이트웨이는 전용 하드웨어 장치이거나 사용자 지정 게이트웨이 소프트웨어를 실행하는 저전력 컴퓨터일 수 있습니다.

[Azure IoT Edge][lnk-iot-edge]를 사용하여 필드 게이트웨이를 구현할 수 있습니다. IoT Edge는 여러 장치에서 동일한 IoT Hub 연결로의 통신 다중 송신과 같은 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [장치 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [할당량 및 제한][lnk-devguide-quotas]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
