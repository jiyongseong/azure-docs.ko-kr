---
title: "빠른 시작 - Azure Time Series Insights 탐색기 | Microsoft Docs"
description: "이 빠른 시작에서는 웹 브라우저에서 간편하게 Azure Time Series Insights 탐색기를 시작하여 다량의 IoT 데이터를 시각화하는 방법을 설명합니다. 데모 환경에서 주요 기능을 둘러봅니다."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: v-mamcge
manager: jhubbard
editor: MarkMcGeeAtAquent, jasonwhowell, kfile, MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/15/2017
ms.openlocfilehash: a2f88ab65a8f9948b83f491896d53b34f162747c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="quickstart-explore-azure-time-series-insights"></a>빠른 시작: Azure Time Series Insights 탐색하기
이 빠른 시작에서는 무료 데모 환경에서 Azure Time Series Insights 탐색기를 시작하는 방법을 설명합니다. 웹 브라우저를 사용하여 많은 양의 IoT 데이터를 시각화하는 방법을 알아보고, Time Series Insights 탐색기의 주요 기능을 둘러봅니다. 

Azure Time Series Insights는 완전히 관리되는 분석, 저장 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다.  시계열 데이터를 저장하거나 쿼리해야 하는 응용 프로그램을 개발할 때는 Time Series Insights REST API를 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>데모 환경에서 Time Series Insights 탐색기 살펴보기

1. 브라우저에서 [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo)로 이동합니다. 

2. 메시지가 표시되면 Azure 계정 자격 증명을 사용하여 Time Series Insights 탐색기에 로그인합니다. 
 
3. Time Series Insights 간단한 둘러보기 페이지가 표시됩니다. **다음**을 클릭하여 간단한 둘러보기를 시작합니다.

   ![다음을 클릭합니다.](media/quickstart/quickstart1.png)

4. **Time 선택 패널**이 표시됩니다. 이 패널에서 시각화할 시간 프레임을 선택합니다.

   ![시간 선택 패널](media/quickstart/quickstart2.png)

5. 마우스를 클릭하고 영역으로 끈 다음 **검색** 단추를 클릭합니다.
 
   ![시간 프레임 선택하기](media/quickstart/quickstart3.png) 

   지정한 시간 프레임에 대해 시각화된 차트가 표시됩니다. 꺾은선형 차트에서 필터링, 고정, 정렬, 스택과 같은 다양한 작업을 수행할 수 있습니다. 

   **시간 선택 패널**로 돌아가려면 다음과 같이 아래쪽 화살표를 클릭합니다.

   ![차트](media/quickstart/quickstart4.png)

6. **기간 패널**에서 **추가**를 클릭하여 새 검색 기간을 추가합니다.

   ![항목 추가](media/quickstart/quickstart5.png)

7. 이 차트에서는 특정 지역을 선택하고, 마우스 오른쪽 단추로 클릭한 다음 **이벤트 탐색**을 선택할 수 있습니다.
 
   ![이벤트 탐색](media/quickstart/quickstart6.png)

   탐색 중인 지역의 원시 데이터가 그리드로 표시됩니다.

   ![그리드 보기](media/quickstart/quickstart7.png)

8. 기간을 수정하여 차트의 값을 변경하고, 다른 기간을 추가하여 다양한 형식의 값 사이의 상관관계를 도출합니다.

   ![기간 추가](media/quickstart/quickstart8.png)

9. **필터 계열...** 상자에 필터 기간을 입력하여 임시 계열 필터링을 수행합니다. 여기서는 **Station5**를 입력하여 해당 측정소의 온도와 기압 사이의 상관관계를 확인합니다.
 
   ![필터 계열](media/quickstart/quickstart9.png)

빠른 시작을 완료했으면 샘플 데이터 집합을 이용하여 다양한 시각화를 수행해 봅니다. 

### <a name="next-steps"></a>다음 단계
이제 Time Series Insights 환경을 만들 준비가 완료되었습니다.
> [!div class="nextstepaction"]
> [Time Series Insights 환경 계획](time-series-insights-environment-planning.md)
