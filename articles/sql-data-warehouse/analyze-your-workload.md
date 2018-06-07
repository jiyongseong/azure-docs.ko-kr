---
title: 워크로드 분석 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse의 워크로드에 대한 쿼리 우선 순위 지정 분석 기술.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6b0d39b81b72615a9522e95558a59007b10bf109
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 워크로드 분석
Azure SQL Data Warehouse의 워크로드에 대한 쿼리 우선 순위 지정 분석 기술.

## <a name="workload-groups"></a>워크로드 그룹 
SQL Data Warehouse는 워크로드 그룹을 사용하여 리소스 클래스를 구현합니다. 다양한 DWU 크기의 리소스 클래스 동작을 제어하는 총 8개의 워크로드 그룹이 있습니다. DWU의 경우 SQL Data Warehouse는 8개의 워크로드 그룹 중 4개만 사용합니다. 이 접근 방법은 각 워크로드 그룹은 4가지 리소스 클래스 smallrc, mediumrc, largerc 또는 xlargerc 중 하나에 할당되기 때문에 타당합니다. 이들 워크로드 그룹의 일부가 더 높은 *중요도*로 설정된다는 점 때문에 워크로드 그룹을 이해하는 것이 중요합니다. 중요도가 CPU 예약에 사용됩니다. 높은 중요도의 쿼리 실행은 중간 중요도의 쿼리 실행보다 3배 더 많은 CPU 사이클이 할당됩니다. 따라서 동시성 슬롯 매핑은 또한 CPU 우선 순위를 결정합니다. 쿼리가 16개 이상의 슬롯을 사용할 경우 중요도 높음으로 실행됩니다.

다음 테이블에서는 각 워크로드 그룹에 대한 중요도 매핑을 보여 줍니다.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>동시성 슬롯 및 중요도에 대한 워크로드 그룹 매핑

| 워크로드 그룹 | 동시성 슬롯 매핑 | MB/배포(탄력성) | MB/배포(계산) | 중요도 매핑 |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | 중간             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | 중간             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | 중간             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | 중간             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | 높음               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | 높음               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | 높음               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | 높음               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | 높음               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
**동시성 슬롯의 할당 및 사용량** 차트는 DW500이 smallrc, mediumrc, largerc 및 xlargerc 각각에 대해 1, 4, 8 또는 16의 동시성 슬롯을 사용한다는 사실을 보여 줍니다. 각 리소스 클래스에 대한 중요도를 확인하기 위해 위 차트에서 해당 값을 확인할 수 있습니다.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500의 리소스 클래스와 중요도 관계
| 리소스 클래스 | 워크로드 그룹 | 사용된 동시성 슬롯 수 | MB/배포 | 중요도 |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | 중간     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | 중간     |
| largerc        | SloDWGroupC03  | 8                      | 800               | 중간     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | 높음       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | 중간     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | 중간     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | 중간     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | 중간     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | 높음       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | 높음       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | 높음       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | 높음       |

## <a name="view-workload-groups"></a>워크로드 그룹 보기
다음 쿼리는 리소스 관리자의 관점에서 메모리 리소스 할당의 세부 정보를 보여 줍니다. 따라서 문제 해결 시 작업 그룹의 현재 및 이전 사용을 분석하는 데 도움이 됩니다.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>큐에 대기 중인 쿼리 검색 및 다른 DMV
`sys.dm_pdw_exec_requests` DMV는 동시성 큐에 대기 중인 쿼리를 식별하는 데 사용할 수 있습니다. 동시성 슬롯을 대기하는 쿼리는 **일시 중단**상태가 됩니다.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

워크로드 관리 역할은 `sys.database_principals`를 사용하여 확인할 수 있습니다.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

다음 쿼리는 각 사용자에게 어떤 역할이 할당되었는지 보여줍니다.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse에 다음과 같은 대기 형식이 있습니다.

* **LocalQueriesConcurrencyResourceType**: 동시성 슬롯 프레임워크 외부에 존재하는 쿼리. `SELECT @@VERSION` 과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.
* **UserConcurrencyResourceType**: 동시성 슬롯 프레임워크 내에 존재하는 쿼리. 최종 사용자 테이블에 대한 쿼리는 이 리소스 형식을 사용하는 예를 나타냅니다.
* **DmsConcurrencyResourceType**: 데이터 이동 작업으로 초래된 대기
* **BackupConcurrencyResourceType**: 이 대기는 데이터베이스가 백업 중임을 나타냅니다. 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다.

`sys.dm_pdw_waits` DMV는 요청이 대기 중인 리소스를 알아내는 데 사용할 수 있습니다.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV는 지정된 쿼리에 사용되는 리소스 대기만 표시합니다. 리소스 대기 시간은 기본 SQL Server가 CPU에 대해 쿼리를 예약하는 데 소요되는 대기 시간을 나타내는 것과 달리 리소스가 제공될 때까지 대기하는 시간만 측정합니다.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
또한 `sys.dm_pdw_resource_waits` DMV를 사용하여 얼마나 많은 동시성 슬롯이 부여되었는지 계산할 수 있습니다.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV는 기록 추세 분석에 사용할 수 있습니다.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>다음 단계
데이터베이스 사용자 및 보안을 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호](sql-data-warehouse-overview-manage-security.md)를 참조하세요. 더 큰 리소스 클래스가 클러스터된 columnstore 인덱스 품질을 향상할 방법에 대한 자세한 내용은 [인덱스를 다시 빌드하여 세그먼트 품질 개선](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)을 참조하세요.


