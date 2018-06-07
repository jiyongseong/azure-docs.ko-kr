---
title: Application Insights로 앱 상태 및 사용 현황 모니터링
description: Application Insights를 시작합니다. 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: sdash
ms.openlocfilehash: 02421492528e44ed6a913443a7793235170d4881
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159410"
---
# <a name="monitor-performance-in-web-applications"></a>웹 응용 프로그램의 성능 모니터링


응용 프로그램이 정상적으로 작동하는지 확인하고 오류가 발생하는지 신속하게 파악합니다. [Application Insights][start]는 성능 문제 및 예외에 대한 정보와, 이러한 현상에 대한 근본 원인을 확인하고 진단하는 기능을 제공합니다.

Application Insights에서 Java 및 ASP.NET 웹 응용 프로그램과 서비스, WCF 서비스를 모니터링할 수 있습니다. 온-프레미스, 가상 머신에서 또는 Microsoft Azure 웹 사이트로 호스트할 수 있습니다. 

Application Insights는 클라이언트 쪽에서 iOS, Android 및 Windows 스토어 앱을 포함한 다양한 장치 및 웹 페이지에서 원격 분석을 수행할 수 있습니다.

## <a name="setup"></a>성능 모니터링 설정
Application Insights를 아직 프로젝트에 추가하지 않은 경우(프로젝트에 ApplicationInsights.config가 없음) 다음 방법 중 하나를 선택하여 작업을 시작합니다.

* [ASP.NET 웹 앱](app-insights-asp-net.md)
  * [예외 모니터링 추가](app-insights-asp-net-exceptions.md)
  * [종속성 모니터링 추가](app-insights-monitor-performance-live-website-now.md)
* [J2EE 웹앱](app-insights-java-get-started.md)
  * [종속성 모니터링 추가](app-insights-java-agent.md)

## <a name="view"></a>성능 메트릭 탐색
[Azure 포털](https://portal.azure.com)에서 응용 프로그램에 대해 설정한 Application Insights 리소스를 찾습니다. 개요 블레이드에 기본 성능 데이터가 표시됩니다.

무엇이든 클릭하면 추가 세부 정보와 장기간에 걸친 결과를 확인할 수 있습니다. 예를 들어 요청 타일을 클릭하고 시간 범위를 선택합니다.

![클릭하여 추가 데이터를 표시한 다음 시간 범위 선택](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

차트를 클릭하여 표시되는 메트릭을 선택하거나 새 차트를 추가하고 해당 메트릭을 선택합니다.

![그래프를 클릭하여 메트릭 선택](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **모든 메트릭의 선택을 취소합니다** . 메트릭은 그룹으로 구분되며 그룹 내의 모든 멤버를 선택하면 해당 그룹의 다른 멤버만 표시됩니다.

## <a name="metrics"></a>성능 메트릭의 의미 성능 타일 및 보고서
다양한 성능 메트릭을 가져올 수 있습니다. 먼저 응용 프로그램 블레이드에 기본적으로 표시되는 메트릭부터 살펴보겠습니다.

### <a name="requests"></a>요청
지정한 기간에 수신된 HTTP 요청의 수입니다. 이 메트릭을 다른 보고서의 결과와 비교하여 로드의 변화에 따른 앱의 동작 방식을 확인합니다.

HTTP 요청에는 페이지, 데이터 및 이미지에 대한 모든 GET 또는 POST 요청이 포함됩니다.

특정 URL에 대한 요청 수를 가져오려면 타일을 클릭합니다.

### <a name="average-response-time"></a>평균 응답 시간
웹 요청이 응용 프로그램에 도착하는 시점과 응답이 반환되는 시점 사이의 시간을 측정합니다.

점은 이동 평균을 보여 줍니다. 많은 요청이 있을 경우 그래프에서 크게 상승하거나 하락하지는 않지만 평균에서 벗어나는 일부 요청이 있을 수 있습니다.

요청 수의 비정상적인 증가를 확인합니다. 일반적으로는 요청 수가 증가하면 응답 시간은 길어집니다. 그러나 이처럼 요청 수와 응답 시간이 비례하지 않는 경우에는 앱이 사용하는 서비스의 용량 또는 CPU와 같은 리소스 제한에 도달해서 일 수 있습니다.

특정 URL에 대한 요청 시간을 가져오려면 타일을 클릭합니다.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>가장 느린 요청
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

성능 튜닝이 필요할 수 있는 요청을 표시합니다.

### <a name="failed-requests"></a>실패한 요청
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

catch되지 않은 예외를 throw한 요청의 수입니다.

타일을 클릭하면 특정 오류의 세부 정보를 확인할 수 있으며 개별 요청을 선택하면 해당 세부 정보를 볼 수 있습니다. 

오류의 대표 샘플만 개별적으로 조사할 수 있도록 보관됩니다.

### <a name="other-metrics"></a>기타 메트릭
표시할 수 있는 기타 메트릭을 보려면 그래프를 클릭하고 모든 메트릭의 선택을 취소하여 사용 가능한 전체 집합을 표시합니다. (i)를 클릭하면 각 메트릭의 정의가 표시됩니다.

![모든 메트릭의 선택을 취소하여 전체 집합 표시](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

메트릭을 선택하면 같은 차트에 표시할 수 없는 다른 메트릭은 사용하지 않도록 설정됩니다.

## <a name="set-alerts"></a>경고 설정
메트릭의 비정상적인 값에 대한 알림을 메일로 받으려면 경고를 추가합니다. 계정 관리자나 특정 메일 주소로 메일을 보내도록 선택할 수 있습니다.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

다른 속성에 앞서 리소스를 설정합니다. 성능 또는 사용 메트릭에 대한 경고를 설정하려면 webtest 리소스를 선택하지 마세요.

임계값을 입력하라는 단위에 주의하세요.

*경고 추가 단추가 보이지 않습니다.* -이것은 읽기 전용 액세스 권한이 있는 그룹 계정입니까? 계정 관리자에게 확인하세요.

## <a name="diagnosis"></a>문제 진단
아래에는 성능 문제를 찾고 진단하기 위한 몇 가지 팁이 나와 있습니다.

* 웹 사이트의 작동이 중단되거나 응답이 잘못되거나 속도가 느려지는 경우 경고를 받도록 [웹 테스트][availability]를 설정합니다. 
* 요청 수를 다른 메트릭과 비교하여 오류 또는 느린 응답이 부하와 관련되어 있는지 확인합니다.
* 코드에서 [검사 추적 문을 삽입 및 검색][diagnostic]하여 문제를 파악합니다.
* [라이브 메트릭 스트림][livestream]을 사용하여 작업에서 웹앱을 모니터링합니다.
* [스냅숏 디버거][snapshot]를 사용하여 .Net 응용 프로그램의 상태를 캡처합니다.

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>성능 조사 환경을 사용하여 성능 병목 현상 찾기 및 수정

성능 조사 환경을 사용하여 웹앱에서 성능이 느린 작업을 검토합니다. 느린 특정 작업을 신속하게 선택하고 [프로파일러](app-insights-profiler.md)를 사용하여 코드에 대한 작업 속도를 저하시키는 근본 원인을 파악할 수 있습니다. 선택한 작업이 표시된 새 기간 분포를 사용하면 해당 환경이 얼마나 고객에게 나쁜지를 빠르게 즉시 가늠할 수 있습니다. 느린 작업마다 영향을 받은 사용자 상호 작용의 상태를 확인할 수 있습니다. 다음 예제에서는 고객/세부 정보 가져오기 작업에 대한 환경을 자세히 살펴보겠습니다. 기간 분포에서 세 가지 스파이크가 있음을 알 수 있습니다. 맨 왼쪽 스파이크는 약 400ms이며 뛰어난 응답 환경을 나타냅니다. 중간 스파이크는 약 1.2s이며 보통의 환경을 나타냅니다. 3.6s에서 99번째 백분위수 환경을 나타내는 마지막 작은 스파이크는 고객 불만족으로 이어질 가능성이 높습니다. 그러한 환경은 동일한 작업에 대해 탁월한 환경보다 10배 더 느립니다. 

![고객/세부 정보 가져오기 세 가지 기간 스파이크](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

이 작업에 대한 사용자 환경을 더 잘 이해하기 위해 더 긴 시간 범위를 선택할 수 있습니다. 그러면 작업이 느린 특정 시간 범위에서 시간을 좁힐 수 있습니다. 다음 예에서는 기본 24시간 시간 범위에서 7일 시간 범위로 전환한 다음, 12일 화요일과 13일 수요일 사이의 9:47~12:47 시간 범위로 확대했습니다. 기간 분포와 샘플 및 프로파일러 추적의 수는 모두 오른쪽에서 업데이트되었습니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

느린 환경에서 범위를 좁히기 위해 95와 99 백분위수 사이로 떨어지는 기간으로 확대합니다. 이는 느린 사용자 상호 작용의 4%를 나타냅니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

이제 샘플 단추를 클릭하거나 대표 프로파일러 추적에서 프로파일러 추적 단추를 클릭하여 대표 샘플 중 하나를 살펴볼 수 있습니다. 다음 예에는 시간 범위 및 관심 있는 범위 기간에서 고객/세부 정보를 가져오기 위해 수집된 4개의 추적이 있습니다.

경우에 따라 문제가 코드에 있지 않고 오히려 코드에서 호출하는 종속성에 있을 수 있습니다. 성능 심사 보기에서 종속성 탭을 전환하여 그러한 느린 종속성을 조사할 수 있습니다. 기본적으로 성능 보기는 추세 평균이지만, 실제로 조사하려는 것은 95번째 백분위수(또는 완성도가 높은 서비스를 모니터링하는 경우 99번째 백분위수)입니다. 다음 예제에서는 PUT fabrikamaccount를 호출하는 느린 Azure BLOB 종속성에 초점을 맞춥니다. 좋은 환경은 약 40ms로 클러스터하지만, 동일한 종속성에 대한 느린 호출은 약 120ms로 클러스터하여 세 배 느립니다. 해당 작업 속도가 현저하게 느려지는 원인이 되므로 이러한 호출은 많이 만들지 않습니다. 작업 탭을 사용할 수 있는 것처럼 대표 샘플 및 프로파일러 추적을 자세히 살펴볼 수 있습니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

성능 조사 환경은 집중하기로 결정한 샘플 집합 측면과 관련된 통찰력을 보여 줍니다. 사용 가능한 모든 정보를 살펴보기에 가장 좋은 방법은 30일 시간 범위로 전환한 다음, 전체를 선택하여 지난 달 동안의 모든 작업에 걸쳐 정보를 확인하는 것입니다.

![고객/세부 정보 가져오기 세 가지 기간 스파이크(기간: 7일 범위)](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>다음 단계
[웹 테스트][availability] - 전 세계에서 웹 요청이 일정한 간격으로 응용 프로그램에 전송되도록 합니다.

[진단 추적 캡처 및 검색][diagnostic] - 추적 호출을 삽입하고 결과를 확인하여 문제를 파악합니다.

[사용 현황 추적][usage] - 사용자의 응용 프로그램 사용 방식을 파악합니다.

[문제 해결][qna] - 질문과 대답



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



