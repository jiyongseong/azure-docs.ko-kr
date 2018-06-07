---
title: "Switch 문 - 특정 값에 따라 단계 실행 - Azure Logic Apps | Microsoft Docs"
description: "논리 앱에서 개체, 식 또는 토큰의 값에 따라 다른 단계를 실행합니다."
services: logic-apps
keywords: "Switch 문"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Switch 문: 논리 앱에서 특정 값에 따라 다른 단계 실행

개체, 식 또는 토큰의 값에 따라 다른 단계를 수행하려면 *switch* 문을 사용합니다. 이 구조는 개체, 식 또는 토큰을 평가하고 결과와 일치하는 케이스를 선택하며 해당 케이스에 대해서만 단계를 실행합니다. switch 문이 실행되는 경우 하나의 케이스만 결과와 일치해야 합니다.

예를 들어 이메일에서 선택한 옵션에 따라 다른 단계를 수행하는 논리 앱이 필요하다고 가정합니다. 이 예에서 논리 앱은 웹 사이트의 RSS 피드에서 새 콘텐츠를 확인합니다. RSS 피드에 새 항목이 나타나면 논리 앱에서 승인자에게 이메일을 보냅니다. 승인자가 "승인" 또는 "거부"를 선택하는지 여부에 따라 논리 앱에서 서로 다른 단계를 수행합니다.

> [!TIP]
> 모든 프로그래밍 언어와 마찬가지로 Switch 문도 같음 연산자만을 지원합니다. 다른 관계 연산자(예: "보다 큼")가 필요하면 [조건문](#conditions)을 사용합니다.
> 결정적 실행 동작을 보장하려면 케이스에 동적 토큰 또는 식 대신 고유하고 정적인 값이 있어야 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 이 문서의 예를 수행하려면 Outlook.com 또는 Office 365 Outlook 계정으로 [이 샘플 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

  1. 이메일을 보내는 작업을 추가하는 경우 **승인 이메일 보내기**를 대신 선택합니다.

     !["승인 이메일 보내기" 선택](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. 승인 이메일을 받는 사람의 이메일 주소와 같은 필수 필드를 입력합니다. 
  **사용자 옵션** 아래에서 "승인, 거부"를 입력합니다.

     ![이메일 세부 정보 입력](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Switch 문 추가

1. 샘플 워크플로의 끝에서 **+ 새 단계** > **... 추가 정보** > **Switch 사례 추가**를 선택합니다. 

   ![Switch 문 추가](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   케이스와 기본 케이스가 하나씩 있는 switch 문이 표시됩니다. 
   switch 문에는 적어도 하나씩의 케이스와 기본 케이스가 필요합니다. 

   단계 사이에 switch 문을 추가하려면 switch 문을 추가하려는 화살표 위로 포인터를 이동합니다. 
   표시되는 **더하기 기호**(**+**)를 선택한 다음, **Switch Case 추가**를 선택합니다.

4. **켜기** 상자에서 해당 출력에서 수행할 작업을 결정하는 **SelectedOption** 필드를 선택합니다. 
   
   표시되는 **동적 콘텐츠 추가** 목록에서 필드를 선택할 수 있습니다.

5. 승인자가 `Approve` 또는 `Reject`를 선택하는 케이스를 처리하려면 **케이스**와 **기본** 사이에 다른 케이스를 추가합니다. 
   
6. 해당 케이스에 다음 작업을 추가합니다.

   | 케이스 # | **SelectedOption** | 조치 |
   |:------ |:-------------------|:------ |
   | 케이스 1 | **승인** | 승인자가 **승인**을 선택한 경우에만 RSS 항목에 대한 세부 정보를 보내는 Outlook **이메일 보내기** 작업을 추가합니다. |
   | 케이스 2 | **거부** | 다른 승인자에게 RSS 항목이 거부되었음을 알리는 Outlook **이메일 보내기** 작업을 추가합니다. |
   | 기본값 | \<없음\> | 필요한 작업이 없습니다. 이 예에서는 **SelectedOption**에 두 가지 옵션만 있으므로 **기본** 케이스는 비어 있습니다. |
   |         |          |

   ![Switch 문](./media/logic-apps-control-flow-switch-statement/switch.png)

7. 논리 앱을 저장합니다. 

   이 예를 수동으로 테스트하려면 논리 앱에서 새 RSS 항목을 찾아서 승인 이메일을 보낼 때까지 **실행**을 선택합니다. 
   **승인**을 선택하여 결과를 관찰합니다.

## <a name="json-definition"></a>JSON 정의

이제 switch 문을 사용하여 논리 앱을 만들었으므로 switch 문 뒤에 있는 상위 수준의 코드 정의를 살펴보겠습니다.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| 레이블              | 설명 |
| :----------------- | :---------- |
| `"Switch"`         | switch 문의 이름이며, 이 이름은 가독성을 높이기 위해 바꿀 수 있습니다. |
| `"type": "Switch"` | 작업이 switch 문임을 지정합니다. |
| `"expression"`     | 이 예에서는 각 케이스에 대해 평가되는 승인자의 선택된 옵션을 나중에 정의에서 선언한 대로 지정합니다 |
| `"cases"` | 임의 개수의 케이스를 정의합니다. 각 케이스에 대해 `"Case_*"`는 해당 케이스의 기본 이름이며, 이 이름은 가독성을 높이기 위해 바꿀 수 있습니다. |
| `"case"` | 케이스의 값을 지정합니다. 이 값은 switch 문에서 비교를 위해 사용하는 상수 및 고유 값이어야 합니다. switch 식 결과와 일치하는 케이스가 없으면 `"default"` 섹션의 작업이 실행됩니다.
|           |         |

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 또는 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)