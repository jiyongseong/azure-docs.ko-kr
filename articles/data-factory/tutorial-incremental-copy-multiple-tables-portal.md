---
title: Azure Data Factory를 사용하여 증분 방식으로 여러 테이블 복사 | Microsoft Docs
description: 이 자습서에서는 델타 데이터를 증분 방식으로 온-프레미스 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL 데이터베이스로 복사하는 Azure Data Factory 파이프라인을 만듭니다.
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
ms.date: 01/20/2018
ms.author: jingwang
ms.openlocfilehash: 399e132f0a28ffc6b60e3d757afff5aae60f7674
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>SQL Server의 여러 테이블에서 Azure SQL 데이터베이스로 데이터 증분 로드
이 자습서에서는 델타 데이터를 온-프레미스 SQL Server의 여러 테이블에서 Azure SQL 데이터베이스로 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.    

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * Integration Runtime을 설치합니다. 
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 집합을 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. 일반 공급되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## <a name="overview"></a>개요
이 솔루션을 만드는 중요한 단계는 다음과 같습니다. 

1. **워터마크 열을 선택합니다**.
    
    원본 데이터 저장소에서 각 테이블에 대해 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 식별하는 데 사용할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.

2. **워터마크 값을 저장할 데이터 저장소를 준비합니다**.   
    
    이 자습서에서는 SQL 데이터베이스에 워터마크 값을 저장합니다.

3. **다음 작업을 사용하여 파이프라인을 만듭니다.** 
    
    a. 파이프라인에 매개 변수로 전달되는 원본 테이블 이름 목록을 반복하는 ForEach 작업을 만듭니다. 각 원본 테이블에 해당 테이블에 대한 델타 로드를 수행하는 다음 작업을 호출합니다.

    b. 두 가지 조회 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다.

    c. 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 복사 작업을 만듭니다. 그런 다음 원본 데이터 저장소의 델타 데이터를 새 파일로 Azure Blob 저장소에 복사합니다.

    d. 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 StoredProcedure 작업을 만듭니다. 

    대략적인 솔루션 다이어그램은 다음과 같습니다. 

    ![데이터 증분 로드](media\tutorial-incremental-copy-multiple-tables-portal\high-level-solution-diagram.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **SQL Server**. 이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 원본 데이터 저장소로 사용합니다. 
* **Azure SQL Database**. SQL 데이터베이스를 싱크 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 만드는 단계를 [Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)에서 참조하세요. 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server 데이터베이스에 원본 테이블 만들기

1. SQL Server Management Studio를 열고 온-프레미스 SQL Server 데이터베이스에 연결합니다.

2. **서버 탐색기**에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

3. 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`이라는 테이블을 만듭니다.

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Azure SQL 데이터베이스에 대상 테이블 만들기
1. SQL Server Management Studio를 열고 Azure SQL 데이터베이스에 연결합니다.

2. **서버 탐색기**에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

3. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`이라는 테이블을 만듭니다.  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>상위 워터마크 값을 저장하기 위해 Azure SQL 데이터베이스에 또 다른 테이블 만들기
1. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장할 `watermarktable` 테이블을 만듭니다. 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. 두 원본 테이블의 초기 워터마크 값을 워터마크 테이블에 삽입합니다.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Azure SQL 데이터베이스에 저장 프로시저 만들기 

다음 명령을 실행하여 SQL 데이터베이스에 저장 프로시저를 만듭니다. 이 저장 프로시저는 파이프라인의 실행이 끝날 때마다 워터마크 값을 업데이트합니다. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Azure SQL 데이터베이스에 데이터 형식 및 추가 저장 프로시저 만들기
다음 쿼리를 실행하여 SQL 데이터베이스에 두 개의 데이터 형식과 두 개의 저장 프로시저를 만듭니다. 원본 테이블의 데이터를 대상 테이블에 병합하는 데 사용됩니다.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFMultiIncCopyTutorialDF**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFMultiIncCopyTutorialDF) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
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

    ![데이터 팩터리 배포 중 타일](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.
11. Azure Data Factory UI의 시작 페이지에서 **파이프라인 만들기**를 클릭하거나 **편집** 탭으로 전환합니다. 

   ![시작 페이지](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기
개인 네트워크(온-프레미스)의 데이터 저장소에서 Azure 데이터 저장소로 데이터를 이동할 때 온-프레미스 환경에 자체 호스팅 IR(통합 런타임)을 설치합니다. 자체 호스팅 IR은 개인 네트워크와 Azure 간에 데이터를 이동합니다. 

1. 왼쪽 창의 아래에서 **연결**을 클릭하고 **연결** 창의 **통합 런타임**으로 전환합니다. 

   ![연결 탭](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
2. **통합 런타임** 탭에서 **+ 새로 만들기**를 클릭합니다. 

   ![새 통합 런타임 - 단추](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
3. **Integration Runtime 설정** 창에서 **데이터 이동을 수행하고 활동을 외부 계산으로 디스패치합니다**를 선택하고, **다음**을 클릭합니다. 

   ![통합 런타임 형식 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
4. **개인 네트워크**를 선택하고 **다음**을 클릭합니다. 

   ![사설망 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
5. **이름**에 대해 **MySelfHostedIR**을 입력하고 **다음**을 클릭합니다. 

   ![자체 호스팅 IR 이름](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
10. **옵션 1: 빠른 설치** 섹션에서 **Click here to launch the express setup for this computer**(이 컴퓨터에 대한 빠른 설치를 시작하려면 여기를 클릭하세요.)를 클릭합니다. 

   ![빠른 설치 링크 클릭](./media/tutorial-incremental-copy-multiple-tables-portal/click-exress-setup.png)
11. **Integration Runtime(자체 호스팅) 빠른 설치** 창에서 **닫기**를 클릭합니다. 

   ![Integration Runtime 설정 - 성공](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
12. 웹 브라우저의 **Integration Runtime 설정** 창에서 **마침**을 클릭합니다. 

   ![통합 런타임 설정 - 완료](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
17. 통합 런타임 목록에 **MySelfHostedIR**이 표시되는지 확인합니다.

       ![통합 런타임 - 목록](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 온-프레미스 SQL Server 데이터베이스와 SQL 데이터베이스에 연결된 서비스를 만듭니다. 

### <a name="create-the-sql-server-linked-service"></a>SQL Server 연결된 서비스 만들기
이 단계에서 온-프레미스 SQL Server 데이터베이스를 데이터 팩터리에 연결합니다.

1. **연결** 창의 **통합 런타임** 탭에서 **연결된 서비스** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다.

    ![새 연결된 서비스 단추](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. **새 연결된 서비스** 창에서 **SQL Server**를 선택하고 **계속**을 클릭합니다. 

    ![SQL Server 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **SqlServerLinkedService**를 입력합니다. 
    2. **통합 런타임을 통해 연결**에 대해 **MySelfHostedIR**을 선택합니다. 이는 **중요한** 단계입니다. 기본 통합 런타임은 온-프레미스 데이터 저장소에 연결할 수 없습니다. 앞에서 만든 자체 호스팅 통합 런타임을 사용합니다. 
    3. **서버 이름**의 경우 SQL Server 데이터베이스가 있는 컴퓨터의 이름을 입력합니다.
    4. **데이터베이스 이름**의 경우 원본 데이터가 있는 SQL Server에 데이터베이스의 이름을 입력합니다. 테이블을 만들었고 필수 구성 요소의 일부로 이 데이터베이스에 데이터를 삽입했습니다. 
    5. **인증 유형**의 경우 데이터베이스에 연결하는 데 사용하려는 **인증 유형**을 선택합니다. 
    6. **사용자 이름**의 경우 SQL Server 데이터베이스에 대한 액세스가 있는 사용자의 이름을 입력합니다. 슬래시 문자(`\`)를 사용자 계정 또는 서버 이름에 사용해야 할 경우 이스케이프 문자(`\`)를 사용합니다. 예는 `mydomain\\myuser`입니다.
    7. **암호**의 경우 사용자에 대한 **암호**를 입력합니다. 
    8. 데이터 팩터리가 SQL Server 데이터베이스에 연결할 수 있는지를 테스트하려면 **연결 테스트**를 클릭합니다. 연결이 성공할 때까지 모든 오류를 수정합니다. 
    9. 연결된 서비스를 저장하려면 **저장**을 클릭합니다.

        ![SQL Server 연결된 서비스 - 설정](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
마지막 단계에서는 SQL Server 데이터베이스를 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 이 단계에서는 대상/싱크 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. 

1. **연결** 창의 **통합 런타임** 탭에서 **연결된 서비스** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다.

    ![새 연결된 서비스 단추](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
2. **새 연결된 서비스** 창에서 **Azure SQL Database**를 선택하고 **계속**을 클릭합니다. 
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **AzureSqlDatabaseLinkedService**를 입력합니다. 
    3. **서버 이름**의 경우 드롭다운 목록에서 Azure SQL 서버의 이름을 선택합니다. 
    4. **데이터베이스 이름**의 경우 필수 구성 요소의 일부로 customer_table 및 project_table을 만든 Azure SQL 데이터베이스를 선택합니다. 
    6. **사용자 이름**의 경우 Azure SQL 데이터베이스에 대한 액세스가 있는 사용자의 이름을 입력합니다. 
    7. **암호**의 경우 사용자에 대한 **암호**를 입력합니다. 
    8. 데이터 팩터리가 SQL Server 데이터베이스에 연결할 수 있는지를 테스트하려면 **연결 테스트**를 클릭합니다. 연결이 성공할 때까지 모든 오류를 수정합니다. 
    9. 연결된 서비스를 저장하려면 **저장**을 클릭합니다.

        ![Azure SQL 연결된 서비스 - 설정](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
10. 두 개의 연결된 서비스가 목록에 표시되는지 확인합니다. 
   
    ![두 개의 연결된 서비스](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>데이터 집합 만들기
이 단계에서는 데이터 원본, 데이터 대상 및 워터마크를 저장할 위치를 나타내는 데이터 집합을 만듭니다.

### <a name="create-a-source-dataset"></a>원본 데이터 집합 만들기

1. 왼쪽 창에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다.

   ![새 데이터 집합 메뉴](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. **새 데이터 집합** 창에서 **SQL Server**를 선택하고 **마침**을 클릭합니다. 

   ![SQL Server 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
3. 웹 브라우저에 데이터 집합을 구성하기 위해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. 아래쪽 속성 창의 **일반** 탭에서 **이름**에 대해 **SourceDataset**를 입력합니다. 

   ![원본 데이터 집합 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
4. 속성 창에서 **연결** 탭으로 전환하고, **연결된 서비스**에 대해 **SqlServerLinkedService**를 선택합니다. 여기에서 테이블을 선택하지 마십시오. 파이프 라인의 복사 작업은 전체 테이블을 로드하는 대신 SQL 쿼리를 사용하여 데이터를 로드합니다.

   ![원본 데이터 집합 - 연결](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>싱크 데이터 집합 만들기
1. 왼쪽 창에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다.

   ![새 데이터 집합 메뉴](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 

   ![Azure SQL Database 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. 웹 브라우저에 데이터 집합을 구성하기 위해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. 아래쪽 속성 창의 **일반** 탭에서 **이름**에 대해 **SinkDataset**를 입력합니다.

   ![싱크 데이터 집합 - 일반](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
4. 속성 창에서 **연결** 탭으로 전환하고, **연결된 서비스**에 대해 **AzureSqlLinkedService**를 선택합니다. 

   ![싱크 데이터 집합 - 연결](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
5. 속성 창에서 **매개 변수** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **Create/update 매개 변수** 섹션에서 **새로 만들기**를 클릭합니다. 
    2. **이름**에 대해 **SinkTableName**을 입력하고 **형식**에 대해 **문자열**을 입력합니다. 이 데이터 집합은 매개 변수로 **SinkTableName**을 사용합니다. SinkTableName 매개 변수는 런타임에 동적으로 파이프라인에 의해 설정됩니다. 파이프라인의 ForEach 작업은 테이블 이름 목록을 반복하고 반복할 때마다 테이블 이름을 이 데이터 집합에 전달합니다.
    3. **Parameterizable 속성** 섹션에서 **tableName** 속성에 대해 `@{dataset().SinkTableName}`을 입력합니다. **SinkTableName** 매개 변수에 전달된 값을 사용하여 데이터 집합의 **tableName** 속성을 초기화합니다. 

       ![싱크 데이터 집합 - 속성](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)

### <a name="create-a-dataset-for-a-watermark"></a>워터마크에 대한 데이터 집합 만들기
이 단계에서는 상위 워터마크 값을 저장하기 위한 데이터 집합을 만듭니다. 

1. 왼쪽 창에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다.

   ![새 데이터 집합 메뉴](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
2. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 

   ![Azure SQL Database 선택](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
3. 아래쪽 속성 창의 **일반** 탭에서 **이름**에 대해 **WatermarkDataset**를 입력합니다.
4. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다.
    2. **테이블**에 대해 **[dbo].[watermarktable]**을 선택합니다.

       ![워터마크 데이터 집합 - 연결](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
파이프라인에서는 테이블 이름 목록을 매개 변수로 사용합니다. ForEach 작업은 테이블 이름 목록을 반복하고 다음 작업을 수행합니다. 

1. 조회 작업을 사용하여 이전 워터마크 값을 검색합니다(초기 값 또는 마지막 반복에서 사용된 값).

2. 조회 작업을 사용하여 새로운 워터마크 값을 검색합니다(원본 테이블의 워터마크 열의 최대값).

3. 복사 작업을 사용하여 원본 데이터베이스에서 대상 데이터베이스로 2개의 워터마크 값 사이에 데이터를 복사합니다.

4. StoredProcedure 작업을 사용하여 다음 반복의 첫 번째 단계에 사용할 이전 워터마크 값을 업데이트합니다. 

### <a name="create-the-pipeline"></a>파이프라인 만들기

1. 왼쪽 창에서 **+(더하기)**, **파이프라인**을 차례로 클릭합니다.

    ![새 파이프라인 - 메뉴](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
2. **속성** 창의 **일반** 탭에서 **이름**에 대해 **IncrementalCopyPipeline**을 입력합니다. 

    ![파이프라인 이름](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
3. **속성** 창에서 다음 단계를 수행합니다. 

    1. **+새로 만들기**를 클릭합니다. 
    2. 매개 변수 **이름**에 대해 **tableList**를 입력합니다. 
    3. 매개 변수 **형식**에 대해 **Object**를 선택합니다.

    ![파이프라인 매개 변수](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
4. **활동** 도구 상자에서 **반복 및 조건부**를 펼치고, **ForEach** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. **속성** 창의 **일반** 탭에서 **IterateSQLTables**를 입력합니다. 

    ![ForEach 활동 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
5. **속성** 창에서 **설정** 탭으로 전환하고 **항목**에 대해 `@pipeline().parameters.tableList`를 입력합니다. ForEach 작업은 테이블 목록을 반복하고 증분 복사 작업을 수행합니다. 

    ![ForEach 활동 - 설정](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
6. 아직 선택되지 않은 경우 파이프라인에서 **ForEach** 활동을 선택합니다. **편집(연필 아이콘)** 단추를 클릭합니다.

    ![ForEach 활동 - 편집](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
7. **활동** 도구 상자에서 **일반**을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, **Name**에 대해 **LookupOldWaterMarkActivity**를 입력합니다.

    ![첫 번째 조회 활동 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
8. **속성** 창에서 **설정** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **원본 데이터 집합**에 대해 **WatermarkDataset**을 선택합니다.
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![첫 번째 조회 활동 - 설정](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
9. **활동** 도구 상자에서 **조회** 활동을 끌어서 놓고 **이름**에 대해 **LookupNewWaterMarkActivity**를 입력합니다.
        
    ![두 번째 조회 활동 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
10. **설정** 탭으로 전환합니다.

    1. **원본 데이터 집합**에 대해 **SourceDataset**를 선택합니다. 
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다.
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![두 번째 조회 활동 - 설정](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
11. **활동** 도구 상자에서 **복사** 활동을 끌어서 놓고 **이름**에 대해 **IncrementalCopyActivity**를 입력합니다. 

    ![복사 활동 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
12. **조회** 활동을 하나씩 **복사** 활동에 연결합니다. 연결하려면 **조회** 활동에 연결된 **녹색** 상자를 끌어서 **복사** 활동에 놓습니다. 복사 활동의 테두리 색이 **파란색**으로 변경되면 마우스 단추를 놓습니다.

    ![조회 활동 및 복사 활동 연결](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
13. 파이프라인에서 **복사** 활동을 선택합니다. **속성** 창의 **원본** 탭으로 전환합니다. 

    1. **원본 데이터 집합**에 대해 **SourceDataset**를 선택합니다. 
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![복사 활동 - 원본 설정](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
14. **싱크** 탭으로 전환하고, **싱크 데이터 집합**에 대해 **SinkDataset**을 선택합니다. 
        
    ![복사 활동 - 싱크 설정](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
15. **매개 변수** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **싱크 저장 프로시저 이름** 속성에 `@{item().StoredProcedureNameForMergeOperation}`을 입력합니다.
    2. **싱크 테이블 형식** 속성에 `@{item().TableType}`을 입력합니다.
    3. **싱크 데이터 집합** 섹션에서 **SinkTableName** 매개 변수에 `@{item().TABLE_NAME}`을 입력합니다.

        ![복사 활동 - 매개 변수](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
16. **저장 프로시저** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. **복사** 활동을 **저장 프로시저** 활동에 연결합니다. 

    ![복사 활동 - 매개 변수](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
17. 파이프라인에서 **저장 프로시저** 활동을 선택하고 **속성** 창의 **일반** 탭에서 **이름**에 대해 **StoredProceduretoWriteWatermarkActivity**를 입력합니다. 

    ![저장 프로시저 활동 - 이름](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
18. **SQL 계정** 탭으로 전환하고, **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다.

    ![저장 프로시저 활동 - SQL 계정](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
19. **저장 프로시저** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **저장 프로시저 이름**에 `sp_write_watermark`를 입력합니다. 
    2. **가져오기 매개 변수**를 선택합니다. 
    3. 매개 변수에 대해 다음 값을 지정합니다. 

        | Name | 형식 | 값 | 
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | 문자열 | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![저장 프로시저 활동 - 저장 프로시저 설정](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
20. 왼쪽 창에서 **게시**를 클릭합니다. 이 작업은 데이터 팩터리 서비스에 대해 만든 엔터티를 게시합니다. 

    ![게시 단추](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
21. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림을 보려면 **알림 표시** 링크를 클릭합니다. **X**를 클릭하여 알림 창을 닫습니다.

    ![알림 표시](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>파이프라인 실행

1. 파이프라인에 대한 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다.     

    ![지금 트리거](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
2. **파이프라인 실행** 창에서 **tableList** 매개 변수에 대해 다음 값을 입력하고 **마침**을 클릭합니다. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

    ![파이프라인 실행 인수](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **수동 트리거**로 트리거된 파이프라인 실행이 표시됩니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다. **작업** 열의 링크를 사용하면 파이프라인 실행과 연결된 활동 실행을 보고 파이프라인을 다시 실행할 수 있습니다. 

    ![파이프라인 실행](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 선택한 파이프라인 실행과 연결된 활동 실행이 표시됩니다. 

    ![작업 실행](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>결과 검토
SQL Server Management Studio에서 대상 SQL 데이터베이스에 대해 다음 쿼리를 실행하여 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다. 

## <a name="add-more-data-to-the-source-tables"></a>원본 테이블에 데이터 추가

원본 SQL Server 데이터베이스에 대해 다음 쿼리를 실행하여 customer_table의 기존 행을 업데이트합니다. project_table에 새 행을 삽입합니다. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>파이프라인 다시 실행
1. 웹 브라우저 창에서 왼쪽의 **편집** 탭으로 전환합니다. 
2. 파이프라인에 대한 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다.   

    ![지금 트리거](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. **파이프라인 실행** 창에서 **tableList** 매개 변수에 대해 다음 값을 입력하고 **마침**을 클릭합니다. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **수동 트리거**로 트리거된 파이프라인 실행이 표시됩니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다. **작업** 열의 링크를 사용하면 파이프라인 실행과 연결된 활동 실행을 보고 파이프라인을 다시 실행할 수 있습니다. 

    ![파이프라인 실행](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
2. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 선택한 파이프라인 실행과 연결된 활동 실행이 표시됩니다. 

    ![작업 실행](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>최종 결과 검토
SQL Server Management Studio에서 대상 데이터베이스에 대해 다음 쿼리를 실행하여 업데이트된/새로운 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

3번 **PersonID**에 대한 **Name** 및 **LastModifytime**의 새 값을 확인합니다. 

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

**NewProject** 항목이 project_table에 추가되었습니다. 

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다.
     
## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 IR(통합 런타임)을 만듭니다.
> * Integration Runtime을 설치합니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 집합을 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[변경 내용 추적 기술을 사용하여 Azure SQL Database에서 Azure Blob 저장소로 데이터 증분 로드](tutorial-incremental-copy-change-tracking-feature-portal.md)


