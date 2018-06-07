---
title: Azure Application Insights를 사용한 웹 응용 프로그램의 사용자 재방문 주기 분석 | Microsoft Docs
description: 앱으로 돌아온 사용자는 몇 명이나 되나요?
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: bafe9579cc783dbc5c851e626e3b9bea0c559520
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Application Insights를 사용한 웹 응용 프로그램의 사용자 재방문 주기 분석

[Azure Application Insights](app-insights-overview.md)의 재방문 주기 기능은 앱으로 돌아온 사용자 수와 특정 작업을 수행하거나 목표를 달성하는 빈도를 분석하는 데 도움을 줍니다. 예를 들어 게임 사이트를 실행하는 경우 게임에서 진 후에 사이트로 돌아오는 사용자 수와 이긴 후에 돌아온 사용자 수를 비교할 수 있습니다. 이러한 지식이 있으면 사용자 환경 및 비즈니스 전략을 둘 다 향상시킬 수 있습니다.

## <a name="get-started"></a>시작하기

Application Insights 포털의 재방문 주기 도구에서 아직 데이터가 표시되지 않으면 [사용 도구 시작 방법을 알아봅니다](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>재방문 주기 도구

![재방문 주기 도구](./media/app-insights-usage-retention/retention.png)

1. 도구 모음을 사용하면 사용자가 새 재방문 주기 보고서를 만들고, 기존 재방문 주기 보고서를 열고, 현재 재방문 주기 보고서 저장하거나 저장된 보고서로 만들어진 변경 내용을 되돌리고, 보고서에서 데이터 새로 고치고, 이메일 또는 직접 링크를 통해 보고서를 공유하고 설명서 페이지에 액세스할 수 있습니다. 
2. 기본적으로 재방문 주기는 기간에 따라 작업을 한 후 다시 방문하고 다른 작업을 한 모든 사용자를 보여 줍니다. 다른 이벤트의 조합을 선택하여 특정 사용자 작업에 대한 범위를 좁힐 수 있습니다.
3. 속성에 대해 하나 이상의 필터를 추가합니다. 예를 들어 특정 국가 또는 하위 지역에 있는 사용자에 집중할 수 있습니다. 필터를 설정한 후에 **업데이트**를 클릭합니다. 
4. 전체 재방문 주기 차트는 선택한 기간에 걸친 사용자 재방문 주기의 요약을 보여 줍니다. 
5. 표는 #2의 쿼리 작성기에 따라 재방문한 사용자 수를 보여 줍니다. 각 행은 표시된 기간에 이벤트를 실행한 사용자의 코호트를 표시합니다. 행의 각 셀은 이후 기간에 1번 이상 돌아온 해당 코호트 수를 표시합니다. 일부 사용자는 둘 이상의 기간에 돌아올 수 있습니다. 
6. insights 카드는 상위 5개 시작 이벤트 및 상위 5개 반환되는 이벤트를 표시하여 사용자가 자신의 재방문 주기 보고서를 더 잘 이해할 수 있도록 합니다. 

![재방문 주기 마우스 가리키기](./media/app-insights-usage-retention/hover.png)

사용자가 셀의 의미를 설명하는 분석 단추 및 도구 팁에 액세스하려면 재방문 주기 도구에서 셀을 마우스로 가리키면 됩니다. 분석 단추를 통해 셀에서 사용자를 생성하는 미리 입력된 쿼리를 사용하여 분석 도구로 이동합니다. 

## <a name="use-business-events-to-track-retention"></a>비즈니스 이벤트를 사용하여 재방문 주기 추적

유용한 재방문 주기 분석 기능을 최대한 활용하려면 유의한 비즈니스 활동을 나타내는 이벤트를 측정합니다. 

예를 들어 많은 사용자는 앱에서 페이지를 열기만 하고 표시되는 게임을 재생하지 않을 수 있습니다. 따라서 페이지 보기만 추적하면 이전에 게임을 즐긴 후에 돌아와서 게임을 재생한 사용자 수를 정확히 추정할 수 없습니다. 돌아온 플레이어를 명확히 분선하기 위해 앱은 사용자가 실제로 게임을 할 때 사용자 지정 이벤트를 전송해야 합니다.  

주요 비즈니스 작업을 나타내는 사용자 지정 이벤트를 코딩하고 재방문 주기 분석에 사용하는 것이 좋습니다. 게임 결과를 캡처하려면 Application Insights에 사용자 지정 이벤트를 전송하는 코드 줄을 작성해야 합니다. 웹 페이지 코드 또는 Node.JS에서 작성할 경우 다음과 같습니다.

```JavaScript
    appinsights.trackEvent("won game");
```

또는 ASP.NET 서버 코드:

```csharp
   telemetry.TrackEvent("won game");
```

[사용자 지정 이벤트 작성에 대해 자세히 알아봅니다](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>다음 단계
- 사용 현황 환경을 활성화하려면 [사용자 지정 이벤트](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 또는 [페이지 보기](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views) 보내기를 시작합니다.
- 사용자 지정 이벤트 또는 페이지 보기를 이미 보낸 경우 사용자가 서비스를 사용하는 방법에 대해 알아보려면 사용 현황 도구를 살펴봅니다.
    - [사용자, 세션, 이벤트](app-insights-usage-segmentation.md)
    - [깔때기](usage-funnels.md)
    - [사용자 흐름](app-insights-usage-flows.md)
    - [통합 문서](app-insights-usage-workbooks.md)
    - [사용자 컨텍스트 추가](app-insights-usage-send-user-context.md)


