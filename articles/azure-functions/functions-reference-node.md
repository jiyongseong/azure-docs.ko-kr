---
title: Azure Functions에 대한 JavaScript 개발자 참조 | Microsoft Docs
description: JavaScript를 사용하여 함수를 개발하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: tdykstra
ms.openlocfilehash: 78f29cd4a20861e40bb7f7f398979b8d93387a7b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 개발자 가이드

Azure Functions의 JavaScript 환경은 런타임과 통신하고 바인딩을 통해 데이터를 보내고 받는 `context` 개체를 전달하는 함수를 쉽게 내보낼 수 있도록 합니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다.

## <a name="exporting-a-function"></a>함수 내보내기
모든 JavaScript 함수는 런타임에 대한 `module.exports`를 통해 단일 `function`을 내보내 함수를 찾고 실행해야 합니다. 이 함수에는 `context` 개체가 항상 포함되어야 합니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

`direction === "in"`의 바인딩은 함수 인수로 전달됩니다. 즉, [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx)를 사용하여 동적으로 새 입력을 처리할 수 있습니다(예: 모든 입력에 대해 반복되는 `arguments.length` 사용). 이 기능은 `context` 개체를 참조하지 않고 트리거 데이터에 예측 가능한 방식으로 액세스할 수 있으므로 트리거만 있고 추가 입력이 없는 경우에 편리합니다.

내보내기 문에 인수를 지정하지 않은 경우에도 *function.json*에서 발생하는 순서에 따라 인수가 항상 함수에 전달됩니다. 예를 들어 `function(context, a, b)`을 `function(context, a)`으로 변경하는 경우 `arguments[2]`를 참조하여 여전히 함수 코드의 `b` 값을 가져올 수 있습니다.

또한 방향에 관계없이 모든 바인딩은 `context` 개체로 전달됩니다(다음 스크립트 참조). 

## <a name="context-object"></a>context 개체
런타임은 함수로 데이터를 전달하거나 전달받으며 사용자가 런타임과 통신할 수 있도록 하는 `context` 개체를 사용합니다.

`context` 개체는 항상 함수에 대한 첫 번째 매개 변수이며, 런타임을 올바르게 사용하는 데 필요한 `context.done` 및 `context.log`와 같은 메서드를 가지고 있으므로 포함되어야 합니다. 원하는 개체 이름(예: `ctx` 또는 `c`)을 지정할 수 있습니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>context.bindings 속성

```
context.bindings
```
모든 입력 및 출력 데이터를 포함하는 명명된 개체를 반환합니다. 예를 들어 *function.json*의 다음 바인딩 정의를 사용하면 `context.bindings.myInput` 개체에서 큐의 콘텐츠에 액세스할 수 있습니다. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>context.done 메서드
```
context.done([err],[propertyBag])
```

코드가 완료되었음을 런타임에 알립니다. `context.done`을 호출해야 하나 그렇지 않으면 런타임에서 함수가 완료되었음을 알 수 없으므로 실행 시간이 초과됩니다. 

`context.done` 메서드를 사용하면 `context.bindings` 개체의 속성을 덮어쓸 속성의 속성 모음뿐만 아니라 사용자 정의 오류도 런타임에 다시 전달할 수 있습니다.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>context.log 메서드  

```
context.log(message)
```
기본 추적 수준에서 스트리밍 콘솔 로그에 기록할 수 있습니다. `context.log`에서 다른 추적 수준에서 콘솔 로그에 쓸 수 있는 추가 로깅 메서드가 제공됩니다.


| 방법                 | 설명                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 오류 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.   |
| **warn(_message_)**    | 경고 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다. |
| **info(_message_)**    | 정보 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.    |
| **verbose(_message_)** | 자세한 정보 표시 수준 로깅에 씁니다.           |

다음 예제는 경고 추적 수준에서 콘솔에 씁니다.

```javascript
context.log.warn("Something has happened."); 
```
host.json 파일에 로그인하기 위한 추적 수준 임계값을 설정하거나 해제할 수 있습니다.  로그에 쓰는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.

## <a name="binding-data-type"></a>바인딩 데이터 형식

입력 바인딩에 대한 데이터 형식을 정의하려면 바인딩 정의에서 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `binary` 형식을 사용합니다.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType`에 대한 다른 옵션은 `stream` 및 `string`입니다.

## <a name="writing-trace-output-to-the-console"></a>콘솔에 추적 출력 작성 

Functions에서 `context.log` 메서드를 사용하여 추적 출력을 콘솔에 씁니다. 이 시점에서는 `console.log`를 사용하여 콘솔에 쓸 수 없습니다.

`context.log()`를 호출하면 메시지를 _정보_ 추적 수준인 기본 추적 수준에서 콘솔에 씁니다. 다음 코드는 정보 추적 수준에서 콘솔에 씁니다.

```javascript
context.log({hello: 'world'});  
```

이전 코드는 다음 코드와 동일합니다.

```javascript
context.log.info({hello: 'world'});  
```

다음 코드는 오류 수준에서 콘솔에 씁니다.

```javascript
context.log.error("An error has occurred.");  
```

_error_(오류)가 가장 높은 추적 수준이므로 로깅이 활성화되어 있는 한 이 추적은 모든 추적 수준에서 출력에 씁니다.  


모든 `context.log` 메서드는 Node.js [util.format 메서드](https://nodejs.org/api/util.html#util_util_format_format)에서 지원하는 것과 동일한 매개 변수 형식을 지원합니다. 기본 추적 수준을 사용하여 콘솔에 쓰는 다음 코드를 살펴보세요.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

또한 동일한 코드를 다음과 같은 형식으로 작성할 수도 있습니다.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>콘솔 로깅에 대한 추적 수준 구성

Functions를 통해 콘솔에 쓸 임계값 추적 수준을 정의할 수 있으므로 추적을 함수에서 콘솔로 쓰는 방식을 쉽게 제어할 수 있습니다. 콘솔에 기록되는 모든 추적에 대한 임계값을 설정하려면 host.json 파일의 `tracing.consoleLevel` 속성을 사용합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다. 다음 예제에서는 추적 임계값을 설정하여 자세한 정보 표시 로깅을 사용하도록 설정합니다.

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

**consoleLevel**의 값은 `context.log` 메서드의 이름에 해당합니다. 콘솔에 대한 모든 추적 로깅을 사용하지 않으려면 **consoleLevel**을 _off_로 설정합니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

## <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.  

### <a name="request-object"></a>요청 개체

`request` 개체의 속성은 다음과 같습니다.

| 자산      | 설명                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 요청의 본문을 포함하는 개체입니다.               |
| _headers_     | 요청 헤더를 포함하는 개체입니다.                   |
| _method_      | 요청의 HTTP 메서드입니다.                                |
| _originalUrl_ | 요청의 URL입니다.                                        |
| _params_      | 요청의 라우팅 매개 변수를 포함하는 개체입니다. |
| _query_       | 쿼리 매개 변수를 포함하는 개체입니다.                  |
| _rawBody_     | 문자열 형식의 메시지 본문입니다.                           |


### <a name="response-object"></a>응답 개체

`response` 개체의 속성은 다음과 같습니다.

| 자산  | 설명                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 응답의 본문을 포함하는 개체입니다.         |
| _headers_ | 응답 헤더를 포함하는 개체입니다.             |
| _isRaw_   | 응답에 대한 서식 지정을 건너뜀을 나타냅니다.    |
| _상태_  | 응답의 HTTP 상태 코드입니다.                     |

### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스 

HTTP 트리거로 작업할 때 세 가지 방법으로 HTTP 요청 및 응답 개체에 액세스할 수 있습니다.

+ 명명된 입출력 바인딩에서. 이러한 방식으로 HTTP 트리거와 바인딩은 다른 바인딩과 동일하게 작동합니다. 다음 예제에서는 명명된 `response` 바인딩을 사용하여 응답 개체를 설정합니다. 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ `context` 개체의 `req` 및 `res` 속성에서. 이러한 방식으로 전체 `context.bindings.name` 패턴을 사용하지 않고 대신 기존 패턴을 사용하여 context 개체에서 HTTP 데이터에 액세스할 수 있습니다. 다음 예제에서는 `context`의 `req` 및 `res` 개체에 액세스하는 방법을 보여 줍니다.

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ `context.done()`을 호출합니다. `context.done()` 메서드에 전달되는 응답을 반환하는 특별한 종류의 HTTP 바인딩이 있습니다. 다음 HTTP 출력 바인딩은 `$return` 출력 매개 변수를 정의합니다.

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    이 출력 바인딩은 다음과 같이 `done()`을 호출할 때 응답을 제공할 수 있어야 합니다.

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>노드 버전 및 패키지 관리

다음 표에서는 주 버전의 Functions 런타임 각각에서 사용되는 Node.js 버전을 보여 줍니다.

| Functions 버전 | Node.js 버전 | 
|---|---|
| 1.x | 6.11.2(런타임에 의해 잠김) |
| 2.x  |8.4.0 이상(현재 LTS 8.9.4가 권장됨). WEBSITE_NODE_DEFAULT_VERSION [앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 사용하여 버전을 설정합니다.|

함수에서 `process.version`을 인쇄하여 해당 런타임이 사용하는 현재 버전을 볼 수 있습니다.

다음 단계를 사용하면 함수 앱에 패키지를 포함할 수 있습니다. 

1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔** > **CMD**를 클릭합니다.

3. `D:\home\site\wwwroot`로 이동한 다음 package.json 파일을 페이지 위쪽의 **wwwroot** 폴더로 끌어갑니다.  
    다른 방법으로 함수 앱에 파일을 업로드할 수도 있습니다. 자세한 내용은 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)을 참조하세요. 

4. package.json 파일을 업로드한 후 **Kudu 원격 실행 콘솔**에서 `npm install` 명령을 실행합니다.  
    이 작업은 package.json 파일에 표시된 패키지를 다운로드하고 함수 앱을 다시 시작합니다.

필요한 패키지가 설치되면 다음 예제와 같이 `require('packagename')`을 호출하여 함수로 가져옵니다.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

함수 앱의 루트에 `package.json` 파일을 정의해야 합니다. 파일을 정의하면 앱의 모든 함수에서 동일한 캐시된 패키지를 공유할 수 있으므로 최상의 성능을 제공합니다. 버전 충돌이 발생하는 경우 특정 함수의 폴더에 `package.json` 파일을 추가하여 이 충돌을 해결할 수 있습니다.  

## <a name="environment-variables"></a>환경 변수
환경 변수 또는 앱 설정 값을 가져오려면 여기에 표시된 `GetEnvironmentVariable` 함수와 같이 `process.env`를 사용합니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>JavaScript 함수에 대한 고려 사항

JavaScript 함수로 작업하는 경우 다음 두 섹션에서 고려 사항을 알아야 합니다.

### <a name="choose-single-vcpu-app-service-plans"></a>단일 vCPU App Service 계획 선택

App Service 계획을 사용하는 함수 앱을 만들 때 여러 vCPU가 있는 계획보다는 단일 vCPU 계획을 선택하는 것이 좋습니다. 현재 Functions는 단일 vCPU VM에서 JavaScript 함수를 더 효율적으로 실행합니다. 더 큰 VM을 사용해도 예상된 성능 향상을 보여 주지 않습니다. 필요한 경우 더 많은 단일 vCPU VM 인스턴스를 추가하여 수동으로 확장하거나 자동 크기 조정을 사용하도록 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)을 참조하세요.    

### <a name="typescript-and-coffeescript-support"></a>TypeScript 및 CoffeeScript 지원
아직 런타임을 통해 TypeScript 또는 CoffeeScript 자동 컴파일에 대한 직접 지원이 없으므로 배포 시 런타임 외부에서 이러한 지원이 처리되어야 합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

