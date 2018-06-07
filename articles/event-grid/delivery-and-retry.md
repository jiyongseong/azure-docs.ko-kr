---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 8eb6717369b48289bd31dcd1972ce275bc550c77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도 

이 문서에서는 Azure Event Grid에서 배달이 승인되지 않는 경우 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 웹후크로 즉시 전송됩니다. 웹후크가 처음 배달 시도의 처음 60초 이내에 이벤트 수신을 승인하지 않는 경우 Event Grid는 이벤트의 배달을 다시 시도합니다. 

현재 Event Grid는 각 이벤트를 개별적으로 구독자에게 보냅니다. 구독자는 단일 이벤트로 배열을 받습니다.

## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

다음 HTTP 응답 코드는 이벤트가 사용자 웹후크에 성공적으로 전달되었음을 나타냅니다. Event Grid는 배달이 완료되었다고 간주합니다.

- 200 정상
- 202 수락됨

### <a name="failure-codes"></a>실패 코드

다음 HTTP 응답 코드는 이벤트 배달 시도가 실패했음을 나타냅니다. 

- 400 잘못된 요청
- 401 권한 없음
- 404 찾을 수 없음
- 408 요청 시간 초과
- 414 URI가 너무 김
- 500 내부 서버 오류
- 503 서비스를 사용할 수 없음
- 504 게이트웨이 시간 초과

Event Grid는 끝점을 사용할 수 없다는 오류를 수신하면 해당 이벤트를 보내려고 다시 시도합니다. 

## <a name="retry-intervals-and-duration"></a>다시 시도 간격 및 기간

Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. 웹후크가 응답하지 않거나 오류 코드를 반환하는 경우 Event Grid는 다음 일정에 따라 배달을 다시 시도합니다.

1. 10초
2. 30초
3. 1분
4. 5분
5. 10분
6. 30분
7. 1시간

Event Grid는 약간의 불규칙을 모든 다시 시도 간격에 추가합니다. 한 시간 후에 이벤트 배달은 한 시간에 한 번 다시 시도됩니다.

기본적으로 Event Grid는 24시간 이내에 배달되지 않는 모든 이벤트를 만료합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.