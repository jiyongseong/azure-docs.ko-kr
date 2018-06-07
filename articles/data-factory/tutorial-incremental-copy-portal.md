---
title: Azure Data Factory를 사용하여 테이블 증분 복사 | Microsoft Docs
description: 이 자습서에서는 Azure SQL 데이터베이스에서 Azure Blob 저장소로 데이터 증분을 복사하는 Azure 데이터 팩터리 파이프라인을 만듭니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 75d291c10f732f2d18fb78b0262c42052acc713e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Azure SQL 데이터베이스에서 Azure Blob 저장소로 데이터 증분 로드
이 자습서에서는 Azure SQL 데이터베이스의 테이블에서 Azure Blob 저장소로 델타 데이터를 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다. 


> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. 일반 공급되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.


이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 워터마크 값을 저장할 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 집합을 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다. 
> * 결과 검토
> * 원본에 더 많은 데이터 추가
> * 파이프라인 다시 실행
> * 두 번째 파이프라인 실행 모니터링
> * 두 번째 실행 결과 검토


## <a name="overview"></a>개요
대략적인 솔루션 다이어그램은 다음과 같습니다. 

![데이터 증분 로드](media\tutorial-Incremental-copy-portal\incrementally-load.png)

이 솔루션을 만드는 중요한 단계는 다음과 같습니다. 

1. **워터마크 열을 선택합니다**.
    원본 데이터 저장소에서 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 분할하는 데 사용할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.

2. **워터마크 값을 저장할 데이터 저장소를 준비합니다**. 이 자습서에서는 SQL 데이터베이스에 워터마크 값을 저장합니다.
    
3. **다음 워크플로를 사용하여 파이프라인을 만듭니다.** 
    
    이 솔루션의 파이프라인에 포함되는 작업은 다음과 같습니다.
  
    * 두 가지 조회 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다. 
    * 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 복사 작업을 만듭니다. 그런 다음 원본 데이터 저장소의 델타 데이터를 새 파일로 Blob 저장소에 복사합니다. 
    * 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 StoredProcedure 작업을 만듭니다. 


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure SQL Database**. 데이터베이스를 원본 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 만드는 단계를 [Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)에서 참조하세요.
* **Azure Storage**. Blob 저장소를 싱크 데이터 저장소로 사용합니다. 저장소 계정이 없는 경우, 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요. adftutorial이라는 컨테이너를 만듭니다. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL 데이터베이스에 데이터 원본 테이블 만들기
1. SQL Server Management Studio를 엽니다. **서버 탐색기**에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

2. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `data_source_table`이라는 테이블을 만듭니다. 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    이 자습서에서는 LastModifytime을 워터마크 열로 사용합니다. 데이터 원본 저장소의 데이터는 다음 테이블에 표시됩니다.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>상위 워터마크 값을 저장하기 위해 SQL 데이터베이스에 다른 테이블 만들기
1. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장할 `watermarktable` 테이블을 만듭니다.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. 원본 데이터 저장소의 테이블 이름으로 상위 워터마크의 기본 값을 설정합니다. 이 자습서에서 테이블 이름은 data_source_table입니다.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` 테이블의 데이터를 검토합니다.
    
    ```sql
    Select * from watermarktable
    ```
    출력 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL 데이터베이스에 저장 프로시저 만들기 

다음 명령을 실행하여 SQL 데이터베이스에 저장 프로시저를 만듭니다.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFIncCopyTutorialDF**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류와 함께 빨간색 느낌표가 표시되면, 데이터 팩터리 이름(예: yournameADFIncCopyTutorialDF)을 변경하고 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
        리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)** 를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.      
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 자습서에서는 하나의 파이프라인에 두 개의 조회 작업, 하나의 복사 작업 및 하나의 StoredProcedure 작업이 연결되어 있는 파이프라인을 만듭니다. 

1. Data Factory UI의 **시작** 페이지에서 **파이프라인 만들기** 타일을 클릭합니다. 

   ![Data Factory UI의 시작 페이지 가져오기](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. 파이프라인에 대한 **속성** 창의 **일반** 페이지에서 **IncrementalCopyPipeline** 이름을 입력합니다. 

   ![파이프라인 이름](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. 이전 워터마크 값을 가져오는 첫 번째 조회 활동을 추가하겠습니다. **활동** 도구 상자에서 **일반**을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **LookupOldWaterMarkActivity**로 변경합니다.

   ![첫 번째 조회 활동 - 이름](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. **설정** 탭으로 전환하고, **원본 데이터 집합**에 대해 **+ 새로 만들기**를 클릭합니다. 이 단계에서는 **watermarktable**에 데이터를 나타내기 위한 데이터 집합을 만듭니다. 이 테이블에는 이전 복사 작업에서 사용된 이전 워터마크가 포함되어 있습니다. 

   ![새 데이터 집합 메뉴 - 이전 워터마크](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 데이터 집합에 대해 열린 새 탭이 표시됩니다. 

   ![Azure SQL Database 선택](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. 데이터 집합에 대한 속성 창에서 **이름**에 대해 **WatermarkDataset**를 입력합니다.

   ![워터마크 데이터 집합 - 이름](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. **연결** 탭으로 전환하고, **+ 새로 만들기**를 클릭하여 Azure SQL Database에 연결합니다(연결된 서비스 만들기). 

   ![새 연결된 서비스 단추](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름**에 대해 **AzureSqlDatabaseLinkedService**를 입력합니다. 
    2. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    3. Azure SQL Server에 액세스할 **사용자의 이름**을 입력합니다. 
    4. 사용자에 대한 **암호**를 입력합니다. 
    5. Azure SQL Database에 대한 연결을 테스트하려면 **연결 테스트**를 클릭합니다.
    6. **저장**을 클릭합니다.
    7. **연결** 탭에서 **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**가 선택되어 있는지 확인합니다.
       
        ![새 연결된 서비스 창](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. **테이블**에 대해 **[dbo].[watermarktable]** 을 선택합니다. 테이블의 데이터를 미리 보려면 **데이터 미리 보기**를 클릭합니다.

    ![워터마크 데이터 집합 - 연결 설정](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. 위쪽의 파이프라인 탭을 클릭하거나 왼쪽의 트리 뷰에서 파이프라인 이름을 클릭하여 파이프라인 편집기로 전환합니다. **조회** 활동에 대한 속성 창에서 **원본 데이터 집합** 필드에 대해 **WatermarkDataset**가 선택되어 있는지 확인합니다. 

    ![파이프라인 - 이전 워터마크 데이터 집합](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. **활동** 도구 상자에서 **일반**을 펼치고, 다른 **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, 속성 창의 **일반** 탭에서 이름을 **LookupNewWaterMarkActivity**로 설정합니다. 이 조회 활동은 대상에 복사될 원본 데이터가 있는 테이블에서 새 워터마크 값을 가져옵니다. 

    ![두 번째 조회 활동 - 이름](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. 두 번째 **조회** 활동에 대한 속성 창에서 **설정** 탭으로 전환하고, **새로 만들기**를 클릭합니다. 새 워터마크 값(LastModifyTime의 최대값)이 포함된 원본 테이블을 가리키도록 데이터 집합을 만듭니다. 

    ![두 번째 조회 활동 - 새 데이터 집합](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. 이 데이터 집합에 대해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 집합이 표시됩니다. 
15. 속성 창의 **일반** 탭에서 **이름**에 대해 **SourceDataset**를 입력합니다. 

    ![원본 데이터 집합 - 이름](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다.
    2. [테이블]에 대해 **[dbo].[data_source_table]** 을 선택합니다. 자습서의 뒷부분에서 이 데이터 집합에 대한 쿼리를 지정합니다. 쿼리는 이 단계에서 지정한 테이블보다 높은 우선 순위를 갖습니다. 

        ![두 번째 조회 활동 - 새 데이터 집합](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. 위쪽의 파이프라인 탭을 클릭하거나 왼쪽의 트리 뷰에서 파이프라인 이름을 클릭하여 파이프라인 편집기로 전환합니다. **조회** 활동에 대한 속성 창에서 **원본 데이터 집합** 필드에 대해 **SourceDataset**가 선택되어 있는지 확인합니다. 
18. **쿼리 사용** 필드에 대해 **쿼리**를 선택하고, **data_source_table**에서 **LastModifytime**의 최대값만 선택하는 다음 쿼리를 입력합니다. 이 쿼리가 없는 경우 데이터 집합은 데이터 집합 정의에서 테이블 이름(data_source_table)을 지정한 대로 테이블의 모든 행을 가져옵니다.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![두 번째 조회 활동 - 쿼리](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, [활동] 도구 상자에서 **복사** 활동을 끌어서 놓고, 이름을 **IncrementalCopyActivity**로 설정합니다. 

    ![복사 활동 - 이름](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. 조회 활동에 붙어 있는 **녹색 단추**를 복사 활동으로 끌어 **조회 활동을 복사 활동에 연결합니다**. 복사 활동의 테두리 색이 파란색으로 변경되면 마우스 단추를 놓습니다. 

    ![조회 활동 및 복사 활동 연결](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. **복사 활동**을 선택하고 **속성** 창에서 활동에 대한 속성을 확인합니다. 

    ![복사 작업 속성](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. **속성** 창에서 **원본**으로 전환하고 다음 단계를 수행합니다.

    1. **원본 데이터 집합** 필드에 대해 **SourceDataset**를 선택합니다. 
    2. **쿼리 사용** 필드에 대해 **쿼리**를 선택합니다. 
    3. **쿼리** 필드에 대해 다음 SQL 쿼리를 입력합니다. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![복사 활동 - 원본](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. **싱크** 탭으로 전환하고, **싱크 데이터 집합** 필드에 대해 **+ 새로 만들기**를 클릭합니다. 

    ![새 데이터 집합 단추](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. 이 자습서에서 싱크 데이터 저장소는 Azure Blob Storage 유형입니다. 따라서 **Azure Blob Storage**를 선택하고, **새 데이터 집합** 창에서 **마침**을 클릭합니다. 

    ![Azure Blob Storage 선택](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. 데이터 집합에 대한 [속성] 창의 **일반** 탭에서 **이름**에 대해 **SinkDataset**를 입력합니다. 

    ![싱크 데이터 집합 - 이름](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. **연결** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다. 이 단계에서는 **Azure Blob 저장소**에 대한 연결(연결된 서비스)을 만듭니다.

    ![싱크 데이터 집합 - 새 연결](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다. 
    2. **저장소 계정 이름**에 대해 Azure Storage 계정을 선택합니다.
    3. **저장**을 클릭합니다. 

        ![Azure Storage 연결된 서비스 - 설정](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. **연결**에서 다음 단계를 수행합니다.

    1. **연결된 서비스**에 대해 **AzureStorageLinkedService**가 선택되어 있는지 확인합니다. 
    2. **파일 경로** 필드의 **폴더** 부분에 대해 **adftutorial/incrementalcopy**를 입력합니다. **adftutorial**은 Blob 컨테이너 이름이고, **incrementalcopy**는 폴더 이름입니다. 이 코드 조각에서는 Blob 저장소에 adftutorial이라는 Blob 컨테이너가 있다고 가정합니다. 컨테이너가 없으면 만들거나 기존 컨테이너의 이름으로 설정합니다. **incrementalcopy** 출력 폴더가 없으면 Azure Data Factory에서 자동으로 만듭니다. 또한 **파일 경로**에 대한 **찾아보기** 단추를 사용하여 Blob 컨테이너의 폴더로 이동할 수도 있습니다. .RunId, '.txt')`.
    3. **파일 경로** 필드의 **파일 이름** 부분에 대해 `@CONCAT('Incremental-', pipeline().RunId, '.txt')`를 입력합니다. 파일 이름은 식을 사용하여 동적으로 생성됩니다. 각 파이프라인 실행에는 고유한 ID가 있습니다. 복사 활동은 실행 ID를 사용하여 파일 이름을 생성합니다. 

        ![싱크 데이터 집합 - 연결 설정](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. 위쪽의 파이프라인 탭을 클릭하거나 왼쪽의 트리 뷰에서 파이프라인 이름을 클릭하여 **파이프라인** 편집기로 전환합니다. 
29. **활동** 도구 상자에서 **일반**을 펼치고, **저장 프로시저** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. **복사** 활동의 녹색(성공) 출력을 **저장 프로시저** 활동에 **연결**합니다. 
    
    ![복사 활동 - 원본](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. 파이프라인 디자이너에서 **저장 프로시저 활동**을 선택하고, 이름을 **StoredProceduretoWriteWatermarkActivity**로 변경합니다. 

    ![저장 프로시저 활동 - 이름](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. **SQL 계정** 탭으로 전환하고, **연결된 서비스**에 대해 *AzureSqlDatabaseLinkedService**를 선택합니다. 

    ![저장 프로시저 활동 - SQL 계정](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. **저장 프로시저** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **저장 프로시저 이름**에 대해 **sp_write_watermark**를 선택합니다. 
    2. 저장 프로시저 매개 변수에 대한 값을 지정하려면, **가져오기 매개 변수**를 클릭하고 매개 변수에 대해 다음 값을 입력합니다. 

        | Name | type | 값 | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | 문자열 | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![저장 프로시저 활동 - 저장 프로시저 설정](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. 파이프라인 설정에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **파이프라인 유효성 검사 보고서** 창을 닫으려면 >>를 클릭합니다.   

    ![파이프라인 유효성 검사](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. **모두 게시** 단추를 선택하여 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 Azure Data Factory 서비스에 게시합니다. 게시 성공 메시지가 표시될 때까지 기다립니다. 

    ![게시 단추](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거
1. 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

    ![지금 트리거 단추](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 왼쪽의 **모니터** 탭으로 전환합니다. 수동 트리거로 트리거된 파이프라인 실행 상태를 볼 수 있습니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다. 
    
    ![파이프라인 실행](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. 이 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 첫 번째 링크(**활동 실행 보기**)를 클릭합니다. 위쪽의 **파이프라인**을 클릭하여 이전 보기로 돌아갈 수 있습니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다.

    ![작업 실행](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>결과 검토
1. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Azure 저장소 계정에 연결합니다. 출력 파일이 **adftutorial** 컨테이너의 **incrementalcopy** 폴더에 만들어졌는지 확인합니다.

    ![첫 번째 출력 파일](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. 출력 파일을 열고 모든 데이터가 **data_source_table**에서 Blob 파일로 복사되었는지 확인합니다. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. `watermarktable`에서 최신 값을 확인합니다. 워터마크 값이 업데이트된 것을 볼 수 있습니다.

    ```sql
    Select * from watermarktable
    ```
    
    출력은 다음과 같습니다.
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>원본에 더 많은 데이터 추가

SQL 데이터베이스(데이터 원본 저장소)에 새 데이터를 삽입합니다.

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

SQL 데이터베이스에서 업데이트된 데이터는 다음과 같습니다.

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>다른 파이프라인 실행 트리거
1. **편집** 탭으로 전환합니다. 디자이너에서 열려 있지 않은 경우 트리 뷰에서 파이프라인을 클릭합니다. 

    ![지금 트리거 단추](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

    ![지금 트리거 단추](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>두 번째 파이프라인 실행 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 수동 트리거로 트리거된 파이프라인 실행 상태를 볼 수 있습니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다. 
    
    ![파이프라인 실행](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. 이 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 첫 번째 링크(**활동 실행 보기**)를 클릭합니다. 위쪽의 **파이프라인**을 클릭하여 이전 보기로 돌아갈 수 있습니다. **새로 고침** 단추를 클릭하여 목록을 새로 고칩니다.

    ![작업 실행](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>두 번째 출력 확인

1. Blob 저장소에 다른 파일이 만들어진 것을 볼 수 있습니다. 이 자습서에서 새 파일 이름은 `Incremental-<GUID>.txt`입니다. 해당 파일을 열면 2개 행의 레코드가 표시됩니다.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. `watermarktable`에서 최신 값을 확인합니다. 워터마크 값이 다시 업데이트된 것을 볼 수 있습니다.

    ```sql
    Select * from watermarktable
    ```
    샘플 출력은 다음과 같습니다. 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 워터마크 값을 저장할 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 집합을 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다. 
> * 결과 검토
> * 원본에 더 많은 데이터 추가
> * 파이프라인 다시 실행
> * 두 번째 파이프라인 실행 모니터링
> * 두 번째 실행 결과 검토

이 자습서에서는 파이프라인이 SQL 데이터베이스의 단일 테이블에서 Blob 저장소로 데이터를 복사했습니다. 온-프레미스 SQL Server 데이터베이스의 여러 테이블에서 SQL 데이터베이스로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 이동하세요. 

> [!div class="nextstepaction"]
>[SQL Server의 여러 테이블에서 Azure SQL Database로 데이터 증분 로드](tutorial-incremental-copy-multiple-tables-portal.md)



