---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 05/10/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 3be379c2513fa20c1a84b547333a4ef2139bb45d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
다음 표에는 Service Bus 메시징에 특정한 할당량 정보가 나와 있습니다. Service Bus에 대한 가격 책정 및 기타 할당량에 대한 정보는 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/) 개요를 참조하세요.

| 할당량 이름 | 범위 | 메모 | 값 |
| --- | --- | --- | --- | --- |
| Azure 구독당 기본 / 표준 네임스페이스 최대 수 |네임스페이스 |기본 / 표준 추가 네임스페이스에 대한 후속 요청은 포털에서 거부됩니다. |100|
| Azure 구독당 프리미엄 네임스페이스 최대 수 |네임스페이스 |프리미엄 추가 네임스페이스에 대한 후속 요청은 포털에서 거부됩니다. |10 |
| 큐/토픽 크기 |엔터티 |큐/토픽을 만들 때 정의됨 <br/><br/> 들어오는 후속 메시지가 거부되며 호출 코드에서 예외를 수신합니다. |1, 2, 3, 4 또는 5GB입니다.<br /><br />프리미엄 SKU에서 [분할](../articles/service-bus-messaging/service-bus-partitioning.md) 가능하게 설정된 표준뿐만 아니라 최대 큐/토픽 크기는 80GB입니다. |
| 네임스페이스에 대한 동시 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. REST 작업은 동시 TCP 연결 계산에 포함되지 않습니다. |NetMessaging: 1,000<br /><br />AMQP: 5,000 |
| 큐/토픽/구독 엔터티의 동시 수신 요청 수 |엔터티 |후속 수신 요청이 거부되며 호출 코드에서 예외를 수신합니다. 이 할당량은 항목의 모든 구독 전반에 걸쳐 종합된 동시 수신 명령 수에 적용됩니다. |5,000 |
| 서비스 네임스페이스당 토픽/큐 수 |네임스페이스 |서비스 네임스페이스에 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |10000<br /><br />서비스 네임스페이스에서 토픽 및 큐의 총수는 10,000 이하이어야 합니다.<br/>모든 엔터티가 분할되기 때문에 프리미엄에 적용되지 않습니다. |
| 서비스 네임스페이스당 분할된 토픽/큐 수 |네임스페이스 |서비스 네임스페이스에 분할된 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출되는 경우 호출 코드에서 **QuotaExceededException** 예외가 수신됩니다. |기본 및 표준 계층 - 100<br />[프리미엄](../articles/service-bus-messaging/service-bus-premium-messaging.md) - 1,000(메시징 단위당)<br/><br />각 분할된 큐 또는 토픽은 네임스페이스 당 10,000개의 엔터티를 할당량으로 계산합니다. |
| 모든 메시징 엔터티 경로의 최대 크기: 큐 또는 토픽 |엔터티 |- |260자 |
| 모든 메시징 엔터티 이름의 최대 크기: 네임스페이스, 구독 또는 구독 규칙 |엔터티 |- |50자 |
| 큐/토픽/구독 엔터티의 메시지 크기 |엔터티 |이러한 할당량을 초과하는 들어오는 메시지가 거부되며 호출 코드에서 예외를 수신합니다. |최대 메시지 크기: 256KB([표준 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md))/1MB([프리미엄 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />**참고** 시스템 오버헤드로 인해 이 제한은 일반적으로 약간 낮아집니다.<br /><br />최대 헤더 크기: 64KB<br /><br />속성 모음에서 헤더 속성의 최대 수: **byte/int.MaxValue**<br /><br />속성 모음의 최대 속성 크기: 명시적 제한은 없습니다. 최대 헤더 크기로 제한됩니다. |
| 큐/토픽/구독 엔터티의 메시지 속성 크기 |엔터티 |A **SerializationException** 예외가 생성됩니다. |각 속성에 대한 최대 메시지 속성 크기는 32K입니다. 모든 속성의 누적 크기는 64K를 초과할 수 없습니다. [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)의 전체 헤더에 적용되며 여기에는 모두 시스템 속성뿐만 아니라 사용자 속성(예: [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) 등)이 포함됩니다. |
| 토픽당 구독 수 |엔터티 |토픽에 추가 구독을 만들기 위한 후속 요청이 거부됩니다. 따라서 포털을 통해 구성된 경우 오류 메시지가 표시됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |2,000 |
| 토픽당 SQL 필터 수 |엔터티 |토픽에서 추가 필터를 생성하기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |2,000 |
| 토픽당 상관 관계 필터 수 |엔터티 |토픽에서 추가 필터를 생성하기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |100,000 |
| SQL 필터/작업의 크기 |네임스페이스 |추가 필터를 생성하기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |필터 조건 문자열의 최대 길이: 1024(1K)<br /><br />규칙 작업 문자열의 최대 길이: 1024(1K)<br /><br />규칙 작업당 식의 최대 수: 32 |
| 네임스페이스, 큐 또는 토픽당 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙 수 |엔터티, 네임스페이스 |추가 규칙을 생성하기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |최대 규칙 수: 12 <br /><br /> Service Bus 네임스페이스에 구성된 규칙이 해당 네임스페이스에 있는 모든 큐 및 토픽에 적용됩니다. |
| 트랜잭션당 메시지 수 | 트랜잭션 | 추가로 들어오는 메시지는 거부되고 호출 코드에서 "단일 트랜잭션에 100개가 넘는 메시지를 보낼 수 없습니다"라는 예외를 수신합니다. | 100 <br /><br /> **Send()** 및 **SendAsync()** 작업 모두에 해당합니다. |

[Azure portal]: https://portal.azure.com