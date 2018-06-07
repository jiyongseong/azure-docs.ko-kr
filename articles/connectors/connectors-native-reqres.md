---
title: "요청 및 응답 작업 사용 | Microsoft Docs"
description: "Azure 논리 앱의 요청, 응답 트리거 및 작업에 대한 개요"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-request-and-response-components"></a>요청 및 응답 구성 요소 시작
논리 앱의 요청 및 응답 구성 요소를 사용하여 이벤트에 실시간으로 응답할 수 있습니다.

예를 들어 다음을 수행할 수 있습니다.

* 논리 앱을 통해 온-프레미스 데이터베이스의 데이터를 사용하여 HTTP 요청에 응답합니다.
* 외부 웹후크 이벤트에서 논리 앱을 트리거합니다.
* 다른 논리 앱 내에서 요청 및 응답 작업으로 논리 앱을 호출합니다.

논리 앱에서 요처 및 응답 작업 사용을 시작하려면 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="use-the-http-request-trigger"></a>HTTP 요청 트리거 사용
트리거는 논리 앱에서 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요](connectors-overview.md).

논리 앱 디자이너에서 HTTP 요청을 설정하는 방법의 예제 시퀀스는 다음과 같습니다.

1. 논리 앱에서 **요청 - HTTP 요청을 받은 경우** 트리거를 추가합니다. 요청 본문에 대해 JSON 스키마를 선택적으로 제공할 수 있습니다( [JSONSchema.net](http://jsonschema.net)과 같은 도구 사용). 이렇게 하면 디자이너가 HTTP 요청에서 속성에 대한 토큰을 생성할 수 있습니다.
2. 논리 앱을 저장할 수 있도록 다른 작업을 추가합니다.
3. 논리 앱을 저장한 후 요청 카드에서 HTTP 요청 URL을 가져올 수 있습니다.
4. URL에 대한 HTTP POST( [Postman](https://www.getpostman.com/)과 같은 도구 사용)는 논리 앱을 트리거합니다.

> [!NOTE]
> 응답 작업을 정의하지 않으면 `202 ACCEPTED` 응답이 호출자에게 즉시 반환됩니다. 응답 작업을 사용하여 응답을 사용자 지정할 수 있습니다.
> 
> 

![응답 트리거](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>HTTP 응답 작업 사용
HTTP 응답 작업은 HTTP 요청에 의해 트리거되는 워크플로에서 사용할 때만 유효합니다. 응답 작업을 정의하지 않으면 `202 ACCEPTED` 응답이 호출자에게 즉시 반환됩니다.  응답 작업은 워크플로 내의 어느 단계에도 추가할 수 있습니다. 논리 앱은 들어오는 요청이 있을 때 응답을 받기 위해 1분 동안만 열어둡니다.  1분 후에 워크플로에서 전송된 응답이 없으면(정의에는 응답 작업이 있음) `504 GATEWAY TIMEOUT` 이 호출자에게 반환됩니다.

HTTP 응답 작업을 추가하는 방법은 다음과 같습니다.

1. **새 단계** 단추를 선택합니다.
2. **작업 추가**를 선택합니다.
3. 작업 검색 상자에 **response** 를 입력하여 응답 작업을 나열합니다.
   
    ![응답 작업 선택](./media/connectors-native-reqres/using-action-1.png)
4. HTTP 응답 메시지에 필요한 모든 매개 변수를 추가합니다.
   
    ![응답 작업 완료](./media/connectors-native-reqres/using-action-2.png)
5. 도구 모음의 왼쪽 위 모서리를 클릭하여 저장하면 논리 앱이 저장하고 게시합니다(활성화).

## <a name="request-trigger"></a>요청 트리거
여기에는 이 커넥터가 지원하는 트리거에 대한 세부 정보가 나와 있습니다. 단일 요청 트리거가 있습니다.

| 트리거 | 설명 |
| --- | --- |
| 요청 |HTTP 요청을 받을 때 발생합니다. |

## <a name="response-action"></a>응답 작업
여기에는 이 커넥터가 지원하는 작업에 대한 세부 정보가 나와 있습니다. 요청 트리거와 함께 나올 때만 사용할 수 있는 단일 응답 작업이 있습니다.

| 조치 | 설명 |
| --- | --- |
| response |상호 관련된 HTTP 요청에 대한 응답을 반환합니다. |

### <a name="trigger-and-action-details"></a>트리거 및 작업 세부 정보
다음 표는 트리거 및 작업에 대한 입력 필드와 해당 출력 세부 정보를 설명합니다.

#### <a name="request-trigger"></a>요청 트리거
다음은 들어오는 HTTP 요청의 트리거에 대한 입력 필드입니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| JSON 스키마 |schema |HTTP 요청 본문의 JSON 스키마 |

<br>

**출력 세부 정보**

다음은 요청에 대한 출력 세부 정보를 출력합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |헤더 요청 |
| 본문 |object |요청 개체 |

#### <a name="response-action"></a>응답 작업
다음은 HTTP 응답 작업에 대한 입력 필드입니다. A*는 필수 필드 임을 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| 상태 코드* |statusCode |HTTP 상태 코드 |
| 헤더 |headers |포함할 응답 헤더의 JSON 개체 |
| 본문 |본문 |응답 본문 |

## <a name="next-steps"></a>다음 단계
이제 플랫폼을 사용해 보고 [논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

