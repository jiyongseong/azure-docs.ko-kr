---
title: "Log Analytics 새 로그 검색 질문과 대답 | Microsoft 문서"
description: "이 문서에서는 Log Analytics를 새 쿼리 언어로 업그레이드하는 과정과 관련된 질문과 대답을 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Log Analytics 새 로그 검색 FAQ 및 알려진 문제

이 문서에는 [새 쿼리 언어로의 Log Analytics](log-analytics-log-search-upgrade.md) 업그레이드와 관련한 질문과 대답 및 알려진 문제가 포함되어 있습니다.  작업 영역을 업그레이드하도록 결정하기 전에 이 전체 문서를 읽어야 합니다.


## <a name="alerts"></a>Alerts

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>질문: 경고 규칙이 매우 많습니다. 업그레이드 후에 새 언어로 규칙을 다시 만들어야 하나요?  
아니요, 경고 규칙은 업그레이드 중에 새 검색 언어로 자동 변환됩니다.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>질문: 웹후크 및 Runbook 작업에 대한 경고 규칙이 있습니다. 업그레이드 시에도 계속 작동할까요?

아니요, 웹후크 및 Runbook 작업에 페이로드 처리 방법을 변경해야 하는 몇 가지 변경 사항이 있습니다. 다양한 출력 형식을 표준화하고 페이로드 크기를 줄이기 위해 이러한 변경 작업을 수행했습니다. 이러한 형식에 대한 자세한 내용은 [Log Analytics에서 경고 규칙에 작업 추가](log-analytics-alerts-actions.md)에 있습니다.


## <a name="computer-groups"></a>컴퓨터 그룹

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>질문: 컴퓨터 그룹을 사용하려고 할 때 오류가 발생합니다.  구문이 변경되었나요?
예, 작업 영역이 업그레이드될 때 컴퓨터 그룹을 사용하는 구문이 변경됩니다.  자세한 내용은 [Log Analytics 로그 검색의 컴퓨터 그룹](log-analytics-computer-groups.md)을 참조하세요.


## <a name="dashboards"></a>대시보드

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>질문: 대시보드를 업그레이드된 작업 영역에서 계속 사용할 수 있나요?
업그레이드를 통해 **내 대시보드** 사용을 중단하는 과정이 시작됩니다.  작업 영역을 업그레이드하기 전에 대시보드에 추가한 타일은 계속 사용할 수 있지만 해당 타일을 편집하거나 새 타일을 추가할 수는 없습니다.  보다 풍부한 기능이 있는 [뷰 디자이너](log-analytics-view-designer.md)로 뷰를 계속 만들고 편집할 수 있으며, Azure Portal에서 대시보드를 만들 수도 있습니다.


## <a name="log-searches"></a>로그 검색

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>질문: 업그레이드한 작업 영역 외부에 저장된 검색이 있습니다. 이러한 검색을 새 검색 언어로 자동 변환할 수 있나요?
로그 검색 페이지의 언어 변환기 도구를 사용하면 각 검색을 변환할 수 있습니다.  작업 영역을 업그레이드하지 않고 여러 검색을 자동으로 변환하는 방법은 없습니다.

### <a name="question-why-are-my-query-results-not-sorted"></a>질문: 내 쿼리 결과가 정렬되지 않는 이유는 무엇입니까?
새 쿼리 언어에서는 결과가 기본적으로 정렬되지 않습니다.  [정렬 연산자](https://go.microsoft.com/fwlink/?linkid=856079)를 사용하여 하나 이상의 속성으로 결과를 정렬합니다.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>질문: 업그레이드 후에 메트릭 보기는 어디로 이동하나요?
메트릭 보기는 로그 검색의 성능 데이터를 그래픽으로 표시합니다.  이 보기는 업그레이드 후에 더 이상 사용할 수 없습니다.  [렌더링 연산자](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator)를 사용하여 시간 차트의 쿼리 출력 형식을 지정할 수 있습니다.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>질문: 업그레이드한 후 축소는 어디로 이동합니까?
축소는 검색 결과를 요약하여 보여주는 기능입니다.  업그레이드한 후 축소 옵션은 로그 검색 포털에 더 이상 나타나지 않습니다.  [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) 또는 [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster)를 사용하여 새로운 검색 언어로 유사한 기능을 얻을 수 있습니다. 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>로그 검색 API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>질문: 제가 업그레이드를 수행한 후에 로그 검색 API가 업데이트되었습니까?
작업 영역을 업그레이드했을 때 레거시 [로그 검색 API](log-analytics-log-search-api.md)이 더 이상 작동하지 않습니다.  새로운 API에 대한 자세한 내용은 [Azure Log Analytics REST API](https://dev.loganalytics.io/)를 참조하세요.


## <a name="portals"></a>포털

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>질문: 새 고급 분석 포털을 사용해야 합니까? 아니면 로그 검색 포털을 계속 사용해야 합니까?
[Azure Log Analytics에서 로그 쿼리를 만들고 편집하기 위한 포털](log-analytics-log-search-portals.md)에서 두 포털의 비교를 볼 수 있습니다.  각 포털은 분명한 이점이 있으므로 자신의 요구 사항에 가장 적합한 포털을 선택할 수 있습니다.  두 포털 모두 고급 분석 포털에서 쿼리를 작성하고 뷰 디자이너와 같은 다른 위치에 붙여넣을 수 있습니다.  이를 수행할 때 [고려해야 할 문제](log-analytics-log-search-portals.md#advanced-analytics-portal)를 읽어보세요.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>질문: 업그레이드 후 쿼리 실행을 시도하는 중 오류가 발생했고 보기에도 오류가 표시됩니다.

업그레이드 후 브라우저가 Log Analytics 쿼리를 실행하려면 다음 주소에 액세스해야 합니다.  브라우저가 방화벽을 통해 Azure Portal에 액세스하는 경우 이 주소에 대한 액세스를 활성화해야 합니다.

| Uri | IP | 포트 |
|:---|:---|:---|
| portal.loganalytics.io | 동적 | 80,443 |
| api.loganalytics.io    | 동적 | 80,443 |
| docs.loganalytics.io   | 동적 | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>질문: PowerBI 통합에서 변경되는 사항이 있나요?
예.  작업 영역이 업그레이드되면 Log Analytics 데이터를 Power BI로 내보내는 프로세스가 더 이상 작동하지 않습니다.  그러면 업그레이드 전에 만든 모든 기존 일정을 사용할 수 없게 됩니다.  

업그레이드 후 Azure Log Analytics는 Application Insights와 같은 플랫폼을 사용하며, [Application Insights 쿼리를 Power BI로 내보내는 프로세스](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)와 같은 프로세스를 사용하여 Log Analytics 쿼리를 Power BI로 내보냅니다.  Power BI로 내보내기를 수행하면 이제 API 끝점이 직접 호출됩니다. 이를 사용하여 최대 50만 개의 행 또는 64,000,000바이트의 데이터를 가져오고, 긴 쿼리의 내보내고, 쿼리 제한 시간(기본 시간 제한: 3분, 최대 시간 제한: 10분)을 사용자 지정할 수 있습니다.

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>질문: 제가 업그레이드를 수행한 후에 로그 검색 PowerShell cmdlet이 업데이트되었습니까?
모든 작업 영역에 대한 업그레이드가 완료되면 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults)가 지원되지 않습니다.  [Invoke-LogAnalyticsQuery cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets)를 사용하여 업그레이드된 작업 영역에서 로그 검색을 수행합니다.




## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>질문: Resource Manager 템플릿을 사용하여 업그레이드된 작업 영역을 작성할 수 있나요?
예.  2017년 3월 15일 미리 보기의 API 버전을 사용하고 다음 예제와 같이 템플릿의 **기능** 섹션을 포함해야 합니다.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>솔루션

### <a name="question-will-my-solutions-continue-to-work"></a>질문: 내 솔루션이 계속 작동하나요?
모든 솔루션은 업그레이드된 작업 영역에서 계속 작동합니다. 단, 새 쿼리 언어로 변환된 경우 성능은 향상됩니다.  이 섹션에 설명된 일부 기존 솔루션에는 알려진 문제가 있습니다.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>알려진 문제: Application Insights 커넥터의 큐브 뷰
[Application Insights 커넥터 솔루션](log-analytics-app-insights-connector.md)의 큐브 뷰가 Application Insights 커넥터 솔루션에서 더 이상 지원되지 않습니다.  Application Insights 데이터로 사용자 지정 뷰를 만들려면 뷰 디자이너를 사용할 수 있습니다.

### <a name="known-issue-backup-solution"></a>알려진 문제: Backup 솔루션
작업 영역을 업그레이드하기 전에 Backup 솔루션을 설치한 경우 Backup 솔루션에서 데이터를 수집하지 못할 수 있습니다. 이 솔루션을 제거한 다음 최신 버전을 설치합니다.  새 버전의 솔루션은 클래식 Backup 자격 증명 모음을 지원하지 않으므로 이 솔루션을 계속 사용하려면 Recovery Services 자격 증명 모음으로도 업그레이드해야 합니다.

## <a name="upgrade-process"></a>업그레이드 프로세스

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>질문: 작업 영역이 여러 개 있습니다. 모든 작업 영역을 동시에 업그레이드할 수 있나요?  
번호  각 업그레이드는 단일 작업 영역에 적용됩니다. 현재는 여러 작업 영역을 한 번에 업그레이드할 수 있는 방법이 없습니다. 업그레이드된 작업 영역의 다른 사용자도 영향을 받게 됩니다.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>질문: 업그레이드하면 작업 영역에 수집되어 있는 기존 로그 데이터가 수정되나요?  
번호 작업 영역 검색에 사용할 수 있는 로그 데이터는 업그레이드의 영향을 받지 않습니다. 저장된 검색, 경고 및 보기는 새 검색 언어로 자동 변환됩니다.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>질문: 내 작업 영역을 업그레이드하지 않으면 어떻게 되나요?  
향후 몇 개월 이내에 레거시 로그 검색은 사용되지 않을 예정입니다. 이 시점까지 업그레이드하지 않은 작업 영역은 자동으로 업그레이드됩니다.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>질문: 업그레이드 후 되돌릴 수 있나요?
일반 공급 이전에는 업그레이드 후 작업 영역을 되돌릴 수 있습니다.  이제 새 언어가 일반 공급에 이르렀으므로 기존 플랫폼을 사용 중지해 나가면서 이 기능이 제거되었습니다.


## <a name="views"></a>뷰

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>질문: 뷰 디자이너를 통해 새 뷰를 만들려면 어떻게 할까요?
업그레이드하기 전에 OMS 포털의 기본 대시보드에 있는 타일에서 뷰 디자이너를 사용하여 새 보기를 만들 수 있습니다.  작업 영역이 업그레이드되면 이 타일은 제거됩니다.  왼쪽 메뉴의 녹색 + 단추를 클릭하여 OMS 포털에서 뷰 디자이너로 새 뷰를 만들 수 있습니다.  [뷰 디자이너] 타일을 클릭하여 Azure Portal을 통해 새 보기를 계속 만듭니다.


## <a name="next-steps"></a>다음 단계

- [새 Log Analytics 쿼리 언어로 작업 영역 업그레이드](log-analytics-log-search-upgrade.md)에 대해 자세히 알아봅니다.
