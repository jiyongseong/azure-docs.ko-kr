---
title: 최신 세대 Azure SQL Data Warehouse로 업그레이드 | Microsoft Docs
description: Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 58d65ef05ed872bb357070de9866253baea5dc70
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777810"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse를 업그레이드하여 성능 최적화
Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유
이제 Azure Portal에서 SQL Data Warehouse Gen2로 원활하게 업그레이드할 수 있습니다. Gen1 데이터 웨어하우스가 있는 경우 업그레이드하는 것이 좋습니다. 업그레이드하여 최신 버전의 Azure 하드웨어 및 향상된 저장소 아키텍처를 사용할 수 있습니다. 더 빠른 성능, 더 높은 확장성 및 제한 없는 열 형식의 저장소를 활용할 수 있습니다. 

## <a name="applies-to"></a>적용 대상
이 업그레이드는 Gen1 데이터 웨어하우스에 적용됩니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="before-you-begin"></a>시작하기 전에
> [!NOTE]
> Gen2를 사용할 수 있는 지역에 기존 Gen1 데이터 웨어하우스가 없는 경우 PowerShell을 통해 지원되는 지역에 [Gen2로 지역 복원](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)할 수 있습니다.
> 
>

1. 업그레이드 대상인 Gen1 데이터 웨어하우스가 일시 중지된 경우 [데이터 웨어하우스를 다시 시작](pause-and-resume-compute-portal.md)합니다.
2. 몇 분 정도의 가동 중지 시간에 대비합니다. 



## <a name="start-the-upgrade"></a>업그레이드 시작

1. Azure Portal에서 Gen1 데이터 웨어하우스로 이동하여 **Gen2로 업그레이드**를 클릭합니다: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. 기본적으로 데이터 웨어하우스에 대한 **제안된 성능 수준 선택**은 아래에 있는 매핑을 사용하여 Gen1에서 현재 성능 수준을 기반으로 합니다.
    
| Gen1 | Gen2 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. 업그레이드 전에 워크로드가 실행되고 정지되었는지 확인합니다. 데이터 웨어하우스가 Gen2 데이터 웨어하우스로 다시 온라인 상태가 되기 전에 몇 분 동안 가동 중지 시간이 발생합니다. **업그레이드를 클릭합니다**. Gen2 성능 계층의 가격은 현재 미리 보기 기간 중 절반 가격입니다.
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal에서 상태를 확인하여 **업그레이드를 모니터링**합니다.

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   비율 크기 조정 작업(“업그레이드 - 오프라인”)을 통해 업그레이드 프로세스의 첫 단계가 진행됩니다. 여기서는 모든 세션이 종료되며 연결이 삭제됩니다. 
   
   업그레이드 프로세스의 두 번째 단계는 데이터 마이그레이션(“업그레이드 - 온라인”)입니다. 데이터 마이그레이션은 지속적인 온라인 백그라운드 프로세스로, 로컬 SSD 캐시를 활용하여 열 형식의 데이터가 이전 저장소 아키텍처에서 새로운 저장소 아키텍처로 느리게 이동합니다. 이 시간 동안 데이터 웨어하우스는 쿼리 및 로딩을 위해 온라인 상태가 됩니다. 모든 데이터는 마이그레이션 여부에 관계 없이 쿼리에 사용할 수 있습니다. 데이터 마이그레이션은 데이터 크기, 성능 수준 및 columnstore 세그먼트의 수에 따라 다양한 속도로 발생합니다. 

5. SQL 데이터베이스 찾아보기 블레이드를 사용하여 **Gen2 데이터 웨어하우스 찾기**합니다. 

> [!NOTE]
> Gen2 데이터 웨어하우스가 SQL 데이터 웨어하우스 찾아보기 블레이드에 표시되지 않는 경우 현재 문제가 있습니다. 새로 업그레이드된 Gen2 데이터 웨어하우스를 찾으려면 SQL 데이터베이스 찾아보기 블레이드를 사용하세요. 이 수정을 위해 적극 노력하고 있습니다.
> 

6. **선택적 권장 사항:** 데이터 마이그레이션 백그라운드 프로세스를 더 신속히 처리하려면 더 큰 SLO 및 리소스 클래스의 모든 columnstore 테이블에서 [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index)를 실행하여 데이터 이동을 즉시 실행하는 것이 좋습니다. 이 작업은 지속적인 백그라운드 프로세스와 달리 오프라인 상태이지만 데이터 마이그레이션은 훨씬 더 빨라집니다. 고품질 행 그룹으로 완료되면 새롭게 향상된 저장소 아키텍처의 장점을 충분히 활용할 수 있습니다. 

이 다음 쿼리는 데이터 마이그레이션 프로세스를 더 신속히 처리하기 위해 필요한 Alter Index Rebuild 명령을 생성합니다.

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>다음 단계
업그레이드한 데이터 웨어하우스가 온라인 상태입니다. 향상된 아키텍처를 이용하려면 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
 
