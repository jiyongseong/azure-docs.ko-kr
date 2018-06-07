---
title: "Azure Application Insights에서 웹앱 성능 변화의 스마트 진단 | Microsoft Docs"
description: "웹앱의 성능 원격 분석에서 스파이크 또는 단계의 자동 진단입니다."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: e0c8d712f03da0c5e47ea422b051c0b8734c6b21
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>앱 원격 분석에서 급격한 변화 진단

*이 기능은 미리 보기 상태입니다.*

단 한 번의 클릭으로 웹앱의 성능 또는 사용 현황의 급격한 변화를 진단해 보세요. 스마트 진단 기능은 [Application Insights](app-insights-overview.md)의 [분석](app-insights-analytics.md)에서 시간 차트를 만들 때마다 사용할 수 있습니다. 급증이나 급강하 같이 결과의 추세에 비정상적인 변화가 있는 경우 스마트 진단은 변화를 설명할 수 있는 차원 및 관련 값의 패턴을 식별합니다. 이 기능을 사용하면 문제를 신속하게 진단할 수 있습니다. 

다음 예제에서 스마트 진단은 변화와 관련된 속성 값의 패턴을 식별하고 해당 패턴이 있는 경우와 없는 경우 결과의 차이를 강조 표시합니다.

![예제 분석 진단 결과](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>데이터 변경 진단

1.  분석에서 쿼리를 실행하고 시간 차트로 렌더링합니다. 
2.  강조 표시된 최대 사용 지점이 있는 경우 클릭합니다.
 
    ![최대 사용 지점](./media/app-insights-analytics-diagnostics/peak.png)

    진단에서 패턴을 검색하려면 몇 초 정도 걸립니다.

3. 진단 결과 탭에는 데이터 불연속성을 설명하는 패턴이 표시됩니다.

    ![result](./media/app-insights-analytics-diagnostics/result.png)
 
    텍스트에는 변화와 연관이 있을 것 같은 차원 값이 표시됩니다. 이 예제에서는 특정 요청 및 특정 브라우저 버전과 관련이 있습니다.

    차트의 두 가지 구성 요소인 true 및 false 필터로 확인합니다. false 구성 요소는 변경되지 않은 추세를 보여 줍니다. 즉, 진단에서 식별한 문제가 있는 차원 조합을 제외하면 원격 분석 결과에 변화가 없습니다. 반면, 해당 조합 내의 결과는 강조 표시된 조사 영역 내의 큰 변화를 보여 줍니다. 진단에서 변화를 설명하는 속성 조합을 발견했음을 나타냅니다.

4.  패턴이 복잡할 경우 차원을 표시하려면 **모두 표시** 위로 마우스를 이동해야 합니다.

    ![모두 표시](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  진단에서 알림을 제공한 중요한 패턴을 찾지 못한 경우 '결과 없음' 페이지가 표시됩니다. 이 시점에서 쿼리를 변경할 수 있습니다. 예를 들어 분석 쿼리에서 시간 범위 및 범주화를 좁혀 추가 분석을 수행하고 더 나은 결과를 얻을 수 있습니다.

특정 브라우저에서 웹 사이트의 특정 페이지에 문제가 있다는 사실을 알았으므로 이제 문제 페이지로 바로 이동하여 최근 변경 내용을 조사할 수 있습니다.

## <a name="try-the-demo"></a>데모 사용해 보기

샘플 데이터에 대한 [데모를 보려면 여기를 클릭](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H)하세요.

## <a name="how-it-works"></a>작동 방법

스마트 진단은 [DiffPatterns](app-insights-analytics-reference.md) 작업을 기반으로 한 자율적인 고급 기계 학습 알고리즘을 사용합니다. 이 진단에서는 데이터 변경을 설명할 수 있는 후보 패턴을 찾습니다. 메트릭에 대한 각 후보의 영향을 분석하고 변경과 가장 관련이 있는 패턴을 보여 줍니다.

## <a name="no-diagnostic-points"></a>진단 지점은 없나요?

스마트 진단은 다음 조건이 충족되는 경우에만 작동합니다.

 * 스마트 진단 설정이 켜져 있습니다. 분석에서 설정 아이콘 아래에서 확인합니다.
 * [분석] 설정의 [스마트 진단] 옵션이 선택되어 있습니다. 
 * 시간 축: 차트의 X축은 `datetime` 형식이어야 합니다.
 * 꺾은선형 또는 영역 차트: 진단은 이러한 종류의 차트에서만 작동합니다. 쿼리의 끝에서 `| render timechart` 또는 `| render areachart`를 사용하거나 드롭다운 선택기에서 꺾은선형 또는 영역 차트를 선택합니다.
 * 불연속성: 데이터에 중요한 불연속성이 있어야 합니다.
 * 분석할 지점이 충분합니다.
 * 쿼리에 둘 이상의 요약 절이 없습니다.
 * 요약 절 앞에 이름 정의를 포함하는 프로젝트 절이 없습니다.

 
 ## <a name="related-articles"></a>관련된 문서

 * [Analytics 자습서](app-insights-analytics-tour.md)
 * [스마트 감지](app-insights-proactive-diagnostics.md)는 성능 문제에 대해 자동으로 경고합니다.