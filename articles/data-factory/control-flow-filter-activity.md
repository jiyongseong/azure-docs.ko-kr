---
title: Azure Data Factory의 필터 작업 | Microsoft Docs
description: 필터 작업은 입력을 필터링합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: shlo
ms.openlocfilehash: 40b409964d139641a06186114fb5e06c19971a36
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887363"
---
# <a name="filter-activity-in-azure-data-factory"></a>Azure Data Factory의 필터 작업
입력 배열에 필터 식을 적용하려면 파이프라인에서 필터 작업을 사용할 수 있습니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory V1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="syntax"></a>구문

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | `Filter` 작업의 이름입니다. | 문자열 | 예
형식 | **필터**로 설정되어야 합니다. | 문자열 | 예
condition | 입력을 필터링하는 데 사용할 조건입니다. | 식 | 예
items | 필터를 적용해야 하는 입력 배열입니다. | 식 | 예

## <a name="example"></a>예

이 예제에서 파이프라인에는 **필터** 및 **ForEach**라는 두 개의 작업이 있습니다. 필터 작업은 3보다 큰 값의 항목에 대한 입력 배열을 필터링하도록 구성됩니다. 그런 다음, ForEach 작업은 필터링된 값에 대해 반복하고 현재 값으로 지정된 몇 초를 기다립니다.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
                "name": "MyForEach",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "batchCount": 1,
                    "items": "@activity('MyFilterActivity').output.value",
                    "activities": [{
                        "type": "Wait",
                        "typeProperties": {
                            "waitTimeInSeconds": "@item()"
                        },
                        "name": "MyWaitActivity"
                    }]
                },
                "dependsOn": [{
                    "activity": "MyFilterActivity",
                    "dependencyConditions": ["Succeeded"]
                }]
            }
        ],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)