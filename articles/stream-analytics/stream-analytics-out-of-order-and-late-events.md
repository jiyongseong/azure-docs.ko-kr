---
title: Azure Stream Analytics에서 이벤트 순서 및 지연 처리
description: 이 아티클에서는 Stream Analytics가 데이터 스트림의 잘못된 순서 또는 지연 이벤트를 처리하는 방법을 알아봅니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: e407a95d3ac858ea7180a75f9fbfc399860ad378
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Azure Stream Analytics 이벤트 순서 고려 사항

## <a name="arrival-time-and-application-time"></a>도착 시간 및 응용 프로그램 시간

이벤트의 임시 데이터 스트림에서는 각 이벤트에 타임스탬프가 할당됩니다. Azure Stream Analytics는 도착 시간이나 응용 프로그램 시간 중 하나를 사용하여 각 이벤트에 타임스탬프를 할당합니다. 이벤트에 할당된 타임스탬프는 **System.Timestamp** 열에 있습니다. 

도착 시간은 이벤트가 소스에 도달하면 입력 소스에서 할당됩니다. 이벤트 허브 입력의 경우 **EventEnqueuedTime** 속성을 사용하고 Blob 입력의 경우 [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) 속성을 사용하여 도착 시간에 액세스할 수 있습니다. 

응용 프로그램 시간은 이벤트 생성 시에 할당되며 페이로드의 일부분입니다. 응용 프로그램 시간을 기준으로 이벤트를 처리하려면 select 쿼리에서 **Timestamp by** 절을 사용합니다. **Timestamp by** 절이 없으면 이벤트는 도착 시간을 기준으로 처리됩니다. 

Azure Stream Analytics는 타임스탬프 순서로 출력을 생성하며 잘못된 순서 데이터를 처리하기 위한 설정을 제공합니다.


## <a name="handling-of-multiple-streams"></a>여러 스트림 처리

Azure Stream Analytics 작업은 다음과 같은 경우에 여러 타임라인의 이벤트를 결합합니다.

* 여러 파티션에서 출력 생성. 명시적 **Partition by PartitionId** 절이 없는 쿼리는 모든 파티션의 이벤트를 결합해야 합니다.
* 서로 다른 두 개 이상의 입력 원본의 합집합
* 입력 원본 조인

여러 타임라인이 결합되는 시나리오에서 Azure Stream Analytics는 결합된 모든 원본이 적어도 *t1* 시간이 된 후에만 타임스탬프 *t1*에 대한 출력을 생성합니다. 예를 들어 쿼리가 두 개의 파티션이 있는 이벤트 허브 파티션에서 읽는다고 가정합니다. 파티션 중 하나인 *P1*에는 *t1* 시간까지 이벤트가 있습니다. 다른 파티션 *P2*에는 *t1 + x* 시간까지 이벤트가 있습니다. *t1* 시간까지 출력이 생성됩니다. 그러나 명시적 **Partition by PartitionId** 절이 있으면 두 파티션은 모두 독립적으로 진행합니다.

일부 파티션에서는 데이터가 없는 문제를 처리하는 데 지연 도착 허용 시간 설정이 사용됩니다.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>지연 도착 허용 시간 및 잘못된 순서 허용 시간 구성
순서가 올바르지 않은 입력 스트림은 다음 중 하나입니다.
* 정렬됨(따라서 지연됨)
* 사용자 지정 정책에 따라 시스템에 의해 조정됨

응용 프로그램 시간을 기준으로 처리할 때 Stream Analytics에서는 지연 이벤트와 잘못된 순서 이벤트를 허용합니다. 다음 설정은 Azure Portal의 **이벤트 순서 지정** 옵션에서 사용할 수 있습니다. 

![Stream Analytics 이벤트 처리](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>지연 도착 허용 시간
지연 도착 허용 시간은 응용 프로그램 시간을 기준으로 처리할 때만 적용할 수 있습니다. 그렇지 않은 경우 이 설정은 무시됩니다.

지연 도착 허용 시간은 도착 시간과 응용 프로그램 시간 사이의 최대 차이입니다. 이벤트가 지연 도착 허용 시간보다 늦게 도착하면(예: 응용 프로그램 시간 *app_t* < 도착 시간 *arr_t* - 지연 도착 정책 허용 시간 *late_t*), 이벤트는 최대 지연 도착 허용 시간(*arr_t* - *late_t*)으로 조정됩니다. 지연 도착 시간은 이벤트 생성 시간과 입력 소스에서 이벤트를 수신하는 시간 사이의 최대 지연입니다. 

같은 입력 스트림이나 여러 입력 스트림의 여러 파티션이 결합될 때의 지연 도착 허용 시간은 모든 파티션이 새 데이터 도착을 대기하는 최대 시간입니다. 

먼저 지연 도착 허용 시간을 기준으로 조정됩니다. 그런 다음, 잘못된 순서 허용 시간을 기준으로 조정됩니다. 이벤트에 할당된 최종 타임스탬프는 **System.Timestamp** 열에 있습니다.

### <a name="out-of-order-tolerance"></a>잘못된 순서 허용 시간
순서가 올바르지 않으나 잘못된 순서 허용 시간 이내에 도착하는 이벤트는 타임스탬프 기준으로 다시 정렬됩니다. 이 허용 시간보다 늦게 도착하는 이벤트는 다음과 같이 처리됩니다.
* **조정**: 최신 허용 가능한 시간에 도착한 것처럼 조정합니다. 
* **삭제**: 삭제합니다.

Stream Analytics가 “잘못된 순서 허용 시간” 이내에 수신된 이벤트를 다시 정렬할 경우 쿼리의 출력은 잘못된 순서 허용 시간만큼 지연됩니다.

### <a name="early-events"></a>초기 이벤트
응용 프로그램 시간으로 처리하는 경우 응용 프로그램 시간이 도착 시간보다 5분 넘게 앞서는 이벤트는 선택한 구성 옵션에 따라 삭제되거나 조정됩니다.

### <a name="example"></a>예

* 지연 도착 허용 시간 = 10분<br/>
* 잘못된 순서 허용 시간 = 3분<br/>
* 응용 프로그램 시간을 기준으로 처리<br/>
* 이벤트:
   1. **응용 프로그램 시간** = 00:00:00, **도착 시간** = 00:10:01, **System.Timestamp** = 00:00:01 - (**도착 시간 - 응용 프로그램 시간**)이 지연 도착 허용 시간보다 크므로 조정됨
   2. **응용 프로그램 시간** = 00:00:01, **도착 시간** = 00:10:01, **System.Timestamp** = 00:00:01 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음
   3. **응용 프로그램 시간** = 00:10:00, **도착 시간** = 00:10:02, **System.Timestamp** = 00:10:00 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음
   4. **응용 프로그램 시간** = 00:09:00, **도착 시간** = 00:10:03, **System.Timestamp** = 00:09:00 - 응용 프로그램 시간이 잘못된 순서 허용 시간 이내이므로 원본 타임스탬프로 수락됨
   5. **응용 프로그램 시간** = 00:06:00, **도착 시간** = 00:10:04, **System.Timestamp** = 00:07:00 - 응용 프로그램 시간이 잘못된 순서 허용 시간보다 이전이므로 조정됨

### <a name="practical-considerations"></a>실제 고려 사항
앞서 설명한 것처럼 지연 도착 허용 시간은 응용 프로그램 시간과 도착 시간 사이의 최대 차이입니다. 응용 프로그램 시간에 따라 처리할 경우, 구성된 지연 도착 허용 시간보다 늦은 이벤트는 잘못된 순서 허용 시간 설정이 적용되기 전에 조정됩니다. 따라서 유효 잘못된 순서 이벤트는 지연 도착 허용 시간 및 잘못된 순서 허용 시간의 최소값입니다.

스트림 내의 잘못된 순서 이벤트에 대한 이유는 다음과 같습니다.
* 보낸 사람 간의 클록 스큐(clock skew)
* 보낸 사람과 입력 이벤트 원본 간의 가변 대기 시간

도착 지연에 대한 이유는 다음과 같습니다.
* 보낸 사람이 특정 시간 간격 동안의 이벤트를 해당 간격 이후에 일괄로 처리하고 전송
* 보낸 사람이 이벤트를 보내시는 간과 입력 원본에서 이벤트를 수신하는 시간 사이에 대기 시간 발생

특정 작업에 대한 지연 도착 허용 시간 및 잘못된 순서 허용 시간을 구성할 경우 정확성, 대기 시간 요구 사항 및 이전 요인도 고려합니다.

다음은 몇 가지 예제입니다.

#### <a name="example-1"></a>예 1 
쿼리에 **Partition by PartitionId** 절이 있습니다. 단일 파티션 내에서 이벤트가 동기 전송 방법을 사용하여 전송됩니다. 동기 전송 방법은 이벤트가 전송될 때까지 차단됩니다.

이 경우 이벤트가 명시적으로 확인되면서 순서대로 전송된 후 다음 이벤트가 전송되므로 잘못된 순서 이벤트 수는 0입니다. 지연 도착은 이벤트가 생성되는 시점과 이벤트를 전송하는 시점 사이의 최대 지연 시간에 보낸 사람과 입력 원본 사이의 최대 대기 시간을 더한 값이 됩니다.

#### <a name="example-2"></a>예 2
쿼리에 **Partition by PartitionId** 절이 있습니다. 단일 파티션 내에서 이벤트가 비동기 전송 방법을 사용하여 전송됩니다. 비동기 전송 방법은 동시에 여러 전송을 시작할 수 있으므로 잘못된 순서 이벤트가 발생할 수 있습니다.

이 경우 잘못된 순서 이벤트 및 지연 도착 이벤트의 최대 크기는 적어도 이벤트가 생성되는 시점과 이벤트를 전송하는 시점 사이의 최대 지연 시간에 보낸 사람과 입력 원본 사이의 최대 대기 시간을 더한 값이 됩니다.

#### <a name="example-3"></a>예 3
쿼리에 **Partition by PartitionId** 절이 없으며 2개 이상의 파티션이 있습니다.

구성은 예제 2와 동일합니다. 그러나 파티션 중 하나에서 데이터가 부족할 경우 추가 지연 도착 허용 시간만큼 출력이 지연될 수 있습니다.

## <a name="handling-event-producers-with-differing-timelines"></a>다른 타임라인으로 이벤트 생산자 처리
경우에 따라 단일 입력 이벤트 스트림에는 개별 장치와 같은 여러 이벤트 생산자에서 시작되는 이벤트가 포함됩니다. 이전에 설명한 이유로 인해 이러한 이벤트가 잘못된 순서로 도착할 수 있습니다. 이러한 시나리오에서는 이벤트 생산자 전체에는 잘못된 순서가 많을 수 있지만 단일 생산자 이벤트 내의 잘못된 순서는 적거나 존재하지 않을 수 있습니다.

Azure Stream Analytics는 잘못된 순서 이벤트를 처리하기 위한 일반적인 메커니즘을 제공합니다. 이러한 메커니즘을 사용하면 지연(낙오된 이벤트가 시스템에 도달하기를 기다리는 동안), 삭제되거나 조정된 이벤트 또는 두 이벤트가 모두 처리됩니다.

대부분의 경우에는 원하는 쿼리가 서로 다른 이벤트 생산자의 이벤트를 개별적으로 처리하고 있습니다. 예를 들어 장치별로 창별 이벤트를 집계할 수 있습니다. 이러한 경우 다른 이벤트 생산자가 포착할 때까지 기다리는 동안 하나의 이벤트 생산자에 해당하는 출력을 지연할 필요가 없습니다. 즉, 생산자 간의 시간차를 처리할 필요가 없습니다. 무시해도 됩니다.

물론 이는 출력 이벤트 자체가 타임스탬프와 관련하여 순서가 잘못되었음을 의미합니다. 다운스트림 소비자는 해당 동작을 처리할 수 있어야 합니다. 그러나 출력의 모든 이벤트가 올바릅니다.

Azure Stream Analytics는 [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) 절을 사용하여 이 기능을 구현합니다.

## <a name="summary"></a>요약
* 정확성 및 대기 시간 요구 사항을 기준으로 지연 도착 허용 시간 및 잘못된 순서 시간을 구성합니다. 또한 이벤트가 전송되는 방법을 고려합니다.
* 잘못된 순서 허용 시간은 지연 도착 허용 시간보다 작은 것이 좋습니다.
* 여러 타임라인을 결합하는 경우 원본 또는 파티션 중 하나에서 데이터가 부족할 경우 추가 지연 도착 허용 시간만큼 출력이 지연될 수 있습니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
