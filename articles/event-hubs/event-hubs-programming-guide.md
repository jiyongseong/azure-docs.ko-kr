---
title: "Azure Event Hubs에 대한 프로그래밍 가이드 | Microsoft Docs"
description: "Azure.NET SDK를 사용하여 Azure Event Hubs에 대한 코드를 작성합니다."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/16/2017
ms.author: sethm
ms.openlocfilehash: 7d5f14d5a65253cf0aad1811ace419bf2f39f7db
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="event-hubs-programming-guide"></a>Event Hubs 프로그래밍 가이드

이 문서에서는 Azure Event Hub 및 Azure .NET SDK를 사용하여 코드를 작성하는 몇 가지 일반적인 시나리오에 대해 논의합니다. Event Hubs에 대한 예비 이해가 있다고 가정합니다. Event Hubs의 개요에 대한 개념은 [Event Hubs 개요](event-hubs-what-is-event-hubs.md)를 참조하세요.

## <a name="event-publishers"></a>이벤트 게시자

HTTP POST를 사용하거나 AMQP 1.0 연결을 통해 이벤트 허브에 이벤트를 보냅니다. 해결할 구체적인 시나리오에 따라 무엇을 언제 사용할지 선택합니다. AMQP 1.0 연결은 영구 메시징 채널을 제공하기 때문에 Service Bus에서 조정된 연결로 계량되며 시나리오에서 자주 높은 메시지 볼륨 및 낮은 대기 시간 요구 사항에 적절합니다.

[NamespaceManager][] 클래스를 사용하여 Event Hubs를 만들고 관리할 수 있습니다. .NET 관리 API를 사용하는 경우 Event Hubs에 데이터를 게시하기 위한 기본 구조는 [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) 및 [EventData][] 클래스입니다. [EventHubClient][]는 이벤트가 이벤트 허브로 전송되는 AMQP 통신 채널을 제공합니다. [EventData][] 클래스는 이벤트를 나타내며 이벤트 허브에 메시지를 게시하는데 사용됩니다. 이 클래스는 이벤트에 대한 본문, 일부 메타데이터 및 헤더 정보를 포함합니다. [EventData][] 개체가 이벤트 허브를 통과할 때 여기에 다른 속성이 추가됩니다.

## <a name="get-started"></a>시작

Event Hubs를 지원하는 .NET 클래스는 Microsoft.ServiceBus.dll 어셈블리에 제공됩니다. Service Bus API를 가져오고 모든 Service Bus 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법은 [Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus)를 다운로드하는 것입니다. 또는 Visual Studio에서 [패키지 관리자 콘솔](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) 을 사용할 수 있습니다. 이렇게 하려면 [패키지 관리자 콘솔](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) 창에서 다음 명령을 발급합니다.

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>이벤트 허브 만들기
Event Hubs 만들기에 [NamespaceManager][] 클래스를 사용할 수 있습니다. 예:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

대부분의 경우에서 [CreateEventHubIfNotExists][] 메서드를 사용하여 서비스를 다시 시작하는 경우 예외를 생성하지 않도록 하는 것이 좋습니다. 예:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

[CreateEventHubIfNotExists][]를 포함하여 모든 Event Hubs 만들기 작업은 해당 네임스페이스에 대한 **관리** 권한이 필요합니다. 게시자 또는 소비자 응용 프로그램의 사용 권한을 제한하려면 제한된 권한으로 자격 증명을 사용하는 경우 프로덕션 코드에서 작업 호출을 만들기를 방지할 수 있습니다

[EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) 클래스에는 권한 부여 규칙, 메시지 보존 간격, 파티션 ID, 상태 및 경로를 포함하는 이벤트 허브에 대한 세부 정보가 포함됩니다. 이 클래스를 사용하여 이벤트 허브에서 메타데이터를 업데이트할 수 있습니다.

## <a name="create-an-event-hubs-client"></a>Event Hubs 클라이언트 만들기
Event Hubs와 상호 작용하기 위한 기본 클래스는 [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]입니다. 이 클래스는 발신자와 수신자 기능을 모두 제공합니다. 다음 예와 같이 [만들기](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) 메서드를 사용하여 이 클래스를 인스턴스화할 수 있습니다.

```csharp
var client = EventHubClient.Create(description.Path);
```

이 메서드는 `appSettings` 섹션의 App.config 파일에서 Service Bus 연결 정보를 사용합니다. Service Bus 연결 정보를 저장하는 데 사용되는 `appSettings` XML의 예제는 [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) 메서드에 대한 문서를 참고하십시오.

다른 옵션은 연결 문자열에 클라이언트를 만드는 것입니다. 작업자에 대한 구성 속성에 문자열을 저장할 수 있으므로 이 옵션은 Azure 작업자 역할을 사용하는 경우에 잘 작동합니다. 예:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

이전 방법에 대한 App.config 파일에서 표시된 대로 연결 문자열은 동일한 형식에 있습니다.

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

또한 마지막으로 다음 예와 같이 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 인스턴스에서 [EventHubClient][] 개체를 만들 수 있습니다.

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

메시징 팩터리 인스턴스에서 만들어진 추가 [EventHubClient][] 개체가 동일한 기본 TCP 연결을 재사용하는 것이 중요합니다. 따라서 이러한 개체는 처리량에 클라이언트쪽 한계를 갖습니다. [만들기](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) 메서드는 단일 메시징 팩터리를 재사용합니다. 단일 발신자에서 매우 높은 처리량이 필요한 경우 각 메시징 팩터리에서 다양한 메시지 팩터리 및 하나의 [EventHubClient][] 개체를 만들 수 있습니다.

## <a name="send-events-to-an-event-hub"></a>이벤트 허브로 이벤트 보내기
[EventData][] 인스턴스를 만들고 [보내기](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) 메서드를 통해 보내 이벤트를 이벤트 허브로 보냅니다. 이 메서드는 단일 [EventData][] 인스턴스 매개 변수를 가져오는 동시에 이벤트 허브로 보냅니다.

## <a name="event-serialization"></a>이벤트 직렬화
[EventData][] 클래스에는 개체 및 직렬 변환기, 바이트 배열 또는 스트림과 같은 다양한 매개 변수를 가진 [4개의 오버로드된 생성자](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_)가 있습니다. [EventData][] 클래스를 인스턴스화하고 나중에 본문 스트림을 설정할 수 있습니다. [EventData][]와 함께 JSON을 사용하는 경우 **Encoding.UTF8.GetBytes()** 를 사용하여 JSON으로 인코딩된 문자열에 대한 바이트 배열을 검색할 수 있습니다.

## <a name="partition-key"></a>파티션 키
[EventData][] 클래스에는 보낸 사람이 파티션 할당을 생성하도록 해시하는 값을 지정할 수 있도록 하는 [PartitionKey][] 속성이 있습니다. 파티션 키를 사용하여 동일한 키를 가진 모든 이벤트가 이벤트 허브의 동일한 파티션에 전송되었는지 확인합니다. 공통 파티션 키에는 사용자 세션 ID 및 고유한 보낸 사람 ID가 있습니다. [PartitionKey][] 속성은 선택적이며 [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) 또는 [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_) 메서드를 사용하는 경우 제공될 수 있습니다. [PartitionKey][]에 대한 값을 제공하지 않는 경우 보내진 이벤트는 라운드 로빈 모델을 사용하여 파티션에 배포됩니다.

### <a name="availability-considerations"></a>가용성 고려 사항

파티션 키 사용은 선택 사항이며 사용 여부를 신중하게 고려해야 합니다. 대부분의 경우에서 이벤트 순서 지정이 중요한 경우 파티션 키를 사용 하는 것이 좋습니다. 파티션 키를 사용할 경우 이러한 파티션은 단일 노드에서 가용성이 필요하며 시간이 지나면 중단이 발생할 수 있습니다(예: 연산 노드를 재부팅하고 패치할 경우). 따라서 파티션 ID를 설정하고 해당 파티션을 어떠한 이유로 사용할 수 없게 되면 해당 파티션의 데이터에 액세스하지 못하게 됩니다. 고가용성이 가장 중요한 경우에는 파티션 키를 지정하지 마십시오. 이러한 경우 이벤트는 이전에 설명한 라운드 로빈 모델을 사용하는 파티션으로 보내집니다. 이 시나리오에서는 가용성(파티션 ID 없음)과 일관성(파티션 ID에 이벤트 고정) 간에 명시적인 선택을 합니다.

이벤트 처리에서 지연을 처리하는 것도 고려해야 할 사항입니다. 경우에 따라, 잠재적으로 추가 다운스트림 처리 지연을 일으킬 수 있으므로 처리를 계속 진행하도록 하는 것보다 데이터를 삭제한 다음 다시 시도하는 것 나을 수 있습니다. 예를 들어 주식 시세 표시기 사용 시 완벽한 최신 데이터를 기다리는 것이 좋지만, 실시간 채팅 또는 VOIP 시나리오에서는 완벽하지는 않아도 데이터를 빨리 보유하는 것이 낫습니다.

이러한 가용성을 고려해 볼 때 이러한 시나리오에서는 다음 오류 처리 전략 중 하나를 선택할 수 있습니다.

- 중지(문제가 해결될 때까지 Event Hubs에서 읽기 중지)
- 삭제(메시지가 중요하지 않을 경우에는 삭제)
- 다시 시도(적합하게 보이도록 메시지를 다시 시도)
- [배달 못 한 편지](../service-bus-messaging/service-bus-dead-letter-queues.md)(처리할 수 없는 메시지만 배달 못 한 편지에 큐 또는 다른 이벤트 허브 사용)

가용성과 일관성 간의 절충에 대한 자세한 내용은 [Event Hubs의 가용성 및 일관성](event-hubs-availability-and-consistency.md)을 참조하세요. 

## <a name="batch-event-send-operations"></a>Batch 이벤트가 작업을 보냅니다
배치에서 이벤트를 보내면 처리량을 향상시키는 데 도움을 줄 수 있습니다. [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) 메서드는 [EventData][] 형식의 **IEnumerable** 매개 변수를 사용하고 이벤트 허브에 원자성 작업으로 전체 배치를 보냅니다.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

단일 배치가 이벤트의 256KB 제한을 넘지 않아야 한다는 것에 유의해야 합니다. 또한 배치의 각 메시지는 동일한 게시자 id를 사용합니다. 배치가 최대 이벤트 크기를 초과하지 않도록 확인하는 것은 보낸 사람의 책임입니다. 그런 경우 클라이언트 **보내기** 오류가 생성됩니다. 도우미 메서드 [EventHubClient.CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch)를 사용하면 일괄 처리가 256KB를 초과하지 않도록 할 수 있습니다. [CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) API에서 빈 [EventDataBatch](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch)를 얻은 다음 [TryAdd](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.tryadd#Microsoft_ServiceBus_Messaging_EventDataBatch_TryAdd_Microsoft_ServiceBus_Messaging_EventData_)를 사용하여 이벤트를 추가하여 일괄 처리를 구성합니다. 마지막으로 [EventDataBatch.ToEnumerable](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.toenumerable)을 사용하여 기본 이벤트를 [EventHubClient.Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) API로 전달합니다.

## <a name="send-asynchronously-and-send-at-scale"></a>비동기적으로 보내고 규모로 보내기
또한 비동기적으로 이벤트를 이벤트 허브로 보낼 수 있습니다. 비동기적으로 보내기는 클라이언트가 이벤트를 보낼 수 있는 속도를 증가시킬 수 있습니다. [보내기](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) 및 [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendbatch) 메서드는 모두 [태스크](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) 개체를 반환하는 비동기 버전에서 사용할 수 있습니다. 이 기술은 처리량을 늘릴 수 있는 반면 Event Hubs 서비스에 의해 제한되는 동안 이벤트를 보내기 위해 클라이언트를 계속 발생시킬 수 있고 제대로 구현되지 않은 경우 클라이언트에 오류 또는 손실 메시지가 발생할 수 있습니다. 또한 클라이언트에서 [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity.retrypolicy) 속성을 사용하여 클라이언트 다시 시도 옵션을 제어할 수 있습니다.

## <a name="create-a-partition-sender"></a>파티션 발신자 만들기
파티션 키 없이 이벤트 허브에 이벤트를 보내는 방법이 가장 일반적이지만 지정된 파티션에 직접 이벤트를 보낼 수 있는 경우도 있습니다. 예:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_)는 특정 Event Hub 파티션에 이벤트를 게시하는데 사용할 수 있는 [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) 개체를 반환합니다.

## <a name="event-consumers"></a>이벤트 소비자
Event Hubs에는 이벤트 사용에 대한 두 기본 모델인 직접 수신기 및 [EventProcessorHost][]와 같은 상위 수준의 추상화가 있습니다. 직접 수신기는 *소비자 그룹* 내의 파티션에 액세스를 자체 조정을 담당합니다. 소비자 그룹은 분할된 이벤트 허브의 보기 (상태, 위치 또는 오프셋)입니다.

### <a name="direct-consumer"></a>직접 소비자
파티션에서 읽을 수 있는 가장 직접적인 방법은 [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) 클래스를 사용하는 것입니다. 이 클래스의 인스턴스를 만들려면 [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) 클래스의 인스턴스를 사용해야 합니다. 다음 예제에서 소비자 그룹에 대한 수신기를 만들 때 파티션 ID를 지정해야 합니다.

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

[CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) 메서드에는 생성되는 판독기에 대한 제어를 용이하게 하는 몇가지 오버로드가 있습니다. 이러한 메서드는 오프셋을 문자열 또는 타임 스탬프로 지정하는 것을 포함하거나 반환된 스트림에서 지정된 오프셋을 포함할지 아니면 이후 시작할지를 지정하는 기능을 포함합니다. 수신기를 만든 후 반환된 개체에 대한 이벤트 수신을 시작할 수 있습니다. [수신](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) 메서드는 배치 크기 및 대기 시간과 같은 수신 작업 매개 변수를 제어하는 4개의 오버로드가 있습니다. 이러한 메서드의 비동기 버전을 사용하여 소비자의 처리량을 증가시킬 수 있습니다. 예:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

특정 파티션과 관련하여 메시지가 이벤트 허브로 전송된 순서로 메시지를 수신합니다. 오프셋은 파티션에서 메시지를 식별하는 데 사용되는 문자열 토큰입니다.

단일 파티션은 언제든지 연결되는 5개의 동시 판독기를 가질 수 없다는 점에 유의해야 합니다. 판독기를 연결하거나 연결이 끊기면 서비스 연결이 끊어져 있는지 인식하기 전에 몇 분간 해당 세션이 활성 상태로 유지될 수 있습니다. 이 시간 동안 파티션에 다시 연결이 실패할 수 있습니다. Event Hubs용 직접 수신기 작성의 전체 예제는 [Event Hubs 직접 수신기](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) 샘플을 참조하세요.

### <a name="event-processor-host"></a>이벤트 프로세서 호스트
[EventProcessorHost][] 클래스는 Event Hubs에서 데이터를 처리합니다. .NET 플랫폼에서 이벤트 판독기를 작성할 때 이 구현을 사용해야 합니다. [EventProcessorHost][] 는 검사점 및 파티션 임대 관리를 제공하는 이벤트 처리기 구현에 대한 스레드 안전, 다중 프로세스, 안전한 런타임 환경을 제공합니다.

[EventProcessorHost][] 클래스를 사용하려면 [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor)를 구현할 수 있습니다. 이 인터페이스는 세 가지 메서드가 포함합니다.

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

이벤트 처리를 시작하려면 이벤트 허브에 대한 적절한 매개 변수를 제공하여 [EventProcessorHost][]를 인스턴스화합니다. 그런 다음 [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1)을 호출하여 런타임에 [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) 구현을 등록합니다. 이 시점에서 호스트는 "greedy" 알고리즘을 사용하여 허브 이벤트의 모든 파티션에서 임대를 획득하려 합니다. 이러한 임대는 지정된 시간 프레임 동안 지속되며 갱신되어야 합니다. 새 노드(이 경우 작업자 인스턴스)가 온라인 상태가 되면 임대 예약을 놓고 더 많은 임대를 획득하기 위해 시간이 지남에 따라 노드 간에 부하가 이동합니다.

![이벤트 프로세서 호스트](./media/event-hubs-programming-guide/IC759863.png)

시간이 지남에 따라 평형이 설정됩니다. 이 동적 기능을 사용하면 확장 및 축소 모두에 대해 소비자에게 적용할 CPU 기반 자동 크기 조정을 할 수 있습니다. Event Hubs에 메시지 개수의 직접 개념이 없기 때문에 평균 CPU 사용률은 백 엔드 또는 소비자 규모를 측정하는 최적의 메커니즘인 경우가 많습니다. 소비자가 처리할 수 있는 것 보다 더 많은 이벤트를 게시자가 게시하기 시작하는 경우 소비자에게 CPU 증가를 사용하여 작업자 인스턴스 수의 크기를 자동으로 조정할 수 있습니다.

또한 [EventProcessorHost][] 클래스는 Azure 저장소 기반 검사점 메커니즘을 구현합니다. 이 메커니즘은 파티션 당 오프셋을 저장하므로 각 소비자가 이전 소비자의 마지막 검사점 무엇인지를 결정할 수 있습니다. 임대를 통해 노드 간에 파티션이 전환되면 이동하는 부하를 용이하게 하는 동기화 메커니즘입니다.

## <a name="publisher-revocation"></a>게시자 해지
[EventProcessorHost][]의 고급 런타임 기능 외에도 Event Hubs는 이벤트 허브로 이벤트를 보내는 특정 게시자를 차단하기 위해 게시자를 해지할 수 있습니다. 이러한 기능은 게시자의 토큰이 손상되거나 소프트웨어 업데이트가 부적절하게 동작을 일으킨 경우 유용합니다. 이러한 상황에서 해당 SAS 토큰의 일부인 게시자의 id가 이벤트 게시에서 차단될 수 있습니다.

게시자 해지 및 게시자로 Event Hubs에 보내는 방법에 대한 자세한 내용은 [Event Hubs 대규모 보안 게시](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) 샘플을 참조하세요.

## <a name="next-steps"></a>다음 단계
Event Hubs 시나리오에 대한 자세한 내용은 다음 링크를 방문하십시오.

* [Event Hubs API 개요](event-hubs-api-overview.md)
* [Event Hubs의 정의](event-hubs-what-is-event-hubs.md)
* [Event Hubs의 가용성 및 일관성](event-hubs-availability-and-consistency.md)
* [이벤트 프로세서 호스트 API 참조](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
