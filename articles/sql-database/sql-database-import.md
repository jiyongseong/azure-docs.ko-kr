---
title: BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 | Microsoft Docs
description: BACPAC 파일을 가져와 새 Azure SQL 데이터베이스를 만듭니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/10/2018
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: bd9554a18775cf98f4415ebd5d4b0d52edc53718
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894194"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>새 Azure SQL Database로 BACPAC 파일 가져오기

아카이브에서 데이터베이스를 가져오거나 다른 플랫폼에서 마이그레이션하는 경우 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 데이터베이스 스키마 및 데이터를 가져올 수 있습니다. BACPAC 파일은 메타데이터 및 SQL Server 데이터베이스의 데이터를 포함하는 BACPAC의 확장명을 가진 ZIP 파일입니다. BACPAC 파일은 Azure Blob Storage(표준 저장소만 해당)에서 또는 온-프레미스 위치의 로컬 저장소에서 가져올 수 있습니다. 가져오기 속도를 최대화하려면 P6처럼 더 높은 서비스 계층과 성능 수준을 지정한 다음 가져오기가 성공하면 적절하게 규모 감축하는 것이 좋습니다. 또한 가져오기 후의 데이터베이스 호환성 수준은 원본 데이터베이스의 호환성 수준에 따라 결정됩니다. 

> [!IMPORTANT] 
> Azure SQL Database로 데이터베이스를 마이그레이션한 후에는 데이터베이스를 현재 호환성 수준에서(AdventureWorks2008R2 데이터베이스에 대해 수준 100) 또는 더 높은 수준에서 작동하도록 선택할 수 있습니다. 특정 호환성 수준에서 데이터베이스를 운영하기 위한 옵션 및 그 영향에 대한 자세한 내용은 [ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요. 또한 호환성 수준과 관련된 추가 데이터베이스 수준 설정에 대한 자세한 내용은 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 참조하세요.   >

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Azure Portal을 사용하여 BACPAC 파일에서 가져오기

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Blob Storage에 저장된 BACPAC 파일로 Azure SQL Database를 만드는 지침을 제공합니다. Azure Portal을 사용하여 가져오기는 Azure Blob Storage에서 BACPAC 파일을 가져오는 것만 지원합니다.

Azure Portal을 사용하여 가져오려면 데이터베이스를 연결하기 위한 서버의 페이지를 연 다음 도구 모음에서 **가져오기**를 클릭합니다. 저장소 계정 및 컨테이너를 지정하고 가져올 BACPAC 파일을 선택합니다. 새 데이터베이스의 크기(일반적으로 원본과 동일)를 선택하고 대상 SQL Server 자격 증명을 제공합니다.  

   ![데이터베이스 가져오기](./media/sql-database-import/import.png)

가져오기 작업의 진행률을 모니터링하려면 가져올 데이터베이스가 포함된 논리 서버에 대한 페이지를 엽니다. 아래로 **작업**이 나올 때까지 스크롤한 다음 **Import/Export** 기록을 클릭합니다.

> [!NOTE]
> [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)는 이 문서의 다른 방법을 사용하여 BACPAC 파일에서 가져오는 작업을 지원했으나, 현재는 Azure Portal을 사용하는 마이그레이션을 지원하지 않습니다.

### <a name="monitor-the-progress-of-an-import-operation"></a>가져오기 작업의 진행률 모니터링

가져오기 작업의 진행률을 모니터링하려면 가져올 데이터베이스에 논리 서버의 페이지를 엽니다. 아래로 **작업**이 나올 때까지 스크롤한 다음 **Import/Export** 기록을 클릭합니다.
   
   ![가져오기](./media/sql-database-import/import-history.png) ![가져오기 상태](./media/sql-database-import/import-status.png)

데이터베이스가 서버에서 라이브 상태인지 확인하려면 **SQL 데이터베이스**를 클릭하고 새 데이터베이스를 **온라인** 상태인지 확인합니다.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SQLPackage를 사용하여 BACPAC 파일에서 가져오기

[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 SQL 데이터베이스를 가져오려면 [매개 변수 및 속성 가져오기](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties)를 참조하세요. SQLPackage 유틸리티는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수 있습니다.

대부분의 프로덕션 환경에서 규모 및 성능에 SQLPackage 유틸리티를 사용하는 것이 좋습니다. BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.

로컬 저장소에서 Azure SQL Database 논리 서버(이 예에서는 **mynewserver20170403**)로 **AdventureWorks2008R2** 데이터베이스를 가져오는 방법에 대한 스크립트 예제는 다음 SQLPackage 명령을 참조하세요. 이 스크립트는 이름이 **myMigratedDatabase**이고, 서비스 계층이 **프리미엄**이고, 서비스 목표가 **P6**인 새 데이터베이스를 만드는 방법을 보여줍니다. 각자 환경에 맞게 적절한 값으로 변경하면 됩니다.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 논리 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 내에서 Azure SQL Database 논리 서버로 연결을 시도하면 성공적인 연결을 위해 회사 방화벽에서 이 포트가 열려야 합니다.
>

이 예제는 Active Directory 유니버설 인증으로 SqlPackage.exe를 사용하여 데이터베이스를 가져오는 방법을 보여줍니다.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell을 사용하여 BACPAC 파일에서 가져오기

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet을 사용하여 Azure SQL Database 서비스에 데이터베이스 가져오기 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

가져오기 요청의 상태를 확인하려면 [Get AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet을 사용합니다. 요청 직후에 이 명령을 실행하면 **Status: InProgress**가 반환됩니다. **Status: Succeeded**가 표시되면 가져오기가 완료된 것입니다.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
다른 스크립트 예제는 [BACPAC 파일에서 데이터베이스 가져오기](scripts/sql-database-import-from-bacpac-powershell.md)를 참조하세요.

## <a name="import-using-other-methods"></a>다른 방법을 사용하여 가져오기

다음 마법사를 사용할 수도 있습니다.

- [SQL Server Management Studio의 데이터 계층 응용 프로그램 가져오기 마법사](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)
- [SQL Server 가져오기 및 내보내기 마법사](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>다음 단계
* 가져온 SQL Database에 연결하고 쿼리하는 방법을 알아보려면 [SQL Server Management Studio를 사용하여 SQL Database에 연결하고 샘플 T-SQL 쿼리 수행](sql-database-connect-query-ssms.md)을 참조하세요.
* BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* 저장소 키 및 공유 액세스 서명을 안전하게 관리하고 공유하는 방법을 알아보려면 [Azure Storage 보안 가이드](https://docs.microsoft.com/azure/storage/common/storage-security-guide)를 참조합니다. 


  

