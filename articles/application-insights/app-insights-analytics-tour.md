---
title: Azure Application Insights 내 Analytics 둘러보기 | Microsoft Docs
description: Application Insights의 강력한 검색 도구인 Analytics의 모든 주요 쿼리에 대한 간단한 샘플
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: mbullwin
ms.openlocfilehash: b57683f5b0634c27e8604c26496a3421ae386439
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights의 Analytics 둘러보기
[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 Log Analytics 쿼리 언어에 대해 설명합니다.

* **[소개 비디오 보기](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, **[시뮬레이션된 데이터에 대한 분석을 테스트](https://analytics.applicationinsights.io/demo)** 합니다.
* **[SQL 사용자 치트 시트](https://aka.ms/sql-analytics)** 에서는 가장 일반적인 코드를 변환합니다.

시작하기 위해 몇 가지 기본적인 쿼리를 연습해 보겠습니다.

## <a name="connect-to-your-application-insights-data"></a>Application Insights 데이터에 연결
Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md) 에서 분석을 엽니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsiodocslanguage-referencetabular-operators-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators): n개의 행 표시
사용자 작업(일반적으로 웹앱에서 받는 HTTP 요청)을 기록하는 데이터 요소는 `requests`라는 테이블에 저장됩니다. 각 행은 앱의 Application Insights SDK에서 수신된 원격 분석 데이터 요소입니다.

테이블의 몇 가지 샘플 행을 검사하는 것으로 시작해 보겠습니다.

![결과](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> 이동을 클릭하기 전에 커서를 문의 어딘가에 둡니다. 문을 둘 이상의 행으로 분할할 수 있지만 문에 빈 줄을 넣으면 안 됩니다. 빈 줄은 창에서 여러 개의 별도 쿼리를 유지하는 편리한 방법입니다.
>
>

열을 선택하고 끌어서 놓고 열로 그룹화하고 필터링합니다.

![결과의 오른쪽 위에 있는 열 선택을 클릭](./media/app-insights-analytics-tour/030.png)

세부 정보를 보려면 모든 항목을 확장합니다.

![테이블을 선택하고 열을 구성](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> 웹 브라우저에서 사용할 수 있는 결과의 순서를 변경하려면 열 머리글을 클릭합니다. 단, 큰 결과 집합의 경우에는 브라우저에 다운로드되는 행의 수가 제한된다는 점을 고려해야 합니다. 이렇게 정렬하면 반환된 결과 집합만 정렬되며 실제 최상위 또는 최하위 항목이 항상 표시되지는 아닙니다. 항목을 안정적으로 정렬하려면 `top` 또는 `sort` 연산자를 사용합니다.
>
>

## <a name="query-across-applications"></a>응용 프로그램 간 쿼리
여러 Application Insights 응용 프로그램의 데이터를 결합하려는 경우 **앱** 키워드를 사용하여 테이블 이름과 함께 응용 프로그램을 지정합니다.  이 쿼리는 **union** 명령을 사용하여 두 개의 서로 다른 응용 프로그램의 요청을 결합합니다.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsiodocslanguage-referencetabular-operatorstop-operator-and-sorthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssort-operator"></a>[Top](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 및 [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
`take` 은(는) 빨리 확인할 결과 샘플을 가져오는 데 유용하지만 테이블의 행을 특정 순서 없이 표시합니다. 순서가 지정된 보기를 가져오려면 `top`(샘플의 경우) 또는 `sort`(전체 테이블에 대해)을(를) 사용합니다.

특정 열을 기준으로 순서가 정해진 처음 n 행 표시:

```AIQL

    requests | top 10 by timestamp desc
```

* *구문:* 대부분의 연산자에는 `by`와(과) 같은 키워드 매개 변수가 있습니다.
* `desc`은(는) 내림차순을 의미하고 `asc`은(는) 오름차순을 의미합니다.

![](./media/app-insights-analytics-tour/260.png)

`top...`을(를) 사용하면 `sort ... | take...`을(를) 보다 효율적으로 설명할 수 있습니다. 다음과 같이 작성했을 수도 있음:

```AIQL

    requests | sort by timestamp desc | take 10
```

결과는 같지만 조금 더 느리게 실행될 수 있습니다. `sort`의 별칭인 `order`을(를) 작성할 수도 있습니다.

테이블 보기의 열 머리글을 사용하여 화면에서 결과를 정렬할 수도 있습니다. 물론 `take` 또는 `top`을 사용하여 테이블의 일부만 검색하는 경우 열 머리글을 클릭하면 검색한 레코드의 순서만 바뀝니다.

## <a name="wherehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorswhere-operator-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator): 조건에 대한 필터링

특정 결과 코드를 반환하는 요청만 살펴보겠습니다.

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` 연산자는 부울 식을 가져옵니다. 여기서 이에 관한 몇 가지 중요한 사항이 있습니다.

* `and`, `or`: 부울 연산자
* `==`, `<>`, `!=`: 같음 및 같지 않음
* `=~`, `!~`: 대/소문자 구분 없는 문자열 같음 및 같지 않음 더 많은 문자열 비교 연산자가 있습니다.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>성공하지 못한 요청 찾기

보다 큼 비교를 사용하려면 문자열 값을 정수로 변환합니다.

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Time

기본적으로 쿼리는 마지막 24시간으로 제한됩니다. 그러나 이 값을 변경할 수 있습니다.

![](./media/app-insights-analytics-tour/change-time-range.png)

where 절에서 `timestamp`를 언급하는 쿼리를 작성하여 시간 범위를 재정의합니다. 예: 

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

시간 범위 기능은 원본 테이블 중 하나를 언급할 때마다 그 다음에 나오는 'where' 절에 해당합니다.

`ago(3d)`는 '3일 전'을 의미합니다. 기타 시간 단위에는 시간(`2h`, `2.5h`), 분(`25m`) 및 초(`10s`)가 포함됩니다.

기타 예제:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

    // Between specific day/time range
    requests
    | where timestamp > datetime(2018-05-17T17:06:19.892Z) and timestamp <= datetime(2018-05-18T17:06:19.892Z)
    | where duration > 0

```

[날짜 및 시간 참조](https://docs.loganalytics.io/docs/Language-Reference/Data-types/datetime)


## <a name="projecthttpsdocsloganalyticsiodocslanguage-referencetabular-operatorsproject-operator-select-rename-and-compute-columns"></a>[Project](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator): 열 선택, 이름 바꾸기 및 계산
원하는 열만 선택하려면 [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html)을(를) 사용합니다.

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

열 이름을 바꾸고 새 열을 정의할 수도 있음:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* 열 이름은 `['...']` 또는 `["..."]`와 같이 대괄호로 묶은 경우 공백이나 기호를 포함할 수 있습니다.
* `%` 은(는) 일반적인 모듈로 연산자입니다.
* `1d` (한 자릿수, 'd' 한 개)은(는) 하루를 의미하는 시간 간격 리터럴입니다. 기타 시간 간격 리터럴에는 `12h`, `30m`, `10s`, `0.01s` 등이 있습니다.
* `floor`(별칭 `bin`)은(는) 값을 사용자가 입력하는 기준 값에 가장 가까운 낮은 배수로 반올림합니다. 따라서 `floor(aTime, 1s)` 은(는) 시간을 가장 가까운 초로 반올림합니다.

식은 일반적인 연산자(`+`,`-`, ...)를 모두 포함할 수 있으며, 유용한 함수가 다양하게 사용됩니다.

## <a name="extend"></a>Extend
기존 열에 열을 추가하기만 하려면 [`extend`](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)을(를) 사용합니다.

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

기존 열을 모두 유지하려는 경우 [`extend`](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)을(를) 사용하면 [`project`](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)에 비해 표시되는 정보가 상세하지 않습니다.

### <a name="convert-to-local-time"></a>현지 시간으로 변환

타임스탬프는 항상 UTC 기준입니다. 따라서 미국 태평양 연안에 있으며 계절이 겨울이면 다음과 같이 나타낼 수 있습니다.

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```

## <a name="summarizehttpsdocsloganalyticsiodocslanguage-referencetabular-operatorssummarize-operator-aggregate-groups-of-rows"></a>[요약](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator): 행 그룹 집계
`Summarize` 은(는) 행 그룹에서 지정된 *집계 함수* 를 적용합니다.

예를 들어 웹앱이 요청에 응답하는 데 걸리는 시간은 `duration`필드에 보고됩니다. 모든 요청에 대한 평균 응답 시간을 살펴보겠습니다.

![](./media/app-insights-analytics-tour/410.png)

또는 결과를 다양한 이름의 요청으로 분리할 수 있습니다.

![](./media/app-insights-analytics-tour/420.png)

`Summarize`은(는) 스트림의 데이터 요소를 `by` 절에서 동일하게 평가하는 그룹으로 수집합니다. `by` 식의 각 값(예: 위 예제의 경우 각 고유 작업 이름)은 결과 테이블의 행이 됩니다.

또는 하루 중 시간으로 결과를 그룹화할 수 있습니다.

![](./media/app-insights-analytics-tour/430.png)

`bin` 함수(즉, `floor`)를 사용하는 방법을 알아보겠습니다. `by timestamp`을(를) 사용하면 모든 입력 행이 고유한 작은 그룹이 됩니다. 시간 또는 숫자와 같은 임의 연속 스칼라의 경우 연속되는 범위를 관리할 수 있는 불연속 값의 수로 나누어야 합니다. 익숙한 자리 내림 `floor` 함수인 `bin`은 이 작업을 수행하는 가장 쉬운 방법입니다.

동일한 기술을 사용하여 문자열의 범위를 줄일 수 있습니다.

![](./media/app-insights-analytics-tour/440.png)

`name=`을(를) 사용하여 집계 식 또는 by 절에서 결과 열의 이름을 설정할 수 있습니다.

## <a name="counting-sampled-data"></a>샘플링된 데이터 수 계산
`sum(itemCount)` 이벤트 수에 대한 권장 집계입니다. 대부분의 경우 itemCount=1이므로 함수는 단순히 그룹의 행 수를 계산합니다. 하지만 [샘플링](app-insights-sampling.md)이 작동 중인 경우에는 Application Insights에서 원래 이벤트의 일부만 데이터 요소로 유지되므로 각 데이터 요소에 `itemCount`개의 이벤트가 있습니다.

예를 들어 샘플링에서 원래 이벤트의 75%를 삭제하는 경우 유지되는 레코드에서 itemCount==4가 됩니다. 즉, 유지된 모든 레코드마다 4개의 원본 레코드가 있게 됩니다.

적응 샘플링을 사용하면 응용 프로그램이 과도하게 사용되는 기간 동안 itemCount가 더 높아집니다.

따라서 itemCount를 합하면 원래 이벤트 수에 대한 적절한 추정치가 제공됩니다.

![](./media/app-insights-analytics-tour/510.png)

그룹의 행 수를 계산하려는 경우 `count()` 집계(및 개수 계산 작업)도 있습니다.

[집계 함수](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions)의 범위가 있습니다.

## <a name="charting-the-results"></a>결과 차트로 작성
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

기본적으로 결과는 테이블로 표시됩니다.

![](./media/app-insights-analytics-tour/225.png)

테이블 보기보다 더 효과적인 보기가 있습니다. 세로 막대 옵션을 사용하여 차트 보기에서 결과를 살펴보겠습니다.

![차트를 클릭한 다음 세로 막대 차트를 선택하고 x 및 y축을 할당](./media/app-insights-analytics-tour/230.png)

참고로 결과를 시간에 의해 정렬하지 않았지만(테이블 표시에서 확인 가능) 차트 표시는 언제나 날짜 및 시간을 정확한 순서로 표시합니다.


## <a name="timecharts"></a>시간 차트
각 시간에 발생한 이벤트 수 표시:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

차트 표시 옵션 선택:

![시간 차트](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>여러 계열
`summarize` 절의 여러 식은 여러 열을 만듭니다.

`by` 절의 여러 식은 값의 각 조합에 대해 하나씩 여러 행을 만듭니다.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![시간 및 위치별 요청 테이블](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>차원으로 차트를 분할
문자열 열과 숫자 열이 있는 테이블을 차트화할 경우 숫자 데이터를 별도 계열 요소로 분할하는 데 문자열을 사용할 수 있습니다. 둘 이상의 문자열 열이 있는 경우 판별자로 사용할 열을 선택할 수 있습니다.

![분석 차트 분할](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>반송률

부울을 문자열로 변환하여 판별자로 사용합니다.

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>여러 메트릭 표시
둘 이상의 숫자 열이 있는 테이블을 차트화할 경우 타임스탬프 외에도 모든 조합을 표시할 수 있습니다.

![분석 차트 분할](./media/app-insights-analytics-tour/110.png)

**분할하지 않음**을 선택해야 여러 숫자 열을 선택할 수 있습니다. 둘 이상의 숫자 열을 표시하면서 동시에 문자열 열로 분할할 수 없습니다.

## <a name="daily-average-cycle"></a>일일 평균 주기
사용량은 평균 일에 대해 얼마나 변화합니까?

시간으로 범주화된 시간 모듈로 하루 기준 계산 요청:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![평균 일의 시간에 대한 꺾은선형 차트](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> 참고로 현재 우리는 꺾은선형 차트에 표시하기 위해 기간을 날짜 및 시간으로 변환해야 합니다.
>
>

## <a name="compare-multiple-daily-series"></a>여러 개의 일일 계열 비교
서로 다른 국가에서 시각에 따라 사용량은 어떻게 변화합니까?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![client_CountryOrRegion별로 분할](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>분포 출력
서로 다른 길이의 세션이 몇 개 있습니까?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

마지막 줄은 날짜/시간으로 변환해야 합니다. 현재 차트의 x-축은 날짜/시간인 경우에만 스칼라로 표시됩니다.

`where` 절은 단발 세션(sessionDuration==0)을 제외하고 x축의 길이를 설정합니다.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsiodocslanguage-referenceaggregation-functionspercentiles"></a>[백분위수](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/percentiles())
서로 다른 세션 백분위수를 다루는 기간 범위는 무엇입니까?

위의 쿼리를 사용하되 마지막 줄을 다음으로 바꿈:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

또한 둘 이상의 요청을 포함하고 있는 모든 세션을 비롯하여 정확한 그림을 가져오기 위해 where 절에서 상한을 제거했습니다.

![result](./media/app-insights-analytics-tour/180.png)

확인할 수 있는 사항:

* 세션의 5%가 3분 34초 미만의 기간을 가지고 있음;
* 세션의 50%가 36분 미만 동안 지속
* 세션의 5%가 7일보다 오래 지속

각 국가에 대해 별도의 분석을 가져오기 위해 summarize 연산자 둘 다를 통해 client_CountryOrRegion 열을 따로 가져와야 합니다.

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join
요청 및 예외를 비롯한 일부 테이블에 액세스할 수 있습니다.

실패 응답을 반환하는 요청과 관련된 예외를 찾으려면 `session_Id`에 대해 테이블을 조인할 수 있습니다.

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


조인을 수행하기 전에 `project`을(를) 사용하여 필요한 열만 선택하는 것이 좋습니다.
동일한 절에서 타임스탬프 열의 이름을 바꿉니다.

## <a name="lethttpsdocsloganalyticsiodocslanguage-referencequery-statementslet-statement-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/docs/Language-Reference/Query-statements/Let-statement): 변수에 결과 할당

`let`을 사용하여 이전 식의 일부를 분리할 수 있습니다. 결과는 변하지 않음:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Analytics 클라이언트에서 쿼리 부분 사이에 공백 줄을 넣지 마세요. 이 부분을 모두 실행해야 합니다.
>

`toscalar`를 사용하여 단일 표 셀을 값으로 변환합니다.

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

*Let*을 사용하여 함수를 정의합니다.

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>중첩된 개체에 액세스
중첩된 개체에 쉽게 액세스할 수 있습니다. 예를 들어 예외 스트림에서 다음과 같이 구조화된 개체가 표시될 수 있습니다.

![result](./media/app-insights-analytics-tour/520.png)

관심이 있는 속성을 선택하여 평면화할 수 있습니다.

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

결과를 적절한 형식으로 캐스트해야 합니다.


## <a name="custom-properties-and-measurements"></a>사용자 지정 속성 및 측정
응용 프로그램이 이벤트에 대한 [사용자 지정 차원(속성) 및 사용자 지정 측정](app-insights-api-custom-events-metrics.md#properties)에 연결되면 `customDimensions` 및 `customMeasurements` 개체에 표시됩니다.

예를 들어 앱이 다음을 포함한 경우입니다.

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2.d2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

분석에서 이러한 값을 추출하려면:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type
```

사용자 지정 차원이 특정 형식인지 여부를 확인하려면

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

### <a name="special-characters"></a>특수 문자

이름에 특수 문자나 언어 키워드가 있는 식별자의 경우 `['` 및 `']` 또는 `["` 및 `"]`를 사용하여 액세스해야 합니다.

```AIQL

    customEvents
    | extend p2d2 = customDimensions.['p2.d2'], ...
```

[식별자 명명 규칙 참조](https://docs.loganalytics.io/docs/Learn/References/Naming-principles)

## <a name="dashboards"></a>대시보드
가장 중요한 모든 차트와 테이블을 결합하기 위해 결과를 대시보드에 고정할 수 있습니다.

* [Azure 공유 대시보드](app-insights-dashboards.md#share-dashboards): 고정 아이콘을 클릭합니다. 이렇게 하려면 공유 대시보드가 있어야 합니다. Azure Portal에서 대시보드를 열거나 만들고 공유를 클릭합니다.
* [Power BI 대시보드](app-insights-export-power-bi.md): 내보내기, Power BI 쿼리를 클릭합니다. 이 대체의 장점은 광범위한 원본의 다른 결과와 함께 쿼리를 표시할 수 있다는 것입니다.

## <a name="combine-with-imported-data"></a>가져온 데이터와 결합

분석 보고서는 대시보드에서 보기 좋게 표시되지만 좀 더 이해하기 쉬운 형태로 데이터를 변환하고 싶을 수 있습니다. 예를 들어 인증된 사용자는 원격 분석에서 별칭으로 식별됩니다. 결과에 실제 이름을 표시하고 싶을 수 있습니다. 이렇게 하려면 별칭을 실제 이름에 매핑하는 CSV 파일이 필요합니다.

데이터 파일을 가져오고 표준 테이블(요청, 예외 등)처럼 사용할 수 있습니다. 자체적으로 쿼리하거나 다른 테이블과 조인합니다. 예를 들어 usermap이라는 테이블이 있고 `realName` 및 `userId` 열이 있으면 요청 원격 분석에서 `user_AuthenticatedId` 필드를 변환하는 데 사용할 수 있습니다.

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

테이블을 가져오려면 스키마 블레이드의 **기타 데이터 원본** 아래에서 지침에 따라 데이터 샘플을 업로드하여 새 데이터 원본을 추가합니다. 그런 다음 이 정의를 사용하여 테이블을 업로드할 수 있습니다.

가져오기 기능은 현재 미리 보기 상태이므로 처음에 "기타 데이터 원본" 아래 "문의처" 링크가 표시됩니다. 이 링크를 사용하여 미리 보기 프로그램에 등록하고 나면 링크가 "새 데이터 원본 추가" 단추로 바뀝니다.


## <a name="tables"></a>테이블
앱에서 수신하는 원격 분석의 스트림이 여러 테이블을 통해 액세스할 수 있습니다. 각 테이블에 대해 사용할 수 있는 속성의 스키마는 창의 왼쪽에 표시 됩니다.

### <a name="requests-table"></a>요청 테이블
웹 앱 및 페이지 이름별 세그먼트에 대 한 HTTP 요청 수 계산:

![이름별로 분할된 요청 수 계산](./media/app-insights-analytics-tour/analytics-count-requests.png)

가장 많이 실패하는 요청 찾기:

![이름별로 분할된 요청 수 계산](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>사용자 지정 이벤트 테이블
사용자 고유의 이벤트를 보내기 위해 [Trackevent()](app-insights-api-custom-events-metrics.md#trackevent)를 사용하면 이 테이블에서 이벤트를 읽을 수 있습니다.

응용 프로그램 코드에 이러한 줄을 포함하는 사례:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

이러한 이벤트의 빈도 표시:

![사용자 지정 이벤트의 표시 속도](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

측정값 및 차원을 이벤트에서 추출:

![사용자 지정 이벤트의 표시 속도](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>사용자 지정 메트릭 테이블
사용자 지정 메트릭 값을 보내기 위해 [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric)을 사용하면 **customMetrics** 스트림에 결과가 나타납니다. 예:   

![Application Insights 분석의 사용자 지정 메트릭](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> [메트릭 탐색기](app-insights-metrics-explorer.md)에서 원격 분석의 모든 형식에 연결된 모든 사용자 지정 측정값은 `TrackMetric()`을(를) 사용하여 전송되는 메트릭과 함께 메트릭 블레이드에 표시됩니다. 하지만 분석에서 사용자 지정 측정은 이벤트, 요청 등 수행된 형식의 원격 분석에 연결되고 TrackMetric에 의해 전송된 메트릭은 해당 스트림에 표시됩니다.
>
>

### <a name="performance-counters-table"></a>성능 카운터 테이블
[성능 카운터](app-insights-performance-counters.md)는 CPU, 메모리, 네트워크 사용률 등 앱에 대한 기본 시스템 메트릭을 보여 줍니다. 사용자 지정 카운터를 포함하여 추가 카운터를 보내도록 SDK를 구성할 수 있습니다.

**performanceCounters** 스키마는 `category`, `counter` 이름 및 각 성능 카운터의 `instance` 이름을 노출합니다. 카운터 인스턴스 이름은 일부 성능 카운터에만 적용할 수 있으며 일반적으로 개수와 관련된 프로세스의 이름을 나타냅니다. 각 응용 프로그램에 대한 원격 분석 데이터에는 해당 응용 프로그램에 대한 카운터에만 표시됩니다. 예를 들면 어떤 카운터를 사용할 수 있는지 알아보기:

![Application Insights 분석의 성능 카운터](./media/app-insights-analytics-tour/analytics-performance-counters.png)

선택한 기간 동안 사용 가능한 메모리의 차트를 가져오려면:

![Application Insights 분석의 메모리 시간 차트](./media/app-insights-analytics-tour/analytics-available-memory.png)

다른 원격 분석과 마찬가지로, **performanceCounters**에도 앱이 실행되는 호스트 컴퓨터의 id를 나타내는 열 `cloud_RoleInstance`이 있습니다. 예를 들어, 서로 다른 컴퓨터에서 앱의 성능을 비교하려면:

![Application Insights 분석에서 역할 인스턴스에 의해 분할된 성능](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>예외 테이블
[앱에서 보고된 예외](app-insights-asp-net-exceptions.md)는 이 테이블에서 사용할 수 있습니다.

예외가 발생하는 경우 앱이 처리하는 HTTP 요청을 찾으려면 operation_Id에 참여합니다.

![Operation_Id에 요청을 사용하여 예외를 조인](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>브라우저 타이밍 테이블
`browserTimings`은 사용자의 브라우저에서 수집된 페이지 로드 데이터를 보여 줍니다.

이러한 메트릭을 보려면 [클라이언트쪽 원격 분석을 위해 앱을 설정](app-insights-javascript.md)합니다.

스키마에 [페이지 로드 프로세스의 여러 단계의 길이를 나타내는 메트릭](app-insights-javascript.md#page-load-performance)이 포함됩니다. (사용자가 한 페이지를 읽는 시간의 길이 나타내지 않습니다.)  

서로 다른 페이지의 popularities 표시하고 각 페이지에 대한 시간을 로드:

![Analytics에서 페이지 로드 시간](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>가용성 결과 테이블
`availabilityResults`은 [웹 테스트](app-insights-monitor-web-app-availability.md) 결과를 표시합니다. 각 테스트 위치에서 테스트가 실행될 때마다 개별적으로 보고됩니다.

![Analytics에서 페이지 로드 시간](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>종속성 테이블
앱이 데이터베이스 및 REST Api에 대해 수행한 호출과 TrackDependency()에 대한 다른 호출의 결과가 포함됩니다. 또한 브라우저에서 생성된 AJAX 호출을 포함합니다.

브라우저의 AJAX 호출:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

서버의 종속성 호출:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Application Insights 에이전트가 설치되지 않은 경우 서버 쪽 종속성 결과에는 항상 `success==False`가 표시됩니다. 그러나 다른 데이터는 올바릅니다.

### <a name="traces-table"></a>추적 테이블
Tracktrace()를 사용하여 앱에서 보낸 원격 분석 데이터나 [다른 로깅 프레임워크](app-insights-asp-net-trace-logs.md)가 포함됩니다.

## <a name="video"></a>비디오 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

고급 쿼리:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>다음 단계
* [분석 언어 참조](app-insights-analytics-reference.md)
* [SQL 사용자 치트 시트](https://aka.ms/sql-analytics)에서는 가장 일반적인 코드를 변환합니다.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
