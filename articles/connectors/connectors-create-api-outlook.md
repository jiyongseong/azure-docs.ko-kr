---
title: Azure Logic Apps의 Outlook.com 커넥터 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. Outlook.com 커넥터를 사용하면 메일, 일정 및 연락처를 관리할 수 있습니다. 메일 보내기, 모임 예약, 연락처 추가 등 다양한 작업을 수행할 수 있습니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9fc0cfd39197bcc834aca600238853a712ebf297
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com 커넥터 시작
Outlook.com 커넥터를 사용하면 메일, 일정 및 연락처를 관리할 수 있습니다. 메일 보내기, 모임 예약, 연락처 추가 등 다양한 작업을 수행할 수 있습니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-outlookcom"></a>Outlook.com에 대한 연결 만들기
Outlook.com으로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Outlook.com 자격 증명 제공 |

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/outlook/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.