---
title: Azure Application Insights의 종속성 추적 | Microsoft Docs
description: Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 99d9ad04ac39d6d0072b13c81e74605e48de175b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010467"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Application Insights 설정: 종속성 추적
*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. [Application Insights](app-insights-overview.md)는 응용 프로그램이 종속성을 기다리는 시간과 종속성 호출에 실패하는 빈도를 측정합니다. 특정 호출을 조사하여 요청 및 예외와 연관지을 수 있습니다.

![예제 차트](./media/app-insights-asp-net-dependencies/10-intro.png)

기본적으로 종속성 모니터는 현재 다음 유형의 종속성에 대한 호출을 보고합니다.

* 서버
  * SQL 데이터베이스
  * ASP.NET 웹 및 HTTP 기반 바인딩을 사용하는 WCF 서비스
  * 로컬 또는 원격 HTTP 호출
  * Azure Cosmos DB, 테이블, Blob Storage 및 큐
* 웹 페이지
  * AJAX 호출

모니터링은 선택한 방법과 관련된 [바이트 코드 계측](https://msdn.microsoft.com/library/z9z62c29.aspx)을 사용하여 작동합니다. 성능 오버 헤드가 최소화됩니다.

[TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency)를 사용하여 클라이언트 및 서버 코드 모두에서 다른 종속성을 모니터링하는 사용자 고유의 SDK 호출을 작성할 수도 있습니다.

## <a name="set-up-dependency-monitoring"></a>종속성 모니터링 설정
부분 종속성 정보는 [Application Insights SDK](app-insights-asp-net.md)에서 자동으로 수집됩니다. 전체 데이터를 가져오려면 호스트 서버에 대한 적절한 에이전트를 설치합니다.

| 플랫폼 | 설치 |
| --- | --- |
| IIS 서버 |[상태 모니터를 서버에 설치](app-insights-monitor-performance-live-website-now.md) 또는 [응용 프로그램을 .NET Framework 4.6 이상으로 업그레이드](http://go.microsoft.com/fwlink/?LinkId=528259)하고 앱에 [Application Insights SDK](app-insights-asp-net.md)를 설치합니다. |
| Azure 웹앱 |웹앱 제어판에서 [Application Insights 블레이드를 열고](app-insights-azure-web-apps.md) 메시지가 표시되면 설치를 선택합니다. |
| Azure 클라우드 서비스 |[시작 작업 사용](app-insights-cloudservices.md) 또는 [.NET Framework 4.6+ 설치](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>종속성 데이터를 찾을 수 있는 위치
* [응용 프로그램 맵](#application-map): 앱과 인접 구성 요소 간의 종속성을 시각화합니다.
* [성능, 브라우저 및 실패 블레이드](#performance-and-blades): 서버 종속성 데이터를 표시합니다.
* [브라우저 블레이드](#ajax-calls): 사용자 브라우저에서의 AJAX 호출을 표시합니다.
* [느리거나 실패한 요청 클릭](#diagnose-slow-requests): 해당 종속성 호출을 확인합니다.
* [분석](#analytics): 종속성 데이터를 쿼리하는 데 사용됩니다.

## <a name="application-map"></a>응용 프로그램 맵
응용 프로그램 맵은 응용 프로그램의 구성 요소 간에 종속성을 검색하는 시각화 보조 도구의 역할을 합니다. 앱의 원격 분석에서 자동으로 생성됩니다. 이 예제에서는 두 외부 서비스에 대한 브라우저 스크립트에서의 AJAX 호출과 서버 앱에서의 REST 호출을 보여 줍니다.

![응용 프로그램 맵](./media/app-insights-asp-net-dependencies/08.png)

* **상자에서 관련 종속성 및 기타 차트로 이동**합니다.
* [대시보드](app-insights-dashboards.md)에 **맵을 고정**하면 완벽하게 작동될 수 있습니다.

[자세히 알아보세요](app-insights-app-map.md)을 확인하세요.

## <a name="performance-and-failure-blades"></a>성능 및 실패 블레이드
성능 블레이드에는 서버 앱에 실행한 종속성 호출 기간이 표시됩니다. 호출별로 구분된 요약 차트와 테이블이 있습니다.

![성능 블레이드 종속성 차트](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

요약 차트 또는 테이블 항목을 클릭하여 이러한 호출의 원시 발생을 검색합니다.

![종속성 호출 인스턴스](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**실패 횟수**는 **실패** 블레이드에 표시됩니다. 실패는 200~399 범위에 없거나 알 수 없는 반환 코드입니다.

> [!NOTE]
> **100% 실패?** - 이는 부분 종속성 데이터만 가져옴을 의미할 수 있습니다. [플랫폼에 적절한 종속성 모니터링을 설정](#set-up-dependency-monitoring)해야 합니다.
>
>

## <a name="ajax-calls"></a>AJAX 호출
브라우저 블레이드에는 [웹 페이지의 JavaScript](app-insights-javascript.md)에서 실행한 AJAX 호출의 기간 및 실패율이 표시됩니다. 이는 종속성으로 표시됩니다.

## <a name="diagnosis"></a> 느린 요청 진단
각 요청 이벤트는 종속성 호출, 예외 및 기타 앱에서 요청을 처리하는 동안 추적된 이벤트와 연결됩니다. 따라서 일부 요청이 잘못 수행되는 경우 종속성의 느린 응답 때문인지 여부를 확인할 수 있습니다.

이에 대한 예제를 살펴보겠습니다.

### <a name="tracing-from-requests-to-dependencies"></a>요청에서 종속성까지 추적
성능 블레이드를 열고 요청 표를 확인합니다.

![평균 및 개수가 포함된 요청 목록](./media/app-insights-asp-net-dependencies/02-reqs.png)

맨 위의 요청은 시간이 아주 오래 걸립니다. 시간이 어디에 소비되는지 확인해 보겠습니다.

개별 요청 이벤트를 보려면 해당 행을 클릭합니다.

![요청 항목 목록](./media/app-insights-asp-net-dependencies/03-instances.png)

장기 실행 인스턴스를 클릭하여 세부 사항을 조사합니다. 이 요청과 관련된 원격 종속성 호출까지 스크롤합니다.

![원격 종속성에 대한 호출 찾기, 특별한 기간 식별](./media/app-insights-asp-net-dependencies/04-dependencies.png)

이 요청을 서비스하는 데 걸린 시간은 대부분 로컬 서비스 호출에 소요된 시간인 것 같습니다.

해당 행을 선택하여 자세한 정보를 봅니다.

![해당 원격 종속성을 클릭하여 원인 식별](./media/app-insights-asp-net-dependencies/05-detail.png)

여기에 문제가 있는 것 같습니다. 문제를 확인했으므로 이제 호출에 시간이 오래 걸리는 이유를 확인하면 됩니다.

### <a name="request-timeline"></a>요청 시간 표시 막대
다른 경우에는 특별히 긴 종속성 호출이 없습니다. 그러나 시간 표시 막대 뷰로 전환하면 내부 처리에 지연이 발생하는 것을 확인할 수 있습니다.

![원격 종속성에 대한 호출 찾기, 특별한 기간 식별](./media/app-insights-asp-net-dependencies/04-1.png)

첫 번째 종속성 호출 후 큰 간격이 있어 보이므로 코드를 보고 그 이유를 확인해야 합니다.

### <a name="profile-your-live-site"></a>라이브 사이트 프로파일링

시간에 따른 위치를 알 수 없나요? [Application Insights 프로파일러](app-insights-profiler.md)는 라이브 사이트에 대한 HTTP 호출을 추적하고 가장 오래 걸린 코드의 함수를 표시합니다.

## <a name="failed-requests"></a>실패한 요청
실패한 요청은 종속성에 대한 실패한 호출과 연관이 있을 수도 있습니다. 문제를 클릭하여 추적할 수 있습니다.

![실패한 요청 차트 클릭](./media/app-insights-asp-net-dependencies/06-fail.png)

실패한 요청 발생을 클릭하고 연관된 이벤트를 확인합니다.

![요청 유형을 클릭하고, 인스턴스를 클릭하여 동일한 인스턴스의 다른 보기로 이동하고, 클릭하여 예외 세부 정보를 표시합니다.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>분석
[Log Analytics 쿼리 언어](https://docs.loganalytics.io/)에서 종속성을 추적할 수 있습니다. 다음은 몇 가지 예제입니다.

* 실패한 종속성 호출을 찾습니다.

```

    dependencies | where success != "True" | take 10
```

* AJAX 호출을 찾습니다.

```

    dependencies | where client_Type == "Browser" | take 10
```

* 요청과 연관된 종속성 호출을 찾습니다.

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 페이지 보기와 관련된 AJAX 호출을 찾습니다.

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>사용자 지정 종속성 추적
표준 종속성 추적 모듈은 데이터베이스 및 REST API와 같은 외부 종속성을 자동으로 검색합니다. 하지만 일부 추가 구성 요소를 동일한 방식으로 취급할 수도 있습니다.

표준 모듈에 의해 사용되는 동일한 [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) 를 사용하여 종속성 정보를 보내는 코드를 작성할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

표준 종속성 추적 모듈을 해제하려는 경우, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 DependencyTrackingTelemetryModule에 대한 참조를 삭제합니다.

## <a name="troubleshooting"></a>문제 해결
*종속성 성공 플래그는 항상 true 또는 false로 표시됩니다.*

*SQL 쿼리가 일부만 표시됩니다.*

* Application Insights SDK의 안정적인 최신 버전으로 업그레이드합니다.

 .NET 버전이 4.6 미만인 경우:

* IIS 호스트: 호스트 서버에 [Application Insights 에이전트](app-insights-monitor-performance-live-website-now.md)를 설치합니다.
* Azure 웹앱: 웹앱 제어판에서 Application Insights 탭을 열고 Application Insights를 설치합니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계
* [예외](app-insights-asp-net-exceptions.md)
* [사용자 및 페이지 데이터](app-insights-javascript.md)
* [Availability](app-insights-monitor-web-app-availability.md)
