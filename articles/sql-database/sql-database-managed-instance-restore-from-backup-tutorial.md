---
title: Azure SQL Database 관리되는 인스턴스에 백업 복원 | Microsoft Docs
description: SSMS를 사용하여 Azure SQL Database 관리되는 인스턴스에 데이터베이스 백업을 복원합니다.
keywords: SQL Database 자습서, SQL Database 관리되는 인스턴스, 백업 복원
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31426205"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database 관리되는 인스턴스에 데이터베이스 백업 복원

이 자습서에서는 Wide World Importers - 표준 백업 파일을 사용하여 Azure Blob 저장소에 저장된 데이터베이스의 백업을 관리되는 인스턴스로 복원하는 방법을 설명합니다. 이 메서드는 가동 중지 시간이 필요합니다. Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 관리되는 인스턴스 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요. 다양한 마이그레이션 방법의 논의는 [SQL Server 인스턴스를 Azure SQL Database 관리되는 인스턴스로 마이그레이션](sql-database-managed-instance-migrate.md)을 참조하세요.

> [!div class="checklist"]
> * Wide World Importers - Standard 백업 파일 다운로드
> * Azure 저장소 계정 만들기 및 백업 파일 업로드
> * 백업 파일에서 Wide World Importers 데이터베이스 복원

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이 자습서: [Azure SQL Database 관리되는 인스턴스 만들기](sql-database-managed-instance-create-tutorial-portal.md)에서 만든 리소스를 해당 시작점으로 사용합니다.

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Wide World Importers - Standard 백업 파일 다운로드

다음 단계를 사용하여 Wide World Importers - Standard 백업 파일을 다운로드합니다.

Internet Explorer를 사용하여 URL에 주소 상자에 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak을 입력한 다음, 대화 상자가 나타나면 **저장**을 클릭해 **다운로드** 폴더에 이 파일을 저장합니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/#create/Microsoft.SQLManagedInstance) 에 로그인합니다.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure 저장소 계정 만들기 및 백업 파일 업로드

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. **저장소**를 찾은 다음, **저장소 계정**을 클릭하여 저장소 계정 양식을 엽니다.

   ![저장소 계정 만들기](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 다음 표의 정보를 사용하여 요청된 정보가 포함된 저장소 계정 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**배포 모델**|리소스 모델||
   |**계정 종류**|Blob 저장소||
   |**성능**|표준 또는 프리미엄|자기 드라이브 또는 SSD|
   |**복제**|로컬 중복 저장소||
   |** 액세스 계층(기본값)|쿨 또는 핫||
   |**보안 전송 필요**|사용 안 함||
   |**구독**|사용자의 구독|구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요.|
   |**리소스 그룹**|이전에 만든 리소스 그룹|| 
   |**위치**:|이전에 선택한 위치||
   |**가상 네트워크**|사용 안 함||

4. **만들기**를 클릭합니다.

   ![저장소 계정 세부 정보](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. 저장소 계정 배포가 완료되면 새 저장소 계정을 엽니다.
6. **설정** 아래에서 **공유 액세스 서명**을 클릭하여 SAS(공유 액세스 서명) 양식을 엽니다.

   ![SAS 양식](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. SAS 양식에서 필요에 따라 기본값을 수정합니다. 만료 날짜/시간은 기본적으로 8시간입니다.
8. **SAS 생성**을 클릭합니다.

   ![완성된 SAS 양식](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. **SAS 토큰** 및 **Blob 서버 SAS URL**을 복사하여 저장합니다.
10. **설정** 아래에서 **컨테이너**를 클릭합니다.

    ![컨테이너](./media/sql-database-managed-instance-tutorial/containers.png)

11. **+ 컨테이너**를 클릭하여 백업 파일을 보관할 컨테이너를 만듭니다.
12. 다음 표의 정보를 사용하여 요청된 정보가 포함된 컨테이너 양식을 작성합니다.

    | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |**Name**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**공용 액세스 수준**|개인(익명 액세스 없음)||

    ![컨테이너 세부 정보](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. **확인**을 클릭합니다.
14. 컨테이너가 만들어지면 컨테이너를 엽니다.

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. **컨테이너 속성**을 클릭한 다음, 컨테이너에 URL을 복사합니다.

    ![컨테이너 URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. **업로드**를 클릭하여 **Blob 업로드** 양식을 엽니다.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. 다운로드 폴더로 이동하고 **WideWorldIimporters-Standard.bak** 파일을 선택합니다.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. **업로드**를 클릭합니다.
19. 업로드가 완료될 때까지 계속하지 마세요.

    ![완료된 업로드](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>백업 파일에서 Wide World Importers 데이터베이스 복원

SSMS를 사용하면 다음 단계를 사용하여 백업 파일에서 관리되는 인스턴스로 Wide World Importers 데이터베이스를 복원합니다.

1. SSMS에서 새 쿼리 창을 엽니다.
2. 다음 스크립트를 사용하여 SAS 자격 증명을 만듭니다. 저장소 계정 컨테이너 및 SAS 키에 대한 URL을 표시된 대로 제공합니다.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![자격 증명](./media/sql-database-managed-instance-tutorial/credential.png)

3. 다음 스크립트를 사용하여 SAS 자격 증명 및 백업 유효성을 검사합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![파일 목록](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 다음 스크립트를 사용하여 백업 파일에서 Adventure Works 2012 데이터베이스를 복원합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![실행 중인 복원](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 복원 상태를 추적하려면 새 쿼리 세션에서 다음 쿼리를 실행합니다.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![복원 완료율](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 복원이 완료되면 개체 탐색기에서 봅니다. 

    ![완료된 복원](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Wide World Importers - 표준 백업 파일을 사용하여 Azure Blob 저장소에 저장된 데이터베이스의 백업을 관리되는 인스턴스로 복원하는 방법을 알아보았습니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * Wide World Importers - Standard 백업 파일 다운로드
> * Azure 저장소 계정 만들기 및 백업 파일 업로드
> * 백업 파일에서 Wide World Importers 데이터베이스 복원

DMS를 사용하여 Azure SQL Database 관리되는 인스턴스로 SQL Server를 마이그레이션하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
>[DMS를 사용하여 Azure SQL Database 관리되는 인스턴스로 SQL Server 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)