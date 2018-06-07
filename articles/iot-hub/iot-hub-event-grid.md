---
title: "Azure IoT Hub 및 Azure Event Grid | Microsoft Docs"
description: "IoT Hub에서 발생하는 작업을 기반으로 프로세스를 트리거하려면 Azure Event Grid를 사용합니다."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 7c75a65714898f27ab0008ad5a30a5714d7174f4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>작업-미리 보기를 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응

Azure IoT Hub는 이벤트 알림을 다른 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합됩니다. 안전하고 안정적이며 확장성 있는 방식으로 중요한 이벤트에 대응할 수 있도록 IoT Hub 이벤트를 수신 대기 할 수 있게 비즈니스 응용 프로그램을 구성합니다. 예를 들어 새 IoT 장치가 IoT 허브에 등록될 때마다 데이터베이스를 업데이트하고, 티켓을 만들고, 이메일 알림을 전달하는 것과 같은 여러 작업을 수행하는 응용 프로그램을 빌드합니다. 

[Azure Event Grid][lnk-eg-overview]는 게시-구독 모델을 사용하여 완전히 관리되는 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개][lnk-eg-overview]를 참조하세요. 

![Azure Event Grid 아키텍처](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>국가별 가용성

Event Grid가지 원되는 지역의 IoT Hub에 대해 Event Grid 통합을 사용할 수 있습니다. 최신 지역 목록을 보려면 [Azure Event Grid 소개][lnk-eg-overview]를 참조하세요. 

## <a name="event-types"></a>일정 유형

IoT Hub는 다음과 같은 이벤트 유형을 게시합니다. 

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 장치를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 장치를 삭제하는 경우 게시합니다. | 

Azure Portal 또는 Azure 명령줄 인터페이스를 사용하여 각 IoT 허브에서 어떤 이벤트를 게시할지 구성할 수 있습니다. 한 예로 자습서 [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](../event-grid/publish-iot-hub-events-to-logic-apps.md)를 시도해 봅니다. 

## <a name="event-schema"></a>이벤트 스키마

IoT Hub 이벤트에는 장치 수명 주기 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. EventType 속성이 **Microsoft.Devices**로 시작하는지 확인하면 IoT Hub 이벤트인지 식별할 수 있습니다. Event Grid 이벤트 속성을 사용하는 방법에 대한 자세한 내용은 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.

### <a name="device-created-schema"></a>스키마를 생성한 장치

다음 예제는 장치가 생성된 이벤트의 스키마를 보여줍니다. 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

각 속성에 대한 상세한 설명은 [IoT Hub에 대한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-iot-hub.md)를 참조하세요.

## <a name="filter-events"></a>이벤트 필터링

IoT Hub 이벤트 구독은 이벤트 유형 및 장치 이름을 기반으로 이벤트를 필터링할 수 있습니다. Event Grid 작업의 제목 필터는 **접두사** 및 **접미사** 일치를 기준으로 작동합니다. 필터는 `AND` 연산자를 사용하므로 접두사와 접미사가 둘 다 일치하는 제목의 이벤트가 구독자에게 전달됩니다. 

IoT 이벤트의 제목은 다음 형식을 사용합니다.

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>이벤트 사용하기 위한 팁

IoT Hub 이벤트를 처리하는 응용 프로그램은 다음 권장 사항을 따라야 합니다.

* 동일한 이벤트 처리기에 이벤트를 라우팅하도록 여러 구독을 구성할 수 있습니다. 따라서 이벤트가 특정 소스에서 온 것으로 가정하지 않는 것이 중요합니다. 항상 메시지 항목을 확인하여 예상한 IoT 허브에서 온 개체인지 확인합니다. 
* 받은 모든 이벤트가 예상대로의 형식일 것이라고 가정하지 마십시오. 메시지를 처리하기 전에 eventType을 항상 확인합니다.
* 메시지는 지연 후 또는 잘못된 순서로 도착할 수 있습니다. 개체에 관한 정보가 최신 상태인지 알아보려면 etag 필드를 사용합니다.



## <a name="next-steps"></a>다음 단계

* [IoT Hub 이벤트 자습서를 시도해 봅니다.](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Event Grid에 대해 자세히 알아보기][lnk-eg-overview]
* [IoT Hub 이벤트 라우팅 및 메시지 간의 차이점 비교][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md