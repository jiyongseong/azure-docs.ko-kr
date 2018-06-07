---
title: "Azure Functions를 사용한 Azure Logic Apps에 대한 사용자 지정 코드 | Microsoft Docs"
description: "Azure Functions를 사용하여 Azure Logic Apps에 대한 사용자 지정 코드 만들기 및 실행"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Azure Functions를 통해 Logic Apps에 대한 사용자 지정 코드 추가 및 실행

Logic Apps에서 C# 또는 node.js의 사용자 지정 코드 조각을 실행하려면 Azure Functions를 통해 사용자 지정 함수를 만들 수 있습니다. 
[Azure Functions](../azure-functions/functions-overview.md)는 Microsoft Azure에서 서버 없는 계산을 제공하고 다음과 같은 태스크를 수행하는 데 유용합니다.

* 고급 서식 지정 또는 논리 앱에서 필드 계산
* 워크플로에서 계산을 수행합니다.
* C# 또는 node.js에서 지원되는 함수를 사용하여 논리 앱 기능 확장

## <a name="create-custom-functions-for-your-logic-apps"></a>Logic Apps에 대한 사용자 지정 함수 만들기

**일반 WebHook - 노드** 또는 **일반 WebHook - C#** 템플릿의 Azure Functions 포털에서 함수를 만드는 것이 좋습니다. 결과적으로 논리 앱에서 `application/json`을 수락하는 템플릿이 자동으로 채워집니다. 이러한 템플릿에서 만든 함수는 **내 지역의 Azure Functions** 아래의 Logic App Designer에서 자동으로 감지되고 표시됩니다.

Azure Portal의 함수에 대한 **통합** 창에서 템플릿은 **모드**를 **WebHook**로 설정하고 **WebHook 유형**을 **일반 JSON**로 설정하여 표시해야 합니다. 

WebHook 함수는 요청을 수락하고 `data` 변수를 통해 메서드를 제공합니다. `data.function-name`와 같은 점 표기법을 사용하여 페이로드 속성에 액세스할 수 있습니다. 예를 들어 DateTime 값을 날짜 문자열로 변환하는 간단한 JavaScript 함수는 다음 예와 같습니다.

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>논리 앱에서 Azure Functions 호출

구독에서 컨테이너를 나열하고 호출하려는 함수를 선택하려면 Logic App Designer에서 **작업** 메뉴를 클릭하고 **내 지역의 Azure Functions**를 선택합니다.

함수를 선택하면 입력 페이로드 개체를 지정하라는 메시지가 표시됩니다. 이 개체는 논리 앱이 함수로 보내는 메시지로, JSON 개체여야 합니다. 예를 들어, salesforce 트리거에서 **마지막으로 수정한** 날짜를 전달하려는 경우 함수 페이로드는 다음 예제와 같을 수 있습니다.

![마지막으로 수정한 날짜][1]

## <a name="trigger-logic-apps-from-a-function"></a>함수에서 논리 앱 트리거

함수 내에서 논리 앱을 트리거할 수 있습니다. [호출 가능 끝점인 논리 앱](logic-apps-http-endpoint.md)을 참조하세요. 수동 트리거가 있는 논리 앱을 만든 다음 함수 내에서 논리 앱으로 보낼 페이로드를 사용하여 수동 트리거 URL로의 HTTP POST를 생성합니다.

### <a name="create-a-function-from-logic-app-designer"></a>Logic App Designer에서 함수 만들기

디자이너에서 node.js 웹후크 함수를 만들 수도 있습니다. 먼저 **내 영역의 Azure Functions** 를 선택하고 함수에 대한 컨테이너를 선택합니다. 컨테이너가 없으면 [Azure Functions 포털](https://functions.azure.com/signin)에서 만들어야 합니다. 그런 다음 **새로 만들기**를 선택합니다.  

계산하려는 데이터를 기준으로 템플릿을 생성하려면 함수에 전달할 컨텍스트 개체를 지정합니다. 이 개체는 JSON 개체여야 합니다. 예를 들어, FTP 작업의 파일 콘텐츠를 전달하는 경우 상황에 맞는 페이로드는 다음 예제와 같습니다.

![상황에 맞는 페이로드][2]

> [!NOTE]
> 이 개체는 문자열로 캐스팅되지 않았으므로 콘텐츠가 JSON 페이로드에 직접 추가됩니다. 하지만 개체가 JSON 토큰이 아니면(즉, 문자열 또는 JSON 개체/배열) 오류가 발생합니다. 개체를 문자열로 캐스팅하려면 이 문서의 첫 번째 그림에 나와 있는 것처럼 따옴표를 추가합니다.
> 

그러면 디자이너는 인라인으로 만들 수 있는 함수 템플릿을 생성합니다. 변수는 함수에 전달하려는 컨텍스트를 기준으로 미리 생성됩니다.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
