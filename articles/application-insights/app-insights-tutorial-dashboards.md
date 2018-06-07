---
title: "Azure Application Insights에서 사용자 지정 대시보드 만들기 | Microsoft Docs"
description: "Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드를 만드는 자습서입니다."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 50d2545d5145f1d93a1ea9fed3e4f98b474d41b2
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드 만들기

각각이 다른 리소스 그룹 및 구독에서 여러 Azure 리소스의 데이터를 시각화하는 타일을 포함하는 여러 대시보드를 Azure Portal에서 만들 수 있습니다.  Azure Application Insights에서 서로 다른 차트 및 보기를 고정하여 응용 프로그램의 상태 및 성능의 전체 그림을 제공하는 사용자 지정 대시보드를 만들 수 있습니다.  이 자습서에서는 Azure Application Insights에서 여러 유형의 데이터 및 시각화를 포함하는 사용자 지정 대시보드를 만드는 과정을 안내합니다.  다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 사용자 지정 대시보드 만들기
> * 타일 갤러리에서 타일 추가
> * Application Insights에서 표준 메트릭을 대시보드에 추가 
> * 사용자 지정 메트릭 차트 Application Insights를 대시보드에 추가
> * Analytics 쿼리의 결과를 대시보드에 추가 



## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure에 .NET 응용 프로그램을 배포하고 [Application Insights SDK를 사용하도록 설정](app-insights-asp-net.md)합니다. 

## <a name="log-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-new-dashboard"></a>새 대시보드 만들기
단일 대시보드는 여러 응용 프로그램, 리소스 그룹 및 구독에서 리소스를 포함할 수 있습니다.  응용 프로그램에 대한 새 대시보드를 만들어서 자습서를 시작합니다.  

2.  포털의 주 화면에서 **새 대시보드**를 선택합니다.

    ![새 대시보드](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. 대시보드의 이름을 입력합니다.
4. 대시보드에 추가할 수 있는 다양한 타일에 대한 **타일 갤러리**를 살펴봅니다.  갤러리에서 타일을 추가하는 것 외에도 Application Insights의 차트 및 다른 보기를 대시보드에 직접 고정할 수 있습니다.
5. **Markdown** 타일을 찾고 대시보드로 끌어옵니다.  이 타일을 통해 대시보드에 설명 텍스트를 추가하는 데 적합한 markdown에서 서식이 지정된 텍스트를 추가할 수 있습니다.
6. 타일의 속성에 텍스트를 추가하고 대시보드 캔버스에서 크기를 조정합니다.
    
    ![markdown 타일 편집](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. 화면의 위쪽에서 **사용자 지정 완료**를 클릭하여 타일 사용자 지정 모드를 종료한 다음 **변경 내용 게시**를 클릭하여 변경 내용을 저장합니다.

    ![markdown 타일이 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>상태 추가 개요
정적 텍스트만이 있는 대시보드는 그다지 흥미롭지 않으므로 이제 Application Insights에서 타일을 추가하여 응용 프로그램에 대한 정보를 표시합니다.  타일 갤러리에서 Application Insights 타일을 추가하거나 Application Insights 화면에서 직접 고정할 수 있습니다.  이를 통해 익숙한 차트 및 보기를 대시보드에 고정하기 전에 해당 내용을 구성할 수 있습니다.  응용 프로그램에 대한 표준 상태 개요를 추가하여 시작합니다.  구성이 필요하지 않으며 대시보드에 최소한의 사용자 지정을 허용합니다.


1. Azure 메뉴에서 **Application Insights**를 선택한 다음 응용 프로그램을 선택합니다.
2. **개요 타임라인**에서 상황에 맞는 메뉴를 선택하고 **대시보드에 고정**을 클릭합니다.  보고 있던 마지막 대시보드에 타일을 추가합니다.  

    ![개요 타임라인 고정](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. 화면 위쪽에서 **대시보드 보기**를 클릭하여 대시보드로 돌아갑니다.
4. 이제 개요 타임라인이 대시보드에 추가됩니다.  위치로 클릭하여 끌어온 다음 **사용자 지정 완료** 및 **변경 내용 게시**를 클릭합니다.  대시보드에는 이제 몇 가지 유용한 정보가 포함된 타일이 있습니다.

    ![개요 타임라인이 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>사용자 지정 메트릭 차트 추가
**메트릭** 패널을 통해 선택적 필터 및 그룹화를 사용하여 시간에 따라 Application Insights에서 수집한 메트릭을 그래프화할 수 있습니다.  Application Insights의 다른 요소와 마찬가지로 이 차트를 대시보드에 추가할 수 있습니다.  먼저 약간의 사용자 지정 작업을 수행해야 합니다.

1. Azure 메뉴에서 **Application Insights**를 선택한 다음 응용 프로그램을 선택합니다.
1. **메트릭**을 선택합니다.  
2. 빈 차트가 이미 생성되었으며 메트릭을 추가하라는 메시지가 표시됩니다.  차트에 메트릭을 추가하고 필요에 따라 필터 및 그룹을 추가합니다.  아래 예제에서는 성공적으로 그룹화된 서버 요청 수를 보여 줍니다.  성공 및 실패한 요청의 실행 중인 보기를 제공합니다.

    ![메트릭 추가](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. 차트에 대한 상황에 맞는 메뉴를 선택하고 **대시보드에 고정**을 선택합니다.  사용하고 있던 마지막 대시보드에 보기를 추가합니다.

    ![메트릭 차트 고정](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. 화면 위쪽에서 **대시보드 보기**를 클릭하여 대시보드로 돌아갑니다.

4. 이제 타임라인 메트릭 차트가 대시보드에 추가되었습니다. 위치로 클릭하여 끌어온 다음 **사용자 지정 완료** 및 **변경 내용 게시**를 차례로 클릭합니다. 

    ![메트릭이 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>메트릭 탐색기
**메트릭 탐색기**는 메트릭과 비슷하지만 대시보드에 추가되는 경우 훨씬 더 많은 사용자 지정이 가능합니다.  메트릭을 그래프화하는 데 사용할 수 있는 것은 특정 기본 설정 및 요구 사항에 따라 달라집니다.

1. Azure 메뉴에서 **Application Insights**를 선택한 다음 응용 프로그램을 선택합니다.
1. **메트릭 탐색기**를 선택합니다. 
2. 클릭하여 차트를 편집하고 하나 이상의 메트릭 및 필요에 따라 자세한 구성을 선택합니다.  예제는 평균 페이지 응답 시간을 추적하는 꺾은선형 차트를 표시합니다.
3. 오른쪽 위의 핀 아이콘을 클릭하여 대시보드에 차트를 추가한 다음 위치로 끌어옵니다.

    ![메트릭 탐색기](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. 메트릭 탐색기 타일을 통해 대시보드에 추가되면 더 많은 사용자 지정이 가능합니다.  타일을 마우스 오른쪽 단추로 클릭하고 **제목 편집**을 선택하여 사용자 지정 제목을 추가합니다.  계속 진행하여 원하는 경우 다른 사용자 지정을 수행합니다.

    ![메트릭 탐색기가 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. 이제 대시보드에 추가된 메트릭 탐색기 차트가 있습니다.

    ![메트릭 탐색기가 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Analytics 쿼리 추가
Azure Application Insights Analytics는 Application Insights에서 수집된 모든 데이터를 분석할 수 있도록 하는 풍부한 쿼리 언어를 제공합니다.  차트 및 다른 보기와 마찬가지로 대시보드에 Analytics 쿼리의 출력을 추가할 수 있습니다.   

Azure Applications Insights Analytics는 별도 서비스이므로 Analytics 쿼리를 포함하도록 대시보드를 공유해야 합니다.  Azure 대시보드를 공유하는 경우 다른 사용자 및 리소스에서 사용할 수 있도록 Azure 리소스로 게시합니다.  

1. 대시보드 화면 위쪽에서 **공유**를 클릭합니다.

    ![대시보드 게시](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. **대시보드 이름**을 동일하게 유지하고 **구독 이름**을 선택하여 대시보드를 공유합니다.  **게시**를 클릭합니다.  이제 대시보드를 다른 서비스 및 구독에서 사용할 수 있습니다.  필요에 따라 대시보드에 대한 액세스를 가져야 하는 특정 사용자를 정의할 수 있습니다.
1. Azure 메뉴에서 **Application Insights**를 선택한 다음 응용 프로그램을 선택합니다.
2. 화면 위쪽에서 **Analytics**를 클릭하여 Analytics 포털을 엽니다.

    ![Analytics 시작](media/app-insights-tutorial-dashboards/start-analytics.png)

3. 상위 10개의 가장 많이 요청된 페이지 및 해당 요청 수를 반환하는 다음 쿼리를 입력합니다.

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. **이동**을 클릭하여 쿼리의 결과를 확인합니다.
5. 고정 아이콘을 클릭하고 대시보드의 이름을 선택합니다.  마지막 대시보드가 사용되는 이전 단계와 달리 이 옵션에서 사용자가 대시보드를 선택하게 하는 이유는 Analytics 콘솔은 별도 서비스이며 모든 사용 가능한 공유 대시보드에서 선택해야 하기 때문입니다.

    ![Analytics 쿼리 고정](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. 대시보드로 다시 이동하기 전에 다른 쿼리를 추가하지만 이번에는 차트로 렌더링하여 대시보드에서 Analytics 쿼리를 시각화하는 다양한 방법을 표시합니다.  대부분의 예외로 상위 10개의 작업을 요약하는 다음 쿼리로 시작합니다.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. **차트**를 선택한 다음 출력을 시각화하는 **도넛형**으로 변경합니다.

    ![Analytics 차트](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. 고정 아이콘을 클릭하여 대시보드에 차트를 고정하고 이번에는 링크를 선택하여 대시보드로 돌아갑니다.
4. 이제 쿼리의 결과가 선택한 형식으로 대시보드에 추가됩니다.  각각을 클릭하여 위치로 끌어온 다음 **편집 완료**를 클릭합니다.
5. 각 타일을 마우스 오른쪽 단추로 클릭하고 **제목 편집**을 선택하여 설명이 포함된 제목을 지정합니다.

    ![Analytics가 표시된 대시보드](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. **변경 내용 게시**를 클릭하여 이제 Application Insights에서 다양한 차트 및 시각화를 포함하는 변경 내용을 대시보드에 커밋합니다.


## <a name="next-steps"></a>다음 단계
이제 사용자 지정 대시보드를 만드는 방법을 배웠으니 사례 연구를 포함하는 Application Insights 설명서의 나머지 부분을 살펴봅니다.

> [!div class="nextstepaction"]
> [심층 진단](app-insights-devops.md)
