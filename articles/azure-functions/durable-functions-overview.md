---
title: 지속성 함수 개요 - Azure
description: Azure Functions의 지속성 함수 확장을 소개합니다.
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
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: d253562e0ecb0d53739a4cdc5f9747e33d7e1171
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="durable-functions-overview"></a>지속성 함수 개요

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](functions-overview.md) 및 [Azure WebJobs](../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다.

이 확장을 통해 *오케스트레이터 함수*라는 새 유형의 함수에서 상태 저장 워크플로를 정의할 수 있습니다. 다음은 오케스트레이터 함수의 이점 중 일부입니다.

* 코드에서 워크플로를 정의합니다. JSON 스키마 또는 디자이너가 필요하지 않습니다.
* 다른 함수를 동기적 및 비동기적으로 호출할 수 있습니다. 호출된 함수의 출력은 지역 변수에 저장할 수 있습니다.
* 함수에서 기다릴 때마다 자동으로 진행 상황 검사점을 설정합니다. 프로세스가 재활용되거나 VM이 다시 부팅되더라도 로컬 상태가 손실되지 않습니다.

> [!NOTE]
> 지속성 함수는 모든 응용 프로그램에 적합하지는 않는 Azure Functions의 고급 확장입니다. 이 문서의 나머지 부분에서는 [Azure Functions](functions-overview.md) 개념과 서버를 사용하지 않는 응용 프로그램 개발과 관련된 문제에 대해 잘 알고 있다고 가정합니다.

지속성 함수에 대한 기본 사용 사례는 서버를 사용하지 않는 응용 프로그램에서 복잡한 상태 저장 조정 문제를 단순화하는 것입니다. 다음 섹션에서는 지속성 함수를 활용할 수 있는 몇 가지 일반적인 응용 프로그램 패턴에 대해 설명합니다.

## <a name="pattern-1-function-chaining"></a>패턴 #1: 함수 체이닝

*함수 체이닝*은 특정 순서로 일련의 함수를 실행하는 패턴을 나타냅니다. 종종 한 함수의 출력을 다른 함수의 입력에 적용해야 합니다.

![함수 체이닝 다이어그램](media/durable-functions-overview/function-chaining.png)

지속성 함수를 사용하면 코드에서 이 패턴을 간결하게 구현할 수 있습니다.

#### <a name="c"></a>C#

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const x = yield ctx.df.callActivityAsync("F1");
    const y = yield ctx.df.callActivityAsync("F2", x);
    const z = yield ctx.df.callActivityAsync("F3", y);
    return yield ctx.df.callActivityAsync("F4", z);
});
```

"F1", "F2", "F3" 및 "F4" 값은 함수 앱에 있는 다른 함수의 이름입니다. 제어 흐름은 일반적인 명령적 코딩 구조를 사용하여 구현됩니다. 즉 코드는 하향식으로 실행되며 조건부 및 반복과 같은 기존 언어 제어 흐름 의미 체계를 포함할 수 있습니다.  오류 처리 논리는 try/catch/finally 블록에 포함될 수 있습니다.

`ctx` 매개 변수([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html))는 이름에 따라 다른 함수를 호출하고, 매개 변수를 전달하며, 함수 출력을 반환하는 메서드를 제공합니다. 코드에서 `await`를 호출할 때마다 지속성 함수 프레임워크는 현재 함수 인스턴스의 진행 상황 *검사점을 설정*합니다. 프로세스 또는 VM이 실행 중간에 재활용되는 경우 함수 인스턴스가 이전 `await` 호출에서 다시 시작됩니다. 이 다시 시작 동작에 대해서는 나중에 자세히 설명합니다.

## <a name="pattern-2-fan-outfan-in"></a>패턴 #2: 팬아웃/팬인

*팬아웃/팬인*은 여러 함수를 병렬로 실행한 다음 모든 함수가 완료되기를 기다리는 패턴을 나타냅니다.  일부 집계 작업은 종종 함수에서 반환된 결과에 대해 수행됩니다.

![팬아웃/팬인 다이어그램](media/durable-functions-overview/fan-out-fan-in.png)

일반 함수를 사용하면 함수에서 여러 메시지를 큐에 보내도록 하여 팬아웃/팬인을 수행할 수 있습니다. 그러나 다시 팬인하는 것은 훨씬 더 어렵습니다. 큐 트리거 함수가 종료되고 함수 출력을 저장하는 시기를 추적하기 위한 코드를 작성해야 합니다. 지속성 함수 확장에서는 비교적 간단한 코드로 이 패턴을 처리합니다.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield ctx.df.callActivityAsync("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(ctx.df.callActivityAsync("F2", workBatch[i]));
    }

    yield ctx.df.task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield ctx.df.callActivityAsync("F3", sum);
});
```

팬아웃 작업은 `F2` 함수의 여러 인스턴스에 배포되며 동적 작업 목록을 사용하여 작업을 추적합니다. `Task.WhenAll` .NET API는 호출된 모든 함수가 완료될 때까지 기다리기 위해 호출됩니다. 그런 다음 동적 작업 목록에서 `F2` 함수 출력이 집계되고 `F3` 함수로 전달됩니다.

`Task.WhenAll`에 대한 `await` 호출에서 발생하는 자동 검사점 설정은 중간에 충돌이나 다시 부팅이 완료되는 경우 이미 완료된 작업을 다시 시작할 필요가 없도록 합니다.

## <a name="pattern-3-async-http-apis"></a>패턴 #3: 비동기 HTTP API

세 번째 패턴은 외부 클라이언트에서 장기 실행 작업 상태를 조정하는 문제에 관한 것입니다. 이 패턴을 구현하는 일반적인 방법은 HTTP 호출로 트리거된 장기 실행 작업을 수행한 다음, 작업 완료 시점을 확인하기 위해 폴링할 수 있는 상태 끝점으로 클라이언트를 리디렉션하는 것입니다.

![HTTP API 다이어그램](media/durable-functions-overview/async-http-api.png)

지속성 함수는 장기 실행 함수 실행과 상호 작용하도록 작성하는 코드를 단순화하는 기본 제공 API를 제공합니다. 이 [샘플](durable-functions-install.md)에서는 새 오케스트레이터 함수 인스턴스를 시작하는 데 사용할 수 있는 간단한 REST 명령을 보여 줍니다. 인스턴스가 시작되면 확장에서 오케스트레이터 함수 상태를 쿼리하는 웹후크 HTTP API를 노출합니다. 다음 예제에서는 오케스트레이터를 시작하고 해당 상태를 쿼리하는 REST 명령을 보여 줍니다. 명확성을 위해 일부 세부 정보가 예제에서 생략되었습니다.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

상태는 지속성 함수 런타임에서 관리되므로 사용자는 자신의 상태 추적 메커니즘을 구현할 필요가 없습니다.

지속성 함수 확장에는 장기 실행 오케스트레이션을 관리하기 위한 기본 제공 웹후크가 있지만, 사용자 고유의 함수 트리거(예: HTTP, 큐 또는 Event Hub) 및 `orchestrationClient` 바인딩을 사용하여 이 패턴을 직접 구현할 수 있습니다. 예를 들어 큐 메시지를 사용하여 종료를 트리거할 수 있습니다.  또는 생성된 키를 인증에 사용하는 기본 제공 웹후크 대신 Azure Active Directory 인증 정책으로 보호된 HTTP 트리거를 사용할 수 있습니다. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 매개 변수는 지속성 함수 확장의 일부인 `orchestrationClient` 출력 바인딩의 값입니다. 새 오케스트레이터 또는 기존 오케스트레이터 함수 인스턴스에 대한 시작, 이벤트 보내기, 종료 및 쿼리를 수행하는 메서드를 제공합니다. 이전 예제에서 HTTP 트리거 함수는 들어오는 URL에서 `functionName` 값을 가져와서 해당 값을 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)에 전달합니다. 그런 다음 이 바인딩 API는 `Location` 헤더 및 나중에 시작된 인스턴스의 상태를 찾거나 종료하는 데 사용할 수 있는 인스턴스에 대한 추가 정보가 포함된 응답을 반환합니다.

## <a name="pattern-4-monitoring"></a>패턴 #4: 모니터링

모니터링 패턴은 워크플로의 유연한 되풀이(예: 특정 조건이 충족될 때까지 폴링) 프로세스를 말합니다. 일반 타이머 트리거는 정기적인 정리 작업과 같은 간단한 시나리오를 처리할 수 있지만 간격은 정적이며 인스턴스 수명을 관리하기가 복잡해집니다. 지속성 함수를 사용하면 유연한 되풀이 간격, 작업 수명 관리 및 단일 오케스트레이션에서 여러 모니터링 프로세스 만들기가 가능합니다.

예를 들어 이전의 비동기 HTTP API 시나리오를 뒤집을 수 있습니다. 외부 클라이언트의 엔드포인트를 노출하여 장기 실행 작업을 모니터링하는 대신, 장기 실행 모니터는 외부 엔드포인트를 소비하여 일부 상태 변경을 기다립니다.

![모니터 다이어그램](media/durable-functions-overview/monitor.png)

지속성 함수를 사용하면, 임의의 엔드포인트를 관찰하는 여러 모니터를 몇 줄의 코드로 작성할 수 있습니다. 모니터는 일정 조건이 충족되면 실행을 끝내거나 [DurableOrchestrationClient](durable-functions-instance-management.md)에 의해 종료될 수 있으며 대기 간격은 일부 조건(예: 지수 백오프)에 따라 변경될 수 있습니다. 다음 코드는 기본 모니터를 구현합니다.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```js
const df = require("durable-functions");
const df = require("moment");

module.exports = df(function*(ctx) {
    const jobId = ctx.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(ctx.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield ctx.df.callActivityAsync("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield ctx.df.callActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(ctx.df.currentUtcDateTime).add(pollingInterval, 's');
        yield ctx.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

요청을 받으면 해당 작업 ID에 대해 새 오케스트레이션 인스턴스가 만들어집니다. 조건이 충족되고 루프가 종료될 때까지 인스턴스는 상태를 폴링합니다. 지속성 타이머는 폴링 간격을 제어하는 데 사용됩니다. 그런 다음, 추가로 작업을 수행하거나 오케스트레이션을 종료할 수 있습니다. `ctx.CurrentUtcDateTime`이 `expiryTime`을 초과하면 모니터가 종료됩니다.

## <a name="pattern-5-human-interaction"></a>패턴 #5: 인간 상호 작용

많은 프로세스에서 일종의 인간 상호 작용을 수반합니다. 자동화된 프로세스에서 인간과 관련된 까다로운 문제는 사람들의 가용성과 응답성이 항상 클라우드 서비스만큼 높은 것은 아니라는 것입니다. 자동화된 프로세스는 이 문제를 허용해야 하며, 종종 시간 제한 및 보정 논리를 사용하여 이를 수행합니다.

인간 상호 작용을 포함하는 비즈니스 프로세스의 한 예가 승인 프로세스입니다. 예를 들어 특정 금액을 초과하는 경비 보고서에 대해 관리자의 승인이 필요할 수도 있습니다. 관리자가 72시간 이내에 승인하지 않으면(아마도 휴가 중인 경우) 에스컬레이션 프로세스가 시작되어 다른 사람(아마도 관리자의 관리자)으로부터 승인을 얻습니다.

![인간 상호 작용 다이어그램](media/durable-functions-overview/approval.png)

이 패턴은 오케스트레이터 함수를 사용하여 구현할 수 있습니다. 오케스트레이터는 [지속성 타이머](durable-functions-timers.md)를 사용하여 승인을 요청하고 시간이 초과된 경우 에스컬레이션합니다. 이 경우 [외부 이벤트](durable-functions-external-events.md)를 기다릴 것입니다. 이 이벤트는 어떤 인간 상호 작용으로 생성된 알림입니다.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

```js
const df = require("durable-functions");
const moment = require('moment');

module.exports = df(function*(ctx) {
    yield ctx.df.callActivityAsync("RequestApproval");

    const dueTime = moment.utc(ctx.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = ctx.df.createTimer(dueTime.toDate());

    const approvalEvent = ctx.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield ctx.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield ctx.df.callActivityAsync("ProcessApproval", approvalEvent.result);
    } else {
        yield ctx.df.callActivityAsync("Escalate");
    }
});
```

지속성 타이머는 `ctx.CreateTimer`를 호출하여 만듭니다. `ctx.WaitForExternalEvent`에서 알림을 받습니다. 그리고 에스컬레이션(시간 제한이 먼저 발생) 또는 프로세스 승인(시간 제한 전에 승인 수신)을 결정하기 위해 `Task.WhenAny`가 호출됩니다.

외부 클라이언트는 [기본 제공 HTTP API](durable-functions-http-api.md#raise-event)를 사용하거나 다른 함수의 [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API를 사용하여 대기 중인 오케스트레이터 함수에 이벤트 알림을 배달할 수 있습니다.

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

## <a name="the-technology"></a>기술

내부적으로 지속성 함수 확장은 지속성 작업 오케스트레이션을 빌드하기 위한 GitHub의 오픈 소스 라이브러리인 [지속성 작업 프레임워크](https://github.com/Azure/durabletask)를 기반으로 합니다. Azure Functions가 Azure WebJobs의 서버를 사용하지 않는 진화인 것과 같이 지속성 함수는 지속성 작업 프레임워크의 서버를 사용하지 않는 진화입니다. 지속성 작업 프레임워크는 Microsoft 내/외부에서 많이 사용되며 중요 업무용 프로세스를 자동화합니다. 이는 서버를 사용하지 않는 Azure Functions 환경에 적합합니다.

### <a name="event-sourcing-checkpointing-and-replay"></a>이벤트 소싱, 검사점 설정 및 재생

오케스트레이터 함수는 [이벤트 소싱](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)으로 알려진 클라우드 디자인 패턴을 사용하여 실행 상태를 안정적으로 유지합니다. 지속성 확장에서는 오케스트레이션의 *현재* 상태를 직접 저장하는 대신, 함수 오케스트레이션에서 수행한 *일련의 전체 작업*을 기록하기 위해 추가 전용 저장소를 사용합니다. 이렇게 하면 전체 런타임 상태를 "덤프"하는 것에 비해 성능, 확장성 및 응답성 향상을 포함한 많은 이점이 있습니다. 다른 이점으로 트랜잭션 데이터에 대한 최종 일관성 제공과 전체 감사 내역 및 기록 유지가 있습니다. 감사 내역 자체는 신뢰할 수 있는 보정 작업을 가능하게 합니다.

이 확장에서 사용하는 이벤트 소싱 사용은 투명합니다. 내부적으로 오케스트레이터 함수의 `await` 연산자는 오케스트레이터 스레드의 제어를 지속성 작업 프레임워크 디스패처에 다시 생성합니다. 그런 다음 디스패처는 오케스트레이터에서 예약한 새 작업(예: 하나 이상의 자식 함수 호출 또는 지속성 타이머 예약)을 저장소에 커밋합니다. 이 투명한 커밋 작업은 오케스트레이션 인스턴스의 *실행 기록*에 추가됩니다. 기록은 저장소 테이블에 저장됩니다. 그런 다음 커밋 작업은 실제 작업을 예약하는 큐에 메시지를 추가합니다. 이 시점에서 오케스트레이터 함수는 메모리에서 언로드할 수 있습니다. Azure Functions 소비 계획을 사용하는 경우 이에 대한 청구가 중지됩니다.  수행할 작업이 더 많이 있으면 함수가 다시 시작되고 해당 상태도 다시 구성됩니다.

오케스트레이션 함수에서 더 많은 작업(예: 응답 메시지 수신 또는 영구 타이머 만료)을 수행해야 하는 경우, 오케스트레이터가 다시 활성화하고 로컬 상태를 다시 작성하기 위해 전체 함수를 처음부터 다시 실행합니다. 이 재생 중에 코드에서 함수를 호출하려고 하면(또는 다른 비동기 작업을 수행하는 경우) 지속성 작업 프레임워크는 현재 오케스트레이션의 *실행 기록*을 참조합니다. 작업 함수가 이미 실행되었음을 알고 있고 일부 결과를 생성한 경우 해당 함수의 결과를 재생하고 오케스트레이터 코드는 계속 실행됩니다. 함수 코드가 끝나거나 새 비동기 작업을 예약한 지점에 도달할 때까지 이 작업은 계속됩니다.

### <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

재생 동작은 오케스트레이터 함수에 작성될 수 있는 코드 형식에 대한 제약 조건을 만듭니다. 예를 들어 오케스트레이터 코드는 여러 번 재생되고 매번 동일한 결과를 만들어야 하므로 결정적이어야 합니다. 제약 조건의 전체 목록은 **검사점 설정 및 다시 시작** 문서의 [오케스트레이터 코드 제약 조건](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) 섹션에서 찾을 수 있습니다.

## <a name="language-support"></a>언어 지원

현재 C#(Functions v1 및 v2) 및 JavaScript(Functions v2만)가 지속성 함수에 대해 지원되는 유일한 언어입니다. 여기에는 오케스트레이터 함수 및 작업 함수가 포함됩니다. 나중에 Azure Functions에서 지원하는 모든 언어에 대한 지원을 추가할 예정입니다. Azure Functions [GitHub 리포지토리 문제 목록](https://github.com/Azure/azure-functions-durable-extension/issues)을 참조하여 추가 언어 지원 작업의 최신 상태를 확인하세요.

## <a name="monitoring-and-diagnostics"></a>모니터링 및 진단

지속성 함수 확장은 Application Insights 계측 키로 함수 앱을 구성할 때 [Application Insights](functions-monitoring.md)에 구조적 추적 데이터를 자동으로 내보냅니다. 이 추적 데이터는 오케스트레이션의 동작 및 진행 상황을 모니터링하는 데 사용할 수 있습니다.

다음은 [Application Insights 분석](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)을 사용하여 Application Insights 포털에서 지속성 함수 추적 이벤트를 보여 주는 예제입니다.

![Application Insights 쿼리 결과](media/durable-functions-overview/app-insights-1.png)

각 로그 항목의 `customDimensions` 필드에 압축된 유용한 구조적 데이터가 많이 있습니다. 다음은 이러한 항목 중 하나에 대해 완전한 확장 예제입니다.

![Application Insights 쿼리의 customDimensions 필드](media/durable-functions-overview/app-insights-2.png)

지속성 작업 프레임워크 디스패처의 재생 동작으로 인해 재생된 작업에 대해 중복된 로그 항목이 표시될 수 있습니다. 이렇게 하면 핵심 엔진의 재생 동작을 이해하는 데 유용할 수 있습니다. [진단](durable-functions-diagnostics.md) 문서에서는 재생 로그를 필터링하여 "실시간" 로그만 볼 수 있는 샘플 쿼리를 보여 줍니다.

## <a name="storage-and-scalability"></a>저장소 및 확장성

지속성 함수 확장은 Azure Storage 큐, 테이블 및 Blob을 사용하여 실행 기록 상태 및 트리거 함수 실행을 유지합니다. 함수 앱에 대한 기본 저장소 계정을 사용하거나 별도의 저장소 계정을 구성할 수 있습니다. 저장소 처리량 제한으로 인해 별도의 계정이 필요할 수 있습니다. 작성한 오케스트레이터 코드는 이러한 저장소 계정의 엔터티와 상호 작용할 필요가 없고 상호 작용하지도 않습니다. 엔터티는 구현 세부 정보로서 지속성 작업 프레임워크에서 직접 관리합니다.

오케스트레이터 함수는 작업 함수를 예약하고 내부 큐 메시지를 통해 이러한 함수의 응답을 받습니다. 함수 앱이 Azure Functions 소비 계획에서 실행되면 이러한 큐는 [Azure Functions 크기 조정 컨트롤러](functions-scale.md#how-the-consumption-plan-works)에서 모니터링되고 필요에 따라 새 계산 인스턴스가 추가됩니다. 여러 VM으로 확장되는 경우 하나의 VM에서 오케스트레이터 함수가 실행될 수 있는 반면, 호출되는 작업 함수는 별도의 여러 VM에서 실행됩니다. 지속성 함수의 크기 조정 동작에 대한 자세한 내용은 [성능 및 크기 조정](durable-functions-perf-and-scale.md)에서 찾을 수 있습니다.

테이블 저장소는 오케스트레이터 계정에 대한 실행 기록을 저장하는 데 사용됩니다. 인스턴스가 특정 VM에서 리하이드레이션할 때마다 테이블 저장소에서 실행 기록을 가져와서 해당 로컬 상태를 다시 작성할 수 있습니다. 테이블 저장소에서 기록을 사용할 수 있는 편리한 작업 중 하나는 [Microsoft Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)와 같은 도구를 사용하여 오케스트레이션의 기록을 확인할 수 있다는 것입니다.

![Microsoft Azure Storage 탐색기 스크린샷](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> 테이블 저장소의 실행 기록을 확인하는 것이 쉽고 편리하지만 이 테이블에 대한 종속성은 사용하지 마세요. 지속성 함수 확장이 진화함에 따라 변경될 수 있습니다.

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

알려진 모든 문제는 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues) 목록에서 추적해야 합니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 함수 설명서 참조 계속](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [지속성 함수 확장 및 샘플 설치](durable-functions-install.md)

