---
title: IoT Hub에 대한 Azure Event Grid 스키마 | Microsoft Docs
description: 이벤트 스키마 양식과 IoT Hub의 속성에 대한 참조 페이지
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 812ca3ba546112f54a76319fda853d441ce34f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT Hub에 대한 Azure Event Grid 이벤트 스키마

이 아티클에서는 Azure IoT Hub 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure IoT Hub는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 장치를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 장치를 삭제하는 경우 게시합니다. | 

## <a name="example-event"></a>예제 이벤트

DeviceCreated 및 DeviceDeleted 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 장치가 IoT 허브에 등록될 때 발생하는 이벤트의 스키마를 보여 줍니다.

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

### <a name="event-properties"></a>이벤트 속성

모든 이벤트에는 동일한 최상위 수준 데이터가 포함됩니다. 

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| 데이터 | object | IoT Hub 이벤트 데이터입니다.  |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 내용은 각 이벤트 게시자에 따라 다릅니다. IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| hubName | string | 장치가 만들어지거나 삭제된 IoT Hub의 이름입니다. |
| deviceId | string | 장치의 고유 식별자입니다. 이 대/소문자 구분 문자열은 최대 128자까지 가능하며, ASCII 7 비트 영숫자 문자 + 다음 특수 문자 `- : . + % _ # * ? ! ( ) , = @ ; $ '`을 지원합니다. |
| operationTimestamp | string | ISO8601 작업 타임스탬프입니다. |
| opType | string | IoT Hub에서 이 작업에 대해 지정한 이벤트 유형은 `DeviceCreated` 또는 `DeviceDeleted`입니다.
| 쌍 | object | 응용 프로그램 장치 메타데이터의 클라우드 표현인 장치 쌍에 관한 정보입니다. | 
| deviceID | string | 장치 쌍의 고유 식별자입니다. | 
| etag | string | 장치 쌍의 내용을 설명하는 정보입니다. 각 etag은 장치 쌍마다 고유합니다. | 
| status | string | 장치 쌍의 활성화 여부를 나타냅니다. | 
| statusUpdateTime | string | 마지막 장치 쌍 상태 업데이트의 ISO8601 타임 스탬프입니다. |
| connectionState | string | 장치의 연결 여부를 나타냅니다. | 
| lastActivityTime | string | 마지막 활동의 ISO8601 타임스탬프입니다. | 
| cloudToDeviceMessageCount | 정수 | 이 장치에 전송된 클라우드-장치 메시지 횟수입니다. | 
| authenticationType | string | 이 장치에 사용되는 인증 유형은 `SAS`, `SelfSigned` 또는 `CertificateAuthority`입니다. |
| x509Thumbprint | string | 지문은 x509 인증서에 대한 고유값으로, 인증서 저장소에서 특정 인증서를 찾는 데 주로 사용됩니다. 지문은 SHA1 알고리즘을 사용하여 동적으로 생성되며, 인증서에 실제로 존재하지는 않습니다. | 
| primaryThumbprint | string | x509 인증서에 대한 주된 지문입니다. |
| secondaryThumbprint | string | x509 인증서에 대한 보조 지문입니다. | 
| 버전 | 정수 | 장치 쌍이 업데이트될 때마다 하나씩 증가하는 정수입니다. |
| 원하는 | object | 응용 프로그램 백 엔드에서만 작성할 수 있고 장치에서 읽을 수 있는 속성의 일부입니다. | 
| 보고된 | object | 장치에서만 작성할 수 있고 응용 프로그램 백 엔드에서 읽을 수 있는 속성의 일부입니다. |
| lastUpdated | string | 마지막 장치 쌍 속성 업데이트의 ISO8601 타임 스탬프입니다. | 

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* IoT Hub 및 Event Grid와 함께 작동하는 방법에 대해 알아보려면 [동작을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md)을 참조하세요.