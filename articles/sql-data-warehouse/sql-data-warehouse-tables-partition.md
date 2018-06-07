---
title: Azure SQL Data Warehouse의 테이블 분할 | Microsoft Docs
description: Azure SQL Data Warehouse의 테이블 파티션을 사용하기 위한 권장 사항 및 예제
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ada55950ee36222e70809e2ef423c63612cd61ed
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>SQL Data Warehouse의 테이블 분할
Azure SQL Data Warehouse의 테이블 파티션을 사용하기 위한 권장 사항 및 예제

## <a name="what-are-table-partitions"></a>테이블 파티션이란?
테이블 파티션을 사용하면 데이터를 더 작은 데이터 그룹으로 나눌 수 있습니다. 대부분의 경우 테이블 파티션은 날짜 열에 만들어집니다. 분할은 클러스터형 columnstore, 클러스터형 인덱스 및 힙을 포함하는 모든 SQL Data Warehouse 테이블 형식에서 지원됩니다. 또한 해시 또는 라운드 로빈 배포를 비롯한 모든 배포 유형에서도 분할이 지원됩니다.  

분할은 데이터 유지 관리 및 쿼리 성능에 도움이 될 수 있습니다. 분할이 이러한 두 가지 측면 모두에 효과적인지 아니면 한 가지 측면에만 효과적인지는 데이터의 로드 방식, 동일한 열이 두 가지 용도로 사용될 수 있는지에 따라 좌우됩니다. 분할은 한 열에 대해서만 수행할 수 있기 때문입니다.

### <a name="benefits-to-loads"></a>로드에 대한 이점
Microsoft Azure SQL Data Warehouse에서 분할이 가져오는 주요 이점은 파티션 삭제, 전환 및 병합을 사용하여 데이터 로드의 효율성과 성능을 향상시킨다는 것입니다. 대부분의 경우 데이터는 데이터가 데이터베이스에 로드되는 순서에 밀접하게 관련된 날짜 열에서 분할됩니다. 파티션을 사용하여 데이터를 유지 관리할 때의 가장 큰 장점 중 하나는 트랜잭션 로깅이 방지된다는 것입니다. 단순히 데이터를 삽입, 업데이트 또는 삭제하는 것이 생각과 노력이 거의 필요하지 않은 가장 간단한 방법일 수 있지만, 로드 프로세스 중에 분할을 사용하면 성능을 크기 향상시킬 수 있습니다.

파티션 전환을 사용하여 테이블 섹션을 빠르게 제거하거나 바꿀 수 있습니다.  예를 들어 판매 팩트 테이블은 지난 36개월 동안의 데이터만 포함할 수 있습니다. 매월 말에 가장 오래된 달의 판매 데이터는 테이블에서 삭제됩니다.  이 데이터는 delete 문을 사용하여 가장 오래된 월의 데이터를 삭제하는 방식으로 삭제할 수 있습니다. 그러나 delete 문을 사용하여 행별로 대량의 데이터를 삭제하는 데 시간이 오래 걸릴 수 있으며, 문제가 있는 경우 롤백하는 데 시간이 오래 걸리는 대형 트랜잭션이 발생할 위험도 높아집니다. 좀 더 최적의 접근 방법은 데이터의 가장 오래된 파티션을 삭제하는 것입니다. 개별 행을 삭제하는 데는 몇 시간이 걸리지만 전체 파티션을 삭제하는 데는 몇 초면 충분합니다.

### <a name="benefits-to-queries"></a>쿼리에 대한 이점
분할은 쿼리 성능 향상을 위해서도 사용할 수 있습니다. 필터를 분할된 데이터에 적용하는 쿼리는 검색을 적격한 파티션에만 제한할 수 있습니다. 이 필터링 방법은 전체 테이블 검색을 방지하고 더 작은 데이터 하위 집합만을 검색할 수 있습니다. 클러스터형 columnstore 인덱스가 사용되면서 조건자 제거에 따른 성능상의 이점은 줄어들었지만 경우에 따라 쿼리에는 도움이 될 수 있습니다. 예를 들어 판매 팩트 테이블은 판매 날짜 필드를 사용하여 36개월로 분할된 다음 판매 날짜를 필터링하는 쿼리가 필터와 일치하지 않는 파티션의 검색을 건너뛸 수 있습니다.

## <a name="sizing-partitions"></a>파티션 크기 조정
일부 시나리오에서 분할을 사용하여 성능을 향상시킬 수 있지만 **너무 많은** 파티션이 있는 테이블을 만들면 경우에 따라 성능이 저하될 수 있습니다.  특히 클러스터형 columnstore 테이블에서 이러한 점이 우려됩니다. 분할이 도움이 되려면 분할을 사용하는 시기 및 만들려는 파티션 수를 이해하는 것이 중요합니다. 파티션 수가 너무 많은 경우와 관련해서는 엄격한 규칙이 없지만 데이터 및 동시에 로드하는 파티션 수에 따라 달라집니다. 성공적인 파티션 구성표에는 일반적으로 수천 개가 아닌 수십 개에서 수백 개의 파티션이 있습니다.

**클러스터형 columnstore** 테이블에서 파티션을 만들 때 각 파티션에 얼마나 많은 행 수를 둘지 고려하는 것은 중요합니다. 클러스터형 columnstore 테이블에 대한 최적의 압축 및 성능을 고려할 때, 배포 및 파티션당 최소 1백만 개의 행이 필요합니다. 파티션이 생성되기 전에 SQL Data Warehouse는 미리 각 테이블을 60개의 분산된 데이터베이스로 나눕니다. 백그라운드에서 생성된 배포 외에, 테이블에 분할이 추가됩니다. 이 예제에서 사용 판매 팩트 테이블이 36개의 월별 파티션을 포함하고 SQL Data Warehouse에 60개의 배포판이 있다면 판매 팩트 테이블은 모든 달이 채워지는 경우 매월 6천만 개의 행 또는 21억 개의 행을 포함합니다. 테이블이 파티션당 권장되는 최소 행 수보다 적은 행을 포함하면 파티션당 행 수를 늘리기 위해 더 적은 수의 파티션을 사용할 것을 고려해야 합니다. 자세한 내용은 [인덱싱](sql-data-warehouse-tables-index.md) 문서를 참조하세요. 여기에는 클러스터 columnstore 인덱스의 품질을 평가할 수 있는 쿼리가 포함되어 있습니다.

## <a name="syntax-differences-from-sql-server"></a>SQL Server와의 구문 차이
SQL Data Warehouse는 SQL Server보다 간단하게 파티션을 정의하는 방법을 소개합니다. 파티션 함수 및 구성표는 SQL Server에서는 사용되지만 SQL Data Warehouse에서는 사용되지 않습니다. 대신 분할된 열 및 경계 지점을 식별하기만 하면 됩니다. 분할의 구문은 SQL Server와 약간 다르지만 기본 개념은 동일합니다. SQL Server 및 SQL Data Warehouse는 테이블당 하나의 파티션 열(범위가 지정된 파티션)을 지원합니다. 분할에 대한 자세한 내용은 [분할된 테이블 및 인덱스](/sql/relational-databases/partitions/partitioned-tables-and-indexes)를 참조하세요.

다음 예제에서는 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) 문을 사용하여 OrderDateKey 열에서 FactInternetSales 테이블을 분할합니다.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>SQL Server에서 분할 마이그레이션
SQL Server 파티션 정의를 SQL Data Warehouse에 마이그레이션하려면

- SQL Server [파티션 구성표](/sql/t-sql/statements/create-partition-scheme-transact-sql)를 제거합니다.
- [파티션 함수](/sql/t-sql/statements/create-partition-function-transact-sql) 정의를 CREATE TABLE에 추가합니다.

SQL Server 인스턴스에서 분할된 테이블을 마이그레이션하는 경우 다음 SQL이 각 파티션에 있는 행의 수를 파악하는 데 도움이 될 수 있습니다. Microsoft Azure SQL Data Warehouse에서 동일한 분할 세분성이 사용될 경우 파티션당 행 수가 60의 배율로 줄어듭니다.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>워크로드 관리
테이블 파티션 결정 시 고려해야 하는 마지막 부분이 [워크로드 관리](resource-classes-for-workload-management.md)입니다. SQL Data Warehouse의 워크로드 관리는 주로 메모리 및 동시성에 대한 관리입니다. Microsoft Azure SQL Data Warehouse에서 쿼리 실행 중 각 배포에 할당된 최대 메모리는 리소스 클래스에 의해 제어됩니다. 이상적으로 파티션은 클러스터형 columnstore 인덱스 빌드에 필요한 메모리 등의 다른 요소를 고려해서 크기가 조정됩니다. 더 많은 메모리가 할당되면 클러스터형 columnstore 인덱스에 훨씬 유리합니다. 따라서 파티션 인덱스 다시 작성이 메모리를 부족하게 하지 않아야 합니다. 쿼리에 사용할 수 있는 메모리의 양을 늘리면 기본 역할, smallrc에서 다른 역할 중 하나(예: largerc)로 전환하여 구현할 수 있습니다.

배포 당 메모리의 할당에 관한 정보는 Resource Governor 동적 관리 뷰를 쿼리하여 사용할 수 있습니다. 실제로 메모리 부여는 다음 쿼리의 결과보다 작습니다. 하지만 이 쿼리는 데이터 관리 작업에 대한 파티션의 크기를 조정할 때 사용할 수 있는 수준의 지침을 제공합니다. 초대형 리소스 클래스에서 제공하는 메모리를 초과하는 파티션의 크기는 피합니다. 파티션이 이 수치를 초과하면 메모가 부족하여 압축 효율이 떨어질 위험이 있습니다.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>파티션 전환
SQL Data Warehouse는 파티션 분할, 병합 및 전환을 지원합니다. 이러한 각 함수는 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) 문을 사용하여 실행됩니다.

두 테이블 간에 파티션을 전환하려면 파티션을 각 해당 경계에 맞추고 테이블 정의와 일치하는지 확인해야 합니다. Check 제약 조건은 테이블에 있는 값의 범위를 적용하는 데 사용할 수 없으므로 원본 테이블은 대상 테이블과 동일한 파티션 경계를 포함해야 합니다. 파티션 경계가 동일하지 않으면 파티션 전환은 파티션 메타데이터가 동기화되지 않아서 실패합니다.

### <a name="how-to-split-a-partition-that-contains-data"></a>데이터를 포함하는 파티션을 분할하는 방법
데이터가 이미 들어 있는 파티션을 분할 하는 가장 효율적인 방법은 `CTAS` 문을 사용하는 것입니다. 분할된 테이블이 클러스터형 columnstore인 경우 테이블 파티션이 비어 있어야 분할될 수 있습니다.

다음 예제에서는 분할된 columnstore 테이블을 만듭니다. 각 파티션에 하나의 행을 삽입합니다.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> 통계 개체를 만들면 테이블 메타 데이터가 더 정확해집니다. 통계를 생략하면 SQL Data Warehouse는 기본값을 사용합니다. 통계에 대한 자세한 내용은 [통계](sql-data-warehouse-tables-statistics.md)를 검토하세요.
> 
> 

다음 쿼리는 `sys.partitions` 카탈로그 뷰를 사용하여 행 수를 찾습니다.

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

다음 SPLIT 명령은 오류 메시지를 수신합니다.

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

파티션이 비어 있어 Msg 35346, 수준 15, 상태 1, 44행 ALTER PARTITION 문의 SPLIT 절이 실패했습니다. Columnstore 인덱스가 테이블에 있는 경우 빈 파티션만 분할할 수 있습니다. ALTER PARTITION 문을 실행한 다음 ALTER PARTITION 완료된 후에 Columnstore 인덱스를 다시 작성하기 전에 Columnstore 인덱스를 비활성화하는 것이 좋습니다.

그러나 `CTAS`를 사용하여 데이터를 저장할 새 테이블을 만들 수 있습니다.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

파티션 경계가 정렬되므로 전환이 허용됩니다. 이렇게 하면 이후에 분할할 수 있는 빈 파티션이 원본 테이블에 남습니다.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

`CTAS`를 사용하여 새로운 파티션 경계에 데이터를 정렬한 다음, 데이터를 다시 기본 테이블로 전환하는 것이 남았습니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

데이터 이동이 완료되면 대상 테이블에 대한 통계를 새로 고치는 것이 좋습니다. 통계를 업데이트하면 해당 파티션의 새로운 데이터 배포가 통계에 정확하게 반영됩니다.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>소스 제어를 분할하는 테이블
소스 제어 시스템에서 테이블 정의가 **부식되지** 않도록 다음 방법을 고려하는 것이 좋습니다.

1. 파티션 값이 없는 분할된 테이블로 테이블을 만듭니다.

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` 배포 프로세스의 일부로서의 테이블:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

이 방법으로 소스 제어의 코드는 정적으로 유지되며 파티션 경계 값은 동적이 될 수 있습니다. 시간이 지남에 따라 웨어하우스가 발전됩니다.

## <a name="next-steps"></a>다음 단계
테이블 개발에 대한 자세한 내용은 [테이블 개요](sql-data-warehouse-tables-overview.md)에 대한 문서를 참조하세요.

