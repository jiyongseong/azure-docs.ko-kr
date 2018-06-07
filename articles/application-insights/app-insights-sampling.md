---
title: Azure Application Insights의 원격 분석 샘플링 | Microsoft Docs
description: 제어에서 원격 분석의 양을 유지하는 방법입니다.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin; vitalyg
ms.openlocfilehash: 53753a3202362c73356e8e39bfca9d813f6387e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869906"
---
# <a name="sampling-in-application-insights"></a>Application Insights의 샘플링


샘플링은 [Azure Application Insights](app-insights-overview.md)의 기능입니다. 응용 프로그램 데이터의 올바른 분석을 통계적으로 유지하는 동안 원격 분석 트래픽 및 저장소를 줄일 수 있는 좋은 방법입니다. 필터는 관련된 항목을 선택하여 진단 조사를 수행할 때 항목 간을 탐색할 수 있도록 합니다.
포털에서 메트릭 개수가 나타나면 통계에 영향을 최소화하기 위해 샘플링을 고려하도록 다시 정상화됩니다.

샘플링은 트래픽 및 데이터 비용을 줄여주며 제한을 피하는 데 도움이 됩니다.

## <a name="in-brief"></a>개요:
* 샘플링은 *n* 레코드에 1을 유지하면서 나머지는 무시합니다. 예를 들어 20%의 샘플링 속도로 5개의 이벤트 1을 유지할 수 있습니다. 
* 응용 프로그램이 다양한 원격 분석을 보내는 경우 샘플링은 ASP.NET 웹 서버 앱에서 자동으로 발생합니다.
* 샘플링을 포털의 사용량 및 예상 비용 페이지나 ASP.NET SDK의 .config 파일 또는 Java SDK의 ApplicationInsights.xml 파일에서 수동으로 설정하여 네트워크 트래픽을 줄일 수도 있습니다.
* 사용자 지정 이벤트를 기록하고 일련의 이벤트가 유지되는지 아니면 함께 무시되는지 확인하려는 경우 동일한 OperationId 값을 갖는지 확인합니다.
* 샘플링 약수 *n*은 `itemCount` 속성의 각 레코드에서 보고되며 이는 검색의 이름 "요청 개수" 또는 "이벤트 개수"에 나타납니다. 샘플링이 작업 중이지 않을 때 `itemCount==1`입니다.
* 분석 쿼리를 작성하는 경우 [샘플링을 고려](app-insights-analytics-tour.md#counting-sampled-data)해야 합니다. 특히, 레코드를 단순히 세는 대신 `summarize sum(itemCount)`를 사용해야 합니다.

## <a name="types-of-sampling"></a>샘플링 유형
다음은 세 가지 대체 샘플링 방법입니다.

* **적응 샘플링** 은 ASP.NET 앱의 SDK에서 전송되는 원격 분석의 양을 자동으로 조정합니다. SDK v 2.0.0-beta3부터는 기본 샘플링 방법입니다. 적응 샘플링은 현재 ASP.NET 서버 쪽 원격 분석에만 사용할 수 있습니다. 전체 Framework를 대상으로 하는 ASP.NET Core 응용 프로그램의 경우 Microsoft.ApplicationInsights.AspNetCore SDK 버전 1.0.0에서 적응 샘플링을 사용할 수 있습니다. NetCore를 대상으로 하는 ASP.NET Core 응용 프로그램의 경우 Microsoft.ApplicationInsights.AspNetCore SDK 2.2.0-beta1에서 적응 샘플링을 사용할 수 있습니다.

* **고정 비율 샘플링** 은 ASP.NET 또는 Java 서버와 사용자 브라우저에서 전송되는 원격 분석의 양을 줄입니다. 비율은 사용자가 설정합니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.
* **수집 샘플링**은 Azure Portal에서 작동합니다. 설정한 샘플링 주기에 따라 앱에서 보낸 원격 분석 중 일부를 삭제합니다. 앱에서 보낸 원격 분석 트래픽을 줄이지는 않지만 월별 할당량 내로 유지하는 데 도움이 됩니다. 수집 샘플링의 주요 이점은 앱을 다시 배포하지 않고 샘플링 주기를 설정할 수 있으며, 모든 서버와 클라이언트에 균일하게 작동한다는 것입니다. 

적응 또는 고정 비율 샘플링이 작업 중인 경우 수집 샘플링은 비활성화됩니다.

## <a name="ingestion-sampling"></a>수집 샘플링
이 샘플링 형식은 웹 서버, 브라우저 및 장치의 원격 분석이 Application Insights 서비스 끝점에 도달하는 지점에서 작동합니다. 앱에서 전송되는 원격 분석 트래픽을 줄이지는 않지만 Application Insights에서 처리 및 보존(및 청구)되는 양을 줄입니다.

앱이 월간 할당량을 자주 초과하지만 SDK 기반의 샘플링 유형 중 하나를 사용할 옵션이 없는 경우 이 샘플링 유형을 사용합니다. 

사용량 및 예상 비용 페이지에서 샘플링 주기를 설정합니다.

![응용 프로그램 개요 블레이드에서 설정, 할당량, 샘플을 차례로 클릭한 다음 샘플링 주기를 선택하고 업데이트를 클릭합니다.](./media/app-insights-sampling/04.png)

다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다. 요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 올바르게 유지됩니다.

샘플링에서 무시된 데이터 요소는 [연속 내보내기](app-insights-export-telemetry.md)와 같은 Application Insights 기능에서 사용할 수 없습니다.

SDK 기반 적응 또는 고정 비율 샘플링이 작동되는 동안에는 수집 샘플링이 작동하지 않습니다. Visual Studio에서 ASP.NET SDK를 사용하도록 설정하거나 상태 모니터를 사용하고 수집 샘플링을 사용하지 않도록 설정하는 경우 적응 샘플링이 기본적으로 사용하도록 설정됩니다. SDK의 샘플링 비율이 100%보다 작으면 설정된 수집 샘플링 설정이 무시됩니다.

> [!WARNING]
> 타일에 표시된 값은 수집 샘플링에 대해 설정한 값을 나타냅니다. SDK 샘플링이 작업 중인 경우 실제 샘플링 속도를 나타내지 않습니다.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>웹 서버의 적응 샘플링
적응 샘플링은 ASP.NET v 2.0.0-beta3 이상용 Application Insights SDK에 사용할 수 있으며, 기본적으로 사용하도록 설정됩니다. 

적응 샘플링은 웹 서버 앱에서 Application Insights 서비스 끝점으로 보내는 원격 분석의 양에 영향을 줍니다. 이 양은 지정된 최대 트래픽 속도 내에서 유지되도록 자동으로 조정됩니다.

적은 양의 원격 분석에서는 작동하지 않으므로 사용량이 적은 웹 사이트 또는 디버그 중인 앱은 영향을 받지 않습니다.

목표량을 달성하기 위해 생성된 원격 분석 중 일부가 삭제됩니다. 그러나 다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다. 

요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 샘플링 주기에 맞게 보정되도록 조정되므로 메트릭 탐색기에서 거의 정확한 값으로 표시됩니다.

### <a name="update-nuget-packages"></a>NuGet 패키지 업데이트 ###

프로젝트의 NuGet 패키지를 최신 *시험판* 버전의 Application Insights로 업데이트합니다. Visual Studio의 [솔루션 탐색기]에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, [NuGet 패키지 관리]를 선택하고, **시험판 포함**을 선택한 다음, Microsoft.ApplicationInsights.Web을 검색합니다. 

### <a name="configuring-adaptive-sampling"></a>적응 샘플링 구성 ###

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)의 `AdaptiveSamplingTelemetryProcessor` 노드에서 여러 매개 변수를 조정할 수 있습니다. 표시된 수치는 기본값입니다.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    대상 비율은 **각 서버 호스트에**대한 것을 목표로 하는 적응 알고리즘입니다. 여러 호스트에서 웹앱을 실행하는 경우 Application Insights 포털에서 트래픽을 대상 비율 범위 내에서 유지하기 위해 이 값을 줄입니다.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    현재 비율의 원격 분석 간격이 다시 평가됩니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮출 수 있는 시간 제한입니다.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 높일 수 있는 시간 제한입니다.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최소값입니다.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최대값입니다.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    앱을 막 시작했을 때 할당된 값입니다. 디버깅하는 동안 이 값을 줄이지 마십시오. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    샘플링하지 않으려는 형식의 세미콜론으로 구분된 목록 인식되는 형식: 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정된 형식의 모든 인스턴스가 전송되고 지정되지 않은 형식은 샘플링됩니다.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    샘플링하려는 형식의 세미콜론으로 구분된 목록 인식되는 형식: 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정된 형식이 샘플링되고 다른 형식의 모든 인스턴스가 항상 전송됩니다.


적응 샘플링을 **해제하려면** applicationinsights-config에서 AdaptiveSamplingTelemetryProcessor 노드를 제거합니다.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>대체: 코드에서 적응 샘플링 구성
.config 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다. 이를 통해 샘플링 속도가 다시 계산될 때마다 호출되는 콜백 함수를 지정할 수 있습니다. 예를 들어 이를 사용하여 샘플링 속도를 사용하는 작업을 찾을 수 있습니다.

.config 파일에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거합니다.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([원격 분석 프로세서에 대해 알아봅니다](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>JavaScript를 사용하는 웹 페이지에 대한 샘플링
서버에서 고정 비율 샘플링에 대한 웹 페이지를 구성할 수 있습니다. 

[Application Insights에 대한 웹 페이지를 구성](app-insights-javascript.md)할 때 Application Insights 포털에서 얻은 JavaScript 코드 조각을 수정합니다. (ASP.NET 앱에서 코드 조각은 일반적으로 _Layout.cshtml로 이동합니다.)  계측 키 앞에 `samplingPercentage: 10,`과 같은 줄을 삽입합니다.

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.

서버에서도 고정 비율 샘플링을 사용하도록 설정하는 경우 클라이언트와 서버가 동기화되므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>ASP.NET 및 Java 웹 사이트에 대한 고정 비율 샘플링
고정 비율 샘플링은 웹 서버와 웹 브라우저에서 전송되는 트래픽을 줄입니다. 적응 샘플링과 달리 사용자가 결정한 고정 비율로 원격 분석을 줄어듭니다. 또한 클라이언트와 서버 샘플링을 동기화하여 관련 항목이 유지되도록 합니다. 예를 들어 Search의 페이지 보기에서 관련 요청을 찾을 수 있습니다.

샘플링 알고리즘에는 관련 항목이 유지됩니다. 각 HTTP 요청 이벤트에 대해 요청 및 관련 이벤트가 함께 삭제되거나 전송됩니다. 

메트릭 탐색기에서 요청 및 예외 수와 같은 빈도는 거의 정확한 값이 되도록 계수가 곱해져 샘플링 주기에 맞게 보정됩니다.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>ASP.NET에서 고정 비율 샘플링 구성 ###

1. **시험판** 버전의 Application Insights로 *프로젝트의 NuGet 패키지를 업데이트* 합니다. Visual Studio의 [솔루션 탐색기]에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, [NuGet 패키지 관리]를 선택하고, **시험판 포함**을 선택한 다음, Microsoft.ApplicationInsights.Web을 검색합니다. 
2. **적응 샘플링을 사용하지 않도록 설정**: [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거하거나 주석으로 처리합니다.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 다음 코드 조각을 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에 추가합니다.
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>JAVA에서 고정 비율 샘플링 구성 ###

1. 최신 [Application Insights Java SDK](app-insights-java-get-started.md)를 사용하여 웹 응용 프로그램 다운로드 및 구성

2. ApplicationInsights.xml 파일에 다음 코드 조각을 추가하여 **고정 비율 샘플링 모듈을 사용하도록 설정**합니다.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. 프로세서 태그 “FixedRateSamplingTelemetryProcessor” 내부에 다음 태그를 사용하여 샘플링에서 특정 유형의 원격 분석을 포함하거나 제외할 수 있습니다.
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
샘플링에서 포함하거나 제외할 수 있는 원격 분석 유형은 종속성, 이벤트, 예외, PageView, 요청 및 추적입니다.

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>대체: 서버 코드에서 고정 비율 샘플링을 사용하도록 설정
.config 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([원격 분석 프로세서에 대해 알아봅니다](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>언제 샘플링을 사용합니까?
ASP.NET SDK 버전 2.0.0-beta3 이상을 사용하는 경우 적응 샘플링이 자동으로 사용되도록 설정됩니다. 사용하는 SDK 버전에 관계 없이 수집 샘플링을 사용하면 Application Insights에서 수집된 데이터를 샘플링할 수 있습니다.

기본적으로 Java SDK에서는 샘플링을 사용할 수 없습니다. 현재 고정 비율 샘플링만 지원합니다. 적응 샘플링은 Java SDK에서 지원되지 않습니다.

일반적으로 중소 규모의 응용 프로그램 대부분에서는 샘플링이 필요하지 않습니다. 가장 유용한 진단 정보 및 가장 정확한 통계는 모든 사용자 활동에서 데이터를 수집하여 구합니다. 

샘플링의 주요 장점은 다음과 같습니다.

* 앱이 짧은 시간 간격으로 매우 높은 비율의 원격 분석을 전송할 때 Application Insights 서비스는 ("제한") 데이터 요소를 삭제합니다. 
* 가격 책정 계층에 대한 데이터 요소의 [할당량](app-insights-pricing.md) 을 유지합니다. 
* 원격 분석의 컬렉션에서 네트워크 트래픽을 줄입니다. 

### <a name="which-type-of-sampling-should-i-use"></a>어떤 유형의 샘플링을 사용해야 합니까?
**다음과 같은 경우 수집 샘플링을 사용합니다.**

* 원격 분석의 월간 할당량을 자주 초과하는 경우
* 샘플링을 지원하지 않는 SDK 버전(예: ASP.NET 버전 2 이전)을 사용하는 경우
* 사용자의 웹 브라우저에서 많은 원격 분석이 전송되는 경우

**다음과 같은 경우 고정 비율 샘플링을 사용합니다.**

* ASP.NET 웹 서비스용 Application Insights SDK 버전 2.0.0 이상 또는 Java SDK v2.0.1 이상을 사용하는 경우
* 사용자가 클라이언트 및 서버 간의 동기화된 샘플링을 원하는 경우 [검색](app-insights-diagnostic-search.md)에서 이벤트를 조사하고 있을 때 클라이언트 및 서버에서 페이지 보기 및 http 요청과 같은 관련 이벤트 사이를 탐색할 수 있습니다.
* 사용자가 앱에 대한 적절한 샘플링 비율을 확신하는 경우입니다. 샘플링 비율은 정확한 메트릭을 가져오기 위해 충분히 높아야 하지만 가격 책정 할당량 및 조정 제한을 초과하는 비율보다 낮아야 합니다. 

**다음과 같은 경우 적응 샘플링을 사용합니다.**

다른 형태의 샘플링을 사용하는 조건이 적용되지 않으면 적응 샘플링을 사용하는 것이 좋습니다. 이 샘플링은 ASP.NET 서버 SDK 버전 2.0.0-beta3 이상에서 기본적으로 사용하도록 설정됩니다. 특정 최소 주기에 도달할 때까지 트래픽이 감소되지 않으므로 사용 빈도가 낮은 사이트는 영향을 받지 않습니다.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>샘플링이 작업 중인지 어떻게 알 수 있나요?
적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](app-insights-analytics.md) 를 사용합니다.

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

보존된 각 레코드에서 `itemCount` 은 나타내는 원래 레코드 수를 나타내며 1 + 이전에 삭제된 레코드의 수와 같습니다. 

## <a name="how-does-sampling-work"></a>샘플링은 어떻게 작동되나요?
ASP.NET 버전 2.0.0 및 Java SDK 버전 2.0.1 이상에서 SDK의 고정 비율 샘플링 기능입니다. 적응 샘플링은 ASP.NET 버전 2.0.0 이상에서 SDK의 기능입니다. 수집 샘플링은 Application Insights 서비스의 기능이며 SDK가 샘플링을 수행하지 않는 경우 작동될 수 있습니다. 

샘플링 알고리즘은 어떤 원격 분석 항목을 삭제할 것인지 및 유지(SDK 또는 Application Insights 서비스에)할 것인지를 결정합니다. 샘플링 의사 결정은 상호 관련된 데이터 요소를 그대로 유지하려는 목표의 여러 규칙에 기반하며 이는 감소된 데이터 집합을 사용하더라도 실행할 수 있고 신뢰할 수 있는 Application Insights에서 진단 환경을 유지 관리합니다. 예를 들어 실패한 요청의 경우 앱이 추가 원격 분석 항목을 전송하면(예: 해당 요청에서 기록된 예외 및 추적) 샘플링은 해당 요청 및 기타 원격 분석을 분할하지 않습니다. 전체를 유지하거나 삭제합니다. 결과적으로 Application Insights에서 요청 세부 정보를 볼 때 항상 연결된 원격 분석 항목과 함께 요청을 확인할 수 있습니다. 

샘플링 결정은 요청의 작업 ID를 기준으로 하므로 특정 작업에 속하는 모든 원격 분석 항목이 유지되거나 삭제됩니다. 작업 ID가 없는 원격 분석 항목의 경우(예: http 컨텍스트가 없는 비동기 스레드에서 보고된 원격 분석 항목) 샘플링은 단순히 각 형식의 원격 분석 항목 백분율을 캡처합니다. .NET SDK 2.5.0-beta2 및 ASP.NET Core SDK 2.2.0-beta3 이전에는 "사용자"를 정의하는 응용 프로그램(즉, 가장 일반적인 웹 응용 프로그램)의 사용자 ID 해시를 기준으로 샘플링 결정이 이루어졌습니다. 사용자를 정의하지 않는 응용 프로그램 형식의 경우(예: 웹 서비스) 샘플링 의사 결정은 요청의 작업 ID를 기반으로 했습니다.

원격 분석을 다시 표시하는 경우 Application Insights 서비스는 누락된 데이터 요소를 보완하기 위해 컬렉션의 시간에 사용된 동일한 샘플링 백분율로 메트릭을 조정합니다. 따라서 Application Insights에서 원격 분석을 보면 사용자는 실제 수에 가까운 통계적으로 올바른 근사치를 확인할 수 있습니다.

근사치의 정확도는 주로 구성된 샘플링 비율에 따라 다릅니다. 또한 아주 많은 사용자에게서 많은 양의 일반적으로 비슷한 요청을 처리하는 응용 프로그램에 대해 정확도가 증가합니다. 반면에 상당한 부하가 있을 때 작동하지 않는 응용 프로그램의 경우 이러한 응용 프로그램이 일반적으로 할당량 내에 있으면서 제한에서 데이터 손실이 발생하지 않고 해당 원격 분석을 모두 보낼 수 있기에 샘플링은 필요하지 않습니다. 

> [!WARNING]
> Application Insights는 메트릭 및 세션 원격 분석 유형을 샘플링하지 않습니다. 정밀도 하락은 이러한 원격 분석 유형에 매우 좋지 못할 수 있습니다.
> 

### <a name="adaptive-sampling"></a>적응 샘플링
적응 샘플링은 SDK에서 현재의 전송 속도를 모니터링하는 구성 요소를 추가하여 샘플링 비율이 대상 최대 비율 안에서 유지되도록 조정합니다. 조정은 정기적으로 다시 계산되고 발신 전송 속도의 이동 평균에 기반합니다.

## <a name="sampling-and-the-javascript-sdk"></a>샘플링 및 JavaScript SDK
클라이언트 측(JavaScript) SDK는 서버 측 SDK과 함께 고정 비율 샘플링에 사용됩니다. 계측된 페이지는 서버 측이 "샘플"로 결정한 동일한 사용자의 클라이언트 측 원격 분석만을 보냅니다. 이 논리는 전체 클라이언트 및 서버 측에 대한 사용자 세션의 무결성을 유지하도록 설계되었습니다. 결과적으로 Application Insights의 특정 원격 분석 항목에서 해당 사용자 또는 세션에 대한 다른 모든 원격 분석 항목을 찾을 수 있습니다. 

*위에 설명한 대로 내 클라이언트 및 서버 측 원격 분석은 조정된 샘플을 표시하지 않습니다.*

* 서버 및 클라이언트 모두에서 고정 비율 샘플링을 사용할 수 있는지 확인합니다.
* SDK 버전이 2.0 이상이어야 합니다.
* 클라이언트와 서버 모두에서 동일한 샘플링 비율을 설정하도록 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답
*샘플링은 왜 간단히 "각 원격 분석 형식의 X 퍼센트를 수집"하지 않습니까?*

* 이 샘플링 방법이 메트릭 근사치에 매우 높은 정밀도를 제공하지만 사용자, 세션 및 요청 단위 당 진단 데이터를 연결하는 기능을 중단시키며 이는 진단에 있어 매우 중요합니다. 따라서 샘플링은 "앱 사용자의 X 퍼센트에 대해 모든 원격 분석 항목 수집" 또는 "앱 요청의 X퍼센트에 대한 모든 원격 분석 수집" 논리를 사용하여 더 잘 작동합니다. 요청과 연결되지 않은 원격 분석 항목의 경우(예: 배경 비동기 처리) "각 원격 분석 형식에 대해 모든 항목의 X퍼센트 수집"으로 대체됩니다. 

*샘플링 비율은 시간이 지나면서 달라질 수 있습니까?*

* 예, 적응 샘플링은 원격 분석의 현재 관찰된 양에 따라 샘플링 비율을 점차적으로 변경합니다.

*고정 비율 샘플링을 사용하는 경우 내 앱에 가장 적합한 샘플링 비율을 어떻게 알 수 있습니까?*

* 한 가지 방법은 적응 샘플링으로 시작하고 안정적인 비율(위 질문 참조)을 찾은 다음 해당 비율을 사용하여 고정 비율 샘플링으로 전환하는 것입니다. 
  
    그렇지 않으면 추측해야 합니다. Application Insights에서 현재 원격 분석 사용을 분석하고, 발생하는 모든 제한을 관찰하며, 수집된 원격 분석의 양을 추정합니다. 선택된 가격 책정 계층과 함께 이러한 세 가지 입력은 수집된 원격 분석의 볼륨을 얼마나 줄여야 할지 제안합니다. 그러나 사용자 수가 증가하거나 원격 분석의 양이 약간 다르게 변화되면 추정치가 무효화될 수도 있습니다.

*샘플링 비율을 너무 낮게 구성하는 경우 어떻게 됩니까?*

* 샘플링 비율이 지나치게 낮으면(지나친 샘플링) Application Insights가 데이터의 볼륨을 축소하기 위해 데이터의 시각화를 보완하려고 할 때 근사치의 정확도가 줄어듭니다. 또한 종종 실패하거나 요청이 느린 경우 샘플링되기에 진단 환경은 부정적으로 영향을 받습니다.

*샘플링 비율을 너무 높게 구성하는 경우 어떻게 됩니까?*

* 샘플링 비율을 너무 높게 구성하면(충분히 공격적이지 않음) 수집된 원격 분석의 볼륨이 충분히 감소되지 않습니다. 여전히 제한에 관련된 원격 분석 데이터 손실이 발생할 수 있고 Application Insights를 사용하는 비용은 초과 요금때문에 계획된 것 보다 클 수 있습니다.

*샘플링을 어떤 플랫폼에서 사용할 수 있습니까?*

* SDK가 샘플링을 수행하지 않는 경우 특정 볼륨을 초과하는 모든 원격 분석에 대해 자동으로 수집 샘플링이 발생할 수 있습니다. 예를 들어 이전 버전의 ASP.NET SDK 또는 이전 버전의 Java SDK(1.0.10 또는 이전 버전)를 사용하는 경우 수집 샘플링이 수행됩니다.
* ASP.NET SDK 버전 2.0.0 이상을 사용(Azure 또는 자체 서버에 호스트됨)하는 경우 기본적으로 적응 샘플링이지만 위에서 설명한 것처럼 고정 비율 샘플링으로 전환할 수 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다. 
* Java SDK 버전 2.0.1 이상을 사용하는 경우, 고정 비율 샘플링을 켜도록 ApplicationInsights.xml을 구성할 수 있습니다. 샘플링은 기본적으로 꺼져 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다.

*항상 보고 싶은 확실히 드문 이벤트가 있습니다. 이전의 샘플링 모듈에서 그 이벤트를 어떻게 가져올 수 있습니까?*

* 기본 활성값이 아닌 새 TelemetryConfiguration을 사용하여 별도의 TelemetryClient 인스턴스를 초기화합니다. 이를 사용하여 드문 이벤트를 보냅니다.

## <a name="next-steps"></a>다음 단계
* [필터링](app-insights-api-filtering-sampling.md) 은 SDK에서 보내는 항목을 더 엄격하게 제어할 수 있습니다.

