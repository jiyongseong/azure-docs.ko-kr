---
title: "Azure Service Bus 메시지 큐, 토픽 및 구독 개요 | Microsoft Docs"
description: "Service Bus 메시징 엔터티의 개요"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus 큐, 토픽 및 구독

Microsoft Azure Service Bus는 신뢰할 수 있는 메시지 큐 및 지속형 게시/구독 메시징을 포함하여 클라우드 기반, 메시지 지향 미들웨어 기술 집합을 지원합니다. 이러한 조정된 메시징 기능은 Service Bus 메시징 워크로드를 사용하여 분리된 메시징 기능 게시-구독, 임시 분리 및 부하 분산 시나리오로 여겨질 수 있습니다. 분리된 통신에는 많은 장점이 있습니다. 예를 들어 필요하면 클라이언트와 서버가 연결되고 비동기 방식으로 작업을 수행할 수 있습니다.

Service Bus에서 메시지 기능의 핵심이 되는 메시지 엔터티는 큐, 토픽 및 구독, 규칙/동작입니다.

## <a name="queues"></a>큐

큐는 하나 이상의 경쟁 소비자에게 *FIFO(선입선출)* 메시지 배달을 제공합니다. 즉, 일반적으로 메시지가 큐에 추가된 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리될 예정입니다. 큐를 사용하는 주요 이점은 응용 프로그램 구성 요소를 "임시로 분리"할 수 있다는 점입니다. 즉, 메시지가 큐에서 영구적으로 저장되기 때문에 생산자(발신자) 및 소비자(수신자)가 동시에 메시지를 보내고 받을 필요가 없습니다. 또한 생산자는 계속해서 메시지를 처리하고 보내기 위해 소비자의 회신을 기다릴 필요가 없습니다.

관련된 이점은 “부하 평준화”로 생산자와 소비자가 서로 다른 속도로 메시지를 주고받을 수 있습니다. 많은 응용 프로그램에서 시스템 부하는 시간에 따라 다르지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 최대 부하 대신 평균 부하를 다룰 수 있으려면 소비 응용 프로그램만 프로비전해야 함을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 응용 프로그램 부하를 제공하는 데 필요한 인프라의 크기와 관련하여 비용이 직접적으로 절약됩니다. 부하가 증가하면 큐에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 속도로 메시지를 가져올 때 처리 능력이 다른 경우에도 작업자 컴퓨터의 최적 사용률을 허용합니다. 이 패턴을 종종 “경쟁 소비자” 패턴이라고 부릅니다.

메시지 생산자와 소비자 간을 중개하는 큐를 사용하면 구성 요소 간에 내재하는 느슨한 연결을 제공합니다. 생산자와 소비자가 서로를 인식하지 않기 때문에 생산자에 영향을 주지 않고 소비자를 업그레이드할 수 있습니다.

큐 만들기는 여러 단계의 프로세스입니다. [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) 클래스를 통해 Service Bus 메시징 엔터티(큐 및 토픽 모두)에 대한 관리 작업을 수행하며 이는 Service Bus 네임스페이스 및 사용자 자격 증명의 기본 주소를 제공하여 생성됩니다. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager)는 메시징 엔터티를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다. SAS 이름 및 키, 서비스 네임스페이스 관리 개체에서 [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) 개체를 만든 후에 [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) 메서드를 사용하여 큐를 만들 수 있습니다. 예:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Service Bus URI를 사용하여 큐 개체 및 메시징 팩터리를 인수로 만들 수 있습니다. 예:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

그러면 큐로 메시지를 보낼 수 있습니다. 예를 들어 `MessageList`라는 조정된 메시지 목록이 있는 경우 코드는 다음 예제와 유사하게 나타납니다.

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

다음과 같이 큐에서 메시지를 받을 수 있습니다:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드에서 수신은 1단계 작업입니다. 즉, Service Bus가 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus가 메시지를 이용되는 것으로 표시했기 때문에 응용 프로그램이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드에서는 수신 작업이 2단계이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus가 **Complete** 호출을 확인하면 메시지를 이용한 것으로 표시하고 큐에서 제거합니다.

어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) 메서드 대신 [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 메시지의 잠금을 해제하므로 동일한 소비나 다른 경쟁적 소비자에게서 메시지를 다시 받을 수 있습니다. 두 번째로 잠금과 연결된 시간 제한도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) Service Bus가 메시지를 잠금 해제하여 다시 받을 수 있게 합니다(기본적으로 [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) 작업 수행).

응용 프로그램이 메시지를 처리한 후 **Complete** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 *최소 한 번 이상* 처리라고 합니다. 즉, 각 메시지가 최소 한 번 처리됩니다. 그러나 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 시나리오가 중복 처리를 허용하지 않는 경우 메시지의 **MessageId** 속성에 따라 얻을 수 있는 중복을 검색하려면 응용 프로그램에 추가 논리가 필요하며 이는 전달 시도를 걸쳐 일관성을 유지합니다. *정확히 한번* 처리로 알려집니다.

## <a name="topics-and-subscriptions"></a>토픽 및 구독
각 메시지가 단일 소비자에 의해 처리되는 큐와 반대로, *토픽*과 *구독*은 *게시/구독* 패턴을 사용하여 일 대 다 형태의 통신을 제공합니다. 많은 수혜자 수를 조정할 수 있도록 각 게시된 메시지를 토픽에 등록된 각 구독에서 사용할 수 있게 합니다. 메시지를 토픽에 보내고 구독 단위로 설정할 수 있는 필터 규칙에 따라 하나 이상의 연결된 구독에 전달합니다. 구독은 추가 필터를 사용하여 수신하려는 메시지를 제한할 수 있습니다. 메시지는 큐로 전송된 것과 동일한 방식으로 토픽에 전송되지만 메시지는 토픽에서 직접 수신되지 않습니다. 대신 구독에서 수신합니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 메시지는 큐에서 수신하는 방식과 동일하게 구독에서 수신됩니다.

비교를 통해 큐의 메시지 보내기 기능은 토픽에 직접 매핑하고 해당 메시지 받기 기능은 구독에 매핑합니다. 무엇보다도 즉, 구독은 큐와 관련하여 경쟁적 소비자, 임시 분리, 부하 평준화 및 부하 분산 등 이 섹션 앞 부분에서 설명한 동일한 패턴을 지원합니다.

이전 섹션의 예제와 같이 토픽을 만드는 것은 큐를 만드는 것과 유사합니다. 서비스 URI를 만든 다음 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 클래스를 사용하여 네임스페이스 클라이언트를 만듭니다. [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) 메서드를 사용하여 토픽을 만들 수 있습니다. 예:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

다음으로 원하는 대로 구독을 추가합니다.

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

그런 다음 토픽 클라이언트를 만들 수 있습니다. 예:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

메시지 발신자를 사용하여 이전 섹션에서 보여준 것처럼 토픽에서 메시지를 보내고 받을 수 있습니다. 예:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

큐와 마찬가지로 [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) 개체 대신 [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) 개체를 사용하여 구독에서 메시지를 수신합니다. 토픽의 이름, 구독의 이름 및 (선택 사항)수신 모드를 매개 변수로 전달하는 구독 클라이언트를 만듭니다. 예를 들어 **인벤토리** 구독을 사용합니다.

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>규칙 및 동작
대부분의 시나리오에서 특정 특성을 가진 메시지를 다른 방법으로 처리해야 합니다. 이 기능을 사용하려면 구독을 구성하여 원하는 속성을 갖는 메시지를 찾은 다음 해당 속성에 특정 수정 작업을 수행할 수 있습니다. Service Bus 구독이 토픽으로 전송된 모든 메시지를 확인하는 동안 가상 구독 큐로 이러한 메시지의 하위 집합을 복사할 수 있습니다. 구독 필터를 사용하여 수행합니다. 이와 같은 수정을 *필터 동작*이라고 합니다. 구독을 만들 경우 메시지의 속성, 즉 시스템 속성(예: **Label**) 및 사용자 지정 응용 프로그램 속성(예:**StoreName**) 모두에서 작동하는 필터 식을 제공할 수 있습니다. 이 경우에 SQL 필터 식은 선택 사항입니다. SQL 필터 식 없이 구독에 정의된 필터 작업을 구독에 대한 모든 메시지에서 수행합니다.

이전 예제를 사용하여 **저장소1**에서 오는 메시지를 필터링하려면 대시보드 구독을 다음과 같이 만듭니다.

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

구독 필터가 준비된 경우 `StoreName` 속성이 `Store1`로 설정된 메시지만이 `Dashboard` 구독에 대해 가상 큐에 복사됩니다.

가능한 필터 값에 대한 자세한 내용은 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) 및 [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) 클래스에 대한 설명서를 참조하세요. 또한 [조정된 메시징: 고급 필터](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) 및 [토픽 필터](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) 샘플을 참조하세요.

## <a name="next-steps"></a>다음 단계
Service Bus 메시지 사용에 대한 자세한 내용 및 예제는 다음 고급 토픽을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [Service Bus 조정된 메시징 .NET 자습서](service-bus-brokered-tutorial-dotnet.md)
* [Service Bus 조정된 메시징 REST 자습서](service-bus-brokered-tutorial-rest.md)
* [조정된 메시징: 고급 필터 샘플](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

