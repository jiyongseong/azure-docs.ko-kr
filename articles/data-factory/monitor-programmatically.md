---
title: Azure Data Factory를 프로그래밍 방식으로 모니터링 | Microsoft Docs
description: 다른 SDK(소프트웨어 개발 키트)를 사용하여 Data Factory에서 파이프라인을 모니터링하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 87e69349245c5f67e23022e3a45ed798400e6a2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Azure Data Factory를 프로그래밍 방식으로 모니터링
이 문서에서는 다른 SDK(소프트웨어 개발 키트)를 사용하여 Data Factory에서 파이프라인을 모니터링하는 방법을 설명합니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1의 파이프라인 모니터링 및 관리](v1/data-factory-monitor-manage-pipelines.md)를 참조하세요.

## <a name="data-range"></a>데이터 범위

Data Factory는 45일 동안 파이프라인 실행 데이터를 저장하기만 합니다. Data Factory 파이프라인 실행에 대한 데이터를 프로그래밍 방식으로 쿼리할 때(예: PowerShell 명령 `Get-AzureRmDataFactoryV2PipelineRun` 사용) 선택적 `LastUpdatedAfter` 및 `LastUpdatedBefore` 매개 변수에 대한 최대 날짜가 없습니다. 하지만 예를 들어 작년 데이터를 쿼리하는 경우 쿼리는 오류를 반환하지 않지만 최근 45일 동안의 파이프라인 실행 데이터를 반환합니다.

45일을 넘는 파이프라인 실행 데이터를 유지하려는 경우 [Azure Monitor](monitor-using-azure-monitor.md)를 사용하여 고유한 진단 로깅을 설정합니다.

## <a name="net"></a>.NET
.NET SDK를 사용하여 파이프라인을 만들고 모니터링하는 전체 연습 과정을 보려면 [.NET을 사용하여 Data Factory 및 파이프라인 만들기](quickstart-create-data-factory-dot-net.md)를 참조하세요.

1. 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행의 상태를 검사합니다.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. 복사 작업 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 코드를 추가합니다.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

.NET SDK에 대한 전체 설명서를 보려면 [Data Factory .NET SDK 참조](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet)를 참조하세요.

## <a name="python"></a>파이썬
Python SDK를 사용하여 파이프라인을 만들고 모니터링하는 전체 연습 과정을 보려면 [Python을 사용하여 Data Factory 및 파이프라인 만들기](quickstart-create-data-factory-python.md)를 참조하세요.

파이프라인 실행을 모니터링하려면 다음 코드를 추가합니다.

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Python SDK에 대한 전체 설명서를 보려면 [Data Factory Python SDK 참조](/python/api/overview/azure/datafactory?view=azure-python)를 참조하세요.

## <a name="rest-api"></a>REST API
REST API를 사용하여 파이프라인을 만들고 모니터링하는 전체 연습 과정을 보려면 [REST API를 사용하여 Data Factory 및 파이프라인 만들기](quickstart-create-data-factory-rest-api.md)를 참조하세요.
 
1. 다음 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

REST API에 대한 전체 설명서를 보려면 [Data Factory REST API 참조](/rest/api/datafactory/)를 참조하세요.

## <a name="powershell"></a>PowerShell
PowerShell을 사용하여 파이프라인을 만들고 모니터링하는 전체 연습 과정을 보려면 [PowerShell을 사용하여 Data Factory 및 파이프라인 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

1. 다음 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

PowerShell cmdlet에 대한 전체 설명서를 보려면 [Data Factory PowerShell cmdlet 참조](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Monitor를 사용하여 Data Factory 파이프라인을 모니터링하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 파이프라인 모니터링](monitor-using-azure-monitor.md) 문서를 참조하세요. 

