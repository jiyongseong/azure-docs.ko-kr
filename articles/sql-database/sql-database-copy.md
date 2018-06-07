---
title: Azure SQL Database 복사 | Microsoft Docs
description: 같은 서버 또는 다른 서버에 기존 Azure SQL 데이터베이스의 트랜잭션 방식으로 일관된 사본을 만듭니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: 6b594c81f86a87e3b50b487f523cf4699c97365a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="copy-an-azure-sql-database"></a>Azure SQL 데이터베이스 복사

Azure SQL Database는 동일한 서버 또는 다른 서버에서 기존 Azure SQL Database의 트랜잭션 측면에서 일관된 복사본을 만들기 위한 여러 가지 방법을 제공합니다. Azure Portal, PowerShell 또는 T-SQL을 사용하여 SQL Database를 복사할 수 있습니다. 

## <a name="overview"></a>개요

데이터베이스 복사본은 복사 요청 당시의 원본 데이터베이스의 스냅숏입니다. 동일한 서버 또는 다른 서버, 해당 서비스 계층 및 성능 수준 또는 동일한 서비스 계층(버전) 내 다른 성능 수준을 선택할 수 있습니다. 복사가 완료되면 완전히 작동하는 독립 데이터베이스가 됩니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.  

## <a name="logins-in-the-database-copy"></a>데이터베이스 복사본에서 로그인

데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하는 보안 주체는 새 데이터베이스의 데이터베이스 소유자가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.  

다른 논리 서버로 데이터베이스를 복사할 때 새 서버의 보안 주체가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하는 경우 복사가 완료된 후 동일한 자격 증명으로 액세스할 수 있도록 주 데이터베이스와 보조 데이터베이스에서 항상 동일한 사용자 자격 증명을 사용해야 합니다. 

[Azure Active Directory](../active-directory/active-directory-whatis.md)를 사용하는 경우 복사본에서 자격 증명을 관리할 필요가 없습니다. 그러나 데이터베이스를 새 서버로 복사할 때 새 서버에 로그인이 존재하지 않으므로 로그인 기반 액세스가 작동하지 않을 수 있습니다. 다른 논리 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요. 

복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자인 복사를 시작한 로그인만 새 데이터베이스에 로그인할 수 있습니다. 복사 작업이 완료된 후 로그인을 확인하려면 [로그인 확인](#resolve-logins)을 참조하세요.

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure Portal을 사용하여 데이터베이스 복사

Azure Portal을 사용하여 데이터베이스를 복사하려면 데이터베이스에 대한 페이지를 열고 **복사**를 클릭합니다. 

   ![데이터베이스 복사](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>PowerShell을 사용하여 데이터베이스 복사

PowerShell을 사용하여 데이터베이스를 복사하려면 [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) cmdlet을 사용합니다. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

전체 샘플 스크립트는 [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-powershell.md)를 참조하세요.

## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL을 사용하여 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 서버 수준 보안 주체가 아닌 로그인이 dbmanager 역할의 구성원이어야 합니다. 서버에 로그인 및 연결하는 방법에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 문으로 원본 데이터베이스 복사를 시작합니다. 이 문을 실행하면 데이터베이스 복사 프로세스가 시작됩니다. 데이터베이스 복사는 비동기 프로세스이므로 CREATE DATABASE 문은 데이터베이스가 복사가 완료되기 전에 반환됩니다.

### <a name="copy-a-sql-database-to-the-same-server"></a>동일한 서버에 SQL 데이터베이스 복사
서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 서버 수준 보안 주체가 아닌 로그인이 dbmanager 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL 데이터베이스를 다른 서버에 복사

새 데이터베이스를 만들 SQL Database 서버인 대상 서버의 master 데이터베이스에 로그인합니다. 원본 SQL Database 서버에서 원본 데이터베이스의 데이터베이스 소유자와 이름 및 암호가 같은 로그인을 사용합니다. 대상 서버의 로그인도 dbmanager 역할의 구성원이거나 서버 수준 보안 주체 로그인이어야 합니다.

이 명령은 server1의 Database1을 server2의 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>복사 작업 진행률 모니터링

sys.databases 및 sys.dm_database_copies 뷰 쿼리를 통해 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **COPYING**으로 설정됩니다.

* 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **SUSPECT**로 설정됩니다. 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
* 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **ONLINE**으로 설정됩니다. 복사가 완료되었으며 새 데이터베이스가 원본 데이터베이스와는 독립적으로 변경 가능한 일반 데이터베이스가 됩니다.

> [!NOTE]
> 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 문을 실행합니다. 또는 원본 데이터베이스에서 DROP DATABASE 문을 실행해도 복사 프로세스가 취소됩니다.
> 

## <a name="resolve-logins"></a>로그인 확인

대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://msdn.microsoft.com/library/ms175475.aspx)을 참조하세요. [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 되며 새 보안 식별자(SID)를 할당 받습니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자인 복사를 시작한 로그인만 새 데이터베이스에 로그인할 수 있습니다.

다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 로그인에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md) 및 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.
* 데이터베이스를 내보내려면 [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)를 참조하세요.
