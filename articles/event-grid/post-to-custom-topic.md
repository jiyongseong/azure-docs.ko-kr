---
title: 사용자 지정 Azure Event Grid 토픽에 이벤트 게시
description: Azure Event Grid에 대한 사용자 지정 토픽에 이벤트를 게시하는 방법 설명
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: tomfitz
ms.openlocfilehash: e4256de1d9112d785b6d1cd52067fc99144a0a04
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Azure Event Grid에 대한 사용자 지정 토픽에 게시

이 문서에서는 Azure Event Grid에 대한 사용자 지정 토픽에 이벤트를 게시하는 방법을 설명합니다. 게시 및 이벤트 데이터의 형식을 보여 줍니다. [SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)는 기대한 형식과 일치하는 게시에만 적용됩니다.

## <a name="endpoint"></a>끝점

HTTP POST를 사용자 지정 토픽에 보낼 때 URI 형식: `https://<topic-endpoint>?api-version=2018-01-01`을 사용합니다.

예를 들어 올바른 URI는 `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`입니다.

Azure CLI에서 사용자 지정 토픽에 대한 엔드포인트를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Azure PowerShell에서 사용자 지정 토픽에 대한 엔드포인트를 가져오려면 다음을 사용합니다.

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>헤더

요청에 인증을 위한 키를 포함하며 이름이 `aeg-sas-key`인 헤더를 포함합니다.

예를 들어 올바른 헤더 값은 `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`입니다.

Azure CLI에서 사용자 지정 토픽에 대한 키를 가져오려면 다음을 사용합니다.

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

PowerShell에서 사용자 지정 토픽에 대한 키를 가져오려면 다음을 사용합니다.

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>이벤트 데이터

사용자 지정 토픽에서 최상위 수준 데이터에는 표준 리소스 정의 이벤트와 동일한 필드가 있습니다. 이러한 속성 중 하나는 사용자 지정 토픽에 고유한 속성을 포함하는 데이터 속성입니다. 이벤트 게시자가 이 데이터 개체에 대한 속성을 결정합니다. 다음 스키마를 사용합니다.

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

이 속성에 대한 설명은 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 이벤트를 Event Grid 항목에 게시할 때 배열은 최대 1MB의 전체 크기를 가질 수 있습니다. 배열의 각 이벤트는 64KB로 제한됩니다.

예를 들어 올바른 이벤트 데이터 스키마는 다음과 같습니다.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>response

항목 엔드포인트에 게시한 후 응답을 수신합니다. 응답은 표준 HTTP 응답 코드입니다. 몇 가지 일반적인 응답은 다음과 같습니다.

|결과  |response  |
|---------|---------|
|성공  | 200 정상  |
|이벤트 데이터의 형식이 잘못되었습니다. | 400 잘못된 요청 |
|잘못된 액세스 키 | 401 권한 없음 |
|잘못된 엔드포인트 | 404 찾을 수 없음 |
|배열 또는 이벤트가 크기 제한을 초과합니다. | 413 페이로드가 너무 큼 |

오류의 경우 메시지 본문에 다음과 같은 형식이 있습니다.

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
