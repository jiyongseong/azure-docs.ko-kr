---
title: 루프 - 배열 처리 또는 반복 작업 - Azure Logic Apps | Microsoft Docs
description: "\"for each\" 루프를 사용하여 배열을 처리하거나 논리 앱에서 특정 조건이 충족될 때까지 작업을 반복합니다."
services: logic-apps
keywords: for each 루프
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 4029da2c7ad59b1e61dabe0af252834746a4c5c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="loops-process-arrays-or-repeat-actions-until-a-condition-is-met"></a>루프: 배열 처리 또는 조건이 충족될 때까지 작업 반복

논리 앱에서 배열을 반복하려면 ["Foreach" 루프](#foreach-loop) 또는 [순차적 "Foreach" 루프](#sequential-foreach-loop)를 사용할 수 있습니다. 표준 "Foreach" 루프의 주기는 병렬로 실행되지만, 순차적 "Foreach" 루프의 주기는 한 번에 하나씩 실행됩니다. "Foreach" 루프가 단일 논리 앱 실행에서 처리할 수 있는 최대 배열 항목 수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

> [!TIP] 
> 배열을 받는 트리거가 있고 각 배열 항목에 대한 워크플로를 실행하려는 경우, [**SplitOn** 트리거 속성](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)을 사용하여 해당 배열을 *분리 처리(debatch)* 할 수 있습니다. 
  
조건이 충족되거나 일부 상태가 변경될 때까지 작업을 반복하려면 ["Until" 루프](#until-loop)를 사용합니다. 논리 앱에서 먼저 루프 내부의 모든 작업을 수행한 다음, 마지막 단계로 조건을 확인합니다. 조건이 충족되면 루프가 중지됩니다. 그렇지 않으면 루프가 반복됩니다. 단일 논리 앱 실행의 최대 "Until" 루프 수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" 루프

배열의 각 항목에 대한 작업을 반복하려면 논리 앱 워크플로에서 "Foreach" 루프를 사용합니다. "Foreach" 루프는 여러 작업을 포함할 수 있으며, 서로의 내부에 중첩할 수 있습니다. 기본적으로 표준 "Foreach" 루프의 주기는 병렬로 실행됩니다. "Foreach" 루프가 실행될 수 있는 최대 병렬 주기 수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요.

> [!NOTE] 
> "Foreach" 루프는 배열에서만 작동하며, 루프의 작업은 `@item()` 참조를 사용하여 배열의 각 항목을 처리합니다. 배열에 없는 데이터를 지정하면 논리 앱 워크플로가 실패합니다. 

예를 들어 이 논리 앱은 웹 사이트의 RSS 피드에서 일별 요약을 보냅니다. 앱은 찾은 각각의 새 항목에 대한 이메일을 보내는 "Foreach" 루프를 사용합니다.

1. Outlook.com 또는 Office 365 Outlook 계정으로 [이 샘플 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. RSS 트리거와 이메일 보내기 작업 사이에 "Foreach" 루프를 추가합니다. 

   단계 사이에 루프를 추가하려면 루프를 추가하려는 화살표 위로 포인터를 이동합니다. 
   표시되는 **더하기 기호**(**+**)를 선택한 다음, **for each 추가**를 선택합니다.

   ![단계 사이에 "Foreach" 루프 추가](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. 이제 루프를 작성합니다. **동적 콘텐츠 추가** 목록이 표시되면 **이전 단계의 출력 선택** 아래에서 RSS 트리거에서 출력된 **피드 링크** 배열을 선택합니다. 

   ![동적 콘텐츠 목록에서 선택](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 이전 단계의 *배열 출력만* 선택할 수 있습니다.

   선택한 배열이 다음과 같이 표시됩니다.

   ![배열 선택](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 각 배열 항목에 대한 작업을 수행하려면 **이메일 보내기** 작업을 **For each** 루프로 끕니다. 

   논리 앱은 다음 예와 같을 수 있습니다.

   !["Foreach" 루프에 단계 추가](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. 논리 앱을 저장합니다. 논리 앱을 수동으로 테스트하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" 루프 정의(JSON)

논리 앱에 대한 코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 `Foreach` 루프를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>순차적 "Foreach" 루프

기본적으로 "Foreach" 루프의 각 주기는 각 배열 항목에 대해 병렬로 실행됩니다. 각 주기를 순차적으로 실행하려면 "Foreach" 루프에 **순차** 옵션을 설정합니다.

1. 루프의 오른쪽 위 모서리에서 **줄임표**(**...**) > **설정**을 선택합니다.

   !["Foreach" 루프에서 "..." > "설정"을 차례로 선택합니다.](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. **순차** 설정을 켜고 **완료**를 선택합니다.

   !["Foreach" 루프의 순차 설정 지정](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

논리 앱의 JSON 정의에서 **operationOptions** 매개 변수를 `Sequential`로 설정할 수도 있습니다. 예: 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" 루프
  
조건이 충족되거나 일부 상태가 변경될 때까지 작업을 반복하려면 논리 앱 워크플로에서 "Until" 루프를 사용합니다. "Until" 루프를 사용할 수 있는 몇 가지 일반적인 사용 사례는 다음과 같습니다.

* 원하는 응답을 받을 때까지 엔드포인트를 호출합니다.
* 데이터베이스에 레코드를 만들고, 해당 레코드의 특정 필드가 승인될 때까지 기다리고, 처리를 계속합니다. 

예를 들어 이 논리 앱은 매일 오전 8시에 변수의 값이 10이 될 때까지 변수를 증가시킵니다. 그런 다음, 논리 앱에서 현재 값을 확인하는 이메일을 보냅니다. 이 예에서는 Office 365 Outlook을 사용하지만, Logic Apps에서 지원하는 모든 이메일 공급자를 사용할 수 있습니다([여기에 있는 커넥터 목록 검토](https://docs.microsoft.com/connectors/)). 다른 이메일 계정을 사용하는 경우 전체 단계는 동일하지만 UI가 약간 다를 수 있습니다. 

1. 빈 논리 앱을 만듭니다. 논리 앱 디자이너에서 "되풀이"를 검색하고, **일정 - 되풀이** 트리거를 선택합니다. 

   !["일정 - 되풀이" 트리거 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. 간격, 빈도 및 하루 중의 시간을 설정하여 트리거가 실행되는 시기를 지정합니다. 시간을 설정하려면 **고급 옵션 표시**를 선택합니다.

   !["일정 - 되풀이" 트리거 추가](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | 자산 | 값 |
   | -------- | ----- |
   | **간격** | 1 | 
   | **Frequency(빈도)** | 일 |
   | **시간 선택** | 8 |
   ||| 

3. 트리거 아래에서 **새 단계** > **작업 추가**를 선택합니다. "변수"를 검색한 다음, **변수 - 변수 초기화** 작업을 선택합니다.

   !["변수 - 변수 초기화" 작업 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. 다음 값을 사용하여 변수를 설정합니다.

   ![변수 속성 설정](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | 자산 | 값 | 설명 |
   | -------- | ----- | ----------- |
   | **Name** | 제한 | 변수의 이름 | 
   | **형식** | 정수  | 변수의 데이터 형식 | 
   | **값** | 0 | 변수의 시작 값 | 
   |||| 

5. **변수 초기화** 작업 아래에서 **새 단계** > **자세히**를 차례로 선택합니다. **do until 추가** 루프를 선택합니다.

   !["do until" 루프 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. **Limit** 변수 및 **같음** 연산자를 선택하여 루프의 종료 조건을 작성합니다. 비교 값으로 **10**을 입력합니다.

   ![루프를 중지하기 위한 종료 조건 작성](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. 루프 내부에서 **작업 추가**를 선택합니다. "변수"를 검색한 다음, **변수 - 변수 증가** 작업을 선택합니다.

   ![변수를 증가시키기 위한 작업 추가](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. **이름**에 대해 **Limit** 변수를 선택합니다. **값**에 대해 "1"을 입력합니다. 

   !["Limit"를 1씩 증가](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. 루프 아래이지만 루프 외부에 이메일을 보내는 작업을 추가합니다. 메시지가 표시되면 이메일 계정에 로그인합니다.

   ![이메일을 보내는 작업 추가](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. 이메일의 속성을 설정합니다. **Limit** 변수를 제목에 추가합니다. 이렇게 하면 변수의 현재 값이 지정된 조건을 충족하는지 확인할 수 있습니다. 예를 들어 다음과 같습니다.

    ![이메일 속성 설정](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | 자산 | 값 | 설명 |
    | -------- | ----- | ----------- | 
    | **To** | *<email-address@domain>* | 받는 사람의 이메일 주소입니다. 테스트를 위해 자신의 이메일 주소를 사용합니다. | 
    | **제목** | "제한"에 대한 현재 값은 **Limit**입니다. | 이메일 제목을 지정합니다. 이 예에서는 **Limit** 변수를 포함해야 합니다. | 
    | **본문** | <*email-content*> | 보내려는 이메일 메시지의 내용을 지정합니다. 이 예에서는 텍스트를 원하는 대로 입력합니다. | 
    |||| 

11. 논리 앱을 저장합니다. 논리 앱을 수동으로 테스트하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

    논리 앱이 실행되기 시작하면 지정한 내용이 포함된 이메일을 받습니다.

    ![받은 이메일](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>무한 루프 방지

"Until" 루프에는 다음 조건 중 하나가 발생하는 경우 실행을 중지하는 기본 제한이 있습니다.

| 자산 | 기본값 | 설명 | 
| -------- | ------------- | ----------- | 
| **개수** | 60 | 루프가 종료되기 전에 실행되는 최대 루프 수입니다. 기본값은 60회 주기입니다. | 
| **시간 제한** | PT1H | 루프가 종료되기 전에 루프를 실행하는 최대 시간입니다. 기본값은 1시간이며 ISO 8601 형식으로 지정됩니다. <p>시간 제한 값은 각 루프 주기에 대해 평가됩니다. 루프의 모든 작업이 시간 제한보다 더 오래 걸리면 현재 주기가 중지되지 않지만, 제한 조건이 충족되지 않으므로 다음 주기가 시작되지 않습니다. | 
|||| 

이러한 기본 제한을 변경하려면 루프 작업 셰이프에서 **고급 옵션 표시**를 선택합니다.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" 정의(JSON)

논리 앱에 대한 코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 `Until` 루프를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

또 다른 예로, 이 "Until" 루프는 리소스를 만드는 HTTP 엔드포인트를 호출하고, "완료" 상태의 HTTP 응답 본문이 반환되면 중지합니다. 또한 무한 루프를 방지하기 위해 다음 조건 중 하나라도 발생하면 루프가 중지됩니다.

* 루프가 `count` 특성에 지정한 대로 10회 실행되었습니다. 기본값은 60회입니다. 
* 루프가 ISO 8601 형식의 `timeout` 특성에 지정한 대로 2시간 동안 실행되려고 했습니다. 기본값은 1시간입니다.
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
