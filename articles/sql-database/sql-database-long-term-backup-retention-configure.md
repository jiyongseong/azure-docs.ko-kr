---
title: Azure SQL Database 장기 백업 보존 관리 | Microsoft Docs
description: 자동화된 백업을 SQL Azure Storage에 저장한 다음, 복원하는 방법 알아보기
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: df3d843516bce30253c23080716e606dfb56f25e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211564"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database 장기 백업 보존 관리

최대 10년 동안 Azure Blob Storage에 백업을 자동으로 보관하도록 [장기 백업 보존](sql-database-long-term-retention.md) 정책(LTR)을 사용하여 Azure SQL 데이터베이스를 구성합니다. 그런 다음, Azure Portal이나 PowerShell에서 이러한 백업을 사용하여 데이터베이스를 복구할 수 있습니다.

> [!NOTE]
> 이 기능의 미리 보기가 2016년 10월에 처음 출시되면서 Azure 서비스 복구 서비스 자격 증명 모음에 백업이 저장되었습니다. 이 업데이트를 종속성을 제거하지만 이전 버전과의 호환성을 위해 2018년 5월 31일까지 원래 API가 지원됩니다. Azure 서비스 복구 자격 증명 모음의 백업과 상호 작용이 필요한 경우 [Azure 서비스 복구 서비스 자격 증명 모음을 사용한 장기 백업 보존](sql-database-long-term-backup-retention-configure-vault.md)을 참조하세요. 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Azure Portal을 사용하여 장기 보존 정책 구성 및 백업 복원

다음 섹션에서는 Azure Portal을 사용하여 장기 보존을 구성하고, 장기 보존되는 백업을 보고, 장기 보존에서 백업을 복원하는 방법을 보여줍니다.

### <a name="configure-long-term-retention-policies"></a>장기 보존 정책 구성

서비스 계층의 보존 기간보다 긴 기간 동안 [자동화된 백업을 유지](sql-database-long-term-retention.md)하도록 SQL Database를 구성할 수 있습니다. 

1. Azure Portal에서 SQL 서버를 선택한 다음, **장기 백업 보존**을 클릭합니다.

   ![장기 백업 보존 링크](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. **정책 구성** 탭에서 장기 백업 보존 정책을 설정하거나 수정할 데이터베이스를 선택합니다.

   ![데이터베이스 선택](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. **정책 구성** 창에서 매주, 매달 또는 매년 백업을 보존할지 여부를 선택하고 각각에 대해 보존 기간을 지정합니다. 

   ![정책 구성](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. 완료되면 **적용**을 클릭합니다.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Azure Portal을 사용하여 백업을 보고 백업에서 복원

LTR 정책으로 특정 데이터베이스에 보존된 백업을 보고 해당 백업에서 복원합니다. 

1. Azure Portal에서 SQL 서버를 선택한 다음, **장기 백업 보존**을 클릭합니다.

   ![장기 백업 보존 링크](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. **사용 가능한 백업** 탭에서 사용 가능한 백업을 보려는 데이터베이스를 선택합니다.

   ![데이터베이스 선택](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. **사용 가능한 백업** 창에서 사용 가능한 백업을 검토합니다. 

   ![백업 보기](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. 복원에 사용할 백업을 선택한 다음, 새 데이터베이스 이름을 지정합니다.

   ![복원](./media/sql-database-long-term-retention/ltr-restore.png)

5. **확인**을 클릭하여 Azure SQL 저장소의 백업에서 새 데이터베이스로 데이터베이스를 복원합니다.

6. 도구 모음에서 알림 아이콘을 클릭하여 복원 작업의 상태를 봅니다.

   ![자격 증명 모음에서 복원 작업 진행률](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 복원 작업이 완료되면 **SQL Database** 페이지를 열어 새로 복원된 데이터베이스를 봅니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](sql-database-recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>PowerShell을 사용하여 장기 보존 정책 구성 및 백업 복원

다음 섹션에서는 PowerShell을 사용하여 장기 백업 보존을 구성하고 Azure SQL 저장소에서 백업을 확인하고 Azure SQL 저장소의 백업에서 복원하는 방법을 보여줍니다.

> [!IMPORTANT]
> 최신 AzureRM powershell을 사용하여 LTR V2 정책을 설정해야 합니다. 현재 버전은 [AzureRM 4.5.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0-preview)이며, 이는 미리 보기 버전이므로 이 명령을 사용하여 설치합니다. `Install-Module -Name AzureRM.Sql -AllowPrerelease -Force`
> 시험판 버전 설치에 대한 지침은 [PowerShellGet 모듈 가져오기](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget)를 참조하세요. AzureRM powershell 2018년 5월 릴리스는 몇 일 내로 시작되며(2018/5/18일로 예상) 사용할 수 있게 될 때 릴리스 버전을 설치하면 AllowPrelease 스위치를 무시하고 다음 명령 `Install-Module -Name AzureRM.Sql -Force`를 사용할 수 있습니다.

### <a name="create-an-ltr-policy"></a>LTR 정책 만들기

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR 정책 보기
이 예제에서는 서버 내의 LTR 정책을 나열하는 방법을 보여줍니다.

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>LTR 정책 지우기
이 예제에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여줍니다.

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

이 예제에서는 서버 내의 LTR 백업을 나열하는 방법을 보여줍니다. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원
이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 이 인터페이스는 변경되지 않았지만 이제 리소스 ID 매개 변수에 LTR 백업 리소스 ID가 필요합니다. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
