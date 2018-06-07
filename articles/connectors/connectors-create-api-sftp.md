---
title: 논리 앱에 SFTP 커넥터 사용 방법 알아보기 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. SFTP API에 연결하여 파일을 보내고 받습니다. 파일 만들기, 업데이트, 가져오기 또는 삭제와 같은 다양한 작업을 수행할 수 있습니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: estfan; ladocs
ms.openlocfilehash: ee4dd59bd7b4c09cccadfff29868029559955c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sftp-connector"></a>SFTP 커넥터 시작
SFTP 커넥터를 사용하여 SFTP 계정에 액세스하여 파일을 보내고 받습니다. 파일 만들기, 업데이트, 가져오기 또는 삭제와 같은 다양한 작업을 수행할 수 있습니다.  

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 시작할 수 있습니다.

## <a name="connect-to-sftp"></a>SFTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-sftp"></a>SFTP에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>SFTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)  

이 예제에서는 파일이 SFTP 서버에 추가되거나 수정될 때 **SFTP - 파일을 추가하거나 수정할 때** 트리거를 사용하여 논리 앱 워크플로를 시작합니다. 새 파일 또는 수정된 파일의 콘텐츠를 확인하는 조건을 추가하고 해당 콘텐츠를 사용하기 전에 추출해야 하는 것으로 나타나는 경우 파일을 추출하도록 결정합니다. 마지막으로, 파일의 콘텐츠를 추출하고 추출한 콘텐츠를 SFTP 서버의 폴더에 배치하는 작업을 추가합니다. 

엔터프라이즈 예제에서는 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 SFTP 폴더를 모니터링할 수 있습니다.  그런 다음 **파일 콘텐츠 가져오기**와 같은 SFTP 커넥터 작업을 사용하여 추가 처리할 주문 및 주문 데이터베이스에 있는 저장소의 콘텐츠를 가져올 수 있습니다.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>조건 추가
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>SFTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/sftpconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.