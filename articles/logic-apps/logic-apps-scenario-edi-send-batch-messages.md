---
title: "그룹 또는 컬렉션으로 EDI 메시지를 일괄 처리 - Azure Logic Apps | Microsoft Docs"
description: "논리 앱에서의 일괄 처리를 위해 EDI 메시지 보내기"
keywords: "일괄 처리, 일괄 처리 프로세스, 일괄 처리 인코딩"
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>거래 업체에 X12 메시지를 일괄 전송

B2B(Business to Business) 시나리오에서 파트너는 그룹 또는 일괄 처리로 메시지를 교환하는 경우가 많습니다. 그룹이나 일괄 처리로 거래 업체에 메시지를 보내려면 여러 항목이 포함된 일괄 처리를 만든 다음 X12 일괄 처리 작업을 사용하여 해당 항목을 일괄 처리할 수 있습니다.


X12 메시지에 대한 일괄 처리는 다른 메시지와 마찬가지로 일괄 처리 트리거와 작업을 사용합니다. 또한 X12의 경우 일괄 처리는 파트너 또는 다른 대상으로 가기 전에 X12 인코딩 단계를 거칩니다. 일괄 처리 트리거 및 작업에 대한 자세한 내용은 [메시지 일괄 처리](logic-apps-batch-process-send-receive-messages.md)를 참조하세요.

이 문서에서는 다음 작업을 수행하여 X12 메시지를 일괄 처리할 수 있는 방법을 보여줍니다.
* [항목을 수신하고 일괄 처리를 생성하는 논리 앱을 만듭니다](#receiver). 이 "수신자" 논리 앱은 다음 작업을 수행합니다.
 
   * 항목을 일괄 처리로 릴리스하기 전에 충족해야 하는 릴리스 기준 및 일괄 처리 이름을 지정합니다.

   * 지정된 X12 규약 또는 파트너 ID를 사용하여 일괄 처리의 항목을 처리하거나 인코딩합니다.

* [항목을 일괄 처리로 보내는 논리 앱을 만듭니다](#sender). 이 "발신자" 논리 앱은 기존 일괄 처리 수신자 논리 앱이어야 하는 항목을, 일괄 처리를 위해 보낼 위치를 지정합니다.


## <a name="prerequisites"></a>필수 조건

이 예제를 수행하려면 다음과 같은 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 그렇지 않으면 [종량제 구독에 등록](https://azure.microsoft.com/pricing/purchase-options/)할 수 있습니다.

* 이미 정의되고 Azure 구독과 연결된 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)

* 통합 계정에 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md). 각 파트너가 파트너의 속성에서 X12(표준 이동 통신 사업자 알파 코드) 한정자를 비즈니스 ID로 사용하는지 확인하십시오.

* 통합 계정에 이미 정의된 [X12 규약](logic-apps-enterprise-integration-x12.md)

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>X12 메시지를 수신하고 일괄 처리를 생성하는 논리 앱 만들기

메시지를 일괄 처리로 보내려면 먼저 **일괄 처리** 트리거가 포함된 "수신자" 논리 앱을 만들어야 합니다. 이런 방식으로 발신자 논리 앱을 만들 때 이 수신자 논리 앱을 선택할 수 있습니다. 수신자에 대한 일괄 처리 이름, 릴리스 기준, X12 규약 및 다른 설정을 지정합니다. 


1. [Azure Portal](https://portal.azure.com)에서 "BatchX12Messages"라는 이름으로 논리 앱을 만듭니다.

2. Logic Apps 디자이너에서 논리 앱 워크플로를 시작하는 **Batch** 트리거를 추가합니다. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. **Batch – Batch 메시지** 트리거를 선택합니다.

   ![Batch 트리거 추가](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. 일괄 처리에 사용할 이름을 입력하고, 다음 예와 같이 일괄 처리를 릴리스하기 위한 조건을 지정합니다.

   * **일괄 처리 이름**: 일괄 처리를 식별하는 데 사용되는 이름으로, 이 예제에서는 “TestBatch”입니다.

   * **릴리스 기준**: 일괄 처리 릴리스 기준으로, 메시지 수, 일정 또는 둘 다를 기준으로 할 수 있습니다.
   
     ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **메시지 수**: 처리를 릴리스하기 전 일괄 처리로 저장하는 메시지 수입니다. 이 예제에서는 “5”입니다.

     ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **일정**: 처리를 위한 일괄 처리 릴리스 일정입니다. 이 예제에서는 "10분 간격"입니다.

     ![Batch 트리거 세부 정보 제공](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. 그룹화된 메시지 또는 일괄 처리 메시지를 인코딩하는 다른 작업을 추가하고 X12 일괄 처리된 메시지를 생성합니다. 

   a. **+ 다음 단계** > **작업 추가**를 선택합니다.

   b. 검색 상자에 "X12 일괄 처리"를 필터로 입력하고 **X12 - 일괄 처리 인코딩**에 대한 작업을 선택합니다. X12 인코딩 커넥터와 마찬가지로 일괄 처리 인코딩 작업에는 여러 가지 변형이 있습니다. 둘 중 하나를 선택할 수 있습니다.

   ![X12 일괄 처리 인코딩 작업 선택](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. 방금 추가한 작업에 대한 속성을 설정합니다.

   * **X12 규약 이름** 상자의 드롭다운 목록에서 규약을 선택합니다. 목록이 비어 있으면 통합 계정에 대한 연결을 만들었는지 확인하십시오.

   * **BatchName** 상자의 동적 콘텐츠 목록에서 **일괄 처리 이름** 필드를 선택합니다.
   
   * **PartitionName** 상자의 동적 콘텐츠 목록에서 **파티션 이름** 필드를 선택합니다.

   * **Items** 상자의 동적 콘텐츠 목록에서 **일괄 처리된 항목** 필드를 선택합니다.

   ![일괄 처리 인코딩 작업 세부 정보](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. 테스트 목적으로 일괄 처리된 메시지를 [요청 Bin 서비스](https://requestbin.fullcontact.com/)로 전송하는 HTTP 작업을 추가합니다. 

   1. 검색 상자에 "HTTP"를 필터로 입력합니다. **HTTP - HTTP** 작업을 선택합니다.
    
      ![HTTP 작업 선택](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. **메서드** 목록에서 **POST**를 선택합니다. **Uri** 상자에는 요청 bin의 URI를 생성하고 해당 URI를 입력합니다. **본문** 상자에서 동적 목록이 열리면 **계약 이름으로 일괄 처리 인코딩** 섹션에서 **본문** 필드를 선택합니다. **본문**이 없는 경우 **계약 이름으로 일괄 처리 인코딩** 옆의 **자세히 보기**를 선택합니다.

      ![HTTP 작업 세부 정보 제공](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  수신자 논리 앱을 만들었으니 논리 앱을 저장합니다.

    ![논리 앱 저장](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 파티션 한도는 5,000개 메시지 또는 80MB입니다. 두 조건 중 하나가 충족될 경우 사용자 정의 조건에 맞지 않더라도 일괄 처리가 해제될 수 있습니다.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>X12 메시지를 일괄 처리로 보내는 논리 앱 만들기

이제는 수신기 논리 앱에서 정의되는 일괄 처리로 항목을 보내는 논리 앱을 하나 이상 만듭니다. 발신자의 경우 수신자 논리 앱 및 일괄 처리 이름, 메시지 콘텐츠 및 다른 설정을 지정합니다. 필요에 따라 해당 키가 있는 항목을 수집하도록 일괄 처리를 하위 집합으로 나누는 고유한 파티션 키를 제공할 수 있습니다.

수신자 논리 앱은 발신자에 대해 알 필요가 없는 반면, 발신자 논리 앱은 항목을 보낼 곳을 알아야 합니다.


1. "X12MessageSender"라는 이름으로 또 다른 논리 앱을 만듭니다. **요청/응답 - 요청** 트리거를 논리 앱에 추가합니다. 
   
   ![요청 트리거 추가](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. 일괄 처리로 메시지를 보내기 위한 새 단계를 추가합니다.

   1. **+ 다음 단계** > **작업 추가**를 선택합니다.

   2. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. 

3. **일괄 처리로 메시지 보내기 – 일괄 처리 트리거를 사용하여 논리 앱 워크플로 선택** 작업을 선택합니다.

   ![“일괄 처리로 메시지 보내기” 선택](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. 이제 작업으로 표시되는 이전에 만든 "BatchX12Messages" 논리 앱을 선택합니다.

   ![“일괄 처리 수신기” 논리 앱 선택](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > 목록에 일괄 처리 트리거가 있는 다른 논리 앱도 표시됩니다.

5. 일괄 처리 속성을 설정합니다.

   * **일괄 처리 이름**: 수신기 논리 앱에서 정의되는 일괄 처리 이름입니다. 이 예에서는 “TestBatch”이며, 런타임에 유효성을 검사합니다.

     > [!IMPORTANT]
     > 일괄 처리 이름을 변경하지 않았는지 확인합니다. 이는 수신기 논리 앱에서 지정되는 일괄 처리 이름과 일치해야 합니다.
     > 일괄 처리 이름을 변경하면 발신자 논리 앱이 실패할 수 있습니다.

   * **메시지 콘텐츠**: 일괄 처리로 보낼 메시지 콘텐츠입니다.
   
   ![일괄 처리 속성 설정](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. 논리 앱을 저장합니다. 발신자 논리 앱이 이제 다음 예제와 비슷하게 표시됩니다.

   ![발신자 논리 앱 저장](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>논리 앱 테스트

일괄 처리 솔루션을 테스트하려면 X12 메시지를 [Postman](https://www.getpostman.com/postman) 또는 유사한 도구에서 발신자 논리 앱에 게시합니다. 곧 요청 bin에 10분 간격으로 또는 5개 항목 일괄 처리로 동일한 파티션 키가 있는 X12 메시지를 받기 시작합니다.

## <a name="next-steps"></a>다음 단계

* [일괄 처리로 메시지 처리](logic-apps-batch-process-send-receive-messages.md) 
* [Azure Logic Apps 및 함수를 사용하여 Visual Studio에서 서버를 사용하지 않는 앱 빌드](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [논리 앱에 대한 예외 처리 및 오류 로깅](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
