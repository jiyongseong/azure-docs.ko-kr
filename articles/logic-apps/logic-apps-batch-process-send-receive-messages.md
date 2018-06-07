---
title: "그룹 또는 컬렉션으로 메시지를 Batch 처리 - Azure Logic Apps | Microsoft Docs"
description: "논리 앱에서의 일괄 처리에 대한 메시지 보내기 및 받기"
keywords: "일괄 처리"
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: de519084a4f172ad984c78727123835eeb9deaef
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>논리 앱의 메시지 보내기, 받기 및 일괄 처리

그룹에서 함께 메시지를 처리하려면 데이터 항목 또는 메시지를 *일괄 처리*로 보내 해당 항목을 일괄 처리로 처리할 수 있습니다. 이 방법은 데이터 항목을 특정 방식으로 그룹화하여 함께 처리하려는 경우에 유용합니다. 

**일괄 처리** 트리거를 사용하여 항목을 일괄 처리로 수신하는 논리 앱을 만들 수 있습니다. 그런 다음 **Batch** 작업을 사용하여 항목을 일괄 처리로 보내는 논리 앱을 만들 수 있습니다.

이 토픽에서는 이러한 작업을 수행하여 일괄 처리 솔루션을 빌드할 수는 방법을 보여 줍니다. 

* [항목을 일괄 처리로 수신하고 수집하는 논리 앱을 만듭니다](#batch-receiver). 이 “일괄 처리 수신자” 논리 앱은 수신자 논리 앱을 해제하고 항목을 처리하기 전에 충족해야 하는 일괄 처리 이름 및 릴리스 조건을 지정합니다. 

* [항목을 일괄 처리로 보내는 논리 앱을 만듭니다](#batch-sender). 이 “일괄 처리 발신자” 논리 앱은 기존 일괄 처리 수신자 논리 앱이어야 하는 항목을 보낼 위치를 지정합니다. 또한 사용자 지정 번호와 같이 고유한 키를 지정하여 대상 일괄 처리를 해당 키에 따라 하위 집합으로 “분할”하거나 나눌 수 있습니다. 이런 방식으로 해당 키가 있는 모든 항목이 함께 수집되어 처리됩니다. 

## <a name="requirements"></a>요구 사항

이 예제를 수행하려면 다음과 같은 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 그렇지 않으면 [종량제 구독에 등록](https://azure.microsoft.com/pricing/purchase-options/)할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 

* [Azure Logic Apps에서 지원하는 전자 메일 공급자](../connectors/apis-list.md)를 사용하는 메일 계정

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>메시지를 일괄 처리로 수신하는 논리 앱 만들기

메시지를 일괄 처리로 보내려면 **Batch** 트리거를 사용하여 “일괄 처리 수신자” 논리 앱을 먼저 만들어야 합니다. 이런 방식으로 발신자 논리 앱을 만들 때 이 수신자 논리 앱을 선택할 수 있습니다. 수신자의 경우 일괄 처리 이름, 릴리스 조건 및 다른 설정을 지정합니다. 

수신자 논리 앱은 발신자에 대해 알 필요가 없는 반면, 발신자 논리 앱은 항목을 보내는 곳을 알아야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 “BatchReceiver”라는 이름을 가진 논리 앱을 만듭니다. 

2. Logic Apps 디자이너에서 논리 앱 워크플로를 시작하는 **Batch** 트리거를 추가합니다. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. **Batch – Batch 메시지** 트리거를 선택합니다.

   ![Batch 트리거 추가](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. 일괄 처리에 사용할 이름을 입력하고, 다음 예와 같이 일괄 처리를 릴리스하기 위한 조건을 지정합니다.

   * **일괄 처리 이름**: 일괄 처리를 식별하는 데 사용되는 이름으로, 이 예제에서는 “TestBatch”입니다.
   * **릴리스 기준**: 일괄 처리 릴리스 기준으로, 메시지 수, 일정 또는 둘 다를 기준으로 할 수 있습니다.
   
     ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **메시지 수**: 처리를 릴리스하기 전 일괄 처리로 저장하는 메시지 수입니다. 이 예제에서는 “5”입니다.

     ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **일정**: 처리에 대한 일괄 처리 릴리스 일정입니다. 이 예제에서는 "5분 간격"입니다.

     ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. 일괄 처리 트리거가 발생할 때 전자 메일을 보내는 다른 작업을 추가합니다. 일괄 처리에 항목이 5개가 될 때마다 또는 5분이 경과할 때마다 논리 앱에서 전자 메일을 보냅니다.

   1. 일괄 처리 트리거에서 **+새 단계** > **작업 추가**를 선택합니다.

   2. 검색 상자에서 필터로 “전자 메일”을 입력합니다.
   전자 메일 공급자에 따라 전자 메일 커넥터를 선택합니다.
   
      예를 들어 회사 또는 학교 계정이 있는 경우 Office 365 Outlook 커넥터를 선택합니다. 
      Gmail 계정이 있는 경우 Gmail 커넥터를 선택합니다.

   3. 커넥터에 대해 **{*메일 공급자*} - 메일 보내기** 작업을 선택합니다.

      예: 

      ![전자 메일 공급자에 대한 “전자 메일 보내기” 작업을 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. 이전에 전자 메일 공급자에 대한 연결을 만들지 않은 경우 메시지가 표시되면 인증을 위해 전자 메일 자격 증명을 제공합니다. [전자 메일 자격 증명 인증](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 대해 자세히 알아보세요.

6. 방금 추가한 작업에 대한 속성을 설정합니다.

   * **받는 사람** 상자에 받는 사람의 이메일 주소를 입력합니다. 
   자신의 이메일 주소를 사용하여 테스트할 수 있습니다.

   * **제목** 상자에 **동적 콘텐츠** 목록이 표시되면 **파티션 이름** 필드를 선택합니다.

     ![“동적 콘텐츠” 목록에서 “파티션 이름”을 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     이후 섹션에서 메시지를 보낼 수 있는 논리적 집합으로 대상 일괄 처리를 분할하는 고유한 파티션 키를 지정할 수 있습니다. 
     각 집합에는 발신자 논리 앱에서 생성된 고유 번호가 있습니다. 
     이 기능을 사용하면 여러 하위 집합이 있는 단일 일괄 처리를 사용하고 사용자가 제공한 이름으로 각 하위 집합을 정의할 수 있습니다.

   * **본문** 상자에 **동적 콘텐츠** 목록이 표시되면 **메시지 ID** 필드를 선택합니다.

     ![“본문”에서 “메시지 ID”를 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     전자 메일 보내기 작업에 대한 입력은 배열이므로 디자이너에서 자동으로 **각 항목** 루프를 **전자 메일 보내기** 작업에 추가합니다. 
     이 루프는 일괄 처리의 각 항목에 대해 내부 작업을 수행합니다. 
     따라서 5개 항목에 설정된 일괄 처리 트리거를 사용하면 트리거가 발생할 때마다 5개의 전자 메일을 받게 됩니다.

7.  이제 일괄 처리 수신기 논리 앱을 만들었으므로 논리 앱을 저장합니다.

    ![논리 앱 저장](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 파티션 한도는 5,000개 메시지 또는 80MB입니다. 두 조건 중 하나가 충족될 경우 사용자 정의 조건에 맞지 않더라도 일괄 처리가 해제될 수 있습니다.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>일괄 처리로 메시지를 보내는 논리 앱 만들기

이제는 수신기 논리 앱에서 정의되는 일괄 처리로 항목을 보내는 논리 앱을 하나 이상 만듭니다. 발신자의 경우 수신자 논리 앱 및 일괄 처리 이름, 메시지 콘텐츠 및 다른 설정을 지정합니다. 필요에 따라 해당 키가 있는 항목을 수집하도록 일괄 처리를 하위 집합으로 나누는 고유한 파티션 키를 제공할 수 있습니다.

수신자 논리 앱은 발신자에 대해 알 필요가 없는 반면, 발신자 논리 앱은 항목을 보내는 곳을 알아야 합니다.

1. “BatchSender”라는 이름을 가진 다른 논리 앱을 만듭니다.

   1. 검색 상자에서 필터로 “되풀이”를 입력합니다. 
   **예약 - 되풀이** 트리거 선택

      ![“예약 - 되풀이” 트리거 추가](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. 1분마다 발신자 논리 앱을 실행하도록 빈도 및 간격을 설정합니다.

      ![되풀이 트리거에 대한 빈도 및 간격 설정](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. 일괄 처리로 메시지를 보내기 위한 새 단계를 추가합니다.

   1. 되풀이 트리거에서 **+새 단계** > **작업 추가**를 선택합니다.

   2. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. 

   3. **일괄 처리로 메시지 보내기 – 일괄 처리 트리거를 사용하여 Logic Apps 워크플로 선택** 작업을 선택합니다.

      ![“일괄 처리로 메시지 보내기” 선택](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. 이제 작업으로 표시되는 이전에 만든 “BatchReceiver” 논리 앱을 선택합니다.

      ![“일괄 처리 수신기” 논리 앱 선택](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > 목록에 일괄 처리 트리거가 있는 다른 논리 앱도 표시됩니다.

3. 일괄 처리 속성을 설정합니다.

   * **일괄 처리 이름**: 수신기 논리 앱에서 정의되는 일괄 처리 이름입니다. 이 예에서는 “TestBatch”이며, 런타임에 유효성을 검사합니다.

     > [!IMPORTANT]
     > 일괄 처리 이름을 변경하지 않았는지 확인합니다. 이는 수신기 논리 앱에서 지정되는 일괄 처리 이름과 일치해야 합니다.
     > 일괄 처리 이름을 변경하면 발신자 논리 앱이 실패할 수 있습니다.

   * **메시지 콘텐츠**: 보낼 메시지 콘텐츠입니다. 
   이 예제의 경우 현재 날짜 및 시간을 일괄 처리로 보내는 메시지 콘텐츠에 삽입하는 이 식을 추가합니다.

     1. **동적 콘텐츠** 목록이 표시되면 **식**을 선택합니다. 
     2. 식 **utcnow()**를 입력하고 **확인**을 선택합니다. 

        ![“메시지 콘텐츠”에서 “식”을 선택합니다. “utcnow()”을 입력합니다.](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. 이제 일괄 처리에 대한 파티션을 설정합니다. “BatchReceiver” 작업에서 **고급 옵션 표시**를 선택합니다.

   * **파티션 이름**: 대상 일괄 처리를 나누는 데 사용하는 선택적 고유 파티션 키입니다. 이 예제의 경우 1과 5 사이의 난수를 생성하는 식을 추가합니다.
   
     1. **동적 콘텐츠** 목록이 표시되면 **식**을 선택합니다.
     2. **rand(1,6)** 식을 입력합니다.

        ![대상 일괄 처리에 대한 파티션 설정](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        이 **rand** 함수는 1과 5 사이의 숫자를 생성합니다. 
        따라서 이 식이 동적으로 설정하는 번호가 지정된 5개 파티션으로 이 일괄 처리를 나눕니다.

   * **메시지 ID**: 선택적 메시지 식별자로, 비어 있는 경우 생성된 GUID입니다. 
   이 예제의 경우 빈 상태로 둡니다.

5. 논리 앱을 저장합니다. 발신자 논리 앱이 이제 다음 예제와 비슷하게 표시됩니다.

   ![발신자 논리 앱 저장](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>논리 앱 테스트

일괄 처리 솔루션을 테스트하려면 논리 앱을 몇 분 동안 실행 상태로 둡니다. 곧 동일한 파티션 키를 사용하는 5개 그룹에서 전자 메일을 받기 시작합니다.

BatchSender 논리 앱은 1분마다 실행되며, 1부터 5 사이의 난수를 생성하고, 메시지를 보내는 대상 일괄 처리에 대한 파티션 키로 이 생성된 번호를 사용합니다. 일괄 처리에 동일한 파티션 키를 사용하는 5개 항목이 있을 때마다 BatchReceiver 논리 앱이 발생하고 각 메시지에 대한 메일을 보냅니다.

> [!IMPORTANT]
> 테스트를 수행할 때 메시지를 보내지 않도록 BatchSender 논리 앱을 비활성화하여 받은 편지함이 오버로드되지 않도록 합니다.

## <a name="next-steps"></a>다음 단계

* [JSON을 사용하여 논리 앱 정의 빌드](../logic-apps/logic-apps-author-definitions.md)
* [Azure Logic Apps 및 함수를 사용하여 Visual Studio에서 서버를 사용하지 않는 앱 빌드](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [논리 앱에 대한 예외 처리 및 오류 로깅](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
