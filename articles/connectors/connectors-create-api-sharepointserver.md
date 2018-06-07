---
title: Logic Apps에서 SharePoint Server 커넥터 사용| Microsoft Docs
description: Logic Apps에서 SharePoint Server 커넥터 사용 시작
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: d342b3c4f84c5dab212b9327d6a72759934d0ae5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>SharePoint 커넥터 시작
SharePoint 커넥터는 SharePoint에서 목록으로 작업하기 위한 방법을 제공합니다.

논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-sharepoint"></a>SharePoint에 대한 연결 만들기
SharePoint 커넥터를 사용하려면 먼저 **연결**을 만든 다음, 이러한 속성에 대한 세부 정보를 제공합니다. 

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |SharePoint 자격 증명 제공 |

**SharePoint**에 연결하려면 ID(사용자 이름 및 암호, 스마트 카드 자격 증명 등)를 입력해야 합니다. 인증되면 논리 앱에서 SharePoint 커넥터를 계속 사용할 수 있습니다. 

논리 앱 디자이너에서는 다음 단계에 따라 로그인하고, 논리 앱에서 사용할 **연결**을 만듭니다.

1. 검색 상자에 SharePoint를 입력하고 이름에 SharePoint가 있는 모든 항목이 반환될 때까지 검색을 기다립니다.   
   ![SharePoint 구성][1]  
2. **SharePoint - 파일을 만들 때** 선택   
3. **SharePoint에 로그인** 선택   
   ![SharePoint 구성][2]    
4. SharePoint 자격 증명을 제공하여 로그인하고 SharePoint에 권한을 부여합니다.   
   ![SharePoint 구성][3]     
5. 인증이 완료된 후에 SharePoint의 **파일을 만들 때** 대화 상자를 구성하여 완료할 논리 앱으로 리디렉션됩니다.          
   ![SharePoint 구성][4]  
6. 그런 다음 논리 앱을 완료하는 데 필요한 다른 트리거 및 작업을 추가할 수 있습니다.   
7. 위쪽 메뉴에서 **저장**을 선택하여 작업을 저장합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/sharepoint/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
