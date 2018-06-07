---
title: Azure Logic Apps에 대한 Azure Service Bus 메시징 설정 | Microsoft Docs
description: Azure Service Bus를 사용한 논리 앱을 통해 메시지 전송 및 수신
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: d5a4760e1e0f38fd81fd779786985f5753d77eab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Azure Service Bus 커넥터를 통해 메시지 전송 및 수신

논리 앱을 통해 메시지를 보내고 받으려면 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)에 연결합니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 동작을 수행할 수 있습니다. [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 및 [Logic Apps 트리거에 대한 가격 책정 방식](../logic-apps/logic-apps-pricing.md)에 대해 자세히 알아봅니다.

## <a name="prerequisites"></a>필수 조건

Service Bus 커넥터를 사용하려면 먼저 이러한 항목이 서로 볼 수 있도록 동일한 Azure 구독에 있어야 합니다.

* [Service Bus 네임스페이스 및 큐와 같은 메시징 엔터티](../service-bus-messaging/service-bus-create-namespace-portal.md)
* [논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Azure Service Bus에 연결

논리 앱에서 서비스에 액세스하려면 논리 앱과 서비스 사이에 [*연결*](./connectors-overview.md)을 만들어야 합니다(아직 없는 경우). 이렇게 연결되면 논리 앱에서 데이터에 액세스할 수 있는 권한이 부여됩니다. 논리 앱이 Service Bus 계정에 액세스하도록 사용 권한을 확인합니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 

2. 특정 “메시징 엔터티”가 아닌 Service Bus *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![Service Bus 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. 연결 정보를 나중에 수동으로 입력하려는 경우 Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. **RootManageSharedAccessKey**를 선택합니다. 기본 키 연결 문자열 옆에 있는 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

   ![Service Bus 네임스페이스 연결 문자열 복사](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 특정 엔터티와 연결되어 있는지 확인하려면 `EntityPath` 매개 변수에 대한 연결 문자열을 확인하세요. 이 매개 변수를 찾으면 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Service Bus가 새 메시지를 받을 때 워크플로 트리거

[*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 새 메시지가 Service Bus에 전송되었을 때 워크플로를 시작하려면 이러한 메시지를 감지하는 트리거를 추가하는 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에서 기존 논리 앱으로 이동하거나 비어 있는 논리 앱을 만듭니다.

2. 논리 앱 디자이너에서 필터로 검색 상자에 “service bus”를 입력합니다. **Service Bus** 커넥터를 선택합니다. 

   ![Service Bus 커넥터 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. 사용할 트리거를 선택합니다. 예를 들어 새 항목이 Service Bus 큐로 보내질 때 논리 앱을 실행하려면 **Service Bus - 큐에 메시지가 수신될 때(자동 완성)** 트리거를 선택합니다.

   ![Service Bus 트리거 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > *Service Bus - 큐에 하나 이상의 메시지가 도착할 때(자동 완성)* 트리거와 같은 일부 트리거는 1 또는 메시지의 수를 반환합니다.
   > 이러한 트리거가 발생되면 1과 트리거의 **최대 메시지 수** 속성으로 지정된 메시지 수 사이의 값을 반환합니다.

   1. Service Bus 네임스페이스에 대한 연결이 아직 없는 경우 이 연결을 지금 생성할지 묻는 메시지가 표시됩니다. 연결에 이름을 지정하고 사용할 Service Bus 네임스페이스를 선택합니다.

      ![Service Bus 연결 만들기](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      또는 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력**을 선택합니다. 
      [연결 문자열을 찾는 방법](#permissions-connection-string)을 알아보세요.
      

   2. 이제 사용할 Service Bus 정책을 선택하고 **만들기**를 선택합니다.

      ![Service Bus 연결 만들기, 2부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. 사용할 Service Bus를 선택하고, 큐를 확인하는 시기에 대한 간격 및 빈도를 설정합니다.

   ![Service Bus 큐를 선택, 폴링 간격 설정](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > 모든 Service Bus 트리거는 **긴 폴링** 트리거입니다. 즉, 이 트리거는 발생될 때 모든 메시지를 처리한 다음, 더 많은 메시지가 큐 또는 토픽 구독에 나타날 때까지 30초 동안 기다립니다.
   > 30초 후에 메시지가 수신되지 않으면 트리거 실행을 건너뜁니다. 그렇지 않으면 큐 또는 토픽 구독이 빈 상태가 될 때까지 트리거는 메시지를 계속 읽습니다.
   > 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제, 논리 앱이 선택된 큐를 검사하고 새 메시지를 발견하는 경우 찾은 메시지에 대한 논리 앱에서 트리거가 동작을 실행합니다.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>논리 앱에서 Service Bus에 메시지 보내기

[*작업*](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 논리 앱 워크플로에서 수행하는 태스크입니다. 논리 앱에 트리거를 추가한 후에 해당 트리거에 의해 생성된 데이터를 사용하여 작업을 수행하는 작업을 추가할 수 있습니다. 논리 앱에서 Service Bus 메시징 엔터티로 메시지를 보내려면 다음 단계를 수행합니다.

1. 논리 앱 디자이너의 트리거에서 **새 단계** > **작업 추가**를 선택합니다.

2. 검색 상자에서 필터로 “service bus”를 입력합니다. **Service Bus** 커넥터를 선택합니다.

   ![Service Bus 커넥터 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. **Service Bus - 메시지 보내기** 작업을 선택합니다.

   ![“Service Bus - 메시지 보내기” 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. 메시지를 보낼 수 있는 위치에 대한 큐 또는 항목 이름인 메시징 엔터티를 선택합니다. 그런 다음, 메시지 콘텐츠 및 기타 세부 정보를 입력합니다.

   ![메시징 엔터티를 선택하고 메시지 세부 정보를 입력](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. 논리 앱을 저장합니다. 

이제 논리 앱에서 메시지를 보내는 작업을 설정했습니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

Swagger 파일 및 모든 제한에 의해 정의된 트리거 및 작업에 대한 자세한 정보는 [커넥터 세부 정보](/connectors/servicebus/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 다른 커넥터](../connectors/apis-list.md)에 대해 자세히 알아봅니다.