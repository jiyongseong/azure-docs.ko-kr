---
title: Azure SQL Data Sync(미리 보기) 설정 | Microsoft Docs
description: 이 자습서에서는 Azure SQL Data Sync(미리 보기) 설정 방법을 보여 줍니다.
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 86b0e78f362d1cf3c2480aad97ef5281c5f3bc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-sql-data-sync-preview"></a>SQL 데이터 동기화(미리 보기) 설정
이 자습서에서는 Azure SQL Database와 SQL Server 인스턴스를 모두 포함하는 하이브리드 동기화 그룹을 만들어 Azure SQL 데이터 동기화를 설정하는 방법에 대해 설명합니다. 새 동기화 그룹을 완벽하게 구성하고 설정한 일정에 동기화합니다.

이 자습서에서는 사용자가 SQL Database 및 SQL Server를 사용해본 경험이 있다고 가정합니다. 

SQL 데이터 동기화에 대한 개요는 [Azure SQL 데이터 동기화(미리 보기)를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-database-sync-data.md)를 참조하세요.

SQL Data Sync 구성 방법을 보여주는 전체 PowerShell 예제는 다음 문서를 참조하세요.
-   [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
-   [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>1단계 - 동기화 그룹 만들기

### <a name="locate-the-data-sync-settings"></a>데이터 동기화 설정 찾기

1.  브라우저에서 Azure Portal로 이동합니다.

2.  포털의 대시보드 또는 툴바의 SQL Database 아이콘에서 SQL Database를 찾습니다.

    ![Azure SQL Database 목록](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  **SQL Database** 페이지에서 데이터 동기화의 허브 데이터베이스로 사용하려는 기존 SQL Database를 선택합니다. SQL Database 페이지가 열립니다.

    허브 데이터베이스는 동기화 그룹에 여러 데이터베이스 엔드포인트를 갖는 동기화 토폴로지의 중앙 엔드포인트입니다. 동일한 동기화 그룹의 다른 모든 데이터베이스 엔드포인트입니다. 즉, 모든 멤버 데이터베이스는 허브 데이터베이스와 동기화합니다.

4.  선택한 데이터베이스의 SQL Database 페이지에서 **다른 데이터베이스에 동기화**를 선택합니다. 데이터 동기화 페이지가 열립니다.

    ![다른 데이터베이스 옵션에 동기화](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>새 동기화 그룹 만들기

1.  데이터 동기화 페이지에서 **새 동기화 그룹**을 선택합니다. 1단계인 **동기화 그룹 만들기**가 강조 표시된 상태로 **새 동기화 그룹** 페이지가 열립니다. **데이터 동기화 그룹 만들기** 페이지도 열립니다.

2.  **데이터 동기화 그룹 만들기** 페이지에서 다음을 수행합니다.

    1.  **동기화 그룹 이름은** 필드에서 새 동기화 그룹의 이름을 입력합니다.

    2.  **동기화 메타데이터 데이터베이스** 섹션에서 새 데이터베이스를 만들지(권장) 아니면 기존 데이터베이스를 사용할지를 선택합니다.

        > [!NOTE]
        > Microsoft에서는 동기화 메타데이터 데이터베이스로 사용할 비어 있는 새 데이터베이스를 만들도록 권장합니다. 데이터 동기화는 이 데이터베이스에서 테이블을 만들고 자주 실행되는 워크로드를 실행합니다. 이 데이터베이스는 선택한 지역에서 모든 동기화 그룹의 동기화 메타데이터 데이터베이스로 자동으로 공유됩니다. 동기화 메타데이터 데이터베이스 또는 해당 이름을 삭제하지 않고 변경할 수 없습니다.

        **새 데이터베이스**를 선택한 경우 **새 데이터베이스 만들기**를 선택합니다. **SQL Database** 페이지가 열립니다. **SQL Database** 페이지에서 새 데이터베이스의 이름을 지정하고 구성합니다. 그런 다음 **확인**을 선택합니다.

        **기존 데이터베이스 사용**을 선택한 경우 목록에서 데이터베이스를 선택합니다.

    3.  **자동 동기화** 섹션에서 먼저 **켜기** 또는 **끄기**를 선택합니다.

        **켜기**를 선택한 경우 **동기화 빈도** 섹션에서 숫자를 입력하고 초, 분, 시간 또는 일을 선택합니다.

        ![동기화 빈도 지정](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  **충돌 해결** 섹션에서 "허브 우선" 또는 "구성원 우선"을 선택합니다.

        "허브 우선"은 충돌이 발생할 경우 허브 데이터베이스의 데이터가 멤버 데이터베이스에서 충돌하는 데이터를 덮어쓰는 것을 의미합니다. "멤버 우선"은 충돌이 발생할 경우 멤버 데이터베이스의 데이터가 허브 데이터베이스에서 충돌하는 데이터를 덮어쓰는 것을 의미합니다. 

        ![충돌 해결 방법 지정](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  **확인**을 선택하고 새 동기화 그룹을 만들고 배포할 때까지 기다립니다.

## <a name="step-2---add-sync-members"></a>2단계 - 동기화 구성원 추가

새 동기화 그룹을 만들고 배포한 후 2단계인 **동기화 구성원 추가**가 **새 동기화 그룹** 페이지에서 강조 표시됩니다.

**허브 데이터베이스** 섹션에서 허브 데이터베이스가 있는 SQL Database 서버에 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다.

![동기화 그룹에 추가된 허브 데이터베이스](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Azure SQL Database 추가

**구성원 데이터베이스** 섹션에서 필요에 따라 **Azure 데이터베이스 추가**를 선택하여 Azure SQL Database를 동기화 그룹에 추가합니다. **Azure Database 구성** 페이지가 열립니다.

**Azure Database 구성** 페이지에서 다음을 수행합니다.

1.  **동기화 구성원 이름** 필드에서 새 동기화 구성원의 이름을 제공합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다.

2.  요금 청구를 위해 **구독** 필드에서 연결된 Azure 구독을 선택합니다.

3.  **Azure SQL Server** 필드에서 기존 SQL Database 서버를 선택합니다.

4.  **Azure SQL Database** 필드에서 기존 SQL Database를 선택합니다.

5.  **동기화 방향** 필드에서 양방향 동기화, 허브 수신 또는 허브 발신을 선택합니다.

    ![새 SQL Database 동기화 구성원 추가](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  **사용자 이름** 및 **암호** 필드에서 구성원 데이터베이스가 있는 SQL Database 서버에 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다.

7.  **확인**을 선택하고 새 동기화 구성원을 만들고 배포할 때까지 기다립니다.

    ![새 SQL Database 동기화 구성원 추가](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a>온-프레미스 SQL Server 데이터베이스 추가

**구성원 데이터베이스** 섹션에서 필요에 따라 **온-프레미스 데이터베이스 추가**를 선택하여 온-프레미스 SQL Server를 동기화 그룹에 추가합니다. **온-프레미스 구성** 페이지가 열립니다.

**온-프레미스 구성** 페이지에서 다음을 수행합니다.

1.  **동기화 에이전트 게이트웨이 선택**을 선택합니다. **동기화 에이전트 선택** 페이지가 열립니다.

    ![동기화 에이전트 게이트웨이 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  **동기화 에이전트 게이트웨이 선택** 페이지에서 기존 에이전트를 사용할지 새 에이전트를 만들지를 선택합니다.

    **기존 에이전트**를 선택한 경우 목록에서 기존 에이전트를 선택합니다.

    **새 에이전트 만들기**를 선택한 경우 다음을 수행합니다.

    1.  제공된 링크에서 클라이언트 동기화 에이전트 소프트웨어를 다운로드하고 SQL Server가 있는 컴퓨터에 설치합니다.
 
        > [!IMPORTANT]
        > 클라이언트 에이전트가 서버와 통신할 수 있도록 방화벽에서 아웃바운드 TCP 포트 1433을 열어야 합니다.


    2.  에이전트의 이름을 입력합니다.

    3.  **키 만들기 및 생성**을 선택합니다.

    4.  에이전트 키를 클립보드에 복사합니다.
        
        ![새 동기화 에이전트 만들기](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  **확인**을 선택하여 **동기화 에이전트 선택** 페이지를 닫습니다.

    6.  SQL Server 컴퓨터에서 클라이언트 동기화 에이전트 앱을 찾아 실행합니다.

        ![데이터 동기화 클라이언트 에이전트 앱](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  동기화 에이전트 앱에서 **에이전트 키 전송**을 선택합니다. **동기화 메타데이터 데이터베이스 구성** 대화 상자가 열립니다.

    8.  **동기화 메타데이터 데이터베이스 구성** 대화 상자에 Azure Portal에서 복사된 에이전트 키를 붙여 넣습니다. 또한 메타데이터 데이터베이스가 있는 Azure SQL Database 서버에 기존 자격 증명을 입력합니다. (새 메타데이터 데이터베이스를 만든 경우 이 데이터베이스는 허브 데이터베이스와 동일한 서버에 위치합니다.) **확인**을 선택하고 구성이 완료되기를 기다립니다.

        ![에이전트 키 및 서버 자격 증명을 입력합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   이 시점에서 방화벽 오류가 발생하면 Azure에 대해 SQL Server 컴퓨터에서 들어오는 트래픽을 허용하는 방화벽 규칙을 만들어야 합니다. 포털에서 규칙을 수동으로 만들 수 있지만 SSMS(SQL Server Management Studio)에서 더 쉽게 만들 수 있습니다. SSMS에서 Azure의 허브 데이터베이스에 연결하려고 합니다. 데이터베이스 이름을 <hub_database_name>.database.windows.net으로 입력합니다. Azure 방화벽 규칙을 구성하려면 대화 상자의 단계를 따릅니다. 그런 다음 클라이언트 동기화 에이전트 앱에 반환합니다.

    9.  클라이언트 동기화 에이전트 앱에서 **등록**을 클릭하여 에이전트와 SQL Server 데이터베이스를 등록합니다. **SQL Server 구성** 대화 상자가 열립니다.

        ![SQL Server 데이터베이스를 추가하고 구성합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. **SQL Server 구성** 대화 상자에서 연결에 SQL Server 인증을 사용할지 아니면 Windows 인증을 사용할지를 선택합니다. SQL Server 인증을 선택한 경우 기존 자격 증명을 입력합니다. 동기화할 SQL Server 이름 및 데이터베이스의 이름을 제공합니다. **연결 테스트**를 선택하여 설정을 테스트합니다. 그런 다음 **저장**을 선택합니다. 등록된 데이터베이스가 목록에 나타납니다.

        ![SQL Server 데이터베이스를 지금 등록합니다.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. 이제 클라이언트 동기화 에이전트 앱을 닫을 수 있습니다.

    12. 포털의 **온-프레미스 구성** 페이지에서 **데이터베이스 선택**을 선택합니다. **데이터베이스 선택** 페이지가 열립니다.

    13. **데이터베이스 선택** 페이지의 **동기화 구성원 이름** 필드에서 새 동기화 구성원의 이름을 입력합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다. 목록에서 데이터베이스를 선택합니다. **동기화 방향** 필드에서 양방향 동기화, 허브 수신 또는 허브 발신을 선택합니다.

        ![온-프레미스 데이터베이스 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. **확인**을 선택하여 **데이터베이스 선택** 페이지를 닫습니다. 그런 다음 **확인**을 선택하여 **온-프레미스 구성** 페이지를 닫고 새 동기화 구성원이 만들어지고 배포될 때까지 기다립니다. 마지막으로 **확인**을 클릭하여 **동기화 구성원 선택** 페이지를 닫습니다.

        ![동기화 그룹에 추가된 온-프레미스 데이터베이스](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  SQL 데이터 동기화 및 로컬 에이전트에 연결하려면 역할 `DataSync_Executor`에 사용자 이름을 추가합니다. 데이터 동기화는 SQL Server 인스턴스에서 이 역할을 만듭니다.

## <a name="step-3---configure-sync-group"></a>3단계 - 동기화 그룹 구성

새 동기화 그룹 구성원이 만들어지고 배포된 후에 3단계인 **동기화 그룹 구성**이 **새 동기화 그룹** 페이지에서 강조 표시됩니다.

1.  **테이블** 페이지의 동기화 그룹 구성원 목록에서 데이터베이스를 선택한 다음 **스키마 새로 고침**을 선택합니다.

2.  사용 가능한 테이블 목록에서 동기화하려는 테이블을 선택합니다.

    ![동기화할 테이블 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  기본적으로 테이블의 모든 열이 선택됩니다. 모든 열을 동기화하지 않으려면 동기화하지 않으려는 열에 확인란을 사용하지 않도록 설정합니다. 선택한 기본 키 열을 남겨 두어야 합니다.

    ![동기화할 필드 선택](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  마지막으로 **저장**을 선택합니다.

## <a name="faq-about-setup-and-configuration"></a>설정 및 구성에 대한 질문과 대답

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>데이터 동기화에서 데이터를 동기화하는 빈도는 어떻게 되나요? 
최소 빈도는 5분마다입니다.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL 데이터 동기화가 테이블을 완벽하게 만들어 프로비전하나요?

동기화 스키마 테이블이 대상 데이터베이스에 아직 만들어지지 않았으면 SQL 데이터 동기화(미리 보기)가 사용자가 선택한 열로 해당 테이블을 만듭니다.  그러나 다음과 같은 이유로 이 동작에서 완전히 충실한 스키마가 만들어지지는 않습니다.

-   사용자가 선택한 열만 대상 테이블에 만들어집니다. 원본 테이블의 일부 열이 동기화 그룹에 속하지 않으면 해당 열은 대상 테이블에서 프로비전되지 않습니다.

-   인덱스는 선택한 열에 대해서만 생성됩니다. 원본 테이블 인덱스에 동기화 그룹의 일부가 아닌 열이 있으면 해당 인덱스는 대상 테이블에서 프로비전되지 않습니다.

-   XML 유형 열의 인덱스는 프로비전되지 않습니다.

-   CHECK 제약 조건은 프로비전되지 않습니다.

-   원본 테이블의 기존 트리거는 프로비전되지 않습니다.

-   뷰 및 저장 프로시저는 대상 데이터베이스에 생성되지 않습니다.

이러한 제한 때문에 다음이 권장됩니다.
-   프로덕션 환경에서는 완전히 충실한 스키마를 직접 프로비전합니다.
-   서비스 테스트에는 SQL 데이터 동기화(미리 보기)의 자동 프로비전 기능이 잘 작동합니다.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>내가 만들지 않은 테이블이 표시되는 이유는 무엇인가요?  
데이터 동기화는 변경 내용 추적을 위해 데이터베이스에 추가 테이블을 만듭니다. 해당 테이블을 삭제하지 마세요. 삭제하면 데이터 동기화의 작동이 중지됩니다.

### <a name="is-my-data-convergent-after-a-sync"></a>동기화 후 내 데이터가 일치하나요?

그럴 필요는 없습니다. 허브와 3개의 스포크(A, B, C)가 있는 동기화 그룹에서 동기화는 허브에서 A, 허브에서 B, 허브에서 C로 이루어집니다. 허브에서 A로 동기화된 *후* 변경이 있으면 이 변경 내용은 다음 번 동기화 시점까지 데이터베이스 B나 데이터베이스 C에 작성되지 않습니다.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>동기화 그룹에 스키마 변경 내용은 어떻게 가져오나요?

스키마를 변경하고 모든 변경 내용을 수동으로 전파해야 합니다.
1. 스키마 변경 내용을 모든 허브 및 모든 동기화 멤버에 수동으로 복제합니다.
2. 동기화 스키마를 업데이트합니다.

**새 테이블 및 열 추가**. 새 테이블 및 열은 현재 동기화에 영향을 미치지 않습니다. 데이터 동기화는 새 테이블 및 열을 동기화 스키마에 추가할 때까지 무시합니다. 새 데이터베이스 개체를 추가할 때는 다음 순서대로 작업하는 것이 가장 좋습니다.
1. 허브 및 모든 동기화 멤버에 새 테이블이나 열을 추가합니다.
2. 동기화 스키마에 새 테이블이나 열을 추가합니다.
3. 새 테이블 및 열에 값을 삽입하기 시작합니다.

**열의 데이터 형식 변경**. 기존 열의 데이터 형식을 변경하면 데이터 동기화는 새 값이 동기화 스키마에 정의된 원본 데이터 형식과 잘 맞을 경우 계속 작동합니다. 예를 들어, 원본 데이터베이스에서 데이터 형식을 **int**에서 **bigint**로 변경할 경우, **int** 데이터 형식에 비해 너무 큰 값을 삽입하기 전까지는 데이터 동기화가 계속 작동합니다. 변경을 완료하려면 스키마 변경 내용을 허브와 모든 동기화 멤버에 수동으로 복제한 다음, 동기화 스키마를 업데이트합니다.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>데이터 동기화를 사용하여 데이터베이스를 어떻게 내보내고 가져오나요?
데이터베이스를 `.bacpac` 파일로 내보내고 새 데이터베이스를 만들기 위해 파일을 가져온 후에 새 데이터베이스에서 데이터 동기화를 사용하려면 다음 두 가지 작업을 수행해야 합니다.
1.  [이 스크립트](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql)를 사용하여 **새 데이터베이스**에서 데이터 동기화 개체와 파생 테이블을 정리합니다. 이 스크립트는 데이터베이스에서 모든 필요한 데이터 동기화 개체를 삭제합니다.
2.  새 데이터베이스를 통해 동기화 그룹을 다시 만듭니다. 더 이상 기존 동기화 그룹이 필요하지 않으면 삭제합니다.

## <a name="faq-about-the-client-agent"></a>클라이언트 에이전트에 대한 질문과 대답

### <a name="why-do-i-need-a-client-agent"></a>클라이언트 에이전트가 왜 필요한가요?

SQL 데이터 동기화(미리 보기) 서비스는 클라이언트 에이전트를 통해 SQL Server 데이터베이스와 통신합니다. 이 보안 기능은 방화벽 뒤에 있는 데이터베이스와의 직접 통신을 방지합니다. SQL 데이터 동기화(미리 보기) 서비스가 에이전트와 통신할 때는 암호화된 연결과 고유 토큰 또는 *에이전트 키*를 사용합니다. SQL Server 데이터베이스는 연결 문자열과 에이전트 키를 사용하여 에이전트를 인증합니다. 이러한 설계는 데이터에 높은 수준의 보안을 제공합니다.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>실행할 수 있는 로컬 에이전트의 UI의 인스턴스는 얼마나 되나요?

UI 인스턴스는 하나만 실행할 수 있습니다.

### <a name="how-can-i-change-my-service-account"></a>내 서비스 계정은 어떻게 변경하나요?

클라이언트 에이전트를 설치한 후 서비스 계정을 변경하는 유일한 방법은 제거한 후 새 서비스 계정으로 새 클라이언트 에이전트를 설치하는 것뿐입니다.

### <a name="how-do-i-change-my-agent-key"></a>내 에이전트 키는 어떻게 변경하나요?

에이전트 키는 에이전트에서 한 번만 사용할 수 있습니다. 제거 후 새 에이전트를 설치하면 재사용할 수 없고 여러 에이전트에서 사용할 수도 없습니다. 기존 에이전트에 대해 새 키를 만들어야 할 경우, 클라이언트 에이전트와 SQL 데이터 동기화(미리 보기) 서비스에 같은 키가 기록되도록 해야 합니다.

### <a name="how-do-i-retire-a-client-agent"></a>클라이언트 에이전트는 어떻게 사용 중지하나요?

에이전트를 즉시 무효화하거나 사용 중지하려면 포털에서 키를 다시 생성하고 에이전트 UI에는 제출하지 않습니다. 키를 다시 생성하면 해당 에이전트가 온라인이던 오프라인이던 관계없이 이전 키가 무효화됩니다.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>클라이언트 에이전트를 어떻게 다른 컴퓨터로 이동하나요?

현재 실행 중인 컴퓨터가 아닌 다른 컴퓨터에서 로컬 에이전트를 실행하려면 다음을 수행합니다.

1. 원하는 컴퓨터에 에이전트를 설치합니다.

2. SQL 데이터 동기화(미리 보기) 포털에 로그인하고 새 에이전트에 대해 에이전트 키를 다시 생성합니다.

3. 새 에이전트의 UI를 사용하여 새 에이전트 키를 제출합니다.

4. 클라이언트 에이전트가 앞서 등록된 온-프레미스 데이터베이스 목록을 다운로드하는 동안 기다립니다.

5. 연결할 수 없다고 표시된 모든 데이터베이스에 대해 데이터베이스 자격 증명을 제공합니다. 이러한 데이터베이스는 에이전트가 설치된 새 컴퓨터에서 연결할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
축하합니다. SQL Database 인스턴스와 SQL Server 데이터베이스가 모두 포함된 동기화 그룹을 만들었습니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

-   [Azure SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화](sql-database-sync-data.md)
-   [Azure SQL 데이터 동기화 모범 사례](sql-database-best-practices-data-sync.md)
-   [Log Analytics를 사용하여 Azure SQL 데이터 동기화 모니터링](sql-database-sync-monitor-oms.md)
-   [Azure SQL 데이터 동기화 문제 해결](sql-database-troubleshoot-data-sync.md)

-   SQL Data Sync 구성 방법을 보여주는 전체 PowerShell 예제:
    -   [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL 데이터 동기화 REST API 설명서 다운로드](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database에 대한 자세한 내용은 다음을 참조하세요.

-   [SQL Database 개요](sql-database-technical-overview.md)
-   [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)
