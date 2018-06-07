---
title: 워크플로 정의 언어 스키마 - Azure Logic Apps | Microsoft Docs
description: 워크플로 정의 언어로 Azure Logic Apps에 대한 사용자 지정 워크플로 정의 작성
services: logic-apps
author: ecfan
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33886811"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>워크플로 정의 언어 스키마로 Logic Apps 워크플로 정의

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 논리 앱 워크플로를 만들 때 워크플로 기본 정의는 논리 앱에 대해 실행되는 실제 논리에 대해 설명합니다. 이 설명은 [JSON(JavaScript Object Notation)](https://www.json.org/) 형식을 사용하는 워크플로 정의 언어 스키마에서 정의하고 유효성을 검사하는 구조를 따릅니다. 
  
## <a name="workflow-definition-structure"></a>워크플로 정의 구조

워크플로 정의에는 논리 앱에서 실행하는 하나 이상의 동작에 더해 로직 앱을 인스턴스화하는 최소 하나 이상의 트리거가 있습니다. 

워크플로 정의에 대한 고급 구조는 다음과 같습니다.  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| 요소 | 필수 | 설명 | 
|---------|----------|-------------| 
| 정의 | 예 | 워크플로 정의에 대한 시작 요소 | 
| $schema | 외부에서 워크플로 정의를 참조하는 경우만 | 워크플로 정의 언어의 버전을 설명하는 JSON 스키마 파일의 위치를 여기서 찾을 수 있습니다. <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | 아니오 | 워크플로 정의의 버전 번호는 기본적으로 “1.0.0.0”입니다. 워크플로 배포하는 경우 올바른 정의를 식별하고 확인하려면 사용할 값을 지정합니다. | 
| 매개 변수 | 아니오 | 데이터를 워크플로에 전달하는 하나 이상의 매개 변수에 대한 정의 <p><p>최대 매개 변수: 50 | 
| 트리거 | 아니오 | 워크플로를 인스턴트화하는 하나 이상의 트리거에 대한 정의 Logic Apps Designer를 사용하여 시각적으로가 아닌, 워크플로 정의 언어를 통해서만 하나 초과 트리거를 정의할 수 있습니다. <p><p>최대 트리거: 10 | 
| actions | 아니오 | 워크플로 런타임 시 실행하는 하나 이상의 작업에 대한 정의 <p><p>최대 작업: 250 | 
| outputs | 아니오 | 워크플로 실행에서 반환되는 출력에 대한 정의 <p><p>최대 출력: 10 |  
|||| 

## <a name="parameters"></a>매개 변수

`parameters` 섹션에서 논리 앱이 입력을 허용하기 위한 배포 시 사용하는 모든 워크플로 매개 변수를 정의합니다. 매개 변수 선언 및 매개 변수 값 모두 배포 시 필요합니다. 다른 워크플로 섹션에서 이러한 매개 변수를 사용할 수 있기 전에 이 섹션에서 모든 매개 변수를 선언하는지 확인합니다. 

매개 변수 정의에 대한 일반적인 구조는 다음과 같습니다.  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| 요소 | 필수 | 형식 | 설명 |  
|---------|----------|------|-------------|  
| 형식 | 예 | int, float, 문자열, securestring, bool, 배열, JSON 개체, secureobject <p><p>**참고**: 모든 암호, 키 및 비밀의 경우 `GET` 작업은 이러한 형식을 반환하지 않기 때문에 `securestring` 및 `secureobject` 형식을 사용합니다. | 매개 변수의 형식 |
| defaultValue | 아니오 | `type`과 동일 | 워크플로 인스턴스화할 때 아무 값도 지정하지 않는 경우의 기본 매개 변수 값 | 
| allowedValues | 아니오 | `type`과 동일 | 매개 변수를 수용할 수 있는 값으로 배열 |  
| metadata | 아니오 | JSON 개체 | 예를 들어 모든 다른 매개 변수는 이름 또는 Visual Studio 또는 다른 도구에서 사용하는 디자인 타임 데이터나 논리 앱에 대한 이름 또는 읽을 수 있는 정보를 설명합니다. |  
||||

## <a name="triggers-and-actions"></a>트리거 및 작업  

워크플로 정의에서 `triggers` 및 `actions` 섹션은 워크플로 실행 중에 발생하는 호출을 정의합니다. 구문 및 이러한 섹션에 대한 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조합니다.
  
## <a name="outputs"></a>outputs 

`outputs` 섹션에서 워크플로가 실행을 종료할 때 반환할 수 있는 데이터를 정의합니다. 예를 들어, 각 실행에서 특정 상태 또는 값을 추적하려면 워크플로 출력이 해당 데이터를 반환하도록 지정합니다. 

> [!NOTE]
> 서비스의 REST API에서 들어오는 요청에 응답하는 경우 `outputs`을 사용하지 마세요. 대신 `Response` 작업 형식을 사용합니다. 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조하세요.

출력 정의에 대한 일반적인 구조는 다음과 같습니다. 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| 요소 | 필수 | 형식 | 설명 | 
|---------|----------|------|-------------| 
| <*key-name*> | 예 | 문자열 | 출력 반환 값에 대한 키 이름 |  
| 형식 | 예 | int, float, 문자열, securestring, bool, 배열, JSON 개체 | 출력 반환 값의 형식 | 
| 값 | 예 | `type`과 동일 | 출력 반환 값 |  
||||| 

워크플로 실행에서 출력을 가져오려면 논리 앱의 실행 기록과 Azure Portal의 세부 정보를 검토하거나 [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 사용합니다. 또한 대시보드를 만들 수 있도록 외부 시스템, 예를 들어 PowerBI에 출력을 전달할 수 있습니다. 

<a name="expressions"></a>

## <a name="expressions"></a>식

Json을 사용하면 예를 들어 디자인 타임 시 존재하는 리터럴 값이 있을 수 있습니다.

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

실행 시간까지 존재하지 않는 값이 있을 수 있습니다. 이러한 값을 표시하려면 실행 시간에 평가되는 *식*을 사용할 수 있습니다. 식은 하나 이상의 [함수](#functions), [연산자](#operators), 변수, 명시적인 값 또는 상수를 포함하는 시퀀스입니다. 워크플로 정의에서 식 앞에 at 기호(@)를 붙여 JSON 문자열 값의 어디에서나 식을 사용할 수 있습니다. JSON 값을 나타내는 식을 계산하는 경우 식 본문은 다른 JSON 값에서 결과 및 @ 문자를 제거하여 추출합니다. 

예를 들어 이전에 정의된 `customerName` 속성의 경우 식에서 [매개 변수()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 함수를 사용하여 속성 값을 얻고 해당 값을 `accountName` 속성에 할당할 수 있습니다.

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*문자열 보간*은 @ 문자와 중괄호({})로 래핑된 문자열 내에 여러 개의 식을 사용할 수 있게 합니다. 구문은 다음과 같습니다.

```json
@{ "<expression1>", "<expression2>" }
```

결과는 항상 문자열로서 예를 들어 이 기능을 `concat()` 함수와 유사하게 만듭니다. 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

@ 문자로 시작하는 리터럴 문자열이 있는 경우 @ 문자 앞에 이스케이프 문자(@ @)로서 다른 @ 문자를 붙입니다.

이러한 예제는 식의 평가 방식을 보여 줍니다.

| JSON 값 | 결과 |
|------------|--------| 
| "Sophia Owen" | 이러한 문자 'Sophia Owen'을 반환 |
| "배열[1]" | 이러한 문자 '배열[1]'을 반환 |
| "\@@\" | 이러한 문자를 한 문자열 ' @'로 반환 |   
| \" \@\" | 이러한 문자를 두 문자열 '@'로 반환 |
|||

이 예제에서는 "myBirthMonth"를 "1월", "myAge"를 숫자 42와 동일하게 정의하는 것으로 가정합니다.  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

이러한 예제는 다음 식의 평가 방식을 보여 줍니다.

| JSON 식 | 결과 |
|-----------------|--------| 
| "@parameters('myBirthMonth')" | 이 문자열 "1월"을 반환 |  
| "@{parameters('myBirthMonth')}" | 이 문자열 "1월"을 반환 |  
| "@parameters('myAge')" | 이 숫자 42를 반환 |  
| "@{parameters('myAge')}" | 이 숫자 "42"를 문자열로 반환 |  
| "내 나이는 @{parameters('myAge')}살" | 이 문자열 "내 나이는 42살"을 반환 |  
| "@concat('My age is ', string(parameters('myAge')))" | 이 문자열 "내 나이는 42살"을 반환 |  
| "내 나이는 @{parameters('myAge')}살" | 식이 포함된 이 문자열 "내 나이는 @{parameters('myAge')}살'을 반환 | 
||| 

Logic Apps Designer에서 시각적으로 작업하는 경우 예를 통해 식 작성기를 통해 식을 만들 수 있습니다. 

![Logic Apps Designer > 식 작성기](./media/logic-apps-workflow-definition-language/expression-builder.png)

완료되면 워크플로 정의에서 해당 속성, 예를 들어 여기서는 `searchQuery` 속성에 대해 식이 표시됩니다.

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>연산자

[식](#expressions) 및 [함수](#functions)에서 연산자는 속성 참조 또는 배열의 값 등의 특정 작업을 수행합니다. 

| 연산자 | Task | 
|----------|------|
| ' | 리터럴 문자열을 입력으로 또는 식 및 함수에서 사용하려면 작은따옴표, 예를 들어 `'<myString>'`만 사용하여 문자열을 래핑합니다. 전체 식에서 JSON 서식과 충돌하는 큰따옴표("")를 사용하지 마세요. 예:  <p>**예**: length('Hello') </br>**아니요**: length('Hello') <p>배열이나 숫자를 전달할 때 문장 부호를 래핑하지 않아도 됩니다. 예:  <p>**예**: length([1, 2, 3]) </br>**아니요**: length([1, 2, 3]) | 
| [] | 배열의 특정 위치(인덱스)에서 값을 참조하려면 대괄호를 사용합니다. 예를 들어 배열에서 두 번째 항목을 가져오려면: <p>`myArray[1]` | 
| 에서도 확인할 수 있습니다. | 개체의 속성을 참조하려면 점 연산자를 사용합니다. 예를 들어 `customer` JSON 개체에 대한 `name` 속성을 가져오려면: <p>`"@parameters('customer').name"` | 
| ? | 런타임 오류 없이 개체의 null 속성을 참조하려면 물음표 연산자를 사용합니다. 예를 들어 트리거에서 null 출력을 처리하려면 이 식을 사용할 수 있습니다. <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

일부 식은 논리 앱이 실행을 시작할 때 존재하지 않을 수도 있는 런타임 작업에서 해당 값을 가져옵니다. 식에서 이러한 값을 사용하거나 참조하려면 [*함수*](../logic-apps/workflow-definition-language-functions-reference.md)를 사용할 수 있습니다. 예를 들어 정수 또는 부동 소수점에서 합계를 반환하는 [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) 함수 같은 계산용 수식 함수를 사용할 수 있습니다. 각 함수에 대한 자세한 내용은 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.
또는 함수 및 함수의 일반 용도에 대해 계속 알아봅니다.

다음은 함수로 수행할 수 있는 한 두 가지 예제 작업입니다. 

| Task | 함수 구문 | 결과 | 
| ---- | --------------- | -------------- | 
| 문자열을 소문자 형식으로 반환합니다. | toLower('<*text*>') <p>예를 들어: toLower('Hello') | "hello" | 
| 전역적으로 고유한 식별자(GUID)를 반환합니다. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

이 예제는 식에서 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 함수를 사용하여 `customerName` 매개 변수에서 값을 가져와 해당 값을 `accountName` 속성에 할당할 수 있는 방법을 보여 줍니다.

```json
"accountName": "@parameters('customerName')"
```

식에서 함수를 사용할 수 있는 몇 가지 다른 일반적인 방법은 다음과 같습니다.

| Task | 식에서 함수 구문 | 
| ---- | -------------------------------- | 
| 함수에 해당 항목을 전달하여 항목으로 작업을 수행합니다. | "@<*functionName*>(<*item*>)" | 
| 1. 중첩된 `parameters()` 함수를 사용하여 *parameterName* 값을 가져옵니다. </br>2. 해당 값을 *functionName*에 전달하여 결과로 작업을 수행합니다. | "@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1. 중첩된 내부 함수 *functionName*에서 결과를 가져옵니다. </br>2. 외부 함수 *functionName2*에 결과를 전달합니다. | "@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1. *functionName*에서 결과를 가져옵니다. </br>2. 결과가 속성 *propertyName*이 있는 개체인 경우 해당 속성의 값을 가져옵니다. | "@<*functionName*>(<*item*>).<*propertyName*>" | 
||| 

예를 들어 `concat()` 함수는 두 개 이상의 문자열 값을 매개 변수로 사용할 수 있습니다. 이 함수는 이러한 문자열을 하나의 문자열로 결합합니다. 문자열 리터럴, 예를 들어 "Sophia" 및 "Owen"을 전달하여 결합된 문자열 "SophiaOwen"을 가져올 수 있습니다.

```json
"customerName": "@concat('Sophia', 'Owen')"
```

또는 매개 변수에서 문자열 값을 가져올 수 있습니다. 이 예제는 각 `concat()` 매개 변수 및 `firstName`, `lastName` 매개 변수에서 `parameters()` 함수를 사용합니다. 결과 문자열을 `concat()` 함수에 전달하면 결합된 문자열, 예를 들어 "SophiaOwen"을 가져올 수 있습니다.

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

두 예제 중 어느 경우든 결과를 `customerName` 속성에 할당합니다. 

각 함수에 대한 자세한 내용은 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.
또는 일반 용도에 기반한 함수에 대해 계속 알아보십시오.

<a name="string-functions"></a>

### <a name="string-functions"></a>문자열 함수

문자열로 작업하려면 이러한 문자열 함수 및 일부 [컬렉션 함수](#collection-functions)도 사용할 수 있습니다. 문자열 함수는 문자열에서만 작동합니다. 

| 문자열 함수 | Task | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 문자열이 지정된 하위 문자열로 끝나는지 여부를 확인합니다. | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 전역적으로 고유한 식별자(GUID)를 문자열로 생성합니다. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 하위 문자열에 대한 시작 위치를 반환합니다. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 하위 문자열에 대한 종료 위치를 반환합니다. | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 하위 문자열을 지정된 문자열로 바꾸고 업데이트된 문자열을 반환합니다. | 
| [분할](../logic-apps/workflow-definition-language-functions-reference.md#split) | 문자열에서 모든 문자를 지닌 배열을 반환하고 특정 구분 문자로 각 문자를 구분합니다. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 문자열이 특정 하위 문자열로 시작하는지 검사합니다. | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 지정된 위치에서 시작하여 문자열에서 문자를 반환합니다. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 문자열을 소문자 형식으로 반환합니다. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 문자열을 대문자 형식으로 반환합니다. | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 문자열에서 선행 및 후행 공백을 제거하고 업데이트된 문자열을 반환합니다. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>컬렉션 함수

컬렉션, 일반적으로 배열, 문자열 및 경우에 따라 사전으로 작업하려면 이러한 컬렉션 함수를 사용할 수 있습니다. 

| 컬렉션 함수 | Task | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | 컬렉션에 특정 항목이 있는지 검사합니다. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | 컬렉션이 비어 있는지 검사합니다. | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | 컬렉션에서 첫 번째 항목을 반환합니다. | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 지정한 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 지정된 문자로 구분되는 배열에서 *모든* 항목이 들어 있는 문자열을 반환합니다. | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | 컬렉션에서 마지막 항목을 반환합니다. | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 문자열 또는 배열에서 항목 수를 반환합니다. | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | 컬렉션 앞의 항목을 제거하고 *다른 모든* 항목을 반환합니다. | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | 컬렉션 앞에서 항목을 반환합니다. | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 지정한 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>비교 함수

조건을 사용하고 값 및 식 결과를 비교하거나 다양한 종류의 논리를 평가하려면 이러한 비교 함수를 사용할 수 있습니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 비교 함수 | Task | 
| ------------------- | ---- | 
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | 모든 식이 true인지 확인합니다. | 
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 두 값이 동일한지 확인합니다. | 
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 첫 번째 값이 두 번째 값보다 큰지 검사합니다. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 식이 true인지 또는 false인지 검사합니다. 결과에 기반해 지정한 값을 반환합니다. | 
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 첫 번째 값이 두 번째 값보다 작은지 검사합니다. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다. | 
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 식이 false인지 검사합니다. | 
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 최소 하나의 식이 true인지 검사합니다. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>변환 함수

값의 유형 또는 형식을 변경하려면 이러한 변환 함수를 사용할 수 있습니다. 예를 들어 부울에서 정수로 값을 변경할 수 있습니다. 변환 동안 Logic Apps이 콘텐츠 형식을 처리하는 방법을 알아보려면 [콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md)를 참조합니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 변환 함수 | Task | 
| ------------------- | ---- | 
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 단일 지정 입력에서 배열을 반환합니다. 여러 입력의 경우 [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)를 참조합니다. | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 문자열에 대한 base64로 인코딩된 버전을 반환합니다. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. | 
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 입력 값에 대한 이진 버전을 반환합니다. | 
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 입력 값에 대한 부울 버전을 반환합니다. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 여러 입력에서 배열을 반환합니다. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 입력 값에 대한 데이터 URI를 반환합니다. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | 데이터 URI에 대한 이진 버전을 반환합니다. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | 데이터 URI에 대한 문자열 버전을 반환합니다. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | 데이터 URI에 대한 이진 버전을 반환합니다. | 
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | 디코딩된 버전으로 이스케이프 문자를 대체하는 문자열을 반환합니다. | 
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | URL 안전하지 않은 문자를 이스케이프 문자로 대체하는 문자열을 반환합니다. | 
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 입력 값에 대해 부동 소수점 숫자를 반환합니다. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 문자열에 대한 정수 버전을 반환합니다. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다. | 
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | 입력 값에 대한 문자열 버전을 반환합니다. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | 이스케이프 문자로 URL 안전하지 않은 문자를 대체하여 입력 값에 대한 URI로 인코딩된 버전을 반환합니다. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | URI로 인코딩된 문자열에 대한 이진 버전을 반환합니다. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 문자열에 대한 XML 버전을 반환합니다. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>수학 함수

정수 및 부동 소수점을 사용하려면 이러한 수식 함수를 사용할 수 있습니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 수식 함수 | Task | 
| ------------- | ---- | 
| [추가](../logic-apps/workflow-definition-language-functions-reference.md#add) | 두 숫자를 더한 결과를 반환합니다. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 두 숫자를 나눈 결과를 반환합니다. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 숫자 또는 배열 집합에서 가장 높은 값을 반환합니다. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다. | 
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 두 숫자를 나눈 나머지를 반환합니다. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 두 숫자를 곱한 결과를 반환합니다. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 지정된 범위에서 임의의 정수를 반환합니다. | 
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 지정한 정수에서 시작하는 정수 배열을 반환합니다. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>날짜 및 시간 함수

날짜 및 시간을 사용하려면 이러한 날짜 및 시간 함수를 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 날짜 또는 시간 함수 | Task | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | 타임스탬프에 일 수를 추가합니다. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | 타임스탬프에 시간 수를 추가합니다. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | 타임스탬프에 분 수를 추가합니다. | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | 타임스탬프에 초 수를 추가합니다. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | 타임스탬프에 시간 단위 수를 추가합니다. [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime)도 참조합니다. | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | UTC(Universal Time Coordinated)의 타임 스탬프를 대상 표준 시간대로 변환합니다. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | 원본 표준 시간대의 타임 스탬프를 대상 표준 시간대로 변환합니다. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | 원본 표준 시간대의 타임 스탬프를 UTC(Universal Time Coordinated)로 변환합니다. | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | 타임 스탬프에서 월 구성 요소인 날짜를 반환합니다. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | 타임 스탬프에서 주 구성 요소인 날짜를 반환합니다. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | 타임 스탬프에서 연 구성 요소인 날짜를 반환합니다. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | 타임 스탬프에서 날짜를 반환합니다. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 지정된 시간 단위를 더한 현재 타임 스탬프를 반환합니다. [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime)도 참조합니다. | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 지정된 시간 단위을 뺀 현재 타임 스탬프를 반환합니다. [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime)도 참조합니다. | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | 타임 스탬프에 대한 날의 시작을 반환합니다. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | 타임 스탬프에 대한 시간의 시작을 반환합니다. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | 타임 스탬프에 대한 분의 시작을 반환합니다. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | 타임스탬프에서 시간 단위 수를 뺍니다. [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime)도 참조합니다. | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 지정된 타임 스탬프에 대한 `ticks` 속성 값을 반환합니다. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 현재 타임스탬프를 문자열로 반환합니다. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>워크플로 함수

이러한 워크플로 함수는 다음을 도울 수 있습니다.

* 실행 시간에 워크플로 인스턴스에 대한 세부 정보를 가져옵니다. 
* 논리 앱을 인스턴스화하는 데 사용된 입력을 사용합니다.
* 트리거 및 작업에서 출력을 참조합니다.

예를 들어 한 동작에서 출력을 참조하고 이후 작업에서 해당 데이터를 사용할 수 있습니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 워크플로 함수 | Task | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 런타임 시 현재 작업의 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)도 참조합니다. | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 런타임 시 작업의 `body` 출력을 반환합니다. [body](../logic-apps/workflow-definition-language-functions-reference.md#body)도 참조합니다. | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 런타임 시 작업의 출력을 반환합니다. [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)을 참조합니다. | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 런타임 시 작업의 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [action](../logic-apps/workflow-definition-language-functions-reference.md#action)도 참조합니다.  | 
| [body](#body) | 런타임 시 작업의 `body` 출력을 반환합니다. [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody)도 참조합니다. | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | *form-data* 또는 *form-encoded* 작업 출력에서 키 이름이 일치하는 값으로 배열을 만듭니다. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | 작업의 *form-data* 또는 *form-encoded*에서 키 이름이 일치하는 단일 값을 반환합니다. | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 배열에 대해 반복 작업 내에 있을 경우 현재 작업 반복하는 동안 배열에서 현재 항목을 반환합니다. | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | for-each 또는 do-until-loop 내에 있을 경우 지정된 루프에서 현재 항목을 반환합니다.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | 트리거 또는 동작을 호출하는 "콜백 URL"을 반환합니다. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 여러 부분으로 구성된 작업의 출력에서 특정 부분에 대한 본문을 반환합니다. | 
| [매개 변수](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | 논리 앱 정의에 설명된 매개 변수에 대한 값을 반환합니다. | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 런타임 시 또는 다른 JSON 이름-값 쌍에서 트리거 출력을 반환합니다. [triggerOutputs](#triggerOutputs) 및 [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)도 참조합니다. | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 런타임 시 트리거의 `body` 출력을 반환합니다. [트리거](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 참조합니다. | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | *form-data* 또는 *form-encoded* 트리거 출력에서 키 이름과 일치하는 단일 값을 반환합니다. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Trigger의 다중 부분 출력에서 특정 부분에 대한 본문을 반환합니다. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | *form-data* 또는 *form-encoded* 트리거 출력에서 키 이름이 일치하는 값의 배열을 만듭니다. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 런타임 시 트리거 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [트리거](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 참조합니다. | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 지정한 변수에 대한 값을 반환합니다. | 
| [워크플로](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 런타임 동안 워크플로 자체에 대한 모든 세부 정보를 반환합니다. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI 구문 분석 기능

URI(uniform resource identifier)를 사용하고 이러한 URI에 대한 다양한 속성 값을 가져오려면 URI 구문 분석 기능을 사용할 수 있습니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| URI 구문 분석 기능 | Task | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | URI(Uniform Resource Identifier)에 대한 `host` 값을 반환합니다. | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | URI(Uniform Resource Identifier)에 대한 `path` 값을 반환합니다. | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | URI(Uniform Resource Identifier)에 대한 `path` 및 `query` 값을 반환합니다. | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | URI(Uniform Resource Identifier)에 대한 `port` 값을 반환합니다. | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | URI(Uniform Resource Identifier)에 대한 `query` 값을 반환합니다. | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | URI(Uniform Resource Identifier)에 대한 `scheme` 값을 반환합니다. | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>JSON과 XML 함수

JSON 개체와 XML 노드를 사용하려면 이러한 조작 함수를 사용할 수 있습니다. 각 함수에 대한 전체 참조는 [사전순 참조 문서](../logic-apps/workflow-definition-language-functions-reference.md)를 참조합니다.

| 조작 함수 | Task | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | JSON 개체에 속성 및 해당 값 또는 이름-값 쌍을 추가하고 업데이트된 개체를 반환합니다. | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 하나 이상의 매개 변수에서 Null이 아닌 첫 번째 값을 반환합니다. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | JSON 개체에서 속성을 제거하고 업데이트된 개체를 반환합니다. | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | JSON 개체의 속성에 대한 값을 설정하고 업데이트된 개체를 반환합니다. | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. | 
||| 

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 알아봅니다
* [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 사용하여 프로그래밍 방식으로 논리 앱을 만들고 관리하는 방법에 대해 알아봅니다
