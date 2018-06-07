---
title: "Azure Functions의 외부 파일 바인딩(실험)"
description: "Azure Functions에서 외부 파일 바인딩 사용"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions 외부 파일 바인딩(실험)
이 문서에서는 Azure Functions에서 다른 SaaS 공급자(예: Dropbox 또는 Google Drive)로부터 파일을 조작하는 방법을 보여 줍니다. Azure Functions는 외부 파일에 대한 트리거, 입력 및 출력 바인딩을 지원합니다. 이러한 바인딩은 SaaS 공급자에 대한 API 연결을 만들거나 함수 앱의 리소스 그룹에서 기존 API 연결을 사용합니다.

> [!IMPORTANT]
> 외부 파일 바인딩은 실험 버전이므로, 절대 GA(일반 공급) 상태가 될 수 없습니다. 이러한 항목은 Azure Functions 1.x에만 포함되어 있으며, Azure Functions 2.x에 추가할 계획이 없습니다. SaaS 공급자의 데이터에 액세스해야 하는 시나리오의 경우 [함수를 호출하는 Logic Apps](functions-twitter-email.md)를 사용하는 것이 좋습니다. [Logic Apps 파일 시스템 커넥터](../logic-apps/logic-apps-using-file-connector.md)를 참조하세요.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>사용 가능한 파일 연결

|커넥터|트리거|입력|출력|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google 드라이브](https://www.google.com/drive/)||x|x|

> [!NOTE]
> 외부 파일 연결은 [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)에서도 사용할 수 있습니다

## <a name="trigger"></a>트리거

외부 파일 트리거를 사용하면 원격 폴더를 모니터링하고 변경 사항이 감지될 때 함수 코드를 실행할 수 있습니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예제에서는 *function.json* 파일의 외부 파일 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 함수는 모니터링되는 폴더에 추가된 각 파일의 콘텐츠를 로깅합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예제에서는 *function.json* 파일의 외부 파일 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 함수는 모니터링되는 폴더에 추가된 각 파일의 콘텐츠를 로깅합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 설명|
|---------|---------|----------------------|
|**type** | `apiHubFileTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. | 
|**연결**| 연결 문자열을 저장하는 앱 설정을 식별합니다. 이 앱 설정은 Azure Portal의 통합 UI에서 연결을 추가할 때 자동으로 생성됩니다.|
|**path** | 모니터링할 폴더와 이름 패턴(선택 사항)입니다.|

### <a name="name-patterns"></a>이름 패턴

`path` 속성에서 파일 이름 패턴을 지정할 수 있습니다. 참조된 폴더는 SaaS 공급자에 있어야 합니다.

예제:

```json
"path": "input/original-{name}",
```

이 경로에는 *input* 폴더에서 *original-File1.txt*라는 파일이 있으며 함수 코드의 `name` 변수값은 `File1.txt`이 됩니다.

다른 예제:

```json
"path": "input/{filename}.{fileextension}",
```

또한 이 경로에는 *original-File1.txt*라는 파일이 있으며 함수 코드에 있는 `filename` 및 `fileextension` 변수값은 *original-File1* 및 *txt*입니다.

파일 확장명에 고정된 값을 사용하여 파일의 파일 형식을 제한할 수 있습니다. 예: 

```json
"path": "samples/{name}.png",
```

이 경우 *samples* 폴더의 *.png* 파일만 함수를 트리거합니다.

중괄호는 이름 패턴에서 특수 문자입니다. 이름에 중괄호가 있는 파일 이름을 지정하려면 이중 중괄호를 사용합니다.
예: 

```json
"path": "images/{{20140101}}-{name}",
```

이 경로에는 *images* 폴더에 *{20140101}-soundfile.mp3*라는 파일이 있으며 함수 코드에 있는 `name` 변수값은 *soundfile.mp3*입니다.

## <a name="trigger---usage"></a>트리거 - 사용

C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 파일 데이터를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [JSON 트리거](#trigger)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 파일 데이터에 액세스합니다.

다음 중 원하는 형식으로 파일을 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 파일 데이터에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `FooType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 파일 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 파일 데이터를 deserialize하려고 시도하게 됩니다.

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>트리거 - 포이즌 파일

외부 파일 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 파일에 대해 기본적으로 최대 5번(첫 번째 시도 포함) 다시 시도합니다.
5번 모두 실패할 경우 Functions는 메시지를 *webjobs-apihubtrigger-poison*이라는 Storage 큐에 추가합니다. 포이즌 파일에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*)
* FileType
* FolderName
* FileName
* ETag(파일 버전 식별자, 예: "0x8D1DC6E70A277EF")

## <a name="input"></a>입력

Azure 외부 파일 입력 바인딩을 사용하면 함수에 외부 폴더의 파일을 사용할 수 있습니다.

## <a name="input---example"></a>입력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>입력 - C# 스크립트 예제

다음 예제에서는 *function.json* 파일의 외부 파일 입력 및 출력 바인딩과 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 함수는 입력 파일을 출력 파일에 복사합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>입력 - JavaScript 예제

다음 예제에서는 *function.json* 파일의 외부 파일 입력 및 출력 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 함수는 입력 파일을 출력 파일에 복사합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>입력 - 구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 설명|
|---------|---------|----------------------|
|**type** | `apiHubFile`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. | 
|**연결**| 연결 문자열을 저장하는 앱 설정을 식별합니다. 이 앱 설정은 Azure Portal의 통합 UI에서 연결을 추가할 때 자동으로 생성됩니다.|
|**path** | 폴더 이름과 파일 이름이 포함되어야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 `samples-workitems` 폴더에서 트리거 메시지에 지정된 파일 이름과 일치하는 이름을 가진 파일을 가리킬 수 있습니다.   

## <a name="input---usage"></a>입력 - 사용

C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 파일 데이터를 바인딩합니다. `T`는 데이터를 deserialize할 데이터 형식이며, `name`은 입력 바인딩에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 파일 데이터에 액세스합니다.

다음 중 원하는 형식으로 파일을 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 파일 데이터에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `InputType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 파일 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 파일 데이터를 deserialize하려고 시도하게 됩니다.

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>출력

Azure 외부 파일 출력 바인딩을 사용하면 함수에 외부 폴더에 대한 파일을 쓸 수 있습니다.

## <a name="output---example"></a>출력 - 예제

[입력 바인딩 예제](#input---example)를 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 설명|
|---------|---------|----------------------|
|**type** | `apiHubFile`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. | 
|**연결**| 연결 문자열을 저장하는 앱 설정을 식별합니다. 이 앱 설정은 Azure Portal의 통합 UI에서 연결을 추가할 때 자동으로 생성됩니다.|
|**path** | 폴더 이름과 파일 이름이 포함되어야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 `samples-workitems` 폴더에서 트리거 메시지에 지정된 파일 이름과 일치하는 이름을 가진 파일을 가리킬 수 있습니다.   

## <a name="output---usage"></a>출력 - 사용

C# 함수에서 `out <T> <name>`같은 함수 시그니처의 명명된 `out` 매개 변수를 사용하여 출력 파일을 바인딩합니다. 여기서 `T`는 데이터를 serialize하려는 데이터 형식이며, `name`은 출력 바인딩에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 파일 데이터에 액세스합니다.

다음 형식 중 하나를 사용하여 출력 파일을 쓸 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화에 유용합니다.
  사용자 지정 출력 형식을 선언하는 경우(예: `out OutputType paramName`), Azure Functions에서 개체를 JSON으로 직렬화하려고 시도합니다. 함수가 종료될 때 출력 매개 변수가 null이면 Functions 런타임은 파일을 null 개체로 만듭니다.
* 문자열(`out string paramName`)은 텍스트 파일 데이터에 유용합니다. Functions 런타임은 함수가 종료될 때 문자열 매개 변수가 null이 아닌 경우에만 파일을 생성합니다.

C# 함수에서 다음 중 원하는 형식으로 출력할 수 있습니다.

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
