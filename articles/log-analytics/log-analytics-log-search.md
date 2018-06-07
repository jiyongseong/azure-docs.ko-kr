---
title: Azure Log Analytics의 로그 검색 | Microsoft Docs
description: 로그 검색을 통해 Log Analytics의 모든 데이터를 검색해야 합니다.  이 문서는 Log Analytics에서 새 로그 검색이 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다.
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
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="understanding-log-searches-in-log-analytics"></a>Log Analytics의 로그 검색 이해

로그 검색을 통해 Log Analytics의 모든 데이터를 검색해야 합니다.  포털에서 데이터를 분석하는 경우, 특정 조건에 대해 알리도록 경고 규칙을 구성하는 경우 또는 Log Analytics API를 사용하여 데이터를 검색하는 경우 모두는 원하는 데이터를 지정하려면 로그 검색을 사용하게 됩니다.  이 문서는 Log Analytics에서 로그 검색이 어떻게 사용되는지를 설명하고 새로 만들기 전에 이해해야 하는 개념을 제공합니다. 로그 검색 만들기 및 편집에 대한 자세한 내용 및 쿼리 언어에 대 한 참조는 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="where-log-searches-are-used"></a>로그 검색이 사용되는 위치

Log Analytics에서 로그 검색을 사용하게 되는 다양한 방법은 다음과 같습니다.

- **포털.** Azure Portal 또는 [고급 분석 포털](https://go.microsoft.com/fwlink/?linkid=856587)에서 리포지토리의 데이터에 대한 대화형 분석을 수행할 수 있습니다.  그러면 다양한 형식 및 시각화로 쿼리를 편집하고 결과를 분석할 수 있습니다.  사용자가 만든 대부분의 쿼리는 포털 중 하나에서 시작된 다음, 예상대로 작동하는지 확인한 후 복사됩니다.
- **경고 규칙.** [경고 규칙](log-analytics-alerts.md)은 작업 영역에서 데이터의 문제를 사전에 식별합니다.  각 경고 규칙은 일정한 간격으로 자동으로 실행되는 로그 검색을 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- **뷰.**  [뷰 디자이너](log-analytics-view-designer.md)를 통해 사용자 대시보드에 포함될 데이터의 시각화를 만들 수 있습니다.  로그 검색은 각 보기에 [타일](log-analytics-view-designer-tiles.md) 및 [시각화 부분](log-analytics-view-designer-parts.md)에서 사용하는 데이터를 제공합니다.  시각화 부분에서 로그 검색 페이지로 드릴 다운하여 데이터에 대한 추가 분석을 수행할 수 있습니다.
- **내보내기.**  데이터를 Log Analytics 작업 영역에서 Excel 또는 [Power BI](log-analytics-powerbi.md)로 내보내는 경우 내보낼 데이터를 정의하는 로그 검색을 만듭니다.
- **PowerShell.** [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0)를 사용하는 명령줄 또는 Azure Automation Runbook에서 PowerShell 스크립트를 실행하여 Log Analytics에서 데이터를 검색합니다.  이 cmdlet에는 검색할 데이터를 결정하는 쿼리가 필요합니다.
- **Log Analytics API.**  모든 REST API 클라이언트는 [Log Analytics 로그 검색 API](log-analytics-log-search-api.md)를 통해 작업 영역에서 데이터를 검색할 수 있습니다.  API 요청에는 검색할 데이터를 확인하기 위해 Log Analytics에 대해 실행되는 쿼리가 포함됩니다.

![로그 검색](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics 데이터 구성 방법
쿼리를 작성하는 경우 원하는 데이터가 있는 테이블이 무엇인지 확인하는 것으로 시작합니다. 각 [데이터 원본](log-analytics-data-sources.md) 및 [솔루션](../operations-management-suite/operations-management-suite-solutions.md)은 해당 데이터를 Log Analytics 작업 영역의 전용 테이블에 저장합니다.  각 데이터 원본 및 솔루션에 대한 설명서는 생성된 데이터 형식의 이름 및 각 속성에 대한 설명을 포함합니다.  많은 쿼리의 경우 단일 테이블의 데이터만 필요하지만, 여러 테이블의 데이터를 포함하는 다양한 옵션이 사용되는 경우도 있습니다.

![테이블](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>쿼리 작성
Log Analytics의 로그 분석에서 핵심은 여러 방법으로 리포지토리의 데이터를 검색 및 분석할 수 있게 해 주는 [광범위한 쿼리 언어](https://docs.loganalytics.io/)입니다.  이 동일한 쿼리 언어는 [Application Insights](../application-insights/app-insights-analytics.md)에 사용됩니다.  쿼리를 작성하는 방법을 배우는 것은 Log Analytics에서 로그 분석을 만드는 데 중요합니다.  일반적으로 기본 쿼리로 시작하고, 요구 사항이 복잡해지면 더 많은 고급 기능을 사용하게 됩니다.

쿼리의 기본 구조는 원본 테이블이며 그 뒤에 파이프 문자 `|`로 구분된 일련의 연산자가 있습니다.  여러 연산자와 함께 연결하여 데이터를 구체화하고 고급 기능을 수행할 수 있습니다.

예를 들어, 과거 오류 이벤트가 가장 많은 상위 10개 컴퓨터를 찾는다고 가정합니다.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

또는 마지막 날에 하트비트를 보유하지 않은 컴퓨터를 찾을 수도 있습니다.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

지난 주 각 컴퓨터의 프로세서 사용률과 관련된 꺾은선형 차트는 어떨까요?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

이러한 간단한 샘플 쿼리에서, 작업하는 데이터의 종류에 관계 없이 구조는 비슷하다는 점을 알 수 있습니다.  한 명령의 결과 데이터가 파이프라인을 통해 다음 명령에 전송되는 별개의 단계로 구분할 수 있습니다.

또한 구독 내에서 Log Analytics 작업 영역 전반에 걸쳐 데이터를 쿼리할 수도 있습니다.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


자습서 및 언어 참조를 포함한 Azure Log Analytics 쿼리 언어에 관한 전체 설명서는 [Azure Log Analytics 쿼리 언어 설명서](https://docs.loganalytics.io/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [로그 검색을 만들고 편집하는 데 사용하는 포털](log-analytics-log-search-portals.md)에 대해 알아보세요.
- 새로운 쿼리 언어를 사용한 [쿼리 작성 자습서](log-analytics-tutorial-viewdata.md)를 확인해 보세요.
