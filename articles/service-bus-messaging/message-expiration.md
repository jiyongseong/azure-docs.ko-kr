---
title: "Azure Service Bus 메시지 만료 | Microsoft Docs"
description: "Azure Service Bus 메시지의 만료 및 TTL(Time to Live)"
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))

메시지 내부 페이로드 또는 메시지가 수신기에 전달하는 명령이나 조회는 거의 항상 특정 형식의 응용 프로그램 수준 만료 기한을 따릅니다. 이러한 기간 후에는 콘텐츠가 더 이상 배달되지 않거나 요청된 작업이 더 이상 실행되지 않습니다.

큐 및 토픽이 주로 응용 프로그램 또는 응용 프로그램 부분의 부분 실행 컨텍스트에서 사용되는 개발 및 테스트 환경에서는 다음 테스트 실행이 새로 시작될 수 있게 표준 테스트 메시지를 자동으로 가비지 수집되도록 할 수 있습니다.

모든 개별 메시지에 대한 만료는 상대적 기간을 지정하는 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 시스템 속성을 설정하여 제어할 수 있습니다. 만료는 메시지가 엔터티 큐에 추가될 때 절대 인스턴스가 됩니다. 이때 [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) 속성 값은 [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)입니다.

**ExpiresAtUtc** 인스턴트 후에 메시지는 검색할 수 없게 됩니다. 만료는 현재 배달 잠금 처리된 메시지에는 영향을 주지 않습니다. 이러한 메시지는 계속 정상적으로 처리됩니다. 잠금이 만료되거나 메시지가 중단되면 만료가 즉시 적용됩니다.

메시지가 잠금 상태인 경우 응용 프로그램이 만료된 메시지를 소유하고 있을 수 있습니다. 응용 프로그램이 메시지를 계속 처리하려고 할지 또는 메시지를 중단하도록 선택할지는 구현자가 결정합니다.

## <a name="entity-level-expiration"></a>엔터티 수준 만료

큐 또는 토픽에 전송된 모든 메시지는 [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) 속성을 사용해서 엔터티 수준에서 설정되는 기본 만료를 따릅니다. 이러한 기본 만료는 메시지 생성 동안 포털에서 설정되며 나중에 조정될 수 있습니다. [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)가 명시적으로 설정되지 않은 엔터티로 보낸 모든 메시지에는 기본 만료가 사용됩니다. 기본 만료는 **TimeToLive** 값의 최대값으로도 사용됩니다. **TimeToLive** 만료가 기본값보다 더 긴 메시지는 큐에 추가되기 전에 자동으로 **defaultMessageTimeToLive** 값으로 조정됩니다.

만료된 메시지는 [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) 속성을 설정하거나 포털에서 해당 확인란을 선택하여 [배달 못 한 메시지 큐](service-bus-dead-letter-queues.md)로 선택적으로 이동할 수 있습니다. 이 옵션을 사용하지 않도록 설정하면 만료된 메시지가 삭제됩니다. 배달 못 한 메시지 큐로 이동되는 만료된 메시지는 브로커가 사용자 속성 섹션에 저장하는 [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) 속성(이 경우 [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq))을 평가하여 다른 배달 못 한 메시지와 구분할 수 있습니다.

이전 경우에서 메시지가 잠금 상태이고 엔터티에 해당 플래그가 설정된 경우 만료되지 않도록 보호하면 메시지는 잠금이 중단되거나 만료될 때 배달 못한 메시지 큐로 이동됩니다. 그러나 명목상으로 만료되었더라도 메시지가 성공적으로 확인되고 응용 프로그램이 성공적으로 처리했다고 가정되면 이 위치로 이동되지 않습니다.

[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 및 만료 시 자동(및 트랜잭션) 배달 못 한 메시지 큐는 마감일 내에 처리기 또는 처리기 그룹에 지정된 작업이 마감일 도달 시 처리를 위해 검색될지 여부를 확실히 설정할 수 있는 유용한 도구입니다.

예를 들어 확장이 제한된 백 엔드에서 작업을 안정적으로 실행해야 하며, 경우에 따라 트래픽 급증이 발생하거나 해당 백 엔드 가용성의 영향을 받지 않도록 하려는 웹 사이트가 있다고 가정해보겠습니다. 일반적인 경우 제출된 사용자 데이터에 대한 서버 쪽 처리기가 해당 정보를 큐에 밀어 넣고, 이후에 트랜잭션을 회신 큐로 성공적으로 처리했음을 확인하는 응답을 수신하게 됩니다. 트래픽이 급증하고 백 엔드 처리기가 시간 안에 해당 백로그 항목을 처리할 수 없는 경우 만료된 작업은 배달 못 한 메시지 큐에 반환됩니다. 대화형 사용자에게 요청된 작업이 평소보다 약간 더 오래 걸린다고 알릴 수 있으며, 요청은 처리 경로의 다른 큐에 배치될 수 있습니다. 이 경우 최종 처리 결과는 전자 메일로 사용자에게 전송됩니다. 

## <a name="temporary-entities"></a>임시 엔터티

Service Bus 큐, 토픽 및 구독을 임시 엔터티로 생성할 수 있습니다. 이러한 항목은 지정된 기간 동안 사용되지 않으면 자동으로 제거됩니다.
 
자동 정리 기능은 테스트 또는 디버깅 실행이 중단되기 때문에 엔터티가 동적으로 생성되고 사용 후에 정리되지 않는 개발 및 테스트 시나리오에 유용합니다. 이 기능은 응용 프로그램이 응답을 웹 서버 프로세스로 다시 수신하거나 비교적 수명이 짧아서 개체 인스턴스가 사라질 때 해당 엔터티를 안정적으로 정리하기 어려운 개체로 다시 수신하기 위해 회신 큐와 같은 동적 엔터티를 만들 경우에도 유용합니다.

이 기능은 [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) 속성을 사용하여 사용하도록 설정합니다. 이 속성은 엔터티를 자동으로 삭제하기까지 유휴(사용되지 않은) 상태를 유지해야 하는 기간으로 설정됩니다. 최소 기간은 5분입니다.
 
**autoDeleteOnIdle** 속성은 Azure Resource Manager 작업 또는 .NET Framework 클라이언트 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API를 통해 설정해야 합니다. 포털을 통해서는 설정할 수 없습니다.


## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)