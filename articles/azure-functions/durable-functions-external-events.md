---
title: 지속성 함수의 외부 이벤트 처리 - Azure
description: Azure Functions의 지속성 함수 확장에서 외부 이벤트를 처리하는 방법을 알아봅니다.
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
ms.openlocfilehash: 77087f04ea641c24a92edd2091432cbcb4329ecd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>지속성 함수의 외부 이벤트 처리(Azure Functions)

오케스트레이터 함수에는 외부 이벤트를 대기 및 수신 대기하는 기능이 있습니다. [지속성 함수](durable-functions-overview.md)의 이 기능은 종종 인간 상호 작용 또는 다른 외부 트리거를 처리하는 데 유용합니다.

## <a name="wait-for-events"></a>이벤트 대기

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 메서드를 사용하면 오케스트레이터 함수에서 비동기적으로 대기하고 외부 이벤트를 수신 대기할 수 있습니다. 수신 오케스트레이터는 이벤트의 *이름*과 수신할 것으로 예상되는 *데이터의 셰이프*를 선언합니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

앞의 예제에서는 특정 단일 이벤트를 수신 대기하고, 이 이벤트가 수신되면 작업을 수행합니다.

수신 가능한 세 가지 이벤트 알림 중 하나를 기다리는 다음 예제와 같이 여러 이벤트를 동시에 수신 대기할 수 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

앞의 예제에서는 여러 이벤트 중 *하나*를 수신 대기합니다. *모든* 이벤트를 기다릴 수도 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivityAsync("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_)는 일부 입력을 무기한으로 기다립니다.  함수 앱은 기다리는 동안 안전하게 언로드될 수 있습니다. 이 오케스트레이션 인스턴스에 이벤트가 도착하면 인스턴스가 자동으로 활성화하고 해당 이벤트를 즉시 처리합니다.

> [!NOTE]
> 함수 앱이 소비 계획을 사용하는 경우 지연 시간에 관계 없이 오케스트레이터 함수에서 `WaitForExternalEvent`의 작업을 기다리는 동안에 대한 요금은 청구되지 않습니다.

.NET에서 이벤트 페이로드를 필요한 `T` 형식으로 변환할 수 없으면 예외가 throw됩니다.

## <a name="send-events"></a>이벤트 보내기

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 메서드는 `WaitForExternalEvent`에서 기다리는 이벤트를 보냅니다.  `RaiseEventAsync` 메서드는 *eventName* 및 *eventData*를 매개 변수로 사용합니다. 이벤트 데이터는 JSON 직렬화 가능해야 합니다.

다음은 오케스트레이터 함수 인스턴스에 "승인" 이벤트를 보내는 큐 트리거 함수 예제입니다. 오케스트레이션 인스턴스 ID는 큐 메시지 본문에서 가져옵니다.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

`RaiseEventAsync`는 내부적으로 대기 중인 오케스트레이터 함수에서 선택된 메시지를 큐에 넣습니다.

> [!WARNING]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없거나 인스턴스에서 지정된 *이벤트 이름*을 기다리지 않는 경우 해당 이벤트 메시지는 버려집니다. 이 동작에 대한 자세한 내용은 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/29)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [영구 오케스트레이션 설정](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [외부 이벤트를 기다리는 샘플 실행](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [인간 상호 작용을 기다리는 샘플 실행](durable-functions-phone-verification.md)

