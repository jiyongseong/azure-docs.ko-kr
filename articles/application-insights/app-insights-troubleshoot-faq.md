---
title: Azure Application Insights FAQ | Microsoft Docs
description: Application Insights에 대한 질문과 대답입니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: d7abfd1ac6f914c75297ff49462590e5b6169dbd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310017"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: 질문과 대답

## <a name="configuration-problems"></a>구성 문제
*다음을 설정하는 데 문제가 있습니다.*

* [.NET 앱](app-insights-asp-net-troubleshoot-no-data.md)
* [이미 실행 중인 앱 모니터링](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure 진단](app-insights-azure-diagnostics.md)
* [Java 웹앱](app-insights-java-troubleshoot.md)

*내 서버에서 데이터를 가져오지 않습니다.*

* [방화벽 예외 설정](app-insights-ip-addresses.md)
* [ASP.NET 서버 설정](app-insights-monitor-performance-live-website-now.md)
* [Java 서버 설정](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Application Insights와 같이 사용할 수 있나요...?

* [IIS 서버의 웹앱 - 온-프레미스 또는 VM](app-insights-asp-net.md)
* [Java 웹앱](app-insights-java-get-started.md)
* [Node.js 앱](app-insights-nodejs.md)
* [Azure의 Web Apps](app-insights-azure-web-apps.md)
* [Azure의 Cloud Services](app-insights-cloudservices.md)
* [Docker에서 실행되는 앱 서버](app-insights-docker.md)
* [단일 페이지 웹앱](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Windows 데스크톱 앱](app-insights-windows-desktop.md)
* [기타 플랫폼](app-insights-platforms.md)

## <a name="is-it-free"></a>무료입니까?

예, 실험용입니다. 기본 가격 책정 계획에서, 사용자 응용 프로그램은 매월 무료로 특정 데이터 할당량을 보낼 수 있습니다. 무료 할당량은 적은 수의 사용자에 대한 앱 개발 및 게시에 충분합니다. 지정된 데이터 크기보다 더 많이 처리하지 않도록 한도를 설정할 수 있습니다.

더 큰 볼륨의 원격 분석은 GB 단위로 청구됩니다. [요금 제한](app-insights-pricing.md) 방법에 대한 몇 가지 팁을 제공합니다.

엔터프라이즈 계획에서는 각 웹 서버 노드에서 원격 분석을 전송하는 각 날짜에 대해 요금이 발생합니다. 대규모로 연속 내보내기를 사용하려는 경우에 적합합니다.

[가격 책정 계획을 참조하세요](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>비용은 어느 정도인가요?

* Application Insights 리소스에서 **사용량 및 예상 비용 페이지**를 엽니다. 최근 사용 현황에 대한 차트가 있습니다. 원하는 경우 데이터 볼륨 한도를 설정할 수 있습니다.
* [Azure 청구 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)를 열어 모든 리소스에 대한 청구서를 확인합니다.

## <a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?
세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 응용 프로그램의 경우:

* 프로젝트에 다음 파일이 추가됩니다.

  * ApplicationInsights.config
  * ai.js
* 다음 NuGet 패키지가 설치됩니다.

  * *Application Insights API* - 핵심 API
  * *웹 응용 프로그램용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용
  * *JavaScript 응용 프로그램용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용

    패키지에는 다음 어셈블리가 포함됩니다.
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 항목 삽입 위치:

  * Web.config
  * packages.config
* (새 프로젝트에만 해당 - [기존 프로젝트에 Application Insights를 추가한 경우][start] 이를 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드를 마스터 페이지 Views/Shared/_Layout.cshtml에 삽입합니다.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?
사용자의 응용 프로그램 유형에 적합한 SDK는 [릴리스 정보](app-insights-release-notes.md) 를 참조하세요.

## <a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?
솔루션 탐색기에서 `ApplicationInsights.config` 를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

## <a name="what-is-status-monitor"></a>상태 모니터란?

웹앱에서 Application Insights를 구성하기 위해 IIS 웹 서버에서 사용할 수 있는 데스크톱 앱입니다. 원격 분석을 수집하지 않으며 앱을 구성하지 않는 경우 중지할 수 있습니다. 

[자세히 알아보기](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>어떤 원격 분석이 Application Insights에서 수집되나요?

서버 웹앱:

* HTTP 요청
* [종속성](app-insights-asp-net-dependencies.md). 호출: SQL Database, 외부 서비스(Azure Cosmos DB, 테이블, Blob Storage 및 큐)에 대한 HTTP 호출. 
* [예외](app-insights-asp-net-exceptions.md) 및 스택 추적.
* [성능 카운터](app-insights-performance-counters.md) - [상태 모니터](app-insights-monitor-performance-live-website-now.md)를 사용하는 경우 [Azure 모니터링](app-insights-azure-web-apps.md) 또는 [Application Insights collectd 기록기](app-insights-java-collectd.md).
* 코딩하는 [사용자 지정 이벤트 및 메트릭](app-insights-api-custom-events-metrics.md).
* 적절한 수집기를 구성한 경우 [추적 로그](app-insights-asp-net-trace-logs.md).

[클라이언트 웹 페이지](app-insights-javascript.md):

* [페이지 보기 수](app-insights-web-track-usage.md)
* 실행되는 스크립트의 [AJAX 호출](app-insights-asp-net-dependencies.md) 요청.
* 페이지 보기 로드 데이터
* 사용자 및 세션 수
* [인증된 사용자 ID](app-insights-api-custom-events-metrics.md#authenticated-users)

다른 원본(구성한 경우):

* [Azure 진단](app-insights-azure-diagnostics.md)
* [Docker 컨테이너](app-insights-docker.md)
* [분석으로 테이블 가져오기](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>일부 원격 분석을 필터링하거나 수정할 수 있나요?

예, 서버에서 다음과 같이 할 수 있습니다.

* 원격 분석 프로세서를 작성하여 앱에서 전송되기 전에 선택한 원격 분석 항목을 필터링하거나 속성을 추가할 수 있습니다.
* 원격 분석 이니셜라이저를 작성하여 원격 분석의 모든 항목에 속성을 추가할 수 있습니다.

[ASP.NET](app-insights-api-filtering-sampling.md) 또는 [Java](app-insights-java-filter-telemetry.md)에 대해 자세히 알아보세요.

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>도시, 국가 및 기타 지리적 위치 데이터는 어떻게 계산되나요?

[GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/)를 사용하여 웹 클라이언트의 IP 주소(IPv4 또는 IPv6)를 조회합니다.

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 수집합니다.
* 서버 원격 분석: Application Insights 모듈에서 클라이언트 IP 주소를 수집합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.

`ClientIpHeaderTelemetryInitializer`를 구성하여 다른 헤더에서 IP 주소를 가져올 수 있습니다. 예를 들어 일부 시스템에서는 프록시, 부하 분산 장치 또는 CDN에 의해 `X-Originating-IP`로 이동됩니다. [자세히 알아보기](http://apmtips.com/blog/2016/07/05/client-ip-address/).

[Power BI를 사용](app-insights-export-power-bi.md)하여 요청 원격 분석을 지도에 표시할 수 있습니다.


## <a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?
[데이터 보존 및 개인 정보][data]를 살펴보십시오.

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>PII(개인 식별이 가능한 정보)가 원격 분석으로 전송될 수 있나요?

코드에서 이러한 데이터를 전송하는 경우 가능합니다. 스택 추적의 변수에 PII가 포함된 경우에도 전송될 수 있습니다. 개발 팀은 PII가 제대로 처리되도록 위험 평가를 수행해야 합니다. [데이터 보존 및 개인 정보](app-insights-data-retention-privacy.md)에 대해 자세히 알아보세요.

클라이언트 웹 주소의 **모든** 8진수는 지리적 위치 특성을 조회한 후에 항상 0으로 설정됩니다.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>내 iKey가 내 웹 페이지 원본에 표시됩니다. 

* 모니터링 솔루션에서 일반적으로 사용됩니다.
* 데이터를 훔치는 데 사용할 수 없습니다.
* 데이터를 기울이거나 경고를 트리거하는 데 사용할 수 있습니다.
* 고객에게 이러한 문제가 발생한 경우는 없었습니다.

다음과 같이 할 수 있습니다.

* 클라이언트 데이터와 서버 데이터에 별도의 iKey(별도의 Application Insights 리소스) 두 개를 사용합니다. 또는
* 서버에서 실행되는 프록시를 작성하고 웹 클라이언트에서 해당 프록시를 통해 데이터를 전송하도록 합니다.

## <a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?
POST 데이터를 자동으로 기록 하지 않지만 TrackTrace 호출을 사용할 수 있습니다. 메시지 매개 변수에 데이터를 배치합니다. 이것은 필터링할 수 없지만 문자열 속성에 대한 제한보다 긴 제한이 있습니다.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>단일 또는 여러 Application Insights 리소스를 사용해야 하나요?

단일 비즈니스 시스템의 모든 구성 요소 또는 역할에 대해 단일 리소스를 사용합니다. 개발, 테스트 및 릴리스 버전과 독립 응용 프로그램에 대해 별도의 리소스를 사용합니다.

* [여기에서 토론 참조](app-insights-separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>계측 키를 동적으로 변경하려면 어떻게 해야 하나요?

* [여기에서 토론 참조](app-insights-separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>사용자 및 세션 수란?

* JavaScript SDK는 웹 클라이언트에서 사용자 쿠키를 설정하여 다시 방문하는 사용자와 그룹 작업에 대한 세션 쿠키를 식별합니다.
* 클라이언트 쪽 스크립트가 없으면 [서버에서 쿠키를 설정](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)할 수 있습니다.
* 한 명의 실제 사용자가 특정 사이트를 다른 브라우저에서 사용하거나, in-private/incognito 검색을 통해 사용하거나, 다른 컴퓨터에서 사용하는 경우 두 번 이상 계산됩니다.
* 여러 컴퓨터 및 브라우저에서 로그인한 사용자를 식별하려면 [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users)에 대한 호출을 추가합니다.

## <a name="q17"></a> Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?
| 표시 내용 | 시작 방법 | 원하는 이유 |
| --- | --- | --- |
| 가용성 차트 |[웹 테스트](app-insights-monitor-web-app-availability.md) |웹 앱이 작동 중인지 확인 |
| 서버 앱 성능: 응답시간. ... |[프로젝트에 Application Insights 추가](app-insights-asp-net.md) 또는 [서버에 AI 상태 모니터 설치](app-insights-monitor-performance-live-website-now.md)(또는 [종속성 추적](app-insights-api-custom-events-metrics.md#trackdependency)을 위한 자체 코드 작성) |성능 문제 검색 |
| 종속성 원격 분석 |[서버에 AI 상태 모니터 설치](app-insights-monitor-performance-live-website-now.md) |데이터베이스 또는 다른 외부 구성 요소의 문제 진단 |
| 예외에서 스택 추적 가져오기 |[TrackException 호출을 코드에 삽입](app-insights-asp-net-exceptions.md)(하지만 일부는 자동으로 보고됨) |예외 감지 및 진단 |
| 로그 추적 검색 |[로깅 어댑터 추가](app-insights-asp-net-trace-logs.md) |예외, 성능 문제 진단 |
| 클라이언트 사용 기본 사항: 페이지 보기, 세션,... |[웹 페이지의 JavaScript 이니셜라이저](app-insights-javascript.md) |사용 현황 분석 |
| 클라이언트 사용자 지정 메트릭 |[웹 페이지에서 추적 호출](app-insights-api-custom-events-metrics.md) |사용자 환경 향상 |
| 서버 사용자 지정 메트릭 |[서버에서 호출 추적](app-insights-api-custom-events-metrics.md) |비즈니스 인텔리전스 |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>검색 및 메트릭 차트의 수가 다른 이유는 무엇인가요?

[샘플링](app-insights-sampling.md)을 하면 실제로 앱에서 포털로 전송되는 원격 분석 항목(요청, 사용자 지정 이벤트 등)의 수가 줄어듭니다. 검색에는 실제로 받은 항목 수가 표시됩니다. 이벤트 수를 표시하는 메트릭 차트에는 발생된 원래 이벤트 수가 표시됩니다. 

전송되는 각 항목은 해당 항목이 나타내는 원래 이벤트의 수를 보여 주는 `itemCount` 속성을 전달합니다. 작업 중인 샘플링을 관찰하려면 분석에서 다음 쿼리를 실행할 수 있습니다.

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Application Insights 구성

Azure Resource Monitor를 통해 [PowerShell 스크립트를 작성](app-insights-powershell.md)하여 다음을 수행할 수 있습니다.

* Application Insights 리소스를 만들고 업데이트합니다.
* 가격 책정 계획을 설정합니다.
* 계측 키를 가져옵니다.
* 메트릭 경고를 추가합니다.
* 가용성 테스트를 추가합니다.

메트릭 탐색기 보고서를 설정하거나 연속 내보내기를 설정할 수는 없습니다.

### <a name="querying-the-telemetry"></a>원격 분석 쿼리

[REST API](https://dev.applicationinsights.io/)를 사용하여 [분석](app-insights-analytics.md) 쿼리를 실행합니다.

## <a name="how-can-i-set-an-alert-on-an-event"></a>이벤트에 대한 경고를 설정하려면 어떻게 해야 하나요?

Azure 경고는 메트릭에 대해서만 설정됩니다. 이벤트가 발생할 때마다 값 임계값을 초과하는 사용자 지정 메트릭을 만듭니다. 그런 다음 메트릭에 대해 경고를 설정합니다. 이때 메트릭이 어느 방향으로든 임계값을 초과할 때마다 알림을 받게 됩니다. 초기 값이 높은지 낮은지에 관계없이 첫 번째 초과까지는 알림을 받지 않습니다. 항상 몇 분 정도의 대기 시간이 있습니다.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure 웹앱과 Application Insights 간 데이터 전송 요금이 있나요?

* Azure 웹앱이 Application Insights 컬렉션 끝점이 있는 데이터 센터에서 호스트되는 경우 무료입니다. 
* 호스트 데이터 센터에 컬렉션 끝점이 없으면 앱의 원격 분석에 [Azure 발신 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 부과됩니다.

이 요금은 Application insights 리소스가 호스트되는 위치에 따라 달라지지 않습니다. 끝점의 배포에 따라 달라집니다.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights 포털에 원격 분석을 보낼 수 있나요?

저희가 제공하는 SDK 및 [SDK API](app-insights-api-custom-events-metrics.md)를 사용하는 것이 좋습니다. 다양한 [플랫폼](app-insights-platforms.md)에 따라 여러 SDK 변형이 있습니다. 이러한 SDK는 버퍼링, 압축, 제한, 다시 시도 등을 처리합니다. 그러나 [수집 스키마](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) 및 [끝점 프로토콜](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)은 공용입니다.

## <a name="can-i-monitor-an-intranet-web-server"></a>인트라넷 웹 서버를 모니터링할 수 있나요?

다음은 두 가지 방법입니다.

### <a name="firewall-door"></a>방화벽 문

그러면 웹 서버가 https://dc.services.visualstudio.com:443 및 https://rt.services.visualstudio.com:443 끝점에 원격 분석을 전송할 수 있습니다. 

### <a name="proxy"></a>Proxy

ApplicationInsights.config 예에서 이러한 설정을 덮어써서 서버에서 인트라넷의 게이트웨이로 트래픽을 라우팅합니다. config에 이러한 "엔드포인트" 속성이 없을 경우 이러한 클래스가 아래 예에 표시된 기본값을 사용합니다.

#### <a name="example-applicationinsightsconfig"></a>ApplicationInsights.config 예:
```xml
<ApplicationInsights>
    ...
    <TelemetryChannel>
         <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
    </TelemetryChannel>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

_ApplicationIdProvider는 v2.6.0부터 사용 가능합니다._

게이트웨이는 트래픽을 https://dc.services.visualstudio.com:443으로 라우팅해야 합니다.

위의 값을 `http://<your.gateway.address>/<relative path>`로 바꾸세요.
 
예: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>인트라넷 서버에서 가용성 웹 테스트를 실행할 수 있나요?

[웹 테스트](app-insights-monitor-web-app-availability.md)는 전 세계에 배포된 클라이언트 로그인 지점에서 실행됩니다. 두 가지 해결 방법이 있습니다.

* 방화벽 문 - [길고 변경 가능한 웹 테스트 에이전트 목록](app-insights-ip-addresses.md)에서 서버에 대한 요청을 허용합니다.
* 인트라넷 내부에서 서버에 주기적으로 요청을 보내는 코드를 직접 작성합니다. 이러한 목적으로 Visual Studio 웹 테스트를 실행할 수 있습니다. 테스터는 TrackAvailability() API를 사용하여 결과를 Application Insights에 보낼 수 있습니다.

## <a name="more-answers"></a>추가 대답
* [Application Insights 포럼](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md