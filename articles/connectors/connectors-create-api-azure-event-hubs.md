---
title: Azure Logic Apps용 Azure Event Hubs로 이벤트 모니터링 설정 | Microsoft Docs
description: Azure Event Hubs를 사용하여 논리 앱에 대한 이벤트를 수신 및 전송하는 데이터 스트림 모니터링
services: logic-apps
keywords: 데이터 스트림, 이벤트 모니터, 이벤트 허브
author: ecfan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 8de56cd64f38791fb27d9bcce1e16641fb162c2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Event Hubs 커넥터로 이벤트 모니터, 수신 및 전송

논리 앱이 이벤트를 감지하고 이벤트를 수신 및 전송할 수 있도록 이벤트 모니터를 설정하려면 논리 앱에서 [Azure Event Hub](https://azure.microsoft.com/services/event-hubs)에 연결합니다. [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) 및 [Logic Apps 커넥터에 대한 가격 책정 방식](../logic-apps/logic-apps-pricing.md)에 대해 자세히 알아봅니다.

## <a name="prerequisites"></a>필수 조건

Event Hubs 커넥터를 사용하려면 먼저 다음과 같은 항목이 있어야 합니다.

* [Azure Event Hubs 네임스페이스 및 Event Hub](../event-hubs/event-hubs-create.md)
* [논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Azure Event Hubs에 대한 연결

논리 앱에서 서비스에 액세스하려면 논리 앱과 서비스 사이에 [*연결*](./connectors-overview.md)을 만들어야 합니다(아직 없는 경우). 이렇게 연결되면 논리 앱에서 데이터에 액세스할 수 있는 권한이 부여됩니다. Event Hub에 액세스하는 논리 앱의 경우 Event Hubs 네임스페이스에 대한 관리 권한과 연결 문자열이 있어야 합니다.

1.  [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 

2.  특정 Event Hub가 아닌 Event Hubs *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

    ![Event Hub 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 연결 정보를 나중에 수동으로 입력하려는 경우 Event Hubs 네임스페이스에 대한 연결 문자열을 가져옵니다. **RootManageSharedAccessKey**를 선택합니다. 기본 키 연결 문자열 옆에 있는 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

    ![Event Hubs 네임스페이스 연결 문자열 복사](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 연결 문자열이 Event Hubs 네임스페이스 또는 특정 Event Hub와 연결되어 있는지 확인하려면 `EntityPath` 매개 변수에 대한 연결 문자열을 확인합니다. 이 매개 변수를 찾으면 연결 문자열은 특정 Event Hub “엔터티”에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Event Hub에서 새 이벤트를 수신할 때 워크플로 트리거

[*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 새 이벤트가 Event Hub에 전송되었을 때 워크플로를 시작하려면 이 이벤트를 감지하는 트리거를 추가하는 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에서 기존 논리 앱으로 이동하거나 비어 있는 논리 앱을 만듭니다.

2. 논리 앱 디자이너에서 필터로 검색 상자에 “event hubs”를 입력합니다. 이 트리거 선택: **이벤트가 Event Hub에서 사용 가능한 경우**

   ![Event Hub에서 새 이벤트를 수신하는 경우에 대한 트리거 선택](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Event Hubs 네임스페이스에 대한 연결이 아직 없는 경우 이 연결을 생성할지 묻는 메시지가 표시됩니다. 연결에 이름을 지정하고 사용할 Event Hubs 네임스페이스를 선택합니다.

      ![Event Hub 연결 만들기](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      또는 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력**을 선택합니다. 
      [연결 문자열을 찾는 방법](#permissions-connection-string)을 알아보세요.

   2. 이제 사용할 Event Hubs 정책을 선택하고 **만들기**를 선택합니다.

      ![Event Hub 연결 만들기, 2부](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 모니터링할 Event Hub를 선택하고, Event Hub를 확인하는 시기에 대한 간격 및 빈도를 설정합니다.

    ![Event Hub 또는 소비자 그룹 지정](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)
    
    > [!NOTE]
    > 모든 Event Hub 트리거는 *긴 폴링* 트리거입니다. 즉, 이 트리거는 발생될 때 모든 이벤트를 처리한 다음, 더 많은 이벤트가 Event Hub에 나타날 때까지 30초 동안 기다립니다.
    > 30초 후에 이벤트가 수신되지 않으면 트리거 실행을 건너뜁니다. 그렇지 않으면 트리거는 Event Hub가 빈 상태가 될 때까지 이벤트를 계속 읽습니다.
    > 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.


4. 고급 트리거 옵션 중 일부를 필요에 따라 선택하려면 **고급 옵션 표시**를 선택합니다.

    ![트리거 고급 옵션](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

    | 자산 | 세부 정보 |
    | --- | --- |
    | 콘텐츠 형식  |드롭다운 목록에서 이벤트의 콘텐츠 유형을 선택합니다. 기본적으로 application/octet-stream이 선택됩니다. |
    | 콘텐츠 스키마 |Event Hub에서 읽는 이벤트에 대한 콘텐츠 스키마를 JSON에 입력합니다. |
    | 소비자 그룹 이름 |이벤트를 읽을 Event Hub [소비자 그룹 이름](../event-hubs/event-hubs-features.md#consumer-groups)을 입력합니다. 소비자 그룹 이름을 지정하지 않으면 기본 소비자 그룹이 사용됩니다. |
    | 최소 파티션 키 |읽을 최소 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
    | 최대 파티션 키 |읽을 최대 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
    | 최대 이벤트 수 |최대 이벤트 수에 대한 값을 입력합니다. 트리거는 1과 이 속성으로 지정된 이벤트의 수 범위의 숫자를 반환합니다. |
    |||

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제, 논리 앱이 선택된 Event Hub를 검사하고 새 이벤트를 발견하는 경우 찾은 이벤트에 대한 논리 앱에서 트리거가 동작을 실행합니다.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>논리 앱에서 Event Hub로 이벤트 보내기

[*작업*](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 논리 앱 워크플로에서 수행하는 태스크입니다. 논리 앱에 트리거를 추가한 후에 해당 트리거에 의해 생성된 데이터를 사용하여 작업을 수행하는 작업을 추가할 수 있습니다. 논리 앱에서 Event Hub로 이벤트를 보내려면 다음 단계를 수행합니다.

1. 논리 앱 디자이너의 트리거에서 **새 단계** > **작업 추가**를 선택합니다.

2. 검색 상자에 필터로 “event hubs”를 입력합니다.
**Event Hubs - Send event**를 선택합니다.

   ![“Event Hubs - Send event” 선택](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. 이벤트를 보낼 수 있는 위치에 대한 Event Hub를 선택합니다. 그런 다음, 이벤트 콘텐츠 및 기타 세부 정보를 입력합니다.

   ![Event Hub 이름을 선택하고 이벤트 콘텐츠를 제공](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. 논리 앱을 저장합니다.

이제 논리 앱에서 이벤트를 보내는 작업을 설정했습니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

Swagger 파일 및 모든 제한에 의해 정의된 트리거 및 작업에 대한 자세한 정보는 [커넥터 세부 정보](/connectors/eventhubs/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 다른 커넥터](../connectors/apis-list.md)에 대해 자세히 알아봅니다.