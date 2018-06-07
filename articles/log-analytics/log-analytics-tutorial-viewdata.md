---
title: 수집된 Azure Log Analytics 데이터 보기 또는 분석 | Microsoft Docs
description: 이 문서에는 로그 검색 포털을 사용하여 Log Analytics 리소스에 저장된 데이터를 분석하고 로그 검색을 만드는 방법을 설명하는 자습서가 포함되어 있습니다.  이 자습서에는 몇 가지 간단한 쿼리를 실행하여 여러 형식의 데이터를 반환하고 결과를 분석하는 방법이 나와 있습니다.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 6345fe89a3bf25041621213274ea0c3081848d99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Log Analytics 로그 검색을 사용하여 수집한 데이터를 보고 분석합니다.

Log Analytics에서 수집된 데이터를 분석하는 쿼리를 생성하여 로그 검색을 활용하고, 가장 귀중한 검색의 그래픽 보기로 사용자 지정할 수 있는 기존 대시보드를 사용할 수 있습니다.  Azure VM 및 활동 로그에서의 작업 데이터 수집을 정의했으므로 이 자습서에서는 이제 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 이벤트 데이터의 단순 검색을 수행하고, 결과를 수정 및 필터링하는 기능 사용 
> * 성능 데이터를 사용하는 방법 알아보기

이 자습서의 예제를 완료하려면 [Log Analytics 작업 영역에 연결된](log-analytics-quick-collect-azurevm.md) 기존 가상 머신이 있어야 합니다.  

반환된 데이터와 대화형으로 작업하는 것 외에도 쿼리 만들기 및 편집을 두 가지 방법 중 하나로 수행할 수 있습니다.  기본 쿼리의 경우 Azure Portal의 로그 검색 페이지를 사용하고 고급 쿼리의 경우 고급 분석 포털을 사용할 수 있습니다. 두 포털 간의 기능상 차이점에 대해 자세히 알아보려면 [Azure Log Analytics에서 로그 쿼리를 만들고 편집하기 위한 포털](log-analytics-log-search-portals.md)을 참조하세요.

이 자습서에서는 Azure Portal의 로그 검색을 사용합니다. 

## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다. 

## <a name="open-the-log-search-portal"></a>로그 검색 포털 열기 
로그 검색 포털을 열어서 시작합니다.   

1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 구독 창에서 작업 영역을 선택한 다음 **로그 검색** 타일을 선택합니다.<br><br> ![로그 검색 단추](media/log-analytics-tutorial-viewdata/azure-portal-02.png)

## <a name="create-a-simple-search"></a>단순 검색 만들기
사용할 데이터를 가장 빠르게 검색할 수 있는 방법은 테이블의 모든 레코드를 반환하는 단순 쿼리를 사용하는 것입니다.  Windows 또는 Linux 클라이언트가 작업 영역에 연결되어 있으면 Event(Windows) 또는 Syslog(Linux) 테이블에 데이터가 포함되어 있습니다.

다음 쿼리 중 하나를 검색 상자에 입력하고 검색 단추를 클릭합니다.  

```
Event
```
```
Syslog
```

기본 목록 보기에 데이터가 반환되며, 반환된 총 레코드 수를 확인할 수 있습니다.

![단순 쿼리](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

각 레코드의 처음 몇 개 속성만 표시됩니다.  특정 레코드의 모든 속성을 표시하려면 **자세히 표시**를 클릭합니다.

## <a name="filter-results-of-the-query"></a>쿼리 결과 필터링
화면 왼쪽에 있는 필터 창에서는 쿼리를 직접 수정하지 않고 쿼리에 필터링을 추가할 수 있습니다.  해당 레코드 종류에 대해 몇 가지 레코드 속성이 표시되며, 하나 이상의 속성 값을 선택하여 검색 결과를 좁힐 수 있습니다.

**Event**를 사용 중이라면 **EVENTLEVELNAME** 아래의 **error** 옆에 있는 확인란을 선택합니다.   **Syslog**를 사용 중이라면 **SEVERITYLEVEL** 아래의 **err** 옆에 있는 확인란을 선택합니다.  그러면 쿼리가 다음 중 하나로 변경되어 결과 범위가 오류 이벤트로 제한됩니다.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

레코드 중 하나의 속성 메뉴에서 **필터에 추가**를 선택하여 필터 창에 속성을 추가합니다.

![필터에 추가 메뉴](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

필터링할 값이 포함된 레코드의 속성 메뉴에서 **필터**를 선택하여 같은 필터를 설정할 수 있습니다.  

이름이 파란색인 속성의 경우에는 마우스를 위로 가져가면 **필터** 옵션만 표시됩니다.  이러한 필드는 검색 조건에 대해 인덱싱되는 *검색 가능* 필드입니다.  회색 필드는 **참조 표시** 옵션만 포함하는 *자유 텍스트 검색 가능* 필드입니다.  이 옵션은 속성에 해당 값이 포함된 레코드를 반환합니다.

레코드 메뉴의 **필터링 기준** 옵션을 선택하면 단일 속성에 대한 결과를 그룹화할 수 있습니다.  이렇게 하면 차트에 결과를 표시하는 [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 연산자가 쿼리에 추가됩니다.  둘 이상의 속성을 그룹화할 수는 있지만 이렇게 하려면 쿼리를 직접 편집해야 합니다.  **Computer** 속성 옆의 레코드 메뉴를 선택하고 **묶는 방법 'Computer'**를 선택합니다.  

![컴퓨터를 기준으로 그룹화](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>결과 작업
로그 검색 포털에는 쿼리 결과를 사용하기 위한 여러 가지 기능이 있습니다.  실제 쿼리는 수정하지 않고 결과를 정렬, 필터링 및 그룹화하여 데이터를 분석할 수 있습니다.  기본적으로 쿼리 결과는 정렬되지 않습니다.

필터링 및 정렬을 위한 추가 옵션을 제공하는 테이블 형식으로 데이터를 표시하려면 **테이블**을 클릭합니다.  

![테이블 보기](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

레코드의 세부 정보를 보려면 해당 레코드 옆의 화살표를 클릭합니다.

![결과 정렬](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

필드의 열 머리글을 클릭하여 필드를 정렬할 수 있습니다.

![결과 정렬](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

필터 단추를 클릭하고 필터 조건을 입력하면 열의 특정 값을 기준으로 결과를 필터링할 수 있습니다.

![결과 필터링](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

열 머리글을 결과 위쪽으로 끌면 해당 열을 기준으로 그룹화할 수 있습니다.  여러 열을 위쪽으로 끌면 여러 필드를 기준으로 그룹화할 수 있습니다.

![결과 그룹화](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>성능 데이터 사용
Windows 및 Linux 에이전트에 대한 성능 데이터는 Log Analytics 작업 영역의 **Perf** 테이블에 저장됩니다.  성능 레코드는 다른 레코드와 비슷하며, 이벤트와 마찬가지로 모든 성능 레코드를 반환하는 단순 쿼리를 작성할 수 있습니다.

```
Perf
```

![성능 데이터](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

모든 성능 개체와 카운터의 레코드 수백만 개가 반환된다면 원하는 데이터를 찾기가 어렵습니다.  따라서 앞에서 사용했던 것과 같은 방법으로 데이터를 필터링하거나, 로그 검색 상자에 다음 쿼리를 직접 입력할 수 있습니다.  이 경우 Windows 및 Linux 컴퓨터의 프로세서 사용률 레코드만 반환됩니다.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![프로세서 사용률](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

이렇게 하면 데이터가 특정 카운터로 제한되기는 하지만 아직은 특별히 유용하다고 할 수 없습니다.  데이터를 꺾은선형 차트에 표시할 수도 있는데, 그러려면 먼저 Computer 및 TimeGenereated를 기준으로 데이터를 그룹화해야 합니다.  여러 필드를 기준으로 그룹화하려면 쿼리를 직접 수정해야 하므로, 여기서는 다음과 같이 쿼리를 수정하겠습니다.  이 쿼리는 **CounterValue** 속성에서 [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 함수를 사용하여 매 시간마다 평균 값을 계산합니다.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![성능 데이터 차트](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

이제 데이터를 적절하게 그룹화했으므로 [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 연산자를 추가해 시각적 차트에 데이터를 표시할 수 있습니다.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![꺾은선형 차트](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 이벤트 및 성능 데이터를 분석하는 기본 로그 검색을 만드는 방법을 배웠습니다.  대시보드를 만들어 데이터를 시각화하는 방법에 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [Log Analytics 대시보드 만들기 및 공유](log-analytics-tutorial-dashboards.md)