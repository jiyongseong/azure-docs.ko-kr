---
title: "Azure Time Series Insights 탐색기로 데이터 탐색 | Microsoft Docs"
description: "이 문서에서는 웹 브라우저에서 Azure Time Series Insights 탐색기를 사용하여 빅 데이터의 글로벌 보기를 한눈에 확인하고 IoT 환경을 검증하는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/30/2017
ms.openlocfilehash: d09292cce1414a1b89e4b75df27d0a689738b4d6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기
이 문서에서는 Time Series Insights 탐색기 웹앱의 다양한 기능과 옵션을 살펴봅니다. 웹 브라우저에서 Time Series Insights 탐색기를 사용하여 데이터를 시각화할 수 있습니다.
 
Azure Time Series Insights는 완전히 관리되는 분석, 저장 및 시각화 서비스이며 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 숨겨진 동향을 찾고, 문제를 찾아내고, 거의 실시간으로 근본 원인 분석을 수행할 수 있도록 지원하는 기능을 통해, 큰 비용이 수반되는 중요 업무용 장치의 가동 중지 시간을 방지할 수 있습니다. Time Series Insights 탐색기는 현재 공개 미리 보기로 제공됩니다.

## <a name="prerequisites"></a>필수 조건

Time Series Insights 탐색기를 사용하려면 다음을 준비해야 합니다.
- Time Series Insights 환경 만들기
- 환경에서 계정에 대한 액세스 제공
- 데이터 수집 및 저장을 위한 이벤트 소스 추가

## <a name="explore-and-query-data"></a>데이터 탐색 및 쿼리하기
이벤트 소스를 Time Series Insights 환경에 연결하면 몇 분 안에 시계열 데이터를 탐색하고 쿼리할 수 있습니다.

1. 시작하려면 웹 브라우저에서 [Time Series Insights 탐색기](https://insights.timeseries.azure.com/)를 열고 창 왼쪽에서 환경을 선택합니다. 액세스 가능한 모든 환경이 알파벳 순으로 표시됩니다.

2. 환경을 선택한 다음에는 상단의 **FROM** 및 **TO** 구성을 사용하거나 마우스로 클릭하여 원하는 시간 범위로 끌어서 놓습니다.  오른쪽 상단에 있는 돋보기를 클릭하거나 선택한 시간 범위를 마우스 오른쪽 단추로 클릭하고 **검색**을 선택합니다.  

3. **자동 켜기** 단추를 선택하면 1분에 한 번씩 자동으로 사용 가능 시간이 업데이트됩니다.  '자동 켜기' 단추는 기본 시각화의 콘텐츠가 아닌 가용성 차트에만 적용됩니다.

4. Azure 클라우드 아이콘을 클릭하면 Azure Portal 환경으로 이동하게 됩니다.

   ![Time Series Insights 환경](media/time-series-insights-explorer/explorer1.png)

5. 선택한 시간 범위에 포함된 모든 이벤트의 개수가 표시된 차트를 보게 됩니다.  여기서 몇 가지 옵션을 사용할 수 있습니다.

    **Terms Editor Panel**(기간 편집기 패널): 기간 패널에서는 환경을 쿼리할 수 있습니다.  화면 왼쪽에 표시되며, 다음과 같은 기능이 제공됩니다. 
      - **측정값**:  이 드롭다운에는 숫자 열(double) 열이 표시됩니다.
      - **분할 기준**: 이 드롭다운에는 범주(문자열) 열이 표시됩니다.
      - 측정값 옆에 있는 제어판에서 단계 보간을 사용하도록 설정하고, 최대값과 최소값을 표시하고, Y축을 조정할 수 있습니다.  데이터를 개수, 평균, 데이터의 합계 중 무엇으로 표시할지 조정할 수도 있습니다.
      - 하나의 X축에서 최대 5개의 기간을 더할 수 있습니다.  **copy-down** 단추를 사용하여 기간을 추가하거나 **추가** 단추를 클릭하여 새 기간을 추가합니다.
     
        ![기간 편집기 패널](media/time-series-insights-explorer/explorer2.png)

      - **조건자**: 조건자 옵션을 사용하면 아래와 같은 연산자를 사용하여 이벤트를 빠르게 필터링할 수 있습니다. 선택/클릭하여 검색을 수행하면 검색을 바탕으로 조건자가 자동으로 업데이트됩니다.      지원되는 연산자는 다음과 같습니다.

         |작업  |지원되는 형식  |메모  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, TimeSpan       |         |
         |=, !=, <>     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  모든 연산자는 같은 형식이거나 NULL 상수여야 합니다.        |
         |HAS     | 문자열        |  우측 항에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열과 NULL은 허용되지 않습니다.       |

      - **쿼리 예**
      
         ![쿼리 예](media/time-series-insights-explorer/explorer9.png)

6. **간격 크기** 슬라이더 도구를 사용하면 동일한 시간 범위에 대해 간격을 확대하거나 축소할 수 있습니다.  이렇게 하면 부드러운 추세 변화를 보여주는 긴 시간과 밀리초 단위의 짧은 시간 사이를 정밀하게 전환하며 데이터를 세밀하고 촘촘하게 살펴볼 수 있습니다. 슬라이더의 기본 시작점은 선택한 데이터를 최적으로 볼 수 있는 보기로 해상도, 쿼리 속도, 세분성이 균형을 이루도록 설정됩니다.

7. 직관적인 UX로 제공되는 **Time brush**(시간 브러시) 도구를 사용하면 서로 다른 시간 범위 사이를 간편하게 이동하며 탐색할 수 있습니다.

8. **저장** 명령을 사용하면 현재 쿼리를 저장하여 환경의 다른 사용자들과 공유할 수 있습니다. **열기**를 사용하면 내가 저장한 쿼리와 환경의 다른 사용자들이 공유한 쿼리 중 나에게 액세스 권한이 부여된 공유된 쿼리를 볼 수 있습니다. 

   ![쿼리](media/time-series-insights-explorer/explorer3.png)

9. **원근감 뷰** 도구를 사용하면 최대 4개의 서로 다른 쿼리를 동시에 볼 수 있습니다. 원근감 뷰 단추는 차트의 오른쪽 상단에 있습니다.  

   ![원근감 뷰](media/time-series-insights-explorer/explorer4.png)

10. **차트**를 사용하면 데이터를 시각적으로 탐색할 수 있습니다. 차트 도구에는 다음과 같은 기능이 있습니다.

   - 선택/클릭: 단일 데이터 계열의 특정 시간 범위를 선택합니다.  
   - 선택한 시간 범위 안에서 이벤트를 확대하고 탐색할 수 있습니다.  
   - 하나의 데이터 계열에서 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
   - 표시된 데이터 계열은 모두 차트 왼쪽에 있는 필터 영역에서 볼 수 있습니다. 여기에서 값이나 이름을 기준으로 순서를 바꾸거나 모든 데이터 계열을 또는 고정된/고정 해제된 계열만 볼 수 있습니다.  하나의 데이터 계열을 선택하여 다른 열을 기준으로 계열을 분할하거나, 계열을 새 기간으로 추가하거나, 선택한 계열만 표시하거나, 선택한 계열을 제외하거나, 계열에 대해 ping을 보내거나 선택한 계열의 이벤트를 탐색할 수 있습니다.
   - 여러 기간을 동시에 볼 때 차트의 오른쪽 상단에 있는 단추를 사용하여 데이터 계열을 스택 또는 스택 해제하고, 데이터 계열에 대한 추가 데이터를 보고, 모든 기간에 대해 동일한 Y축을 사용할 수 있습니다.
 
   ![차트 도구](media/time-series-insights-explorer/explorer5.png) 

11. **열 지도**를 사용하면 주어진 쿼리에서 고유한 특성을 갖는 데이터 계열과 이상점이 있는 데이터 계열을 빠르게 파악할 수 있습니다. 하나의 검색 기간만 열 지도로 시각화할 수 있습니다.    

   ![열 지도](media/time-series-insights-explorer/explorer6.png)

12. **이벤트**: 선택하거나 마우스 오른쪽 단추를 클릭할 때 이벤트 탐색을 선택하면 이벤트 패널이 표시됩니다.  여기에서 원시 이벤트를 보고 이벤트를 JSON 또는 CSV 파일로 내보낼 수 있습니다. Time Series Insights에는 원시 데이터가 모두 저장됩니다.

   ![이벤트](media/time-series-insights-explorer/explorer7.png)

13. 이벤트 탐색을 완료했으면 **통계** 탭을 클릭하여 패턴과 열 통계를 확인합니다.  

   - **패턴**: 선택한 데이터 영역에서 통계적으로 가장 유의미한 패턴을 보여줍니다. 이 기능은 사용자가 수천 개의 이벤트를 보고 어떤 패턴에 시간과 에너지를 투입할지 파악해야 하는 수고를 덜어줍니다. 통계적으로 유의미한 패턴으로 곧바로 이동하여 계속해서 분석을 수행할 수도 있습니다. 이 기능은 과거 데이터에 대한 사후 평가 분석을 실시할 때도 유용합니다. 

   - **열 통계**: 열 통계는 선택한 시간 범위를 기준으로 선택한 데이터 계열의 각 열 데이터를 차트와 테이블로 표시합니다.  
 
      ![통계](media/time-series-insights-explorer/explorer8.png) 

지금까지 Time Series Insights 탐색기 웹앱의 다양한 기능과 옵션을 살펴봤습니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
>[Time Series Insights 환경에서 문제 진단 및 해결](time-series-insights-diagnose-and-solve-problems.md)
