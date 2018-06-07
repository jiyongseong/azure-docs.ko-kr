---
title: Analytics 사용 - Azure Application Insights의 강력한 검색 도구 | Microsoft Docs
description: 'Application Insights의 강력한 진단 검색 도구인 Analytics를 사용하는 방법에 대해 설명합니다. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 5a49d38ddc9d2181f9b35ac948c44df70bad831b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869889"
---
# <a name="using-analytics-in-application-insights"></a>Application Insights에서 Analytics 사용
[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 Log Analytics 쿼리 언어에 대해 설명합니다.

* **[소개 비디오 보기](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, **[시뮬레이션된 데이터에 대한 분석을 테스트](https://analytics.applicationinsights.io/demo)** 합니다.

## <a name="open-analytics"></a>Analytics 열기
Application Insights의 앱 홈 리소스에서 Analytics를 클릭합니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-insights-analytics-using/001.png)

인라인 자습서에서 수행할 수 있는 몇 가지 작업을 확인할 수 있습니다.

[보다 광범위한 둘러보기는 여기](app-insights-analytics-tour.md)서 제공됩니다.

## <a name="query-your-telemetry"></a>원격 분석 쿼리
### <a name="write-a-query"></a>쿼리 작성
![스키마 표시](./media/app-insights-analytics-using/150.png)

왼쪽에 나열된 테이블의 이름(또는 [range](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) 또는 [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) 연산자)으로 시작합니다. [연산자](https://docs.loganalytics.io/docs/Learn/References/Useful-operators) 파이프라인을 만들려면 `|`을(를) 사용합니다. 

IntelliSense에 사용할 수 있는 연산자 및 식 요소를 지정하라는 메시지가 표시됩니다. 정보 아이콘을 클릭하거나 Ctrl+스페이스바를 눌러 보다 긴 설명 및 각 요소를 사용하는 방법에 대한 예제를 가져옵니다.

[Analytics 언어 개요](app-insights-analytics-tour.md) 및 [언어 참조](app-insights-analytics-reference.md)를 참조하세요.

### <a name="run-a-query"></a>쿼리 실행
![쿼리 실행](./media/app-insights-analytics-using/130.png)

1. 쿼리에서는 단일 줄 바꿈을 사용할 수 있습니다.
2. 실행하려는 쿼리의 내부 또는 끝에 커서를 놓습니다.
3. 쿼리 시간 범위를 확인합니다. 쿼리에 고유한 [`where...timestamp...`](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) 절을 포함하여 재정의하거나 변경할 수 있습니다.
3. 이동을 클릭하여 쿼리를 실행합니다.
4. 쿼리에 빈 줄을 넣으면 안 됩니다. 빈 줄로 구분하여 하나의 쿼리 탭에서 분리된 여러 개의 쿼리를 유지할 수 있습니다. 커서가 있는 쿼리만 실행됩니다.

### <a name="save-a-query"></a>쿼리 저장
![쿼리 저장](./media/app-insights-analytics-using/140.png)

1. 현재 쿼리 파일을 저장합니다.
2. 저장된 쿼리 파일을 엽니다.
3. 새 쿼리 파일을 만듭니다.

## <a name="see-the-details"></a>세부 정보 참조
속성의 전체 목록을 보려면 결과의 행을 확장합니다. 구조화된 값이 있는 속성(예: 사용자 지정 차원 또는 예외에 나열된 스택)을 추가로 확장할 수 있습니다.

![행 확장](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>결과 정렬
쿼리에서 반환된 결과를 정렬하고, 필터링하고, 페이지를 매기고, 그룹화할 수 있습니다.

> [!NOTE]
> 브라우저에서 정렬, 그룹화 및 필터링을 수행해도 쿼리가 다시 실행되지는 않습니다. 마지막 쿼리에서 반환된 결과만 다시 정렬됩니다. 
> 
> 결과가 반환하기 전에 서버에서 이러한 작업을 수행하려면 [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 및 [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) 연산자를 사용하여 쿼리를 작성합니다.
> 
> 

보려는 열을 선택하고, 열 머리글을 끌어 다시 정렬하고, 테두리를 끌어 열 크기를 조정합니다.

![열 정렬](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>항목 정렬 및 필터
열의 제목을 클릭하여 결과를 정렬합니다. 다른 방법으로 정렬하려면 다시 클릭하고, 쿼리에서 반환된 원래 순서로 되돌리려면 세 번째로 클릭합니다.

필터 아이콘을 사용하여 검색 범위를 좁힙니다.

![열 정렬 및 필터링](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>항목 그룹화
둘 이상의 열로 정렬하려면 그룹화를 사용합니다. 먼저 사용하도록 설정한 후 열 머리글을 테이블 위의 공간으로 끕니다.

![그룹](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>일부 결과가 누락되었나요?

예상한 결과 중 일부가 표시되지 않는 경우 두 가지 가능한 이유가 있습니다.

* **시간 범위 필터**. 기본적으로 지난 24시간 동안의 결과만 표시됩니다. 원본 테이블에서 검색된 결과의 범위를 제한하는 자동 필터가 있습니다. 

    그러나 드롭다운 메뉴를 사용하여 시간 범위 필터를 변경할 수 있습니다.

    또는 [`where  ... timestamp ...` 절](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)을 쿼리에 포함하여 자동 범위를 재정의할 수 있습니다. 예: 

    `requests | where timestamp > ago('2d')`

* **결과 제한**. 포털에서 반환된 결과에 대해 약 10,000개의 행 제한이 있습니다. 이 제한을 초과하면 경고가 표시됩니다. 이런 경우 테이블의 결과를 정렬한다고 해서 항상 첫 번째 또는 마지막 실제 결과가 모두 표시되는 것은 아닙니다. 

    이 제한에 도달하지 않도록 하는 것이 좋습니다. 시간 범위 필터를 사용하거나 다음과 같은 연산자를 사용합니다.

  * [top 100 by timestamp](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [take 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [summarize ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [where timestamp > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

10,000개가 넘는 행을 원하는 경우 [연속 내보내기](app-insights-export-telemetry.md)를 대신 사용하는 것이 좋습니다. Analytics는 원시 데이터를 검색하기 위한 것이 아니라 분석용으로 설계되었습니다.

## <a name="diagrams"></a>다이어그램
원하는 다이어그램의 유형을 선택합니다.

![다이어그램 유형 선택](./media/app-insights-analytics-using/230.png)

적합한 유형의 열이 여러 개인 경우 x축과 y축 및 결과 정렬 기준으로 사용할 크기의 열을 선택할 수 있습니다.

기본적으로 결과는 처음에는 테이블로 표시되며 다이어그램은 수동으로 선택합니다. 하지만 쿼리 끝에 [render 지시문](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 을 사용하여 다이어그램을 선택할 수 있습니다.

### <a name="analytics-diagnostics"></a>분석 진단


시간 차트에서 데이터에 갑작스러운 스파이크 또는 단계가 나타날 경우 선에서 강조 표시된 점을 볼 수 있습니다. 이것은 분석 진단이 급격한 변화를 필터링하는 속성 조합을 확인했다는 것을 나타냅니다. 해당 점을 클릭하여 필터에 대한 자세한 내용을 확인하고 필터링된 버전을 확인합니다. 이렇게 하면 변화의 원인을 파악할 수 있습니다. 

[진단 분석에 대해 자세히 알아보기](app-insights-analytics-diagnostics.md)


![분석 진단](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>대시보드에 고정
다이어그램 또는 테이블을 [대시보드 공유](app-insights-dashboards.md) 중 하나에 고정할 수 있습니다. 핀을 클릭하면 됩니다. 

![핀 클릭](./media/app-insights-analytics-using/pin-01.png)

즉, 웹 서비스의 성능 또는 사용 현황을 모니터링하기 위해 대시보드를 함께 사용하는 경우 매우 복잡한 분석을 기타 메트릭과 함께 포함할 수 있습니다. 

테이블에 열이 네 개 이하인 경우 대시보드에 고정할 수 있습니다. 상위 7개의 행만 표시됩니다.

### <a name="dashboard-refresh"></a>대시보드 새로 고침
대시보드에 고정된 차트는 약 1시간마다 쿼리를 다시 실행하여 자동으로 새로 고쳐집니다. 새로 고침 단추를 클릭할 수도 있습니다.

### <a name="automatic-simplifications"></a>자동 단순화

차트를 대시보드에 고정할 때 특정 단순화가 적용됩니다.

**시간 제한:** 쿼리는 지난 14일로 자동으로 제한됩니다. 이는 쿼리에 `where timestamp > ago(14d)`를 포함한 것과 같습니다.

**Bin 수 제한:** 수많은 불연속 막대(일반적으로 가로 막대형 차트)가 있는 차트를 표시할 경우 덜 채워진 막대가 하나의 "기타" 막대로 자동으로 그룹화됩니다. 예를 들어 다음 쿼리는

    requests | summarize count_search = count() by client_CountryOrRegion

분석에서 다음과 같이 표시됩니다.

![길게 늘어지는 차트](./media/app-insights-analytics-using/pin-07.png)

하지만 대시보드에 이를 고정하면 다음과 같이 표시됩니다.

![제한된 막대가 있는 차트](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Excel로 내보내기
쿼리를 실행한 후 .csv 파일을 다운로드할 수 있습니다. **Excel로 내보내기**를 클릭합니다.

## <a name="export-to-power-bi"></a>Power BI에 내보내기
쿼리에 커서를 놓고 **Power BI로 내보내기**를 선택합니다.

![Analytics에서 Power BI로 내보내기](./media/app-insights-analytics-using/240.png)

Power BI에서 쿼리를 실행합니다. 일정에 따라 새로 고침을 설정할 수 있습니다.

Power BI를 사용하여 다양한 원본에서 데이터를 결합하는 대시보드를 만들 수 있습니다.

[Power BI에 내보내기에 대한 자세한 정보](app-insights-export-power-bi.md)

## <a name="deep-link"></a>딥 링크

다른 사용자에게 보낼 수 있는 **공유 링크로 내보내기**에서 링크를 가져옵니다. 사용자에게 [리소스 그룹에 대한 액세스 권한](app-insights-resources-roles-access-control.md)이 있는 경우 해당 쿼리가 Analytics UI에서 열립니다.

(링크에서 쿼리 텍스트는 "?q=", gzip 압축 및 base-64 인코딩 다음에 나타납니다. 코드를 작성하여 사용자에게 제공하는 딥 링크를 생성할 수 있습니다. 단, [REST API](https://dev.applicationinsights.io/)를 사용하여 코드에서 분석을 실행하는 것이 좋습니다.)


## <a name="automation"></a>Automation

[데이터 액세스 REST API](https://dev.applicationinsights.io/)를 실행하여 분석 쿼리를 실행합니다. [예](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count)(PowerShell 사용):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

분석 UI와는 달리 REST API가 타임스탬프 제한을 쿼리에 자동으로 추가하지 않습니다. 엄청난 응답을 받지 않으려면 반드시 where-clause를 직접 추가해야 합니다.



## <a name="import-data"></a>데이터 가져오기

CSV 파일에서 데이터를 가져올 수 있습니다. 일반적인 용도는 원격 분석에서 테이블을 결합할 수 있는 정적 데이터를 가져오는 것입니다. 

예를 들어 원격 분석에서 인증된 사용자가 별칭 또는 임시 ID로 식별되는 경우 별칭을 실제 이름에 매핑하는 테이블을를 가져올 수 있습니다. 요청 원격 분석에서 조인을 수행하여 Analytics 보고서의 실제 이름으로 사용자를 식별할 수 있습니다.

### <a name="define-your-data-schema"></a>데이터 스키마 정의

1. **설정**(왼쪽 위), **데이터 원본**을 차례로 클릭합니다. 
2. 지침에 따라 데이터 원본을 추가합니다. 10개 이상의 행을 포함해야 하는 데이터의 샘플을 제공하라는 메시지가 표시됩니다. 그런 다음 스키마를 수정합니다.

그러면 개별 테이블을 가져오는 데 사용할 수 있는 데이터 원본이 정의됩니다.

### <a name="import-a-table"></a>테이블 가져오기

1. 목록에서 데이터 원본 정의를 엽니다.
2. "업로드"를 클릭하고 지침에 따라 테이블을 업로드합니다. 여기에는 REST API에 대한 호출이 포함되므로 쉽게 자동화할 수 있습니다. 

이제 Analytics 쿼리에서 테이블을 사용할 수 있습니다. Analytics에 표시됩니다. 

### <a name="use-the-table"></a>테이블 사용

`usermap`이라는 데이터 원본 정의가 있고 여기에 `realName` 및 `user_AuthenticatedId`라는 두 개의 필드가 있다고 가정해 보겠습니다. `requests` 테이블에도 `user_AuthenticatedId` 필드가 있으므로 쉽게 조인할 수 있습니다.

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
요청의 결과 테이블에는 `realName`이라는 추가 열이 있습니다.

### <a name="import-from-logstash"></a>LogStash에서 가져오기

[LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)를 사용하는 경우 Analytics를 사용하여 로그를 쿼리할 수 있습니다. [Analytics로 데이터를 파이프하는 플러그 인](https://github.com/Microsoft/logstash-output-application-insights)을 사용합니다. 

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

