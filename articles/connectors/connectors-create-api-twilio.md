---
title: Azure Logic Apps에 Twilio 커넥터 추가 | Microsoft Docs
description: REST API 매개 변수를 사용하는 Twilio 커넥터 개요
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 2bb7961c850fc8a35f3e114d9497941b188cf51b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twilio-connector"></a>Twilio 커넥터 시작
Twilio에 연결하여 전역 SMS, MMS 및 IP 메시지를 보내고 받습니다. Twilio를 사용하여 다음을 수행할 수 있습니다.

* Twilio에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 메시지 가져오기, 메시지 나열 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 새 Twilio 메시지를 받은 경우 이 메시지를 가져와 Service Bus 워크플로에서 사용할 수 있습니다. 

논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-twilio"></a>Twilio에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 다음 Twilio 값을 입력합니다.

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 계정 ID |예 |Twilio 계정 ID 입력 |
| 액세스 토큰 |예 |Twilio 액세스 토큰 입력 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Twilio 액세스 토큰이 없으면 [사용자 ID 및 액세스 토큰](https://www.twilio.com/docs/api/chat/guides/identity)을 참조하세요.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/twilio/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.