---
title: 워크플로 트리거 및 작업 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에 대한 워크플로 정의에서 트리거 및 작업에 대해 알아보기
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33886785"
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로 정의에 대한 트리거 및 작업

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 모든 논리 앱 워크플로는 트리거로 시작되고, 작업이 이어집니다. 이 문서에서는 통합 솔루션에서 비즈니스 워크플로 또는 프로세스를 자동화하기 위한 논리 앱을 빌드하는 데 사용할 수 있는 트리거 및 작업을 설명합니다. Logic Apps 디자이너를 시각적으로 사용하거나 [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)로 기본 워크플로 정의를 직접 작성하여 논리 앱을 빌드할 수 있습니다. Azure Portal 또는 Visual Studio를 사용할 수 있습니다. [트리거 및 작업에 대한 가격 책정 방식](../logic-apps/logic-apps-pricing.md)에 대해 알아보세요.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>트리거 개요

모든 논리 앱은 논리 앱 워크플로를 시작할 수 있는 호출을 지정하는 트리거로 시작됩니다. 사용할 수 있는 트리거 유형은 다음과 같습니다.

* *폴링* 트리거 - 정기적인 간격으로 서비스의 HTTP 엔드포인트를 검사합니다.
* *푸시* 트리거 - [워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 호출합니다.
 
모든 트리거에는 이러한 최상위 요소가 있습니다. 단, 일부는 선택 사항입니다.  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*triggerName*> | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | 트리거 유형, 예: “Http” 또는 “ApiConnection” | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| 되풀이 | JSON 개체 | 트리거가 실행되는 빈도를 설명하는 간격과 빈도 |  
| frequency | 문자열 | 트리거가 실행되는 빈도를 설명하는 시간 단위: “초”, “분”, “시간”, “일”, “주” 또는 “월” | 
| interval | 정수  | 빈도에 따라 트리거가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 "월"이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [조건](#trigger-conditions) | 배열 | 워크플로를 실행할 것인지 여부를 결정하는 하나 이상의 조건 | 
| [splitOn](#split-on-debatch) | 문자열 | 처리를 위해 여러 워크플로 인스턴스로 배열 항목을 분할 또는 *분리*하는 식입니다. 이 옵션은 코드 보기에서 직접 작업하는 경우에만 배열을 반환하는 트리거에 대해 사용할 수 있습니다. | 
| [operationOptions](#trigger-operation-options) | 문자열 | 일부 트리거는 사용자가 기본 트리거 동작을 변경할 수 있는 추가 옵션을 제공합니다. | 
||||| 

## <a name="trigger-types-and-details"></a>트리거 유형 및 세부 정보  

각 트리거 유형은 트리거의 동작을 정의하는 다른 인터페이스 및 입력을 갖습니다. 

| 트리거 유형 | 설명 | 
| ------------ | ----------- | 
| [**Recurrence**](#recurrence-trigger) | 정의된 일정에 따라 실행합니다. 이 트리거를 발생시키기 위한 미래의 날짜 및 시간을 설정할 수 있습니다. 빈도에 따라 워크플로를 실행할 시간 및 요일을 지정할 수도 있습니다. | 
| [**Request**](#request-trigger)  | “수동” 트리거로도 알려져 있는 호출할 수 있는 엔드포인트로의 논리 앱을 만듭니다. 예를 들어 [HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. | 
| [**HTTP**](#http-trigger) | HTTP 웹 끝점을 확인 또는 *폴링*합니다. HTTP 엔드포인트는 “202” 비동기 패턴을 사용하거나 배열을 반환하여 특정 트리거 계약을 준수해야 합니다. | 
| [**ApiConnection**](#apiconnection-trigger) | HTTP 트리거처럼 작동하지만 [Microsoft에서 관리하는 API](../connectors/apis-list.md)를 사용합니다. | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Request 트리거처럼 작동하지만 등록 및 등록 취소를 위해 지정된 URL을 호출합니다. |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | HTTPWebhook 트리거처럼 작동하지만 [Microsoft에서 관리하는 API](../connectors/apis-list.md)를 사용합니다. | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>되풀이 트리거  

이 트리거는 사용자가 지정하는 되풀이 및 일정에 따라 실행되고 워크플로를 주기적으로 실행하는 쉬운 방법을 제공합니다. 

트리거 정의 다음과 같습니다.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 되풀이 | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “Recurrence”인 트리거 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| 되풀이 | JSON 개체 | 트리거가 실행되는 빈도를 설명하는 간격과 빈도 |  
| frequency | 문자열 | 트리거가 실행되는 빈도를 설명하는 시간 단위: “초”, “분”, “시간”, “일”, “주” 또는 “월” | 
| interval | 정수  | 빈도에 따라 트리거가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 "월"이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| startTime | 문자열 | 다음 형식의 시작 날짜 및 시간: <p>YYYY-MM-DDThh:mm:ss(표준 시간대를 지정하는 경우) <p>또는 <p>YYYY-MM-DDThh:mm:ssZ(표준 시간대를 지정하지 않는 경우) <p>따라서 예를 들어 2017년 9월 18일, 오후 2시를 원할 경우 “2017-09-18T14:00:00”을 지정하고 “태평양 표준시”와 같은 표준 시간대를 지정하거나 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 지정하지 않으면 공백 없이 맨 끝에 문자 "Z"를 추가해야 합니다. 이 "Z"는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간이 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. 시작 날짜 및 시간에 대한 자세한 내용은 [정기적으로 실행되는 작업 만들기 및 예약](../connectors/connectors-native-recurrence.md)을 참조하세요. | 
| timeZone | 문자열 | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 지정합니다. | 
| hours | 정수 또는 정수 배열 | `frequency`에 대해 "Day" 또는 "Week"를 지정하는 경우 0~23 사이의 정수 하나 이상을 쉼표로 구분해서 워크플로를 실행하려는 시간으로 지정할 수 있습니다. <p>예를 들어 “10”, “12” 및 “14”를 지정하면 10 AM, 12 PM 및 2 PM이 시간 표시로 제공됩니다. | 
| minutes | 정수 또는 정수 배열 | `frequency`에 대해 "Day" 또는 "Week"를 지정하는 경우 0~59 사이의 정수 하나 이상을 쉼표로 구분해서 워크플로를 실행하려는 분으로 지정할 수 있습니다. <p>예를 들어 분 표시로 “30”을 지정하고, 앞에 나온 하루 시간 예제를 사용하면 10:30 AM, 12:30 PM 및 2:30 PM이 표시됩니다. | 
| weekDays | 문자열 또는 문자열 배열 | `frequency`에 대해 “Week”를 지정하는 경우 워크플로를 실행하려는 경우 하나 이상의 요일을 쉼표로 구분해서 지정할 수 있습니다. 예를 들면 "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"와 같이 지정합니다. | 
| 동시성 | JSON 개체 | 되풀이 및 폴링 트리거의 경우 이 개체는 동시에 실행할 수 있는 최대 워크플로 인스턴스 수를 지정합니다. 이 값을 사용하여 백 엔드 시스템에서 수신하는 요청을 제한합니다. <p>예를 들어 이 값은 동시성 제한을 10개 인스턴스로 설정합니다. `"concurrency": { "runs": 10 }` | 
| operationOptions | 문자열 | `singleInstance` 옵션은 모든 활성 실행이 완료된 후에만 트리거가 실행되도록 지정합니다. [트리거: 모든 활성 실행이 완료된 후에만 실행](#single-instance)을 참조하세요. | 
|||| 

*예 1*

이 기본 되풀이 트리거가 매일 실행됩니다.

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*예 2*

트리거를 실행할 시작 날짜 및 시간을 설정할 수 있습니다. 이 되풀이 트리거는 지정된 날짜에 시작한 다음, 매일 실행됩니다.

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*예 3*

이 되풀이 트리거는 2017년 9월 9일 오후 2시에 시작하고, 매주 월요일 오전 10시 30분, 오후 12시 30분, 오후 2시 30분에 실행됩니다(태평양 표준시).

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

이 트리거에 대한 자세한 정보 및 예제는 [정기적으로 실행되는 작업 만들기 및 예약](../connectors/connectors-native-recurrence.md)을 참조하세요.

<a name="request-trigger"></a>

## <a name="request-trigger"></a>요청 트리거

이 트리거는 들어오는 HTTP 요청을 수락할 수 있는 엔드포인트를 만들어서 논리 앱을 호출할 수 있게 합니다. 이 트리거를 호출하려면 [워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows)에서 `listCallbackUrl` API를 사용해야 합니다. HTTP 엔드포인트로 이 트리거를 사용하는 방법을 알아보려면 [HTTP 엔드포인트를 사용하여 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 수동 | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “Request”인 트리거 유형 | 
| kind | 문자열 | “Http”인 요청의 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 메서드 | 문자열 | 요청에서 트리거를 호출하는 데 사용해야 하는 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” 또는 “HEAD” |
| relativePath | 문자열 | HTTP 엔드포인트의 URL을 허용하는 매개 변수에 대한 상대 경로 | 
| schema | JSON 개체 | 트리거가 들어오는 요청에서 수신하는 입력 또는 페이로드에 대해 설명하고 유효성을 검사하는 JSON 스키마. 이 스키마는 후속 워크플로 작업에서 참조할 속성을 파악하는 데 유용합니다. | 
| properties | JSON 개체 | 페이로드를 설명하는 JSON 스키마에서 하나 이상의 속성 | 
| 필수 | 배열 | 값이 필요한 하나 이상의 속성 | 
|||| 

*예제*

이 요청 트리거는 들어오는 요청이 HTTP POST 메서드를 사용하여 트리거를 호출하고, 들어오는 요청에서 입력의 유효성을 검사하는 스키마를 지정합니다. 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>HTTP 트리거  

이 트리거는 지정된 엔드포인트를 폴링하고 응답을 확인합니다. 응답은 워크플로를 실행해야 하는지 여부를 결정합니다. `inputs` JSON 개체는 HTTP 호출을 생성하는 데 필요한 `method` 및 `uri` 매개 변수를 포함하며 필요로 합니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| HTTP | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “Http”인 트리거 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| 메서드 | 예 | 문자열 | 지정된 엔드포인트를 폴링하기 위한 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” 또는 “HEAD” | 
| uri | 예| 문자열 | 트리거가 확인 또는 폴링하는 HTTP 또는 HTTPS 엔드포인트 URL <p>최대 문자열 크기: 2KB | 
| 되풀이 | JSON 개체 | 트리거가 실행되는 빈도를 설명하는 간격과 빈도 |  
| frequency | 문자열 | 트리거가 실행되는 빈도를 설명하는 시간 단위: “초”, “분”, “시간”, “일”, “주” 또는 “월” | 
| interval | 정수  | 빈도에 따라 트리거가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 "월"이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 쿼리 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수 <p>예를 들어 이 요소는 `?api-version=2015-02-01` 쿼리 문자열을 URL에 추가합니다. <p>`"queries": { "api-version": "2015-02-01" }` <p>결과: `https://contoso.com?api-version=2015-02-01` | 
| headers | JSON 개체 | 요청과 함께 보낼 하나 이상의 헤더. <p>예를 들어 요청에 대한 언어 및 유형을 설정하려면 다음을 수행합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | JSON 개체 | 엔드포인트에 보낼 페이로드(데이터) | 
| 인증 | JSON 개체 | 들어오는 요청이 인증에 대해 사용해야 하는 메서드. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. Scheduler 외에 `authority` 속성이 지원됩니다. 지정되지 않은 경우 기본값은 `https://login.windows.net`이지만, `https://login.windows\-ppe.net`과 같이 다른 값을 사용할 수 있습니다. | 
| retryPolicy | JSON 개체 | 이 개체는 4xx 또는 5xx 상태 코드가 있는 일시적인 오류에 대한 다시 시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
| 동시성 | JSON 개체 | 되풀이 및 폴링 트리거의 경우 이 개체는 동시에 실행할 수 있는 최대 워크플로 인스턴스 수를 지정합니다. 이 값을 사용하여 백 엔드 시스템에서 수신하는 요청을 제한합니다. <p>예를 들어 이 값은 동시성 제한을 10개 인스턴스로 설정합니다. <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | 문자열 | `singleInstance` 옵션은 모든 활성 실행이 완료된 후에만 트리거가 실행되도록 지정합니다. [트리거: 모든 활성 실행이 완료된 후에만 실행](#single-instance)을 참조하세요. | 
|||| 

HTTP 트리거가 논리 앱과 잘 작동하도록 하려면 특정 패턴을 따르는 HTTP API가 필요합니다. HTTP 트리거는 다음과 같은 속성을 인식합니다.  
  
| response | 필수 | 설명 | 
| -------- | -------- | ----------- |  
| 상태 코드 | 예 | “200 확인” 상태 코드가 실행을 시작합니다. 다른 상태 코드는 실행을 시작하지 않습니다. | 
| Retry-after 헤더 | 아니오 | 논리 앱이 엔드포인트를 다시 폴링할 때까지의 시간(초) | 
| 위치 헤더 | 아니오 | 다음 폴링 간격에서 호출할 URL입니다. 지정하지 않으면 원래 URL이 사용됩니다. | 
|||| 

*다른 요청에 대한 예제 동작*

| 상태 코드 | 다음 후 다시 시도 | 동작 | 
| ----------- | ----------- | -------- | 
| 200 | {없음} | 워크플로를 실행한 다음, 정의된 되풀이 이후에 데이터가 더 있는지를 다시 확인합니다. | 
| 200 | 10초 | 워크플로를 실행한 다음, 10초 후에 데이터가 더 있는지를 다시 확인합니다. |  
| 202 | 60초 | 워크플로를 트리거하지 않습니다. 정의된 되풀이 간격에 따라 1분 후에 다음 시도를 합니다. 정의된 되풀이 간격이 1분 이내이면 retry-after 헤더가 우선적으로 적용됩니다. 그렇지 않으면 정의된 되풀이 간격이 사용됩니다. | 
| 400 | {없음} | 잘못된 요청이며 워크플로를 실행하지 않습니다. `retryPolicy`가 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거는 정의된 되풀이 이후에 데이터가 더 있는지 다시 확인합니다. | 
| 500 | {없음}| 서버 오류이며 워크플로를 실행하지 않습니다. `retryPolicy`가 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거는 정의된 되풀이 이후에 데이터가 더 있는지 다시 확인합니다. | 
|||| 

### <a name="http-trigger-outputs"></a>HTTP 트리거 출력

| 요소 이름 | type | 설명 |
| ------------ | ---- | ----------- |
| headers | JSON 개체 | HTTP 응답의 헤더 | 
| 본문 | JSON 개체 | HTTP 응답의 본문 | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection 트리거  

이 트리거는 [HTTP 트리거](#http-trigger)처럼 작동하지만, [Microsoft에서 관리하는 API](../connectors/apis-list.md)를 사용하므로 이 트리거에 대한 매개 변수는 다릅니다. 

트리거 정의는 다음과 같습니다. 많은 섹션이 선택 사항이긴 하지만, 트리거의 동작은 섹션의 포함 여부에 따라 결정됩니다.

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “ApiConnection”인 트리거 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| host | JSON 개체 | 관리되는 API에 대한 호스트 게이트웨이 및 ID를 설명하는 JSON 개체 <p>`host` JSON 개체에는 `api` 및 `connection`과 같은 요소가 있습니다. | 
| api | JSON 개체 | 관리되는 API에 대한 엔드포인트 URL: <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| connection | JSON 개체 | 워크플로에서 사용하는 관리되는 API 연결의 이름으로, `$connection`이라는 매개 변수에 대한 참조를 포함해야 합니다. <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| 메서드 | 문자열 | 관리되는 API와 통신하기 위한 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” 또는 “HEAD” | 
| 되풀이 | JSON 개체 | 트리거가 실행되는 빈도를 설명하는 간격과 빈도 |  
| frequency | 문자열 | 트리거가 실행되는 빈도를 설명하는 시간 단위: “초”, “분”, “시간”, “일”, “주” 또는 “월” | 
| interval | 정수  | 빈도에 따라 트리거가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 "월"이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 쿼리 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수 <p>예를 들어 이 요소는 `?api-version=2015-02-01` 쿼리 문자열을 URL에 추가합니다. <p>`"queries": { "api-version": "2015-02-01" }` <p>결과: `https://contoso.com?api-version=2015-02-01` | 
| headers | JSON 개체 | 요청과 함께 보낼 하나 이상의 헤더. <p>예를 들어 요청에 대한 언어 및 유형을 설정하려면 다음을 수행합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | JSON 개체 | 관리되는 API로 보내는 페이로드(데이터)를 설명하는 JSON 개체 | 
| 인증 | JSON 개체 | 들어오는 요청이 인증에 대해 사용해야 하는 메서드. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
| retryPolicy | JSON 개체 | 이 개체는 4xx 또는 5xx 상태 코드가 있는 일시적인 오류에 대한 다시 시도 동작을 사용자 지정합니다. <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
| 동시성 | JSON 개체 | 되풀이 및 폴링 트리거의 경우 이 개체는 동시에 실행할 수 있는 최대 워크플로 인스턴스 수를 지정합니다. 이 값을 사용하여 백 엔드 시스템에서 수신하는 요청을 제한합니다. <p>예를 들어 이 값은 동시성 제한을 10개 인스턴스로 설정합니다. `"concurrency": { "runs": 10 }` | 
| operationOptions | 문자열 | `singleInstance` 옵션은 모든 활성 실행이 완료된 후에만 트리거가 실행되도록 지정합니다. [트리거: 모든 활성 실행이 완료된 후에만 실행](#single-instance)을 참조하세요. | 
||||

*예제*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>APIConnection 트리거 출력
 
| 요소 이름 | type | 설명 |
| ------------ | ---- | ----------- |
| headers | JSON 개체 | HTTP 응답의 헤더 | 
| 본문 | JSON 개체 | HTTP 응답의 본문 | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>HTTPWebhook 트리거  

이 트리거는 논리 앱에 대한 호출 가능 엔드포인트를 만들어서 [Request 트리거](#request-trigger)처럼 작동합니다. 그러나 이 트리거는 구독을 등록하거나 등록 취소하기 위한 지정된 끝점 URL을 호출합니다. [HTTP 비동기 제한](#asynchronous-limits)과 동일한 방식으로 웹후크 트리거에 대한 제한을 지정할 수 있습니다. 

트리거 정의는 다음과 같습니다. 많은 섹션이 선택 사항이긴 하지만, 트리거의 동작은 사용자가 사용하거나 생략하는 섹션에 따라 결정됩니다.

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “HttpWebhook”인 트리거 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| subscribe | JSON 개체| 트리거가 생성될 때 초기 등록을 호출 및 수행할 나가는 요청. 이 호출이 발생하면 트리거가 엔드포인트에서 이벤트 수신을 시작할 수 있습니다. 자세한 내용은 [구독 및 구독 취소](#subscribe-unsubscribe)를 참조하세요. | 
| 메서드 | 문자열 | 구독 요청에 사용되는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” 또는 “HEAD” | 
| uri | 문자열 | 구독 요청을 보낼 위치에 대한 엔드포인트 URL | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| unsubscribe | JSON 개체 | 작업으로 인해 트리거가 잘못되면 자동으로 구독을 호출하고 취소하는 나가는 요청. 자세한 내용은 [구독 및 구독 취소](#subscribe-unsubscribe)를 참조하세요. | 
| 메서드 | 문자열 | 취소 요청에 사용하는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” 또는 “HEAD” | 
| uri | 문자열 | 취소 요청을 보낼 위치에 대한 엔드포인트 URL | 
| 본문 | JSON 개체 | 구독 또는 취소 요청에 대한 페이로드(데이터)를 설명하는 JSON 개체 | 
| 인증 | JSON 개체 | 들어오는 요청이 인증에 대해 사용해야 하는 메서드. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
| retryPolicy | JSON 개체 | 이 개체는 4xx 또는 5xx 상태 코드가 있는 일시적인 오류에 대한 다시 시도 동작을 사용자 지정합니다. <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
|||| 

*예제*

```json
"myAppSpotTrigger": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "headers": {},
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` 및 `unsubscribe`

`subscribe` 호출은 예를 들어 자격 증명이 갱신되거나 트리거의 입력 매개 변수가 변경될 때와 같이 워크플로가 어떤 식으로든 변경될 때 발생합니다. 호출은 표준 HTTP 작업과 동일한 매개 변수를 사용합니다. 
 
다음 예제와 같이 작업으로 인해 HTTPWebhook 트리거가 잘못될 때 `unsubscribe` 호출이 자동으로 발생합니다.

* 트리거 삭제 또는 비활성화 
* 워크플로 삭제 또는 비활성화 
* 구독 삭제 또는 비활성화 

이러한 호출을 지원하려면 `@listCallbackUrl()` 함수는 이 트리거에 대한 고유한 “콜백 URL”을 반환합니다. 이 URL은 서비스의 REST API를 사용하는 엔드포인트에 대한 고유 식별자를 나타냅니다. 이 함수에 대한 매개 변수는 HTTP 트리거와 같습니다.

### <a name="httpwebhook-trigger-outputs"></a>HTTPWebhook 트리거 출력

| 요소 이름 | type | 설명 |
| ------------ | ---- | ----------- |
| headers | JSON 개체 | HTTP 응답의 헤더 | 
| 본문 | JSON 개체 | HTTP 응답의 본문 | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook 트리거

이 트리거는 [HTTPWebhook 트리거](#httpwebhook-trigger)처럼 작동하지만 [Microsoft에서 관리하는 API](../connectors/apis-list.md)를 사용합니다. 

트리거 정의 다음과 같습니다.

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*필수*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | JSON 개체 | JSON(Javascript Notation) 형식에 설명된 개체인 트리거에 대한 이름  | 
| 형식 | 문자열 | “ApiConnectionWebhook”인 트리거 유형 | 
| inputs | JSON 개체 | 트리거의 동작을 정의하는 트리거의 입력 | 
| host | JSON 개체 | 관리되는 API에 대한 호스트 게이트웨이 및 ID를 설명하는 JSON 개체 <p>`host` JSON 개체에는 `api` 및 `connection`과 같은 요소가 있습니다. | 
| connection | JSON 개체 | 워크플로에서 사용하는 관리되는 API 연결의 이름으로, `$connection`이라는 매개 변수에 대한 참조를 포함해야 합니다. <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| 본문 | JSON 개체 | 관리되는 API로 보내는 페이로드(데이터)를 설명하는 JSON 개체 | 
| NotificationUrl | 문자열 | 관리되는 API가 사용할 수 있는 이 트리거에 대한 고유한 “콜백 URL”을 반환합니다. | 
|||| 

*선택 사항*

| 요소 이름 | type | 설명 | 
| ------------ | ---- | ----------- | 
| 쿼리 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수 <p>예를 들어 이 요소는 `?folderPath=Inbox` 쿼리 문자열을 URL에 추가합니다. <p>`"queries": { "folderPath": "Inbox" }` <p>결과: `https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>트리거: 조건

모든 트리거의 경우 워크플로의 실행 여부를 결정하는 하나 이상의 조건과 함께 배열을 포함할 수 있습니다. 이 예제에서는 워크플로의 `sendReports` 매개 변수가 true로 설정된 경우에만 보고서 트리거가 실행됩니다. 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

또한 조건에서 트리거의 상태 코드를 참조할 수 있습니다. 예를 들어 다음과 같이 웹 사이트에서 “500” 상태 코드를 반환하는 경우에만 워크플로를 시작하려 한다고 가정합니다.

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> 기본적으로 트리거는 "200 확인" 응답을 받는 경우에만 트리거됩니다. 식에서 어떤 방식으로든 트리거의 상태 코드를 참조하는 경우 트리거의 기본 동작이 바뀝니다. 따라서 여러 상태 코드(예: 200 및 201 상태 코드)에서 트리거를 실행하려면 다음 명령문을 조건으로 포함해야 합니다. 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>트리거: 여러 번 실행으로 배열을 분할

트리거에서 논리 앱이 처리할 배열을 반환하는 경우, "for each" 루프에서 각 배열 항목을 처리하는 데 너무 오래 걸리는 경우가 있습니다. 대신, 트리거에 **SplitOn** 속성을 사용하여 배열을 *분리 처리(debatch)* 합니다. 

분리 처리는 배열 항목을 분할하고 각 배열 항목에 대해 실행되는 새 논리 앱 인스턴스를 시작합니다. 예를 들어 이 방법은 폴링 간격 사이에 여러 개의 새 항목을 반환할 수 있는 엔드포인트를 폴링하려는 경우에 유용합니다.
**SplitOn**이 단일 논리 앱 실행에서 처리할 수 있는 배열 항목의 최대 개수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

> [!NOTE]
> 논리 앱의 JSON 정의에 대한 코드 뷰에서 수동으로 정의하거나 재정의하여 트리거에 **SplitOn**만 추가할 수 있습니다. 동기 응답 패턴을 구현하려는 경우 **SplitOn**을 사용할 수 없습니다. **SplitOn**을 사용하고 응답 작업을 포함하는 모든 워크플로는 비동기적으로 실행되고 즉시 `202 ACCEPTED` 응답을 보냅니다.

트리거의 Swagger 파일에서 배열인 페이로드를 설명하고 있으면 **SplitOn** 속성이 자동으로 트리거에 추가됩니다. 그렇지 않으면 분리 처리할 배열이 있는 응답 페이로드 내에 이 속성을 추가합니다. 

예를 들어 이 응답을 반환하는 API가 있다고 가정합니다. 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
논리 앱에는 `Rows`의 콘텐츠만 필요하므로 다음 예제와 같은 트리거를 만들 수 있습니다.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": 1
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> `SplitOn` 명령을 사용하는 경우 배열 외부에 있는 속성을 가져올 수 없습니다. 따라서 이 예제에서는 API에서 반환된 응답에서 `status` 속성을 가져올 수 없습니다.
> 
> `Rows` 속성이 없는 경우 오류를 방지하기 위해 이 예제에서는 `?` 연산자를 사용하고 있습니다.

이제 워크플로 정의에서 `@triggerBody().name`을 사용하여 첫 번째 실행에서 `customer-name-one`을, 두 번째 실행에서 `customer-name-two`를 가져올 수 있습니다. 따라서 트리거 출력의 예제는 다음과 같습니다.

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>트리거: 작업 옵션

이러한 트리거는 사용자가 기본 동작을 변경할 수 있는 더 많은 옵션을 제공합니다.

| 트리거 | 작업 옵션 | 설명 |
|---------|------------------|-------------|
| [Recurrence](#recurrence-trigger), <br>[HTTP](#http-trigger), <br>[ApiConnection](#apiconnection-trigger) | singleInstance | 모든 활성 실행이 완료된 후에만 트리거를 실행합니다. |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>트리거: 모든 활성 실행이 완료된 후에만 실행

되풀이를 설정할 수 있는 트리거의 경우 모든 활성 실행이 완료된 후에만 트리거를 실행하도록 지정할 수 있습니다. 워크플로 인스턴스가 실행되는 동안 예약된 되풀이가 발생하면, 다시 확인하기 전에 트리거에서 이를 건너뛰고 예약된 다음 되풀이까지 기다립니다. 예: 

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>작업 개요

다양한 작업 형식이 있으며 각각 고유한 동작을 포함합니다. 각 작업 형식은 작업의 동작을 정의하는 서로 다른 입력을 포함합니다. 컬렉션 작업은 자체적으로 여러 다른 작업을 포함할 수 있습니다. 

### <a name="standard-actions"></a>표준 작업  

| 작업 유형 | 설명 | 
| ----------- | ----------- | 
| **HTTP** | HTTP 웹 끝점을 호출합니다. | 
| **ApiConnection**  | HTTP 작업처럼 작동하지만 [Microsoft에서 관리하는 API](https://docs.microsoft.com/azure/connectors/apis-list)를 사용합니다. | 
| **ApiConnectionWebhook** | HTTPWebhook처럼 작동하지만 Microsoft에서 관리하는 API를 사용합니다. | 
| **응답** | 들어오는 호출에 대한 응답을 정의합니다. | 
| **작성** | 작업의 입력에서 임의 개체를 생성합니다. | 
| **Function** | Azure 함수를 만듭니다. | 
| **Wait** | 정해진 시간 동안 기다리거나 특정 시간까지 대기합니다. | 
| **Workflow** | 중첩된 워크플로를 나타냅니다. | 
| **작성** | 작업의 입력에서 임의 개체를 생성합니다. | 
| **쿼리** | 조건에 따라 배열을 필터링합니다. | 
| **Select** | 배열의 각 요소를 새 값으로 프로젝션합니다. 예를 들어 숫자 배열을 개체 배열로 변환할 수 있습니다. | 
| **테이블** | 항목의 배열을 CSV 또는 HTML 테이블로 변환합니다. | 
| **Terminate** | 워크플로 실행을 중지합니다. | 
| **Wait** | 정해진 시간 동안 기다리거나 특정 시간까지 대기합니다. | 
| **Workflow** | 중첩된 워크플로를 나타냅니다. | 
||| 

### <a name="collection-actions"></a>컬렉션 작업

| 작업 유형 | 설명 | 
| ----------- | ----------- | 
| **If** | 식을 계산하고 결과에 따라 해당 분기를 실행합니다. | 
| **Switch** | 개체의 특정 값을 기반으로 하는 다른 작업을 수행합니다. | 
| **ForEach** | 이 루프 작업은 배열을 반복하고 각 배열 항목에 대해 내부 작업을 수행합니다. | 
| **Until** | 이 루프 작업은 조건 결과가 true일 때까지 내부 작업을 수행합니다. | 
| **범위** | 다른 작업을 논리적으로 그룹화하는 데 사용합니다. | 
|||  

## <a name="http-action"></a>HTTP 동작  

HTTP 작업은 지정된 엔드포인트를 호출하고 응답을 확인하여 워크플로를 실행할지 여부를 결정합니다. 예: 
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

여기서 `inputs` 개체는 HTTP 호출을 생성하는 데 필요한 다음 매개 변수를 사용합니다. 

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- | 
| 메서드 | 예 | 문자열 | HTTP 메서드 "GET", "POST", "PUT", "DELETE", "PATCH" 또는 "HEAD" 중 하나를 사용합니다. | 
| uri | 예| 문자열 | 트리거가 확인하는 HTTP 또는 HTTPS 끝점입니다. 최대 문자열 크기: 2KB | 
| 쿼리 | 아니오 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수를 나타냅니다. <p>예를 들어 `"queries": { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다. | 
| headers | 아니오 | JSON 개체 | 요청에서 전송된 각 헤더를 나타냅니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | 아니오 | JSON 개체 | 끝점에 전송된 페이로드를 나타냅니다. | 
| retryPolicy | 아니오 | JSON 개체 | 4xx 또는 5xx 오류에 대한 다시 시도 동작을 사용자 지정하기 위해 이 개체를 사용합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
| operationsOptions | 아니오 | 문자열 | 재정의할 특정 동작 집합을 정의합니다. | 
| 인증 | 아니오 | JSON 개체 | 요청이 인증을 위해 사용해야 하는 메서드를 나타냅니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. <p>스케줄러 이외에도 지원되는 속성이 하나 더 있습니다. `authority` 기본적으로 지정되지 않은 경우 이 값은 `https://login.windows.net`이지만 `https://login.windows\-ppe.net`과 등의 다른 값을 사용할 수 있습니다. | 
||||| 

HTTP 작업 및 APIConnection 작업은 *다시 시도 정책*을 지원합니다. 다시 시도 정책은 모든 연결 예외 외에도 HTTP 상태 코드 408, 429 및 5xx로 지정되는 일시적인 오류에 적용됩니다. 여기에 표시된 것처럼 `retryPolicy` 개체를 사용하여 이 정책을 정의할 수 있습니다.
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

이 예제 HTTP 작업은 일시적인 오류가 있는 경우 최신 뉴스 가져오기를 두 번 다시 시도하는 데 각 시도 사이에 30초 지연을 포함하여 총 3번 실행합니다.
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

다시 시도 간격은 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)으로 지정됩니다. 이 간격에는 기본값이 있으며 최소값은 20초인 반면 최대값은 1시간입니다. 기본값 및 최대 재시도 횟수는 4시간입니다. 재시도 정책 정의를 지정하지 않으면 `fixed` 전략이 기본 재시도 횟수 및 간격 값으로 사용됩니다. 다시 시도 정책을 사용하지 않도록 설정하려면 해당 형식을 `None`으로 설정합니다.

### <a name="asynchronous-patterns"></a>비동기 패턴

기본적으로 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 따라서 원격 서버가 "202 ACCEPTED" 응답으로 처리를 위해 요청을 수락했다는 것을 나타내면 Logic Apps 엔진은 터미널 상태가 202가 아닌 응답에 도달할 때까지 응답의 위치 헤더에 지정된 URL의 폴링을 유지합니다.
  
이전에 설명된 비동기 동작을 사용하지 않도록 하려면 작업 입력에서 `operationOptions`를 `DisableAsyncPattern`으로 설정합니다. 이 경우 작업 출력은 서버의 초기 202 응답을 기준으로 합니다. 예: 
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>비동기 제한

비동기 패턴의 지속 시간을 특정 시간 간격으로 제한할 수 있습니다. 최종 상태에 도달하기 전에 시간 간격이 경과하면 작업의 상태가 `ActionTimedOut` 코드를 사용하여 `Cancelled`로 표시됩니다. 시간 제한은 ISO 8601 형식으로 지정됩니다. 다음 예제에서는 제한을 지정하는 방법을 보여 줍니다.


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection 작업

이 작업에서는 유효한 연결에 대한 참조와 API 및 매개 변수에 대한 정보가 필요하므로 Microsoft 관리 커넥터를 참조합니다. APIConnection 작업 예제는 다음과 같습니다.

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- | 
| host | 예 | JSON 개체 | `runtimeUrl` 및 연결 개체에 대한 참조 등 커넥터 정보를 나타냅니다. | 
| 메서드 | 예 | 문자열 | HTTP 메서드 "GET", "POST", "PUT", "DELETE", "PATCH" 또는 "HEAD" 중 하나를 사용합니다. | 
| 경로 | 예 | 문자열 | API 작업의 경로입니다. | 
| 쿼리 | 아니오 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수를 나타냅니다. <p>예를 들어 `"queries": { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다. | 
| headers | 아니오 | JSON 개체 | 요청에서 전송된 각 헤더를 나타냅니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | 아니오 | JSON 개체 | 끝점에 전송된 페이로드를 나타냅니다. | 
| retryPolicy | 아니오 | JSON 개체 | 4xx 또는 5xx 오류에 대한 다시 시도 동작을 사용자 지정하기 위해 이 개체를 사용합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
| operationsOptions | 아니오 | 문자열 | 재정의할 특정 동작 집합을 정의합니다. | 
| 인증 | 아니오 | JSON 개체 | 요청이 인증을 위해 사용해야 하는 메서드를 나타냅니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
||||| 

다시 시도 정책은 모든 연결 예외 외에도 HTTP 상태 코드 408, 429 및 5xx로 지정되는 일시적인 오류에 적용됩니다. 여기에 표시된 것처럼 `retryPolicy` 개체를 사용하여 이 정책을 정의할 수 있습니다.

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>APIConnectionWebhook 작업

APIConnectionWebhook 작업은 Microsoft에서 관리하는 커넥터를 참조합니다. 이 작업에는 유효한 연결에 대한 참조와 필요한 API 및 매개 변수에 대한 정보가 필요합니다. [HTTP 비동기 제한](#asynchronous-limits)과 동일한 방식으로 웹후크 작업에 대한 제한을 지정할 수 있습니다.

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- | 
| host | 예 | JSON 개체 | `runtimeUrl` 및 연결 개체에 대한 참조 등 커넥터 정보를 나타냅니다. | 
| 경로 | 예 | 문자열 | API 작업의 경로입니다. | 
| 쿼리 | 아니오 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수를 나타냅니다. <p>예를 들어 `"queries": { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다. | 
| headers | 아니오 | JSON 개체 | 요청에서 전송된 각 헤더를 나타냅니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | 아니오 | JSON 개체 | 끝점에 전송된 페이로드를 나타냅니다. | 
| retryPolicy | 아니오 | JSON 개체 | 4xx 또는 5xx 오류에 대한 다시 시도 동작을 사용자 지정하기 위해 이 개체를 사용합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md)을 참조하세요. | 
| operationsOptions | 아니오 | 문자열 | 재정의할 특정 동작 집합을 정의합니다. | 
| 인증 | 아니오 | JSON 개체 | 요청이 인증을 위해 사용해야 하는 메서드를 나타냅니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
||||| 

## <a name="response-action"></a>응답 작업  

이 작업에는 HTTP 요청의 전체 응답 페이로드와 `statusCode`, `body` 및 `headers`가 포함됩니다.
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

response 작업에는 다른 작업에는 적용되지 않는 특수한 제한이 있습니다. 구체적으로 다음과 같습니다.  
  
* 들어오는 요청에는 결정적 응답이 필요하기 때문에 논리 앱 정의 내의 병렬 분기에 response 작업을 포함할 수 없습니다.
  
* 들어오는 요청이 이미 응답을 수신한 후 워크플로가 response 작업에 도달하면 response 작업은 실패 또는 충돌 상태로 간주됩니다. 따라서 논리 앱 실행은 `Failed`로 표시됩니다.
  
* response 작업이 있는 워크플로는 트리거 정의에 `splitOn` 명령을 사용할 수 없습니다. 해당 호출 시 여러 실행이 생성되기 때문입니다. 따라서 workflow 작업이 PUT일 때 이러한 경우인지 확인하고 "잘못된 요청" 응답을 반환합니다.

## <a name="compose-action"></a>작성 작업

이 작업을 사용하여 임의 개체를 생성할 수 있으며 작업의 입력을 계산한 결과가 출력으로 제공됩니다. 

> [!NOTE]
> 개체, 배열 및 논리 앱에서 기본적으로 지원하는 기타 형식(XML 및 이진)을 포함한 출력을 생성하는 데 `Compose` 작업을 사용할 수 있습니다.

예를 들어 `Compose` 작업을 사용하여 여러 작업의 출력을 병합할 수 있습니다.

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Function 작업

이 작업을 사용하여 다음과 같이 [Azure 함수](../azure-functions/functions-overview.md)를 나타내고 호출할 수 있습니다.

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- |  
| function id | 예 | 문자열 | 호출하려는 Azure 함수의 리소스 ID입니다. | 
| 메서드 | 아니오 | 문자열 | 함수를 호출하는 데 사용되는 HTTP 메서드입니다. 지정하지 않으면 "POST"가 기본 메서드입니다. | 
| 쿼리 | 아니오 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수를 나타냅니다. <p>예를 들어 `"queries": { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다. | 
| headers | 아니오 | JSON 개체 | 요청에서 전송된 각 헤더를 나타냅니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | 아니오 | JSON 개체 | 끝점에 전송된 페이로드를 나타냅니다. | 
|||||

논리 앱을 저장하면 Azure Logic Apps 엔진에서 참조된 함수에 대해 몇 가지 검사를 수행합니다.

* 함수에 액세스할 수 있어야 합니다.
* 표준 HTTP 트리거 또는 일반 JSON 웹후크 트리거만 사용할 수 있습니다.
* 이 함수에는 경로가 정의되어 있지 않아야 합니다.
* "함수" 및 "익명" 권한 부여 수준만 허용됩니다.

> [!NOTE]
> Logic Apps 엔진은 런타임에 사용되는 트리거 URL을 검색하고 캐시합니다. 따라서 어떤 작업으로 캐시된 URL이 무효화되면 런타임에 작업이 실패합니다. 이 문제를 해결하려면 논리 앱을 다시 저장합니다. 그러면 논리 앱에서 트리거 URL을 다시 검색하고 캐시합니다.

## <a name="select-action"></a>작업 선택

이 작업은 배열의 각 요소를 새 값으로 프로젝션하도록 합니다. 다음 예제에서는 숫자 배열을 개체 배열로 변환합니다.

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| from | 예 | 배열 | 원본 배열입니다. |
| 선택 | 예 | 모두 | 원본 배열의 각 요소에 적용되는 프로젝션입니다. |
||||| 

`select` 작업의 출력은 입력 배열과 동일한 카디널리티를 갖는 배열입니다. 각 요소는 `select` 속성에 정의된 대로 변환됩니다. 입력이 빈 배열인 경우 출력도 빈 배열입니다.

## <a name="terminate-action"></a>종료 작업

이 작업은 워크플로 실행을 중지하고, 진행 중인 작업을 취소하며, 나머지 작업을 건너뜁니다. 종료 작업은 이미 완료된 작업에 영향을 주지 않습니다.

예를 들어 `Failed` 상태의 실행을 중지하는 경우는 다음과 같습니다.

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| runStatus | 예 | 문자열 | 대상 실행의 상태로, `Failed` 또는 `Cancelled`입니다. |
| runError | 아니오 | JSON 개체 | 오류 세부 정보입니다. `runStatus`가 `Failed`로 설정된 경우에만 지원됩니다. |
| runError code | 아니오 | 문자열 | 실행의 오류 코드입니다. |
| runError message | 아니오 | 문자열 | 실행의 오류 메시지입니다. | 
||||| 

## <a name="query-action"></a>쿼리 작업

이 작업을 통해 조건에 따라 배열을 필터링할 수 있습니다. 

> [!NOTE]
> 작성 작업을 사용하여 개체, 배열 및 논리 앱에서 기본적으로 지원하는 다른 형식(예: XML 및 이진)을 포함한 출력을 생성할 수 있습니다.

예를 들어 2보다 큰 숫자를 선택하는 경우 다음과 같습니다.

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| from | 예 | 배열 | 원본 배열입니다. |
| 여기서, | 예 | 문자열 | 원본 배열의 각 요소에 적용되는 조건입니다. `where` 조건을 충족하는 값이 없는 경우 결과는 빈 배열입니다. |
||||| 

`query` 작업의 출력은 조건을 충족하는 입력 배열의 요소를 포함하는 배열입니다.

## <a name="table-action"></a>테이블 작업

이 작업을 사용하면 배열을 CSV 또는 HTML 테이블로 변환할 수 있습니다. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| from | 예 | 배열 | 원본 배열입니다. `from` 속성 값이 빈 배열인 경우 출력도 빈 테이블입니다. | 
| format | 예 | 문자열 | 원하는 테이블 형식이며, "CSV" 또는 "HTML" 중 하나입니다. | 
| 열 | 아니오 | 배열 | 원하는 테이블 열입니다. 기본 테이블 모양을 재정의하는 데 사용합니다. | 
| 열 머리글 | 아니오 | 문자열 | 열 머리글입니다. | 
| 열 값 | 예 | 문자열 | 열 값입니다. | 
||||| 

다음 예제와 같이 테이블 작업을 정의한다고 가정합니다.

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

그리고 다음 배열을 `@triggerBody()`에 사용합니다.

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

이 예제의 출력은 다음과 같습니다.

<table><thead><tr><th>ID</th><th>Name</th></tr></thead><tbody><tr><td>0</td><td>사과</td></tr><tr><td>1</td><td>오렌지</td></tr></tbody></table>

테이블을 사용자 지정하려면 다음과 같이 열을 명시적으로 지정할 수 있습니다.

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

이 예제의 출력은 다음과 같습니다.

<table><thead><tr><th>제품 ID</th><th>설명</th></tr></thead><tbody><tr><td>0</td><td>신선한 사과</td></tr><tr><td>1</td><td>신선한 오렌지</td></tr></tbody></table>

## <a name="wait-action"></a>대기 작업  

이 작업은 지정된 간격에 워크플로 실행을 일시 중단합니다. 이 예제에서는 워크플로가 15분 동안 대기합니다.
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
또는 특정 시점까지 대기하려면 다음 예제를 사용할 수 있습니다.
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> `interval` 개체 또는 `until` 개체 중 하나를 사용하여 대기 기간을 지정할 수 있지만, 둘 다는 지정할 수 없습니다.

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- | 
| until | 아니오 | JSON 개체 | 특정 시점에 따른 대기 시간입니다. | 
| until timestamp | 예 | 문자열 | 대기가 만료되는 [UTC 날짜/시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 특정 시점입니다. | 
| interval | 아니오 | JSON 개체 | 간격 단위 및 수에 따른 대기 시간입니다. | 
| interval unit | 예 | 문자열 | 시간 단위입니다. 값으로 "second", "minute", "hour", "day", "week" 또는 "month" 중 하나만 사용합니다. | 
| interval count | 예 | 정수  | 대기 기간에 사용되는 간격 단위 수를 나타내는 양의 정수입니다. | 
||||| 

## <a name="workflow-action"></a>워크플로 작업

이 작업을 통해 워크플로를 중첩할 수 있습니다. Logic Apps 엔진에서 자식 워크플로, 더 구체적으로 트리거에 대해 액세스 검사를 수행하므로 자식 워크플로에 액세스할 수 있어야 합니다. 예: 

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 요소 이름 | 필수 | 형식 | 설명 | 
| ------------ | -------- | ---- | ----------- |  
| host id | 예 | 문자열| 호출할 워크플로의 리소스 ID입니다. | 
| host triggerName | 예 | 문자열 | 호출할 트리거의 이름입니다. | 
| 쿼리 | 아니오 | JSON 개체 | URL에 포함하려는 모든 쿼리 매개 변수를 나타냅니다. <p>예를 들어 `"queries": { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다. | 
| headers | 아니오 | JSON 개체 | 요청에서 전송된 각 헤더를 나타냅니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| 본문 | 아니오 | JSON 개체 | 끝점에 전송된 페이로드를 나타냅니다. | 
||||| 

이 작업의 출력은 하위 워크플로의 `Response` 작업에 정의된 내용을 기준으로 합니다. 자식 워크플로에서 `Response` 작업을 정의하지 않으면 출력이 비어 있습니다.

## <a name="collection-actions-overview"></a>컬렉션 작업 개요

워크플로 실행을 제어할 수 있도록 컬렉션 작업에는 다른 작업이 포함될 수 있습니다. 컬렉션 외부에서 컬렉션의 참조 작업을 직접 참조할 수 있습니다. 예를 들어 범위에 `Http` 작업을 정의하면 `@body('http')`는 워크플로의 모든 위치에서 계속 유효합니다. 또한 컬렉션의 작업은 동일한 컬렉션의 다른 작업 "이후에만 실행"될 수 있습니다.

## <a name="if-action"></a>If 작업

조건문인 이 작업을 통해 조건을 평가하고 식이 true로 평가 되는지 여부에 따라 분기를 실행할 수 있습니다. 조건이 true로 성공적으로 평가되면 해당 조건은 “성공” 상태로 표시됩니다. `actions` 또는 `else` 개체에 있는 작업은 다음 값으로 평가됩니다.

* "성공" - 성공적으로 실행되는 경우
* "실패" - 실행되었지만 실패하는 경우
* "건너뜀" - 해당 분기가 실행되지 않는 경우

[논리 앱의 조건문](../logic-apps/logic-apps-control-flow-conditional-statement.md)에 대해 자세히 알아보세요.

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| actions | 예 | JSON 개체 | `expression`이 `true`로 평가될 때 실행할 내부 작업입니다. | 
| 식 | 예 | 문자열 | 평가할 식 |
| else | 아니오 | JSON 개체 | `expression`이 `false`로 평가될 때 실행할 내부 작업입니다. |
||||| 

예: 

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>작업에서 조건 식을 사용하는 방법

조건에서 식을 사용하는 방법을 보여 주는 몇 가지 예제는 다음과 같습니다.
  
| JSON 식 | 결과 | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | true로 평가 되는 값이 있으면 해당 조건이 전달됩니다. 부울 식만 지원됩니다. 다른 형식을 부울로 변환하려면 `empty` 또는 `equals` 함수를 사용합니다. | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | 비교 함수가 지원됩니다. 이 예제에서는 action1의 출력이 임계값보다 큰 경우에만 작업이 실행됩니다. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | 중첩된 부울 식을 만들기 위한 논리 함수가 지원됩니다. 이 예제에서는 action1의 출력이 임계값보다 크거나 100 미만인 경우에 작업이 실행됩니다. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | 배열에 항목이 있는지 여부를 확인하려면 배열 함수를 사용할 수 있습니다. 이 예제에서는 errors 배열이 비어 있는 경우에 작업이 실행됩니다. | 
| `"expression": "parameters('hasSpecialAction')"` | 이 식은 오류를 발생시키고 유효한 조건이 아닙니다. 조건에서 "@" 기호를 사용해야 합니다. | 
||| 

## <a name="switch-action"></a>Switch 작업

switch 문인 이 작업은 개체, 식 또는 토큰의 특정 값에 따라 다른 작업을 수행합니다. 이 작업은 개체, 식 또는 토큰을 평가하고, 결과와 일치하는 케이스를 선택하고, 해당 케이스에 대한 작업만 실행합니다. 결과가 일치하는 케이스가 없으면 기본 작업이 실행됩니다. switch 문이 실행되는 경우 하나의 케이스만 결과와 일치해야 합니다. [논리 앱의 switch 문](../logic-apps/logic-apps-control-flow-switch-statement.md)에 대해 자세히 알아보세요.

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| 식 | 예 | 문자열 | 평가할 개체, 식 또는 토큰 | 
| cases | 예 | JSON 개체 | 식 결과에 따라 실행되는 내부 작업 집합을 포함합니다. | 
| case | 예 | 문자열 | 결과와 일치하는 값 | 
| actions | 예 | JSON 개체 | 식 결과와 일치하는 케이스에 대해 실행되는 내부 작업 | 
| 기본값 | 아니오 | JSON 개체 | 결과와 일치하는 케이스가 없는 경우 실행되는 내부 작업 | 
||||| 

예: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Foreach 작업

이 루프 작업은 배열을 반복하고 각 배열 항목에 대해 내부 작업을 수행합니다. 기본적으로 Foreach 루프는 병렬로 실행됩니다. "for each" 루프가 실행될 수 있는 최대 병렬 주기 수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 각 주기를 순차적으로 실행하려면 `operationOptions` 매개 변수를 `Sequential`로 설정합니다. [논리 앱의 Foreach 루프](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)에 대해 자세히 알아보세요.

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| actions | 예 | JSON 개체 | 루프 내에서 실행될 내부 작업입니다. | 
| foreach | 예 | 문자열 | 반복할 배열입니다. | 
| operationOptions | 아니오 | 문자열 | 동작 사용자 지정에 대한 작업 옵션을 지정합니다. 기본 동작이 병렬인 반복을 순차적으로 실행하기 위해 현재 `Sequential`만 지원합니다. |
||||| 

예: 

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Until 작업

이 루프 작업은 조건이 true로 평가될 때까지 내부 작업을 실행합니다. [논리 앱의 "until" 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)에 대해 자세히 알아보세요.

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- | 
| actions | 예 | JSON 개체 | 루프 내에서 실행될 내부 작업입니다. | 
| 식 | 예 | 문자열 | 각 반복 후마다 평가할 식 | 
| limit | 예 | JSON 개체 | 루프에 대한 제한입니다. 하나 이상의 제한을 정의해야 합니다. | 
| count | 아니오 | 정수  | 수행할 반복 수에 대한 제한 | 
| 시간 제한 | 아니오 | 문자열 | 루프가 실행될 기간을 지정하는 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)의 시간 초과 제한입니다. |
||||| 

예: 

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>범위 작업

이 작업을 통해 워크플로에서 작업을 논리적으로 그룹화할 수 있습니다. 범위의 모든 작업에서 실행을 완료한 후에도 해당 범위에서 자체의 상태를 가져옵니다. [범위](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)에 대해 자세히 알아보세요.

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Name | 필수 | 형식 | 설명 | 
| ---- | -------- | ---- | ----------- |  
| actions | 예 | JSON 개체 | 범위 내에서 실행될 내부 작업 |
||||| 

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아봅니다.
* [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)에 대해 자세히 알아봅니다.