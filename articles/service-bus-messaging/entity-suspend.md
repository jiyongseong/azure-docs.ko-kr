---
title: "Azure Service Bus 메시징 엔터티 일시 중단 | Microsoft Docs"
description: "Azure Service Bus 메시지 엔터티를 일시 중단한 후 다시 활성화합니다."
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
ms.openlocfilehash: 1984b113f695107f8d4d80e5bbf25c7dc39d13f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2018
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>메시징 엔터티 일시 중단 및 다시 활성화(사용 안 함)

큐, 토픽 및 구독을 일시적으로 중단할 수 있습니다. 일시 중단을 수행하면 엔터티가 사용 안 함 상태가 되며 모든 메시지는 저장소에 유지됩니다. 그러나 메시지를 제거 또는 추가할 수 없으며 해당 프로토콜 작업은 오류를 생성합니다.

엔터티 일시 중단은 일반적으로 긴급한 관리상의 이유로 수행됩니다. 한 가지 시나리오는 큐에서 메시지를 제거하고, 처리를 실패하고, 메시지를 잘못 완료 및 제거하는 잘못된 수신기를 배포하는 경우입니다. 해당 동작이 진단되면 수정된 코드를 배포할 때까지 큐를 사용하지 않도록 설정하고, 결함 있는 코드에 의한 추가 데이터 손실을 방지할 수 있습니다.

사용자 또는 시스템에 의해 일시 중단 또는 다시 활성화가 수행될 수 있습니다. 시스템은 구독 지출 제한에 도달하는 등, 중대한 관리상의 이유가 있을 때만 엔터티를 일시 중단합니다. 시스템에서 사용하지 않도록 설정된 엔터티는 사용자가 다시 활성화할 수 없지만 일시 중단의 원인에 해결되면 복원됩니다.

포털에서 해당 엔터티에 대한 **속성** 섹션은 상태 변경을 허용합니다. 다음 스크린샷은 큐의 설정/해제를 보여 줍니다.

![][1]

포털에서는 큐를 완전히 사용하지 않도록 설정하는 것만 허용합니다. .NET Framework SDK의 Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API를 사용하거나 Azure CLI 또는 Azure PowerShell을 통해 Azure Resource Manager 템플릿을 사용하여 송신 및 수신 작업을 별도로 사용하지 않도록 설정할 수도 있습니다.

## <a name="suspension-states"></a>일시 중단 상태

큐에 대해 설정할 수 있는 상태는 다음과 같습니다.

-   **Active**: 큐가 활성 상태입니다.
-   **Disabled**: 큐가 일시 중단되었습니다.
-   **SendDisabled**: 큐가 부분적으로 일시 중단되었으며 수신이 허용됩니다.
-   **ReceiveDisabled**: 큐가 부분적으로 일시 중단되었으며 송신이 허용됩니다.

구독 및 토픽의 경우 **Active** 및 **Disabled**만 설정할 수 있습니다.

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 열거형은 시스템에 의해서만 설정될 수 있는 전환 상태 집합도 정의합니다. 큐를 사용하지 않도록 설정하는 PowerShell 명령은 다음 예제에 나와 있습니다. 재활성화 명령은 동일하게 `Status`를 **Active**로 설정합니다.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 기본 사항](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

