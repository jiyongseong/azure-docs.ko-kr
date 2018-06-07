---
title: Azure Logic Apps에 Yammer 커넥터 추가 | Microsoft Docs
description: REST API 매개 변수를 사용하는 Yammer 커넥터 개요
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7f1758e9b95f534b23188f427ae0edaddbb29a48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Yammer 커넥터 시작
Yammer에 연결하여 엔터프라이즈 네트워크의 대화에 액세스합니다. Yammer를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Yammer에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 그룹의 새 메시지 또는 팔로잉의 피드가 있을 때 트리거를 사용합니다.
* 메시지 게시, 모든 메시지 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 새 메시지가 표시되면 Office 365를 사용하여 메일을 보낼 수 있습니다.

논리 앱을 만들어 시작합니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-yammer"></a>Yammer에 대한 연결 만들기
Yammer 커넥터를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다. 

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Yammer 자격 증명 제공 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/yammer/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.