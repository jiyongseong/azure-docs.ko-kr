---
title: Azure Event Grid 구독 이벤트 스키마
description: Azure Event Grid를 사용하여 구독 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 04bcc3018346e9cf162d26b0a23147c88e902e1e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid 구독에 대한 이벤트 스키마

이 문서에서는 Azure 구독 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

Azure 구독 및 리소스 그룹은 동일한 이벤트 유형을 내보냅니다. 이벤트 유형은 리소스의 변경과 관련됩니다. 주요 차이는 리소스 그룹이 리소스 그룹 내 리소스에 대한 이벤트를 내보내고 Azure 구독은 구독 전체에서 리소스에 대한 이벤트를 내보낸다는 것입니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

VM을 만들거나 저장소 계정을 삭제할 때와 같이 Azure 구독은 Azure Resource Manager에서 관리 이벤트를 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | 리소스 생성 또는 업데이트 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceWriteFailure | 리소스 생성 또는 업데이트 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceWriteCancel | 리소스 생성 또는 업데이트 작업이 취소되면 발생합니다. |
| Microsoft.Resources.ResourceDeleteSuccess | 리소스 삭제 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteFailure | 리소스 삭제 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteCancel | 리소스 삭제 작업이 취소되면 발생합니다. 이 이벤트는 템플릿 배포가 취소될 때 발생합니다. |

## <a name="example-event"></a>예제 이벤트

다음 예제는 리소스가 생성된 이벤트의 스키마를 보여줍니다. 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
      "dataVersion": "",
      "metadataVersion": "1"
  }
]
```

리소스가 삭제된 이벤트의 스키마는 다음과 유사합니다.

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
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
| 데이터 | object | 구독 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 권한 부여 | string | 작업에 대해 요청된 권한입니다. |
| claims | string | 클레임의 속성입니다. |
| CorrelationId | string | 문제 해결을 위한 작업 ID입니다. |
| httpRequest | string | 작업의 세부 정보입니다. |
| resourceProvider | string | 작업을 수행하는 리소스 공급자입니다. |
| resourceUri | string | 작업에서 리소스의 URI입니다. |
| operationName | string | 수행된 작업입니다. |
| status | string | 작업의 상태. |
| subscriptionId | string | 리소스의 구독 ID입니다. |
| tenantId | string | 리소스의 테넌트 ID입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
