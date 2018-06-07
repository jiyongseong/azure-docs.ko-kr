---
title: 논리 앱에서 Twitter 커넥터를 사용하는 방법 알아보기 | Microsoft Docs
description: REST API 매개 변수를 사용하는 Twitter 커넥터 개요
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: eb953ee7701d407b9b75a0699f53b9b64828a0e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twitter-connector"></a>Twitter 커넥터 시작
Twitter 커넥터를 사용하여 다음을 수행할 수 있습니다.

* 트윗 게시 및 트윗 가져오기
* 타임라인, 친구 및 팔로워에 액세스
* 이 아티클에 설명된 기타 작업 및 트리거 수행

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 시작할 수 있습니다.  

## <a name="connect-to-twitter"></a>Twitter에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-twitter"></a>Twitter에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Twitter 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

이 예제에서는 **새 트윗이 게시될 때** 트리거를 사용하여 #Seattle을 검색합니다. #Seattle이 발견되면 Dropbox에 있는 파일을 트윗의 텍스트로 업데이트합니다. 엔터프라이즈 예에서는 회사 이름을 검색하고 SQL 데이터베이스를 트윗의 텍스트로 업데이트할 수 있습니다.

1. 논리 앱 디자이너의 검색 상자에 *twitter*를 입력한 후 **Twitter - 새 트윗이 게시될 때** 트리거를 선택합니다.   
   ![Twitter 트리거 이미지 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. **검색 텍스트** 컨트롤에 *#Seattle*을 입력합니다.  
   ![Twitter 트리거 이미지 2](./media/connectors-create-api-twitter/trigger-2.png) 

이제, 논리 앱은 워크플로의 다른 트리거 및 동작의 실행을 시작하는 트리거로 구성되었습니다. 

> [!NOTE]
> 논리 앱이 작동하려면 하나 이상의 트리거와 작업이 있어야 합니다. 다음 섹션의 단계를 사용하여 작업을 추가합니다.

## <a name="add-a-condition"></a>조건 추가
50명 이상의 사용자로부터 받은 트윗에만 관심이 있습니다. 따라서 팔로워 수가 논리 앱에 먼저 추가되었는지를 확인하는 조건입니다.  

1. **+ 새 단계**를 선택하여 새 트윗에 #Seattle이 있을 때 수행할 작업을 추가합니다.  
   ![Twitter 작업 이미지 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. **조건 추가** 링크를 선택합니다.  
   ![Twitter 조건 이미지 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   그러면 *같음*, *보다 작음*, *보다 큼*, *포함* 등과 같은 조건을 확인할 수 있는 **조건** 컨트롤이 열립니다.  
   ![Twitter 조건 이미지 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. **값 선택** 컨트롤을 선택합니다. 이 컨트롤에서는 이전 작업 또는 트리거 중 하나 이상의 속성을 선택할 수 있습니다. 이 속성 값의 조건은 true 또는 false로 평가됩니다.
   ![Twitter 조건 이미지 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. **...**를 선택하여 속성 목록을 확장하면 사용 가능한 모든 속성을 볼 수 있습니다.        
   ![Twitter 조건 이미지 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. **팔로워 수** 속성을 선택합니다.    
   ![Twitter 조건 이미지 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 이제 팔로워 수 속성이 값 컨트롤에 있는 것을 확인합니다.    
   ![Twitter 조건 이미지 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 연산자 목록에서 **보다 큼**을 선택합니다.    
   ![Twitter 조건 이미지 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. *보다 큼* 연산자의 피연산자로 50을 입력합니다.  
   이제 조건이 추가됩니다. 메뉴에서 **저장** 링크를 사용하여 작업을 저장합니다.    
   ![Twitter 조건 이미지 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Twitter 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)  

이제 트리거가 있으므로 트리거에서 찾은 트윗의 내용으로 새 트윗을 게시하는 작업을 추가합니다. 이 연습은 50명 이상의 팔로워가 있는 사용자의 트윗만 게시합니다.  

다음 단계에서는 50명 이상의 팔로워가 있는 사용자가 게시한 각 트윗의 일부 속성을 사용하여 트윗을 게시하는 Twitter 작업을 추가합니다.  

1. **작업 추가**를 선택합니다. 이 단계에서는 다른 작업 및 트리거를 검색할 수 있는 검색 컨트롤을 엽니다.  
   ![Twitter 조건 이미지 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 검색 상자에 *twitter*를 입력한 후 **Twitter - 트윗 게시** 작업을 선택합니다. 이 단계에서는 트윗에 대해 게시되는 모든 세부 사항을 입력할 **트윗 게시** 컨트롤을 엽니다.      
   ![Twitter 작업 이미지 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. **트윗 텍스트** 컨트롤을 선택합니다. 이제 논리 앱에서 이전 작업 및 트리거의 모든 출력이 표시됩니다. 이러한 출력 중 하나를 선택하여 새 트윗의 일부 트윗 텍스트로 사용할 수 있습니다.     
   ![Twitter 작업 이미지 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. **사용자 이름**을 선택합니다.   
5. 사용자 이름 바로 뒤에 있는 트윗 텍스트 컨트롤에 *says:*를 입력합니다.
6. *트윗 텍스트*를 선택합니다.       
   ![Twitter 작업 이미지 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. 워크플로를 활성화하려면 작업을 저장하고 #Seattle 해시 태그로 트윗을 보냅니다.


## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/twitterconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)