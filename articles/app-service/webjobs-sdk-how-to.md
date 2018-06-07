---
title: 이벤트 중심 백그라운드 처리를 위한 WebJobs SDK 사용 방법 - Azure
description: WebJobs SDK에 대한 코드 작성 방법을 알아봅니다. Azure 서비스 및 타사 서비스의 데이터에 액세스하는 이벤트 중심 백그라운드 처리 작업을 만듭니다.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 3adf725f76f744fd1d321668fe892b9703de25de
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-use-the-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 WebJobs SDK 사용 방법

이 문서에서는 [WebJobs SDK](webjobs-sdk-get-started.md)에 대한 코드 작성 방법을 안내합니다. 별도로 명시되지 않는 한 이 설명서는 2.x 및 3.x 버전에 적용됩니다. 3.x 버전에 도입된 주요 변경 내용은 .NET Framework 대신 .NET Core를 사용하는 것입니다.

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md)는 WebJobs SDK를 기반으로 하며, 이 문서는 일부 항목에 대한 Azure Functions 설명서로 연결됩니다. 다음은 Functions와 WebJobs SDK의 차이점입니다.
> * Azure Functions 1.x 버전은 WebJobs SDK 2.x 버전에 해당하고, Azure Functions 2.x 버전은 WebJobs SDK 3.x 버전에 해당합니다. 소스 코드 리포지토리는 WebJobs SDK 번호 매기기를 따르고, 많은 리포지토리에 v2.x 분기가 있으며, 마스터 분기에는 현재 3.x 코드가 있습니다.
> * Azure Functions C# 클래스 라이브러리에 대한 샘플 코드는 WebJobs SDK 프로젝트에 `FunctionName` 특성이 필요 없다는 점을 제외하고 WebJobs SDK 코드와 비슷합니다.
> * HTTP, 웹후크, Event Grid(HTTP 기반의) 등의 일부 바인딩 형식은 Functions에서만 지원됩니다. 
> 
> 자세한 내용은 [WebJobs SDK과 Azure Functions 비교](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 여러분이 [WebJobs SDK 시작](webjobs-sdk-get-started.md)을 읽어 보셨다고 가정합니다.

## <a name="jobhost"></a>JobHost

`JobHost` 개체는 함수에 대한 런타임 컨테이너이며, 트리거를 수신 대기하고 함수를 호출합니다. 사용자는 코드에서 `JobHost`를 만들고 동작을 사용자 지정하는 코드를 작성하게 됩니다.

이것이 WebJobs SDK를 직접 사용할 때와 Azure Functions를 통해 간접적으로 사용할 때의 주요 차이점입니다. Azure Functions에서는 서비스가 `JobHost`를 제어하며, 사용자가 코드를 작성하여 서비스를 사용자 지정할 수 없습니다. Azure Functions를 사용하면 *host.json* 파일의 설정을 통해 호스트 동작을 사용자 지정할 수 있습니다. 이러한 설정은 코드가 아닌 문자열이며, 수행 가능한 사용자 지정의 종류를 제한합니다.

### <a name="jobhost-connection-strings"></a>JobHost 연결 문자열

WebJobs SDK는 로컬에서 실행할 경우 *local.settings.json* 파일에서 Service Bus 연결 문자열을 찾고, Azure에서 실행할 경우 WebJob 환경에서 찾습니다. 이러한 연결 문자열에 사용자 고유의 이름을 사용하거나 다른 위치에 저장하려면 다음과 같이 코드에서 설정하면 됩니다.

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>JobHost 개발 설정

`JobHostConfiguration` 클래스에는 로컬 개발의 효율성을 높이기 위해 호출할 수 있는 `UseDevelopmentSettings` 메서드가 있습니다. 다음은 이 메서드가 변경하는 설정 중 일부입니다.

| 자산 | 개발 설정 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | 로그 출력을 최대화하는 `TraceLevel.Verbose`. |
| `Queues.MaxPollingInterval`  | 값이 작으면 큐 메서드가 즉시 트리거됩니다.  |
| `Singleton.ListenerLockPeriod` | 신속한 반복 개발에 도움이 되는 시간은 15초입니다. |

다음 예제에서는 개발 설정을 사용하는 방법을 보여줍니다. 로컬로 실행할 때 `config.IsDevelopment`에서 `true`를 반환하게 하려면 `Development` 값을 사용하여 `AzureWebJobsEnv`라는 로컬 환경 변수를 지정합니다.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager 설정

.NET Framework에는 호스트에 대한 동시 연결 수를 제어하는 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit)라고 하는 API가 포함되어 있습니다. WebJobs 호스트를 시작하기 전에 이 값을 기본값인 2보다 높게 설정하는 것이 좋습니다.

사용자가 `ServicePointManager`를 통해 `HttpClient` 흐름을 사용하여 함수에서 만드는 모든 나가는 HTTP 요청. `DefaultConnectionLimit`에 도달하면 `ServicePointManager`는 요청 큐잉을 시작한 후 요청을 보냅니다. `DefaultConnectionLimit`가 2로 설정되었고 코드에서 HTTP 요청 1,000개를 만든다고 가정해 봅시다. 처음에는 실제로 OS까지 전달되는 요청이 2개밖에 없습니다. 나머지 998개는 공간이 생길 때까지 큐에서 대기합니다. 즉, `HttpClient`는 요청을 만들었지만 OS가 요청을 대상 서버로 보낸 적이 없다고 *생각*하고 시간이 초과될 수 있습니다. 이와 같은 이유로 로컬 `HttpClient`가 요청을 완료하는 데 10초가 걸리지만 서비스가 200ms 후에 모든 요청을 반환하는 이상한 동작이 관찰될 수 있습니다. 

ASP.NET 응용 프로그램의 기본값은 `Int32.MaxValue`이고, Basic 이상 App Service 계획에서 실행되는 WebJobs에서 잘 작동할 것입니다. WebJobs는 일반적으로 Always On 설정이 필요한데, 이 설정은 Basic 이상 App Service 계획에서만 지원됩니다. 

WebJob이 무료 또는 공유 App Service 계획에서 실행되는 경우 응용 프로그램이 App Service 샌드박스의 제한을 받으며, 현재 [연결 제한은 300개](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)입니다. `ServicePointManager`의 바인딩되지 않은 제한 때문에 샌드박스 연결 임계값에 도달하여 사이트가 종료될 가능성이 있습니다. 이 경우 `DefaultConnectionLimit`를 50 또는 100처럼 약간 낮추면 이와 같은 상황을 방지하면서도 충분한 처리량을 계속 제공할 수 있습니다.

이 설정은 HTTP 요청을 만들기 전에 구성해야 합니다. 이러한 이유로 WebJobs 호스트는 설정을 자동으로 조정하려고 시도하면 안 됩니다. 호스트가 시작되기 전에 발생하는 HTTP 요청이 있을 수 있으며 이로 인해 예기치 않은 동작이 발생할 수 있습니다. 가장 좋은 방법은 다음 예제와 같이 `JobHost`를 초기화하기 전에 `Main` 메서드에서 즉시 값을 설정하는 것입니다.

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>트리거

함수는 공용 메서드여야 하고 트리거 특성 또는 [NoAutomaticTrigger](#manual-trigger) 특성이 하나만 있어야 합니다.

### <a name="automatic-trigger"></a>자동 트리거

자동 트리거는 이벤트에 대한 응답으로 함수를 호출합니다. 예제는 [시작 문서](webjobs-sdk-get-started.md)의 큐 트리거를 참조하세요.

### <a name="manual-trigger"></a>수동 트리거

함수를 수동으로 트리거하려면 다음 예제와 같이 `NoAutomaticTrigger` 특성을 사용합니다.

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>입력 및 출력 바인딩

입력 바인딩은 Azure 또는 타사 서비스의 데이터를 코드에 사용할 수 있는 선언적 방법을 제공합니다. 출력 바인딩은 데이터를 업데이트하는 방법을 제공합니다. [시작 문서](webjobs-sdk-get-started.md)는 각각의 예제를 보여줍니다.

메서드 반환 값에 특성을 적용하여 출력 바인딩에 대한 메서드 반환 값을 사용할 수 있습니다. Azure Functions [트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) 문서의 예제를 참조하세요.

## <a name="binding-types"></a>바인딩 형식

다음 트리거 및 바인딩 형식은 `Microsoft.Azure.WebJobs` 패키지에 포함되어 있습니다.

* Blob 저장소
* 큐 저장소
* 테이블 저장소

다른 트리거 및 바인딩 형식을 사용하려면 사용하려는 트리거 및 바인딩 형식이 포함된 NuGet 패키지를 설치하고 `JobHostConfiguration` 개체에서 `Use<binding>` 메서드를 호출합니다. 예를 들어 타이머 트리거를 사용하려는 경우 다음 예제처럼 `Microsoft.Azure.WebJobs.Extensions`를 설치하고 `Main` 메서드에서 `UseTimers`를 호출합니다.

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

특정 바인딩 형식에 대해 설치할 패키지는 Azure Functions에 대한 [참조 문서](#binding-reference-information)에서 해당 바인딩 형식의 **패키지** 섹션에서 찾을 수 있습니다. Azure Functions에서 지원되지 않는 파일 트리거 및 바인딩은 예외입니다(로컬 파일 시스템의 경우). 파일 바인딩을 사용하려면 `Microsoft.Azure.WebJobs.Extensions`를 설치하고 `UseFiles`를 호출합니다.

### <a name="usecore"></a>UseCore

앞에서 언급한 `Microsoft.Azure.WebJobs.Extensions` 패키지는 `UseCore` 메서드를 호출하여 등록할 수 있는 특별한 바인딩 형식을 제공합니다. 이 바인딩을 사용하면 함수 서명에서 [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) 매개 변수를 정의할 수 있습니다. 컨텍스트 개체는 지정된 함수 호출에 의해 생성된 모든 로그의 상관 관계를 지정하는 데 사용할 수 있는 호출 ID 액세스를 제공합니다. 예를 들면 다음과 같습니다.

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

## <a name="binding-configuration"></a>바인딩 구성

`JobHost`에 전달하는 구성 개체의 속성을 설정하여 동작을 구성할 수 있는 여러 트리거 및 바인딩 형식이 있습니다.

### <a name="queue-trigger-configuration"></a>큐 트리거 구성

Storage 큐 트리거에 대해 구성할 수 있는 설정은 Azure Functions [host.json 참조](../azure-functions/functions-host-json.md#queues)에 설명되어 있습니다. WebJobs SDK 프로젝트에서 설정하는 방법은 다음 예제에 설명되어 있습니다.

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>다른 바인딩의 구성

일부 트리거 및 바인딩 형식은 자체적인 사용자 지정 구성 형식을 정의합니다. 예를 들어 파일 트리거는 모니터링할 루트 경로를 지정할 수 있습니다.

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>바인딩 식

특성 생성자 매개 변수에서는 다양한 소스의 값을 확인하는 식을 사용할 수 있습니다. 예를 들어 다음 코드에서 `BlobTrigger` 특성의 경로는 `filename`이라는 식을 만듭니다. 출력 바인딩에 사용될 경우 `filename`은 트리거 BLOB의 이름으로 확인됩니다.
 
```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

바인딩 식에 대한 자세한 내용은 Azure Functions 설명서의 [바인딩 식 및 패턴](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)을 참조하세요.

### <a name="custom-binding-expressions"></a>사용자 지정 바인딩 식

경우에 따라 큐 이름, Blob 이름 또는 컨테이너, 테이블 이름을 하드 코드하지 않고 코드에서 지정할 수 있습니다. 예를 들어 구성 파일 또는 환경 변수에서 `QueueTrigger` 특성에 대한 큐 이름을 지정할 수 있습니다.

이렇게 하려면 `NameResolver` 개체를 `JobHostConfiguration` 개체에 전달하면 됩니다. 트리거 또는 바인딩 특성 생성자 매개 변수에 자리 표시자를 포함하면 `NameResolver` 코드가 해당 자리 표시자 위치에 사용할 실제 값을 제공합니다. 자리 표시자는 다음 예제와 같이 백분율 기호(%)로 묶어서 식별합니다.
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

이 코드를 사용하면 테스트 환경에 logqueuetest라는 큐를 사용하고 프로덕션 환경에 logqueueprod라는 큐를 사용할 수 있습니다. 하드 코드된 큐 이름 대신 `appSettings` 컬렉션의 항목 이름을 지정합니다. 

사용자 지정 이름을 제공하지 않을 때 적용되는 기본 NameResolver가 있습니다. 기본값은 앱 설정이나 환경 변수에서 가져옵니다.

`NameResolver` 클래스가 다음 예제와 같이 `appSettings`에서 큐 이름을 가져옵니다.

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

다음 예제와 같이 `NameResolver` 클래스를 `JobHost` 개체에 전달합니다.

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions가 예제와 같이 앱 설정에서 값을 가져오도록 `INameResolver`를 구현합니다. 사용자는 WebJobs SDK를 직접 사용하는 경우 원하는 소스에서 자리 표시자 대체 값을 가져오는 사용자 지정 구현을 작성할 수 있습니다. 

## <a name="binding-at-runtime"></a>런타임에 바인딩

`Queue`, `Blob` 또는 `Table` 같은 바인딩 특성을 사용하려면 함수에서 일부 작업을 수행해야 하는 경우 `IBinder` 인터페이스를 사용하면 됩니다.

다음 예제에서는 입력 큐 메시지를 사용하여 동일한 내용의 새 메시지를 출력 큐에 만듭니다. 출력 큐 이름은 함수 본문에서 코드로 설정됩니다.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

자세한 내용은 Azure Functions 설명서의 [런타임에 바인딩](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)을 참조하세요.

## <a name="binding-reference-information"></a>바인딩 참조 정보

각 바인딩 형식에 대한 참조 정보는 Azure Functions 설명서에 제공됩니다. Storage 큐를 예제로 사용하여 각 바인딩 참조 문서에서 다음 정보를 확인할 수 있습니다.

* [패키지](../azure-functions/functions-bindings-storage-queue.md#packages) - WebJobs SDK 프로젝트에 바인딩 지원을 포함하기 위해 설치해야 하는 패키지.
* [예제](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - C# 클래스 라이브러리 예제는 WebJobs SDK에 적용됩니다. `FunctionName` 특성을 생략하세요.
* [특성](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) - 바인딩 형식에 사용할 특성.
* [구성](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) - 특성 속성 및 생성자 매개 변수에 대한 설명.
* [사용량](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - 바인딩할 수 있는 형식 및 바인딩의 작동 원리에 대한 정보. 예: 폴링 알고리즘, 포이즌 큐 처리.
  
바인딩 참조 문서 목록은 Azure Functions에 대한 [트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md#supported-bindings) 문서의 **지원되는 바인딩**을 참조하세요. 이 목록의 HTTP, 웹후크 및 Event Grid 바인딩은 WebJobs SDK가 아닌 Azure Functions에서만 지원됩니다.

## <a name="disable-attribute"></a>Disable 특성 

[Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 특성을 사용하여 함수를 트리거할 수 있는지 여부를 제어할 수 있습니다. 

다음 예제에서 앱 설정 `Disable_TestJob`의 값이 "1" 또는 "True"이면(대/소문자 구분 안 함) 함수가 실행되지 않습니다. 이 경우 런타임에서 *Function 'Functions.TestJob' is disabled* 로그 메시지를 작성합니다.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure Portal에서 앱 설정 값을 변경하면 WebJob이 다시 시작되고, 설정을 새로 선택해야 합니다.

매개 변수, 메서드 또는 클래스 수준에서 특성을 선언할 수 있습니다. 설정 이름에 바인딩 식을 포함할 수도 있습니다.

## <a name="timeout-attribute"></a>Timeout 특성

[Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 특성은 지정된 시간 내에 함수가 완료되지 않으면 함수를 취소합니다. 다음 예제의 함수는 시간 제한이 없으면 하루 동안 실행됩니다. 시간 제한이 있으면 함수가 15초 후에 취소됩니다.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

클래스 또는 메서드 수준에서 Timeout 특성을 적용할 수 있으며, `JobHostConfiguration.FunctionTimeout`을 사용하여 전역 시간 제한을 지정할 수 있습니다. 클래스 또는 메서드 수준 시간 제한은 전역 시간 제한을 재정의합니다.

## <a name="singleton-attribute"></a>Singleton 특성

호스트 웹앱 인스턴스가 여러 개 있어도 함수의 인스턴스 중 하나만 실행하려면 [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 특성을 사용합니다. 이 특성은 이를 위해 [분산 잠금](#viewing-lease-blobs)을 구현합니다.

다음 예제에서는 `ProcessImage` 함수의 단일 인스턴스만 지정된 시간에 실행됩니다.

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

일부 트리거는 동시성 관리를 기본적으로 지원합니다.

* **QueueTrigger** - `JobHostConfiguration.Queues.BatchSize`를 1로 설정합니다.
* **ServiceBusTrigger** - `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls`를 1로 설정합니다.
* **FileTrigger** - `FileProcessor.MaxDegreeOfParallelism`을 1로 설정합니다.

이러한 설정을 사용하여 함수가 단일 인스턴스에서 싱글톤으로 실행되도록 할 수 있습니다. 웹앱이 여러 인스턴스로 규모 확장될 때 함수의 단일 인스턴스만 실행되게 하려면 함수에서 수신기 수준 싱글톤 잠금을 적용합니다(`[Singleton(Mode = SingletonMode.Listener)]`). 수신기 잠금은 JobHost 시작 시 획득합니다. 확장된 인스턴스 3개가 동시에 시작되면 인스턴스 중 하나만 잠금을 획득하고 하나의 수신기만 시작됩니다.

### <a name="singletonscopehost"></a>SingletonScope.Host

잠금의 기본 범위는 `SingletonScope.Function`입니다. 즉, 잠금 범위(BLOB 임대 경로)가 정규화된 함수 이름에 연결됩니다. 모든 함수에서 잠그려면 `SingletonScope.Host`를 지정하고, 동시에 실행하지 않으려는 모든 함수에서 동일한 범위 ID 이름을 사용하세요. 다음 예제에서는 `AddItem` 또는 `RemoveItem`의 인스턴스가 한 번에 하나씩 실행됩니다.

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>임대 BLOB 보기

WebJobs SDK는 분산 잠금을 구현하기 위해 백그라운드에서 [Azure blob 임대](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)를 사용합니다. 싱글톤에서 사용되는 임대 BLOB은 "locks" 경로 아래의 `AzureWebJobsStorage` 저장소 계정에 있는 `azure-webjobs-host` 컨테이너에서 찾을 수 있습니다. 예를 들어 앞에서 살펴본 첫 번째 `ProcessImage` 예제의 임대 BLOB 경로가 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`일 수 있습니다. 모든 경로는 JobHost ID(이 예에서는 061851c758f04938a4426aa9ab3869c0)를 포함합니다.

## <a name="async-functions"></a>비동기 함수

비동기 함수를 코딩하는 방법은 [비동기 함수](../azure-functions/functions-dotnet-class-library.md#async)에 대한 Azure Functions 설명서를 참조하세요.

## <a name="cancellation-tokens"></a>취소 토큰

취소 토큰을 처리하는 방법은 [취소 토큰 및 정상 종료](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)에 대한 Azure Functions 설명서를 참조하세요.

## <a name="multiple-instances"></a>여러 인스턴스

웹앱이 여러 인스턴스에서 실행되는 경우 연속적인 WebJob이 각 인스턴스에서 실행되고, 트리거를 기다렸다가 함수를 호출합니다. 더 많은 인스턴스로 확장하여 더 많은 부하를 처리할 수 있도록, 작업을 효율적으로 여러 인스턴스에서 공동으로 공유하도록 설계된 다양한 트리거 바인딩이 있습니다.

큐 및 BLOB 트리거는 함수가 큐 메시지 또는 BLOB을 여러 번 처리하는 것을 방지합니다. 함수는 idempotent일 필요가 없습니다.

타이머 트리거는 자동으로 타이머의 한 인스턴스만 실행되도록 보장하므로 예약된 특정 시간에 오직 하나의 함수 인스턴스만 실행됩니다.

호스트 웹앱의 인스턴스가 여러 개 있는 경우에도 함수 인스턴스 중 하나만 실행되게 하려면 [Singleton](#singleton) 특성을 사용하면 됩니다.
    
## <a name="filters"></a>필터 

함수 필터(미리 보기)는 사용자 고유의 논리로 WebJobs 실행 파이프라인을 사용자 지정하는 방법을 제공합니다. 필터는 [ASP.NET Core 필터](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)와 비슷합니다. 함수 또는 클래스에 적용되는 선언적 특성으로 구현할 수 있습니다. 자세한 내용은 [함수 필터](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

ASP.NET용으로 개발된 로깅 프레임워크를 권장하며, 사용 방법은 [시작](webjobs-sdk-get-started.md) 문서에 설명되어 있습니다. 

### <a name="log-filtering"></a>로그 필터링

`ILogger` 인스턴스로 만든 모든 로그에는 `Category` 및 `Level`이 연결됩니다. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel)은 열거형이며, 정수 코드는 상대적 중요도를 나타냅니다.

|LogLevel    |코드|
|------------|---|
|추적       | 0 |
|디버그       | 1 |
|정보 | 2 |
|Warning     | 3 |
|오류       | 4 |
|중요    | 5 |
|없음        | 6 |

각 범주를 특정 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel)로 독립적으로 필터링할 수 있습니다. 예를 들어 BLOB 트리거 처리에 대한 모든 로그를 보고 싶지만 그 외에는 `Error` 이상만 보고 싶은 경우가 있습니다.

간편하게 필터링 규칙을 지정할 수 있도록 WebJobs SDK는 Application Insights 및 콘솔을 포함하여 여러 기존 로깅 공급자에게 전달할 수 있는 `LogCategoryFilter`를 제공합니다.

`LogCategoryFilter`의 `Default` 속성은 초기 값이 `Information`입니다. 다시 말해서 수준이 `Information`, `Warning`, `Error` 또는 `Critical`인 메시지는 로깅되지만 수준이 `Debug` 또는 `Trace`인 메시지는 필터링됩니다.

`CategoryLevels` 속성을 사용하면 특정 범주의 로그 수준을 지정하여 로깅 출력을 조정할 수 있습니다. `CategoryLevels` 디렉터리 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

다음 예제는 기본적으로 `Warning` 수준에서 모든 로그를 필터링하는 필터를 작성합니다. `Function` 또는 `Host.Results` 범주는 `Error` 수준에서 필터링됩니다. `LogCategoryFilter`는 현재 범주를 모든 등록된 `CategoryLevels`와 비교하여 가장 긴 일치 항목을 선택합니다. 즉, `Host.Triggers`에 대해 등록된 `Debug` 수준은 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`과 일치합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights에 대한 사용자 지정 원격 분석​

내부적으로 WebJobs SDK에 대한 Application Insights 공급자가 만든 `TelemetryClient`는 [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)을 사용합니다. Application Insights 엔드포인트를 사용할 수 없거나 들어오는 요청을 제한하는 경우 이 채널은 [웹앱의 파일 시스템에 요청을 저장해 두었다가 나중에 다시 전송](http://apmtips.com/blog/2015/09/03/more-telemetry-channels)합니다.

`TelemetryClient`는 `ITelemetryClientFactory`를 구현하는 클래스에서 생성합니다. 기본적으로는 [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)입니다.

Application Insights 파이프라인의 특정 부분을 수정하려는 경우 사용자 고유의 `ITelemetryClientFactory`를 제공하면 호스트가 해당 클래스를 사용하여 `TelemetryClient`를 생성합니다. 예를 들어 다음 코드는 `ServerTelemetryChannel` 속성을 수정하도록 `DefaultTelemetryClientFactory`를 재정의합니다.

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

SamplingPercentageEstimatorSettings 개체는 [적응 샘플링](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server)을 구성합니다. 즉, 특정 대량 볼륨 시나리오에서는 App Insights가 서버에 선별된 원격 분석 데이터 하위 집합을 보냅니다.

원격 분석 팩터리를 만들었으면 Application Insights 로깅 공급자에게 전달합니다.

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 다음 단계

이 가이드에서는 WebJobs SDK 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여주는 코드 조각을 제공했습니다. 전체 샘플은 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples)를 참조하세요.