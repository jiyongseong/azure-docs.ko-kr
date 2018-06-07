---
title: "Azure Log Analytics에서 변경된 기능 | Microsoft Docs"
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Azure Log Analytics에서 변경된 기능
쿼리 언어 자체 외에도 Log Analytics 작업 영역을 [새로운 쿼리 언어로 업그레이드](log-analytics-log-search-new.md)할 때 알고 있어야 하는 일부 향상된 기능과 변경 내용이 있습니다.  이 문서에서는 각각의 자세한 내용에 대한 링크를 통해 레거시와 업그레이드된 작업 영역 간의 변경 내용을 간략하게 설명합니다. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

공통 질문에 대한 업그레이드 및 대답에 관해 알려진 문제에 대한 설명은 [Log Analytics 새 로그 검색 FAQ 및 알려진 문제](log-analytics-log-search-faq.md)를 참조하세요.  

## <a name="query-language"></a>쿼리 언어
Log Analytics 업그레이드에서 주요 변경 내용은 이전 언어보다 크게 향상된 기능을 포함하는 새 쿼리 언어입니다.  

[Azure Log Analytics 새로운 쿼리 언어로 전환](log-analytics-log-search-transition.md)에서 레거시 언어와 새로운 언어 간의 공통 작업을 직접 비교할 수 있습니다.  새 언어에 대한 전체 설명서는 [Azure Log Analytics 쿼리 언어](https://docs.loganalytics.io)에서 제공됩니다.


## <a name="computer-groups"></a>컴퓨터 그룹
이제 각각에 고유한 별칭을 제공해야 하지만 컴퓨터 그룹을 만드는 메서드는 변경되지 않았습니다.  로그 검색에 따라 컴퓨터 그룹은 새 언어 구문을 사용해야 합니다.

로그 검색에서 컴퓨터 그룹을 사용하는 새 구문입니다.  컴퓨터 그룹은 이제 $ComputerGroups 함수를 사용하는 대신 각각 고유한 별칭을 가진 함수로 구현됩니다.  다른 함수와 같이 로그 검색에서 별칭을 사용합니다.  

자세한 내용은 [Log Analytics 로그 검색의 컴퓨터 그룹](log-analytics-computer-groups.md)에서 제공됩니다.


## <a name="log-search-portals"></a>로그 검색 포털
로그 검색을 만들고 실행하는 로그 검색 포털 외에도 이제 크게 향상된 편집 기능을 제공하는 고급 분석 포털을 사용할 수 있습니다.

두 포털에 대한 간단한 설명은 [Azure Log Analytics에서 로그 쿼리를 만들고 편집하기 위한 포털](log-analytics-log-search-portals.md)에서 제공됩니다.  [분석 포털 시작](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)에서 새 포털에 대한 자습서를 살펴볼 수 있습니다.

## <a name="alerts"></a>경고
경고는 업그레이드된 작업 영역에서 동일하게 작동하지만 실행하는 쿼리는 새로운 언어로 작성되어야 합니다.  업그레이드하기 전에 있던 모든 기존 경고 규칙은 자동으로 새 언어로 변환됩니다.  [Log Analytics에서 경고 이해](log-analytics-alerts.md)에서 자세한 내용을 확인할 수 있습니다.

경고에서 전송된 Runbook 및 웹후크에 대한 페이로드 형식이 변경되었습니다.  [Log Analytics에서 경고 규칙에 작업 추가](log-analytics-alerts-actions.md)에서 새로운 페이로드 형식에 대한 자세한 내용을 확인합니다.

## <a name="dashboards"></a>대시보드
[대시보드](log-analytics-dashboards.md)는 점점 사용하지 않고 있습니다.  작업 영역을 업그레이드하기 전에 대시보드에 추가한 타일은 계속 사용할 수 있지만 해당 타일을 편집하거나 새 타일을 추가할 수는 없습니다.  뷰 디자이너를 사용하여 대시보드보다 다양한 기능 집합을 가진 사용자 지정 보기를 만듭니다.

뷰 디자이너에 대한 자세한 내용은 [뷰 디자이너를 사용하여 Log Analytics에서 사용자 지정 보기 만들기](log-analytics-view-designer.md)에서 제공됩니다.

## <a name="power-bi"></a>Power BI
Log Analytics 데이터를 Power BI로 내보내는 프로세스는 업그레이드된 작업 영역에서 변경되고 업그레이드하기 전에 만든 모든 기존 일정은 사용할 수 없게 됩니다.  

자세한 내용은 [Log Analytics 데이터를 Power BI로 내보내기](log-analytics-powerbi.md)에서 제공됩니다.

## <a name="powershell"></a>PowerShell
PowerShell에서 로그 검색을 실행하기 위한 Get-AzureRmOperationalInsightsSearchResults는 업그레이드된 작업 영역에서 작동하지 않습니다.  업그레이드된 작업 영역에서 이 기능에 대해 Invoke-LogAnalyticsQuery를 사용합니다.

자세한 내용은 [Azure Log Analytics REST API - PowerShell Cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets)에서 제공됩니다.

## <a name="log-search-api"></a>로그 검색 API
로그 검색 REST API가 변경되고 레거시 버전을 사용하는 모든 솔루션이 새 버전의 API를 사용하도록 업데이트되어야 합니다.   

새 버전의 API에 대한 자세한 내용은 [Azure Log Analytics REST API](https://dev.loganalytics.io/)에서 제공됩니다.

## <a name="next-steps"></a>다음 단계

- 공통 질문에 대한 업그레이드 및 대답에 관해 알려진 문제에 대한 설명은 [Log Analytics 새 로그 검색 FAQ 및 알려진 문제](log-analytics-log-search-faq.md)를 참조하세요.