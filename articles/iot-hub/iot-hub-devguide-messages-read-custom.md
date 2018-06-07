---
title: Azure IoT Hub 사용자 지정 끝점 이해 | Microsoft Docs
description: 개발자 가이드 - 라우팅 규칙을 사용하여 장치-클라우드 메시지를 사용자 지정 끝점으로 라우팅합니다.
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
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: 3d54da43141dc2bdf34c9f71adc41dc7cf24ff10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>장치-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 끝점 사용

IoT Hub를 사용하면 메시지 속성을 기반으로 IoT Hub 서비스 지향 끝점으로 [장치-클라우드 메시지][lnk-device-to-cloud]를 라우팅할 수 있습니다. 라우팅 규칙을 사용하면 추가 서비스나 사용자 지정 코드 없이 필요한 곳에 메시지를 보낼 수 있습니다. 구성하는 각 라우팅 규칙에는 다음과 같은 속성이 있습니다.

| 자산      | 설명 |
| ------------- | ----------- |
| **Name**      | 규칙을 식별하는 고유한 이름입니다. |
| **원본**    | 처리할 데이터 스트림의 원본입니다. 예를 들어 장치 원격 분석이 있습니다. |
| **Condition** | 메시지 헤더 및 본문에 대해 실행되고 엔드포인트와 일치하는지 여부를 확인하는 데 사용되는 라우팅 규칙에 대한 쿼리 식입니다. 경로 조건 구성에 대한 자세한 내용은 [참조 - 장치 쌍 및 작업에 대한 쿼리 언어][lnk-devguide-query-language]를 참조하세요. |
| **끝점**  | IoT Hub에서 조건과 일치하는 메시지를 보내는 끝점의 이름입니다. 끝점은 IoT Hub와 동일한 지역에 있어야 합니다. 그렇지 않으면 지역 간 쓰기 요금이 부과될 수 있습니다. |

하나의 메시지가 여러 라우팅 규칙의 조건과 일치할 수 있습니다.이 경우 IoT Hub는 일치된 각 규칙과 연결된 끝점으로 메시지를 배달합니다. 또한 IoT Hub는 메시지 배달을 자동으로 중복 제거하므로 메시지가 동일한 대상을 가진 여러 규칙과 일치하면 해당 대상에 한 번만 기록됩니다.

## <a name="endpoints-and-routing"></a>끝점 및 라우팅

IoT Hub에는 기본 [기본 제공 끝점][lnk-built-in]이 있습니다. 구독의 다른 서비스를 허브에 연결하여 메시지를 라우팅할 사용자 지정 끝점을 만들 수 있습니다. IoT Hub는 현재 사용자 지정 끝점으로 Azure Storage 컨테이너, Event Hubs, Service Bus 큐 및 Service Bus 토픽을 지원합니다.

라우팅 및 사용자 지정 끝점을 사용할 때 메시지에 일치하는 규칙이 없으면 기본 제공 끝점에만 메시지가 전달됩니다. 메시지를 기본 제공 끝점과 사용자 지정 끝점에 모두 전달하려면 메시지를 **이벤트** 끝점에 보내는 경로를 추가합니다.

> [!NOTE]
> IoT Hub는 데이터를 Azure Storage 컨테이너에 BLOB으로 쓰는 것만 지원합니다.

> [!WARNING]
> **세션** 또는 **중복 검색**이 사용하도록 설정된 Service Bus 큐 및 토픽은 사용자 지정 끝점으로 지원되지 않습니다.

IoT Hub에 사용자 지정 끝점을 만드는 방법에 대한 자세한 내용은 [IoT Hub 끝점][lnk-devguide-endpoints]을 참조하세요.

사용자 지정 끝점에서 읽는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Storage 컨테이너][lnk-getstarted-storage]에서 읽기
* [Event Hubs][lnk-getstarted-eh]에서 읽기
* [Service Bus 큐][lnk-getstarted-queue]에서 읽기
* [Service Bus 토픽][lnk-getstarted-topic]에서 읽기

## <a name="latency"></a>대기 시간

기본 제공 끝점을 사용하여 장치-클라우드 간 원격 분석 메시지를 라우팅할 경우 첫 번째 경로를 만든 후 종단 간 대기 시간이 약간 증가합니다.

대부분의 경우 평균적인 대기 시간 증가는 1초 미만입니다. **d2c.endpoints.latency.builtIn.events** [IoT Hub 메트릭](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)을 사용하여 대기 시간을 모니터링할 수 있습니다. 첫 번째 경로 이후에 다른 경로를 만들거나 삭제해도 종단 간 대기 시간에는 영향을 주지 않습니다.

### <a name="next-steps"></a>다음 단계

IoT Hub 끝점에 대한 자세한 내용은 [IoT Hub 끝점][lnk-devguide-endpoints]을 참조하세요.

라우팅 규칙을 정의하는 데 사용하는 쿼리 언어에 대한 자세한 내용은 [장치 쌍, 작업 및 메시지 라우팅을 위한 IoT Hub 쿼리 언어][lnk-devguide-query-language]를 참조하세요.

[경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리][lnk-d2c-tutorial] 자습서는 라우팅 규칙과 사용자 지정 끝점을 사용하는 방법을 보여 줍니다.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
