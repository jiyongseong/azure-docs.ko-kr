---
title: 지속성 함수의 바인딩 - Azure
description: Azure Functions의 지속성 함수 확장에 트리거 및 바인딩을 사용하는 방법을 설명합니다.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 370e6e2c569aaf6d9289bddccde2174b4dd2ee97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>지속성 함수의 바인딩(Azure Functions)

[지속성 함수](durable-functions-overview.md) 확장은 오케스트레이터 및 작업 함수의 실행을 제어하는 새로운 두 가지 트리거 바인딩을 도입하고 있습니다. 또한 지속성 함수 런타임에 대한 클라이언트 역할을 하는 출력 바인딩도 도입하고 있습니다.

## <a name="orchestration-triggers"></a>오케스트레이션 트리거

오케스트레이션 트리거를 사용하여 지속성 오케스트레이터 함수를 작성할 수 있습니다. 이 트리거는 새 오케스트레이터 함수 인스턴스를 시작하고 작업을 "대기 중인" 기존의 오케스트레이터 함수 인스턴스를 다시 시작할 수 있도록 지원합니다.

Azure Functions에 Visual Studio 도구를 사용하는 경우 오케스트레이션 트리거는 [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET 특성을 사용하여 구성됩니다.

스크립팅 언어(예: Azure Portal)에서 오케스트레이터 함수를 작성하는 경우 오케스트레이션 트리거는 *function.json* 파일의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`은 오케스트레이션의 이름입니다. 이 오케스트레이터 함수의 새 인스턴스를 시작하려고 할 때 클라이언트에서 사용해야 하는 값입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 저장소 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 오케스트레이션 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드** - 단일 디스패처 스레드는 단일 호스트 인스턴스에서 모든 오케스트레이터 함수를 실행하는 데 사용됩니다. 이러한 이유로 오케스트레이터 함수 코드가 효율적이고 모든 I/O를 수행하지 않도록 하는 것이 중요합니다. 또한 이 스레드에서 지속성 함수 관련 작업 종류를 기다리는 경우를 제외하고는 비동기 작업을 수행하지 않도록 하는 것이 중요합니다.
* **포이즌 메시지 처리** - 오케스트레이션 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 오케스트레이션 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table 저장소의 오케스트레이션 기록 테이블에 유지됩니다. 이러한 반환 값은 나중에 설명할 오케스트레이션 클라이언트 바인딩을 통해 쿼리할 수 있습니다.

> [!WARNING]
> 오케스트레이터 함수는 오케스트레이션 트리거 바인딩 이외의 입력 또는 출력 바인딩을 절대로 사용하면 안됩니다. 이렇게 하면 그러한 바인딩에서 단일 스레딩 및 I/O 규칙을 따르지 않을 수 있으므로 지속성 작업 확장에서 문제가 발생할 수 있습니다.

### <a name="trigger-usage"></a>트리거 사용

오케스트레이션 트리거 바인딩은 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - 오케스트레이션 함수는 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)만 매개 변수 형식으로 지원합니다. 함수 시그니처에서 직접적인 역직렬화 입력은 지원되지 않습니다. 코드에서는 오케스트레이터 함수 입력을 가져오기 위해 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) 메서드를 사용해야 합니다. 이러한 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 오케스트레이션 트리거는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.

### <a name="trigger-sample"></a>트리거 샘플

다음은 가장 간단한 "Hello World" 오케스트레이터 함수 예제입니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(함수 v2에만 해당)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript 오케스트레이터는 `return`을 사용해야 합니다. `durable-functions` 라이브러리는 `context.done` 메서드 호출을 처리합니다.

대부분의 오케스트레이터 함수에서 작업 함수를 호출하므로 작업 함수를 호출하는 방법을 보여 주는 "Hello World" 예제가 여기에 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(함수 v2에만 해당)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivityAsync("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>작업 트리거

작업 트리거를 사용하면 오케스트레이터 함수에서 호출하는 함수를 작성할 수 있습니다.

Visual Studio를 사용하는 경우 작업 트리거는 [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET 특성을 사용하여 구성됩니다. 

개발을 위해 Azure Portal을 사용하는 경우 작업 트리거는 *function.json*의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`은 작업의 이름입니다. 오케스트레이터 함수에서 이 작업 함수를 호출하는 데 사용하는 값입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 저장소 계정에 있는 큐를 폴링합니다. 이 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 작업 트리거에 대한 몇 가지 참고 사항입니다.

* **스레딩** - 오케스트레이션 트리거와 달리 작업 트리거에는 스레딩 또는 I/O에 대한 제한이 없습니다. 일반 함수처럼 처리될 수 있습니다.
* **포이즌 메시지 처리** - 작업 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 작업 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table 저장소의 오케스트레이션 기록 테이블에 유지됩니다.

> [!WARNING]
> 작업 함수에 대한 저장소 백 엔드는 구현 세부 정보이며, 사용자 코드는 이러한 저장소 엔터티와 직접 상호 작용하면 안됩니다.

### <a name="trigger-usage"></a>트리거 사용

작업 트리거 바인딩은 오케스트레이션 트리거와 마찬가지로 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - 작업 함수는 본질적으로 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)를 매개 변수 형식으로 사용합니다. 또는 JSON 직렬화 가능 매개 변수 형식으로 선언될 수 있습니다. `DurableActivityContext`를 사용하면 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1)를 호출하여 작업 함수 입력을 가져오고 역직렬화할 수 있습니다.
* **출력** - 작업 함수는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.
* **메타데이터** - 작업 함수는 `string instanceId` 매개 변수에 바인딩하여 부모 오케스트레이션의 인스턴스 ID를 가져올 수 있습니다.

### <a name="trigger-sample"></a>트리거 샘플

다음은 간단한 "Hello World" 작업 함수 예제입니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(함수 v2에만 해당)

```javascript
module.exports = function(context) {
    context.done(null, `Hello ${context.bindings.name}!`);
};
```

`ActivityTriggerAttribute` 바인딩에 대한 기본 매개 변수 형식은 `DurableActivityContext`입니다. 그러나 작업 트리거는 JSON 직렬화 가능 형식(기본 형식 포함)에 대한 직접 바인딩도 지원하므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(함수 v2에만 해당)

```javascript
module.exports = function(context, name) {
    context.done(null, `Hello ${name}!`);
};
```

### <a name="passing-multiple-parameters"></a>여러 매개 변수 전달 

작업 함수에 직접 여러 매개 변수를 전달하는 것은 불가능합니다. 이 경우 개체의 배열에 전달하거나 [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) 개체를 사용하는 것이 좋습니다.

다음 샘플은 [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples)로 추가된 [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples)의 새로운 기능을 사용하는 것입니다.

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university 
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>오케스트레이션 클라이언트

오케스트레이션 클라이언트 바인딩을 사용하면 오케스트레이터 함수와 상호 작용하는 함수를 작성할 수 있습니다. 예를 들어 오케스트레이션 인스턴스에서 다음과 같은 방법으로 작동할 수 있습니다.
* 인스턴스를 시작합니다.
* 해당 상태를 쿼리합니다.
* 인스턴스를 종료합니다.
* 실행하는 동안 이벤트를 보냅니다.

Visual Studio를 사용하는 경우 [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET 특성을 사용하여 오케스트레이션 클라이언트에 바인딩할 수 있습니다.

개발을 위해 스크립팅 언어(예: *.csx* 파일)를 사용하는 경우 오케스트레이션 트리거는 *function.json*의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 저장소 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 오케스트레이터 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 저장소 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 저장소 계정은 대상 오케스트레이터 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 저장소 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 이러한 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

### <a name="client-usage"></a>클라이언트 사용

C# 함수에서는 일반적으로 `DurableOrchestrationClient`에 바인딩하며, 이는 지속성 함수에서 지원하는 모든 클라이언트 API에 대한 모든 액세스 권한을 부여합니다. 클라이언트 개체에 대한 API는 다음과 같습니다.

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

또는 `T`가 [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) 또는 `JObject`인 `IAsyncCollector<T>`에 바인딩할 수 있습니다.

이러한 작업에 대한 자세한 내용은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API 설명서를 참조하세요.

### <a name="client-sample-visual-studio-development"></a>클라이언트 샘플(Visual Studio 개발)

다음은 "HelloWorld" 오케스트레이션을 시작하는 큐 트리거 함수 예제입니다.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>클라이언트 샘플(Visual Studio 사용 안 함)

개발을 위해 Visual Studio를 사용하지 않는 경우 다음 *function.json* 파일을 만들 수 있습니다. 이 예제에서는 지속성 함수 오케스트레이션 클라이언트 바인딩을 사용하는 큐 트리거 함수를 구성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

다음은 새 오케스트레이터 함수 인스턴스를 시작하는 언어 관련 샘플입니다.

#### <a name="c-sample"></a>C# 샘플

다음 샘플에서는 지속성 오케스트레이션 클라이언트 바인딩을 사용하여 C# 스크립트 함수에서 새 함수 인스턴스를 시작하는 방법을 보여 줍니다.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript 샘플

다음 샘플에서는 지속성 오케스트레이션 클라이언트 바인딩을 사용하여 JavaScript 함수에서 새 함수 인스턴스를 시작하는 방법을 보여줍니다.

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

인스턴스 시작에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [검사점 설정 및 재생 동작](durable-functions-checkpointing-and-replay.md)

