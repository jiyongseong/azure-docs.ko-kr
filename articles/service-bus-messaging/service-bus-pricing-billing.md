---
title: "Service Bus 가격 및 대금 청구 | Microsoft Docs"
description: "Service Bus 가격 구조 개요"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 8ccb44b5009588c28bc79bb45e1a7640ead6c817
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus 가격 및 대금 청구

Azure Service Bus는 표준 및 [프리미엄](service-bus-premium-messaging.md) 계층으로 제공됩니다. 자신이 만든 Service Bus 서비스 네임스페이스 각각에 서비스 계층을 선택할 수 있으며, 이 계층 선택은 해당 네임스페이스 안에서 모든 엔터티에 적용됩니다.

> [!NOTE]
> 최신 Service Bus 가격 책정에 대한 자세한 내용은 [Azure Service Bus 가격 책정 페이지](https://azure.microsoft.com/pricing/details/service-bus/) 및 [Service Bus FAQ](service-bus-faq.md#pricing)를 참조하세요.
>
>

Service Bus는 다음 두 미터를 큐와 토픽/구독에 사용합니다.

1. **메시징 운영**: 큐 또는 토픽/구독 서비스 끝점에 대한 API 호출로 정의됩니다. 이 미터는 보내거나 받은 메시지를 큐 및 토픽/구독에 대한 청구 가능 사용량의 주 단위로 대체합니다.
2. **조정된 연결**: 특정 1시간 샘플링 기간 동안 큐, 토픽 또는 구독에 대해 열린 영구 연결의 최대 수로 정의합니다. 이 미터는 명시적 연결당 비용에 대해 추가적인 연결을 열 수 있는 표준 계층에만 적용됩니다(이전에는 연결이 큐/토픽/구독당 100개로 제한되었음).

**표준** 계층은 최고 사용량 수준에서 최대 80% 수준의 볼륨 기반 할인이 가능한 큐 및 토픽/구독에서 수행한 작업에 대해 누진 가격 책정을 도입하고 있습니다. 추가 비용 없이 최대 1250만 개의 작업을 수행할 수 있는 매월 10달러의 표준 계층 기본 요금도 있습니다.

**프리미엄** 계층은 각 고객의 작업이 따로 실행되도록 CPU 및 메모리 계층에서 리소스 격리를 제공합니다. 이 리소스 컨테이너를 *메시징 단위*라고 합니다. 각 프리미엄 네임스페이스에는 하나 이상의 메시징 단위가 할당됩니다. 각 Service Bus 프리미엄 네임스페이스에 대해 1, 2 또는 4 메시징 단위를 구입할 수 있습니다. 요금은 24시간 단위 또는 일별 요금으로 부과되더라도 단일 워크로드 또는 엔터티가 여러 메시징 단위에 걸쳐 있을 수 있고 메시징 단위 수를 변경할 수도 있습니다. 그 결과, Service Bus 기반 솔루션에 대해 예측 가능하고 반복 가능한 성능이 구현됩니다. 이로 인해 예측 가능성 및 가용성도 높아질 뿐 아니라 속도도 더 빨라집니다.

표준 기본 계층 기본 요금은 Azure 구독별로 매월 한 번만 청구됩니다. 즉, 표준 계층 Service Bus 네임스페이스를 하나 만든 후에는 추가 기본 요금을 발생시키지 않으면서 동일한 Azure 구독에서 추가로 표준 네임스페이스를 원하는 만큼 만들 수 있습니다.

다음 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/) 표에서는 표준 및 프리미엄 계층 간의 기능적 차이점에 대해 요약합니다.

## <a name="messaging-operations"></a>메시징 운영

큐 및 토픽/구독은 메시지가 아닌 "작업" 기준으로 요금이 청구됩니다. "작업"은 큐나 토픽/구독 서비스 끝점에 대한 모든 API 호출을 의미합니다. 여기에는 관리, 송신/수신 및 세션 상태 작업이 포함됩니다.

| 작업 유형 | 설명 |
| --- | --- |
| 관리 |큐나 토픽/구독에 대한 만들기, 읽기, 업데이트, 삭제(CRUD) |
| 메시징 |큐나 토픽/구독에서 메시지 송신 및 수신 |
| 세션 상태 |큐나 토픽/구독에서 세션 상태 가져오기 또는 설정. |

비용 정보에 대해서는 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/) 페이지에 나열된 가격을 참조하세요.

## <a name="brokered-connections"></a>조정된 연결

*조정된 연결*에서는 큐, 토픽 또는 구독에 대해 대용량의 "영구적인 연결"을 포함하는 사용 패턴을 허용합니다. 영구적으로 연결된 발신자/수신자는 0이 아닌 수신 시간 제한(예: HTTP 긴 폴링)이 있는 AMQP나 HTTP를 사용하여 연결합니다. 즉각적인 시간 제한이 적용된 HTTP 발신자와 수신자는 조정된 연결을 생성하지 않습니다.

연결 할당량 및 기타 서비스 제한에 대해서는 [Service Bus 할당량](service-bus-quotas.md) 문서를 참조하세요. 조정된 연결에 대한 자세한 내용은 이 문서의 뒷부분에 나오는 [FAQ](#faq) 섹션을 참조하세요.

표준 계층은 네임스페이스당 조정된 연결 제한이 없으며 전체 Azure 구독에서 조정된 연결 사용을 집계 산출합니다. 자세한 내용은 [조정된 연결](https://azure.microsoft.com/pricing/details/service-bus/) 표를 참조하세요.

> [!NOTE]
> 1,000개의 조정된 연결이 표준 메시징 계층에 포함되며(기본 요금) 관련 Azure 구독 내의 모든 큐, 토픽 또는 구독 전체에서 공유할 수 있습니다.
>
>

<br />

> [!NOTE]
> 대금 청구는 최대 동시 연결 수를 기준으로 하며 매월 744시간을 기준으로 시간 비례 책정됩니다.
>
>

### <a name="premium-tier"></a>프리미엄 계층

프리미엄 계층에서는 조정된 연결에 요금이 부과되지 않습니다.

## <a name="faq"></a>FAQ

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>조정된 연결이 무엇이며 요금은 어떻게 청구되나요?

조정된 연결은 다음 중 하나로 정의됩니다.

1. 클라이언트에서 Service Bus 큐 또는 토픽/구독으로의 AMQP 연결.
2. 수신 시간 제한 값이 0보다 큰 Service Bus 토픽이나 큐에서 메시지를 수신하는 HTTP 호출.

Service Bus는 포함된 수량(표준 계층의 1,000개)을 초과하는 최대 동시 조정된 연결 수에 대해 청구됩니다. 최대 값은 시간을 기준으로 한 달에 744시간으로 나누어 비례 산출하고 월별 청구 기간에 따라 합하여 측정합니다. 포함된 수량(1,000개의 조정된 연결/월)은 비례 산출한 시간별 최대값의 합에 대해 청구 기간의 종료 시점에 적용됩니다.

예: 

1. 10,000개의 장치가 각각 단일 AMQP 연결을 통해 연결되며 Service Bus 토픽으로부터 명령을 받습니다. 장치는 이벤트 허브로 원격 분석 이벤트를 보냅니다. 모든 장치가 매일 12시간 동안 연결하는 경우 다음 연결 요금이 적용됩니다(타 Service Bus 토픽 요금 외에 추가): 연결 10,000개 * 12시간 * 31일/744=조정된 연결 5,000개. 월별 허용 조정된 연결 1,000개 이후에는 4,000개의 조정된 연결에 대해 조정된 연결 1개당 0.03달러의 요금이 부과되어 총 120달러입니다.
2. 10,000개의 장치가 시간 제한이 0이 아니며 HTTP를 통해 Service Bus 큐에서 메시지를 수신합니다. 모든 장치가 매일 12시간 동안 연결하는 경우 다음 연결 요금이 적용됩니다(타 Service Bus 요금 외에 추가): HTTP 수신 연결 10,000개 * 일일 12시간 * 31일/744=조정된 연결 5,000개.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>조정된 연결 요금은 큐와 토픽/구독에 적용되나요?

예. 시스템 또는 장치 수에 관계 없이 HTTP를 사용하여 이벤트를 보내는 데에는 연결 요금이 부과되지 않습니다. 0이 아닌 시간 제한을 사용한 HTTP를 통한 이벤트 수신은 경우에 따라 "긴 폴링"이라고 하며 조정된 연결 요금이 발생합니다. AMQP 연결에서는 연결이 송신 또는 수신에 사용되는지 여부에 관계없이 조정된 연결 요금이 발생합니다. Azure 구독의 모든 표준 네임스페이스에서 최초 1,000개의 조정된 연결은 추가 비용 없이(기본 요금 외) 포함되어 있습니다. 이러한 허용치는 여러 서비스 간 메시징 상황에 충분히 부합하므로, 조정된 연결 요금은 보통 대규모 클라이언트에서 AMQP나 HTTP 롱 폴링을 사용하려는 경우에만 관련이 있습니다. 더 효율적인 이벤트 스트리밍이나 여러 장치 또는 응용 프로그램 인스턴스와의 양방향 통신을 구현하려는 경우를 예로 들 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Service Bus 가격 책정에 대한 자세한 내용은 [Service Bus 가격 책정 페이지](https://azure.microsoft.com/pricing/details/service-bus/)를 참조하세요.
* Service Bus 가격 책정 및 대금 청구에 대한 몇 가지 일반적인 FAQ를 보려면 [Service Bus FAQ](service-bus-faq.md#pricing)를 참조하세요.

[Azure portal]: https://portal.azure.com
