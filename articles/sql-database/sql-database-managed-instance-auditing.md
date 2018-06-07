---
title: Azure SQL Database 관리되는 인스턴스 감사 | Microsoft Azure
description: T-SQL을 사용하여 Azure SQL Database 관리되는 인스턴스 감사를 시작하는 방법을 알아봅니다.
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 3d5a4ad3f4046dfdfe6eb3f7ddd931ccb240b1a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Azure SQL Database 관리되는 인스턴스 감사 시작

[Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md) 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다. 또한
- 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.
- 감사를 사용하면 규정을 완전히 준수한다고 보장할 수는 없지만 규정 표준을 보다 쉽게 준수할 수 있습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.


## <a name="set-up-auditing-for-your-server"></a>서버에 대한 감사 설정

다음 섹션에서는 관리되는 인스턴스에 대한 감사 구성을 설명합니다.
1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 다음 단계에서는 감사 로그가 저장되는 Azure Storage **컨테이너**를 만듭니다.

   - 감사 로그를 저장할 Azure Storage로 이동합니다.

     > [!IMPORTANT]
     > 동일한 지역의 저장소 계정을 관리되는 인스턴스 서버로 사용하여 지역 간 읽기/쓰기를 방지합니다.

   - 저장소 계정에서 **개요**로 이동한 다음, **Blob**을 클릭합니다.

     ![탐색 창][1]

   - 최상위 메뉴에서 **+ 컨테이너**를 클릭하여 새 컨테이너를 만듭니다.

     ![탐색 창][2]

   - 컨테이너 **이름**을 지정하고 공용 액세스 수준을 **개인**으로 설정한 다음,**확인**을 클릭합니다.

     ![탐색 창][3]

   - 컨테이너 목록에서 새로 만든 컨테이너를 클릭한 다음, **컨테이너 속성**을 클릭합니다.

     ![탐색 창][4]

   - 복사 아이콘을 클릭하여 컨테이너 URL을 복사하고 나중에 사용할 수 있도록 메모장 등에서 URL을 저장합니다. 컨테이너 URL 형식은 `https://<StorageName>.blob.core.windows.net/<ContainerName>`이어야 합니다.

     ![탐색 창][5]

3. 다음 단계에서는 저장소 계정에 관리되는 인스턴스 감사 액세스 권한을 부여하는 데 사용되는 Azure Storage **SAS 토큰**을 생성합니다.

   - 이전 단계에서 컨테이너를 만든 Azure Storage 계정으로 이동합니다.

   - 저장소 설정 메뉴에서 **공유 액세스 서명**을 클릭합니다.

     ![탐색 창][6]

   - 다음과 같이 SAS를 구성합니다.
     - **허용된 서비스**: Blob
     - **시작 날짜**: 표준 시간대 관련 문제를 방지하려면 어제 날짜를 사용하는 것이 좋습니다.
     - **종료 날짜**: 이 SAS 토큰이 만료되는 날짜를 선택합니다. 

       > [!NOTE]
       > 감사 오류를 방지하려면 만료 시 토큰을 갱신합니다.

     - **SAS 생성**을 클릭합니다.

       ![탐색 창][7]

   - SAS 생성을 클릭하면 SAS 토큰이 맨 아래에 표시됩니다. 복사 아이콘을 클릭하여 토큰을 복사하고 나중에 사용할 수 있도록 메모장 등에서 토큰을 저장합니다.

     > [!IMPORTANT]
     > 토큰 시작 부분에서 물음표(“?”) 문자를 제거합니다.

     ![탐색 창][8]

4. SSMS(SQL Server Management Studio)를 통해 관리되는 인스턴스에 연결합니다.

5. 다음 T-SQL 문을 실행하여 이전 단계에서 만든 컨테이너 URL 및 SAS 토큰으로 **새 자격 증명을 만듭니다**.

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. 다음 T-SQL 문을 실행하여 새 서버 감사를 만듭니다(고유한 감사 이름을 선택하고 이전 단계에서 만든 컨테이너 URL 사용).

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    지정되지 않은 경우 `RETENTION_DAYS` 기본값은 0(무제한 보존)입니다.

    추가 정보:
    - [관리되는 인스턴스, Azure SQL DB 및 SQL Server 간의 감사 차이점](#subheading-3)
    - [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. SQL Server에 대한 서버 감사 사양 또는 데이터베이스 감사 사양 만들기:
    - [서버 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [데이터베이스 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. 6단계에서 만든 서버 감사를 사용하도록 설정:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>감사 로그 분석
여러 방법으로 Blob 감사 로그를 볼 수 있습니다.

- 시스템 함수 `sys.fn_get_audit_file`(T-SQL)을 사용하여 테이블 형식의 감사 로그 데이터를 반환할 수 있습니다. 이 함수 사용에 대한 자세한 내용은 [sys.fn_get_audit_file 설명서](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)를 참조하세요.

- 감사 로그 사용 방법의 전체 목록은 [SQL 데이터베이스 감사 시작](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing)을 참조하세요.

> [!IMPORTANT]
> Azure Portal(‘감사 레코드’ 창)에서 감사 레코드를 보는 방법은 현재 관리되는 인스턴스에 사용할 수 없습니다.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>관리되는 인스턴스, Azure SQL Database 및 SQL Server 간의 감사 차이점

관리되는 인스턴스, Azure SQL Database 및 SQL Server 온-프레미스의 SQL Audit 간의 주요 차이점은 다음과 같습니다.

- 관리되는 인스턴스의 SQL Audit는 서버 수준에서 작동하며, `.xel` 로그 파일을 Azure Blob 저장소 계정에 저장합니다.
- Azure SQL Database에서 SQL Audit는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스/가상 머신의 SQL Audit는 서버 수준에서 작동하지만, 파일 시스템/Windows 이벤트 로그에 이벤트를 저장합니다.

관리되는 인스턴스의 XEvent 감사는 Azure Blob 저장소 대상을 지원합니다. 파일 및 Windows 로그는 **지원되지 않습니다**.

Azure Blob 저장소에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.
- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치되는 Azure Blob Storage 컨테이너의 URL을 지정할 수 있습니다.
- 관리되는 인스턴스에서 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 **지원되지 않습니다**.
- 종료 옵션은 **지원되지 않습니다**.
- `queue_delay` 0은 **지원되지 않습니다**.


## <a name="next-steps"></a>다음 단계

- 감사 로그 사용 방법의 전체 목록은 [SQL 데이터베이스 감사 시작](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing)을 참조하세요.
- 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
