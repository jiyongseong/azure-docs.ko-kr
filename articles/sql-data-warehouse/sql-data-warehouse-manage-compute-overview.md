---
title: Azure SQL Data Warehouse에서 계산 리소스 관리 | Microsoft Docs
description: Azure SQL Data Warehouse의 성능 확장 기능을 알아봅니다. DWU를 조정하여 확장하거나 데이터 웨어하우스를 일시 중지하여 비용을 절약합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ca6d34d3b670bfd05a9b65fe9e6b260120e3a5b8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 계산 관리
Azure SQL Data Warehouse에서 계산 리소스를 관리하는 방법에 대해 알아봅니다. 데이터 웨어하우스를 일시 중지하여 비용을 절약하거나 성능 요구 사항에 맞게 데이터 웨어하우스의 크기를 조정합니다. 

## <a name="what-is-compute-management"></a>계산 관리란?
SQL Data Warehouse는 저장소와 계산을 분리하여 각각의 성능을 독립적으로 조정할 수 있습니다. 이에 따라 데이터 저장소와는 관계없이 성능 요구 사항에 맞게 계산의 크기를 조정할 수 있습니다. 또한 계산 리소스를 일시 중지했다가 다시 시작할 수도 있습니다. 이 아키텍처의 당연한 결과는 계산 및 저장소에 대한 [청구](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)가 분리되어 있다는 것입니다. 한동안 데이터 웨어하우스를 사용할 필요가 없으면 계산을 일시 중지하여 계산 비용을 절약할 수 있습니다. 

## <a name="scaling-compute"></a>계산 크기 조정
데이터 웨어하우스에 대한 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md) 설정을 조정하여 계산 크기를 확장 또는 축소할 수 있습니다. 데이터 웨어하우스 단위를 더 추가함에 따라 로드 및 쿼리 성능이 선형적으로 증가할 수 있습니다. 

확장 단계는 [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) 또는 [T-SQL](quickstart-scale-compute-tsql.md) 빠른 시작을 참조하세요. 또한 확장 작업은 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)를 통해 수행할 수도 있습니다.

SQL Data Warehouse는 크기 조정 작업을 수행하기 위해 먼저 들어오는 모든 쿼리를 종료한 다음, 트랜잭션을 롤백하여 일관된 상태를 보장합니다. 크기 조정은 트랜잭션 롤백이 완료된 후에만 수행됩니다. 크기 조정 작업의 경우 시스템은 저장소 계층을 계산 노드에서 분리하고, 계산 노드를 추가한 다음, 저장소 계층을 계산 계층에 다시 연결합니다. 각 데이터 웨어하우스는 계산 노드에 고르게 배포되어 60개의 배포로 저장됩니다. 계산 노드를 더 추가할수록 계산 성능이 더 많이 추가됩니다. 계산 노드 수가 늘어나면 계산 노드당 배포 수가 줄어들어 쿼리에 대한 계산 능력이 더 많이 제공됩니다. 마찬가지로, 데이터 웨어하우스 단위를 줄이면 계산 노드 수가 줄어들어 쿼리에 대한 계산 리소스가 줄어듭니다.

다음 표에서는 데이터 웨어하우스 단위를 변경할 때 계산 노드당 배포 수가 변경되는 방식을 보여 줍니다.  6,000DWU는 60개 계산 노드를 제공하며, 100DWU보다 훨씬 높은 쿼리 성능을 달성합니다. 

| DWU(데이터 웨어하우스 단위)  | \#(계산 노드 수) | 노드당 배포 수 \# |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>올바른 데이터 웨어하우스 단위 크기 찾기

확장, 특히 더 큰 데이터 웨어하우스 단위의 성능상 이점을 알아보기 위해 최소한 1TB의 데이터 집합을 사용하려고 합니다. 데이터 웨어하우스에 가장 적합한 수의 데이터 웨어하우스 단위를 찾으려면 크기를 늘리거나 줄여봅니다. 데이터를 로드한 후 서로 다른 데이터 웨어하우스 단위 수를 사용하여 몇 가지 쿼리를 실행합니다. 크기 조정이 빠르기 때문에 1시간 이내에 다양한 성능 수준을 시험해 볼 수 있습니다. 

최적의 데이터 웨어하우스 단위 수를 찾는 데 권장되는 사항은 다음과 같습니다.

- 개발 중인 데이터 웨어하우스의 경우 우선 더 적은 수의 데이터 웨어하우스 단위를 선택하여 시작합니다.  적합한 시작 지점은 DW400 또는 DW200입니다.
- 응용 프로그램 성능을 모니터링하여 선택한 데이터 웨어하우스 단위 수와 관찰한 성능을 비교합니다.
- 선형 크기 조정을 가정하고 데이터 웨어하우스 단위를 늘리거나 줄이는 데 필요한 크기를 결정합니다. 
- 비즈니스 요구 사항에 맞는 최적 성능 수준에 도달할 때까지 계속 조정합니다.

## <a name="when-to-scale-out"></a>확장하는 경우
데이터 웨어하우스 단위를 확장하는 경우 성능 측면에 미치는 영향은 다음과 같습니다.

- 검색, 집계 및 CTAS 문에 대한 시스템 성능을 선형적으로 향상시킵니다.
- 데이터를 로드하기 위해 판독기 및 기록기의 수를 늘립니다.
- 동시 쿼리 및 동시성 슬롯의 최대 수

데이터 웨어하우스 단위를 확장하는 경우 권장 사항은 다음과 같습니다.

- 대량의 데이터 로드 또는 변환 작업을 수행하기 전에 데이터를 더 빠르게 사용할 수 있도록 확장합니다.
- 가장 바쁜 업무 시간에 많은 수의 동시 쿼리를 수용할 수 있도록 확장합니다. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>확장으로도 성능이 향상되지 않는 경우

데이터 웨어하우스 단위를 추가하면 병렬 처리가 증가합니다. 작업이 계산 노드 간에 균등하게 분할되면 추가 병렬 처리로 인해 쿼리 성능이 향상됩니다. 확장을 통해 성능이 변경되지 않는 경우 이러한 상황이 발생할 수 있는 몇 가지 이유가 있습니다. 데이터가 배포를 통해 왜곡되거나 쿼리에서 대량의 데이터 이동이 발생할 수 있습니다. 쿼리 성능 문제를 조사하려면 [성능 문제 해결](sql-data-warehouse-troubleshoot.md#performance)을 참조하세요. 

## <a name="pausing-and-resuming-compute"></a>컴퓨팅 일시 중지 및 다시 시작
계산을 일시 중지하면 저장소 계층이 계산 노드에서 분리됩니다. 계산 리소스는 계정에서 해제됩니다. 계산이 일시 중지된 동안에는 계산 비용이 청구되지 않습니다. 계산을 다시 시작하면 저장소를 계산 노드에 다시 연결하여 계산 비용에 대한 청구를 다시 시작합니다. 데이터 웨어하우스를 일시 중지하는 경우 다음과 같이 진행됩니다.

* Compute 및 메모리 리소스가 데이터 센터에서 사용 가능한 리소스의 풀에 반환됩니다.
* 일시 중지 기간 동안에는 데이터 웨어하우스 단위 비용이 0입니다.
* 데이터 저장소에는 영향이 없으며 데이터는 그대로 유지됩니다. 
* SQL Data Warehouse가 실행 중이거나 큐에 있는 모든 작업을 취소합니다.

데이터 웨어하우스를 다시 시작하는 경우 다음과 같이 진행됩니다.

* SQL Data Warehouse는 데이터 웨어하우스 단위 설정에 대한 계산 및 메모리 리소스를 확보합니다.
* 데이터 웨어하우스 단위에 대한 계산 비용 청구가 다시 시작됩니다.
* 데이터를 사용할 수 있게 됩니다.
* 데이터 웨어하우스가 온라인 상태가 되면 작업 쿼리를 다시 시작해야 합니다.

데이터 웨어하우스에 항상 액세스할 수 있게 하려면 일시 중지하는 대신 가장 작은 크기로 축소하는 것이 좋습니다. 

일시 중지 및 다시 시작 단계는 [Azure Portal](pause-and-resume-compute-portal.md) 또는 [PowerShell](pause-and-resume-compute-powershell.md) 빠른 시작을 참조하세요. 또한 [일시 중지 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) 또는 [다시 시작 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)를 사용할 수도 있습니다.

## <a name="drain-transactions-before-pausing-or-scaling"></a>일시 중지 또는 크기 조정 전 트랜잭션 비우기
일시 중지 또는 크기 조정 작업을 시작하기 전에 기존 트랜잭션이 완료되도록 허용하는 것이 좋습니다.

SQL Data Warehouse를 일시 중지하거나 크기를 조정하는 경우 일시 중지 또는 크기 조정 요청을 시작하면 백그라운드에서 쿼리가 취소됩니다.  간단한 SELECT 쿼리를 취소하는 것은 빠른 작업이며 인스턴스를 일시 중지하거나 규모를 조정하는 데 소요되는 시간에 거의 영향을 주지 않습니다.  하지만 트랜잭션 쿼리는 데이터 또는 데이터 구조를 수정하므로 신속하게 중지되지 않을 수 있습니다.  **기본적으로 트랜잭션 쿼리는 전체를 완료하거나 변경 사항을 롤백해야 합니다.**  트랜잭션 쿼리로 완료된 작업을 롤백하는 데는 쿼리가 적용된 원래 변경 사항보다 시간이 훨씬 더 오래 걸릴 수 있습니다.  예를 들어 행을 삭제하고 1시간 동안 실행 중인 쿼리를 취소하는 경우 시스템에서 삭제된 행을 다시 삽입하는 데 1시간이 소요될 수 있습니다.  트랜잭션이 진행 중인 동안 일시 중지 또는 크기 조정을 실행할 경우 일시 중지 및 크기 조정 시 롤백이 완료될 때까지 기다린 후 진행할 수 있으므로 시간이 오래 소요되는 것처럼 보일 수 있습니다.

[트랜잭션 이해](sql-data-warehouse-develop-transactions.md) 및 [트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md)도 참조하세요.

## <a name="automating-compute-management"></a>계산 관리 자동화
계산 관리 작업을 자동화하려면 [Azure Functions를 사용하여 계산 관리](manage-compute-with-azure-functions.md)를 참조하세요.

각각의 확장, 일시 중지 및 다시 시작 작업을 완료하는 데 몇 분이 걸릴 수 있습니다. 자동으로 크기 조정, 일시 중지 또는 다시 시작하는 경우, 다른 작업을 진행하기 전에 특정 작업이 완료되었는지 확인하는 논리를 구현하는 것이 좋습니다. 다양한 엔드포인트를 통해 데이터 웨어하우스 상태를 확인하면 이러한 작업의 자동화를 올바르게 구현할 수 있습니다. 

데이터 웨어하우스 상태를 확인하려면 [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) 또는 [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) 빠른 시작을 참조하세요. 또한 [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state)를 사용하여 데이터 웨어하우스 상태를 확인할 수도 있습니다.


## <a name="permissions"></a>권한

데이터 웨어하우스 크기를 조정하려면 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)에서 설명하는 권한이 필요합니다.  일시 중지하고 다시 시작하려면 [SQL DB 참가자](../role-based-access-control/built-in-roles.md#sql-db-contributor) 권한, 특히 Microsoft.Sql/servers/databases/action이 필요합니다.


## <a name="next-steps"></a>다음 단계
계산 리소스를 관리하는 또 다른 측면은 개별 쿼리에 대해 서로 다른 계산 리소스를 할당하는 것입니다. 자세한 내용은 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
