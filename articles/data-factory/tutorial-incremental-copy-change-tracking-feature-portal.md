---
title: 변경 내용 추적 및 Azure Data Factory를 사용하여 데이터 증분 복사 | Microsoft Docs
description: '이 자습서에서는 델타 데이터를 증분 방식으로 온-프레미스 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL 데이터베이스로 복사하는 Azure Data Factory 파이프라인을 만듭니다. '
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/12/2018
ms.author: jingwang
ms.openlocfilehash: 891dad1a481c966e6ea1771f3e7c7850fa429352
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>변경 내용 추적 정보를 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터 증분 로드 
이 자습서에서는 원본 Azure SQL 데이터베이스의 **변경 내용 추적** 정보를 기반으로 Azure Blob 저장소에 델타 데이터를 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.  

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 변경 내용 추적 데이터 집합을 만듭니다.
> * 전체 복사 파이프라인을 생성, 실행 및 모니터링합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 증분 복사 파이프라인을 생성, 실행 및 모니터링합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## <a name="overview"></a>개요
데이터 통합 솔루션에서 초기 데이터로드 후 데이터 증분을 로드하는 것은 널리 사용되는 시나리오입니다. 경우에 따라 원본 데이터 저장소에서 특정 기간 내에 변경된 데이터를 쉽게 조각낼 수 있습니다(예: LastModifyTime, CreationTime). 데이터를 마지막으로 처리한 이후 델타 데이터를 식별하는 명시적인 방법이 없는 경우가 있습니다. 변경 내용 추적 기술은 Azure SQL Database와 같은 데이터 저장소 기술에 의해 지원되며 SQL Server는 델타 데이터를 파악하는 데 사용될 수 있습니다.  이 자습서는 Azure Data Factory 버전 2를 사용하여 SQL 변경 내용 추적 기술을 통해 Azure SQL Database에서 Azure Blob Storage로 델타 데이터를 증분 로드하는 방법을 설명합니다.  SQL 변경 내용 추적 기술에 대한 구체적인 정보는 [SQL Server에서 변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 참조하세요. 

## <a name="end-to-end-workflow"></a>종단 간 워크플로
다음은 변경 내용 추적 기술을 사용하여 데이터 증분을 로드하는 일반적인 종단 간 워크플로 단계입니다.

> [!NOTE]
> Azure SQL Database와 SQL Server 모두 변경 내용 추적 기술을 지원합니다. 이 자습서는 Azure SQL Database를 원본 데이터 저장소로 사용합니다. 온-프레미스 SQL Server를 사용할 수도 있습니다. 

1. **과거 데이터의 초기 로드**(한 번 실행):
    1. 원본 Azure SQL 데이터베이스에서 변경 내용 추적 기술을 사용하도록 설정합니다.
    2. Azure SQL 데이터베이스의 SYS_CHANGE_VERSION 초기 값을 변경된 데이터를 캡처하는 기준선으로 가져옵니다.
    3. Azure SQL 데이터베이스의 전체 데이터를 Azure Blob 저장소로 로드합니다. 
2. **일정에 따라 델타 데이터 증분 로드**(데이터의 초기 로드 후 주기적으로 실행):
    1. SYS_CHANGE_VERSION의 이전 값과 새 값을 가져옵니다.
    3. **sys.change_tracking_tables**의 변경된 행(두 SYS_CHANGE_VERSION 값 사이)의 기본 키를 **원본 테이블**의 데이터와 조인하여 델타 데이터를 로드한 다음 델타 데이터를 대상으로 이동합니다.
    4. 다음 번 델타 로드를 위해 the SYS_CHANGE_VERSION을 업데이트합니다.

## <a name="high-level-solution"></a>대략적인 솔루션
이 자습서에서는 다음 두 가지 작업을 수행하는 파이프라인을 만듭니다.  

1. **초기 로드:** 원본 데이터 저장소(Azure SQL Database)에서 대상 데이터 저장소(Azure Blob Storage)로 전체 데이터를 복사하는 복사 작업이 있는 파이프라인을 만듭니다.

    ![데이터 전체 로드](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **증분 로드:** 다음 작업이 있는 파이프라인을 만들어서 주기적으로 실행합니다. 
    1. Azure SQL Database의 기존 및 새 값을 가져오도록 **두 가지 조회 작업**을 만들어서 복사 작업에 전달합니다.
    2. 두 가지 SYS_CHANGE_VERSION 값 사이에 삽입된/업데이트된/삭제된 데이터를 Azure SQL Database에서 Azure Blob Storage로 복사하도록 **복사 작업을 하나** 만듭니다.
    3. 다음 번 파이프라인 실행을 위해 SYS_CHANGE_VERSION 값을 업데이트하도록 **저장 프로시저 작업을 하나** 만듭니다.

    ![증분 로드 흐름 다이어그램](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure SQL Database**. 데이터베이스를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.
* **Azure Storage 계정**. Blob 저장소를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요. **adftutorial**이라는 컨테이너를 만듭니다. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Azure SQL 데이터베이스에 데이터 원본 테이블 만들기
1. **SQL Server Management Studio**를 시작하고 Azure SQL Server에 연결합니다. 
2. **서버 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
3. Azure SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `data_source_table`이라는 테이블을 만듭니다.  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. 다음 SQL 쿼리를 실행하여 데이터베이스 및 원본 테이블(data_source_table)에서 **변경 내용 추적** 메커니즘을 사용하도록 설정합니다. 

    > [!NOTE]
    > - &lt;데이터베이스 이름&gt;을 data_source_table이 있는 Azure SQL 데이터베이스 이름으로 바꿉니다. 
    > - 현재 예제에서 변경된 데이터는 2일간 유지됩니다. 3일(또는 그 이상)마다 변경된 데이터를 로드하는 경우 일부 변경된 데이터가 포함되지 않습니다.  CHANGE_RETENTION 값을 더 큰 숫자로 변경해야 합니다. 또는 변경된 데이터를 로드하는 기간이 2일 이내가 되도록 해야 합니다. 자세한 내용은 [데이터베이스에 대한 변경 내용 추적 사용](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)을 참조하세요.
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 다음 쿼리를 실행하여 새 테이블을 만들고 ChangeTracking_version을 기본값으로 저장합니다. 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > SQL Database에 대한 변경 내용 추적을 사용하도록 설정한 후 데이터가 변경되지 않으면 변경 내용 추적 버전의 값은 0입니다.
6. 다음 쿼리를 실행하여 Azure SQL 데이터베이스에 저장 프로시저를 만듭니다. 파이프라인이 이전 단계에서 만든 테이블에서 변경 내용 추적 버전을 업데이트하도록 저장 프로시저를 호출합니다. 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
        리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.      
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.
11. **시작** 페이지에서 다음 이미지와 같이 왼쪽 패널의 **편집** 탭으로 전환합니다. 

    ![파이프라인 만들기 단추](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 Azure Storage 계정과 Azure SQL 데이터베이스에 연결된 서비스를 만듭니다. 

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스를 만듭니다.
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. **연결**, **+ 새로 만들기**를 차례로 클릭합니다.

   ![새 연결 단추](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. **새 연결된 서비스** 창에서 **Azure Blob Storage**를 선택하고 **계속**을 클릭합니다. 

   ![Azure Blob Storage 선택](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다. 
    2. **저장소 계정 이름**에 대해 Azure Storage 계정을 선택합니다. 
    3. **저장**을 클릭합니다. 
    
   ![Azure Storage 계정 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
이 단계에서는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다.

1. **연결**, **+ 새로 만들기**를 차례로 클릭합니다.
2. **새 연결된 서비스** 창에서 **Azure SQL Database**를 선택하고 **계속**을 클릭합니다. 
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름** 필드에 대해 **AzureSqlDatabaseLinkedService**를 입력합니다. 
    2. **서버 이름** 필드에 대해 Azure SQL Server를 선택합니다.
    4. **데이터베이스 이름** 필드에 대해 Azure SQL Database를 선택합니다. 
    5. **사용자 이름** 필드에 대해 사용자의 이름을 입력합니다. 
    6. **암호** 필드에 대해 사용자의 암호를 입력합니다. 
    7. **연결 테스트**를 클릭하여 연결을 테스트합니다.
    8. **저장**을 클릭하여 연결된 서비스를 저장합니다. 
    
       ![Azure SQL Database 연결된 서비스 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 데이터 원본, 데이터 대상을 나타내는 데이터 집합을 만듭니다. 그리고 SYS_CHANGE_VERSION을 저장할 곳을 만듭니다.

### <a name="create-a-dataset-to-represent-source-data"></a>원본 데이터를 나타내는 데이터 집합 만들기 
이 단계에서는 원본 데이터를 나타내는 데이터 집합을 만듭니다. 

1. 트리 뷰에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다. 

   ![새 데이터 집합 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 

   ![원본 데이터 집합 유형 - Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. 데이터 집합을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. **속성** 창에서 데이터 집합의 이름을 **SourceDataset**로 변경합니다.

   ![원본 데이터 집합 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 
    
    1. **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다. 
    2. **테이블**에 대해 **[dbo].[data_source_table]**을 선택합니다. 

   ![원본 연결](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>싱크 데이터 저장소에 복사된 데이터를 나타내는 데이터 집합 만들기 
이 단계에서는 원본 데이터 저장소에서 복사된 데이터를 나타내는 데이터 집합을 만듭니다. 필수 구성 요소의 일부로 adftutorial 컨테이너를 Azure Blob Storage에 만들었습니다. 아직 없는 경우 컨테이너를 만들거나 기존 컨테이너의 이름으로 설정합니다. 이 자습서에서 출력 파일 이름은 `@CONCAT('Incremental-', pipeline().RunId, '.txt')` 식을 사용하여 동적으로 생성됩니다.

1. 트리 뷰에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다. 

   ![새 데이터 집합 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다. 

   ![싱크 데이터 집합 유형 - Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. 데이터 집합을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. **속성** 창에서 데이터 집합의 이름을 **SinkDataset**로 변경합니다.

   ![싱크 데이터 집합 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. [속성] 창에서 **연결** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **연결된 서비스**에 대해 **AzureStorageLinkedService**를 선택합니다.
    2. **파일 경로**의 **폴더** 부분에 대해 **adftutorial/incchgtracking**을 입력합니다.
    3. **파일 경로**의 **파일** 부분에 대해 **@CONCAT('Incremental-', pipeline().RunId, '.txt')**를 입력합니다.  

       ![싱크 데이터 집합 - 연결](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>변경 내용 추적 데이터를 나타내는 데이터 집합 만들기 
이 단계에서는 변경 내용 추적 버전을 저장하기 위한 데이터 집합을 만듭니다.  필수 구성 요소의 일부로 table_store_ChangeTracking_version 테이블을 만들었습니다.

1. 트리 뷰에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다. 
2. **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 
3. 데이터 집합을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. **속성** 창에서 데이터 집합의 이름을 **ChangeTrackingDataset**로 변경합니다.
4. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 
    
    1. **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다. 
    2. **테이블**에 대해 **[dbo].[table_store_ChangeTracking_version]**을 선택합니다. 

## <a name="create-a-pipeline-for-the-full-copy"></a>전체 복사본에 대한 파이프라인 만들기
이 단계에서는 원본 데이터 저장소(Azure SQL Database)에서 대상 데이터 저장소(Azure Blob Storage)로 전체 데이터를 복사하는 복사 작업이 있는 파이프라인을 만듭니다.

1. 왼쪽 창에서 **+(더하기)**, **파이프라인**을 차례로 클릭합니다. 

    ![새 파이프라인 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. 파이프라인을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 파이프라인이 표시됩니다. **속성** 창에서 파이프라인 이름을 **FullCopyPipeline**으로 변경합니다.

    ![새 파이프라인 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, 이름을 **FullCopyActivity**로 설정합니다. 

    ![전체 복사 활동 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. **원본** 탭으로 전환하고, **원본 데이터 집합** 필드에 대해 **SourceDataset**를 선택합니다. 

    ![복사 활동 - 원본](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. **싱크** 탭으로 전환하고, **싱크 데이터 집합** 필드에 대해 **SinkDataset**를 선택합니다. 

    ![복사 활동 - 싱크](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. 파이프라인 정의에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **>>**를 클릭하여 **파이프라인 유효성 검사 보고서**를 닫습니다. 

    ![파이프라인 유효성 검사](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 게시하려면 **게시**를 클릭합니다. 게시가 성공적으로 완료될 때까지 기다립니다. 

    ![게시 단추](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. **게시됨** 메시지가 표시될 때까지 기다립니다. 

    ![게시 성공](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. 왼쪽의 **알림 표시** 단추를 클릭하여 알림을 볼 수도 있습니다. 알림 창을 닫으려면 **X**를 클릭합니다.

    ![알림 표시](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>전체 복사 파이프라인 실행
파이프라인에 대한 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

![지금 트리거 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>전체 복사 파이프라인 모니터링

1. 왼쪽의 **모니터** 탭을 클릭합니다. 파이프라인 실행 및 해당 상태가 목록에 표시됩니다. 목록을 새로 고치려면 **새로 고침**을 클릭합니다. [작업] 열의 링크를 사용하면 파이프라인 실행과 연결된 활동 실행을 보고 파이프라인을 다시 실행할 수 있습니다. 

    ![파이프라인 실행](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 활동만 있으므로 목록에는 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. 

    ![작업 실행](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>결과 검토
`adftutorial` 컨테이너의 `incchgtracking` 폴더에 `incremental-<GUID>.txt`라는 파일이 표시됩니다. 

![전체 복사의 출력 파일](media\tutorial-incremental-copy-change-tracking-feature-portal\full-copy-output-file.png)

파일에는 Azure SQL 데이터베이스의 데이터가 포함됩니다.

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>원본 테이블에 데이터 추가

Azure SQL 데이터베이스에 다음 쿼리를 실행하여 행을 추가하고 행을 업데이트합니다. 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>델타 복사를 위한 파이프라인 만들기
이 단계에서는 다음 작업이 있는 파이프라인을 만들어서 주기적으로 실행합니다. **조회 작업**은 Azure SQL Database에서 SYS_CHANGE_VERSION의 기존 및 새 값을 가져와서 만들어서 복사 작업에 전달합니다. **복사 작업**은 두 가지 SYS_CHANGE_VERSION 값 사이에 삽입된/업데이트된/삭제된 데이터를 Azure SQL Database에서 Azure Blob Storage로 복사합니다. **저장 프로시저 작업**은 다음 번 파이프라인 실행을 위해 SYS_CHANGE_VERSION 값을 업데이트합니다.

1. Data Factory UI에서 **편집** 탭으로 전환합니다. 왼쪽 창에서 **+(더하기)**, **파이프라인**을 차례로 클릭합니다. 

    ![새 파이프라인 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. 파이프라인을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 파이프라인이 표시됩니다. **속성** 창에서 파이프라인 이름을 **IncrementalCopyPipeline**으로 변경합니다.

    ![파이프라인 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. **활동** 도구 상자에서 **일반**을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **LookupLastChangeTrackingVersionActivity**로 설정합니다. 이 활동은 **table_store_ChangeTracking_version** 테이블에 저장된 마지막 복사 작업에서 사용된 변경 내용 추적 버전을 가져옵니다.

    ![조회 활동 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. **속성** 창에서 **설정**으로 전환하고, **원본 데이터 집합** 필드에 대해 **ChangeTrackingDataset**를 선택합니다. 

    ![조회 활동 - 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. **조회** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **LookupCurrentChangeTrackingVersionActivity**로 설정합니다. 이 활동은 현재 변경 내용 추적 버전을 가져옵니다.

    ![조회 활동 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. **속성** 창에서 **설정**으로 전환하고 다음 단계를 수행합니다.

    1. **원본 데이터 집합** 필드에 대해 **SourceDataset**를 선택합니다.
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다. 

        ```sql
        SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
        ```

    ![조회 활동 - 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **IncrementalCopyActivity**로 설정합니다. 이 활동은 마지막 변경 내용 추적 버전과 현재 변경 내용 추적 버전 사이의 데이터를 대상 데이터 저장소에 복사합니다. 

    ![복사 활동 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. **속성** 창에서 **원본**으로 전환하고 다음 단계를 수행합니다.

    1. **원본 데이터 집합**에 대해 **SourceDataset**를 선택합니다. 
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다. 

        ```sql
        select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
        ```
    
    ![복사 활동 - 원본 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. **싱크** 탭으로 전환하고, **싱크 데이터 집합** 필드에 대해 **SinkDataset**를 선택합니다. 

    ![복사 활동 - 싱크 설정](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **두 개의 조회 활동을 하나씩 복사 활동에 연결합니다**. **조회** 활동에 붙어 있는 **녹색** 단추를 **복사** 활동으로 끕니다. 

    ![조회 활동 및 복사 활동 연결](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. **저장 프로시저** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **StoredProceduretoUpdateChangeTrackingActivity**로 설정합니다. 이 활동은 **table_store_ChangeTracking_version** 테이블의 변경 내용 추적 버전을 업데이트합니다.

    ![저장 프로시저 활동 - 이름](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. *SQL 계정** 탭으로 전환하고, **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다. 

    ![저장 프로시저 활동 - SQL 계정](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. **저장 프로시저** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **저장 프로시저 이름**에 대해 **Update_ChangeTracking_Version**을 선택합니다.  
    2. **가져오기 매개 변수**를 선택합니다. 
    3. **저장 프로시저 매개 변수** 섹션에서 매개 변수에 대해 다음 값을 지정합니다. 

        | Name | 형식 | 값 | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | 문자열 | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![저장 프로시저 활동 - 매개 변수](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **복사 활동을 저장 프로시저 활동에 연결합니다**. 복사 활동에 붙어 있는 **녹색** 단추를 저장 프로시저 활동으로 끌어서 놓습니다. 

    ![복사 활동 및 저장 프로시저 활동 연결](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. 도구 모음에서 **유효성 검사**를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **>>**를 클릭하여 **파이프라인 유효성 검사 보고서** 창을 닫습니다. 

    ![유효성 검사 단추](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16.  **모두 게시** 단추를 클릭하여 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 Data Factory 서비스에 게시합니다. **게시 성공** 메시지가 표시될 때까지 기다립니다. 

        ![게시 단추](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>증분 복사 파이프라인 실행
1. 파이프라인에 대한 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

    ![지금 트리거 메뉴](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. **파이프라인 실행** 창에서 **마침**을 선택합니다.

### <a name="monitor-the-incremental-copy-pipeline"></a>증분 복사 파이프라인 모니터링
1. 왼쪽의 **모니터** 탭을 클릭합니다. 파이프라인 실행 및 해당 상태가 목록에 표시됩니다. 목록을 새로 고치려면 **새로 고침**을 클릭합니다. **작업** 열의 링크를 사용하면 파이프라인 실행과 연결된 활동 실행을 보고 파이프라인을 다시 실행할 수 있습니다. 

    ![파이프라인 실행](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 활동만 있으므로 목록에는 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. 

    ![작업 실행](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>결과 검토
`adftutorial` 컨테이너의 `incchgtracking` 폴더에 두 번째 파일이 표시됩니다. 

![증분 복사의 출력 파일](media\tutorial-incremental-copy-change-tracking-feature-portal\incremental-copy-output-file.png)

파일에는 Azure SQL 데이터베이스의 델타 데이터만 포함됩니다. `U`가 포함된 레코드가 데이터베이스에서 업데이트된 행이고 `I`가 추가된 행입니다. 

```
1,update,10,2,U
6,new,50,1,I
```
처음 세 열은 data_source_table에서 변경된 데이터입니다. 마지막 두 열은 변경 내용 추적 시스템 테이블의 메타데이터입니다. 네 번째 열은 변경된 각 행의 SYS_CHANGE_VERSION입니다. 다섯 번째 열의 U=업데이트, I=삽입 작업입니다.  변경 내용 추적 정보에 대한 자세한 내용은 [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)을 참조하세요. 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>다음 단계
Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[클라우드에서 Spark 클러스터를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)



