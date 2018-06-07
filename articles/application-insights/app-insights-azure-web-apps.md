---
title: Azure 웹앱 성능 모니터링 | Microsoft Docs
description: Azure 웹앱에 대한 응용 프로그램 성능 모니터링입니다. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: 9de6ca48db98d7691310199b90bf7414f07547fd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-azure-web-app-performance"></a>Azure 웹앱 성능 모니터링
[Azure Portal](https://portal.azure.com)에서 [Azure 웹앱](../app-service/app-service-web-overview.md)의 응용 프로그램 성능 모니터링을 설정할 수 있습니다. [Azure Application Insights](app-insights-overview.md)는 해당 작업에 대한 원격 분석을 저장하고 분석하는 Application Insights 서비스에 보내는 앱을 계측합니다. 여기서 메트릭 차트 및 검색 도구를 사용하여 문제를 진단하고 성능을 개선하며 사용량 평가할 수 있습니다.

## <a name="run-time-or-build-time"></a>실행 시간 또는 빌드 시간
다음과 같은 두 가지 방법 중 하나로 앱을 계측하여 모니터링을 구성할 수 있습니다.

* **런타임** - 웹앱이 이미 라이브 상태인 경우 성능 모니터링 확장을 선택할 수 있습니다. 앱을 다시 빌드하거나 설치할 필요는 없습니다. 응답 시간, 성공률, 예외, 종속성 등을 모니터링하는 패키지의 표준 집합을 얻게 됩니다. 
* **빌드 시간** - 개발 중인 앱에서 패키지를 설치할 수 있습니다. 이 옵션은 융통성이 뛰어납니다. 동일한 표준 패키지 외에도 원격 분석 데이터를 사용자 지정하거나 고유한 원격 분석을 보내는 코드를 작성할 수 있습니다. 앱 도메인의 의미 체계에 따라 특정 작업 또는 레코드 이벤트를 기록할 수 있습니다. 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights를 사용하여 시간 계측 실행
Azure에서 웹앱을 이미 실행 중인 경우 이미 일부 요청 및 오류 비율을 모니터링하고 있습니다. Application Insights를 추가하여 응답 시간, 종속성 호출 모니터링, 스마트 검색, 강력한 Log Analytics 쿼리 언어 등 더 많은 것을 얻을 수 있습니다. 

1. 웹앱의 Azure 제어판에서 **Application Insights를 선택**합니다.
   
    ![모니터링 아래에서 Application Insights 선택](./media/app-insights-azure-web-apps/05-extend.png)
   
   * 다른 경로로 이 앱에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다.
2. Application Insights를 설치한 후 **웹앱을 계측**합니다. 
   
    ![웹앱 계측](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   페이지 보기 및 사용자 원격 분석을 위해 **클라이언트 쪽 모니터링을 사용하도록 설정**합니다.

   * 설정 > 응용 프로그램 설정 선택
   * 앱 설정 아래에서 새로운 키 값 쌍을 추가합니다. 
   
    키: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    값: `true`
   * 설정을 **저장**하고 앱을 **다시 시작**합니다.
3. **앱을 모니터링합니다**.  [데이터를 탐색합니다](#explore-the-data).

나중에 원하는 경우 Application Insights로 앱을 빌드할 수 있습니다.

*Application Insights를 제거하거나 다른 리소스에 보내기로 전환하려면 어떻게 해야 합니까?*

* Azure에서 웹앱 제어 블레이드를 열고 개발 도구 아래에서 **확장**을 엽니다. Application Insights 확장을 삭제합니다. 그런 다음 모니터링 아래에서 Application Insights를 선택하고 원하는 리소스를 만들거나 선택합니다.

## <a name="build-the-app-with-application-insights"></a>Application Insights로 앱 빌드
Application Insights는 앱에 SDK를 설치하여 더 자세한 원격 분석을 제공할 수 있습니다. 특히 추적 로그를 수집하고 [사용자 지정 원격 분석을 작성](app-insights-api-custom-events-metrics.md)하고 보다 자세한 예외 보고서를 가져올 수 있습니다.

1. **Visual Studio**(2013 업데이트 2 이상)에서 프로젝트를 위한 Application Insights를 구성합니다.

    웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > Application Insights** 또는 **Application Insights 구성**을 선택합니다.
   
    ![웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가 또는 구성 선택](./media/app-insights-azure-web-apps/03-add.png)
   
    로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.
   
    작업에는 두 가지 효과가 있습니다.
   
   1. 원격 분석을 저장하고, 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 만듭니다.
   2. Application Insights NuGet 패키지를 사용자 코드에 추가하고(없는 경우) 원격 분석을 Azure 리소스로 전송하도록 구성합니다.
2. 개발 컴퓨터(F5)에서 앱을 실행하여 **원격 분석을 테스트**합니다.
3. 일반적인 방법으로 Azure에 **앱을 게시**합니다. 

*다른 Application Insights 리소스에 보내기로 전환하려면 어떻게 해야 합니까?*

* Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **Application Insights 구성**을 선택한 다음, 원하는 리소스를 선택합니다. 새 리소스를 만드는 옵션이 생깁니다. 다시 빌드하고 다시 배포합니다.

## <a name="explore-the-data"></a>데이터 탐색
1. 웹앱 제어판의 Application Insights 블레이드에 두 번째 또는 두 개의 발생 내에서 요청 및 실패를 보여 주는 라이브 메트릭이 표시됩니다. 모든 문제를 즉시 확인할 수 있으므로 앱을 다시 게시하는 경우 매우 유용한 표시입니다.
2. 전체 Application Insights 리소스를 클릭합니다.

    ![클릭](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Azure 리소스 탐색에서 직접 이동할 수도 있습니다.

1. 차트를 클릭하여 자세한 내용을 봅니다.
   
    ![Application Insights 개요 블레이드에서 차트를 클릭합니다.](./media/app-insights-azure-web-apps/07-dependency.png)
   
    [메트릭 블레이드를 사용자 지정](app-insights-metrics-explorer.md)할 수 있습니다.
2. 개별 이벤트와 해당 속성을 보려면 또 클릭합니다.
   
    ![해당 유형에 대해 필터링된 검색을 열려면 이벤트 유형을 클릭합니다.](./media/app-insights-azure-web-apps/08-requests.png)
   
    "..." 링크를 확인하여 모든 속성을 엽니다.
   
    [검색을 사용자 지정](app-insights-diagnostic-search.md)할 수 있습니다.

원격 분석을 통해 보다 강력하게 검색하려면 [Log Analytics 쿼리 언어](app-insights-analytics-tour.md)를 사용합니다.

## <a name="more-telemetry"></a>추가 원격 분석

* [웹 페이지 로드 데이터](app-insights-javascript.md)
* [사용자 지정 원격 분석](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](app-insights-azure-diagnostics.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](app-insights-javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](app-insights-monitor-web-app-availability.md) 합니다.

