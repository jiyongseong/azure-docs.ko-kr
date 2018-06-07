---
title: Azure Functions C# 스크립트 개발자 참조
description: C# 스크립트를 사용하여 Azure Functions를 개발하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: 9de8119cbde486800639bc5f3559a1a2859ec204
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# 스크립트(.csx) 개발자 참조

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

이 문서는 C# 스크립트(*.csx*)를 사용하여 Azure Functions를 개발하는 방법을 소개합니다.

Azure Functions는 C# 및 C# 스크립트 프로그래밍 언어를 지원합니다. [Visual Studio 클래스 라이브러리 프로젝트에서 C#을 사용](functions-develop-vs.md)하기 위한 지침을 찾고 있는 경우 [C# 개발자 참조](functions-dotnet-class-library.md)를 참조하세요.

이 문서에서는 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다.

## <a name="how-csx-works"></a>.csx의 작동 원리

Azure Functions에 대한 C# 스크립트 환경은 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction)를 기준으로 합니다. 데이터는 메서드 인수를 통해 C# 함수로 흐릅니다. 인수 이름은 `function.json` 파일에 지정되며 함수 로거 및 취소 토큰 같은 항목에 액세스하기 위해 미리 정의된 이름이 있습니다.

*.csx* 형식을 사용하면 "상용구"를 덜 작성하고 C# 함수를 작성하는 데 집중할 수 있습니다. 네임스페이스 및 클래스의 모든 항목을 래핑하는 대신 `Run` 메서드만 정의합니다. 일반적으로 파일의 시작 부분에 모든 어셈블리 참조 및 네임스페이스를 포함합니다.

함수 앱의 *.csx* 파일은 인스턴스가 초기화될 때 컴파일됩니다. 이 컴파일 단계는 콜드 시작 등의 방식이 C# 클래스 라이브러리에 비해 C# 스크립트 함수를 실행하는 데 더 오래 걸릴 수 있음을 의미합니다. 이 컴파일 단계는 C# 스크립트 함수가 C# 클래스 라이브러리와 달리 Azure Portal에서 편집 가능한 이유를 나타내기도 합니다.

## <a name="binding-to-arguments"></a>인수에 바인딩

입력 또는 출력 데이터는 *function.json* 구성 파일의 `name` 속성을 통해 C# 스크립트 함수 매개 변수에 바인딩됩니다. 다음 예제에서는 큐 트리거 함수에 대한 *function.json* 파일 및 *run.csx* 파일을 보여 줍니다. 큐 메시지에서 데이터를 받는 매개 변수 이름은 `name` 속성의 값이기 때문에 `myQueueItem`으로 지정됩니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r` 문은 [이 문서 뒷부분에](#referencing-external-assemblies) 설명되어 있습니다.

## <a name="supported-types-for-bindings"></a>바인딩에 대해 지원되는 형식

각 바인딩에는 자체적인 지원 형식이 있습니다. 예를들 어, Blob 트리거는 문자열 매개 변수, POCO 매개 변수, `CloudBlockBlob` 매개 변수 또는 지원되는 기타 몇 가지 형식과 함께 사용될 수 있습니다. [Blob 바인딩에 대한 바인딩 참조 문서](functions-bindings-storage-blob.md#trigger---usage)에는 Blob 트리거에 대해 지원되는 모든 매개 변수 형식이 나와 있습니다. 자세한 내용은 [트리거 및 바인딩](functions-triggers-bindings.md) 및 [각 바인딩 형식에 대한 바인딩 참조 문서](functions-triggers-bindings.md#next-steps)를 참조하세요.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>사용자 지정 클래스 참조

사용자 지정 POCO(Plain Old CLR Object) 클래스를 사용해야 하는 경우 동일한 파일 내에 클래스 정의를 포함하거나 별도 파일에 추가할 수 있습니다.

다음 예제에서는 POCO 클래스 정의를 포함하는 *run.csx* 예를 보여 줍니다.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

POCO 클래스에는 각 속성에 대해 정의된 Getter 및 setter가 있어야 합니다.

## <a name="reusing-csx-code"></a>.csx 코드 재사용

*run.csx* 파일에 있는 다른 *.csx* 파일에 정의된 클래스와 메서드를 사용할 수 있습니다. 이를 위해 *run.csx* 파일의 `#load` 지시문을 사용합니다. 다음 예제에서는 `MyLogger`이라는 이름의 로깅 루틴이 *myLogger.csx*에서 공유되고 `#load` 지시문을 사용하여 *run.csx*로 로드됩니다.

예제 *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

예제 *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

공유된 *.csx* 파일을 사용하는 것은 POCO 개체를 사용하여 함수 간에 전달된 데이터에 강력한 형식을 지정하려는 경우에 일반적인 패턴입니다. 다음 간단한 예제에서는 HTTP 트리거 및 큐 트리거는 `Order`라는 이름의 POCO 개체를 공유하여 주문 데이터를 강력한 형식으로 만듭니다.

HTTP 트리거를 위한 *run.csx* 예제:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

큐 트리거를 위한 *run.csx* 예제:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

*order.csx* 예제:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

`#load` 지시문으로 상대 경로를 사용할 수 있습니다.

* `#load "mylogger.csx"` 는 함수 폴더에 있는 파일을 로드합니다.
* `#load "loadedfiles\mylogger.csx"` 는 함수 폴더의 폴더에 있는 파일을 로드합니다.
* `#load "..\shared\mylogger.csx"` 는 함수 폴더와 동일한 수준의 폴더 즉, *wwwroot*에 있는 파일을 로드합니다.

`#load` 지시문은 *.cs* 파일이 아닌 *.csx* 파일에서만 작동합니다.

## <a name="binding-to-method-return-value"></a>메서드 반환 값에 바인딩

*function.json*에서 이름 `$return`을 사용하여 출력 바인딩에 메서드 반환 값을 사용할 수 있습니다. 예제를 보려면 [트리거 및 바인딩](functions-triggers-bindings.md#using-the-function-return-value)을 참조하세요.

## <a name="writing-multiple-output-values"></a>여러 출력 값 쓰기

출력 바인딩에 여러 값을 쓰려면 [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 형식을 사용합니다. 이러한 형식은 메서드가 완료될 때 출력 바인딩에 기록되는 쓰기 전용 컬렉션입니다.

이 예제에서는 `ICollector`를 사용하여 동일한 큐에 여러 큐 메시지를 씁니다.

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>로깅

C#의 스트리밍 로그에 대한 출력을 기록하려면 `TraceWriter` 형식의 인수를 포함합니다. 이름을 `log`로 하는 것이 좋습니다. Azure Functions에서 `Console.Write`를 사용하지 마세요. 

`TraceWriter`는 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs)에 정의되어 있습니다. `TraceWriter`에 대한 로그 수준은 [host.json](functions-host-json.md)에서 구성할 수 있습니다.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> `TraceWriter` 대신 사용할 수 있는 최신 로깅 프레임워크에 대한 내용은 **Azure Functions 모니터링** 문서에서 [C# 함수로 로그 작성](functions-monitoring.md#write-logs-in-c-functions)을 참조하세요.

## <a name="async"></a>Async

[비동기화](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/) 함수를 만들려면 `async` 키워드를 사용하고 `Task` 개체를 반환합니다.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

## <a name="cancellation-tokens"></a>취소 토큰

함수는 함수가 종료될 때 운영 체제가 코드에 알릴 수 있게 해주는 [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 매개 변수를 사용할 수 있습니다. 이 알림을 통해 함수가 예기치 않게 종료되어 데이터가 일관되지 않은 상태가 되는 것을 방지할 수 있습니다.

다음 예제에서는 임박한 함수 종료를 확인하는 방법을 보여 줍니다.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>네임스페이스 가져오기

네임스페이스를 가져와야 하는 경우 `using` 절과 함께 정상적으로 수행할 수 있습니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

다음 네임스페이스를 자동으로 가져오므로 다음은 선택적입니다.

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>외부 어셈블리 참조

프레임워크 어셈블리의 경우 `#r "AssemblyName"` 지시문을 사용하여 참조를 추가합니다.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

다음 어셈블리는 Azure Functions 호스팅 환경에 의해 자동으로 추가됩니다.

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

다음 어셈블리는 단순한 이름(예: `#r "AssemblyName"`)으로 참조될 수 있습니다.

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>사용자 지정 어셈블리 참조

사용자 지정 어셈블리를 참조하려면 *공유* 어셈블리 또는 *전용* 어셈블리를 사용합니다.
- 공유 어셈블리는 함수 앱 내의 모든 함수에서 공유됩니다. 사용자 지정 어셈블리를 참조하려면 [함수 앱 루트 폴더](functions-reference.md#folder-structure)(wwwroot)의 `bin` 폴더에 해당 어셈블리를 업로드합니다. 
- 전용 어셈블리는 지정된 함수 컨텍스트의 일부이며 여러 버전의 테스트용 로드를 지원합니다. 전용 어셈블리는 함수 디렉터리의 `bin` 폴더에 업로드해야 합니다. `#r "MyAssembly.dll"`과 같은 파일 이름을 사용하여 어셈블리를 참조합니다. 

함수 폴더에 파일을 업로드하는 방법에 대한 내용은 [패키지 관리](#using-nuget-packages)에 대한 섹션을 참조하세요.

### <a name="watched-directories"></a>감시 디렉터리

함수 스크립트 파일을 포함하는 디렉터리는 어셈블리 변경 내용이 자동으로 감시됩니다. 다른 디렉터리의 어셈블리 변경 내용을 감시하려면 [host.json](functions-host-json.md)의 `watchDirectories` 목록에 해당 디렉터리를 추가합니다.

## <a name="using-nuget-packages"></a>NuGet 패키지 사용

C# 함수에서 NuGet 패키지를 사용하려면 *project.json* 파일을 함수 앱의 파일 시스템에 있는 함수의 폴더에 업로드합니다. 다음은 Microsoft.ProjectOxford.Face 버전 1.1.0에 참조를 추가하는 예제 *project.json* 파일입니다.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Azure Functions 1.x에서는 .NET Framework 4.6만 지원되므로 *project.json* 파일이 다음과 같이 `net46`을 지정하도록 합니다.

*project.json* 파일을 업로드하는 경우 런타임은 패키지를 가져오고 패키지 어셈블리에 참조를 자동으로 추가합니다. `#r "AssemblyName"` 지시문을 추가할 필요가 없습니다. NuGet 패키지에 정의된 형식을 사용하려면 필요한 `using` 문을 *run.csx* 파일에 추가하기만 하면 됩니다. 

함수 런타임에서 NuGet 복원은 `project.json` 및 `project.lock.json`를 비교하여 작동합니다. 파일의 날짜와 타임스탬프가 일치하지 **않으면** NuGet에서는 복원을 실행하고 업데이트된 패키지를 다운로드합니다. 그러나 파일의 날짜 및 타임스탬프가 **일치하는** 경우 NuGet은 복원을 수행하지 않습니다. 따라서 `project.lock.json`을 배포하면 NuGet이 패키지 복원을 건너뛰므로 배포해서는 안 됩니다. 잠금 파일의 배포를 방지하려면 `project.lock.json`을 `.gitignore` 파일에 추가합니다.

사용자 지정 NuGet 피드를 사용하려면 함수 앱 루트의 *Nuget.Config* 파일에서 피드를 지정합니다. 자세한 내용은 참조 [NuGet 동작 구성](/nuget/consume-packages/configuring-nuget-behavior)을 참조하세요.

### <a name="using-a-projectjson-file"></a>project.json 파일 사용

1. Azure Portal에서 함수를 엽니다. 로그 탭에 패키지 설치 출력이 표시됩니다.
2. project.json 파일을 업로드하려면 Azure Functions 개발자 참조 토픽의 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)에 설명되어 있는 방법 중 하나를 사용합니다.
3. *project.json* 파일을 업로드하면 함수의 스트리밍 로그에 다음 예제와 같은 출력이 표시됩니다.

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>환경 변수

환경 변수 또는 앱 설정 값을 가져오려면 다음 코드 예제와 같이 `System.Environment.GetEnvironmentVariable`을 사용합니다.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>런타임에 바인딩

C# 및 기타 .NET 언어에서는 *function.json*의 [*declarative*](https://en.wikipedia.org/wiki/Declarative_programming) 바인딩과 달리 [명령적](https://en.wikipedia.org/wiki/Imperative_programming) 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 바인딩 매개 변수를 디자인 타임이 아닌 런타임에 계산해야 할 경우 유용합니다. 이 패턴을 사용하면 함수 코드에서 지원되는 입력 및 출력 바인딩을 즉시 바인딩할 수 있습니다.

다음과 같이 명령적 바인딩을 정의합니다.

- 원하는 명령적 바인딩에 대한 *function.json*에 항목을 포함하지 **마세요**.
- 입력 매개 변수 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 또는 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)에 전달합니다.
- 다음 C# 패턴을 사용하여 데이터 바인딩을 수행합니다.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`는 바인딩을 정의하는 .NET 특성이며, `T`는 해당 바인딩 형식에서 지원되는 입력 또는 출력 형식입니다. `T`는 `out` 매개 변수 형식(예: `out JObject`)일 수 없습니다. 예를 들어, Mobile Apps 테이블 출력 바인딩은 [6가지 출력 형식](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)을 지원하지만 `T`에는 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)만 사용할 수 있습니다.

### <a name="single-attribute-example"></a>단일 특성 예제

다음 예제 코드에서는 런타임에서 정의된 Blob경로를 사용하는 [Storage Blob 출력 바인딩](functions-bindings-storage-blob.md#output)을 만든 다음, Blob에 문자열을 씁니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)는 [Storage Blob](functions-bindings-storage-blob.md) 입력 또는 출력 바인딩을 정의하며, [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)는 지원되는 출력 바인딩 형식입니다.

### <a name="multiple-attribute-example"></a>다중 특성 예제

앞의 예제에서는 함수 앱의 주 Storage 계정 연결 문자열(`AzureWebJobsStorage`)에 대한 앱 설정을 가져옵니다. [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)를 추가하고 `BindAsync<T>()`에 특성 배열을 전달하여 저장소 계정에 사용할 사용자 지정 앱 설정을 지정할 수 있습니다. `IBinder`가 아닌 `Binder` 매개 변수를 사용합니다.  예: 

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

다음 표에는 각 바인딩 유형의 .NET 특성과 해당 특성이 정의된 패키지가 나열되어 있습니다.

> [!div class="mx-codeBreakAll"]
| 바인딩 | 특성 | 추가 참조 |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Storage 큐 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage 테이블 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [트리거 및 바인딩에 대해 자세히 알아보기](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례에 대해 자세히 알아보기](functions-best-practices.md)
