---
title: 워크플로 정의 언어 함수 - Azure Logic Apps | Microsoft Docs
description: 워크플로 정의 언어를 사용하여 논리 앱을 만드는 함수에 대해 알아봅니다.
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 8240ddf8a93c6589f89f3ad680c1c99c594742c9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886615"
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Azure Logic Apps에 대한 워크플로 정의 언어 함수 참조

이 문서에서는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)으로 워크플로를 만들 때 사용할 수 있는 함수를 설명합니다. 논리 앱 정의의 함수에 대한 자세한 내용은 [Azure Logic Apps에 대한 워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md#functions)를 참조하세요. 

> [!NOTE]
> 매개 변수 정의 구문에서 매개 변수 뒤에 나오는 물음표(?)는 해당 매개 변수가 선택 사항임을 의미합니다. 예를 들어 [getFutureTime()](#getFutureTime)을 참조하세요.

<a name="action"></a>

## <a name="action"></a>action

런타임 시 *현재* 작업의 출력 또는 식에 할당할 수 있는 다른 JSON 이름과 값 쌍을 반환합니다. 기본적으로 이 함수는 전체 작업 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다. [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions)도 함께 참조하세요.

이 자리에 `action()` 함수만 사용할 수 있습니다. 

* 원래 `subscribe` 요청의 결과에 액세스할 수 있도록 하는 웹후크 작업에 대한 `unsubscribe` 속성
* 작업에 대한 `trackedProperties` 속성
* 작업에 대한 `do-until` 루프 조건

```
action()
action().outputs.body.<property> 
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | 아니오 | 문자열 | 해당 값을 원하는 작업 개체의 속성에 대한 이름: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** 및 **clientTrackingId**. Azure Portal에서는 특정 실행 기록을 검토하여 이러한 속성을 찾을 수 있습니다. 자세한 내용은 [REST API - 워크플로 실행 작업](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)을 참조하세요. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*action-output*> | 문자열 | 현재 작업 또는 속성의 출력 | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

런타임 시 작업의 `body` 출력을 반환합니다. `actions('<actionName>').outputs.body`를 줄인 표현입니다. [body()](#body) 및 [actions()](#actions)를 참조하세요.

```
actionBody('<actionName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 원하는 작업의 `body` 출력에 대한 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*action-body-output*> | 문자열 | 지정한 작업의 `body` 출력 | 
|||| 

*예제*

이 예제는 Twitter 작업 `Get user`에서 `body` 출력을 가져옵니다. 

```
actionBody('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

## <a name="actionoutputs"></a>actionOutputs

런타임 시 작업의 출력을 반환합니다. `actions('<actionName>').outputs`를 줄인 표현입니다. [actions()](#actions)를 참조하세요.

```
actionOutputs('<actionName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 원하는 작업의 출력에 대한 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*output*> | 문자열 | 지정한 작업의 출력 | 
|||| 

*예제*

이 예제는 Twitter 작업 `Get user`에서 출력을 가져옵니다. 

```
actionOutputs('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
{ 
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

## <a name="actions"></a>actions

런타임 시 작업의 출력 또는 식에 할당할 수 있는 다른 JSON 이름-값 쌍을 반환합니다. 기본적으로 이 함수는 전체 작업 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다. 줄인 표현에 대해서는 [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) 및 [body()](#body)를 참조하세요. 현재 작업에 대해서는 [action()](#action)을 참조하세요.

> [!NOTE] 
> 이전에는 한 작업이 다른 작업의 출력을 기반으로 실행했음을 지정할 때 `actions()` 함수 또는 `conditions` 요소를 사용할 수 있었습니다. 그러나 작업 간의 종속성을 명시적으로 선언하려면 이제 종속 작업의 `runAfter` 속성을 사용해야 합니다. `runAfter` 속성에 대해 자세히 알아보려면 [runAfter 속성으로 실패 파악 및 처리](../logic-apps/logic-apps-workflow-definition-language.md)를 참조하세요.

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 해당 출력을 원하는 작업 개체의 이름  | 
| <*property*> | 아니오 | 문자열 | 해당 값을 원하는 작업 개체의 속성에 대한 이름: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** 및 **clientTrackingId**. Azure Portal에서는 특정 실행 기록을 검토하여 이러한 속성을 찾을 수 있습니다. 자세한 내용은 [REST API - 워크플로 실행 작업](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)을 참조하세요. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*action-output*> | 문자열 | 지정한 작업 또는 속성의 출력 | 
|||| 

*예제*

이 예제는 런타임 시 Twitter 작업 `Get user`에서 `status` 속성 값을 가져옵니다. 

```
actions('Get_user').outputs.body.status 
```

그리고 다음 결과를 반환합니다. `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>추가

두 숫자를 더한 결과를 반환합니다.

```
add(<summand_1>, <summand_2>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | 예 | 정수, 부동 소수점 수 또는 혼합 | 더할 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*result-sum*> | 정수 또는 부동 소수점 수 | 지정한 숫자들을 더한 결과 | 
|||| 

*예제*

이 예제는 지정한 숫자들을 더합니다.

```
add(1, 1.5)
```

그리고 다음 결과를 반환합니다. `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>addDays

타임스탬프에 일 수를 더합니다.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*days*> | 예 | 정수  | 더할 양수 또는 음수 일 수 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 + 지정한 일 수  | 
|||| 

*예 1*

이 예제는 지정한 타임스탬프에 10일을 더합니다.

```
addDays('2018-03-15T13:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-25T00:00:0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5일을 뺍니다.

```
addDays('2018-03-15T00:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

타임스탬프에 시간 수를 더합니다.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*hours*> | 예 | 정수  | 더할 양수 또는 음수 시간 수 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 + 지정한 시간 수  | 
|||| 

*예 1*

이 예제는 지정한 타임스탬프에 10시간을 더합니다.

```
addHours('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5시간을 뺍니다.

```
addHours('2018-03-15T15:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

타임스탬프에 분 수를 더합니다.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*minutes*> | 예 | 정수  | 더할 양수 또는 음수 분 수 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 + 지정한 분 수 | 
|||| 

*예 1*

이 예제는 지정한 타임스탬프에 10분을 더합니다.

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:20:00.0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5분을 뺍니다.

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

JSON 개체에 속성 및 해당 값 또는 이름-값 쌍을 추가하고 업데이트된 개체를 반환합니다. 런타임 시 개체가 이미 있는 경우 함수에서 오류가 발생합니다.

```
addProperty(<object>, '<property>', <value>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | 예 | Object | 속성을 추가할 JSON 개체 | 
| <*property*> | 예 | 문자열 | 추가할 속성의 이름 | 
| <*value*> | 예 | 모두 | 속성의 값 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | 지정한 속성으로 업데이트된 JSON 개체 | 
|||| 

*예제*

이 예제는 `accountNumber` 속성을 `customerProfile` 개체에 추가한 다음,[JSON()](#json) 함수를 사용하여 JSON 개체로 변환합니다. 이 함수는 [guid()](#guid) 함수에서 생성된 값을 할당하고 업데이트된 개체를 반환합니다.

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>addSeconds

타임스탬프에 초 수를 더합니다.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*seconds*> | 예 | 정수  | 더할 양수 또는 음수 초 수 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 + 지정한 초 수  | 
|||| 

*예 1*

이 예제는 지정한 타임스탬프에 10초를 더합니다.

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:10.0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5초를 뺍니다.

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

타임스탬프에 시간 단위 수를 더합니다. [getFutureTime()](#getFutureTime)도 함께 참조하세요.

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*interval*> | 예 | 정수  | 더할 지정한 시간 단위 수 | 
| <*timeUnit*> | 예 | 문자열 | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 + 지정한 시간 단위 수  | 
|||| 

*예 1*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

그리고 다음 결과를 반환합니다. `"2018-01-02T00:00:00:0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

그리고 선택적 "D"를 사용하여 다음 결과를 반환합니다. `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>and

모든 식이 true인지 확인합니다. 모든 식이 true이면 true를 반환하거나 식 중의 적어도 개가 false이면 false를 반환합니다.

```
and(<expression1>, <expression2>, ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>, ... | 예 | BOOLEAN | 검사할 식 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| true 또는 false | BOOLEAN | 모든 식이 true이면 true를 반환합니다. 식 중의 적어도 한 개가 false이면 false를 반환합니다. | 
|||| 

*예 1*

아래 예제는 지정한 부울 값이 모두 true인지 검사합니다.

```
and(true, true)
and(false, true)
and(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다. 
* 두 번째 예제: 식 한 개가 false이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

*예 2*

아래 예제는 지정한 식이 모두 true인지 검사합니다.

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다. 
* 두 번째 예제: 식 한 개가 false이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

<a name="array"></a>

## <a name="array"></a>array

단일 지정 입력에서 배열을 반환합니다. 여러 입력의 경우 [createArray()](#createArray)를 참조하세요. 

```
array('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 배열을 만드는 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*value*>] | 배열 | 단일 지정 입력을 포함하는 배열 | 
|||| 

*예제*

이 예제는 "hello" 문자열에서 배열을 만듭니다.

```
array('hello')
```

그리고 다음 결과를 반환합니다. `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>base64

문자열에 대한 base64로 인코딩된 버전을 반환합니다.

```
base64('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 입력 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*base64-string*> | 문자열 | 문자열에 대한 base64로 인코딩된 버전 | 
|||| 

*예제*

이 예제는 "hello" 문자열을 base64로 인코딩된 문자열로 변환합니다.

```
base64('hello')
```

그리고 다음 결과를 반환합니다. `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다.

```
base64ToBinary('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 base64로 인코딩된 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*binary-for-base64-string*> | 문자열 | Base64로 인코딩된 문자열에 대한 이진 버전 | 
|||| 

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 이진 문자열로 변환합니다.

```
base64ToBinary('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

base64로 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 base64 문자열을 디코딩합니다. [decodeBase64()](#decodeBase64) 대신에 이 함수를 사용합니다. 두 함수 모두 같은 방법으로 작동하지만 `base64ToString()`를 사용하는 것이 좋습니다.

```
base64ToString('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 디코딩할 base64로 인코딩된 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*decoded-base64-string*> | 문자열 | Base64로 인코딩된 문자열에 대한 문자열 버전 | 
|||| 

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 단순한 문자열로 변환합니다.

```
base64ToString('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="binary"></a>

## <a name="binary"></a>binary 

문자열에 대한 이진 버전을 반환합니다.

```
binary('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*binary-for-input-value*> | 문자열 | 지정한 문자열에 대한 이진 버전 | 
|||| 

*예제*

이 예제는 "hello" 문자열을 이진 문자열로 변환합니다.

```
binary('hello')
```

그리고 다음 결과를 반환합니다. 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>본문

런타임 시 작업의 `body` 출력을 반환합니다. `actions('<actionName>').outputs.body`를 줄인 표현입니다. [actionBody()](#actionBody) 및 [actions()](#actions)를 참조하세요.

```
body('<actionName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 원하는 작업의 `body` 출력에 대한 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | -----| ----------- | 
| <*action-body-output*> | 문자열 | 지정한 작업의 `body` 출력 | 
|||| 

*예제*

이 예제는 `Get user` Twitter 작업 `body`에서 출력을 가져옵니다. 

```
body('Get_user')
```

그리고 다음 결과를 반환합니다. 

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

## <a name="bool"></a>bool

값에 대한 부울 버전을 반환합니다.

```
bool(<value>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 모두 | 변환할 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 지정한 값에 대한 부울 버전 | 
|||| 

*예제*

아래 예제는 지정한 값을 부울 값으로 변환합니다. 

```
bool(1)
bool(0)
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true` 
* 두 번째 예제: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>coalesce

하나 이상의 매개 변수에서 Null이 아닌 첫 번째 값을 반환합니다. 빈 문자열, 빈 배열 및 빈 개체는 null이 아닙니다.

```
coalesce(<object_1>, <object_2>, ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>, ... | 예 | Any, 형식을 혼합할 수 있음 | Null인지 검사할 하나 이상의 항목 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*first-non-null-item*> | 모두 | Null이 아닌 첫 번째 항목 또는 값입니다. 모든 매개 변수가 Null이면 이 함수는 Null을 반환합니다. | 
|||| 

*예제*

아래 예제는 지정한 값에서 Null이 아닌 첫 번째 값 또는 모든 값이 Null인 경우 Null을 반환합니다.

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true` 
* 두 번째 예제: `"hello"`
* 세 번째 예제: `null`

<a name="concat"></a>

## <a name="concat"></a>concat

둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다. 

```
concat('<text1>', '<text2>', ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text1*>, <*text2*>, ... | 예 | 문자열 | 결합할 적어도 두 개의 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | 문자열 | 결합된 입력 문자열에서 만든 문자열 | 
|||| 

*예제*

이 예제는 문자열 "Hello"와 "World"를 결합합니다.

```
concat('Hello', 'World')
```

그리고 다음 결과를 반환합니다. `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>contains

컬렉션에 특정 항목이 있는지 검사합니다. 항목이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다. 이 함수는 대/소문자를 구분합니다.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

특히 이 함수는 아래 컬렉션 형식에서 작동합니다. 

* *부분 문자열*을 찾기 위한 *문자열*
* *값*을 찾기 위한 *배열*
* *키*를 찾기 위한 *사전*

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열, 배열 또는 사전 | 검사할 컬렉션 | 
| <*value*> | 예 | 각각 문자열, 배열 또는 사전 | 찾을 항목 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 항목이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
|||| 

*예 1*

이 예제는 부분 문자열 "world"에 대한 문자열 "hello world"를 검사하고 true를 반환합니다.

```
contains('hello world', 'world')
```

*예 2*

이 예제는 부분 문자열 "universe"에 대한 문자열 "hello world"를 검사하고 false를 반환합니다.

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

UTC(Universal Time Coordinated)의 타임스탬프를 대상 표준 시간대로 변환합니다.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*destinationTimeZone*> | 예 | 문자열 | 대상 표준 시간대의 이름입니다. 자세한 내용은 [표준 시간대 ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))를 참조하세요. | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | 문자열 | 대상 표준 시간대로 변환된 타임스탬프 | 
|||| 

*예 1*

이 예제는 타임스탬프를 지정한 표준 시간대로 변환합니다. 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예 2*

이 예제는 타임스탬프를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

원본 표준 시간대의 타임스탬프를 대상 표준 시간대로 변환합니다.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*sourceTimeZone*> | 예 | 문자열 | 원본 표준 시간대의 이름입니다. 자세한 내용은 [표준 시간대 ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))를 참조하세요. | 
| <*destinationTimeZone*> | 예 | 문자열 | 대상 표준 시간대의 이름입니다. 자세한 내용은 [표준 시간대 ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))를 참조하세요. | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | 문자열 | 대상 표준 시간대로 변환된 타임스탬프 | 
|||| 

*예 1*

이 예제는 원본 표준 시간대를 대상 표준 시간대로 변환합니다. 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예 2*

이 예제는 표준 시간대를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

원본 표준 시간대의 타임스탬프를 UTC(Universal Time Coordinated)로 변환합니다.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*sourceTimeZone*> | 예 | 문자열 | 원본 표준 시간대의 이름입니다. 자세한 내용은 [표준 시간대 ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80))를 참조하세요. | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*converted-timestamp*> | 문자열 | UTC로 변환된 타임스탬프 | 
|||| 

*예 1*

이 예제는 타임스탬프를 UTC로 변환합니다. 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T08:00:00.0000000Z"`

*예 2*

이 예제는 타임스탬프를 UTC로 변환합니다.

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

여러 입력에서 배열을 반환합니다. 단일 입력 배열의 경우 [array()](#array)를 참조하세요.

```
createArray('<object1>', '<object2>', ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*>, ... | 예 | 모두, 그러나 혼합은 안 됨 | 배열을 만들 적어도 두 개의 항목 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*object1*>, <*object2*>, ...] | 배열 | 모든 입력 항목에서 만든 배열 | 
|||| 

*예제*

이 예제는 다음 입력에서 배열을 만듭니다.

```
createArray('h', 'e', 'l', 'l', 'o')
```

그리고 다음 결과를 반환합니다. `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

문자열에 대한 데이터 URI(Uniform Resource Identifier)를 반환합니다. 

```
dataUri('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*data-uri*> | 문자열 | 입력 문자열에 대한 데이터 URI | 
|||| 

*예제*

이 예제는 "hello" 문자열에 대한 데이터 URI를 만듭니다.

```
dataUri('hello') 
```

그리고 다음 결과를 반환합니다. `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다. [decodeDataUri()](#decodeDataUri) 대신에 이 함수를 사용합니다. 두 함수 모두 같은 방법으로 작동하지만 `decodeDataUri()`를 사용하는 것이 좋습니다.

```
dataUriToBinary('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 데이터 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*binary-for-data-uri*> | 문자열 | 데이터 URI에 대한 이진 버전 | 
|||| 

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 만듭니다.

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다. 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

데이터 URI(Uniform Resource Identifier)에 대한 문자열 버전을 반환합니다.

```
dataUriToString('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 데이터 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*string-for-data-uri*> | 문자열 | 데이터 URI에 대한 문자열 버전 | 
|||| 

*예제*

이 예제는 이 데이터 URI에 대한 문자열을 만듭니다.

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>dayOfMonth

타임스탬프에서 월의 날짜를 반환합니다. 

```
dayOfMonth('<timestamp>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*day-of-month*> | 정수  | 지정한 타임스탬프의 월의 날짜 | 
|||| 

*예제*

이 예제는 이 타임스탬프에서 월의 날짜에 대한 숫자를 반환합니다.

```
dayOfMonth('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>dayOfWeek

타임스탬프에서 요일을 반환합니다.  

```
dayOfWeek('<timestamp>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*day-of-week*> | 정수  | 일요일이 0, 월요일이 1이라는 식으로 지정한 타임스탬프의 요일 | 
|||| 

*예제*

이 예제는 이 타임스탬프에서 요일에 대한 숫자를 반환합니다.

```
dayOfWeek('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>dayOfYear

타임스탬프에서 년의 날짜를 반환합니다. 

```
dayOfYear('<timestamp>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*day-of-year*> | 정수  | 지정한 타임스탬프의 년의 날짜 | 
|||| 

*예제*

이 예제는 이 타임스탬프에서 년의 날짜인 숫자를 반환합니다.

```
dayOfYear('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

base64로 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 base64 문자열을 디코딩합니다. `decodeBase64()` 대신에 [base64ToString()](#base64ToString)의 사용을 검토해 보세요. 두 함수 모두 같은 방법으로 작동하지만 `base64ToString()`를 사용하는 것이 좋습니다.

```
decodeBase64('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 디코딩할 base64로 인코딩된 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*decoded-base64-string*> | 문자열 | Base64로 인코딩된 문자열에 대한 문자열 버전 | 
|||| 

*예제*

이 예제는 base64로 인코딩된 문자열에 대한 문자열을 만듭니다.

```
decodeBase64('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다. `decodeDataUri()` 대신에 [dataUriToBinary()](#dataUriToBinary)의 사용을 검토해 보세요. 두 함수 모두 같은 방법으로 작동하지만 `dataUriToBinary()`를 사용하는 것이 좋습니다.

```
decodeDataUri('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 디코딩할 데이터 URI 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*binary-for-data-uri*> | 문자열 | 데이터 URI 문자열에 대한 이진 버전 | 
|||| 

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 반환합니다.

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다. 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeUriComponent

디코딩된 버전으로 이스케이프 문자를 바꾸는 문자열을 반환합니다. 

```
decodeUriComponent('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 디코딩할 이스케이프 문자를 포함한 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*decoded-uri*> | 문자열 | 디코딩된 이스케이프 문자로 업데이트된 문자열 | 
|||| 

*예제*

이 예제는 이 문자열의 이스케이프 문자를 디코딩된 버전으로 바꿉니다.

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

두 숫자를 나눈 정수 결과를 반환합니다. 나머지 결과를 얻으려면 [mod()](#mod)를 참조하세요.

```
div(<dividend>, <divisor>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | 예 | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 | 
| <*divisor*> | 예 | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 수, 그러나 0이 올 수 없음 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*quotient-result*> | 정수  | 첫 번째 숫자를 두 번째 숫자로 나눈 정수 결과 | 
|||| 

*예제*

두 예제 모두 첫 번째 숫자를 두 번째 숫자로 나눕니다.

```
div(10, 5)
div(11, 5)
```

그리고 다음 결과를 반환합니다. `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeUriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다. `encodeUriComponent()` 대신에 [uriComponent()](#uriComponent)의 사용을 검토해 보세요. 두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`를 사용하는 것이 좋습니다.

```
encodeUriComponent('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | URI 인코딩된 형식으로 변환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*encoded-uri*> | 문자열 | 이스케이프 문자로 URI 인코딩된 문자열 | 
|||| 

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
encodeUriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>empty

컬렉션이 비어 있는지 검사합니다. 컬렉션이 비어 있으면 true를 반환하거나 비어 있지 않으면 false를 반환합니다.

```
empty('<collection>')
empty([<collection>])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열, 배열 또는 개체 | 검사할 컬렉션 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 컬렉션이 비어 있으면 true를 반환합니다. 비어 있지 않으면 false를 반환합니다. | 
|||| 

*예제* 

아래 예제는 지정한 컬렉션이 비어 있는지 검사합니다.

```
empty('')
empty('abc')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: 비어 있는 문자열을 전달하므로 함수는 `true`를 반환합니다. 
* 두 번째 예제: 문자열 "abc"를 전달하므로 함수는 `false`를 반환합니다. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

문자열이 특정 하위 문자열로 종료하는지 검사합니다. 하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다. 이 함수는 대/소문자를 구분하지 않습니다.

```
endsWith('<text>', '<searchText>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 검사할 문자열 | 
| <*searchText*> | 예 | 문자열 | 찾을 끝나는 하위 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false  | BOOLEAN | 끝나는 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. | 
|||| 

*예 1* 

이 예제는 "hellow world" 문자열이 "world" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `true`

*예 2*

이 예제는 "hellow world" 문자열이 "universe" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'universe')
```

그리고 다음 결과를 반환합니다. `false`

<a name="equals"></a>

## <a name="equals"></a>equals

두 값, 식 또는 개체가 모두 같은지 검사합니다. 둘이 다 같으면 true를 반환하거나 같지 않으면 false를 반환합니다.

```
equals('<object1>', '<object2>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object1*>, <*object2*> | 예 | 다양 | 비교할 값, 식 또는 개체 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 둘이 같으면 true를 반환합니다. 같지 않으면 false를 반환합니다. | 
|||| 

*예제*

아래 예제는 지정한 입력들이 같은지 검사합니다. 

```
equals(true, 1)
equals('abc', 'abcd')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: 두 값이 모두 같으므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 값이 같지 않으므로 함수는 `false`를 반환합니다.

<a name="first"></a>

## <a name="first"></a>first

문자열 또는 배열에서 첫 번째 항목을 반환합니다.

```
first('<collection>')
first([<collection>])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열 또는 배열 | 첫 번째 항목을 찾을 컬렉션 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*first-collection-item*> | 모두 | 컬렉션의 첫 번째 항목 | 
|||| 

*예제*

아래 예제는 이러한 컬렉션의 첫 번째 항목을 찾습니다.

```
first('hello')
first([0, 1, 2])
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `"h"`
* 두 번째 예제: `0`

<a name="float"></a>

## <a name="float"></a>float

부동 소수점 수에 대한 문자열 버전을 실제 부동 소수점 수로 변환합니다. 이 함수는 논리 앱과 같은 앱에 사용자 지정 매개 변수를 전달할 경우에만 사용할 수 있습니다.

```
float('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 유효한 부동 소수점 수를 가진 문자열 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*float-value*> | Float | 지정한 문자열에 대한 부동 소수점 수 | 
|||| 

*예제*

이 예제는 이 부동 소수점 수에 대한 문자열 버전을 만듭니다.

```
float('10.333')
```

그리고 다음 결과를 반환합니다. `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>formatDateTime

지정한 형식의 타임스탬프를 반환합니다.

```
formatDateTime('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*reformatted-timestamp*> | 문자열 | 지정한 형식으로 업데이트된 타임스탬프 | 
|||| 

*예제*

이 예제는 타임스탬프를 지정한 형식으로 변환합니다.

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

작업의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 값을 가진 배열을 반환합니다. 

```
formDataMultiValues('<actionName>', '<key>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 해당 출력이 원하는 키를 가진 작업 | 
| <*key*> | 예 | 문자열 | 해당 값을 원하는 키의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | 배열 | 지정한 키와 일치하는 모든 값을 가진 배열 | 
|||| 

*예제* 

이 예제는 지정한 작업의 양식 데이터 또는 양식 인코딩된 출력의 "제목" 키의 값에서 배열을 만듭니다.  

```
formDataMultiValues('Send_an_email', 'Subject')
```

그리고 배열의 제목 텍스트를 반환, 예: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

작업의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 단일 값을 반환합니다. 함수가 일치하는 항목을 두 개 이상 찾으면 함수에서 오류가 발생합니다.

```
formDataValue('<actionName>', '<key>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 해당 출력이 원하는 키를 가진 작업 | 
| <*key*> | 예 | 문자열 | 해당 값을 원하는 키의 이름 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*key-value*> | 문자열 | 지정한 키의 값  | 
|||| 

*예제* 

이 예제는 지정한 작업의 양식 데이터 또는 양식 인코딩된 출력의 "제목" 키의 값에서 문자열을 만듭니다.  

```
formDataValue('Send_an_email', 'Subject')
```

그리고 제목 텍스트를 문자열로 반환, 예: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

지정한 시간 단위를 더한 현재 타임스탬프를 반환합니다.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | 예 | 정수  | 뺄 지정한 시간 단위 수 | 
| <*timeUnit*> | 예 | 문자열 | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 현재 타임스탬프 + 지정한 시간 단위 수 | 
|||| 

*예 1*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다. 이 예제는 해당 타임스탬프에 5일을 더합니다.

```
getFutureTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-03-06T00:00:00.0000000Z"`

*예 2*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다. 이 예제는 5일을 더하고 결과를 "D" 형식으로 변환합니다.

```
getFutureTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

지정한 시간 단위를 뺀 현재 타임스탬프를 반환합니다.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | 예 | 정수  | 뺄 지정한 시간 단위 수 | 
| <*timeUnit*> | 예 | 문자열 | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 현재 타임스탬프 - 지정한 시간 단위 수 | 
|||| 

*예 1*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다. 이 예제는 해당 타임스탬프에서 5일을 뺍니다.

```
getPastTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-27T00:00:00.0000000Z"`

*예 2*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다. 이 예제는 5일을 빼고 결과를 "D" 형식으로 변환합니다.

```
getPastTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>greater

첫 번째 값이 두 번째 값보다 큰지 검사합니다. 첫 번째 값이 더 크면 true를 반환하거나 더 작으면 false를 반환합니다.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 큰지 검사할 첫 번째 값 | 
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 첫 번째 값이 두 번째 값보다 크면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작거나 같으면 false를 반환합니다. | 
|||| 

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 큰지 검사합니다.

```
greater(10, 5)
greater('apple', 'banana')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다.
첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환하거나 첫 번째 값이 더 작으면 false를 반환합니다.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 크거나 같은지 검사할 첫 번째 값 | 
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작으면 false를 반환합니다. | 
|||| 

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다.

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="guid"></a>

## <a name="guid"></a>GUID

GUID(전역적으로 고유한 식별자)를 문자열로 생성합니다. 예, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce": 

```
guid()
```

또한 GUID에 대해 하이픈으로 구분된 32자리인 기본 형식 "D" 이외의 다른 형식을 지정할 수 있습니다.

```
guid('<format>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | 아니오 | 문자열 | 반환되는 GUID에 대한 단일 [형식 지정자](https://msdn.microsoft.com/library/97af8hh4)입니다. 기본적으로 형식은 "D"이지만 "N", "D", "B", "P" 또는 "X"를 사용할 수 있습니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*GUID-value*> | 문자열 | 임의로 생성된 GUID | 
|||| 

*예제* 

이 예제는 같은 GUID를 생성하되, 하이픈으로 구분되고 소괄호로 둘러싼 32자리로 생성합니다. 

```
guid('P')
```

그리고 다음 결과를 반환합니다. `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>if

식이 true인지 또는 false인지 검사합니다. 결과에 기반해 지정한 값을 반환합니다.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | 예 | BOOLEAN | 검사할 식 | 
| <*valueIfTrue*> | 예 | 모두 | 식이 true이면 반환할 값 | 
| <*valueIfFalse*> | 예 | 모두 | 식이 false이면 반환할 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*specified-return-value*> | 모두 | 식이 true인지 또는 false인지에 기반해 반환하는 지정한 값 | 
|||| 

*예제* 

이 예제는 지정한 식이 true를 반환하므로 `"yes"`를 반환합니다. 그렇지 않으면 이 예제는 `"no"`를 반환합니다.

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexof

하위 문자열에 대한 시작 위치 또는 인덱스 값을 반환합니다. 이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다. 

```
indexOf('<text>', '<searchText>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 찾을 하위 문자열을 가진 문자열 | 
| <*searchText*> | 예 | 문자열 | 찾을 하위 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*index-value*>| 정수  | 지정한 하위 문자열에 대한 시작 위치 또는 인덱스 값 <p>문자열을 찾을 수 없는 경우 -1을 반환합니다. </br>문자열이 비어 있으면 0을 반환합니다. | 
|||| 

*예제* 

이 예제는 "hello world" 문자열의 "world" 하위 문자열에 대한 시작 인덱스 값을 찾습니다.

```
indexOf('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `6`

<a name="int"></a>

## <a name="int"></a>int

문자열에 대한 정수 버전을 반환합니다.

```
int('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*integer-result*> | 정수  | 지정한 문자열에 대한 정수 버전 | 
|||| 

*예제* 

이 예제는 문자열 "10"에 대한 정수 버전을 만듭니다.

```
int('10')
```

그리고 다음 결과를 반환합니다. `10`

<a name="item"></a>

## <a name="item"></a>항목

배열에 대해 반복 작업 내에 사용하는 경우 작업의 현재 반복 중에 배열의 현재 항목을 반환합니다. 또한 해당 항목의 속성에서 값을 가져올 수 있습니다. 

```
item()
```

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*current-array-item*> | 모두 | 작업의 현재 반복에 대한 배열의 현재 항목 | 
|||| 

*예제* 

이 예제는 for-each 루프의 현재 반복 내에 있는 "Send_an_email" 작업에 대한 현재 메시지에서 `body`요소를 가져옵니다.

```
item().body
```

<a name="items"></a>

## <a name="items"></a>항목

for-each 루프의 각 주기에서 현재 항목을 반환합니다. 이 함수를 for-each 루프 내에 사용합니다.

```
items('<loopName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | 예 | 문자열 | for-each 루프의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*item*> | 모두 | 지정한 for-each 루프의 현재 주기의 항목 | 
|||| 

*예제* 

이 예제는 지정한 for-each 루프에서 현재 항목을 가져옵니다.

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>json :

JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다.

```
json('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 또는 XML | 변환할 문자열 또는 XML | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*JSON-result*> | JSON 원시 형식 또는 개체 | JSON 고유 형식 값 또는 지정한 문자열이나 XML에 대한 개체입니다. 문자열이 Null이면, 함수는 빈 개체를 반환합니다. | 
|||| 

*예 1* 

이 예제는 이 문자열을 JSON 값으로 변환합니다.

```
json('[1, 2, 3]')
```

그리고 다음 결과를 반환합니다. `[1, 2, 3]`

*예 2*

이 예제는 이 문자열을 JSON으로 변환합니다. 

```
json('{"fullName": "Sophia Owen"}')
```

그리고 다음 결과를 반환합니다.

```
{
  "fullName": "Sophia Owen"
}
```

*예 3*

이 예제는 이 XML을 JSON으로 변환합니다. 

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

그리고 다음 결과를 반환합니다.

```json
{ 
   "?xml": { "@version": "1.0" }, 
   "root": { 
      "person": [ { 
         "@id": "1", 
         "name": "Sophia Owen", 
         "occupation": "Engineer" 
       } ] 
   } 
}
```

<a name="intersection"></a>

## <a name="intersection"></a>교집합

지정한 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다. 결과에 나타내려면 항목이 이 함수에 전달된 모든 컬렉션에 나타나야 합니다. 같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>, ... | 예 | 배열 또는 개체, 그러나 둘 다는 아님 | 공통 항목*만* 원하는 컬렉션 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*common-items*> | 각각 배열 또는 개체 | 지정한 컬렉션에 대해 공통 항목만 있는 컬렉션 | 
|||| 

*예제* 

이 예제는 이러한 배열에 대한 공통 항목을 찾습니다.  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

그리고 다음 항목*만* 가진 배열을 반환합니다. `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

배열의 모든 항목을 포함하고 각 문자가 *구분 기호*로 구분된 문자열을 반환합니다.

```
join([<collection>], '<delimiter>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 배열 | 조인할 항목이 있는 배열 |  
| <*delimiter*> | 예 | 문자열 | 결과 문자열의 각 문자 사이에 나타나는 구분 기호 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*char1*><*delimiter*><*char2*><*delimiter*>... | 문자열 | 지정한 배열의 모든 항목에서 만든 결과 문자열 |
|||| 

*예제* 

이 예제는 지정한 문자를 구분 기호로 하여 이 배열의 모든 항목에서 문자열을 만듭니다.

```
join([a, b, c], '.')
```

그리고 다음 결과를 반환합니다. `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>last

컬렉션에서 마지막 항목을 반환합니다.

```
last('<collection>')
last([<collection>])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열 또는 배열 | 마지막 항목을 찾을 컬렉션 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*last-collection-item*> | 각각 문자열 또는 배열 | 컬렉션의 마지막 항목 | 
|||| 

*예제* 

아래 예제는 이러한 컬렉션의 마지막 항목을 찾습니다.

```
last('abcd')
last([0, 1, 2, 3])
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `"d"`
* 두 번째 예제: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastindexof

하위 문자열에 대한 종료 위치 또는 인덱스 값을 반환합니다. 이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다.

```
lastIndexOf('<text>', '<searchText>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 찾을 하위 문자열을 가진 문자열 | 
| <*searchText*> | 예 | 문자열 | 찾을 하위 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*ending-index-value*> | 정수  | 지정한 하위 문자열에 대한 종료 위치 또는 인덱스 값입니다. <p>문자열을 찾을 수 없는 경우 -1을 반환합니다. </br>문자열이 비어 있으면 0을 반환합니다. | 
|||| 

*예제* 

이 예제는 "hello world" 문자열의 "world" 하위 문자열에 대한 종료 인덱스 값을 찾습니다.

```
lastIndexOf('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `10`

<a name="length"></a>

## <a name="length"></a>length

컬렉션의 항목 수를 반환합니다.

```
length('<collection>')
length([<collection>])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열 또는 배열 | 개수를 셀 항목을 포함하는 컬렉션 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*length-or-count*> | 정수  | 컬렉션의 항목 수 | 
|||| 

*예제*

아래 예제는 이러한 컬렉션의 항목 수를 셉니다. 

```
length('abcd')
length([0, 1, 2, 3])
```

그리고 다음 결과를 반환합니다. `4`

<a name="less"></a>

## <a name="less"></a>less

첫 번째 값이 두 번째 값보다 작은지 검사합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작은지 검사할 첫 번째 값 | 
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 첫 번째 값이 두 번째 값보다 작으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크거나 같으면 false를 반환합니다. | 
|||| 

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작은지 검사합니다.

```
less(5, 10)
less('banana', 'apple')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작거나 같은지 검사할 첫 번째 값 | 
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false  | BOOLEAN | 첫 번째 값이 두 번째 값보다 작거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크면 false를 반환합니다. |  
|||| 

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

그리고 다음 결과를 반환합니다. 

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

트리거 또는 작업을 호출하는 "콜백 URL"을 반환합니다. 이 함수는 **HttpWebhook** 및 **ApiConnectionWebhook** 커넥터 형식에 대해서만 작동하며, **Manual**, **Recurrence**, **HTTP** 및 **APIConnection** 형식에 대해서는 작동하지 않습니다. 

```
listCallbackUrl()
```

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*callback-URL*> | 문자열 | 트리거 또는 작업에 대한 콜백 URL |  
|||| 

*예제*

이 예제는 이 함수가 반환할 수 있는 샘플 콜백 URL을 보여 줍니다.

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

양끝이 포함된 숫자를 가진 목록 또는 배열에서 가장 높은 값을 반환합니다. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>, ... | 예 | 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 집합 | 
| [<*number1*>, <*number2*>, ...] | 예 | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 배열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*max-value*> | 정수 또는 부동 소수점 수 | 지정한 배열 또는 숫자 집합의 가장 높은 값 | 
|||| 

*예제* 

아래 예제는 숫자 집합 및 배열에서 가장 높은 값을 가져옵니다.

```
max(1, 2, 3)
max([1, 2, 3])
```

그리고 다음 결과를 반환합니다. `3`

<a name="min"></a>

## <a name="min"></a>min

숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*number1*>, <*number2*>, ... | 예 | 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 집합 | 
| [<*number1*>, <*number2*>, ...] | 예 | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 배열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*min-value*> | 정수 또는 부동 소수점 수 | 지정한 숫자 집합 또는 지정한 배열의 가장 낮은 값 | 
|||| 

*예제* 

아래 예제는 숫자 집합 및 배열에서 가장 낮은 값을 가져옵니다.

```
min(1, 2, 3)
min([1, 2, 3])
```

그리고 다음 결과를 반환합니다. `1`

<a name="mod"></a>

## <a name="mod"></a>mod

두 숫자를 나눈 나머지를 반환합니다. 정수 결과를 가져오는 방법은 [div()](#div)를 참조하세요.

```
mod(<dividend>, <divisor>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*dividend*> | 예 | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 | 
| <*divisor*> | 예 | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 숫자, 그러나 0이 올 수 없습니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*modulo-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 나눈 나머지 | 
|||| 

*예제* 

이 예제는 첫 번째 숫자를 두 번째 숫자로 나눕니다.

```
mod(3, 2)
```

그리고 다음 결과를 반환합니다. `1`

<a name="mul"></a>

## <a name="mul"></a>mul

두 숫자를 곱한 결과를 반환합니다.

```
mul(<multiplicand1>, <multiplicand2>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | 예 | 정수 또는 부동 소수점 수 | *multiplicand2*로 곱할 숫자 | 
| <*multiplicand2*> | 예 | 정수 또는 부동 소수점 수 | *multiplicand1*를 곱하는 숫자 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*product-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 곱한 결과 | 
|||| 

*예제* 

아래 예제는 첫 번째 숫자를 두 번째 숫자로 곱합니다.

```
mul(1, 2)
mul(1.5, 2)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `2`
* 두 번째 예제 `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

여러 부분으로 구성된 작업의 출력에서 특정 부분에 대한 본문을 반환합니다.

```
multipartBody('<actionName>', <index>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*actionName*> | 예 | 문자열 | 여러 부분으로 구성된 출력을 가진 작업의 이름 | 
| <*index*> | 예 | 정수  | 원하는 부분에 대한 인덱스 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*body*> | 문자열 | 지정한 부분에 대한 본문 | 
|||| 

<a name="not"></a>

## <a name="not"></a>not

식이 false인지 검사합니다. 식이 false이면 true를 반환하거나 true이면 false를 반환합니다.

```
not(<expression>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*expression*> | 예 | BOOLEAN | 검사할 식 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 식이 false이면 true를 반환합니다. 식이 true이면 false를 반환합니다. |  
|||| 

*예 1*

아래 예제는 지정한 식이 모두 false인지 검사합니다. 

```
not(false)
not(true)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

*예 2*

아래 예제는 지정한 식이 모두 false인지 검사합니다. 

```
not(equals(1, 2))
not(equals(1, 1))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

<a name="or"></a>

## <a name="or"></a>또는

최소 하나의 식이 true인지 검사합니다. 최소 하나의 식이 true이면 true를 반환하거나 모든 식이 false이면 false를 반환합니다.

```
or(<expression1>, <expression2>, ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*expression1*>, <*expression2*>, ... | 예 | BOOLEAN | 검사할 식 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false | BOOLEAN | 최소 하나의 식이 true이면 true를 반환합니다. 모든 식이 false이면 false를 반환합니다. |  
|||| 

*예 1*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```
or(true, false)
or(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 하나의 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

*예 2*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 하나의 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

<a name="parameters"></a>

## <a name="parameters"></a>매개 변수

논리 앱 정의에 설명된 매개 변수에 대한 값을 반환합니다. 

```
parameters('<parameterName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*parameterName*> | 예 | 문자열 | 해당 값을 원하는 매개 변수의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*parameter-value*> | 모두 | 지정한 매개 변수의 값 | 
|||| 

*예제* 

이 JSON 값을 가지고 있다고 가정합니다.

```json
{
  "fullName": "Sophia Owen"
}
```

이 예제는 지정한 매개 변수의 값을 가져옵니다.

```
parameters('fullName')
```

그리고 다음 결과를 반환합니다. `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>rand

시작 끝만 포함하는 지정한 범위에서 임의의 정수를 반환합니다.

```
rand(<minValue>, <maxValue>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*minValue*> | 예 | 정수  | 범위의 가장 작은 정수 | 
| <*maxValue*> | 예 | 정수  | 함수가 반환할 수 있는 범위에서 가장 높은 정수 다음에 오는 정수 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*random-result*> | 정수  | 지정한 범위에서 반환된 임의의 정수 |  
|||| 

*예제*

이 예제는 최댓값을 제외하고 지정한 범위에서 임의의 정수를 가져옵니다. 

```
rand(1, 5)
```

그리고 다음 숫자 중 하나를 결과로 반환합니다. `1`, `2`, `3` 또는 `4` 

<a name="range"></a>

## <a name="range"></a>range

지정한 정수에서 시작하는 정수 배열을 반환합니다.

```
range(<startIndex>, <count>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*startIndex*> | 예 | 정수  | 배열을 첫 번째 항목으로 시작하는 정수 값 | 
| <*count*> | 예 | 정수  | 배열에 있는 정수의 숫자 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*range-result*>] | 배열 | 지정한 인덱스에서 시작하는 정수를 포함하는 배열 |  
|||| 

*예제*

이 예제는 지정한 인덱스에서 시작하고 지정한 수의 정수를 가진 정수 배열을 만듭니다.

```
range(1, 4)
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>replace

하위 문자열을 지정한 문자열로 바꾸고 결과 문자열을 반환합니다. 이 함수는 대/소문자를 구분합니다.

```
replace('<text>', '<oldText>', '<newText>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 바꿀 하위 문자열을 가진 문자열 | 
| <*oldText*> | 예 | 문자열 | 바꿀 하위 문자열 | 
| <*newText*> | 예 | 문자열 | 대체 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-text*> | 문자열 | 하위 문자열을 바꾼 후 업데이트된 문자열 <p>하위 문자열이 발견되지 않으면 원래 문자열을 반환합니다. | 
|||| 

*예제* 

이 예제는 "the old string"의 "old" 하위 문자열을 찾고 "old"를 "new"로 바꿉니다. 

```
replace('the old string', 'old', 'new')
```

그리고 다음 결과를 반환합니다. `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

개체에서 속성을 제거하고 업데이트된 개체를 반환합니다.

```
removeProperty(<object>, '<property>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | 예 | Object | 속성을 제거할 JSON 개체 | 
| <*property*> | 예 | 문자열 | 제거할 속성의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | 지정한 속성 없이 업데이트된 JSON 개체 | 
|||| 

*예제*

이 예제는 `"customerProfile"` 개체에서 `"accountLocation"` 속성을 제거하며, [JSON()](#json) 함수를 이 개체를 JSON으로 변환하고 업데이트된 개체를 반환합니다.

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>setProperty

개체의 속성에 대한 값을 설정하고 업데이트된 개체를 반환합니다. 새 속성을 추가하려면 이 함수 또는 [addProperty()](#addProperty) 함수를 사용할 수 있습니다.

```
setProperty(<object>, '<property>', <value>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*object*> | 예 | Object | 해당 속성을 설정할 JSON 개체 | 
| <*property*> | 예 | 문자열 | 설정할 기존 또는 새 속성의 이름 | 
| <*value*> | 예 | 모두 | 지정한 속성에 대해 설정할 값 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-object*> | Object | 해당 속성을 설정한 업데이트된 JSON 개체 | 
|||| 

*예제*

이 예제는 `"customerProfile"` 개체에서 `"accountNumber"` 속성을 설정하며, [JSON()](#json) 함수를 사용하여 이 개체를 JSON으로 변환합니다. 이 함수는 [guid()](#guid) 함수에서 생성된 값을 할당하고 업데이트된 JSON 개체를 반환합니다.

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>skip

컬렉션 앞의 항목을 제거하고 *다른 모든* 항목을 반환합니다.

```
skip([<collection>], <count>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 배열 | 해당 항목을 제거할 컬렉션 | 
| <*count*> | 예 | 정수  | 앞에서 제거할 항목 수에 대한 양수 정수 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*updated-collection*>] | 배열 | 지정한 항목을 제거한 후 업데이트된 컬렉션 | 
|||| 

*예제*

이 예제는 지정한 배열 앞에서 항목 한 개, 숫자 0을 제거합니다. 

```
skip([0, 1, 2, 3], 1)
```

그리고 남은 항목을 포함하는 다음 배열을 반환합니다. `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

문자열의 모든 문자를 포함하고 각 문자가 *구분 기호*로 구분된 배열을 반환합니다.

```
split('<text>', '<separator>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 분할할 문자가 있는 문자열 |  
| <*separator*> | 예 | 문자열 | 결과 배열의 각 문자 사이에 나타나는 구분 기호 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*char1*><*separator*><*char2*><*separator*>...] | 배열 | 지정한 문자열의 모든 항목에서 만든 결과 배열 |
|||| 

*예제* 

이 예제는 지정한 문자열에서 배열을 만들고 쉼표를 구분 기호로 사용하여 각 문자를 구분합니다.

```
split('abc', ',')
```

그리고 다음 결과를 반환합니다. `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

타임스탬프에 대한 날의 시작을 반환합니다. 

```
startOfDay('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 지정한 타임스탬프이지만 해당 날짜의 0시 마크에서 시작 | 
|||| 

*예제* 

이 예제는 이 타임스탬프에 대한 날의 시작을 찾습니다.

```
startOfDay('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

타임스탬프에 대한 시간의 시작을 반환합니다. 

```
startOfHour('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 지정한 타임스탬프이지만 시에 대한 0분 마크에서 시작 | 
|||| 

*예제* 

이 예제는 이 타임스탬프에 대한 시의 시작을 찾습니다.

```
startOfHour('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

타임스탬프에 대한 월의 시작을 반환합니다. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 지정한 타임스탬프이지만 0시 마크에 있는 월의 첫 번째 날에 시작 | 
|||| 

*예제* 

이 예제는 이 타임스탬프에 대한 월의 시작을 반환합니다.

```
startOfMonth('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startswith

문자열이 특정 하위 문자열로 시작하는지 검사합니다. 하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다. 이 함수는 대/소문자를 구분하지 않습니다.

```
startsWith('<text>', '<searchText>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 검사할 문자열 | 
| <*searchText*> | 예 | 문자열 | 찾을 시작 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| true 또는 false  | BOOLEAN | 시작 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. | 
|||| 

*예 1* 

이 예제는 "hello world" 문자열이 "hello" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'hello')
```

그리고 다음 결과를 반환합니다. `true`

*예 2*

이 예제는 "hello world" 문자열이 "greetings" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'greetings')
```

그리고 다음 결과를 반환합니다. `false`

<a name="string"></a>

## <a name="string"></a>string

값에 대한 문자열 버전을 반환합니다.

```
string(<value>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 모두 | 변환할 값 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*string-value*> | 문자열 | 지정한 값에 대한 문자열 버전 | 
|||| 

*예 1* 

이 예제는 이 숫자에 대한 문자열 버전을 만듭니다.

```
string(10)
```

그리고 다음 결과를 반환합니다. `"10"`

*예 2*

이 예제는 지정한 JSON 개체에 대한 문자열을 만들고 백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다.

```
string( { "name": "Sophie Owen" } )
```

그리고 다음 결과를 반환합니다. `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>sub

첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다.

```
sub(<minuend>, <subtrahend>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | 예 | 정수 또는 부동 소수점 수 | *감수*를 뺄 수 | 
| <*subtrahend*> | 예 | 정수 또는 부동 소수점 수 | *피감수*에서 뺄 수 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과 | 
|||| 

*예제* 

이 예제는 첫 번째 숫자에서 두 번째 숫자를 뺍니다.

```
sub(10.3, .3)
```

그리고 다음 결과를 반환합니다. `10`

<a name="substring"></a>

## <a name="substring"></a>substring

지정한 위치 또는 인덱스에서 시작하여 문자열에서 문자를 반환합니다. 인덱스 값은 숫자 0으로 시작합니다. 

```
substring('<text>', <startIndex>, <length>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 해당 문자를 원하는 문자열 | 
| <*startIndex*> | 예 | 정수  | 시작 위치 또는 인덱스 값에 대한 양수 | 
| <*length*> | 예 | 정수  | 하위 문자열에 원하는 문자의 양수 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*substring-result*> | 문자열 | 원본 문자열의 지정한 인덱스 위치에서 시작하는 지정한 수의 문자를 포함하는 하위 문자열 | 
|||| 

*예제* 

이 예제는 인덱스 값 6에서 시작하여 지정한 문자열에서 문자 하위 문자열 5개를 만듭니다.

```
substring('hello world', 6, 5)
```

그리고 다음 결과를 반환합니다. `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

타임스탬프에서 시간 단위 수를 뺍니다. [getPastTime](#getPastTime)도 함께 참조하세요.

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프를 포함하는 문자열 | 
| <*interval*> | 예 | 정수  | 뺄 지정한 시간 단위 수 | 
| <*timeUnit*> | 예 | 문자열 | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updated-timestamp*> | 문자열 | 타임스탬프 - 지정한 시간 단위 수 | 
|||| 

*예 1*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00:0000000Z"`

*예 2*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

그리고 선택적 "D" 형식을 사용하여 다음 결과를 반환합니다. `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>take

컬렉션 앞에서 항목을 반환합니다. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection*> | 예 | 문자열 또는 배열 | 해당 항목을 원하는 컬렉션 | 
| <*count*> | 예 | 정수  | 앞에서 제거할 항목 수에 대한 양수 정수 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*subset*> or [<*subset*>] | 각각 문자열 또는 배열 | 원본 컬렉션의 앞에서 가져온 지정한 수의 항목을 포함하는 문자열 또는 배열 | 
|||| 

*예제*

아래 예제는 이 컬렉션의 앞에서 지정한 수의 항목을 가져옵니다.

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"abc"`
* 두 번째 예제: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>ticks

지정한 타임스탬프에 대한 `ticks` 속성 값을 반환합니다. *tick*은 100나노초 간격입니다.

```
ticks('<timestamp>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*timestamp*> | 예 | 문자열 | 타임스탬프에 대한 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*ticks-number*> | 정수  | 지정한 타임스탬프 이후 틱 수 | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

문자열을 소문자 형식으로 반환합니다. 문자열의 문자에 소문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toLower('<text>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 소문자 형식으로 반환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*lowercase-text*> | 문자열 | 소문자 형식의 원래 문자열 | 
|||| 

*예제* 

이 예제는 이 문자열을 소문자로 변환합니다. 

```
toLower('Hello World')
```

그리고 다음 결과를 반환합니다. `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

문자열을 대문자 형식으로 반환합니다. 문자열의 문자에 대문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toUpper('<text>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 대문자 형식으로 반환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*uppercase-text*> | 문자열 | 대문자 형식의 원래 문자열 | 
|||| 

*예제* 

이 예제는 이 문자열을 대문자로 변환합니다.

```
toUpper('Hello World')
```

그리고 다음 결과를 반환합니다. `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>trigger

런타임 시 트리거의 출력 또는 식에 할당할 수 있는 다른 JSON 이름-값 쌍을 반환합니다. 

* 트리거의 입력 내부에서 이 함수는 이전 실행의 출력을 반환합니다. 

* 트리거의 조건 내부에서 이 함수는 현재 실행의 출력을 반환합니다. 

기본적으로 이 함수는 전체 트리거 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다. 또한 이 함수의 줄인 버전을 사용할 수 있습니다. [triggerOutputs()](#triggerOutputs) 및 [triggerBody()](#triggerBody)를 참조하세요. 

```
trigger()
```

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | 문자열 | 런타임 시 트리거의 출력 | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

런타임 시 트리거의 `body` 출력을 반환합니다. `trigger().outputs.body`를 줄인 표현입니다. [trigger()](#trigger)를 참조하세요. 

```
triggerBody()
```

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*trigger-body-output*> | 문자열 | 트리거의 `body` 출력 | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

트리거의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 값을 가진 배열을 반환합니다. 

```
triggerFormDataMultiValues('<key>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | 예 | 문자열 | 해당 값을 원하는 키의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| [<*array-with-key-values*>] | 배열 | 지정한 키와 일치하는 모든 값을 가진 배열 | 
|||| 

*예제* 

이 예제는 RSS 트리거의 양식 데이터 또는 양식 인코딩된 출력의 "feedUrl" 키 값에서 배열을 만듭니다. 

```
triggerFormDataMultiValues('feedUrl')
```

그리고 이 배열을 예제 결과로 반환합니다. `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

트리거의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 단일 값을 가진 문자열을 반환합니다. 함수가 일치하는 항목을 두 개 이상 찾으면 함수에서 오류가 발생합니다.

```
triggerFormDataValue('<key>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*key*> | 예 | 문자열 | 해당 값을 원하는 키의 이름 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*key-value*> | 문자열 | 지정한 키의 값 | 
|||| 

*예제* 

이 예제는 RSS 트리거의 양식 데이터 또는 양식 인코딩된 출력의 "feedUrl" 키 값에서 문자열을 만듭니다.

```
triggerFormDataValue('feedUrl')
```

그리고 이 문자열을 예제 결과로 반환합니다. `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

여러 부분으로 구성된 트리거의 출력에서 특정 부분에 대한 본문을 반환합니다. 

```
triggerMultipartBody(<index>)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*index*> | 예 | 정수  | 원하는 부분에 대한 인덱스 값 |
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*body*> | 문자열 | 트리거의 다중 부분 출력에서 지정한 부분에 대한 본문 | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

런타임 시 트리거 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. `trigger().outputs`를 줄인 표현입니다. [trigger()](#trigger)를 참조하세요. 

```
triggerOutputs()
```

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*trigger-output*> | 문자열 | 런타임 시 트리거의 출력  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>trim

문자열에서 선행 및 후행 공백을 제거하고 업데이트된 문자열을 반환합니다.

```
trim('<text>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*text*> | 예 | 문자열 | 제거할 선행 및 후행 공백이 있는 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | 문자열 | 선행 또는 후행 공백이 없는 원래 문자열에 대한 업데이트된 버전 | 
|||| 

*예제* 

이 예제는 문자열 " Hello World  "에서 선행 및 후행 공백을 제거합니다.  

```
trim(' Hello World  ')
```

그리고 다음 결과를 반환합니다. `"Hello World"`

<a name="union"></a>

## <a name="union"></a>union

지정한 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다. 결과에 나타내기 위해 항목이 이 함수에 전달된 모든 컬렉션에 나타날 수 있습니다. 같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>, ...  | 예 | 배열 또는 개체, 그러나 둘 다는 아님 | *모든* 항목을 원하는 컬렉션 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | 각각 배열 또는 개체 | 지정한 컬렉션의 모든 항목을 포함하는 컬렉션 - 중복 없음 | 
|||| 

*예제* 

이 예제는 다음 컬렉션에서 *모든* 항목을 가져옵니다. 

```
union([1, 2, 3], [1, 2, 10, 101])
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다. [encodeUriComponent()](#encodeUriComponent) 대신에 이 함수를 사용합니다. 두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`를 사용하는 것이 좋습니다.

```
uriComponent('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | URI 인코딩된 형식으로 변환할 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*encoded-uri*> | 문자열 | 이스케이프 문자로 URI 인코딩된 문자열 | 
|||| 

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
uriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

URI(Uniform Resource Identifier) 구성 요소에 대한 이진 버전을 반환합니다.

```
uriComponentToBinary('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 URI 인코딩된 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*binary-for-encoded-uri*> | 문자열 | URI 인코딩된 문자열에 대한 이진 버전 이진 콘텐츠는 base64로 인코딩되며 `$content`에 의해 표시됩니다. | 
|||| 

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 이진 버전을 만듭니다. 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

URI(Uniform Resource Identifier) 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 URI 인코딩된 문자열을 디코딩합니다.

```
uriComponentToString('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 디코딩할 URI 인코딩된 문자열 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*decoded-uri*> | 문자열 | URI 인코딩된 문자열에 대한 디코딩된 버전 | 
|||| 

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 디코딩된 버전을 만듭니다. 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

URI(Uniform Resource Identifier)에 대한 `host` 값을 반환합니다.

```
uriHost('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `host` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*host-value*> | 문자열 | 지정한 URI에 대한 `host` 값 | 
|||| 

*예제*

이 예제는 이 URI에 대한 `host` 값을 찾습니다. 

```
uriHost('https://www.localhost.com:8080')
```

그리고 다음 결과를 반환합니다. `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

URI(Uniform Resource Identifier)에 대한 `path` 값을 반환합니다. 

```
uriPath('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `path` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*path-value*> | 문자열 | 지정한 URI에 대한 `path` 값입니다. `path`가 값을 포함하지 않은 경우 "/" 문자를 반환합니다. | 
|||| 

*예제*

이 예제는 이 URI에 대한 `path` 값을 찾습니다. 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

URI(Uniform Resource Identifier)에 대한 `path` 및 `query` 값을 반환합니다.

```
uriPathAndQuery('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `path` 및 `query` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*path-query-value*> | 문자열 | 지정한 URI에 대한 `path` 및 `query` 값입니다. `path`에 값을 지정하지 않은 경우 "/" 문자를 반환합니다. | 
|||| 

*예제*

이 예제는 이 URI에 대한 `path` 및 `query`값을 찾습니다.

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

URI(Uniform Resource Identifier)에 대한 `port` 값을 반환합니다.

```
uriPort('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `port` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*port-value*> | 정수  | 지정한 URI에 대한 `port` 값입니다. `port`에 값을 지정하지 않은 경우 프로토콜에 대한 기본 포트를 반환합니다. | 
|||| 

*예제*

이 예제는 이 URI에 대한 `port` 값을 반환합니다.

```
uriPort('http://www.localhost:8080')
```

그리고 다음 결과를 반환합니다. `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

URI(Uniform Resource Identifier)에 대한 `query` 값을 반환합니다.

```
uriQuery('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `query` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*query-value*> | 문자열 | 지정한 URI에 대한 `query` 값 | 
|||| 

*예제*

이 예제는 이 URI에 대한 `query` 값을 반환합니다. 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>uriScheme

URI(Uniform Resource Identifier)에 대한 `scheme` 값을 반환합니다.

```
uriScheme('<uri>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*uri*> | 예 | 문자열 | 해당 `scheme` 값을 원하는 URI | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*scheme-value*> | 문자열 | 지정한 URI에 대한 `scheme` 값 | 
|||| 

*예제*

이 예제는 이 URI에 대한 `scheme` 값을 반환합니다.

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

현재 타임스탬프를 반환합니다. 

```
utcNow('<format>')
```

선택적으로 <*format*> 매개 변수를 사용하여 다른 형식을 지정할 수 있습니다. 


| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*format*> | 아니오 | 문자열 | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프에 대한 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)이며, 이는 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하고 표준 시간대 정보를 보존합니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*current-timestamp*> | 문자열 | 현재 날짜 및 시간 | 
|||| 

*예 1*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다. 이 예제는 현재 타임스탬프를 가져옵니다. 

```
utcNow()
```

그리고 다음 결과를 반환합니다. `"2018-04-15T13:00:00.0000000Z"`

*예 2*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다. 이 예제는 선택적 "D" 형식을 사용하여 현재 타임스탬프를 가져옵니다.

```
utcNow('D')
```

그리고 다음 결과를 반환합니다. `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>variables

지정한 변수에 대한 값을 반환합니다. 

```
variables('<variableName>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*variableName*> | 예 | 문자열 | 해당 값을 원하는 변수의 이름 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*variable-value*> | 모두 | 지정한 변수의 값 | 
|||| 

*예제*

"numItems" 변수에 대한 현재 값이 20이라고 가정합니다. 이 예제는 이 변수에 대한 정수 값을 가져옵니다.

```
variables('numItems')
```

그리고 다음 결과를 반환합니다. `20`

<a name="workflow"></a>

## <a name="workflow"></a>워크플로

런타임 동안 워크플로 자체에 대한 모든 세부 정보를 반환합니다. 

```
workflow().<property>
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*property*> | 아니오 | 문자열 | 해당 값을 원하는 워크플로 속성의 이름 <p>워크플로 개체는 다음 속성을 갖습니다. **name**, **type**, **id**, **location** 및 **run**. **run** 속성 값은 다음 속성을 갖는 개체이기도 합니다. **name**, **type** 및 **id**. | 
||||| 

*예제*

이 예제는 워크플로의 현재 실행에 대한 이름을 반환합니다.

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

JSON 개체를 포함하는 문자열에 대한 XML 버전을 반환합니다. 

```
xml('<value>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*value*> | 예 | 문자열 | 변환할 JSON 개체를 가진 문자열 <p>JSON 개체는 루트 속성 한 개만 가져야 합니다. <br>백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다. | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*xml-version*> | Object | 지정한 문자열 또는 JSON 개체에 대한 인코딩된 XML | 
|||| 

*예 1*

이 예제는 이 문자열에 대한 XML 버전을 만들며, 이 문자열은 JSON 개체를 포함합니다. 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

그리고 다음 결과 XML을 반환합니다. 

```xml
<name>Sophia Owen</name>
```

*예 2*

이 JSON 개체가 있다고 가정합니다.

```json
{ 
  "person": { 
    "name": "Sophia Owen", 
    "city": "Seattle" 
  } 
}
```

이 예제는 이 JSON 개체를 포함하는 문자열에 대한 XML을 만듭니다.

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

그리고 다음 결과 XML을 반환합니다. 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>xpath

XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. XPath 식 또는 단순히 "XPath"는 XML 콘텐츠에서 노드를 선택하고 값을 계산할 수 있도록 XML 문서 구조를 탐색하는 데 도움이 됩니다.

```
xpath('<xml>', '<xpath>')
```

| 매개 변수 | 필수 | 형식 | 설명 | 
| --------- | -------- | ---- | ----------- | 
| <*xml*> | 예 | 모두 | XPath 식 값과 일치하는 노드 또는 값을 검색하기 위한 XML 문자열 | 
| <*xpath*> | 예 | 모두 | 일치하는 XML 노드 또는 값을 찾는 데 사용한 XPath 식 | 
||||| 

| 반환 값 | type | 설명 | 
| ------------ | ---- | ----------- | 
| <*xml-node*> | XML | 단일 노드만이 지정한 XPath 식과 일치하는 경우 XML 노드 | 
| <*value*> | 모두 | 단일 값만이 지정한 XPath 식과 일치하는 경우 XML 노드의 값 | 
| [<*xml-node1*>, <*xml-node2*>, ...] </br>또는 </br>[<*value1*>, <*value2*>, ...] | 배열 | 지정한 XPath 식과 일치하는 XML 노드 또는 값을 가진 배열 | 
|||| 

*예 1*

이 예제는 지정한 인수의 `<name></name>` 노드와 일치하는 노드를 찾고 해당 노드 값을 가진 배열을 반환합니다. 

`xpath(xml(parameters('items')), '/produce/item/name')`

다음은 인수입니다.

* 이 XML을 포함하는 "items" 문자열은 다음과 같습니다.

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  이 예제는 [parameters()](#parameters) 함수를 사용하여 "items" 인수에서 XML 문자열을 가져오지만, [xml()](#xml) 함수를 사용하여 해당 문자열을 XML 형식으로 변환하기도 해야 합니다. 

* 문자열로 전달되는 이 XPath 식:

  `"/produce/item/name"`

다음은 `<name></name`과 일치하는 노드를 가진 결과 배열입니다.

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*예 2*

예제 1 다음에 이 예제는 `<count></count>` 노드와 일치하는 노드를 찾고 `sum()` 함수를 사용하여 해당 노드 값을 더합니다.

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

그리고 다음 결과를 반환합니다. `30`

*예 3*

이 예제의 경우 두 식은 모두 지정한 인수의 `<location></location>` 노드와 일치하는 노드를 찾으며, 이 인수는 네임스페이스와 함께 XML을 포함합니다. 이 식은 백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다.

* *식 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *식 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

다음은 인수입니다.

* 다음 XML 문서 네임스페이스를 포함하는 이 XML, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* 여기서 XPath 식 중 하나:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

다음은 `<location></location` 노드와 일치하는 결과 노드입니다.

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*예제 4*

예제 3 다음에 이 예제는 `<location></location>` 노드에서 값을 찾습니다. 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

그리고 다음 결과를 반환합니다. `"Paris"`

## <a name="next-steps"></a>다음 단계

[워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 알아보기
