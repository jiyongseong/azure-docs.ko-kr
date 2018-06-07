---
title: Scheduler 개념, 용어 및 엔터티 | Microsoft Docs
description: 작업 및 작업 컬렉션을 포함하는 Azure Scheduler 개념, 용어 및 엔터티 계층 구조입니다.  예약된 작업의 자세한 예를 보여줍니다.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 91302d57c43a6c9d14aeeee95df3d61fa6f73172
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Scheduler 개념, 용어 + 엔터티 계층 구조
## <a name="scheduler-entity-hierarchy"></a>Scheduler 엔터티 계층 구조
다음 표에서 Scheduler API에서 노출 하거나 사용하는 주 리소스를 설명 합니다.

| 리소스 | 설명 |
| --- | --- |
| **작업 컬렉션** |작업 컬렉션은 작업 그룹을 포함하며 컬렉션 내에서 작업이 공유하는 설정, 할당량 및 제한을 유지합니다. 작업 컬렉션은 구독 소유자가 만들며, 사용 또는 응용 프로그램 경계를 기준으로 작업을 함께 그룹화합니다. 한 지역으로 제한됩니다. 또한 해당 컬렉션에 있는 모든 작업의 사용량을 제한하는 할당량을 적용할 수 있습니다. 할당량은 MaxJobs 및 MaxRecurrence을 포함합니다. |
| **작업** |작업은 간단하거나 복잡한 실행 전략을 통해 단일 반복 작업을 정의합니다. 작업은 HTTP, 저장소 큐, 서비스 버스 큐 또는 서비스 버스 항목 요청을 포함할 수 있습니다. |
| **작업 기록** |작업 기록의 경우 작업 실행에 대한 세부 정보를 나타냅니다. 응답 세부 정보와 함께 성공 또는 실패를 포함합니다. |

## <a name="scheduler-entity-management"></a>Scheduler 엔터티 관리
높은 수준에서 스케줄러 및 서비스 관리 API는 리소스에서 다음 작업을 노출합니다.

| 기능 | 설명 및 URI 주소 |
| --- | --- |
| **작업 컬렉션 관리** |작업 컬렉션과 그 안에 포함된 작업의 생성 및 수정을 위한 GET, PUT 및 DELETE 지원. 작업 컬렉션을 작업에 대한 컨테이너이며 할당량 및 공유 설정에 매핑됩니다. 나중에 설명하는 할당량은 최대 작업 수와 최소 반복 간격을 예로 듭니다. <p>PUT 및 DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **작업 관리** |작업의 생성 및 수정 지원을 위한 GET, PUT, POST, PATCH 및 DELETE 지원  모든 작업은 기존 작업 컬렉션에 속해야 하며 암시적으로 만들어지지 않습니다. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **작업 기록 관리** |GET은 작업 경과 시간, 작업 실행 결과 등, 60일 간의 작업 실행 기록을 가져옵니다. 상태를 기초로 한 필터링을 위해 쿼리 문자열 매개 변수 지원을 추가합니다. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>작업 유형
HTTP 작업(SSL을 지원하는 HTTPS 작업 포함), 저장소 큐 작업, 서비스 버스 큐 작업 및 서비스 버스 항목 작업 등 여러 가지 유형의 작업이 있습니다. HTTP 작업은 기존 작업 부하 또는 서비스의 끝점을 사용하는 경우에 이상적입니다. 저장소 큐 작업을 사용하여 저장소 큐에 메시지를 게시할 수 있으므로 저장소 큐를 사용하는 워크로드에 이상적입니다. 마찬가지로, 서비스 버스 작업은 서비스 버스 큐와 토픽을 사용하는 워크로드에 적합합니다.

## <a name="the-job-entity-in-detail"></a>"Job" 엔터티 세부 정보
기본 수준에서, 예약된 작업에는 다음과 같은 여러 부분이 있습니다.

* 작업 타이머가 발생할 때 수행할 작업  
* (선택 사항) 작업 실행 시간  
* (선택 사항) 작업 반복 시기 및 빈도  
* (선택 사항) 기본 동작 실패 시 실행할 동작  

내부적으로, 예약된 작업에는 다음 예약 실행 시간 등, 시스템에서 제공하는 데이터도 포함됩니다.

다음 코드는 예약된 작업의 자세한 예를 제공합니다. 세부 정보는 이후의 섹션에서 제공합니다.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

위의 예제 예약된 작업에서와 같이 작업 정의에는 여러 부분이 있습니다.

* 시작 시간("startTime")  
* 오류 동작("errorAction")을 포함하는 동작("action")
* 되풀이("recurrence")  
* 상태(“state”)  
* 상태(“status”)  
* 재시도 정책("retryPolicy")  

각각에 대해 자세히 살펴보겠습니다.

## <a name="starttime"></a>startTime
"startTime"은 시작 시간이며, 호출자가 통신 중의 시간대 오프셋을 [ISO-8601 형식](http://en.wikipedia.org/wiki/ISO_8601)으로 지정할 수 있습니다.

## <a name="action-and-erroraction"></a>action 및 errorAction
"action"은 각각의 발생 시 호출되는 동작이며 서비스 호출 유형을 설명합니다. 동작은 제공된 일정에 따라 실행되는 것입니다. Scheduler는 HTTP, 저장소 큐, 서비스 버스 항목 또는 서비스 버스 큐 작업을 지원합니다.

위 예의 동작은 HTTP 동작입니다. 다음은 저장소 큐 동작의 예입니다.

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

다음은 서비스 버스 항목 작업의 예입니다.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

다음은 서비스 버스 큐 동작의 예입니다.

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

“errorAction”은 오류 처리기로, 주 동작 실패 시 호출되는 동작입니다. 이 변수를 사용하여 오류 처리 끝점을 호출하거나 사용자 알림을 보낼 수 있습니다. 주 끝점을 사용할 수 없을 때(예: 끝점 사이트의 장애) 보조 끝점에 연결하거나, 오류 처리 끝점을 알리는 데 사용할 수 있습니다. 기본 동작과 마찬가지로 오류 동작은 다른 동작에 따라 단순 또는 복합 로직이 될 수 있습니다. SAS 토큰을 만드는 방법을 알아보려면 [공유 액세스 서명 만들기 및 사용](https://msdn.microsoft.com/library/azure/jj721951.aspx)을 참조하세요.

## <a name="recurrence"></a>되풀이
되풀이에는 여러 부분이 있습니다.

* 빈도: 분, 시간, 일, 주, 월, 년 중 하나  
* 간격: 되풀이를 위해 제공한 빈도의 간격  
* 정해진 일정: 되풀이할 분, 시간, 요일, 월, 날짜 지정  
* 개수: 발생 횟수  
* 종료 시간: 지정된 종료 시간 이후 작업이 실행되지 않습니다.  

JSON 정의에 지정된 되풀이 개체가 있으면 작업이 반복됩니다. Count 및 endTime이 모두 지정된 경우 먼저 발생하는 완료 규칙이 적용 됩니다.

## <a name="state"></a>state
작업의 상태는 enabled(활성화), disabled(비활성화), completed(완료) 또는 faulted(결함) 등, 4가지 값 중 하나입니다. 작업을 PUT 또는 PATCH하여 활성화 또는 비활성화 상태로 업데이트할 수 있습니다. 작업이 완료되었거나 오류가 발생한 경우 최종 상태를 업데이트할 수 없습니다(작업 삭제는 가능함). state 속성의 예는 다음과 같습니다.

        "state": "disabled", // enabled, disabled, completed, or faulted
완료 및 오류 작업은 60일 후 삭제됩니다.

## <a name="status"></a>status
Scheduler 작업이 시작되면 현재 작업 상태에 대한 정보가 반환됩니다. 이 개체는 사용자가 설정할 수 없고 시스템에서 설정합니다. 그러나 작업 상태를 쉽게 확보할 수 있게 작업 개체(별도의 연결 리소스가 아님)에 포함되어 있습니다.

작업 상태에는 이전 실행 시간(있는 경우), 다음 예약 실행 시간(진행 중인 작업의 경우), 작업 실행 수가 포함됩니다.

## <a name="retrypolicy"></a>retryPolicy
Scheduler 작업이 실패할 경우 작업 재시도 여부 및 방법을 결정하는 재시도 정책을 지정할 수 있습니다. 이 항목은 **retryType** 개체에서 결정합니다. 재시도 정책이 없으면 **none**으로 설정됩니다. 재시도 정책이 있는 경우 **fixed**로 설정합니다.

재시도 정책을 설정하기 위해 재시도 간격(**retryInterval**)과 재시도 횟수(**retryCount**) 등, 두 추가 설정을 지정할 수 있습니다.

**retryInterval** 개체로 지정한 재시도 간격은 재시도 간 간격입니다. 기본값은 30초이며 구성 가능한 최소값은 15초, 최대값은 18개월입니다. ISO 8601 형식으로 정의됩니다. 마찬가지로, 재시도 횟수 값은 **retryCount** 개체로 정의하며 재시도를 시도하는 횟수입니다. 기본값은 4이고 최대값은 20입니다. **retryInterval** 및 **retryCount**는 모두 선택 사항입니다. **retryType**이 **fixed**로 설정되고 명시적으로 지정한 값이 없을 때 기본값으로 제공됩니다.

## <a name="see-also"></a>참고 항목
 [Scheduler란?](scheduler-intro.md)

 [Azure Portal에서 Scheduler 사용 시작](scheduler-get-started-portal.md)

 [Azure Scheduler의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure Scheduler 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 아웃바운드 인증](scheduler-outbound-authentication.md)

