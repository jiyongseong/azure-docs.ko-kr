---
title: Azure Log Analytics에서 로그 검색 포털 사용 | Microsoft 문서
description: 이 문서에는 로그 검색 포털을 사용하여 Log Analytics 작업 영역에 저장된 데이터를 분석하고 로그 검색을 만드는 방법을 설명하는 자습서가 포함되어 있습니다.  이 자습서에는 몇 가지 간단한 쿼리를 실행하여 여러 형식의 데이터를 반환하고 결과를 분석하는 방법이 나와 있습니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 37213012e817f0fae21a47a4334a519bbbca206b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>로그 검색 포털을 사용하여 Azure Log Analytics에서 로그 검색 만들기

> [!NOTE]
> 이 문서에서는 새로운 쿼리 언어를 사용하는 Azure Log Analytics의 로그 검색 포털에 대해 설명합니다.  [Azure Log Analytics 작업 영역을 새 로그 검색으로 업그레이드](log-analytics-log-search-upgrade.md)에서 새 언어에 대해 자세히 알아보고 작업 영역을 업그레이드하는 절차를 확인할 수 있습니다.  
>
> 작업 영역을 새로운 쿼리 언어로 업그레이드하지 않은 경우에는 [Log Analytics에서 로그 검색을 사용하여 데이터 찾기](log-analytics-log-searches.md)를 참조하여 로그 검색 포털의 현재 버전에 대한 정보를 확인해야 합니다.

이 문서에는 로그 검색 포털을 사용하여 Log Analytics 작업 영역에 저장된 데이터를 분석하고 로그 검색을 만드는 방법을 설명하는 자습서가 포함되어 있습니다.  이 자습서에는 몇 가지 간단한 쿼리를 실행하여 여러 형식의 데이터를 반환하고 결과를 분석하는 방법이 나와 있습니다.  여기서는 쿼리를 직접 수정하기보다는 쿼리를 수정하는 로그 검색 포털의 기능에 대해 중점적으로 설명합니다.  쿼리를 직접 편집하는 방법에 대한 자세한 내용은 [쿼리 언어 참조](https://go.microsoft.com/fwlink/?linkid=856079)를 참조하세요.

로그 검색 포털 대신 고급 분석 포털에서 검색을 만들려면 [분석 포털 시작](https://go.microsoft.com/fwlink/?linkid=856587)을 참조하세요.  이 두 포털은 모두 같은 쿼리 언어를 사용하여 Log Analytics 작업 영역의 동일 데이터에 액세스합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서에서는 분석할 쿼리용 데이터를 생성하는 원본이 하나 이상 연결된 Log Analytics 작업 영역이 이미 있다고 가정합니다.  

- 작업 영역이 없는 경우 [Log Analytics 작업 영역 시작](log-analytics-get-started.md)의 절차에 따라 무료로 만들 수 있습니다.
- [Windows 에이전트](log-analytics-windows-agent.md) 하나 이상 또는 [Linux 에이전트](log-analytics-linux-agents.md) 하나를 작업 영역에 연결합니다.  

## <a name="open-the-log-search-portal"></a>로그 검색 포털 열기
로그 검색 포털을 열어서 시작합니다. 

1. Azure Portal을 엽니다.
2. Log Analytics로 이동한 후 작업 영역을 선택합니다.
3. **로그 검색**을 선택합니다.

![로그 검색 단추](media/log-analytics-log-search-log-search-portal/log-search-button.png)

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

![단순 쿼리](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

각 레코드의 처음 몇 개 속성만 표시됩니다.  특정 레코드의 모든 속성을 표시하려면 **자세히 표시**를 클릭합니다.

![레코드 세부 정보](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>시간 범위 설정
Log Analytics에 의해 수집되는 모든 레코드에는 레코드가 작성된 날짜와 시간을 포함하는 **TimeGenerated** 속성이 있습니다.  로그 검색 포털의 쿼리는 **TimeGenerated**가 화면 왼쪽에 표시된 시간 범위 내에 포함되는 레코드만 반환합니다.  

드롭다운을 선택하거나 슬라이더를 수정하여 시간 필터를 변경할 수 있습니다.  슬라이더에는 범위 내 각 시간 세그먼트의 상대 레코드 수를 보여 주는 막대 그래프가 표시됩니다.  이 세그먼트는 범위에 따라 달라집니다.

기본 시간 범위는 **1일**입니다.  이 값을 **7일**로 변경하면 총 레코드 수가 증가합니다.

![날짜 시간 범위](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>쿼리 결과 필터링
화면 왼쪽에 있는 필터 창에서는 쿼리를 직접 수정하지 않고 쿼리에 필터링을 추가할 수 있습니다.  이 창에는 반환된 레코드의 여러 속성이 표시되며, 상위 10개 값과 레코드 수도 함께 표시됩니다.

**Event**를 사용 중이라면 **EVENTLEVELNAME** 아래의 **error** 옆에 있는 확인란을 선택합니다.   **Syslog**를 사용 중이라면 **SEVERITYLEVEL** 아래의 **err** 옆에 있는 확인란을 선택합니다.  그러면 쿼리가 다음 중 하나로 변경되어 결과 범위가 오류 이벤트로 제한됩니다.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filter](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

레코드 중 하나의 속성 메뉴에서 **필터에 추가**를 선택하여 필터 창에 속성을 추가합니다.

![필터에 추가 메뉴](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

필터링할 값이 포함된 레코드의 속성 메뉴에서 **필터**를 선택하여 같은 필터를 설정할 수 있습니다.  

이름이 파란색인 속성의 경우에는 **필터** 옵션만 표시됩니다.  이러한 필드는 검색 조건에 대해 인덱싱되는 *검색 가능* 필드입니다.  회색 필드는 **참조 표시** 옵션만 포함하는 *자유 텍스트 검색 가능* 필드입니다.  이 옵션은 속성에 해당 값이 포함된 레코드를 반환합니다.

![필터 메뉴](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

레코드 메뉴의 **필터링 기준** 옵션을 선택하면 단일 속성에 대한 결과를 그룹화할 수 있습니다.  이렇게 하면 차트에 결과를 표시하는 [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 연산자가 쿼리에 추가됩니다.  둘 이상의 속성을 그룹화할 수는 있지만 이렇게 하려면 쿼리를 직접 편집해야 합니다.  **Computer** 속성 옆의 레코드 메뉴를 선택하고 **’컴퓨터’별로 그룹화**를 선택합니다.  

![컴퓨터를 기준으로 그룹화](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>결과 작업
로그 검색 포털에는 쿼리 결과를 사용하기 위한 여러 가지 기능이 있습니다.  실제 쿼리는 수정하지 않고 결과를 정렬, 필터링 및 그룹화하여 데이터를 분석할 수 있습니다.  기본적으로 쿼리 결과는 정렬되지 않습니다.

필터링 및 정렬을 위한 추가 옵션을 제공하는 테이블 형식으로 데이터를 표시하려면 **테이블**을 클릭합니다.  

![테이블 보기](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

레코드의 세부 정보를 보려면 해당 레코드 옆의 화살표를 클릭합니다.

![결과 정렬](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

필드의 열 머리글을 클릭하여 필드를 정렬할 수 있습니다.

![결과 정렬](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

필터 단추를 클릭하고 필터 조건을 입력하면 열의 특정 값을 기준으로 결과를 필터링할 수 있습니다.

![결과 필터링](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

열 머리글을 결과 위쪽으로 끌면 해당 열을 기준으로 그룹화할 수 있습니다.  여러 열을 위쪽으로 끌면 여러 필드를 기준으로 그룹화할 수 있습니다.

![결과 그룹화](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>성능 데이터 사용
Windows 및 Linux 에이전트에 대한 성능 데이터는 Log Analytics 작업 영역의 **Perf** 테이블에 저장됩니다.  성능 레코드는 다른 레코드와 비슷하며, 이벤트와 마찬가지로 모든 성능 레코드를 반환하는 단순 쿼리를 작성할 수 있습니다.

```
Perf
```

![성능 데이터](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

모든 성능 개체와 카운터의 레코드 수백만 개가 반환된다면 원하는 데이터를 찾기가 어렵습니다.  따라서 앞에서 사용했던 것과 같은 방법으로 데이터를 필터링하거나, 로그 검색 상자에 다음 쿼리를 직접 입력할 수 있습니다.  이 경우 Windows 및 Linux 컴퓨터의 프로세서 사용률 레코드만 반환됩니다.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![프로세서 사용률](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

이렇게 하면 데이터가 특정 카운터로 제한되기는 하지만 아직은 특별히 유용하다고 할 수 없습니다.  데이터를 꺾은선형 차트에 표시할 수도 있는데, 그러려면 먼저 Computer 및 TimeGenereated를 기준으로 데이터를 그룹화해야 합니다.  여러 필드를 기준으로 그룹화하려면 쿼리를 직접 수정해야 하므로, 여기서는 다음과 같이 쿼리를 수정하겠습니다.  이 쿼리는 **CounterValue** 속성에서 [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 함수를 사용하여 매 시간마다 평균 값을 계산합니다.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![성능 데이터 차트](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

이제 데이터를 적절하게 그룹화했으므로 [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 연산자를 추가해 시각적 차트에 데이터를 표시할 수 있습니다.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![꺾은선형 차트](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>다음 단계

- [분석 포털 시작](https://go.microsoft.com/fwlink/?linkid=856079)에서 Log Analytics 쿼리 언어에 대해 자세히 알아봅니다.
- 로그 검색 포털과 같은 데이터에 액세스하고 같은 쿼리를 실행할 수 있는 [고급 분석 포털](https://go.microsoft.com/fwlink/?linkid=856587) 사용 방법을 안내하는 자습서를 진행합니다.
