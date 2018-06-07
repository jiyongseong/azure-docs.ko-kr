---
title: Azure SQL Database 관리되는 인스턴스 T-SQL 차이점 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database 관리되는 인스턴스 및 SQL Server 간의 T-SQL 차이점에 대해 설명합니다.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: b36099c6fd2deb6b627c8ccd7cc9e13c328f54e3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database 관리되는 인스턴스 및 SQL Server 간의 T-SQL 차이점 

Azure SQL Database 관리되는 인스턴스(미리 보기)는 온-프레미스 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공합니다. 대부분의 SQL Server 데이터베이스 엔진 기능은 관리되는 인스턴스에서 지원됩니다. 구문과 동작에는 여전히 몇 가지 차이점이 있으므로 이 문서에서는 이러한 차이점을 요약하고 설명합니다.
 - [T-SQL 차이점 및 지원되지 않는 기능](#Differences)
 - [관리되는 인스턴스에서 동작이 다른 기능](#Changes)
 - [임시 제한 사항 및 알려진 문제](#Issues)

## <a name="Differences"></a> SQL Server와의 T-SQL 차이점 

이 섹션에서는 관리되는 인스턴스와 온-프레미스 SQL Server 데이터베이스 엔진 간의 T-SQL 구문과 동작에 대한 주요 차이점과 지원되지 않는 기능을 요약합니다.

### <a name="always-on-availability"></a>Always-On 가용성

[고가용성](sql-database-high-availability.md)은 관리되는 인스턴스에 기본적으로 제공되며 사용자가 제어할 수 없습니다. 지원되지 않는 명령문은 다음과 같습니다.
 - [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="auditing"></a>감사 
 
관리되는 인스턴스, Azure SQL Database 및 SQL Server 온-프레미스의 SQL Audit 간의 주요 차이점은 다음과 같습니다.
- 관리되는 인스턴스에서 SQL Audit는 서버 수준에서 작동하며, `.xel` 파일을 Azure Blob 저장소 계정에 저장합니다.  
- Azure SQL Database에서 SQL Audit는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스/가상 머신에서 SQL Audit는 서버 수준에서 작동하지만, 파일 시스템/Windows 이벤트 로그에 이벤트를 저장합니다.  
  
관리되는 인스턴스의 XEvent 감사는 Azure Blob 저장소 대상을 지원합니다. 파일 및 Windows 로그는 지원되지 않습니다.    
 
Azure Blob 저장소에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.
- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치될 Azure Blob 저장소 컨테이너의 URL을 지정할 수 있습니다 
- 관리되는 인스턴스에서 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 지원되지 않습니다. 
 
자세한 내용은 다음을 참조하세요.  
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

관리되는 인스턴스에는 자동 백업이 있으며, 사용자가 전체 데이터베이스 `COPY_ONLY` 백업을 만들 수 있습니다. 차등, 로그 및 파일 스냅숏 백업은 지원되지 않습니다.  
- 관리되는 인스턴스에서는 Azure Blob Storage 계정에만 데이터베이스를 백업할 수 있습니다. 
 - `BACKUP TO URL`만 지원됩니다. 
 - `FILE`, `TAPE` 및 백업 장치는 지원되지 않습니다.  
- 대부분의 일반 `WITH` 옵션이 지원됩니다. 
 - `COPY_ONLY`는 필수입니다.
 - `FILE_SNAPSHOT`은 지원되지 않습니다.  
 - 테이프 옵션: `REWIND`, `NOREWIND`, `UNLOAD` 및 `NOUNLOAD`는 지원되지 않습니다. 
 - 로그 관련 옵션: `NORECOVERY`, `STANDBY` 및 `NO_TRUNCATE`은 지원되지 않습니다. 
 
제한 사항:  
- 관리되는 인스턴스는 최대 32개의 스트라이프가 있는 백업에 데이터베이스를 백업할 수 있습니다. 이는 백업 압축을 사용할 경우 데이터베이스를 최대 4TB까지 백업할 수 있습니다.
- 최대 백업 스트라이프 크기는 195GB(최대 Blob 크기)입니다. 백업 명령에서 스트라이프 수를 늘려 개별 스트라이프 크기를 줄이고 이 제한 내에서 유지합니다. 

> [!TIP]
> 온-프레미스에서 이 제한 사항을 해결하려면 `URL`에 백업하는 대신 `DISK`에 백업하고, Blob에 백업 파일을 업로드한 다음, 해당 파일을 복원합니다. 다른 Blob 유형이 사용되기 때문에 복원은 더 큰 파일을 지원합니다.  

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

### <a name="buffer-pool-extension"></a>버퍼 풀 확장 
 
- [버퍼 풀 확장](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)은 지원되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`은 지원되지 않습니다. [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요. 
 
### <a name="bulk-insert--openrowset"></a>대량 삽입/openrowset

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob 저장소에서 파일을 가져와야 합니다.
- Azure Blob 저장소에서 파일을 가져오는 동안 `BULK INSERT` 명령에 `DATASOURCE`가 필요합니다. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- Azure Blob 저장소에서 파일의 내용을 읽을 때 `OPENROWSET` 함수에 `DATASOURCE`가 필요합니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
 
### <a name="certificates"></a>인증서 

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다. 
- `CREATE FROM`/`BACKUP TO` 파일은 인증서에서 지원되지 않습니다.
- `FILE`/`ASSEMBLY`의 `CREATE`/`BACKUP` 인증서는 지원되지 않습니다. 개인 키 파일은 사용할 수 없습니다.  
 
[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요.  
  
> [!TIP]
> 해결 방법: 다음과 같이 인증서/개인 키 스크립트를 작성하고, .sql 파일로 저장하고 이진 파일에서 만듭니다. 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

관리되는 인스턴스는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다. 
- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요.  
- `CREATE ASSEMBLY FROM FILE`은 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.
 
### <a name="compatibility-levels"></a>호환성 수준 
 
- 지원되는 호환성 수준: 100, 110, 120, 130, 140  
- 100 미만의 호환성 수준은 지원되지 않습니다. 
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원된 데이터베이스의 경우 호환성 수준이 100 이상이면 변경되지 않고 그대로 유지됩니다.

[ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.
 
### <a name="credential"></a>자격 증명 
 
Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.
 
[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요. 
 
### <a name="cryptographic-providers"></a>암호화 공급자

관리되는 인스턴스는 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.
- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요. 

### <a name="collation"></a>Collation 
 
서버 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 변경할 수 없습니다. [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.
 
### <a name="database-options"></a>데이터베이스 옵션 
 
- 다중 로그 파일은 지원되지 않습니다. 
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다.  
- 인스턴스당 280개 파일의 제한이 있습니다. 이는 데이터베이스당 최대 280개 파일을 의미합니다. 데이터 및 로그 파일은 모두 이 제한으로 계산됩니다.  
- 데이터베이스에는 파일 스트림 데이터가 있는 파일 그룹이 포함될 수 없습니다.  .bak에 `FILESTREAM` 데이터가 포함되어 있으면 복원에 실패합니다.  
- 모든 파일은 Azure Premium Storage에 배치됩니다. 파일당 IO 및 처리량은 Azure Premium Storage 디스크와 동일한 방식으로 각 개별 파일의 크기에 따라 달라집니다. [Azure Premium 디스크 성능](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)을 참조하세요.  
 
#### <a name="create-database-statement"></a>CREATE DATABASE 문

`CREATE DATABASE` 제한 사항은 다음과 같습니다. 
- 파일 및 파일 그룹은 정의할 수 없습니다.  
- `CONTAINMENT` 옵션은 지원되지 않습니다.  
- `WITH` 옵션은 지원되지 않습니다.  
   > [!TIP]
   > 해결 방법으로, `CREATE DATABASE` 뒤에 `ALTER DATABASE`를 사용하여 파일을 추가하거나 포함을 설정하는 데이터베이스 옵션을 설정합니다.  

- `FOR ATTACH` 옵션은 지원되지 않습니다. 
- `AS SNAPSHOT OF` 옵션은 지원되지 않습니다. 

자세한 내용은 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.
- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 문에서 파일 경로를 지정할 수 없습니다. 관리되는 인스턴스에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다.  
- `ALTER DATABASE` 문을 사용하여 파일 이름을 변경할 수 없습니다.

기본적으로 설정되고 변경할 수 없는 옵션은 다음과 같습니다. 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

수정할 수 없는 옵션은 다음과 같습니다. 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

이름 수정은 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.
 - `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING`은 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="dbcc"></a>DBCC 
 
SQL Server에서 사용하도록 설정되었지만 문서화되지 않은 DBCC 문은 관리되는 인스턴스에서 지원되지 않습니다.
- `Trace Flags`는 지원되지 않습니다. [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)를 참조하세요.
- `DBCC TRACEOFF`는 지원되지 않습니다. [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)를 참조하세요.
- `DBCC TRACEON`은 지원되지 않습니다. [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)을 참조하세요.

### <a name="extended-events"></a>확장 이벤트 

XEvent에 대한 일부 Windows 관련 대상은 지원되지 않습니다.
- `etw_classic_sync target`은 지원되지 않습니다. Azure Blob 저장소에 `.xel` 파일을 저장합니다. [etw_classic_sync 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target)을 참조하세요. 
- `event_file target`은 지원되지 않습니다. Azure Blob 저장소에 `.xel` 파일을 저장합니다. [event_file 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python 외부 라이브러리는 아직 지원되지 않습니다. [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 Filetable

- filestream 데이터는 지원되지 않습니다. 
- 데이터베이스에는 `FILESTREAM` 데이터가 있는 파일 그룹이 포함될 수 없습니다.
- `FILETABLE`은 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

자세한 내용은 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버
 
관리되는 인스턴스의 연결된 서버는 제한된 수의 대상을 지원합니다. 
- 지원 대상: SQL Server 및 SQL Database
- 지원되지 않는 대상: 파일, Analysis Services 및 기타 RDBMS

작업

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- `OPENROWSET` 함수는 SQL Server 인스턴스(관리되는 인스턴스, 온-프레미스 인스턴스 또는 Virtual Machines 중 하나)에서만 쿼리를 실행하는 데 사용할 수 있습니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENDATASOURCE` 함수는 SQL Server 인스턴스(관리되는 인스턴스, 온-프레미스 인스턴스 또는 Virtual Machines 중 하나)에서만 쿼리를 실행하는 데 사용할 수 있습니다. `SQLNCLI`, `SQLNCLI11` 및 `SQLOLEDB` 값만 공급자로 지원됩니다. 예: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.
 
### <a name="logins--users"></a>로그인/사용자 

- `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` 및 `FROM SID`에서 만든 SQL 로그인이 지원됩니다. [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- `CREATE LOGIN ... FROM WINDOWS` 구문으로 만든 Windows 로그인은 지원되지 않습니다.
- 인스턴스를 만든 Azure AD(Azure Active Directory) 사용자에게는 [무제한 관리자 권한](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts)이 있습니다.
- 관리자가 아닌 Azure AD(Azure Active Directory) 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용하여 만들 수 있습니다. [CREATE USER ... FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)를 참조하세요.
 
### <a name="polybase"></a>Polybase

HDFS 또는 Azure Blob 저장소의 파일을 참조하는 외부 테이블은 지원되지 않습니다. Polybase에 대한 자세한 내용은 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)를 참조하세요.

### <a name="replication"></a>복제 
 
복제는 아직 지원되지 않습니다. 복제에 대한 자세한 내용은 [SQL Server 복제](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication)를 참조하세요.
 
### <a name="restore-statement"></a>RESTORE 문 
 
- 지원되는 구문  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- 지원되지 않는 구문 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- 원본  
 - `FROM URL`(Azure Blob 저장소)만 지원되는 옵션입니다.
 - `FROM DISK`/`TAPE`/백업 장치는 지원되지 않습니다.
 - 백업 세트는 지원되지 않습니다. 
- `WITH` 옵션은 지원되지 않습니다(`DIFFERENTIAL`, `STATS` 등이 없음)     
- `ASYNC RESTORE` - 클라이언트 연결이 중단되더라도 복원이 계속됩니다. 연결이 삭제된 경우 `sys.dm_operation_status` 보기에서 복원 작업의 상태를 확인할 수 있습니다(CREATE 및 DROP 데이터베이스의 경우도 동일함). 자세한 내용은 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요.  
 
설정/재정의되고 나중에 변경할 수 없는 데이터베이스 옵션은 다음과 같습니다.  
- `NEW_BROKER`(.bak 파일에서 broker를 사용할 수 없는 경우)  
- `ENABLE_BROKER`(.bak 파일에서 broker를 사용할 수 없는 경우)  
- `AUTO_CLOSE=OFF`(.bak 파일의 데이터베이스에 `AUTO_CLOSE=ON`이 있는 경우)  
- `RECOVERY FULL`(.bak 파일의 데이터베이스에 `SIMPLE` 또는 `BULK_LOGGED` 복구 모드가 있는 경우)
- 원본 .bak 파일에 없는 경우 메모리 최적화 파일 그룹이 추가되고 XTP라고 합니다.  
- 기존의 모든 메모리 최적화 파일 그룹의 이름이 XTP로 바뀝니다.  
- `SINGLE_USER` 및 `RESTRICTED_USER` 옵션이 `MULTI_USER` 로 변환됩니다.  
제한 사항:  
- 여러 백업 세트가 있는 `.BAK` 파일은 복원할 수 없습니다. 
- 여러 로그 파일이 있는 `.BAK` 파일은 복원할 수 없습니다. 
- .bak에 `FILESTREAM` 데이터가 포함되어 있으면 복원에 실패합니다.
- 활성 메모리 내 개체가 있는 데이터베이스를 포함한 백업은 현재 복원할 수 없습니다.  
- 특정 시점의 메모리 내 개체가 있는 데이터베이스를 포함한 백업은 현재 복원할 수 없습니다.   
- 읽기 전용 모드의 데이터베이스를 포함한 백업은 현재 복원할 수 없습니다. 이 제한은 곧 제거될 예정입니다.   
 
복원 명령문에 대한 자세한 내용은 [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)을 참조하세요.

### <a name="service-broker"></a>Service Broker 
 
- 인스턴스 간 서비스 broker는 지원되지 않습니다. 
 - `sys.routes` - 필수 조건: sys.routes에서 주소를 선택합니다. 주소는 모든 경로에서 LOCAL이어야 합니다. [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
 - `CREATE ROUTE` - `LOCAL` 이외의 `ADDRESS`가 포함된 `CREATE ROUTE`는 수행할 수 없습니다. [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
 - `ALTER ROUTE`는 `LOCAL` 이외의 `ADDRESS`가 포함된 `ALTER ROUTE`는 수행할 수 없습니다. [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요.  
 
### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키 
 
- [마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다. 
- [마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다. 
- [서비스 마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다. 
- [서비스 마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다. 
 
### <a name="stored-procedures-functions-triggers"></a>저장 프로시저, 함수, 트리거 
 
- `NATIVE_COMPILATION`은 현재 지원되지 않습니다. 
- 지원되지 않는 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`은 지원되지 않습니다. [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `sp_addextendedproc` 및 `sp_dropextendedproc`를 포함하여 `Extended stored procedures`는 지원되지 않습니다. [확장 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)를 참조하세요.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.
- `sp_renamedb`는 지원되지 않습니다. [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트 
 
- SQL 에이전트 설정은 읽기 전용입니다. `sp_set_agent_properties` 프로시저는 관리되는 인스턴스에서 지원되지 않습니다.  
- 작업 - T-SQL 작업 단계만 현재 지원됩니다(공개 미리 보기로 있는 동안 더 많은 단계가 추가될 예정임).
 - SSIS는 아직 지원되지 않습니다. 
 - 복제는 아직 지원되지 않습니다.  
  - 트랜잭션 로그 판독기는 아직 지원되지 않습니다.  
  - 스냅숏은 아직 지원되지 않습니다.  
  - 배포자는 아직 지원되지 않습니다.  
  - 병합은 지원되지 않습니다.  
  - 큐 판독기는 지원되지 않습니다.  
 - 명령 셸은 아직 지원되지 않습니다. 
  - 관리되는 인스턴스는 외부 리소스(예: robocopy를 통한 네트워크 공유)에 액세스할 수 없습니다.  
 - PowerShell은 아직 지원되지 않습니다.
 - Analysis Services는 지원되지 않습니다.  
- 알림은 부분적으로 지원됩니다.
 - 이메일 알림이 지원되며, 데이터베이스 메일 프로필을 구성해야 합니다. 데이터베이스 메일 프로필은 하나만 있을 수 있으며, 공개 미리 보기에서는 `AzureManagedInstance_dbmail_profile`이라고 해야 합니다(임시 제한).  
 - 호출기는 지원되지 않습니다.  
 - NetSend는 지원되지 않습니다. 
 - 경고는 아직 지원되지 않습니다.
 - 프록시는 지원되지 않습니다.  
- Eventlog는 지원되지 않습니다. 
 
현재 지원되지 않지만 나중에 사용할 수 있을 예정인 기능은 다음과 같습니다.  
- 프록시
- 유휴 CPU에 대한 작업 예약 
- 에이전트 사용 설정/해제
- Alerts

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)를 참조하세요.
 
### <a name="tables"></a>테이블 

지원되지 않는 옵션은 다음과 같습니다. 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

테이블 만들기 및 변경에 대한 자세한 내용은 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 및 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 참조하세요.
 
## <a name="Changes"></a> 동작 변경 
 
다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.  
- `SERVERPROPERTY('EngineEdition')`는 8 값을 반환합니다. 이 속성은 관리되는 인스턴스를 고유하게 식별합니다. [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `SERVERPROPERTY('InstanceName')`는 짧은 인스턴스 이름을 반환합니다(예: 'myserver'). [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`은 전체 DNS '연결 가능한' 이름을 반환합니다(예: my-managed-instance.wcus17662feb9ce98.database.windows.net). [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)을 참조하세요.  
- `SYS.SERVERS` - 'name' 및 'data_source' 속성에 대한 전체 DNS '연결 가능한' 이름을 반환합니다(예: `myinstance.domain.database.windows.net`). [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요. 
- `@@SERVERNAME`은 전체 DNS '연결 가능한' 이름을 반환합니다(예: `my-managed-instance.wcus17662feb9ce98.database.windows.net`). [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)을 참조하세요.  
- `SYS.SERVERS` - 'name' 및 'data_source' 속성에 대한 전체 DNS '연결 가능한' 이름을 반환합니다(예: `myinstance.domain.database.windows.net`). [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요. 
- `@@SERVICENAME`은 관리되는 인스턴스 환경에서 의미가 없기 때문에 NULL을 반환합니다. [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.   
- `SUSER_ID`가 지원됩니다. AAD 로그인이 sys.syslogins에 없는 경우 NULL을 반환합니다. [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요.  
- `SUSER_SID`는 지원되지 않습니다. 잘못된 데이터를 반환합니다(알려진 임시 문제). [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 
- `GETDATE()` 및 다른 기본 제공 날짜/시간 함수는 항상 UTC 표준 시간대의 시간을 반환합니다. [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)를 참조하세요.

## <a name="Issues"> </a> 알려진 문제 및 제한 사항

### <a name="tempdb-size"></a>TEMPDB 크기

`tempdb`는 파일당 최대 크기가 14GB인 12개의 파일로 분할됩니다. 이 파일당 최대 크기는 변경할 수 없으며, 새 파일을 `tempdb`에 추가할 수 없습니다. 이 제한은 곧 제거될 예정입니다. `tempdb`에 필요한 크기가 168GB를 초과하는 경우 일부 쿼리에서 오류를 반환할 수 있습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 저장소 공간 초과

각 관리되는 인스턴스에는 Azure Premium 디스크 공간에 대해 예약된 최대 35TB의 저장소가 있으며 각 데이터베이스 파일은 별도의 실제 디스크에 배치됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크의 사용되지 않는 공간은 변경될 수 있지만 Azure Premium 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요 없는 관리되는 인스턴스는 내부 조각화로 인해 저장소 크기에 대한 35TB Azure 제한을 초과할 수 있습니다. 

예를 들어 관리되는 인스턴스는 4TB 디스크를 사용하는 1.2TB 크기의 하나의 파일 및 각각 128GB 크기의 248개의 디스크에 있는 1GB의 248개의 파일을 가질 수 있습니다. 이 예제에서 전체 디스크 저장소 크기는 1x4TB + 248x128GB = 35TB입니다. 그러나 데이터베이스에 대한 총 예약된 인스턴스 크기는 1x1.2TB + 248x1GB = 1.4TB입니다. 특정 상황에서 매우 구체적인 파일의 배포로 인해 관리되는 인스턴스는 예상치 못한 Azure Premium 디스크 저장소 용량 한도에 도달할 수 있음을 보여줍니다. 

기존 데이터베이스에 오류가 없으며 새 파일이 추가되지 않으면 문제 없이 증가시킬 수 있지만 새 데이터베이스는 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브에 대한 충분한 공간이 없기 때문에 생성 또는 복원될 수 없습니다. 이 경우 반환되는 오류가 명확하지 않습니다.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>데이터베이스 복원 중 잘못된 SAS 키 구성

.bak 파일을 읽는 `RESTORE DATABASE`는 .bak 파일을 읽으려고 계속 시도하고, `CREDENTIAL`의 공유 액세스 서명이 올바르지 않으면 오랜 시간이 지난 후에 오류를 반환할 수 있습니다. SAS 키가 올바른지 확인하기 위해 데이터베이스를 복원하기 전에 RESTORE HEADERONLY를 실행합니다.
Azure Portal을 사용하여 생성된 SAS 키에서 선행 `?`를 제거했는지 확인합니다.

### <a name="tooling"></a>도구

관리되는 인스턴스에 액세스하는 동안 SQL Server Management Studio 및 SQL Server Data Tools에 몇 가지 문제가 발생할 수 있습니다. 모든 도구 문제는 일반 공급 이전에 해결될 예정입니다.

### <a name="incorrect-database-names"></a>잘못된 데이터베이스 이름

관리되는 인스턴스는 복원 중 또는 일부 오류 메시지에서 데이터베이스 이름 대신 GUID 값을 표시할 수 있습니다. 이러한 문제는 일반 공급 이전에 수정될 예정입니다.

### <a name="database-mail-profile"></a>데이터베이스 메일 프로필
데이터베이스 메일 프로필은 하나만 있을 수 있으며, `AzureManagedInstance_dbmail_profile`이라고 해야 합니다. 이는 곧 제거될 임시 제한 사항입니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- 관리되는 인스턴스를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)를 참조하세요.
