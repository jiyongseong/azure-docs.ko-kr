---
title: "Azure IoT Hub 기본 제공 끝점 이해 | Microsoft Docs"
description: "개발자 가이드에서는 기본 제공 이벤트 허브 호환 끝점을 사용하여 장치-클라우드 메시지를 읽는 방법을 설명합니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 82681214e9e42819bfc698aa670755467d250fa7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>기본 제공 끝점에서 장치-클라우드 메시지 읽기

기본적으로 메시지는 [Event Hubs][lnk-event-hubs]와 호환되는 기본 제공 서비스 연결 끝점(**messages/events**)으로 라우팅됩니다. 이 끝점은 현재 [AMQP][lnk-amqp] 프로토콜을 통해서만 포트 5671에 노출됩니다. IoT Hub는 다음 속성을 노출하여 기본 제공 Event Hub와 호환되는 메시징 끝점 **messages/events**를 제어할 수 있게 합니다.

| 자산            | 설명 |
| ------------------- | ----------- |
| **분할 개수** | 이 속성은 생성 시 설정하여 장치-클라우드 이벤트 수집에 대한 [파티션][lnk-event-hub-partitions] 수를 정의합니다. |
| **보존 시간**  | 이 속성은 IoT Hub에서 메시지를 보존할 일 수를 지정합니다. 기본값은 1일이지만 7일로 늘릴 수 있습니다. |

또한 IoT Hub를 사용하면 기본 제공 장치-클라우드 수신 끝점에서 소비자 그룹을 관리할 수 있습니다.

기본적으로 메시지 라우팅 규칙과 명시적으로 일치하지 않는 모든 메시지는 기본 제공 끝점에 기록됩니다. 이 대체 경로를 비활성화하면 메시지 라우팅 규칙과 명시적으로 일치하지 않는 메시지는 삭제됩니다.

[IoT Hub 리소스 공급자 REST API][lnk-resource-provider-apis]를 사용하여 또는 [Azure Portal][lnk-management-portal]을 사용하여 프로그래밍 방식으로 보존 시간을 수정할 수 있습니다.

IoT Hub는 허브에서 수신한 장치-클라우드 메시지를 읽도록 백 엔드 서비스의 기본 제공 끝점인 **messages/events**를 공개합니다. 이 끝점은 Event Hub와 호환되기 때문에 Event Hubs 서비스가 메시지 읽기에 대해 지원하는 모든 메커니즘을 사용할 수 있습니다.

## <a name="read-from-the-built-in-endpoint"></a>기본 제공 끝점에서 읽기

[.NET용 Azure Service Bus SDK][lnk-servicebus-sdk] 또는 [Event Hubs - 이벤트 프로세서 호스트][lnk-eventprocessorhost]를 사용하는 경우 적절한 권한으로 모든 IoT Hub 연결 문자열을 사용할 수 있습니다. **이벤트/메시지** 를 이벤트 허브 이름으로 사용합니다.

IoT Hub를 인식하지 않는 SDK(또는 제품 통합)를 사용하는 경우 Event Hub 호환 끝점 및 Event Hub 호환 이름을 검색해야 합니다.

1. [Azure Portal][lnk-management-portal]에 로그인하고 IoT 허브로 이동합니다.
1. **Endpoints**를 클릭합니다.
1. **기본 제공 끝점** 섹션에서 **이벤트**를 클릭합니다. 
1. **Event Hub 호환 끝점**, **Event Hub 호환 이름**, **파티션**, **보존 시간** 및 **소비자 그룹** 값을 포함하는 속성 페이지가 열립니다.

    ![장치-클라우드 설정][img-eventhubcompatible]

IoT Hub SDK에는 **끝점**에서 보여 주듯이 IoT Hub 끝점 이름으로 **messages/events**가 필요합니다.

사용 중인 SDK에 **호스트 이름** 또는 **네임스페이스** 값이 필요한 경우 **Event Hub 호환 끝점**에서 스키마를 제거합니다. 예를 들어 Event Hubs 호환 끝점이 **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**인 경우 **호스트 이름**은 **iothub-ns-myiothub-1234.servicebus.windows.net**이 됩니다. **네임스페이스**는 **iothub-ns-myiothub-1234**가 됩니다.

지정된 Event Hubs에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 정책을 사용할 수 있습니다.

이전의 정보를 사용하여 이벤트 허브 연결 문자열을 작성해야 하는 경우 다음과 같은 패턴을 사용합니다.

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

IoT Hub를 노출하는 이벤트 허브와 호환 가능한 끝점으로 사용할 수 있는 SDK 및 통합에는 다음 목록의 항목이 포함됩니다.

* [Java Event Hubs 클라이언트](https://github.com/Azure/azure-event-hubs-java)
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/spark/apache-spark-eventhub-streaming.md)

## <a name="next-steps"></a>다음 단계

IoT Hub 끝점에 대한 자세한 내용은 [IoT Hub 끝점][lnk-endpoints]을 참조하세요.

[시작][lnk-get-started] 자습서에서는 시뮬레이션된 장치에서 장치-클라우드 메시지를 보내고 기본 제공 끝점에서 메시지를 읽는 방법을 설명합니다. 자세한 내용은 [경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리][lnk-d2c-tutorial] 자습서를 참조하세요.

장치-클라우드 메시지를 사용자 지정 끝점으로 라우팅하려면 [장치-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 끝점 사용][lnk-custom]을 참조하세요.

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
