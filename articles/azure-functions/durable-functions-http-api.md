---
title: 지속성 함수의 HTTP API - Azure
description: Azure Functions의 지속성 함수 확장에서 HTTP API를 구현하는 방법을 알아봅니다.
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
ms.openlocfilehash: aa5c46a4d0ca55339e8f26a3e577d03bf4b504b2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "32309983"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>지속성 함수의 HTTP API(Azure Functions)

지속성 작업 확장은 다음 작업을 수행하는 데 사용할 수 있는 HTTP API 집합을 공개합니다.

* 오케스트레이션 인스턴스의 상태를 가져옵니다.
* 대기 중인 오케스트레이션 인스턴스에 이벤트를 보냅니다.
* 실행 중인 오케스트레이션 인스턴스를 종료합니다.


이러한 HTTP API 각각은 지속성 작업 확장에서 직접 처리하는 웹후크 작업입니다. 웹후크 작업은 함수 앱의 모든 함수에 특정되지 않습니다.

> [!NOTE]
> 이러한 작업은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스에서 인스턴스 관리 API를 사용하여 직접 호출할 수도 있습니다. 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)를 참조하세요.

## <a name="http-api-url-discovery"></a>HTTP API URL 검색

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스는 지원되는 모든 작업에 대한 링크가 포함된 HTTP 응답 페이로드를 생성하는 데 사용할 수 있는 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API를 공개합니다. 다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

이 예제 함수에서는 다음 JSON 응답 데이터를 생성합니다. 모든 필드의 데이터 형식은 `string`입니다.

| 필드             |설명                           |
|-------------------|--------------------------------------|
| id                |오케스트레이션 인스턴스의 ID입니다. |
| statusQueryGetUri |오케스트레이션 인스턴스의 상태 URL입니다. |
| sendEventPostUri  |오케스트레이션 인스턴스의 "이벤트 발생" URL입니다. |
| terminatePostUri  |오케스트레이션 인스턴스의 "종료" URL입니다. |

다음은 응답 예제입니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 위의 예제는 Azure Functions 2.0 호스트에 대한 것입니다.

## <a name="async-operation-tracking"></a>비동기 작업 추적

앞에서 언급한 HTTP 응답은 지속성 함수를 사용하여 장기 실행 HTTP 비동기 API를 구현하는 데 도움이 되도록 설계되었습니다. 이는 때때로 *폴링 소비자 패턴*이라고도 합니다. 클라이언트/서버 흐름은 다음과 같이 작동합니다.

1. 클라이언트에서 장기 실행 프로세스(예: 오케스트레이터 함수)를 시작하기 위해 HTTP 요청을 발급합니다.
2. 대상 HTTP 트리거에서 `statusQueryGetUri` 값을 사용하여 `Location` 헤더가 있는 HTTP 202 응답을 반환합니다.
3. 클라이언트에서 `Location` 헤더의 URL을 폴링합니다. `Location` 헤더가 있는 HTTP 202 응답이 계속 표시됩니다.
4. 인스턴스가 완료되거나 실패하면 `Location` 헤더의 끝점에서 HTTP 200을 반환합니다.

이 프로토콜을 사용하면 HTTP 끝점 폴링 및 `Location` 헤더 추적을 지원하는 외부 클라이언트 또는 서비스에서 장기 실행 프로세스를 조정할 수 있습니다. 기본 조각은 지속성 함수 HTTP API에 이미 작성되어 있습니다.

> [!NOTE]
> 기본적으로 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)에서 제공하는 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 이 기능을 사용하면 Logic Apps 워크플로의 일부로 장기 실행 지속성 함수를 포함할 수 있습니다. Logic Apps의 비동기 HTTP 패턴 지원에 대한 자세한 내용은 [Azure Logic Apps 워크플로 작업 및 트리거 설명서](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)에서 찾을 수 있습니다.

## <a name="http-api-reference"></a>HTTP API 참조

확장에서 구현된 모든 HTTP API에서 사용하는 매개 변수는 다음과 같습니다. 모든 매개 변수의 데이터 형식은 `string`입니다.

| 매개 변수  | 매개 변수 형식  | 설명 |
|------------|-----------------|-------------|
| instanceId | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| taskHub    | 쿼리 문자열    | [작업 허브](durable-functions-task-hubs.md)의 이름입니다. 지정하지 않으면 현재 함수 앱의 작업 허브 이름이 사용됩니다. |
| connection | 쿼리 문자열    | 저장소 계정에 대한 연결 문자열의 **이름**입니다. 지정하지 않으면 함수 앱에 대한 기본 연결 문자열이 사용됩니다. |
| systemKey  | 쿼리 문자열    | API를 호출하는 데 필요한 권한 부여 키입니다. |
| showHistory| 쿼리 문자열    | 선택적 매개 변수. `true`로 설정하면 오케스트레이션 실행 기록이 응답 페이로드에 포함됩니다.| 
| showHistoryOutput| 쿼리 문자열    | 선택적 매개 변수. `true`로 설정하면 작업 출력이 오케스트레이션 실행 기록에 포함됩니다.| 

`systemKey`는 Azure Functions 호스트에서 자동 생성된 권한 부여 키입니다. 특히 지속성 작업 확장 API에 대한 액세스 권한을 부여하고 [다른 권한 부여 키](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)와 동일한 방식으로 관리할 수 있습니다. `systemKey` 값을 검색하는 가장 간단한 방법은 앞에서 언급한 `CreateCheckStatusResponse` API를 사용하는 것입니다.

다음 몇 가지 섹션에서는 확장에서 지원되는 특정 HTTP API에 대해 설명하고 이 API를 사용하는 방법에 대한 예제를 제공합니다.

### <a name="get-instance-status"></a>인스턴스 상태 가져오기

지정된 오케스트레이션 인스턴스의 상태를 가져옵니다.

#### <a name="request"></a>요청

Functions 1.0의 경우 요청 형식은 다음과 같습니다.

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 형식에는 모두 동일한 매개 변수가 있지만 약간 다른 URL 접두사가 있습니다.

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 200(OK)**: 지정된 인스턴스가 완료된 상태입니다.
* **HTTP 202(수락됨)**: 지정된 인스턴스가 진행 중입니다.
* **HTTP 400(잘못된 요청)**: 지정된 인스턴스가 실패했거나 종료되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스가 없거나 실행이 시작되지 않았습니다.

**HTTP 200** 및 **HTTP 202** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드           | 데이터 형식 | 설명 |
|-----------------|-----------|-------------|
| runtimeStatus   | string    | 인스턴스의 런타임 상태입니다. *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed* 값이 포함됩니다. |
| input           | JSON      | 인스턴스를 초기화하는 데 사용되는 JSON 데이터입니다. |
| customStatus    | JSON      | 사용자 지정 오케스트레이션 상태에 사용되는 JSON 데이터입니다. 이 필드는 설정하지 않으면 `null`입니다. |
| output          | JSON      | 인스턴스의 JSON 출력입니다. 인스턴스가 완료된 상태가 아닌 경우 이 필드는 `null`입니다. |
| createdTime     | string    | 인스턴스를 만든 시간입니다. ISO 8601 확장 표기법을 사용합니다. |
| lastUpdatedTime | string    | 인스턴스가 마지막으로 유지된 시간입니다. ISO 8601 확장 표기법을 사용합니다. |
| historyEvents   | JSON      | 오케스트레이션 실행 기록이 포함된 JSON 배열입니다. `showHistory` 쿼리 문자열 매개 변수가 `true`로 설정되지 않으면 이 필드는 `null`입니다.  | 

다음은 오케스트레이션 실행 기록 및 작업 출력을 포함하는 응답 페이로드 예제입니다(읽기 쉽게 형식이 지정됨).

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** 응답에는 앞에서 언급한 `statusQueryGetUri` 필드와 동일한 URL을 참조하는 **위치** 응답 헤더도 포함됩니다.

### <a name="raise-event"></a>이벤트 발생

실행 중인 오케스트레이션 인스턴스에 이벤트 알림 메시지를 보냅니다.

#### <a name="request"></a>요청

Functions 1.0의 경우 요청 형식은 다음과 같습니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Functions 2.0 형식에는 모두 동일한 매개 변수가 있지만 약간 다른 URL 접두사가 있습니다.

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드       | 매개 변수 형식  | 데이터 형식 | 설명 |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | string    | 대상 오케스트레이션 인스턴스가 대기 중인 이벤트의 이름입니다. |
| {콘텐츠}   | 요청 콘텐츠 | JSON      | JSON 형식 이벤트 페이로드입니다. |

#### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 발생된 이벤트를 처리하도록 수락되었습니다.
* **HTTP 400(잘못된 요청)**: 요청 콘텐츠가 `application/json` 형식이 아니거나 유효한 JSON이 아니었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 실패했으며 발생된 이벤트를 처리할 수 없습니다.

다음은 **operation**이라는 이벤트를 기다리는 인스턴스에 `"incr"` JSON 문자열을 보내는 요청 예제입니다.

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

### <a name="terminate-instance"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스를 종료합니다.

#### <a name="request"></a>요청

Functions 1.0의 경우 요청 형식은 다음과 같습니다.

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 형식에는 모두 동일한 매개 변수가 있지만 약간 다른 URL 접두사가 있습니다.

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드       | 매개 변수 형식  | 데이터 형식 | 설명 |
|-------------|-----------------|-----------|-------------|
| reason      | 쿼리 문자열    | string    | 선택 사항입니다. 오케스트레이션 인스턴스를 종료하는 이유입니다. |

#### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 종료된 요청을 처리하도록 수락되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 실패했습니다.

다음은 실행 중인 인스턴스를 종료하고 **buggy(버그 있음)** 에 대한 이유를 지정하는 요청 예제입니다.

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오류 처리](durable-functions-error-handling.md)
