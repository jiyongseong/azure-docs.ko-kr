---
title: Azure Application Insights에서 사용자 흐름을 사용하여 사용자 탐색 패턴 분석 | Microsoft docs
description: 페이지와 웹앱의 기능 간에 사용자가 탐색하는 방법을 분석합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 5913039d3ab288c2fc8366073b340538989512ee
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Application Insights에서 사용자 흐름을 사용하여 사용자 탐색 패턴 분석

![Application Insights 사용자 흐름 도구](./media/app-insights-usage-flows/00001-flows.png)

사용자 흐름 도구는 페이지와 웹앱의 기능 간에 사용자가 탐색하는 방법을 시각화합니다. 다음과 같은 질문에 대답할 수 있습니다.

* 사용자가 사이트 페이지에서 벗어나려면 어떻게 할까요?
* 사용자가 사이트 페이지에서 어디를 클릭하나요?
* 사용자가 사이트에서 가장 많이 이탈하는 위치는 어디인가요?
* 사용자가 동일한 작업을 반복하는 위치가 있나요?

사용자 흐름 도구는 지정한 초기 페이지 보기, 사용자 지정 이벤트 또는 예외에서 시작됩니다. 초기 이벤트가 정해지면 사용자 세션 전후에 발생한 이벤트가 사용자 흐름에 표시됩니다. 다양한 두께의 선은 사용자가 따라간 각 경로의 횟수를 보여줍니다. 특별 **세션 시작** 노드에는 후속 노드가 세션을 시작한 위치가 표시됩니다. **세션 종료됨** 노드에는 이전 노드 다음에 페이지 보기나 사용자 지정 이벤트를 보내지 않은 사용자 수가 표시되어 사용자가 사이트를 떠난 지점이 강조 표시됩니다.

> [!NOTE]
> Application Insights 리소스는 사용자 흐름 도구를 사용하기 위한 페이지 보기 또는 사용자 지정 이벤트를 포함해야 합니다. [Application Insights JavaScript SDK를 사용하여 자동으로 페이지 뷰를 수집하도록 앱을 설정하는 방법에 대해 알아봅니다](app-insights-javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>초기 이벤트를 선택하여 시작

![사용자 흐름에 대한 초기 이벤트 선택](./media/app-insights-usage-flows/00002-flows-initial-event.png)

사용자 흐름 도구로 질문에 답변을 시작하려면 시각화의 시작점으로 사용할 초기 페이지 보기, 사용자 지정 이벤트 또는 예외를 선택합니다.

1. **이후에 사용자가 수행할 작업은 무엇인가요...?** 제목의 링크를 선택하거나 **편집** 단추를 클릭합니다.
2. **초기 이벤트** 드롭다운에서 페이지 보기, 사용자 지정 이벤트 또는 예외를 선택합니다.
3. **그래프 만들기**를 클릭합니다.

시각화의 "1단계" 열에서는 초기 이벤트 직후 사용자가 가장 자주 수행한 작업을 빈도가 높은 순서부터 낮은 순서로 정렬하여 보여줍니다. "2단계" 및 후속 열에서는 이후 사용자가 수행한 작업을 표시하고 사이트를 통해 사용자가 탐색한 방법으로 그림을 만듭니다.

기본적으로 사용자 흐름 도구는 최근 24시간 동안 사이트에서 페이지 보기 및 사용자 지정 이벤트를 무작위로 샘플링합니다. 시간 범위를 늘리고 편집 메뉴에서 무작위 샘플링에 대한 성능 및 정확도의 균형을 변경할 수 있습니다.

페이지 보기, 사용자 지정 이벤트 및 예외 중 일부가 나와 연관이 없는 경우 숨기려면 노드에서 **X**를 클릭합니다. 숨기려는 노드를 선택한 다음, 시각화 아래에서 **그래프 만들기** 단추를 클릭합니다. 숨겨진 노드를 모두 표시하려면 **편집** 단추를 클릭한 다음, **제외된 이벤트** 섹션을 확인합니다.

페이지 보기 또는 사용자 지정 이벤트가 누락된 경우 시각화에 다음이 표시됩니다.

* **편집** 메뉴에서 **제외된 이벤트** 섹션을 확인합니다.
* **기타** 노드의 더하기 단추를 사용하여 덜 빈번한 이벤트를 시각화에 포함시킵니다.
* 원하는 페이지 보기 또는 사용자 지정 이벤트를 사용자가 자주 전달하는 경우 **편집** 메뉴에서 시각화의 시간 범위를 늘려 주세요.
* 원하는 페이지 보기, 사용자 지정 이벤트 또는 예외가 사이트의 소스 코드에 있는 Application Insights SDK를 통해 수집되도록 설정되어야 합니다. [사용자 지정 이벤트 수집에 대해 자세히 알아봅니다.](app-insights-api-custom-events-metrics.md)

시각화에서 더 많은 단계를 보려면 시각화 위에서 **이전 단계** 및 **다음 단계** 드롭다운을 사용합니다.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>페이지 또는 기능을 방문한 후에 사용자가 어디로 이동하고 어떤 항목을 클릭하나요?

![사용자 흐름을 사용하여 사용자가 클릭하는 위치 이해](./media/app-insights-usage-flows/00003-flows-one-step.png)

초기 이벤트가 페이지 보기인 경우 시각화의 첫 번째 열("1단계")은 페이지를 방문한 직후 사용자가 수행하는 작업을 신속하게 이해하는 방법입니다. 사용자 흐름 시각화 옆에 있는 창에서 사이트를 열어 보세요. 사용자가 "1단계" 열에 있는 이벤트 목록의 페이지와 상호 작용하는 방법을 예상과 비교합니다. 종종 팀에서 불필요해 보이는 페이지의 UI 요소가 페이지에서 가장 많이 사용될 수 있습니다. 사이트의 설계 향상을 위한 시작점으로 적당할 수 있습니다.

초기 이벤트가 사용자 지정 이벤트인 경우 첫 번째 열은 해당 작업을 수행한 직후에 사용자가 수행한 작업을 보여줍니다. 페이지 보기에서 사용자의 관찰된 동작이 팀의 목표 및 예상과 일치하는지를 고려합니다. 예를 들어, 선택한 초기 이벤트가 "장바구니에 추가된 항목"인 경우 곧이어 "체크아웃으로 이동" 및 "완료된 구매"가 시각화에 표시되는지를 확인합니다. 사용자 동작이 예상과 다른 경우 시각화를 사용하여 사용자가 사이트의 현재 디자인에 의해 "포착"되는 방식을 이해합니다.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>사용자가 사이트에서 가장 많이 이탈하는 위치는 어디인가요?

특히 흐름의 초기에 시각화의 열에 높이 나타날 수 있는 **세션 종료됨** 노드에 주의합니다. 즉, 많은 사용자가 페이지와 UI 상호 작용의 이전 경로를 따른 후에 사이트에서 변동했을 수 있습니다. 예를 들어, 전자 상거래 사이트에서 구매를 완료한 후와 같은 경우 종종 변동이 예상됩니다. 하지만 변동은 일반적으로 설계 상의 문제, 성능 저하 또는 개선될 수 있는 사이트 관련 기타 문제를 의미합니다.

**세션 종료됨** 노드가 이 Application Insights 리소스에 의해 수집된 원격 분석에만 기반한다는 점을 기억합니다. Application Insights가 특정 사용자 상호 작용에 대한 원격 분석을 수신하지 못하면 사용자는 사용자 흐름 도구가 세션을 종료한 후에 해당 방법으로 사이트와 상호 작용합니다.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>사용자가 동일한 작업을 반복하는 위치가 있나요?

시각화의 후속 단계를 통해 많은 사용자가 반복하는 페이지 보기 또는 사용자 지정 이벤트를 찾아봅니다. 즉, 일반적으로 사용자가 사이트에서 반복 작업을 수행하고 있습니다. 반복 작업을 발견한 경우 반복 횟수를 줄이기 위해 사이트의 디자인을 변경하거나 새 기능을 추가하는 방법을 고려하세요. 예를 들어 테이블 요소의 각 행에서 반복되는 동작을 수행하는 사용자를 발견한 경우 대량 편집 기능을 추가합니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>초기 이벤트는 이벤트가 처음으로 세션에 나타나는 경우인가요, 아니면 세션에 나타날 때마다 인가요?

시각화의 초기 이벤트는 처음으로 사용자가 세션 중 해당 페이지 보기 또는 사용자 지정 이벤트를 전송한 경우만을 나타냅니다. 사용자가 세션에서 여러 번 초기 이벤트를 전송할 수 있는 경우 "1단계" 열에서는 모든 인스턴스가 아닌 초기 이벤트의 *첫 번째* 인스턴스 이후에 사용자 동작 방식만을 표시합니다.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>내 시각화에 있는 일부 노드가 수준이 너무 높습니다. 예를 들어, 노드에 "단추 클릭"만 표시됩니다. 어떻게 하면 더 세분화된 노드로 나눌 수 있나요?

**편집** 메뉴의 **분할 기준** 옵션을 사용합니다.

1. **이벤트** 메뉴에서 세분화할 이벤트를 선택합니다.
2. **차원** 메뉴에서 차원을 선택합니다. 예를 들어 "단추 클릭"이라는 이벤트가 있는 경우 "단추 이름"이라는 사용자 지정 속성을 사용해 봅니다.

## <a name="next-steps"></a>다음 단계

* [사용 현황 개요](app-insights-usage-overview.md)
* [사용자, 세션 및 이벤트](app-insights-usage-segmentation.md)
* [보존](app-insights-usage-retention.md)
* [앱에 사용자 지정 이벤트 추가](app-insights-api-custom-events-metrics.md)