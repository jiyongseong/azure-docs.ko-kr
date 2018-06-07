---
title: Azure Application Insights의 사용자 지정 메트릭 및 진단을 사용한 라이브 메트릭 스트림 | Microsoft Docs
description: 사용자 지정 메트릭으로 웹앱을 실시간으로 모니터링하고 오류, 추적 및 이벤트의 라이브 피드를 통해 문제를 진단할 수 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin; Soubhagya.Dash
ms.openlocfilehash: 3b17344af099ea8b5d2554d5f6045a10641ff861
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193430"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>라이브 메트릭 스트림: 1초 대기 시간으로 모니터링 및 진단 

[Application Insights](app-insights-overview.md)의 라이브 메트릭 스트림을 사용하여 프로덕션 웹 응용 프로그램에서 실시간 심장 박동을 검사합니다. 메트릭과 성능 카운터를 선택 및 필터링하여 서비스에 지장 없이 실시간으로 확인합니다. 실패한 요청 및 예외 샘플에서 스택 추적을 검사합니다. [프로파일러](app-insights-profiler.md), [스냅숏 디버거](app-insights-snapshot-debugger.md) 및 [성능 테스트](app-insights-monitor-web-app-availability.md#performance-tests)와 함께 라이브 메트릭 스트림은 라이브 웹 사이트에 강력하고 비침해적인 진단 도구를 제공합니다.

라이브 메트릭 스트림을 사용하여 다음을 수행할 수 있습니다.

* 릴리스되는 동안 성능 및 실패 수를 확인하여 수정된 부분의 유효성을 검사합니다.
* 테스트 로드의 영향을 확인하고 문제를 실시간으로 진단합니다. 
* 확인하려는 메트릭을 선택 및 필터링하여 특정 테스트 세션에 중점을 두거나 알려진 문제를 필터링합니다.
* 예외 추적이 발생하면 가져옵니다.
* 필터를 사용하여 가장 관련성이 높은 KPI를 찾아 봅니다.
* 모든 Windows 성능 카운터를 실시간 모니터링합니다.
* 문제가 있는 서버를 쉽게 식별하고 해당 서버로의 모든 KPI/라이브 피드만 필터링합니다.

[![라이브 메트릭 스트림 비디오](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

라이브 메트릭 스트림은 현재 온-프레미스를 실행 중인 ASP.NET 앱 또는 클라우드에서 사용할 수 있습니다. 

## <a name="get-started"></a>시작하기

1. 아직 ASP.NET 웹앱 또는 [Windows 서버 앱](app-insights-windows-services.md)에 [Application Insights를 설치](app-insights-asp-net.md)하지 않은 경우 지금 설치합니다. 
2. Application Insights 패키지의 **최신 버전으로 업데이트**합니다. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 선택합니다. **업데이트** 탭을 열어 **시험판 포함**을 선택하고 모든 Microsoft.ApplicationInsights.* 패키지를 선택합니다.

    응용 프로그램을 다시 배포 합니다.

3. [Azure Portal](https://portal.azure.com)에서 앱에 대한 Application Insights 리소스를 연 다음 라이브 스트림을 엽니다.

4. 필터에 고객 이름과 같은 중요한 데이터를 사용할 경우 [컨트롤 채널을 보호](#secure-the-control-channel)합니다.


![개요 블레이드에서 라이브 스트림 클릭](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>데이터가 없나요? 서버 방화벽을 확인합니다.

[라이브 메트릭 스트림의 나가는 포트](app-insights-ip-addresses.md#outgoing-ports)가 서버의 방화벽에서 열려 있는지 확인합니다. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>라이브 메트릭 스트림과 메트릭 탐색기 및 분석과의 차이점

| |라이브 스트림 | 메트릭 탐색기 및 분석 |
|---|---|---|
|대기 시간|데이터가 1초 내에 표시됨|몇 분에 걸쳐 집계됨|
|보존 없음|데이터가 차트에 있는 동안 지속된 후 삭제됨|[데이터가 90일 동안 유지됨](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|주문형|라이브 메트릭을 여는 동안 데이터가 스트리밍됨|SDK가 설치되고 사용될 때마다 데이터가 전송됨|
|무료|라이브 스트림 데이터 무료|[가격 책정](app-insights-pricing.md)에 따라 다름
|샘플링|선택한 모든 메트릭 및 카운터가 전송되고 오류 및 스택 추적이 샘플링되며 TelemetryProcessors가 적용되지 않음|이벤트가 [샘플링](app-insights-api-filtering-sampling.md)될 수 있음|
|컨트롤 채널|필터 제어 신호가 SDK로 전송되며 [이 채널을 보호](#secure-channel)하는 것이 좋음|포털로의 단방향 통신임|


## <a name="select-and-filter-your-metrics"></a>메트릭 선택 및 필터링

(최신 SDK를 사용하여 클래식 ASP.NET 앱에서 사용 가능)

포털에서 Application Insights 원격 분석에 임의 필터를 적용하여 사용자 지정 KPI를 라이브로 모니터링할 수 있습니다. 차트 위로 마우스를 가져가면 표시되는 필터 컨트롤을 클릭합니다. 다음 차트는 URL 및 기간 특성에 필터를 적용하여 사용자 지정 요청 수 KPI를 그래프로 나타냅니다. 언제든지 지정한 조건과 일치하는 원격 분석의 라이브 피드를 표시하는 스트림 미리 보기 섹션을 사용하여 필터의 유효성을 검사합니다. 

![사용자 지정 요청 KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Count와 다른 값을 모니터링할 수 있습니다. 옵션은 Application Insights 원격 분석이 될 수 있는 스트림 유형에 따라 달라집니다. 요청, 종속성, 예외, 추적, 이벤트 또는 메트릭 등이 옵션으로 제공됩니다. 이것은 사용자 고유의 [사용자 지정 측정](app-insights-api-custom-events-metrics.md#properties)일 수 있습니다.

![값 옵션](./media/app-insights-live-stream/live-stream-valueoptions.png)

Application Insights 원격 분석 외에, 스트림 옵션 중에서 선택하고 성능 카운터의 이름을 제공하여 Windows 성능 카운터도 모니터링할 수도 있습니다.

라이브 메트릭은 각 서버에서 로컬로 집계된 후 모든 서버에서 집계됩니다. 해당 드롭다운 목록에서 다른 옵션을 선택하여 기본값을 변경할 수 있습니다.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>샘플 원격 분석: 사용자 지정 라이브 진단 이벤트
기본적으로 이벤트의 라이브 피드는 실패한 요청 및 종속성 호출, 예외, 이벤트 및 추적의 샘플을 보여 줍니다. 필터 아이콘을 클릭하면 특정 시점에 적용된 조건을 확인할 수 있습니다. 

![기본 라이브 피드](./media/app-insights-live-stream/live-stream-eventsdefault.png)

메트릭과 마찬가지로, Application Insights 원격 분석 유형 중 하나에 대해 임의 조건을 지정할 수 있습니다. 이 예제에서는 특정 요청 실패, 추적 및 이벤트를 선택합니다. 모든 예외 및 종속성 오류도 선택합니다.

![사용자 지정 라이브 피드](./media/app-insights-live-stream/live-stream-events.png)

참고: 현재 예외 메시지 기반 조건의 경우 가장 바깥쪽 예외 메시지를 사용하세요. 앞에 나온 예제에서 안쪽 예외 메시지("<--" 구분 기호 다음에 나옴) "클라이언트 연결이 끊어졌습니다."를 포함하는 심각하지 않은 예외를 필터링하기 위해서는 "요청 콘텐츠를 읽는 동안 오류 발생" 조건을 포함하지 않는 메시지를 사용합니다.

라이브 피드 항목을 클릭하여 세부 정보를 확인합니다. **일시 중지**를 클릭하거나, 아래로 스크롤하거나, 항목을 클릭하여 피드를 일시 중지할 수 있습니다. 맨 위로 다시 스크롤하거나 일시 중지된 상태에서 수집된 항목의 카운터를 클릭하면 라이브 피드가 계속됩니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>서버 인스턴스별 필터링

특정 서버 역할 인스턴스를 모니터링하려는 경우 서버별로 필터링할 수 있습니다.

![샘플링된 라이브 실패](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK 요구 사항
사용자 지정 라이브 메트릭 스트림은 버전 2.4.0-beta2 또는 최신 버전의 [웹용 Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)에서 사용할 수 있습니다. NuGet 패키지 관리자에서 "시험판 포함" 옵션을 선택해야 합니다.

## <a name="secure-the-control-channel"></a>컨트롤 채널 보호
지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 계측 키 외에도 비밀 API 키를 사용해서 채널 보안을 유지할 수 있습니다.
### <a name="create-an-api-key"></a>API 키 만들기

![API 키 만들기](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>구성에 API 키 추가

### <a name="classic-aspnet"></a>클래식 ASP.NET

applicationinsights.config 파일에서 QuickPulseTelemetryModule에 AuthenticationApiKey를 추가합니다.
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
또는 코드에서 QuickPulseTelemetryModule에 설정합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core(Application Insights ASP.NET Core SDK 2.3.0 베타 이상 필요)

다음과 같이 startup.cs 파일을 수정합니다.

먼저 추가

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

그런 다음, ConfigureServices 메서드 내에 다음을 추가합니다.

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule>( module => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```


그러나 연결된 모든 서버를 인식하고 신뢰하는 경우 인증된 채널을 사용하지 않고 사용자 지정 필터를 시도할 수 있습니다. 이 옵션은 6개월 동안 사용할 수 있습니다. 이 재정의는 새 세션마다 한 번씩 또는 새 서버가 온라인 상태가 될 때마다 필요합니다.

![라이브 메트릭 인증 옵션](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>필터 조건에 CustomerID와 같은 잠재적으로 중요한 정보를 입력하기 전에 인증된 채널을 설정하는 것이 좋습니다.
>

## <a name="generating-a-performance-test-load"></a>성능 테스트 로드 생성

로드 증가의 영향을 확인하려면 성능 테스트 블레이드를 사용합니다. 여러 동시 사용자의 요청을 시뮬레이트합니다. 단일 URL의 "수동 테스트"(ping 테스트)를 실행하거나, 가용성 테스트와 동일한 방식으로 업로드하는 [다단계 웹 성능 테스트](app-insights-monitor-web-app-availability.md#multi-step-web-tests)를 실행할 수 있습니다.

> [!TIP]
> 성능 테스트를 만든 후 테스트 및 라이브 스트림 블레이드를 별도 창에서 엽니다. 대기 중인 성능 테스트가 시작될 때를 볼 수 있으며 동시에 라이브 스트림을 확인할 수 있습니다.
>


## <a name="troubleshooting"></a>문제 해결

데이터가 없나요? 응용 프로그램이 보호된 네트워크에 있는 경우: 라이브 메트릭 스트림은 Application Insights 원격 분석과는 다른 IP 주소를 사용합니다. 방화벽에서 [해당 포트](app-insights-ip-addresses.md)가 열려 있는지 확인합니다.



## <a name="next-steps"></a>다음 단계
* [Application Insights를 사용하여 사용량 모니터링](app-insights-web-track-usage.md)
* [진단 검색 사용](app-insights-diagnostic-search.md)
* [프로파일러](app-insights-profiler.md)
* [스냅숏 디버거](app-insights-snapshot-debugger.md)
