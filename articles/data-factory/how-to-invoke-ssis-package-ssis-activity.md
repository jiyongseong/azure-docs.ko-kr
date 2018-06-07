---
title: Azure Data Factory에서 SSIS 작업을 사용하여 SSIS 패키지 실행 | Microsoft Docs
description: 이 문서에서는 SSIS 작업을 사용하여 Azure Data Factory 파이프라인에서 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/17/2018
ms.author: douglasl
ms.openlocfilehash: 6c8bbe7ef7f74638b978cdad5b59a89fd81d12a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="run-an-ssis-package-using-the-ssis-activity-in-azure-data-factory"></a>Azure Data Factory에서 SSIS 작업을 사용하여 SSIS 패키지 실행
이 문서에서는 SSIS 작업을 사용하여 Azure Data Factory 파이프라인에서 SSIS 패키지를 실행하는 방법에 대해 설명합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. SSIS 작업은 일반 공급(GA)되는 Data Factory 서비스 버전 1에서 사용할 수 없습니다. Data Factory 서비스 버전 1을 사용하여 SSIS 패키지를 실행하는 다른 방법은 [버전 1에서 저장 프로시저 작업을 사용하여 SSIS 패키지 실행](v1/how-to-invoke-ssis-package-stored-procedure-activity.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="azure-sql-database"></a>Azure SQL Database 
이 문서의 연습에서는 SSIS 카탈로그를 호스트하는 Azure SQL 데이터베이스를 사용합니다. Azure SQL 관리되는 인스턴스(미리 보기)를 사용할 수도 있습니다.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기
Azure-SSIS 통합 런타임이 없는 경우 [자습서: SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md)의 단계별 지침에 따라 만듭니다.

## <a name="data-factory-ui-azure-portal"></a>Data Factory UI(Azure Portal )
이 섹션에서는 Data Factory UI를 사용하여 SSIS 패키지를 실행하는 SSIS 작업이 있는 Data Factory 파이프라인을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
먼저, Azure Portal을 사용하여 데이터 팩터리를 만듭니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com)로 이동합니다. 
3. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **ADFTutorialDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 이름 필드에 대해 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다(예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
     ![사용할 수 없는 이름 - 오류](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)** 를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. Data Factory에서 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 위치에 있을 수 있습니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    ![데이터 팩터리 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스) 응용 프로그램을 시작합니다. 

### <a name="create-a-pipeline-with-an-ssis-activity"></a>SSIS 작업이 있는 파이프라인 만들기
이 단계에서는 Data Factory UI를 사용하여 파이프라인을 만듭니다. 파이프라인에 SSIS 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. 시작 페이지에서 **파이프라인 만들기**를 클릭합니다. 

    ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. **작업** 도구 상자에서 **일반**을 펼치고, **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

   ![SSIS 작업을 디자이너 화면으로 끌기](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. SSIS 작업에 대한 속성의 **일반** 탭에서 작업 이름과 설명을 입력합니다. 선택적 시간 제한 및 다시 시도 값을 설정합니다.

    ![일반 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. SSIS 작업에 대한 속성의 **설정** 탭에서 패키지가 배포된 `SSISDB` 데이터베이스와 연결된 Azure-SSIS Integration Runtime을 선택합니다. `SSISDB` 데이터베이스의 패키지 경로를 `<folder name>/<project name>/<package name>.dtsx` 형식으로 제공합니다. 필요에 따라 32비트 실행 및 미리 정의되거나 사용자 지정된 로깅 수준을 지정하고 `<folder name>/<environment name>` 형식의 환경 경로를 제공합니다.

    ![설정 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. 파이프라인 구성에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>** 를 클릭합니다.

6. **모두 게시** 단추를 클릭하여 Data Factory에 파이프라인을 게시합니다. 

### <a name="run-and-monitor-the-pipeline"></a>파이프라인 실행 및 모니터링
이 섹션에서는 파이프라인 실행을 트리거한 후 모니터링합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거**를 클릭하고 **지금 트리거**를 클릭합니다. 

    ![지금 트리거](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 
3. 왼쪽의 **모니터** 탭으로 전환합니다. 다른 정보(예: 실행 시작 시간)와 함께 파이프라인 실행 및 해당 상태를 확인합니다. 보기를 새로 고치려면 **새로 고침**을 클릭합니다.

    ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 작업(SSIS 작업)만 있으므로 하나의 작업 실행만 파이프라인으로 표시됩니다.

    ![작업 실행](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

4. Azure SQL 서버의 SSISDB 데이터베이스에 대해 다음 **쿼리**를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

    ```sql
    select * from catalog.executions
    ```

    ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> 또한 파이프라인이 일정대로(시간별, 일별, 등) 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell
이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 실행하는 SSIS 작업이 있는 Data Factory 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다. 

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
Azure-SSIS IR이 있는 동일한 데이터 팩터리를 사용하거나 별도의 데이터 팩터리를 만들 수 있습니다. 다음 절차에서는 데이터 팩터리를 만드는 단계를 설명합니다. SSIS 작업이 있는 파이프라인을 이 데이터 팩터리에 만듭니다. SSIS 작업은 SSIS 패키지를 실행합니다. 

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
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

5. 데이터 팩터리를 만들려면 $ResGrp 변수의 Location 및 ResourceGroupName 속성을 사용하여 다음 **Set-AzureRmDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 미국 동부, 미국 동부 2, 유럽 서부 및 동남 아시아 지역에서만 Data Factory 버전 2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>SSIS 작업이 있는 파이프라인 만들기 
이 단계에서는 SSIS 작업이 있는 파이프라인을 만듭니다. 이 작업은 SSIS 패키지를 실행합니다. 

1. 다음 예제와 비슷한 내용이 포함된 **RunSSISPackagePipeline.json**이라는 JSON 파일을 **C:\ADF\RunSSISPackage** 폴더에 만듭니다.

    > [!IMPORTANT]
    > 파일을 저장하기 전에 개체 이름, 설명 및 경로, 속성 및 매개 변수 값, 암호 및 기타 변수 값을 바꾸세요. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  Azure PowerShell에서 `C:\ADF\RunSSISPackage` 폴더로 전환합니다.

3. **RunSSISPackagePipeline** 파이프라인을 만들려면 **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행합니다.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기
**Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 사용하여 파이프라인을 실행합니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>트리거 만들기
이전 단계에서는 파이프라인을 주문형으로 실행했습니다. 일정 트리거를 만들어 파이프라인을 예약형으로 실행할 수도 있습니다(매시간, 매일 등).

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **MyTrigger.json**이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.
3. **Set-AzureRmDataFactoryV2Trigger** cmdlet을 실행하여 트리거를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 기본적으로 트리거는 중지된 상태입니다. **Start-AzureRmDataFactoryV2Trigger** cmdlet을 실행하여 트리거를 시작합니다. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. **Get-AzureRmDataFactoryV2Trigger** cmdlet을 실행하여 트리거가 시작되었는지 확인합니다. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Azure SQL Server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>다음 단계
Azure Portal을 사용하여 파이프라인을 모니터링 할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.
