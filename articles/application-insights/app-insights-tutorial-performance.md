---
title: Azure Application Insights를 사용하여 성능 문제 진단 | Microsoft Docs
description: Azure Application Insights를 사용하여 응용 프로그램에서 성능 문제를 찾고 진단하는 자습서입니다.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 8489992303425cc00c15994b55ade958d77549e4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Azure Application Insights를 사용하여 성능 문제 찾기 및 진단

Azure Application Insights는 응용 프로그램에서 원격 분석을 수집하여 해당 작업 및 성능 분석을 돕습니다.  이 정보를 사용하여 발생할 수 있는 문제를 식별하거나 사용자에게 가장 큰 영향을 주는 응용 프로그램에 대한 개선 사항을 식별할 수 있습니다.  이 자습서에서는 응용 프로그램의 서버 구성 요소 및 클라이언트 관점의 성능을 분석하는 프로세스를 안내합니다.  다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서버 쪽 작업의 성능 식별
> * 성능 저하의 근본 원인을 파악하는 서버 작업 분석
> * 가장 느린 클라이언트 쪽 작업 식별
> * 쿼리 언어를 사용하여 페이지 보기의 세부 정보 분석


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- Azure에 .NET 응용 프로그램을 배포하고 [Application Insights SDK를 사용하도록 설정](app-insights-asp-net.md)합니다.
- 응용 프로그램에 대한 [Application Insights 프로파일러를 활성화합니다](app-insights-profiler.md#installation).

## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.

## <a name="identify-slow-server-operations"></a>느린 서버 작업 식별
Application Insights는 응용 프로그램에서 다른 작업에 대한 성능 정보를 수집합니다. 가장 긴 기간으로 이러한 작업을 식별하여 잠재적인 문제를 진단하거나 진행 중인 개발을 대상으로 하여 응용 프로그램의 전반적인 성능을 개선할 수 있습니다.

1. **Application Insights**를 선택한 다음 구독을 선택합니다.  
1. **성능** 패널을 열려면 **조사** 메뉴 아래의 **성능**을 선택하거나 **서버 응답 시간** 그래프를 클릭합니다.

    ![성능](media/app-insights-tutorial-performance/performance.png)

2. **성능** 패널은 응용 프로그램에 대한 각 작업의 수 및 평균 기간을 표시합니다.  이 정보를 사용하여 사용자에게 가장 큰 영향을 주는 해당 작업을 식별할 수 있습니다. 이 예제에서는 **고객/세부 정보 가져오기** 및 **홈/인덱스 가져오기**는 상대적으로 높은 기간 및 호출 수로 인해 조사할 후보일 가능성이 있습니다.  다른 작업은 더 높은 기간을 가질 수 있지만 거의 호출되지 않으므로 해당 개선의 효과는 최소화됩니다.  

    ![성능 패널](media/app-insights-tutorial-performance/performance-blade.png)

3. 그래프는 현재 시간에 따라 모든 작업의 평균 기간을 보여 줍니다.  그래프에 고정하여 관심이 있는 작업을 추가합니다.  일부 조사할 가치가 있는 최대치가 있다는 것을 보여 줍니다.  그래프의 시간 창을 축소하여 이를 더 분리합니다.

    ![고정 작업](media/app-insights-tutorial-performance/pin-operations.png)

4.  작업을 클릭하여 오른쪽에서 해당 성능 패널을 봅니다. 다른 요청에 대한 기간의 분포를 보여 줍니다.  사용자는 일반적으로 약 0.5초에서 성능 저하를 알아차리므로 500밀리초 이상의 요청으로 창을 축소합니다.  

    ![기간 배포](media/app-insights-tutorial-performance/duration-distribution.png)

5.  이 예제에서는 상당한 요청의 수가 처리되는 데 1초 이상이 소요되는 것을 볼 수 있습니다. **작업 세부 정보**를 클릭하여 이 작업의 세부 정보를 볼 수 있습니다.

    ![작업 세부 정보](media/app-insights-tutorial-performance/operation-details.png)

    > [!NOTE]
    “통합된 세부 정보: E2E 트랜잭션 진단” [미리 보기 환경](app-insights-previews.md)을 사용하도록 설정하여 요청, 종속성, 예외, 추적, 이벤트 등과 같은 모든 관련 서버 쪽 원격 분석을 하나의 전체 화면 보기에서 확인할 수 있습니다. 

    미리 보기를 사용하도록 설정하면 통합된 환경에서 오류 또는 예외와 함께 종속성 호출에 소요된 시간을 볼 수 있습니다. 구성 요소 간 트랜잭션의 경우 세부 정보 창 함께 Gantt 차트를 통해 근본 원인 구성 요소, 종속성 또는 예외를 신속하게 진단할 수 있습니다. 아래쪽 섹션을 확장하여 선택한 구성 요소 작업에 수집된 모든 추적 또는 이벤트의 시간 시퀀스를 확인할 수 있습니다. [새 환경에 대해 자세히 알아보세요](app-insights-transaction-diagnostics.md).  

    ![트랜잭션 진단](media/app-insights-tutorial-performance/e2e-transaction-preview.png)


6.  지금까지 수집한 정보는 성능 저하가 있다는 것만을 확인하지만 근본 원인에 조금 접근합니다.  **프로파일러**는 작업에 대해 실행되는 실제 코드 및 각 단계에 필요한 시간을 표시하여 이를 돕습니다. 일부 작업은 프로파일러가 주기적으로 실행되므로 추적이 없을 수 있습니다.  시간이 지남에 따라 더 많은 작업에 추적이 있어야 합니다.  작업에 대한 프로파일러를 시작하려면 **프로파일러 추적**을 클릭합니다.
5.  추적은 각 작업에 대한 개별 이벤트를 보여주므로 전반적인 작업의 기간에 대한 근본 원인을 진단할 수 있습니다.  가장 긴 기간을 포함하는 상위 예 중 하나를 클릭합니다.
6.  **실행 부하 과다 경로 표시**를 클릭하여 작업의 총 기간에 가장 큰 영향을 주는 이벤트의 특정 경로를 강조 표시합니다.  이 예에서는 가장 느린 호출이 *FabrikamFiberAzureStorage.GetStorageTableData* 메서드에서 비롯되었음을 확인할 수 있습니다. 대부분의 시간을 사용하는 파트는 *CloudTable.CreateIfNotExist* 메서드입니다. 함수가 호출될 때마다 이 코드 줄을 실행하면 불필요한 네트워크 호출 및 CPU 리소스가 사용됩니다. 코드를 수정하는 가장 좋은 방법은 한 번만 실행하는 일부 시작 메서드에 이 줄을 배치하는 것입니다. 

    ![프로파일러 세부 정보](media/app-insights-tutorial-performance/profiler-details.png)

7.  화면 위쪽의 **성능 팁**은 과도한 기간이 대기로 인한 것인 평가를 지원합니다.  다양한 유형의 이벤트 해석에 대한 설명서의 **대기** 링크를 클릭합니다.

    ![성능 팁](media/app-insights-tutorial-performance/performance-tip.png)

8.  추가 분석을 위해 **.etl 추적 다운로드**를 클릭하여 Visual Studio에 추적을 다운로드할 수 있습니다.

## <a name="use-analytics-data-for-server"></a>서버에 대해 분석 데이터 사용
Application Insights Analytics는 Application Insights에서 수집된 모든 데이터를 분석할 수 있도록 하는 풍부한 쿼리 언어를 제공합니다.  이를 사용하여 요청 및 성능 데이터에 대한 심층 분석을 수행할 수 있습니다.

1. 작업 세부 정보 패널로 돌아가서 Analytics 단추를 클릭합니다.

    ![Analytics 단추](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics가 패널에서 각 보기에 대한 쿼리와 함께 열립니다.  그대로 이러한 쿼리를 실행하거나 요구 사항에 맞게 수정할 수 있습니다.  첫 번째 쿼리는 시간이 지남에 따른 이 작업에 대한 기간을 보여 줍니다.

    ![분석](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>느린 클라이언트 작업 식별
Application Insights는 최적화를 위한 서버 프로세스 식별 외에도 클라이언트 브라우저의 관점을 분석할 수 있습니다.  이를 통해 클라이언트 구성 요소에 대한 잠재적인 개선 사항을 식별하고 다양한 브라우저 또는 다른 위치에서 문제를 식별할 수도 있습니다.

1. **조사** 아래의 **브라우저**를 선택하여 브라우저 요약을 엽니다.  이는 브라우저의 관점에서 응용 프로그램의 다양한 원격 분석의 시각적 요약을 제공합니다.

    ![브라우저 요약](media/app-insights-tutorial-performance/browser-summary.png)

2.  아래의 **가장 느린 페이지는 무엇인가요?**로 스크롤합니다.  클라이언트가 로드하는 데 가장 긴 시간이 소요되는 응용 프로그램의 페이지 목록을 표시합니다.  이 정보를 사용하여 사용자에 가장 큰 영향을 주는 해당 페이지의 우선 순위를 지정할 수 있습니다.
3.  페이지 중 하나를 클릭하여 **페이지 보기** 패널을 엽니다.  예제에서 **/FabrikamProd** 페이지는 과도 한 평균 기간을 표시합니다.  **페이지 보기** 패널은 다른 기간 범위의 분석을 포함하여 이 페이지에 대한 세부 정보를 제공합니다.

    ![페이지 보기](media/app-insights-tutorial-performance/page-view.png)

4.  가장 높은 기간을 클릭하여 이러한 요청의 세부 정보를 검사합니다.  그런 다음 개별 요청을 클릭하여 브라우저의 유형 및 해당 위치를 포함하여 페이지를 요청하는 클라이언트의 세부 정보를 봅니다.  이 정보는 특정 유형의 클라이언트와 관련된 성능 문제가 있는지 여부를 결정하도록 지원할 수 있습니다.

    ![요청 세부 정보](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>클라이언트에 대해 분석 데이터 사용
서버 성능에 대해 수집된 데이터와 같이 Application Insights는 Analytics를 사용하여 심층 분석에 모든 클라이언트 데이터를 사용할 수 있도록 합니다.

1. 브라우저 요약으로 돌아가서 Analytics 아이콘을 클릭합니다.

    ![Analytics 아이콘](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics가 패널에서 각 보기에 대한 쿼리와 함께 열립니다. 첫 번째 쿼리는 시간이 지남에 따른 다양한 페이지 보기에 대한 기간을 보여 줍니다.

    ![분석](media/app-insights-tutorial-performance/client-analytics.png)

3.  스마트 진단은 데이터에서 고유한 패턴을 식별하는 Application Insights Analytics의 기능입니다.  꺾은선형 차트에서 스마트 진단 점을 클릭하면 변칙을 발생한 레코드 없이 동일한 쿼리가 실행됩니다.  이러한 레코드의 세부 정보는 과도한 기간을 유발시키는 해당 페이지 보기의 속성을 식별할 수 있도록 쿼리의 주석 섹션에 표시됩니다.

    ![스마트 진단](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>다음 단계
이제 런타임 예외를 식별하는 방법을 배웠으므로 오류에 대한 응답으로 경고를 생성하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [응용 프로그램 상태에 대한 경고](app-insights-tutorial-alert.md)
