---
title: Azure Logic Apps의 Dropbox 커넥터 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. Dropbox에 연결하여 파일을 관리합니다. Dropbox에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7ac72cf5b18fa19bc0294abc67bf0a7089774a89
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-dropbox-connector"></a>Dropbox 커넥터 시작
Dropbox에 연결하여 파일을 관리합니다. Dropbox에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 시작할 수 있습니다.

## <a name="connect-to-dropbox"></a>Dropbox에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 Dropbox에 연결하려면 먼저 Dropbox *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 제공해야 합니다. 따라서 Dropbox 예제에서는 Dropbox에 대한 연결을 만들기 위해 Dropbox 계정에 대한 자격 증명이 필요합니다. [연결에 대한 자세한 정보]()

### <a name="create-a-connection-to-dropbox"></a>Dropbox에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Dropbox 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

이 예제에서는 **파일을 만들 때** 트리거를 사용합니다. 이 트리거가 발생하면 **경로를 사용하여 파일 콘텐츠 가져오기** Dropbox 작업을 호출합니다. 

1. Logic Apps 디자이너의 검색 상자에 *dropbox*를 입력한 후 **Dropbox - 파일을 만들 때** 트리거를 선택합니다.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. 파일 생성을 추적할 폴더를 선택합니다. ...(빨간색 상자에 표시)를 선택하고 트리거의 입력을 선택할 폴더를 찾습니다.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Dropbox 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

이제 트리거가 추가되었고 다음 단계에 따라 새 파일의 콘텐츠를 가져올 작업을 추가합니다.

1. **+ 새 단계**를 선택하여 새 파일을 만들 때 수행할 작업을 추가합니다.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. **작업 추가**를 선택합니다. 수행할 동작을 검색할 수 있는 검색 상자가 열립니다.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. *dropbox*를 입력하여 Dropbox와 관련된 작업을 검색합니다.  
4. 선택한 Dropbox 폴더에서 새 파일이 생성될 때 수행할 작업으로 **Dropbox - 경로를 사용하여 파일 콘텐츠 가져오기**를 선택합니다. 작업 제어 블록이 열립니다. 논리 앱에 Dropbox 계정에 액세스하기 위한 권한을 아직 부여하지 않았으면 이러한 권한을 부여하라는 메시지가 표시됩니다.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. ...(**파일 경로** 컨트롤의 오른쪽에 있음)를 선택하고 사용할 파일 경로를 찾습니다. 또는 **파일 경로** 토큰을 사용하여 빠르게 논리 앱을 만들 수 있습니다.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. 작업을 저장하고 Dropbox에 새 파일을 만들어 워크플로를 활성화합니다.  

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/dropbox/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.