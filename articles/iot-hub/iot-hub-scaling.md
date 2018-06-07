---
title: Azure IoT Hub 크기 조정 | Microsoft Docs
description: 예상된 메시지 처리량 및 원하는 기능을 지원하도록 IoT 허브를 확장하는 방법입니다. 분할을 위한 옵션 및 각 계층에 지원되는 처리량에 대한 요약을 포함합니다.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01e6c3a6fb922a649f0ae139af9c8515fcb8b2e0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>솔루션에 대한 올바른 IoT Hub 계층 선택

모든 IoT 솔루션은 다르므로 Azure IoT Hub는 가격 책정 및 규모에 따라 여러 가지 옵션을 제공합니다. 이 문서는 IoT Hub 요구 사항 평가를 돕기 위한 것입니다. IoT Hub 계층에 대한 가격 책정 정보는 [IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub)을 참조하세요. 

솔루션에 적합한 IoT Hub 계층을 결정하려면 두 가지 사항을 스스로에게 질문합니다.

**어떤 기능을 사용할 계획입니까?**
Azure IoT Hub는 지원하는 기능의 수가 다른 두 계층, 기본 및 표준을 제공합니다. IoT 솔루션이 장치의 데이터 수집을 기반으로 하고 중앙 집중식으로 분석하는 경우 기본 계층은 아마도 사용자에게 적합합니다. 더욱 고급 구성을 사용하여 IoT 장치를 원격으로 제어하거나 일부 작업을 장치 자체에 배포하려는 경우 표준 계층을 고려해야 합니다. 각 계층에 포함된 기능의 자세한 내용은 [기본 및 표준 계층](#basic-and-standard-tiers)을 계속합니다.

**얼마나 많은 데이터를 매일 이동할 계획입니까?**
각 IoT Hub 계층은 지정된 날에 처리할 수 있는 데이터 처리량을 기반으로 세 가지 크기에서 사용 가능합니다. 이러한 크기는 1, 2, 3으로 숫자로 식별됩니다. 예를 들어 수준 1 IoT 허브의 각 단위는 하루에 40만 개의 메시지를 처리할 수 있는 반면 수준 3 단위는 3억 개를 처리할 수 있습니다. 데이터 지침에 대한 자세한 내용은 [메시지 처리량](#message-throughput)을 계속합니다.

## <a name="basic-and-standard-tiers"></a>기본 및 표준 계층

IoT Hub의 표준 계층은 모든 기능을 활성화하고 양방향 통신 기능을 활용하려는 IoT 솔루션에 필요합니다. 기본 계층은 기능의 하위 집합을 활성화하고 장치에서 클라우드로의 단방향 통신만 필요한 IoT 솔루션을 위한 것입니다. 두 계층은 동일한 보안 및 인증 기능을 제공합니다.

IoT 허브를 만들면 기존 작업을 중단하지 않고 기본 계층에서 표준 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [IoT 허브를 업그레이드하는 방법](iot-hub-upgrade.md)을 참조하세요.

| 기능 | 기본 계층 | 표준 계층 |
| ---------- | ---------- | ------------- |
| [장치-클라우드 원격 분석](iot-hub-devguide-messaging.md) | 예 | 예 |
| [장치당 ID](iot-hub-devguide-identity-registry.md) | 예 | 예 |
| [메시지 라우팅](iot-hub-devguide-messages-read-custom.md) 및 [Event Grid 통합](iot-hub-event-grid.md) | 예 | 예 |
| [HTTP, AMQP 및 MQTT 프로토콜](iot-hub-devguide-protocols.md) | 예 | 예 |
| [장치 프로비저닝 서비스](../iot-dps/about-iot-dps.md) | 예 | 예 |
| [모니터링 및 진단](iot-hub-monitor-resource-health.md) | 예 | 예 |
| [클라우드-장치 메시징](iot-hub-devguide-c2d-guidance.md) |   | 예 |
| [장치 쌍](iot-hub-devguide-device-twins.md), [모듈 쌍](iot-hub-devguide-module-twins.md) 및 [장치 관리](iot-hub-device-management-overview.md) |   | 예 |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | 예 |

IoT Hub는 또한 테스트와 평가를 위한 무료 계층을 제공합니다. 제한된 메시징 허용 개수로 표준 계층의 모든 기능을 포함합니다. 무료 계층에서 기본 또는 표준 계층으로 업그레이드할 수 없습니다. 

### <a name="iot-hub-rest-apis"></a>IoT Hub REST API

IoT Hub의 기본 및 표준 계층 간의 지원되는 기능 차이는 일부 API 호출이 기본 계층 허브와 작동하지 않는 것을 의미합니다. 다음 표는 사용 가능한 API를 보여줍니다. 

| API | 기본 계층 | 표준 계층 |
| --- | ---------- | ------------- |
| [장치 삭제](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | 예 | 예 |
| [장치 가져오기](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | 예 | 예 |
| 모듈 삭제 | 예 | 예 |
| 모듈 가져오기 | 예 | 예 |
| [레지스트리 통계 가져오기](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | 예 | 예 |
| [서비스 통계 가져오기](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | 예 | 예 |
| [장치 넣기](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | 예 | 예 |
| 모듈 넣기 | 예 | 예 |
| [장치 쿼리](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | 예 | 예 |
| 모듈 쿼리 | 예 | 예 |
| [파일 업로드 SAS URI 만들기](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | 예 | 예 |
| [바인딩된 장치 알림 수신](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | 예 | 예 |
| [장치 이벤트 보내기](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | 예 | 예 |
| 모듈 이벤트 보내기 | 예 | 예 |
| [파일 업로드 상태 업데이트](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | 예 | 예 |
| [대량 장치 작업](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | 예, IoT Edge 기능 제외 | 예 | 
| [명령 큐 제거](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | 예 |
| [장치 쌍 가져오기](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | 예 |
| 모듈 쌍 가져오기 |   | 예 |
| [장치 메서드 호출](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | 예 |
| [장치 쌍 업데이트](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | 예 | 
| 모듈 쌍 업데이트 |   | 예 | 
| [바인딩된 장치 알림 중단](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | 예 |
| [바인딩된 장치 알림 완료](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | 예 |
| [작업 취소](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | 예 |
| [작업 만들기](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | 예 |
| [작업 가져오기](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | 예 |
| [작업 쿼리](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | 예 |

## <a name="message-throughput"></a>메시지 처리량

IoT Hub 솔루션의 크기를 조정하는 가장 적절한 방법은 장치별로 트래픽을 평가하는 것입니다. 특히 다음과 같은 범주의 작업에 필요한 최대 처리량을 고려해야 합니다.

* 장치-클라우드 메시지
* 클라우드-장치 메시지
* ID 레지스트리 작업

트래픽은 허브별이 아니라 단위별로 측정됩니다. 수준 1 또는 2 IoT Hub 인스턴스는 연결된 최대 200 단위를 가질 수 있습니다. 수준 3 IoT Hub 인스턴스는 최대 10개 단위를 가질 수 있습니다. IoT 허브를 만들면 기존 작업을 중단하지 않고 단위 수를 변경하거나 특정 계층 내에서 1, 2 및 3 크기 간에 이동할 수 있습니다. 자세한 내용은 [IoT Hub를 업그레이드하는 방법](iot-hub-upgrade.md)을 참조하세요.

각 계층의 트래픽 기능의 예로 장치-클라우드 메시지는 지속적인 처리량 지침을 따릅니다.

| 계층 | 지속적인 처리량 | 지속적인 전송 속도 |
| --- | --- | --- |
| B1, S1 |장치당 최대 1111KB/분<br/>(1.5GB/일/장치) |장치당 평균 278메시지/분<br/>(400,000메시지/일/장치당) |
| B2, S2 |장치당 최대 16MB/분<br/>(22.8GB/일/장치) |장치당 평균 4,167개 메시지/분<br/>(6백만 개의 메시지/일/장치당) |
| B3, S3 |장치당 최대 814MB/분<br/>(1144.4GB/일/장치) |장치당 평균 208,333 메시지/분<br/>(3억 개의 메시지/일/장치당) |

이 처리량 정보 외에도 [IoT Hub 할당량 및 제한][IoT Hub quotas and throttles]을 참조하고 솔루션을 적절히 디자인하세요.

### <a name="identity-registry-operation-throughput"></a>ID 레지스트리 작업 처리량
IoT Hub ID 레지스트리 작업은 대부분이 장치 프로비전과 관련되므로 런타임 작업으로 간주되지 않습니다.

관련 버스트 성능 수치는 [IoT Hub 할당량 및 제한][IoT Hub quotas and throttles]을 참조하세요.

## <a name="sharding"></a>분할
단일 IoT Hub를 수백만 대의 장치로 확장할 수 있기는 하지만, 솔루션에서 단일 IoT Hub에서 보장할 수 없는 특정 성능 특성을 필요로 하는 경우가 있습니다. 이 경우 여러 IoT 허브에서 장치를 분할할 수 있습니다. 여러 IoT Hub가 갑작스러운 트래픽 증가를 원활하게 처리하고 필요한 처리량 또는 필요한 작업 속도를 얻습니다.

## <a name="next-steps"></a>다음 단계

* IoT Hub 기능 및 성능 세부 정보에 대한 자세한 내용은 [IoT Hub 가격 책정] [링크 가격 책정] 또는 [IoT Hub 할당량 및 제한][IoT Hub quotas and throttles]을 참조하세요.
* IoT Hub 계층을 변경하려면 [IoT 허브 업그레이드](iot-hub-upgrade.md)의 단계를 따릅니다.

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
