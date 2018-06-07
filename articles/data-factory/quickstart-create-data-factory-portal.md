---
title: Azure Data Factory UI를 사용하여 Azure 데이터 팩터리 만들기 | Microsoft Docs
description: Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만듭니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 02/01/2018
ms.author: jingwang
ms.openlocfilehash: ea70d6053f12c33d9c3ff4c69f22a41130c783e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Azure Data Factory UI를 사용하여 데이터 팩터리 만들기
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-portal.md)

이 빠른 시작에서는 Azure Data Factory UI를 사용하여 데이터 팩터리를 만들고 모니터링하는 방법에 대해 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob 저장소의 한 폴더에서 다른 폴더로 데이터를 *복사합니다*. Azure Data Factory를 사용하여 데이터를 *변환*하는 방법에 대한 자습서는 [자습서: Apache Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-portal.md)을 참조하세요. 


> [!NOTE]
> Azure Data Factory를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요. 
>
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 버전 1 서비스를 사용하는 경우 [데이터 팩터리 버전 1 자습서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>비디오 
이 비디오를 시청하면 Data Factory UI를 이해하는 데 도움이 됩니다. 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure 포털](https://portal.azure.com)로 이동합니다. 
3. 왼쪽 메뉴에서 **새로 만들기**를 선택하고 **데이터 + 분석**을 선택한 다음 **데이터 팩터리**를 선택합니다. 
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
   !["새 데이터 팩터리" 페이지](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: **&lt;yourname&gt;ADFTutorialDataFactory**) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
   ![이름을 사용할 수 없는 경우 오류](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. **구독**에 대해 데이터 팩터리를 만들려는 위치에 Azure 구독을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
   - **기존 항목 사용**을 선택하고 목록에서 기존 리소스 그룹을 선택합니다. 
   - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
   리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. **위치**에 데이터 팩터리의 위치를 선택합니다.

   목록은 데이터 팩터리에서 지원하는 위치만 표시합니다. 데이터 팩터리에서 사용하는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(Azure HDInsight 등)은 다른 위치에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 선택합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다. 

   !["데이터 팩터리 배포 중" 타일](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. 만들기가 완료되면 **데이터 팩터리** 페이지가 표시됩니다. **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스) 응용 프로그램을 시작합니다.
   
   !["작성 및 모니터링" 타일이 있는 데이터 팩터리에 대한 홈페이지](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. **시작하기** 페이지에서 왼쪽 패널의 **편집** 탭으로 전환합니다. 

    !["시작하기" 페이지](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>연결된 서비스 만들기
이 절차에서는 Azure 저장소 계정을 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다.

1. **연결**을 선택한 다음 도구 모음에서 **새로 만들기** 단추를 선택합니다. 

   ![새 연결 만들기에 대한 단추](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. **새 연결된 서비스** 페이지에서 **Azure Blob Storage**를 선택한 다음 **계속**을 선택합니다. 

   !["Azure Blob Storage" 타일 선택](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
3. 다음 단계를 완료합니다. 

   a. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다.

   나. **저장소 계정 이름**에 대해 Azure 저장소 계정의 이름을 선택합니다.

   다. **연결 테스트**를 선택하여 데이터 팩터리 서비스에서 저장소 계정에 연결할 수 있는지 확인합니다. 

   d. **저장**을 선택하여 연결된 서비스를 저장합니다. 

   ![Azure Storage 연결된 서비스 설정](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
4. 연결된 서비스 목록에서 **AzureStorageLinkedService**가 표시되는지 확인합니다. 

   ![목록의 Azure Storage 연결된 서비스](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>데이터 집합 만들기
이 절차에서는 **InputDataset** 및 **OutputDataset**라는 두 개의 데이터 집합을 만듭니다. 이러한 데이터 집합은 **AzureBlob** 유형입니다. 이 데이터 집합은 이전 섹션에서 만든 Azure Storage 연결된 서비스를 참조합니다. 

입력 데이터 집합은 입력 폴더의 원본 데이터를 나타냅니다. 입력 데이터 집합 정의에서 원본 데이터가 포함된 Blob 컨테이너(**adftutorial**), 폴더(**input**) 및 파일(**emp.txt**)을 지정합니다. 

출력 데이터 집합은 대상에 복사되는 데이터를 나타냅니다. 출력 데이터 집합 정의에서 Blob 컨테이너(**adftutorial**), 폴더(**output**) 및 데이터가 복사될 파일을 지정합니다. 파이프라인의 각 실행에는 연결된 고유 ID가 있습니다. 시스템 변수 **RunId**를 사용하여 이 ID에 액세스할 수 있습니다. 출력 파일의 이름은 파이프라인의 실행 ID에 따라 동적으로 평가됩니다.   

연결된 서비스 설정에서 원본 데이터가 포함된 Azure 저장소 계정을 지정했습니다. 원본 데이터 집합 설정에서 원본 데이터가 있는 정확한 위치(Blob 컨테이너, 폴더 및 파일)를 지정합니다. 싱크 데이터 집합 설정에서 데이터가 복사될 위치(Blob 컨테이너, 폴더 및 파일)를 지정합니다. 
 
1. **+**(더하기) 단추를 선택한 다음 **데이터 집합**을 선택합니다.

   ![데이터 집합 만들기에 대한 메뉴](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. **새 데이터 집합** 페이지에서 **Azure Blob Storage**를 선택한 다음 **마침**을 선택합니다. 

   !["Azure Blob Storage" 선택](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. 데이터 집합의 **속성** 창에서 **이름**에 대해 **InputDataset**를 입력합니다. 

   ![데이터 집합 일반 설정](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. **연결** 탭으로 전환하고 다음 단계를 완료합니다. 

   a. **연결된 서비스**에 대해 **AzureStorageLinkedService**를 선택합니다.

   나. **파일 경로**에 대해 **찾아보기** 단추를 선택합니다.

      !["연결" 탭 및 "찾아보기" 단추](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. **파일 또는 폴더 선택** 창에서 **adftutorial** 컨테이너의 **input** 폴더로 이동하여 **emp.txt** 파일을 선택한 다음 **마침**을 선택합니다.

      ![입력 파일에 대한 찾아보기](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (선택 사항) **데이터 미리 보기**를 선택하여 emp.txt 파일의 데이터를 미리 봅니다.     
5. 단계를 반복하여 출력 데이터 집합을 만듭니다.  

   a. **+**(더하기) 단추를 선택한 다음 **데이터 집합**을 선택합니다.

   나. **새 데이터 집합** 페이지에서 **Azure Blob Storage**를 선택한 다음 **마침**을 선택합니다.

   다. 이름에 대해 **OutputDataset**를 지정합니다.

   d. 폴더에 대해 **adftutorial/output**을 입력합니다. **출력** 폴더가 존재하지 않는 경우 복사 작업은 런타임 시 해당 폴더를 만듭니다.

   e. 파일 이름에 대해 `@CONCAT(pipeline().RunId, '.txt')`를 입력합니다. 
   
      파이프라인을 실행할 때마다 파이프라인 실행에는 이와 연결된 고유 ID가 있습니다. 식에서 파이프라인의 실행 ID를 **.txt**와 연결하여 출력 파일 이름을 평가합니다. 지원되는 시스템 변수 및 식에 대한 목록은 [시스템 변수](control-flow-system-variables.md) 및 [식 언어](control-flow-expression-language-functions.md)를 참조하세요.

   ![출력 데이터 집합 설정](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다. 
이 절차에서는 입력 및 출력 데이터 집합을 사용하는 복사 활동이 있는 파이프라인을 만들고 유효성을 검사합니다. 복사 작업은 입력 데이터 집합 설정에 지정된 파일의 데이터를 출력 데이터 집합 설정에 지정된 파일로 복사합니다. 입력 데이터 집합에서 폴더(파일 이름 제외)만 지정하면 복사 활동에서 원본 폴더의 모든 파일을 대상에 복사합니다. 

1. **+**(더하기) 단추를 선택한 다음 **파이프라인**을 선택합니다. 

   ![새 파이프라인 만들기에 대한 메뉴](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. **속성** 창에서 **이름**에 대해 **CopyPipeline**을 지정합니다. 

   ![파이프라인 일반 설정](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. **활동** 도구 상자에서 **데이터 흐름**을 확장합니다. **복사** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. 또한 **활동** 도구 상자에서 활동을 검색할 수도 있습니다. **이름**에 대해 **CopyFromBlobToBlob**을 지정합니다.

   ![복사 활동 일반 설정](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. 복사 활동 설정의 **원본** 탭으로 전환하고, **원본 데이터 집합**에 대해 **InputDataset**를 선택합니다.

   ![복사 활동 원본 설정](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. 복사 활동 설정의 **싱크** 탭으로 전환하고, **싱크 데이터 집합**에 대해 **OutputDataset**를 선택합니다.

   ![복사 활동 싱크 설정](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. **유효성 검사**를 선택하여 파이프라인 설정에 대한 유효성을 검사합니다. 파이프라인에 대한 유효성이 성공적으로 검사되었는지 확인합니다. 유효성 검사 출력을 닫으려면 **>>**(오른쪽 화살표) 단추를 선택합니다. 

   ![파이프라인 유효성 검사](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>파이프라인 실행 테스트
이 단계에서는 데이터 팩터리에 파이프라인을 배포하기 전에 파이프라인을 실행 테스트합니다. 

1. 파이프라인에 대한 도구 모음에서 **실행 테스트**를 선택합니다. 
    
   ![파이프라인 실행 테스트](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. 파이프라인 설정의 **출력** 탭에서 파이프라인 실행 상태가 표시되는지 확인합니다. 

   ![실행 테스트 출력](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. **adftutorial** 컨테이너의 **outputfolder** 폴더에 있는 출력 파일이 표시되는지 확인합니다. 출력 폴더가 없으면 Data Factory 서비스에서 자동으로 만듭니다. 
    
   ![출력 확인](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>수동으로 파이프라인 트리거
이 절차에서는 엔터티(연결된 서비스, 데이터 집합, 파이프라인)를 Azure Data Factory에 배포합니다. 그런 다음 파이프라인 실행을 수동으로 트리거합니다. 또한 [다른 자습서](tutorial-copy-data-portal.md?#configure-code-repository)에서 설명하는 사용자 고유의 Visual Studio Team Services GIT 리포지토리에 엔터티를 게시할 수도 있습니다.

1. 파이프라인을 트리거하기 전에 엔터티를 데이터 팩터리에 게시해야 합니다. 게시하려면 왼쪽 창에서 **모두 게시**를 선택합니다. 

   ![게시 단추](./media/quickstart-create-data-factory-portal/publish-button.png)
2. 파이프라인을 수동으로 트리거하려면 도구 모음에서 **트리거**를 선택한 다음 **지금 트리거**를 선택합니다. 
    
   !["지금 트리거" 명령](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다.

   !["새로 고침" 단추가 있는 파이프라인 실행 모니터링에 대한 탭](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. **작업** 아래에서 **활동 실행 보기** 링크를 선택합니다. 이 페이지에서 복사 활동의 상태가 표시됩니다. 

   ![파이프라인 활동 실행](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. 복사 활동에 대한 세부 정보를 보려면 **작업** 열에서 **세부 정보**(안경 이미지) 링크를 선택합니다. 속성에 대한 자세한 내용은 [복사 활동 개요](copy-activity-overview.md)를 참조하세요. 

   ![복사 작업 세부 정보](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. **output** 폴더에 있는 새 파일이 표시되는지 확인합니다. 
5. **파이프라인** 링크를 선택하여 **활동 실행** 보기에서 **파이프라인 실행** 보기로 다시 전환할 수 있습니다. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>일정에 따라 파이프라인 트리거
이 절차는 이 자습서에서 선택 사항입니다. *스케줄러 트리거*를 만들어 파이프라인이 주기적으로(매시간, 매일 등) 실행되도록 예약할 수 있습니다. 이 절차에서는 지정한 종료 날짜 및 시간까지 매분마다 실행하는 트리거를 만듭니다. 

1. **편집** 탭으로 전환합니다. 

   ![편집 단추](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. 메뉴에서 **트리거**를 선택한 다음 **새로 만들기/편집**을 선택합니다. 

   ![새 트리거에 대한 메뉴](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. **트리거 추가** 페이지에서 **트리거 선택**을 선택한 다음 **새로 만들기**를 선택합니다. 

   ![새 트리거 추가에 대한 선택 항목](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. **새 트리거** 페이지의 **종료** 아래에서 **날짜**를 선택하고, 종료 시간을 현재 시간 이후 몇 분으로 지정하고, **적용**을 선택합니다. 

   비용은 각 파이프라인 실행과 관련되어 있으므로 종료 시간은 시작 시간과는 별도로 분 단위로만 지정합니다. 같은 날인지 확인합니다. 그러나 게시 시간과 종료 시간 사이에 파이프라인이 실행되는 데 충분한 시간이 있는지 확인합니다. 트리거는 UI에 트리거를 저장할 때가 아니라 Data Factory에 솔루션을 게시한 후에만 적용됩니다. 

   ![트리거 설정](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. **새 트리거** 페이지에서 **활성화됨** 확인란을 선택한 후 **다음**을 선택합니다. 

   !["활성화됨" 확인란 및 "다음" 단추](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. 경고 메시지를 검토하고 **마침**을 선택합니다.

   ![경고 및 "마침" 단추](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. **모두 게시**를 선택하여 변경 내용을 Data Factory에 게시합니다. 

   ![게시 단추](./media/quickstart-create-data-factory-portal/publish-button.png)
8. 왼쪽의 **모니터** 탭으로 전환합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 파이프라인이 게시 시간에서 종료 시간까지 매분 한 번씩 실행되는 것을 볼 수 있습니다. 

   **트리거 기준** 열의 값을 확인합니다. 수동 트리거 실행은 이전 단계(**지금 트리거**)에서 수행되었습니다. 

   ![트리거된 실행 목록](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. **파이프라인 실행** 옆의 아래쪽 화살표를 선택하여 **트리거 실행** 보기로 전환합니다. 

   !["트리거 실행" 보기로 전환](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. 지정된 종료 날짜 및 시간까지의 모든 파이프라인 실행에 대한 출력 파일이 **output** 폴더에 만들어졌는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. 더 많은 시나리오에서의 데이터 팩터리 사용에 대해 알아보려면 [자습서](tutorial-copy-data-portal.md)를 따릅니다. 