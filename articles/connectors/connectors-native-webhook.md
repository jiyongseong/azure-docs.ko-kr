---
title: "Azure Logic Apps용 웹후크 커넥터 | Microsoft Docs"
description: "웹후크 동작 및 트리거를 사용하여 Logic Apps에서 필터 배열과 같은 동작을 수행하는 방법"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: ec407d72f90cf3616214d27c378dee0ff42f4c6f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-webhook-connector"></a>웹후크 커넥터 시작

웹후크 동작 및 트리거를 사용하여 다음 작업을 수행하기 위한 흐름을 시작, 일시 중지 및 계속할 수 있습니다.

* 항목이 수신되자마자 [Azure 이벤트 허브](https://github.com/logicappsio/EventHubAPI)에서 트리거
* 워크플로를 계속하기 전에 승인을 대기합니다.

[웹후크를 지원하는 사용자 지정 API를 만드는 방법](../logic-apps/logic-apps-create-api-app.md)에 대해 자세히 알아봅니다.

## <a name="use-the-webhook-trigger"></a>웹후크 트리거 사용

[*트리거*](connectors-overview.md)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 웹후크 트리거는 이벤트 기반이며 새 항목에 대한 폴링에 의존하지 않습니다. [요청 트리거](connectors-native-reqres.md)와 마찬가지로, 논리 앱은 이벤트가 발생하는 인스턴트를 발생시킵니다. 웹후크 트리거는 *콜백 URL*을 서비스에 등록하고 해당 URL을 사용해서 필요에 따라 논리 앱을 발생시킵니다.

논리 앱 디자이너에서 HTTP 트리거를 설정하는 방법을 보여 주는 예제는 다음과 같습니다. 이러한 단계에서는 [Logic Apps에서 사용되는 웹후크 구독 및 구독 취소 패턴](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다. 구독 호출은 논리 앱이 새 웹후크와 함께 저장되거나 사용 안 함 상태에서 사용 상태로 전환될 때마다 수행됩니다. 구독 취소 호출은 논리 앱 웹후크 트리거가 제거 또는 저장되거나 사용 상태에서 사용 안 함 상태로 전환될 때 수행됩니다.

**웹후크 트리거를 사용하려면**

1. **HTTP 웹후크** 트리거를 논리 앱의 첫 번째 단계로 추가합니다.
2. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   이 단계는 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![HTTP 트리거](./media/connectors-native-webhook/using-trigger.png)

3. 하나 이상의 동작을 추가합니다.
4. **저장**을 클릭하여 논리 앱을 게시합니다. 이 단계를 수행하면 이 Logic App을 트리거하는 데 필요한 콜백 URL을 사용하여 구독 끝점이 호출됩니다.
5. 서비스에서 콜백 URL에 댛 `HTTP POST` 를 수행할 때마다 논리 앱이 실행됩니다(논리 앱에는 요청에 전달된 모든 데이터가 포함됨).

## <a name="use-the-webhook-action"></a>웹후크 동작 사용

[*동작*](connectors-overview.md)은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. 웹후크 동작은 서비스에 *콜백 URL* 을 등록하고, 다시 시작하기 전에 URL이 호출될 때까지 대기합니다. ["승인 전자 메일 보내기"](connectors-create-api-office365-outlook.md) 는 이 패턴을 따르는 커넥터의 예입니다. 이 패턴을 웹후크 동작을 통해 서비스로 확장할 수 있습니다. 

논리 앱 디자이너에서 웹후크 동작을 설정하는 방법을 보여 주는 예제는 다음과 같습니다. 이러한 단계에서는 [Logic Apps에서 사용되는 웹후크 구독 및 구독 취소 패턴](../logic-apps/logic-apps-create-api-app.md#webhook-actions)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다. 논리 앱이 웹후크 동작을 실행할 때 구독 호출이 수행됩니다. 응답을 기다리는 동안 또는 논리 앱 실행이 시간 초과되기 전에 실행이 취소될 때 구독 취소 호출이 수행됩니다.

**웹후크 동작을 추가하려면**

1. **다음 단계** > **동작 추가**를 선택합니다.

2. 검색 상자에 "웹후크"를 입력하여 **HTTP 웹후크** 동작을 찾습니다.

    ![쿼리 동작 선택](./media/connectors-native-webhook/using-action-1.png)

3. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   이 단계는 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![쿼리 동작 완료](./media/connectors-native-webhook/using-action-2.png)
   
   런타임 시 논리 앱은 해당 단계에 도달한 후 구독 끝점을 호출합니다.

4. **저장**을 클릭하여 논리 앱을 게시합니다.

## <a name="technical-details"></a>기술 세부 정보

웹후크가 지원하는 트리거 및 동작에 대한 자세한 내용은 다음과 같습니다.

## <a name="webhook-triggers"></a>웹후크 트리거

| 조치 | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 논리 앱을 실행합니다. |

### <a name="trigger-details"></a>트리거 세부 정보

#### <a name="http-webhook"></a>HTTP 웹후크

필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 논리 앱을 실행합니다.
*는 필수 필드를 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| 구독 메서드* |메서드 |구독 요청에 사용할 HTTP 메서드 |
| 구독 URI* |uri |구독 요청에 사용할 HTTP URI |
| 구독 취소 메서드* |메서드 |구독 취소 요청에 사용할 HTTP 메서드 |
| 구독 취소 URI* |uri |구독 취소 요청에 사용할 HTTP URI |
| 구독 본문 |body |구독의 HTTP 요청 본문 |
| 구독 헤더 |headers |구독의 HTTP 요청 헤더 |
| 구독 인증 |인증 |구독에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |
| 구독 취소 본문 |본문 |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| 본문 |object |웹후크 요청 개체 |
| 상태 코드 |int |웹후크 요청 상태 코드 |

## <a name="webhook-actions"></a>웹후크 작업

| 조치 | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 워크플로 단계를 다시 시작합니다. |

### <a name="action-details"></a>작업 세부 정보

#### <a name="http-webhook"></a>HTTP 웹후크

필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 워크플로 단계를 다시 시작합니다.
*는 필수 필드를 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| 구독 메서드* |메서드 |구독 요청에 사용할 HTTP 메서드 |
| 구독 URI* |uri |구독 요청에 사용할 HTTP URI |
| 구독 취소 메서드* |메서드 |구독 취소 요청에 사용할 HTTP 메서드 |
| 구독 취소 URI* |uri |구독 취소 요청에 사용할 HTTP URI |
| 구독 본문 |body |구독의 HTTP 요청 본문 |
| 구독 헤더 |headers |구독의 HTTP 요청 헤더 |
| 구독 인증 |인증 |구독에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |
| 구독 취소 본문 |본문 |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| 본문 |object |웹후크 요청 개체 |
| 상태 코드 |int |웹후크 요청 상태 코드 |

## <a name="next-steps"></a>다음 단계

* [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [다른 커넥터 찾기](apis-list.md)