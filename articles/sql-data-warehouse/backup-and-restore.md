---
title: Azure SQL Data Warehouse 백업 및 복원 - 스냅숏, 지역 중복 | Microsoft Docs
description: Azure SQL Data Warehouse에서 백업 및 복원이 어떻게 작동하는지 알아봅니다. 데이터 웨어하우스 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원합니다. 지역 중복 백업을 사용하여 다른 지역에 복원합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a4f24aad95f13315eaeac790c9006ca00f61af69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187602"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 백업 및 복원
Azure SQL Data Warehouse에서 백업 및 복원이 어떻게 작동하는지 알아봅니다. 데이터 웨어하우스 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원합니다. 지역 중복 백업을 사용하여 다른 지역에 복원합니다. 

## <a name="what-is-backup-and-restore"></a>백업 및 복원이란?
*데이터 웨어하우스 백업*은 데이터 웨어하우스를 복원하는 데 사용할 수 있는 데이터베이스 복사본입니다.  SQL Data Warehouse는 분산 시스템이므로 데이터 웨어하우스 백업은 Azure Storage에 있는 여러 파일로 구성됩니다. 데이터 웨어하우스 백업에는 로컬 데이터베이스 스냅숏과 데이터 웨어하우스와 연결된 모든 데이터베이스 및 파일의 지역 백업이 모두 포함됩니다. 

*데이터 웨어하우스 복원*은 기존 데이터 웨어하우스 또는 삭제된 데이터 웨어하우스의 백업에서 생성되는 새 데이터 웨어하우스입니다. 복원된 데이터 웨어하우스는 특정 시점에 백업된 데이터 웨어하우스를 다시 만듭니다. 데이터 웨어하우스 복원은 데이터가 실수로 손상되거나 삭제된 후 데이터를 다시 만들기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.

로컬 복원 및 지리적 복원 모두 SQL Data Warehouse의 재해 복구 기능에 속합니다. 

## <a name="local-snapshot-backups"></a>로컬 스냅숏 백업
로컬 스냅숏 백업은 서비스에 기본 제공되는 기능입니다.  사용하도록 설정할 필요가 없습니다. 

SQL Data Warehouse는 온종일 데이터 웨어하우스의 스냅숏을 생성합니다. 스냅숏은 7일 동안 사용할 수 있습니다. SQL Data Warehouse는 8시간 RPO(복구 지점 목표)를 지원합니다. 주 지역의 데이터 웨어하우스를 지난 7일 내에 생성된 스냅숏 중 하나로 복원할 수 있습니다.

마지막 스냅숏이 시작한 시간을 보려면 온라인 SQL Data Warehouse에서 이 쿼리를 실행합니다. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>데이터 웨어하우스가 일시 중지된 경우 스냅숏 보존
SQL Data Warehouse는 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏을 만들지 않으며 데이터 웨어하우스가 만료되지 않습니다. 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏 경과 시간이 변하지 않습니다. 스냅숏 보존 기간은 달력 일 수가 아닌 데이터 웨어하우스가 온라인 상태인 일 수를 기반으로 합니다.

예를 들어 스냅숏이 10월 1일 오후 4시에 시작되고 데이터 웨어하우스가 10월 3일 오후 4시에 일시 중지되면 스냅숏 경과 시간은 최대 2일입니다. 데이터 웨어하우스가 다시 온라인 상태가 되면 스냅숏 경과 시간은 2일입니다. 데이터 웨어하우스가 10월 5일 오후 4시에 온라인 상태가 되면 스냅숏 경과 시간은 2일이고 앞으로 5일 동안 유지됩니다.

데이터 웨어하우스가 다시 온라인 상태가 되면 SQL Data Warehouse는 새 스냅숏을 다시 시작하고 7일이 넘은 데이터가 있으면 스냅숏을 만료합니다.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>데이터 웨어하우스가 삭제되는 경우 스냅숏 보존
데이터 웨어하우스를 삭제하면 SQL Data Warehouse에서 최종 스냅숏을 만들어 7일 동안 저장합니다. 데이터 웨어하우스를 삭제 시 생성된 최종 복원 지점으로 복원할 수 있습니다. 

> [!IMPORTANT]
> 논리적 SQL 서버 인스턴스를 삭제하면 이 인스턴스에 속하는 모든 데이터베이스도 삭제되고 복구될 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
> 

## <a name="geo-backups"></a>지역 백업
SQL Data Warehouse는 하루에 한 번 [쌍으로 연결된 데이터 센터](../best-practices-availability-paired-regions.md)로 지역 백업을 수행합니다. 지역 복원의 RPO는 24시간입니다. SQL Data Warehouse가 지원되는 다른 지역에 있는 서버로 지역 백업을 복원할 수 있습니다. 지역 백업을 사용하면 주 지역의 스냅숏에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있습니다.

지역 백업은 기본적으로 켜져 있습니다. 데이터 웨어하우스가 Gen1인 경우 원하는 대로 [옵트아웃](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)할 수 있습니다. 데이터 보호는 기본 제공 보증이므로 Gen2에 대한 지역 백업을 옵트아웃할 수 없습니다.

## <a name="backup-costs"></a>백업 비용
Azure 청구서에는 Azure Premium Storage에 대한 항목과 지역 중복 저장소에 대한 항목이 있습니다. Premium Storage 요금은 주 지역에 데이터를 저장하는 데 드는 총 비용이며, 스냅숏을 포함합니다.  지역 중복 요금은 지역 백업을 저장하는 데 드는 비용을 포함합니다.  

주 데이터 웨어하우스 및 Azure Blob 스냅숏 7일 보관에 드는 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅숏에서 100GB를 사용하면 Azure Premium Storage 요금으로 2TB 데이터에 대한 비용이 청구됩니다. 

> [!NOTE]
> 각 스냅숏은 처음에는 비어 있으며 주 데이터 웨어하우스를 변경할 때마다 증가합니다. 데이터 웨어하우스가 변경되면 모든 스냅숏의 크기가 커집니다. 따라서 변경 비율에 따라 저장소 비용이 증가합니다.
> 
> 

지역 중복 저장소를 사용하는 경우 별도의 저장소 비용이 청구됩니다. 지역 중복 저장소는 표준 RA-GRS(Read-Access Geo Redundant Storage) 요금이 청구됩니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="restoring-from-restore-points"></a>복원 지점에서 복원
각 스냅숏에는 스냅숏이 시작된 시간을 나타내는 복원 지점이 있습니다. 데이터 웨어하우스를 복원하려면 복원 지점을 선택하고 복원 명령을 실행합니다.  

SQL Data Warehouse는 항상 새 데이터 웨어하우스에 백업을 복원합니다. 복원된 데이터 웨어하우스와 현재 데이터 웨어하우스 중 하나를 유지하거나 삭제할 수 있습니다. 현재 데이터 웨어하우스를 복원된 데이터 웨어하우스로 바꾸려면 [ALTER DATABASE(Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)를 MODIFY NAME 옵션과 함께 사용하여 이름을 변경할 수 있습니다. 

데이터 웨어하우스를 복원하려면 [Azure Portal을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-portal.md), [PowerShell을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-powershell.md) 또는 [T-SQL을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-rest-api.md)을 참조하세요.

삭제되거나 일시 중지된 데이터 웨어하우스를 복원하기 위해 [지원 티켓을 만들](sql-data-warehouse-get-started-create-support-ticket.md) 수 있습니다. 


## <a name="geo-redundant-restore"></a>지역 중복 복원
선택한 성능 수준에서 Azure SQL Data Warehouse를 지원하는 모든 영역에 데이터 웨어하우스를 복원할 수 있습니다. 

> [!NOTE]
> 지역 중복 복원을 수행하려면 이 기능에서 옵트아웃(opt out)하지 않아야 합니다.
> 
> 

## <a name="restore-timeline"></a>복원 타임라인
지난 7일 이내의 사용 가능한 복원 지점으로 데이터베이스를 복원할 수 있습니다. 스냅숏은 4~8시간마다 시작되며 7일 동안 사용할 수 있습니다. 7일보다 오래된 스냅숏은 만료되고 해당 복원 지점을 더 이상 사용할 수 없게 됩니다. 

일시 중지된 데이터 웨어하우스에 대해서는 백업이 발생하지 않습니다. 데이터 웨어하우스가 7일 넘게 일시 중지된 경우에는 복원 지점이 없습니다. 

## <a name="restore-costs"></a>복원 비용
복원된 데이터 웨어하우스에 대한 저장소 비용은 Azure Premium Storage 요금으로 청구됩니다. 

복원된 데이터 웨어하우스를 일시 중지하면 저장소에 Azure Premium Storage 요금이 부과됩니다. 일시 중지의 장점은 컴퓨팅 리소스에 대한 요금이 부과되지 않는다는 점입니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="restore-use-cases"></a>복원 사용 사례
데이터 웨어하우스 복원의 주 용도는 실수로 인한 데이터 손실 또는 손상 후 데이터를 복구하는 것입니다. 백업을 7일 넘게 보존하는 데에도 데이터 웨어하우스 복원을 사용할 수 있습니다. 백업이 복원되면 데이터 웨어하우스가 온라인 상태가 되며 계산 비용을 절감하기 위해 이를 무기한 일시 중지할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 저장소 비용이 부과됩니다. 

## <a name="next-steps"></a>다음 단계
재해 계획에 대한 자세한 내용은 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.
