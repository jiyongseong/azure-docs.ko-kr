---
title: CloudEvents 스키마에서 이벤트에 Azure Event Grid 사용
description: Azure Event Grid에서 이벤트에 대한 CloudEvents 스키마를 설정하는 방법을 설명합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 23187fbc230e384984085d330bfbfbc90cc9f945
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Event Grid에서 CloudEvents 스키마 사용

[기본 이벤트 스키마](event-schema.md) 외에, Azure Event Grid는 기본적으로 [CloudEvents JSON 스키마](https://github.com/cloudevents/spec/blob/master/json-format.md)의 이벤트를 지원합니다. [CloudEvents](http://cloudevents.io/)는 일반적인 방법으로 이벤트 데이터를 설명하는 [공개 표준 사양](https://github.com/cloudevents/spec/blob/master/spec.md)입니다.

CloudEvents는 클라우드 기반 이벤트를 게시 및 사용하기 위한 일반적인 이벤트 스키마를 제공하여 상호 운용성을 간소화합니다. 이 스키마를 통해 균일한 도구, 이벤트를 라우팅 및 처리하는 표준 방법, 외부 이벤트 스키마를 역직렬화하는 유니버설 방법이 가능해집니다. 공통 스키마를 통해 여러 플랫폼에서 작업을 보다 쉽게 통합할 수 있습니다.

CloudEvents는 [Cloud Native Compute Foundation](https://www.cncf.io/)을 통해 Microsoft를 비롯한 일부 [공동 작업자](https://github.com/cloudevents/spec/blob/master/community/contributors.md)가 작성하고 있습니다. 현재, 버전 0.1로 제공됩니다.

이 문서에서는 Event Grid에서 CloudEvents 스키마를 사용하는 방법을 설명합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent 스키마

CloudEvents 형식의 Azure Blob 저장소 이벤트의 예는 다음과 같습니다.

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1에서는 다음과 같은 속성을 사용할 수 있습니다.

| CloudEvents        | type     | 예제 JSON 값             | 설명                                                        | Event Grid 매핑
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | 문자열   | "com.example.someevent"          | 발생한 이벤트 유형                                   | eventType
| eventTypeVersion   | 문자열   | "1.0"                            | eventType 버전(선택 사항)                            | dataVersion
| cloudEventsVersion | 문자열   | "0.1"                            | 이벤트가 사용하여 CloudEvents 사양 버전        | *통과*
| 원본             | URI      | "/mycontext"                     | 이벤트 생산자를 설명합니다.                                       | topic#subject
| eventID            | 문자열   | "1234-1234-1234"                 | 이벤트의 ID                                                    | id
| eventTime          | 타임 스탬프| "2018-04-05T17:31:00Z"           | 이벤트가 발생한 때의 타임스탬프(선택 사항)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | 데이터 특성이 준수하는 스키마에 대한 링크(선택 사항) | *사용되지 않음*
| contentType        | 문자열   | "application/json"               | 데이터 인코딩 형식을 설명합니다(선택 사항).                       | *사용되지 않음*
| 확장         | Map      | { "extA": "vA", "extB", "vB" }  | 추가 메타데이터(선택 사항)                                 | *사용되지 않음*
| 데이터               | Object   | { "objA": "vA", "objB", "vB" }  | 이벤트 페이로드(선택 사항)                                       | 데이터

자세한 내용은 [CloudEvents 사양](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes)을 참조하세요.

## <a name="configure-event-grid-for-cloudevents"></a>CloudEvents에 대한 Event Grid 구성

현재, Azure Event Grid에서는 **미국 중서부**, **미국 중부** 및 **유럽 북부**에서 CloudEvents JSON 형식의 입출력이 미리 보기로 지원됩니다.

CloudEvents 스키마에서 이벤트의 입출력 둘 다에 Event Grid를 사용할 수 있습니다. Blob Storage 이벤트 및 IoT Hub 이벤트와 같은 시스템 이벤트와 사용자 지정 이벤트에 CloudEvents를 사용할 수 있습니다. 또한 온라인 상태에서 이러한 이벤트를 변환할 수도 있습니다.


| 입력 스키마       | 출력 스키마입니다.
|--------------------|---------------------
| CloudEvents 형식 | CloudEvents 형식
| Event Grid 형식  | CloudEvents 형식
| CloudEvents 형식 | Event Grid 형식
| Event Grid 형식  | Event Grid 형식

모든 이벤트 스키마에 대해, Event Grid는 Event Grid 토픽에 게시할 때와 이벤트 구독을 만들 때 유효성 검사를 요구합니다. 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

### <a name="input-schema"></a>입력 스키마

사용자 지정 토픽에 대한 입력 스키마를 CloudEvents로 설정하려면 토픽 `--input-schema cloudeventv01schema`를 만들 때 Azure CLI에서 다음 매개 변수를 사용합니다. 이제 사용자 지정 토픽의 들어오는 이벤트는 CloudEvents v0.1 형식을 갖습니다.

Event Grid 토픽을 만들려면 다음을 사용합니다.

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

현재 버전의 CloudEvents는 이벤트의 일괄 처리를 지원하지 않습니다. CloudEvent 스키마를 갖는 이벤트를 토픽에 게시하려면 각 이벤트를 개별적으로 게시합니다.

### <a name="output-schema"></a>출력 스키마입니다.

이벤트 구독의 출력 스키마를 CloudEvents로 설정하려면 이벤트 구독 `--event-delivery-schema cloudeventv01schema`를 만들 때 Azure CLI에서 다음 매개 변수를 사용합니다. 이 이벤트 구독에 대한 이벤트는 이제 CloudEvents v0.1 형식으로 배달할 수 있습니다.

이벤트 구독을 만들려면 다음을 사용하세요.

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

현재 버전의 CloudEvents는 이벤트의 일괄 처리를 지원하지 않습니다. CloudEvent 스키마용으로 구성된 이벤트 구독은 각 이벤트를 개별적으로 수신합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* CloudEvents를 테스트하고 주석을 달고 [참여](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md)할 것을 권장합니다.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
