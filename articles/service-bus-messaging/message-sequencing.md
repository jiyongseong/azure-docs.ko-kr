---
title: Azure Service Bus 메시지 순서 지정 및 타임스탬프 | Microsoft Docs
description: 타임스탬프를 통한 Service Bus 메시지 시퀀스 및 순서 유지
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="message-sequencing-and-timestamps"></a>메시지 순서 지정 및 타임스탬프 

순서 지정과 타임스탬프는 수신 또는 탐색된 메시지의 [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) 및 [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) 속성을 통해 모든 Service Bus 엔터티 및 표면에서 항상 사용되는 두 가지 기능입니다.

메시지의 절대적 순서가 중대하거나 소비자가 메시지에 대해 신뢰할 수 있는 고유 식별자를 필요로 할 경우 Broker가 메시지에 gap-free를 스탬프하여 큐나 토픽에 상대적인 시퀀스 번호를 증대할 수 있습니다. 분할된 엔터티의 경우 시퀀스 번호는 파티션에 상대적으로 발생됩니다.

**SequenceNumber** 값은 Broker에서 수락하여 저장할 때 메시지에 할당되는 고유의 64비트 정수이며 내부 식별자로 작동합니다. 분할된 엔터티의 경우 최상위 16비트는 파티션 식별자를 나타냅니다. 시퀀스 번호는 48/64비트 범위가 소진되었을 때 0으로 롤오버됩니다.

시퀀스 번호는 클라이언트가 아닌 중앙 및 중립 기관에서 할당하므로 고유 식별자로 신뢰할 수 있습니다. 또한 실제 도착 순서를 나타내며 순서 기준으로는 타임스탬프보다 정확합니다. 타임스탬프는 메시지 비율이 매우 높을 때 필요한 만큼의 확인을 갖지 못하고 노드 간에 Broker 소유권이 이전될 때 (미세한) 시계 오차가 발생할 수 있습니다.

제공 상품의 한정 수량을 공급 가능한 동안만 선착순으로 서비스하거나 콘서트 티켓을 판매하는 상황을 절대적 도착 순서가 중요한 경우의 예로 들 수 있습니다.

타임스탬프 기능은 메시지 도착의 UTC 시간을 정확하게 수집하여 **EnqueuedTimeUtc** 속성에 반영하는 신뢰할 수 있는 중립 기관의 역할을 합니다. 이 값은 작업 항목을 특정일 자정 전에 제출했으나 처리는 큐 백로그보다 훨씬 늦어지는 등, 기한이 중요한 비즈니스 시나리오에서 유용합니다.

## <a name="scheduled-messages"></a>예약된 메시지

지연 처리를 위해 큐 또는 토픽에 메시지를 제출할 수 있습니다. 특정 시간에 시스템에서 처리할 수 있게 되는 작업 예약을 예로 들 수 있습니다. 이 기능은 신뢰할 수 있는 분산된 시간 기반 스케줄러를 인식합니다.

예약된 메시지는 정의된 큐에 넣기 시간이 되어야 큐에 구체화됩니다. 이 시간 이전에는 예약된 메시지를 취소할 수 있습니다. 취소는 메시지를 삭제합니다.

일반 보내기 경로를 통해 메시지를 보낼 때 [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) 속성을 설정하거나, [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API를 통해 명시적으로 메시지를 예약할 수 있습니다. 후자의 경우 예약된 메시지의 **SequenceNumber**를 즉시 반환하므로 사용자가 나중에 필요한 경우 예약된 메시지를 취소하는 데 사용할 수 있습니다. 예약된 메시지와 해당 시퀀스 번호도 [메시지 찾아보기](message-browsing.md)로 검색할 수 있습니다.

예약된 메시지의 **SequenceNumber**는 메시지가 이 상태인 동안만 유효합니다. 메시지가 활성 상태로 전환되면 메시지는 현재 인스턴스의 큐에 넣어진 것처럼 큐에 추가되며 새 **SequenceNumber**를 포함합니다.

이 기능은 개별 메시지에 고정되며 메시지는 한 번만 큐에 넣을 수 있으므로 Service Bus가 메시지의 예약 반복을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)