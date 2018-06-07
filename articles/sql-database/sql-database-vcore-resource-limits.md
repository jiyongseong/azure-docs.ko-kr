---
title: Azure SQL Database vCore 기반 리소스 제한 | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database에 대한 몇 가지 일반적인 vCore 기반 리소스 제한을 설명합니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/15/2018
ms.author: carlrab
ms.openlocfilehash: d225af55a705d56a94bb0e8dcfcc938b64f3633a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210803"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Azure SQL Database vCore 기반 구매 모델 제한(미리 보기)

> [!IMPORTANT]
> DTU 기반 구매 모델 제한의 경우 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md)을 참조하세요.

## <a name="single-database-storage-sizes-and-performance-levels"></a>단일 데이터베이스: 저장소 크기 및 성능 수준

다음 표에서는 각 서비스 계층과 성능 수준에서 단일 데이터베이스에 대해 사용할 수 있는 리소스를 나타냅니다. [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) 또는 [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 성능 수준 및 저장소 용량을 설정할 수 있습니다.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1536|3072|4096|4096|
|최대 로그 크기|307|307|461|922|1,229|1,229|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|7000|7000|
|최대 동시 작업자(요청)|200|400|800|1600|3200|4800|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|000
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|48|80|
|메모리(GB)|11|22|44|88|132|176|264|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|최대 로그 크기|307|307|461|614|1,229|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|6000|7000|7000|7000|
|최대 동시 작업자(요청)|200|400|800|1600|2400|3200|4800|8000|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|복제본 수|1|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1|2|4|8|20|36|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기|307|307|307|307|307|307|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|80,000|120000|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 동시 작업자(요청)|200|400|800|1600|3200|4800|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|복제본 수|3|3|3|3|3|3|
|다중 AZ|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|48|80|
|메모리(GB)|11|22|44|88|132|176|264|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1.571|3.142|6.284|15.768|25.252|37.936|68.104|131.64|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|최대 로그 크기|307|307|307|307|614|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|60000|80,000|120000|200000
|최대 동시 작업자(요청)|200|400|800|1600|2400|3200|4800|8000|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|복제본 수|1|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

## <a name="single-database-change-storage-size"></a>단일 데이터베이스: 저장소 크기 변경

- 저장소는 1GB 증분 단위로 최대 크기 제한까지 프로비전할 수 있습니다. 구성 가능한 최소 데이터 저장소는 5GB입니다. 
- 단일 데이터베이스에 대한 저장소는 [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), 또는 [REST API](/rest/api/sql/databases/update)를 사용하여 해당 최대 크기를 늘리거나 줄여서 프로비전할 수 있습니다.
- SQL Database는 로그 파일에 대해 추가 저장소의 30% 및 TempDB에 대해 vCore당 32GB를 자동으로 할당하지만 384GB를 초과하지 않도록 합니다. TempDB는 모든 서비스 계층의 연결형 SSD에 있습니다.
- 단일 데이터베이스에 대한 저장소의 가격은 데이터 저장소 및 로그 저장소의 용량 합계에 해당 서비스 계층의 저장소 단가를 곱한 값입니다. TempDB의 비용은 vCore 가격에 포함됩니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

## <a name="single-database-change-vcores"></a>단일 데이터베이스: vCore 수 변경

vCore 수를 처음 선택한 후에는 [Azure Portal](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update) 또는 [REST API](/rest/api/sql/databases/update)를 사용하여 실제 환경에 따라 단일 데이터베이스를 동적으로 늘리거나 줄일 수 있습니다. 

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하게 되면 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 전환 시간은 다양하지만, 일반적으로 30초보다 작은 4초 미만이 해당 시간의 99%를 차지하고 있습니다. 연결을 사용할 수 없는 짧은 시간에 많은 수의 항공편 트랜잭션이 있으면 전환 시간이 더 길어질 수 있습니다. 

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 범용 서비스 계층으로, 이 계층에서 또는 이 계층 내에서 변경되는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 중요 비즈니스 계층 내에서 성능 수준을 변경하는 동일한 크기의 데이터베이스에 대한 강화는 3시간 내에 완료되어야 합니다.

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [SQL REST API를 사용하여 작업 관리](/rest/api/sql/Operations/List), [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL을 사용하여 작업 모니터링](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)과 두 가지 PowerShell 명령 즉, [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 및 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)를 참조하세요.

* 상위 서비스 계층이나 성능 수준으로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한 최대 데이터베이스 크기는 증가하지 않습니다.
* 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 성능 수준의 최대 허용 크기보다 작아야 합니다. 
* [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다.
* [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 다운그레이드하는 경우, 보조 데이터베이스를 다운그레이드하기 전에 먼저 해당 주 데이터베이스를 원하는 성능 계층으로 다운그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드할 때 주 데이터베이스를 먼저 다운그레이드해야 합니다.
* 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>탄력적 풀: 저장소 크기 및 성능 수준

다음 표에서는 SQL Database 탄력적 풀에 대해 각 서비스 계층과 성능 수준에서 사용할 수 있는 리소스를 나타냅니다. [Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) 또는 [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api)를 사용하여 서비스 계층, 성능 수준 및 저장소 용량을 설정할 수 있습니다.

> [!NOTE]
> 탄력적 풀의 개별 데이터베이스에 대한 리소스 제한은 일반적으로 성능 수준이 동일한 풀 외부의 단일 데이터베이스에 대한 리소스 제한과 동일합니다. 예를 들어 GP_Gen4_1 데이터베이스에 대한 최대 동시 작업자 수는 200명입니다. 따라서 GP_Gen4_1 풀의 데이터베이스에 대한 최대 동시 작업자 수도 200명입니다. GP_Gen4_1 풀에 대한 총 동시 작업자 수는 210명입니다.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|최대 데이터 크기(GB)|512|756|1536|2048|3584|4096|
|최대 로그 크기|154|227|461|614|1,075|1,229|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|7000|7000|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 동시 작업자(요청)|210|420|840|1,680|3,360|5040|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|최대 풀 밀도|100|200|500|500|500|500|
|최소/최대 탄력적 풀 클릭 중지 수|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|48|80|
|메모리(GB)|11|22|44|88|132|176|264|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|최대 데이터 크기(GB)|512|756|1536|2048|3072|4096|4096|4096|
|최대 로그 크기|154|227|461|614|922|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|6000|7000|7000|7000|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 동시 작업자(요청)|210|420|840|1,680|2520|3,360|5040|8400
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|최대 풀 밀도|100|200|500|500|500|500|500|500|
|최소/최대 탄력적 풀 클릭 중지 수|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|복제본 수|1|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1|2|4|8|20|36|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기|307|307|307|307|307|307|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|80,000|120000|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 동시 작업자(요청)|210|420|840|1,680|3,360|5040|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|최대 풀 밀도|해당 없음|50|100|100|100|100|
|최소/최대 탄력적 풀 클릭 중지 수|해당 없음|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|다중 AZ|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|48|80|
|메모리(GB)|11|22|44|88|132|176|264|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1.571|3.142|6.284|15.768|25.252|37.936|68.104|131.64|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|최대 로그 크기|307|307|307|307|614|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|60000|80,000|120000|200000
|최대 동시 작업자(요청)|210|420|840|1,680|2520|3,360|5040|8400|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|최대 풀 밀도|해당 없음|50|100|100|100|100|100|100|
|최소/최대 탄력적 풀 클릭 중지 수|해당 없음|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|다중 AZ|예|예|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|예|예|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

탄력적 풀의 모든 vCore가 사용 중인 경우 풀의 각 데이터베이스는 동일한 양의 계산 리소스를 받아서 쿼리를 처리합니다. SQL Database 서비스는 같은 분량의 계산 시간을 보장하여 데이터베이스 간의 공정성을 공유할 리소스를 제공합니다. 데이터베이스당 최소 vCore 수가 0이 아닌 값으로 설정되면, 탄력적 풀 리소스 공유 공정성이 각 데이터베이스에 대해 보장된 리소스의 양에 추가됩니다.

### <a name="database-properties-for-pooled-databases"></a>풀링된 데이터베이스에 대한 데이터베이스 속성

다음 표에서 풀링된 데이터베이스에 대한 속성을 설명합니다.

| 자산 | 설명 |
|:--- |:--- |
| 데이터베이스당 최대 vCore 수 |풀의 다른 데이터베이스에서 사용량에 따라 사용할 수 있는 경우 풀의 모든 데이터베이스에서 사용할 수 있는 최대 vCore 수입니다. 데이터베이스당 최대 vCore 수는 데이터베이스에 대한 리소스를 보장하지 않습니다. 풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스당 최대 vCore 수는 데이터베이스 사용량의 최고값을 처리할 수 있을 만큼 충분히 높게 설정합니다. 풀은 일반적으로 모든 데이터베이스가 동시에 최대로 사용되지 않을 경우 데이터베이스에 대해 핫 및 콜드 사용률 패턴을 가정하므로 일정 수준의 오버커밋이 예상됩니다.|
| 데이터베이스당 최소 vCore 수 |풀의 데이터베이스가 보장되는 최소 vCore 수입니다. 풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스당 최소 vCore 수를 0으로 설정할 수 있으며 기본값이기도 합니다. 이 속성은 0과 데이터베이스당 평균 vCore 사용량 사이의 값으로 설정됩니다. 풀의 데이터베이스 수와 데이터베이스당 최소 vCore 수를 곱한 값은 풀당 vCore 수를 초과할 수 없습니다.|
| 데이터베이스당 최대 저장소 |사용자가 풀의 데이터베이스에 대해 설정한 최대 데이터베이스 크기입니다. 풀된 데이터베이스는 할당된 풀 저장소를 공유하므로 데이터베이스에 도달할 수 있는 크기는 남아있는 풀 저장소 및 데이터베이스 크기 중 작은 값으로 제한됩니다. 최대 데이터베이스 크기는 데이터 파일의 최대 크기를 나타내고, 로그 파일에서 사용하는 공간을 포함하지 않습니다. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>탄력적 풀: 저장소 크기 변경

- 저장소는 최대 크기 제한까지 프로비전할 수 있습니다. 
 - 표준 저장소의 경우 크기는 10GB 증분 단위로 늘리거나 줄입니다.
 - 프리미엄 저장소의 경우 크기는 250GB 증분 단위로 늘리거나 줄입니다.
- 탄력적 풀에 대한 저장소는 [Azure portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 또는 [REST API](/rest/api/sql/elasticpools/update)를 사용하여 해당 최대 크기를 늘리거나 줄여서 프로비전할 수 있습니다.
- 탄력적 풀에 대한 저장소의 가격은 저장소 용량에 해당 서비스 계층의 저장소 단가를 곱한 값입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

## <a name="elastic-pool-change-vcores"></a>탄력적 풀: vCore 수 변경

[Azure Portal](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 또는 [REST API](/rest/api/sql/elasticpools/update)를 사용하여 리소스 요구에 따라 탄력적 풀에 대한 성능 수준을 높이거나 낮출 수 있습니다.

- 풀 vCore 수를 다시 조정하면 데이터베이스 연결이 잠시 끊어집니다. 이 동작은 단일 데이터베이스(풀의 데이터베이스 아님)에 대한 DTU 크기를 조정할 때와 동일합니다. 해당 기간 및 크기 조정 작업 동안 데이터베이스 연결 끊김에 미치는 영향에 대한 자세한 내용은 [단일 데이터베이스에 대한 DTU 크기 조정](#single-database-change-storage-size)을 참조하세요. 
- 풀 vCore 수를 다시 조정하는 기간은 풀의 모든 데이터베이스에서 사용하는 저장소 공간의 총 크기에 따라 달라질 수 있습니다. 일반적으로 크기 조정 대기 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 크기 조정을 위해 예상되는 대기 시간은 3시간 이하입니다. 표준 또는 기본 계층 내의 일부 경우에는 크기 조정 대기 시간이 사용 중인 공간 크기에 관계 없이 5분 이내일 수 있습니다.
- 일반적으로 데이터베이스당 최소 vCore 수 또는 데이터베이스당 최대 vCore 수를 변경하는 기간은 5분 이내입니다.
- 풀 vCore 수를 줄이는 경우 풀 사용 공간은 대상 서비스 계층 및 풀 vCore 수에 허용되는 최대 크기보다 작아야 합니다.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>서버 및 데이터베이스의 최대 개수는 어떻게 되나요?

| 최대 | 값 |
| :--- | :--- |
| 서버당 데이터베이스 | 5,000 |
| 지역별 구독당 서버 수 | 20 |
|||

> [!IMPORTANT]
> 데이터베이스 수가 서버당 제한에 근접하면 다음이 발생할 수 있습니다.
> <br> • 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> <br> • 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>데이터베이스 및 탄력적 풀 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-vcores"></a>계산(vCore 수)

데이터베이스 계산 사용량(vCore 사용량으로 측정)이 높아지면 쿼리 대기 시간이 늘어나고 시간이 초과될 수도 있습니다. 이러한 상황에서는 쿼리가 서비스에서 대기될 수 있으며 리소스가 사용 가능해질 경우 실행될 수 있게 리소스가 제공됩니다.
높은 계산 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 성능 수준을 높여 데이터베이스에 더 많은 vCore를 제공합니다. [단일 데이터베이스: vCore 수 변경](#single-database-change-vcores) 및 [탄력적 풀: vCore 수 변경](#elastic-pool-change-vcores)을 참조하세요.
- 쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>Storage

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](sql-database-develop-error-messages.md)가 표시됩니다. 데이터베이스 선택 및 삭제는 계속 성공적으로 수행됩니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 성능 수준을 변경하여 최대 저장소 크기를 늘립니다. [SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 속하는 경우 데이터베이스를 풀 외부로 이동하여 저장소 공간이 다른 데이터베이스와 공유되지 않도록 할 수 있습니다.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청) 

세션 및 작업자의 최대 수는 서비스 계층 및 성능 수준에 따라 결정됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 응용 프로그램에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 좀 더 오래 실행되는 쿼리로 인해 데이터베이스 리소스 제한에 도달하고 작업자가 대기하게 되는 최대 부하 기간이 특히 그렇습니다. 

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.
- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 성능 수준을 늘립니다. [단일 데이터베이스: 저장소 크기 변경](#single-database-change-storage-size), [단일 데이터베이스: vCore 수 변경](#single-database-change-vcores), [탄력적 풀: 저장소 크기 변경](#elastic-pool-change-storage-size) 및 [탄력적 풀: vCore 수 변경](#elastic-pool-change-vcores)을 참조하세요.
- 계산 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 자주 묻는 질문에 대한 답변은 [SQL Database FAQ](sql-database-faq.md)를 참조하세요.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
