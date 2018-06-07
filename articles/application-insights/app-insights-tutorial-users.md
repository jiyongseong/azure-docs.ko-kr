---
title: "Azure Application Insights에서 고객 이해 | Microsoft Docs"
description: "Azure Application Insights를 사용하여 고객이 응용 프로그램을 사용하는 방법을 이해하는 자습서입니다."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Azure Application Insights를 사용하여 고객이 응용 프로그램을 사용하는 방법 이해

Azure Application Insights는 사용량 정보를 수집하여 사용자자 응용 프로그램과 상호 작용하는 방법을 이해할 수 있도록 돕습니다.  이 자습서에서는 이 정보를 분석하는 데 사용할 수 있는 다양한 리소스를 안내합니다.  다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * 응용 프로그램에 액세스하는 사용자에 대한 세부 정보 분석
> * 세션 정보를 사용하여 고객이 응용 프로그램을 사용하는 방법 분석
> * 원하는 사용자 작업을 실제 작업과 비교할 수 있도록 하는 깔때기 정의 
> * 통합 문서를 만들어 시각화 및 쿼리를 단일 문서로 통합
> * 유사한 사용자를 그룹화하여 함께 분석
> * 응용 프로그램으로 돌아오는 사용자 알아보기
> * 사용자가 응용 프로그램을 통해 탐색하는 방법 검사


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- [Visual Studio 스냅숏 디버거](http://aka.ms/snapshotdebugger)를 다운로드 및 설치합니다.
- Azure에 .NET 응용 프로그램을 배포하고 [Application Insights SDK를 사용하도록 설정](app-insights-asp-net.md)합니다. 
- 사용자 지정 이벤트/페이지 보기 추가에 대한 [응용 프로그램에서 원격 분석 전송](app-insights-usage-overview.md#send-telemetry-from-your-app)
- [사용자 컨텍스트](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)를 전송하여 시간에 따라 사용자 작업을 추적하고 사용량 기능을 최대한 활용합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="get-information-about-your-users"></a>사용자에 대한 정보 가져오기
**사용자** 패널을 통해 여러 가지 방법으로 사용자에 대한 중요한 세부 정보를 이해할 수 있습니다. 이 패널을 사용하여 사용자가 연결하는 위치, 해당 클라이언트의 세부 정보 및 액세스하는 응용 프로그램의 영역과 같은 정보를 이해합니다. 

1. **Application Insights**를 선택한 다음 구독을 선택합니다.
2. 메뉴에서 **사용자**를 선택합니다.
3. 기본 보기는 지난 24시간 동안 응용 프로그램에 연결한 고유 사용자 수를 표시합니다.  시간 창을 변경할 수 있으며 다양한 조건을 설정하여 이 정보를 필터링할 수 있습니다.

    ![쿼리 작성기](media\app-insights-tutorial-users\QueryBuilder.png)

6. **기간** 드롭다운을 클릭하고 시간 창을 7일로 변경합니다.  그러면 패널에서 다른 차트에 포함된 데이터를 증가시킵니다.

    ![시간 범위 변경](media\app-insights-tutorial-users\TimeRange.png)

4. **분할 기준** 드롭다운을 클릭하여 그래프에 사용자 속성으로 분석을 추가합니다.  **국가 또는 지역**을 선택합니다.  그래프는 동일한 데이터를 포함하지만 각 국가에 대한 사용자 수의 분석을 볼 수 있습니다.

    ![국가 또는 지역 그래프](media\app-insights-tutorial-users\CountryorRegion.png)

5. 차트의 다른 막대 위로 커서를 배치하고 각 국가에 대한 수가 해당 막대로 표시된 시간 창만 반영하는지 확인합니다.
6. 사용자 데이터에 대한 분석을 수행하는 오른쪽의 **Insights** 열을 살펴봅니다.  기간에 따른 고유한 세션 수 및 중요한 사용자 데이터를 구성하는 공용 속성이 있는 레코드와 같은 정보를 제공합니다. 

    ![Insights 열](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>사용자 세션 분석
**세션** 패널은 **사용자** 패널과 유사합니다.  여기서 **사용자**는 응용 프로그램에 액세스하는 사용자에 대한 세부 정보를 이해하도록 돕고, **세션**은 해당 사용자가 응용 프로그램을 사용한 방법을 이해하도록 돕습니다.  

1. 메뉴에서 **세션**을 선택합니다.
2. 그래프를 확인하고 필터링하는 동일한 옵션이 있는지 확인하고 **사용자** 패널에서와 같이 데이터를 분석합니다.

    ![세션 쿼리 작성기](media\app-insights-tutorial-users\SessionsBuilder.png)

3. 오른쪽의 **이러한 세션의 샘플** 창은 많은 수의 이벤트를 포함하는 세션을 나열합니다.  이들은 분석할 관심 있는 세션입니다.

    ![이러한 세션의 샘플](media\app-insights-tutorial-users\SessionsSample.png)

4. 세션 중 하나를 클릭하여 세션에서 모든 작업을 보여 주는 해당 **세션 타임라인**을 봅니다.  이를 통해 많은 수의 예외가 있는 세션과 같은 정보를 확인할 수 있습니다.

    ![세션 타임라인](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>비슷한 사용자를 함께 그룹화
**코호트**는 비슷한 특성에 대해 그룹화된 사용자의 집합입니다.  코호트를 사용하여 특정 사용자 그룹을 분석할 수 있도록 다른 패널에서 데이터를 필터링할 수 있습니다.  예를 들어, 구매를 완료한 사용자만 분석하려 할 수 있습니다.

1.  메뉴에서 **코호트**를 선택합니다.
2.  **새로 만들기**를 클릭하여 새 코호트를 만듭니다.
3.  **사용한 사용자** 드롭다운을 선택하고 작업을 선택합니다.  보고서의 시간 창 내에서 이 작업을 수행한 사용자만 포함됩니다.

    ![지정된 작업을 수행한 코호트](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  메뉴에서 **사용자**를 선택합니다.
5.  **표시** 드롭다운에서 방금 만든 코호트를 선택합니다.  그래프에 대한 데이터는 해당 사용자로 제한됩니다.

    ![사용자 도구에서 코호트](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>원하는 작업과 실제 비교
이전 패널이 응용 프로그램의 사용자가 수행한 작업에 집중한 반면 **깔때기**는 사용자가 수행하길 원하는 작업에 집중합니다.  깔때기는 응용 프로그램에서 단계의 집합 및 단계 간을 이동하는 사용자의 백분율을 나타냅니다.  예를 들어, 응용 프로그램에 연결하고 제품을 검색하는 사용자의 백분율을 측정하는 깔때기를 만들 수 있습니다.  그런 다음 해당 제품을 쇼핑 카트에 추가한 사용자의 백분율 및 구매를 완료하는 사용자의 백분율을 볼 수 있습니다.

1. 메뉴에서 **깔때기**를 선택한 다음 **새로 만들기**를 클릭합니다. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. **깔때기 이름**에 입력합니다.
3. 각 단계에 대한 작업을 선택하여 두 개 이상의 단계로 깔때기를 만듭니다.  작업 목록은 Application Insights에서 수집한 사용량 데이터에서 만들어집니다.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. **저장**을 클릭하여 깔때기를 저장한 다음 해당 결과를 확인합니다.  깔때기의 오른쪽 창은 첫 번째 작업 전과 마지막 작업 후에 가장 일반적인 이벤트를 보여 주어 특정 시퀀스에 대한 사용자 경향을 이해할 수 있도록 돕습니다.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>돌아오는 고객 알아보기
**재방문 주기**를 통해 응용 프로그램으로 다시 돌아오는 사용자를 이해할 수 있습니다.  

1. 메뉴에서 **재방문 주기**를 선택합니다.
2. 기본적으로 분석된 정보는 작업을 수행한 다음 작업을 수행하기 위해 돌아온 사용자를 포함합니다.  이 필터를 다음을 포함하도록, 예를 들어, 구매를 완료한 후 돌아온 사용자만 포함하도록 변경할 수 있습니다.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. 조건과 일치하는 돌아온 사용자는 서로 다른 기간에 대한 그래픽 및 테이블 형식으로 표시됩니다.  일반적인 패턴은 시간에 따라 사용자가 돌아오는 점진적 하락에 대한 것입니다.  한 기간에서 다음 기간으로의 갑작스런 하락은 문제를 야기할 수 있습니다. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>사용자 탐색 분석
**사용자 흐름**은 페이지와 응용 프로그램의 기능 간에 사용자가 탐색하는 방법을 시각화합니다.  이 기능을 사용하면 사용자가 일반적으로 특정 페이지에서 이동하는 위치, 일반적으로 응용 프로그램을 종료하는 방법 및 정기적으로 반복되는 작업이 있는지와 같은 질문에 답할 수 있습니다.

1.  메뉴에서 **사용자 흐름**을 선택합니다.
2.  **새로 만들기**를 클릭하여 새 사용자 흐름을 만든 다음 **편집**을 클릭하여 해당 세부 정보를 편집합니다.
3.  **시간 범위**를 7일로 증가시킨 다음 초기 이벤트를 선택합니다.  흐름은 해당 이벤트로 시작하는 사용자 세션을 추적합니다.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  사용자 흐름이 표시되고 다른 사용자 경로 및 해당 세션 수를 볼 수 있습니다.  파란색 선은 현재 작업 후에 사용자가 수행한 작업을 나타냅니다.  빨간색 선은 사용자 세션의 끝을 나타냅니다.

    ![](media\app-insights-tutorial-users\flows.png)

5.  흐름에서 이벤트를 제거하려면 작업 모서리에서 **x**를 클릭한 다음 **그래프 만들기**를 클릭합니다.  그래프가 제거된 해당 이벤트의 모든 인스턴스로 다시 그려집니다.  **편집**을 클릭하여 이벤트가 이제 **제외된 이벤트**에 추가되었는지 확인합니다.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>사용량 데이터 통합
**통합 문서**는 데이터 시각화, Analytics 쿼리 및 텍스트를 대화형 문서로 결합합니다.  통합 문서를 사용하여 일반적인 사용량 정보를 함께 그룹화하고, 특정 오류로부터 정보를 통합하거나 응용 프로그램의 사용량에 대해 팀에게 다시 보고할 수 있습니다.

1.  메뉴에서 **통합 문서**를 선택합니다.
2.  **새로 만들기**를 클릭하여 새 통합 문서를 만듭니다.
3.  막대형 차트로 표시된 마지막 날짜의 모든 사용량 데이터를 포함하는 쿼리가 이미 제공됩니다.  이 쿼리를 사용하여 수동으로 편집하거나 **샘플 쿼리**를 클릭하여 다른 유용한 쿼리에서 선택할 수 있습니다.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  **편집 완료**를 클릭합니다.
5.  위쪽 창에서 **편집**을 클릭하여 통합 문서 맨 위에 있는 텍스트를 편집합니다.  Markdown을 사용하여 형식이 지정됩니다.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  **사용자 추가**를 클릭하여 사용자 정보를 포함하는 그래프를 추가합니다.  원하는 경우 그래프의 세부 정보를 편집한 다음 **편집 완료**를 클릭하여 저장합니다.


## <a name="next-steps"></a>다음 단계
이제 사용자를 분석하는 방법을 배웠으므로 이 정보를 응용 프로그램에 대한 다른 유용한 데이터와 결합하는 사용자 지정 대시보드를 만드는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 대시보드 만들기](app-insights-tutorial-dashboards.md)
