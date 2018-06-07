---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
다음 표는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)에 지정된 할당량과 제한을 나열합니다. Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

| 제한 | 범위 | 메모 | 값 |
| --- | --- | --- | --- | --- |
| 네임스페이스당 Event Hubs 개수 |네임스페이스 |새로운 Event Hub 생성에 대한 이후 요청은 거부됩니다. |10 |
| Event Hub당 파티션 수 |엔터티 |- |32 |
| Event Hub당 소비자 그룹 수 |엔터티 |- |20 |
| 네임스페이스당 AMQP 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |5,000 |
| Event Hubs 이벤트의 최대 크기|엔터티 |- |256 KB |
| Event Hub 이름의 최대 크기 |엔터티 |- |50자 |
| 소비자 그룹당 비 Epoch 수신자 수 |엔터티 |- |5 |
| 이벤트 데이터의 최대 보존 기간 |엔터티 |- |1-7일 |
| 최대 처리량 단위 |네임스페이스 |처리량 단위 제한을 초과하면 데이터가 정체되고 **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**이 생성됩니다. 표준 계층의 경우 [지원 요청](/azure/azure-supportability/how-to-create-azure-support-request)을 작성하여 더 많은 수의 처리량 단위를 요청할 수 있습니다. [추가 처리량 단위](../articles/event-hubs/event-hubs-auto-inflate.md)는 약정된 구매를 기준으로 20개 단위로 사용할 수 있습니다. |20 |
| 네임스페이스당 권한 부여 규칙의 수 |네임스페이스|권한 부여 규칙 만들기를 위한 후속 요청이 거부됩니다.|12 |
