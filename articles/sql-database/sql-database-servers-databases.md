---
title: Azure SQL Server 및 데이터베이스 만들기 및 관리 | Microsoft Docs
description: SQL Database 서버 및 데이터베이스 개념, 서버 및 데이터베이스 생성 및 관리에 대해 알아봅니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: carlrab
ms.openlocfilehash: 3ffae541020a2672affab774ee6da2a8c707745f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195535"
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Azure SQL Database 서버 및 데이터베이스 만들기 및 관리

SQL Database는 다음 세 가지 유형의 데이터베이스를 제공합니다.

- [결합된 계산 및 저장소 리소스 집합](sql-database-service-tiers-dtu.md) 또는 [계산 및 저장소 리소스의 독립된 크기 조정](sql-database-service-tiers-vcore.md)을 통해 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에서 만들어진 단일 데이터베이스. Azure SQL Database는 Azure SQL Database 논리 서버와 연결되어 있으며 특정 Azure 지역에서 만들어집니다.
- 풀의 모든 데이터베이스 간에 공유되는 [결합된 계산 및 저장소 리소스 집합(DTU 기반)](sql-database-service-tiers-dtu.md) 또는 [계산 및 저장소 리소스의 독립된 크기 조정(vCore 기반)](sql-database-service-tiers-vcore.md)을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에서 [데이터베이스 풀](sql-database-elastic-pool.md)의 일부로 만들어진 데이터베이스. Azure SQL Database는 Azure SQL Database 논리 서버와 연결되어 있으며 특정 Azure 지역에서 만들어집니다.
- 해당 서버 인스턴스의 모든 데이터베이스에 대해 정의된 계산 및 저장소 리소스 집합을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에서 만들어진 [SQL Server 인스턴스](sql-database-managed-instance.md)(관리되는 인스턴스). 관리되는 인스턴스는 시스템 및 사용자 데이터베이스를 둘 다 포함합니다. 관리되는 인스턴스는 응용 프로그램을 다시 설계하지 않고도 완전히 관리되는 PaaS로 데이터베이스로 리프트 앤 시프트 방식으로 이동할 수 있도록 설계되었습니다. 관리되는 인스턴스는 온-프레미스 SQL Server 프로그래밍 모델과의 호환성이 뛰어나고, 대부분의 SQL Server 기능과 함께 제공되는 도구 및 서비스에 대한 지원을 제공합니다.  

Microsoft Azure SQL Database는 TDS(Tabular Data Stream) 프로토콜 클라이언트 버전 7.3 이상을 지원하며 암호화된 TCP/IP 연결만 허용합니다.

> [!IMPORTANT]
> 현재 공개 미리 보기로 제공되는 SQL Database 관리되는 인스턴스는 단일 범용 서비스 계층을 제공합니다. 자세한 내용은 [SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요. 이 문서의 나머지 부분은 관리되는 인스턴스에 적용되지 않습니다.

## <a name="what-is-an-azure-sql-logical-server"></a>Azure SQL 논리 서버란?

논리 서버는 단일 또는 여러 [풀링된](sql-database-elastic-pool.md) 데이터베이스, [로그인](sql-database-manage-logins.md), [방화벽 규칙](sql-database-firewall-configure.md), [감사 규칙](sql-database-auditing.md), [위협 검색 정책](sql-database-threat-detection.md) 및 [장애 조치(Failover) 그룹](sql-database-geo-replication-overview.md)에 대한 중앙 관리 지점의 역할을 담당합니다. 논리 서버는 리소스 그룹과 다른 지역에 위치할 수 있습니다. Azure SQL Database를 만들기 전에 논리 서버가 있어야 합니다. 서버에 있는 모든 데이터베이스는 논리 서버와 동일한 지역 내에서 생성됩니다.

논리 서버는 온-프레미스 환경에서 친숙한 SQL Server 인스턴스와 구별되는 논리적 구문입니다. 특히, SQL Database 서비스는 해당 논리 서버와 관련하여 데이터베이스의 위치르 보증하지는 않으며 인스턴스 수준의 액세스 또는 기능을 노출하지 않습니다. 반대로, SQL Database 관리되는 인스턴스의 서버는 온-프레미스 환경에서 친숙할 수 있는 SQL Server 인스턴스와 비슷합니다.

논리 서버를 만들 때 해당 서버의 마스터 데이터베이스 및 해당 서버에서 생성된 모든 데이터베이스에 대한 관리 권한이 있는 서버 로그인 계정 및 암호를 제공합니다. 이 초기 계정이 SQL 로그인 계정입니다. Azure SQL Database는 인증을 위해 SQL 인증 및 Azure Active Directory 인증을 지원합니다. 로그인 및 인증에 대한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요. Windows 인증은 지원되지 않습니다. 

> [!TIP]
> 유효한 리소스 그룹 및 서버 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.
>

Azure 데이터베이스 논리 서버는 다음과 같습니다.

- Azure 구독 내에서 만들었지만 다른 구독에 포함된 리소스를 사용하여 이동시킬 수 있습니다
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 상위 리소스입니다.
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 논리 컨테이너로서 서버를 삭제하고 포함된 데이터베이스, 탄력적 풀, 데이터 웨어하우스를 삭제합니다.
- [Azure RBAC(역할 기반 액세스 제어)](/azure/role-based-access-control/overview)에 포함됩니다. 서버 내의 데이터베이스, 탄력적 풀 및 데이터 웨어하우스는 서버에서 액세스 권한을 상속합니다.
- Azure 리소스를 관리하기 위한 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 ID 상위 요소입니다(데이터베이스 및 풀은 URL 구성표 참조).
- 지역에 리소스 배치
- 데이터베이스 액세스에 대한 연결 끝점을 제공합니다(<serverName>.database.windows.net).
- 마스터 데이터베이스에 연결하여 DMV를 통해 포함된 리소스 관련 메타데이터에 대한 액세스를 제공합니다. 
- 로그인, 방화벽, 감사, 위협 요소 탐지 등 해당 데이터베이스에 적용되는 관리 정책에 대한 범위를 제공합니다. 
- 상위 구독 내의 할당량으로 제한됩니다(기본적으로 구독 당 6대의 서버임. [여기에서 구독 제한 참조](../azure-subscription-service-limits.md)).
- 포함한 리소스에 대한 데이터베이스 할당량 및 DTU 또는 vCore 할당량의 범위를 제공합니다(예: 45000DTU).
- 포함된 리소스에서 사용하도록 설정된 기능에 대한 버전 관리 범위입니다. 
- 서버 수준 주체 로그인은 서버에 있는 모든 데이터베이스를 관리할 수 있습니다.
- 서버에서 하나 이상의 데이터베이스에 대한 액세스를 부여하는 SQL Server 온-프레미스 인스턴스에서 해당 항목과 비슷한 로그인을 포함하고 제한된 관리 권한이 부여될 수 있습니다. 자세한 내용은 [로그인](sql-database-manage-logins.md)을 참조하세요.
- 논리 서버에서 생성되는 모든 사용자 데이터베이스에 대한 기본 데이터 정렬은 `SQL_LATIN1_GENERAL_CP1_CI_AS`입니다. 여기서 `LATIN1_GENERAL`은 영어(미국)이고, `CP1`은 코드 페이지 1252이며, `CI`는 대/소문자 비구분이고, `AS`는 악센트 구분입니다.

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Azure SQL Database는 SQL Database 방화벽으로 보호됩니다.

데이터를 보호하기 위해 [SQL Database 방화벽](sql-database-firewall-configure.md)은 Azure 구독 연결을 통해 직접 서버에 대한 연결 외부에서 데이터베이스 서버 또는 데이터베이스에 대한 모든 액세스를 방지합니다. 추가 연결을 사용하도록 설정하려면 [하나 이상의 방화벽 규칙을 만들](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)어야 합니다. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽 관리

Azure SQL Database의 리소스 그룹을 미리 만들거나 서버 자체를 만드는 동안 만들 수 있습니다. 새 SQL Server를 만들거나 새 데이터베이스 만들기의 일부분으로 새 SQL Server 양식을 가져오는 여러 방법이 있습니다. 

### <a name="create-a-blank-sql-server-logical-server"></a>비어 있는 SQL Server(논리 서버) 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 (데이터베이스 없이) Azure SQL Database 서버를 만들려면 비어 있는 SQL Server(논리 서버) 양식으로 이동합니다.  

### <a name="create-a-blank-or-sample-sql-database"></a>비어 있거나 샘플인 SQL Database 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 Azure SQL Database를 만들려면 비어 있는 SQL Database 양식으로 이동하고 요청된 정보를 제공합니다. Azure SQL Database의 리소스 그룹 및 논리 서버를 미리 만들거나 데이터베이스 자체를 만드는 동안 만들 수 있습니다. 비어 있는 데이터베이스를 만들거나 Adventure Works LT에 따라 샘플 데이터베이스를 만들 수 있습니다. 

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> 데이터베이스의 가격 책정 계층 선택에 대한 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 참조하세요.

관리되는 인스턴스를 만들려면 [관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)를 참조하세요.

### <a name="manage-an-existing-sql-server"></a>기존 SQL Server 관리

기존 서버를 관리하려면 특정 SQL Database 페이지, **SQL server** 페이지 또는 **모든 리소스** 페이지와 같은 다양한 방법을 사용하여 서버로 이동합니다. 

기존 데이터베이스를 관리하려면 **SQL Database** 페이지로 이동하고 관리하려는 데이터베이스를 클릭합니다. 다음 스크린샷에서는 데이터베이스의 **개요** 페이지에서 데이터베이스의 서버 수준 방화벽을 설정하기 시작하는 방법을 보여줍니다. 

   ![서버 방화벽 규칙](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> 데이터베이스의 성능 속성을 구성하려면 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델(미리 보기)](sql-database-service-tiers-vcore.md)을 참조하세요.
>

> [!TIP]
> Azure Portal 빠른 시작은 [Azure Portal에서 Azure SQL Database 만들기](sql-database-get-started-portal.md)를 참조하세요.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>PowerShell을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽 관리

Azure PowerShell을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|데이터베이스 만들기 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스 가져오기|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|데이터베이스 제거|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|리소스 그룹 만들기]
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|서버 만들기|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|서버에 대한 정보 반환|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|서버의 속성 수정|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|서버 제거|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|서버 수준 방화벽 규칙 만들기 |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|서버의 방화벽 규칙 가져오기|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|서버에서 방화벽 규칙 수정|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|서버에서 방화벽 규칙 삭제|
| New-AzureRmSqlServerVirtualNetworkRule | Virtual Network 서비스 끝점인 서브넷을 기반으로 [*가상 네트워크 규칙*](sql-database-vnet-service-endpoint-rule-overview.md)을 만듭니다. |

> [!TIP]
> PowerShell 빠른 시작은 [PowerShell을 사용하여 단일 Azure SQL Database 만들기](sql-database-get-started-portal.md)를 참조하세요. PowerShell 예제 스크립트에 대해서는 [PowerShell을 사용하여 단일 Azure SQL Database 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-powershell.md) 및 [PowerShell을 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure SQL Server, 데이터베이스 및 방화벽 관리

[Azure CLI](/cli/azure)를 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |데이터베이스 만들기|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|서버의 모든 데이터베이스 및 데이터 웨어하우스 또는 탄력적 풀의 모든 데이터베이스 나열|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|사용 가능한 서비스 목표 및 저장소 용량 제한 나열|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|데이터베이스 사용 정보 반환|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|데이터베이스 또는 데이터 웨어하우스 가져오기|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|데이터베이스 업데이트|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|데이터베이스 제거|
|[az group create](/cli/azure/group#az_group_create)|리소스 그룹 만들기|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|서버 만들기|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|서버 나열|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list_usages)|서버 사용 반환|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|서버 가져오기|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|서버 업데이트|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|서버를 삭제합니다.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|서버 방화벽 규칙 만들기|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|서버의 방화벽 규칙 나열|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|방화벽 규칙의 세부 정보 표시|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|방화벽 규칙 업데이트|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|방화벽 규칙 삭제|

> [!TIP]
> Azure CLI 빠른 시작은 [Azure CLI를 사용하여 단일 Azure SQL Database 만들기](sql-database-get-started-cli.md)를 참조하세요. Azure CLI 예제 스크립트에 대해서는 [CLI를 사용하여 단일 Azure SQL Database 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-cli.md) 및 [CLI를 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md)을 참조하세요.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Transact-SQL을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽 관리

Transact-SQL을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 T-SQL cmdlet을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 탄력적 풀 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 서버를 만들거나 삭제할 수 없습니다.
>

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL 데이터베이스를 수정합니다. |
|[ALTER DATABASE(Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Azure SQL Data Warehouse를 수정합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|
|[sys.dm_db_resource_stats(Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL Database 데이터베이스에 대한 CPU, IO 및 메모리 소비량을 반환합니다. 데이터베이스에 작업이 없는 경우에도 한 행은 15초 간격으로 존재합니다.|
|[sys.resource_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL Database에 대한 CPU 사용량 및 저장소 데이터를 반환합니다. 데이터는 5분 미만 간격으로 수집되고 집계됩니다.|
|[sys.database_connection_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|SQL Database 데이터베이스 연결 이벤트에 대한 통계를 포함하며 데이터베이스 연결 성공 및 실패에 대한 개요를 제공합니다. |
|[sys.event_log(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|성공적인 Azure SQL Database 데이터베이스 연결, 연결 실패 및 교착 상태를 반환합니다. 이 정보를 사용하여 SQL Database의 데이터베이스 작업을 추적하거나 문제를 해결할 수 있습니다.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|SQL Database 서버에 서버 수준 방화벽 설정을 만들거나 업데이트합니다. 이 저장 프로시저는 마스터 데이터베이스에서 서버 수준 보안 주체 로그인에만 사용할 수 있습니다. Azure 수준 사용 권한 가진 사용자가 첫 번째 서버 수준 방화벽 규칙을 만든 후에만 Transact-SQL을 사용하여 서버 수준 방화벽 규칙을 다시 만들 수 있습니다.|
|[sys.firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Microsoft Azure SQL Database와 연결된 서버 수준 방화벽 설정에 대한 정보를 반환합니다.|
|[sp_delete_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|SQL Database 서버에서 서버 수준 방화벽 설정을 제거합니다. 이 저장 프로시저는 마스터 데이터베이스에서 서버 수준 보안 주체 로그인에만 사용할 수 있습니다.|
|[sp_set_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL Database 또는 SQL Data Warehouse에서 데이터베이스 수준 방화벽 규칙을 만들거나 업데이트합니다. 마스터 데이터베이스와 SQL Database에서 사용자 데이터베이스에서 데이터베이스 방화벽 규칙을 구성할 수 있습니다. 데이터베이스 방화벽 규칙은 포함된 데이터베이스 사용자를 사용하는 경우에 유용합니다. |
|[sys.database_firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Microsoft Azure SQL Database와 연결된 데이터베이스 수준 방화벽 설정에 대한 정보를 반환합니다. |
|[sp_delete_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Azure SQL Database 또는 SQL Data Warehouse에서 데이터베이스 수준 방화벽 설정을 제거합니다. |


> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용하는 빠른 시작은 [Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요. Windows, Linux 또는 macOS에서 Visual Studio Code를 사용하는 빠른 시작은 [Azure SQL Database: Visual Studio Code를 사용하여 연결 및 데이터 쿼리](sql-database-connect-query-vscode.md)를 참조하세요.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>REST API를 사용하여 Azure SQL Server, 데이터베이스 및 방화벽 관리

Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 REST API 요청을 사용하세요.

| 명령 | 설명 |
| --- | --- |
|[Servers - Create 또는 Update](/rest/api/sql/servers/createorupdate)|새 서버를 만들거나 업데이트합니다.|
|[Servers - Delete](/rest/api/sql/servers/delete)|SQL 서버를 삭제합니다.|
|[Servers - Get](/rest/api/sql/servers/get)|서버를 가져옵니다.|
|[Servers - List](/rest/api/sql/servers/list)|서버 목록을 반환합니다.|
|[Servers - List By Resource Group](/rest/api/sql/servers/listbyresourcegroup)|리소스 그룹의 서버 목록을 반환합니다.|
|[Servers - Update](/rest/api/sql/servers/update)|기존 서버를 업데이트합니다.|
|[데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[데이터베이스 - Get](/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스 목록을 반환합니다.|
|[데이터베이스 - List By Server](/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|
|[Firewall Rules - Create 또는 Update](/rest/api/sql/firewallrules/createorupdate)|방화벽 규칙을 만들거나 업데이트합니다.|
|[Firewall Rules - Delete](/rest/api/sql/firewallrules/delete)|방화벽 규칙을 삭제합니다.|
|[Firewall Rules - Get](/rest/api/sql/firewallrules/get)|방화벽 규칙을 가져옵니다.|
|[Firewall Rules - List By Server](/rest/api/sql/firewallrules/listbyserver)|방화벽 규칙 목록을 반환합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.
