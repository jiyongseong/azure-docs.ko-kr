---
title: Azure Data Factory를 사용하여 데이터 대량 복사 | Microsoft Docs
description: Azure Data Factory 및 복사 작업을 사용하여 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 대량으로 복사하는 방법을 알아봅니다.
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 440b07b494b34db7ff3fcdf5d5ac830b165c339d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 여러 테이블 대량 복사
이 자습서에서는 **Azure SQL Database에서 Azure SQL Data Warehouse로 여러 테이블을 복사**하는 방법을 보여 줍니다. 다른 복사 시나리오에도 동일한 패턴을 적용할 수 있습니다. 예를 들어 SQL Server/Oracle에서 Azure SQL Database/Data Warehouse/Azure Blob으로 테이블을 복사하고, Blob에서 Azure SQL Database 테이블로 다른 경로를 복사합니다.

> [!NOTE]
> - Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.
> - 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

이 자습서에서 수행하는 단계는 대략적으로 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure SQL Database, Azure SQL Data Warehouse 및 Azure Storage 연결된 서비스를 만듭니다.
> * Azure SQL Database 및 Azure SQL Data Warehouse 데이터 집합을 만듭니다.
> * 복사할 테이블을 조회하는 파이프라인을 만들고, 실제 복사 작업을 수행하는 다른 파이프라인을 만듭니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

이 자습서에서는 Azure Portal을 사용합니다. 다른 도구/SDK를 사용하여 데이터 팩터리를 만드는 방법을 알아보려면 [빠른 시작](quickstart-create-data-factory-dot-net.md)을 참조하세요. 

## <a name="end-to-end-workflow"></a>종단 간 워크플로
이 시나리오에는 SQL Data Warehouse에 복사하려는 여러 개의 Azure SQL Database 테이블이 있습니다. 다음은 파이프라인에서 발생하는 워크플로 단계의 논리적 시퀀스입니다.

![워크플로](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* 첫 번째 파이프라인은 싱크 데이터 저장소로 복사해야 하는 테이블의 목록을 찾습니다.  또는 싱크 데이터 저장소에 복사할 모든 테이블을 나열하는 메타데이터 테이블을 유지할 수 있습니다. 그런 다음 파이프라인에서 다른 파이프라인을 트리거하여 데이터베이스의 각 테이블을 반복하고 데이터 복사 작업을 수행합니다.
* 두 번째 파이프라인은 실제 복사를 수행하며, 테이블 목록을 매개 변수로 사용합니다. 최상의 성능을 위해 목록의 각 테이블에 대해 [Blob 저장소 및 PolyBase를 통해 스테이징되는 복사](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)를 사용하여 Azure SQL Database의 특정 테이블을 SQL Data Warehouse의 해당 테이블에 복사합니다. 이 예제에서 첫 번째 파이프라인은 테이블의 목록을 매개 변수의 값으로 전달합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건
* **Azure Storage 계정**. Azure Storage 계정은 대량 복사 작업에서 스테이징 Blob 저장소로 사용됩니다. 
* **Azure SQL Database**. 이 데이터베이스에는 원본 데이터가 포함되어 있습니다. 
* **Azure SQL Data Warehouse** 이 데이터 웨어하우스에는 SQL Database에서 복사된 데이터를 보관하고 있습니다. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Database 및 SQL Data Warehouse 준비

**원본 Azure SQL Database 준비**

[Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서에 따라 Adventure Works LT 샘플 데이터가 포함된 Azure SQL Database를 만듭니다. 이 자습서에서는 이 샘플 데이터베이스의 모든 테이블을 SQL 데이터 웨어하우스로 복사합니다.

**싱크 Azure SQL Data Warehouse 준비**

1. 아직 없는 경우 Azure SQL Data Warehouse를 만드는 단계는 [SQL Data Warehouse 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) 문서를 참조하세요.

2. SQL Data Warehouse에 해당하는 테이블 스키마를 만듭니다. [마이그레이션 유틸리티](https://www.microsoft.com/download/details.aspx?id=49100)를 사용하여 Azure SQL Database에서 Azure SQL Data Warehouse로 **스키마를 마이그레이션할 수 있습니다**. Azure Data Factory를 사용하여 이후 단계에서 데이터를 마이그레이션/복사합니다.

## <a name="azure-services-to-access-sql-server"></a>SQL 서버에 액세스하는 Azure 서비스

SQL Database와 SQL Data Warehouse 모두에서 Azure 서비스를 통해 SQL 서버에 액세스할 수 있도록 허용합니다. Azure SQL 서버에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **켜기**로 지정되어 있는지 확인합니다. 이 설정을 사용하면 Data Factory 서비스에서 Azure SQL Database의 데이터를 읽고 Azure SQL Data Warehouse에 데이터를 쓸 수 있습니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

1. 왼쪽의 **더 많은 서비스** 허브를 클릭하고 **SQL 서버**를 클릭합니다.
2. 서버를 선택하고 **설정** 아래의 **방화벽**을 클릭합니다.
3. **방화벽 설정** 페이지에서 **Azure 서비스에 대한 액세스 허용**에 대해 **켜기**를 클릭합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialBulkCopyDF**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialBulkCopyDF). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory V2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Data Factory UI 응용 프로그램을 시작합니다.
11. **시작** 페이지에서 다음 이미지와 같이 왼쪽 패널의 **편집** 탭으로 전환합니다.  

    ![시작 페이지](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기
연결된 서비스를 만들어 데이터 저장소와 계산을 데이터 팩터리에 연결합니다. 연결된 서비스에는 런타임에 Data Factory 서비스에서 데이터 저장소에 연결하는 데 사용하는 연결 정보가 있습니다. 

이 자습서에서는 Azure SQL Database, Azure SQL Data Warehouse 및 Azure Blob Storage 데이터 저장소를 데이터 팩터리에 연결합니다. Azure SQL Database는 원본 데이터 저장소입니다. Azure SQL Data Warehouse는 싱크/대상 데이터 저장소입니다. Azure Blob Storage는 PolyBase를 사용하여 데이터를 SQL Data Warehouse에 로드하기 전에 데이터를 준비하는 저장소입니다. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>원본 Azure SQL Database 연결된 서비스 만들기
이 단계에서는 Azure SQL Database를 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 

1. 창의 아래쪽에서 **연결**을 클릭하고, 도구 모음에서 **+ 새로 만들기**를 클릭합니다. 

    ![새 연결된 서비스 단추](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. **새 연결된 서비스** 창에서 **Azure SQL Database**를 선택하고 **계속**을 클릭합니다. 

    ![Azure SQL Database 선택](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureSqlDatabaseLinkedService**를 입력합니다. 
    2. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    3. **데이터베이스 이름**에 대해 Azure SQL Database를 선택합니다. 
    4. Azure SQL Database에 연결할 **사용자의 이름**을 입력합니다. 
    5. 사용자에 대한 **암호**를 입력합니다. 
    6. 지정된 정보를 사용하여 Azure SQL Database에 대한 연결을 테스트하려면 **연결 테스트**를 클릭합니다.
    7. **저장**을 클릭합니다.

        ![Azure SQL Database 설정](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>싱크 Azure SQL Data Warehouse 연결된 서비스 만들기

1. **연결** 탭의 도구 모음에서 **+ 새로 만들기**를 다시 클릭합니다. 
2. **새 연결된 서비스** 창에서 **Azure SQL Data Warehouse**를 선택하고 **계속**을 클릭합니다. 
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureSqlDWLinkedService**를 입력합니다. 
    2. **서버 이름**에 대해 Azure SQL Server를 선택합니다.
    3. **데이터베이스 이름**에 대해 Azure SQL Database를 선택합니다. 
    4. Azure SQL Database에 연결할 **사용자의 이름**을 입력합니다. 
    5. 사용자에 대한 **암호**를 입력합니다. 
    6. 지정된 정보를 사용하여 Azure SQL Database에 대한 연결을 테스트하려면 **연결 테스트**를 클릭합니다.
    7. **저장**을 클릭합니다.

### <a name="create-the-staging-azure-storage-linked-service"></a>스테이징 Azure Storage 연결된 서비스 만들기
이 자습서에서는 더 나은 복사 성능을 위해 Azure Blob 저장소를 중간 스테이징 영역으로 사용하여 PolyBase를 사용할 수 있게 합니다.

1. **연결** 탭의 도구 모음에서 **+ 새로 만들기**를 다시 클릭합니다. 
2. **새 연결된 서비스** 창에서 **Azure Blob Storage**를 선택하고 **계속**을 클릭합니다. 
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다. 
    2. **저장소 계정 이름**에 대해 **Azure Storage 계정**을 선택합니다.
    4. **저장**을 클릭합니다.


## <a name="create-datasets"></a>데이터 집합 만들기
이 자습서에서는 데이터가 저장되는 위치를 지정하는 원본 및 싱크 데이터 집합을 만듭니다. 

AzureSqlDatabaseDataset 입력 데이터 집합은 AzureSqlDatabaseLinkedService를 참조합니다. 연결된 서비스에서 데이터베이스에 연결하기 위한 연결 문자열을 지정합니다. 데이터 집합은 원본 데이터가 포함된 데이터베이스와 테이블의 이름을 지정합니다. 

AzureSqlDWDataset 출력 데이터 집합은 AzureSqlDWLinkedService를 참조합니다. 연결된 서비스에서 데이터 웨어하우스에 연결하기 위한 연결 문자열을 지정합니다. 데이터 집합은 데이터가 복사될 데이터베이스와 테이블을 지정합니다. 

이 자습서에서는 원본 및 대상 SQL 테이블이 데이터 집합 정의에 하드 코드되지 않습니다. 대신 ForEach 활동에서 런타임에 테이블의 이름을 복사 활동으로 전달합니다. 

### <a name="create-a-dataset-for-source-sql-database"></a>원본 SQL Database에 대한 데이터 집합 만들기

1. 왼쪽 창에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다. 

    ![새 데이터 집합 메뉴](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택하고 **마침**을 클릭합니다. **AzureSqlTable1**이라는 새 탭이 표시됩니다. 
    
    ![Azure SQL Database 선택](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. 아래쪽의 속성 창에서 **이름**에 대해 **AzureSqlDatabaseDataset**를 입력합니다.

    ![원본 데이터 집합 이름](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다.
    2. **테이블**에 대해 임의의 테이블을 선택합니다. 이 테이블은 더미 테이블입니다. 파이프라인을 만들 때 원본 데이터 집합에 대한 쿼리를 지정합니다. 이 쿼리는 Azure SQL Database에서 데이터를 추출하는 데 사용됩니다. 또는 **편집** 확인란을 클릭하고 **dummyName**을 테이블 이름으로 입력할 수 있습니다. 

    ![원본 데이터 집합 연결 페이지](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>싱크 SQL Data Warehouse에 대한 데이터 집합 만들기

1. 왼쪽 창에서 **+(더하기)**, **데이터 집합**을 차례로 클릭합니다. 
2. **새 데이터 집합** 창에서 **Azure SQL Data Warehouse**를 선택하고 **마침**을 클릭합니다. **AzureSqlDWTable1**이라는 새 탭이 표시됩니다. 
3. 아래쪽의 속성 창에서 **이름**에 대해 **AzureSqlDWDataset**를 입력합니다.
4. **연결** 탭으로 전환하고, **연결된 서비스**에 대해 **AzureSqlDatabaseLinkedService**를 선택합니다.
5. **매개 변수** 탭으로 전환하고 **+ 새로 만들기**를 클릭합니다.

    ![원본 데이터 집합 연결 페이지](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. 매개 변수 이름으로 **DWTableName**을 입력합니다. 페이지에서 이 이름을 복사/붙여넣는 경우 **DWTableName** 끝에 **후행 공백 문자**가 없는지 확인합니다. 
7. **매개 변수화된 속성** 섹션에서 **tableName** 속성에 대해 `@{dataset().DWTableName}`을 입력합니다. 데이터 집합의 **tableName** 속성은 **DWTableName** 매개 변수에 대한 인수로 전달되는 값으로 설정됩니다. ForEach 활동은 테이블 목록을 반복하여 복사 활동에 값을 하나씩 전달합니다. 
   
    ![매개 변수 이름](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>파이프라인 만들기
이 자습서에서는 **IterateAndCopySQLTables** 및 **GetTableListAndTriggerCopyData**라는 두 개의 파이프라인을 만듭니다. 

**GetTableListAndTriggerCopyData** 파이프라인은 다음 두 단계를 수행합니다.

* Azure SQL Database 시스템 테이블을 찾아 복사할 테이블의 목록을 가져옵니다.
* **IterateAndCopySQLTables** 파이프라인을 트리거하여 실제 데이터 복사를 수행합니다.

**GetTableListAndTriggerCopyData**는 테이블 목록을 매개 변수로 사용합니다. 목록의 각 테이블에 대해 스테이징된 복사본과 PolyBase를 사용하여 Azure SQL Database의 테이블에서 Azure SQL Data Warehouse로 데이터를 복사합니다.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>IterateAndCopySQLTables 파이프라인 만들기

1. 왼쪽 창에서 **+(더하기)**, **파이프라인**을 차례로 클릭합니다.

    ![새 파이프라인 메뉴](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. [속성] 창에서 파이프라인 이름을 **IterateAndCopySQLTables**로 변경합니다. 

    ![파이프라인 이름](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. **매개 변수** 탭으로 전환하고 다음 작업을 수행합니다. 

    1. **+새로 만들기**를 클릭합니다. 
    2. 매개 변수 **이름**에 대해 **tableList**를 입력합니다.
    3. **유형**에 대해 **개체**를 선택합니다.

        ![파이프라인 매개 변수](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. **활동** 도구 상자에서 **반복 및 조건**을 펼치고, **ForEach** 활동을 파이프라인 디자인 화면으로 끌어서 놓습니다. 또한 **활동** 도구 상자에서 활동을 검색할 수도 있습니다. 아래쪽의 **속성** 창에서 **이름**에 대해 **IterateSQLTables**를 입력합니다. 

    ![ForEach 활동 이름](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. **설정** 탭으로 전환하고 **항목**에 대해 `@pipeline().parameters.tableList`를 입력합니다.

    ![ForEach 활동 설정](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. **ForEach** 활동에 자식 활동을 추가하려면, **ForEach** 활동을 두 번 클릭하거나 **편집(연필 아이콘)**을 클릭합니다. 활동을 선택할 때만 해당 활동에 대한 작업 링크가 표시됩니다. 

    ![ForEach 활동 이름](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, [속성] 창에서 이름을 **CopyData**로 변경합니다. 위쪽의 이동 경로 탐색 메뉴를 확인합니다. IterateAndCopySQLTable은 파이프라인 이름이고, IterateSQLTables는 ForEach 활동 이름입니다. 디자이너가 활동 범위에 있습니다. ForEach 편집기에서 파이프라인 편집기로 다시 전환하려면 이동 경로 탐색 메뉴에서 링크를 클릭합니다. 

    ![ForEach의 복사](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. **원본** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **원본 데이터 집합**에 대해 **AzureSqlDatabaseDataset**를 선택합니다. 
    2. **쿼리 사용**에 대해 **쿼리** 옵션을 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![원본 복사 설정](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. **싱크** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **싱크 데이터 집합**에 대해 **AzureSqlDWDataset**를 선택합니다.
    2. **Polybase 설정**을 펼치고 **Polybase 허용**을 선택합니다. 
    3. **유형 기본 사용** 옵션의 선택을 취소합니다. 
    4. **정리 스크립트**에 대해 다음 SQL 스크립트를 입력합니다. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![싱크 복사 설정](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. **매개 변수** 탭으로 전환하고, 필요한 경우 아래로 스크롤하여 **DWTableName** 매개 변수가 있는 **싱크 데이터 집합** 섹션을 확인합니다. 이 매개 변수의 값을 `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`으로 설정합니다.

    ![싱크 복사 매개 변수](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. **설정** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **준비 사용**에 대해 **True**(사용)를 선택합니다.
    2. **저장소 계정 연결된 서비스**에 대해 **AzureStorageLinkedService**를 선택합니다.

        ![준비 사용](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. 파이프라인 설정에 대한 유효성을 검사하려면 **유효성 검사**를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>**를 클릭합니다.

    ![파이프라인 유효성 검사 보고서](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>GetTableListAndTriggerCopyData 파이프라인 만들기

이 파이프라인에서는 두 단계를 수행합니다.

* Azure SQL Database 시스템 테이블을 찾아 복사할 테이블의 목록을 가져옵니다.
* "IterateAndCopySQLTables" 파이프라인을 트리거하여 실제 데이터 복사를 수행합니다.

1. 왼쪽 창에서 **+(더하기)**, **파이프라인**을 차례로 클릭합니다.

    ![새 파이프라인 메뉴](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. [속성] 창에서 파이프라인 이름을 **GetTableListAndTriggerCopyData**로 변경합니다. 

    ![파이프라인 이름](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. **활동** 도구 상자에서 **일반**을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, 다음 단계를 수행합니다.

    1. **이름**에 대해 **LookupTableList**를 입력합니다. 
    2. **설명**에 대해 **Azure SQL Database 에서 테이블 목록을 검색합니다.**를 입력합니다.

        ![조회 활동 - 일반 페이지](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. **설정** 페이지로 전환하고 다음 단계를 수행합니다.

    1. **원본 데이터 집합**에 대해 **AzureSqlDatabaseDataset**를 선택합니다. 
    2. **쿼리 사용**에 대해 **쿼리**를 선택합니다. 
    3. **쿼리**에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. **First row only**(첫 번째 행만) 필드의 확인란 선택을 취소합니다.

        ![조회 활동 - 설정 페이지](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. **파이프라인 실행** 활동을 [활동] 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓고, 이름을 **TriggerCopy**로 설정합니다.

    ![파이프라인 실행 활동 - 일반 페이지](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. **설정** 페이지로 전환하고 다음 단계를 수행합니다. 

    1. **호출된 파이프라인**에 대해 **IterateAndCopySQLTables**를 선택합니다. 
    2. **고급** 섹션을 펼칩니다. 
    3. **매개 변수** 섹션에서 **+ 새로 만들기**를 클릭합니다. 
    4. 매개 변수 **이름**에 대해 **tableList**를 입력합니다.
    5. 매개 변수 **값**에 대해 `@activity('LookupTableList').output.value`를 입력합니다. 조회 활동의 결과 목록을 두 번째 파이프라인에 대한 입력으로 설정합니다. 결과 목록에는 데이터를 대상에 복사해야 하는 테이블 목록이 포함됩니다. 

        ![파이프라인 실행 활동 -설정 페이지](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. 조회 활동에 붙어 있는 **녹색 상자**를 파이프라인 실행 활동의 왼쪽으로 끌어서 **조회** 활동을 **파이프라인 실행** 활동에 **연결**합니다.

    ![조회 활동 및 파이프라인 실행 활동 연결](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. 파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>**를 클릭합니다.

    ![두 번째 파이프라인 - 유효성 검사 보고서](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. 엔터티(데이터 집합, 파이프라인 등)를 Data Factory 서비스에 게시하려면 **모두 게시**를 클릭합니다. 게시가 성공적으로 완료될 때까지 기다립니다. 

    ![게시 단추](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거

1. **GetTableListAndTriggerCopyData** 탭이 활성화되어 있는지 확인합니다. 
2. **트리거**, **지금 트리거**를 차례로 클릭합니다. 

    ![지금 트리거](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. **모니터** 탭으로 전환합니다. 솔루션에서 두 파이프라인 모두에 대한 실행이 표시될 때까지 **새로 고침**을 클릭합니다. **성공** 상태가 표시될 때까지 목록 새로 고침을 계속합니다. 

    ![파이프라인 실행](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. GetTableListAndTriggerCopyData 파이프라인과 연결된 활동 실행을 보려면, 해당 파이프라인에 대한 [작업] 링크의 첫 번째 링크를 클릭합니다. 이 파이프라인 실행에 대한 두 개의 활동 실행이 표시됩니다. 

    ![작업 실행](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. **조회** 활동의 출력을 보려면 해당 활동의 **출력** 열에 있는 링크를 클릭합니다. **출력** 창을 최대화하고 복원할 수 있습니다. 검토한 후에 **X**를 클릭하여 **출력** 창을 닫습니다.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. **파이프라인 실행** 보기로 다시 전환하려면 위쪽의 **파이프라인** 링크를 클릭합니다. **IterateAndCopySQLTables** 파이프라인에 대한 **활동 실행 보기** 링크(**작업** 열의 첫 번째 링크)를 클릭합니다. 다음 이미지와 같이 출력이 표시됩니다. **조회** 활동 출력에서 각 테이블에 대해 하나의 **복사** 활동만 실행됨을 확인할 수 있습니다. 

    ![작업 실행](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. 이 자습서에서 사용한 대상 SQL Data Warehouse에 데이터가 복사되었는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure SQL Database, Azure SQL Data Warehouse 및 Azure Storage 연결된 서비스를 만듭니다.
> * Azure SQL Database 및 Azure SQL Data Warehouse 데이터 집합을 만듭니다.
> * 복사할 테이블을 조회하는 파이프라인을 만들고, 실제 복사 작업을 수행하는 다른 파이프라인을 만듭니다. 
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

원본에서 대상으로 데이터를 증분 방식으로 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.
> [!div class="nextstepaction"]
>[증분 방식으로 데이터 복사](tutorial-incremental-copy-portal.md)