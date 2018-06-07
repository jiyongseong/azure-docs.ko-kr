---
title: Azure Functions HTTP 및 WebHook 바인딩
description: Azure Functions에서 HTTP와 WebHook 트리거 및 바인딩을 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처, HTTP, API, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: d15c5556325284dd3b0b6f11a080c9abc263286c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356327"
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP 및 WebHook 바인딩

이 문서에서는 Azure Functions에서 HTTP 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 HTTP 트리거 및 출력 바인딩을 지원합니다.

HTTP 트리거는 [웹후크](https://en.wikipedia.org/wiki/Webhook)에 응답하도록 사용자 지정할 수 있습니다. 웹후크 트리거는 JSON 페이로드만 받아들이고 JSON의 유효성을 검사합니다. GitHub 및 Slack과 같은 특정 공급자의 웹후크를 쉽게 처리할 수 있도록 해 주는 웹후크 트리거의 특수 버전이 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages"></a>패키지

HTTP 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet 패키지에 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="trigger"></a>트리거

HTTP 트리거를 사용하면 HTTP 요청으로 함수를 호출할 수 있습니다. HTTP 트리거를 사용하여 서버리스 API를 만들고 웹후크에 응답할 수 있습니다. 

기본적으로 HTTP 트리거는 Functions 1.x에서 본문이 비어 있는 HTTP 200 OK 또는 Functions 2.x에서 본문이 비어 있는 HTTP 204 No Content를 반환합니다. 응답을 수정하려면 [HTTP 출력 바인딩](#http-output-binding)을 구성합니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다. 반환 값은 출력 바인딩에 사용되지만, 반환 값 특성은 필요 없습니다.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 `HttpRequestMessage`에 바인딩하는 C# 스크립트 코드입니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

`HttpRequestMessage` 대신 사용자 지정 개체에 바인딩할 수 있습니다. 이 개체는 요청 본문에서 만들어지고 JSON으로 구문 분석됩니다. 마찬가지로, 형식을 HTTP 응답 출력 바인딩으로 전달할 수도 있습니다. 그러면 200 상태 코드를 갖는 응답 본문으로 반환됩니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
```

### <a name="trigger---f-example"></a>트리거 - F# 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

F# 코드는 다음과 같습니다.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

다음 예제와 같이 NuGet을 사용하여 `FSharp.Interop.Dynamic` 및 `Dynamitey` 어셈블리를 참조하는 `project.json` 파일이 필요합니다.

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예제는 *function.json* 파일의 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>트리거 - 웹후크 예제

언어 관련 예제를 참조하세요.

* [C#](#webhook---c-example)
* [C# 스크립트(.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>웹후크 - C# 예제

다음 예제는 일반 JSON 요청에 대한 응답으로 HTTP 200을 보내는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>웹후크 - C# 스크립트 예제

다음 예제는 *function.json* 파일의 웹후크 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 이 함수는 GitHub 문제 설명을 기록합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>웹후크 - F# 예제

다음 예제는 *function.json* 파일의 웹후크 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여줍니다. 이 함수는 GitHub 문제 설명을 기록합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

F# 코드는 다음과 같습니다.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>웹후크 - JavaScript 예제

다음 예제는 *function.json* 파일의 웹후크 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 이 함수는 GitHub 문제 설명을 기록합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 특성을 사용합니다.

특성 생성자 매개 변수에 권한 부여 수준 및 허용되는 HTTP 메서드를 설정할 수 있으며 웹후크 유형 및 경로 템플릿에 대한 속성이 있습니다. 이러한 설정에 대한 자세한 내용은 [트리거 - 구성](#trigger---configuration)을 참조하세요. 다음은 메서드 서명의 `HttpTrigger` 특성입니다.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `HttpTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
| **type** | 해당 없음| 필수 - `httpTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음| 필수 - `in`으로 설정해야 합니다. |
| **name** | 해당 없음| 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |키가 있는 경우 함수를 호출하기 위해 요청에 포함되어야 하는 키를 결정합니다. 권한 부여 수준은 다음 값 중 하나일 수 있습니다. <ul><li><code>anonymous</code>&mdash;: API 키가 필요하지 않습니다.</li><li><code>function</code>&mdash;: 함수 전용 API 키가 필요합니다. authLevel 속성 값을 제공하지 않을 경우 기본값입니다.</li><li><code>admin</code>&mdash;: 마스터 키가 필요합니다.</li></ul> 자세한 내용은 [권한 부여 키](#authorization-keys)에 대한 섹션을 참조하세요. |
| **methods** |**메서드** | 함수에서 응답할 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 함수에서 모든 HTTP 메서드에 응답합니다. [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **route** | **Route** | 경로 템플릿을 정의하여 함수에서 응답할 요청 URL을 제어합니다. 값을 제공하지 않을 경우 기본값은 `<functionname>`입니다. 자세한 내용은 [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **webHookType** | **WebHookType** |HTTP 트리거가 지정된 공급자의 [웹후크](https://en.wikipedia.org/wiki/Webhook) 수신기(receiver)로 작동하도록 구성합니다. 이 속성을 설정하면 `methods` 속성을 설정하지 마십시오. 웹후크 형식은 다음 값 중 하나일 수 있습니다.<ul><li><code>genericJson</code>&mdash;특정 공급자를 위한 논리가 없는 범용 webhook 끝점입니다. 이 설정은 HTTP POST 및 `application/json` 콘텐츠 형식을 사용하는 요청으로만 제한됩니다.</li><li><code>github</code>&mdash;이 함수는 [GitHub 웹후크](https://developer.github.com/webhooks/)에 응답합니다. GitHub 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 GitHub 웹후크 섹션을 참조하세요.</li><li><code>slack</code>&mdash;이 함수는 [Slack 웹후크](https://api.slack.com/outgoing-webhooks)에 응답합니다. Slack 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 Slack 웹후크 섹션을 참조하세요.</li></ul>|

## <a name="trigger---usage"></a>트리거 - 사용

C# 및 F# 함수의 경우 트리거 입력 형식을 `HttpRequestMessage` 또는 사용자 지정 형식으로 선언할 수 있습니다. `HttpRequestMessage`를 선택하면 요청 개체에 대한 모든 권한을 갖게 됩니다. 사용자 지정 형식의 경우 함수는 JSON 요청 본문의 구문을 분석하여 개체 속성을 설정하려고 합니다. 

JavaScript 함수의 경우 함수 런타임은 요청 개체 대신 요청 본문을 제공합니다. 자세한 내용은 [JavaScript 트리거 예제](#trigger---javascript-example)를 참조하세요.

### <a name="github-webhooks"></a>GitHub 웹후크

GitHub 웹후크에 응답하려면 먼저 HTTP 트리거를 사용하여 함수를 만들고 **webHookType** 속성을 `github`로 설정합니다. 그런 다음 URL 및 API 키를 GitHub 리포지토리의 **웹후크 추가** 페이지에 복사합니다. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

예제를 보려면 [GitHub 웹후크를 통해 트리거되는 함수 만들기](functions-create-github-webhook-triggered-function.md)를 참조하세요.

### <a name="slack-webhooks"></a>Slack 웹후크

Slack webhook은 함수 전용 키를 지정하는 대신 사용자를 위한 토큰을 생성하므로 Slack의 토큰을 사용하여 함수 전용 키를 구성해야 합니다. [권한 부여 키](#authorization-keys)를 참조하세요.

### <a name="customize-the-http-endpoint"></a>HTTP 엔드포인트 사용자 지정

기본적으로 HTTP 트리거 또는 WebHook용 함수를 만드는 경우 폼의 경로를 사용하여 이 함수의 주소를 지정할 수 있습니다.

    http://<yourapp>.azurewebsites.net/api/<funcname> 

HTTP 트리거의 입력 바인딩에서 선택적 `route` 속성을 사용하여 이 경로를 사용자 지정할 수 있습니다. 예를 들어 다음 *function.json* 파일은 HTTP 트리거에 대한 `route` 속성을 정의합니다.

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

이 구성을 사용하면 원래 경로 대신 다음 경로를 사용하여 함수의 주소를 지정할 수 있습니다.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

이렇게 하면 함수 코드에서 주소의 두 매개 변수, _category_ 및 _id_를 지원할 수 있습니다. 매개 변수에서 [웹 API 경로 제약 조건](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)을 사용할 수 있습니다. 다음 C# 함수 코드는 두 매개 변수를 모두 사용합니다.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

다음은 동일한 경로 매개 변수를 사용하는 Node.js 함수 코드입니다.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

기본적으로 모든 함수 경로에는 *api* 접두사가 붙습니다. [host.json](functions-host-json.md) 파일에서 `http.routePrefix` 속성을 사용하여 접두사를 사용자 지정하거나 제거할 수도 있습니다. 다음 예제에서는 *host.json* 파일에서 빈 문자열을 접두사로 사용하여 *api* 경로 접두사를 제거합니다.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>권한 부여 키

HTTP 트리거를 통해 키를 사용하여 보안을 강화할 수 있습니다. 표준 HTTP 트리거에서 이러한 키는 API 키로 사용될 수 있으므로 요청 시에 필요합니다. Webhooks에서는 공급자가 지원하는 항목에 따라 다양한 방식으로 요청을 인증하는 데 키를 사용할 수 있습니다.

> [!NOTE]
> 함수를 로컬로 실행할 경우 `function.json`에서 `authLevel`이 어떻게 설정되든 권한 부여가 비활성화됩니다. Azure Functions에 게시하는 즉시 `authLevel`이 적용됩니다.

키는 Azure에 함수 앱의 일부로 저장되며 나머지는 암호화되어 있습니다. 키를 보거나, 새 키를 만들거나, 키를 새 값으로 전환하려면 포털에 있는 함수 중 하나를 탐색하여 "관리"를 선택합니다. 

다음과 같이 두 가지 유형의 키가 있습니다.

- **호스트 키**: 함수 앱 내의 모든 함수에서 공유합니다. API 키로 사용되면 이 키를 통해 함수 앱 내의 모든 함수에 액세스할 수 있습니다.
- **function 키**: 정의된 특정 함수에만 적용됩니다. API 키로 사용되면 이 키를 통해 해당 함수에만 액세스할 수 있습니다.

각 키의 이름은 참조될 수 있도록 지정되며 함수 및 호스트 수준에서는 "default"라는 기본 키가 있습니다. function 키는 호스트 키보다 우선합니다. 두 키가 동일한 이름으로 정의되면 항상 함수 키가 사용됩니다.

**master 키**는 각 함수 앱에 대해 정의된 "_master"라는 기본 호스트 키입니다. 이 키를 취소할 수 없습니다. 런타임 API에 대한 관리 액세스를 제공합니다. 바인딩 JSON에서 `"authLevel": "admin"`을 사용하면 요청 시 이 키가 필요합니다. 다른 키는 권한 부여 오류가 발생합니다.

> [!IMPORTANT]  
> master 키에서 부여하는 승격된 권한으로 인해 이 키를 타사와 공유하거나 네이티브 클라이언트 응용 프로그램에 배포해서는 안됩니다. 따라서 관리자 권한 부여 수준을 선택할 때는 주의해야 합니다.

### <a name="api-key-authorization"></a>API 키 권한 부여

기본적으로 HTTP 트리거는 HTTP 요청에서 API 키가 필요합니다. 따라서 HTTP 요청은 일반적으로 다음과 같습니다.

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

키는 위와 같이 `code`라는 쿼리 문자열 변수에 포함되거나 `x-functions-key` HTTP 헤더에 포함될 수 있습니다. 키 값은 함수에 대해 정의된 모든 function 키 또는 모든 호스트 키일 수 있습니다.

키를 요구하지 않는 익명 요청을 허용할 수 있습니다. 마스터 키를 사용하도록 요구할 수도 있습니다. 바인딩 JSON에서 `authLevel` 속성을 사용하여 기본 권한 수준을 변경합니다. 자세한 내용은 [트리거 - 구성](#trigger---configuration)을 참조하세요.

### <a name="keys-and-webhooks"></a>키 및 webhook

웹후크 인증은 HTTP 트리거의 일부로 웹후크 수신기 구성 요소에서 처리하며 메커니즘은 웹후크 유형에 따라 다릅니다. 그러나 각 메커니즘마다 키를 사용합니다. 기본적으로 "default"라는 함수 키가 사용됩니다. 다른 키를 사용하려면 다음 중 한 가지 방법으로 요청과 함께 키 이름을 보내도록 웹후크 공급자를 구성합니다.

- **쿼리 문자열**: 공급자에서 `clientid` 쿼리 문자열 매개 변수에 키 이름을 전달합니다(예: `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **요청 헤더**: 공급자에서 `x-functions-clientid` 헤더에 키 이름을 전달합니다.

## <a name="trigger---limits"></a>트리거 - 제한

HTTP 요청 길이는 100MB(104,857,600바이트)로 제한되고 URL 길이는 4KB(4,096바이트)로 제한됩니다. 이러한 제한은 런타임의 [Web.config 파일](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)의 `httpRuntime` 요소에 의해 지정됩니다.

HTTP 트리거를 사용하는 함수가 약 2.5분 안에 완료되지 않으면 게이트웨이가 시간 제한을 적용하고 HTTP 502 오류를 반환합니다. 함수는 계속 실행되지만 HTTP 응답은 반환할 수 없습니다. 장기 실행 함수의 경우 비동기 패턴을 따르고 요청 상태를 ping할 수 있는 위치를 반환하는 것이 좋습니다. 함수 실행 시간에 대한 정보는 [크기 조정 및 호스팅 - 소비 계획](functions-scale.md#consumption-plan)을 참조하세요. 

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](functions-host-json.md) 파일에는 HTTP 트리거 동작을 제어하는 설정이 포함됩니다.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>출력

HTTP 요청 발신기(sender)에 응답하려면 HTTP 출력 바인딩을 사용합니다. 이 바인딩에는 HTTP 트리거가 필요하며 트리거 요청과 관련된 응답을 사용자 지정할 수 있습니다. HTTP 출력 바인딩을 제공하지 않으면 HTTP 트리거는 Functions 1.x에서 본문이 비어 있는 HTTP 200 OK 또는 Functions 2.x에서 본문이 비어 있는 HTTP 204 No Content를 반환합니다.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. C# 클래스 라이브러리의 경우 *function.json* 속성에 해당하는 attribute 속성이 없습니다. 

|자산  |설명  |
|---------|---------|
| **type** |`http`로 설정해야 합니다. |
| **direction** | `out`로 설정해야 합니다. |
|**name** | 응답에 대한 함수 코드에 사용되는 변수 이름이거나 반환 값을 사용하는 `$return`입니다. |

## <a name="output---usage"></a>출력 - 사용

HTTP 응답을 보내려면 언어 표준 응답 패턴을 사용합니다. C# 또는 C# 스크립트에서는 함수 반환 형식을 `HttpResponseMessage` 또는 `Task<HttpResponseMessage>`로 만듭니다. C#에서는 반환 값 특성이 필요 없습니다.

예제 응답은 [트리거 예제](#trigger---example) 및 [웹후크 예제](#trigger---webhook-example)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
