---
title: "Azure Time Series Insights 사용자 지정 보기를 매개 변수가 있는 URL과 공유 | Microsoft Docs"
description: "이 문서에서는 고객 보기를 쉽게 공유할 수 있도록 Azure Time Series Insights에서 매개 변수가 있는 URL을 개발하는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: ffa8e96ab9a5344c924400fe55b4d1e6aee95f06
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>매개 변수가 있는 URL을 사용하여 사용자 지정 보기 공유

Time Series Insights 탐색기에서 사용자 지정 보기를 공유하려면 사용자 지정 보기의 매개 변수가 있는 URL을 프로그래밍 방식으로 만들면 됩니다.

Time Series Insights 탐색기는 URL에서 바로 환경의 보기를 지정할 수 있는 URL 쿼리 매개 변수를 지원합니다.  예를 들어 URL만 사용하여 대상 환경, 검색 조건자 및 원하는 시간 범위를 지정할 수 있습니다. 사용자가 사용자 지정 URL을 클릭하면 인터페이스는 Time Series Insights 포털의 해당 자산과 직접 연결되는 링크를 제공합니다.  데이터 액세스 정책이 적용됩니다. 

## <a name="environment-id"></a>환경 ID

`environmentId=<guid>` 매개 변수는 대상 환경 ID를 지정합니다.  데이터 액세스 FQDN의 구성 요소이며, Azure Portal 환경 개요의 오른쪽 위 모서리에서 찾을 수 있습니다.  `env.timeseries.azure.com` 앞에 있는 모든 것이 환경 ID입니다. 예를 들어 `?environmentId=10000000-0000-0000-0000-100000000108`은 환경 ID 매개 변수입니다.

## <a name="time"></a>Time

매개 변수가 있는 URL을 사용하여 절대 또는 상대 시간 값을 지정할 수 있습니다.

### <a name="absolute-time-values"></a>절대 시간 값

절대 시간 값의 경우 `from=<integer>` 및 `to=<integer>` 매개 변수를 사용합니다. 

`from=<integer>`는 검색 범위에 대한 시작 시간의 JavaScript 밀리초 값입니다.

`to=<integer>`는 검색 범위에 대한 종료 시간의 JavaScript 밀리초 값입니다. 

날짜에 대한 JavaScript 밀리초를 확인하는 방법은 [Epoch & Unix 타임스탬프 변환기](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)를 참조하세요.

### <a name="relative-time-values"></a>상대 시간 값

상대 시간 값의 경우 `relativeMillis=<value>`를 사용합니다. 여기서 *value*는 백 엔드에서 가장 최신 데이터의 JavaScript 밀리초입니다.

예를 들어 `&relativeMillis=3600000`은 최근 60분의 데이터를 표시합니다.

허용되는 값은 Time Series Insights 탐색기 **빠른 시간** 메뉴와 일치하며 다음을 포함합니다.

- 1800000(최근 30분)
- 3600000(최근 60분)
- 10800000(최근 3시간)
- 21600000(최근 6시간)
- 43200000(최근 12시간)
- 86400000(최근 24시간)
- 604800000(최근 7일)
- 2592000000(최근 30시간)

### <a name="optional-parameters"></a>선택적 매개 변수

`timeSeriesDefinitions=<collection of term objects>` 매개 변수는 Time Series Insights 보기의 조건을 지정하며, 여기서 각 항목은 다음과 같습니다.

- "name":"<string>"
  - *조건*의 이름입니다.
- "splitBy":"<string>"
  - *분할의 기준*이 되는 열 이름입니다.
- "measureName":"<string>"
  - *측정값*의 열 이름입니다.
- "predicate":"<string>"
  - 서버 쪽 필터링에 대한 *where* 절입니다.
-  "useSum":"true"
  - 이는 측정값에 대한 합계를 사용하도록 지정하는 선택적 매개 변수입니다.  "이벤트"가 선택된 측정값이면 count가 기본적으로 선택됩니다.  "이벤트"가 선택되지 않으면 average가 기본적으로 선택됩니다.  

'multiChartStack=<true/false>' 매개 변수를 사용하면 차트에 스택이 가능하고 'multiChartSameScale=<true/false>' 매개 변수를 사용하면 선택적 매개 변수 내에서 용어 전체에 동일한 Y 축 눈금을 사용할 수 있습니다.  

- 'multiChartStack=false'
  - 'True'가 기본적으로 사용되기 때문에 스택에 'false'를 전달합니다.
- 'multiChartStack=false&multiChartSameScale=true' 
  - 용어 전체에서 동일한 Y-축 눈금 사용하려면 스택을 사용하도록 설정해야 합니다.  기본적으로 'false'이므로 'true'를 전달하면 이 기능을 사용할 수 있습니다.  
  
'timeBucketUnit=<Unit>&timeBucketSize=<integer>'를 사용하면 보다 세밀하거나 매끄럽고 더 많이 집계된 차트 보기를 제공하도록 간격 슬라이더를 조정할 수 있습니다.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - 단위 = 일, 시간, 분, 초, 밀리초.  단위는 항상 대문자로 시작합니다.
  - timeBucketSize에 원하는 정수를 전달하여 단위의 수를 정의합니다.  최대 7일까지 설정할 수 있습니다.  
  
'timezoneOffset=<integer>' 매개 변수를 사용하면 UTC에 대한 오프셋으로 차트가 표시될 표준 시간대를 설정할 수 있습니다.  
  - 'timezoneOffset=-<integer>'
    - 정수는 항상 밀리초 단위입니다.  
    - 이 기능은 로컬(브라우저 시간) 또는 UTC를 선택할 수 있는 TSI 탐색기에서 사용할 수 있는 기능과 약간 다릅니다.  
 
### <a name="examples"></a>예

예를 들어 시계열 정의를 URL 매개 변수로 추가하려면 다음 명령을 사용하면 됩니다.

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

다음 항목에 대한 예제 시계열 정의를 사용하면 

- 환경 ID
- 최근 60분의 데이터
- 선택적 매개 변수를 구성하는 조건(F1PressureID, F2TempStation 및 F3VibrationPL)
 
보기에 대해 매개 변수가 있는 다음 URL을 생성할 수 있습니다.

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Time Series Insights 탐색기를 사용하여 위의 URL이 설명하는 보기를 빌드한 경우 다음과 같이 표시됩니다.

![Time Series Insights 탐색기 조건](media/parameterized-url/url1.png)

전체 보기(차트 포함)는 다음과 같이 표시됩니다.

![차트 보기](media/parameterized-url/url2.png)

## <a name="next-steps"></a>다음 단계
[C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)
