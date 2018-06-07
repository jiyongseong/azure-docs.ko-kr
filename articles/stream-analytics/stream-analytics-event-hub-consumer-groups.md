---
title: Azure Stream Analytics에서 이벤트 허브 수신기 문제 해결
description: 이 문서에서는 Stream Analytics 작업의 Event Hubs 입력에 여러 소비자 그룹을 사용하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Azure Stream Analytics에서 이벤트 허브 수신기 문제 해결

Azure Stream Analytics에서 Azure Event Hubs를 사용하여 작업에서 데이터를 수집하거나 출력할 수 있습니다. Event Hubs 사용에 대한 모범 사례는 여러 소비자 그룹을 사용하여 작업 확장성을 보장하는 것입니다. 한 가지 이유는 특정 입력에 대한 Stream Analytics 작업의 판독기 수가 단일 소비자 그룹의 판독기 수에 영향을 준다는 것입니다. 수신자의 정확한 수는 확장 토폴로지 논리에 대한 내부 구현 세부 정보를 기반으로 합니다. 수신자 수는 외부적으로 노출되지 않습니다. 판독기 수는 작업 시작 시간에 또는 작업을 업그레이드하는 동안에 바뀔 수 있습니다.

수신기 수가 최대값을 초과하는 경우 `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.` 오류가 표시됩니다.

> [!NOTE]
> 작업을 업그레이드하는 동안 판독기 수가 변경되면 일시적인 경고가 감사 로그에 기록됩니다. Stream Analytics 작업은 자동으로 이러한 일시적인 문제를 복구합니다.

## <a name="add-a-consumer-group-in-event-hubs"></a>Event Hubs에 소비자 그룹 추가
Event Hubs 인스턴스에 새 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. Azure 포털에 로그인합니다.

2. Event Hubs를 찾습니다.

3. **엔터티** 제목 아래에서 **Event Hubs**를 선택합니다.

4. 이름으로 이벤트 허브를 선택합니다.

5. **Event Hubs 인스턴스** 페이지의 **엔터티** 제목 아래에서 **소비자 그룹**을 선택합니다. 이름이 **$Default**인 소비자 그룹이 표시됩니다.

6. **+소비자 그룹**을 선택하여 새 소비자 그룹을 추가합니다. 

   ![Event Hubs에 소비자 그룹 추가](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Stream Analytics 작업에서 이벤트 허브를 가리키는 입력을 만들 때 소비자 그룹을 지정했습니다. 아무 것도 지정하지 않으면 $Default가 사용됩니다. 새 소비자 그룹을 만든 후에는 Stream Analytics 작업에서 이벤트 허브 입력을 편집하고 새 소비자 그룹의 이름을 지정합니다.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>파티션당 판독기 수가 5개 Event Hubs 제한을 초과

스트리밍 쿼리 구문이 동일한 입력 이벤트 허브 리소스를 여러 번 참조하는 경우 작업 엔진이 해당 소비자 그룹의 쿼리에 여러 판독기를 사용할 수 있습니다. 동일한 소비자 그룹에 대한 참조가 너무 많으면 작업이 한도 5를 초과하여 오류가 throw 될 수 있습니다. 이 경우 다음 섹션에 설명된 솔루션으로 여러 소비자 그룹의 여러 입력을 사용하여 추가로 분할하면 됩니다. 

파티션당 읽기 권한자 수가 5개 Event Hubs 제한을 초과하는 시나리오에는 다음이 포함됩니다.

* 여러 SELECT 문: **동일한** 이벤트 허브 입력을 참조하는 여러 SELECT 문을 사용하는 경우 각 SELECT 문으로 인해 새 수신기가 생성됩니다.
* UNION: UNION을 **동일한** 이벤트 허브 및 소비자 그룹을 참조하는 여러 입력이 발생할 수 있습니다.
* SELF JOIN: SELF JOIN 작업을 사용하면 **동일한** 이벤트 허브를 여러 번 참조할 수 있습니다.

## <a name="solution"></a>해결 방법

다음 모범 사례는 파티션당 읽기 권한자 수가 5개 Event Hubs 제한을 초과하는 시나리오를 완화하는 데 도움이 될 수 있습니다.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH 절을 사용하여 쿼리를 여러 단계로 분할

WITH 절은 쿼리의 FROM 절에서 참조할 수 있는 명명된 임시 결과 집합을 지정합니다. 단일 SELECT 문의 실행 범위에서 WITH 절을 정의합니다.

예를 들어 아래 쿼리 대신,

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

다음 쿼리를 사용합니다.

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>입력이 다른 소비자 그룹으로 바인딩되었는지 확인

세 개 이상의 입력이 동일한 Event Hubs 소비자 그룹에 연결된 쿼리의 경우 개별 소비자 그룹을 만듭니다. 이를 위해서는 추가 Stream Analytics 입력을 만들어야 합니다.


## <a name="next-steps"></a>다음 단계
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
