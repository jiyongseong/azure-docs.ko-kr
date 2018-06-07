---
title: Azure Functions에 대한 Java 개발자 참조 | Microsoft Docs
description: Java로 함수를 개발하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 함수, 이벤트 처리, 웹후크, 동적 계산, 서버를 사용하지 않는 아키텍처, Java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 3f63cb5a16b74458f9b53fddaea13a61ec1196a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

## <a name="programming-model"></a>프로그래밍 모델 

Azure 함수는 입력을 처리하고 출력을 생성하는 상태 비저장 클래스 메서드여야 합니다. 인스턴스 메서드를 작성할 수는 있지만 클래스의 인스턴스 필드에 종속되지 않아야 합니다. 모든 함수 메서드에는 `public` 액세스 한정자가 있어야 합니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

일반적으로 Azure 함수는 외부 트리거로 인해 호출됩니다. 함수는 해당 트리거 및 연결된 입력을 처리하고 하나 이상의 출력을 생성해야 합니다.

Java 주석은 `azure-functions-java-core` 패키지에 포함되어 입력 및 출력을 메서드에 바인딩합니다. 지원되는 입력 트리거와 출력 바인딩 주석은 다음 표에 포함되어 있습니다.

바인딩 | 주석
---|---
CosmosDB | 해당 없음
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | 해당 없음
Notification Hubs | 해당 없음
저장소 Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
저장소 큐 | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
저장소 테이블 | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
타이머 | <ul><li>`TimerTrigger`</li></ul>
Twilio | 해당 없음

트리거 입력 및 출력은 응용 프로그램에 대한 [function.json](/azure/azure-functions/functions-reference#function-code)에서도 정의할 수 있습니다.

> [!IMPORTANT] 
> Azure Storage Blob, 큐 또는 테이블 트리거를 로컬로 실행하려면 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)에서 Azure Storage 계정을 구성해야 합니다.

주석을 사용 하는 예제:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

주석 없이 작성된 동일한 함수:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

해당하는 `function.json` 사용:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>데이터 형식

입력 및 출력 데이터에 대해 네이티브 형식, 사용자 지정 Java 형식 및 `azure-functions-java-core` 패키지에 정의된 Azure 특수 형식을 포함하여 Java의 모든 데이터 형식을 자유롭게 사용할 수 있습니다. Azure Functions 런타임에서는 받은 입력을 코드에서 요청한 형식으로 변환합니다.

### <a name="strings"></a>문자열

함수의 해당 입력 매개 변수 형식이 `String` 형식인 경우 함수 메서드에 전달된 값은 문자열로 캐스팅됩니다. 

### <a name="plain-old-java-objects-pojos"></a>이전의 일반 Java 개체(POJO)

함수 메서드의 입력이 해당 Java 형식을 예상하는 경우 JSON 형식으로 지정된 문자열이 Java 형식으로 캐스팅됩니다. 이 변환을 사용하면 사용자 고유의 코드에서 변환을 구현하지 않고도 JSON 입력을 함수에 전달하고 코드에서 Java 형식으로 작업할 수 있습니다.

함수에 대한 입력으로 사용되는 POJO 형식은 사용 중인 함수 메서드와 동일한 `public` 액세스 한정자여야 합니다. `public` POJO 클래스 필드를 선언할 필요가 없습니다. 예를 들어 `{ "x": 3 }` JSON 문자열은 다음 POJO 형식으로 변환될 수 있습니다.

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>이진 데이터

이진 데이터는 Azure Functions 코드에서 `byte[]`로 표시됩니다. function.json의 `dataType` 필드를 `binary`로 설정하여 이진 입력 또는 출력을 함수에 바인딩합니다.

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

그런 다음 함수 코드에서 사용합니다.

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

`OutputBinding<byte[]>` 형식을 사용하여 이진 출력 바인딩을 만듭니다.


## <a name="function-method-overloading"></a>함수 메서드 오버로드

동일한 이름이지만 다른 형식의 함수 메서드를 오버로드할 수 있습니다. 예를 들어 하나의 클래스에서 `String echo(String s)`과 `String echo(MyType s)`를 모두 포함할 수 있으며 Azure Functions 런타임에서 실제 입력 형식을 검사하여 호출할 메서드를 결정합니다(HTTP 입력의 경우 `text/plain` MIME 형식은 `String`, `application/json`은 `MyType`을 나타냄).

## <a name="inputs"></a>입력

입력은 Azure Functions에서 두 가지 범주로 나뉩니다. 즉 하나는 트리거 입력이고, 다른 하나는 추가 입력입니다. `function.json`에서는 사용법이 서로 다르지만, Java 코드에서는 동일합니다. 예를 들어 다음 코드 조각을 살펴보겠습니다.

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` 주석에는 `function.json`에 정의된 바인딩/트리거의 이름을 나타내는 `String` 속성이 허용됩니다.

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

따라서 이 함수가 호출되면 HTTP 요청 페이로드에서 `in` 인수에 대한 선택적 `String` 및 `obj` 인수에 전달된 Azure Table Storage `MyObject` 형식을 전달합니다. `Optional<T>` 유형을 사용하여 null이 될 수 있는 함수에 대한 입력을 처리합니다.

## <a name="outputs"></a>outputs

출력은 반환 값 또는 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

반환 값은 가장 간단한 형식의 출력입니다. 즉 모든 형식의 값을 반환하고 Azure Functions 런타임에서 이 반환 값을 실제 형식(예: HTTP 응답)으로 다시 마샬링합니다. `functions.json`에서는 출력 바인딩의 이름으로 `$return`을 사용합니다.

여러 출력 값을 생성하려면 `azure-functions-java-core` 패키지에 정의된 `OutputBinding<T>` 형식을 사용합니다. HTTP 응답을 만들고 메시지도 큐로 푸시해야 하는 경우 다음과 같이 작성할 수 있습니다.

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

여기에서 `function.json`에 출력 바인딩을 정의해야 합니다.

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>특수 형식

함수는 경우에 따라 입력과 출력을 자세히 제어해야 합니다. 요청 정보를 조작하고 HTTP 트리거의 반환 상태에 맞게 조정할 수 있도록 `azure-functions-java-core` 패키지의 특수 형식이 제공됩니다.

| 특수 형식      |       대상        | 일반적 용도                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리 가져오기 |
| `HttpResponseMessage<T>` | HTTP 출력 바인딩 | 200 이외의 반환 상태   |

> [!NOTE] 
> `@BindingName` 주석을 사용하여 HTTP 헤더 및 쿼리를 가져올 수도 있습니다. 예를 들어 `@BindingName("name") String query`는 HTTP 요청 헤더와 쿼리를 반복하고 해당 값을 메서드에 전달합니다. 예를 들어 요청 URL이 `http://example.org/api/echo?name=test`이면 `query`는 `"test"`가 됩니다.

### <a name="metadata"></a>Metadata

메타데이터는 HTTP 헤더, HTTP 쿼리 및 [트리거 메타데이터](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties)와 같은 다양한 원본에서 가져옵니다. 값을 얻으려면 메타데이터 이름과 함께 `@BindingName` 주석을 사용합니다.

예를 들어 요청된 URL이 `http://{example.host}/api/metadata?name=test`이면 다음 코드 조각의 `queryValue`는 `"test"`가 됩니다.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>함수 실행 컨텍스트

`azure-functions-java-core` 패키지에 정의된 `ExecutionContext` 개체를 통해 Azure Functions 실행 환경과 상호 작용합니다. 코드에서 호출 정보와 함수 런타임 정보를 사용하려면 `ExecutionContext` 개체를 사용합니다.

### <a name="logging"></a>로깅

Azure Functions 런타임 로거에 대한 액세스는 `ExecutionContext` 개체를 통해 사용할 수 있습니다. 이 로거는 Azure 모니터에 연결되어 있으며, 함수를 실행하는 동안 발생하는 경고 및 오류에 대한 플래그를 지정할 수 있습니다.

다음 예제 코드에서는 받은 요청 본문이 비어 있을 때 경고 메시지를 기록합니다.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>환경 변수

보안상의 이유로 소스 코드에서 암호 정보를 추출하는 것이 좋습니다. 그러면 실수로 다른 개발자에게 자격 증명을 제공하지 않고 소스 코드 리포지토리에 코드를 게시할 수 있습니다. Azure Functions를 로컬로 실행할 때 및 함수를 Azure에 배포할 때 모두 환경 변수를 사용하여 이 작업을 수행할 수 있습니다.

로컬로 Azure Functions를 실행할 때 쉽게 환경 변수를 설정하려면 local.settings.json 파일에 이러한 변수를 추가하도록 선택할 수 있습니다. 변수가 함수 프로젝트의 루트 디렉터리에 없는 경우 새로 만듭니다. 파일은 다음과 같아야 합니다.

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

`values` 맵의 각 키/값 매핑은 런타임 시 환경 변수로 사용할 수 있고 `System.getenv("<keyname>")`(예: `System.getenv("AzureWebJobsStorage")`)를 호출하여 액세스할 수 있습니다. 추가 키/값 쌍을 추가하는 것이 좋습니다.

> [!NOTE]
> 이 방법을 사용하는 경우 파일을 커밋하지 않도록 local.settings.json을 리포지토리에 추가하여 파일을 무시하도록 합니다.

이제 이러한 환경 변수에 따라 코드를 사용하면 로컬로 테스트할 때 및 Azure에 배포할 때 코드가 동등하게 작동되도록 Azure Portal에 로그인하여 함수 앱 설정에서 동일한 키/값 쌍을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Visual Studio Code를 사용하여 Java Azure Functions 원격 디버그](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
