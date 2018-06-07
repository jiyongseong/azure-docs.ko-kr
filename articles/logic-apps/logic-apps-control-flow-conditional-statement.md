---
title: "조건문 - 조건에 따라 단계 실행 - Azure Logic Apps | Microsoft Docs"
description: "조건이 충족된 후에만 논리 앱에서 단계를 실행합니다. 지정된 조건에 따라 워크플로를 실행하는 의사 결정 트리를 만듭니다."
services: logic-apps
keywords: "조건문, 의사 결정 트리"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>조건문: 논리 앱에서 조건에 따라 단계 실행

지정된 조건을 전달한 후에만 단계를 수행하려면 *조건문*을 사용합니다. 이 구조는 워크플로의 데이터를 특정 값 또는 필드와 비교합니다. 그런 다음, 데이터가 조건을 충족하는 지 여부에 따라 실행할 다른 단계를 정의할 수 있습니다. 서로의 내부에 조건을 중첩할 수 있습니다.

예를 들어 웹 사이트의 RSS 피드에 새 항목이 표시될 때 너무 많은 이메일을 보내는 논리 앱이 있다고 가정합니다. 새 항목에 특정 문자열이 포함된 경우에만 이메일을 보내는 조건문을 추가할 수 있습니다. 

> [!TIP]
> 다른 특정 값에 따라 다른 단계를 실행하려면 [*switch 문*](../logic-apps/logic-apps-control-flow-switch-statement.md)을 대신 사용합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 이 문서의 예를 수행하려면 Outlook.com 또는 Office 365 Outlook 계정으로 [이 샘플 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-a-condition"></a>조건 추가

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 논리 앱 디자이너에서 논리 앱을 엽니다.

2. 원하는 위치에 조건을 추가합니다. 

   단계 사이에 조건을 추가하려면 조건을 추가하려는 화살표 위로 포인터를 이동합니다. 표시되는 **더하기 기호**(**+**)를 선택한 다음, **조건 추가**를 선택합니다. 예: 

   ![단계 사이에 조건 추가](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   워크플로 끝 부분에 조건을 추가하려면 논리 앱의 아래쪽에서 **+ 새 단계** > **조건 추가**를 차례로 선택합니다.

3. **조건** 아래에서 조건을 만듭니다. 

   1. 왼쪽 상자에서 비교하려는 데이터 또는 필드를 지정합니다.

      **동적 콘텐츠 추가** 목록에서 논리 앱의 기존 필드를 선택할 수 있습니다.

   2. 가운데 목록에서 수행할 작업을 선택합니다. 
   3. 오른쪽 상자에서 또는 필드를 기준으로 값 지정합니다.

   예: 

   ![기본 모드에서 조건 편집](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   완성된 조건은 다음과 같습니다.

   ![완성된 조건](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > 고급 조건을 만들거나 식을 사용하려면 **고급 모드에서 편집**을 선택합니다. [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)로 정의된 식을 사용할 수 있습니다.
   > 
   > 예: 
   >
   > ![코드에서 조건 편집](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. **IF YES** 및 **IF NO** 아래에서 조건이 충족되는지 여부에 따라 수행할 단계를 추가합니다. 예: 

   ![YES 및 NO 경로가 있는 조건](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > 기존 동작을 **IF YES** 및 **IF NO** 경로로 끌 수 있습니다.

6. 논리 앱을 저장합니다.

이제 이 논리 앱에서 RSS 피드의 새 항목이 조건을 충족할 때만 메일을 보냅니다.

## <a name="json-definition"></a>JSON 정의

이제 조건문을 사용하여 논리 앱을 만들었으므로 조건문 뒤에 있는 상위 수준의 코드 정의를 살펴보겠습니다.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)