---
title: HTTP를 통해 끝점과 통신 - Azure Logic Apps | Microsoft Docs
description: HTTP를 통해 끝점과 통신할 수 있는 Logic Apps 만들기
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 355d1e02ef8195bcee469b77976f1a5834f192d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-the-http-action"></a>HTTP 동작 시작

HTTP 작업을 사용하여 조직에 대한 워크플로를 확장하고 HTTP를 통해 끝점과 통신할 수 있습니다.

다음을 수행할 수 있습니다.

* 관리하는 웹 사이트가 중단되면 활성화되는(트리거) 논리 앱 워크플로를 만듭니다.
* HTTP를 통해 모든 끝점과 통신하여 다른 서비스로 워크플로를 확장합니다.

논리 앱에서 HTTP 동작 사용을 시작하려면 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="use-the-http-trigger"></a>HTTP 트리거 사용
트리거는 논리 앱에서 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요](connectors-overview.md).

논리 앱 디자이너에서 HTTP 트리거를 설정하는 방법의 예제 시퀀스는 다음과 같습니다.

1. 논리 앱에 HTTP 트리거를 추가합니다.
2. 폴링할 HTTP 끝점에 대한 매개 변수를 입력합니다.
3. 폴링할 빈도에 대해 되풀이 간격을 수정합니다.

   이제 각 검사 중에 반환된 모든 콘텐츠에 대해 논리 앱이 실행됩니다.

   ![HTTP 트리거](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>HTTP 트리거 작동 방식

HTTP 트리거는 되풀이 간격에 따라 HTTP 끝점에 대한 호출을 전송합니다. 기본적으로 300 미만의 모든 HTTP 응답 코드에서 논리 앱이 실행됩니다. 논리 앱을 실행할지 여부를 지정하려면 코드 보기에서 논리 앱을 편집하고 HTTP 호출 이후에 평가되는 조건을 추가합니다. 다음은 반환된 상태 코드가 `400`보다 크거나 같을 때마다 발생하는 HTTP 트리거의 예입니다.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

HTTP 트리거 매개 변수에 대한 전체 세부 정보는 [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)에서 확인할 수 있습니다.

## <a name="use-the-http-action"></a>HTTP 동작 사용

동작은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. 
[작업에 대해 자세히 알아봅니다.](connectors-overview.md)

1. **다음 단계** > **동작 추가**를 선택합니다.
3. 동작 검색 상자에 **http**를 입력하여 HTTP 동작을 나열합니다.
   
    ![HTTP 동작 선택](./media/connectors-native-http/using-action-1.png)

4. HTTP 호출에 대한 모든 필수 매개 변수를 추가합니다.
   
    ![HTTP 동작 완료](./media/connectors-native-http/using-action-2.png)

5. 디자이너 도구 모음에서 **저장**을 클릭합니다. 논리 앱이 저장되면서 동시에 게시(활성화)됩니다.

## <a name="http-trigger"></a>HTTP 트리거
여기에는 이 커넥터가 지원하는 트리거에 대한 세부 정보가 나와 있습니다. HTTP 커넥터에는 1개의 트리거가 있습니다.

| 트리거 | 설명 |
| --- | --- |
| HTTP |HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다. |

## <a name="http-action"></a>HTTP 동작
여기에는 이 커넥터가 지원하는 동작에 대한 세부 정보가 나와 있습니다. HTTP 커넥터에는 1개의 가능한 동작이 있습니다.

| 조치 | 설명 |
| --- | --- |
| HTTP |HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다. |

## <a name="http-details"></a>HTTP 세부 정보
다음 표에서는 동작의 필수 및 선택적 입력 필드와 함께 동작 사용과 연관된 해당 출력 세부 정보를 설명합니다.

#### <a name="http-request"></a>HTTP 요청
HTTP 아웃바운드 요청을 하는 동작에 대한 입력 필드는 다음과 같습니다.
A*는 필수 필드 임을 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| Method* |메서드 |사용할 HTTP 동사 |
| URI* |uri |HTTP 요청에 대한 URI |
| 헤더 |headers |포함할 HTTP 헤더의 JSON 개체 |
| 본문 |본문 |HTTP 요청 본문 |
| 인증 |인증 |[인증](#authentication) 섹션의 세부 정보 |

<br>

#### <a name="output-details"></a>출력 세부 정보
HTTP 요청에 대한 출력 세부 정보는 다음과 같습니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |응답 헤더 |
| 본문 |object |응답 개체 |
| 상태 코드 |int |HTTP 상태 코드 |

## <a name="authentication"></a>인증
Logic Apps 기능을 사용하면 HTTP 끝점에 대해 다른 유형의 인증을 사용할 수 있습니다. 이 인증은 HTTP, **HTTP**, **[HTTP + Swagger](connectors-native-http-swagger.md)** 및 **[HTTP 웹후크](connectors-native-webhook.md)** 커넥터와 함께 사용할 수 있습니다. 다음 인증 유형은 구성할 수 있습니다.

* [기본 인증](#basic-authentication)
* [클라이언트 인증서 인증](#client-certificate-authentication)
* [Azure AD(Azure Active Directory) OAuth 인증](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>기본 인증

기본 인증 개체는 기본 인증에 필요합니다.
A*는 필수 필드 임을 의미합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| 형식* |형식 |인증 유형(기본 인증의 경우 `Basic` 이어야 함) |
| 사용자 이름* |사용자 이름 |인증할 사용자 이름 |
| 암호* |암호 |인증하기 위한 암호 |

> [!TIP]
> 정의에서 검색할 수 없는 암호를 사용하려는 경우 `securestring` 매개 변수 및 `@parameters()` 
> [워크플로 정의 함수](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow)를 사용합니다.

예: 

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

다음 인증 개체는 클라이언트 인증서 인증에 필요합니다. A*는 필수 필드 임을 의미합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| 형식* |형식 |인증 유형(SSL 클라이언트 인증서의 경우 `ClientCertificate` 여야 함) |
| PFX* |pfx |PFX(개인 정보 교환) 파일의 Base64로 인코딩된 콘텐츠 |
| 암호* |암호 |PFX 파일에 액세스하기 위한 암호 |

> [!TIP]
> 논리 앱을 저장한 후 정의에서 읽을 수 없는 매개 변수를 사용하려면 `securestring` 매개 변수 및 `@parameters()` 
> [워크플로 정의 함수](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow)를 사용할 수 있습니다.

예: 

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth 인증
다음 인증 개체는 Azure AD OAuth 인증에 필요합니다. A*는 필수 필드 임을 의미합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| 형식* |형식 |인증 유형(Azure AD OAuth의 경우 `ActiveDirectoryOAuth` 여야 함) |
| 테넌트* |tenant |Azure AD 테넌트의 테넌트 식별자 |
| 대상* |audience |사용 권한을 요청하는 리소스. 예: `https://management.core.windows.net/` |
| 클라이언트 ID* |clientId |Azure AD 응용 프로그램의 클라이언트 ID |
| 암호* |secret |토큰을 요청하는 클라이언트의 암호 |

> [!TIP]
> `securestring` 매개 변수 및 `@parameters()` [워크플로 정의 함수](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-parameters-and-inputs-within-a-workflow)를 사용하면 저장한 후 정의에서 읽을 수 없는 매개 변수를 사용할 수 있습니다.
> 
> 

예: 

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>다음 단계
이제 플랫폼을 사용해 보고 [논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

