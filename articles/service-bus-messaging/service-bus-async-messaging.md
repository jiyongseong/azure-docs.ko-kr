---
title: "Service Bus 비동기 메시징 | Microsoft Docs"
description: "Azure Service Bus 비동기 메시지에 대한 설명입니다."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: e48e95d99847e68bdb218b341ad2fbcd44eb31f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>비동기 메시징 패턴 및 고가용성

비동기 메시징은 다양한 방식으로 구현할 수 있습니다. 큐, 토픽 및 구독으로 Azure Service Bus는 저장소 및 전달 메커니즘을 통해 비동기를 지원합니다. 정상(동기) 작업에서 큐 및 토픽에 메시지를 보내고 큐 및 구독에서 메시지를 받습니다. 작성하는 응용 프로그램은 항상 사용 가능한 이러한 엔터티에 따라 다릅니다. 다양한 상황으로 인해 엔터티 상태가 변경되면 대부분의 요구를 충족할 수 있는 축소된 기능 엔터티를 제공하는 방법이 필요합니다.

일반적으로 응용 프로그램은 비동기 메시징 패턴을 사용하여 다양한 통신 시나리오를 사용합니다. 서비스가 실행 중이지 않을 경우에도 클라이언트가 서비스로 메시지를 보낼 수 있는 응용 프로그램을 빌드할 수 있습니다. 통신의 버스트를 경험한 응용 프로그램의 경우 큐는 통신을 버퍼링할 장소를 제공하여 부하를 일정하게 할 수 있습니다. 마지막으로 여러 컴퓨터 간에 메시지를 배포하는 간단하면서도 효율적인 부하 분산 장치를 얻을 수 있습니다.

이러한 엔터티의 가용성을 유지 관리하기 위해 이러한 엔터티가 지속 가능한 메시징 시스템에 사용 불가하다고 나타날 수 있는 다양한 다른 방법을 고려합니다. 일반적으로 말해서 다음의 다른 방법으로 작성한 응용 프로그램에서 사용할 수 없게 되는 엔터티를 봅니다.

* 메시지를 보낼 수 없습니다.
* 메시지를 받을 수 없습니다.
* 엔터티를 관리할 수 없습니다(엔터티 만들기, 검색, 업데이트 또는 삭제).
* 서비스에 연결할 수 없습니다.

이러한 각 오류의 경우 다른 오류 모드는 응용 프로그램이 기능이 축소된 수준에서 계속 작업을 수행할 수 있도록 존재합니다. 예를 들어 메시지를 보낼 수 있지만 받지 않는 시스템은 고객의 주문을 받을 수 있지만 이러한 주문을 처리할 수 없습니다. 이 항목에서는 발생할 수 있는 잠재적인 문제 및 이러한 문제를 줄이는 방법을 설명합니다. Service Bus는 선택해야 하는 다양한 완화 방법을 도입하고 또한 이 항목도 이러한 옵트인 완화 방법의 사용을 제어하는 규칙을 설명합니다.

## <a name="reliability-in-service-bus"></a>Service Bus의 안정성
메시지와 엔터티 문제를 처리하는 여러 방법이 있고 이러한 완화 방법을 적절하게 사용하도록 관리하는 지침이 있습니다. 지침을 이해하려면 먼저 Service Bus에서 무엇이 실패할 수 있는지 이해해야 합니다. Azure 시스템의 설계로 인해 이러한 문제는 모두 수명이 짧은 경향이 있습니다. 높은 수준에서 사용할 수 없는 다른 원인은 다음과 같이 나타납니다.

* Service Bus가 의존하는 외부 시스템에서 제한합니다. 제한은 계산 및 저장소 리소스와의 상호작용에서 발생합니다.
* Service Bus가 의존하는 시스템에서 발생하는 문제입니다. 예를 들어 저장소의 특정 부분에 문제가 발생할 수 있습니다.
* 단일 하위 시스템에서 Service Bus의 오류입니다. 이 상황에서 계산 노드는 일관성이 없는 상태일 수 있으며 자체를 다시 시작해야 하며 이는 부하를 다른 노드로 분산하는 모든 엔터티를 발생시킵니다. 단기간 동안 차례로 메시지 처리 속도가 느려질 수 있습니다.
* Azure 데이터 센터 내에서 Service Bus의 오류입니다. 몇 분 또는 몇 시간 동안 시스템에 연결할 수 없는 "치명적 오류"입니다.

> [!NOTE]
> 용어 **저장소**는 Azure Storage 및 SQL Azure를 의미할 수 있습니다.
> 
> 

Service Bus는 이런 문제에 대한 다양한 완화 방법을 포함합니다. 다음 섹션에서는 각 문제 및 해당 완화 방법을 설명합니다.

### <a name="throttling"></a>제한
Service Bus로 제한을 사용하면 공동으로 메시지 속도를 관리할 수 있습니다. 각 개별 Service Bus 노드가 여러 엔터티가 있습니다. 이러한 각 엔터티는 CPU, 메모리, 저장소 및 기타 측면에서 시스템에 요청을 만듭니다. 패싯이 정의된 임계값을 초과하는 사용을 감지하면 Service Bus는 지정된 요청을 거부할 수 있습니다. 호출자는 [ServerBusyException][ServerBusyException]을 수신하고 10 초 후에 다시 시도합니다.

완화 방법으로 코드는 오류를 읽고 적어도 10초 동안 메시지의 다시 시도를 중단해야 합니다. 오류는 고객 응용 프로그램의 조각에 발생할 수 있으므로 각 조각이 재시도 논리를 독립적으로 실행한다고 예상됩니다. 코드는 큐 또는 토픽에서 분할을 사용하여 제한될 가능성을 줄일 수 있습니다.

### <a name="issue-for-an-azure-dependency"></a>Azure 종속성에서 발생하는 문제
Azure 내의 다른 구성 요소에는 서비스 문제가 있는 경우도 있습니다. 예를 들어 Service Bus가 사용하는 시스템이 업그레이드되는 경우 해당 시스템은 일시적으로 기능이 축소될 수 있습니다. 이러한 유형의 문제를 해결하려면 Service Bus가 정기적으로 완화 방법을 조사하고 구현합니다. 이러한 완화의 부작용도 물론 나타납니다. 예를 들어 저장소에서 발생하는 일시적인 문제를 처리하려면 Service Bus가 일관되 게 작동하도록 메시지 보내기 작업을 허용하는 시스템을 구현합니다. 완화 방법의 특성 상 보낸 메시지는 영향을 받는 큐 또는 구독에 표시되고 수신 작업을 준비하는 데 최대 15분이 걸릴 수 있습니다. 일반적으로 엔터티에는 대부분 이 문제가 발생하지 않습니다. 그러나 Azure 내에서 Service Bus의 엔터티 수를 고려하면 이 완화 방법은 종종 Service Bus 고객의 작은 하위 집합에 필요합니다.

### <a name="service-bus-failure-on-a-single-subsystem"></a>단일 하위 시스템에서 Service Bus 오류
응용 프로그램의 경우 Service Bus의 내부 구성 요소가 일치하지 않는 상황이 발생할 수 있습니다. Service Bus가 이를 감지하는 경우 응용 프로그램에서 데이터를 수집하여 상황을 진단하기 위해 지원합니다. 데이터가 수집되면 응용 프로그램은 일관 된 상태로 반환하기 위해 다시 시작됩니다. 이 프로세스는 매우 신속하게 발생하고 엔터티가 최대 몇 분 동안 사용 불가 상태로 나타날 수 있지만 일반적인 가동 중지 시간은 훨씬 짧습니다.

이러한 경우 클라이언트 응용 프로그램은 [System.TimeoutException][System.TimeoutException] 또는 [MessagingException][MessagingException] 예외를 생성합니다. Service Bus는 자동화된 클라이언트 다시 시도 논리의 형태로 이 문제에 대한 완화 방법을 포함합니다. 다시 시도 기간이 끝나고 메시지가 전달되지 않으면 [쌍을 이루는 네임스페이스][paired namespaces]와 같은 다른 기능을 사용하여 탐색할 수 있습니다. 쌍을 이루는 네임스페이스에는 이 문서에서 설명하는 다른 주의 사항이 있습니다.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Azure 데이터 센터 내에서 Service Bus의 오류
Azure 데이터 센터에 장애에 대한 가장 가능성이 높은 원인은 Service Bus 또는 종속 시스템의 업그레이드 배포 실패입니다. 플랫폼이 완성되어 이러한 유형의 오류가 발생할 가능성이 감소했습니다. 또한 데이터 센터 장애가 다음과 같은 이유로 발생할 수 있습니다.

* 전기 중단(전력 공급 및 전력 생산 불가).
* 연결.(클라이언트와 Azure 간에 인터넷 끊김)

두 경우 모두 자연 재해 또는 인재가 문제를 발생시킵니다. 이 문제를 해결하고 메시지를 보낼 수 있는지 확인하려면 [쌍을 이루는 네임스페이스][paired namespaces]를 사용하여 기본 위치가 다시 정상인 동안 메시지를 보조 위치로 보내도록 할 수 있습니다. 자세한 정보는 [Service Bus 중단 및 재해로부터 응용 프로그램을 보호하는 모범 사례][Best practices for insulating applications against Service Bus outages and disasters]를 참조하세요.

## <a name="paired-namespaces"></a>쌍을 이루는 네임스페이스
[쌍을 이루는 네임스페이스][paired namespaces] 기능은 데이터 센터 내의 Service Bus 엔터티 또는 배포가 사용할 수 없게 된 시나리오를 지원합니다. 이 이벤트가 드물게 발생하는 반면 분산된 시스템은 여전히 최악의 시나리오를 처리하기 위해 준비되어야 합니다. 일반적으로 이 이벤트는 Service Bus가 의존 하는 일부 요소가 수명이 짧기 때문에 발생합니다. 정전 중 응용 프로그램 가용성을 유지 관리하려면 Service Bus 사용자가 가급적이면 별도 데이터 센터에서 두 개의 별도 네임스페이스를 사용하여 메시징 엔터티를 호스팅할 수 있습니다. 이 섹션의 나머지 부분에서는 다음과 같은 용어를 사용합니다.

* 기본 네임스페이스: 응용 프로그램이 상호 작용하는 네임스페이스가 작업을 보내고 받습니다.
* 보조 네임스페이스: 기본 네임스페이스에 대한 백업의 역할을 하는 네임스페이스입니다. 응용 프로그램 논리는 이 네임스페이스 상호작용하지 않습니다.
* 장애 조치 간격: 응용 프로그램이 기본 네임 스페이스에서 보조 네임스페이스로 전환하기 전에 일반적인 오류를 수락하는 시간의 양입니다.

쌍을 이루는 네임스페이스는 *가용성 보내기*를 지원합니다. 보내기 가용성은 메시지를 보내는 기능을 보존합니다. 보내기 가용성을 사용하려면 응용 프로그램이 다음 요구 사항을 충족해야 합니다.

1. 기본 네임스페이스에서 메시지를 수신할 수만 있습니다.
2. 지정된 큐 또는 토픽으로 보낸 메시지는 잘못된 순서로 도착할 수 있습니다.
3. 세션 내의 메시지가 잘못된 순서로 도착할 수 있습니다. 세션의 정상적인 기능에 문제가 생겼습니다. 응용 프로그램이 세션을 논리적으로 그룹 메시지에 사용하는 것을 의미합니다.
4. 세션 상태는 기본 네임스페이스에서 유지 관리됩니다.
5. 보조 큐가 기본 큐에 모든 메시지를 전달하기 전에 기본 큐는 온라인이 되어 메시지를 수락하기 시작할 수 있습니다.

다음 섹션에서는 API, 즉 API가 어떻게 구현되는지를 설명하고 기능을 사용하는 샘플 코드를 보여줍니다. 이 기능과 관련된 비용이 청구될 수 있습니다.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>The MessagingFactory.PairNamespaceAsync API
쌍을 이루는 네임스페이스 기능은 [Microsoft.ServiceBus.Messaging.MessagingFactory][Microsoft.ServiceBus.Messaging.MessagingFactory] 클래스에 [PairNamespaceAsync][PairNamespaceAsync] 메서드를 포함합니다.

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

작업이 완료되면 네임스페이스 쌍이 완료되고 [MessagingFactory][MessagingFactory] 인스턴스로 만든 [MessageReceiver][MessageReceiver], [QueueClient][QueueClient] 또는 [TopicClient][TopicClient]에 대해 작업할 준비가 됩니다. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]는 [MessagingFactory][MessagingFactory] 개체와 사용할 수 있는 다양한 유형의 쌍에 대한 기본 클래스입니다. 현재 유일하게 파생된 클래스는 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]이며 이는 보내기 가용성 요구 사항을 구현합니다. [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]에는 서로 간에 빌드한 생성자의 집합이 있습니다. 대부분의 매개 변수가 있는 생성자를 살펴보면 다른 생성자의 동작을 이해할 수 있습니다.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

이러한 매개 변수는 다음과 같은 의미가 있습니다.

* *secondaryNamespaceManager*: [PairNamespaceAsync][PairNamespaceAsync] 메서드가 보조 네임스페이스를 설정하는 데 사용할 수 있는 보조 네임 스페이스에 대해 초기화된 [NamespaceManager][NamespaceManager] 인스턴스입니다. 네임스페이스 관리자는 네임스페이스에서 큐 목록을 가져오고 필요한 백로그 큐가 존재하는지 확인하는 데 사용됩니다. 이러한 큐가 존재하지 않는 경우 만들어집니다. [NamespaceManager][NamespaceManager]는 **관리** 클레임으로 토큰을 만드는 기능을 필요로 합니다.
* *messagingFactory*: 보조 네임스페이스에 대한 [MessagingFactory][MessagingFactory] 인스턴스입니다. [EnableSyphon][EnableSyphon] 속성이 **true**로 설정된 경우 [MessagingFactory][MessagingFactory] 개체는 백로그 큐에서 메시지를 보내고 받는 데 사용됩니다.
* *backlogQueueCount*: 만들려는 백로그 큐의 수입니다. 이 값은 적어도 1이어야 합니다. 백로그에 메시지를 보낼 때 이러한 큐 중 하나가 무작위로 선택됩니다. 값을 1로 설정한 경우 하나의 큐만 사용될 수 있습니다. 이 문제가 발생하고 백로그 큐 하나에서 오류를 생성하면 클라이언트는 다른 백로그 큐를 시도할 수 없고 메시지를 전송하는 데 실패할 수도 있습니다. 이 값을 큰 값으로, 기본값을 10으로 설정하는 것이 좋습니다. 이 응용 프로그램에서 하루에 보내는 데이터의 크기에 따라 더 높거나 낮은 값으로 변경할 수 있습니다. 각 백로그 큐는 최대 5GB의 메시지를 보관할 수 있습니다.
* *failoverInterval*: 모든 단일 엔터티를 보조 네임스페이스로 전환하기 전에 기본 네임스페이스에 발생한 오류를 수락하는 데 걸리는 시간입니다. 장애 조치는 엔터티 단위로 발생합니다. 단일 네임스페이스의 엔터티는 주로 Service Bus 내의 다른 노드에 있습니다. 한 엔터티의 오류가 다른 오류를 의미하지는 않습니다. 이 값을 [System.TimeSpan.Zero][System.TimeSpan.Zero]로 설정하여 먼저 일시적이지 않은 오류가 발생한 후에 즉시 보조에 대한 장애 조치를 할 수 있습니다. 장애 조치 타이머를 트리거하는 오류는 [IsTransient][IsTransient] 속성이 false 또는 [System.TimeoutException][System.TimeoutException]인 [MessagingException][MessagingException]입니다. [UnauthorizedAccessException][UnauthorizedAccessException]과 같은 다른 예외는 클라이언트가 잘못 구성되어 있다고 나타내기 때문에 장애 조치를 발생하지 않습니다. [ServerBusyException][ServerBusyException]은 올바른 패턴이 10초를 기다리고 다음 메시지를 다시 보내기 때문에 장애 조치를 발생하지 않습니다.
* *enableSyphon*: 또한 이러한 특정 쌍은 보조 네임스페이스에서 기본 네임스페이스에 다시 사이펀 메시지를 나타냅니다. 일반적으로 메시지를 보내는 응용 프로그램이 이 값을 **false**로 설정합니다. 메시지를 수신하는 응용 프로그램이 값을 **true**로 설정해야 합니다. 이러한 경우가 자주 있다는 이유로 메시지 발신자 보다 적은 메시지 수신자가 더 적습니다. 수신자의 수에 따라 단일 응용 프로그램 인스턴스가 사이펀 의무를 처리하도록 선택할 수 있습니다. 여러 수신자를 사용하면 각 백로그 큐에 비용 청구의 영향을 미칩니다.

코드를 사용하려면 기본 [MessagingFactory][MessagingFactory] 인스턴스, 보조 [MessagingFactory][MessagingFactory] 인스턴스, 보조 [NamespaceManager][NamespaceManager] 인스턴스 및 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 인스턴스를 만듭니다. 호출은 다음과 같이 간단할 수 있습니다.

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

[PairNamespaceAsync][PairNamespaceAsync] 메서드에서 반환한 작업이 완료되면 모든 것이 설정되고 사용할 준비가 됩니다. 작업이 반환되기 전에 쌍이 올바로 작동하는 데 필요한 백그라운드 작업을 모두 완료하지 못했을 수도 있습니다. 결과적으로 작업을 반환할 때까지 메시지를 보내기 시작하지 말아야 합니다. 잘못된 자격 증명 또는 백로그 큐 만들기 실패와 같은 오류가 발생한 경우 작업이 완료되면 해당 예외는 throw됩니다. 작업이 반환되면 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 인스턴스에서 [BacklogQueueCount][BacklogQueueCount] 속성을 검사하여 큐를 찾거나 만들었는지 확인합니다. 앞의 코드의 경우 작업이 다음과 같이 나타납니다.

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>다음 단계
이제 Service Bus에서 비동기 메시징의 기본 사항을 알아보았으므로 [쌍을 이루는 네임스페이스]에 대한 자세한 내용을 읽습니다.[paired namespaces]

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
