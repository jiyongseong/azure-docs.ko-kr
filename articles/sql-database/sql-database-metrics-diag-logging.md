---
title: Azure SQL Database 메트릭 및 진단 로깅 | Microsoft Docs
description: 리소스 사용, 연결 및 쿼리 실행 통계를 저장하도록 Azure SQL Database를 구성하는 방법을 알아봅니다.
services: sql-database
documentationcenter: ''
author: veljko-msft
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 03/16/2018
ms.author: vvasic
ms.openlocfilehash: c18d4d175bace79fefedc09fb887e707b8c066d9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365766"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database 메트릭 및 진단 로깅 
Azure SQL Database는 모니터링 편의를 위해 메트릭 및 진단 로그를 내보낼 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나에 저장하도록 SQL Database를 구성할 수 있습니다.

* **Azure Storage**: 작은 가격으로 방대한 양의 원격 분석을 보관하는 경우 사용됩니다.
* **Azure Event Hub**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 SQL Database 원격 분석을 통합하는 경우 사용됩니다.
* **Azure Log Analytics**: 보고, 경고 및 완화 기능을 사용하는 기본 제공 모니터링 솔루션의 경우 사용됩니다. [OMS(Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md)의 기능에 해당합니다.

    ![아키텍처](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>로깅 사용

메트릭 및 진단 로깅은 기본적으로 사용되지 않습니다. 다음 방법 중 하나를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하고 관리할 수 있습니다.

- Azure portal
- PowerShell
- Azure CLI
- Azure Monitor REST API 
- Azure Resource Manager 템플릿

메트릭 및 진단 로깅을 사용하려면 선택한 데이터가 수집되는 Azure 리소스를 지정해야 합니다. 사용 가능한 옵션은 다음과 같습니다.

- Log Analytics
- Event Hubs
- Storage 

새 Azure 리소스를 프로비전하거나 기존 리소스를 선택할 수 있습니다. 저장소 리소스를 선택한 후에는 수집할 데이터를 지정해야 합니다. 사용 가능한 옵션은 다음과 같습니다.

- [모든 메트릭](sql-database-metrics-diag-logging.md#all-metrics): DTU 백분율, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율을 포함합니다.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): CPU 사용량, 쿼리 기간 등 쿼리 런타임 통계에 대한 정보를 포함합니다.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): CPU, LOG, LOCKING 등 쿼리가 대기된 항목을 알리는 쿼리 대기 통계에 대한 정보를 포함합니다.
- [Errors](sql-database-metrics-diag-logging.md#errors-dataset): 이 데이터베이스에서 발생한 SQL 오류에 대한 정보를 포함합니다.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): 대기 형식에 따라 데이터베이스가 대기하는 데 사용된 시간에 대한 정보를 포함합니다.
- [Time-outs](sql-database-metrics-diag-logging.md#time-outs-dataset): 이 데이터베이스에서 발생한 SQL 오류에 대한 정보를 포함합니다.
- [Blockings](sql-database-metrics-diag-logging.md#blockings-dataset): 데이터베이스에서 발생한 차단 이벤트에 대한 정보를 포함합니다.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): Intelligent Insights를 포함합니다. [Intelligent Insights에 대해 자세히 알아보세요.](sql-database-intelligent-insights.md)
- **Audit** / **SQLSecurityAuditEvents**: 현재 사용할 수 없습니다.

Event Hubs 또는 저장소 계정을 선택하면 보존 정책을 지정할 수 있습니다. 이 정책은 선택한 기간보다 오래된 데이터를 삭제합니다. Log Analytics를 지정한 경우 선택한 가격 책정 계층에 따라 보존 정책이 달라집니다. 자세한 내용은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요. 

로깅을 사용하도록 설정하는 방법을 학습하고 여러 Azure 서비스에서 지원되는 메트릭과 로그 범주를 이해하려면 다음을 확인하는 것이 좋습니다. 

* [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure portal

1. Azure Portal에서 메트릭 및 진단 로그 수집을 사용하려면 SQL Database 또는 탄력적 풀 페이지로 이동하여 **진단 설정**을 클릭합니다.

   ![Azure Portal에서 사용](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. 대상 및 원격 분석을 선택하여 새 진단 설정을 만들거나 기존 진단 설정을 편집합니다.

   ![진단 설정](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 영역의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="to-configure-multiple-azure-resources"></a>Azure 리소스를 여러 개 구성하려면

여러 구독을 지원하려면 [PowerShell을 사용하여 Azure 리소스 메트릭 로깅 사용](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)에서 PowerShell 스크립트를 사용합니다.

여러 리소스의 진단 데이터를 작업 영역으로 보내는 스크립트(Enable-AzureRMDiagnostics.ps1)를 실행할 때 작업 영역 리소스 ID &lt;$WSID&gt;를 매개 변수로 제공합니다. 진단 데이터를 보내려는 작업 영역 ID &lt;$WSID&gt;를 가져오려면 다음 스크립트에서 &lt;subID&gt;를 구독 ID, &lt;RG_NAME&gt;을 리소스 그룹 이름, &lt;WS_NAME&gt;을 작업 영역 이름으로 바꿉니다.

- Azure 리소스를 여러 개 구성하려면 다음 명령을 사용하세요.

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus 규칙 ID는 이 형식의 문자열입니다.

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

[Azure Monitor REST API를 사용하여 진단 설정 변경](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings)을 참조하세요. 

### <a name="resource-manager-template"></a>Resource Manager 템플릿

[Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정을 활성화하는 방법](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)을 참조하세요.  

## <a name="stream-into-log-analytics"></a>Log Analytics에 스트림 
SQL Database 메트릭 및 진단 로그는 포털에서 기본 제공되는 **Log Analytics로 보내기** 옵션을 사용하여 Log Analytics로 스트림할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Log Analytics를 사용하도록 설정할 수도 있습니다.

### <a name="installation-overview"></a>설치 개요

Log Analytics를 사용하여 SQL Database 세트를 간편하게 모니터링할 수 있습니다. 세 단계를 수행해야 합니다.

1. Log Analytics 리소스를 만듭니다.

2. 생성된 Log Analytics 리소스에 메트릭 및 진단 로그를 기록하도록 데이터베이스를 구성합니다.

3. Log Analytics의 갤러리에서 **Azure SQL Analytics** 솔루션을 설치합니다.

### <a name="create-a-log-analytics-resource"></a>Log Analytics 리소스 만들기

1. 왼쪽에 있는 메뉴에서 **리소스 만들기**를 선택합니다.

2. **모니터링 + 관리**를 선택합니다.

3. **Log Analytics**를 선택합니다.

4. 필요한 추가 정보(작업 영역 이름, 구독, 리소스 그룹, 위치 및 가격 책정 계층)를 Log Analytics 양식에 입력합니다.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>메트릭 및 진단 로그를 기록하도록 데이터베이스 구성

데이터베이스에서 메트릭을 기록하는 위치를 구성하는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. 포털에서 SQL Database 리소스로 이동하 고 **진단 설정**을 선택합니다. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>갤러리에서 SQL Analytics 솔루션 설치

1. Log Analytics 리소스를 만들고 데이터 흐름이 시작되면 SQL Analytics 솔루션을 설치합니다. 홈페이지의 사이드 메뉴에서 **솔루션 갤러리**를 선택합니다. 갤러리에서 **Azure SQL Analytics** 솔루션을 선택한 다음 **추가**를 클릭합니다.

   ![모니터링 솔루션](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. 홈페이지에**Azure SQL Analytics** 타일이 나타납니다. 이 타일을 선택하여 SQL Analytics 대시보드를 엽니다.

### <a name="use-the-sql-analytics-solution"></a>SQL Analytics 솔루션 사용

SQL Analytics는 SQL Database 리소스의 계층을 탐색할 수 있는 계층적 대시보드입니다. SQL Analytics 솔루션 사용 방법은 [SQL Analytics 솔루션을 사용한 SQL Database 모니터링](../log-analytics/log-analytics-azure-sql.md)을 참조하세요.

## <a name="stream-into-event-hubs"></a>Event Hubs로 스트림

SQL Database 메트릭 및 진단 로그는 포털에서 기본 제공되는 **Event Hubs로 스트림** 옵션을 사용하여 Event Hubs로 스트림할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Service Bus 규칙 ID를 사용하도록 설정할 수도 있습니다. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Event Hubs에서 메트릭 및 진단 로그를 사용하여 수행할 수 있는 작업
선택한 데이터가 Event Hubs로 스트리밍되면 고급 모니터링 시나리오를 사용할 수 있는 단계에 한 단계 더 가까워집니다. Event Hubssms 이벤트 파이프라인의 프런트 도어 역할을 합니다. 데이터가 Event Hub에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs는 해당 이벤트 소비에서 이벤트 스트림 프로덕션을 분리합니다. 이러한 방식으로 이벤트 소비자는 자체 일정에 따라 이벤트를 액세스할 수 있습니다. Event Hubs에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Event Hubs란?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


스트림 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

* **Power BI로 핫 패스 데이터를 스트림하여 서비스 상태를 확인합니다**. Event Hubs, Stream Analytics 및 PowerBI를 사용하여 Azure 서비스에서 메트릭 및 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. Event Hubs를 설정하고 Stream Analytics로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 개요는 [Stream Analytics 및 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)를 참조하세요.

* **제3자 로깅 및 원격 분석에 로그를 스트림합니다**. Event Hubs 스트림을 사용하여 메트릭 및 진단 로그를 다른 타사 모니터링 및 로그 분석 솔루션으로 가져올 수 있습니다. 

* **사용자 지정 원격 분석 및 로깅 플랫폼을 빌드합니다**. 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 Event Hubs 사용에 대한 Dan Rosanova의 가이드](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)를 참조하세요.

## <a name="stream-into-storage"></a>Storage에 스트림

포털에서 기본 제공되는 **저장소 계정에 보관** 옵션을 사용하여 SQL Database 메트릭 및 진단 로그를 Storage에 저장할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여Storage를 사용하도록 설정할 수도 있습니다.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>저장소 계정의 메트릭 및 진단 로그 스키마

메트릭 및 진단 로그 수집을 설정하면 첫 번째 데이터 행을 사용할 수 있을 때 선택한 저장소 계정에 저장소 컨테이너가 만들어집니다. 해당 Blob의 구조는 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
또는 더 간단하게 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 모든 메트릭에 대한 Blob 이름은 다음과 같을 수 있습니다.

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

탄력적 풀에서 데이터를 기록하려는 경우에는 Blob 이름이 약간 다릅니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Storage에서 메트릭 및 로그 다운로드

[Storage에서 메트릭 및 진단 로그 다운로드](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)를 참조하세요.

## <a name="metrics-and-logs-available"></a>사용 가능한 메트릭 및 로그

### <a name="all-metrics"></a>모든 메트릭

|**리소스**|**metrics**|
|---|---|
|데이터베이스|DTU 백분율, 사용된 DTU, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율, 교착 상태를 포함합니다. |
|탄력적 풀|eDTU 백분율, 사용된 eDTU, eDTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 용량 한도, XTP 저장소 백분율을 포함합니다. |
|||

### <a name="query-store-runtime-statistics"></a>쿼리 저장소 런타임 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: QueryStoreRuntimeStatistics|
|OperationName|작업의 이름입니다. 항상: QueryStoreRuntimeStatisticsEvent|
|리소스|리소스의 이름입니다.|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|query_hash_s|쿼리 해시|
|query_plan_hash_s|쿼리 계획 해시|
|statement_sql_handle_s|문 SQL 핸들|
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset.|
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset.|
|logical_io_writes_d|총 논리적 IO 쓰기 수.|
|max_logical_io_writes_d|실행당 최대 논리적 IO 쓰기 수.|
|physical_io_reads_d|총 물리적 IO 읽기 수.|
|max_physical_io_reads_d|실행당 최대 논리적 IO 읽기 수.|
|logical_io_reads_d|총 논리적 IO 읽기 수.|
|max_logical_io_reads_d|실행당 최대 논리적 IO 읽기 수.|
|execution_type_d|실행 형식|
|count_executions_d|쿼리의 실행 수.|
|cpu_time_d|쿼리에 사용된 총 CPU 시간(밀리초).|
|max_cpu_time_d|단일 실행에 사용된 최대 CPU 시간(밀리초).|
|dop_d|병렬 처리 수준 합계.|
|max_dop_d|단일 실행에 사용된 최대 병렬 처리 수준.|
|rowcount_d|반환된 총 행 수.|
|max_rowcount_d|단일 실행으로 반환된 최대 행 수.|
|query_max_used_memory_d|사용된 총 메모리 양(KB).|
|max_query_max_used_memory_d|단일 실행에 사용된 최대 메모리 양(KB).|
|duration_d|총 실행 시간(밀리초).|
|max_duration_d|단일 실행의 최대 실행 시간.|
|num_physical_io_reads_d|총 물리적 읽기 수.|
|max_num_physical_io_reads_d|실행당 최대 물리적 읽기 수.|
|log_bytes_used_d|사용된 총 로그 바이트 양.|
|max_log_bytes_used_d|실행당 사용된 최대 로그 바이트 양.|
|query_id_d|쿼리 저장소의 쿼리 ID|
|plan_id_d|쿼리 저장소의 계획 ID|

[쿼리 저장소 런타임 통계 데이터에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>쿼리 저장소 대기 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: QueryStoreWaitStatistics|
|OperationName|작업의 이름입니다. 항상: QueryStoreWaitStatisticsEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|wait_category_s|대기 범주.|
|is_parameterizable_s|매개 변수화할 수 있는 쿼리.|
|statement_type_s|문 형식.|
|statement_key_hash_s|문 키 해시.|
|exec_type_d|실행 형식|
|total_query_wait_time_ms_d|특정 대기 범주의 총 쿼리 대기 시간.|
|max_query_wait_time_ms_d|특정 대기 범주 개별 실행의 최대 쿼리 대기 시간.|
|query_param_type_d|0|
|query_hash_s|쿼리 저장소의 쿼리 해시.|
|query_plan_hash_s|쿼리 저장소의 쿼리 계획 해시|
|statement_sql_handle_s|쿼리 저장소의 문 핸들|
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset.|
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset.|
|count_executions_d|쿼리 실행 수|
|query_id_d|쿼리 저장소의 쿼리 ID|
|plan_id_d|쿼리 저장소의 계획 ID|

[쿼리 저장소 대기 통계 데이터를 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>오류 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Errors|
|OperationName|작업의 이름입니다. 항상: ErrorEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|Message|일반 텍스트의 오류 메시지|
|user_defined_b|오류 사용자 정의 비트|
|error_number_d|오류 코드|
|심각도|오류의 심각도|
|state_d|오류 상태|
|query_hash_s|실패한 쿼리의 쿼리 해시(있는 경우)|
|query_plan_hash_s|실패한 쿼리의 쿼리 계획 해시(있는 경우).|

[SQL Server 오류 메시지에 대해 자세히 알아봅니다](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>데이터베이스 대기 통계 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: DatabaseWaitStatistics|
|OperationName|작업의 이름입니다. 항상: DatabaseWaitStatisticsEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|wait_type_s|대기 형식 이름|
|start_utc_date_t [UTC]|측정 기간 시작 시간.|
|end_utc_date_t [UTC]|측정 기간 종료 시간.|
|delta_max_wait_time_ms_d|실행당 최대 대기 시간|
|delta_signal_wait_time_ms_d|총 신호 대기 시간|
|delta_wait_time_ms_d|기간의 총 대기 시간|
|delta_waiting_tasks_count_d|대기 작업 수|

[데이터베이스 대기 통계에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="time-outs-dataset"></a>제한 시간 데이터 집합 

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Timeouts|
|OperationName|작업의 이름입니다. 항상: TimeoutEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|error_state_d|오류 상태 코드|
|query_hash_s|쿼리 해시(있는 경우)|
|query_plan_hash_s|쿼리 계획 해시(있는 경우)|

### <a name="blockings-dataset"></a>차단 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Blocks|
|OperationName|작업의 이름입니다. 항상: BlockEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|lock_mode_s|쿼리에 사용된 잠금 모드|
|resource_owner_type_s|잠금 소유자.|
|blocked_process_filtered_s|차단된 프로세스 보고서 XML.|
|duration_d|잠금 기간(밀리초).|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights 데이터 집합
[Intelligent Insights 로그 형식에 대해 자세히 알아봅니다.](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>다음 단계

로깅을 사용하도록 설정하는 방법을 학습하고 여러 Azure 서비스에서 지원되는 메트릭과 로그 범주를 이해하려면 다음을 확인합니다.

 * [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Event Hubs에 대해 알아보려면 다음을 확인합니다.

* [Azure Event Hubs 정의](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Storage에 대해 자세히 알아보려면 [Storage에서 메트릭 및 진단 로그 다운로드하는 방법](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)을 참조하세요.
