---
title: Azure Data Factory에서 분할된 데이터를 읽거나 쓰는 방법 | Microsoft Docs
description: Azure Data Factory 버전 2에서 분할된 데이터를 읽거나 쓰는 방법을 알아봅니다.
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
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: e3b6ccd1e7066ed86b3d6d2d85228688b06931c4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Azure Data Factory 버전 2에서 분할된 데이터를 읽거나 쓰는 방법
버전 1에서 Azure Data Factory는 SliceStart/SliceEnd/WindowStart/WindowEnd 시스템 변수를 사용하여 분할된 데이터 읽기 또는 쓰기를 지원했습니다. 버전 2에서는 파이프라인 매개 변수와 트리거의 시작 시간/예약된 시간을 매개 변수의 값으로 사용하여 이 동작을 수행할 수 있습니다. 

## <a name="use-a-pipeline-parameter"></a>파이프라인 매개 변수 사용 
버전 1에서는 다음 예제와 같이 partitionedBy 속성과 SliceStart 시스템 변수를 사용할 수 있었습니다. 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/yearno={Year}/monthno={Month}/dayno={Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

partitonedBy 속성에 대한 자세한 내용은 [버전 1 Azure Blob 커넥터](v1/data-factory-azure-blob-connector.md#dataset-properties) 문서를 참조하세요. 

버전 2에서 이 동작을 수행하는 방법은 다음 작업을 수행하는 것입니다. 

1. type 문자열의 **파이프라인 매개 변수**를 정의합니다. 다음 예에서 파이프라인 매개 변수의 이름은 **scheduledRunTime**입니다. 
2. 데이터 집합 정의에서 **folderPath**를 파이프라인 매개 변수의 값으로 설정합니다. 
3. 파이프라인을 실행하기 전에 매개 변수에 대한 하드 코드된 값을 전달합니다. 또는 런타임에 트리거의 시작 시간 또는 예약된 시간을 동적으로 전달합니다. 

```json
"folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>트리거에서 값 전달
다음 트리거 정의에서 트리거의 예약된 시간은 **scheduledRunTime** 파이프라인 매개 변수의 값으로 전달됩니다. 

```json
{
    "name": "MyTrigger",
    "properties": {
       ...
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "scheduledRunTime": "@trigger().scheduledTime"
            }
        }
    }
}
```

## <a name="example"></a>예

다음은 샘플 데이터 집합 정의(`date`라는 매개 변수 사용)입니다.

```json
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

파이프라인 정의: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "PARTITIONEDOUTPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/partitionedgameevents/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%M')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%d')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "scheduledRunTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            },
            "partitionHiveScriptFile": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
이 파이프라인으로 데이터 팩터리를 만드는 전체 연습은 [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요. 