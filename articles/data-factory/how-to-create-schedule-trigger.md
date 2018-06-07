---
title: Azure Data Factory에서 일정 트리거 만들기 | Microsoft Docs
description: 일정에 따라 파이프라인을 실행하는 Azure Data Factory에서 트리거를 만드는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 6466d6cb535bbe0042d7c4c3e828e576e23d5d07
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>일정에 따라 파이프라인을 실행하는 트리거 만들기
이 문서에서는 일정 트리거 및 일정 트리거를 만들고 시작 및 모니터링하는 단계에 대한 정보를 제공합니다. 다른 트리거 유형의 경우 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

일정 트리거를 만들 때 트리거에 일정(시작 날짜, 되풀이, 종료 날짜 등)을 지정하고 파이프라인에 연결합니다. 파이프라인 및 트리거는 다 대 다 관계를 가지고 있습니다. 다중 트리거는 단일 파이프라인을 시작할 수 있습니다. 단일 트리거는 여러 파이프라인을 시작할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Azure Data Factory 버전 1을 사용 중인 경우 [Azure Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

다음 섹션에서는 다양한 방식으로 일정 트리거를 만드는 단계를 제공합니다. 

## <a name="data-factory-ui"></a>Data Factory UI
**스케줄러 트리거**를 만들어 파이프라인이 주기적으로(매시간, 매일 등) 실행되도록 예약할 수 있습니다. 

> [!NOTE]
> 파이프라인 및 일정 트리거를 만들고, 파이프라인과 트리거를 연결하고, 파이프라인과 트리거를 연결하고, 파이프라인을 실행하고 모니터링하는 전체 연습은 [빠른 시작: Data Factory UI를 사용하여 Data Factory 만들기](quickstart-create-data-factory-portal.md)를 참조하세요.

1. **편집** 탭으로 전환합니다. 

    ![편집 탭으로 전환](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. 메뉴에서 **트리거**를 클릭하고 **새로 만들기/편집**을 클릭합니다. 

    ![새 트리거 메뉴](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. **트리거 추가** 페이지에서 **트리거 선택...**을 클릭하고 **새로 만들기**를 클릭합니다. 

    ![트리거 추가 - 새 트리거](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. **새 트리거** 페이지에서 다음 단계를 수행합니다. 

    1. **형식**에 대해 **일정**이 선택되었는지 확인합니다. 
    2. **날짜 시작(UTC)**에 대한 트리거의 시작 날짜/시간을 지정합니다. 기본적으로 현재 날짜/시간으로 설정됩니다. 
    3. 트리거에 대한 **되풀이**를 지정합니다. 드롭다운 목록에서 값 중 하나를 선택합니다(매분, 매시간, 매일, 매주 및 매월). 텍스트 상자에서 승수를 입력합니다. 예를 들어 트리거를 15분마다 한 번 실행하려는 경우 **매분**을 선택하고, 텍스트 상자에 **15**를 입력합니다. 
    4. **종료** 필드에서 트리거에 대한 종료 날짜/시간을 지정하지 않은 경우 **종료 없음**을 선택합니다. 종료 날짜/시간을 지정하려면 **날짜에**를 선택하고, 종료 날짜/시간을 지정하고, **적용**을 클릭합니다. 각 파이프라인 실행과 관련된 비용이 있습니다. 테스트하는 경우 파이프라인을 몇 번만 트리거하도록 합니다. 그러나 게시 시간과 종료 시간 사이에 파이프라인이 실행되는 데 충분한 시간이 있는지 확인합니다. 트리거는 UI에 트리거를 저장할 때가 아니라 Data Factory에 솔루션을 게시한 후에만 적용됩니다.

        ![트리거 설정](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. **새 트리거** 창에서 **활성화됨** 옵션을 확인하고 **다음**을 클릭합니다. 나중에 트리거를 비활성화하려면 이 확인란을 사용할 수 있습니다. 

    ![트리거 설정 - 다음 단추](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. **새 트리거** 페이지에서 경고 메시지를 검토하고 **마침**을 클릭합니다.

    ![트리거 설정 - 마침 단추](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. **게시**를 클릭하여 변경 내용을 Data Factory에 클릭합니다. 트리거는 Data Factory에 변경 내용을 게시하기 전에 파이프라인 실행을 트리거하기 시작하지 않습니다. 

    ![게시 단추](./media/how-to-create-schedule-trigger/publish-2.png)
8. 왼쪽의 **모니터** 탭으로 전환합니다. **새로 고침**을 클릭하여 목록을 새로 고칩니다. 예약된 트리거로 트리거된 파이프라인 실행이 표시됩니다. **트리거 기준** 열의 값을 확인합니다. **지금 트리거** 옵션을 사용하는 경우 목록에서 수동 트리거 실행이 표시됩니다. 

    ![트리거된 실행 모니터링](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. **파이프라인 실행** 옆의 아래쪽 화살표를 클릭하여 **트리거 실행** 보기로 전환합니다. 

    ![트리거 실행 모니터링](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
이 섹션에서는 Azure PowerShell을 사용하여 일정 트리거를 만들고 시작 및 모니터링하는 방법을 보여줍니다. 이 샘플이 작동하는지 확인하려면 먼저 [빠른 시작: Azure PowerShell을 사용하여 Data Factory 만들기](quickstart-create-data-factory-powershell.md)를 진행하세요. 그런 다음 15분마다 실행되는 일정 트리거를 만들고 시작하는 다음 코드를 주 메서드에 추가하세요. 트리거는 빠른 시작의 일부로 만든 **Adfv2QuickStartPipeline**이라는 파이프라인과 연결되어 있습니다.

1. 다음 내용을 포함하는 **MyTrigger.json**이라는 JSON 파일을 C:\ADFv2QuickStartPSH\ 폴더에 만듭니다.

    > [!IMPORTANT]
    > JSON 파일을 저장하기 전에 **startTime** 요소의 값을 현재 UTC 시간으로 설정합니다. **endTime** 요소의 값을 현재 UTC 시간의 한 시간 이후로 설정합니다.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    JSON 코드 조각에서 다음을 수행합니다.
    - 트리거의 **형식** 요소는 "ScheduleTrigger"로 설정됩니다.
    - **빈도** 요소는 "분"으로 설정되고 **간격** 요소는 15로 설정됩니다. 따라서 트리거는 시작 시간과 종료 시간 사이에 15분마다 파이프라인을 실행합니다.
    - **endTime** 요소는 **startTime** 요소 값 이후 1시간입니다. 따라서 트리거가 시작 시간 이후 15분, 30분 및 45분에 파이프라인을 실행합니다. 시작 시간을 현재 UTC 시간으로 업데이트하고, 종료 시간을 시작 시간의 1시간 후로 업데이트하는 것에 유의하세요. 
    - 트리거는 **Adfv2QuickStartPipeline** 파이프라인과 연결되어 있습니다. 여러 파이프라인을 트리거와 연결하려면 **pipelineReference 섹션** 섹션을 더 추가하세요.
    - 빠른 시작의 파이프라인은 **inputPath** 및 **outputPath**라는 두 개의 **parameters** 값을 사용합니다. 따라서 이러한 매개 변수에 대한 값을 트리거에서 전달합니다.

2. **Set-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 만듭니다.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. **Get-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **중지됨**인지 확인합니다.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. **Start-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 시작합니다.

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. **Get-AzureRmDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **시작됨**인지 확인합니다.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  **Get-AzureRmDataFactoryV2TriggerRun** cmdlet을 사용하여 Azure PowerShell에서 트리거 실행을 가져옵니다. 트리거 실행에 대한 정보를 가져오려면 다음 명령을 주기적으로 실행합니다. **TriggerRunStartedAfter** 및 **TriggerRunStartedBefore** 값을 업데이트하여 트리거 정의의 값과 일치시킵니다.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Azure Portal에서 트리거 실행 및 파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.


## <a name="net-sdk"></a>.NET SDK
이 섹션에서는 .NET SDK를 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다. 이 샘플이 작동하는지 확인하려면 먼저 [빠른 시작: .NET SDK를 사용하여 Data Factory 만들기](quickstart-create-data-factory-dot-net.md)를 진행하세요. 그런 다음 15분마다 실행되는 일정 트리거를 만들고 시작하는 다음 코드를 주 메서드에 추가하세요. 트리거는 빠른 시작의 일부로 만든 **Adfv2QuickStartPipeline**이라는 파이프라인과 연결되어 있습니다.

15분마다 실행되는 일정 트리거를 만들고 시작하려면 다음 코드를 주 메서드에 추가합니다.

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

트리거 실행을 모니터링하려면 샘플에서 마지막 `Console.WriteLine` 문 앞에 다음 코드를 추가합니다.

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Azure Portal에서 트리거 실행 및 파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.


## <a name="python-sdk"></a>Python SDK
이 섹션에서는 Python SDK를 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여줍니다. 이 샘플이 작동하는지 확인하려면 먼저 [빠른 시작: Python SDK를 사용하여 Data Factory 만들기](quickstart-create-data-factory-python.md)를 진행하세요. 그런 다음 Python 스크립트에서 "monitor the pipeline run" 코드 블록 뒤에 다음 코드 블록을 추가합니다. 이 코드는 지정된 시작 시간과 종료 시간 사이에 15분마다 실행되는 일정 트리거를 만듭니다. **start_time** 변수를 현재 UTC 시간으로 업데이트하고, **end_time** 변수를 현재 UTC 시간의 1시간 후로 업데이트합니다.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Azure Portal에서 트리거 실행 및 파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용하여 트리거를 만들 수 있습니다. 단계별 지침은 [Resource Manager 템플릿을 사용하여 Azure Data Factory 만들기](quickstart-create-data-factory-resource-manager-template.md)를 참조하세요.  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>파이프라인에 트리거 시작 시간 전달
Azure Data Factory 버전 1은 **SliceStart**, **SliceEnd**, **WindowStart** 및 **WindowEnd**를 사용하여 분할된 데이터를 읽거나 쓰도록 지원합니다. Azure Data Factory 버전 2에서 파이프라인 매개 변수를 사용하여 이 동작을 수행할 수 있습니다. 트리거의 시작 시간 및 예약된 시간은 파이프라인 매개 변수의 값으로 설정됩니다. 다음 예제에서는 트리거의 예약된 시간이 파이프라인 매개 변수 **scheduledRunTime**에 값으로 전달됩니다.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

자세한 내용은 [분할된 데이터를 읽거나 쓰는 방법](how-to-read-write-partitioned-data.md)의 지침을 참조하세요.

## <a name="json-schema"></a>JSON 스키마
다음 JSON 정의는 일정 및 되풀이를 사용하여 일정 트리거를 만드는 방법을 보여줍니다.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **매개 변수** 속성은 **pipelines** 요소의 필수 속성입니다. 파이프라인에서 매개 변수를 사용하지 않는 경우에도 **parameters** 속성에 대해 빈 JSON 정의를 포함해야 합니다.


### <a name="schema-overview"></a>스키마 개요
다음 테이블은 트리거의 되풀이 및 일정 계획과 관련된 주요 스키마 요소의 공급 개요를 제공합니다.

| JSON 속성 | 설명 |
|:--- |:--- |
| **startTime** | 날짜-시간 값입니다. 단순한 일정의 경우 **startTime** 속성의 값이 첫 번째 되풀이에 적용됩니다. 복잡한 일정의 경우 트리거는 특정 **startTime** 값 이후에 시작합니다. |
| **endTime** | 트리거의 종료 날짜 및 시간입니다. 트리거는 지정된 종료 날짜 및 시간 이후에 실행되지 않습니다. 속성에 대한 값은 이전에 있을 수 없습니다. 이 속성은 선택 사항입니다. |
| **timeZone** | 표준 시간대입니다. 현재는 UTC 표준 시간대만 지원됩니다. |
| **recurrence** | 트리거에 대한 되풀이 규칙을 지정하는 recurrence 개체입니다. 되풀이 개체는 **frequency**, **interval**, **endTime**, **count** 및 **schedule** 요소를 지원합니다. 되풀이 개체가 정의된 경우 **frequency** 요소는 필수입니다. 되풀이 개체의 다른 요소는 선택적입니다. |
| **frequency** | 트리거가 되풀이되는 빈도 단위입니다. 지원되는 값은 "minute", "hour", "day", "week" 및 "month"입니다. |
| **interval** | 트리거가 실행되는 빈도를 결정하는 **frequency** 값에 대한 간격을 나타내는 양의 정수입니다. 예를 들어 **interval**이 3이고 **frequency**가 "week"인 경우 매 3주마다 트리거가 되풀이됩니다. |
| **schedule** | 트리거에 대한 되풀이 일정입니다. 지정된 **frequency** 값을 가진 트리거는 되풀이 일정을 기반으로 되풀이를 변경합니다. **schedule** 속성에는 분, 시간, 요일, 날짜, 주차를 기반으로 하는 되풀이에 대한 수정 내용이 포함됩니다.


### <a name="schema-defaults-limits-and-examples"></a>스키마 기본값, 제한 및 예제

| JSON 속성 | 유형 | 필수 | 기본값 | 유효한 값 | 예 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | 문자열 | 예 | 없음 | ISO-8601 날짜-시간 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Object | 예 | 없음 | 되풀이 개체 | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | 아니오 | 1 | 1~1,000 | `"interval":10` |
| **endTime** | 문자열 | 예 | 없음 | 현재 이후의 시간을 나타내는 날짜-시간 값입니다. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Object | 아니오 | 없음 | 일정 개체 | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime 속성
다음 표는 **startTime** 속성이 트리거 실행을 제어하는 방법을 보여줍니다.

| startTime 값 | 일정 없이 되풀이 | 일정대로 되풀이 |
|:--- |:--- |:--- |
| 시작 시간이 이전임 | 시작 시간 이후 첫 실행 시간을 계산하고 해당 시간에 실행됩니다.<br/><br/>마지막 실행 시간에서 계산된 값을 기반으로 후속 실행을 수행합니다.<br/><br/>이 테이블 다음에 나오는 예제를 참조하세요. | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다.<br/><br/>되풀이 일정에 따라 후속 실행을 수행합니다. |
| 시작 시간이 이후이거나 현재임 | 지정된 시작 시간에 한 번 실행됩니다.<br/><br/>마지막 실행 시간에서 계산된 값을 기반으로 후속 실행을 수행합니다. | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다.<br/><br/>되풀이 일정에 따라 후속 실행을 수행합니다. |

시작 시간이 이전이고 되풀이가 있으나 일정이 없는 경우에 어떻게 되는지 예제를 보겠습니다. 현재 시간이 `2017-04-08 13:00`이고, 시작 시간이 `2017-04-07 14:00`이며 되풀이는 2일마다라고 가정합니다. (**recurrence** 값은 **frequency** 속성을 "day"로 **interval** 속성을 2로 설정하여 정의됩니다.) **startTime** 값이 현재 시간보다 이전임에 유의하십시오.

이러한 조건일 경우 첫 실행은 `2017-04-09 at 14:00`입니다. Scheduler 엔진이 시작 시간에서 되풀이 실행 시간을 계산합니다. 현재보다 이전의 모든 인스턴스는 무시됩니다. 엔진은 이후에 발생하는 다음 인스턴스를 사용합니다. 이 시나리오에서 시작 시간은 `2017-04-07 at 2:00pm`이므로 다음 인스턴스는 `2017-04-09 at 2:00pm`인 시간부터 2일 후입니다.

첫 번째 실행 시간은 **startTime** 값이 `2017-04-05 14:00` 또는 `2017-04-01 14:00`이더라도 동일합니다. 첫 번째 실행 후 후속 실행은 일정을 사용하여 계산됩니다. 따라서 후속 실행은 차례로 `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm`, `2017-04-15 at 2:00pm` 등입니다.

마지막으로, 시간 또는 분이 트리거에 대한 일정에 설정되지 않은 경우 첫 번째 실행의 시간 또는 분은 기본값으로 사용됩니다.

### <a name="schedule-property"></a>일정 속성
한편 일정을 사용하여 트리거 실행의 횟수를 제한할 수 있습니다. 예를 들어 매월 빈도인 트리거가 31일에만 실행되도록 예정되어 있을 경우, 트리거는 31일이 있는 달에만 실행됩니다.

반면 schedule로 트리거 실행의 횟수를 늘릴 수도 있습니다. 예를 들어 매월 1일 및 2일에 실행되도록 예약된 월별 빈도인 트리거는 월에 한 번이 아닌 해당 월의 첫 번째 및 두 번째 날에 실행됩니다.

여러 **일정** 요소가 지정된 경우, 계산되는 순서는 가장 큰 것부터 가장 작은 일정 설정입니다. 계산은 주차, 날짜, 요일, 시간, 분으로 시작합니다.

다음 테이블에서는 **일정** 요소의 세부 정보를 설명합니다.


| JSON 요소 | 설명 | 유효한 값 |
|:--- |:--- |:--- |
| **minutes** | 트리거가 실행될 시간(분)입니다. | <ul><li>정수 </li><li>정수 배열</li></ul>
| **hours** | 트리거가 실행될 일(시간)입니다. | <ul><li>정수 </li><li>정수 배열</li></ul> |
| **weekDays** | 트리거가 실행되는 요일입니다. 값은 빈도가 주인 경우에만 지정될 수 있습니다. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday</li><li>날짜 값의 배열(최대 배열 크기는 7)</li><li>날짜 값은 대/소문자 구분 안 함</li></ul> |
| **monthlyOccurrences** | 트리거가 실행되는 날짜입니다. 값은 빈도가 월인 경우에만 지정될 수 있습니다. | <ul><li>**monthlyOccurence** 개체의 배열: `{ "day": day,  "occurrence": occurence }`.</li><li>**day** 특성은 트리거가 실행되는 요일입니다. 예를 들어 `{Sunday}`의 **day** 값을 가진 **monthlyOccurrences** 속성은 해당 월의 매주 일요일을 의미합니다. **day** 특성은 필수입니다.</li><li>**occurrence** 특성은 월동안 지정된 **day**의 되풀이입니다. 예를 들어 `{Sunday, -1}`의 **day** 및 **occurrence** 값을 가진 **monthlyOccurrences** 속성은 해당 월의 마지막 일요일을 의미합니다. **occurrence** 특성은 선택 사항입니다.</li></ul> |
| **monthDays** | 트리거가 실행되는 날짜입니다. 값은 빈도가 월인 경우에만 지정될 수 있습니다. | <ul><li>1 이상 31 이하 사이의 모든 값</li><li>1 이하 및 31 이상의 모든 값</li><li>값의 배열</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>트리거 되풀이 일정의 예
이 섹션은 되풀이 일정의 예를 제공하며 **schedule** 개체와 하위 요소에 초점을 맞춥니다.

예제에서 **interval** 값은 1이고 **frequency** 값은 일정 정의에 따라 올바르다고 가정합니다. 예를 들어 "day"의 **frequency** 값을 가질 수 없으며 **schedule** 개체에 "monthDays" 수정 내용을 가질 수 없습니다. 이러한 제한 사항은 이전 섹션의 테이블에서 언급했습니다.

| 예 | 설명 |
|:--- |:--- |
| `{"hours":[5]}` | 매일 오전 5시에 실행됩니다. |
| `{"minutes":[15], "hours":[5]}` | 매일 오전 5시 15분에 실행됩니다. |
| `{"minutes":[15], "hours":[5,17]}` | 매일 오전 5시 15분 및 오후 5시 15분에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17]}` | 매일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45]}` | 15분마다 실행됩니다. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 매시간 실행됩니다. 이 트리거는 매 1시간마다 실행합니다. 값이 지정된 경우 분은 **startTime** 값으로 제어됩니다. 값이 지정되지 않은 경우 분은 생성 시간으로 제어됩니다. 예를 들어 시작 시간 또는 생성 시간(둘 중 해당하는 것)이 오후 12:25이라면 트리거는 00:25, 01:25, 02:25, …, 23:25에 실행됩니다.<br/><br/>이 일정은 트리거의 **frequency** 값이 "hour"이고 **interval** 값은 1이며 **schedule**이 없는 것과 동일합니다.  다른 **frequency** 및 **interval** 값으로 이 일정을 사용하여 다른 트리거를 만들 수 있습니다. 예를 들어 **frequency** 값이 "month"인 경우 일정은 **frequency** 값이 "day"인 경우 매일보다는 한 달에 한 번만 실행됩니다. |
| `{"minutes":[0]}` | 매시간 정각에 실행됩니다. 이 트리거는 오전 12시, 오전 1시, 오전 2시 등에 시작하는 시간에 매시간 실행됩니다.<br/><br/>이 일정은 트리거의 **frequency** 값이 "hour"이고 **startTime** 값이 0분이거나 **schedule**이 없지만 **frequency**  값이 "day"인 것과 동일합니다. **frequency** 값이 "week" 또는 "month"인 경우 일정은 각각 주에 하루 또는 월에 하루만 실행됩니다. |
| `{"minutes":[15]}` | 매시간 15분에 실행됩니다. 이 트리거는 오전 0시 15분, 오전 1시 15분, 오전 2시 15분 등에 시작하여 오후 11시 15분에 끝나는 시간 이후에 매시간 15분에 실행됩니다. |
| `{"hours":[17], "weekDays":["saturday"]}` | 매주 토요일 오후 5시에 실행됩니다. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일, 금요일 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일, 금요일 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 15분마다 실행됩니다. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 15분마다 실행됩니다. |
| `{"weekDays":["tuesday", "thursday"]}` | 화요일과 목요일의 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 매월 28일 오전 6시에 실행됩니다(**frequency** 값이 "month"라고 가정). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 월의 마지막 날짜 오전 6시에 실행됩니다. 월의 마지막 날짜에 트리거를 실행하려면 28, 29, 30 또는 31 대신 -1을 사용합니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 매월 1일과 말일의 오전 6시에 실행됩니다. |
| `{monthDays":[1,14]}` | 매월 1일과 14일의 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일 오전 5시에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 매월 마지막 달의 세 번째 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째와 마지막 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 매월 다섯 번째 금요일의 지정된 시작 시간에 실행됩니다. 5번째 금요일에만 실행되도록 설정되었으므로 5번째 금요일이 없는 달에는 파이프라인이 실행되지 않습니다. 해당 월의 마지막 금요일에 트리거를 실행되도록 하려면 **occurrence** 값으로 5대신 -1을 사용하면 됩니다. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 매월 마지막 금요일에 15분마다 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 매월 세 번째 수요일에 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |


## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
