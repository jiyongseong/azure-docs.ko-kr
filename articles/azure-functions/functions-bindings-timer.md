---
title: Azure Functions의 타이머 트리거
description: Azure Functions에서 타이머 트리거를 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: a8844ea44bf604944c5980b0d41ab5d01a30b876
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions의 타이머 트리거 

이 문서에서는 Azure Functions에서 타이머 트리거를 사용하는 방법을 설명합니다. 타이머 트리거를 사용하면 일정에 따라 함수를 실행할 수 있습니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>패키지

타이머 트리거는 [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지에 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="example"></a>예

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# 예제

다음 예제에서는 5분마다 실행되는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# 스크립트 예제

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# 예제

다음 예제에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [F# 스크립트 함수](functions-reference-fsharp.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# 스크립트 코드는 다음과 같습니다.

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript 예제

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 스크립트 코드는 다음과 같습니다.

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 CRON 식 또는 `TimeSpan`을 사용합니다. App Service 계획에서 함수 앱을 실행 중인 경우에만 `TimeSpan`을 사용할 수 있습니다. 다음 예제는 CRON 식을 보여줍니다.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `TimerTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | "timerTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 타이머 개체를 나타내는 변수의 이름입니다. | 
|**schedule**|**ScheduleExpression**|[CRON 식](#cron-expressions) 또는 [TimeSpan](#timespan) 값입니다. App Service 계획에서 함수 앱을 실행 중인 경우에만 `TimeSpan`을 사용할 수 있습니다. "%ScheduleAppSetting%" 예제와 같이 앱 설정에서 일정 식을 배치하고 이 속성을 **%** 기호에서 래핑된 앱 설정 이름으로 설정할 수 있습니다. |
|**runOnStartup**|**RunOnStartup**|`true`인 경우 함수는 런타임이 시작될 때 호출됩니다. 예를 들어 비활성으로 인해 유휴 상태로 전환된 후에 함수 앱이 작동될 때 런타임이 시작됩니다. 함수 변경으로 인해 함수 앱을 다시 시작할 때 및 함수 앱이 확장할 때입니다. 따라서 **runOnStartup**은 예측할 수 없는 경우에 코드를 실행하므로 `true`로 설정되는 경우가 거의 없습니다.|
|**useMonitor**|**UseMonitor**|`true` 또는 `false`로 설정하여 일정을 모니터링해야 하는지를 나타냅니다. 일정 모니터링은 일정 발생을 유지하여 함수 앱 인스턴스가 다시 시작하는 경우에도 일정을 올바르게 유지하도록 지원합니다. 명시적으로 설정하지 않는 경우 되풀이 간격이 1분을 넘는 큰 일정에서 기본값은 `true`입니다. 분당 한 번 넘게 트리거되는 일정에서 기본값은 `false`입니다.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용 현황

타이머 트리거 함수를 호출하면 [타이머 개체](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)가 함수에 전달됩니다. 다음 JSON은 타이머 개체의 예제 표현입니다. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

현재 함수 호출이 일정보다 늦은 경우 `IsPastDue` 속성은 `true`입니다. 예를 들어 함수 앱을 다시 시작하면 호출이 누락될 수 있습니다.

## <a name="cron-expressions"></a>CRON 식 

Azure Functions 타이머 트리거의 CRON 식에는 다음과 같은 6개의 필드가 포함됩니다. 

`{second} {minute} {hour} {day} {month} {day-of-week}`

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|type  |예  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>"0 5 * * * *"</nobr>|hh:05:00에서 hh는 매시간임(시간당 한 번)|
|모든 값(`*`)|<nobr>"0 * 5 * * *"</nobr>|5:mm:00에서 mm은 해당 시간의 매분임(하루 60번)|
|범위(`-` 연산자)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05,hh:mm:06 및 hh:mm:07에서 hh:mm은 매시간의 매분임(분당 3번)|  
|값 집합(`,` 연산자)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05,hh:mm:08 및 hh:mm:10에서 hh:mm은 매시간의 매분임(분당 3번)|
|간격 값(`/` 연산자)|<nobr>"0 */5 * * * *"</nobr>|hh:05:00, hh:10:00, hh:15:00부터 hh:55:00까지에서 hh는 매시간임(시간당 12번)|

### <a name="cron-examples"></a>CRON 예제

Azure Functions에서 타이머 트리거를 사용할 수 있는 CRON 식의 몇 가지 예는 다음과 같습니다.

|예|트리거될 때  |
|---------|---------|
|"0 */5 * * * *"|5분마다 한 번|
|"0 0 * * * *"|1시간이 시작할 때마다 한 번|
|"0 0 */2 * * *"|2시간마다 한 번|
|"0 0 9-17 * * *"|오전 9시에서 오후 5시까지 1시간마다 한 번|
|"0 30 9 * * *"|매일 오전 9시 30분|
|"0 30 9 * * 1-5"|평일 오전 9:30|

>[!NOTE]   
>온라인으로 CRON 식 예제를 찾을 수 있지만 그 중 대부분은 `{second}` 필드를 생략합니다. 이들 중 하나를 복사하는 경우 누락된 `{second}` 필드를 추가합니다. 일반적으로 해당 필드에 별표가 아닌 0을 사용합니다.

### <a name="cron-time-zones"></a>CRON 표준 시간대

CRON 식에 있는 숫자는 시간 범위가 아닌 시간 및 날짜를 가리킵니다. 예를 들어 `hour` 필드에 있는 5는 5시간마다 한 번이 아닌 오전 5시를 가리킵니다.

CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 앱 설정을 만듭니다. [Microsoft 표준 시간대 색인](https://technet.microsoft.com/library/cc749073)에 나온 대로 값을 원하는 표준 시간대의 이름으로 설정합니다. 

예를 들어 *동부 표준시*는 UTC-05:00입니다. 타이머 트리거를 매일 오전 10시 EST에 발생하도록 하려면 UTC 표준 시간대를 반영하는 다음과 같은 CRON 식을 사용합니다.

```json
"schedule": "0 0 15 * * *",
``` 

또는 `WEBSITE_TIME_ZONE`이라는 함수 앱에 앱 설정을 만들고, 값을 **동부 표준시**로 설정합니다.  그런 다음, 다음 CRON 식을 사용합니다. 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>timespan

 App Service 계획에서 함수 앱을 실행 중인 경우에만 `TimeSpan`을 사용할 수 있습니다.

CRON 식과 다르게 `TimeSpan` 값은 각 함수 호출 간의 시간 간격을 지정합니다. 함수가 지정된 간격보다 오랫동안 실행한 후에 완료되면 타이머는 즉시 함수를 다시 호출합니다.

`hh`이 24 미만인 경우 문자열로 표현되는 `TimeSpan` 형식은 `hh:mm:ss`입니다. 처음 두 자리가 24 이상인 경우 형식은 `dd:hh:mm`입니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

|예 |트리거될 때  |
|---------|---------|
|"01:00:00" | 매시간        |
|"00:01:00"|매분         |
|"24:00:00" | 매일        |

## <a name="scale-out"></a>스케일 아웃

함수 앱이 여러 인스턴스로 확장하는 경우 모든 인스턴스에서 타이머 트리거 함수의 단일 인스턴스만을 실행합니다.

## <a name="function-apps-sharing-storage"></a>저장소를 공유하는 함수 앱

여러 함수 앱에서 Storage 계정을 공유하는 경우 *host.json*에서 각 함수 앱의 `id`가 다른지 확인합니다. `id` 속성을 생략하거나 수동으로 각 함수 앱의 `id`를 다른 값으로 설정할 수 있습니다. 타이머 트리거는 함수 앱이 여러 인스턴스로 확장되는 경우 저장소 잠금을 사용하여 하나의 타이머 인스턴스만이 존재하도록 합니다. 두 개의 함수 앱이 동일한 `id`를 공유하고 각각 타이머 트리거를 사용하는 경우 하나의 타이머만이 실행됩니다.

## <a name="retry-behavior"></a>다시 시도 동작

큐 트리거와 다르게 타이머 트리거는 함수가 실패한 후에 다시 시도하지 않습니다. 함수가 실패한 경우 일정에 따라 다음 시도까지 다시 호출되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [타이머 트리거를 사용하는 빠른 시작으로 이동](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
