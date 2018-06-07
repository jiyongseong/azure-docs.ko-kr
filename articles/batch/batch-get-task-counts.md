---
title: 상태별로 작업을 계산하여 작업 진행 상태 모니터링 - Azure Batch | Microsoft Docs
description: 작업에 대한 태스크 수를 계산하도록 Get Task Counts 연산을 호출하여 작업의 진행 상황을 모니터링합니다. 활성, 실행 중, 완료된 태스크 수는 물론, 성공 또는 성공한 태스크 수를 계산할 수 있습니다.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: danlep
ms.openlocfilehash: bc112ed5b481560362962d6b550d336de6b3d9b4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>상태별로 태스크 수를 계산하여 작업의 진행 상황 모니터링(미리 보기)

Azure Batch는 태스크 실행 시 작업의 진행 상황을 모니터링하는 효율적인 방법을 제공합니다. [Get Task Counts][rest_get_task_counts] 연산을 호출하여 활성, 실행 중 또는 완료된 상태의 태스크 수와 성공 또는 실패한 태스크 수를 확인할 수 있습니다. 각 상태에 있는 태스크 수를 계산하면 사용자에게 해당 작업의 진행 상태를 쉽게 표시하거나, 작업에 영향을 미칠 수 있는 예상치 못한 지연 또는 오류를 감지할 수 있습니다.

> [!IMPORTANT]
> Get Task Counts 연산은 현재 미리 보기 상태이며 Azure Government, Azure China 및 Azure Germany에서는 아직 지원되지 않습니다. 
>
>

## <a name="how-tasks-are-counted"></a>태스크 계산 방법

Get Task Counts 연산은 다음과 같이 상태별로 태스크 수를 계산합니다.

- 대기열에 있으며 실행할 수 있는 태스크는 **활성**으로 계산되지만, 현재는 계산 노드에 할당되어 있지 않습니다. 아직 완료되지 않은 상위 테스크에 종속된 태스크도 **활성**으로 계산됩니다. 태스크 종속성 동작에 대한 자세한 내용은 [태스크 종속성을 만들어 다른 태스크에 종속된 태스크 실행](batch-task-dependencies.md)을 참조하세요. 
- 계산 모드에 할당되었지만, 아직 완료하지 않은 태스크는 **실행 중**으로 계산됩니다. [태스크에 대한 정보 가져오기][rest_get_task] 연산으로 표시된 대로 상태가 `preparing` 또는 `running`인 태스크는 **실행 중**으로 계산됩니다.
- 더 이상 실행 자격이 없는 태스크는 **완료**로 계산됩니다. **완료**로 계산된 태스크는 일반적으로 성공적으로 완료되었거나, 비성공적으로 완료되었고 다시 시도 제한 횟수를 모두 사용했습니다. 

또한 Get Task Counts 연산은 성공 또는 실패한 태스크 수도 보고합니다. Batch는 [executionInfo][https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] 속성의 **result** 속성을 확인하여 작업의 성공 여부를 확인합니다.

    - 태스크 실행 결과가 `success`인 태스크는 **성공**으로 계산됩니다.
    - 태스크 실행 결과가 `failure`인 태스크는 **실패**로 계산됩니다.

태스크 상태에 대한 자세한 내용은 [태스크에 대한 정보 가져오기][rest_get_task]를 참조하세요.

다음 .NET 코드 예제는 상태별로 태스크 수를 가져오는 방법을 보여 줍니다. 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> REST 및 기타 지원되는 언어에 유사한 패턴을 사용하여 작업에 대한 태스크 수를 가져올 수 있습니다. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>태스크 수에 대한 일관성 확인

Batch 서비스는 비동기식 분산 시스템의 여러 부분에서 데이터를 수집하여 태스크 수를 집계합니다. 태스크 수가 정확한지 확인하기 위해 Batch는 시스템의 여러 구성 요소에 대한 일관성 검사를 수행하여 상태 수에 대한 추가적인 검증을 제공합니다. Batch는 작업에 200,000개 미만의 태스크가 있는 경우 이러한 일관성 검사를 수행합니다. 드물게 일관성 검사에서 오류가 발견되면 Batch는 일관성 검사의 결과를 기준으로 Get Tasks Counts 연산의 결과를 수정합니다. 일관성 검사는 Get Task Counts 연산을 사용하는 고객이 솔루션에 필요한 올바른 정보를 얻을 수 있도록 해주는 추가적인 조치입니다.

응답에서 **validationStatus** 속성은 Batch가 일관성 검사를 수행했는지 여부를 나타냅니다. Batch가 시스템에 포함된 실제 상태를 기준으로 상태 수를 검사하지 못했다면 **validationStatus** 속성은 `unvalidated`(으)로 설정됩니다. 성능상의 이유로, 작업에 200,000개 이상의 태스크가 포함되어 있는 경우 Batch가 일관성 검사를 수행하지 않으므로, 이 경우 **validationStatus** 속성을 `unvalidated`(으)로 설정할 수 있습니다. 그러나 이 경우에는 모든 제한된 데이터 손실 가능성이 매우 높으므로, 태스크 수가 틀릴 수도 있습니다. 

태스크 상태가 변경되면 집계 파이프라인은 몇 초 이내에 변경 사항을 처리합니다. Get Task Counts 연산은 해당 기간 이내에 업데이트된 태스크 수를 반영합니다. 그러나 집계 파이프라인이 태스크 상태의 변경을 놓칠 경우 해당 변경 사항은 다음 유효성 검사에 통과할 때까지 등록되지 않습니다. 이 시간 동안 태스크 수는 누락된 이벤트로 인해 약간 정확하지 않을 수 있지만, 다음 유효성 검사 통과 시 수정됩니다.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>작업의 태스크 수를 계산하는 모범 관행

Get Task Counts 연산 호출은 작업 상태별로 기본 수를 반환하는 가장 효율적인 방법입니다. Batch 서비스 버전 2017-06-01.5.1을 사용하고 있는 경우 Get Task Counts를 사용하도록 코드를 작성 또는 업데이트하는 것이 좋습니다.

2017-06-01.5.1 이전의 Batch 서비스 버전에서는 Get Task Counts 연산을 사용할 수 없습니다. 이전 버전의 서비스를 사용하는 경우, 대신 목록 쿼리를 사용하여 작업의 태스크를 계산하세요. 자세한 내용은 [쿼리를 만들어서 효율적으로 Batch 리소스 나열](batch-efficient-list-queries.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Batch 서비스의 개념 및 기능에 대한 자세한 내용은 [Batch 기능 개요](batch-api-basics.md)를 참조하세요. 이 문서에서는 풀, 계산 노드, 작업 및 태스크 등의 기본 Batch 리소스에 대해 설명하며 서비스의 기능에 대한 개요를 제공합니다.
* [Batch .NET 클라이언트 라이브러리](batch-dotnet-get-started.md) 또는 [Python](batch-python-tutorial.md)을 사용하여 배치 지원 응용 프로그램 개발에 대한 기본 사항을 알아봅니다. 이러한 소개 자료에서는 Batch 서비스를 사용하여 여러 계산 노드에서 워크로드를 실행하는 작업 응용 프로그램을 단계별로 안내합니다.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
