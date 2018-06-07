---
title: Azure Data Factory를 사용하여 Blob Storage 데이터 복사 | Microsoft Docs
description: Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사하는 Azure 데이터 팩터리를 만듭니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 2f9ec088f7a0853817df032164c08d9dfedc405e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-data-factory-using-powershell"></a>PowerShell을 사용하여 Azure Data Factory 만들기 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [버전 2 - 미리 보기](quickstart-create-data-factory-powershell.md)

이 빠른 시작에서는 PowerShell을 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환**하는 방법에 대한 자습서는 [자습서: Apache Spark를 사용하여 데이터 변환](transform-data-using-spark.md)을 참조하세요. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.
>
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-azurerm-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

#### <a name="log-in-to-powershell"></a>PowerShell에 로그인

1. 컴퓨터에서 **PowerShell**을 시작합니다. 이 빠른 시작을 완료할 때까지 PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
2. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 동일한 Azure사용자 이름 및 암호를 입력합니다.
       
    ```powershell
    Connect-AzureRmAccount
    ```        
3. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
4. 계정과 연결된 구독이 여러 개인 경우 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId**를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ``` 
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요. 
3. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 예를 들어 ADFTutorialFactorySP1127이라는 이름을 사용합니다. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

5. 데이터 팩터리를 만들려면 $ResGrp 변수의 Location 및 ResourceGroupName 속성을 사용하여 다음 **Set-AzureRmDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서만 Data Factory 버전 2를 사용하여 데이터 팩터리를 만들 수 있습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

## <a name="create-a-linked-service"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서 원본 및 싱크 저장소로 사용되는 Azure Storage 연결된 서비스를 만듭니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다.

1. 다음 콘텐츠가 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다. (없는 경우 ADFv2QuickStartPSH 폴더를 만듭니다.) 

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountName&gt;과 &lt;accountKey&gt;를 Azure 저장소 계정의 이름과 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
    메모장을 사용하는 경우 **다른 이름으로 저장** 대화 상자의 **파일 형식** 필드에서 **모든 파일**을 선택합니다. 선택하지 않으면 파일에 `.txt` 확장이 추가됩니다. 예: `AzureStorageLinkedService.json.txt` 메모장에서 파일을 열기 전에 파일 탐색기에서 파일을 만들면 **알려진 파일 형식의 확장명 숨기기** 옵션이 기본적으로 설정되어 `.txt` 확장이 보이지 않을 수 있습니다. `.txt` 확장을 제거한 후 다음 단계로 넘어갑니다.
2. **PowerShell**에서 **ADFv2QuickStartPSH** 폴더로 전환합니다.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```
3. **Set-AzureRmDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>데이터 집합 만들기
이 단계에서는 원본에서 싱크로 복사할 데이터를 나타내는 데이터 집합을 정의합니다. 데이터 집합은 **AzureBlob** 형식입니다. 이 데이터 집합은 이전 단계에서 만든 **Azure Storage 연결된 서비스**를 참조합니다. **folderPath** 속성을 생성하려면 매개 변수를 사용합니다. 입력 데이터 집합의 경우 파이프라인에서의 복사 작업은 입력 경로를 이 매개 변수에 대한 값으로 전달합니다. 마찬가지로, 출력 데이터 집합의 경우 복사 작업은 출력 경로를 이 매개 변수에 대한 값으로 전달합니다. 

1. 다음 콘텐츠가 포함된 **BlobDataset.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **BlobDataset** 데이터 집합을 만들려면 **Set-AzureRmDataFactoryV2Dataset** cmdlet을 실행합니다.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
  
이 빠른 시작에서 입력 Blob 경로 및 출력 Blob 경로의 두 매개 변수를 취하는 하나의 활동을 사용하여 파이프라인을 만듭니다. 이러한 매개 변수의 값은 파이프라인이 트리거/실행될 때 설정됩니다. 복사 작업은 입력 및 출력 시 이전 단계에서 만든 동일한 Blob 데이터 집합을 사용합니다. 데이터 집합을 입력된 데이터 집합으로 사용하는 경우 입력된 경로가 지정됩니다. 또한 데이터 집합을 출력된 데이터 집합으로 사용하는 경우 출력된 경로가 지정됩니다. 

1. 다음 콘텐츠가 포함된 **Adfv2QuickStartPipeline.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. **Adfv2QuickStartPipeline** 파이프라인을 만들려면 **Set-AzureRmDataFactoryV2Pipeline** cmdlet을 실행합니다.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

이 단계에서 **inputPath** 및 **outputPath** 파이프라인 매개 변수에 대한 값을 원본 및 싱크 Blob 경로의 실제 값으로 설정합니다. 그런 다음 이러한 인수를 사용하여 실행할 파이프라인을 만듭니다. 

1. 다음 콘텐츠가 포함된 **PipelineParameters.json**이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet을 실행하여 매개 변수 값에서 실행 및 전달할 파이프라인을 만듭니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

    ```powershell
    while ($True) {
        $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

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

    파이프라인 실행의 샘플 출력은 다음과 같습니다.

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    오류가 표시되는 경우
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    다음 단계를 수행합니다. 
    1. AzureStorageLinkedService.json에서 Azure Storage 계정의 이름과 키가 정확한지 확인합니다. 
    2. 연결 문자열의 형식이 정확한지 확인합니다. 예를 들어 AccountName 및 AccountKey 속성은 세미콜론 (`;`) 문자로 구분됩니다. 
    3. 계정 이름 및 계정 키를 둘러싼 각괄호가 있다면 제거합니다. 
    4. 다음은 연결 문자열의 예제입니다. 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. [연결된 서비스 만들기](#create-a-linked-service) 섹션에서 다음 단계를 통해 연결된 서비스를 다시 만듭니다. 
    6. [파이프라인 실행 만들기](#create-a-pipeline-run) 섹션에서 다음 단계를 통해 파이프라인을 다시 실행합니다. 
    7. 새 파이프라인 실행을 모니터링하기 위해 현재 모니터링 명령을 다시 실행합니다. 
1. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. 활동 실행 결과의 다음 샘플 출력과 유사한 출력이 표시되는지 확인합니다.

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob 저장소의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요. 
