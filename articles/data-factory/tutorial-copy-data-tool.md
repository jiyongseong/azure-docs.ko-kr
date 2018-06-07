---
title: Azure 데이터 복사 도구를 사용하여 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 만든 다음, 데이터 복사 도구를 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>데이터 복사 도구를 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터 복사
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [버전 1 - 일반 공급](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](tutorial-copy-data-tool.md)

이 자습서에서는 Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 그런 다음, 데이터 복사 도구를 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사하는 파이프라인을 만듭니다. 

> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.
>
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급되는 Data Factory 버전 1을 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.


이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure 저장소 계정**: Blob 저장소를 _원본_ 데이터 저장소로 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)의 지침을 참조하세요.
* **Azure SQL 데이터베이스**: SQL 데이터베이스를 _싱크_ 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 [SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)의 지침을 참조하세요.

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

다음 단계를 수행하여 자습서에서 사용할 Blob 저장소와 SQL 데이터베이스를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. **메모장**을 시작합니다. 다음 텍스트를 복사하여 디스크에 **inputEmp.txt**라는 파일로 저장합니다.

    ```
    John|Doe
    Jane|Doe
    ```

2. **adfv2tutorial**이라는 컨테이너를 만들고, inputEmp.txt 파일을 이 컨테이너에 업로드합니다. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 다양한 도구를 사용하여 이러한 작업을 수행할 수 있습니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

1. 다음 SQL 스크립트를 사용하여 SQL 데이터베이스에 **dbo.emp**라는 테이블을 만듭니다.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure 서비스에서 SQL Server에 액세스하도록 허용합니다. SQL Server를 실행하는 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. 이 설정을 사용하면 Data Factory에서 SQL Server 인스턴스에 데이터를 쓸 수 있습니다. 이 설정을 확인하고 설정하려면 다음 단계를 수행합니다.

    a. 왼쪽에서 **더 많은 서비스**를 선택한 다음, **SQL 서버**를 선택합니다.

    나. 서버를 선택한 다음, **설정** > **방화벽**을 차례로 선택합니다.

    다. **방화벽 설정** 페이지에서 **Azure 서비스 방문 허용** 옵션을 **켜기**로 설정합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **+ 새로 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다. 
   
   ![새 데이터 팩터리 만들기](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 data factory](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   데이터 팩터리 이름은 _전역적으로 고유_해야 합니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.
   
   ![새 데이터 팩터리 오류 메시지](./media/tutorial-copy-data-tool/name-not-available-error.png)

   이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**를 사용합니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.
3. 새 데이터 팩터리를 만들 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    나. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

5. **버전** 아래에서 버전에 대해 **V2(미리 보기)**를 선택합니다.
6. **위치** 아래에서 데이터 팩터리에 대한 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 위치와 지역에 있을 수 있습니다.
7. **대시보드에 고정**을 선택합니다. 
8. **만들기**를 선택합니다.
9. 대시보드의 **Data Factory 배포 중** 타일에서 프로세스 상태가 표시됩니다.

    ![Data Factory 배포 중 타일](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. 만들기가 완료되면 **Data Factory** 홈페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작하려면 **작성 및 모니터링** 타일을 선택합니다. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>데이터 복사 도구를 사용하여 파이프라인 만들기

1. **시작** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 시작합니다. 

   ![데이터 복사 도구 타일](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. **속성** 페이지의 **작업 이름** 아래에서 **CopyFromBlobToSqlPipeline**을 입력합니다. 그런 후 **다음**을 선택합니다. Data Factory UI에서 지정한 작업 이름이 있는 파이프라인을 만듭니다. 

    ![속성 페이지](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. **원본 데이터 저장소** 페이지에서 **Azure Blob Storage**를 선택한 후 **다음**을 선택합니다. 원본 데이터는 Blob 저장소에 있습니다. 

    ![원본 데이터 저장소 페이지](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. **Azure Blob 저장소 계정 지정** 페이지에서 다음 단계를 수행합니다.

    a. **연결 이름** 아래에서 **AzureStorageLinkedService**를 입력합니다.

    나. **저장소 계정 이름** 드롭다운 목록에서 저장소 계정 이름을 선택합니다.

    다. **다음**을 선택합니다. 

    ![저장소 계정 지정](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. 여기서는 Storage 연결된 서비스를 만들어 저장소 계정을 데이터 저장소에 연결합니다. 연결된 서비스에는 런타임에 Data Factory에서 Blob 저장소에 연결하는 데 사용하는 연결 정보가 있습니다. 데이터 집합은 원본 데이터가 포함된 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다. 

5. **입력 파일 또는 폴더 선택** 페이지에서 다음 단계를 수행합니다.
    
    a. **adfv2tutorial/input** 폴더로 이동합니다.

    나. **inputEmp.txt** 파일을 선택합니다.

    다. **선택**을 선택합니다. 또는 **inputEmp.txt** 파일을 두 번 클릭할 수도 있습니다.

    d. **다음**을 선택합니다. 

    ![입력 파일 또는 폴더 선택](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. **파일 형식 설정** 페이지에서 도구를 통해 열 및 행 구분 기호를 자동으로 검색합니다. **다음**을 선택합니다. 또한 이 페이지에서 데이터를 미리 보고 입력 데이터의 스키마를 볼 수도 있습니다. 

    ![파일 형식 설정](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. **대상 데이터 저장소** 페이지에서 **Azure SQL Database**, **다음**을 차례로 선택합니다.

    ![대상 데이터 저장소](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. **Azure SQL 데이터베이스 지정** 페이지에서 다음 단계를 수행합니다. 

    a. **연결 이름** 아래에서 **AzureSqlDatabaseLinkedService**를 지정합니다.

    나. **서버 이름** 아래에서 SQL Server 인스턴스를 선택합니다.

    다. **데이터베이스 이름** 아래에서 SQL 데이터베이스를 선택합니다.

    d. **사용자 이름** 아래에서 사용자의 이름을 입력합니다.

    e. **암호** 아래에서 사용자의 암호를 입력합니다.

    f. **다음**을 선택합니다. 

    ![SQL 데이터베이스 지정](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    데이터 집합은 연결된 서비스와 연결되어야 합니다. 연결된 서비스에는 런타임에 Data Factory에서 SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열 있습니다. 데이터 집합은 데이터가 복사될 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다.

9. **테이블 매핑** 페이지에서 **[dbo].[emp]** 테이블, **다음**을 차례로 선택합니다. 

    ![테이블 매핑](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. **스키마 매핑** 페이지에서 입력 파일의 첫 번째 및 두 번째 열이 **emp** 테이블의 **FirstName** 및 **LastName** 열에 매핑됩니다.

    ![스키마 매핑 페이지](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. **설정** 페이지에서 **다음**을 선택합니다. 

    ![설정 페이지](./media/tutorial-copy-data-tool/settings-page.png)
12. **요약** 페이지에서 설정을 검토하고 **다음**을 선택합니다.

    ![요약 페이지](./media/tutorial-copy-data-tool/summary-page.png)
13. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/tutorial-copy-data-tool/deployment-page.png)
14. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![파이프라인 실행 모니터링](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 활동(복사 활동)만 있으므로 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. **파이프라인 실행** 보기로 돌아가려면 위쪽의 **파이프라인** 링크를 선택합니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다. 

    ![작업 실행 모니터링](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. 왼쪽의 **편집** 탭을 선택하여 편집기 모드로 전환합니다. 편집기를 사용하여 도구를 통해 만든 연결된 서비스, 데이터 집합 및 파이프라인을 업데이트할 수 있습니다. **코드**를 선택하여 현재 편집기에서 열려 있는 엔터티에 대한 JSON 코드를 살펴봅니다. Data Factory UI에서 이러한 엔터티를 편집하는 방법에 대한 자세한 내용은 [이 자습서의 Azure Portal 버전](tutorial-copy-data-portal.md)을 참조하세요.

    ![편집기 탭](./media/tutorial-copy-data-tool/edit-tab.png)
17. 데이터가 SQL 데이터베이스의 **emp** 테이블에 삽입되어 있는지 확인합니다.

    ![SQL 출력 확인](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인이 Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 데이터 복사 도구를 사용하여 파이프라인 만들기
> * 파이프라인 및 작업 실행을 모니터링합니다.

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-data-tool.md)
