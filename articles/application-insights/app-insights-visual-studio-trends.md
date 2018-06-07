---
title: Visual Studio에서 추세 분석 | Microsoft Docs
description: Visual Studio의 Application Insights 원격 분석에서 추세를 분석, 시각화 및 탐색합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: 77ed0b5809a1da4629b80f65d1a27da2a2758236
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="analyzing-trends-in-visual-studio"></a>Visual Studio에서 추세 분석
Application Insights 추세 도구는 웹 응용 프로그램의 중요한 원격 분석 이벤트가 시간이 지남에 따라 어떻게 변했는지 시각화하여, 문제와 잘못된 부분을 신속하게 식별하는 데 도움을 줍니다. 더 자세한 진단 정보에 연결하면 추세를 통해 앱의 성능을 향상시키고 예외의 원인을 추적하며 사용자 지정 이벤트로부터 새로운 정보를 발견할 수 있습니다.

![예제 추세 창](./media/app-insights-visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Application Insights의 웹앱 구성

이 작업을 아직 수행하지 않은 경우 [Application Insights의 웹앱을 구성](app-insights-overview.md)합니다. 그러면 Application Insights 포털에 원격 분석을 보낼 수 있습니다. 추세 도구에서는 원격 분석 데이터를 읽을 수 있습니다.

Application Insights 추세는 Visual Studio 2015 업데이트 3 이상에서 사용할 수 있습니다.

## <a name="open-application-insights-trends"></a>Application Insights 추세 열기
Application Insights 추세 창을 열려면:

* Application Insights 도구 모음 단추에서 **원격 분석 추세 탐색**을 선택합니다. 또는
* 프로젝트 상황에 맞는 메뉴에서 **Application Insights > 원격 분석 추세 탐색**을 선택합니다. 또는
* Visual Studio 메뉴 모음에서 **보기 > 다른 창 > Application Insights 추세**를 선택합니다.

리소스를 선택하라는 메시지가 표시될 수 있습니다. **리소스 선택**을 클릭하고 Azure 구독을 사용하여 로그인한 다음, 원격 분석 추세를 분석하려는 목록에서 Application Insights 리소스를 선택합니다.

## <a name="choose-a-trend-analysis"></a>추세 분석 선택
![일반적인 유형의 추세 분석 메뉴](./media/app-insights-visual-studio-trends/app-insights-trends-1-750.png)

지난 24시간 동안의 데이터를 각각 분석한 5개의 공통 추세 분석 중에서 하나를 선택하여 다음을 시작합니다.

* **서버 요청을 통해 성능 문제 조사** - 서비스에 대한 요청으로, 응답 시간별로 그룹화됨
* **서버 요청에서 오류 분석** - 서비스에 대한 요청으로, HTTP 응답 코드별로 그룹화됨
* **응용 프로그램에서 예외 검사** - 서비스의 예외로, 예외 유형별로 그룹화됨
* **응용 프로그램의 종속성 성능 확인** - 서비스별로 호출된 서비스로, 서비스 응답 시간별로 그룹화됨
* **사용자 지정 이벤트 검사** - 서비스에 대해 설정한 사용자 지정 이벤트로, 이벤트 유형별로 그룹화됨

이들 미리 작성된 분석은 추세 창의 왼쪽 위 모서리에 있는 **일반적인 유형의 원격 분석 보기** 단추를 통해 나중에 사용할 수 있습니다.

## <a name="visualize-trends-in-your-application"></a>응용 프로그램에서 추세 시각화
Application Insights 추세는 앱의 원격 분석으로부터 시계열 시각화를 만듭니다. 각 시계열 시각화는 어떤 시간 범위 동안 한 원격 분석 유형을 표시하며, 해당 원격 분석의 한 가지 속성으로 그룹화됩니다. 예를 들어 지난 24시간 동안 출신 국가별로 그룹화하여 서버 요청을 볼 수 있습니다. 이 예에서 시각화의 각 거품은 1시간 동안 일부 국가/지역에 대한 서버 요청 수를 나타낸 것입니다.

창 맨 위에 있는 컨트롤을 사용하여 보려는 유형의 원격 분석 데이터를 조정합니다. 첫째, 다음과 같이 관심 있는 원격 분석 유형 선택을 선택합니다.

* **원격 분석 유형** - 서버 요청, 예외, 종속성 또는 사용자 지정 이벤트
* **시간 범위** - 지난 30분에서 지난 3일까지의 범위
* **그룹별** - 예외 형식, 문제 ID, 국가/지역 등

그런 다음 **분석 원격 분석** 을 클릭하여 쿼리를 실행합니다.

시각화의 거품 사이를 이동하려면:

* 한 거품을 클릭하면, 창의 아래쪽에 있는 필터를 업데이트하고 특정 기간 동안 발생한 이벤트만 요약합니다.
* 거품을 두 번 클릭하여 검색 도구를 이동하고 해당 기간 동안 발생한 개별 원격 분석 이벤트를 모두 확인합니다.
* 시각화 요소에서 선택 취소하려면 거품을 Ctrl 키를 누르고 클릭합니다.

> [!TIP]
> 추세 및 검색 도구를 함께 사용하면 수천 개의 원격 분석 이벤트 중에서 서비스 문제의 원인을 찾는 데 도움이 됩니다. 예를 들어, 어느 날 오후 고객이 앱의 응답 속도 저하를 인지한 경우 추세를 시작합니다. 지난 몇 시간 동안 서비스에 대한 요청을 분석하여, 응답 시간별로 그룹화합니다. 비정상적으로 큰 느린 요청 클러스터가 있는지 확인합니다. 그런 다음 해당 거품을 두 번 클릭하여 검색 도구로 이동하고 요청 이벤트에 대해 필터링합니다. 검색에서 요청 내용을 탐색할 수 있으며 문제를 해결하기 위해 관련 코드로 이동할 수 있습니다.
> 
> 

## <a name="filter"></a>Filter
창의 맨 아래에서 필터 컨트롤로 보다 구체적인 추세를 검색합니다. 필터를 적용하려면 해당 이름을 클릭합니다. 원격 분석의 특정 차원에 숨어 있을 수 있는 추세를 검색하기 위해 여러 필터 사이를 신속하게 전환할 수 있습니다. 예외 형식 등 한 차원에서 필터를 적용하는 경우, 흐리게 표시되더라도 다른 차원의 필터를 클릭할 수 있습니다. 필터를 적용하지 않으려면 다시 클릭합니다. 동일한 차원의 여러 필터를 선택하려면 Ctrl 키를 누르고 클릭합니다.

![추세 필터](./media/app-insights-visual-studio-trends/TrendsFiltering-750.png)

여러 필터를 적용하려면 어떻게 해야 할까요? 

1. 첫 번째 필터를 적용합니다. 
2. 첫 번째 필터 차원의 이름 옆에 있는 **선택한 필터를 적용하고 다시 쿼리** 단추를 클릭합니다. 이렇게 하면 첫 번째 필터와 일치하는 이벤트에 대해서만 원격 분석을 다시 쿼리합니다. 
3. 두 번째 필터를 적용합니다. 
4. 원격 분석의 특정 하위 집합에서 추세를 찾기 위해 프로세스를 반복합니다. 예를 들어, "GET Home/Index"라는 서버 요청 *및* 독일에서 발생한 서버 요청 *및* 500 응답 코드를 수신한 서버 요청입니다. 

이들 필터 중 하나를 적용하지 않으려면 해당 차원의 **선택한 필터를 제거하고 다시 쿼리** 단추를 클릭합니다.

![여러 필터](./media/app-insights-visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>잘못된 부분을 찾기
추세 도구는 같은 시계열에서 다른 거품에 비해 비정상적인 거품 이벤트를 강조 표시할 수 있습니다. 보기 유형 드롭다운에서 **시간 버킷의 수(변칙 강조 표시)** 또는 **시간 버킷의 백분율(변칙 강조 표시)** 을 선택합니다. 빨간색 거품이 잘못된 것입니다. 거품의 개수/비율이 지난 두 기간(지난 24시간을 보는 경우 48시간 등) 동안 발생한 개수/비율의 표준 편차를 2.1배 초과하는 경우 잘못된 것으로 간주됩니다.

![컬러 점은 잘못된 부분을 나타냅니다.](./media/app-insights-visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> 잘못된 부분을 강조 표시하면, 그렇지 않은 경우 비슷하게 보일 수 있는 작은 거품의 시계열에서 이상값을 찾는 데 특히 유용합니다.  
> 
> 

## <a name="next"></a>다음 단계
|  |  |
| --- | --- |
| **[Visual Studio Online에서 Application Insights로 작업](app-insights-visual-studio.md)**<br/>원격 분석을 검색하고, CodeLens에서 데이터를 확인하며, Application Insights를 구성합니다. Visual Studio 내에서 모두 수행할 수 있습니다. |![프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 및 검색을 선택합니다.](./media/app-insights-visual-studio-trends/34.png) |
| **[더 많은 데이터 추가](app-insights-asp-net-more.md)**<br/>사용량, 가용성, 종속성, 예외를 모니터링합니다. 로깅 프레임 워크의 추적을 통합합니다. 사용자 지정 원격 분석을 작성합니다. |![Visual studio](./media/app-insights-visual-studio-trends/64.png) |
| **[Application Insights 포털 사용](app-insights-dashboards.md)**<br/>대시보드, 강력한 분석 및 진단 도구, 경고, 응용 프로그램의 라이브 종속성 맵 및 원격 분석 내보내기입니다. |![Visual studio](./media/app-insights-visual-studio-trends/62.png) |

