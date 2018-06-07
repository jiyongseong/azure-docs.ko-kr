---
title: Azure Event Grid 이벤트 처리기
description: Azure Event Grid에 대한 지원되는 이벤트 처리기에 대해 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 996bd4b3497861a3bfcbfecebe18a6936f487028
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301770"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid의 이벤트 처리기

이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 여러 Azure 서비스는 이벤트를 처리하도록 자동으로 구성됩니다. 이벤트를 처리하기 위해 웹후크를 사용할 수도 있습니다. 웹후크는 이벤트를 처리하기 위해 Azure에 호스트될 필요는 없습니다.

이 문서에서는 각 이벤트 처리기에 대한 콘텐츠 링크를 제공합니다.

## <a name="azure-automation"></a>Azure Automation

자동화된 Runbook과 함께 Azure Automation을 사용하여 이벤트를 처리합니다.

|제목  |설명  |
|---------|---------|
|[Event Grid와 Azure Automation 통합 및 Microsoft 팀](ensure-tags-exists-on-new-virtual-machines.md) |이벤트를 전송 하는 가상 머신을 만듭니다. 이벤트는 가상 머신에 태그를 지정하는 Automation Runbook을 트리거하고, Microsoft 팀 채널에 전송되는 메시지를 트리거합니다. |

## <a name="azure-functions"></a>Azure 기능

이벤트에 대한 serverless 응답으로 Azure Functions를 사용합니다.

|제목  |설명  |
|---------|---------|
| [Azure Functions의 Event Grid 트리거](../azure-functions/functions-bindings-event-grid.md) | Functions에서 Event Grid 트리거 사용 개요. |
| [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](resize-images-on-storage-blob-upload-event.md) | 사용자가 웹앱을 통해 이미지를 저장소 계정에 업로드합니다. 저장소 BLOB이 만들어지면 Event Grid는 이벤트를 함수 앱에 보내고, 그곳에서 업로드된 이미지를 크기 조정합니다. |
| [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md) | Event Hubs가 캡처 파일을 만들 때 Event Grid는 함수 앱에 이벤트를 보냅니다. 앱은 캡처 파일을 검색하고 데이터를 데이터 웨어하우스에 마이그레이션합니다. |
| [Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |

## <a name="hybrid-connections"></a>하이브리드 연결

Azure Relay 하이브리드 연결을 사용하여 엔터프라이즈 네트워크 내에 있으며 공개적으로 액세스할 수 있는 엔드포인트가 없는 응용 프로그램에 이벤트를 보냅니다.

|제목  |설명  |
|---------|---------|
| [이벤트 하이브리드 연결 보내기](custom-event-to-hybrid-connection.md) | 수신기 응용 프로그램에서 프로세싱에 대한 기존 하이브리드 연결에 사용자 지정 이벤트를 보냅니다. |

## <a name="logic-apps"></a>Logic Apps

Logic Apps를 사용하여 이벤트에 응답하는 비즈니스 프로세스를 자동화합니다.

|제목  |설명  |
|---------|---------|
| [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md) | 논리 앱은 가상 머신의 변경 내용을 모니터링하고 이러한 변경에 대한 이메일을 보냅니다. |
| [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](publish-iot-hub-events-to-logic-apps.md) | 논리 앱은 사용자의 IoT Hub에 장치가 추가될 때마다 알림 이메일을 보냅니다. |
| [Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |

## <a name="queue-storage"></a>Queue Storage

Queue Storage를 사용하여 끌어와야 할 이벤트를 수신합니다.

|제목  |설명  |
|---------|---------|
| [Azure CLI 및 Event Grid를 사용하여 Azure Queue 저장소로 사용자 지정 이벤트 라우팅](custom-event-to-queue-storage.md) | 큐 저장소에 사용자 지정 이벤트를 보내는 방법을 설명합니다. |

## <a name="webhooks"></a>웹후크

이벤트에 응답하는 사용자 지정 가능한 엔드포인트에는 웹후크를 사용합니다.

|제목  |설명  |
|---------|---------|
| [HTTP 엔드포인트에서 이벤트 수신](receive-events.md) | 이벤트 구독으로부터 이벤트를 수신하기 위해 HTTP 엔드포인트의 유효성을 검사하고, 이벤트를 수신하고 직렬화를 해제하는 방법을 설명합니다. |

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
