---
title: Azure Data Factory 파이프라인 분기 | Microsoft Docs
description: 분기 및 연결 작업을 통해 Azure Data Factory에서 데이터 흐름을 제어하는 방법을 알아봅니다.
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
ms.openlocfilehash: 65441882827ecb26405f74fb1389b6a21d99cf9c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory 파이프라인 분기 및 연결 작업
이 자습서에서는 몇 가지 컨트롤 흐름 기능을 보여 주는 Data Factory 파이프라인을 만듭니다. 이 파이프라인은 Azure Blob Storage의 컨테이너에서 동일한 저장소 계정의 다른 컨테이너로 간단한 복사를 수행합니다. 복사 활동이 성공하면 파이프라인에서 성공적인 복사 작업에 대한 세부 정보(예: 기록된 데이터 양)를 성공 전자 메일에 보냅니다. 복사 활동이 실패하면 파이프라인에서 실패한 복사 작업에 대한 세부 정보(예: 오류 메시지)를 실패 전자 메일에 보냅니다. 자습서 전체에서 매개 변수를 전달하는 방법을 확인할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

대략적인 시나리오 개요: ![개요](media/tutorial-control-flow-portal/overview.png)

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 집합 만들기
> * 복사 활동 및 웹 활동이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 활용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이 자습서에서는 Azure Portal을 사용합니다. 다른 메커니즘을 사용하여 Azure Data Factory와 상호 작용할 수 있습니다. 이 경우 목차에서 "빠른 시작"을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* **Azure Storage 계정**. Blob 저장소를 **원본** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md#create-a-storage-account) 문서를 참조하세요.
* **Azure SQL Database**. 데이터베이스를 **싱크** 데이터 저장소로 사용합니다. 아직 없는 경우 Azure SQL Database를 만드는 단계는 [Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서를 참조하세요.

### <a name="create-blob-table"></a>Blob 테이블 만들기

1. 메모장을 시작합니다. 다음 텍스트를 복사하여 **input.txt** 파일로 디스크에 저장합니다.

    ```
    John,Doe
    Jane,Doe
    ```
2. [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 다음 단계를 수행합니다. 
    1. **adfv2branch** 컨테이너를 만듭니다.
    2. **adfv2branch** 컨테이너에 **input** 폴더를 만듭니다.
    3. **input.txt** 파일을 컨테이너에 업로드합니다.

## <a name="create-email-workflow-endpoints"></a>전자 메일 워크플로 끝점 만들기
파이프라인에서 전자 메일 보내기를 트리거하려면 [Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 워크플로를 정의합니다. 논리 앱 워크플로를 만드는 방법에 대한 자세한 내용은 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 참조하세요. 

### <a name="success-email-workflow"></a>성공 전자 메일 워크플로 
`CopySuccessEmail`이라는 논리 앱 워크플로를 만듭니다. 워크플로 트리거를 `When an HTTP request is received`로 정의하고 `Office 365 Outlook – Send an email` 작업을 추가합니다.

![성공 전자 메일 워크플로](media/tutorial-control-flow-portal/success-email-workflow.png)

요청 트리거의 경우 `Request Body JSON Schema`를 다음 JSON으로 채웁니다.

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Logic Apps 디자이너에서 요청은 다음 이미지와 같이 표시됩니다. 

![Logic Apps 디자이너 - 요청](media/tutorial-control-flow-portal/logic-app-designer-request.png)

**전자 메일 보내기** 작업의 경우 요청 본문 JSON 스키마에 전달된 속성을 활용하여 전자 메일의 형식을 지정하는 방법을 사용자 지정합니다. 다음은 예제입니다.

![Logic Apps 디자이너 - 전자 메일 작업 보내기](media/tutorial-control-flow-portal/send-email-action-2.png)

워크플로를 저장합니다. 성공 전자 메일 워크플로에 대한 HTTP Post 요청 URL을 적어 둡니다.

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>실패 전자 메일 워크플로 
동일한 단계에 따라 **CopyFailEmail**의 다른 Logic Apps 워크플로를 만듭니다. 요청 트리거에서 `Request Body JSON schema`은 동일합니다. `Subject`와 같은 전자 메일의 형식을 변경하여 실패 전자 메일에 맞게 조정합니다. 다음은 예제입니다.

![Logic Apps 디자이너 - 실패 전자 메일 워크플로](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

워크플로를 저장합니다. 실패 전자 메일 워크플로에 대한 HTTP Post 요청 URL을 적어 둡니다.

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

이제 두 개의 워크플로 URL이 있습니다.

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-control-flow-portal/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-control-flow-portal/new-azure-data-factory.png)
 
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

    ![데이터 팩터리 배포 중 타일](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.


## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 단계에서는 하나의 복사 활동과 두 개의 웹 활동이 있는 파이프라인을 만듭니다. 다음 기능을 사용하여 파이프라인을 만듭니다.

- 데이터 집합에서 액세스하는 파이프라인에 대한 매개 변수 
- Logic Apps 워크플로를 호출하여 성공/실패 전자 메일을 보내는 웹 활동 
- 하나의 활동을 다른 활동과 연결(성공 및 실패 시)
- 작업의 출력을 후속 작업에 대한 입력으로 사용

1. Data Factory UI의 **시작** 페이지에서 **파이프라인 만들기** 타일을 클릭합니다.  

   ![시작 페이지](./media/tutorial-control-flow-portal/get-started-page.png) 
3. 파이프라인에 대한 속성 창에서 **매개 변수**탭으로 전환하고, **새로 만들기** 단추를 사용하여 문자열 형식의 세 매개 변수인 sourceBlobContainer, sinkBlobContainer 및 receiver를 추가합니다. 

    - **sourceBlobContainer** - 원본 Blob 데이터 집합에서 사용하는 파이프라인의 매개 변수입니다.
    - **sinkBlobContainer** - 싱크 Blob 데이터 집합에서 사용하는 파이프라인의 매개 변수입니다.
    - **receiver** – 이 매개 변수로 지정된 이메일 주소의 받는 사람에게 성공 또는 실패 전자 메일을 보내는 파이프라인의 두 개의 웹 활동에서 사용됩니다.

   ![새 파이프라인 메뉴](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. **활동** 도구 상자에서 **데이터 흐름**을 펼치고, **복사** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

   ![복사 활동 끌어서 놓기](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. 아래쪽의 **복사** 활동에 대한 **속성** 창에서 **원본** 탭으로 전환하고, **+ 새로 만들기**를 클릭합니다. 이 단계에서는 복사 활동에 대한 원본 데이터 집합을 만듭니다. 

   ![원본 데이터 집합](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. **새 데이터 집합** 창에서 **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다. 

   ![Azure Blob Storage 선택](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. **AzureBlob1**이라는 제목의 새 **탭**이 표시됩니다. 데이터 집합의 이름을 **SourceBlobDataset**로 변경합니다.

   ![데이터 집합 일반 설정](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. **속성** 창에서 **연결** 탭으로 전환하고, **연결된 서비스**에 대한 새로 만들기를 클릭합니다. 이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 
    
   ![데이터 집합 연결 - 새 연결된 서비스](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. **새 연결된 서비스** 창에서 다음 단계를 수행합니다. 

    1. **이름**에 대해 **AzureStorageLinkedService**를 입력합니다.
    2. **저장소 계정 이름**에 대해 Azure 저장소 계정을 선택합니다.
    3. **저장**을 클릭합니다.

   ![새 Azure Storage 연결된 서비스](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. 폴더에는 `@pipeline().parameters.sourceBlobContainer`를 입력하고, 파일 이름에는 `emp.txt`를 입력합니다. sourceBlobContainer 파이프라인 매개 변수를 사용하여 데이터 집합의 폴더 경로를 설정합니다. 

    ![원본 데이터 집합 설정](./media/tutorial-control-flow-portal/source-dataset-settings.png)
13. **파이프라인** 탭으로 전환하거나 트리 뷰에서 파이프라인을 클릭합니다. **Source Dataset**에 대해 **SourceBlobDataset**가 선택되어 있는지 확인합니다. 

   ![원본 데이터 집합](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)
13. 속성 창에서 **싱크** 탭으로 전환하고, **싱크 데이터 집합**에 대해 **+ 새로 만들기**를 클릭합니다. 이 단계에서는 원본 데이터 집합을 만드는 것과 비슷한 방식으로 복사 활동에 대한 싱크 데이터 집합을 만듭니다. 

    ![새 싱크 데이터 집합 단추](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. **새 데이터 집합** 창에서 **Azure Blob Storage**를 선택하고 **마침**을 클릭합니다. 
15. 데이터 집합에 대한 **일반** 설정 페이지에서 **이름**에 대해 **SinkBlobDataset**를 입력합니다.
16. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **LinkedService**에 대해 **AzureStorageLinkedService**를 선택합니다.
    2. 폴더에 대해 `@pipeline().parameters.sinkBlobContainer`를 입력합니다.
    1. 파일 이름에 대해 `@CONCAT(pipeline().RunId, '.txt')`를 입력합니다. 이 식은 파일 이름에 대해 현재 파이프라인 실행의 ID를 사용합니다. 지원되는 시스템 변수 및 식에 대한 목록은 [시스템 변수](control-flow-system-variables.md) 및 [식 언어](control-flow-expression-language-functions.md)를 참조하세요.

        ![싱크 데이터 집합 설정](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. 위쪽의 **파이프라인** 탭으로 전환합니다. **활동** 도구 상자에서 **일반**을 펼치고, **웹** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **SendSuccessEmailActivity**로 설정합니다. 웹 작업은 모든 REST 끝점에 대한 호출을 허용합니다. 작업에 대한 자세한 내용은 [웹 작업](control-flow-web-activity.md)을 참조하세요. 이 파이프라인은 웹 작업을 사용하여 Logic Apps 전자 메일 워크플로를 호출합니다. 

   ![첫 번째 웹 활동 끌어서 놓기](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. **일반** 탭에서 **설정** 탭으로 전환하고 다음 단계를 수행합니다. 
    1. **URL**에 대해 성공 전자 메일을 보내는 Logic Apps 워크플로에 대한 URL을 지정합니다.  
    2. **메서드**에 대해 **POST**를 선택합니다. 
    3. **헤더** 섹션에서 **+ 헤더 추가** 링크를 클릭합니다. 
    4. **Content-Type** 헤더를 추가하고 **application/json**으로 설정합니다. 
    5. **본문**에 대해 다음 JSON을 지정합니다. 

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        메시지 본문에 포함되는 속성은 다음과 같습니다.

        - 메시지 - `@{activity('Copy1').output.dataWritten`의 값을 전달합니다. 이전 복사 작업의 속성에 액세스하고 dataWritten 값을 전달합니다. 실패의 경우 `@{activity('CopyBlobtoBlob').error.message` 대신 오류 출력을 전달합니다.
        - 데이터 팩터리 이름 - `@{pipeline().DataFactory}`의 값을 전달합니다. 이 변수는 시스템 변수이며, 해당 데이터 팩터리 이름에 액세스할 수 있게 합니다. 시스템 변수 목록은 [시스템 변수](control-flow-system-variables.md) 문서를 참조하세요.
        - 파이프라인 이름 - `@{pipeline().Pipeline}`의 값을 전달합니다. 이는 시스템 변수이기도 하므로 해당 파이프라인 액세스할 수 있게 합니다. 
        - 받는 사람 - "@pipeline().parameters.receiver")의 값을 전달합니다. 파이프라인 매개 변수에 액세스합니다.
    
        ![첫 번째 웹 활동에 대한 설정](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. 복사 활동 옆의 녹색 단추를 끌어 웹 활동에 놓아서 **복사** 활동을 **웹** 활동에 연결합니다. 

    ![첫 번째 웹 활동 및 복사 활동 연결](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. 다른 **웹** 활동을 [활동] 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓고, **이름**을 **SendFailureEmailActivity**로 설정합니다.

    ![두 번째 웹 활동 이름](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. **설정** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **URL**에 대해 실패 전자 메일을 보내는 Logic Apps 워크플로에 대한 URL을 지정합니다.  
    2. **메서드**에 대해 **POST**를 선택합니다. 
    3. **헤더** 섹션에서 **+ 헤더 추가** 링크를 클릭합니다. 
    4. **Content-Type** 헤더를 추가하고 **application/json**으로 설정합니다. 
    5. **본문**에 대해 다음 JSON을 지정합니다. 

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![두 번째 웹 활동에 대한 설정](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. 파이프라인 디자이너에서 **복사** 활동을 선택하고, **+->** 단추를 클릭하고, **오류**를 선택합니다.  

    ![두 번째 웹 활동에 대한 설정](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. 복사 활동 옆의 **빨간색** 단추를 두 번째 웹 활동인 **SendFailureEmailActivity**로 끕니다. 파이프라인이 다음 이미지와 같이 표시되도록 활동을 이동할 수 있습니다. 

    ![모든 활동이 포함된 전체 파이프라인](./media/tutorial-control-flow-portal/full-pipeline.png)
24. 파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사** 단추를 클릭합니다. **>>** 단추를 클릭하여 **파이프라인 유효성 검사 출력** 창을 닫습니다.

    ![파이프라인 유효성 검사](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. 엔터티(데이터 집합, 파이프라인 등)를 Data Factory 서비스에 게시하려면 **모두 게시**를 선택합니다. **게시됨** 메시지가 표시될 때까지 기다립니다.

    ![게시](./media/tutorial-control-flow-portal/publish-button.png)
 
## <a name="trigger-a-pipeline-run-that-succeeds"></a>성공한 파이프라인 실행 트리거
1. 파이프라인 실행을 **트리거**하려면 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 

    ![파이프라인 실행 트리거](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. **파이프라인 실행** 창에서 다음 단계를 수행 합니다. 

    1. **sourceBlobContainer** 매개 변수에 대해 **adftutorial/adfv2branch/input**을 입력합니다. 
    2. **sinkBlobContainer** 매개 변수에 대해 **adftutorial/adfv2branch/output**을 입력합니다. 
    3. **받는 사람**의 **이메일 주소**를 입력합니다. 
    4. **마침**을 클릭합니다.

        ![파이프라인 실행 매개 변수](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>성공적인 파이프라인 실행 모니터링

1. 파이프라인 실행을 모니터링하려면 왼쪽의 **모니터** 탭으로 전환합니다. 수동으로 트리거된 파이프라인 실행이 표시됩니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다. 
    
    ![성공적인 파이프라인 실행](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. 이 파이프라인 실행과 연결된 **활동 실행을 보려면** **작업** 열에서 첫 번째 링크를 클릭합니다. 위쪽의 **파이프라인**을 클릭하여 이전 보기로 다시 전환할 수 있습니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다. 

    ![작업 실행](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>실패한 파이프라인 실행 트리거
1. 왼쪽의 **편집** 탭으로 전환합니다. 
2. 파이프라인 실행을 **트리거**하려면 도구 모음에서 **트리거**, **지금 트리거**를 차례로 클릭합니다. 
3. **파이프라인 실행** 창에서 다음 단계를 수행 합니다. 

    1. **sourceBlobContainer** 매개 변수에 대해 **adftutorial/dummy/input**을 입력합니다. 더미 폴더가 adftutorial 컨테이너에 존재하지 않는지 확인합니다. 
    2. **sinkBlobContainer** 매개 변수에 대해 **adftutorial/dummy/output**을 입력합니다. 
    3. **받는 사람**의 **이메일 주소**를 입력합니다. 
    4. **Finish**를 클릭합니다.

## <a name="monitor-the-failed-pipeline-run"></a>실패한 파이프라인 실행 모니터링

1. 파이프라인 실행을 모니터링하려면 왼쪽의 **모니터** 탭으로 전환합니다. 수동으로 트리거된 파이프라인 실행이 표시됩니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다. 
    
    ![실패한 파이프라인 실행](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. 오류에 대한 세부 정보를 보려면 파이프라인 실행에 대한 **오류** 링크를 클릭합니다. 

    ![파이프라인 오류](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. 이 파이프라인 실행과 연결된 **활동 실행을 보려면** **작업** 열에서 첫 번째 링크를 클릭합니다. **새로 고침** 단추를 사용하여 목록을 새로 고칩니다. 파이프라인의 복사 활동이 실패했음을 확인할 수 있습니다. 웹 활동에서 지정된 받는 사람에게 실패 전자 메일을 보내는 데 성공했습니다. 

    ![작업 실행](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. **작업** 열에서 **오류** 링크를 클릭하여 오류에 대한 세부 정보를 확인합니다. 

    ![활동 실행 오류](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 집합 만들기
> * 복사 작업 및 웹 작업이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 활용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이제 개념 섹션으로 진행하여 Azure Data Factory에 대한 자세한 내용을 확인할 수 있습니다.
> [!div class="nextstepaction"]
>[파이프라인 및 활동](concepts-pipelines-activities.md)
