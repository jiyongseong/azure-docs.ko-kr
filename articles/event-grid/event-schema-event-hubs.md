---
title: Azure Event Grid 이벤트 허브 이벤트 스키마
description: Azure Event Grid를 사용하여 이벤트 허브 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6fac70a8837245e6d60a5a61db8a22ab718d4b49
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid 이벤트 허브에 대한 이벤트 스키마

이 문서에서는 이벤트 허브 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Event Hubs는 캡처 파일이 생성되면 **Microsoft.EventHub.CaptureFileCreated** 이벤트 유형을 내보냅니다.

## <a name="example-event"></a>예제 이벤트

이 샘플 이벤트는 캡처 기능에서 파일을 저장할 때 발생하는 이벤트 허브 이벤트의 스키마를 보여 줍니다. 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 이벤트 허브 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| fileUrl | string | 캡처 파일에 대한 경로입니다. |
| fileType | string | 캡처 파일의 파일 형식입니다. |
| partitionId | string | shard ID입니다. |
| sizeInBytes | 정수 | 파일 크기입니다. |
| eventCount | 정수 | 파일에 있는 이벤트의 수입니다. |
| firstSequenceNumber | 정수 | 큐의 가장 작은 시퀀스 번호입니다. |
| lastSequenceNumber | 정수 | 큐의 마지막 시퀀스 번호입니다. |
| firstEnqueueTime | string | 큐의 처음 시간입니다. |
| lastEnqueueTime | string | 큐의 마지막 시간입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* 이벤트 허브 이벤트 처리에 대한 자세한 내용은 [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)을 참조하세요.