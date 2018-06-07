---
title: Azure Functions의 Event Grid 트리거
description: Azure Functions에서 Event Grid 이벤트를 처리하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 9228b1e80c8c46780a24d33e13fcedbd8da63ac3
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Functions의 Event Grid 트리거

이 문서에서는 Azure Functions에서 [Event Grid](../event-grid/overview.md) 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 *게시자*에서 발생하는 이벤트를 알리기 위해 HTTP 요청을 전송하는 Azure 서비스입니다. 게시자는 이벤트를 시작하는 서비스 또는 리소스입니다. 예를 들어, Azure Blob 저장소 계정은 게시자이고 [Blob 업로드 또는 삭제는 이벤트](../storage/blobs/storage-blob-event-overview.md)입니다. 일부 [Azure 서비스에는 Event Grid에 이벤트를 게시하는 방식을 기본적으로 지원합니다](../event-grid/overview.md#event-sources). 

이벤트 *처리기*는 이벤트를 수신하고 처리합니다. Azure Functions는 [Event Grid 이벤트를 처리를 기본적으로 지원하는 Azure 서비스](../event-grid/overview.md#event-handlers) 중 하나입니다. 이 문서에서는 Event Grid에서 이벤트가 수신될 때 Event Grid 트리거를 사용하여 함수를 호출하는 방법을 알아봅니다.

원할 경우 HTTP 트리거를 사용하여 Event Grid 이벤트를 처리할 수 있습니다. 관련 내용은 이 문서 뒷부분에서 [HTTP 트리거를 Event Grid 트리거로 사용](#use-an-http-trigger-as-an-event-grid-trigger)을 참조하세요.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>패키지

Event Grid 트리거는 [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet 패키지에 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension) GitHub 리포지토리에 있습니다.

<!--
If you want to bind to the `Microsoft.Azure.EventGrid.Models.EventGridEvent` type instead of `JObject`, install the [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) package.
-->

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="example"></a>예

Event Grid 트리거에 대한 다음과 같은 언어별 예제를 참조하세요.

* [C#](#c-example)
* [C# 스크립트(.csx)](#c-script-example)
* [JavaScript](#javascript-example)

HTTP 트리거 예제에 대해서는 이 문서 뒷부분에 나오는 [HTTP 트리거 사용 방법](#use-an-http-trigger-as-an-event-grid-trigger)을 참조하세요.

### <a name="c-example"></a>C# 예제

다음 예제에서는 `JObject`에 바인딩되는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

<!--
The following example shows a [C# function](functions-dotnet-class-library.md) that binds to `EventGridEvent`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
            public static void EventGridTest([EventGridTrigger] Microsoft.Azure.EventGrid.Models.EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info("C# Event Grid function processed a request.");
            log.Info($"Subject: {eventGridEvent.Subject}");
            log.Info($"Time: {eventGridEvent.EventTime}");
            log.Info($"Data: {eventGridEvent.Data.ToString()}");
        }
    }
}
```
-->

자세한 내용은 [패키지](#packages), [특성](#attributes), [구성](#configuration) 및 [사용](#usage)을 참조하세요.

### <a name="c-script-example"></a>C# 스크립트 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

다음은 `JObject`에 바인딩하는 C# 스크립트 코드입니다.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

<!--
Here's C# script code that binds to `EventGridEvent`:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
#r "Microsoft.Azure.EventGrid"

using Microsoft.Azure.WebJobs.Extensions.EventGrid;
Using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```
-->

자세한 내용은 [패키지](#packages), [특성](#attributes), [구성](#configuration) 및 [사용](#usage)을 참조하세요.

### <a name="javascript-example"></a>JavaScript 예제

다음 예제는 *function.json* 파일의 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) 특성을 사용합니다.

다음은 메서드 서명의 `EventGridTrigger` 특성입니다.

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

전체 예제는 [C# 예제](#c-example)를 참조하세요.

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. `EventGridTrigger` 특성에서 설정할 생성자 매개 변수 또는 속성은 없습니다.

|function.json 속성 |설명|
|---------|---------|----------------------|
| **type** | 필수 - `eventGridTrigger`으로 설정해야 합니다. |
| **direction** | 필수 - `in`으로 설정해야 합니다. |
| **name** | 필수 - 이벤트 데이터를 수신하는 매개 변수에 대한 함수 코드에 사용되는 변수 이름입니다. |

## <a name="usage"></a>사용 현황

C# 및 C# 함수의 경우, Event Grid 트리거에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `JObject`
* `string`
* `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` - 모든 이벤트 유형에 공통되는 필드의 속성을 정의합니다. **이 유형은 더 이상 지원되지 않지만** 대체 유형이 NuGet에 아직 게시되지 않았습니다.

JavaScript 함수의 경우 *function.json* `name` 속성에 따라 명명되는 매개 변수가 이벤트 개체를 참조합니다.

## <a name="event-schema"></a>이벤트 스키마

Event Grid 이벤트의 데이터는 HTTP 요청 본문에 JSON 개체로 수신됩니다. JSON은 다음 예제와 유사합니다.

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

표시된 예제는 한 요소의 배열입니다. Event Grid는 항상 배열을 전송하며, 배열에서 둘 이상의 이벤트를 전송할 수 있습니다. 런타임은 각 배열 요소에 대해 한 번씩 함수를 호출합니다.

이벤트 JSON 데이터의 최상위 속성은 모든 이벤트 유형에서 동일하지만, `data` 속성의 내용은 이벤트 유형마다 다릅니다. 표시된 예제는 blob 저장소 이벤트에 대한 것입니다.

공통 및 이벤트별 속성에 대한 설명을 보려면 Event Grid 설명서에서 [이벤트 속성](../event-grid/event-schema.md#event-properties)을 참조하세요.

`EventGridEvent` 형식은 최상위 속성만 정의하며, `Data` 속성은 `JObject`입니다. 

## <a name="create-a-subscription"></a>구독 만들기

Event Grid HTTP 요청 수신을 시작하려면 함수를 호출하는 끝점 URL을 지정하는 Event Grid 구독을 만듭니다.

### <a name="azure-portal"></a>Azure portal

Event Grid 트리거를 사용하여 Azure Portal에서 개발하는 함수에 대해 **Event Grid 구독 추가**를 선택합니다.

![포털에서 구독 만들기](media/functions-bindings-event-grid/portal-sub-create.png)

이 링크를 선택하면 포털에서 끝점 URL로 미리 채워진 **이벤트 구독 만들기** 페이지가 열립니다.

![미리 채워진 끝점 URL](media/functions-bindings-event-grid/endpoint-url.png)

Azure Portal을 사용하여 구독을 만드는 방법에 대한 자세한 내용은 Event Grid 설명서에서 [사용자 지정 이벤트 만들기 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)을 참조하세요.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)를 사용하여 구독을 만들려면 [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create)를 사용합니다.

이 명령에는 함수를 호출하는 끝점 URL이 필요합니다. 다음 예제에서는 URL 패턴을 보여 줍니다.

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

시스템 키는 Event Grid 트리거에 대한 끝점 URL에 포함되어야 하는 인증 키입니다. 다음 섹션에서는 시스템 키를 가져오는 방법을 설명합니다.

다음은 Blob 저장소 계정을 구독하는 예제입니다(시스템 키에 대한 자리 표시자 포함).

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

구독을 만드는 방법에 대한 자세한 내용은 [Blob 저장소 빠른 시작](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) 또는 다른 Event Grid 빠른 시작을 참조하세요.

### <a name="get-the-system-key"></a>시스템 키 가져오기

다음 API(HTTP GET)를 사용하여 시스템 키를 가져올 수 있습니다.

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

이것은 관리자 API로, 사용자의 [관리자 키](functions-bindings-http-webhook.md#authorization-keys)를 입력해야 합니다. 시스템 키(Event Grid 트리거 함수 호출용)와 관리자 키(함수 앱의 관리 작업 수행용)를 혼동하지 마세요. Event Grid 토픽을 구독할 때는 시스템 키를 사용해야 합니다.

시스템 키를 제공하는 응답 예제는 다음과 같습니다.

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

자세한 내용은 HTTP 트리거 참조 문서에서 [인증 키](functions-bindings-http-webhook.md#authorization-keys)를 참조하세요. 

또는 HTTP PUT을 전송하여 키 값을 직접 지정할 수 있습니다.

## <a name="local-testing-with-requestbin"></a>RequestBin을 사용한 로컬 테스트

> [!NOTE]
> 현재 RequestBin 사이트를 사용할 수 없지만, 그 대신 https://hookbin.com에 이 방법을 사용할 수 있습니다. 해당 사이트가 중지된 경우 [ngrok](#local-testing-with-ngrok)를 사용하면 됩니다.

Event Grid 트리거를 로컬로 테스트하려면 클라우드의 원본에서 로컬 컴퓨터로 전달된 Event Grid HTTP 요청을 가져와야 합니다. 이 작업을 수행하는 한 가지 방법은 온라인으로 요청을 캡처하고 로컬 컴퓨터에서 수동으로 다시 전송하는 것입니다.

2. [RequestBin 끝점을 만듭니다](#create-a-RequestBin-endpoint).
3. RequestBin 끝점으로 이벤트를 보내는 [Event Grid 구독을 만듭니다](#create-an-event-grid-subscription).
4. RequestBin 사이트에서 [요청을 생성](#generate-a-request)하고 요청 본문을 복사합니다.
5. Event Grid 트리거 함수의 localhost URL에 [요청을 수동으로 게시](#manually-post-the-request)합니다.

테스트가 완료되면 끝점을 업데이트하여 프로덕션 환경에도 동일한 구독을 사용할 수 있습니다. [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI 명령을 사용합니다.

### <a name="create-a-requestbin-endpoint"></a>RequestBin 끝점 만들기

RequestBin은 HTTP 요청을 수락하고 요청 본문을 표시하는 오픈 소스 도구입니다. http://requestb.in URL은 Azure Event Grid에서 특수하게 처리됩니다. 테스트를 용이하게 진행하기 위해 Event Grid는 구독 유효성 검사 요청에 대한 올바른 응답을 요구하지 않고, RequestBin URL로 이벤트를 전송합니다. 다른 테스트 도구 하나(http://hookbin.com)는 동일하게 처리됩니다.

처리량이 높은 경우에는 RequestBin 사용이 적합하지 않습니다. 한 번에 둘 이상의 이벤트를 푸시하면 도구에서 모든 이벤트가 표시되지 않을 수 있습니다.

끝점을 만듭니다.

![RequestBin 끝점 만들기](media/functions-bindings-event-grid/create-requestbin.png)

끝점 URL을 복사합니다.

![RequestBin 끝점 복사](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기

테스트하려는 유형의 Event Grid 구독을 만들고 RequestBin 끝점에 제공합니다. 구독을 만드는 방법에 대한 내용은 이 문서 앞부분에 나오는 [구독 만들기](#create-a-subscription)를 참조하세요.

### <a name="generate-a-request"></a>요청 생성

RequestBin 끝점으로의 HTTP 트래픽을 생성하는 이벤트를 트리거합니다.  예를 들어, Blob 저장소 구독을 만든 경우 Blob을 업로드하거나 삭제합니다. RequestBin 페이지에 요청이 표시되면 요청 본문을 복사합니다.

구독 유효성 검사 요청이 먼저 수신됩니다. 유효성 검사 요청은 모두 무시하고 이벤트 요청을 복사합니다.

![RequestBin에서 요청 본문 복사](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>수동으로 요청 게시

Event Grid 함수를 로컬로 실행합니다.

[Postman](https://www.getpostman.com/) 또는 [curl](https://curl.haxx.se/docs/httpscripting.html)과 같은 도구를 사용하여 HTTP POST 요청을 만듭니다.

* `Content-Type: application/json` 헤더를 설정합니다.
* `aeg-event-type: Notification` 헤더를 설정합니다.
* 요청 본문에 RequestBin 데이터를 붙여 넣습니다. 
* 다음 패턴을 사용하여 Event Grid 트리거 함수의 URL에 게시합니다.

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` 매개 변수는 `FunctionName` 특성에 지정된 이름이어야 합니다.

다음 스크린샷에서는 Postman의 헤더 및 요청 본문을 보여 줍니다.

![Postman의 헤더](media/functions-bindings-event-grid/postman2.png)

![Postman의 요청 본문](media/functions-bindings-event-grid/postman.png)

Event Grid 트리거 함수가 실행되고, 다음 예제와 비슷한 결과를 표시합니다.

![샘플 Event Grid 트리거 함수 로그](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>ngrok를 사용한 로컬 테스트

Event Grid 트리거를 로컬로 테스트하는 다른 방법은 인터넷과 개발 컴퓨터 간의 HTTP 연결을 자동화하는 것입니다. [ngrok](https://ngrok.com/)이라는 오픈 소스 도구를 사용하여 이 작업을 수행할 수 있습니다.

3. [ngrok 끝점을 만듭니다](#create-an-ngrok-endpoint).
4. [Event Grid 트리거 함수를 실행](#run-the-event-grid-trigger-function)합니다.
5. ngrok 끝점으로 이벤트를 보내는 [Event Grid 구독을 만듭니다](#create-a-subscription).
6. [이벤트를 트리거](#trigger-an-event)합니다.

테스트가 완료되면 끝점을 업데이트하여 프로덕션 환경에도 동일한 구독을 사용할 수 있습니다. [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI 명령을 사용합니다.

### <a name="create-an-ngrok-endpoint"></a>ngrok 끝점 만들기

[ngrok](https://ngrok.com/)에서 *ngrok.exe*를 다운로드하고 다음 명령을 사용해서 실행합니다.

```
ngrok http -host-header=localhost 7071
```

함수 런타임은 localhost에서 실행될 때 localhost의 요청을 예상하기 때문에 -host-header 매개 변수가 필요합니다. 7071은 런타임이 로컬로 실행될 때의 기본 포트 번호입니다.

이 명령은 다음과 같은 출력을 만듭니다.

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Event Grid 구독에 대해 https://{subdomain}.ngrok.io URL을 사용하게 됩니다.

### <a name="run-the-event-grid-trigger-function"></a>Event Grid 트리거 함수 실행

ngrok URL은 Event Grid에서 특수하게 처리되지 않으므로, 구독이 만들어질 때 함수가 로컬로 실행되고 있어야 합니다. 그렇지 않은 경우 유효성 검사 응답이 전송되지 않으며 구독을 만들지 못합니다.

### <a name="create-a-subscription"></a>구독 만들기

다음 패턴을 사용해서 테스트하려는 유형의 Event Grid 구독을 만들고 ngrok 끝점에 제공합니다.

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` 매개 변수는 `FunctionName` 특성에 지정된 이름이어야 합니다.

다음은 Azure CLI 사용 예입니다.

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

구독을 만드는 방법에 대한 내용은 이 문서 앞부분에 나오는 [구독 만들기](#create-a-subscription)를 참조하세요.

### <a name="trigger-an-event"></a>이벤트 트리거

ngrok 끝점으로의 HTTP 트래픽을 생성하는 이벤트를 트리거합니다.  예를 들어, Blob 저장소 구독을 만든 경우 Blob을 업로드하거나 삭제합니다.

Event Grid 트리거 함수가 실행되고, 다음 예제와 비슷한 결과를 표시합니다.

![샘플 Event Grid 트리거 함수 로그](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>HTTP 트리거를 Event Grid 트리거로 사용

Event Grid 이벤트는 HTTP 요청으로 수신되므로, Event Grid 트리거 대신 HTTP 트리거를 사용하여 이벤트를 처리할 수 있습니다. 이렇게 하는 한 가지 가능한 이유는 함수를 호출하는 끝점 URL을 보다 강력하게 제어할 수 있기 때문입니다. 

HTTP 트리거를 사용하는 경우, 다음과 같이 Event Grid 트리거가 자동으로 수행하는 작업에 대한 코드를 작성해야 합니다.

* 유효성 검사 응답을 [구독 유효성 검사 요청](../event-grid/security-authentication.md#webhook-event-delivery)으로 보냅니다.
* 요청 본문에 포함된 이벤트 배열의 요소별로 한 번씩 함수를 호출합니다.

HTTP 트리거에 대한 다음 샘플 C# 코드는 Event Grid 트리거 동작을 시뮬레이트합니다.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

HTTP 트리거에 대한 다음 샘플 JavaScript 코드는 Event Grid 트리거 동작을 시뮬레이트합니다.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: JSON.stringify({validationResponse: messages[0].data.validationCode}) }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

이벤트 처리 코드는 `messages` 배열을 통해 루프로 들어갑니다.

함수를 로컬로 호출하거나 Azure에서 실행할 때 사용할 URL에 대한 내용은 [HTTP 트리거 바인딩 참조 설명서](functions-bindings-http-webhook.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Event Hubs에 대한 자세한 정보](../event-grid/overview.md)
