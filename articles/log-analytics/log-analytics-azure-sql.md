---
title: Log Analytics의 Azure SQL Analytics 솔루션 | Microsoft Docs
description: Azure SQL Analytics 솔루션을 통해 Azure SQL Database를 관리할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Log Analytics에 Azure SQL Analytics(미리 보기)를 사용하여 Azure SQL Database 모니터링

![Azure SQL 분석 기호](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics의 Azure SQL 분석 솔루션은 중요한 SQL Azure 성능 메트릭을 수집하여 시각화합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. 그리고 여러 Azure 구독 및 탄력적 풀 간에 Azure SQL Database 및 탄력적 풀 메트릭을 모니터링하고 이를 시각화할 수 있습니다. 또한 솔루션은 응용 프로그램 스택의 각 계층에서 문제를 식별할 수도 있습니다.  [Azure 진단 메트릭](log-analytics-azure-storage.md)과 Log Analytics 뷰를 함께 사용하여 모든 Azure SQL Databases 및 탄력적 풀에 대한 데이터를 단일 Log Analytics 작업 영역에 표시합니다.

현재, 이 미리 보기 솔루션은 작업 영역당 최대 150,000개의 Azure SQL Database 및 5,000개의 SQL 탄력적 풀을 지원합니다.

Log Analytics에 대해 제공되는 다른 도구처럼 Azure SQL 분석 솔루션은 Azure 리소스의 상태에 대한 알림(이 경우, Azure SQL Database)을 모니터링하고 수신할 수 있습니다. Microsoft Azure SQL Database는 Azure 클라우드에서 실행되는 응용 프로그램에 친숙한 SQL Server와 유사한 기능을 제공하는 확장성 있는 관계형 데이터베이스 서비스입니다. Log Analytics를 통해 구조적 및 비구조적 데이터를 수집하고, 상관 관계를 지정하며 시각화할 수 있습니다.

Azure SQL Analytics 솔루션 사용에 대한 실무 중심 개요와 일반적인 사용 시나리오에 대해서는 포함된 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>연결된 소스

Azure SQL 분석 솔루션은 Log Analytics 서비스에 연결하는 데 에이전트를 사용하지 않습니다.

다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원 | 설명 |
| --- | --- | --- |
| [Windows 에이전트](log-analytics-windows-agent.md) | 아니오 | 직접 Windows 에이전트는 솔루션에 사용되지 않습니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) | 아니오 | 직접 Linux 에이전트는 솔루션에 사용되지 않습니다. |
| [SCOM 관리 그룹](log-analytics-om-agents.md) | 아니오 | SCOM 에이전트에서 Log Analytics로 직접 연결은 솔루션에 사용되지 않습니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) | 아니오 | Log Analytics는 저장소 계정의 데이터를 읽지 않습니다. |
| [Azure 진단](log-analytics-azure-storage.md) | 예 | Azure 메트릭 및 로그 데이터는 Azure에 의해 직접 Log Analytics에 전송됩니다. |

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 없는 경우 [무료](https://azure.microsoft.com/free/) 구독을 하나 만들 수 있습니다.
- Log Analytics 작업 영역. 기존 항목을 사용하거나 이 솔루션 사용을 시작하기 전에 [새로 만들 수 있습니다.](log-analytics-quick-create-workspace.md)
- Azure SQL Database 및 탄력적 풀에 대한 Azure 진단을 사용하도록 설정하고 [Log Analytics에 데이터를 보내도록 구성](../sql-database/sql-database-metrics-diag-logging.md)합니다.

## <a name="configuration"></a>구성

Azure SQL 분석 솔루션을 작업 영역에 추가하려면 다음 단계를 수행합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명하는 프로세스를 사용하여 작업 영역에 Azure SQL 분석 솔루션을 추가합니다.
2. Azure Portal에서 **리소스 만들기** > **모니터링 + 관리**를 클릭합니다.  
    ![모니터링 + 관리](./media/log-analytics-azure-sql/monitoring-management.png)
3. **모니터링 + 관리** 목록에서 **모두 표시**를 클릭합니다.
4. **권장** 목록에서 **자세히**를 클릭한 후 새 목록에서 **Azure SQL 분석(미리 보기)** 을 찾아 선택합니다.  
    ![Azure SQL Analytics 솔루션](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. **Azure SQL 분석(미리 보기)** 영역에서 **만들기**를 클릭합니다.  
    ![만들기](./media/log-analytics-azure-sql/portal-create.png)
6. **새 솔루션 만들기** 영역에서 솔루션을 추가할 작업 영역을 선택한 후 **만들기**를 클릭합니다.  
    ![작업 영역에 추가](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Azure 구독을 여러 개 구성하려면

여러 구독을 지원하려면 [PowerShell을 사용하여 Azure 리소스 메트릭 로깅 사용](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)에서 PowerShell 스크립트를 사용합니다. 하나의 Azure 구독에서 다른 Azure 구독의 작업 영역으로 진단 데이터를 전송하는 스크립트를 실행할 때 작업 영역 리소스 ID를 매개 변수로 제공합니다.

**예제**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>솔루션 사용

솔루션을 작업 영역에 추가하면 Azure SQL Analytics 타일이 작업 영역에 추가되고 개요에 표시됩니다. 타일은 솔루션이 연결된 Azure SQL 데이터베이스 및 Azure SQL 탄력적 풀 수를 보여 줍니다.

![Azure SQL Analytics 타일](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL 분석 데이터 보기

**Azure SQL 분석** 타일을 클릭하여 Azure SQL 분석 대시보드를 엽니다. 대시보드에는 서로 다른 관점을 통해 모니터링되는 모든 데이터베이스의 개요가 포함됩니다. 다른 관점에서 작업하려면 SQL 리소스의 적절한 메트릭이나 로그를 Azure Log Analytics 작업 영역으로 스트리밍할 수 있어야 합니다.

![Azure SQL 분석 개요](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

![Azure SQL 분석 시간 제한](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

각 관점에서 구독, 서버, 탄력적 풀 및 데이터베이스 수준에 대한 요약이 제공됩니다. 또한 각 관점에 따라 오른쪽에 관점별 보고서가 표시됩니다. 목록에서 구독, 서버, 풀 또는 데이터베이스를 선택하면 계속해서 드릴다운됩니다.

| 관점 | 설명 |
| --- | --- |
| 유형별 리소스 | 모니터링되는 모든 리소스를 계산하는 관점. 드릴다운은 DTU 및 GB 메트릭에 대한 요약을 제공합니다. |
| 자세한 정보 | Intelligent Insights에 대한 계층적 드릴다운을 제공합니다. Intelligent Insights에 대해 자세히 알아보세요. |
| 오류 | 데이터베이스에서 발생한 SQL 오류에 대한 계층적 드릴다운을 제공합니다. |
| 시간 제한 | 데이터베이스에서 발생한 SQL 시간 제한에 대한 계층적 드릴다운을 제공합니다. |
| 차단 | 데이터베이스에서 발생한 SQL 차단에 대한 계층적 드릴다운을 제공합니다. |
| 데이터베이스 대기 | 데이터베이스 수준에서 발생한 SQL 대기 통계에 대한 계층적 드릴다운을 제공합니다. 총 대기 시간 및 대기 유형별 대기 시간에 대한 요약을 포함합니다. |
| 쿼리 기간 | 쿼리 기간, CPU 사용량, 데이터 IO 사용량, 로그 IO 사용량과 같은 쿼리 실행 통계에 대한 계층적 드릴다운을 제공합니다. |
| 쿼리 대기 | 대기 범주별 쿼리 대기 통계에 대한 계층적 드릴다운을 제공합니다. |

### <a name="intelligent-insights-report"></a>Intelligent Insights 보고서

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md)는 사용자의 데이터베이스 성능을 알려줍니다. 수집된 모든 Intelligent Insights를 시각화하고 Insights 관점에서 액세스할 수 있습니다.

![Azure SQL 분석 정보](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>탄력적 풀 및 데이터베이스 보고서

탄력적 풀과 데이터베이스 모두 지정된 시간에 리소스에 대해 수집된 데이터를 모두 보여 주는 고유한 특정 보고서를 포함합니다.

![Azure SQL 분석 데이터베이스](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL 분석 탄력적 풀](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>쿼리 보고서

쿼리 기간 및 쿼리 대기 관점을 통해, 쿼리 보고서에서 쿼리 성능을 상호 연결할 수 있습니다. 이 보고서는 여러 데이터베이스 간의 쿼리 성능을 비교하고 선택한 쿼리를 원활하게 수행하는 데이터베이스와 느리게 수행하는 데이터베이스를 쉽게 찾아낼 수 있습니다.

![Azure SQL 분석 쿼리](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>데이터 분석 및 경고 만들기

Azure SQL Database 리소스에서 가져온 데이터와 [경고를 쉽게 만들](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) 수 있습니다. 다음은 로그 경고와 함께 사용할 수 있는 몇 가지 유용한 [로그 검색](log-analytics-log-searches.md) 쿼리입니다.



*Azure SQL Database에 대한 높은 DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Azure SQL Database 탄력적 풀에 대한 높은 DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>다음 단계

- Log Analytics의 [로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Azure SQL 데이터를 확인합니다.
- Azure SQL 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](log-analytics-dashboards.md).
- 특정 Azure SQL 이벤트가 발생하는 경우의 [경고 만들기](log-analytics-alerts.md).
