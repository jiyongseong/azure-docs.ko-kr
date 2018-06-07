---
title: Azure SQL Database 단일 데이터베이스 | Microsoft Docs
description: 단일 Azure SQL 데이터베이스의 서비스 계층, 성능 수준 및 저장 용량을 관리합니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: c1fe162beca258fe8ec3d03ce2844c1abe3176dc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188976"
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스에 대한 리소스를 관리합니다.

단일 데이터베이스를 사용하는 경우 데이터베이스가 서비스 계층, 성능 수준 및 필요한 저장소 크기에서 해당 워크로드를 처리하는 데 필요한 리소스의 양을 결정합니다. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Azure Portal을 사용하여 단일 데이터베이스 리소스 관리

Azure Portal을 사용하여 새 또는 기존 Azure SQL 데이터베이스의 서비스 계층, 성능 수준 또는 저장소 용량을 설정하거나 변경하려면 다음 스크린샷과 같이 **가격 책정 계층(DTU 조정)** 을 클릭하여 데이터베이스에 대한 **성능 구성** 창을 엽니다. 

- 워크로드에 대한 서비스 계층을 선택하여 서비스 계층을 설정하거나 변경합니다. 
- **DTU** 슬라이더를 사용하여 서비스 계층 내의 성능 수준(**DTU**)을 설정하거나 변경합니다.
- **저장소** 슬라이더를 사용하여 성능 수준의 저장소 용량을 설정하거나 변경합니다. 

![서비스 계층 및 성능 수준 구성](./media/sql-database-single-database-resources/change-service-tier.png)

**개요**를 클릭하여 진행 중인 작업을 모니터링 및/또는 취소합니다.

![작업 취소](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> P11 또는 P15 서비스 계층을 선택하는 경우 [4TB 최대 크기의 P11 및 P15 데이터베이스의 현재 제한 사항](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 검토하세요.
>

## <a name="manage-single-database-resources-using-powershell"></a>PowerShell을 사용하여 단일 데이터베이스 리소스 관리

PowerShell을 사용하여 Azure SQL Database 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|데이터베이스 만들기 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스 가져오기|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동. 예를 들어 **MaxSizeBytes** 속성을 사용하여 데이터베이스의 최대 크기를 설정합니다.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|데이터베이스 작업의 상태를 가져옵니다. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|데이터베이스에서 비동기 업데이트 작업을 취소합니다.|


> [!TIP]
> 데이터베이스의 성능 메트릭을 모니터링하고 성능 수준을 더 높이고 성능 메트릭 중 하나에 대한 경고 규칙을 만드는 PowerShell 예제 스크립트는 [PowerShell을 사용하여 단일 SQL 데이터베이스 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Azure CLI를 사용하여 단일 데이터베이스 리소스 관리

Azure CLI를 사용하여 Azure SQL Database 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. SQL 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|서버 방화벽 규칙 만들기|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|서버의 방화벽 규칙 나열|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|방화벽 규칙의 세부 정보 표시|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|방화벽 규칙 업데이트|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|방화벽 규칙 삭제|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|데이터베이스에서 수행한 작업 목록을 가져옵니다.|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|데이터베이스에서 비동기 작업을 취소합니다.|

> [!TIP]
> 데이터베이스의 크기 정보를 쿼리한 후 단일 Azure SQL Database를 다른 성능 수준으로 크기 조정하는 Azure CLI 예제 스크립트는 [CLI를 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md)을 참조하세요.
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Transact-SQL을 사용하여 단일 데이터베이스 리소스 관리

Transact-SQL을 사용하여 Azure SQL Database 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL 데이터베이스를 수정합니다. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|
|[sys.database_usage(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Azure SQL Database 서버의 데이터베이스 수, 유형 및 기간을 나열합니다.|

다음 예제에서는 ALTER DATABASE 명령을 사용하여 변경되는 데이터베이스의 최대 크기를 보여 줍니다.

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>REST API를 사용하여 단일 데이터베이스 리소스 관리

Azure SQL Database 서비스 계층, 성능 수준 및 저장소 용량을 설정하거나 변경하려면 다음 REST API 요청을 사용합니다.

| 명령 | 설명 |
| --- | --- |
|[데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[데이터베이스 - Get](/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스 목록을 반환합니다.|
|[데이터베이스 - List By Server](/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|
|[작업 - 목록](/rest/api/sql/Operations/List)|사용 가능한 SQL Rest API 작업을 모두 나열합니다.|



## <a name="next-steps"></a>다음 단계

- 서비스 계층, 성능 수준 및 저장소 용량에 대해 알아보고, [[DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 참조하세요.
- 탄력적 풀에 대한 자세한 내용은 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
