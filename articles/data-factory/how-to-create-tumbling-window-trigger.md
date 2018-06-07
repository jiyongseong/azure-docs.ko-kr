---
title: Azure Data Factory에서 연속 창 트리거 만들기 | Microsoft Docs
description: 연속 창에 따라 파이프라인을 실행하는 Azure Data Factory에서 트리거를 만드는 방법을 알아봅니다.
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>연속 창에 따라 파이프라인을 실행하는 트리거 만들기
이 문서에서는 연속 창 트리거를 만들고 시작 및 모니터링하는 단계를 제공합니다. 트리거 및 지원되는 형식에 대한 일반적인 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Azure Data Factory 버전 1을 사용 중인 경우 [Azure Data Factory 버전 1 시작](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

연속 창 트리거는 상태를 유지하면서 지정된 시작 시간부터 주기적 시간 간격으로 실행되는 트리거 유형입니다. 연속 창은 고정된 크기의 겹치지 않고 연속적인 일련의 시간 간격입니다. 연속 창 트리거는 파이프라인과 1:1 관계이며 단일 파이프라인만 참조할 수 있습니다.

## <a name="tumbling-window-trigger-type-properties"></a>연속 창 트리거 형식 속성
연속 창에는 다음과 같은 트리거 형식 속성이 있습니다.

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

다음 테이블은 연속 창 트리거의 되풀이 및 일정 계획과 관련된 주요 JSON 요소의 대략적인 개요를 제공합니다.

| JSON 요소 | 설명 | 유형 | 허용되는 값 | 필수 |
|:--- |:--- |:--- |:--- |:--- |
| **type** | 트리거 형식입니다. 형식은 고정된 값 "TumblingWindowTrigger"입니다. | 문자열 | "TumblingWindowTrigger" | 예 |
| **runtimeState** | 트리거 런타임의 현재 상태입니다.<br/>**참고**: 이 요소는 \<readOnly>입니다. | 문자열 | "시작된," "중지된," "사용 안 함" | 예 |
| **frequency** | 트리거를 되풀이하는 빈도 단위(시간 또는 분)를 나타내는 문자열입니다. **startTime** 날짜 값이 **frequency** 값보다 더 세부적인 경우 **startTime** 날짜는 시간 경계를 계산할 때 고려됩니다. 예를 들어 **frequency** 값이 시간이고 **startTime** 값이 2016-04-01T10:10:10Z이면 첫 번째 시간은 (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)입니다. | 문자열 | "minute," "hour"  | 예 |
| **interval** | 트리거가 실행되는 빈도를 결정하는 **frequency** 값에 대한 간격을 나타내는 양의 정수입니다. 예를 들어 **interval**이 3이고 **frequency**가 "시간"인 경우 트리거가 세 시간마다 되풀이됩니다. | 정수  | 양의 정수입니다. | 예 |
| **startTime**| 첫 번째 발생이며 과거일 수 있습니다. 첫 번째 트리거 간격은 (**startTime**, **startTime** + **interval**)입니다. | Datetime | DateTime 값입니다. | 예 |
| **endTime**| 마지막 발생이며 과거일 수 있습니다. | Datetime | DateTime 값입니다. | 예 |
| **delay** | 시간에서 데이터 처리의 시작 지점을 지연하는 시간입니다. 예상 실행 시간 및 **delay** 시간 후에 파이프라인이 실행되기 시작됩니다. **delay**는 새 실행을 트리거하기 전에 트리거가 기한 이후 대기하는 기간을 정의합니다. **delay**는 **startTime** 시간을 변경하지 않습니다. 예를 들어 00:10:00이라는 **delay** 값은 10분을 지연한다는 의미입니다. | Timespan  | 기본값이 00:00:00인 시간 값입니다. | 아니오 |
| **maxConcurrency** | 준비된 시간에 발생하는 동시 트리거 실행 수입니다. 예를 들어 채우기를 백업 하려면 24 windows에서 어제 결과 대 한 매시간 실행 됩니다. **maxConcurrency** = 10이면 처음 10개 시간(00:00-01:00 - 09:00-10:00)에만 트리거 이벤트가 발생합니다. 처음 10개의 트리거된 파이프라인 실행이 완료되면 다음 10개 시간(10:00-11:00 - 19:00-20:00)에 트리거 실행이 발생합니다. **maxConcurrency** = 10이라는 이 예제에서 10개 시간이 준비되면 총 10개의 파이프라인이 실행됩니다. 1개의 시간이 있는 경우 1개의 파이프라인만이 실행됩니다. | 정수  | 1~50 사이의 정수입니다. | 예 |
| **retryPolicy: Count** | 파이프라인 실행이 "실패함"으로 표시되기 전에 다시 시도한 횟수입니다.  | 정수  | 기본값이 0(다시 시도 안 함)인 정수입니다. | 아니요 |
| **retryPolicy: intervalInSeconds** | 시간(초)로 지정된 다시 시도 사이의 지연입니다. | 정수  | 기본값이 30인 시간(초)입니다. | 아니요 |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart 및 WindowEnd 시스템 변수

**파이프라인** 정의에서 연속 창 트리거의 **WindowStart** 및 **WindowEnd** 시스템 변수를 사용할 수 있습니다(즉, 쿼리 부분의 경우). **트리거** 정의에 있는 파이프라인에 매개 변수로 시스템 변수를 전달합니다. 다음 예제에서는 이러한 변수를 매개 변수로 전달하는 방법을 보여줍니다.

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

그런 다음 파이프라인 정의에서 **WindowStart** 및 **WindowEnd** 값을 사용하려면 "MyWindowStart" 및 "MyWindowEnd" 매개 변수를 적절하게 사용합니다.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>백필 시나리오에서 Windows 실행 순서
특히 백필 시나리오에서 실행할 시간이 여러 개인 경우 시간의 실행 순서는 결정적이며 가장 오래된 간격부터 최신 간격으로 실행됩니다. 현재 이 동작을 수정할 수 없습니다.

### <a name="existing-triggerresource-elements"></a>기존 TriggerResource 요소
기존 **TriggerResource** 요소에 다음과 같은 사항을 적용합니다.

* 트리거의 **frequency** 요소에 대한 값(또는 시간 크기)이 변경되는 경우 이미 처리된 시간 상태가 다시 설정되지 *않습니다*. 트리거는 새로운 시간 크기를 사용하여 실행되는 마지막 기간의 시간에 계속 실행됩니다.
* 트리거의 **endTime** 요소에 대한 값이 변경(추가 또는 업데이트)되는 경우 이미 처리된 시간 상태가 다시 설정되지 *않습니다*. 트리거는 새 **endTime** 값을 사용합니다. 시간 전에 새 **endTime** 값이 이미 실행되는 경우 트리거가 중지됩니다. 그렇지 않으면 새 **endTime** 값이 발견될 때 트리거가 중지됩니다.

## <a name="sample-for-azure-powershell"></a>Azure PowerShell의 샘플
이 섹션에서는 Azure PowerShell을 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다.

1. 다음 내용을 포함하는 **MyTrigger.json**이라는 JSON 파일을 C:\ADFv2QuickStartPSH\ 폴더에 만듭니다.

   > [!IMPORTANT]
   > JSON 파일을 저장하기 전에 **startTime** 요소의 값을 현재 UTC 시간으로 설정합니다. **endTime** 요소의 값을 현재 UTC 시간의 한 시간 이후로 설정합니다.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  

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

6. **Get-AzureRmDataFactoryV2TriggerRun** cmdlet을 사용하여 Azure PowerShell에서 트리거 실행을 가져옵니다. 트리거 실행에 대한 정보를 가져오려면 다음 명령을 주기적으로 실행합니다. **TriggerRunStartedAfter** 및 **TriggerRunStartedBefore** 값을 업데이트하여 트리거 정의의 값과 일치시킵니다.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Azure Portal에서 트리거 실행 및 파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
