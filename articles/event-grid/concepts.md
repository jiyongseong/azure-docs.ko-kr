---
title: Azure Event Grid 개념
description: Azure Event Grid 및 해당 개념을 설명합니다. Event Grid의 몇 가지 주요 구성 요소를 정의합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: fd82d163ba8407a3dfa088cd322f3e236be5d7ea
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303048"
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid의 개념

이 문서에서는 Azure Event Grid의 주요 개념을 설명합니다.

## <a name="events"></a>이벤트

이벤트는 시스템에서 발생하는 무언가를 완벽히 설명하는 가장 작은 크기의 정보입니다. 모든 이벤트에는 이벤트의 원본, 이벤트가 발생한 시간 및 고유 식별자와 같은 일반적인 정보가 있습니다. 또한 모든 이벤트에는 특정 유형의 이벤트에만 관련된 특정 정보도 있습니다. 예를 들어 Azure Storage에서 만드는 새 파일에 대한 이벤트에는 `lastTimeModified` 값과 같은 파일에 대한 세부 정보가 포함되어 있습니다. 또는 Event Hubs 이벤트는 캡처 파일의 URL을 갖습니다. 각 이벤트는 64KB의 데이터로 제한됩니다.

## <a name="event-sourcespublishers"></a>이벤트 원본/게시자

이벤트 원본은 이벤트가 발생하는 위치입니다. 예를 들어 Azure Storage는 이벤트가 생성된 Blob에 대한 이벤트 원본입니다. Azure VM 패브릭은 가상 머신 이벤트에 대한 이벤트 원본입니다. 이벤트 원본은 이벤트를 Event Grid에 게시하는 역할을 합니다.

지원되는 Event Grid 원본을 구현하는 방법에 대한 내용은 [Azure Event Grid의 이벤트 원본](event-sources.md)을 참조하세요.

## <a name="topics"></a>토픽

게시자는 이벤트를 토픽으로 분류합니다. Event Grid 토픽에는 게시자가 이벤트를 보내는 끝점이 포함되어 있습니다. 특정 이벤트 형식에 응답하려면 구독자가 구독할 토픽을 결정합니다. 또한 토픽은 구독자가 이벤트를 적절하게 사용하는 방법을 알 수 있도록 이벤트 스키마를 제공합니다.

시스템 토픽은 Azure 서비스에서 제공하는 기본 제공 항목입니다. 사용자 지정 토픽은 응용 프로그램 및 타사 토픽입니다.

응용 프로그램을 디자인할 때는 만들려는 토픽 수를 유연하게 결정할 수 있습니다. 대규모 솔루션의 경우 관련된 이벤트의 각 범주에 대한 사용자 지정 토픽을 만듭니다. 사용자 계정을 수정하고 주문을 처리하는 것과 관련된 이벤트를 보내는 응용 프로그램을 예로 들 수 있습니다. 이벤트 처리기가 이벤트의 두 범주 모두를 원할 가능성은 낮습니다. 두 개의 사용자 지정 토픽을 만들고 이벤트 처리기가 관심 있는 토픽에 구독할 수 있도록 합니다. 소규모 솔루션의 경우 모든 이벤트를 단일 토픽으로 전송할 수도 있습니다. 원하는 이벤트 유형에 대한 이벤트 구독자를 필터링할 수 있습니다.

## <a name="event-subscriptions"></a>이벤트 구독

구독은 구독자가 받고자 하는 토픽의 이벤트를 Event Grid에 지시합니다. 또한 구독에는 이벤트가 구독자에게 어떻게 배달되는지에 대한 정보가 포함됩니다.

## <a name="event-handlers"></a>이벤트 처리기

Event Grid 측면에서 볼 때 이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. Event Grid는 여러 구독자 형식을 지원합니다. 구독자의 형식에 따라 Event Grid는 이벤트의 배달을 보장하는 다양한 메커니즘을 따릅니다. HTTP 웹후크 이벤트 처리기의 경우 처리기가 `200 – OK`의 상태 코드를 반환할 때까지 이벤트를 다시 시도합니다. Azure Storage Queue의 경우 큐 서비스가 성공적으로 큐에 메시지 푸시를 처리할 수 있을 때까지 이벤트를 다시 시도합니다.

지원되는 Event Grid 처리기를 구현하는 방법에 대한 내용은 [Azure Event Grid의 이벤트 처리기](event-handlers.md)를 참조하세요.

## <a name="filters"></a>필터

Event Grid 토픽을 구독하면 끝점에 전송된 이벤트를 필터링할 수 있습니다. 이벤트 형식 또는 제목 패턴으로 필터링할 수 있습니다. 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.

## <a name="security"></a>보안

Event Grid는 토픽 구독 및 게시에 대한 보안을 제공합니다. 구독할 때 리소스 또는 Event Grid 토픽에 대해 적절한 사용 권한이 있어야 합니다. 게시할 때 토픽에 대한 SAS 토큰 또는 키 인증이 있어야 합니다. 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

## <a name="failed-delivery"></a>배달 실패

Event Grid에서 이벤트가 구독자의 끝점에서 수신되었는지 확인할 수 없는 경우 이벤트를 다시 배달합니다. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](delivery-and-retry.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
