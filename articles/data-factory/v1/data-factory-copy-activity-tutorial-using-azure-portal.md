---
title: '자습서: 데이터를 복사하는 Azure Data Factory 파이프라인 만들기 (Azure Portal) | Microsoft Docs'
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4e8de6b229fe027e43a2b13f2a7ad54b7c3eaf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>자습서: Azure Portal을 사용하여 데이터를 복사하는 Data Factory 파이프라인 만들기 
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> 이 문서는 GA(일반 공급) 상태인 Data Factory 버전 1에 적용됩니다. 미리 보기에 있는 Data Factory 서비스 버전 2를 사용하는 경우 [버전 2 설명서의 복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대해 알아봅니다. Azure Data Factory를 처음 사용하는 경우 이 자습서를 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.   

이 자습서에는 한 가지 작업 즉, 복사 작업이 포함된 파이프라인을 만듭니다. 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요. 

> [!NOTE] 
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하도록 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 자습서를 수행하기 전에 [자습서 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 문서에서 나열하는 필수 구성 요소를 완료합니다.

## <a name="steps"></a>단계
이 자습서의 일부로 수행하는 단계는 다음과 같습니다.

1. Azure **데이터 팩터리**를 만듭니다. 이 단계에서는 ADFTutorialDataFactory라는 데이터 팩터리를 만듭니다. 
2. 데이터 팩터리에서 **연결된 서비스**를 만듭니다. 이 단계에서는 두 가지 연결된 서비스 유형, 즉 Azure Storage와 Azure SQL Database를 만듭니다. 
    
    AzureStorageLinkedService는 Azure 저장소 계정을 데이터 팩터리에 연결합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 컨테이너를 만들고 이 저장소 계정에 데이터를 업로드했습니다.   

    AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. Blob 저장소에서 복사된 데이터는 이 데이터베이스에 저장됩니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 SQL 테이블을 만들었습니다.   
3. 데이터 팩터리에서 입력 및 출력 **데이터 집합**을 만듭니다.  
    
    Azure 저장소 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure 저장소 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 집합은 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

    마찬가지로 Azure SQL Database 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 출력 SQL 테이블 데이터 집합은 Blob 저장소의 데이터가 복사되는 데이터베이스의 테이블을 지정합니다.
4. 데이터 팩터리에서 **파이프라인**을 만듭니다. 이 단계에서는 복사 활동을 사용하여 파이프라인을 만듭니다.   
    
    복사 활동은 Azure Blob 저장소의 Blob에서 Azure SQL 데이터베이스의 테이블로 데이터를 복사합니다. 파이프라인의 복사 활동을 사용하여 지원되는 모든 원본의 데이터를 지원되는 모든 대상으로 복사할 수 있습니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 문서를 참조하세요. 
5. 파이프라인을 모니터링합니다. 이 단계에서는 Azure Portal을 사용하여 입력 및 출력 데이터 집합의 조각을 **모니터링**합니다. 

## <a name="create-data-factory"></a>데이터 팩터리 만들기
> [!IMPORTANT]
> 아직 완료하지 않은 경우 [자습서의 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 완료합니다.   

데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 제품 출력 데이터로 변환하는 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 이 단계에서는 데이터 팩터리 만들기를 시작하겠습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인한 후 왼쪽 메뉴에서 **리소스 만들기**를 클릭하고 **데이터 + 분석**을 클릭한 다음, **Data Factory**를 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
   
   1. **ADFTutorialDataFactory**를 **이름**으로 입력합니다. 
      
         ![새 데이터 팩터리 블레이드](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![데이터 팩터리 이름을 사용할 수 없음](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
   3. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
      
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
          이 자습서의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup** 이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
   4. 데이터 팩터리의 **위치** 를 선택합니다. Data Factory 서비스에서 지원하는 지역만 드롭다운 목록에 표시됩니다.
   5. **대시보드에 고정**을 선택합니다.     
   6. **만들기**를 클릭합니다.
      
      > [!IMPORTANT]
      > 데이터 팩터리 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [데이터 팩터리 참여자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.
      > 
      > 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.                
      > 
      > 
3. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. 만들기가 완료되면 이미지와 같이 **Data Factory** 블레이드가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 계산 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure HDInsight 또는 Azure Data Lake Analytics와 같은 계산 서비스를 사용하지 않습니다. Azure Storage(원본) 및 Azure SQL Database(대상) 유형의 두 데이터 저장소를 사용합니다. 

이에 따라 두 개의 연결된 서비스, 즉 AzureStorage와 AzureSqlDatabase 유형의 AzureStorageLinkedService와 AzureSqlLinkedService를 만듭니다.  

AzureStorageLinkedService는 Azure 저장소 계정을 데이터 팩터리에 연결합니다. 이 저장소 계정은 컨테이너를 만들고 [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 데이터를 업로드한 계정입니다.   

AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. Blob 저장소에서 복사된 데이터는 이 데이터베이스에 저장됩니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다.  

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
이 단계에서는 Azure 저장소 계정을 데이터 팩터리에 연결합니다. 이 섹션의 Azure 저장소 계정 이름 및 키를 지정합니다.  

1. **Data Factory** 블레이드에서 **작성자 및 배포** 타일을 클릭합니다.
   
   ![작성 및 배포 타일](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. 다음 이미지와 같이 **Data Factory Editor**가 표시됩니다. 

    ![데이터 팩터리 편집기](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. 편집기의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고, 드롭다운 메뉴에서 **Azure 저장소**를 선택합니다. 오른쪽 창에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다. 
   
    ![편집기 새 데이터 저장소 단추](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. `<accountname>` 및 `<accountkey>`를 Azure Storage 계정의 계정 이름 및 계정 키 값으로 바꿉니다. 
   
    ![편집기 Blob Storage JSON](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. 도구 모음에서 **배포** 를 클릭합니다. 이제 트리 보기에서 배포된 **AzureStorageLinkedService** 가 표시됩니다. 
   
    ![편집기 Blob Storage 배포](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    연결된 서비스 정의의 JSON 속성에 대한 자세한 내용은 [Azure Blob Storage 커넥터](data-factory-azure-blob-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Azure SQL Database에 대한 연결된 서비스 만들기
이 단계에서는 Azure SQL 데이터베이스를 데이터 팩터리에 연결합니다. 이 섹션에서 Azure SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 사용자 암호를 지정합니다. 

1. **Data Factory 편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **Azure SQL Database**를 선택합니다. 오른쪽 창에 Azure SQL 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다.
2. `<servername>`, `<databasename>`, `<username>@<servername>` 및 `<password>`를 Azure SQL Server의 이름, 사용자 계정 및 암호로 바꿉니다. 
3. 도구 모음에서 **배포**를 클릭하여 **AzureSqlLinkedService**를 만들고 배포합니다.
4. **연결된 서비스** 아래의 트리 보기에서 **AzureSqlLinkedService**가 표시되는지 확인합니다.  

    이러한 JSON 속성에 대한 자세한 내용은 [Azure SQL Database 커넥터](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.

## <a name="create-datasets"></a>데이터 집합 만들기
이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 팩터리에 연결하는 연결된 서비스를 만들었습니다. 이 단계에서는 AzureStorageLinkedService 및 AzureSqlLinkedService에서 각각 참조하는 데이터 저장소에 저장된 입력 및 출력 데이터를 나타내는 InputDataset 및 OutputDataset이라는 두 개의 데이터 집합을 정의합니다.

Azure 저장소 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure 저장소 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 집합(InputDataset)은 입력 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

마찬가지로 Azure SQL Database 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 출력 SQL 테이블 데이터 집합(OututDataset)은 Blob 저장소의 데이터가 복사되는 데이터베이스의 테이블을 지정합니다. 

### <a name="create-input-dataset"></a>입력 데이터 집합 만들기
이 단계에서는 AzureStorageLinkedService 연결된 서비스에서 나타내는 Azure Storage의 Blob 컨테이너(adftutorial)의 루트 폴더에 있는 Blob 파일(emp.txt)을 가리키는 InputDataset이라는 데이터 집합을 만듭니다. fileName 값을 지정하지 않거나 건너뛰면 입력 폴더에 있는 모든 Blob의 데이터가 대상에 복사됩니다. 이 자습서에서는 fileName 값을 지정합니다. 

1. Data Factor의 **편집기**에서 **... 추가**를 클릭하고 **새 데이터 집합**을 클릭하고 드롭 다운 메뉴에서 **Azure Blob Storage**를 클릭합니다. 
   
    ![새 데이터 집합 메뉴](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다. 
   
    ```json
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 자산 | 설명 |
    |:--- |:--- |
    | 형식 | Azure Blob 저장소에 데이터가 있기 때문에 type 속성은 **AzureBlob**으로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureStorageLinkedService**를 참조합니다. |
    | folderPath | 입력 Blob이 포함된 Blob **컨테이너**와 **폴더**를 지정합니다. 이 자습서에서 adftutorial은 Blob 컨테이너이며, 폴더는 루트 폴더입니다. | 
    | fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일이 선택됩니다. 이 자습서에서는 fileName에 대해 **emp.txt**를 지정하므로 해당 파일만 처리를 위해 선택됩니다. |
    | format -> type |입력 파일은 텍스트 형식이므로 **TextFormat**을 사용합니다. |
    | columnDelimiter | 입력 파일의 열은 **쉼표(`,`)**로 구분됩니다. |
    | frequency/interval | frequency는 **Hour**로 설정되고, interval은 **1**로 설정됩니다. 즉 입력 조각이 **매시간** 사용될 수 있습니다. 다시 말하면, Data Factory 서비스가 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 매 시간마다 입력 데이터를 찾습니다. 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 내에 있는 데이터를 찾습니다.  |
    | external | 이 파이프라인에 의해 데이터가 생성되지 않는 경우 이 속성은 **true**로 설정됩니다. 이 자습서의 입력 데이터는 이 파이프라인에 의해 생성되지 않는 emp.txt 파일에 있으므로 이 속성을 true로 설정합니다. |

    이러한 JSON 속성에 대한 자세한 내용은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요.      
3. 도구 모음에서 **배포**를 클릭하여 **InputDataset** 데이터 집합을 만들고 배포합니다. 트리 뷰에 **InputDataset** 이 표시되는지 확인합니다.

### <a name="create-output-dataset"></a>출력 데이터 집합 만들기
Azure SQL Database 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열을 지정합니다. 이 단계에서 만든 출력 SQL 테이블 데이터 집합(OututDataset)은 Blob Storage의 데이터가 복사되는 데이터베이스의 테이블을 지정합니다.

1. Data Factor의 **편집기**에서 **... 추가**를 클릭하고 **새 데이터 집합**을 클릭하고 드롭 다운 메뉴에서 **Azure SQL**을 클릭합니다. 
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다.

    ```json   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```     

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 자산 | 설명 |
    |:--- |:--- |
    | 형식 | Azure SQL 데이터베이스의 테이블에 데이터가 복사되기 때문에 type 속성은 **AzureSqlTable**로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureSqlLinkedService**를 참조합니다. |
    | tableName | 데이터가 복사되는 **테이블**을 지정했습니다. | 
    | frequency/interval | frequency는 **Hour**로 설정되고, interval은 **1**입니다. 즉 출력 조각이 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 사이에서 **매시간** 생성됩니다.  |

    데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName**이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName**만 지정해야 합니다.

    이러한 JSON 속성에 대한 자세한 내용은 [Azure SQL 커넥터 문서](data-factory-azure-sql-connector.md#dataset-properties)를 참조하세요.
3. 도구 모음에서 **배포**를 클릭하여 **OutputDataset** 데이터 집합을 만들고 배포합니다. **데이터 집합**의 트리 보기에서 **OutputDataset**이 표시되는지 확인합니다. 

## <a name="create-pipeline"></a>파이프라인 만들기
이 단계에서는 **InputDataset**을 입력으로 사용하고 **OutputDataset**을 출력으로 사용하는 **복사 활동**을 포함한 파이프라인을 만듭니다.

현재 출력 데이터 집합은 일정을 작동하는 것입니다. 이 자습서에서는 출력 데이터 집합이 한 시간에 한 번씩 조각을 생성하도록 구성됩니다. 이 파이프라인은 하루 24시간 간격, 즉 24시간 동안에 걸친 시작 시간과 종료 시간을 갖습니다. 따라서 24개의 출력 데이터 집합이 파이프라인에 의해 생성됩니다. 

1. Data Factor의 **편집기**에서 **... 추가**를 클릭하고 **새 파이프라인**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다. 

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    다음 사항에 유의하세요.
   
    - 작업 섹션에는 **형식**이 **복사**로 설정된 작업만 있습니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. Data Factory 솔루션에서 [데이터 변환 활동](data-factory-data-transformation-activities.md)을 사용할 수도 있습니다.
    - 작업에 대한 입력을 **InputDataset**으로 설정하고 작업에 대한 출력을 **OutputDataset**으로 설정합니다. 
    - **typeProperties** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다. 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 전체 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 지원되는 특정 데이터 저장소를 원본/싱크로 사용하는 방법을 알아보려면 표에 나와 있는 링크를 클릭하세요.
    - start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들어 2016-10-14T16:32:41Z입니다. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다. **종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09**를 지정합니다.
     
    앞의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

    파이프라인 정의의 JSON 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 복사 활동 정의의 JSON 속성에 대한 설명은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. BlobSource에서 지원하는 JSON 속성에 대한 설명은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md)를 참조하세요. SqlSink에서 지원하는 JSON 속성에 대한 설명은 [Azure SQL Database 커넥터 문서](data-factory-azure-sql-connector.md)를 참조하세요.
3. 도구 모음에서 **배포**를 클릭하여 **ADFTutorialPipeline**을 만들고 배포합니다. 트리 뷰에 파이프라인이 표시되는지 확인합니다. 
4. 이제 **X**를 클릭하여 **편집기** 블레이드를 닫습니다. **X**를 다시 클릭하여 **ADFTutorialDataFactory**에 대한 **Data Factory** 홈페이지를 봅니다.

**축하합니다.** Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 파이프라인이 있는 Azure 데이터 팩터리를 성공적으로 만들었습니다. 


## <a name="monitor-pipeline"></a>파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>앱 모니터링 및 관리를 사용하여 파이프라인 모니터링
다음 단계에서는 [모니터링 및 관리] 응용 프로그램을 사용하여 데이터 팩터리의 파이프라인을 모니터링하는 방법을 보여 줍니다. 

1. 데이터 팩터리의 홈페이지에서 **모니터링 및 관리** 타일을 클릭합니다.
   
    ![타일 모니터링 및 관리](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. 별도의 탭에서 **모니터링 및 관리 응용 프로그램**이 표시되어야 합니다. 

    > [!NOTE]
    > 웹 브라우저가 "권한 부여 중..." 상태에서 갇혀 있음이 확인되면, **타사 쿠키 및 사이트 데이터 차단** 확인란의 선택을 해제하거나 **login.microsoftonline.com**에 대한 예외를 만든 다음, 앱을 다시 열어봅니다.

    ![앱 모니터링 및 관리](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. 파이프라인의 시작 시간(2017-05-11) 및 종료 시간(2017-05-12)을 포함하도록 **시작 시간** 및 **종료 시간**을 변경하고 **적용**을 클릭합니다.       
3. 중간 창의 목록에서 파이프라인의 시작 시간과 종료 시간 사이의 각 시간과 연관된 **활동 창**이 표시됩니다. 
4. 활동 창에 대한 세부 정보를 보려면 **활동 창** 목록에서 해당 활동 창을 선택합니다. 
    ![활동 창 세부 정보](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    오른쪽의 [활동 창 탐색기]에서 현재 UTC 시간(오후 8시 12분)까지의 조각이 모두 처리됩니다(녹색으로 표시됨). 오후 8-9시, 오후 9-10시, 오후 10-11시, 오후 11-12시의 조각은 아직 처리되지 않습니다.

    오른쪽 창의 **시도** 섹션은 해당 데이터 조각에 대해 실행된 작업에 대한 정보를 제공합니다. 오류가 발생한 경우 오류에 대한 세부 정보를 제공합니다. 예를 들어 입력된 폴더 또는 컨테이너가 존재하지 않고 조각 처리가 실패하면 해당 컨테이너 또는 폴더가 존재하지 않는다는 오류 메시지가 표시됩니다.

    ![활동 실행 시도](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. **SQL Server Management Studio**를 시작하고 Azure SQL Database에 연결한 다음 데이터베이스의 **emp** 테이블에 행이 삽입되었는지 확인합니다.
    
    ![SQL 쿼리 결과](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

이 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [앱 모니터링 및 관리를 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

### <a name="monitor-pipeline-using-diagram-view"></a>다이어그램 보기를 사용하여 파이프라인 모니터링
다이어그램 보기를 사용하여 데이터 파이프라인을 모니터링할 수도 있습니다.  

1. **Data Factory** 블레이드에서 **다이어그램**을 클릭합니다.
   
    ![데이터 팩터리 블레이드 - 다이어그램 타일](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. 다음 이미지와 유사한 다이어그램이 표시됩니다. 
   
    ![다이어그램 뷰](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. 다이어그램 보기에서 **InputDataset**을 두 번 클릭하여 데이터 집합의 조각을 표시합니다.  
   
    ![InputDataset을 선택한 데이터 집합](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. **자세히 보기**를 클릭하여 모든 데이터 조각을 표시합니다. 24시간의 매시간 조각 중 파이프라인의 시작 시간과 종료 시간 사이에 있는 조각이 표시됩니다. 
   
    ![모든 입력 데이터 조각](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    **emp.txt** 파일이 항상 **adftutorial\input** Blob 컨테이너에 있으므로 현재 UTC 시간까지의 모든 데이터 조각은 **준비** 상태로 있습니다. 이후 시간의 조각은 아직 준비 상태에 있지 않습니다. 맨 아래의 **Recently failed slices(최근에 실패한 조각)** 섹션에 표시되는 조각이 없는지 확인합니다.
6. 다이어그램 보기를 표시할 때까지 블레이드를 닫거나 왼쪽으로 스크롤하여 다이어그램 보기를 표시합니다. 그런 다음 **OutputDataset**을 두 번 클릭합니다. 
8. **OutputDataset**에 대한 **테이블** 블레이드에서 **자세히 보기** 링크를 클릭하여 모든 조각을 표시합니다.

    ![데이터 조각 블레이드](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. 현재 UTC 시간까지의 모든 조각이 **실행 보류 중** 상태 => **진행 중** ==> **준비** 상태로 바뀝니다. 과거(현재 시간 이전)의 조각은 기본적으로 최신 조각에서 가장 오래된 조각순으로 처리됩니다. 예를 들어 현재 시간이 오후 8시 12분 UTC인 경우 오후 7-8시 조각이 오후 6-7시 조각보다 먼저 처리됩니다. 오후 8-9시 조각은 기본적으로 시간 간격이 끝날 때, 즉 오후 9시 이후에 처리됩니다.  
10. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다. 활동 창과 연결된 데이터 부분을 조각이라고 합니다. 조각은 하나의 파일이거나 여러 파일일 수 있습니다.  
    
     ![데이터 조각 블레이드](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     조각이 **준비** 상태가 아닌 경우 **준비되지 않은 업스트림 슬라이스** 목록에서 [준비] 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.
11. **데이터 조각** 블레이드의 맨 아래 목록에 모든 작업 실행이 표시됩니다. **작업 실행**을 클릭하여 **작업 실행 세부 정보** 블레이드를 표시합니다. 
    
    ![작업 실행 세부 정보](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    이 블레이드에서 복사 작업의 소요 시간, 처리량, 읽기/쓰기된 데이터의 바이트 크기, 실행 시작 시간, 실행 종료 시간 등을 확인할 수 있습니다.  
12. **X**를 클릭하여 모든 블레이드를 닫아 **ADFTutorialDataFactory**의 홈 블레이드로 돌아옵니다.
13. (선택 사항) **데이터 집합** 타일 또는 **파이프라인** 타일을 클릭하여 이전 단계에서 표시한 블레이드를 가져옵니다. 
14. **SQL Server Management Studio**를 시작하고 Azure SQL Database에 연결한 다음 데이터베이스의 **emp** 테이블에 행이 삽입되었는지 확인합니다.
    
    ![SQL 쿼리 결과](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>요약
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure Data Factory를 만들었습니다. Azure 포털을 사용하여 데이터 팩터리, 연결된 서비스, 데이터 집합 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1. Azure **Data Factory**를 만들었습니다.
2. **연결된 서비스**를 만들었습니다.
   1. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 **Azure Storage** 연결된 서비스입니다.     
   2. 출력 데이터를 보유하는 Azure SQL 데이터베이스를 연결하는 **Azure SQL** 연결된 서비스입니다. 
3. 파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 집합** 을 만들었습니다.
4. 원본으로 **BlobSource**를 사용하고 싱크로 **SqlSink**를 사용하는 **복사 작업**으로 **파이프라인**을 만들었습니다.  

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 저장소를 원본 데이터 저장소로 사용하고 Azure SQL 데이터베이스를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요.