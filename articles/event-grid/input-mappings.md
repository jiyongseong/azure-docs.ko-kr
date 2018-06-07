---
title: Azure Event Grid 스키마에 사용자 지정 필드 매핑
description: 사용자 지정 스키마를 Azure Event Grid 스키마로 변환하는 방법을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Event Grid 스키마에 사용자 지정 필드 매핑

이벤트 데이터가 예상되는 [Event Grid 스키마](event-schema.md)와 일치하지 않는 경우 계속 Event Grid를 사용하여 구독자에 이벤트를 라우팅할 수 있습니다. 이 문서에서는 Event Grid 스키마로 사용자의 스키마를 매핑하는 방법을 설명합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>원래 이벤트 스키마

다음과 같은 형식의 이벤트를 보내는 응용 프로그램이 있다고 가정하겠습니다.

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

해당 형식이 필요한 스키마와 일치하지 않지만 Event Grid를 사용하면 스키마에 사용자의 필드를 매핑할 수 있습니다. 또는 원래 스키마의 값을 받을 수 있습니다.

## <a name="create-custom-topic-with-mapped-fields"></a>매핑된 필드로 사용자 지정 토픽 만들기

사용자 지정 토픽을 만들 때 Event Grid 스키마로 사용자의 원래 이벤트의 필드를 매핑하는 방법을 지정합니다. 매핑을 사용자 지정할 때 사용하는 세 가지 속성이 있습니다.

* `--input-schema` 매개 변수는 스키마의 형식을 지정합니다. 사용 가능한 옵션은 *cloudeventv01schema*, *customeventschema* 및 *eventgridschema*입니다. 기본값은 eventgridschema입니다. 사용자의 스키마와 Event Grid 스키마 간 사용자 지정 매핑을 만들 때는 customeventschema를 사용합니다. 이벤트가 CloudEvents 스키마에 있는 경우 cloudeventv01schema를 사용합니다.

* `--input-mapping-default-values` 매개 변수는 Event Grid 스키마의 필드에 대한 기본값을 지정합니다. *subject*, *eventtype* 및 *dataversion*에 대한 기본값을 설정할 수 있습니다. 일반적으로 사용자 지정 스키마가 이 세 필드 중 하나에 해당하는 필드를 포함하지 않는 경우에 이 매개 변수를 사용합니다. 예를 들어 해당 dataversion은 항상 **1.0**으로 설정됩니다.

* `--input-mapping-fields` 매개 변수는 Event Grid 스키마로 사용자 스키마의 필드를 매핑합니다. 공백으로 구분된 키/값 쌍의 값을 지정합니다. 키 이름의 경우 Event Grid 필드의 이름을 사용합니다. 값의 경우 사용자의 필드 이름을 사용합니다. *id*, *topic*, *eventtime*, *subject*, *eventtype* 및 *dataversion*에 대한 키 이름을 사용할 수 있습니다.

다음 예제에서는 일부 매핑된 기본 필드를 사용하여 사용자 지정 토픽을 만듭니다.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Event Grid 토픽 구독

사용자 지정 항목을 구독할 경우 이벤트를 수신하는 데 사용할 스키마를 지정합니다. `--event-delivery-schema` 매개 변수를 사용하여 *cloudeventv01schema*, *eventgridschema* 또는 *inputeventschema*로 설정합니다. 기본값은 eventgridschema입니다.

이 섹션의 예제에서는 이벤트 처리기에 대한 큐 저장소를 사용합니다. 자세한 내용은 [Azure Queue Storage로 사용자 지정 이벤트 라우팅](custom-event-to-queue-storage.md)을 참조하세요.

다음 예제에서는 Event Grid 토픽을 구독하고 기본 Event Grid 스키마를 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

다음 예제에서는 이벤트의 입력 스키마를 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>항목에 이벤트 게시

이제 사용자 지정 토픽에 이벤트를 보내고 매핑의 결과 확인할 준비가 되었습니다. 다음 스크립트는 [예제 스키마](#original-event-schema)에서 이벤트를 게시합니다.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

이제 큐 저장소를 살펴 봅니다. 두 개의 구독이 서로 다른 스키마에 이벤트를 전달합니다.

첫 번째 구독은 Event Grid 스키마를 사용합니다. 전달된 이벤트의 형식은 다음과 같습니다.

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

이러한 필드에는 사용자 지정 토픽에 나온 매핑을 포함합니다. **myEventTypeField**는 **EventType**에 매핑됩니다. **DataVersion** 및 **Subject**에 대한 기본값이 사용됩니다. **Data** 개체에는 원래 이벤트 스키마 필드가 포함됩니다.

두 번째 구독은 입력 이벤트 스키마를 사용합니다. 전달된 이벤트의 형식은 다음과 같습니다.

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

원본 필드가 전달되었음을 확인합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
