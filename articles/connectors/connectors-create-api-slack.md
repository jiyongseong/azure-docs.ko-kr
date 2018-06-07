---
title: Logic Apps에서 Slack 커넥터 사용 | Microsoft Docs
description: Logic Apps에서 Slack에 연결
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 73c512c70f1c135bd791d93cecc42bd6f4c06b3d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-slack-connector"></a>Slack 커넥터 시작
Slack은 팀의 모든 통신을 한데 모아, 어디서나 즉시 검색 및 사용할 수 있는 팀 통신 도구입니다. 

논리 앱을 만들어 시작합니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-slack"></a>Slack에 대한 연결 만들기
Slack 커넥터를 사용하려면 먼저 **연결** 을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다. 

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |Slack 자격 증명 제공 |

다음 단계를 사용하여 Slack에 로그인하고 논리 앱에서 Slack **연결**의 구성을 완료합니다.

1. **되풀이**
2. **빈도**를 선택하고 **간격**을 입력합니다.
3. **작업 추가**를 선택합니다.  
   ![Slack 구성][1]  
4. 검색 상자에 Slack을 입력하고 이름에 Slack이 있는 모든 항목이 반환될 때까지 검색을 기다립니다.
5. **Slack-메시지 게시**
6. **Slack에 로그인**을 선택합니다.  
   ![Slack 구성][2]
7. Slack 자격 증명을 제공하여 로그인하고 응용 프로그램에 권한을 부여합니다.    
   ![Slack 구성][3]  
8. 조직의 로그인 페이지로 리디렉션됩니다. 논리 앱과 상호 작용하도록 Slack에 **권한을 부여**합니다.      
   ![Slack 구성][5] 
9. 권한 부여가 완료된 후에 **Slack - 모든 메시지 가져오기** 섹션을 구성하여 완료할 논리 앱으로 리디렉션됩니다. 필요한 다른 트리거 및 작업을 추가합니다.  
   ![Slack 구성][6]
10. 위쪽 메뉴에서 **저장**을 선택하여 작업을 저장합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/slack/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
