---
title: "Azure Service Bus와 쌍을 이루는 연결된 네임스페이스 | Microsoft Docs"
description: "쌍을 이루는 네임스페이스 구현의 세부 사항 및 비용"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>쌍을 이루는 네임스페이스 구현의 세부 사항 및 비용의 영향

[PairNamespaceAsync][PairNamespaceAsync] 메서드는 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 인스턴스를 사용하여 사용자 대신 눈에 보이는 작업을 수행합니다. 이 기능을 사용할 때는 비용을 고려해야 하기 때문에, 이러한 작업을 이해하고 사용할 때 어떤 점을 예측해야 하는지 알아두면 유용합니다. API는 사용자를 대신해 다음과 같은 자동 동작을 수행합니다.

* 백로그 큐를 만듭니다.
* 큐 또는 토픽에 메시지를 전달하는 [MessageSender][MessageSender] 개체를 만듭니다.
* 메시징 엔터티를 사용할 수 없게 되면, 해당 엔터티를 다시 사용할 수 있게 되는 시기를 감지하기 위해 Ping 메시지를 보냅니다.
* 선택적으로 백로그 큐에서 기본 큐로 메시지를 옮기는 "메시지 펌프" 집합을 만듭니다.
* 기본 및 보조 [MessagingFactory][MessagingFactory] 인스턴스의 닫기/오류를 조정합니다.

간략히 보면 이 기능은 다음과 같이 작동합니다. 일단 기본 엔터티가 정상이면 아무 동작도 변경되지 않습니다. [FailoverInterval][FailoverInterval] 기간이 경과되고, 기본 엔터티에서 일시적이 아닌 [MessagingException][MessagingException] 또는 [TimeoutException][TimeoutException] 다음에 성공적인 보내기가 없다고 보여질 경우 다음과 같은 동작이 발생합니다.

1. 기본 엔터티로 작업 보내기가 비활성화되고 기본 엔터티에 성공적으로 전달될 때까지 Ping을 보냅니다.
2. 임의 백로그 큐가 선택됩니다.
3. [BrokeredMessage][BrokeredMessage] 개체를 선택된 백로그 큐로 라우팅합니다.
4. 선택한 백로그 큐로 보내기 작업에 실패하면 사용 대기 목록에서 현재 큐를 제외하고 새 큐를 선택합니다. [MessagingFactory][MessagingFactory] 인스턴스의 모든 발신자가 이 실패에 대해 알게 됩니다.

다음 그림은 작동 순서를 보여줍니다. 먼저 발신자가 메시지를 보냅니다.

![쌍을 이루는 네임스페이스][0]

기본 큐로 보내기가 실패하면 발신자는 임의로 선택한 백로그 큐로 메시지를 보내기 시작합니다. 동시에 Ping 작업을 시작합니다.

![쌍을 이루는 네임스페이스][1]

이 시점에서 메시지는 계속 보조 큐에 있으며, 기본 큐로 전달되지 않은 상태입니다. 기본 큐가 다시 정상 상태가 되면 하나 이상의 프로세스가 사이펀을 실행해야 합니다. 사이펀은 다양한 모든 백로그 큐에서 적절한 대상 엔터티(큐 또는 토픽)로 메시지를 전달합니다.

![쌍을 이루는 네임스페이스][2]

이 항목의 나머지 부분에서는 각 작업이 어떻게 작동하는지 자세히 설명합니다.

## <a name="creation-of-backlog-queues"></a>백로그 큐 만들기
[SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 개체가 [PairNamespaceAsync][PairNamespaceAsync] 메서드에 전달됩니다. 이 개체는 사용자가 사용하려는 백로그 큐의 수를 나타냅니다. 이제 다음 속성이 명시적으로 설정(다른 모든 값은 [QueueDescription][QueueDescription] 기본값으로 설정)된 상태로 각 백로그 큐가 생성됩니다.

| path | [기본 네임스페이스]/x-servicebus-transfer/[index] 여기서 [index]는 [0, BacklogQueueCount] 사이의 값입니다. |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1분 |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

예를 들어 네임스페이스 **contoso**에 대한 첫 백로그 큐 `contoso/x-servicebus-transfer/0`가 생성됩니다.

큐를 만들 때 코드는 먼저 해당 큐가 이미 있는지 확인합니다. 해당 큐가 없다면 큐가 생성 됩니다. 코드는 "추가" 백로그 큐를 정리하지 않습니다. 구체적으로 설명하면, 기본 네임스페이스 **contoso**를 사용하는 응용 프로그램이 5개의 백로그 큐를 요청했지만 `contoso/x-servicebus-transfer/7` 경로에 백로그 큐가 있다면, 추가 백로그 큐는 그대로 유지되고 사용되지만 사용되지는 않습니다. 시스템은 사용되지 않는 추가 백로그 큐가 존재하는 것을 명시적으로 허용하고 있습니다. 사용되지 않는/불필요한 백로그 큐는 네임스페이스 소유자가 정리해야 합니다. 그 이유는 Service Bus가 네임스페이스 내의 모든 큐가 어떤 용도로 존재하는지 알 수 없기 때문입니다. 또한 지정된 이름의 큐가 있지만 [QueueDescription][QueueDescription]이 적절히 맞지 않는 경우라면 기본 동작을 사용자가 직접 변경해야 할 것입니다. 코드로 백로크 큐를 수정하는 것에 대해서는 어떤 보장도 기대할 수 없습니다. 변경 내용을 철저히 테스트해 보십시오.

## <a name="custom-messagesender"></a>사용자 지정 MessageSender
모든 메시지는 내부 [MessageSender][MessageSender] 개체를 통해 전송됩니다. 이 개체는 모든 것이 정상일 경우 일반적인 방식으로 작동하지만 오류가 있으면 백로그 큐로 리디렉션합니다. 일시적이지 않은 오류가 발생할 경우 타이머가 시작됩니다. [FailoverInterval][FailoverInterval] 속성 값을 구성하는 [TimeSpan][TimeSpan] 기간 동안 메시지가 성공적으로 전송되지 않을 경우, 장애 조치(failover)가 시작됩니다. 이 시점에서 각 엔터티에 대해 다음 작업이 수행됩니다.

* 엔터티를 사용할 수 있는지 확인하기 위해 [PingPrimaryInterval][PingPrimaryInterval]마다 Ping 작업이 실행됩니다. 이 작업이 성공하면 해당 엔터티를 사용하는 모든 클라이언트 코드가 즉시 기본 네임스페이스로 새 메시지를 보내는 작업을 시작합니다.
* 이후에 다른 발신자에서 동일한 엔터티로 메시지 발송을 요청하면 백로그 큐에 보관되도록 [BrokeredMessage][BrokeredMessage]가 전송되어 수정됩니다. 이 수정을 통해 [BrokeredMessage][BrokeredMessage] 개체의 일부 속성이 제거되고 다른 곳에 저장됩니다. Service Bus와 SDK가 메시지를 동일한 방식으로 처리할 수 있도록 다음 속성이 지워지고 새 별칭으로 추가됩니다.

| 이전 속성 이름 | 새 속성 이름 |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

원래 대상 경로도 메시지 내에 x-ms-path라는 이름의 속성으로 저장됩니다. 이 방식을 통해 많은 엔터티에 대한 메시지가 하나의 백로그 큐에 함께 존재할 수 있게 됩니다. 속성은 사이펀을 통해 해석됩니다.

사용자 지정 [MessageSender][MessageSender] 개체는 메시지가 256KB 제한에 도달하면 문제가 발생할 수 있으며 장애 조치(failover)가 시작됩니다. 사용자 지정 [MessageSender][MessageSender] 개체는 백로그 큐에 모든 큐와 토픽에 대한 메시지를 저장합니다. 이 개체는 백로그 큐 내에서 여러 기본 큐의 메시지를 혼합합니다. 서로를 알 수 없는 많은 클라이언트 사이에서 부하 분산을 처리하기 위해 SDK는 사용자가 코드에서 만든 각 [QueueClient][QueueClient] 또는 [TopicClient][TopicClient]에 대해 하나씩의 백로그 큐를 임의로 선택합니다.

## <a name="pings"></a>Ping
Ping 메시지는 [ContentType][ContentType] 속성이 application/vnd.ms-servicebus-ping으로, [TimeToLive][TimeToLive] 값이 1로 설정된 빈 [BrokeredMessage][BrokeredMessage]입니다. 이 Ping은 Service Bus에서 한 가지 특별한 특징이 있습니다. 어떤 호출자라도 [BrokeredMessage][BrokeredMessage]를 요청하면 서버는 절대 Ping을 전달하지 않습니다. 따라서 이러한 메시지를 받고 무시하는 방법을 알 필요가 없습니다. 각 클라이언트당 [MessagingFactory][MessagingFactory] 인스턴스의 엔터티(고유한 큐 또는 토픽)를 대상으로, 엔터티를 사용할 수 없다고 간주되면 Ping이 발송됩니다. 기본적으로 이 작업은 1분에 한 번씩 수행됩니다. Ping 메시지는 일반 Service Bus 메시지로 간주되며, 대역폭과 메시지에 대한 비용이 소비됩니다. 클라이언트가 시스템이 사용 가능해진 것으로 감지하면 곧바로 메시지는 중단됩니다.

## <a name="the-syphon"></a>사이펀
응용 프로그램에서 하나 이상의 실행 프로그램이 적극적으로 사이펀을 실행해야 합니다. 사이펀은 15분 동안 지속되는 긴 폴링을 수행합니다. 모든 엔터티가 사용 가능 상태이고 10개의 백로그 큐가 있는 경우, 사이펀을 호스팅하는 응용 프로그램은 시간당 40번, 하루에 960번, 30일에 28,000번의 수신 작업을 호출합니다. 사이펀이 적극적으로 메시지를 백로그에서 기본 큐로 보낼 경우 각 메시지에 대한 비용(모든 단계에서 메시지 크기 및 대역폭에 대한 표준 비용을 적용)은 다음과 같습니다.

1. 백로그로 보냅니다.
2. 백로그에서 수신합니다.
3. 기본 큐로 보냅니다.
4. 기본 큐에서 수신합니다.

## <a name="closefault-behavior"></a>닫기/오류 동작
사이펀을 호스팅하는 응용 프로그램 내에서 기본 또는 보조 [MessagingFactory][MessagingFactory]가 해당 파트너에 오류가 발생하거나 닫히지 않은 상태에서 오류가 발생하거나 닫히고, 사이펀이 이 상태를 감지하면 사이펀이 작동합니다. 다른 [MessagingFactory][MessagingFactory]가 5초 내에 닫히지 않으면 사이펀이 계속 열려 있는 [MessagingFactory][MessagingFactory]를 오류 처리합니다.

## <a name="next-steps"></a>다음 단계
Service Bus 비동기 메시징에 대한 자세한 내용은 [비동기 메시징 패턴 및 고가용성][Asynchronous messaging patterns and high availability]을 참조하세요. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
