---
title: Azure Logic Apps의 SMTP 커넥터 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. SMTP에 연결하여 전자 메일을 보냅니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9bf7c9b7c3e775ab03b071d13d792f4b2d8fb3e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-smtp-connector"></a>SMTP 커넥터 시작
SMTP에 연결하여 전자 메일을 보냅니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 시작할 수 있습니다.

## <a name="connect-to-smtp"></a>SMTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 SMTP에 연결하려면 먼저 SMTP *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 SMTP 예제에서는 SMTP에 대한 연결을 만들기 위해 연결 이름, SMTP 서버 주소 및 사용자 로그인 정보에 대한 자격 증명이 필요합니다.  

### <a name="create-a-connection-to-smtp"></a>SMTP에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>SMTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

이 예제에서 SMTP에는 고유한 트리거가 없습니다. 따라서 **Salesforce - 개체를 만들 때** 트리거를 사용합니다. 이 트리거는 Salesforce에서 새 개체를 만들 때 활성화됩니다. 이 예제에서는 Salesforce에서 새 잠재 고객이 생성될 때마다 새 잠재 고객이 생성되었다는 알림과 함께 SMTP 커넥터를 사용하여 *메일 보내기* 작업이 발생하도록 설정합니다.

1. 논리 앱 디자이너에서 검색 상자에 *salesforce* 를 입력한 후 **Salesforce - 개체를 만들 때** 트리거를 선택합니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. **개체를 만들 때** 컨트롤이 표시됩니다.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. **개체 형식** 을 선택하고 개체 목록에서 *Lead* 를 선택합니다. 이 단계에서는 Salesforce에서 새 잠재 고객을 만들 때마다 논리 앱에 알리는 트리거를 만듭니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. 트리거를 만들었습니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>SMTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

이제 트리거가 추가되었으므로 다음 단계를 사용하여 Salesforce에서 새 잠재 고객이 생성될 때 발생하는 SMTP 작업을 추가합니다.

1. **+ 새 단계**를 선택하여 새 잠재 고객이 생성될 때 수행할 작업을 추가합니다.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. **작업 추가**를 선택합니다. 수행할 동작을 검색할 수 있는 검색 상자가 열립니다.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. *smtp*를 입력하여 SMTP와 관련된 작업을 검색합니다.  
4. 새 잠재 고객이 생성될 때 수행할 작업으로 **SMTP - 전자 메일 보내기**를 선택합니다. 작업 제어 블록이 열립니다. 아직 수행하지 않은 경우 디자이너 블록에서 smtp 연결을 설정해야 합니다.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. **SMTP - 전자 메일 보내기** 블록에 원하는 전자 메일 정보를 입력합니다.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. 워크플로를 활성화하려면 작업을 저장합니다.  

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/smtpconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.