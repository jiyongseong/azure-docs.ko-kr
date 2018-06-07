---
title: Azure에서 Logic Apps에 대한 오류 및 예외 처리 | Microsoft Docs
description: Logic Apps의 오류 및 예외 처리 패턴
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Logic Apps에서 오류 및 예외 처리

종속 시스템에서 가동 중지 시간 또는 문제를 적절하게 처리하면 모든 통합 아키텍처에 문제가 발생할 수 있습니다. Logic Apps는 문제와 실패에서 복원할 수 있는 강력한 통합을 만들기 위해 오류와 예외를 처리하는 최상의 환경을 제공합니다. 

## <a name="retry-policies"></a>재시도 정책

가장 기본적인 예외 및 오류 처리의 경우 재시도 정책을 사용할 수 있습니다. 초기 요청 시간이 초과되었거나 실패한 경우(429 또는 5xx 응답이 발생하는 요청인 경우), 이 정책은 작업에서 요청을 다시 시도하는지 여부 및 그 방법을 정의합니다. 

기본값, 없음, 고정 간격 및 기하급수적 간격의 네 가지 재시도 정책 유형이 있습니다. 워크플로 정의에 재시도 정책이 없으면 서비스에서 정의한 기본 정책이 대신 사용됩니다.

재시도 정책을 설정하려면 해당하는 경우 논리 앱의 논리 앱 디자이너를 열고 논리 앱의 특정 작업에 대한 **설정**으로 이동합니다. 또는 워크플로 정의에서 다시 시도할 수 있는 경우 특정 작업 또는 트리거에 대한 **inputs** 섹션에서 재시도 정책을 정의할 수 있습니다. 일반적인 구문은 다음과 같습니다.

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

구문 및 **inputs** 섹션에 대한 자세한 내용은 [논리 앱 워크플로의 트리거 및 작업에 있는 재시도 정책 섹션][retryPolicyMSDN]을 참조하세요. 재시도 정책의 제한에 대한 자세한 내용은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

### <a name="default"></a>기본값

**retryPolicy** 섹션에서 재시도 정책을 정의하지 않으면, 논리 앱에서 기하급수적으로 증가하는 간격(7.5초 단위로 조정됨)으로 최대 네 번의 재시도를 보내는 [지수 간격 정책](#exponential-interval)인 기본 정책을 사용합니다. 간격은 5~45초 사이로 제한됩니다. 이 정책은 다음 HTTP 워크플로 정의 예제의 정책과 동일합니다.

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>없음

**retryPolicy**를 **none**으로 설정하는 경우 이 정책은 실패한 요청을 다시 시도하지 않습니다.

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- | 
| 형식 | 예 | 문자열 | **없음** | 
||||| 

### <a name="fixed-interval"></a>고정 간격

**retryPolicy**를 **fixed**로 설정하는 경우 이 정책은 다음 요청을 보내기 전에 지정된 시간 간격 동안 기다린 후 실패한 요청을 다시 시도합니다.

| 요소 이름 | 필수 | 형식 | 설명 |
| ------------ | -------- | ---- | ----------- |
| 형식 | 예 | 문자열 | **고정** |
| count | 예 | 정수  | 재시도 횟수이며, 1~90 사이여야 합니다. | 
| interval | 예 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 간격이며, PT5S와 PT1D 사이여야 합니다. | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>기하급수적 간격

**retryPolicy**를 **exponential**로 설정하는 경우 이 정책은 기하급수적으로 증가하는 범위에서 임의의 시간 간격 후에 실패한 요청을 다시 시도합니다. 또한 **minimumInterval**보다 크고 **maximumInterval**보다 작은 임의의 간격으로 각각의 재시도를 보냅니다. 지수 간격 정책에는 **count**와 **interval**이 필요하며 **minimumInterval**과 **maximumInterval** 값은 선택 사항입니다. PT5S 및 PT1D 기본값을 각각 재정의하려면 다음 값을 추가합니다.

| 요소 이름 | 필수 | 형식 | 설명 |
| ------------ | -------- | ---- | ----------- |
| 형식 | 예 | 문자열 | **기하급수적** |
| count | 예 | 정수  | 재시도 횟수이며, 1~90 사이여야 합니다.  |
| interval | 예 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 간격이며, PT5S와 PT1D 사이여야 합니다. |
| minimumInterval | 아니요 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 최소 재시도 간격이며, PT5S와 **interval** 사이여야 합니다. |
| maximumInterval | 아니오 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 최소 재시도 간격이며, **interval**과 PT1D 사이여야 합니다. | 
||||| 

다음 표에서는 지정된 범위의 균일한 임의 변수가 각 재시도에 대해 **count**까지 생성되는 방법을 보여 줍니다.

**임의 변수 범위**

| 재시도 횟수 | 최소 간격 | 최대 간격 |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>RunAfter 속성을 사용하여 오류 catch 및 처리

각 논리 앱 작업은 워크플로에서 단계 순서를 지정하는 방법과 비슷하게 해당 작업이 시작되기 전에 완료해야 하는 작업을 선언합니다. 작업 정의에서 **runAfter** 속성은 이 순서를 정의하며, 해당 작업을 실행하는 작업 및 작업 상태를 설명하는 개체입니다.

기본적으로 논리 앱 디자이너에서 추가한 모든 작업은 이전 단계의 결과가 **성공**일 때 이전 단계 이후에 실행되도록 설정됩니다. 그러나 이전 작업이 **실패**, **건너뜀** 또는 이러한 값의 일부 조합이 될 때 실행되도록 **runAfter** 값을 사용자 지정할 수 있습니다. 예를 들어 특정 **Insert_Row** 작업이 실패한 후 특정 Service Bus 항목에 항목을 추가하려면 다음 **runAfter** 정의 예제를 사용할 수 있습니다.

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**runAfter** 속성은 **Insert_Row** 작업 상태가 **실패**일 때 실행되도록 설정됩니다. 작업 상태가 **성공**, **실패** 또는 **건너뜀**인 경우 작업을 실행하려면 다음 구문을 사용합니다.

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 이전 작업이 실패한 후 실행되고 성공적으로 완료되는 작업은 **성공**으로 표시됩니다. 이 동작은 워크플로의 모든 실패를 성공적으로 catch하면 실행 자체가 **성공**으로 표시됨을 의미합니다.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>범위 및 해당 결과를 사용하여 작업 평가

**runAfter** 속성을 사용하여 개별 작업을 수행한 후에 단계를 실행하는 방법과 비슷하게 [범위](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 내의 작업을 모두 그룹화할 수 있습니다. 작업을 논리적으로 그룹화하고, 범위의 집계 상태를 평가하고, 해당 상태에 따라 작업을 수행하려는 경우 범위를 사용할 수 있습니다. 범위 내 모든 작업의 실행이 완료되면 범위 자체에서 자체의 상태를 가져옵니다. 

범위의 상태를 확인하려면 **성공**, **실패** 등과 같이 논리 앱의 실행 상태를 확인하는 데 사용하는 것과 동일한 기준을 사용할 수 있습니다. 

기본적으로 범위의 모든 작업이 성공하면 범위 상태가 **성공**으로 표시됩니다. 범위의 마지막 작업이 **실패** 또는 **중단됨**인 경우 범위 상태가 **실패**로 표시됩니다. 

**실패** 범위의 예외를 catch하고 해당 오류를 처리하는 작업을 실행하려면 해당 **실패** 범위에 대해 **runAfter** 속성을 사용할 수 있습니다. 이렇게 하는 경우 범위의 *모든* 작업이 실패하고 해당 범위에 대해 **runAfter** 속성을 사용하면 실패를 catch하는 단일 작업을 만들 수 있습니다.

범위에 대한 제한은 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요.

### <a name="get-context-and-results-for-failures"></a>실패에 대한 컨텍스트 및 결과 가져오기

범위에서 실패를 catch하는 것이 유용하지만, 실패한 작업과 반환된 오류 또는 상태 코드를 정확히 파악하는 데 도움이 되는 컨텍스트가 필요할 수 있습니다. **@result()** 워크플로 함수는 범위에 있는 모든 작업의 결과에 대한 컨텍스트를 제공합니다.

**@result()** 함수는 단일 매개 변수(범위 이름)를 수락하고 해당 범위 내에서 발생하는 모든 작업 결과의 배열을 반환합니다. 이러한 작업 개체에는 작업 시작 시간, 종료 시간, 상태, 입력, 상관 ID 및 출력과 같이 **@actions()** 개체와 동일한 특성이 포함됩니다. 범위 내에서 실패한 작업에 대한 컨텍스트를 보내려면 **@result()** 함수를 **runAfter** 속성과 쉽게 페어링할 수 있습니다.

**실패** 결과가 있는 범위에서 *각 작업*에 대해 작업을 실행하고 실패한 작업에 대한 결과 배열을 필터링하려면 **[배열 필터링](../connectors/connectors-native-query.md)** 작업 및 **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** 루프와 **@result()**를 페어링할 수 있습니다. 필터링된 결과 배열을 사용하고 **ForEach** 루프를 사용하여 각 오류에 대한 작업을 수행할 수 있습니다. 

다음 예제에서는 자세한 설명과 함께 "My_Scope" 범위 내에서 실패한 작업의 응답 본문이 포함된 HTTP POST 요청을 보냅니다.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

이 예제에서 수행되는 작업을 설명하는 자세한 연습은 다음과 같습니다.

1. My_Scope 내의 모든 작업에 대한 결과를 가져오기 위해 **배열 필터링** 작업에서 **@result('My_Scope')**를 필터링합니다.

2. **배열 필터링**에 대한 조건은 상태가 **실패**인 모든 **@result()** 항목입니다. 이 조건은 My_Scope의 모든 작업 결과에 대한 배열을 실패한 작업 결과만 있는 배열로 필터링합니다.

3. *필터링된 배열* 출력에서 **For Each** 루프 작업을 수행합니다. 이 단계는 이전에 필터링된 실패한 작업 결과 *각각에 대해* 작업을 수행합니다.

   범위에서 단일 작업이 실패한 경우 **foreach**의 작업은 한 번만 실행됩니다. 
   여러 실패한 작업에서 오류당 하나의 작업이 발생합니다.

4. **@item()['outputs']['body']**인 **foreach** 항목 응답 본문에 HTTP POST를 보냅니다. **@result()** 항목 모양은 **@actions()** 모양과 같으며 동일한 방식으로 구문 분석할 수 있습니다.

5. 실패한 작업 이름 **@item()[’name’]** 및 실패한 실행 클라이언트 추적 ID **@item()['clientTrackingId']**가 있는 두 개의 사용자 지정 헤더도 포함됩니다.

참고로, 이전 예제에서 구문 분석된 **name**, **body** 및 **clientTrackingId** 속성을 보여 주는 단일 **@result()** 항목의 예제는 다음과 같습니다. **foreach** 작업 외부에서 **@result()**는 이러한 개체의 배열을 반환합니다.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

다른 예외 처리 패턴을 수행하려면 이 문서의 앞부분에서 설명한 식을 사용할 수 있습니다. 필터링된 실패 배열 전체를 허용하는 범위 외부에서 단일 예외 처리 작업을 실행하고 **foreach** 작업을 제거하도록 선택할 수 있습니다. 또한 앞에서 설명한 대로 **@result()** 응답의 다른 유용한 속성을 포함할 수도 있습니다.

## <a name="azure-diagnostics-and-telemetry"></a>Azure 진단 및 원격 분석

이전 패턴은 실행 내에서 오류 및 예외를 처리하는 훌륭한 방법이지만 실행 자체와는 독립적으로 오류를 식별하고 오류에 대응할 수도 있습니다. 
[Azure 진단](../logic-apps/logic-apps-monitor-your-logic-apps.md)은 모든 실행 및 작업 상태를 포함하여 모든 워크플로 이벤트를 Azure Storage 계정 또는 Azure Event Hubs로 만든 이벤트 허브로 보내는 간단한 방법을 제공합니다. 

실행 상태를 평가하려면 로그 및 메트릭을 모니터링하거나 선호하는 모든 모니터링 도구에 게시할 수 있습니다. 한 가지 잠재적 옵션은 Event Hubs를 통해 모든 이벤트를 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)로 스트리밍하는 것입니다. Stream Analytics에서는 진단 로그의 모든 잘못된 부분, 평균 또는 오류를 기반으로 라이브 쿼리를 작성할 수 있습니다. Stream Analytics를 사용하여 큐, 토픽, SQL, Azure Cosmos DB 및 Power BI와 같은 다른 데이터 원본에 정보를 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [고객이 Azure Logic Apps의 오류 처리를 빌드하는 방법을 참조하세요.](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Logic Apps 예제 및 시나리오 더 찾아보기](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9