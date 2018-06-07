---
title: Azure Event Grid 메시지 배달 모니터링
description: Azure Event Grid 메시지 배달을 모니터링하는 방법을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: f9719bb1f1563c55537c7ef32278411a2034bd75
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid 메시지 배달 모니터링 

이 문서에서는 포털을 사용하여 이벤트 배달 상태를 확인하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 웹후크로 즉시 전송됩니다. 웹후크가 처음 배달 시도의 처음 60초 이내에 이벤트 수신을 승인하지 않는 경우 Event Grid는 이벤트의 배달을 다시 시도합니다.

이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.

## <a name="delivery-metrics"></a>배달 메트릭

포털에는 이벤트 메시지 배달 상태에 대한 메트릭이 표시됩니다.

토픽에 대한 메트릭은 다음과 같습니다.

* **게시 성공**: 이벤트가 성공적으로 토픽으로 전송되고 2xx 응답으로 처리되었습니다.
* **게시 실패**: 이벤트가 토픽으로 전송되었지만 거부되고 오류 코드가 표시되었습니다.
* **일치하지 않음**: 이벤트가 성공적으로 토픽에 게시되었지만 이벤트 구독과 일치하지 않습니다. 이벤트가 삭제되었습니다.

구독에 대한 메트릭은 다음과 같습니다.

* **배달 성공**: 이벤트가 성공적으로 구독 끝점으로 배달되고 2xx 응답을 받았습니다.
* **배달 실패**: 이벤트가 구독 끝점으로 전송되었지만 4xx 또는 5xx 응답을 받았습니다.
* **만료된 이벤트**: 이벤트가 배달되지 않았으며 다시 시도 횟수가 모두 전송되었습니다. 이벤트가 삭제되었습니다.
* **일치된 이벤트**: 토픽의 이벤트가 이벤트 구독과 일치되었습니다.

## <a name="event-subscription-status"></a>이벤트 구독 상태

이벤트 구독에 대한 메트릭을 보려면 사용 가능한 서비스에서 **Event Grid 구독**을 검색하여 선택합니다.

![이벤트 구독 검색](./media/monitor-event-delivery/select-event-subscriptions.png)

이벤트 유형, 구독 및 위치를 기준으로 필터링합니다. 보려는 구독에 대한 **메트릭**을 선택합니다.

![이벤트 구독 필터링](./media/monitor-event-delivery/filter-events.png)

이벤트 토픽 및 구독에 대한 메트릭을 봅니다.

![이벤트 메트릭 보기](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>사용자 지정 이벤트 상태

사용자 지정 토픽을 게시한 경우 해당 메트릭을 볼 수 있습니다. 토픽이 포함된 리소스 그룹을 선택한 다음 토픽을 선택합니다.

![사용자 지정 토픽 선택](./media/monitor-event-delivery/select-custom-topic.png)

사용자 지정 이벤트 토픽에 대한 메트릭을 봅니다.

![이벤트 메트릭 보기](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
