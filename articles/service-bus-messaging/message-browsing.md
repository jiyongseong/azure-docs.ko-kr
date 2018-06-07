---
title: "Azure Service Bus 메시지 찾아보기 | Microsoft Docs"
description: "Service Bus 메시지 찾아보기 및 미리 보기"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 124c4592a41bf9f3e2a148ba5c3b928bb051d160
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="message-browsing"></a>메시지 찾아보기

메시지 찾아보기("미리 보기")를 통해 Service Bus 클라이언트는 큐 또는 구독에 있는 모든 메시지를 일반적으로 진단 및 디버깅 용도로 열거할 수 있습니다.

미리 보기(peek) 작업은 `Receive()` 또는 `OnMessage()` 루프를 사용하여 즉시 가져올 수 있는 메시지뿐만 아니라 큐 또는 구독 메시지 로그에 있는 모든 메시지를 반환합니다. 각 메시지의 `State` 속성은 메시지가 활성(수신 가능)인지, [지연되었는지](message-deferral.md) 또는[예약되었는지](message-sequencing.md)를 알려줍니다.

사용되고 만료된 메시지는 비동기 "가비지 수집" 실행에 의해 정리되며 정확히 메시지가 만료될 때 정리되는 것은 아닙니다. 따라서 `Peek`은 큐 또는 구독에서 수신 작업이 다음에 호출되면 이미 만료되어 삭제될 메시지나 배달 못한 메시지를 반환할 수 있습니다.

이런 내용은 지연된 메시지를 큐에서 복구하려고 시도할 때 특히 유의해야 합니다. [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) 인스턴트를 통과 한 메시지는 어떠한 방법으로도(Peek에 의해 반환되는 경우에도) 일반 검색을 수행할 수 없습니다. Peek은 로그의 현재 상태를 반영하는 진단 도구이기 때문에 이러한 메시지를 반환하는 것은 의도적입니다.

또한 Peek은 잠긴 상태에서 다른 수신기가 현재 처리하고 있지만 아직 완료되지 않은 메시지를 반환합니다. 하지만 Peek은 연결이 끊긴 스냅숏을 반환하기 때문에 미리 본 메시지에서 메시지의 잠금 상태를 관찰할 수 없으며 응용 프로그램이 해당 메시지를 읽으려고 하면 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) 및 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 속성이 [InvalidOperationException](/dotnet/api/system.invalidoperationexception)을 throw합니다.

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 및 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 메서드는 모든 .NET 및 Java 클라이언트 라이브러리와 모든 수신기 개체(**MessageReceiver**, **MessageSession**, **QueueClient**, **SubscriptionClient**)에 존재합니다. Peek은 모든 큐와 구독 및 각각의 배달 못한 메시지 큐에서 작동합니다.

반복적으로 호출되면 Peek 메서드는 큐 또는 구독 로그에 있는 모든 메시지를 시퀀스 번호 순서대로(가장 낮은 시퀀스 번호에서 가장 높은 시퀀스 번호로) 열거합니다. 다음은 메시지를 큐에 넣는 순서이며 최종적으로 메시지가 검색되는 순서는 아닙니다.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_)는 여러 메시지를 검색하여 열거형으로 반환합니다. 메시지가 없는 경우 열거 개체는 null이 아니고 비어 있습니다.

또한 시작할 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)로 메서드의 오버로드를 시드한 다음 매개 변수가 없는 메서드 오버로드를 호출하여 추가로 열거할 수도 있습니다. **PeekBatch**는 동등하게 작동하지만 메시지 집합을 모두 한번에 검색합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)