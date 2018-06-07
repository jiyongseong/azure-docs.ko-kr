---
title: Azure Data Factory를 사용하여 SSIS 패키지 호출 - 저장 프로시저 작업 | Microsoft Docs
description: 이 문서에서는 Azure Data Factory 파이프라인에서 저장 프로시저 작업을 사용하여 SSIS(SQL Server Integration Services) 패키지를 호출하는 방법에 대해 설명합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 309a64c47eb95ca2ca05bb82bc1c41256305823d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory에서 저장 프로시저 작업을 사용하여 SSIS 패키지 호출
이 문서에서는 Azure Data Factory 파이프라인에서 저장 프로시저 작업을 사용하여 SSIS 패키지를 호출하는 방법에 대해 설명합니다. 

> [!NOTE]
> 이 문서는 일반 공급 상태인 Data Factory 버전 1에 적용됩니다. 공개 미리 보기 상태인 Data Factory 버전 2 서비스를 사용 중인 경우 [버전 2에서 저장 프로시저 작업을 사용하여 SSIS 패키지 호출](../how-to-invoke-ssis-package-stored-procedure-activity.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-sql-database"></a>Azure SQL Database 
이 문서의 연습에서는 SSIS 카탈로그를 호스트하는 Azure SQL 데이터베이스를 사용합니다. Azure SQL 관리되는 인스턴스(미리 보기)를 사용할 수도 있습니다.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기
Azure-SSIS 통합 런타임이 없는 경우 [자습서: SSIS 패키지 배포](../tutorial-create-azure-ssis-runtime-portal.md)의 단계별 지침에 따라 만듭니다. Azure-SSIS 통합 런타임을 만들려면 버전 2의 데이터 팩터리를 만들어야 합니다. 

## <a name="azure-portal"></a>Azure portal
이 섹션에서는 Azure Portal을 사용하여 SSIS 패키지를 호출하는 저장 프로시저 작업이 있는 Data Factory 파이프라인을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
먼저, Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다. 
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 문서를 참조하세요.

    `Data factory name ADFTutorialDataFactory is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 대해 **V1**을 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. Data Factory에서 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 위치에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. **작성자 및 배포** 타일을 클릭하여 데이터 팩터리 편집기를 시작합니다.

    ![데이터 팩터리 편집기](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
SSIS 카탈로그를 호스트하는 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다. 데이터 팩터리는 이 연결된 서비스의 정보를 사용하여 SSISDB 데이터베이스에 연결하고 저장 프로시저를 실행하여 SSIS 패키지를 실행합니다. 

1. 데이터 팩터리 편집기의 메뉴에서 **새 데이터 저장소**, **Azure SQL Database**를 차례로 클릭합니다. 

    ![새 데이터 저장소 -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. 오른쪽 창에서 다음 단계를 수행합니다.

    1. `<servername>`을 Azure SQL 서버의 이름으로 바꿉니다. 
    2. `<databasename>`을 **SSISDB**(SSIS 카탈로그 데이터베이스의 이름)로 바꿉니다. 
    3. `<username@servername>`을 Azure SQL 서버에 액세스할 수 있는 사용자의 이름으로 바꿉니다. 
    4. `<password>`를 사용자의 암호로 바꿉니다. 
    5. 도구 모음에서 **배포** 단추를 클릭하여 연결된 서비스를 배포합니다. 

        ![Azure SQL Database 연결된 서비스](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>출력에 대한 더미 데이터 집합 만들기
출력 데이터 집합은 파이프라인의 일정을 결정하는 더미 데이터 집합입니다. 빈도는 시간으로 설정되고 간격은 1로 설정됩니다. 따라서 파이프라인은 파이프라인 시작 및 종료 시간 내에 한 시간에 한 번 실행됩니다. 

1. 데이터 팩터리 편집기의 왼쪽 창에서 **... 자세히** -> **새 데이터 집합** -> **Azure SQL**을 차례로 클릭합니다.

    ![자세히 -> 새 데이터 집합](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. 다음 JSON 코드 조각을 오른쪽 창의 JSON 편집기에 복사합니다. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. 도구 모음에서 **배포** 를 클릭합니다. 이 작업은 데이터 집합을 Azure Data Factory 서비스에 배포합니다. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>저장 프로시저 작업을 사용하여 파이프라인 만들기 
이 단계에서는 저장 프로시저 작업을 사용하여 파이프라인 만듭니다. 이 작업은 sp_executesql 저장 프로시저를 호출하여 SSIS 패키지를 실행합니다. 

1. 왼쪽 창에서 **... 추가**를 클릭하고 **새 파이프라인**을 클릭합니다.
2. 다음 JSON 코드 조각을 JSON 편집기에 복사합니다. 

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt;을 SSIS 카탈로그에 있는 폴더, 프로젝트 및 패키지의 이름으로 바꿉니다.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. 도구 모음에서 **배포** 를 클릭합니다. 이 작업은 파이프라인을 Azure Data Factory 서비스에 배포합니다. 

### <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.
출력 데이터 집합의 일정은 매시간으로 정의됩니다. 파이프라인 종료 시간은 시작 시간에서 5시간입니다. 따라서 5개의 파이프라인 실행이 표시됩니다. 

1. 데이터 팩터리에 대한 홈페이지가 표시되도록 편집기 창을 닫습니다. **모니터링 및 관리** 타일을 클릭합니다. 

    ![다이어그램 타일](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. **시작 시간** 및 **종료 시간**을 **2018년 1월 18일 오전 8시 30분** 및 **2018년 1월 20일 오전 8시 30분**으로 업데이트하고 **적용**을 클릭합니다. 파이프라인 실행과 연결된 **활동 창**이 표시됩니다. 

    ![활동 창](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

파이프라인 모니터링에 대한 자세한 내용은 [모니터링 및 관리 앱을 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell
이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 호출하는 저장 프로시저 작업이 있는 Data Factory 파이프라인을 만듭니다.

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
다음 절차에서는 데이터 팩터리를 만드는 단계를 설명합니다. 이 데이터 팩터리의 저장 프로시저 작업을 사용하여 파이프라인을 만듭니다. 저장 프로시저 작업은 SSISDB 데이터베이스의 저장 프로시저를 실행하여 SSIS 패키지를 실행합니다.

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요. 
3. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. 데이터 팩터리를 만들려면 $ResGrp 변수의 Location 및 ResourceGroupName 속성을 사용하여 다음 **New-AzureRmDataFactory** cmdlet을 실행합니다. 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
SSIS 카탈로그를 호스트하는 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다. 데이터 팩터리는 이 연결된 서비스의 정보를 사용하여 SSISDB 데이터베이스에 연결하고 저장 프로시저를 실행하여 SSIS 패키지를 실행합니다. 

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **AzureSqlDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. 

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; 및 &lt;password&gt;를 Azure SQL Database의 값으로 바꿉니다.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.
3. **New-AzureRmDataFactoryLinkedService** cmdlet을 실행하여 **AzureSqlDatabaseLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>출력 데이터 집합 만들기
출력 데이터 집합은 파이프라인의 일정을 결정하는 더미 데이터 집합입니다. 빈도는 시간으로 설정되고 간격은 1로 설정됩니다. 따라서 파이프라인은 파이프라인 시작 및 종료 시간 내에 한 시간에 한 번 실행됩니다. 

1. 다음 콘텐츠를 사용하여 OuputDataset.json 파일을 만듭니다. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. **New-AzureRmDataFactoryDataset** cmdlet을 실행하여 데이터 집합을 만듭니다. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>저장 프로시저 작업을 사용하여 파이프라인 만들기 
이 단계에서는 저장 프로시저 작업을 사용하여 파이프라인 만듭니다. 이 작업은 sp_executesql 저장 프로시저를 호출하여 SSIS 패키지를 실행합니다. 

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **MyPipeline.json**이라는 JSON 파일을 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt;을 SSIS 카탈로그에 있는 폴더, 프로젝트 및 패키지의 이름으로 바꿉니다.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. **New-AzureRmDataFactoryPipeline** cmdlet을 실행하여 **RunSSISPackagePipeline** 파이프라인을 만듭니다.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

2. **Get-AzureRmDataFactorySlice**를 실행하여 파이프라인의 출력 테이블인 출력 데이터 집합\** 의 모든 조각에 대한 세부 정보를 가져옵니다.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    여기에 지정하는 StartDateTime은 파이프라인 JSON에 지정된 것과 동일한 시작 시간입니다. 
3. **Get-AzureRmDataFactoryRun** 을 실행하여 특정 조각에 대한 작업 실행의 세부 정보를 가져옵니다.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    **준비** 상태 또는 **실패** 상태에 조각이 표시될 때가지 이 cmdlet을 계속 실행합니다. 

    Azure SQL Server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>다음 단계
저장 프로시저 작업에 대한 자세한 내용은 [저장 프로시저 작업](data-factory-stored-proc-activity.md) 문서를 참조하세요.

