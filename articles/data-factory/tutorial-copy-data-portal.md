---
title: Azure Portal을 사용하여 Data Factory 파이프라인 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대한 단계별 지침을 제공합니다. 파이프라인은 복사 활동을 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다.
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
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 34c78a114c1d106c400a94941aa113153383e206
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob 저장소에서 SQL 데이터베이스로 데이터 복사
이 자습서에서는 Azure Data Factory UI(사용자 인터페이스)를 사용하여 데이터 팩터리를 만듭니다. 데이터 팩터리의 파이프라인은 Azure Blob 저장소에서 SQL 데이터베이스로 데이터를 복사합니다. 이 자습서의 구성 패턴은 파일 기반 데이터 저장소에서 관계형 데이터 저장소로 복사하는 데 적용됩니다. 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

> [!NOTE]
> - Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.
>
> - 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급되는 Data Factory 버전 1을 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 복사 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 테스트
> * 수동으로 파이프라인 트리거
> * 일정에 따라 파이프라인 트리거
> * 파이프라인 및 작업 실행을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure Storage 계정**. Blob 저장소를 *원본* 데이터 저장소로 사용합니다. 저장소 계정이 없는 경우 [Azure 저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 *싱크* 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 [SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)를 참조하세요.

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 다음 단계를 수행하여 자습서에서 사용할 Blob 저장소 및 SQL 데이터베이스를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하고 디스크에 **emp.txt** 파일로 저장합니다.

    ```
    John,Doe
    Jane,Doe
    ```

2. Blob 저장소에 **adftutorial**이라는 컨테이너를 만듭니다. 이 컨테이너에 **input**이라는 폴더를 만듭니다. 그런 다음 **emp.txt** 파일을 **input** 폴더에 업로드합니다. 이러한 작업을 수행하려면 Azure Portal 또는 [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

1. 다음 SQL 스크립트를 사용하여 SQL 데이터베이스에 **dbo.emp** 테이블을 만듭니다.

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

2. Azure 서비스에서 SQL Server에 액세스하도록 허용합니다. Data Factory에서 SQL Server에 데이터를 쓸 수 있도록 SQL Server에 대해 **Azure 서비스 방문 허용**이 **켜기**로 설정되어 있는지 확인합니다. 이 설정을 확인하고 설정하려면 다음 단계를 수행합니다.

    a. 왼쪽에서 **더 많은 서비스** > **SQL 서버**를 차례로 선택합니다.

    나. 서버를 선택하고 **설정** 아래에서 **방화벽**을 선택합니다.

    다. **방화벽 설정** 페이지에서 **Azure 서비스 방문 허용**에 대해 **켜기**를 선택합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
이 단계에서는 데이터 팩터리를 만들고, Data Factory UI를 시작하여 파이프라인을 데이터 팩터리에 만듭니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. 왼쪽 메뉴에서 **새로 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다. 
  
   ![새 데이터 팩터리 만들기](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 data factory](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 이름 필드에 대해 다음과 같은 오류 메시지가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.
  
   ![오류 메시지](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. 데이터 팩터리를 만들 Azure **구독**을 선택합니다. 
5. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
    a. **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    나. **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 
6. **버전** 아래에서 **V2(미리 보기)**를 선택합니다.
7. **위치**에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
8. **대시보드에 고정**을 선택합니다. 
9. **만들기**를 선택합니다. 
10. 대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다. 

    ![Data Factory 배포 중 타일](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. **작성 및 모니터링**을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 단계에서는 복사 활동이 있는 파이프라인을 데이터 팩터리에 만듭니다. 복사 활동은 Blob 저장소에서 SQL Database로 데이터를 복사합니다. [빠른 시작 자습서](quickstart-create-data-factory-portal.md)에서 다음 단계에 따라 파이프라인을 만들었습니다.

1. 연결된 서비스를 만듭니다. 
2. 입력 및 출력 데이터 집합을 만듭니다.
3. 파이프라인을 만듭니다.

이 자습서에서는 파이프라인을 만드는 것부터 시작합니다. 그런 다음, 파이프라인을 구성해야 할 때 연결된 서비스와 데이터 집합을 만듭니다. 

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![파이프라인 만들기](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. 파이프라인에 대한 **속성** 창의 **이름** 아래에서 파이프라인 이름으로 **CopyPipeline**을 입력합니다.

    ![파이프라인 이름](./media/tutorial-copy-data-portal/pipeline-name.png)
3. **활동** 도구 상자에서 **데이터 흐름** 범주를 펼치고, **복사** 활동을 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

    ![복사 활동](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. **속성** 창의 **일반** 탭에서 활동 이름으로 **CopyFromBlobToSql**을 입력합니다.

    ![작업 이름](./media/tutorial-copy-data-portal/activity-name.png)
5. **원본** 탭으로 이동합니다. **+ 새로 만들기**를 선택하여 원본 데이터 집합을 만듭니다. 

    ![원본 탭](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. **새 데이터 집합** 창에서 **Azure Blob Storage**를 선택한 다음, **마침**을 선택합니다. 원본 데이터는 Blob 저장소에 있으므로 원본 데이터 집합으로 **Azure Blob Storage**를 선택합니다. 

    ![저장소 선택](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. 응용 프로그램에서 **AzureBlob1** 제목으로 열린 새 탭이 표시됩니다.

    ![AzureBlob1 tab ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. **속성** 창의 아래쪽에 있는 **일반** 탭의 **이름**에서 **SourceBlobDataset**를 입력합니다.

    ![데이터 집합 이름](./media/tutorial-copy-data-portal/dataset-name.png)
9. **속성** 창의 **연결** 탭으로 이동합니다. **연결된 서비스** 텍스트 상자 옆에 있는 **+ 새로 만들기**를 선택합니다. 

    연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. 여기서는 Storage 연결된 서비스를 만들어 저장소 계정을 데이터 저장소에 연결합니다. 연결된 서비스에는 런타임에 Data Factory에서 Blob 저장소에 연결하는 데 사용하는 연결 정보가 있습니다. 데이터 집합은 원본 데이터가 포함된 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다. 

    ![새 연결된 서비스 단추](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    a. **이름** 아래에서 **AzureStorageLinkedService**를 입력합니다. 

    나. **저장소 계정 이름** 아래에서 저장소 계정을 선택합니다.

    다. **연결 테스트**를 선택하여 저장소 계정에 대한 연결을 테스트합니다.

    d. **저장**을 선택하여 연결된 서비스를 저장합니다.

    ![새 연결된 서비스](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. **파일 경로** 옆에 있는 **찾아보기**를 선택합니다.

    ![파일 경로에 대한 찾아보기 단추](./media/tutorial-copy-data-portal/file-browse-button.png)
12. **adftutorial/input** 폴더로 이동하고, **emp.txt** 파일을 선택한 다음, **마침**을 선택합니다. 또는 **emp.txt**를 두 번 클릭할 수도 있습니다. 

    ![입력 파일 선택](./media/tutorial-copy-data-portal/select-input-file.png)
13. **파일 형식**이 **텍스트 형식**으로 설정되고, **열 구분 기호**가 **쉼표(`,`)**로 설정되어 있는지 확인합니다. 원본 파일에서 다른 행 및 열 구분 기호를 사용하는 경우 **파일 형식**에 대해 **텍스트 형식 검색**을 선택할 수 있습니다. 데이터 복사 도구에서 파일 형식과 구분 기호를 자동으로 검색합니다. 이러한 값은 여전히 재정의할 수 있습니다. 이 페이지에서 데이터를 미리 보려면 **데이터 미리 보기**를 선택합니다.

    ![텍스트 형식 검색](./media/tutorial-copy-data-portal/detect-text-format.png)
14. **속성** 창의 **스키마** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 응용 프로그램이 원본 파일에서 두 개의 열을 검색했습니다. 원본 데이터 저장소의 열을 싱크 데이터 저장소에 매핑할 수 있도록 스키마를 여기로 가져옵니다. 열을 매핑할 필요가 없으면 이 단계를 건너뛸 수 있습니다. 이 자습서에서는 스키마를 가져옵니다.

    ![원본 스키마 검색](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. 이제 파이프라인이 있는 탭으로 이동하거나 왼쪽의 파이프라인을 선택합니다.

    ![파이프라인 탭](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. **속성** 창의 **원본 데이터 집합**에서 **SourceBlobDataset**가 선택되어 있는지 확인합니다. 이 페이지에서 데이터를 미리 보려면 **데이터 미리 보기**를 선택합니다. 
    
    ![원본 데이터 집합](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. **싱크** 탭으로 이동하고, **+ 새로 만들기**를 선택하여 싱크 데이터 집합을 만듭니다. 

    ![싱크 데이터 집합](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. **새 데이터 집합** 창에서 **Azure SQL Database**를 선택한 다음, **마침**을 선택합니다. 이 자습서에서는 데이터를 SQL 데이터베이스에 복사합니다. 

    ![SQL 데이터베이스 선택](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. **속성** 창에 있는 **일반** 탭의 **이름**에서 **OutputSqlDataset**를 설정합니다. 
    
    ![출력 데이터 집합 이름](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. **연결** 탭으로 이동하여 **연결된 서비스** 옆에 있는 **+ 새로 만들기**를 선택합니다. 데이터 집합은 연결된 서비스와 연결되어야 합니다. 연결된 서비스에는 런타임에 Data Factory에서 SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열 있습니다. 데이터 집합은 데이터가 복사될 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다. 
    
    ![연결된 서비스](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    a. **이름** 아래에서 **AzureSqlDatabaseLinkedService**를 입력합니다.

    나. **서버 이름** 아래에서 SQL Server 인스턴스를 선택합니다.

    다. **데이터베이스 이름** 아래에서 SQL 데이터베이스를 선택합니다.

    d. **사용자 이름** 아래에서 사용자의 이름을 입력합니다.

    e. **암호** 아래에서 사용자의 암호를 입력합니다.

    f. **연결 테스트**를 선택하여 연결을 테스트합니다.

    g. **저장**을 선택하여 연결된 서비스를 저장합니다. 
    
    ![새 연결된 서비스 저장](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. **테이블**에서 **[dbo].[emp]**를 선택합니다. 

    ![테이블](./media/tutorial-copy-data-portal/select-emp-table.png)
23. **스키마** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 

    ![스키마 가져오기 선택](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. **ID** 열을 선택한 다음, **삭제**를 선택합니다. **ID** 열은 SQL 데이터베이스의 ID 열이므로 복사 활동에서 이 열에 데이터를 삽입할 필요가 없습니다.

    ![ID 열 삭제](./media/tutorial-copy-data-portal/delete-id-column.png)
25. 파이프라인이 있는 탭으로 이동하고, **싱크 데이터 집합**에서 **OutputSqlDataset**가 선택되어 있는지 확인합니다.

    ![파이프라인 탭](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. **속성** 창의 아래쪽에 있는 **매핑** 탭으로 이동하고 **스키마 가져오기**를 선택합니다. 원본 파일의 첫 번째 및 두 번째 열은 SQL 데이터베이스의 **FirstName** 및 **LastName**에 매핑됩니다.

    ![스키마 매핑](./media/tutorial-copy-data-portal/map-schemas.png)
27. 파이프라인에 대한 유효성을 검사하려면 **유효성 검사**를 선택합니다. 오른쪽 위 모서리에서 오른쪽 화살표를 선택하여 유효성 검사 창을 닫습니다.

    ![파이프라인 유효성 검사 출력](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. 오른쪽 위 모서리에서 **코드**를 선택합니다. 파이프라인과 연결된 JSON 코드가 표시됩니다. 

    ![코드 단추](./media/tutorial-copy-data-portal/code-button.png)
29. 다음 코드 조각과 비슷한 JSON 코드가 표시됩니다. 

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>파이프라인 실행 테스트
아티팩트(연결된 서비스, 데이터 집합 및 파이프라인)를 Data Factory 또는 사용자 고유의 Visual Studio Team Services Git 리포지토리에 게시하기 전에 파이프라인 실행을 테스트할 수 있습니다. 

1. 파이프라인 실행을 테스트하려면 도구 모음에서 **실행 테스트**를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다. 

    ![파이프라인 테스트](./media/tutorial-copy-data-portal/test-run-output.png)
2. 원본 파일의 데이터가 대상 SQL 데이터베이스에 삽입되었는지 확인합니다. 

    ![SQL 출력 확인](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. 왼쪽 창에서 **모두 게시**를 선택합니다. 이 작업은 사용자가 만든 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 Data Factory에 게시합니다.

    ![게시](./media/tutorial-copy-data-portal/publish-button.png)
4. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 표시하려면 왼쪽 세로 막대에서 **알림 표시** 탭을 선택합니다. 알림 창을 닫으려면 **Close**를 클릭합니다.

    ![알림 표시](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>코드 리포지토리 구성
데이터 팩터리 아티팩트와 연결된 코드를 Visual Studio Team Services 코드 리포지토리에 게시할 수 있습니다. 이 단계에서는 코드 리포지토리를 만듭니다.  VSTS 통합을 사용한 시각적 작성에 대한 자세한 내용은 [VSTS Git 통합을 통한 작성](author-visually.md#author-with-vsts-git-integration)을 참조하세요.

Visual Studio Team Services 코드 리포지토리를 사용하지 않으려면 이 단계를 건너뛸 수 있습니다. 이전 단계에서 수행한 대로 데이터 팩터리에 계속 게시할 수 있습니다. 

1. 왼쪽 위 모서리에서 **Data Factory**를 선택하거나 옆에 있는 아래쪽 화살표를 사용하고, **코드 리포지토리 구성**을 선택합니다. 

    ![코드 리포지토리 구성](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. **리포지토리 설정** 페이지에서 다음 단계를 수행합니다.

    a. **리포지토리 유형** 아래에서 **Visual Studio Team Services Git**을 선택합니다.

    나. **Visual Studio Team Services 계정** 아래에서 Visual Studio Team Services 계정을 선택합니다.

    다. **프로젝트 이름** 아래에서 Visual Studio Team Services 계정으로 프로젝트를 선택합니다.

    d. **Git 리포지토리 이름** 아래에서 데이터 팩터리와 연결할 Git 리포지토리에 대해 **Tutorial2**를 입력합니다.

    e. **리포지토리로 기존 Data Factory 리소스 가져오기** 확인란이 선택되어 있는지 확인합니다.

    f. **저장**을 선택하여 설정을 저장합니다. 

    ![리포지토리 설정](./media/tutorial-copy-data-portal/repository-settings.png)
3. 리포지토리에 대해 **VSTS GIT**가 선택되어 있는지 확인합니다.

    ![VSTS GIT 선택](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. 웹 브라우저의 별도 탭에서 **Tutorial2** 리포지토리로 이동합니다. **adf_publish**와 **master**의 두 개의 분기가 표시됩니다.

    ![master 및 adf_publish 분기](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Data Factory 엔터티에 대한 JSON 파일이 **master** 분기에 있는지 확인합니다.

    ![master 분기의 파일](./media/tutorial-copy-data-portal/master-branch-files.png)
6. JSON 파일이 **adf_publish** 분기에 없는지 확인합니다. 

    ![adf_publish 분기의 파일](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. **설명**에서 파이프라인에 대한 설명을 추가하고, 도구 모음에서 **저장**을 선택합니다. 

    ![파이프라인 설명](./media/tutorial-copy-data-portal/pipeline-description.png)
8. 이제 **Tutorial2** 리포지토리에서 사용자 이름이 있는 분기가 표시됩니다. 변경 내용은 master 분기가 아닌 사용자 고유의 분기에 있습니다. master 분기에서만 엔터티를 게시할 수 있습니다.

    ![사용자 고유의 분기](./media/tutorial-copy-data-portal/your-branch.png)
9. 마우스를 **동기화** 단추 위로 이동하고(아직 선택하지 않음), **변경 내용 적용** 확인란을 선택하고, **동기화**를 선택하여 변경 내용을 master 분기와 동기화합니다. 

    ![변경 내용 적용 및 동기화](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. **변경 내용 동기화** 창에서 다음 작업을 수행합니다. 

    a. 업데이트된 **파이프라인** 목록에 **CopyPipeline**이 표시되는지 확인합니다.

    나. **동기화 후 변경 내용 게시**가 선택되어 있는지 확인합니다. 이 확인란의 선택을 취소하면 분기의 변경 내용만 master 분기와 동기화됩니다. 데이터 팩터리에는 게시되지 않습니다. 나중에 **게시** 단추를 사용하여 게시할 수 있습니다. 이 확인란을 선택하면 변경 내용이 먼저 master에 동기화된 다음, Data Factory에 게시됩니다.

    다. **동기화**를 선택합니다. 

    ![변경 내용 동기화](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. 이제 **Tutorial2** 리포지토리의 **adf_publish** 분기에서 파일이 표시됩니다. 또한 이 분기에서 Data Factory 솔루션에 대한 Azure Resource Manager 템플릿을 찾을 수도 있습니다. 

    ![adf_publish 분기의 파일 목록](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>수동으로 파이프라인 트리거
이 단계에서는 이전 단계에서 게시한 파이프라인을 수동으로 트리거합니다. 

1. 도구 모음에서 **트리거**를 선택한 다음 **지금 트리거**를 선택합니다. **파이프라인 실행** 페이지에서 **마침**을 선택합니다.  

    ![파이프라인 트리거](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. 왼쪽의 **모니터** 탭으로 이동합니다. 수동 트리거로 트리거되는 파이프라인 실행이 표시됩니다. **작업** 열의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

    ![파이프라인 실행 모니터링](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. 파이프라인 실행과 연결된 활동 실행을 확인하려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 이 예에서는 활동이 하나뿐이므로 목록에 하나의 항목만 표시됩니다. 복사 활동에 대한 자세한 내용은 **작업** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 위쪽의 **파이프라인**을 선택하여 **파이프라인 실행** 보기로 돌아갑니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.

    ![작업 실행 모니터링](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. SQL 데이터베이스의 **emp** 테이블에 둘 이상의 행이 추가되어 있는지 확인합니다. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>일정에 따라 파이프라인 트리거
이 일정에서 파이프라인에 대한 스케줄러 트리거를 만듭니다. 트리거는 지정된 일정(예: 매시간, 매일)에 따라 파이프라인을 실행합니다. 이 예에서는 지정된 종료 날짜/시간까지 매분마다 실행되도록 트리거를 설정합니다. 

1. 왼쪽의 **편집** 탭으로 이동합니다. 

    ![편집 탭](./media/tutorial-copy-data-portal/edit-tab.png)
2. **트리거**를 선택하고 **새로 만들기/편집**을 선택합니다. 파이프라인이 활성 상태가 아닌 경우 해당 파이프라인으로 이동합니다. 

    ![트리거 옵션](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. **트리거 추가** 창에서 **트리거 선택**, **새로 만들기**를 차례로 선택합니다. 

    ![새 단추](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. **새 트리거** 창에서 다음 단계를 수행합니다. 

    a. **이름** 아래에서 **RunEveryMinute**를 입력합니다.

    나. **종료** 아래에서 **날짜**를 선택합니다.

    다. **종료 날짜/시간** 아래에서 드롭다운 목록을 선택합니다.

    d. **현재 날짜** 옵션을 선택합니다. 기본적으로 종료 날짜는 다음 날로 설정됩니다.

    e. **분** 부분은 현재 날짜/시간 이후 몇 분으로 업데이트합니다. 트리거는 변경 내용을 게시한 후에만 활성화됩니다. 몇 분 이후로 설정하고 그때까지 게시하지 않으면 트리거 실행이 표시되지 않습니다.

    f. **적용**을 선택합니다. 

    ![트리거 속성](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. **활성화됨** 옵션을 선택합니다. 이 확인란을 사용하여 비활성화할 수 있으며, 나중에 활성화할 수 있습니다.

    h. **다음**을 선택합니다.

    ![활성화됨 단추](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > 비용은 각 파이프라인 실행과 연결되므로 종료 날짜를 적절하게 설정합니다. 
5. **트리거 실행 매개 변수** 페이지에서 경고를 검토한 다음, **마침**을 선택합니다. 다음 예의 파이프라인은 매개 변수를 사용하지 않습니다. 

    ![트리거 실행 매개 변수](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. **동기화**를 선택하여 분기의 변경 내용을 master 분기와 동기화합니다. **동기화 후 변경 내용 게시**는 기본적으로 선택되어 있습니다. **동기화**를 선택하면 업데이트된 엔터티도 master 분기의 Data Factory에 게시됩니다. 게시가 성공적으로 완료될 때까지 트리거는 활성화되지 않습니다.

    ![변경 내용 동기화](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. 왼쪽의 **모니터** 탭으로 이동하여 트리거된 파이프라인 실행을 확인합니다. 

    ![트리거된 파이프라인 실행](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. **파이프라인 실행** 보기에서 **트리거 실행** 보기로 전환하려면 **파이프라인 실행**을 선택한 다음, **트리거 실행**을 선택합니다.
    
    ![트리거 실행](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. 목록에서 트리거 실행이 표시됩니다. 

    ![트리거 실행 목록](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. 지정된 종료 시간까지 각 파이프라인 실행에 대해 분당 두 행이 **emp** 테이블에 삽입되었는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인이 Blob 저장소의 위치 간에 데이터를 복사합니다. 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * 복사 활동이 있는 파이프라인 만들기
> * 파이프라인 실행 테스트
> * 수동으로 파이프라인 트리거
> * 일정에 따라 파이프라인 트리거
> * 파이프라인 및 작업 실행을 모니터링합니다.


온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-portal.md)
