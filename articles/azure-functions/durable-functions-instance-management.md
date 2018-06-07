---
title: 지속성 함수의 인스턴스 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 인스턴스를 관리하는 방법을 알아봅니다.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0e573b4973ea30b990043b54c5cdcf0805135a40
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>지속성 함수의 인스턴스 관리(Azure Functions)

[지속성 함수](durable-functions-overview.md) 오케스트레이션 인스턴스는 알림 이벤트를 시작, 종료, 쿼리 및 전송할 수 있습니다. 모든 인스턴스 관리는 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md)을 사용하여 수행됩니다. 이 문서에서는 각 인스턴스 관리 작업에 대해 자세히 설명합니다.

## <a name="starting-instances"></a>인스턴스 시작

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)의 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 메서드는 오케스트레이터 함수의 새 인스턴스를 시작합니다. 이 클래스의 인스턴스는 `orchestrationClient` 바인딩을 사용하여 얻을 수 있습니다. 내부적으로 이 메서드는 메시지를 제어 큐에 넣고 `orchestrationTrigger` 트리거 바인딩을 사용하는 지정된 이름의 함수 시작을 트리거합니다. 

오케스트레이션 프로세스를 시작할 때 작업을 완료합니다. 오케스트레이션 프로세스는 30초 이내에 시작해야 합니다. 더 오래 걸리는 경우 `TimeoutException`이 throw됩니다. 

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)에 대한 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **Input**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 지정하지 않으면 임의의 인스턴스 ID가 생성됩니다.

다음은 간단한 C# 예제입니다.

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

비.NET 언어의 경우 함수 출력 바인딩을 사용하여 새 인스턴스를 시작할 수도 있습니다. 이 경우 위의 세 매개 변수를 필드로 사용하는 JSON 직렬화 가능 개체를 사용할 수 있습니다. 예를 들어 다음 JavaScript 함수를 살펴보세요.

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> 인스턴스 ID에 대해 임의의 식별자를 사용하는 것이 좋습니다. 이렇게 하면 여러 VM에서 오케스트레이터 함수를 크기 조정할 때 균등한 부하 분산을 보장하는 데 도움이 됩니다. D가 외부 원본에서 제공되어야 하거나 [단일 항목 오케스트레이터](durable-functions-singletons.md) 패턴을 구현하는 경우에는 임의가 아닌 인스턴스 ID를 사용하는 것이 좋습니다.

## <a name="querying-instances"></a>인스턴스 쿼리

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [ GetStatusAsync ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 메서드는 오케스트레이션 인스턴스의 상태를 쿼리합니다. `instanceId`(필수), `showHistory`(선택 사항) 및 `showHistoryOutput`(선택 사항)을 매개 변수로 사용합니다. `showHistory`를 `true`로 설정한 경우 응답에는 실행 기록이 포함됩니다. `showHistoryOutput`을 `true`로 설정한 경우 실행 기록에는 작업 출력이 포함됩니다. 메서드는 다음과 같은 속성이 있는 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **Input**: JSON 값의 함수 입력입니다.
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다. 
* **Output**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오케스트레이터 함수가 실패하면 이 속성에 오류 세부 정보가 포함됩니다. 오케스트레이터 함수가 종료되면 이 속성에 제공된 종료 이유가 포함됩니다(있는 경우).
* **RuntimeStatus**: 다음 값 중 하나입니다.
    * **실행 중**: 인스턴스가 실행되기 시작했습니다.
    * **완료됨**: 인스턴스가 정상적으로 완료되었습니다.
    * **ContinuedAsNew(새 기록으로 계속됨)**: 인스턴스가 새 기록으로 다시 시작되었습니다. 일시적인 상태입니다.
    * **실패**: 인스턴스가 오류로 인해 실패했습니다.
    * **종료됨**: 인스턴스가 갑자기 종료되었습니다.
* **기록**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.
    
인스턴스가 존재하지 않거나 아직 실행을 시작하지 않은 경우 이 메서드는 `null`을 반환합니다.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

## <a name="terminating-instances"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스는 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 메서드를 사용하여 종료할 수 있습니다. 두 가지 매개 변수는 `instanceId` 및 `reason` 문자열이며, 로그 및 인스턴스 상태에 기록됩니다. 종료된 인스턴스는 다음 `await` 지점에 도달하는 즉시 실행을 중지하거나 이미 `await`에 있는 경우 즉시 종료됩니다. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 현재 인스턴스 종료는 전파되지 않았습니다. 활동 함수 및 하위 오케스트레이션은 호출된 오케스트레이션 인스턴스가 종료되었는지 여부에 관계 없이 완료될 때까지 실행됩니다.

## <a name="sending-events-to-instances"></a>인스턴스로 이벤트 보내기

이벤트 알림은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스의 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 메서드를 사용하여 실행 중인 인스턴스로 보낼 수 있습니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_)에 대한 호출을 기다리는 인스턴스입니다. 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)에 대한 매개 변수는 다음과 같습니다.

* **InstanceId**: 인스턴스의 고유 ID입니다.
* **EventName**: 보낼 이벤트의 이름입니다.
* **EventData**: 인스턴스에 보낼 JSON 직렬화 가능 페이로드입니다.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> 지정된 *인스턴스 ID*가 있는 오케스트레이션 인스턴스가 없거나 인스턴스에서 지정된 *이벤트 이름*을 기다리지 않는 경우 해당 이벤트 메시지는 버려집니다. 이 동작에 대한 자세한 내용은 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/29)를 참조하세요.

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스는 오케스트레이션 인스턴스에서 실제 출력을 동기적으로 가져오는 데 사용할 수 있는 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API를 노출합니다. 메서드가 설정되지 않은 경우 `timeout`에서 10초를 기본값으로 사용하고 `retryInterval`에서 1초를 기본값으로 사용합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

2초 제한 시간 및 0.5초 다시 시도 간격을 사용하여 다음 행에서 함수를 호출할 수 있습니다.

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

1. 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료됩니다. 응답은 동기적으로 배달된 실제 오케스트레이션 인스턴스 출력입니다.

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료될 수 없습니다. 응답은 **HTTP API URL 검색**에 설명된 기본값입니다.

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 앞의 예제는 Azure Functions 2.0 호스트에 대한 것입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리에 HTTP API 사용](durable-functions-http-api.md)
