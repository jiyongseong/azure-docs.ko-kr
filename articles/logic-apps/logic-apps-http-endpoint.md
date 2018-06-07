---
title: "HTTP 끝점을 통해 워크플로 호출, 트리거 또는 중첩 - Azure Logic Apps | Microsoft 문서"
description: "Azure Logic Apps에 대해 워크플로를 호출, 트리거 또는 중첩하기 위해 HTTP 끝점 설정"
services: logic-apps
keywords: "워크플로, HTTP 끝점"
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: de4f4ee086fbf3799fcac1f1b008d9237b5e7a09
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2018
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Logic Apps의 HTTP 끝점을 통해 워크플로 호출, 트리거 또는 중첩

URL을 통해 Logic Apps를 트리거 또는 호출할 수 있도록 동기식 HTTP 끝점을 기본적으로 논리 앱에 트리거로 표시할 수 있습니다. 또한 호출 가능 끝점의 패턴을 사용하여 Logic Apps에서 워크플로를 중첩할 수도 있습니다.

HTTP 끝점을 만들려면 Logic Apps가 들어오는 요청을 받을 수 있도록 이러한 트리거를 추가할 수 있습니다.

* [요청](../connectors/connectors-native-reqres.md)

* [API 연결 웹후크](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP 웹후크](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > 이 예제에서는 **요청** 트리거를 사용하지만 나열된 어떤 HTTP 트리거도 사용할 수 있으며, 모든 원칙은 다른 트리거 유형에 동일하게 적용됩니다.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>논리 앱을 위해 HTTP 끝점 설정

HTTP 끝점을 만들려면 들어오는 요청을 받을 수 있는 트리거를 추가합니다.

1. [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 논리 앱으로 이동하고 논리 앱 디자이너를 엽니다.

2. 논리 앱이 들어오는 요청을 받을 수 있도록 하는 트리거를 추가합니다. 예를 들어, 논리 앱에 **요청** 트리거를 추가합니다.

3.  필요에 따라 **요청 본문 JSON 스키마**에서 트리거가 받을 것으로 예상하는 페이로드(데이터)에 대해 JSON 스키마를 입력할 수 있습니다.

    설계자는 워크플로를 통해 논리 앱이 트리거에서 데이터를 소비, 구문 분석 및 전달하는 데 사용할 수 있는 토큰을 생성하는 데 이 스키마를 사용합니다. 
    [JSON 스키마에서 생성된 토큰](#generated-tokens)에 관한 추가 정보.

    이 예의 경우 디자이너에 표시된 스키마를 입력합니다.

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![요청 작업 추가][1]

    > [!TIP]
    > 
    > [jsonschema.net](http://jsonschema.net/) 같은 도구에서, 또는 **샘플 페이로드를 사용하여 스키마 생성**을 선택하여 **요청** 트리거에서 샘플 JSON 페이로드에 대한 스키마를 생성할 수 있습니다. 
    > 샘플 페이로드를 입력하고 **완료**를 선택합니다.

    예를 들어 다음 샘플 페이로드는:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    다음 스키마를 생성합니다.

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  논리 앱을 저장합니다. **이 URL의 HTTP POST** 아래에서 이제 다음 예와 같은 생성된 콜백 URL을 확인할 수 있습니다.

    ![끝점에 대해 생성된 콜백 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    이 URL은 인증에 사용하는 쿼리 매개 변수에 공유 액세스 서명(SAS) 키를 포함합니다. 
    Azure Portal의 논리 앱 개요에서 HTTP 끝점 URL을 가져올 수도 있습니다. **트리거 기록** 아래에서 트리거를 선택합니다.

    ![Azure Portal에서 HTTP 끝점 URL 가져오기][2]

    또는 다음을 호출하여 URL을 가져올 수도 있습니다.

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>트리거를 위한 HTTP 메서드 변경

기본적으로 **요청** 트리거는 HTTP POST 요청을 예상하지만 다른 HTTP 메서드를 사용할 수 있습니다. 

> [!NOTE]
> 메서드 유형을 하나만 지정할 수 있습니다.

1. **요청** 트리거에서 **고급 옵션 표시**를 선택합니다.

2. **메서드** 목록을 엽니다. 이 예의 경우 **GET**을 선택하면 HTTP 끝점의 URL을 나중에 테스트할 수 있습니다.

    > [!NOTE]
    > 다른 HTTP 메서드를 선택하거나 사용자의 고유한 논리 앱에 대한 사용자 지정 메서드를 지정할 수 있습니다.

    ![HTTP 메서드 변경](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>HTTP 끝점 URL을 통해 매개 변수 허용

HTTP 끝점 URL이 매개 변수를 허용하도록 하려면 트리거의 상대 경로를 사용자 지정합니다.

1. **요청** 트리거에서 **고급 옵션 표시**를 선택합니다. 

2. **메서드** 아래에서 요청에 사용할 HTTP 메서드를 지정합니다. 이 예의 경우 아직 선택하지 않은 경우 **GET** 메서드를 선택하여 HTTP 끝점의 URL을 테스트할 수 있습니다.

      > [!NOTE]
      > 트리거에 대한 상대 경로를 지정하는 경우 트리거에 대한 HTTP 메서드도 명시적으로 지정해야 합니다.

3. **상대 경로** 아래에서 URL이 허용해야 하는 매개 변수에 대한 상대 경로를 지정합니다. 예: `customers/{customerID}`.

    ![HTTP 메서드 및 매개 변수에 대한 상대 경로 지정](./media/logic-apps-http-endpoint/relativeurl.png)

4. 매개 변수를 사용하려면 논리 앱에 **응답** 작업을 추가합니다. (트리거 아래에서 **새 단계** > **작업 추가** > **응답**을 선택) 

5. 응답의 **본문**에 트리거의 상대 경로에 지정한 매개 변수의 토큰을 포함합니다.

    예를 들어 `Hello {customerID}`로 돌아가려면 응답의 **본문**을 `Hello {customerID token}`로 업데이트합니다. 
    동적 콘텐츠 목록이 나타나고 선택할 `customerID` 토큰이 표시됩니다.

    ![응답 본문에 매개 변수 추가](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    **본문**은 다음 예와 유사해야 합니다.

    ![매개 변수 포함 응답 본문](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. 논리 앱을 저장합니다. 

    HTTP 끝점 URL은 이제 다음 예와 같은 상대 경로를 포함합니다. 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. HTTP 끝점을 테스트하려면 업데이트된 URL을 복사하여 다른 브라우저 창에 붙여넣되, `{customerID}`을 `123456`로 바꾸고 Enter 키를 누릅니다.

    브라우저에 다음 텍스트가 표시되어야 합니다. 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>논리 앱에 대한 JSON 스키마에서 생성된 토큰

**요청** 트리거에 JSON 스키마를 제공하면 논리 앱 디자이너가 이 스키마의 속성에 대한 토큰을 생성합니다. 논리 앱 워크플로를 통해 데이터를 전달하는 데 해당 토큰을 사용할 수 있습니다.

이 예의 경우 JSON 스키마에 `title` 및 `name` 속성을 추가하면 해당 토큰은 이제 이후 워크플로 단계에 사용할 수 있습니다. 

다음은 완료된 JSON 스키마입니다.

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Logic Apps에 대한 중첩 워크플로 만들기

요청을 받을 수 있는 다른 논리 앱을 추가하여 Logic Apps에서 워크플로를 중첩할 수 있습니다. 이러한 논리 앱을 포함하려면 **Azure Logic Apps - 논리 앱 워크플로 선택** 작업을 트리거에 추가합니다. 그런 다음 자격이 있는 Logic Apps 중에서 선택할 수 있습니다.

![다른 논리 앱 추가](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>HTTP 끝점을 통해 Logic Apps 호출 또는 트리거

HTTP 끝점을 만든 후 `POST` 메서드를 통해 논리 앱을 전체 URL로 트리거할 수 있습니다. Logic Apps는 직접 액세스 끝점에 대한 기본 제공 지원을 포함합니다.

> [!NOTE] 
> 언제든 논리 앱을 수동으로 실행하려면 Logic App 디자이너 또는 Logic App 코드 보기 도구모음에서 **실행**을 선택합니다.

## <a name="reference-content-from-an-incoming-request"></a>들어오는 요청의 콘텐츠 참조

콘텐츠의 형식이 `application/json`이면 들어오는 요청의 속성을 참조할 수 있습니다. 그렇지 않으면 콘텐츠는 다른 API에 전달할 수 있는 단일 이진 단위로 처리됩니다. 워크플로 내에서 이 콘텐츠를 참조하려면 해당 콘텐츠를 변환해야 합니다. 예를 들어 `application/xml` 콘텐츠를 전달하는 경우 `@xpath()`를 사용하여 XPath 추출을 수행하거나 `@json()`를 사용하여 XML을 JSON으로 변환할 수 있습니다. [콘텐츠 형식 사용](../logic-apps/logic-apps-content-type.md)에 대해 자세히 알아봅니다.

들어오는 요청에서 출력을 가져오려면 `@triggerOutputs()` 함수를 사용할 수 있습니다. 출력은 다음 예제와 같이 표시될 수 있습니다.

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

특히 `@triggerBody()` 속성에 액세스하기 위해 `body` 바로 가기를 사용할 수 있습니다. 

## <a name="respond-to-requests"></a>요청에 응답

콘텐츠를 호출자에게 반환하여 논리 앱을 시작하는 특정 요청에 응답하는 것이 좋습니다. 응답에 대한 상태 코드, 헤더 및 본문을 생성하려면 **응답** 작업을 사용할 수 있습니다. 이 작업은 워크플로의 끝뿐만 아니라 논리 앱의 어디서나 나타날 수 있습니다.

> [!NOTE] 
> 논리 앱에 **응답**이 포함되지 않은 경우 HTTP 끝점은 **202 수락됨** 상태로 *즉시* 응답합니다. 또한 워크플로를 중첩 논리 앱으로 호출하지 않은 한 원래 요청에서 응답을 가져오려면 응답에 필요한 모든 단계를 [요청 시간 제한](./logic-apps-limits-and-config.md) 이내에 완료해야 합니다. 이 시간 제한 내에 도달하는 응답 작업이 없으면 들어오는 요청은 시간 초과되어 **408 클라이언트 시간 제한** HTTP 응답을 수신합니다. 중첩 논리 앱의 경우 부모 논리 앱은 필요한 시간에 관계없이 응답이 완료될 때까지 계속 기다립니다.

### <a name="construct-the-response"></a>응답 생성

응답 본문에 둘 이상의 헤더 및 임의 형식의 콘텐츠를 포함할 수 있습니다. 예제 응답의 경우 헤더는 응답의 콘텐츠 형식이 `application/json`인 것으로 지정합니다. 그리고 본문은 **요청** 트리거에 대해 이전에 업데이트된 JSON 스키마에 따라 `title` 및 `name`를 포함합니다.

![HTTP 응답 작업][3]

응답 속성:

| 자산 | 설명 |
| --- | --- |
| statusCode |들어오는 요청에 응답하는 HTTP 상태 코드를 지정합니다. 이 코드는 2xx, 4xx 또는 5xx로 시작하는 모든 유효한 상태 코드가 될 수 있습니다. 하지만 3xx 상태 코드는 허용되지 않습니다. |
| headers |응답에 포함될 헤더의 수를 정의합니다. |
| 본문 |문자열, JSON 개체 또는 이전 단계에서 참조한 이진 콘텐츠일 수도 있는 본문 개체를 지정합니다. |

**응답** 작업에 대한 JSON 스키마는 이제 다음과 유사하게 표시됩니다.

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> 논리 앱에 대한 전체 JSON 정의를 보려면 논리 앱 디자이너에서 **코드 보기**를 선택합니다.

## <a name="q--a"></a>질문과 대답

#### <a name="q-what-about-url-security"></a>Q: URL 보안이란 무엇입니까?

A: Azure는 공유 액세스 서명(SAS)을 사용하여 논리 앱 콜백 URL을 안전하게 생성합니다. 이 서명은 쿼리 매개 변수로 전달되고 논리 앱이 시작하기 전에 유효성이 검사되어야 합니다. Azure는 논리 앱, 트리거 이름 및 수행되는 작업 별로 비밀 키의 고유한 조합을 사용하여 서명을 생성합니다. 따라서 사용자가 비밀 논리 앱 키에 액세스하지 않으면 유효한 서명을 생성할 수 없습니다.

   > [!IMPORTANT]
   > 프로덕션 및 보안 시스템의 경우 다음과 같은 이유로 논리 앱을 브라우저에서 직접 호출하는 것을 권장하지 않습니다.
   > 
   > * URL에 공유 액세스 키가 나타납니다.
   > * 논리 앱 고객 간에 공유 도메인으로 인해 보안 콘텐츠 정책을 관리할 수 없습니다.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Q: HTTP 끝점을 추가로 구성할 수 있습니까?

A: 예, HTTP 끝점은 [**API Management**](../api-management/api-management-key-concepts.md)를 통해 고급 구성을 지원합니다. 또한 이 서비스는 Logic Apps를 포함한 모든 API를 일관성 있게 관리하고 사용자 지정 도메인 이름을 설정하고 다음과 같은 더 많은 인증 방법을 사용하는 기능을 제공합니다.

* [요청 메서드 변경](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [요청의 URL 세그먼트 변경](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* [Azure Portal](https://portal.azure.com/ "Azure Portal")에서 API Management 도메인 설정
* 기본 인증을 확인하는 정책 설정

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Q: 스키마가 2014년 12월 1일 마이그레이션되었을 때 미리 보기가 어떻게 변경되었습니까?

A: 다음은 이러한 변경 내용에 대한 요약입니다.

| 2014 년 12 월 1 일 미리 보기 | 2016년 6월 1일 |
| --- | --- |
| **HTTP 수신기** API 앱 클릭 |**수동 트리거** 클릭(API 앱 필요 없음) |
| HTTP 수신기 설정 "*자동으로 응답 보내기*" |**응답** 작업 포함 또는 워크플로 정의에 없음 |
| 기본 또는 OAuth 인증 구성 |API Management를 통해 |
| HTTP 메서드 구성 |**고급 옵션 표시** 아래에서 HTTP 메서드를 선택합니다. |
| 상대 경로 구성 |**고급 옵션 표시** 아래에서 상대 경로를 추가합니다. |
| `@triggerOutputs().body.Content`을 통해 들어오는 본문을 참조합니다. |`@triggerOutputs().body`을 통해 참조합니다. |
| **HTTP 응답 보내기** 작업 |**HTTP 요청에 응답** 클릭(API 앱 필요 없음) |

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 알아보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

## <a name="next-steps"></a>다음 단계

* [작성자 논리 앱 정의](./logic-apps-author-definitions.md)
* [오류 및 예외 처리](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png