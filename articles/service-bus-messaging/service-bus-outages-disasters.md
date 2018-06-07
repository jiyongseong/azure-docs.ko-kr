---
title: "가동 중단 및 재해로부터 Azure Service Bus 응용 프로그램 보호 | Microsoft Docs"
description: "잠재적 Service Bus 가동 중단으로부터 응용 프로그램을 보호하는 기술."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 7b01412202b5091ad3ae420089049bf456f9a30b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Service Bus 가동 중단 및 재해로부터 응용 프로그램을 보호하기 위한 모범 사례

중요 업무용 응용 프로그램은 갑작스러운 가동 중단이나 재해가 발생하더라도 계속해서 작동해야 합니다. 이 항목에서는 잠재적 서비스 가동 중단 또는 재해로부터 Service Bus 응용 프로그램을 보호하기 위해 사용할 수 있는 기술을 설명합니다.

가동 중단이란 Azure Service Bus를 일시적으로 사용할 수 없게 됨을 의미합니다. 가동 중단은 메시징 저장소와 같은 Service Bus의 일부 구성 요소에 영향을 줄 수 있으며 때로는 전체 데이터센터에도 영향을 줄 수 있습니다. 문제가 해결되면 Service Bus가 다시 사용할 수 있는 상태가 됩니다. 일반적으로 가동 중단으로 인해 메시지나 기타 데이터는 손실되지는 않습니다. 구성 요소 오류의 예로 특정 메시지 저장소를 사용할 수 없는 경우를 들 수 있습니다. 데이터센터 범위의 가동 중단으로는 데이터센터의 전원 문제나 잘못된 데이터센터 네트워크 전환을 예로 들 수 있습니다. 가동 중단은 몇 분에서 며칠까지 지속될 수 있습니다.

재해란 Service Bus 범위의 장치 또는 데이터센터의 영구적인 손실을 의미합니다. 데이터센터는 다시 사용할 수 있게 될 수도, 그렇지 않을 수도 있습니다. 일반적으로 재해가 발생하면 메시지나 기타 데이터의 일부 또는 전체를 잃게 됩니다. 재해의 예로는 화재, 홍수 또는 지진이 있습니다.

## <a name="current-architecture"></a>현재 아키텍처
Service Bus는 여러 메시징 저장소를 사용하여 큐 또는 항목에 전송되는 메시지를 저장합니다. 분할되지 않은 큐나 항목은 하나의 메시징 저장소에 할당됩니다. 이 메시지 저장소를 사용할 수 없게 되면 해당 큐 또는 항목의 모든 작업이 실패하게 됩니다.

모든 Service Bus 메시징 엔터티(큐, 항목, 릴레이)는 데이터센터와 연결된 서비스 네임스페이스에 상주합니다. 이제 Service Bus는 네임스페이스 수준에서 [*지역 재해 복구* 및 *지역 복제*](service-bus-geo-dr.md)를 지원합니다.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>메시지 저장소 오류로부터 큐와 항목 보호
분할되지 않은 큐나 항목은 하나의 메시징 저장소에 할당됩니다. 이 메시지 저장소를 사용할 수 없게 되면 해당 큐 또는 항목의 모든 작업이 실패하게 됩니다. 반면에 분할된 큐는 여러 조각으로 구성됩니다. 각 조각은 서로 다른 메시징 저장소에 저장됩니다. 분할된 큐 또는 항목으로 메시지를 보내면 Service Bus는 메시지를 조각 중 하나에 할당합니다. 해당 메시징 저장소를 사용할 수 없는 경우, Service Bus는 가능한 다른 조각에 메시지를 씁니다. 분할된 엔터티에 대한 자세한 내용은 [분할된 메시지 엔터티][Partitioned messaging entities]를 참조하세요.

## <a name="protecting-against-datacenter-outages-or-disasters"></a>데이터센터 가동 중단 또는 재해로부터 보호
두 데이터센터 간의 장애 조치(failover)를 허용하려면 각 데이터센터에 Service Bus 서비스 네임스페이스를 만들 수 있습니다. 예를 들어, Service Bus 서비스 네임스페이스 **contosoPrimary.servicebus.windows.net**은 미국(북부/중부) 하위 지역에 있고 **contosoSecondary.servicebus.windows.net**은 미국(남부/중부) 하위 지역에 있을 수 있습니다. 데이터센터가 가동 중단되어도 Service Bus 메시징 엔터티를 액세스할 수 있도록 하려면 이 엔터티를 두 네임스페이스 모두에 만들 수 있습니다.

자세한 내용은 [비동기 메시지 패턴 및 고가용성][Asynchronous messaging patterns and high availability]의 "Azure 데이터 센터 내의 Service Bus 오류" 섹션을 참조하세요.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>데이터센터 가동 중단 또는 재해로부터 릴레이 끝점 보호
릴레이 끝점의 지역 복제를 사용하면 Service Bus 가동 중단 시에도 릴레이 끝점이 있는 서비스에 연결할 수 있습니다. 지역 복제를 하려면 서비스에서 서로 다른 네임스페이스에 두 릴레이 끝점을 만들어야 합니다. 네임스페이스는 서로 다른 데이터센터에 있어야 하고 두 끝점 이름이 서로 달라야 합니다. 예를 들어, 기본 끝점은 **contosoPrimary.servicebus.windows.net/myPrimaryService**에서 연결할 수 있고 이에 상응하는 보조 끝점은 **contosoSecondary.servicebus.windows.net/mySecondaryService**에서 연결할 수 있습니다.

서비스는 두 끝점을 모두를 수신하고, 클라이언트는 두 끝점 중 어디를 통해서든 서비스를 호출할 수 있습니다. 클라이언트 응용 프로그램은 기본 끝점에서 임의로 하나의 릴레이를 선택하고, 활성 끝점으로 요청을 보냅니다. 오류 코드가 표시되고 작업이 실패하면, 이 오류는 릴레이 끝점을 사용할 수 없음을 나타냅니다. 응용 프로그램은 백업 끝점으로의 채널을 열고, 요청을 다시 발송합니다. 이때 활성 끝점과 백업 끝점의 역할이 서로 바뀝니다. 클라이언트 응용 프로그램은 이전 활성 끝점을 새 백업 끝점으로, 이전 백업 끝점을 새 활성 끝점으로 간주합니다. 두 보내기 작업이 모두 실패하면, 두 엔터티의 역할이 바뀌지 않고 유지되며 오류가 반환됩니다.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>데이터센터 가동 중단 또는 재해로부터 큐 또는 항목 보호
조정된 메시징을 사용할 때 데이터센터 가동 중단 시에도 가용성을 유지하기 위해 Service Bus는 *능동*과 *수동* 복제의 두 방식을 지원합니다. 각 방식에서, 지정된 큐 또는 항목이 데이터센터 가동 중단 상태에서도 액세스 가능하려면 양쪽 네임스페이스에서 큐 또는 항목을 모두 만들어야 합니다. 두 엔터티는 동일한 이름을 가질 수 있습니다. 예를 들어, 기본 큐는 **contosoPrimary.servicebus.windows.net/myQueue**에서 연결할 수 있고 이에 상응하는 보조 큐는 **contosoSecondary.servicebus.windows.net/myQueue**에서 연결할 수 있습니다.

응용 프로그램에 영구적인 발신자-수신자 통신이 필요하지 않을 경우, 메시지 손실을 막고 일시적 Service Bus 오류로부터 발신자를 보호하기 위해 지속형 클라이언트 쪽 큐를 구현할 수 있습니다.

## <a name="active-replication"></a>능동 복제
능동 복제는 모든 작업에서 양쪽 네임스페이스의 엔터티를 사용합니다. 메시지를 보내는 클라이언트는 동일한 메시지의 두 복사본을 보냅니다. 첫 번째 복사본은 기본 엔터티(예: **contosoPrimary.servicebus.windows.net/sales**)로 전송되고, 메시지의 두 번째 복사본은 보조 엔터티(예: **contosoSecondary.servicebus.windows.net/sales**)로 전송됩니다.

클라이언트는 두 큐에서 모두 메시지를 받습니다. 수신자는 메시지의 첫 번째 복사본을 처리하고 두 번째 복사본은 표시하지 않습니다. 중복 메시지를 표시하지 않기 위해 발송자는 각 메시지에 고유 식별자로 태그를 지정해야 합니다. 메시지의 두 복사본 모두 동일한 식별자로 태그가 지정되어야 합니다. 메시지에 태그를 지정하기 위해 [BrokeredMessage.MessageId][BrokeredMessage.MessageId]나 [BrokeredMessage.Label][BrokeredMessage.Label] 속성 또는 사용자 지정 속성을 사용할 수 있습니다. 수신자는 이미 받은 메시지의 목록을 유지해야 합니다.

[Service Bus 조정된 메시지를 사용한 지역 복제][Geo-replication with Service Bus Brokered Messages] 샘플을 통해 메시지 엔터티를 능동 복제하는 방법을 볼 수 있습니다.

> [!NOTE]
> 능동 복제 방식은 작업의 수가 두 배이므로 그만큼 비용이 많이 소요됩니다.
> 
> 

## <a name="passive-replication"></a>수동 복제
오류가 없는 경우 수동 복제는 두 메시징 엔터티 중 하나만 사용합니다. 클라이언트는 활성 엔터티로 메시지를 보냅니다. 활성 엔터티를 호스팅하는 데이터센터를 사용할 수 없음을 나타내는 오류 코드와 함께 활성 엔터티의 작업에 실패하는 경우 클라이언트는 메시지의 복사본을 백업 엔터티로 보냅니다. 이때 활성 엔터티와 백업 엔터티의 역할이 서로 바뀝니다. 전송 클라이언트는 이전 활성 엔터티를 새 백업 엔터티로, 이전 백업 엔터티를 새 활성 엔터티로 간주합니다. 두 보내기 작업이 모두 실패하면, 두 엔터티의 역할이 바뀌지 않고 유지되며 오류가 반환됩니다.

클라이언트는 두 큐에서 모두 메시지를 받습니다. 수신자가 동일한 메시지의 두 복사본을 받을 가능성이 있기 때문에, 수신자는 중복 메시지를 표시하지 않아야 합니다. 능동 복제에서 설명한 것과 동일한 방법으로 중복 메시지를 숨길 수 있습니다.

일반적으로 대부분의 경우 하나의 작업만 수행되기 때문에 수동 복제가 능동 복제보다 더 경제적입니다. 대기 시간, 처리량 및 금전적 비용은 비복제 시나리오와 동일합니다.

수동 복제를 사용하는 경우 다음과 같은 경우에 메시지가 유실되거나 중복되어 수신될 수 있습니다.

* **메시지 지연 또는 손실**: 발신자가 메시지 m1을 기본 큐로 보내고, 수신자가 m1을 받기 전에 큐가 사용할 수 없는 상태가 되었다고 가정해 보겠습니다. 발신자는 후속 메시지 m2를 보조 큐로 보냅니다. 기본 큐가 일시적으로 사용할 수 없는 상태였다면 수신자는 큐가 다시 사용 가능하게 된 후에 m1을 받게 됩니다. 재해가 발생한 경우라면 수신자가 m1을 아예 받지 못할 수도 있습니다.
* **중복 수신**: 발신자가 메시지 m을 기본 큐로 보냈다고 가정해 보겠습니다. Service Bus에서 성공적으로 m을 처리했지만 응답을 보내는 데는 실패합니다. 보내기 작업 시간이 초과된 후, 발신자는 m의 동일한 복사본을 보조 큐에 보냅니다. 기본 큐가 사용할 수 없는 상태가 되기 전에 수신자가 m의 첫 번째 복사본을 받을 수 있었다면, 수신자는 거의 동일한 시기에 두 복사본을 받게 됩니다. 기본 큐가 사용할 수 없는 상태가 되기 전에 수신자가 m의 첫 번째 복사본을 받을 수 없었다면, 수신자는 먼저 m의 두 번째 복사본만 받게 되고, 기본 큐가 다시 사용할 수 있게 되면 첫 번째 복사본도 받게 됩니다.

[Service Bus 조정된 메시지를 사용한 지역 복제][Geo-replication with Service Bus Brokered Messages] 샘플을 통해 메시지 엔터티를 수동 복제하는 방법을 볼 수 있습니다.

## <a name="geo-replication"></a>지역에서 복제

Service Bus는 네임스페이스 수준에서 지역 재해 복구 및 지역 복제를 지원합니다. 자세한 내용은 [Azure Service Bus 지역 재해 복구](service-bus-geo-dr.md)를 참조하세요. [Premium SKU](service-bus-premium-messaging.md)에서만 사용할 수 있는 재해 복구 기능은 메타데이터 재해 복구를 구현하며 주 및 보조 재해 복구 네임스페이스를 사용합니다.

## <a name="next-steps"></a>다음 단계
재해 복구에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Service Bus 지역 재해 복구](service-bus-geo-dr.md)
* [Azure SQL Database 비즈니스 연속성][Azure SQL Database Business Continuity]
* [Azure용 복원 응용 프로그램 디자인][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency
