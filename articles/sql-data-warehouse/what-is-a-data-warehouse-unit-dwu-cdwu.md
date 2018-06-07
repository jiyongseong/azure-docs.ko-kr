---
title: Azure SQL Data Warehouse의 데이터 웨어하우스 단위(DWU, cDWU) | Microsoft Docs
description: 가격 및 성능을 최적화하기 위한 이상적인 데이터 웨어하우스 단위(DWU, cDWU) 수 선택에 대한 권장 사항 및 단위 수를 변경하는 방법
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 94791e4dc3d3c841dde4685d34d4e3fdaf7d9af7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185963"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>DWU(데이터 웨어하우스 단위) 및 cDWU(계산 데이터 웨어하우스 단위)
가격 및 성능을 최적화하기 위한 이상적인 데이터 웨어하우스 단위(DWU, cDWU) 수 선택에 대한 권장 사항 및 단위 수를 변경하는 방법 

## <a name="what-are-data-warehouse-units"></a>데이터 웨어하우스 단위란?
SQL Data Warehouse CPU, 메모리 및 IO는 DWU(데이터 웨어하우스 단위)라는 계산 크기 단위로 함께 제공됩니다. DWU는 계산 리소스 및 성능의 추상적이고 정규화된 측정값을 나타냅니다. 서비스 수준을 변경하면 시스템에 할당된 DWU 수를 변경하여 시스템의 성능과 비용이 조정됩니다. 

성능을 향상하려면 데이터 웨어하우스 단위 수를 늘리면 됩니다. 성능을 낮추려면 데이터 웨어하우스 단위 수를 줄입니다. 저장소 및 계산 비용은 별도로 청구되므로 데이터 웨어하우스 단위를 변경해도 저장소 비용에 영향을 미치지 않습니다.

데이터 웨어하우스 단위의 성능은 다음과 같은 데이터 웨어하우스 워크로드 메트릭을 기반으로 합니다.

- 표준 데이터 웨어하우징 쿼리가 대량의 행을 검색한 후 복잡한 집계를 수행하는 속도. 이 작업은 I/O 및 CPU를 많이 사용합니다.
- 데이터 웨어하우스가 Azure Storage Blob 또는 Azure Data Lake에서 데이터를 수집하는 속도. 이 작업은 네트워크 및 CPU를 많이 사용합니다. 
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 명령이 테이블을 복사하는 속도. 이 작업에는 데이터를 저장소에서 읽어오기, 어플라이언스의 노드 전체에 배포하기, 저장소에 다시 쓰기가 포함됩니다. 이 작업은 CPU, IO 및 네트워크를 많이 사용합니다.

DWU 늘리기:
- 검색, 집계 및 CTAS 문에 대한 시스템 성능을 선형적으로 변경합니다.
- PolyBase 로드 작업용 판독기 및 작성기 수를 늘립니다.
- 동시 쿼리 및 동시성 슬롯의 최대 수를 늘립니다.

## <a name="service-level-objective"></a>서비스 수준 목표
SLO(서비스 수준 목표)는 데이터 웨어하우스의 비용 및 성능 수준을 결정하는 확장성 설정입니다. Gen2에 대한 서비스 수준은 cDWU(계산 데이터 웨어하우스 단위)로 측정됩니다(예: DW2000c). Gen1 서비스 수준은 DWU로 측정됩니다(예: DW2000). 

T-SQL에서 SERVICE_OBJECTIVE 설정은 데이터 웨어하우스에 대한 서비스 수준 및 성능 계층을 결정합니다.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>성능 계층 및 데이터 웨어하우스 단위

각 성능 계층은 약간 다른 데이터 웨어하우스의 측정 단위를 사용합니다. 이러한 차이는 크기 단위를 직접 요금 청구로 변환하므로 송장에 반영됩니다.

- Gen1 데이터 웨어하우스는 DWU(데이터 웨어하우스 단위)로 측정됩니다.
- Gen2 데이터 웨어하우스는 cDWU(계산 데이터 웨어하우스 단위)로 측정됩니다. 

DWU 및 cDWU 모두 계산을 확장 또는 축소할 수 있고 데이터 웨어하우스를 사용할 필요가 없는 경우 계산을 일시 중지할 수 있습니다. 이러한 작업은 모두 주문형 작업입니다. Gen2는 계산 노드에서 로컬 디스크 기반 캐시를 사용하여 성능을 향상시킵니다. 시스템의 크기를 조정하거나 시스템을 일시 중지할 경우 캐시가 무효화되므로 최적의 성능을 얻으려면 캐시 준비 시간이 필요합니다.  

데이터 웨어하우스 단위를 늘리면 계산 리소스가 선형적으로 증가됩니다. Gen2는 최상의 쿼리 성능과 최고의 확장성을 제공하지만 진입 가격이 높습니다. 지속적인 성능이 요구되는 비즈니스용으로 설계되었습니다. 이러한 시스템은 캐시를 최대한 이용합니다. 

### <a name="capacity-limits"></a>용량 제한
각 SQL Server(예: myserver.database.windows.net)에는 특정 데이터 웨어하우스 단위 수를 허용하는 [DTU(데이터베이스 트랜잭션 단위)](../sql-database/sql-database-what-is-a-dtu.md) 할당량이 지정되어 있습니다. 자세한 내용은 [워크로드 관리 용량 제한](sql-data-warehouse-service-capacity-limits.md#workload-management)을 참조하세요.


## <a name="how-many-data-warehouse-units-do-i-need"></a>필요한 데이터 웨어하우스 단위
이상적인 데이터 웨어하우스 단위 수는 워크로드 및 시스템에 로드한 데이터 양에 따라 매우 다릅니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 단계:

1. 더 작은 DWU를 선택하여 시작합니다. 
2. 시스템으로 로드하는 데이터를 테스트할 때 응용 프로그램 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 정기적으로 작업량이 많은 기간에 필요한 추가 요구 사항을 식별합니다. 워크로드가 작업에서 뚜렷한 최고점 및 최저점을 나타내면 크기를 자주 조정하는 것이 좋습니다.

SQL Data Warehouse는 데이터 양 조정이 가능한 대량의 계산 및 쿼리를 프로비전할 수 있는 스케일 아웃 시스템입니다. 특히 큰 DWU에서 진정한 크기 조정 기능을 확인하려면 데이터에 충분한 CPU가 할당되도록 데이터 집합의 크기를 조정하는 것이 좋습니다. 크기 조정 테스트의 경우 1TB 이상을 사용하는 것이 좋습니다.

> [!NOTE]
>
> 계산 노드 간에 작업을 분할할 수 있는 경우 더 많은 병렬 처리를 통해 쿼리 성능만 증가합니다. 크기를 조정해도 성능이 변경되지 않는 경우 테이블 디자인 및/또는 쿼리를 튜닝해야 할 수 있습니다. 쿼리 튜닝 지침에 대해서는 [사용자 쿼리 관리](sql-data-warehouse-overview-manage-user-queries.md)를 참조하세요. 

## <a name="permissions"></a>권한

데이터 웨어하우스 단위를 변경하려면 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)에 설명된 권한이 필요합니다. 

## <a name="view-current-dwu-settings"></a>현재 DWU 설정 보기

현재 DWU 설정을 보려면:

1. Visual Studio에서 SQL Server 개체 탐색기를 엽니다.
2. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
3. sys.database_service_objectives 동적 관리 뷰에서 선택합니다. 다음은 예제입니다. 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>데이터 웨어하우스 단위 변경

### <a name="azure-portal"></a>Azure portal
DWU 또는 cDWU를 변경하려면

1. [Azure Portal](https://portal.azure.com)을 열고 데이터베이스를 연 다음 **크기 조정**을 클릭합니다.

2. **크기 조정**에서 슬라이더를 왼쪽 또는 오른쪽으로 이동해 DWU 설정을 변경합니다.

3. **저장**을 클릭합니다. 확인 메시지가 표시됩니다. **예**를 클릭하여 확인하거나 **아니요**를 클릭하여 취소합니다.

### <a name="powershell"></a>PowerShell
DWU 또는 cDWU를 변경하려면 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell cmdlet을 사용합니다. 다음 예제에서는 MyServer에서 호스트되는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

자세한 내용은 [SQL Data Warehouse용 PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)을 참조하세요.

### <a name="t-sql"></a>T-SQL
T-SQL을 사용하여 현재 DWU 또는 cDWU 설정을 보고, 설정을 변경하고, 진행 상황을 확인할 수 있습니다. 

DWU 또는 cDWU를 변경하려면:

1. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 문을 사용합니다. 다음 예제에서는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

DWU를 변경하려면 [데이터베이스 생성 또는 업데이트](/rest/api/sql/databases/createorupdate) REST API를 사용합니다. 다음 예제에서는 MyServer에서 호스팅되는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

추가 REST API 예제를 보려면 [SQL Data Warehouse용 REST API](sql-data-warehouse-manage-compute-rest-api.md)를 참조하세요.

## <a name="check-status-of-dwu-changes"></a>DWU 변경 상태 확인

DWU 변경을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 자동으로 크기를 조정하는 경우 다른 작업을 진행하기 전에 특정 작업이 완료되었는지 확인하는 논리를 구현하는 것이 좋습니다. 

다양한 끝점을 통해 데이터베이스 상태를 확인하면 자동화를 올바르게 구현할 수 있습니다. 포털에서는 작업 완료 시의 알림과 데이터베이스 현재 상태를 제공하지만 프로그래밍 방식으로 상태를 확인할 수는 없습니다. 

Azure Portal에서 스케일 아웃 작업에 대한 데이터베이스 상태를 확인할 수 없습니다.

DWU 변경 상태를 확인하려면:

1. 논리적 SQL Database 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. 다음 쿼리를 제출하여 데이터베이스 상태를 확인합니다.


```sql
SELECT    *
FROM      sys.databases
;
```

3. 다음 쿼리를 제출하여 작업 상태를 확인합니다.

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

이 DMV는 SQL Data Warehouse에 대해 작업 및 작업 상태(IN_PROGRESS 또는 COMPLETED)와 같은 다양한 관리 작업에 대한 정보를 반환합니다.

## <a name="the-scaling-workflow"></a>크기 조정 워크플로

크기 조정 작업을 시작할 때 시스템은 먼저 열려 있는 모든 세션을 종료하고 일관된 상태를 유지하도록 열려 있는 모든 트랜잭션을 롤백합니다. 크기 조정 작업의 경우 이 트랜잭션 롤백을 완료한 후에만 크기 조정이 수행됩니다.  

- 확장 작업의 경우 시스템은 추가 계산을 프로비전한 다음 저장소 계층에 다시 연결합니다. 
- 축소 작업의 경우 불필요한 노드를 저장소에서 분리하고 나머지 노드에 다시 연결합니다.

## <a name="next-steps"></a>다음 단계
성능 관리에 대한 자세한 내용은 [워크로드 관리에 대한 리소스 클래스](resource-classes-for-workload-management.md) 및 [메모리와 동시성 제한](memory-and-concurrency-limits.md)을 참조하세요.



