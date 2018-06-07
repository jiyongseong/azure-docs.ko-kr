---
title: Azure Functions의 트리거 및 바인딩
description: Azure Functions에서 트리거 및 바인딩을 사용하여 코드 실행을 온라인 이벤트 및 클라우드 기반 서비스에 연결하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: tdykstra
ms.openlocfilehash: 56b0f8e24dfc38b542f4bbfc7975f1704d70f22c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

이 문서는 Azure Functions의 트리거 및 바인딩에 대한 개념적 개요를 제공합니다. 여기에서는 모든 바인딩 및 지원되는 모든 언어에 공통되는 기능을 설명합니다.

## <a name="overview"></a>개요

*트리거*는 함수가 호출되는 방식을 정의합니다. 함수에는 정확히 하나의 트리거만 있어야 합니다. 트리거는 관련 데이터가 있으며, 이 데이터는 일반적으로 함수를 트리거한 페이로드입니다.

입력 및 출력 *바인딩*은 코드에서 데이터에 연결하기 위한 선언적 방식을 제공합니다. 바인딩은 선택 사항이며 함수는 여러 개의 입력 및 출력 바인딩을 가질 수 있습니다. 

트리거 및 바인딩을 통해 작업하는 서비스 세부 정보의 하드 코딩을 방지할 수 있습니다. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값, `out` 매개 변수 또는 [collector 개체](functions-reference-csharp.md#writing-multiple-output-values)를 사용하여 데이터를 보냅니다(예: 큐 메시지를 만들기 위해).

Azure Portal을 사용하여 함수를 개발하는 경우 트리거 및 바인딩은 *function.json* 파일에서 구성됩니다. 포털은 이 구성에 대한 UI를 제공하지만 **고급 편집기**로 변경하여 파일을 직접 편집할 수 있습니다.

클래스 라이브러리를 만들기 위해 Visual Studio를 사용하여 함수를 개발하는 경우 특성으로 메서드 및 매개 변수를 데코레이트하여 트리거 및 바인딩을 구성합니다.

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="register-binding-extensions"></a>바인딩 확장 등록

Azure Functions 런타임의 2.x 버전에서는 함수 앱에서 사용하는 [바인딩 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md)을 명시적으로 등록해야 합니다. 

확장은 패키지 이름이 일반적으로 [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions)로 시작하는 NuGet 패키지로 제공됩니다.  바인딩 확장을 설치하고 등록하는 방법은 함수를 개발하는 방법에 따라 달라집니다. 

+ [Visual Studio 또는 VS Code를 사용하여 C#에서 로컬로](#local-c-development-using-visual-studio-or-vs-code)
+ [Azure Functions 핵심 도구를 사용하여 로컬로](#local-development-azure-functions-core-tools)
+ [Azure Portal에서](#azure-portal-development) 

2.x 버전에는 확장으로 제공되지 않는 바인딩 확장 핵심 집합이 있습니다. HTTP, 타이머 및 Azure Storage와 같은 트리거 및 바인딩에 대한 확장을 등록할 필요가 없습니다. 

2.x 버전의 Functions 런타임을 사용하도록 함수 앱을 설정하는 방법에 대한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요. Functions 런타임의 버전 2.x는 현재 미리 보기로 제공됩니다. 

이 섹션에 표시된 패키지 버전은 예제로만 제공됩니다. 함수 앱의 다른 종속성에 필요한 지정된 확장 버전은 [NuGet.org 사이트](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions)에서 확인하세요.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Visual Studio 또는 VS Code를 사용한 로컬 C# 개발 

Visual Studio 또는 Visual Studio Code를 사용하여 C#에서 로컬로 함수를 개발하려면 확장을 위한 NuGet 패키지를 추가하기만 하면 됩니다. 

+ **Visual Studio**: NuGet 패키지 관리자 도구를 사용합니다. 다음 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 명령은 패키지 관리자 콘솔에서 Azure Cosmos DB 확장 프로그램을 설치합니다.

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: 다음과 같이 .NET CLI에서 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용하여 명령 프롬프트에서 패키지를 설치할 수 있습니다.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Azure Functions 핵심 도구 로컬 개발

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Azure Portal 개발

함수를 만들거나 기존 함수에 바인딩을 추가할 때 추가되는 트리거 또는 바인딩에 대한 확장에 등록이 필요한 경우 메시지가 표시됩니다.   

설치되는 특정 확장에 대한 경고가 나타나면 **설치**를 클릭하여 확장을 등록할 수 있습니다. 지정된 함수 앱에 대해 각 확장을 한 번만 등록하면 됩니다. 

>[!Note] 
>포털 내 설치 프로세스는 소비 계획에 대해 최대 10분이 소요될 수 있습니다.

## <a name="example-trigger-and-binding"></a>예제 트리거 및 바인딩

Azure Queue 저장소에 새 메시지가 나타날 때마다 Azure Table 저장소에 새 행을 쓰려는 경우를 가정하겠습니다. 이 시나리오는 Azure Queue 저장소 트리거 및 Azure Table 저장소 출력 바인딩을 사용하여 구현할 수 있습니다. 

다음은 이 시나리오에 대한 *function.json* 파일입니다. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 배열의 첫 번째 요소는 큐 저장소 트리거입니다. `type` 및 `direction` 속성은 트리거를 식별합니다. `name` 속성은 큐 메시지 콘텐츠를 받는 함수 매개 변수를 식별합니다. 모니터링하는 큐 이름은 `queueName`에 있으며 연결 문자열은 `connection`으로 식별되는 앱 설정에 있습니다.

`bindings` 배열의 두 번째 요소는 Azure Table Storage 출력 바인딩입니다. `type` 및 `direction` 속성은 바인딩을 식별합니다. `name` 속성은 함수가 새 테이블 행을 제공하는 방법을 지정하며 이 경우 함수 반환 값을 사용합니다. 테이블의 이름은 `tableName`에 있으며 연결 문자열은 `connection`으로 식별되는 앱 설정에 있습니다.

Azure Portal에서 *function.json*의 내용을 보고 편집하려면 함수의 **통합** 탭에서 **고급 편집기**를 클릭합니다.

> [!NOTE]
> `connection`의 값은 연결 문자열 자체가 아닌 연결 문자열을 포함하는 앱 설정의 이름입니다. 바인딩은 *function.json*에 서비스 비밀이 포함되지 않은 모범 사례를 실행하기 위해 앱 설정에 저장된 연결 문자열을 사용합니다.

이 트리거 및 바인딩을 사용하는 C# 스크립트 코드는 다음과 같습니다. 큐 메시지 콘텐츠를 제공하는 매개 변수 이름은 `order`이며 *function.json*의 `name` 속성 값은 `order`이므로 이 이름이 필요합니다. 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

동일한 function.json 파일을 JavaScript 함수로 사용할 수 있습니다.

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

클래스 라이브러리에서 동일한 트리거 및 바인딩 정보(큐 및 테이블 이름, 저장소 계정, 입력 및 출력에 대한 함수 매개 변수)는 function.json 파일 대신 특성에 의해 제공됩니다. 예를 들면 다음과 같습니다.

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>바인딩 방향

모든 트리거와 바인딩은 *function.json* 파일에 `direction` 속성이 있습니다.

- 트리거의 경우 방향은 언제나 `in`입니다
- 입력 및 출력 바인딩은 `in`과 `out`을 사용합니다
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. `inout`을 사용할 경우 **통합** 탭에서 **고급 편집기**만 사용할 수 있습니다.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="using-the-function-return-value"></a>함수 반환 값 사용

반환 값이 있는 언어에서 출력 바인딩을 반환 값에 바인딩할 수 있습니다.

* C# 클래스 라이브러리에서 출력 바인딩 특성을 메서드 반환 값에 적용합니다.
* 다른 언어에서 *function.json*의 `name` 속성을 `$return`에 설정합니다.

한 항목 초과를 작성해야 하는 경우 반환 값 대신 [collector 개체](functions-reference-csharp.md#writing-multiple-output-values)를 사용합니다. 여러 개의 출력 바인딩이 있으면 둘 중 하나에 대한 반환 값을 사용합니다.

언어 관련 예제를 참조하세요.

* [C#](#c-example)
* [C# 스크립트(.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# 예제

다음은 출력 바인딩에 대한 반환 값을 사용하는 C# 코드이며, 그 뒤에 비동기 예제가 나와 있습니다.

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>C# 스크립트 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

다음은 C# 스크립트 코드로, 그 뒤에 비동기 예제가 나와 있습니다.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F# 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

F# 코드는 다음과 같습니다.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>JavaScript 예제

*function.json* 파일의 출력 바인딩은 다음과 같습니다.

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

JavaScript에서 반환 값은 `context.done`에 대한 두 번째 매개 변수로 이어집니다.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>dataType 속성 바인딩

.NET에서는 매개 변수 형식을 사용하여 입력 데이터에 대한 데이터 형식을 정의합니다. 예를 들어 `string`을 사용하여 큐 트리거의 텍스트, 이진으로 읽을 바이트 배열 및 POCO 개체로 직렬화할 사용자 지정 형식에 바인딩합니다.

JavaScript와 같은 동적으로 형식화되는 언어의 경우 *function.json* 파일의 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `dataType`을 `binary`로 설정합니다.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType`에 대한 다른 옵션은 `stream` 및 `string`입니다.

## <a name="binding-expressions-and-patterns"></a>바인딩 식 및 패턴

트리거와 바인딩의 가장 강력한 기능 중 하나는 *바인딩 식*입니다. *function.json* 파일에서 그리고 함수 매개 변수 및 코드에서 다양한 원본의 값을 확인하는 식을 사용할 수 있습니다.

대부분의 식은 중괄호로 래핑하여 식별됩니다. 예를 들어 큐 트리거 함수에서 `{queueTrigger}`는 큐 메시지 텍스트를 확인합니다. blob 출력 바인딩에 대한 `path` 속성이 `container/{queueTrigger}`이고 함수가 큐 메시지 `HelloWorld`에 의해 트리거되는 경우 `HelloWorld`라는 blob이 만들어집니다.

바인딩 식의 형식

* [앱 설정](#binding-expressions---app-settings)
* [트리거 파일 이름](#binding-expressions---trigger-file-name)
* [트리거 메타데이터](#binding-expressions---trigger-metadata)
* [JSON 페이로드](#binding-expressions---json-payloads)
* [새 GUID](#binding-expressions---create-guids)
* [현재 날짜 및 시간](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>바인딩 식 - 앱 설정

비밀과 연결 문자열은 구성 파일이 아닌 앱 설정을 사용하여 관리하는 것이 가장 좋습니다. 그럴 경우 이러한 비밀에 대한 액세스가 제한되고 *function.json*과 같은 파일을 공용 원본 제어 리포지토리에 안전하게 저장할 수 있습니다.

환경을 기준으로 구성을 변경하려는 경우에도 앱 설정이 유용합니다. 예를 들어 테스트 환경에서 다른 큐 또는 Blob Storage 컨테이너를 모니터링할 수 있습니다.

앱 설정 바인딩 식은 다른 바인딩 식과는 다르게 식별됩니다. 중괄호 대신 백분율 기호로 래핑됩니다. 예를 들어 blob 출력 바인딩 경로가 `%Environment%/newblob.txt`이고 `Environment` 앱 설정 값이 `Development`인 경우 blob은 `Development` 컨테이너에 생성됩니다.

함수를 로컬로 실행 중인 경우 앱 설정 값은 *local.settings.json* 파일에서 가져옵니다.

트리거 및 바인딩의 `connection` 속성은 특수한 경우이며, 백분율 기호 없이 앱 설정으로 값을 자동 확인합니다. 

다음 예제는 `%input-queue-name%` 앱 설정을 사용하여 트리거할 큐를 정의하는 Azure Queue Storage 트리거입니다.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

클래스 라이브러리에서 동일한 방법을 사용할 수 있습니다.

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>바인딩 식 - 트리거 파일 이름

Blob 트리거에 대한 `path`는 다른 바인딩 및 함수 코드에서 blob 트리거의 이름을 참조할 수 있도록 하는 패턴일 수 있습니다. 또한 패턴은 함수 호출을 트리거할 수 있는 blob을 지정하는 필터링 조건을 포함할 수 있습니다.

예를 들어, 다음 Blob 트리거 바인딩에서 `path` 패턴은 `sample-images/{filename}`으로, `filename`이라는 바인딩 식을 만듭니다.

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

그런 다음, 식 `filename`은 출력 바인딩에서 생성되는 blob의 이름을 지정하는 데 사용될 수 있습니다.

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

함수 코드는 매개 변수 이름으로 `filename`을 사용하여 이 동일한 값에 액세스할 수 있습니다.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

바인딩 식 및 패턴을 사용하는 동일한 기능은 클래스 라이브러리의 특성에 적용됩니다. 다음 예제에서 특성 생성자 매개 변수는 앞의 *function.json* 예제와 동일한 `path` 값입니다. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

또한 확장과 같은 파일 이름 부분에 대한 식을 만들 수 있습니다. Blob 경로 문자열에서 식 및 패턴을 사용하는 방법에 대한 자세한 내용은 [저장소 blob 바인딩 참조](functions-bindings-storage-blob.md)를 확인하세요.
 
### <a name="binding-expressions---trigger-metadata"></a>바인딩 식 - 트리거 메타데이터

트리거가 제공한 데이터 페이로드(예: 함수를 트리거한 큐 메시지의 콘텐츠) 이외에 많은 트리거가 추가 메타데이터 값을 제공합니다. 이러한 값은 C# 및 F#에서 입력 매개 변수로 사용하거나 JavaScript에서 `context.bindings` 개체의 속성으로 사용할 수 있습니다. 

예를 들어 Azure Queue 저장소 트리거는 다음 속성을 지원합니다.

* QueueTrigger - 유효한 문자열인 경우 트리거 메시지 내용
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

이러한 메타데이터 값은 *function.json* 파일 속성에서 액세스할 수 있습니다. 예를 들어 큐 트리거를 사용하고 큐 메시지는 읽으려는 Blob의 이름을 포함한다고 가정합니다. *function.json* 파일에서 다음 예제와 같이 Blob `path` 속성에서 `queueTrigger` 메타데이터 속성을 사용할 수 있습니다.

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

각 트리거의 메타데이터 속성은 해당 참조 문서에서 자세히 설명되어 있습니다. 예를 들어 [큐 트리거 메타데이터](functions-bindings-storage-queue.md#trigger---message-metadata)를 참조하세요. 설명서는 Portal에서 **통합** 탭의 바인딩 구성 영역 아래 **설명서** 섹션에서도 참조할 수 있습니다.  

### <a name="binding-expressions---json-payloads"></a>바인딩 식 - JSON 페이로드

트리거 페이로드가 JSON인 경우 같은 함수 및 함수 코드의 다른 바인딩에 대한 구성에서 해당 속성을 참조할 수 있습니다.

다음 예제에 JSON에서 blob 이름을 수신하는 웹후크 함수에 대한 *function.json* 파일이 나와 있습니다(`{"BlobName":"HelloWorld.txt"}`). Blob 입력 바인딩은 blob을 읽고, HTTP 출력 바인딩은 HTTP 응답에서 blob 콘텐츠를 반환합니다. Blob 입력 바인딩은 `BlobName` 속성(`"path": "strings/{BlobName}"`)을 직접 참조하여 blob 이름을 가져옵니다.

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

이렇게 하려면 C# 및 F#에서 다음 예제와 같이 deserialize할 필드를 정의하는 클래스가 필요합니다.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript에서 JSON deserialization은 자동으로 실행됩니다.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

#### <a name="dot-notation"></a>점 표기법

JSON 페이로드의 속성 중 일부가 속성을 가진 개체인 경우 점 표기법을 사용하여 직접 참조할 수 있습니다. 예를 들어 JSON이 다음과 같다고 가정합니다.

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

직접 `FileName`을 `BlobName.FileName`으로 참조할 수 있습니다. 이 JSON 형식을 사용할 때 이전 예제에서의 `path` 속성은 다음과 같습니다.

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

C#에서 다음과 같은 두 개의 클래스가 필요합니다.

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>바인딩 식 - GUID 만들기

`{rand-guid}` 바인딩 식은 GUID를 만듭니다. `function.json` 파일의 다음 blob 경로는 *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*와 같은 이름의 blob을 만듭니다.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>바인딩 식 - 현재 시간

바인딩 식 `DateTime`은 `DateTime.UtcNow`로 확인됩니다. `function.json` 파일의 다음 blob 경로는 *2018-02-16T17-59-55Z.txt*와 같은 이름의 blob을 만듭니다.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>런타임에 바인딩

C# 및 기타 .NET 언어에서는 *function.json* 및 특성의 바인딩과 달리 명령적 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 바인딩 매개 변수를 디자인 타임이 아닌 런타임에 계산해야 할 경우 유용합니다. 자세한 내용은 [C# 개발자 참조](functions-dotnet-class-library.md#binding-at-runtime) 또는 [C# 스크립트 개발자 참조](functions-reference-csharp.md#binding-at-runtime)를 확인하세요.

## <a name="functionjson-file-schema"></a>function.json 파일 스키마

*function.json* 파일 스키마는 [http://json.schemastore.org/function](http://json.schemastore.org/function)에서 제공됩니다.

## <a name="handling-binding-errors"></a>바인딩 오류 처리

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

함수에서 지원하는 다양한 서비스와 관련된 모든 오류 항목은 [Azure Functions 오류 처리](functions-bindings-error-pages.md) 개요 항목의 [바인딩 오류 코드](functions-bindings-error-pages.md#binding-error-codes) 섹션을 참조하세요.  

## <a name="next-steps"></a>다음 단계

특성 바인딩에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [HTTP 및 webhook](functions-bindings-http-webhook.md)
- [타이머](functions-bindings-timer.md)
- [Queue storage](functions-bindings-storage-queue.md)
- [Blob storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [이벤트 허브](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [외부 파일](functions-bindings-external-file.md)
